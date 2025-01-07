# 使用PydanticAI进行智能体应用的评估驱动开发

> 原文：[https://towardsdatascience.com/evaluation-driven-development-for-agentic-applications-using-pydanticai-d9293ac81d91?source=collection_archive---------0-----------------------#2024-12-21](https://towardsdatascience.com/evaluation-driven-development-for-agentic-applications-using-pydanticai-d9293ac81d91?source=collection_archive---------0-----------------------#2024-12-21)

## 一个开源的、模型无关的智能体框架，支持依赖注入

[](https://lakshmanok.medium.com/?source=post_page---byline--d9293ac81d91--------------------------------)[![Lak Lakshmanan](../Images/9faaaf72d600f592cbaf3e9089cbb913.png)](https://lakshmanok.medium.com/?source=post_page---byline--d9293ac81d91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d9293ac81d91--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d9293ac81d91--------------------------------) [Lak Lakshmanan](https://lakshmanok.medium.com/?source=post_page---byline--d9293ac81d91--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d9293ac81d91--------------------------------) ·12分钟阅读·2024年12月21日

--

理想情况下，你可以在开发过程中就评估智能体应用，而不是将评估视为事后的事情。不过，要实现这一点，你需要能够模拟你正在开发的智能体的内部和外部依赖。我对PydanticAI感到非常兴奋，因为它从根本上就支持依赖注入。它是第一个让我能够以评估驱动的方式构建智能体应用的框架。

![](../Images/6786f9e4942182ffc3b8b875a045bfd6.png)

克拉科夫布料大厅的图像，由作者使用Google Imagen生成。这个建筑是分阶段建设的，经过几个世纪的改进，改进的方向基于当前建筑的不足之处。换句话说，这是一种以评估驱动的开发方式。

在这篇文章中，我将讨论核心挑战，并演示如何使用PydanticAI以评估驱动的方式开发一个简单的智能体。

## 开发GenAI应用时的挑战

和许多GenAI开发者一样，我一直在等待一个支持完整开发生命周期的智能体框架。每当一个新框架出现时，我都会尝试，期望这次能是那个“终极框架”——例如，我的文章中提到过的[ DSPy](/building-an-ai-assistant-with-dspy-2e1e749a1a95)、[Langchain](/four-approaches-to-build-on-top-of-generative-ai-foundational-models-43c1a64cffd5)、[LangGraph和Autogen](https://www.linkedin.com/pulse/how-implement-genai-agent-using-autogen-langgraph-lakshmanan-cwx4c/)。

我发现，在开发基于LLM的应用程序时，软件开发者面临一些核心挑战。如果你正在构建一个简单的GenAI概念验证（PoC），这些挑战通常不是阻碍因素，但如果你在生产环境中构建基于LLM的应用程序，它们会成为问题。

什么挑战？

(1) **非确定性**：与大多数软件API不同，向LLM发送完全相同的输入，每次调用可能会返回不同的输出。那么，你该如何开始测试这样的应用程序呢？

(2) **LLM的局限性**：像GPT-4、Claude和Gemini这样的基础模型受限于其训练数据（例如，无法访问企业机密信息）、能力（例如，无法调用企业API和数据库），并且不能进行规划/推理。

(3) **LLM灵活性**：即使你决定坚持使用来自单一供应商的LLM（如Anthropic），你可能会发现每个步骤需要不同的LLM——也许你的工作流中的某个步骤需要一个低延迟的小型语言模型（Haiku），另一个步骤需要强大的代码生成能力（Sonnet），而第三个步骤需要出色的上下文意识（Opus）。

(4) **变化速率**：GenAI技术发展迅速。最近，许多改进出现在基础模型的能力上。基础模型不再只是根据用户提示生成文本。它们现在是多模态的，可以生成结构化输出，并且具备记忆能力。然而，如果你试图以LLM无关的方式构建，通常会失去开启这些功能的低级API访问权限。

为了解决第一个问题——非确定性问题，你的软件测试需要纳入一个评估框架。你永远不会有完全完美的软件；相反，你需要能够设计出一个在x%准确的情况下运行的软件，构建保护措施和人工监督以捕捉例外，并实时监控系统以发现回归问题。实现这一能力的关键是**评估驱动开发**（我自己的术语），它是软件中测试驱动开发的扩展。

![](../Images/9db9ba18e95ae10febbe505241b7d65a.png)

评估驱动开发。作者草图。

对于挑战#2中的所有LLM局限性，目前的解决方法是使用**代理架构**（如RAG），为LLM提供工具访问权限，并采用诸如反射（Reflection）、反应（ReACT）和思维链（Chain of Thought）等模式。因此，你的框架需要能够协调代理。然而，评估可以调用外部工具的代理是困难的。你需要能够**为这些外部依赖项注入代理**，以便单独测试它们，并在构建过程中进行评估。

为了处理挑战 #3，代理需要能够调用不同类型基础模型的能力。你的代理框架需要在代理工作流的单个步骤粒度上是**与 LLM 无关的**。为了应对变化速度的问题（挑战 #4），你需要保留对基础模型 API 的**低级访问**权限，并且能够去除不再需要的代码部分。

有没有一个框架能满足所有这些标准？很长一段时间，答案是否定的。我能做到的最接近的方式是使用 Langchain、pytest 的依赖注入以及 deepeval，像这样（完整示例请见[这里](https://github.com/lakshmanok/lakblogs/blob/main/genai_agents/eval_weather_agent.py)）：

```py
from unittest.mock import patch, Mock
from deepeval.metrics import GEval

llm_as_judge = GEval(
    name="Correctness",
    criteria="Determine whether the actual output is factually correct based on the expected output.",
    evaluation_params=[LLMTestCaseParams.INPUT, LLMTestCaseParams.ACTUAL_OUTPUT],
    model='gpt-3.5-turbo'
)

@patch('lg_weather_agent.retrieve_weather_data', Mock(return_value=chicago_weather))
def eval_query_rain_today():
    input_query = "Is it raining in Chicago?"
    expected_output = "No, it is not raining in Chicago right now."
    result = lg_weather_agent.run_query(app, input_query)
    actual_output = result[-1]

    print(f"Actual: {actual_output}   Expected: {expected_output}")
    test_case = LLMTestCase(
        input=input_query,
        actual_output=actual_output,
        expected_output=expected_output
    )

    llm_as_judge.measure(test_case)
    print(llm_as_judge.score)
```

本质上，我会为每次 LLM 调用构建一个 Mock 对象（如上例中的 chicago_weather），并在需要模拟代理工作流的部分时，将 LLM 调用（如上例中的 retrieve_weather_data）替换为硬编码的对象。依赖注入到处都是，你需要一堆硬编码的对象，调用工作流变得非常难以跟踪。注意，如果没有依赖注入，就无法测试这样的函数：显然，外部服务会返回当前天气，而对于像“现在下雨吗？”这样的问题，无法确定正确的答案。

*那么…是否有一个支持依赖注入、Pythonic、提供低级 LLM 访问、与模型无关、支持逐步评估构建且易于使用和跟踪的代理框架呢？*

几乎做到了。[PydanticAI](https://ai.pydantic.dev/) 满足了前三个要求；第四个（低级 LLM 访问）是不可能的，但设计上并不排斥这一点。在本文的其余部分，我将向你展示如何以评估驱动的方式使用它来开发一个代理应用。

## 1. 你的第一个 PydanticAI 应用程序

让我们从构建一个简单的 PydanticAI 应用开始。这个应用将使用 LLM 回答有关山脉的问题：

```py
 agent = llm_utils.agent()
    question = "What is the tallest mountain in British Columbia?"
    print(">> ", question)
    answer = agent.run_sync(question)
    print(answer.data) 
```

在上面的代码中，我创建了一个代理（稍后我会告诉你如何做），然后调用 run_sync 传入用户提示，并获取 LLM 的响应。run_sync 是一种让代理调用 LLM 并等待响应的方式。其他方式是异步执行查询，或者流式返回响应。（[完整代码](https://github.com/lakshmanok/lakblogs/blob/main/pydantic_ai_mountains/1_zero_shot.py) 在这里，如果你想跟着一起做）。

运行上述代码，你将得到类似这样的结果：

```py
>>  What is the tallest mountain in British Columbia?
The tallest mountain in British Columbia is **Mount Robson**, at 3,954 metres (12,972 feet).
```

要创建代理，先创建一个模型，然后告诉代理在所有步骤中使用该模型。

```py
import pydantic_ai
from pydantic_ai.models.gemini import GeminiModel

def default_model() -> pydantic_ai.models.Model:
    model = GeminiModel('gemini-1.5-flash', api_key=os.getenv('GOOGLE_API_KEY'))
    return model

def agent() -> pydantic_ai.Agent:
    return pydantic_ai.Agent(default_model())
```

default_model() 背后的思路是使用像 Gemini Flash 这样相对廉价但快速的模型作为默认模型。然后，你可以通过传递不同的模型给 run_sync() 来根据需要更改特定步骤中使用的模型。

PydanticAI模型支持[看起来较为稀疏](https://ai.pydantic.dev/api/models/base/#pydantic_ai.models)，但最常用的模型——来自OpenAI、Groq、Gemini、Mistral、Ollama和Anthropic的当前前沿模型——都得到了支持。通过Ollama，你可以访问Llama3、Starcoder2、Gemma2和Phi3。似乎没有什么显著缺失。

## 2\. 使用结构化输出的Pydantic

前一部分的示例返回的是自由格式的文本。在大多数智能体工作流程中，你会希望LLM返回结构化数据，以便可以直接在程序中使用。

考虑到这个API来自Pydantic，返回结构化输出是相当直接的。只需将期望的输出定义为数据类（[完整代码](https://github.com/lakshmanok/lakblogs/blob/main/pydantic_ai_mountains/2_zero_shot_structured.py)在这里）：

```py
from dataclasses import dataclass

@dataclass
class Mountain:
    name: str
    location: str
    height: float
```

当你创建智能体时，告诉它期望的输出类型：

```py
agent = Agent(llm_utils.default_model(),
                  result_type=Mountain,
                  system_prompt=(
                      "You are a mountaineering guide, who provides accurate information to the general public.",
                      "Provide all distances and heights in meters",
                      "Provide location as distance and direction from nearest big city",
                  ))
```

另请注意使用系统提示来指定单位等。

在三个问题上运行此代码后，我们得到：

```py
>>  Tell me about the tallest mountain in British Columbia?
Mountain(name='Mount Robson', location='130km North of Vancouver', height=3999.0)
>>  Is Mt. Hood easy to climb?
Mountain(name='Mt. Hood', location='60 km east of Portland', height=3429.0)
>>  What's the tallest peak in the Enchantments?
Mountain(name='Mount Stuart', location='100 km east of Seattle', height=3000.0)
```

但是这个智能体有多好呢？罗布森山的高度正确吗？斯图尔特山真的是“魔法山脉”中最高的峰吗？这些信息可能是虚构的！

除非你将智能体与参考答案进行评估，否则你无法知道一个智能体应用的好坏。你不能仅凭眼睛“估算”。不幸的是，这正是许多LLM框架的不足之处——它们使得在开发LLM应用时评估变得非常困难。

## 3\. 与参考答案进行评估

当你开始与参考答案进行对比评估时，PydanticAI开始展现其优势。一切都非常符合Python风格，因此你可以非常简单地构建自定义评估指标。

例如，下面是我们如何在三个标准上评估返回的Mountain对象，并创建一个综合得分（[完整代码](https://github.com/lakshmanok/lakblogs/blob/main/pydantic_ai_mountains/3_eval_against_reference.py)在这里）：

```py
def evaluate(answer: Mountain, reference_answer: Mountain) -> Tuple[float, str]:
    score = 0
    reason = []
    if reference_answer.name in answer.name:
        score += 0.5
        reason.append("Correct mountain identified")
        if reference_answer.location in answer.location:
            score += 0.25
            reason.append("Correct city identified")
        height_error = abs(reference_answer.height - answer.height)
        if height_error < 10:
            score += 0.25 * (10 - height_error)/10.0
        reason.append(f"Height was {height_error}m off. Correct answer is {reference_answer.height}")
    else:
        reason.append(f"Wrong mountain identified. Correct answer is {reference_answer.name}")

    return score, ';'.join(reason)
```

现在，我们可以在一组问题和参考答案的数据集上运行此功能：

```py
 questions = [
        "Tell me about the tallest mountain in British Columbia?",
        "Is Mt. Hood easy to climb?",
        "What's the tallest peak in the Enchantments?"
    ]

    reference_answers = [
        Mountain("Robson", "Vancouver", 3954),
        Mountain("Hood", "Portland", 3429),
        Mountain("Dragontail", "Seattle", 2690)
    ]

    total_score = 0
    for l_question, l_reference_answer in zip(questions, reference_answers):
        print(">> ", l_question)
        l_answer = agent.run_sync(l_question)
        print(l_answer.data)
        l_score, l_reason = evaluate(l_answer.data, l_reference_answer)
        print(l_score, ":", l_reason)
        total_score += l_score

    avg_score = total_score / len(questions)
```

运行此代码后，我们得到：

```py
>>  Tell me about the tallest mountain in British Columbia?
Mountain(name='Mount Robson', location='130 km North-East of Vancouver', height=3999.0)
0.75 : Correct mountain identified;Correct city identified;Height was 45.0m off. Correct answer is 3954
>>  Is Mt. Hood easy to climb?
Mountain(name='Mt. Hood', location='60 km east of Portland, OR', height=3429.0)
1.0 : Correct mountain identified;Correct city identified;Height was 0.0m off. Correct answer is 3429
>>  What's the tallest peak in the Enchantments?
Mountain(name='Dragontail Peak', location='14 km east of Leavenworth, WA', height=3008.0)
0.5 : Correct mountain identified;Height was 318.0m off. Correct answer is 2690
Average score: 0.75
```

罗布森山的高度偏差了45米；龙尾峰的高度偏差了318米。你会如何修正这个问题？

没错。你会使用RAG架构，或者为智能体提供一个能够提供正确高度信息的工具。我们就使用后者的方法，看看如何用Pydantic实现。

请注意，如何通过评估驱动开发向我们展示了改善智能体应用的前进道路。

## 4a. 使用工具

PydanticAI支持多种向智能体提供工具的方式。在这里，我为一个函数添加了注解，以便在需要时调用它来获取山的高度（[完整代码在这里](https://github.com/lakshmanok/lakblogs/blob/main/pydantic_ai_mountains/4_use_tool.py)）：

```py
 agent = Agent(llm_utils.default_model(),
                  result_type=Mountain,
                  system_prompt=(
                      "You are a mountaineering guide, who provides accurate information to the general public.",
                      "Use the provided tool to look up the elevation of many mountains."
                      "Provide all distances and heights in meters",
                      "Provide location as distance and direction from nearest big city",
                  ))
    @agent.tool
    def get_height_of_mountain(ctx: RunContext[Tools], mountain_name: str) -> str:
        return ctx.deps.elev_wiki.snippet(mountain_name)
```

然而，这个函数做了一些奇怪的事情。它从智能体的运行时上下文中提取了一个名为elev_wiki的对象。这个对象在我们调用run_sync时被传入：

```py
class Tools:
    elev_wiki: wikipedia_tool.WikipediaContent
    def __init__(self):
        self.elev_wiki = OnlineWikipediaContent("List of mountains by elevation")

tools = Tools()  # Tools or FakeTools

l_answer = agent.run_sync(l_question, deps=tools) # note how we are able to inject
```

因为运行时上下文可以传递给每次代理调用或工具调用，所以我们可以用它来进行依赖注入。在下一节中你会看到这一点。

维基本身只是查询在线的维基百科（[代码在这里](https://github.com/lakshmanok/lakblogs/blob/main/pydantic_ai_mountains/wikipedia_tool.py)），提取页面内容并将适当的山脉信息传递给代理：

```py
import wikipedia

class OnlineWikipediaContent(WikipediaContent):
    def __init__(self, topic: str):
        print(f"Will query online Wikipedia for information on {topic}")
        self.page = wikipedia.page(topic)

    def url(self) -> str:
        return self.page.url

    def html(self) -> str:
        return self.page.html()
```

事实上，当我们运行时，现在得到了正确的高度：

```py
Will query online Wikipedia for information on List of mountains by elevation
>>  Tell me about the tallest mountain in British Columbia?
Mountain(name='Mount Robson', location='100 km west of Jasper', height=3954.0)
0.75 : Correct mountain identified;Height was 0.0m off. Correct answer is 3954
>>  Is Mt. Hood easy to climb?
Mountain(name='Mt. Hood', location='50 km ESE of Portland, OR', height=3429.0)
1.0 : Correct mountain identified;Correct city identified;Height was 0.0m off. Correct answer is 3429
>>  What's the tallest peak in the Enchantments?
Mountain(name='Mount Stuart', location='Cascades, Washington, US', height=2869.0)
0 : Wrong mountain identified. Correct answer is Dragontail
Average score: 0.58
```

## 4b. 依赖注入一个模拟服务

每次在开发或测试期间等待维基百科的API调用是不好的做法。相反，我们希望模拟维基百科的响应，这样我们就能快速开发，并确保得到预期的结果。

做这个非常简单。我们创建一个假的维基百科服务：

```py
class FakeWikipediaContent(WikipediaContent):
    def __init__(self, topic: str):
        if topic == "List of mountains by elevation":
            print(f"Will used cached Wikipedia information on {topic}")
            self.url_ = "https://en.wikipedia.org/wiki/List_of_mountains_by_elevation"
            with open("mountains.html", "rb") as ifp:
                self.html_ = ifp.read().decode("utf-8")

    def url(self) -> str:
        return self.url_

    def html(self) -> str:
        return self.html_
```

然后，在开发过程中，将这个假对象注入到代理的运行时上下文中：

```py
class FakeTools:
    elev_wiki: wikipedia_tool.WikipediaContent
    def __init__(self):
        self.elev_wiki = FakeWikipediaContent("List of mountains by elevation")

tools = FakeTools()  # Tools or FakeTools

l_answer = agent.run_sync(l_question, deps=tools) # note how we are able to inject
```

这次当我们运行时，评估使用了缓存的维基百科内容：

```py
Will used cached Wikipedia information on List of mountains by elevation
>>  Tell me about the tallest mountain in British Columbia?
Mountain(name='Mount Robson', location='100 km west of Jasper', height=3954.0)
0.75 : Correct mountain identified;Height was 0.0m off. Correct answer is 3954
>>  Is Mt. Hood easy to climb?
Mountain(name='Mt. Hood', location='50 km ESE of Portland, OR', height=3429.0)
1.0 : Correct mountain identified;Correct city identified;Height was 0.0m off. Correct answer is 3429
>>  What's the tallest peak in the Enchantments?
Mountain(name='Mount Stuart', location='Cascades, Washington, US', height=2869.0)
0 : Wrong mountain identified. Correct answer is Dragontail
Average score: 0.58
```

仔细观察上面的输出——它与零-shot示例中的错误不同。在第2节中，LLM将温哥华选为最接近罗布森山的城市，将龙尾山选为魔法山脉中最高的峰。这些答案恰好是正确的。现在，它选择了贾斯珀和斯图尔特山。我们需要做更多工作来修复这些错误——但基于评估的开发至少给了我们一个前进的方向。

## 当前的局限性

PydanticAI非常新，还有一些可以改进的地方：

+   当前没有对模型本身的底层访问。例如，不同的基础模型支持上下文缓存、提示缓存等。PydanticAI中的模型抽象没有提供设置这些功能的方法。理想情况下，我们可以通过传递`kwargs`的方式来实现这种设置。

+   创建两个版本的代理依赖关系，一个真实的和一个假的，是很常见的。如果我们能够标注一个工具或提供一种简单的方法来在两种类型的服务之间切换，那将是非常好的。

+   在开发过程中，你不需要那么多的日志记录。但当你运行代理时，通常会希望记录提示和响应。有时，你还需要记录中间的响应。实现这个目标的方法似乎是一个叫做Logfire的商业产品。一个与PydanticAI库集成的开源、与云平台无关的日志框架将是理想的选择。

可能这些服务已经存在，我没注意到，或者在你读这篇文章的时候它们已经被实现了。不管是哪种情况，都请为未来的读者留个评论。

总的来说，我喜欢PydanticAI——它提供了一种非常简洁且符合Python风格的方式，来以评估驱动的方式构建代理应用。

## 建议的下一步：

1.  这是那种你通过实际运行示例会受益的博客文章，因为它描述了一个开发过程以及一个新的库。这个 GitHub 仓库包含了我在这篇文章中演示的 PydanticAI 示例：[https://github.com/lakshmanok/lakblogs/tree/main/pydantic_ai_mountains](https://github.com/lakshmanok/lakblogs/tree/main/pydantic_ai_mountains)。按照 README 中的说明进行操作试试。

1.  Pydantic AI 文档：[https://ai.pydantic.dev/](https://ai.pydantic.dev/)

1.  使用 Mock 对象修补 Langchain 工作流。我的“前置”解决方案：[https://github.com/lakshmanok/lakblogs/blob/main/genai_agents/eval_weather_agent.py](https://github.com/lakshmanok/lakblogs/blob/main/genai_agents/eval_weather_agent.py)
