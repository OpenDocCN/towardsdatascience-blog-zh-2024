# 减少为大语言模型提供服务的Docker镜像大小（第一部分）

> 原文：[https://towardsdatascience.com/reducing-the-size-of-docker-images-serving-llm-models-b70ee66e5a76?source=collection_archive---------6-----------------------#2024-05-03](https://towardsdatascience.com/reducing-the-size-of-docker-images-serving-llm-models-b70ee66e5a76?source=collection_archive---------6-----------------------#2024-05-03)

## 你是否遇到过这样的问题：一个1 GB的基于Transformer的模型在使用Docker容器化部署时，大小竟然增加到了8 GB？

[](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)[![Michał Marcińczuk, Ph.D.](../Images/74fb7b0099084be3f7a35a149471ffbd.png)](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------) [Michał Marcińczuk, Ph.D.](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------) ·6分钟阅读·2024年5月3日

--

![](../Images/817ed50bee602682eb9c094396535e05.png)

图片由 [Dominik Lückmann](https://unsplash.com/@exdigy?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

基于Transformer的模型，如**BERT**、**RoBERTa**或**T5**，为许多定制的自然语言处理问题提供了最先进的解决方案。在生产环境中交付这些模型的常见方式是构建一个Docker镜像，提供模型的API。该镜像封装了所需的依赖项、模型本身以及处理输入数据的代码。与大型生成模型（GenAI）相比，这些模型相对较小，**从0.5到2 GB**不等。然而，当你采用直接的方式将模型部署为Docker镜像时，你可能会对镜像的大小感到惊讶，镜像的**大小可能达到8 GB**。你是否曾经想过为什么目标镜像如此庞大，是否有办法减少其大小？在本文中，我将讨论为什么Docker镜像可能如此巨大，以及如何减少其大小。

本文中使用的Python脚本和Docker文件示例也可以在这个仓库中找到 [1]：
