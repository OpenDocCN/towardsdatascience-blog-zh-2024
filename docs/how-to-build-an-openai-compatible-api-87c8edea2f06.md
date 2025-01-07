# 如何构建一个 OpenAI 兼容的 API

> 原文：[https://towardsdatascience.com/how-to-build-an-openai-compatible-api-87c8edea2f06?source=collection_archive---------0-----------------------#2024-03-24](https://towardsdatascience.com/how-to-build-an-openai-compatible-api-87c8edea2f06?source=collection_archive---------0-----------------------#2024-03-24)

## 创建一个服务器来复制 OpenAI 的 Chat Completions API，使任何 LLM 都能与为 OpenAI API 编写的工具集成

[](https://medium.com/@saarb?source=post_page---byline--87c8edea2f06--------------------------------)[![Saar Berkovich](../Images/8a834597e8c6cce1b948f6aa17bfe8be.png)](https://medium.com/@saarb?source=post_page---byline--87c8edea2f06--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--87c8edea2f06--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--87c8edea2f06--------------------------------) [Saar Berkovich](https://medium.com/@saarb?source=post_page---byline--87c8edea2f06--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--87c8edea2f06--------------------------------) ·阅读时长 6 分钟·2024 年 3 月 24 日

--

![](../Images/3678fe82d7cec7940e7288da517234fa.png)

图片由作者使用 OpenAI DALL-E 生成

现在是 2024 年初，生成型 AI 市场由 [OpenAI](https://iot-analytics.com/leading-generative-ai-companies/) 主导。原因很简单——他们具有先发优势，是第一个提供易用 API 的 LLM 提供商，而且他们提供的 GPT-4 可能是迄今为止最强大的 LLM。鉴于此，各种工具的开发者（如 [代理程序](https://docs.agpt.co/autogpt/)、[个人助手](https://github.com/QuivrHQ/quivr)、[编码扩展](https://github.com/jupyterlab/jupyter-ai)）纷纷转向 OpenAI 以满足他们的 LLM 需求。

虽然有许多理由让你使用 OpenAI 的 GPT 来推动你的生成型 AI 创作，但也有许多理由选择其他替代方案。有时候，使用 OpenAI 可能不够具有成本效益，而有时候你的数据隐私政策可能禁止你使用 OpenAI，或者你可能在托管一个开源的 LLM（或者你自己的 LLM）。

OpenAI 的市场主导地位意味着许多你可能想要使用的工具仅支持 OpenAI API。像 OpenAI、Anthropic 和 Google 这样的生成型 AI 和 LLM 提供商似乎在创建不同的 API 模式（可能是故意的），这增加了开发者为支持所有这些平台而需做的额外工作。

所以，作为一个快速的周末项目，我决定实现一个兼容 OpenAI API 规范的 Python [FastAPI](https://fastapi.tiangolo.com/) 服务器，目的是让你可以将几乎任何你喜欢的 LLM（无论是像 Anthropic 的 Claude 这样的托管模型，还是自托管的模型）包装起来，以模仿 OpenAI API。幸运的是，OpenAI API 规范中有一个 `base_url` 参数，可以将其设置为指向你的服务器，而不是 OpenAI 的服务器，大多数上述工具的开发者允许你根据需要设置这个参数。

为了实现这一点，我参照了 OpenAI 的聊天 API 参考文档，公开可用的[链接在此](https://platform.openai.com/docs/api-reference/chat)，并在[ vLLM](https://github.com/vllm-project/vllm)的代码帮助下进行了一些修改。vLLM 是一个 Apache-2.0 许可证下的推理服务器，支持 LLMs，同时兼容 OpenAI API。

## 游戏计划

我们将构建一个模拟的 API，模仿 OpenAI 的聊天完成 API（`/v1/chat/completions`）的工作方式。尽管这个实现是用 Python 和 FastAPI 编写的，我将其保持得非常简单，以便能够轻松迁移到像 TypeScript 或 Go 这样的其他现代编程语言。我们将使用 Python 官方的[OpenAI 客户端库](https://github.com/openai/openai-python)进行测试——我们的想法是，如果我们能让库认为我们的服务器是 OpenAI 的服务器，那么任何使用该库的程序也会认为是同样的。

# 第一步 — 聊天完成 API，非流式处理

我们将从实现非流式部分开始。首先建模我们的请求：

```py
from typing import List, Optional

from pydantic import BaseModel

class ChatMessage(BaseModel):
    role: str
    content: str

class ChatCompletionRequest(BaseModel):
    model: str = "mock-gpt-model"
    messages: List[ChatMessage]
    max_tokens: Optional[int] = 512
    temperature: Optional[float] = 0.1
    stream: Optional[bool] = False
```

[PyDantic](https://docs.pydantic.dev/latest/) 模型代表了来自客户端的请求，旨在复制 API 参考。为了简洁起见，这个模型没有实现完整的规范，而是实现了使其正常工作的基本部分。如果你缺少一个属于[API 规范](https://platform.openai.com/docs/api-reference/chat)的参数（比如 `top_p`），你只需将其添加到模型中。

`ChatCompletionRequest` 模型化了 OpenAI 在请求中使用的参数。聊天 API 规范要求指定一个 `ChatMessage` 列表（类似于聊天历史，通常由客户端负责保持并在每次请求时反馈）。每个聊天消息有一个 `role` 属性（通常是 `system`、`assistant` 或 `user`）和一个 `content` 属性，包含实际的消息文本。

接下来，我们将编写 FastAPI 聊天完成端点：

```py
import time

from fastapi import FastAPI

app = FastAPI(title="OpenAI-compatible API")

@app.post("/chat/completions")
async def chat_completions(request: ChatCompletionRequest):

    if request.messages and request.messages[0].role == 'user':
      resp_content = "As a mock AI Assitant, I can only echo your last message:" + request.messages[-1].content
    else:
      resp_content = "As a mock AI Assitant, I can only echo your last message, but there were no messages!"

    return {
        "id": "1337",
        "object": "chat.completion",
        "created": time.time(),
        "model": request.model,
        "choices": [{
            "message": ChatMessage(role="assistant", content=resp_content)
        }]
    }
```

就这么简单。

## 测试我们的实现

假设这两个代码块位于名为 `main.py` 的文件中，我们将在选择的环境中安装两个 Python 库（最好创建一个新的环境）：`pip install fastapi openai`，并通过终端启动服务器：

```py
uvicorn main:app
```

使用另一个终端（或在后台启动服务器），我们将打开一个 Python 控制台，并复制粘贴以下代码，这些代码直接来自[OpenAI 的 Python 客户端参考](https://github.com/openai/openai-python#Usage)：

```py
from openai import OpenAI

# init client and connect to localhost server
client = OpenAI(
    api_key="fake-api-key",
    base_url="http://localhost:8000" # change the default port if needed
)

# call API
chat_completion = client.chat.completions.create(
    messages=[
        {
            "role": "user",
            "content": "Say this is a test",
        }
    ],
    model="gpt-1337-turbo-pro-max",
)

# print the top "choice" 
print(chat_completion.choices[0].message.content)
```

如果你一切操作正确，服务器的响应应该会正确打印。检查`chat_completion`对象也很有价值，可以确认所有相关属性是否与我们服务器发送的一致。你应该能看到类似下面的内容：

![](../Images/b41ac9b9749709c7180ac0bf9d59a281.png)

由作者编写的代码，使用[Carbon](https://carbon.now.sh/)格式化

# 升级——支持流式传输

由于LLM生成通常较慢（计算开销大），将生成的内容流式传输回客户端是值得的，这样用户可以在生成过程中看到响应，而不必等到它完全生成完毕。如果你记得，我们给`ChatCompletionRequest`添加了一个布尔型的`stream`属性——这让客户端可以请求将数据流式传输回去，而不是一次性发送。

这使得事情变得稍微复杂了一些。我们将创建一个[生成器函数](https://wiki.python.org/moin/Generators)来包装我们的模拟响应（在实际场景中，我们将需要一个连接到LLM生成的生成器）

```py
import asyncio
import json

async def _resp_async_generator(text_resp: str):
    # let's pretend every word is a token and return it over time
    tokens = text_resp.split(" ")

    for i, token in enumerate(tokens):
        chunk = {
            "id": i,
            "object": "chat.completion.chunk",
            "created": time.time(),
            "model": "blah",
            "choices": [{"delta": {"content": token + " "}}],
        }
        yield f"data: {json.dumps(chunk)}\n\n"
        await asyncio.sleep(1)
    yield "data: [DONE]\n\n"
```

现在，我们将修改原始的端点，当`stream==True`时返回一个StreamingResponse

```py
import time

from starlette.responses import StreamingResponse

app = FastAPI(title="OpenAI-compatible API")

@app.post("/chat/completions")
async def chat_completions(request: ChatCompletionRequest):

    if request.messages:
      resp_content = "As a mock AI Assitant, I can only echo your last message:" + request.messages[-1].content
    else:
      resp_content = "As a mock AI Assitant, I can only echo your last message, but there wasn't one!"
    if request.stream:
      return StreamingResponse(_resp_async_generator(resp_content), media_type="application/x-ndjson")

    return {
        "id": "1337",
        "object": "chat.completion",
        "created": time.time(),
        "model": request.model,
        "choices": [{
            "message": ChatMessage(role="assistant", content=resp_content)        }]
    }
```

## 测试流式传输实现

重启uvicorn服务器后，我们将打开Python控制台并输入这段代码（同样是来自OpenAI的文档）

```py
from openai import OpenAI

# init client and connect to localhost server
client = OpenAI(
    api_key="fake-api-key",
    base_url="http://localhost:8000" # change the default port if needed
)

stream = client.chat.completions.create(
    model="mock-gpt-model",
    messages=[{"role": "user", "content": "Say this is a test"}],
    stream=True,
)
for chunk in stream:
    print(chunk.choices[0].delta.content or "")
```

你应该看到服务器响应中的每个单词都被慢慢打印出来，模拟标记生成。我们可以检查最后一个`chunk`对象，看到类似下面的内容：

![](../Images/ad32225e29fca2ff7ea30226264ff67f.png)

由作者编写的代码，使用[Carbon](https://carbon.now.sh/)格式化

## 将所有内容整合起来

最后，在下面的代码片段中，你可以看到服务器的完整代码。

# 最后的注意事项

+   这里我们可以做很多其他有趣的事情，比如支持其他请求参数，和其他OpenAI的抽象，如函数调用和助手API。

+   LLM API 缺乏标准化使得切换提供商变得困难，既对于公司也对于开发LLM封装包的开发者而言。在没有任何标准的情况下，我在这里采取的做法是将LLM抽象化，基于最大且最成熟的API的规格进行封装。
