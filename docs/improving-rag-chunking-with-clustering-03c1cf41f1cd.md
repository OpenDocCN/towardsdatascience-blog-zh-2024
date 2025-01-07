# 我如何使用聚类技术改进分块并构建更好的 RAGs

> 原文：[`towardsdatascience.com/improving-rag-chunking-with-clustering-03c1cf41f1cd?source=collection_archive---------1-----------------------#2024-09-04`](https://towardsdatascience.com/improving-rag-chunking-with-clustering-03c1cf41f1cd?source=collection_archive---------1-----------------------#2024-09-04)

## 它既快速又具有成本效益

[](https://thuwarakesh.medium.com/?source=post_page---byline--03c1cf41f1cd--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--03c1cf41f1cd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03c1cf41f1cd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03c1cf41f1cd--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--03c1cf41f1cd--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03c1cf41f1cd--------------------------------) ·阅读时长 7 分钟·2024 年 9 月 4 日

--

![](img/119dde6165cd559a1ab312345b7b72e8.png)

图片来源：[Eaters Collective](https://unsplash.com/@eaterscollective?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当我的句子之间相距甚远时，语义分块崩溃了。

我第一次尝试语义分块时失败了，因为远距离的句子共享了太多的意义。然而，代理性方法在大多数情况下成本高且速度过慢。我想在两者之间找到一个平衡点，我希望它既能保持合理的准确性，又能更便宜。

RAG 应用程序在很大程度上依赖于分块策略。更好的分块能带来更好的响应。有许多方式可以对文本进行分块。最简单且最流行的是递归字符分块，稍微复杂但有帮助的是语义分块。更接近人类思维的方式是代理性分块。

如果你是新手，可以查看我之前关于分块的文章。

[](/agentic-chunking-for-rags-091beccd94b1?source=post_page-----03c1cf41f1cd--------------------------------) ## 如何实现接近人类水平的 RAG 分块性能

### 昂贵但强大的分割技术，用于优化 RAG 检索

towardsdatascience.com
