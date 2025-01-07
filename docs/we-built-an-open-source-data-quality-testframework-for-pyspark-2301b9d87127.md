# 我们为 PySpark 构建了一个开源数据质量测试框架

> 原文：[`towardsdatascience.com/we-built-an-open-source-data-quality-testframework-for-pyspark-2301b9d87127?source=collection_archive---------6-----------------------#2024-08-16`](https://towardsdatascience.com/we-built-an-open-source-data-quality-testframework-for-pyspark-2301b9d87127?source=collection_archive---------6-----------------------#2024-08-16)

## 轻松衡量和报告您的数据质量

[](https://medium.com/@tomergabay?source=post_page---byline--2301b9d87127--------------------------------)![Tomer Gabay](https://medium.com/@tomergabay?source=post_page---byline--2301b9d87127--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2301b9d87127--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2301b9d87127--------------------------------) [Tomer Gabay](https://medium.com/@tomergabay?source=post_page---byline--2301b9d87127--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2301b9d87127--------------------------------) ·阅读时间：7 分钟·2024 年 8 月 16 日

--

![](img/ba1fda4f902d10e14c6c3ccb5cf20841.png)

[图片由作者提供，使用 Dall-E 生成]

每个数据科学家都知道经典的说法“*垃圾进，垃圾出*”。因此，衡量数据质量是至关重要的。

在*Woonstad Rotterdam*，一家荷兰的社会住房协会，我们在 Databricks 中使用 PySpark 进行 ETL。来自我们外部软件供应商的数据通过 API 加载到我们的数据湖中。然而，并不是每个软件供应商都在进行数据质量测试。社会住房领域中数据错误的后果可能非常严重，范围从租户无法申请补贴，到租金被定为根据[《负担得起的租金法案》](https://www.volkshuisvestingnederland.nl/onderwerpen/wet-betaalbare-huur)规定为非法的价格。因此，我们为 PySpark DataFrame 构建了[a data quality testframework](https://github.com/woonstadrotterdam/pyspark-testframework)，以便向供应商和数据用户报告数据质量。

> 社会住房领域中数据错误的后果可能非常严重，范围从租户无法申请补贴，到租金被定为根据《负担得起的租金法案》规定为非法的价格。
