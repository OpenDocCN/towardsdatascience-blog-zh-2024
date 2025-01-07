# 使用Python和Meshroom进行3D重建教程

> 原文：[https://towardsdatascience.com/3d-reconstruction-tutorial-with-python-and-meshroom-2aa37805ab4a?source=collection_archive---------5-----------------------#2024-07-20](https://towardsdatascience.com/3d-reconstruction-tutorial-with-python-and-meshroom-2aa37805ab4a?source=collection_archive---------5-----------------------#2024-07-20)

## 3D重建

## 快速学习如何从照片创建3D模型，并掌握使用Python + Meshroom（摄影测量法）生成点云。

[](https://medium.com/@florentpoux?source=post_page---byline--2aa37805ab4a--------------------------------)[![Florent Poux, Ph.D.](../Images/74df1e559b2edefba71ffd0d1294a251.png)](https://medium.com/@florentpoux?source=post_page---byline--2aa37805ab4a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2aa37805ab4a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2aa37805ab4a--------------------------------) [Florent Poux, Ph.D.](https://medium.com/@florentpoux?source=post_page---byline--2aa37805ab4a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2aa37805ab4a--------------------------------) ·5分钟阅读·2024年7月20日

--

你可能已经从我之前写的文章中听说过基于图像的3D重建。这是一个深度优先的示例（以备后用）：

[](/the-ultimate-guide-to-3d-reconstruction-with-photogrammetry-56155516ddc4?source=post_page-----2aa37805ab4a--------------------------------) [## 使用摄影测量法进行3D重建的终极指南

### 使用3D摄影测量法、Reality Capture、Meshroom和Blender的完整实操3D重建教程。

towardsdatascience.com](/the-ultimate-guide-to-3d-reconstruction-with-photogrammetry-56155516ddc4?source=post_page-----2aa37805ab4a--------------------------------)

今天，我想重点介绍一个“直奔主题”的阅读，确保你可以拍摄一堆照片，并在你的Python脚本中创建3D点云或3D网格，进而开发出一个很酷的体验。

如果你觉得不错，让我快速回答下面的问题：

![](../Images/030d243dbfb650b1491bdda482823f2a.png)

用于3D点云生成的工具应该选择哪一个？© [Florent Poux, Ph.D.](https://medium.com/u/8ba7bf4ad784?source=post_page---user_mention--2aa37805ab4a--------------------------------)

当你准备好时，让我们开始有趣的部分吧。

📦 **Florent的资源**：请在这里找到本教程的资源——首先是[Meshroom软件](https://alicevision.org/)及其文档。然后是[Anaconda发行版](https://docs.anaconda.com/miniconda/)，包含Python 3.9+，以便使用一些库。
