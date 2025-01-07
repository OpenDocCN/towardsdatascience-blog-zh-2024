# 从零开始创建你自己的提示增强器

> 原文：[https://towardsdatascience.com/create-your-own-prompt-enhancer-from-scratch-b870963a1ca0?source=collection_archive---------7-----------------------#2024-10-17](https://towardsdatascience.com/create-your-own-prompt-enhancer-from-scratch-b870963a1ca0?source=collection_archive---------7-----------------------#2024-10-17)

![](../Images/5f7386ad07429a8086b032597f05732a.png)

AI 生成的图片（Dalle-3）

## 使用 Python、Gradio 和 Groq

## 如何模拟 OpenAI 的系统提示生成器功能

[](https://medium.com/@thomas_reid?source=post_page---byline--b870963a1ca0--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--b870963a1ca0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b870963a1ca0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b870963a1ca0--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--b870963a1ca0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b870963a1ca0--------------------------------) ·10分钟阅读·2024年10月17日

--

如果你是 OpenAI 用户，你可能已经注意到，最近他们在 OpenAI Playground 网站上为其系统提示添加了一个增强功能。

现在，你可以选择要求 OpenAI 生成一个新的、详细且高度集中的提示，你可以用它替代最初的简单提示。

要测试此功能，请点击下面的链接访问 playground，并使用你的 openAI 凭据登录，如果你尚未登录的话。

[https://platform.openai.com/playground/](https://platform.openai.com/playground/)

你应该会看到如下屏幕。

![](../Images/111402c6027d77fcdce21eb04d7e0d8e.png)

来自 OpenAI 网站的图片

如果你查看 `**系统指令**` 输入框右侧的两个图标，你会看到一个像四角星的图标。这就是提示生成器，它会将你的简单提示转化为一个高度集中的详细提示。

请注意，屏幕底部的用户提示输入框并没有显示这个图标。但，秘密在于，你并不需要…
