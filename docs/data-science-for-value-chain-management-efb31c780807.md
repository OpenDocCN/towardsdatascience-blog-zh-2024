# 数据科学在价值链管理中的应用

> 原文：[https://towardsdatascience.com/data-science-for-value-chain-management-efb31c780807?source=collection_archive---------6-----------------------#2024-05-15](https://towardsdatascience.com/data-science-for-value-chain-management-efb31c780807?source=collection_archive---------6-----------------------#2024-05-15)

## 你如何利用数据科学优化运营并提升盈利能力？

[](https://s-saci95.medium.com/?source=post_page---byline--efb31c780807--------------------------------)[![Samir Saci](../Images/722d1f56a3308f6527d82b5ab97064ec.png)](https://s-saci95.medium.com/?source=post_page---byline--efb31c780807--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--efb31c780807--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--efb31c780807--------------------------------) [Samir Saci](https://s-saci95.medium.com/?source=post_page---byline--efb31c780807--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--efb31c780807--------------------------------) ·阅读时间：12分钟·2024年5月15日

--

![](../Images/9b9162339e5cc9cb640b09a68aa3050f.png)

价值链管理 — （图像来源：作者）

价值链管理 (VCM) 是指组织那些为商品或服务增加价值的活动，以在市场上获得竞争优势。

这种方法帮助组织有效响应市场趋势并提高效率，以提升盈利能力。

![](../Images/41595d88637c1226ba1926d6bb8e6686.png)

价值链与供应链 — （图像来源：作者）

> 作为一名数据科学家和分析经理，您如何对公司价值链产生影响？

在本文中，我们将快速探讨**价值链管理的基本组成部分**。

接下来，我们将探讨**四个数据科学应用示例**，以支持**战略性主要活动**。

```py
Summary

**I. The Pillars of Value Chain Management**
 **1\. Activities to create value**
Understanding the fundamental components to create value 
 **2\. What are the primary activities?**
Core functions directly involved in product creation, marketing, and delivery.
 **3\. What are the support activities?**
Essential functions that indirectly contribute to value creation
 **4\. Data Science to Support Primary Activities**
Discussing how data science tools and techniques can be employed to 
optimize primary activities and enhance overall value chain management.
**II. Inbound Logistics: Supply of Raw Materials**
 **1\. Supplier Mapping with the Graph Theory**
Analyze supplier networks for risk assessment and optimization.
 **2\. Sustainable Sourcing Network Optimization**
Select suppliers based on economic and sustainable criteria
**III. Operations: From Raw Materials to Finished Goods**
 **1\. Production Planning Optimization with Wagner-Whitin Algorithm**
Optimize production planning, balancing setup costs and inventory management.
 **2\. How to measure the impact of your solution?**
**IV. Outbound Logistics: Distribute your Final Products**
 **1\. Automate a Supply Chain Control Tower**
Automate monitoring and improve the efficiency of outbound distribution.
 **2\. How can we improve the performance using these diagnostics?**
**V. Conclusion** 
```

# 价值链管理的两大支柱

企业不断寻求通过提高效率和最大化盈利能力来获得竞争优势的方法。

> 客户：Samir，我们希望将物流成本降低20%。我们希望你重新设计整个分销网络。

这是我作为供应链工程师或数据科学家进行的大多数项目的共性。

因此，本博客中发布的大多数文章都专注于**使用数据分析**来优化供应链流程，以降低**成本**。

> 时尚零售商：我们如何才能以最低成本生产和交付产品？

**价值链管理 (VCM)** 是一种战略性方法，旨在简化业务流程的每个阶段，以优化绩效。

这涵盖了**从生产到交付**的所有环节，以在最小化成本的同时创造最大客户价值。

## 创造价值的活动

价值链框架最早由迈克尔·波特在他的著作**《竞争优势：创造和维持卓越表现》**中提出。

这种方式通过将任何业务拆解为一系列**互联的活动，这些活动有助于**创造和向客户传递价值，从而彻底改变了企业对其运营的看法。

![](../Images/748addc8b7bb07d97348e7c7ee0c2208.png)

价值链的组成部分 — （作者提供的图像）

+   **主要活动**直接涉及产品或服务的创建、销售、维护和支持。

+   **支持活动**包括基础设施、技术开发、人力资源管理和采购。

让我们通过一个时尚零售商**在亚洲生产T恤并在欧洲销售**的例子，来探讨它们的定义。

## 主要活动有哪些？

主要活动包括入境物流、运营、出境物流、营销和服务。

![](../Images/e528a810e8b9444afa0d322c3b1c439f.png)

价值链管理中的主要活动 — （作者提供的图像）

这些核心功能涉及创建、生产、营销和向最终客户交付**产品或服务**。

![](../Images/8211bdd559524e9e45819cf3b11158ac.png)

时尚零售公司供应链运营 — （作者提供的图像）

以我们T恤的价值链为例：

+   **入境物流**包括从供应商处采购棉花并运输到制造设施。

+   **运营**包括将棉花转化为面料图案，缝合起来以制作T恤。

+   **出境物流**涉及所有包装、储存和将T恤送达最终目的地的物流过程。

+   **营销**和销售专注于推广以促进销售。

+   **服务**包括售后支持、客户服务及其他附加服务，如定制。

> 如何支持和协调这些活动？

此外，**支持活动**在确保这些主要活动顺利进行方面发挥着关键作用。

让我们深入探讨这些支撑整个价值链的关键支持功能。

## 支持活动有哪些？

支持活动通过提升**主要活动的效率和效果**，**间接**地增加了产品或服务的价值。

![](../Images/cf9d2393bf452b912f9bc5f6453c1d4c.png)

聚焦支持活动 — （作者提供的图像）

支持活动涵盖基础设施、**技术开发**、人力资源管理和采购。

> 作为数字化转型部门的数据科学家，你是支持功能的一部分。

+   **公司基础设施**包括组织结构、控制系统和行政任务，这些任务确保其价值链的顺利运行。

+   **技术开发**活动与支持价值链的技术和系统相关。

+   **人力资源管理**涉及招聘、培训和留住为每个价值链阶段做出贡献的员工。

+   **采购**涉及为价值链采购和购买所需的输入，从原材料到办公设备。

> 作为技术开发活动中的关键角色，你能产生什么样的影响？

## 数据科学支持主要活动

作为新聘的数据科学经理，你希望提出一份**路线图**，以实施**先进的分析工具**来**支持主要活动**。

目标是支持精心挑选的价值链活动，并使你的团队成为公司的一项战略资产。

# **入境物流：原材料供应**

这包括所有与**接收**、**存储**和**分配**生产前输入相关的过程和活动。

在我们的例子中，这些输入可能是**原材料**，如棉花、染料和制造T恤所需的其他物料。

![](../Images/2f050e5235e04b1092731efd9961d050.png)

入境物流优化的数据分析解决方案示例 ——（图片由作者提供）

💡 基于你的经验，你提议一组分析工具来应对运营挑战并优化流程。

> **我们如何使用描述性分析来监控采购表现？**

## 使用图论的供应商映射

你想提出解决方案以支持**供应商风险评估**。

> ***💾 输入数据***：购买订单、供应商信息、工厂/仓库容量和Excel文件中的发货记录。

采购团队需要一个工具，可以提供跨供应商网络的可见性，用于执行风险评估、供应商整合和物流网络设计。

> ***❔ 问题陈述***：你有一组工厂，这些工厂从供应商那里接收关键组件和原材料。
> 
> -
> 
> 你如何估算特定供应商故障对整体制造足迹的影响？*

[![](../Images/aae726254762a9d3eb83e2d7a4726583.png)](https://towardsdatascience.com/transportation-network-analysis-with-graph-theory-55eceb7e4de4)

网络图示例 ——（图片由作者提供）

> B-45工厂有多少个关键供应商？
> 
> ***🚀 解决方案***：图论是一个数学领域，研究图中作为顶点和边表示的对象之间的关系。

在这个特定的案例中，可以使用图论来

+   可视化所有参与**特定项目**价值链的**供应商**

+   可视化所有参与同一价值链的工厂：原材料加工厂和组装零件。

我使用这个理论来分析一家零售公司路线策略。

目标是可视化在同一路线交付的所有商店。

[![](../Images/1459a1e1ce36be277603d8968eb2cb4e.png)](https://towardsdatascience.com/transportation-network-analysis-with-graph-theory-55eceb7e4de4)

用于运输优化的图论示例 [[文章](/transportation-network-analysis-with-graph-theory-55eceb7e4de4)] — （图片来源：作者）

这个例子可以很容易地**调整**用于审计供应商网络

+   如果供应商正在向工厂B-45交付，请创建一个链接

+   创建原材料加工工厂B-45与装配线C-78之间的链接。

有关图论的更多信息，

[](/transportation-network-analysis-with-graph-theory-55eceb7e4de4?source=post_page-----efb31c780807--------------------------------) [## 图论的运输网络分析

### 使用图论优化零售公司道路运输网络

towardsdatascience.com](/transportation-network-analysis-with-graph-theory-55eceb7e4de4?source=post_page-----efb31c780807--------------------------------)

> 什么样的分析可以支持图论？

+   **风险评估**：有多少项目依赖于特定供应商？

    🎯 直观评估该供应商的重要性，并评估其风险。

+   **网络设计**：特定工厂的供应商位于哪里？🚛 为了减少采购提前期并减少对海运的依赖，你可以推动供应商将工厂搬迁至靠近该工厂的地方。

此可视化提供了足够的透明度，支持采购团队讨论**风险评估**或**供应商优化**。

## 可持续采购网络优化

采购团队要求一个工具，用于选择**合适的供应商**，以减少**进货流**的环境足迹。

> ***💾 输入数据****：工厂需求（单位/月）、供应商和工厂位置、每个供应商的环境影响（CO2、水等）以及每个供应商的生产成本。*

你的同事需要一个工具，能够根据以下条件选择合适的供应商

+   **约束条件**如供应量 ≥ 需求量、供应商的容量限制和每单位产品的最大排放或水使用量。

+   **特定目标**：最小化成本、水使用量或CO2排放。

> ***❔ 问题声明****：我们应该选择哪些供应商以最小化CO2排放？*

[![](../Images/df90c3e4ec59326d02969467094c6552.png)](https://towardsdatascience.com/data-science-for-sustainable-sourcing-a72f2c4db424)

可持续采购问题声明 — （图片来源：作者）

> ***🚀 解决方案****：使用Python中的线性规划，你可以根据用户选择的目标自动选择合适的供应商。*

[![](../Images/302b282004b6975e4680a8905e3d0604.png)](https://towardsdatascience.com/data-science-for-sustainable-sourcing-a72f2c4db424)

多目标的不同解决方案 [尝试[应用](https://cloud.viktor.ai/public/sustainable-supply-chain-network-optimization)] — （图片来源：作者）

在我部署的应用程序中，用户可以根据经济或可持续标准轻松模拟几种场景，以便于决策。

[![](../Images/f6e91e9e022b3df35211e84575fba371.png)](https://towardsdatascience.com/data-science-for-sustainable-sourcing-a72f2c4db424)

用户可以可视化四个目标函数的成本影响 [试试这个[应用程序](https://cloud.viktor.ai/public/sustainable-supply-chain-network-optimization)]— （图片来自作者）

本文详细介绍了该工具背后的理论。

[](/data-science-for-sustainable-sourcing-a72f2c4db424?source=post_page-----efb31c780807--------------------------------) [## 可持续采购的数据科学

### 我们如何使用数据科学来选择最佳供应商，同时考虑可持续性和社会等指标……

[towardsdatascience.com](/data-science-for-sustainable-sourcing-a72f2c4db424?source=post_page-----efb31c780807--------------------------------)

> 你的解决方案有什么影响？

部署这些分析解决方案可以帮助你的组织**可持续地确保**以**最低成本**供应**原材料**。

我们现在可以进入第二个活动，即原材料的转化。

# 运营：从原材料到成品

这是T恤的**实际生产**阶段，通过将从入境物流获得的输入转化为成品。

这一活动包括诸如裁剪、缝纫、染色和印刷等过程。

![](../Images/ef76cd8535f55756a193a670abe5deb5.png)

运营优化的数据分析解决方案示例 — （图片来自作者）

💡 为了吸收原材料价格上涨，制造部门请求你的支持，以减少生产成本。

> 我们如何使用规范分析来优化生产过程？

## 使用 Wagner-Whitin 算法优化生产计划

你希望提出一个解决方案来**优化生产计划**。

[![](../Images/f6c22c549d32f858fdaa69fd53f35459.png)](https://towardsdatascience.com/production-fixed-horizon-planning-with-python-8dd38b468e86)

生产计划 — （图片来自作者）

> ***💾 输入数据****：客户发送* ***采购订单*** *，并指定* ***交付数量*** *和* ***交付时间****。*

[![](../Images/2f604c58d7e6cad0f6fa28a68ae41bbe.png)](https://towardsdatascience.com/production-fixed-horizon-planning-with-python-8dd38b468e86)

客户订单 — （图片来自作者）

在上面的示例中，客户共享了未来12个月要交付的数量。

> ***❔ 问题陈述****：如何组织生产批次，以最小化每单位的总生产成本？*

对于这个练习，你需要找到在以下方面的平衡：

+   **设置成本**：每次设置生产线时发生的固定成本

    如果你每个月只生产要求的数量，那么库存将会很低，但设置成本将会爆炸式增长。

+   **持有成本**：每单位每单位时间的存储成本

    如果你在第一个月生产全部数量，设置成本将会最小化，但你将会积累过多的库存。

> ***🚀 解决方案***：生产规划通过寻找*在* ***最小化库存*** *与* ***最大化每次设置生产数量*** *之间的平衡，* ***最小化生产总成本*** *。

[![](../Images/aab7a05e3fdccadfd136b5cda1003acf.png)](https://towardsdatascience.com/production-fixed-horizon-planning-with-python-8dd38b468e86)

生产规划输出 — （图片来自作者）

Wagner 和 Whitin 开发了一种通过动态规划寻找最优规划的算法，该算法**平衡了设置和库存成本**。

如果你想了解更多关于理论的内容，这篇文章提供了详细的介绍。

[](/production-fixed-horizon-planning-with-python-8dd38b468e86?source=post_page-----efb31c780807--------------------------------) [## 使用 Python 优化生产规划

### 使用 Python 实现 Wagner-Whitin 算法进行生产规划，以最小化生产总成本

towardsdatascience.com](/production-fixed-horizon-planning-with-python-8dd38b468e86?source=post_page-----efb31c780807--------------------------------)

## 如何衡量你解决方案的影响？

我会尝试使用一个支持特定工厂规划的原型

1.  从**评估**当前的生产规划开始：库存、特定期间的设置次数和成本。

1.  运行工具以**相同的时间段**进行。

1.  **验证**结果与**生产规划员**，并计算潜在的**节省**。

你现在可以尝试我几个月前部署的这个原型

[![](../Images/5bc241943254555ef39d87560ab4781e.png)](https://cloud.viktor.ai/public/production-planning-optimization)

生产规划优化应用 [[链接](https://cloud.viktor.ai/public/production-planning-optimization)] — （图片来自作者）

我们现在可以转向出口物流，将这些成品交付给客户。

# 出口物流：分发你的最终产品

在分销中心存储成品的情况下，物流操作团队管理流程，准备并将订单交付给客户。

对于我们的时尚零售商，这包括存储、订单履行、运输和整个欧洲的门店配送。

![](../Images/8f3a8904249de7d4549285f94d297eda.png)

出口物流优化分析解决方案示例 — （图片来自作者）

💡 因为店铺经理抱怨配送交货时间，运营总监要求你提供支持来改进分销流程。

> 我们如何能够通过诊断分析自动监控分销网络？

## 自动化供应链控制塔

你与分销规划经理保持联系；她的团队监控所有门店补货订单。

[![](../Images/8211bdd559524e9e45819cf3b11158ac.png)](https://towardsdatascience.com/automated-supply-chain-control-tower-with-python-17dbf93a18d0)

零售公司分销过程 — （图片来自作者）

配送计划员在ERP系统中监控每个在商店销售的商品的库存水平（单位）。

当库存水平达到规划人员设置的最小水平时

1.  系统自动创建补货订单，包含商品数量和请求的交货日期

1.  仓库运营团队**准备订单**进行发货

1.  运输团队**组织交付到**各个商店

> 你可以使用哪些指标来监控这个复杂的过程？

参与配送链的不同系统在每个关键步骤记录时间戳。

[![](../Images/12772ee804ce299e07ccb68139212b63.png)](https://towardsdatascience.com/automated-supply-chain-control-tower-with-python-17dbf93a18d0)

配送过程中的关键步骤及截止时间——（图示：作者）

从**订单创建**到**店铺交付**

+   在过程完成时记录时间戳；

+   完成预计时间是根据服务水平协议计算的；

为了支持她的根本原因分析，她希望你实现一个系统，**自动标记**中间步骤中的延迟。

[![](../Images/a3dc13c917fdec899854bc5935b62408.png)](https://towardsdatascience.com/automated-supply-chain-control-tower-with-python-17dbf93a18d0)

已交付货物的示例（顶部：准时，底部：延迟）——（图示：作者）

例如，底部的示例错过了“发货时间”目标，导致了交货延迟。

[![](../Images/0425d3ec6da18123ccacd079910cfafc.png)](https://towardsdatascience.com/automated-supply-chain-control-tower-with-python-17dbf93a18d0)

带有原因代码的延迟——（图示：作者）

使用这些规则，你可以自动创建迟交理由代码以支持诊断。

有关如何实施此解决方案的更多细节，

[](/automated-supply-chain-control-tower-with-python-17dbf93a18d0?source=post_page-----efb31c780807--------------------------------) [## 什么是供应链控制塔？

### 使用Python优化你的供应链网络，通过自动化解决方案跟踪你的货物并评估……

towardsdatascience.com](/automated-supply-chain-control-tower-with-python-17dbf93a18d0?source=post_page-----efb31c780807--------------------------------)

## 我们如何通过这些诊断来提高性能？

配送计划经理可以利用这些洞察力推动运营团队：

+   每周按原因代码报告延迟数量；

+   通过原因代码映射，挑战运营团队进行根本原因分析；

+   通过一个整体KPI衡量商店的影响，KPI衡量按时完整交付（OTIF）的订单百分比；

作为数据分析经理，你通过提供足够的洞察力，支持持续改进计划，贡献了交货周期的缩短。

# 结论

价值链管理是一种方法，帮助企业深入了解其运营，简化流程并为客户创造更大的价值。

> 作为数据分析经理，您扮演着关键角色。

将数据科学融入价值链管理为企业提供了提升运营效率、降低成本并最终推动盈利的机会。

通过探索从入库物流到出库分销的各种案例，我们展示了数据科学对战略流程的变革性影响。

[![](../Images/5cecbfa7a3b54b0193dee2fcbd12dead.png)](https://towardsdatascience.com/leveraging-data-analytics-for-sustainable-business-transformation-cfd8d71f97f5)

“绿色转型的四大隐形敌人”示例[文章：[链接](/leveraging-data-analytics-for-sustainable-business-transformation-cfd8d71f97f5)] — （图片由作者提供）

这可以成为任何重大转型的推动力，影响整个价值链。

随着组织不断拥抱数字化转型，您拥有展示团队潜力的工具，使其成为公司战略资产。

[](/leveraging-data-analytics-for-sustainable-business-transformation-cfd8d71f97f5?source=post_page-----efb31c780807--------------------------------) [## 利用数据分析推动可持续业务转型

### 了解如何利用分析克服规模化绿色倡议所面临的挑战，这些挑战阻碍了组织的…

towardsdatascience.com](/leveraging-data-analytics-for-sustainable-business-transformation-cfd8d71f97f5?source=post_page-----efb31c780807--------------------------------)

# 关于我

让我们在[Linkedin](https://www.linkedin.com/in/samir-saci/)和[Twitter](https://twitter.com/Samir_Saci_)上联系。我是一名[供应链工程师](https://www.samirsaci.com/blog/)，利用数据分析改善物流运营并降低成本。

如果您需要供应链转型的咨询或建议，请通过[Logigreen Consulting](https://www.logi-green.com/)与我联系。

如果您对数据分析和供应链感兴趣，请访问我的网站。

[](https://samirsaci.com/?source=post_page-----efb31c780807--------------------------------) [## Samir Saci | 数据科学与生产力

### 这是一个专注于数据科学、个人生产力、自动化、运筹学和可持续发展的技术博客……

samirsaci.com](https://samirsaci.com/?source=post_page-----efb31c780807--------------------------------)

📘 供应链分析完整指南：[分析备忘单](https://bit.ly/supply-chain-cheat)

💌 免费将最新文章直送到您的邮箱：[时事通讯](https://www.samirsaci.com/#/portal/signup)

# 参考文献

+   **“竞争优势：创造并维持卓越的表现”，** 迈克尔·波特
