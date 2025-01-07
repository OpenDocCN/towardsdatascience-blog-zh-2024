# 可扩展的文档OCR管道，使用AWS

> 原文：[https://towardsdatascience.com/scalable-ocr-pipelines-using-aws-88b3c130a1ea?source=collection_archive---------11-----------------------#2024-05-30](https://towardsdatascience.com/scalable-ocr-pipelines-using-aws-88b3c130a1ea?source=collection_archive---------11-----------------------#2024-05-30)

## 对三种不同OCR管道模式及其优缺点的调查

[](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)[![Viktor Leandersson](../Images/df4dc7e3d05659153b07167e41e011ac.png)](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------) [Viktor Leandersson](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------) ·阅读时长10分钟·2024年5月30日

--

![](../Images/efe9ce347068af3ca9494d156a9be0b1.png)

图片由作者提供

OCR（光学字符识别）系统通常被视为通过提取文档内容将其转化为计算机可读格式。然而，OCR是识别图像中仅包含文本的字符的过程，因此，任何包含更多信息的图像（或未正确旋转、倾斜等）都需要一些预处理和后处理服务，以便获得有意义的输出。实际上，大多数输入OCR管道的图像既不是纯文本，也没有正确旋转。相反，图像可能由结构化和非结构化信息组成，文本和图像并排出现。这意味着OCR管道必须具备旋转输入文档、识别兴趣区域（RoI）、检测文本、执行OCR以及验证结果的能力。

本文探讨了如何构建模型管道，以创建一个适用于生产的OCR管道，用于文档文本提取。

# 5个步骤

OCR过程可以分为以下五个步骤：

1.  **旋转与倾斜校正** — 该步骤旋转并校正图像的倾斜，以创建一个正立且矩形的页面。经典的计算机视觉技术是…
