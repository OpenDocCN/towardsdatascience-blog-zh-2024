# 如何使用Burr、FastAPI和React构建流媒体代理

> 原文：[https://towardsdatascience.com/how-to-build-a-streaming-agent-with-burr-fastapi-and-react-e2459ef527a8?source=collection_archive---------3-----------------------#2024-07-25](https://towardsdatascience.com/how-to-build-a-streaming-agent-with-burr-fastapi-and-react-e2459ef527a8?source=collection_archive---------3-----------------------#2024-07-25)

## 如何利用开源工具构建一个简单的代理聊天机器人，使用流媒体技术。

[](https://medium.com/@stefan.krawczyk?source=post_page---byline--e2459ef527a8--------------------------------)[![Stefan Krawczyk](../Images/150405abaad9590e1dc2589168ed2fa3.png)](https://medium.com/@stefan.krawczyk?source=post_page---byline--e2459ef527a8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2459ef527a8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2459ef527a8--------------------------------) [Stefan Krawczyk](https://medium.com/@stefan.krawczyk?source=post_page---byline--e2459ef527a8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2459ef527a8--------------------------------) ·阅读时间12分钟·2024年7月25日

--

![](../Images/f76e421b3ba109b434f51c1d43eb0ba0.png)

我们的代理应用模型。我们将展示如何通过流媒体构建这个模型，让你能够创造出出色的用户体验。图片由作者提供。

在这篇文章中，我们将介绍如何构建一个代理聊天机器人，通过流媒体将响应传递给用户，利用[Burr](https://github.com/dagworks-inc/burr)（我也是作者）的流媒体功能，[FastAPI](https://fastapi.tiangolo.com/)的[StreamingResponse](https://fastapi.tiangolo.com/advanced/custom-response/?h=streamingresponse#using-streamingresponse-with-file-like-objects)以及由[React](https://react.dev/)查询的服务器推送事件（SSEs）。这些都是开源工具。本文面向那些希望了解如何在Python中使用流媒体，以及如何为他们的代理/应用程序添加交互性的人。虽然我们使用的工具相对具体，但所学的内容应该适用于广泛的流媒体响应实现方式。

首先，我们将讨论流媒体的重要性。接着，我们将介绍我们使用的开源工具。我们将通过一个示例讲解，并提供你可以使用的代码，帮助你入门，之后分享更多资源和替代实现方案。

你可以通过[Burr + FastAPI 代码](https://github.com/DAGWorks-Inc/burr/tree/main/examples/streaming-fastapi)和前端代码[这里](https://github.com/DAGWorks-Inc/burr/blob/main/telemetry/ui/src/examples/StreamingChatbot.tsx)跟着一起操作。你也可以通过运行 `pip install “burr[start]” && burr`，然后访问 localhost:7241/demos/streaming-chatbot 来运行这个示例（你需要一个 OPENAI_API_KEY 环境变量）；浏览器会自动打开，点击左侧的 demos/streaming-chatbot 即可。注意，这个示例要求 `burr>=0.23.0`。

# 为什么要使用流式传输？

虽然通过网络流式传输媒体是[90年代](https://en.wikipedia.org/wiki/Streaming_media#:~:text=Online%20streaming%20was%20initially%20popularised,being%20offered%20since%20the%202010s.)的技术，并且如今已无处不在（视频游戏、流媒体电视、音乐等），但最近生成式 AI 应用的兴起使得逐字流式传输文本的兴趣重新回到了人们的视野。

LLMs 是一种有趣的技术（甚至可能是有用的），但运行相对较慢，用户不喜欢等待。幸运的是，可以通过流式传输结果，让用户在 LLM 的响应生成过程中实时看到结果。此外，鉴于 LLM 的普遍机械且呆板的特性，流式传输可以使其看起来更具互动性，几乎就像它们在思考一样。

一个正确的实现将允许跨多个服务边界进行流式通信，使得中间代理能够在将数据呈现给用户时增强/存储流式数据。

![](../Images/9f99df7ca53e3a575dacdc8d977ef642.png)

一个简单的聊天机器人架构展示。图片由作者提供。

虽然这些都不是火箭科学，但使网页开发变得简单且高度标准化的工具（如 OpenAPI / FastAPI / React + 朋友们等）在流式传输支持上存在不同程度的差异，这意味着你常常会遇到与自己习惯的工具不同的选择。流式传输通常是框架设计中的事后考虑，导致一些限制，直到你开发到一半时才会发现。

让我们先了解一些将用来实现上面技术栈的工具，然后一起看个例子。

# 开源工具

我们用来构建这个的工具相互解耦得很好——如果你愿意，可以互换相似的工具，依然可以应用相同的教训/代码。

# Burr

[Burr](https://github.com/DAGWorks-Inc/burr/) 是一个轻量级的 Python 库，用于构建作为状态机的应用程序。你可以通过一系列动作（这些可以是装饰过的函数或对象）构建你的应用程序，这些动作声明来自状态的输入，以及来自用户的输入。这些定义了自定义逻辑（可以委托给任何框架），以及如何更新状态的指令。状态是不可变的，这允许你在任何时候检查它。Burr 处理编排、监控、持久化等工作。

```py
@action(reads=["count"], writes=["count"])
def counter(state: State) -> State:
    return state.update(counter=state.get("count", 0) +1) 
```

你将Burr操作作为应用程序的一部分运行——这允许你将它们通过一系列（可选的）条件转换串联起来，从一个动作到另一个动作。

```py
from burr.core import ApplicationBuilder, default, expr
app = (
    ApplicationBuilder()
    .with_actions(
        count=count, 
        done=done # implementation left out above
    ).with_transitions(
        ("counter", "counter", expr("count < 10")), # Keep counting if the counter is < 10
        ("counter", "done", default) # Otherwise, we're done
    ).with_state(count=0)
    .with_entrypoint("counter") # we have to start somewhere
    .build()
)
```

Burr配有一个用户界面，可以进行监控/遥测，并且提供挂钩功能来持久化状态/在执行过程中执行任意代码。

你可以将其视为一个流程图，即图形/状态机：

![](../Images/f987bc5eedaa49d7f5a17e860b10a6e0.png)

Burr免费为你提供这张图片。图片由作者提供。

并使用本地遥测调试器进行监控：

![](../Images/daf833de5eef0cfd6bd0b09cdb576096.png)

操作系统遥测UI在任何给定时刻都会告诉你应用程序的状态。图像由作者提供。

虽然上述示例是一个简单的插图，但Burr通常用于代理（如本示例中所示）、RAG应用程序和人类环中AI接口。请参阅仓库中的[示例](https://github.com/DAGWorks-Inc/burr/tree/main/examples)以获取（更详尽的）使用案例。稍后我们将详细介绍流式传输和一些更强大的功能。

# FastAPI

[FastAPI](https://fastapi.tiangolo.com/)是一个框架，可以让你将Python函数暴露为REST API。它有一个简单的接口——你编写函数然后为其添加装饰器，最后运行脚本——它会变成一个带有自文档化端点的服务器，通过[OpenAPI](https://www.openapis.org/)进行描述。

```py
@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: Union[str, None] = None):
    return {"item_id": item_id, "q": q}
```

FastAPI提供了许多好处。它是原生支持异步的，通过OpenAPI提供文档，并且易于在任何云提供商上部署。它与基础设施无关，并且通常可以横向扩展（只要考虑到状态管理）。欲了解更多信息，请参见[此页面](https://fastapi.tiangolo.com/deployment/cloud/?h=deploy)。

# React

React无需介绍——它是一个极受欢迎的工具，支撑了互联网的许多部分。即便是最近流行的工具（如next.js/remix）也建立在它之上。欲了解更多信息，请访问[react.dev](http://react.dev/)。我们将使用React、[typescript](https://www.typescriptlang.org/)和[tailwind](https://tailwindcss.com/)，但你也可以替换为自己喜欢的前端工具，并且大部分内容都可以复用。

# 构建一个简单的代理型聊天机器人

让我们构建一个简单的*代理型*聊天机器人——它将是*代理型*的，因为它实际上会进行两次LLM调用：

1.  一个调用，用于确定要查询的模型。我们的模型将有几个“模式”——生成诗歌、回答问题等……

1.  一个调用，指向实际的模型（在本例中为提示+模型组合）

使用OpenAI API时，这是一个玩具示例——他们的模型是令人印象深刻的多面手。也就是说，这种工具委托的模式在各种AI系统中都可以看到，且这个示例可以干净地外推。

# 在Burr中建模代理

## 作为状态机建模

为了利用Burr，我们将我们的代理型应用建模为一个状态机。基本的逻辑流程如下：

![](../Images/f76e421b3ba109b434f51c1d43eb0ba0.png)

我们从用户输入的提示开始（顶部）。然后检查安全性，如果不安全，我们将进入“非安全”响应。否则，我们根据 *mode* 状态字段的值决定模式，并进行切换。每个操作都返回一个流式响应。流式传输完成后，它会返回到提示并等待另一个用户输入……图像由作者提供。

为了在 Burr 中建模这一过程，我们首先将创建相应的操作，使用[流式 API](https://burr.dagworks.io/concepts/streaming-actions/)。然后我们将它们组合成一个[应用程序](https://burr.dagworks.io/concepts/state-machine/)。

## 流式操作

在 Burr 中，操作可以同时使用同步和异步 API。在这个例子中，我们将使用[异步](https://docs.python.org/3/library/asyncio.html)。Burr 中的流式函数也可以与非流式操作混合使用，但为了简化，我们将一切都实现为流式操作。因此，无论是从 OpenAPI 流式传输（它有自己的[异步流式接口](https://www.asyncapi.com/tools/generator)），还是返回一个固定的 *抱歉，我无法回答这个问题* 的响应，它仍然会作为生成器实现。

对于那些不熟悉的人，[生成器](https://wiki.python.org/moin/Generators)是 Python 中的一种结构，可以高效地、懒惰地对一系列值进行评估。它们通过 `yield` 关键字创建，`yield` 会将控制权从函数交还给调用者，直到需要下一个项目时才会继续执行。[异步生成器](https://peps.python.org/pep-0525/)的功能类似，不过它们在 `yield` 时还会将事件循环的控制权交出。阅读更多关于[同步生成器](https://wiki.python.org/moin/Generators)和[异步生成器](https://peps.python.org/pep-0525/)的内容。

Burr 中的流式操作作为生成器实现，生成包含元组的流，其中包括：

1.  中间结果（在此情况下，消息中的增量标记）

1.  如果状态更新已完成，则为最终状态更新，若仍在生成中，则为 None

因此，最终的 yield 将表明流已完成，并输出最终结果，以便稍后进行存储/调试。一个基本的响应，代理到 OpenAI 并进行一些自定义提示处理，长这样：

```py
@streaming_action(reads=["prompt", "chat_history", "mode"], writes=["response"])
async def chat_response(
    state: State, prepend_prompt: str, model: str = "gpt-3.5-turbo"
) -> AsyncGenerator[Tuple[dict, Optional[State]], None]:
    """A simple proxy.

    This massages the chat history to pass the context to OpenAI, 
    streams the result back, and finally yields the completed result 
    with the state update.
    """
    client = _get_openai_client()
    # code skipped that prepends a custom prompt and formats chat history
    chat_history_for_openai = _format_chat_history(
        state["chat_history"], 
        prepend_final_promprt=prepend_prompt)
    result = await client.chat.completions.create(
        model=model, messages=chat_history_api_format, stream=True
    )
    buffer = []

    async for chunk in result:
        chunk_str = chunk.choices[0].delta.content
        if chunk_str is None:
            continue
        buffer.append(chunk_str)
        yield {"delta": chunk_str}, None

    result = {
        "response": {"content": "".join(buffer), "type": "text", "role": "assistant"},
    }
    yield result, state.update(**result).append(chat_history=result["response"])
```

在这个示例中，我们还有一些其他的流式操作——这些操作将代表“终端”操作——即在状态机完成时触发工作流暂停的操作。

## 构建应用程序

为了构建应用程序，我们首先将构建一个图形。我们将使用 Burr 的[图形 API](https://burr.dagworks.io/concepts/state-machine/#graph-api)，使我们能够将图形的形状与其他应用程序关注点解耦。在 Web 服务中，图形 API 是一种非常简洁的方式来表达状态机逻辑。你可以全局构建一次，然后在每个单独的应用实例中重复使用它。图形构建器是这样的——请注意它引用了上面提到的 *chat_response* 函数：

```py
# Constructing a graph from actions (labeled by kwargs) and 
# transitions (conditional or default).
graph = (
    GraphBuilder()
    .with_actions(
        prompt=process_prompt,
        check_safety=check_safety,
        decide_mode=choose_mode,
        generate_code=chat_response.bind(
            prepend_prompt="Please respond with *only* code and no other text" 
                "(at all) to the following",
        ),
        # more left out for brevity
    )
    .with_transitions(
        ("prompt", "check_safety", default),
        ("check_safety", "decide_mode", when(safe=True)),
        ("check_safety", "unsafe_response", default),
        ("decide_mode", "generate_code", when(mode="generate_code")),
        # more left out for brevity
    )
    .build()
)
```

最后，我们可以将这些内容组合到一个应用程序中——它暴露了正确的执行方法，以便服务器与之交互：

```py
# Here we couple more application concerns (telemetry, tracking, etc…).
app = ApplicationBuilder()
  .with_entrypoint("prompt")
  .with_state(chat_history=[])
  .with_graph(graph)
  .with_tracker(project="demo_chatbot_streaming")
  .with_identifiers(app_id=app_id)
  .build()
)
```

当我们想要运行它时，我们可以调用[astream_results](https://burr.dagworks.io/concepts/streaming-actions/#usage)。这个方法接受一组*停止条件*，并返回一个[AsyncStreamingResultContainer](https://burr.dagworks.io/reference/actions/#burr.core.action.AsyncStreamingResultContainer)(一个缓存结果的生成器，并确保调用 Burr 跟踪)，以及触发停止的动作。

```py
# Running the application as you would to test, 
# (in a jupyter notebook, for instance).
action, streaming_container = await app.astream_result(
    halt_after=["generate_code", "unsafe_response", ...], # terminal actions
    inputs={
      "prompt": "Please generate a limerick about Alexander Hamilton and Aaron Burr"
    }
)

async for item in streaming_container:
    print(item['delta'], end="")
```

# 在 Web 服务器中暴露

现在我们有了 Burr 应用程序，我们希望通过使用服务器推送事件 (SSEs) 将其与 FastAPI 的[流式响应 API](https://fastapi.tiangolo.com/advanced/custom-response/#streamingresponse)集成。虽然我们不会深入讨论 SSEs，简而言之，它们作为 WebSocket 的单向（服务器 → 客户端）版本工作。你可以通过文末的链接了解更多信息。

要在 FastAPI 中使用这些内容，我们声明一个端点作为一个返回 StreamingResponse 的函数——StreamingResponse 是一个包装生成器的类。标准做法是以一种特殊的格式提供流式响应，“data: <contents> \n\n”。关于为什么这么做，可以[点击这里](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)了解更多。虽然这个标准主要是针对 [EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) API（我们将绕过它，选择使用 fetch 和[getReader()](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream/getReader)），但我们将保持这一格式以遵循标准（这样任何使用 EventSource API 的人都能复用这段代码）。

我们已经单独实现了 `_get_application`，这是一个通过 ID 获取/加载应用程序的工具函数。

该功能将是一个 POST 端点，因为我们正在向服务器添加数据，尽管也可以轻松改为 PUT。

```py
@app.post("/response/{project_id}/{app_id}", response_class=StreamingResponse)
async def chat_response(project_id: str, app_id: str, prompt: PromptInput) -> StreamingResponse:
    """A simple API that wraps our Burr application."""
    burr_app = _get_application(project_id, app_id)
    chat_history = burr_app.state.get("chat_history", [])
    action, streaming_container = await burr_app.astream_result(
        halt_after=chat_application.TERMINAL_ACTIONS, inputs=dict(prompt=prompt.prompt)
    )

    async def sse_generator():
        yield f"data: {json.dumps({'type': 'chat_history', 'value': chat_history})}\n\n"

        async for item in streaming_container:
            yield f"data: {json.dumps({'type': 'delta', 'value': item['delta']})} \n\n"

    return StreamingResponse(sse_generator())
```

请注意，我们在函数内部定义了一个生成器，它包装了 Burr 结果并将其转换为适合 SSE 的输出。这使我们能够对结果施加一些结构，后端将使用这些结构。不幸的是，我们将不得不自行解析它，因为 fastAPI 不支持对 StreamingResponse 进行严格的类型定义。

此外，我们实际上会在执行前，最开始就输出整个状态。虽然这并非严格必要（我们也可以有一个单独的 API 用于聊天历史），但它会让渲染更加容易。

要测试这一点，你可以使用 requests 库的[Response.iter_lines](https://requests.readthedocs.io/en/latest/user/advanced/#body-content-workflow) API。

# 构建用户界面

现在我们已经有了服务器、状态机和 LLM，让我们让它看起来更好！这就是一切整合的地方。虽然你可以下载并玩转完整的代码，[示例](https://github.com/DAGWorks-Inc/burr/tree/main/examples/streaming-fastapi)中有全部内容，但我们将专注于在点击“发送”时查询 API 的函数。

![](../Images/a31aeb84cbc323ece54bc65f05a68bad.png)

这是 UI 的样子。你可以通过 Burr 附带的打包 Telemetry UI 运行这个。图像来自作者。

首先，让我们使用 fetch 查询我们的 API（显然，你需要根据你的端点调整，当前我们将所有的 /api 调用代理到另一个服务器）：

```py
// A simple fetch call with getReader()
const response = await fetch(
      `/api/v0/streaming_chatbot/response/${props.projectId}/${props.appId}`,
      {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ prompt: currentPrompt })
      }
    );
const reader = response.body?.getReader();
```

这看起来像一个普通的 API 调用，利用了 TypeScript 的 [async API](https://blog.logrocket.com/async-await-typescript/)。它提取了一个 *reader* 对象，帮助我们随着结果的到来进行流式传输。

让我们定义一些数据类型，以便利用我们上面创建的结构。除了 `ChatItem` 数据类型（这是通过 [openapi-typescript-codegen](https://www.npmjs.com/package/openapi-typescript-codegen) 生成的），我们还将定义两个类，它们对应服务器返回的数据类型。

```py
// Datatypes on the frontend. 
// The contract is loose, as nothing in the framework encodes it
type Event = {
  type: 'delta' | 'chat_history';
};

type ChatMessageEvent = Event & {
  value: string;
};

type ChatHistoryEvent = Event & {
  value: ChatItem[];
};
```

接下来，我们将遍历读取器并进行解析。这假设在 React 中存在以下状态变量：

+   `setCurrentResponse`/`currentResponse`

+   `setDisplayedChatHistory`

我们通过“data:”进行拆分，然后循环遍历拆分结果，并根据事件类型进行解析/反应。

```py
// Loop through, continually getting the stream. 
// For each item, parse it as our desired datatype and react appropriately.
while (true) {
    const result = await reader.read();
    if (result.done) {
      break;
    }
    const message = decoder.decode(result.value, { stream: true });
    message
      .split('data: ')
      .slice(1)
      .forEach((item) => {
        const event: Event = JSON.parse(item);
        if (event.type === 'chat_history') {
          const chatMessageEvent = event as ChatHistoryEvent;
          setDisplayedChatHistory(chatMessageEvent.value);
        }
        if (event.type === 'delta') {
          const chatMessageEvent = event as ChatMessageEvent;
          chatResponse += chatMessageEvent.value;
          setCurrentResponse(chatResponse);
        }
      });
}
```

我们省略了一些清理/错误处理代码（例如在请求之前/之后清除、初始化状态变量、处理失败等）——你可以在示例中看到更多内容。

最后，我们可以渲染它（请注意，这指的是在上面的代码之外设置/取消设置的额外状态变量，以及一个简单显示聊天消息和相应图标的 ChatMessage React 组件）。

```py
<!-- More to illustrates the example -->
<div className="flex-1 overflow-y-auto p-4 hide-scrollbar" id={VIEW_END_ID}>
  {displayedChatHistory.map((message, i) => (
    <ChatMessage
      message={message}
      key={i}
    />
  ))}
  {isChatWaiting && (
    <ChatMessage
      message={{
        role: ChatItem.role.USER,
        content: currentPrompt,
        type: ChatItem.type.TEXT
      }}
    />
  )}
  {isChatWaiting && (
    <ChatMessage
      message={{
        content: currentResponse,
        type: ChatItem.type.TEXT,
        role: ChatItem.role.ASSISTANT
      }}
    />
  )}
</div>
<!-- Note: We've left out the isChatWaiting and currentPrompt state fields above, 
 see StreamingChatbot.tsx for the full implementation. --> 
```

我们最终完成了整个应用程序！要查看所有的[代码，点击这里](https://github.com/DAGWorks-Inc/burr/tree/main/examples/streaming-fastapi)。

# 替代的 SSE 工具

请注意，我们上面展示的仅仅是使用 FastAPI/react/Burr 进行流式传输的一种方法。你还可以使用其他许多工具，包括：

+   [EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) API——标准但仅限于 get/ 请求

+   [FetchEventSource](https://github.com/Azure/fetch-event-source) API（看起来不再维护，但构建得很好）

以及其他一些很棒的博客文章（我看这些文章来入门）。这些文章也能帮助你更好地理解架构。

+   [使用 FastAPI 流式传输 OpenAI 并通过 React.js 消费它](https://medium.com/@hxu296/serving-openai-stream-with-fastapi-and-consuming-with-react-js-part-1-8d482eb89702)

+   [使用 FastAPI 流式传输](https://www.vidavolta.io/streaming-with-fastapi/)

# 总结

在这篇文章中，我们讲解了很多内容——我们介绍了 Burr、FastAPI 和 React，讨论了如何使用 OpenAI API 构建一个流式 *代理* 聊天机器人，构建了整个堆栈，并进行了数据流式传输！虽然你可能不会使用所有的技术，但每个单独的部分应该都能独立工作。

要下载并尝试这个示例，你可以运行：

```py
pip install "burr[start]"
burr # will open up in a new window
```

请注意，您需要一个来自[OpenAI的API密钥](http://platform.openai.com/)来运行这个特定的示例。您可以在[Burr + FastAPI代码](https://github.com/DAGWorks-Inc/hamilton/tree/main/examples/streaming-fastapi)中找到相关代码，在[前端代码](https://github.com/DAGWorks-Inc/hamilton/tree/main/telemetry/ui/src/examples/StreamingChatbot.tsx)中也可以找到。

# 额外资源

+   [Burr的Github仓库](http://github.com/dagworks-inc/burr)（如果您喜欢看到的内容，请给我们点个星！）

+   [FastAPI指南](https://fastapi.tiangolo.com/)

+   [示例 + README](https://github.com/DAGWorks-Inc/burr/tree/main/examples/streaming-fastapi)
