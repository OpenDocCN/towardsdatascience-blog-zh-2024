# YOLO — 手动实现

> 原文：[https://towardsdatascience.com/yolo-by-hand-4c1ba2a9f72c?source=collection_archive---------2-----------------------#2024-06-07](https://towardsdatascience.com/yolo-by-hand-4c1ba2a9f72c?source=collection_archive---------2-----------------------#2024-06-07)

## 机器学习 | 计算机视觉 | 物体检测

## YOLO 中的数学分解

[](https://medium.com/@danielwarfield1?source=post_page---byline--4c1ba2a9f72c--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--4c1ba2a9f72c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c1ba2a9f72c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4c1ba2a9f72c--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--4c1ba2a9f72c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c1ba2a9f72c--------------------------------) ·阅读时间 6 分钟·2024年6月7日

--

![](../Images/f5f2e5e5810aa881e1949532017cab2f.png)

“手动 YOLO”由 Daniel Warfield 使用 MidJourney 创作。除非另有说明，所有图像均由作者提供。

YOLO 是一个具有里程碑意义的物体检测模型，可以快速分类和定位图像中的多个物体。本文总结了 YOLO 模型中的所有关键数学操作。如果你想深入了解 YOLO 背后的直觉，查看 IAEE 文章。

[](/yolo-intuitively-and-exhaustively-explained-83143925c7a9?source=post_page-----4c1ba2a9f72c--------------------------------) [## YOLO — 直观而全面的解释

### 最广泛使用的物体检测模型的起源。

towardsdatascience.com](/yolo-intuitively-and-exhaustively-explained-83143925c7a9?source=post_page-----4c1ba2a9f72c--------------------------------)

# 第一步：定义输入

要使用 YOLO 模型，必须首先将 RGB 图像转换为 448 x 448 x 3 的张量。

![](../Images/f527329d5ace20438a37b6b185bb2aa3.png)

图像可以拉伸到正确的维度，从而构建一个 448x448x3 的张量。[狗的图片来源](https://www.pexels.com/photo/two-yellow-labrador-retriever-puppies-1108099/)。

我们将使用简化的 5 x 5 x 1 张量，以便数学表达式占用更少的空间。

![](../Images/6aeb557a7e46252e4a0ca7c5a9ae21ec.png)

输入被理想化为一个较小的 2D 矩阵。你可以参考[我关于卷积的文章](https://medium.com/towards-data-science/convolutional-networks-intuitively-and-exhaustively-explained-ab08f6353f96)，以更好地理解这如何影响数学运算。

# 第二步：层归一化
