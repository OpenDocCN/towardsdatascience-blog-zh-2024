# 在 Power BI 中计算前一个值

> 原文：[https://towardsdatascience.com/calculating-the-previous-value-in-power-bi-9ddc062ef2df?source=collection_archive---------8-----------------------#2024-04-19](https://towardsdatascience.com/calculating-the-previous-value-in-power-bi-9ddc062ef2df?source=collection_archive---------8-----------------------#2024-04-19)

## 基于仪表数据计算消耗量看起来很简单。然而，复杂的情况可能会带来挑战。让我们看看如何解决这个问题。

[](https://medium.com/@salvatorecagliari?source=post_page---byline--9ddc062ef2df--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--9ddc062ef2df--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ddc062ef2df--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9ddc062ef2df--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--9ddc062ef2df--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ddc062ef2df--------------------------------) ·10 分钟阅读·2024年4月19日

--

*当我们拥有仪表数据时，就像我们从家里的能源或水表中获得的数据一样，我们想计算这些值随时间的消耗量。在一个仪表的情况下非常简单，但如果我们有多个仪表，涉及不同区域、值等情况，就可能变得复杂。让我们看看如何在 Power BI 中解决这个问题。*

![](../Images/3c6e4c7004e145b72d8aa37563ad2bf9.png)

照片由 [Doris Morgan](https://unsplash.com/@d_morgan?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

大多数时候，我们处理的是事务数据。

每一行描述了来自源系统的一个事件（或交易）。

然后我们有库存数据，比如我们商店里某种特定产品的单位数，这些单位数可能随时间变化。

或者我们有仪表数据，随着时间变化，比如家里的电表。

当我们想计算消耗量时，我们根据获取仪表值的日期和时间对数据进行排序，然后将当前值减去前一个值。这样，我们就得到了消耗量。

现在，想象一下我们有多个不同地址的房屋，每个房屋里有多个仪表。

在这种情况下，我们必须为每个仪表计算前一个值，以获得正确的值。

当我们必须在 Power BI 中进行此操作时，这带来了一些挑战。

顺便说一下，在 SQL 中，我们有一些技术可以以最小的努力解决这个挑战。所以，当你把数据放在关系型数据库中时，就在那里处理它。那样会更容易。

那么，让我们看看如何在 Power BI 中解决这个问题。

我先在 Power Query 中做一次，然后再用 DAX 做一次。

# 数据

我通过我的 Date 表生成数据，并使用以下 SQL 查询将结果加载到一个表中：

```py
DECLARE @dimId int = 2;
DECLARE @value decimal(18,5) = RAND() * 12;

INSERT INTO [dbo].[MeterData]
  ([DateKey]
  ,[Value]
  ,[House]
  ,[Meter])
SELECT [DateKey]
    ,DATEDIFF(dd, '20000101', [Date]) + (DATEDIFF(dd, '20000101', [Date]) * @value%@dimId)
    ,'House ID ' + CAST(([DateKey]%3) + 1 AS varchar(15)) AS [House]
    ,'Meter ID ' + CAST(@dimId - 1 AS varchar(15)) AS [Meter]
  FROM [dbo].[Date]
    WHERE [DateKey] BETWEEN 20210101 AND 20240415;
```

我多次执行此查询，以在将变量 @dimId 设置为 2 到 6 之间的值时获取所需的数据。

结果是每个 Meter ID 随时间变化的值列表：

![](../Images/5985f0a4a1189c4861ee7337c03921a8.png)

图 1 — 我的场景数据（图源自作者）

我将此表导入 Power BI 两次，并命名为：

+   MeterData_PQ → 对于 Power Query 方法

+   MeterData_DAX → 对于 DAX 方法

我需要这两张表，在完成后将它们并排比较，分析哪种方法可能更好。

# 在 Power Query 中进行操作

我在互联网上稍作搜索后找到了这种方法。

我在下面的参考部分中添加了指向原始文章的链接。

为确保数据在下一步中按正确顺序排列，我通过 House、Meter 和 DateKey 添加排序表达式，以确保所有行在一起：

```py
= Table.Sort(#"Changed Type",{{"House", Order.Ascending}, {"Meter", Order.Ascending}, {"DateKey", Order.Ascending}})
```

这是排序后的数据结果：

![](../Images/9e78062f3cb7c7d6315884599e506a86.png)

图 2 — 排序操作后的表格（图源自作者）

该模式会自动为每个嵌套表重复。

现在，我在 Power Query 中使用 Group By 转换，将每个 House 和 Meter 的组合的所有行分组在一起：

![](../Images/deeb6dbcce7b185f1cbb7cb2deac4b43.png)

图 3 — 将所有的 Value 行组合在一起（图源自作者）

现在数据看起来是这样的：

![](../Images/44b1ee9245b7ef3ed438a36444cd8871.png)

图 4 — 分组后的数据（图源自作者）

当我点击 ValueTable 列的某个单元格时，我看到与该行关联的所有行作为嵌套表呈现：

![](../Images/f0f169a45083a598f1429d07338f5a74.png)

图 5 — 每个 House 和 Meter 组合的嵌套表（图源自作者）

后续的转换必须应用于嵌套表。Power Query 界面不支持这种操作。

因此，我必须将连续的转换作为手动步骤添加：

![](../Images/41492a771947706e023b3bb290dffdd6.png)

图 6 — 向 Power Query 添加新步骤（图源自作者）

我输入以下表达式来为数据添加一个索引列：

```py
= Table.TransformColumns(
    #"Grouped Rows",
    {{"ValueTable", each Table.AddIndexColumn(_,"Index")}}
    )
```

这是嵌套表中的结果：

![](../Images/19de2020f63559739873d351d4d3ca84.png)

图 7 — 向嵌套表添加索引（图源自作者）

索引列是根据数据的顺序计算的。这就是为什么在添加索引之前，我们必须按顺序排列数据。

为了使其更易读，我将此步骤重命名为“AddIndexColumn”。

现在，我添加另一个步骤来获取前一个值：

```py
= Table.AddColumn(AddIndexColumn, "PrevValue", each let
    AllDataTable = [ValueTable],
    PrevRowValue = Table.AddColumn(AllDataTable, "PrevValue",
                        each try AllDataTable [Value] { [Index] -1 }
                        otherwise null
                        )

                    in
                    PrevRowValue)
```

嵌套表中新增列的结果如下：

![](../Images/b3b5feae21e9f5649ea202f6ec16a599.png)

图 8 — 带有前一个值的嵌套表（图源自作者）

接下来，我使用 Drill Down 转换，将嵌套表展开为原始表格：

![](../Images/3035522d1db3a924ac7b05912dd631bc.png)

图 9 — 深入嵌套表格（图由作者提供）

现在，我有一个包含所有嵌套表格的列表。我添加了一个新步骤，使用以下表达式来实现：

```py
= Table.Combine( #"Drill down PrevValue"
              ,{"DateKey", "House", "Meter", "Value", "PrevValue"}
              )
```

结果是包含所有原始行的表格，但增加了“PrevValue”列。

为了完成任务，我可以添加一个新的计算列，将“PrevValue”从“Value”中减去，以得到所需的消耗量：

![](../Images/60ca6527aa9b504749742dd31b31a5b7.png)

图 10 — 计算消耗量（图由作者提供）

最后，我必须将新数字列的数据类型设置为“十进制数”。

在将结果加载到 Power BI 后，我可以为每个仪表和房屋创建一个消耗图表：

![](../Images/9d8a80dc192911b8397dbe3c08e17c39.png)

图 11 — 每个房屋和仪表的时间消耗（图由作者提供）

这是预期的结果，我可以开始创建一个漂亮的报告和有用的可视化图表。

但首先，我想给你展示一个使用 DAX 的方法。

# 在 DAX 中实现

在了解如何在 Power Query 中实现之后，让我们在 DAX 中做一下。

这里有两种可能的情况：

+   仪表读取之间的固定间隔。

+   读取之间的间隔变化。

获取第一种情况的消耗量是简单的：

1.  我必须识别出前一天的值所在的行。

1.  获取该行的值。

1.  计算消耗量。

让我们开始吧：

我通过创建两个关键列来实现：

1.  一个是当前读数

1.  一个是前一天的读取值（这可以是前一天、前一周、前一月，或任何你设置的间隔）。

由于数据生成方式的不同，每个仪表 ID 都有一个读数。

因此，为了创建第一个关键列，我创建了一个计算列，使用以下表达式（暂时忽略房屋列）：

```py
CurrentKey =
VAR RowKey = FORMAT('MeterData_DAX'[Date], "YYYYMMDD") & "_" & 'MeterData_DAX'[Meter]

RETURN
  RowKey
```

注意：我使用“YYYYMMDD”格式，以便更清晰地展示结果，因为这是一个通用格式。

我需要从日期表中获取前一天的日期，以便应用日期计算，例如 [DATEADD()](https://dax.guide/dateadd/)。

然后我可以回到前一天：

```py
PreviousKey =
VAR PreviousDate = FORMAT(DATEADD('MeterData_DAX'[Date], -1, DAY), "YYYYMMDD")
VAR RowKey =
      PreviousDate & "_" & 'MeterData_DAX'[Meter]

RETURN
  RowKey 
```

最后，我可以使用 [LOOKUPVALUE()](https://dax.guide/lookupvalue/) 来获取前一个值：

```py
Previous Value = LOOKUPVALUE('MeterData_DAX'[Value]
                            ,'MeterData_DAX'[CurrentKey]
                                ,'MeterData_DAX'[PreviousKey]
                                )
```

或者，我可以使用 [CALCULATE()](https://dax.guide/calculate/) 来实现相同的结果：

```py
PrevValue =
VAR PreviousKey = 'MeterData_DAX'[PreviousKey]

RETURN
  CALCULATE(
      MAX('MeterData_DAX'[Value])
      ,REMOVEFILTERS('MeterData_DAX')
      ,'MeterData_DAX'[CurrentKey] = PreviousKey
      )
```

这是这三个表达式的结果：

![](../Images/cb4137e6b5e1349ad635af58eb5841e8.png)

图 12 — 按天读取的前值结果（图由作者提供）

但是这种方法不适用于不规则的读数。

当我查看我的数据（包括房屋数据）时，我看到这样的情况：

![](../Images/7171389c369957350f0fa35e028c4160.png)

图 13 — 按房屋和仪表筛选的读数视图（图由作者提供）

如你所见，读取之间存在间隔。

为了获得正确的结果，我使用了一个包含两步的方案：

1.  获取前一个读取的日期。

1.  获取该日期的值。

我为第一步创建了一个度量值：

```py
Previous reading date =
VAR CurrentDate = SELECTEDVALUE('MeterData_DAX'[DateKey])
VAR CurrentHouse = SELECTEDVALUE('MeterData_DAX'[House])
VAR CurrentMeter = SELECTEDVALUE('MeterData_DAX'[Meter])

RETURN
  CALCULATE(MAX('MeterData_DAX'[DateKey])
          ,REMOVEFILTERS('MeterData_DAX')
          ,'MeterData_DAX'[House] = CurrentHouse
          ,'MeterData_DAX'[Meter] = CurrentMeter
          ,'MeterData_DAX'[DateKey] < CurrentDate
          )
```

首先，我将当前的日期、房屋和仪表存储到变量中。

然后，我计算 DateKey 的最大值，同时移除表中的所有筛选器，添加当前房屋和表计的筛选器，并仅包含低于当前 DateKey 的 DateKey。

对于包含更多列的表格，我可能会使用稍微不同的方法，不是移除所有表格中的筛选器，而只是移除必须的列筛选器，例如，仅对 DateKey、House 和 Meter 列进行筛选。

但结果符合需求：

![](../Images/7c80c9686a15771fcbcaeb48b5d471ca.png)

图14 — 用于获取上次读取日期的度量结果（图由作者提供）

通过使用上下文转换，我可以使用这个度量创建一个新的“前键”列版本（我也在当前键列的表达式中包括了房屋）：

```py
PreviousKey =
VAR PreviousDate = [Previous reading date]
VAR RowKey = PreviousDate & "_" & 'MeterData_DAX'[House] & "_" & 'MeterData_DAX'[Meter]

RETURN
  RowKey
```

现在，我可以使用与之前相同的表达式，根据两个键列获取所需结果：

![](../Images/ebc4b0ccf9beab330741d3c60766193e.png)

图15 — 使用变量间隔计算前值的结果（图由作者提供）

在加入与之前相同的视觉效果后，结果与我通过 Power Query 操作数据得到的结果相同：

![](../Images/2a179c0eff88350824620d1e49c07a33.png)

图16 — 使用 DAX 计算的2024年图表（图由作者提供）

最后，我可以通过直接在一个压缩且自包含的“前值”列版本中计算它们，从而摆脱中间的键列：

```py
PreviousValue Compact =
VAR PreviousDate = [Previous reading date]
VAR PreviousRowKey = PreviousDate & "_" & 'MeterData_DAX'[House] & "_" & 'MeterData_DAX'[Meter]

RETURN
  CALCULATE(MIN('MeterData_DAX'[Value])
          ,REMOVEFILTERS('MeterData_DAX')
          ,FORMAT('MeterData_DAX'[Date], "YYYYMMDD") & "_" & 'MeterData_DAX'[House] & "_" & 'MeterData_DAX'[Meter] = PreviousRowKey
)
```

这里是结果并排显示，它们是完全相同的：

![](../Images/0f8354093ce3feb4ac0dd1c3d1e005ec.png)

图17 — 使用中间键列和压缩（自包含）版本计算前值的结果（图由作者提供）

现在我们有了多种解决方案，哪个是更好的呢？

# 哪个更好？

我们应该如何决定哪种方法更好？

在我看来，这归结于可用的技能。

我的意思是，在必须维护该解决方案的团队中可用的技能。这可以是你，或者是客户团队。

哪个是我偏好的方法？

+   我是否想尽早准备好所有数据？

+   或者，我想要最简单的解决方案吗？

+   或者，我是否更倾向于某种语言？

在这种情况下，这就是使用 Power Query 或 DAX。

我更倾向于尽早准备我的数据。

因此，我更倾向于使用 Power Query 准备数据，并使其准备好使用，而不需要在 Power BI 中添加计算列。

然而，考虑到简洁性，我必须承认，使用 DAX 的自包含计算列的方法是最好的解决方案。

但要完全理解发生了什么以及为什么发生并不容易。

现在，我们可以通过硬性数据分析这两种方法的效率：模型统计。

我使用 DAX Studio 获取度量（高级菜单和查看度量）。

我得到以下信息：

![](../Images/807bf8c298739f4f278c0617b8647399.png)

图18 — 数据模型的度量，包含两张表（图由作者提供）

我可以看到，使用 DAX 中计算列的方法比 Power Query 方法占用更多内存。

但当我们减去两个关键列的大小（上方红色部分）时，得到的结果是：

930’634–332’745–332’813 = 265’076 字节

然后，我必须从两个列中减去前一个值（上方蓝色部分）：265’076–48’248 = 207’828 字节。

与使用 Power Query 准备的表格相比，空间差异在这种情况下是微不足道的。

但我只有 6’005 行数据。当我们处理数十万甚至数百万行时，这个差异可能非常大。

我曾经遇到过客户希望以特定方式解决问题的情况，因为他不熟悉另一种方法，尽管后一种方法会更高效地提供解决方案。

决定最佳方法是具有挑战性的，因为你可能需要考虑不同的因素。

现在你已经有了两种解决方案的信息，轮到你选择最合适的一个了。

![](../Images/c9e479b08dbd0f22ce24e760264d3437.png)

图片由[Brendan Church](https://unsplash.com/@bdchu614?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 参考资料

如上所述，数据是自生成的，与现实世界无关。

使用 Power Query 的方法源自这篇博客文章和视频：

[https://gorilla.bi/power-query/get-previous-row-value/#:~:text=The%20earlier%20row%20has%20an,a%20table%20to%20merge%20with](https://gorilla.bi/power-query/get-previous-row-value/#:~:text=The%20earlier%20row%20has%20an,a%20table%20to%20merge%20with).

这里是我关于上下文转换的文章：

[](/whats-fancy-about-context-transition-in-dax-efb5d5bc4c01?source=post_page-----9ddc062ef2df--------------------------------) [## DAX 中的上下文转换有什么特别之处

### 行上下文和筛选上下文是 DAX 中的常见概念。但我们可以通过上下文转换在这两者之间切换。

towardsdatascience.com](/whats-fancy-about-context-transition-in-dax-efb5d5bc4c01?source=post_page-----9ddc062ef2df--------------------------------)

其他解决方案和方法包括在 Power Query 中使用单一的 M 表达式。我决定使用这个方法，因为它实现起来简单，而且很容易理解发生了什么。

请考虑关注我并订阅，以便我添加新内容时可以第一时间收到邮件：

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----9ddc062ef2df--------------------------------) [## 每当 Salvatore Cagliari 发布新内容时，你都会收到电子邮件。

### 每当 Salvatore Cagliari 发布新内容时，你都会收到电子邮件。通过注册，如果你还没有 Medium 账户，你将创建一个账户...

medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----9ddc062ef2df--------------------------------)

我让我的文章对所有人开放，尽管 Medium 有付费墙。这让我能从每个读者那里赚取一些收入，但我关闭了付费墙，所以你可以免费阅读我的文章。

你可以通过以下方式支持我在空闲时间进行的工作：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或扫描此二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都非常感激，这将帮助我找到更多时间为你创作更多内容。

非常感谢。
