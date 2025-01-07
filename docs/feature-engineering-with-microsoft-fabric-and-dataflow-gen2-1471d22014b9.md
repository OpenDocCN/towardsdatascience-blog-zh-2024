# 使用 Microsoft Fabric 和 Dataflow Gen2 进行特征工程

> 原文：[https://towardsdatascience.com/feature-engineering-with-microsoft-fabric-and-dataflow-gen2-1471d22014b9?source=collection_archive---------11-----------------------#2024-04-15](https://towardsdatascience.com/feature-engineering-with-microsoft-fabric-and-dataflow-gen2-1471d22014b9?source=collection_archive---------11-----------------------#2024-04-15)

## Fabric Madness 第三部分

[](https://medium.com/@roger_noble?source=post_page---byline--1471d22014b9--------------------------------)[![Roger Noble](../Images/869b5b0f237f24b119ca6c41c2e31162.png)](https://medium.com/@roger_noble?source=post_page---byline--1471d22014b9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1471d22014b9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1471d22014b9--------------------------------) [Roger Noble](https://medium.com/@roger_noble?source=post_page---byline--1471d22014b9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1471d22014b9--------------------------------) ·阅读时间11分钟·2024年4月15日

--

![](../Images/6a72c6bcc90ff02e9f300c48d9df2afc.png)

图片由作者和 ChatGPT 提供。“设计一张插图，展示一名残奥篮球运动员在比赛中的动作，本次主题为数据管道”提示。ChatGPT，4，OpenAI，2024年4月15日。[https://chat.openai.com.](https://chat.openai.com./)

在[上一篇文章](https://medium.com/towards-data-science/feature-engineering-with-microsoft-fabric-and-pyspark-16d458018744)中，我们讨论了如何使用 PySpark 和 Notebooks 进行特征工程。虽然 Spark 提供了很大的灵活性和强大功能，但它相当复杂，需要大量代码来入门。并不是每个人都愿意编写代码，或者有时间学习一种新的编程语言，这就是 Dataflow Gen2 的用武之地。

# 什么是 Dataflow Gen2？

Dataflow Gen2 是一个低代码数据转换和集成引擎，允许您创建数据管道，将数据从各种来源加载到 Microsoft Fabric 中。它基于 Power Query，Power Query 已集成到许多 Microsoft 产品中，如 Excel、Power BI 和 Azure Data Factory。Dataflow Gen2 是一个通过可视化界面创建数据管道的优秀工具，能够让您轻松、快速地创建数据管道。如果您已经熟悉 Power Query 或不怕编写代码，您还可以使用底层的 M（“Mashup”）语言来创建更复杂的转换。

在这篇文章中，我们将演示如何使用 Dataflow Gen2 创建训练机器学习模型所需的特征。我们将使用与上一篇文章相同的数据集，该数据集包含有关大学篮球比赛的数据。

![](../Images/9612ef75cfbd3ebf67434d7e9b667e29.png)

图 1 — 最终结果。图片由作者提供。

# 挑战

我们将使用两个数据集来创建我们的特征：常规赛比赛数据和锦标赛比赛数据。这两个数据集还分别拆分为男篮和女篮比赛数据，最终需要将它们合并为一个单独的数据集。总共有四个 csv 文件，需要将其合并并转换为两个独立的表格存储在 Lakehouse 中。

使用 Dataflows 有多种方法可以解决这个问题，在这篇文章中，我将展示三种不同的方法：无代码方法、低代码方法以及最终的更高级的全代码方法。

# 无代码方法

第一个也是最简单的方法是使用 Dataflow Gen2 可视化界面加载数据并创建特征。

## 数据

我们所查看的数据来自 2024 年美国大学篮球锦标赛，这些数据是从正在进行的 2024 年三月机器学习狂潮（March Machine Learning Mania）Kaggle 竞赛中获得的，详细信息请见 [此处](https://www.kaggle.com/competitions/march-machine-learning-mania-2024/overview)，并且此数据已根据 CC BY 4.0 授权协议发布。

## 数据加载

第一步是从 Lakehouse 获取数据，这可以通过在“开始”选项卡中点击“获取数据”按钮，然后从数据源列表中选择 **更多…** 来实现。

![](../Images/09e6830ca797495bd419893e0cb18b0e.png)

图 2 — 选择数据源。图片由作者提供。

从列表中选择 **OneLake 数据中心**，找到 Lakehouse，然后在文件夹中找到 csv 文件。

![](../Images/79af66f24ffb07087bd9b233f519caa0.png)

图 3 — 选择 csv 文件。图片由作者提供。

这将创建一个包含四个步骤的新查询，具体步骤如下：

+   来源：一个查询 Lakehouse 中所有内容的函数。

+   导航 1：将 Lakehouse 中的内容转换为表格。

+   导航 2：通过名称过滤表格以检索选定的 csv 文件。

+   导入的 CSV：将二进制文件转换为表格。

![](../Images/b2fd1a2b8f2b1f429cc45716b45bb3a2.png)

图 4 — 初始加载。图片由作者提供。

数据加载完成后，我们可以开始进行一些基本的数据准备，将数据转换为可以用来创建特征的格式。首先需要做的是将列名设置为基于数据集的第一行。这可以通过在“开始”选项卡的“转换”组中或在“转换”菜单项中选择“使用第一行作为标题”选项来完成。

下一步是将“WLoc”列重命名为“location”，可以通过选择表格视图中的该列，或右键点击该列并选择“重命名”来实现。

location 列包含比赛地点，值为“H”代表主场，“A”代表客场，或“N”代表中立场地。为了方便我们的分析，我们希望将这些值转换为数值，其中“H”表示 1，“A”表示 -1，“N”表示 0，这样更方便在模型中使用。这可以通过选择该列，然后在“转换”菜单项中使用 **替换值…** 转换来实现。

![](../Images/3026952f8952ad4f14af39db338e6cc0.png)

图 5 — 替换值。图片来自作者。

这对于其他两个位置值也需要进行相同的操作。

最后，我们需要将位置列的数据类型从文本更改为整数。这可以通过选择该列，然后在“主页”功能区的“转换”组中，从下拉列表中选择数据类型来完成。

![](../Images/6a798ea01fb3197b4ffcacc135a69f88.png)

图 6 — 最终数据加载。图片来自作者。

为了避免对每种位置类型重复重命名步骤，我们可以使用一些 M 代码来替换位置列中的值。可以通过选择查询中的前一个转换步骤（重命名列），然后在公式栏中选择“插入步骤”按钮来实现。这将添加一个新步骤，您可以输入以下代码来替换位置列中的值。

```py
Table.ReplaceValue(#"Renamed columns", each [location], each if Text.Contains([location], "H") then "1" else if Text.Contains([location], "A") then "-1" else "0", Replacer.ReplaceText, {"location"})
```

## 添加特征

我们已经加载了数据，但它仍然不符合我们的模型要求。数据集中的每一行代表两支队伍之间的一场比赛，包含了胜利队伍和失败队伍的得分和统计信息，并且这些信息都在同一张宽表格中。我们需要创建能够表示每支队伍在比赛中的表现的特征，并且每场比赛每支队伍需要有一行数据。

为了实现这一点，我们需要将数据拆分成两张表，一张用于胜利队伍，另一张用于失败队伍。最简单的方法是为每支队伍创建一个新的查询，然后在最后将它们合并。虽然有几种方法可以做到这一点，但为了保持简单和易于理解（尤其是当我们以后需要回到这个步骤时），我们将创建两个源查询的引用，然后在进行一些轻微的转换后，再将它们合并。

引用列可以通过左侧的查询面板完成，或者在使用图示视图时，通过选择查询的上下文菜单来完成。这将创建一个引用原始查询的新查询，并且对原始查询所做的任何更改都会反映到新查询中。我做了两次操作，一次用于胜利队伍，一次用于失败队伍，然后通过分别为它们加上“ T1_” 和 “T2_” 前缀来重命名列。

![](../Images/eaa71856383445c2c54e32569ebd31f8.png)

图 7 — 拆分数据集。图片来自作者。

一旦列的值设置完毕，我们就可以通过使用“附加查询”将两个查询合并在一起，然后创建我们的第一个特征，即两支队伍之间的得分差。这可以通过选择 T1_Score 和 T2_Score 列，然后在“添加列”功能区的“标准”组中选择“减法”来完成。

完成这些后，我们就可以将数据作为新表加载到 Lakehouse 中。最终的结果应该如下所示：

![](../Images/b1e0440221e77afad699b89e8a1ee67d.png)

图 8 — 所有连接起来。图片来自作者。

无代码方法存在一些局限性，主要问题在于不容易重复使用查询或转换。在上述示例中，我们需要再重复三次相同的步骤，以加载每一个单独的 csv 文件。此时，复制/粘贴就变得非常方便，但这并不是最理想的方式。接下来我们来看看低代码方法。

# 低代码方法

在低代码方法中，我们将结合使用可视化界面和 M 语言来加载和转换数据。这种方法比无代码方法更灵活，但仍然不需要编写大量代码。

## 加载数据

低代码方法的目标是减少所需的重复查询次数，并使得转换的重用变得更加容易。为此，我们将利用 Power Query 是一种函数式语言的特点，创建函数来封装我们想要应用于数据的转换操作。当我们第一次从 Lakehouse 加载数据时，创建了四个步骤，第二步是将 Lakehouse 的内容转换为一个表格，每一行包含一个指向二进制 csv 文件的引用。我们可以将这个作为函数的输入，这个函数将使用“调用自定义函数”转换来加载 csv 到一个新表格中。

![](../Images/38cb149e20c76260a93ed2ba7fc2ddf4.png)

图 9 — 使用名为“Content”的列中的二进制 csv 文件进行 Lakehouse 查询。图片来自作者。

要创建函数，请从“获取数据”菜单中选择“空查询”，或者右键点击查询面板，选择“新建查询” > “空查询”。在新查询窗口中，输入以下代码：

```py
(TableContents as binary) =>let
  Source = Csv.Document(TableContents, [Delimiter = ",", Columns = 34, QuoteStyle = QuoteStyle.None]),
  PromoteHeaders = Table.PromoteHeaders(Source, [PromoteAllScalars = true])
in
  PromoteHeaders
```

这个函数的代码是从我们最初的无代码方法中复制过来的，但它不是直接加载 csv 文件，而是接受一个名为**TableContents**的参数，将其读取为一个 csv 文件`Csv.Document`，然后将数据的第一行设置为列标题`Table.PromoteHeaders`。

然后，我们可以使用“调用自定义函数”转换来将这个函数应用到 Lakehouse 查询的每一行。这可以通过从“添加列”功能区中选择“调用自定义函数”转换，并选择我们刚刚创建的函数来完成。

![](../Images/93db46a8fd3ec467e7c6ccb9c13d3605.png)

图 10 — 调用自定义函数。图片来自作者。

这将会在 Lakehouse 查询中创建一个新列，其中 csv 文件的所有内容会加载到一个表格中，并在表格视图中显示为`[Table]`。然后，我们可以使用列标题上的展开功能，将表格展开为单独的列。

![](../Images/a2f6d83805e7ca0cf3ab9814c6746ac0.png)

图 11 — 展开列。图片来自作者。

结果有效地将两个 csv 文件合并为一个单独的表格，然后我们可以像之前一样继续创建我们的特征。

这个方法仍然有一些局限性，尽管我们减少了重复查询的数量，但我们仍然需要为常规赛和锦标赛数据集分别复制所有内容。这里就是全代码方法的作用。

# 全代码方法

全代码方法是最灵活和最强大的方法，但也需要编写最多的代码。这个方法最适合那些熟悉编写代码的人，他们希望完全控制应用于数据的转换。

本质上，我们会提取每个查询中生成的所有 M 代码，并将它们合并成一个单一的查询。这将允许我们在一个查询中加载所有 CSV 文件，然后在一个步骤中对每个文件应用转换。要获取所有的 M 代码，我们可以选择每个查询，然后点击主页功能区中的高级编辑器，这样就会显示出该查询生成的所有 M 代码。然后，我们可以将这些代码复制并粘贴到新的查询中，并将它们全部合并。

为此，我们需要创建一个新的空白查询，然后输入以下代码：

```py
(TourneyType as text) => let
  Source = Lakehouse.Contents(null){[workspaceId = "..."]}[Data]{[lakehouseId = "..."]}[Data],
  #"Navigation 1" = Source{[Id = "Files", ItemKind = "Folder"]}[Data],
  #"Filtered rows" = Table.SelectRows(#"Navigation 1", each Text.Contains([Name], TourneyType)),
  #"Invoked custom function" = Table.AddColumn(#"Filtered rows", "Invoked custom function", each LoadCSV([Content])),
  #"Removed columns" = Table.RemoveColumns(#"Invoked custom function", {"Content", "Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path", "ItemKind", "IsLeaf"}),
  #"Expanded Invoked custom function" = Table.ExpandTableColumn(#"Removed columns", "Invoked custom function", {"Season", "DayNum", "WTeamID", "WScore", "LTeamID", "LScore", "WLoc", "NumOT", "WFGM", "WFGA", "WFGM3", "WFGA3", "WFTM", "WFTA", "WOR", "WDR", "WAst", "WTO", "WStl", "WBlk", "WPF", "LFGM", "LFGA", "LFGM3", "LFGA3", "LFTM", "LFTA", "LOR", "LDR", "LAst", "LTO", "LStl", "LBlk", "LPF"}, {"Season", "DayNum", "WTeamID", "WScore", "LTeamID", "LScore", "WLoc", "NumOT", "WFGM", "WFGA", "WFGM3", "WFGA3", "WFTM", "WFTA", "WOR", "WDR", "WAst", "WTO", "WStl", "WBlk", "WPF", "LFGM", "LFGA", "LFGM3", "LFGA3", "LFTM", "LFTA", "LOR", "LDR", "LAst", "LTO", "LStl", "LBlk", "LPF"}),
  #"Renamed columns" = Table.RenameColumns(#"Expanded Invoked custom function", {{"WLoc", "location"}}),
  Custom = Table.ReplaceValue(#"Renamed columns", each [location], each if Text.Contains([location], "H") then "1" else if Text.Contains([location], "A") then "-1" else "0", Replacer.ReplaceText, {"location"}),
  #"Change Types" = Table.TransformColumnTypes(Custom, {{"Season", Int64.Type}, {"DayNum", Int64.Type}, {"WTeamID", Int64.Type}, {"WScore", Int64.Type}, {"LTeamID", Int64.Type}, {"LScore", Int64.Type}, {"location", Int64.Type}, {"NumOT", Int64.Type}, {"WFGM", Int64.Type}, {"WFGA", Int64.Type}, {"WFGM3", Int64.Type}, {"WFGA3", Int64.Type}, {"WFTM", Int64.Type}, {"WFTA", Int64.Type}, {"WOR", Int64.Type}, {"WDR", Int64.Type}, {"WAst", Int64.Type}, {"WTO", Int64.Type}, {"WStl", Int64.Type}, {"WBlk", Int64.Type}, {"WPF", Int64.Type}, {"LFGM", Int64.Type}, {"LFGA", Int64.Type}, {"LFGM3", Int64.Type}, {"LFGA3", Int64.Type}, {"LFTM", Int64.Type}, {"LFTA", Int64.Type}, {"LOR", Int64.Type}, {"LDR", Int64.Type}, {"LAst", Int64.Type}, {"LTO", Int64.Type}, {"LStl", Int64.Type}, {"LBlk", Int64.Type}, {"LPF", Int64.Type}}),
  Winners = Table.TransformColumnNames(#"Change Types", each if Text.StartsWith(_, "W") then Text.Replace(_, "W", "T1_") else Text.Replace(_, "L", "T2_")),
  #"Rename L" = Table.TransformColumnNames(#"Change Types", each if Text.StartsWith(_, "W") then Text.Replace(_, "W", "T2_") else Text.Replace(_, "L", "T1_")),
  #"Replaced Value L" = Table.ReplaceValue(#"Rename L", each [location], each if [location] = 1 then -1 else if Text.Contains([location], -1) then 1 else [location], Replacer.ReplaceValue, {"location"}),
  Losers = Table.TransformColumnTypes(#"Replaced Value L", {{"location", Int64.Type}}),
  Combined = Table.Combine({Winners, Losers}),
  PointDiff = Table.AddColumn(Combined, "PointDiff", each [T1_Score] - [T2_Score], Int64.Type)
in
  PointDiff
```

*注意：Lakehouse 连接值已被移除*

这里发生的事情是，我们：

1.  从 Lakehouse 加载数据；

1.  过滤行，仅包括与 `TourneyType` 参数匹配的 CSV 文件；

1.  将 CSV 文件加载到表格中；

1.  将表格扩展为列；

1.  重命名列；

1.  更改数据类型；

1.  将两张表重新合并；

1.  计算两支队伍之间的得分差。

使用查询非常简单，只需选择它，然后使用 `TourneyType` 参数调用函数。

![](../Images/6fb312c0bced0e91a3e7141022d951e5.png)

图 12 — 调用函数。图片由作者提供。

这将创建一个新的查询，使用函数作为源，并加载并转换数据。然后，只需将数据作为新表加载到 Lakehouse 中即可。

![](../Images/91ea5a15eb7ff19de63185d8fee0d1a8.png)

图 13 — 函数加载。图片由作者提供。

如你所见，`LoadTournamentData` 函数使用了参数“RegularSeasonDetailedResults”，该参数会将男子和女子常规赛的比赛数据加载到同一个表格中。

# 结论

就这样！

希望这篇文章能为你提供如何使用 Dataflow Gen2 准备数据并为机器学习模型创建特征的概述。其低代码方法使得快速创建数据管道变得非常简单，并且包含了许多强大的功能，可以用于创建复杂的转换。对于需要转换数据的人来说，这是一个很好的起点，更重要的是，它的好处在于不需要编写容易出错、难以测试且难以维护的复杂代码。

在撰写本文时，Dataflows Gen2 尚不支持 Git 集成，因此无法对数据流进行版本控制或共享。预计此功能将在[2024年第四季度发布](https://learn.microsoft.com/en-us/fabric/release-plan/data-factory#git-df)。

*最初发布于* [*https://nobledynamic.com*](https://nobledynamic.com/posts/fabric-madness-3/) *2024年4月15日。*
