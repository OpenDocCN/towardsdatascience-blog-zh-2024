# 周末AI项目：为视力障碍人士制作视觉助手

> 原文：[https://towardsdatascience.com/a-weekend-ai-project-making-a-visual-assistant-for-people-with-vision-impairments-df0b9f0b8c23?source=collection_archive---------6-----------------------#2024-02-17](https://towardsdatascience.com/a-weekend-ai-project-making-a-visual-assistant-for-people-with-vision-impairments-df0b9f0b8c23?source=collection_archive---------6-----------------------#2024-02-17)

## 运行多模态LLaVA模型、摄像头和语音合成

[](https://dmitryelj.medium.com/?source=post_page---byline--df0b9f0b8c23--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--df0b9f0b8c23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--df0b9f0b8c23--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--df0b9f0b8c23--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--df0b9f0b8c23--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--df0b9f0b8c23--------------------------------) ·阅读时间：8分钟·2024年2月17日

--

![](../Images/0413a659f0b961e489105065af810837.png)

图片来自Enoc Valenzuela，[Unsplash](https://unsplash.com/photos/tilt-shift-photography-of-eyeglasses-with-silver-colored-frames-WJolaNbXt90)

现代大型多模态模型（LMMs）不仅能够处理文本，还能处理不同类型的数据。确实，“一图胜千言”，这种功能在与现实世界互动时至关重要。在这个“周末项目”中，我将使用一个免费的[LLaVA](https://llava-vl.github.io)（大型语言与视觉助手）模型、一台摄像头和一个语音合成器；我们将制作一个AI助手，帮助视力障碍人士。与之前的部分一样，所有组件将完全离线运行，不会产生任何云端费用。

事不宜迟，让我们开始吧！

## 组件

在这个项目中，我将使用几个组件：

+   一个[LLaVA](https://llava-vl.github.io)模型，它结合了大型语言模型和视觉编码器，并借助一个特殊的投影矩阵，使得该模型不仅能够理解文本，还能理解图像提示。我将使用[LlamaCpp](https://github.com/abetlen/llama-cpp-python)库来运行该模型（尽管它的名字中有“LLaMA”，但它不仅能运行LLaMA模型，也能运行LLaVA模型）。

+   [Streamlit](https://streamlit.io) Python库，允许我们制作交互式UI。使用摄像头，我们可以拍摄图像，并向LMM提出不同的问题…
