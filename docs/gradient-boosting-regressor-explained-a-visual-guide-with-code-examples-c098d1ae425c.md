# 梯度提升回归器详解：带有代码示例的视觉指南

> 原文：[`towardsdatascience.com/gradient-boosting-regressor-explained-a-visual-guide-with-code-examples-c098d1ae425c?source=collection_archive---------1-----------------------#2024-11-14`](https://towardsdatascience.com/gradient-boosting-regressor-explained-a-visual-guide-with-code-examples-c098d1ae425c?source=collection_archive---------1-----------------------#2024-11-14)

## 集成学习

## 一次一个增强阶段地拟合误差

[](https://medium.com/@samybaladram?source=post_page---byline--c098d1ae425c--------------------------------)![Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--c098d1ae425c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c098d1ae425c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c098d1ae425c--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--c098d1ae425c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c098d1ae425c--------------------------------) ·阅读时间 11 分钟·2024 年 11 月 14 日

--

[](/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef?source=post_page-----c098d1ae425c--------------------------------) ## 决策树回归器详解：带有代码示例的视觉指南

### 通过成本复杂度修剪智能地修剪分支

towardsdatascience.com

当然，在机器学习中，我们希望我们的预测非常准确。我们从[简单的决策树](https://medium.com/towards-data-science/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e)开始——它们还不错。然后出现了[随机森林](https://medium.com/towards-data-science/random-forest-explained-a-visual-guide-with-code-examples-9f736a6e1b3c)和[AdaBoost](https://medium.com/towards-data-science/adaboost-classifier-explained-a-visual-guide-with-code-examples-fc0f25326d7b)，效果更好。但梯度提升呢？那简直是一个游戏规则的改变，使得预测变得更加准确。

他们说：“使得梯度提升如此有效的原因其实很简单：它逐个构建模型，每个新模型都专注于修正所有之前模型的错误。这样逐步修正错误的方式就是它特别之处。”我本以为这真的会这么简单，但*每次*我查阅梯度提升，试图理解它是如何工作的时，我看到的却是相同的内容：一行行复杂的数学公式和令人头疼的图表，这些东西总是让我抓狂。试试看吧。

让我们终结这一点，并以一种实际有意义的方式来分解它。我们将通过梯度提升的训练步骤进行可视化导航，专注于回归案例——这是一个比分类更简单的场景——这样我们就可以避免混淆的数学计算。就像多级火箭为了达到轨道而丢弃不必要的重量一样，我们将一项一项地消除那些预测误差。

![](img/2d6078ab2a677250978f6a47f6460221.png)

所有视觉图像：作者使用 Canva Pro 创建。优化为移动设备显示；在桌面上可能显示过大。

## 定义

梯度提升是一种集成机器学习技术，它构建一系列决策树，每棵树旨在纠正前一棵树的错误。与使用浅层树的 AdaBoost 不同，梯度提升使用更深的树作为其弱学习器。每棵新树的目标是最小化残差误差——实际值与预测值之间的差异——而不是直接从原始目标中学习。

对于回归任务，梯度提升逐一添加决策树，每棵新树的训练目标是通过解决当前的残差误差来减少剩余的错误。最终的预测是通过将所有树的输出相加得到的。

模型的优势来自于其加法学习过程——每棵树专注于纠正集成中的剩余错误，而顺序组合的方式使得它成为一个强大的预测器，通过专注于模型仍然难以处理的问题部分，**逐步减少整体预测误差**。

![](img/3ac141fa57a3f0707e680165024c850a.png)

梯度提升是提升家族算法的一部分，因为它是逐一构建树的，每棵新树尝试纠正前一棵树的错误。然而，与其他提升方法不同，梯度提升从优化的角度来解决问题。

## 使用的数据集

在本文中，我们将以经典的高尔夫数据集作为回归的例子。尽管梯度提升可以有效地处理回归和分类任务，但我们将专注于更简单的回归任务——根据天气条件预测来打高尔夫的玩家人数。

![](img/dfe988f34df90e6d6bc95d67f04271db.png)

列：‘阴天（通过一热编码转换为 3 列）’，‘温度’（华氏度），‘湿度’（百分比），‘有风’（是/否）和‘玩家人数’（目标特征）

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

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
```

## 主要机制

以下是梯度提升的工作原理：

1.  **初始化模型：** 从一个简单的预测开始，通常是目标值的平均值。

1.  **迭代学习：** 在设定的迭代次数内，计算残差，训练一棵决策树来预测这些残差，并将新树的预测结果（按学习率缩放）添加到运行总和中。

1.  **在残差上构建决策树：** 每棵新树专注于所有前期迭代中的剩余误差。

1.  **最终预测：** 汇总所有树的贡献（按学习率缩放）和初始预测。

![](img/5aebdd14999e70e5924a74ce7ef351b5.png)

梯度提升回归模型从平均预测开始，通过多棵树进行改进，每棵树都在小步修正前一棵树的错误，直到达到最终预测。

## 训练步骤

我们将遵循标准的梯度提升方法：

1.0\. 设置模型参数：

在构建任何树之前，我们需要设置控制学习过程的核心参数：

· 树的数量（通常为 100，但我们选择 50）按顺序构建，

· 学习率（通常为 0.1），以及

· 每棵树的最大深度（通常为 3）

![](img/11972f0214d401ffdf105a994791fc6b.png)

一棵树的图示，展示了我们的关键设置：每棵树将有 3 个层级，我们将创建 50 棵树，并在每次迭代中以 0.1 的小步前进。

## 对于第一棵树

2.0 对标签进行初始预测。通常这是均值（就像是 a dummy prediction 一样。）

![](img/0583a69027986dee2fda863439108614.png)

为了开始我们的预测，我们使用所有训练数据的平均值（37.43）作为每个案例的第一次猜测。

2.1\. 计算临时残差（或伪残差）：

残差 = 实际值 — 预测值

![](img/5f135dbb9d7034726d771919188d31f6.png)

通过从每个目标值中减去均值预测（37.43）来计算初始残差。

2.2\. 构建决策树以**预测这些残差**。树的构建步骤与[回归树](https://medium.com/towards-data-science/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef)完全相同。

![](img/77364ffaaeca757af50cfa5a758e12b1.png)

第一棵决策树开始训练时，通过寻找特征中的模式，来预测我们初步均值预测的计算残差。

a. 计算根节点的初始均方误差（MSE）

![](img/44736650f521e040f1c4b17edd948edc.png)

就像常规回归树一样，我们计算均方误差（MSE），但这次我们测量的是残差的分布（围绕零），而不是实际值的分布（围绕它们的均值）。

b. 对每个特征：

· 按特征值对数据进行排序

![](img/b2f725b3c389f832749b26326e214e85.png)

对于数据集中的每个特征，我们对其值进行排序并找到潜在的分裂点，正如我们在标准决策树中所做的那样，来确定最好的方式来划分我们的残差。

· 对每个可能的分裂点：

·· 将样本分为左组和右组

·· 计算两个组的均方误差（MSE）

·· 计算这个分裂的均方误差（MSE）减少量

![](img/7082a68606950c5a393205a4e8694ad0.png)

类似于常规的回归树，我们通过计算两组的加权均方误差（MSE）来评估每次划分，但这里我们衡量的是划分后的组如何聚集相似的残差，而不是相似的目标值。

c. 选择能够带来最大 MSE 降低的分裂

![](img/26c4db7c3842cc6336ef543ffe94653d.png)![](img/164adc4d983357b7bd928394075c5cbb.png)

树通过使用“rain”特征（值为 0.5）进行第一次分裂，基于残差将样本分成两组——这个第一次决策将在更深层次的进一步分裂中得到精炼。

d. 继续分裂，直到达到最大深度或每个叶子的最小样本数。

![](img/dfb7d12a8ebb84f85be346d2ab27e286.png)

经历了三层基于不同特征的分裂后，我们的第一棵树创建了八个不同的组，每个组都有自己的残差预测值。

2.3\. 计算叶子节点值

对于每个叶子节点，计算**残差的均值**。

![](img/fbb6b98089b4529f42201e77bbdeca1d.png)

第一棵树的每个叶子节点包含该组残差的平均值——这些值将用于调整和改善我们最初的 37.43 的预测。

2.4\. 更新预测

· 对于**训练数据集**中的每个数据点，基于新树确定它属于哪个叶子节点。

![](img/df18786126f2894b4b244d4fde2b9192.png)

将我们的训练数据通过第一棵树运行时，每个样本根据天气特征沿着自己的路径获取预测残差值，这将帮助修正我们最初的预测。

· 将新树的预测结果乘以学习率，然后将这些缩放后的预测值加到当前模型的预测结果中。这将是更新后的预测。

![](img/76a4aa18c7ed15a837c54901efb09dcc.png)

我们的模型通过采取小步进来更新预测：它只将每个预测残差的 10%（学习率为 0.1）加到我们最初的 37.43 预测值上，从而得到稍微改进的预测。

## 对于第二棵树

2.1\. 基于当前模型计算新的残差

a. 计算目标预测值与当前预测值之间的差异。

这些残差与第一次迭代的残差会略有不同。

![](img/2843e92a51f3b6bd97696bf603c17128.png)

更新了第一棵树的预测后，我们计算新的残差——注意到它们比原来的残差稍微小一些，显示我们的预测逐渐得到了改善。

2.2\. 构建一棵新树来预测这些残差。过程与第一棵树相同，但目标是新的残差。

![](img/bd50d352c28d235ec429312a6fc54dd7.png)

启动我们的第二棵树来预测新的、更小的残差——我们将使用与之前相同的树构建过程，但这次我们试图捕捉第一棵树遗漏的错误。

2.3\. 计算每个叶子节点的均值残差

![](img/b5226feb595f93476c22f6f3acfb4488.png)

第二棵树与第一棵树的结构相同，使用相同的天气特征和分裂点，但其叶节点的值较小——这表明我们正在微调剩余的误差。

2.4\. 更新模型预测

· 将新树的预测乘以学习率。

· 将新缩放过的树预测加到当前总和中。

![](img/269aeb6fbb665e5f8d830fff8afb22f0.png)

在将数据通过第二棵树后，我们再次以 0.1 的学习率做出小步调整以更新预测，并计算出比之前更小的残差——我们的模型正在逐渐学习模式。

## 从第三棵树开始

对剩余的迭代重复步骤 2.1–2.3。注意，每棵树看到的残差不同。

· 决策树逐渐专注于更难预测的模式

· 学习率通过限制每棵树的贡献来防止过拟合

![](img/c3bec57f3cfc4d79ab75c562246a8250.png)

随着我们构建更多的树，注意分裂点如何逐渐变化，叶节点中的残差值变得更小——到第 50 棵树时，我们通过不同的特征组合进行微调，调整的幅度比最初的树要小。

```py
from sklearn.tree import plot_tree
import matplotlib.pyplot as plt
from sklearn.ensemble import GradientBoostingRegressor

# Train the model
clf = GradientBoostingRegressor(criterion='squared_error', learning_rate=0.1, random_state=42)
clf.fit(X_train, y_train)

# Plot trees 1, 2, 49, and 50
plt.figure(figsize=(11, 20), dpi=300)

for i, tree_idx in enumerate([0, 2, 24, 49]):
    plt.subplot(4, 1, i+1)
    plot_tree(clf.estimators_[tree_idx,0], 
              feature_names=X_train.columns,
              impurity=False,
              filled=True, 
              rounded=True,
              precision=2,
              fontsize=12)
    plt.title(f'Tree {tree_idx + 1}')

plt.suptitle('Decision Trees from GradientBoosting', fontsize=16)
plt.tight_layout(rect=[0, 0.03, 1, 0.95])
plt.show()
```

![](img/8a98aab772c6bdcf801764e343cb4c17.png)

来自 scikit-learn 的可视化展示了我们的梯度提升树如何演变：从树 1 进行大范围的分裂并给出大预测值，到树 50 进行精细的分裂并做出微小的调整——每棵树都专注于修正前面树所产生的剩余误差。

## 测试步骤

预测时：

a. 从初始预测开始（玩家的平均数量）

b. 将输入数据传递给每棵树以获得其预测的调整值

c. 按照学习率缩放每棵树的预测值。

d. 将所有这些调整添加到初始预测中

e. 这些和直接给出我们预测的玩家数量

![](img/4b7ce876ba3cdfad96da7c7e0415989f.png)

在对未见数据进行预测时，每棵树都会贡献一个小的预测值，从树 1 的 5.57 开始，到树 50 的 0.008——所有这些预测都被我们的 0.1 学习率进行缩放，并加到我们的基础预测值 37.43 上，得到最终的答案。

## 评估步骤

构建所有树后，我们可以评估测试集。

![](img/32381bf34c6c5780246b6047af2863bd.png)

我们的梯度提升模型达到了 4.785 的 RMSE，相较于[a 棵回归树的 5.27](https://medium.com/towards-data-science/decision-tree-regressor-explained-a-visual-guide-with-code-examples-fbd2836c3bef)有了显著的提升——这表明将多个小的调整组合起来，比单棵复杂的树更能做出准确的预测！

```py
# Get predictions
y_pred = clf.predict(X_test)

# Create DataFrame with actual and predicted values
results_df = pd.DataFrame({
    'Actual': y_test,
    'Predicted': y_pred
})
print(results_df) # Display results DataFrame

# Calculate and display RMSE
from sklearn.metrics import root_mean_squared_error
rmse = root_mean_squared_error(y_test, y_pred)
print(f"\nModel Accuracy: {rmse:.4f}")
```

## 关键参数

这里是梯度提升中的关键参数，特别是在`scikit-learn`中：

`max_depth`：用于建模残差的树的深度。与使用树桩的 AdaBoost 不同，梯度提升在深层树（通常为 3-8 层）上效果更好。深层树能够捕捉更复杂的模式，但也有过拟合的风险。

`n_estimators`：要使用的树的数量（通常为 100-1000）。当与较小的学习率配对时，更多的树通常能提高性能。

`learning_rate`：也称为“收缩”，用于缩放每棵树的贡献（通常为 0.01-0.1）。较小的值需要更多的树，但通过使学习过程更精细化，通常能获得更好的结果。

`subsample`：用于训练每棵树的样本比例（通常为 0.5-0.8）。这个可选特性增加了随机性，可以提高鲁棒性并减少过拟合。

这些参数是相互配合工作的：较小的学习率需要更多的树，而较深的树可能需要较小的学习率以避免过拟合。

## 与 AdaBoost 的关键区别

AdaBoost 和 Gradient Boosting 都是提升算法，但它们从错误中学习的方式不同。以下是它们的关键区别：

1.  `max_depth` 通常在 Gradient Boosting 中较高（3-8），而 AdaBoost 更倾向于使用树桩。

1.  没有 `sample_weight` 更新，因为 Gradient Boosting 使用残差而不是样本加权。

1.  `learning_rate` 通常比 AdaBoost 的较大值（0.1-1.0）小得多（0.01-0.1）。

1.  初始预测从均值开始，而 AdaBoost 从零开始。

1.  树是通过简单的加法而不是加权投票来组合的，这使得每棵树的贡献更加直观。

1.  可选的 `subsample` 参数增加了随机性，这是标准 AdaBoost 所没有的特性。

# 优点与缺点

## 优点：

+   **逐步错误修正：** 在 Gradient Boosting 中，每棵新树专注于修正前一棵树的错误。这使得模型在之前错误的区域更好地改进预测。

+   **灵活的误差度量：** 与 AdaBoost 不同，Gradient Boosting 可以优化不同类型的误差度量（如平均绝对误差、均方误差等）。这使得它可以适应各种问题。

+   **高准确度：** 通过使用更详细的树并仔细控制学习率，Gradient Boosting 往往能提供比其他算法更准确的结果，尤其是对于结构良好的数据。

## 缺点：

+   **过拟合的风险：** 使用更深的树和顺序构建过程可能导致模型过度拟合训练数据，从而降低在新数据上的表现。这需要仔细调整树的深度、学习率和树的数量。

+   **训练过程缓慢：** 和 AdaBoost 一样，树必须一个接一个地构建，因此相比于可以并行构建树的算法（如随机森林），训练速度较慢。每棵树都依赖于前一棵树的错误。

+   **高内存使用：** 由于需要更深和更多的树，Gradient Boosting 的内存消耗可能比像 AdaBoost 这样的简单提升方法更高。

+   **对设置敏感：** 梯度提升的有效性在很大程度上取决于找到合适的学习率、树的深度和树的数量的组合，这可能比调优简单算法更复杂且耗时。

# 结语

梯度提升（Gradient Boosting）是提升算法的一项重要改进。这一成功催生了像 XGBoost 和 LightGBM 这样的流行版本，它们在机器学习竞赛和实际应用中得到了广泛使用。

尽管梯度提升比简单算法需要更精细的调优——尤其是在调整决策树深度、学习率和树的数量时——它非常灵活且强大。这使得它成为结构化数据问题的首选方法。

梯度提升能够处理简单方法（如 AdaBoost）可能忽视的复杂关系。其持续的流行和不断的改进表明，使用梯度并逐步构建模型的方法在现代机器学习中依然极为重要。

# 🌟 梯度提升回归器代码总结

```py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import root_mean_squared_error
from sklearn.ensemble import GradientBoostingRegressor

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

# Train Gradient Boosting
gb = GradientBoostingRegressor(
   n_estimators=50,     # Number of boosting stages (trees)
   learning_rate=0.1,    # Shrinks the contribution of each tree
   max_depth=3,          # Depth of each tree
   subsample=0.8,        # Fraction of samples used for each tree
   random_state=42
)
gb.fit(X_train, y_train)

# Predict and evaluate
y_pred = gb.predict(X_test)
rmse = root_mean_squared_error(y_test, y_pred))

print(f"Root Mean Squared Error: {rmse:.2f}")
```

## 进一步阅读

对于[梯度提升回归器（GradientBoostingRegressor）](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingRegressor.html)及其在 scikit-learn 中的实现的详细解释，读者可以参考官方文档，该文档提供了有关其使用和参数的全面信息。

## 技术环境

本文使用的是 Python 3.7 和 scikit-learn 1.6 版本。虽然所讨论的概念具有普遍适用性，但具体的代码实现可能会因版本不同而略有差异。

## 关于插图

除非另有说明，所有图片均由作者创作，包含来自 Canva Pro 的授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙀𝙣𝙨𝙚𝙢𝙗𝙡𝙚 𝙇𝙚𝙖𝙧𝙣𝙞𝙣𝙜 𝙝𝙚𝙧𝙚:

![Samy Baladram](img/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----c098d1ae425c--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----c098d1ae425c--------------------------------)4 篇故事![](img/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](img/22a5d43568e70222eb89fd36789a9333.png)![](img/8ea1a2f29053080a5feffc709f5b8669.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](img/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----c098d1ae425c--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----c098d1ae425c--------------------------------)5 个故事![一只戴着粉色帽子、扎着双马尾的卡通娃娃。这个“假人”娃娃，凭借其简化的设计和心形装饰的衣服，形象地表现了机器学习中的“假回归器”概念。就像这个玩具般的角色是一个简化的、静态的人物表现一样，假回归器也是一个基础模型，作为更复杂分析的基准。](img/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](img/44e6d84e61c895757ff31e27943ee597.png)![](img/7f3e5f3e2aca2feec035ca92e1bc440a.png)
