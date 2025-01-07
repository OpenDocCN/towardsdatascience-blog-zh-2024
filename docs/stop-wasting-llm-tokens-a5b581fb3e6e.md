# 停止浪费LLM令牌

> 原文：[https://towardsdatascience.com/stop-wasting-llm-tokens-a5b581fb3e6e?source=collection_archive---------5-----------------------#2024-08-07](https://towardsdatascience.com/stop-wasting-llm-tokens-a5b581fb3e6e?source=collection_archive---------5-----------------------#2024-08-07)

## 将输入批量处理可以在不影响性能的情况下带来显著的节省

[](https://medium.com/@toschnab?source=post_page---byline--a5b581fb3e6e--------------------------------)[![Tobias Schnabel](../Images/92a6c1addc602dae8e8d54fec5116385.png)](https://medium.com/@toschnab?source=post_page---byline--a5b581fb3e6e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a5b581fb3e6e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a5b581fb3e6e--------------------------------) [Tobias Schnabel](https://medium.com/@toschnab?source=post_page---byline--a5b581fb3e6e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a5b581fb3e6e--------------------------------) ·阅读时间：5分钟·2024年8月7日

--

![](../Images/ef7ee72d335ca3384877e59bbc877f89.png)

图片由[Orgalux](https://unsplash.com/@orgalux?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你使用LLM来标注或处理更大的数据集，很可能你并没有意识到自己浪费了大量的输入令牌。当你反复调用LLM处理文本片段或整个文档时，你的任务指令和静态的少样本示例会在*每个*输入示例中重复出现。就像将盘子整齐地堆叠能节省空间一样，将输入批量处理可以带来显著的节省。

假设你想要标注一个较小的文档库，包含1000个单页文档，并且每个文档的指令和少样本示例约为半页长。分别标注每个文档将花费大约100万个输入令牌。然而，如果你在同一次调用中标注十个文档，你将节省大约**30万个****输入令牌**（即节省30%），因为我们不需要重复指令！正如下面的示例所示，这通常在性能损失最小（甚至可能获得性能提升）的情况下发生，特别是当你同时优化提示时。

# 通过小批量处理来节省令牌

在下面的图中，我假设我们平均文档长度为*D*个令牌，指令和少样本示例的长度为*r*D*个令牌。我在前一段中提到的示例场景，即指令长度为文档的一半（*r* = 0.5），出现在下方的蓝色曲线中。对于更长的共享指令，我们的节省可能会更高：

![](../Images/64d532489c7951a7ac87012d7fc726cf.png)

主要的收获是：

+   即使是相对简短的指令（蓝线），小批量处理依然具有价值

+   并不一定需要使用非常大的小批量大小。大多数节省可以通过适中的小批量大小（*B* ≤ 10）获得。

# 实际操作中的小批量

让我们实际操作一个任务，我们希望对文本片段进行分类，以便进行进一步分析。我们将使用来自[自然指令基准](https://instructions.apps.allenai.org/)的一个有趣任务，在该任务中，我们需要将辩论中的句子标注为四个类别之一（价值、事实、证词或政策）。

看一个示例，我们看到我们首先获取当前主题作为上下文，然后需要对相关句子进行分类。

```py
{
  "input": {
    "topic": "the fight for justice,equality,peaceand love is futile",
    "sentence": "What matters is what I am personally doing to ensure that I am filling the cup!"
  },
  "output": "Value"
}
```

我们尚未回答的一个问题是：

## **我们如何选择正确的小批量大小？**

[之前的工作](https://arxiv.org/pdf/2301.08721.pdf)表明，最佳的小批量大小取决于任务和模型。我们实际上有两个选择：

1.  我们选择一个合理的小批量大小，比如5，并希望我们不会看到任何下降。

1.  我们优化小批量大小以及其他选择，例如少量示例的数量。

正如你可能已经猜到的那样，我们将在这里选择第二种方案。为了运行我们的实验，我们将使用开源框架[SAMMO](https://github.com/microsoft/sammo)，这是一个用于LLM调用和提示优化的框架。

在SAMMO中，提示被编码为提示程序（这些程序只是嵌套的Python类，接收输入数据后被调用）。我们将任务分成三个部分，并以JSON格式组织小批量。

```py
def prompt_program(fewshot_data, n_fewshot_examples=5, minibatch_size=1):
    return Output(
        MetaPrompt(
            [
                Section("Instructions", task["Definition"]),
                Section(
                    "Examples",
                    FewshotExamples(
                        fewshot_data, n_fewshot_examples
                    ),
                ),
                Section("Output in same format as above", InputData()),
            ],
            data_formatter=JSONDataFormatter(),
            render_as="markdown",
        ).with_extractor(on_error="empty_result"),
        minibatch_size=minibatch_size,
        on_error="empty_result",
    )
```

如果不使用小批量，并且使用五个少量示例，我们得到的**准确率为0.76**，并且需要支付**58255个输入令牌**。

现在，让我们探索小批量如何影响成本和性能。由于小批量减少了总输入成本，我们现在可以使用这些节省的成本来添加更多的少量示例！我们可以通过在SAMMO中设置搜索空间来研究这些权衡：

```py
def search_space(fewshot_data):
    minibatch_size = search_op.one_of([1, 5, 10], name="minibatch_size")
    n_fewshot_examples = search_op.one_of([5, 20], name="n_fewshot")

    return prompt_program(fewshot_data, n_fewshot_examples, minibatch_size)
```

运行这个实验向我们展示了完整的权衡：

```py
 setting                                  objective    costs                              parse_errors
  ---------------------------------------  -----------  ---------------------------------  --------------
* {'minibatch_size': 1, 'n_fewshot': 5}    0.76         {'input': 58255, 'output': 5817}   0.0
  {'minibatch_size': 1, 'n_fewshot': 20}   0.76         {'input': 133355, 'output': 6234}  0.0
  {'minibatch_size': 5, 'n_fewshot': 5}    0.75         {'input': 15297, 'output': 5695}   0.0
  {'minibatch_size': 5, 'n_fewshot': 20}   0.77         {'input': 30317, 'output': 5524}   0.0
  {'minibatch_size': 10, 'n_fewshot': 5}   0.73         {'input': 9928, 'output': 5633}    0.0
* {'minibatch_size': 10, 'n_fewshot': 20}  0.77         {'input': 17438, 'output': 5432}   0.0
```

因此，即使使用20个少量示例，我们仍然节省了近**70%的输入成本**（[58255–17438]/58255），并且**保持了整体准确性！**作为练习，你可以实现自己的目标，自动考虑成本，或在搜索空间中包括不同的数据格式化方式。

# 注意事项

所有这些背后的隐含前提是：（i）我们有足够的输入示例使用共享的指令，（ii）我们在延迟方面有一定的灵活性。第一个假设在许多标注场景中是成立的，但显然在一次性查询中不成立。在标注或其他离线处理任务中，延迟并不是特别关键，因为吞吐量最为重要。然而，如果你的任务是尽可能快地给用户提供答案，那么发出*B*个并行调用可能比一次调用*B*个输入示例更有意义。

# 结论

正如这个快速且实用的示例所示，同时对LLM进行多个输入提示可以在更好或相当的准确度下大大降低成本。好消息是，即使在适中的小批量大小下（例如5或10），节省的成本也可能相当可观。借助SAMMO，你可以自动查看在不同选择下性能的表现，从而做出最佳选择。

一个开放的研究问题是如何将此与检索增强生成（RAG）结合——可以通过对所有检索的示例取并集，或以某种方式对其进行排名。SAMMO让你在构建提示时探索这些策略中的一些，同时还有很多其他选择，例如如何格式化输入数据。如果你希望查看更多关于这个话题或其他任何内容，请留下评论。

*免责声明：**我是SAMMO的作者，这是一个开源的MIT许可证框架，用于提示优化。*

## 资源

+   **此示例的代码：** [Notebook文件](https://github.com/microsoft/sammo/blob/main/examples/blog/stop_wasting_tokens.ipynb) 或 [在MyBinder上实时运行](https://mybinder.org/v2/gh/microsoft/sammo/main?urlpath=tree%2Fexamples%2Fblog%2Fstop_wasting_tokens.ipynb)

+   **阅读：** [SAMMO用户指南](https://microsoft.github.io/sammo/) 和 [arXiv上的论文](https://arxiv.org/abs/2404.02319)，其中有更多细节
