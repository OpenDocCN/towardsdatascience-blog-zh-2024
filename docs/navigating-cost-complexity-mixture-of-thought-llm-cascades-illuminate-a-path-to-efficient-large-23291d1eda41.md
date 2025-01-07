# 导航成本与复杂性：思维混合LLM级联揭示了高效部署大语言模型的路径

> 原文：[https://towardsdatascience.com/navigating-cost-complexity-mixture-of-thought-llm-cascades-illuminate-a-path-to-efficient-large-23291d1eda41?source=collection_archive---------5-----------------------#2024-03-06](https://towardsdatascience.com/navigating-cost-complexity-mixture-of-thought-llm-cascades-illuminate-a-path-to-efficient-large-23291d1eda41?source=collection_archive---------5-----------------------#2024-03-06)

[](https://medium.com/@yuval_domino?source=post_page---byline--23291d1eda41--------------------------------)[![Yuval Zukerman](../Images/b04d3068659ed79643398dc39f6ce950.png)](https://medium.com/@yuval_domino?source=post_page---byline--23291d1eda41--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--23291d1eda41--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--23291d1eda41--------------------------------) [Yuval Zukerman](https://medium.com/@yuval_domino?source=post_page---byline--23291d1eda41--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23291d1eda41--------------------------------) ·5分钟阅读·2024年3月6日

--

![](../Images/535098db554b3f659cfd241166a5b1b7.png)

图片来源：[Joshua Sortino](https://unsplash.com/@sortino) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果我告诉你，在不牺牲准确性的情况下，你可以节省60%或更多的LLM API开销，你会相信吗？令人惊讶的是，现在你可以。

大型语言模型（LLM）现在已经成为我们日常生活的一部分。公司利用这项技术来自动化流程、改善客户体验、开发更好的产品、节省开支等。

托管自己的LLM非常具有挑战性。它们提供了广泛的功能，但运行成本通常很高。它们通常需要复杂的基础设施和大量数据。成本和复杂性是你使用提示工程的原因。你甚至可能使用检索增强生成（RAG）来改善上下文并减少幻觉。通过这两种技术，你将LLM的运行工作卸载给像OpenAI、Cohere或Google这样的公司。然而，将LLM的应用扩展到新的使用场景，尤其是使用最新的强大模型，可能会产生一个之前没有预料到的新成本。较弱的模型可能更便宜，但你能信任它们回答复杂的问题吗？现在，新的研究展示了如何节省开支，并获得与强大模型相媲美，甚至更好的LLM结果。

**了解LLM级联**

在追求降低LLM成本的过程中，研究人员转向了LLM级联的概念。在黑暗时代，也就是ChatGPT推出之前，[谷歌和多伦多大学的一个团队定义了这个术语](https://arxiv.org/pdf/2207.10342.pdf)，指的是利用概率计算，通过多个LLM获得最佳结果的程序。

最近，[FrugalGPT论文](https://arxiv.org/abs/2305.05176)将级联定义为将用户查询依次发送给一系列LLM，从较弱到较强，直到答案足够好为止。FrugalGPT级联使用专门的模型来确定何时答案达到了质量阈值，足够好。

一篇来自乔治梅森大学、微软和弗吉尼亚理工大学的最新论文《[‘基于思维混合表示的低成本推理的大型语言模型级联’](https://arxiv.org/pdf/2310.03094.pdf)》提供了一种替代方案：一种可以判断答案是否足够好的方法，而无需微调另一个模型。

**思维混合LLM级联**

与使用多个LLM不同，‘思维混合’（MoT）推理只使用两个——GPT 3.5 Turbo和GPT 4。前者被认为是‘较弱’的LLM，而后者则是‘较强’的LLM。作者利用LLM的‘答案一致性’来判断LLM的回答是否足够好。当LLM对类似提示给出一致的回答时，说明它对答案有信心。因此，当较弱的LLM的回答一致时，就不需要调用较强的LLM。相反，当LLM缺乏信心时，它们会给出不一致的回答。这时，就需要更强的LLM来回答提示。（注：你也可以选择任何较弱/较强的LLM组合。）

这些提示本身使用少量示例上下文提示，以提高LLM回答质量。这样的提示通过提供类似问题和答案的示例来引导LLM的回应。

为了改进模型推理并简化一致性测量，研究人员通过‘混合’两种提示技术，提出了一种新的推理任务提示方法：

+   [思维链](https://openreview.net/forum?id=_VjQlMeSB_J)（CoT）提示鼓励LLM在给出最终答案之前生成中间步骤或推理过程。生成这些步骤有助于模型改善复杂任务的结果，同时提高答案的准确性。

+   [思维程序](https://arxiv.org/abs/2211.12588)（PoT）扩展了思维链提示，使用模型的输出作为进一步提示的新输入。使用这种技术的提示通常要求模型用代码回答问题，而非人类语言。

论文还介绍了两种确定答案一致性的方法：

+   投票：该方法通过类似的提示或改变响应温度选项，从LLM查询中采样多个答案。然后，衡量LLM的答案之间的相似度。与其他所有答案最为一致的答案被认为是正确的。团队还定义了一个灵活的“阈值”值，以平衡答案一致性与预算限制。

+   验证：该方法比较LLM在两种不同思维表示（例如CoT和PoT）下最一致的答案。如果两个提示的回答相同，则算法接受较弱LLM的答案。

由于投票方法需要多个提示，因此当有预算来指导阈值时，它可能更为适用。

**结论：思维混合帮助你节省成本**

让我们看看MoT技术节省了多少成本，以及它对答案准确性的影响。

研究人员使用以下公式来计算提示成本：

+   较弱模型的提示成本（因为我们可能会提示它多次）

+   答案评估过程的成本

+   如果评估过程拒绝答案，我们将添加提示强模型的成本。

结果非常显著：

+   使用MoT变体——将投票和验证与CoT和PoT结合——可以在仅使用GPT-4的40%成本下，达到相当的表现。

+   在与[CREPE](https://github.com/velocityCavalry/CREPE)问答数据集的测试中，MoT以仅47%的成本超越了GPT-4。

+   将PoT与CoT结合使用，相较于单独使用其中一种技术，能提高决策的准确性。

+   在使用投票方法时，即使提高阈值，额外的成本也未显著影响质量。

+   一致性模型在可靠地识别正确的LLM答案方面证明了其有效性。它成功地预测了何时应当使用强模型来获得最佳结果。

在内部托管和管理大型语言模型（LLMs）带来了显著的挑战。它们带来了复杂性、高成本，并且需要大量的基础设施和数据资源。因此，对于希望利用LLM广泛能力的组织来说，LLM呈现出实质性的障碍。这可能促使你转向托管的LLM。然而，这种方法在扩展到新的应用场景时，往往给公司带来预料之外的成本增加和预算挑战。当整合最新的强大模型时，这一点尤为明显。为了避免这种命运，你面临一个新的困境：你能信任较弱且更实惠的模型吗？你能克服它们在处理复杂问题时的准确性顾虑吗？

LLM级联与思维混合（MoT）提供了两项重要的进展：

1.  相较于仅使用最新模型，能够实现显著的成本节省。

1.  与最新模型相当的可验证结果。

这一突破为组织提供了一种实用且高效的方法，在LLM的强大能力与有效管理成本之间找到微妙的平衡。

Domino的工作人员软件工程师Subir Mansukhani为这篇文章做出了贡献。
