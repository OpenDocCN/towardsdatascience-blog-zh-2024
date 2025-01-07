# 如何充分利用LLM生产数据：模拟用户反馈

> 原文：[https://towardsdatascience.com/how-to-make-the-most-out-of-llm-production-data-simulated-user-feedback-843c444febc7?source=collection_archive---------6-----------------------#2024-04-11](https://towardsdatascience.com/how-to-make-the-most-out-of-llm-production-data-simulated-user-feedback-843c444febc7?source=collection_archive---------6-----------------------#2024-04-11)

## 一种新颖的方法，通过使用生产数据来模拟用户反馈，以测试和评估您的LLM应用

[](https://medium.com/@pasquale_68605?source=post_page---byline--843c444febc7--------------------------------)[![Pasquale Antonante, Ph.D.](../Images/97733117413b3daeb79886e171ab30c9.png)](https://medium.com/@pasquale_68605?source=post_page---byline--843c444febc7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--843c444febc7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--843c444febc7--------------------------------) [Pasquale Antonante, Ph.D.](https://medium.com/@pasquale_68605?source=post_page---byline--843c444febc7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--843c444febc7--------------------------------) ·阅读时间9分钟·2024年4月11日

--

![](../Images/17386b21ff3d693432dc70e815185505.png)

图片由作者和ChatGPT提供。“两只羊驼的图像，一只竖起大拇指，另一只竖起大拇指向下”提示。ChatGPT，4，OpenAI，2024年4月10日。[https://chat.openai.com.](https://chat.openai.com./)

一系列博客文章，分享我们如何评估和改进您的GenAI应用管道的观点

*(由Pasquale Antonante和Yi Zhang在Relari.ai撰写)*

LLM应用开发的世界总是变化无常——每周都有新的技巧、模型和应用出现。随着技术的进步，用户的期望也在不断提高。保持领先地位是确保用户不断回归的关键。

现在的问题变成了：如何衡量性能提升？当你调整提示、调整温度或更换模型时，你是否曾停下来思考过，“我的用户真的会更喜欢这个吗？”

在这篇文章中，我们将详细介绍如何利用早期部署中的应用内用户反馈（或内部人工评估）快速塑造产品的未来版本。我们将讨论传统反馈机制的局限性，并介绍一种新技术，使AI开发者能够直接在离线测试和迭代中使用反馈数据（在新的部署之前），从而使开发周期更加灵活，能够更好地响应用户偏好。

# 理解用户反馈的价值

## 为什么用户反馈很重要以及它的挑战

在开发基于大语言模型（LLM）的应用程序时，我们常常会遇到一个特定的问题需要解决，*例如*，某种特定类型的问题准确度较低。当我们在调整提示语、参数、架构等方面进行实验时，我们希望评估新流程的表现，特别是用户是否会喜欢您应用程序的新版本。最直接的方式是通过A/B测试每个变化并收集用户的反馈数据，如点赞/点踩、评分或书面评论，但实际上，由于以下几个原因，这会面临一些挑战：

1.  **收集缓慢：**除非您的应用程序已经有了大量的用户流量，否则您无法获得太多的反馈数据。据我们的客户反馈，AI应用中的反馈参与率通常在<1%（正常）到~10%（优秀，通常通过精心设计的UI/UX来鼓励更多的反馈）之间。因此，可能需要很长时间才能收集到足够的反馈数据，从而做出统计学上可靠的判断，判断某个变化是否在用户中产生了积极或消极的反响。

1.  **破坏用户关系的风险：**直接与用户测试是获得洞察的最有效方式，但如果用户遇到不满意的版本，确实有破坏与他们关系的风险。用户判断可能非常迅速，可能会在出现错误的第一时间就放弃您的应用程序。因此，开发者往往选择更保守或影响较小的变化进行A/B测试，保留更大胆、更具创新性的更新供内部测试。这种方法既允许实验，又能最大程度地减少疏远用户群体的风险。

1.  **测量不一致：**由于大多数AI应用程序具有相当大的开放性，考虑到不同用户可能以不同的方式与您的产品互动，因此通常很难获得真正的“同类对比”的反馈数据。因此，基于LLM的应用程序的反馈数据A/B测试往往比传统应用程序的反馈数据更加嘈杂。

在下一节中，我们将介绍一种新颖的方法，已经被我们部署到多个客户身上，帮助他们在离线开发中充分利用用户反馈数据。

# 一种新颖的方法：模拟用户反馈

针对这些用户反馈收集的挑战，我们开发了一种新颖的方法，通过少量的用户（或内部标注的）反馈数据来模拟用户反馈。具体来说，我们使用度量集成和符合性预测来学习用户偏好，并在开发阶段离线使用它们。其核心是我们学习用户如何权衡不同的标准（例如语气、简洁性等），并利用符合性预测提供预测来量化信心。这种方法通过提供一种在完全实施新功能或更改之前预测用户可能反应的方式，极大地加速了LLM应用程序的开发。

为了评估其有效性，我们将此方法与使用单一LLM调用来评估响应不同方面并做出判断的传统方法进行了比较。为了比较这两种方法（建议的方法与单一LLM调用），我们使用了[Unified-Feedback](https://huggingface.co/datasets/llm-blender/Unified-Feedback)数据集进行实验。我们使用Kendall tau这一排名相关性度量，比较了我们的用户反馈模拟和单一LLM调用方法产生的排名与由人工评估建立的真实排名。这一分析不仅帮助我们评估了各方法之间的一致性，还可以比较每种方法与人类排名的偏好顺序。

**我们的实验表明，用户反馈模拟与人类评判之间的相关性达到了93%，远远超过了单一LLM调用方法的约70%的相关性。这表明，在排名方面，模拟的用户反馈提供了更接近人类判断的近似结果。**

![](../Images/60384adfaaf39c33f4a4ee9b84534c31.png)

两种方法的Kendall tau值。较高的值表示该方法生成的排名与人类判断之间的相关性更强。模拟用户反馈（建议的方法，浅蓝色）在识别改进时与人类的意见高度一致，表明它更准确地反映了人类在识别和评估改进方面的判断。图像由作者提供。

模拟用户反馈表现更好的原因有两个：

1.  它通过实际用户反馈学习不同标准的重要性，使得该方法能够根据您的使用案例进行定制。

1.  虽然个别标准可能出现在LLM的训练集里，但不同标准的复杂（且可能是庞大的）集合可能并未出现在训练数据中，这使得LLM评估器更难准确判断。

虽然单一LLM调用能够识别管道中的重大改进，但它未必能发现成熟管道中至关重要的、更为频繁的小改进。然而，模拟的用户反馈与人类判断具有高度相关性，使其能够发现这些渐进的改进。

作为旁注，虽然我们本可以利用这些数据来微调一个大语言模型（LLM），但这种方法通常需要更多的数据，并且不如直观易懂。

在下一部分，我们将演示如何创建您的模拟用户反馈。

# 工作原理

在本节中，我们将展示如何使用开源库[continuous-eval](https://github.com/relari-ai/continuous-eval)来创建模拟用户反馈。

以一个问答聊天机器人应用为例。部署后，用户开始用点赞或点踩来评分反馈，表示需要提升性能。对于这个例子，我们将使用`correctness`这个在continuous-eval中定义的示例：

```py
dataset = Dataset(example_data_downloader("correctness"))

# Samples are annotated with "correct", "incorrect" or "refuse-to-answer"
# We remove the samples where the LLL refused to answer (i.e., said "I don't know")
dataset.filter(lambda x: x["annotation"] != "refuse-to-answer")
dataset.sample(300)  # Only for this example: randomly sample 300 examples
```

正如我们所提到的，我们希望创建一些自定义标准。我们利用`LLMBasedCustomMetric`类来定义*语气*和*简洁性*指标。为此，我们需要定义指标并提供评分标准。

关于语气：

```py
tone = LLMBasedCustomMetric(
    name="Tone",
    definition="The Tone/Content Issues metric evaluates the appropriateness and accuracy of the tone and content in responses to specific questions. It focuses on ensuring that the tone is professional and suitable for the context, and that the content accurately addresses the question without unnecessary deviations or inaccuracies. This metric is crucial for maintaining a professional image and ensuring clear, direct communication.",
    scoring_rubric="""Use the following rubric to assign a score to the answer based on its tone:
- Score 1: The response is inappropriate or inaccurate, with a tone that is either too informal, overly strong, or not suited to the professional context. The content may be irrelevant, incorrect, or fail to directly address the question posed.
- Score 2: The response is mostly appropriate and accurate but may contain minor tone or content issues. The tone is generally professional but may slip into informality or unnecessary strength in places. The content addresses the question but may include minor inaccuracies or unnecessary details.
- Score 3: The response is appropriate and accurate, with a tone that is professional and suited to the context. The content directly and correctly addresses the question without unnecessary deviations or inaccuracies.""",
    scoring_function=ScoringFunctions.Numeric(min_val=1, max_val=3),
    model_parameters={"temperature": 0},
)
```

关于简洁性：

```py
conciseness = LLMBasedCustomMetric(
    name="Conciseness",
    definition="Conciseness in communication refers to the expression of ideas in a clear and straightforward manner, using the fewest possible words without sacrificing clarity or completeness of information. It involves eliminating redundancy, verbosity, and unnecessary details, focusing instead on delivering the essential message efficiently. ",
    scoring_rubric="""Use the following rubric to assign a score to the answer based on its conciseness:
- Score 1: The answer is overly verbose, containing a significant amount of unnecessary information, repetition, or redundant expressions that do not contribute to the understanding of the topic.
- Score 2: The answer includes some unnecessary details or slightly repetitive information, but the excess does not severely hinder understanding.
- Score 3: The answer is clear, direct, and to the point, with no unnecessary words, details, or repetition.""",
    scoring_function=ScoringFunctions.Numeric(min_val=1, max_val=3),
    model_parameters={"temperature": 0},
)
```

我们将*语气*和*简洁性*与更多的标准化指标结合使用，特别是我们会考虑

+   回答正确性（`DeterministicAnswerCorrectens` 和 `LLMBasedAnswerCorrectness`）

+   回答相关性（`LLMBasedAnswerRelevance`）

+   风格一致性（`LLMBasedStyleConsistency`）

+   可读性（`FleschKincaidReadability`）

下一步是将所有指标汇总，并指定数据集中应使用的字段来计算这些指标。为此，我们可以使用`SingleModulePipeline`

```py
pipeline = SingleModulePipeline(
    dataset=dataset,
    eval=[
        DeterministicAnswerCorrectness().use(
            answer=dataset.answer,
            ground_truth_answers=dataset.ground_truths,
        ),
        LLMBasedAnswerCorrectness().use(
            question=dataset.question,
            answer=dataset.answer,
            ground_truth_answers=dataset.ground_truths,
        ),
        LLMBasedAnswerRelevance().use(
            question=dataset.question, answer=dataset.answer
        ),
        LLMBasedStyleConsistency().use(
            answer=dataset.answer, ground_truth_answers=dataset.ground_truths
        ),
        FleschKincaidReadability().use(answer=dataset.answer),
        tone.use(
            question=dataset.question,
            answer=dataset.answer,
            ground_truth_answers=dataset.ground_truths,
        ),
        conciseness.use(
            question=dataset.question,
            answer=dataset.answer,
            ground_truth_answers=dataset.ground_truths,
        ),
    ],
)
```

并使用`EvaluationManager`运行所有指标

```py
eval_manager = EvaluationManager(pipeline)
# The dataset already contains the model output so we just set the evaluation results
eval_manager.evaluation.results = dataset.data
eval_manager.run_metrics()  # Note: there is no progress bar, it might take a few minutes
```

下一步是训练模拟用户反馈预测器

```py
datasplit = DataSplit(
    X=eval_manager.metrics.to_pandas(),
    y=map(lambda x: 1 if x == "correct" else 0, dataset["annotation"]),
    split_ratios=SplitRatios(train=0.6, test=0.2, calibration=0.2),
)

# We use the train and calibration sets to train the classifier
predictor = EnsembleMetric(training=datasplit.train, calibration=datasplit.calibration)
```

这个模拟用户反馈预测器能够在测试集上正确预测人类反馈的准确率为96.67%。

我们可以利用所提出的方法更好地理解用户关注的重点。下面是通过模拟用户反馈预测器学到的每个指标的重要性。

![](../Images/6404708dceb38cba2037dde13c071620.png)

通过模拟用户反馈预测器学到的每个指标的重要性。图片由作者提供。

从图表中，我们可以看到*正确性*（包括*令牌重叠*，它是衡量正确性的另一种方式）和与问题的*相关性*是用户偏好的最重要预测因素。但用户也会将*语气*和*风格一致性*纳入决策。同时，我们可以看到*简洁性*和*可读性*的重要性较低。回顾这个图表可以为我们提供有关用户偏好的宝贵见解，清楚地表明哪些元素是必要的，哪些可以在需要做出妥协时进行调整。

# 总结

收集用户反馈是一个挑战，但它是大型语言模型（LLM）开发者最重要的信息。通过在离线测试中模拟用户反馈，我们显著缩短了反馈从实际场景到开发者之间的传递时间，同时保持了积极的用户关系。

在实践中，我们的方法已被证明能够与实际人类反应高度一致，超过了传统依赖于孤立LLM响应的方法。这一策略使得生成性AI应用得以渐进式改进，促进了持续的优化，并与用户期望保持更高的一致性。

—

注：我们很快将发布一篇关于这种方法的研究论文，敬请关注！

# 下一步

+   创建黄金数据集的技术

+   如何最大化使用你的嵌入模型？

+   我应该使用哪些数据进行微调？

# 早期帖子

+   [RAG管道评估实用指南 第1部分：检索](https://medium.com/p/27a472b09893)

+   [RAG管道评估实践指南第2部分：生成](https://medium.com/relari/a-practical-guide-to-rag-evaluation-part-2-generation-c79b1bde0f5d)

+   [黄金数据集对LLM管道评估有多重要？](https://medium.com/relari/how-important-is-a-golden-dataset-for-llm-pipeline-evaluation-4ef6deb14dc5)

+   [案例研究：无参考与基于参考的评估](https://medium.com/relari/case-study-reference-free-vs-reference-based-evaluation-of-rag-pipeline-9a49ef49866c)

+   [如何评估复杂的GenAI应用：一种细化方法](https://medium.com/relari/how-to-evaluate-complex-genai-apps-a-granular-approach-0ab929d5b3e2)
