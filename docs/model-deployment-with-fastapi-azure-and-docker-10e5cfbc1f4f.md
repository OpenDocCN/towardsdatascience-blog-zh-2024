# 使用 FastAPI、Azure 和 Docker 进行模型部署

> 原文：[`towardsdatascience.com/model-deployment-with-fastapi-azure-and-docker-10e5cfbc1f4f?source=collection_archive---------2-----------------------#2024-09-28`](https://towardsdatascience.com/model-deployment-with-fastapi-azure-and-docker-10e5cfbc1f4f?source=collection_archive---------2-----------------------#2024-09-28)

## 使用 FastAPI 服务机器学习模型的完整指南

[](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--10e5cfbc1f4f--------------------------------)![Sabrine Bendimerad](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--10e5cfbc1f4f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--10e5cfbc1f4f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--10e5cfbc1f4f--------------------------------) [Sabrine Bendimerad](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--10e5cfbc1f4f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--10e5cfbc1f4f--------------------------------) ·10 分钟阅读·2024 年 9 月 28 日

--

![](img/7393956ab23646a1e4ec15b4d17af81d.png)

[pixabay.com](https://pixabay.com/)

欢迎来到我**MLOps 系列**的第三篇文章。在[第一篇文章](https://medium.com/code-like-a-girl/mlops-fundamentals-a-beginners-guide-to-docker-containerization-4f5a984e7117)中，我们探讨了 Docker 及其如何简化应用程序打包。在[第二篇文章](https://medium.com/towards-data-science/model-management-with-mlflow-azure-and-docker-2920b51a5bdd)中，我们使用**MLflow**、**Azure**和**Docker**来管理机器学习模型。现在，在这一第三部分，我们将通过构建一个**FastAPI**应用程序，将我们之前存储的模型部署到 Azure 上，从而将所有内容整合起来。这将允许我们创建一个全球可访问的预测服务！

# 什么是 API？

**API**就像一座桥梁。当你与 Python 中的库进行交互时，你就是在使用它的 API。它是一个应用程序的公开部分，你可以与之交互，而其背后的所有内容则是隐藏的。

API 通常用于与 Web 应用程序进行通信，它们提供一组返回数据的 URL（你发送带有一些参数的请求，并收到响应）。通常，数据以像 JSON 或 XML 这样的格式返回，这些格式易于解析。这与返回 HTML 的网页不同，HTML 包括渲染页面所需的信息。通过 API，你只会得到原始数据。

有些 API 是公开的，而其他的是私有的。在构建 API 时，你决定分享哪些数据，以及如何分享……
