# 使用 SageMaker 异步推理部署大型语言模型

> 原文：[`towardsdatascience.com/deploying-large-language-models-with-sagemaker-asynchronous-inference-c00038b70b3e?source=collection_archive---------7-----------------------#2024-01-27`](https://towardsdatascience.com/deploying-large-language-models-with-sagemaker-asynchronous-inference-c00038b70b3e?source=collection_archive---------7-----------------------#2024-01-27)

## 为近实时应用程序排队请求

[](https://ram-vegiraju.medium.com/?source=post_page---byline--c00038b70b3e--------------------------------)![Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--c00038b70b3e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c00038b70b3e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c00038b70b3e--------------------------------) [Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--c00038b70b3e--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c00038b70b3e--------------------------------) ·阅读时间：10 分钟·2024 年 1 月 27 日

--

![](img/0fce977b001046234030458bd3e0ab4c.png)

图片来自[Unsplash](https://unsplash.com/photos/a-robot-holding-a-gun-next-to-a-pile-of-rolls-of-toilet-paper-YeoSV_3Up-k)，由[**Gerard Siderius**](https://unsplash.com/@siderius_creativ)提供

大型语言模型（LLMs）继续迅速流行，托管和部署它们进行推理的方式也在不断增加。有关 LLM 托管的挑战，尤其是由于模型的大小以及确保其在部署硬件上得到最佳使用，已经有很多文献记录。LLM 的使用案例也各不相同。有些可能需要基于实时的响应时间，而其他则具有更接近实时的延迟要求。

对于后者以及更多离线推理的使用案例，[SageMaker 异步推理](https://docs.aws.amazon.com/sagemaker/latest/dg/async-inference.html)是一个不错的选择。正如其名所示，异步推理专注于一种更接近实时的工作负载，在这种情况下，延迟不必严格要求极低，但仍然需要一个可以根据需要调用并扩展的活动端点。特别是在大型语言模型中，这类工作负载正变得越来越流行，使用案例包括内容编辑/生成、摘要等。这些工作负载不需要毫秒级响应，但仍然要求能及时推理，按需调用，而不是完全离线的方式，如 SageMaker 批处理转换。

在这个例子中，我们将看看如何使用[HuggingFace Text](https://github.com/huggingface/text-generation-inference)…
