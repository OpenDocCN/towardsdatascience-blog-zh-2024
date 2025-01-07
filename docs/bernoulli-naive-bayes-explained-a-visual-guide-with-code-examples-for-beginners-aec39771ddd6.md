# 伯努利朴素贝叶斯，详解：适合初学者的可视化指南及代码示例

> 原文：[https://towardsdatascience.com/bernoulli-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-aec39771ddd6?source=collection_archive---------2-----------------------#2024-08-24](https://towardsdatascience.com/bernoulli-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-aec39771ddd6?source=collection_archive---------2-----------------------#2024-08-24)

## 分类算法

## 通过是/否概率解锁预测能力

[](https://medium.com/@samybaladram?source=post_page---byline--aec39771ddd6--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--aec39771ddd6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aec39771ddd6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aec39771ddd6--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--aec39771ddd6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aec39771ddd6--------------------------------) ·阅读时间9分钟·2024年8月24日

--

![](../Images/03ca097360665649a9247ebe64f4ed22.png)

`⛳️ 更多分类算法，详解： · [虚拟分类器](/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e) · [K近邻分类器](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1) ▶ [伯努利朴素贝叶斯](/bernoulli-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-aec39771ddd6) · [高斯朴素贝叶斯](/gaussian-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-04949cef383c) · [决策树分类器](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e) · [逻辑回归](/logistic-regression-explained-a-visual-guide-with-code-examples-for-beginners-81baf5871505) · [支持向量分类器](/support-vector-classifier-explained-a-visual-guide-with-mini-2d-dataset-62e831e7b9e9) · [多层感知机](/multilayer-perceptron-explained-a-visual-guide-with-mini-2d-dataset-0ae8100c5d1c)`

与[虚拟分类器](/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e)或[KNN基于相似度推理](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1)的基线方法不同，朴素贝叶斯利用概率理论。它将每个“线索”（或特征）的个体概率结合起来，做出最终预测。这种直接而强大的方法在许多机器学习应用中被证明是无价的。

![](../Images/30ab2ece7ab19eccfe1d56b891244792.png)

所有视觉内容：作者使用Canva Pro创建。已优化移动设备显示；在桌面上可能显示过大。

# 定义

朴素贝叶斯是一种使用概率对数据进行分类的机器学习算法。它基于[贝叶斯定理](https://en.wikipedia.org/wiki/Bayes%27_theorem)，这是一个计算条件概率的公式。 “朴素”部分指的是它的一个关键假设：它假设所有特征彼此独立，即使它们在现实中可能不是。尽管这种简化通常不现实，但它大大减少了计算复杂性，并且在许多实际场景中表现良好。

![](../Images/f7d3cf3a614af1aa7768d0a939f589b7.png)

朴素贝叶斯方法是机器学习中的一种简单算法，基于概率作为其基础。

# 朴素贝叶斯分类器的主要类型

朴素贝叶斯分类器有三种主要类型。这些类型之间的主要区别在于它们对特征分布的假设：

1.  **伯努利朴素贝叶斯**：适用于二进制/布尔特征。它假设每个特征是二进制值（0/1）的变量。

1.  **多项式朴素贝叶斯**：通常用于离散计数。它常用于文本分类，其中特征可能是单词计数。

1.  **高斯朴素贝叶斯**：假设连续特征服从正态分布。

![](../Images/649ef29e85be0e8574c9d60724d5f436.png)

伯努利朴素贝叶斯假设二进制数据，多项式朴素贝叶斯处理离散计数，高斯朴素贝叶斯处理假设正态分布的连续数据。

重点是从最简单的朴素贝叶斯开始，这就是伯努利朴素贝叶斯。其名称中的“伯努利”来自于假设每个特征都是二进制值的假设。

# 使用的数据集

在本文中，我们将使用这个人工高尔夫数据集（灵感来源于[1]）作为示例。该数据集预测一个人是否会根据天气条件打高尔夫。

![](../Images/26a4306804458312bd32df0299f49b7b.png)

列：‘Outlook’，‘Temperature’（华氏度），‘Humidity’（百分比），‘Wind’和‘Play’（目标特征）

```py
# IMPORTING DATASET #
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
import pandas as pd
import numpy as np

dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}
df = pd.DataFrame(dataset_dict)

# ONE-HOT ENCODE 'Outlook' COLUMN
df = pd.get_dummies(df, columns=['Outlook'],  prefix='', prefix_sep='', dtype=int)

# CONVERT 'Windy' (bool) and 'Play' (binary) COLUMNS TO BINARY INDICATORS
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Set feature matrix X and target vector y
X, y = df.drop(columns='Play'), df['Play']

# Split the data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

print(pd.concat([X_train, y_train], axis=1), end='\n\n')
print(pd.concat([X_test, y_test], axis=1))
```

我们将稍作调整，使用伯努利朴素贝叶斯，通过将特征转换为二进制格式。

![](../Images/61eb5e8d4ce645769beb07e6f3802a4c.png)

由于所有数据必须是0和1格式，因此‘Outlook’被进行独热编码，而Temperature被分为≤80和>80。同样，Humidity被分为≤75和>75。

```py
# One-hot encode the categorized columns and drop them after, but do it separately for training and test sets
# Define categories for 'Temperature' and 'Humidity' for training set
X_train['Temperature'] = pd.cut(X_train['Temperature'], bins=[0, 80, 100], labels=['Warm', 'Hot'])
X_train['Humidity'] = pd.cut(X_train['Humidity'], bins=[0, 75, 100], labels=['Dry', 'Humid'])

# Similarly, define for the test set
X_test['Temperature'] = pd.cut(X_test['Temperature'], bins=[0, 80, 100], labels=['Warm', 'Hot'])
X_test['Humidity'] = pd.cut(X_test['Humidity'], bins=[0, 75, 100], labels=['Dry', 'Humid'])

# One-hot encode the categorized columns
one_hot_columns_train = pd.get_dummies(X_train[['Temperature', 'Humidity']], drop_first=True, dtype=int)
one_hot_columns_test = pd.get_dummies(X_test[['Temperature', 'Humidity']], drop_first=True, dtype=int)

# Drop the categorized columns from training and test sets
X_train = X_train.drop(['Temperature', 'Humidity'], axis=1)
X_test = X_test.drop(['Temperature', 'Humidity'], axis=1)

# Concatenate the one-hot encoded columns with the original DataFrames
X_train = pd.concat([one_hot_columns_train, X_train], axis=1)
X_test = pd.concat([one_hot_columns_test, X_test], axis=1)

print(pd.concat([X_train, y_train], axis=1), '\n')
print(pd.concat([X_test, y_test], axis=1))
```

# 主要机制

伯努利朴素贝叶斯算法适用于每个特征值为 0 或 1 的数据。

1.  计算训练数据中每个类别的概率。

1.  对于每个特征和类别，计算在给定类别下特征为 1 和 0 的概率。

1.  对于一个新实例：对于每个类别，将其概率与该类别下每个特征值（0 或 1）的概率相乘。

1.  预测具有最高结果概率的类别。

![](../Images/84714052b549a7af7c0d539e9ad80a40.png)

对于我们的高尔夫数据集，伯努利 NB 分类器查看每个特征在每个类别（YES 和 NO）下发生的概率，然后根据哪个类别的概率更高来做出决策。

# 训练步骤

伯努利朴素贝叶斯的训练过程包括从训练数据中计算概率：

1.  **类别概率计算**：对于每个类别，计算其概率：（该类别中实例的数量）/（所有实例的总数）。

![](../Images/9b5948e82009cdf2e32a27b5175ef059.png)

在我们的高尔夫示例中，算法将计算总体上打高尔夫的频率。

```py
from fractions import Fraction

def calc_target_prob(attr):
    total_counts = attr.value_counts().sum()
    prob_series = attr.value_counts().apply(lambda x: Fraction(x, total_counts).limit_denominator())
    return prob_series

print(calc_target_prob(y_train))
```

2. **特征概率计算**：对于每个特征和每个类别，计算：

+   （特征为 0 的实例数量）/（该类别中实例的数量）

+   （特征为 1 的实例数量）/（该类别中实例的数量）

![](../Images/2b11338b2b4b82915b66915a59a02c86.png)

对于每种天气条件（例如，晴天），计算在晴天时打高尔夫的频率，以及晴天时不打高尔夫的频率。

```py
from fractions import Fraction

def sort_attr_label(attr, lbl):
    return (pd.concat([attr, lbl], axis=1)
            .sort_values([attr.name, lbl.name])
            .reset_index()
            .rename(columns={'index': 'ID'})
            .set_index('ID'))

def calc_feature_prob(attr, lbl):
    total_classes = lbl.value_counts()
    counts = pd.crosstab(attr, lbl)
    prob_df = counts.apply(lambda x: [Fraction(c, total_classes[x.name]).limit_denominator() for c in x])

    return prob_df

print(sort_attr_label(y_train, X_train['sunny']))
print(calc_feature_prob(X_train['sunny'], y_train))
```

![](../Images/3f877762f5383987bfcc7aebba944cee.png)

同样的过程应用于所有其他特征。

```py
for col in X_train.columns:
  print(calc_feature_prob(X_train[col], y_train), "\n")
```

3. **平滑（可选）**：在每次概率计算时，向分子和分母中添加一个小值（通常为 1），以避免出现零概率。

![](../Images/12828bae35f3666b1a11714d940b86dd.png)

我们对所有分子加 1，对所有分母加 2，以保持总类别概率为 1。

```py
# In sklearn, all processes above is summarized in this 'fit' method:
from sklearn.naive_bayes import BernoulliNB
nb_clf = BernoulliNB(alpha=1)
nb_clf.fit(X_train, y_train)
```

4. **存储结果**：保存所有计算出的概率，以便在分类过程中使用。

![](../Images/4f252c018473e393221152c14c8237de.png)

平滑已经应用到所有特征概率中。我们将使用这些表格进行预测。

# 分类步骤

给定一个新实例，其特征值为 0 或 1：

1.  **概率收集**：对于每个可能的类别：

+   从该类别发生的概率（类别概率）开始。

+   对于新实例中的每个特征，收集该特征在此类别下为 0/1 的概率。

![](../Images/e75f17e0d471205439b795c7edd459e1.png)

对于 ID 14，我们选择每个特征（无论是 0 还是 1）发生的概率。

2. **得分计算与预测**：对于每个类别：

+   将所有收集到的概率相乘。

+   结果是该类别的得分。

+   得分最高的类别就是预测结果。

![](../Images/27aa29b9b8151107a827f3c9a27df791.png)

在将类别概率与所有特征概率相乘后，选择得分更高的类别。

```py
y_pred = nb_clf.predict(X_test)
print(y_pred)
```

# 评估步骤

![](../Images/3902cfe7e903f14c43f4a61c2785b3f9.png)

这个简单的概率模型在这个简单数据集上表现出了很好的准确性。

```py
# Evaluate the classifier
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
```

# 关键参数

伯努利朴素贝叶斯有一些重要参数：

1.  **Alpha (α)**：这是平滑参数。它为每个特征添加一个小的计数，以防止零概率。默认通常为1.0（拉普拉斯平滑），如前所示。

1.  **Binarize**：如果您的特征尚未是二元的，此阈值会将其转换。任何高于此阈值的值变为1，低于该阈值的值变为0。

![](../Images/ec7dd643acab9b9101b133d2d24b6fb2.png)

对于scikit-learn中的BernoulliNB，数值特征通常是标准化的，而不是手动二值化的。模型会将这些标准化的值转化为二进制，通常使用0（均值）作为阈值。

3. **Fit Prior**：是否学习类别的先验概率，或假设均匀先验（50/50）。

![](../Images/84092e5c0f5d406baed165d4f14aab41.png)

对于我们的高尔夫数据集，我们可能从默认的α=1.0开始，不进行二值化（因为我们的特征已经是二值的），并设置fit_prior=True。

# 优缺点

和机器学习中的任何算法一样，伯努利朴素贝叶斯也有其优点和局限性。

# 优点：

1.  **简单性**：容易实现和理解。

1.  **效率**：训练和预测速度快，适用于大规模特征空间。

1.  **小数据集的表现**：即使在训练数据有限的情况下也能表现良好。

1.  **处理高维数据**：在特征维度较多时表现良好，尤其适用于文本分类。

# 缺点：

1.  **独立性假设**：假设所有特征是独立的，但在实际数据中，这通常不成立。

1.  **仅限二元特征**：在其纯粹形式下，仅适用于二元数据。

1.  **对输入数据的敏感性**：对特征的二值化方式可能较为敏感。

1.  **零频问题**：如果没有平滑处理，零概率会强烈影响预测结果。

# 最后的备注

伯努利朴素贝叶斯分类器是一种简单而强大的二元分类机器学习算法。它在文本分析和垃圾邮件检测中表现优秀，这些场景中的特征通常是二元的。因其速度和效率著称，这一概率模型在小数据集和高维空间中表现出色。

尽管假设特征之间相互独立，但它的准确性常常能与更复杂的模型相媲美。伯努利朴素贝叶斯作为一个优秀的基准模型和实时分类工具。

# 🌟 伯努利朴素贝叶斯简化版

```py
# Import needed libraries
import pandas as pd
from sklearn.naive_bayes import BernoulliNB
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import accuracy_score
from sklearn.model_selection import train_test_split

# Load the dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rainy', 'rainy', 'rainy', 'overcast', 'sunny', 'sunny', 'rainy', 'sunny', 'overcast', 'overcast', 'rainy', 'sunny', 'overcast', 'rainy', 'sunny', 'sunny', 'rainy', 'overcast', 'rainy', 'sunny', 'overcast', 'sunny', 'overcast', 'rainy', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}
df = pd.DataFrame(dataset_dict)

# Prepare data for model
df = pd.get_dummies(df, columns=['Outlook'],  prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Split data into training and testing sets
X, y = df.drop(columns='Play'), df['Play']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Scale numerical features (for automatic binarization)
scaler = StandardScaler()
float_cols = X_train.select_dtypes(include=['float64']).columns
X_train[float_cols] = scaler.fit_transform(X_train[float_cols])
X_test[float_cols] = scaler.transform(X_test[float_cols])

# Train the model
nb_clf = BernoulliNB()
nb_clf.fit(X_train, y_train)

# Make predictions
y_pred = nb_clf.predict(X_test)

# Check accuracy
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
```

## 进一步阅读

对于[伯努利朴素贝叶斯](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.BernoulliNB.html)分类器及其在scikit-learn中的实现，读者可以参考官方文档，文档中提供了关于其使用和参数的详细信息。

## 技术环境

本文使用 Python 3.7 和 scikit-learn 1.5。虽然讨论的概念通常适用，但具体的代码实现可能会因版本不同而略有差异。

## 关于插图

除非另有说明，所有图片均由作者创作，并结合了来自 Canva Pro 的授权设计元素。

![](../Images/3eeb239271faf81969b84660399a1fb4.png)

要查看 Bernoulli Naive Bayes 的简洁视觉总结，请查看[Instagram上的配套帖子](https://www.instagram.com/p/C_CUwtAyVI3/)。

## 参考文献

[1] T. M. Mitchell, [机器学习](https://www.cs.cmu.edu/afs/cs.cmu.edu/user/mitchell/ftp/mlbook.html) (1997), McGraw-Hill Science/Engineering/Math, 第59页

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝘾𝙡𝙖𝙨𝙨𝙞𝙛𝙞𝙘𝙖𝙩𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----aec39771ddd6--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----aec39771ddd6--------------------------------)8篇故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----aec39771ddd6--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----aec39771ddd6--------------------------------)5篇故事![一个带着双马尾和粉色帽子的卡通娃娃。这个“假人”娃娃，凭借其简单的设计和心形装饰的衣服，直观地展示了机器学习中“假回归器”的概念。就像这个玩具人物是一个简化的、静态的人物表示一样，假回归器是作为基线的基本模型，用于更复杂的分析。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----aec39771ddd6--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----aec39771ddd6--------------------------------)4篇故事![](../Images/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](../Images/22a5d43568e70222eb89fd36789a9333.png)![](../Images/8ea1a2f29053080a5feffc709f5b8669.png)
