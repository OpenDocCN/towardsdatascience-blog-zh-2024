# OMOP与DataSHIELD：提升隐私保护医疗分析的完美匹配？

> 原文：[https://towardsdatascience.com/omop-datashield-a-perfect-match-to-elevate-privacy-enhancing-healthcare-analytics-041b425d532a?source=collection_archive---------5-----------------------#2024-07-03](https://towardsdatascience.com/omop-datashield-a-perfect-match-to-elevate-privacy-enhancing-healthcare-analytics-041b425d532a?source=collection_archive---------5-----------------------#2024-07-03)

## 探索DataSHIELD与OHDSI/OMOP之间的协同效应，以促进协作医疗分析

[](https://medium.com/@eric.boernert?source=post_page---byline--041b425d532a--------------------------------)[![Eric Boernert](../Images/3038e5e8f0cd468ec69794caecf32bba.png)](https://medium.com/@eric.boernert?source=post_page---byline--041b425d532a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--041b425d532a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--041b425d532a--------------------------------) [Eric Boernert](https://medium.com/@eric.boernert?source=post_page---byline--041b425d532a--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--041b425d532a--------------------------------) ·阅读时间：8分钟·2024年7月3日

--

# 背景

跨境或多站点的数据共享可能会面临挑战，原因包括法规和法律的差异，以及有关数据隐私、安全性和所有权的担忧。然而，进行这些共享的需求正在增长。

大规模跨国和多站点的临床研究可以生成更强大和及时的证据，从而改善医疗保健。为了解决这一问题，罗氏的联邦开放科学团队相信，联邦分析（增强隐私的去中心化统计分析）是促进更多多站点和数据驱动的合作的有前景的解决方案。

高质量（经过筛选的）患者级别数据的可获得性和可访问性仍然是进展中的一个持续瓶颈。联邦模型是医学领域中进行协作分析和机器学习的一个推动因素，它无需将任何敏感的患者级数据转移。

# 用于分析的联邦模型

联邦范式的理念是将分析带到数据中，而不是将数据带到分析中。

这意味着数据保持在各自组织的边界内，协作分析工作并不意味着将数据复制到本地基础设施之外，也不意味着对数据进行无限制查询的访问。

它有许多优点，包括：

+   降低数据暴露风险

+   没有难以追踪和管理的数据副本离开场所

+   避免了构建数据湖的前期成本和努力

+   跨越监管边界

+   尝试不同分析方法和功能的互动方式

让我们用一个简化的例子来说明，假设有来自三个不同医院的糖尿病患者。假设外部数据科学家想要分析患者的平均年龄。

![](../Images/5e30176600ca047060ef46fb712f4bea.png)

*简化的联邦分析示意图*（图片来源：作者）

远程数据科学家并未完全获得数据拥有者的信任，不能访问数据，无法访问任何行级数据，也不能随意发送查询（如 `DataFrame.get`），但他们可以调用联邦函数，并在网络中获取聚合的均值。

数据拥有者允许远程数据科学家在指定的队列和变量（例如**年龄**）上运行联邦函数**均值**。

这些高级分析能力在进行观察性研究时为评估不同地区群体的治疗效果等提供了极大的附加值和支持。

这就是数据科学家使用流行的联邦分析解决方案DataSHIELD时的视角。

![](../Images/5243a22870b0e232b17949683c8d8604.png)

*分析脚本的截图*（图片来源：作者）

DataSHIELD是什么？

[DataSHIELD](https://wiki.datashield.org/en/comms/2023conf/workshops/exercises)是一个允许您在不查看数据或推断其中任何敏感信息的情况下分析敏感数据的系统。

它源于学术项目DataSHIELD（利物浦大学）和obiba.org（麦吉尔大学）。

它是一个开源解决方案，托管在[GitHub](https://github.com/datashield)上，这有助于建立信任和透明度，因为这段代码在数据拥有者的基础设施防火墙后运行。

它已经在市场上存在了超过[十年](https://pubmed.ncbi.nlm.nih.gov/25261970/)，并且在多个成功的项目中得到了应用。

![](../Images/d8633140f81d9b6bf4650691d5dfde4c.png)

*RStudio或Jupyter R笔记本是与联邦网络互动的常见方式*（图片来源：作者）

DataSHIELD的主要优势包括：

+   具有披露检查和智能聚合结果的高级联邦分析函数

+   联邦认证与授权，使数据拥有者能够完全控制谁对其数据做什么

+   用于自动化架构各部分的API

+   内置扩展机制，允许创建自定义联邦函数

+   额外功能的社区包

+   完全透明，所有代码都可在GitHub上找到

数据拥有者负责：

+   在其基础设施中部署本地DataSHIELD Opal和Rock节点

+   管理用户、权限（从函数到变量）

+   配置披露检查过滤器

+   审查和接受自定义函数及其本地部署

数据分析师是：

+   调用联邦函数并聚合结果，通常能提供高精度的结果，而不是使用元分析，且始终确保数据泄露保护。

+   编写和测试他们的自定义联邦函数，然后将其与网络共享，由数据拥有者在所有节点上部署并用于协作分析工作。

# 观察性健康数据科学与信息学的优势

[OHDSI](https://www.ohdsi.org/)以其数据协调和标准化著称，称为[观察性医疗结果合作伙伴关系（OMOP）通用数据模型（CDM）](https://ohdsi.github.io/CommonDataModel/)。

当前版本的标准是5.4，尽管它正在不断发展，以适应来自现实世界应用的反馈和新需求，但它已经成熟并得到了OHDSI生态系统中工具的支持，如[ATLAS](https://github.com/OHDSI/Atlas/wiki)、[HADES](https://ohdsi.github.io/Hades/)和[Strategus](https://ohdsi.github.io/Strategus/index.html)。

OHDSI技术栈已有十多年历史，拥有许多成功的实际应用。

OHDSI不要求医院和其他数据源将其数据或API暴露到互联网，因此可以通过将分析规范交付给数据拥有者来执行分析查询和算法，数据拥有者执行分析，审查输出结果并通过安全渠道将其发送到分析方。OHDSI提供端到端的工具来支持这一工作流程的所有步骤。

# 集成的商业价值

DataSHIELD虽然需要连接到其分析服务器的API（Opal），但它通过使用一组不泄露数据的分析函数和内置的高级泄露检查，提供了一种互动的分析数据方式，同时保护数据隐私。

这使得分析更加敏捷、探索性（在一定程度上），并使数据分析师能够尝试不同的分析方法，从数据中学习。

在传统的OHDSI方法中，代码固定在定义好的研究定义中，由数据拥有者手动执行。这导致获得结果的等待时间较长（依赖人工），可能需要几周甚至几个月，具体取决于各组织的情况。而在描述的联邦分析方法中，结果可以在几秒钟内获得。

另一方面，不需要手动审查返回给外部分析师的结果，数据拥有者应信任内置的联邦函数和泄露检查。同时，联邦方法需要互联网连接。

利益总结：

+   DataSHIELD使结果能够立即且自动地可用

+   内置联邦聚合提高了准确性

+   泄露保护保护原始数据

+   重新利用在OMOP CDM数据协调中的投资

+   通过使用OMOP进行协调来提高数据质量 → 更高质量的分析结果

换句话说，可以兼顾两全其美，以改进现实世界医疗保健应用中的分析结果。

# 集成场景

我们与 [DataSHIELD 团队](https://www.linkedin.com/company/datashield-os/) 合作，确定了四种主要的集成场景。我们的角色（联邦开放科学团队）不仅仅是表达我们对集成的兴趣和商业理由，而是定义可行的集成架构和概念验证定义。

## 选项 1\. 从 OMOP CDM 数据源提取、加载并转换（ETL）数据到 DataSHIELD 数据存储（项目开始时）。

![](../Images/832de0d9f699bd3b92b3742b397c26f4.png)

（图片由作者提供）

在这种方法中，我们使用经典的 ETL 方法从 OHDSI 数据源提取数据，并将其转换为即将成为数据源的数据，然后将其作为资源添加或直接导入到 DataSHIELD Opal 服务器。

## 选项 2\. 将 OMOP CDM 作为 DataSHIELD 中原生支持的数据源。

![](../Images/f2edd26c41ea173eb3423d54b33a61d5.png)

（图片由作者提供）

DataSHIELD 支持多种数据源（如 CSV 等平面文件、XML、JSON 等结构化数据、关系型数据库等），但不直接支持 OHDSI OMOP CDM 数据源。

dsOMOP 库（正在开发中）的目标是为 DataSHIELD 提供扩展，以便为 OMOP CDM 数据源提供一流的支持。

## 选项 3\. 使用 REST API 根据需要检索数据子集。

![](../Images/14dde9eebe5002ba84bdf1ab1887684b.png)

（图片由作者提供）

该选项不绕过 OHDSI 堆栈的 API 层，而是作为 DataSHIELD API 到 OHDSI 工具 API 的桥接、编排和翻译层。

## 选项 4\. 将 DataSHIELD 嵌入 OHDSI 堆栈中。

![](../Images/bd8f59f45e2a7b9e23fe6b6a65e3995f.png)

（图片由作者提供）

这意味着两个生态系统的深度集成，以最大化其效益，但需要付出较高的努力和两个团队（DataSHIELD 和 OHDSI 技术团队）之间的协调。

# 采用障碍

这两种解决方案和社区在使用各自的工具和方法进行成功的分析项目方面有着良好的记录。过去，DataSHIELD 方面曾有过有限的尝试来接纳 OMOP CDM 和查询库（即 [GitHub — sib-swiss/dsSwissKnife](https://github.com/sib-swiss/dsSwissKnife)，早期的 [https://github.com/isglobal-brge/dsomop](https://github.com/isglobal-brge/dsomop)）。

我们试图解决的主要问题是对联邦模型的认知仍然有限，我们在鹿特丹的 [OHDSI Europe 2024](https://ohdsi-europe.org/symposium-event) 研讨会上愉快地展示了这一点，并获得了非常积极的反馈，大家认识到未来集成的好处。来自数据分析师视角的联邦分析如何工作的实操演示非常有助于传达信息。关于计划集成的主要问题是“何时”而非“为什么”，我们认为这是一个好兆头，也是对未来的鼓励。

两个技术生态系统（DataSHIELD，OHDSI）都已经成熟，然而它们的整合正在开发中（截至2024年6月），尚未准备好投入生产。DataSHIELD可以在没有OMOP CDM的情况下使用，尽管数据质量和协调问题被认识到，OMOP从未是联合项目的直接要求或指导。

如果项目更多集中于长期合作而非单次分析，联合网络的价值可能会更高，建立网络的初始成本（从各个角度看）在多个研究执行时能够得到重复利用。目前，在这一领域已有进展的迹象，尽管大多数联合项目是单一研究项目。

# 未来步骤

我们对OHDSI和DataSHIELD整合的潜力和未来持乐观态度。这是行业期望发生的事情，并且得到了两个社区的热烈欢迎。

[dsOMOP R库](https://github.com/isglobal-brge/dsomop)在DataSHIELD中的开发最近得到了加速。

预计结果将提供数据源整合的端到端解决方案（策略二），并允许进一步发展和更紧密地合作这两个生态系统。预期整合的实际应用总是收集宝贵反馈和发现问题的最佳方式。

作者特别感谢[Jacek Chmiel](https://www.linkedin.com/in/jacekchmiel/)对本文的重大影响，以及以下帮助塑造此项目的人员：[Jacek Chmiel](https://www.linkedin.com/in/jacekchmiel/)、[Rebecca Wilson](https://www.linkedin.com/in/drbeccawilson/)、[Olly Butters](https://www.linkedin.com/in/ollybutters/)和[Frank DeFalco](https://www.linkedin.com/in/frank-j-defalco/)，以及Roche的Federated Open Science团队。
