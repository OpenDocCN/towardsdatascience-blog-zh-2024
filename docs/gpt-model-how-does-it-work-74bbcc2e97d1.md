# GPT 模型：它是如何工作的？

> 原文：[`towardsdatascience.com/gpt-model-how-does-it-work-74bbcc2e97d1?source=collection_archive---------1-----------------------#2024-02-21`](https://towardsdatascience.com/gpt-model-how-does-it-work-74bbcc2e97d1?source=collection_archive---------1-----------------------#2024-02-21)

## 让我们一起用 Python 和 PyTorch 深入探索它的工作原理吧

[](https://dmitryelj.medium.com/?source=post_page---byline--74bbcc2e97d1--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--74bbcc2e97d1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--74bbcc2e97d1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74bbcc2e97d1--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--74bbcc2e97d1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74bbcc2e97d1--------------------------------) ·阅读时间：9 分钟·2024 年 2 月 21 日

--

![](img/b8e5cd4404f0635a88b139c733b2a8c1.png)

图片来自 Hal Gatewood，[Unsplash](https://unsplash.com/@halacious)

在过去几年里，围绕人工智能的热议一直非常庞大，而这一切的主要推动力显然是基于 GPT 的大型语言模型的出现。有趣的是，这种方法本身并不新颖。LSTM（长短期记忆）神经网络是 1997 年创建的，而 2017 年发表的著名论文《Attention is All You Need》则为现代自然语言处理奠定了基石。但直到 2020 年，GPT-3 的效果才足够好，不仅仅是在学术论文中得到认可，也能在现实世界中得到应用。

如今，任何人都可以在网页浏览器中与 GPT 聊天，但可能不到 1%的人真正了解它是如何工作的。模型给出的聪明且机智的回答让人产生与*智能生物*对话的错觉，但真的是这样吗？好吧，了解它的最好方式就是看看它是如何工作的。在本文中，我们将拿 OpenAI 的一个真实 GPT 模型来做演示，在本地运行它，并一步步查看它的工作原理。

本文面向初学者以及对编程和数据科学感兴趣的读者。我将通过 Python 来演示我的步骤，但不需要对 Python 有深入的理解。

让我们开始吧！

## 加载模型
