# GCP上的机器学习：从Notebook到数据流水线

> 原文：[https://towardsdatascience.com/machine-learning-on-gcp-from-dev-to-prod-with-vertex-ai-c9e42c4b366f?source=collection_archive---------4-----------------------#2024-05-11](https://towardsdatascience.com/machine-learning-on-gcp-from-dev-to-prod-with-vertex-ai-c9e42c4b366f?source=collection_archive---------4-----------------------#2024-05-11)

## Notebook不足以支撑大规模的机器学习

[](https://medium.com/@benjamin_47408?source=post_page---byline--c9e42c4b366f--------------------------------)[![Benjamin Etienne](../Images/cad8bc2d4b900575e76b7cf9debc9eea.png)](https://medium.com/@benjamin_47408?source=post_page---byline--c9e42c4b366f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c9e42c4b366f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c9e42c4b366f--------------------------------) [Benjamin Etienne](https://medium.com/@benjamin_47408?source=post_page---byline--c9e42c4b366f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c9e42c4b366f--------------------------------) ·15分钟阅读·2024年5月11日

--

![](../Images/a0f9612d168ba15a6cbadea7628ba7db.png)

图片来源：[Sylvain Mauroux](https://unsplash.com/@alpifree?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*除非另有说明，所有图片均由作者提供*

# 推广人工智能

每当谈到 AI 和机器学习时，总有一种误解（甚至可以说是幻想）不断在公司间蔓延。人们常常错误地判断了将机器学习项目投入生产的复杂性和所需的技能，或者是因为他们不了解这项工作，或者更糟的是，他们以为自己了解，却根本不明白。

当他们发现 AI 时，第一次的反应可能是：“AI 其实挺简单的，我只需要一个 Jupyter Notebook，从这里那里复制粘贴代码——或者问 Copilot——然后就好了。最终也不需要聘请数据科学家了……”然而故事总是以失败告终，带来的是苦涩、失望，以及 AI 是骗局的感觉：难以投入生产环境、数据漂移、漏洞、不期望的行为。

所以，让我们一劳永逸地写下来：AI/机器学习/任何数据相关工作，都是一项真正的工作，而不是爱好。它需要技能、工艺和工具。**如果你认为仅凭 Notebook 就能在生产环境中做机器学习，那你错了。**

本文旨在通过一个简单的例子，展示从 Notebook 到实际生产环境流水线所需的所有努力、技能和工具。因为机器学习的生产环境，主要是要能够定期自动化运行你的代码，并进行自动化和监控。

对于那些寻找从“笔记本到顶点管道”端到端教程的人来说，你可能会觉得这个有帮助。

# 一个简单的用例

假设你是一位数据科学家，正在一家电子商务公司工作。你的公司在网上销售服装，营销团队请求你的帮助：他们正在为特定产品准备一项特别优惠，并希望通过定制电子邮件内容来高效地将其推送给客户，以最大化转化率。因此，你的工作很简单：每个客户应被分配一个分数，表示他/她购买该特别优惠产品的概率。

特别优惠将专门针对这些品牌，这意味着营销团队希望知道哪些客户会从以下品牌购买下一个产品：

*Allegra K, Calvin Klein, Carhartt, Hanes, Volcom, Nautica, Quiksilver, Diesel, Dockers, Hurley*

对于本文，我们将使用 Google 提供的公开数据集 `*thelook_ecommerce*` [数据集](https://console.cloud.google.com/marketplace/product/bigquery-public-data/thelook-ecommerce)。它包含了虚构的数据，包括交易数据、客户数据、产品数据，这些数据是我们在在线时尚零售商工作时可以使用的。

要跟随此笔记本，你需要访问 Google Cloud Platform，但其逻辑可以移植到其他云服务提供商或第三方平台，如 Neptune、MLFlow 等。

作为一名受人尊敬的数据科学家，你开始创建一个笔记本，帮助我们探索数据。

我们首先导入将在本文中使用的库：

```py
import catboost as cb
import pandas as pd
import sklearn as sk
import numpy as np
import datetime as dt

from dataclasses import dataclass
from sklearn.model_selection import train_test_split
from google.cloud import bigquery

%load_ext watermark
%watermark --packages catboost,pandas,sklearn,numpy,google.cloud.bigquery 
```

```py
catboost             : 1.0.4
pandas               : 1.4.2
numpy                : 1.22.4
google.cloud.bigquery: 3.2.0
```

# 生产前

## 获取和准备数据

然后，我们将使用 Python 客户端从 BigQuery 加载数据。请确保使用你自己的项目 ID：

```py
query = """
    SELECT 
      transactions.user_id,
      products.brand,
      products.category,
      products.department,
      products.retail_price,
      users.gender,
      users.age,
      users.created_at,
      users.country,
      users.city,
      transactions.created_at
    FROM `bigquery-public-data.thelook_ecommerce.order_items` as transactions
    LEFT JOIN `bigquery-public-data.thelook_ecommerce.users` as users
      ON transactions.user_id = users.id
    LEFT JOIN `bigquery-public-data.thelook_ecommerce.products` as products
      ON transactions.product_id = products.id
    WHERE status <> 'Cancelled'
"""

client = bigquery.Client()
df = client.query(query).to_dataframe() 
```

查看数据框时，你应该能看到类似的内容：

![](../Images/880c9b38a51bc63c48840f0c89577208.png)

这些代表了客户所做的交易/购买，包含了客户和产品信息。

鉴于我们的目标是预测客户在下次购买时将选择哪个品牌，我们将按照以下步骤进行：

1.  按照时间顺序为每个客户分组购买记录

1.  如果一个客户有 N 次购买，我们将第 N 次购买视为目标，第 N-1 次购买视为特征。

1.  因此，我们排除了只有一次购买的客户

让我们将其转化为代码：

```py
# Compute recurrent customers
recurrent_customers = df.groupby('user_id')['created_at'].count().to_frame("n_purchases")

# Merge with dataset and filter those with more than 1 purchase
df = df.merge(recurrent_customers, left_on='user_id', right_index=True, how='inner')
df = df.query('n_purchases > 1')

# Fill missing values
df.fillna('NA', inplace=True)

target_brands = [
    'Allegra K', 
    'Calvin Klein', 
    'Carhartt', 
    'Hanes', 
    'Volcom', 
    'Nautica', 
    'Quiksilver', 
    'Diesel',
    'Dockers', 
    'Hurley'
]

aggregation_columns = ['brand', 'department', 'category']

# Group purchases by user chronologically
df_agg = (df.sort_values('created_at')
          .groupby(['user_id', 'gender', 'country', 'city', 'age'], as_index=False)[['brand', 'department', 'category']]
          .agg({k: ";".join for k in ['brand', 'department', 'category']})
         )

# Create the target
df_agg['last_purchase_brand'] = df_agg['brand'].apply(lambda x: x.split(";")[-1])
df_agg['target'] = df_agg['last_purchase_brand'].isin(target_brands)*1

df_agg['age'] = df_agg['age'].astype(float)

# Remove last item of sequence features to avoid target leakage :
for col in aggregation_columns:
    df_agg[col] = df_agg[col].apply(lambda x: ";".join(x.split(";")[:-1]))
```

请注意我们如何删除了序列特征中的最后一个项：这非常重要，否则我们会遇到所谓的“数据泄漏”问题：目标是特征的一部分，模型在学习时已经获得了答案。

我们现在得到这个新的 `df_agg` 数据框：

![](../Images/0b0974517e0f107036fa37d8339f6f23.png)![](../Images/5cb46a927920c0bb0df6c3465aa5ec81.png)

与原始数据框比较，我们看到用户 ID 为 2 的用户确实购买了 IZOD、Parke & Ronen，最后还购买了 Orvis，而 Orvis 不在目标品牌之内。

## 将数据划分为训练集、验证集和测试集

作为一名经验丰富的数据科学家，你现在应该将数据分割成不同的子集，因为你显然知道，所有三个子集对于进行严格的机器学习都是必不可少的。*（交叉验证今天不讨论，大家简单了解一下。）*

在分割数据时，一个关键的点是使用 scikit-learn `train_test_split()` 方法中的不太为人知的 `stratify` 参数。这样做的原因是类不平衡：如果目标分布（在我们的例子中是 0 和 1 的百分比）在训练集和测试集之间有所不同，我们可能在部署模型时遇到差劲的结果，感到沮丧。*机器学习初学者：尽量使训练数据和测试数据之间的数据分布尽可能相似。*

```py
# Remove unecessary features

df_agg.drop('last_purchase_category', axis=1, inplace=True)
df_agg.drop('last_purchase_brand', axis=1, inplace=True)
df_agg.drop('user_id', axis=1, inplace=True)

# Split the data into train and eval
df_train, df_val = train_test_split(df_agg, stratify=df_agg['target'], test_size=0.2)
print(f"{len(df_train)} samples in train")

df_train, df_val = train_test_split(df_agg, stratify=df_agg['target'], test_size=0.2)
print(f"{len(df_train)} samples in train") 
# 30950 samples in train

df_val, df_test = train_test_split(df_val, stratify=df_val['target'], test_size=0.5)
print(f"{len(df_val)} samples in val")
print(f"{len(df_test)} samples in test")
# 3869 samples in train
# 3869 samples in test
```

现在这一部分已经完成，我们将优雅地将数据集分割为特征和目标：

```py
X_train, y_train = df_train.iloc[:, :-1], df_train['target']
X_val, y_val = df_val.iloc[:, :-1], df_val['target']
X_test, y_test = df_test.iloc[:, :-1], df_test['target']
```

特征中有不同的类型。我们通常将它们分为：

+   数值特征：它们是连续的，反映了一个可衡量或有序的量。

+   类别特征：它们通常是离散的，且常常用字符串表示（例如：国家、颜色等）。

+   文本特征：它们通常是由单词组成的序列。

当然，还可以有更多类型的数据，比如图像、视频、音频等。

## 模型：介绍CatBoost

对于我们的分类问题（你早就知道我们在做分类问题了，不是吗？），我们将使用一个简单但非常强大的库：CatBoost。它是由 Yandex 构建和维护的，并提供了一个高层次的 API，方便我们使用提升树。它与 XGBoost 相似，但底层的实现方式并不完全相同。

CatBoost 提供了一个很好的包装器，用来处理不同类型的特征。在我们的例子中，一些特征可以被视为“文本”，因为它们是单词的连接，例如“Calvin Klein;BCBGeneration;Hanes”。处理这种类型的特征有时可能会很麻烦，因为你需要用到文本分割器、分词器、词形还原器等工具。幸运的是，CatBoost 可以为我们处理一切！

```py
# Define features
features = {
    'numerical': ['retail_price', 'age'],
    'static': ['gender', 'country', 'city'],
    'dynamic': ['brand', 'department', 'category']
}

# Build CatBoost "pools", which are datasets
train_pool = cb.Pool(
    X_train,
    y_train,
    cat_features=features.get("static"),
    text_features=features.get("dynamic"),
)

validation_pool = cb.Pool(
    X_val,
    y_val,
    cat_features=features.get("static"),
    text_features=features.get("dynamic"),
)

# Specify text processing options to handle our text features
text_processing_options = {
    "tokenizers": [
        {"tokenizer_id": "SemiColon", "delimiter": ";", "lowercasing": "false"}
    ],
    "dictionaries": [{"dictionary_id": "Word", "gram_order": "1"}],
    "feature_processing": {
        "default": [
            {
                "dictionaries_names": ["Word"],
                "feature_calcers": ["BoW"],
                "tokenizers_names": ["SemiColon"],
            }
        ],
    },
}
```

我们现在准备好定义并训练我们的模型。由于参数非常多，今天的范围无法逐一讲解，但欢迎大家自己查看 API。

为了简洁起见，我们今天不进行超参数调优，但这显然是数据科学家工作的重要组成部分！

```py
# Train the model
model = cb.CatBoostClassifier(
    iterations=200,
    loss_function="Logloss",
    random_state=42,
    verbose=1,
    auto_class_weights="SqrtBalanced",
    use_best_model=True,
    text_processing=text_processing_options,
    eval_metric='AUC'
)

model.fit(
    train_pool, 
    eval_set=validation_pool, 
    verbose=10
)
```

就这样，我们的模型训练好了。我们完成了吗？

不对。我们需要检查模型在训练和测试之间的表现是否一致。训练和测试之间的巨大差距意味着我们的模型出现了过拟合（即“死记硬背训练数据，而在预测未知数据时表现差”）。

对于我们的模型评估，我们将使用 ROC-AUC 分数。今天也不会深入探讨这个内容，但根据我个人的经验，这是一个通常非常稳健的评估指标，比准确率要好得多。

> 关于准确度的简短备注：我通常不推荐将其作为评估指标。想象一下一个不平衡的数据集，其中有 1% 的正样本和 99% 的负样本。一个总是预测 0 的非常简单的模型，它的准确度是多少？99%。所以在这里，准确度并不有帮助。

```py
from sklearn.metrics import roc_auc_score

print(f"ROC-AUC for train set      : {roc_auc_score(y_true=y_train, y_score=model.predict(X_train)):.2f}")
print(f"ROC-AUC for validation set : {roc_auc_score(y_true=y_val, y_score=model.predict(X_val)):.2f}")
print(f"ROC-AUC for test set       : {roc_auc_score(y_true=y_test, y_score=model.predict(X_test)):.2f}")
```

```py
ROC-AUC for train set      : 0.612
ROC-AUC for validation set : 0.586
ROC-AUC for test set       : 0.622
```

老实说，0.62 的 AUC 远不算好，对于你这个专家级的数据科学家来说有些让人失望。我们的模型肯定需要一些参数调优，或许我们也应该更加认真地进行特征工程。

但这已经比随机预测要好（呼）：

```py
# random predictions

print(f"ROC-AUC for train set      : {roc_auc_score(y_true=y_train, y_score=np.random.rand(len(y_train))):.3f}")
print(f"ROC-AUC for validation set : {roc_auc_score(y_true=y_val, y_score=np.random.rand(len(y_val))):.3f}")
print(f"ROC-AUC for test set       : {roc_auc_score(y_true=y_test, y_score=np.random.rand(len(y_test))):.3f}")
```

```py
ROC-AUC for train set      : 0.501
ROC-AUC for validation set : 0.499
ROC-AUC for test set       : 0.501
```

假设我们现在对我们的模型和笔记本感到满意。此时，业余的数据科学家可能就会停下来。那么，我们该如何迈出下一步，成为生产环境下的准备好模型呢？

# 迈向生产

## 认识 Docker

Docker 是一套平台即服务（PaaS）产品，使用操作系统级虚拟化技术将软件打包为称为容器的包进行交付。也就是说，可以把 Docker 想象成一种可以在任何地方运行的代码，它能避免“在你的机器上能工作，但在我的机器上不行”的情况。

为什么使用 Docker？因为除了能够分享代码、保留版本并确保代码在任何地方都能轻松部署等酷炫的功能外，它还可以用于构建管道。请耐心听我解释，随着我们的进展你会明白的。

构建容器化应用的第一步是重构并清理我们凌乱的笔记本。我们将为这个非常简单的示例定义两个文件，`preprocess.py` 和 `train.py`，并将它们放入 `src` 目录中。我们还会包括一个包含所有内容的 `requirements.txt` 文件。

```py
# src/preprocess.py

from sklearn.model_selection import train_test_split
from google.cloud import bigquery

def create_dataset_from_bq():
    query = """
        SELECT 
          transactions.user_id,
          products.brand,
          products.category,
          products.department,
          products.retail_price,
          users.gender,
          users.age,
          users.created_at,
          users.country,
          users.city,
          transactions.created_at
        FROM `bigquery-public-data.thelook_ecommerce.order_items` as transactions
        LEFT JOIN `bigquery-public-data.thelook_ecommerce.users` as users
          ON transactions.user_id = users.id
        LEFT JOIN `bigquery-public-data.thelook_ecommerce.products` as products
          ON transactions.product_id = products.id
        WHERE status <> 'Cancelled'
    """
    client = bigquery.Client(project='<replace_with_your_project_id>')
    df = client.query(query).to_dataframe()
    print(f"{len(df)} rows loaded.")

    # Compute recurrent customers
    recurrent_customers = df.groupby('user_id')['created_at'].count().to_frame("n_purchases")

    # Merge with dataset and filter those with more than 1 purchase
    df = df.merge(recurrent_customers, left_on='user_id', right_index=True, how='inner')
    df = df.query('n_purchases > 1')

    # Fill missing value
    df.fillna('NA', inplace=True)

    target_brands = [
        'Allegra K', 
        'Calvin Klein', 
        'Carhartt', 
        'Hanes', 
        'Volcom', 
        'Nautica', 
        'Quiksilver', 
        'Diesel',
        'Dockers', 
        'Hurley'
    ]

    aggregation_columns = ['brand', 'department', 'category']

    # Group purchases by user chronologically
    df_agg = (df.sort_values('created_at')
              .groupby(['user_id', 'gender', 'country', 'city', 'age'], as_index=False)[['brand', 'department', 'category']]
              .agg({k: ";".join for k in ['brand', 'department', 'category']})
             )

    # Create the target
    df_agg['last_purchase_brand'] = df_agg['brand'].apply(lambda x: x.split(";")[-1])
    df_agg['target'] = df_agg['last_purchase_brand'].isin(target_brands)*1

    df_agg['age'] = df_agg['age'].astype(float)

    # Remove last item of sequence features to avoid target leakage :
    for col in aggregation_columns:
        df_agg[col] = df_agg[col].apply(lambda x: ";".join(x.split(";")[:-1]))

    df_agg.drop('last_purchase_category', axis=1, inplace=True)
    df_agg.drop('last_purchase_brand', axis=1, inplace=True)
    df_agg.drop('user_id', axis=1, inplace=True)
    return df_agg

def make_data_splits(df_agg):

    df_train, df_val = train_test_split(df_agg, stratify=df_agg['target'], test_size=0.2)
    print(f"{len(df_train)} samples in train")

    df_val, df_test = train_test_split(df_val, stratify=df_val['target'], test_size=0.5)
    print(f"{len(df_val)} samples in val")
    print(f"{len(df_test)} samples in test")

    return df_train, df_val, df_test
```

```py
# src/train.py

import catboost as cb
import pandas as pd
import sklearn as sk
import numpy as np
import argparse

from sklearn.metrics import roc_auc_score

def train_and_evaluate(
        train_path: str,
        validation_path: str,
        test_path: str
    ):
    df_train = pd.read_csv(train_path)
    df_val = pd.read_csv(validation_path)
    df_test = pd.read_csv(test_path)

    df_train.fillna('NA', inplace=True)
    df_val.fillna('NA', inplace=True)
    df_test.fillna('NA', inplace=True)

    X_train, y_train = df_train.iloc[:, :-1], df_train['target']
    X_val, y_val = df_val.iloc[:, :-1], df_val['target']
    X_test, y_test = df_test.iloc[:, :-1], df_test['target']

    features = {
        'numerical': ['retail_price', 'age'],
        'static': ['gender', 'country', 'city'],
        'dynamic': ['brand', 'department', 'category']
    }

    train_pool = cb.Pool(
        X_train,
        y_train,
        cat_features=features.get("static"),
        text_features=features.get("dynamic"),
    )

    validation_pool = cb.Pool(
        X_val,
        y_val,
        cat_features=features.get("static"),
        text_features=features.get("dynamic"),
    )

    test_pool = cb.Pool(
        X_test,
        y_test,
        cat_features=features.get("static"),
        text_features=features.get("dynamic"),
    )

    params = CatBoostParams()

    text_processing_options = {
        "tokenizers": [
            {"tokenizer_id": "SemiColon", "delimiter": ";", "lowercasing": "false"}
        ],
        "dictionaries": [{"dictionary_id": "Word", "gram_order": "1"}],
        "feature_processing": {
            "default": [
                {
                    "dictionaries_names": ["Word"],
                    "feature_calcers": ["BoW"],
                    "tokenizers_names": ["SemiColon"],
                }
            ],
        },
    }

    # Train the model
    model = cb.CatBoostClassifier(
        iterations=200,
        loss_function="Logloss",
        random_state=42,
        verbose=1,
        auto_class_weights="SqrtBalanced",
        use_best_model=True,
        text_processing=text_processing_options,
        eval_metric='AUC'
    )

    model.fit(
        train_pool, 
        eval_set=validation_pool, 
        verbose=10
    )

    roc_train = roc_auc_score(y_true=y_train, y_score=model.predict(X_train))
    roc_eval  = roc_auc_score(y_true=y_val, y_score=model.predict(X_val))
    roc_test  = roc_auc_score(y_true=y_test, y_score=model.predict(X_test))
    print(f"ROC-AUC for train set      : {roc_train:.2f}")
    print(f"ROC-AUC for validation set : {roc_eval:.2f}")
    print(f"ROC-AUC for test.      set : {roc_test:.2f}")

    return {"model": model, "scores": {"train": roc_train, "eval": roc_eval, "test": roc_test}}

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument("--train-path", type=str)
    parser.add_argument("--validation-path", type=str)
    parser.add_argument("--test-path", type=str)
    parser.add_argument("--output-dir", type=str)
    args, _ = parser.parse_known_args()
    _ = train_and_evaluate(
        args.train_path,
        args.validation_path,
        args.test_path)
```

现在干净多了。你实际上可以从命令行启动你的脚本了！

```py
$ python train.py --train-path xxx --validation-path yyy etc.
```

现在我们准备好构建我们的 Docker 镜像了。为此，我们需要在项目根目录下编写一个 Dockerfile：

```py
# Dockerfile

FROM python:3.8-slim
WORKDIR /
COPY requirements.txt /requirements.txt
COPY src /src
RUN pip install --upgrade pip && pip install -r requirements.txt
ENTRYPOINT [ "bash" ]
```

这将获取我们的需求，复制 `src` 文件夹及其内容，并在镜像构建时使用 pip 安装需求。

要将此镜像构建并部署到容器注册表，我们可以使用 Google Cloud SDK 和 `gcloud` 命令：

```py
PROJECT_ID = ...
IMAGE_NAME=f'thelook_training_demo'
IMAGE_TAG='latest'
IMAGE_URI='eu.gcr.io/{}/{}:{}'.format(PROJECT_ID, IMAGE_NAME, IMAGE_TAG)

!gcloud builds submit --tag $IMAGE_URI .
```

如果一切顺利，你应该会看到像这样的结果：

![](../Images/696d3dbba8791000cd8405119c1144d3.png)

## Vertex Pipelines，迈向生产

Docker 镜像是进行生产级机器学习的第一步。下一步是构建我们所称之为“管道”的东西。管道是一系列由名为 Kubeflow 的框架协调的操作。Kubeflow 可以在 Google Cloud 上的 Vertex AI 运行。

关于在生产中偏好管道而非笔记本的原因，可能存在争议，但根据我的经验，我给出三个理由：

1.  **监控与可复现性**：每个管道都会与其产物（数据集、模型、指标）一起存储，这意味着你可以比较不同的运行、重新运行它们并进行审计。每次重新运行一个笔记本时，你都会失去历史记录（或者你得自己管理产物和日志。祝你好运。）

1.  **成本**：运行一个笔记本意味着你需要一台能够运行它的机器。— 这台机器是有成本的，对于大型模型或庞大的数据集，你需要具有高规格的虚拟机。

    — 你必须记得在不使用它时将其关闭。

    — 或者，如果你选择不使用虚拟机并且有其他应用程序在运行，你可能会直接崩溃本地机器。

    — Vertex AI管道是一个*无服务器*服务，意味着你不需要管理底层基础设施，只需为你使用的部分付费，即执行时间。

1.  **可扩展性**：当你在本地笔记本上同时运行几十个实验时，祝你好运。你将回到使用虚拟机，扩展虚拟机，并重新阅读上面的要点。

偏好管道而非笔记本的最后一个原因也是主观的，并且高度可辩论，但在我看来，笔记本根本不是为定时调度的工作负载设计的。它们非常适合用于探索。

至少使用Docker镜像来进行定时任务，或者如果你想做得更规范，可以使用管道，但永远不要在生产环境中运行笔记本。

不再多说，接下来我们写出管道的组件：

```py
# IMPORT REQUIRED LIBRARIES
from kfp.v2 import dsl
from kfp.v2.dsl import (Artifact,
                        Dataset,
                        Input,
                        Model,
                        Output,
                        Metrics,
                        Markdown,
                        HTML,
                        component, 
                        OutputPath, 
                        InputPath)
from kfp.v2 import compiler
from google.cloud.aiplatform import pipeline_jobs

%watermark --packages kfp,google.cloud.aiplatform
```

```py
kfp                    : 2.7.0
google.cloud.aiplatform: 1.50.0
```

第一个组件将从Bigquery下载数据并将其保存为CSV文件。

我们使用的BASE_IMAGE是我们之前构建的镜像！我们可以用它来导入我们在Docker镜像`src`文件夹中定义的模块和函数：

```py
@component(
    base_image=BASE_IMAGE,
    output_component_file="get_data.yaml"
)
def create_dataset_from_bq(
    output_dir: Output[Dataset],
):

    from src.preprocess import create_dataset_from_bq

    df = create_dataset_from_bq()

    df.to_csv(output_dir.path, index=False)
```

下一步：拆分数据

```py
@component(
    base_image=BASE_IMAGE,
    output_component_file="train_test_split.yaml",
)
def make_data_splits(
    dataset_full: Input[Dataset],
    dataset_train: Output[Dataset],
    dataset_val: Output[Dataset],
    dataset_test: Output[Dataset]):

    import pandas as pd
    from src.preprocess import make_data_splits

    df_agg = pd.read_csv(dataset_full.path)

    df_agg.fillna('NA', inplace=True)

    df_train, df_val, df_test = make_data_splits(df_agg)
    print(f"{len(df_train)} samples in train")
    print(f"{len(df_val)} samples in train")
    print(f"{len(df_test)} samples in test")

    df_train.to_csv(dataset_train.path, index=False)
    df_val.to_csv(dataset_val.path, index=False)
    df_test.to_csv(dataset_test.path, index=False)
```

下一步：模型训练。我们将保存模型评分，以便在下一步展示它们：

```py
@component(
    base_image=BASE_IMAGE,
    output_component_file="train_model.yaml",
)
def train_model(
    dataset_train: Input[Dataset],
    dataset_val: Input[Dataset],
    dataset_test: Input[Dataset],
    model: Output[Model]
):

    import json
    from src.train import train_and_evaluate

    outputs = train_and_evaluate(
        dataset_train.path,
        dataset_val.path,
        dataset_test.path
    )
    cb_model = outputs['model']
    scores = outputs['scores']

    model.metadata["framework"] = "catboost" 
    # Save the model as an artifact
    with open(model.path, 'w') as f: 
        json.dump(scores, f)
```

最后一步是计算指标（这些指标实际上是在模型训练过程中计算的）。这只是一个必要步骤，但它能让你看到构建轻量级组件是多么简单。注意，在这种情况下我们并没有从BASE_IMAGE构建组件（它有时可能非常大），而是仅仅构建了一个包含必要组件的轻量级镜像：

```py
@component(
    base_image="python:3.9",
    output_component_file="compute_metrics.yaml",
)
def compute_metrics(
    model: Input[Model],
    train_metric: Output[Metrics],
    val_metric: Output[Metrics],
    test_metric: Output[Metrics]
):

    import json

    file_name = model.path
    with open(file_name, 'r') as file:  
        model_metrics = json.load(file)

    train_metric.log_metric('train_auc', model_metrics['train'])
    val_metric.log_metric('val_auc', model_metrics['eval'])
    test_metric.log_metric('test_auc', model_metrics['test'])
```

通常还会有其他步骤可以加入，例如如果我们希望将模型部署为API端点，但这属于更高级的内容，需要为模型的服务创建另一个Docker镜像。下一次会介绍。

现在让我们把组件连接起来：

```py
# USE TIMESTAMP TO DEFINE UNIQUE PIPELINE NAMES
TIMESTAMP = dt.datetime.now().strftime("%Y%m%d%H%M%S")
DISPLAY_NAME = 'pipeline-thelook-demo-{}'.format(TIMESTAMP)
PIPELINE_ROOT = f"{BUCKET_NAME}/pipeline_root/"

# Define the pipeline. Notice how steps reuse outputs from previous steps
@dsl.pipeline(
    pipeline_root=PIPELINE_ROOT,
    # A name for the pipeline. Use to determine the pipeline Context.
    name="pipeline-demo"   
)

def pipeline(
    project: str = PROJECT_ID,
    region: str = REGION, 
    display_name: str = DISPLAY_NAME
):

    load_data_op = create_dataset_from_bq()
    train_test_split_op = make_data_splits(
        dataset_full=load_data_op.outputs["output_dir"]
    )
    train_model_op = train_model(
        dataset_train=train_test_split_op.outputs["dataset_train"], 
        dataset_val=train_test_split_op.outputs["dataset_val"],
        dataset_test=train_test_split_op.outputs["dataset_test"],
        )
    model_evaluation_op = compute_metrics(
        model=train_model_op.outputs["model"]
    )

# Compile the pipeline as JSON
compiler.Compiler().compile(
    pipeline_func=pipeline,
    package_path='thelook_pipeline.json'
)

# Start the pipeline
start_pipeline = pipeline_jobs.PipelineJob(
    display_name="thelook-demo-pipeline",
    template_path="thelook_pipeline.json",
    enable_caching=False,
    location=REGION,
    project=PROJECT_ID
)

# Run the pipeline
start_pipeline.run(service_account=<your_service_account_here>)
```

如果一切顺利，你现在应该能在Vertex UI中看到你的管道：

![](../Images/ed0f2a97ea1def0df9a9ccdac40f0be6.png)

你可以点击它，查看不同的步骤：

![](../Images/c8ce67a3cf2ebdcff0665e1cbc227d01.png)

# 结论

数据科学，尽管所有不写代码/低代码的爱好者告诉你不需要做开发者就能做机器学习，但它依然是一项真正的工作。像所有工作一样，它需要技能、概念和工具，超越了笔记本的范畴。

对于那些渴望成为数据科学家的朋友们，这就是这份工作的现实。

编程愉快。
