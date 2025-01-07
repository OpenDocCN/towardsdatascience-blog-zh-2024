# Segment-Anything 模型（SAM）的编码器是如何工作的？

> 原文：[https://towardsdatascience.com/how-does-the-segment-anything-models-sam-s-encoder-work-003a8a6e3f8b?source=collection_archive---------5-----------------------#2024-05-14](https://towardsdatascience.com/how-does-the-segment-anything-models-sam-s-encoder-work-003a8a6e3f8b?source=collection_archive---------5-----------------------#2024-05-14)

## 深入探讨图像内容嵌入、正弦和余弦位置嵌入、引导点击嵌入以及密集掩码嵌入是如何生成的

[](https://jasonweiyi.medium.com/?source=post_page---byline--003a8a6e3f8b--------------------------------)[![魏毅](../Images/24b7a438912082519f24d18e11ac9638.png)](https://jasonweiyi.medium.com/?source=post_page---byline--003a8a6e3f8b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--003a8a6e3f8b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--003a8a6e3f8b--------------------------------) [魏毅](https://jasonweiyi.medium.com/?source=post_page---byline--003a8a6e3f8b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--003a8a6e3f8b--------------------------------) ·阅读时间 16 分钟 ·2024年5月14日

--

![](../Images/b8fa7cce26e763966c76f98d3a33a940.png)

照片由 [benjamin lehman](https://unsplash.com/@abject?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文解释了 Segment-Anything 模型（SAM）的编码器是如何工作的。SAM 使用 [ImageEncoderViT](https://github.com/facebookresearch/segment-anything/blob/main/segment_anything/modeling/image_encoder.py) 类来生成输入图像的内容嵌入。它还使用 [PromptEncoder](https://github.com/facebookresearch/segment-anything/blob/main/segment_anything/modeling/prompt_encoder.py) 类来生成以下嵌入：

+   引导点击，包括正面和负面

+   边界框

+   图像位置编码

+   图像密集掩码编码

ImageEncoderViT 是一个标准的 ViT 网络，所以我在这里不会详细解释。本文仅专注于 PromptEncoder 如何生成上述嵌入。那些嵌入会被传递给 SAM 的解码器，用于生成分割掩码。了解解码器如何使用这些嵌入会让理解编码过程更加容易。你可以通过阅读我另一篇文章 “[Segment-Anything 模型（SAM）解码器是如何工作的？](https://medium.com/p/0e4ab4732c37)” 进一步了解 SAM 的解码过程。

# 编码引导点击

在 SAM 中，引导点击可以是正向的，告诉模型在哪里进行分割，或者是…
