# 如何在 Power Query 中动态限制数据导入

> 原文：[https://towardsdatascience.com/how-to-dynamically-restrict-data-import-in-power-query-68a29fce0640?source=collection_archive---------10-----------------------#2024-08-06](https://towardsdatascience.com/how-to-dynamically-restrict-data-import-in-power-query-68a29fce0640?source=collection_archive---------10-----------------------#2024-08-06)

## *当我们存储大量数据时，我们可能会问自己：我真的需要所有这些数据来进行报告吗？如果你有这个问题，并且想了解如何控制 Power BI 文件中的数据量，那么这篇文章是你需要阅读的。*

[](https://medium.com/@salvatorecagliari?source=post_page---byline--68a29fce0640--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--68a29fce0640--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68a29fce0640--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--68a29fce0640--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--68a29fce0640--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68a29fce0640--------------------------------) ·阅读时间 8 分钟·2024年8月6日

--

![](../Images/be1a76f7b3dac12b0445af16cc220af0.png)

图片来源：[Claudio Schwarz](https://unsplash.com/@purzlbaum?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

我的最大数据集包含 20 年的数据。

虽然当我想使用大数据集进行一些测试时，这是很好的，但报告有时需要超过几年的历史数据也并不罕见。

我的客户通常只需要两三年的数据。

而现在，挑战来了：

如何在将数据导入 Power BI 时控制时间范围？

当处理 SQL 数据库或访问可以通过编程控制或传递参数的 Web REST API 等数据源时，这并不算什么大问题。

然而，有时我必须自己控制这一点。在这种情况下，我必须在 Power Query 中进行操作。

理想情况下，我希望只构建一次，它就能永远运行，无需进一步干预。

这正是本文的核心内容。

# 准备工作

如果你还没有启用，请在 Power Query 中启用公式栏，以便按照我的指示操作。

打开 Power BI 设置，并切换到 Power Query 编辑器页面。

在这里，启用“显示公式栏”选项：

![](../Images/4bb15b293231d4c825e9c52034880804.png)

图 1 — 启用 Power Query 编辑器的公式栏（图由作者提供）

# 创建动态参数

好吧，标题有点误导。

Power Query 的参数本身并不是动态的。

它们可以基于查询，但我无法用表达式设置值，即使查询可以是动态的或数据驱动的。

所以，我必须构建一个或两个查询，然后可以用来筛选我们的数据。

当数据包含对预期报告目标有用的时间点时，我需要一个查询来导入最早的日期。

如果我需要限制开始和结束日期，例如感兴趣的最新数据点的日期，我需要两个查询。

假设我想加载从两年前开始到上个月底的数据：从 2022 年年初到 2024 年 7 月底。

让我们开始吧。

打开 Power Query 后，我创建一个新的空白查询：

![](../Images/69922758f602a1075a5d5ba3f3928ca9.png)

图 2 — 在 Power Query 中创建一个空白查询（图由作者提供）

这将包含我数据的开始日期。

我将使用 M 函数 [#date](https://learn.microsoft.com/en-us/powerquery-m/sharpdate)、[Date.Year](https://learn.microsoft.com/en-us/powerquery-m/date-year) 和 [DateTime.LocalNew](https://learn.microsoft.com/en-us/powerquery-m/datetime-localnow) 来设置它：

```py
#date(Date.Year(DateTime.LocalNow())-2, 1, 1)
```

1.  DateTime.LocalNow() 获取当前的日期和时间

1.  Date.Year() 从给定日期中提取年份

1.  #date() 根据年份、月份和日期的参数创建一个日期

当我从年份中减去 2 时，我得到 2022 作为年份，并且使用以下两个参数，我将得到 2022–01–01。

但是结果并不像预期的那样：

![](../Images/33441bd01f8f807a8b58ca543597329e.png)

图 3 — 空白查询中的 M 表达式结果（图由作者提供）

当我们打开高级编辑器时，会看到 Power Query 已将表达式设置为字符串。

我们必须移除引号，然后它就能正常工作：

![](../Images/bad77b815f213dd31b3c942384076dbf.png)

图 4 — 移除引号以按预期设置表达式（图由作者提供）

但是，在继续之前，我将查询名称从 Query1 改为“StartDate”。

接下来，我创建第二个空白查询（或者复制第一个查询），并以与上面相同的方式设置以下表达式：

```py
Date.EndOfMonth(Date.AddMonths(DateTime.LocalNow(), -1))
```

1.  DateTime.LocalNow 获取当前的日期和时间

1.  Date.AddMonths() 向给定日期添加月份。

    在这种情况下，它会回溯一个月。

1.  Date.EndOfMonth() 将日期移到给定月份的最后一天。

在我的例子中，我将得到 2024–07–31。

结果如下：

![](../Images/d7496bd6e5fa948d9ff450e646e52723.png)

图 5 — 获取结束日期的表达式结果（图由作者提供）

由于我只需要日期，可以通过将结果转换为日期来去除时间部分：

![](../Images/a6405772ab4f6151f2c2ab028e6bc2b5.png)

图 6 — 将日期/时间输出转换为仅日期（图由作者提供）

在重命名新查询后，一切就绪，可以根据这两个查询筛选数据。

# 筛选数据

接下来，我可以在我的事实表中添加一个筛选表达式。

我找到我的数据的日期列，并选择一个“between”筛选器：

![](../Images/f934c0c3a89da5bb19c3942ca5e25a7a.png)

图7 — 为日期列选择“between”筛选器（图由作者提供）

接下来，我在两个字段中输入两个任意日期（稍后我会将它们替换为新创建的查询）：

![](../Images/a77c34167fba0b4ec1beee32990308ed.png)

图8 — 在“between”筛选器中添加任意日期（图由作者提供）

点击确定后，数据已经被筛选。

但现在，我想更改筛选器，以使用这两个查询。

为此，我替换筛选器表达式中的日期以使用它们：

当我查看Power Query编辑器顶部的公式栏时，我看到选定步骤的表达式。

我将表达式从以下内容更改为：

```py
Table.SelectRows(#"Renamed Columns", each [Date] >= **#date(2024, 8, 1)** and [Date] <= **#date(2024, 8, 31)**)
```

如此：

```py
Table.SelectRows(#"Renamed Columns", each [Date] >= **StartDate** and [Date] <= **EndDate**)
```

完成。

由于我有两个事实表，我复制这个表达式并将其添加到另一个表中：

我选择表格，并点击fx按钮。之后，我可以将与之前相同的表达式粘贴到公式栏中：

![](../Images/7f0f0f72b817b337511fe1e0aff9f5ef.png)

图9 — 为第二个表格添加新表达式以进行筛选（图由作者提供）

最后，我将两个表格的步骤名称更改为更有意义的名称：

![](../Images/e0a9a340d779f102aead7abb575e28a0.png)

图10 — 更改步骤名称以筛选数据（图由作者提供）

# 添加一个参数来控制年份数

最后，我可以添加一个Power Query参数，以控制我要在Power BI中导入的年份。

![](../Images/d01621619f23d278e6500c75f05b0735.png)

图11 — 创建一个新参数来存储年份数（图由作者提供）

我输入一个名称，将类型设置为十进制数字，并为当前值输入2：

![](../Images/b8138176ad2d829ccb6148b4012a1ae2.png)

图12 — 设置新参数（图由作者提供）

接下来，我更改StartDate查询的M表达式，以使用此参数：

```py
#date(Date.Year(DateTime.LocalNow())- **NumberOfYearsToImport**, 1, 1)
```

结果不会改变，因为我之前使用过两个。

现在，我可以通过Power BI更改参数和数据量，而无需更改Power Query中的表达式。

此外，在将报告发布到Power BI云端后，我可以更改此参数：

打开语义模型的设置，打开“参数”部分，并更改值。

在Power BI中加载数据后，我只看到从2022年1月1日到2024年7月末的数据：

![](../Images/5affd53933446b4685549d8392c18204.png)

图13 — 加载到Power BI后的筛选数据（图由作者提供）

# 那么，DateId呢？

如果你的数据不包含日期列，而是包含数字DateId列，那么上述表达式将不再有效。

对于不熟悉DateId的人来说，它是日期的数字表示。

例如，2024–07–15会被转换为20240715。

在这种情况下，使用这些表达式来计算DateId：

对于StartDate：

```py
(Date.Year(DateTime.LocalNow())- NumberOfYearsToImport) * 10000 + 101
```

对于EndDate，这是完整的M脚本（在创建空查询后，将脚本替换到高级编辑器中）：

```py
let
Source = Date.EndOfMonth(Date.AddMonths(DateTime.LocalNow(), -1)),
#"Extracted Date" = Date.Year(Date.From(Source)) * 10000 + Date.Month(Date.From(Source)) * 100 + Date.Day(Date.From(Source))
in
#"Extracted Date"
```

数据过滤与上述相同，只不过使用的是数值而非日期。

# 关闭新查询的加载

没有进一步的操作，两个新查询已被加载到Power BI中：

![](../Images/04a75b0381653ff4ec8f5574683675dc.png)

图14 — Power BI中的Start-和EndDate表（图由作者提供）

由于这两张表在Power BI中用途不大（除非你想在报告中展示这两个值作为信息），我将禁用Power Query中这两张表的加载：

![](../Images/0bdec8218ca8ef319292ed520935985a.png)

图15 — 禁用不需要的表加载到Power BI中（图由作者提供）

你将收到一个关于可能数据丢失的警告。你可以确认它。

这些表的名称将以斜体显示，并且它们不再被加载到Power BI中。

# 结论

这里展示的方法非常有用，可以动态定义导入到Power BI中的数据的时间范围。

在我的案例中，我使用SQL Server作为我的数据源。

Power Query可以将数据过滤转换为SQL，并将查询发送（折叠）到数据源。

如果你想了解更多关于查询折叠的信息，请阅读这个：

[](/exploring-query-folding-in-power-query-8288fb3c9c2f?source=post_page-----68a29fce0640--------------------------------) [## 探索Power Query中的查询折叠

### 查询折叠是Power Query中的一个重要功能，它将处理步骤转移到源端。这里我们进一步探讨…

towardsdatascience.com](/exploring-query-folding-in-power-query-8288fb3c9c2f?source=post_page-----68a29fce0640--------------------------------)

然而，这种通用的方法也适用于任何其他数据源。

但要注意：如果Power Query无法将过滤器传递给数据源，它将首先加载整个数据集，然后在加载完所有数据后过滤掉不需要的行。

如果你拥有大量数据，这可能会成为一个问题。

尽管这种方法很有用，但将所需数据放在源端是至关重要的。请向你的数据提供者寻求帮助。

然而，在开发过程中，限制加载到Power BI中的数据量可能会有所帮助，等解决方案发布后再加载整个数据集。

无论如何，我希望你能学到一些Power Query的新知识。

# 参考文献

和我之前的文章一样，我使用了Contoso示例数据集。你可以从微软[这里](https://www.microsoft.com/en-us/download/details.aspx?id=18279)免费下载ContosoRetailDW数据集。

Contoso数据可以根据MIT许可自由使用，详情见[这里](https://github.com/microsoft/Power-BI-Embedded-Contoso-Sales-Demo)。

我扩大了数据集，以便让DAX引擎处理得更加繁重。

Online Sales表包含7100万行（而不是1260万行），而Retail Sales表包含1850万行（而不是340万行）。

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----68a29fce0640--------------------------------) [## 每当Salvatore Cagliari发布时，订阅电子邮件。

### 每当萨尔瓦托雷·卡利亚里发布新内容时，您将收到电子邮件通知。通过注册，您将创建一个Medium账户，如果您还没有的话…

[medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----68a29fce0640--------------------------------)

尽管Medium有付费墙，我仍然让我的文章对所有人开放。这使我可以为每位读者赚取少许收入，但我关闭了付费墙，您可以免费阅读我的文章。

您可以通过以下方式支持我的工作，我是在空闲时间完成这些工作的：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或扫描此二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都将不胜感激，并帮助我找到更多时间为您创作更多内容。

非常感谢。
