# 下一代代理：释放动态上下文的力量

> 原文：[https://towardsdatascience.com/next-level-agents-unlocking-the-power-of-dynamic-context-68b8647eef89?source=collection_archive---------5-----------------------#2024-05-15](https://towardsdatascience.com/next-level-agents-unlocking-the-power-of-dynamic-context-68b8647eef89?source=collection_archive---------5-----------------------#2024-05-15)

## *如何提高AI代理的准确性、速度和令牌使用效率*

[](https://medium.com/@frankw_usa?source=post_page---byline--68b8647eef89--------------------------------)[![Frank Wittkampf](../Images/3dbd69f8ef648074fa170fac451645fd.png)](https://medium.com/@frankw_usa?source=post_page---byline--68b8647eef89--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68b8647eef89--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--68b8647eef89--------------------------------) [Frank Wittkampf](https://medium.com/@frankw_usa?source=post_page---byline--68b8647eef89--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--68b8647eef89--------------------------------) ·阅读时长12分钟·2024年5月15日

--

![](../Images/7eafdf2106c409b65fc51079a1e7dbcd.png)

*大多数AI代理之间存在显著差异（图片由MidJourney生成）*

# 引言

AI代理的行为由两件事决定：(1) 它运行的模型和 (2) 你提供给模型的上下文。**你**提供这些上下文的方式决定了代理输出的质量。你甚至可以说，在同一模型下运行的两个AI代理之间的行为完全由它们的上下文定义。那么，什么是代理的上下文呢？请参见下方的*上下文类型*（图片）以了解更多示例。

本文深入探讨了通过根据代理需求有选择地定制上下文，以提高AI代理效率和准确性的更高级方法。我将首先介绍5种策略，然后提供一些额外的实施技巧。本文的经验来源于在生产环境中与多代理团队合作的丰富经验，但同样适用于单代理系统。

![](../Images/2aa6eb7cdbbd3b57166431add3fd6662.png)

AI代理的上下文类型（图片来自作者）

# 理解上下文需求

大多数在线示例和教程简化了它们的上下文处理方式（例如应用固定的截止长度）。实际的AI应用需要更加复杂的方法。

不同的代理根据手头任务的不同具有不同的上下文需求。有些代理可能只需要前一条消息，而其他代理可能需要大量的历史数据才能准确地做出回应。这些差异表明，每个代理都需要一种量身定制的上下文管理方法。

考虑以下用户与两个代理（一个软件开发人员和一个软件审查员）之间的持续对话示例：

```py
1 **User**: “Please improve the sorting function I wrote from the uploaded file”
2 **System_prompt_for_coder**: “You are a SW developer, you help the user to 
  develop …”
3 **Coding_agent**: “read_file_function”
4 **Function_response**: “coding snippet: ```一些返回的代码```py”
5 **System_prompt_for_coder**: “You are a SW developer, you help the user to
  develop …”
6 **Coding_agent**: “I’ve improved your code by adding … and … to your code, 
  here is the new function …”
7 **System_prompt_for_reviewer**: “you are a SW code reviewer, you …”
8 **Code_reviewer_agent**: “After reviewing the improved code, there 
  are a few more things that I would add to the code …"
9 **System_prompt_for_coder**: “you are a SW developer, you help the user to 
  develop …”
10 **Coding_agent**: “ … ”
```

这个例子中的上下文显然是重复且低效的。许多行是重复的（例如系统提示），而每个代理都得到了超过它所需的上下文。让我们回顾一下几种改善上下文处理的方法。

# 策略1：消息标签化

消息标签化是管理和优化人工智能代理与其任务之间互动的关键工具。通过为对话中的每条消息分配元数据，我们可以智能地选择与代理当前任务最相关的信息。这一策略涉及几个关键策略：

**相关性标签**：每条消息都应标记上反映其与当前和未来互动相关性的标签。这个过程涉及分析消息的内容，并确定它对代理决策过程的潜在帮助。例如，包含问题、决策或见解的消息应该标记为高度相关。

**持久性标签**：对消息进行分类，依据其在时间上的持续性和有用性非常重要。有些消息，比如包含基础决策或里程碑通讯的消息，具有长期价值，应该在多个会话中保留。相反，系统消息可能只在特定时刻需要一次。这些应该在其即时相关性过去后从代理的记忆中排除。

**来源与关联标签**：这涉及识别每条消息的来源，无论是来自特定代理、用户、功能还是其他过程。这个标签有助于构建一个结构化且易于浏览的历史记录，使代理能够根据来源或任务相关性高效地检索和引用信息。

为消息的元数据应用智能标签可以使你进行智能选择。继续阅读以了解一些示例。

# 策略2：代理特定的上下文需求

不同的代理有不同的需求。有些代理能在极少的信息下正常工作，而其他代理则需要大量上下文才能正确操作。这一策略建立在我们刚刚讨论的标签基础上。

**关键上下文识别**：识别哪些消息对每个特定代理至关重要并专注于这些消息，对于简化处理过程和提高响应准确性至关重要。让我们看一下上述上下文中的第8行。代码审查员只需要有限的上下文才能准确地完成工作。我们甚至可以相当确定地说，如果给它提供超过必要的上下文，它将给出更糟的答案。

那么它需要什么样的上下文呢？快速看一下，你会推测出代码审查员只需要它自己的系统提示，还需要它之前的最后一条代理消息，其中包含了最新版本的代码（第6行）。

因此，每个代理应该被配置为只选择它所需要的历史记录。代码审查员只查看最后2条消息，而代码编写者则需要更长的历史记录。

# 策略3：优化系统提示

**位置：** 当你快速搜索代理和系统提示时，很明显，系统提示在代理中的位置非常重要。它应该是链中的第一条消息，还是最后一条消息？不同的使用案例会得出不同的观点和结果。例如，哪种方式能得到更好的结果？

```py
1) user: "I visited dr. Fauci on Thursday, and got diagnosed with …"
2) system: "Extract all medically relevant info from the user prompt"
```

或

```py
 1) system: "Extract all medically relevant info from the user prompt"
2) user: "I visited dr. Fauci on Thursday, and got diagnosed with …"
```

如果你在一个较大且更复杂的历史记录中进行测试，你会发现同样的指令放在不同的位置会产生不同的结果。可以明确的是，系统提示应该有意识地放置在特定位置，而且这取决于代理和使用场景。

注：根据我的经验，是否将系统提示放在链中的最后一条消息取决于LLM。通常，将其作为最后一条消息是一个不错的选择（当然有明确的例外情况，比如信息提取）。

**持久性：** 其次，除了系统提示的位置，在大多数使用场景中，你应该确保系统提示被标记为“短暂的”。我所说的短暂消息，是指它在使用后不会保留在聊天历史中。

例如，agent1 可能会收到一个提示，要求它回顾并反思问题。接下来的一轮，agent1 被要求使用它的反思来找到问题的解决方案。在第二轮中，最初的提示（让其反思）会让代理感到困惑，导致答案更差。如果那个初始的系统提示不是持久性的，并且没有再次提供给代理，那就会更好。下面的例子中，这意味着在你第二次调用LLM时，第2行应该被删除。

```py
#1 **User**: 
  "What is the meaning of the universe?"
#2 **System**: 
  "You're a philosopher; before you answer, step back and 
  reflect on what the user's question implies."
#3 **Agent_philo**: 
  "Reflecting on this question, one can consider the universe 
  through various philosophical lenses - existentialism views 
  it as a canvas for creating personal meaning, while cosmology 
  sees it as a playground for understanding physical laws. This 
  interplay between human perception and scientific exploration 
  is central to understanding our query."
#4 **System**: 
  "You're a philosopher, and you just reflected deeply on the 
  question posed by the user. Use that insightful reflection to 
  craft a comprehensive answer."
#5 **Agent_philo**: 
  "From an existentialist perspective, the meaning of the universe 
  is a personal journey, where each individual constructs their own 
  meaning through choices and life experiences. In contrast, from a 
  cosmological viewpoint, it is about exploring and understanding 
  the vast, intricate laws that govern the cosmos. These perspectives 
  help us grasp the multifaceted nature of our universe, blending our 
  existential quests with scientific inquiry."
```

这种方法确保任何旧的系统提示都从链中删除，并且每个提示都被合理地放置。现在我们拥有一个整洁的对话历史，从而实现更精确且可预测的交互。

# 策略4：减少RAG中的冗余

我们可以撰写十几篇文章来优化你的代理，通过改善RAG的使用，但在这里我们会简要介绍几段。使用RAG时可能产生的大量令牌，已经足够让我们必须提到一些管理技巧。如果你还没有开始研究，这是一个你应该花大量时间研究的课题。

关于RAG的基础教程大多假设你或你的用户上传的文档是简单直接的。然而，在实际应用中，大多数文档都是复杂且不可预测的。我的经验是，很多文档都包含重复信息。例如，同样的信息往往会在PDF文章的引言、正文和结论中反复出现。或者，一份医疗文件中可能会有重复的医生更新，内容几乎完全相同。又或者，日志会一遍遍地重复。此外，特别是在生产环境中，当需要在大量文件中进行检索时，标准的RAG过程返回的内容可能会非常重复。

**处理重复内容：** 优化RAG上下文的第一步是识别并去除检索到的文档片段中的完全重复和近似重复内容，以防止冗余。完全重复的内容很容易识别。近似重复可以通过语义相似性来检测，方法是查看向量嵌入的多样性（多样化的片段具有相互间距离较大的向量），以及许多其他技术。如何实现这一点将极大地依赖于你的使用案例。以下是[一些示例（来自perplexity）](https://www.perplexity.ai/search?q=show+me+best+practices+on+how+to+remove+near+duplicates+in+my+RAG+results)

**响应多样性：** 另一种确保RAG响应多样性的方法是通过智能地对来自不同文件的内容进行分组。一种非常简单但有效的方法是，不仅仅按相似度取前N个文档，而是使用检索查询中的GROUP BY。是否采用这种方法在很大程度上取决于你的使用案例。这里有一个[示例（来自perplexity）](https://www.perplexity.ai/search?q=show+me+a+query+that+selects+the+top+N+similar+results+from+a+vector+database+but+groups+it+by+file+so+that+you+ensure+the+diversity+of+snippets+come+from+several+files+instead+of+just+one+or+two)

**动态检索：** 既然本文讨论的是动态上下文，那么你如何将这种哲学引入到你的RAG过程中呢？大多数RAG过程会检索前N个结果，例如，前10个最相似的文档片段。然而，这并不是人类检索结果的方式。当你搜索信息时，你会去像Google这样的搜索引擎，直到找到正确的答案。这可能是第1个或第2个搜索结果，也可能是第20个。当然，这也取决于你的运气和耐力 ;-)。你可以以同样的方式构建RAG。我们可以允许代理进行更具选择性的检索，只提供前几个结果，并让代理决定是否需要更多信息。

这是一个建议的做法。不要仅仅定义一个相似度的临界值，而是定义高、中、低三个临界点。例如，你的搜索结果可能有 11 个非常相似的文档，5 个中等相似的文档和 20 个稍微相似的文档。如果我们说代理每次获取 5 个文档，那么现在你让代理自己决定是否需要更多。你告诉代理它已经看到 11 个非常相似的文档中的 5 个，还有 25 个文档在其后。通过一些提示工程，你的代理在寻找数据时将开始更加理性地行动。

# 战术 5：上下文处理的高级策略

我将简要介绍几种策略，将动态上下文进一步推进。

**即时元数据**：如战术 1 所述，向消息添加元数据可以帮助你预选择特定代理所需的历史记录。对于大多数情况，简单的一个词文本标签就足够了。知道某个内容来自某个特定功能、代理或用户，可以让你为消息添加一个简单的标签，但如果你处理的是非常大的 AI 响应并且需要更多优化，那么有一种更高级的方式可以向消息添加元数据：通过 AI。

其中的一些例子包括：

+   标记历史消息的一种简单方法是进行一次单独的 AI 调用（使用更便宜的模型），该调用会为消息生成标签。然而，现在你每次都需要进行两次 AI 调用，并且在流程中引入了额外的复杂性。

一种更优雅的生成标签的方式是让消息的原作者在编写响应的同时生成标签。

+   让代理以 JSON 格式给出响应，其中一个元素是其正常的响应，另一个元素是内容的标签。

+   使用多功能调用，并为代理提供它需要调用的功能，该功能定义了消息标签。

+   在代理进行的任何功能调用中，保留一个必需的参数，其中包含一个标签。

    通过这种方式，你可以即时为功能内容生成标签。

另一个动态优化上下文的高级策略是对你的 RAG 进行预处理。

**RAG 的双重处理**：为了优化你的 RAG 流程，你可以考虑使用一个更便宜（且更快）的 LLM 来浓缩你的 RAG 结果，然后再将其提供给标准的 LLM。使用这种方法时的窍门是使用一个非常简单且非干扰性的提示，将原始的 RAG 结果浓缩或简化为更易消化的形式。

例如，你可能会使用一个更便宜的模型来提取特定信息，以减少重复，或者只选择与当前任务相关的文档部分。这确实需要你了解更便宜的模型的优缺点。当与更强大的模型结合使用时，这种方法可以节省大量成本（和提高速度）。

# 实施

好的，那么以上内容是否意味着我的每个代理都需要几页定制代码来优化其性能？我该如何将这些概念普遍化并加以扩展呢？

**代理架构**：这些问题的答案是，有一些清晰的方法可以进行设置。只需要一些前瞻性和规划。建立一个能够正确运行多种代理的平台要求你具备一个代理架构。如果你从一套明确的设计原则开始，那么利用动态上下文让你的代理更快、更便宜、更好，一次性满足所有需求，并不会非常复杂。

动态上下文配置是你代理架构中的一个元素。

**动态上下文配置**：正如本文所讨论的，每个代理都有独特的上下文需求。而管理这些需求可能会涉及管理所有可能代理上下文中的大量变化（请参见本文顶部的图像）。然而，好消息是，这些变化可以轻松地编码为几个简单的维度。让我给你一个例子，整合本文中的大多数概念。

让我们想象一个代理，它是一个软件开发人员，首先规划自己的行动，然后执行该计划。这个代理的上下文配置可能是：

+   保留最初的用户问题

+   保留计划

+   忘记除最后一次代码修订和最后一条消息外的所有历史记录

+   使用RAG（上传的代码文件中）而不进行RAG凝缩

+   始终将系统提示设置为最后一条消息

这个配置保存在该代理的上下文配置中。因此，现在你对AI代理的定义不仅仅是一个提示指令集合。你的代理还有一个特定的上下文配置。

你会看到，在不同的代理之间，这些配置可以非常有意义并且各不相同，它们使得代码的抽象化变得非常有意义，而不再是完全定制化的。

# *总结*

正确管理动态上下文不仅能提升你的AI代理的性能，还能显著提高准确性、速度和令牌使用效率……你的代理现在更快、更好、更便宜，一次性满足所有需求。

你的代理不应仅仅由其提示指令定义，它还应该有自己的上下文配置。通过使用简单的维度来编码每个代理不同的配置，将极大地增强你通过代理实现的功能。

动态上下文只是你的代理架构中的一个元素。如果你想了解更多，欢迎邀请我讨论。如果你有任何问题或其他见解，可以在评论区与我交流，当然，如果你觉得这篇文章对你有帮助，也请给我点个赞或关注我。

编程愉快！
