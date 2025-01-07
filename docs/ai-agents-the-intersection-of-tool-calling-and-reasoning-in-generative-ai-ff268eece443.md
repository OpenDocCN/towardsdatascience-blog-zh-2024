# AI 代理：生成性 AI 中工具调用与推理的交汇点

> 原文：[https://towardsdatascience.com/ai-agents-the-intersection-of-tool-calling-and-reasoning-in-generative-ai-ff268eece443?source=collection_archive---------2-----------------------#2024-10-05](https://towardsdatascience.com/ai-agents-the-intersection-of-tool-calling-and-reasoning-in-generative-ai-ff268eece443?source=collection_archive---------2-----------------------#2024-10-05)

## 拆解 AI 中的解决问题与工具驱动的决策过程

[](https://medium.com/@tula.masterman?source=post_page---byline--ff268eece443--------------------------------)[![Tula Masterman](../Images/c36b3740befd5dfdb8719dc6596f1a99.png)](https://medium.com/@tula.masterman?source=post_page---byline--ff268eece443--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ff268eece443--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ff268eece443--------------------------------) [Tula Masterman](https://medium.com/@tula.masterman?source=post_page---byline--ff268eece443--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff268eece443--------------------------------) ·阅读时间：11 分钟·2024年10月5日

--

![](../Images/3279c4d07b6b2c6d9e752246854f9da1.png)

作者与 GPT-4o 创作的图像，描绘了一个位于推理与工具调用交汇点的 AI 代理

# 引言：代理性 AI 的崛起

如今，新的库和低代码平台使得构建 AI 代理（也称为数字工作者）比以往任何时候都更容易。工具调用是推动生成性 AI 模型“代理性”特征的主要能力之一，它扩展了 AI 模型的功能，使其超越了仅限于对话任务。通过执行工具（功能），代理可以代表你采取行动，解决那些需要强大决策能力和与多种外部数据源交互的复杂多步骤问题。

本文聚焦于通过工具调用表达推理，探讨了工具使用中的一些挑战，涵盖了评估工具调用能力的常见方法，并提供了不同模型和代理与工具交互的示例。

# 解决问题的推理表达方式

成功的代理背后有两种关键的推理表达方式：**通过评估和规划进行推理**与**通过工具使用进行推理**。

+   **通过评估和规划进行推理**与智能体通过迭代规划、评估进展并调整方法直到任务完成的能力有关。像[思维链](https://arxiv.org/abs/2201.11903)（CoT）、[ReAct](https://arxiv.org/abs/2210.03629)和[提示分解](https://arxiv.org/abs/2210.02406)等技术，都是为提高模型通过分解任务来战略性推理的能力而设计的。这种类型的推理更具宏观性，确保通过迭代工作并考虑每个阶段的结果来正确完成任务。

+   **通过工具使用进行推理**与智能体有效地与环境互动的能力有关，决定调用哪些工具以及如何构建每个调用。这些工具使得智能体能够检索数据、执行代码、调用 API 等等。这种推理的优势在于正确执行工具调用，而非反思调用结果。

尽管两种推理方式都很重要，但它们不总是需要结合使用以创造强大的解决方案。例如，**OpenAI的新** **o1模型擅长通过评估和规划进行推理**，因为它经过训练，能够使用思维链进行推理。这大大提高了它思考并解决复杂挑战的能力，且在各种基准测试中得到了体现。例如，o1模型在涵盖物理学、生物学和化学的GPQA基准测试中，**超越了人类博士级准确度**，并在**Codeforces**比赛中得分处于**86-93百分位**。虽然o1的推理能力可以用来生成基于文本的响应，建议根据工具描述选择工具，但目前它缺乏显式的工具调用能力（至少现在是如此！）。

相反，**许多模型专门为通过工具使用进行推理而微调**，使它们能够生成函数调用并非常有效地与API进行互动。这些模型专注于在正确的时间以正确的格式调用正确的工具，但通常并不设计得像o1那样全面评估其结果。[**伯克利函数调用排行榜**](https://gorilla.cs.berkeley.edu/leaderboard.html) **(BFCL)是一个很好的资源，用于比较不同模型在函数调用任务中的表现**。它还提供了一个**评估套件，用于比较你自己微调的模型**在各种具有挑战性的工具调用任务中的表现。事实上，[最新数据集BFCL v3](https://huggingface.co/datasets/gorilla-llm/Berkeley-Function-Calling-Leaderboard)刚刚发布，现在包括了[多步骤、多轮函数调用](https://gorilla.cs.berkeley.edu/blogs/13_bfcl_v3_multi_turn.html)，进一步提高了基于工具的推理任务的标准。

这两种推理方式各自都非常强大，当结合在一起时，它们有可能创建能够有效分解复杂任务并自主与环境互动的代理。有关更多AI代理架构的推理、规划和工具调用的例子，请[查看我团队在ArXiv上的调研论文](https://arxiv.org/abs/2404.11584)。

# 工具调用的挑战：应对复杂的代理行为

构建强大且可靠的代理需要克服许多不同的挑战。在解决复杂问题时，代理通常需要同时平衡多个任务，包括规划、在正确的时间与合适的工具互动、正确格式化工具调用、记住前一步的输出、避免重复循环，以及遵循指导原则以保护系统免受越狱/提示注入等攻击。

**过多的需求容易让单个代理不堪重负，这导致了一个日益增长的趋势——看似由一个代理提供服务的系统，实际上是由多个代理和提示共同协作、分工合作来完成任务**。这种分工使得任务能够被分解，并由不同的模型和代理并行处理，每个代理专门解决某个特定问题。

在这里，具备优秀工具调用能力的模型发挥了作用。虽然工具调用是一种强大的方式，可以使代理更加高效，但它也带来了自己的一系列挑战。代理需要理解可用的工具，从一组可能相似的选项中选择合适的工具，准确格式化输入，按照正确的顺序调用工具，并可能需要整合来自其他代理或人类的反馈或指令。许多模型经过专门微调，用于工具调用，使它们能够专注于在合适的时机高精度地选择功能。

**在微调模型以进行工具调用时的一些关键考虑因素包括：**

1.  **正确的工具选择**：模型需要理解可用工具之间的关系，在适用时进行嵌套调用，并在存在其他相似工具的情况下选择正确的工具。

1.  **处理结构性挑战**：尽管大多数模型使用JSON格式进行工具调用，但也可以使用YAML或XML等其他格式。需要考虑模型是否需要跨格式进行泛化，或者是否仅使用一种格式。无论使用哪种格式，模型都需要为每次工具调用包含适当的参数，并可能在后续调用中使用前一次调用的结果。

1.  **确保数据集的多样性和可靠的评估**：使用的数据集应该具有多样性，并涵盖多步骤、多轮次的函数调用的复杂性。应进行适当的评估，以防止过拟合并避免基准污染。

# 用于评估工具调用的常见基准

随着工具使用在语言模型中的重要性不断增长，许多数据集应运而生，以帮助评估和改进模型的工具调用能力。目前最流行的两个基准是伯克利功能调用排行榜和Nexus功能调用基准，这两个基准都是[Meta用来评估其Llama 3.1系列模型表现的](https://arxiv.org/pdf/2407.21783)。最近的一篇论文，[ToolACE](https://arxiv.org/abs/2409.00920)，展示了如何利用智能体创建一个多样化的数据集，以进行模型工具使用的微调和评估。

让我们更详细地探讨这些基准：

+   **伯克利功能调用排行榜（**[**BFCL**](https://gorilla.cs.berkeley.edu/leaderboard.html)**）：** BFCL包含2,000个跨多个编程语言的问题-功能-答案对。目前有3个版本的BFCL数据集，每个版本都进行了增强，以更好地反映现实世界的场景。例如，[BFCL-V2](https://gorilla.cs.berkeley.edu/blogs/12_bfcl_v2_live.html)，于2024年8月19日发布，包含了用户贡献的样本，旨在解决与数据集污染相关的评估挑战。[BFCL-V3](https://gorilla.cs.berkeley.edu/blogs/13_bfcl_v3_multi_turn.html)于2024年9月19日发布，增加了多轮、多步工具调用的评估标准。这对于智能体应用至关重要，因为在这些应用中，模型需要在一定时间内进行多次工具调用才能成功完成任务。[关于在BFCL上评估模型的说明可以在GitHub上找到](https://github.com/ShishirPatil/gorilla)，[最新数据集可在HuggingFace上获取](https://huggingface.co/datasets/gorilla-llm/Berkeley-Function-Calling-Leaderboard)，[当前排行榜可在此访问](https://gorilla.cs.berkeley.edu/leaderboard.html)。伯克利团队还发布了多个版本的Gorilla Open-Functions模型，专门针对功能调用任务进行了微调。

+   **Nexus功能调用基准：** 该基准评估模型在零-shot功能调用和API使用方面的表现，涵盖九个不同任务，这些任务被分为单一、并行和嵌套工具调用三大类。Nexusflow发布了专为功能调用设计的模型NexusRaven-V2。[Nexus基准可以在GitHub上找到](https://github.com/nexusflowai/NexusRaven-V2/tree/master#benchmarks)，相应的[排行榜可在HuggingFace上查看](https://huggingface.co/spaces/Nexusflow/Nexus_Function_Calling_Leaderboard)。

+   **ToolACE：** [ToolACE论文](https://arxiv.org/pdf/2409.00920)展示了一种创新的方法，克服了与收集现实世界数据以进行函数调用相关的挑战。研究团队创建了一个代理管道，以生成一个包含超过26,000个不同API的合成数据集，用于工具调用。该数据集包括单一、并行和嵌套工具调用的示例，以及非工具基础的交互，支持单轮和多轮对话。团队发布了一个针对这些复杂工具调用任务优化过的Llama-3.1–8B-Instruct版本，[ToolACE-8B](https://huggingface.co/Team-ACE/ToolACE-8B)，旨在处理这些任务。 [ToolACE数据集的子集可以在HuggingFace上找到](https://huggingface.co/datasets/Team-ACE/ToolACE)。

这些基准测试有助于我们评估通过工具调用表达的模型推理能力。这些基准测试和精调模型反映了一个日益增长的趋势，旨在开发更专业化的模型以执行特定任务，并通过扩展它们与现实世界的互动能力来提高LLM能力。

# 工具调用的实际示例

如果您有兴趣了解工具调用的实际应用，以下是一些按易用性组织的示例，从简单的内建工具到使用精调模型和具有工具调用能力的代理，助您入门。

**第1级 — ChatGPT**：开始并实时查看工具调用而无需自己定义任何工具的最佳地方是通过ChatGPT。在这里，您可以通过聊天界面使用GPT-4o来调用并执行网页浏览工具。例如，当询问“本周最新的AI新闻是什么？”时，ChatGPT-4o会进行网页搜索，并根据找到的信息返回答案。*请记住，新款o1模型尚不具备工具调用能力，无法进行网页搜索。*

![](../Images/d4e671c962183b932578d58a05a894c1.png)

作者图片 2024年9月30日

虽然这个内建的网页搜索功能很方便，但大多数使用场景仍然需要定义自定义工具，这些工具可以直接集成到您自己的模型工作流和应用程序中。这将带我们进入下一个复杂度级别。

**第2级 — 使用具有工具调用能力的模型并定义自定义工具**：

这个级别涉及使用具有工具调用能力的模型，以了解模型如何有效地选择和使用这些工具。需要注意的是，**当一个模型被训练用于工具调用时，它只会生成工具调用的文本或代码，并不会实际执行代码。需要有模型外部的系统来调用工具，正是在这一点——将生成与执行结合起来的地方——我们从语言模型能力过渡到代理系统。**

为了感受模型如何表达工具调用，我们可以转向Databricks Playground。例如，我们可以选择模型Llama 3.1 405B，并给予它访问样本工具get_distance_between_locations和get_current_weather的权限。当提示用户消息“我将从洛杉矶去纽约，这两个城市有多远？纽约的天气怎么样？我想为到达那里做准备”时，模型决定调用哪些工具以及传递哪些参数，以便有效地回复用户。

![](../Images/ccc6160bc9393164ecb15310a765b94b.png)

作者于2024年10月2日描绘了使用Databricks Playground进行示例工具调用的图像

在这个示例中，模型建议了两个工具调用。由于模型无法执行这些工具，用户需要填入一个示例结果来模拟工具输出（例如，“2500”表示距离，“68”表示天气）。然后，模型使用这些模拟的输出回复用户。

这种使用Databricks Playground的方法允许你观察模型如何使用自定义定义的工具，并且是测试函数定义的绝佳方式，尤其是在将其实现到启用工具调用的应用程序或代理之前。

在Databricks Playground之外，我们可以直接通过代码观察和评估在像HuggingFace这样的平台上，不同模型如何有效地使用工具。例如，我们可以加载不同的模型，如Llama 3.2–3B-Instruct、ToolACE-8B、NexusRaven-V2–13B等，从HuggingFace获取它们，给予相同的系统提示、工具和用户消息，然后观察并比较每个模型返回的工具调用。这是理解不同模型如何推理使用自定义定义工具的绝佳方式，能够帮助你确定哪些工具调用模型最适合你的应用程序。

下面是一个示例，展示了Llama-3.2–3B-Instruct基于以下工具定义和用户消息生成的工具调用，其他模型可以按照相同的步骤进行比较生成的工具调用。

```py
import torch
from transformers import pipeline

function_definitions = """[
    {
        "name": "search_google",
        "description": "Performs a Google search for a given query and returns the top results.",
        "parameters": {
            "type": "dict",
            "required": [
                "query"
            ],
            "properties": {
                "query": {
                    "type": "string",
                    "description": "The search query to be used for the Google search."
                },
                "num_results": {
                    "type": "integer",
                    "description": "The number of search results to return.",
                    "default": 10
                }
            }
        }
    },
    {
        "name": "send_email",
        "description": "Sends an email to a specified recipient.",
        "parameters": {
            "type": "dict",
            "required": [
                "recipient_email",
                "subject",
                "message"
            ],
            "properties": {
                "recipient_email": {
                    "type": "string",
                    "description": "The email address of the recipient."
                },
                "subject": {
                    "type": "string",
                    "description": "The subject of the email."
                },
                "message": {
                    "type": "string",
                    "description": "The body of the email."
                }
            }
        }
    }
]
"""

# This is the suggested system prompt from Meta
system_prompt = """You are an expert in composing functions. You are given a question and a set of possible functions. 
Based on the question, you will need to make one or more function/tool calls to achieve the purpose. 
If none of the function can be used, point it out. If the given question lacks the parameters required by the function,
also point it out. You should only return the function call in tools call sections.

If you decide to invoke any of the function(s), you MUST put it in the format of [func_name1(params_name1=params_value1, params_name2=params_value2...), func_name2(params)]\n
You SHOULD NOT include any other text in the response.

Here is a list of functions in JSON format that you can invoke.\n\n{functions}\n""".format(functions=function_definitions)
```

![](../Images/c611f529dd5a8b70118f307696a7b85b.png)

作者的示例输出，展示了从Llama 3.2–3B-Instruct生成的工具调用

从这里我们可以进入第3级，在该级别我们定义执行语言模型生成的工具调用的代理。

**第3级代理（调用/执行LLM工具调用）**：代理通常通过规划和执行以及工具调用来表达推理，使其成为基于AI的应用程序中日益重要的组成部分。使用像LangGraph、AutoGen、Semantic Kernel或LlamaIndex这样的库，你可以快速创建一个代理，使用像GPT-4o或Llama 3.1–405B这样的模型，它们既支持与用户的对话，又支持工具执行。

查看这些指南，了解一些激动人心的代理实例：

+   LangGraph: [使用Llama 3的本地RAG代理](https://langchain-ai.github.io/langgraph/tutorials/rag/langgraph_adaptive_rag_local/)

+   AutoGen: [解决需要网页信息的任务](https://github.com/microsoft/autogen/blob/main/notebook/agentchat_web_info.ipynb)

+   语义内核： [在语义内核中使用代理入门](https://github.com/microsoft/semantic-kernel/blob/main/dotnet/samples/GettingStartedWithAgents/README.md)

+   LlamaIndex: [代理使用模式文档](https://docs.llamaindex.ai/en/stable/module_guides/deploying/agents/usage_pattern/)

# 结论：

代理系统的未来将由具有强大推理能力的模型驱动，这些模型使它们能够有效地与环境互动。随着该领域的发展，我预计我们将继续看到越来越多的小型专用模型，它们将专注于特定任务，如工具调用和规划。

在构建代理时，考虑模型大小的当前限制是非常重要的。例如，根据[Llama 3.1模型卡片](https://www.llama.com/docs/model-cards-and-prompt-formats/llama3_1)，Llama 3.1–8B模型对于既需要保持对话又需要调用工具的任务并不可靠。相反，应该使用具有70B+参数的更大模型来处理这些任务。结合其他针对小型语言模型微调的研究表明，较小的模型可能最适合作为专门的工具调用者，而更大的模型则可能更适合进行更高级的推理。通过结合这些能力，我们可以构建出越来越有效的代理，提供无缝的用户体验，并使人们能够在专业和个人事务中利用这些推理能力。

*有兴趣进一步讨论或合作吗？请在* [*LinkedIn*](https://www.linkedin.com/in/tula-masterman/)*上联系我们！*
