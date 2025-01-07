# 如何在企业层面同时实施知识图谱和大型语言模型（LLMs）

> 原文：[https://towardsdatascience.com/how-to-implement-knowledge-graphs-and-large-language-models-llms-together-at-the-enterprise-level-cf2835475c47?source=collection_archive---------0-----------------------#2024-04-19](https://towardsdatascience.com/how-to-implement-knowledge-graphs-and-large-language-models-llms-together-at-the-enterprise-level-cf2835475c47?source=collection_archive---------0-----------------------#2024-04-19)

![](../Images/60d87195bccffe2ddafad1e92df165ff.png)

来源：[OpenArt SDXL](https://openart.ai/create?ai_model=openart%2FOpenArt_SDXL)

## 当前集成方法的调研

[](https://stevehedden.medium.com/?source=post_page---byline--cf2835475c47--------------------------------)[![Steve Hedden](../Images/af7bec4a191ab857eccd885dd89e88b4.png)](https://stevehedden.medium.com/?source=post_page---byline--cf2835475c47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf2835475c47--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cf2835475c47--------------------------------) [Steve Hedden](https://stevehedden.medium.com/?source=post_page---byline--cf2835475c47--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf2835475c47--------------------------------) ·阅读时长13分钟·2024年4月19日

--

[大型语言模型](https://en.wikipedia.org/wiki/Large_language_model)（LLMs）和[知识图谱](https://en.wikipedia.org/wiki/Knowledge_graph)（KGs）是提供更多人群访问数据的不同方式。KGs通过语义将数据集连接起来，即通过它们所代表的实体的含义。LLMs通过向量和深度神经网络来预测自然语言。它们通常都旨在“解锁”数据。对于实施KG的企业，最终目标通常是类似于[数据市场](https://www.snowflake.com/guides/what-data-marketplace/)、[语义层](https://enterprise-knowledge.com/the-top-3-ways-to-implement-a-semantic-layer/)，将数据[公平化](https://www.go-fair.org/fair-principles/fairification-process/)，或者使企业更加[以数据为中心](https://www.semanticarts.com/the-data-centric-revolution-gaining-traction-data-centric-manifesto/)。这些都是不同的解决方案，目标相同：让更多的数据更快地提供给正确的人。对于实施LLM或其他类似生成AI解决方案的企业，目标通常是类似的：为员工或客户提供一个“数字助手”，帮助更快地将正确信息提供给正确的人。潜在的协同效应是显而易见的：LLM的一些主要弱点——它们是黑箱模型，且在事实知识上表现较差——恰恰是KG的一些最大优势。KG本质上是事实的集合，并且是完全可解释的。但究竟KG和LLM应如何以及应该如何在企业中结合实施呢？

去年我在找工作时，必须写很多求职信。我使用ChatGPT来帮助——我会将我现有的求职信复制到提示窗口中，再加上我的简历和我所申请的职位的职位描述，然后让ChatGPT完成剩下的部分。ChatGPT帮助我快速起步，写出了几份相当不错的初稿，但如果不加以审查，它也给了我一些我并没有的工作经验，还声称我去过我从未就读过的学校。

我提到我的求职信是因为 1) 我认为它是LLM的优缺点的一个很好的例子，说明了KG在LLM实现中的重要性；2) 这个使用案例与许多大型企业当前使用LLM的方式并没有太大不同：自动化报告生成。ChatGPT在通过改变内容使其更加针对特定职位描述的同时，能够很好地重建求职信，**只要你在提示中明确包括现有的求职信和职位描述。确保LLM拥有正确的内容正是KG发挥作用的地方。**如果你只是写道：“为我想要的工作写一封求职信”，结果将是可笑的。此外，求职信示例是LLM的一个很好的应用，因为它涉及到总结和重组语言。还记得LLM中的第二个L代表什么吗？LLM历史上专注于非结构化数据（文本），而这正是它们的强项，而KG则擅长于*整合*结构化和非结构化数据。你可以使用LLM来写求职信，但你应该使用KG来确保它有正确的简历。

*注：我不是AI专家，但我对任何假装知道AI未来的人也有些怀疑。这个领域变化如此之快，根本无法跟上，更不用说预测企业层面AI实现的未来样貌了。以下是我认为当前KG和LLM集成的一些方式，这绝不是一个全面的列表，我也愿意接受补充和建议。*

# KG和LLM的两种关联方式

目前，知识图谱（KG）和LLM的互动方式有两种：LLM作为构建KG的工具，以及KG作为LLM或生成型人工智能（GenAI）应用的输入。我们这些在知识图谱领域工作的人，正处在一个奇怪的位置：我们在构建旨在改善AI应用的事物的同时，AI又在改变我们构建这些事物的方式。我们被期望在日常工作中将AI作为工具进行优化，同时改变我们的输出，以促进AI的优化。这两种趋势相互关联，且常常重叠，但我将在下面逐一讨论它们。

# 使用LLM（大语言模型）辅助知识图谱的创建和管理过程

LLM是构建KG的有价值工具。在KG管理过程中，利用LLM技术的一个方法是通过将KG向量化（或嵌入）到向量数据库中。向量数据库（或向量存储）是为存储向量或数字列表而构建的数据库。向量化是驱动语言模型的核心技术组件之一（如果不是唯一的核心技术的话）。这些模型通过大量的训练数据，学会将单词与向量关联起来。这些向量根据训练数据中的上下文，捕捉关于单词的语义和句法信息。通过使用经过大量数据训练的嵌入服务，我们可以在KG中利用这些语义和句法信息。

*注：将知识图谱向量化绝不是使用LLM技术进行知识图谱策划和构建的唯一方式。此外，LLM在这些应用中的使用对于知识图谱的创建并不新鲜。例如，NLP已经被用于实体提取几十年，LLM只是作为一种新的能力来协助本体论专家/分类法专家。*

大型语言模型（LLM）在知识图谱创建过程中可以提供帮助的一些方式包括：

+   [**实体解析：**](/an-introduction-to-entity-resolution-needs-and-challenges-97fba052dde5)实体解析是对指向同一现实世界实体的记录进行对齐的过程。例如，扑热息痛（acetaminophen），一种在美国常用的止痛药，以泰诺（Tylenol）为品牌销售，在英国被称为对乙酰氨基酚（paracetamol），并以潘达洛（Panadol）为品牌销售。这四个名称看似毫无相似之处，但如果你将你的知识图谱（KG）嵌入到向量数据库中，向量将具备语义理解，知道这些实体是紧密相关的。

+   **无结构数据标注：**假设你想将一些无结构数据整合到你的知识图谱中。你有一堆文件名模糊的PDF文件，但你知道这些文件中有重要信息。你需要为这些文档添加文件类型和主题标签。如果你的主题分类法和文档类型分类法已经嵌入，所有你需要做的就是将文档向量化，向量数据库将能够识别出每个分类法中最相关的实体。

+   **实体和类别提取：**根据一组无结构数据创建或增强受控词汇，如本体论或分类法。实体提取类似于标注，但这里的目标是增强本体论，而不是将无结构数据整合到知识图谱中。假设你有一个地理本体论，并且你希望将城镇、城市、州等实例填充到其中。你可以使用大型语言模型（LLM）从一组文本中提取实体来填充本体论。同样，你也可以使用LLM从文本中提取类别和类别之间的关系。假设你忘记在本体论中包含“首都”这一类。LLM可能能够将其提取为新的类别或城市的一个属性。

# 使用知识图谱（KG）来驱动和管理生成式人工智能（GenAI）流水线

使用知识图谱（KG）来驱动和管理你的生成式人工智能（GenAI）流水线和应用程序有多种理由。根据[Gartner](https://www.gartner.com/en/articles/highlights-from-gartner-data-analytics-summit-2024)的说法，“到2025年，至少30%的生成式人工智能项目将在概念验证（POC）阶段之后因数据质量差、风险控制不足、成本上涨或商业价值不明确而被放弃。”知识图谱可以帮助提高数据质量、降低风险并减少成本。

## **数据治理、访问控制和合规性**

只有授权的人和应用程序才能访问某些数据并用于特定目的。通常，企业希望某些类型的人（或应用程序）以良好的治理方式与某些类型的数据进行交互。你如何知道哪些数据应该进入哪个生成型人工智能（GenAI）流程？你如何确保个人身份信息（PII）不会进入你希望所有员工都能与之对话的数字助手？答案就是数据治理。以下是一些额外的要点：

+   政策和法规可能会发生变化，尤其是在人工智能领域。即使你的人工智能应用程序现在符合规定，未来也未必如此。一个良好的数据治理基础可以帮助企业适应这些变化的法规。

+   有时，问题的正确答案是“我不知道”，或者“你没有访问回答该问题所需的信息”，或者“回答这个问题对我来说是非法或不道德的”。回应的质量不仅仅是事实或准确性的问题，还涉及到合规性问题。

+   **实施或支持通过知识图谱（KG）进行数据治理的主要参与者（按字母顺序）：** 语义知识图谱公司，如[Cambridge Semantics](https://cambridgesemantics.com/)、[data.world](https://data.world/)、[PoolParty](https://www.poolparty.biz/)、[metaphacts](https://metaphacts.com/)和[TopQuadrant](https://www.topquadrant.com/)，还有数据目录如[Alation](https://www.alation.com/)、[Collibra](https://www.collibra.com/us/en/demo)和[Informatica](https://www.informatica.com/)（以及更多）。

## **准确性和上下文理解**

知识图谱（KG）还可以帮助提高整体数据质量——如果你的文档充满了矛盾和/或错误的陈述，当你的聊天机器人告诉你不一致和错误的信息时，不要感到惊讶。如果你的数据结构不良，将其存储在一个地方也不会有帮助。这就是数据湖的承诺如何变成数据沼泽的灾难。同样，如果你的数据结构不良，将其向量化也无法解决你的问题，它只会创造一个新的头痛问题：向量化的数据沼泽。然而，如果你的数据结构良好，知识图谱可以为大型语言模型（LLMs）提供额外的相关资源，从而以多种方式生成更个性化和准确的推荐。有多种方法可以利用知识图谱提高大型语言模型的准确性，但它们通常都属于[自然语言查询](https://www.ontotext.com/knowledgehub/fundamentals/what-is-natural-language-querying/)（NLQ）范畴——使用自然语言与数据库互动。据我所知，目前实现NLQ的方式包括RAG、提示到查询（prompt-to-query）和微调。

**检索增强生成（RAG）：** RAG指的是用训练数据之外的额外相关信息来补充提示，从而生成更准确的回答。尽管LLM已经在大量数据上进行过训练，但它们并没有在你的数据上进行训练。想象一下上面提到的求职信示例。我可以让LLM“为Steve Hedden写一封申请TopQuadrant公司产品管理职位的求职信”，它会返回一个回答，但其中可能包含虚假信息。一种更聪明的做法是让模型接收这个提示，检索Steve Hedden的LinkedIn资料，检索TopQuadrant公司该职位的职位描述，然后再写求职信。目前有两种主要的检索方式：通过将图谱向量化或将提示转换为图谱查询（提示到查询）。

+   **基于向量的检索：** 这种检索方法要求将你的知识图谱（KG）向量化，并将其存储在向量存储中。如果你随后将自然语言提示也向量化，你就可以在向量存储中找到与提示最相似的向量。由于这些向量对应于图谱中的实体，你可以根据自然语言提示返回图谱中最“相关”的实体。这与上述标记能力描述的过程完全相同——我们本质上是在用来自知识图谱的相关标签来“标记”提示。

+   **提示到查询检索：** 作为替代方案，你可以使用大语言模型（LLM）生成一个SPARQL或Cypher查询，并使用该查询从图谱中获取最相关的数据。*注意：你可以使用提示到查询的方法直接查询数据库，而不将结果用于补充LLM的提示。这将不属于RAG应用，因为你并没有“增强”任何内容。该方法在下面将进一步详细说明。*

关于RAG和两种检索方法的一些额外优缺点和说明：

+   RAG本质上需要一个知识库。知识图谱是一个知识库，因此知识图谱的支持者也会支持基于图谱的RAG（有时称为GraphRAG）。但是RAG也可以在没有知识图谱的情况下实现。

+   RAG可以根据提示的内容以及提示中的元数据，从你的KG中补充最相关的数据。例如，我们可以根据谁提出问题、他们可以访问哪些资源以及关于他们的其他人口统计信息来定制回答。

+   如上所述，使用基于向量的检索方法的一个好处是，如果你已将KG嵌入到向量数据库中进行标签和实体解析，困难的部分已经完成。找到与提示相关的最相关实体与用来自KG的实体对一段非结构化文本进行标记并没有什么不同。

+   RAG 提供了一定程度的可解释性。用户现在可以看到进入其提示的补充数据，以及潜在的，回答他们问题的答案所在的那些数据。

+   我在上面提到，人工智能正在影响我们构建知识图谱的方式，而我们被要求构建能够促进 AI 的知识图谱。提示到查询的方法就是一个完美的例子。知识图谱的架构将影响 LLM 查询的效果。如果知识图谱的目的是为 AI 应用提供数据，那么“最佳”本体不再是现实的反映，而是 AI 视角下对现实的反映。

+   理论上，更多相关的信息应该能够减少幻觉现象，但这并不意味着 RAG 可以消除幻觉。我们依然在使用语言模型生成回应，因此仍然存在大量的不确定性和幻觉。即使有我的简历和职位描述，一个大型语言模型（LLM）仍然可能夸大我的经验。对于文本查询方法，我们使用 LLM 来生成知识图谱（KG）查询和回应，因此实际上存在两个可能出现幻觉的地方。

+   同样，RAG 提供了一定程度的可解释性，但并非完全。例如，如果我们使用基于向量的检索，模型可以告诉我们它包含了哪些实体，因为这些实体是最相关的，但它无法解释为什么这些实体是最相关的。如果使用自动生成的 KG 查询，自动生成的查询“解释”了为什么图谱返回了某些数据，但用户需要理解 SPARQL 或 Cypher 才能完全理解为什么这些数据会被返回。

+   这两种方法并不互相排斥，许多公司正在同时追求这两种方法。例如，Neo4j 提供了关于实现 RAG 的教程，涉及 [基于向量的检索](https://neo4j.com/developer-blog/advanced-rag-strategies-neo4j/) 和 [从提示生成查询](https://www.youtube.com/watch?v=TB6URe5f3MA)。据我个人经验，我在参加一个重点讨论生命科学中知识图谱与 LLM 实施的会议后写下这些内容，许多我看到的生命科学公司都在做基于向量的和从提示到查询的 RAG 的组合。

+   **实施或支持 RAG 解决方案的著名玩家（按字母顺序）：** [data.world](https://page.data.world/ai-context-engine)，[Microsoft](https://www.microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data/)，[Neo4j](https://neo4j.com/developer-blog/advanced-rag-strategies-neo4j/)，[Ontotext](https://graphdb.ontotext.com/documentation/10.6/talk-to-graph.html)，[PoolParty](https://www.poolparty.biz/semantic-retrieval-augmented-generation)，[SciBite](https://scibite.com/scibite-chat/)，[Stardog](https://www.stardog.com/blog/how-ai-uses-stardog/)，[TopQuadrant](https://www.topquadrant.com/)（还有很多很多其他公司）

**仅使用提示-查询：** 使用LLM将自然语言查询转换为适用于你的KG的正式查询（例如SPARQL或Cypher）。这与上面描述的提示-查询检索方法（RAG）相同，只不过我们在数据检索后不会将其发送给LLM。这里的想法是，通过使用LLM来生成查询而非解释数据，你可以减少幻觉的发生。尽管如此，正如前面提到的，LLM生成的内容可能仍然包含幻觉。采用这种方法的论据是，用户更容易在自动生成的查询中发现幻觉，而不是在自动生成的响应中。我对这种说法有些怀疑，因为推测许多使用LLM生成SPARQL查询的用户可能对SPARQL不够熟悉，无法检测到自动生成查询中的问题。

+   任何实现了基于提示-查询检索的RAG解决方案的人，也可以实现仅使用提示-查询的解决方案。这些包括：[Neo4j,](https://www.youtube.com/watch?v=TB6URe5f3MA) [Ontotext,](https://www.ontotext.com/blog/natural-language-querying-of-graphdb-in-langchain/) 和 [Stardog](https://www.stardog.com/blog/a-vision-for-voicebox/)。

**用于微调LLM的知识图谱（KGs）：** 使用你的KG为现成的LLM提供额外的训练。与其在查询时将KG数据作为提示的一部分（RAG），你可以使用KG来训练LLM本身。这样做的好处是，你可以将所有数据保留在本地——无需将提示发送给OpenAI或其他任何人。缺点是，LLM中的第一个L代表“大型”，因此下载和微调一个LLM是资源密集型的。此外，虽然基于你的企业或行业特定数据微调的模型会更加准确，但它并不能完全消除幻觉。对此的一些额外思考：

+   一旦使用图形数据来微调模型，你也会失去将图形数据用于访问控制的能力。

+   已经有一些LLM针对不同的行业进行了微调，例如面向医疗行业的[MedLM](https://cloud.google.com/blog/topics/healthcare-life-sciences/introducing-medlm-for-the-healthcare-industry)和面向网络安全的[SecLM](https://cloud.withgoogle.com/cloudsecurity/podcast/ep168-beyond-regular-llms-how-seclm-enhances-security-and-what-teams-can-do-with-it/)。

+   根据使用案例，微调LLM可能不是必须的。例如，如果你主要使用LLM来总结新闻文章，那么LLM可能不需要特别的训练。

+   与其通过行业特定的信息来微调LLM，有些人更倾向于使用已经微调过、专门生成代码的LLM（比如[Code Llama](https://ai.meta.com/blog/code-llama-large-language-model-coding/)）作为其提示-查询解决方案的一部分。

+   **实施或启用专注于使用KG来微调LLM的解决方案的知名企业：** 据我所知，[Stardog](https://www.stardog.com/blog/how-ai-uses-stardog/)的Voicebox是唯一一个使用KG来为客户微调LLM的解决方案。

**关于我在这里列出的集成KG和LLM的不同方式的说明：** 这些分类（RAG、提示查询和微调）既不全面，也非互斥。还有其他实施KG和LLM的方法，并且未来会有更多。此外，这些解决方案之间有相当大的重叠，你可以将不同的解决方案结合使用。例如，你可以在一个经过微调的模型上运行基于向量的提示查询RAG混合解决方案。

## **效率与可扩展性**

构建许多相互独立且无法连接的应用程序是低效的，这正是Dave McComb所称的[软件荒原](https://www.amazon.com/Software-Wasteland-Application-Centric-Hobbling-Enterprises/dp/1634623169)。即使这些应用程序是“由AI驱动”的也无关紧要。孤立的应用程序会导致数据和代码的重复，以及整体的冗余。知识图谱（KG）为消除这些冗余提供了基础，确保数据在整个企业中能够顺畅流动。

上述Gartner的观点是，许多GenAI项目将因为成本上升而被放弃，但我不确定KG是否能显著降低这些成本。我不知道有任何研究或成本效益分析来支持这一观点。为企业开发一个基于LLM的聊天机器人是昂贵的，开发KG也是如此。

# 结论

我不会假装知道“最优”解决方案，但正如我之前所说，我认为没有人知道。我确实相信，KG和LLM是任何希望更快将更多数据提供给正确人员的有用工具，它们各自有其优点和缺点。用LLM写求职信（或监管报告），但用KG确保你给了它正确的简历（或研究、期刊文章等）。

一般来说，我相信尽可能多地使用AI来构建、维护和扩展知识图谱，我也认为KG对那些希望采用GenAI技术的企业来说是必要的。这有几个原因：数据治理、访问控制和监管合规性；准确性和上下文理解；以及效率和可扩展性。
