# LLM 驱动的半结构化和无结构文档解析与分析

> 原文：[`towardsdatascience.com/llm-powered-parsing-and-analysis-of-semi-structured-structured-documents-f03ac92f063e?source=collection_archive---------3-----------------------#2024-08-12`](https://towardsdatascience.com/llm-powered-parsing-and-analysis-of-semi-structured-structured-documents-f03ac92f063e?source=collection_archive---------3-----------------------#2024-08-12)

## 如何从文档中提取所需的信息

[](https://medium.com/@umairali.khan?source=post_page---byline--f03ac92f063e--------------------------------)![Umair Ali Khan](https://medium.com/@umairali.khan?source=post_page---byline--f03ac92f063e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f03ac92f063e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f03ac92f063e--------------------------------) [Umair Ali Khan](https://medium.com/@umairali.khan?source=post_page---byline--f03ac92f063e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f03ac92f063e--------------------------------) ·阅读时长 17 分钟·2024 年 8 月 12 日

--

***如果你不是 Medium 会员，你可以通过*** ***这个链接******阅读完整文章。***

文档解析是分析文档内容（无结构或半结构化）以提取特定信息或将内容转化为更结构化格式的过程。文档解析的目标是将文档分解成其组成部分，并对这些部分进行解释。文档解析对于处理大量各种格式数据的组织非常有用，因为这些数据需要自动化提取。文档解析在商业中有很多应用场景，例如发票处理、法律合同分析、来自多个来源的客户反馈分析和财务报表分析等等。

在大规模语言模型（LLM）出现之前，文档解析是通过使用预定义规则，如正则表达式（Regex）来完成的。然而，这些规则缺乏灵活性，并且仅限于预定义的结构。现实世界中的文档通常存在不一致性，并且没有固定的结构或格式。这正是 LLM 在从半结构化或无结构文档中提取特定信息并进行进一步分析方面，具有巨大潜力的地方。

在本文中，我将通过一个实际的例子，解释如何自动提取所需的信息……
