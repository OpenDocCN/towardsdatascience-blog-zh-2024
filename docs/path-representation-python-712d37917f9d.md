# Python中的路径表示

> 原文：[https://towardsdatascience.com/path-representation-python-712d37917f9d?source=collection_archive---------5-----------------------#2024-08-20](https://towardsdatascience.com/path-representation-python-712d37917f9d?source=collection_archive---------5-----------------------#2024-08-20)

## 停止使用字符串表示路径，改用pathlib

[](https://gmyrianthous.medium.com/?source=post_page---byline--712d37917f9d--------------------------------)[![Giorgos Myrianthous](../Images/ff4b116e4fb9a095ce45eb064fde5af3.png)](https://gmyrianthous.medium.com/?source=post_page---byline--712d37917f9d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--712d37917f9d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--712d37917f9d--------------------------------) [Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--712d37917f9d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--712d37917f9d--------------------------------) ·阅读时间：5分钟·2024年8月20日

--

![](../Images/404512a03f445eb628ed92f113312cc2.png)

由[Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，图片来源于[Unsplash](https://unsplash.com/photos/a-pink-and-white-abstract-painting-with-horizontal-lines-1CZCGvc_DBQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

与文件系统打交道是看似微不足道的任务，但即使是经验丰富的开发者，也可能因此出其不意。我是第一个承认——我也犯过不少错误。我遇到的最常见的反模式之一就是在Python中将文件路径表示为字符串。

*是时候重新考虑这种方法了。*

在今天的文章中，我们将探讨为什么使用字符串（甚至是`os`模块）来表示文件路径**是一场灾难的前奏**。我们将深入了解最佳实践，并看到`pathlib`包如何帮助你编写更清晰、更易维护的代码。

## 为什么使用字符串表示路径是个糟糕的主意

如果你曾经参与过需要在不同操作系统上运行的项目，那么你一定知道处理路径的痛苦。不同的系统有不同的路径表示方式。基于Unix的系统（如Linux和macOS）使用正斜杠`/`，而Windows使用反斜杠`\`。这是一个小细节，但如果不小心，它会引发巨大的麻烦。

```py
# Unix (e.g. Linux, OSX, etc.)
/home/this/is/a/path/to/a/directory

# Windows
C:\home\this\is\a\path\to\a\directory
```
