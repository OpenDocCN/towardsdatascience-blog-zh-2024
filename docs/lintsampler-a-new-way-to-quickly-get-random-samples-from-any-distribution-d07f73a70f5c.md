# lintsampler：一种快速从任何分布中获取随机样本的新方法

> 原文：[https://towardsdatascience.com/lintsampler-a-new-way-to-quickly-get-random-samples-from-any-distribution-d07f73a70f5c?source=collection_archive---------8-----------------------#2024-10-14](https://towardsdatascience.com/lintsampler-a-new-way-to-quickly-get-random-samples-from-any-distribution-d07f73a70f5c?source=collection_archive---------8-----------------------#2024-10-14)

[](https://medium.com/@aneesh92?source=post_page---byline--d07f73a70f5c--------------------------------)[![Aneesh Naik](../Images/65aff47941ac2013ccbbbc15423e03a8.png)](https://medium.com/@aneesh92?source=post_page---byline--d07f73a70f5c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d07f73a70f5c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d07f73a70f5c--------------------------------) [Aneesh Naik](https://medium.com/@aneesh92?source=post_page---byline--d07f73a70f5c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d07f73a70f5c--------------------------------) ·阅读时间4分钟·2024年10月14日

--

`lintsampler`是一个纯Python包，可以轻松高效地从任何概率分布中生成随机样本。

完整披露：我是`lintsampler`的作者之一。

# 为什么你需要lintsampler

我们经常遇到需要从给定概率分布（PDF）中抽取随机样本的情况。例如，我们可能想估算一些总结性统计量，或者为模拟创建一群粒子。

如果概率分布是标准分布，如均匀分布或高斯（正态）分布，那么`numpy`/`scipy`生态系统提供了一些简便的方法来从这些分布中抽取样本，方法是通过`numpy.random`或`scipy.stats`模块。

然而，在实际应用中，我们经常遇到非高斯的概率分布。有时，它们*非常*非高斯。例如：

![](../Images/a816e849fa2d0c53df5dd8cde36582eb.png)

一个非常非高斯的PDF。等高线是密度相等的线，在对数空间中按相等间隔分隔。图片来源：作者。

我们如何从这个分布中抽取样本呢？

有几种广泛使用的技术可以从任意分布中抽取样本，比如[拒绝采样](https://en.wikipedia.org/wiki/Rejection_sampling)或[马尔可夫链蒙特卡罗（MCMC）](https://en.wikipedia.org/wiki/Markov_chain_Monte_Carlo)。这些都是优秀且可靠的方法，并且有一些方便的Python实现。例如，[emcee](https://emcee.readthedocs.io/en/stable/)是一个在科学应用中广泛使用的MCMC采样器。

现有技术的问题在于它们需要相当多的设置和调试。使用拒绝采样时，必须选择一个提议分布，而不合适的选择会使得过程非常低效。使用 MCMC 时，必须担心样本是否已收敛，这通常需要进行一些后验测试来评估。

输入 `lintsampler`。操作简单如同：

```py
from lintsampler import LintSampler
import numpy as np

x = np.linspace(xmin, xmax, ngrid)
y = np.linspace(ymin, ymax, ngrid)
sampler = LintSampler((x, y), pdf)
pts = sampler.sample(N=100000)
```

在这段代码中，我们沿着两个维度构造了 1D 数组，然后将它们与表示我们想从中抽取样本的概率分布的 `pdf` 函数一起传递给 `LintSampler` 对象（从 `lintsampler` 包中导入）。我们在这个片段中没有列出 `pdf` 函数，但在 [docs](https://lintsampler.readthedocs.io/en/latest/) 中有一些完全自包含的示例。

现在，`pts` 是一个包含来自 PDF 的 100000 个样本的数组。以下是它们的散点图：

![](../Images/d79dcd41533335c64d581654d00cf3ee.png)

从上面奇怪的 PDF 中采样的点的散点图（后者由轮廓线表示）。图片由作者提供。

本例的重点是演示如何轻松地设置和使用 `lintsampler`。在某些情况下，它也比 MCMC 和/或拒绝采样要快得多且高效。如果你有兴趣了解 `lintsampler` 背后的工作原理，请继续阅读。否则，可以访问 [docs](https://lintsampler.readthedocs.io/en/latest/)，那里有描述 [如何安装](https://lintsampler.readthedocs.io/en/latest/installation.html) 和使用 `lintsampler` 的说明，包括带有 [1D](https://lintsampler.readthedocs.io/en/latest/example_notebooks/1_gmm.html)、[2D](https://lintsampler.readthedocs.io/en/latest/example_notebooks/2_doughnuts.html) 和 [3D](https://lintsampler.readthedocs.io/en/latest/example_notebooks/3_dark_matter.html) 用例的示例笔记本，以及对一些 `lintsampler` 附加功能的描述： [准蒙特卡洛采样](https://lintsampler.readthedocs.io/en/latest/example_notebooks/4_qmc.html)（又称低差异序列）和在 [自适应树结构](https://lintsampler.readthedocs.io/en/latest/densitytree.html) 上的采样。此外，还有一篇发表于《开源软件杂志》(JOSS) 的 [论文](https://joss.theoj.org/papers/10.21105/joss.06906)，描述了 `lintsampler`。

# `lintsampler` 的工作原理

`lintsampler` 背后是我们称之为 *线性插值采样* 的算法。文档的 [理论](https://lintsampler.readthedocs.io/en/latest/theory/preamble.html) 部分提供了一个更详细且更具数学性的算法描述，但这里简要说明一下。

下面的示例演示了当你将 PDF 和网格传递给 `LintSampler` 类时，`lintsampler` 背后发生了什么。我们将以 2D 高斯分布的简单示例为例，但这种方法适用于任意维度，并且适用于更不友好的 PDF。

+   首先，在网格上评估概率密度函数。在下面的示例中，网格的间距不均匀，仅供娱乐。

![](../Images/3ea73a9e43dfcf1f8357735edfbacb2c.png)

左：2D 高斯概率密度函数（PDF）。右：在（不均匀）网格上评估的概率密度函数。图片来自作者。

+   通过这种方式在网格上评估概率密度函数后，我们可以根据梯形法则估算每个网格单元的总概率（即，单元的体积乘以其角点密度的平均值）。

+   在每个网格单元内，我们可以通过[双线性插值](https://en.wikipedia.org/wiki/Bilinear_interpolation)在单元角点之间近似概率密度函数：

![](../Images/d349e131a17ba10837746ab5363527d6.png)

使用（双）线性插值填充的网格化概率密度函数。图片来自作者。

+   这种对概率密度函数的线性近似可以非常高效地进行采样。绘制一个单一的样本是一个两步过程，如下图所示。首先，从按概率加权的单元列表中选择一个随机单元（左侧面板）。接下来，通过[逆变换采样](https://en.wikipedia.org/wiki/Inverse_transform_sampling)在单元内采样一个点（右侧面板）。

![](../Images/abf2821331ada9ab62cafcd622eb73e0.png)

左：与前一图相同，随机选择的单元被高亮显示。右：高亮单元的放大图，显示采样点。图片来自作者。

值得理解的是，这里的关键步骤是线性近似：我们将在`lintsampler`文档中描述这一点，以及逆变换采样过程的更多细节。将概率密度函数近似为每个网格单元内的线性函数意味着它具有封闭的、解析的形式用于其[分位数函数](https://en.wikipedia.org/wiki/Quantile_function)（即其逆累积分布函数），这意味着进行逆变换采样本质上归结为绘制均匀样本并对其应用代数函数。

用户需要关心的主要问题是获得合适的网格分辨率，以便线性近似足够准确。什么是合适的分辨率会因使用案例而异，正如在`lintsampler`文档中的一些示例笔记本所展示的那样。

祝你采样愉快！
