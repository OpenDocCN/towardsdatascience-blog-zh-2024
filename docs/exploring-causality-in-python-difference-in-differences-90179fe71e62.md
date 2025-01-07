# 使用 Python 探索因果关系。差分中的差分方法

> 原文：[`towardsdatascience.com/exploring-causality-in-python-difference-in-differences-90179fe71e62?source=collection_archive---------1-----------------------#2024-04-08`](https://towardsdatascience.com/exploring-causality-in-python-difference-in-differences-90179fe71e62?source=collection_archive---------1-----------------------#2024-04-08)

[](https://medium.com/@lukasz.szubelak?source=post_page---byline--90179fe71e62--------------------------------)![Lukasz Szubelak](https://medium.com/@lukasz.szubelak?source=post_page---byline--90179fe71e62--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--90179fe71e62--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--90179fe71e62--------------------------------) [Lukasz Szubelak](https://medium.com/@lukasz.szubelak?source=post_page---byline--90179fe71e62--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--90179fe71e62--------------------------------) ·14 分钟阅读·2024 年 4 月 8 日

--

![](img/80b21bbed51b46e2940a1a1d38827d5d.png)

图片由 [Scott Graham](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

建立因果关系是现代分析中最为重要且常被忽视的领域之一。我想在接下来的系列文章中描述并重点介绍我们因果推断研讨会中最常使用的工具。

# **因果推断基础**

让我们从定义因果推断开始。我将使用 Scott Cunningham 在*Mixtape*一书中的定义。

他将其定义为*估计事件和选择对给定结果影响的研究*。我们正在尝试建立变量之间的因果关系（我们可以称之为处理和效果）。这是许多领域中的一个普遍问题，从商业到公共政策领域都有涉及。

通常，因果关系框架的设置相对简单，包含以下内容：

+   处理组 — 接受处理的组

+   对照组 — 我们希望将其作为基准来评估处理效果的组

+   处理 — 我们希望分析的任何指向处理的活动

+   关注的结果

这种设置不仅是一个理论概念，而且是一个可以应用于广泛现实场景的实用工具。从网站优化到 A/B 测试…
