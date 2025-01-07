# 关于图形数据库和Neo4j的所有你需要知道的事

> 原文：[https://towardsdatascience.com/everything-you-need-to-know-about-graph-databases-neo4j-b9154f57dad0?source=collection_archive---------3-----------------------#2024-07-26](https://towardsdatascience.com/everything-you-need-to-know-about-graph-databases-neo4j-b9154f57dad0?source=collection_archive---------3-----------------------#2024-07-26)

## 了解图形数据库：关键概念与优势

[](https://medium.com/@martin-jurran?source=post_page---byline--b9154f57dad0--------------------------------)[![马丁·朱兰（Martin Jurran）](../Images/f34f11741f9a7b8c0ccb42ef0509a04a.png)](https://medium.com/@martin-jurran?source=post_page---byline--b9154f57dad0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b9154f57dad0--------------------------------)[![数据科学之道（Towards Data Science）](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b9154f57dad0--------------------------------) [马丁·朱兰](https://medium.com/@martin-jurran?source=post_page---byline--b9154f57dad0--------------------------------)

·发表于[数据科学之道（Towards Data Science）](https://towardsdatascience.com/?source=post_page---byline--b9154f57dad0--------------------------------) ·13分钟阅读·2024年7月26日

--

![](../Images/76c848deb3e7080a6e3b77e8a0318e7b.png)

（图片由作者提供，插图来自[高桥美船（Takashi Mifune）在免费使用下](https://www.irasutoya.com/p/terms.html)）

存储和处理数据是软件工程中的基本任务。在大型专业开发的早期阶段，关系数据库如Oracle、IBM DB2和SQL占据主导地位。数据操作系统无法轻松处理结构化或关系型数据，而只能处理扁平化的数据表示。[1] 图形数据库试图弥合关系型数据和扁平数据表示之间的差距，同时使访问信息变得更加容易。[2] 这种类型数据库的最受欢迎代表是Neo4j。[[3](https://db-engines.com/en/ranking/graph+dbms)]

```py
Name: Neo4j
Software Type: Graph Database (GDB)
Initial Release: 2007
Origin: Neo4j, Inc.
Target Platform: Cross-Platform, e.g. Windows, Linux, ..
Languages: Implemented in Java and Scala, Web-Tools in Typescript, Cloud functionalities in Go
Website: https://neo4j.com/
```

# 介绍

当今世界的各种交易越来越依赖数字化。也就是说，由于大多数国家（例如德国）卡片和电子支付方式的使用显著增加。[4] 随着交易变得更加数字化，IC3投诉统计等指标表明，数字犯罪活动也在增加。[10] 例如，作为支付处理软件供应商的TransUnion报告称，全球数字欺诈尝试增长了149%。[5]

如果不使用图形数据库，涉及此类活动的人的交易和关系需要以关系型的方式建模。但是，如果使用更合适的数据库类型——图形数据库，建模和访问关系数据要容易得多。

让我们通过一些例子来看看图形数据库在哪些方面非常有用。

## 示例 1 — 揭露巴拿马文件 ⚖️

![](../Images/c1fd9c45abd4fa33e4f581ee42c6db8e.png)

《巴拿马文件》Neo4j 数据库数据模型。[13]（图片由作者提供）

2016年，大量文件泄露给了德国记者巴斯蒂安·奥伯迈耶（Bastian Obermayer），他来自报纸[南德意志报](https://panamapapers.sueddeutsche.de/)。[6]

泄露的文件包含了前所未见的大规模税务逃避和洗钱线索。此次泄露包含了2.6TB的数据，由1150万个单独的文件组成。[7]

为了处理和调查主要由商业主体之间的关系数据构成的数据，基于图形的方法显而易见。提到的人物和公司可以作为*1) 节点*，而关系的类型和属性则作为*2) 边*。在本节的特色图片中，您可以看到《巴拿马文件》是如何在 Neo4j 中建模的。

## 示例 2 — 物业管理 ️🛫️

![](../Images/fe75850499e56996d8e71a00ae058a1e.png)

示例：如何在 Neo4j 中建模一个物业管理的应用场景（图片由作者提供，插图来自[Takashi Mifune 在免费使用许可下](https://www.irasutoya.com/p/terms.html)）

如同那些在机场待得太久的人所证明的那样，机场里有无数对象，它们之间相互关联。

从飞机到航站楼，再到登机口和餐厅，机场的每个元素都是互联的，并依赖于多种关系来顺利运作。

这就是图形数据库所能支持的功能。通过将机场、航站楼、飞机、餐厅等作为图中的节点，以及它们之间的关系作为边，物业管理人员可以更深入地了解机场的运营情况。

## 示例 3 — JR 东日本的火车运营 🚆

![](../Images/1740580807574c8c4a3ecc3e5480d9a1.png)

我们针对东京公共交通示例的 Cypher 查询结果。[ [14](https://github.com/ggszk/neo4j-train-route-sample) ]（图片由作者提供）

公共交通涉及许多不同的对象，如车站和站台，这些对象之间有相互连接。

这些连接可以有额外的细节，例如在公共交通网络中从一个点到另一个点的旅行时间。

这种网络的一个例子是东京的火车线路和车站。当我们将它们加载到 Neo4j 中时，可以使用它的查询语言 Cypher 来运行查询，收集有关这些对象及其连接的信息。[14]

```py
MATCH (source:Station {name: '高田馬場'}), (destination:Station {name: '池袋'})
MATCH path = shortestPath((source)-[r*]-(destination))
RETURN path, 
  reduce(cost = 0, rel in relationships(path) | 
    cost + coalesce(rel.cost, 0)) AS total_cost LIMIT 1
```

本节的特色图片展示了我们查询的结果。为了验证我们的结果是否正确，我使用了谷歌地图来计算路线，令我惊讶的是，结果与使用 Cypher 查询得到的结果相同。用 Neo4j 实现路线优化算法一定很容易。

![](../Images/9c4e04f5791876321ee691c3f034de0a.png)

在谷歌地图中查看 Cypher 查询结果：我们的输出是有意义的，Neo4j 帮助计算了最短路线。（图片由作者提供）

如果你想亲自尝试，我已经fork了原始的代码库，并包含了我请求的Cypher文件。为了获得最佳体验，建议你具备一些基础的日语知识。祝你玩得开心！🤗  

[](https://github.com/martinjurran/neo4j-train-route-sample?source=post_page-----b9154f57dad0--------------------------------) [## GitHub - martinjurran/neo4j-train-route-sample: 一个用于最短路径查询或行程规划查询的示例数据库…  

### 一个用于最短路径查询或行程规划查询的示例数据库，使用Neo4j（用日语）。…  

github.com](https://github.com/martinjurran/neo4j-train-route-sample?source=post_page-----b9154f57dad0--------------------------------)  

## 图数据库的替代方案

有许多场景可以通过图形来展示。这些数据也可以存储在关系数据库中——每种对象类型有一个表，通过外键来建模它们与其他对象的关系。然后，可以使用*SQL*查询中的连接来实现数据处理。随着*NoSQL*解决方案的趋势[[8](https://neo4j.com/blog/hello-world-neo4j-inc/)]，根据具体的使用场景，使用除*SQL*之外的数据库变体变得更为可接受。  

如果你在犹豫是否应该选择图数据库，看看这个概述：  

![](../Images/f7d80ac6c19881e153780f518a475125.png)  

重要的数据库类型及其典型应用和最流行的代表（照片由作者提供）  

# 图数据库Neo4j  

图数据库的第一个，也是最著名的代表，是Neo4j。这个名字原本计划为NeoDB，但NeoDB.com在发布时已被占用，所以团队决定将应用命名为Neo4j。  

+   “Neo”一词是拉丁语中表示“新的”意思，灵感来源于电影《黑客帝国》中的角色Neo。[[19](https://twitter.com/emileifrem/status/712327903032188928)]  

+   4j意味着*“为Java”*。[[8](https://neo4j.com/blog/hello-world-neo4j-inc/), [9](https://www.youtube.com/watch?v=YB723cp9jgM)]  

今天，Neo4j不再是一个嵌入式Java应用程序，但它的名称中仍然保留了一段历史。  

## Neo4j的历史  

![](../Images/1fdb03997b6a1c139560251dcaff8819.png)  

Neo4j历史上重要事件的自创时间轴（照片由作者提供）  

**2000年：概念化。** 创始人们对基于关系数据库(*RDMBS*)的内容管理系统（*CMS*）感到困扰，该系统使用的是Informix。[[15](https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog)]。在*CMS*中实现他们的用例导致了大量复杂的SQL查询的编写，而这些查询越来越难以维护。  

创始人们认为他们的数据是相互连接的，构成了我们相关内容项目、元数据、标签和元标签之间的一种路径[[12](https://neo4j.com/blog/evolution-neo4j-native-graph-database/)]，这最终促使了属性图模型的发展。  

**2000–2002: 在RDBMS之上的图层。** 第一步是在名为Informix的关系数据库之上编写一个图层[[12](https://neo4j.com/blog/evolution-neo4j-native-graph-database/)]。[[15](https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog)]

**2002: Neo4j的第一个版本。** 在Informix之上的图层遇到了一些挑战。问题在于，Informix并未针对处理图状数据之间的所有关系进行优化[[15](https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog)]。

面对使用RDBMS处理连接数据的挑战，开发者决定创建一种针对连接数据优化的新型数据库[[15](https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog)]。

**2007: 图数据库一词的发明。** 图数据库这一词由Emil Eifrem发明，基于Facebook营销声明“我们是社交图谱的工具”而产生。他将“图”和“数据库”两个词结合，这就诞生了*图数据库*这一术语[[16](https://neo4j.com/news/birth-graph-databases-neo4j-built-product-category/)] [[17](https://www.forbes.com/sites/alastairdryburgh/2017/03/22/growth-stories-the-magical-power-of-a-name/#49b4ebe56db9)]。

**2007: Neo4j Technologies的创立。** 这家公司成立了，但主要从事咨询工作，因为当时还没有自己的产品可以销售[[17](https://www.forbes.com/sites/alastairdryburgh/2017/03/22/growth-stories-the-magical-power-of-a-name/#49b4ebe56db9)]。

**2007: 图数据库完全本地化。** 原型变成了一个完全本地化的图数据库[[18](https://neo4j.com/open-source-project/)]。最初它是作为一个嵌入式Java数据库启动的[[8](https://neo4j.com/blog/hello-world-neo4j-inc/)]。

**2010: Neo4j 1.0的发布。** [[18](https://neo4j.com/open-source-project/)]

**2011: Cypher的开发。** 第一个面向属性图的声明式查询语言诞生了[[18](https://neo4j.com/open-source-project/)]。它的灵感来自于MS Visio中将对象和关系放置在用户界面上的方式，因此它是一种非常人性化的查询模型[[8](https://neo4j.com/blog/hello-world-neo4j-inc/)]。

随着时间的推移，Neo4j从一次飞行中草拟的原型[[21](https://www.youtube.com/watch?v=Nhi4XwmCh9A)]，发展为一个独立的数据库应用，满足了客户的需求。这些需求也反映在解决方案的架构中，接下来会进行阐述。

## 利益相关者

要了解*Neo4j*的架构，必须理解可能影响架构决策的利益相关者，正如*Rozanski和Woods*所述。[20]

以下权力/利益矩阵列出了应用程序的利益相关者，并将他们置于不同的视角，以可视化他们对架构决策的影响可能性。

![](../Images/cdd79a2e981acf8278ad4e70c8e48d14.png)

Neo4j的权力/利益矩阵（作者拍摄的照片）

那些既拥有高权力又有高兴趣的利益相关者，如Neo4j的投资者[[22](https://www.crunchbase.com/organization/neo-technology)]、Neo4j公司本身及其合作伙伴[[23](https://neo4j.com/partners/directory/)]，对软件架构决策有着重要的影响力。需要密切管理他们。[[24](https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid)]

尽管会考虑到企业客户的需求和要求，但由于他们在软件上的投资不如其他利益相关者，因此他们的影响相对有限。需要保持他们的满意度。[[24](https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid)]

竞争者可能通过挑战其市场地位间接影响Neo4j的方向。开发者通常已经为他们的产品设定了技术栈，并没有足够的能力单独影响Neo4j的架构。他们需要被监控。[[24](https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid)]

*开源社区*和*个人贡献者*在塑造软件架构的讨论和发现漏洞方面起着重要作用。尽管如此，他们的影响力有限。正确的做法是保持他们的知情。[[24](https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid)]

## 依赖关系

为了更好地理解Neo4j所基于的架构，了解其当前的依赖关系以及这些依赖关系如何塑造Neo4j成为今天的解决方案是至关重要的。

![](../Images/d6eb89fbb5fd323668111b7c8510da6d.png)

Neo4j的依赖关系上下文（作者提供的照片）

## 架构目标

下表展示了Neo4j的主要架构目标，表中顺序代表其重要性。现在，让我们探索软件为实现这些目标所采用的各种技术。

![](../Images/f07db01393b3f195e9440e59931b0a6d.png)

达成所有架构目标（作者提供的照片，插图来自[Takashi Mifune，使用自由许可](https://www.irasutoya.com/p/terms.html)）

**1.** 🟢 **易用性。** 这是一个独立的平台，具有图形原生的数据建模、用户友好的查询语言Cypher以及易于理解的全面开发者文档。这使得使用Neo4j变得简单，所有相关的利益相关者都能获得良好的体验。(*开发者视角*)

**2.** 🟠 **性能。** Neo4j是一个图形原生数据库，配备了多种优化功能，确保复杂图形数据的快速查询响应时间。该平台还拥有高并发性和一致性特性。此外，图算法的运行效率非常高。

**3\.** 🔵 **可靠性。** 对任何数据库平台来说，可靠性至关重要。Neo4j 通过 ACID 事务、监控、事件日志记录、TLS 数据加密和权限系统实现这一点。

**4\.** 🟣 **安全性。** 数据涉及时，安全性尤为重要。Neo4j 通过事件日志记录、TLS 数据加密、精细粒度的权限系统以及静态数据加密来实现安全。

**5\.** 🟡 **互操作性。** 能在所有相关的目标系统上运行。能够轻松集成到现有环境中。支持多种数据访问客户端。

**6\.** 🔴**可用性。** 能在集群中运行，从而提高可用性以满足企业需求。

**7\.** 🟤 **可扩展性。** 支持水平和垂直扩展，能够处理越来越多的数据和查询量。

**8\.** ⚪ **可扩展性。** 无论是 Neo4j 开发团队还是外部社区开发者，都可以轻松扩展解决方案。这适用于修改现有组件或添加新功能。Neo4j 对新的技术和方法趋势持开放态度。

🟢 **易用性**

大多数开发人员至少对关系数据库的工作原理有基本的了解，但基于图的解决方案对大多数人来说是新的。为了确保 Neo4j 的成功，开发者能够迅速适应该解决方案，并在没有不必要障碍的情况下构建可行的产品至关重要。

![](../Images/5627e39dd1704c8a932f0dad501128e3.png)

公共交通的概念性和实施数据模型（作者提供的照片）

支持开发者适应的是，图形数据库通常与其概念数据模型相同。它们是无模式的，像大多数 NoSQL 解决方案一样。数据以节点和边的形式表示。

非开发人员可以通过交互式 Neo4j 浏览器探索数据。这包括从白板上的初步构想到使用 Cypher 语言（等同于 SQL）开发查询。

![](../Images/8c8765504ebfb8761d242c01f1d9ee75.png)

在 Neo4j 浏览器中探索数据，基于 Cypher 查询的结果（作者提供的照片）

Neo4j 高效执行涉及多个*节点*和*边*的复杂查询的能力，主要归功于其专用的图数据模型。关系直接以数据库中的数据结构形式体现，允许“指针”在没有多次子查询和连接的情况下遍历。Neo4j 不是基于现有数据库，而是一个高度专业化的实现。

🟠 **性能**

Neo4j 是一个高度优化的图形原生数据库，专门优化以处理大规模的图操作。图查询执行得更快、更高效，因为所有相关数据都存储在一个地方并以图的形式反映出来。

此外，Neo4j 实现了多个专为图形优化的性能提升：

+   [**B树**](https://en.wikipedia.org/wiki/B-tree) **用于快速检索图数据集中的节点和边**

+   **专为图形数据设计的索引**，可以为单个属性定义多个索引选项

⚪ **可扩展性**

Neo4j 提供了官方的 Bolt 驱动程序，支持 .NET、Java、JavaScript、Go 和 Python。社区实现的驱动程序也可以用于 C/C++ 和 PHP。此外，无论使用什么编程语言，都可以通过 HTTP API 访问 Neo4j 数据库。

有时，开发者需要通过创建自己的过程或函数来扩展 Neo4j 数据库的功能。Neo4j 提供了插件和未管理的服务器扩展，用于 HTTP 端点，支持此功能。文档提供了 JVM 语言的实现指南。

Neo4j 提供了多种方式来扩展其功能并保持与趋势的同步。开发者可以通过 Cypher 使用 Bolt 和 HTTP 访问协议，或创建自己的插件来改进 Neo4j 软件。

Neo4j 最近的更新包括 GraphQL 集成和对流行流媒体解决方案（如 Kafka 和 Spark）的连接器。此外，Neo4j 提供了一个 [Graph Data Science 库](https://neo4j.com/docs/graph-data-science/current/)，提供多种算法和机器学习建模选项，涵盖与数据分析和机器学习相关的用例。

**全局视野（**🟢/🟠/🔵/🟣/🟡/🔴/🟤/⚪）

现在，我们已经探索了一些个别架构目标以及 Neo4j 如何解决它们，让我们退一步，展望整体架构图。

想象一个互联的架构目标图，每个节点代表一个特定目标，例如数据一致性或可用性，每个关系代表这些目标如何相互关联或依赖：

![](../Images/c09162dc76a8d14b5a770417f8c7146b.png)

Neo4j 企业架构目标图（作者提供的照片）

## 架构结构

为了更好地理解 Neo4j 如何实现其架构目标，我们应从更技术的角度来看待该解决方案。

![](../Images/6b1d7c35406237aaef2debe9e4c16f91.png)

关于典型 Neo4j 设置的粗略概览 [22]（作者提供的照片）

**Neo4j 对软件架构师的优势**

现在你已经掌握了有关图形数据库和 Neo4j 的所有信息。那么，如何利用这些信息呢？使用图形数据库可以立即为你和你的工作带来积极的影响：

+   高性能的复杂查询

+   数据建模的更大灵活性

+   处理非结构化数据的能力

+   改进的可扩展性和可用性

+   更容易与其他技术集成

+   更高的业务敏捷性

## 结论

Neo4j 因其处理图形数据的能力而在现代软件架构中获得认可。但今天，它已经不仅仅是处理图形数据的万能解决方案。

对于今天的软件架构师而言，选择专用数据库来处理特定用例非常重要，而不是围绕单一数据后端构建完整的解决方案。尤其是在微服务架构仍在持续增长的情况下，这一点尤为有效。

如果你还没有开始，我个人鼓励你探索一些测试用例，以发现图数据库的强大功能以及它们如何为你带来益处。无论是物流、金融、医疗、社交媒体还是电子商务，图数据库都能提供传统数据库无法提供的洞察。

# 来源

[1, 2] Silvescu, Adrian & Caragea, Doina & Atramentov, Anna. (2002). 图数据库。

[3] DB-Engines 图数据库管理系统排名 2024 [https://db-engines.com/en/ranking/graph+dbms)](https://db-engines.com/en/ranking/graph+dbms))

[4] 德国联邦银行 (2022年7月7日). 2021年德国支付行为报告。 [https://www.bundesbank.de/en/press/press-releases/payment-behaviour-in-germany-in-2021-894120](https://www.bundesbank.de/en/press/press-releases/payment-behaviour-in-germany-in-2021-894120)

[5] Leonhardt, M. (2021年6月3日). 美国在线欺诈尝试增加了25%——原因在这里。 *CNBC*. [https://www.cnbc.com/2021/06/03/why-online-fraud-attempts-are-up-25percent-in-the-us.html](https://www.cnbc.com/2021/06/03/why-online-fraud-attempts-are-up-25percent-in-the-us.html)

[6] Clark, Nicola (2016年4月5日). [“一条神秘信息‘对数据感兴趣？’是如何引发巴拿马文件的”](https://www.nytimes.com/2016/04/06/business/media/how-a-cryptic-message-interested-in-data-led-to-the-panama-papers.html). *纽约时报*. [ISSN](https://en.wikipedia.org/wiki/ISSN_(identifier)) [0362–4331](https://www.worldcat.org/issn/0362-4331). [存档](https://web.archive.org/web/20160815215759/http://www.nytimes.com/2016/04/06/business/media/how-a-cryptic-message-interested-in-data-led-to-the-panama-papers.html) 于2016年8月15日。

[7] [“关于巴拿马文件调查”](https://www.icij.org/investigations/panama-papers/pages/panama-papers-about-the-investigation/). *国际调查记者联盟*. 2018年1月31日。 [存档](https://web.archive.org/web/20200724235914/https://www.icij.org/investigations/panama-papers/pages/panama-papers-about-the-investigation/) 于2020年7月24日。

[8] Emil Eifrem (2017年7月27日), 你好，世界：Neo4j公司 [https://neo4j.com/blog/hello-world-neo4j-inc/](https://neo4j.com/blog/hello-world-neo4j-inc/)

[9] Dr. Jim Webber (2022年6月8日). [Neo4j的个人历史](https://www.youtube.com/watch?v=YB723cp9jgM&t=201s). *Neo4j Inc.* [https://www.youtube.com/watch?v=YB723cp9jgM](https://www.youtube.com/watch?v=YB723cp9jgM)

[10] 美国联邦调查局 (2023). 网络犯罪报告。 [https://www.ic3.gov/Media/PDF/AnnualReport/2023_IC3Report.pdf](https://www.ic3.gov/Media/PDF/AnnualReport/2023_IC3Report.pdf)

[11] Gopala Kr (2017). Neo4j架构。 [https://github.com/gopala-kr/10-weeks/blob/master/Projects-Blogs/07-bigdata-databases/neo4j-architecture.md](https://github.com/gopala-kr/10-weeks/blob/master/Projects-Blogs/07-bigdata-databases/neo4j-architecture.md)

[12] Dr. Jim Webber（2017年5月2日），Neo4j作为原生图数据库的工程演变。[https://neo4j.com/blog/evolution-neo4j-native-graph-database/](https://neo4j.com/blog/evolution-neo4j-native-graph-database/)

[13] William Lyon（2018年12月3日），在Neo4j中对巴拿马文件数据的图形可视化，[https://medium.com/neo4j/graph-visualization-of-panama-papers-data-in-neo4j-9c08ca17039c](https://medium.com/neo4j/graph-visualization-of-panama-papers-data-in-neo4j-9c08ca17039c)

[14] ggszk（2020年），Neo4j示例数据库：东京铁路路线（日本语），[https://github.com/ggszk/neo4j-train-route-sample](https://github.com/ggszk/neo4j-train-route-sample)

[15] Emil Eifrem（2016年3月29日），DB-Engines，Informix与Neo4j：起源故事，[https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog](https://neo4j.com/blog/db-engines-informix-neo4j/?ref=blog)

[16] Emil Eifrem (未知)，图数据库的诞生：Neo4j如何构建其产品和类别，[https://neo4j.com/news/birth-graph-databases-neo4j-built-product-category/](https://neo4j.com/news/birth-graph-databases-neo4j-built-product-category/)

[17] Alastair Dryburgh（2007年3月22日），成长故事：名字的神奇力量，[https://www.forbes.com/sites/alastairdryburgh/2017/03/22/growth-stories-the-magical-power-of-a-name/#49b4ebe56db9](https://www.forbes.com/sites/alastairdryburgh/2017/03/22/growth-stories-the-magical-power-of-a-name/#49b4ebe56db9)

[18] Neo4j公司，Neo4j的历史——开源，大社区，[https://neo4j.com/open-source-project/](https://neo4j.com/open-source-project/)

[19] Emil Eifrem（2016年3月22日），Twitter帖子，[https://twitter.com/emileifrem/status/712327903032188928](https://twitter.com/emileifrem/status/712327903032188928)

[20] Rozanski, Nick 和 Eóin Woods。《软件系统架构：与利益相关者一起使用视角和观点》。Addison-Wesley，2012年。

[21] Emil Eifrem（2022年8月8日），这个数据库的第一个代码是在印度孟买的IIT编写的，[https://www.youtube.com/watch?v=Nhi4XwmCh9A](https://www.youtube.com/watch?v=Nhi4XwmCh9A)

[22] Crunchbase，Neo4j简介，[https://www.crunchbase.com/organization/neo-technology](https://www.crunchbase.com/organization/neo-technology)

[23] Neo4j公司，合作伙伴目录，[https://neo4j.com/partners/directory/](https://neo4j.com/partners/directory/)

[24] Latha Thamma Reddi（2023年4月14日），使用权力利益网格进行利益相关者分析，[https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid](https://www.projectmanagement.com/wikis/368897/stakeholder-analysis--using-the-power-interest-grid)

图标由[かわいいフリー素材集 いらすとや (irasutoya.com)](https://www.irasutoya.com/)，© Takashi Mifune

![](../Images/cda231b38af31aa11c49c38bee9c0acc.png)

（作者提供的照片，插图由[Takashi Mifune在免费使用许可下提供](https://www.irasutoya.com/p/terms.html)）
