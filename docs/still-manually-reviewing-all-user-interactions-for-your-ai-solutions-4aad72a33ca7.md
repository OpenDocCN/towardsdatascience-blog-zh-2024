# 仍在手动审查所有用户与 AI 解决方案的互动吗？

> 原文：[https://towardsdatascience.com/still-manually-reviewing-all-user-interactions-for-your-ai-solutions-4aad72a33ca7?source=collection_archive---------12-----------------------#2024-10-08](https://towardsdatascience.com/still-manually-reviewing-all-user-interactions-for-your-ai-solutions-4aad72a33ca7?source=collection_archive---------12-----------------------#2024-10-08)

## 发现如何利用余弦相似度来节省时间并简化你的 AI 系统

[](https://medium.com/@marcosss?source=post_page---byline--4aad72a33ca7--------------------------------)[![Marcos Santiago](../Images/570c90fc27b75cbc22786ffcc4a38e6d.png)](https://medium.com/@marcosss?source=post_page---byline--4aad72a33ca7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4aad72a33ca7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4aad72a33ca7--------------------------------) [Marcos Santiago](https://medium.com/@marcosss?source=post_page---byline--4aad72a33ca7--------------------------------)

·发表于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--4aad72a33ca7--------------------------------) ·阅读时间：7分钟·2024年10月8日

--

想象一下：

*你的 AI 系统终于完成了* ***，你对它的成果感到无比骄傲。你发布它，用户开始测试，结果…*

现在你**陷入困境**，需要手动审查每个新查询，以确保解决方案按预期工作。

随着查询越来越多，判断哪些是重复的变得更加困难，耗费了你更多的时间。

![](../Images/25b26eaabfbc733c414aa3ae4afc73eb.png)

图像由作者使用 Dall-E 创建

但是…如果你可以在不动手的情况下自动发现那些从未被问过的新查询呢？

好吧，借助余弦相似度的强大功能，我们可以做到。

这正是我们在这里要讨论的内容——为你提供清晰的理解，告诉你这些系统是如何设置的，以及你需要的工具！目标是给你提供结构，使你能够自信地应用它，节省数小时的试错时间 :)

*完全透明：这种方法可能不是唯一的途径，但经过数小时的解决问题，我发现这是最有效的。我将其分解为清晰的步骤，帮助你避免试错。*
