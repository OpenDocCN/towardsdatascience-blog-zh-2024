# 将 Graph RAG 投入生产——逐步指南

> 原文：[https://towardsdatascience.com/graph-rag-into-production-step-by-step-3fe71fb4a98e?source=collection_archive---------0-----------------------#2024-09-23](https://towardsdatascience.com/graph-rag-into-production-step-by-step-3fe71fb4a98e?source=collection_archive---------0-----------------------#2024-09-23)

![](../Images/203dbf1037dad17615e4716e4782dc37.png)

图片由[JJ Ying](https://unsplash.com/@jjying?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 一个原生于 GCP、完全无服务器的实现，你只需几分钟即可复制

[](https://jakobpoerschmann.medium.com/?source=post_page---byline--3fe71fb4a98e--------------------------------)[![Jakob Pörschmann](../Images/b130445bf9ac471b70070eb4a2dc6b64.png)](https://jakobpoerschmann.medium.com/?source=post_page---byline--3fe71fb4a98e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3fe71fb4a98e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3fe71fb4a98e--------------------------------) [Jakob Pörschmann](https://jakobpoerschmann.medium.com/?source=post_page---byline--3fe71fb4a98e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3fe71fb4a98e--------------------------------) ·阅读时长 14 分钟·2024年9月23日

--

在[概述 Graph RAG 概念](/graph-rag-a-conceptual-introduction-41cd0d431375)后，让我们将其投入生产。这就是如何将 GraphRAG 投入生产：完全无服务器、完全并行化，以最小化推理和索引时间，而且完全不涉及图数据库（保证！）。

在本文中，我将向您介绍[graphrag-lite](https://github.com/jakobap/graphrag-light)，这是一个端到端的 Graph RAG 数据摄取和查询实现。我将 graphrag-lite 作为开源项目发布，旨在让您在 GCP 上部署 graphrag 时更加轻松。graphrag-lite 是 Google Cloud 原生的，可以直接使用。代码以模块化方式设计，可以根据您选择的平台进行调整。

# 回顾：

检索增强生成（Retrieval Augmented Generation）本身并未描述任何特定的架构或方法。它仅展示了如何通过任意的检索方法增强给定的生成任务。原始的 RAG 论文（[《面向知识密集型 NLP 任务的检索增强生成》](https://arxiv.org/abs/2005.11401)，作者：Lewis 等）比较了两塔嵌入方法与词袋检索方法。

现代问答系统区分局部问题和全局问题。一个局部（提取性）问题可能是“2023年谁获得了诺贝尔和平奖？”，针对一个非结构化的样本知识库。一个全局（聚合性）问题可能是“你知道的最近诺贝尔奖得主是谁？”。Text2embedding RAG在处理全局和结构化问题时存在明显的缺口。Graph RAG能够弥补这些缺口，并且做得很好！通过一个抽象层，它学习知识图谱社区的语义，从而构建对索引数据集的更“全球化”的理解。[这里是Graph RAG的概念介绍，供你阅读。](/graph-rag-a-conceptual-introduction-41cd0d431375)

# Graph RAG管道

一个Graph RAG管道通常遵循以下步骤：

## 图谱提取

这是主要的摄取步骤。你的LLM扫描每一个传入的文档，使用提示来提取与我们知识图谱相关的节点和边。你会多次执行此提示，确保捕捉到所有相关的信息。

## 图谱存储

你将提取的节点和边存储在你选择的数据存储中。专用图数据库（Graph DB）是一个选项，但它们通常比较繁琐。Graph2nosql是一个基于Python的接口，用于在Firestore或任何其他NoSQL数据库中存储和管理知识图谱。我开源了这个项目，因为我没有找到任何市场上可比的、原生支持知识图谱的选项。

## 社区检测

一旦你存储了知识图谱数据，你将使用社区检测算法来识别节点群体，这些节点在彼此之间的连接比与图谱其他部分的连接更加密集。在知识图谱的上下文中，假设是密集的社区覆盖了共同的主题。

## 社区报告生成

然后，你指示你的大型语言模型（LLM）为每个图谱社区生成报告。这些社区报告有助于在单一主题之间进行抽象，以便掌握数据集中更广泛的全球概念。社区报告与知识图谱一起存储。这标志着管道的摄取层部分完成。

## 用于最终上下文构建的映射-归约（Map-Reduce）模式

在查询时，你遵循映射-归约（map-reduce）模式，为知识图谱中的每个社区报告生成一个中间响应。你还让LLM评估每个中间查询响应的相关性。最后，你根据相关性对中间响应进行排名，并选择前n个作为最终响应的上下文，回复用户。

![](../Images/00aa36eaa8a8bf508304c06feae85d4e.png)

Graph RAG逐步逻辑 — 图像由作者提供

# 图谱提取

在初始摄取步骤中，你需要指示你的LLM将输入文档编码为图形。一个详细的提示会指示你的LLM首先识别给定类型的节点，其次识别你所识别节点之间的边。就像任何LLM提示一样，这个挑战没有唯一的解决方案。以下是我基于[微软的开源实现](https://github.com/microsoft/graphrag)的图形提取提示核心部分：

```py
-Goal-
Given a text document that is potentially relevant to this activity and a list of entity types, identify all entities of those types from the text and all relationships among the identified entities.

-Steps-
1\. Identify all entities. For each identified entity, extract the following information:
- entity_name: Name of the entity, capitalized
- entity_type: One of the following types: [{entity_types}]
- entity_description: Comprehensive description of the entity's attributes and activities
Format each entity as ("entity"{tuple_delimiter}<entity_name>{tuple_delimiter}<entity_type>{tuple_delimiter}<entity_description>

2\. From the entities identified in step 1, identify all pairs of (source_entity, target_entity) that are *clearly related* to each other.
For each pair of related entities, extract the following information:
- source_entity: name of the source entity, as identified in step 1
- target_entity: name of the target entity, as identified in step 1
- relationship_description: explanation as to why you think the source entity and the target entity are related to each other
- relationship_strength: a numeric score indicating strength of the relationship between the source entity and target entity
 Format each relationship as ("relationship"{tuple_delimiter}<source_entity>{tuple_delimiter}<target_entity>{tuple_delimiter}<relationship_description>{tuple_delimiter}<relationship_strength>)

3\. Return output in English as a single list of all the entities and relationships identified in steps 1 and 2\. Use **{record_delimiter}** as the list delimiter.

4\. When finished, output {completion_delimiter}

<Multishot Examples>

-Real Data-
######################
Entity_types: {entity_types}
Text: {input_text}
######################
Output:
```

提取步骤决定了哪些信息将反映在你的知识库中。因此，你应该使用一个相对强大的模型，比如 Gemini 1.5 Pro。你还可以进一步增加结果的鲁棒性，使用多轮版本的 Gemini 1.5 Pro，并查询模型多次改进其结果。以下是我在 graphrag-lite 中实现图形提取循环的方式：

```py
class GraphExtractor:
    def __init__(self, graph_db) -> None:
        self.tuple_delimiter = "<|>"
        self.record_delimiter = "##"
        self.completion_delimiter = "<|COMPLETE|>"
        self.entity_types = ["organization", "person", "geo", "event"]

        self.graph_extraction_system = prompts.GRAPH_EXTRACTION_SYSTEM.format(
            entity_types=", ".join(self.entity_types),
            record_delimiter=self.record_delimiter,
            tuple_delimiter=self.tuple_delimiter,
            completion_delimiter=self.completion_delimiter,
        )

        self.llm = LLMSession(system_message=self.graph_extraction_system,
                              model_name="gemini-1.5-pro-001")

    def __call__(self, text_input: str, max_extr_rounds: int = 5) -> None:

        input_prompt = self._construct_extractor_input(input_text=text_input)

        print("+++++ Init Graph Extraction +++++")

        init_extr_result = self.llm.generate_chat(
            client_query_string=input_prompt, temperature=0, top_p=0)
        print(f"Init result: {init_extr_result}")

        for round_i in range(max_extr_rounds):

            print(f"+++++ Contd. Graph Extraction round {round_i} +++++")

            round_response = self.llm.generate_chat(
                client_query_string=prompts.CONTINUE_PROMPT, temperature=0, top_p=0)
            init_extr_result += round_response or ""

            print(f"Round response: {round_response}")

            if round_i >= max_extr_rounds - 1:
                break

            completion_check = self.llm.generate_chat(
                client_query_string=prompts.LOOP_PROMPT, temperature=0, top_p=0)

            if "YES" not in completion_check:
                print(
                    f"+++++ Complete with completion check after round {round_i} +++++")
                break
```

首先，我调用多轮模型提取节点和边。然后，我要求模型多次改进先前的提取结果。

在 graphrag-lite 的实现中，提取模型的调用由前端客户端发出。如果你想减少客户端的负载，可以将提取查询外包给微服务。

# 图形存储

一旦你从文档中提取了节点和边，你需要以可访问的格式存储它们。图形数据库是一个选择，但它们也可能比较繁琐。对于你的知识图谱，你可能更倾向于寻找一些更轻量级的解决方案。我也有同样的想法，因为我没有找到任何开源的知识图谱本地库，于是我开源了 graph2nosql。[Graph2nosql 是一个简单的知识图谱本地 Python 接口](https://github.com/jakobap/graph2nosql)，它帮助你在任何 NoSQL 数据库中存储和管理知识图谱。所有这一切都无需通过图形数据库来扩展你的技术栈，或学习 Cypher。

Graph2nosql 是为知识图谱检索而设计的，考虑到了图形RAG的需求。该库围绕三种主要数据类型进行设计：EdgeData（边数据）、NodeData（节点数据）和CommunityData（社区数据）。节点通过唯一标识符（uid）识别。边通过源节点和目标节点的uid以及边的uid识别。由于uid可以自由设计，graph2nosql的数据模型为任何规模的知识图谱留出了空间。你甚至可以添加文本或图形嵌入。这使得基于嵌入的分析、边预测和额外的文本嵌入检索成为可能（考虑到混合RAG）。

[Graph2nosql](https://github.com/jakobap/graph2nosql) 本地设计时围绕 Firestore。

```py
@dataclass
class EdgeData:
    source_uid: str
    target_uid: str 
    description: str
    edge_uid: str | None = None
    document_id: str | None = None

@dataclass
class NodeData:
    node_uid: str
    node_title: str
    node_type: str
    node_description: str
    node_degree: int
    document_id: str 
    community_id: int | None = None # community id based on source document 
    edges_to: list[str] = field(default_factory=list)
    edges_from: list[str] = field(default_factory=list)  # in case of directed graph
    embedding: list[float] = field(default_factory=list)  # text embedding representing node e.g. combination of title & description

@dataclass
class CommunityData:
    title: str # title of comm, None if not yet computed
    community_nodes: set[str] = field(default_factory=set) # list of node_uid belonging to community
    summary: str | None = None # description of comm, None if not yet computed
    document_id: str | None = None # identifier for source knowlede base document for this entity
    community_uid: str | None = None # community identifier
    community_embedding: Tuple[float, ...] = field(default_factory=tuple) # text embedding representing community
    rating: int | None = None
    rating_explanation: str | None = None
    findings: list[dict] | None = None
```

要通过 graph2nosql 存储你的图形数据，只需在解析提取步骤的结果时运行以下代码。这是 graphrag-lite 的实现。

```py
from graph2nosql.graph2nosql.graph2nosql import NoSQLKnowledgeGraph
from graph2nosql.databases.firestore_kg import FirestoreKG
from graph2nosql.datamodel import data_model

fskg = FirestoreKG(
        gcp_project_id=project_id,
        gcp_credential_file=firestore_credential_file,
        firestore_db_id=database_id,
        node_collection_id=node_coll_id,
        edges_collection_id=edges_coll_id,
        community_collection_id=community_coll_id)

node_data = data_model.NodeData(
        node_uid=entity_name,
        node_title=entity_name,
        node_type=entity_type,
        node_description=entity_description,
        document_id=str(source_doc_id),
        node_degree=0)

fskg.add_node(node_uid=entity_name,node_data=node_data)
```

# 社区检测

将所有相关的节点和边存储在你的图形数据库中后，你可以开始构建抽象层。实现这一点的一种方式是查找描述相似概念的节点，并描述它们是如何在语义上连接的。例如，Graph2nosql 提供了内建的社区检测，基于Louvain社区。

根据您的提取结果质量，您可能会在知识图谱中发现零度节点。从经验来看，零度节点通常是重复的。graphrag-lite使用图社区作为主要的抽象层，因此您应该删除没有任何边的节点。因此，考虑进行另一个去重/合并步骤和/或基于描述和图嵌入的节点预测步骤，以添加在提取步骤中可能遗漏的边是有意义的。在graphrag-lite中，我目前简单地删除所有零度节点。

```py
# clean graph off all nodes without any edges
fskg.clean_zerodegree_nodes()

# generate communities based on cleaned graph
comms = kg.get_louvain_communities()
```

这是[graphrag-lite社区检测的实现](https://github.com/jakobap/graphrag-light/blob/39d7ed73fe23509951a8c93cc4806499110e1433/graphrag_lite/GraphExtractor.py#L508C1-L509C1)。

# 优化LLM应用中的吞吐量延迟

上述提到的GraphRAG管道在每个文档摄取和用户查询时都会进行多个LLM调用。例如，为每个新索引的文档生成多个社区报告，或者在查询时为多个社区生成中间响应。如果并发处理，结果将导致糟糕的用户体验。特别是在大规模应用时，用户可能需要等待几分钟到几小时才能收到查询的响应。幸运的是，如果您正确构建LLM提示，您可以将其设计为“无状态工作者”。无状态处理架构的优势是双重的。首先，它们易于并行化。其次，它们易于实现为无服务器基础设施。结合并行化和无服务器架构，可以最大化吞吐量可扩展性，并最小化空闲集群设置的成本。

在graphrag-lite架构中，我将社区报告生成和中间查询生成作为无服务器的Cloud Run微服务工作者进行托管。这些工作者通过GCP的无服务器消息队列PubSub接收消息。

![](../Images/c49303b5e6ca48937ad3fc3115f7eb01.png)

graphrag-lite的无服务器分布式摄取和查询管道 — 图片由作者提供

# 社区报告生成

运行社区检测后，您现在知道了多个社区成员节点集。每个集合代表您的知识图谱中的一个语义主题。社区报告步骤需要在这些源自不同文档的概念之间进行抽象处理。我再次基于微软的实现，并添加了一个函数调用，以便轻松解析结构化输出。

```py
You are an AI assistant that helps a human analyst to perform general information discovery. Information discovery is the process of identifying and assessing relevant information associated with certain entities (e.g., organizations and individuals) within a network.

# Goal
Write a comprehensive report of a community, given a list of entities that belong to the community as well as their relationships and optional associated claims. The report will be used to inform decision-makers about information associated with the community and their potential impact. The content of this report includes an overview of the community's key entities, their legal compliance, technical capabilities, reputation, and noteworthy claims.

# Report Structure

The report should include the following sections:

- TITLE: community's name that represents its key entities - title should be short but specific. When possible, include representative named entities in the title.
- SUMMARY: An executive summary of the community's overall structure, how its entities are related to each other, and significant information associated with its entities.
- IMPACT SEVERITY RATING: a float score between 0-10 that represents the severity of IMPACT posed by entities within the community.  IMPACT is the scored importance of a community.
- RATING EXPLANATION: Give a single sentence explanation of the IMPACT severity rating.
- DETAILED FINDINGS: A list of 5-10 key insights about the community. Each insight should have a short summary followed by multiple paragraphs of explanatory text grounded according to the grounding rules below. Be comprehensive.
```

社区报告生成还展示了知识图谱检索中的最大挑战。从理论上讲，任何文档都可能向图中的每个现有社区添加一个新节点。在最坏的情况下，您需要在每次添加新文档时重新生成知识库中的每个社区报告。实际上，关键是要包含一个检测步骤，识别在文档上传后发生变化的社区，从而仅为已调整的社区生成新报告。

由于您需要为每次文档上传重新生成多个社区报告，如果同时运行这些请求，我们也面临显著的延迟挑战。因此，您应该将这项工作外包并将其并行化到异步工作者中。如前所述，graphrag-lite通过使用无服务器架构解决了这一问题。我使用PubSub作为消息队列来管理工作项并确保处理。Cloud Run作为计算平台，托管调用LLM的无状态工作者。在生成过程中，它们使用如上所示的提示。

[这是运行在无状态工作者中的社区报告生成代码](https://github.com/jakobap/graphrag-light/blob/main/stateless-comm-reporter/main.py)：

```py
def async_generate_comm_report(self, comm_members: set[str]) -> data_model.CommunityData:

        llm = LLMSession(system_message=prompts.COMMUNITY_REPORT_SYSTEM,
                         model_name="gemini-1.5-flash-001")

        response_schema = {
            "type": "object",
            "properties": {
                    "title": {
                        "type": "string"
                    },
                "summary": {
                        "type": "string"
                        },
                "rating": {
                        "type": "int"
                        },
                "rating_explanation": {
                        "type": "string"
                        },
                "findings": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "summary": {
                                    "type": "string"
                                },
                                "explanation": {
                                    "type": "string"
                                }
                            },
                            # Ensure both fields are present in each finding
                            "required": ["summary", "explanation"]
                        }
                        }
            },
            # List required fields at the top level
            "required": ["title", "summary", "rating", "rating_explanation", "findings"]
        }

        comm_report  = llm.generate(client_query_string=prompts.COMMUNITY_REPORT_QUERY.format(
            entities=comm_nodes,
            relationships=comm_edges,
            response_mime_type="application/json",
            response_schema=response_schema
        ))

comm_data = data_model.CommunityData(title=comm_report_dict["title"],                                              summary=comm_report_dict["summary"],                                                rating=comm_report_dict["rating"],             rating_explanation=comm_report_dict["rating_explanation"],               findings=comm_report_dict["findings"],
community_nodes=comm_members)

 return comm_data
```

这完成了数据摄取流程。

# 中间响应的映射步骤

最后，您已进入查询时间。为了生成最终的用户响应，您需要生成一组中间响应（每个社区报告一个）。每个中间响应都将用户查询和一个社区报告作为输入。然后，您按相关性对这些中间查询进行评分。最终，您使用最相关的社区报告以及额外的信息（如相关成员节点的节点描述）作为最终的查询上下文。鉴于大规模社区报告的数量较高，这再次带来了延迟和成本的挑战。和之前一样，您还应将中间响应生成（映射步骤）并行化到无服务器微服务中。在未来，您可以通过增加一个过滤层来显著提高效率，以预先确定社区报告对用户查询的相关性。

[映射步骤微服务如下所示](https://github.com/jakobap/graphrag-light/blob/main/stateless-context-processor/main.py)：

```py
def generate_response(client_query: str, community_report: dict):

    llm = LLMSession(
        system_message=MAP_SYSTEM_PROMPT,
        model_name="gemini-1.5-pro-001"
    )

    response_schema = {
        "type": "object",
        "properties": {
            "response": {
                "type": "string",
                "description": "The response to the user question as raw string.",
            },
            "score": {
                "type": "number",
                "description": "The relevance score of the given community report context towards answering the user question [0.0, 10.0]",
            },
        },
        "required": ["response", "score"],
    }

    query_prompt = MAP_QUERY_PROMPT.format(
        context_community_report=community_report, user_question=client_query)

    response = llm.generate(client_query_string=query_prompt,
                 response_schema=response_schema,
                 response_mime_type="application/json")

    return response
```

映射步骤微服务使用以下提示：

```py
---Role---
You are an expert agent answering questions based on context that is organized as a knowledge graph.
You will be provided with exactly one community report extracted from that same knowledge graph.

---Goal---
Generate a response consisting of a list of key points that responds to the user's question, summarizing all relevant information in the given community report.

You should use the data provided in the community description below as the only context for generating the response.
If you don't know the answer or if the input community description does not contain sufficient information to provide an answer respond "The user question cannot be answered based on the given community context.".

Your response should always contain following elements:
- Query based response: A comprehensive and truthful response to the given user query, solely based on the provided context.
- Importance Score: An integer score between 0-10 that indicates how important the point is in answering the user's question. An 'I don't know' type of response should have a score of 0.

The response should be JSON formatted as follows:
{{"response": "Description of point 1 [Data: Reports (report ids)]", "score": score_value}}

---Context Community Report---
{context_community_report}

---User Question---
{user_question}

---JSON Response---
The json response formatted as follows:
{{"response": "Description of point 1 [Data: Reports (report ids)]", "score": score_value}}

response: 
```

# 最终用户响应的归约步骤

为了成功完成归约步骤，您需要存储中间响应以便在查询时访问。在graphrag-lite中，我使用Firestore作为微服务之间的共享状态。在触发中间响应生成后，客户端还会定期检查共享状态中是否存在所有预期的条目。以下是来自graphrag-lite的代码片段，展示了我如何将每个社区报告提交到PubSub队列中。之后，我定期查询共享状态，以检查是否所有中间响应都已处理。最后，针对用户的最终响应将使用得分最高的社区报告作为上下文来回应用户查询。

```py
class KGraphGlobalQuery:
    def __init__(self) -> None:
        # initialized with info on mq, knowledge graph, shared nosql state
        pass

    @observe()
    def __call__(self, user_query: str) -> str:

        # orchestration method taking natural language user query to produce and return final answer to client
        comm_report_list = self._get_comm_reports()

        # pair user query with existing community reports
        query_msg_list = self._context_builder(
            user_query=user_query, comm_report_list=comm_report_list)

        # send pairs to pubsub queue for work scheduling
        for msg in query_msg_list:
            self._send_to_mq(message=msg)
        print("int response request sent to mq")

        # periodically query shared state to check for processing compeltion & get intermediate responses
        intermediate_response_list = self._check_shared_state(
            user_query=user_query)

        # based on helpfulness build final context
        sorted_final_responses = self._filter_and_sort_responses(intermediate_response_list=intermediate_response_list)

        # get full community reports for the selected communities
        comm_report_list = self._get_communities_reports(sorted_final_responses)

        # generate & return final response based on final context community repors and nodes.
        final_response_system = prompts.GLOBAL_SEARCH_REDUCE_SYSTEM.format(
            response_type="Detailled and wholistic in academic style analysis of the given information in at least 8-10 sentences across 2-3 paragraphs.")

        llm = LLMSession(
            system_message=final_response_system,
            model_name="gemini-1.5-pro-001"
        )

        final_query_string = prompts.GLOBAL_SEARCH_REDUCE_QUERY.format(
            report_data=comm_report_list,
            user_query=user_query
        )
        final_response = llm.generate(client_query_string=final_query_string)
        return final_response
```

一旦找到所有条目，客户端将触发基于选定社区上下文的最终用户响应生成。

# 最终思考

图谱 RAG 是一种强大的技术，每个 ML 工程师都应该将其添加到自己的工具箱中。每个问答类型的应用最终都会遇到这样一个问题：纯粹的提取式、“局部”查询再也无法满足需求了。通过 graphrag-lite，你现在拥有了一种轻量级、云原生、无服务器的实现，可以快速复制。

尽管具有这些优势，请注意，在当前状态下，Graph RAG 消耗的 LLM 输入 token 数量明显高于 text2emb RAG。这通常会导致查询和文档索引的延迟和成本显著增加。然而，在体验了结果质量的提升后，我相信在适当的应用场景中，Graph RAG 是值得花时间和金钱的。

RAG 应用最终会朝着混合方向发展。提取式查询可以通过 text2emb RAG 高效且准确地处理。全局抽象查询可能需要知识图谱作为替代的检索层。最后，两种方法在处理定量和分析性查询时表现不佳。因此，第三种 text2sql 检索层将大大增加价值。为了完善这一框架，用户查询最初可以在三种检索方法之间进行分类。这样，每个查询都能以最有效的方式获取适量和深度的信息。

我迫不及待想看看这个技术还会走向何方。**你目前在使用哪些替代的检索方法？**
