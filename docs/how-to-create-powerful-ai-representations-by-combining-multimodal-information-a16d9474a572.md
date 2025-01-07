# 如何通过结合多模态信息创建强大的AI表示

> 原文：[https://towardsdatascience.com/how-to-create-powerful-ai-representations-by-combining-multimodal-information-a16d9474a572?source=collection_archive---------7-----------------------#2024-04-02](https://towardsdatascience.com/how-to-create-powerful-ai-representations-by-combining-multimodal-information-a16d9474a572?source=collection_archive---------7-----------------------#2024-04-02)

## 学习如何将多模态信息融入到你的机器学习系统中

[](https://oieivind.medium.com/?source=post_page---byline--a16d9474a572--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--a16d9474a572--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a16d9474a572--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a16d9474a572--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--a16d9474a572--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a16d9474a572--------------------------------) ·阅读时间：10分钟·2024年4月2日

--

在本文中，我将讨论如何将来自不同模态的信息融入到你的机器学习系统中。这些模态可以是图像、文本或音频等信息。也可以是例如从不同角度拍摄的同一物体的几张图像。通过加入来自不同模态的信息，机器学习系统可以获得更多的信息，从而提高系统的性能。

![](../Images/934df1a90b2a11d9c1c50ea2d5009433.png)

在本文中，您将学习如何将来自不同模态的信息结合在一起。图像由ChatGPT提供。“在机器学习中结合多模态信息的图像”提示。*ChatGPT*，4，OpenAI，2024年4月1日。[https://chat.openai.com.](https://chat.openai.com.)

# 动机

我写这篇文章的动机是，我目前正在处理一个问题，涉及到来自两种不同模态的信息。第一种模态是文档的视觉信息，第二种模态是文档中的文本信息。如果单独使用文档中的视觉数据或文本数据，机器学习系统可以取得不错的表现。然而，如果只使用两种模态中的一种，你需要向机器学习系统提供尽可能多的信息，以获得最佳表现。因此，你应该结合不同的模态，以确保最佳效果……
