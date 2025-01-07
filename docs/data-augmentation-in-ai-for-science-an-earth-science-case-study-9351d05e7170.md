# AI在科学中的数据增强：一项地球科学案例研究

> 原文：[https://towardsdatascience.com/data-augmentation-in-ai-for-science-an-earth-science-case-study-9351d05e7170?source=collection_archive---------12-----------------------#2024-05-22](https://towardsdatascience.com/data-augmentation-in-ai-for-science-an-earth-science-case-study-9351d05e7170?source=collection_archive---------12-----------------------#2024-05-22)

## 用物理仿真增强观测数据能够提高AI模型在科学研究中的表现

[](https://medium.com/@yangwconion?source=post_page---byline--9351d05e7170--------------------------------)[![Wencong Yang, PhD](../Images/9fc2c73c81473637365adb234e2a042b.png)](https://medium.com/@yangwconion?source=post_page---byline--9351d05e7170--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9351d05e7170--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9351d05e7170--------------------------------) [Wencong Yang, PhD](https://medium.com/@yangwconion?source=post_page---byline--9351d05e7170--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9351d05e7170--------------------------------) ·6分钟阅读·2024年5月22日

--

![](../Images/f72d6505006adba942fb3b9aefae7155.png)

来源：USGS（[Unsplash](https://unsplash.com/)）

# 背景

在大数据时代，AI通过预测、仿真和知识挖掘推动科学研究。AI for Science（AI4Science）正在成为一种新的科学范式。使用AI的一个障碍是数据有限——尤其是观测的地面真实数据。幸运的是，这些数据可以通过科学界的另一种数据来源得到增强，即通过物理模型生成的模拟数据。

## 为什么使用物理仿真来增强数据？

数据增强是提高AI模型性能的强大技术。以下是结合模拟数据的三个关键原因：

![](../Images/ca0559c641ed0f32bf0b0f57bf44fbce.png)

图1. 使用模拟数据进行数据增强的好处。来源：作者。

+   **更多数据**：由于数据收集成本高，观测数据通常是有限的。模拟数据提供了一个更大规模的数据集，且成本更低。

+   **泛化**：观测数据分布不均…
