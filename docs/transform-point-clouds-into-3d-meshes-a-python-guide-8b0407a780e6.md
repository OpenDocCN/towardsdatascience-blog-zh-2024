# 将点云转换为 3D 网格：Python 教程

> 原文：[`towardsdatascience.com/transform-point-clouds-into-3d-meshes-a-python-guide-8b0407a780e6?source=collection_archive---------3-----------------------#2024-11-01`](https://towardsdatascience.com/transform-point-clouds-into-3d-meshes-a-python-guide-8b0407a780e6?source=collection_archive---------3-----------------------#2024-11-01)

## 3D Python

## *学习如何* 使用 Python 从点云数据生成 3D 网格*。本教程的最后，我们将实现一个带有*Marching Cubes 算法*的 3D 建模应用程序。*

[](https://medium.com/@florentpoux?source=post_page---byline--8b0407a780e6--------------------------------)![Florent Poux, Ph.D.](https://medium.com/@florentpoux?source=post_page---byline--8b0407a780e6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b0407a780e6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b0407a780e6--------------------------------) [Florent Poux, Ph.D.](https://medium.com/@florentpoux?source=post_page---byline--8b0407a780e6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b0407a780e6--------------------------------) ·15 分钟阅读·2024 年 11 月 1 日

--

![](img/5a11d05208c4cb7e83d2d3a04ed9386d.png)

如何将任何点云转换为一个可靠的 3D 网格？© [Florent Poux](https://learngeodata.eu/)

3D 数据的世界通常是一个支离破碎的景观。

存在**点云**，它们细节丰富，但缺乏表面信息。

存在**3D 网格**，它们明确定义了表面，但通常创建起来比较复杂。

将点云转换为网格弥补了这一差距，并解锁了许多可能性，从逼真的模拟到 3D 数字环境设计。

即使你不在渲染部分使用网格，拥有它也能确保我们可以高效地模拟碰撞效果、计算 3D 建筑中的可步行空间，或处理大型场景的遮挡剔除。

那么我们该如何实现呢？如何将**任何**点云（来自摄影测量、3D 高斯点云渲染或激光扫描方法）转化为一个可靠的 3D 网格？

> 让我们使用 Python 编写一个强大的点云网格化技术，并将其打造成一个具有图形用户界面的微型 SaaS 应用程序。
