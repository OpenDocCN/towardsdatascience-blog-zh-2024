# 深度学习图解，第一部分：神经网络是如何工作的？

> 原文：[`towardsdatascience.com/neural-networks-illustrated-part-1-how-does-a-neural-network-work-c3f92ce3b462?source=collection_archive---------0-----------------------#2024-01-31`](https://towardsdatascience.com/neural-networks-illustrated-part-1-how-does-a-neural-network-work-c3f92ce3b462?source=collection_archive---------0-----------------------#2024-01-31)

## 神经网络的图解和直观介绍

[](https://medium.com/@shreya.rao?source=post_page---byline--c3f92ce3b462--------------------------------)![Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--c3f92ce3b462--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c3f92ce3b462--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c3f92ce3b462--------------------------------) [Shreya Rao](https://medium.com/@shreya.rao?source=post_page---byline--c3f92ce3b462--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c3f92ce3b462--------------------------------) ·15 分钟阅读·2024 年 1 月 31 日

--

如果你读过我之前的文章，你就会知道接下来会讲什么。在这部分内容中，我们将复杂的概念通过图解使其变得有趣且易懂。如果你还没读过我之前的文章，我强烈推荐你从我的系列文章《[机器学习入门包](https://medium.com/@shreya.rao/list/machine-learning-starter-pack-b89c3a7f97ad)》开始阅读，因为你会发现这里的许多内容在后续的文章中仍然相关。

![Shreya Rao](img/45d3d481fab74a720c78346bc47e95fd.png)

[Shreya Rao](https://medium.com/@shreya.rao?source=post_page-----c3f92ce3b462--------------------------------)

## 机器学习入门包

[查看列表](https://medium.com/@shreya.rao/list/machine-learning-starter-pack-b89c3a7f97ad?source=post_page-----c3f92ce3b462--------------------------------)3 个故事![](img/6343ce3bf14ba921f69d1ffa2d90bcce.png)![](img/d244f614e40bc7fb412f09c04dde40ec.png)![](img/e7ba1c7470c32d224f4a4ddf06c6596c.png)

今天，我们要讲解的重点是神经网络。这只是我计划进行的一系列关于深度学习的文章中的第一篇。它将聚焦于简单的人工神经网络是如何学习的，并为你提供一个**深入**（哈，双关）理解神经网络是如何一层一层构建的——从神经元到神经元。这对我们后续的学习非常重要，*超级*必要。虽然我们会涉及一些数学细节，但不用担心，因为我们会逐步拆解并图解每一个步骤。等到这篇文章读完，你会发现它比看起来简单得多。

但是在我们探讨这个问题之前，你可能会想：我们为什么需要神经网络？随着如此多的…
