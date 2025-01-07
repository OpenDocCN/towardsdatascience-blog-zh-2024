# 使用 Gemini 为任何类型的 PDF 构建文档 AI 流水线

> 原文：[https://towardsdatascience.com/build-a-document-ai-pipeline-for-any-type-of-pdf-with-gemini-9221c8e143db?source=collection_archive---------0-----------------------#2024-12-15](https://towardsdatascience.com/build-a-document-ai-pipeline-for-any-type-of-pdf-with-gemini-9221c8e143db?source=collection_archive---------0-----------------------#2024-12-15)

## 表格、图片、图表或公式已经不再是问题！提供完整代码。

[](https://medium.com/@CVxTz?source=post_page---byline--9221c8e143db--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--9221c8e143db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9221c8e143db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9221c8e143db--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--9221c8e143db--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9221c8e143db--------------------------------) ·阅读时间 10 分钟·2024年12月15日

--

![](../Images/04f5f0506207581622a222ae56c38208.png)

图片来源：[Matt Noble](https://unsplash.com/@mcnoble?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

自动化文档处理是 ChatGPT 革命中的最大赢家之一，因为大型语言模型（LLM）能够在零样本环境下处理各种主题和任务，这意味着它们不需要领域内的标注训练数据。这使得构建基于 AI 的应用程序来处理、解析和自动理解任意文档变得更加容易。尽管使用 LLM 的简单方法仍然受到非文本上下文的制约，如图表、图片和表格，但这正是我们将在本博客中尝试解决的问题，特别是关注 PDF 文件。

从基础层面来说，PDF 文件仅仅是由字符、图片和线条以及它们的精确坐标组成。它们没有内在的“文本”结构，并且并非为了作为文本进行处理而构建，而只是为了查看而存在。这就是为什么与它们打交道会很困难，因为仅限文本的方法无法捕捉到这些文档中的所有布局和视觉元素，导致大量的上下文和信息丢失。

绕过这一“仅限文本”的限制的一种方法是，通过在将文档输入到 LLM 之前，进行大量的预处理工作，检测表格、图片和布局。表格可以解析为 Markdown 或 JSON，图片和图表可以通过它们的标题来表示，文本则可以按原样输入。然而，这种方法需要自定义模型，并且仍然会导致一些信息丢失，那么我们能做得更好吗？

# 多模态 LLMs

最近的大型模型大多是多模态的，意味着它们能够处理文本、代码和图像等多种模态。这为我们的问题提供了更简单的解决方案，可以让一个模型一次性处理所有内容。因此，代替为图像加上标题和解析表格，我们可以直接将页面作为图像输入，并按原样处理。我们的管道将能够加载PDF，提取每一页作为图像，使用LLM将其拆分为多个分块，并为每个分块创建索引。如果某个分块被检索到，完整的页面将被包含在LLM的上下文中进行任务处理。接下来，我们将详细说明如何在实践中实现这一点。

# 管道

我们实现的管道是一个两步过程。首先，我们将每一页分割成重要的分块并对每个分块进行摘要。其次，我们在每次获取请求时检索这些分块并将完整上下文与每个检索到的分块一同包含在LLM上下文中。

## 步骤 1：页面分割与摘要

我们将页面提取为图像，并将每个图像传递给多模态LLM进行分割。像Gemini这样的模型可以轻松理解并处理页面布局：

+   **表格**被视为一个分块。

+   **图形**构成另一个分块。

+   **文本块**被分割成独立的分块。

+   …

对于每个元素，LLM会生成一个摘要，可以嵌入并索引到向量数据库中。

## 步骤 2：嵌入与上下文检索

在本教程中，我们将仅使用文本嵌入以简化操作，但一种改进方法是直接使用视觉嵌入。

数据库中的每一条记录包括：

+   分块的摘要。

+   找到该项的页面编号。

+   提供完整页面图像的链接以增加上下文信息。

该架构允许**局部级别搜索**（按分块级别），同时保持**上下文跟踪**（通过链接回完整页面）。例如，如果搜索查询检索到某个项，代理可以将整个页面图像包含进来，以便向LLM提供完整的布局和额外的上下文信息，从而最大化响应质量。

通过提供完整图像，所有的视觉提示和重要的布局信息（如图像、标题、项目符号…）以及邻近的项（表格、段落等）都可以在生成响应时提供给LLM。

# 代理

我们将每个步骤实现为一个独立的、可重用的代理：

第一个代理用于解析、分块和摘要。这涉及到将文档分割成重要的分块，并为每个分块生成摘要。这个代理每个PDF只需要运行一次，用于预处理文档。

第二个代理负责管理索引、搜索和检索。这包括将分块的嵌入插入到向量数据库中，以便高效搜索。索引在每个文档中只执行一次，而搜索可以根据不同的查询重复进行。

对于两个代理，我们使用**Gemini**，一个具有强大视觉理解能力的多模态LLM。

## 解析与分块代理

第一个代理负责将每一页分割成有意义的片段，并对每个片段进行总结，按照以下步骤进行：

**步骤 1：将PDF页面提取为图像**

我们使用`pdf2image`库。然后将图像编码为Base64格式，以简化将其添加到LLM请求中。

下面是实现过程：

```py
from document_ai_agents.document_utils import extract_images_from_pdf
from document_ai_agents.image_utils import pil_image_to_base64_jpeg
from pathlib import Path

class DocumentParsingAgent:
    @classmethod
    def get_images(cls, state):
        """
        Extract pages of a PDF as Base64-encoded JPEG images.
        """
        assert Path(state.document_path).is_file(), "File does not exist"
        # Extract images from PDF
        images = extract_images_from_pdf(state.document_path)
        assert images, "No images extracted"
        # Convert images to Base64-encoded JPEG
        pages_as_base64_jpeg_images = [pil_image_to_base64_jpeg(x) for x in images]
        return {"pages_as_base64_jpeg_images": pages_as_base64_jpeg_images}
```

`extract_images_from_pdf`：将PDF的每一页提取为PIL图像。

`pil_image_to_base64_jpeg`：将图像转换为Base64编码的JPEG格式。

**步骤 2：分段和总结**

每个图像将被发送到LLM进行分割和总结。我们使用结构化输出以确保获得我们期望的格式的预测：

```py
from pydantic import BaseModel, Field
from typing import Literal
import json
import google.generativeai as genai
from langchain_core.documents import Document

class DetectedLayoutItem(BaseModel):
    """
    Schema for each detected layout element on a page.
    """
    element_type: Literal["Table", "Figure", "Image", "Text-block"] = Field(
        ..., 
        description="Type of detected item. Examples: Table, Figure, Image, Text-block."
    )
    summary: str = Field(..., description="A detailed description of the layout item.")

class LayoutElements(BaseModel):
    """
    Schema for the list of layout elements on a page.
    """
    layout_items: list[DetectedLayoutItem] = []

class FindLayoutItemsInput(BaseModel):
    """
    Input schema for processing a single page.
    """
    document_path: str
    base64_jpeg: str
    page_number: int

class DocumentParsingAgent:
    def __init__(self, model_name="gemini-1.5-flash-002"):
        """
        Initialize the LLM with the appropriate schema.
        """
        layout_elements_schema = prepare_schema_for_gemini(LayoutElements)
        self.model_name = model_name
        self.model = genai.GenerativeModel(
            self.model_name,
            generation_config={
                "response_mime_type": "application/json",
                "response_schema": layout_elements_schema,
            },
        )
    def find_layout_items(self, state: FindLayoutItemsInput):
        """
        Send a page image to the LLM for segmentation and summarization.
        """
        messages = [
            f"Find and summarize all the relevant layout elements in this PDF page in the following format: "
            f"{LayoutElements.schema_json()}. "
            f"Tables should have at least two columns and at least two rows. "
            f"The coordinates should overlap with each layout item.",
            {"mime_type": "image/jpeg", "data": state.base64_jpeg},
        ]
        # Send the prompt to the LLM
        result = self.model.generate_content(messages)
        data = json.loads(result.text)

        # Convert the JSON output into documents
        documents = [
            Document(
                page_content=item["summary"],
                metadata={
                    "page_number": state.page_number,
                    "element_type": item["element_type"],
                    "document_path": state.document_path,
                },
            )
            for item in data["layout_items"]
        ]
        return {"documents": documents}
```

`LayoutElements`模式定义了输出的结构，其中包括每种布局项类型（表格、图形等）及其总结。

**步骤 3：页面的并行处理**

页面并行处理以提高速度。以下方法创建一个任务列表，以便同时处理所有页面图像，因为处理是IO绑定的：

```py
from langgraph.types import Send

class DocumentParsingAgent:
    @classmethod
    def continue_to_find_layout_items(cls, state):
        """
        Generate tasks to process each page in parallel.
        """
        return [
            Send(
                "find_layout_items",
                FindLayoutItemsInput(
                    base64_jpeg=base64_jpeg,
                    page_number=i,
                    document_path=state.document_path,
                ),
            )
            for i, base64_jpeg in enumerate(state.pages_as_base64_jpeg_images)
        ]
```

每一页将作为独立任务发送到`find_layout_items`函数。

**完整工作流程**

该代理的工作流程使用`StateGraph`构建，将图像提取和布局检测步骤链接成一个统一的管道 ->

```py
from langgraph.graph import StateGraph, START, END

class DocumentParsingAgent:
    def build_agent(self):
        """
        Build the agent workflow using a state graph.
        """
        builder = StateGraph(DocumentLayoutParsingState)

        # Add nodes for image extraction and layout item detection
        builder.add_node("get_images", self.get_images)
        builder.add_node("find_layout_items", self.find_layout_items)
        # Define the flow of the graph
        builder.add_edge(START, "get_images")
        builder.add_conditional_edges("get_images", self.continue_to_find_layout_items)
        builder.add_edge("find_layout_items", END)

        self.graph = builder.compile()
```

要在一个样本PDF上运行代理，我们需要执行以下操作：

```py
if __name__ == "__main__":
    _state = DocumentLayoutParsingState(
        document_path="path/to/document.pdf"
    )
    agent = DocumentParsingAgent()

    # Step 1: Extract images from PDF
    result_images = agent.get_images(_state)
    _state.pages_as_base64_jpeg_images = result_images["pages_as_base64_jpeg_images"]

    # Step 2: Process the first page (as an example)
    result_layout = agent.find_layout_items(
        FindLayoutItemsInput(
            base64_jpeg=_state.pages_as_base64_jpeg_images[0],
            page_number=0,
            document_path=_state.document_path,
        )
    )
    # Display the results
    for item in result_layout["documents"]:
        print(item.page_content)
        print(item.metadata["element_type"]) 
```

这将生成一个解析、分段和总结后的PDF表示，它是我们接下来要构建的第二个代理的输入。

## RAG代理

这个第二个代理负责索引和检索部分。它将前一个代理的文档保存到向量数据库中，并使用结果进行检索。这可以分为两个独立的步骤，索引和检索。

**步骤 1：索引拆分文档**

使用生成的总结，我们将它们向量化并保存到ChromaDB数据库中：

```py
class DocumentRAGAgent:
    def index_documents(self, state: DocumentRAGState):
        """
        Index the parsed documents into the vector store.
        """
        assert state.documents, "Documents should have at least one element"
        # Check if the document is already indexed
        if self.vector_store.get(where={"document_path": state.document_path})["ids"]:
            logger.info(
                "Documents for this file are already indexed, exiting this node"
            )
            return  # Skip indexing if already done
        # Add parsed documents to the vector store
        self.vector_store.add_documents(state.documents)
        logger.info(f"Indexed {len(state.documents)} documents for {state.document_path}")
```

`index_documents`方法将片段总结嵌入到向量存储中。我们保留文档路径和页面编号等元数据，以备后续使用。

**步骤 2：处理问题**

当用户提出问题时，代理会在向量存储中搜索最相关的片段。它检索总结和相应的页面图像以便理解上下文。

```py
class DocumentRAGAgent:
    def answer_question(self, state: DocumentRAGState):
        """
        Retrieve relevant chunks and generate a response to the user's question.
        """
        # Retrieve the top-k relevant documents based on the query
        relevant_documents: list[Document] = self.retriever.invoke(state.question)

        # Retrieve corresponding page images (avoid duplicates)
        images = list(
            set(
                [
                    state.pages_as_base64_jpeg_images[doc.metadata["page_number"]]
                    for doc in relevant_documents
                ]
            )
        )
        logger.info(f"Responding to question: {state.question}")
        # Construct the prompt: Combine images, relevant summaries, and the question
        messages = (
            [{"mime_type": "image/jpeg", "data": base64_jpeg} for base64_jpeg in images]
            + [doc.page_content for doc in relevant_documents]
            + [
                f"Answer this question using the context images and text elements only: {state.question}",
            ]
        )
        # Generate the response using the LLM
        response = self.model.generate_content(messages)
        return {"response": response.text, "relevant_documents": relevant_documents}
```

检索器查询向量存储以查找与用户问题最相关的片段。然后我们为LLM（Gemini）构建上下文，结合文本片段和图像以生成回应。

**完整代理工作流程**

代理的工作流程包括两个阶段，一个是索引阶段，另一个是问答阶段：

```py
class DocumentRAGAgent:
    def build_agent(self):
        """
        Build the RAG agent workflow.
        """
        builder = StateGraph(DocumentRAGState)
        # Add nodes for indexing and answering questions
        builder.add_node("index_documents", self.index_documents)
        builder.add_node("answer_question", self.answer_question)
        # Define the workflow
        builder.add_edge(START, "index_documents")
        builder.add_edge("index_documents", "answer_question")
        builder.add_edge("answer_question", END)
        self.graph = builder.compile()
```

**示例运行**

```py
if __name__ == "__main__":
    from pathlib import Path

  # Import the first agent to parse the document
    from document_ai_agents.document_parsing_agent import (
        DocumentLayoutParsingState,
        DocumentParsingAgent,
    )
    # Step 1: Parse the document using the first agent
    state1 = DocumentLayoutParsingState(
        document_path=str(Path(__file__).parents[1] / "data" / "docs.pdf")
    )
    agent1 = DocumentParsingAgent()
    result1 = agent1.graph.invoke(state1)
    # Step 2: Set up the second agent for retrieval and answering
    state2 = DocumentRAGState(
        question="Who was acknowledged in this paper?",
        document_path=str(Path(__file__).parents[1] / "data" / "docs.pdf"),
        pages_as_base64_jpeg_images=result1["pages_as_base64_jpeg_images"],
        documents=result1["documents"],
    )
    agent2 = DocumentRAGAgent()
    # Index the documents
    agent2.graph.invoke(state2)
    # Answer the first question
    result2 = agent2.graph.invoke(state2)
    print(result2["response"])
    # Answer a second question
    state3 = DocumentRAGState(
        question="What is the macro average when fine-tuning on PubLayNet using M-RCNN?",
        document_path=str(Path(__file__).parents[1] / "data" / "docs.pdf"),
        pages_as_base64_jpeg_images=result1["pages_as_base64_jpeg_images"],
        documents=result1["documents"],
    )
    result3 = agent2.graph.invoke(state3)
    print(result3["response"])
```

通过此实现，管道完成了文档处理、检索和问答功能。

# 示例：使用文档AI管道

让我们通过一个实际示例来操作，使用文档[LLM & Adaptation.pdf](https://github.com/SharifiZarchi/Introduction_to_Machine_Learning/blob/main/Slides/Chapter_05_Natural_Language_Processing/04-LLM%26Adaptation/LLM%20%26%20Adaptation.pdf)，它包含39页幻灯片，包含文本、公式和图形（CC BY 4.0）。

## 第1步：解析和总结文档（代理1）

+   **执行时间**：解析39页文档花费了**29秒**。

+   **结果**：代理1生成了一个索引文档，其中包含每页的摘要和Base64编码的JPEG图片。

## 第2步：质疑文档（代理2）

我们提出了以下问题：

**“**解释LoRA，给出相关的公式**”**

## 结果：

检索的页面：

![](../Images/a96c96db2579443f470a142aa9c9d1a7.png)

来源：[LLM & Adaptation.pdf](https://github.com/SharifiZarchi/Introduction_to_Machine_Learning/blob/main/Slides/Chapter_05_Natural_Language_Processing/04-LLM%26Adaptation/LLM%20%26%20Adaptation.pdf) 许可证 CC-BY

## 来自LLM的回应

![](../Images/4af2dc191f5a778641c7a0e0c0b2fcb8.png)

图片来源：作者。

LLM能够通过利用生成连贯且正确回应的视觉上下文，将公式和图形纳入其回应中。

# 结论

在这个简短的教程中，我们看到了如何通过利用近期LLMs的多模态性，进一步推进你的文档AI处理流程，并利用每个文档中的完整视觉上下文，来提升你从信息提取或RAG流程中获得的输出质量。

我们构建了一个更强大的文档分割步骤，能够检测到重要的项目，如段落、表格和图形，并对其进行总结，随后使用这一第一步的结果来查询项目和页面集合，利用Gemini给出相关且精准的答案。作为下一步，你可以在你的使用案例和文档上尝试，试着使用一个可扩展的向量数据库，并将这些代理部署为你AI应用的一部分。

完整代码和示例请参见：[https://github.com/CVxTz/document_ai_agents](https://github.com/CVxTz/document_ai_agents)

感谢阅读！😃
