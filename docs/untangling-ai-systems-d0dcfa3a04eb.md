# 解开人工智能系统的谜团

> 原文：[https://towardsdatascience.com/untangling-ai-systems-d0dcfa3a04eb?source=collection_archive---------4-----------------------#2024-10-26](https://towardsdatascience.com/untangling-ai-systems-d0dcfa3a04eb?source=collection_archive---------4-----------------------#2024-10-26)

## 物理学如何帮助我们理解神经网络

[](https://javier-marin.medium.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------)[![哈维尔·马林](../Images/31800b2fbfd1f7c841c9f6a2579d5681.png)](https://javier-marin.medium.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------) [哈维尔·马林](https://javier-marin.medium.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0dcfa3a04eb--------------------------------) ·13分钟阅读·2024年10月26日

--

![](../Images/3d9ff64d6133948ec3f46afd682c6f5d.png)

由作者使用DALL-E生成的图像

如果我们能够打开一个人工智能系统，发现它是一个组织良好的组件工厂，所有组件都在一起工作，那会怎么样？本文探讨了一种结合了两个强大概念的新方法：稀疏神经电路和受物理学启发的数学。通过结合这些不同的领域，我们可以找到分析和构建人工智能系统的新方法。虽然神经网络看起来像是难以捉摸的“黑盒子”，研究人员却发现了令人着迷的东西：它们包含可解释的“电路”，这些电路的功能类似于机器组件。让我用简单的语言解释一下。

# 神经电路

如果我们不是试图一次性理解整个神经网络，而是像生物学家研究个别细胞和神经通路一样，逐一检查它们，那会怎么样？这种方法受神经学和细胞生物学的启发，于2018年由[Chris Olah](https://www.anthropic.com/research#interpretability)率先提出，提供了一种更彻底理解神经网络的方式。

![](../Images/bb43cebb8565de35ea71fd1810db7b2e.png)

图像来自 [https://distill.pub/2020/circuits/zoom-in/](https://distill.pub/2020/circuits/zoom-in/)

想想我们如何在一张图片中识别出一只狗。我们的脑袋处理不同的特征：耳朵的弯曲、毛发的纹理、眼睛的圆形。神经网络……
