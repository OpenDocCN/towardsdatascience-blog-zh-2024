# 核密度估计背后的数学

> 原文：[https://towardsdatascience.com/the-math-behind-kernel-density-estimation-5deca75cba38?source=collection_archive---------1-----------------------#2024-09-17](https://towardsdatascience.com/the-math-behind-kernel-density-estimation-5deca75cba38?source=collection_archive---------1-----------------------#2024-09-17)

## 探索核密度估计的基础、概念和数学原理

[](https://medium.com/@z.nay854?source=post_page---byline--5deca75cba38--------------------------------)[![Zackary Nay](../Images/b2eca451f39b1227ba868befe969f4ff.png)](https://medium.com/@z.nay854?source=post_page---byline--5deca75cba38--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5deca75cba38--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5deca75cba38--------------------------------) [Zackary Nay](https://medium.com/@z.nay854?source=post_page---byline--5deca75cba38--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5deca75cba38--------------------------------) ·14分钟阅读·2024年9月17日

--

![](../Images/7b02965740dfdbe0f77e416a5b6eb7e3.png)

核密度估计是一个基本的非参数方法，是揭示数据隐藏分布的多功能工具。本文深入探讨了估计器的数学基础，提供了选择最优带宽的指导，并简要触及了核函数的选择以及其他相关话题。

# **第1部分：简介**

假设我给你以下样本数据：

![](../Images/9d7b5704844ecc01c61ef6440a044011.png)

分析样本数据的第一个也是最简单的步骤之一是生成一个直方图，对于我们的数据，我们得到以下图形：

![](../Images/52fdb96422c868e48af5ae2cfcb9a638.png)

并不是非常有用，我们距离理解底层分布仍然很远。实际上，还有一个额外的问题，那就是数据很少表现出由直方图产生的那种锐利的矩形结构。核密度估计提供了解决方法，它是一种非参数方法，用于估计随机变量的概率密度函数。

![](../Images/815e4c24ba9ea67563e2a705b1e06d04.png)

```py
import numpy as np
import matplotlib.pyplot as plt
from sklearn.neighbors import KernelDensity

# Generate sample data
np.random.seed(14)
uniform_data = np.random.uniform(low=1, high=7, size=20)
normal_data = np.random.normal(loc=3, scale=0.7, size=20)

# Combine both distributions
combined_data = np.concatenate([uniform_data, normal_data])

# Compute histogram
hist, bin_edges = np.histogram(combined_data, bins=9, density=True)

# Compute KDE
kde = KernelDensity(kernel='gaussian').fit(combined_data[:, None])
x = np.linspace(min(combined_data), max(combined_data), 1000)[:, None]
log_density = kde.score_samples(x)
density = np.exp(log_density)

# Plot histogram
plt.hist(combined_data, bins=9, density=True, color='blue', edgecolor='black', label='Histogram')

# Plot KDE
plt.plot(x, density, color='red', label='KDE')

# Add labels and legend
plt.ylabel('Density')
plt.title('Histogram and KDE')
plt.legend()

# Show plot
plt.show()
```

# **第2部分：推导**

以下推导灵感来源于Bruce E. Hansen的《非参数讲义笔记》（2009年）。如果你有兴趣了解更多，可以参考他的原始讲义笔记[这里](https://users.ssc.wisc.edu/~bhansen/718/NonParametrics1.pdf)。

假设我们想从一组数据样本中估计一个概率密度函数 f(t)。一个好的起点是通过使用 [经验分布函数](https://en.wikipedia.org/wiki/Empirical_distribution_function#:~:text=The%20empirical%20distribution%20function%20is,to%20the%20Glivenko%E2%80%93Cantelli%20theorem.) (EDF) 来估计累积分布函数 F(t)。设 X1, …, Xn 为独立同分布的实值随机变量，其共同的累积分布函数为 F(t)。EDF 定义为：

![](../Images/124f9bd1a658b206a5279dfd27173ddd.png)

然后，根据大数法则，当 n 趋近于无穷大时，EDF 几乎必然收敛于 F(t)。现在，EDF 是一个阶梯函数，可能如下所示：

![](../Images/b8c9f6aac2ad35cb70daede61f52be29.png)

```py
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm

# Generate sample data
np.random.seed(14)
data = np.random.normal(loc=0, scale=1, size=40)

# Sort the data
data_sorted = np.sort(data)

# Compute ECDF values
ecdf_y = np.arange(1, len(data_sorted)+1) / len(data_sorted)

# Generate x values for the normal CDF
x = np.linspace(-4, 4, 1000)
cdf_y = norm.cdf(x)

# Create the plot
plt.figure(figsize=(6, 4))
plt.step(data_sorted, ecdf_y, where='post', color='blue', label='ECDF')
plt.plot(x, cdf_y, color='gray', label='Normal CDF')
plt.plot(data_sorted, np.zeros_like(data_sorted), '|', color='black', label='Data points')

# Label axes
plt.xlabel('X')
plt.ylabel('Cumulative Probability')

# Add grid
plt.grid(True)

# Set limits
plt.xlim([-4, 4])
plt.ylim([0, 1])

# Add legend
plt.legend()

# Show plot
plt.show()
```

因此，如果我们尝试通过取经验分布函数（EDF）的导数来找到 f(t) 的估计器，我们将得到一个 [狄拉克 delta 函数](https://en.wikipedia.org/wiki/Dirac_delta_function) 的加权和，这并没有太大帮助。相反，让我们考虑使用估计器的 t[二点中心差分公式](https://home.cc.umanitoba.ca/~farhadi/Math2120/Numerical%20Differentiation.pdf)作为导数的近似。对于一个小的 h>0，我们得到：

![](../Images/59ae71a5b9101204a61466fda298a3b7.png)

现在定义函数 k(u) 如下：

![](../Images/39d48ca6e7d2b0c4db071cd0eaddc43c.png)

然后我们得到：

![](../Images/130318df46d4d42879bfb8b4ef0d52e2.png)

这是核密度估计器的特例，其中 k 是均匀核函数。更一般地，核函数是一个从实数到实数的非负函数，满足以下条件：

![](../Images/d490e3f1e63f3b07459eb3f97598cde8.png)

我们假设本文讨论的所有核函数都是对称的，因此我们有 k(-u) = k(u)。

核的矩，它为核函数的形状和行为提供了洞察，定义如下：

![](../Images/3a624958e05b0beb451fdc6b1e39836a.png)

最后，核的阶数定义为第一个非零矩。

我们只能通过改变 h 值（带宽）或核函数来最小化核密度估计器的误差。带宽参数对结果估计的影响远大于核函数，但选择起来也更加困难。为了演示 h 值的影响，考虑以下两个核密度估计。使用高斯核来估计从标准正态分布生成的样本，两个估计器之间唯一的区别就是所选择的 h 值。

![](../Images/8202c10fcb27e1b851f069889558d274.png)

```py
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import gaussian_kde

# Generate sample data
np.random.seed(14)
data = np.random.normal(loc=0, scale=1, size=100)

# Define the bandwidths
bandwidths = [0.1, 0.3]

# Plot the histogram and KDE for each bandwidth
plt.figure(figsize=(12, 8))
plt.hist(data, bins=30, density=True, color='gray', alpha=0.3, label='Histogram')

x = np.linspace(-5, 5, 1000)
for bw in bandwidths:
    kde = gaussian_kde(data , bw_method=bw)
    plt.plot(x, kde(x), label=f'Bandwidth = {bw}')

# Add labels and title
plt.title('Impact of Bandwidth Selection on KDE')
plt.xlabel('Value')
plt.ylabel('Density')
plt.legend()
plt.show()
```

差异相当显著。

现在让我们看看在保持带宽不变的情况下改变核函数的影响。

![](../Images/c99738f2f355e32578811f64b2dd879c.png)

```py
import numpy as np
import matplotlib.pyplot as plt
from sklearn.neighbors import KernelDensity

# Generate sample data
np.random.seed(14)
data = np.random.normal(loc=0, scale=1, size=100)[:, np.newaxis]  # reshape for sklearn

# Intialize a constant bandwidth
bandwidth = 0.6

# Define different kernel functions
kernels = ["gaussian", "epanechnikov", "exponential", "linear"]

# Plot the histogram (transparent) and KDE for each kernel
plt.figure(figsize=(12, 8))

# Plot the histogram
plt.hist(data, bins=30, density=True, color="gray", alpha=0.3, label="Histogram")

# Plot KDE for each kernel function
x = np.linspace(-5, 5, 1000)[:, np.newaxis]
for kernel in kernels:
    kde = KernelDensity(bandwidth=bandwidth, kernel=kernel)
    kde.fit(data)
    log_density = kde.score_samples(x)
    plt.plot(x[:, 0], np.exp(log_density), label=f"Kernel = {kernel}")

plt.title("Impact of Different Kernel Functions on KDE")
plt.xlabel("Value")
plt.ylabel("Density")
plt.legend()
plt.show()
```

虽然在尾部视觉上有很大的差异，但不同核函数的估计器整体形状相似。因此，我将主要集中于为估计器找到最优带宽。现在，让我们探讨一些核密度估计器的性质，包括其偏差和方差。

# **第 3 部分：核密度估计器的性质**

我们需要利用的第一个事实是，估计器在实数线上的积分为 1。为了证明这一事实，我们将使用 u 代换：

![](../Images/e33cf2e2049c29aca18e8a093ca66c13.png)

运用 u 代换，我们得到以下结果：

![](../Images/9f3a57706737f576377668a492d07d83.png)

现在我们可以找到估计密度的均值：

![](../Images/ed503405029561f4a7976cd6a585ae03.png)

因此，估计密度的均值就是样本均值。

现在让我们找到估计量的第二阶矩。

![](../Images/bfe471ec7996859cc904c593b9a23c86.png)

然后我们可以找到估计密度的方差：

![](../Images/2c822ab90eee6dff0793fa3b7c7ddaa8.png)

为了找到最优带宽和核函数，我们将最小化估计量的均方误差。为此，我们首先需要找到估计量的偏差和方差。

随机变量 X 的期望值，具有概率密度函数 f，可以通过以下公式计算：

![](../Images/1675a80c94a4c62e844235a9ee73b25f.png)

因此，

![](../Images/3f155d13144fd99e7f129ce6610dc563.png)

其中 z 是一个虚拟的积分变量。然后我们可以使用变量代换得到以下结果：

![](../Images/c0a469d13dbaa87efedfbe3e45fac73f.png)

因此，我们得到以下结果：

![](../Images/33a5697549c9c8fc7aef8cc5cc5ac786.png)

然而，大多数情况下，上述积分无法解析求解。因此，我们将通过使用泰勒展开来近似 f(x+ hu)。作为提醒，f(x) 关于 a 的泰勒展开式为：

![](../Images/e21d33426eddb14757b89477692c5e9c.png)

因此，假设 f(x) 对 v+1 次项可微。对于 f(x+hu)，展开式为：

![](../Images/b0e29e10c63fa4e7688400f1b90e4c1e.png)

然后，对于 v 阶核函数，我们可以将表达式展开到 v’项：

![](../Images/e062240689da239fe38625a7e2a75c84.png)

其中最后一项是余项，当 h 趋近于 0 时，该项的消失速度比 h 快。现在，假设 k 是 v 阶核函数，我们得到：

![](../Images/f7854ca857959f4e956f84b66bff38be.png)

因此：

![](../Images/9618d12cb67889aa94553603e126194a.png)

因此，估计量的偏差为：

![](../Images/aeab30eaee8173e839d4b93f05452fbf.png)

方差的上界可以通过以下计算获得 [[Chen 2].](https://faculty.washington.edu/yenchic/17Sp_403/Lec7-density.pdf)

![](../Images/4082e2eee9c96f468b9e2bd8e068cdae.png)

其中：

![](../Images/349e7cdc90db769123596f25b306f7f0.png)

这个项也被称为粗糙度，可以表示为 R(k)。

最后，我们可以得到均方误差的表达式：

![](../Images/9c1d3c9c4559f6512c1fd4194a06d4ea.png)

AMSE 是渐近均方误差（Asymptotic Mean Squared Error）的缩写。我们可以最小化渐近均积分平方误差，该误差定义如下，

![](../Images/02ada6a1f661f96e4fbc20bc99bbf909.png)

以找到能带来最小误差的带宽（Silverman, 1986）：

![](../Images/c473fd6806cfa8781ba6b8a6a67c3ebb.png)

然后，通过将方程设置为 0 并简化，得到一个核函数的最优带宽为：

![](../Images/b95e30c3665889767077d44b94e3e699.png)

更为熟悉的表达式适用于二阶核，其中最小化 AMISE 的带宽为：

![](../Images/53599fe5eeb322d7a7b1003ac5a7276d.png)

然而，这个解法可能令人失望，因为我们需要知道我们正在估计的分布才能找到最优的带宽。实际上，如果我们一开始就使用核密度估计器，我们就不会知道这个分布。

# **第 4 部分：带宽选择**

尽管无法找到最小化均积分平方误差的带宽，但仍有几种方法可以在不知道基础分布的情况下选择带宽。**然而，值得注意的是，较大的 h 值会导致估计量的方差较小，但偏差较大，而较小的带宽会产生一个粗略的估计，偏差较小（Eidous 等，2010）。**

一些用于找到带宽的方法包括使用：

1: 解方程规则

2: 最小二乘交叉验证

3: 偏差交叉验证

4: 直接代入法

5: 对比法

值得注意的是，根据样本大小和数据的偏斜程度，选择带宽的“最佳”方法会有所不同。Python 中的大多数软件包允许你使用 Silverman 提出的法则，你可以直接代入某个分布（通常是正态分布）来表示 f，然后根据你选择的核函数计算带宽。这一过程被称为**Silverman 经验法则**，它为带宽提供了一个相对简单的估计。然而，它往往会高估带宽，导致较平滑的估计，方差较低，但偏差较大。Silverman 经验法则对于双峰分布的表现也不好，对于这种情况，有更精确的技术可供选择。

如果假设数据服从均值为 0 的正态分布，使用样本标准差，并应用 Epanechnikov 核（下文讨论），你可以通过以下方程使用经验法则选择带宽：

![](../Images/15398a17b0be9cacc757582fe73205c7.png)

Eidous 等人发现，与我列出的其他方法相比，对比法表现最佳。然而，这种方法有其缺点，因为它增加了需要选择的参数数量。

交叉验证方法是带宽选择的另一种不错选择，因为它通常会导致较小的偏差，但较大的方差（Heidenreich 等，2013）。它在寻找倾向于低估最佳带宽的带宽选择器时非常有用。为了避免这篇文章过长，我将只介绍普通最小二乘交叉验证方法。该方法对于较为弯曲的密度和样本量大约为 50 到 100 的情况效果较好。如果样本量非常小或非常大，[这篇论文](https://core.ac.uk/download/pdf/159147961.pdf)是一个很好的资源，可以帮助你找到另一种选择带宽的方法。如 Heidenreich 所指出的，“**选择哪个带宽选择器确实有很大不同；不仅在数值上，而且对密度估计的质量也有影响**”。

为了快速复习，当我们创建模型时，我们会保留一部分样本作为验证集，以查看模型在未训练的样本上的表现。K 折交叉验证通过将数据集分成 K 部分并训练模型 K 次，从而最小化可能选择错误测试集的影响，每一部分都会轮流作为验证集一次。

留一法交叉验证（Leave One Out Cross Validation）是 K 折交叉验证的极端形式，对于样本量为 n 的情况，我们训练模型 n 次，每次留出一个样本。[这里](https://machinelearningmastery.com/loocv-for-evaluating-machine-learning-algorithms/)有一篇文章详细探讨了这种方法在训练机器学习算法中的应用。

让我们回到 AMISE 表达式。我们将不再研究渐近平均积分平方误差，而是最小化平均积分平方误差（MISE）。首先，我们可以展开平方：

![](../Images/8d07064b850466ae87e1c1924a01c17d.png)

由于我们无法控制最后一项的期望值，因此我们可以尽力最小化前两项并舍弃第三项。然后，由于 X 是 E[X] 的无偏估计量，我们可以直接求得第一项：

![](../Images/5e82bb063a7a2eccf4342d6c403d000a.png)

然后，k 与 k 的卷积定义如下：

![](../Images/57fa4edb38fda6db5b996091bd9dd4df.png)

因此，

![](../Images/bba7957ebdfc234384c04e6ebd32959b.png)

因此，对于第一项，我们得到：

![](../Images/dfba3c5255b22e2d8c8b1b0a13996df6.png)

接下来，我们可以通过蒙特卡洛方法来逼近第二项。首先，正如前面讨论的，密度函数等于累积分布函数的导数，我们可以通过经验分布函数来逼近它。然后，积分可以通过估计量在[样本](https://jwmi.github.io/BMS/chapter5-monte-carlo.pdf)上的平均值来逼近。

![](../Images/f96f9260cac17a62a41b9d40be4bb682.png)

然后，最小二乘交叉验证选择器（LSCV）被定义为如下：

![](../Images/d4fc33d8d1ce23ecd2bd8b275c7012fc.png)![](../Images/f372f058f5adfa4cc5fd14285e1ffcbb.png)

我们得到最终定义的选择器如下：

![](../Images/2d69b80d31ae868cc3d74514a1728103.png)

最优带宽是最小化LSCV的h值，定义如下：

![](../Images/3088842523b4847cd1a05dd2477eb061.png)

LSCV(h)函数可能有多个局部最小值，因此你找到的最优带宽可能对选择的区间非常敏感。绘制该函数并直观地检查全局最小值所在位置是很有用的。

# **第五部分：最优内核选择**

如果我们使用的是二阶内核（这很常见），则内核选择比带宽选择要简单得多。**在最小化AMISE的标准下，Epanechnikov** **内核是最优的内核。**完整的证明可以在[Muler](https://projecteuclid.org/journals/annals-of-statistics/volume-12/issue-2/Smooth-Optimum-Kernel-Estimators-of-Densities-Regression-Curves-and-Modes/10.1214/aos/1176346523.full)的论文中找到。

![](../Images/cfe9062c4a6e3b26a7b94a474d190507.png)

还有一些内核与Epanechnikov内核一样高效，这些也在Muler的论文中有所提及。然而，我认为你不必过于担心内核函数的选择，带宽的选择要重要得多。

# **第六部分：进一步的主题与结论**

## 自适应带宽

提出的一种改进核密度估计器的方法是通过自适应带宽。自适应带宽会根据每个数据点的密度调整带宽，当样本数据的密度较低时增加带宽，密度较高时减少带宽。虽然理论上很有前景，但在一维情况下，自适应带宽的表现却非常差（[Terrel, Scott 1992](https://projecteuclid.org/journals/annals-of-statistics/volume-20/issue-3/Variable-Kernel-Density-Estimation/10.1214/aos/1176348768.full)）。虽然它在高维空间中可能表现更好，但在一维情况下，我认为坚持使用常数带宽是最好的选择。

## 多变量核密度估计

核密度估计器还可以扩展到更高维度，其中内核是径向基函数，或是多个内核函数的乘积。这种方法确实存在维度灾难的问题，随着维度的增加，所需数据点的数量会呈指数增长。它计算成本高昂，对于高维数据分析并不是一个理想的方法。

非参数多变量密度估计仍然是一个非常活跃的领域，[Masked Autoregressive Flow](https://arxiv.org/abs/1705.07057)似乎是一个全新且有前景的方法。

## 现实世界应用

核密度估计在各个学科中有着广泛的应用。首先，它已被证明能改善机器学习算法，例如在灵活的朴素贝叶斯分类器中。

它还被用于估算[交通事故密度](https://www.sciencedirect.com/science/article/pii/S2095756415305808)。该链接的论文使用KDE帮助构建模型，指示日本不同城市的交通事故风险。

另一个有趣的应用是在[地震学](https://pubs.geoscienceworld.org/ssa/bssa/article-abstract/86/2/353/120015/Kernel-estimation-methods-for-seismic-hazard-area)y中，KDE被用于建模不同地点的地震风险。

## 结论

核密度估计器是数据分析师工具箱中的一个优秀补充。虽然直方图无疑是分析数据的一种良好方式，它不依赖任何假设，但核密度估计器为单变量数据提供了一个稳固的替代方案。对于高维数据，或当计算时间是一个问题时，我建议考虑其他方法。尽管如此，KDE仍然是数据分析中直观、强大且多用途的工具。

*除非另有注明，所有图片均为作者提供。*
