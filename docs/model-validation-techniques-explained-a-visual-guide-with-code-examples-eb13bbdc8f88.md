# 模型验证技术解析：带有代码示例的可视化指南

> 原文：[https://towardsdatascience.com/model-validation-techniques-explained-a-visual-guide-with-code-examples-eb13bbdc8f88?source=collection_archive---------1-----------------------#2024-11-30](https://towardsdatascience.com/model-validation-techniques-explained-a-visual-guide-with-code-examples-eb13bbdc8f88?source=collection_archive---------1-----------------------#2024-11-30)

## 模型评估与优化

## 12种必须了解的**机器学习验证方法**

[](https://medium.com/@samybaladram?source=post_page---byline--eb13bbdc8f88--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--eb13bbdc8f88--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eb13bbdc8f88--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--eb13bbdc8f88--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--eb13bbdc8f88--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eb13bbdc8f88--------------------------------) ·阅读时长26分钟·2024年11月30日

--

每天，机器都会做出数百万个预测——从检测照片中的物体到帮助医生发现疾病。但在相信这些预测之前，我们需要知道它们是否准确。毕竟，没有人愿意使用一个大多数时候都错误的机器！

这时，验证就显得尤为重要。验证方法测试机器的预测结果，以衡量其可靠性。虽然这听起来很简单，但实际上存在多种验证方法，每种方法都是为了应对机器学习中的特定挑战而设计的。

在这里，我将这些验证技术——全部12种——以树状结构组织，展示它们如何从基本概念发展成更为专业的技术。当然，我们将使用清晰的可视化图像和一致的数据集，展示每种方法的不同之处以及为什么选择方法至关重要。

![](../Images/b1f5d5ea3c85d86aa30c1a32e4af95d6.png)

所有可视化图像：作者使用Canva Pro创建。已优化为移动端显示；在桌面端可能会显得过大。

# 什么是模型验证？

模型验证是测试机器学习模型在未见过或未在训练中使用过的数据上表现如何的过程。基本上，我们使用现有数据来检查模型的表现，而不是使用新的数据。这帮助我们在实际使用模型之前识别问题。

有多种验证方法，每种方法都有其特定的优势，并且解决不同的验证挑战：

1.  不同的验证方法可能会产生不同的结果，因此选择正确的方法很重要。

1.  一些验证技术在特定类型的数据和模型中效果更佳。

1.  使用不正确的验证方法可能会导致关于模型真实表现的误导性结果。

这里有一棵树形图，展示了这些验证方法之间的关系：

![](../Images/ef40a8b199595fb3a2ea907fc7d8c4e7.png)

这棵树形图展示了哪些验证方法相互关联。

接下来，我们将更仔细地研究每种验证方法，展示它们是如何工作的。为了更容易理解，我们将通过清晰的示例，展示这些方法如何在实际数据中运作。

# 📊 📈 我们的运行示例

我们将始终使用相同的示例，帮助你理解每种验证方法。虽然这个数据集可能不适合某些验证方法，但为了教学目的，使用这个示例使得比较不同方法并观察每种方法如何工作的过程更加容易。

## 📊 高尔夫游戏数据集

我们将使用这个数据集，它根据天气条件预测某人是否会打高尔夫。

![](../Images/a76a1336de0cf6952c9aee515376a7ad.png)

列：‘Overcast（独热编码为3列）’，‘Temperature’（以华氏度表示），‘Humidity’（百分比），‘Windy’（是/否）和‘Play’（是/否，目标特征）

```py
import pandas as pd
import numpy as np

# Load the dataset
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

df = pd.DataFrame(dataset_dict)

# Data preprocessing
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)

# Set the label
X, y = df.drop('Play', axis=1), df['Play']
```

## 📈 我们的模型选择

我们将在所有测试中使用[决策树分类器](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e)。如果你不熟悉它，可以参考以下文章：

[](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----eb13bbdc8f88--------------------------------) [## 决策树分类器解析：附带代码示例的视觉指南（面向初学者）

### 对我们最喜欢的倒立树的全新看法

[towardsdatascience.com](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e?source=post_page-----eb13bbdc8f88--------------------------------)

我们选择这个模型是因为我们可以很容易地将结果模型绘制为树形结构，每个分支显示不同的决策。为了简化操作并专注于如何测试模型，我们将使用默认的`scikit-learn`参数，并设置固定的`random_state`。

让我们明确这两个术语：决策树分类器是我们的**学习算法**——它是找到数据中模式的方法。当我们将数据输入该算法时，它会创建一个**模型**（在这种情况下，是一棵显示不同决策的树）。这个模型就是我们实际用来进行预测的模型。

![](../Images/05f04d2e03922330e874044c751e77f9.png)

```py
from sklearn.tree import DecisionTreeClassifier, plot_tree
import matplotlib.pyplot as plt

dt = DecisionTreeClassifier(random_state=42)
```

每次我们以不同的方式拆分数据进行验证时，都会得到不同的模型和不同的决策规则。一旦我们的验证表明算法可靠地工作，我们将使用所有数据创建一个最终模型。这个最终模型就是我们实际用来预测某人是否会打高尔夫的模型。

设置好这一切后，我们现在可以集中精力了解每种验证方法的工作原理，以及它如何帮助我们根据天气条件做出更好的高尔夫球预测。我们将逐一检查每种验证方法。

# 保留法

保留法是检验我们模型效果的最基础方法。在这些方法中，我们基本上将一部分数据专门用于测试。

## 训练-测试拆分

这种方法很简单：我们将数据分成两部分。我们使用一部分来训练模型，另一部分来测试模型。在分割数据之前，我们会随机打乱数据顺序，以确保原始数据的顺序不会影响结果。

训练集和测试集的大小取决于我们的总数据集大小，通常用它们的比例来表示。为了确定它们的大小，您可以遵循以下指导原则：

+   对于小型数据集（大约1,000–10,000个样本），使用80:20的比例。

+   对于中等规模的数据集（大约10,000–100,000个样本），使用70:30的比例。

+   大型数据集（超过100,000个样本），使用90:10的比例。

![](../Images/ea1cda2f5b4ebaf2ac345e82232c49e6.png)

```py
from sklearn.model_selection import train_test_split

### Simple Train-Test Split ###
# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train and evaluate
dt.fit(X_train, y_train)
test_accuracy = dt.score(X_test, y_test)

# Plot
plt.figure(figsize=(5, 5), dpi=300)
plot_tree(dt, feature_names=X.columns, filled=True, rounded=True)
plt.title(f'Train-Test Split (Test Accuracy: {test_accuracy:.3f})')
plt.tight_layout()
```

![](../Images/9b572acdaf081abe2ed17104646ae2ac.png)

这种方法很容易使用，但也有一些局限性 —— 结果可能会因为我们如何随机分割数据而有很大变化。这就是为什么我们总是需要尝试不同的`random_state`来确保结果的一致性。此外，如果我们起初的数据不多，可能没有足够的数据来充分训练或测试我们的模型。

## 训练-验证-测试拆分

这种方法将数据分为三部分。中间部分，称为验证数据，用来调整模型的参数，我们的目标是尽量减少该部分的误差。

由于在调整过程中会多次考虑验证结果，我们的模型可能会在验证数据上表现得太好（这正是我们想要的）。这就是我们为什么要设立单独的测试集的原因。我们只在最后一次测试它 —— 它能真实地反映出我们的模型效果如何。

以下是常见的数据拆分方式：

+   对于较小的数据集（1,000–10,000个样本），使用60:20:20的比例。

+   对于中等规模的数据集（10,000–100,000个样本），使用70:15:15的比例。

+   大型数据集（>100,000个样本），使用80:10:10的比例。

![](../Images/8abee1c3e7b3526152ccf2256108da3f.png)

```py
### Train-Validation-Test Split ###
# First split: separate test set
X_temp, X_test, y_temp, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Second split: separate validation set
X_train, X_val, y_train, y_val = train_test_split(
    X_temp, y_temp, test_size=0.25, random_state=42
)

# Train and evaluate
dt.fit(X_train, y_train)
val_accuracy = dt.score(X_val, y_val)
test_accuracy = dt.score(X_test, y_test)

# Plot
plt.figure(figsize=(5, 5), dpi=300)
plot_tree(dt, feature_names=X.columns, filled=True, rounded=True)
plt.title(f'Train-Val-Test Split\nValidation Accuracy: {val_accuracy:.3f}'
          f'\nTest Accuracy: {test_accuracy:.3f}')
plt.tight_layout()
```

![](../Images/4b6cfed209a3227dfc62eaf3f28413a1.png)

保留法根据数据量的不同会有不同的表现。当你有大量数据（>100,000个样本）时，它效果很好。但当你数据较少（<1,000个样本）时，这种方法可能不是最理想的。在数据较少的情况下，你可能需要使用更高级的验证方法，以便更好地了解你的模型到底有多有效。

## 📊 转向交叉验证

我们刚刚了解到，留出法可能在小数据集上效果不佳。这正是我们目前面临的挑战——我们只有28天的数据。按照留出法原则，我们将保留14天的数据作为最终测试数据。这样，我们剩下14天的数据可以用于尝试其他验证方法。

![](../Images/81a27f280c9b79b4950ec9a9f00ae731.png)

```py
# Initial train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.5, shuffle=False)
```

在接下来的部分，我们将看到交叉验证方法如何将这14天的数据多次划分，并以不同的方式进行测试。这让我们即使在数据有限的情况下，也能更好地了解模型的实际效果。

# 交叉验证

交叉验证改变了我们测试模型的方式。我们不再仅仅用一种数据划分方式测试一次模型，而是通过多次使用相同数据的不同划分来进行测试。这有助于我们更好地理解模型的实际表现。

交叉验证的主要思想是多次测试我们的模型，每次的训练集和测试集都来自我们数据的不同部分。这有助于避免由于数据划分极端（如特别好或特别差）而带来的偏差。

这为什么很重要呢？假设我们的模型在某次测试中得到95%的准确率，而在另一种测试方法下只得到75%的准确率，哪一个结果才是真正反映模型表现的呢？交叉验证通过提供多个测试结果，而不仅仅是一个，帮助我们回答这个问题。这让我们更清楚地了解模型的实际表现。

## K折法

***基础K折交叉验证*** *K*折交叉验证解决了基本数据划分方法的一个大问题：过于依赖单一的数据划分方式。与其只进行一次数据划分，*K*折将数据划分成*K*个相等的部分。然后，它多次测试模型，每次使用不同的部分进行测试，而其他部分则用于训练。

我们选择的*K*数值会影响我们如何测试模型。大多数人使用5或10作为*K*，但这个数值也可以根据我们拥有的数据量和项目需求来调整。假设我们使用*K* = 3。这意味着我们将数据分成三等份。然后我们将模型训练和测试三次。每次，2/3的数据用于训练，1/3的数据用于测试，但每次测试时，所用的测试部分都会不同。这样，每个数据片段都会同时用于训练和测试。

![](../Images/06b5298158f9daf8c0fdf2f24ba9d7f9.png)

```py
from sklearn.model_selection import KFold, cross_val_score

# Cross-validation strategy
cv = KFold(n_splits=3, shuffle=True, random_state=42)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
    # Train and visualize the tree for this split
    dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
    plt.subplot(cv.get_n_splits(X_train), 1, i+1)
    plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
    plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\nTrain indices: {train_idx}\nValidation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率: 0.433 ± 0.047`

![](../Images/3597efd5b0c424bb9ef27f510ef42907.png)

当我们完成所有轮次后，我们会计算所有*K*测试的平均表现。这个平均值为我们提供了一个更可靠的衡量标准，来评估我们的模型表现如何。我们还可以通过观察不同测试轮次之间结果的变化，来了解我们的模型有多稳定。

***分层 K 折*** 基本的 K 折交叉验证通常效果不错，但当我们的数据不平衡时——即某些类型的数据比其他类型多得多——它可能会遇到问题。例如，如果我们有 100 个数据点，其中 90 个是 A 类型，而只有 10 个是 B 类型，随机划分这些数据可能会导致某些划分中没有足够的 B 类型数据来进行合理的测试。

分层 K 折交叉验证通过确保每个数据划分与原始数据的分布相同来解决这个问题。如果我们的完整数据集中有 10% 是 B 类型，那么每个划分也将包含大约 10% 的 B 类型数据。这使得我们的测试更加可靠，特别是在某些数据类型比其他类型稀少时。

![](../Images/6410d2ca1a0a1801423584f4ee9c30dd.png)

```py
from sklearn.model_selection import StratifiedKFold, cross_val_score

# Cross-validation strategy
cv = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(5, 4*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
    # Train and visualize the tree for this split
    dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
    plt.subplot(cv.get_n_splits(X_train), 1, i+1)
    plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
    plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\nTrain indices: {train_idx}\nValidation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率：0.650 ± 0.071`

![](../Images/6a845da93fa64fae2b73609521f534a5.png)

保持这种平衡有两个好处。首先，它确保每个划分能够恰当地代表我们数据的分布。其次，它使得我们的测试结果更加一致。这意味着，如果我们多次测试模型，我们很可能每次都会得到类似的结果。

***重复 K 折*** 有时，即使我们使用了 K 折验证，测试结果在不同的随机划分之间也可能发生较大的变化。重复 K 折通过多次运行整个 K 折过程来解决这个问题，每次使用不同的随机划分。

例如，假设我们运行 5 折交叉验证三次。这意味着我们的模型总共会进行 15 次训练和测试。通过如此多次的测试，我们可以更好地判断结果中的差异是来自随机因素，还是能真正反映出模型的性能。缺点是，所有这些额外的测试需要更多的时间来完成。

![](../Images/f2b033a8e6b90cc1bad5a07059d1457a.png)![](../Images/53d8b614cdb52f63a8289ec002c7dce5.png)

```py
from sklearn.model_selection import RepeatedKFold

# Cross-validation strategy
n_splits = 3
cv = RepeatedKFold(n_splits=n_splits, n_repeats=2, random_state=42)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
total_splits = cv.get_n_splits(X_train)  # Will be 6 (3 folds × 2 repetitions)
plt.figure(figsize=(5, 4*total_splits))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])

   # Calculate repetition and fold numbers
   repetition, fold = i // n_splits + 1, i % n_splits + 1

   plt.subplot(total_splits, 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {repetition}.{fold} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {list(train_idx)}\n'
            f'Validation indices: {list(val_idx)}')

plt.tight_layout()
```

`验证准确率：0.425 ± 0.107`

![](../Images/e94bb4e17347fd027be23da900507dd9.png)

当我们查看重复 K 折结果时，由于我们有很多组测试结果，我们可以做的不仅仅是计算平均值——我们还可以了解我们对结果的信心。这使我们更好地理解模型的可靠性。

***重复分层 K 折*** 这种方法结合了我们刚刚学习的两件事：保持类别平衡（分层）和进行多轮测试（重复）。它在测试多次的同时保持了不同类型数据的正确比例。这在我们的数据集较小且不平衡时尤其有效——例如，当我们有大量一种类型的数据，而其他类型的数据较少时。

![](../Images/f9084cb48d717d1a53b287556171438e.png)![](../Images/9736bf44bf4d82ce511033183bcb338a.png)

```py
from sklearn.model_selection import RepeatedStratifiedKFold

# Cross-validation strategy
n_splits = 3
cv = RepeatedStratifiedKFold(n_splits=n_splits, n_repeats=2, random_state=42)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
total_splits = cv.get_n_splits(X_train)  # Will be 6 (3 folds × 2 repetitions)
plt.figure(figsize=(5, 4*total_splits))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])

   # Calculate repetition and fold numbers
   repetition, fold = i // n_splits + 1, i % n_splits + 1

   plt.subplot(total_splits, 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {repetition}.{fold} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {list(train_idx)}\n'
            f'Validation indices: {list(val_idx)}')

plt.tight_layout()
```

`验证准确率：0.542 ± 0.167`

![](../Images/61517576ff80c26f2c20ca066afe43fd.png)

然而，这种方法有一个权衡：它需要更多的时间来运行。每次我们重复整个过程时，训练模型所需的时间会成倍增加。在决定是否使用这种方法时，我们需要考虑，是否值得花费额外的时间来获得更可靠的结果。

***分组 K 折交叉验证*** 有时，我们的数据自然分为一些应该保持在一起的组。例如，高尔夫数据中，我们可能有来自同一个高尔夫球场的多次测量数据。如果我们将来自一个高尔夫球场的部分测量数据放入训练数据，而其他的放入测试数据，就会出现问题：我们的模型可能会在训练过程中间接了解测试数据，因为它看到了来自同一球场的其他测量数据。

分组 K 折交叉验证通过保持来自同一组的数据（例如来自同一高尔夫球场的所有测量数据）一起划分，来解决这一问题。这可以防止我们的模型在训练过程中无意中看到不应该看到的信息，从而让我们误以为它表现得比实际情况更好。

![](../Images/cde7db87459ae48728b9dd87dd26ac88.png)

```py
# Create groups 
groups = ['Group 1', 'Group 4', 'Group 5', 'Group 3', 'Group 1', 'Group 2', 'Group 4', 
          'Group 2', 'Group 6', 'Group 3', 'Group 6', 'Group 5', 'Group 1', 'Group 4', 
          'Group 4', 'Group 3', 'Group 1', 'Group 5', 'Group 6', 'Group 2', 'Group 4', 
          'Group 5', 'Group 1', 'Group 4', 'Group 5', 'Group 5', 'Group 2', 'Group 6']

# Simple Train-Test Split
X_train, X_test, y_train, y_test, groups_train, groups_test = train_test_split(
    X, y, groups, test_size=0.5, shuffle=False
)

# Cross-validation strategy
cv = GroupKFold(n_splits=3)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv.split(X_train, y_train, groups=groups_train))
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train, groups=groups_train)):
   # Get the groups for this split
   train_groups = sorted(set(np.array(groups_train)[train_idx]))
   val_groups = sorted(set(np.array(groups_train)[val_idx]))

   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(cv.get_n_splits(X_train), 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx} ({", ".join(train_groups)})\n'
            f'Validation indices: {val_idx} ({", ".join(val_groups)})')

plt.tight_layout()
```

`验证准确度: 0.417 ± 0.143`

![](../Images/616c266d6ae7d923873b81a26b0df5de.png)

当我们处理自然分组的数据时，这种方法尤其重要，比如来自同一个高尔夫球场的多次天气数据，或者同一地点在不同时间收集的数据。

***时间序列划分*** 当我们在常规的 K 折交叉验证中随机划分数据时，我们假设每个数据点不会影响其他数据点。但这对于随时间变化的数据并不适用，因为过去发生的事情会影响未来的结果。时间序列划分通过调整 K 折交叉验证，更好地处理这种时间顺序数据。

时间序列划分并非随机分割数据，而是按顺序使用数据，从过去到未来。训练数据仅包括测试数据之前的时间段的信息。这与我们在现实生活中使用模型的方式一致，即我们利用过去的数据来预测未来的事件。

![](../Images/81146fa4c70beaca8801445fd200d3fb.png)

```py
from sklearn.model_selection import TimeSeriesSplit, cross_val_score

# Cross-validation strategy
cv = TimeSeriesSplit(n_splits=3)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(cv.get_n_splits(X_train), 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx}\n'
            f'Validation indices: {val_idx}')

plt.tight_layout()
```

`验证准确度: 0.556 ± 0.157`

![](../Images/4d881beb9b6d1811302457256067f38f.png)

例如，假设*K*=3，并且我们有高尔夫数据。我们可以使用一月和二月的天气数据训练，来预测三月的高尔夫打球模式。接着，我们使用一月到三月的数据来预测四月，依此类推。通过只向前推进时间，这种方法能更真实地反映我们的模型在预测基于天气的未来高尔夫打球模式时的表现。

## 留出法

***留一交叉验证 (LOOCV)*** 留一交叉验证 (LOOCV) 是最彻底的验证方法。它仅使用*一个*样本进行测试，其他所有样本用于训练。验证会重复进行，直到每一条数据都被用作测试。

假设我们有100天的高尔夫天气数据。LOOCV会训练并测试模型100次。每次，它使用99天的数据进行训练，1天的数据进行测试。这种方法消除了测试中的任何随机性——如果你多次在相同的数据上运行LOOCV，你将始终得到相同的结果。

然而，LOOCV需要很长的计算时间。如果你有*N*个数据点，你需要训练模型*N*次。对于大型数据集或复杂模型，这可能需要的时间太长，无法实际使用。一些简单的模型，如线性模型，有一些捷径使得LOOCV变得更快，但并不是所有模型都适用。

![](../Images/52e0f4b1c42428101fe15f6f81637446.png)

```py
from sklearn.model_selection import LeaveOneOut

# Cross-validation strategy
cv = LeaveOneOut()

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(cv.get_n_splits(X_train), 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx}\n'
            f'Validation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率：0.429 ± 0.495`

![](../Images/40709bb360f71992f6218297c2d2242e.png)

LOOCV在数据量不多，需要最大限度利用每一份数据时表现得非常好。由于结果依赖于每一条数据，如果数据中有噪声或异常值，结果可能会有很大变化。

***Leave-P-Out交叉验证*** Leave-P-Out基于Leave-One-Out的思想，但它每次测试时使用P个数据点，而不是仅测试一个数据点。这在Leave-One-Out和K-fold验证之间创造了平衡。我们选择的P值会改变模型的测试方式以及所需的时间。

Leave-P-Out的主要问题是可能的测试组合数量增长得非常快。例如，如果我们有100天的高尔夫天气数据，并且每次测试5天（P=5），那么选择这5天的方式有数百万种不同的组合。当数据量很大或P值较大时，测试所有这些组合会耗费大量时间。

![](../Images/b08884b049867b549153b90e059cd20b.png)

```py
from sklearn.model_selection import LeavePOut, cross_val_score

# Cross-validation strategy
cv = LeavePOut(p=3)

# Calculate cross-validation scores (using all splits for accuracy)
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot first 15 trees
n_trees = 15
plt.figure(figsize=(4, 3.5*n_trees))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   if i >= n_trees:
       break

   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(n_trees, 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx}\n'
            f'Validation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率：0.441 ± 0.254`

![](../Images/b285dc37f93b1968c4e62df28fba57d7.png)

由于这些实际限制，Leave-P-Out通常用于需要非常彻底测试且数据集足够小以使其可行的特殊情况。它在研究项目中尤其有用，在这些项目中，获取最准确的测试结果比测试所需的时间更为重要。

## 随机方法

***ShuffleSplit交叉验证*** ShuffleSplit与其他验证方法不同，它采用完全随机的分割方式。与K-fold按有序方式划分数据，或像Leave-P-Out那样测试所有可能的组合不同，ShuffleSplit每次都会创建随机的训练和测试分割。

ShuffleSplit与K-fold的不同之处在于，分割不遵循任何固定模式。在K-fold中，每条数据都恰好用于一次测试。但在ShuffleSplit中，一天的高尔夫天气数据可能被用于多次测试，也可能根本不被用于测试。这种随机性为我们提供了一种不同的方式来理解模型的表现。

ShuffleSplit 在大数据集上特别有效，而 K-折交叉验证可能需要花费过多时间来运行。我们可以选择测试多少次，无论数据量多大。同时，我们还可以控制每次划分的大小。这让我们能够在全面测试和运行时间之间找到一个良好的平衡。

![](../Images/923156cc72cc526aae6376b4a83d7b24.png)

```py
from sklearn.model_selection import ShuffleSplit, train_test_split

# Cross-validation strategy
cv = ShuffleSplit(n_splits=3, test_size=0.2, random_state=41)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(cv.get_n_splits(X_train), 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx}\n'
            f'Validation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率：0.333 ± 0.272`

![](../Images/8c1642d4aa7fd19870eddc81316dddfa.png)

由于 ShuffleSplit 可以创建任意数量的随机划分，它在我们希望查看模型性能如何随不同的随机划分而变化，或在我们需要更多的测试以确保结果的可靠性时非常有用。

***分层 ShuffleSplit*** 分层 ShuffleSplit 结合了随机划分和保持不同类型数据的正确混合。像分层 K-折交叉验证一样，它确保每个划分的每种类型的数据占比与整个数据集相同。

该方法为我们提供了双赢的局面：既有随机划分的自由，又有保持数据平衡的公平性。例如，如果我们的高尔夫数据集有 70% 的“是”天和 30% 的“否”天，每个随机划分都会尽量保持这一 70-30 的比例。这在数据不均衡时尤其有用，因为随机划分可能会无意中创建不代表我们数据的测试集。

![](../Images/c6f0a11a4d547374bf57dafa1869e61c.png)

```py
from sklearn.model_selection import StratifiedShuffleSplit, train_test_split

# Cross-validation strategy
cv = StratifiedShuffleSplit(n_splits=3, test_size=0.2, random_state=41)

# Calculate cross-validation scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Plot trees for each split
plt.figure(figsize=(4, 3.5*cv.get_n_splits(X_train)))
for i, (train_idx, val_idx) in enumerate(cv.split(X_train, y_train)):
   # Train and visualize the tree for this split
   dt.fit(X_train.iloc[train_idx], y_train.iloc[train_idx])
   plt.subplot(cv.get_n_splits(X_train), 1, i+1)
   plot_tree(dt, feature_names=X_train.columns, impurity=False, filled=True, rounded=True)
   plt.title(f'Split {i+1} (Validation Accuracy: {scores[i]:.3f})\n'
            f'Train indices: {train_idx}\n'
            f'Validation indices: {val_idx}')

plt.tight_layout()
```

`验证准确率：0.556 ± 0.157`

![](../Images/96409b67fddbc5e5715ea4f0bed7f347.png)

然而，保持划分的随机性以及数据类型的正确混合可能会很棘手。该方法有时需要在完全随机和保持完美比例之间做出一些小的妥协。在实际使用中，这些小的折衷很少会引起问题，且通常保持测试集的平衡比拥有完全随机的划分更为重要。

## 🌟 验证技术总结与代码总结

总结来说，模型验证方法分为两大类：留出法和交叉验证法：

**留出法** · 训练-测试分割：最简单的方法，将数据分成两部分

· 训练-验证-测试分割：一种三分法用于更复杂的模型开发

**交叉验证法** 交叉验证法通过多轮验证更好地利用可用数据：

*K-折交叉验证法* 这些方法将数据分为 K 个部分，而不是一个单独的划分：

· 基本 K-折交叉验证：轮流使用不同的测试集

· 分层 K-折交叉验证：保持各个划分中的类别平衡

· 分组 K-折交叉验证：保留数据分组

· 时间序列分割：尊重时间顺序

· 重复 K-折交叉验证

· 重复分层 K-折交叉验证

*留出法* 这些方法将验证推向极限：

· 留 P 法：一次对 P 个数据点进行测试

· 留一法：对单个数据点进行测试

*随机方法* 这些方法引入了受控的随机性：

· ShuffleSplit：重复创建随机划分

· 分层 ShuffleSplit：随机划分且保持类别平衡

```py
import pandas as pd
import numpy as np
from sklearn.tree import DecisionTreeClassifier
from sklearn.model_selection import (
    # Hold-out methods
    train_test_split,
    # K-Fold methods 
    KFold,                   # Basic k-fold
    StratifiedKFold,         # Maintains class balance
    GroupKFold,              # For grouped data
    TimeSeriesSplit,         # Temporal data
    RepeatedKFold,           # Multiple runs
    RepeatedStratifiedKFold, # Multiple runs with class balance
    # Leave-out methods
    LeaveOneOut,             # Single test point
    LeavePOut,               # P test points
    # Random methods
    ShuffleSplit,           # Random train-test splits
    StratifiedShuffleSplit, # Random splits with class balance
    cross_val_score         # Calculate validation score
)

# Load the dataset
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

df = pd.DataFrame(dataset_dict)

# Data preprocessing
df = pd.DataFrame(dataset_dict)
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)
df['Wind'] = df['Wind'].astype(int)

# Set the label
X, y = df.drop('Play', axis=1), df['Play']

## Simple Train-Test Split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.5, shuffle=False,
)

## Train-Test-Validation Split
# First split: separate test set
# X_temp, X_test, y_temp, y_test = train_test_split(
#    X, y, test_size=0.2, random_state=42
# )
# Second split: separate validation set
# X_train, X_val, y_train, y_val = train_test_split(
#    X_temp, y_temp, test_size=0.25, random_state=42
# )

# Create model
dt = DecisionTreeClassifier(random_state=42)

# Select validation method
#cv = KFold(n_splits=3, shuffle=True, random_state=42)
#cv = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
#cv = GroupKFold(n_splits=3) # Requires groups parameter
#cv = TimeSeriesSplit(n_splits=3)
#cv = RepeatedKFold(n_splits=3, n_repeats=2, random_state=42)
#cv = RepeatedStratifiedKFold(n_splits=3, n_repeats=2, random_state=42)
cv = LeaveOneOut()
#cv = LeavePOut(p=3)
#cv = ShuffleSplit(n_splits=3, test_size=0.2, random_state=42)
#cv = StratifiedShuffleSplit(n_splits=3, test_size=0.3, random_state=42)

# Calculate and print scores
scores = cross_val_score(dt, X_train, y_train, cv=cv)
print(f"Validation accuracy: {scores.mean():.3f} ± {scores.std():.3f}")

# Final Fit & Test
dt.fit(X_train, y_train)
test_accuracy = dt.score(X_test, y_test)
print(f"Test accuracy: {test_accuracy:.3f}")
```

`验证准确率: 0.429 ± 0.495`

`测试准确率: 0.714`

***对上述结果的评论:*** 验证准确率和测试准确率之间的巨大差距，以及验证分数中非常高的标准差，表明我们的模型表现不稳定。这种不一致性很可能来源于在我们的小型天气数据集上使用 LeaveOneOut 验证——在单个数据点上进行测试导致性能剧烈波动。使用较大的验证集的不同验证方法可能会给我们带来更可靠的结果。

# 选择正确的验证方法

选择如何验证模型并不简单——不同的情况需要不同的方法。理解使用哪种方法可能意味着获得可靠结果或误导性结果之间的差异。以下是选择验证方法时应该考虑的一些方面：

## 1\. 数据集大小

数据集的大小对选择哪种验证方法最有效有很大的影响。让我们来看一下不同大小的数据集：

**大数据集（超过 100,000 个样本）**

当你有大量数据集时，测试所需的时间成为主要考虑因素之一。简单的保留验证（将数据一次性分为训练集和测试集）通常效果不错，因为你有足够的数据进行可靠的测试。如果需要使用交叉验证，使用 3 折或使用 ShuffleSplit 进行较少轮次的验证可以在不花费太多时间的情况下获得良好的结果。

***中等数据集（1,000 到 100,000 个样本）***

对于中等大小的数据集，常规的 K 折交叉验证效果最佳。使用 5 折或 10 折可以在可靠结果和合理的计算时间之间取得良好的平衡。这种数据量通常足以创建具有代表性的划分，而不会使得测试时间过长。

***小型数据集（少于 1,000 个样本）***

小型数据集，例如我们28天高尔夫记录的例子，需要更仔细的测试。在这种情况下，Leave-One-Out 交叉验证或重复 K 折交叉验证（使用更多折数）实际上可以很好地工作。尽管这些方法的运行时间较长，但在数据量不大的情况下，它们帮助我们获得最可靠的结果。

## 2\. 计算资源

在选择验证方法时，我们需要考虑计算资源。在数据集大小、模型复杂度和所使用的验证方法之间存在三方面的平衡：

**快速训练模型**

像决策树、逻辑回归和线性 SVM 这样的简单模型可以使用更彻底的验证方法，如 Leave-One-Out 交叉验证或重复分层 K 折交叉验证，因为它们训练速度较快。由于每轮训练只需几秒钟或几分钟，我们可以承受多次验证迭代。即使是使用 N 轮训练的 LOOCV，也可能对这些算法来说是可行的。

**资源密集型模型**

深度神经网络、拥有大量树的随机森林或梯度提升模型的训练时间较长。在使用这些模型时，更加密集的验证方法，如重复K折交叉验证或Leave-P-Out，可能不太实际。我们可能需要选择更简单的方法，如基本的K折交叉验证或ShuffleSplit，以保持合理的测试时间。

**内存考虑因素**

一些方法，如K折交叉验证，需要同时跟踪多个数据划分。ShuffleSplit可以帮助解决内存限制问题，因为它一次只处理一个随机划分。对于具有复杂模型（如需要大量内存的深度神经网络）的大规模数据集，可能需要使用更简单的保留方法。如果我们在内存有限的情况下仍需要彻底的验证，可以使用时间序列划分，因为它自然地按顺序处理数据，而不需要一次性将所有划分存储在内存中。

当资源有限时，使用一个我们可以顺利运行的更简单的验证方法（例如基本的K折交叉验证）比尝试运行一个我们无法完成的更复杂方法（例如Leave-P-Out）要好。

## 3\. 类别分布

类别不平衡会强烈影响我们应该如何验证模型。对于不平衡数据，分层验证方法变得至关重要。像分层K折交叉验证和分层ShuffleSplit这样的方式确保每个测试划分与完整数据集的类别分布大致相同。如果不使用这些分层方法，一些测试集可能完全没有某个类别，这样就无法正确测试模型的预测效果。

## 4\. 时间序列

当处理随时间变化的数据时，我们需要特殊的验证方法。常规的随机划分方法效果不佳，因为时间顺序很重要。对于时间序列数据，我们必须使用像时间序列划分（Time Series Split）这样的方式，尊重时间顺序。

## 5\. 群组依赖

许多数据集包含自然的相关数据组。在验证模型时，这些数据中的连接需要特殊处理。当数据点相关时，我们需要使用像Group K-fold这样的方式，以防止我们的模型错误地学习到不该学习的东西。

## 实用指南

这张流程图将帮助你为你的数据选择最合适的验证方法。下面的步骤概述了一个清晰的选择最佳验证方法的过程，前提是你有足够的计算资源。

![](../Images/3c111d0da670ace01dcf36fc6effc876.png)

# 最后的备注

模型验证对于构建可靠的机器学习模型至关重要。在探索了许多验证方法，从简单的训练-测试划分到复杂的交叉验证方法后，我们发现，总有一种适合你的数据的验证方法。

虽然机器学习在不断变化，出现了新的方法和工具，但这些基本的验证规则始终不变。当你很好地理解这些原则时，我相信你会建立起人们可以信任和依赖的模型。

## 深入阅读

关于[验证方法](https://scikit-learn.org/stable/api/sklearn.model_selection.html)的详细解释，读者可以参考官方文档，里面提供了全面的使用和参数说明。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.5。尽管所讨论的概念通常适用，但具体的代码实现可能会因版本不同而有所变化。

## 关于插图

除非另有说明，否则所有图片均由作者创作，并结合了Canva Pro的授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙈𝙤𝙙𝙚𝙡 𝙀𝙫𝙖𝙡𝙪𝙖𝙩𝙞𝙤𝙣 & 𝙊𝙥𝙩𝙞𝙢𝙞𝙯𝙖𝙩𝙞𝙤𝙣 𝙢𝙚𝙩𝙝𝙤𝙙𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----eb13bbdc8f88--------------------------------)

## 模型评估与优化

[查看列表](https://medium.com/@samybaladram/list/model-evaluation-optimization-331287896864?source=post_page-----eb13bbdc8f88--------------------------------)3个故事![](../Images/18fa82b1435fa7d5571ee54ae93a6c62.png)![](../Images/c95e89d05d1de700c631c342cd008de0.png)![](../Images/30e20e1a8ba3ced1e77644b706acd18d.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----eb13bbdc8f88--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----eb13bbdc8f88--------------------------------)8个故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----eb13bbdc8f88--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----eb13bbdc8f88--------------------------------)4个故事![](../Images/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](../Images/22a5d43568e70222eb89fd36789a9333.png)![](../Images/8ea1a2f29053080a5feffc709f5b8669.png)
