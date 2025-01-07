# 让你的AI代理保持受控：跟踪、指标和日志的入门指南

> 原文：[https://towardsdatascience.com/keeping-your-ai-agent-in-check-an-introductory-guide-to-traces-metrics-and-logging-a731b57b8658?source=collection_archive---------8-----------------------#2024-11-14](https://towardsdatascience.com/keeping-your-ai-agent-in-check-an-introductory-guide-to-traces-metrics-and-logging-a731b57b8658?source=collection_archive---------8-----------------------#2024-11-14)

## 了解如何实现指标、日志和集中监控，以保持你的AI代理稳健且准备好投入生产

[](https://medium.com/@CVxTz?source=post_page---byline--a731b57b8658--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--a731b57b8658--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a731b57b8658--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a731b57b8658--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--a731b57b8658--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a731b57b8658--------------------------------) ·10分钟阅读·2024年11月14日

--

![](../Images/13551e5e37c4acae5906b74dec680db2.png)

图片来自[Rostyslav Savchyn](https://unsplash.com/@ross_savchyn?utm_source=medium&utm_medium=referral)提供，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

构建AI代理是一个令人兴奋的挑战，但仅仅部署它们并不足以确保为用户提供顺畅、稳定的体验。一旦部署，AI应用程序需要有效的监控和日志记录来保持其最佳运行状态。没有适当的可观察性工具，问题可能未被发现，即使是小的bug也可能演变成重大生产问题。

在本指南中，我们将带你了解如何为你的AI代理设置监控和日志记录，以便你能完全掌握其行为和性能。我们将探讨如何收集必要的指标，收集日志，并将这些数据集中在一个平台上。在本教程结束时，你将拥有一个基础设置，能够及早发现、诊断和解决问题，从而确保AI应用程序更加稳定和响应迅速。

完整代码可在这里获取：[https://github.com/CVxTz/opentelemetry-langgraph-langchain-example](https://github.com/CVxTz/opentelemetry-langgraph-langchain-example)

# 介绍关键工具：OpenTelemetry、New Relic 和 LangGraph
