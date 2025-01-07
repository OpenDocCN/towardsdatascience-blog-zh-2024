# CodeLlama vs. CodeGemma：使用开放模型进行 AI 编程辅助

> 原文：[`towardsdatascience.com/codellama-vs-codegemma-using-open-models-for-ai-coding-assistance-da446c9157b8?source=collection_archive---------6-----------------------#2024-05-11`](https://towardsdatascience.com/codellama-vs-codegemma-using-open-models-for-ai-coding-assistance-da446c9157b8?source=collection_archive---------6-----------------------#2024-05-11)

## 将 7B 和 13B 模型与 IDE 和终端集成

[](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--da446c9157b8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da446c9157b8--------------------------------) ·13 分钟阅读·2024 年 5 月 11 日

--

![](img/5c7f1ce4b25814e6af62deff202137b1.png)

图片来源：AltumCode，[Unsplash](https://unsplash.com/@altumcode)

AI 编程工具市场是一个价值数十亿美元的行业。预计到 2030 年将达到 172 亿美元，而即便是今天，VS Code 或 JetBrains IDE 的 AI 插件下载量已达数百万次。但我们能否将本地模型作为免费的编程助手运行，它的表现如何呢？在本文中，我将测试两个开放模型——Code Gemma 和 Code Llama。我会将它们安装在我的电脑上，看看它们的表现如何。

不再浪费时间，直接进入正题！

## 1\. 模型

在撰写本文时，已有两个主要的开放模型可以免费下载并用于编码目的：

+   [CodeLlama](https://ai.meta.com/blog/code-llama-large-language-model-coding/)。该模型由 Meta 于 2023 年发布，提供 7B、13B、34B 和 70B 四种尺寸。提供“Base”，“Instruct”和“Python”模型。尽管有四种尺寸，但只有 7B 和 13B 模型可以在本地实际使用；其他模型过于“庞大”。

+   [CodeGemma](https://ai.google.dev/gemma/docs/codegemma)。该模型由 Google 于 2024 年发布，提供 2B 和 7B 两种尺寸。2B 模型仅用于代码补全，而 7B 模型则用于代码填充和自然语言提示。
