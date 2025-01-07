# 注意力，注意了！

> 原文：[`towardsdatascience.com/attention-please-25b2933309f4?source=collection_archive---------8-----------------------#2024-08-29`](https://towardsdatascience.com/attention-please-25b2933309f4?source=collection_archive---------8-----------------------#2024-08-29)

## 注意力是你所需要的一切，但其范围是有限的。

[](https://dpoulopoulos.medium.com/?source=post_page---byline--25b2933309f4--------------------------------)![Dimitris Poulopoulos](https://dpoulopoulos.medium.com/?source=post_page---byline--25b2933309f4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--25b2933309f4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25b2933309f4--------------------------------) [Dimitris Poulopoulos](https://dpoulopoulos.medium.com/?source=post_page---byline--25b2933309f4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25b2933309f4--------------------------------) ·10 分钟阅读·2024 年 8 月 29 日

--

![](img/86ebfbfc89df1f076e0f6bebad8e26cb.png)

图片由[Google DeepMind](https://unsplash.com/@googledeepmind?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/a-crystal-vase-with-pink-flowers-in-it-Oy2yXvl1WLg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

> FlashAttention 第二部分：通过实际的类比、简单的视觉呈现和通俗的叙述，为注意力机制提供直观的介绍。第一部分的内容现已发布。

在上一章中，我从一个高层次的角度介绍了 FlashAttention 机制，采用了“像我五岁一样解释”（ELI5）的方法。这种方式我最为认同；我总是尽力将困难的概念与现实生活中的类比联系起来，我发现这种方式有助于长期记忆。

接下来我们教育菜单上的主菜是标准的注意力算法——这是我们如果以后想要加料，不能跳过的一道菜。先理解它，然后再改进它。这是无法绕开的。

到现在为止，你可能已经浏览了大量关于注意力机制的文章，并观看了无数的 YouTube 视频。的确，注意力机制是 AI 领域的明星，每个人都急于在某个功能上与之合作。

所以，我也跳出来分享我对这个广受欢迎概念的看法，并为一些曾启发我的资源送上致谢。我将继续使用我们经过验证的类比方式，但也会融入更具视觉化的呈现。呼应我之前的观点（冒着听起来像破碎唱片的风险……）
