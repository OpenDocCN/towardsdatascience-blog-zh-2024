# 探索目标编码中的层级融合

> 原文：[https://towardsdatascience.com/exploring-hierarchical-blending-in-target-encoding-fea4c59b305b?source=collection_archive---------9-----------------------#2024-04-18](https://towardsdatascience.com/exploring-hierarchical-blending-in-target-encoding-fea4c59b305b?source=collection_archive---------9-----------------------#2024-04-18)

## 何时代码层级结构能改善高基数类别特征的目标编码？

[](https://medium.com/@vla6?source=post_page---byline--fea4c59b305b--------------------------------)[![Valerie Carey](../Images/9ef394fe5a6a5439521c1905e0195751.png)](https://medium.com/@vla6?source=post_page---byline--fea4c59b305b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fea4c59b305b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fea4c59b305b--------------------------------) [Valerie Carey](https://medium.com/@vla6?source=post_page---byline--fea4c59b305b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fea4c59b305b--------------------------------) ·12分钟阅读·2024年4月18日

--

![](../Images/b3b5bc1f98427d53ba1b2d808bf7bb9f.png)

图片由[Jessica Alves](https://unsplash.com/@fifteensunflowers?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你住在哪个社区？你被开了什么药？你为什么取消了流媒体订阅？如今，针对这些问题都有相应的代码，存储在你与之互动的政府机构、企业等的数据库中。如果你从事数据工作，你可能会遇到很多此类代码。当这些代码可以取多个值时，它们被称为“**高基数类别特征**”。

一些高基数类别特征具有**层级**结构。图1展示了这种结构，即北美行业分类系统（NAICS），这是美国政府用来对企业进行分类的系统[1]。

![](../Images/d69ae827720b57e7b85f2c9a032a6382.png)

**图1：** [北美行业分类系统](https://www.census.gov/naics/)（NAICS）代码的层级结构示意图[1]，该系统根据活动领域对企业进行分类。底部是许多具体的代码（“金字塔”的底部），这些代码被分组到更一般的类别中（上层）。这里展示了一个贝果店的例子。图片由作者提供。

许多代码集可以表示为层级结构。例如，美国地理区域可以被划分为多个小区域，每个区域有许多代码值（邮政编码），或者划分为非常大的区域，代码值较少（[美国人口普查区域](https://www2.census.gov/geo/pdfs/maps-data/maps/reference/us_regdiv.pdf)，例如“西部”）。或者，美国医学会定义了约475个[提供者专业化领域](https://www.nucc.org/images/stories/PDF/taxonomy_24_0.pdf)，这些领域被汇总为分类、分组和章节。
