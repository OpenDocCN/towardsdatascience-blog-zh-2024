# 使用 Airflow 和 Mlflow 构建机器学习管道：预订取消预测

> 原文：[`towardsdatascience.com/build-machine-learning-pipelines-with-airflow-and-mlflow-reservation-cancellation-forecasting-da675d409842?source=collection_archive---------1-----------------------#2024-01-12`](https://towardsdatascience.com/build-machine-learning-pipelines-with-airflow-and-mlflow-reservation-cancellation-forecasting-da675d409842?source=collection_archive---------1-----------------------#2024-01-12)

## 通过高级机器学习任务学习如何创建可复现的、适用于生产环境的机器学习管道

[](https://medium.com/@jeremyarancio?source=post_page---byline--da675d409842--------------------------------)![Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--da675d409842--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da675d409842--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da675d409842--------------------------------) [Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--da675d409842--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da675d409842--------------------------------) ·21 分钟阅读·2024 年 1 月 12 日

--

现如今，公司需要工程技能来开发和部署机器学习模型到生产环境中。

**机器学习工程师**现在应具备处理整个模型生命周期的能力，涵盖如*实验跟踪*、*调度器*、*CI/CD*、*版本控制*和*模型注册表*等方面——从数据提取到生产化。

在本文中，**我们将深入探讨我为短期租赁行业领导者进行的机器学习任务，**并使用该行业中广泛应用的两种工具：**Airflow**和**Mlflow**。

本文不仅指导读者解决一个真实的机器学习项目，还提供了该项目的完整仓库，供未来在机器学习项目中使用。

[](https://github.com/jeremyarancio/reservation_cancellation_prediction?source=post_page-----da675d409842--------------------------------) [## GitHub - jeremyarancio/reservation_cancellation_prediction: 预测预订是否会被取消…

### 通过 Airflow 和 Mlflow 的强大机器学习管道预测预订是否会被取消 - GitHub …

github.com](https://github.com/jeremyarancio/reservation_cancellation_prediction?source=post_page-----da675d409842--------------------------------) ![](img/474f0e53e92347d888f1255e2ef23cfa.png)

照片来源：[EJ Strat](https://unsplash.com/@xoforoct?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)
