# 为什么是 ETL-Zero？理解数据集成的转变

> 原文：[`towardsdatascience.com/why-etl-zero-understanding-the-shift-in-data-integration-as-a-beginner-d0cefa244154?source=collection_archive---------0-----------------------#2024-11-11`](https://towardsdatascience.com/why-etl-zero-understanding-the-shift-in-data-integration-as-a-beginner-d0cefa244154?source=collection_archive---------0-----------------------#2024-11-11)

[](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)![Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------)![数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------) [Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------) ·10 分钟阅读·2024 年 11 月 11 日

--

在我准备 Salesforce 数据云认证时，我遇到了“零 ETL”这个术语。数据云提供了从其他系统（如数据仓库或数据湖）直接访问数据的可能性，或者与这些系统共享数据，而无需复制数据。Salesforce 也将其描述为自带数据湖（BYOL），借用了“自带设备”（BYOD）这一术语。我想更好地理解零 ETL 的概念，并以易于理解的方式进行说明。

在本文中，我将向你展示如何使用 Python 创建一个简化的 ETL 流程，以更好地理解这个概念，零 ETL 或零复制是什么意思，以及这种新的数据集成方式如何在 Salesforce 数据云中实现。

> **目录**
> 
> 1) 传统 ETL 流程：Python 初学者的逐步指南
> 
> 2) 那么，什么是零 ETL？
> 
> 3) 为什么是零 ETL？优缺点分析
> 
> 4) 零 ETL 在 Salesforce 数据云中是什么样的？
> 
> [5) 结语](http://1db1)

# 1) 传统 ETL 流程：Python 初学者的逐步指南

如果你已经熟悉 ETL 和 ELT 流程，可以跳过这一部分。如果你对这个话题比较陌生，可以先看一下超简化的示例，更好地理解提取——转换——加载（ETL）过程。或者更好的是，亲自实践——通过应用它，你通常能更好地理解这些概念。
