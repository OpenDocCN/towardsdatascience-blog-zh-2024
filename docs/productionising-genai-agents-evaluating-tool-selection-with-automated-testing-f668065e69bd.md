# 生产化GenAI代理：通过自动化测试评估工具选择

> 原文：[https://towardsdatascience.com/productionising-genai-agents-evaluating-tool-selection-with-automated-testing-f668065e69bd?source=collection_archive---------6-----------------------#2024-11-22](https://towardsdatascience.com/productionising-genai-agents-evaluating-tool-selection-with-automated-testing-f668065e69bd?source=collection_archive---------6-----------------------#2024-11-22)

## 如何为现实世界应用创建可靠且可扩展的GenAI代理

[](https://heiko-hotz.medium.com/?source=post_page---byline--f668065e69bd--------------------------------)[![Heiko Hotz](../Images/d08394d46d41d5cd9e76557a463be95e.png)](https://heiko-hotz.medium.com/?source=post_page---byline--f668065e69bd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f668065e69bd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f668065e69bd--------------------------------) [Heiko Hotz](https://heiko-hotz.medium.com/?source=post_page---byline--f668065e69bd--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f668065e69bd--------------------------------) ·阅读时长17分钟·2024年11月22日

--

![](../Images/a0144d98e0cbb6a5ff74813998a134b5.png)

图片由作者提供 — 使用Flux 1.1 Pro创建

# 引言

生成型AI代理正在改变企业与用户和客户互动的方式。从个性化的旅行搜索体验到简化故障排除的虚拟助手，这些智能系统帮助公司提供更快速、更智能、更具吸引力的互动。无论是阿拉斯加航空重新构想客户预订，还是ScottsMiracle-Gro提供定制的园艺建议，AI代理已经成为不可或缺的工具。

然而，在动态环境中部署这些代理带来了自己的挑战。模型、提示和工具的频繁更新可能会意外干扰这些代理的操作。在这篇博客中，我们将探讨企业如何应对这些挑战，确保它们的AI代理保持可靠和高效。

# 本文介绍的内容是什么？

本文重点介绍了一个实际框架，解决将GenAI代理投入生产中的一个关键任务：**确保它们能够有效选择工具**。工具选择是生成型AI代理执行任务的核心，无论是检索…
