# 构建 Azure 容器应用：一个使用 Python Flask、Plotly Dash 和 Docker 的数据分析应用

> 原文：[`towardsdatascience.com/azure-container-app-a-data-analytics-webapp-with-python-flask-plotly-dash-docker-869686a558cc?source=collection_archive---------6-----------------------#2024-02-19`](https://towardsdatascience.com/azure-container-app-a-data-analytics-webapp-with-python-flask-plotly-dash-docker-869686a558cc?source=collection_archive---------6-----------------------#2024-02-19)

## 部署可扩展的数据分析 web 应用程序：利用 Flask、Dash 和 Azure 容器应用程序实现更高的灵活性

[](https://medium.com/@numersoz?source=post_page---byline--869686a558cc--------------------------------)![Mert Ersoz](https://medium.com/@numersoz?source=post_page---byline--869686a558cc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--869686a558cc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--869686a558cc--------------------------------) [Mert Ersoz](https://medium.com/@numersoz?source=post_page---byline--869686a558cc--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--869686a558cc--------------------------------) ·阅读时长 16 分钟·2024 年 2 月 19 日

--

![](img/3d39011d2f3d9bfef0078a34a5fef0ab.png)

图片由[Tima Miroshnichenko](https://www.pexels.com/@tima-miroshnichenko/)提供，来自[Pexels](https://www.pexels.com/photo/a-man-in-white-long-sleeves-looking-at-the-screen-7567522/)

在 Python 中开发数据分析 web 应用程序时，像[Plotly 的 Dash](https://plotly.com/)和[Streamlit](https://streamlit.io/)这样的框架是目前最流行的。但如何在实际应用中部署这些框架，超越教程的范畴，同时考虑到可扩展性、效率和成本，并利用最新的云计算解决方案呢？

在本文中，我们将介绍如何使用[Flask](https://flask.palletsprojects.com/)后端和 Plotly Dash 前端构建容器化应用程序，并将其通过 Docker 容器化后部署到 Microsoft Azure 的容器应用服务。我们将不会单纯依赖 Dash 的现成解决方案，而是部署一个自定义的 Flask 服务器。这种方法为应用程序提供了更多的灵活性，使 Dash 可以与其他框架一起使用，并且克服了开源 Dash 与 Dash Enterprise 相比的一些局限性。

本教程的代码示例可以在[Github](https://github.com/numersoz/azure_data_analytics_webapp)上找到。

# 微软 Azure 容器应用服务
