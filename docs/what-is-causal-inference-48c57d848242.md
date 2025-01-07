# 什么是因果推断？

> 原文：[https://towardsdatascience.com/what-is-causal-inference-48c57d848242?source=collection_archive---------5-----------------------#2024-07-22](https://towardsdatascience.com/what-is-causal-inference-48c57d848242?source=collection_archive---------5-----------------------#2024-07-22)

## 初学者的因果推断方法指南：随机对照试验、差分中的差分法、合成控制法和A/B测试

[](https://medium.com/@khinydnlin_310?source=post_page---byline--48c57d848242--------------------------------)[![Khin Yadanar Lin](../Images/1018a44583239dfd33901b6d392d257f.png)](https://medium.com/@khinydnlin_310?source=post_page---byline--48c57d848242--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--48c57d848242--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--48c57d848242--------------------------------) [Khin Yadanar Lin](https://medium.com/@khinydnlin_310?source=post_page---byline--48c57d848242--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--48c57d848242--------------------------------) ·阅读时长：11分钟·2024年7月22日

--

![](../Images/f8409c205db10358744df0ca23a00b43.png)

摄影：由[Delano Ramdas](https://unsplash.com/@delanodzr?utm_source=medium&utm_medium=referral)提供，刊登于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文面向那些希望全面了解因果关系和因果推断方法的初学者，且尽量减少数学内容的讲解。

# 什么是因果推断？

说到因果关系，我们无法避免这个经典的说法：**“相关性不意味着因果关系。”** 一个经典的例子是，尽管冰淇淋销售和溺水事件之间存在相关性，但它们并不是彼此因果关系的体现。你可能听过很多类似的例子，用以说明两者之间的区别。虽然这些例子通常是直白的，但在实际分析中，这种区别可能会变得模糊。

如果没有明确理解因果关系是如何衡量的，那么很容易得出错误的因果推断。在这方面，我经常遇到一个问题：“是的，我们知道相关性不意味着因果关系，但回归分析呢？”简短的回答是，线性回归默认情况下并不会提供任何因果性结论，除非我们采取适当的步骤——这就是因果推断方法发挥作用的地方。
