# Delta Lake — 类型扩展

> 原文：[`towardsdatascience.com/delta-lake-type-widening-82935384a0f5?source=collection_archive---------6-----------------------#2024-04-29`](https://towardsdatascience.com/delta-lake-type-widening-82935384a0f5?source=collection_archive---------6-----------------------#2024-04-29)

## 什么是类型扩展，为什么它很重要？

[](https://medium.com/@vitorf24?source=post_page---byline--82935384a0f5--------------------------------)![Vitor Teixeira](https://medium.com/@vitorf24?source=post_page---byline--82935384a0f5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82935384a0f5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82935384a0f5--------------------------------) [Vitor Teixeira](https://medium.com/@vitorf24?source=post_page---byline--82935384a0f5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82935384a0f5--------------------------------) ·5 分钟阅读·2024 年 4 月 29 日

--

![](img/bb2aaa61d9daf044cfc42e106d1c8535.png)

图片来源：[Luca Florio](https://unsplash.com/@elleflorio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Delta Lake 正在发布一个新版本，当然，社区内每个人都在广泛期待许多功能。其中之一就是所谓的类型扩展，本文将专门解释它是什么以及为什么它有用。

> 唯一不变的就是变化 —— 赫拉克利特

*赫拉克利特*的这句话不仅适用于我们生活的世界，也适用于谈论数据，即描述数据的信息。我们正处于一个快节奏的商业环境中，在这个环境下，适应能力是跟上新要求的关键，而这些要求源自我们不断变化的世界。数据的基础结构发生变化是非常常见的现象，因此我们需要能够适应这些变化，以确保所描述的内容始终准确无误。

在 Delta Lake 中，我们使用 Delta 表来描述我们的数据，并且有几种方式可以利用当前的模式演化功能无缝地适应这些变化。我们可以：添加一个更好地描述我们实体的新列；将一个列重命名为更相关的名称；重新排列列的顺序，如果列顺序没有优化的话；删除不再需要的列；或者根据需要改变某列的类型，例如因为数字超出了原有范围（数字无法适应…）。
