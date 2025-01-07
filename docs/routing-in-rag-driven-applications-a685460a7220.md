# 在 RAG 驱动的应用中的路由

> 原文：[https://towardsdatascience.com/routing-in-rag-driven-applications-a685460a7220?source=collection_archive---------1-----------------------#2024-05-09](https://towardsdatascience.com/routing-in-rag-driven-applications-a685460a7220?source=collection_archive---------1-----------------------#2024-05-09)

## 根据查询意图引导应用流程

[](https://medium.com/@ssmaameri?source=post_page---byline--a685460a7220--------------------------------)[![Sami Maameri](../Images/9e9892fe7d3cc53ad1c4d165145878ef.png)](https://medium.com/@ssmaameri?source=post_page---byline--a685460a7220--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a685460a7220--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a685460a7220--------------------------------) [Sami Maameri](https://medium.com/@ssmaameri?source=post_page---byline--a685460a7220--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a685460a7220--------------------------------) ·9分钟阅读·2024年5月9日

--

根据用户查询的意图在 RAG 应用中路由控制流可以帮助我们创建更有用和强大的基于检索增强生成（RAG）的应用。

我们希望用户能够交互的数据可能来自多个不同的来源，例如报告、文档、图像、数据库以及第三方系统。对于基于业务的 RAG 应用，我们可能还希望让用户能够交互来自业务各个领域的信息，例如来自销售、订单和会计系统的信息。

由于数据来源的多样性，信息的存储方式以及我们希望与其交互的方式也可能是多样的。有些数据可能存储在向量存储中，有些存储在 SQL 数据库中，还有些数据我们可能需要通过 API 调用来访问，因为它位于第三方系统中。

![](../Images/34159eb96b878359e44851c2335541d4.png)

RAG 系统根据查询意图路由到不同的数据源

对于相同的数据，可能会设置不同的向量存储，以优化不同的查询类型。例如，可以为回答总结类问题设置一个向量存储，为回答特定、定向类问题设置另一个向量存储。

我们可能还希望根据问题的类型路由到**不同的组件类型**。例如，我们可能希望将查询传递给一个代理、向量存储，或者直接传递给一个 LLM 进行处理，这一切都取决于问题的性质。

![](../Images/e171242fbcc3f09ba6c6fd62dd48e126.png)

根据用户查询路由到不同的组件类型

我们甚至可能希望根据问题的不同定制提示模板。

![](../Images/61f627b15df4c09e6ffcf469dcbd9b42.png)

根据用户查询，通过不同的提示模板进行路由

总的来说，我们有许多理由希望在应用程序中改变和引导用户查询的流程。我们的应用程序试图满足的用例越多，我们就越有可能在整个应用程序中存在路由需求。

路由器本质上只是我们可以用来引导查询控制流的If/Else语句。

然而，令它们有趣的是，它们**需要基于自然语言输入做出决策**。因此，我们正在寻找一个基于自然语言描述的离散输出。

由于许多路由逻辑是基于使用大语言模型（LLM）或机器学习算法，这些算法本质上是非确定性的，我们无法保证路由器总是能够100%做出正确的选择。此外，我们也不太可能预测所有进入路由器的不同查询变体。然而，通过使用最佳实践和一些测试，我们应该能够利用路由器帮助创建更强大的RAG应用程序。

# 自然语言路由器

在这里，我们将探索一些我发现的自然语言路由器，它们是由一些不同的RAG和LLM框架和库实现的。

+   LLM补全路由器

+   LLM函数调用路由器

+   语义路由器

+   零-shot分类路由器

+   语言分类路由器

下面的图表描述了这些路由器，并提供了它们可以找到的框架/包。

该图还包括逻辑路由器，我将其定义为基于离散逻辑工作的路由器，例如字符串长度、文件名、整数值等条件。换句话说，它们不依赖于理解自然语言查询的意图。

![](../Images/78163855bb31bbb8345e6d141e77a57b.png)

不同类型的自然语言路由器

让我们更详细地探讨一下这些路由器

# LLM路由器

这些利用LLM的决策能力，根据用户的查询选择路由。

## LLM补全路由器

这些使用LLM的补全调用，要求LLM从你传递给其提示的单词选项列表中返回一个最能描述查询的单词。然后，这个单词可以作为If/Else条件的一部分来控制应用程序的流程。

这就是[LlamaIndex的LLM选择路由器](https://docs.llamaindex.ai/en/stable/module_guides/querying/router/)的工作原理。这也是[LangChain](https://python.langchain.com/docs/expression_language/how_to/routing/)文档中给出的一个路由器示例。

让我们看看一个代码示例，基于LangChain文档中提供的示例，来更清楚地说明这一点。正如你所见，在LangChain内部编写这样的代码是非常直接的。

```py
from langchain_anthropic import ChatAnthropic
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import PromptTemplate

# Set up the LLM Chain to return a single word based on the query,
# and based on a list of words we provide to it in the prompt template
llm_completion_select_route_chain = (
        PromptTemplate.from_template("""
Given the user question below, classify it as either
being about `LangChain`, `Anthropic`, or `Other`.

Do not respond with more than one word.

<question>
{question}
</question>

Classification:"""
                                     )
        | ChatAnthropic(model_name="claude-3-haiku")
        | StrOutputParser()
)

# We setup an IF/Else condition to route the query to the correct chain 
# based on the LLM completion call above
def route_to_chain(route_name):
    if "anthropic" == route_name.lower():
        return anthropic_chain
    elif "langchain" == route_name.lower():
        return langchain_chain
    else:
        return general_chain

...

# Later on in the application, we can use the response from the LLM
# completion chain to control (i.e route) the flow of the application 
# to the correct chain via the route_to_chain method we created
route_name = llm_completion_select_route_chain.invoke(user_query)
chain = route_to_chain(route_name)
chain.invoke(user_query)
```

## LLM函数调用路由器

这利用了LLM的函数调用能力来选择要遍历的路由。不同的路由被设置为LLM函数调用中的函数，并附有适当的描述。然后，基于传递给LLM的查询，LLM能够返回正确的函数（即路由），供我们使用。

这就是[LlamaIndex](https://docs.llamaindex.ai/en/stable/module_guides/querying/router/)中[PyDantic Router](https://docs.llamaindex.ai/en/stable/module_guides/querying/router/)的工作方式。这也是大多数Agent在选择正确工具时所采用的方式。它们利用LLM的函数调用功能来根据用户的查询选择适合的工具。

# 语义路由器

这种路由器类型利用嵌入和相似度搜索来选择最佳的路径进行遍历。

每个路由都有一组关联的示例查询，这些查询会被嵌入并存储为向量。传入的查询也会被嵌入，并与来自路由器的其他示例查询进行相似度搜索。与查询最接近的路由将被选中。

实际上，有一个名为[semantic-router](https://github.com/aurelio-labs/semantic-router)的Python包可以做到这一点。让我们看一下其中的一些实现细节，以便更好地理解整个过程的工作原理。这些示例直接来自该库的GitHub页面。

让我们设置两条路由，一条用于关于政治的问题，另一条用于一般闲聊类型的问题。我们为每条路由分配一组可能会被问到的问题，这些问题通常用于触发该路由。这些示例查询称为*话语*。这些话语将被嵌入，以便我们可以用于与用户查询的相似度搜索。

```py
from semantic_router import Route

# we could use this as a guide for our chatbot to avoid political
# conversations
politics = Route(
    name="politics",
    utterances=[
        "isn't politics the best thing ever",
        "why don't you tell me about your political opinions",
        "don't you just love the president",
        "they're going to destroy this country!",
        "they will save the country!",
    ],
)

# this could be used as an indicator to our chatbot to switch to a more
# conversational prompt
chitchat = Route(
    name="chitchat",
    utterances=[
        "how's the weather today?",
        "how are things going?",
        "lovely weather today",
        "the weather is horrendous",
        "let's go to the chippy",
    ],
)

# we place both of our decisions together into single list
routes = [politics, chitchat]
```

我们指定OpenAI作为编码器，尽管任何嵌入库都可以使用。接下来，我们使用路由器和编码器创建我们的路由层。

```py
encoder = OpenAIEncoder()

from semantic_router.layer import RouteLayer

route_layer = RouteLayer(encoder=encoder, routes=routes)
```

然后，当我们将查询应用于路由器层时，它会返回应使用的查询路由。

```py
route_layer("don't you love politics?").name
# -> 'politics'
```

所以，再总结一下，这个语义路由器利用嵌入和相似度搜索，通过用户的查询选择最佳的路径进行遍历。由于只需要处理一个索引查询，相较于其他类型需要调用LLM的路由器，这种路由器的速度应该更快。

# 零-shot 分类路由器

[“Zero-shot文本分类](https://huggingface.co/tasks/zero-shot-classification)是自然语言处理中的一项任务，其中模型在一组标记示例上进行训练，但随后能够对来自先前未见类别的新示例进行分类”。这些路由器利用零-shot分类模型为文本分配标签，标签来自你传入路由器的预定义标签集。

**示例：** Haystack中的[ZeroShotTextRouter](https://docs.haystack.deepset.ai/reference/routers-api#module-zero_shot_text_router)，它利用了来自Hugging Face的Zero Shot分类模型。查看[源代码](https://github.com/deepset-ai/haystack/blob/main/haystack/components/routers/zero_shot_text_router.py#L130)，看看魔法发生的地方。

# 语言分类路由器

这种类型的路由器能够识别查询所使用的语言，并基于此进行路由。如果您的应用需要某种多语言解析能力，这会非常有用。

**示例：** 来自Haystack的[TextClassificationRouter](https://docs.haystack.deepset.ai/reference/routers-api#module-text_language_router)。 [它利用langdetect Python库](https://github.com/deepset-ai/haystack/blob/main/haystack/components/routers/text_language_router.py#L90)来检测文本的语言，而langdetect库本身使用了[朴素贝叶斯](https://www.slideshare.net/shuyo/language-detection-library-for-java)算法来检测语言。

# 关键词路由器

这篇来自LlamaIndex联合创始人Jerry Liu的[文章](https://betterprogramming.pub/unifying-llm-powered-qa-techniques-with-routing-abstractions-438e2499a0d0)，讨论了RAG应用程序中的路由问题，其中提出了一个关键词路由器，它通过匹配查询与路由列表之间的关键词来选择路由，除此之外还提到了其他几种方案。

这个关键词路由器也可以通过LLM来识别关键词，或者通过其他关键词匹配库来实现。我还没有找到任何实现这种路由器类型的包。

# 逻辑路由器

这些使用逻辑检查来处理变量，例如字符串长度、文件名和数值比较，以决定如何路由查询。它们与编程中常见的If/Else条件非常相似。

换句话说，它们并不需要理解自然语言查询的意图，而是基于现有的离散变量来做出选择。

**示例：** 来自Haystack的[ConditionalRouter](https://docs.haystack.deepset.ai/docs/conditionalrouter)和[FileTypeRouter](https://docs.haystack.deepset.ai/docs/filetyperouter)。

# 智能体与路由器

初看之下，路由器和智能体之间确实有很多相似之处，可能很难区分它们的不同。

这些相似之处的存在是因为智能体实际上在其流程中执行了路由操作。它们使用路由机制来选择正确的工具以完成任务。它们通常利用函数调用来选择正确的工具，就像上面描述的**LLM函数调用路由器**一样。

然而，路由器通常比智能体简单得多，通常只有一个“简单”的任务，就是将任务路由到正确的地方，而不涉及与该任务相关的任何逻辑或处理。

另一方面，代理通常负责处理逻辑，包括管理它们所能访问的工具所完成的工作。

# 结论

我们在这里介绍了当前在不同的RAG和LLM框架及包中找到的一些不同的自然语言路由器。

关于路由的概念、包和库，随着时间的推移肯定会增加。在构建RAG应用程序时，你会发现，在某个时候，路由功能确实变得必要，以便构建一个对用户有用的应用程序。

路由器是这些基本构建块，它们允许你将自然语言请求路由到应用程序的正确位置，以便尽可能最好地满足用户的查询。

*希望这对你有帮助！请* [*订阅*](https://medium.com/@ssmaameri/subscribe) *以便在我发布新文章时收到通知*

*除非另有说明，所有图片均由作者提供。第一张图片中使用的图标来自* [*SyafriStudi*](https://www.flaticon.com/free-icons/open-book)*，* [*Dimitry Miroliubovand*](https://www.flaticon.com/free-icons/png)*，以及* [*Free Icons PNG*](https://www.freeiconspng.com/img/13315)
