# 数据网格中的挑战与解决方案 — 第3部分

> 原文：[https://towardsdatascience.com/challenges-and-solutions-in-data-mesh-part-3-dacb917f3c91?source=collection_archive---------7-----------------------#2024-06-21](https://towardsdatascience.com/challenges-and-solutions-in-data-mesh-part-3-dacb917f3c91?source=collection_archive---------7-----------------------#2024-06-21)

## 在联合架构中，互操作性是很难实现的。我将通过联合企业数据建模概述在数据网格中实现这一目标的实际方法。

[](https://medium.com/@bernd.wessely?source=post_page---byline--dacb917f3c91--------------------------------)[![Bernd Wessely](../Images/e60e01c19412d8af8f8bddf78e561275.png)](https://medium.com/@bernd.wessely?source=post_page---byline--dacb917f3c91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dacb917f3c91--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dacb917f3c91--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--dacb917f3c91--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dacb917f3c91--------------------------------) ·阅读时间9分钟·2024年6月21日

--

“联合计算治理”确保了一个安全、可信和可互操作的数据网格。从互操作性中获得的附加价值通常可以用“整体大于部分之和”来概括。尽管设立标准协议（例如HTTP）、高效的数据传输机制以及组件的全面版本控制对于实现互操作性至关重要，但我们在这里的重点将是保持整体数据的一致性和兼容性。

为了让数据网格真正支持互操作性，我们必须确保一个一致且连贯的模型，涵盖所有数据产品的内容。这个模型需要在每次新增数据产品时动态更新。在我的三篇系列文章的最后一篇中，我将展示一种保持这一整体视图、确保其一致性和时效性的方法。

*治理*常常让人联想到可能会阻碍进展的僵化规则。它被认为是一种负担，增加了额外的工作而没有直接的好处。通过自动化治理过程（使其“计算化”）可以简化在数据网格中的应用，尽管这仍然需要在项目或产品层面付出代价。
