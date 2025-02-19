# 在多页面应用中实现 Streamlit-Authenticator

> 原文：[`towardsdatascience.com/implementing-streamlit-authenticator-across-multi-page-apps-5ad70ac315b3?source=collection_archive---------3-----------------------#2024-11-23`](https://towardsdatascience.com/implementing-streamlit-authenticator-across-multi-page-apps-5ad70ac315b3?source=collection_archive---------3-----------------------#2024-11-23)

## Streamlit-Authenticator 允许你在 Streamlit 应用中添加一种简单而强大的用户身份验证方法。

[](https://medium.com/@broepke?source=post_page---byline--5ad70ac315b3--------------------------------)![Brian Roepke](https://medium.com/@broepke?source=post_page---byline--5ad70ac315b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ad70ac315b3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ad70ac315b3--------------------------------) [Brian Roepke](https://medium.com/@broepke?source=post_page---byline--5ad70ac315b3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ad70ac315b3--------------------------------) ·7 分钟阅读·2024 年 11 月 23 日

--

![](img/26c5182019386c761a06bdffefbbc876.png)

AI 生成的图像由作者使用 HubSpot AI 制作

# 介绍

Streamlit 是一个广泛使用的工具，用于创建由数据支持的快速 Web 应用程序，但它缺乏的一个功能是管理多个用户并在他们使用应用时识别他们。

Streamlit 提供了一些基本的[文档](https://docs.streamlit.io/knowledge-base/deploy/authentication-without-sso)，介绍了如何通过在`secrets.toml`文件中使用硬编码的键值对来添加此功能，然而，这是一种非常简单的方法，你很可能会很快超越它！

幸运的是，开发者 Mohammad Khorasani 写了一个优秀的包，它以一种非常简单和优雅的方式添加了相当复杂的身份验证功能！这个包叫做[Streamlit Authenticator](https://github.com/mkhorasani/Streamlit-Authenticator)，可以在 GitHub 上找到，并可以通过你喜欢的包管理器安装，比如 PIP。

Mohammad 提供了一个不错的[示例应用](https://demo-app-v0-3-3.streamlit.app/)和单页面应用的代码，但没有提供多页面应用的示例。我决定为有兴趣使用它的任何人提供一个起始点。我还[确认](https://github.com/mkhorasani/Streamlit-Authenticator-demo/issues/1)了作者，以确保代码是正确的。
