# 可扩展的文档 OCR 管道，使用 AWS

> 原文：[`towardsdatascience.com/scalable-ocr-pipelines-using-aws-88b3c130a1ea?source=collection_archive---------11-----------------------#2024-05-30`](https://towardsdatascience.com/scalable-ocr-pipelines-using-aws-88b3c130a1ea?source=collection_archive---------11-----------------------#2024-05-30)

## 对三种不同 OCR 管道模式及其优缺点的调查

[](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)![Viktor Leandersson](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------) [Viktor Leandersson](https://medium.com/@viktor.leandersson?source=post_page---byline--88b3c130a1ea--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--88b3c130a1ea--------------------------------) ·阅读时长 10 分钟·2024 年 5 月 30 日

--

![](img/efe9ce347068af3ca9494d156a9be0b1.png)

图片由作者提供

OCR（光学字符识别）系统通常被视为通过提取文档内容将其转化为计算机可读格式。然而，OCR 是识别图像中仅包含文本的字符的过程，因此，任何包含更多信息的图像（或未正确旋转、倾斜等）都需要一些预处理和后处理服务，以便获得有意义的输出。实际上，大多数输入 OCR 管道的图像既不是纯文本，也没有正确旋转。相反，图像可能由结构化和非结构化信息组成，文本和图像并排出现。这意味着 OCR 管道必须具备旋转输入文档、识别兴趣区域（RoI）、检测文本、执行 OCR 以及验证结果的能力。

本文探讨了如何构建模型管道，以创建一个适用于生产的 OCR 管道，用于文档文本提取。

# 5 个步骤

OCR 过程可以分为以下五个步骤：

1.  **旋转与倾斜校正** — 该步骤旋转并校正图像的倾斜，以创建一个正立且矩形的页面。经典的计算机视觉技术是…
