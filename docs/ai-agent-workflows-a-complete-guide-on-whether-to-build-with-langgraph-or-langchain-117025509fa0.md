# AI代理工作流：关于是否使用LangGraph或LangChain构建的完整指南

> 原文：[https://towardsdatascience.com/ai-agent-workflows-a-complete-guide-on-whether-to-build-with-langgraph-or-langchain-117025509fa0?source=collection_archive---------0-----------------------#2024-10-25](https://towardsdatascience.com/ai-agent-workflows-a-complete-guide-on-whether-to-build-with-langgraph-or-langchain-117025509fa0?source=collection_archive---------0-----------------------#2024-10-25)

## **深入探讨由同一创作者开发的两个库 — LangChain 和 LangGraph：它们的关键构建块、如何处理核心功能部分，以及如何根据你的使用场景在它们之间做出选择**

[](https://medium.com/@sandibesen?source=post_page---byline--117025509fa0--------------------------------)[![Sandi Besen](../Images/97361d97f50269f70b6621da2256bc29.png)](https://medium.com/@sandibesen?source=post_page---byline--117025509fa0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--117025509fa0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--117025509fa0--------------------------------) [Sandi Besen](https://medium.com/@sandibesen?source=post_page---byline--117025509fa0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--117025509fa0--------------------------------) ·阅读时长：11分钟·2024年10月25日

--

语言模型为用户与AI系统的互动方式以及这些系统之间的通信方式（通过自然语言）开辟了可能性。

> 当企业希望利用代理型AI功能构建解决方案时，最常见的技术问题之一是“我应该使用哪些工具？”对于那些急于入手的人来说，这是第一个障碍。

![](../Images/bbabc9b4020e5715a76bb4a605734055.png)

来源：Dalle-3

**在本文中，我们将探索构建代理型AI应用程序的两个最流行框架 — LangChain 和 LangGraph。** **在文章结束时，你应该能够全面理解它们的关键构建块，以及每个框架在处理核心功能时的差异，并能够形成一个有根据的观点，选择最适合你问题的框架。**

由于广泛将生成性AI整合到解决方案中的实践相对较新，开源社区的参与者正积极竞争，开发“最佳”的代理框架和编排工具。这意味着尽管每个参与者都带来了独特的方法，但他们几乎在不断推出新的功能。在阅读本文时，请记住，今天的事实可能明天就不再适用！

*注意：我原本打算对 AutoGen、LangChain 和 LangGraph 进行对比。然而，AutoGen 已经* [*宣布*](https://microsoft.github.io/autogen/0.2/blog/2024/10/02/new-autogen-architecture-preview/) *它将发布 AutoGen 0.4，这是从基础上完全重新设计的框架。当 AutoGen 0.4 发布时，请关注另一篇文章！*

# LangChain 和 LangGraph 的基础组件

通过理解每个框架的不同基础元素，你将更深入地理解它们在下一节中处理某些核心功能时的关键差异。以下描述并不是每个框架所有组件的详尽列表，而是理解它们总体方法差异的坚实基础。

**LangChain**

使用 LangChain 有两种方法：作为一系列预定义命令的顺序链条，或者使用 LangChain 代理。每种方法在处理工具和协调方面有所不同。链条遵循预定义的线性工作流，而代理则充当协调者，可以做出更动态的（非线性）决策。

+   链接：一系列步骤，可以包括调用 LLM、代理、工具、外部数据源、过程代码等。链条可以分支，意味着一个链条可以根据逻辑条件分裂成多个路径。

+   代理或语言模型：语言模型具有生成自然语言响应的能力。但代理使用语言模型加上额外的能力来推理、调用工具，并在工具调用失败的情况下重复调用过程。

+   工具：可以在链条中调用或由代理调用与外部系统交互的基于代码的功能。

+   提示：这可以包括指导模型如何完成任务以及可用工具的系统提示、从外部数据源注入的信息（为模型提供更多上下文），以及用户为模型完成的任务或提示。

**LangGraph**

LangGraph 从不同的角度处理 AI 工作流。正如其名称所示，它像图形一样协调工作流。由于它在处理 AI 代理、过程代码和其他工具之间不同流程时的灵活性，它更适合用在那些线性链方法、分支链条或简单代理系统无法满足需求的用例。与 LangChain 相比，LangGraph 设计用于处理更复杂的条件逻辑和反馈循环。

+   图形：一种灵活的工作流组织方式，可以包括调用 LLM、工具、外部数据源、过程代码等。LangGraph 也支持循环图；这意味着你可以创建循环和反馈机制，使节点能够多次回访。

+   节点：表示工作流中的步骤，如 LLM 查询、API 调用或工具执行。

+   边和条件边：边通过将一个节点的输出连接到下一个节点的输入来定义信息流。条件边则定义了在满足特定条件时，信息如何从一个节点流向另一个节点。开发者可以自定义这些条件。

+   状态：状态是应用程序在信息通过图形流动时的当前状态。它是一个由开发者定义的可变TypedDict对象，包含当前执行图形所需的所有相关信息。LangGraph会自动处理每个节点状态的更新，随着信息在图中流动而更新状态。

+   代理或语言模型：图中的语言模型仅负责根据输入生成文本响应。代理能力利用语言模型，但使得图能够有多个节点代表代理的不同组件（例如推理、工具选择和工具执行）。代理可以决定在图中采取哪条路径，更新图的状态，并执行比仅生成文本更多的任务。

# 每个框架如何处理核心功能的差异

LangGraph和LangChain在一些功能上有所重叠，但它们从不同的角度来解决问题。LangChain专注于通过使用链条来实现线性工作流或不同的AI代理模式。而LangGraph则专注于创建更灵活、更细粒度的基于过程的工作流，可以包括AI代理、工具调用、过程代码等。

通常来说，LangChain的学习曲线比LangGraph更平缓。LangChain具有更多的抽象和预定义配置，使其在简单使用场景下更易于实现。而LangGraph则允许对工作流的设计进行更多的自定义控制，这意味着它的抽象程度较低，开发者需要学习更多才能有效使用该框架。

## **工具调用**：

**LangChain**

在LangChain中，工具可以通过两种方式被调用，具体取决于你是否使用链来按步骤顺序执行一系列任务，或者仅仅使用其代理功能，而没有明确在链中定义。在链中，工具作为链中的预定义步骤被包括进去——这意味着它们不一定由代理调用，因为在链中已经预定了它们会被调用。然而，当代理没有在链中定义时，代理拥有自主决定调用哪些工具及其调用时机的权利，这取决于代理可以访问的工具列表。

*链的工作流示例：*

![](../Images/a937a13e7dc33df0a6714362f650f726.png)

图片由Sandi Besen提供

1.  创建代表工具的函数，并使其与链兼容

1.  将工具集成到链中

1.  执行链

*代理的工作流示例：*

![](../Images/80980bbb40706002a4595f55bc4957e3.png)

图片由Sandi Besen提供

1.  工具已定义

1.  工具已添加到代理

1.  代理接收查询并决定是否以及何时使用搜索工具。如果需要，代理可能会多次使用该工具。

**LangGraph**

在**LangGraph**中，工具通常作为图中的一个节点进行表示。如果图中包含代理，则由代理基于其推理能力决定调用哪个工具。根据代理的工具决策，图会导航到“工具节点”来处理工具的执行。可以在代理到工具节点的边缘中包含条件逻辑，添加额外的逻辑来决定是否执行某个工具。如果图中没有代理，则类似于LangChain中的链，可以根据条件逻辑将工具包含在工作流中。

*包含代理的图示例流程：*

![](../Images/c7156da1e5dbb0a3dc3af2b9532cb2fc.png)

图片由Sandi Besen提供

1.  工具已定义

1.  工具绑定到代理

1.  代理决定是否需要工具，如果需要，则决定使用哪种工具。

1.  LangGraph框架检测到需要调用工具，并导航到图中的工具节点以执行工具调用。

1.  工具的输出被捕获并添加到图的状态中

1.  代理会再次被调用并更新状态，以便做出下一步的决策

*没有代理的图示例流程：*

![](../Images/bd4b0a727fce78bbe0e2c0babed8a5ff.png)

图片由Sandi Besen提供

1.  工具已定义

1.  工具作为一个节点被添加到图中

1.  可以使用条件边缘来确定何时使用特定的工具节点，并控制图的流程

1.  工具可以配置为更新图的状态

*如果你想了解更多关于工具调用的内容，我的朋友* [*Tula Masterman*](https://medium.com/u/ce8c2ab0804a?source=post_page---user_mention--117025509fa0--------------------------------) *写了一篇精彩的* [*文章*](https://medium.com/towards-data-science/ai-agents-the-intersection-of-tool-calling-and-reasoning-in-generative-ai-ff268eece443) *，讲解了工具调用在生成性AI中的工作原理。*

注意：LangChain和LangGraph都不支持像微软的Semantic Kernel那样开箱即用的[语义函数](https://www.developerscantina.com/p/semantic-kernel-functions/)。

## 对话历史和记忆

**LangChain**

LangChain提供了内建的抽象层，用于处理对话历史和记忆。你可以选择想要传递给LLM的粒度级别（从而决定传递多少令牌），这些选项包括完整的会话历史、总结版或自定义定义的记忆。开发者还可以创建自定义的长期记忆系统，将记忆存储在外部数据库中，以便在需要时进行检索。

**LangGraph**

在 LangGraph 中，状态通过跟踪每个时刻定义的变量来处理内存。状态可以包括诸如对话历史、计划步骤、语言模型前一个响应的输出等内容。它可以从一个节点传递到下一个节点，以便每个节点都可以访问系统当前的状态。然而，跨会话的长期持久性内存并不是该框架的直接特性。为了实现这一点，开发者可以包含负责存储记忆和其他变量的节点，并将其保存在外部数据库中，稍后可以检索。

## **开箱即用的 RAG 功能：**

**LangChain**

LangChain 可以处理复杂的检索和生成工作流，并拥有更成熟的工具集，帮助开发者将 RAG 集成到应用中。例如，LangChain 提供了开箱即用的文档加载、文本解析、嵌入创建、向量存储和检索功能，可以直接使用 langchain.document_loaders、langchain.embeddings 和 langchain.vectorstores。

**LangGraph**

在 LangGraph 中，RAG 需要从零开始开发，作为图结构的一部分。例如，可以为文档解析、嵌入和检索分别设置不同的节点，并通过普通或条件边连接这些节点。每个节点的状态将用于在 RAG 流水线的各个步骤之间传递信息。

## **并行性：**

**LangChain**

LangChain 通过使用 RunnableParallel 类提供了运行多个链或代理的机会。对于更高级的并行处理和异步工具调用，开发者需要通过使用像 asyncio 这样的 Python 库来定制实现这些功能。

**LangGraph**

LangGraph 支持节点的并行执行，只要没有任何依赖关系（例如一个语言模型的响应输出作为下一个节点的输入）。这意味着，只要节点之间没有依赖关系，它就可以支持多个代理在图中同时运行。与 LangChain 类似，LangGraph 也可以使用 RunnableParallel 类来并行运行多个图。LangGraph 还支持通过使用像 asyncio 这样的 Python 库进行并行工具调用。

## **重试逻辑与错误处理：**

**LangChain**

在 LangChain 中，错误处理由开发者明确定义，可以通过在链本身中引入重试逻辑来完成，或者在工具调用失败时由代理来处理。

**LangGraph**

在 LangGraph 中，你实际上可以通过将错误处理作为独立节点来嵌入到工作流中。当某些任务失败时，可以指向另一个节点或让同一个节点重试。最棒的是，只有失败的特定节点会被重试，而不是整个工作流。这意味着图可以从失败点恢复，而不需要从头开始。如果你的使用案例涉及许多步骤和工具调用，这一点可能很重要。

# 总结

你可以仅使用LangChain而不使用LangGraph，或仅使用LangGraph而不使用LangChain，或者两者一起使用！同样，也完全可以探索将LangGraph的图形化编排与其他Agentic AI框架（如微软的AutoGen）结合使用，通过将AutoGen代理作为图中的独立节点来实现。可以说有很多选择——这可能会让人感到不知所措。

那么，在进行完这些研究后，我应该在什么时候使用每个框架呢？虽然没有硬性规定，以下是我的个人选择：

**何时仅使用LangChain：**

你需要快速原型设计或开发AI工作流，这些工作流涉及顺序任务（例如文档检索、文本生成或摘要），遵循预定义的线性模式。或者，你希望利用AI代理模式来动态做出决策，但不需要对复杂的工作流进行精细控制。

**何时仅使用LangGraph：**

你的使用场景需要非线性工作流，其中多个组件动态交互，例如依赖于条件的工作流、需要复杂分支逻辑、错误处理或并行处理的工作流。你愿意为那些像在LangChain中那样没有抽象出来的组件构建自定义实现。

**何时使用LangChain和LangGraph一起：**

你喜欢LangChain中预构建的提取组件，比如开箱即用的RAG能力、内存功能等，但同时也希望使用LangGraph的非线性编排来管理复杂的任务流。将两个框架结合使用可以充分发挥它们各自的优势。

最终，无论你选择LangChain、LangGraph，还是两者的组合，都取决于你项目的具体需求。

注：本文和论文中表达的观点仅代表作者个人意见，并不一定反映其各自雇主的观点或政策。

还有问题或认为有些地方需要进一步澄清吗？在[LinkedIn](https://www.linkedin.com/in/sandibesen/)上给我发个私信！我总是渴望参与思想交流，并不断完善我的工作。

***参考文献***：

[](/navigating-the-new-types-of-llm-agents-and-architectures-309382ce9f88?source=post_page-----117025509fa0--------------------------------) [## 探索新型LLM代理和架构

### ReAct代理的失败为新一代代理和可能性铺平了道路

towardsdatascience.com](/navigating-the-new-types-of-llm-agents-and-architectures-309382ce9f88?source=post_page-----117025509fa0--------------------------------) [](https://langchain-ai.github.io/langgraph/?source=post_page-----117025509fa0--------------------------------) [## 主页

### 将语言代理构建为图形

[LangGraph和研究代理 | Pinecone](https://langchain-ai.github.io/langgraph/?source=post_page-----117025509fa0--------------------------------)

### LangGraph是一个基于图形的代理框架，使我们能够构建更灵活、更可控的AI代理。这里…

[链式未来：深入探讨LangChain](https://www.pinecone.io/learn/langgraph-research-agent/?source=post_page-----117025509fa0--------------------------------) 

### LLM链是由一系列互联组件组成，旨在以特定顺序执行任务，以最大化…

[从基础到高级：探索LangGraph](https://www.comet.com/site/blog/chaining-the-future-an-in-depth-dive-into-langchain/?source=post_page-----117025509fa0--------------------------------) 

### 构建单一和多代理工作流，带有“人类在回路”互动

[面向数据科学的探索：LangGraph从基础到高级](https://www.comet.com/site/blog/chaining-the-future-an-in-depth-dive-into-langchain/?source=post_page-----117025509fa0--------------------------------)
