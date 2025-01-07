# AI通过伪DXA预测你从外部到内部的健康状态

> 原文：[https://towardsdatascience.com/ai-pseudo-dxa-8e3f83b5d373?source=collection_archive---------6-----------------------#2024-02-01](https://towardsdatascience.com/ai-pseudo-dxa-8e3f83b5d373?source=collection_archive---------6-----------------------#2024-02-01)

## 一个定量准确且临床有用的生成性医学影像模型

[](https://lambertleong.medium.com/?source=post_page---byline--8e3f83b5d373--------------------------------)[![Lambert T Leong博士](../Images/2080be3fcabb7f5d702487a95c59d41b.png)](https://lambertleong.medium.com/?source=post_page---byline--8e3f83b5d373--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8e3f83b5d373--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8e3f83b5d373--------------------------------) [Lambert T Leong博士](https://lambertleong.medium.com/?source=post_page---byline--8e3f83b5d373--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8e3f83b5d373--------------------------------) ·11分钟阅读·2024年2月1日

--

![](../Images/b6c1f5c2edf02f9d04e9f6d8607056b1.png)

[3D人体表面扫描点云与匹配的双能X射线吸收法（DXA）扫描（图片来自作者）](https://www.lambertleong.com/projects/pseudo-dxa-gen-ai)

# **关键点**

1.  据我们所知，这是第一个可以通过商业临床软件分析生成医学影像并具有定量准确性的模型。

1.  能够从外部形态预测脂肪、肌肉和骨骼的内部分布，表明身体组成与体型之间存在强烈的关系

1.  这个模型代表了朝着可获得健康监测迈出的重要一步，能够生成通常需要专业昂贵设备、训练有素的技术人员，并且涉及可能有害的电离辐射的图像。

1.  [阅读论文 HERE](https://www.nature.com/articles/s43856-024-00434-w)

生成性人工智能（AI）特别是在扩散模型（如DALL-E）和大型语言模型（LLM，如ChatGPT）发布之后，变得极为流行。通常，当模型生成某种输出时，AI模型被归类为“生成性”。对于DALL-E，输出产品是高质量的图像，而对于ChatGPT，输出产品是高度结构化且有意义的文本。这些生成性模型不同于分类模型，后者的输出是…
