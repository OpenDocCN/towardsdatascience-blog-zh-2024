# 如何在Unity中使用LLM

> 原文：[https://towardsdatascience.com/how-to-use-llms-in-unity-308c9c0f637c?source=collection_archive---------3-----------------------#2024-01-09](https://towardsdatascience.com/how-to-use-llms-in-unity-308c9c0f637c?source=collection_archive---------3-----------------------#2024-01-09)

## 在Unity引擎中集成大语言模型与LLMUnity

[](https://benuix.medium.com/?source=post_page---byline--308c9c0f637c--------------------------------)[![Antonis Makropoulos](../Images/5bdd3826eeb31dfb6d8e6fc393b24d8b.png)](https://benuix.medium.com/?source=post_page---byline--308c9c0f637c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--308c9c0f637c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--308c9c0f637c--------------------------------) [Antonis Makropoulos](https://benuix.medium.com/?source=post_page---byline--308c9c0f637c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--308c9c0f637c--------------------------------) ·8分钟阅读·2024年1月9日

--

![](../Images/0df94a0bf1fe6c6416fbd5c27060b0e9.png)

图片改编自[Life is Strange Steam页面](https://store.steampowered.com/app/319630/Life_is_Strange__Episode_1/)，包含了游戏中的对话。

在本文中，我们将展示如何在Unity引擎中使用LLM（大语言模型）🎮。我们将使用[LLMUnity](https://github.com/undreamai/LLMUnity)包，并展示如何用几行代码设置对话交互的示例！

*免责声明：我是LLMUnity的作者。如果你有任何评论或建议，请通过打开一个* [*GitHub issue*](https://github.com/undreamai/LLMUnity/issues) 🤗！

# 为什么在游戏中使用LLM？

> *目前几乎所有PC游戏的互动都基于多选对话。这是自PC游戏早期时代以来建立的一种非常原始的互动方式。LLM在游戏中的应用可以构建更具沉浸感的体验，因为它们允许* 与PC游戏元素或角色（NPC）进行智能互动。

以《上古卷轴5：天际》为例，这是目前最成功的RPG之一。当你第一次遇到Lydia时，这位NPC可能会成为你游戏中的重要伙伴，你会有三种可能的对话选项。如果你想了解更多关于她的背景或讨论其他话题怎么办？

![](../Images/8d1d4f4241ff69ccda616049d069b88a.png)

与《上古卷轴5：天际》中的NPC Lydia进行互动。截图来自游戏。

这正是LLM能够大显身手的地方。你可以描述AI的角色以及他们对世界的理解（这些内容通常是游戏叙事的一部分），并且它们可以提升对话的质量。

![](../Images/36cf9f3201e12cbde430204cacd2785b.png)

与Lydia的对话互动示例

# 那么ChatGPT呢？

访问此页面的大多数人应该对 OpenAI 发布的 [ChatGPT](https://chat.openai.com/) 有一定了解，并且见证了与 LLM 的互动是多么自然和强大。那么，为什么不直接在游戏中使用 ChatGPT 呢？

+   大规模使用 ChatGPT **费用** 💸。每次交互的成本非常小，但当规模扩大时，例如成千上万的用户和每个用户上千次交互时，成本不可忽视。

+   它会创建一个 **依赖** 🔗。如果出于任何原因 ChatGPT 停止工作，或者价格上涨而开发者负担不起，那么游戏就会崩溃。

+   开源 LLM 的 **准确性** 与 ChatGPT 相当 🏎️。虽然我还没找到一个标准化的基准来证明这一点，但 Meta（Llama）和 Mistral 发布的模型在质量上似乎与 ChatGPT 的准确性相似。

+   LLM 的 **体积** 越来越小 🤏。最近的 Mistral 7B 超越了 Llama2 13B，并在许多基准测试中超越了 Llama 34B。量化方法进一步推动了这一极限，将模型体积缩小到可以在任何最近的 PC 和 GPU 上使用的程度。使用 Q4_K_M 方法量化的 Mistral 7B ([模型](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF)，[量化](https://github.com/ggerganov/llama.cpp/pull/1684)) 只需最多 7GB 的 RAM 即可运行！

# 欢迎使用 LLMUnity！

> [LLMUnity](https://github.com/undreamai/LLMUnity) 是一个允许在 Unity 引擎中运行和分发 LLM 模型的包。

![](../Images/3ea583c2f9450e8db48e31bca9d5ed9b.png)

它基于强大的 [llama.cpp](https://github.com/ggerganov/llama.cpp) 库构建，使得使用 LLM 不需要外部软件依赖，同时利用 [llamafile](https://github.com/Mozilla-Ocho/llamafile) 以跨平台方式部署 llama.cpp。

LLMUnity 提供以下功能：

+   💻 跨平台！支持 Windows、Linux 和 macOS

+   🏠 本地运行无需互联网连接，但也支持远程服务器

+   ⚡ 在 CPU 和 GPU 上快速推理

+   🤗 支持主要的 LLM 模型

+   🔧 易于设置，一行代码即可调用

+   💰 个人和商业用途均可免费使用

# 工作原理

![](../Images/a810f2e39d21e0c9a20835e5ed5232a0.png)

LLMUnity 架构

LLMUnity 在后台使用 [llama.cpp](https://github.com/ggerganov/llama.cpp) 服务器。该服务器接收 POST 请求，在 LLM 上运行推理并返回回复。服务器通过 [llamafile](https://github.com/Mozilla-Ocho/llamafile) 编译为可执行文件，可以在不同操作系统（Windows、Linux、MacOS）上使用，基于 [cosmopolitan](https://github.com/jart/cosmopolitan) 库。

LLMUnity 实现了一个客户端，发送 POST 请求并将结果传递给你的 Unity 应用程序。

# 如何设置

可以通过 GitHub URL 作为自定义包安装 LLMUnity，或者作为 Unity 资产（待资产商店批准）安装。详细说明请参考[此处](https://github.com/undreamai/LLMUnity?tab=readme-ov-file#setup) 🛠️。

开发者可以创建一个`LLM`或`LLMClient`对象来使用LLM功能。`LLMClient`类仅处理客户端功能，而`LLM`类继承了`LLMClient`类，并额外处理服务器功能。

然后，开发者可以指定`LLMClient` / `LLM`属性：

+   **提示**。这指定了AI的角色。

+   **玩家/AI名称（可选）**。玩家和AI名称可以为角色定义。

+   **流式功能（可选）**。流式功能允许Unity应用程序实时接收模型生成的输出。如果禁用，Unity应用程序将在模型完全生成后接收回复。

+   **其他模型选项（可选）**。有[更多模型选项](https://github.com/undreamai/LLMUnity?tab=readme-ov-file#hugs-model-settings)，这些选项可以由专家用户指定，并直接由llama.cpp服务器使用。

另外还有`LLM`特有的属性：

+   **模型**。这指定了要使用的LLM。[Mistral 7B Instruct v0.2](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2)模型[由TheBloke量化](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF)可以在Unity Inspector中直接下载作为默认模型。否则，任何由llama.cpp支持的LLM都可以加载。llama.cpp使用gguf格式，并提供了一个[转换脚本](https://github.com/ggerganov/llama.cpp/tree/master?tab=readme-ov-file#prepare-data--run)用于[HuggingFace模型](https://huggingface.co/models)。如果你不想安装llama.cpp并自行进行转换，你可以使用已经被[TheBloke](https://huggingface.co/TheBloke)转换过的模型💣。

![](../Images/985354ae67566a44b03ff00e491da154.png)

llama.cpp支持的模型

+   **运行资源（可选）**。你可以指定用户应用程序可以使用的CPU线程数量和/或GPU将运行的模型层数。如果用户的GPU不受支持，则会改为使用CPU。

除非你想弄脏双手，否则你可以*简单地按“下载模型”并定义提示* 😌！

![](../Images/dbfe8dcc18f10859f4e1b3e784fc2986.png)

在LLM脚本中可以参数化的不同选项

# 如何使用

现在让我们进入有趣的部分🎢！

LLMUnity的编写方式是可以用最少的代码进行使用。你只需要构造一个`LLM`对象，然后通过以下方式与之交互：

```py
_ = llm.Chat(message, HandleReply, ReplyCompleted);
```

其中：

+   `message`：包含用户输入的字符串对象。

+   `HandleReply`：此方法接收模型的回复作为字符串类型。通过此函数，你可以指定如何处理回复。如果启用了流式功能（默认行为），该函数将在模型实时生成回复时接收实时回复，否则它会一次性接收整个回复。

+   `ReplyCompleted`（可选）：无参数的方法。当模型完成生成回复时，会调用此函数。

## 基本功能

下面展示了一个最小示例🚂。在这里，我们发送一条消息“Hello bot!”并在控制台中显示模型的回复：

```py
using UnityEngine;
using LLMUnity;

public class MyGame : MonoBehaviour{
  public LLM llm;

  void HandleReply(string reply){
    Debug.Log(reply);
  }

  void Start(){
    _ = llm.Chat("Hello bot!", HandleReply);
  }
}
```

调用 `LLM` 的 `Chat` 函数，回复将在完成时通过 HandleReply 函数异步接收（无论是流式还是非流式的）。

要在 Unity 中创建应用程序，您需要创建一个包含以下内容的场景：

+   一个 `LLM` 脚本的 GameObject。LLM 对象的属性会在 Unity Inspector 中暴露，并可以按照前一节中的描述进行设置。

+   一个 `MyGame` 脚本的 GameObject。在这里，你将把上述创建的 `LLM` GameObject 绑定到 Unity Inspector 中的 `llm` 属性。

而且……就这些了 ✨！

## 简单交互

现在让我们看看一个展示基本交互的示例：

![](../Images/b432cbc591f1476685a3167e11d47645.png)

简单交互示例

这里我们有一个场景，其中包含：

+   一个 `LLM` 脚本的 GameObject（如前所述）

+   一个 `SimpleInteraction` 脚本的 GameObject

+   一个输入框（绿色的），允许用户输入文本

+   一个文本框（蓝色的）用来显示来自模型的回复

`SimpleInteraction` 脚本可以如下实现：

```py
using UnityEngine;
using LLMUnity;
using UnityEngine.UI;

public class SimpleInteraction : MonoBehaviour
{
    public LLM llm;
    public InputField playerText;
    public Text AIText;

    void Start()
    {
        playerText.onSubmit.AddListener(onInputFieldSubmit);
        playerText.Select();
    }

    void onInputFieldSubmit(string message)
    {
        playerText.interactable = false;
        AIText.text = "...";
        _ = llm.Chat(message, SetAIText, AIReplyComplete);
    }

    public void SetAIText(string text)
    {
       AIText.text = text;
    }

    public void AIReplyComplete()
    {
        playerText.interactable = true;
        playerText.Select();
        playerText.text = "";
    }
}
```

脚本定义了以下函数：

+   `Start`：场景开始时会选择 playerText 输入框，这样用户就可以输入文本。当文本提交时，会附加一个监听器，调用 `onInputFieldSubmit` 函数。

+   `onInputFieldSubmit`：当用户提交输入时，playerText 会被禁用，直到模型回复。模型输出字段 AIText 会被清空，然后调用 LLM 聊天函数。

+   `SetAIText`：当模型产生某个回复时，会调用此函数并将 AIText 文本设置为回复内容。

+   `AIReplyComplete`：当模型完成回复时，会调用此函数。playerText 会重新启用并清空，以便玩家可以输入下一个内容。

就这么简单，我们就能拥有一个功能完备的 LLM 交互（功能完备，虽然不美观我知道 🙃）。你可以在 [SimpleInteraction 示例](https://github.com/undreamai/LLMUnity/tree/main/Samples~/SimpleInteraction) 中找到这个示例。

## 多个 AI 功能

到目前为止，我们已经看到了与单个 AI 的交互。在实践中，游戏中会有多个 NPC 🤖。解决方案是创建如上所述的一个 `LLM` 对象来处理服务器，但还需要额外的 `LLMClient` 对象，使用不同的提示词为 AI 定义额外的行为。

展示此功能的示例可以在 [ServerClient 示例](https://github.com/undreamai/LLMUnity/tree/main/Samples~/ServerClient) 中找到。这是上面代码的扩展，使用一个 `LLM` 对象作为第一个 AI，并使用带有不同提示词的 `LLMClient` 对象作为第二个 AI（与第一个 AI 使用相同的服务器）。

![](../Images/87a99c2753883f8c8cd96f75bc7f2a33.png)

多个 AI 功能

## 聊天机器人

创建更具游戏性元素的最终步骤是根据你希望在游戏中拥有的方式来增强UI方面的设计🏰。这里不再详细讨论，因为这超出了LLM集成的范围。如果你对更复杂的UI感兴趣，可以查看[聊天机器人示例](https://github.com/undreamai/LLMUnity/tree/main/Samples~/ChatBot)，它创建了一个类似于消息应用程序的更愉悦的互动体验。

![](../Images/aeefb617616d1b50f95b84def8f3efe4.png)

一种消息应用程序风格的互动

# 结束

就是这样！在本指南中，我们了解了如何使用LLMUnity包将LLM集成到Unity中，并提供了一些实际示例。希望你觉得有用！如果你有任何问题/评论/建议，欢迎随时告诉我，以帮助改进本文或LLMUnity包🙏。

*注：除非另有说明，所有图片均由作者创建。*
