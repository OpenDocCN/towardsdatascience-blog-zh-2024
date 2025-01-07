# AutoML与AutoGluon：仅需四行代码即可完成ML工作流

> 原文：[https://towardsdatascience.com/automl-with-autogluon-transform-your-ml-workflow-with-just-four-lines-of-code-1d4b593be129?source=collection_archive---------0-----------------------#2024-07-03](https://towardsdatascience.com/automl-with-autogluon-transform-your-ml-workflow-with-just-four-lines-of-code-1d4b593be129?source=collection_archive---------0-----------------------#2024-07-03)

## AutoGluon如何主导Kaggle竞赛，以及你如何能够超越它。这个算法通过仅四行代码击败99%的数据科学家。

[](https://medium.com/@cristianleo120?source=post_page---byline--1d4b593be129--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--1d4b593be129--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1d4b593be129--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1d4b593be129--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--1d4b593be129--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1d4b593be129--------------------------------) ·阅读时间19分钟·2024年7月3日

--

![](../Images/404314451922e8f34abe0b2c42dd920a.png)

由DALL-E生成的图像

> 在两个受欢迎的Kaggle竞赛中，AutoGluon在仅对原始数据进行4小时训练后，击败了99%的参与数据科学家（AutoGluon团队。“AutoGluon：适用于文本、图像和表格数据的AutoML。”2020年）

这段话摘自[AutoGluon研究论文](https://arxiv.org/abs/2003.06505)，完美地概括了我们今天要探讨的内容：一个能够以最少的编码交付出色表现的机器学习框架。你只需四行代码即可设置完整的ML流水线，而这个任务通常可能需要数小时才能完成。没错，仅需四行代码！自己看看：

```py
from autogluon.tabular import TabularDataset, TabularPredictor

train_data = TabularDataset('train.csv')
predictor = TabularPredictor(label='Target').fit(train_data, presets='best_quality')
predictions = predictor.predict(train_data)
```

这四行代码通过自动识别每一列的数据类型来处理数据预处理，通过寻找有用的列组合来进行特征工程，并通过集成模型训练来识别给定数据集中的最佳表现模型……
