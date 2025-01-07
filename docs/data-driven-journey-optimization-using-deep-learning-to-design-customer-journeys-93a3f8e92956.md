# 数据驱动的旅程优化：使用深度学习设计客户旅程

> 原文：[https://towardsdatascience.com/data-driven-journey-optimization-using-deep-learning-to-design-customer-journeys-93a3f8e92956?source=collection_archive---------5-----------------------#2024-11-06](https://towardsdatascience.com/data-driven-journey-optimization-using-deep-learning-to-design-customer-journeys-93a3f8e92956?source=collection_archive---------5-----------------------#2024-11-06)

## 机器学习模型能否学会构建最优的客户旅程？

[](https://medium.com/@brechterlaurin?source=post_page---byline--93a3f8e92956--------------------------------)[![Laurin Brechter](../Images/5a68b96bddf86846a2bef9d482ef9dd3.png)](https://medium.com/@brechterlaurin?source=post_page---byline--93a3f8e92956--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--93a3f8e92956--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--93a3f8e92956--------------------------------) [Laurin Brechter](https://medium.com/@brechterlaurin?source=post_page---byline--93a3f8e92956--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--93a3f8e92956--------------------------------) ·阅读时间8分钟·2024年11月6日

--

![](../Images/141b852186fec6412524cd5d8908e7ce.png)

使用束搜索优化客户旅程

市场营销归因传统上是回顾性的：分析过去的客户旅程，了解哪些接触点对转化做出了贡献。但如果我们能利用这些历史数据来设计最优的未来旅程呢？在这篇文章中，我将展示如何将深度学习与优化技术相结合，设计高转化率的客户旅程，同时尊重现实世界中的约束条件。我们将通过使用LSTM预测高转化概率的旅程，然后使用束搜索来找到具有高转化机会的序列来实现这一目标。所有图片均由作者创作。

# 介绍

客户与企业的互动可以被称为客户旅程。在这一旅程中，他们通过所谓的接触点（例如社交媒体、谷歌广告等）与公司接触。在任何时刻，用户都可能发生转化（例如购买你的产品）。我们希望了解在这一旅程中的哪些接触点对转化做出了贡献，从而优化转化率。

# 传统归因的局限性

在深入了解我们的解决方案之前，了解传统归因模型为何存在不足是很重要的。

## 1\. 位置无关的归因

传统的归因模型（首接触、末接触、线性等）通常会为每个渠道分配一个单一的重要性分数，无论该渠道在客户旅程中处于什么位置。这是根本性的缺陷，因为：

+   社交媒体广告在意识阶段可能非常有效，但在考虑阶段影响力较小

+   邮件的有效性通常取决于它是欢迎邮件、培养序列还是重新参与活动

+   重新定向广告作为第一次接触点意义不大，但在客户旅程后期可以非常有力

## 2\. 上下文盲点

大多数归因模型（即使是数据驱动的）忽略了关键的上下文因素：

+   **客户特征**：年轻的科技精通型客户可能会对数字渠道与传统渠道有不同的响应

```py
Customer 1 (Young, Urban): Social → Video → Purchase
Customer 2 (Older, Rural): Print → Email → Purchase
```

+   **先前购买历史**：现有客户通常需要与新客户不同的参与策略

+   **一天中的时间/一周中的时间**：渠道效果可能会根据时机有显著变化

+   **设备/平台**：相同的渠道在不同的平台上可能表现不同

+   **地理/文化因素**：在一个市场有效的方法可能在另一个市场失败

## 3\. 静态渠道值

传统模型假设渠道效果可以用一个数字表示，其中所有其他影响效果的因素被边缘化。如上所述，渠道效果高度依赖于上下文，应该是该上下文的一个函数（例如位置、其他接触点等）。

# 深度学习进入舞台

客户旅程本质上是顺序的——接触点的顺序和时机非常重要。我们可以将归因建模框架设为*二元时间序列分类*任务，我们希望从接触点序列中预测客户是否转化。这使得它们成为使用R*递归神经网络*（RNN）特别是*长短期记忆*（LSTM）网络的顺序建模的完美候选。这些模型能够捕捉顺序数据中的复杂模式，包括：

+   不同渠道组合的效果

+   接触点排序的重要性

+   时间敏感性

+   渠道交互效应

## 从历史数据中学习

第一步是训练一个LSTM模型，基于历史客户旅程数据。对于每个客户，我们需要：

1.  他们遇到的接触点的顺序

1.  客户是否最终转化

1.  客户的特征

LSTM学会根据任何一系列接触点预测转化概率。这为我们提供了一个强大的“模拟器”，可以评估任何提出的客户旅程的可能效果。

由于我没有找到合适的数据集（特别是包含客户特征作为上下文数据的），我决定生成自己的合成数据。数据生成的笔记本可以在[这里](https://github.com/LaurinBrechter/PatternMining/blob/main/attribution_modelling.ipynb)找到。我们为每个客户生成一些特征和随机数量的客户旅程。这些旅程长度是随机的。在旅程的每一个点，客户都会与一个接触点互动，并且有转化的概率。这个概率由多个因素组成。

+   渠道的基础转化率

+   位置乘数。某些渠道在旅程的某些位置上更加或更少有效。

+   一个细分乘数。渠道的有效性取决于客户所在的细分群体。

+   我们还会有交互效应。例如，如果用户年轻，那么像社交和搜索这样的接触点会更有效。

+   此外，前一个接触点对当前接触点的有效性也有影响。

![](../Images/7851800686a72b45682b42118fd66169.png)![](../Images/53ae754078112929b1a802d2ef534af7.png)

旅程数据（左）和用户数据（右）

接着，我们通过合并这两张表格、缩放数值特征以及对类别特征进行OneHot编码来预处理数据。然后，我们可以建立一个LSTM模型，处理在嵌入后按顺序排列的接触点数据。在最后的全连接层中，我们还加入了客户的上下文特征。数据预处理和训练的完整代码可以在这个[notebook](https://github.com/LaurinBrechter/PatternMining/blob/main/attribution_modelling.ipynb)中找到。

然后，我们可以使用二元交叉熵损失函数训练神经网络。我已经绘制了在测试集上实现的召回率，见下图。在这种情况下，我们更关注召回率而非准确率，因为我们希望尽可能多地检测到会转化的客户。如果错误地预测某些客户会转化，而他们实际上不会，这比错过潜力较大的客户要轻微得多。

![](../Images/5998b8c4871c80ab6116a6a598fd04de.png)

训练JourneyLSTM

此外，我们会发现，大多数旅程并未导致转化。我们通常会看到2%到7%之间的转化率，这意味着我们有一个高度不平衡的数据集。出于同样的原因，准确率并不是特别有意义。总是预测多数类（在这种情况下是“无转化”）会得到一个很高的准确率，但我们无法找到任何转化的用户。

# 从预测到优化

一旦我们有了训练好的模型，就可以用它来设计最优的旅程。我们可以对一组客户强加一系列渠道（如下例，先是渠道1然后是渠道2），并观察模型预测的转化概率。我们已经可以看到，这些概率根据客户的特征差异很大。因此，我们希望为每个客户单独优化旅程。

![](../Images/d571bec702bb098ab68e075be6b6b91c.png)

强加的旅程和预测的转化概率

此外，我们不能仅仅选择最高概率的序列。现实世界中的营销有很多约束：

+   渠道特定限制（例如，电子邮件频率上限）

+   在特定位置所需的接触点

+   预算约束

+   时间要求

因此，我们将此问题框定为一个受约束的组合优化问题：找到一个触点序列，在满足所有约束的同时，最大化模型预测的转化概率。在这种情况下，我们将仅在旅程中的特定位置限制触点的出现。也就是说，我们有一个从位置到触点的映射，指定某个触点必须出现在给定的位置。

还需要注意，我们的目标是优化预定义的旅程长度，而不是任意长度的旅程。根据模拟的性质，由于在每个触点上都有非零的转化概率，因此整体转化概率会严格单调递增。因此，更长的旅程（更多的非零条目）通常会优于较短的旅程，我们可能会构造出无限长的旅程。

## 使用束搜索进行优化

以下是使用递归实现束搜索的代码。在每一层，我们优化旅程中的某个位置。如果该位置处于约束条件中并且已经固定，我们将跳过它。如果我们达到了要优化的最大长度，我们将停止递归并返回结果。

在每一层，我们查看当前的解决方案并生成候选解。在任何时刻，我们保留由束宽定义的最佳K个候选解。这些最佳候选解将作为输入用于下一轮的束搜索，在其中我们优化序列中的下一个位置。

```py
def beam_search_step(
        model: JourneyLSTM, 
        X: torch.Tensor, 
        pos: int, 
        num_channels: int, 
        max_length: int, 
        constraints:dict[int, int], 
        beam_width: int = 3
    ):
    if pos > max_length:
        return X

    if pos in constraints:
        return beam_search_step(model, X, pos + 1, num_channels, max_length, constraints, beam_width)

    candidates = []  # List to store (sequence, score) tuples

    for sequence_idx in range(min(beam_width, len(X))):
        X_current = X[sequence_idx:sequence_idx+1].clone()

        # Try each possible channel
        for channel in range(num_channels):
            X_candidate = X_current.clone()
            X_candidate[0, extra_dim + pos] = channel

            # Get prediction score
            pred = model(X_candidate)[0].item()
            candidates.append((X_candidate, pred))

    candidates.sort(key=lambda x: x[1], reverse=True)
    best_candidates = candidates[:beam_width]

    X_next = torch.cat([cand[0] for cand in best_candidates], dim=0)

    # Recurse with best candidates
    return beam_search_step(model, X_next, pos + 1, num_channels, max_length, constraints, beam_width)
```

这种优化方法是贪婪的，我们很可能会错过高概率的组合。然而，在许多场景下，特别是在有许多渠道的情况下，暴力求解最优解可能不可行，因为可能的旅程数量随着旅程长度的增加呈指数级增长。

![](../Images/141b852186fec6412524cd5d8908e7ce.png)

使用束搜索优化客户旅程

在上图中，我们为单个客户优化了转化概率。在位置0，我们已将“电子邮件”指定为固定触点。然后，我们探索与电子邮件的可能组合。由于我们设置的束宽为五，所有组合（例如，电子邮件 -> 搜索）都进入下一轮。在那一轮中，我们发现了高潜力的旅程，该旅程将用户展示两次电子邮件，最终进行再营销。

# 结论

从预测到优化的转变，在归因建模中意味着我们从预测性建模转向指导性建模，即模型告诉我们需要采取的行动。这有可能实现更高的转化率，特别是当我们面对具有许多渠道和上下文变量的复杂场景时。

同时，这种方法也有几个缺点。首先，如果我们没有一个足够准确地检测转化客户的模型，我们可能会降低转化率。其次，模型输出的概率必须经过良好的校准。否则，我们优化的转化概率可能没有实际意义。最后，当模型需要预测超出其数据分布的旅程时，我们将遇到问题。因此，使用强化学习（RL）方法也是一种值得考虑的选择，在这种方法中，模型可以主动生成新的训练数据。
