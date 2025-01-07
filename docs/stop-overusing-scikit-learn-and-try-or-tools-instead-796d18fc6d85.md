# 停止过度使用Scikit-Learn，改用OR-Tools

> 原文：[https://towardsdatascience.com/stop-overusing-scikit-learn-and-try-or-tools-instead-796d18fc6d85?source=collection_archive---------1-----------------------#2024-01-26](https://towardsdatascience.com/stop-overusing-scikit-learn-and-try-or-tools-instead-796d18fc6d85?source=collection_archive---------1-----------------------#2024-01-26)

## 许多数据科学家过度使用机器学习，并忽视了数学优化技术，尽管这些技术对你的职业生涯非常有帮助。

[](https://medium.com/@mattchapmanmsc?source=post_page---byline--796d18fc6d85--------------------------------)[![Matt Chapman](../Images/7511deb8d9ed408ece21031f6614c532.png)](https://medium.com/@mattchapmanmsc?source=post_page---byline--796d18fc6d85--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--796d18fc6d85--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--796d18fc6d85--------------------------------) [Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--796d18fc6d85--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--796d18fc6d85--------------------------------) ·9分钟阅读·2024年1月26日

--

![](../Images/8a433a899b854daeecc63a6d635a2d9e.png)

图片由[Emilio Garcia](https://unsplash.com/@piensaenpixel)提供，来源于[Unsplash](https://unsplash.com/photos/man-playing-soccer-game-on-field-AWdCgDDedH0)

你想听我对2024年数据科学现状的独特见解吗？

*数据科学家过于迷恋机器学习*。

对于拿着锤子的人来说，所有的问题看起来都像钉子；对现代数据科学家而言，所有问题似乎都变成了机器学习问题。我们已经变得如此擅长将问题转化为分析和机器学习的语言，以至于有时忘记了还有其他数据科学方法存在。这是一个巨大的遗憾。

在这篇文章中，我将介绍数据科学的另一个分支——数学优化（特别是**约束规划**）——并展示它如何为你的数据科学家职业生涯增值。

**如果你没有扎实的数学背景**，请不要被名字吓到。我在大学时也没有学习数学，但得益于谷歌的开源Python库`OR-Tools`，我发现开始使用数学优化技术出乎意料的简单，我将在这篇面向初学者的文章中介绍它。
