# 维度建模中的层次结构处理

> 原文：[`towardsdatascience.com/handling-hierarchies-in-dimensional-modeling-176156f20f61?source=collection_archive---------5-----------------------#2024-07-24`](https://towardsdatascience.com/handling-hierarchies-in-dimensional-modeling-176156f20f61?source=collection_archive---------5-----------------------#2024-07-24)

## 对于层次结构，有多种建模技术可供选择。那么，哪种技术在数据仓库的维度建模中表现最佳呢？如何使用这些技术处理不同类型的层次结构？让我们一探究竟。

[](https://medium.com/@krzysztof.kornel?source=post_page---byline--176156f20f61--------------------------------)![Krzysztof K. Zdeb](https://medium.com/@krzysztof.kornel?source=post_page---byline--176156f20f61--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--176156f20f61--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--176156f20f61--------------------------------) [Krzysztof K. Zdeb](https://medium.com/@krzysztof.kornel?source=post_page---byline--176156f20f61--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--176156f20f61--------------------------------) ·14 分钟阅读·2024 年 7 月 24 日

--

层次结构在数据仓库的维度建模中起着至关重要的作用，它们影响着数据分析的结构和效率。通过我在为多家公司实施数据解决方案的经验，本文探讨了处理不同类型层次结构的最佳实践和技术。通过详细的示例和实用指南，我将带领大家应对处理各种层次结构的复杂性，确保数据仓库设计的稳健性和可扩展性。

在处理层次结构时，重要的是要认识到它们的具体性以及所有相关的细微差别。因此，在深入探讨建模技术之前，让我们先看看在实际层次结构场景中可能遇到的各种问题。本文中的示例是虚构的，但灵感来源于我为一家全球制药公司实施项目时的实际案例。尽管这些示例被大大简化，但它们仍然展示了数据建模中有趣的方面。

# 示例层次结构概述

让我们考虑以下示例层次结构：某公司的内部组织结构…
