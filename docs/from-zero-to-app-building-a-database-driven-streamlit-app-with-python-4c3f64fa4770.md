# 从零到应用：使用 Python 构建数据库驱动的 Streamlit 应用

> 原文：[`towardsdatascience.com/from-zero-to-app-building-a-database-driven-streamlit-app-with-python-4c3f64fa4770?source=collection_archive---------6-----------------------#2024-09-26`](https://towardsdatascience.com/from-zero-to-app-building-a-database-driven-streamlit-app-with-python-4c3f64fa4770?source=collection_archive---------6-----------------------#2024-09-26)

## 构建一个功能性 Streamlit 应用并集成 SQLite 的初学者指南

[](https://alle-sravani.medium.com/?source=post_page---byline--4c3f64fa4770--------------------------------)![Alle Sravani](https://alle-sravani.medium.com/?source=post_page---byline--4c3f64fa4770--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c3f64fa4770--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c3f64fa4770--------------------------------) [Alle Sravani](https://alle-sravani.medium.com/?source=post_page---byline--4c3f64fa4770--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c3f64fa4770--------------------------------) ·阅读时间：5 分钟·2024 年 9 月 26 日

--

从零代码平台到复杂的编程解决方案，我们有很多选择来构建应用程序。从头开始创建一个功能性的应用可能会让人感到非常有压力。如果你是分析师，且了解 Python 和 SQL，那么你非常幸运。Python 提供了强大的库，使其非常适合那些希望快速创建交互式应用程序的人——而无需深入了解 HTML、CSS 或 JavaScript。

[**Streamlit**](https://docs.streamlit.io/) 是一个开源的 Python 库，允许你以最少的努力构建自定义 Web 应用程序。它负责前端组件的处理，让你可以专注于编写 Python 代码。通过 Streamlit 分享功能，用户也可以轻松免费地部署自己的 Streamlit 应用。[**SQLite**](https://www.sqlite.org/docs.html) 则是一个轻量级、无服务器的数据库引擎，已包含在 Python 中。它无需额外的设置，可以将应用的数据保存在本地文件中，使其极具便携性并且简单易用。

将 **Streamlit** 和 **SQLite** 结合使用，你将得到一个交互式、用户友好的应用程序，背后有一个强大的后端数据库系统。在本文中，我将引导你了解如何创建一个简单的费用管理应用，用于记录个人开销并导出所有数据以进行进一步分析。
