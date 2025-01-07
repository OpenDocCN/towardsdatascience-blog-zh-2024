# 重新思考统计显著性

> 原文：[`towardsdatascience.com/rethinking-statistical-significance-a6150f588b9a?source=collection_archive---------7-----------------------#2024-05-22`](https://towardsdatascience.com/rethinking-statistical-significance-a6150f588b9a?source=collection_archive---------7-----------------------#2024-05-22)

## 放弃严格的界限值，无论这些界限多么严格，并提倡在科学文章中呈现原始数据，而不是推动闭合形式的、可能存在偏见的结论。

[](https://lucianosphere.medium.com/?source=post_page---byline--a6150f588b9a--------------------------------)![LucianoSphere (Luciano Abriata, PhD)](https://lucianosphere.medium.com/?source=post_page---byline--a6150f588b9a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6150f588b9a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6150f588b9a--------------------------------) [LucianoSphere (Luciano Abriata, PhD)](https://lucianosphere.medium.com/?source=post_page---byline--a6150f588b9a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6150f588b9a--------------------------------) ·阅读时间 6 分钟·2024 年 5 月 22 日

--

![](img/3d3d21b2315f1b0132643e87d188a3e6.png)

图片由[Edge2Edge Media](https://unsplash.com/@edge2edgemedia?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

统计显著性今天已成为科学研究的支柱，但我最近发现，在《自然》杂志上发布的一份由超过 800 位研究人员签署的请愿书，使我重新想起了有关统计检验的一些长期存在的观点和思考，即统计检验在许多情况下更可能破坏科学研究，而不是帮助它。

传统上，研究结果根据预设的 p 值阈值（例如 0.05、0.01 或 0.001，这些是化学和生物科学中常用的阈值，当然在其他领域的使用阈值可能不同）被分类为“显著”或“非显著”类别。这个做法的第一个问题，尤其是在非专家中表现得尤为明显，他们常常误解结果：非显著结果并不否定效应的存在；相反，它表明没有足够的确凿证据来支持这一效应。然而，除了这个问题之外，还有其他一些更普遍的问题，我将在接下来的部分讨论，这些问题即使对于专家来说也会产生影响，尤其是当存在隐性偏见和利益冲突时，这些问题可能在某些情况下被加剧……
