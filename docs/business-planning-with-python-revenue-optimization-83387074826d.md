# 使用Python进行商业规划——收入优化

> 原文：[https://towardsdatascience.com/business-planning-with-python-revenue-optimization-83387074826d?source=collection_archive---------5-----------------------#2024-06-26](https://towardsdatascience.com/business-planning-with-python-revenue-optimization-83387074826d?source=collection_archive---------5-----------------------#2024-06-26)

## 你如何利用数据分析帮助小型企业在保持或提高盈利能力的同时，最大化其收入？

[](https://s-saci95.medium.com/?source=post_page---byline--83387074826d--------------------------------)[![Samir Saci](../Images/722d1f56a3308f6527d82b5ab97064ec.png)](https://s-saci95.medium.com/?source=post_page---byline--83387074826d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--83387074826d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--83387074826d--------------------------------) [Samir Saci](https://s-saci95.medium.com/?source=post_page---byline--83387074826d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--83387074826d--------------------------------) ·13分钟阅读·2024年6月26日

--

![](../Images/d7d35c2f5711acb07c0133d038b04810.png)

使用Python进行收入优化——（图片由作者提供）

**收入优化**是通过实施策略来最大化公司收入，同时保持盈利能力的过程。

[在上一篇文章中](/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)，我们开始构建一个模型，帮助小企业主（*我的朋友*）管理库存并避免流动性问题。

[![](../Images/780427e0f79f330e6f0aa29d26960945.png)](https://towardsdatascience.com/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)

商业规划——库存和现金流管理 [文章：[链接](/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)] ——（图片由作者提供）

多亏了这个模型，我们消除了限制商业增长的瓶颈，如库存和流动性问题。

> 如何在不降低盈利能力的情况下，增加收入增长的最佳策略是什么？

现在的重点是扩大市场并带来额外的收入，同时不危及商业模式。

本文将把我朋友的商业计划转化为一个Python模型，**模拟不同场景**以找到**最佳收入优化策略**。

[![](../Images/42d4ed69bfd0d204bb39ea158ec3dc4e.png)](https://towardsdatascience.com/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)

我朋友的商业模式——（图片由作者提供）

该案例研究基于一个现有的小公司，该公司将可再生材料制成的杯子销售给咖啡馆和分销商。

第一部分将**更新Python模型**，并加入新的**业务和运营假设**。

[![](../Images/e5e48b0e0682fcd0fd39cc5b68c91442.png)](https://towardsdatascience.com/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)

定价策略 — （图片由作者提供）

在第二部分中，我们将测试**几种以价格为驱动的增长策略**，并衡量它们对**不同销售增长**场景下盈利能力的影响。

```py
Summary

**I. Update the Business Planning Model**
Adapt the model to the new business practice
 **1\. Inventory Management Simulation**
Update the inventory management rule with a periodic rule.
 **2\. Degressive Tariff for Inbound Freight**
Update the pricing structure after negotications with the forwarder
 **3\. Economies of Scale**
Selling more to reduce the impact of fixed costs.
  **4\. Translating Business Ideas with Analytics**
**II. Revenue Optimization: Growth with Higher Profit**
What is the best strategy to increase turnover with the same profitability.
 **1\. Baseline & Profitability Indicators**
What is the current level of profitability.
 **2\. Five growth scenarios for each pricing strategy**
Assess 3 pricing strategies using their impact on profitability.
**III. Conclusion**
 **1\. What is the best strategy?**
Implement middle-risk pricing until reaching +50% growth
 **2\. Next Step: Go green**
Let us measure and reduce the environmental impact of this business.
 **3\. Automate Accounting Tasks using Python** Automatically extract and process data from multiple excel files
```

# 更新商业规划模型

在评估商业增长策略之前，我们将根据我从朋友那里收集的额外业务见解来更新模型。

[在第一篇文章中](/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)，我介绍了构建一个销售可再生咖啡杯的业务价值链模型的方法。

[![](../Images/f0fc828bd837c471f48360e9754dc706.png)](https://towardsdatascience.com/business-planning-with-python-inventory-and-cash-flow-management-4f9beb7ecbec)

使用Python构建模拟模型 — （图片由作者提供）

该模型使用历史销售数据、运营约束和成本信息作为输入，估算您的业务盈利能力和现金流。

它涵盖了从供应商到客户的[完整价值链](/data-science-for-value-chain-management-efb31c780807)。

![](../Images/4acb09a6d98e8fc3514cabad095c3bca.png)

咖啡杯的价值链 — （图片由作者提供）

+   一个**库存管理模块**，根据**客户需求**和**补货交货时间**向供应商发送采购订单。

+   **入境/出境物流流程建模**，包括交货时间和每个处理单元（托盘或纸箱）的成本。

+   **两个销售渠道（直接销售给咖啡店和经销商）各自有**销售佣金（直接销售为30%）和付款条款。

> 什么是最佳商业模型？

我们模拟了**战略业务和运营决策**对不同场景下**盈利能力**和**流动性需求**的影响。

+   初始场景是**基准**（他们现在如何运营业务）。

+   以下场景测试了减少库存覆盖、使用空运进行入境或仅向经销商销售的影响。

![](../Images/739138ef97b6ab52a436822bd06d7a99.png)

找到最佳商业模型的5种场景 — （图片由作者提供）

结论是，**最佳策略**是**仅向经销商销售**并保持**6周库存**覆盖。

> 我的朋友：“我们不再每天检查库存了，而且我们与货运代理商达成了新的协议”。

随着情况的变化，我们需要通过更新模型来进行调整。

+   切换到**周期性审查政策**进行库存管理。

+   更新**成本结构**和**付款条款**的入境物流。

## 库存管理模拟

初始模型基于**持续审查政策（s，Q）**，当**库存低于阈值（s）**时触发补货订单（Q）。

![](../Images/28df665be3192233bc40927fa27b50d6.png)

2023年客户需求（以托盘计） — （作者插图）

本模块使用客户需求、补货提前期和安全库存参数，确保我们拥有正确的库存水平。

然而，我的朋友**没有专门的库存团队**来每天监控库存。

> 我们的资源有限，既要管理库存，又要向供应商下订单。

因此，我使用**周期性复审政策 Order-Up-To-Level (R, S)** 来调整模型。

+   **周期性复审**意味着库存团队每**R周**进行一次复审。

+   （R, S）意味着团队每**R周**下单，订购足够数量以达到**库存水平S**。

**S**级是您需要的库存，以满足客户在下次复审之前**R周**内的需求。

![](../Images/ddb2546961ba6804b329c28c3deb49e5.png)

库存管理规则参数 — （作者插图）

我们通过**补货提前期**、**目标周期服务水平**和**客户需求的标准偏差**来定义它。

![](../Images/f0318900e802eefecaa656dcf110b129.png)

库存管理参数 — （作者插图）

💡 关于周期性复审政策的更多细节，请查看这篇文章

[](/inventory-management-for-retail-periodic-review-policy-4399330ce8b0?source=post_page-----83387074826d--------------------------------) [## 零售库存管理 — 周期性复审政策

### 基于周期性复审政策实施库存管理规则，以减少商店补货的次数

towardsdatascience.com](/inventory-management-for-retail-periodic-review-policy-4399330ce8b0?source=post_page-----83387074826d--------------------------------)

结果如下图所示。

![](../Images/1210c79d2cc5ab139ad3b3855be37089.png)

库存管理规则可视化 — （作者插图）

**📈 图例**

+   蓝色图线代表**最佳策略**，即**每4周订货**。

+   绿色图线代表**手头库存**（ioh），即仓库中存储的托盘数量。

现在库存管理模块已更新，我们可以专注于进口货运成本和提前期。

## 进口货运的递减关税

与货运代理商成功谈判后，他们达成了一个有利的协议：

+   付款条款：他们可以在交货后**4周内支付**给货运代理。

+   达到特定**阈值**时，递减关税并提供**返利**。

![](../Images/1bcec9ca9e9820d330387e9b3905908e.png)

递减海运运费 — （作者插图）

> 这对流动性和盈利能力有什么影响？

我们更新了进口物流成本的计算方式，并加入了返利机制。

![](../Images/d9d5f9345bf767c4bb7fadaa00909f33.png)

进口成本结构 — （作者插图）

这里是最佳场景下的一些快速胜利

![](../Images/4217ffcab1c0bf29399c68509761bacd.png)

更新后的场景包含谈判后的合同条款和新的库存政策 — （作者插图）

+   由于新的条款，维持业务不再需要现金流。

+   **13.6%**的**物流成本**减少，源自新的运费价格，这导致**商品销售成本（COGS）**平均降低**2.4%**。

下图显示我们**在支付供应商**和货运代理之前**先收取分销商的付款**。

![](../Images/378127027ede9a8d1f79205a5ca1df2a.png)

更新商业模式下的现金流—（图源：作者）

我们只解决了最优情景下的流动性问题。

> 萨米尔：“下一步是什么？”
> 
> 我的朋友：“我们需要增加收入。”

为了提高盈利能力，我的朋友现在希望通过增加销售量来减少固定成本的影响。

## 规模经济

**规模经济**指的是通过增加销售量，企业能够实现的成本优势，从而降低单位的固定成本。

这些成本与公司的结构相关，无法压缩。

![](../Images/2010eebbc0a5431052d6e6d3793d70d1.png)

受规模经济影响的指标—（图源：作者）

然而，如果我们增加销售并控制这些固定成本，我们就能减少它们对每个售出箱子的影响。

# 收入优化：通过更高的利润实现增长

经过24个月的运营，我的朋友找到了**一位商业伙伴**，他在**食品和饮料**行业有25年的经验。

> 我的朋友：“她带来了投资和市场专业知识，目的是推动增长。”

她估算了如果遵循特定商业策略，他们能够达到的增长。

![](../Images/0f10c9dae410fa2fa012a2b60b322e1e.png)

每个销售渠道的增长潜力—（图源：作者）

在分析完商业模式后，她提议改变定价机制，以增加平均购物篮大小。

> 我的朋友：“她想通过阶梯定价折扣来激励批量购买。”

![](../Images/5f770931a47590e19ff7ae947b68050c.png)

定价策略示例—（图源：作者）

然而，**风险在于影响盈利能力**并导致**流动性崩溃**，因为我们减少了每个箱子售出的营业额。

> 我的朋友：“如果我们实施策略X，你能告诉我需要多少增长才能保持相同的盈利能力吗？”

该模型可以估算为了在每种策略中相较基线改善盈利能力所需的最小增长。

## 基线与盈利能力指标

对于这次分析，我们使用2023年的历史销售数据，并更新了条款和库存管理规则，来定义基线。

![](../Images/ace23d6bb1df4c86adc5a9cb1f19d4ef.png)

销售渠道营业额—（图源：作者）

超过70%的营业额来自直接销售给咖啡店，其余来自具有四周付款期限的分销商。

![](../Images/473308521f5933216eb9b74eefa1f8db.png)

基线现金流—（图源：作者）

感谢我们与供应商和咖啡店之间有利的付款条款，我们没有流动性问题。

![](../Images/b0f837ae1a87ee73fd976cbdffa68614.png)

基准指标 — （图像由作者提供）

考虑到固定和变动成本，对于这个情景，我们每售出一托盘可以获得**3,910美元的利润**。

这一盈利能力受到绿色指标的影响，这些指标涵盖了固定和变动成本。

## 每种定价策略的五种增长情景

我朋友的商业伙伴将定价策略建立在市场实践和她的行业知识之上。

该模拟旨在通过基于数据的讨论来支持商业决策。

![](../Images/f08095fa52120c7aef5f65288452d8ab.png)

模拟情景 — （图像由作者提供）

> 我的朋友：“如果你想应用定价策略1，我们能确保至少有50%的增长吗？如果不能，我们的盈利能力就会下降。”

让我们从第一种定价策略开始。

## **情景1：低风险定价策略1**

这一策略通过在**订单超过50箱**时提供**2.5%的折扣**，激励客户至少订购一整托盘。

> 我们需要多大的增长才能保持相同的盈利能力？

正如我们在折扣情景中看到的，实施新定价会导致**171 ($/托盘)**的利润损失。

![](../Images/6897d9e6b73333d1b286609252611ffa.png)

模拟定价策略1 — （图像由作者提供）

然而，由于销售增长会机械性地降低单位成本，当我们达到50%增长时，这一损失是**会被弥补的**。

+   由于**入库流优化**，变动成本也有所降低。

+   由于所需的更高安全库存，托盘的储存成本增加。

如果她想实施这个策略，必须带来至少**当前营业额的1.5倍**。

![](../Images/27dee90a47bcf103ac399e7485995582.png)

模拟结论 — （图像由作者提供）

> 商业伙伴：“如果没有额外的折扣，我们永远无法超过50%的增长。”

## **情景2：中风险定价策略2**

确实，第一种定价策略并未激励客户订购超过1托盘（50箱）。

![](../Images/fe501dfb8a43c90fa55645dc0d05b3d6.png)

模拟定价策略2 — （图像由作者提供）

因此，他们希望对订购超过**150箱（3托盘）**的客户增加5%的折扣。

如果我们保持相同的销售量，实施这个额外的折扣会导致**315 ($/单位)**的利润损失。

> 如果我们只达到50%的增长，是否会失去盈利能力？

是的，我们的利润是**3,751 ($/单位)**，相比基准的3,910 ($/单位)，增长了50%。

因此，我们至少需要**+200%的销售增长**，才能恢复基准情景下的盈利水平。

+   与第一种定价策略不同，每托盘的营业额在达到100%增长后会下降，直到达到一个平稳状态。

+   销售成本（COGS）低于第一种定价策略，因为销售代表的30%销售佣金是基于开票金额。

如果我们实现+200%的销售增长，相较于第一种定价策略，我们会损失**171 ($/托盘)**的利润。

![](../Images/8a4f29c6656716e51c20d64b022ffd72.png)

定价策略2的模拟结论 — （图像由作者提供）

然而，根据商业伙伴的说法，这一策略更有可能帮助我们实现这些目标。

> 我的朋友: “如果我们对大宗订单再实施10%的折扣呢？”

## 情景3：高风险定价3

即使这看起来不是一个好主意，他们还是想估算在大于500箱的订单上增加10%返利后的利润损失。

> Samir: “这给了你关于你商业的有趣见解。”

![](../Images/bc6385b43fafdce0b458948a7c18f9b7.png)

定价策略3的模拟结果 — （图像来源：作者）

在返利情景下盈利的轻微减少（*从策略2的3,595 $/托盘到策略3的3,588 $/托盘*）表明，他们当前几乎没有超过500箱的大订单。

然而，他们将有一个糟糕的体验——当他们实现+50%的增长时，会看到盈利能力下降。

> Samir: “你永远无法达到初始情景下的盈利水平。”

该模型提供了通过数据驱动的见解来评估商业伙伴建议的方法。

![](../Images/edf9336d1bbce36564c2ac9da8ad72a8.png)

定价策略3的模拟结果 — （图像来源：作者）

## 用分析来转化商业思路

这个练习展示了如何将基于直觉的商业思路转化为实际数据，从而拯救利润并避免破产。

我的朋友曾是一名数据科学家，采用定量方法进行商业分析，这可能与餐饮行业的文化不太兼容。

> 基于你的直觉，你的商业决策的定量影响是什么？

该模型支持在讨论战略方法时，与商业伙伴一起通过第三方客观评判来评估思路。

> 根据模拟，如果我们选择定价策略2，我们至少需要+50%的增长。我们能做到吗？

我已经开始在一家小型面包店中使用数据分析来帮助商业决策，该面包店希望优化其盈利能力。

[![](../Images/68bb293fb2a05f95e6c63543b1845a13.png)](https://towardsdatascience.com/maximize-your-business-profitability-with-python-fbefebbdf802)

使用Python最大化商业盈利能力 [文章: [链接](/maximize-your-business-profitability-with-python-fbefebbdf802)] — （图像来源：作者）

当时，这只是一个小练习，利用线性规划为商店老板提供战略性见解。

> 最大化盈利能力的最佳商品组合是什么？

[](/maximize-your-business-profitability-with-python-fbefebbdf802?source=post_page-----83387074826d--------------------------------) [## 使用Python最大化你的商业盈利能力

### 使用线性规划帮助你当地的面包店通过选择合适的商品来提高其商业盈利能力…

towardsdatascience.com](/maximize-your-business-profitability-with-python-fbefebbdf802?source=post_page-----83387074826d--------------------------------)

这个建模继续了尝试使用数据分析来支持战略商业决策的过程。

> 你有没有类似项目的例子，且这些项目有数据分析支持？

# 结论

## 最佳策略是什么？

根据当前的订单情况，我建议使用定价策略2，直到他们达到50%的增长。

然后，他们可以专注于向分销商销售，以避免销售佣金。

![](../Images/c62f5038bed9e85972f40e9c106f9192.png)

最优销售策略 — （图片来源：作者）

这样可以帮助我们设定更好的折扣，因为削减佣金能节省30%的营业额。

他们可以根据分销商的订单情况重新进行分析，以设定更新的销售增长目标。

> 你同意这种方法吗？

## 下一步：走向绿色

我的朋友被他的客户挑战，要提供这些可再生杯子对环境影响的可见性。

> 他们将这些杯子宣传为“可持续的”，具有“低环境影响”，并且基于“公平贸易”。

因此，我们将开始沿着价值链估算这项活动的环境足迹，并进行完整的[生命周期评估（LCA）](/what-is-a-life-cycle-assessment-lca-e32a5078483a)。

[![](../Images/b26c805636d4f2cfc8f5a86cce84c07f.png)](https://towardsdatascience.com/what-is-a-life-cycle-assessment-lca-e32a5078483a)

什么是生命周期评估 [文章: [链接](/what-is-a-life-cycle-assessment-lca-e32a5078483a)] — （图片来源：作者）

这将提供关于采购、储存和交付这些杯子到最终客户的影响的可见性。

> 我们如何减少这一足迹？选择合适的供应商。

[可持续采购](/data-science-for-sustainable-sourcing-a72f2c4db424)是将社会和环境绩效因素融入选择供应商过程中的方法。

![](../Images/c15cd533a7e8bd2bd185daba7e422489.png)

可持续采购方法 [文章: [链接](/data-science-for-sustainable-sourcing-a72f2c4db424)] — （图片来源：作者）

其目标是基于

+   不同市场的客户需求（单位/月）。

+   一份潜在供应商的列表（在不同地点），包括他们的生产成本和环境影响。

![](../Images/9758f94e00165bacb1a22637466acee4.png)

可持续供应链网络设计示例 — （图片来源：作者）

我们将使用线性编程和Python来选择最优的供应商组合，以最小化成本并减少二氧化碳排放或水资源使用。

最后，我们将使用绿色库存管理来查看分销网络和最后一公里交付。

![](../Images/98d8a83e2547085e5e0777716c7eb044.png)

绿色库存管理 [文章: [链接](/data-science-for-sustainability-green-inventory-management-e7ddfd97696f)] — （图片来源：作者）

这个理念是通过激励分销商减少订货频率并最大化每次交货的数量，来实现可持续的库存管理。

> 我的朋友是如何收集用于构建这个模型的财务数据的？

## 使用Python自动化会计任务

我的朋友正在寻找一种解决方案来自动化重复的会计任务，并提高他们团队的生产力。

因此，我开发了一个解决方案，基于[本文中](https://www.samirsaci.com/?source=post_page-----83387074826d--------------------------------)提供的示例。

![](../Images/a1df864cfb5e53e3ae4a5c1c5266c7f0.png)

数据收集与处理框架 —— （图片由作者提供）

这个想法是使用Python自动提取数据并处理Excel文件中的数据。

![](../Images/4115b0180dde1650934dd00cc03c0ecd.png)

输入文件示例 —— （图片由作者提供）

在这个示例中，文件遵循相同的模板（如上所示）。

然后很容易创建一个Python脚本，能够打开多个文件，提取信息并生成报告。

该报告随后被用来训练这里介绍的模型。

有关会计任务自动化的更多信息，请查看这篇包含构建该工具详细步骤的文章。

[](/help-your-finance-team-to-automate-accounting-tasks-using-python-68bcefe7524c?source=post_page-----83387074826d--------------------------------) [## 使用Python自动化会计任务

### 构建自动化解决方案，简化财务审计中的重复任务，并与财务同事共享……

towardsdatascience.com](/help-your-finance-team-to-automate-accounting-tasks-using-python-68bcefe7524c?source=post_page-----83387074826d--------------------------------)

# 关于我

让我们在[Linkedin](https://www.linkedin.com/in/samir-saci/)和[Twitter](https://twitter.com/Samir_Saci_)上连接。我是一名使用数据分析来改善物流操作并降低成本的[供应链工程师](https://www.samirsaci.com/blog/)。

如果你需要关于供应链转型的咨询或建议，请通过[Logigreen Consulting](https://www.logi-green.com/)与我联系。

如果你对数据分析和供应链感兴趣，请访问我的网站。

[](https://samirsaci.com/?source=post_page-----83387074826d--------------------------------) [## Samir Saci | 数据科学与生产力

### 专注于数据科学、个人生产力、自动化、运筹学和可持续性的技术博客…

samirsaci.com](https://samirsaci.com/?source=post_page-----83387074826d--------------------------------)

📘 你完整的供应链分析指南：[分析备忘单](https://bit.ly/supply-chain-cheat)
