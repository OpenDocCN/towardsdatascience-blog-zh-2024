# 使用命名的 Lambda 函数进行数据分析

> 原文：[`towardsdatascience.com/data-analysis-with-named-lambda-functions-86e095153058?source=collection_archive---------8-----------------------#2024-03-13`](https://towardsdatascience.com/data-analysis-with-named-lambda-functions-86e095153058?source=collection_archive---------8-----------------------#2024-03-13)

## PYTHON 编程

## 你不应该在 Python 中使用命名的匿名函数。永远不可以。真的吗？

[](https://medium.com/@nyggus?source=post_page---byline--86e095153058--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--86e095153058--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--86e095153058--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86e095153058--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--86e095153058--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86e095153058--------------------------------) ·5 分钟阅读·2024 年 3 月 13 日

--

![](img/13d012fd2225642ab623a1f3d4582fea.png)

Lambda 函数在数据科学中可以非常有用——不仅仅是匿名的函数。照片来自[Daniel Monteiro](https://unsplash.com/@danielmonteirox?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

从技术上讲，你*不*应该使用命名的 lambda 函数，因为这就像给一个本质上是匿名的函数命名：

[](/should-you-define-named-python-functions-using-lambda-fb5806c0898b?source=post_page-----86e095153058--------------------------------) ## 你应该使用 lambda 定义命名的 Python 函数吗？

### 这样做违反了 PEP8 规范，那为什么那么多作者还建议这么做呢？

towardsdatascience.com

在实际代码中，尤其是在生产环境中，我*从不*做这样的事情——你也不应该这么做。Lambda 函数是为特定情况保留的——而这些特定情况*不*包括给匿名函数命名。

这是我在上文中提到的文章中写的内容：

> 我真的希望我已经说服了你。即使两种函数定义方式看起来对你来说都没问题——即便如此，我也*不*会使用命名的`lambda`定义。原因在于，使用它们，你并*不会*获得任何好处，同时还冒着别人不同意你的风险。如果这一点还不能说服你，请记住，这样做是违背 PEP8 规范的。

这完全正确。但……

# 数据分析代码是个例外吗？
