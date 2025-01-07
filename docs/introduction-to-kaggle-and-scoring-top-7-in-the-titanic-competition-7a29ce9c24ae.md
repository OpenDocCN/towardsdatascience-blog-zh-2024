# Kaggle 简介及如何在 Titanic 竞赛中获得前 7%的成绩

> 原文：[`towardsdatascience.com/introduction-to-kaggle-and-scoring-top-7-in-the-titanic-competition-7a29ce9c24ae?source=collection_archive---------6-----------------------#2024-04-23`](https://towardsdatascience.com/introduction-to-kaggle-and-scoring-top-7-in-the-titanic-competition-7a29ce9c24ae?source=collection_archive---------6-----------------------#2024-04-23)

## 开始使用 Kaggle 并提交一个（不错的）初步解决方案

[](https://medium.com/@hrmnmichaels?source=post_page---byline--7a29ce9c24ae--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--7a29ce9c24ae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7a29ce9c24ae--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a29ce9c24ae--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--7a29ce9c24ae--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a29ce9c24ae--------------------------------) ·14 分钟阅读·2024 年 4 月 23 日

--

[Kaggle](https://www.kaggle.com/)是一个有趣的平台，举办各种数据科学和机器学习竞赛，涵盖了如[体育](https://www.kaggle.com/competitions/nfl-big-data-bowl-2024)、[能源](https://www.kaggle.com/competitions/predict-energy-behavior-of-prosumers)或[自动驾驶](https://www.kaggle.com/competitions/lyft-motion-prediction-autonomous-vehicles)等主题。

在这篇文章中，我们将介绍 Kaggle，并解决入门级的[“Titanic”挑战](https://www.kaggle.com/competitions/titanic)。我们将解释如何处理并解决这样的挑战，并通过一个“Titanic”竞赛的前 7%解决方案进行演示。

![](img/4d456d0962291b5d6047de081115584b.png)

图片由[Derek Oyen](https://unsplash.com/@goosegrease?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/glacier-during-daytime-4ReskwNsh68?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

你可以在[Github](https://github.com/hermanmichaels/titanic)上找到完整的代码，并在阅读本文时跟随代码，也可以复制我的准确分数。在这篇文章中，我们遵循了一些我认为的 Python 最佳实践，并使用了一些有用的工具，如 mypy 和 poetry。话不多说，让我们直接开始吧。

# Kaggle

[Kaggle](https://www.kaggle.com/) 提供了种类繁多的数据科学/机器学习竞赛，具体例子请参见介绍部分。这是一个测试和提高你数据科学/机器学习知识的绝佳途径，同时还能学习如何动手解决问题。而且，你甚至可以赢得现金奖励！然而，Kaggle 聚集了许多顶尖的数据科学家和机器学习专家——奖励只会颁发给少数几个最佳解决方案（在数百个或数千个解决方案中）——因此，在这里获胜是...
