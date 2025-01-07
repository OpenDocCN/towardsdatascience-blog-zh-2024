# 一行行地复现 GPT-2：第三部分 — 训练

> 原文：[`towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-3-training-f2fef87880fc?source=collection_archive---------8-----------------------#2024-09-03`](https://towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-3-training-f2fef87880fc?source=collection_archive---------8-----------------------#2024-09-03)

## 本文将逐行解析 Andrej Karpathy 的《一起来复现 GPT-2（124M）》第三部分中的代码。

[](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------) ·17 分钟阅读·2024 年 9 月 3 日

--

![](img/e552f6ecab2356374aafb878612114c8.png)

图片来源 — SDXL

在第一篇博文中，我们编写了 Transformer 架构的代码。在第二篇博文中，我们优化了 NVIDIA 的交互。为了完成我们的系列教程，接下来我们将对 GPT-2 模型进行预训练。如果你还没有观看过，强烈推荐 [观看 Andrej Karpathy 的《让我们复现 GPT-2（124M）》视频](https://www.youtube.com/watch?v=l8pRSuU81PU)，以及下面我们系列中的两篇前文。

[](/line-by-line-lets-reproduce-gpt-2-section-1-b26684f98492?source=post_page-----f2fef87880fc--------------------------------) ## 一行行地复现 GPT-2：第一部分

### 本文将逐行解析 Andrej Karpathy 的《一起来复现 GPT-2（124M）》第一部分中的代码。

towardsdatascience.com [](/line-by-line-lets-reproduce-gpt-2-section-2-hardware-optimization-86e71c91d9bb?source=post_page-----f2fef87880fc--------------------------------) ## 一行行地复现 GPT-2：第二部分 — 硬件优化

### 本文将逐行解析 Andrej Karpathy 的《一起来复现 GPT-2》第二部分中的硬件优化内容。

towardsdatascience.com

不再废话，让我们开始吧！

在我们开始预训练之前，我们需要对代码进行一些修改，以提高性能并在训练过程中提高可视性。请注意，我们将...
