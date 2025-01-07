# 学习机器学习的勇气：解决梯度消失与爆炸问题（第二部分）

> 原文：[`towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-2-d0b8aed1ce7a?source=collection_archive---------9-----------------------#2024-05-03`](https://towardsdatascience.com/courage-to-learn-ml-tackling-vanishing-and-exploding-gradients-part-2-d0b8aed1ce7a?source=collection_archive---------9-----------------------#2024-05-03)

## 激活函数、权重初始化、批归一化及其在 PyTorch 中的应用的全面调查

[](https://amyma101.medium.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------)![Amy Ma](https://amyma101.medium.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------) [Amy Ma](https://amyma101.medium.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0b8aed1ce7a--------------------------------) ·37 分钟阅读·2024 年 5 月 3 日

--

欢迎回到“[学习机器学习的勇气](https://towardsdatascience.com/tagged/courage-to-learn-ml)”新的一章。对于初次接触本系列的读者，本系列旨在让这些复杂的主题变得易于理解并且富有趣味，就像是导师与学习者之间的轻松对话，写作风格受到《[敢于不讨好](https://www.goodreads.com/book/show/43306206-the-courage-to-be-disliked)》的启发，特别关注于机器学习。

这一次，我们将继续探索如何克服梯度消失与爆炸的挑战。在开篇中，我们讨论了保持梯度稳定的重要性，以确保网络中有效的学习。我们揭示了不稳定的梯度如何成为加深网络的障碍，实质上限制了深度“学习”的潜力。为了更生动地阐述这些概念，我们使用了一个比喻，讲述了如何经营一个名为 DNN（Delicious Nutritious Nibbles，意为美味营养小吃）的迷你冰淇淋工厂，并通过类比阐明了类似于协调无缝生产线的 DNN 训练策略。

现在，在第二部分中，我们将深入探讨每个提出的解决方案，以与我们的冰淇淋工厂相同的清晰度和创造力来审视它们。以下是我们将在这一部分讨论的主题：
