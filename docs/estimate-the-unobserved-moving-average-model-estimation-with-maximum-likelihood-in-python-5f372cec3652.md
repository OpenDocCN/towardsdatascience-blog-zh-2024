# 估计未观测的：使用最大似然法在Python中估计移动平均模型

> 原文：[https://towardsdatascience.com/estimate-the-unobserved-moving-average-model-estimation-with-maximum-likelihood-in-python-5f372cec3652?source=collection_archive---------11-----------------------#2024-06-28](https://towardsdatascience.com/estimate-the-unobserved-moving-average-model-estimation-with-maximum-likelihood-in-python-5f372cec3652?source=collection_archive---------11-----------------------#2024-06-28)

## 如何使用最大似然估计（MLE）估计未观测协变量的系数

[](https://medium.com/@pollak.daniel?source=post_page---byline--5f372cec3652--------------------------------)[![Daniel Pollak](../Images/a48f0aa944aeb4189e75cfc99949b4a7.png)](https://medium.com/@pollak.daniel?source=post_page---byline--5f372cec3652--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5f372cec3652--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5f372cec3652--------------------------------) [Daniel Pollak](https://medium.com/@pollak.daniel?source=post_page---byline--5f372cec3652--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5f372cec3652--------------------------------) ·阅读时间：8分钟·2024年6月28日

--

![](../Images/300647241e2591ade3f90bbd02c13b0f.png)

图片由[Connor Naasz](https://unsplash.com/@cjnaasz?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

对于有时间序列数据和预测经验的人来说，回归、AR、MA和ARMA等术语应该不陌生。线性回归是一个简单的模型，具有封闭形式的参数解，可以通过最小二乘法（OLS）获得。AR模型也可以通过OLS进行估计。然而，MA模型的情况更为复杂，它构成了更高级的ARMA和ARIMA模型的第二个组成部分。

本文计划：

1.  介绍移动平均模型

1.  讨论为何MA模型没有封闭解

1.  介绍最大似然估计方法

1.  MA(1) 最大似然估计——理论与Python代码

# 移动平均模型

MA模型可以通过以下公式描述：

![](../Images/d8b50b75af780962a4fd28205f7e819b.png)

方程 1：MA(q) 公式

这里，θ（theta）表示模型参数，而ε（epsilon）是误差项，假定它们是互相独立且服从常方差的正态分布。这个公式背后的直觉是，我们的时间序列X总是可以通过系列中最后q个冲击来描述。从公式可以明显看出，每个冲击只影响后续的q个X值，这与AR模型不同，在AR模型中，冲击的影响会持续下去，尽管会随着时间逐渐减弱。

## 简单模型的封闭估计——线性回归

提醒一下，线性回归方程的一般形式如下：

![](../Images/05e531f5319c4f008477de2efa698218.png)

方程 2：一般线性回归公式

对于预测任务，我们通常的目标是使用一组x和y的样本来估计所有模型的参数（beta）。根据我们对模型的一些假设，**高斯-马尔科夫定理**指出，普通最小二乘法（OLS）对betas的估计具有在所有线性无偏估计量中最低的抽样方差。简单来说，OLS为我们提供了betas的最佳估计。

那么，OLS是什么？它是一个闭式解，用于损失函数最小化问题：

![](../Images/44114c60ef9374efc409c10735aedd51.png)

方程 3：OLS最小化方程

其中，损失函数S定义如下 -

![](../Images/46e61adacddd09c1deaca8c27e0ac1a1.png)

方程 4：OLS损失函数

在这个背景下，y和X是我们的样本数据，是**可观察**的数字向量（如时间序列）。因此，计算函数S，求出其导数，并找到解决最小化问题的beta是直接的。

## MA(q)的闭式估计

应该很清楚，为什么像OLS这样的估计方法应用于MA(q)模型是有问题的——因变量，即时间序列值，是由**不可观察**的变量（epsilons）描述的。这就引出了一个问题：这些模型究竟如何进行估计呢？

# 最大似然估计（MLE）

## 似然函数

统计分布通常依赖于一个或多个参数。例如，正态分布由其均值和方差来表征，这些参数定义了它的“高度”和“质心”—

![](../Images/ece1c8b44d8345a842f0ef08a6bfcb99.png)

正态分布来自于[维基百科](https://en.wikipedia.org/wiki/Normal_distribution)

假设我们有一个数据集X={x_1,…x_n}，由从一个未知的正态分布中抽取的样本组成，且其参数未知。我们的目标是确定那些最能描述我们数据集X的正态分布的均值和方差值，这些均值和方差值使得我们的数据集X最**可能**是从该正态分布中抽样得到的。

最大似然估计（MLE）提供了一个框架，可以精确解决这个问题。它引入了一个似然函数，这是一个输出另一个函数的函数。这个似然函数接受一个参数向量，通常用theta表示，并生成一个依赖于theta的**概率密度函数**（PDF）。

![](../Images/90bb1564ef187f89653b8e970dcbf7a9.png)

似然函数的一般定义

一个分布的概率密度函数（PDF）是一个函数，它接受一个值x，并返回该值在分布中的概率。因此，似然函数通常表示如下：

![](../Images/456f9612c7bf6b89851084577631fd3d.png)

给定x，似然作为theta的函数

该函数的值表示**从由PDF定义的分布中，观察到x的似然性，假设theta是其参数**。

## 目标

构建预测模型时，我们有数据样本和一个参数化的模型，我们的目标是估计模型的参数。在我们的例子中，例如回归模型和MA模型，这些参数是各自模型公式中的系数。

![](../Images/a686eaf98592b3337da346b90ecf6f4e.png)

统计模型估计过程

MLE中的等效概念是，我们有观察值和一个定义在一组未知且不可直接观察的参数theta上的分布的PDF。**我们的目标是估计theta**。

MLE方法包括寻找一组参数theta，使得在给定可观察数据x的情况下，似然函数达到最大值。

![](../Images/6fa203b5d5c3e4913ca968a43172b6d8.png)

似然函数的最大化

我们假设我们的样本x是从一个已知PDF的分布中抽取的，该PDF依赖于一组参数theta。这意味着，在该PDF下观察到x的似然性（概率）本质上是1。因此，识别使得我们的似然函数值在样本上接近1的theta值，应该揭示出真实的参数值。

## 条件似然

请注意，我们并没有对似然函数所依据的分布（PDF）做出任何假设。现在，假设我们的观察值X是一个向量（x_1, x_2, …, x_n）。我们将考虑一个概率函数，表示在已经观察到（x_1, x_2, …, x_{n-1})的条件下，观察到x_n的概率——

![](../Images/b628eb50e4fbb5553662921a658cd9b7.png)

这表示在已知前面观察值的条件下，仅观察x_n的似然性（以及theta，参数集合）。现在，我们将条件似然函数定义如下：

![](../Images/d432a00759163e07d59afd0e2c528354.png)

条件似然函数

稍后我们将看到，为什么使用条件似然函数而不是精确似然函数是有用的。

## 对数似然

在实际应用中，通常使用似然函数的自然对数，称为对数似然函数：

![](../Images/43e35a0a64b76e0493f71695bb840b3d.png)

最大化对数似然函数

这样更方便，因为我们通常处理的似然函数是一个独立变量的联合概率函数，这等同于每个变量概率的乘积。取对数后，将这个乘积转换为和。

# 使用MLE估计MA(1)

为了简化，我将演示如何估计最基本的移动平均模型——MA(1)：

![](../Images/2cb70a6bc4250b8adbad548dd8b753bb.png)

MA(1)模型

这里，x_t表示时间序列观测值，alpha和beta是待估计的模型参数，epsilon是从均值为零且方差为sigma的正态分布中抽取的随机噪声，sigma也将被估计。因此，我们的“theta”是(alpha, beta, sigma)，我们要估计的正是这些参数。

让我们定义我们的参数并使用 Python 生成一些合成数据：

```py
import pandas as pd
import numpy as np

STD = 3.3
MEAN = 0
ALPHA = 18
BETA = 0.7
N = 1000

df = pd.DataFrame({"et": np.random.normal(loc=MEAN, scale=STD, size=N)})
df["et-1"] = df["et"].shift(1, fill_value=0)
df["xt"] = ALPHA + (BETA*df["et-1"]) + df["et"]
```

请注意，我们已将误差分布的标准差设置为 3.3，alpha 设置为 18，beta 设置为 0.7。数据大致如下所示 —

![](../Images/3db379dc985a0e08595c01c315be070e.png)

MA(1) 数据生成过程的模拟

## MA(1) 的似然函数

我们的目标是构建一个似然函数来回答这个问题：假设我们的时间序列 X=(x_1, …, x_n) 是由前面描述的 MA(1) 过程生成的，那么观察到这些数据的可能性有多大？

![](../Images/947345b1be99350bbb24f5c8ea3a1762.png)

观察 X 的似然

计算这个概率的挑战在于我们样本之间的相互依赖 —— 这从 x_t 和 x_{t-1} 都依赖于 e_{t-1} 的事实中可以看出 —— 使得计算所有样本的联合概率（即精确似然）变得非同寻常。

因此，如前所述，我们将不计算精确似然，而是使用条件似然。让我们从给定所有前述样本的情况下观察单个样本的似然开始：

![](../Images/c0bb56d8d620460ed195d16df8b9ffc2.png)

给定其余样本，观察 x_n 的条件似然

这计算起来要简单得多，因为 —

![](../Images/d6f46649792a3912f04dfbbfd1cdf268.png)![](../Images/be8c97e8745aec7ec37422597408f555.png)

正态分布的概率密度函数

剩下的就是计算观察所有样本的条件似然：

![](../Images/90b57b8dca8c17a5890715d0796b95dd.png)

应用自然对数得：

![](../Images/83615610be05471262a0d06a459e9076.png)

最终的似然函数需要最大化

这是我们应该最大化的函数。

## 代码

我们将使用来自 statsmodels 的 `GenericLikelihoodModel` 类来实现我们的最大似然估计（MLE）。如 statsmodels 网站上的[教程](https://www.statsmodels.org/dev/examples/notebooks/generated/generic_mle.html)所述，我们只需继承这个类并包括我们的似然函数计算：

```py
from scipy import stats
from statsmodels.base.model import GenericLikelihoodModel
import statsmodels.api as sm

class MovingAverageMLE(GenericLikelihoodModel):
    def initialize(self):
        super().initialize()
        extra_params_names = ['beta', 'std']
        self._set_extra_params_names(extra_params_names)

        self.start_params = np.array([0.1, 0.1, 0.1])

    def calc_conditional_et(self, intercept, beta):
        df = pd.DataFrame({"xt": self.endog})
        ets = [0.0]
        for i in range(1, len(df)):
            ets.append(df.iloc[i]["xt"] - intercept - (beta*ets[i-1]))

        return ets

    def loglike(self, params):
        ets = self.calc_conditional_et(params[0], params[1])
        return stats.norm.logpdf(
            ets,
            scale=params[2],
        ).sum()
```

函数 `loglike` 是实现的关键。给定迭代的参数值 `params` 和依赖变量（在此为时间序列样本），这些变量作为类成员 `self.endog` 存储，它计算条件对数似然值，正如我们之前讨论的那样。

现在让我们创建模型并拟合我们的模拟数据：

```py
df = sm.add_constant(df) # add intercept for estimation (alpha)
model = MovingAverageMLE(df["xt"], df["const"])
r = model.fit()
r.summary()
```

输出结果为：

![](../Images/9e4512927b243a89efd88794d665358f.png)

来自 Python 的最大似然估计（MLE）结果

就这样！如演示所示，最大似然估计成功地估算了我们为模拟选择的参数。

# 总结

即使是估计一个简单的 MA(1) 模型，也能展示这种方法的强大功能，它不仅能高效地利用我们的数据，而且为理解和解释时间序列数据的动态提供了坚实的统计基础。

希望你喜欢这个内容！

# 参考文献

[1] Andrew Lesniewski, [时间序列分析](https://mfe.baruch.cuny.edu/wp-content/uploads/2014/12/TS_Lecture1_2019.pdf), 2019, 巴鲁克学院，纽约

[2] Eric Zivot, [ARMA模型的估计](https://faculty.washington.edu/ezivot/econ584/notes/armaestimation.pdf), 2005

*除非另有说明，所有图片均由作者提供*
