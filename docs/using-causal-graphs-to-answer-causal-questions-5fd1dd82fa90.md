# 使用因果图回答因果问题

> 原文：[https://towardsdatascience.com/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=collection_archive---------1-----------------------#2024-01-31](https://towardsdatascience.com/using-causal-graphs-to-answer-causal-questions-5fd1dd82fa90?source=collection_archive---------1-----------------------#2024-01-31)

## 因果 AI，探讨因果推理与机器学习的融合

[](https://medium.com/@raz1470?source=post_page---byline--5fd1dd82fa90--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--5fd1dd82fa90--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5fd1dd82fa90--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5fd1dd82fa90--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--5fd1dd82fa90--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5fd1dd82fa90--------------------------------) ·阅读时间 9分钟·2024年1月31日

--

![](../Images/de2d3dc8860ce785769383e679f20d22.png)

图片由[GuerrillaBuzz](https://unsplash.com/@guerrillabuzz?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 这系列文章讲的是什么？

欢迎来到我的《因果 AI》系列文章，在这里我们将探讨因果推理如何与机器学习模型融合。你将会看到在不同商业场景中应用的多个实际案例。

# **简介**

本文提供了因果图潜力的实践性介绍。

本文面向任何希望更好理解以下内容的人：

+   因果图是什么以及它们是如何工作的

+   一个通过 Python 展示如何构建因果图的案例研究

+   它们与机器学习的比较

+   主要挑战和未来的思考

完整的笔记本可以在这里找到：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/using%20causal%20graphs%20to%20answer%20causal%20questions.ipynb?source=post_page-----5fd1dd82fa90--------------------------------) [## causal_ai/notebooks/using causal graphs to answer causal questions.ipynb 在主分支 ·…

### 本项目介绍了因果 AI 及其如何推动商业价值。- causal_ai/notebooks/using causal graphs to…

[github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/using%20causal%20graphs%20to%20answer%20causal%20questions.ipynb?source=post_page-----5fd1dd82fa90--------------------------------)

# **什么是因果图？**

因果图帮助我们区分因果关系与相关性。它们是因果推理/因果机器学习/因果 AI 工具箱的关键部分，可用于回答因果问题。

因果图通常被称为DAG（有向无环图），它包含节点和边——边连接的是具有因果关系的节点。

确定因果图的方式有两种：

+   专家领域知识

+   因果发现算法

目前，我们假设我们有专家领域知识来确定因果图（我们将在后续讲解因果发现算法）。

# **为什么我们不能仅仅使用机器学习？**

机器学习的目标是尽可能准确地分类或预测，前提是有一些训练数据。机器学习算法没有动力确保它使用的特征与目标之间具有因果关系。也无法保证每个特征的方向（正向/负向效应）和强度与真实的数据生成过程相符。机器学习不会考虑以下情况：

+   假相关——当两个变量有一个共同原因时，它们会有一个假相关，例如：高温增加冰淇淋销量和鲨鱼袭击的数量。

+   混杂因子——一个变量同时影响你的处理和结果，例如：需求影响我们在营销上花费的金额以及新客户的注册数量。

+   冲突变量——一个变量受到两个独立变量的影响，例如：客户关怀质量 -> 用户满意度 <- 公司规模

+   中介变量——两个变量通过一个中介变量（间接）关联，例如：规律性运动 -> 心血管健康（中介变量） -> 总体健康

由于这些复杂性以及机器学习的黑箱性质，我们无法确信它能够回答因果问题。

# **结构因果模型**

给定已知的因果图和观察数据，我们可以训练一个结构因果模型（SCM）。一个SCM可以看作是一系列因果模型，每个节点一个模型。每个模型使用一个节点作为目标，并使用其直接父节点作为特征。如果我们观察数据中的关系是线性的，SCM将是一个线性方程组。这可以通过一系列线性回归模型来建模。如果我们观察数据中的关系是非线性的，可以通过一系列增强树来建模。

与传统机器学习的关键区别在于，SCM建模的是因果关系，并考虑了假相关、混杂因子、冲突变量和中介变量。

通常为每个非根节点使用加性噪声模型（ANM）（意味着它至少有一个父节点）。这使我们能够使用一系列机器学习算法（加上噪声项）来估计每个非根节点。

Y := f(X) + N

根节点可以使用随机模型来建模，以描述其分布。

结构因果模型（SCM）可以视为一个生成模型，它能够生成新的数据样本——这使得它能够回答一系列因果问题。它通过从根节点采样数据并通过图传播数据来生成新数据。

# **回答因果问题**

结构因果模型的价值在于，它通过计算反事实和模拟干预来回答因果问题：

+   反事实：使用历史观测数据计算如果我们改变了x，y会发生什么。例如，如果我们上个月将呼叫等待时间减少20%，客户流失数量会有什么变化？

+   干预：与反事实非常相似（且常常互换使用），但干预模拟的是未来可能发生的情况。例如，如果明年我们将呼叫等待时间减少20%，客户流失数量会有什么变化？

# **呼叫中心案例研究**

客户服务团队监控多个关键绩效指标（KPI）。其中之一是呼叫等待时间。增加呼叫中心员工数量将减少呼叫等待时间。

但减少呼叫等待时间会如何影响客户流失率？这是否足以抵消增加呼叫中心员工的成本？

数据科学团队被要求构建并评估业务案例。

# **确定因果图**

关注的群体是拨打进线电话的客户。以下是每天收集的时间序列数据：

![](../Images/ea95f2a517a5ea6c23b1cc89cb028263.png)

图片由作者提供

在这个例子中，我们使用时间序列数据，但因果图也可以与客户级别的数据一起使用。

在这个例子中，我们利用专家领域知识来确定因果图。

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
```

![](../Images/7f87d73da2c5be5e5e94137445c52f12.png)

图片由作者提供

# **数据生成过程**

接下来，我们需要为案例研究生成数据。

我们想要生成一些数据，以便比较使用因果图与机器学习（为了简单起见，使用岭回归）计算反事实的结果。

正如我们在上一节中确定的因果图，我们可以利用这些知识来创建数据生成过程。

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
```

```py
# Generate data
np.random.seed(999)
df = data_generator(max_call_waiting=600, inbound_calls=10000, call_reduction=1.00)

sns.pairplot(df)
```

![](../Images/9922931040e9b9c7d7029dbe255b01bf.png)

图片由作者提供

# **训练SCM**

现在我们有了表示因果图的邻接矩阵和一些数据。我们使用来自dowhy Python包的gcm模块来训练SCM。

考虑选择何种因果机制用于根节点和非根节点是非常重要的。如果你查看我们的数据生成函数，你会看到所有的关系都是线性的。因此，选择岭回归应该足够了。

```py
# Setup graph
graph = nx.from_numpy_array(graph_actual, create_using=nx.DiGraph)
graph = nx.relabel_nodes(graph, node_lookup)

# Create SCM
causal_model = gcm.InvertibleStructuralCausalModel(graph)
causal_model.set_causal_mechanism('Demand', gcm.EmpiricalDistribution()) # Root node
causal_model.set_causal_mechanism('Call waiting time', gcm.AdditiveNoiseModel(gcm.ml.create_ridge_regressor())) # Non-root node
causal_model.set_causal_mechanism('Call abandoned', gcm.AdditiveNoiseModel(gcm.ml.create_ridge_regressor())) # Non-root node
causal_model.set_causal_mechanism('Reported problems', gcm.AdditiveNoiseModel(gcm.ml.create_ridge_regressor())) # Non-root node
causal_model.set_causal_mechanism('Discount sent', gcm.AdditiveNoiseModel(gcm.ml.create_ridge_regressor())) # Non-root 
causal_model.set_causal_mechanism('Churn', gcm.AdditiveNoiseModel(gcm.ml.create_ridge_regressor())) # Non-root 
gcm.fit(causal_model, df)
```

你也可以使用自动分配功能，自动分配因果机制，而不是手动分配。

关于gcm包的更多信息，请参阅文档：

[## DoWhy文档 - DoWhy文档](https://www.pywhy.org/dowhy/v0.11.1/?source=post_page-----5fd1dd82fa90--------------------------------)

### 日期：2023年12月25日 版本：v0.11.1 相关资源：源代码仓库 | 问题与建议 | PyWhy组织 | DoWhy…

[www.pywhy.org](https://www.pywhy.org/dowhy/v0.11.1/?source=post_page-----5fd1dd82fa90--------------------------------)

# **岭回归比较**

我们还使用岭回归来帮助创建基准比较。我们可以回顾数据生成器，看到它正确估算了每个变量的系数。然而，除了直接影响客户流失外，通话等待时间还通过弃单、报告问题和发送折扣间接影响客户流失。

当涉及到估计反事实时，看看SCM与岭回归的比较将会很有趣。

```py
# Ridge regression
y = df['Churn'].copy()
X = df.iloc[:, 1:-1].copy()
model = RidgeCV()
model = model.fit(X, y)
y_pred = model.predict(X)

print(f'Intercept: {model.intercept_}')
print(f'Coefficient: {model.coef_}')
# Ground truth[0.10 0.30 0.15 -0.20]
```

![](../Images/8bdef4723a46a3c0aaec5a3a2a4d6f89.png)

图片来源：作者

# **估计真实情况**

在我们继续使用因果图和岭回归计算反事实之前，我们需要一个真实情况基准。我们可以使用数据生成器，在将通话等待时间减少20%之后，创建反事实样本。

我们无法在现实世界问题中做到这一点，但这种方法允许我们评估因果图和岭回归的有效性。

```py
# Set call reduction to 20%
reduce = 0.20
call_reduction = 1 - reduce

# Generate counterfactual data
np.random.seed(999)
df_cf = data_generator(max_call_waiting=600, inbound_calls=10000, call_reduction=call_reduction)
```

# **估计反事实**

我们现在可以使用我们的三种方法估计如果我们将通话等待时间减少20%会发生什么：

+   真实情况（来自数据生成器）

+   岭回归

+   因果图

我们看到岭回归显著低估了对客户流失的影响，而因果图与真实情况非常接近。

```py
# Ground truth counterfactual
ground_truth = round((df['Churn'].sum() - df_cf['Churn'].sum()) / df['Churn'].sum(), 2)

# Causal graph counterfactual
df_counterfactual = gcm.counterfactual_samples(causal_model, {'Call waiting time': lambda x: x*call_reduction}, observed_data=df)
causal_graph = round((df['Churn'].sum() - df_counterfactual['Churn'].sum()) / (df['Churn'].sum()), 3)

# Ridge regression counterfactual
ridge_regression = round((df['Call waiting time'].sum() * 1.0 * model.coef_[0] - (df['Call waiting time'].sum() * call_reduction * model.coef_[0])) / (df['Churn'].sum()), 3)
```

![](../Images/0da8f6eb1bd4a932cc8e7626cb4e0046.png)

图片来源：作者

# **未来的考虑**

这是一个简单的例子，旨在让你思考因果图的强大作用。

对于更复杂的情况，有几个挑战需要考虑：

+   假设了哪些前提条件，违反这些前提条件会有什么影响？

+   如果我们没有专家领域的知识来识别因果图怎么办？

+   如果存在非线性关系会怎样？

+   多重共线性有多严重？

+   如果某些变量具有滞后效应，会怎样？

+   我们如何处理高维数据集（大量变量）？

所有这些内容将在未来的博客中覆盖。

# **资源**

如果你有兴趣进一步了解因果AI，我强烈推荐以下资源：

[](https://www.packtpub.com/product/causal-inference-and-discovery-in-python/9781804612989?source=post_page-----5fd1dd82fa90--------------------------------) [## Python中的因果推断与发现 | Packt

### 通过揭示因果原理并将其与强大的机器学习方法结合，解开因果推断与因果发现的神秘面纱…

www.packtpub.com](https://www.packtpub.com/product/causal-inference-and-discovery-in-python/9781804612989?source=post_page-----5fd1dd82fa90--------------------------------) [](https://www.oreilly.com/library/view/causal-inference-in/9781098140243/?source=post_page-----5fd1dd82fa90--------------------------------) [## Python中的因果推断

### 在线营销增加一美元会带来多少买家？哪些客户只有在提供某些条件时才会购买…

[www.oreilly.com](https://www.oreilly.com/library/view/causal-inference-in/9781098140243/?source=post_page-----5fd1dd82fa90--------------------------------)

# ChatGPT对作者有什么评价呢？😊

“认识一下Ryan，一位经验丰富的首席数据科学家，专注于在商业环境中运用因果分析技术，涵盖营销、运营和客户服务等领域。他的专长在于揭示因果关系的复杂性，从而推动明智的决策和战略改进，服务于不同的组织职能。”

如果你想继续探索因果人工智能，请关注我——在下一篇文章中，我们将回顾一步，深入探讨如何在缺乏专家领域知识的情况下，利用因果发现来估计因果图。
