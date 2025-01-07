# 检测社交媒体流量时间序列中的异常

> 原文：[https://towardsdatascience.com/detecting-anomalies-in-social-media-volume-time-series-9cae614a11d0?source=collection_archive---------5-----------------------#2024-11-11](https://towardsdatascience.com/detecting-anomalies-in-social-media-volume-time-series-9cae614a11d0?source=collection_archive---------5-----------------------#2024-11-11)

## **我如何检测社交媒体流量中的异常：一种基于残差的方法**

[](https://medium.com/@lorenzo.mezzini?source=post_page---byline--9cae614a11d0--------------------------------)[![Lorenzo Mezzini](../Images/e4df2ae1cd2e9bfde4c7fd636781de74.png)](https://medium.com/@lorenzo.mezzini?source=post_page---byline--9cae614a11d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9cae614a11d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9cae614a11d0--------------------------------) [Lorenzo Mezzini](https://medium.com/@lorenzo.mezzini?source=post_page---byline--9cae614a11d0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9cae614a11d0--------------------------------) ·7分钟阅读·2024年11月11日

--

![](../Images/aa6c46de491ac7955aced3b1c58d4cf0.png)

图片由 [Joshua Hoehne](https://unsplash.com/@joshua_hoehne?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在社交媒体时代，分析对话量对于理解用户行为、检测趋势，以及最重要的是识别异常变得至关重要。知道异常发生的时间可以帮助管理层和营销团队在危机情境中作出回应。

在这篇文章中，我们将探讨一种基于残差的方法，用于检测社交媒体流量时间序列数据中的异常，使用来自Twitter的一个真实世界示例。对于这个任务，我将使用 [Numenta Anomaly Benchmark](https://github.com/numenta/NAB) 提供的数据集，其中包含Twitter帖子的流量数据，并且数据窗口为5分钟。

我们将从两个角度分析数据：作为第一个练习，我们将使用完整的数据集检测异常，然后我们将在实时场景中检测异常，以检查此方法的响应能力。

# 使用完整数据集检测异常

## **分析一个示例的Twitter流量数据集**

让我们从加载和可视化一个关于Apple的示例Twitter流量数据集开始：

![](../Images/22c65890ffd6b0b6c1656361831c0dfa.png)

观察到的AAPL Twitter流量的体积和对数体积

作者提供的图片

从这个图表中，我们可以看到数据中有几个尖峰（异常）。这些体积尖峰就是我们希望识别的异常。

从第二个图（对数尺度）来看，我们可以看到Twitter成交量数据呈现出明显的日常周期，白天活动较多，夜间活动较少。这种季节性模式在社交媒体数据中很常见，因为它反映了用户的昼夜活动。它还表现出每周的季节性，但我们将忽略这一点。

## 去除季节性趋势

我们希望确保这个周期不会干扰我们的结论，因此我们将将其移除。为了去除这种季节性，我们将进行季节性分解。

首先，我们将计算成交量的移动平均（MA），这将捕捉到趋势。然后，我们将计算观察到的成交量与MA的比率，这为我们提供了乘法季节效应。

![](../Images/f501a6697ad6760f04208d3af5c9ae29.png)

时间对成交量的乘法效应

图片来源：作者

正如预期的那样，季节性趋势遵循昼夜周期，白天时达到高峰，夜间则处于低谷。

为了进一步进行分解，我们需要根据之前发现的乘法趋势计算成交量的期望值。

![](../Images/9c15e62b66dce0ae415eba2189620db1.png)

AAPL Twitter成交量的观察值和期望值的成交量及对数成交量

图片来源：作者

## **分析残差并检测异常**

分解的最终组成部分是由于期望值和真实值之间的差异所产生的误差。我们可以将这一度量视为去除均值后的考虑季节性的成交量：

![](../Images/3b054b29c8ae1dff7458a90e29410cc8.png)

AAPL Twitter成交量季节性分解后的绝对误差和对数误差

图片来源：作者

有趣的是，残差分布紧密地跟随帕累托分布。这一特性使得我们可以使用帕累托分布来设置阈值以检测异常，因为我们可以标记任何高于某一百分位（例如0.9995）的残差为潜在异常。

![](../Images/cbf2d33f41a5ccc3349225eb0e711028.png)

绝对误差和对数误差的分位数与帕累托分位数

图片来源：作者

现在，我必须做一个重要的声明：我所说的这一特性本身并不“绝对正确”。根据我在社交监听中的经验，我观察到这一点对于大多数社交数据来说是成立的，除了那些异常值较多且右偏的数据集。

在这种特定情况下，我们的观察值超过了15k，因此我们将p值设置为0.9995。基于这一阈值，每10,000个观察值大约会检测到5个异常（假设完美的帕累托分布）。

因此，如果我们检查数据中哪些观察值的误差的p值高于0.9995，我们将得到以下信号：

![](../Images/825a02da7c854a300393682312658db1.png)

AAPL Twitter成交量的异常信号

图片来源：作者

从这张图表中，我们看到体积最大的观察值被突出显示为异常。当然，如果我们希望更多或更少的信号，可以调整所选择的p值，记住，随着p值的降低，信号的数量将增加。

# **实时异常检测**

现在让我们切换到一个实时场景。在这种情况下，我们将对每个新观察数据运行相同的算法，检查哪些信号被返回以及信号在观察发生后的返回速度：

![](../Images/caf3d10fbb73d8281539f2b18a4b19ae.png)

AAPL Twitter交易量的实时信号

作者提供的图片

我们可以清楚地看到，这次我们有更多的信号。这是合理的，因为我们拟合的帕累托曲线会随着可用数据的变化而变化。如果我们检查“2015-03-08”之前的数据，前三个信号可以被视为异常，但如果我们考虑整个数据集，它们的重要性就较低。

根据构建方式，提供的代码返回的信号仅限于过去24小时的数据。然而，正如我们在下面所看到的，大多数信号在考虑到新观察数据时立即被返回，只有少数几个信号已被加粗：

```py
New signal at datetime 2015-03-03 21:02:53, relative to timestamp 2015-03-03 21:02:53
New signal at datetime 2015-03-03 21:07:53, relative to timestamp 2015-03-03 21:07:53
New signal at datetime 2015-03-03 21:12:53, relative to timestamp 2015-03-03 21:12:53
New signal at datetime 2015-03-03 21:17:53, relative to timestamp 2015-03-03 21:17:53 **
New signal at datetime 2015-03-05 05:37:53, relative to timestamp 2015-03-04 20:07:53 
New signal at datetime 2015-03-07 09:47:53, relative to timestamp 2015-03-06 19:42:53 ** 
New signal at datetime 2015-03-09 15:57:53, relative to timestamp 2015-03-09 15:57:53 
New signal at datetime 2015-03-09 16:02:53, relative to timestamp 2015-03-09 16:02:53
New signal at datetime 2015-03-09 16:07:53, relative to timestamp 2015-03-09 16:07:53
New signal at datetime 2015-03-14 01:37:53, relative to timestamp 2015-03-14 01:37:53
New signal at datetime 2015-03-14 08:52:53, relative to timestamp 2015-03-14 08:52:53
New signal at datetime 2015-03-14 09:02:53, relative to timestamp 2015-03-14 09:02:53
New signal at datetime 2015-03-15 16:12:53, relative to timestamp 2015-03-15 16:12:53
New signal at datetime 2015-03-16 02:52:53, relative to timestamp 2015-03-16 02:52:53
New signal at datetime 2015-03-16 02:57:53, relative to timestamp 2015-03-16 02:57:53
New signal at datetime 2015-03-16 03:02:53, relative to timestamp 2015-03-16 03:02:53
New signal at datetime 2015-03-30 17:57:53, relative to timestamp 2015-03-30 17:57:53
New signal at datetime 2015-03-30 18:02:53, relative to timestamp 2015-03-30 18:02:53
New signal at datetime 2015-03-31 03:02:53, relative to timestamp 2015-03-31 03:02:53
New signal at datetime 2015-03-31 03:07:53, relative to timestamp 2015-03-31 03:07:53
New signal at datetime 2015-03-31 03:12:53, relative to timestamp 2015-03-31 03:12:53
New signal at datetime 2015-03-31 03:17:53, relative to timestamp 2015-03-31 03:17:53
New signal at datetime 2015-03-31 03:22:53, relative to timestamp 2015-03-31 03:22:53
New signal at datetime 2015-03-31 03:27:53, relative to timestamp 2015-03-31 03:27:53
New signal at datetime 2015-03-31 03:32:53, relative to timestamp 2015-03-31 03:32:53
New signal at datetime 2015-03-31 03:37:53, relative to timestamp 2015-03-31 03:37:53
New signal at datetime 2015-03-31 03:42:53, relative to timestamp 2015-03-31 03:42:53
New signal at datetime 2015-03-31 20:22:53, relative to timestamp 2015-03-31 20:22:53 **
New signal at datetime 2015-04-02 12:52:53, relative to timestamp 2015-04-01 20:42:53 ** 
New signal at datetime 2015-04-14 14:12:53, relative to timestamp 2015-04-14 14:12:53
New signal at datetime 2015-04-14 22:52:53, relative to timestamp 2015-04-14 22:52:53
New signal at datetime 2015-04-14 22:57:53, relative to timestamp 2015-04-14 22:57:53
New signal at datetime 2015-04-14 23:02:53, relative to timestamp 2015-04-14 23:02:53
New signal at datetime 2015-04-14 23:07:53, relative to timestamp 2015-04-14 23:07:53
New signal at datetime 2015-04-14 23:12:53, relative to timestamp 2015-04-14 23:12:53
New signal at datetime 2015-04-14 23:17:53, relative to timestamp 2015-04-14 23:17:53
New signal at datetime 2015-04-14 23:22:53, relative to timestamp 2015-04-14 23:22:53
New signal at datetime 2015-04-14 23:27:53, relative to timestamp 2015-04-14 23:27:53
New signal at datetime 2015-04-21 20:12:53, relative to timestamp 2015-04-21 20:12:53
```

正如我们所见，算法能够实时检测到异常，大多数信号在考虑到新观察数据时立即被触发。这使得组织能够迅速对社交媒体谈论量的意外变化做出反应。

# 结论与进一步改进

本文提出的基于残差的方法提供了一种灵活的工具，用于检测社交媒体量时间序列中的异常。这种方法可以帮助公司和营销人员识别发生中的重要事件、趋势和潜在危机。

尽管该算法已经有效，但仍有几个方面可以进一步发展，例如：

+   依赖于固定窗口进行实时检测，目前的实现依赖于所有历史数据

+   探索不同的时间粒度（例如，使用每小时而非5分钟间隔）

+   使用统计测试验证帕累托分布假设

+   …

如果你喜欢这篇文章，请留下掌声并随时评论，任何建议和反馈都非常感激！

[*在这里你可以找到实现的笔记本*](https://github.com/lorenzomezzini/MediumPosts/tree/main/AnomalyDetection)*.*
