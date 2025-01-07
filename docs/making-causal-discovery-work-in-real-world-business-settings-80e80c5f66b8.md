# 使因果发现能够在现实世界的商业环境中应用

> 原文：[https://towardsdatascience.com/making-causal-discovery-work-in-real-world-business-settings-80e80c5f66b8?source=collection_archive---------1-----------------------#2024-03-04](https://towardsdatascience.com/making-causal-discovery-work-in-real-world-business-settings-80e80c5f66b8?source=collection_archive---------1-----------------------#2024-03-04)

## 因果 AI，探索因果推理与机器学习的结合

[](https://medium.com/@raz1470?source=post_page---byline--80e80c5f66b8--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--80e80c5f66b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--80e80c5f66b8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--80e80c5f66b8--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--80e80c5f66b8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--80e80c5f66b8--------------------------------) ·阅读时长10分钟·2024年3月4日

--

![](../Images/f05a4aa4db90cf22a2acbe769fe06153.png)

照片由[N.](https://unsplash.com/@ellladee?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 这篇文章系列讲的是什么？

欢迎来到我的因果 AI 系列文章，在这里我们将探索因果推理如何融入机器学习模型。期待在不同的商业场景中探索多个实际应用。

在第一篇文章中，我们探索了*使用因果图来回答因果问题*。这一次，我们将深入探讨*如何在实际商业环境中实现因果发现*。

如果你错过了关于因果图的第一篇文章，可以在这里查看：

[](https://medium.com/@raz1470/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=post_page-----80e80c5f66b8--------------------------------) [## 使用因果图回答因果问题

### 本文为因果图的潜力提供了一个实际的介绍。

medium.com](https://medium.com/@raz1470/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=post_page-----80e80c5f66b8--------------------------------)

# **引言**

本文旨在帮助你导航因果发现的世界。

**适合任何想了解以下内容的人：**

+   介绍因果发现是什么，包括它所做的假设。

+   深入探讨条件独立性测试，这是因果发现的基础。

+   PC 算法概述，这是一个流行的因果发现算法。

+   一个使用 Python 实现的案例研究，展示如何应用 PC 算法。

+   关于如何在现实世界商业环境中使因果发现发挥作用的指南。

完整的笔记本可以在这里找到：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/making%20causal%20discovery%20work%20in%20real%20world%20business%20settings.ipynb?source=post_page-----80e80c5f66b8--------------------------------) [## causal_ai/notebooks/making causal discovery work in real world business settings.ipynb 位于 main ·…

### 本项目介绍了因果 AI 及其如何驱动商业价值。- causal_ai/notebooks/making causal discovery…

github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/making%20causal%20discovery%20work%20in%20real%20world%20business%20settings.ipynb?source=post_page-----80e80c5f66b8--------------------------------)

# 什么是因果发现？

在我上一篇文章中，我讲解了如何使用因果图来回答因果问题。

通常称为 DAG（有向无环图），因果图包含节点和边 — 边连接因果相关的节点。

确定因果图有两种方法：

+   专家领域知识

+   因果发现算法

我们并不总是具备专家领域知识来确定因果图。在这本笔记本中，我们将探讨如何利用观察数据，通过因果发现算法来确定因果图。

# 算法

因果发现是学术界一个广泛研究的领域，提出了四组方法：

目前的研究尚不清楚哪种方法最好。回答这个问题的一个挑战是缺乏现实的基准数据集来验证真相。

![](../Images/3a67ca0bfe5871399e38649c64eb6e00.png)

图片由作者提供

在这篇博客中，我们将专注于理解 PC 算法，这是一种基于约束的方法，使用条件独立性测试。

# 假设

在我们介绍 PC 算法之前，让我们先了解因果发现算法所做的关键假设：

1.  **因果马尔可夫条件：** 每个变量在给定其直接原因的情况下，与其非后代条件独立。这告诉我们，如果我们知道一个变量的原因，了解那些未直接受这些原因影响的变量不会提供任何额外的信息。这个基本假设简化了因果关系的建模，使我们能够进行因果推理。

1.  **因果忠实性：** 如果数据中保持统计独立性，那么对应变量之间就没有直接的因果关系。检验这个假设具有挑战性，违反这一假设可能表明模型不准确或缺少变量。

1.  **因果充分性：** 所包括的变量是否足以对感兴趣的变量做出因果声明？换句话说，我们需要观察到所有可能的混杂因素。检验这一假设涉及敏感性分析，用来评估潜在未观测混杂因素的影响。

1.  **无环性：** 图中没有循环。

在实践中，虽然这些假设对因果发现是必要的，但它们通常被视为假设，而不是直接进行检验。

即使做出了这些假设，我们仍然可能得到一个Markov等价类。当我们有多个因果图，每个图的可能性相同，就形成了Markov等价类。

# 条件独立性检验

条件独立性检验是因果发现的构建块，并被PC算法使用（我们稍后会讲解该算法）。

让我们先理解独立性。两个变量的独立性意味着，知道一个变量的值不会提供关于另一个变量值的任何信息。在这种情况下，可以较为安全地假设它们之间没有直接的因果关系。然而，如果两个变量不独立，那么盲目地假设因果关系就是错误的。

条件独立性检验可以用来判断在给定一个或多个其他变量的情况下，两个变量是否相互独立。如果两个变量是条件独立的，那么我们可以推断它们没有因果关系。

Fisher精确检验可以用来判断在控制了一个或多个额外变量的影响后，两个变量之间是否存在显著的关联（使用额外的变量将数据分割成子集，然后对每个子集应用检验）。零假设假定这两个感兴趣的变量之间没有关联。然后可以计算p值，如果p值低于0.05，则拒绝零假设，表明这两个变量之间存在显著关联。

# 识别虚假相关性

我们可以用一个虚假相关性的例子来说明如何使用条件独立性检验。

两个变量之间存在虚假相关性，当它们有一个共同的原因时，例如高温导致冰淇淋销量和鲨鱼攻击的增加。

```py
np.random.seed(999)

# Create dataset with spurious correlation
temperature = np.random.normal(loc=0, scale=1, size=1000)
ice_cream_sales = 2.5 * temperature + np.random.normal(loc=0, scale=1, size=1000)
shark_attacks = 0.5 * temperature + np.random.normal(loc=0, scale=1, size=1000)
df_spurious = pd.DataFrame(data=dict(temperature=temperature, ice_cream_sales=ice_cream_sales, shark_attacks=shark_attacks))

# Pairplot
sns.pairplot(df_spurious, corner=True)
```

![](../Images/f544381e6bb7d26d0958df2b2aee2515.png)

图片来源：作者

```py
# Create node lookup variables
node_lookup = {0: 'Temperature',
               1: 'Ice cream sales',
               2: 'Shark attacks'                                                                          
 }

total_nodes = len(node_lookup)

# Create adjacency matrix - this is the base for our graph
graph_actual = np.zeros((total_nodes, total_nodes))

# Create graph using expert domain knowledge
graph_actual[0, 1] = 1.0 # Temperature -> Ice cream sales
graph_actual[0, 2] = 1.0 # Temperature -> Shark attacks

plot_graph(input_graph=graph_actual, node_lookup=node_lookup)
```

![](../Images/8012d363828cd7ea22e2d474b5c29f9b.png)

图片来源：作者

以下条件独立性检验可用于确定因果图：

```py
# Run first conditional independence test
test_id_1 = round(gcm.independence_test(ice_cream_sales, shark_attacks, conditioned_on=temperature), 2)

# Run second conditional independence test
test_id_2 = round(gcm.independence_test(ice_cream_sales, temperature, conditioned_on=shark_attacks), 2)

# Run third conditional independence test
test_id_3 = round(gcm.independence_test(shark_attacks, temperature, conditioned_on=ice_cream_sales), 2)
```

![](../Images/a85b275df7ee00a835fef623cdb5687f.png)

图片来源：作者

尽管我们不知道关系的方向，但我们可以正确推断温度与冰淇淋销量和鲨鱼攻击之间存在因果关系。

# PC算法

PC算法（以其发明者Peter和Clark命名）是一种基于约束的因果发现算法，它使用条件独立性检验。

它可以总结为两个主要步骤：

1.  它从一个完全连接的图开始，然后使用条件独立性检验来去除边缘并识别无向因果图（节点之间有连接但没有方向）。

1.  然后，它通过各种方向性技巧（部分）地确定边的方向。

我们可以用之前的虚假相关性示例来说明第一步：

+   从一个完全连接的图开始

+   测试 ID 1：接受原假设并删除边，冰激凌销量与鲨鱼攻击之间不存在因果关系

+   测试 ID 2：拒绝原假设并保留边，冰激凌销量与温度之间存在因果关系

+   测试 ID 3：拒绝原假设并保留边，鲨鱼攻击与冰激凌销量之间存在因果关系

# 评估结果

因果发现中的一个关键挑战是评估结果。如果我们知道因果图，就不需要应用因果发现算法了！然而，我们可以创建合成数据集来评估因果发现算法的表现。

有几种评估因果发现算法的指标：

![](../Images/f5865fe263ed76a07bffd2b393348ee6.png)

图片来源：作者

+   *真正正例：正确识别因果关系*

+   *假正例：错误识别因果关系*

+   *真正负例：正确识别不存在因果关系*

+   *假负例：错误识别不存在因果关系*

+   *反向边：正确识别因果关系，但方向错误*

我们希望得到高数量的真正正例，但这不应以高数量的假正例为代价（因为在构建 SCM 时，错误的因果关系可能会造成很大损害）。因此，GScore 似乎能够很好地捕捉这一点，同时给出一个介于 0 和 1 之间的可解释比率。

# 呼叫中心案例研究

我们将重新审视我之前文章中的呼叫中心案例研究。首先，我们确定因果图（作为真实值使用），然后利用我们对数据生成过程的了解来创建一些样本。

真实的因果图和生成的样本将使我们能够评估 PC 算法。

```py
# Create node lookup for channels
node_lookup = {0: 'Demand',
               1: 'Call waiting time',
               2: 'Call abandoned', 
               3: 'Reported problems',                   
               4: 'Discount sent',
               5: 'Churn'                                                                             
 }

total_nodes = len(node_lookup)

# Create adjacency matrix - this is the base for our graph
graph_actual = np.zeros((total_nodes, total_nodes))

# Create graph using expert domain knowledge
graph_actual[0, 1] = 1.0 # Demand -> Call waiting time
graph_actual[0, 2] = 1.0 # Demand -> Call abandoned
graph_actual[0, 3] = 1.0 # Demand -> Reported problems
graph_actual[1, 2] = 1.0 # Call waiting time -> Call abandoned
graph_actual[1, 5] = 1.0 # Call waiting time -> Churn
graph_actual[2, 3] = 1.0 # Call abandoned -> Reported problems
graph_actual[2, 5] = 1.0 # Call abandoned -> Churn
graph_actual[3, 4] = 1.0 # Reported problems -> Discount sent
graph_actual[3, 5] = 1.0 # Reported problems -> Churn
graph_actual[4, 5] = 1.0 # Discount sent -> Churn

plot_graph(input_graph=graph_actual, node_lookup=node_lookup)
```

![](../Images/7f87d73da2c5be5e5e94137445c52f12.png)

图片来源：作者

```py
def data_generator(max_call_waiting, inbound_calls, call_reduction):
    '''
     A data generating function that has the flexibility to reduce the value of node 0 (Call waiting time) - this enables us to calculate ground truth counterfactuals

    Args:
        max_call_waiting (int): Maximum call waiting time in seconds
        inbound_calls (int): Total number of inbound calls (observations in data)
        call_reduction (float): Reduction to apply to call waiting time

    Returns:
        DataFrame: Generated data
    '''

    df = pd.DataFrame(columns=node_lookup.values())

    df[node_lookup[0]] = np.random.randint(low=10, high=max_call_waiting, size=(inbound_calls)) # Demand
    df[node_lookup[1]] = (df[node_lookup[0]] * 0.5) * (call_reduction) + np.random.normal(loc=0, scale=40, size=inbound_calls) # Call waiting time
    df[node_lookup[2]] = (df[node_lookup[1]] * 0.5) + (df[node_lookup[0]] * 0.2) + np.random.normal(loc=0, scale=30, size=inbound_calls) # Call abandoned
    df[node_lookup[3]] = (df[node_lookup[2]] * 0.6) + (df[node_lookup[0]] * 0.3) + np.random.normal(loc=0, scale=20, size=inbound_calls) # Reported problems
    df[node_lookup[4]] = (df[node_lookup[3]] * 0.7) + np.random.normal(loc=0, scale=10, size=inbound_calls) # Discount sent
    df[node_lookup[5]] = (0.10 * df[node_lookup[1]] ) + (0.30 * df[node_lookup[2]]) + (0.15 * df[node_lookup[3]]) + (-0.20 * df[node_lookup[4]]) # Churn

    return df

# Generate data
np.random.seed(999)
df = data_generator(max_call_waiting=600, inbound_calls=10000, call_reduction=1.00)

# Pairplot
sns.pairplot(df, corner=True)
```

![](../Images/c1a355767929b4851297ba48e8f2fb1d.png)

图片来源：作者

# 应用 PC 算法

Python 包 gCastle 实现了几种因果发现算法，包括 PC 算法：

[](https://github.com/huawei-noah/trustworthyAI/tree/master/gcastle?source=post_page-----80e80c5f66b8--------------------------------) [## trustworthyAI/gcastle at master · huawei-noah/trustworthyAI

### 与可信 AI 相关的项目。通过在 GitHub 上创建帐户来为 huawei-noah/trustworthyAI 的开发做出贡献。

[github.com](https://github.com/huawei-noah/trustworthyAI/tree/master/gcastle?source=post_page-----80e80c5f66b8--------------------------------)

当我们将样本输入算法时，返回的是学习到的因果图（以邻接矩阵的形式）。

```py
# Apply PC method to learn graph
pc = PC(variant='stable')
pc.learn(df)
graph_pred = pc.causal_matrix

graph_pred
```

![](../Images/05a6f4f3f1d28011a50f0e7f11811d18.png)

图片来源：作者

gCastle 还提供了几种评估指标，包括 GScore。我们学习到的图的 GScore 是 0！为什么它这么差？

```py
# GScore
metrics = MetricsDAG(
    B_est=graph_pred, 
    B_true=graph_actual)
metrics.metrics['gscore']
```

![](../Images/cff9208e806a15803a1eb51b797ab5d9.png)

图片来源：作者

经过仔细检查学习到的图，我们可以看到它正确地识别了无向图，但在定向边时遇到了困难。

```py
plot_graph(input_graph=graph_pred, node_lookup=node_lookup)
```

![](../Images/fab02cea93344a2bae808a12a80c12e1.png)

图片由作者提供

# 评估无向图

在应用 PC 算法的学习成果基础上，我们可以使用 gCastle 提取学习到的无向因果图。

```py
# Apply PC method to learn skeleton
skeleton_pred, sep_set = find_skeleton(df.to_numpy(), 0.05, 'fisherz')

skeleton_pred
```

![](../Images/c062b2429c96d525e3003542879584f0.png)

图片由作者提供

如果我们将我们的真实图转换为无向邻接矩阵，我们就可以用它来计算无向图的Gscore。

```py
# Transform the ground truth graph into an undirected adjacency matrix
skeleton_actual = graph_actual + graph_actual.T
skeleton_actual = np.where(skeleton_actual > 0, 1, 0)
```

使用学习到的无向因果图，我们得到了一个 GScore 为 1.00。

```py
# GScore
metrics = MetricsDAG(
    B_est=skeleton_pred, 
    B_true=skeleton_actual)
metrics.metrics['gscore']
```

![](../Images/d21aa0f78e089638117ec5e5ef87362a.png)

图片由作者提供

我们已经准确地学习到了一个无向图——我们能否利用专家领域知识来指导边的方向？这个问题的答案在不同的应用场景中会有所不同，但它是一个合理的策略。

```py
plot_graph(input_graph=skeleton_pred, node_lookup=node_lookup)
```

![](../Images/8f7cc6d5bee46dbbbec62b82a6aa8c2f.png)

图片由作者提供

# 在现实商业环境中使因果发现发挥作用

**我们需要开始将因果发现视为任何因果推断项目中的一个关键 EDA 步骤：**

+   *然而，我们也需要对其局限性保持透明。*

+   *因果发现是一个需要结合专家领域知识的工具。*

**在假设上要务实：**

+   *我们是否能期望观察到所有的混杂因素？可能不行。然而，通过正确的领域知识和广泛的数据收集，我们有可能观察到所有关键的混杂因素。*

**选择一个我们可以应用约束来结合专家领域知识的算法 — gCastle 允许我们在 PC 算法中应用约束：**

+   *最初的工作是识别无向因果图，然后将这个输出分享给领域专家，并利用他们的帮助来指导图的方向。*

**使用代理变量时要小心，并考虑对我们强烈认为存在的关系强加约束：**

+   *例如，如果将 Google 趋势数据作为产品需求的代理变量，我们可能需要对这种数据对销售的驱动作用施加约束。*

# 未来的工作

+   如果我们有非线性关系怎么办？PC 算法能处理这种情况吗？

+   如果我们有未观察到的混杂因素，会发生什么？FCI 算法能有效处理这种情况吗？

+   基于约束、基于得分、基于函数和基于梯度的方法如何比较？

如果你想继续这段因果 AI 之旅，请关注我 —— 在下一篇文章中，我们将了解双重机器学习如何帮助消除治疗效应的偏差。
