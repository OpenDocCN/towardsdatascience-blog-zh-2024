# LLMs 的新前沿：挑战、解决方案与工具

> 原文：[`towardsdatascience.com/the-new-frontiers-of-llms-challenges-solutions-and-tools-b1d48c34cf8e?source=collection_archive---------2-----------------------#2024-01-25`](https://towardsdatascience.com/the-new-frontiers-of-llms-challenges-solutions-and-tools-b1d48c34cf8e?source=collection_archive---------2-----------------------#2024-01-25)

[](https://towardsdatascience.medium.com/?source=post_page---byline--b1d48c34cf8e--------------------------------)![TDS Editors](https://towardsdatascience.medium.com/?source=post_page---byline--b1d48c34cf8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b1d48c34cf8e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1d48c34cf8e--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--b1d48c34cf8e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1d48c34cf8e--------------------------------) ·通过 Newsletter 发送 ·3 分钟阅读·2024 年 1 月 25 日

--

大型语言模型已经存在多年，但直到 2023 年，它们在机器学习社区内外的存在才变得真正无处不在。以前晦涩的概念，如微调和 RAG，已经成为主流，大小公司要么在构建，要么在将 LLM 驱动的工具整合到它们的工作流程中。

展望 2024 年，似乎几乎可以确定这些模型的影响力将进一步扩大，并且随着令人兴奋的创新，它们也将为从业者带来新的挑战。本周我们强调的一些突出文章指向了与 LLMs 相关的一些新兴方面；无论你是对这个话题相对陌生，还是已经广泛尝试过这些模型，你都能在这里找到一些能激发你好奇心的内容。

+   **民主化 LLMs：4 位量化以优化 LLM 推理**

    量化是使大规模模型的强大能力能够为更广泛的机器学习专业人士群体所用的主要方法之一，这些专业人士中的许多人可能无法获得无限的内存和计算能力。[Wenqi Glantz](https://medium.com/u/ce7cd5b8b74a?source=post_page---user_mention--b1d48c34cf8e--------------------------------)带我们了解了量化 Mistral-7B-Instruct-v0.2 模型的过程，并解释了这种方法在效率和性能之间固有的权衡。

+   **导航 LLM 代理的世界：初学者指南**我们如何让 LLM“达到能够独立解决更复杂问题的水平”？[Dominik Polzer](https://medium.com/u/3ab8d3143e32?source=post_page---user_mention--b1d48c34cf8e--------------------------------)的易懂入门指南展示了如何构建能够利用不同工具和功能、以最少人类干预自动化复杂工作流的 LLM 代理。

![](img/41f848543855fdc6762d8c38848f6b66.png)

图片由[Beth Macdonald](https://unsplash.com/@elsbethcat?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   **利用 KeyBERT、HDBSCAN 和 Zephyr-7B-Beta 构建知识图谱**LLM 本身非常强大，当然，但当与其他方法和工具结合时，其潜力更为显著。[Silvia Onofrei](https://medium.com/u/ab562e798558?source=post_page---user_mention--b1d48c34cf8e--------------------------------)最近的指南通过 Zephyr-7B-Beta 模型的帮助，展示了如何构建一个知识图谱，这正是一个例证；它演示了如何将 LLM 与传统的 NLP 方法结合，产生令人印象深刻的结果。

+   **将大型语言模型与 mergekit 合并**尽管听起来不太可能，但有时单一的 LLM 可能无法满足项目的特定需求。正如[Maxime Labonne](https://medium.com/u/dc89da634938?source=post_page---user_mention--b1d48c34cf8e--------------------------------)在他最新的教程中所展示的，模型合并是一种“相对较新且实验性的低成本创建新模型的方法”，它可能正是你需要从多个模型中混合搭配元素时的解决方案。

+   **在招聘过程中使用 LLM 会让你作为候选人变成欺诈者吗？**LLM 提出的问题不仅仅是技术性的——它们还涉及到伦理和社会问题，这些问题可能变得相当棘手。[Christine Egan](https://medium.com/u/8e9b4d1cb38?source=post_page---user_mention--b1d48c34cf8e--------------------------------)聚焦于那些在求职过程中利用 LLM 和像 ChatGPT 这样的工具的求职者所面临的风险，并探讨了利用和滥用技术来简化繁琐任务之间有时模糊的界限。

正如往常一样，我们作者在最近几周所涵盖的主题范围和深度令人震撼——这里是一些必读的代表性样本：

+   超越 LLM，[Nate Cibik](https://medium.com/u/82bf2304955e?source=post_page---user_mention--b1d48c34cf8e--------------------------------) 深入探索了大规模*多模态*模型（LMMs）和它们如何塑造自主机器人领域。

+   在一个新的项目介绍中，[Christabelle Pabalan](https://medium.com/u/4200eb8e8b26?source=post_page---user_mention--b1d48c34cf8e--------------------------------) 结合了 NLP 技术、特征工程和可视化来评估电影对白的语义属性与类型之间的联系。

+   今年时序图学习的发展方向在哪里？[Shenyang(Andy) Huang](https://medium.com/u/8aa224c5cedd?source=post_page---user_mention--b1d48c34cf8e--------------------------------) 与合著者 Emanuele Rossi，[Michael Galkin](https://medium.com/u/4d4f8ddd1e68?source=post_page---user_mention--b1d48c34cf8e--------------------------------)，Andrea Cini 和 Ingo Scholtes 提供了该领域发展轨迹的全景概览。

+   因果推断是所有数据专业人士必须掌握的基本概念，但其含义可能会因角色而异。[Zijing Zhu, PhD](https://medium.com/u/7d83c09fb5d4?source=post_page---user_mention--b1d48c34cf8e--------------------------------) 聚焦于这些差异在学术界和行业中的表现。

+   扩散在近年来的 AI 生成图像领域引起了广泛关注，但正如[Christopher Landschoot](https://medium.com/u/b64548f914a5?source=post_page---user_mention--b1d48c34cf8e--------------------------------) 所展示的，其潜力同样延伸到了音乐和音频生成的世界。

感谢你支持我们作者的工作！如果你感到受到启发并想加入他们的行列，为什么不[写下你的第一篇文章？我们期待阅读它](http://bit.ly/write-for-tds)。

直到下一个 Variable，

TDS 团队
