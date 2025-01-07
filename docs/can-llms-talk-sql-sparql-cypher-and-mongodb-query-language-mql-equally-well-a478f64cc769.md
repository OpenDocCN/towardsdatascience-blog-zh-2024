# LLM在生成SQL、SPARQL、Cypher或MongoDB查询时表现更好吗？

> 原文：[https://towardsdatascience.com/can-llms-talk-sql-sparql-cypher-and-mongodb-query-language-mql-equally-well-a478f64cc769?source=collection_archive---------3-----------------------#2024-12-09](https://towardsdatascience.com/can-llms-talk-sql-sparql-cypher-and-mongodb-query-language-mql-equally-well-a478f64cc769?source=collection_archive---------3-----------------------#2024-12-09)

## 我们的NeurIPS’24论文通过一个全新的独特公开数据集和基准，揭示了这一尚未得到充分研究的话题。

[](https://medium.com/@jf987?source=post_page---byline--a478f64cc769--------------------------------)[![Jonathan Fürst](../Images/241a54ac752c0072f6e4b053bb5dbd90.png)](https://medium.com/@jf987?source=post_page---byline--a478f64cc769--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a478f64cc769--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a478f64cc769--------------------------------) [Jonathan Fürst](https://medium.com/@jf987?source=post_page---byline--a478f64cc769--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a478f64cc769--------------------------------) ·阅读时长8分钟·2024年12月9日

--

![](../Images/5dc8d2837216f00fd34f96693435a3d4.png)

（图片由作者提供）

许多近期的研究集中在如何使用LLM从自然语言问题生成SQL。然而，关于LLM在直接比较中能生成多少种其他数据库查询语言的理解仍然较少。为了解答这个问题，我们创建了一个全新的数据集和基准，包含了10K个问题-查询对，涵盖了四种数据库和查询语言。我们评估了来自OpenAI、Google和Meta的几款相关的闭源和开源LLM，并结合常见的上下文学习（ICL）策略进行评估。相关论文*“SM3-Text-to-Query: Synthetic Multi-Model Medical Text-to-Query Benchmark”* [1]已在2024年NeurIPS会议的Dataset and Benchmark专场发布([https://arxiv.org/abs/2411.05521](https://arxiv.org/abs/2411.05521))。

所有代码和数据都可以在[https://github.com/jf87/SM3-Text-to-Query](https://github.com/jf87/SM3-Text-to-Query)上找到，您可以使用这些资源测试自己在四种查询语言下的Text-to-Query方法。但是在我们研究Text-to-Query之前，首先让我们回顾一下更常见的Text-to-SQL范式。

# **什么是Text-to-SQL？**

文本到 SQL（也称为 NL 到 SQL）系统将提供的自然语言问题翻译成相应的 SQL 查询。SQL 作为结构化数据源的主要查询语言（[关系模型](https://en.wikipedia.org/wiki/Relational_database)），为应用程序开发人员提供了声明性接口，以便访问信息。因此，文本到 SQL 系统旨在使非 SQL 专业的用户仅通过自然语言提出请求，从而访问和满足他们的信息需求。

![](../Images/73a5a93dadc3e6942e2f3b55583c2bd2.png)

图 1\. 文本到 SQL 概述。用户用自然语言提问，系统将其翻译成相应的 SQL 查询。该查询将在关系数据库（如 PostgreSQL）上执行，结果将返回给用户。(*图像来自作者*)

最近，文本到 SQL 方法的普及度和生成能力取得了显著进展。从文本到 SQL 的准确率可以轻松地看出这一点，在流行的基准 Spider（[https://yale-lily.github.io/spider](https://yale-lily.github.io/spider)）上达到了 90%，在更新且更复杂的 BIRD 基准（[https://bird-bench.github.io/](https://bird-bench.github.io/)）上则达到了 74%。这种成功的核心在于

基于 transformer 的语言模型，从 Bert [2]（340M 参数）和 Bart [3]（148M 参数）到 T5 [4]（3B 参数），再到大型语言模型（LLMs）的出现，如 OpenAI 的 GPT 模型、Anthropic Claude 模型或 Meta 的 LLaMA 模型（高达数百亿参数）。

# 超越关系数据库：文档与图形模型

尽管许多公司和组织内部的结构化数据源确实存储在关系数据库中，并通过 SQL 查询语言进行访问，但还有其他核心数据库模型（通常也被称为 [NoSQL](https://en.wikipedia.org/wiki/NoSQL)），它们在数据建模的易用性、查询性能和查询简洁性方面各有利弊：

+   **关系数据库模型。** 在此模型中，数据存储在具有固定、难以演变的模式中的表格（关系）中，模式定义了表格、列、数据类型和关系。每个表格由行（记录）和列（属性）组成，每一行代表表格描述的实体的唯一实例（例如，医院中的患者），每一列代表该实体的特定属性。关系模型通过约束（如主键，主键唯一标识每条记录）和外键（外键建立表格之间的关系）来强制数据完整性。数据通过 SQL 进行访问。流行的关系数据库包括 [PostgreSQL](https://www.postgresql.org/)、[MySQL](https://www.mysql.com/) 和 [Oracle 数据库](https://www.oracle.com/database/)。

+   **文档数据库模型。** 在这里，数据以文档结构（层次化数据模型）存储，并具有易于演化的灵活模式。每个文档通常以JSON或BSON等格式表示，允许数据以嵌套结构的丰富形式展现。与关系型数据库不同，在关系型数据库中，数据必须符合预定义的模式，而文档数据库允许同一集合中的不同文档拥有不同的字段和结构，从而促进了快速的开发和迭代。这种灵活性意味着可以添加或删除属性，而不影响其他文档，非常适用于需求经常变化的应用程序。流行的文档数据库包括[MongoDB](https://www.mongodb.com/)、[CouchDB](https://couchdb.apache.org/)和[Amazon DocumentDB](https://aws.amazon.com/documentdb/)。

+   **图形数据库模型。** 在这里，数据以节点（实体）和边（关系）的形式表示在图结构中，从而能够建模复杂的关系和互联数据。该模型提供了灵活的模式，可以轻松容纳变化，因为可以在不改变现有结构的情况下添加新的节点和关系。图形数据库擅长处理涉及关系和遍历的查询，使它们非常适合用于社交网络、推荐系统和欺诈检测等应用。流行的图形数据库包括[Neo4j](https://neo4j.com/)、[Amazon Neptune](https://aws.amazon.com/neptune/)和[ArangoDB](https://arangodb.com/)。

# 从文本到SQL，再到文本到查询

数据库的选择以及底层的核心数据模型（关系型、文档、图形）对读写性能和查询复杂度有很大影响。例如，图形模型自然地表示多对多的关系，比如患者、医生、治疗方法和病情之间的联系。相比之下，关系型数据库需要进行可能非常昂贵的连接操作和复杂查询。文档数据库仅提供对多对多关系的基础支持，且旨在处理数据不是高度互联并且存储在具有灵活模式的文档集合中的场景。

![](../Images/f6ec18037a116f21550e321f3300417e.png)

图2\. 对于相同的用户请求，不同查询语言和数据库系统之间的差异。(*图片由作者提供*)

尽管这些差异在数据库研究和行业中早已为人所知，但它们对日益增长的文本到查询系统的影响，直到目前为止竟然没有得到研究。

# SM3-文本到查询基准测试

SM3-文本到查询是一个新的数据集和基准测试，支持跨四种查询语言（SQL、MongoDB查询语言、Cypher和SPARQL）和三种数据模型（关系型、图形、文档）进行评估。

![](../Images/5e822c38489b30798bba1588bb039c48.png)

图 3\. SM3-Text-to-Query基准构建。将合成患者数据生成与四种数据库的ETL过程结合起来，使得可以创建任意大的合成数据集。(*图像由作者提供*)

SM3-Text-to-Query是由使用Synthea创建的合成患者数据构建的。Synthea是一个开源的合成患者生成器，能够生成真实的电子健康记录（EHR）数据。它模拟患者随时间变化的医疗历史，包括各种人口统计学特征、疾病、药物和治疗。生成的数据随后被转换并加载到四种不同的数据库系统中：PostgreSQL、MongoDB、Neo4J和GraphDB（RDF）。

基于一组超过400个手动创建的[模板问题](https://github.com/jf87/SM3-Text-to-Query/tree/main/data/question_templates)和生成的数据，针对四种查询语言（SQL、MQL、Cypher和SPARQL），为每种语言生成了10K个问题-查询对。然而，基于合成数据生成过程，增加额外的模板问题或生成自己的患者数据也是完全可行的（例如，适应特定地区或使用其他语言）。甚至可以构建一个包含实际患者数据的（私有）数据集。

# Text-to-Query 结果

那么，当前的LLM在四种查询语言的生成任务中表现如何呢？从报告的结果中，我们可以得出三个主要的结论。

**结论 01：模式信息对所有查询语言都有帮助，但效果不一。**

模式信息对所有查询语言都有帮助，但其效果差异显著。利用模式信息的模型表现优于不使用模式信息的模型——尤其是在准确率急剧下降的单次尝试场景中。对于SQL、Cypher和MQL，效果可以提高两倍以上。然而，SPARQL的改善幅度较小。这表明，LLM可能已经熟悉了底层的模式（SNOMED CT，[https://www.snomed.org](https://www.snomed.org/)），这是一个常见的医学本体。

![](../Images/9252cfb4c048ce22dc5f26b16622539f.png)

图 4\. 模式信息对执行准确性的影响。(*图像由作者提供*)

**结论 02：通过上下文学习（ICL）添加示例可以提高所有LLM和查询语言的准确性；然而，改进的速率在查询语言之间差异较大。**

示例通过上下文学习（ICL）提高了所有LLM和查询语言的准确性。然而，改进的程度差异很大。对于最流行的查询语言SQL，较大的LLM（GPT-3.5、Llama3–70b、Gemini 1.0）在零样本模式下通过架构输入已显示出约40%的稳定基准准确性，增加约10%的得分通过五次示例。然而，模型在没有示例的情况下对SPARQL和MQL等不常见的查询语言表现较差。例如，**SPARQL的零样本准确率低于4%。**但通过**五次示例，它迅速飙升至30%，**这表明ICL在提供相关示例时能帮助模型生成更准确的查询。

![](../Images/70eb4bf3f675b398aa8a2c6d27c37e8e.png)

图5\. 通过少量示例进行的上下文学习（ICL）影响。（*图片来源：作者*）

**第03课：LLMs在不同查询语言中的训练知识水平存在差异**

LLM在不同查询语言中的熟练程度存在差异。这可能源于它们的训练数据来源。对Stack Overflow帖子进行的分析支持了这一假设。不同查询语言的帖子频率存在很大差异：

+   [SQL]: 673K 条帖子

+   [SPARQL]: 6K 条帖子

+   [MongoDB, MQL]: 176K 条帖子

+   [Cypher, Neo4J]: 33K 条帖子

这与零样本准确性结果直接相关，其中SQL以47.05%的最佳模型准确率领先，其次是Cypher和MQL，分别为34.45%和21.55%。SPARQL仅为3.3%。这些发现与现有研究[5]一致，表明像Stack Overflow这样的平台上问题的频率和时效性显著影响LLM的表现。一个有趣的例外是MQL，它的表现比Cypher差，这可能是因为MQL查询的复杂性和长度。

# 结论

SM3-Text-to-query是首个针对越来越多由大型语言模型（LLMs）推动的Text-to-Query系统的跨查询语言和跨数据库模型评估的数据集。现有研究主要集中在SQL上，其他重要的查询语言尚未得到充分研究。这个新的数据集和基准测试首次允许对四种相关查询语言进行直接比较，使其成为研究人员和实践者设计和实施Text-to-Query系统的宝贵资源。

初步结果已经提供了许多有趣的见解，我鼓励你查看完整的论文[1]。

# 亲自试试

所有代码和数据都已开源，网址为[https://github.com/jf87/SM3-Text-to-Query](https://github.com/jf87/SM3-Text-to-Query)。欢迎贡献。在后续的文章中，我们将提供一些关于如何部署不同数据库并尝试你自己的Text-to-Query方法的实践指南。

[1] Sivasubramaniam, Sithursan, Cedric Osei-Akoto, Yi Zhang, Kurt Stockinger, 和 Jonathan Fuerst. “SM3-Text-to-Query：合成多模型医学文本到查询基准。”载于 *第三十八届神经信息处理系统会议数据集与基准轨道*。

[2] Devlin, Jacob. “BERT：用于语言理解的深度双向转换器的预训练。” *arXiv预印本 arXiv:1810.04805*（2018年）。

[3] Mike Lewis, Yinhan Liu, Naman Goyal, Marjan Ghazvininejad, Abdelrahman Mohamed, Omer Levy, Veselin Stoyanov, 和 Luke Zettlemoyer. 2020年. [BART：去噪序列到序列预训练用于自然语言生成、翻译和理解](https://aclanthology.org/2020.acl-main.703)。载于 *第58届计算语言学协会年会论文集*，第7871–7880页，在线发布。计算语言学协会。

[4] Raffel, Colin, Noam Shazeer, Adam Roberts, Katherine Lee, Sharan Narang, Michael Matena, Yanqi Zhou, Wei Li, 和 Peter J. Liu. “探索统一文本到文本转换器在迁移学习中的极限。” *机器学习研究期刊* 21, 第140期（2020年）：第1–67页。

[5] Kabir, Samia, David N. Udo-Imeh, Bonan Kou, 和 Tianyi Zhang. “Stack Overflow 是否过时？对 ChatGPT 回答 Stack Overflow 问题特点的实证研究。”载于 *2024年CHI计算机系统人因会议论文集*，第1–17页。
