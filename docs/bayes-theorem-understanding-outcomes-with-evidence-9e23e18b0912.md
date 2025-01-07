# 贝叶斯定理：用证据理解商业结果

> 原文：[`towardsdatascience.com/bayes-theorem-understanding-outcomes-with-evidence-9e23e18b0912?source=collection_archive---------1-----------------------#2024-12-12`](https://towardsdatascience.com/bayes-theorem-understanding-outcomes-with-evidence-9e23e18b0912?source=collection_archive---------1-----------------------#2024-12-12)

## 贝叶斯定理的实际介绍：数据科学系列中的概率（2）

[](https://medium.com/@sahn1998?source=post_page---byline--9e23e18b0912--------------------------------)![Sunghyun Ahn](https://medium.com/@sahn1998?source=post_page---byline--9e23e18b0912--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9e23e18b0912--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9e23e18b0912--------------------------------) [Sunghyun Ahn](https://medium.com/@sahn1998?source=post_page---byline--9e23e18b0912--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9e23e18b0912--------------------------------) ·阅读时间：10 分钟·2024 年 12 月 12 日

--

![](img/70de8eac55764858c50935df27bc6eee.png)

图片由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/padlocks-on-gray-steel-fence-UphDp1J3kWo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

> 如果你不是 Medium 的付费会员，我会免费提供我的文章：[朋友链接](https://medium.com/@sahn1998/bayes-theorem-understanding-outcomes-with-evidence-9e23e18b0912?sk=9768877969f35c5ba5fdb853defdbdab)

贝叶斯定理是统计学中最广泛使用和最受推崇的概念之一。它为概率理论奠定了基础，使我们能够根据新的证据修正预测或假设。

在上一篇关于[概率符号](https://medium.com/@sahn1998/probability-notations-a-way-to-write-the-likelihood-of-events-9e97233a7e1c)的文章中，我介绍了 P(B∣A)—在事件 A 已经发生的条件下，事件 B 发生的概率。

贝叶斯定理翻转了这一视角，专注于 P(A∣B)：在 B 事件发生的前提下，A 事件发生的概率。本质上，它通过**结合先验信息（已知数据）**帮助我们完善对结果的理解。

> 实际上，即使你最初的假设或估计并不完美，应用贝叶斯定理的过程也鼓励我们对未来做出更加深思熟虑和有依据的猜测！

首先，让我们来看一个受[丹尼尔·卡尼曼](https://www.jstor.org/stable/1914185)和阿莫斯·特维尔斯基的[著名研究](https://www.jstor.org/stable/1914185)启发的例子。

# 目录
