# 将Markdown文件嵌入Streamlit仪表板

> 原文：[https://towardsdatascience.com/embedding-markdown-files-in-a-streamlit-dashboard-ad232bc3b866?source=collection_archive---------10-----------------------#2024-05-28](https://towardsdatascience.com/embedding-markdown-files-in-a-streamlit-dashboard-ad232bc3b866?source=collection_archive---------10-----------------------#2024-05-28)

## PYTHON 编程

## 通过将较长的静态内容移动到Markdown文件中，可以简化Streamlit应用程序的代码。

[](https://medium.com/@nyggus?source=post_page---byline--ad232bc3b866--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--ad232bc3b866--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ad232bc3b866--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ad232bc3b866--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--ad232bc3b866--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ad232bc3b866--------------------------------) ·6分钟阅读·2024年5月28日

--

![](../Images/6e35d86bceef9be1eee412c57d66b3c6.png)

照片来自 [Lukas Blazek](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Streamlit提供了一个简单而高效的工具，用于在Python中创建交互式仪表板：

[](https://streamlit.io/?source=post_page-----ad232bc3b866--------------------------------) [## Streamlit * 更快速的构建和共享数据应用的方式

### Streamlit是一个开源的Python框架，专为机器学习和数据科学团队设计。用它来创建交互式数据应用……

streamlit.io](https://streamlit.io/?source=post_page-----ad232bc3b866--------------------------------)

这种交互性是仪表板如此出色的原因。然而，有时仪表板的某些部分并不是互动的。要格式化这些部分，你可以使用各种函数，其中之一是`st.markdown()`（`st`源自`import streamlit as st`）：

[](https://docs.streamlit.io/develop/api-reference/text/st.markdown?source=post_page-----ad232bc3b866--------------------------------) [## st.markdown - Streamlit 文档

### st.markdown 显示格式化为Markdown的字符串。

docs.streamlit.io](https://docs.streamlit.io/develop/api-reference/text/st.markdown?source=post_page-----ad232bc3b866--------------------------------)

它允许使用典型的Markdown语法来格式化文本，因此对我来说，它是最有用的Streamlit文本格式化函数。

如果仪表板的大部分内容都需要这样格式化，你可以通过将这些内容移至Markdown文件来简化仪表板应用程序的代码……
