# 终身机器学习的起源：为什么LLML是AI领域的下一个游戏改变者（第一部分）

> 原文：[https://towardsdatascience.com/the-origins-of-lifelong-ml-part-1-of-why-llml-is-the-next-game-changer-of-ai-8dacf9897143?source=collection_archive---------12-----------------------#2024-01-17](https://towardsdatascience.com/the-origins-of-lifelong-ml-part-1-of-why-llml-is-the-next-game-changer-of-ai-8dacf9897143?source=collection_archive---------12-----------------------#2024-01-17)

## 通过Q学习和基于解释的神经网络理解终身机器学习的力量

[](https://medium.com/@almond.maj?source=post_page---byline--8dacf9897143--------------------------------)[![Anand Majmudar](../Images/4840cb28e81326221cebef9f540c8e12.png)](https://medium.com/@almond.maj?source=post_page---byline--8dacf9897143--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8dacf9897143--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8dacf9897143--------------------------------) [Anand Majmudar](https://medium.com/@almond.maj?source=post_page---byline--8dacf9897143--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8dacf9897143--------------------------------) ·阅读时间 10分钟·2024年1月17日

--

![](../Images/07305a2b6bc6a0fd59db4598e74b716c.png)

太空中的AI机器人，使用GPT-4生成

机器学习接下来如何发展？许多，甚至可以说是大多数，机器学习领域的伟大创新都受到了神经科学的启发。神经网络和基于注意力的模型的发明就是[典型例子](https://www.cell.com/neuron/pdf/S0896-6273(17)30509-3.pdf)。同样，机器学习的下一次革命将从大脑中汲取灵感：终身机器学习。

现代机器学习仍然缺乏像人类一样在学习新领域时使用过去信息的能力。例如，一个学会了走路的强化学习代理，在学习如何攀爬时仍然从零开始。然而，代理可以使用*持续学习*：它可以将走路中获得的知识应用于学习攀爬的过程，就像人类一样。

受到这一特性的启发，终身机器学习（LLML）利用过去的知识来更高效地学习新任务。通过在机器学习中逼近持续学习，我们可以极大地提高学习者的时间效率。

为了理解LLML的强大功能，我们可以从其起源开始，逐步了解现代LLML。在第一部分中，我们探讨了Q学习和基于解释的神经网络。在第二部分中，我们将深入研究高效的终身学习算法和Voyager！尽管我鼓励你先阅读第一部分再阅读第二部分，但如果你愿意，也可以直接跳到第二部分！

**终身机器学习的起源**

Sebastian Thrun 和 Tom Mitchell，LLML的奠基人，开始他们的LLML之旅时，研究了强化学习在机器人中的应用。如果读者曾经看到过一个可视化的强化学习者（例如这个代理 [学习玩宝可梦](https://www.youtube.com/watch?v=DcYLT37ImBY&t=17s)），他们会意识到，为了在合理的人类时间尺度内取得任何训练结果，代理必须能够在训练期间迭代数百万次动作（如果不是更多的话）。然而，机器人每个动作需要几秒钟才能完成。因此，将典型的在线强化学习方法应用于机器人，会显著降低最终机器人模型的效率和能力。

*是什么让人类在现实世界学习中如此出色，而机器人中的机器学习目前却失败了呢？*

Thrun 和 Mitchell 发现了现代机器学习能力中可能最大的空白：它无法将过去的信息应用于新任务。为了解决这个问题，他们创造了第一个基于解释的神经网络（EBNN），这也是LLML的首次应用！

要理解它是如何工作的，我们首先需要了解典型的强化学习（RL）是如何运作的。在强化学习中，我们的机器学习模型决定了代理的行动，我们可以将其视为与我们选择的环境进行互动的“身体”。我们的代理存在于环境W中，具有状态Z，当代理采取行动A时，它会收到感知S（来自环境的反馈，例如物体的位置或温度）。我们的环境是一个映射 Z x A -> Z（对于每一个动作，环境会以指定的方式发生变化）。我们希望在模型F：S -> A中最大化奖励函数R：S -> R（换句话说，我们希望选择能获得最佳结果的动作，我们的模型将感知作为输入并输出一个动作）。如果代理有多个任务需要学习，每个任务都有其自己的奖励函数，我们希望最大化每个函数。

我们可以独立训练每个单独的任务。然而，Thrun 和 Michael 意识到，每个任务都发生在相同的环境中，代理面临相同的可能动作和感知（只是每个任务有不同的奖励函数）。因此，他们创建了EBNN，利用以前问题中的信息来解决当前任务（LLML）！例如，一个机器人可以利用它在杯子翻转任务中学到的知识来执行杯子移动任务，因为它在杯子翻转中学会了如何抓住杯子。

为了了解EBNN是如何工作的，我们现在需要理解Q函数的概念。

**Q* 和 Q学习**

Q: S x A -> r 是一个评估函数，其中r表示在状态S下采取动作A后的预期未来总奖励。如果我们的模型学会了准确的Q，它可以简单地选择在任何给定时刻最大化Q的动作。

现在，我们的问题转化为学习一个准确的Q，我们称之为Q*。其中一种方法被称为[Q学习](https://link.springer.com/article/10.1007/BF00992698)，一些人认为它是OpenAI的Q*背后的灵感来源（尽管命名可能完全是巧合）。

在Q学习中，我们将我们的动作策略定义为函数π，该函数为每个状态输出一个动作，状态X的值定义为函数

![](../Images/0cff7e4188ddcc03fcc0927f638df227.png)

我们可以将其视为动作π(x)的即时奖励，再加上所有可能未来动作的概率之和乘以它们的价值（我们通过递归计算这些价值）。我们希望找到最佳策略（动作集）π*，使得

![](../Images/d7d1479a4315a93a7854a2bcf6b0ebbc.png)

（在每个状态下，策略选择最大化V*的动作）。随着过程的重复，Q值将变得更加准确，从而改善代理选择的动作。现在，我们将Q*值定义为执行动作a的真实预期奖励：

![](../Images/5c7586e24c26f91a82cfcb8885a0be5b.png)

在Q学习中，我们将学习π*的问题简化为学习π*的Q*值的问题。显然，我们希望选择Q值最大的动作。

我们将训练分为多个回合。在第n回合，我们获得状态x_n，选择并执行动作a_n，观察到y_n，获得奖励r_n，并根据以下公式调整Q值：

![](../Images/07de44e2c35e8317996dadccde25c9f7.png)

其中

![](../Images/f8fea711a426d35276fb3f20ae157a92.png)

实质上，除了与前一个状态x和所选动作a对应的Q值外，我们将所有先前的Q值保持不变。对于那个Q值，我们通过将前一个回合的Q值乘以(1 - α)并加上我们的回报，再加上当前状态y的前一个回合的最大值，这两者都乘以α来更新它。

记住，这个算法的目标是近似每个可能状态下每个可能动作的准确Q值。所以当我们更新Q时，我们更新与旧状态和该回合采取的动作对应的Q值，因为我们

α越小，我们每个回合更新Q值的变化就越小（1 - α会非常大）。α越大，我们就越不关心Q的旧值（当α=1时，它完全无关紧要），而越关注我们所发现的新状态的预期值。

让我们考虑两种情况，以便直观地理解这个算法，以及它如何在我们从状态x采取动作a到达状态y后更新Q(x, a)：

1.  我们从状态 x 通过动作 a 到达状态 y，并处于一个“终点路径”，此时无法再执行任何操作。那么，Q(x, a)，即这个动作和之前状态的期望值，应该仅仅是该动作的即时奖励（想想为什么！）。此外，a 的奖励越高，我们在下一个回合中选择它的可能性就越大。我们在前一个回合中该状态的最大 Q 值为 0，因为无法执行任何操作，因此我们只是将该动作的奖励添加到 Q 值中，正如预期的那样！

1.  现在，我们正确的 Q*s 从终点递归回溯！让我们考虑导致状态 w 到状态 x 的动作 b，假设现在我们已经过了 1 回合。现在，当我们更新 Q*(w, b) 时，我们将把 b 的奖励添加到 Q*(x, a) 的值中，因为如果我们之前选择了它，它必须是最高的 Q 值。因此，我们的 Q(w, b) 现在也是正确的（想想为什么）！

太好了！现在你已经对 Q-learning 有了直观的理解，我们可以回到最初的目标，继续理解：

**基于解释的神经网络（EBNN）**

我们可以看到，通过简单的 Q-learning，我们没有 LL 属性：即先前的知识被用于学习新任务。Thrun 和 Mitchell 创立了解释性神经网络学习算法（Explanation Based Neural Network Learning Algorithm，EBNN），该算法将 LL 应用于 Q-learning！我们将该算法分为 3 个步骤。

(1) 在执行一系列操作后，智能体预测接下来将发生的状态，直到最终状态 s_n，此时不再可能执行其他操作。这些预测将与实际观察到的状态不同，因为我们的预测器目前不完美（否则我们早就完成了！）。

(2) 算法提取 Q 函数关于观察到的状态的偏导数。通过最初计算最终奖励关于最终状态 s_n 的偏导数（顺便说一下，我们假设智能体已经获得奖励函数 R(s)），然后我们使用链式法则从最终状态向后计算斜率：

![](../Images/dce1650913b1d11db44865d523f2ec7c.png)

其中 M: S x A -> S 是我们的模型，R 是我们的最终奖励。

(3) 现在，我们已经估算了 Q*s 的斜率，并使用这些斜率在反向传播中更新我们的 Q 值！对于那些不知道的人，反向传播是神经网络学习的方法，通过这种方法，它们计算网络每个节点改变时，最终输出如何变化，使用这种同样的反向计算斜率方法，然后调整这些节点的权重和偏置，使得网络输出更接近期望（无论这如何通过网络的成本函数定义，成本函数与我们的奖励函数有着相同的作用）！

我们可以将（1）视为*解释*步骤（因此得名！），我们回顾过去的行动并尝试预测可能采取的行动。接着在（2）中，我们对这些预测进行*分析*，试图理解不同的行动如何影响我们的奖励变化。在（3）中，我们将这种理解应用于*学习*如何通过改变我们的Q值来改进行动选择。

该算法通过利用过去行为与过去行为估计之间的差异，作为提升估计某一行动路径效率的动力，从而提高我们的效率。你可能接下来的问题是：

*EBNN如何帮助一个任务的学习迁移到另一个任务？*

当我们将EBNN应用于多个任务时，我们将任务间共同的信息表示为神经网络（NN）行动模型，这通过解释和分析过程为我们的学习提供了助力（一个有效的偏向）。它在学习新任务时使用先前学习的*任务独立*知识。我们的关键洞察是，我们拥有可泛化的知识，因为每个任务都共享相同的代理、环境、可能的行动和可能的状态。唯一依赖于每个任务的是我们的奖励函数！因此，通过从解释步骤开始，使用特定于任务的奖励函数，我们可以利用旧任务中发现的状态作为训练示例，并简单地将奖励函数替换为当前任务的奖励函数，从而加速学习过程！LML的创始人发现，机器人抓杯任务的时间效率提高了3到4倍，这仅仅是开始！

如果我们重复这个解释和分析过程，我们可以替代一些朴素Q学习中需要通过现实世界探索代理环境的部分需求！而且我们使用得越多，它就变得越有效，因为（抽象地说）它可以从更多的知识中获取信息，增加这些知识与当前任务相关的可能性。

自从LLML的创始人提出使用任务独立信息来学习新任务的思想以来，LLML不仅在机器人强化学习中取得了进展，而且扩展到我们今天所知的更广泛的机器学习领域：监督学习。Paul Ruvolo和Eric Eatons的高效终身学习算法（ELLA）将使我们更接近理解LLML的强大力量！

请阅读[第二部分：通过ELLA检查LLML](https://medium.com/@almond.maj/examining-lifelong-machine-learning-through-ella-and-voyager-part-2-of-why-llml-is-next-in-ai-bea36a01f529)和Voyager，了解它是如何工作的！

感谢阅读第一部分！欢迎访问我的网站[anandmaj.com](https://anandmaj.com/)，那里有我的其他文章、项目和艺术作品，并在[Twitter](https://twitter.com/Almondgodd)上关注我。

**原始论文及其他来源：**

Thrun和Mitchel: [终身机器人学习](https://www.sciencedirect.com/science/article/abs/pii/092188909500004Y)

Watkins: [Q学习](https://link.springer.com/article/10.1007/BF00992698)

陈和刘，《终身机器学习》（启发我写这篇文章！）：[https://www.cs.uic.edu/~liub/lifelong-machine-learning-draft.pdf](https://www.cs.uic.edu/~liub/lifelong-machine-learning-draft.pdf)

带有课程的无监督LL: [https://par.nsf.gov/servlets/purl/10310051](https://par.nsf.gov/servlets/purl/10310051)

深度LL: [https://towardsdatascience.com/deep-lifelong-learning-drawing-inspiration-from-the-human-brain-c4518a2f4fb9](/deep-lifelong-learning-drawing-inspiration-from-the-human-brain-c4518a2f4fb9)

神经启发的AI: [https://www.cell.com/neuron/pdf/S0896-6273(17)30509-3.pdf](https://www.cell.com/neuron/pdf/S0896-6273(17)30509-3.pdf)

具身LL: [https://lis.csail.mit.edu/embodied-lifelong-learning-for-decision-making/](https://lis.csail.mit.edu/embodied-lifelong-learning-for-decision-making/)

高效LLA (ELLA): [https://www.seas.upenn.edu/~eeaton/papers/Ruvolo2013ELLA.pdf](https://www.seas.upenn.edu/~eeaton/papers/Ruvolo2013ELLA.pdf)

情感分类中的LL: [https://arxiv.org/abs/1801.02808](https://arxiv.org/abs/1801.02808)

知识基础思想: [https://arxiv.org/ftp/arxiv/papers/1206/1206.6417.pdf](https://arxiv.org/ftp/arxiv/papers/1206/1206.6417.pdf)

AGI LLLM LLMs: [https://towardsdatascience.com/towards-agi-llms-and-foundational-models-roles-in-the-lifelong-learning-revolution-f8e56c17fa66](/towards-agi-llms-and-foundational-models-roles-in-the-lifelong-learning-revolution-f8e56c17fa66)

DEPS: [https://arxiv.org/pdf/2302.01560.pdf](https://arxiv.org/pdf/2302.01560.pdf)

Voyager: [https://arxiv.org/pdf/2305.16291.pdf](https://arxiv.org/pdf/2305.16291.pdf)

元学习: [https://machine-learning-made-simple.medium.com/meta-learning-why-its-a-big-deal-it-s-future-for-foundation-models-and-how-to-improve-it-c70b8be2931b](https://machine-learning-made-simple.medium.com/meta-learning-why-its-a-big-deal-it-s-future-for-foundation-models-and-how-to-improve-it-c70b8be2931b)

元强化学习调查: [https://arxiv.org/abs/2301.08028](https://arxiv.org/abs/2301.08028)
