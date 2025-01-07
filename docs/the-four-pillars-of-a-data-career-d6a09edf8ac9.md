# 数据职业的四大支柱

> 原文：[`towardsdatascience.com/the-four-pillars-of-a-data-career-d6a09edf8ac9?source=collection_archive---------0-----------------------#2024-11-10`](https://towardsdatascience.com/the-four-pillars-of-a-data-career-d6a09edf8ac9?source=collection_archive---------0-----------------------#2024-11-10)

## 确保获得入门级数据分析师职位的技术基础

[](https://medium.com/@Carobert?source=post_page---byline--d6a09edf8ac9--------------------------------)![Charles Mendelson](https://medium.com/@Carobert?source=post_page---byline--d6a09edf8ac9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6a09edf8ac9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6a09edf8ac9--------------------------------) [Charles Mendelson](https://medium.com/@Carobert?source=post_page---byline--d6a09edf8ac9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6a09edf8ac9--------------------------------) ·阅读时长 8 分钟·2024 年 11 月 10 日

--

![](img/e76d8b4c0425a6797e4bf0fd3eb343a6.png)

由 [Jon Tyson](https://unsplash.com/@jontyson?utm_source=medium&utm_medium=referral) 摄影，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

TLDR;

1.  电子表格（Excel）

1.  SQL

1.  可视化工具（Tableau 或 Power BI）

1.  脚本语言（Python 或 R）

# 介绍

我经常被尝试进入数据领域的人问到：他们需要学习哪些技能才能获得第一份数据工作，应该在哪里学习这些技能。本文总结了我过去五年给有志成为数据科学家、分析师和工程师的人的建议。

本文主要面向那些自学成才、希望进入数据领域并获得第一份工作的读者。如果你正在阅读这篇文章，那么很可能你第一份工作会是数据分析师。大多数入门级数据职位都是分析师职位，我不认为数据科学家或数据工程师是入门级职位。

四大支柱是电子表格、SQL、可视化工具和脚本语言。

不同的工作需要不同组合的这些技能，你可以通过精通其中一个支柱来建立整个职业生涯，但几乎所有的数据职位都至少需要对这四个领域有初步了解。

# Excel

![](img/ecac22a9d26ab955919cd36c57101468.png)

由 [m.](https://unsplash.com/@m_____me?utm_source=medium&utm_medium=referral) 摄影，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Excel 是数据世界的起源和终结。30 年来，数据界一直在讨论传说中的“Excel 杀手”，但至今未曾出现。你可能参与了一个由多个团队组成、历时 6 个月的项目，旨在统一来自 7 个数据库的数据，并将其构建成最炫的 Tableau 仪表板，而你的利益相关者问的第一件事就是，如何将数据导出到 Excel。

Excel 功能庞大，大多数用户只是触及了其表面，但这是我认为成为分析师角色所需的最低技能列表：

1.  基本界面导航

1.  公式

1.  条件语句（IF, IFS, COUNTIFS, SUMIFS 等）

1.  电子表格卫生（确保你的电子表格逻辑布局清晰）

1.  数据集连接（V-lookup, X-lookup, index-match）

1.  图表/可视化

1.  数据透视表

1.  数据筛选和排序

## Power Query

如果你想更进一步，我还推荐有抱负的分析师熟悉 Power Query（也叫 Get and Transform）。我喜欢 Power Query，因为它是初学者接触更正式结构化数据和适当表格数据的良好入门工具。

学习 Power Query 和 Power Pivot 的一个优势是，它们在 Power BI 中得到了广泛应用。

## 那么，Google 表格怎么样呢？

Google 表格是 Excel 的一个可靠替代品，但缺少很多高级功能。如果你学会了 Excel，可以很快适应 Google 表格，并且可以在 Google 表格中学习许多基本的电子表格功能，但我认为目前它并不足以替代 Excel。

我的观察是，Google 表格通常在政府、学术界和早期至中期的创业公司中使用。

## VBA

如果你正在尝试解决 Excel 中的某个问题，而你找到的教程建议使用 VBA，那么请寻找其他解决方案。

# SQL 和数据库

![](img/7bf945ae258f937b9050394386b469fb.png)

图片来源：[Jan Antonin Kolar](https://unsplash.com/@jankolar?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是一个让有抱负的分析师感到棘手的话题，因为除了在生产环境中，学习如何处理数据库的细微差别非常困难，特别是当数据集非常干净时。这是因为大多数实践数据集过于整洁。

在我的一份工作中，我曾完全搞砸了一个 SQL 查询请求，因为我犯了一个初学者的错误——将两个表连接在 FINANCING_ID 列上，而不是 FINANCING_ID_NEW 列。

大多数拥有分析师的组织，其数据库并非经过精心规划或设计，而是随着时间的推移、通过并购以及软件工程师为了解决眼前问题而迅速积累的数据。

对许多组织来说，可能需要几个月的时间才能熟悉其数据库。

所以我的建议是，除了学习 SQL 的基本语法外，我不会花太多时间去精通 SQL，除非你已经有了一个每天需要写 SQL 的工作。

这是我建议你学习的基本查询技能：

1.  基本语法

1.  SQL 查询的结构

1.  聚合

1.  多表联接

1.  维度建模

1.  CTEs 和子查询

## 你应该学习哪种方言？

其实并不重要，因为它们非常相似，一旦你掌握了其中一种，差异可以通过 Google 或 Chat GPT 轻松解决。我的建议是选择 Postgres 或 T-SQL。

# 可视化

![](img/fe73510dcbcdddb53bed5bf4c6c75531.png)

图片来源：[Clay Banks](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral) 摄于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

虽然 Excel 可以用于生成一些可视化图表，但大多数雇佣分析师的公司会使用 Power BI 或 Tableau 来制作仪表盘（我也使用过其他一些工具，但这两者是主流）。

和 SQL 一样，我不建议在你找到工作之前过多关注可视化中的索引问题，学习基础是很重要的，但许多高级功能最好是在生产环境中学习。

## Power BI 还是 Tableau？

我建议你选择其中一个并专注于它，而不是在两者之间分散注意力。

如果你主要的工作经验是在 Excel 中处理数据，那么 Power BI 对你来说可能更直观。一旦你学会了使用其中一个，学习另一个也会变得很容易，对于大多数通用分析师职位，招聘经理并不会特别在意，只要你能掌握其中之一。

我曾面试过一家大型企业的职位，负责开发 Tableau 仪表盘，我问招聘经理：“如果你雇佣我，你认为六个月后，我会是什么样的成功员工？”

他的回答是：“如果你在六个月后能编辑一个仪表盘，我会认为这是成功。”

和 SQL 一样，使用可视化工具时，挑战之一就是理解组织的数据。

## 你应该学什么？

1.  制作所有标准图表

1.  数据清理，以及如何将数据结构化以导入你选择的工具中

1.  基础仪表盘制作

# 脚本语言

![](img/e36478ae8e17173134611cd1b12cd7cf.png)

图片来源：[Kevin Canlas](https://unsplash.com/@kvncnls?utm_source=medium&utm_medium=referral) 摄于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最后，我们有脚本语言。作为提示，我的前几个分析师职位并不要求我掌握脚本语言，但那是很久以前的事了。根据当前的应用需求，至少了解一些脚本语言现在已经成为入门级职位的要求。

## R 还是 Python？

如果你已经掌握了 R（在统计学课程中学过），那么可以继续专注于 R，否则学习 Python。如果你已经精通其中一种语言，学习另一种语言的语法在你上手时就能轻松掌握。

R 语言通常在与学术界关系密切的组织中更为常见。生物技术公司更有可能使用 R，因为他们的研究人员在研究生阶段更有可能使用它。

## 你应该学习什么？

1.  变量

1.  基本的数值操作

1.  字符串操作

1.  条件语句（If/then）

1.  基本数据结构（列表、字典、元组、集合）

1.  循环

1.  定义和使用函数

1.  Pandas（是库，不是动物）

# 一般建议

+   你不需要成为这些主题的专家，你需要对它们有所了解。

+   对于初级分析师职位，主要集中在 Excel 上。

+   不要高估自己的技能水平。我曾面试过一位自称为“中级”Python 用户的候选人。虽然这个职位并不要求使用 Python，但既然他们说能做，我就给他们布置了一个现场编程测试。我要求他们定义一个函数，检测给定的字符串输入是否是回文（即正着和倒着拼写都相同的单词）。他们随后承认自己不知道如何定义函数。我礼貌地结束了面试。

# 证书

![](img/f01f21b09875a30bfb2d24c27044200f.png)

图片来源：[Lewis Keegan](https://unsplash.com/@skillscouter?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在大多数情况下，我认为证书对于获得初级职位并不是特别有用。它们可能在某些情况下有所帮助（也许你会获得本不该获得的招聘面试），但我不认为它们值得花费精力。

有一个例外：南亚的就业市场。

在评估候选人时，我确实使用了一些证书作为启发式方法。

通常这些证书有几个共同点：

1.  它们来自像 Snowflake、Microsoft 或 Tableau 这样的主要数据技术公司。

1.  获得这些证书需要花费数百美元，对于典型的南亚员工（或他们的雇主）来说，这是一个相当大的投资。

## 免费证书

有很多免费的或非常低价的证书，比如 Google 数据证书。总体而言，我认为它们的价值大致等同于你为它们支付的费用。学习内容扎实，课程结构完整，但证书本身并不会真正帮助你脱颖而出。

# 通过技术面试

![](img/7b737e5da4a8aa133be3c5a83d098902.png)

图片来源：[charlesdeluvio](https://unsplash.com/@charlesdeluvio?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当我面试候选人时，我真的希望他们成功，我怀疑大多数面试官也都是如此。

所以在面试时，保持对话式的方式。

我更感兴趣的是你如何得出正确答案，而不是你是否得出正确答案。我更倾向于候选人提出问题、测试想法并请求澄清。如果你走偏了，我会提问，看看能不能帮助你走回正轨。

# 资源

![](img/0f4f28f1ba8def6d0ffa30755c3b1d6c.png)

图片由 [Twinkl](https://unsplash.com/@twinkl?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

以下是我在学习这些技能时使用的主要付费资源。这些不是推荐链接，我不会从你购买中获得任何收益。

## Excel

[Tom Hinkle](https://www.udemy.com/user/tomhinkle/) 是我亲爱的朋友，我强烈推荐他在 Udemy 上的课程。

[Oz Du Soleil 是我最喜欢的在线讲师之一，也是个非常好的人](https://www.youtube.com/channel/UCZgOVykPoRbSZQfY9YysiRQ?app=desktop): 我链接了他的 YouTube 频道，因为他在那里提供了很多内容。

[如果你想学习 Power Query，skillwave 培训](https://skillwave.training/learn/) 非常出色。他们也有 Power BI 课程，尽管我没有上过。

## SQL

[IMDB 的实际数据库](https://developer.imdb.com/non-commercial-datasets/): 这是一个非常干净的数据集，可以让你在一个维度化建模的数据库中练习复杂的 SQL 查询。

[Microsoft Contoso 数据库](https://www.microsoft.com/en-us/download/details.aspx?id=18279): 这个模拟了一个零售网站的数据库，可以让你在聚合和回答业务问题上得到很好的练习。

## Tableau

[Tableau 提供了使用其产品的最佳培训之一](https://www.tableau.com/learn/training/elearning)。我建议你学习他们的课程，而不是支付其他人的费用。

## Python

[Python 圣经](https://www.udemy.com/course/the-python-bible/?couponCode=JUST4U02223): Ziyad 是一位非常引人入胜的在线讲师。

[完整的 Pandas 训练营](https://www.udemy.com/course/the-pandas-bootcamp/): Alexander Hagman 内容枯燥，但非常彻底。每当我需要复习 Pandas 时，我仍然参考这门课程。

## 一般

[Anil 是我早期的导师之一](https://academy.optizent.com/)，他后来创建了一个数字分析导师/教育平台。他曾在当地大学教授我，但他的工作非常出色，并且他对学生投入了大量的心血。

你认为我遗漏了哪些基础分析技能吗？

# 关于作者

Charles Mendelson 是一家三大管理咨询公司的高级软件工程师，帮助客户构建 AI 原型和 MVP。

他以自学数据分析师的身份开始了他的技术生涯，后来成为了一名数据工程师。
