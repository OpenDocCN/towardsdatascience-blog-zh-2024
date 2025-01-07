# 在本地PC上使用GPT4All LLM与Java创建并重构您的离线ChatGPT

> 原文：[https://towardsdatascience.com/create-and-revamp-your-own-offline-chatgpt-on-local-pc-with-gpt4all-llm-in-java-7c03996f28c8?source=collection_archive---------5-----------------------#2024-03-30](https://towardsdatascience.com/create-and-revamp-your-own-offline-chatgpt-on-local-pc-with-gpt4all-llm-in-java-7c03996f28c8?source=collection_archive---------5-----------------------#2024-03-30)

## 基于gpt4all-java-binding，并增加了与JDK 1.8的兼容性。便携、平台独立，无需互联网。

[](https://geek-cc.medium.com/?source=post_page---byline--7c03996f28c8--------------------------------)[![Charmaine Chui](../Images/8e79a096799e5cde5e57c6407777fc71.png)](https://geek-cc.medium.com/?source=post_page---byline--7c03996f28c8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7c03996f28c8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7c03996f28c8--------------------------------) [Charmaine Chui](https://geek-cc.medium.com/?source=post_page---byline--7c03996f28c8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7c03996f28c8--------------------------------) ·12分钟阅读·2024年3月30日

--

随着[**OpenAI的ChatGPT**](https://openai.com/blog/chatgpt)的问世，这项技术的采纳率和受欢迎程度急剧上升，并持续呈指数级增长至今。由此产生的范式转变已经在我们生活的多个方面有所体现；从我们的工作方式到将我们日常回答问题的默认信息来源切换。虽然大众为ChatGPT带来的便利而欢呼，但主流媒体似乎未充分报道的另一面是——**(1)** **“**[**在用户/提供方发生电力/网络故障或服务器故障时会发生什么？**](https://www.bleepingcomputer.com/news/technology/its-not-just-you-chatgpt-is-down-for-many-worldwide/)**”**，**(2)** **“如果工作场所没有互联网连接怎么办？”** 或 **(3)** **“**[**数据隐私会受到威胁吗？**](https://www.makeuseof.com/things-you-must-not-share-with-ai-chatbots/)**”** 等等。

将（2）应用于现实世界的背景，这一点尤为相关，因为已知事实是，像医疗保健和军事防御等高安全性环境中的工作人员必须在几乎没有网络访问的情况下完成大部分工作。

> 这仅仅是因为在这些高风险的环境下，任何不必要的数据泄露（例如病人健康数据）的风险都是无法承受的。
