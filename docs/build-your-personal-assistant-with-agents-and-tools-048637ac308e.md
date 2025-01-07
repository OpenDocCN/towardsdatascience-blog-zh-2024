# 使用代理和工具构建你的个人助手

> 原文：[https://towardsdatascience.com/build-your-personal-assistant-with-agents-and-tools-048637ac308e?source=collection_archive---------0-----------------------#2024-11-24](https://towardsdatascience.com/build-your-personal-assistant-with-agents-and-tools-048637ac308e?source=collection_archive---------0-----------------------#2024-11-24)

## 单独的LLM无法访问外部或实时数据。了解如何通过将LLM与外部资源结合，使用LangChain代理和Gemini构建个人助手。

[](https://medium.com/@benjamin_47408?source=post_page---byline--048637ac308e--------------------------------)[![Benjamin Etienne](../Images/cad8bc2d4b900575e76b7cf9debc9eea.png)](https://medium.com/@benjamin_47408?source=post_page---byline--048637ac308e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--048637ac308e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--048637ac308e--------------------------------) [Benjamin Etienne](https://medium.com/@benjamin_47408?source=post_page---byline--048637ac308e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--048637ac308e--------------------------------)·14分钟阅读·2024年11月24日

--

## 总结：

1.  LLM的问题

1.  代理、工具和链是什么？

1.  创建一个没有工具的简单聊天

1.  向我们的聊天添加工具：谷歌式的函数调用方式

1.  向我们的聊天添加工具：Langchain方式与代理

1.  向我们的代理添加记忆

1.  创建一个包含人工验证步骤的链条

1.  使用搜索工具

## 1. LLM的问题

所以你有了你最喜欢的聊天机器人，你用它来提升工作效率。它可以翻译文本、写漂亮的邮件、讲笑话等。然后有一天，你的同事走到你面前，问道：

*“你知道美元和欧元的当前汇率吗？我在想是否应该卖掉我的欧元……”*

你向你最喜欢的聊天机器人提问，答案马上就出现：

```py
I am sorry, I cannot fulfill this request. 
I do not have access to real-time information, including financial data 
like exchange rates.
```

*这里的问题是什么？*

问题在于，你已经碰到了LLM的一个短板。大型语言模型（LLM）在解决许多类型的问题时非常强大，例如问题解决、文本摘要、生成等。

然而，它们受到以下限制的约束：

+   **它们在训练后被“冻结”，导致知识过时。**

+   **它们无法查询或修改外部数据。**

就像我们每天使用搜索引擎、阅读书籍和文档或查询数据库一样，我们理想中希望将这些知识提供给我们的LLM，以提高其效率。

幸运的是，有一种方法可以做到：工具和代理。

> 尽管基础模型在文本和图像生成方面令人印象深刻，但它们仍然受到无法与外部世界交互的限制。工具弥补了这一鸿沟，使智能体能够与外部数据和服务交互，同时解锁比单独的基础模型更多的操作。

*(来源：谷歌智能体白皮书)*

使用智能体和工具，我们可以通过聊天界面：

+   从我们自己的文档中检索数据

+   阅读/发送电子邮件

+   与内部数据库进行交互

+   执行实时谷歌搜索

+   等等。

## 2\. 什么是智能体、工具和链条？

*智能体*是一个应用程序，它试图通过拥有一组工具并根据对环境的观察做出决策来实现一个目标（或任务）。

一个好的智能体例子可以是你自己：如果你需要计算一个复杂的数学操作（目标），你可以使用计算器（工具#1）或编程语言（工具#2）。也许你会选择计算器来进行简单的加法，但对于更复杂的算法，你可能会选择工具#2。

因此，智能体由以下部分构成：

+   模型：我们智能体的大脑是大语言模型（LLM）。它将理解查询（目标），并浏览其可用工具以选择最佳工具。

+   一个或多个*工具*：这些是函数或API，负责执行特定的操作（例如：检索美元与欧元的当前汇率、加法等）。

+   一个协调过程：这就是模型在被要求解决任务时的行为方式。它是一个认知过程，定义了模型如何分析问题、完善输入、选择工具等。此类过程的示例有ReAct、CoT（思维链）、ToT（思维树）

下面是一个工作流说明

![](../Images/85a293f46127b027cf0d29236c2e7d04.png)

图片由作者提供

*链条*有些不同。尽管智能体可以“自行决定”做什么以及采取哪些步骤，但链条仅是预定义步骤的序列。它们仍然可以依赖工具，这意味着它们可以包括一个需要从可用工具中选择的步骤。稍后我们将详细讨论。

## 3\. 创建一个没有工具的简单聊天

为了说明我们的观点，我们将首先看到没有任何帮助的情况下，我们的LLM如何表现。

让我们安装所需的库：

```py
vertexai==1.65.0
langchain==0.2.16
langchain-community==0.2.16
langchain-core==0.2.38
langchain-google-community==1.0.8
langchain-google-vertexai==1.0.6
```

并使用谷歌的Gemini大语言模型创建我们非常简单的聊天：

```py
from vertexai.generative_models import (
    GenerativeModel,
    GenerationConfig,
    Part
)

gemini_model = GenerativeModel(
    "gemini-1.5-flash",
    generation_config=GenerationConfig(temperature=0),
)
chat = gemini_model.start_chat()
```

如果你运行这个简单的聊天并询问当前的汇率问题，你可能会得到类似的答案：

```py
response = chat.send_message("What is the current exchange rate for USD vs EUR ?")
answer = response.candidates[0].content.parts[0].text

--- OUTPUT ---
"I am sorry, I cannot fulfill this request. I do not have access to real-time information, including financial data like exchange rates." 
```

并不奇怪，因为我们知道大语言模型（LLMs）无法访问实时数据。

让我们为此添加一个工具。我们的工具将是一个调用API以实时检索汇率数据的小功能。

```py
def get_exchange_rate_from_api(params):
    url = f"https://api.frankfurter.app/latest?from={params['currency_from']}&to={params['currency_to']}"
    print(url)
    api_response = requests.get(url)
    return api_response.text

# Try it out !
get_exchange_rate_from_api({'currency_from': 'USD', 'currency_to': 'EUR'})
---
'{"amount":1.0,"base":"USD","date":"2024-11-20","rates":{"EUR":0.94679}}'
```

现在我们知道了工具的工作原理，我们希望告诉我们的聊天LLM使用这个功能来回答问题。因此，我们将创建一个单工具智能体。为此，我们有几个选项，我将在此列出：

+   使用谷歌的Gemini聊天API与功能调用

+   使用LangChain的API与智能体和工具

两者各有优缺点。本文的目的是展示这些可能性，让您决定更喜欢哪一种方式。

## 4\. 向我们的聊天添加工具：使用Google的函数调用方式

创建工具的基本方法有两种。

第一个是“字典”方法，在这种方法中，您指定函数的输入和描述。重要的参数是：

+   函数的名称（请明确）

+   描述：在这里要详细说明，因为一个扎实且详尽的描述将帮助LLM选择正确的工具

+   参数：这是您指定参数的位置（类型和描述）。同样，请在描述参数时进行详细说明，帮助LLM了解如何将值传递给您的函数

```py
import requests

from vertexai.generative_models import FunctionDeclaration

get_exchange_rate_func = FunctionDeclaration(
    name="get_exchange_rate",
    description="Get the exchange rate for currencies between countries",
    parameters={
    "type": "object",
    "properties": {
        "currency_from": {
            "type": "string",
            "description": "The currency to convert from in ISO 4217 format"
        },
        "currency_to": {
            "type": "string",
            "description": "The currency to convert to in ISO 4217 format"
        }
    },
        "required": [
            "currency_from",
            "currency_to",
      ]
  },
)
```

使用Google SDK添加工具的第二种方式是通过`from_func`实例化。这需要我们修改原始函数，使其更加明确，并添加文档字符串等内容。与工具创建时冗长不同，我们是在函数创建时进行详细描述。

```py
# Edit our function
def get_exchange_rate_from_api(currency_from: str, currency_to: str):
    """
    Get the exchange rate for currencies   

    Args:
        currency_from (str): The currency to convert from in ISO 4217 format
        currency_to (str): The currency to convert to in ISO 4217 format
    """
    url = f"https://api.frankfurter.app/latest?from={currency_from}&to={currency_to}"
    api_response = requests.get(url)
    return api_response.text

# Create the tool
get_exchange_rate_func = FunctionDeclaration.from_func(
  get_exchange_rate_from_api
)
```

下一步实际上是创建工具。为此，我们将把我们的FunctionDeclaration添加到列表中，以创建我们的Tool对象：

```py
from vertexai.generative_models import Tool as VertexTool

tool = VertexTool(
    function_declarations=[
        get_exchange_rate_func,
        # add more functions here !
    ]
)
```

现在让我们将其传递给我们的聊天，看看它是否能够回答我们关于汇率的查询！记住，如果没有工具，我们的聊天回答是：

![](../Images/8206400b049669a30d50ea74965d1d7a.png)

让我们尝试Google的函数调用工具，看看这是否有帮助！首先，让我们将查询发送给聊天：

```py
from vertexai.generative_models import GenerativeModel

gemini_model = GenerativeModel(
    "gemini-1.5-flash",
    generation_config=GenerationConfig(temperature=0),
    tools=[tool] #We add the tool here !
)
chat = gemini_model.start_chat()

response = chat.send_message(prompt)

# Extract the function call response
response.candidates[0].content.parts[0].function_call

--- OUTPUT ---
"""
name: "get_exchange_rate"
args {
  fields {
    key: "currency_to"
    value {
      string_value: "EUR"
    }
  }
  fields {
    key: "currency_from"
    value {
      string_value: "USD"
    }
  }
  fields {
    key: "currency_date"
    value {
      string_value: "latest"
    }
  }
}""" 
```

LLM正确猜测它需要使用`get_exchange_rate`函数，并且也正确猜测两个参数是`USD`和`EUR`。

但这还不够。我们现在想要的是实际运行这个函数以获得我们的结果！

```py
# mapping dictionnary to map function names and function
function_handler = {
    "get_exchange_rate": get_exchange_rate_from_api,
}

# Extract the function call name
function_name = function_call.name
print("#### Predicted function name")
print(function_name, "\n")

# Extract the function call parameters
params = {key: value for key, value in function_call.args.items()}
print("#### Predicted function parameters")
print(params, "\n")

function_api_response = function_handler[function_name](params)
print("#### API response")
print(function_api_response)
response = chat.send_message(
    Part.from_function_response(
        name=function_name,
        response={"content": function_api_response},
    ),
)   
print("\n#### Final Answer")
print(response.candidates[0].content.parts[0].text)

--- OUTPUT ---
"""
#### Predicted function name
get_exchange_rate 

#### Predicted function parameters
{'currency_from': 'USD', 'currency_date': 'latest', 'currency_to': 'EUR'} 

#### API response
{"amount":1.0,"base":"USD","date":"2024-11-20","rates":{"EUR":0.94679}}

#### Final Answer
The current exchange rate for USD vs EUR is 0.94679\. This means that 1 USD is equal to 0.94679 EUR. 
"""
```

我们现在可以看到我们的聊天能够回答问题了！它：

+   正确猜测调用的功能是`get_exchange_rate`

+   正确分配了调用函数的参数`{'currency_from': 'USD', 'currency_to': 'EUR'}`

+   从API获取结果

+   并且格式化答案，使其易于人类阅读！

现在让我们看看另一种使用LangChain的方法。

## 5\. 向我们的聊天添加工具：使用Langchain的代理方式

LangChain是一个可组合的框架，用于与LLM一起构建。它是用于可控代理工作流的编排框架。

与我们之前采用的“Google”方式类似，我们将使用Langchain的方式构建工具。让我们从定义函数开始。与Google一样，我们需要在文档字符串中详尽且冗长地描述：

```py
from langchain_core.tools import tool

@tool
def get_exchange_rate_from_api(currency_from: str, currency_to: str) -> str:
    """
    Return the exchange rate between currencies
    Args:
        currency_from: str
        currency_to: str
    """
    url = f"https://api.frankfurter.app/latest?from={currency_from}&to={currency_to}"
    api_response = requests.get(url)
    return api_response.text
```

为了让事情更加有趣，我将添加另一个可以列出BigQuery数据集中的表的工具。以下是代码：

```py
@tool
def list_tables(project: str, dataset_id: str) -> list:
    """
    Return a list of Bigquery tables
    Args:
        project: GCP project id
        dataset_id: ID of the dataset
    """
    client = bigquery.Client(project=project)
    try:
        response = client.list_tables(dataset_id)
        return [table.table_id for table in response]
    except Exception as e:
        return f"The dataset {params['dataset_id']} is not found in the {params['project']} project, please specify the dataset and project"
```

一旦完成，我们将我们的函数添加到LangChain工具箱中！

```py
langchain_tool = [
    list_tables,
    get_exchange_rate_from_api
]
```

为了构建我们的代理，我们将使用LangChain的`AgentExecutor`对象。这个对象基本上将使用之前定义的三个组件：

+   LLM

+   一个提示

+   以及工具。

让我们首先选择我们的LLM：

```py
gemini_llm = ChatVertexAI(model="gemini-1.5-flash")
```

然后我们创建一个提示来管理对话：

```py
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a helpful assistant"),
        ("human", "{input}"),
        # Placeholders fill up a **list** of messages
        ("placeholder", "{agent_scratchpad}"),
    ]
)
```

最后，我们创建`AgentExecutor`并运行查询：

```py
agent = create_tool_calling_agent(gemini_llm, langchain_tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=langchain_tools)
agent_executor.invoke({
    "input": "Which tables are available in the thelook_ecommerce dataset ?"
})

--- OUTPUT ---
"""
{'input': 'Which tables are available in the thelook_ecommerce dataset ?',
 'output': 'The dataset `thelook_ecommerce` is not found in the `gcp-project-id` project. 
            Please specify the correct dataset and project. \n'}
""" 
```

嗯，看起来代理缺少一个参数，或者至少需要更多的信息……我们来回应并提供这个信息：

```py
agent_executor.invoke({"input": f"Project id is bigquery-public-data"})

--- OUPTUT ---
"""
{'input': 'Project id is bigquery-public-data',
 'output': 'OK. What else can I do for you? \n'}
""" 
```

好像我们又回到了原点。虽然已经告诉了LLM项目ID，但它忘记了问题。我们的代理似乎缺乏记忆，无法记住之前的问题和答案。也许我们应该考虑……

## 6\. 为我们的代理添加记忆

记忆是代理中的另一个概念，它基本上帮助系统记住对话历史，避免像上述情况那样的无休止循环。可以把记忆看作是一个记事本，LLM用它来跟踪之前的问题和答案，从而构建对话的上下文。

我们将修改我们的提示（指令），让模型包含记忆：

```py
from langchain_core.chat_history import InMemoryChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

# Different types of memory can be found in Langchain
memory = InMemoryChatMessageHistory(session_id="foo")

prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a helpful assistant."),
        # First put the history
        ("placeholder", "{chat_history}"),
        # Then the new input
        ("human", "{input}"),
        # Finally the scratchpad
        ("placeholder", "{agent_scratchpad}"),
    ]
)

# Remains unchanged
agent = create_tool_calling_agent(gemini_llm, langchain_tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=langchain_tools)

# We add the memory part and the chat history
agent_with_chat_history = RunnableWithMessageHistory(
    agent_executor,
    lambda session_id: memory, #<-- NEW
    input_messages_key="input", 
    history_messages_key="chat_history", #<-- NEW
)

config = {"configurable": {"session_id": "foo"}}
```

我们现在将从头重新运行我们的查询：

```py
agent_with_chat_history.invoke({
    "input": "Which tables are available in the thelook_ecommerce dataset ?"
    }, 
    config
)

--- OUTPUT ---
"""
{'input': 'Which tables are available in the thelook_ecommerce dataset ?',
 'chat_history': [],
 'output': 'The dataset `thelook_ecommerce` is not found in the `gcp-project-id` project. Please specify the correct dataset and project. \n'}
"""
```

在没有聊天历史的情况下，模型仍然要求提供项目ID。这与之前没有记忆的代理行为一致。我们来回应代理并补充缺失的信息：

```py
reply = "Project id is bigquery-public-data"
agent_with_chat_history.invoke({"input": reply}, config)

--- OUTPUT ---
"""
{'input': 'Project id is bigquery-public-data',
 'chat_history': [HumanMessage(content='Which tables are available in the thelook_ecommerce dataset ?'),
  AIMessage(content='The dataset `thelook_ecommerce` is not found in the `gcp-project-id` project. Please specify the correct dataset and project. \n')],
 'output': 'The following tables are available in the `thelook_ecommerce` dataset:\n- distribution_centers\n- events\n- inventory_items\n- order_items\n- orders\n- products\n- users \n'}
"""
```

请注意，在输出中：

+   `聊天历史`跟踪之前的问答

+   输出现在返回了表格列表！

```py
'output': 'The following tables are available in the `thelook_ecommerce` dataset:\n- distribution_centers\n- events\n- inventory_items\n- order_items\n- orders\n- products\n- users \n'}
```

然而，在某些使用场景中，某些操作可能因其性质需要特别关注（例如删除数据库中的条目、编辑信息、发送电子邮件等）。没有控制的完全自动化可能导致代理做出错误决定并造成损害。

确保我们的工作流的一种方法是添加一个人工干预步骤。

## 7\. 创建一个包含人工验证步骤的链

链与代理有所不同。代理可以决定是否使用工具，而链则更为静态。它是一个步骤序列，我们仍然可以在其中加入一个步骤，让LLM从一组工具中选择。

在LangChain中构建链时，我们使用LCEL。

LangChain表达式语言（LCEL）是一种声明式的方法，可以轻松地将链式操作组合在一起。在LangChain中，链使用管道符号`|`表示步骤的执行顺序，如`step 1 | step 2 | step 3 等`。与代理的不同之处在于，链始终会按照这些步骤执行，而代理可以“自行决定”并在决策过程中具有自主性。

在我们的例子中，我们将按照以下方式构建一个简单的`prompt | llm`链。

```py
# define the prompt with memory
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a helpful assistant."),
        # First put the history
        ("placeholder", "{chat_history}"),
        # Then the new input
        ("human", "{input}"),
        # Finally the scratchpad
        ("placeholder", "{agent_scratchpad}"),
    ]
)

# bind the tools to the LLM
gemini_with_tools = gemini_llm.bind_tools(langchain_tool)

# build the chain
chain = prompt | gemini_with_tools
```

还记得在前一个步骤中，我们将一个代理传递给了我们的`RunnableWithMessageHistory`吗？好吧，我们将在这里做同样的事情，但……

```py
# With AgentExecutor

# agent = create_tool_calling_agent(gemini_llm, langchain_tool, prompt)
# agent_executor = AgentExecutor(agent=agent, tools=langchain_tool)

# agent_with_chat_history = RunnableWithMessageHistory(
#     agent_executor,
#     lambda session_id: memory,
#     input_messages_key="input",
#     history_messages_key="chat_history",
# )

config = {"configurable": {"session_id": "foo"}}

# With Chains
memory = InMemoryChatMessageHistory(session_id="foo")
chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: memory,
    input_messages_key="input",
    history_messages_key="chat_history",
)

response = chain_with_history.invoke(
    {"input": "What is the current CHF EUR exchange rate ?"}, config)

--- OUTPUT
"""
content='', 
additional_kwargs={
    'function_call': {
        'name': 'get_exchange_rate_from_api', 
        'arguments': '{"currency_from": "CHF", "currency_to": "EUR"}'
    }
}
""" 
```

与代理不同，链在没有明确告诉它的情况下不会提供答案。在我们的例子中，它停留在LLM返回需要调用的功能的步骤上。

我们需要添加一个额外的步骤来实际*调用*工具。让我们添加另一个函数来调用这些工具：

```py
from langchain_core.messages import AIMessage

def call_tools(msg: AIMessage) -> list[dict]:
    """Simple sequential tool calling helper."""
    tool_map = {tool.name: tool for tool in langchain_tool}
    tool_calls = msg.tool_calls.copy()
    for tool_call in tool_calls:
        tool_call["output"] = tool_map[tool_call["name"]].invoke(tool_call["args"])
    return tool_calls

chain = prompt | gemini_with_tools | call_tools #<-- Extra step

chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: memory,
    input_messages_key="input",
    history_messages_key="chat_history",
)

# Rerun the chain 
chain_with_history.invoke({"input": "What is the current CHF EUR exchange rate ?"}, config)
```

现在我们得到以下输出，显示API已成功调用：

```py
[{'name': 'get_exchange_rate_from_api',
  'args': {'currency_from': 'CHF', 'currency_to': 'EUR'},
  'id': '81bc85ea-dfd4-4c01-85e8-f3ca592fff5b',
  'type': 'tool_call',
  'output': '{"amount":1.0,"base":"USD","date":"2024-11-20","rates":{"EUR":0.94679}}'
}]
```

现在我们已经理解了如何链式连接步骤，接下来让我们加入“人类在环”步骤！我们希望这个步骤检查 LLM 是否已经理解我们的请求，并将正确地调用 API。如果 LLM 理解错了请求或将错误地使用该功能，我们可以决定中断这个过程。

```py
def human_approval(msg: AIMessage) -> AIMessage:
    """Responsible for passing through its input or raising an exception.

    Args:
        msg: output from the chat model

    Returns:
        msg: original output from the msg
    """
    for tool_call in msg.tool_calls:
        print(f"I want to use function [{tool_call.get('name')}] with the following parameters :")
        for k,v in tool_call.get('args').items():
            print(" {} = {}".format(k, v))

    print("")
    input_msg = (
        f"Do you approve (Y|y)?\n\n"
        ">>>"
    )
    resp = input(input_msg)
    if resp.lower() not in ("yes", "y"):
        raise NotApproved(f"Tool invocations not approved:\n\n{tool_strs}")
    return msg
```

接下来，在函数调用之前将此步骤添加到链中：

```py
chain = prompt | gemini_with_tools | human_approval | call_tools

memory = InMemoryChatMessageHistory(session_id="foo")

chain_with_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: memory,
    input_messages_key="input",
    history_messages_key="chat_history",
)

chain_with_history.invoke({"input": "What is the current CHF EUR exchange rate ?"}, config)
```

然后你将被要求确认 LLM 是否正确理解：

![](../Images/5028f700edfcffd815b18eeb32e9da41.png)

这个“人类在环”步骤对于关键工作流非常有帮助，因为 LLM 的误解可能会造成严重后果。

## 8\. 使用搜索工具

获取实时信息的最便捷工具之一是搜索引擎。实现这一点的一种方法是使用 `GoogleSerperAPIWrapper`（你需要注册并获取 API 密钥才能使用它），它提供了一个良好的界面，可以快速查询 Google 搜索并获取结果。

幸运的是，LangChain 已经为你提供了一个工具，所以我们不需要自己编写这个函数。

因此，让我们尝试问一个关于昨天事件（11 月 20 日）的问题，看看我们的代理是否能回答。我们的问题是关于拉斐尔·纳达尔的最后一场正式比赛（他输给了范·德·赞斯丘普）。

```py
agent_with_chat_history.invoke(
    {"input": "What was the result of Rafael Nadal's latest game ?"}, config)

--- OUTPUT ---
"""
{'input': "What was the result of Rafael Nadal's latest game ?",
 'chat_history': [],
 'output': "I do not have access to real-time information, including sports results. To get the latest information on Rafael Nadal's game, I recommend checking a reliable sports website or news source. \n"}
"""
```

如果无法访问 Google 搜索，我们的模型将无法回答，因为这些信息在训练时并不可用。

现在让我们将 Serper 工具加入我们的工具箱，看看我们的模型能否使用 Google 搜索来找到信息：

```py
from langchain_community.utilities import GoogleSerperAPIWrapper

# Create our new search tool here
search = GoogleSerperAPIWrapper(serper_api_key="...")

@tool
def google_search(query: str):
    """
    Perform a search on Google
    Args:
        query: the information to be retrieved with google search
    """
    return search.run(query)

# Add it to our existing tools
langchain_tool = [
    list_datasets,
    list_tables,
    get_exchange_rate_from_api,
    google_search
]

# Create agent
agent = create_tool_calling_agent(gemini_llm, langchain_tool, prompt)
agent_executor = AgentExecutor(agent=agent, tools=langchain_tool)

# Add memory
memory = InMemoryChatMessageHistory()
agent_with_chat_history = RunnableWithMessageHistory(
    agent_executor,
    lambda session_id: memory,
    input_messages_key="input",
    history_messages_key="chat_history",
)
```

然后重新运行我们的查询：

```py
agent_with_chat_history.invoke({"input": "What was the result of Rafael Nadal's latest game ?"}, config)

--- OUTPUT ---
"""
{'input': "What was the result of Rafael Nadal's latest game ?",
 'chat_history': [],
 'output': "Rafael Nadal's last match was a loss to Botic van de Zandschulp in the Davis Cup. Spain was eliminated by the Netherlands. \n"}
""" 
```

## 结论

仅依靠 LLM，当涉及到使用个人、公司、私密或真实数据时，通常会遇到阻碍。事实上，这类信息在训练时通常不可用。代理和工具是增强这些模型的强大手段，它们允许模型与系统和 API 交互，并协调工作流以提升生产力。
