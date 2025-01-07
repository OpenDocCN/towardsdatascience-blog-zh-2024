# 随机森林解析：带有代码示例的视觉指南

> 原文：[https://towardsdatascience.com/random-forest-explained-a-visual-guide-with-code-examples-9f736a6e1b3c?source=collection_archive---------0-----------------------#2024-11-07](https://towardsdatascience.com/random-forest-explained-a-visual-guide-with-code-examples-9f736a6e1b3c?source=collection_archive---------0-----------------------#2024-11-07)

## 集成学习

## 用随机树做出惊人的预测

[](https://medium.com/@samybaladram?source=post_page---byline--9f736a6e1b3c--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--9f736a6e1b3c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9f736a6e1b3c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9f736a6e1b3c--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--9f736a6e1b3c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f736a6e1b3c--------------------------------) ·阅读时间12分钟·2024年11月7日

--

[](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----9f736a6e1b3c--------------------------------) [## 决策树分类器解析：带有代码示例的初学者视觉指南

### 从全新的角度看我们最喜爱的倒立树

towardsdatascience.com](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----9f736a6e1b3c--------------------------------)

[决策树](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e)是机器学习的一个很好的起点——它们直观且易于理解。但有一个问题：它们在处理新数据时往往效果不好。预测结果可能不稳定且不可靠，这在你试图构建有用的东西时是一个真正的问题。

这时，随机森林就派上用场了。它结合了决策树的优点，并通过将多棵树结合在一起，使它们的效果更好。它已经成为许多数据科学家最喜爱的工具，因为它既有效又实用。

让我们来看看随机森林是如何工作的，以及它为什么可能正是你下一个项目所需要的。是时候不再迷失在树木中，真正看到森林的全貌——你下一个可靠的机器学习工具。

![](../Images/a46fad3053f42f475b5f070c27b60998.png)

所有图像：作者使用Canva Pro创建。优化了移动设备显示；在桌面设备上可能会显得过大。

# 定义

随机森林是一种集成机器学习模型，它结合了多个决策树。森林中的每一棵树都在数据的随机样本上进行训练（自助采样），并且在做出分裂时仅考虑特征的随机子集（特征随机化）。

对于分类任务，森林通过树之间的多数投票来进行预测；而对于回归任务，它通过平均各棵树的预测结果来进行预测。该模型的优势来自于它的“集体智慧”方法——虽然单棵树可能会犯错，但集体决策过程**往往能将这些错误平均化**，从而得出更可靠的预测结果。

![](../Images/a7c8ea0333ddfcdeb2779b785e896929.png)

随机森林是袋装（自助聚合）算法的一部分，因为它使用数据的不同随机部分来构建每棵树，并将它们的结果结合起来。

# 使用的数据集

在本文中，我们将以经典的高尔夫数据集为分类任务的例子进行讲解。虽然随机森林既可以处理分类任务，也可以处理回归任务，但我们将集中讨论分类部分——根据天气条件预测某人是否会打高尔夫。我们探讨的概念也可以很容易地应用于回归问题（例如预测球员数量），使用相同的原理。

![](../Images/05586d1dcea17f8a18206b58019181ea.png)

列：‘Overcast（被一热编码成3列）’，’Temperature（华氏温度）’，‘Humidity（湿度，%）’，‘Windy（是否有风，Yes/No）’和‘Play（是否打球，Yes/No，目标特征）’

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

# Create and prepare dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rainy', 'rainy', 'rainy', 'overcast', 
                'sunny', 'sunny', 'rainy', 'sunny', 'overcast', 'overcast', 'rainy',
                'sunny', 'overcast', 'rainy', 'sunny', 'sunny', 'rainy', 'overcast',
                'rainy', 'sunny', 'overcast', 'sunny', 'overcast', 'rainy', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0,
                   72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0,
                   88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0,
                 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0,
                 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True,
             True, False, True, True, False, False, True, False, True, True, False,
             True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes',
             'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes',
             'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}

# Prepare data
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Rearrange columns
column_order = ['sunny', 'overcast', 'rainy', 'Temperature', 'Humidity', 'Wind', 'Play']
df = df[column_order]

# Prepare features and target
X,y = df.drop('Play', axis=1), df['Play']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)
```

# 主要机制

以下是随机森林的工作原理：

1.  **自助采样（Bootstrap Sampling）：** 每棵树都有自己的独特训练集，这些训练集是通过从原始数据中随机抽样并允许重复抽取得到的。这意味着一些数据点可能会出现多次，而其他数据点则未被使用。

1.  **随机特征选择：** 在进行分裂时，每棵树只考虑一个随机的特征子集（通常是总特征数的平方根）。

1.  **生长树木：** 每棵树仅使用其自助样本和选定的特征进行生长，进行分裂，直到达到停止点（如纯净组或最小样本量）。

1.  **最终预测：** 所有树一起投票决定最终的预测结果。对于分类任务，采用类别预测的多数投票；对于回归任务，计算所有树的预测值的平均值。

![](../Images/3e815426c28ac98519c4884d12f43fac.png)

随机森林分类器通过结合来自100棵不同决策树的结果来进行预测，每棵树分析的特征包括温度和天气条件。最终的预测来自所有树中最常见的答案。

# 训练步骤

随机森林算法构建多个决策树并将它们结合起来。其工作原理如下：

**步骤1：自助样本创建**

1.0\. 设置树的数量（默认 = 100）

1.1\. 对于森林中的每一棵树：

a. 通过从原始数据中进行随机抽样并允许重复抽取，直到达到原始数据集的大小。这被称为**自助采样**。

b. 标记并将未选中的样本作为袋外（OOB）样本保留，以便后续进行误差估算

![](../Images/1671fe794bccb45ec5a642740a8d0d8f.png)

随机森林通过从原始训练集中随机选择数据点为每棵树创建不同的训练集，某些数据点可能会被多次选择。未使用的数据点则成为测试集，用于检查每棵树的性能。

```py
# Generate 100 bootstrap samples
n_samples = len(X_train)
n_bootstraps = 100
all_bootstrap_indices = []
all_oob_indices = []

np.random.seed(42)  # For reproducibility
for i in range(n_bootstraps):
    # Generate bootstrap sample indices
    bootstrap_indices = np.random.choice(n_samples, size=n_samples, replace=True)

    # Find OOB indices
    oob_indices = list(set(range(n_samples)) - set(bootstrap_indices))

    all_bootstrap_indices.append(bootstrap_indices)
    all_oob_indices.append(oob_indices)

# Print details for samples 1, 2, and 100
samples_to_show = [0, 1, 99]

for i in samples_to_show:
    print(f"\nBootstrap Sample {i+1}:")
    print(f"Chosen indices: {sorted(all_bootstrap_indices[i])}")
    print(f"Number of unique chosen indices: {len(set(all_bootstrap_indices[i]))}")
    print(f"OOB indices: {sorted(all_oob_indices[i])}")
    print(f"Number of OOB samples: {len(all_oob_indices[i])}")
    print(f"Percentage of OOB: {len(all_oob_indices[i])/n_samples*100:.1f}%")
```

![](../Images/fd0d49cc5d9614e8b4d96dedd10a6ca2.png)

注意，OOB的百分比是多么相似？在进行*n*样本的自助法抽样时，每个样本大约有37%的机会永远不会被选择。这来自于概率计算（1–1/*n*)*ⁿ*，随着*n*的增大，它接近1/e ≈ 0.368。因此，每棵树最终使用约63%的数据进行训练，其余的37%成为OOB样本。

**步骤2：树的构建**

2.1. 从根节点开始，使用完整的自助法样本

![](../Images/58dd94d352eb90afbfe2312662b55270.png)

在构建每棵决策树时，随机森林会考虑数据点的子集，并基于这些数据点的值提出分割问题——将较小的值分配到左侧，将较大的值分配到右侧进行预测。

a. 使用节点中的所有样本计算初始节点杂质

· 分类：基尼指数或熵

· 回归：均方误差（MSE）

![](../Images/49ffa6509e2a0b7efc710bf7c8891e58.png)

随机森林首先计算整个数据集的基尼杂质（在任何分割之前），使用YES和NO标签的比例——这是一种衡量当前数据中标签混合程度的指标。

b. 从总可用特征中选择随机子集：

· 分类：√n_features

· 回归：n_features/3

![](../Images/71393bd15609a6d43354efe5d9f06ac3.png)

对于树中的每次分割，随机森林会随机选择一个天气特征的子集（这里是从6个特征中选择2个）来进行考虑，从而使每棵树关注数据的不同方面。

c. 对每个选定的特征：

· 按特征值对数据点进行排序

· 确定潜在的分割点（连续唯一特征值之间的中点）

![](../Images/31b2530c2f2eab495683e971a5cc88da.png)

对于每个选定的特征，随机森林会查看排序后的数据中所有可能的分割点（如温度值66.0、69.0、71.0等），以找出最佳的方式将数据分为两组。

d. 对每个潜在的分割点：

· 将样本分为左右两组

· 使用其样本计算左子节点的杂质

· 使用其样本计算右子节点的杂质

· 计算杂质减少：

parent_impurity — (left_weight × left_impurity + right_weight × right_impurity)

![](../Images/3a76a1bc476cd60a143540debd4b04b3.png)

为了找到最佳分割点，随机森林会计算每个可能分割点的基尼杂质，基于组的大小进行加权平均，并选择那个能最大程度减少父节点杂质的分割点。

e. 使用提供最大纯度减少的特征和分割点来分割当前节点的数据。然后将数据点传递给各自的子节点。

![](../Images/e71e8da4672712136d3478ad9d1cd59b.png)

在比较所有可能的分割后，随机森林选择73.5°F的温度阈值，因为它提供了最大的纯度减少（0.041），并创建了两个分组：一个是低于73.5°F的混合组，另一个是纯净组。

f. 对每个子节点，重复过程（步骤b-e），直到：

- 纯节点或最小不纯度减少

- 最小样本数阈值

- 最大深度

- 最大叶子节点数

![](../Images/8e9f1677218a52ede01934cdc731077e.png)

这个过程对每个新的分组（节点）继续进行：随机选择特征，找到最佳的分割点，并进一步划分数据，直到每个分组是纯净的（全部为YES或全部为NO）或无法再分割。

**步骤3：树构建** 对其他自助抽样重复整个步骤2。

![](../Images/5a6197d5a37f101b794221d1c2c3383d.png)

随机森林中的每棵决策树通过使用不同的特征和阈值以不同的方式分割数据。这种多样性帮助森林做出比单棵树更好的预测。

```py
import matplotlib.pyplot as plt
from sklearn.tree import plot_tree
from sklearn.ensemble import RandomForestClassifier

# Train Random Forest
np.random.seed(42)  # For reproducibility
rf = RandomForestClassifier(n_estimators=100, random_state=42)
rf.fit(X_train, y_train)

# Create visualizations for trees 1, 2, and 100
trees_to_show = [0, 1, 99]  # Python uses 0-based indexing
feature_names = X_train.columns.tolist()
class_names = ['No', 'Yes']

# Set up the plot
fig, axes = plt.subplots(1, 3, figsize=(20, 6), dpi=300)  # Reduced height, increased DPI
fig.suptitle('Decision Trees from Random Forest', fontsize=16)

# Plot each tree
for idx, tree_idx in enumerate(trees_to_show):
    plot_tree(rf.estimators_[tree_idx], 
              feature_names=feature_names,
              class_names=class_names,
              filled=True,
              rounded=True,
              ax=axes[idx],
              fontsize=10)  # Increased font size
    axes[idx].set_title(f'Tree {tree_idx + 1}', fontsize=12)

plt.tight_layout(rect=[0, 0.03, 1, 0.95])
```

![](../Images/b46220bdf49910aa49e1d13e353fe1fc.png)

当前的scikit-learn实现中无法直接访问内部的自助抽样索引，因此生成的树与我们之前示例中计算的树不同。

# 测试步骤

对于预测，通过所有树路由新样本并进行汇总：

- 分类：多数投票

- 回归：平均预测

![](../Images/e8bd423acc3d8361e5ce82ec3fd542e4.png)

当新数据进入时，随机森林中的每棵树使用自己的决策路径进行预测。森林结合所有这些预测（74个YES与26个NO），并通过多数投票得到最终答案（在这个例子中是YES）。

## 袋外（OOB）评估

记住那些未被用于训练每棵树的样本——那剩下的1/3？它们就是你的OOB样本。随机森林不仅仅是忽略它们，而是将它们作为每棵树的便捷验证集。

![](../Images/afccd043882db9aba10d7e5fb93558e5.png)

每棵树都会在其自己的袋外样本上进行测试（即未用于训练的数据）。通过平均这些单个的OOB准确率得分（50%、66.6%、60%），随机森林提供了一种内建的方式来测量性能，无需单独的测试集。

# 评估步骤

构建完所有树后，我们可以评估测试集。

![](../Images/55f4ae7dd4d6a6513400959cda5c3c12.png)

通过结合多个多样化的决策树并使用多数投票，随机森林达到了85.7%的高准确率——通常比单棵决策树或更简单的模型表现更好！

# 关键参数

随机森林的关键参数（尤其是在`scikit-learn`中）包括所有决策树参数，以及一些独特的参数。

## 随机森林特定参数

+   `oob_score` 这个参数使用剩余的数据（袋外样本）来检查模型的表现。这为你提供了一种不需要单独设置测试数据来测试模型的方法，尤其适用于小数据集。

+   `n_estimators` 这个参数控制要构建多少棵树（默认值是100）。为了找到最佳的树木数量，**在添加更多树木时跟踪OOB误差率**。误差通常会迅速下降，然后趋于平稳。**稳定的点即是最佳树木数量**——在此之后增加更多树木只会带来最小的改善，同时增加计算时间。

```py
# Calculate OOB error for different numbers of trees
n_trees_range = range(10, 201)
oob_errors = [
    1 - RandomForestClassifier(n_estimators=n, oob_score=True, random_state=42).fit(X_train, y_train).oob_score_
    for n in n_trees_range
]

# Create a plot
plt.figure(figsize=(7, 5), dpi=300)
plt.plot(n_trees_range, oob_errors, 'b-', linewidth=2)
plt.xlabel('Number of Trees')
plt.ylabel('Out-of-Bag Error Rate')
plt.title('Random Forest OOB Error vs Number of Trees')
plt.grid(True, alpha=0.2)
plt.tight_layout()

# Print results at key intervals
print("OOB Error by Number of Trees:")
for i, error in enumerate(oob_errors, 1):
    if i % 10 == 0:
        print(f"Trees: {i:3d}, OOB Error: {error:.4f}")
```

![](../Images/97234d73657a128e85e5409216f55df5.png)![](../Images/3d6b30f69790966d7d1dc81b1bed0c83.png)

在我们的结果中，尽管大约27棵树显示出最佳的得分（0.2857），但这个早期表现可能不太可靠。在40到100棵树之间，误差率大约稳定在0.5000，显示出更一致的结果。超过100棵树并没有帮助，反而有时会使结果变差。这表明，使用大约50到60棵树是一个不错的选择——它既稳定、高效，又可靠。

+   `bootstrap` 这个参数决定了每棵树是从数据的随机样本中学习（`True`），还是使用所有数据（`False`）。默认值（`True`）有助于创建不同类型的树，这是随机森林工作原理的关键。**只有在数据非常少，无法跳过任何样本时，才考虑将其设置为**`**False**`。

+   `n_jobs` 这个参数控制训练过程中使用的处理器核心数。将其设置为 `-1` 会使用所有可用的核心，加快训练速度，但会占用更多内存。对于大数据集，你可能需要使用更少的核心，以避免内存不足。

## 与决策树共享的参数

以下参数与[决策树中的工作方式相同](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e)。

+   `max_depth`: 最大树深度

+   `min_samples_split`: 切分节点所需的最小样本数

+   `min_samples_leaf`: 叶节点所需的最小样本数

与决策树相比，以下是参数重要性的关键差异：

1.  `max_depth` 在随机森林中不太重要，因为结合多个树可以帮助防止过拟合，即使是较深的树也如此。通常你可以让树长得更深，以捕捉数据中的复杂模式。

1.  `min_samples_split` 和 `min_samples_leaf` 在随机森林中不太重要，因为使用多棵树天然有助于避免过拟合。通常可以将这些参数设置为比单棵决策树更小的值。

# 优点与缺点

## 优点：

1.  **强大且可靠：** 随机森林能提供准确的结果，并且比单棵决策树更不容易发生过拟合。通过使用随机采样并在每个节点混合不同的特征，随机森林能在多个问题上表现良好，且无需过多调整。

1.  **特征重要性：** 通过衡量每个特征在所有树中的贡献，模型可以告诉你哪些特征对预测最为重要。这有助于你理解驱动预测的因素。

1.  **最小预处理：** 随机森林能够很好地处理数值型和类别型变量，几乎不需要额外的准备工作。它们能够很好地处理缺失值和异常值，并且可以自动发现数据中的复杂关系。

# 缺点：

1.  **计算成本：** 随着树的增加或树的深度加深，训练和使用模型所需的时间也会增加。尽管可以通过使用多个处理器来加速训练，但对于大数据集来说，仍然需要大量的计算能力。

1.  **有限的可解释性：** 尽管你可以看到哪些特征在整体上最重要，但很难确切理解模型为何做出特定预测，这与单棵决策树不同。当你需要解释每个决策时，这可能会成为一个问题。

1.  **预测速度：** 为了做出预测，数据必须通过所有的树，然后将它们的答案结合起来。这使得随机森林比简单模型更慢，可能会成为实时应用中的一个问题。

# 最终评论

在看到随机森林在实践中的出色表现后，我开始真正喜欢它们。通过结合多棵树并让每棵树从数据的不同部分学习，它们始终能做出更好的预测——当然，比仅使用单棵树更有效。

尽管你确实需要调整一些设置，比如树的数量，但通常即使没有过多的微调，它们也能表现得很好。它们确实需要更多的计算能力（有时在处理数据中的稀有情况时可能会遇到困难），但它们可靠的性能和易用性使它们成为我许多项目中的首选。很明显，为什么这么多数据科学家也有同样的看法！

# 🌟 随机森林分类器代码总结

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.ensemble import RandomForestClassifier

# Create dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rainy', 'rainy', 'rainy', 'overcast', 
                'sunny', 'sunny', 'rainy', 'sunny', 'overcast', 'overcast', 'rainy',
                'sunny', 'overcast', 'rainy', 'sunny', 'sunny', 'rainy', 'overcast',
                'rainy', 'sunny', 'overcast', 'sunny', 'overcast', 'rainy', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0,
                   72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0,
                   88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0,
                 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0,
                 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True,
             True, False, True, True, False, False, True, False, True, True, False,
             True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes',
             'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes',
             'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}

# Prepare data
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Rearrange columns
column_order = ['sunny', 'overcast', 'rainy', 'Temperature', 'Humidity', 'Wind', 'Play']
df = df[column_order]

# Split features and target
X, y = df.drop('Play', axis=1), df['Play']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Train Random Forest
rf = RandomForestClassifier(n_estimators=100, max_features='sqrt', random_state=42)
rf.fit(X_train, y_train)

# Predict and evaluate
y_pred = rf.predict(X_test)
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
```

# 🌟 随机森林回归器代码总结

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import root_mean_squared_error
from sklearn.ensemble import RandomForestRegressor

# Create dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 
                'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain',
                'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast',
                'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temp.': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0,
              72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0,
              88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humid.': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0,
               90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0,
               65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True,
             True, False, True, True, False, False, True, False, True, True, False,
             True, False, False, True, False, False],
    'Num_Players': [52, 39, 43, 37, 28, 19, 43, 47, 56, 33, 49, 23, 42, 13, 33, 29,
                    25, 51, 41, 14, 34, 29, 49, 36, 57, 21, 23, 41]
}

# Prepare data
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='')
df['Wind'] = df['Wind'].astype(int)

# Split features and target
X, y = df.drop('Num_Players', axis=1), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Train Random Forest
rf = RandomForestRegressor(n_estimators=100, max_features='sqrt', random_state=42)
rf.fit(X_train, y_train)

# Predict and evaluate
y_pred = rf.predict(X_test)
rmse = root_mean_squared_error(y_test, y_pred)

print(f"Root Mean Squared Error: {rmse:.2f}") 
```

## 进一步阅读

对于[RandomForestClassifier](https://scikit-learn.org/1.5/modules/generated/sklearn.ensemble.RandomForestClassifier.html)和[RandomForestRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html)的详细解释及其在scikit-learn中的实现，读者可以参考官方文档，其中提供了关于使用和参数的全面信息。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.5版本。尽管讨论的概念通常是适用的，但不同版本的代码实现可能会略有不同。

## 关于插图

除非另有说明，所有插图均由作者创作，包含了来自Canva Pro的授权设计元素。

在这里查看更多关于集成学习的信息：

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----9f736a6e1b3c--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----9f736a6e1b3c--------------------------------)4个故事![](../Images/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](../Images/22a5d43568e70222eb89fd36789a9333.png)![](../Images/8ea1a2f29053080a5feffc709f5b8669.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----9f736a6e1b3c--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----9f736a6e1b3c--------------------------------)8个故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
