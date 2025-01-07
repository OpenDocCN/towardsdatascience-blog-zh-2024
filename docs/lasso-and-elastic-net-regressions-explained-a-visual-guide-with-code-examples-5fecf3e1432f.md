# Lasso和Elastic Net回归解释：带代码示例的可视化指南

> 原文：[https://towardsdatascience.com/lasso-and-elastic-net-regressions-explained-a-visual-guide-with-code-examples-5fecf3e1432f?source=collection_archive---------1-----------------------#2024-12-06](https://towardsdatascience.com/lasso-and-elastic-net-regressions-explained-a-visual-guide-with-code-examples-5fecf3e1432f?source=collection_archive---------1-----------------------#2024-12-06)

## 回归算法

## 使用坐标下降法引入关键特征

[](https://medium.com/@samybaladram?source=post_page---byline--5fecf3e1432f--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--5fecf3e1432f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5fecf3e1432f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5fecf3e1432f--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--5fecf3e1432f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5fecf3e1432f--------------------------------)·阅读时间15分钟·2024年12月6日

--

[](/least-squares-regression-explained-a-visual-guide-with-code-examples-for-beginners-2e5ad011eae4?source=post_page-----5fecf3e1432f--------------------------------) [## 最小二乘回归解释：带代码示例的可视化指南

### 通过点滑动以最小化平方差

[towardsdatascience.com](https://towardsdatascience.com/lasso-and-elastic-net-regressions-explained-a-visual-guide-with-code-examples-5fecf3e1432f?source=post_page-----5fecf3e1432f--------------------------------)

线性回归有不同的类型：[最小二乘法](https://towardsdatascience.com/lasso-and-elastic-net-regressions-explained-a-visual-guide-with-code-examples-5fecf3e1432f)是基础，从经典的普通最小二乘法（OLS）到通过正则化防止过拟合的岭回归。然后是Lasso回归，它通过自动选择重要特征并忽略其他特征来采取独特的方式。Elastic Net则结合了两者的优势，混合了Lasso的特征选择和岭回归处理相关特征的能力。

看到许多文章将这些方法视为基本相同，只是做了一些小的调整，真的让人感到沮丧。它们让人觉得在这些方法之间切换就像改变代码中的一个设置一样简单，但每种方法实际上使用不同的方式来解决优化问题！

虽然OLS和Ridge回归可以通过矩阵运算直接求解，但Lasso和Elastic Net需要一种不同的方法——一种称为**坐标下降法**的迭代方法。在这里，我们将通过清晰的可视化来探索这个算法是如何工作的。那么，让我们准备好，*套索*一下这些细节吧！

![](../Images/89cfee410d2caa68f774230733aaf4c0.png)

所有视觉效果：作者使用Canva Pro创建。优化为手机显示；在桌面上可能会显得过大。

# 定义

## Lasso回归

**LASSO**（**L**east **A**bsolute **S**hrinkage and **S**election **O**perator）是线性回归的一种变体，它在模型中添加了惩罚项。它使用线性方程来预测数值，类似于线性回归。然而，Lasso还有一种方法可以将某些因素的重要性减少到零，这使得它在两个主要任务中非常有用：进行预测和识别最重要的特征。

## Elastic Net回归

Elastic Net回归是Ridge和Lasso回归的混合体，结合了它们的惩罚项。名称“Elastic Net”源自物理学：就像弹性网可以伸展而保持形状一样，这种方法在保持结构的同时适应数据。

该模型平衡三个目标：最小化预测误差、保持系数的较小（如Lasso）以及防止任何系数变得过大（如Ridge）。使用该模型时，你将数据的特征值输入线性方程中，就像标准的线性回归一样。

Elastic Net的主要优势在于，当特征之间相关时，它倾向于将它们作为一个整体保留或移除，而不是随机选择组中的某个特征。

![](../Images/92cd64e687e3a8222ab9d9001eead95d.png)

线性模型如Lasso和Elastic Net属于更广泛的机器学习方法家族，它们通过变量之间的线性关系来预测结果。

# 📊 使用的数据集

为了说明我们的概念，我们将使用[我们的标准数据集](/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)，该数据集用于预测在某一天访问的高尔夫球手人数，使用的特征包括天气情况、温度、湿度和风速条件。

为了使Lasso和Elastic Net有效工作，我们需要标准化数值特征（使它们的尺度可比）并对分类特征应用独热编码，因为这两种模型的惩罚项对特征尺度非常敏感。

![](../Images/d5b052ff81db21a6616645b0912d1802.png)

列：‘Outlook’（独热编码为晴天、阴天、雨天），‘Temperature’（标准化），‘Humidity’（标准化），‘Wind’（是/否）和‘Number of Players’（数值型，目标特征）

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

Lasso和Elastic Net回归通过将数据拟合成一条直线（或超平面）来预测数值，同时以不同的方式控制系数的大小：

1.  两个模型通过在预测精度和系数控制之间找到最佳平衡来找到最佳线。它们努力减小真实值和预测值之间的差距，同时通过惩罚项控制系数。

1.  在Lasso中，惩罚（由*λ*控制）可以将系数缩小到零，完全移除特征。弹性网结合了两种类型的惩罚：一种可以移除特征（像Lasso），另一种则将相关特征组合在一起进行收缩。这两种惩罚的混合由`l1_ratio`（*α*）控制。

1.  要预测一个新的答案，两个模型都会将每个输入与其系数相乘（如果不为零），然后将它们加起来，再加上一个起始数值（截距/偏差）。弹性网通常保留更多的特征，而Lasso则通过较小的系数来实现这一点，尤其是在特征相关时。

1.  惩罚的强度影响模型的行为：

    - 在Lasso中，较大的*λ*意味着更多的系数变为零

    - 在弹性网中，*λ*控制整体惩罚强度，而*α*决定特征移除与系数收缩之间的平衡

    - 当惩罚非常小时，两个模型的行为更像标准的线性回归

![](../Images/a24d6aea0a7577617aaccec1a0dda614.png)

Lasso和弹性网通过将输入特征与其训练权重相乘，并将它们与偏差项相加，来进行预测，从而生成最终的输出值。

# 训练步骤

让我们探讨一下Lasso和弹性网如何通过坐标下降法从数据中学习。虽然这些模型有复杂的数学基础，但我们将专注于**理解坐标下降法**——一种高效的优化方法，使计算变得更加实际和直观。

## Lasso回归的坐标下降法

Lasso回归的优化问题如下：

![](../Images/ffc77f9feca66a0d82b821c01933508b.png)

虽然[scikit-learn实现](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.Lasso.html)包括了额外的缩放因子（1/(2*n_samples)）以提高计算效率，但为了更清晰的说明，我们将使用标准的理论形式。

以下是坐标下降法通过一次更新一个特征来找到最优系数的方法：

1\. 首先将模型初始化，使所有系数为零。为正则化参数设置一个固定值，该参数将控制惩罚的强度。

![](../Images/063f2e17b33d37e82d58982abab75518.png)

Lasso回归从将所有特征权重设置为零开始，并使用惩罚参数（λ）来控制在训练过程中权重的收缩程度。

2\. 通过计算所有目标值的均值来计算初始偏差。

![](../Images/f2df6fc11bb7aa805db1e21ff38c5325.png)

初始偏差值设置为37.43，这是通过计算训练数据中所有目标值的平均值（从索引0到13的玩家数量的均值）得出的。

3\. 更新第一个系数（在我们的例子中是“sunny”）：

- 使用加权和，计算模型**在不使用该特征的情况下**的预测值。

![](../Images/16a9669367286fe143c27efffb821f61.png)

在训练开始时，所有特征权重都被设置为零，同时使用初始偏差值37.43，这导致模型对于所有训练示例，无论其输入特征如何，都预测相同的平均值（37.43）。

- 找到偏残差——这些预测值与实际值的偏差。使用这个值来计算临时系数。

![](../Images/0431216a648bff8bd9d223beb5d087ad.png)

对于第一个特征，Lasso通过比较真实标签和预测值来计算临时系数11.17，只考虑特征值为1的行，并应用梯度公式。

- 对这个临时系数应用Lasso的收缩（软阈值）来得到该步骤的最终系数。

![](../Images/290cc6c85081860c00fb5348ecf1e598.png)

Lasso将其收缩公式应用于临时系数（11.17），其中从绝对值中减去惩罚项（λ/5 = 0.2）并保留符号，得到最终系数10.97。

4\. 逐个处理其余系数，重复相同的更新过程。在每次更新时计算预测值时，使用所有其他系数的最新更新值。

![](../Images/970b037792a6038bf56c11b1122af6ba.png)

在将第一个系数更新为10.97后，Lasso使用这些更新后的预测值来计算第二个特征的临时系数（0.32），展示了算法如何通过坐标下降一次更新一个系数。

```py
import numpy as np

# Initialize bias as mean of target values and coefficients to 0
bias = np.mean(y_train)
beta = np.zeros(X_train_scaled.shape[1])
lambda_param = 1

# One cycle through all features
for j, feature in enumerate(X_train_scaled.columns):
    # Get current feature values
    x_j = X_train_scaled.iloc[:, j].values

    # Calculate prediction excluding the j-th feature
    y_pred_no_j = bias + X_train_scaled.values @ beta - x_j * beta[j]

    # Calculate partial residuals
    residual_no_j = y_train.values - y_pred_no_j

    # Calculate the dot product of x_j with itself (sum of squared feature values)
    sum_squared_x_j = np.dot(x_j, x_j)

    # Calculate temporary beta without regularization (raw update)
    beta_old = beta[j]
    beta_temp = beta_old + np.dot(x_j, residual_no_j) / sum_squared_x_j

    # Apply soft thresholding for Lasso penalty
    beta[j] = np.sign(beta_temp) * max(abs(beta_temp) - lambda_param / sum_squared_x_j, 0)

# Print results
print("Coefficients after one cycle:")
for feature, coef in zip(X_train_scaled.columns, beta):
    print(f"{feature:11}: {coef:.2f}")
```

5\. 返回更新偏差，通过计算当前模型**使用所有特征**时的预测值，然后根据这些预测值与实际值之间的平均差异调整偏差。

![](../Images/2ad780f7356dc1034f149cd228fb7f72.png)

在通过坐标下降更新所有特征系数后，模型重新计算偏差（40.25），作为真实标签与使用当前特征权重所做预测之间的平均差异，确保模型的预测值围绕目标值进行合理的调整。

```py
# Update bias (not penalized by lambda)
y_pred = X_train_scaled.values @ beta  # only using coefficients, no bias
residuals = y_train.values - y_pred
bias = np.mean(residuals)  # this replaces the old bias
```

6\. 检查模型是否已收敛，可以通过达到允许的最大迭代次数或看到系数不再发生显著变化来判断。如果没有收敛，返回第3步并重复该过程。

![](../Images/dbde5df18fb4166d6ca595733b30eef6.png)

经过1000次坐标下降迭代后，Lasso生成了最终模型，其中一些系数已经精确地缩小到零（“雨”和“温度”特征），而其他系数保持非零值，展示了Lasso的特征选择能力。

```py
from sklearn.linear_model import Lasso

# Fit Lasso from scikit-learn
lasso = Lasso(alpha=1) # Default value is 1000 cycle
lasso.fit(X_train_scaled, y_train)

# Print results
print("\nCoefficients after 1000 cycles:")
print(f"Bias term  : {lasso.intercept_:.2f}")
for feature, coef in zip(X_train_scaled.columns, lasso.coef_):
   print(f"{feature:11}: {coef:.2f}")
```

## 弹性网回归的坐标下降

弹性网回归的优化问题如下：

![](../Images/57cb5db32b807d107f8cf8cae9a41f19.png)

虽然 [scikit-learn 的实现](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.ElasticNet.html)包括了额外的缩放因子（1/(2*n_samples)）并使用 alpha (α) 来控制整体正则化强度，同时使用 l1_ratio 来控制惩罚项的比例，但为了清晰起见，我们将使用标准的理论形式。

Elastic Net 的坐标下降算法与 Lasso 类似，但在更新系数时考虑了两种惩罚项。它的工作原理如下：

1\. 首先将模型初始化为所有系数为零。设置两个固定值：一个控制特征移除（类似于 Lasso），另一个用于一般的系数缩减（这是与 Lasso 的主要区别）。

![](../Images/ce717b9cd599b195a4313563dc486b1c.png)

Elastic Net 回归像 Lasso 一样从所有特征的权重为零开始，但使用两个参数：λ（lambda）表示整体正则化强度，α（alpha）用于在 Lasso 和 Ridge 惩罚之间进行平衡。

2\. 通过计算所有目标值的均值来计算初始偏置。 （与 Lasso 相同）

![](../Images/de17fbaa9b6e7d4e9b6b761036cc08ce.png)

像 Lasso 一样，Elastic Net 也通过计算训练数据集中所有目标值的均值，将偏置项初始化为 37.43。

3\. 更新第一个系数：

- 使用加权和，计算模型如果不使用该特征时的预测结果。（与 Lasso 相同）

![](../Images/16a9669367286fe143c27efffb821f61.png)

Elastic Net 从类似 Lasso 的坐标下降过程开始，由于所有特征的权重都被设为零，只有偏置项是活跃的，因此对所有训练样本的初始预测值为 37.43。

- 计算部分残差——这些预测值与实际值之间的差距。利用该值，计算临时系数。（与 Lasso 相同）

![](../Images/0431216a648bff8bd9d223beb5d087ad.png)

像 Lasso 一样，Elastic Net 通过比较预测值与真实标签，计算出第一个特征的临时系数 11.17。

- 对于 Elastic Net，应用 **软阈值** 和 **系数缩减** 对临时系数进行处理，从而获得该步骤的最终系数。这种综合效果是与 Lasso 回归的主要区别。

![](../Images/e327609ca8f462275e64233a3bf0d258.png)

Elastic Net 应用了独特的缩减公式，结合了 Lasso（L1）和 Ridge（L2）惩罚项，其中 α 控制它们的平衡。通过这种组合的正则化方法，临时系数 11.17 被缩减为 10.06。

4\. 逐个处理其余系数，重复相同的更新过程。在每次更新时，计算预测值时使用所有其他系数的最新更新值。（与 Lasso 相同，但使用修改后的更新公式）

![](../Images/e88dbf7e3ac2a2cd0364b38a574f89a1.png)

在将第一个系数更新为10.06后，弹性网继续进行坐标下降法，通过计算和更新第二个系数，展示了其在处理特征时一一处理并保持L1和L2正则化效果的过程。

```py
import numpy as np

# Initialize bias as mean of target values and coefficients to 0
bias = np.mean(y_train)
beta = np.zeros(X_train_scaled.shape[1])
lambda_param = 1
alpha = 0.5  # mixing parameter (0 for Ridge, 1 for Lasso)

# One cycle through all features
for j, feature in enumerate(X_train_scaled.columns):
    # Get current feature values
    x_j = X_train_scaled.iloc[:, j].values

    # Calculate prediction excluding the j-th feature
    y_pred_no_j = bias + X_train_scaled.values @ beta - x_j * beta[j]

    # Calculate partial residuals
    residual_no_j = y_train.values - y_pred_no_j

    # Calculate the dot product of x_j with itself (sum of squared feature values)
    sum_squared_x_j = np.dot(x_j, x_j)

    # Calculate temporary beta without regularization (raw update)
    beta_old = beta[j]
    beta_temp = beta_old + np.dot(x_j, residual_no_j) / sum_squared_x_j

    # Apply soft thresholding for Elastic Net penalty
    l1_term = alpha * lambda_param / sum_squared_x_j     # L1 (Lasso) penalty term
    l2_term = (1-alpha) * lambda_param / sum_squared_x_j # L2 (Ridge) penalty term

    # First apply L1 soft thresholding, then L2 scaling
    beta[j] = (np.sign(beta_temp) * max(abs(beta_temp) - l1_term, 0)) / (1 + l2_term)

# Print results
print("Coefficients after one cycle:")
for feature, coef in zip(X_train_scaled.columns, beta):
    print(f"{feature:11}: {coef:.2f}")
```

5. 更新偏差，方法是计算当前模型使用所有特征的预测值，然后根据这些预测与实际值之间的平均差异调整偏差。（与Lasso相同）

![](../Images/84a57041683e2e82a1f091998f2fd4e8.png)

在使用弹性网的L1和L2正则化更新所有特征系数之后，模型通过取真实标签与预测值之间的平均差异，将偏差重新计算为40.01，类似于Lasso回归中的过程。

```py
# Update bias (not penalized by lambda)
y_pred_with_updated_beta = X_train_scaled.values @ beta  # only using coefficients, no bias
residuals_for_bias_update = y_train.values - y_pred_with_updated_beta
new_bias = np.mean(y_train.values - y_pred_with_updated_beta)  # this replaces the old bias

print(f"Bias term  : {new_bias:.2f}")
```

6. 检查模型是否已经收敛，方法是检查是否已达到最大允许的迭代次数，或者查看系数是否不再发生显著变化。如果未收敛，返回到步骤3并重复该过程。

![](../Images/4bdff8f8191a93822b8c5d9788dce60f.png)

经过1000次迭代后的最终弹性网模型，与Lasso相比，显示出较小的系数值，并且较少的系数被压缩为零。

```py
from sklearn.linear_model import ElasticNet

# Fit Lasso from scikit-learn
elasticnet = Lasso(alpha=1) # Default value is 1000 cycle
elasticnet.fit(X_train_scaled, y_train)

# Print results
print("\nCoefficients after 1000 cycles:")
print(f"Bias term  : {elasticnet.intercept_:.2f}")
for feature, coef in zip(X_train_scaled.columns, elasticnet.coef_):
   print(f"{feature:11}: {coef:.2f}")
```

# **测试步骤**

预测过程与OLS相同——将新的数据点与系数相乘：

## Lasso回归

![](../Images/5b346c92504c34a5978d7fa63339b597.png)

当将训练好的Lasso模型应用于未见过的数据时，它会将每个特征值与相应的系数相乘，并加上偏差项（41.24），从而对这个新的数据点进行最终预测，得出40.2个玩家的结果。

## 弹性网回归

![](../Images/d236c4e9015808c670b7af71685e43ea.png)

训练好的弹性网模型通过将特征与其更均匀分布的系数相乘并加上偏差（38.59），预测出40.83个玩家，与Lasso相比，由于其平衡的正则化方法，预测结果略有不同。

# 评估步骤

我们可以对所有数据点执行相同的过程。对于我们的数据集，以下是最终结果以及对应的RMSE值：

## Lasso回归

![](../Images/b6e2f8a906fcbe42c82b4e75ee41cddf.png)

Lasso在多个测试案例中的表现显示，其均方根误差（RMSE）为7.203，这是通过将其预测值与14个不同测试样本的实际玩家数进行比较计算得出的。

## 弹性网回归

![](../Images/5ede9d56bb31b43bac0195a76b3cde52.png)

弹性网的RMSE稍高于Lasso，可能是因为其结合的L1和L2惩罚项保留了更多的小的非零系数，这可能引入更多的预测方差。

# 关键参数

## Lasso回归

Lasso回归使用坐标下降法来解决优化问题。以下是该方法的关键参数：

+   `alpha`（λ）：控制惩罚大系数的强度。较高的值会强制更多的系数变为零。默认值为1.0。

+   `max_iter`：设置算法在寻找最佳结果时会更新其解的最大周期数。默认值是1000。

+   `tol`：设置系数变化需要多小才能使算法判断找到足够好的解。默认值是0.0001。

## 弹性网回归。

弹性网回归结合了两种类型的惩罚，并且使用坐标下降法。以下是相关的关键参数：

+   `alpha`（*λ*）：控制两种惩罚的整体强度。较高的值表示较强的惩罚。默认值是1.0。

+   `l1_ratio`（*α*）：设置每种惩罚的使用比例。值为0时，仅使用Ridge惩罚；值为1时，仅使用Lasso惩罚；0和1之间的值则同时使用两者。默认值为0.5。

+   `max_iter`：坐标下降算法的最大迭代次数。默认值是1000次迭代。

+   `tol`：优化收敛的容差，类似于Lasso。默认值是1e-4。

*注意*：不要混淆，在`scikit-learn`的代码中，正则化参数叫做`alpha`，但在数学符号中通常写作*λ*（lambda）。类似地，混合参数在代码中叫做`l1_ratio`，但在数学符号中写作*α*（alpha）。我们在这里使用数学符号，以匹配标准的教科书符号。

# 模型比较：OLS与Lasso与Ridge与弹性网。

使用弹性网时，实际上可以通过调整参数来探索不同类型的线性回归模型：

+   当`alpha` = 0时，我们得到普通最小二乘法（OLS）。

+   当`alpha` > 0且`l1_ratio` = 0时，我们得到Ridge回归。

+   当`alpha` > 0且`l1_ratio` = 1时，我们得到Lasso回归。

+   当`alpha` > 0且0 < `l1_ratio` < 1时，我们得到弹性网回归。

实际操作中，建议探索一系列的`alpha`值（如0.0001、0.001、0.01、0.1、1、10、100）和`l1_ratio`值（如0、0.25、0.5、0.75、1），最好使用交叉验证来寻找最佳组合。

在这里，让我们看看模型系数、偏置项以及测试集RMSE如何随着不同的正则化强度（*λ*）和混合参数（`l1_ratio`）的变化而变化。

![](../Images/7b26b010afc0a4b6801ec0128418fb8c.png)![](../Images/6f1640b2c83d7addefc37d8c4619dc71.png)![](../Images/d8d68a8953bf6af67433a1de86169808.png)![](../Images/04b0cdb113e505cc73a2a6a207f47dbb.png)![](../Images/9dd62b75a888017ce978d13d494a615e.png)

最佳模型是Lasso（α = 0）与λ = 0.1，获得的RMSE为6.561，表明纯L1正则化最适合我们的数据集。

```py
# Define parameters
l1_ratios = [0, 0.25, 0.5, 0.75, 1]
lambdas = [0, 0.01, 0.1, 1, 10]
feature_names = X_train_scaled.columns

# Create a dataframe for each lambda value
for lambda_val in lambdas:
    # Initialize list to store results
    results = []
    rmse_values = []

    # Fit ElasticNet for each l1_ratio
    for l1_ratio in l1_ratios:
        # Fit model
        en = ElasticNet(alpha=lambda_val, l1_ratio=l1_ratio)
        en.fit(X_train_scaled, y_train)

        # Calculate RMSE
        y_pred = en.predict(X_test_scaled)
        rmse = root_mean_squared_error(y_test, y_pred)

        # Store coefficients and RMSE
        results.append(list(en.coef_.round(2)) + [round(en.intercept_,2),round(rmse,3)])

    # Create dataframe with RMSE column
    columns = list(feature_names) + ['Bias','RMSE']
    df = pd.DataFrame(results, index=l1_ratios, columns=columns)
    df.index.name = f'λ = {lambda_val}'

    print(df)
```

*注意*：尽管弹性网（Elastic Net）可以通过调整其参数实现OLS、Ridge和Lasso的功能，但最好使用针对每种回归方法设计的特定命令。在scikit-learn中，使用`LinearRegression`进行OLS，使用`Ridge`进行Ridge回归，使用`Lasso`进行Lasso回归。只有在你想将Lasso和Ridge的特殊功能结合起来时，才使用弹性网。

# 最后总结：你应该使用哪种回归方法？

让我们来分析何时使用每种方法。

当你的数据集中的样本数多于特征数，并且特征之间的预测关系不强时，可以从**普通最小二乘回归（OLS）**开始。

**岭回归**在你遇到相反情况时表现良好——特征远多于样本数。当你的特征之间有强相关性时，它也非常有效。

**套索回归**在你想要发现哪些特征对预测真正有作用时最为适用。它会自动将不重要的特征设置为零，从而简化你的模型。

**弹性网**结合了岭回归和套索回归的优点。当你有一组相关特征，并且想要将它们一起保留或删除时，弹性网非常有用。如果你已经尝试过岭回归和套索回归，但结果不理想，弹性网可能会给你带来更好的预测。

如果你希望保留所有特征，好的策略是从岭回归开始。如果你想识别重要特征，可以尝试套索回归。如果这两者都没有得到好的结果，那么可以尝试弹性网回归。

# 🌟 套索回归和弹性网回归代码总结

```py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.compose import ColumnTransformer
from sklearn.metrics import root_mean_squared_error
from sklearn.linear_model import Lasso  #, ElasticNet

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
model = Lasso(alpha=0.1)  # Option 1: Lasso Regression (alpha is the regularization strength, equivalent to λ, uses coordinate descent)
#model = ElasticNet(alpha=0.1, l1_ratio=0.5)  # Option 2: Elastic Net Regression (alpha is the overall regularization strength, and l1_ratio is the mix between L1 and L2, uses coordinate descent)

# Fit the model
model.fit(X_train_scaled, y_train)

# Make predictions
y_pred = model.predict(X_test_scaled)

# Calculate and print RMSE
rmse = root_mean_squared_error(y_test, y_pred)
print(f"RMSE: {rmse:.4f}")

# Additional information about the model
print("\nModel Coefficients:")
for feature, coef in zip(X_train_scaled.columns, model.coef_):
    print(f"{feature:13}: {coef:.2f}")
print(f"Intercept    : {model.intercept_:.2f}")
```

## 进一步阅读

有关[Lasso](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.Lasso.html)回归和[Elastic Net](https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.ElasticNet.html)回归的详细解释以及它们在`scikit-learn`中的实现，读者可以参考它们的官方文档。该文档提供了有关如何使用和配置这些方法的全面信息。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.5版本。尽管本文讨论的概念通常适用，但具体的代码实现可能会根据不同版本有所不同。

## 关于插图

除非另有说明，所有图片均由作者创建，并结合了Canva Pro的授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙍𝙚𝙜𝙧𝙚𝙨𝙨𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----5fecf3e1432f--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----5fecf3e1432f--------------------------------)5个故事![一个戴着粉色帽子、扎着双马尾的卡通娃娃。这款“傀儡”娃娃以其简单的设计和心形图案的衣服，直观地展示了机器中“傀儡回归器”的概念。就像这个玩具人物是一个简化的静态人类形象一样，傀儡回归器是一个基础模型，作为更复杂分析的基准。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----5fecf3e1432f--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----5fecf3e1432f--------------------------------)8个故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----5fecf3e1432f--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----5fecf3e1432f--------------------------------)4个故事![](../Images/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](../Images/22a5d43568e70222eb89fd36789a9333.png)![](../Images/8ea1a2f29053080a5feffc709f5b8669.png)
