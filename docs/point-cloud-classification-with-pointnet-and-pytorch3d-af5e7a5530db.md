# 使用PointNet和PyTorch3D进行点云分类

> 原文：[https://towardsdatascience.com/point-cloud-classification-with-pointnet-and-pytorch3d-af5e7a5530db?source=collection_archive---------7-----------------------#2024-03-22](https://towardsdatascience.com/point-cloud-classification-with-pointnet-and-pytorch3d-af5e7a5530db?source=collection_archive---------7-----------------------#2024-03-22)

[](https://medium.com/@masonmcgough?source=post_page---byline--af5e7a5530db--------------------------------)[![Mason McGough](../Images/4b465e0eef1590b1f12dea23a6f688e1.png)](https://medium.com/@masonmcgough?source=post_page---byline--af5e7a5530db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af5e7a5530db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af5e7a5530db--------------------------------) [Mason McGough](https://medium.com/@masonmcgough?source=post_page---byline--af5e7a5530db--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af5e7a5530db--------------------------------) ·阅读时间：11分钟·2024年3月22日

--

![](../Images/8696378ffba981b68292b37cb3734b92.png)

来自ModelNet数据集的类别为“monitor”的物体

*通过此文章，您可以使用* [*Google Colab笔记本*](https://colab.research.google.com/drive/1zKu3IZw_ZkgIWMDplp_tilMAp1hoQL-b?usp=sharing)*来跟进学习。*

在今天这个技术迅猛发展的时代，3D技术正变得不可或缺。原型设计、[虚拟试穿](https://blog.google/products/shopping/ai-virtual-try-on-google-shopping/)、虚拟和增强现实体验、[数字双胞胎](https://blogs.nvidia.com/blog/lowes-retail-digital-twins-omniverse/)、测量、[医疗假肢](https://3d.nih.gov/collections/prosthetics)以及电影和游戏行业只是3D技术冰山一角。LinkedIn估计，到2028年，全球对3D内容的需求将超过30亿美元，并且没有放缓的迹象。从《冰雪奇缘》到《堡垒之夜》，可以说，3D模型正成为新的照片。

随着对3D数据需求的增加，对于有效分类和理解3D数据的方法的需求也在增长。2016年由斯坦福大学研究人员发明的[PointNet](https://arxiv.org/abs/1612.00593)在快速发展的机器学习领域中可谓是一个化石，然而它经受住了时间的考验。直到2023年，研究人员仍然发布了基于PointNet架构的变种，适用于各种任务，包括：

+   脉冲神经网络

+   通过多孔介质预测流体流动

+   森林场景中的垂直结构分割

+   3D人脸验证

+   基于雷达的人体活动识别

+   单孔膜冷却

+   以及更多内容
