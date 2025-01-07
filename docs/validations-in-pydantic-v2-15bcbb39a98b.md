# Pydantic V2 中的验证

> 原文：[https://towardsdatascience.com/validations-in-pydantic-v2-15bcbb39a98b?source=collection_archive---------6-----------------------#2024-07-17](https://towardsdatascience.com/validations-in-pydantic-v2-15bcbb39a98b?source=collection_archive---------6-----------------------#2024-07-17)

## 使用字段验证器、注解验证器、字段验证器和模型验证器进行验证

[](https://kayjanwong.medium.com/?source=post_page---byline--15bcbb39a98b--------------------------------)[![Kay Jan Wong](../Images/28e803eca6327d97b6aa97ee4095d7bd.png)](https://kayjanwong.medium.com/?source=post_page---byline--15bcbb39a98b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--15bcbb39a98b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--15bcbb39a98b--------------------------------) [Kay Jan Wong](https://kayjanwong.medium.com/?source=post_page---byline--15bcbb39a98b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--15bcbb39a98b--------------------------------) ·7分钟阅读·2024年7月17日

--

![](../Images/bd2605efe60b46b99118b62a88d62303.png)

图片由 [Max Di Capua](https://unsplash.com/@maxdicapua?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Pydantic 是*Python*中最常用的数据验证库，与 FastAPI、类、数据类和函数无缝集成。数据验证指的是验证输入字段是否为合适的数据类型（并在非严格模式下自动进行数据转换），对输入字段施加简单的数字或字符限制，甚至施加自定义和复杂的约束。

在处理更大规模的类和更多验证字段时，并且验证器能够处理和修改原始输入时，了解**不同类型的验证器**及其**执行优先级**是非常重要的。

本文将讨论 Pydantic 提供的不同类型的验证，以及不同类型验证的优先执行顺序，并提供代码示例，这些内容在 Pydantic 文档中并没有详细介绍。重点将放在类的验证上，也就是 `BaseModel`。

## 目录

+   [验证类型](https://medium.com/p/15bcbb39a98b/#d692)

+   [使用字段函数进行验证](https://medium.com/p/15bcbb39a98b/#6a8b)

+   [使用注解类型进行验证](https://medium.com/p/15bcbb39a98b/#6c2b)

+   [使用字段验证器进行验证](https://medium.com/p/15bcbb39a98b/#d28b)
