# 模型评估与任务评估

> 原文：[https://towardsdatascience.com/model-evaluations-versus-task-evaluations-5bc742054957?source=collection_archive---------7-----------------------#2024-03-26](https://towardsdatascience.com/model-evaluations-versus-task-evaluations-5bc742054957?source=collection_archive---------7-----------------------#2024-03-26)

![](../Images/d871ad67918406174ff5c74003dc75fb.png)

由作者使用Dall-E 3创建的图像

## 理解LLM应用中的差异

[](https://aparnadhinak.medium.com/?source=post_page---byline--5bc742054957--------------------------------)[![Aparna Dhinakaran](../Images/e431ee69563ecb27c86f3428ba53574c.png)](https://aparnadhinak.medium.com/?source=post_page---byline--5bc742054957--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5bc742054957--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5bc742054957--------------------------------) [Aparna Dhinakaran](https://aparnadhinak.medium.com/?source=post_page---byline--5bc742054957--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5bc742054957--------------------------------) ·9分钟阅读·2024年3月26日

--

想象一下飞机。你首先想到的是什么？现在再想象一架波音737和一架[V-22鱼鹰](https://www.af.mil/About-Us/Fact-Sheets/Display/Article/104531/cv-22-osprey/)。这两者都是旨在运输货物和人员的飞机，但它们服务的目的不同——一种更为通用（商业航班和货运），另一种非常具体（为特种作战部队执行渗透、撤离和补给任务）。它们看起来完全不同，因为它们是为不同的活动而设计的。

随着LLM的兴起，我们见证了第一批真正的通用机器学习模型。它们的通用性在许多方面帮助了我们：

+   同一个工程团队现在可以进行情感分析和结构化数据提取

+   许多领域的从业者可以共享知识，从而使整个行业能够相互受益于彼此的经验

+   有许多行业和工作领域，其中相同的经验是有用的

但正如我们在飞机中看到的，通用性需要与在特定任务上出色表现截然不同的评估方法，归根结底，商业价值通常来自于解决特定的问题。

这是模型评估与任务评估差异的一个很好的类比。模型评估侧重于总体的综合评估，而任务评估则侧重于评估特定任务的表现。

# 不止一个LLM评估

*LLM评估* 这个术语常常被广泛使用。[OpenAI早期发布了一些工具来进行LLM评估](/how-to-best-leverage-openais-evals-framework-c38bcef0ec47)，例如。大多数从业者更关注LLM任务评估，但这一区分并不总是很清晰。

## 有什么区别？

模型评估关注的是模型的“整体健身情况”。它在各种任务上的表现如何？

任务评估则专门设计用来检查模型是否适合你的特定应用。

一般锻炼并且身体素质较好的人，在真实比赛中可能会在职业相扑选手面前表现不佳，而模型评估无法与任务评估在评估你特定需求的能力上相提并论。

# 模型评估

模型评估专门用于构建和微调通用模型。它们基于你给模型提出的一组问题以及你用来评分的地面真实答案。可以将其想象成参加SAT考试。

虽然模型评估中的每个问题都不同，但通常有一个共同的测试领域。每个指标都有一个特定的目标主题或技能。例如，HellaSwag的表现已经成为衡量LLM质量的流行方式。

[HellaSwag](https://rowanzellers.com/hellaswag/) 数据集包含了一系列上下文和多项选择题，每个问题都有多个可能的完成选项。只有一个选项是合乎逻辑且合理的，其他选项虽然看似合理，但其实是错误的。这些完成项旨在对AI模型提出挑战，不仅要求语言理解能力，还需要常识推理才能选择正确的选项。

这是一个例子：

*一盘土豆被放入烤箱并取出。一大盘蛋糕被翻过来并放到柜台上。一大盘肉*

*A. 被放到烤土豆上*

*B. 土豆和腌菜被放入烤箱中*

*C. 被准备好后，由助手在完成时从烤箱中取出。*

另一个例子是 MMLU。[MMLU](https://paperswithcode.com/dataset/mmlu) 涵盖了多个学科的任务，包括科学、文学、历史、社会科学、数学以及法律和医学等专业领域。这些学科的多样性旨在模拟人类学习者所需的知识和理解的广度，使其成为测试模型处理多面语言理解挑战能力的好方法。

这里有一些例子——你能解答它们吗？

*在以下哪些热力学过程中，理想气体的内能增加等于加热到气体的热量？*

*A. 恒温*

*B. 恒体积*

*C. 恒压*

*D. 绝热过程*

![](../Images/2336f2e5e5ddc480643c4db20ca56fda.png)

图片来源：作者

[Hugging Face排行榜](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard)可能是获取此类模型评估的最知名平台。排行榜跟踪开源的大型语言模型，并记录许多模型评估指标。这通常是一个很好的起点，用来理解开源LLM在不同任务表现上的差异。

多模态模型需要更多的评估。[Gemini论文](https://storage.googleapis.com/deepmind-media/gemini/gemini_1_report.pdf)展示了多模态引入了许多其他基准，比如VQAv2，它测试理解和整合视觉信息的能力。这些信息不仅仅是简单的物体识别，而是对行动和物体之间关系的解读。

同样，针对音频和视频信息以及如何跨模态整合的指标也存在。

这些测试的目标是区分两个模型或同一个模型的两个不同快照。选择一个适合你应用的模型很重要，但这是你做的事，一般情况下只是一次性操作或非常不频繁的操作。

![](../Images/aa0265520e614b0de21aa34794f3abb7.png)

作者提供的图片

# 任务评估

更常见的问题是通过任务评估来解决的。基于任务的评估目标是分析使用LLM作为评判者的模型表现。

+   你的检索系统是否获取了正确的数据？

+   你的回答中有幻觉吗？

+   系统是否用相关的答案回答了重要问题？

有些人可能对LLM评估其他LLM感到有些不确定，但我们每天都有人工评估其他人。

模型评估和任务评估的真正区别在于：在模型评估中，我们会提出许多不同的问题，而在任务评估中，问题保持不变，只有数据会变化。例如，假设你在操作一个聊天机器人。你可以在数百次客户互动中使用你的任务评估，并问它：“*这里有幻觉吗？*”这个问题在所有对话中始终相同。

![](../Images/864534f219b59a6179b2009f4f190190.png)

作者提供的图片

有几个库旨在帮助实践者构建这些评估：[Ragas](https://docs.ragas.io/en/latest/index.html)，[Phoenix](https://phoenix.arize.com/)（完全披露：作者领导了开发Phoenix的团队），[OpenAI](https://github.com/openai/evals/tree/main/evals)，[LlamaIndex](https://docs.llamaindex.ai/en/latest/optimizing/evaluation/evaluation.html)。

## 它们是如何工作的？

任务评估整体上评估应用程序的每个输出的表现。我们来看看构建一个评估任务需要哪些内容。

## 建立基准

基础在于建立一个稳健的基准。这从创建一个能够准确反映 LLM 将遇到的场景的黄金数据集开始。该数据集应包含地面真实标签——通常来源于细致的人工审核——作为对比标准。别担心，通常你可以用几十到几百个示例来完成。选择合适的 LLM 进行评估也至关重要。虽然它可能与应用程序的主要 LLM 不同，但应该与成本效益和准确性目标保持一致。

## 制定评估模板

任务评估过程的核心是评估模板。该模板应清晰定义输入（例如，用户查询和文档）、评估问题（例如，文档与查询的相关性）和预期的输出格式（二元或多类别相关性）。根据应用程序的具体需求，可能需要调整模板，以确保它能够准确评估 LLM 在黄金数据集上的表现。

这里是一个用来评估问答任务的模板示例。

```py
You are given a question, an answer and reference text. You must determine whether the given answer correctly answers the question based on the reference text. Here is the data:
  [BEGIN DATA]
  ************
  [QUESTION]: {input}
  ************
  [REFERENCE]: {reference}
  ************
  [ANSWER]: {output}
  [END DATA]
Your response should be a single word, either "correct" or "incorrect", and should not contain any text or characters aside from that word.
"correct" means that the question is correctly and fully answered by the answer. 
"incorrect" means that the question is not correctly or only partially answered by the answer. 
```

## 度量标准与迭代

在你的黄金数据集上运行评估，可以生成关键度量指标，如准确率、精确度、召回率和 F1 分数。这些指标为评估模板的有效性提供了洞察，并突出改进的领域。迭代至关重要；根据这些度量标准精炼模板，确保评估过程与应用目标保持一致，同时避免对黄金数据集的过拟合。

在任务评估中，仅依赖总体准确率是不够的，因为我们总是会遇到显著的类别不平衡。精确度和召回率提供了更全面的视角，强调准确识别相关和不相关结果的重要性。平衡的度量方法确保评估对提升 LLM 应用有实际贡献。

## LLM 评估的应用

一旦评估框架就绪，下一步是将这些评估直接应用到你的 LLM 应用中。这涉及将评估过程集成到应用程序的工作流程中，以便实时评估 LLM 对用户输入的响应。这个持续的反馈循环对于保持和提高应用的相关性和准确性至关重要。

## 系统生命周期中的评估

有效的任务评估不仅限于单一阶段，而是贯穿整个大语言模型（LLM）系统的生命周期。从生产前的基准测试和评估，到生产中的持续性能评估， [LLM 评估](https://arize.com/blog-course/llm-evaluation-the-definitive-guide/) 确保系统始终能够响应用户需求。

## 示例：模型是否出现幻觉？

让我们更详细地看一个幻觉示例。

![](../Images/474e7210ed5ea5d8d8d011a5c4e42f96.png)

作者示例

由于幻觉是大多数从业者面临的普遍问题，现有一些基准数据集可供使用。这是一个很好的起步，但你通常需要在公司内部拥有一个定制的数据集。

接下来的重要步骤是开发提示模板。这里同样一个好的库可以帮助你入门。我们之前看到了一个示例提示模板，这里我们看到另一个专门针对幻觉的模板。你可能需要根据你的需求进行调整。

```py
In this task, you will be presented with a query, a reference text and an answer. The answer is
generated to the question based on the reference text. The answer may contain false information, you
must use the reference text to determine if the answer to the question contains false information,
if the answer is a hallucination of facts. Your objective is to determine whether the reference text
contains factual information and is not a hallucination. A 'hallucination' in this context refers to
an answer that is not based on the reference text or assumes information that is not available in
the reference text. Your response should be a single word: either "factual" or "hallucinated", and
it should not include any other text or characters. "hallucinated" indicates that the answer
provides factually inaccurate information to the query based on the reference text. "factual"
indicates that the answer to the question is correct relative to the reference text, and does not
contain made up information. Please read the query and reference text carefully before determining
your response.

    [BEGIN DATA]
    ************
    [Query]: {input}
    ************
    [Reference text]: {reference}
    ************
    [Answer]: {output}
    ************
    [END DATA]

    Is the answer above factual or hallucinated based on the query and reference text?

Your response should be a single word: either "factual" or "hallucinated", and it should not include any other text or characters. 
"hallucinated" indicates that the answer provides factually inaccurate information to the query based on the reference text.
"factual" indicates that the answer to the question is correct relative to the reference text, and does not contain made up information.
Please read the query and reference text carefully before determining your response. 
```

现在你已经准备好将黄金数据集中的查询传递给你的评估LLM，并让它标记出幻觉。当你查看结果时，记住应该存在类别不平衡。你需要跟踪精确度和召回率，而不是整体准确率。

构建混淆矩阵并将其可视化非常有用。当你有了这样的图表时，你可以对你的LLM性能感到放心。如果性能不尽如人意，你可以随时优化提示模板。

![](../Images/a3804aa615cd2edded178962eaeb8b6e.png)

*评估任务评估性能的示例，以便用户能够建立对其评估的信心*

在评估构建完成后，你现在拥有了一个强大的工具，可以用已知的精确度和召回率标记所有数据。你可以使用它来跟踪你系统中的幻觉，无论是在开发阶段还是生产阶段。

# 区别总结

让我们总结一下任务评估和模型评估之间的区别。

![](../Images/7e289f5124ebc71d4065f18dc29f0bbc.png)

表格由作者提供

# 要点总结

最终，模型评估和任务评估在构建功能性LLM系统时都非常重要。理解[何时以及如何应用每种评估方法](https://arize.com/blog-course/large-language-model-evaluations-vs-llm-task-evaluations-in-llm-application-development/)是很关键的。对于大多数从业者来说，大部分时间将花费在任务评估上，这为系统在特定任务上的性能提供了衡量标准。
