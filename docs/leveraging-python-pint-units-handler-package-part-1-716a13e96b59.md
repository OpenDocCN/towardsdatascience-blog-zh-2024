# 利用 Python Pint 单位处理包 — 第 1 部分

> 原文：[https://towardsdatascience.com/leveraging-python-pint-units-handler-package-part-1-716a13e96b59?source=collection_archive---------11-----------------------#2024-04-16](https://towardsdatascience.com/leveraging-python-pint-units-handler-package-part-1-716a13e96b59?source=collection_archive---------11-----------------------#2024-04-16)

## 在 Python 中操作和处理物理量

[](https://medium.com/@jodhernandezbemj?source=post_page---byline--716a13e96b59--------------------------------)[![Jose D. Hernandez-Betancur](../Images/fc2be8064501a06c2e363f6fb7d93be7.png)](https://medium.com/@jodhernandezbemj?source=post_page---byline--716a13e96b59--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--716a13e96b59--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--716a13e96b59--------------------------------) [Jose D. Hernandez-Betancur](https://medium.com/@jodhernandezbemj?source=post_page---byline--716a13e96b59--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--716a13e96b59--------------------------------) ·阅读时间：8 分钟·2024 年 4 月 16 日

--

![](../Images/2dd3881c7290d53918a0c30a6f497785.png)

图片由作者使用 Gencraft 生成。

如果你从事工程或科学领域的工作，甚至如果你是从事供应链操作、环境可持续性或任何涉及物理量（如时间、质量和长度）的领域的人，你可能会遇到需要编程处理和操作物理量的情况。作为一个数据实践者或使用 Python 的软件开发人员，你可能已经想出了像创建类似字典的查找表来进行单位转换（例如，kg 转 lb）或执行包含不同物理维度（例如，体积和时间）的操作的解决方案。Python 日益增长的生态系统的一个特点是，有各种各样的包可以帮助你实现你所需的功能。在这篇文章中，我将介绍 [Pint](https://pint.readthedocs.io/en/stable/)，一个用于在数据科学或软件项目中编程处理单位的 Python 包 🍻。我将以一种有条理的方式组织这篇文章，让你不仅理解构成 Pint 的关键元素，还能了解如何无缝集成和扩展它们，以适应你的项目 🧩。

# Pint 数量（Quantity）

Pint 包是基于面向对象编程（OOP）范式开发的。它使用对象来设置一个工具集，以“Python 风格”操作物理量。`Quantity`……
