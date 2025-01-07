# 赫克曼选择偏差建模在因果研究中的应用

> 原文：[https://towardsdatascience.com/heckman-selection-bias-modeling-in-causal-studies-30e207987025?source=collection_archive---------9-----------------------#2024-08-14](https://towardsdatascience.com/heckman-selection-bias-modeling-in-causal-studies-30e207987025?source=collection_archive---------9-----------------------#2024-08-14)

## 选择偏差与OLS的识别假设之间的关系，以及应采取哪些步骤来解决它

[](https://medium.com/@pollak.daniel?source=post_page---byline--30e207987025--------------------------------)[![Daniel Pollak](../Images/a48f0aa944aeb4189e75cfc99949b4a7.png)](https://medium.com/@pollak.daniel?source=post_page---byline--30e207987025--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--30e207987025--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--30e207987025--------------------------------) [Daniel Pollak](https://medium.com/@pollak.daniel?source=post_page---byline--30e207987025--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--30e207987025--------------------------------) ·9分钟阅读·2024年8月14日

--

![](../Images/8ca5a683283586a11d43d54ab03986aa.png)

图片来源：[Dimitry B](https://unsplash.com/@dimitry_b?utm_source=medium&utm_medium=referral) 来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在我的应用研究过程中，我曾经很难理解选择偏差和样本偏差问题的复杂性。这些问题以多种形式出现，可能源于不同因素，并且会影响因果模型中的外部和内部有效性。此外，它们常常是语义混淆的根源。

在处理线性因果模型中的偏差和不一致性时，理解**遗漏变量问题**是一个基础概念。当一个通常未被观察到的随机变量与自变量和模型误差之间存在相关性时，就会发生这个问题。在估计线性模型时，如果未考虑这个变量，就会导致估计值存在偏差。因此，这个问题会妨碍我们从因变量的变化中隔离出由自变量变化引起的方差，从而掩盖二者之间的真实因果关系。

![](../Images/e2ffe873e11bc0eac0f1fb74cc3ca058.png)

因果DAG中的混杂变量

这些概念是相关的吗？选择偏差可以被认为是遗漏变量问题的一种形式吗？让我们深入探讨一下！

# 背景

我想阐明一些基本要素，以便充分理解选择偏差如何影响我们线性模型的估计过程。我们有一个因变量Y，假设它与另一个变量X（即自变量）之间存在一个线性关系（存在一些误差项）。

## 识别假设

给定人群变量Y、X的子样本Y'、X' -

1.  误差项（**原始模型的误差项!!!**）与X'不相关。

1.  误差项的均值为零。

1.  Y和X之间确实存在线性关系——

![](../Images/9a4c58ead8f2e2f9a2e5d8b01f5e4722.png)

需要注意的是，在实证研究中，我们观察到X和Y（或它们的子样本），但**我们无法观察到误差项**，这使得假设（1）无法直接测试或验证。在这一点上，我们通常依赖理论解释来证明这个假设。常见的解释是通过随机对照试验（RCTs），在其中，子样本X完全随机地收集，确保它与任何其他变量，特别是与误差项之间没有相关性。

## 条件期望

基于之前提到的假设，我们可以准确确定给定X的Y的条件期望的形式——

![](../Images/675b7d031b44f15b10d8266152485914.png)

线性模型中的条件期望

最后的过渡来源于识别假设。需要注意的是，这个是x的函数，这意味着它表示在x等于特定值的条件下，所有观察到的y值的平均值（或者是给定x值的小范围内，y的局部平均值——更多信息可以在[这里](https://theeffectbook.net/ch-DescribingRelationships.html#conditional-means)找到）

## 最小二乘法（OLS）

给定符合识别假设的X样本，已知最小二乘法（OLS）方法为线性模型参数（alpha和beta）提供了一个封闭形式的解，从而也为给定X的Y的条件期望函数提供了一个一致且无偏的估计。

从本质上讲，OLS是一种**拟合线性线**（或者在多元样本的情况下是拟合线性超平面）到一组（y_i, x_i）对的技术。OLS特别有趣的是——

1.  如果Y和X之间存在线性关系（考虑经典误差项），我们已经看到，给定X的Y的条件期望是完全线性的。在这种情况下，OLS能够以强大的统计精度有效地揭示这个函数。

1.  即使是满足之前讨论的识别假设的**任何子样本**，OLS也能够实现这一点——前提是样本足够大。

# 动机

让我们从一个简单的示例开始，使用模拟数据。我们将模拟上述的线性模型。

使用模拟数据的一个显著优势是，它使我们能够更好地理解在现实世界场景中无法观察到的变量之间的关系，例如模型中的误差项。

```py
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import statsmodels.api as sm

N = 5000
BETA = 0.7
INTERCEPT = -2
SIGMA = 5

df = pd.DataFrame({
    "x": np.random.uniform(0, 50, N),
    "e": np.random.normal(0, SIGMA, N)
})
df["y"] = INTERCEPT + BETA * df["x"] + df["e"]
```

并对全样本运行OLS -

```py
r1 = sm.OLS(df["y"], sm.add_constant(df["x"])).fit()
plt.scatter(df["x"], df["y"], label="population")
plt.plot(df["x"], r1.fittedvalues, label="OLS Population", color="r")
```

![](../Images/d8e5548a623c71f3021dd30046846b0c.png)

现在，让我们从我们的人群中生成一个随机的子样本X，并对这个子样本应用OLS。我将从之前生成的500个样本中随机选择100个x，然后对这个子集运行OLS。

```py
sample1 = df.sample(100)
r2 = sm.OLS(sample1["y"], sm.add_constant(sample1["x"])).fit()

plt.scatter(sample1["x"], sample1["y"], label="sample")
plt.plot(sample1["x"], r2.fittedvalues, label="OLS Random sample")
```

和图表 -

![](../Images/9ea78ac3e7ba238e593e06adf5b3ebc5.png)

看起来我们为随机子样本获得了一致的估计量，因为两种OLS结果产生了非常相似的条件期望线。此外，你可以观察到X和误差项之间的相关性——

```py
print(f"corr {np.corrcoef(df['x'], df['e'])}")
print(f"E(e|x) {np.mean(df['e'])}")

# corr [[ 1\.         -0.02164744]
#  [-0.02164744  1\.        ]]
# E(e|x) 0.004016713100777963
```

这表明识别假设得到了满足。然而，实际上我们无法直接计算这些，因为误差是不可观察的。现在，让我们创建一个新的子样本——我将选择所有(y, x)对，其中y ≤ 10：

```py
sample2 = df[df["y"] <= 10]
r3 = sm.OLS(sample2["y"], sm.add_constant(sample2["x"])).fit()

plt.scatter(sample2["x"], sample2["y"], label="Selected sample")
plt.plot(sample["x"], r3.fittedvalues, label="OLS Selected Sample")
```

我们得到的是——

![](../Images/92fb9d05ee88537e6a2028ba330184bf.png)

现在，OLS给出了一个完全不同的直线。让我们检查一下子样本X和误差之间的相关性。

```py
print(f"corr {np.corrcoef(df['x'], df['e'])}")
print(f"E(e|x) {np.mean(df['e'])}")

# corr [[ 1\.         -0.48634973]
#  [-0.48634973  1\.        ]]
# E(e|x) -2.0289245650303616
```

看起来识别假设被违反了。我们还可以将子样本的误差项绘制成X的函数——

![](../Images/83fcaf6e5c9f1ff6b6ac3f022ee00d26.png)

如你所见，随着X的增加，大误差变少，表明存在明显的相关性，导致OLS估计量有偏且不一致。

让我们进一步探索这个问题。

# 建模

那么，这里到底发生了什么？

我将引用詹姆斯·赫克曼提出的模型，他与丹尼尔·麦克法登共同获得了2000年诺贝尔经济学奖。赫克曼因其在计量经济学和微观经济学方面的开创性工作而闻名，尤其是在定量分析中处理选择偏差和自我选择方面的贡献。他著名的赫克曼修正将在后文中讨论。

在他1979年的论文《样本选择偏误作为规格误差》中，赫克曼阐述了如何通过截尾依赖变量来引发选择偏误——这是一种选择的特殊情况，可以扩展到更多非随机样本选择过程。

对依赖变量进行截尾处理正是我们在上一节创建最后一个子样本时所做的。让我们来看看赫克曼的框架。

我们从一个完整的样本（或总体）开始，包含(y_i, x_i)对。在这种情况下，给定x_i，ε_i可以变化——它可以是正的、负的、小的或大的，这完全取决于误差分布。我们将这个完整的依赖变量样本称为y*。然后，我们将y定义为截尾的依赖变量，它只包括我们实际观察到的值。

![](../Images/4d2158cfcf3d7fc86a1652cc5430bee3.png)

现在，让我们计算截尾变量y的条件期望：

![](../Images/b1b77e1aa939cf4b1452c3d4aeb796fc.png)

如你所见，这个函数与我们之前看到的函数相似，但它包含了一个额外的项，这与之前不同。这个最后的项不能被忽视，这意味着条件期望函数**不是纯线性的**，它与x有关（并伴有一定的噪声）。因此，在未截尾的值上运行OLS将产生对α和β的有偏估计。

此外，该方程说明了选择偏误问题如何被看作是一个**遗漏变量**问题。由于最后一项依赖于X，因此它与因变量共享大量的方差。

# 赫克曼修正

## **逆米尔斯比率**

赫克曼修正方法基于以下原理：给定一个随机变量Z，它遵循均值为μ，标准差为σ的正态分布，以下方程适用：

![](../Images/bee5544cf6b47d5414ca07ac6945e0b2.png)

给定任何常数α，Φ（大写的phi）表示标准正态分布的累积分布函数（CDF），而ɸ表示标准正态分布的概率密度函数（PDF）。这些值被称为**逆米尔斯比率**。

那么，这对我们有什么帮助呢？让我们重新审视前一个条件期望方程的最后一项—

![](../Images/6a0c28909cde6017e96ff61941315479.png)

结合我们的误差项遵循正态分布这一事实，我们可以使用逆米尔斯比率来刻画它们的行为。

## 回到我们的模型

逆米尔斯比率的优点在于，它将之前的条件期望函数转换为以下形式——

![](../Images/34182c74fe884cfa79f029dbb26060db.png)

这导致了一个线性函数，其中包含了一个额外的协变量——逆米尔斯比率。因此，为了估计模型参数，我们可以将OLS应用于这个修订后的公式。

首先计算逆米尔斯比率—

![](../Images/3080cc31de48d31065b1b2686b2c64b4.png)

并在代码中：

```py
from scipy.stats import norm

sample["z"] = (CENSOR-INTERCEPT-BETA*sample["x"])/SIGMA
sample["mills"] = -SIGMA*(norm.pdf(sample["z"])/(norm.cdf(sample["z"])))
```

并进行OLS回归—

```py
correcred_ols = sm.OLS(sample["y"], sm.add_constant(sample[["x", "mills"]])).fit(cov_type="HC1")
print(correcred_ols.summary())
```

以及输出结果—

```py
 OLS Regression Results                            
==============================================================================
Dep. Variable:                      y   R-squared:                       0.313
Model:                            OLS   Adj. R-squared:                  0.313
Method:                 Least Squares   F-statistic:                     443.7
Date:                Mon, 12 Aug 2024   Prob (F-statistic):          3.49e-156
Time:                        16:47:01   Log-Likelihood:                -4840.1
No. Observations:                1727   AIC:                             9686.
Df Residuals:                    1724   BIC:                             9702.
Df Model:                           2                                         
Covariance Type:                  HC1                                         
==============================================================================
                 coef    std err          z      P>|z|      [0.025      0.975]
------------------------------------------------------------------------------
const         -1.8966      0.268     -7.088      0.000      -2.421      -1.372
x              0.7113      0.047     14.982      0.000       0.618       0.804
mills          1.0679      0.130      8.185      0.000       0.812       1.324
==============================================================================
Omnibus:                       96.991   Durbin-Watson:                   1.993
Prob(Omnibus):                  0.000   Jarque-Bera (JB):              115.676
Skew:                          -0.571   Prob(JB):                     7.61e-26
Kurtosis:                       3.550   Cond. No.                         34.7
==============================================================================
```

## 在现实中

α和β是我们希望估计的模型中未观察到的参数，因此在实际操作中，我们无法像之前那样直接计算逆米尔斯比率。赫克曼在他的修正方法中引入了一个初步步骤，以帮助估计逆米尔斯比率。这就是为什么赫克曼修正被称为**双阶段估计量**。

回顾一下，我们的问题是我们无法观察到因变量的所有值。例如，如果我们正在研究教育（Z）如何影响工资（Y），但只观察到超过某一阈值的工资，那么我们需要为那些工资低于该阈值的个体的教育水平制定理论解释。一旦我们有了这个解释，我们就可以估计一个[probit](https://en.wikipedia.org/wiki/Probit)模型，形式如下：

![](../Images/8d7d7775e31a6d62647800c4a63ec600.png)

并使用这个probit模型的估计参数来计算逆米尔斯比率的估计量。在我们的案例中（注意我没有使用α和β）—

```py
from statsmodels.discrete.discrete_model import Probit

pbit = Probit(df["y"] <= CENSOR, sm.add_constant(df["x"])).fit()
sample["z_pbit"] = sample["z"] = (pbit.params.const + sample["x"]*pbit.params.x)
sample["mills_pbit"] = -SIGMA*(norm.pdf(sample["z_pbit"])/(norm.cdf(sample["z_pbit"])))
correcred_ols = sm.OLS(sample["y"], sm.add_constant(sample[["x", "mills_pbit"]])).fit(cov_type="HC1")
```

同样，第二阶段的OLS给我们提供了一致的估计量—

```py
 OLS Regression Results                            
...
==============================================================================
                 coef    std err          z      P>|z|      [0.025      0.975]
------------------------------------------------------------------------------
const         -1.8854      0.267     -7.068      0.000      -2.408      -1.363
x              0.7230      0.049     14.767      0.000       0.627       0.819
mills_pbit     1.1005      0.135      8.165      0.000       0.836       1.365
==============================================================================
```

# 总结

我们使用模拟数据展示了由于对因变量值进行删选而导致的样本选择偏差问题。我们通过检查模型的模拟误差和偏倚子样本，探讨了该问题如何与OLS因果识别假设相关。最后，我们介绍了赫克曼方法来修正这种偏差，从而使我们即使在使用偏倚样本时，也能获得一致且无偏的估计量。

如果你喜欢这个故事，我将非常感激你的支持——[请为我买杯咖啡，这对我来说意义重大！](https://ko-fi.com/dapollak)

# 参考文献

[1] James J. Heckman, [样本选择偏差作为规格错误](https://www.jstor.org/stable/1912352)（1979年），《计量经济学》

[2] Nick Huntington-Klein, [《效应》](https://theeffectbook.net/index.html) 书籍（2022年）

[3] Christopher Winship, [样本偏差的模型](https://typeset.io/pdf/models-for-sample-selection-bias-4qnrg4cc1f.pdf)（1992年）

*除非另有说明，所有图片均由作者提供*
