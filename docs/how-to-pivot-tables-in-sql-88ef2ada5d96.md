# 如何在SQL中创建Pivot表

> 原文：[https://towardsdatascience.com/how-to-pivot-tables-in-sql-88ef2ada5d96?source=collection_archive---------2-----------------------#2024-06-12](https://towardsdatascience.com/how-to-pivot-tables-in-sql-88ef2ada5d96?source=collection_archive---------2-----------------------#2024-06-12)

## 数据科学、SQL、ETL

## SQL中创建Pivot表的全面指南，以提升数据分析能力

[](https://medium.com/@yunglinchang?source=post_page---byline--88ef2ada5d96--------------------------------)[![Jack Chang](../Images/6783c539c3523b2fd58ada3770c2c99a.png)](https://medium.com/@yunglinchang?source=post_page---byline--88ef2ada5d96--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--88ef2ada5d96--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--88ef2ada5d96--------------------------------) [Jack Chang](https://medium.com/@yunglinchang?source=post_page---byline--88ef2ada5d96--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--88ef2ada5d96--------------------------------) ·阅读时间11分钟·2024年6月12日

--

![](../Images/3c4d61dc0a7e693c31ccfd1ad8255352.png)

图片来源：[Mika Baumeister](https://unsplash.com/@mbaumi?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 前言

[结构化查询语言（SQL）](https://en.wikipedia.org/wiki/SQL)是数据专业人员（如数据科学家和数据分析师）的重要工具，它使他们能够高效、有效地检索、处理和分析大数据集。它是行业中广泛使用的工具，因此是一项重要的技能。**在本文中，我将分享如何在SQL中创建Pivot表。** 本文是我上一篇文章“[Pandas！！！我在第一次现场技术面试后的收获](https://medium.com/towards-data-science/pandas-what-ive-learned-after-my-1st-on-site-technical-interview-4fb94dbc1b45)”的延续，文章中我分享了我对Pandas的学习心得。

> 你知道SQL可以用于数据分析吗？

在SQL中，Pivot表是一种将数据从行转换为列的技术。

Joan Casteel的[Oracle 12c: SQL](https://a.co/d/2Uwgbqd)书中提到，“**Pivot表是多维数据的呈现。**” 使用Pivot表，用户可以查看不同数据维度的不同聚合。它是数据分析中的一项强大工具，能够帮助用户以更直观、易于阅读的格式汇总、总结和呈现数据。

例如，一家冰激凌店的老板可能想分析上周哪种口味的冰激凌销量最好。在这种情况下，Pivot表将非常有用，数据有两个维度——冰激凌口味和星期几。收入可以作为聚合数据进行分析。

冰淇淋店老板可以轻松使用数据透视表比较不同冰淇淋口味和一周中各天的销售情况。数据透视表将转化这些数据，使得发现模式和趋势变得更加容易。有了这些信息，老板可以做出数据驱动的决策，例如增加最受欢迎冰淇淋口味的供应量，或根据需求调整价格。

总体而言，数据透视表是一个出色的数据分析工具，允许用户以更直观和有意义的方式汇总和展示多维数据。它们广泛应用于金融、零售和医疗保健等行业，在这些行业中，通常需要分析大量复杂数据。

![](../Images/186a5b835a755ae284ea4f11a994e567.png)

图片来源：[Lama Roscu](https://unsplash.com/@lamaroscu?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概览

**本文将基于Oracle中的分析函数，通常是“PIVOT”函数。** 组织的内容旨在全面展示在不同情境下如何使用SQL中的数据透视表。我们不仅会介绍创建数据透视表的最简单方法，还会讲解如何利用PIVOT函数以最简便和最常见的方式完成任务。最后，我还会讨论PIVOT函数的一些局限性。

## 仅供参考：

+   我将使用Oracle 11g，但这些函数在更新版的Oracle 12c及以上版本中是相同的。

+   演示数据集来自[Microsoft](https://github.com/microsoft)的[Northwind数据集](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs)。这是一个虚构的特色食品进出口公司Northwind Traders的销售数据。该数据库是[免费使用](https://github.com/microsoft/sql-server-samples/blob/master/license.txt)的，并广泛分发用于学习和展示目的。请务必在开始前设置好数据库环境！我还附上了Northwind模式：

```py
**REGION** (RegionID, RDescription)**TERRITORIES** ( TerritoryID, TDescription, RegionID@)**CATEGORIES** (CategoryID, CategoryName, Description)**SUPPLIERS** (SupplierID, CompanyName, ContactName, ContactTitle, Address, City, Region, PostalCode, Country, Phone)**CUSTOMERS** (CustomerID, CompanyName, ContactName, ContactTitle, Address, City, Region, PostalCode, Country, Phone)**SHIPPERS** (ShipperID, CompanyName, Phone)**PRODUCTS** (ProductID, ProductName, SupplierID@, CategoryID@, QuantityPerUnit, UnitPrice, UnitsInStock, UnitsOnOrder, ReorderLevel, Discontinued)**EMPLOYEES** (EmployeeID, LastName, FirstName, Title, BirthDate, HireDate, Address, City, RegionID@, PostalCode, Country, HomePhone, Extension, ReportsTo@)**EMPLOYEETERRITORIES** (EmployeeID@, TerritoryID@)**ORDERS** (OrderID, CustomerID@, EmployeeID@, TerritoryID@, OrderDate, RequiredDate, ShippedDate, ShipVia@, Freight, ShipName, ShipAddress, ShipCity, ShipRegion, ShipPostalCode, ShipCountry)**ORDERDETAILS** (OrderID@, ProductID@, UnitPrice, Quantity, Discount)
```

+   如果你不熟悉SQL*Plus，建议在开始之前查看Oracle的[SQL*Plus快速入门](https://docs.oracle.com/cd/B19306_01/server.102/b14357/qstart.htm)。

不再赘述，让我们开始吧！

# 使用“DECODE”的数据透视表

![](../Images/74a9451ab9ff3d7249015797d4488746.png)

图片来源：[Jean-Philippe Delberghe](https://unsplash.com/@jipy32?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最原始的数据透视表方法是利用函数：[**DECODE()**](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/DECODE.html#GUID-39341D91-3442-4730-BD34-D3CF5D4701CE)。DECODE()函数类似于if else语句。它将输入与每个值进行比较，并产生一个输出。

> DECODE(input, value1, return1, value2, return2, …, default)

+   **input/value**：“input”将与所有“values”进行比较。

+   **return**：如果输入值等于某个值，则“return”是输出结果。

+   **默认（可选）**：如果输入 != 所有值，则输出为“默认”。

当我们知道DECODE()如何工作时，就该制作我们的第一个透视表了。

## 第一版：不带总计列和总计行的透视表

![](../Images/01c28e4ecd251f365a719f825518832c.png)

不带总计列和总计行的透视表，来源：我

使用DECODE()，我们可以为冰淇淋店老板绘制一个透视表的伪代码。当“星期几”与每个工作日匹配时，DECODE()返回当天的收入；如果不匹配，则返回0。

```py
SELECT ice cream flavor, 
SUM(DECODE(day of the week, 'Monday', revenue, 0)) AS MONDAY, SUM(DECODE(day of the week, 'Tuesday', revenue, 0)) AS TUESDAY,
SUM(DECODE(day of the week, 'Wednesday', revenue, 0)) AS WEDNESDAY,
SUM(DECODE(day of the week, 'Thursday', revenue, 0)) AS THURSDAY,
SUM(DECODE(day of the week, 'Friday', revenue, 0)) AS FRIDAY,
SUM(DECODE(day of the week, 'Saturday', revenue, 0)) AS SATURDAY,
SUM(DECODE(day of the week, 'Sunday', revenue, 0)) AS SUNDAY
FROM ice cream shop dataset
WHERE date between last Monday and last Sunday;
```

## 第二版：带有总计列和总计行的透视表

![](../Images/3a58fe26e6b3c13ffbe6a4ba3bddd4a5.png)

带有总计列和总计行的透视表，来源：我

干得好！现在冰淇淋店老板想了解更多关于上周销售情况的信息。你可以通过添加总计列和总计行来升级你的透视表。

这可以通过在GROUP BY语句中使用[GROUPING SETS 表达式](https://docs.oracle.com/cd/E93962_01/bigData.Doc/eql_onPrem/src/reql_aggregation_grouping_sets.html)来实现。GROUPING SETS表达式定义了多个GROUP BY聚合的标准。

> GROUPING SETS (属性1，…，())

+   **属性**：单个元素或用于GROUP BY的元素列表

+   **()**：一个空的组，将成为透视表的总计行

```py
SELECT NVL(ice cream flavor, 'TOTAL') "ICE CREAM FLAVOR", 
SUM(DECODE(day of the week, 'Monday', revenue, 0)) AS MONDAY, SUM(DECODE(day of the week, 'Tuesday', revenue, 0)) AS TUESDAY,
SUM(DECODE(day of the week, 'Wednesday', revenue, 0)) AS WEDNESDAY,
SUM(DECODE(day of the week, 'Thursday', revenue, 0)) AS THURSDAY,
SUM(DECODE(day of the week, 'Friday', revenue, 0)) AS FRIDAY,
SUM(DECODE(day of the week, 'Saturday', revenue, 0)) AS SATURDAY,
SUM(DECODE(day of the week, 'Sunday', revenue, 0)) AS SUNDAY, 
SUM(revenue) AS TOTAL
FROM ice cream shop dataset
WHERE date between last Monday and last Sunday
GROUP BY GROUPING SETS (ice cream flavor, ());
```

注意：NVL()将由()创建的空值行替换为“TOTAL”。如果你不熟悉[NVL()](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/NVL.html#GUID-3AB61E54-9201-4D6A-B48A-79F4C4A034B2)，它只是一个用来替换空值的函数。

计算总计列的另一种方法是将从周一到周日的所有收入加起来：

```py
SUM(DECODE(day of the week, 'Monday', revenue, 0)) 
+ SUM(DECODE(day of the week, 'Tuesday', revenue, 0)) 
+ SUM(DECODE(day of the week, 'Wednesday', revenue, 0)) 
+ SUM(DECODE(day of the week, 'Thursday', revenue, 0)) 
+ SUM(DECODE(day of the week, 'Friday', revenue, 0)) 
+ SUM(DECODE(day of the week, 'Saturday', revenue, 0))
+ SUM(DECODE(day of the week, 'Sunday', revenue, 0)) AS TOTAL
```

## 第三版：带有总计列和总计行及其他总计的透视表

![](../Images/b51145e9666531e26b0b454773076572.png)

带有总计列和总计行及其他总计的透视表，来源：我

假设冰淇淋店老板想要在你提供的透视表中再加一列：每种口味冰淇淋的购买总数。没问题！你可以用相同的概念再添加一个“TOTAL”列！

```py
SELECT NVL(ice cream flavor, 'TOTAL') "ICE CREAM FLAVOR", 
SUM(DECODE(day of the week, 'Monday', revenue, 0)) AS MONDAY, SUM(DECODE(day of the week, 'Tuesday', revenue, 0)) AS TUESDAY,
SUM(DECODE(day of the week, 'Wednesday', revenue, 0)) AS WEDNESDAY,
SUM(DECODE(day of the week, 'Thursday', revenue, 0)) AS THURSDAY,
SUM(DECODE(day of the week, 'Friday', revenue, 0)) AS FRIDAY,
SUM(DECODE(day of the week, 'Saturday', revenue, 0)) AS SATURDAY,
SUM(DECODE(day of the week, 'Sunday', revenue, 0)) AS SUNDAY, 
SUM(revenue) AS TOTAL,
SUM(purchase ID) "OTHER TOTAL"
FROM ice cream shop dataset
WHERE date between last Monday and last Sunday
GROUP BY GROUPING SETS (ice cream flavor, ());
```

现在你已经知道如何使用DECODE()做透视表了，接下来让我们尝试三个关于Northwind数据集的练习吧！

Q1. 假设我们想找出每个原籍国的员工在各个区域的服务情况。

为了拆解这个问题，首先，我们可以查询REGION表中的所有不同区域，并检查员工来自哪些国家。

```py
SELECT DISTINCT REGIONID||' '||RDescription AS REGION
FROM REGION
ORDER BY 1;
```

![](../Images/3627116e1d54ed0917364c2a13a43fe5.png)

```py
SELECT DISTINCT Country
FROM EMPLOYEES
ORDER BY 1;
```

![](../Images/80132a8d40c683c0db3caebce861de5a.png)

我们将需要为这个问题制作一个2 * 4的透视表。

接下来，我们可以使用DECODE()来制作透视表。下面概述了一个示例答案和输出：

![](../Images/2961edeea876bc9ccf18d701f9e086b6.png)

```py
SELECT NVL(Country, 'TOTAL') AS COUNTRY, 
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '1 eastern', 1, 0)) "1 EASTERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '2 western', 1, 0)) "2 WESTERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '3 northern', 1, 0)) "3 NORTHERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '4 southern', 1, 0)) "4 SOUTHERN",
SUM(EmployeeID) AS TOTAL
FROM EMPLOYEES
JOIN REGION USING (REGIONID)
GROUP BY GROUPING SETS (Country, ());
```

![](../Images/1c753723386af8721f3b7d2ea9cb6ec6.png)

```py
--Q1
SELECT Country, 
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '1 eastern', 1, 0)) "1 EASTERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '2 western', 1, 0)) "2 WESTERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '3 northern', 1, 0)) "3 NORTHERN",
SUM(DECODE(LOWER(REGIONID||' '||RDescription), '4 southern', 1, 0)) "4 SOUTHERN",
SUM() AS TOTAL
FROM EMPLOYEES
JOIN REGION USING (REGIONID)
GROUP BY Country;
```

Q2. 对于2010年中的每个月，显示每个员工处理的订单收入。此外，四舍五入到最接近的美元，并显示总收入和订单总数。

```py
--Q2
COLUMN EMPLOYEE FORMAT A18
SELECT NVL(EmployeeID||' '||FirstName||' '||LastName, 'TOTAL') AS EMPLOYEE,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 1, (UnitPrice * Quantity - Discount), 0)), '$990') AS JAN,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 2, (UnitPrice * Quantity - Discount), 0)), '$990') AS FEB,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 3, (UnitPrice * Quantity - Discount), 0)), '$990') AS MAR,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 4, (UnitPrice * Quantity - Discount), 0)), '$990') AS APR,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 5, (UnitPrice * Quantity - Discount), 0)), '$990') AS MAY,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 6, (UnitPrice * Quantity - Discount), 0)), '$990') AS JUN,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 7, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS JUL,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 8, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS AUG,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 9, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS SEP,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 10, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS OCT,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 11, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS NOV,
TO_CHAR(SUM(DECODE(EXTRACT(MONTH FROM OrderDate), 12, (UnitPrice * Quantity - Discount), 0)), '$99,990') AS DEC, 
TO_CHAR(SUM((UnitPrice * Quantity - Discount)), '$999,990') AS TOTAL
FROM ORDERS 
JOIN ORDERDETAILS USING (OrderID)
JOIN EMPLOYEES USING (EmployeeID)
WHERE EXTRACT(YEAR FROM OrderDate) = 2010
GROUP BY GROUPING SETS (EmployeeID||' '||FirstName||' '||LastName, ())
ORDER BY 1;
```

注意：请注意，FORMAT命令和TO_CHAR()函数是用于格式化目的。如果你想了解更多信息，请查看Oracle网站上的[格式模型](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Format-Models.html#GUID-DFB23985-2943-4C6A-96DF-DF0F664CED96)和[格式化SQL*Plus报告](https://docs.oracle.com/database/121/SQPUG/ch_six.htm#SQPUG016)部分。

![](../Images/20c3ea352b3155d73bf77da29769b3dc.png)

# 使用“PIVOT”的透视表

![](../Images/93e298803a661c1d579b990d24846251.png)

图片由[Noah Windler](https://unsplash.com/@noahwindler?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

现在你已经知道如何使用DECODE()创建透视表，我们可以继续介绍Oracle在11g版本中引入的PIVOT()子句。

> SELECT *
> 
> FROM (查询)
> 
> PIVOT (aggr FOR 列 IN (value1, value2, …)
> 
> );

+   **aggr**: 函数，如SUM、COUNT、MIN、MAX或AVG

+   **value**: 用于*列*的值列表，这些值将转换为交叉表查询结果中的标题。

让我们回到冰淇淋店的例子。以下是如何使用PIVOT()子句来实现：

## 第一版：没有总计列和行的透视表

```py
SELECT *
FROM (
      SELECT day of the week, ice cream flavor, revenue
      FROM ice cream shop dataset 
      WHERE date between last Monday and last Sunday
)
PIVOT (
       SUM(revenue) 
       FOR day of the week IN ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday')
);
```

## 第二版：带有总计列和行的透视表

如果你想在透视表中添加一个总计列，使用NVL()函数是一个很好的方法。

```py
SELECT *
FROM (
      SELECT NVL(ice cream flavor, 'TOTAL') AS ice cream flavor,
             NVL(day of the week, -1) AS DOW, 
             SUM(revenue) AS REV
      FROM ice cream shop dataset 
      WHERE date between last Monday and last Sunday
      GROUP BY CUBE (ice cream flavor, day of the week) 
)
PIVOT (
       SUM(REV) 
       FOR DOW IN ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday', -1 AS TOTAL)
);
```

## 第三版：带有总计列和行以及其他总计的透视表

当其他总计出现时，只有一种方法可以解决问题，那就是使用JOIN()子句。

```py
SELECT ice cream flavor, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday, TOTAL, OTHER TOTAL
FROM (
      SELECT NVL(ice cream flavor, 'TOTAL') AS ice cream flavor,
             NVL(day of the week, -1) AS DOW, 
             SUM(revenue) AS REV
      FROM ice cream shop dataset 
      WHERE date between last Monday and last Sunday
      GROUP BY CUBE (ice cream flavor, day of the week) 
)
PIVOT (
       SUM(REV) 
       FOR DOW IN ('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday', -1 AS TOTAL)
)
JOIN (
      SELECT NVL(ice cream flavor, 'TOTAL') AS ice cream flavor, 
             SUM(purchase ID) "OTHER TOTAL"
      FROM ice cream shop dataset 
      WHERE date between last Monday and last Sunday
      GROUP BY ROLLUP (ice cream flavor)
) USING (ice cream flavor);
```

注意：在上面的伪代码中，我们使用了GROUP BY中的CUBE和ROLLUP扩展。简短的解释就可以说明问题。

+   **CUBE(A, B, C)**: (A, B, C), (A, B), (A, C), (B, C), (A), (B), (C), ()

+   **ROLLUP(A, B, C)**: (A, B, C), (A, B), (A), ()

一旦我们了解了PIVOT()子句的工作原理，你能否用我们在第一部分中提供的Northwind数据集进行练习？

Q1\. 假设我们想找出每个员工在各自的原籍国中，在哪些区域服务。

```py
--Q1
--Try it out!
```

Q2\. 对于2010年每个月，显示每个员工处理的订单收入。同时，四舍五入到最接近的美元，并显示总收入和总订单数。

```py
--Q2
--Try it out!
```

# 结语

在本指南中，我们探讨了SQL中透视表的强大功能，重点介绍了**DECODE()**和**PIVOT()**函数。我们首先介绍了透视表及其在将行转换为列以进行更深入的数据分析中的重要性。接着，我们演示了如何使用DECODE()创建透视表，并详细讨论了Oracle 11g中引入的更简化的PIVOT()函数，该函数简化了透视表的创建过程。通过应用这些技术，我们展示了如何通过实际示例（如冰淇淋店数据集）高效地分析多维数据。

![](../Images/99f5eaa3aca6a02bd505db3ffccfd7dd.png)

图片由[karl muscat](https://unsplash.com/@kmus07?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 回顾与收获

1.  **使用DECODE()的透视表**：一种使用DECODE()函数手动进行数据透视的基本方法。

1.  **使用PIVOT()的透视表**：利用PIVOT()函数创建更加高效且易于阅读的透视表。

**随时在评论区分享你的答案。** 我喜欢学习数据，并反思（写下）我在实际应用中学到的东西。如果你喜欢这篇文章，请为它点赞，表示支持。如果你有更多话题想要讨论，可以通过[LinkedIn](https://www.linkedin.com/in/yung-linchang/)和[Twitter](https://twitter.com/yunglinchang)联系我。也欢迎在Medium上关注我，未来会有更多数据科学相关文章发布！

> 来数据科学乐园一起玩吧！
