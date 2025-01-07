# 使用 AWS SageMaker 端点部署模型——逐步实现

> 原文：[`towardsdatascience.com/deploy-models-with-aws-sagemaker-endpoints-step-by-step-implementation-1700316afd1d?source=collection_archive---------14-----------------------#2024-08-30`](https://towardsdatascience.com/deploy-models-with-aws-sagemaker-endpoints-step-by-step-implementation-1700316afd1d?source=collection_archive---------14-----------------------#2024-08-30)

## 这是一个关于创建 SageMaker 端点并调用它的四步教程。

[](https://medium.com/@fmnobar?source=post_page---byline--1700316afd1d--------------------------------)![Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--1700316afd1d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1700316afd1d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1700316afd1d--------------------------------) [Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--1700316afd1d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1700316afd1d--------------------------------) ·阅读时间：11 分钟·2024 年 8 月 30 日

--

![](img/7e51da1b21b1ea39b00de951b3372c17.png)

图片来源：[Ayla Verschueren](https://unsplash.com/@moob?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)来自[Unsplash](https://unsplash.com/photos/white-and-brown-long-coated-dog-lying-on-black-laptop-computer-V4YuRB6o7R4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在离线实验中，我们习惯于测试各种机器学习模型，训练和/或微调它们，然后用来进行预测（即推理）。现在假设我们希望超越单纯的离线实验，向我们的客户提供访问我们出色模型的权限，使他们也能用来进行预测。在这种情况下，我们可以将我们的模型“部署”到一个 SageMaker“端点”上。然后，我们的客户可以向已部署的端点发送请求，并接收实时预测。这些端点提供了一些好处，包括：

1.  **访问权限：** 端点只是托管（或部署）模型的一个网址。因此，我们可以像使用任何其他网址一样使用它，发送请求（即负载）并接收响应（即模型预测）。

1.  **可扩展性：** 一旦端点创建完成，Amazon/AWS 将负责提供必要的计算资源来服务我们的客户。例如，假设我的笔记本只能处理每秒 10 个请求，但我预期每秒会有 10,000 个客户请求。AWS 会扩展端点并提供足够的硬件来支持所有 10,000 个请求……
