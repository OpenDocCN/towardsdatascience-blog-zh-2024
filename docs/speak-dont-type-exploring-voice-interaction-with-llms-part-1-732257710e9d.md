# 说话，不要打字：探索与大型语言模型（LLMs）的语音交互

> 原文：[`towardsdatascience.com/speak-dont-type-exploring-voice-interaction-with-llms-part-1-732257710e9d?source=collection_archive---------4-----------------------#2024-04-23`](https://towardsdatascience.com/speak-dont-type-exploring-voice-interaction-with-llms-part-1-732257710e9d?source=collection_archive---------4-----------------------#2024-04-23)

## 为 LLM 应用增添语音模式

[](https://medium.com/@CVxTz?source=post_page---byline--732257710e9d--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--732257710e9d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--732257710e9d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--732257710e9d--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--732257710e9d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--732257710e9d--------------------------------) ·阅读时间 6 分钟·2024 年 4 月 23 日

--

![](img/b1a5f00dff88978127a9214c8aa3c074.png)

图片由[Quino Al](https://unsplash.com/@quinoal?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/top-view-photo-of-purple-daisy--3KfR1GVKXY?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

许多 LLMs，特别是开源的 LLMs，通常仅限于处理文本，或偶尔处理带有图像的文本（大型多模态模型，LMMs）。但如果你想用声音与 LLM 进行沟通怎么办？得益于近年来强大的语音转文本开源技术的发展，这一目标变得可实现。

我们将探讨 Llama 3 与语音转文本模型的集成，所有操作都将在一个用户友好的界面中完成。这一融合使得通过语音与 LLM 进行（接近）实时的交流成为可能。我们的探索涉及选择 Llama 3 8B 作为 LLM，使用 Whisper 语音转文本模型，并利用 NiceGUI 的功能——这是一个在后端使用 FastAPI，前端使用 Vue3，并通过 socket.io 互联的框架。

阅读本文后，你将能够为 LLM 增添一种新的音频模式。这将允许你构建一个完整的端到端工作流和用户界面，使用语音来命令和提示 LLM，而不是打字。这一功能对于移动应用特别有用，因为在手机上打字可能不像在桌面上那么方便。此外，集成这一功能还可以增强你 LLM 应用的可访问性……
