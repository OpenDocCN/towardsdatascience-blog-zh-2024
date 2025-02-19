# 评估模型再训练策略

> 原文：[`towardsdatascience.com/evaluating-model-retraining-strategies-9c337d95409a?source=collection_archive---------2-----------------------#2024-10-20`](https://towardsdatascience.com/evaluating-model-retraining-strategies-9c337d95409a?source=collection_archive---------2-----------------------#2024-10-20)

## 数据漂移和概念漂移如何影响选择正确的再训练策略？

[](https://medium.com/@reinhard.sellmair?source=post_page---byline--9c337d95409a--------------------------------)![Reinhard Sellmair](https://medium.com/@reinhard.sellmair?source=post_page---byline--9c337d95409a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9c337d95409a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9c337d95409a--------------------------------) [Reinhard Sellmair](https://medium.com/@reinhard.sellmair?source=post_page---byline--9c337d95409a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9c337d95409a--------------------------------) ·阅读时间 9 分钟·2024 年 10 月 20 日

--

![](img/b6d9de178844a026183cbd16af50612d.png)

（由必应的图像生成器创建）

# 引言

许多 MLOps 领域的人可能听过类似的故事：

公司 A 开始了一项雄心勃勃的计划，旨在利用机器学习的力量。这是一段充满挑战的旅程，因为团队难以确定一个既能发挥机器学习优势，又能带来实际商业价值的主题。经过多次头脑风暴，他们最终确定了一个有望彻底改变他们运营的用例。满怀期待，他们与享有盛誉的公司 B 签订合同，由其负责构建和部署机器学习模型。在经过几个月的严格开发和测试后，模型通过了所有验收标准，标志着公司 A 的一项重要里程碑，他们期待未来的更多机会。

然而，随着时间的推移，模型开始产生意外结果，导致它在预期用途上失效。公司 A 联系了公司 B 寻求建议，才得知改变后的环境需要重新构建一个新的模型，这意味着需要更高的投资，甚至超过原始模型的成本。

出了什么问题？公司 B 创建的模型是不是没有达到预期效果？还是公司 A 只是运气不好，发生了意外情况？

可能的问题是，即使在部署前对模型进行最严格的测试，也不能保证该模型能够在无限的时间内表现良好。影响模型随时间变化表现的两个最重要因素是数据漂移和概念漂移。

**数据漂移**：也称为协变量漂移，指的是当输入数据的统计特性随时间发生变化时出现的现象。如果一个机器学习模型是在特定人群的数据上训练的，但输入数据的人群特征发生了变化，模型的性能可能会下降。假设你教一个孩子乘法表直到 10。它能迅速告诉你 3 * 7 或 4 * 9 的正确答案。然而，有一次你问它 4 * 13，尽管乘法规则并没有变化，它可能会给出错误的答案，因为它没有记住这个解法。

**概念漂移**：当输入数据与目标变量之间的关系发生变化时，称为概念漂移。这可能会导致模型性能下降，因为模型的预测不再与不断变化的数据模式相符。这里的一个例子可以是拼写改革。当你还是孩子时，你可能学会了写“co-operate”，但现在它被写作“cooperate”。虽然你指的是同一个词，但你写这个词的方式随时间发生了变化。

在本文中，我研究了不同的数据漂移和概念漂移场景如何影响模型随时间的性能表现。此外，我还展示了哪些重新训练策略能够缓解性能下降。

我专注于评估关于模型预测性能的重新训练策略。实际上，还涉及更多的方面，如：

+   **数据可用性与质量**：确保有足够的高质量数据可用于重新训练模型。

+   **计算成本**：评估重新训练所需的计算资源，包括硬件和处理时间。

+   **业务影响**：在选择重新训练策略时，要考虑对业务运营和结果的潜在影响。

+   **合规性要求**：确保重新训练策略符合任何相关的法规和标准，例如反歧视法规。

需要考虑的因素，以识别合适的重新训练策略。

# 数据合成

![](img/64b1a6bc63bc5cff19c9d904e241f5fb.png)

(由 Bing 的图像创建工具生成)

为了突出数据漂移和概念漂移之间的差异，我合成了数据集，并控制了这些方面出现的程度。

我生成了 100 个步骤的数据集，并逐步更改参数以模拟数据集的演变。每一步包含多个数据点，可以解释为在一小时、一天或一周内收集的数据量。每一步之后，模型都会重新评估，并可以重新训练。

为了创建数据集，我首先从正态分布中随机抽取特征，其中均值µ和标准差σ依赖于步骤编号 s：

![](img/21accb943cab84be5919b10c73d07ec3.png)

特征 xi 的漂移取决于µi 和σi 在步骤编号 s 上变化的程度。

所有特征汇总如下：

![](img/89b0e66c590f9604e2abcc299b295eed.png)

其中 ci 是描述特征 xi 对 X 影响的系数。概念漂移可以通过根据步骤 s 改变这些系数来控制。添加一个随机数 ε（在模型训练时无法获得），以考虑特征并不包含完整的信息来预测目标 y。

目标变量 y 通过将 X 输入到非线性函数中计算得到。这样做会为机器学习模型创造一个更具挑战性的任务，因为特征与目标之间没有线性关系。本文中的情景，我选择了一个正弦函数。

![](img/1364afc8dbd6921c20ebc1c343ad6102.png)

# 情景分析

![](img/7879fabd0027dada605891831f21d422.png)

(使用 Bing 的 Image Creator 创建)

我创建了以下情景进行分析：

+   **稳态：**模拟无数据或概念漂移 — 参数 µ，σ，和 c 与步骤 s 无关

+   **分布漂移：**模拟数据漂移 — 参数 µ，σ 是 s 的线性函数，参数 c 与 s 无关

+   **系数漂移：**模拟概念漂移：参数 µ，σ 与 s 独立，参数 c 是 s 的线性函数

+   **黑天鹅：**模拟一个意外且突然的变化 — 参数 µ，σ 和 c 除了在某一步骤中这些参数发生变化外，其它步骤与步骤 s 无关

COVID-19 大流行是黑天鹅事件的典型例子。黑天鹅事件的特点是其极为罕见且出乎意料。COVID-19 无法提前预测，因此也无法减轻其影响。许多部署的机器学习模型在疫情爆发后突然产生了意想不到的结果，必须重新训练。

对于每个情景，我使用了前 20 步作为初始模型的训练数据。在剩余的步骤中，我评估了三种重新训练策略：

+   ***None*：**不进行重新训练 — 在所有剩余步骤中都使用基于训练数据训练的模型。

+   ***All Data*：**使用所有之前的数据训练新模型，例如，在步骤 30 评估的模型是基于步骤 0 到 29 的数据训练的。

+   ***Window*：**使用固定窗口大小选择训练数据，例如，对于窗口大小为 10，步骤 30 的训练数据包含步骤 20 到 29 的数据。

我使用了 XG Boost 回归模型，并以均方误差（MSE）作为评估指标。

## 稳态

![](img/30ca7f88807354199e0dd656fc593f86.png)

稳态情景的预测误差

上图显示了稳态情景的评估结果。由于前 20 步用于训练模型，因此评估误差远低于后续步骤。在整个情景中，*None* 和 *Window* 重新训练策略的性能保持在相似水平。*All Data* 策略在较高步数时略微降低了预测误差。

在这种情况下，*All Data* 是最好的策略，因为它从不断增加的训练数据量中受益，而其他策略的模型则在固定的训练数据量上进行训练。

## 分布漂移（数据漂移）

![](img/7078ada35dc9de7ba1f329f1249c5572.png)

分布漂移场景的预测误差

当输入数据分布发生变化时，我们可以清楚地看到，如果模型没有在最新数据上进行重训练，预测误差会持续增加。无论是在所有数据上进行重训练，还是在数据窗口上进行重训练，性能几乎相同。原因在于，尽管*所有数据*使用了更多的数据，但旧数据对于预测最新数据并不相关。

## 系数漂移（概念漂移）

![](img/cd1fa6455b041e6e0dbc79a0c63b1a67.png)

系数漂移场景的预测误差

系数变化意味着特征的重要性随时间变化。在这种情况下，我们可以看到*无重训练*策略的预测误差急剧增加。此外，结果显示，在所有数据上进行重训练也导致预测误差的持续增加，而*窗口重训练*策略则保持预测误差在一个恒定水平。

*所有数据*策略的表现随时间下降的原因是，训练数据中包含了越来越多类似输入却导致不同输出的情况。因此，模型在识别清晰模式并推导决策规则时变得更加困难。这对于*窗口*策略来说问题较小，因为忽略了旧数据，这使得模型能够“忘记”旧的模式，并专注于最新的情况。

## 黑天鹅

![](img/e662136c11e4449771ef27c39905d738.png)

黑天鹅事件场景的预测误差

黑天鹅事件发生在第 39 步，所有模型的误差在这一点突然增加。然而，在最新数据上重新训练一个新模型后，*所有数据*和*窗口*策略的误差恢复到了之前的水平。这在*无重训练*策略中并不适用，在这种策略下，误差比黑天鹅事件发生前增加了大约三倍，并一直保持在该水平直到场景结束。

与之前的场景相比，黑天鹅事件包含了数据漂移和概念漂移。值得注意的是，*所有数据*和*窗口*策略在黑天鹅事件后以相同的方式恢复，而在概念漂移场景中我们发现这两者之间有显著的差异。其原因可能是数据漂移与概念漂移同时发生。因此，在旧数据上学习到的模式在黑天鹅事件之后不再相关，因为输入数据已经发生了变化。

这可以是一个例子：你是一个翻译员，收到请求翻译你以前没有翻译过的语言（数据漂移）。同时，该语言发生了全面的拼写改革（概念漂移）。虽然那些翻译这门语言多年的人可能在应用这些改革时遇到困难，但这对你没有影响，因为你甚至在改革前就不知道这些规则。

要复制此分析或进一步探索，您可以查看我的[git 仓库](https://github.com/ReinhardSellmair/mlmm)。

# 结论

识别、量化并缓解数据漂移和概念漂移的影响是一个具有挑战性的课题。在本文中，我分析了简单的场景，以展示这些概念的基本特征。更全面的分析无疑将提供对这个课题更深入和更详细的结论。

这是我从这个项目中学到的内容：

缓解概念漂移比数据漂移更具挑战性。虽然数据漂移可以通过基本的再训练策略来处理，但概念漂移则需要更仔细地选择训练数据。具有讽刺意味的是，数据漂移和概念漂移同时发生的情况可能比纯粹的概念漂移情况更容易处理。

对训练数据进行全面分析将是找到合适的再训练策略的理想起点。因此，按照记录数据的时间对训练数据进行分区至关重要。为了对模型性能进行最真实的评估，最新的数据应仅用作测试数据。为了初步评估数据漂移和概念漂移，剩余的训练数据可以分成两个大小相等的集合，较旧的数据放在一个集合中，较新的数据放在另一个集合中。比较这些集合的特征分布可以评估数据漂移。分别在每个集合上训练一个模型，并比较特征重要性的变化，可以对概念漂移做出初步评估。

在所有场景中，不进行再训练是最差的选择。此外，在没有考虑到模型再训练的情况下，评估和/或再训练模型的数据也更可能没有以自动化方式收集。这意味着模型性能的退化可能无法被及时识别，或者直到较晚阶段才会被注意到。一旦开发者意识到模型可能存在问题，宝贵的时间将被浪费，直到收集到可以用来再训练模型的新数据。

在早期阶段识别完美的再训练策略是非常困难的，如果服务数据发生了意外变化，这种识别甚至可能是不可能的。因此，我认为合理的做法是从一个在划分后的训练数据上表现良好的再训练策略开始。当出现未能以最佳方式应对变化的情况时，应及时审查并更新该策略。持续的模型监控对于快速发现并在模型性能下降时做出反应至关重要。

*如果没有特别说明，所有图片均由作者创建。*
