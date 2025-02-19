# 构建 LLM 应用：清晰的逐步指南

> 原文：[`towardsdatascience.com/building-llm-apps-a-clear-step-by-step-guide-1fe1e6ef60fd?source=collection_archive---------0-----------------------#2024-06-10`](https://towardsdatascience.com/building-llm-apps-a-clear-step-by-step-guide-1fe1e6ef60fd?source=collection_archive---------0-----------------------#2024-06-10)

## 构建 LLM 原生应用的全面步骤：从最初的构想到实验、评估和产品化

[](https://medium.com/@almogbaku?source=post_page---byline--1fe1e6ef60fd--------------------------------)![Almog Baku](https://medium.com/@almogbaku?source=post_page---byline--1fe1e6ef60fd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1fe1e6ef60fd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1fe1e6ef60fd--------------------------------) [Almog Baku](https://medium.com/@almogbaku?source=post_page---byline--1fe1e6ef60fd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1fe1e6ef60fd--------------------------------) ·12 分钟阅读·2024 年 6 月 10 日

--

大型语言模型（LLM）正迅速成为现代人工智能的基石。然而，目前并**没有成熟的最佳实践**，而且许多先驱者常常面临**没有清晰路线图**的困境，不得不重新发明轮子，或者陷入困境。

在过去的两年里，我帮助组织利用 LLM 构建创新应用。通过这些经验，我开发了一种***经受考验的方法***，用于创建创新解决方案（这些方法受到[LLM.org.il](https://llm.org.il)社区的见解影响），我将在本文中分享这些方法。

本指南提供了一个*清晰的路线图*，帮助你在 LLM 原生开发的复杂领域中找到前进的方向。你将学习如何从构思到实验、评估和产品化，释放你的潜力，创造开创性的应用。

![](img/36d91b22a5b41e77c6e555b9655839f9.png)

（由 Dall-E3 创建）

# 为什么标准化流程至关重要

LLM 领域变化如此之快，有时我们每天都会听到新的突破性创新。这虽然令人振奋，但也充满混乱——你可能会发现自己迷失在过程当中，不知道该做什么，或者如何将你的新创意付诸实践。

长话短说，如果你是一个**AI 创新者**（无论是管理者还是从业者），想要有效地构建 LLM 原生应用，**这篇文章适合你**。

实施标准化流程有助于启动新项目，并带来几个关键的好处：

1.  **标准化流程 ——** 标准化流程有助于协调团队成员，确保顺利的新人入职过程（特别是在这种混乱中）。

1.  **定义清晰的里程碑** —— 一种简单的方法来跟踪你的工作、衡量它并确保你走在正确的道路上。

1.  **确定决策点** —— LLM 原生开发充满了未知和“小规模实验”[见下文]。明确的决策点使我们能够降低风险，并始终保持精益开发。

# LLM 工程师的必备技能

与软件研发中的任何其他既定角色不同，LLM 原生开发绝对需要一个新的角色：**LLM 工程师** 或 **AI 工程师**。

LLM 工程师是一个独特的混合型角色，涉及不同（既定）角色的技能：

+   **软件工程技能——** 就像大多数软件工程师一样，大部分工作是将乐高积木拼凑在一起，并将所有东西粘合在一起。

+   **研究技能** —— 正确理解 LLM 原生实验的性质是***至关重要的。*** 尽管构建“酷炫的演示应用”相对容易，但“酷炫演示”和实际解决方案之间的距离需要实验和敏捷性。

+   **深入理解业务/产品——** 由于模型的脆弱性，理解业务目标和流程至关重要，而不是坚持我们定义的架构。能够建模手动流程是 LLM 工程师的金牌技能。

在写这篇文章时，LLM 工程学仍然是全新的领域，**招聘可能非常具有挑战性**。寻找有后端/数据工程或数据科学背景的候选人可能是一个不错的主意。

*软件工程师* 可能会期望一个 *更平滑的过渡*，因为实验过程更加“工程化”，而不像传统的数据科学工作那样“科学”。话虽如此，我也见过许多*数据科学家*成功完成这个过渡。只要你能接受你必须拥抱新的软技能这一事实，你就走在了正确的道路上！

# LLM 原生开发的关键要素

与传统的后端应用（如 CRUD）不同，这里没有一步步的操作指南。像“AI”中的其他所有事物一样，LLM 原生应用需要一个**研究和实验的*思维方式*。**

要驯服这只野兽，你必须通过将工作拆分成更小的实验来实现分而治之，尝试其中一些实验，并选择最有前景的实验。

我无法强调*研究心态*的重要性。 这意味着你可能会投入时间去探索一个研究方向，结果发现它“不可行”、“不够好”或“不值得”。完全没关系——这意味着你在正确的轨道上。

![](img/d80576c3df986e4e82ba49558a9d3111.png)

实验 LLM 是构建 LLM 原生应用的唯一途径（并且避免途中出现障碍）（由 Dall-E3 创建）

# 拥抱实验：过程的核心

有时候，你的“实验”会失败，然后你会稍微调整工作，结果另一个实验会成功得多。

这正是为什么，在设计我们的终极解决方案之前，我们必须从简单开始并对我们的风险进行对冲。

1.  **定义“预算”或时间框架。**我们看看在 X 周内能做些什么，然后决定是否继续。通常，2 到 4 周的时间来理解基本的 PoC 就足够了。如果看起来有希望——继续投入资源以改善它。

1.  **实验—**无论你在实验阶段选择自下而上还是自上而下的方法，你的目标是最大化结果成功率。在第一次实验迭代结束时，你应该拥有一些 PoC（供利益相关者使用）以及你所取得的基准。

1.  **回顾—**在我们的研究阶段结束时，我们可以了解构建此类应用的可行性、局限性和成本。这有助于我们决定是否将其投入生产，并设计最终的产品及其用户体验。

1.  **产品化—**开发项目的生产就绪版本，并通过遵循标准的软件工程最佳实践，将其与其他解决方案整合，实施反馈和数据收集机制。

![](img/3584722390c58fe3144fc2d6496b099d.png)

LLM 原生应用开发生命周期（图示来自作者）

为了有效地实现面向实验的过程，我们必须做出有根据的决定来接近并构建这些实验：

## 精简起步：自下而上的方法

尽管许多早期采用者很快就跳入了*最先进的*多链条代理系统，像是完整的 Langchain 或类似的框架，但我发现**自下而上的方法**往往能取得更好的效果。

从精简开始，**非常精简**，拥抱*“一个提示统治一切”*的哲学。尽管这种策略看起来不太传统，并且一开始可能会产生不好的结果，但它为你的系统建立了一个*基准*。

从那里开始，持续迭代和优化你的提示，运用提示工程技术来优化结果。当你发现精简方案中的弱点时，通过添加分支来拆分过程，解决这些不足。

在设计我的 LLM 工作流图的每个“叶子”或 LLM 原生架构时，我遵循*LLM 三角原理*³来决定在哪些时候、以何种方式剪枝、分支或加粗根部（通过使用提示工程技术），并挤出更多的“柠檬汁”。

![](img/5201b05f41b5d4fed69473f9d53293e3.png)

自下而上的方法插图（图示来自作者）

例如，要使用自下而上的方法实现“本地语言 SQL 查询”，我们将从直接向 LLM 发送模式并要求它生成查询开始。

![](img/1cc8092436c0a284cf6ca8b5914b78ad.png)

一个自下而上的方法示例（图示来自作者）

通常，这并不与“自上而下的方法”相矛盾，而是作为它的另一个步骤。这使我们能够展示快速的胜利，并吸引更多项目投资。

## 全局视角：自上而下的策略

> “我们知道 LLM 工作流并不容易，为了实现我们的目标，我们可能最终会采用某种工作流或 LLM 本地架构。”

自上而下方法认识到这一点，并从第一天开始就设计 LLM 本地架构，并从一开始就实施其不同的步骤/链条。

通过这种方式，你可以测试整个工作流架构，而不是单独优化每一片叶子，真正做到榨干整个柠檬。

![](img/c45de8272f121dbc6dc5740d4459892f.png)

自上而下方法流程：一次性设计架构，实施、测试并衡量效果（图片由作者提供）

例如，要实现“本地语言 SQL 查询”并采用自上而下的方法，我们会在开始编码之前先设计架构，然后再跳到完整实现：

![](img/59ca9496b40b497469678b254636bbcc.png)

自上而下方法的一个例子（图片由作者提供）

## 找到正确的平衡

当你开始尝试实验 LLM（大型语言模型）时，你可能会从两个极端中的一个开始（过于复杂的自上而下方法或超级简单的一次性方法）。实际上，并没有绝对的“赢家”。

理想情况下——你会在编码和实验模型之前，先定义好一个好的 SoP¹，并建模一个专家。但在现实中，建模非常困难，有时你可能没有接触到这样的专家。

我发现要在第一次尝试时找到一个好的架构/SoP¹非常具有挑战性，因此在跳到更复杂的方案之前，轻微实验是值得的。然而，这并不意味着*一切*都必须*过于精简*。如果你已经有*先验理解*，知道某些东西*必须*被拆解成更小的部分——就去做。

你应该利用 *LLM 三角原理*³，正确地建模手动过程，同时设计你的解决方案。

## 优化你的解决方案：榨干柠檬

在实验阶段，我们不断地榨干柠檬并增加更多的“复杂性层次”：

+   [**提示工程技术**](https://www.promptingguide.ai/)——如少量样本、角色分配，甚至是动态少量样本

+   **扩展上下文窗口**，从简单的变量信息到复杂的 RAG 流程，可以帮助提高结果。

+   **尝试不同的模型**——不同的模型在不同的任务上表现不同。此外，大型 LLM 往往不具备成本效益，因此值得尝试更多针对特定任务的模型。

+   **提示精简**——我发现将 SOP¹（具体来说是提示和请求的输出）进行“精简”通常可以提高延迟。

    通过减少提示大小和模型需要经过的步骤，我们可以减少模型需要生成的输入和输出。你会感到惊讶，但有时候，提示精简甚至能提高质量！

    请注意，饮食可能也会导致质量下降，因此在进行之前设置一个合理性测试非常重要。

+   **将过程分解**成更小的步骤也非常有利，并能使得优化 SOP¹ 中的子过程变得更容易且可行。

    请注意，这可能会增加解决方案的复杂性或影响性能（例如，增加处理的令牌数）。为了缓解这一点，尽量使用简洁的提示和更小的模型。

    一般来说，当系统提示的剧烈变化能为 SOP¹ 流程的这一部分带来更好的结果时，分割是个不错的选择。

![](img/0ccb287ce6adee9c671ffc63e24be4ae.png)

挤压 AI 柠檬（由 Dall-E3 创建）

## LLM 实验的解剖学

就个人而言，我更倾向于使用一个简单的 Jupyter Notebook，结合 Python、Pydantic 和 Jinja2，*以轻量的方式*开始：

1.  **使用 Pydantic** 定义模型输出的架构。

1.  使用 **Jinja2** 编写 **提示模板**。

1.  **定义一个结构化输出**格式（使用 **YAML**²）。这将确保模型遵循“思考步骤”并遵循我的 SOP。

1.  **通过**你的 Pydantic 验证确保此输出；如果需要——请重试。

1.  **稳定你的工作**——将代码结构化为功能单元，使用 Python 文件和包。

从更广泛的角度来看，你可以使用不同的工具，比如 [openai-streaming](https://github.com/AlmogBaku/openai-streaming) 来轻松 **利用流式处理（和工具）**，[LiteLLM](https://docs.litellm.ai/docs/) 来拥有一个 **标准化的 LLM SDK**，适用于不同的提供商，或者 [vLLM](https://docs.vllm.ai/) 来 **提供开源 LLM 服务**。

## 通过健全性测试和评估确保质量

一项健全性测试评估你的项目质量，并确保你没有降低已经定义的某个成功率基准。

把你的解决方案/提示想象成一条短毛毯——如果你把它拉得太长，它可能突然无法覆盖以前能够覆盖的某些用例。

为此，定义一组你已经成功覆盖的案例，并确保它保持这样（或者至少值得这样做）。把它当作 [表驱动测试](https://lorenzopeppoloni.com/tabledriventestspy/) 可能会有帮助。

评估“生成性”解决方案（例如，写文本）的成功比使用 LLM 处理其他任务（如分类、实体提取等）要复杂得多。对于这些任务，你可能需要引入更智能的模型（如 GPT4、Claude Opus 或 LLAMA3–70B）作为“裁判”。

另外，尝试让输出先包含“确定性部分”，再输出“生成部分”可能是一个好主意，因为这类输出更容易进行测试：

```py
cities:
  - New York
  - Tel Aviv
vibes:
  - vibrant
  - energetic
  - youthful
target_audience:
  age_min: 18
  age_max: 30
  gender: both
  attributes:
    - adventurous
    - outgoing
    - culturally curious
# ignore the above, only show the user the `text` attr.
text: Both New York and Tel Aviv buzz with energy, offering endless activities, nightlife, and cultural experiences perfect for young, adventurous tourists.
```

有一些前沿的、🤩🤩 有前景的解决方案值得探索。我发现它们在评估基于 RAG 的解决方案时尤其相关：可以看看 [DeepChecks](https://deepchecks.com/)、[Ragas](https://github.com/explodinggradients/ragas) 或 [ArizeAI](https://arize.com/)。

# 做出明智的决策：回顾的意义

在每次主要/定期实验或里程碑后，我们应该停下来并做出**明智的决策**，决定是否继续采用这种方法。

此时，您的实验将有一个明确的成功率基准，您也会了解需要改进的地方。

这是一个很好的时机来开始讨论这个解决方案的产品化影响，并开始进行“产品工作”：

1.  这在产品中会是什么样子？

1.  有哪些限制/挑战？您将如何应对？

1.  目前的延迟是多少？够好吗？

1.  用户体验应如何设计？可以使用哪些 UI 技巧？[流媒体](https://github.com/AlmogBaku/openai-streaming)能否有所帮助？

1.  预计的代币消耗是多少？我们能否使用更小的模型来减少消耗？

1.  优先级是什么？有哪些挑战是不可妥协的？

假设我们达成的*基准*“足够好”，并且我们相信可以解决提出的问题。在这种情况下，我们将继续投资并改善项目，同时*确保它永远不会退化*，并使用理智测试。

![](img/bacac79b87978cd360d070bb0f44464a.png)

（由 Dall-E3 创建）

# 从实验到产品：让您的解决方案落地

最后但同样重要的是，我们必须将我们的工作产品化。像任何其他生产级解决方案一样，我们必须实现生产工程概念，如日志记录、监控、依赖管理、容器化、缓存等。

这是一个庞大的世界，但幸运的是，我们可以借用许多来自传统生产工程的机制，甚至采用许多现有的工具。

话虽如此，在处理涉及 LLM 原生应用的细节时要特别小心：

+   **反馈循环**—我们如何衡量成功？是仅仅一个“点赞/点踩”机制，还是更复杂的机制，考虑到我们解决方案的采用情况？

    收集这些数据也很重要；在未来，这将帮助我们重新定义我们的理智“基准”或通过[动态少量样本](https://arxiv.org/abs/1804.09458)来微调结果，或微调模型。

+   **缓存**—与传统的软件工程不同，当我们在解决方案中引入生成性特征时，缓存可能会变得非常具有挑战性。为缓解这一问题，可以探索缓存相似结果（例如，使用 RAG）和/或减少生成性输出（通过严格的输出架构）。

+   **成本跟踪**—许多公司发现，启动时使用“强大模型”（如 GPT-4 或 Opus）非常有吸引力，然而——在生产中，成本可能迅速上升。避免在最终账单上感到惊讶，并确保衡量输入/输出代币并跟踪工作流的影响（如果没有这些做法——稍后再进行分析可能就会非常困难）

+   **可调试性和追踪**——确保你已经设置好正确的工具，以追踪“有问题”的输入并贯穿整个过程。这通常涉及保存用户输入以供后续调查，并设置一个[追踪系统](https://github.com/traceloop/openllmetry?tab=readme-ov-file)。记住：“与传统软件不同，AI 的失败是悄无声息的！”

# 结束语：你在推动 LLM 原生技术发展中的角色

这可能是文章的结束，但肯定不是我们工作的终结。LLM 原生开发是一个迭代过程，涉及更多的使用案例、挑战和功能，并不断改进我们的 LLM 原生产品。

在你继续进行 AI 开发之旅时，保持灵活，勇敢地进行实验，并始终关注最终用户。与社区分享你的经验和见解，让我们一起推动 LLM 原生应用的可能性边界。继续探索、学习和构建——无限的可能等待着你。

我希望这本指南在你进行 LLM 原生开发的过程中成为了一个有价值的伴侣！我很想听听你的故事——在下面的评论中分享你的成功与挑战吧。💬

如果你觉得这篇文章对你有帮助，请在 Medium 上给它点几个**掌声** 👏并**分享**给你的 AI 爱好者朋友们。你的支持对我意义重大！🌍

让我们继续对话——随时通过电子邮件或[LinkedIn 联系](https://www.linkedin.com/in/almogbaku/) 🤝

特别感谢[Yonatan V. Levin](https://medium.com/u/8735065c2497?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Gal Peretz](https://medium.com/u/532f8dc01db8?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Philip Tannor](https://medium.com/u/5c5d2a69bcdb?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Ori Cohen](https://medium.com/u/4dde5994e6c1?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Nadav](https://medium.com/u/ed1905bd6262?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Ben Huberman](https://medium.com/u/e6ad8abedec9?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Carmel Barniv](https://medium.com/u/6374acbf3a05?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)、[Omri Allouche](https://medium.com/u/bf14cec4d697?source=post_page---user_mention--1fe1e6ef60fd--------------------------------) 和 [Liron Izhaki Allerhand](https://medium.com/u/251cd1007ce8?source=post_page---user_mention--1fe1e6ef60fd--------------------------------)提供的见解、反馈和编辑意见。

**¹SoP**——标准操作程序，这一概念来自于《LLM 三角原则》³

**²YAML** - 我发现使用 YAML 来结构化输出对于 LLM 来说效果更好。为什么？我的理论是，它减少了不相关的标记，行为更像是原生语言。本文深入探讨了这个话题。

**³****LLM 三角原理** - 用于设计和构建 LLM 原生应用的软设计原则；更新 - 最近发布的白皮书，你可以在这里阅读。
