# GraphRAG 实践：从商业合同到动态问答代理

> 原文：[https://towardsdatascience.com/graphrag-in-action-from-commercial-contracts-to-a-dynamic-q-a-agent-7d4a6caa6eb5?source=collection_archive---------0-----------------------#2024-11-04](https://towardsdatascience.com/graphrag-in-action-from-commercial-contracts-to-a-dynamic-q-a-agent-7d4a6caa6eb5?source=collection_archive---------0-----------------------#2024-11-04)

## 一种基于问题的提取方法

[](https://medium.com/@edward.sandoval.2000?source=post_page---byline--7d4a6caa6eb5--------------------------------)[![Ed Sandoval](../Images/2bdc5126db03add63b2ee251db2c3e0b.png)](https://medium.com/@edward.sandoval.2000?source=post_page---byline--7d4a6caa6eb5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7d4a6caa6eb5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7d4a6caa6eb5--------------------------------) [Ed Sandoval](https://medium.com/@edward.sandoval.2000?source=post_page---byline--7d4a6caa6eb5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7d4a6caa6eb5--------------------------------) ·23分钟阅读·2024年11月4日

--

在这篇博客文章中，我们介绍了一种方法，利用图形检索增强生成（GraphRAG）方法——简化商业合同数据的摄取过程，并构建一个问答代理。

这种方法与传统的RAG（检索增强生成）方法不同，它强调数据提取的效率，而不是像传统RAG方法那样随意拆解和向量化整个文档。

在传统的RAG中，每个文档都会被拆分成多个块并向量化以供检索，这可能导致大量不必要的数据被拆分、分块并存储在向量索引中。然而，在这里，重点是从每份合同中提取最相关的信息，以满足特定的应用场景——商业合同审核。然后，这些数据会被结构化成一个知识图谱，图谱组织了关键实体和关系，从而通过Cypher查询和向量检索实现更精确的图数据检索。

通过最小化向量化内容的数量，并专注于提取高度相关的知识，这种方法提高了问答代理的准确性和性能，使其能够处理复杂和特定领域的问题。

该四阶段方法包括：有针对性的信息提取（LLM + Prompt），创建知识图谱（LLM + Neo4J）以及简单的一组图数据检索功能（Cypher、Text to Cypher、Vector Search）。最后，构建一个利用数据检索功能的问答代理，基于([Microsoft Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/overview/))。

下图展示了该方法的实现方式

![](../Images/c97973ee81561dc36141cd7bcad3a15d.png)

四阶段GraphRAG方法：从基于问题的提取 -> 知识图谱模型 -> GraphRAG检索 -> 问答代理。图像由Sebastian Nilsson @ Neo4J提供，并获得作者许可在此重制。

但首先，对于那些不熟悉商业法的人来说，让我们先简要介绍一下合同审查问题。

# 合同审查与大语言模型

商业合同审查是一个劳动密集型过程，涉及律师助理和初级律师仔细识别合同中的关键信息。

> “合同审查是全面阅读合同的过程，目的是理解签署合同的个人或公司所承担的权利和义务，并评估相关影响。”
> 
> Hendrycks, Burns 等人，NeurIPS 2021, 见[CUAD：一个专家标注的法律合同审查NLP数据集](https://arxiv.org/pdf/2103.06268)

合同审查的第一阶段包括审查数百页合同，寻找相关条款或义务。合同审查员必须确定是否存在相关条款，若存在，这些条款的内容是什么，并且跟踪它们的位置。

> 例如，他们必须确定合同是三年期合同还是一年期合同。他们必须确定合同的结束日期。他们必须确定某个条款是否是反转让条款或排他性条款……
> 
> Hendrycks, Burns 等人，NeurIPS 2021, 见[CUAD：一个专家标注的法律合同审查NLP数据集](https://arxiv.org/pdf/2103.06268)

这是一个需要仔细审查的任务，但通常效率低下，但它非常适合大语言模型！

一旦完成第一阶段，高级法律从业者可以开始审查合同中的弱点和风险。这是一个领域，在这个领域中，由LLM支持并通过存储在知识图谱中的信息为基础的问答代理是法律专家的完美副驾驶。

# 使用LLMs、功能调用和GraphRAG构建商业合同审查代理的四步方法

本文的其余部分将描述这个过程中的每个步骤。在此过程中，我将使用代码片段来说明主要概念。

四个步骤是：

1.  **从合同中提取相关信息（LLM + 合同）**

1.  **将提取的信息存储到知识图谱中（Neo4j）**

1.  **开发简单的KG数据检索功能（Python）**

1.  **构建处理复杂问题的问答代理（语义内核，LLM，Neo4j）**

# 数据集：

[CUAD（合同理解阿提克斯数据集）](https://www.atticusprojectai.org/cuad)是一个采用CC BY 4.0许可的公开数据集，包含超过13,000个专家标注的条款，跨越510份法律合同，旨在帮助构建用于合同审查的AI模型。它涵盖了广泛的重要法律条款，例如保密条款、终止条款和赔偿条款，这些对于合同分析至关重要。

我们将使用这个数据集中的三个合同，展示我们如何有效地提取和分析关键的法律信息，构建知识图谱，并利用它进行精确的复杂问题回答。

三个合同合计包含 95 页。

# 第1步：从合同中提取相关信息

向大型语言模型（LLM）请求提取合同中的精确信息并生成 JSON 输出，表示合同中的相关信息，是相对直接的。

在商业审查中，可以编写一个提示，来定位上述提到的每个关键元素——各方、日期、条款——并将其整洁地汇总成机器可读的（JSON）文件。

**提取提示（简化版）**

> 请仅使用本合同中的信息回答以下问题
> 
> [Contract.pdf]
> 
> 1) 这是什么类型的合同？
> 
> 2) 各方是谁及其角色是什么？他们在哪个国家注册？请提供州和国家名称（使用 ISO 3166 国家名称）
> 
> 3) 协议日期是什么？
> 
> 4) 生效日期是什么？
> 
> 对于以下每种类型的合同条款，提取两条信息：
> 
> a) 一个是/否选项，表示你是否认为该条款出现在此合同中
> 
> b) 一份摘录列表，指示该条款类型的存在。
> 
> 合同条款类型：竞争限制例外、不竞争条款、排他性、禁止诱导客户、禁止诱导员工、不得贬损、便捷解除、Rofr/Rofo/Rofn、控制权变更、反转让、无限责任、责任上限
> 
> 请将最终答案以 JSON 文档的形式提供。

请注意，上述部分展示了提取提示的简化版本。完整版本可以在[此处查看](https://github.com/neo4j-product-examples/graphrag-contract-review/blob/main/prompts/contract_extraction_prompt.txt)。你会发现，提示的最后部分指定了 JSON 文档的所需格式。这有助于确保输出的一致 JSON 模式。

这个任务在 Python 中相对简单。下面的 `main()` 函数旨在通过提取相关法律信息（extraction_prompt），使用**OpenAI gpt-4o**处理一组 PDF 合同文件，并将结果保存为 JSON 格式。

```py
def main():
    pdf_files = [filename for filename in os.listdir('./data/input/') if filename.endswith('.pdf')]

    for pdf_filename in pdf_files:
        print('Processing ' + pdf_filename + '...')    
        # Extract content from PDF using the assistant
        complete_response = process_pdf('./data/input/' + pdf_filename)
        # Log the complete response to debug
        save_json_string_to_file(complete_response, './data/debug/complete_response_' + pdf_filename + '.json') 
```

“***process_pdf***” 函数使用“**OpenAI gpt-4o**”从合同中执行知识提取，使用“提取提示”。

```py
def process_pdf(pdf_filename):
    # Create OpenAI message thread
    thread = client.beta.threads.create()
    # Upload PDF file to the thread
    file = client.files.create(file=open(pdf_filename, "rb"), purpose="assistants")
    # Create message with contract as attachment and extraction_prompt
    client.beta.threads.messages.create(thread_id=thread.id,role="user",
        attachments=[
            Attachment(
                file_id=file.id, tools=[AttachmentToolFileSearch(type="file_search")])
        ],
        content=extraction_prompt,
    )
    # Run the message thread
    run = client.beta.threads.runs.create_and_poll(
        thread_id=thread.id, assistant_id=pdf_assistant.id, timeout=1000)
    # Retrieve messages
    messages_cursor = client.beta.threads.messages.list(thread_id=thread.id)
    messages = [message for message in messages_cursor]
    # Return last message in Thread 
    return messages[0].content[0].text.value
```

对于每个合同，**“process_pdf”** 返回的消息如下

```py
{
    "agreement": {
        "agreement_name": "Marketing Affiliate Agreement",
        "agreement_type": "Marketing Affiliate Agreement",
        "effective_date": "May 8, 2014",
        "expiration_date": "December 31, 2014",
        "renewal_term": "1 year",
        "Notice_period_to_Terminate_Renewal": "30 days",
        "parties": [
            {
                "role": "Company",
                "name": "Birch First Global Investments Inc.",
                "incorporation_country": "United States Virgin Islands",
                "incorporation_state": "N/A"
            },
            {
                "role": "Marketing Affiliate",
                "name": "Mount Knowledge Holdings Inc.",
                "incorporation_country": "United States",
                "incorporation_state": "Nevada"
            }
        ],
        "governing_law": {
            "country": "United States",
            "state": "Nevada",
            "most_favored_country": "United States"
        },
        "clauses": [
            {
                "clause_type": "Competitive Restriction Exception",
                "exists": false,
                "excerpts": []
            },
            {
                "clause_type": "Exclusivity",
                "exists": true,
                "excerpts": [
                    "Company hereby grants to MA the right to advertise, market and sell to corporate users, government agencies and educational facilities for their own internal purposes only, not for remarketing or redistribution."
                ]
            },
            {
                "clause_type": "Non-Disparagement",
                "exists": true,
                "excerpts": [
                    "MA agrees to conduct business in a manner that reflects favorably at all times on the Technology sold and the good name, goodwill and reputation of Company."
                ]
            },
            {
                "clause_type": "Termination For Convenience",
                "exists": true,
                "excerpts": [
                    "This Agreement may be terminated by either party at the expiration of its term or any renewal term upon thirty (30) days written notice to the other party."
                ]
            },
            {
                "clause_type": "Anti-Assignment",
                "exists": true,
                "excerpts": [
                    "MA may not assign, sell, lease or otherwise transfer in whole or in part any of the rights granted pursuant to this Agreement without prior written approval of Company."
                ]
            },

            {
                "clause_type": "Price Restrictions",
                "exists": true,
                "excerpts": [
                    "Company reserves the right to change its prices and/or fees, from time to time, in its sole and absolute discretion."
                ]
            },
            {
                "clause_type": "Minimum Commitment",
                "exists": true,
                "excerpts": [
                    "MA commits to purchase a minimum of 100 Units in aggregate within the Territory within the first six months of term of this Agreement."
                ]
            },

            {
                "clause_type": "IP Ownership Assignment",
                "exists": true,
                "excerpts": [
                    "Title to the Technology and all copyrights in Technology shall remain with Company and/or its Affiliates."
                ]
            },

            {
                "clause_type": "License grant",
                "exists": true,
                "excerpts": [
                    "Company hereby grants to MA the right to advertise, market and sell the Technology listed in Schedule A of this Agreement."
                ]
            },
            {
                "clause_type": "Non-Transferable License",
                "exists": true,
                "excerpts": [
                    "MA acknowledges that MA and its Clients receive no title to the Technology contained on the Technology."
                ]
            },
            {
                "clause_type": "Cap On Liability",
                "exists": true,
                "excerpts": [
                    "In no event shall Company be liable to MA, its Clients, or any third party for any tort or contract damages or indirect, special, general, incidental or consequential damages."
                ]
            },

            {
                "clause_type": "Warranty Duration",
                "exists": true,
                "excerpts": [
                    "Company's sole and exclusive liability for the warranty provided shall be to correct the Technology to operate in substantial accordance with its then current specifications."
                ]
            }

        ]
    }
}
```

# 第2步：创建知识图谱

现在，每个合同都是一个 JSON 文件，下一步是在 Neo4J 中创建知识图谱。

在这一点上，花些时间设计数据模型是有用的。你需要考虑一些关键问题：

+   图中的节点和关系代表什么？

+   每个节点和关系的主要属性是什么？

+   是否需要索引任何属性？

+   哪些属性需要向量嵌入以启用语义相似度搜索？

在我们的案例中，一个合适的设计（模式）包括主要实体：协议（合同）、它们的条款、作为协议当事方的组织及其之间的关系。

下方显示的是模式的可视化表示。

![](../Images/28f50df8abdbf7e7816523de25b5bfaf.png)

作者提供的图片

```py
 Node properties:
Agreement {agreement_type: STRING, contract_id: INTEGER,
          effective_date: STRING, expiration_date: STRING,
          renewal_term: STRING, name: STRING}
ContractClause {name: STRING, type: STRING}
ClauseType {name: STRING}
Country {name: STRING}
Excerpt {text: STRING}
Organization {name: STRING}

Relationship properties:
IS_PARTY_TO {role: STRING}
GOVERNED_BY_LAW {state: STRING}
HAS_CLAUSE {type: STRING}
INCORPORATED_IN {state: STRING} 
```

只有“摘录”——在步骤1中由LLM识别的短文本片段——需要文本嵌入。这种方法显著减少了表示每个合同所需的向量数量和向量索引的大小，从而提高了处理效率和可扩展性。

一个简化版的Python脚本，通过上述模式将每个JSON加载到知识图谱中的示例如下：

```py
NEO4J_URI=os.getenv('NEO4J_URI', 'bolt://localhost:7687')
NEO4J_USER=os.getenv('NEO4J_USERNAME', 'neo4j')
NEO4J_PASSWORD=os.getenv('NEO4J_PASSWORD')
OPENAI_API_KEY = os.getenv('OPENAI_API_KEY')
JSON_CONTRACT_FOLDER = './data/output/'

driver = GraphDatabase.driver(NEO4J_URI, auth=(NEO4J_USER, NEO4J_PASSWORD))

contract_id = 1

json_contracts = [filename for filename in os.listdir(JSON_CONTRACT_FOLDER) if filename.endswith('.json')]
for json_contract in json_contracts:
  with open(JSON_CONTRACT_FOLDER + json_contract,'r') as file:
    json_string = file.read()
    json_data = json.loads(json_string)
    agreement = json_data['agreement']
    agreement['contract_id'] = contract_id
    driver.execute_query(CREATE_GRAPH_STATEMENT,  data=json_data)
    contract_id+=1

create_full_text_indices(driver)
driver.execute_query(CREATE_VECTOR_INDEX_STATEMENT)
print ("Generating Embeddings for Contract Excerpts...")
driver.execute_query(EMBEDDINGS_STATEMENT, token = OPENAI_API_KEY)
```

这里的“**CREATE_GRAPH_STATEMENT**”是唯一的“复杂”部分。它是一个CYPHER语句，将合同（JSON）映射到知识图谱中的节点和关系。

完整的Cypher语句如下：

```py
CREATE_GRAPH_STATEMENT = """
WITH $data AS data
WITH data.agreement as a

MERGE (agreement:Agreement {contract_id: a.contract_id})
ON CREATE SET 
  agreement.contract_id  = a.contract_id,
  agreement.name = a.agreement_name,
  agreement.effective_date = a.effective_date,
  agreement.expiration_date = a.expiration_date,
  agreement.agreement_type = a.agreement_type,
  agreement.renewal_term = a.renewal_term,
  agreement.most_favored_country = a.governing_law.most_favored_country
  //agreement.Notice_period_to_Terminate_Renewal = a.Notice_period_to_Terminate_Renewal

MERGE (gl_country:Country {name: a.governing_law.country})
MERGE (agreement)-[gbl:GOVERNED_BY_LAW]->(gl_country)
SET gbl.state = a.governing_law.state

FOREACH (party IN a.parties |
  // todo proper global id for the party
  MERGE (p:Organization {name: party.name})
  MERGE (p)-[ipt:IS_PARTY_TO]->(agreement)
  SET ipt.role = party.role
  MERGE (country_of_incorporation:Country {name: party.incorporation_country})
  MERGE (p)-[incorporated:INCORPORATED_IN]->(country_of_incorporation)
  SET incorporated.state = party.incorporation_state
)

WITH a, agreement, [clause IN a.clauses WHERE clause.exists = true] AS valid_clauses
FOREACH (clause IN valid_clauses |
  CREATE (cl:ContractClause {type: clause.clause_type})
  MERGE (agreement)-[clt:HAS_CLAUSE]->(cl)
  SET clt.type = clause.clause_type
  // ON CREATE SET c.excerpts = clause.excerpts
  FOREACH (excerpt IN clause.excerpts |
    MERGE (cl)-[:HAS_EXCERPT]->(e:Excerpt {text: excerpt})
  )
  //link clauses to a Clause Type label
  MERGE (clType:ClauseType{name: clause.clause_type})
  MERGE (cl)-[:HAS_TYPE]->(clType)
)"""
```

以下是该语句执行的操作概述：

## 数据绑定

```py
WITH $data AS data
WITH data.agreement as a
```

+   `$data`是以JSON格式传入查询的输入数据，包含有关协议（合同）的信息。

+   第二行将`data.agreement`赋值给别名`a`，以便在后续查询中引用合同详情。

## **插入协议节点**

```py
MERGE (agreement:Agreement {contract_id: a.contract_id})
ON CREATE SET 
  agreement.name = a.agreement_name,
  agreement.effective_date = a.effective_date,
  agreement.expiration_date = a.expiration_date,
  agreement.agreement_type = a.agreement_type,
  agreement.renewal_term = a.renewal_term,
  agreement.most_favored_country = a.governing_law.most_favored_country
```

+   `MERGE`尝试查找具有指定`contract_id`的现有`Agreement`节点。如果没有这样的节点，它会创建一个。

+   `ON CREATE SET`子句设置新创建的`Agreement`节点的各种属性，如`contract_id`、`agreement_name`、`effective_date`和来自JSON输入的其他协议相关字段。

## **创建适用法律关系**

```py
MERGE (gl_country:Country {name: a.governing_law.country})
MERGE (agreement)-[gbl:GOVERNED_BY_LAW]->(gl_country)
SET gbl.state = a.governing_law.state
```

+   这会为与协议相关的适用法律国家创建或合并一个`Country`节点。

+   然后，创建或合并`Agreement`和`Country`之间的`GOVERNED_BY_LAW`关系。

+   它还设置了`GOVERNED_BY_LAW`关系的`state`属性。

## **创建当事方和注册地关系**

```py
FOREACH (party IN a.parties |
  MERGE (p:Organization {name: party.name})
  MERGE (p)-[ipt:IS_PARTY_TO]->(agreement)
  SET ipt.role = party.role
  MERGE (country_of_incorporation:Country {name: party.incorporation_country})
  MERGE (p)-[incorporated:INCORPORATED_IN]->(country_of_incorporation)
  SET incorporated.state = party.incorporation_state
)
```

对于合同中的每个当事方（`a.parties`），它：

+   为当事方插入（合并）一个`Organization`节点。

+   创建一个`IS_PARTY_TO`关系，表示`Organization`和`Agreement`之间的关系，并设置当事方的`role`（例如，买方、卖方）。

+   合并一个`Country`节点，表示组织注册的国家。

+   创建一个`INCORPORATED_IN`关系，表示组织与注册国家之间的关系，并设置组织的注册地`state`。

## **创建合同条款和摘录**

```py
WITH a, agreement, [clause IN a.clauses WHERE clause.exists = true] AS valid_clauses
FOREACH (clause IN valid_clauses |
  CREATE (cl:ContractClause {type: clause.clause_type})
  MERGE (agreement)-[clt:HAS_CLAUSE]->(cl)
  SET clt.type = clause.clause_type
  FOREACH (excerpt IN clause.excerpts |
    MERGE (cl)-[:HAS_EXCERPT]->(e:Excerpt {text: excerpt})
  )
  MERGE (clType:ClauseType{name: clause.clause_type})
  MERGE (cl)-[:HAS_TYPE]->(clType)
)
```

+   这一部分首先筛选条款列表（`a.clauses`），仅包括`clause.exists = true`的条款（即在步骤1中由LLM识别的包含摘录的条款）。

+   对于每个条款：

+   它为每个条款创建一个`ContractClause`节点，其中`name`和`type`对应于条款类型。

+   在`Agreement`和`ContractClause`之间建立了`HAS_CLAUSE`关系。

+   对于与条款相关的每个`excerpt`，它创建一个`Excerpt`节点，并通过`HAS_EXCERPT`关系将其链接到`ContractClause`。

+   最后，为条款的类型创建（或合并）一个`ClauseType`节点，并通过`HAS_TYPE`关系将`ContractClause`与`ClauseType`链接。

一旦导入脚本运行完毕，单个合同就可以在Neo4J中以知识图谱的形式可视化

![](../Images/f3dd8827f94f0b3130da91d1104f56b0.png)

单一合同的知识图谱表示：组织（各方）用绿色表示，合同条款用蓝色表示，摘录用浅棕色表示，国家用橙色表示。图片来源：作者

知识图谱中的三个合同仅需要一个小型图（不到100个节点，少于200个关系）。最重要的是，仅需要40-50个摘录的向量嵌入。这个拥有少量向量的知识图谱现在可以用来支持一个相当强大的问答代理。

# 第3步：为GraphRAG开发数据检索功能

现在，合同已被结构化为知识图谱，下一步是创建一组小型图数据检索功能。这些功能作为核心构建块，使我们能够在第4步开发问答代理。

我们将定义几个基本的数据检索功能：

1.  检索合同的基本信息（给定合同ID）

1.  查找涉及特定组织的合同（给定部分组织名称）

1.  查找**不包含**特定条款类型的合同

1.  查找包含特定类型条款的合同

1.  基于与条款中的文本（摘录）语义相似度查找合同（例如，查找提到“禁止物品”的合同）

1.  对数据库中的所有合同执行自然语言查询。例如，执行聚合查询，统计“有多少合同符合特定条件”。

在第4步，我们将使用[Microsoft Semantic Kernel库](https://learn.microsoft.com/en-us/semantic-kernel/overview/)构建问答系统。该库简化了代理构建过程，允许开发人员定义代理可用的功能和工具，以便回答问题。

为了简化Neo4J与Semantic Kernel库之间的集成，我们将定义一个`ContractPlugin`，该插件定义了每个数据检索功能的“签名”。请注意每个函数的`@kernel_function`装饰器，以及为每个函数提供的类型信息和描述。

Semantic Kernel使用“**插件**”类的概念来封装代理可用的一组功能。它将使用装饰器函数、类型信息和文档来通知LLM函数调用能力，以了解可用的功能。

```py
from typing import List, Optional, Annotated
from AgreementSchema import Agreement, ClauseType
from semantic_kernel.functions import kernel_function
from ContractService import  ContractSearchService

class ContractPlugin:
    def __init__(self, contract_search_service: ContractSearchService ):
        self.contract_search_service = contract_search_service

    @kernel_function
    async def get_contract(self, contract_id: int) -> Annotated[Agreement, "A contract"]:
        """Gets details about a contract with the given id."""
        return await self.contract_search_service.get_contract(contract_id)

    @kernel_function
    async def get_contracts(self, organization_name: str) -> Annotated[List[Agreement], "A list of contracts"]:
        """Gets basic details about all contracts where one of the parties has a name similar to the given organization name."""
        return await self.contract_search_service.get_contracts(organization_name)

    @kernel_function
    async def get_contracts_without_clause(self, clause_type: ClauseType) -> Annotated[List[Agreement], "A list of contracts"]:
        """Gets basic details from contracts without a clause of the given type."""
        return await self.contract_search_service.get_contracts_without_clause(clause_type=clause_type)

    @kernel_function
    async def get_contracts_with_clause_type(self, clause_type: ClauseType) -> Annotated[List[Agreement], "A list of contracts"]:
        """Gets basic details from contracts with a clause of the given type."""
        return await self.contract_search_service.get_contracts_with_clause_type(clause_type=clause_type)

    @kernel_function
    async def get_contracts_similar_text(self, clause_text: str) -> Annotated[List[Agreement], "A list of contracts with similar text in one of their clauses"]:
        """Gets basic details from contracts having semantically similar text in one of their clauses to the to the 'clause_text' provided."""
        return await self.contract_search_service.get_contracts_similar_text(clause_text=clause_text)

    @kernel_function
    async def answer_aggregation_question(self, user_question: str) -> Annotated[str, "An answer to user_question"]:
        """Answer obtained by turning user_question into a CYPHER query"""
        return await self.contract_search_service.answer_aggregation_question(user_question=user_question)
```

我建议探索包含上述每个函数实现的 [“ContractService”](https://github.com/neo4j-product-examples/graphrag-contract-review/blob/main/ContractService.py) 类。每个函数展示了一种不同的数据检索技术。

让我们逐步了解这些函数的实现，因为它们展示了不同的 GraphRAG 数据检索技术/模式

## 根据合同 ID 获取合同 — 基于 Cypher 的检索函数

`get_contract(self, contract_id: int)` 是一个异步方法，旨在使用 Cypher 查询从 Neo4J 数据库中检索特定合同（`Agreement`）的详细信息。该函数返回一个填充了关于协议、条款、相关方及其关系信息的 `Agreement` 对象。

以下是此函数的实现

```py
async def get_contract(self, contract_id: int) -> Agreement:

        GET_CONTRACT_BY_ID_QUERY = """
            MATCH (a:Agreement {contract_id: $contract_id})-[:HAS_CLAUSE]->(clause:ContractClause)
            WITH a, collect(clause) as clauses
            MATCH (country:Country)-[i:INCORPORATED_IN]-(p:Organization)-[r:IS_PARTY_TO]-(a)
            WITH a, clauses, collect(p) as parties, collect(country) as countries, collect(r) as roles, collect(i) as states
            RETURN a as agreement, clauses, parties, countries, roles, states
        """

        agreement_node = {}

        records, _, _  = self._driver.execute_query(GET_CONTRACT_BY_ID_QUERY,{'contract_id':contract_id})

        if (len(records)==1):
            agreement_node =    records[0].get('agreement')
            party_list =        records[0].get('parties')
            role_list =         records[0].get('roles')
            country_list =      records[0].get('countries')
            state_list =        records[0].get('states')
            clause_list =       records[0].get('clauses')

        return await self._get_agreement(
            agreement_node, format="long",
            party_list=party_list, role_list=role_list,
            country_list=country_list,state_list=state_list,
            clause_list=clause_list
        )
```

最重要的组件是 `**GET_CONTRACT_BY_ID_QUERY**` 中的 Cypher 查询。此查询使用作为输入参数提供的 **contract_id** 执行。输出是匹配的协议、其条款和相关方（每个方都有角色和注册国家/州）。

数据随后传递给一个工具函数 `_get_agreement`，该函数仅将数据映射到一个“Agreement”对象。协议是一个定义为 TypedDict 的类型。

```py
class Agreement(TypedDict):  
    contract_id: int
    agreement_name: str
    agreement_type: str
    effective_date: str
    expiration_date: str
    renewal_term: str
    notice_period_to_terminate_Renewal: str
    parties: List[Party]
    clauses: List[ContractClause]
```

## 获取没有特定条款类型的合同 — 另一个 Cypher 检索函数

该函数展示了知识图谱的一个强大功能，即测试关系的不存在。

`get_contracts_without_clause()` 函数从 Neo4J 数据库中检索所有 **不** 包含特定条款类型的合同（`Agreements`）。该函数接受一个 `ClauseType` 作为输入，并返回一个符合条件的 `Agreement` 对象列表。

这种类型的数据检索信息无法通过向量搜索轻松实现。完整的实现如下

```py
async def get_contracts_without_clause(self, clause_type: ClauseType) -> List[Agreement]:
        GET_CONTRACT_WITHOUT_CLAUSE_TYPE_QUERY = """
            MATCH (a:Agreement)
            OPTIONAL MATCH (a)-[:HAS_CLAUSE]->(cc:ContractClause {type: $clause_type})
            WITH a,cc
            WHERE cc is NULL
            WITH a
            MATCH (country:Country)-[i:INCORPORATED_IN]-(p:Organization)-[r:IS_PARTY_TO]-(a)
            RETURN a as agreement, collect(p) as parties, collect(r) as roles, collect(country) as countries, collect(i) as states
        """

        #run the Cypher query
        records, _ , _ = self._driver.execute_query(GET_CONTRACT_WITHOUT_CLAUSE_TYPE_QUERY,{'clause_type':clause_type.value})

        all_agreements = []
        for row in records:
            agreement_node =  row['agreement']
            party_list =  row['parties']
            role_list =  row['roles']
            country_list = row['countries']
            state_list = row['states']
            agreement : Agreement = await self._get_agreement(
                format="short",
                agreement_node=agreement_node,
                party_list=party_list,
                role_list=role_list,
                country_list=country_list,
                state_list=state_list
            )
            all_agreements.append(agreement)
        return all_agreements
```

一如既往，格式与前一个函数相似。Cypher 查询 `**GET_CONTRACTS_WITHOUT_CLAUSE_TYPE_QUERY**` 定义了要匹配的节点和关系模式。它执行 **可选匹配** 以过滤掉包含条款类型的合同，并收集有关协议的相关数据，例如相关方及其详细信息。

然后，函数构建并返回一个 `Agreement` 对象列表，其中封装了每个匹配协议的所有相关信息。

## 获取具有语义相似文本的合同 — 向量搜索 + 图数据检索函数

`get_contracts_similar_text()` 函数旨在查找包含与提供的 `clause_text` 相似文本的条款的协议（合同）。它使用语义向量搜索来识别相关摘录，然后遍历图谱以返回有关相应协议和条款的信息，以及这些摘录的来源。

该函数利用定义在每个摘录的 “text” 属性上的向量索引。它使用最近发布的 [Neo4J GraphRAG 包](https://neo4j.com/blog/graphrag-python-package/) 来简化执行语义搜索和图遍历所需的 Cypher 代码。

```py
async def get_contracts_similar_text(self, clause_text: str) -> List[Agreement]:

        #Cypher to traverse from the semantically similar excerpts back to the agreement
        EXCERPT_TO_AGREEMENT_TRAVERSAL_QUERY="""
            MATCH (a:Agreement)-[:HAS_CLAUSE]->(cc:ContractClause)-[:HAS_EXCERPT]-(node) 
            RETURN a.name as agreement_name, a.contract_id as contract_id, cc.type as clause_type, node.text as excerpt
        """

        #Set up vector Cypher retriever
        retriever = VectorCypherRetriever(
            driver= self._driver,  
            index_name="excerpt_embedding",
            embedder=self._openai_embedder, 
            retrieval_query=EXCERPT_TO_AGREEMENT_TRAVERSAL_QUERY,
            result_formatter=my_vector_search_excerpt_record_formatter
        )

        # run vector search query on excerpts and get results containing the relevant agreement and clause 
        retriever_result = retriever.search(query_text=clause_text, top_k=3)

        #set up List of Agreements (with partial data) to be returned
        agreements = []
        for item in retriever_result.items:
            //extract information from returned items and append agreement to results
            // full code not shown here but available on the Github repo

        return agreements
```

让我们来回顾一下这个数据检索函数的主要组件。

+   Neo4j GraphRAG **VectorCypherRetriever** 允许开发者在向量索引上执行语义相似度分析。在我们的案例中，对于每一个语义相似的摘录“节点”，会使用额外的 Cypher 表达式来获取与该节点相关的图中其他节点。

+   **VectorCypherRetriever 的参数** 非常直接。`index_name` 是执行语义相似度分析的向量索引。`embedder` 为一段文本生成向量嵌入。`driver` 只是 Neo4j Python 驱动的一个实例。`retrieval_query` 指定与每个由语义相似度识别的“摘录”节点相关联的其他节点和关系。

+   `EXCERPT_TO_AGREEMENT_TRAVERSAL_QUERY`

    指定要检索的额外节点。在这种情况下，对于每个摘录，我们都在检索与其相关的合同条款和相应的协议。

```py
EXCERPT_TO_AGREEMENT_TRAVERSAL_QUERY="""
  MATCH (a:Agreement)-[:HAS_CLAUSE]->(cc:ContractClause)-[:HAS_EXCERPT]-(node) 
  RETURN a.name as agreement_name, a.contract_id as contract_id, cc.type as clause_type, node.text as excerpt
"""
```

## 执行自然语言查询——一个 Text 2Cypher 数据检索函数

`answer_aggregation_question()` 函数利用 Neo4j GraphRAG 包中的 “Text2CypherRetriever” 来回答自然语言的问题。Text2CypherRetriever 使用 LLM 将用户问题转换为 Cypher 查询，并在 Neo4j 数据库中执行该查询。

该函数利用 **OpenAI gpt-4o** 生成所需的 Cypher 查询。让我们逐步了解这个数据检索函数的主要组件。

```py
 async def answer_aggregation_question(self, user_question) -> str:
        answer = ""

        NEO4J_SCHEMA = """
            omitted for brevity (see below for the full value)
        """

        # Initialize the retriever
        retriever = Text2CypherRetriever(
            driver=self._driver,
            llm=self._llm,
            neo4j_schema=NEO4J_SCHEMA
        )

        # Generate a Cypher query using the LLM, send it to the Neo4j database, and return the results
        retriever_result = retriever.search(query_text=user_question)

        for item in retriever_result.items:
            content = str(item.content)
            if content:
                answer += content + '\n\n'

        return answer
```

这个函数利用 Neo4j GraphRAG 包中的 “**Text2CypherRetriever**”。它使用 LLM，在本例中使用的是 OpenAI LLM，将用户问题（自然语言）转换为一个在数据库中执行的 Cypher 查询，并返回该查询的结果。

确保 LLM 生成一个使用数据库中定义的节点、关系和属性的查询的关键要素是向 LLM 提供模式的文本描述。

在我们的案例中，我们使用以下的数据模型表示已经足够。

```py
NEO4J_SCHEMA = """
Node properties:
Agreement {agreement_type: STRING, contract_id: INTEGER,effective_date: STRING,renewal_term: STRING, name: STRING}
ContractClause {name: STRING, type: STRING}
ClauseType {name: STRING}
Country {name: STRING}
Excerpt {text: STRING}
Organization {name: STRING}

Relationship properties:
IS_PARTY_TO {role: STRING}
GOVERNED_BY_LAW {state: STRING}
HAS_CLAUSE {type: STRING}
INCORPORATED_IN {state: STRING}

The relationships:
(:Agreement)-[:HAS_CLAUSE]->(:ContractClause)
(:ContractClause)-[:HAS_EXCERPT]->(:Excerpt)
(:ContractClause)-[:HAS_TYPE]->(:ClauseType)
(:Agreement)-[:GOVERNED_BY_LAW]->(:Country)
(:Organization)-[:IS_PARTY_TO]->(:Agreement)
(:Organization)-[:INCORPORATED_IN]->(:Country)
  """
```

# 第四步：构建一个问答代理

拥有我们的知识图谱数据检索函数后，我们已经准备好构建一个由 GraphRAG 支持的代理 :-)

让我们建立一个能够回答关于合同的用户查询的聊天代理，使用 OpenAI 的 **gpt-4o** 模型、我们的数据检索函数和一个由 Neo4j 支持的知识图谱。

我们将使用 Microsoft **Semantic Kernel，这个框架** 允许开发者将 LLM 函数调用与现有的 API 和数据检索功能进行集成。

该框架使用一个名为 **Plugins** 的概念来表示内核可以执行的特定功能。在我们的案例中，所有在 “ContractPlugin” 中定义的数据检索函数都可以被 LLM 用来回答问题。

该框架使用**记忆**的概念来保存用户与代理之间的所有交互，以及执行的功能和检索的数据。

一个极其简单的基于终端的代理可以通过几行代码实现。下面的代码片段展示了代理的主要部分（导入和环境变量已删除）。

```py
logging.basicConfig(level=logging.INFO)

# Initialize the kernel
kernel = Kernel()

# Add the Contract Search plugin to the kernel
contract_search_neo4j = ContractSearchService(NEO4J_URI,NEO4J_USER,NEO4J_PASSWORD)
kernel.add_plugin(ContractPlugin(contract_search_service=contract_search_neo4j),plugin_name="contract_search")

# Add the OpenAI chat completion service to the Kernel
kernel.add_service(OpenAIChatCompletion(ai_model_id="gpt-4o",api_key=OPENAI_KEY, service_id=service_id))

# Enable automatic function calling
settings: OpenAIChatPromptExecutionSettings = kernel.get_prompt_execution_settings_from_service_id(service_id=service_id)
settings.function_choice_behavior = FunctionChoiceBehavior.Auto(filters={"included_plugins": ["contract_search"]})

# Create a history of the conversation
history = ChatHistory()

async def basic_agent() :
    userInput = None
    while True:
        # Collect user input
        userInput = input("User > ")

        # Terminate the loop if the user says "exit"
        if userInput == "exit":
            break

        # Add user input to the history
        history.add_user_message(userInput)

        # 3\. Get the response from the AI with automatic function calling
        chat_completion : OpenAIChatCompletion = kernel.get_service(type=ChatCompletionClientBase)
        result = (await chat_completion.get_chat_message_contents(
            chat_history=history,
            settings=settings,
            kernel=kernel,
            arguments=KernelArguments(),
        ))[0]

        # Print the results
        print("Assistant > " + str(result))

        # Add the message from the agent to the chat history
        history.add_message(result)

if __name__ == "__main__":
    asyncio.run(basic_agent())
```

# 让我们问问我们的代理一些非平凡的问题

> **1\. 查找我有价格限制但没有保险条款的合同**

```py
Assistant > I found the following contract that contains a Price Restrictions clause but does not contain an Insurance clause:

- **Name:** Birch First Global Investments Inc. and Mount Knowledge Holdings Inc. MARKETING AFFILIATE AGREEMENT
- **Agreement Type:** MARKETING AFFILIATE AGREEMENT
- **Parties:**
  - **MOUNT KNOWLEDGE HOLDINGS INC.** (Marketing Affiliate), incorporated in Nevada, USA
  - **BIRCH FIRST GLOBAL INVESTMENTS INC.** (Company), incorporated in the Virgin Islands (VIR)

Would you like to know more details about this contract?
```

在这里，语义代理规划器已审查内核中可用的功能，并确定回答这个问题需要调用两个单独的功能，而这两个功能本身无法提供完整的答案。

首先，它执行**“获取带有[价格限制]条款的合同”**功能，然后执行**“获取不含[保险]条款的合同”**功能。接着，规划器将这两个功能的结果结合起来，找到交集，最终得出正确答案。

如果你查看调试信息，你可以确认LLM执行的调用顺序，以及调用中消耗的token数量。注意具有两个数据检索功能的调用。

![](../Images/fab333a8a824e1d0ce2c3367bcfa10ce.png)

> **2\. 是的，请提供更多细节**

```py
Assistant > Here are the detailed information about the contract:

### Contract Details:
- **Name:** Birch First Global Investments Inc. and Mount Knowledge Holdings Inc. MARKETING AFFILIATE AGREEMENT
- **Agreement Type:** MARKETING AFFILIATE AGREEMENT
- **Expiration Date:** December 31, 2014
- **Renewal Term:** One (1) year

### Parties Involved:
1\. **MOUNT KNOWLEDGE HOLDINGS INC.** 
   - **Role:** Marketing Affiliate
   - **Incorporation Country:** USA
   - **Incorporation State:** Nevada
2\. **BIRCH FIRST GLOBAL INVESTMENTS INC.**
   - **Role:** Company
   - **Incorporation Country:** Virgin Islands (VIR)

### Clauses Included:
- No-Solicit Of Employees
- Third Party Beneficiary
- Price Restrictions
- Affiliate License-Licensee
- Affiliate License-Licensor
- Cap On Liability
- Audit Rights
- Warranty Duration
- Post-Termination Services
- Non-Transferable License
- License grant
- IP Ownership Assignment
- Minimum Commitment
- Anti-Assignment
- Non-Disparagement

### Clauses Not Included:
- Insurance

Is there anything specific you would like to know or any particular clause details you are interested in?
```

在这种情况下，背后，LLM使用了**“按ID查找合同”**。你可能已经注意到，显示给用户的输出并未包含合同的ID。然而，语义内核已经将之前调用返回的所有信息存储在对话记忆中！

这种代理决定调用哪个功能、使用记忆以及将用户查询自动转换为具有正确参数的功能调用的强大组合，解释了构建于语义内核上的代理背后的**“魔力”**！

> **3\. 是否有任何合同提到可以不时调整价格的能力**

```py
Assistant > Yes, there is a contract that mentions 
the ability to make price changes from time to time. 
Here is the relevant excerpt:

1\. **Birch First Global Investments Inc. and Mount Knowledge Holdings Inc. MARKETING AFFILIATE AGREEMENT**
   - **Contract ID:** 1
   - **Clause Type:** Price Restrictions
   - **Excerpt:**
     ```

    公司保留随时根据其单独且绝对的判断更改流程和/或费用的权利。如果技术价格和/或费用发生变化，公司应在任何此类变更生效日期前九十（90）天书面通知MA。所有其他价格和/或费用可在无需提前通知MA的情况下进行调整。

    ```py

Would you like more information about this contract or any other details?
```

为了回答这个问题，语义内核正在执行**get_contract_similar_text 数据**检索功能。

```py
User > any contract mentions the ability to make price changes from time to time
INFO:semantic_kernel.connectors.ai.open_ai.services.open_ai_chat_completion_base:processing 1 tool calls in parallel.

INFO:semantic_kernel.kernel:Calling contract_search-get_contracts_similar_text
 function with args: {"clause_text":"price changes from time to time"}

Assistant > Yes, there is a contract that mentions the ability to make price changes from time to time. Here is the relevant excerpt:

1\. **Birch First Global Investments Inc. and Mount Knowledge Holdings Inc. MARKETING AFFILIATE AGREEMENT**
   - **Contract ID:** 1
   - **Clause Type:** Price Restrictions
   - **Excerpt:**
     ```

    公司保留随时根据其单独且绝对的判断更改流程和/或费用的权利。如果技术价格和/或费用发生变化，公司应在任何此类变更生效日期前九十（90）天书面通知MA。所有其他价格和/或费用可在无需提前通知MA的情况下进行调整。

    ```py
```

如前所述，该功能使用向量搜索和Cypher查询来检索不仅在语义上相似的摘录，还包括包含它们的相关合同和条款。

最后，让我们询问数据库中有多少合同

> 4\. 数据库中有多少个合同？

这个问题要求LLM使用Text2Cypher数据检索器

```py
Assistant > There are a total of 3 contracts in the database.
```

调试信息确认语义内核运行了Text2Cypher数据检索功能，且“user_question” = “数据库中有多少个合同？”

```py
User >  how many contracts are there on the database?
INFO:httpx:HTTP Request: POST https://api.openai.com/v1/chat/completions "HTTP/1.1 200 OK"
INFO:semantic_kernel.connectors.ai.open_ai.services.open_ai_chat_completion_base:processing 1 tool calls in parallel.

INFO:semantic_kernel.kernel:Calling contract_search-answer_aggregation_question function 
with args: {"user_question":"How many contracts are there in the database?"}

INFO:semantic_kernel.functions.kernel_function:Function completed. Duration: 0.588805s

INFO:semantic_kernel.connectors.ai.open_ai.services.open_ai_handler:OpenAI usage: CompletionUsage(completion_tokens=13, prompt_tokens=3328, total_tokens=3341, completion_tokens_details={'reasoning_tokens': 0})

Assistant > There are a total of 3 contracts in the database.
```

# **亲自尝试**

[github 仓库](https://github.com/neo4j-product-examples/graphrag-contract-review)包含一个Streamlit应用程序，提供了一个更优雅的代理UI。我们鼓励你与代理互动，并对ContractPlugin进行修改，以提升代理处理更多问题的能力！

# 结论

在这篇博客中，我们探讨了一个Graph Retrieval Augmented Generation（GraphRAG）方法，将商业合同审查这一劳动密集型任务转化为更高效、更智能的AI驱动过程。

通过利用大型语言模型（LLMs）和提示进行针对性的信息提取，使用Neo4j构建结构化知识图谱，实现简单的数据检索功能，并最终开发出问答代理，我们创建了一种能够有效处理复杂问题的智能解决方案。

这种方法减少了传统基于向量搜索的RAG中发现的低效问题，而是专注于提取相关信息，减少了不必要的向量嵌入，简化了整体过程。我们希望从合同导入到互动问答代理的这段旅程能激励你在自己的项目中使用GraphRAG，以提高效率和更智能的AI驱动决策。

今天就开始构建你自己的商业合同审查代理，并亲身体验GraphRAG的强大功能！

# 资源

对于那些渴望深入了解的人，请查看以下链接的资源：

+   [带有代码和详细说明的GitHub仓库](https://github.com/neo4j-product-examples/graphrag-contract-review)

+   [法律合同的合同理解Atticus数据集(CUAD)](https://github.com/TheAtticusProject/cuad)（Github）

+   [CUAD：一个专家注释的法律合同审查NLP数据集。Hendrycks，Burns，Chen，Ball。NeurIPS 2021](https://arxiv.org/pdf/2103.06268)

+   [Neo4j GraphRAG包发布博客](https://neo4j.com/blog/graphrag-python-package/)

+   [微软语义内核库](https://learn.microsoft.com/en-us/semantic-kernel/overview/)

除非另有说明，所有图片均由作者提供
