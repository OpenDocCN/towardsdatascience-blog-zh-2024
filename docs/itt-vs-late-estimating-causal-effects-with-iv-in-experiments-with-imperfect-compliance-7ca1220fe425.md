# ITT 与 LATE：在非完全遵守的实验中使用 IV 估算因果效应

> 原文：[https://towardsdatascience.com/itt-vs-late-estimating-causal-effects-with-iv-in-experiments-with-imperfect-compliance-7ca1220fe425?source=collection_archive---------7-----------------------#2024-10-09](https://towardsdatascience.com/itt-vs-late-estimating-causal-effects-with-iv-in-experiments-with-imperfect-compliance-7ca1220fe425?source=collection_archive---------7-----------------------#2024-10-09)

## 直觉、逐步脚本和使用 IV 所需的假设

[](https://medium.com/@robson.tigre0?source=post_page---byline--7ca1220fe425--------------------------------)[![Robson Tigre](../Images/220b66a5189f45f3a43ed2c94075f577.png)](https://medium.com/@robson.tigre0?source=post_page---byline--7ca1220fe425--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7ca1220fe425--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7ca1220fe425--------------------------------) [Robson Tigre](https://medium.com/@robson.tigre0?source=post_page---byline--7ca1220fe425--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7ca1220fe425--------------------------------) ·阅读时长：9分钟·2024年10月9日

--

![](../Images/f65f6f65b7387de907d11a279a24d176.png)

图片由作者提供

在许多实验中，并非所有被分配接受治疗的个体实际上都会接受或使用治疗。例如，一家公司可能会向客户发送折扣券，旨在让他们使用这些优惠券立即购买，这可能会增加他们未来的购买。然而，并不是所有客户都会兑换优惠券。

这一情景代表了“非完全遵守”情况（[参见此处](https://medium.com/@robson.tigre0/part-1-simplifying-causal-inference-to-connect-stakeholders-and-data-scientists-e119856f0f61)），即治疗分配并不总是能导致治疗的实施。为了估算提供优惠券对未来客户购买行为的影响，我们必须区分两种主要方法：

+   **治疗意图效应（ITT）**：估算被分配接受优惠券的效果，不管是否使用了优惠券。

+   **局部平均治疗效应（LATE）**：估算在那些遵守分配的人群中的治疗效果——那些因为被分配到接受优惠券而使用了优惠券的人。

本教程介绍了这些方法背后的直觉、假设以及如何使用 R 实现它们（[参见脚本此处](https://github.com/RobsonTigre/iv_experimental_design/blob/main/IV%20imperfect%20compliance.R)）。我们还将讨论用于估算 LATE 的两阶段最小二乘法（2SLS）。

# 直觉

在存在不完美遵循的实验中，处理分配（例如，收到优惠券）与实际使用处理（例如，使用优惠券）并不完全对应。因此，简单地将处理组与对照组进行比较可能会导致误导性的结论，因为那些确实使用处理的个体（下图中的蓝色组）的治疗效果会在更大的处理组（绿色组）中被稀释。

![](../Images/ce475b292a5bd53825d042519a6647c2.png)

图片由作者提供

为了解决这种情况，我们使用了两种主要方法：

## **意向处理（ITT）**

它衡量的是*分配到某一处理的效果，无论个体是否真正执行该处理*。在我们的例子中，它比较了被分配到接收优惠券（处理组）的顾客与没有接收优惠券（对照组）的顾客未来的平均购买行为。这种方法对于理解分配本身的效果非常有用，但它可能低估了处理的影响，因为它包括了那些没有使用优惠券的个体。

![](../Images/fd97e83156ab232bb0bca56f2b867a11.png)

图片由作者提供

## **局部平均处理效应（LATE）**

在这里，我们使用工具变量（IV）方法来估计局部平均处理效应（LATE），即处理对于那些遵守分配的个体（“遵守者”）的因果效应——即那些因为被分配接收优惠券而使用优惠券的人。总结如下：

+   随机分配到处理（收到优惠券）作为工具变量，强烈预测实际的处理采纳（使用优惠券）。

+   工具变量（IV）必须满足特定的假设（相关性、外生性和排除限制），我们将详细讨论这些假设。

+   工具变量提取了优惠券使用中由随机分配所引起的变异部分，消除了可能偏倚估计的未观察因素的影响（[了解更多关于“选择偏差”的内容](https://medium.com/@robson.tigre0/part-1-simplifying-causal-inference-to-connect-stakeholders-and-data-scientists-e119856f0f61)）。

+   LATE通过调整处理分配（ITT）的影响和遵循率（在顾客被分配的情况下使用优惠券的概率），来估计处理效果。

+   它通过二阶段最小二乘法（2SLS）估计，其中每个阶段在下图中进行了说明。该方法的直观解释在[第5节](https://medium.com/@robson.tigre0/part-2-simplifying-causal-inference-to-connect-stakeholders-and-data-scientists-e2e9336e1b26)中进行了讨论。

![](../Images/24d33846812b14398f49758a792cc872.png)

图片由作者提供

# **假设和局限性**

虽然可以通过使用OLS直接获得ITT估计值，但IV方法需要强假设才能提供有效的因果估计。幸运的是，在实验情境中这些假设通常是成立的：

## **工具相关性**

工具变量（在此案例中，是被分配到处理组）必须与我们想要衡量其对未来购买影响的内生变量（优惠券使用）相关。换句话说，随机分配接收优惠券应该显著增加顾客使用优惠券的可能性。这可以通过第一阶段回归中处理组系数的大小和统计显著性来检验。

## **工具变量的外生性和排除限制**

工具变量必须与任何未观察到的影响结果（未来购买）的因素独立。它应当仅通过其对内生变量（优惠券使用）的影响来影响结果。

> 简单来说，工具变量应该仅通过影响优惠券使用来影响结果，而不是通过任何其他途径。

在我们的场景中，优惠券的随机分配确保其与任何可能影响未来购买的未观察到的顾客特征没有相关性。随机化还意味着，被分配优惠券的影响主要取决于顾客是否选择使用它。

## **局限性与挑战**

1.  LATE只提供“符合条件者”的因果效应——即那些因为收到优惠券而使用优惠券的顾客，这一效应仅适用于这一群体（仅局部有效）。它不能推广到所有顾客或因其他原因使用优惠券的顾客。

1.  当符合条件的顾客比例较低时（即只有一小部分顾客对处理作出反应），估计的效应变得不太精确，研究结果的可靠性较低。由于效应基于少数符合条件的顾客，因此也很难确定这些结果是否对更广泛的群体有意义。

1.  外生性假设和排除限制并不能直接进行检验，这意味着我们必须依赖实验设计或理论论证来支持IV实施的有效性。

# **使用R进行工具变量实操**

现在我们理解了直觉和假设，我们将通过一个例子应用这些技术，在R中估计ITT和LATE。我们将探讨以下场景，并在这个[R脚本](https://github.com/RobsonTigre/iv_experimental_design/blob/main/IV%20imperfect%20compliance.R)中进行了复现：

> 一家电子商务公司希望评估使用折扣优惠券是否能增加顾客未来的购买量。为了避免选择偏差，优惠券被随机发送给一组顾客，但并非所有收到优惠券的顾客都使用了它。此外，未收到优惠券的顾客无法使用优惠券。

![](../Images/4fdb20e440433ce09aae7487017fca47.png)

图片由作者提供

我模拟了一个数据集来表示这种情况：

+   **处理组：** 一半顾客被随机*分配接收优惠券*（处理组 = 1），另一半顾客则没有接收（处理组 = 0）。

+   **优惠券使用：** 在接受处理的个体中，*那些使用了优惠券*进行购买的人通过coupon_use = 1进行标识。

+   **收入和年龄：** 模拟的协变量，遵循正态分布。

+   **优惠券使用概率：** 为了使模型更现实，优惠券使用的概率在接受优惠券的人群中有所不同。收入较高、年龄较低的个体更倾向于使用优惠券。

+   **未来购买：** 结果变量——未来购买（以巴西雷亚尔为单位），也受到收入和年龄的影响。

+   **过去购买：** 之前几个月的购买金额（以巴西雷亚尔为单位），在优惠券分配之前。这不应与控制协变量后的优惠券使用或接收存在相关性。

+   最后，对于那些使用了优惠券的客户，模拟的优惠券使用效应设置为“**true_effect <- 50**”。这意味着，平均而言，使用优惠券将使未来购买增加50巴西雷亚尔。

## **验证假设**

**工具变量的相关性：** 第一阶段回归解释了属于处理组与使用优惠券之间的关系。在该回归中，“**treatment**”的系数为0.362，意味着约36%的处理组使用了优惠券。该系数的p值为< 0.01，t值为81.2（显著），表明处理分配（接收优惠券）显著影响优惠券的使用。

![](../Images/41299bf7cd97a8969c0e18431b3ff675.png)

作者提供的图片

**工具变量外生性和排除限制：** 从构造上讲，由于分配是随机的，因此工具变量与影响未来购买的未观察到的因素不相关。但无论如何，这些假设可以通过以下两个结果集间接进行检验：

第一个结果集包括来自第一阶段（[仅在脚本中](https://github.com/RobsonTigre/iv_experimental_design/blob/main/IV%20imperfect%20compliance.R)）和第二阶段（见下）的回归结果，有和没有协变量。这些结果应当得出相似的结论，以支持我们的工具变量（优惠券分配）仅通过内生变量（优惠券使用）影响结果（未来购买）的观点。在没有协变量的情况下，估计效应为49.24，p值 < 0.01；在有协变量的情况下，估计效应为49.31，p值 < 0.01。

![](../Images/1f4c032ce2b7d94991bbc4b7dd534f28.png)

作者提供的图片

第二个结果集包含了一项安慰剂检验，用于确定工具变量是否会影响过去的购买（从逻辑上讲，它不应影响）。该检验表明，工具变量除了通过内生变量的效应外，不会直接影响结果。无论是否包含协变量，估计效应都接近零且不具有统计显著性。

![](../Images/fce708994aa1892f857bc059f1ca29cf.png)

作者提供的图片

## **通过两阶段最小二乘法（2SLS）估计LATE**

由于上述间接测试的性质，我们最终提前预见了主要分析的结果，包括LATE（见“小节“验证假设”中的第二张图”）。但现在让我们更详细地讨论这个估计过程，它包括使用2SLS方法的两个步骤：

1.  **第一阶段**：我们将优惠券使用情况与处理分配进行回归。这会生成优惠券使用的预测值，隔离出归因于随机分配的部分。

1.  **第二阶段**：我们将第一阶段预测的优惠券使用情况与未来购买行为进行回归。这一步骤使我们能够估计优惠券使用的因果影响。

```py
first_stage <- lm(coupon_use ~ treatment + income + age, data = data)
second_stage <- ivreg(future_purchases ~ coupon_use + income + age | treatment + income + age, data = data)
```

通过应用2SLS方法，我们得出了一个无偏的因果效应估计，专门针对那些遵守治疗方案的顾客，有效地过滤了随机分配的影响。

## **估计因果效应**

**ITT估计**：它衡量了提供优惠券对顾客未来购买行为的平均影响。在我们具体的例子中，处理组的系数为R$17.89，p值小于0.01，同时控制了年龄和收入。这表明提供优惠券有显著影响，尽管由于不符合要求，ITT通常会低于真实的因果效应。

![](../Images/faa3c195790e94ee555e4ebf73537914.png)

图片来源：作者

**使用IV的LATE估计**：它代表了在符合条件者（那些因为收到优惠券而使用它的顾客）中，优惠券使用对未来购买行为的因果影响。在我们的例子中，估计的LATE接近$50（见“小节‘验证假设’中的第二张图”），这表明那些遵守使用优惠券的顾客，相较于没有优惠券的情况，他们的未来购买行为增加了大约$50。

这个结果大于ITT效应R$17.89，因为LATE特别关注符合条件的顾客，他们更有可能体验到使用优惠券的真实因果影响。现在你可以看到，在这种实验中，衡量LATE有多么重要，它帮助我们更准确地理解那些真正使用治疗方法的个体的影响，并提供关于干预真正效果的洞察。

> 出于好奇，运行以下部分的[脚本](https://github.com/RobsonTigre/iv_experimental_design/blob/main/IV%20imperfect%20compliance.R)，你将会发现，符合条件者和不符合条件者之间的均值差异，或者符合条件者和对照组之间的差异，并不能给我们模拟数据中的LATE——这是许多数据专业人士往往忽视的一点。

```py
mean_compliers - mean_non_compliers # difference in means between compliers and non-compliers
mean_compliers - mean_control # difference in means between compliers and control
```

# 需要牢记的洞察

许多数据专业人士只关注ITT估计，通常是因为它更容易解释，或因为他们不熟悉其他估计量。然而，ITT效应可能掩盖了对那些实际“接受”处理的个体的真实影响。另一方面，LATE提供了一个更准确的干预效果衡量，特别是在符合条件者中，提供了ITT单独无法捕捉的洞察。

尽管工具变量（IV）方法一开始可能显得复杂，但在实验设计中，随机分配使得IV成为一种强大且可靠的工具，用于孤立因果效应。然而需要注意的是，IV方法也可以应用于实验环境以外的情境，只要假设成立。例如，在模糊回归不连续设计（RDD）中，IV是估计局部处理效应的可信方法，尽管[尖锐RDD](https://medium.com/@robson.tigre0/when-and-how-to-apply-causal-inference-with-rdd-be6b96733dcc)可能看起来更直接。

# 感谢阅读。关注我，获取更多系列内容 :)

如果你喜欢这篇内容，并希望深入了解因果推断和计量经济学，可以在[这里](https://medium.com/@robson.tigre0)和[Linkedin](https://www.linkedin.com/in/robson-tigre/)关注我，我会在这两个平台上发布关于因果推断和职业发展的内容。

你想支持我吗？只需与可能感兴趣的人分享这个内容！

**推荐参考文献**

+   Facure M.（2022）。《勇敢与真实的因果推断》，[第8章](https://matheusfacure.github.io/python-causality-handbook/08-Instrumental-Variables.html) 和 [第9章](https://matheusfacure.github.io/python-causality-handbook/09-Non-Compliance-and-LATE.html)。

+   Huntington-Klein N.（2021）。《效应：研究设计与因果关系导论》，[第19章](https://theeffectbook.net/ch-InstrumentalVariables.html)。
