# 减少为大语言模型提供服务的 Docker 镜像大小（第一部分）

> 原文：[`towardsdatascience.com/reducing-the-size-of-docker-images-serving-llm-models-b70ee66e5a76?source=collection_archive---------6-----------------------#2024-05-03`](https://towardsdatascience.com/reducing-the-size-of-docker-images-serving-llm-models-b70ee66e5a76?source=collection_archive---------6-----------------------#2024-05-03)

## 你是否遇到过这样的问题：一个 1 GB 的基于 Transformer 的模型在使用 Docker 容器化部署时，大小竟然增加到了 8 GB？

[](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)![Michał Marcińczuk, Ph.D.](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------) [Michał Marcińczuk, Ph.D.](https://czuk.medium.com/?source=post_page---byline--b70ee66e5a76--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b70ee66e5a76--------------------------------) ·6 分钟阅读·2024 年 5 月 3 日

--

![](img/817ed50bee602682eb9c094396535e05.png)

图片由 [Dominik Lückmann](https://unsplash.com/@exdigy?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

基于 Transformer 的模型，如**BERT**、**RoBERTa**或**T5**，为许多定制的自然语言处理问题提供了最先进的解决方案。在生产环境中交付这些模型的常见方式是构建一个 Docker 镜像，提供模型的 API。该镜像封装了所需的依赖项、模型本身以及处理输入数据的代码。与大型生成模型（GenAI）相比，这些模型相对较小，**从 0.5 到 2 GB**不等。然而，当你采用直接的方式将模型部署为 Docker 镜像时，你可能会对镜像的大小感到惊讶，镜像的**大小可能达到 8 GB**。你是否曾经想过为什么目标镜像如此庞大，是否有办法减少其大小？在本文中，我将讨论为什么 Docker 镜像可能如此巨大，以及如何减少其大小。

本文中使用的 Python 脚本和 Docker 文件示例也可以在这个仓库中找到 [1]：
