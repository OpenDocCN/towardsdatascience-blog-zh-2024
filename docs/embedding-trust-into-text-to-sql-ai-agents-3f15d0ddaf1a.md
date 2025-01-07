# 赋能数据驱动决策：在文本到 SQL 的 AI 代理中嵌入信任

> 原文：[https://towardsdatascience.com/embedding-trust-into-text-to-sql-ai-agents-3f15d0ddaf1a?source=collection_archive---------11-----------------------#2024-08-20](https://towardsdatascience.com/embedding-trust-into-text-to-sql-ai-agents-3f15d0ddaf1a?source=collection_archive---------11-----------------------#2024-08-20)

![](../Images/a4174a1284a827efb6b44987745e46a2.png)

## 简化复杂数据环境，利用可靠的 AI 代理系统帮助用户做出更好的数据驱动决策

[](https://husseinjundi.medium.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------)[![Hussein Jundi](../Images/721d74f2b902cff791715ffad7a8791f.png)](https://husseinjundi.medium.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------) [Hussein Jundi](https://husseinjundi.medium.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f15d0ddaf1a--------------------------------) ·阅读时长 16 分钟 ·2024 年 8 月 20 日

--

驱动用户[***信任和参与度***](https://hbr.org/2024/05/ais-trust-problem)的关键因素是什么，特别是在对话式数据驱动的 AI 应用中？

[组织内 AI 应用的采用数量](https://www.mckinsey.com/featured-insights/mckinsey-global-surveys)的增加，并不一定反映出更高的用户参与度评分。关于 AI 结果的[信任评分](https://kpmg.com/xx/en/home/insights/2023/09/trust-in-artificial-intelligence.html)并未达到关键的决策范围。

向此类应用程序中引入额外的组件对于推动用户的参与度和信任变得至关重要，最终帮助组织朝着更加数据驱动的决策环境迈进。

# 目录

[1- 简单性在推动参与度中的关键作用（为什么）](#0bf5)

[2- 决策变得对话化（是什么）](#0ca4)

[3- 在 AI 数据应用中建立信任（如何做）](#d28e)

[4 — 实操：使用 Gradio、Postgres 和 Langchain 构建数据聊天室](#c6b5)

+   [数据库](#c2cf)

+   [代理](#8306)

+   [工具](#7cb2)
