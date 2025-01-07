# 使用LangGraph和LangChain创建任务导向对话系统

> 原文：[https://towardsdatascience.com/creating-task-oriented-dialog-systems-with-langgraph-and-langchain-fada6c9c4983?source=collection_archive---------2-----------------------#2024-09-14](https://towardsdatascience.com/creating-task-oriented-dialog-systems-with-langgraph-and-langchain-fada6c9c4983?source=collection_archive---------2-----------------------#2024-09-14)

## 又一篇LangGraph教程

[](https://medium.com/@mesquitadeh?source=post_page---byline--fada6c9c4983--------------------------------)[![Déborah Mesquita](../Images/3b77b7eb569e24f2679875429173daf1.png)](https://medium.com/@mesquitadeh?source=post_page---byline--fada6c9c4983--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fada6c9c4983--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fada6c9c4983--------------------------------) [Déborah Mesquita](https://medium.com/@mesquitadeh?source=post_page---byline--fada6c9c4983--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fada6c9c4983--------------------------------) ·阅读时间11分钟·2024年9月14日

--

![](../Images/df4dbf4e422ff2e27cc77246f75e6d94.png)

图片来源：[Kaleidico](https://unsplash.com/pt-br/@kaleidico?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于[Unsplash](https://unsplash.com/pt-br/fotografias/duas-pessoas-desenhando-no-quadro-branco-26MJGnCM0Wc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

任务导向对话系统（ToD）是帮助用户**完成特定任务**的系统，例如预定餐厅、规划旅行行程或订餐。

我们知道，使用提示语（prompts）来指导大型语言模型（LLMs），但我们如何实现这些任务导向对话系统（ToD），使得**对话始终围绕我们希望用户完成的任务**展开呢？一种方法是使用**提示语**、**记忆**和**工具调用**。幸运的是，LangChain + LangGraph可以帮助我们将这些要素结合起来。

在这篇文章中，您将学习如何构建一个任务导向对话系统，帮助用户以高质量创建用户故事。该系统完全基于LangGraph的[根据用户需求生成提示](https://langchain-ai.github.io/langgraph/tutorials/chatbots/information-gather-prompting/)教程。

# 为什么我们需要使用LangGraph？

在本教程中，我们假设您已经知道如何使用LangChain。用户故事包含一些组件，如目标、成功标准、执行计划和交付物。用户应提供每一个组件，而我们需要一步步地“引导”他们逐个提供。仅使用LangChain实现这一点会需要大量的条件判断语句（if/else）。

使用 LangGraph，我们可以使用图抽象来**创建循环**来控制对话。它还具有**内置持久性**，因此我们无需担心主动追踪图内发生的交互。

LangGraph 的主要抽象是 [**StateGraph**](https://langchain-ai.github.io/langgraph/reference/graphs/#stategraph)，它用于创建图工作流。每个图都需要用**state_schema** 初始化：一个每个节点使用的模式类，用于读取和写入信息。

我们系统的流程将由*LLM*和*用户*消息的轮次组成。主要循环包含以下步骤：

1.  用户说了些什么

1.  LLM 读取状态中的消息，并决定是否准备好创建用户故事，或者是否需要用户再次响应

我们的系统很简单，因此架构仅包含对话中交换的消息。

```py
from langgraph.graph.message import add_messages

class StateSchema(TypedDict):
    messages: Annotated[list, add_messages]
```

**add_messages** 方法用于将每个**节点**的输出消息合并到图中现有状态的消息列表中。

说到节点，另两个主要的 LangGraph 概念是**节点**和**边**。每个图的**节点**运行一个函数，每个**边**控制一个节点到另一个节点的流动。我们还有**START**和**END**虚拟节点，告诉图从哪里开始执行以及执行应该在哪里结束。

要运行系统，我们将使用 `.stream()` 方法。在构建并编译图后，**每一轮交互都会经过图的 START，直到 END**，它的路径（哪些节点应运行或不运行）由我们的工作流与图的状态共同控制。以下代码包含我们系统的主要流程：

```py
config = {"configurable": {"thread_id": str(uuid.uuid4())}}

while True:
    user = input("User (q/Q to quit): ")
    if user in {"q", "Q"}:
        print("AI: Byebye")
        break
    output = None
    for output in graph.stream(
        {"messages": [HumanMessage(content=user)]}, config=config, stream_mode="updates"
    ):
        last_message = next(iter(output.values()))["messages"][-1]
        last_message.pretty_print()

    if output and "prompt" in output:
        print("Done!")
```

在每次交互时（如果用户没有输入“q”或“Q”退出），我们运行 graph.stream()，传递用户消息，并使用“updates” stream_mode，它会流式传输图每一步后状态的更新（[https://langchain-ai.github.io/langgraph/concepts/low_level/#stream-and-astream](https://langchain-ai.github.io/langgraph/concepts/low_level/#stream-and-astream)）。然后我们从 state_schema 获取最后一条消息并打印出来。

在本教程中，我们仍然会学习如何创建图的节点和边，但首先让我们更多地了解 ToD 系统的架构，并学习如何用**LLMs**、**提示**和**工具调用**实现一个系统。

# ToD 系统的架构

构建**端到端任务导向对话系统**的框架的主要组件是 [1]：

1.  自然语言理解（NLU）用于**提取用户的意图和关键信息**

1.  对话状态跟踪（DST）用于**追踪用户在对话中的信念状态**

1.  对话策略学习（DPL）用于**确定下一步操作**

1.  自然语言生成（NLG）用于**生成对话系统响应**

![](../Images/a4be2cd1978b86ea567eaae32cea0cf2.png)

ToD 系统的主要组件（图片来自秦立波等人 [1]）

通过使用LLM，我们可以将这些组件中的一些组合成一个。**NLP**和**NLG**组件使用LLM实现起来轻而易举，因为理解和生成对话回应正是它们的专长。

我们可以通过使用LangChain的**SystemMessage**来实现对话状态跟踪（**DST**）和对话策略学习（**DPL**），以引导AI行为，并在每次与LLM互动时始终传递此消息。对话的状态也应始终在每次与模型互动时传递给LLM。这意味着我们将确保对话始终围绕我们希望用户完成的任务进行，**通过始终告诉LLM对话的目标是什么以及它应如何行为**。我们首先通过使用提示来做到这一点：

```py
prompt_system_task = """Your job is to gather information from the user about the User Story they need to create.

You should obtain the following information from them:

- Objective: the goal of the user story. should be concrete enough to be developed in 2 weeks.
- Success criteria the sucess criteria of the user story
- Plan_of_execution: the plan of execution of the initiative
- Deliverables: the deliverables of the initiative

If you are not able to discern this info, ask them to clarify! Do not attempt to wildly guess. 
Whenever the user responds to one of the criteria, evaluate if it is detailed enough to be a criterion of a User Story. If not, ask questions to help the user better detail the criterion.
Do not overwhelm the user with too many questions at once; ask for the information you need in a way that they do not have to write much in each response. 
Always remind them that if they do not know how to answer something, you can help them.

After you are able to discern all the information, call the relevant tool."""
```

然后每次我们向LLM发送消息时，都附加这个提示：

```py
def domain_state_tracker(messages):
    return [SystemMessage(content=prompt_system_task)] + messages
```

我们ToD系统LLM实现的另一个重要概念是**工具调用**。如果你再次阅读**prompt_system_task**的最后一句话，它说：“*在你能够辨识出所有信息后，调用相关工具*”。通过这种方式，我们在告诉LLM，当它判断用户提供了所有用户故事参数时，**它应该调用工具来创建用户故事**。我们为此创建的工具将使用Pydantic模型与用户故事参数来构建。

通过仅使用提示和工具调用，我们可以控制我们的ToD系统。很棒对吧？实际上，我们还需要使用**图的状态**来使这一切正常工作。我们将在下一节中完成这一部分，届时我们将最终构建ToD系统。

# 创建对话系统以构建用户故事

好的，开始编码吧。首先我们将指定使用哪个LLM模型，然后设置提示并绑定工具来生成用户故事：

```py
import os
from dotenv import load_dotenv, find_dotenv

from langchain_openai import AzureChatOpenAI
from langchain_core.pydantic_v1 import BaseModel
from typing import List, Literal, Annotated

_ = load_dotenv(find_dotenv()) # read local .env file

llm = AzureChatOpenAI(azure_deployment=os.environ.get("AZURE_OPENAI_CHAT_DEPLOYMENT_NAME"),
                    openai_api_version="2023-09-01-preview",
                    openai_api_type="azure",
                    openai_api_key=os.environ.get('AZURE_OPENAI_API_KEY'),
                    azure_endpoint=os.environ.get('AZURE_OPENAI_ENDPOINT'),
                    temperature=0)

prompt_system_task = """Your job is to gather information from the user about the User Story they need to create.

You should obtain the following information from them:

- Objective: the goal of the user story. should be concrete enough to be developed in 2 weeks.
- Success criteria the sucess criteria of the user story
- Plan_of_execution: the plan of execution of the initiative

If you are not able to discern this info, ask them to clarify! Do not attempt to wildly guess. 
Whenever the user responds to one of the criteria, evaluate if it is detailed enough to be a criterion of a User Story. If not, ask questions to help the user better detail the criterion.
Do not overwhelm the user with too many questions at once; ask for the information you need in a way that they do not have to write much in each response. 
Always remind them that if they do not know how to answer something, you can help them.

After you are able to discern all the information, call the relevant tool."""

class UserStoryCriteria(BaseModel):
    """Instructions on how to prompt the LLM."""
    objective: str
    success_criteria: str
    plan_of_execution: str

llm_with_tool = llm.bind_tools([UserStoryCriteria])
```

正如我们之前所讨论的，我们图的状态仅包括交换的消息和一个标志，用于判断用户故事是否已创建。让我们首先使用**StateGraph**和这个模式来创建图：

```py
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages

class StateSchema(TypedDict):
    messages: Annotated[list, add_messages]

workflow = StateGraph(StateSchema)
```

下一张图展示了最终图的结构：

![](../Images/a2eb92c3d942ba8fb79c921ee2362301.png)

用于创建用户故事的ToD图的结构（图像由作者创建）

在顶部我们有一个**talk_to_user**节点。这个节点可以是：

+   完成对话（跳转到**finalize_dialogue**节点）

+   决定是时候等待用户输入了（跳转到**END**节点）

由于主循环是永远运行的（*while True*），每当图到达END节点时，它会再次等待用户输入。等到我们创建循环时，这一点会变得更加清晰。

让我们从**talk_to_user**节点开始创建图中的节点。这个节点需要跟踪任务（在整个对话中保持主要提示），并且还需要保持消息交换，因为它是存储对话状态的地方。这个状态还会跟踪哪些用户故事的参数已经填写，哪些还没有，使用的是消息。因此，每次此节点应该添加**SystemMessage**并附加来自LLM的新消息：

```py
def domain_state_tracker(messages):
    return [SystemMessage(content=prompt_system_task)] + messages

def call_llm(state: StateSchema):
    """
    talk_to_user node function, adds the prompt_system_task to the messages,
    calls the LLM and returns the response
    """
    messages = domain_state_tracker(state["messages"])
    response = llm_with_tool.invoke(messages)
    return {"messages": [response]}
```

现在我们可以将**talk_to_user**节点添加到此图中了。我们通过为它命名，然后传递我们创建的函数来完成这个操作：

```py
workflow.add_node("talk_to_user", call_llm)
```

这个节点应该是图中运行的第一个节点，所以我们通过**边**来指定这一点：

```py
workflow.add_edge(START, "talk_to_user")
```

到目前为止，图是这样的：

![](../Images/da757765b057ce82f01f6af98a41e93b.png)

这是只有一个节点的图（由作者创建的图像）

为了控制图的流程，我们还将使用LangChain中的消息类。我们有四种类型的消息：

+   **SystemMessage：** 用于引导AI行为的消息

+   **HumanMessage：** 来自人类的消息

+   **AIMessage：** 从聊天模型返回的作为对提示的响应的消息

+   **ToolMessage：** 包含工具调用结果的消息，用于将执行工具的结果传递回模型

我们将使用**图状态**中最后一条消息的**类型**来控制**talk_to_user**节点的流程。如果最后一条消息是***AIMessage***并且它包含***tool_calls***键，那么我们将跳转到**finalize_dialogue**节点，因为该是创建用户故事的时候了。否则，我们应该跳转到**END**节点，因为该是用户回答的时候，应该重启循环。

**finalize_dialogue**节点应该构建**ToolMessage**来将结果传递给模型。**tool_call_id**字段用于将工具调用请求与工具调用响应关联。让我们创建这个节点并将它添加到图中：

```py
def finalize_dialogue(state: StateSchema):
    """
    Add a tool message to the history so the graph can see that it`s time to create the user story
    """
    return {
        "messages": [
            ToolMessage(
                content="Prompt generated!",
                tool_call_id=state["messages"][-1].tool_calls[0]["id"],
            )
        ]
    }

workflow.add_node("finalize_dialogue", finalize_dialogue)
```

现在让我们创建最后一个节点，**create_user_story**节点。此节点将使用提示调用LLM来创建用户故事，并将对话过程中收集到的信息包含在内。如果模型决定该是调用工具的时机，那么**tool_calls**键的值应该包含创建用户故事所需的所有信息。

```py
prompt_generate_user_story = """Based on the following requirements, write a good user story:

{reqs}"""

def build_prompt_to_generate_user_story(messages: list):
    tool_call = None
    other_msgs = []
    for m in messages:
        if isinstance(m, AIMessage) and m.tool_calls: #tool_calls is from the OpenAI API
            tool_call = m.tool_calls[0]["args"]
        elif isinstance(m, ToolMessage):
            continue
        elif tool_call is not None:
            other_msgs.append(m)
    return [SystemMessage(content=prompt_generate_user_story.format(reqs=tool_call))] + other_msgs

def call_model_to_generate_user_story(state):
    messages = build_prompt_to_generate_user_story(state["messages"])
    response = llm.invoke(messages)
    return {"messages": [response]}

workflow.add_node("create_user_story", call_model_to_generate_user_story)
```

所有节点创建完成后，接下来是添加**边**。我们将向**talk_to_user**节点添加一个条件边。记住，这个节点可以：

+   如果该是调用工具的时机，就结束对话（跳转到**finalize_dialogue**节点）

+   决定我们需要收集用户输入（跳转到**END**节点）

这意味着我们只检查最后一条消息是否是AIMessage，并且是否包含tool_calls键；否则我们应该跳转到END节点。让我们创建一个函数来检查这个，并将它作为一条边添加：

```py
def define_next_action(state) -> Literal["finalize_dialogue", END]:
    messages = state["messages"]

    if isinstance(messages[-1], AIMessage) and messages[-1].tool_calls:
        return "finalize_dialogue"
    else:
        return END

workflow.add_conditional_edges("talk_to_user", define_next_action)
```

现在让我们添加其他边：

```py
workflow.add_edge("finalize_dialogue", "create_user_story")
workflow.add_edge("create_user_story", END)
```

这样图的工作流程就完成了。是时候编译图并创建循环来运行它了：

```py
memory = MemorySaver()
graph = workflow.compile(checkpointer=memory)

config = {"configurable": {"thread_id": str(uuid.uuid4())}}

while True:
    user = input("User (q/Q to quit): ")
    if user in {"q", "Q"}:
        print("AI: Byebye")
        break
    output = None
    for output in graph.stream(
        {"messages": [HumanMessage(content=user)]}, config=config, stream_mode="updates"
    ):
        last_message = next(iter(output.values()))["messages"][-1]
        last_message.pretty_print()

    if output and "create_user_story" in output:
        print("User story created!")
```

最后让我们测试系统：

![](../Images/f88a5dbb64636f7ecc2afae4a90569d9.png)

助手在行动中（图片由作者创建）

# 最后的思考

有了 LangGraph 和 LangChain，我们可以构建引导用户通过结构化互动的系统，借助 LLMs 帮助我们控制条件逻辑，从而减少创建它们的复杂性。

通过结合提示、记忆管理和工具调用，我们可以创建直观且有效的对话系统，开启用户互动和任务自动化的新可能性。

我希望这个教程能帮助你更好地理解如何使用 LangGraph（我花了好几天时间琢磨如何让这个库的所有部分协同工作）。

本教程的所有代码可以在这里找到：[dmesquita/task_oriented_dialogue_system_langgraph (github.com)](https://github.com/dmesquita/task_oriented_dialogue_system_langgraph)

感谢阅读！

# 参考文献

[1] Qin, Libo, 等人。“端到端任务导向对话：任务、方法及未来方向的调查。” *arXiv 预印本 arXiv:2311.09008* (2023)。

[2] *从用户需求生成提示*。可在以下地址查看：[https://langchain-ai.github.io/langgraph/tutorials/chatbots/information-gather-prompting](https://langchain-ai.github.io/langgraph/tutorials/chatbots/information-gather-prompting)
