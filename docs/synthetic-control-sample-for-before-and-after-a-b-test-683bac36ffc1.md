# A/B 测试的前后合成控制样本

> 原文：[`towardsdatascience.com/synthetic-control-sample-for-before-and-after-a-b-test-683bac36ffc1?source=collection_archive---------7-----------------------#2024-12-19`](https://towardsdatascience.com/synthetic-control-sample-for-before-and-after-a-b-test-683bac36ffc1?source=collection_archive---------7-----------------------#2024-12-19)

## 学习一种简单的方法，使用线性回归为您的 A/B 测试创建一个合成控制样本

[](https://gustavorsantos.medium.com/?source=post_page---byline--683bac36ffc1--------------------------------)![Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--683bac36ffc1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--683bac36ffc1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--683bac36ffc1--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--683bac36ffc1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--683bac36ffc1--------------------------------) ·阅读时间 11 分钟 ·2024 年 12 月 19 日

--

![](img/31558bdea35ecfe6f55a3cc0012aee29.png)

前后测试 | 图片由 AI 生成。Meta Llama，2024\. [`meta.ai`](https://meta.ai)

# 介绍

A/B 测试非常强大。我喜欢这种实验，因为它使我们能够比较不同结果，并确定某些事物是否表现得比其他事物更好。

A/B 测试有一种特定的类型，它加入了 *时间* 组件，即 **前后 A/B 测试**。在该测试中，比较的是某一主题在干预前后的情况。

让我们将之前的句子翻译为一个实际的例子。

一家公司想知道广告是否能推动销售增长，因此他们可以将该广告展示给实验组，并将结果与未看到广告的对照组进行比较。广告前后的差异将指示干预是否有效。

现在，有时候在干预之前无法提前计划并进行控制组和实验组的分配。

这时，合成控制样本将变得有用。通过一些统计学和机器学习技术，可以模拟如果没有干预样本会发生什么…
