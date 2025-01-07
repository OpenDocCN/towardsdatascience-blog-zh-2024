# 亲身体验 OpenAI 的 Swarm 多代理框架

> 原文：[`towardsdatascience.com/hands-on-with-openai-swam-bbbffaa833e5?source=collection_archive---------4-----------------------#2024-11-05`](https://towardsdatascience.com/hands-on-with-openai-swam-bbbffaa833e5?source=collection_archive---------4-----------------------#2024-11-05)

## AI 代理编排

## 使用 Swarm，一个回归基础的框架构建多代理系统

[](https://medium.com/@alan-jones?source=post_page---byline--bbbffaa833e5--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--bbbffaa833e5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bbbffaa833e5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bbbffaa833e5--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--bbbffaa833e5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bbbffaa833e5--------------------------------) ·14 分钟阅读·2024 年 11 月 5 日

--

![](img/170fb750ca017ecbff10102a07609277.png)

图片由作者和 Bing 图像创作工具提供

有一个关于伦敦公交车的笑话：你等了很久才等到一辆，结果突然三辆一起到达。（这个笑话虽然听起来像笑话，但也是[真实的](https://en.wikipedia.org/wiki/Bus_bunching)）。

代理编排框架开始让人有这种感觉。当然，我们有来自常见厂商的解决方案，比如 LangChain 和 LlamaIndex，但新产品以惊人的频率涌现：CrewAI、MotleyCrew 和 Autogen 是我最近遇到的一些产品，但还有更多框架我甚至没有时间查看 GitHub 上的 README 文件。

现在，来自 OpenAI 这样的大公司推出了**Swarm**。因为它来自 OpenAI，我觉得应该仔细了解一下。Swarm 从其他产品的复杂性中退后一步。与其将自己呈现为一个功能齐全的解决方案，它更像是一个由 OpenAI 开发的实验性、教育性框架，旨在探索轻量级的多代理编排。看起来它的目标是让你了解工具使用的多代理系统是如何工作的，而不是提供像上述产品那样的生产就绪解决方案。
