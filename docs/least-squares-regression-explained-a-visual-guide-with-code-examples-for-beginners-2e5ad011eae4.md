# 最小二乘回归解析：带有代码示例的可视化指南（适合初学者）

> 原文：[https://towardsdatascience.com/least-squares-regression-explained-a-visual-guide-with-code-examples-for-beginners-2e5ad011eae4?source=collection_archive---------1-----------------------#2024-11-05](https://towardsdatascience.com/least-squares-regression-explained-a-visual-guide-with-code-examples-for-beginners-2e5ad011eae4?source=collection_archive---------1-----------------------#2024-11-05)

## 回归算法

## 滑动穿越数据点以最小化平方差

[](https://medium.com/@samybaladram?source=post_page---byline--2e5ad011eae4--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--2e5ad011eae4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e5ad011eae4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2e5ad011eae4--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--2e5ad011eae4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e5ad011eae4--------------------------------) ·阅读时间：11 分钟·2024年11月5日

--

当人们开始学习数据分析时，他们通常从线性回归开始。这是有充分理由的——它是理解回归如何工作的最有用且直接的方式之一。线性回归最常见的方法被称为“最小二乘法”——它通过最小化预测值与实际值之间的平方差来寻找数据中的模式。最基本的类型是**普通最小二乘法**（OLS），它通过数据点找到最佳的直线拟合方式。

然而，有时，OLS 并不够——尤其是当你的数据有许多相关特征，可能导致结果不稳定时。这时，**岭回归**就派上用场了。岭回归与 OLS 做的工作相同，但它增加了一个特殊的控制项，帮助防止模型对任何单一特征过于敏感。

在这里，我们将快速了解两种最小二乘回归方法，探索这些算法如何平滑地穿过数据点，并在理论上看到它们的差异。

![](../Images/e13851516ec1dfc98b92fd91fe5661c6.png)

所有视觉内容：作者使用 Canva Pro 创建，已针对移动设备优化；在桌面上可能显示过大。

# 定义

线性回归是一种统计方法，使用线性方程预测数值。它通过拟合一条直线（或在多维情况下为平面）来模拟因变量与一个或多个自变量之间的关系。模型计算每个特征的系数，表示它们对结果的影响。要得到结果，你需要将数据的特征值输入到线性方程中以计算预测值。

# 📊 使用的数据集

为了说明我们的概念，我们将使用[我们的标准数据集](/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)，它用于预测某一天来访的高尔夫球手人数。该数据集包括天气预报、温度、湿度和风力等变量。

![](../Images/8f70777fcdcfbe631c352c17a649d816.png)

列：‘Outlook’（一热编码为晴天、多云、雨天）、‘Temperature’（以华氏度为单位）、‘Humidity’（以百分比表示）、‘Wind’（是/否）以及‘Number of Players’（数值型，目标特征）

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

# Create dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temp.': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humid.': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Num_Players': [52, 39, 43, 37, 28, 19, 43, 47, 56, 33, 49, 23, 42, 13, 33, 29, 25, 51, 41, 14, 34, 29, 49, 36, 57, 21, 23, 41]
}

df = pd.DataFrame(dataset_dict)

# One-hot encode 'Outlook' column
df = pd.get_dummies(df, columns=['Outlook'],prefix='',prefix_sep='')

# Convert 'Wind' column to binary
df['Wind'] = df['Wind'].astype(int)

# Split data into features and target, then into training and test sets
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)
```

虽然不是强制性的，但为了有效使用线性回归——包括岭回归——我们可以首先对数值特征进行标准化。

![](../Images/856bb5024939a0acbe57928f62346faa.png)

对“温度”和“湿度”应用标准化处理，而对“天气预报”和“风力”应用一热编码处理。

```py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.compose import ColumnTransformer

# Create dataset
data = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 
                'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 
                'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85, 80, 83, 70, 68, 65, 64, 72, 69, 75, 75, 72, 81, 71, 81, 74, 76, 78, 82, 
                   67, 85, 73, 88, 77, 79, 80, 66, 84],
    'Humidity': [85, 90, 78, 96, 80, 70, 65, 95, 70, 80, 70, 90, 75, 80, 88, 92, 85, 75, 92, 
                 90, 85, 88, 65, 70, 60, 95, 70, 78],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, 
             True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Num_Players': [52, 39, 43, 37, 28, 19, 43, 47, 56, 33, 49, 23, 42, 13, 33, 29, 25, 51, 41, 
                    14, 34, 29, 49, 36, 57, 21, 23, 41]
}

# Process data
df = pd.get_dummies(pd.DataFrame(data), columns=['Outlook'])
df['Wind'] = df['Wind'].astype(int)

# Split data
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Scale numerical features
numerical_cols = ['Temperature', 'Humidity']
ct = ColumnTransformer([('scaler', StandardScaler(), numerical_cols)], remainder='passthrough')

# Transform data
X_train_scaled = pd.DataFrame(
    ct.fit_transform(X_train),
    columns=numerical_cols + [col for col in X_train.columns if col not in numerical_cols],
    index=X_train.index
)

X_test_scaled = pd.DataFrame(
    ct.transform(X_test),
    columns=X_train_scaled.columns,
    index=X_test.index
)
```

# 主要机制

线性回归通过从数据中绘制一条直线（或超平面）来预测数字：

1.  该模型通过使真实值与线性预测值之间的误差尽可能小来找到最佳拟合线。这被称为“最小二乘法”。

1.  每个输入都会得到一个数字（系数/权重），表示它对最终结果的影响程度。还有一个起始数字（截距/偏置），当所有输入为零时使用该数字。

1.  要预测一个新的答案，模型会将每个输入乘以其对应的数字，然后加总这些值，再加上起始数字。这样就得到了预测结果。

![](../Images/c72b0e92063ef36ac8fb47688848a377.png)

# **普通最小二乘法（OLS）回归**

让我们从普通最小二乘法（OLS）开始——线性回归的基本方法。OLS的目标是找到一条最佳拟合线，通过数据点。我们通过衡量预测与实际值之间的“误差”来实现这一点，然后找到一条使这些误差尽可能小的线。当我们说“误差”时，我们指的是每个点与线之间的垂直距离——换句话说，就是我们的预测与实际值之间的偏差。首先让我们看看二维情况发生了什么。

## 二维情况

在二维情况下，我们可以这样想象线性回归算法：

![](../Images/6c8ce9540dd0d3e926482943741c9ad0.png)

这是上述过程的解释：

1.我们从一个训练集开始，每一行包含：

· *x* ：我们的输入特征（数字1，2，3，1，2）

· *y* ：我们的目标值（0，1，1，2，3）

2\. 我们可以将这些点绘制在散点图上，我们希望找到一条最佳拟合这些点的直线 *y* = *β*₀ + *β*₁*x*

3\. 对于任何给定的直线（任何 *β*₀ 和 *β*₁），我们可以通过以下方法来衡量它的好坏：

· 计算每个点到直线的垂直距离 (*d*₁, *d*₂, *d*₃, *d*₄, *d*₅)

· 这些距离是每个点的 |*y* — (*β*₀ + *β*₁*x*)|

4\. 我们的优化目标是找到 *β*₀ 和 *β*₁，使得平方距离之和最小化：*d*₁² + *d*₂² + *d*₃² + *d*₄² + *d*₅²。在向量表示法中，这可以写作 ||*y* — *Xβ*||²，其中 *X* = [1 *x*] 包含我们的输入数据（包含用于截距的1），*β* = [*β*₀ *β*₁]ᵀ 包含我们的系数。

5\. 最优解具有封闭形式：*β* = (*X*ᵀ*X*)⁻¹*X*ᵀy。通过计算，我们得到 *β*₀ = -0.196（截距），*β*₁ = 0.761（斜率）。

这个向量表示法使得公式更为简洁，且展示了我们实际上是使用矩阵和向量进行运算，而不是单独的点。我们将在下文的多维情况中看到更多关于计算的细节。

## 在多维情况下（📊 数据集）

同样，最小二乘法（OLS）的目标是找到系数 (*β*)，使得我们的预测与实际值之间的平方差最小化。数学上，我们将其表示为 **最小化** ||*y* — *Xβ*||²，其中 *X* 是我们的数据矩阵，*y* 包含我们的目标值。

训练过程遵循以下关键步骤：

## 训练步骤

1\. 准备我们的数据矩阵 *X*。这涉及到添加一列1来考虑偏置/截距项 (*β*₀)。

![](../Images/94f0dc6a1292fe7a1cd4d41f648b377b.png)

2\. 我们可以通过使用正规方程直接计算系数，而不是通过迭代搜索最佳系数：

*β* = (*X*ᵀ*X*)⁻¹*X*ᵀ*y*

其中：

· *β* 是估计系数的向量，

· *X* 是数据集矩阵（包括一个用于截距的列），

· *y* 是标签，

· *X*ᵀ 表示矩阵 *X* 的转置，

· ⁻¹ 表示矩阵的逆。

让我们分解一下：

a. 我们将 *X*ᵀ（*X* 的转置）与 *X* 相乘，得到一个方阵

![](../Images/41c746d7c63229c8463c4b4451d547c4.png)

b. 我们计算这个矩阵的逆

![](../Images/ec4c7598c2579c5b2aed1e6f0ba14c65.png)

c. 我们计算 *X*ᵀ*y*

![](../Images/56478c8b91a65d73a45096748d575778.png)

d. 我们将 (*X*ᵀ*X*)⁻¹ 与 *X*ᵀ*y* 相乘，以得到我们的系数

![](../Images/963c4e9a98a88c168f9c90739b1fc1a8.png)

## 测试步骤

一旦我们得到了系数，进行预测就变得非常简单：我们只需将新的数据点与这些系数相乘，就能得到我们的预测。

在矩阵表示法中，对于一个新的数据点 *x*，预测 *y* 可以计算为

*y* = *x* × *β* = [1, x₁, x₂, …, xₚ] × [β₀, β₁, β₂, …, βₚ]ᵀ，

其中 *β*₀ 是截距，*β*₁ 到 *β*ₚ 是每个特征的系数。

![](../Images/fc075c4c8801b373f49a757ac742fef5.png)

## 评估步骤

我们可以对所有数据点执行相同的过程。对于我们的数据集，以下是最终结果，并附有 RMSE 值。

![](../Images/c0f9605d793360d6c403abdb3192e0d5.png)

# **岭回归**

现在，让我们考虑岭回归，它在OLS的基础上解决了其一些局限性。岭回归的关键思想是，有时最优的OLS解**涉及非常大的系数**，这可能导致过拟合。

岭回归在目标函数中添加了惩罚项 (*λ*||*β*||²)。该项通过将系数的平方值添加到最小化目标中，来抑制大系数的出现。目标函数变为：

min ||*y* — *X*β||² + λ||*β*||²

*λ*（lambda）参数控制我们对大系数的惩罚程度。当 *λ* = 0 时，我们得到OLS；随着 *λ* 增加，系数会向零收缩（但永远不会完全为零）。

## 训练步骤

1.  就像OLS一样，准备我们的数据矩阵 *X*。这包括添加一列全为1的值来考虑截距项 (*β*₀)。

1.  岭回归的训练过程与OLS类似，但有所修改。封闭形式的解变为：

    *β* = (*X*ᵀ*X*+ λI)⁻¹*X*ᵀ*y*

其中：

· *I*是单位矩阵（第一个元素对应 *β*₀，某些实现中有时设置为0，以排除截距项的正则化），

· λ是正则化值。

· *Y*是观察到的因变量值的向量。

· 其他符号与OLS部分定义相同。

让我们拆解一下：

a. 我们将 *λ*I 加到 *X*ᵀ*X* 上。*λ*的值可以是任何正数（比如0.1）。

![](../Images/1f631555a5c3da55dcc0e5c818fdc266.png)

b. 我们计算该矩阵的逆。将 *λ*I 加到 *X*ᵀ*X* 后再进行求逆的好处是：

· 使矩阵可逆，即使 *X*ᵀ*X* 不是（通过OLS解决一个关键的数值问题）

· 按 *λ* 比例缩小系数

![](../Images/41521c73d61c93a7d2f3d061ed957b9a.png)

c. 我们计算 (*X*ᵀ*X*+ *λI*)⁻¹ 和 *X*ᵀ*y* 的乘积来获得系数

![](../Images/e4ed2341b5407401f67dd250d1b98f4f.png)

## **测试步骤**

预测过程与OLS相同——将新的数据点与系数相乘。不同之处在于系数本身，通常它们比OLS的系数要小且更稳定。

![](../Images/918f8768c3d930ee52fc25e57f1c38bb.png)

## 评估步骤

我们可以对所有数据点执行相同的过程。对于我们的数据集，这里是带有RMSE的最终结果。

![](../Images/d89851ba086f08012ca77a947dd7e06f.png)

## **最后备注：选择OLS与岭回归**

OLS和岭回归的选择通常取决于你的数据：

+   当你的数据表现良好，特征之间几乎没有多重共线性并且样本数足够时（相对于特征），使用OLS

+   当你有以下情况时，使用岭回归：

    - 特征多（相对于样本）

    - 特征中的多重共线性

    - OLS中的过拟合迹象

使用岭回归时，你需要选择 *λ*。从一系列值开始（通常以对数间隔），选择能够提供最佳验证性能的那个值。

![](../Images/0d0c60a41bac36424c7f233554495189.png)

显然，默认值 *λ = 1* 为我们的数据集提供了最佳的RMSE。

# 🌟 OLS 和岭回归代码总结

```py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.metrics import root_mean_squared_error
from sklearn.linear_model import Ridge

# Create dataset
data = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 
                'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 
                'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85, 80, 83, 70, 68, 65, 64, 72, 69, 75, 75, 72, 81, 71, 81, 74, 76, 78, 82, 
                   67, 85, 73, 88, 77, 79, 80, 66, 84],
    'Humidity': [85, 90, 78, 96, 80, 70, 65, 95, 70, 80, 70, 90, 75, 80, 88, 92, 85, 75, 92, 
                 90, 85, 88, 65, 70, 60, 95, 70, 78],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, 
             True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Num_Players': [52, 39, 43, 37, 28, 19, 43, 47, 56, 33, 49, 23, 42, 13, 33, 29, 25, 51, 41, 
                    14, 34, 29, 49, 36, 57, 21, 23, 41]
}

# Process data
df = pd.get_dummies(pd.DataFrame(data), columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)
df = df[['sunny','overcast','rain','Temperature','Humidity','Wind','Num_Players']]

# Split data
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Scale numerical features
numerical_cols = ['Temperature', 'Humidity']
ct = ColumnTransformer([('scaler', StandardScaler(), numerical_cols)], remainder='passthrough')

# Transform data
X_train_scaled = pd.DataFrame(
    ct.fit_transform(X_train),
    columns=numerical_cols + [col for col in X_train.columns if col not in numerical_cols],
    index=X_train.index
)

X_test_scaled = pd.DataFrame(
    ct.transform(X_test),
    columns=X_train_scaled.columns,
    index=X_test.index
)

# Initialize and train the model
#model = LinearRegression() # Option 1: OLS Regression
model = Ridge(alpha=0.1)  # Option 2: Ridge Regression (alpha is the regularization strength, equivalent to λ)

# Fit the model
model.fit(X_train_scaled, y_train)

# Make predictions
y_pred = model.predict(X_test_scaled)

# Calculate and print RMSE
rmse = root_mean_squared_error(y_test, y_pred)
print(f"RMSE: {rmse:.4f}")

# Additional information about the model
print("\nModel Coefficients:")
print(f"Intercept    : {model.intercept_:.2f}")
for feature, coef in zip(X_train_scaled.columns, model.coef_):
    print(f"{feature:13}: {coef:.2f}")
```

![](../Images/ff9ff4226a31e1ad21380175a7da9bac.png)

## 进一步阅读

对于 [OLS 线性回归](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.LinearRegression.html) 和 [岭回归](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.Ridge.html) 的详细解释及其在 scikit-learn 中的实现，读者可以参考它们的官方文档。文档提供了关于它们的使用和参数的全面信息。

## 技术环境

本文使用的是 Python 3.7 和 scikit-learn 1.5。虽然讨论的概念通常适用，但在不同版本中，具体的代码实现可能会有所不同。

## 关于插图

除非另有说明，所有图片均由作者创建，且融入了 Canva Pro 授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙍𝙚𝙜𝙧𝙚𝙨𝙨𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----2e5ad011eae4--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----2e5ad011eae4--------------------------------)5篇故事![一只戴着粉色帽子、绑着小辫子的卡通娃娃。这个“假人”娃娃，凭借其简单的设计和心形图案的衣服，形象地代表了机器学习中假回归器的概念。就像这个玩具般的形象是一个简化、静态的人物表现一样，假回归器是一个基本模型，用作更复杂分析的基线。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----2e5ad011eae4--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----2e5ad011eae4--------------------------------)8篇故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
