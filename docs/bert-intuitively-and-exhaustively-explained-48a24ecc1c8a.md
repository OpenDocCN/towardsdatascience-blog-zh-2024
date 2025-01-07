# BERT — 直观且详尽的解释

> 原文：[https://towardsdatascience.com/bert-intuitively-and-exhaustively-explained-48a24ecc1c8a?source=collection_archive---------2-----------------------#2024-08-23](https://towardsdatascience.com/bert-intuitively-and-exhaustively-explained-48a24ecc1c8a?source=collection_archive---------2-----------------------#2024-08-23)

## 将通用理解融入语言模型

[](https://medium.com/@danielwarfield1?source=post_page---byline--48a24ecc1c8a--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--48a24ecc1c8a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--48a24ecc1c8a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--48a24ecc1c8a--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--48a24ecc1c8a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--48a24ecc1c8a--------------------------------) ·阅读时间：46分钟·2024年8月23日

--

![](../Images/a936f980e2bca9d843e27da4b08d8e3f.png)

“Baking”由Daniel Warfield使用MidJourney创作。所有图片除非另有说明，均为作者提供。文章最初发布于[直观且详尽的解释](https://iaee.substack.com/)。

在本文中，我们将讨论“来自变换器的双向编码器表示”（BERT），这是一种旨在理解语言的模型。虽然BERT与像GPT这样的模型相似，但BERT的重点是理解文本，而不是生成文本。这在各种任务中都很有用，比如对产品评论的正面性进行排序，或者预测问题的答案是否正确。

在深入探讨BERT之前，我们将简要讨论变换器架构，它是BERT的直接灵感来源。通过理解这一点，我们将深入了解BERT，并讨论它是如何构建和训练的，以通过利用语言的一般理解来解决问题。最后，我们将从零开始创建一个BERT模型，并使用它来预测产品评论是正面的还是负面的。

**这个对谁有用？** 任何希望全面了解人工智能前沿状态的人。

**这篇文章的难度如何？** 本文的前半部分适合各个水平的读者，而后半部分涉及从零开始的实现则相对较为高级。根据需要提供了补充资源。

**前提条件：** 我强烈建议理解关于…的基本概念。
