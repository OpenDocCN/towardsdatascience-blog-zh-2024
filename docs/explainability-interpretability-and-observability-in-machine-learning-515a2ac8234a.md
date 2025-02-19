# 机器学习中的可解释性、可解释性和可观察性

> 原文：[`towardsdatascience.com/explainability-interpretability-and-observability-in-machine-learning-515a2ac8234a?source=collection_archive---------4-----------------------#2024-06-30`](https://towardsdatascience.com/explainability-interpretability-and-observability-in-machine-learning-515a2ac8234a?source=collection_archive---------4-----------------------#2024-06-30)

## 这些术语通常用来描述模型的透明度，但它们*到底*是什么意思？

[](https://medium.com/@jasonyzhong06?source=post_page---byline--515a2ac8234a--------------------------------)![Jason Zhong](https://medium.com/@jasonyzhong06?source=post_page---byline--515a2ac8234a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--515a2ac8234a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--515a2ac8234a--------------------------------) [Jason Zhong](https://medium.com/@jasonyzhong06?source=post_page---byline--515a2ac8234a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--515a2ac8234a--------------------------------) ·6 分钟阅读·2024 年 6 月 30 日

--

![](img/de8a5cafa120b5d37952d505542009a8.png)

模型洞察。截图来自[Xplainable](https://www.xplainable.io)。

机器学习（ML）由于能够从大数据集中生成准确的预测和可操作的洞察，已经在各个行业中变得越来越普及。**全球有 34%的公司已部署机器学习，并报告在客户保持、收入增长和成本效率方面取得了显著改善** [(IBM, 2022)](https://www.ibm.com/downloads/cas/GVAGA3JP)。这一机器学习采用激增的原因在于模型变得更加易于访问，且能够以更高的准确性生成结果，在多个领域超越了传统的商业方法。

然而，随着机器学习模型变得越来越复杂，同时又被广泛依赖，透明度的需求变得日益重要。根据 IBM 的全球采用指数，**80%的企业认为能够确定模型如何得出决策是一个关键因素**。这在医疗保健和刑事司法等行业尤为重要，在这些领域，模型及其所做决策的信任与问责至关重要。透明度的缺乏可能是限制这些行业广泛使用机器学习的因素，可能会阻碍运营速度、决策过程和整体效率的显著提升。

三个关键术语——*可解释性、可理解性和可观察性*——被广泛认为构成了机器学习模型的透明度。

尽管这些概念很重要，研究人员仍未能为它们建立严格的定义和区分，这源于缺乏数学形式化以及无法通过特定指标来衡量它们[(Linardatos et al., 2020)](https://doi.org/10.3390/e23010018)。

# 可解释性

可解释性没有标准的定义，但通常被认为指的是**“针对人工智能透明度和信任问题所做的运动、倡议和努力”** [(Adadi & Berrada, 2018)](https://doi.org/10.1109/access.2018.2870052)。[Bibal 等人（2021）](https://doi.org/10.1007/s10506-020-09270-4)旨在制定法律要求的指导方针，得出的结论是，一个可解释的模型必须能够“(i) [提供] 用于做出决策的主要特征，(ii) [提供] 所有处理过的特征，(iii) [提供] 对决策的全面解释，(iv) [提供] 对整个模型的可理解表示”。他们将可解释性定义为提供“有关如何做出特定决策的有意义的见解”，这需要“一种思维过程，能够让决策对用户有意义（即让他能理解该决策）”。因此，可解释性指的是**理解支持决策的模型内部逻辑和机制**。

可解释性的历史例子是 AlphaGo（一个算法）与李世石（被认为是史上最优秀的围棋选手之一）之间的围棋对局。在第二局比赛中，AlphaGo 的第 19 手棋被专家和创作者广泛认为是“如此令人惊讶，[颠覆]了几百年的传统智慧”[(Coppey, 2018)](https://medium.com/point-nine-news/what-does-alphago-vs-8dadec65aaf)。这一着棋极为‘*非人类*’，但却是决定性的一着，最终使得该算法赢得了比赛。尽管人类后来能够确定这一着棋的动机，但他们无法解释为什么模型选择这一着棋，而不是其他着棋，缺乏对模型逻辑的内部理解。这展示了机器学习超越人类能力的非凡计算能力，但也提出了一个问题：**这足以让我们盲目信任它们的决策吗？**

> 虽然准确性是采用机器学习的关键因素，但在许多情况下，可解释性被认为比准确性更为重要。

医生们不愿意，也有充分理由不愿意接受一个如果无法提供决策背后内部逻辑的模型，即便该模型从长远来看对患者有益，特别是当模型输出结果为“不应去除癌症肿瘤”时。这是机器学习，尽管具有巨大潜力，未能在许多领域得到充分利用的主要限制因素之一。

# 可解释性

可解释性通常被认为与可解释性相似，并且常常可以互换使用。然而，普遍认为可解释性指的是基于输入理解整体决策的能力，而不需要完全理解模型是如何生成输出的。因此，可解释性被视为比可解释性更广泛的术语。[Doshi-Velez 和 Kim (2017)](https://arxiv.org/abs/1702.08608) 将可解释性定义为**“能够以人类可以理解的术语进行解释或呈现的能力”**。另一个流行的可解释性定义是“人类理解决策原因的程度”[(Miller, 2019)](https://doi.org/10.1016/j.artint.2018.07.007)。

在实际应用中，一个可解释的模型可能是能够通过可识别的模式和特征（例如毛发的存在）预测家庭宠物图像是动物的模型。然而，这个模型缺乏对内部逻辑或过程的理解，这使得该模型无法解释。

> 尽管许多研究人员在相同的语境中使用“可解释性”（interpretability）和“可解释性”（explainability）这两个词，但“可解释性”通常指的是对模型内部工作原理的更深入理解。

[Doshi-Velez 和 Kim (2017)](https://arxiv.org/abs/1702.08608) 提出了评估可解释性的三种方法。第一种方法是应用级评估。这包括通过将模型与领域专家进行任务对比评估，确保模型的有效性。一个例子是将 CT 扫描模型的性能与放射科医生使用相同数据的表现进行比较。第二种方法是人类级评估，要求外行评估解释的质量，例如选择他们认为更高质量的模型解释。最后一种方法是功能性基础评估，不需要人类参与。相反，模型是根据可解释性的某种正式定义进行评估的。这可能包括展示一个已经被证明是可解释的模型在预测准确性上的提高。假设是，如果预测准确性有所提高，那么可解释性就更高，因为模型已经通过基础合理的推理生成了正确的输出。

# 可观察性

机器学习可观测性是指了解机器学习模型在生产环境中的表现。Mahinda (2023)235) 将可观测性定义为“通过系统的输出测量和理解系统状态的一种手段”，并进一步指出它“是操作系统和基础设施的必要实践，其可靠性依赖于此”。**可观测性旨在解决一个潜在问题，即在研发中表现出色的模型可能在部署中不如预期。** 这种差异通常是由于模型遇到的实际数据与最初训练时使用的历史数据之间的差异所致。因此，持续监控输入数据和模型性能至关重要。在涉及高风险问题的行业中，确保模型按预期表现是采用的关键前提。

> 可观测性是保持模型在现实条件下性能的关键方面。

可观测性由两种主要方法组成，监控和可解释性 [(*机器学习模型可观测性指南*, n.d.)](https://encord.com/blog/model-observability-techniques/#:~:text=Standard%20machine%20learning%20observability%20involves)。

在部署过程中，可以使用多种指标来监控模型的性能，例如精确度、F1 得分和 AUC ROC。通常，当某个值达到时，系统会触发警报，从而促使对问题根源的及时调查。

可解释性是可观测性的一个重要组成部分。了解为什么一个模型在某个数据集上的表现不佳，对于能够改进模型，使其在未来相似情况下表现更优至关重要。如果无法理解形成决策的底层逻辑，就无法对模型进行改进。

# 结论

随着机器学习的进一步普及，模型透明度的重要性成为确保决策背后信任和问责的关键因素。

可解释性使用户能够理解机器学习模型的内部逻辑，增强对模型预测结果的信心。可解释性确保模型预测背后的理由能够被验证和证明。可观测性提供对模型性能的监控和洞察，帮助在生产环境中迅速而准确地发现操作问题。

尽管机器学习有巨大的潜力，但基于我们无法完全理解的模型决策进行操作所带来的风险不容忽视。因此，在机器学习系统的开发和集成中，必须优先考虑可解释性、可解释性和可观测性。

创建具有高预测准确性的透明模型一直是并将继续带来巨大的挑战。然而，这一追求将带来负责任且知情的决策，远远超越当前的模型。
