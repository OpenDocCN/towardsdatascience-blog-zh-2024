# 用生成式人工智能提升电子商务 — 第1部分

> 原文：[https://towardsdatascience.com/enhancing-e-commerce-with-generative-ai-part-1-9e402fb30e7b?source=collection_archive---------13-----------------------#2024-07-31](https://towardsdatascience.com/enhancing-e-commerce-with-generative-ai-part-1-9e402fb30e7b?source=collection_archive---------13-----------------------#2024-07-31)

## 从数据到产品推荐

[](https://medium.com/@mina.ghashami?source=post_page---byline--9e402fb30e7b--------------------------------)[![Mina Ghashami](../Images/745f53b94f5667a485299b49913c7a21.png)](https://medium.com/@mina.ghashami?source=post_page---byline--9e402fb30e7b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9e402fb30e7b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9e402fb30e7b--------------------------------) [Mina Ghashami](https://medium.com/@mina.ghashami?source=post_page---byline--9e402fb30e7b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9e402fb30e7b--------------------------------) ·16分钟阅读·2024年7月31日

--

![](../Images/639f05553254a4e2b8989e943a2f9e28.png)

图片来自[unsplash.com](https://unsplash.com/photos/a-shopping-cart-next-to-a-sign-that-says-online-is-better-ByoLORRlUdk)

生成式人工智能正在革新各个领域企业的运作方式，电子商务也不例外。随着电子商务的不断发展，对更具可扩展性和复杂度的解决方案的需求也在增加。在这一系列文章中，我们将探讨生成式人工智能在电子商务中的各种应用。在第一部分中，我们将介绍生成式人工智能如何帮助产品推荐。我们首先使用产品的元数据来学习其代表性嵌入，并展示这些嵌入如何帮助分类物品。接着，我们根据用户的历史购买记录计算用户的嵌入。最后，我们利用学到的用户嵌入和物品嵌入来推荐产品。在系列的第二部分中，我们将探讨如何生成引人入胜的产品描述，并如何从评论中提取关键的正面和负面特征。

让我们从**第1部分**开始。这是本文的目录：

# 目录

**1\. 数据集**

+   合成数据生成

+   物品元数据数据集生成

+   用户-物品数据集生成

**3\. 学习物品的代表性嵌入**

+   数据准备与清洗
