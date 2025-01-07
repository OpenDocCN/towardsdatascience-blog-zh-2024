# 朝着提高LLM应用中的真实性前进

> 原文：[https://towardsdatascience.com/towards-increased-truthfulness-in-llm-applications-d25fc35c7ef9?source=collection_archive---------7-----------------------#2024-03-01](https://towardsdatascience.com/towards-increased-truthfulness-in-llm-applications-d25fc35c7ef9?source=collection_archive---------7-----------------------#2024-03-01)

## 当前研究中的面向应用的方法

[](https://medium.com/@Marlon_H?source=post_page---byline--d25fc35c7ef9--------------------------------)[![Marlon Hamm](../Images/fc8c340a9729c9d1623692445e651e57.png)](https://medium.com/@Marlon_H?source=post_page---byline--d25fc35c7ef9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d25fc35c7ef9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d25fc35c7ef9--------------------------------) [Marlon Hamm](https://medium.com/@Marlon_H?source=post_page---byline--d25fc35c7ef9--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d25fc35c7ef9--------------------------------) ·阅读时间：10分钟·2024年3月1日

--

# 摘要

本文探讨了增强检索增强生成（RAG）应用输出真实性的方法，重点解决诸如幻觉现象和依赖预训练知识等问题。我分析了不真实结果的原因，评估了评估真实性的方法，并提出了解决方案以提高准确性。本研究强调了RAG输出中基础性和完整性的重要性，建议对大型语言模型（LLM）进行微调，并采用元素感知摘要方法以确保事实准确性。此外，文章还讨论了可扩展的评估指标的使用，如可学习评估指标（LENS）和基于思维链（CoT）的评估，用于实时输出验证。文章强调了在增加真实性的好处与可能带来的成本和性能影响之间保持平衡的必要性，并建议根据应用需求选择性地实施这些方法。

# 1\. 引言

一种广泛使用的、大型语言模型（LLM）架构是检索增强生成（RAG）。RAG是一种通过将参数化记忆（即LLM预训练模型）与非参数化记忆（即检索到的文档）结合来扩展LLM记忆的方法。为此，从向量数据库中检索最相关的文档，并将它们与用户问题和定制的提示一起传递给LLM，LLM根据这些信息生成回答（见图1）。更多详细信息，请参阅Lewis等人（2021年）。

![](../Images/6a9c52e4fc242cc2c7cfc03e70b57cae.png)

图1 — 简化的RAG架构

一个现实世界的应用实例可以将LLM连接到医疗指南文档的数据库中。医疗从业人员可以通过向RAG询问自然语言问题，取代手动查找，作为一种“搜索引擎”来使用。该应用将回答用户的问题，并引用源指南。如果答案基于参数化记忆，例如回答的是包含在预训练中而不是连接数据库中的指南，或者如果LLM产生幻觉，这可能会产生严重影响。

首先，如果医疗从业人员核实了引用的指南，他们可能会对应用的回答失去信任，导致使用量下降。其次，更令人担忧的是，如果不是每个答案都经过核实，可能会错误地假设答案是基于查询的医疗指南，这将直接影响患者的治疗。这突显了RAG应用中输出真实性的重要性。

在本文中，评估RAG时，真实性被定义为在检索文档的事实知识中扎实根植。为了解决这个问题，衍生出了一个总体研究问题（GRQ）和三个具体研究问题（SRQ）。

GRQ: 如何提高RAG输出的真实性？

SRQ 1: 什么原因导致RAG应用产生不真实的结果？

SRQ 2: 如何评估真实性？

SRQ 3: 可以使用哪些方法来提高真实性？

为了回答GRQ，SRQ将根据文献研究按顺序进行分析。目的是识别可以实施的解决方法，适用于医学领域中上述例子的使用场景。最终将推荐两类解决方案方法，以便进一步分析和定制。

# 2. 不真实的RAG输出

如前所定义，真实的答案应当扎实地基于检索文档中的事实知识。衡量这一点的一个指标是事实一致性，衡量摘要中是否包含未经源文献支持的不真实或误导性事实（Liu等，2023）。这一指标被作为多个基准中的关键评估指标（Kim等，2023；Fabbri等，2021；Deutsch & Roth，2022；Wang等，2023；Wu等，2023）。在RAG领域，这通常被称为“扎实性”（Levonian等，2023）。此外，为了考虑真实答案的实用性，其完整性也具有重要意义。接下来的段落将深入探讨RAG结果不真实的原因。这指的是图1中的生成步骤，该步骤根据用户问题总结检索到的文档。

首先，RAG 应用的基础性（groundedness）受到 LLM 的回答是基于参数记忆而非检索文档中的事实知识的影响。例如，如果回答来源于预训练知识或是由于幻觉现象引起的，这种情况可能会发生。幻觉仍然是 LLM 的一个根本问题（Bang 等, 2023；Ji 等, 2023；Zhang & Gao, 2023），即使是强大的 LLM 也会受到其影响（Liu 等, 2023）。根据定义，低基础性会导致不真实的 RAG 结果。

其次，完整性描述了 LLM 的回答是否缺乏文档中的事实知识。这可能是由于 LLM 的总结能力较弱，或缺乏理解事实知识所需的领域知识（T. Zhang 等, 2023）。输出可能依然高度基于事实。然而，回答可能相对于文档而言是不完整的，从而导致用户对数据库内容的错误认知。此外，如果文档中的事实知识缺失，LLM 可能会通过利用其自身的参数记忆来弥补这一点，进而引发上述问题。

在确定了不真实输出的关键原因之后，有必要首先测量并量化这些错误，然后才能寻找解决方案。因此，以下部分将介绍针对上述不真实RAG输出来源的测量方法。

# 3. 评估真实性

在阐述了基础性和完整性及其来源后，本节旨在指导它们的测量方法。我将从广为人知的通用方法开始，并继续突出最近的趋势。TruLens 的反馈功能图在可扩展性和意义性方面提供了宝贵的参考（见图 2）。

在讨论自然语言生成评估时，传统的评估指标如 ROUGE（Lin, 2004）和 BLEU（Papineni 等, 2002）被广泛使用，但往往与人工评估存在差异（Liu 等, 2023）。此外，中型语言模型（MLM）在传统评估指标上展示了优异的表现，但在许多领域中可以被大型语言模型（LLM）替代（X. Zhang & Gao, 2023）。最后，另一种著名的评估方法是对生成文本的人工评估，但这种方法在规模和成本上存在明显的缺点（Fabbri 等, 2021）。由于这些方法的缺点（见图 2），它们在本文中不再进一步讨论。

![](../Images/fb4848d5356e302c9d8324125089d93d.png)

图 2 — 反馈功能（反馈功能 — TruLens，o. J.）

关于近期趋势，评估指标随着大语言模型（LLMs）普及的增加而发展。其中一种发展是LLM评估，允许另一个LLM通过思维链（CoT）推理来评估生成的文本（Liu et al., 2023）。通过定制化的提示策略，可以强调并用数值打分聚焦于诸如真实性和完整性等方面（Kim et al., 2023）。对于这种方法，已有研究表明，较大的模型规模有利于摘要评估（Liu et al., 2023）。此外，这种评估还可以基于参考或收集的真实数据，比较生成的文本和参考文本（Wu et al., 2023）。对于没有唯一正确答案的开放性任务，基于LLM的评估在与人工质量判断的相关性方面优于基于参考的指标。此外，收集真实数据的成本可能很高。因此，基于参考或真实数据的指标不在本评估范围之内（Liu et al., 2023；*反馈函数 — TruLens*，o. J.）。

最后，介绍一个值得注意的近期进展，即**L**可学习**评**估**指标**用于文本**简化**（LENS），Maddela等人（2023）称其为“首个监督自动化的文本简化评估指标”，在最近的基准测试中展现了良好的结果。它以其在识别幻觉（Kew et al., 2023）方面的有效性而著称。在可扩展性和有效性方面，预计该方法由于成本较低，具有较高的可扩展性，但在意义性上略逊色于LLM评估，因此LENS接近于图2右上角的LLM评估。然而，仍需进一步评估以验证这些说法。至此，评估方法的讨论已结束，下一节将聚焦于这些方法的应用。

# 4\. 朝向更高的真实性

在第一节中已确立了RAG应用中真实性的重要性，并通过SRQ1探讨了不真实输出的原因，通过SRQ2进行了评估，本节将聚焦于SRQ3。因此，将详细描述改善真实性和完整性的具体推荐方法，以提高真实的回应。这些方法可分为两类：输出生成的改进和输出验证。

为了改进RAG应用的生成步骤，本文将重点介绍两种方法。这些方法在图3中得到了可视化，并且左侧参考了简化的RAG架构。第一种方法是微调生成LLM。模型大小上的指令调整对LLM的零-shot摘要能力至关重要。因此，最先进的LLM能够与自由职业者编写的摘要相媲美（T. Zhang等，2023）。第二种方法专注于元素感知摘要。通过CoT提示，如SumCoT中所展示的，LLM可以逐步生成摘要，强调源文本中的事实实体（Wang等，2023）。具体来说，在额外的一步中，相关文档中的事实元素被提取，并与摘要的上下文一起提供给LLM，见图3。两种方法在提高LLM生成摘要的基础性和完整性方面已显示出良好的效果。

![](../Images/8fc1964dfeef26680b2ff843985779af.png)

图 3 — 改进的生成步骤

在验证RAG输出时，LLM生成的摘要会根据其基础性和完整性进行评估。这可以通过CoT提示将LLM的基础性和完整性得分进行聚合来完成。图4中展示了一个CoT提示示例，您可以将其转发给更大模型的LLM以进行完成。此外，这一步可以通过使用监督指标（如LENS）来替代或推进。最后，生成的评估结果将与阈值进行比较。如果输出没有基础性或不完整，则可以对其进行修改、提升给用户，或可能被拒绝。

![](../Images/c340417353118f5143a2970195f46942.png)

图 4 — 输出验证

在将这些方法应用于RAG时，应考虑到运行时评估和生成模型的微调会导致额外的成本。此外，评估步骤会影响应用的回答速度。最后，由于输出被拒绝或提升了真实性问题而没有答案，可能会使应用用户感到困惑。因此，评估这些方法时需要根据应用领域、应用功能和用户期望进行综合考虑。这将导致一种定制化的方法，提升RAG应用输出的真实性。

除非另有注明，所有图片均由作者提供。

# 参考文献列表

Bang, Y., Cahyawijaya, S., Lee, N., Dai, W., Su, D., Wilie, B., Lovenia, H., Ji, Z., Yu, T., Chung, W., Do, Q. V., Xu, Y., & Fung, P. (2023). *A Multitask, Multilingual, Multimodal Evaluation of ChatGPT on Reasoning, Hallucination, and Interactivity* (arXiv:2302.04023). arXiv. [https://doi.org/10.48550/arXiv.2302.04023](https://doi.org/10.48550/arXiv.2302.04023)

Deutsch, D., & Roth, D. (2022). *基准测试基于问答的摘要评估度量的答案验证方法* (arXiv:2204.10206). arXiv. [https://doi.org/10.48550/arXiv.2204.10206](https://doi.org/10.48550/arXiv.2204.10206)

Fabbri, A. R., Kryściński, W., McCann, B., Xiong, C., Socher, R., & Radev, D. (2021). *SummEval: 重新评估摘要评估方法* (arXiv:2007.12626). arXiv. [https://doi.org/10.48550/arXiv.2007.12626](https://doi.org/10.48550/arXiv.2007.12626)

*反馈函数 — TruLens*。(o. J.)。于2024年2月11日访问，来自[https://www.trulens.org/trulens_eval/core_concepts_feedback_functions/#feedback-functions](https://www.trulens.org/trulens_eval/core_concepts_feedback_functions/#feedback-functions)

Ji, Z., Lee, N., Frieske, R., Yu, T., Su, D., Xu, Y., Ishii, E., Bang, Y., Dai, W., Madotto, A., & Fung, P. (2023). 关于自然语言生成中的幻觉问题的调查. *ACM计算机评论*, *55*(12), 1–38\. [https://doi.org/10.1145/3571730](https://doi.org/10.1145/3571730)

Kew, T., Chi, A., Vásquez-Rodríguez, L., Agrawal, S., Aumiller, D., Alva-Manchego, F., & Shardlow, M. (2023). *BLESS: 基准测试大规模语言模型在句子简化上的表现* (arXiv:2310.15773). arXiv. [https://doi.org/10.48550/arXiv.2310.15773](https://doi.org/10.48550/arXiv.2310.15773)

Kim, J., Park, S., Jeong, K., Lee, S., Han, S. H., Lee, J., & Kang, P. (2023). *哪种更好？探索基于LLM度量的提示策略* (arXiv:2311.03754). arXiv. [https://doi.org/10.48550/arXiv.2311.03754](https://doi.org/10.48550/arXiv.2311.03754)

Levonian, Z., Li, C., Zhu, W., Gade, A., Henkel, O., Postle, M.-E., & Xing, W. (2023). *增强检索生成以提高数学问答的表现：基础性与人类偏好之间的权衡* (arXiv:2310.03184). arXiv. [https://doi.org/10.48550/arXiv.2310.03184](https://doi.org/10.48550/arXiv.2310.03184)

Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., Küttler, H., Lewis, M., Yih, W., Rocktäschel, T., Riedel, S., & Kiela, D. (2021). *基于检索增强生成的知识密集型NLP任务* (arXiv:2005.11401). arXiv. [https://doi.org/10.48550/arXiv.2005.11401](https://doi.org/10.48550/arXiv.2005.11401)

Lin, C.-Y. (2004). ROUGE: 一种自动化摘要评估工具包. *文本摘要的扩展应用*, 74–81\. [https://aclanthology.org/W04-1013](https://aclanthology.org/W04-1013)

Liu, Y., Iter, D., Xu, Y., Wang, S., Xu, R., & Zhu, C. (2023). *G-Eval: 使用GPT-4的NLG评估与更好的人工对齐* (arXiv:2303.16634). arXiv. [https://doi.org/10.48550/arXiv.2303.16634](https://doi.org/10.48550/arXiv.2303.16634)

Maddela, M., Dou, Y., Heineman, D., & Xu, W. (2023). *LENS: 一种可学习的文本简化评估度量* (arXiv:2212.09739). arXiv. [https://doi.org/10.48550/arXiv.2212.09739](https://doi.org/10.48550/arXiv.2212.09739)

Papineni, K., Roukos, S., Ward, T., & Zhu, W.-J. (2002). Bleu：一种自动评估机器翻译的方法. 载于 P. Isabelle, E. Charniak, & D. Lin (编辑), *第40届计算语言学会年会论文集* (第311–318页). 计算语言学会. [https://doi.org/10.3115/1073083.1073135](https://doi.org/10.3115/1073083.1073135)

Wang, Y., Zhang, Z., & Wang, R. (2023). *具有元素感知的大型语言模型摘要：专家对齐评估与思维链方法* (arXiv:2305.13412). arXiv. [https://doi.org/10.48550/arXiv.2305.13412](https://doi.org/10.48550/arXiv.2305.13412)

Wu, N., Gong, M., Shou, L., Liang, S., & Jiang, D. (2023). *大型语言模型在摘要评估中的多样化角色* (arXiv:2303.15078). arXiv. [https://doi.org/10.48550/arXiv.2303.15078](https://doi.org/10.48550/arXiv.2303.15078)

Zhang, T., Ladhak, F., Durmus, E., Liang, P., McKeown, K., & Hashimoto, T. B. (2023). *基准测试大型语言模型在新闻摘要中的表现* (arXiv:2301.13848). arXiv. [https://doi.org/10.48550/arXiv.2301.13848](https://doi.org/10.48550/arXiv.2301.13848)

Zhang, X., & Gao, W. (2023). *基于大型语言模型的新闻声明事实验证：一种分层逐步提示方法* (arXiv:2310.00305). arXiv. [https://doi.org/10.48550/arXiv.2310.00305](https://doi.org/10.48550/arXiv.2310.00305)
