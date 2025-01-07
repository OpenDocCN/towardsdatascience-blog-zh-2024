# 认识NiceGUI：你即将成为最爱的Python UI库

> 原文：[https://towardsdatascience.com/meet-the-nicegui-your-soon-to-be-favorite-python-ui-library-fb69f14bb0ac?source=collection_archive---------0-----------------------#2024-04-16](https://towardsdatascience.com/meet-the-nicegui-your-soon-to-be-favorite-python-ui-library-fb69f14bb0ac?source=collection_archive---------0-----------------------#2024-04-16)

## 轻松快速地构建自定义网页应用

[](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--fb69f14bb0ac--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb69f14bb0ac--------------------------------) ·阅读时间8分钟·2024年4月16日

--

![](../Images/5ef8878e03ec9d8e52a6a1352cec42f0.png)

图片来源：[Alexander Schimmeck](https://unsplash.com/@alschim?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)于[Unsplash](https://unsplash.com/photos/purple-yellow-and-pink-flowers-QHmR7iiILec?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

认识NiceGUI，一个简单的基于Python的UI框架，能够与您的网页浏览器或桌面应用无缝协作。无论你是在开发小型网页应用、仪表板，还是在玩机器人项目，NiceGUI都通过其简单的界面和众多功能使之变得简单。

本文的目标是通过列出这个库的优缺点，并展示如何构建和部署一个NiceGUI应用，来说服你尝试它。（这不是一篇赞助文章，我只是喜欢这个库🙃）

**Streamlit与NiceGUI：为什么要切换？**

虽然Streamlit非常适合创建交互式应用，但处理事件和状态时可能会很棘手，特别是在较大的项目中。NiceGUI则不同，它让你直接控制状态和交互，无需额外步骤或复杂的解决方法。

**简单的状态管理**

NiceGUI使得状态管理变得简单。与Streamlit可能会意外重置状态不同，NiceGUI保持状态稳定，无论是初始状态还是用户所做的更改。你可以使用回调函数以事件驱动的方式处理用户交互，而无需因页面完全刷新而感到烦恼，也不会丢失状态数据。
