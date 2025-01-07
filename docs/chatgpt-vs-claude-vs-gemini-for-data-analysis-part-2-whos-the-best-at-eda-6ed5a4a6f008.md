# ChatGPT与Claude与Gemini在数据分析中的对决（第二部分）：谁在EDA中表现最佳？

> 原文：[https://towardsdatascience.com/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-2-whos-the-best-at-eda-6ed5a4a6f008?source=collection_archive---------3-----------------------#2024-08-20](https://towardsdatascience.com/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-2-whos-the-best-at-eda-6ed5a4a6f008?source=collection_archive---------3-----------------------#2024-08-20)

## **比较ChatGPT、Claude和Gemini在探索性数据分析（EDA）中的五大标准**

[](https://ydong029.medium.com/?source=post_page---byline--6ed5a4a6f008--------------------------------)[![Yu Dong](../Images/55c3c11c76cde72c65eb81a60384a436.png)](https://ydong029.medium.com/?source=post_page---byline--6ed5a4a6f008--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6ed5a4a6f008--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6ed5a4a6f008--------------------------------) [Yu Dong](https://ydong029.medium.com/?source=post_page---byline--6ed5a4a6f008--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6ed5a4a6f008--------------------------------) ·12分钟阅读·2024年8月20日

--

# 目录

· [Context](#c8e2)

· [What is EDA](#35f9)

· [Evaluation Criteria](#74af)

· [Problem Setup](#e766)

· [ChatGPT-4o](#1adb)

· [Claude 3.5 Sonnet](#ec43)

· [Gemini Advanced](#e632)

· [Final Results](#196a)

# 上下文

欢迎回到我的系列文章的第二篇，***ChatGPT与Claude与Gemini在数据分析中的对决*!** 在这个系列中，我的目标是将这些 AI 工具在各种数据科学和分析任务中进行比较，以帮助数据爱好者和专业人士选择最适合他们需求的 AI 助手。如果你错过了[第一篇文章](https://medium.com/towards-data-science/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-1-821086810318)，我对它们在编写和优化 SQL 查询方面的表现进行了比较——一定要去看看！

尽管2024年奥运会已经结束，但我们的AI竞赛才刚刚升温。目前，Claude 3.5 Sonnet已暂时领先！但它能保持这一地位吗，还是ChatGPT和Gemini会迎头赶上？🏆

在本文的第二部分，我们将重点讨论它们在独立进行探索性数据分析（EDA）方面的能力。作为一名数据科学家，想象一下拥有一款能够即时提供数据洞察和推荐的 AI 工具，帮助你进行新项目的分析将是多么方便……
