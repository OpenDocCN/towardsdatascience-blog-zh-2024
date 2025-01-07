# 如何使用 Taipy 在 Python 中构建多页面数据科学 Web 应用

> 原文：[`towardsdatascience.com/how-to-construct-a-multipage-data-science-web-app-in-python-with-taipy-727967680745?source=collection_archive---------2-----------------------#2024-09-05`](https://towardsdatascience.com/how-to-construct-a-multipage-data-science-web-app-in-python-with-taipy-727967680745?source=collection_archive---------2-----------------------#2024-09-05)

## Taipy 支持在 Python Web 应用中轻松进行页面间导航——我们创建了一个简单的 CO2 排放应用

[](https://medium.com/@alan-jones?source=post_page---byline--727967680745--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--727967680745--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--727967680745--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--727967680745--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--727967680745--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--727967680745--------------------------------) ·13 分钟阅读·2024 年 9 月 5 日

--

![](img/d30b6b7b901a3b729f074eab65e61bc3.png)

*照片由* [*Ria*](https://unsplash.com/@riapuskas?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) *提供，发布于* [*Unsplash*](https://unsplash.com/photos/factory-chimney-emitting-smoke-CvTaPeo3NRk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Taipy 是一个用于在 Python 中构建数据科学和 AI Web 应用的框架。因此，它是 Streamlit 或 Dash 等工具的竞争者，但与这两个产品有本质区别。

Taipy 将用户界面与程序的其他逻辑分离，并使用回调函数为用户控件添加功能。从这个角度看，它更接近于 Dash，而不是 Streamlit，因为后者的用户界面控件通常会融入到主 Python 代码中。

Dash 和 Taipy 都是基于 Flask 微框架构建的，因此不应感到惊讶的是它们之间有相似之处。不过，在 Dash 应用中，构建用户界面时的感觉更像是用 HTML（但使用 Python 函数编写），而 Taipy 则有一个额外的抽象层，让用户定义的用户控件更接近于 Streamlit 而非 Dash。

那么，Taipy 是不是兼具两全其美的优势呢？我在 [用 Taipy 构建纯 Python 数据仪表盘](https://medium.com/towards-data-science/a-data-dashboard-in-pure-python-with-taipy-bdb164a62b8b) 中写了一个介绍，探讨了如何在 Taipy 中构建 Web 应用，您可以自行判断它的易用性。
