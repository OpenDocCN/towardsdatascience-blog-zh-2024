# 关于在 DAX 中根据另一个值计算销售额

> 原文：[https://towardsdatascience.com/about-calculating-sales-depending-on-another-value-in-dax-914deb450d23?source=collection_archive---------9-----------------------#2024-01-18](https://towardsdatascience.com/about-calculating-sales-depending-on-another-value-in-dax-914deb450d23?source=collection_archive---------9-----------------------#2024-01-18)

## *我们通常的度量值会聚合来自一个事实表中列的值。那么，计算一个依赖于另一个事实表中数据的结果需要什么？让我们来看一个可能的解决方案。*

[](https://medium.com/@salvatorecagliari?source=post_page---byline--914deb450d23--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--914deb450d23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--914deb450d23--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--914deb450d23--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--914deb450d23--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--914deb450d23--------------------------------) ·8分钟阅读·2024年1月18日

--

![](../Images/45ea30c39f5de4fab1569c22c2102c56.png)

图片由 [Isaac Smith](https://unsplash.com/@isaacmsmith?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

在 DAX 中根据特定值计算结果需要什么？

简单：一个过滤器。

但如果过滤器必须是一个表呢？

幸运的是，我已经知道如何在 DAX 度量值中使用表格：

[](/on-using-intermediary-results-in-dax-measures-9971efa72ae?source=post_page-----914deb450d23--------------------------------) [## 在 DAX 度量值中使用中间结果

### 我们在 DAX 中经常使用表变量。如果我们必须计算中间结果并在 DAX 中重复使用它们，怎么办呢……

towardsdatascience.com](/on-using-intermediary-results-in-dax-measures-9971efa72ae?source=post_page-----914deb450d23--------------------------------)

现在，我的客户给了我一个挑战，让我把我的知识付诸实践：

看看以下报告：

![](../Images/16bf481118051d54e12d94515a99c9d2.png)

图1 — 在线销售与零售销售及销售的产品（图由作者提供）

如你所见，在线销售和零售销售的产品数量有所不同。

我的客户问了以下问题：

+   那么，零售商店销售的产品的在线销售额是多少？

+   那么，哪些产品仅在线销售？

# 尝试在 Power BI 中使用过滤器解决这个问题

我能通过像这样在左侧表格上应用过滤器来回答第一个问题吗？

![](../Images/2049a3eec6ee685b06bdaa88035954df.png)

图2 — 在Power BI中应用基于零售销售度量值的过滤器（作者提供的图）

不幸的是，这并不起作用。

原因是这两个表之间没有任何关系，并且维度表的过滤方向仅在单向工作。

是的，我可以将其更改为双向过滤。但这样做可能会引入问题，因为在某些情况下它不会生效。

另一种方法是添加多个度量值，并为可视化添加一个过滤器：

![](../Images/acf08f12a75a2596a631b7e0a7324d79.png)

图3 — 在Power BI中应用多个度量值的过滤器（作者提供的图）

这样可以得到所需的结果。

我甚至可以将过滤器更改为“Sum Retail Sales *is blank*”，以获取仅在网上销售的产品及其销售数量。

但生成的可视化包含了我不想看到的“Sum Retail Sales”度量值。而且，当我想获取仅在线销售的产品数量时，这个度量值会显示空列，这也不是我想看到的结果。

所以，我必须创建一个明确的DAX度量值来解决上述问题。

# 从源数据库获取结果

但首先，我必须获取一个参考结果。

我可以使用这个参考结果在构建解决方案时验证结果。

我编写以下SQL查询来获取此参考结果：

```py
WITH [RetailProductsByMonth]
AS
  (SELECT DISTINCT [ProductKey]
            ,[D].[MonthKey]
    FROM [dbo].[FactSales] AS [F]
      INNER JOIN [Date] AS [D]
        ON [D].[Date] = [F].[DateKey]
  )
SELECT [D].[EnglishYearMonthShortName]
      ,[D].[MonthKey]
      ,COUNT(DISTINCT [F].[ProductKey]) AS [ProductCount]
  FROM [dbo].[FactOnlineSales] AS [F]
    INNER JOIN [Date] AS [D]
      ON [D].[Date] = [F].[DateKey]
  INNER JOIN [RetailProductsByMonth] AS [PbM]
      ON [PbM].[ProductKey] = [F].[ProductKey]
      AND [D].[MonthKey] = [PbM].[MonthKey]
  GROUP BY [D].[EnglishYearMonthShortName], [D].[MonthKey]
  ORDER BY [D].[MonthKey] DESC;
```

这是结果：

![](../Images/d016e912a8539eba2492eb3391832c6a.png)

图4 — 获取仅在线和门店销售的产品的SQL查询结果（作者提供的图）

要获取仅在线销售的产品数量，我使用以下SQL查询：

```py
WITH [RetailProductsByMonth]
AS
  (SELECT DISTINCT [ProductKey]
            ,[D].[MonthKey]
      FROM [dbo].[FactSales] AS [F]
        INNER JOIN [Date] AS [D]
          ON [D].[Date] = [F].[DateKey]
  )
SELECT [D].[EnglishYearMonthShortName]
      ,[D].[MonthKey]
      ,COUNT(DISTINCT [F].[ProductKey]) AS [ProductCount]
  FROM [dbo].[FactOnlineSales] AS [F]
    INNER JOIN [Date] AS [D]
      ON [D].[Date] = [F].[DateKey]
    LEFT OUTER JOIN [RetailProductsByMonth] AS [PbM]
      ON [PbM].[ProductKey] = [F].[ProductKey]
      AND [D].[MonthKey] = [PbM].[MonthKey]
  WHERE [PbM].[ProductKey] IS NULL
  GROUP BY [D].[EnglishYearMonthShortName], [D].[MonthKey]
  ORDER BY [D].[MonthKey] DESC;
```

这个查询的结果如下：

![](../Images/208abc208c96c901f1458d80815b0b7c.png)

图5 — 获取仅在线销售产品的SQL查询结果（作者提供的图）

在这两种情况下，首先，我会获取每个月零售销售中所有销售产品的列表。

然后，我将结果与在线销售表连接，并统计每月销售的产品数量。

在第二个查询中，我排除了所有零售销售数据中的产品。

在这两种情况下，我得到了所需的参考结果，并且可以继续在Power BI中编写DAX代码。

# 查询数据模型

我没有尝试在Power BI中构建度量值，而是使用[DAX Studio](https://www.sqlbi.com/tools/dax-studio/)编写DAX查询，逐步构建解决方案。

首先，我想获取所有在零售店销售的产品。

由于我的SQL查询按月分组结果，我将第一个查询限制为仅一月。这也简化了所需的代码：

```py
DEFINE
  VAR MonthSelection = 202311

EVALUATE
  CALCULATETABLE(
          SUMMARIZECOLUMNS('Retail Sales'[ProductKey] )
          ,'Date'[MonthKey] = MonthSelection
          )
```

这是结果：

![](../Images/3fa909d205cd46aa1e52821480a29fec.png)

图6 — DAX查询结果，列出在零售店销售的产品（作者提供的图）

所以，我得到了2023年11月销售的2504个产品。

下一步是将此结果作为过滤器注入到查询中，以统计仅在线销售的产品数量：

```py
DEFINE
  VAR MonthSelection = 202311

  VAR RetailProductList =
        CALCULATETABLE(
                SUMMARIZECOLUMNS('Retail Sales'[ProductKey] )
                ,'Date'[MonthKey] = MonthSelection
                )

EVALUATE
{
  COUNTROWS(
        CALCULATETABLE(
                SUMMARIZECOLUMNS('Online Sales'[ProductKey] )
                ,'Online Sales'[ProductKey] IN RetailProductList ,'Date'[MonthKey] = MonthSelection
                )
        )
 }
```

现在我有2498个产品：

![](../Images/2bdfafdc20ad8973b801114873ec65a9.png)

图 7 — 计算在线销售和零售商店销售产品的 DAX 查询结果（图源：作者）

如你所见，我将零售商店中销售的产品列表存储在变量 RetailProductList 中。

然后，我使用 [CALCULATETABLE()](https://dax.guide/calculatetable/) 来过滤在线销售表中的 ProductKey，使用这个变量。

为了将结果限制为仅一个月份，我必须添加第 17 行。

为了仅获得产品的计数，我使用 [COUNTROWS()](https://dax.guide/countrows/) 并将其包含在大括号中以生成输出表。

我必须添加大括号，因为 COUNTROWS() 返回一个数字，而 [EVALUATE](https://dax.guide/st/evaluate/) 期望一个表表达式。大括号将 COUNTROWS() 的结果转换为一个包含一列一行的表。

当我将 2498 的计数与上述第一个 SQL 查询的结果进行比较时，对于 2023 年 11 月，我得到了相同的结果。

因此，逻辑按预期工作。

# 创建度量值

现在，我可以将查询放入度量值中。

由于度量值必须在我希望获取月份列表并计算每个月的结果时工作，我必须去掉硬编码的月份，并针对结果中的每一行评估该月份：

```py
DEFINE
 MEASURE 'All Measures'[Online Sold Product Count with Retail Sales] =
    VAR MonthSelection = MAX('Date'[MonthKey])

    VAR RetailProductList =
        CALCULATETABLE(
              SUMMARIZECOLUMNS('Retail Sales'[ProductKey] )
                                ,'Date'[MonthKey] = MonthSelection
                         )

RETURN
  COUNTROWS(
      CALCULATETABLE(
              SUMMARIZECOLUMNS('Online Sales'[ProductKey] )
                  ,KEEPFILTERS('Online Sales'[ProductKey] IN RetailProductList)
                  ,'Date'[MonthKey] = MonthSelection
                  )
              )
EVALUATE
  SUMMARIZECOLUMNS('Date'[Year Month Short Name]
            ,'Date'[MonthKey]
            ,"Online Sold Product Count with Retail Sales", [Online Sold Product Count with Retail Sales]
            )
 ORDER BY 'Date'[MonthKey] DESC
```

度量值是 DEFINE MEASURE 和 EVALUATE 之间的所有内容。

这是结果，与上述 SQL 查询的结果相同：

![](../Images/2b15846d21995f24f8d9352f27ec6894.png)

图 8 — 使用查询派生的度量值的 DAX 查询结果（图源：作者）

为了获得仅在线销售的产品，我必须修改度量值中的一行，排除零售商店中的销售产品：

```py
,KEEPFILTERS(NOT ('Online Sales'[ProductKey] IN RetailProductList) )
```

结果与上述第二个 SQL 查询的结果相同：

![](../Images/d16e149debb8aeb4005423d4bab8b77b.png)

图 9 — 使用仅在线销售的度量值的 DAX 查询结果（图源：作者）

# 结论

这个任务与我在关于在 DAX 中使用中间结果的文章中描述的逻辑类似（你可以在下面的参考部分找到该文章的链接）。

但这次，在使用表变量时，我不需要额外的 [FILTER()](https://dax.guide/filter/)。

我不确定为什么在写那篇文章时我需要这样做，但我将进一步研究。

如你所见，这次我使用 DAX 查询一步一步地开发了解决方案。

我觉得这样做很有用，因为我使用了一个中间表，可以在开发过程中进行检查。

对于这种场景，使用查询比直接在 Power BI Desktop 中构建这样的解决方案要更容易。

此外，对于那些来自关系型数据库工作背景并开始使用 DAX 的人来说，这一点很有意思：我需要显式地在 SQL 查询中添加月份，以便按月份对结果进行分组。而在 DAX 中，添加月份到查询时分组会自动完成，我不需要做额外的操作。

![](../Images/ade78146acb63e47f1347db6543c4dc8.png)

照片由 [Firmbee.com](https://unsplash.com/@firmbee?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 参考资料

几个月前，我写了一篇关于在 DAX 度量中使用中介表的文章。你可以在这里找到它：

[](/on-using-intermediary-results-in-dax-measures-9971efa72ae?source=post_page-----914deb450d23--------------------------------) [## 在 DAX 度量中使用中介结果

### 我们在 DAX 中经常使用表变量。如果我们必须计算中介结果并在 DAX 中重用它们怎么办……

towardsdatascience.com](/on-using-intermediary-results-in-dax-measures-9971efa72ae?source=post_page-----914deb450d23--------------------------------)

我使用 Contoso 示例数据集，就像在我之前的文章中一样。你可以从微软 [这里](https://www.microsoft.com/en-us/download/details.aspx?id=18279) 免费下载 ContosoRetailDW 数据集。

我将客户的请求翻译为 ContosoRetailDW 数据集中的数据。

Contoso 数据可以在 MIT 许可下自由使用，如 [这里](https://github.com/microsoft/Power-BI-Embedded-Contoso-Sales-Demo) 所述。

我扩大了数据集，以便让 DAX 引擎处理更多的工作。

Online Sales 表包含 7100 万行（而不是 1260 万行），而 Retail Sales 表包含 1850 万行（而不是 340 万行）。

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----914deb450d23--------------------------------) [## 每当 Salvatore Cagliari 发布新文章时，收到电子邮件通知。

### 每当 Salvatore Cagliari 发布新文章时，收到电子邮件通知。通过注册，你将创建一个 Medium 账户，如果你还没有的话……

medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----914deb450d23--------------------------------)

我让我的文章对每个人都可访问，即使 Medium 有付费墙。这让我能从每个读者那里赚取一点收入，但我关闭了付费墙，这样你就可以免费阅读我的文章。

你可以通过以下方式支持我的工作，我在空闲时间做这些工作：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或者扫描这个二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都将不胜感激，并帮助我找到更多时间为你创作更多内容。

非常感谢。
