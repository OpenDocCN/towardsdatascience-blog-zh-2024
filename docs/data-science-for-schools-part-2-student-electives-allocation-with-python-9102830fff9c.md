# 学校的数据科学，第2部分：使用Python进行学生选修课程分配

> 原文：[https://towardsdatascience.com/data-science-for-schools-part-2-student-electives-allocation-with-python-9102830fff9c?source=collection_archive---------1-----------------------#2024-06-24](https://towardsdatascience.com/data-science-for-schools-part-2-student-electives-allocation-with-python-9102830fff9c?source=collection_archive---------1-----------------------#2024-06-24)

## 是时候停止依赖`allocations_final_FINALv2.xlsx`了

[](https://medium.com/@mattchapmanmsc?source=post_page---byline--9102830fff9c--------------------------------)[![Matt Chapman](../Images/7511deb8d9ed408ece21031f6614c532.png)](https://medium.com/@mattchapmanmsc?source=post_page---byline--9102830fff9c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9102830fff9c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9102830fff9c--------------------------------) [Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--9102830fff9c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9102830fff9c--------------------------------) ·阅读时间10分钟·2024年6月24日

--

想象以下场景：你是一名教师，被要求帮助为200名学生创建一个课外“选项/选修课”项目。

每个学生选择他们的四个优先课程，你需要以最大化学生满意度的方式进行分配，同时考虑到各种约束条件（例如，选修课需要至少5名学生才能开设）。

你怎么做的？

![](../Images/125de72025669ce6c2e9678a306e9045.png)

作者提供的图片

# 学校的数据科学

在本文中，我将向你展示如何使用数据科学——特别是一种叫做线性规划的技术——来解决这个问题。

这是一个更广泛系列的一部分，我将在其中展示一些学校如何应用数据科学和人工智能来改善以下方面：

+   排课

+   评估

+   课程计划

我的目标是帮助你自动化那些枯燥的工作，从而腾出时间让教师专注于他们最擅长的事情：教学。

# 为什么我应该关心（如果我不是教师的话）？
