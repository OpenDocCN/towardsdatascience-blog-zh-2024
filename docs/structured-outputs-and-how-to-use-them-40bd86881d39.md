# 结构化输出及其使用方法

> 原文：[https://towardsdatascience.com/structured-outputs-and-how-to-use-them-40bd86881d39?source=collection_archive---------3-----------------------#2024-08-09](https://towardsdatascience.com/structured-outputs-and-how-to-use-them-40bd86881d39?source=collection_archive---------3-----------------------#2024-08-09)

## 在LLM应用中构建鲁棒性和确定性

[](https://medium.com/@armin.catovic?source=post_page---byline--40bd86881d39--------------------------------)[![Armin Catovic](../Images/046042098f3fec885e756f7f8ee94e6a.png)](https://medium.com/@armin.catovic?source=post_page---byline--40bd86881d39--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40bd86881d39--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--40bd86881d39--------------------------------) [Armin Catovic](https://medium.com/@armin.catovic?source=post_page---byline--40bd86881d39--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40bd86881d39--------------------------------) ·阅读时间：5分钟·2024年8月9日

--

![](../Images/3c27ad73265f5ca5be36e8041289d13e.png)

作者提供的图片

OpenAI [最近宣布](https://openai.com/index/introducing-structured-outputs-in-the-api/)支持其最新的**gpt-4o-2024–08–06**模型中的*结构化输出*。对于大型语言模型（LLM）而言，结构化输出并不是什么新鲜事——开发者们通常使用各种提示工程技术，或者第三方工具。

在本文中，我们将解释什么是结构化输出，它是如何工作的，以及如何在你自己的基于LLM的应用中应用它们。虽然OpenAI的公告使得通过其API实现结构化输出变得相当简单（如我们在此演示的），你可能更倾向于选择开源的[Outlines](https://github.com/outlines-dev/outlines)包（由[dotxt](https://dottxt.co/)的可爱团队维护），因为它既可以应用于自托管的开源模型（如Mistral和LLaMA），也可以应用于专有API。（**免责声明**：由于[这个问题](https://github.com/outlines-dev/outlines/issues/637)，截至本文写作时，Outlines尚不支持通过OpenAI API生成结构化JSON；但这一点很快就会有所改变！）

# 什么是结构化输出？

如果[RedPajama数据集](https://www.together.ai/blog/redpajama-data-v2)可以作为参考，那么压倒性的预训练数据来自人类文本。因此，“自然语言”是LLM的本土领域——无论是在输入端，还是输出端。然而，当我们构建应用程序时，我们希望使用机器可读的正式结构或模式来封装我们的数据输入/输出。通过这种方式，我们在应用程序中构建了鲁棒性和确定性。

**结构化输出**是一种强制执行预定义模式的机制，作用于LLM的输出。这通常意味着我们强制执行一个JSON模式，然而它不限于JSON—我们原则上可以强制执行XML、Markdown或完全自定义的模式。结构化输出的好处有两个方面：

1.  **更简单的提示设计**—我们在指定输出的格式时，**不需要**过于冗长。

1.  **确定性的名称和类型**—我们可以**保证**例如在LLM的响应中获得一个具有`Number` [JSON类型](https://json-schema.org/)的`age`属性。

# 实现一个JSON模式

对于这个例子，我们将使用[Sam Altman的维基百科条目](https://en.wikipedia.org/wiki/Sam_Altman)中的第一句话…

> Samuel Harris Altman（1985年4月22日出生）是美国企业家和投资者，最著名的是自2019年起担任OpenAI的首席执行官（他在2023年11月被短暂解雇并恢复职务）。

…并且我们将使用最新的GPT-4o检查点作为命名实体识别（NER）系统。我们将强制执行以下JSON模式：

```py
json_schema = {
    "name": "NamedEntities",
    "schema": {
        "type": "object",
        "properties": {
            "entities": {
                "type": "array",
                "description": "List of entity names and their corresponding types",
                "items": {
                    "type": "object",
                    "properties": {
                        "name": {
                            "type": "string",
                            "description": "The actual name as specified in the text, e.g. a person's name, or the name of the country"
                        },
                        "type": {
                            "type": "string",
                            "description": "The entity type, such as 'Person' or 'Organization'",
                            "enum": ["Person", "Organization", "Location", "DateTime"]
                        }
                    },
                    "required": ["name", "type"],
                    "additionalProperties": False
                }
            }
        },
        "required": ["entities"],
        "additionalProperties": False
    },
    "strict": True
}
```

从本质上讲，我们的LLM响应应该包含一个`NamedEntities`对象，其中包含一个`entities`数组，每个数组元素都包含一个`name`和`type`。这里有几点需要注意。例如，我们可以强制使用*Enum*类型，这在NER中非常有用，因为我们可以将输出限制为一组固定的实体类型。我们必须在`required`数组中指定所有字段；然而，我们也可以通过将类型设置为例如`["string", null]`来模拟“可选”字段。

我们现在可以将我们的模式、数据和指令传递给API。我们需要用*dict*填充`response_format`参数，在其中将`type`设置为`"json_schema"`，然后提供相应的模式。

```py
completion = client.beta.chat.completions.parse(
    model="gpt-4o-2024-08-06",
    messages=[
        {
            "role": "system",
            "content": """You are a Named Entity Recognition (NER) assistant.
                Your job is to identify and return all entity names and their 
                types for a given piece of text. You are to strictly conform
                only to the following entity types: Person, Location, Organization
                and DateTime. If uncertain about entity type, please ignore it.
                Be careful of certain acronyms, such as role titles "CEO", "CTO",
                "VP", etc - these are to be ignore.""",
        },
        {
            "role": "user",
            "content": s
        }
    ],
    response_format={
        "type": "json_schema",
        "json_schema": json_schema,
    }
)
```

输出应该看起来像这样：

```py
{   'entities': [   {'name': 'Samuel Harris Altman', 'type': 'Person'},
                    {'name': 'April 22, 1985', 'type': 'DateTime'},
                    {'name': 'American', 'type': 'Location'},
                    {'name': 'OpenAI', 'type': 'Organization'},
                    {'name': '2019', 'type': 'DateTime'},
                    {'name': 'November 2023', 'type': 'DateTime'}]}
```

本文中使用的完整源代码可以在[这里](https://github.com/acatovic/structured-outputs-demo)找到。

# 它是如何工作的

魔力在于**约束采样**和**无上下文语法（CFG）**的结合。我们之前提到过，大多数预训练数据是“自然语言”。从统计学角度来看，这意味着在每一个解码/采样步骤中，从已学习的词汇表中采样某个任意令牌的概率是不可忽略的（在现代LLM中，词汇表通常包含超过40,000个令牌）。然而，在处理正式模式时，我们确实希望快速排除所有不太可能的令牌。

在前面的例子中，如果我们已经生成了…

```py
{   'entities': [   {'name': 'Samuel Harris Altman',
```

…那么理想情况下，我们希望在下一步解码时对`'typ`令牌施加一个非常高的logit偏置，并对词汇表中的所有其他令牌施加非常低的概率。

本质上，发生的就是这个过程。当我们提供模式时，它会被转换成一种形式化的文法，或称上下文无关文法（CFG），该文法在解码步骤中用于引导logit偏置值。CFG是一种经典的计算机科学和自然语言处理（NLP）机制，现在正重新崛起。实际上，[这个StackOverflow回答](https://stackoverflow.com/questions/6713240/what-is-a-context-free-grammar/6713333#6713333)对CFG做了一个非常好的介绍，但本质上，它是一种描述符号集合转换规则的方式。

# 结论

结构化输出并不是什么新鲜事物，但随着专有API和大型语言模型（LLM）服务的发展，它们无疑已成为关注的重点。结构化输出在LLM的“不稳定”和“不可预测”的“自然语言”领域与软件工程的确定性和结构化领域之间架起了一座桥梁。对于任何设计复杂LLM应用程序的人来说，结构化输出本质上是**必须的**，尤其是在LLM输出必须以各种组件共享或“展示”的情况下。尽管API本地支持终于到来，但构建者仍应考虑使用如Outlines等库，因为它们提供了一种与LLM/API无关的方式来处理结构化输出。
