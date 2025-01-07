# 构建一个能够写入 Google Docs 的研究代理（第一部分）

> 原文：[https://towardsdatascience.com/building-a-research-agent-that-can-write-to-google-docs-part-1-4b49ea05a292?source=collection_archive---------6-----------------------#2024-11-20](https://towardsdatascience.com/building-a-research-agent-that-can-write-to-google-docs-part-1-4b49ea05a292?source=collection_archive---------6-----------------------#2024-11-20)

![](../Images/467c2bf3a8c599809df9567803dcb8fe.png)

Dalle-3对“一个古怪的AI助手在辛勤工作中检查文档”的诠释。图片由作者生成。

## 可能帮助你完成作业的工具

[](https://medium.com/@rmartinshort?source=post_page---byline--4b49ea05a292--------------------------------)[![Robert Martin-Short](../Images/e3910071b72a914255b185b850579a5a.png)](https://medium.com/@rmartinshort?source=post_page---byline--4b49ea05a292--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4b49ea05a292--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4b49ea05a292--------------------------------) [Robert Martin-Short](https://medium.com/@rmartinshort?source=post_page---byline--4b49ea05a292--------------------------------)

·发表于[面向数据科学](https://towardsdatascience.com/?source=post_page---byline--4b49ea05a292--------------------------------) ·阅读时间15分钟·2024年11月20日

--

***本文是两部分系列的第一篇，我们将使用LangGraph和Tavily构建一个简单的研究代理，能够编写并优化简短的文章。为了跟踪代理生成的计划、文章和评论，我们添加了通过编程方式创建和编辑Google Docs的功能。在本文中，我们将重点介绍代理部分，将Google Docs连接的部分留到第二篇文章中。你可以在[***这里***](https://github.com/rmartinshort/research_assist)***找到所有相关代码。***

大型语言模型（LLMs）正在快速应用于与分析师和研究人员相关的各种场景，特别是在文本信息的提取、组织和总结方面。无论是商业界还是开源社区，都在不断简化构建和扩展所谓“代理型”应用程序的过程，在这些应用程序中，LLM充当（希望是）熟练的分析师角色，并做出半自主决策。例如，在一个聊天机器人应用程序中，如果用户提出一个复杂或多步骤的问题，LLM可能需要设计一个行动计划，正确查询多个外部工具——可能是计算器、网页搜索工具、向量数据库等——汇总结果并生成答案。

这种系统通常被认为使用了[ReAct框架](https://www.promptingguide.ai/techniques/react)的提示工程方法，其中“ReAct”代表“Reasoning-Action”（推理-行动）。基本上，提示的结构和顺序迫使LLM以非常有条理的方式回答问题，首先通过表达一个思想（通常是攻击计划），然后执行一个行动，再观察结果。在代理系统中，这个过程可以不断迭代，直到LLM认为已经得出了一个可接受的答案。

在这一系列文章中，我们将使用[LangGraph](https://www.langchain.com/langgraph)库和[Tavily](https://tavily.com/)搜索工具，构建一个简单的研究助手，展示一些这些概念，并且可能对我们那些希望快速生成关于任何主题的简洁、写得好的报告的人有帮助。我们的代理将受到同行评审研究中的计划 -> 研究 -> 写作 -> 提交 -> 审阅 -> 修订周期的启发，你可以在[这里](https://github.com/rmartinshort/research_assist/blob/main/research_assist/researcher/prompts.py)查看这些不同部分的提示。

为了让系统感觉更完整，我们还将添加将生成的材料自动添加到Google文档的功能，详细内容请参见[第2部分](/building-a-research-assistant-that-can-write-to-google-docs-part-2-ac9dcacff4ff)。这应该被视为一个附加功能，而不是代理的集成组件，但它本身也很有趣，因此也可以作为一篇独立的文章阅读。

# 1\. 我们的研究助手应该做些什么？

在看看我们如何构建这个助手以及它的“代理性”意味着什么之前，我们应该简要思考一下我们希望它做些什么。目标是构建一个可以计划并撰写关于给定主题的简短、信息丰富的文章，然后通过审阅和修订来改进自己的工作的系统。

为什么？主要这只是一次技术探索，但将LLM用作半自主研究员是一个活跃的研究领域，并且产生了一些有趣的项目，如[GPT-researcher](https://github.com/assafelovic/gpt-researcher)。它们有潜力加速分析师、学生、作家和研究人员的工作——尽管当然，如果目标是人类学习，仔细阅读、做笔记和讨论是不可替代的，AI无法取而代之。

像GPT4、Anthropic Claude Sonnet、Meta Llama 3、Google Gemini Pro等大型语言模型（LLM）已经能够通过一个简单的提示写出很棒的文章。然而，这些LLM存在知识截止问题，因此需要访问额外的工具来获取最新信息，比如关于时事新闻的内容。有许多服务——特别是像Perplexity、ChatGPT（现在可以通过chat.com访问）和Google的AI概览这些工具，它们已经具备了这种能力，但它们更倾向于提供快速总结，而不是精心编写的研究报告。

在这里，我们假设多次的审阅和修改将提升LLM生成文章的质量。这当然是人类写作中的工作方式。我们的助手将有以下几个组件，每个组件都有自己的指令提示。

+   **规划者。** 将一个定义不清的任务转化为结构化的文章计划

+   **研究员。** 接受计划并在互联网上搜索相关内容。

+   **写作者。** 利用计划、检索到的内容和自身知识来撰写报告

+   **审阅者。** 阅读报告并提供建设性的批评

+   **编辑。** 阅读报告和审阅者的批评，决定报告是否需要修改。如果需要，报告将被送回研究员和写作者阶段。

在我们的实现中，这些组件将调用相同的LLM，即GPT4o-mini，但在实际应用中，它们完全可以使用不同的、更专业的模型。

输出将是一份写得很好的、信息丰富的报告——最好附带参考文献——我们可以程序化地将其放入Google文档中保存。通过调整提示，可以轻松修改我们研究员的“个性”。编辑特别重要，因为它是整个过程的把关人。如果我们让编辑非常严格，系统可能需要经过多次修改才能被接受。严格的编辑在多大程度上能提高结果质量？这是一个非常有趣的问题，正如他们所说，这超出了当前工作的范围！

# 2\. 代理的结构

我们的研究助手在很大程度上基于[这门关于LangGraph的优秀短期课程](https://www.deeplearning.ai/short-courses/ai-agents-in-langgraph/)。LangGraph是一个LLM编排库，旨在让我们更容易设计和构建可靠的代理。关于LangGraph与LangChain的深入对比，我推荐这篇优秀的[文章](/ai-agent-workflows-a-complete-guide-on-whether-to-build-with-langgraph-or-langchain-117025509fa0)。

代理究竟是什么？似乎社区尚未达成统一定义，但至少广义上来说，我们可以说代理是一个[多步骤的系统，在这个系统中，LLM被允许对结果做出有意义的决策](https://blog.langchain.dev/what-is-an-agent/)。这使得它比链条更复杂（并且可能更不可预测），链条只是预先定义的一组LLM调用按顺序执行。

在代理框架中，LLM对于如何解决其所给定的问题有一定的自主性，可能通过选择合适的工具来调用，或者决定在解决方案足够好时何时停止改进。从这个意义上讲，LLM更像是系统的大脑，更像一个人类分析师，而不仅仅是一个API调用。这里的一个有趣挑战是，尽管代理可以自由做出决策，但它们通常嵌入在或与传统的软件系统交互，这些系统需要结构化的输入和输出。因此，迫使代理以这些其他系统能够理解的方式返回答案非常重要，无论它做出了什么决策。

对于在LangGraph上下文中讨论代理的更深入内容，这份[文档](https://langchain-ai.github.io/langgraph/concepts/#graphs)非常有帮助。我们的研究代理将是一个相当简单的代理（部分原因是我也在学习这些材料！），但希望它能成为通向更复杂系统的一块垫脚石。

在LangGraph中，我们将系统的逻辑定义为一个图，其中包含节点和边。节点是进行LLM调用的地方，边则将信息从一个节点传递到下一个节点。边可以是有条件的，意味着它们可以根据做出的决策将信息指向不同的节点。信息以由状态定义的结构化格式在节点之间传递。

我们的研究助手只有一个阶段，叫做`AgentState`，看起来像这样：

```py
class AgentState(TypedDict):
    """
    A dictionary representing the state of the research agent.

    Attributes:
        task (str): The description of the task to be performed.
        plan (str): The research plan generated for the task.
        draft (str): The current draft of the research report.
        critique (str): The critique received for the draft.
        content (List[str]): A list of content gathered during research.
        revision_number (int): The current revision number of the draft.
        max_revisions (int): The maximum number of revisions allowed.
        finalized_state (bool): Indicates whether the report is finalized.
    """

    task: str
    plan: str
    draft: str
    critique: str
    content: List[str]
    editor_comment: str
    revision_number: int
    max_revisions: int
    finalized_state: bool
```

这是存储与我们问题相关的所有信息的地方，并且可以通过LLM在图的某个节点内部进行更新。

现在我们可以定义一些节点。在代码中，所有节点都保存在`AgentNodes`类中，这只是我发现的一个有用的方式来对它们进行分组。例如，规划节点看起来像这样：

```py
 def plan_node(self, state: AgentState) -> Dict[str, str]:
        """
        Generate a research plan based on the current state.

        Args:
            state (AgentState): The current state of the research agent.

        Returns:
            Dict[str, str]: A dictionary containing the generated research plan.
        """
        messages = [
            SystemMessage(content=ResearchPlanPrompt.system_template),
            HumanMessage(content=state["task"]),
        ]
        response = self.model.invoke(messages)
        return {"plan": response.content}
```

注意它如何接收一个`AgentState`并返回对其某个组件的修改，即研究计划的文本。当这个节点被运行时，计划会被更新。

节点函数中的代码使用标准的LangChain语法。`self.model`是`ChatOpenAI`的一个实例，像这样：

```py
model = ChatOpenAI(
    model="gpt-4o-mini", temperature=0, api_key=secrets["OPENAI_API_KEY"]
)
```

这个提示由来自`ResearchPlanPrompt`数据类的系统消息与AgentState的“task”元素拼接而成，后者是用户提供的研究课题。计划提示看起来像这样。

```py
@dataclass
class ResearchPlanPrompt:
    system_template: str = """
    You are an expert writer tasked with creating a high-level outline for a research report.
    Write such an outline for the user-provided topic. Include relevant notes or instructions for each section.
    The style of the research report should be geared towards the educated public. It should be detailed enough to provide
    a good level of understanding of the topic, but not unnecessarily dense. Think of it more like a whitepaper to be consumed 
    by a business leader rather than an academic journal article. 
    """
```

需要为以下任务创建类似的节点：

+   **进行研究**。在这里，我们使用LLM将研究任务转换为一系列查询，然后使用Tavily搜索工具在线查找答案并将其保存在AgentStage的“content”下。此过程将在第2节中详细讨论。

+   **撰写报告**。在这里，我们利用任务名称、研究计划、研究内容以及任何先前的审稿人评论来实际撰写研究报告。这些内容会保存在AgentState的“draft”下。每次运行时，`revision_number`指示器都会更新。

+   **审查报告。** 调用LLM来批评研究报告，并将审查保存到“critique”下。

+   **根据反馈进行更多的研究**。这将处理原始草稿和审查意见，并为Tavily生成更多的查询，帮助系统解决审稿人评论。再一次，这些信息会保存在“content”下。

+   **做出决策**，判断报告是否满足审稿人的评论。LLM会根据编辑提示的指导做出是/否决策，并解释其推理过程。

+   **虚拟节点**，用于拒绝或接受研究。一旦我们到达这两个节点中的任何一个，我们就可以结束流程。最终的研究报告可以从AgentState中提取。

我们需要在图中的编辑节点处创建一个条件边：如果编辑器选择是，我们进入已接受节点。如果选择否，我们返回审查节点。

为了定义此逻辑，我们需要创建一个函数在条件边内运行。我选择将其放入一个AgentEdges类中，但这不是必须的。

```py
 def should_continue(state: AgentState) -> str:
        """
        Determine whether the research process should continue based on the current state.

        Args:
            state (AgentState): The current state of the research agent.

        Returns:
            str: The next state to transition to ("to_review", "accepted", or "rejected").
        """
        # always send to review if editor hasn't made comments yet
        current_editor_comments = state.get("editor_comment", [])
        if not current_editor_comments:
            return "to_review"

        final_state = state.get("finalized_state", False)
        if final_state:
            return "accepted"
        elif state["revision_number"] > state["max_revisions"]:
            logger.info("Revision number > max allowed revisions")
            return "rejected"
        else:
            return "to_review"
```

在代码中，整个图的设置如下所示

```py
from research_assist.researcher.AgentComponents import (
    AgentNodes,
    AgentState,
    AgentEdges,
)
# this is the predefined end node
from langgraph.graph import END

agent = StateGraph(AgentState)
nodes = AgentNodes(model, searcher)
edges = AgentEdges()

## Nodes
agent.add_node("initial_plan", nodes.plan_node)
agent.add_node("write", nodes.generation_node)
agent.add_node("review", nodes.review_node)
agent.add_node("do_research", nodes.research_plan_node)
agent.add_node("research_revise", nodes.research_critique_node)
agent.add_node("reject", nodes.reject_node)
agent.add_node("accept", nodes.accept_node)
agent.add_node("editor", nodes.editor_node)

## Edges
agent.set_entry_point("initial_plan")
agent.add_edge("initial_plan", "do_research")
agent.add_edge("do_research", "write")
agent.add_edge("write", "editor")

## Conditional edges
agent.add_conditional_edges(
  "editor",
  edges.should_continue,
  {"accepted": "accept", "to_review": "review", "rejected": "reject"},
)
agent.add_edge("review", "research_revise")
agent.add_edge("research_revise", "write")
agent.add_edge("reject", END)
agent.add_edge("accept", END)
```

在数据可以流经图之前，图必须被编译。从文档中的理解来看，它只是对图的结构做一些简单检查，并返回一个`CompiledGraph`对象，该对象具有像`stream`和`invoke`这样的函数。这些方法允许你将输入传递给起始节点，起始节点通过上面的代码中的`set_entry_point`来定义。

在构建这些图时，将所有节点和边可视化在笔记本中非常有帮助。这可以通过以下命令实现。

```py
from IPython.display import Image

Image(agent.compile().get_graph().draw_png())
```

[LangGraph提供了几种不同的绘制图形的方式](https://langchain-ai.github.io/langgraph/how-tos/visualization/)，具体取决于你安装的可视化包。我使用的是pygraphviz，可以通过以下命令在M系列Mac上安装。

```py
brew install graphviz
pip install -U --no-cache-dir  \
        --config-settings="--global-option=build_ext" \
        --config-settings="--global-option=-I$(brew --prefix graphviz)/include/" \
        --config-settings="--global-option=-L$(brew --prefix graphviz)/lib/" \
        pygraphviz
```

![](../Images/234af656591507969c2639052529c85b.png)

我们代理的控制流可视化。节点是LLM调用发生的地方，而边表示信息流动。图像由作者生成。

我们如何测试代理？最简单的方法是使用一些AgentState组件的初始值（例如任务、最大修订次数和修订号）调用`invoke`，这些值将进入图的入口节点。

```py
graph = agent.compile()
res = graph.invoke(
    {
        "task": "What are the key trends in LLM research and application that you see in 2024",
        "max_revisions": 1,
        "revision_number": 0,
    }
)
```

经过一段时间（如果`max_revisions`设置为较大值，可能需要几分钟），这将返回一个包含所有组件的代理状态字典。我正在使用gpt4o-mini进行此操作，结果非常令人印象深刻，尽管关于“审查”和“编辑器”组件是否能真正提高文章质量的问题仍有争议，我们将在第三节中讨论这个问题。

如果我们希望更深入了解图中每个阶段节点的输入和输出怎么办？这是调试和解释性非常重要的，特别是在图形变得越来越复杂或我们希望将其部署到生产环境时。幸运的是，LangGraph提供了一些很棒的工具，这些工具在其文档的[持久性](https://langchain-ai.github.io/langgraph/concepts/persistence/)和[流式传输](https://langchain-ai.github.io/langgraph/concepts/streaming/)部分中有介绍。一个最小的实现可能类似于下面的样子，在这里我们使用内存存储来跟踪图的每个阶段的更新。

```py
from langgraph.store.memory import InMemoryStore
from langgraph.checkpoint.memory import MemorySaver
import uuid

checkpointer = MemorySaver()
in_memory_store = InMemoryStore()
graph = agent.compile(checkpointer=checkpointer, store=self.in_memory_store)

# Invoke the graph
user_id = "1"
config = {"configurable": {"thread_id": "1", "user_id": user_id}}
namespace = (user_id, "memories")

for i, update in enumerate(graph.stream(
  {
     "task": task_description,
     "max_revisions": max_revisions,
     "revision_number": 0,
  }, config, stream_mode="updates"
        )):
   # print the data that just got generated 
   print(update)
   memory_id = str(uuid.uuid4())
   # store the data that just got generated in memory
   self.in_memory_store.put(namespace, memory_id, {"memory": update})
   results.append(update)
```

更复杂的应用程序将从节点内部访问存储，允许聊天机器人回忆与某个用户的先前对话。例如，在这里我们只是使用内存来保存每个节点的输出，这些输出可以用于调试目的查看。我们将在最后一节中进一步探讨这个问题。

# **3\. “*do_research*”节点中有什么？Tavily搜索的强大功能**

也许上述控制流中最有趣的部分是`do_research`和`research_revise`节点。在这两个节点中，我们使用LLM生成与任务相关的一些网页搜索查询，然后我们使用[Tavily](https://docs.tavily.com/docs/welcome) API实际进行搜索。Tavily是一个相对较新的服务，提供针对AI代理优化的搜索引擎。实际上，这意味着该服务返回来自网站的相关文本块，而不是像典型的搜索引擎API那样仅返回网址列表（这些网址需要被抓取和解析）。

在背后，Tavily很可能使用网页抓取工具和LLM来提取与用户搜索相关的内容，但所有这些都被抽象化了。你可以在[这里](https://app.tavily.com/home)注册Tavily的免费“研究员”计划，获得1000次免费的API调用。不幸的是，超过此次数后，你需要支付月费才能继续使用，可能只有在商业用例中才值得这样做。

让我们来看一个使用与`AgentNodes.research_plan_node`中非常相似的代码的例子。

```py
 from langchain_core.messages import (
    SystemMessage,
    HumanMessage,
)
from research_assist.researcher.prompts import (
    ResearchPlanPrompt,
)
from langchain_openai import ChatOpenAI
from tavily import TavilyClient

class Queries(BaseModel):
    """
    A model representing a list of search queries.

    Attributes:
        queries (List[str]): A list of search queries to be executed.
    """

    queries: List[str]

# set up task
task = """
What are the key trends in LLM reseach and application that you see in 2024
"""

# set up LLM and Tavily
model = ChatOpenAI(
    model="gpt-4o-mini", temperature=0, api_key=secrets["OPENAI_API_KEY"]
)
tavily = TavilyClient(api_key=secrets["TAVILY_API_KEY"])

# generate some queries relevant to the task
queries = agent.nodes.model.with_structured_output(Queries).invoke(
            [
                SystemMessage(content=ResearchPlanPrompt.system_template),
                HumanMessage(content=task),
            ]
)
```

这会生成5个与我们定义的任务相关的搜索查询，结果如下所示：

```py
['key trends in LLM research 2024',
 'LLM applications 2024',
 'latest developments in LLM technology 2024',
 'future of LLMs 2024',
 'LLM research advancements 2024']
```

接下来，我们可以对这些查询中的每一个调用Tavily搜索。

```py
response = tavily.search(query=queries[0], max_results=2)
```

这将提供一个格式良好的结果，包含网址、标题和文本块。

![](../Images/d53f7c7858080ce176276d07bcf8a46c.png)

来自Tavily搜索的示例结果。图片由作者生成。

这是一个非常强大且易于使用的搜索工具，可以让LLM应用程序访问网络，而无需额外的工作！

在我们的研究员代理中，我们目前只使用内容字段，并将其提取并附加到一个列表中，该列表被传递到AgentState中。然后，这些信息会被注入到用于写作节点的提示中，从而允许LLM在生成报告时访问这些信息。

Tavily 搜索可以做的事情还很多，但要注意，实验使用它会迅速消耗你的免费 API 调用。事实上，对于我们的报告写作任务，有很多应用场景 Tavily 调用可能不是必须的（即 LLM 已经有足够的知识来写报告），所以我建议添加一个额外的条件边，使系统在判断不需要进行网络搜索时跳过 `do_research` 和 `research_revise` 节点。我可能很快会在仓库中更新这个修改。

# **4. 演示一个例子**

为了巩固我们刚刚学到的内容，让我们通过一个实际的例子来演示研究人员的工作，使用与上面相同的任务。

首先，我们导入库并设置我们的 LLM 和搜索模型

```py
from research_assist.researcher.Agent import ResearchAgent, load_secrets
from langchain_openai import ChatOpenAI
from tavily import TavilyClient

secrets = load_secrets()
model = ChatOpenAI(
    model="gpt-4o-mini", temperature=0, api_key=secrets["OPENAI_API_KEY"]
)
tavily = TavilyClient(api_key=secrets["TAVILY_API_KEY"])

agent = ResearchAgent(model, tavily)
```

现在我们可以在任务上运行代理，并给它一个最大的修订次数。

```py
task = """
What are the key trends in LLM reseach and application that you see in 2024
"""
result = agent.run_task(task_description=task,max_revisions=3)
```

现在代理将运行它的任务，这可能需要大约一分钟。已经添加了日志记录以显示它正在做什么，重要的是，结果正在保存到 `in_memory_store` 中，我们在第二部分末尾看到了它。

最终报告有几种方式可以访问。它存储在结果列表中，可以像这样在笔记本中可视化。

```py
Markdown(result[-3]['write']['draft'])
```

它也存储在代理的记忆中，和所有其他输出一起。我们可以按照以下方式访问它。

```py
agent.in_memory_store.search(("1", "memories"))[-3].dict()
```

报告本身大约有 1300 字——有点多，无法在这里复制——但我已经将其粘贴到了仓库的[这里](https://github.com/rmartinshort/research_assist/tree/main/research_assist/examples)。我们也可以看看编辑器在经过一轮修订后的看法。

```py
editor_comments = agent.in_memory_store.search(("1", "memories"))[-2].dict()
```

```py
{'value': {'memory': {'editor': {'editor_comment': 
'The report has addressed the critiques by enhancing depth in key sections, 
adding clarity, and improving structure with subheadings. 
It provides specific examples and discusses ethical considerations, 
making it a valuable resource. The revisions are sufficient for publication.',
    'finalized_state': True}}},
 'key': '9005ad06-c8eb-4c6f-bb94-e77f2bc867bc',
 'namespace': ['1', 'memories'],
 'created_at': '2024-11-11T06:09:46.170263+00:00',
 'updated_at': '2024-11-11T06:09:46.170267+00:00'}
```

看起来编辑器很满意！

为了调试，我们可能需要阅读其他所有输出。不过，在笔记本中做这件事可能会很痛苦，所以在下一篇文章中，我们将讨论如何将这些输出程序化地插入到 Google Docs 中。感谢你坚持看到最后，[我们将在第二部分继续](https://building-a-research-assistant-that-can-write-to-google-docs-part-2-ac9dcacff4ff)！

作者与本文讨论的任何工具都没有任何关联。
