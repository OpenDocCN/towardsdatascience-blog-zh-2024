# 统计确认你的基准测试——通过案例研究比较 Pandas 和 Polars 在 100 万行数据上的表现

> 原文：[https://towardsdatascience.com/statistically-confirm-your-benchmark-case-study-comparing-pandas-and-polars-with-1-million-rows-0ea04d7b61f2?source=collection_archive---------14-----------------------#2024-06-18](https://towardsdatascience.com/statistically-confirm-your-benchmark-case-study-comparing-pandas-and-polars-with-1-million-rows-0ea04d7b61f2?source=collection_archive---------14-----------------------#2024-06-18)

## 使用 Python 比较独立样本 t 检验和 Welch t 检验的基准测试得分

[](https://medium.com/@borih.k?source=post_page---byline--0ea04d7b61f2--------------------------------)[![Boriharn K](../Images/1b23a79640f5272c1382918bfdba03b0.png)](https://medium.com/@borih.k?source=post_page---byline--0ea04d7b61f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0ea04d7b61f2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0ea04d7b61f2--------------------------------) [Boriharn K](https://medium.com/@borih.k?source=post_page---byline--0ea04d7b61f2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0ea04d7b61f2--------------------------------) ·13 分钟阅读·2024年6月18日

--

![](../Images/20108a3b2ffd45cefd90269fd833a6ce.png)

图像由 [Google Gemini](https://gemini.google.com/app) 生成

基准测试是我在互联网上最喜欢的内容之一。不仅看到结果令人兴奋，而且它还帮助我在不浪费时间和金钱的情况下进行比较。例如，比较 GPU 的文章和视频可以指导我下一台应该购买的笔记本电脑。在编程中，基准测试帮助我看到哪些方法运行得更快。

然而，要小心你在互联网上看到的一切，因为你可能会得到完全不同的结果。为什么？

我并不是说发布的分数不真实。这些测试必须在标准且可靠的流程下进行。顺便说一下，问题不在于在其他设备上执行相同的测试；即使是相同配置的设备，结果也可能不同。基准测试的结果可能会受到多种因素的影响（[参考1](https://www.quora.com/Why-does-the-same-processor-never-hit-the-same-score-in-benchmark-tests), [参考2](https://www.quora.com/Why-do-some-PCs-with-the-same-specs-perform-worse-than-others-in-benchmarks)）。

![](../Images/04cf16ced21254e7f4d7395e701a11f2.png)

本文中的一个示例，比较 Pandas 和 Polars 库的执行时间——图像由作者提供。

对于某些硬件和软件测试，如果资源有限，进行实验是很困难的。顺便说一下，对于编程来说，这可能是……
