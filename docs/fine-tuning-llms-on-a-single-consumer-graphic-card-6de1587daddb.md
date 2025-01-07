# 在单个消费级显卡上微调大型语言模型

> 原文：[`towardsdatascience.com/fine-tuning-llms-on-a-single-consumer-graphic-card-6de1587daddb?source=collection_archive---------2-----------------------#2024-01-31`](https://towardsdatascience.com/fine-tuning-llms-on-a-single-consumer-graphic-card-6de1587daddb?source=collection_archive---------2-----------------------#2024-01-31)

## 生成性人工智能

## 从在单个消费级 GPU 上微调大型语言模型的经验中获得的教训

[](https://tamimi-naser.medium.com/?source=post_page---byline--6de1587daddb--------------------------------)![Naser Tamimi](https://tamimi-naser.medium.com/?source=post_page---byline--6de1587daddb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6de1587daddb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6de1587daddb--------------------------------) [Naser Tamimi](https://tamimi-naser.medium.com/?source=post_page---byline--6de1587daddb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6de1587daddb--------------------------------) ·10 分钟阅读·2024 年 1 月 31 日

--

![](img/6ec03b837f13b9f080ccff02926db486.png)

图片由作者提供（Midjourney）。

# 背景

当我们想到大型语言模型或任何其他生成性模型时，第一个想到的硬件就是 GPU。如果没有 GPU，许多生成性人工智能、机器学习、深度学习和数据科学的进展是不可能实现的。如果 15 年前，玩家们热衷于最新的 GPU 技术，那么今天数据科学家和机器学习工程师也与他们一起，关注这个领域的最新动态。尽管通常游戏玩家和机器学习用户关注的 GPU 和显卡是两种不同类型的。

游戏用户通常使用消费级显卡（如 NVIDIA GeForce RTX 系列 GPU），而机器学习和人工智能开发者通常关注数据中心和云计算 GPU 的新闻（如 V100、A100 或 H100）。与数据中心 GPU（通常在 40GB 到 80GB 之间）相比，游戏显卡通常具有较少的 GPU 内存（截至 2024 年 1 月最多为 24GB）。此外，它们的价格也是一个显著的差异。虽然大多数消费级显卡的价格可能高达$3000，但大多数数据中心显卡的起售价就是这个价格，并且价格轻松突破数万美元。
