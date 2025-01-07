# 在 EC2 上设置带 GPU 支持的 PyTorch，无需预配置的 AMI

> 原文：[`towardsdatascience.com/setting-up-pytorch-with-gpu-support-on-ec2-without-preconfigured-amis-3b101b05a765?source=collection_archive---------10-----------------------#2024-03-09`](https://towardsdatascience.com/setting-up-pytorch-with-gpu-support-on-ec2-without-preconfigured-amis-3b101b05a765?source=collection_archive---------10-----------------------#2024-03-09)

## 一种具有成本效益的方法

[](https://medium.com/@raicik.zach?source=post_page---byline--3b101b05a765--------------------------------)![Zachary Raicik](https://medium.com/@raicik.zach?source=post_page---byline--3b101b05a765--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b101b05a765--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b101b05a765--------------------------------) [Zachary Raicik](https://medium.com/@raicik.zach?source=post_page---byline--3b101b05a765--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b101b05a765--------------------------------) ·阅读时间：7 分钟·2024 年 3 月 9 日

--

![](img/611620a8c0e388194e057b4bafa6a9c0.png)

图片来源：[Adi Goldstein](https://unsplash.com/@adigold1?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

亚马逊云服务（“AWS”）的弹性计算云（“EC2”）提供了强大且可扩展的计算选项。它允许开发者访问配备高性能处理单元（如 GPU，图形处理单元）的虚拟计算环境。这些 GPU 加速了复杂机器学习模型的训练，使得在普通计算机上不可行或极其缓慢的任务变得可行。这对于深度学习模型尤为重要，因为深度学习需要大量的计算能力来处理庞大的数据集并进行复杂的计算。

当你启动 EC2 实例时，AWS 会提供从零开始配置该实例或使用预构建的 Amazon 机器镜像（AMI）的选择。预构建的 AMI 是一个包含特定用途的软件配置（操作系统、工具和应用程序）的模板。例如，你可能会使用一个为深度学习配置的预构建 AMI。

尽管预构建的 AMI 很好，但它们不是免费的，可能会增加 EC2 实例的成本。随着时间的推移，这些额外的成本可能会变得显著。通过从零开始配置 EC2 实例，你不仅能节省成本，还能对设置过程有更深的理解……
