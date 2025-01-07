# 使用 BERTtopic 进行 Python 中的主题建模

> 原文：[`towardsdatascience.com/topic-modelling-with-berttopic-in-python-8a80d529de34?source=collection_archive---------1-----------------------#2024-04-01`](https://towardsdatascience.com/topic-modelling-with-berttopic-in-python-8a80d529de34?source=collection_archive---------1-----------------------#2024-04-01)

## 使用最先进的基于 Transformer 的主题模型进行政治言论建模的实践教程

[](https://petrkorab.medium.com/?source=post_page---byline--8a80d529de34--------------------------------)![Petr Korab](https://petrkorab.medium.com/?source=post_page---byline--8a80d529de34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8a80d529de34--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a80d529de34--------------------------------) [Petr Korab](https://petrkorab.medium.com/?source=post_page---byline--8a80d529de34--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a80d529de34--------------------------------) ·5 分钟阅读·2024 年 4 月 1 日

--

![](img/7bf97b6bb5cc27c1020b066165982886.png)

图片来源：[Harryarts](https://www.freepik.com/author/harryarts)于[Freepik](https://www.freepik.com/free-vector/modern-molecules-background_1186441.htm#fromView=search&page=2&position=26&uuid=5e3b0934-0d71-4782-bbf7-d1e739b45b27)

***主题建模***（即在文本数据语料库中进行主题识别）自*隐含狄利克雷分配（LDA）*模型[发布](https://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf)以来，发展迅速。然而，这一经典的主题模型并未很好地捕捉词语之间的关系，因为它基于[词袋模型](https://miningthedetails.com/LDA_Inference_Book/word-representations.html)的统计概念。近年来，基于嵌入的[*Top2Vec*](https://github.com/ddangelov/Top2Vec)和[*BERTopic*](https://arxiv.org/abs/2203.05794)模型通过利用预训练语言模型生成主题，从而解决了这一缺点。

在本文中，我们将使用[Maarten Grootendorst（2022）](https://arxiv.org/abs/2203.05794)的**BERTopic**来识别政治演讲稿中的代表性术语。它在各种语料库中的主题建模指标上优于大多数传统和现代主题模型，并已被[公司](https://maartengr.github.io/BERTopic/usecases.html#intelligent-virtual-assistants)、学术界（[Chagnon，2024](https://www.sciencedirect.com/science/article/pii/S2949719123000419)）以及公共部门广泛使用。我们将在 Python 代码中进行探讨：

+   如何有效地预处理数据

+   如何创建一个二元组主题模型

+   如何探索随时间变化的最频繁术语。

# 1\. 示例数据

作为一个示例数据集，我们将使用[*Empoliticon: 政治演讲-语境与情感数据集*](https://www.kaggle.com/datasets/efatazher/empoliticon-political-speeches-context-and-emotion)，该数据集发布于…
