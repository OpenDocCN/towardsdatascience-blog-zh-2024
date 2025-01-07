# **Tiny Time Mixers (TTM)**：IBM 推出的强大零-shot 预测模型

> 原文：[`towardsdatascience.com/tiny-time-mixers-ttm-a-powerful-zero-shot-forecasting-model-by-ibm-576b0e0af583?source=collection_archive---------3-----------------------#2024-06-08`](https://towardsdatascience.com/tiny-time-mixers-ttm-a-powerful-zero-shot-forecasting-model-by-ibm-576b0e0af583?source=collection_archive---------3-----------------------#2024-06-08)

## 一个新的轻量级开源基础模型

[](https://medium.com/@nikoskafritsas?source=post_page---byline--576b0e0af583--------------------------------)![Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--576b0e0af583--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--576b0e0af583--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--576b0e0af583--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--576b0e0af583--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--576b0e0af583--------------------------------) ·阅读时间 10 分钟·2024 年 6 月 8 日

--

![](img/4489291773c482e9b53559c2610fe74b.png)

作者使用 DALLE*3 创建

**如果你跟随 LLM（大语言模型）领域的最新研究，你会注意到两种主要的研究方法：**

首先，研究人员专注于构建尽可能大的模型。在下一个词预测任务上进行预训练对于提高性能至关重要（也是花费数百万美元的地方！）。

其次，研究人员使用像量化这样的技术来创建更小、更快的模型——同时保持强大的通用性能。

然而，当较小的模型在某些任务上超过了更大的模型时，事情变得非常有趣。例如，**Llama 3–8B** 在 MMLU 任务中超过了**更大的 Llama 2–70B**！

***Tiny Time Mixers*** (**TTM**)**[1],** 由***IBM***推出，采用了第二种方法。它是一个轻量级模型，超越了更大的 SOTA 模型——包括 MOIRAI，在 M4 数据集上的表现尤为突出。**此外，它是开源的！**

本文讨论了：

+   *TTM*的架构和功能。

+   使*TTM*与众不同的创新特性。

+   *TTM*与其他模型的基准测试结果。

让我们开始吧！

> 我推出了**AI Horizon Forecast，** 一个...
