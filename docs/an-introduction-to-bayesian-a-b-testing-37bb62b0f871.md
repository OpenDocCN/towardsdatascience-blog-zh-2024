# 贝叶斯 A/B 测试简介

> 原文：[https://towardsdatascience.com/an-introduction-to-bayesian-a-b-testing-37bb62b0f871?source=collection_archive---------4-----------------------#2024-09-05](https://towardsdatascience.com/an-introduction-to-bayesian-a-b-testing-37bb62b0f871?source=collection_archive---------4-----------------------#2024-09-05)

## 从数据中获得更好的洞察

[](https://medium.com/@brechterlaurin?source=post_page---byline--37bb62b0f871--------------------------------)[![Laurin Brechter](../Images/5a68b96bddf86846a2bef9d482ef9dd3.png)](https://medium.com/@brechterlaurin?source=post_page---byline--37bb62b0f871--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--37bb62b0f871--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--37bb62b0f871--------------------------------) [Laurin Brechter](https://medium.com/@brechterlaurin?source=post_page---byline--37bb62b0f871--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--37bb62b0f871--------------------------------) ·7分钟阅读·2024年9月5日

--

A/B 测试，也称为分割测试，允许企业通过实验不同版本的网页或营销素材，来确定哪个版本在用户参与度、点击率以及最重要的转化率方面表现更好。

转化率——即完成某个期望行为（如购买或注册新闻通讯）的访客百分比——通常是决定在线营销活动成功与否的关键指标。通过仔细测试网页的不同版本，企业可以做出基于数据的决策，从而显著提高这些转化率。无论是调整行动按钮的颜色、改变标题，还是重新排列布局，A/B 测试都能提供可操作的洞察，极大提升在线表现的效果。

在这篇文章中，我将展示如何进行贝叶斯 A/B 测试来查看转化率。我们还将查看一个更复杂的例子，分析干预后客户行为变化的差异。我们还将比较这种方法与频率主义方法的差异，并探讨它们的可能优缺点。

# 比较转化率

假设我们想要改进我们的电子商务网站。我们通过让两组客户分别接触我们网站的两个版本（例如更改一个按钮）来实现这一目标。然后，我们在暴露一定数量的访客于这两个版本后停止实验。接下来，我们得到一个二进制数组，其中1表示转化，0表示没有转化。

![](../Images/538a58557a9ed41b3f2f4f02aa6e844b.png)

A/B 测试后的观察数据

我们可以通过列联表总结数据，展示（相对）频率。

```py
contingency = np.array([[obsA.sum(), (1-obsA).sum()], [obsB.sum(), (1-obsB).sum()]])
```

![](../Images/26ca07272b3e6d8f16fc1d0de2086716.png)

列联表

在我们的例子中，我们向100位客户展示了每个变体。在第一个变体中，有5位（即5%）转化，在第二个变体中，有3位转化。

## 频率派设置

我们将进行统计检验，来衡量这个结果是否具有显著性，还是仅仅由于偶然因素。在这种情况下，我们将使用卡方检验，该检验将观察到的频率与如果两种版本之间没有实际差异（即原假设成立）的情况下可能期望的频率进行比较。如需更多信息，可以查看[这篇博客](https://medium.com/p/6599271a2c31)，该博客详细讲解了相关内容。

![](../Images/c0d129c07dc84718f65917ee7c6659b2.png)

在这种情况下，p值未达到显著性阈值（例如5%），因此我们无法拒绝原假设，即这两种变体在转化率上的效果存在差异。

现在，在使用卡方检验时存在一些陷阱，这些陷阱可能导致从中获得的洞察结果出现误差。首先，卡方检验对样本大小非常敏感。当样本量很大时，即使是微小的差异也会变得显著，而当样本量较小时，检验可能无法检测到差异。如果任何字段的计算期望频率小于五，这种情况尤为明显。在这种情况下，需要使用其他检验方法。此外，卡方检验并未提供关于差异大小或实际意义的信息。在同时进行多个A/B测试时，由于偶然性找到至少一个显著结果的概率会增加。卡方检验没有考虑这一多重比较问题，如果没有适当控制（例如，通过Bonferroni校正），可能会导致假阳性。

另一个常见的陷阱出现在解释卡方检验结果时（或者说任何统计检验的结果）。p值给出了在原假设成立的前提下观察到当前数据的概率。但它并不说明转化率或它们的差异的分布情况。这是一个主要问题。我们不能做出类似“变体B的转化率为2%的概率是X%”这样的陈述，因为为了做出这种陈述，我们需要转化率的概率分布（以观察到的数据为条件）。

这些陷阱凸显了理解卡方检验的局限性并在其约束范围内恰当使用它的重要性。在应用该检验时，必须结合其他统计方法和背景分析，以确保得出准确且有意义的结论。

## 贝叶斯设置

在了解了频率派处理A/B测试的方法后，我们来看看贝叶斯版本。在这里，我们直接对数据生成过程（也就是转化率）进行建模。也就是说，我们指定了一个似然函数和一个先验，它们可以导致观察到的结果。可以把它看作是指定了一个“故事”，解释数据可能是如何生成的。

![](../Images/a995f4986e8f48bfcb1bfef90acce58c.png)![](../Images/ce455ab36315c7b421e5296cb7bebc5c.png)

贝叶斯公式

在这种情况下，我使用了Python包[PyMC](https://www.pymc.io/welcome.html)进行建模，因为它具有清晰简洁的语法。在‘with’语句中，我们指定了可以组合的分布，这些分布会导致数据生成过程。

```py
with pm.Model() as ConversionModel:
    # priors
    pA = pm.Uniform('pA', 0, 1)
    pB = pm.Uniform('pB', 0, 1)

    delta = pm.Deterministic('delta', pA - pB)

    obsA = pm.Bernoulli('obsA', pA, observed=obsA)
    obsB = pm.Bernoulli('obsB', pB, observed=obsB)

    trace = pm.sample(2000)
```

我们有pA和pB，它们分别是A组和B组的转化概率。通过使用pm.Uniform，我们指定了我们对这些参数的先验信念。这是我们可以编码先验知识的地方。在我们的案例中，我们持中立态度，允许任何介于0和1之间的转化率具有相同的可能性。

然后，PyMC允许我们从后验分布中抽样，后验分布是我们在看到数据后对参数的更新信念。我们现在得到了转化概率的完整概率分布。

![](../Images/4e9275410a7ad50d7b7a954ad9d34ddb.png)

转化率的后验分布

从这些分布中，我们可以直接读取感兴趣的量，例如可信区间。这使我们能够回答诸如“转化率在X%到Y%之间的可能性是多少？”这样的问题。

贝叶斯方法提供了更大的灵活性，稍后我们将看到这一点。与频率派方法相比，解释结果也更加直接和直观。

# 建模任意数据生成过程

接下来，我们将看看一个更复杂的A/B测试示例。假设我们在观察期开始时让受试者接触某种干预措施。这将是A/B部分，其中一组接受干预A，另一组接受干预B。然后，我们观察两个小组在接下来的100天内与我们的平台的互动（可能是登录次数之类的指标）。我们可能看到的情况如下：

![](../Images/a5d22d6182ecfc7aac0644ccdaebb43e.png)

现在我们想知道这两个组在干预反应上是否存在有意义的差异。我们如何用统计检验来解决这个问题呢？坦率地说，我不知道。必须有人为这种特定的情境提出统计检验。另一种方法是再次回到贝叶斯框架，我们将首先构建一个数据生成过程。我们假设每个个体是独立的，且与平台的互动呈正态分布。它们有一个开关点，在这个点上，它们会改变行为。这个开关点只会出现一次，但可以发生在任何时刻。在开关点之前，我们假设互动强度的均值为mu1，之后为mu2。这个语法可能看起来有点复杂，尤其是你从未使用过PyMC的情况下。如果是这样，我建议你查看他们的[学习资料](https://www.pymc.io/projects/docs/en/stable/learn.html)。

```py
with pm.Model(coords={
    'ind_id': ind_id,
}) as SwitchPointModel:

    sigma = pm.HalfCauchy("sigma", beta=2, dims="ind_id")

    # draw a switchpoint from a uniform distribution for each individual
    switchpoint = pm.DiscreteUniform("switchpoint", lower=0, upper=100, dims="ind_id")

    # priors for the two groups
    mu1 = pm.HalfNormal("mu1", sigma=10, dims="ind_id")
    mu2 = pm.HalfNormal("mu2", sigma=10, dims="ind_id")

    diff = pm.Deterministic("diff", mu1 - mu2)

    # create a deterministic variable for the 
    intercept = pm.math.switch(switchpoint < X.T, mu1, mu2)

    obsA = pm.Normal("y", mu=intercept, sigma=sigma, observed=obs)

    trace = pm.sample()
```

该模型随后可以显示开关点位置的分布，以及开关点前后差异的分布。

![](../Images/3f8ca155b8c7ec99464d2cff8a319d22.png)

我们可以通过森林图仔细查看这些差异。

![](../Images/114a4e4f6b463873636e6fd1e5610db9.png)

我们可以清楚地看到，组 A（id 0 到 9）和组 B（10 到 19）之间的差异非常明显，组 B对干预的反应明显更强烈。

# 结论

贝叶斯推断在建模数据不足且我们关注建模不确定性的情况下，提供了很大的灵活性。此外，我们必须明确我们的假设并对其进行思考。在更简单的情况下，频率学派的统计检验通常更容易使用，但我们必须注意与之相关的假设。

*本文中使用的所有代码都可以在我的* [*GitHub*](https://github.com/LaurinBrechter/BayesianInference/blob/main/hierarchical_switchpoint.ipynb)*上找到。除非另有说明，所有图片均由作者创作。*
