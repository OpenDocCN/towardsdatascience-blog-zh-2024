# VisionTS：从图像构建卓越的预测模型

> 原文：[`towardsdatascience.com/visionts-building-superior-forecasting-models-from-images-cb06257c7ef9?source=collection_archive---------7-----------------------#2024-09-26`](https://towardsdatascience.com/visionts-building-superior-forecasting-models-from-images-cb06257c7ef9?source=collection_archive---------7-----------------------#2024-09-26)

## 利用图像的力量进行时间序列预测

[](https://medium.com/@nikoskafritsas?source=post_page---byline--cb06257c7ef9--------------------------------)![Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--cb06257c7ef9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cb06257c7ef9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb06257c7ef9--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--cb06257c7ef9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb06257c7ef9--------------------------------) ·阅读时长 8 分钟·2024 年 9 月 26 日

--

![](img/797f2fc21e550ded226f456070345737.png)

由作者使用 DALLE*3 创作

在构建预训练时间序列模型时，最大挑战是什么？

**答案：** 寻找高质量、多样化的时间序列数据。我们在之前的文章中已经讨论过这个问题。

构建基础预测模型有两种主要方法：

1.  **“Bootstrap”一个 LLM**：通过应用针对时间序列任务定制的微调或分词策略，重新利用像*GPT-4*或*Llama*这样的预训练大型语言模型。

1.  **“从零开始”：** 构建一个大规模的时间序列数据集，并从零开始预训练一个模型，期望它能够推广到新数据。

虽然第一种方法有效，因为 Transformers 是通用计算引擎，但它并没有产生最佳结果。第二种方法更为成功，正如这里所展示的：MOIRAI、TimesFM、TTM 等。

然而，这些模型似乎遵循了扩展规律，它们的表现依赖于找到大量的时间序列数据——这又让我们回到了最初的挑战。

但是，如果我们能够利用一种不同的模态，比如图像呢？这可能看起来违反直觉，但*一些*研究人员探索了这个假设并取得了开创性的成果。在…
