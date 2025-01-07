# 决策树回归器解释：带有代码示例的可视化指南

> 原文：[https://towardsdatascience.com/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef?source=collection_archive---------1-----------------------#2024-10-10](https://towardsdatascience.com/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef?source=collection_archive---------1-----------------------#2024-10-10)

## 回归算法

## 明智地修剪分支，通过成本复杂度修剪

[](https://medium.com/@samybaladram?source=post_page---byline--fbd2836c3bef--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--fbd2836c3bef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fbd2836c3bef--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fbd2836c3bef--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--fbd2836c3bef--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbd2836c3bef--------------------------------) ·阅读时间：11分钟·2024年10月10日

--

[](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----fbd2836c3bef--------------------------------) [## 决策树分类器解释：带有代码示例的初学者可视化指南

### 对我们喜爱的倒立树的全新视角

towardsdatascience.com](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----fbd2836c3bef--------------------------------)

决策树不仅仅限于数据分类——它们同样擅长预测数值！分类树经常占据焦点，但决策树回归器（或回归树）在连续变量预测的世界中是强大且多功能的工具。

虽然我们将讨论回归树构建的机制（这些机制与分类树大致相同），但在这里，我们还将超越分类器文章中介绍的*前*修剪方法，如“最小样本叶节点”和“最大树深度”。我们将探索最常见的*后*修剪方法——**成本复杂度修剪**，它为决策树的成本函数引入了一个复杂度参数。

![](../Images/af2717ef7b5f2f1e541ab5ba0909511d.png)

所有可视化图：作者使用Canva Pro创作。优化为移动端显示；在桌面端可能显得过大。

# 定义

回归决策树是一种使用树状结构预测数值的模型。它根据关键特征拆分数据，从根问题开始并分支。每个节点询问一个特征，继续拆分数据，直到达到叶节点并作出最终预测。要获得结果，你需要从根节点到叶节点，沿着匹配数据特征的路径进行跟踪。

![](../Images/4d58bb0bb413cc741a3d2babe2bb0a24.png)

回归决策树通过一系列基于数据的问题来预测数值结果，逐步缩小范围直到最终结果。

# 📊 使用的数据集

为了演示我们的概念，我们将使用[我们的标准数据集](/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)。该数据集用于预测某一天访客高尔夫球场的人数，包含天气展望、温度、湿度和风力等变量。

![](../Images/be5edbc6b8231b1605ca2447d8c33657.png)

列：‘天气展望’（通过独热编码转为晴天、阴天、雨天），‘温度’（以华氏度表示），‘湿度’（以百分比表示），‘风力’（是/否）和‘玩家数量’（数值型，目标特征）

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

# Create dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Num_Players': [52, 39, 43, 37, 28, 19, 43, 47, 56, 33, 49, 23, 42, 13, 33, 29, 25, 51, 41, 14, 34, 29, 49, 36, 57, 21, 23, 41]
}

df = pd.DataFrame(dataset_dict)

# One-hot encode 'Outlook' column
df = pd.get_dummies(df, columns=['Outlook'],prefix='',prefix_sep='')

# Convert 'Wind' column to binary
df['Wind'] = df['Wind'].astype(int)

# Rearrange columns
column_order = ['sunny', 'overcast', 'rain', 'Temperature', 'Humidity', 'Wind', 'Num_Players']
df = df[column_order]

# Split features and target
X, y = df.drop('Num_Players', axis=1), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)
```

# 主要机制

回归决策树通过递归地根据最能减少预测误差的特征来拆分数据。以下是一般过程：

1.  从根节点开始，使用整个数据集。

1.  选择最小化特定误差指标（如均方误差或方差）的特征来拆分数据。

1.  根据拆分创建子节点，每个子节点表示与相应特征值对齐的数据子集。

1.  对每个子节点重复步骤2–3，继续拆分数据直到达到停止条件。

1.  为每个叶节点分配一个最终的预测值，通常是该节点中目标值的**平均值**。

![](../Images/48ae23f237c31ae05ba52c569f790fb8.png)

# 训练步骤

我们将探索决策树算法CART（分类与回归树）中的回归部分。它构建二叉树，通常遵循以下步骤：

1. 从根节点开始，使用所有训练样本。

![](../Images/62033724ca4273f8d77b2f9184658c43.png)

2. 对于数据集中的每个特征：

a. 按升序排序特征值。

b. 将相邻值之间的所有中点视为潜在的拆分点。

![](../Images/b37c7a5dead079a3548bbc02b7d9d800.png)

总共需要检查23个拆分点。

3. 对每个潜在拆分点：

a. 计算当前节点的均方误差（MSE）。

b. 计算结果拆分的加权平均误差。

![](../Images/edce98c77731a3a1fb3e871e75d9b0f2.png)

例如，在这里，我们计算了拆分点“温度”值为73.5时的均方误差（MSE）的加权平均值。

```py
def calculate_split_mse(X_train, y_train, feature_name, split_point):
    # Create DataFrame and sort by feature
    analysis_df = pd.DataFrame({
        'feature': X_train[feature_name],
        'y_actual': y_train
    }).sort_values('feature')

    # Split data and calculate means
    left_mask = analysis_df['feature'] <= split_point
    left_mean = analysis_df[left_mask]['y_actual'].mean()
    right_mean = analysis_df[~left_mask]['y_actual'].mean()

    # Calculate squared differences
    analysis_df['squared_diff'] = np.where(
        left_mask,
        (analysis_df['y_actual'] - left_mean) ** 2,
        (analysis_df['y_actual'] - right_mean) ** 2
    )

    # Calculate MSEs and counts
    left_mse = analysis_df[left_mask]['squared_diff'].mean()
    right_mse = analysis_df[~left_mask]['squared_diff'].mean()
    n_left = sum(left_mask)
    n_right = len(analysis_df) - n_left

    # Calculate weighted average MSE
    weighted_mse = (n_left * left_mse + n_right * right_mse) / len(analysis_df)

    # Print results
    print(analysis_df)
    print(f"\nResults for split at {split_point} on feature '{feature_name}':")
    print(f"Left child MSE (n={n_left}, mean={left_mean:.2f}): {left_mse:.2f}")
    print(f"Right child MSE (n={n_right}, mean={right_mean:.2f}): {right_mse:.2f}")
    print(f"Weighted average MSE: {weighted_mse:.2f}")

# Example usage:
calculate_split_mse(X_train, y_train, 'Temperature', 73.5)
```

![](../Images/8302971702ad2e5e18eba4e710464c8a.png)

4. 在评估所有特征和拆分点后，选择均方误差（MSE）加权平均值最低的一个。

![](../Images/2bcbe92a762d4f11ca62b9548587d9d7.png)

```py
def evaluate_all_splits(X_train, y_train):
    """Evaluate all possible split points using midpoints for all features"""
    results = []

    for feature in X_train.columns:
        data = pd.DataFrame({'feature': X_train[feature], 'y_actual': y_train})
        splits = [(a + b)/2 for a, b in zip(sorted(data['feature'].unique())[:-1], 
                                          sorted(data['feature'].unique())[1:])]

        for split in splits:
            left_mask = data['feature'] <= split
            n_left = sum(left_mask)

            if not (0 < n_left < len(data)): continue

            left_mean = data[left_mask]['y_actual'].mean()
            right_mean = data[~left_mask]['y_actual'].mean()

            left_mse = ((data[left_mask]['y_actual'] - left_mean) ** 2).mean()
            right_mse = ((data[~left_mask]['y_actual'] - right_mean) ** 2).mean()

            weighted_mse = (n_left * left_mse + (len(data) - n_left) * right_mse) / len(data)

            results.append({'Feature': feature, 'Split_Point': split, 'Weighted_MSE': weighted_mse})

    return pd.DataFrame(results).round(2)

# Example usage:
results = evaluate_all_splits(X_train, y_train)
print(results)
```

![](../Images/1ce048d6bbf06583edef5c90b22e9d5e.png)

5\. 根据选择的特征和切分点创建两个子节点：

- 左子节点：特征值 <= 切分点的样本

- 右子节点：特征值 > 切分点的样本

![](../Images/87c47d48c4a8dd061010b27bc42362f0.png)

6\. 对每个子节点递归重复步骤2到5。（继续直到满足停止准则。）

![](../Images/60ff1e00eadc6faef39b8fb6a57fe968.png)![](../Images/3ad498b5148c0d1e27ed7cf1a7f885e7.png)

7\. 在每个叶节点，分配该节点中样本的平均目标值作为预测值。

![](../Images/aab4fe4bdcce592f0c4e10224a18ad10.png)

```py
from sklearn.tree import DecisionTreeRegressor, plot_tree
import matplotlib.pyplot as plt

# Train the model
regr = DecisionTreeRegressor(random_state=42)
regr.fit(X_train, y_train)

# Visualize the decision tree
plt.figure(figsize=(26,8))
plot_tree(regr, feature_names=X.columns, filled=True, rounded=True, impurity=False, fontsize=16, precision=2)
plt.tight_layout()
plt.show()
```

![](../Images/4c99833d95cd4cda3734cf643d7c2b9d.png)

在这个scikit-learn的输出中，展示了叶节点和中间节点的样本及其值。

# 回归/预测步骤

这是回归树如何对新数据进行预测的过程：

1\. 从树的顶部（根节点）开始。

2\. 在每个决策点（节点）：

- 查看特征和切分值。

- 如果数据点的特征值较小或相等，向左走。

- 如果它更大，向右走。

3\. 一直向下移动直到到达树的末端（叶节点）。

4\. 预测值是该叶节点中存储的平均值。

![](../Images/ce9e66e2984c1dd8926cee0a62199806.png)

## 评估步骤

![](../Images/b1973f3c301a44f070306fc8def936ce.png)

这个RMSE值比[虚拟回归器的结果](https://medium.com/towards-data-science/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)要好得多。

# 预剪枝与后剪枝

在构建完树之后，我们唯一需要担心的就是如何使树变小，以防止过拟合。通常，修剪的方法可以分为以下几类：

## 预剪枝

预剪枝，也称为早期停止，是指在训练过程中基于某些预定义的标准，停止决策树的生长。此方法旨在防止树变得过于复杂并导致过拟合。常见的预剪枝技术包括：

1.  **最大深度**：限制树的最大深度。

1.  **每次切分的最小样本数**：要求切分一个节点时，必须满足最小样本数的条件。

1.  **每个叶节点的最小样本数**：确保每个叶节点至少包含一定数量的样本。

1.  **最大叶节点数**：限制树中叶节点的总数。

1.  **最小纯度减少**：仅允许那些减少纯度达到指定值的切分。

这些方法在满足指定条件时停止树的生长，有效地在树的构建阶段进行“修剪”。

([我们之前已经讨论过这些方法，它们在回归问题中完全相同。](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e))

## 后剪枝

后修剪方法允许决策树生长到最大，然后修剪回去以减少复杂性。这种方法首先构建完整的树，然后移除或合并那些对模型表现贡献不大的分支。一种常见的后修剪技术被称为**成本复杂度修剪**。

# 成本复杂度修剪

## 步骤1：计算每个节点的不纯度

对于每个中间节点，计算不纯度（回归问题中的MSE）。然后我们将这个值从小到大排序。

![](../Images/57e29c193ac2b684411d3da6c0176e9c.png)

```py
# Visualize the decision tree
plt.figure(figsize=(26,8))
plot_tree(regr, feature_names=X.columns, filled=True, rounded=True, impurity=True, fontsize=16, precision=2)
plt.tight_layout()
plt.show()
```

![](../Images/d4244591a1bc3d372a2b2429fc6e24b9.png)

在这个scikit-learn的输出中，节点的不纯度显示为每个节点的“squared_error”。

![](../Images/fbbc7f5114b6b7959849be45f651aec6.png)

我们给这些中间节点（从A到J）命名。然后我们根据它们的MSE从小到大进行排序。

## 步骤2：通过修剪最弱的链接来创建子树

目标是从**均方误差（MSE）最小的节点**开始，逐渐将中间节点转化为叶子节点。我们可以基于此创建一个修剪路径。

![](../Images/bd666447562c4b89a0452d15a3a697b9.png)

我们将它们命名为“子树*i*”，基于它被修剪的次数(*i*)。从原始树开始，树会在具有最低MSE的节点上修剪（从节点J开始，M（已经被J修剪掉），L，K，依此类推）。

## 步骤3：计算每棵子树的总叶子不纯度

对于每一棵子树*T*，可以计算总叶子不纯度（*R*(*T*)）：

*R*(*T*) = (1/*N*) Σ *I*(*L*) * *n*_*L*

其中：

**·** *L* 遍历所有叶子节点

**·** *n_L* 是叶子*L*中样本的数量 **·** *N* 是树中样本的总数

**·** *I*(*L*) 是叶子*L*的不纯度（MSE）

![](../Images/db7b97078d3ca41116ba6586413f17a6.png)

我们修剪得越多，总的叶子不纯度就越高。

## 步骤4：计算成本函数

为了控制何时停止将中间节点转化为叶子节点，我们首先使用以下公式检查每棵子树*T*的成本复杂度：

Cost(*T*) = *R*(*T*) + *α* * |*T*|

其中：

**·** *R*(*T*) 是总叶子不纯度

**·** |*T*| 是子树中的叶子节点数**·** *α* 是复杂度参数

![](../Images/d1e444f5b41346a1628e95d70944d312.png)

## 步骤5：选择Alpha

*α*的值控制我们最终得到哪一棵子树。**具有最低成本的子树将是最终的树**。

![](../Images/7f89a62ec5564f5169596ae9b6d2d1d6.png)

当*α*较小时，我们更关心准确性（较大的树）。当*α*较大时，我们更关心简洁性（较小的树）。

虽然我们可以自由设定*α*，在scikit-learn中，你也可以获取最小的*α*值来获得特定的子树。这被称为**有效的*α***。

![](../Images/d70f61e11dcfad94df3cbd4294949d3d.png)

这个有效的***α*** *也可以计算出来*。

```py
# Compute the cost-complexity pruning path
tree = DecisionTreeRegressor(random_state=42)
effective_alphas = tree.cost_complexity_pruning_path(X_train, y_train).ccp_alphas
impurities = tree.cost_complexity_pruning_path(X_train, y_train).impurities

# Function to count leaf nodes
count_leaves = lambda tree: sum(tree.tree_.children_left[i] == tree.tree_.children_right[i] == -1 for i in range(tree.tree_.node_count))

# Train trees and count leaves for each complexity parameter
leaf_counts = [count_leaves(DecisionTreeRegressor(random_state=0, ccp_alpha=alpha).fit(X_train_scaled, y_train)) for alpha in effective_alphas]

# Create DataFrame with analysis results
pruning_analysis = pd.DataFrame({
    'total_leaf_impurities': impurities,
    'leaf_count': leaf_counts,
    'cost_function': [f"{imp:.3f} + {leaves}α" for imp, leaves in zip(impurities, leaf_counts)],
    'effective_α': effective_alphas
})

print(pruning_analysis)
```

![](../Images/970ac8efb0ce4a38d8e6f98f7d4a57ee.png)

## 最终备注

预剪枝方法通常更快且更节省内存，因为它们从一开始就防止了树形过大。

后剪枝可能创建出更优的树形结构，因为它会在做出剪枝决定之前考虑整个树的结构。然而，这可能会消耗更多的计算资源。

两种方法的目标都是在模型复杂度和性能之间找到平衡，目的是创建一个能很好地对未见数据进行泛化的模型。选择预剪枝还是后剪枝（或两者结合）通常取决于具体的数据集、当前问题以及可用的计算资源。

在实际应用中，通常会结合使用这些方法，比如先应用一些预剪枝标准来防止树形过大，再使用后剪枝对模型的复杂度进行微调。

# 🌟 决策树回归器（带成本复杂度剪枝）代码总结

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import root_mean_squared_error
from sklearn.tree import DecisionTreeRegressor
from sklearn.preprocessing import StandardScaler

# Create dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Num_Players': [52,39,43,37,28,19,43,47,56,33,49,23,42,13,33,29,25,51,41,14,34,29,49,36,57,21,23,41]
}

df = pd.DataFrame(dataset_dict)

# One-hot encode 'Outlook' column
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)

# Convert 'Wind' column to binary
df['Wind'] = df['Wind'].astype(int)

# Split data into features and target, then into training and test sets
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Initialize Decision Tree Regressor
tree = DecisionTreeRegressor(random_state=42)

# Get the cost complexity path, impurities, and effective alpha
path = tree.cost_complexity_pruning_path(X_train, y_train)
ccp_alphas, impurities = path.ccp_alphas, path.impurities
print(ccp_alphas)
print(impurities)

# Train the final tree with the chosen alpha
final_tree = DecisionTreeRegressor(random_state=42, ccp_alpha=0.1)
final_tree.fit(X_train_scaled, y_train)

# Make predictions
y_pred = final_tree.predict(X_test)

# Calculate and print RMSE
rmse = root_mean_squared_error(y_test, y_pred)
print(f"RMSE: {rmse:.4f}")
```

## 进一步阅读

关于[决策树回归器](https://scikit-learn.org/1.5/modules/generated/sklearn.tree.DecisionTreeRegressor.html)、[成本复杂度剪枝](https://scikit-learn.org/1.5/auto_examples/tree/plot_cost_complexity_pruning.html)及其在scikit-learn中的实现，读者可以参考其官方文档。该文档提供了关于使用方法和参数的全面信息。

## 技术环境

本文使用Python 3.7和scikit-learn 1.5。虽然讨论的概念通常适用，但具体的代码实现可能会因版本不同而略有差异。

## 关于插图

除非另有说明，所有图像均由作者创作，并结合了Canva Pro授权的设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙍𝙚𝙜𝙧𝙚𝙨𝙨𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----fbd2836c3bef--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----fbd2836c3bef--------------------------------)5个故事![一个戴着粉红色帽子、扎着辫子的卡通玩偶。这只“虚拟”玩偶，凭借其基础的设计和心形图案的衬衫，形象地表现了机器学习中的虚拟回归器概念。就像这个玩具般的形象是一个简化、静态的人的表现，虚拟回归器则是作为更复杂分析基线的基本模型。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----fbd2836c3bef--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----fbd2836c3bef--------------------------------) 8 个故事！[](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
