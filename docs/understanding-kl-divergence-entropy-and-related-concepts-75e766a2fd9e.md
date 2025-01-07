# 理解 KL 散度、熵及相关概念

> 原文：[`towardsdatascience.com/understanding-kl-divergence-entropy-and-related-concepts-75e766a2fd9e?source=collection_archive---------8-----------------------#2024-10-08`](https://towardsdatascience.com/understanding-kl-divergence-entropy-and-related-concepts-75e766a2fd9e?source=collection_archive---------8-----------------------#2024-10-08)

## 信息理论、机器学习和统计学中的重要概念

[](https://saankhya.medium.com/?source=post_page---byline--75e766a2fd9e--------------------------------)![Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--75e766a2fd9e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75e766a2fd9e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75e766a2fd9e--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--75e766a2fd9e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75e766a2fd9e--------------------------------) ·阅读时间 8 分钟·2024 年 10 月 8 日

--

![](img/eea0002633893dd7eab17a08678acd3f.png)

图像由 Gemini AI 生成

# 引言

在信息理论、机器学习和统计学中，KL 散度（Kullback-Leibler 散度）是一个基础概念，帮助我们量化两个概率分布的差异。它通常用来衡量当一个概率分布用来近似另一个概率分布时，信息损失的量。本文将解释 KL 散度以及一些其他广泛使用的散度。

# KL 散度

KL 散度，也叫相对熵，是一种衡量两个概率分布（P 和 Q）之间差异的方法。它通常表示为 —

这个方程比较了真实分布 P 与近似分布 Q。假设你在使用一个为某一分布（分布 Q）优化的编码系统来压缩数据，但实际数据来自另一个分布（分布 P）。KL 散度衡量了你的编码系统的低效程度。如果 Q 接近 P，则 KL 散度较小，意味着在近似过程中损失的信息较少。如果 Q 与 P 差异较大，则 KL 散度较大，表示信息丢失较为严重……
