# 使用LLM作为评判者评估SQL生成

> 原文：[https://towardsdatascience.com/evaluating-sql-generation-with-llm-as-a-judge-1ff69a70e7cf?source=collection_archive---------4-----------------------#2024-07-31](https://towardsdatascience.com/evaluating-sql-generation-with-llm-as-a-judge-1ff69a70e7cf?source=collection_archive---------4-----------------------#2024-07-31)

![](../Images/9476fd2135a3132b8b6ef277b53fe2a6.png)

图片由作者使用Dall-E创作

## 结果指向了一种有前景的方法

[](https://aparnadhinak.medium.com/?source=post_page---byline--1ff69a70e7cf--------------------------------)[![Aparna Dhinakaran](../Images/e431ee69563ecb27c86f3428ba53574c.png)](https://aparnadhinak.medium.com/?source=post_page---byline--1ff69a70e7cf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ff69a70e7cf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1ff69a70e7cf--------------------------------) [Aparna Dhinakaran](https://aparnadhinak.medium.com/?source=post_page---byline--1ff69a70e7cf--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ff69a70e7cf--------------------------------) ·4分钟阅读·2024年7月31日

--

*特别感谢Manas Singh和Evan Jolley与我们共同合作进行这项研究！*

一种引起关注和投资的LLM潜在应用是其生成SQL查询的能力。使用自然语言查询大型数据库解锁了几个有吸引力的用例，从提高数据透明度到改善非技术用户的可访问性。

然而，和所有AI生成内容一样，评估问题非常重要。我们如何判断LLM生成的SQL查询是否正确并产生预期结果？我们最近的研究深入探讨了这个问题，并探索了使用[LLM作为评判者](https://docs.arize.com/phoenix/evaluation/concepts-evals/llm-as-a-judge)来评估SQL生成的有效性。

# 研究发现总结

在这项实验中，LLM作为评判者在评估SQL生成方面表现出初步的前景，使用OpenAI的GPT-4 Turbo，F1得分在0.70到0.76之间。在评估提示中加入相关的模式信息可以显著减少假阳性。尽管仍然存在挑战——包括由于错误的模式解释或对数据的假设导致的假阴性——LLM作为评判者为AI SQL生成性能提供了一个可靠的代理，尤其是在快速检查结果时。

# 方法论与结果

本研究建立在 Defog.ai 团队之前的工作基础上，该团队[开发了一种方法](https://github.com/defog-ai/sql-eval)来使用黄金数据集和查询评估 SQL 查询。该过程涉及使用黄金数据集问题生成 AI SQL，使用 AI 生成的 SQL 生成测试结果“x”，然后使用预先存在的黄金查询在相同数据集上生成结果“y”，最后比较结果“x”和“y”以判断准确性。

![](../Images/0321f9684b937ee0c804967416443634.png)

图表由作者提供

在这个比较中，我们首先探讨了传统的 SQL 评估方法，例如精确的数据匹配。该方法涉及直接比较两个查询的输出数据。例如，在评估关于作者引用的查询时，如果作者人数或引用次数有任何差异，就会导致不匹配并失败。虽然这种方法简单直接，但它无法处理边界情况，如如何处理零计数的桶或数值输出的轻微变化。

![](../Images/128cb4d97ec04ebbac643c34ee6044ee.png)

图表由作者提供

我们随后尝试了一种更细致的方法：使用 LLM 作为评判者。我们使用 OpenAI 的 GPT-4 Turbo 进行的初步测试，在不包含数据库模式信息的评估提示中，取得了令人鼓舞的结果，F1 分数介于 0.70 和 0.76 之间。在这种设置下，LLM 通过仅检查问题和生成的查询来判断生成的 SQL。

![](../Images/40b5b95a1c7b9ce81ea8e9c3bdf0be65.png)

结果：图片由作者提供

在本次测试中，我们注意到有相当多的假阳性和假阴性，其中许多与对数据库模式的误解或假设有关。在这个假阴性案例中，LLM 假设响应的单位与预期不同（学期与天数）。

![](../Images/9e03dd586b3b2a1be3425f269d1f9caf.png)

图片由作者提供

这些差异促使我们在评估提示中添加了数据库模式。与我们的预期相反，这导致了性能下降。然而，当我们精细化方法，只包含查询中引用的表的模式时，我们在假阳性和假阴性率方面看到了显著的改善。

![](../Images/73f63923309c1d1977eb4a238041a0de.png)

结果：图片由作者提供

# 挑战与未来方向

尽管使用 LLM 来评估 SQL 生成的潜力显而易见，但仍然存在挑战。通常，LLM 会对数据结构和关系做出错误的假设，或者错误地假设度量单位或数据格式。找到适当的模式信息类型和数量来包含在评估提示中，对于优化性能至关重要。

任何探索 SQL 生成用例的人可能会探讨其他几个领域，比如优化模式信息的包含、提高大型语言模型（LLM）对数据库概念的理解，以及开发结合 LLM 判断与传统技术的混合评估方法。

# 结论

由于能够捕捉到细微的错误，LLM作为评判者展现出作为快速有效工具的潜力，能够评估AI生成的SQL查询。

仔细选择提供给LLM评判者的信息，有助于最大化这一方法的效益；通过包含相关的架构细节并持续优化[LLM评估过程](https://arize.com/blog-course/llm-evaluation-the-definitive-guide/)，我们可以提高SQL生成评估的准确性和可靠性。

随着自然语言接口在数据库中的普及，对有效评估方法的需求也将不断增长。尽管LLM作为评判者的方法并不完美，但它比简单的数据匹配提供了更为细致的评估，能够理解上下文和意图，这一点是传统方法无法做到的。

有问题吗？欢迎随时在这里或通过[*LinkedIn*](https://www.linkedin.com/in/aparnadhinakaran/)或[*Slack*](https://join.slack.com/t/arize-ai/shared_invite/zt-26zg4u3lw-OjUNoLvKQ2Yv53EfvxW6Kg)与我联系。
