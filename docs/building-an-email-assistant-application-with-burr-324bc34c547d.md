# 使用Burr构建邮件助手应用程序

> 原文：[https://towardsdatascience.com/building-an-email-assistant-application-with-burr-324bc34c547d?source=collection_archive---------4-----------------------#2024-04-25](https://towardsdatascience.com/building-an-email-assistant-application-with-burr-324bc34c547d?source=collection_archive---------4-----------------------#2024-04-25)

## 本教程演示了如何使用Burr，通过简单的OpenAI客户端调用GPT4，以及使用FastAPI来创建一个定制的邮件助手智能体。

[](https://medium.com/@stefan.krawczyk?source=post_page---byline--324bc34c547d--------------------------------)[![Stefan Krawczyk](../Images/150405abaad9590e1dc2589168ed2fa3.png)](https://medium.com/@stefan.krawczyk?source=post_page---byline--324bc34c547d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--324bc34c547d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--324bc34c547d--------------------------------) [Stefan Krawczyk](https://medium.com/@stefan.krawczyk?source=post_page---byline--324bc34c547d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--324bc34c547d--------------------------------) ·12分钟阅读·2024年4月25日

--

![](../Images/7b059bfe0d0e941f2f4d698e8c8ce886.png)

我们将创建的智能体应用程序的控制流程。图像由作者提供。

在本教程中，我将演示如何使用 [Burr](https://github.com/dagworks-inc/burr)，一个开源框架（披露：我参与了它的创建），通过简单的OpenAI客户端调用GPT4，以及 [FastAPI](https://fastapi.tiangolo.com/) 来创建一个定制的邮件助手智能体。我们将描述面对的挑战，然后讲解如何解决这些问题。对于应用的前端，我们提供一个参考实现，但不会深入细节。

# **为什么交互式智能体应用程序是一项挑战？**

大型语言模型（LLMs）很少能单独完成复杂的任务，而且几乎从未在第一次尝试时成功。虽然目前流行一种说法，认为如果给ChatGPT连接互联网，它可以解决世界上的所有问题，但我们遇到的大多数高价值工具都使用了AI的创新和人类的引导。这是构建智能体（agent）的普遍趋势——一种由AI根据收到的信息做出决策的方法——这些信息可以是它查询的内容、用户提供的信息，或者是另一个大型语言模型提供的信息。

一个简单的例子是一个帮助你起草邮件回复的工具。你提供邮件内容和你的回复目标，它会为你写出回复内容。至少，你需要提供反馈，这样它才能调整回复内容。此外，你还希望它能有机会提出澄清性问题（一个过于自信但错误的聊天机器人对任何人都没有帮助）。

在设计这个交互时，你的系统将不可避免地成为用户/LLM控制之间的反复往返。除了AI应用程序的标准挑战（不可靠的API、随机实现等），你还将面临一系列新的问题，包括：

1.  合理建模一组交互点/流程

1.  持久化状态，以便用户可以从中断的地方继续交互/应用程序

1.  监控LLM做出的决策（例如，是否向用户提问）

以此类推……在本文中，我们将讲解如何解决这些问题——我们将使用Burr库和FastAPI构建一个Web服务，以可扩展、模块化的方式解决这些挑战；这样你就可以将其作为自己代理助手需求的蓝图。

# 工具

# Burr

[Burr](https://github.com/dagworks-inc/burr)是一个轻量级的Python库，用于构建状态机应用程序。你可以通过一系列操作（这些可以是装饰函数或对象）构建你的应用程序，这些操作声明来自状态的输入，以及来自用户的输入。这些操作指定自定义逻辑（可以委托给任何框架），以及如何更新状态的指令。状态是不可变的，这使得你可以在任何时候检查它。Burr处理编排、监控和持久化。

```py
@action(reads=["counter"], writes=["counter"])
def count(state: State) -> Tuple[dict, State]:
    current = state["counter"] + 1
    result = {"counter": current}
    return result, state.update(counter=counter)
```

请注意，上述操作有两个返回值——结果（计数器），以及新的、修改后的状态（计数器字段已增加）。

你可以将Burr操作作为应用程序的一部分运行——这使得你可以将它们通过一系列（可选的）条件过渡串联起来。

```py
from burr.core import ApplicationBuilder, default, expr
app = (
    ApplicationBuilder()
    .with_state(counter=0) # initialize the count to zero
    .with_actions(
        count=count, 
        done=done # implementation left out above
    ).with_transitions(
        ("count", "count", expr("counter < 10")), # Keep counting if the counter is less than 10
        ("count", "done", default) # Otherwise, we're done
    ).with_entrypoint("count") # we have to start somewhere
    .build()
)
```

Burr提供了一个用户界面，支持监控/遥测功能，并提供钩子来在执行过程中持久化状态/执行任意代码。

你可以将其可视化为一个流程图，即图形/状态机：

![](../Images/82b3314c0dd8202e046fac71eb013c1d.png)

我们应用程序的图像，由Burr生成。图像来自作者。

并使用本地遥测调试器进行监控：

![](../Images/7cfd6694f108ffd1e74d01f09afba486.png)

Burr自带UI——这是我们检查计数器示例运行时的界面。图像来自作者。

虽然我们在上面展示了（非常简单的）计数器示例，Burr通常用于构建聊天机器人/代理（我们将在本文中展示一个示例）。

# FastAPI

[FastAPI](https://fastapi.tiangolo.com/)是一个框架，可以让你将Python函数暴露为REST API。它有一个简单的接口——你编写你的函数并装饰它们，然后运行你的脚本——将其转换为一个具有自文档化端点的服务器，通过[OpenAPI](https://www.openapis.org/)实现。

```py
@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    """A very simpler example of an endpoint that takes in arguments."""
    return {"item_id": item_id, "q": q}
```

FastAPI易于在任何云提供商上部署——它是基础设施无关的，通常可以横向扩展（只要考虑到状态管理）。有关更多信息，请参阅[此页面](https://fastapi.tiangolo.com/deployment/cloud/?h=deploy)。

# React（或任何前端框架）

你可以使用任何前端框架——然而，基于 React 的工具具有天然优势，因为它将一切建模为状态的函数，这可以与 Burr 中的概念 1:1 映射。在演示应用程序中，我们使用了[react](https://react.dev/)、[react-query](https://tanstack.com/query/latest/docs/framework/react/overview) 和[tailwind](https://tailwindcss.com/)，但我们将大部分跳过这部分内容（因为它与文章的主要目的无关）。

# 构建

让我们更深入地探讨一下概念模型。从高层次来看，我们的电子邮件助手将执行以下操作：

1.  接受电子邮件和回应指令

1.  提出一组澄清性问题（如果 LLM 认为有必要）

1.  使用这些问题的答案生成草稿

1.  接受反馈并生成另一个草稿，重复此过程直到用户满意

1.  返回最终草稿（完成）

# 控制流建模

由于 Burr 要求你从*动作*和*过渡*中构建控制流，我们最初可以将其建模为一个简单的流程图。

![](../Images/7b059bfe0d0e941f2f4d698e8c8ce886.png)

我们应用程序的外观。图片由作者提供。

我们在实际编写任何代码之前就已草拟了这个——你会看到它自然地转化为代码。

绿色节点代表动作（这些操作接收状态并修改它），蓝色节点代表输入（这些是应用程序必须暂停并向用户询问信息的点）。请注意，存在一个循环（formulate_draft ⇔process_feedback）——我们根据反馈进行迭代，直到对结果满意为止。

该图表仅是 Burr 所展示内容的样式化版本——建模应该尽量接近实际代码。我们没有显示状态信息（步骤中传入/返回的数据），但我们需要跟踪以下内容（这些内容在任何给定时刻可能会被填充，也可能不会），以便我们做出下一步决策：

1.  初始输入：`{email_to_respond: str, response_instructions: str}`

1.  LLM 提出的问题和用户的回答（如果有的话）：`{clarifications: list[str], response_instructions: list[str]}`

1.  草稿和反馈的列表：`{drafts: list[str], feedback_history: list[str]}`

1.  最终结果：`{final_result: str}`

# 实施/测试

根据上述需求，我们可以构建一个简单的 Burr 应用程序，因为我们的代码可以与上面的图表紧密匹配。举个例子，我们来看一下[determine_clarifications](https://github.com/DAGWorks-Inc/burr/blob/c3810dcf5a4aa44153957377ddb17fc97e05ac92/examples/email-assistant/application.py#L32C1-L65C46)步骤：

```py
@action(
    reads=["response_instructions", "incoming_email"], 
    writes=["clarification_questions"]
)
def determine_clarifications(state: State) -> Tuple[dict, State]:
    """Determines if the response instructions require clarification."""

    incoming_email = state["incoming_email"]
    response_instructions = state["response_instructions"]
    client = _get_openai_client()

    result = client.chat.completions.create(
        model="gpt-4",
        messages=[
            {
                "role": "system",
                "content": ("You are a chatbot that has the task of "
                            "generating responses to an email on behalf "
                            "of a user. "),
            },
            {
                "role": "user",
                "content": (
                    f"The email you are to respond to is: {incoming_email}."
                     # ... left out, see link above
                    "The questions, joined by newlines, must be the only "
                    "text you return. If you do not need clarification, "
                    "return an empty string."
                ),
            },
        ],
    )
    content = result.choices[0].message.content
    all_questions = content.split("\n") if content else []
    return {"clarification_questions": all_questions}, state.update(
        clarification_questions=all_questions)
```

请注意，这使用的是简单的OpenAI调用——如果你希望更抽象一些，你可以将其替换为[Langchain](https://www.langchain.com/)、[LlamaIndex](https://www.llamaindex.ai/)、[Hamilton](https://github.com/dagWorks-Inc/hamilton)（或其他类似工具），并委托你喜欢使用的LLM。而且，你应该可能使用一些更具体的工具（例如[instructor](https://github.com/jxnl/instructor)）来保证输出格式。

为了将这些结合在一起，我们将它们放入应用构建器中——这使我们能够设置条件转移（例如`len(clarification_questions>0)`），从而连接不同的动作，重现上面的图表。

```py
application = (
    ApplicationBuilder()
    # define our actions
    .with_actions(
        process_input,
        determine_clarifications,
        clarify_instructions,
        formulate_draft,
        process_feedback,
        final_result,
    )
    # define how our actions connect
    .with_transitions(
        ("process_input", "determine_clarifications"),
        (
            "determine_clarifications",
            "clarify_instructions",
            expr("len(clarification_questions) > 0"),
        ),
        ("determine_clarifications", "formulate_draft"),
        ("clarify_instructions", "formulate_draft"),
        ("formulate_draft", "process_feedback"),
        ("process_feedback", "formulate_draft", expr("len(feedback) > 0")),
        ("process_feedback", "final_result"),
    )
    .with_state(draft_history=[])
    .with_entrypoint("process_input")
    .build()
)
```

为了进行迭代，我们使用了一个[jupyter notebook.](https://github.com/DAGWorks-Inc/burr/blob/main/examples/email-assistant/notebook.ipynb) 运行我们的应用非常简单——你只需要在Application上调用`.run()`方法，并设置合适的*停止*条件。我们希望它在任何需要用户输入的动作之前停止（`clarify_instructions`和`process_feedback`），以及在`final_result`之后停止。然后，我们可以在一个while循环中运行它，要求用户输入并将其反馈给状态机：

```py
def request_answers(questions):
    """Requests answers from the user for the questions the LLM has"""
    answers = []
    print("The email assistant wants more information:\n")
    for question in questions:
        answers.append(input(question))
    return answers

def request_feedback(draft):
    """Requests feedback from the user for a draft"""
    print( 
        f"here's a draft!: \n {draft} \n \n What feedback do you have?",
    )
    return input("Write feedback or leave blank to continue (if you're happy)")
inputs = {
    "email_to_respond" : EMAIL,
    "response_instructions" : INSTRUCTIONS
}

# in our notebook cell:
while True:
    action, result, state = app.run(
        halt_before=["clarify_instructions", "process_feedback"], 
        halt_after=["final_result"],
        inputs=inputs
    )
    if action.name == "clarify_instructions":
        questions = state["clarification_questions"]
        answers = request_answers(questions)
        inputs = {
            "clarification_inputs" : answers
        }
    if action.name == "process_feedback":
        feedback = request_feedback(state["current_draft"])
        inputs = {"feedback" : feedback}
    if action.name == "final_result":
        print("final result is:", state["current_draft"])
        break
```

然后，你可以使用Burr UI来监控应用的运行！

![](../Images/fe14069261250066fd0ba15e25320d6b.png)

使用Burr UI（结合电子邮件应用UI）并查看其执行情况的示例。图像由作者提供。

# 持久化

我们将把结果保存到一个SQLite服务器中（虽然正如你稍后看到的，这可以自定义）。为此，我们需要向ApplicationBuilder中添加几行代码。

```py
state_persister = SQLLitePersister(
    db_path="sqllite.db", 
    table_name="email_assistant_table"
)

app = (
    ApplicationBuilder().
    ... # the code we had above
    .initialize(  
        initializer=state_persister,
        resume_at_next_action=True,
        default_state={"chat_history" : []},
        default_entrypoint="process_input"
    )
    .with_identifiers(app_id=app_id)
    .build()
)
```

这确保了我们创建的每个电子邮件草稿都会被保存，并且可以在每个步骤中加载。当你想恢复之前的电子邮件草稿时，你只需要重新运行代码，它将从上次停止的地方继续。

# 集成到Web服务器中

为了在Web服务器中暴露这些端点，我们将使用FastAPI来创建端点，并使用Pydantic来表示类型。在深入细节之前，我们需要注意，Burr自然为每个应用实例提供了一个`application_id`（可以是生成的或指定的）。在这种情况下，`application_id`对应于某个特定的电子邮件草稿。这使得我们能够唯一地访问它，从数据库中查询等……它还支持分区键（例如user_id），这样你可以在数据库中添加额外的索引。我们将API围绕输入/输出进行设计。

# 端点

我们将构建以下端点：

1.  `POST /create`: 这将创建一个新应用并返回ID

1.  `PUT /initialize_draft/{id}/`: 这会调用process_input，传入电子邮件和指令

1.  `PUT /clarify_instructions/{id}`: 这将把答案返回给LLM

1.  `PUT /process_feedback/{id}`: 这将把反馈返回给LLM

1.  `GET /{id}/state`: 这将返回应用程序的当前状态

GET端点允许我们获取应用的当前状态——这使得用户可以在退出浏览器或被分心后重新加载。每个端点将返回应用的完整状态，可以在前端渲染。此外，它将指示我们调用的下一个API端点，从而让用户界面渲染正确的表单并提交到正确的端点。

使用FastAPI + Pydantic，这变得非常简单。首先，我们添加一个工具来获取应用对象。它将使用缓存的版本或实例化它：

```py
@functools.lru_cache(maxsize=128)
def get_application(app_id: str) -> Application:
    app = email_assistant_application.application(app_id=app_id)
    return app
```

这仅仅是调用我们在email_assistant中的应用函数，该函数重新创建了应用。我们没有在这里包含`create`函数，但它会调用相同的API。

# 数据模型

然后我们定义一个Pydantic模型来表示状态，以及FastAPI中的应用对象：

```py
class EmailAssistantState(pydantic.BaseModel):
    app_id: str
    email_to_respond: Optional[str]
    response_instructions: Optional[str]
    questions: Optional[List[str]]
    answers: Optional[List[str]]
    drafts: List[str]
    feedback_history: List[str]
    final_draft: Optional[str]
    # This stores the next step, which tells the frontend which ones to call
    next_step: Literal[
        "process_input", "clarify_instructions", 
        "process_feedback", None]

    @staticmethod
    def from_app(app: Application):
        # implementation left out, call app.state and translate to 
        # pydantic model we can use `app.get_next_action()` to get 
        #the next step and return it to the user
        ...
```

注意，每个端点将返回相同的Pydantic模型！

# 端点

由于每个端点返回相同的内容（当前状态的表示以及要执行的下一步），它们看起来都是一样的。我们可以首先实现一个通用的`run_through`函数，它将推动我们的状态机前进，并返回状态。

```py
def run_through(
    project_id: str, 
    app_id: Optional[str], 
    inputs: Dict[str, Any]
) -> EmailAssistantState:
    email_assistant_app = get_application(project_id, app_id)
    email_assistant_app.run(
        halt_before=["clarify_instructions", "process_feedback"],
        halt_after=["final_result"],
        inputs=inputs,
    )
    return EmailAssistantState.from_app(email_assistant_app)
```

这代表了一个简单但强大的架构。我们可以继续调用这些端点，直到达到“终端”状态，在此时我们可以随时请求状态。如果我们决定添加更多的输入步骤，我们可以修改状态机并添加更多的输入步骤。我们不需要在应用中保持状态（所有状态都委托给Burr的持久化），因此我们可以轻松地从任何给定的点加载，允许用户在继续之前等待几秒钟、几分钟、几小时甚至几天。

由于前端只是根据当前状态和下一步渲染，它始终是正确的，用户总是可以从上次离开的地方继续。通过Burr的遥测功能，你可以调试任何与状态相关的问题，确保流畅的用户体验。

# 添加用户界面

现在我们有了一组端点，用户界面很简单。事实上，它几乎与API完全一致。我们不会深入探讨这一点，但总体思路是你将需要以下功能：

1.  渲染当前状态（显示历史记录，最新草稿）

1.  为下一步的输入操作包含一个表单（提供反馈，回答澄清问题）

1.  将结果发布到你的FastAPI端点，暂停等待响应，转到（1）

你可以在[这里](https://github.com/DAGWorks-Inc/burr/blob/main/telemetry/ui/src/examples/EmailAssistant.tsx)查看用户界面。以下是它运行时的一个示例：

![](../Images/3e645b6e63e200b656e18c6fce0a0cf0.png)

如果你下载了burr（``pip install “burr[start]” && burr``），并导航到[http://localhost:7241/demos/email-assistant](http://localhost:7241/demos/email-assistant)，你可以随意尝试。

请注意，有 *许多* 工具使得原型制作更简单/更容易，包括 [chainlit](https://docs.chainlit.io/get-started/overview)、[streamlit](https://streamlit.io/) 等等……我们构建的后端 API 也可以与这些工具进行交互。

# 额外功能

## 自定义持久化

虽然我们使用了简单的 SQLLite 持久化方式，但你可以使用 Burr 提供的其他持久化方法，或者实现自己的持久化方式，以匹配你的架构/数据库基础设施。为此，你需要实现 [BaseStatePersister](https://burr.dagworks.io/reference/persister/#burr.core.persistence.BaseStatePersister) 类，并将其与 ApplicationBuilder 一起使用，而不是我们上面使用的 SQLLite 持久化方式。

## 额外的监控/可视化

使用 Burr UI 进行监控并不是唯一的方法。你可以通过利用 [生命周期钩子](https://burr.dagworks.io/concepts/hooks/) 来集成自己的监控方式，允许你以自定义格式记录数据，并将其发送到比如 [datadog](http://datadog.com/)、[langsmith](https://www.langchain.com/langsmith) 或 [langfuse](http://langfuse.com/)。

此外，你还可以利用额外的监控功能来跟踪跨度/追踪，直接记录到 Burr UI 或上述任何提供商。查看可用钩子列表 [这里](https://burr.dagworks.io/reference/lifecycle/#hooksref)。

## 异步/流式处理

虽然为了简化，我们暴露的 API 是同步的，但 Burr 也支持异步执行。Burr 还支持流式响应，以便为那些想要提供更互动 UI/减少 *首次响应时间* 的用户提供支持。

# 那么它在实践中如何运作呢？

与任何 LLM 应用一样，整个提示是至关重要的。如果你能提供正确的指导，结果会比不提供指导时更好。就像你给人类下达指令一样，更多的指导总是更好。也就是说，如果你总是在修正某些方面，那么改变基本的提示可能是最好的解决方案。例如，使用 [单次或少次示例](https://neptune.ai/blog/zero-shot-and-few-shot-learning-with-llms#:~:text=%E2%80%9COne%2Dshot%E2%80%9D%20means%20we,examples%20%E2%80%93%20you%20get%20the%20gist.) 方法可能是一个不错的选择，帮助指导 LLM 明确你希望根据特定上下文看到的内容。

# 文章总结

在本文中，我们讨论了如何解决构建人机协作代理工作流的一些挑战。我们演示了如何使用 Burr 构建并运行一个状态机的电子邮件助手示例，并使用 FastAPI 在 Web 服务中运行 Burr。最后，我们展示了如何扩展我们在这里使用的工具，以满足各种常见的生产需求——例如，监控和存储。

## 额外资源

+   加入 Burr 的 [Discord](https://discord.gg/6Zy2DwP4f3)，寻求帮助或如果你有任何问题！

+   [Burr 的 Github 仓库](http://github.com/dagworks-inc/burr)（如果你喜欢它，我们很高兴收到你的 ⭐️）

+   [FastAPI 指南](https://fastapi.tiangolo.com/)

+   [GitHub 上的 Web 服务器技术深度解析](https://github.com/DAGWorks-Inc/burr/tree/main/examples/web-server)

+   [电子邮件助手的代码](https://github.com/DAGWorks-Inc/burr/tree/main/examples/email-assistant)
