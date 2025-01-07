# 本地运行 LLM 比你想象的更有用且更简单

> 原文：[`towardsdatascience.com/running-local-llms-is-more-useful-and-easier-than-you-think-f735631272ad?source=collection_archive---------0-----------------------#2024-07-11`](https://towardsdatascience.com/running-local-llms-is-more-useful-and-easier-than-you-think-f735631272ad?source=collection_archive---------0-----------------------#2024-07-11)

## 逐步指南：如何使用 Python 在本地运行 Llama3

[](https://guillaume-weingertner.medium.com/?source=post_page---byline--f735631272ad--------------------------------)![Guillaume Weingertner](https://guillaume-weingertner.medium.com/?source=post_page---byline--f735631272ad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f735631272ad--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f735631272ad--------------------------------) [Guillaume Weingertner](https://guillaume-weingertner.medium.com/?source=post_page---byline--f735631272ad--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f735631272ad--------------------------------) ·阅读时间：6 分钟·2024 年 7 月 11 日

--

![](img/c33e44339c2e97b84b8ec61429cda142.png)

图像由作者通过 AI 生成

# #1 为什么选择本地 LLM

ChatGPT 非常好，这一点毫无疑问，但它也有一个显著的缺点：你写的或上传的所有内容都会存储在 OpenAI 的服务器上。虽然在许多情况下这没有问题，但在处理敏感数据时，这可能会成为一个问题。

正因如此，我开始探索可以在个人计算机上本地运行的开源 LLM。事实证明，它们有更多优点，远超我的预期。

1\. **数据隐私**：您的信息保存在本地设备上。

2\. **具有成本效益**：无需订阅费用或 API 成本，免费使用。

3\. **自定义**：可以根据您的特定系统提示或数据集对模型进行微调。

4\. **离线功能**：无需互联网连接即可使用。

5\. **无限制使用**：不受外部 API 限制的约束。

> 现在，设置本地 LLM 出乎意料的简单。本文提供了逐步指南，帮助您在您的计算机上安装并运行开源模型……
