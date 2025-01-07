# 如何有效使用合成数据和模拟数据

> 原文：[https://towardsdatascience.com/how-to-use-synthetic-and-simulated-data-effectively-04d8582b6f88?source=collection_archive---------9-----------------------#2024-04-11](https://towardsdatascience.com/how-to-use-synthetic-and-simulated-data-effectively-04d8582b6f88?source=collection_archive---------9-----------------------#2024-04-11)

[](https://towardsdatascience.medium.com/?source=post_page---byline--04d8582b6f88--------------------------------)[![TDS Editors](../Images/4b2d1beaf4f6dcf024ffa6535de3b794.png)](https://towardsdatascience.medium.com/?source=post_page---byline--04d8582b6f88--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--04d8582b6f88--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--04d8582b6f88--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--04d8582b6f88--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04d8582b6f88--------------------------------) ·通过 [Newsletter](/newsletter?source=post_page---byline--04d8582b6f88--------------------------------) 发送 ·阅读时长 3 分钟·2024 年 4 月 11 日

--

使用合成数据并不是一个新做法：它已经成为一种有效的方式，帮助从业者在现实世界数据集无法访问、无法获取或因版权或使用许可问题受限时，为他们的项目提供所需的数据。

最近，LLM（大规模语言模型）和 AI 生成工具的兴起已经改变了合成数据的领域，正如它改变了机器学习和数据科学专业人士的许多其他工作流程一样。本周，我们将展示一系列最新的文章，涵盖你应该关注的趋势和可能性，以及如果你决定从头开始创建自己的玩具数据集时需要考虑的问题。让我们一起深入了解吧！

+   [**如何使用生成型 AI 和 Python 创建设计师假数据集**](/how-to-use-generative-ai-and-python-to-create-designer-dummy-datasets-d3cd9755c091)如果你已经有一段时间没有遇到需要合成数据的情况，不妨看看[Mia Dwyer](https://medium.com/u/110567c9c334?source=post_page---user_mention--04d8582b6f88--------------------------------)的简明教程，里面概述了一种使用 GPT-4 和一些 Python 编程来创建假数据集的简化方法。Mia 的方法相当简单，你可以根据自己的具体需求对这个方法进行调整和扩展。

+   [**创建合成用户研究：使用人物角色提示和自主代理**](/creating-synthetic-user-research-using-persona-prompting-and-autonomous-agents-b521e0a80ab6) 对于一个更为高级的应用场景，也依赖于生成式AI应用的力量，我们推荐阅读[Vincent Koc](https://medium.com/u/79191c4a8149?source=post_page---user_mention--04d8582b6f88--------------------------------)的合成用户研究指南。该方法利用自主代理架构，“在模拟研究情境中创建和与数字客户人物进行互动”，使用户研究既更易于接触，又不那么耗费资源。

+   [**合成数据：好、坏与未整理**](/synthetic-data-the-good-the-bad-and-the-unsorted-8220de6aac40) 使用生成数据解决了一些常见问题，但也可能带来一些新的问题。[Tea Mustać](https://medium.com/u/109d4928877a?source=post_page---user_mention--04d8582b6f88--------------------------------)关注了一个有前景的应用场景——训练AI产品，这通常需要大量数据——并分析了合成数据可以帮助我们绕过的法律和伦理问题，以及它无法解决的问题。

![](../Images/dcedd34eb5d291067c30d107667ee4af.png)

图片由[Rachel Loughman](https://unsplash.com/@rachelloughman?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   [**模拟数据，真实学习：情景分析**](/simulated-data-real-learnings-scenario-analysis-02ee56ed8886) 在他的持续系列中，[Jarom Hulet](https://medium.com/u/88982a88b4e5?source=post_page---user_mention--04d8582b6f88--------------------------------)探讨了模拟数据如何帮助我们做出更好的商业和政策决策，并从中提取有力的洞见。在前几篇文章中介绍了模型测试和功效分析后，最新的一篇重点讨论了模拟更复杂情景以优化结果的可能性。

+   [**评估合成数据——百万美元的问题**](/evaluating-synthetic-data-the-million-dollar-question-a54701d1b621) 每一个依赖于合成数据的过程背后的主要假设是，合成数据足够接近其模拟的真实数据的统计特性和模式。[Andrew Skabar博士](https://medium.com/u/95140850a5ea?source=post_page---user_mention--04d8582b6f88--------------------------------)提供了一份详细指南，帮助实践者评估其生成数据集的质量，以及这些数据集在多大程度上达到了这一关键门槛。

欲了解更多引人深思的文章，涉及话题从数据职业发展到多臂摆，我们邀请您探索以下几篇近期亮点：

+   在生成式人工智能工具的背景下，[版权问题](https://medium.com/u/6c1850419995?source=post_page---user_mention--04d8582b6f88--------------------------------)继续主导着行业的讨论；[Stephanie Kirmer](https://medium.com/u/a8dc77209ef3?source=post_page---user_mention--04d8582b6f88--------------------------------)分析了这一问题的利害关系，并在她的最新深度剖析中展望了未来。

+   我们很高兴欢迎[Fraser King](https://medium.com/u/3de75072e25d?source=post_page---user_mention--04d8582b6f88--------------------------------)回归，他分享了一个简明易懂的讲解，介绍了他关于使用深度学习进行雷达盲区图像修复的[研究](/beyond-the-blind-zone-706ba4b171c5)。

+   如何才能从[数据科学家转型为机器学习/人工智能产品经理](/from-data-scientist-to-ml-ai-product-manager-39359bd44512)？[Anna Via](https://medium.com/u/c1a8933ed8b?source=post_page---user_mention--04d8582b6f88--------------------------------)基于她过去几年的亲身经验，提供了成功转型的务实建议。

+   寻找产品市场契合度是每个创业公司的目标——也是一个常常难以实现的目标。[Myriam Barnés](https://medium.com/u/6c1850419995?source=post_page---user_mention--04d8582b6f88--------------------------------)提出了一种[基于用户数据的定量方法](/a-quantitative-approach-to-product-market-fit-python-code-56e0022e4b35)，重点关注增长和群体分析。

+   数据团队要有效地扩展其平台可能会面临困难；[Mahdi Karabiben](https://medium.com/u/7cda12823b7a?source=post_page---user_mention--04d8582b6f88--------------------------------)概述了[帮助数据管理者的几个关键原则](/navigating-your-data-platforms-growing-pains-a-path-from-data-mess-to-data-mesh-c16df72f5463)，帮助他们保持正确的方向。

+   最后，在理论层面，我们邀请你阅读[Oliver W. Johnson](https://medium.com/u/c2623d8d07f3?source=post_page---user_mention--04d8582b6f88--------------------------------)的首篇 TDS 文章，文章通过 VPython 模拟来[建模混沌运动并探讨混沌系统的定义](/the-double-pendulum-and-beyond-investigating-chaos-with-multi-armed-pendulums-7a5751d059db)。

感谢你支持我们作者的工作！如果你感到受到了启发，为什么不[写下你的第一篇文章呢？我们非常期待阅读](http://bit.ly/write-for-tds)。

直到下一个《Variable》，

TDS 团队
