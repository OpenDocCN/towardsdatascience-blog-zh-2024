# 为什么是ETL-Zero？理解数据集成的转变

> 原文：[https://towardsdatascience.com/why-etl-zero-understanding-the-shift-in-data-integration-as-a-beginner-d0cefa244154?source=collection_archive---------0-----------------------#2024-11-11](https://towardsdatascience.com/why-etl-zero-understanding-the-shift-in-data-integration-as-a-beginner-d0cefa244154?source=collection_archive---------0-----------------------#2024-11-11)

[](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)[![Sarah Lea](../Images/09c9fa20016d1439b70b9fb6670b57a9.png)](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------) [Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--d0cefa244154--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--d0cefa244154--------------------------------) ·10分钟阅读·2024年11月11日

--

在我准备Salesforce数据云认证时，我遇到了“零ETL”这个术语。数据云提供了从其他系统（如数据仓库或数据湖）直接访问数据的可能性，或者与这些系统共享数据，而无需复制数据。Salesforce也将其描述为自带数据湖（BYOL），借用了“自带设备”（BYOD）这一术语。我想更好地理解零ETL的概念，并以易于理解的方式进行说明。

在本文中，我将向你展示如何使用Python创建一个简化的ETL流程，以更好地理解这个概念，零ETL或零复制是什么意思，以及这种新的数据集成方式如何在Salesforce数据云中实现。

> **目录**
> 
> [1) 传统ETL流程：Python初学者的逐步指南](#6649)
> 
> [2) 那么，什么是零ETL？](#d82b)
> 
> [3) 为什么是零ETL？优缺点分析](#617b)
> 
> [4) 零ETL在Salesforce数据云中是什么样的？](#48b1)
> 
> [5) 结语](http://1db1)

# 1) 传统ETL流程：Python初学者的逐步指南

如果你已经熟悉ETL和ELT流程，可以跳过这一部分。如果你对这个话题比较陌生，可以先看一下超简化的示例，更好地理解提取——转换——加载（ETL）过程。或者更好的是，亲自实践——通过应用它，你通常能更好地理解这些概念。
