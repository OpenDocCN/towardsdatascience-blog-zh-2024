# 偏差-方差权衡解析：为初学者提供的带有代码示例的视觉指南

> 原文：[https://towardsdatascience.com/bias-variance-tradeoff-explained-a-visual-guide-with-code-examples-for-beginners-9521871f728a?source=collection_archive---------4-----------------------#2024-11-25](https://towardsdatascience.com/bias-variance-tradeoff-explained-a-visual-guide-with-code-examples-for-beginners-9521871f728a?source=collection_archive---------4-----------------------#2024-11-25)

## 模型评估与优化

## 欠拟合与过拟合如何在你的模型上“斗争”

[](https://medium.com/@samybaladram?source=post_page---byline--9521871f728a--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--9521871f728a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9521871f728a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9521871f728a--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--9521871f728a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9521871f728a--------------------------------) ·阅读时长 20 分钟·2024年11月25日

--

每当有人构建预测模型时，他们都会面临这些经典问题：欠拟合和过拟合。模型不能太简单，但也不能过于复杂。这两者之间的互动被称为偏差-方差权衡，它影响着所有预测模型。

关于“偏差-方差权衡”这一主题的问题是，每当你尝试在线查找这些术语时，你会发现很多文章展示了完美的图表曲线。是的，它们解释了基本概念——但它们忽略了一个重要的点：它们过于关注理论，而不够关注现实世界中的问题，且很少展示在处理实际数据时会发生什么。

在这里，我们将不使用理论示例，而是使用一个真实数据集并构建实际的模型。一步一步地，我们将确切地看到模型是如何失败的，欠拟合和过拟合在实践中是怎样表现的，以及为什么找到正确的平衡如此重要。让我们停止偏差和方差之间的斗争，找到一个公平的中间地带。

![](../Images/bf28840b0f2c53d90e3c33fda840385f.png)

所有视觉效果：作者使用Canva Pro创建。已优化为移动端显示，可能在桌面端显示过大。

# 什么是偏差-方差权衡？

在我们开始之前，为了避免混淆，让我们澄清一下在这里机器学习中使用的“**偏差**”与“**方差**”这两个术语。这些词在数学和数据科学的许多领域中的使用方式是不同的。

偏差可以有多种含义。[在统计学中](https://en.wikipedia.org/wiki/Bias_(statistics))，它表示我们的计算与真实答案之间的偏离程度，而[在数据科学中](https://en.wikipedia.org/wiki/Selection_bias)，它可以指对某些群体的不公平对待。即使是在机器学习的另一部分，[在神经网络中](https://www.turing.com/kb/necessity-of-bias-in-neural-networks)，它是一个帮助网络学习的特殊数字。

方差也有不同的含义。 [在统计学中](https://en.wikipedia.org/wiki/Variance)，它告诉我们数字与其平均值之间的分散程度，而[在科学实验中](https://www.creative-wisdom.com/teaching/WBI/variance_control.shtml)，它表示每次我们重复实验时结果的变化程度。

但在机器学习的“偏差-方差权衡”中，这些词语有特殊的含义。

**偏差**指的是模型学习模式的能力。当我们说一个模型有高偏差时，我们的意思是它太简单了，并且不断重复同样的错误。

**方差**在这里指的是当你给模型不同的训练数据时，它的答案会发生多大变化。当我们说方差很高时，我们的意思是模型在看到新数据时，其答案变化过大。

“**偏差-方差权衡**”并不是我们可以通过数字精确测量的东西。相反，它帮助我们理解我们的模型是如何工作的：如果一个模型有很高的偏差，它在训练数据和测试数据上的表现都不好；而如果一个模型有很高的方差，它在训练数据上表现很好，但在测试数据上表现较差。

这帮助我们修复模型在表现不佳时的问题。让我们设置我们的任务和数据集，看看如何应用这个概念。

# ⛳️ 设置我们的任务

## 训练与测试数据集

假设你拥有一个高尔夫球场，现在你试图预测某一天有多少球员会到场。你已经收集了关于天气的数据：从一般的天气概况到温度和湿度的详细信息。你想利用这些天气条件来预测将会有多少球员到来。

![](../Images/62b6715e07ff01c02029294d84cd18f9.png)

列：‘天气概况（晴天、阴天、雨天）’，‘温度’（华氏度），‘湿度’（百分比），‘风力’（是/否）和‘球员人数’（目标特征）

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

# Data preparation
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'overcast', 'sunny', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'rain',
                'sunny', 'overcast', 'rain', 'sunny', 'rain', 'overcast', 'sunny', 'rain', 'overcast', 'sunny', 'overcast', 'rain', 'sunny', 'rain'],
    'Temp.': [92.0, 78.0, 75.0, 70.0, 62.0, 68.0, 85.0, 73.0, 65.0, 88.0, 76.0, 63.0, 83.0, 66.0,
              91.0, 77.0, 64.0, 79.0, 61.0, 72.0, 86.0, 67.0, 74.0, 89.0, 75.0, 65.0, 82.0, 63.0],
    'Humid.': [95.0, 65.0, 82.0, 90.0, 75.0, 70.0, 88.0, 78.0, 95.0, 72.0, 80.0, 85.0, 68.0, 92.0,
               93.0, 80.0, 88.0, 70.0, 78.0, 75.0, 85.0, 92.0, 77.0, 68.0, 83.0, 90.0, 65.0, 87.0],
    'Wind': [False, False, False, True, False, False, False, True, False, False, True, True, False, True,
             True, True, False, False, True, False, True, True, False, False, True, False, False, True],
    'Num_Players': [25, 85, 80, 30, 17, 82, 45, 78, 32, 65, 70, 20, 87, 24,
                   28, 68, 35, 75, 25, 72, 55, 32, 70, 80, 65, 24, 85, 25]
}

# Data preprocessing
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)
```

这听起来很简单，但有一个问题。我们只有28天的数据——这不多！为了让事情变得更复杂，我们需要将这些数据分为两部分：14天的数据用来帮助我们的模型学习（我们称之为训练数据），而剩下的14天用来测试我们的模型是否有效（测试数据）。

![](../Images/385ccc5503447fe191d0d6c2e3a5fc50.png)

前14个数据集将用于训练模型，而最后14个将用于测试模型。

```py
# Split features and target
X, y = df.drop('Num_Players', axis=1), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)
```

想一想这有多难。天气条件的组合有很多种。它可以是阳光明媚且潮湿、阳光明媚且凉爽、下雨且有风、阴天且凉爽，或其他组合。只有 14 天的训练数据，我们肯定无法看到每一种可能的天气组合。但我们的模型仍然需要对它可能遇到的任何天气条件做出准确预测。

这就是我们挑战的开始。如果我们让模型过于简单——比如只关注温度——它将忽略像风和雨这样的重要细节。这样是不够的。但如果我们让模型过于复杂——试图考虑每一个微小的天气变化——它可能会认为在一个多雨的周里，某个随机的安静日子意味着雨水实际上带来了更多的玩家。只有 14 个训练样本时，模型很容易变得混淆。

这里有个问题：与许多你在网上看到的例子不同，我们的数据并不完美。有些日子天气相似，但玩家数量不同。也许那天有本地活动，或者那天是节假日——但我们的天气数据无法告诉我们这些。这正是现实世界预测问题的复杂性所在。

所以，在我们开始构建模型之前，先花点时间了解我们正在尝试做的事情：

> 使用仅有的 14 个例子来创建一个可以预测任何天气条件下玩家数量的模型，即使是它之前没见过的天气条件。

这就是使得偏差-方差权衡如此重要的问题。

## 模型复杂度

对于我们的预测，我们将使用深度不同的决策树回归器（如果你想了解这个是如何工作的，可以查看我关于[决策树基础](https://example.org/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef)的文章）。对我们讨论来说，重要的是我们让这个模型变得多复杂。

![](../Images/bf5db4005f606e72d8e76bceb6b6a41b.png)

我们将使用整个训练数据集来训练决策树。树的深度首先设置，以防止树生长到某一深度。

```py
from sklearn.tree import DecisionTreeRegressor

# Define constants
RANDOM_STATE = 3 # As regression tree can be sensitive, setting this parameter assures that we always get the same tree
MAX_DEPTH = 5

# Initialize models
trees = {depth: DecisionTreeRegressor(max_depth=depth, random_state=RANDOM_STATE).fit(X_train, y_train) 
         for depth in range(1, MAX_DEPTH + 1)}
```

我们将通过控制模型的深度来控制模型的复杂度——从深度 1（最简单）到深度 5（最复杂）。

![](../Images/a67bdc7fd280e92d694e0663903f4123.png)

```py
 import matplotlib.pyplot as plt
from sklearn.tree import plot_tree

# Plot trees
for depth in range(1, MAX_DEPTH + 1):
    plt.figure(figsize=(12, 0.5*depth+1.5), dpi=300)
    plot_tree(trees[depth], feature_names=X_train.columns.tolist(), 
              filled=True, rounded=True, impurity=False, precision=1, fontsize=8)
    plt.title(f'Depth {depth}')
    plt.show()
```

![](../Images/01f44ef4c0388791ef9aacbfd5b7082c.png)![](../Images/8fa8694a1819b9ef1a4b11f46a4ab28d.png)![](../Images/7a3031d067133f5aee51d4887838a3fc.png)![](../Images/708ba80f3954ee07c52c534e06007255.png)![](../Images/9d4c754e23142f5685ea75c0ef9cc311.png)

为什么这些复杂度级别很重要：

+   深度 1：极其简单 — 只创建几种不同的预测

+   深度 2：稍微灵活一些 — 可以创建更多样化的预测

+   深度 3：适度复杂度 — 接近过多规则

+   深度 4–5：最高复杂度 — 每个训练样本几乎有一个规则

发现了什么有趣的事情吗？我们最复杂的模型（深度5）几乎为每一个训练示例创建了不同的预测规则。当一个模型开始为几乎每个训练示例都生成独特的规则时，这是一个明确的信号，说明我们已经将模型做得对我们的小数据集来说过于复杂。

在接下来的章节中，我们将看到这些不同复杂度的模型在我们高尔夫球场数据上的表现，并探讨为什么找到合适的复杂度对做出可靠预测至关重要。

# 什么样的模型“好”？

## 预测误差

预测的主要目标是使猜测尽可能接近真实值。我们需要一种衡量误差的方式，这种方式把高估或低估视为同样的不正确。预测值比真实答案高10个单位和低10个单位是一样错误的。

这就是我们使用**均方根误差（RMSE）**作为衡量标准的原因。RMSE告诉我们预测误差的典型大小。如果RMSE是7，意味着我们的预测通常会偏离真实值约7个单位。如果是3，通常偏离约3个单位。较低的RMSE意味着更好的预测效果。

![](../Images/4b69f4c62599ba2ba173037469adb4b4.png)

在上面的简单5点数据集中，我们可以说我们的预测大约偏离3个人。

在衡量模型性能时，我们通常会计算两种不同的误差。首先是训练误差——即模型在它学习过的数据上的表现。其次是测试误差——即模型在它从未见过的新数据上的表现。这个测试误差非常关键，因为它告诉我们模型在面对现实世界中新的数据时的表现如何。

## ⛳️ 查看我们的高尔夫球场预测

在我们的高尔夫球场案例中，我们试图根据天气状况预测每日的玩家数量。我们拥有来自28个不同日期的数据，并将其分成两部分：

+   训练数据：模型用来学习模式的14天记录

+   测试数据：我们隐藏在模型之外的14天不同记录

使用我们构建的模型，让我们测试训练数据和测试数据，并计算它们的RMSE。

![](../Images/dc2e2893ad9d5e16348b6abb679078ca.png)![](../Images/4c345cc4d52de24c83c3d9d4c1759c5b.png)

```py
# Create training predictions DataFrame
train_predictions = pd.DataFrame({
    f'Depth_{i}': trees[i].predict(X_train) for i in range(1, MAX_DEPTH + 1)
})
#train_predictions['Actual'] = y_train.values
train_predictions.index = X_train.index

# Create test predictions DataFrame
test_predictions = pd.DataFrame({
    f'Depth_{i}': trees[i].predict(X_test) for i in range(1, MAX_DEPTH + 1)
})
#test_predictions['Actual'] = y_test.values
test_predictions.index = X_test.index

print("\nTraining Predictions:")
print(train_predictions.round(1))
print("\nTest Predictions:")
print(test_predictions.round(1))
```

![](../Images/413369456a72d5c176d6baa2f6a16e5b.png)

```py
from sklearn.metrics import root_mean_squared_error

# Calculate RMSE values
train_rmse = {depth: root_mean_squared_error(y_train, tree.predict(X_train))
              for depth, tree in trees.items()}
test_rmse = {depth: root_mean_squared_error(y_test, tree.predict(X_test))
             for depth, tree in trees.items()}

# Print RMSE summary as DataFrame
summary_df = pd.DataFrame({
    'Train RMSE': train_rmse.values(),
    'Test RMSE': test_rmse.values()
}, index=range(1, MAX_DEPTH + 1))
summary_df.index.name = 'max_depth'

print("\nSummary of RMSE values:")
print(summary_df.round(2))
```

![](../Images/857cc086116969099a9091f30891f2db.png)

从这些数字来看，我们已经能够看到一些有趣的模式：随着我们让模型变得越来越复杂，它们在预测之前已经见过的日期的玩家数量时变得越来越准确——直到我们最复杂的模型在训练数据上做出了完美的预测。

![](../Images/e92e88824480d1a8c42c20133d07f189.png)

但真正的考验是它们在预测新日期的玩家数量时的表现。在这里，我们看到了一些不同的情况。尽管增加一些复杂性有助于提高表现（从深度1到深度3，测试误差不断改善），但是将模型做得过于复杂（深度4-5）实际上会开始导致效果变差。

![](../Images/d91133e82e8ea3802cc41540d83ca708.png)

训练和测试表现之间的差异（从误差为3-4名玩家到误差为9名玩家）揭示了预测中的一个根本挑战：在新的、未见过的情况中表现良好，比在熟悉的情况下表现好要难得多。即使是我们表现最好的模型，也能看到训练和测试表现之间的差距。

![](../Images/d18915b0493f91277de71fb1301381f2.png)

```py
# Create figure
plt.figure(figsize=(4, 3), dpi=300)
ax = plt.gca()

# Plot main lines
plt.plot(summary_df.index, summary_df['Train RMSE'], marker='o', label='Train RMSE', 
         linestyle='-', color='crimson', alpha=0.1)
plt.plot(summary_df.index, summary_df['Test RMSE'], marker='o', label='Test RMSE', 
         linestyle='-', color='crimson', alpha=0.6)

# Add vertical lines and difference labels
for depth in summary_df.index:
    train_val = summary_df.loc[depth, 'Train RMSE']
    test_val = summary_df.loc[depth, 'Test RMSE']
    diff = abs(test_val - train_val)

    # Draw vertical line
    plt.vlines(x=depth, ymin=min(train_val, test_val), ymax=max(train_val, test_val), 
               colors='black', linestyles='-', lw=0.5)

    # Add white box behind text
    bbox_props = dict(boxstyle="round,pad=0.1", fc="white", ec="white")
    plt.text(depth - 0.15, (train_val + test_val) / 2, f'{diff:.1f}', 
             verticalalignment='center', fontsize=9, fontweight='bold',
             bbox=bbox_props)

# Customize plot
plt.xlabel('Max Depth')
plt.ylabel('RMSE')
plt.title('Train vs Test RMSE by Tree Depth')
plt.grid(True, linestyle='--', alpha=0.2)
plt.legend()

# Remove spines
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# Set limits
plt.xlim(0.8, 5.2)
plt.ylim(0, summary_df['Train RMSE'].max() * 1.1)

plt.tight_layout()
plt.show()
```

接下来，我们将探讨模型失败的两种主要方式：通过持续不准确的预测（偏差）或通过极其不一致的预测（方差）。

# 理解偏差（当模型欠拟合时）

## 什么是偏差？

偏差发生在模型通过过于简单无法捕捉到重要模式时。具有高偏差的模型会持续犯大错，因为它错过了关键的关系。可以把它理解为以可预测的方式始终错误。

当模型欠拟合时，会表现出以下特定的行为：

+   不同预测中的误差相似

+   训练误差很高

+   测试误差也很高

+   训练和测试误差接近

高偏差和欠拟合表明我们的模型需要更加复杂——它需要关注数据中的更多模式。但我们如何发现这个问题呢？我们查看训练和测试的误差。如果两个误差都很高，并且彼此相似，我们很可能遇到偏差问题。

## ⛳️ 查看我们的简单高尔夫球场模型

让我们检查一下我们最简单模型（深度为1）的表现：

![](../Images/a60afb362ddeb2e1d3f7d3170c422b45.png)

+   训练RMSE：16.13

    平均而言，即使是它训练过的日期，误差也大约为16名玩家。

+   测试RMSE：13.26

    对于新的日期，模型预测误差大约为13名玩家。

这些数字讲述了一个重要的故事。首先，注意到这两个误差都很高。误差为13-16名玩家，在很多日期的玩家数量在20到80之间时，这个误差很大。其次，尽管测试误差更高（如我们所料），但两者的误差都明显较大。

深入分析正在发生的情况：

![](../Images/4fb3f69c9d775e008d7a9fe5365f1556.png)

1.  在深度为1时，我们的模型只能做出一个分裂决策。它可能只是根据是否下雨来分裂日期，从而只创建两种可能的玩家数量预测。这意味着许多不同的天气条件被归类到相同的预测中。

1.  这些误差遵循明显的模式：

    - 在炎热、潮湿的日子里：模型预测的玩家数过多，因为它只看是否下雨

    - 在凉爽、完美的日子里：模型预测的玩家数过少，因为它忽略了良好的比赛条件。

1.  最具说明性的是训练误差和测试误差的相似性。两者都很高，这意味着即使是在模型训练过的日期上进行预测，模型也表现得很差。这是高偏差的最明显迹象——模型过于简单，甚至无法捕捉到其训练数据中的模式。

这是欠拟合的关键问题：模型缺乏捕捉影响玩家人数的重要天气条件组合的复杂性。每个预测都是以可预测的方式错误，因为模型根本无法同时考虑多个天气因素。

这个解决方案看起来很明显：使模型更加复杂，以便它能够同时考虑多种天气条件。但正如我们在下一节将看到的，这会带来一些新的问题。

# 理解方差（当模型过拟合时）

## 什么是方差？

方差发生在模型过拟合时，它变得过于复杂，并对数据中的小变化过于敏感。虽然欠拟合模型忽视了重要的模式，但过拟合模型则相反——它把每一个微小的细节都当作重要的模式来处理。

一个过拟合的模型表现出以下特点：

+   训练数据中的非常小的错误

+   测试数据中的更大误差

+   训练和测试误差之间的巨大差距

+   随着数据变化剧烈变化的预测

当数据集很小的时候，这个问题尤其危险。当我们只有几个示例供学习时，过拟合模型可能会完美地记住所有这些示例，而没有学习到真正重要的模式。

## ⛳️ 看看我们复杂的高尔夫球场模型

让我们来检视一下我们最复杂模型（深度为5）的表现：

![](../Images/29cbb043dbe43ddb37a06a163e20d381.png)

+   训练RMSE：0.00

    完美的预测！训练数据中没有一个错误

+   测试RMSE：9.14

    但是在新的一天，它的预测偏差大约是9到10个玩家

这些数字揭示了一个典型的过拟合案例。零的训练误差意味着我们的模型学会了预测每个它训练过的特定日期的玩家人数。听起来不错，对吧？但是看看测试误差——它高得多。训练和测试表现之间的巨大差距（从0到9-10个玩家）是一个红旗。

更深入地看一下发生了什么：

![](../Images/47825e3568da33adba6f9cacd2f9844d.png)

1.  在深度为5时，我们的模型会创建非常具体的规则。例如：

    - 如果没有下雨并且温度是76°F并且湿度是80%并且有风 → 预测恰好70个玩家

    每个规则仅基于我们训练数据中的一两天。

1.  当模型在测试数据中看到稍微不同的条件时，它会感到困惑。

    这与我们上面的第一个规则非常相似，但模型可能会预测一个完全不同的数字

1.  在只有14个训练示例的情况下，每个训练日都会有自己非常具体的一组规则。模型并没有学习天气如何影响玩家人数的普遍模式——它只是记住了每个特定日子发生了什么。

特别有趣的是，尽管这个过拟合模型比我们的欠拟合模型表现得更好（测试误差9.15），但它实际上比我们适度复杂的模型更差。这表明，增加过多的复杂性可能会开始损害我们的预测，尽管训练表现看起来是完美的。

这就是过拟合的根本挑战：模型变得过于专注于对训练数据做出完美预测，以至于无法学习能够帮助其预测新情况的通用模式。当处理像我们这样的小数据集时，尤其是有问题的，因为为每个训练样本创建一个独特的规则会使我们无法可靠地处理新情况。

# 寻找平衡

## 核心问题

现在我们已经看到了两个问题——欠拟合和过拟合——让我们看看当我们尝试解决它们时会发生什么。这就是偏差-方差权衡的真正挑战所在。

看看我们在使模型变得更复杂时，它们的表现：

![](../Images/5ab580aa2868077d0063910ba7fe1884.png)

这些数字讲述了一个重要的故事。随着我们使模型变得更复杂：

1.  训练误差持续改善（16.3 → 6.7 → 3.6 → 1.1 → 0.0）

1.  测试误差最初显著改善（13.3 → 10.1 → 7.3）

1.  但随后测试误差略微变差（7.3 → 8.8 → 9.1）

## 为什么会发生这种情况

这个模式不是巧合——它是偏差-方差权衡的基本特性。

当我们使模型变得更复杂时：

+   它不太可能欠拟合训练数据（偏差减少）

+   但它变得更容易对小的变化发生过拟合（方差增加）

我们的高尔夫球场数据清晰地展示了这一点：

1.  深度 1 模型欠拟合严重——它只能将天数分为两组，导致到处都有大的误差

1.  增加复杂性有所帮助——深度 2 可以考虑更多的天气组合，而深度 3 发现了更好的模式

1.  但是深度 4 开始出现过拟合——为几乎每个训练天创建独特的规则

最佳点出现在我们的深度 3 模型中：

![](../Images/65d6d8150cad95ca942bb23e71884aa5.png)

这个模型足够复杂，避免了欠拟合，同时又足够简单，避免了过拟合。它在所有模型中具有最佳的测试表现（RMSE 7.13）。

## 现实世界的影响

在我们的高尔夫球场预测中，这种权衡有着真实的后果：

+   深度 1：通过只看温度来欠拟合，错过了关于雨量或风速的关键信息

+   深度 2：可以结合两个因素，如温度和雨量

+   深度 3：能够发现类似“温暖、低湿度、没有雨意味着高出勤率”的模式

+   深度 4-5：通过不可靠的规则过拟合，如“在风大的日子里，温度恰好是 76°F，湿度是 80%，意味着恰好 70 名球员”

这就是为什么找到正确的平衡很重要。只有 14 个训练样本，每个关于模型复杂度的决策都会产生很大影响。我们的深度 3 模型并不完美——平均偏差 7 名球员并不理想。但它比深度 1 的欠拟合（偏差 13 名球员）或深度 4 的过拟合（对非常相似的天气条件给出截然不同的预测）要好得多。

# 如何选择正确的平衡

## 基本方法

在选择最佳模型时，仅查看训练误差和测试误差是不够的。为什么？因为我们的测试数据有限——只有14个测试样本，我们可能会因为特定的几天模型表现好或者不好而感到幸运或不幸运。

测试模型的更好方法叫做**交叉验证**。与其仅使用一次训练和测试数据拆分，我们尝试不同的拆分。每次我们：

1.  选择不同的样本作为训练数据

1.  训练我们的模型

1.  在未用于训练的样本上测试

1.  记录误差

通过多次这样做，我们可以更好地理解我们的模型真正的表现如何。

## ⛳️ 我们从高尔夫球场数据中发现的结果

让我们来看一下不同模型在多个训练拆分中的交叉验证表现。鉴于我们仅有14个训练样本，我们使用了K折交叉验证，k=7，这意味着每个验证折叠有2个样本。

![](../Images/e385c08896e206e43fcd8e9de12d97f9.png)

尽管这是一个较小的验证集，但它让我们能够最大化我们的训练数据，同时仍然获得有意义的交叉验证估计：

```py
from sklearn.model_selection import KFold

def evaluate_model(X_train, y_train, X_test, y_test, n_splits=7, random_state=42):
   kf = KFold(n_splits=n_splits, shuffle=True, random_state=random_state)
   depths = range(1, 6)
   results = []

   for depth in depths:
       # Cross-validation scores
       cv_scores = []
       for train_idx, val_idx in kf.split(X_train):
           # Split data
           X_tr, X_val = X_train.iloc[train_idx], X_train.iloc[val_idx]
           y_tr, y_val = y_train.iloc[train_idx], y_train.iloc[val_idx]

           # Train and evaluate
           model = DecisionTreeRegressor(max_depth=depth, random_state=RANDOM_STATE)
           model.fit(X_tr, y_tr)
           val_pred = model.predict(X_val)
           cv_scores.append(np.sqrt(mean_squared_error(y_val, val_pred)))

       # Test set performance
       model = DecisionTreeRegressor(max_depth=depth, random_state=RANDOM_STATE)
       model.fit(X_train, y_train)
       test_pred = model.predict(X_test)
       test_rmse = np.sqrt(mean_squared_error(y_test, test_pred))

       # Store results
       results.append({
           'CV Mean RMSE': np.mean(cv_scores),
           'CV Std': np.std(cv_scores),
           'Test RMSE': test_rmse
       })

   return pd.DataFrame(results, index=pd.Index(depths, name='Depth')).round(2)

# Usage:
cv_df = evaluate_model(X_train, y_train, X_test, y_test)
print(cv_df)
```

![](../Images/457b97b29c2a97b2dbc1c62c05b5f659.png)

简单模型（深度1）：

- CV均值RMSE：20.28（±12.90）

- 在交叉验证中显示出较大的波动（±12.90）

- 不同数据拆分下表现 consistently 较差

稍微灵活的模型（深度2）：

- CV均值RMSE：17.35（±11.00）

- 比深度1的平均误差低

- 交叉验证中仍然显示出相当大的波动

- 预测能力有所提升

中等复杂度模型（深度3）：

- CV均值RMSE：16.16（±9.26）

- 更稳定的交叉验证表现

- 比更简单的模型有明显改善

- 稳定性和准确性的最佳平衡

复杂模型（深度4）：

- CV均值RMSE：16.10（±12.33）

- 与深度3的均值非常相似

- 交叉验证中的较大波动表明预测不够稳定

- 开始显示出过拟合的迹象

非常复杂的模型（深度5）：

- CV均值RMSE：16.59（±11.73）

- CV性能开始恶化

- 高波动继续

- 明显的过拟合迹象开始出现

![](../Images/853f4e7f0dd7ac08f000f411fc7c5d4c.png)

这个交叉验证展示了一个重要的点：尽管我们之前的分析中深度3模型在测试性能上表现最好，但交叉验证结果揭示了模型性能的波动。所有模型中较高的标准差（从±9.26到±12.90不等）表明，在这样一个小的数据集上，任何一个数据拆分可能都会给我们带来误导性的结果。这也是交叉验证如此重要的原因——它帮助我们看到模型的真实表现，而不仅仅是一个幸运或不幸运的拆分结果。

## 如何在实践中做出这一决定

根据我们的结果，以下是我们如何找到合适模型平衡的方法：

1.  **从简单开始**

    从你能构建的最基础模型开始。检查它在训练数据和测试数据上的表现。如果在这两者上都表现不佳，那也没关系！这只是说明你的模型需要稍微复杂一些，以便捕捉到重要的模式。

1.  **逐渐增加复杂性**

    现在，逐步使你的模型变得更加复杂，注意每次调整后的性能变化。当你发现模型在新数据上的表现开始变差时，那就是信号，告诉你该停止了——你已经找到了合适的复杂性平衡。

1.  **注意警告信号**

    留意潜在问题：如果你的模型在训练数据上表现极好，但在新数据上表现很差，那就说明模型太复杂。如果在所有数据上表现都很差，那说明模型太简单。如果模型在不同数据分割间的表现差异很大，那你可能做得太复杂了。

1.  **考虑数据规模**

    当你没有太多数据（比如我们的14个样本）时，保持模型简单。你不能指望在非常少的样本上训练出一个能够做出完美预测的模型。对于小数据集，拥有一个稳定的简单模型比一个不可靠的复杂模型要好。

每当我们构建预测模型时，我们的目标不是获得完美的预测——而是获得可靠、有效的预测，这些预测将在新数据上表现良好。对于我们的高尔夫球场数据集，平均预测偏差为6-7名球员虽然不是完美的，但远比偏差为11-12名球员（过于简单）或预测极度不可靠（过于复杂）要好得多。

# 关键要点

## 快速识别问题的方法

让我们总结一下我们关于构建实际有效的预测模型所学到的知识。以下是一些关键迹象，可以告诉你模型是过拟合还是欠拟合：

![](../Images/ed6d8ea301f30aa4d80257ec7e1326fb.png)

**欠拟合的迹象（过于简单）：**

当模型欠拟合时，训练误差将很高（就像我们深度为1的模型，其RMSE为16.13）。同样，测试误差也会很高（13.26 RMSE）。这两个误差之间的差距很小（16.13与13.26），这告诉我们模型一直表现不佳。这样的模型过于简单，无法捕捉到真实的关系。

**过拟合的迹象（过于复杂）：**

过拟合模型显示出完全不同的模式。你会看到非常低的训练误差（就像我们深度为5的模型，其RMSE为0.00），但测试误差却高得多（9.15 RMSE）。训练和测试表现之间的巨大差距（0.00与9.15）是一个信号，表明模型很容易被训练数据中的噪声干扰，它仅仅是在记忆它所训练过的特定示例。

**良好平衡的迹象（如我们的深度3模型）：**

一个平衡良好的模型表现出更有前景的特征。训练误差相对较低（3.16 RMSE），尽管测试误差较高（7.33 RMSE），但这是我们最好的整体表现。训练误差和测试误差之间的差距存在，但并不极端（3.16 vs 7.33）。这告诉我们，模型找到了甜蜜点：它足够复杂，能够捕捉到数据中的真实模式，同时又足够简单，避免了被噪声干扰。欠拟合和过拟合之间的这种平衡正是我们在可靠模型中所追求的。

# 最后备注

偏差-方差权衡不仅仅是理论。它对实际预测有着真实的影响，包括我们之前的高尔夫球场示例。这里的目标不是完全消除欠拟合或过拟合，因为那是不可能的。我们想要的是找到那个甜蜜点，在这个点上，模型足够复杂以避免欠拟合并捕捉到真实的模式，同时又足够简单，以避免对随机噪声过拟合。

最终，一个始终略有误差的模型通常比一个过拟合的模型更有用——后者偶尔完美，但通常误差较大。

> 在现实世界中，可靠性比完美更为重要。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.6。虽然所讨论的概念通常适用，但不同版本之间的具体代码实现可能会略有不同。

## 关于插图

除非另有说明，否则所有图片均由作者创作，结合了Canva Pro的授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙈𝙤𝙙𝙚𝙡 𝙀𝙫𝙖𝙡𝙪𝙖𝙩𝙞𝙤𝙣 & 𝙊𝙥𝙩𝙞𝙢𝙞𝙯𝙖𝙩𝙞𝙤𝙣 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----9521871f728a--------------------------------)

## 模型评估与优化

[查看列表](https://medium.com/@samybaladram/list/model-evaluation-optimization-331287896864?source=post_page-----9521871f728a--------------------------------)3个故事![](../Images/18fa82b1435fa7d5571ee54ae93a6c62.png)![](../Images/c95e89d05d1de700c631c342cd008de0.png)![](../Images/30e20e1a8ba3ced1e77644b706acd18d.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----9521871f728a--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----9521871f728a--------------------------------)8个故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
