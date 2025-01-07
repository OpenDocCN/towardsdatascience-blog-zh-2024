# 使用 Elementary 实现开源数据可观测性——从零到英雄（第二部分）

> 原文：[`towardsdatascience.com/opensource-data-observability-with-elementary-from-zero-to-hero-part-2-c09077ed4476?source=collection_archive---------15-----------------------#2024-09-10`](https://towardsdatascience.com/opensource-data-observability-with-elementary-from-zero-to-hero-part-2-c09077ed4476?source=collection_archive---------15-----------------------#2024-09-10)

## 带你免费提升 dbt 测试水平的指南

[](https://sezin-sezgin.medium.com/?source=post_page---byline--c09077ed4476--------------------------------)![Sezin Sezgin-Rummelsberger](https://sezin-sezgin.medium.com/?source=post_page---byline--c09077ed4476--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c09077ed4476--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c09077ed4476--------------------------------) [Sezin Sezgin-Rummelsberger](https://sezin-sezgin.medium.com/?source=post_page---byline--c09077ed4476--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c09077ed4476--------------------------------) ·阅读时间 6 分钟·2024 年 9 月 10 日

--

![](img/4436d79ec654db529fca075e12919af5.png)

图片来源：[Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/macbook-pro-with-images-of-computer-language-codes-fPkvU7RDmCo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在上一部分中，我们已在 dbt 仓库中设置了 Elementary，并希望它也能在生产环境中运行。在本部分中，我们将更详细地探讨 Elementary 中可用的测试，并通过示例说明哪些测试适用于哪些数据场景。

如果你错过了第一部分，下面是它的内容：

[开源数据可观测性与 Elementary —— 从零到英雄（第一部分）](https://sezin-sezgin.medium.com/23d5e98b68db)

在运行报告时，我们看到在 Elementary Cloud 中有一个“测试配置”标签。这是报告中一个便捷的 UI 部分，只在云端版本中可用，但我们也可以在 Elementary 的开源版本中通过 `.yaml` 文件创建测试配置。它与设置原生 dbt 测试类似，遵循类似的 dbt 层次结构，更具体的配置会覆盖更高层次的配置。

你可以设置哪些测试？Elementary 将它们分为 3 大类：模式测试、异常测试和 Python 测试。我们一起来逐个了解它们的工作原理：

## 模式测试：
