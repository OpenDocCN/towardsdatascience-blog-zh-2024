# 通过符号程序搜索超充提示工程

> 原文：[https://towardsdatascience.com/supercharging-prompt-engineering-via-symbolic-program-search-c6c353bc80a4?source=collection_archive---------1-----------------------#2024-04-22](https://towardsdatascience.com/supercharging-prompt-engineering-via-symbolic-program-search-c6c353bc80a4?source=collection_archive---------1-----------------------#2024-04-22)

## 通过自动探索大量提示变体来找到更好的提示

[](https://medium.com/@toschnab?source=post_page---byline--c6c353bc80a4--------------------------------)[![Tobias Schnabel](../Images/92a6c1addc602dae8e8d54fec5116385.png)](https://medium.com/@toschnab?source=post_page---byline--c6c353bc80a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c6c353bc80a4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c6c353bc80a4--------------------------------) [Tobias Schnabel](https://medium.com/@toschnab?source=post_page---byline--c6c353bc80a4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6c353bc80a4--------------------------------) ·阅读时间8分钟·2024年4月22日

--

![](../Images/bfbe607a7b9395877d23744afe241481.png)

图片由[Icons8 Team](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

不是什么秘密，LLM（大规模语言模型）的成功很大程度上依赖于我们能够为它们提供正确的提示和示例。随着新一代LLM变得越来越强大，提示已经变得足够复杂，以至于可以被视为[程序本身](/intro-to-dspy-goodbye-prompting-hello-programming-4ca1c6ce3eb9)。这些提示程序与食谱非常相似——它们都有一组需要遵循的指令，并将原材料（无论是数据还是食材）转化为最终的成果。

提示工程类似于改进一个食谱。家庭厨师通常会遵循整体食谱，但会做一些小的修改——例如在意面菜肴中去掉大蒜或加入香菜。[DSPy](https://github.com/stanfordnlp/dspy)这样的框架在优化上下文示例时遵循了这种总体范式。然而，专业厨师将食谱视为灵感来源，并且经常完全重新解释菜肴的组成部分。例如，他们可能会将意面视为富含淀粉的组成部分，并将其换成新鲜制作的意大利饺子，以达到类似的效果。

是什么让专业级厨师能如此富有创意地工作呢？正是因为他们以抽象的方式思考食谱，就像上面提到的意面例子一样。手动提示工程类似于专业级烹饪。它可以获得令人印象深刻的结果，但需要[大量的时间和知识](https://medium.com/the-generator/the-perfect-prompt-prompt-engineering-cheat-sheet-d0b9c62a2bba)。我们真正想要的是手动提示工程的创造力，但无需付出那么多努力。

# 抽象提示的强大之处

假设我们想改进一个用于标注发言人回应的提示。我们最终会使用许多不同的输入，但暂时先插入一个具体的例子：

```py
Instructions: Does Speaker 2's answer mean yes or no?
Output labels: no, yes
Input: Speaker 1: "You do this often?" Speaker 2: "It's my first time."
Output:
```

假设，暂时我们有一个抽象的提示表示，它提取出单独的组件并且易于操作。也许是这样的：

![](../Images/f419eb3d7a2cb20fc0c6a90b9918af6a.png)

一个简单的分类任务提示，以抽象符号程序表示。图片由作者提供。

通过这个，你可以自动化在提示原型过程中需要进行的大量（半）手动调整。像改写这样的简单编辑只是开始。想试试[链式思维推理](https://chain-of-thought-prompting-for-llms-33c963eead38)吗？添加一段话说“让我们一步一步思考”。怎么样更改数据格式为JSON？只需更改`format`参数的属性。你还可以探索

+   从单一示例到批量注释

+   在RAG场景中更改你的检索器和排序函数

+   重新排序一些段落

+   压缩指令中的某些部分

+   等等。

本质上，插入你最喜欢的提示工程启发式方法。提示的这种抽象表示方式使我们能够真正发挥创意，并自动探索大量可能的提示空间。那么，我们如何将提示表示为抽象且可修改的程序呢？继续阅读。

## 将提示转换为抽象程序

> “计算机科学中的任何问题都可以通过另一层间接性来解决。”
> 
> [David J. Wheeler](https://en.wikipedia.org/wiki/David_Wheeler_(computer_scientist))

为了表示抽象提示，首先让我们将其转换为*非符号提示程序*，通过将它们拆解为单独的组件，并作为Python类实现：

```py
class Component:
    def __init__(self, **kwargs): pass
class Metaprompt(Component): pass
class Paragraph(Component): pass
class InputData(Component): pass

prompt = Metaprompt(
    children=[
        Paragraph(text="Instructions: "),
        Paragraph(
            id="instructions",
            text="Does Speaker 2's answer mean yes or no?",
        ),
        Paragraph(id="labels", text="Output labels: yes, no"),
        InputData(),
        Paragraph(text="Output: "),
    ]
)
```

到目前为止，进展不错。这类似于DSpy所做的，尽管更加通用，因为我们还表示了提示的内部结构。

接下来，我们将其转换为*符号提示程序*，以便可以进行任意更改（这也超出了静态DSPy程序的范围）。这可以通过[pyGlove](https://pyglove.readthedocs.io/en/latest/notebooks/intro/birdview.html)库完成，这是一个用于符号面向对象编程（SOOP）的库。pyGlove将Python类转化为可操作的符号对象，其属性在实例化后仍然可以完全编辑。

使用pyGlove，我们只需要添加`pg.symbolize`装饰器：

```py
import pyglove as pg
@pg.symbolize
class Component:
    def __init__(self, **kwargs): pass
```

我们现在可以通过一系列说明符查询和修改提示程序，类似于操作DOM树。假设我们希望将上面的程序转换成以下内容：

![](../Images/7df9a7710a1fb272046f66ae8c63ad21.png)

我们希望实现的目标提示程序。图片来源：作者。

注意，我们现在在问“回应是表示肯定吗？”而不是提供肯定和否定的输出标签。要做到这一点，我们需要（i）更改指令文本，和（ii）删除第三个节点。使用pyGlove，这非常简单：

```py
prompt.rebind({'children[1].text': 'Does the response mean yes?'})
prompt.rebind({'children[2]': pg.MISSING_VALUE})
print(prompt)
```

输出确认了我们的成功：

```py
Metaprompt(
  children = [
    0 : Paragraph(
      text = 'Instructions: '
    ),
    1 : Paragraph(
      id = 'instructions',
      text = 'Does the response mean yes?'
    ),
    2 : InputData(),
    3 : Paragraph(
      text = 'Output: '
    )
  ]
)
```

瞧！本质上，pyGlove为我们提供了一种方式，可以像操作源代码一样使用Python类（和函数），几乎没有额外的开销。现在我们有了灵活且易于操作的表示方式，让我们开始使用它们吧。

等一下。我们现在可能有了一种表示和修改提示的方法，但我们仍然缺少一个自动优化它们的过程。

一旦厨师理解了食谱的抽象和组成部分，他们就会尝试多种变体，精炼味道、成本或外观，直到感觉合适。要做同样的事情来优化提示抽象，我们需要一个搜索算法、一个目标以及一组标注样本，以便知道我们在取得进展。

听起来需要自己实现很多东西？认识一下[SAMMO](https://github.com/microsoft/sammo)，一个用于构建和优化符号提示程序的Python库。

# 热身：使用SAMMO进行指令调优

为了说明SAMMO的核心工作流程，我们现在将展示如何调整上述提示示例中的指令部分。一旦我们完成了这个简单示例，我们就可以准备讨论更高级的应用，比如RAG优化或压缩。

关键步骤是

1.  定义你的初始提示

1.  准备数据——几百个标注样本就足够了。

1.  定义目标

1.  选择一组变异器

1.  运行优化

## 步骤1：定义你的初始提示

我们基本上已经做到了这一点。SAMMO期望一个函数，所以我们必须将其包装在一个函数中。如果你想存储额外的信息，可以将其包装在[可调用对象](/python-callables-the-basics-and-the-secrets-ba88bf0729aa)中。我们还会将它包装在一个输出组件中以运行它。

```py
def starting_prompt():
    instructions = MetaPrompt(
        Paragraph(text="Instructions: "),
        Paragraph(
            id="instructions",
            text="Does Speaker 2's answer mean yes or no?",
        ),
        Paragraph(id="labels", text="Output labels: yes, no"),
        InputData(),
        Paragraph(text="Output: "),
    )
    return Output(instructions.with_extractor())
```

## 步骤2：准备你的数据

SAMMO使用一种简单的数据结构，称为DataTable，用于将输入与输出（标签）配对。这将帮助我们进行评估和记账。

```py
mydata = DataTable.from_records(
    records, # list of {"input": <>, "output": <>}
    constants={"instructions": default_instructions}, 
)
```

## 步骤3：定义目标

我们的目标是优化准确性，所以我们将在下面实现这个目标：

```py
def accuracy(y_true: DataTable, y_pred: DataTable) -> EvaluationScore:
    y_true = y_true.outputs.normalized_values()
    y_pred = y_pred.outputs.normalized_values()
    n_correct = sum([y_p == y_t for y_p, y_t in zip(y_pred, y_true)])
    return EvaluationScore(n_correct / len(y_true))
```

## 步骤4：选择一组变异器

在这里，你可以尽情发挥创造力。你可以实现自己的运算符来生成新的提示变体，或者仅仅依赖于SAMMO提供的预构建突变运算符。

在下面，我们采用后者，混合使用释义和从一些标注示例中引导指令，实质上是在实现[自动提示工程（APE）](https://cobusgreyling.medium.com/automatic-prompt-engineering-907e230ece0)。

```py
mutation_operators = BagOfMutators(
    starting_prompt=StartingPrompt(d_train),
    InduceInstructions({"id": "instructions"}, d_train),
    Paraphrase({"id": "instructions"}),
)
```

## 第五步：运行优化

```py
runner = OpenAIChat(
    model_id="gpt-3.5-turbo-16k",
    api_config={"api_key": YOUR_KEY},
    cache="cache.tsv",
)
prompt_optimizer = BeamSearch(runner, mutation_operators, accuracy, depth=6)
transformed = prompt_optimizer.fit_transform(d_train)
```

介绍性示例提示实际上取自于[BigBench含义任务](https://github.com/google/BIG-bench/tree/main/bigbench/benchmark_tasks/implicatures)，我们将使用它来运行本次实验。如果您使用100个样本进行训练和测试，并设置48个候选项评估预算，您将看到SAMMO将起始提示的准确率从**0.56**提升到**0.77**——提高了**37.5%**。哪些指令效果最好？

```py
...
Paragraph(
    "Consider the dialogue, context, and background "
    "information provided to determine the most suitable output label",
    id="instructions",
)
...
```

有趣的是，不同的LLM偏好不同的指令。如上所见，GPT-3.5最喜欢通用指令。SAMMO为Llama-2选择的最佳提示，在相同的训练和预算设置下，指令部分使用了一个空字符串：

```py
...
Paragraph(
    "",
    id="instructions",
)
...
```

# 实践操作：RAG调优

我们现在将展示如何将RAG管道转换为符号程序，并使用SAMMO进行调优。我们将使用语义解析作为应用任务，在该任务中，我们希望将用户查询转换为领域特定语言（DSL）结构，例如查询某些数据库或调用外部API。

为了创建起始提示，我们包括了所有操作符的列表，使用基于嵌入的检索器获取五个少样本示例，然后指示LLM按与示例相同的格式输出答案。

```py
class RagStartingPrompt:
    def __init__(self, dtrain, examples, embedding_runner):
        self._examples = examples
        self._dtrain = dtrain
        self._embedding_runner = embedding_runner

    def __call__(self, return_raw=False):
        structure = [
            Section("Syntax", self._dtrain.constants["list_of_operators"]),
            Section(
                "Examples",
                EmbeddingFewshotExamples(
                    self._embedding_runner, self._examples, 5
                ),
            ),
            Section(
                "Complete and output in the same format as above",
                InputData(),
            ),
        ]
        instructions = MetaPrompt(
            structure,
            render_as="markdown",
            data_formatter=JSONDataFormatter(),
        )  
        return Output(
            instructions.with_extractor(),
            on_error="empty_result",
        )
```

现在我们有了一个符号程序，让我们发挥创意。对于突变，我们进行探索

+   不同数量的少样本示例

+   为少样本示例提供不同格式（XML、JSON、逐行格式）

+   是否提供关于DSL的额外信息

+   显示输入输出对或输入输出组

使用这些并尝试总共24个候选项时，我们可以看到一个明显的趋势。以下是三个不同数据集在四个不同LLM上的测试集准确率。在绝大多数情况下，我们可以看到SAMMO显著提升了性能，即使对于表现最好的LLM也是如此。

![](../Images/b8b5eb0779ecd8d30ded3ab902eba027.png)

即使预算只有24个候选项的评估，我们也能在性能上获得显著提升。图片由作者提供。

# 结论

将提示转换为符号程序是一个非常强大的想法，可以探索大量可能的提示和设置设计空间。就像一位专业厨师解构并重新诠释食谱以创造烹饪创新一样，符号编程让我们能够将同样的创造力和实验精神应用于自动化提示工程。

SAMMO通过一组突变操作符和搜索例程实现了符号程序搜索。根据经验，这可以在指令调优和RAG调优中带来准确率的大幅提升，与后端LLM无关。

您可以通过自定义突变操作符扩展SAMMO，加入您最喜欢的提示工程技巧，或实现超越准确度（例如，成本）的目标。祝您愉快地进行提示创作！

*免责声明：* 我是SAMMO的作者。

## 资源

+   **代码与 Jupyter 笔记本：** [指令调优](https://github.com/microsoft/sammo/blob/main/docs/tutorials/5_instruction_optimization.ipynb) 和 [RAG 调优](https://github.com/microsoft/sammo/blob/main/examples/rag_tuning.py)

+   **阅读：** [SAMMO 用户指南](https://microsoft.github.io/sammo/tutorials/0_quickstart.html) 和 [arXiv 上的论文](https://arxiv.org/abs/2404.02319)，包含更多细节
