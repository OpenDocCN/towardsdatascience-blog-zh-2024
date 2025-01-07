# 你真的了解`__init__.py`吗？

> 原文：[https://towardsdatascience.com/can-you-even-init-py-a682d1adf4e8?source=collection_archive---------0-----------------------#2024-02-29](https://towardsdatascience.com/can-you-even-init-py-a682d1adf4e8?source=collection_archive---------0-----------------------#2024-02-29)

![](../Images/1e11dd1e3c3bb1a43a45c6f642b5a107.png)

图片由作者通过MidJourney生成，并已购买版权

## 使用`__init__.py`施展魔法

[](https://louis-chan.medium.com/?source=post_page---byline--a682d1adf4e8--------------------------------)[![Louis Chan](../Images/6d8df9a478e929dd521059631f26e081.png)](https://louis-chan.medium.com/?source=post_page---byline--a682d1adf4e8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a682d1adf4e8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a682d1adf4e8--------------------------------) [Louis Chan](https://louis-chan.medium.com/?source=post_page---byline--a682d1adf4e8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a682d1adf4e8--------------------------------) ·6分钟阅读·2024年2月29日

--

每当你尝试从不同的文件夹导入代码时，你都会放入一个空的`__init__.py`文件。这几乎已经成为大多数Python开发者——无论是初学者还是高手——的肌肉记忆。但我们真的了解`__init__.py`吗？

在这篇博客文章中，让我们深入探讨`__init__.py`是如何工作的，以及一个非空的`__init__.py`如何帮助我们作为Python开发者。

## 什么是`__init__.py`？

`__init__.py`是一个Python文件，告诉Python解释器该文件夹应被视为一个包。

与C和C++等编译型语言不同，后者的依赖项必须在使用前预先编译，Python的解释器会动态地获取你的依赖项。通过`__init__.py`来告诉Python一个文件夹包含将在其他地方使用的代码。

所以，可以把`__init__.py`看作是一个门卫。它将你的文件夹转变为一个可导入的Python包。

![](../Images/b56137d20798e7867b3b1b6de83f1c32.png)

图片由作者创建

但是`__init__.py`不仅仅是这样。当你在Python中创建一个类时，你通常也需要创建一个`__init__`函数。这将定义对象如何构造，且是…
