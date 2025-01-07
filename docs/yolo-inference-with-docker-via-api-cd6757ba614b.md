# 通过 API 使用 Docker 进行 YOLO 推理

> 原文：[https://towardsdatascience.com/yolo-inference-with-docker-via-api-cd6757ba614b?source=collection_archive---------2-----------------------#2024-07-19](https://towardsdatascience.com/yolo-inference-with-docker-via-api-cd6757ba614b?source=collection_archive---------2-----------------------#2024-07-19)

## 学习如何通过 API 与 Docker 协同进行物体检测推理

[](https://medium.com/@JavierMtz5?source=post_page---byline--cd6757ba614b--------------------------------)[![Javier Martínez Ojeda](../Images/5b5df4220fa64c13232c29de9b4177af.png)](https://medium.com/@JavierMtz5?source=post_page---byline--cd6757ba614b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cd6757ba614b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cd6757ba614b--------------------------------) [Javier Martínez Ojeda](https://medium.com/@JavierMtz5?source=post_page---byline--cd6757ba614b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cd6757ba614b--------------------------------) ·12 分钟阅读·2024年7月19日

--

![](../Images/a66a644b8d2d01a642d8feea6dfac0f8.png)

“通过 API 使用 Docker 进行 YOLO 推理”项目结构。图片来源：作者。

> 如果你想在没有 Premium Medium 账户的情况下阅读这篇文章，可以通过这个朋友链接进行阅读 :)
> 
> [https://www.learnml.wiki/yolo-inference-with-docker-via-api/](https://www.learnml.wiki/yolo-inference-with-docker-via-api/)

# 引言

本文将解释如何使用 Docker 运行 YOLOv8 物体检测模型的推理，并如何创建一个 REST API 以便通过它来组织推理过程。为此，本文分为三个部分：如何运行 YOLOv8 推理、如何实现 API 以及如何在 Docker 容器中运行两者。

在文章中，将展示项目所需的所有概念和组件的代码实现。完整的代码也可以在[我的 GitHub 仓库](https://github.com/JavierMtz5/YOLOv8-docker)中找到。

为了深入了解代码及其结构，并能够轻松通过几个命令使用 Docker 运行 REST API 推理，[仓库中的 README 文件](https://github.com/JavierMtz5/YOLOv8-docker/blob/main/README.md)详细解释了需要遵循的步骤、如何获取 API 文档以及项目的结构。

# YOLOv8 推理

YOLO 的诞生旨在解决平衡训练时间与准确性的难题，并实现通过……
