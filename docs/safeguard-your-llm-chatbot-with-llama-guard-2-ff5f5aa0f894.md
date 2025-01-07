# 使用Llama Guard 2保护您的LLM聊天机器人

> 原文：[https://towardsdatascience.com/safeguard-your-llm-chatbot-with-llama-guard-2-ff5f5aa0f894?source=collection_archive---------9-----------------------#2024-05-13](https://towardsdatascience.com/safeguard-your-llm-chatbot-with-llama-guard-2-ff5f5aa0f894?source=collection_archive---------9-----------------------#2024-05-13)

## 如何将内容审查应用到您的LLM输入和输出，以建立更负责任的AI系统

[](https://medium.com/@leoneversberg?source=post_page---byline--ff5f5aa0f894--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--ff5f5aa0f894--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ff5f5aa0f894--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ff5f5aa0f894--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--ff5f5aa0f894--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff5f5aa0f894--------------------------------) ·阅读时间9分钟·2024年5月13日

--

![](../Images/f8058b6fb5ad17e0cdf1fd34fe4db5ef.png)

Llama Guard。图像由作者使用Adobe Photoshop的AI图像生成工具创建。

# 介绍

责任AI是一个广泛的术语，自从ChatGPT和大型语言模型（LLMs）崛起以来，受到了更多的关注。

一般来说，这个术语意味着AI系统不应对人类造成伤害。就LLMs而言，这可能意味着例如语言模型不应产生仇恨性回应或协助用户进行非法活动。

Llama Guard是一个基于LLM的聊天机器人安全保护工具，由Meta开发。它是Meta的[Purple Llama](https://llama.meta.com/purple-llama/)责任AI项目的一部分。

Meta最近发布了Llama Guard 2。这是旧版Llama Guard 1的更新，后者是基于Llama 2的。

Llama Guard 2是一个经过训练的LLM，充当**二元分类器**，将用户问题以及LLM的回应分类为**不安全或安全**。该模型**基于新的Llama 3**，在接受Meta的Llama 3社区许可协议后，可以从Hugging Face自由下载[1]。
