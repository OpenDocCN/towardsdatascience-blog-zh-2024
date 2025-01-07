# 拥抱不确定性：模糊逻辑在决策中的力量

> 原文：[https://towardsdatascience.com/embracing-uncertainty-the-power-of-fuzzy-logic-in-decision-making-73abb7c30ac4?source=collection_archive---------5-----------------------#2024-10-09](https://towardsdatascience.com/embracing-uncertainty-the-power-of-fuzzy-logic-in-decision-making-73abb7c30ac4?source=collection_archive---------5-----------------------#2024-10-09)

## **探索模糊逻辑如何增强人工智能、系统思维及其在现实世界中的应用**

[](https://medium.com/@niklas_lang?source=post_page---byline--73abb7c30ac4--------------------------------)[![Niklas Lang](../Images/5fa71386db00d248438c588c5ae79c67.png)](https://medium.com/@niklas_lang?source=post_page---byline--73abb7c30ac4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--73abb7c30ac4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--73abb7c30ac4--------------------------------) [Niklas Lang](https://medium.com/@niklas_lang?source=post_page---byline--73abb7c30ac4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--73abb7c30ac4--------------------------------) ·11分钟阅读·2024年10月9日

--

![](../Images/99fa5629b07f5fd17f87b87dd539e2eb.png)

图片来自[Volodymyr Hryshchenko](https://unsplash.com/@lunarts?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

模糊逻辑是经典逻辑的扩展，使得处理不精确和不确定的数据成为可能。与传统逻辑不同，传统逻辑基于事物要么为真，要么为假的原则，而模糊逻辑则允许介于两者之间的状态。这意味着，状态可以是“部分为真”或“某种程度上为假”。

在本文中，我们详细探讨了模糊逻辑及其概念和基本原理，并尽量使用尽可能简单的例子。我们还展示了使用这种方法的优缺点，并解释了它与传统逻辑的不同之处。

# 什么是模糊逻辑？

模糊逻辑是经典布尔逻辑的扩展，除了真/假或0/1这两种状态外，它还允许那些只有部分为真或假程度的陈述，因此这些陈述的值介于0和1之间。这个概念是由洛特菲·扎德（Lotfi Zadeh）在1960年代于加利福尼亚大学发明的。他试图解决教机器说人类自然语言的问题，并发现语言不能仅仅归类为0和1。
