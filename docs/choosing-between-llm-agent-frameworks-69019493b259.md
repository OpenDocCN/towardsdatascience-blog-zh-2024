# 在LLM代理框架之间进行选择

> 原文：[https://towardsdatascience.com/choosing-between-llm-agent-frameworks-69019493b259?source=collection_archive---------0-----------------------#2024-09-21](https://towardsdatascience.com/choosing-between-llm-agent-frameworks-69019493b259?source=collection_archive---------0-----------------------#2024-09-21)

## *构建定制基于代码的代理与主要代理框架之间的权衡。*

[](https://aparnadhinak.medium.com/?source=post_page---byline--69019493b259--------------------------------)[![Aparna Dhinakaran](../Images/e431ee69563ecb27c86f3428ba53574c.png)](https://aparnadhinak.medium.com/?source=post_page---byline--69019493b259--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--69019493b259--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--69019493b259--------------------------------) [Aparna Dhinakaran](https://aparnadhinak.medium.com/?source=post_page---byline--69019493b259--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--69019493b259--------------------------------) ·12分钟阅读·2024年9月21日

--

![](../Images/7f41def86bbb86b6e740c349eac8b70e.png)

作者提供的图片

*感谢John Gilhuly对本文的贡献。*

代理正处于一个重要时刻。随着多个新框架和新的[投资](https://foundationcapital.com/goodbye-aiops-welcome-agentsres-the-next-100b-opportunity/)的涌入，现代AI代理正在克服[动摇的起源](https://arxiv.org/html/2405.13966v1)，迅速取代RAG成为实施优先事项。那么，2024年是否终于会成为自动化AI系统的元年，这些系统可以接管撰写电子邮件、预订航班、与我们的数据对话，甚至是其他任何任务呢？

也许，但还有很多工作需要完成才能到达那个点。任何构建代理的开发者不仅需要选择基础架构——选择使用哪个模型、使用场景和架构——还需要选择利用哪个框架。你是选择长期存在的LangGraph，还是选择新兴的LlamaIndex工作流？或者你选择传统方式，自己编写整个代码？

本文旨在让这个选择变得更加容易。在过去的几周里，我在主要的框架中构建了相同的代理，以从技术层面考察每个框架的优缺点。每个代理的所有代码都可以在[这个仓库](https://github.com/Arize-ai/phoenix/tree/main/examples/agent_framework_comparison)中找到。

## 用于测试的代理背景

用于测试的代理包括函数调用、多个工具或技能、与外部资源的连接以及共享的状态或记忆。

该代理具有以下功能：

1.  从知识库回答问题

1.  与数据对话：回答关于LLM应用程序遥测数据的问题

1.  数据分析：分析检索到的遥测数据中的更高层次的趋势和模式

为了实现这些目标，代理有三个初始技能：使用产品文档的RAG、在跟踪数据库上生成SQL、以及数据分析。代理UI使用一个简单的Gradio驱动界面，代理本身则构建为一个聊天机器人。

# 基于代码的代理（无框架）

在开发代理时，你的第一个选择是完全跳过框架，完全自己构建代理。在开始这个项目时，这就是我选择的方式。

![](../Images/d10a779ccc5d91bfa0325de4b2665e86.png)

图片由作者提供

## 纯代码架构

下面的基于代码的代理由一个OpenAI驱动的路由器组成，它使用函数调用来选择要使用的正确技能。在该技能完成后，它返回路由器，继续调用另一个技能或回应用户。

代理会保持一份持续更新的消息和响应列表，每次调用路由器时，都会将其完整传入，以便在多个周期中保持上下文。

```py
def router(messages):
    if not any(
        isinstance(message, dict) and message.get("role") == "system" for message in messages
    ):
        system_prompt = {"role": "system", "content": SYSTEM_PROMPT}
        messages.append(system_prompt)

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages,
        tools=skill_map.get_combined_function_description_for_openai(),
    )

    messages.append(response.choices[0].message)
    tool_calls = response.choices[0].message.tool_calls
    if tool_calls:
        handle_tool_calls(tool_calls, messages)
        return router(messages)
    else:
        return response.choices[0].message.content
```

技能本身在各自的类中定义（例如：GenerateSQLQuery），这些类统一保存在SkillMap中。路由器本身只与SkillMap交互，使用它加载技能名称、描述和可调用的函数。这种方法意味着，向代理中添加新技能就像编写该技能的类一样简单，然后将其添加到SkillMap中的技能列表中。这里的想法是使添加新技能变得容易，而不干扰路由器的代码。

```py
class SkillMap:
    def __init__(self):
        skills = [AnalyzeData(), GenerateSQLQuery()]

        self.skill_map = {}
        for skill in skills:
            self.skill_map[skill.get_function_name()] = (
                skill.get_function_dict(),
                skill.get_function_callable(),
            )

    def get_function_callable_by_name(self, skill_name) -> Callable:
        return self.skill_map[skill_name][1]

    def get_combined_function_description_for_openai(self):
        combined_dict = []
        for _, (function_dict, _) in self.skill_map.items():
            combined_dict.append(function_dict)
        return combined_dict

    def get_function_list(self):
        return list(self.skill_map.keys())

    def get_list_of_function_callables(self):
        return [skill[1] for skill in self.skill_map.values()]

    def get_function_description_by_name(self, skill_name):
        return str(self.skill_map[skill_name][0]["function"])
```

总体而言，这种方法相对简单易实现，但也存在一些挑战。

## 纯代码代理的挑战

第一个难点在于构建路由器系统提示。通常，上述例子中的路由器坚持自己生成SQL，而不是将这一任务委托给合适的技能。如果你曾试图让LLM*不*做某件事，你就会知道这个过程是多么令人沮丧；找到有效的提示语经历了多轮调试。考虑到每个步骤的不同输出格式也很棘手。由于我选择不使用结构化输出，我必须为路由器和技能中的每个LLM调用准备应对不同格式的输出。

## 纯代码代理的优势

基于代码的方法提供了一个很好的基础和起点，提供了一种很好的方式来学习代理是如何工作的，而不依赖于现有框架中的现成代理教程。虽然让LLM表现如预期可能有些挑战，但代码结构本身足够简单，适用于某些特定的使用场景（详细分析见下文）。

# LangGraph

LangGraph是最早期的代理框架之一，首次发布于2024年1月。该框架旨在通过采用Pregel图结构来解决现有管道和链的无环性质问题。LangGraph通过将节点、边和条件边的概念添加到图中，使得定义代理中的循环变得更加容易。LangGraph建立在LangChain之上，使用该框架的对象和类型。

![](../Images/08d1c12009b7fdd2a59ea76f527eeee7.png)

图片由作者提供

## LangGraph架构

从表面上看，LangGraph代理与代码基础代理相似，但其背后的代码截然不同。LangGraph仍然技术上使用“路由器”，通过函数调用OpenAI，并使用响应继续执行新步骤。然而，程序在技能之间的切换方式完全不同。

```py
tools = [generate_and_run_sql_query, data_analyzer]
model = ChatOpenAI(model="gpt-4o", temperature=0).bind_tools(tools)

def create_agent_graph():
    workflow = StateGraph(MessagesState)

    tool_node = ToolNode(tools)
    workflow.add_node("agent", call_model)
    workflow.add_node("tools", tool_node)

    workflow.add_edge(START, "agent")
    workflow.add_conditional_edges(
        "agent",
        should_continue,
    )
    workflow.add_edge("tools", "agent")

    checkpointer = MemorySaver()
    app = workflow.compile(checkpointer=checkpointer)
    return app
```

此处定义的图有一个节点用于初始的OpenAI调用，称为上面的“agent”，还有一个节点用于工具处理步骤，称为“tools”。LangGraph有一个内置的对象叫做ToolNode，它接收一个可调用工具的列表，并根据ChatMessage响应触发这些工具，然后再返回到“agent”节点。

```py
def should_continue(state: MessagesState):
    messages = state["messages"]
    last_message = messages[-1]
    if last_message.tool_calls:
        return "tools"
    return END

def call_model(state: MessagesState):
    messages = state["messages"]
    response = model.invoke(messages)
    return {"messages": [response]}
```

每次调用“agent”节点后（换句话说：代码基础代理中的路由器），`should_continue`边缘决定是否将响应返回给用户，还是传递给ToolNode以处理工具调用。

在每个节点中，“state”存储了来自OpenAI的消息和响应列表，类似于代码基础代理的方法。

## LangGraph的挑战

示例中与LangGraph相关的大多数困难都源于需要使用Langchain对象来确保流程顺畅。

**挑战 #1：函数调用验证**

为了使用ToolNode对象，我不得不重构我现有的技能代码。ToolNode接收一个可调用函数的列表，最初我以为可以直接使用现有的函数，然而由于我的函数参数问题，程序出现了错误。

这些技能被定义为具有可调用成员函数的类，意味着它们的第一个参数是“self”。虽然GPT-4o足够聪明，能够在生成的函数调用中不包含“self”参数，但LangGraph将其视为一个验证错误，因为缺少该参数。

这花了我几个小时才弄明白，因为错误信息却将函数中的第三个参数（在数据分析技能中的“args”）标记为缺失的参数：

```py
pydantic.v1.error_wrappers.ValidationError: 1 validation error for data_analysis_toolSchema
args field required (type=value_error.missing)
```

值得一提的是，错误信息源自Pydantic，而非LangGraph。

最终，我下定决心重新定义我的技能，使用Langchain的@tool装饰器，并且成功使其工作。

```py
@tool
def generate_and_run_sql_query(query: str):
    """Generates and runs an SQL query based on the prompt.

    Args:
        query (str): A string containing the original user prompt.

    Returns:
        str: The result of the SQL query.
    """
```

**挑战 #2：调试**

如前所述，调试框架非常困难。主要原因是令人困惑的错误信息和抽象的概念，使得查看变量变得更加困难。

抽象概念主要在尝试调试代理发送的消息时出现。LangGraph 将这些消息存储在 state[“messages”] 中。图中的一些节点会自动从这些消息中提取内容，这可能使得理解节点访问消息时消息的值变得困难。

![](../Images/14b7cf88b1c2547c64d82b735e16a194.png)

*代理行为的顺序视图（图片由作者提供）*

## LangGraph 的优势

LangGraph 的主要优势之一是它易于使用。图结构代码简洁且易于访问。特别是当你有复杂的节点逻辑时，拥有一个单一的图视图可以更容易理解代理是如何互相连接的。LangGraph 还使得将现有的基于 LangChain 构建的应用程序转化为此结构变得非常简单。

## 重点总结

如果你使用框架中的所有内容，LangGraph 工作得很干净；如果你跳出框架，准备好面对一些调试上的困难。

# LlamaIndex 工作流

Workflows 是一个较新的代理框架，首次亮相是在今年夏天。像 LangGraph 一样，它旨在让构建循环代理变得更容易。Workflows 还特别关注异步执行。

工作流中的一些元素似乎是直接响应 LangGraph 的，特别是它使用事件而非边缘和条件边缘。工作流使用步骤（类似于 LangGraph 中的节点）来包含逻辑，并通过发射和接收事件在步骤之间进行转换。

![](../Images/26b9e664d6cce92214a956f0faf4f485.png)

图片由作者提供

上面的结构看起来与 LangGraph 结构相似，唯一的不同是我为工作流添加了一个设置步骤来准备代理上下文，更多内容请见下文。尽管结构相似，但其背后驱动的代码是完全不同的。

## 工作流架构

下面的代码定义了工作流结构。与 LangGraph 类似，这里是我准备状态并将技能附加到 LLM 对象上的地方。

```py
class AgentFlow(Workflow):
    def __init__(self, llm, timeout=300):
        super().__init__(timeout=timeout)
        self.llm = llm
        self.memory = ChatMemoryBuffer(token_limit=1000).from_defaults(llm=llm)
        self.tools = []
        for func in skill_map.get_function_list():
            self.tools.append(
                FunctionTool(
                    skill_map.get_function_callable_by_name(func),
                    metadata=ToolMetadata(
                        name=func, description=skill_map.get_function_description_by_name(func)
                    ),
                )
            )

    @step
    async def prepare_agent(self, ev: StartEvent) -> RouterInputEvent:
        user_input = ev.input
        user_msg = ChatMessage(role="user", content=user_input)
        self.memory.put(user_msg)

        chat_history = self.memory.get()
        return RouterInputEvent(input=chat_history)
```

这里也是我定义额外步骤“prepare_agent”的地方。这个步骤根据用户输入创建一个ChatMessage，并将其添加到工作流的记忆中。将其作为一个独立的步骤意味着我们在代理循环执行步骤时会返回到这个步骤，这样可以避免重复将用户消息添加到记忆中。

在 LangGraph 的情况下，我通过一个位于图外的 run_agent 方法实现了相同的功能。这个改变主要是风格上的，然而我认为将这些逻辑与工作流和图结构一起处理会更简洁一些，正如我们在这里所做的。

设置好工作流后，我定义了路由代码：

```py
@step
async def router(self, ev: RouterInputEvent) -> ToolCallEvent | StopEvent:
    messages = ev.input

    if not any(
        isinstance(message, dict) and message.get("role") == "system" for message in messages
    ):
        system_prompt = ChatMessage(role="system", content=SYSTEM_PROMPT)
        messages.insert(0, system_prompt)

    with using_prompt_template(template=SYSTEM_PROMPT, version="v0.1"):
        response = await self.llm.achat_with_tools(
            model="gpt-4o",
            messages=messages,
            tools=self.tools,
        )

    self.memory.put(response.message)

    tool_calls = self.llm.get_tool_calls_from_response(response, error_on_no_tool_call=False)
    if tool_calls:
        return ToolCallEvent(tool_calls=tool_calls)
    else:
        return StopEvent(result=response.message.content)
```

还有工具调用处理代码：

```py
@step
async def tool_call_handler(self, ev: ToolCallEvent) -> RouterInputEvent:
    tool_calls = ev.tool_calls

    for tool_call in tool_calls:
        function_name = tool_call.tool_name
        arguments = tool_call.tool_kwargs
        if "input" in arguments:
            arguments["prompt"] = arguments.pop("input")

        try:
            function_callable = skill_map.get_function_callable_by_name(function_name)
        except KeyError:
            function_result = "Error: Unknown function call"

        function_result = function_callable(arguments)
        message = ChatMessage(
            role="tool",
            content=function_result,
            additional_kwargs={"tool_call_id": tool_call.tool_id},
        )

        self.memory.put(message)

    return RouterInputEvent(input=self.memory.get())
```

这两者与基于代码的代理相比，更像是`LangGraph`代理。这主要是因为`Workflows`将条件路由逻辑保留在步骤中，而不是条件边中——在`LangGraph`中，第18至24行是一个条件边，而现在它们只是路由步骤的一部分——而`LangGraph`有一个`ToolNode`对象，几乎自动完成`tool_call_handler`方法中的所有操作。

在路由步骤之后，有一件事让我非常高兴，那就是我可以使用我的`SkillMap`和基于代码的代理中的现有技能与`Workflows`配合使用。这些技能无需任何修改就能与`Workflows`一起工作，这让我的工作变得更加轻松。

## `Workflows`的挑战

**挑战 #1：同步与异步**

虽然实时代理更倾向于使用异步执行，但调试同步代理要容易得多。`Workflows`是为了异步执行而设计的，强行实现同步执行非常困难。

我最初以为只需要去掉“async”方法标记，并将“achat_with_tools”改为“chat_with_tools”就可以了。然而，由于`Workflow`类中的底层方法也被标记为异步，因此必须重新定义这些方法以便同步执行。我最终还是坚持使用异步方法，但这并没有使调试变得更加困难。

![](../Images/86bb92ce7e47d92f16d5fb9bdcadc985.png)

*代理操作的顺序视图（图像由作者提供）*

**挑战 #2：Pydantic验证错误**

与`LangGraph`的问题重演，类似的问题出现在技能的Pydantic验证错误上。幸运的是，这次比较容易解决，因为`Workflows`能够很好地处理成员函数。我最终只是不得不在为我的技能创建`LlamaIndex FunctionTool`对象时更加严格：

```py
for func in skill_map.get_function_list(): 
            self.tools.append(FunctionTool(
                skill_map.get_function_callable_by_name(func), 
                metadata=ToolMetadata(name=func, description=skill_map.get_function_description_by_name(func))))
```

*`AgentFlow.__init__`中构建`FunctionTools`的摘录*

## `Workflows`的优点

我构建`Workflows`代理的难度远低于构建`LangGraph`代理，主要是因为`Workflows`仍然需要我自己编写路由逻辑和工具处理代码，而不是提供内置函数。这也意味着我的`Workflow`代理与我的基于代码的代理非常相似。

最大的区别在于事件的使用。我使用了两个自定义事件在我的代理中各个步骤之间移动：

```py
class ToolCallEvent(Event):
    tool_calls: list[ToolSelection]

class RouterInputEvent(Event):
    input: list[ChatMessage]
```

事件驱动的发射器-接收器架构取代了在代理中直接调用一些方法，例如工具调用处理器。

如果你有更复杂的系统，包含多个异步触发并可能发出多个事件的步骤，那么这种架构在清晰地管理这些步骤时会非常有用。

`Workflows`的其他优点包括其非常轻量级，并且不会强制要求你使用太多结构（除了使用某些`LlamaIndex`对象），而且其基于事件的架构提供了一种有益的替代方法，替代了直接的函数调用——特别是在复杂的异步应用中。

# 比较框架

看待这三种方法，每种都有其优点。

无框架的方法是最简单的实现方式。由于所有的抽象都是由开发者定义的（例如上例中的SkillMap对象），因此保持各种类型和对象的清晰非常容易。然而，代码的可读性和可访问性完全取决于个人开发者，且不难看出，随着代理的复杂性增加，若没有强制的结构，代码可能会变得混乱。

LangGraph提供了相当多的结构化，这使得代理的定义非常清晰。如果更大的团队在协作开发一个代理，这种结构将有助于强制执行架构。LangGraph对于不太熟悉结构的人来说，也可能是一个很好的起点。然而，这也存在权衡 —— 由于LangGraph为你做了很多工作，如果你没有完全接受这个框架，可能会带来麻烦；代码可能非常干净，但你可能需要为此付出更多的调试工作。

工作流位于中间位置。基于事件的架构对于某些项目可能非常有用，而且LlamaIndex类型的使用要求较少，为那些没有在整个应用程序中完全使用框架的人提供了更大的灵活性。

![](../Images/e4c647265b3453dd773d7ac32c1702b3.png)

由作者创建的图像

最终，核心问题可能只是“你是否已经在使用LlamaIndex或LangChain来协调你的应用程序？”LangGraph和工作流与各自的底层框架紧密相连，以至于每个代理特定框架的额外好处可能仅凭优点本身不足以让你做出切换决定。

纯代码方法可能始终是一个有吸引力的选择。如果你有足够的严格性来文档化并执行任何创建的抽象，那么确保外部框架中的任何部分不会拖慢你是很容易的。

# 选择代理框架时的关键问题

当然，“这取决于情况”从来不是一个令人满意的答案。这三个问题应该能帮助你决定在下一个代理项目中使用哪个框架。

***你是否已经在项目中使用LlamaIndex或LangChain的重要部分？***

如果是，首先探索这个选项。

***你熟悉常见的代理结构吗，还是希望有某些指导来告诉你如何构建代理？***

如果你属于后者，尝试工作流。如果你*真的*属于后者，尝试LangGraph。

***你的代理是否已经构建过？***

框架的一个好处是，每个框架都有许多教程和示例。纯代码代理的示例要少得多。

![](../Images/f8250fa7aea13c3785aeb32ecd006a74.png)

由作者创建的图像

# 结论

选择一个代理框架只是众多决策中的一项，它将影响生成式AI系统在生产中的结果。像往常一样，设置健全的防护措施和[LLM追踪](https://docs.arize.com/phoenix/tracing/llm-traces)是值得的——并且随着新的代理框架、研究和模型的出现，打破既有技术时要保持灵活性。
