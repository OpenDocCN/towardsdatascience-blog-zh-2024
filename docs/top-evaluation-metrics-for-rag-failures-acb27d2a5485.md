# RAG失败的顶级评估指标

> 原文：[https://towardsdatascience.com/top-evaluation-metrics-for-rag-failures-acb27d2a5485?source=collection_archive---------3-----------------------#2024-02-02](https://towardsdatascience.com/top-evaluation-metrics-for-rag-failures-acb27d2a5485?source=collection_archive---------3-----------------------#2024-02-02)

![](../Images/303836d4eb6b35c4a200aa1fd4ef5171.png)

图像由作者使用Dall-E 3创建

## 使用检索和响应指标排查LLM和检索增强生成问题

[](https://medium.com/@amber.roberts?source=post_page---byline--acb27d2a5485--------------------------------)[![Amber Roberts](../Images/ee686891eeedca7f33a63e147cc2c086.png)](https://medium.com/@amber.roberts?source=post_page---byline--acb27d2a5485--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--acb27d2a5485--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--acb27d2a5485--------------------------------) [Amber Roberts](https://medium.com/@amber.roberts?source=post_page---byline--acb27d2a5485--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--acb27d2a5485--------------------------------) ·7分钟阅读·2024年2月2日

--

![](../Images/be678bd9a80b7f2b544ca337c3da88a6.png)

图1：LLM RAG应用的根本原因工作流程（图表由作者创建）

如果你一直在使用大语言模型（LLM）进行搜索和检索任务，你很可能遇到过检索增强生成（RAG）这一技术，用于向LLM生成的响应中添加相关的上下文信息。通过将LLM与私有数据连接，RAG可以通过在上下文窗口中提供相关数据来增强响应效果。

RAG已被证明在复杂查询解答、知识密集型任务以及提高AI模型响应的精度和相关性方面非常有效，特别是在单独的训练数据可能不足的情况下。

然而，只有在持续监控LLM系统的常见故障点——特别是响应和检索评估指标时，才能获得RAG的这些好处。在本文中，我们将介绍用于排查检索和响应指标不佳的最佳工作流程。

# 排查检索和响应问题

值得记住的是，**RAG在所需信息易于获取时效果最佳**。相关文档的可用性将RAG系统评估集中在两个关键方面：

+   ***检索评估：*** 评估检索到的文档的准确性和相关性

+   ***响应评估：*** 衡量在提供上下文时，系统生成的响应的适当性

![](../Images/0a43ebe03eca8b3e0fdb478c80064940.png)

图2：LLM应用中的响应评估与检索评估（图像由作者提供）

## 表1：响应评估指标

![](../Images/c69acf88763ec390f93559601d082b09.png)

表1 由作者提供

## 表2：检索评估指标

![](../Images/907ac76c31da0365dbf548f1907aa30e.png)

表2 由作者提供

# 排除RAG工作流程中的问题

让我们回顾三种潜在场景，以根据流程图排除LLM性能不佳的问题。

## 场景1：良好响应，良好检索

![](../Images/28c5088db40d603793b39beac7b19aa0.png)

图示 由作者提供

在这种情况下，LLM应用中的一切都按预期工作，我们得到了一个良好的响应和有效的检索。我们发现我们的响应评估是“正确的”，并且我们的“命中 = 真”。命中是一个二元度量，“真”意味着相关文档已被检索，而“假”则意味着未检索到相关文档。请注意，命中的汇总统计量是命中率（具有相关上下文的查询百分比）。

对于我们的响应评估，正确性是一个评估标准，可以通过简单地结合**输入**（查询）、**输出**（响应）和**上下文**来完成，具体如*表1*所示。多个评估标准不需要用户标注的真实标签，因为大型语言模型（LLM）也可以使用像[OpenAI函数调用](https://arize.com/blog/calling-all-functions-benchmarking-openai-function-calling-and-explanations/)这样的工具来生成标签、分数和解释，下面是一个示例提示模板。

![](../Images/1776a24f280136742b49ffb25f859d1e.png)

图像 由作者提供

这些[LLM评估](https://arize.com/blog-course/llm-evaluation-the-definitive-guide/)可以格式化为数值型、类别型（二元和多类）和多输出型（多个分数或标签）——其中类别型-二元是最常用的，数值型则是最不常用的。

## 场景2：错误响应，错误检索

![](../Images/478d15be23dedf0f5031c07c5a2edf9d.png)

图示 由作者提供

在这种情况下，我们发现响应是错误的，且相关内容未被接收。根据查询，我们看到内容没有接收，因为查询没有解决方案。无论提供什么文档，LLM都无法预测未来的购买。然而，LLM可以生成一个比幻觉答案更好的响应。在这里，我们可以通过简单地在LLM提示模板中添加一行“*如果未提供相关内容且未找到结论性解决方案，请回答答案未知*”来进行实验。在某些情况下，正确的答案就是答案不存在。

![](../Images/124665fbc597674323ef4459f78ff639.png)

图示 由作者提供

## 场景3：错误响应，混合检索指标

在第三种场景中，我们看到一个错误的响应和混合检索指标（相关文档已被检索，但LLM由于接收到过多信息而产生了幻觉）。

![](../Images/09c89963a3710d41d9c93d128d5c8899.png)

图表由作者提供

要评估LLM RAG系统，您需要既能获取正确的上下文，又能生成合适的答案。通常，开发人员会嵌入用户查询并用它来搜索向量数据库中的相关片段（见图3）。检索性能不仅依赖于返回的片段与查询在语义上的相似性，还依赖于这些片段是否提供足够的相关信息来生成正确的查询响应。现在，您需要配置RAG系统的参数（检索类型、片段大小和K值）。

![](../Images/a37b959baffef3f83e5a1bd9958b4b25.png)

图3：RAG框架（作者提供）

类似于我们上一个场景，我们可以尝试编辑提示模板或更换用于生成响应的LLM。由于在文档检索过程中已检索到相关内容，但LLM没有将其显示出来，这可能是一个快速的解决方案。下面是运行经过修订的提示模板（在调整提示变量、LLM参数和提示模板本身后）生成的正确响应示例。

![](../Images/52689ef6524a0041ad99e1a1373f7907.png)

图表由作者提供

在排查混合性能指标的错误响应时，我们首先需要弄清楚哪些检索指标表现不佳。实现这一点最简单的方法是设置阈值和监控器。一旦您收到某个性能不佳的指标提醒，就可以通过特定的工作流程来解决问题。以nDCG为例，nDCG用于衡量排名靠前文档的有效性，并考虑相关文档的位置。因此，如果您检索到相关文档（命中 = ‘True’），则需要考虑实现重新排序技术，将相关文档靠近排名靠前的搜索结果。

在我们当前的场景中，我们检索到了一份相关文档（命中 = ‘True’），且该文档位于第一位，因此让我们尝试提升精确度（相关文档的百分比），直到检索到‘K’份文档。目前我们的Precision@4是25%，但如果只使用前两份相关文档，则Precision@2 = 50%，因为一半的文档是相关的。这个变化导致了LLM的正确响应，因为它获取到的信息较少，但比例上却更相关。

![](../Images/df1e002e326d5b7f14e8881ce5e0f6cf.png)

图表由作者提供

本质上，我们在这里看到的是RAG中一个常见的问题，称为[中途迷失](https://arxiv.org/abs/2307.03172)，当你的LLM被过多不总是相关的信息压倒时，便无法给出最优的答案。从我们的图示中可以看出，调整分块大小是许多团队在改善RAG应用时首先做的事情，但这并不总是直观的。面对上下文溢出和中途迷失的问题，更多的文档并不总是更好，重排序也不一定能提高性能。为了评估哪种分块大小最有效，你需要定义一个评估基准，并对不同的分块大小和top-k值进行一遍扫查。除了实验不同的分块策略外，测试不同的文本提取技术和嵌入方法也会提升整体RAG性能。

# 响应和检索评估指标总结

[这篇文章](https://arize.com/blog-course/rag-evaluation/)中的响应和检索评估指标与方法提供了一种全面的方式来查看LLM RAG系统的表现，指导开发者和用户理解其优点和局限性。通过不断根据这些指标评估这些系统，可以改进RAG的能力，提供准确、相关和及时的信息。

改进RAG的其他高级方法包括[重排序](https://blog.llamaindex.ai/a-cheat-sheet-and-some-recipes-for-building-advanced-rag-803a9d94c41b)、元数据附件、测试不同的嵌入模型、测试不同的索引方法、实施[HyDE](https://arize.com/blog/hyde-paper-reading-and-discussion/)、实施关键词搜索方法，或实施Cohere文档模式（类似于HyDE）。请注意，虽然这些更高级的方法——如分块、文本提取、嵌入模型实验——可能会产生更具上下文一致性的块，但这些方法会更消耗资源。将RAG与高级方法结合使用可以提升LLM系统的性能，并且只要检索和响应指标得到适当监控和维护，性能提升将会持续。

*有问题吗？请通过这里或者在* [*LinkedIn*](https://www.linkedin.com/in/amber-roberts42/)*、* [*X*](https://twitter.com/astronomeramber)*，或* [*Slack*](https://join.slack.com/t/arize-ai/shared_invite/zt-26zg4u3lw-OjUNoLvKQ2Yv53EfvxW6Kg)*与我联系！*
