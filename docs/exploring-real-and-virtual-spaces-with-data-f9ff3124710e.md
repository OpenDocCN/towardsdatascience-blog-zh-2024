# 用数据探索真实与虚拟空间

> 原文：[`towardsdatascience.com/exploring-real-and-virtual-spaces-with-data-f9ff3124710e?source=collection_archive---------7-----------------------#2024-04-04`](https://towardsdatascience.com/exploring-real-and-virtual-spaces-with-data-f9ff3124710e?source=collection_archive---------7-----------------------#2024-04-04)

[](https://towardsdatascience.medium.com/?source=post_page---byline--f9ff3124710e--------------------------------)![TDS Editors](https://towardsdatascience.medium.com/?source=post_page---byline--f9ff3124710e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9ff3124710e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9ff3124710e--------------------------------) [TDS Editors](https://towardsdatascience.medium.com/?source=post_page---byline--f9ff3124710e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9ff3124710e--------------------------------) ·通过 Newsletter 发送 ·3 分钟阅读 ·2024 年 4 月 4 日

--

在地理空间数据领域，总有新的令人兴奋的领域等待探索：从帮助我们更好地理解物理地形和社会基础设施的实际应用，到允许我们在抽象空间中导航的理论方法。

自从我们在《Variable》栏目中上次讨论这个话题以来已经有一段时间了，因此本周我们很高兴分享一系列最新文章，这些文章为我们提供了关于地理空间数据涵盖的广泛应用场景的精彩 glimpses。从适合初学者的教程到更深入的理论问题，我们相信无论你的背景和经验水平如何，你都能在这里找到许多能引发兴趣的内容。

+   **使用六边形网格探索位置数据** 利用赫尔辛基城市自行车项目的多样化数据，[Sara Tähtinen](https://medium.com/u/736155dfbdfa?source=post_page---user_mention--f9ff3124710e--------------------------------) 提供了对 Uber 全球 H3 六边形网格系统的详细介绍，该系统既是“一个用户友好且实用的空间数据分析工具”，又是“通过将地理信息聚合到六边形区域来匿名化位置数据的便捷方法”。

+   **Depth Anything — 单目深度估计的基础模型**在一篇详细的论文解读中，[Sascha Kirch](https://medium.com/u/5c38dace9d5e?source=post_page---user_mention--f9ff3124710e--------------------------------) 揭示了单目深度估计的复杂性，“从 2D 图像预测 3D 空间中的距离”——这是一个要求实践者应用地理空间、计算机视觉和深度学习技能的问题，而一个新的基础模型旨在解决这个问题。

![](img/982de5205e90756fec0580a2d0f8c34e.png)

图片来自 [Karla Rivera](https://unsplash.com/@karla_rivera?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   **将卫星热图像从 1000m 缩小到 10m（Python）**有许多方法可以基于卫星图像生成强大的环境洞察，但处理这种类型的数据也带来了一些挑战。[Mahyar Aboutalebi 博士](https://medium.com/u/7e6350a085ee?source=post_page---user_mention--f9ff3124710e--------------------------------) 经常在这一主题上发布文章；他最新的教程之一专注于基于 Python 的方法，用于缩小 Sentinel-2 和 Sentinel-3 卫星拍摄的热成像图像**。**

+   [**如何在数字世界中找到自我**](https://medium.com/towards-data-science/how-to-find-yourself-in-a-digital-world-f58580a69c6a)对日益发展的机器人技术世界感到好奇吗？[Eden B.](https://medium.com/u/2e98aa8fb704?source=post_page---user_mention--f9ff3124710e--------------------------------) 的首篇 TDS 文章聚焦于机器人自我定位的能力，这对许多常见产品（如：自动驾驶汽车和配送机器人）来说是一个至关重要的要求；他们的文章进一步阐述了我们如何利用概率工具来计算定位。

+   **欧盟 Horizon H2020 资金的去向**地理空间分析可以是解答远超地理学问题的有力起点。举例来说：[Milan Janosov](https://medium.com/u/838408aa2ad4?source=post_page---user_mention--f9ff3124710e--------------------------------) 的新教程结合了数据分析、网络科学和丰富的 Python 技巧，绘制了成千上万的欧盟资助的研究和创新项目。

等等，还有更多！如果你有时间探索本周的其他话题，以下是我们最近的亮点：

+   随着大型语言模型的不断发展和改进，[Maja Pavlovic](https://medium.com/u/9b1766e00cb4?source=post_page---user_mention--f9ff3124710e--------------------------------)分享了一篇有用的回顾，概述了近期研究，这些研究探讨了这些模型有效标记数据的潜力。

+   大型语言模型的另一个新兴应用场景是标记不安全代码；[Melanie Hart Buehler](https://medium.com/u/8647d310b710?source=post_page---user_mention--f9ff3124710e--------------------------------)总结了利用 LLM 进行漏洞检测和修复的最新研究成果。

+   初次接触张量工作吗？[Eva Revear](https://medium.com/u/6aa5c0e6cd9f?source=post_page---user_mention--f9ff3124710e--------------------------------)整理了基于她最近的经验的实用经验教训，帮助你排除和调试这种无处不在的数据结构中的常见错误。

+   向量数据库是如何工作的？[Srijanie Dey 博士](https://medium.com/u/d60d06fe8655?source=post_page---user_mention--f9ff3124710e--------------------------------)的首篇 TDS 文章深入探讨了它们的内部工作原理。

+   如果你想了解另一个强大工具的同样易于理解的解释，不要错过[Wei Yi](https://medium.com/u/1b4bd5317a6e?source=post_page---user_mention--f9ff3124710e--------------------------------)的全面关于 Meta 的 Segment Anything 模型解码器的入门教程。

+   如果你想要尝试专家混合（MoE）方法，[Maxime Labonne](https://medium.com/u/dc89da634938?source=post_page---user_mention--f9ff3124710e--------------------------------)的最新教程是一个很好的资源，可以帮助你了解这种架构以及 MergeKit 工具，这是一种用于合并预训练的大型语言模型（LLM）的工具。

感谢你支持我们作者的工作！如果你感到受到启发，想加入他们的行列，何不[写下你的第一篇文章？我们期待阅读它](http://bit.ly/write-for-tds)。

直到下一个变量，

TDS 团队
