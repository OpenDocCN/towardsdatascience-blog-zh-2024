# 稀疏自编码器、加性决策树以及 AI 可解释性中的其他新兴话题

> 原文：[`towardsdatascience.com/sparse-autoencoders-additive-decision-trees-and-other-emerging-topics-in-ai-interpretability-9ab1eaf14d3e?source=collection_archive---------6-----------------------#2024-06-13`](https://towardsdatascience.com/sparse-autoencoders-additive-decision-trees-and-other-emerging-topics-in-ai-interpretability-9ab1eaf14d3e?source=collection_archive---------6-----------------------#2024-06-13)

[](https://towardsdatascience.medium.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------)![TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ab1eaf14d3e--------------------------------) ·以 时事通讯 形式发送 ·阅读时间 4 分钟 ·2024 年 6 月 13 日

--

> *想要写你的第一篇 TDS 文章吗？* [*我们始终欢迎新作者的投稿*](http://bit.ly/write-for-tds)*。*

随着大规模语言模型（LLM）变得越来越庞大，AI 应用变得更加强大，理解其内部机制的探索变得更加困难且更加紧迫。关于黑箱模型风险的讨论并不是什么新鲜话题，但随着 AI 驱动工具的影响力不断扩大，幻觉现象和其他次优输出频繁出现在浏览器和用户界面中，这使得从业者（和最终用户）比以往任何时候都更加重要，必须抵制接受 AI 生成内容的表面价值。

我们的每周亮点栏目深入探讨了在广泛使用大规模语言模型时代，模型可解释性和解释性的问题。从对一篇有影响力的新论文的详细分析，到使用其他近期技术的实践实验，我们希望你能花时间探索这一日益重要的话题。

+   **手动深入探索 Anthropic 的稀疏自编码器**在短短几周内，Anthropic 的“单义性扩展”论文吸引了 XAI 社区的广泛关注。[Srijanie Dey 博士](https://medium.com/u/d60d06fe8655?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)为任何对研究人员的主张和目标感兴趣的人提供了一份面向初学者的介绍，并展示了他们是如何提出“创新方法来理解神经网络中不同组件如何相互作用以及每个组件的角色”的。

+   **大型语言模型中的可解释特征**对于“单义性扩展”论文的理论基础，若想获取一篇高水平、图文并茂的解析文章，我们强烈推荐[Jeremi Nuer](https://medium.com/u/7ce320f77bc9?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)的 TDS 首篇文章——阅读之后，你将对研究人员的思路以及该研究对未来模型发展的意义有一个清晰的了解：“随着改进停滞，且大规模语言模型越来越难以扩展，如果我们希望在性能上实现下一次飞跃，真正理解它们的工作原理将变得至关重要。”

+   **人工智能可解释性的意义**

    [斯蒂芬妮·基尔默](https://medium.com/u/a8dc77209ef3?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)从特定模型及其带来的技术挑战中抽身，稍微“哲学化”了一些，在她关于可解释性局限性的文章中提出；她认为，试图揭示这些黑箱模型的努力可能永远无法实现完全的透明度，但仍然对机器学习研究人员和开发者而言非常重要，值得投入研究。

![](img/cb75693400faf7951b95020c81f00e96.png)

图片由[乔安娜·科辛斯卡](https://unsplash.com/@joannakosinska?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   **加性决策树**在他最近的研究中，[W·布雷特·肯尼迪](https://medium.com/u/5176dd5e0bcf?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)一直专注于可解释的预测模型，剖析其底层数学，并展示它们在实践中的工作原理。他对加性决策树的深入研究是对这种模型的有力且全面的介绍，展示了它如何旨在补充有限的可解释分类和回归模型选项。

+   **深度解析累计局部效应图（ALEs）与 Python**为了丰富我们的内容，我们非常高兴地分享[Conor O'Sullivan](https://medium.com/u/4ae48256fb37?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)关于累计局部效应图（ALEs）的动手探索：一种较老但可靠的方法，即使在模型中存在多重共线性的情况下，也能提供清晰的解释。

本周有兴趣深入了解其他话题吗？从量化到宝可梦优化策略，我们都有涉及！

+   在一个令人着迷的项目演示中，[Parvathy Krishnan](https://medium.com/u/102000f20d44?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)、Joaquim Gromicho 和 Kai Kaiser 展示了他们如何结合多个地理空间数据集和一些 Python 来优化选择医疗设施位置的过程。

+   学习权重量化如何工作以及如何应用于实际的深度学习工作流程中——[Chien Vu](https://medium.com/u/f2928e8b6c04?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)的教程既详尽又易懂。

+   背包问题是一个经典的优化挑战；[Maria Mouschoutzi 博士](https://medium.com/u/dce3cb684eae?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)为此提出了一个有趣的新角度，展示了如何通过建模和 PuLP，一个 Python 优化框架来创建最强大的宝可梦团队。

+   对于许多机器学习专业人士来说，从 RAG 系统中挤出最大价值仍然是一个重要的优先事项。[Leonie Monigatti](https://medium.com/u/3a38da70d8dc?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)仔细分析了衡量上下文相关性的潜在解决方案。

+   在作为技术巨头和高速成长初创公司数据领导者十多年后，[Torsten Walbaum](https://medium.com/u/4e291ce6380c?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)分享了他在一个基本问题上积累的见解：我们如何理解数据？

+   数据分析师可能不常把自己当作程序员，但跨学科学习仍然有很大的空间—正如[玛丽亚·曼苏罗娃](https://medium.com/u/15a29a4fc6ad?source=post_page---user_mention--9ab1eaf14d3e--------------------------------)在一篇数据聚焦的软件工程最佳实践总结中所展示的。

感谢你支持我们作者的工作！我们喜欢发布新作者的文章，所以如果你最近写了一篇有趣的项目教程、教程或关于我们核心主题的理论思考，不要犹豫，[与我们分享](http://bit.ly/write-for-tds)。

直到下一个变量，

TDS 团队
