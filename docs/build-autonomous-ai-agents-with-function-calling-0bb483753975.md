# 使用函数调用构建自主AI代理

> 原文：[https://towardsdatascience.com/build-autonomous-ai-agents-with-function-calling-0bb483753975?source=collection_archive---------0-----------------------#2024-04-02](https://towardsdatascience.com/build-autonomous-ai-agents-with-function-calling-0bb483753975?source=collection_archive---------0-----------------------#2024-04-02)

## 将您的聊天机器人转变为能够与外部API交互的代理

[](https://medium.com/@jyipkl?source=post_page---byline--0bb483753975--------------------------------)[![Julian Yip](../Images/2afc0ac6c4dcccaa57ffe70b2f5a14d0.png)](https://medium.com/@jyipkl?source=post_page---byline--0bb483753975--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0bb483753975--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0bb483753975--------------------------------) [Julian Yip](https://medium.com/@jyipkl?source=post_page---byline--0bb483753975--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0bb483753975--------------------------------) ·阅读时间 11 分钟·2024年4月2日

--

函数调用并不是什么新鲜事。2023年7月，OpenAI为其GPT模型引入了函数调用功能，现在这一功能正在被竞争对手采用。谷歌的Gemini API最近也支持了这一功能，而Anthropic正在将其集成到Claude中。函数调用正在成为大型语言模型（LLMs）中不可或缺的一部分，增强了它们的能力。学习这项技术会更加有用！

牢记这一点，我计划编写一份全面的教程，深入探讨函数调用，超越基本介绍（市面上已有很多相关教程）。重点将放在实际应用上，构建一个完全自主的AI代理，并将其与Streamlit集成，提供类似ChatGPT的界面。虽然使用OpenAI进行演示，但本教程也可以轻松适应支持函数调用的其他LLM，例如Gemini。

# **函数调用有什么用途？**

函数调用使开发者能够描述函数（即工具，您可以将其视为模型执行的操作，例如进行计算或下订单），并让模型智能地选择输出一个包含调用这些函数所需参数的 JSON 对象。更简单来说，它使得：

+   **自主决策**：模型能够智能地选择工具来回答问题。

+   **可靠的解析**：响应以 JSON 格式返回，而不是更典型的对话式响应。乍一看，这似乎没什么，但正是这种格式让大型语言模型（LLM）能够与外部系统连接，比如通过结构化输入的 API。

它开启了许多可能性：

+   **自主AI助手**：机器人可以与内部系统互动，执行诸如客户订单和退货等任务，而不仅仅是回答查询

+   **个人研究助手**：假设你正在计划旅行，助手可以搜索网页、抓取内容、比较选项，并将结果总结在Excel中。

+   **物联网语音命令**：模型可以控制设备或根据检测到的意图建议操作，比如调整空调温度。

*(稍微偏题：为了实现这些潜力，我们必须有一个系统化的方法来设计我们的提示并进行测试。我也写了一篇关于此的文章！)*

[](/prompt-like-a-data-scientist-auto-prompt-optimization-and-testing-with-dspy-ff699f030cb7?source=post_page-----0bb483753975--------------------------------) [## 像数据科学家一样构建提示：使用DSPy进行自动提示优化和测试

### 将机器学习方法应用于提示构建

towardsdatascience.com](/prompt-like-a-data-scientist-auto-prompt-optimization-and-testing-with-dspy-ff699f030cb7?source=post_page-----0bb483753975--------------------------------)

不再浪费时间，让我们深入探讨一下功能调用是什么！

# 功能调用的结构

借鉴[Gemini的功能调用文档](https://cloud.google.com/vertex-ai/generative-ai/docs/multimodal/function-calling)，功能调用具有以下结构，在OpenAI中也适用

![](../Images/9793c45c8d104b8ac96e104541df9644.png)

来自[Gemini的功能调用文档](https://cloud.google.com/vertex-ai/generative-ai/docs/multimodal/function-calling)的图片

1.  用户向应用程序发出提示

1.  应用程序传递用户提供的提示以及功能声明，这些功能声明是对模型可能使用的工具的描述

1.  基于功能声明，模型建议使用的工具和相关请求参数。**注意，模型仅输出建议的工具和参数，并不会实际调用这些功能**

1.  & 5\. 根据响应，应用程序调用相关的API

6\. & 7\. 来自API的响应再次传入模型，输出一个人类可读的响应

8\. 应用程序将最终响应返回给用户，然后从第1步开始重复。

这看起来可能有些复杂，但这个概念将通过示例详细说明

# 架构

在深入代码之前，先简要说明一下演示应用程序的架构

## **解决方案**

在这里，我们为游客访问酒店构建一个助手。该助手可以访问以下工具，使其能够访问外部应用程序。

+   `get_items`、`purchase_item`：通过API连接到存储在数据库中的产品目录，分别用于检索商品列表和进行购买

+   `rag_pipeline_func`：通过检索增强生成（RAG）连接到文档存储，从非结构化文本（例如酒店的宣传册）中获取信息

![](../Images/aa901baf05ab30875134a9a0b958aa14.png)

## **技术栈**

+   **嵌入模型**：[all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)

+   **向量数据库**：[Haystack的InMemoryDocumentStore](https://docs.haystack.deepset.ai/docs/inmemorydocumentstore)

+   **LLM**：[通过OpenRouter访问的GPT-4 Turbo](https://openrouter.ai/models/openai/gpt-4-1106-preview)。通过OpenRouter，你可以在香港无VPN访问不同的LLM API。此流程可以通过稍微修改代码，使用其他支持函数调用的LLM（例如Gemini）进行适配

+   **LLM框架**：[Haystack](https://haystack.deepset.ai/)因其易用性、出色的文档和管道构建的透明性而被选中。本教程实际上是他们[精彩教程](https://haystack.deepset.ai/tutorials/40_building_chat_application_with_function_calling)的延伸，主题相同

现在，让我们开始吧！

# 示例应用

## 准备工作

访问[Github](https://github.com/yip-kl/llm_function_calling_demo)来克隆我的代码。以下内容可以在`function_calling_demo` Notebook中找到

请先创建并激活一个虚拟环境，然后通过`pip install -r requirements.txt`安装所需的包

## **初始化**

我们首先连接到OpenRouter。或者，使用原始的`OpenAIChatGenerator`，如果没有覆盖`api_base_url`，也可以工作，前提是你有OpenAI的API密钥

```py
import os
from dotenv import load_dotenv
from haystack.components.generators.chat import OpenAIChatGenerator
from haystack.utils import Secret
from haystack.dataclasses import ChatMessage
from haystack.components.generators.utils import print_streaming_chunk

# Set your API key as environment variable before executing this
load_dotenv()
OPENROUTER_API_KEY = os.environ.get('OPENROUTER_API_KEY')

chat_generator = OpenAIChatGenerator(api_key=Secret.from_env_var("OPENROUTER_API_KEY"),
  api_base_url="https://openrouter.ai/api/v1",
  model="openai/gpt-4-turbo-preview",
        streaming_callback=print_streaming_chunk)
```

然后我们测试是否可以成功调用`chat_generator`

```py
chat_generator.run(messages=[ChatMessage.from_user("Return this text: 'test'")])
```

```py
---------- The response should look like this ----------
{'replies': [ChatMessage(content="'test'", role=<ChatRole.ASSISTANT: 'assistant'>, name=None, meta={'model': 'openai/gpt-4-turbo-preview', 'index': 0, 'finish_reason': 'stop', 'usage': {}})]}
```

## 步骤1：建立数据存储

在这里，我们建立了应用程序与两个数据源之间的连接：**文档存储**用于非结构化文本，**应用数据库**通过API连接

**使用管道索引文档**

我们在`documents`中提供样本文本，供模型执行检索增强生成（RAG）。这些文本被转换为嵌入并存储在内存文档存储中

```py
from haystack import Pipeline, Document
from haystack.document_stores.in_memory import InMemoryDocumentStore
from haystack.components.writers import DocumentWriter
from haystack.components.embedders import SentenceTransformersDocumentEmbedder

# Sample documents
documents = [
    Document(content="Coffee shop opens at 9am and closes at 5pm."),
    Document(content="Gym room opens at 6am and closes at 10pm.")
]

# Create the document store
document_store = InMemoryDocumentStore()

# Create a pipeline to turn the texts into embeddings and store them in the document store
indexing_pipeline = Pipeline()
indexing_pipeline.add_component(
    "doc_embedder", SentenceTransformersDocumentEmbedder(model="sentence-transformers/all-MiniLM-L6-v2")
)
indexing_pipeline.add_component("doc_writer", DocumentWriter(document_store=document_store))

indexing_pipeline.connect("doc_embedder.documents", "doc_writer.documents")

indexing_pipeline.run({"doc_embedder": {"documents": documents}})
```

它应该输出与我们之前创建的`documents`样本相对应的内容

```py
{'doc_writer': {'documents_written': 2}}
```

**启动API服务器**

使用Flask创建的API服务器位于`db_api.py`，用于连接SQLite。请通过在终端运行`python db_api.py`来启动它

![](../Images/7036bc280c989f445aeae16d0544b7ed.png)

如果成功执行，这将在终端显示

还要注意，一些初始数据已添加到`db_api.py`中

![](../Images/9adc825085a9c02489dc32ff3bf6fad1.png)

数据库中的示例数据

## 步骤2：定义函数

在这里，我们为模型准备实际的函数，以便在调用函数后（如**函数调用结构**中描述的第4-5步）进行调用

**RAG功能**

即`rag_pipeline_func`。这是让模型通过搜索存储在文档存储中的文本来提供答案。我们首先将RAG检索定义为Haystack管道

```py
from haystack.components.embedders import SentenceTransformersTextEmbedder
from haystack.components.retrievers.in_memory import InMemoryEmbeddingRetriever
from haystack.components.builders import PromptBuilder
from haystack.components.generators import OpenAIGenerator

template = """
Answer the questions based on the given context.

Context:
{% for document in documents %}
    {{ document.content }}
{% endfor %}
Question: {{ question }}
Answer:
"""
rag_pipe = Pipeline()
rag_pipe.add_component("embedder", SentenceTransformersTextEmbedder(model="sentence-transformers/all-MiniLM-L6-v2"))
rag_pipe.add_component("retriever", InMemoryEmbeddingRetriever(document_store=document_store))
rag_pipe.add_component("prompt_builder", PromptBuilder(template=template))
# Note to llm: We are using OpenAIGenerator, not the OpenAIChatGenerator, because the latter only accepts List[str] as input and cannot accept prompt_builder's str output
rag_pipe.add_component("llm", OpenAIGenerator(api_key=Secret.from_env_var("OPENROUTER_API_KEY"),
  api_base_url="https://openrouter.ai/api/v1",
  model="openai/gpt-4-turbo-preview"))

rag_pipe.connect("embedder.embedding", "retriever.query_embedding")
rag_pipe.connect("retriever", "prompt_builder.documents")
rag_pipe.connect("prompt_builder", "llm")
```

测试函数是否正常工作

```py
query = “When does the coffee shop open?”
rag_pipe.run({"embedder": {"text": query}, "prompt_builder": {"question": query}})
```

这应该会产生以下输出。注意，模型给出的`replies`来自我们之前提供的样本文档

```py
{'llm': {'replies': ['The coffee shop opens at 9am.'],
  'meta': [{'model': 'openai/gpt-4-turbo-preview',
    'index': 0,
    'finish_reason': 'stop',
    'usage': {'completion_tokens': 9,
     'prompt_tokens': 60,
     'total_tokens': 69,
     'total_cost': 0.00087}}]}}
```

然后我们可以将`rag_pipe`转化为一个函数，该函数仅提供`replies`，而不加入其他细节

```py
def rag_pipeline_func(query: str):
    result = rag_pipe.run({"embedder": {"text": query}, "prompt_builder": {"question": query}})

    return {"reply": result["llm"]["replies"][0]}
```

**API调用**

我们定义了`get_items`和`purchase_item`函数，用于与数据库交互

```py
# Flask's default local URL, change it if necessary
db_base_url = 'http://127.0.0.1:5000'

# Use requests to get the data from the database
import requests
import json

# get_categories is supplied as part of the prompt, it is not used as a tool
def get_categories():
    response = requests.get(f'{db_base_url}/category')
    data = response.json()
    return data

def get_items(ids=None,categories=None):
    params = {
        'id': ids,
        'category': categories,
    }
    response = requests.get(f'{db_base_url}/item', params=params)
    data = response.json()
    return data

def purchase_item(id,quantity):

    headers = {
    'Content-type':'application/json', 
    'Accept':'application/json'
    }

    data = {
        'id': id,
        'quantity': quantity,
    }
    response = requests.post(f'{db_base_url}/item/purchase', json=data, headers=headers)
    return response.json()
```

**定义工具列表**

现在我们已经定义了函数，我们需要让模型识别这些函数，并通过提供描述来指示它们如何使用。

由于我们在这里使用的是OpenAI，`tools`格式如下，遵循[OpenAI要求的格式](https://cookbook.openai.com/examples/function_calling_with_an_openapi_spec)

```py
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_items",
            "description": "Get a list of items from the database",
            "parameters": {
                "type": "object",
                "properties": {
                    "ids": {
                        "type": "string",
                        "description": "Comma separated list of item ids to fetch",
                    },
                    "categories": {
                        "type": "string",
                        "description": "Comma separated list of item categories to fetch",
                    },
                },
                "required": [],
            },
        }
    },
    {
        "type": "function",
        "function": {
            "name": "purchase_item",
            "description": "Purchase a particular item",
            "parameters": {
                "type": "object",
                "properties": {
                    "id": {
                        "type": "string",
                        "description": "The given product ID, product name is not accepted here. Please obtain the product ID from the database first.",
                    },
                    "quantity": {
                        "type": "integer",
                        "description": "Number of items to purchase",
                    },
                },
                "required": [],
            },
        }
    },
    {
        "type": "function",
        "function": {
            "name": "rag_pipeline_func",
            "description": "Get information from hotel brochure",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {
                        "type": "string",
                        "description": "The query to use in the search. Infer this from the user's message. It should be a question or a statement",
                    }
                },
                "required": ["query"],
            },
        },
    }
]
```

## **步骤3：将所有内容整合**

现在我们有了必要的输入来测试函数调用！在这里我们做了几件事：

1.  提供初始提示给模型，以便给它一些上下文

1.  提供一个示例用户生成的消息

1.  最重要的是，我们将工具列表传递给`tools`中的聊天生成器

```py
# 1\. Initial prompt
context = f"""You are an assistant to tourists visiting a hotel.
You have access to a database of items (which includes {get_categories()}) that tourists can buy, you also have access to the hotel's brochure.
If the tourist's question cannot be answered from the database, you can refer to the brochure.
If the tourist's question cannot be answered from the brochure, you can ask the tourist to ask the hotel staff.
"""
messages = [
    ChatMessage.from_system(context),
    # 2\. Sample message from user
    ChatMessage.from_user("Can I buy a coffee?"),
    ]

# 3\. Passing the tools list and invoke the chat generator
response = chat_generator.run(messages=messages, generation_kwargs= {"tools": tools})
response
```

```py
---------- Response ----------
{'replies': [ChatMessage(content='[{"index": 0, "id": "call_AkTWoiJzx5uJSgKW0WAI1yBB", "function": {"arguments": "{\\"categories\\":\\"Food and beverages\\"}", "name": "get_items"}, "type": "function"}]', role=<ChatRole.ASSISTANT: 'assistant'>, name=None, meta={'model': 'openai/gpt-4-turbo-preview', 'index': 0, 'finish_reason': 'tool_calls', 'usage': {}})]}
```

现在让我们检查响应。注意函数调用是如何返回模型选择的函数以及调用该函数的参数的。

```py
function_call = json.loads(response["replies"][0].content)[0]
function_name = function_call["function"]["name"]
function_args = json.loads(function_call["function"]["arguments"])
print("Function Name:", function_name)
print("Function Arguments:", function_args)
```

```py
---------- Response ----------
Function Name: get_items
Function Arguments: {‘categories’: ‘Food and beverages’}
```

当出现另一个问题时，模型将使用另一个更相关的工具

```py
# Another question
messages.append(ChatMessage.from_user("Where's the coffee shop?"))

# Invoke the chat generator, and passing the tools list
response = chat_generator.run(messages=messages, generation_kwargs= {"tools": tools})
function_call = json.loads(response["replies"][0].content)[0]
function_name = function_call["function"]["name"]
function_args = json.loads(function_call["function"]["arguments"])
print("Function Name:", function_name)
print("Function Arguments:", function_args)
```

```py
---------- Response ----------
Function Name: rag_pipeline_func
Function Arguments: {'query': "Where's the coffee shop?"}
```

再次注意，这里并没有实际调用函数，这是我们接下来要做的！

**调用函数**

然后我们可以将参数传递到选定的函数中

```py
## Find the correspoding function and call it with the given arguments
available_functions = {"get_items": get_items, "purchase_item": purchase_item,"rag_pipeline_func": rag_pipeline_func}
function_to_call = available_functions[function_name]
function_response = function_to_call(**function_args)
print("Function Response:", function_response)
```

```py
---------- Response ----------
Function Response: {'reply': 'The provided context does not specify a physical location for the coffee shop, only its operating hours. Therefore, I cannot determine where the coffee shop is located based on the given information.'}
```

然后`rag_pipeline_func`的响应可以作为上下文传递给聊天，通过将其附加到`messages`下，供模型提供最终答案

```py
messages.append(ChatMessage.from_function(content=json.dumps(function_response), name=function_name))
response = chat_generator.run(messages=messages)
response_msg = response["replies"][0]

print(response_msg.content)
```

```py
---------- Response ----------
For the location of the coffee shop within the hotel, I recommend asking the hotel staff directly. They will be able to guide you to it accurately.
```

我们现在已经完成了聊天循环！

## 步骤4：转变为互动聊天

上面的代码展示了如何进行函数调用，但我们想更进一步，将其转化为互动聊天

这里我展示了两种方法：从更原始的`input()`，它将对话打印到笔记本本身，到通过**Streamlit**渲染它，提供类似ChatGPT的UI

`**input()**` **循环**

该代码来自[Haystack的教程](https://haystack.deepset.ai/tutorials/40_building_chat_application_with_function_calling)，使我们能够快速测试模型。*注意：此应用程序是为了展示函数调用的概念，而非旨在做到完美稳健，例如不支持同时处理多个项目的顺序、没有幻想等。*

```py
import json
from haystack.dataclasses import ChatMessage, ChatRole

response = None
messages = [
    ChatMessage.from_system(context)
]

while True:
    # if OpenAI response is a tool call
    if response and response["replies"][0].meta["finish_reason"] == "tool_calls":
        function_calls = json.loads(response["replies"][0].content)

        for function_call in function_calls:
            ## Parse function calling information
            function_name = function_call["function"]["name"]
            function_args = json.loads(function_call["function"]["arguments"])

            ## Find the correspoding function and call it with the given arguments
            function_to_call = available_functions[function_name]
            function_response = function_to_call(**function_args)

            ## Append function response to the messages list using `ChatMessage.from_function`
            messages.append(ChatMessage.from_function(content=json.dumps(function_response), name=function_name))

    # Regular Conversation
    else:
        # Append assistant messages to the messages list
        if not messages[-1].is_from(ChatRole.SYSTEM):
            messages.append(response["replies"][0])

        user_input = input("ENTER YOUR MESSAGE 👇 INFO: Type 'exit' or 'quit' to stop\n")
        if user_input.lower() == "exit" or user_input.lower() == "quit":
            break
        else:
            messages.append(ChatMessage.from_user(user_input))

    response = chat_generator.run(messages=messages, generation_kwargs={"tools": tools})
```

![](../Images/a3322060f0f01905204135a0cec63711.png)

在IDE中运行互动聊天

虽然它可以工作，但我们可能希望有一些看起来更漂亮的东西。

**Streamlit界面**

Streamlit将数据脚本转化为可分享的Web应用，这为我们的应用提供了整洁的UI。上面展示的代码被改编成一个Streamlit应用，位于我仓库的`streamlit`文件夹下。

你可以通过以下方式运行：

1.  如果你还没有做，使用`python db_api.py`启动API服务器

1.  将OPENROUTER_API_KEY设置为环境变量，例如`export OPENROUTER_API_KEY = ‘@REPLACE WITH YOUR API KEY’`，假设你在Linux上或使用git bash执行

1.  在终端中使用`cd streamlit`命令导航到`streamlit`文件夹

1.  通过`streamlit run app.py`运行Streamlit。你的浏览器中应该会自动创建一个新标签页来运行该应用程序。

就是这样！希望你喜欢这篇文章。

![](../Images/d47ce72177773e21a95ff14e3e8e5b1d.png)

Streamlit 用户界面

**除非另有说明，所有图片均由作者提供*
