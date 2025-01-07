# 构建AI助手的10分钟模板

> 原文：[https://towardsdatascience.com/a-10-minute-template-to-build-an-ai-assistant-on-huggingface-e1c79f321d95?source=collection_archive---------2-----------------------#2024-05-05](https://towardsdatascience.com/a-10-minute-template-to-build-an-ai-assistant-on-huggingface-e1c79f321d95?source=collection_archive---------2-----------------------#2024-05-05)

## 以及你今天为什么应该开始构建自己的AI助手

[](https://nabil-alouani.medium.com/?source=post_page---byline--e1c79f321d95--------------------------------)[![Nabil Alouani](../Images/8ceea018e9b15413d318bfb710bb0011.png)](https://nabil-alouani.medium.com/?source=post_page---byline--e1c79f321d95--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e1c79f321d95--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e1c79f321d95--------------------------------) [Nabil Alouani](https://nabil-alouani.medium.com/?source=post_page---byline--e1c79f321d95--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e1c79f321d95--------------------------------) ·阅读时长10分钟·2024年5月5日

--

![](../Images/bd9b248b1f12384f90d12e83647331e5.png)

所有图片均由作者通过截图和Midjourney提供。

AI代理是[人工智能领域的下一个大趋势](https://www.technologyreview.com/2024/05/01/1091979/sam-altman-says-helpful-agents-are-poised-to-become-ais-killer-function/)。想象一下，完全自动驾驶的汽车，机器人帮你报税，还有AI导师在教你的孩子。

“嘿，Jarvis！请遛狗，预定下周末的旅行，顺便把我的收入翻倍。”

但让我们现实一点。代理——我指的是*完全*的*自主*代理——离被解决还有很长的路要走。这是因为代理涉及计划、世界模型和资源管理，而这些正是机器学习的弱项。

构建自主代理是如此困难，以至于公司们要么改变“代理”一词的定义，要么试图找到绕过难题的办法。

有些公司将两个笨重的大型语言模型（LLM）拼凑在一起，假装它们已经弄明白了代理（嗨，[AutoGPT](https://autogpt.net/)）。其他公司则展示了半成品的虚假演示，吹嘘其不成熟的功能（你好，[Devin](https://www.cognition-labs.com/introducing-devin)）。

然而，这并非全是炒作和空洞的言辞。少数几个参与者正在努力接近目标，给我们展示了代理（agents）可能的样子——而且……
