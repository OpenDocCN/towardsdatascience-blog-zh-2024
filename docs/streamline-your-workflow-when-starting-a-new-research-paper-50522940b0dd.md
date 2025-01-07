# 启动新研究论文时优化工作流程

> 原文：[https://towardsdatascience.com/streamline-your-workflow-when-starting-a-new-research-paper-50522940b0dd?source=collection_archive---------2-----------------------#2024-12-08](https://towardsdatascience.com/streamline-your-workflow-when-starting-a-new-research-paper-50522940b0dd?source=collection_archive---------2-----------------------#2024-12-08)

## ***Python 代码用于创建生物医学科学研究论文的文件夹和 Word 文档——只需两个输入，立即完成所有操作***

[](https://medium.com/@rcarrillo90?source=post_page---byline--50522940b0dd--------------------------------)[![Rodrigo M Carrillo Larco, MD, PhD](../Images/458ed24625ded66ef47c5af718db3659.png)](https://medium.com/@rcarrillo90?source=post_page---byline--50522940b0dd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--50522940b0dd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--50522940b0dd--------------------------------) [Rodrigo M Carrillo Larco, MD, PhD](https://medium.com/@rcarrillo90?source=post_page---byline--50522940b0dd--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--50522940b0dd--------------------------------) ·6分钟阅读·2024年12月8日

--

![](../Images/c9aa1d91de1076956838a6284b0ec791.png)

图片来源：[Maksym Kaharlytskyi](https://unsplash.com/@qwitka?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我是一名研究员，拥有超过七年的公共卫生和流行病学研究经验。每次我要开始撰写新的研究论文时，我都会为这个项目创建一个文件夹，在其中创建多个子文件夹来存放各个部分的工作，并为手稿创建带有特定标题的 Word 文档。迄今为止，我已发布超过 170 篇同行评审论文，估计这个过程我已经做了 200 多次。我认为是时候自动化这个过程了，今天我将与大家分享如何做到这一点！

在文章的最后，您会找到完整的代码。只需将代码复制并粘贴到您喜欢的 Python 环境中，点击运行即可。代码会生成一个名为 create_project_structure 的函数。这个函数将创建一个文件夹结构和 Word 文档，准备好让您直接开始撰写研究论文。

**这个功能做什么？**

这个函数将在指定的路径 (`base_path`) 下生成一个文件夹，您可以自定义文件夹名称 (`project_name`)。该函数还会创建两个 Word 文档，一个用于补充材料，另一个…
