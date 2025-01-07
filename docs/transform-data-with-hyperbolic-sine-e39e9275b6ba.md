# 使用双曲正弦转换数据

> 原文：[https://towardsdatascience.com/transform-data-with-hyperbolic-sine-e39e9275b6ba?source=collection_archive---------3-----------------------#2024-04-29](https://towardsdatascience.com/transform-data-with-hyperbolic-sine-e39e9275b6ba?source=collection_archive---------3-----------------------#2024-04-29)

## *为什么处理负值应该是轻松的事*

[](https://medium.com/@david.kyle_13073?source=post_page---byline--e39e9275b6ba--------------------------------)[![David Kyle](../Images/536175491ed7f89d03a4e528a986bf8a.png)](https://medium.com/@david.kyle_13073?source=post_page---byline--e39e9275b6ba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e39e9275b6ba--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e39e9275b6ba--------------------------------) [David Kyle](https://medium.com/@david.kyle_13073?source=post_page---byline--e39e9275b6ba--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e39e9275b6ba--------------------------------) ·阅读时间：8分钟·2024年4月29日

--

![](../Images/f9953f39cfbd1e188cffa967e1745689.png)

图片由 [Osman Rana](https://unsplash.com/@osmanrana?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

许多模型对异常值非常敏感，例如 [线性回归](https://medium.com/swlh/how-outliers-can-pose-a-problem-in-linear-regression-1431c50a8e0)、[k近邻算法](/k-nearest-neighbors-knn-algorithm-23832490e3f4) 和 [ARIMA](/limitations-of-arima-dealing-with-outliers-30cc0c6ddf33)。机器学习算法在异常值的存在下容易过拟合，可能无法很好地推广。¹ 然而，适当的转换可以缩小这些极端值，从而提高模型的性能。

处理负值数据的转换包括：

1.  **平移对数**

1.  **平移Box-Cox**

1.  **逆双曲正弦**

1.  **双曲正弦-逆双曲正弦**

对数和Box-Cox是处理正值数据时的有效工具，但逆双曲正弦（arcsinh）在处理负值时要有效得多。

双曲正弦-逆双曲正弦更为强大。它有两个参数可以调整数据的*偏度*和*峰度*，使其更接近正态分布。这些参数可以通过梯度下降法推导出来。请参见本文末尾的Python实现。

# 平移对数

对数转换可以通过添加平移项*α*来适应负值的处理。

![](../Images/99c6c190ddd6505638f2678eb47efe0b.png)

在整篇文章中，我使用“log”表示自然对数。

从视觉上看，这是将对数的垂直渐近线从0移动到*α*。

![](../Images/b23b4614a839d9a61ce842cc05f28bee.png)

这是平移后的对数变换图，偏移值为*-5*，使用[*Desmos*](https://www.desmos.com/calculator)制作，遵循[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)协议。图中添加了方程式文本。

## 股票价格预测

假设你正在构建一个预测股市的模型。Hosenzade和Haratizadeh通过卷积神经网络解决这个问题，使用了我从[UCI欧文机器学习库](https://archive.ics.uci.edu/)²提取的大量特征变量。下面是成交量变化特征的分布——这是股市预测中的一个重要技术指标。

![](../Images/cb8996afed994306181c18ac2606825b.png)

使用Matplotlib制作

[分位数-分位数（QQ）图](/significance-of-q-q-plots-6f0c6e31c626)显示了右尾和左尾的重尾现象。我们变换的目标是将尾部拉近正态分布（红线），使其不再有离群值。

使用-250的平移值，我得到了这个对数分布。

![](../Images/9762e83d81f0bcc42223528b8d72e848.png)

右尾看起来稍微好一些，但左尾仍然偏离红线。对数变换通过对数据应用一个凹函数，压缩高值并拉伸低值，从而使数据左偏。

> **对数变换仅使右尾变轻。**

虽然这种方法对正偏态数据有效，但对于带有负离群值的数据效果较差。

![](../Images/c022841ba714b40e16e78b07f5e0bfae.png)

*使用* [*Desmos*](https://www.desmos.com/calculator) *制作，遵循[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)协议。图中添加了文本和箭头。*

在股票数据中，偏态不是问题。极值出现在左右两侧。*峰度*较高，意味着两侧的尾部都较重。一个简单的凹函数无法应对这种情况。

![](../Images/dd5ccbccc9db1cd0fcfc02d79bddc6b8.png)

# 移动的Box-Cox变换

Box-Cox是对数变换的广义版本，它也可以通过平移来处理负值，写作：

![](../Images/35718e7af8786adef4b75da40dd17dd0.png)

*λ*参数控制变换的凹度，使其能够呈现多种形式。当*λ* = 2时，Box-Cox变换为二次型；当*λ* = 1时，它是线性的；当*λ*接近0时，它趋近于对数变换。这可以通过使用L'Hôpital法则验证。

![](../Images/0cbf359a4587fca43a715f1fa106fbdc.png)![](../Images/672b9b7574cb3fc0e11b5d3c5d1cfd53.png)

这是平移后的Box-Cox变换图，平移值为*-5*，并使用了五个不同的*λ*值，使用[*Desmos*](https://www.desmos.com/calculator)制作，遵循[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)协议。图中添加了文本。

为了在我们的股价数据上应用这种变换，我使用平移值-250，并使用scipy的`boxcox`函数来确定*λ*。

```py
from scipy.stats import boxcox
y, lambda_ = boxcox(x - (-250))
```

变换后的数据如下所示：

![](../Images/4b2fcd3045616c56e3f19c0db0aa3ac8.png)

尽管这种变换具有灵活性，但它未能缩小股票价格数据的尾部。*λ*的低值使数据向左偏斜，缩小了右尾。*λ*的高值使数据向右偏斜，缩小了左尾，但没有任何值能同时缩小两者。

# 反双曲正弦

双曲正弦函数（sinh）的定义为

![](../Images/65ef55f997fba3e9932083583db2a288.png)

其反函数为

![](../Images/6be839af43c14014259a6e3e424fee59.png)

在这种情况下，反函数更为有用，因为它对于较大的*x*（无论是正数还是负数）大致是对数函数，而对于较小的*x*则是线性函数。实际上，这种变换缩小了极端值，同时保持了中心值基本不变。

> **Arcsinh减小了正负尾部。**

对于正值，arcsinh是凹的，而对于负值，它是凸的。这种曲率的变化是其能够同时处理正负极端值的秘诀。

![](../Images/15d87992438fa114983ffb31af99b23c.png)

与对数函数相比，反双曲正弦（arcsinh）的图像，*由* [*Desmos*](https://www.desmos.com/calculator) *制作，并在* [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) *下可用*。图像中添加了文本、箭头和框形。

使用这种变换后的股票数据结果显示，尾部几乎符合正态分布。新的数据没有异常值！

![](../Images/aa54a96575ef16c793face51f9fe5557.png)

## 尺度很重要

在将数据输入arcsinh之前，请考虑数据的尺度。

对于对数函数，单位的选择无关紧要。美元还是分，克还是千克，英里还是英尺——对对数函数来说都是一样的。输入值的尺度只会将变换后的值平移一个常数值。

![](../Images/3d27470fec2088c8429ee388b40f0239.png)

对于反双曲正弦（arcsinh），情况并非如此。-1到1之间的值几乎保持不变，而较大的数值则由对数主导。在将数据输入arcsinh之前，您可能需要调整不同的尺度和偏移量，以获得您满意的结果。

在文章的最后，我在python中实现了一个梯度下降算法，以更精确地估计这些变换参数。

![](../Images/237220229b36a2e0b15700f261721f04.png)

# Sinh-arcsinh

由Jones和Pewsey³提出，sinh-arcsinh变换为

![](../Images/c9d90b8fa9c77d4eec462f153ed2e90e.png)

Jones和Pewsey没有在前面包含常数项1/*δ*。不过，我在这里包含它，因为它使得展示arcsinh作为极限情况更加容易。

参数*ε*调整数据的偏斜度，*δ*调整峰度³，使得变换可以呈现多种形式。例如，恒等变换 *f(x) = x* 是当*ε* = 0且*δ* = 1时的sinh-arcsinh的特例。arcsinh是当*ε* = 0且*δ*趋近于零时的极限情况，使用L'Hôpital法则可以验证这一点。

![](../Images/8affeb48e27667cbb459a63d1d4d4e7a.png)![](../Images/07826aa87a3306c2ec7bdc9a39ed1f45.png)

sinh-arcsinh 变换在不同 *ε* 和 *δ* 值下的图。左侧 *ε* 固定为零，右侧 *δ* 固定为 0.5，使用* [*Desmos*](https://www.desmos.com/calculator) *制作，采用* [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) *许可。图中加入了文字*。

## 规模仍然重要

就像 arcsinh 一样，sinh-arcsinh 变换的结果会受到输入数据平移或缩放的显著影响，这意味着可以选择的不仅仅是两个，而是四个参数。

![](../Images/5b19c8b2798b2c1c7a1525d3ce501935.png)

# 参数估计

我们已经看到数据变换如何使数据的尾部更加符合高斯分布。现在，让我们通过选择最大化正态对数似然的参数，将其提升到一个新的层次。

设 *T(x)* 为我的变换，*N(x | μ, σ)* 为正态分布的概率密度函数，其中均值为 *μ*，标准差为 *σ*。假设独立，整个数据集 *X* 的似然是

![](../Images/342cd4d56e6394c8a91767cc4430d2e5.png)

这里我利用了变换的雅可比矩阵。对数似然是

![](../Images/aaee0bef4e435c9d8366a31fc8c08826.png)

我可以去掉绝对值符号，因为变换的导数始终是正的。

## 梯度下降

我使用梯度下降估计我的参数，将损失函数设置为负的平均对数似然。Tensorflow 的 `GradientTape` 会自动计算关于 sinh-arcsinh 四个参数以及正态分布的 *μ* 和 *σ* 的偏导数。为了确保它们保持正值，参数 β、*δ* 和 *σ* 以对数形式表示。你可能想尝试几个变量初始化，以防算法陷入局部最小值。我还建议在运行脚本前，将输入数据标准化为均值为零、标准差为一，以获得最佳性能。

```py
import tensorflow as tf
import numpy as np

@tf.function
def log_prob(x, params):
    # extract parameters
    alpha, log_beta = params[0], params[1]  # rescaling params
    beta = tf.math.exp(log_beta)
    epsilon, log_delta = params[2], params[3]  # transformation params
    delta = tf.math.exp(log_delta)
    mu, log_sigma = params[4], params[5]  # normal dist params
    sigma = tf.math.exp(log_sigma)
    # rescale
    x_scaled = (x - alpha)/beta
    # transformation
    sinh_arg = epsilon + delta * tf.math.asinh(x_scaled)
    x_transformed = (1/delta) * tf.math.sinh(sinh_arg)
    # log jacobian of transformation
    d_sinh = tf.math.log(tf.math.cosh(sinh_arg))
    d_arcsinh = - 0.5*tf.math.log(x_scaled**2 + 1)
    d_rescaling = - log_beta
    jacobian =  d_sinh + d_arcsinh + d_rescaling  # chain rule
    # normal likelihood
    z = (x_transformed - mu)/sigma  # standardized
    normal_prob = -0.5*tf.math.log(2*np.pi) - log_sigma -0.5*z**2
    return normal_prob + jacobian

# Learning rate and number of epochs
learning_rate = 0.1
epochs = 1000

# Initialize variables
tf.random.set_seed(892)
params = tf.Variable(tf.random.normal(shape=(6,), mean=0.0, stddev=1.0), dtype=tf.float32)

# Use the Adam optimizer
optimizer = tf.optimizers.Adam(learning_rate=learning_rate)

# Perform gradient descent
for epoch in range(epochs):
    with tf.GradientTape() as tape:
        loss = - tf.reduce_mean(log_prob(x_tf, params))

    # Compute gradients
    gradients = tape.gradient(loss, [params])

    # Apply gradients to variables
    optimizer.apply_gradients(zip(gradients, [params]))

    if (epoch % 100) == 0:
        print(-loss.numpy())

print(f"Optimal vals: {params}")
```

这种优化方法使得分布非常接近高斯分布——不仅是尾部，连中间部分也如此！

![](../Images/d06fde39c2703077445fa6b463fb2370.png)

# 结论

对于处理正值数据时，Log 和 Box-Cox 是强大的变换，但仅仅将这些变换平移以包括负值是有严重局限的。arcsinh 变换在同时处理极端正值和负值时要好得多。

如果你愿意增加复杂度，sinh-arcsinh 变换是一个更强大的函数，它是 arcsinh 的广义形式。当正态性非常重要时，其参数也可以通过梯度下降推导出来，以匹配高斯分布。

Arcsinh 变换不常被关注，但它是一个至关重要的变换，应该是每个数据工程师工具箱的一部分。

如果你发现这些转换技巧有用，或者对如何将它们应用到你自己的数据集有任何问题，请在下面的评论中分享你的想法和经验。

除非另有说明，所有图片均由作者提供。

***来自《Towards Data Science》编辑的提醒：*** *虽然我们允许独立作者根据我们的* [*规则和指南*](/questions-96667b06af5) *发布文章，但我们并不认同每位作者的贡献。你不应在没有寻求专业建议的情况下依赖作者的作品。详情请参见我们的* [*读者条款*](/readers-terms-b5d780a700a4) *。*

[1] Jabbar, H. K., & Khan, R. Z. (2014). [在监督机器学习中避免过拟合和欠拟合的方法（比较研究）](https://www.researchgate.net/publication/295198699_METHODS_TO_AVOID_OVER-FITTING_AND_UNDER-FITTING_IN_SUPERVISED_MACHINE_LEARNING_COMPARATIVE_STUDY)。

[2] [CNNpred: 基于CNN的股票市场预测，使用多样化的变量集](https://doi.org/10.48550/arXiv.1810.08923)。（2019）。 [UCI机器学习数据集](https://doi.org/10.24432/C55P70)。

[3] Jones, M & Pewsey, Arthur. (2009). [Sinh-arcsinh分布：一种广泛的家族，能够产生强有力的正态性和对称性检验](https://www.researchgate.net/publication/295198699_METHODS_TO_AVOID_OVER-FITTING_AND_UNDER-FITTING_IN_SUPERVISED_MACHINE_LEARNING_COMPARATIVE_STUDY)。Biometrika。
