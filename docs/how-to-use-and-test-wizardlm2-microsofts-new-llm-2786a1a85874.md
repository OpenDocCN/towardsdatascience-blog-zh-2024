# 如何使用和测试 WizardLM2：微软的新 LLM

> 原文：[`towardsdatascience.com/how-to-use-and-test-wizardlm2-microsofts-new-llm-2786a1a85874?source=collection_archive---------11-----------------------#2024-05-07`](https://towardsdatascience.com/how-to-use-and-test-wizardlm2-microsofts-new-llm-2786a1a85874?source=collection_archive---------11-----------------------#2024-05-07)

## 学习如何运行和测试微软的新 LLM——WizardLM2，并使用它执行任务，如问答和信息提取

[](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--2786a1a85874--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2786a1a85874--------------------------------) ·16 分钟阅读·2024 年 5 月 7 日

--

本文将讨论如何使用微软的新语言模型 WizardLM2。它还将讨论如何测试该模型（以及一般的语言模型），以获得对其性能的表面性了解。此外，我将讨论该模型的优缺点，并给出我对其表现的看法。

![](img/8008f305c3eb2c5709e6552b03fd5047.png)

ChatGPT 对运行 WizardLM2 语言模型的可视化。图像由 ChatGPT 提供。“生成一个运行 WizardLM2 语言模型的图像”提示。*ChatGPT*，4，OpenAI，2024 年 5 月 5 日。[`chat.openai.com.`](https://chat.openai.com.)

# 目录

· 动机

· 在本地实现 WizardLM2

· 测试 WizardLM2

∘ 测试简洁的问答

∘ 测试格式化响应

∘ 从上下文中测试信息提取

· 我的整体思考

· 结论

# 动机

我写这篇文章的动机是为了测试机器学习中的最新模型。为了跟上所有的进展，我关注像 [PapersWithCode](https://paperswithcode.com/)、[GitHub Trending](https://github.com/trending) 和 [HuggingFace](https://huggingface.co/blog) 这样的网站。我是通过 [HuggingFace 公告](https://huggingface.co/posts/WizardLM/329547800484476)发现这个模型的，然后…
