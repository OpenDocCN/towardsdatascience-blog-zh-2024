# 认识 NiceGUI：你即将成为最爱的 Python UI 库

> 原文：[`towardsdatascience.com/meet-the-nicegui-your-soon-to-be-favorite-python-ui-library-fb69f14bb0ac?source=collection_archive---------0-----------------------#2024-04-16`](https://towardsdatascience.com/meet-the-nicegui-your-soon-to-be-favorite-python-ui-library-fb69f14bb0ac?source=collection_archive---------0-----------------------#2024-04-16)

## 轻松快速地构建自定义网页应用

[](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------) ·阅读时间 8 分钟·2024 年 4 月 16 日

--

![](img/5ef8878e03ec9d8e52a6a1352cec42f0.png)

图片来源：[Alexander Schimmeck](https://unsplash.com/@alschim?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)于[Unsplash](https://unsplash.com/photos/purple-yellow-and-pink-flowers-QHmR7iiILec?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

认识 NiceGUI，一个简单的基于 Python 的 UI 框架，能够与您的网页浏览器或桌面应用无缝协作。无论你是在开发小型网页应用、仪表板，还是在玩机器人项目，NiceGUI 都通过其简单的界面和众多功能使之变得简单。

本文的目标是通过列出这个库的优缺点，并展示如何构建和部署一个 NiceGUI 应用，来说服你尝试它。（这不是一篇赞助文章，我只是喜欢这个库🙃）

**Streamlit 与 NiceGUI：为什么要切换？**

虽然 Streamlit 非常适合创建交互式应用，但处理事件和状态时可能会很棘手，特别是在较大的项目中。NiceGUI 则不同，它让你直接控制状态和交互，无需额外步骤或复杂的解决方法。

**简单的状态管理**

NiceGUI 使得状态管理变得简单。与 Streamlit 可能会意外重置状态不同，NiceGUI 保持状态稳定，无论是初始状态还是用户所做的更改。你可以使用回调函数以事件驱动的方式处理用户交互，而无需因页面完全刷新而感到烦恼，也不会丢失状态数据。
