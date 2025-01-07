# transformers 能解决一切问题吗？

> 原文：[`towardsdatascience.com/can-transformers-solve-everything-0421ebaf9f8e?source=collection_archive---------1-----------------------#2024-10-01`](https://towardsdatascience.com/can-transformers-solve-everything-0421ebaf9f8e?source=collection_archive---------1-----------------------#2024-10-01)

## 查看数学和数据后发现，transformers 既被过度使用，也被低估。

[](https://medium.com/@crackalamoo?source=post_page---byline--0421ebaf9f8e--------------------------------)![Harys Dalvi](https://medium.com/@crackalamoo?source=post_page---byline--0421ebaf9f8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0421ebaf9f8e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0421ebaf9f8e--------------------------------) [Harys Dalvi](https://medium.com/@crackalamoo?source=post_page---byline--0421ebaf9f8e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0421ebaf9f8e--------------------------------) ·阅读时长 13 分钟·2024 年 10 月 1 日

--

transformers 最为人知的是它们在自然语言处理中的应用。它们最初是为语言翻译而设计的[[1](https://arxiv.org/pdf/1706.03762)]，现在因其在大型语言模型中的应用而最为著名，例如 ChatGPT（生成预训练的*transformer*）。

但是自从它们被引入以来，transformers 已经被应用于越来越多的任务，并取得了很好的结果。这些任务包括图像识别[[2](https://arxiv.org/abs/2010.11929)]、强化学习[[3](https://arxiv.org/abs/2106.01345)]，甚至是天气预测[[4](https://arxiv.org/abs/2312.03876)]。

即使是看似特定的任务，如使用 transformers 进行语言生成，也有许多令人惊讶的地方，正如我们已经看到的那样。大型语言模型具有涌现的属性，表现得比单纯预测下一个词更智能。例如，它们可能知道关于世界的各种事实，或复现一个人讲话风格的细微差别。

transformers 的成功让一些人提出了一个问题，那就是 transformers 是否能解决所有问题。如果 transformers 能推广到这么多任务，难道就没有理由*不*使用 transformer 吗？

显然，仍然需要其他机器学习模型，并且如今天常被忽视的那样，非机器学习模型和人类智力也有其存在的意义。但 transformers 确实具有一些独特的属性，并且迄今为止已经展现出惊人的结果。它们也有相当的数学和经验基础……
