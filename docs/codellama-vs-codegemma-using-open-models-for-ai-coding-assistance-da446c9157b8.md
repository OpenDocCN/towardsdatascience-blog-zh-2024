# CodeLlama vs. CodeGemma：使用开放模型进行AI编程辅助

> 原文：[https://towardsdatascience.com/codellama-vs-codegemma-using-open-models-for-ai-coding-assistance-da446c9157b8?source=collection_archive---------6-----------------------#2024-05-11](https://towardsdatascience.com/codellama-vs-codegemma-using-open-models-for-ai-coding-assistance-da446c9157b8?source=collection_archive---------6-----------------------#2024-05-11)

## 将7B和13B模型与IDE和终端集成

[](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------) ·13分钟阅读·2024年5月11日

--

![](../Images/5c7f1ce4b25814e6af62deff202137b1.png)

图片来源：AltumCode，[Unsplash](https://unsplash.com/@altumcode)

AI编程工具市场是一个价值数十亿美元的行业。预计到2030年将达到172亿美元，而即便是今天，VS Code或JetBrains IDE的AI插件下载量已达数百万次。但我们能否将本地模型作为免费的编程助手运行，它的表现如何呢？在本文中，我将测试两个开放模型——Code Gemma和Code Llama。我会将它们安装在我的电脑上，看看它们的表现如何。

不再浪费时间，直接进入正题！

## 1\. 模型

在撰写本文时，已有两个主要的开放模型可以免费下载并用于编码目的：

+   [CodeLlama](https://ai.meta.com/blog/code-llama-large-language-model-coding/)。该模型由Meta于2023年发布，提供7B、13B、34B和70B四种尺寸。提供“Base”，“Instruct”和“Python”模型。尽管有四种尺寸，但只有7B和13B模型可以在本地实际使用；其他模型过于“庞大”。

+   [CodeGemma](https://ai.google.dev/gemma/docs/codegemma)。该模型由Google于2024年发布，提供2B和7B两种尺寸。2B模型仅用于代码补全，而7B模型则用于代码填充和自然语言提示。
