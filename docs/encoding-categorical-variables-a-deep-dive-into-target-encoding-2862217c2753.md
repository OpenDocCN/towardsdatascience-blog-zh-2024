# 编码分类变量：深入探讨目标编码

> 原文：[https://towardsdatascience.com/encoding-categorical-variables-a-deep-dive-into-target-encoding-2862217c2753?source=collection_archive---------2-----------------------#2024-02-05](https://towardsdatascience.com/encoding-categorical-variables-a-deep-dive-into-target-encoding-2862217c2753?source=collection_archive---------2-----------------------#2024-02-05)

## 数据有不同的形态和形式，其中一种形式被称为分类数据。

[](https://medium.com/@juanjosemunozp?source=post_page---byline--2862217c2753--------------------------------)[![Juan Jose Munoz](../Images/b42d72e9e2a2eaf11da5465e9b041d53.png)](https://medium.com/@juanjosemunozp?source=post_page---byline--2862217c2753--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2862217c2753--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2862217c2753--------------------------------) [Juan Jose Munoz](https://medium.com/@juanjosemunozp?source=post_page---byline--2862217c2753--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2862217c2753--------------------------------) ·10分钟阅读·2024年2月5日

--

**这带来了一个问题，因为大多数机器学习算法仅使用数值数据作为输入**。然而，由于有一些简单且定义明确的函数将分类数据转换为数值，因此处理分类数据通常并不困难。如果你参加过任何数据科学课程，你一定会熟悉一热编码策略用于处理分类特征。当你的特征类别有限时，这个策略效果很好。然而，在处理高基数特征（类别众多的特征）时，你将会遇到一些问题。

**下面是如何使用目标编码将分类特征转换为数值的方法。**

![](../Images/f5faebf9812d3395ba1b4d55a6ddf0f3.png)

图片由[Sonika Agarwal](https://unsplash.com/@sonika_agarwal?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 一热编码的问题

**在任何数据科学课程的早期，你都会接触到一热编码，它作为处理分类值的关键策略而被广泛使用**，这也很有道理，因为该策略在低基数特征（类别有限的特征）上效果非常好。

**简而言之，一热编码将每个类别转换为一个二进制向量，** 其中相应的类别标记为‘True’或‘1’，其他所有类别标记为‘False’或‘0’。

```py
import pandas as pd

# Sample categorical data
data = {'Category': ['Red', 'Green', 'Blue', 'Red', 'Green']}

# Create a DataFrame
df = pd.DataFrame(data)

# Perform one-hot encoding
one_hot_encoded = pd.get_dummies(df['Category'])

# Display the result
print(one_hot_encoded)
```

![](../Images/ba9c89d63e6cba7e5db81567deaf9b35.png)

一热编码输出——我们可以通过去掉一列来改进这一点，因为如果我们知道了蓝色和绿色，就能推断出红色的值。图片由作者提供

虽然这种方法对于具有有限类别的特征*(少于 10 到 20 个类别)*效果很好，但随着类别数量的增加，独热编码的向量会变得更长、更稀疏，这可能导致内存使用增加和计算复杂度上升，我们来看一个例子。

*下面的代码使用了亚马逊员工访问数据，该数据在 Kaggle 上公开可用：* [*https://www.kaggle.com/datasets/lucamassaron/amazon-employee-access-challenge*](https://www.kaggle.com/datasets/lucamassaron/amazon-employee-access-challenge)

数据包含八个类别特征列，表示员工在亚马逊所需资源、角色和工作组的特征。

```py
data.info()
```

![](../Images/5e6c072ac9109edba362fc86ca6496ee.png)

列信息。图像来源：作者

```py
# Display the number of unique values in each column
unique_values_per_column = data.nunique()

print("Number of unique values in each column:")
print(unique_values_per_column)
```

![](../Images/3272325aaa9c9f09c59e5aa935a95c95.png)

这八个特征具有高基数。图像来源：作者

**在像这样的数据集中使用独热编码可能会带来挑战，因为每个特征的不同类别数量非常高。**

```py
#Initial data memory usage
memory_usage = data.memory_usage(deep=True)
total_memory_usage = memory_usage.sum()
print(f"\nTotal memory usage of the DataFrame: {total_memory_usage / (1024 ** 2):.2f} MB")
```

![](../Images/c46013a222c086c67eca8bdf08167d6a.png)

初始数据集大小为 11.24 MB。图像来源：作者

```py
#one-hot encoding categorical features
data_encoded = pd.get_dummies(data, 
                              columns=data.select_dtypes(include='object').columns,
                              drop_first=True)

data_encoded.shape
```

![](../Images/c18fd40a3d00ecb79244ef3e78d51923.png)

独热编码后，数据集有 15,618 列。图像来源：作者

![](../Images/8d92598376535d840d3f52d59a26e239.png)

结果数据集非常稀疏，这意味着它包含了大量的 0 和 1。图像来源：作者

```py
# Memory usage for the one-hot encoded dataset
memory_usage = data_encoded.memory_usage(deep=True)
total_memory_usage = memory_usage.sum()
print(f"\nTotal memory usage of the DataFrame: {total_memory_usage / (1024 ** 2):.2f} MB")
```

![](../Images/ca2ecf4f83515a06eae3394bcd8aa012.png)

由于列数增加，数据集的内存使用量增加到 488.08 MB。图像来源：作者

如你所见，独热编码并不是处理高基数类别特征的可行解决方案，因为它显著增加了数据集的大小。

**在具有高基数特征的情况下，目标编码是更好的选择。**

# 目标编码 — 基本原理概述

目标编码将一个类别特征转换为一个数值特征，而不会添加任何额外的列，避免将数据集转化为更大且稀疏的数据集。

**目标编码通过将每个类别特征转换为其相应的期望值来工作。** 计算期望值的方法将取决于你尝试预测的值。

> 对于回归问题，期望值仅仅是该类别的平均值。
> 
> 对于分类问题，期望值是给定类别下的条件概率。

在这两种情况下，我们只需使用 pandas 中的 `group_by` 函数即可得到结果。

```py
#Example of how to calculate the expected value for Target encoding of a Binary outcome
expected_values = data.groupby('ROLE_TITLE')['ACTION'].value_counts(normalize=True).unstack()
expected_values
```

![](../Images/492473834fd253f24f7d6a3fe7c7dcb6.png)

结果表格显示了每个 `ACTION` 结果按唯一 `Role_title` ID 的概率。图像来源：作者

结果表格显示了每个 “*ACTION*” 结果按唯一 “*ROLE_TITLE*” ID 的概率。剩下的工作是将 “*ROLE_TITLE*” ID 替换为原始数据集中“*ACTION*”为 1 的概率值。*(即，代替类别 117879，数据集将显示 0.889331)*

**虽然这能帮助我们直观理解目标编码的工作原理，但使用这个简单的方法存在过拟合的风险**。尤其是对于稀有类别，因为在这种情况下，目标编码基本上会将目标值直接传递给模型。此外，上述方法只能处理已见过的类别，因此如果你的测试数据中有新类别，它将无法处理。

**为了避免这些错误，你需要使目标编码转换器更加稳健。**

# 定义目标编码类

为了使目标编码更加稳健，你可以创建一个自定义的转换器类，并将其与 scikit-learn 集成，这样就可以在任何模型管道中使用。

*注意：以下代码取自《Kaggle书籍》，可以在 Kaggle 中找到：* [*https://www.kaggle.com/code/lucamassaron/meta-features-and-target-encoding*](https://www.kaggle.com/code/lucamassaron/meta-features-and-target-encoding)

```py
import numpy as np
import pandas as pd

from sklearn.base import BaseEstimator, TransformerMixin

class TargetEncode(BaseEstimator, TransformerMixin):

    def __init__(self, categories='auto', k=1, f=1, 
                 noise_level=0, random_state=None):
        if type(categories)==str and categories!='auto':
            self.categories = [categories]
        else:
            self.categories = categories
        self.k = k
        self.f = f
        self.noise_level = noise_level
        self.encodings = dict()
        self.prior = None
        self.random_state = random_state

    def add_noise(self, series, noise_level):
        return series * (1 + noise_level *   
                         np.random.randn(len(series)))

    def fit(self, X, y=None):
        if type(self.categories)=='auto':
            self.categories = np.where(X.dtypes == type(object()))[0]

        temp = X.loc[:, self.categories].copy()
        temp['target'] = y
        self.prior = np.mean(y)
        for variable in self.categories:
            avg = (temp.groupby(by=variable)['target']
                       .agg(['mean', 'count']))
            # Compute smoothing 
            smoothing = (1 / (1 + np.exp(-(avg['count'] - self.k) /                 
                         self.f)))
            # The bigger the count the less full_avg is accounted
            self.encodings[variable] = dict(self.prior * (1 -  
                             smoothing) + avg['mean'] * smoothing)

        return self

    def transform(self, X):
        Xt = X.copy()
        for variable in self.categories:
            Xt[variable].replace(self.encodings[variable], 
                                 inplace=True)
            unknown_value = {value:self.prior for value in 
                             X[variable].unique() 
                             if value not in 
                             self.encodings[variable].keys()}
            if len(unknown_value) > 0:
                Xt[variable].replace(unknown_value, inplace=True)
            Xt[variable] = Xt[variable].astype(float)
            if self.noise_level > 0:
                if self.random_state is not None:
                    np.random.seed(self.random_state)
                Xt[variable] = self.add_noise(Xt[variable], 
                                              self.noise_level)
        return Xt

    def fit_transform(self, X, y=None):
        self.fit(X, y)
        return self.transform(X)
```

刚开始可能看起来有些令人生畏，但我们可以逐步解析每一部分代码，以理解如何创建一个强大的目标编码器。

## 类定义

```py
class TargetEncode(BaseEstimator, TransformerMixin):
```

这第一步确保你可以在 scikit-learn 管道中使用这个转换器类进行数据预处理、特征工程和机器学习工作流。它通过继承 scikit-learn 类 *BaseEstimator* 和 *TransformerMixin* 来实现这一点。

继承使得 *TargetEncode* 类可以重用或覆盖基类中定义的方法和属性，在此情况下是 *BaseEstimator* 和 *TransformerMixin*。

*BaseEstimator* 是所有 scikit-learn 估计器的基类。估计器是 scikit-learn 中的对象，具有用于训练数据的“fit”方法和用于做出预测的“predict”方法。

*TransformerMixin* 是 scikit-learn 中用于转换器的混入类，它提供了额外的方法，如“fit_transform”，该方法将拟合和转换合并为一个步骤。

**继承自 *BaseEstimator* 和 *TransformerMixin*，使得 TargetEncode 可以实现这些方法，从而与 scikit-learn API 兼容。**

## 定义构造函数

```py
def __init__(self, categories='auto', k=1, f=1, 
                 noise_level=0, random_state=None):
        if type(categories)==str and categories!='auto':
            self.categories = [categories]
        else:
            self.categories = categories
        self.k = k
        self.f = f
        self.noise_level = noise_level
        self.encodings = dict()
        self.prior = None
        self.random_state = random_state
```

第二步定义了 *“TargetEncode*” 类的构造函数，并用默认值或用户指定的值初始化实例变量。

“*categories*” 参数决定了输入数据中哪些列应该被视为目标编码的分类变量。默认为 'auto'，在拟合过程中自动识别分类列。

参数 k、f 和 noise_level 控制目标编码过程中的平滑效果以及在转换过程中添加的噪声量。

## 添加噪声

**下一步非常重要，以避免过拟合**。

```py
def add_noise(self, series, noise_level):
        return series * (1 + noise_level *   
                         np.random.randn(len(series)))
```

“*add_noise*”方法向数据中添加随机噪声，以引入变异性并在转换阶段防止过拟合。

*“np.random.randn(len(series))”* 从标准正态分布（均值为 0，标准差为 1）生成一个随机数数组。

将这个数组乘以“*noise_level*” s 以根据指定的噪声级别调整随机噪声的规模。

**这一步骤有助于增强目标编码过程的鲁棒性和泛化能力。**

## 拟合目标编码器

这部分代码通过计算类别列的目标编码并将其存储起来，来训练目标编码器，以便在转换时使用。

```py
def fit(self, X, y=None):
        if type(self.categories)=='auto':
            self.categories = np.where(X.dtypes == type(object()))[0]

        temp = X.loc[:, self.categories].copy()
        temp['target'] = y
        self.prior = np.mean(y)
        for variable in self.categories:
            avg = (temp.groupby(by=variable)['target']
                       .agg(['mean', 'count']))
            # Compute smoothing 
            smoothing = (1 / (1 + np.exp(-(avg['count'] - self.k) /                 
                         self.f)))
            # The bigger the count the less full_avg is accounted
            self.encodings[variable] = dict(self.prior * (1 -  
                             smoothing) + avg['mean'] * smoothing)
```

平滑项有助于防止过拟合，尤其是在处理样本量小的类别时。

**该方法遵循 scikit-learn 中转换器拟合方法的约定。**

它首先检查并识别类别列，然后创建一个临时的 DataFrame，包含来自输入 X 的选择的类别列和目标变量 y。

目标变量的先验均值被计算并存储在 prior 属性中。**这代表了整个数据集上目标变量的总体均值。**

**然后，它使用 group-by 方法计算每个类别的目标变量的均值和计数，正如前面所见。**

还增加了一个平滑步骤，以防止在样本量小的类别上过拟合。平滑是基于每个类别中的样本数量计算的。样本量越大，平滑效应越小。

当前变量中每个类别的计算编码被存储在编码字典中。这个字典将在转换阶段后续使用。

## 转换数据

这部分代码将原始的类别值替换为存储在*self.encodings*中的相应目标编码值。

```py
def transform(self, X):
        Xt = X.copy()
        for variable in self.categories:
            Xt[variable].replace(self.encodings[variable], 
                                 inplace=True)
            unknown_value = {value:self.prior for value in 
                             X[variable].unique() 
                             if value not in 
                             self.encodings[variable].keys()}
            if len(unknown_value) > 0:
                Xt[variable].replace(unknown_value, inplace=True)
            Xt[variable] = Xt[variable].astype(float)
            if self.noise_level > 0:
                if self.random_state is not None:
                    np.random.seed(self.random_state)
                Xt[variable] = self.add_noise(Xt[variable], 
                                              self.noise_level)
        return Xt
```

这一步骤增加了一个鲁棒性检查，以确保目标编码器能够处理新的或未见过的类别。**对于这些新的或未知的类别，它将用目标变量的均值替代**，该均值存储在 prior_mean 变量中。

如果你需要更强的抗过拟合能力，可以设置一个大于 0 的*noise_level*，向编码值中添加随机噪声。

*fit_transform* 方法结合了拟合和转换数据的功能，首先将转换器拟合到训练数据，然后基于计算出的编码进行转换。

现在你已经理解了代码的工作原理，让我们看看它的实际应用。

```py
#Instantiate TargetEncode class
te = TargetEncode(categories='ROLE_TITLE')
te.fit(data, data['ACTION'])
te.transform(data[['ROLE_TITLE']])
```

![](../Images/22681eff29fda72f848991a453fed087.png)

输出带有目标编码角色标题的结果。图像由作者提供

目标编码器用每个类别的概率替换了每个“*ROLE_TITLE*” ID。现在，让我们对所有特征做相同的操作，并检查在使用目标编码后内存的使用情况。

```py
y = data['ACTION']
features = data.drop('ACTION',axis=1)

te = TargetEncode(categories=features.columns)
te.fit(features,y)
te_data = te.transform(features)

te_data.head()
```

![](../Images/16cffc6f388731f36b184f0376183964.png)

输出目标编码特征。图像由作者提供

```py
memory_usage = te_data.memory_usage(deep=True)
total_memory_usage = memory_usage.sum()
print(f"\nTotal memory usage of the DataFrame: {total_memory_usage / (1024 ** 2):.2f} MB")
```

![](../Images/9d24943bd0681c1ba70420d2f47e6bdd.png)

结果数据集仅使用了 2.25 MB，而独热编码器则使用了 488.08 MB。图像由作者提供

目标编码成功地将分类数据转换为数值数据，而没有创建额外的列或增加内存使用。

# 使用 SciKitLearn API 进行目标编码

**到目前为止，我们已经创建了自己的目标编码器类，但你不再需要这样做。**

在 scikit-learn 1.3 版本发布中，大约在 2023 年 6 月，他们将 Target Encoder 类引入了他们的 API。这是如何使用目标编码与 Scikit Learn 的方法。

```py
from sklearn.preprocessing import TargetEncoder

#Splitting the data
y = data['ACTION']
features = data.drop('ACTION',axis=1)

#Specify the target type
te = TargetEncoder(smooth="auto",target_type='binary')
X_trans = te.fit_transform(features, y)

#Creating a Dataframe
features_encoded = pd.DataFrame(X_trans, columns = features.columns) 
```

![](../Images/1aaf7df1af07f04088bc8380e62d514f.png)

sklearn 目标编码器转换的输出。图片由作者提供

请注意，由于平滑参数和噪声水平的随机性，我们从手动目标编码器类中得到的结果略有不同。

如你所见，sklearn 使得进行目标编码转换变得简单。**然而，首先了解该转换的内部原理，以便理解和解释输出，是非常重要的。**

虽然目标编码是一种强大的编码方法，但重要的是要考虑数据集的特定要求和特点，并选择最适合您的需求以及您计划使用的机器学习算法要求的编码方法。

# 参考文献

[1] Banachewicz, K. & Massaron, L. (2022). *《Kaggle 书：用于竞争数据科学的数据分析与机器学习》*。Packt>

[2] Massaron, L. (2022年1月)。Amazon 员工访问挑战。于 2024 年 2 月 1 日检索自 [https://www.kaggle.com/datasets/lucamassaron/amazon-employee-access-challenge](https://www.kaggle.com/datasets/lucamassaron/amazon-employee-access-challenge)

[3] Massaron, L. 元特征和目标编码。于 2024 年 2 月 1 日检索自 [https://www.kaggle.com/luca-massaron/meta-features-and-target-encoding](https://www.kaggle.com/luca-massaron/meta-features-and-target-encoding)

[4] Scikit-learn.`sklearn.preprocessing.TargetEncoder`。在 scikit-learn：Python 中的机器学习（版本 1.3）。于 2024 年 2 月 1 日检索自 [https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.TargetEncoder.html](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.TargetEncoder.html)
