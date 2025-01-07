# 通用语——基于实体感知的机器翻译方法，用于知识图谱上的问答

> 原文：[https://towardsdatascience.com/lingua-franca-entity-aware-machine-translation-approach-for-question-answering-over-knowledge-e2c7e481c870?source=collection_archive---------12-----------------------#2024-01-29](https://towardsdatascience.com/lingua-franca-entity-aware-machine-translation-approach-for-question-answering-over-knowledge-e2c7e481c870?source=collection_archive---------12-----------------------#2024-01-29)

## 朝着知识图谱问答系统的通用语发展

[](https://perevalov.medium.com/?source=post_page---byline--e2c7e481c870--------------------------------)[![Aleksandr Perevalov](../Images/3b2512ac381b1dc2e72ae79d7e14285a.png)](https://perevalov.medium.com/?source=post_page---byline--e2c7e481c870--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2c7e481c870--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2c7e481c870--------------------------------) [Aleksandr Perevalov](https://perevalov.medium.com/?source=post_page---byline--e2c7e481c870--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2c7e481c870--------------------------------) ·阅读时间7分钟·2024年1月29日

--

# TLDR

机器翻译（MT）可以增强现有的问答（QA）系统，这些系统的语言能力有限，通过使其支持多种语言来提高其功能。然而，机器翻译有一个主要缺点——它通常在翻译那些不能逐字翻译的命名实体时失败。例如，电影《教皇必须死》的德语片名是“Ein Papst zum Küssen”，其字面翻译是：“一个待吻的教皇”。由于命名实体的正确性对问答系统至关重要，因此必须妥善处理这一挑战。在本文中，我们提出了一种名为“Lingua Franca”的实体感知机器翻译方法。它利用知识图谱中的信息来确保命名实体翻译的准确性。没错，它能有效工作！

# 挑战

实现高质量的翻译在很大程度上依赖于准确翻译句子中的命名实体（NE）。已经提出了各种方法来增强命名实体的翻译，包括集成知识图谱（KG）来改善实体翻译的方法，这些方法承认实体在整体翻译质量中的关键作用，特别是在问答系统的上下文中。值得注意的是，命名实体翻译的质量并不是一个孤立的目标；它对涉及信息检索（IR）或基于知识图谱的问答（KGQA）等任务的系统有更广泛的影响。本文将深入讨论机器翻译（MT）和基于知识图谱的问答（KGQA）。

KGQA 系统的重要性在于它们能够基于结构化数据为用户提供事实性答案（见下图）。

![](../Images/caae9e2754d21389606f63bb4c355ea6.png)

Google 直接回答功能的截图（由作者提供）

KGQA 系统是现代搜索引擎的核心组件，使其能够为用户提供直接答案（Google 搜索，截图由作者提供）。

此外，多语言 KGQA 系统在解决 Web 上的“数字语言鸿沟”中发挥着至关重要的作用。例如，涉及德国的 Wikipedia 文章，特别是与城市或人物相关的文章，其中包含的信息在德语中的比例高于其他语言——这种信息不平衡可以通过多语言 KGQA 系统加以处理，顺便说一句，该系统是所有现代搜索引擎的核心。

使 KGQA 系统能够用不同语言回答问题的选项之一是使用机器翻译（MT）。然而，现成的 MT 在翻译命名实体（NEs）时面临显著挑战，因为许多实体无法直接翻译，需要背景知识才能准确解读。例如，考虑电影《教皇必须死》的德语片名，“Ein Papst zum Küssen”。字面翻译为“一个待吻的教皇”，这突显了超越简单翻译方法的上下文理解需求。

鉴于传统机器翻译方法在翻译实体时的局限性，将 KGQA 系统与机器翻译相结合通常会导致命名实体失真，显著降低准确回答问题的可能性。因此，需要一种增强的方法，将多语言背景知识整合到命名实体中。

# 我们的方法

本文介绍并实现了 [一种新的命名实体感知机器翻译（NEAMT）方法](https://dl.acm.org/doi/abs/10.1145/3587259.3627567)，旨在增强 KGQA 系统的多语言能力。NEAMT 的核心概念是通过结合知识图谱（例如 [Wikidata](https://www.wikidata.org/) 和 [DBpedia](https://www.dbpedia.org/)）中的信息来提升机器翻译的质量。这是通过使用“实体替换”技术实现的。

作为评估数据，我们使用了 [QALD-9-plus](https://github.com/KGQA/qald_9_plus) 和 [QALD-10](https://github.com/KGQA/QALD-10) 数据集。然后，我们使用 NEAMT 框架中的多个组件，这些组件可在我们的 [仓库](https://github.com/dice-group/LFQA) 中获取。最后，该方法在两个 KGQA 系统上进行了评估：[QAnswer](https://qanswer.ai/) 和 [Qanary](https://github.com/WDAqua/Qanary)。该方法的详细描述请参见下图。

![](../Images/d81a519a5003f291a80df6099cf155ee.png)

KGQA 过程中的 Lingua Franca 方法概述（图由作者提供）

从本质上讲，我们的方法在翻译过程中使用实体替换技术保留已知的命名实体。随后，这些实体会被从知识图谱中提取出的相应标签替换为目标翻译语言中的标签。这个细致的过程确保了问题在KGQA系统回答之前的精确翻译。

根据[我们之前的文章](https://medium.com/towards-data-science/can-machine-translation-be-a-reasonable-alternative-for-multilingual-question-answering-systems-242c4674a29b)的见解，我们将英语指定为共同的目标翻译语言，从而将我们的方法命名为“Lingua Franca”（灵感来自于[“桥梁”或“联结”语言](https://en.wikipedia.org/wiki/Lingua_franca)的含义）。需要特别指出的是，我们的框架具有多功能性，可以无缝地适应任何其他语言作为目标语言。重要的是，Lingua Franca不仅限于KGQA的范畴，还可以应用于各种面向实体的搜索应用。

Lingua Franca方法包括三个主要步骤：（1）命名实体识别（NER）和命名实体链接（NEL），（2）基于已识别命名实体应用实体替换技术，（3）利用机器翻译工具生成目标语言文本，同时考虑前述步骤中的信息。在这里，英语始终作为目标语言，与相关研究一致，认为这是问答（QA）质量的最优策略。然而，该方法不限于英语，如有需要，可以使用其他语言。

该方法作为开源框架实现，允许用户通过集成自定义的NER、NEL和MT组件来构建自己的命名实体感知机器翻译（NEAMT）管道（请参见我们的[GitHub](https://github.com/dice-group/LFQA)）。Lingua Franca方法在所有设置下的详细信息在下面的示例中进行了说明，如下图所示。

![](../Images/c85f6e934a700faeaabeec33c9989fb5.png)

Lingua Franca方法在多个设置下的详细表示（作者提供的图示）

本研究的实验结果强烈支持Lingua Franca在与KGQA系统结合时优于标准机器翻译工具的观点。

# 实验结果

在评估每个实体替换设置时，计算了通过机器翻译工具处理后占位符或命名实体标签的损坏率。该比率作为该方法相关管道中实际命名实体翻译质量的指标。更新后的统计数据如下：

+   设置1（类似字符串的占位符）：6.63%的占位符丢失或损坏。

+   设置2（数值占位符）：2.89%的占位符丢失或损坏。

+   设置3（在翻译前用英文标签替换命名实体）：6.16%的标签被损坏。

因此，通过我们的方法，我们可以自信地断言，在文本中识别的NEs中，最多有97.11%（**设置2**）被正确翻译。

我们分析了关于QA质量的结果，同时考虑了以下实验组件：一种方法管道或标准MT工具、源语言和KGQA基准。下图展示了方法与标准MT的对比——这些结果可以被解释为一种消融研究。

![](../Images/abbd8a04dc5e07a2024561c24cf57c03.png)

我们实验的宏观F1得分分组柱状图（按作者）

分组柱状图展示了关于每种语言和拆分的[宏观F1得分](https://en.wikipedia.org/wiki/F-score#:~:text=geometric%20mean.%5B26%5D-,Extension%20to%20multi%2Dclass%20classification,-%5Bedit%5D)（使用[Gerbil-QA](https://gerbil-qa.aksw.org/gerbil/)获得的）。在消融研究的背景下，每组包含两根柱状条：第一根柱条属于我们提出的最佳方法，而第二根柱条反映了标准MT工具（基准）的表现。

我们观察到，在大多数实验案例中（24个中的19个），使用我们方法的KGQA系统的表现超过了使用标准MT工具的KGQA系统。为了验证上述结论，我们对相同数据进行了Wilcoxon符号秩检验。基于检验结果（p值 = 0.0008，α = 0.01），我们拒绝了零假设，该假设表示QA质量结果没有差异，即将KGQA与标准MT结合与将KGQA与我们的方法结合没有区别。因此，我们得出结论，依赖我们NEAMT框架的方法显著提高了在回答多语言问题时的QA质量，相比标准MT工具。

实验的可重复性通过重复实验并计算所有QA质量指标之间的Pearson相关系数来确保。结果的系数为0.794，表示强相关与非常强相关之间的边界值。因此，我们假设我们的实验是可重复的。

# 结论

本文介绍了一种名为[Lingua Franca](https://dl.acm.org/doi/abs/10.1145/3587259.3627567)的NEAMT方法。Lingua Franca旨在增强多语言能力，并与标准机器翻译工具相比提高问答质量，它专为KGQA系统设计，目的是扩大其潜在用户范围。Lingua Franca的实现和评估采用了作者开发的模块化NEAMT框架，详细信息可参见“实验”部分。本文的主要贡献包括：（1）据我们所知，首次将NEAMT方法（即Lingua Franca）与KGQA结合；（2）提出了一个开源模块化NEAMT框架，使研究社区能够构建自己的机器翻译管道；（3）进行了一项全面的评估和消融研究，展示了Lingua Franca方法的有效性。

在未来的工作中，我们计划扩展实验设置，涵盖更广泛的语言、基准和KGQA系统。为了处理实体替换过程中的损坏占位符，我们计划使用这些数据对机器翻译模型进行微调。此外，我们还将进行更详细的错误分析，重点分析错误传播。

请不要忘记查看我们的[完整研究论文](https://dl.acm.org/doi/abs/10.1145/3587259.3627567)以及[GitHub 仓库](https://github.com/dice-group/LFQA)。

# 致谢

本研究得到了德国联邦教育与研究部（BMBF）资助，资助编号为01IS17046和01QE2056C，同时也得到了德国北莱茵-威斯特法伦州文化与科学部（MKW NRW）资助，资助编号为NW21–059D。本研究还在QA4CB研究项目内获得资助，该项目旨在开发用于扩展聊天机器人框架的问答组件。
