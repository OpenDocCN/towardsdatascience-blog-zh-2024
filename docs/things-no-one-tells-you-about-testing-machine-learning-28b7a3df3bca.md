# **关于机器学习测试，没人告诉你的事**

> 原文：[`towardsdatascience.com/things-no-one-tells-you-about-testing-machine-learning-28b7a3df3bca?source=collection_archive---------6-----------------------#2024-01-04`](https://towardsdatascience.com/things-no-one-tells-you-about-testing-machine-learning-28b7a3df3bca?source=collection_archive---------6-----------------------#2024-01-04)

## 如何避免灾难

[](https://medium.com/@ryan.feather?source=post_page---byline--28b7a3df3bca--------------------------------)![Ryan Feather](https://medium.com/@ryan.feather?source=post_page---byline--28b7a3df3bca--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--28b7a3df3bca--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28b7a3df3bca--------------------------------) [Ryan Feather](https://medium.com/@ryan.feather?source=post_page---byline--28b7a3df3bca--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28b7a3df3bca--------------------------------) ·7 分钟阅读·2024 年 1 月 4 日

--

你已经准备好将你精心设计、专业调优和准确训练的算法部署到那个叫做“生产”的最终前沿。你已经收集了高质量的测试集，并对算法的表现感到高兴。是时候发布它并认为今天的工作圆满完成了！但不要太急。

没有人愿意在事后被叫去修复一个令人尴尬的机器学习应用。如果你的客户已经指出有问题，那就意味着你失去了信任。现在是时候疯狂地尝试调试并修复一个可能涉及数十亿自动学习参数的系统了。

![](img/9267516a16e656b2cc2a69feebc53c5f.png)

图片由作者使用 Stable Diffusion 创建

如果这听起来有些抽象或不太可能，以下是我自己职业生涯中的一些例子，这些模型在“测试”集上表现良好，但在实际应用中却有不同的结果：

+   一个用于预测建筑能效审计节能的模型对于大多数测试建筑来说很准确。然而，在实际数据中，它预测一个单独的场所节省的能源比整个州消耗的还要多。用户显然更关注那些异常值，而不是正确的预测。

+   一个用于理解建筑中哪个设备驱动能耗的模型，当上游系统用零填充缺失的家庭区域数据而不是预期的空值时，突然给出了异常的结果。这导致了一个持续数周、跨多个团队的努力，以修复问题并重新生成结果。

当然，你知道，尽管真正的价值在于机器学习，你已经构建了软件，并且所有的常规规则都适用。你需要单元测试、对集成点的审查以及监控，以捕捉真实系统中出现的众多问题。但是，如何有效地做到这一点呢？随着你改进模型，输出预计会发生变化，而你训练时的假设则会受到变化世界的影响。

# **（大致）围绕模型进行单元测试**

```py
# Not a great use of test code
def test_predict(model_instance, features):
    prediction = model_instance.predict(features)
    assert prediction == 0.133713371337
```

很显然，上述测试非常脆弱，无法捕捉到许多潜在问题。它仅仅测试我们的模型是否生成了最初训练时预期的结果。不同的本地和生产环境中的软件和硬件栈使得在推进部署的过程中，它容易出现问题。随着模型的演化，这会带来比其价值更多的维护工作。你预测管道的大部分复杂性都来自于数据收集、预处理、清洗、特征工程以及将预测结果包装成有用的输出格式。在这方面，更好的测试可以让任务变得更加轻松。

下面是你应该做的事情。

+   **彻底地单元测试你的特征生成和后处理。** 你的特征工程应该是确定性的，可能涉及多个数据源的处理以及一些非平凡的变换。这是进行单元测试的一个绝佳机会。

+   **单元测试所有的数据清洗和边界检查。** 你确实有一些代码专门用于清理输入数据并确保它与训练时的数据一致，对吧？如果没有，请继续阅读。一旦你有了，测试这些检查。确保所有的断言、裁剪和替代都能让你的预测安全，免受真实数据世界的挑战。确保在应该发生时抛出异常。

+   **额外加分：使用近似断言。** 如果你还没有意识到，有一些简单的方法可以避免由于对精确浮动点进行断言而导致的难以捉摸的失败。Numpy 提供了一套[近似断言](https://numpy.org/doc/stable/reference/routines.testing.html)，可以在与你的应用相关的精度水平上进行测试。

# **怀疑集成问题**

曾经玩过“传话游戏”吗？ **在每次传递时，理解度都会下降。复杂系统有许多传递环节。** 你对每个团队成员在快速交付时，如何全面地文档化和沟通数据语义有多少信心？或者，几个月或几年后，当你做出更改时，你对自己能否精确地记住所有这些事情又有多大信心？

复杂软件中的管道是问题频发的地方。解决方案是创建一组黑盒测试用例，测试整个管道的输出。尽管这需要定期更新，如果你的模型或代码频繁变化，但它覆盖了大量的代码并且可以快速发现未预见的影响。所花的时间是值得的。

```py
def test_pipeline(pipeline_instance):
    # Execute the entire pipeline over a set of test configurations that 
    # exemplify the important cases

    # complex inputs, file paths, and whatever your pipeline needs
    test_configuration = 'some config here'

    # Run the entire flow. Data munging, feature engineering, prediction, 
    # post processing
    result = pipline_instance.run(test_configuration)
    assert np.testing.assert_almost_equal(result, 0.1337)
```

测试整个管道保持 ML 应用的健康。

## 信任是有代价的。

在开发 ML 管道时，偏执是一种美德。你的管道和依赖关系越复杂，出错的可能性就越大，而这些错误往往是你的算法至关重要的部分。即使你的上游依赖由有能力的团队管理，你真的能指望他们永远不会犯错吗？**输入数据绝对不会被破坏的可能性为零吗**？**大概不行。**但是，为人类所犯错误做准备的公式其实很简单。

1.  坚持已知的输入范围。

1.  快速失败。

1.  高声失败。

最简单的方法是在管道的早期尽早检查已知的输入范围。你可以手动设置这些范围，或者在训练模型时与模型一起学习这些范围。

```py
def check_inputs(frame):
  """ In this scenario we're checking areas
  for a wide but  plausible range. The goal is really just to make sure
  nothing has gone completely wrong in the inputs."""

  conforms = frame['square_footage'].apply(lambda x: 1 < x < 1000000)

  if not conforms.all():
    # Fail loud. We're no longer in the state the pipeline was designed for.
    raise ValueError("Some square_footage values are not in plausible range") 
```

上面的例子演示了公式。只需对每个输入重复此操作。将其放在管道的最前面。嘈杂的验证函数实现起来很快，并能帮助团队避免不幸的后果。这种简单的检查方法本可以帮助我们避免那次不幸的“空值替换为零”错误。然而，这些测试并不能捕捉到所有涉及多变量交互的场景。这时，稍后提到的 MLOps 技术便会发挥作用，显著提高鲁棒性水平。

# 运行真实数据障碍赛。

单元测试就像一把外科手术刀，能够精确控制并执行代码中的具体路径。集成测试则非常适合检查数据是否按预期流经整个系统。然而，**在真实数据中总是存在“未知的未知”**。

理想的部署前检查是将你的 ML 管道应用于尽可能多的真实数据，具体取决于时间和成本。接下来，对结果进行剖析，以发现异常值、错误和边缘情况。作为额外的好处，你还可以利用大规模执行进行性能测试和基础设施成本估算。

另一个有效的策略是“软启动”你的模型。在正式发布之前，先让一小部分用户体验。这让你能够发现任何负面的用户反馈，并且可以在小规模上找到真实世界中的失败，而不是在大规模上发现。这也是一个很好的时机，可以对现有或替代方案进行 A/B 测试。

# **测试永远不够**。

创建并认真维护单元测试只是开始。显然，实时软件需要异常处理策略、监控和告警。当软件依赖于一个可能很快过时的学习模型时，这一点尤为重要。

MLOps 领域已经发展起来，专门解决这些挑战。本文不会深入概述 MLOps 的现状，但这里有一些快速的想法，帮助你监控 ML 应用程序，超越"[黄金信号](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals)"。

+   **寻找目标漂移——预测分布与长期或测试平均值之间的偏差。** 例如，在足够大的样本中，预测的类别应与基础率分布相似。你可以监控最近预测与预期分布之间的偏离，作为某些事情发生变化的迹象。

+   **特征漂移的监控同样重要，甚至比预测漂移更为重要。** 特征是你对世界的快照。如果它们的关系不再与模型学习到的关系匹配，预测的有效性就会急剧下降。类似于监控预测，关键在于监控特征与初始分布之间的偏离。监控特征之间关系的变化更为强大。特征监控会在用户发现之前捕捉到储蓄模型预测出不可能的值。

大型云平台工具，如 Azure AI、Vertex AI 和 SageMaker，都提供内置的漂移检测功能。其他选项包括 [Fiddler AI](https://www.fiddler.ai/) 和 [EvidentlyAI](https://www.evidentlyai.com/)。欲了解更多关于如何选择机器学习技术栈的想法，请参阅 [适合每个团队的机器学习扩展选项](https://medium.com/towards-data-science/machine-learning-scaling-options-for-every-team-5e713d66bf03)。

# 结论

从训练到部署及以后的整个机器学习流程的保持最佳状态是一项挑战。幸运的是，凭借精明的测试和监控策略，这完全是可以管理的。密切关注一些关键信号，以防止即将发生的灾难！单元测试管道，以检测大规模代码库中的断裂。尽可能多地利用你的生产数据。监控预测和特征，以确保你的模型保持相关性。
