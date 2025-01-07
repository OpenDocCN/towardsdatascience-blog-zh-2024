# 数据质量不需要复杂

> 原文：[https://towardsdatascience.com/stop-overcomplicating-data-quality-4569fc6d35a4?source=collection_archive---------1-----------------------#2024-12-10](https://towardsdatascience.com/stop-overcomplicating-data-quality-4569fc6d35a4?source=collection_archive---------1-----------------------#2024-12-10)

## 三种零成本解决方案，只需几个小时，而非几个月

[](https://medium.com/@caiparryjones96?source=post_page---byline--4569fc6d35a4--------------------------------)[![Cai Parry-Jones](../Images/60b83f5167651f9621a3e73b8d72ccae.png)](https://medium.com/@caiparryjones96?source=post_page---byline--4569fc6d35a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4569fc6d35a4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4569fc6d35a4--------------------------------) [Cai Parry-Jones](https://medium.com/@caiparryjones96?source=post_page---byline--4569fc6d35a4--------------------------------)

·发布在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4569fc6d35a4--------------------------------) ·阅读时长 8 分钟 ·2024年12月10日

--

![](../Images/4b570b710780faeea2899cb85facde86.png)

一个“数据质量”认证的管道。来源：[unsplash.com](https://unsplash.com/)

在我的职业生涯中，数据质量相关的项目通常意味着重大变革。从治理流程到昂贵的工具，再到 dbt 实施——数据质量项目似乎从不想做得小而简单。

更重要的是，使用这种方式修复数据质量问题通常会导致新问题。更复杂、更高的成本、更慢的数据项目发布……

![](../Images/3713bea14e131ad68e6fde839cda4e8f.png)

由作者使用 [Google Sheets](https://workspace.google.com/intl/en_uk/products/sheets/) 创建

但事实并非一定如此。

虽然像 Google 和 Meta 这样的大公司由于其庞大的规模和复杂性需要广泛的数据质量框架，但大多数组织可以通过更简单的方法实现优秀的数据质量。

在本文中，我们将探讨三种小型到中型数据项目可以用来快速提高数据质量的方法，同时保持

将复杂性降到最低，新的成本为零。我们开始吧！

# 简短总结：

+   利用老派的数据库技巧，如 ENUM 数据类型和列约束。

+   为您的特定数据质量问题创建一个自定义仪表板。

+   使用一个简单的 Python 脚本生成数据血统。

# 利用老派的数据库技巧

在过去的 10 到 15 年里，我们见证了数据行业的巨大变化，尤其是大数据、并行处理、云计算、数据仓库和新工具（大量大量的新工具）。

因此，为了腾出空间容纳所有这些新事物，我们不得不告别一些东西。有一些积极的方面（比如微软 Access），但也有一些最多只能说是值得怀疑的，比如传统的数据设计原则和数据质量及验证过程（数据摄入时）。后者将是本节的主题。

首先，什么是“数据质量和验证在摄入时”？简单来说，这意味着在数据进入表格之前对其进行检查。可以把它想象成夜店外的保安。

替代方案是构建后再测试，这意味着先将新数据放入表格中，然后再检查它。构建后再测试是许多现代数据质量工具选择的方法，包括最流行的[dbt](https://www.getdbt.com/product/what-is-dbt)。

Dbt 会首先运行整个数据转换管道，只有在所有新数据就位后，才会检查数据是否合格。当然，在许多情况下，这可能是最佳解决方案。例如，如果业务愿意为速度而牺牲质量，或者如果在生产表之前有一个 QA 表（Netflix 称之为[写入-审计-发布](https://lakefs.io/blog/data-engineering-patterns-write-audit-publish/)）的话。然而，仅使用这种数据质量方法的工程师可能错失了对其组织的巨大收益。

![](../Images/46fe0562178bfdf861d61c21943b513c.png)

生成表格前后进行测试。由作者使用[draw.io](https://app.diagrams.net/)创建。

测试后再构建比构建后再测试有两个主要优势。

第一个优势是，它确保下游表格中的数据始终符合预期的数据质量标准。这为下游用户提供了一定程度的可信度，这种可信度通常是缺乏的。它还可以减轻负责数据管道的工程师的焦虑感。

我记得当我曾经在一家公司负责一个关键财务管道时。遗憾的是，这个管道非常容易出现数据质量问题，而当时的解决方案是一个构建后再测试的系统，每晚运行一次。这意味着我每天早上必须赶早去检查运行结果，以免下游用户开始查看他们的数据。如果出现任何问题，我就需要迅速修复问题，或者发送一条羞耻的 Slack 消息，通知业务部门数据有问题，请耐心等待我修复。

当然，测试后再构建并不能完全解决这种焦虑问题。故事的转变从需要匆忙修复问题以避免下游用户看到不良数据，变成了匆忙修复问题以避免下游用户看到过时数据。然而，工程师的工作就是权衡不同解决方案的利弊。在这种情况下，我知道，对于业务和我的理智来说，旧数据是两种恶性选择中的最佳方案。

测试-然后构建的第二个好处是，它通常比设置一个完整的QA区域更容易实现，尤其是与为解决大多数数据质量问题而准备的“火箭筒打兔子”解决方案相比。你需要做的就是在创建表时包含数据质量标准。看看下面的PostgreSQL查询：

```py
CREATE TYPE currency_code_type AS ENUM (
    'USD', -- United States Dollar
    'EUR', -- Euro
    'GBP', -- British Pound Sterling
    'JPY', -- Japanese Yen
    'CAD', -- Canadian Dollar
    'AUD', -- Australian Dollar
    'CNY', -- Chinese Yuan
    'INR', -- Indian Rupee
    'BRL', -- Brazilian Real
    'MXN'  -- Mexican Peso
);

CREATE TYPE payment_status AS ENUM (
    'pending',
    'completed',
    'failed',
    'refunded',
    'partially_refunded',
    'disputed',
    'canceled'
);

CREATE TABLE daily_revenue (
    id INTEGER PRIMARY KEY,
    date DATE NOT NULL,
    revenue_source revenue_source_type NOT NULL,
    gross_amount NUMERIC(15,2) NOT NULL CHECK (gross_amount >= 0),
    net_amount NUMERIC(15,2) NOT NULL CHECK (net_amount >= 0),
    currency currency_code_type,
    transaction_count INTEGER NOT NULL CHECK (transaction_count >= 0),
    notes TEXT,

    CHECK (net_amount <= gross_amount),
    CHECK (gross_amount >= processing_fees + tax_amount),
    CHECK (date <= CURRENT_DATE),
    CONSTRAINT unique_daily_source UNIQUE (date, revenue_source)
); 
```

这14行代码将确保daily_revenue表强制执行以下标准：

`id`

+   主键约束确保唯一性。

`date`

+   不能是未来日期（通过CHECK约束）。

+   是与revenue_source的唯一约束的一部分。

`revenue_source`

+   不能为NULL。

+   是与date的唯一约束的一部分。

+   必须是revenue_source_type枚举中的有效值。

`gross_amount`

+   不能为NULL。

+   必须大于或等于0。

+   必须大于或等于processing_fees + tax_amount。

+   必须大于或等于net_amount。

+   精确的小数处理。

`net_amount`

+   不能为NULL。

+   必须大于或等于0。

+   必须小于或等于gross_amount。

+   精确的小数处理。

`currency`

+   必须是currency_code_type枚举中的有效值。

`transaction_count`

+   不能为NULL。

+   必须大于或等于0。

这很简单，可靠。你能相信这些功能自PostgreSQL 6.5发布以来就已经可用吗……它发布于1999年！

当然，没有免费的午餐。通过这种方式强制执行约束确实有其缺点。例如，它使得表格的灵活性大大降低，并且在更新表格时会降低性能。像往常一样，在深入使用任何工具/技术/方法之前，你需要像工程师一样思考。

# 创建自定义仪表板

我有一个忏悔要说。我曾经认为优秀的数据工程师不会使用仪表板工具来解决问题。我认为一个真正的工程师会查看日志、难以阅读的代码，以及其他任何让他们看起来聪明的东西，如果有人不小心瞥见他们的电脑屏幕的话。

我当时真傻。

事实证明，如果有效地执行并有明确的目的，它们可以非常有价值。此外，大多数BI工具使得创建仪表板变得非常简单和快速，而且不需要（太多）时间来学习该工具。

回到我个人的管道经验。我曾经管理一个包含所有业务收入来源的每日汇总表。每个来源来自不同的收入提供者，因此来自不同的系统。有些是通过API调用，有些通过电子邮件，还有些通过共享的S3桶。正如任何工程师所预期的那样，这些来源有时会出现问题，而由于它们来自第三方，我无法在源头解决问题（只能请求，成功的机会非常有限）。

起初，我只使用故障日志来确定需要修复的地方。问题在于优先级。一些故障需要迅速修复，而另一些则不够重要，不能因此打乱所有工作（我们有些收入来源每天报告的收入只有几分钱）。因此，出现了一些小的数据质量问题的积累，变得难以追踪。

引入Tableau。

我创建了一个非常基础的仪表盘，展示了过去14天按收入来源和日期的元数据。三个指标就是我所需要的：

1.  一个绿色或红色的标记，表示数据是否存在或缺失。

1.  数据的行数。

1.  数据的收入总和。

![](../Images/c52b8496ecc6364ad902401ced5c886e.png)

一个简单但有效的仪表盘。由作者使用[Tableau](https://www.tableau.com/en-gb)创建

这使得管道的数据质量管理变得更加轻松。不仅我能更快速地查看问题所在，而且它足够用户友好，其他人也能轻松阅读，确保了责任的共享。

实施仪表盘后，业务方关于管道的错误工单几乎降到零，我的中风风险也降低了。

# 使用血缘图绘制您的数据

简单的数据可观察性解决方案不仅限于仪表盘。

数据血缘图可以帮助快速发现受坏数据影响的表格。

然而，实施起来也可能是一项庞大的任务。

在我看来，造成这一问题的首要原因是dbt。这个开源工具的一个关键卖点就是它的数据血缘功能。但为了实现这一点，您必须屈服于dbt的框架。这包括但不限于：

+   在所有SQL文件中实现[Jinja3](https://jinja.palletsprojects.com/en/stable/)。

+   为每个数据模型创建一个YAML文件。

+   通过YAML文件添加源数据配置。

+   设置开发和测试流程，例如开发环境、版本控制、CI/CD。

+   基础设施设置，例如托管自己的服务器或购买托管版本([dbtCloud](https://www.getdbt.com/pricing))。

是的，确实很多。

但它不一定非得是这样。归根结底，实现动态数据血缘图所需的就是一台扫描您的SQL文件的机器，以及输出用户友好的血缘图的工具。得益于Python，这可以通过一个仅有100行代码的脚本来实现。

如果你了解一些Python和LLM提示，你应该能在一个小时内修改代码。或者，已经有一个轻量级的开源Python工具叫做[SQL-WatchPup](https://github.com/caitpj/SQL-WatchPup)，它已经包含了代码。

只要您拥有所有的SQL文件，在15分钟的设置之后，您就应该能够生成像这样的动态数据血缘图：

![](../Images/2ce584894fa4dbecf310e22749ba9487.png)

示例数据血缘图输出。由作者使用[SQL-WatchPup](https://github.com/caitpj/SQL-WatchPup)创建

就这样。没有服务器托管费用。无需学习额外的编程语言。无需重构文件。只需在本地运行一个简单的Python脚本。

# 结论

说实话——我们都喜欢那些闪亮的新工具，但有时最好的解决方案是那些老旧、不可酷、和/或不流行的工具。

下次当你面临数据质量问题时，先退后一步，再考虑是否需要进行那次大规模的基础设施改造。问问自己：一个简单的数据库约束、一个基础的仪表盘，或者一个轻量级的 Python 脚本，能解决问题吗？

你的理智会感谢你这么做，你公司的预算也会感谢你。
