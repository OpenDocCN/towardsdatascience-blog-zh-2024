# 用强化学习彻底改变大数据集的特征选择

> 原文：[`towardsdatascience.com/reinforcement-learning-for-feature-selection-be1e7eeb0acc?source=collection_archive---------2-----------------------#2024-05-19`](https://towardsdatascience.com/reinforcement-learning-for-feature-selection-be1e7eeb0acc?source=collection_archive---------2-----------------------#2024-05-19)

## 在面对非常大的数据集时，利用强化学习的优势进行特征选择

[](https://medium.com/@baptistelefort?source=post_page---byline--be1e7eeb0acc--------------------------------)![Baptiste Lefort](https://medium.com/@baptistelefort?source=post_page---byline--be1e7eeb0acc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--be1e7eeb0acc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--be1e7eeb0acc--------------------------------) [Baptiste Lefort](https://medium.com/@baptistelefort?source=post_page---byline--be1e7eeb0acc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--be1e7eeb0acc--------------------------------) ·阅读时长 11 分钟·2024 年 5 月 19 日

--

了解强化学习如何改变机器学习模型的特征选择。通过实际示例和专用的 Python 库，学习这一创新方法的过程、实现及其优势。

![](img/743ea5c0887670d49bff360b95359e4c.png)

图片来自 Jared Murray，来源于[Unplash](https://unsplash.com/photos/NSuufgf-BME)

**特征选择是构建机器学习模型过程中的一个决定性步骤。** 为模型和我们想要实现的任务选择合适的特征，确实可以提升性能。事实上，某些特征可能会引入噪声，从而干扰模型。

此外，选择特征对于处理高维数据集尤其重要。它能使模型更快、更好地学习。其核心思想是找到最优的特征数量和最具意义的特征。

在这篇文章中，我将解决这个问题，并**通过引入一种新实现的特征选择方法，进一步探讨**。尽管存在许多不同的特征选择过程，但由于很多文章已经在讨论它们，因此在这里不做介绍。我将重点介绍使用强化学习策略进行特征选择。

首先，将介绍强化学习，尤其是马尔可夫决策过程。这是数据科学领域中的一种全新方法，特别是在特征选择方面。接下来，我将介绍如何实现这一方法以及如何安装和使用 Python 库（**FSRLearning**）。最后，我将证明这种实现的有效性。在所有可能的特征选择方法中，如包装器或过滤器，**强化学习是最强大和高效的**。

本文的目标是强调针对具体实际问题的应用实现。虽然一些参考资料将在文末提供，但本文会通过示例简化该问题的理论部分。

## 强化学习：特征选择的马尔可夫决策问题

已经证明，强化学习（RL）技术在解决类似游戏问题等方面非常高效。RL 的概念基于马尔可夫决策过程（MDP）。这里的重点不是深入定义 MDP，而是理解它的基本工作原理，以及它如何为我们的实际问题提供帮助。

RL 背后的直觉是，智能体从一个未知的环境开始。这个智能体必须采取行动来完成任务。根据智能体当前的状态以及他先前选择的动作，智能体将更倾向于选择某些动作。在每一个新状态到达并采取动作时，智能体都会获得奖励。以下是我们需要为特征选择定义的主要参数：

+   什么是状态？

+   什么是动作？

+   奖励是什么？

+   我们如何选择动作？

首先，状态只是数据集中的特征子集。例如，如果数据集有三个特征（年龄、性别、身高）加上一个标签，以下将是可能的状态：

```py
[]                                              --> Empty set                           
[Age], [Gender], [Height]                       --> 1-feature set
[Age, Gender], [Gender, Height], [Age, Height]  --> 2-feature set
[Age, Gender, Height]                           --> All-feature set
```

在一个状态中，特征的顺序并不重要，稍后在本文中会解释为什么。我们必须将其视为一个集合，而非特征的列表。

关于动作，从一个子集出发，我们可以通过增加一个当前状态中未曾探索的特征，转向任何其他子集。在特征选择问题中，动作就是选择一个在当前状态中未曾探索的特征，并将其添加到下一个状态。以下是一些可能的动作示例：

```py
[Age] -> [Age, Gender]
[Gender, Height] -> [Age, Gender, Height]
```

下面是一些不可能的动作示例：

```py
[Age] -> [Age, Gender, Height]
[Age, Gender] -> [Age]
[Gender] -> [Gender, Gender]
```

我们已定义了状态和动作，但未定义奖励。奖励是一个实数，用于评估状态的质量。例如，如果一个机器人试图到达迷宫的出口，并决定将出口作为下一个动作，那么与该动作相关的奖励将是“好”。如果他选择进入一个陷阱作为下一个动作，则奖励将是“不好”。奖励是一个值，反映了先前采取的动作的信息。

在特征选择问题中，一个有趣的奖励可能是通过添加新特征使模型的准确性得到提高的值。下面是奖励计算的一个示例：

```py
[Age] --> Accuracy = 0.65
[Age, Gender] --> Accuracy = 0.76
Reward(Gender) = 0.76 - 0.65 = 0.11
```

对于每个首次访问的状态，都会用特征集训练一个分类器。这个值会被存储在状态中，并且分类器的训练是非常昂贵的，即使该状态稍后再次被访问，也只会训练一次。分类器不考虑特征的顺序。这就是为什么我们可以将此问题视为图而不是树的原因。在这个示例中，选择性别作为新特征的动作的奖励是当前状态和下一个状态准确性的差值。

![](img/10e93339802fdda3ba8d0913277d4826.png)

每个状态都有多个可能的动作和相关的奖励（图像来自作者）。

在上面的图中，每个特征都被映射为一个数字（例如，“Age”是 1，“Gender”是 2，“Height”是 3）。完全可以选择其他度量来最大化以找到最优的特征集。在许多商业应用中，更看重召回率而不是准确率。

下一个重要问题是，我们如何从当前状态选择下一个状态，或者说如何探索我们的环境。我们必须找到最优的方式来执行此操作，因为这很快会变成一个非常复杂的问题。实际上，如果我们天真地探索一个具有 10 个特征的问题中的所有可能特征集，那么状态的数量将是

```py
10! + 2 = 3 628 802 possible states
```

+2 是因为我们考虑了一个空状态和一个包含所有可能特征的状态。在这个问题中，我们必须在所有状态上训练相同的模型，以获得最大化准确性的特征集。在强化学习方法中，我们不需要遍历所有状态，并且每次进入一个已访问过的状态时不需要重新训练模型。

我们必须为这个问题确定一些停止条件，这些条件会在后面详细说明。现在已经选择了 epsilon-贪婪状态选择方法。这个方法的思路是，从当前状态出发，我们以 epsilon 的概率（介于 0 和 1 之间，通常在 0.2 左右）随机选择下一个动作，否则选择最大化某个函数的动作。对于特征选择，那个函数就是每个特征对模型准确性贡献的平均奖励。

epsilon-贪婪算法包含两个步骤：

1.  随机阶段：以 epsilon 的概率，我们在当前状态的所有可能邻居中随机选择下一个状态（我们可以假设使用均匀选择或 softmax 选择）。

1.  贪婪阶段：我们选择下一个状态，使得添加到当前状态的特征对模型准确性的贡献最大。为了减少时间复杂度，我们已经初始化了一个包含每个特征对应值的列表。每次选择一个特征时，这个列表都会更新。感谢以下公式，更新过程非常优化：

![](img/fe8b18d529027bc03b6f4c32265f2db9.png)

更新每个特征的奖励平均值列表（图片由作者提供）

+   *AORf*：特征“f”带来的奖励的平均值

+   *k*：特征“f”被选择的次数

+   *V(F)*：特征集 F 的状态值（出于清晰考虑，本文未详细说明）

全局思路是找出哪个特征为模型带来了最多的准确度。这就是为什么我们需要浏览不同的状态，在多种不同的环境中评估特征对模型的全局准确值。

最后，我将详细说明两种停止条件。由于目标是最小化算法访问的状态数，我们需要对它们保持谨慎。我们访问的未访问过的状态越少，使用不同特征集训练模型的次数就越少。训练模型以获取准确度是时间和计算能力上最为耗费的阶段。

1.  算法在最终状态停止，该状态是包含所有特征的集合。我们希望避免到达此状态，因为训练模型时最为昂贵。

1.  此外，如果一系列访问过的状态的值逐渐降低，算法将停止浏览图形。已设定一个阈值，当数据集中所有特征的总数的平方根后，算法停止探索。

现在问题的建模已经解释清楚，我们将详细说明 Python 中的实现。

## 特征选择的强化学习 Python 库

有一个解决此问题的 Python 库可用。我将在本部分中解释它是如何工作的，并证明它是一种高效的策略。此外，本文还作为文档，你将在本部分结束时能够将此库用于你的项目。

## 1\. 数据预处理

由于我们需要评估访问过的状态的准确性，因此需要将特征和数据输入模型，用于此特征选择任务。数据必须进行标准化，类别变量需要编码，并且行数应尽可能少（越小越快）。此外，创建特征与整数之间的映射非常重要，正如前面部分所述。此步骤不是强制性的，但非常推荐。该步骤的最终结果是得到一个包含所有特征的 DataFrame 和一个包含要预测标签的 DataFrame。以下是使用一个基准数据集的示例（可以在这里找到 [UCI Irvine 机器学习库](https://archive.ics.uci.edu/ml/datasets/statlog+(australian+credit+approval))）。

处理数据

## 2\. 安装和导入 FSRLearning 库

第二步是通过 pip 安装库。以下是安装命令：

```py
pip install FSRLearning
```

导入库时，可以使用以下代码：

导入库

你将能够通过创建一个 Feature_Selector_RL 对象来创建一个特征选择器。需要填写一些参数。

+   ***feature_number***（整数）：数据框架 X 中的特征数量

+   ***feature_structure***（字典）：图形实现的字典

+   ***eps***（浮动 [0; 1]）：选择随机下一个状态的概率，0 表示完全贪婪算法，1 表示完全随机

+   ***alpha***（浮动 [0; 1]）：控制更新速率，0 表示几乎不更新，1 表示完全更新

+   ***gamma***（浮动 [0, 1]）：观察下一个状态的调节因子，0 表示目光短浅的情况，1 则表现出远见行为

+   ***nb_iter***（整数）：遍历图形的序列数量

+   ***starting_state***（“empty” 或 “random”）：如果为“empty”，算法从空状态开始；如果为“random”，算法从图中的随机状态开始

所有参数都可以调节，但对于大多数问题来说，只需要少量迭代就能得到良好的结果（大约 100 次），而 epsilon 值通常设为 0.2 就足够了。起始状态有助于更高效地浏览图形，但它可能非常依赖数据集，两个值都可以进行测试。

最后，我们可以通过以下代码非常简单地初始化选择器：

选择器对象初始化

在大多数机器学习库的基础上，训练算法非常简单：

这里是输出的一个示例：

![](img/6ca11e2f53ddc0befd561002a7b034fe.png)

选择器的输出（图片由作者提供）

输出是一个五元组，如下所示：

+   数据框架 X 中特征的索引（类似映射）

+   特征观察的次数

+   在所有迭代后，特征带来的奖励的平均值

+   特征从最不重要到最重要的排名（这里 2 是最不重要，7 是最重要的特征）

+   全局访问的状态数量

此选择器的另一个重要方法是与 Scikit-Learn 的 RFE 选择器进行比较。它的输入为 X、y 和选择器的结果。

输出是在每一步选择后，打印 RFE 和 FSRLearning 的全局指标。它还输出模型准确性的视觉比较，x 轴为选定特征的数量，y 轴为准确性。两条水平线分别为每种方法的准确性中位数。以下是一个示例：

![](img/b74a30d601f80f8b0a2970d80f8808a9.png)

RL 方法与 RFE 方法的比较（图片由作者提供）

```py
Average benchmark accuracy : 0.854251012145749, rl accuracy : 0.8674089068825909 
Median benchmark accuracy : 0.8552631578947368, rl accuracy : 0.868421052631579 
Probability to get a set of variable with a better metric than RFE : 1.0 
Area between the two curves : 0.17105263157894512
```

在这个示例中，RL 方法总是为模型提供比 RFE 更好的特征集。我们可以在排序后的特征集中选择任何子集，它将为模型提供更好的准确性。我们可以多次运行模型和比较器以获得非常准确的估计，但 RL 方法总是更好。

另一个有趣的方法是 `get_plot_ratio_exploration`。它绘制了一个图表，比较已访问节点与在精确迭代中访问的节点数。

![](img/eaa6224563da601d84a3e70911b0e8f0.png)

每次迭代中访问与未访问状态的比较（图片来自作者）

此外，由于第二个停止条件，算法的时间复杂度呈指数级下降。因此，即使特征数量很大，收敛也会迅速找到。下面的图展示了某一大小的特征集被访问的次数。

![](img/313c55b8da35476e077b1f932fa3529c.png)

根据状态大小绘制的访问状态数量（图片来自作者）

在所有迭代中，算法访问的状态包含不超过 6 个变量。超过 6 个变量时，我们可以看到访问的状态数量在减少。这是一个良好的行为，因为使用较小特征集训练模型比使用大特征集更快速。

# 结论与参考文献

总的来说，我们可以看到强化学习方法对于最大化模型的度量非常高效。它总是快速收敛到一个有趣的特征子集。此外，这种方法在 ML 项目中非常容易且快速实现，使用 FSRLearning 库即可。

该项目的 GitHub 仓库及完整文档可在 [这里](https://github.com/blefo/FSRLearning)获取。

如果你希望与我联系，可以直接在 linkedin [这里](https://www.linkedin.com/in/baptistelefort/)找到我。

本库的实现得到了以下两篇文章的帮助：

+   Sali Rasoul, Sodiq Adewole 和 Alphonse Akakpo，[使用强化学习进行特征选择](https://arxiv.org/pdf/2101.09460.pdf)（2021），ArXiv

+   Seyed Mehdin Hazrati Fard, Ali Hamzeh 和 Sattar Hashemi，[使用强化学习寻找最优特征集](https://www.sciencedirect.com/science/article/pii/S0898122113004495)（2013），ScienceDirect
