# 如何识别影响购买决策的因素

> 原文：[https://towardsdatascience.com/how-to-identify-factors-that-influence-purchase-decisions-89994ecafcf2?source=collection_archive---------8-----------------------#2024-09-04](https://towardsdatascience.com/how-to-identify-factors-that-influence-purchase-decisions-89994ecafcf2?source=collection_archive---------8-----------------------#2024-09-04)

## 没有什么比简洁更复杂。因子分析如何被用来揭示隐藏的因素。

[](https://medium.com/@panData?source=post_page---byline--89994ecafcf2--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--89994ecafcf2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--89994ecafcf2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--89994ecafcf2--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--89994ecafcf2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--89994ecafcf2--------------------------------) ·阅读时间19分钟·2024年9月4日

--

![](../Images/0a8d0feaa47a57f5b9fef158e0d49d7e.png)

图片由[Joshua Rawson-Harris](https://unsplash.com/@joshrh19?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **项目概述**

**和我一起想象这个场景：** 你刚刚被聘为一家零售公司的数据科学家。这家公司有多家门店，多个部门，每天销售着无数产品。作为你的第一项任务，你的经理问你：影响顾客购买决策的因素有哪些？

请注意，你的经理并没有要求你预测顾客是否会进行购买。事实上，这样的任务要简单得多。建立这样的模型是直接的。经理想要的是这个：**哪些因素影响顾客的购买决策？**——现在这就变得更复杂了。

你能想象这个情况吗？**这不仅仅是预测一个数值**，这就是我们通常在机器学习中的线性回归所做的事情。它是关于识别各种场景，**找出哪些因素推动了购买决策**。这时你就需要一种不同的技术，这正是我将在这个项目中通过因子分析——一种降维策略——引入的内容。
