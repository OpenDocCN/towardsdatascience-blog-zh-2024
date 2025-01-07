# 如何在 SQL 中训练决策树分类器…

> 原文：[https://towardsdatascience.com/how-to-train-a-decision-tree-classifier-in-sql-e29f37835f18?source=collection_archive---------2-----------------------#2024-04-11](https://towardsdatascience.com/how-to-train-a-decision-tree-classifier-in-sql-e29f37835f18?source=collection_archive---------2-----------------------#2024-04-11)

## SQL 现在可以替代 Python 完成大部分监督式机器学习任务。你是否应该做出这个切换？

[](https://medium.com/@radecicdario?source=post_page---byline--e29f37835f18--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--e29f37835f18--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e29f37835f18--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e29f37835f18--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--e29f37835f18--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e29f37835f18--------------------------------) ·8分钟阅读·2024年4月11日

--

![](../Images/bce1aa30e358ab9ddfa19caa8fd0452b.png)

由[Resource Database](https://unsplash.com/@resourcedatabase?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，照片来源于[Unsplash](https://unsplash.com/photos/a-black-and-white-photo-of-a-circular-object-FNuryNf-8-g?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

说到机器学习，我是一个热衷于在数据原地进行处理的粉丝。90%以上的情况，这将是一个**关系型数据库**，假设我们谈论的是监督式机器学习。

Python 非常强大，但每次训练模型时都需要拉取数十 GB 的数据，这对于训练频繁的模型来说是一个巨大的**瓶颈**。消除数据迁移非常有意义。SQL 是你的好帮手。

对于本文，我将使用一个始终免费的 Oracle Database 21c，部署在[Oracle Cloud](https://www.oracle.com/cloud/)上。我不确定是否能将逻辑迁移到其他数据库供应商，但 Oracle 工作得非常顺畅，而且你部署的数据库永远不会收费。

# 数据集加载与预处理

我会把 Python 与 Oracle 在大数据集上的机器学习对比留到以后再说。今天，我们回归基础。

今天我将使用以下数据集：

+   **Fisher, R.A. (1936)**. 在分类学问题中使用多重测量。加利福尼亚大学欧文分校信息与计算机科学学院。检索自…
