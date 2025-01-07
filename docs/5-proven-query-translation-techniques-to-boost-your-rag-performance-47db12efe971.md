# 提升 RAG 表现的 5 种经过验证的查询翻译技术

> 原文：[`towardsdatascience.com/5-proven-query-translation-techniques-to-boost-your-rag-performance-47db12efe971?source=collection_archive---------0-----------------------#2024-08-08`](https://towardsdatascience.com/5-proven-query-translation-techniques-to-boost-your-rag-performance-47db12efe971?source=collection_archive---------0-----------------------#2024-08-08)

## 如何在面对模糊的用户输入时，获得近乎完美的 LLM 表现

[](https://thuwarakesh.medium.com/?source=post_page---byline--47db12efe971--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--47db12efe971--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47db12efe971--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47db12efe971--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--47db12efe971--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47db12efe971--------------------------------) ·10 分钟阅读·2024 年 8 月 8 日

--

![](img/aaea2442d0b67b92c9038b9002999b61.png)

图片来自[travelnow.or.crylater](https://unsplash.com/@travelnow_or_crylater?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你认为用户会向 LLM 提出完美的问题，那你就大错特错了。与其直接执行，不如我们先优化用户的问题。这就是查询翻译。

我们开发了一个应用，允许用户查询公司曾经制作的所有文档。这些文档包括 PPT、项目提案、进度更新、交付物、文档等。这一尝试非常值得注意，因为过去许多类似的尝试都未能成功。多亏了 RAGs，这一次，结果非常有希望。

我们做了一个演示，大家都很兴奋地想要使用它。最初的推出是针对一小批选定的员工。但我们注意到的结果对我们来说并不那么令人兴奋。

这一举措本应彻底改变我们的工作方式。但大多数用户只尝试了几次应用，之后便再也没有使用过。他们退出了应用，就像这是一个为小学生准备的玩具项目一样。

日志显示了令人满意的结果。然而，我们与真正使用该应用的用户进行了交谈，以确定真正的问题。我们从中学到的经验让我们开始思考如何通过*查询翻译技术*来克服用户输入中的模糊性。
