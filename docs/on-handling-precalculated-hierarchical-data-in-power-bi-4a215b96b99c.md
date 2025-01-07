# 在Power BI中处理预计算层次数据

> 原文：[https://towardsdatascience.com/on-handling-precalculated-hierarchical-data-in-power-bi-4a215b96b99c?source=collection_archive---------12-----------------------#2024-05-03](https://towardsdatascience.com/on-handling-precalculated-hierarchical-data-in-power-bi-4a215b96b99c?source=collection_archive---------12-----------------------#2024-05-03)

## **虽然层次结构是数据中的常见概念，但一些来源以不寻常的格式提供数据。通常，我们在最低层级获取值。但当我们得到预先聚合的值时，会发生什么呢？在这里，我将深入探讨这个话题。**

[](https://medium.com/@salvatorecagliari?source=post_page---byline--4a215b96b99c--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--4a215b96b99c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4a215b96b99c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4a215b96b99c--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--4a215b96b99c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4a215b96b99c--------------------------------) ·8分钟阅读·2024年5月3日

--

![](../Images/45a1401ef7a0f5ec3fdf498000e02d6b.png)

照片由[ThisisEngineering](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍和数据

让我们设定一个场景：我们有一个包含行政费用的组织。

费用可以发生在国家、州和商店级别。

请看以下表格：

![](../Images/dce67aeea8c01e4ae3050904b2f33e34.png)

图1 — 数据在预期位置的值（图源：作者）

我们看到两行分别是两家商店的费用，一行是南卡罗来纳州的组织费用。

我可以使用这些数据来计算费用的总和，并得出南卡罗来纳州所有商店的总费用。

但是，当源系统以不同的形式提供数据时，怎么办？

例如，像这样：

![](../Images/ddddf77439bedfee5575a5da2680a495.png)

图2 — 南卡罗来纳州预先聚合的值数据（图源：作者）

第三行包含了南卡罗来纳州两家商店的预先聚合的总和，以及南卡罗来纳州的组织费用。

简单地将这三行相加会得到错误的结果，因为结果中会重复计算这两家商店的费用：

![](../Images/134946f9f906a1cabb2f97d570761f53.png)

图3 — 聚合包含预先聚合值的数据时的错误结果（图源：作者）

挑战是：如何计算每个层级中的正确结果？

我的解决方案方法必须考虑以下几点：

+   我不能更改数据源中的数据。

+   我必须在数据模型中添加一些计算以纠正结果。

+   我必须在层级的每个级别执行不同的计算。

但是我在哪里以及如何进行操作呢？

我有三种方法可以解决这个问题：

+   添加一个计算列来获得正确的结果。

+   添加一个度量值来计算正确的结果。

+   使用可视化计算。

# 计算列

好的，让我们开始添加一些计算列。

首先，我需要知道每一行在层级中的级别。为此，我需要一个名为“路径长度”的列。这样的列通常用于处理父子层级。

因此，我添加了两列新列，以便更好地导航层级：

![](../Images/b632fd803b5bd49043bcd3f945b5a8c1.png)

图 4 — 用于层级导航的额外计算列（作者提供的图）

我使用了以下表达式来计算 HierachyPath 列：

```py
HierarchyPath =
'Cost Data'[Country]
    & IF (
        'Cost Data'[State] <> 'Cost Data'[Country],
        "|" & 'Cost Data'[Country]
    )
    & IF (
        'Cost Data'[Store] <> 'Cost Data'[State],
        "|" & 'Cost Data'[Store]
    )
```

然后，我使用了[PATHLENGHTH()](https://dax.guide/pathlength/)函数来计算“路径长度”列：

```py
Path Length = PATHLENGTH('Cost Data'[HierarchyPath])
```

接下来，我可以编写一个表达式，执行以下步骤来处理表中的每一行：

1.  获取当前职位的值。

1.  获取当前职位在层级中下方的值的总和。

1.  从第 2 步的总和中扣除当前行中的值。

结果是一个包含上面第一张图片中值的列。

```py
Corrected Expenses = 
VAR CurrentExp = 'Cost Data'[Expenses]
VAR CurrentLevel = 'Cost Data'[Path Length]
VAR CurrentPath = 'Cost Data'[HierarchyPath]

VAR ChildExpenses = 
            CALCULATE(SUM('Cost Data'[Expenses])
                        ,REMOVEFILTERS('Cost Data')
                        ,'Cost Data'[Path Length] = CurrentLevel + 1
                        ,CONTAINSSTRING('Cost Data'[HierarchyPath], CurrentPath)
            )

RETURN
    CurrentExp - ChildExpenses
```

关键在于“ChildExpenses”变量的表达式。该表达式计算了当前职位下、同一父级下的所有行的总和。

请注意，在 Power BI 中调用[CALCULATE()](https://dax.guide/calculate/)函数计算一个计算列时，会触发上下文转换。

如果你不熟悉上下文转换的概念，确保阅读我解释它的文章：

[](/whats-fancy-about-context-transition-in-dax-efb5d5bc4c01?source=post_page-----4a215b96b99c--------------------------------) [## DAX 中上下文转换的精彩之处

### 行上下文和筛选上下文是 DAX 中的常见概念。但我们可以通过上下文转换在这两者之间切换。

towardsdatascience.com](/whats-fancy-about-context-transition-in-dax-efb5d5bc4c01?source=post_page-----4a215b96b99c--------------------------------)

这是该列的结果：

![](../Images/27ec7282310a6d995e3c4357a76feb3a.png)

图 5 — 计算列的结果以获得正确的结果（作者提供的图）

这列替代了原始的 Expenses 列。

我将原始的 Expenses 列重命名为“Expense_Original”，并将计算列重命名为“Expenses”。由于 Expense_Original 列对报告没有用处，因此它在数据模型中是隐藏的。

现在，我可以直观地创建报告了：

![](../Images/e76f5adc863aa25672ceb98b9dcdfcd4.png)

图 6 — Power BI 中重命名的原始 Expenses 列和计算列并排显示（作者提供的图）

这是所需的结果。

但让我们看看我是否能创建一个度量值来计算正确的结果。

# 度量值

要编写一个度量值，我必须分别处理每个层级。

我不能使用与计算列相同的方法，因为在每个上层级（如国家或州）下，商店级别有多行数据。

结果是以下的 DAX 代码：

```py
Expenses (Corrected) = 
VAR CurrentExp = [Expenses (Original)]
VAR CurrentLevel = SELECTEDVALUE('Cost Data'[Path Length])
VAR CurrentPath = SELECTEDVALUE('Cost Data'[HierarchyPath])

VAR CurrentCountry = SELECTEDVALUE('Cost Data'[Country])
VAR CurrentState = SELECTEDVALUE('Cost Data'[State])
VAR CurrentStore = SELECTEDVALUE('Cost Data'[Store])

VAR StateExpenses =
             -- Get the pre-aggregated value of the Expenses for the State
            CALCULATE([Expenses (Original)]
                        ,REMOVEFILTERS('Cost Data')
                        ,'Cost Data'[Path Length] = CurrentLevel + 1
                        ,CONTAINSSTRING('Cost Data'[HierarchyPath], CurrentPath)
            )

RETURN
    SWITCH(TRUE()
        -- Calculation at the lowest level (Store)
        -- But only when the Store has a different name than the State
        ,NOT ISBLANK(CurrentStore) && CurrentStore <> CurrentState
            ,CurrentExp
        -- Detract the Expenses from the sum at the State level when the "Store" has the same name as the State
        -- These are the rows with the Expenses for the State
        ,NOT ISBLANK(CurrentStore) && CurrentStore = CurrentState
            ,CurrentExp - StateExpenses
        -- Calculate the Sum at the state level
        ,NOT ISBLANK(CurrentState) && ISBLANK(CurrentStore)
            -- First, calculate the Sum for all Stores
            -- But only when the Stores have a different name than the State
            ,CALCULATE([Expenses (Original)]
                        ,REMOVEFILTERS('Cost Data')
                        ,'Cost Data'[Country] = CurrentCountry
                        ,'Cost Data'[State] = CurrentState
                        ,'Cost Data'[Store] <> CurrentState
                        )
                    -- At this stage, each row in the Visual has multiple Data rows.
                    -- Therefore, SELECTEDVALUE() for the path doesn't return any value.

                    -- Now add the sum for all Stores, detracting the duplicate value for the "Stores" with the same name as the State
                        +
                        (
                        CALCULATE([Expenses (Original)]
                                    ,REMOVEFILTERS('Cost Data')
                                    ,'Cost Data'[Country] = CurrentCountry
                                    ,'Cost Data'[State] = CurrentState
                                    ,'Cost Data'[Store] = CurrentState
                                )
                        -
                        CALCULATE([Expenses (Original)]
                                    ,REMOVEFILTERS('Cost Data')
                                    ,'Cost Data'[Country] = CurrentCountry
                                    ,'Cost Data'[State] = CurrentState
                                    ,'Cost Data'[Store] <> CurrentState
                                    )
                        )
            -- Calculate the corrected Sum for the Country
            -- Must use the same logic as above, but by moving one level above, considering only the Country and the State
            ,CALCULATE([Expenses (Original)]
                        ,REMOVEFILTERS('Cost Data')
                        ,'Cost Data'[Country] = CurrentCountry
                        ,'Cost Data'[State] <> CurrentCountry
                        ) + 
                        (
                        CALCULATE([Expenses (Original)]
                                    ,REMOVEFILTERS('Cost Data')
                                    ,'Cost Data'[Country] = CurrentCountry
                                    ,'Cost Data'[State] = CurrentCountry
                                )
                        -
                        CALCULATE([Expenses (Original)]
                                    ,REMOVEFILTERS('Cost Data')
                                    ,'Cost Data'[Country] = CurrentCountry
                                    ,'Cost Data'[State] <> CurrentCountry
                                    )
                        )
    )
```

我在代码中添加了大量的注释。

因此，我不会详细解释度量值的每一步。

然而，这种方法非常复杂，无法与使用计算列的方法的简便性相比。

# 可视化计算

最后，我可以使用 Power BI 中的最新功能之一：[可视化计算](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-visual-calculations-overview)。

可视化计算可以直接在视觉效果中添加计算，而无需将度量值添加到数据模型中。

这为我们提供了一些激动人心的可能性，并且消除了为满足特定视觉效果需求而编写度量值的必要。

我在下面的参考部分添加了一些关于这个话题的链接。

在这里，我尝试使用这个新功能来实现一个简单的解决方案。

然而，在进行了大量的研究和反复试验后，我仍然没有找到一个有效的解决方案。

我找到了解决方案来计算每个商店的正确结果，但对于州和国家的计算则没有成功：

```py
Visual calculation =
VAR CurrentCountry = [Country]
VAR CurrentState = [State]

RETURN
  SWITCH(TRUE()
        ,[State] <> [Store] && ISATLEVEL([Store])
          ,[Expenses (Original)]
        ,[State] = [Store] && ISATLEVEL([Store])
          ,[Expenses (Original)] -
                CALCULATE(SUM([Expenses (Original)])
                          ,[State] <> [Store]
                          ,[Country] = CurrentCountry
                          ,[State] = CurrentState
                          )
          )
```

这是这个公式的结果：

![](../Images/07e9815be2b0f5a70546481d7198baa0.png)

图 7 — 可视化计算的结果（图由作者提供）

我尝试找出计算州和国家结果的解决方案，但没有成功。

一个关键细节是，我们使用这种方法时不会得到总计。这可能是由于公式未完成的原因，但这是一个重要的细节。

尽管在这个特定场景中没有成功，我还是会将这个新功能纳入我的技能范围。

我鼓励你关注这个新的激动人心的功能。它为找到计算特定于某个视觉效果的结果提供了新的可能性，这些结果不会在其他地方重用。

[Amit Chandak](https://medium.com/u/fff358c001e0?source=post_page---user_mention--4a215b96b99c--------------------------------) 写了关于这个话题的介绍：

[](https://medium.com/microsoft-power-bi/understanding-visual-calculations-in-power-bi-revolutionizing-data-analysis-1c15c943243e?source=post_page-----4a215b96b99c--------------------------------) [## 在 Power BI 中理解可视化计算：数据分析的革命

### 在 2024 年 2 月，Power BI 推出了一个正在预览中的具有颠覆性意义的功能：可视化计算。这个…

medium.com](https://medium.com/microsoft-power-bi/understanding-visual-calculations-in-power-bi-revolutionizing-data-analysis-1c15c943243e?source=post_page-----4a215b96b99c--------------------------------)

# 结论

当我开始写这篇文章时，我打算为你提供三个有效的解决方案来解决这个问题。

我找到了两种可行的解决方案。第一种是最直接且高效的，而第二种则是一个很好的操作层次结构的练习。

一段时间前，我写了一篇简短的文章，讲解了为什么预聚合数据对我们不利：

[](/pre-calculated-aggregations-for-power-bi-why-should-you-avoid-them-371abdec5bb4?source=post_page-----4a215b96b99c--------------------------------) [## Power BI 的预计算聚合—为什么你应该避免它们

### 我的一个客户总是希望在他的 Excel 文件中为报告预计算聚合。以下是我们应该避免的原因...

towardsdatascience.com](/pre-calculated-aggregations-for-power-bi-why-should-you-avoid-them-371abdec5bb4?source=post_page-----4a215b96b99c--------------------------------)

现在，我有一个更多的例子，证明我之前写的那句话是对的。

一个重要的收获是，准备和格式化数据以支持简易的解决方案至关重要，即使这意味着要额外付出努力去寻找解决方案。

# 参考文献

下面是一些关于新可视化计算功能的参考资料：

[MS Power 博客上的可视化计算（预览）（2024年2月）](https://powerbi.microsoft.com/en-us/blog/visual-calculations-preview/)

[使用可视化计算（预览）](https://learn.microsoft.com/en-us/power-bi/transform-model/desktop-visual-calculations-overview)

[SQLBI 文章页面（包含多个相关主题的文章，更多内容敬请期待）](https://www.sqlbi.com/articles/)

我使用了 Contoso 示例数据集，就像在我之前的文章中一样。你可以从微软[这里](https://www.microsoft.com/en-us/download/details.aspx?id=18279)免费下载 ContosoRetailDW 数据集。

Contoso 数据可以在 MIT 许可证下自由使用，具体请参见[这里](https://github.com/microsoft/Power-BI-Embedded-Contoso-Sales-Demo)。

我提取了数据的一个子集，并进行了处理，以获得所需的数据。

请考虑关注我并订阅，以便在我添加新内容时立即收到电子邮件：

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----4a215b96b99c--------------------------------) [## 每当 Salvatore Cagliari 发布新内容时，收到电子邮件通知。

### 每当 Salvatore Cagliari 发布新内容时，收到电子邮件通知。通过注册，你将创建一个 Medium 账户，如果你还没有的话...

medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----4a215b96b99c--------------------------------)

我将我的文章免费开放给每个人，尽管 Medium 有付费墙。这允许我从每个读者那里赚取一点收入，但我关闭了付费墙，以便你能免费阅读我的文章。

你可以通过以下方式支持我的工作，这是我在空闲时间进行的工作：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或扫描此二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都非常感谢，这能帮助我腾出更多时间为你创造更多内容。

非常感谢。
