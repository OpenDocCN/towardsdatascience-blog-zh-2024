# 在LangChain中使用工具和工具包构建简单代理

> 原文：[https://towardsdatascience.com/building-a-simple-agent-with-tools-and-toolkits-in-langchain-77e0f9bd1fa5?source=collection_archive---------1-----------------------#2024-04-10](https://towardsdatascience.com/building-a-simple-agent-with-tools-and-toolkits-in-langchain-77e0f9bd1fa5?source=collection_archive---------1-----------------------#2024-04-10)

## 熟悉LangChain中代理的构建模块

[](https://medium.com/@ssmaameri?source=post_page---byline--77e0f9bd1fa5--------------------------------)[![Sami Maameri](../Images/9e9892fe7d3cc53ad1c4d165145878ef.png)](https://medium.com/@ssmaameri?source=post_page---byline--77e0f9bd1fa5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--77e0f9bd1fa5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--77e0f9bd1fa5--------------------------------) [Sami Maameri](https://medium.com/@ssmaameri?source=post_page---byline--77e0f9bd1fa5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--77e0f9bd1fa5--------------------------------) ·13分钟阅读·2024年4月10日

--

![](../Images/c890aaf761bb571b28a0b60b25e8fda6.png)

图片由[Dan LeFebvre](https://unsplash.com/@danlefeb?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

让我们在LangChain中构建一个简单的代理，帮助我们理解一些基础概念以及代理如何工作的构建模块。

通过保持简单，我们可以更好地理解这些代理背后的基础理念，从而在未来构建更复杂的代理。

```py
**Contents**

[What are Agents?](#417f)

[Building the Agent](#6fc2)
- [The Tools](#8705)
- [The Toolkit](#6004)
- [The LLM](#1c1d)
- [The Prompt](#0728)

[The Agent](#159c)

[Testing our Agent](#0844)

[Observations](#fef3)

[The Future](#daa9)

[Conclusion](#c1a5)
```

想要获得我未来文章的通知吗？[*在此订阅*](https://medium.com/@ssmaameri/subscribe)

# 什么是代理

LangChain文档实际上有一个相当[好的页面](https://python.langchain.com/docs/modules/agents/)介绍了关于代理的高层次概念。它简短易懂，在开始之前浏览一下肯定值得。

如果你查找人工智能代理的定义，你会看到类似于“一个能够感知其环境、对环境采取行动、做出智能决策以达到目标，并且具有边走边学习能力的实体”的描述。

我认为这完全符合LangChain代理的定义。让这一切在软件中成为可能的是大型语言模型（LLM）的推理能力。LangChain代理的大脑是LLM。正是LLM被用来推理执行用户请求的最佳方法。

为了执行任务并操作事物以及获取信息，代理在LangChain中有一些叫做工具（Tool）的东西可供使用。正是通过这些工具，它能够与环境进行交互。

这些工具基本上就是代理可以访问的方法/类，它们可以做一些事情，比如通过API与股票市场指数交互、更新Google日历事件或对数据库执行查询。我们可以根据需要构建工具，这取决于我们试图通过代理完成的任务的性质。

在LangChain中，工具的集合称为Toolkit。在实现上，这实际上只是一个包含可供代理使用的工具的数组。因此，LangChain中代理的高级概述大致如下所示

![](../Images/34df972c8faa9ba0de48bb3d01d91eb9.png)

图片由作者提供

所以，从基本的角度来看，一个代理需要

+   一个LLM作为它的大脑，并赋予它推理能力

+   工具，以便它能够与周围的环境进行交互，并实现其目标

# 构建代理

为了让这些概念更加具体，我们来构建一个简单的代理。

我们将创建一个数学代理，它可以执行一些简单的数学运算。

## 环境设置

首先，让我们设置我们的环境和脚本

```py
mkdir simple-math-agent && cd simple-math-agent
touch math-agent.py
python3 -m venv .venv
. .venv/bin/activate
```

```py
pip install langchain langchain_openai
```

另外，你也可以克隆这里使用的代码[从GitHub](https://github.com/smaameri/simple-math-agent)

```py
git clone git@github.com:smaameri/simple-math-agent.git
```

或者也可以查看[Google Colab](https://colab.research.google.com/drive/1dHG7d4Iq0yuDEOWK1UaMZ7ADeTo0uAFT?usp=sharing)中的代码。

# 工具

最简单的开始方式是首先为我们的数学代理定义工具。

让我们为它提供“add”、“multiply”和“square”工具，以便它能够对我们传递给它的问题执行这些操作。通过保持工具简单，我们可以专注于核心概念，自己构建工具，而不是依赖像WikipediaTool这样更复杂的现成工具，它作为Wikipedia API的包装器，需要我们从LangChain库中导入。

再次强调，我们并不打算做什么复杂的事情，只是保持简单，将代理的主要构建模块拼凑在一起，以便我们能够理解它们是如何工作的，并让我们的第一个代理启动并运行。

我们从“add”工具开始。在LangChain中创建工具的自底向上的方式是扩展**BaseTool**类，设置类中的**name**和**description**字段，并实现**_run**方法。代码如下所示

```py
from langchain_core.tools import BaseTool

class AddTool(BaseTool):
    name = "add"
    description = "Adds two numbers together"
    args_schema: Type[BaseModel] = AddInput
    return_direct: bool = True

    def _run(
        self, a: int, b: int, run_manager: Optional[CallbackManagerForToolRun] = None
    ) -> str:
        return a + b
```

注意，我们需要实现**_run**方法，以显示我们的工具如何处理传递给它的参数。

还要注意它需要一个pydantic模型作为**args_schema**。我们将在这里定义它。

```py
AddInput
    a: int = Field(description="first number")
    b: int = Field(description="second number")
```

现在，LangChain确实为我们提供了一种更简便的方式来定义工具，而不需要每次都扩展**BaseTool**类。我们可以借助**@tool**装饰器来做到这一点。使用@tool装饰器在LangChain中定义“add”工具的代码如下所示

```py
from langchain.tools import tool

@tool
def add(a: int, b: int) -> int:
 “””Adds two numbers together””” # this docstring gets used as the description
 return a + b # the actions our tool performs
```

更简单对吧。幕后，装饰器神奇地使用了提供的方法来扩展 BaseTool 类，就像我们之前做的那样。有一点需要注意：

+   方法名称也成为工具名称

+   方法参数定义了工具的输入参数

+   文档字符串被转换为工具的描述

你也可以在工具上访问这些属性

```py
print(add.name) # add
print(add.description) # Adds two numbers together.
print(add.args) # {'a': {'title': 'A', 'type': 'integer'}, 'b': {'title': 'B', 'type': 'integer'}}
```

请注意，工具的描述非常重要，因为 LLM 会根据这个描述来决定是否选择该工具。如果描述不准确，可能导致工具在该使用时没有被使用，或者在错误的时机被使用。

完成 **add** 工具后，让我们继续定义 **multiply** 和 **square** 工具。

```py
@tool
def multiply(a: int, b: int) -> int:
    """Multiply two numbers."""
    return a * b

@tool
def square(a) -> int:
    """Calculates the square of a number."""
    a = int(a)
    return a * a
```

就是这样，简单如斯。

所以我们定义了自己的三个 [自定义工具](https://python.langchain.com/docs/modules/tools/custom_tools/)。一个更常见的用例可能是使用 LangChain 中已提供和存在的一些工具，你可以在 [这里](https://python.langchain.com/docs/integrations/tools/) 查看。不过，在源代码层面，它们都会使用类似上述描述的方法来构建和定义。

到这里为止，我们的工具就算完成了。现在是时候将工具组合成一个工具包了。

# 工具包

工具包听起来很复杂，但其实它们非常简单。它们**实际上**就是一组工具的列表。我们可以像这样定义我们的工具包，作为一个工具数组：

```py
toolkit = [add, multiply, square]
```

就这样。非常简单，没有什么好混淆的。

通常，工具包是一些可以一起使用的工具组，对于那些试图执行特定任务的智能体来说非常有帮助。例如，SQLToolkit 可能包含用于生成 SQL 查询、验证 SQL 查询和执行 SQL 查询的工具。

LangChain 文档中的 [集成工具包](https://python.langchain.com/docs/integrations/toolkits/) 页面列出了由社区开发的许多工具包，可能对你有用。

# LLM

如上所述，LLM 是智能体的大脑。它根据传入的问题决定调用哪些工具，根据工具的描述决定下一步应该采取哪些最佳步骤。它还决定何时达到最终答案，并准备将其返回给用户。

在这里设置 LLM

```py
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-3.5-turbo-1106", temperature=0)
```

# 提示词

最后，我们需要一个提示词传递给我们的智能体，这样它就能大致了解它是什么类型的智能体，以及应该解决哪些任务。

我们的智能体需要一个 ChatPromptTemplate 来工作（稍后会详细介绍）。这是一个基本的 ChatPromptTemplate，主要关注部分是系统提示，其他则是我们需要传递的默认设置。

在我们的提示词中，我们包含了一个示例答案，向智能体展示我们希望它只返回答案，而不附带任何描述性文字。

```py
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", """
          You are a mathematical assistant. Use your tools to answer questions.
          If you do not have a tool to answer the question, say so. 

          Return only the answers. e.g
          Human: What is 1 + 1?
          AI: 2
          """),
        MessagesPlaceholder("chat_history", optional=True),
        ("human", "{input}"),
        MessagesPlaceholder("agent_scratchpad"),
    ]
)
```

就这样。我们已经设置好了代理所需的工具和工具包，代理需要这些作为设置的一部分，以便了解它可以执行哪些类型的操作和具有哪些能力。我们也已经设置好了LLM和系统提示。

现在是有趣的部分了。设置我们的代理！

# 代理

LangChain有[多种不同的代理类型](https://python.langchain.com/docs/modules/agents/agent_types/)可以创建，这些代理具有不同的推理能力和特性。我们将使用目前最强大和最有能力的代理——[OpenAI工具](https://python.langchain.com/docs/modules/agents/agent_types/openai_tools/)代理。根据OpenAI工具代理的文档，它也使用了更新的OpenAI模型。

> 更新后的OpenAI模型已经经过微调，以便能够检测何时应该调用一个或多个函数，并响应应传递给函数的输入。在API调用中，你可以描述函数，并让模型智能地选择输出一个包含调用这些函数所需参数的JSON对象。OpenAI工具API的目标是比使用通用的文本完成或聊天API更可靠地返回有效且有用的函数调用。

换句话说，这个代理擅长生成正确的结构来调用函数，并能够理解我们的任务是否需要多个函数（工具）。这个代理还能够调用具有多个输入参数的函数（工具），就像我们的代理一样。有些代理只能处理具有单一输入参数的函数。

如果你熟悉[OpenAI的函数调用功能](https://platform.openai.com/docs/guides/function-calling)，在该功能中，我们可以使用OpenAI的LLM来生成正确的参数，以便调用函数，那么我们在这里使用的OpenAI工具代理也在利用其中的一部分功能，能够以正确的参数调用正确的工具。

为了在LangChain中设置代理，我们需要使用提供的工厂方法来创建我们选择的代理。

创建OpenAI工具代理的工厂方法是**create_openai_tools_agent()**。它需要传入我们上面设置的LLM、工具和提示。因此，让我们初始化我们的代理。

```py
agent = create_openai_tools_agent(llm, toolkit, prompt)
```

最后，为了在LangChain中运行代理，我们不能直接调用“*run*”类型的方法。它们需要通过AgentExecutor来运行。

我之所以在最后提到Agent Executor，是因为我认为它不是理解代理工作原理的关键概念，放在开始时与其他内容一起讲解只会让整个过程看起来比实际需要的更复杂，而且还可能分散对一些更基本概念的理解。

所以，现在我们介绍一下，AgentExecutor作为LangChain中代理的运行时，允许代理一直运行，直到准备好返回最终的响应给用户。在伪代码中，AgentExecutor的工作原理大致如下（直接摘自[LangChain文档](https://python.langchain.com/docs/modules/agents/concepts/#agentexecutor)）

```py
next_action = agent.get_action(...)
while next_action != AgentFinish:
    observation = run(next_action)
    next_action = agent.get_action(..., next_action, observation)
return next_action
```

所以它们基本上是一个while循环，持续调用代理的下一个操作方法，直到代理返回最终响应。

所以，让我们将代理设置在代理执行器内。我们传递给它代理，并且还必须传递工具包。我们将“verbose”设置为True，以便在代理处理我们的请求时了解它在做什么。

```py
agent_executor = AgentExecutor(agent=agent, tools=toolkit, verbose=True)
```

就这些了。我们现在准备向代理传递命令。

```py
result = agent_executor.invoke({"input": "what is 1 + 1"})
```

让我们运行我们的脚本，看看代理的输出。

```py
python3 math-agent.py
```

![](../Images/38082f690ae8ca1409e8cac5d2270f77.png)

图片来自作者

由于我们在AgentExecutor上设置了**verbose=True**，我们可以看到代理所采取的每个操作步骤。它已经识别出我们应该调用“**加法**”工具，并使用所需的参数调用了“**加法**”工具，最终返回了结果。

这就是完整的源代码

```py
import os

from langchain.agents import AgentExecutor, create_openai_tools_agent
from langchain_openai import ChatOpenAI

from langchain.tools import BaseTool, StructuredTool, tool
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

os.environ["OPENAI_API_KEY"] = "sk-"

# setup the tools
@tool
def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b

@tool
def multiply(a: int, b: int) -> int:
    """Multiply two numbers."""
    return a * b

@tool
def square(a) -> int:
    """Calculates the square of a number."""
    a = int(a)
    return a * a

prompt = ChatPromptTemplate.from_messages(
    [
        ("system", """You are a mathematical assistant.
        Use your tools to answer questions. If you do not have a tool to
        answer the question, say so. 

        Return only the answers. e.g
        Human: What is 1 + 1?
        AI: 2
        """),
        MessagesPlaceholder("chat_history", optional=True),
        ("human", "{input}"),
        MessagesPlaceholder("agent_scratchpad"),
    ]
)

# Choose the LLM that will drive the agent
llm = ChatOpenAI(model="gpt-3.5-turbo-1106", temperature=0)

# setup the toolkit
toolkit = [add, multiply, square]

# Construct the OpenAI Tools agent
agent = create_openai_tools_agent(llm, toolkit, prompt)

# Create an agent executor by passing in the agent and tools
agent_executor = AgentExecutor(agent=agent, tools=toolkit, verbose=True)

result = agent_executor.invoke({"input": "what is 1 + 1?"})

print(result['output'])
```

# 测试我们的代理

让我们向代理提几个问题，看看它的表现如何。

## **5的平方是多少？**

再次获得正确的结果，看到它确实使用了我们的**平方**工具

![](../Images/4da75f6cccd073be0ccf10095da58268.png)

图片来自作者

## 5的6次方是多少？

它采取了一个有趣的行动步骤。它首先使用**平方**工具。然后，使用该结果，尝试使用**乘法**工具多次来得到最终答案。坦率地说，最终答案3125是错误的，还需要再乘以5才能得到正确的答案。但有趣的是，看到代理尝试使用不同的工具，并通过多个步骤来尝试得到最终答案。

![](../Images/06605974f62a317700216b36626a3a9f.png)

图片来自作者

## **1减去3是多少？**

我们没有减法工具。但它足够聪明，使用我们的加法工具，并将第二个值设置为-3。有时候它们真是太聪明和富有创意了，挺有趣的，甚至令人惊讶。

![](../Images/1c4add21cd9408cce4be6aeaa9abdd53.png)

图片来自作者

## **64的平方根是多少**

作为最后的测试，如果我们要求它执行一个不在我们工具集中的数学运算会怎样？由于我们没有平方根工具，它不会尝试调用工具，而是直接使用LLM计算该值。

![](../Images/670b50b68a2de3c16cf3636692a5838c.png)

图片来自作者

我们的系统提示确实告诉它，如果没有正确的工具，它应该回答“不知道”，并且在测试过程中确实有时会这样做。一个改进的初始系统提示可能会有所帮助，至少在一定程度上能解决这个问题。

## 观察

基于对代理的使用，我注意到以下几点

+   当直接询问它具备工具可以回答的问题时，它在使用正确工具并返回正确答案方面相当一致。所以，从这个角度来看，它是相当可靠的。

+   如果问题稍微复杂一点，例如我们提到的“5 的 6 次方”问题，它并不总是返回正确的结果。

+   它有时可以仅仅使用 LLM 的强大能力来回答我们的问题，而无需调用我们的工具。

# 未来

代理和能够自主推理的程序是编程中的一种新范式，我认为它们将成为构建许多事物的主流方式。显然，LLM 的非确定性（即不是完全可预测的）意味着代理的结果也会受到影响，这使我们质疑在需要确保答案准确无误的任务中，我们能在多大程度上依赖它们。

也许随着技术的成熟，它们的结果将变得更加可预测，我们可能会为此开发一些解决方法。

我还看到代理类型的库和软件包开始成为一个趋势。类似于我们如何将第三方库和软件包安装到软件中，例如通过 Python 的 pip 包管理器或 Docker 镜像的 Docker Hub，我想我们可能会开始看到一个代理的库和包管理器开始被开发出来，代理在某一特定任务上变得非常擅长，然后我们也可以将它们作为软件包安装到我们的应用程序中。

的确，LangChain 的[工具包库](https://python.langchain.com/docs/integrations/toolkits/)列在其集成页面上，这些工具包是社区构建的工具集，供大家使用，这可能是社区构建的代理类型库的一个早期示例。

# 结论

希望这篇文章能为你提供一些有用的入门指导，帮助你开始在 LangChain 中构建代理。

记住，代理基本上就是一个大脑（即 LLM）和一堆工具，它们可以利用这些工具来完成我们周围世界中的任务。

快乐编程！

*如果你喜欢这篇文章，并希望随时了解我发布的关于使用 LangChain 和 AI 工具构建项目的未来文章，欢迎* [*在这里订阅*](https://medium.com/@ssmaameri/subscribe) *，这样当新文章发布时，你会通过电子邮件收到通知*
