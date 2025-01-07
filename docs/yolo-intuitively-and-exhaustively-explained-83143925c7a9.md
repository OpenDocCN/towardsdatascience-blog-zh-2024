# YOLO — 直观且详尽地解释

> 原文：[https://towardsdatascience.com/yolo-intuitively-and-exhaustively-explained-83143925c7a9?source=collection_archive---------0-----------------------#2024-05-31](https://towardsdatascience.com/yolo-intuitively-and-exhaustively-explained-83143925c7a9?source=collection_archive---------0-----------------------#2024-05-31)

## GorMachine Learning | 计算机视觉 | 目标检测

## 最广泛使用的目标检测模型的起源。

[](https://medium.com/@danielwarfield1?source=post_page---byline--83143925c7a9--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--83143925c7a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--83143925c7a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--83143925c7a9--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--83143925c7a9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--83143925c7a9--------------------------------) ·阅读时间21分钟·2024年5月31日

--

![](../Images/15e034ac49ec3b25f11fd8f036137837.png)

“Look Once”由Daniel Warfield使用MidJourney创作。所有图片均由作者提供，除非另有说明。

在这篇文章中，我们将讨论 YOLO，这篇开创性的论文为现代实时计算机视觉奠定了基础。我们将从一些相关概念的简要时间轴开始，然后逐步讲解 YOLO，以全面理解它是如何工作的。

**谁会觉得这篇文章有用？** 任何对计算机视觉或前沿AI技术感兴趣的人。

**这篇文章的难度如何？** 这篇文章适合技术爱好者，甚至最熟练的数据科学家也会觉得它有趣。

**前提条件：** 良好的标准神经网络工作原理理解。对卷积网络有一些初步的了解也会有所帮助。

# YOLO之前的计算机视觉简史

以下部分包含了在深入了解 YOLO 之前需要知道的有用概念和技术。如果你觉得自己已经很有信心，可以跳过。

## 计算机视觉问题的类型
