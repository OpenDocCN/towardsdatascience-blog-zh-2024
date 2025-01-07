# requirements.txt 已过时

> 原文：[`towardsdatascience.com/ditch-requirements-use-poetry-00a936fe9b6d?source=collection_archive---------0-----------------------#2024-08-06`](https://towardsdatascience.com/ditch-requirements-use-poetry-00a936fe9b6d?source=collection_archive---------0-----------------------#2024-08-06)

## 使用 Poetry 管理 Python 项目的依赖关系和元数据

[](https://gmyrianthous.medium.com/?source=post_page---byline--00a936fe9b6d--------------------------------)![Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--00a936fe9b6d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--00a936fe9b6d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00a936fe9b6d--------------------------------) [Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--00a936fe9b6d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00a936fe9b6d--------------------------------) ·阅读时间 9 分钟·2024 年 8 月 6 日

--

![](img/5bba39a22dcab96f27d25e796f004528.png)

图片来源：[Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/green-illustration-X4PSwltIx9c?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Python 的 [标准库](https://docs.python.org/3/library/index.html) 是一组内置模块，提供了丰富的功能，旨在标准化日常编程中的基本操作。一些示例包括 *I/O* *操作*、*文本处理*、*文件压缩* 和 *数学* 操作等。这个庞大的库使得开发者无需安装额外的包，就能执行各种任务。

然而，尽管标准库功能强大，现代 Python 应用程序通常需要更多**高级**的**功能**，超出内置内容的范畴。这时，庞大的 **开源** **项目** 库就显得至关重要。

由各个团队和社区——甚至个人——管理的这些包，能够显著扩展你项目的功能，避免了**重复发明轮子**。

这些依赖项可以在 [Python 包索引](https://pypi.org/)（PyPI）上找到，任何开发者都可以轻松安装。从像 [Django](https://www.djangoproject.com/) 和 [Flask](https://flask.palletsprojects.com/en/3.0.x/) 这样的 Web 开发框架，到像 [Pandas](https://pandas.pydata.org/) 和 [Scikit-learn](https://scikit-learn.org/) 这样的数据科学库，这些包已经成为日常 Python 编程的核心部分。
