# 探索在Power BI中使用RLS实现数据安全性的所有方法

> 原文：[https://towardsdatascience.com/explore-all-ways-to-implement-rls-rules-ee4a402db39a?source=collection_archive---------4-----------------------#2024-04-03](https://towardsdatascience.com/explore-all-ways-to-implement-rls-rules-ee4a402db39a?source=collection_archive---------4-----------------------#2024-04-03)

## *在Power BI中实施行级安全性是开发人员的常见任务。我们使用各种技术来实现这一目标。让我们看看其中的一些方法。*

[](https://medium.com/@salvatorecagliari?source=post_page---byline--ee4a402db39a--------------------------------)[![Salvatore Cagliari](../Images/a24b0cefab6e707cfee06cde9e857559.png)](https://medium.com/@salvatorecagliari?source=post_page---byline--ee4a402db39a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ee4a402db39a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ee4a402db39a--------------------------------) [Salvatore Cagliari](https://medium.com/@salvatorecagliari?source=post_page---byline--ee4a402db39a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ee4a402db39a--------------------------------) ·12分钟阅读·2024年4月3日

--

![](../Images/60d3dfeaaf055fa3c953ebabf3927744.png)

图片来源：[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

当我们在Power BI解决方案中调节数据访问时，必须实现RLS（行级安全性）。

RLS通过实施RLS角色来工作，这些角色包含用于控制数据访问的访问逻辑。

这个逻辑是由DAX表达式定义的，可以非常简单，也可以非常复杂。

由于我已经在Medium上写过几篇关于这个主题的文章，所以我决定将不同的方法汇总到一个指南中，而不是让你在不同的地方查找信息。

最后，我会将它们并排分析，并推荐最好的方法。

如果有其他相关内容，我会参考并链接到它们。你可以在本文末尾的参考文献部分找到相关链接。

# 变体

我们有以下几种变体来实现RLS：

+   简单查找表

+   使用层级结构

+   复杂的DAX表达式

+   奖励：使用SCD2维度

# 如何识别用户

如果你已经熟悉构建RLS角色，可以跳到下一部分。

每个RLS角色使用以下两种基本方法之一：

1.  识别用户

1.  应用访问逻辑

第一个方法是基于用户列表，将其映射到他们可以访问的数据。

因此，当你有一个包含用户列表的表（以邮件地址的形式），你可以使用[USERPRINCIPALNAME()](https://dax.guide/userprincipalname/)函数来比较当前用户。

以下度量使用这个函数来显示当前用户：

```py
Current User = USERPRINCIPALNAME()
```

现在，我可以将其添加到卡片可视化中，从而得到以下结果：

![](../Images/dc3c8b2adfe42e7d386e7529805def5c.png)

图 1 — 当前用户的简单度量结果（出于数据保护原因隐藏域）（图由作者提供）

我可以在 RLS 角色中使用这个函数来检查数据是否符合当前用户的要求。

这是第一种方法的基本原理。

另一种方法是使用 DAX 逻辑来实现访问控制逻辑。这个逻辑可以简单，也可以根据需要变得复杂。

稍后你将看到这个方法的两个示例。

# 简单的查找表

这是最简单的方法。

我需要一个用户列表，其中包含需要访问数据的用户的引用。

每个用户在我的数据模型中都有对数据子集的引用。

考虑以下包含销售渠道的表格：

![](../Images/01688dffc52de40b790b123fdb9b2481.png)

图 2 — 频道表，将通过查找表进行限制（图由作者提供）

我希望限制我的用户只能访问一个或多个定义的频道。

为了实现这一点，我需要一张包含频道用户映射的表格。

类似这样的情况：

![](../Images/e9c55d82669724e2e1f25ee24a4cb441.png)

图 3 — 频道的用户映射（图由作者提供）

我将这张表导入到 Power BI 文件中，并与 Channel 表建立关系。

但是，在这种情况下，我必须将两个表之间的默认关系更改为以下内容：

![](../Images/abc3e0b19edae5a895a7f5e1a26d529b.png)

图 4 — 频道表和 Channel-Accesslist 表之间的关系（图由作者提供）

这些设置是必要的，因为 Power BI 会创建一个多对一的关系，其中 Channel 表（单边）会过滤 Channel-Accesslist 表（多边）。

因此，我必须更改关系设置，以确保过滤器从 Channel-Accesslist 表传递到 Channel 表，并对其应用安全过滤器。

接下来，我必须为 Channel-Accesslist 表创建一个 RLS 角色：

![](../Images/661a4cd2079636128ad09b7c11fa2356.png)

图 5 — 为 Channel-Accesslist 创建 RLS 角色（图由作者提供）

不要忘记在关闭对话框之前点击“保存”按钮，以保存角色的添加。

这个 DAX 表达式必须返回 TRUE 或 FALSE，无论其复杂度如何。不允许返回结果集或值。

要测试 John Doe 的访问权限，我可以点击“以...查看”按钮，选择要测试的 RLS 角色，并输入邮件地址（即 Principalusername），以将所选角色应用于该用户：

![](../Images/dfd572293fe3eaf0646370646cc0f31f.png)

图 6 — 测试 John Doe 的 RLS 角色（图由作者提供）

当我测试访问权限时，我将看到 John Doe 的情况：

![](../Images/c144e774ef9fc2f52a3dd74b2172e1aa.png)

图 7 — John Doe 的 RLS 测试结果（图由作者提供）

这是预期的结果。

# 使用层级结构

使用 RLS 和层级结构是一个略微不同的故事。

让我们来看这个例子：

![](../Images/90942588cda99b3b13cf5d3b924b69c8.png)

图 8 — 带有产品层级的示例（图示由作者提供）

现在，假设我们有负责单一产品类别的销售人员。因此，他们只能看到分配给他们的子类别和产品。

为了设置权限，我们利用Power BI的功能，过滤表格中的一列，并交叉过滤所有其他列。

看一下下面的源数据库图片，它解释了层级结构的过滤传播（数据与Power BI中的相同，但我在Power BI中重命名了列）：

![](../Images/ba5528001ad02becc33bae1fe47a8ddb.png)

图 9 — 层级的过滤传播（图示由作者提供）

如你所见，当对类别列应用过滤器时，它会自动应用到定义层级的其他列。

因此，我们只需要在类别列上应用过滤器。

当然，这只适用于这种形式的层级结构。

如果你有父子层级结构，这就不再适用了。

你可以阅读这篇文章，获取有关如何解决这个挑战的提示：

[](https://medium.com/microsoft-power-bi/finding-managers-in-organizational-hierarchies-f3911a5f3e22?source=post_page-----ee4a402db39a--------------------------------) [## 在组织层级中找到管理者

### 组织层级结构是数据中最常见的层级之一。但找到管理者可能会很有挑战性……

[medium.com](https://medium.com/microsoft-power-bi/finding-managers-in-organizational-hierarchies-f3911a5f3e22?source=post_page-----ee4a402db39a--------------------------------)

然而，由于Power BI不支持父子层级结构，我们必须在任何情况下将其扁平化（从父子结构转换为面向列的结构），以便有意义地使用它们。我在本文末尾的参考文献部分添加了一个有用的文章链接，展示了如何做到这一点。

现在，回到扁平化的层级结构。

现在我们有两种可能性。

1.  创建一个类似于第一种方法的表格，将用户分配到每个类别。

1.  为每个类别创建角色，并将用户分配到该角色。

如前所述，第一种方法与上面相同。

因此，我将向你展示第二种方法。

和之前一样，我创建了一个RLS角色。但这次，表达式直接过滤了产品类别。

![](../Images/2cc0a6b0af93f937a7ddf9cd8ccf929c.png)

图 10 — 为“计算机”产品类别定义RLS角色（图示由作者提供）

当我测试RLS角色时，这次没有输入用户邮箱地址，得到的结果是：

![](../Images/321467d9ba7ee8ef1acc6ea79ca47473.png)

图 11 — 测试“计算机”类别的RLS角色结果（图示由作者提供）

再次，这就是预期的结果。

因此，我需要为每个产品类别创建一个RLS角色。

这允许我对用户访问进行隔离，或给一个用户多个产品类别的访问权限。

然而，由于可以向数据中添加新类别，我必须添加新的 RLS 角色以覆盖对这些类别的访问。没有新角色（或角色），新类别将对任何人不可见。

# 复杂的 DAX 表达式

每当访问规则太复杂，无法通过经典数据模型实现时，我就需要一种更复杂的方法来控制对数据的访问。

在这里，我们需要复杂的 DAX 表达式。

请查看此表格：

![](../Images/e0088284f119fe3264e977115deea301.png)

图 12 — 每个用户及其类别和品牌的访问列表（图示：作者提供）

每个列出的用户必须仅能访问分配的类别和品牌组合中的产品。

例如，John Doe 访问的是“电视和视频”和“计算机”类别的产品，但仅能访问“Contoso”和“Adventureworks”品牌。而 Sam Sample 只能访问“北风贸易公司”的“家电”类别，以及“Litware”和“Proseware”品牌，即使“家电”类别还有四个其他品牌。

由于 Power BI（像所有其他基于 Microsoft 产品的表格模型一样）不允许在多个列之间建立表之间的关系，我无法将此表集成到数据模型中并使用标准方法。

因此，我必须使用 DAX 创建一个 RLS 角色。

为了在两张表之间找到匹配的行，我使用 [LOOKUPVALUES()](https://dax.guide/lookupvalue/) 函数来对产品表应用 RLS 角色：

```py
NOT ISBLANK(
  LOOKUPVALUE('Accesslist by Category and Brand'[UserMailaddress]
                ,'Accesslist by Category and Brand'[Category]
                  ,'Product'[Category]
                ,'Accesslist by Category and Brand'[Brand]
                  ,'Product'[Brand]
                ,'Accesslist by Category and Brand'[UserMailaddress]
                  ,USERPRINCIPALNAME()
          )
  )
```

如上所述，我必须返回 TRUE 或 FALSE。因此，我使用 [NOT](https://dax.guide/op/not/) [ISBLANK()](https://dax.guide/isblank/) 来获得所需的结果。当我找到匹配的行时，我会得到一个非空结果，而 NOT ISBLANK() 返回 TRUE。

在使用 John Doe 测试 RLS 角色时，我得到了所需的结果：

![](../Images/f9f513b1cf0211202cdf24b77525ccbc.png)

图 13 — 对 John Doe 应用类别和品牌的 RLS 角色后的结果（图示：作者提供）

由于这是一个非常简单的表达式，我想找到另一种方法来做，以展示复杂表达式的可能性。

这是我为产品表设计的作为 RLS 角色的 DAX 表达式，展示了我的 DAX 技能：

```py
CONTAINS(
  -- Construct the table from the AccessList table
  CALCULATETABLE(
      SUMMARIZE('Accesslist by Category and Brand'
                ,'Accesslist by Category and Brand'[Category]
                ,'Accesslist by Category and Brand'[Brand]
                )

  -- Filter the table by the current User
  -- To get only the rows to which the User has access
    ,'Accesslist by Category and Brand'[UserMailaddress] = USERPRINCIPALNAME()
    )

  -- Compare the Rows from the AccessList table using COMBINE to the Product table
  -- TRUE is returned only when the values correspond
  ,'Accesslist by Category and Brand'[Category]
  ,'Product'[Category]
  ,'Accesslist by Category and Brand'[Brand]
  ,'Product'[Brand]
  )
```

这种方法展示了如何在 Power BI 中构建更复杂的表达式作为 RLS 角色，以及如何构造这些角色。

我使用我在文章中开发和测试 RLS 规则的方法（参考文献部分中的链接）来获得表达式的正确方法。

然后，我使用 DAX 查询中的方法来找到 RLS 编辑器的正确解决方案。

在我的方法中，我使用 [CONTAINS()](https://dax.guide/contains/) 函数来比较访问列表和产品表。

CONTAINS() 函数允许我比较来自两张表的多个列，并找到匹配的行。

如你在表达式中的注释所见，我从 AccessList 表中构建表格，同时使用 USERPRINCIPALNAME() 对其进行筛选，以便为 CONTAINS() 函数提供输入。

然后，我逐一比较这些列，找到当前用户的匹配行。

然而，两个 RLS 角色都会显著影响性能。

我观察到启用 RLS 角色后，执行时间比未启用时长了三倍。

这个规则应用于 Product 表的每一行，用以确定 Category 和 Brand 的组合是否被允许在结果集中。

好的，DAX 引擎的工作效率比这里解释的更高，但原则是正确的。

有什么更高效的替代方法吗？

例如，我可以添加计算列作为人工键，以便能够在这两个表之间建立关系：

![](../Images/cad7af1ae4f0dfb4f8aa4fb96362a6d9.png)

图 14 — 使用连接的 Category 和 Brand 的复合键（图示由作者提供）

我使用 Power Query 向两个表格（“Product” & “Accesslist by Category and Brand”）添加了一个计算列。现在我在两个表中都有一个关键列，可以用来建立关系：

![](../Images/b7a5d7be5928870eaaefd4d7e70985d8.png)

图 15 — 使用 CompositeKey 列的关系（图示由作者提供）

现在，我可以使用第一种方法（RLS 角色表达式：[UserMailaddress] = USERPRINCIPALNAME()）来实现访问控制。

三种方法的结果是相同的。但最后一种方法使用了一种更简单、更高效的方法。

无论如何，这里展示的示例是基于 Contoso 数据模型中的有限可能性。

你可能会在数据中遇到更复杂的情况，无法用第一种方法解决。此时，你必须开发一个 DAX 表达式来实现规则。

# 附加内容：使用 SCD2 维度

当我们访问经典数据仓库时，维度表很可能会被历史化。

假设你作为客户，在一个拥有多个门店的公司的数据库中注册，系统根据你的地理位置将你分配到某个门店。

随着时间推移，你可能会更改地址。这可能会改变你在地理位置上的分配。

在这种情况下，数据仓库中会存在两行数据：

+   一个带有旧地址的表

+   一个带有新地址的表

每一行都有一个有效期（Valid-From 和 -To）。

这意味着我们在定义 RLS 角色时必须格外小心，考虑哪些数据我们必须允许访问，哪些我们必须限制访问。

这是必要的，以确保正确的销售人员可以访问你数据的正确有效期。

我已经写了一篇关于这个主题的文章，邀请你阅读以了解更多：

[](/handling-historisations-in-power-bi-30dc43e9e6f?source=post_page-----ee4a402db39a--------------------------------) [## Power BI 中的历史化处理

### 在报告中，历史化是至关重要的。但除了常规的时间序列数据，我们还需要关注历史化……

[towardsdatascience.com](/handling-historisations-in-power-bi-30dc43e9e6f?source=post_page-----ee4a402db39a--------------------------------)

# 结论

我还没有提到的是，一旦你将Power BI文件发布到Power BI服务中，你必须将用户分配到RLS角色中，以确保规则能应用到他们身上。

阅读以下内容以获取更多指导：

[learn.microsoft.com](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-rls?source=post_page-----ee4a402db39a--------------------------------#working-with-members) [## Row-level security (RLS) with Power BI - Power BI

### 如何在Power BI服务中为导入的语义模型和DirectQuery配置行级安全性。

[learn.microsoft.com](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-rls?source=post_page-----ee4a402db39a--------------------------------#working-with-members)

正如你所看到的，最简单的方法是实现RLS角色的最有效方式。

在需要应用复杂规则的情况下，我尝试将它们转化为尽可能简单的访问列表。理想情况下，这个列表类似于第一种方法中展示的示例。

通过这种方式，我避免了在RLS角色中编写复杂的DAX表达式，从而避免了效率和性能的损失。

在我的一个项目中，我面临了将两个独立的表格与相同权限列表限制的挑战。

在这种情况下，我复制了包含用户/访问列表的表，并使用该列表对两张表进行过滤。

然后，我将相同的DAX表达式作为RLS角色添加到这两张表中，魔法就发生了。

我的一个同事曾经说过：“通过实现复杂或低效的RLS角色，我可以轻松让你的数据模型变慢。”

这一点非常正确，我们必须小心，不要被过于雄心勃勃的想法诱惑，陷入编写炫酷的DAX表达式来炫耀我们DAX能力的陷阱。

用户对于慢速报告不会感到非常感激。

实现RLS角色时的另一个注意事项：

SQLBI制作了一段关于在启用RLS时DAX的限制的有趣视频：

了解这些限制对于避免在编写DAX度量时出现错误信息或错误结果至关重要。

![](../Images/b05cf17aece3197f9296e2bca5b553e1.png)

图片由[Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 参考资料

微软关于Power BI行级安全性的文档：

[learn.microsoft.com](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-rls?source=post_page-----ee4a402db39a--------------------------------) [## Row-level security (RLS) with Power BI - Power BI

### 如何在Power BI服务中为导入的语义模型和DirectQuery配置行级安全性。

[learn.microsoft.com](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-rls?source=post_page-----ee4a402db39a--------------------------------)

为了将父子层级转换为经典层级，我遵循了数据莫扎特所描述的方法：

[](https://data-mozart.com/finding-the-right-path-understanding-parent-child-hierarchies-in-power-bi/?source=post_page-----ee4a402db39a--------------------------------) [## 寻找正确的路径 - 理解Power BI中的父子层级！ - Data Mozart

### 不同的数据源系统以不同的方式存储数据！父子层级可能是一个相当具有挑战性的问题…

data-mozart.com](https://data-mozart.com/finding-the-right-path-understanding-parent-child-hierarchies-in-power-bi/?source=post_page-----ee4a402db39a--------------------------------)

RADACAD的这篇文章解释了如何在组织层级中实现RLS：

[](https://radacad.com/dynamic-row-level-security-in-power-bi-with-organizational-hierarchy-and-multiple-positions-in-many-to-many-relationship-part-1?source=post_page-----ee4a402db39a--------------------------------) [## 在Power BI中使用组织层级和多位置实现动态行级安全性…

### 我之前写过关于动态行级安全性的文章，以及其中的一些模式。两种最常见的模式是…

radacad.com](https://radacad.com/dynamic-row-level-security-in-power-bi-with-organizational-hierarchy-and-multiple-positions-in-many-to-many-relationship-part-1?source=post_page-----ee4a402db39a--------------------------------)

这里是我写的所有关于实现RLS规则和其他相关主题的文章链接。

[](/handling-historisations-in-power-bi-30dc43e9e6f?source=post_page-----ee4a402db39a--------------------------------) [## 在Power BI中处理历史化

### 在报告中，历史化是最重要的。除了通常的时间序列数据外，我们还需要查看历史化…

towardsdatascience.com](/handling-historisations-in-power-bi-30dc43e9e6f?source=post_page-----ee4a402db39a--------------------------------) [](/develop-and-test-rls-rules-in-power-bi-9dc705945feb?source=post_page-----ee4a402db39a--------------------------------) [## 在Power BI中开发和测试RLS规则

### 很多时候，并不是所有用户都应该有权限访问报告中的所有数据。这里我将解释如何开发RLS（行级安全性）…

towardsdatascience.com](/develop-and-test-rls-rules-in-power-bi-9dc705945feb?source=post_page-----ee4a402db39a--------------------------------) [](/how-to-get-performance-data-from-power-bi-with-dax-studio-b7f11b9dd9f9?source=post_page-----ee4a402db39a--------------------------------) [## 如何使用DAX Studio从Power BI获取性能数据

### 有时我们遇到报告加载缓慢的问题，且需要找出原因。我们将看到如何收集性能数据以及…

towardsdatascience.com](/how-to-get-performance-data-from-power-bi-with-dax-studio-b7f11b9dd9f9?source=post_page-----ee4a402db39a--------------------------------)

我使用了Contoso示例数据集，就像我在之前的文章中做的那样。你可以从微软[这里](https://www.microsoft.com/en-us/download/details.aspx?id=18279)免费下载ContosoRetailDW数据集。

Contoso 数据可以在 MIT 许可证下自由使用，如 [这里](https://github.com/microsoft/Power-BI-Embedded-Contoso-Sales-Demo)所述。

[](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----ee4a402db39a--------------------------------) [## 每当 Salvatore Cagliari 发布时，您将收到电子邮件通知。

### 每当 Salvatore Cagliari 发布时，您将收到电子邮件通知。通过注册，如果您还没有 Medium 账户，将自动创建一个账户。

[medium.com](https://medium.com/@salvatorecagliari/subscribe?source=post_page-----ee4a402db39a--------------------------------)

尽管 Medium 有付费墙，我依然让我的文章对所有人开放。这让我可以从每个读者那里赚取一点收入，但我关闭了它，以便您可以免费阅读我的作品。

您可以通过以下方式支持我在空闲时间进行的工作：

[https://buymeacoffee.com/salvatorecagliari](https://buymeacoffee.com/salvatorecagliari)

或扫描此二维码：

![](../Images/e7ac062070dcd7a00dcf995ad7e95434.png)

任何支持都将不胜感激，并帮助我找到更多时间为您创作更多内容。

非常感谢。
