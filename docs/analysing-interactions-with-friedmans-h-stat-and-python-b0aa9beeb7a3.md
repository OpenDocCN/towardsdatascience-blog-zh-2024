# 使用Friedman的H-stat和Python分析特征交互

> 原文：[https://towardsdatascience.com/analysing-interactions-with-friedmans-h-stat-and-python-b0aa9beeb7a3?source=collection_archive---------8-----------------------#2024-06-21](https://towardsdatascience.com/analysing-interactions-with-friedmans-h-stat-and-python-b0aa9beeb7a3?source=collection_archive---------8-----------------------#2024-06-21)

## 使用artemis包应用H-stat并解释成对、总体和未标准化的度量

[](https://conorosullyds.medium.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------)[![Conor O'Sullivan](../Images/2dc50a24edb12e843651d01ed48a3c3f.png)](https://conorosullyds.medium.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0aa9beeb7a3--------------------------------) ·8分钟阅读·2024年6月21日

--

![](../Images/b9d8a2e4f92ffcc728ce9f278bbe790e.png)

（来源：作者）

Friedman的h统计量（h-stat）为复杂的机器学习模型提供了一个强大的观察窗口。具体来说，它帮助我们了解模型是否通过特征之间的交互来进行预测。我们将看到，这种[XAI](/what-is-interpretable-machine-learning-2d217b62185a)方法可以告诉我们某个特征是否与其他任何特征或某个特定特征发生交互。为此，我们将：

+   使用[artemis](https://github.com/pyartemis/artemis) Python包应用h-stat。

+   解释输出结果，包括交互热图和条形图。

你可以在[GitHub](https://github.com/a-data-odyssey/XAI-tutorial)上找到完整的项目。你也可以观看关于该主题的视频。如果你想了解更多，可以查看我的课程——[使用Python的XAI](https://adataodyssey.com/courses/xai-with-python/)。如果你订阅我的[新闻通讯](https://mailchi.mp/aa82a5ce1dc0/signup)，你将获得**免费**访问权限。

# 什么是H-stat？

交互是指特征与目标变量之间的关系依赖于另一个特征的值……
