# 构建PubMed数据集

> 原文：[https://towardsdatascience.com/building-a-pubmed-dataset-b1267408417c?source=collection_archive---------11-----------------------#2024-10-31](https://towardsdatascience.com/building-a-pubmed-dataset-b1267408417c?source=collection_archive---------11-----------------------#2024-10-31)

## 构建关于心血管疾病研究的PubMed收录文献数据集的逐步说明

[](https://dianarozenshteyn.medium.com/?source=post_page---byline--b1267408417c--------------------------------)[![Diana Rozenshteyn](../Images/8c202142e102622b3bde20a19121ad79.png)](https://dianarozenshteyn.medium.com/?source=post_page---byline--b1267408417c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b1267408417c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b1267408417c--------------------------------) [Diana Rozenshteyn](https://dianarozenshteyn.medium.com/?source=post_page---byline--b1267408417c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1267408417c--------------------------------) ·6分钟阅读·2024年10月31日

--

![](../Images/1acf884e635ebbf72e580141102f18d0.png)

图片由作者提供

# 挑战

当我开始撰写我的硕士论文《与NIH资助的心脏病研究中具有影响力的科学出版物相关的因素》时，第一项任务是构建一个原始数据集来进行研究。为了实现这一目标，我转向了PubMed，这是由美国国立医学图书馆（NLM）提供的一个免费的生物医学文献研究数据库。

## 该数据集需要满足多个特定标准，包括：

1.  跨越尽可能长的时间段。

1.  包含美国国立卫生研究院（NIH）资助的研究。

1.  仅专注于心血管疾病研究的文献。

1.  提供第一作者的详细信息，例如全名、性别、所在机构以及研究机构所在国家。

1.  包含每篇文章的引用次数、NIH百分位排名、文章中的参考文献总数以及其他与引用相关的数据。

1.  包括期刊的科学排名信息。

在本文中，我将解释如何根据这些标准创建一个PubMed收录文献的数据集。

两个限制因素——第一作者的完整姓名的可用性和引用发生所需的年数——被用来选择数据收集的时间段。PubMed记录从2002年开始包含完整的作者姓名（Full Author, FAU）[1]。此外，三年是进行引用和出版影响分析的最低推荐年数[2]。为了最大化数据集的大小，采用了至少两年的时间框架来积累引用，因为数据集是在2022年构建的。此外，2020年是当时用于数据分析的科学期刊排名（SJR）信息可用的最后一年[3]。因此，我在PubMed上搜索了2002年到2020年的记录，共创建了18个数据集——每年一个。限制因素的概述如下图所示。

![](../Images/47d3efea207b76fe56a2bbd7ec9cd8de.png)

图片由作者提供

我使用PubMed的高级搜索工具[4]构建了关于心血管疾病的出版物数据集。PubMed数据元素（字段）描述[1]被用来构建查询。NIH资助通过国立心脏、肺、血液研究所（NHLBI）资助来表示。我在查询中使用了NHLBI资助（[GR]）、出版日期（[DP]）等关键词，以及基于心血管疾病相关病症的关键词组合。这些关键词包括cardiovascular、ischemic和heart。

![](../Images/d8c467a15921ece4cdf9d38e61f1f1d2.png)

图片由作者提供

2020年PubMed查询示例：“cardiovascular OR ischemic OR heart AND NHLBI[GR] AND 2020[DP]”。

![](../Images/0965d74e066746676ce80c58bc60ac0f.png)

图片由作者提供

为了获取期刊名称、文章第一作者所属机构及其国家信息以便进一步解析，我通过选择显示选项菜单中的摘要格式选项和保存引用到文件菜单中的PubMed格式选项，保存了PubMed高级搜索查询。

![](../Images/921ba3159ff81c78444d4828d1b504ed.png)

图片由作者提供

为了获取每个出版物的PMID（PubMed唯一标识符）列表，以便进一步获取引用信息，我通过选择显示选项菜单中的摘要格式选项和保存引用到文件菜单中的PMID格式选项，保存了通过高级搜索PubMed查询收集的数据。

![](../Images/d9556036750373b185a4d1394a9c3ca7.png)

图片由作者提供

以下流程图概述了从PubMed网站下载PubMed和PMID文件后的步骤。更详细的解释见后文。

![](../Images/ecc0943210bc3662b276328db687a3bb.png)

图片由作者提供

为了获取引用相关信息和（如有）完整未缩写的作者名字，我将每年的PMID数据集上传到ICite Web工具[5]。我将结果数据分析保存为csv文件。ICite由NIH的投资组合分析办公室（OPA）运行。OPA是NIH的一个部门，负责基于数据的研究评估，帮助NIH决定哪些当前或新领域的研究将对科学和人类健康产生更大的益处。ICite提供了有关作者的完整名字、总引用次数、每年引用次数、一个经过领域和时间调整的科学影响力的引用衡量标准——相对引用比率（RCR），以及NIH百分位数的可用信息。

![](../Images/df3c65e0cb9abe3cb8701b2f5599f38d.png)

图片来自作者

PubMed格式数据集不能以CSV格式保存，因此必须解析以提取期刊标题（JT）、第一作者所属机构（AD）和国家。我为此编写了一个Python 3.10.1解析脚本。本文不讨论该脚本的详细内容，但我计划在未来的出版物中涉及。第一作者所属机构是通过向研究组织登记处（ROR）API [5]发出应用程序编程接口（API）请求来确定的。由于数据元素字段提供了研究机构不一致的名称以及如地址和部门名称等不必要的信息，因此需要进行ROR匹配。ROR匹配可以帮助查找在PubMed格式数据集中提到的研究机构，这些机构随后通过API调用提供。API调用的结果以JSON格式返回。我使用PubMed数据元素（字段）描述从PubMed格式数据集中解析期刊标题和国家。我分别处理了每年的数据集。以下是PubMed格式文件条目的示例。

![](../Images/615b93cccd0d1f76ecdd0982368cea4f.png)

图片来自作者

我在JupyterLab中处理了每年查询的解析过的PubMed格式数据集，并通过ICite引用数据集在PMID上合并它们。然后，我根据期刊名称将每年的合并数据集与SJR数据集进行合并。我从SCImago Journal & Country Rank网站[3]下载了最后可用年份（2020年）的SJR数据集。SCImago Journal & Country Rank数据库排名基于SJR指标。SJR指标是通过Scopus®数据库中的信息开发的，是衡量期刊科学影响力的一个标准。

![](../Images/4017a3585ba7e6c672bf88b4eb4b36f5.png)

图片来自作者

随后的步骤包括使用Gender-API Web服务估算第一作者的性别，并进行数据清理。这些步骤将在本刊物中不做详细讨论。

## 总结来说，自己构建数据集是有多方面益处的：

1.  定制化：你可以根据特定的研究需求定制数据集。

1.  数据理解：构建自己的数据集帮助你深入理解数据本身，包括其局限性和偏差。

1.  技能发展：它加强了如数据收集、清理、组织等关键研究技能。

1.  质量控制：通过自行构建数据集，你可以控制数据质量。

1.  灵活性：你可以在出现新问题时修改或扩展数据集。

1.  问题解决：这一过程促进了创造性问题解决，因为你需要开发收集、筛选和结构化数据以进行分析的方法。

这些好处提升了你的研究能力，并有助于产生更有影响力和更精确的结果。

本文使用的Jupyter Notebook可以在[GitHub](https://github.com/drozenshteyn/Building-a-PubMed-Dataset)找到。

这里引用的完整硕士论文也可以在[GitHub](https://github.com/drozenshteyn/Master-s-Thesis)找到。

感谢阅读，

Diana

# 参考文献

1.  美国国立医学图书馆，“MEDLINE/PubMed数据元素（字段）描述，”nlm.nih.gov。可用：[https://www.nlm.nih.gov/bsd/mms/medlineelements.html#fau](https://www.nlm.nih.gov/bsd/mms/medlineelements.html#fau)

1.  M. Thelwall，“1996–2014年27个领域和6个英语国家的引用影响性别差异，”《定量科学研究》，第1卷，第2期，页码599–617，2020年6月。

1.  SCImago，（无日期），“SJR — SCImago期刊与国家排名[门户]”，2020年，scimagojr.com。可用：[http://www.scimagojr.com](http://www.scimagojr.com)

1.  美国国立医学图书馆，“高级搜索结果 — pubmed，”可用：[https://pubmed.ncbi.nlm.nih.gov/advanced/](https://pubmed.ncbi.nlm.nih.gov/advanced/)

1.  研究组织注册，“ROR，”ror.org。可用：[https://ror.org/](https://ror.org/)
