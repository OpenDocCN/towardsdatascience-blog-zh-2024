# Langfuse中的AI代理单元测试

> 原文：[https://towardsdatascience.com/ai-agent-unit-testing-in-langfuse-00d21a680ddc?source=collection_archive---------5-----------------------#2024-06-13](https://towardsdatascience.com/ai-agent-unit-testing-in-langfuse-00d21a680ddc?source=collection_archive---------5-----------------------#2024-06-13)

## 为AI代理创建一个可扩展的测试解决方案，供非编码人员使用

[](https://jacknotjohn.medium.com/?source=post_page---byline--00d21a680ddc--------------------------------)[![Jack Moore](../Images/a2354c65edc7fa01f5fa2bfa22fe6b34.png)](https://jacknotjohn.medium.com/?source=post_page---byline--00d21a680ddc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--00d21a680ddc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--00d21a680ddc--------------------------------) [Jack Moore](https://jacknotjohn.medium.com/?source=post_page---byline--00d21a680ddc--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00d21a680ddc--------------------------------) ·8分钟阅读·2024年6月13日

--

![](../Images/bd9c62b0e085d4c850f81382ec1e69cf.png)

Langfuse是一个用于灵活测试AI代理的有用工具。最近，我们着手实现一个框架来测试基于聊天的AI代理。以下是我们在探索可用工具过程中所经历的过程。

我们将主要集中讨论如何完成这项任务，但在最后，我们会谈到一些仍然面临的挑战，以及现有工具如何更好地支持这种用例的前景。

# 用例概述

在回顾我们如何构建系统之前，我们将快速介绍我们的目标和成功标准。

生成式AI的用例通常容易部署，但难以控制。当部署带有大上下文模型的代理时，模型提示、温度设置、内容审核政策等上游的变化可能会大幅影响其性能。

挑战在于创建一个系统，可以评估代理在不产生幻觉或违反内容政策的情况下完成特定任务的能力。我们将其等同于单元测试，确保你的代理即使在团队可能专注于特定改进的情况下，也能保持完成广泛任务的能力。手动进行这种测试可能不准确、耗时且难以追踪。

因此，我们着手创建一个能够轻松创建这些测试并监控其结果的系统。重要的是，我们希望这个系统能够在最小的代码更改频率下操作，以便产品经理或质量测试人员可以在不接触代码的情况下参与其中。

# 为什么选择Langfuse

我们为搜索设定了几个关键参数：

HIPAA 合规性，因为我们构建的产品以及许多我们的咨询合作伙伴都涉及医疗行业。

低成本，既包括搭建成本，也包括运行成本，因为我们运作得相当精简，我们的合作伙伴也是如此。

发展势头。LLM 可观测性领域正在快速发展。从我们搜索的开始，我们就做好了可能会错的准备，但我们希望通过选择一个有可能与我们一起发展的工具来最小化这种机会。

自定义 LLM 评估能力。能够搭建和运行自定义评估器，这一点令人惊讶地没有在我们找到的所有选项中得到广泛支持，尤其是在开源选项中。

为了简化我们的搜索，我们确定了以下几个在企业和开源类别中符合我们标准的工具，按大致排名顺序列出。

企业

+   [LangSmith](https://docs.smith.langchain.com/evaluation/faq/custom-evaluators)

+   [Galileo](https://www.rungalileo.io/)

+   [Weave](https://github.com/wandb/weave)

开源

+   [Langfuse](https://github.com/langfuse/langfuse)

+   [Empirical](https://docs.empirical.run/introduction)

我们选择 Langfuse，主要因为它可以轻松自行部署，无需与企业销售团队互动，并且我们相信它具备我们需要的关键功能。到目前为止，这个选择证明是正确的。

部署

我们发现部署过程总体上相对简单。Langfuse 提供了一个易于使用的 Docker 镜像，并且有详尽的文档指导如何在本地部署。构建 YAML 文件并部署到 EKS 也非常简单，我们在几个小时内就成功启动了一个演示实例。我们没有为 POC 设置 SSO，因此使用的是开箱即用的基本用户管理功能（功能不多），并依赖匿名化数据来满足安全要求。RDS 上的免费层 PG 数据库能够处理多个用户的许多查询、评估和提示管理。该应用非常轻量级。我们遇到的一些问题包括：

+   SDK 中无法编程方式获取提示列表。这意味着当我们组合各种系统提示或单元测试聊天时，我们必须将提示名称存储在我们为特定用例使用的任何入口点的配置文件中（例如，代理的系统提示中的单元测试列表）。

+   我们没有找到一种方法能够获取提示中用于编译的变量列表。我们使用不同的变量用于不同的系统提示，并且不得不硬编码每一部分数据应编译到哪个提示中，或者进行一些试错。

+   观察记录没有很好地记录。当将分数记录到 Langfuse 时，我们看到可以添加 observationId，但虽然文档整体不错，但并未提供更多的上下文信息。一旦我们弄清楚这些功能的所有可能性后，可能会在未来使用它们。

# 一个无代码单元测试框架

![](../Images/8af623759549191db38e38e8865ccdef.png)

我们如何利用系统提示配置在Langfuse中创建一个中央的、无代码的测试系统的示意图

经过几周的工作，我们建立了一个端到端测试系统。Langfuse提供的功能超出了我们目前的使用范围，但我们重点使用了提示、会话和追踪。

# 将聊天历史作为测试的上下文

在对基于聊天的代理进行测试时，我们的一个关键需求是能够将代理插入到聊天场景的中间，使用之前交换的消息作为上下文。任何自定义提示都可以包括聊天历史，但Langfuse特别简便地实现了这一点。

此外，我们为代理构建了一个聊天界面，使用户能够实时测试并生成新的测试提示以进行评估。这解决了其中一个问题。

注入提示作为上下文的潜在问题是，聊天必须代表模型可能生成的实际输出。

这带来了一个潜在的漏洞：我们作为上下文使用的聊天历史如果模型的底层行为发生变化，则必须刷新。尽管如此，我们认为这种方法比潜在的替代方案更可控和一致，例如让一个代理与另一个代理交互——我们将探索这一点，作为这种系统的另一个扩展。

# 无代码的测试创建与测试运行管理

我们解决的另一个关键挑战是如何在不需要代码的情况下创建一个完整的测试套件。首先，为了定义测试集，我们在系统提示中为代理创建了一个配置对象，其中定义了要运行的测试列表。

这也使我们能够在运行一套测试时将系统提示作为变量传入。像Langfuse这样的系统的主要好处之一是，它能够在其UI中实现提示管理作为代码。为此，可能会注入系统中的后续系统提示也会与配置中的系统提示关联，从而使我们能够在测试过程中强制底层模型进入特定状态，同时增强系统对主提示和后续提示变化的抵抗力。

通过将要运行的测试列表作为系统提示中的配置来管理，我们只需要每个代理一次代码更改。要运行的测试列表可以在Langfuse UI中进行更改和扩展。

每个测试提示都与其评估者作为配置的一部分相关联。每个测试提示至少有一个自定义评估运行，并且这些提示大致遵循以下模板：一个有用的AI评估者，将提供反馈和评分。

```py
You are a helpful AI evaluator who will provide feedback and scoring on the task below.[Describe the scenario and how the agent has been instructed to behave in said scenario]Based on the transcript output, you will determine whether this task was successfully completed.  You will return a JSON object in the following form:-------------Example outputs:{"score": -1, "comment": [Description of an example negative case}{“score”: 1, “comment”: [Description of an example positive case]}------------In this object, score is a number between -1 and 1, with 1 indicating complete success and a -1 indicating complete failure.  The comment is a string indicating your reasoning for the score.-------------BEGIN TRANSCRIPT:{{transcript}}END TRANSCRIPT--------------Do not return any output except the JSON object referenced above.
```

使用这个系统

我们认为这个测试与评估框架是一个合理的折衷方案，用于创建一个低成本、易于操作的系统。我们将其主要应用视为持续集成/持续交付（CI/CD）管道的一部分，确保，或者作为快速评分卡的来源，供那些希望调整系统提示并需要比手动测试更多反馈的人使用。

基于支撑代理和评估器的模型，令牌的使用可能意味着一个完整的测试套件运行，像我们这样的测试套件通常包含数十个测试提示和评估器，可能需要花费数十美元。

控制运行此类系统成本的一种方式是，在迭代提示和工具时，尤其是在进行大量更改以迭代提升性能时，首先从一个较小的模型开始，衡量相对性能，只有在找到鼓舞人心的结果时，才逐步增加测试规模，使用更大的模型。

# Langfuse 印象

总的来说，我们对使用 Langfuse 的决定感到满意。只需相对较少的工作，我们就能部署出符合需求的系统。该系统足够灵活，让我们能够快速定制，以适应我们的用例。

我们已经注意到一些不足之处，希望未来的开发能够解决这些问题：

Langfuse 的用户体验缺乏一些打磨，若进行改进，将大大提升用户的使用体验。举例来说，目前无法复制提示，并且无法通过除名称以外的其他参数来搜索可用的提示。

自托管选项不允许你通过 UI 触发新的测试运行，这意味着操作系统的人需要通过命令行或为此目的开发的其他 UI 来执行此操作。

我们理解这个环境在快速发展，但我们相信这个粗略的框架具有相当好的可移植性，若我们最终决定将其实现到另一个系统中，应该是可行的。

# 未来创新潜力

# AI 生成的测试提示变体

增加测试覆盖面的一种方式是创建我们现有测试提示的变体。像 TestGen-LLM 这样的工具正在该领域崭露头角，但总体而言，利用生成式 AI 测试生成式 AI 仍是一个相对年轻的领域。由于这些负载本质上是 JSON 对象，因此确实可以指示 LLM 创建变体。那么问题是，如何控制这些变体的质量，以确保它们仍然是有效的测试。

# 使用数据集

Langfuse 数据集是一个有趣的工具功能，允许用户将特定的追踪部分链接为模型的输入和预期输出。虽然我们本可以在单元测试中使用类似的工具，但我们发现创建聊天提示作为输入，并一般描述我们在评估提示中寻找的内容，比制作“预期输出”用于数据集评估要更简单。我们认为数据集是用于代码中可评估的测试（例如，聊天机器人在被问到时是否返回了正确的年份？聊天机器人是否返回了有效的 JSON？）的明确方向。我们可能会在未来用于更通用的测试，但我们发现通过分别创建提示来生成新测试会更快。

*感谢阅读！我是* [*Jack Moore*](https://medium.com/u/266c1c6aac8?source=post_page---user_mention--00d21a680ddc--------------------------------)*，* [*Auril.ai*](http://auril.ai)*的创始人兼CEO。这篇文章最初发布在我们的技术博客上，我们将在这里探讨与将生成式AI从概念性的兴趣带到实际价值应用相关的主题。*

*所有观点仅代表我们个人。我们与Langfuse没有任何关联或合作关系。*

*除非另有说明，所有图片均由作者提供。*
