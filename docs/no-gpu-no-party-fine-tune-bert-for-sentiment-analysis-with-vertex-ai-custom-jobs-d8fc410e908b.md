# 没有GPU，没派对：使用Vertex AI自定义作业微调BERT进行情感分析

> 原文：[https://towardsdatascience.com/no-gpu-no-party-fine-tune-bert-for-sentiment-analysis-with-vertex-ai-custom-jobs-d8fc410e908b?source=collection_archive---------5-----------------------#2024-06-03](https://towardsdatascience.com/no-gpu-no-party-fine-tune-bert-for-sentiment-analysis-with-vertex-ai-custom-jobs-d8fc410e908b?source=collection_archive---------5-----------------------#2024-06-03)

## 通过无服务器作业加速训练过程

[](https://medium.com/@benjamin_47408?source=post_page---byline--d8fc410e908b--------------------------------)[![Benjamin Etienne](../Images/cad8bc2d4b900575e76b7cf9debc9eea.png)](https://medium.com/@benjamin_47408?source=post_page---byline--d8fc410e908b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d8fc410e908b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d8fc410e908b--------------------------------) [Benjamin Etienne](https://medium.com/@benjamin_47408?source=post_page---byline--d8fc410e908b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8fc410e908b--------------------------------) ·13分钟阅读·2024年6月3日

--

![](../Images/de04296aee8fcd10eae449ceb2f6d4ce.png)

图片来源：[yns plt](https://unsplash.com/@ynsplt?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*TL;DR：如何在Vertex上使用GPU启动Pytorch训练作业。包含示例代码。*

在我[之前的文章](/machine-learning-on-gcp-from-dev-to-prod-with-vertex-ai-c9e42c4b366f)中，我提到过，当本地训练大规模模型时，如果资源有限，这并不是一个好习惯。有时候你根本没有选择，但有时候，你可以使用像 Google Cloud Platform 这样的云服务提供商，它能够显著加速你的训练过程，方法如下：

+   提供您定制配置（内存、GPU 等）的先进机器

+   允许您同时启动多个作业，并最终选择最佳模型

*更不用说，将训练任务卸载到云端将减轻您个人机器的负担。我已经亲眼看到，个人笔记本电脑训练模型一周后电池几乎融化。假期回来后，我的触控板居然快要掉出来了。*

**在本文中，我们将以一个具体的用例为例，展示如何在社交媒体评论上微调BERT模型进行情感分析。正如我们所看到的，使用CPU训练这种模型是非常繁琐且不理想的。因此，我们将探讨如何利用Google Cloud Platform通过仅花费60美分来加速这一过程，使用GPU进行训练。**

# 总结

+   什么是BERT

+   什么是情感分析

+   获取并准备数据

+   使用小型BERT预训练模型

+   创建数据加载器。

+   编写主要脚本以训练模型。

+   将脚本Docker化。

+   构建并推送镜像到Google Cloud。

+   在Vertex AI上创建一个作业。

## 什么是BERT？

BERT代表双向编码器表示（Bidirectional Encoder Representations from Transformers），由Google于2018年开源。它主要用于NLP任务，因为它被训练用来捕捉句子的语义并提供丰富的词向量（表示）。与其他模型如Word2Vec和Glove的不同之处在于，它使用Transformers来处理文本。Transformers（如果你想了解更多，可以参考我之前的文章）是一类神经网络，它们有点像RNN，可以双向处理序列，因此能够捕捉到例如一个词的上下文。

## 什么是情感分析？

情感分析是NLP领域中的一项特定任务，目标是将文本分类为与其情感色彩相关的类别。情感色彩通常表现为*积极*、*消极*或*中立*。它通常用于分析文字记录、社交媒体上的帖子、产品评论等。

## 在社交媒体数据上微调BERT模型。

## 获取和准备数据。

我们将使用的数据集来自Kaggle，你可以在这里下载：[https://www.kaggle.com/datasets/farisdurrani/sentimentsearch](https://www.kaggle.com/datasets/farisdurrani/sentimentsearch)（CC BY 4.0许可证）。在我的实验中，我只选择了来自Facebook和Twitter的数据集。

以下代码片段将处理csv文件，并将数据分割为3部分（训练集、验证集和测试集），然后保存到你指定的位置。我建议将它们保存在Google Cloud Storage中。

你可以使用以下命令运行脚本：

```py
python make_splits --output-dir gs://your-bucket/
```

```py
import pandas as pd
import argparse
import numpy as np
from sklearn.model_selection import train_test_split

def make_splits(output_dir):
    df=pd.concat([
        pd.read_csv("data/farisdurrani/twitter_filtered.csv"),
        pd.read_csv("data/farisdurrani/facebook_filtered.csv")
    ])
    df = df.dropna(subset=['sentiment'], axis=0)
    df['Target'] = df['sentiment'].apply(lambda x: 1 if x==0 else np.sign(x)+1).astype(int)

    df_train, df_ = train_test_split(df, stratify=df['Target'], test_size=0.2)
    df_eval, df_test = train_test_split(df_, stratify=df_['Target'], test_size=0.5)

    print(f"Files will be saved in {output_dir}")
    df_train.to_csv(output_dir + "/train.csv", index=False)
    df_eval.to_csv(output_dir + "/eval.csv", index=False)
    df_test.to_csv(output_dir + "/test.csv", index=False)

    print(f"Train : ({df_train.shape}) samples")
    print(f"Val : ({df_eval.shape}) samples")
    print(f"Test : ({df_test.shape}) samples")

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--output-dir')
    args, _ = parser.parse_known_args()
    make_splits(args.output_dir)
```

数据大致应如下所示：

![](../Images/bd678c8886d35aadd886a31d031ad2ed.png)

（作者提供的图片）

## 使用小型BERT预训练模型。

对于我们的模型，我们将使用轻量级BERT模型BERT-Tiny。该模型已经在大量数据上进行了预训练，但不一定是社交媒体数据，也不一定是为了进行情感分析而预训练的。因此，我们将对其进行微调。

它仅包含2层，每层有128个单元，完整的模型列表可以在[这里](https://github.com/google-research/bert)查看，如果你想使用更大的模型。

首先让我们创建一个`main.py`文件，包含所有必要的模块：

```py
import pandas as pd
import argparse
import tensorflow as tf
import tensorflow_hub as hub
import tensorflow_text as text
import logging
import os
os.environ["TFHUB_MODEL_LOAD_FORMAT"] = "UNCOMPRESSED"

def train_and_evaluate(**params):
    pass
    # will be updated as we go
```

让我们也在一个专用的`requirements.txt`文件中写下我们的需求。

```py
transformers==4.40.1
torch==2.2.2
pandas==2.0.3
scikit-learn==1.3.2
gcsfs
```

我们现在将加载两部分数据来训练我们的模型：

+   ***分词器***，它将负责将文本输入拆分为BERT训练时使用的词元。

+   ***模型***本身。

你可以从Huggingface[这里](http://google/bert_uncased_L-2_H-128_A-2)获得这两者。你也可以将它们下载到Cloud Storage中。我就是这么做的，因此会通过以下方式加载它们：

```py
 # Load pretrained tokenizers and bert model
tokenizer = BertTokenizer.from_pretrained('models/bert_uncased_L-2_H-128_A-2/vocab.txt')
model = BertModel.from_pretrained('models/bert_uncased_L-2_H-128_A-2')
```

现在让我们将以下内容添加到文件中：

```py
class SentimentBERT(nn.Module):
    def __init__(self, bert_model):
        super().__init__()
        self.bert_module = bert_model
        self.dropout = nn.Dropout(0.1)
        self.final = nn.Linear(in_features=128, out_features=3, bias=True) 

        # Uncomment the below if you only want to retrain certain layers.
        # self.bert_module.requires_grad_(False)
        # for param in self.bert_module.encoder.parameters():
        #     param.requires_grad = True

    def forward(self, inputs):
        ids, mask, token_type_ids = inputs['ids'], inputs['mask'], inputs['token_type_ids']
        # print(ids.size(), mask.size(), token_type_ids.size())
        x = self.bert_module(ids, mask, token_type_ids)
        x = self.dropout(x['pooler_output'])
        out = self.final(x)
        return out
```

在这里稍作休息。我们在重用现有模型时有几种选择。

+   **迁移学习**：我们冻结模型的权重，并将其作为“特征提取器”。因此，我们可以在后续添加额外的层。这在计算机视觉中非常常见，比如VGG、Xception等模型可以在小数据集上被重新训练，作为自定义模型的一部分。

+   **微调**：我们解冻模型的全部或部分权重，并在自定义数据集上重新训练模型。这是在训练自定义LLM时的首选方法。

关于迁移学习和微调的更多细节，请参见[这里](https://www.tensorflow.org/tutorials/images/transfer_learning)：

在模型中，我们选择解冻整个模型，但也可以选择冻结预训练BERT模块中的一层或多层，看看它对性能的影响。

这里的关键是，在BERT模块后添加一个全连接层，将其“连接”到我们的分类任务，因此最终的层有3个单元。这将使我们能够重用预训练BERT的权重，并将我们的模型调整到我们的任务。

## 创建数据加载器

要创建数据加载器，我们将需要上述加载的Tokenizer。Tokenizer接受一个字符串作为输入，并返回多个输出，其中包括我们可以找到的标记（在我们的案例中是‘input_ids’）：

![](../Images/894b0cbe3aec033e162d3be5ca1ddc3d.png)

BERT的分词器有点特殊，它会返回多个输出，但最重要的是`input_ids`：它们是用于编码我们句子的标记。它们可能是单词，或者单词的一部分。例如，单词“looking”可能由两个标记组成：“look”和“##ing”。

现在让我们创建一个数据加载器模块来处理我们的数据集：

```py
class BertDataset(Dataset):
    def __init__(self, df, tokenizer, max_length=100):
        super(BertDataset, self).__init__()
        self.df=df
        self.tokenizer=tokenizer
        self.target=self.df['Target']
        self.max_length=max_length

    def __len__(self):
        return len(self.df)

    def __getitem__(self, idx):

        X = self.df['bodyText'].values[idx]
        y = self.target.values[idx]

        inputs = self.tokenizer.encode_plus(
            X,
            pad_to_max_length=True,
            add_special_tokens=True,
            return_attention_mask=True,
            max_length=self.max_length,
        )
        ids = inputs["input_ids"]
        token_type_ids = inputs["token_type_ids"]
        mask = inputs["attention_mask"]

        x = {
            'ids': torch.tensor(ids, dtype=torch.long).to(DEVICE),
            'mask': torch.tensor(mask, dtype=torch.long).to(DEVICE),
            'token_type_ids': torch.tensor(token_type_ids, dtype=torch.long).to(DEVICE)
            }
        y = torch.tensor(y, dtype=torch.long).to(DEVICE)

        return x, y
```

## 编写训练模型的主脚本

首先，让我们定义两个函数来处理训练和评估步骤：

```py
def train(epoch, model, dataloader, loss_fn, optimizer, max_steps=None):
    model.train()
    total_acc, total_count = 0, 0
    log_interval = 50
    start_time = time.time()

    for idx, (inputs, label) in enumerate(dataloader):
        optimizer.zero_grad()
        predicted_label = model(inputs)

        loss = loss_fn(predicted_label, label)
        loss.backward()
        optimizer.step()

        total_acc += (predicted_label.argmax(1) == label).sum().item()
        total_count += label.size(0)

        if idx % log_interval == 0:
            elapsed = time.time() - start_time
            print(
                "Epoch {:3d} | {:5d}/{:5d} batches "
                "| accuracy {:8.3f} | loss {:8.3f} ({:.3f}s)".format(
                    epoch, idx, len(dataloader), total_acc / total_count, loss.item(), elapsed
                )
            )
            total_acc, total_count = 0, 0
            start_time = time.time()

        if max_steps is not None:
            if idx == max_steps:
                return {'loss': loss.item(), 'acc': total_acc / total_count}

    return {'loss': loss.item(), 'acc': total_acc / total_count}

def evaluate(model, dataloader, loss_fn):
    model.eval()
    total_acc, total_count = 0, 0

    with torch.no_grad():
        for idx, (inputs, label) in enumerate(dataloader):
            predicted_label = model(inputs)
            loss = loss_fn(predicted_label, label)
            total_acc += (predicted_label.argmax(1) == label).sum().item()
            total_count += label.size(0)

    return {'loss': loss.item(), 'acc': total_acc / total_count}
```

我们离让主脚本运行起来越来越近了。让我们将各个部分拼接在一起。我们有：

+   一个`BertDataset`类，用于处理数据的加载

+   一个`SentimentBERT`模型，它基于我们的Tiny-BERT模型并添加了一个额外的层来适应我们的自定义用例

+   `train()`和`eval()`函数，用于处理这些步骤

+   一个`train_and_eval()`函数，将所有内容组合在一起

我们将使用`argparse`来使我们能够通过参数启动脚本。这些参数通常是训练/评估/测试文件，用于将数据集传递给我们的模型，模型存储路径以及与训练相关的参数。

```py
import pandas as pd
import time
import torch.nn as nn
import torch
import logging
import numpy as np
import argparse

from torch.utils.data import Dataset, DataLoader
from transformers import BertTokenizer, BertModel

logging.basicConfig(format='%(asctime)s [%(levelname)s]: %(message)s', level=logging.DEBUG)
logging.getLogger().setLevel(logging.INFO)

# --- CONSTANTS ---
BERT_MODEL_NAME = 'small_bert/bert_en_uncased_L-2_H-128_A-2'

if torch.cuda.is_available():
    logging.info(f"GPU: {torch.cuda.get_device_name(0)} is available.")
    DEVICE = torch.device('cuda')
else:
    logging.info("No GPU available. Training will run on CPU.")
    DEVICE = torch.device('cpu')

# --- Data preparation and tokenization ---
class BertDataset(Dataset):
    def __init__(self, df, tokenizer, max_length=100):
        super(BertDataset, self).__init__()
        self.df=df
        self.tokenizer=tokenizer
        self.target=self.df['Target']
        self.max_length=max_length

    def __len__(self):
        return len(self.df)

    def __getitem__(self, idx):

        X = self.df['bodyText'].values[idx]
        y = self.target.values[idx]

        inputs = self.tokenizer.encode_plus(
            X,
            pad_to_max_length=True,
            add_special_tokens=True,
            return_attention_mask=True,
            max_length=self.max_length,
        )
        ids = inputs["input_ids"]
        token_type_ids = inputs["token_type_ids"]
        mask = inputs["attention_mask"]

        x = {
            'ids': torch.tensor(ids, dtype=torch.long).to(DEVICE),
            'mask': torch.tensor(mask, dtype=torch.long).to(DEVICE),
            'token_type_ids': torch.tensor(token_type_ids, dtype=torch.long).to(DEVICE)
            }
        y = torch.tensor(y, dtype=torch.long).to(DEVICE)

        return x, y

# --- Model definition ---
class SentimentBERT(nn.Module):
    def __init__(self, bert_model):
        super().__init__()
        self.bert_module = bert_model
        self.dropout = nn.Dropout(0.1)
        self.final = nn.Linear(in_features=128, out_features=3, bias=True) 

    def forward(self, inputs):
        ids, mask, token_type_ids = inputs['ids'], inputs['mask'], inputs['token_type_ids']
        x = self.bert_module(ids, mask, token_type_ids)
        x = self.dropout(x['pooler_output'])
        out = self.final(x)
        return out

# --- Training loop ---
def train(epoch, model, dataloader, loss_fn, optimizer, max_steps=None):
    model.train()
    total_acc, total_count = 0, 0
    log_interval = 50
    start_time = time.time()

    for idx, (inputs, label) in enumerate(dataloader):
        optimizer.zero_grad()
        predicted_label = model(inputs)

        loss = loss_fn(predicted_label, label)
        loss.backward()
        optimizer.step()

        total_acc += (predicted_label.argmax(1) == label).sum().item()
        total_count += label.size(0)

        if idx % log_interval == 0:
            elapsed = time.time() - start_time
            print(
                "Epoch {:3d} | {:5d}/{:5d} batches "
                "| accuracy {:8.3f} | loss {:8.3f} ({:.3f}s)".format(
                    epoch, idx, len(dataloader), total_acc / total_count, loss.item(), elapsed
                )
            )
            total_acc, total_count = 0, 0
            start_time = time.time()

        if max_steps is not None:
            if idx == max_steps:
                return {'loss': loss.item(), 'acc': total_acc / total_count}

    return {'loss': loss.item(), 'acc': total_acc / total_count}

# --- Validation loop ---
def evaluate(model, dataloader, loss_fn):
    model.eval()
    total_acc, total_count = 0, 0

    with torch.no_grad():
        for idx, (inputs, label) in enumerate(dataloader):
            predicted_label = model(inputs)
            loss = loss_fn(predicted_label, label)
            total_acc += (predicted_label.argmax(1) == label).sum().item()
            total_count += label.size(0)

    return {'loss': loss.item(), 'acc': total_acc / total_count}

# --- Main function ---
def train_and_evaluate(**params):

    logging.info("running with the following params :")
    logging.info(params)

    # Load pretrained tokenizers and bert model
    # update the paths to whichever you are using
    tokenizer = BertTokenizer.from_pretrained('models/bert_uncased_L-2_H-128_A-2/vocab.txt')
    model = BertModel.from_pretrained('models/bert_uncased_L-2_H-128_A-2')

    # Training parameters
    epochs = int(params.get('epochs'))
    batch_size = int(params.get('batch_size'))
    learning_rate = float(params.get('learning_rate'))

    #  Load the data
    df_train = pd.read_csv(params.get('training_file'))
    df_eval = pd.read_csv(params.get('validation_file'))
    df_test = pd.read_csv(params.get('testing_file'))

    # Create dataloaders
    train_ds = BertDataset(df_train, tokenizer, max_length=100)
    train_loader = DataLoader(dataset=train_ds,batch_size=batch_size, shuffle=True)
    eval_ds = BertDataset(df_eval, tokenizer, max_length=100)
    eval_loader = DataLoader(dataset=eval_ds,batch_size=batch_size)
    test_ds = BertDataset(df_test, tokenizer, max_length=100)
    test_loader = DataLoader(dataset=test_ds,batch_size=batch_size)

    # Create the model
    classifier = SentimentBERT(bert_model=model).to(DEVICE)
    total_parameters = sum([np.prod(p.size()) for p in classifier.parameters()])
    model_parameters = filter(lambda p: p.requires_grad, classifier.parameters())
    params = sum([np.prod(p.size()) for p in model_parameters])
    logging.info(f"Total params : {total_parameters} - Trainable : {params} ({params/total_parameters*100}% of total)")

    # Optimizer and loss functions
    optimizer = torch.optim.Adam([p for p in classifier.parameters() if p.requires_grad], learning_rate)
    loss_fn = nn.CrossEntropyLoss()

    # If dry run we only
    logging.info(f'Training model with {BERT_MODEL_NAME}')
    if args.dry_run:
        logging.info("Dry run mode")
        epochs = 1
        steps_per_epoch = 1
    else:
        steps_per_epoch = None

    # Action !
    for epoch in range(1, epochs + 1):
        epoch_start_time = time.time()
        train_metrics = train(epoch, classifier, train_loader, loss_fn=loss_fn, optimizer=optimizer, max_steps=steps_per_epoch)
        eval_metrics = evaluate(classifier, eval_loader, loss_fn=loss_fn)

        print("-" * 59)
        print(
            "End of epoch {:3d} - time: {:5.2f}s - loss: {:.4f} - accuracy: {:.4f} - valid_loss: {:.4f} - valid accuracy {:.4f} ".format(
                epoch, time.time() - epoch_start_time, train_metrics['loss'], train_metrics['acc'], eval_metrics['loss'], eval_metrics['acc']
            )
        )
        print("-" * 59)

    if args.dry_run:
        # If dry run, we do not run the evaluation
        return None

    test_metrics = evaluate(classifier, test_loader, loss_fn=loss_fn)

    metrics = {
        'train': train_metrics,
        'val': eval_metrics,
        'test': test_metrics,
    }
    logging.info(metrics)

    # save model and architecture to single file
    if params.get('job_dir') is None:
        logging.warning("No job dir provided, model will not be saved")
    else:
        logging.info("Saving model to {} ".format(params.get('job_dir')))
        torch.save(classifier.state_dict(), params.get('job_dir'))
    logging.info("Bye bye")

if __name__ == '__main__':
    # Create arguments here
    parser = argparse.ArgumentParser()
    parser.add_argument('--training-file', required=True, type=str)
    parser.add_argument('--validation-file', required=True, type=str)
    parser.add_argument('--testing-file', type=str)
    parser.add_argument('--job-dir', type=str)
    parser.add_argument('--epochs', type=float, default=2)
    parser.add_argument('--batch-size', type=float, default=1024)
    parser.add_argument('--learning-rate', type=float, default=0.01)
    parser.add_argument('--dry-run', action="store_true")

    # Parse them
    args, _ = parser.parse_known_args()

    # Execute training
    train_and_evaluate(**vars(args))
```

这很好，但不幸的是，这个模型需要很长时间才能训练完成。实际上，训练约470万参数时，每一步大约需要3秒，在一台配备Intel芯片、16GB内存的MacBook Pro上进行训练。

![](../Images/b5a00cef328f5d4ab3879f010a18ff24.png)

每步3秒，对于1238步和10个epoch的训练来说，可能是相当长的时间…

没有GPU，就没有派对。

# 如何使用Vertex AI并启动派对？

*简短回答：Docker和gcloud。*

如果您的笔记本电脑没有强大的 GPU（就像我们大多数人一样），和/或您不想烧坏笔记本电脑的散热风扇，您可能希望将脚本移至 Google Cloud 等云平台（免责声明：我在工作中使用 Google Cloud）。

Google 的一个优点是，当您使用 Gmail 账户创建自己的项目时，会提供 300 美元的信用额度。

一如既往，当需要将代码转移到其他地方时，Docker 通常是首选解决方案。

## 将脚本 Docker 化

让我们编写一个启用了 GPU 的 Docker 镜像。您可以在官方 Docker 仓库中找到许多 Docker 镜像，我选择了 *pytorch/pytorch:2.2.2-cuda11.8-cudnn8-runtime*，因为我使用的是 Pytorch 2.2.2 版本。请确保选择一个带有 CUDA 的版本，否则您将需要在 Dockerfile 中自己安装它，相信我，除非必须，否则您不希望这样做。

这个 Dockerfile 会预安装必要的 CUDA 依赖和驱动程序，确保我们能够在自定义训练作业中使用它们，并在调用镜像时，使用您传递的参数运行 Python `main.py` 文件。

```py
FROM pytorch/pytorch:2.2.2-cuda11.8-cudnn8-runtime

WORKDIR /src
COPY . .
RUN pip install --upgrade pip && pip install -r requirements.txt

ENTRYPOINT ["python", "main.py"]
```

## 在 Google Cloud 上构建并推送镜像

一旦我们的镜像准备好构建，我们需要构建它并将其推送到一个注册表。您可以选择任何注册表，但 Google Cloud 提供了一个名为 Artefact Registry 的服务。因此，您将能够非常轻松地将镜像存储在 Google Cloud 上。

在您的目录根目录下创建这个小文件，并确保 Dockerfile 文件与其在同一级别：

```py
# build.sh

export PROJECT_ID=<your-project-id>
export IMAGE_REPO_NAME=pt_bert_sentiment
export IMAGE_TAG=dev
export IMAGE_URI=eu.gcr.io/$PROJECT_ID/$IMAGE_REPO_NAME:$IMAGE_TAG

gcloud builds submit --tag $IMAGE_URI .
```

运行 `build.sh` 文件，等待几分钟镜像构建完成后，您应该会看到类似的内容：

**eu.gcr.io/<your-project-id>/pt_bert_sentiment:dev SUCCESS**

## 在 Vertex AI 上创建作业

一旦您的镜像构建并推送到 Artefact Registry，我们就可以告诉 Vertex AI 在我们想要的任何机器上运行这个镜像，包括带有强大 GPU 的机器！Google 在您创建自己的 GCP 项目时会提供 300 美元的信用额度，这对于运行我们的模型是非常足够的。

费用详情请见 [这里](https://cloud.google.com/vertex-ai/pricing#custom-trained_models)。在我们的案例中，我们将选择每小时 0.24 美元的 *n1-standard-4* 机器，并附加每小时 0.40 美元的 *NVIDIA T4* GPU。

![](../Images/8405cc80d311c01147fa5b3ed826a415.png)

（来源：Google Cloud）

![](../Images/0cd5fc0930e342b345abb1060e0593f1.png)

（来源：Google Cloud）

创建一个 `job.sh` 文件，内容如下，指定您所在的地区和使用的机器类型。如果您在其他地区，费用可能会有所不同，请参考上面的链接。

> 您还需要传递参数给您的训练脚本。`gcloud ai custom-jobs create` 的语法由两部分组成：
> 
> - 与作业本身相关的参数：`--region`、`--display-name`、`--worker-pool-spec`、`--service-account` 和 `--args`
> 
> - 与训练相关的参数：`--training-file`、`--epochs` 等。
> 
> 后者需要以`--args`开头，表示所有后续的参数都与训练 Python 脚本相关。
> 
> 例如：假设我们的脚本有 2 个参数 x 和 y，我们可以这样写：
> 
> `--args=x=1,y=2`

```py
# job.sh

export PROJECT_ID=<your-project-id>
export BUCKET=<your-bucket-id>
export REGION="europe-west4"
export SERVICE_ACCOUNT=<your-service-account>
export JOB_NAME="pytorch_bert_training"
export MACHINE_TYPE="n1-standard-4"  # We can specify GPUs here
export ACCELERATOR_TYPE="NVIDIA_TESLA_T4"
export IMAGE_URI="eu.gcr.io/$PROJECT_ID/pt_bert_sentiment:dev"

gcloud ai custom-jobs create \
--region=$REGION \
--display-name=$JOB_NAME \
--worker-pool-spec=machine-type=$MACHINE_TYPE,accelerator-type=$ACCELERATOR_TYPE,accelerator-count=1,replica-count=1,container-image-uri=$IMAGE_URI \
--service-account=$SERVICE_ACCOUNT \
--args=\
--training-file=gs://$BUCKET/data/train.csv,\
--validation-file=gs://$BUCKET/data/eval.csv,\
--testing-file=gs://$BUCKET/data/test.csv,\
--job-dir=gs://$BUCKET/model/model.pt,\
--epochs=10,\
--batch-size=128,\
--learning-rate=0.0001
```

## 在 Vertex AI 上运行任务

启动脚本并进入你的 GCP 项目，在 Vertex 菜单下的“训练”部分。

![](../Images/d622336b28fc8d7f5f60fbb0431498ca.png)

（图片来自作者）

启动脚本并进入控制台。你应该会看到任务状态为“等待中”，然后是“训练中”。

为了确保正在使用 GPU，你可以检查任务及其资源：

![](../Images/4f18577283659330ba871d2d88baf21b.png)

（图片来自作者）

这表明我们正在使用 GPU 进行训练，因此现在应该能够显著加速！让我们来看一下日志：

![](../Images/959225237e7b4fa03ac158ba31a68e23.png)

运行 1 个周期不到 10 分钟，而在 CPU 上需要 1 小时/周期！我们已经将训练任务转移到 Vertex，并加速了训练过程。我们可以选择启动其他配置不同的任务，而不会超载我们笔记本的能力。

那么模型的最终准确率如何呢？在经过 10 个周期后，它大约为 94% 到 95%。我们可以让它继续运行更长时间，看看分数是否提高（我们还可以添加早停回调来避免过拟合）

![](../Images/40b37ce0f0bb8c48db7b04e1152a1ef2.png)

## 我们的模型表现如何？

![](../Images/b3808a8189226c3c73eacc46470f071c.png)

（图片来自作者）

到派对时间了！
