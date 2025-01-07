# 《15分钟了解如何在商业中使用因果推断（含安慰剂测试）》

> 原文：[https://towardsdatascience.com/your-15-minute-guide-on-using-causal-inference-in-business-with-placebo-tests-102b5bed7c95?source=collection_archive---------3-----------------------#2024-07-15](https://towardsdatascience.com/your-15-minute-guide-on-using-causal-inference-in-business-with-placebo-tests-102b5bed7c95?source=collection_archive---------3-----------------------#2024-07-15)

## “相关性不等于因果关系。”那么，让我们来计算因果关系吧。

[](https://medium.com/@itsaithomas?source=post_page---byline--102b5bed7c95--------------------------------)[![Tomas Jancovic (It's AI Thomas)](../Images/64dde7765fc28898c952dd2aa24d265d.png)](https://medium.com/@itsaithomas?source=post_page---byline--102b5bed7c95--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--102b5bed7c95--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--102b5bed7c95--------------------------------) [Tomas Jancovic (It's AI Thomas)](https://medium.com/@itsaithomas?source=post_page---byline--102b5bed7c95--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--102b5bed7c95--------------------------------) ·18分钟阅读·2024年7月15日

--

作者：[Tomas Jancovic (It's AI Thomas)](https://medium.com/u/fbf5eb668bee?source=post_page---user_mention--102b5bed7c95--------------------------------)

2024年7月15日（更新于2024年7月24日，增加了风险因素示例）

![](../Images/b25834279294444d02fa1cc4c0b6af4c.png)

包含所有变量的因果机器学习模型（来源：本人在Python中的制作）

医生使用临床因果推断和安慰剂等技术来判断药物是否真的有效。

然而，因果推断、因果机器学习和因果AI，正被像 [微软、TripAdvisor 和 Uber](https://www.researchgate.net/publication/353908277_Causal_Inference_and_Machine_Learning_in_Practice_with_EconML_and_CausalML_Industrial_Use_Cases_at_Microsoft_TripAdvisor_Uber) 这样的企业广泛应用于解决商业问题，因为它们帮助企业突破简单的相关性分析。

你可能以前听说过：**“相关性不等于因果关系。”**

> 相关性（r）是一个范围从0到1的值，用来显示两个或多个变量如何仅仅一起变化。

但如果相关性不足以说明问题，那么因果关系是什么，我们又该如何计算它呢？

这就是因果推断建模发挥作用的地方。

> *受临床研究和医学的启发，我们可以弄清楚到底是什么因素影响了我们的业务变量，比如KPI等*…
