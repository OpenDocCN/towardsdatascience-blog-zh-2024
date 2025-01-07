# 使用Yolo-NAS进行简单的物体检测

> 原文：[https://towardsdatascience.com/easy-object-detection-with-yolo-nas-51f9931be979?source=collection_archive---------4-----------------------#2024-08-03](https://towardsdatascience.com/easy-object-detection-with-yolo-nas-51f9931be979?source=collection_archive---------4-----------------------#2024-08-03)

## 学习如何使用Python和yolo-NAS进行物体检测

[](https://ivopbernardo.medium.com/?source=post_page---byline--51f9931be979--------------------------------)[![Ivo Bernardo](../Images/39887b6f3e63a67c0545e87962ad5df0.png)](https://ivopbernardo.medium.com/?source=post_page---byline--51f9931be979--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--51f9931be979--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--51f9931be979--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--51f9931be979--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--51f9931be979--------------------------------) ·阅读时间：6分钟·2024年8月3日

--

![](../Images/2460b263c9776aa2d53243fab1714f13.png)

图片来源：[googledeepmind](https://unsplash.com/pt-br/@googledeepmind) @ Unplash.com

YOLO（*You only look once*）彻底改变了计算机视觉领域。YOLO的第一个版本由Joseph Redmon等人在201[6年发布](https://arxiv.org/abs/1506.02640)，在速度和准确性方面打破了多个基准。在物体检测领域，YOLO一直是数据科学家和机器学习工程师的最爱，也是分割图像中实体的首选模型。

自从推出以来，YOLO经历了多次迭代，解决了之前版本中的若干问题，具体包括：

+   改进了底层深度学习模型的架构。

+   实现了提高性能的替代方案，例如数据增强技术。

+   将原始的YOLO代码迁移至使用*pytorch*训练和部署框架。

+   改进了小物体的检测机制。

YOLO的最新版本是YOLO v9（[https://arxiv.org/abs/2402.13616](https://arxiv.org/abs/2402.13616)）。需要注意的一点是，每个计算机视觉和物体检测模型都会根据两个参数进行评估：**准确性**（由与计算机视觉分割相关的指标定义）和**速度**（由推理中的延迟定义）。下面是计算机视觉算法评估的一个示例：
