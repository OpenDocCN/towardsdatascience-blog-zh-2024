# 利用 Python Pint 单位处理包 —— 第2部分

> 原文：[https://towardsdatascience.com/leveraging-python-pint-units-handler-package-part-2-2c5cc41d63ec?source=collection_archive---------10-----------------------#2024-07-10](https://towardsdatascience.com/leveraging-python-pint-units-handler-package-part-2-2c5cc41d63ec?source=collection_archive---------10-----------------------#2024-07-10)

## 在 Python 中创建自定义的物理量单位注册表

[](https://medium.com/@jodhernandezbemj?source=post_page---byline--2c5cc41d63ec--------------------------------)[![Jose D. Hernandez-Betancur](../Images/fc2be8064501a06c2e363f6fb7d93be7.png)](https://medium.com/@jodhernandezbemj?source=post_page---byline--2c5cc41d63ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2c5cc41d63ec--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2c5cc41d63ec--------------------------------) [Jose D. Hernandez-Betancur](https://medium.com/@jodhernandezbemj?source=post_page---byline--2c5cc41d63ec--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2c5cc41d63ec--------------------------------) ·9 分钟阅读·2024年7月10日

--

![](../Images/841e47842861009d8142b3acf6e6a188.png)

图片由作者使用 OpenAI 的 DALL-E 生成。

现实世界中的系统，如供应链，通常涉及与物理量（如质量和能量）打交道。你不必是专业的科学家或工程师，就能创建一个能够扩展的应用，并允许用户以任意单位输入物理量，而不会导致应用崩溃。Python 拥有一个强大且不断增长的生态系统，充满了各种可以轻松集成并扩展的替代方案，供你的应用使用。

在之前的一篇文章中，我介绍了 [Pint](https://pint.readthedocs.io/en/stable/) 库，它使得处理物理量变得更加简单。如果你想通过更有趣的方式学习并将编程的各个部分拼凑在一起，可以随时回到那篇文章 🧩。

[](/leveraging-python-pint-units-handler-package-part-1-716a13e96b59?source=post_page-----2c5cc41d63ec--------------------------------) [## 利用 Python Pint 单位处理包 —— 第1部分

### 在 Python 中操作和处理物理量

towardsdatascience.com](/leveraging-python-pint-units-handler-package-part-1-716a13e96b59?source=post_page-----2c5cc41d63ec--------------------------------)

本文的目标是提供更多关于 Pint 包的信息，以便我们可以创建一种方法来存储动态定义的单位，并在程序结束后仍能保留这些单位⚡💾。

# 理解单位和维度…
