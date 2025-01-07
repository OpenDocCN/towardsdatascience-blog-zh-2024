# Cramér–Rao 界限

> 原文：[https://towardsdatascience.com/the-cram%C3%A9r-rao-bound-d971ec0b641c?source=collection_archive---------2-----------------------#2024-10-22](https://towardsdatascience.com/the-cram%C3%A9r-rao-bound-d971ec0b641c?source=collection_archive---------2-----------------------#2024-10-22)

![](../Images/817a8f4946ea9493af1b74d88e04652a.png)

（[CC BY-SA 4.0](https://commons.wikimedia.org/wiki/File:%D0%97%D0%B8%D0%BC%D1%81%D0%BA%D0%B0_%D1%83%D1%82%D1%80%D0%B8%D0%BD%D1%81%D0%BA%D0%B0_%D0%B8%D0%B7%D0%BC%D0%B0%D0%B3%D0%BB%D0%B8%D1%86%D0%B0_%D0%B2%D0%BE_%D0%B4%D0%BE%D0%BB%D0%B8%D0%BD%D0%B0%D1%82%D0%B0_%D0%BD%D0%B0_%D0%91%D0%BE%D0%B3%D0%BE%D0%BC%D0%B8%D0%BB%D0%B0.JPG))

## 你并不总能得到你想要的

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--d971ec0b641c--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--d971ec0b641c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d971ec0b641c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d971ec0b641c--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--d971ec0b641c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d971ec0b641c--------------------------------) ·阅读时间 17 分钟·2024年10月22日

--

有时，我们得不到我们想要的东西。有时，自然界的规律会明确禁止我们得到它，不管我们多么努力。

Cramér–Rao 界限就是这样一条规律，它设定了一个坚不可摧的天花板——对任何回归模型的预测精度设定了硬性上限，任何人都无法突破这个上限。

在本文中，我们将研究这条规律。

# 文章大纲

我将涵盖以下两个主题：

1.  **Cramér–Rao 界限是什么？** 我将介绍界限的方差形式和精度形式。逐步地，我将解析它的定义，并解释它如何整合在一起。

1.  **Cramér–Rao 界限如何应用于回归模型？** 通过使用一个实际数据集和线性回归模型，我们将研究 **Cramér–Rao 界限** 在回归建模中的适用性。

# Cramér–Rao 界限是什么？

Cramér–Rao 界限（CRB）可以通过两种方式来表示：作为方差的下限，和作为精度的上限。

当你第一次读到 Cramér–Rao 界限的定义时，可能会觉得它像是从水管里喝水一样。但不用担心……
