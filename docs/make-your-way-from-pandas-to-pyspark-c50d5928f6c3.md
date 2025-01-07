# 从 Pandas 到 PySpark

> 原文：[https://towardsdatascience.com/make-your-way-from-pandas-to-pyspark-c50d5928f6c3?source=collection_archive---------9-----------------------#2024-09-26](https://towardsdatascience.com/make-your-way-from-pandas-to-pyspark-c50d5928f6c3?source=collection_archive---------9-----------------------#2024-09-26)

## 学习一些基本的命令，开始从 Pandas 转向 PySpark

[](https://gustavorsantos.medium.com/?source=post_page---byline--c50d5928f6c3--------------------------------)[![Gustavo R Santos](../Images/a19a9f4525cdeb6e7a76cd05246aa622.png)](https://gustavorsantos.medium.com/?source=post_page---byline--c50d5928f6c3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c50d5928f6c3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c50d5928f6c3--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--c50d5928f6c3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c50d5928f6c3--------------------------------) ·阅读时长9分钟·2024年9月26日

--

![](../Images/ebaf1b15ab1ec28e2db93416cd0480b5.png)

图片来自 [Karsten Würth](https://unsplash.com/@karsten_wuerth?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/pathway-between-fence-and-grasses-HiE1bIIoRqQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

我是 LinkedIn 和其他地方一些数据科学社区的成员，我时常看到有些人会提到关于 PySpark 的问题。

面对现实吧：数据科学是一个极其广泛的领域，没人能了解所有的内容。因此，当我加入一个统计学课程或社区时，偶尔会看到有人问 *什么是 PySpark*、*如何在 PySpark 中计算一些统计量*，以及其他各种各样的问题。

通常，已经使用 Pandas 的人尤其对 Spark 感兴趣。我相信这背后有几个原因：

1.  Pandas 无疑是数据科学家非常熟悉和常用的工具，但它也肯定不是最快的库。随着数据量的增大，速度会按比例减慢。

1.  对于那些已经掌握 Pandas 的人来说，学习一种新的数据处理方法是自然而然的选择。随着数据变得更加容易获取且数量增加，掌握 Spark 是处理大数据的一个很好的选择。

1.  Databricks 非常有名，而 PySpark 可能是平台上最常用的语言之一，与 SQL 并列。
