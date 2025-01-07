# 5 种你必须尝试的 PCA 可视化方法，用于下一个数据科学项目

> 原文：[https://towardsdatascience.com/5-pca-visualizations-you-must-try-on-your-next-data-science-project-148ec3d31e4d?source=collection_archive---------0-----------------------#2024-08-02](https://towardsdatascience.com/5-pca-visualizations-you-must-try-on-your-next-data-science-project-148ec3d31e4d?source=collection_archive---------0-----------------------#2024-08-02)

## 哪些特征具有最大的权重？原始特征如何贡献于主成分？这五种可视化类型能给出答案。

[](https://medium.com/@radecicdario?source=post_page---byline--148ec3d31e4d--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--148ec3d31e4d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--148ec3d31e4d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--148ec3d31e4d--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--148ec3d31e4d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--148ec3d31e4d--------------------------------) ·阅读时长 8 分钟·2024年8月2日

--

![](../Images/c2c37457c29a6813661d876c8bd50202.png)

图片来源：[Andrew Neel](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

主成分分析（PCA）能告诉你很多关于数据的信息。简而言之，它是一种降维技术，用于将高维数据集转换到一个可以可视化的空间。

**但我猜你已经知道这一点了。** 如果不知道，可以查看我的[从零开始指南](/principal-component-analysis-pca-from-scratch-in-python-7f3e2a540c51)。

今天，我们只关注视觉效果。到文章结束时，你将学会如何创建和解读：

1.  解释方差图

1.  累计解释方差图

1.  2D/3D 组件散点图

1.  属性双变量图

1.  加载得分图

# 开始 — PCA 可视化的先决条件

我很想立刻深入可视化的内容，但你需要数据才能跟上。 本节将介绍数据加载、预处理、PCA 拟合和一般的 Matplotlib 样式调整。

## 数据集信息
