# 构建自定义自托管 Llama3 应用程序的四个简单步骤

> 原文：[`towardsdatascience.com/four-simple-steps-to-build-a-custom-self-hosted-llama3-application-8d1ef139b36f?source=collection_archive---------9-----------------------#2024-05-01`](https://towardsdatascience.com/four-simple-steps-to-build-a-custom-self-hosted-llama3-application-8d1ef139b36f?source=collection_archive---------9-----------------------#2024-05-01)

## 学习如何通过四个简单步骤构建并部署一个自定义的自托管 Llama 3 聊天助手

[](https://medium.com/@yangwconion?source=post_page---byline--8d1ef139b36f--------------------------------)![Wencong Yang, PhD](https://medium.com/@yangwconion?source=post_page---byline--8d1ef139b36f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8d1ef139b36f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d1ef139b36f--------------------------------) [Wencong Yang, PhD](https://medium.com/@yangwconion?source=post_page---byline--8d1ef139b36f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d1ef139b36f--------------------------------) ·6 分钟阅读·2024 年 5 月 1 日

--

![](img/6377b4756f041e25fec4e79577f9239a.png)

来源：作者提供（Ideogram）。

# 背景：LLM 应用程序

最近几个月，我们见证了开发大型语言模型（LLM）应用程序解决方案的激增。以下是流行的方法。

## 1\. 基于云的在线平台

像 OpenAI 的 GPT4 商店和 Huggingface Space 这样的平台允许开发人员专注于提示工程和交互设计，而无需配置硬件、环境和 Web 框架。然而，它们有以下局限性：

+   与个人或商业信息相关的**隐私**问题。

+   由于远程服务器和共享 GPU 资源池的**延迟**。

+   远程应用程序编程接口（API）调用或按需服务器的**成本**。

## 2\. 托管自托管应用程序

依赖于像[Ollama](https://github.com/ollama/ollama)+[OpenWebUI](https://github.com/open-webui/open-webui)这样的托管堆栈或框架的自托管应用程序提供了用于在本地运行各种 LLM 应用程序的现成模板。此解决方案引起了关注，因为最先进的 Llama 3（8B）模型…
