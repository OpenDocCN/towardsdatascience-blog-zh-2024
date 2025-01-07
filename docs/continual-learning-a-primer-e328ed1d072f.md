# 持续学习：入门

> 原文：[`towardsdatascience.com/continual-learning-a-primer-e328ed1d072f?source=collection_archive---------5-----------------------#2024-10-15`](https://towardsdatascience.com/continual-learning-a-primer-e328ed1d072f?source=collection_archive---------5-----------------------#2024-10-15)

## 附加的论文推荐

[](https://pascaljanetzky.medium.com/?source=post_page---byline--e328ed1d072f--------------------------------)![Pascal Janetzky](https://pascaljanetzky.medium.com/?source=post_page---byline--e328ed1d072f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e328ed1d072f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e328ed1d072f--------------------------------) [Pascal Janetzky](https://pascaljanetzky.medium.com/?source=post_page---byline--e328ed1d072f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e328ed1d072f--------------------------------) ·阅读时长 7 分钟·2024 年 10 月 15 日

--

目前，训练大型语言模型的成本大约在 430 万美元（GPT3）到 1.91 亿美元（Gemini）之间[1]。一旦有新的文本数据可用，例如通过许可协议，使用这些数据进行再训练可以提高模型的性能。然而，以这样的成本（并且不仅仅是在这些水平；哪家公司能拿出 100 万美元仅用于*最终*训练，更不用说前期实验了？）来看，频繁地从头开始再训练的费用是难以承受的。

![](img/95561cd7ccfb6f32109e8e2d865c3628.png)

照片由 [Dan Schiumarini](https://unsplash.com/@dan_schiumarini?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这就是持续学习（CL）发挥作用的地方。在持续学习中，数据随着时间的推移逐步到达，且无法（完全）存储。机器学习模型仅基于新数据进行训练；这里的挑战是*灾难性遗忘*：模型在旧数据上的表现下降。表现下降的原因在于，模型仅将权重调整为当前数据的需求，因为没有动机去保留从以前数据中获得的信息。

为了应对遗忘并保持旧知识，提出了许多方法。这些方法可以分为三大类*：**基于复习**、**基于正则化**、和**基于架构**。在接下来的部分中，我将详细介绍每一类，并介绍一些选定的论文供进一步探索。虽然我将重点放在分类问题上，但所有涉及的思想*大多*也适用于其他领域，例如……
