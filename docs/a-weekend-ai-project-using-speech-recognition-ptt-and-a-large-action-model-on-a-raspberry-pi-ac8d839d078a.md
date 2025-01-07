# 一个周末的AI项目：在树莓派上使用语音识别、PTT和大规模行动模型

> 原文：[https://towardsdatascience.com/a-weekend-ai-project-using-speech-recognition-ptt-and-a-large-action-model-on-a-raspberry-pi-ac8d839d078a?source=collection_archive---------1-----------------------#2024-02-03](https://towardsdatascience.com/a-weekend-ai-project-using-speech-recognition-ptt-and-a-large-action-model-on-a-raspberry-pi-ac8d839d078a?source=collection_archive---------1-----------------------#2024-02-03)

## 使用Python、Push-to-Talk、本地和云端LLM制作“类似Rabbit的原型”

[](https://dmitryelj.medium.com/?source=post_page---byline--ac8d839d078a--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--ac8d839d078a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ac8d839d078a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ac8d839d078a--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--ac8d839d078a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac8d839d078a--------------------------------) ·阅读时间16分钟·2024年2月3日

--

![](../Images/b27570c10fa29135bdb9f2a56c45bed3.png)

作者提供的图片

在2024年初，几乎所有的科技评测者都写了关于Rabbit R1的文章——这是一款首个便携式“AI助手”，售价199美元。根据作者的描述，它使用了“神经符号编程”和LAM（“大规模行动模型”）来执行各种任务。但它是如何工作的呢？嗯，最好的了解方式就是自己动手制作原型！

那些从未听说过Rabbit R1的读者，可以在YouTube上找到很多类似这样的评测：

本文也受到了[Nabil Alouani](https://medium.com/u/7e6956110712?source=post_page---user_mention--ac8d839d078a--------------------------------)的文章启发，他对Rabbit R1的制作方法做了有趣的分析：
