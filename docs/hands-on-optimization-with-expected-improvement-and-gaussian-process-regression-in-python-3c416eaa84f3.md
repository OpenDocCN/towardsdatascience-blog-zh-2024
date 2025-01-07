# 《在 Python 中进行期望改进和高斯过程回归的实操优化》

> 原文：[`towardsdatascience.com/hands-on-optimization-with-expected-improvement-and-gaussian-process-regression-in-python-3c416eaa84f3?source=collection_archive---------2-----------------------#2024-06-09`](https://towardsdatascience.com/hands-on-optimization-with-expected-improvement-and-gaussian-process-regression-in-python-3c416eaa84f3?source=collection_archive---------2-----------------------#2024-06-09)

## 《在 Python 中进行全局优化期望改进的友好指南》

[](https://piero-paialunga.medium.com/?source=post_page---byline--3c416eaa84f3--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--3c416eaa84f3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3c416eaa84f3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c416eaa84f3--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--3c416eaa84f3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c416eaa84f3--------------------------------) ·11 分钟阅读·2024 年 6 月 9 日

--

![](img/8cd1badb6282fc614131f6a8ab079eed.png)

图片由作者使用 DALL·E 制作

> 免责声明：高斯出生于 1777 年，他比我聪明得多。很多人在我之前就写过这些内容，并且做得非常出色。这篇文章的一个很好的参考是[这篇](https://medium.com/@okanyenigun/step-by-step-guide-to-bayesian-optimization-a-python-based-approach-3558985c6818)，由[@okanyenigun](http://twitter.com/okanyenigun)写得非常好。

**我的** **妻子**是商科专业，如果你问她：“**什么是人工智能？**”

她说：

> “某些事物是通过教给一堆信息来发展思维和他妈的（她没有说‘他妈的’）…我不知道…想法。”

我会说这与人们描述人工智能的方式相当一致。经过一些调整后，这也离事实不远。

现在，如果你和一位数学家或物理学家交谈，答案会更为技术性，可能还会让人觉得很无聊（我是物理学家，所以我可以这么说）。如果要我定义人工智能，我会说：

> “人工智能是一组使用数据来最小化损失函数的算法”

…我告诉过你这会更无聊。那么我这是什么意思呢？

# 0\. 机器学习…
