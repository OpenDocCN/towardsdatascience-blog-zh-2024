# Pytest Mocking备忘单

> 原文：[https://towardsdatascience.com/pytest-mocking-cheat-sheet-c7bb06d58598?source=collection_archive---------8-----------------------#2024-03-14](https://towardsdatascience.com/pytest-mocking-cheat-sheet-c7bb06d58598?source=collection_archive---------8-----------------------#2024-03-14)

## 模拟变得简单，补丁轻松应用

[](https://kayjanwong.medium.com/?source=post_page---byline--c7bb06d58598--------------------------------)[![Kay Jan Wong](../Images/28e803eca6327d97b6aa97ee4095d7bd.png)](https://kayjanwong.medium.com/?source=post_page---byline--c7bb06d58598--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c7bb06d58598--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c7bb06d58598--------------------------------) [Kay Jan Wong](https://kayjanwong.medium.com/?source=post_page---byline--c7bb06d58598--------------------------------)

·发表于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--c7bb06d58598--------------------------------) ·阅读时间：11分钟·2024年3月14日

--

![](../Images/95ab5764cc80203effbf54558ab13c62.png)

图片来源：[Habib Beaini](https://unsplash.com/@hbeaini?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我之前写过一篇文章，在Medium上获得了超过10万次的浏览量，提供了关于`pytest`的全面概述，涵盖了设置、执行以及标记、模拟和固定装置使用等高级功能。引用之前的文章内容：

> Mocking（模拟）在单元测试中用于替换函数的返回值。它对于替代在测试环境中不应执行的操作非常有用，例如替换连接数据库并加载数据的操作，当测试环境没有相同的数据访问权限时。

关于模拟的话题还有很多。这篇文章将展示如何模拟常量、函数、类的*已初始化实例*、私有方法、魔法方法、环境变量、外部模块、固定装置，甚至是用户输入的示例！由于模拟是用插入的“虚假”值替代实际值，本文还将涉及与模拟一起使用的**有意义的断言**。

[](/pytest-with-marking-mocking-and-fixtures-in-10-minutes-678d7ccd2f70?source=post_page-----c7bb06d58598--------------------------------) [## Pytest快速入门：标记、模拟和固定装置（10分钟掌握）

### 使用Python pytest编写稳健的单元测试

towardsdatascience.com](/pytest-with-marking-mocking-and-fixtures-in-10-minutes-678d7ccd2f70?source=post_page-----c7bb06d58598--------------------------------)

# 目录
