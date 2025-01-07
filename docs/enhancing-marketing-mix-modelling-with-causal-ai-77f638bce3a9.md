# 通过因果AI提升营销组合建模

> 原文：[https://towardsdatascience.com/enhancing-marketing-mix-modelling-with-causal-ai-77f638bce3a9?source=collection_archive---------4-----------------------#2024-06-21](https://towardsdatascience.com/enhancing-marketing-mix-modelling-with-causal-ai-77f638bce3a9?source=collection_archive---------4-----------------------#2024-06-21)

## 因果AI，探索因果推理与机器学习的整合

[](https://medium.com/@raz1470?source=post_page---byline--77f638bce3a9--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--77f638bce3a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--77f638bce3a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--77f638bce3a9--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--77f638bce3a9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--77f638bce3a9--------------------------------) ·阅读时间：8分钟·2024年6月21日

--

![](../Images/ba87b7898135052179d91cc352ccb0d1.png)

图片来源：[Alexey Ruban](https://unsplash.com/@intelligenciya?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 这系列文章讲的是什么？

欢迎来到我的因果AI系列文章，我们将在这里探讨因果推理与机器学习模型的整合。你将会看到多个在不同商业场景中的实践应用。

在上一篇文章中，我们介绍了*验证合成控制方法的因果影响*。在本文中，我们将继续探讨*通过因果AI提升营销组合建模*。

如果你错过了上一篇关于合成控制的文章，可以在这里查看：

[](/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=post_page-----77f638bce3a9--------------------------------) [## 验证合成控制方法的因果影响

### 因果AI，探索因果推理与机器学习的整合

towardsdatascience.com](/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=post_page-----77f638bce3a9--------------------------------)

# 介绍

数字跟踪的持续挑战促使了营销组合建模（MMM）的新一轮复兴。在最近的因果AI大会上，Judea Pearl表示，营销可能是第一个采纳因果AI的行业。因此，我决定是时候开始撰写我在过去7年里有关MMM、因果AI和实验交集的学习成果了。

![](../Images/ea63f8de5bb6afdf127ae21ac6377379.png)

用户生成的图片

**以下领域将被探讨：**

+   什么是MMM？

+   因果 AI 如何增强 MMM？

+   我们可以进行哪些实验来完成三角定位？

+   营销测量中的突出挑战。

完整的笔记本可以在这里找到：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/enhancing%20marketing%20mix%20modelling%20with%20causal%20ai.ipynb?source=post_page-----77f638bce3a9--------------------------------) [## causal_ai/notebooks/enhancing marketing mix modelling with causal ai.ipynb 在主分支 ·…

### 本项目介绍了因果 AI 及其如何推动商业价值。- causal_ai/notebooks/enhancing marketing mix…

github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/enhancing%20marketing%20mix%20modelling%20with%20causal%20ai.ipynb?source=post_page-----77f638bce3a9--------------------------------)

# 什么是 MMM？

MMM 是一种统计框架，用于估算每个营销渠道对销售的贡献。它受到计量经济学的深刻影响，最简单的形式是回归模型。让我们来介绍一下其关键组成部分的基础知识！

## 回归

构建回归模型，其中因变量/目标（通常是销售）是基于多个自变量/特征预测的 —— 这些通常包括在不同营销渠道上的支出和可能影响需求的外部因素。

![](../Images/919e8bfda9b3518d2649e67e58379f6b.png)

用户生成的图片

支出变量的系数表明它们对销售的贡献大小。

Python 中的 PyMC 营销包是开始探索 MMM 的一个很好的起点：

[](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_example.html?source=post_page-----77f638bce3a9--------------------------------) [## MMM 示例笔记本 - pymc-marketing 0.6.0 文档

### 在本笔记本中，我们通过一个模拟示例来展示来自 pymc-marketing 的媒体混合模型（MMM）API。这…

www.pymc-marketing.io](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_example.html?source=post_page-----77f638bce3a9--------------------------------)

## 广告存量

广告存量指的是营销支出（或广告支出）对消费者行为的持久影响。它有助于建模营销的长期效果。通常，第一次听说某个品牌时，人们不会急于购买该产品 —— 广告存量的理念是，营销的效果是累积的。

![](../Images/a0ee219560d16686c9f7cfdc78d4cea9.png)

用户生成的图片

最常见的广告存量方法是几何衰减法，它假设广告的影响会以恒定的速率随时间衰减。尽管这种方法相对容易实现，但它的灵活性较差。值得了解的是威布尔方法，它更为灵活 —— PyMC 营销包已实现此方法，务必查阅：

[](https://www.pymc-marketing.io/en/0.6.0/api/generated/pymc_marketing.mmm.transformers.weibull_adstock.html?source=post_page-----77f638bce3a9--------------------------------) [## weibull_adstock - pymc-marketing 0.6.0 文档]

### pymc_marketing.mmm.transformers.weibull_adstock ( x , , , , , , ) [source] 威布尔 Adstock 变换。这…

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/0.6.0/api/generated/pymc_marketing.mmm.transformers.weibull_adstock.html?source=post_page-----77f638bce3a9--------------------------------)

## 饱和度

在营销的语境下，饱和度指的是收益递减的概念。增加营销投入可以提高客户获取，但随着时间的推移，影响新受众变得更加困难。

![](../Images/e5564cac2316d5b85b67bdc6fdd2318c.png)

用户生成的图像

我们可以使用几种饱和度方法。迈克利斯-门腾函数是常见的一种——你也可以在 PyMC 营销包中查看这个：

[](https://www.pymc-marketing.io/en/0.6.0/api/generated/pymc_marketing.mmm.transformers.michaelis_menten.html?source=post_page-----77f638bce3a9--------------------------------) [## michaelis_menten - pymc-marketing 0.6.0 文档]

### pymc_marketing.mmm.transformers.michaelis_menten ( x , alpha , lam ) [source] 评估迈克利斯-门腾函数…

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/0.6.0/api/generated/pymc_marketing.mmm.transformers.michaelis_menten.html?source=post_page-----77f638bce3a9--------------------------------)

# 因果 AI 如何增强 MMM？

MMM 框架通常使用平坦的回归模型。然而，营销渠道之间的相互作用存在一些复杂性。我们是否有来自因果 AI 工具箱的工具可以帮助解决这个问题？

## 因果图

因果图擅长将原因与相关性分离，这使得它们成为解决营销渠道相互作用复杂性的问题的绝佳工具。

如果你对因果图不熟悉，可以参考我之前的文章来迅速了解：

[](/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=post_page-----77f638bce3a9--------------------------------) [## 使用因果图回答因果问题]

### 因果 AI，探索将因果推理融入机器学习

towardsdatascience.com](/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=post_page-----77f638bce3a9--------------------------------)

## 理解营销图

在缺乏领域知识的情况下估计因果图是具有挑战性的。但我们可以利用因果发现来帮助我们入门——请查看我之前关于因果发现的文章，了解更多信息：

[](/making-causal-discovery-work-in-real-world-business-settings-80e80c5f66b8?source=post_page-----77f638bce3a9--------------------------------) [## 让因果发现在人类商业环境中发挥作用]

### 因果AI，探索因果推理与机器学习的结合

[towardsdatascience.com](/making-causal-discovery-work-in-real-world-business-settings-80e80c5f66b8?source=post_page-----77f638bce3a9--------------------------------)

因果发现有其局限性，应该仅用于为图形创建初始假设。幸运的是，关于营销渠道如何相互作用的领域知识非常丰富，我们可以将其纳入其中！

在这里，我分享我多年来与营销专家合作中积累的知识…

![](../Images/5db89e3e17d839acfaba055af0159779.png)

用户生成图像

+   **PPC**（付费搜索）对SEO（自然搜索）有负面影响。我们在PPC上的花费越多，SEO点击量就越少。然而，我们有一个重要的混杂因素……需求！一个简单的回归模型通常无法捕捉到这一复杂性，往往导致PPC的过度估计。

+   **社交支出**对社交点击有很强的影响，我们花费越多，潜在客户点击社交广告的次数就越多。然而，一些潜在客户可能会先看到社交广告，然后在第二天通过PPC、SEO或直接访问你的站点。一个简单的回归模型无法捕捉到这种光环效应。

+   对于**品牌支出**，也可以做类似的分析，你通过长期的品牌信息来吸引潜在客户，但没有直接的点击行动号召。这些潜在客户可能在意识到你的品牌后，通过PPC、SEO或直接访问你的站点。

+   **点击量**是中介变量。如果我们运行一个简单的回归模型并包括中介变量，这在估计因果效应时可能会引发问题。我在这里不会详细讨论这个话题，但使用因果图使我们能够在估计因果效应时仔细控制正确的变量。

希望你能从以上的例子中看到，使用因果图而非简单回归模型会大大增强你的解决方案。计算反事实和进行干预的能力也使它非常有吸引力！

值得注意的是，仍然值得将广告库存和饱和度转化纳入你的框架中。

# 我们可以进行什么实验来完成三角验证？

在使用观察性数据时，我们还应该努力进行实验，以帮助验证假设并补充我们的因果估计。获取营销中有三种主要的测试方法。让我们深入了解它们！

## 转化提升测试

社交平台如Facebook和Snapchat允许你进行转化提升测试。这是一种AB测试，我们通过治疗组与对照组的对比来衡量转化的提升。这对于评估因果图中社交支出的反事实（counterfactual）非常有用。

## 地理位置提升测试

地理提升测试可以用来估算营销暂停期间或你开始使用新渠道时的效果。这对于品牌数字广告和电视广告特别有用，因为它们没有直接的行动号召来衡量。我在上一篇文章中详细讲解了这一点：

[](/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=post_page-----77f638bce3a9--------------------------------) [## 验证合成控制方法的因果影响

### 因果 AI，探索将因果推理集成到机器学习中的方法

[towardsdatascience.com](/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=post_page-----77f638bce3a9--------------------------------)

## 切换回测

PPC（按点击付费）广告系列可以设置为每小时开启和关闭。这为切换回测提供了一个很好的机会。将 PPC 广告系列每小时开关一次，持续几周，然后计算 PPC 和 SEO 点击量在关闭与开启期间的差异。这将帮助你理解多少 PPC 可以通过 SEO 捕捉，从而评估你因果图中关于 PPC 支出的反事实。

我认为，进行实验是调整并增强你因果图信心的一个好方法。但结果也可以用来校准你的模型。看看 PyMC 团队是如何处理这个问题的：

[](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_lift_test.html?source=post_page-----77f638bce3a9--------------------------------) [## 提升测试校准 - pymc-marketing 0.6.0 文档

### 你可能听过一句话：“所有模型都是错误的；但有些模型是有用的。” 这在许多领域都是真实的，而且…

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_lift_test.html?source=post_page-----77f638bce3a9--------------------------------)

# 营销衡量中的突出挑战

今天我讲解了如何利用因果 AI 来提升 MMM（营销混合建模）。然而，因果 AI 并不能解决获取营销中的所有挑战——不幸的是，这些挑战非常多！

![](../Images/9d36431f028cc976e4f7cb30399c0326.png)

用户生成的图片

+   **根据需求预测调整支出** — 营销支出与销售量高度相关的一个原因是营销团队会根据需求预测来调整支出。解决方案之一是每周随机调整支出范围在-10%到+10%之间，加入一些变动。如你所想，营销团队通常不太喜欢这种方法！

+   **估算需求** — 需求是我们模型中的一个关键变量。然而，收集相关数据非常困难。一个合理的选择是提取与您销售的产品相关的搜索词的 Google 趋势数据。

+   **品牌的长期效应** — 品牌的长期效应很难捕捉，因为通常没有足够的信号来支持这一点。长期的地理提升测试可以帮助解决这个问题。

+   **多重共线性**——这实际上是最大的问题之一。我们所有的变量都高度相关。使用岭回归可以稍微缓解这一问题，但它仍然可能成为一个问题。因果图也能提供一些帮助，因为它本质上将问题分解为更小的模型。

+   **营销团队的支持**——根据我的经验，这将是你面临的最大挑战。因果图提供了一种吸引营销团队的不错视觉方式。它还为你创造了一个机会，在与他们合作并达成图表细节的过程中建立关系。

我就先说到这里——很希望听听你在评论中的看法！

如果你想继续深入了解因果AI，记得关注我——在下一篇文章中，我们将探讨因果AI是否能够改进我们的预测。
