# 构建涉及 API 的增量数据加载脚本时需要解决的 3 个关键问题

> 原文：[https://towardsdatascience.com/3-essential-questions-to-address-when-building-an-api-involved-incremental-data-loading-script-03723cad3411?source=collection_archive---------3-----------------------#2024-06-30](https://towardsdatascience.com/3-essential-questions-to-address-when-building-an-api-involved-incremental-data-loading-script-03723cad3411?source=collection_archive---------3-----------------------#2024-06-30)

[](https://medium.com/@khoadaniel?source=post_page---byline--03723cad3411--------------------------------)[![Daniel Khoa Le](../Images/5c01c760dc1e92b3048cfae005838ef1.png)](https://medium.com/@khoadaniel?source=post_page---byline--03723cad3411--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03723cad3411--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--03723cad3411--------------------------------) [Daniel Khoa Le](https://medium.com/@khoadaniel?source=post_page---byline--03723cad3411--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03723cad3411--------------------------------) ·阅读时长 8 分钟·2024年6月30日

--

# **简短说明**

> 本文解释了使用 dlt（一个 Python 库）从 API 端点到数据库同步数据的概念框架和实际代码实现。
> 
> 在本教程结束时，你将理解（并知道如何实现）以下示意图中的同步行为，在此示意图中，我们通过增量方式提取数据，并使用合并（去重）策略将数据写入目标表。

![](../Images/e80987236bba43864b69abbfaef3cadd.png)

作者图片

# 背景

1.  你希望将数据从一个应用程序（例如，广告效果、销售数据等）同步到你的数据库。

1.  你的应用程序提供 API 端点来检索其数据。

1.  数据需要每天同步到你的数据库。

1.  你希望仅将 ❗***“新”数据（或更改部分）***❗ 加载到数据库中。你不希望每次同步时都重新加载整个数据集。

你如何在 Python 中实现这一点？

> 我将通过回答以下三个问题，带你了解解决方案。

# 💥 问题 1：我需要从 API 获取哪些信息以进行增量数据同步？

在开发增量加载脚本之前，我们需要了解我们正在使用的 API 端点的行为。

❗并不是所有的 API 都支持增量加载。

## 👉 答案：支持增量加载的查询参数

让我们来看一个应用程序的示例（或称为“源”应用程序），它跟踪你的销售业绩。在这个应用程序中，每条记录代表一个产品及其销售量。字段 `created_at` 和 `updated_at` 表示记录的创建和更新时间。

销售数据的变化通常有两种主要方式：

1.  新产品已被添加到列表中。

1.  更新会应用到现有记录的销售数据，**这会导致** `**updated_at**` 的新值。这样可以帮助我们追踪新的变化；如果没有它，我们就无法知道哪些记录已被修改。

👁️👁️ 下面是源应用数据库中的示例销售表。

↪️ **昨天的数据：2条记录**

![](../Images/fcf8c37fb8ec51718382ef2cb9d09a8b.png)

图片由作者提供

↪️ **今天的数据：新增了一条记录，并且修改了现有记录**

![](../Images/a7b45d06827f32925d53d8bf2cf1043d.png)

图片由作者提供

🟢 **要点：** 如果API端点允许基于 `updated_at` 参数进行查询，你可以通过发起请求来只检索那些 `updated_at` 值晚于上次同步保存的 `updated_at` 的记录，从而实现增量加载。在这种情况下，`updated_at` 被称为增量游标，其值会持续到下次同步，这个值称为状态。

`updated_at` 字段是增量游标的常见选择。其他查询参数，如 **id** 或 **sales**，无法帮助我们增量请求数据，因为它们无法告诉我们自上次同步以来，哪些记录已被添加或更新。

**你需要哪个查询参数来增量加载数据？**

![](../Images/7301b30bfbf0c3f33879ec2791bd4802.png)

图片由作者提供

> 由于我们正在开发一个与API配合使用的数据加载脚本，我将介绍两个API的其他重要方面，以便代码实现：**分页和路径参数**。不过，它们与增量加载无关。

## 🤷 分页机制

API通常会分批返回结果，以提高性能。例如，API可能不会一次性返回10,000条记录，而是将响应限制为每次最多100条记录，要求你迭代处理后续批次。

为了管理这个，你通常（但并非总是）需要使用两个查询参数：`limit` 和 `skip`（或 `offset`）。

这里有一个简单的例子来说明：

对于第一个请求：

+   `limit`=100

+   `skip`=0

对于第二个请求，要跳过我们已经同步的前100条记录：

+   `limit`=100

+   `skip`=100

这一模式将持续进行，每批次后，`skip` 值会按 `limit` 增加，直到所有记录都被检索。

🟢 **要点：** 你需要理解API如何返回响应，以免在提取数据时漏掉任何记录。API有很多管理分页的方法，超出了常用的 `skip` 和 `offset` 方法。这是另一个话题，留待以后再说。

## 🤷 路径参数

路径参数直接包含在API的URL中，通常用于区分数据的不同部分（分区）。例如，它们可能指定你营销账户中的不同活动或源应用中管理的不同子账户。

在下面的例子中：路径参数是 `applicationId` 和 `campaignId`。

`[https://yourbaseurl.myapp/v1/applications/{applicationId}/campaigns/{campaignId}/](https://yourbaseurl.talon.one/v1/applications/%7BapplicationId%7D/campaigns/%7BcampaignId%7D/coupons/no_total)sales`

🟢 **总结：**你需要决定是将来自同一API但具有不同路径参数的数据同步到同一张表，还是同步到不同的表（如 sales_campaign_1, sales_campaign_2 等）。

# 💥 问题 2：我如何将提取的记录写入目标表？

现在，假设你已经通过上述参数提取了一批记录，是时候决定如何将这些记录写入目标表了。

## 👉 答案：合并/去重模式（推荐）

这个问题涉及到**写入方式（Write disposition）**或**同步模式（Sync mode）**的选择。直接的答案是，如果你希望增量加载数据，你可能会选择以追加模式或合并模式（也叫去重模式）将提取的数据写入。

然而，让我们退后一步，更仔细地审视我们的选择，并确定哪种方法最适合增量加载。

以下是常见的写入方式。

+   🟪 **覆盖/替换（overwrite/replace）**：删除目标表中所有现有记录，然后插入提取的记录。

+   🟪 **追加（append）**：简单地将提取的记录追加到目标表中。

+   🟪 **合并 / 去重（merge / dedup）**：插入新(*)记录并更新(**)现有记录。

(*) 我们怎么知道哪些记录是新的？：通常，我们会使用主键来确定。如果使用 dlt，它的合并策略可以比这更复杂，包括区分`merge_key`和`primary_key` *(一个用于合并，一个用于去重后再合并)*，或者`dedup_sort` *(在去重过程中，哪些记录会被删除)*。这一部分我将留到另一个教程讲解。

(**) 这是一个简单的解释，如果你想了解更多关于 dlt 如何处理这种合并策略的信息，可以[点击这里](https://dlthub.com/docs/general-usage/incremental-loading#merge-incremental-loading)阅读更多。

👁️👁️ 这里有一个例子帮助我们理解不同写入方式的结果。

↪️ **2024年6月19日：我们进行了第一次同步。**

🅰️ **数据在** `**源应用程序**`️️

![](../Images/74b8a8cbec32ac881ce296f257ab9913.png)

图片由作者提供

🅱️ **数据已加载到我们的** `**目标数据库**`

无论选择哪种同步策略，目标表中的数据**严格来说是源表的副本**。

![](../Images/74b8a8cbec32ac881ce296f257ab9913.png)

图片由作者提供

已保存的 `updated_at` 状态 = 2024-06-03，这是我们同步的两条记录中最新的 `updated_at`。

↪️ **2024年6月2日：我们进行了第二次同步。**

**🅰️ ️️️️️️️数据在** `**源应用程序**`

![](../Images/43dd22288ef2069f3129fa81154bed22.png)

图片由作者提供

✍️ 源表中的更改：

+   记录 ID=1 已更新（销售数据）。

+   记录 ID=2 已删除。

+   记录 ID=3 已插入。

在这次同步中，我们仅提取 `updated_at` > 2024–06–03（上次同步时保存的状态）之后的记录。因此，我们只会提取记录 id=1 和 id=3。由于记录 id=2 已从源数据中删除，我们无法识别此变动。

在第二次同步后，你将能够看到不同的写入策略之间的差异。

🅱️ **数据已加载到我们的** `**目标数据库**`

❗ **场景 1：覆盖**

![](../Images/19496a1ddb57a9f1e1107c691ddf441d.png)

图片由作者提供

目标表将被这次提取的两条记录覆盖。

❗ **场景 2：追加**

![](../Images/c30e26065a3f3bf15be9590181267768.png)

图片由作者提供

这两条提取的记录将被追加到目标表中，现有的记录不会受到影响。

❗ **场景 3：合并或去重**

![](../Images/380959de013398c27bd89d42b83d8da6.png)

图片由作者提供

提取的两条记录，id=1 和 id=3，将替换目标中的现有记录。这一处理过程被称为合并或去重。目标表中的记录 id=2 将保持不变。

🟢 **总结：** 合并（去重）策略在增量数据加载管道中可能非常有效，但如果你的表非常大，去重过程可能需要相当长的时间。

# 💥 问题 3：我如何在代码中实现它？

## 👉 答案：dlt —— 因为它轻量、文档齐全，并且有活跃的社区支持。

> [dlt](https://dlthub.com/) 是一个非常不错的选择，因为它提供了适当的抽象层次。事实上，你可以选择你需要的抽象程度。如你在我下面的示例代码中看到的，我主动编写了自己的请求循环，但 dlt 提供了可以用更少代码行完成的辅助函数。这种灵活性使得 dlt 相较于其他解决方案脱颖而出。

你可以参考这个图表来获得高层次的视图，然后再深入到带有详细备注的代码中。

小提示：dlt 在其结构中使用了 **源** 和 **资源** 这两个术语。资源通常对应一个 API 端点，并将数据写入目标数据库中的表。源是资源的集合。

在下面的插图中，你可以看到我们讨论的两个问题的答案：

+   问题 1 的答案：使用日期游标向 API 端点发送请求，逐步获取数据（并持久化游标值，也就是状态，以便后续执行）。

+   问题 2 的答案：使用合并策略将数据写入目标表。

![](../Images/0d48e7a8399a7ac757f7be442cb17cd2.png)

> 现在，你可能想知道如何运行这个 Python 脚本？我建议你访问[这个代码库](https://github.com/khoadaniel/incremental-data-loading-guide)，并亲自尝试一下。这个代码库还为你提供了一个模拟 API，你可以在本地部署进行测试。查看 README 获取详细的执行指南。

💥 这里是我附带备注的 dlt 实现片段 💥

🟣️ **完整代码可以在仓库中查看** [**点击这里**](https://github.com/khoadaniel/incremental-data-loading-guide)🟣

✅ 本教程到此结束。希望您已经了解了构成增量加载脚本的不同组件，以及代码的实现方法。

如果您有兴趣了解如何使用dlt构建增量加载脚本，请查看他们的文档[点击这里](https://dlthub.com/docs/general-usage/incremental-loading)。

# 关于我

我是Daniel Le，居住在柏林，目前从事数据工程和机器学习领域的工作。

我对新技术充满兴趣，尤其是它们如何应用于解决现实世界中的问题。

如果您有任何疑问或希望进一步讨论这些兴趣，欢迎通过[LinkedIn](https://www.linkedin.com/in/khoadaniel/)与我联系。

# 参考文献

+   [https://dlthub.com/docs/general-usage/incremental-loading](https://dlthub.com/docs/general-usage/incremental-loading)
