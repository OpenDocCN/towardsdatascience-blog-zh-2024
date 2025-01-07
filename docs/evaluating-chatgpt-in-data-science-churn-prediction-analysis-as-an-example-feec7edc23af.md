# 评估 ChatGPT 在数据科学中的应用：以客户流失预测分析为例

> 原文：[`towardsdatascience.com/evaluating-chatgpt-in-data-science-churn-prediction-analysis-as-an-example-feec7edc23af?source=collection_archive---------3-----------------------#2024-05-26`](https://towardsdatascience.com/evaluating-chatgpt-in-data-science-churn-prediction-analysis-as-an-example-feec7edc23af?source=collection_archive---------3-----------------------#2024-05-26)

## ChatGPT 能否帮助甚至取代数据科学家？

[](https://ydong029.medium.com/?source=post_page---byline--feec7edc23af--------------------------------)![Yu Dong](https://ydong029.medium.com/?source=post_page---byline--feec7edc23af--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--feec7edc23af--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--feec7edc23af--------------------------------) [余东](https://ydong029.medium.com/?source=post_page---byline--feec7edc23af--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--feec7edc23af--------------------------------) ·阅读时长 8 分钟 ·2024 年 5 月 26 日

--

![](img/1f1f48ec9ef272f7bb8e5a0046314bb2.png)

图片由作者提供。（AI 生成的数据科学家）

你是否曾想过像 ChatGPT 这样的 AI 工具在实际数据科学应用中的有效性？或者它们在多大程度上能够协助，甚至可能取代数据科学家角色的某些方面？在这篇文章中，我使用了一个真实的世界数据集，深入探讨了这些问题，并记录了我的发现。

这篇文章最初发布在我的博客[这里](https://yudong-94.github.io/personal-website/blog/EvaluatingChatGPTinDataScience/)于 2024 年 2 月。

# 引言

自从 GPT 推出了数据分析功能已经有一段时间了。正如去年所承诺的，我写这篇文章来评估它在帮助（甚至取代）数据科学家方面的能力及其局限性。

为了完成这个任务，我让 GPT 执行了一个在实际生活中非常常见的数据科学项目——客户流失预测。这是许多企业的核心问题，也是数据科学应用案例较多的领域。我从 Kaggle 上找到了[这个在线零售客户流失数据集](https://www.kaggle.com/datasets/hassaneskikri/online-retail-customer-churn-dataset)，其中包含各种客户人口统计和参与数据，并且有一个干净的目标列，标明客户是否流失。以下是我给 ChatGPT 的完整提示：

```py
You are a professional data scientist working at an online retail company.
You have this dataset…
```
