# 你从未见过的 3 个 Airflow 中分支的惊人用例

> 原文：[`towardsdatascience.com/3-surprising-use-cases-for-branching-in-airflow-youve-not-seen-before-058e3cb91ae0?source=collection_archive---------6-----------------------#2024-08-02`](https://towardsdatascience.com/3-surprising-use-cases-for-branching-in-airflow-youve-not-seen-before-058e3cb91ae0?source=collection_archive---------6-----------------------#2024-08-02)

![](img/f17032b8b193f43d59f3be5c1671b4c2.png)

你的数据管道可以像这棵漂亮的树一样有许多分支。照片由 [Andrew Svk](https://unsplash.com/@andrew_svk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 拍摄，来源于 [Unsplash](https://unsplash.com/photos/a-very-tall-tree-on-top-of-a-rocky-hill-MCYR8cJYQRM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 分支条件性是许多 DAG 的重要特性

[](https://medium.com/@hugolu87?source=post_page---byline--058e3cb91ae0--------------------------------)![Hugo Lu](https://medium.com/@hugolu87?source=post_page---byline--058e3cb91ae0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--058e3cb91ae0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--058e3cb91ae0--------------------------------) [Hugo Lu](https://medium.com/@hugolu87?source=post_page---byline--058e3cb91ae0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--058e3cb91ae0--------------------------------) ·4 分钟阅读·2024 年 8 月 2 日

--

# 介绍

你多久会写一个数据管道，突然希望能够做一些*有条件的*操作？某些操作*仅在*满足一组条件时才会发生？

希望不会太频繁！[Airflow](https://www.getorchestra.io/blog/airflow-added-as-connecto)已经通过 AirflowBranchPython Operator 支持了这种功能。许多其他工作流编排工具也效仿了这一做法。Prefect 有 [Conditional Flows](https://discourse.prefect.io/t/how-can-i-build-conditional-logic-within-a-flow/61)，Dagster 有 [DyanmicOutput](https://discuss.dagster.io/t/16757774/i-have-a-graph-with-conditional-branching-the-conditional-br)，而在 [Orchestra](https://getorchestra.io) 中，我们基于状态来实现分支。

这引出了最重要的问题：为什么？

那么，为什么要费心去做分支，使得管道变得比实际需要的更复杂呢？我们将看到，实际上有一些非常不可思议的用例，尤其是对于那些希望生活中有更多自动化的人来说。

# Airflow 中分支的一个简单示例

在深入探讨用例之前，我们将使用下面的代码作为参考，以便理解分支在实践中的工作原理。

```py
from airflow import DAG
from airflow.operators.dummy import DummyOperator
from…
```
