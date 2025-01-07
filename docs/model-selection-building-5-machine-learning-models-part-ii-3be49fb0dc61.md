# 构建5个机器学习模型 第二部分

> 原文：[https://towardsdatascience.com/model-selection-building-5-machine-learning-models-part-ii-3be49fb0dc61?source=collection_archive---------8-----------------------#2024-10-10](https://towardsdatascience.com/model-selection-building-5-machine-learning-models-part-ii-3be49fb0dc61?source=collection_archive---------8-----------------------#2024-10-10)

## 构建、比较和优化机器学习模型。

[](https://medium.com/@panData?source=post_page---byline--3be49fb0dc61--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--3be49fb0dc61--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3be49fb0dc61--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3be49fb0dc61--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--3be49fb0dc61--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3be49fb0dc61--------------------------------) ·阅读时间38分钟·2024年10月10日

--

![](../Images/cf992d4cb8a02e80ad932bb52723714b.png)

模型选择

现在我们进入项目的第二部分——**机器学习模型选择与多变量分析中的匿名化数据**。

这第二部分是**魅力**所在——**预测建模**，**机器学习**。每个人都迫不及待地想直接进入构建**机器学习模型**的阶段。我理解这一点，我也感到同样的兴奋，因为我热爱这个阶段。

但在我们深入之前，我们必须经过**数据处理**——这正是我们在上一篇教程中涵盖的内容。

[](/model-selection-a-guide-to-class-balancing-part-i-14b17003186f?source=post_page-----3be49fb0dc61--------------------------------) [## 模型选择：类平衡指南（第一部分）

### 关于机器学习模型选择中的匿名数据类平衡的综合教程

towardsdatascience.com](/model-selection-a-guide-to-class-balancing-part-i-14b17003186f?source=post_page-----3be49fb0dc61--------------------------------)

# 安装和加载软件包

我们首先安装**XGBoost**包，这是参与**机器学习竞赛**的Kaggle平台用户的最爱之一。

```py
# This package does not come with Anaconda and needs to be installed
!pip install -q xgboost
```

这个软件包不包括**Anaconda**，因此你需要单独安装它。要安装它，我们使用命令……
