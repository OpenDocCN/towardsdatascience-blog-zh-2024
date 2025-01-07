# Pytorch中的高级张量选择

> 原文：[https://towardsdatascience.com/advanced-selection-from-tensors-in-pytorch-f012e52eef80?source=collection_archive---------9-----------------------#2024-02-27](https://towardsdatascience.com/advanced-selection-from-tensors-in-pytorch-f012e52eef80?source=collection_archive---------9-----------------------#2024-02-27)

## 使用 `torch.index_select`、`torch.gather` 和 `torch.take`

[](https://medium.com/@hrmnmichaels?source=post_page---byline--f012e52eef80--------------------------------)[![Oliver S](../Images/b5ee0fa2d5fb115f62e2e9dfcb92afdd.png)](https://medium.com/@hrmnmichaels?source=post_page---byline--f012e52eef80--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f012e52eef80--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f012e52eef80--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--f012e52eef80--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f012e52eef80--------------------------------) ·6分钟阅读·2024年2月27日

--

在某些情况下，您可能需要使用Pytorch进行一些高级索引/选择，例如回答这个问题：“如何根据张量B中指定的索引从张量A中选择元素？”

在这篇文章中，我们将介绍三种最常见的方法来处理这类任务，即 [torch.index_select](https://pytorch.org/docs/stable/generated/torch.index_select.html)、[torch.gather](https://pytorch.org/docs/stable/generated/torch.gather.html) 和 [torch.take](https://pytorch.org/docs/stable/generated/torch.take.html)。我们将详细解释它们，并相互对比。

![](../Images/341e3c1fa4e2acfbb5e8b65dde45f516.png)

图片来源：[Jerin J](https://unsplash.com/de/@rejiie?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/de/fotos/zeitrafferfoto-von-lichtern-X5FDG7k5M94?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

坦率地说，写这篇文章的一个动机是我忘记了如何以及何时使用哪个函数，结果不得不去谷歌搜索、浏览[Stack Overflow](https://stackoverflow.com/)以及官方文档（在我看来相对简短且不太有帮助）。因此，如前所述，我们将在此深入探讨这些函数：我们将阐明何时使用哪个函数，给出二维和三维的示例，并图示化展示选择结果。

我希望这篇文章能为这些函数带来清晰的解释，并消除进一步探索的需要——感谢阅读！

现在，话不多说，让我们逐一探讨这些函数。对于所有函数，我们首先从一个二维示例开始，展示其结果选择，然后再转向一个稍微复杂的三维示例。此外，我们还将在简单的 Python 中重新实现已执行的操作 —— 这样你就可以…
