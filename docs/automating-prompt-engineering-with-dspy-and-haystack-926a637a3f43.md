# 使用DSPy和Haystack自动化提示工程

> 原文：[https://towardsdatascience.com/automating-prompt-engineering-with-dspy-and-haystack-926a637a3f43?source=collection_archive---------0-----------------------#2024-06-07](https://towardsdatascience.com/automating-prompt-engineering-with-dspy-and-haystack-926a637a3f43?source=collection_archive---------0-----------------------#2024-06-07)

## 通过示例教会你的LLM如何回答问题

[](https://medium.com/@dataqa?source=post_page---byline--926a637a3f43--------------------------------)[![Maria Mestre](../Images/3e2586487691b731293b1bfa091b651e.png)](https://medium.com/@dataqa?source=post_page---byline--926a637a3f43--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--926a637a3f43--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--926a637a3f43--------------------------------) [Maria Mestre](https://medium.com/@dataqa?source=post_page---byline--926a637a3f43--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--926a637a3f43--------------------------------) ·阅读时长9分钟·2024年6月7日

--

![](../Images/da9ea834c6eb33d8ee5a3ba9234bf9f0.png)

摄影：由[Markus Winkler](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)提供，图片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

构建生成式AI应用程序时，最令人沮丧的部分之一是手动优化提示的过程。在今年早些时候LinkedIn发布的[文章](https://www.linkedin.com/blog/engineering/generative-ai/musings-on-building-a-generative-ai-product)中，他们描述了在部署一个代理式RAG应用程序后学到的经验。其中一个主要挑战是确保一致的质量。他们花了4个月时间调整应用程序的各个部分，包括提示，以缓解诸如幻觉等问题。

DSPy是一个开源库，它试图将提示参数化，从而将其转化为一个优化问题。[原始论文](https://arxiv.org/abs/2310.03714)将提示工程称为“脆弱且不可扩展”，并将其与“手动调优分类器的权重”进行了比较。

[Haystack](https://github.com/deepset-ai/haystack)是一个开源库，用于构建LLM应用程序，包括RAG管道。它是平台无关的，并且与不同的LLM提供商、搜索数据库等有大量集成。它还拥有自己的[评估指标](https://docs.haystack.deepset.ai/docs/evaluation)。

在本文中，我们将简要介绍DSPy的内部工作原理，并展示如何使用它教会LLM在回答学术医学数据集中的问题时，更倾向于提供简洁的答案。

# DSPy 快速概述

这篇来自 TDS 的[文章](https://medium.com/towards-data-science/prompt-like-a-data-scientist-auto-prompt-optimization-and-testing-with-dspy-ff699f030cb7)对 DSPy 进行了深入的探讨。我们将总结并使用其中的一些示例。

为了构建一个可以优化的 LLM 应用程序，DSPy 提供了两个主要的抽象概念：**签名**和**模块**。签名是定义与 LLM 交互的系统输入和输出的一种方式。签名会在内部由 DSPy 转化为提示。

```py
class Emotion(dspy.Signature):
   # Describe the task
   """Classify emotions in a sentence."""

   sentence = dspy.InputField()
   # Adding description to the output field
   sentiment = dspy.OutputField(desc="Possible choices: sadness, joy, love, anger, fear, surprise.")
```

使用 DSPy 的 `Predict` 模块时（稍后会详细介绍），这个签名会转化为以下的提示：

```py
Classify emotions in a sentence.

---

Follow the following format.

Sentence: ${sentence}
Sentiment: Possible choices: sadness, joy, love, anger, fear, surprise.

---

Sentence:
```

然后，DSPy 还有 **模块**，这些模块定义了“预测器”，这些预测器拥有可以优化的参数，例如选择少量示例的方式。最简单的模块是 `dspy.Predict`，它不会修改签名。在本文后面，我们将使用 `dspy.ChainOfThought` 模块，它会要求 LLM 提供推理过程。

一旦我们尝试优化一个模块（或者说，DSPy 称之为“编译”一个模块），事情就开始变得有趣了。优化模块时，通常需要指定三件事：

+   需要优化的模块，

+   一个训练集，可能包含标签，

+   以及一些评估指标。

使用 `dspy.Predict` 或 `dspy.ChainOfThought` 模块时，DSPy 会在训练集上进行搜索，选择最好的示例，作为少量示例添加到提示中。在 RAG 的情况下，它还可以包括用于生成最终回答的上下文。它将这些示例称为“示范”。

你还需要指定一个[优化器](https://dspy-docs.vercel.app/docs/building-blocks/optimizers)类型，用来在参数空间中进行搜索。在这篇文章中，我们使用的是 `BootstrapFewShot` 优化器。这个算法是如何在内部工作的呢？其实它非常简单，文中提供了一些简化的伪代码：

```py
class SimplifiedBootstrapFewShot ( Teleprompter ) :
  def __init__ ( self , metric = None ) :
  self . metric = metric

  def compile ( self , student , trainset , teacher = None ) :
    teacher = teacher if teacher is not None else student
    compiled_program = student . deepcopy ()

    # Step 1\. Prepare mappings between student and teacher Predict modules .
    # Note : other modules will rely on Predict internally .
    assert student_and_teacher_have_compatible_predict_modules ( student , teacher )
    name2predictor , predictor2name = map_predictors_recursively ( student , teacher )

    # Step 2\. Bootstrap traces for each Predict module .
    # We ’ll loop over the training set . We ’ll try each example once for simplicity .
    for example in trainset :
      if we_found_enough_bootstrapped_demos () : break

      # turn on compiling mode which will allow us to keep track of the traces
      with dspy . setting . context ( compiling = True ) :
        # run the teacher program on the example , and get its final prediction
        # note that compiling = True may affect the internal behavior here
        prediction = teacher (** example . inputs () )

        # get the trace of the all interal Predict calls from teacher program
        predicted_traces = dspy . settings . trace

      # if the prediction is valid , add the example to the traces
      if self . metric ( example , prediction , predicted_traces ) :
        for predictor , inputs , outputs in predicted_traces :
          d = dspy . Example ( automated = True , ** inputs , ** outputs )
          predictor_name = self . predictor2name [id( predictor ) ]
          compiled_program [ predictor_name ]. demonstrations . append ( d )

 return compiled_program
```

搜索算法会遍历 `trainset` 中的每一个训练输入，获取预测结果，然后通过查看 `self.metric(example, prediction, predicted_traces)` 来检查它是否“通过”评估指标。如果通过，那么这些示例会被添加到已编译程序的 `demonstrations` 中。

# 让我们创建一个自定义的 Haystack 流水线

完整的代码可以在这个[食谱及关联的 colab](https://github.com/deepset-ai/haystack-cookbook/blob/main/notebooks/prompt_optimization_with_dspy.ipynb)中找到，因此我们这里只会讲解其中一些最重要的步骤。作为示例，我们使用了一个[数据集](https://huggingface.co/datasets/vblagoje/PubMedQA_instruction/viewer/default/train?row=0)，它来源于[PubMedQA 数据集](https://github.com/pubmedqa/pubmedqa)（都在 MIT 许可下）。该数据集包含基于医学研究论文摘要的问题及其相关答案。某些提供的答案可能相当长，因此我们将使用 DSPy 来“教导”LLM 优先生成更简洁的答案，同时保持最终答案的准确性。

在将前1000个示例添加到内存文档存储（可以被任何数量的[检索器](https://docs.haystack.deepset.ai/docs/retrievers)替换）后，我们现在可以构建我们的RAG管道：

```py
from haystack.components.retrievers.in_memory import InMemoryBM25Retriever
from haystack.components.generators import OpenAIGenerator
from haystack.components.builders import PromptBuilder
from haystack import Pipeline

retriever = InMemoryBM25Retriever(document_store, top_k=3)
generator = OpenAIGenerator(model="gpt-3.5-turbo")

template = """
Given the following information, answer the question.

Context:
{% for document in documents %}
    {{ document.content }}
{% endfor %}

Question: {{question}}
Answer:
"""

prompt_builder = PromptBuilder(template=template)

rag_pipeline = Pipeline()
rag_pipeline.add_component("retriever", retriever)
rag_pipeline.add_component("prompt_builder", prompt_builder)
rag_pipeline.add_component("llm", generator)

rag_pipeline.connect("retriever", "prompt_builder.documents")
rag_pipeline.connect("prompt_builder", "llm")
```

让我们试试吧！

```py
question = "What effects does ketamine have on rat neural stem cells?"

response = rag_pipeline.run({"retriever": {"query": question}, "prompt_builder": {"question": question}})

print(response["llm"]["replies"][0])
```

上述问题的答案：

> 凯他命在200、500、800和1000µM浓度下以剂量依赖的方式抑制大鼠神经干细胞的增殖。此外，凯他命还减少细胞内Ca(2+)浓度，抑制蛋白激酶C-α（PKCα）激活，并抑制大鼠神经干细胞中细胞外信号调节激酶1/2（ERK1/2）的磷酸化。这些效应似乎不是通过半胱天冬酶-3依赖性凋亡介导的。

我们可以看到，答案通常非常详细且较长。

# 使用DSPy获取更简洁的答案

我们从创建输入和输出字段的DSPy签名开始：

```py
class GenerateAnswer(dspy.Signature):
    """Answer questions with short factoid answers."""

    context = dspy.InputField(desc="may contain relevant facts")
    question = dspy.InputField()
    answer = dspy.OutputField(desc="short and precise answer")
```

如我们所见，我们在描述中已经指定我们期望一个简短的答案。

然后，我们创建一个DSPy模块，稍后将进行编译：

```py
class RAG(dspy.Module):
    def __init__(self):
        super().__init__()
        self.generate_answer = dspy.ChainOfThought(GenerateAnswer)

    # this makes it possible to use the Haystack retriever
    def retrieve(self, question):
        results = retriever.run(query=question)
        passages = [res.content for res in results['documents']]
        return Prediction(passages=passages)

    def forward(self, question):
        context = self.retrieve(question).passages
        prediction = self.generate_answer(context=context, question=question)
        return dspy.Prediction(context=context, answer=prediction.answer)
```

我们使用之前定义的Haystack检索器来搜索文档存储中的文档 `results = retriever.run(query=question)`。预测步骤使用DSPy模块 `dspy.ChainOfThought`，该模块教会语言模型在做出回答之前逐步思考。

在编译过程中，优化后的提示将如下所示：

![](../Images/68ebd2d7ccc8136d323e686b67686a38.png)

所有粗体文本都被DSPy选择的示例和特定查询的问答上下文替换。由作者制作。

最后，我们需要定义我们希望优化的度量标准。评估器将包括两个部分：

+   `[SASEvaluator](https://docs.haystack.deepset.ai/docs/sasevaluator)` : 语义答案相似度度量是一个介于0和1之间的分数，用于计算给定输出与实际输出之间的相似度。

+   我们将对超过20个单词的答案进行惩罚，惩罚会根据单词数的增加成比例增长，最大为0.5。

```py
from haystack.components.evaluators import SASEvaluator

sas_evaluator = SASEvaluator()
sas_evaluator.warm_up()

def mixed_metric(example, pred, trace=None):
    semantic_similarity = sas_evaluator.run(ground_truth_answers=[example.answer], predicted_answers=[pred.answer])["score"]

    n_words=len(pred.answer.split())
    long_answer_penalty=0
    if 20<n_words<40:
      long_answer_penalty = 0.025 * (n_words - 20)
    elif n_words>=40:
      long_answer_penalty = 0.5

    return semantic_similarity - long_answer_penalty
```

我们的评估数据集由20个训练示例和50个开发集示例组成。

如果我们使用下面的代码评估当前的简单RAG管道，我们得到一个平均得分为0.49。

查看一些示例可以帮助我们直观理解得分的作用：

> 问题：新辅助化疗放疗到手术的时间增加是否与食管癌的病理完全缓解率较高相关？
> 
> 预测答案：是的，新辅助化疗放疗到手术的时间增加与食管癌的病理完全缓解率较高相关。
> 
> **得分：0.78**

但是

> 问题：基于静息状态间歇期MEG记录的癫痫灶定位是否可以不考虑尖波的存在或缺失？
> 
> 预测答案：是的。
> 
> **得分：0.089**

从这些示例中我们可以看到，如果答案太短，它会得到一个较低的得分，因为它与真实答案的相似度降低。

然后，我们使用DSPy编译RAG管道：

```py
from dspy.teleprompt import BootstrapFewShot

optimizer = BootstrapFewShot(metric=mixed_metric)

compiled_rag = optimizer.compile(RAG(), trainset=trainset)
```

在我们完成这一过程并重新评估编译后的管道后，得分现在是0.69！

现在是时候获取最终优化的提示并将其添加到我们的Haystack管道中。

# 获取最终的提示优化管道

我们可以通过查看`compiled_rag`对象中的`demos`字段来查看DSPy选择的少量示例：

```py
compiled_rag.predictors()[0].demos
```

最终提示中提供了两种类型的示例：少量示例和引导演示，类似于上面所展示的提示。少量示例是问答对：

```py
Example({'question': 'Does increased Syk phosphorylation lead to overexpression of TRAF6 in peripheral B cells of patients with systemic lupus erythematosus?', 'answer': 'Our results suggest that the activated Syk-mediated TRAF6 pathway leads to aberrant activation of B cells in SLE, and also highlight Syk as a potential target for B-cell-mediated processes in SLE.'})
```

而引导演示则包含LLM的完整追踪，包括提供的上下文和推理（在下面的`rationale`字段中）：

```py
Example({'augmented': True, 'context': ['Chronic rhinosinusitis (CRS) …', 'Allergic airway …', 'The mechanisms and ….'], 'question': 'Are group 2 innate lymphoid cells ( ILC2s ) increased in chronic rhinosinusitis with nasal polyps or eosinophilia?', 'rationale': 'produce the answer. We need to consider the findings from the study mentioned in the context, which showed that ILC2 frequencies were associated with the presence of nasal polyps, high tissue eosinophilia, and eosinophil-dominant CRS.', 'answer': 'Yes, ILC2s are increased in chronic rhinosinusitis with nasal polyps or eosinophilia.'})
```

现在我们所需要做的就是提取这些由DSPy找到的示例，并将它们插入到我们的Haystack管道中：

```py
static_prompt = lm.inspect_history(n=1).rpartition("---\n")[0]
```

我们的新管道变为：

```py
from haystack.components.retrievers.in_memory import InMemoryBM25Retriever
from haystack.components.generators import OpenAIGenerator
from haystack.components.builders import PromptBuilder, AnswerBuilder
from haystack import Pipeline

template = static_prompt+"""
---

Context:
{% for document in documents %}
    «{{ document.content }}»
{% endfor %}

Question: {{question}}
Reasoning: Let's think step by step in order to
"""

new_prompt_builder = PromptBuilder(template=template)

new_retriever = InMemoryBM25Retriever(document_store, top_k=3)
new_generator = OpenAIGenerator(model="gpt-3.5-turbo")

answer_builder = AnswerBuilder(pattern="Answer: (.*)")

optimized_rag_pipeline = Pipeline()
optimized_rag_pipeline.add_component("retriever", new_retriever)
optimized_rag_pipeline.add_component("prompt_builder", new_prompt_builder)
optimized_rag_pipeline.add_component("llm", new_generator)
optimized_rag_pipeline.add_component("answer_builder", answer_builder)

optimized_rag_pipeline.connect("retriever", "prompt_builder.documents")
optimized_rag_pipeline.connect("prompt_builder", "llm")
optimized_rag_pipeline.connect("llm.replies", "answer_builder.replies")
```

让我们检查一下之前尝试过的相同问题。

问题：

> 速氯胺对大鼠神经干细胞有哪些作用？

之前：

> 速氯胺（Ketamine）在200、500、800和1000µM的浓度下以剂量依赖性方式抑制大鼠神经干细胞的增殖。此外，速氯胺还降低了细胞内Ca(2+)浓度，抑制了蛋白激酶C-α（PKCα）激活以及大鼠神经干细胞中细胞外信号调节激酶1/2（ERK1/2）的磷酸化。这些效应似乎并非通过半胱天冬酶-3依赖的凋亡途径介导。

之后：

> 速氯胺在较高浓度下抑制大鼠神经干细胞的增殖，而不影响凋亡。此外，它降低了细胞内钙浓度，抑制了PKCα激活和这些细胞中ERK1/2的磷酸化。

工作完成！

## 简单的结论

在这篇文章中，我们使用DSPy优化了在Haystack RAG管道中使用的提示。我们通过使用基于Haystack评估框架的自定义度量，惩罚LLM的长答案，同时保持与正确答案的相似度较高。通过这种方法，我们成功地提高了大约40%的性能，而无需进行任何手动提示工程。
