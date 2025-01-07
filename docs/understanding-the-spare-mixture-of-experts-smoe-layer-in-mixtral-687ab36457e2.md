# 理解Mixtral中的稀疏专家混合（SMoE）层

> 原文：[https://towardsdatascience.com/understanding-the-spare-mixture-of-experts-smoe-layer-in-mixtral-687ab36457e2?source=collection_archive---------2-----------------------#2024-03-21](https://towardsdatascience.com/understanding-the-spare-mixture-of-experts-smoe-layer-in-mixtral-687ab36457e2?source=collection_archive---------2-----------------------#2024-03-21)

## 本博客文章将探讨《极大规模神经网络：稀疏门控专家混合层》论文的研究成果及其在Mixtral中的实现。

[](https://medium.com/@mgunton7?source=post_page---byline--687ab36457e2--------------------------------)[![Matthew Gunton](../Images/6f5a9530ad5252aa3f2fae87b3f272b1.png)](https://medium.com/@mgunton7?source=post_page---byline--687ab36457e2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--687ab36457e2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--687ab36457e2--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--687ab36457e2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--687ab36457e2--------------------------------) ·8分钟阅读·2024年3月21日

--

![](../Images/22c84ef8e930f85af40fdb3ea5507534.png)

作者提供的图片，由DALL-E生成

# 专业化的探索

当遇到困难的问题时，分而治之通常是一个有价值的解决方案。无论是亨利·福特的流水线，归并排序如何分割数组，还是社会上如何分工，使每个人专注于特定的工作，这个列表还可以继续下去！

自然地，当人们开始着手教计算机进行推理时，分解任务成机器可以处理的多个组件是有意义的——例如，数学一个组件，科学一个组件，语言一个组件，等等。

![](../Images/2f4560d6c8508122c1048e4db4625f03.png)

作者提供的图片

然而，这一想法尚未成功实现。可能是因为它与我们的大脑并非完全独立的组件类似：复杂的推理需要多种不同部分协同工作，而不是单独使用。
