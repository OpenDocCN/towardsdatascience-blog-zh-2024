# 一行行地复现GPT-2：第3节 — 训练

> 原文：[https://towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-3-training-f2fef87880fc?source=collection_archive---------8-----------------------#2024-09-03](https://towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-3-training-f2fef87880fc?source=collection_archive---------8-----------------------#2024-09-03)

## 本文将逐行解析Andrej Karpathy的《一起来复现GPT-2（124M）》第3节中的代码。

[](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)[![Matthew Gunton](../Images/6f5a9530ad5252aa3f2fae87b3f272b1.png)](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--f2fef87880fc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f2fef87880fc--------------------------------) ·17分钟阅读·2024年9月3日

--

![](../Images/e552f6ecab2356374aafb878612114c8.png)

图片来源 — SDXL

在第一篇博文中，我们编写了Transformer架构的代码。在第二篇博文中，我们优化了NVIDIA的交互。为了完成我们的系列教程，接下来我们将对GPT-2模型进行预训练。如果你还没有观看过，强烈推荐 [观看Andrej Karpathy的《让我们复现GPT-2（124M）》视频](https://www.youtube.com/watch?v=l8pRSuU81PU)，以及下面我们系列中的两篇前文。

[](/line-by-line-lets-reproduce-gpt-2-section-1-b26684f98492?source=post_page-----f2fef87880fc--------------------------------) [## 一行行地复现GPT-2：第1节

### 本文将逐行解析Andrej Karpathy的《一起来复现GPT-2（124M）》第1节中的代码。

towardsdatascience.com](/line-by-line-lets-reproduce-gpt-2-section-1-b26684f98492?source=post_page-----f2fef87880fc--------------------------------) [](/line-by-line-lets-reproduce-gpt-2-section-2-hardware-optimization-86e71c91d9bb?source=post_page-----f2fef87880fc--------------------------------) [## 一行行地复现GPT-2：第2节 — 硬件优化

### 本文将逐行解析Andrej Karpathy的《一起来复现GPT-2》第2节中的硬件优化内容。

towardsdatascience.com](/line-by-line-lets-reproduce-gpt-2-section-2-hardware-optimization-86e71c91d9bb?source=post_page-----f2fef87880fc--------------------------------)

不再废话，让我们开始吧！

在我们开始预训练之前，我们需要对代码进行一些修改，以提高性能并在训练过程中提高可视性。请注意，我们将...
