# 如何使用和测试WizardLM2：微软的新LLM

> 原文：[https://towardsdatascience.com/how-to-use-and-test-wizardlm2-microsofts-new-llm-2786a1a85874?source=collection_archive---------11-----------------------#2024-05-07](https://towardsdatascience.com/how-to-use-and-test-wizardlm2-microsofts-new-llm-2786a1a85874?source=collection_archive---------11-----------------------#2024-05-07)

## 学习如何运行和测试微软的新LLM——WizardLM2，并使用它执行任务，如问答和信息提取

[](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------) ·16分钟阅读·2024年5月7日

--

本文将讨论如何使用微软的新语言模型WizardLM2。它还将讨论如何测试该模型（以及一般的语言模型），以获得对其性能的表面性了解。此外，我将讨论该模型的优缺点，并给出我对其表现的看法。

![](../Images/8008f305c3eb2c5709e6552b03fd5047.png)

ChatGPT对运行WizardLM2语言模型的可视化。图像由ChatGPT提供。“生成一个运行WizardLM2语言模型的图像”提示。*ChatGPT*，4，OpenAI，2024年5月5日。[https://chat.openai.com.](https://chat.openai.com.)

# 目录

· [动机](#0630)

· [在本地实现WizardLM2](#cb99)

· [测试WizardLM2](#51ce)

∘ [测试简洁的问答](#20d2)

∘ [测试格式化响应](#5cbb)

∘ [从上下文中测试信息提取](#ab41)

· [我的整体思考](#2204)

· [结论](#e51c)

# 动机

我写这篇文章的动机是为了测试机器学习中的最新模型。为了跟上所有的进展，我关注像 [PapersWithCode](https://paperswithcode.com/)、[GitHub Trending](https://github.com/trending) 和 [HuggingFace](https://huggingface.co/blog) 这样的网站。我是通过 [HuggingFace公告](https://huggingface.co/posts/WizardLM/329547800484476)发现这个模型的，然后…
