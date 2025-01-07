# 如何在 Power BI 中操作总计

> 原文：[https://towardsdatascience.com/tablehow-to-manipulate-the-total-in-power-bi-b55e2d07207d?source=collection_archive---------10-----------------------#2024-07-16](https://towardsdatascience.com/tablehow-to-manipulate-the-total-in-power-bi-b55e2d07207d?source=collection_archive---------10-----------------------#2024-07-16)

## *在大多数情况下，总计会将可视化中的详细行汇总，比如在表格中。但是，如果总计应该显示不同的内容呢？我将在这里详细讨论这个挑战。*

[](https://medium.com/@salvatorecagliari?source=post_page---byline--b55e2d07207d--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--b55e2d07207d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b55e2d07207d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b55e2d07207d--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--b55e2d07207d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b55e2d07207d--------------------------------) ·8分钟阅读·2024年7月16日

--

![](../Images/7cfa6e5823f1e78eedc322869996e772.png)

图片来源：[UX Indonesia](https://unsplash.com/@uxindo?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

首先，让我们看一下这个报告页面：

![](../Images/e528cf14f8fb8e78d2c5f4c2511c8887.png)

图1 — 基础报告，包含求和和平均值度量（图由作者提供）

这个矩阵可视化没有什么特别之处。

现在，假设我想计算总计列和行中不同的销售平均值。

例如，一个加权平均数。

我不会深入探讨如何计算加权平均数，因为我已经写过一篇相关文章。我将在下面的参考文献部分添加一个链接。

为了做到这一点，我必须首先理解总计是如何计算的。然后，我可以开始操作它。

所以，让我们开始吧。

# 过滤上下文

即使你已经熟悉 DAX 中的过滤上下文，我还是建议你阅读这一部分，因为我将定义一些稍后使用的术语。

好的，让我们回到上面展示的矩阵可视化。

例如，“Contoso Paris Store”和“Economy”产品类别的度量值是如何计算的？

我选择任意单元格来解释它们的详细情况：

![](../Images/7df314d48e48d773c55c880d44ed87da.png)

图2 — 基础报告，突出显示的单元格（图由作者提供）

标记单元格的结果是在应用以下过滤器以定义过滤上下文后计算得出的。

+   状态：塞纳省（巴黎）

+   城市：巴黎

+   商店：Contoso巴黎店

+   产品类别：经济型

+   年份：2024

+   月份：四月

+   大陆：欧洲

+   国家：法国

+   制造商：Proseware Inc.

所有这些过滤器都会影响这些特定单元格的结果。

当我们将矩阵“内部”过滤器与周围切片器的过滤器分开时，我们可以定义“内部”和“外部”过滤器：

![](../Images/9ce6c3f8c73d3cefdf9ca2c8fe309e27.png)

图3 — 带有内部和外部过滤器的基础报告（图由作者提供）

你可以看到内部过滤器用红色箭头标记，外部过滤器用蓝色箭头标记。

过滤器来自何处对度量值没有影响。然而，我们需要理解这两种过滤器源之间的区别，因为在大多数情况下，只有在计算总计时，我们才能对“内部”过滤器的变化做出反应。

那么，如何处理总计的过滤上下文呢？

在继续之前，考虑一下。

.

.

.

.

行和列总计是没有行和列过滤器时度量的计算结果。

总计结果来自所有“内部”过滤器的缺失。

所有外部过滤器仍然处于激活状态。

因此，我们可以检查是否应用了过滤器来操控总计。

# 概念验证

为了证明这一说法，我将度量更改为以下代码：

```py
Avg Retail Sales = 1
```

这个度量的结果将始终为1，无论过滤上下文如何，因为它没有使用过滤器。

实际上，这正是我们得到的结果：

![](../Images/2c4ec056fb034abaa5543bdc6bc2fc04.png)

图4 — 操作后的度量结果（图由作者提供）

如果Power BI通过对可视化中的值求和来计算总计，结果将会非常不同。

由于度量的表达式中没有使用过滤上下文，因此始终返回1，无论是否有销售。

记住这一点，当你稍后查看结果时。

# 可能的解决方案

要操控这个可视化中的总计结果，我必须检查地理层级和产品类别的过滤器。

我可以使用[ISFILTERED()](https://dax.guide/isfiltered/)函数来实现这一点。

例如，当我想得到列总计为1，行总计为2时：

```py
Avg Retail Sales =
SWITCH(TRUE()
    ,**NOT** ISFILTERED('Geography' [StateProvinceName]), 1
    ,**NOT** ISFILTERED('Product'[ClassName]), 2
    ,AVERAGEX('Retail Sales', 'Retail Sales'[UnitPrice]*'Retail Sales'[SalesQuantity])
    )
```

这个度量执行以下检查：

1.  如果列“State”没有被过滤，位置是列总计，我希望它为1。

1.  如果产品类别没有被过滤，位置是行总计，我希望它为

1.  如果两者都被过滤，我就在矩阵内部（而不是在总计中），并且希望得到平均零售销售额。

这是结果：

![](../Images/76b41ab2c6006151016a55b23d05577a.png)

图5 — 操控总计的度量结果。我移除了销售总和以简化可视化（图由作者提供）

如你所见，这是一个非常特定于可视化的解决方案。

由于我有一个切片器在过滤大陆和国家，我无法检查“NOT ISFILTERED(‘Geography’)”，因为当我从地理切片器中选择某个项目时，地理表已经被过滤。

但是，如果我知道在报告中通常使用哪些表或列，我可以设计一个更通用的解决方案。

例如，假设产品类别层级和商店通常作为行类别使用，而产品类别、商店类型和颜色则作为列类别使用。

例如，像这样：

![](../Images/a9cc35f7bbda777c3ae5b6b6eab87ac7.png)

图 6 — 修改后的零售销售报告，按产品类别层级和商店类型分类（图由作者提供）

在这种情况下，我可以设计两个度量值来检查这些列和层级上是否存在现有过滤器，并在SWITCH()调用中使用它们。

这里是行过滤器：

```py
Row filter Check = 
ISFILTERED('Store')
||
ISFILTERED('Product'[ProductCategoryName])
||
ISFILTERED('Product'[ProductSubcategoryName])
||
ISFILTERED('Product'[ProductName])
```

对于列过滤器也是一样：

```py
Column filter check = 
ISFILTERED('Store'[StoreType])
||
ISFILTERED('Product'[ColorName])
||
ISFILTERED('Product'[ClassName])
```

如果我将这两个度量值添加到矩阵中，我可以看到结果：

![](../Images/b4eb587e5840bc69fe783e965968d582.png)

图 7 — 过滤器检查度量值的结果（图由作者提供）

“列过滤器检查”度量值按预期工作。它始终返回True，除了总计之外。

但是，“行过滤器检查”度量值并未按预期工作，因为即使是总计它也返回True。

原因是列“商店类型”也来自商店表。

在检查过滤器时，我必须修改度量值，以去除该列上的任何过滤器：

```py
Row filter Check = 
CALCULATE(
    ISFILTERED('Store')
    ,REMOVEFILTERS('Store'[StoreType])
    )
||
ISFILTERED('Product'[ProductCategoryName])
||
ISFILTERED('Product'[ProductSubcategoryName])
||
ISFILTERED('Product'[ProductName])
```

现在，结果如预期所示：

![](../Images/b4eb587e5840bc69fe783e965968d582.png)

图 8 — 过滤器检查度量值的正确结果（图由作者提供）

最后，我可以将度量值更改为使用这两个检查度量值来计算结果：

```py
Avg Retail Sales =
SWITCH(TRUE()
    ,NOT [Row filter check], 1
    ,NOT [Column filter Check], 2
    ,AVERAGEX('Retail Sales', 'Retail Sales'[UnitPrice]*'Retail Sales'[SalesQuantity])
    )
```

结果如预期所示：

![](../Images/2f8209f9f363a9effc95277aadf42364.png)

图 9 — 使用目标度量值的结果（图由作者提供）

逻辑稍显复杂，因为我必须从列和行总计的角度考虑。这对于不习惯的人来说有些困惑。

下一步是插入返回处理结果的度量值，这个结果不同于简单的聚合。

我甚至可以选择以不同的方式操作总计，而不是行和列总计：

![](../Images/a85f933d6ea99766c93ffccb8fbd9564.png)

图 10 — 操作总计的DAX表达式和结果（图由作者提供）

由于SWITCH()逐一评估检查，并返回第一个符合条件的表达式的给定结果，我必须注意每个变体检查的顺序。

# 最后一个小的调整

我可以为那些不喜欢在检查为假时执行某些操作的人提供一个小的调整。

上面的度量值检查表达式 = FALSE。

我可以将检查度量值更改为以下内容。然后，我可以避免在度量值中使用NOT：

```py
Column filter Check =
IF(
    ISFILTERED('Store'[StoreType])
    ||
    ISFILTERED('Product'[ColorName])
    ||
    ISFILTERED('Product'[ClassName])
    ,FALSE()
    ,TRUE()
)
```

并且

```py
Row filter Check = 
IF(
    CALCULATE(
        ISFILTERED('Store')
        ,REMOVEFILTERS('Store'[StoreType])
        )
    ||
    ISFILTERED('Product'[ProductCategoryName])
    ||
    ISFILTERED('Product'[ProductSubcategoryName])
    ||
    ISFILTERED('Product'[ProductName])
    ,FALSE()
    ,TRUE()
)
```

现在我可以去掉度量值中的NOT反转：

```py
Avg Retail Sales =
SWITCH(TRUE()
    ,[Row filter check] && [Column filter Check], 3
    ,[Row filter check], 1
    ,[Column filter Check], 2
    ,AVERAGEX('Retail Sales', 'Retail Sales'[UnitPrice]*'Retail Sales'[SalesQuantity])
    )
```

这种方法比上述方法更直观。

# 结论

有时，我遇到必须操作总计结果的情况。

在这种情况下，了解Power BI的工作原理、过滤器上下文的应用以及总计的计算方式至关重要。

我尝试解释在查看矩阵时，过滤上下文是如何工作的。

但对于任何其他可视化也是一样的，它们可以使用类别来获取我们数据中的详细信息。

我写了一篇关于 DAX 中可用函数来探索过滤上下文的文章。

我强烈建议你也阅读它，因为这可能会为你提供更多的工具来正确检查过滤上下文：

[](/exploring-the-filter-context-with-dax-functions-422211c1118e?source=post_page-----b55e2d07207d--------------------------------) [## 使用 DAX 函数探索过滤上下文

### 在查看当前过滤上下文时，DAX 中有几个有用的函数。以下是一些例子：

towardsdatascience.com](/exploring-the-filter-context-with-dax-functions-422211c1118e?source=post_page-----b55e2d07207d--------------------------------)

然而，一个重要的经验是避免过于特定的度量值，这些度量值只能用于一个可视化。相反，跳出框框思考，构建更通用和可重用的代码。

这肯定会对你未来的工作有所帮助。

![](../Images/77b7b5481f36750525919e296f813f9f.png)

图片由 [krakenimages](https://unsplash.com/@krakenimages?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 参考文献

这里是最初提到的关于计算加权平均值的文章链接：

[](/to-weigh-or-not-to-weigh-this-is-the-average-question-ece33fad9180?source=post_page-----b55e2d07207d--------------------------------) [## 称重还是不称重——这是一个平均值的问题

### 平均值是一个简单的计算。但有时，背后还有更多可以探索的内容。让我们来看看这个被低估的话题。

towardsdatascience.com](/to-weigh-or-not-to-weigh-this-is-the-average-question-ece33fad9180?source=post_page-----b55e2d07207d--------------------------------)

和我之前的文章一样，我使用了 Contoso 示例数据集。你可以从微软的[这里](https://www.microsoft.com/en-us/download/details.aspx?id=18279)免费下载 ContosoRetailDW 数据集。

我将客户的请求转化为 ContosoRetailDW 数据集中的数据。

Contoso 数据可以在 MIT 许可证下自由使用，详情请见[这里](https://github.com/microsoft/Power-BI-Embedded-Contoso-Sales-Demo)。

我扩大了数据集，以便让 DAX 引擎更加高效地工作。

在线销售表包含 7100 万行（而不是 1260 万行），零售销售表包含 1850 万行（而不是 340 万行）。

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----b55e2d07207d--------------------------------) [## 每当 Salvatore Cagliari 发布时获取电子邮件通知。

### 每当 Salvatore Cagliari 发布时获取电子邮件通知。通过注册，你将创建一个 Medium 账户，如果你还没有的话……

medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----b55e2d07207d--------------------------------)

尽管Medium有付费墙，我依然让我的文章对所有人开放。这让我能从每位读者那里赚取一些收入，但我关闭了付费墙，因此你可以免费阅读我的作品。

你可以通过以下方式支持我的工作，这些工作是我在空闲时间进行的：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或者扫描此二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都将不胜感激，并帮助我找到更多时间为你创作更多内容。

非常感谢。
