# 机器学习的3种关键编码技术：适合初学者的指南，包含优缺点和Python代码示例

> 原文：[https://towardsdatascience.com/3-key-encoding-techniques-for-machine-learning-a-beginner-friendly-guide-aff8a01a7b6a?source=collection_archive---------1-----------------------#2024-02-07](https://towardsdatascience.com/3-key-encoding-techniques-for-machine-learning-a-beginner-friendly-guide-aff8a01a7b6a?source=collection_archive---------1-----------------------#2024-02-07)

## 我们应该如何在标签编码、独热编码和目标编码之间做选择呢？

[](https://medium.com/@ryuryu09030903?source=post_page---byline--aff8a01a7b6a--------------------------------)[![Ryu Sonoda](../Images/52445252872ed381dd86d3ada5665e1b.png)](https://medium.com/@ryuryu09030903?source=post_page---byline--aff8a01a7b6a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aff8a01a7b6a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aff8a01a7b6a--------------------------------) [Ryu Sonoda](https://medium.com/@ryuryu09030903?source=post_page---byline--aff8a01a7b6a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aff8a01a7b6a--------------------------------) ·15分钟阅读·2024年2月7日

--

**为什么我们需要编码？**

在机器学习领域，大多数算法要求输入为数字形式，特别是在许多流行的Python框架中。例如，在scikit-learn中，线性回归和神经网络要求数字变量。这意味着我们需要将分类变量转换为数值变量，以便这些模型能够理解它们。然而，对于像基于树的模型，通常不需要这一过程。

今天，我很高兴为大家介绍三种基本的编码技术，它们是每个初学数据科学家的必备技能！另外，我在最后还附上了一个实用的小贴士，帮助你更好地理解这些技术的实际应用！除非特别说明，所有代码和图片均由作者创作。

**标签编码 / 顺序编码**

标签编码和顺序编码都涉及将整数分配给不同的类别。区别在于类别变量是否本身具有顺序。例如，“强烈同意”，“同意”，“中立”，“不同意”和“强烈不同意”是有序的，因为它们遵循特定的顺序。当一个变量没有这样的顺序时，我们使用标签编码。

让我们深入探讨**标签编码**。

我已经准备了一个合成数据集，包含数学考试成绩和学生最喜欢的科目。这个数据集旨在反映偏爱STEM科目的学生获得更高的分数。以下代码展示了数据集的合成过程。

```py
import numpy as np
import pandas as pd

math_score = [60, 70, 80, 90]
favorite_subject = ["History", "English", "Science", "Math"]
std_deviation =  5  
num_samples = 30   

# Generate 30 samples with a normal distribution
scores = []
subjects = []
for i in range(4):
  scores.extend(np.random.normal(math_score[i], std_deviation, num_samples))
  subjects.extend([favorite_subject[i]]*num_samples)

data = {'Score': scores, 'Subject': subjects}
df_math = pd.DataFrame(data)

# Print the DataFrame
print(df_math.sample(frac=0.04))import numpy as np
import pandas as pd
import random

math_score = [60, 70, 80, 90]
favorite_subject = ["History", "English", "Science", "Math"]
std_deviation =  5  # Standard deviation in cm
num_samples = 30   # Number of samples

# Generate 30 samples with a normal distribution
scores = []
subjects = []
for i in range(4):
  scores.extend(np.random.normal(math_score[i], std_deviation, num_samples))
  subjects.extend([favorite_subject[i]]*num_samples)

data = {'Score': scores, 'Subject': subjects}
df_math = pd.DataFrame(data)

# Print the DataFrame
sampled_index = random.sample(range(len(df_math)), 5)
sampled = df_math.iloc[sampled_index]
print(sampled)
```

![](../Images/425df2f19fb2c7f82ab651ebec06fa83.png)

你会惊讶于数据编码是多么简单——只需一行代码！你可以传递一个字典，将主题名称和数字映射到 pandas 数据框的默认方法，如下所示。

```py
# Simple way
df_math['Subject_num'] = df_math['Subject'].replace({'History': 0, 'Science': 1, 'English': 2, 'Math': 3})
print(df_math.iloc[sampled_index])
```

![](../Images/669fe3aa0c74e4e450d749624c259f84.png)

手动编码

但是，如果你处理的是大量的类别，或者你希望找到一种更直接的方法怎么办？这时，**scikit-learn** 库中的`**LabelEncoder**`函数就派上了用场。它根据类别的字母顺序自动编码。为了获得最佳体验，我建议使用 1.4.0 版本，它支持我们正在讨论的所有编码器。

```py
# Scikit-learn
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
df_math["Subject_num_scikit"] = le.fit_transform(df_math[['Subject']])
print(df_math.iloc[sampled_index])
```

![](../Images/c834b34b76ab0d0baaf65601dac39920.png)

使用 scikit-learn 库进行编码

然而，有一个问题。考虑一下：我们的数据集并没有暗示喜欢的科目之间存在有序关系。例如，“历史”被编码为 0，但这并不意味着它比编码为 3 的“数学”更“低级”。类似地，“英语”和“科学”之间的数值差距小于“英语”和“历史”之间的差距，但这不一定反映它们之间的相对相似性。

这种编码方法也会影响某些算法的可解释性。例如，在线性回归中，每个系数表示预测变量变化 1 单位时，结果变量的预期变化。但是，当一个主题被数值编码时，我们该如何解释“单位变化”？让我们通过对我们的数据集进行线性回归来进行理解。

```py
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(df_math[["Subject_num"]], df_math[["Score"]])

coefficients = model.coef_

print("Coefficients:", coefficients)
```

![](../Images/283806185bd22927dfab305bad5004c1.png)

我们如何解释这里的系数 8.26 呢？最直观的方式是，当标签变化 1 单位时，测试分数变化 8。但这对于从科学（编码为 1）到历史（编码为 2）并不完全正确，因为我合成的方式使得平均分分别为 80 和 70。所以，当我们标记每个类别的方式没有实际意义时，我们不应解释该系数！

现在，转到**有序编码**，我们将其应用于另一个合成数据集，这次专注于身高和学校类别。我已经调整这个数据集，以反映不同学校级别的平均身高：幼儿园 110 厘米，小学 140 厘米，依此类推。让我们看看结果如何。

```py
import numpy as np
import pandas as pd

# Set the parameters
mean_height = [110, 140, 160, 175, 180]  # Mean height in cm
grade = ["kindergarten", "elementary school", "middle school", "high school", "college"]
std_deviation = 5  # Standard deviation in cm
num_samples = 10   # Number of samples

# Generate 10 samples with a normal distribution
heights = []
grades = []
for i in range(5):
  heights.extend(np.random.normal(mean_height[i], std_deviation, num_samples))
  grades.extend([grade[i]]*10)

data = {'Grade': grades, 'Height': heights}
df = pd.DataFrame(data)

sampled_index = random.sample(range(len(df)), 5)
sampled = df.iloc[sampled_index]
print(sampled)
```

![](../Images/d812fbef79a68c55496d48fc51d143cb.png)

一部分合成的学校身高数据

scikit-learn 的预处理工具包中的`**OrdinalEncoder**`是处理有序变量的一个真正的宝藏。它非常直观，自动确定有序结构并相应地进行编码。如果你查看 encoder.categories_，你可以检查变量是如何被编码的。

```py
from sklearn.preprocessing import OrdinalEncoder

encoder = OrdinalEncoder(categories=[grade])
df['Category'] = encoder.fit_transform(df[['Grade']])
print(encoder.categories_)
print(df.iloc[sampled_index])
```

![](../Images/6bfbcd0470e04147051ae7fb70ebdc82.png)

编码完成后

对于序数类别变量，解释线性回归模型变得更加简单。编码反映了教育程度的数值顺序——教育程度越高，其对应的数值越大。

```py
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(df[["Category"]], df[["Height"]])

coefficients = model.coef_

print("Coefficients:", coefficients)

height_diff = [mean_height[i] - mean_height[i-1] for i in range(1, len(mean_height),1)]
print("Average Height Difference:", sum(height_diff)/len(height_diff))
```

![](../Images/14d3d127f4382cfa23145eabdf63eb02.png)

模型揭示了一些直观的内容：学校类型的单位变化对应身高增加17.5厘米。考虑到我们的数据集，这完全合情合理！

那么，让我们用一个快速的总结来结束**标签/序数**编码：

优点：

- 简单性：用户友好，易于实现。

- 效率：这种方法对计算资源和内存的要求较低，仅生成一个新的数值特征。

- 适用于有序类别：在处理具有自然顺序的类别变量时，独热编码表现得尤为出色。

缺点：

- 隐含顺序：一个潜在的缺点是，它可能会引入一种本不存在的顺序，导致误解（比如认为标记为‘3’的类别优于标记为‘2’的类别）。

- 并非总是适用：某些算法，如线性回归或逻辑回归，可能会错误地将编码后的数值解释为具有序数意义。

**独热编码**

接下来，让我们深入探讨另一种解决可解释性问题的编码技术：**独热编码**。

标签编码的核心问题是，它为那些本身没有顺序的变量强加了一个序数结构，通过将类别替换为数值。**独热编码通过为每个类别创建一个单独的列来解决这个问题。每个列包含二进制值，表示该行是否属于该类别。** 这就像是将数据转换为更宽格式，对于熟悉这种概念的人来说。为了更清楚地说明这一点，我们来看一个使用`math_score`和`subject`数据的例子。`**OneHotEncoder**`来自sklearn.preprocessing，非常适合这个任务。

```py
from sklearn.preprocessing import OneHotEncoder

data = {'Score': scores, 'Subject': subjects}
df_math = pd.DataFrame(data)

y = df_math["Score"] # Target 
x = df_math.drop('Score', axis=1)

# Define encoder
encoder = OneHotEncoder()
x_ohe = encoder.fit_transform(x)
print("Type:",type(x_ohe))
# Convert x_ohe to array so that it is more compatible
x_ohe = x_ohe.toarray()
print("Dimension:", x_ohe.shape)

# Convet back to pandas dataframe
x_ohe = pd.DataFrame(x_ohe, columns=encoder.get_feature_names_out())
df_math_ohe = pd.concat([y, x_ohe], axis=1)
sampled_ohe_idx = random.sample(range(len(df_math_ohe)), 5)
print(df_math_ohe.iloc[sampled_ohe_idx])
```

![](../Images/ff50f995059ddc727f541bdec6365237.png)

通过独热编码进行编码

现在，我们的数据集不再只有一个‘学科’列，而是为每个学科单独设置了列。这有效地消除了任何不必要的序数结构！不过，这个过程稍微复杂一些，我来解释一下。

与标签/序数编码一样，你首先需要定义编码器。但独热编码的输出有所不同：标签/序数编码返回一个numpy数组，而独热编码通常生成一个`scipy.sparse._csr.csr_matrix`。为了将其与pandas数据框架结合使用，你需要将其转换为数组。然后，创建一个包含这个数组的新数据框，并为其指定列名，这些列名可以通过编码器的`get_feature_names_out()`方法获取。或者，你也可以通过在定义编码器时设置`sparse_output=False`来直接获得numpy数组。

然而，在实际应用中，你不需要经历所有这些步骤。我将在讨论的最后向你展示使用`**make_column_transformer**`的更简化方法！

现在，让我们继续对我们的独热编码数据进行线性回归。这应该使得解释变得更容易，对吧？

```py
model = LinearRegression()
model.fit(x_ohe, y)

coefficients = model.coef_
intercept = model.intercept_

print("Coefficients:", coefficients)
print(encoder.get_feature_names_out())
print("Intercept:",intercept)
```

![](../Images/a4ff2048605cc43f2b11c754ec38397a.png)

每列的截距和系数

等等，为什么系数这么小，截距又这么大？到底出了什么问题？这个困惑是线性回归中的一个特定问题，叫做完美多重共线性。完美多重共线性发生在当线性回归模型中的一个变量可以通过其他变量完美预测时，独热编码中也会出现这种情况，因为如果其他类别都为零，一个类别就可以被推断出来。为了避免这个问题，我们可以通过设置`OneHotEncoder(drop=”first”)`来去除一个类别。让我们看看这一调整的影响。

```py
encoder_with_drop = OneHotEncoder(drop="first")
x_ohe_drop = encoder_with_drop.fit_transform(x)

# if you don't sparse_output = False, you need to run the following to convert type
x_ohe_drop = x_ohe_drop.toarray()

x_ohe_drop = pd.DataFrame(x_ohe_drop, columns=encoder_with_drop.get_feature_names_out())

model = LinearRegression()
model.fit(x_ohe_drop, y)

coefficients = model.coef_
intercept = model.intercept_

print("Coefficients:", coefficients)
print(encoder_with_drop.get_feature_names_out())
print("Intercept:",intercept)
```

![](../Images/0900f84041b8739332f51a45eb66a95f.png)

每列的截距和系数（去除一列）

在这里，英语列已经被去除，现在系数看起来更加合理了！而且，它们也更容易解释。当所有的独热编码列都为零（表示英语是最喜欢的科目）时，我们预测考试成绩大约为71（与我们为英语定义的平均分数一致）。对于历史科目，成绩是71减去11等于60，对于数学，成绩是71加上19，以此类推。

然而，独热编码有一个显著的警告：它可能导致高维数据集，特别是当变量具有大量类别时。让我们考虑一个包含1000行数据集的情况，每一行代表一个具有各种特征的独特产品，其中一个类别跨越了100种不同类型。

```py
# Define 1000 categories (for simplicity, these are just numbered)
categories = [f"Category_{i}" for i in range(1, 200)]

manufacturers = ["Manufacturer_A", "Manufacturer_B", "Manufacturer_C"]
satisfied = ["Satisfied", "Not Satisfied"]
n_rows = 1000  

# Generate random data
data = {
    "Product_ID": [f"Product_{i}" for i in range(n_rows)],
    "Category": [random.choice(categories) for _ in range(n_rows)],
    "Price": [round(random.uniform(10, 500), 2) for _ in range(n_rows)],
    "Quality": [random.choice(satisfied) for _ in range(n_rows)],
    "Manufacturer": [random.choice(manufacturers) for _ in range(n_rows)],
}

df = pd.DataFrame(data)

print("Dimension before one-hot encoding:",df.shape)
print(df.head())
```

![](../Images/1ac7b8734b85d8db3f69932a87037dd7.png)

产品的合成数据集

请注意，数据集的维度是1000行和5列。现在，让我们观察应用独热编码后的变化。

```py
# Now do one-hot encoding
encoder = OneHotEncoder(sparse_output=False)

# Reshape the 'Category' column to a 2D array as required by the OneHotEncoder
category_array = df['Category'].values.reshape(-1, 1)

one_hot_encoded_array = encoder.fit_transform(category_array)
one_hot_encoded_df = pd.DataFrame(one_hot_encoded_array, columns=encoder.get_feature_names_out(['Category']))
encoded_df = pd.concat([df.drop('Category', axis=1), one_hot_encoded_df], axis=1)

print("Dimension after one-hot encoding:", encoded_df.shape)
```

![](../Images/925f370c09ba99ca0a025fc32e89fdbf.png)

维度显著增加了！

在应用独热编码后，我们的数据集维度膨胀到了1000x201——比之前大了整整40倍。这一增长是个问题，因为它需要更多的内存。而且，你会注意到新创建的列中的大多数值都是零，这就导致了我们所谓的稀疏数据集。某些模型，特别是基于树的模型，处理稀疏数据时会遇到困难。此外，处理高维数据时会出现其他挑战，通常被称为“维度灾难”。另外，由于独热编码将每个类别当作一个独立的列，我们会失去任何顺序信息。因此，如果你的变量中的类别本身具有层次顺序，独热编码可能不是最佳选择。

我们如何应对这些缺点？一种方法是使用不同的编码方式。或者，你可以限制变量的类别数量。通常，即使类别数量很多，变量的大多数值也集中在少数几个类别中。在这种情况下，将这些少数类别视为“其他”类别可能会很有效。这可以通过设置如`**min_frequency**`或`**max_categories**`之类的参数，在OneHotEncoder中实现。另一种应对稀疏数据的策略是使用特征哈希技术，本质上是通过哈希函数将多个类别映射到一个低维空间，或者使用PCA等降维技术。

这是**独热编码**的简要总结：

优点：

- 防止误导性解释：避免模型将数据误解为有某种顺序，这是标签/目标编码中常见的问题。

- 适用于非序数特征：非常适合没有序数关系的类别数据。

缺点：

- 维度增加：会显著增加数据集的维度，这在变量类别较多时尤为成问题。

- 稀疏矩阵：会导致许多列填充零，生成稀疏数据。

- 高基数特征效率低：对于类别数较多的变量效果较差。

**目标编码**

现在，让我们探索目标编码，这是一种在高基数数据和树模型等算法中特别有效的技术。

目标编码的本质是利用因变量的值信息。其实现方式根据任务的不同而有所变化。在回归任务中，我们通过每个类别的因变量均值来编码目标变量。在二分类任务中，编码是通过目标变量所属类别的概率来进行的（该概率通过该类别中结果为1的行数与该类别总行数之比计算得到）。在多分类任务中，类别变量是基于所属每个类别的概率进行编码的，结果会产生与因变量中的类别数量相等的新列。为了更清楚地说明这一点，让我们使用与独热编码相同的商品数据集。

让我们从回归任务中的目标编码开始。假设我们想要预测商品价格，并希望对商品类型进行编码。与其他编码方式类似，我们使用来自sklearn.preprocessing的**TargetEncoder**！

```py
from sklearn.preprocessing import TargetEncoder
x = df.drop(["Price"], axis=1)
x_need_encode = df["Category"].to_frame()
y = df["Price"]

# Define encoder
encoder = TargetEncoder()
x_encoded = encoder.fit_transform(x_need_encode, y)

# Encoder with 0 smoothing
encoder_no_smooth = TargetEncoder(smooth=0)
x_encoded_no_smooth = encoder_no_smooth.fit_transform(x_need_encode, y)

x_encoded = pd.DataFrame(x_encoded, columns=["encoded_category"])
data_target = pd.concat([x, x_encoded], axis=1)

print("Dimension before encoding:", df.shape)
print("Dimension after encoding:", data_target.shape)
print("---------")
print("Encoding")
print(encoder.encodings_[0][:5])
print(encoder.categories_[0][:5])
print(" ")
print("Encoding with no smooth")
print(encoder_no_smooth.encodings_[0][:5])
print(encoder_no_smooth.categories_[0][:5])
print("---------")
print("Mean by Category")
print(df.groupby("Category").mean("Price").head())
print("---------")
print("dataset:")
print(data_target.head())
```

![](../Images/2bb9fff8787574918ac3354c70c58f01.png)

回归任务中的目标编码

编码后，你会注意到，尽管变量有许多类别，数据集的维度保持不变（1000 x 5）。你还可以观察每个类别是如何编码的。尽管我提到每个类别的编码是基于该类别的目标变量的均值，但你会发现，使用默认设置进行编码时，实际均值与编码结果略有不同。这种差异产生的原因是，默认情况下，函数会自动选择一个平滑参数。该参数将本地类别的均值与全局均值相结合，这对于防止样本量较少的类别过拟合非常有用。如果我们设置`smooth=0`，编码值将与实际均值完全一致。

现在，让我们考虑二分类问题。假设我们的目标是判断产品的质量是否令人满意。在这种情况下，编码值表示该类别是“满意”的概率。

```py
x = df.drop(["Quality"], axis=1)
x_need_encode = df["Category"].to_frame()
y = df["Quality"]

# Define encoder
encoder = TargetEncoder()
x_encoded = encoder.fit_transform(x_need_encode, y)

x_encoded = pd.DataFrame(x_encoded, columns=["encoded_category"])
data_target = pd.concat([x, x_encoded], axis=1)

print("Dimension:", data_target.shape)
print("---------")
print("Encoding")
print(encoder.encodings_[0][:5])
print(encoder.categories_[0][:5])
print("---------")
print(encoder.classes_)
print("---------")
print("dataset:")
print(data_target.head())
```

![](../Images/3f79e8ec250bc34257fd730c1849880c.png)

二分类的目标编码

你确实可以看到，`encoded_category`表示的是“满意”的概率（介于0和1之间的浮动值）。要查看每个类别是如何编码的，你可以查看编码器的`classes_`属性。对于二分类问题，列表中的第一个值通常会被丢弃，这意味着此列表示的是满意的概率。方便的是，编码器会自动检测任务类型，因此无需指定它是一个二分类任务。

最后，我们来看一个多类分类的例子。假设我们正在预测哪家制造商生产了某个产品。

```py
x = df.drop(["Manufacturer"], axis=1)
x_need_encode = df["Category"].to_frame()
y = df["Manufacturer"]

# Define encoder
encoder = TargetEncoder()
x_encoded = encoder.fit_transform(x_need_encode, y)

x_encoded = pd.DataFrame(x_encoded, columns=encoder.classes_)
data_target = pd.concat([x, x_encoded], axis=1)

print("Dimension:", data_target.shape)
print("---------")
print("Encoding")
print(encoder.encodings_[0][:5])
print(encoder.categories_[0][:5])
print("---------")
print("dataset:")
print(data_target.head())
```

![](../Images/2bdc98734f9d85e95b1efc30c5c275a5.png)

多类分类的目标编码

编码后，你会看到现在我们有了每个制造商的列。这些列表示一个产品属于某个类别并由该制造商生产的概率。尽管我们的数据集略有扩展，但因变量的类别数通常较少，因此不太可能引起问题。

目标编码对于基于树的模型尤其有利。这些模型根据特征值进行划分，目的是最有效地分隔目标变量。通过直接结合目标变量的均值，目标编码为模型提供了一种清晰且高效的划分方式，通常比其他编码方法更有效。

然而，使用目标编码时需要谨慎。如果某个类别的观测样本很少，而且这些样本不能代表该类别的真实均值，就有可能导致过拟合。

这引出了另一个关键点：在将数据拆分为训练集和测试集之后执行目标编码是至关重要的。如果提前进行编码，可能会导致数据泄漏，因为编码会受到测试数据集中的结果的影响。这可能导致模型在训练数据集上表现得非常好，给您一个虚假的效果评价。因此，为了准确评估模型的性能，确保在训练集和测试集拆分后再进行目标编码。

这里是关于**目标编码**的简要总结：

优点：

- 控制基数：对于高基数特征非常有效，因为它不会增加特征空间。

- 能够捕获标签中的信息：通过引入目标数据，它通常能够提高预测性能。

- 对树模型有用：特别适用于复杂模型，如随机森林或梯度提升机。

缺点：

- 过拟合风险：特别是当类别的观察数据量有限时，过拟合的风险会增加。

- 目标泄漏：它可能无意中将未来的信息引入模型，即目标变量中的细节，这些细节在实际预测中是无法访问的。

- 可解释性较差：由于转换是基于目标的，与独热编码或标签编码等方法相比，它们可能更难解释。

**最后的提示**

总结一下，我想提供一些实用的建议。在整个讨论中，我们已经探讨了不同的编码技术，但实际上，您可能希望对数据集中的不同变量应用不同的编码。这时候，`**make_column_transformer**`来自sklearn.compose就非常有用了。例如，假设您正在预测产品价格，并决定对“类别”使用目标编码，因为其基数较高，而对“制造商”和“质量”使用独热编码。为此，您可以定义包含每种编码类型变量名称的数组，并像下面这样应用该函数。这个方法使得您能够无缝地处理转换后的数据，从而得到一个高效编码的数据集，准备好进行分析！

```py
from sklearn.compose import make_column_transformer
ohe_cols = ["Manufacturer"]
te_cols = ["Category", "Quality"]

encoding = make_column_transformer(
    (OneHotEncoder(), ohe_cols),
    (TargetEncoder(), te_cols)
)

x = df.drop(["Price"], axis=1)
y = df["Price"]

# Fit the transformer
x_encoded = encoding.fit_transform(x, y)
x_encoded = pd.DataFrame(x_encoded, columns=encoding.get_feature_names_out())

x_rest = x.drop(ohe_cols+te_cols, axis=1)
print(pd.concat([x_rest, x_encoded],axis=1).head()) 
```

![](../Images/3cb2e44c1f3d49351b860bced3a3f104.png)

使用make_column_faster结合目标编码和独热编码

非常感谢您抽出时间阅读这篇文章！当我第一次开始我的机器学习旅程时，选择正确的编码技术并理解它们的实现对我来说是一件相当复杂的事。我真诚地希望这篇文章能为您提供一些启示，让您的路径变得更加清晰！

**来源：**

Scikit-learn: Python中的机器学习，Pedregosa等人，JMLR 12，页2825–2830，2011。

Scikit-learn文档：

有序编码器: [https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OrdinalEncoder.html#sklearn.preprocessing.OrdinalEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OrdinalEncoder.html#sklearn.preprocessing.OrdinalEncoder)

目标编码器: [https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.TargetEncoder.html#sklearn.preprocessing.TargetEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.TargetEncoder.html#sklearn.preprocessing.TargetEncoder)

独热编码器 [https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder)
