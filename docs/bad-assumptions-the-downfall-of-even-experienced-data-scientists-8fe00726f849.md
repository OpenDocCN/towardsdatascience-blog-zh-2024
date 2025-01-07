# 错误假设——即使是经验丰富的数据科学家的失败

> 原文：[https://towardsdatascience.com/bad-assumptions-the-downfall-of-even-experienced-data-scientists-8fe00726f849?source=collection_archive---------3-----------------------#2024-08-16](https://towardsdatascience.com/bad-assumptions-the-downfall-of-even-experienced-data-scientists-8fe00726f849?source=collection_archive---------3-----------------------#2024-08-16)

## 统计学

## 数据可能具有欺骗性，所以要保持警惕！

[](https://medium.com/@maclayton?source=post_page---byline--8fe00726f849--------------------------------)[![Mike Clayton](../Images/2d37746b13b7d2ff1c6515893914da97.png)](https://medium.com/@maclayton?source=post_page---byline--8fe00726f849--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8fe00726f849--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8fe00726f849--------------------------------) [Mike Clayton](https://medium.com/@maclayton?source=post_page---byline--8fe00726f849--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8fe00726f849--------------------------------) ·阅读时间 9分钟·2024年8月16日

--

![](../Images/78504a1b294bacf8fb40aaf7c53eb9a7.png)

图片由[Gordon Johnson](https://pixabay.com/users/gdj-1086657/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4296272)提供，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=4296272)

**在处理任何形式的数据时，做出假设是不可避免的。数据通常本质上是不完整和不完美的。无论如何，我们的工作是尽力解读和提取意义。**

**然而，总是存在一种风险，即对数据的错误假设可能会扭曲最终结果，甚至可能使结果误导人，或者更糟的是，完全没有意义。**

**我认为，这种情况既可能发生在经验丰富的从业者身上，也可能发生在那些刚接触该领域的人身上。**

**那么，这种情况是如何发生的呢？我们又能做些什么来减轻这种影响呢？**

# 简介

在我的职业生涯初期，我是一名工程师，处理实验气动学相关的工作。这要求处理和解释大量来自现实世界的不完美实验数据。

我曾在我工作过的办公室里听过一句非常常用的口号（我已经把真实的说法稍微减轻了，您可以根据需要插入合适的脏话）：

> 假设是所有****真正糟糕错误****的母亲
