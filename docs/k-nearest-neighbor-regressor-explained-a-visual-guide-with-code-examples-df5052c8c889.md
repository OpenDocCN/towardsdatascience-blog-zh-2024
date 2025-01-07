# K最近邻回归器，解释：带代码示例的视觉指南

> 原文：[https://towardsdatascience.com/k-nearest-neighbor-regressor-explained-a-visual-guide-with-code-examples-df5052c8c889?source=collection_archive---------1-----------------------#2024-10-07](https://towardsdatascience.com/k-nearest-neighbor-regressor-explained-a-visual-guide-with-code-examples-df5052c8c889?source=collection_archive---------1-----------------------#2024-10-07)

## 回归算法

## 使用KD树和Ball树快速寻找邻居

[](https://medium.com/@samybaladram?source=post_page---byline--df5052c8c889--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--df5052c8c889--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--df5052c8c889--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--df5052c8c889--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--df5052c8c889--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--df5052c8c889--------------------------------) ·阅读时间11分钟·2024年10月7日

--

[](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1?source=post_page-----df5052c8c889--------------------------------) [## K近邻分类器，解释：带代码示例的初学者视觉指南

### 机器学习中的友好邻居方法

[towardsdatascience.com](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1?source=post_page-----df5052c8c889--------------------------------)

在我们探讨了[最近邻分类器](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1)后，让我们转向回归领域中的“兄弟”——最近邻回归器。最近邻回归器将相同的直观概念应用于预测连续值。但是，随着数据集的增大，如何高效地找到这些邻居变得非常麻烦。这就是KD树和Ball树派上用场的地方。

令人沮丧的是，目前没有明确的指南能够真正解释这些算法的运作原理。当然，有一些二维可视化图像，但它们通常无法清楚地展示树在多维环境中的工作方式。

在这里，我们将解释这些算法**实际**是如何运作的，而不使用过于简化的二维表示。我们将专注于树的构建过程，并看看哪些计算（和数字）实际上是重要的。

![](../Images/00c7b6d82e926d5ce08ebc77977eab63.png)

所有可视化：作者使用Canva Pro创建，针对移动设备进行了优化；在桌面上可能显示过大。

# 定义

最近邻回归器是一个直接的预测模型，通过平均附近数据点的结果来估算值。该方法基于相似输入可能产生相似输出的理念。

![](../Images/aba12c7b4e3b510a0d187544f9510e12.png)

最近邻方法是机器学习工具包中最基础且强大的技术之一。它们的简单性掩盖了它们在许多实际场景中的有效性。

# 📊 使用的数据集

为了说明我们的概念，我们将使用[我们通常的数据集](/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)。这个数据集有助于预测每天的高尔夫球手数量。它包括天气状况、温度、湿度和风速等因素。我们的目标是基于这些变量估算每日高尔夫球手的到场人数。

![](../Images/8f70777fcdcfbe631c352c17a649d816.png)

列：‘Outlook’（天气状况），‘Temperature’（温度，单位华氏度），‘Humidity’（湿度，单位%），‘Wind’（风速，是否有风），以及‘Number of Players’（玩家数量，数值型，目标特征）

为了有效地使用最近邻回归，我们需要对数据进行预处理。这包括[将分类变量转换为数值格式](/encoding-categorical-data-explained-a-visual-guide-with-code-example-for-beginners-b169ac4193ae)和[缩放数值特征](/scaling-numerical-data-explained-a-visual-guide-with-code-examples-for-beginners-11676cdb45cb)。

![](../Images/856bb5024939a0acbe57928f62346faa.png)

对‘Temperature’（温度）和‘Humidity’（湿度）应用标准缩放，而对‘Outlook’（天气状况）和‘Wind’（风速）应用独热编码。

```py
# Import libraries
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import root_mean_squared_error
from sklearn.neighbors import KNeighborsRegressor
from sklearn.preprocessing import StandardScaler
from sklearn.compose import ColumnTransformer

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
df = pd.get_dummies(df, columns=['Outlook'])

# Convert 'Wind' column to binary
df['Wind'] = df['Wind'].astype(int)

# Split data into features and target, then into training and test sets
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Identify numerical columns
numerical_columns = ['Temperature', 'Humidity']

# Create a ColumnTransformer to scale only numerical columns
ct = ColumnTransformer([
    ('scaler', StandardScaler(), numerical_columns)
], remainder='passthrough')

# Fit the ColumnTransformer on the training data and transform both training and test data
X_train_transformed = ct.fit_transform(X_train)
X_test_transformed = ct.transform(X_test)

# Convert the transformed data back to DataFrames
feature_names = numerical_columns + [col for col in X_train.columns if col not in numerical_columns]
X_train_scaled = pd.DataFrame(X_train_transformed, columns=feature_names, index=X_train.index)
X_test_scaled = pd.DataFrame(X_test_transformed, columns=feature_names, index=X_test.index)
```

# 主要机制

最近邻回归器的工作原理类似于其分类器对等物，但它不是对类别进行投票，而是对目标值进行平均。基本过程如下：

1.  计算新数据点与训练集中所有点之间的距离。

1.  根据这些距离选择K个最近邻居。

1.  计算这些K个邻居的目标值的平均值。

1.  将这个平均值作为新数据点的预测值。

![](../Images/250524333fd9efd70666c9f0c773da69.png)

上述方法，即使用所有数据点来寻找邻居，称为**暴力搜索**方法。它简单直接，但对于大规模数据集来说可能比较慢。

在这里，我们将探讨两种更高效的寻找最近邻居的算法：

# **KNN回归的KD树**

KD树（K维树）是一种二叉树结构，用于在*k*维空间中组织点。它特别适用于诸如最近邻搜索和多维数据范围搜索等任务。

## **训练步骤：**

1. 构建KD树：

a. 从包含所有点的根节点开始。

![](../Images/f9eb3bb6d235cd561bf67f214fa3586f.png)

b. 选择一个特征进行划分。实际上，选择任何一个随机特征都可以，但另一种较好的选择方式是查看哪个特征的中位数值最接近最大值与最小值之间的中点。

![](../Images/79a398331b63176a9e9084603cb200e9.png)

温度具有最接近中位线的中点线。我们可以从该维度开始进行拆分。

c. 在选定的特征和中点处分割树。

![](../Images/c1a195c9003c18519a00a688f662db95.png)

d. 递归地执行步骤a-c，直到达到停止标准，通常是最小叶子节点大小（见[这里的“最小样本叶子”](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e/)）

![](../Images/cfad5e8688c34aba173a931f66dbd614.png)

2\. 存储目标值：

在KD树中的每个点旁边存储其对应的目标值。每个节点的最小值和最大值也被存储。

![](../Images/3cf4c4165760cabc63946fa1328de2c8.png)

## **回归/预测步骤：**

1\. 遍历KD树：

a. 从根节点开始。

b. 比较查询点（测试点）与每个节点处的分割维度和值。

c. 根据比较结果递归地向左或向右遍历。

d. 当到达叶子节点时，将其点添加到候选集。

![](../Images/b6ac7ebb18046966c3e7886af47a307e.png)

2\. 精细化搜索：

a. 回溯树结构，检查其他分支是否有更接近的点。

b. 使用未探索分支的最大值和最小值进行距离计算，以确定是否需要继续探索。

![](../Images/d7b70deaeed99a10b64698e7be1c231e.png)

我们回溯到未访问的分支，并检查那些节点的最小值和最大值的距离。

![](../Images/dfb1b7b1b636bee05aa2c2ce53805785.png)

由于这些节点的最小值和最大值都比第k个距离远，因此无需检查这些节点中数据点的距离。

3\. 查找K个最近邻：

a. 在找到的候选点中，选择与查询点最接近的K个点。

4\. 执行回归：

a. 计算K个最近邻的目标值的平均值。

b. 这个平均值即为查询点的预测值。

![](../Images/73271b1d2aa3434168ed7b3c7b85b162.png)

通过使用KD树，找到最近邻的平均时间复杂度可以从暴力搜索法中的*O*(*n*)减少到在许多情况下的*O*(log *n*)，其中*n*是数据集中的点数。这使得KNN回归在大数据集中的效率大大提高。

# **KNN回归中的球树**

球树是另一种空间划分数据结构，它通过一系列嵌套的超球面组织点。在高维数据中，KD树可能变得低效，而球树在这种情况下尤为有效。

**训练步骤：**

1\. 构建球树：

a. 计算节点中所有点的重心（均值）。这将成为**枢纽点**。

![](../Images/f4e68143b7ec7469a2c23cc8f59c3d50.png)

b. 构建第一个分支：

- **找到第一个中心：** 选择距离枢纽点最远的点作为第一个中心，并将其距离作为**半径**。

- **寻找第二个中心：** 从第一个中心出发，选择最远的点作为第二个中心。

- **分区：** 根据点更接近哪个中心将剩余的点分成两个子节点。

![](../Images/3f318c1ad3e5909f1299034607cbb9d7.png)

递归地对每个子节点应用步骤a-b，直到满足停止标准，通常是最小叶子节点大小（请参见[“最小样本叶子”](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e/)）。

![](../Images/7a2c7c38531c763643bd4d7f179f03e4.png)![](../Images/466452b8a11ac4c4308f57d663d4c728.png)

2\. 存储目标值：

随着每个Ball Tree中的点，存储其对应的目标值。每个节点的半径和质心也会被存储。

![](../Images/9883a4e7158dce6fa0413c88d60c6432.png)

**回归/预测步骤：**

1\. 遍历Ball Tree：

a. 从根节点开始。

b. 在每个节点上，计算未见数据与每个子超球中心之间的距离。

c. 首先遍历到最接近的超球。

d. 当到达叶节点时，将其点添加到候选集。

![](../Images/85e4feaae0d4811c3cc6f20839783835.png)

2\. 精细化搜索：

a. 确定是否需要探索其他分支。

b. 如果到超球的距离加上其半径大于当前第K个最近距离，则可以安全忽略该分支。

![](../Images/a55da95adf34346e7d47fb4f5f316f47.png)

对于之前考虑过的分支，将半径加到距离上。如果该距离大于当前的第K个距离，则无需探索这些球。

![](../Images/0b1dfe1b420ff207edb0a413d4f3f131.png)

3\. 查找K个最近邻：

a. 从找到的候选点中，选择与查询点最接近的K个点。

4\. 执行回归：

a. 计算K个最近邻的目标值平均值。

b. 这个平均值就是查询点的预测值。

![](../Images/e2b2f49da2d913d67e259e3e5fa857fb.png)

Ball Tree对于高维数据或维度大于样本数量对数的情况比KD Tree更高效。即使维度增加，它们也能保持良好的性能，适用于广泛的数据集。

在Ball Tree中查询的时间复杂度通常是O(log *n*)，与KD Tree相似，但在高维度下，Ball Tree通常表现得更好，而KD Tree可能退化为线性时间复杂度。

# 评估步骤（暴力法、KD树、Ball树）

无论我们选择哪种算法，它们都会给出相同的结果：

![](../Images/ac80599b3efec37df8746639217d7aea.png)

与[虚拟回归器的结果](https://medium.com/towards-data-science/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629)相比，RMSE值有了显著改善。

# 选择哪种算法？

你可以遵循这个简单的规则来选择最优的算法：

- 对于小型数据集（< 1000样本），‘brute’可能足够快，并保证找到精确的最近邻。

- 对于特征较少的大型数据集（< 20），‘kd_tree’通常是最快的。

- 对于具有许多特征的大型数据集，‘ball_tree’通常表现最佳。

scikit-learn中的‘auto’选项通常遵循以下图表：

![](../Images/cdea807e4c32673c58a2daa66594870a.png)

# 关键参数

虽然KNN回归有许多其他参数，除了我们刚刚讨论的算法（暴力法、kd树、ball树），但你主要需要考虑的是

1.  **邻居数（K）**

    - 较小的K：对局部模式更敏感，但可能导致过拟合。

    - 较大的K：平滑的预测，但可能错过重要的局部变化。

    与分类不同，**回归中偶数也可以**，因为我们是在对值进行平均。

1.  **叶节点大小**

    这是构建kd树或ball树的停止条件。通常，它会影响构建和查询的速度，以及存储树所需的内存。

# 优缺点

## 优点：

1.  **简洁性和多功能性**：易于理解和实现；可以用于分类和回归任务。

1.  **无假设**：不对数据分布做任何假设，使其适用于复杂数据集。

1.  **无训练阶段**：可以快速纳入新数据，而无需重新训练。

1.  **可解释性**：通过检查最近邻，可以解释预测结果。

## **缺点：**

1.  **计算复杂度**：预测时间可能较慢，尤其是对于大数据集，尽管优化算法（KD-Tree、Ball Tree）在低维情况下能有所帮助。

1.  **维度灾难**：在高维空间中，性能会下降，影响准确性和效率。

1.  **内存密集型**：需要存储所有训练数据。

1.  **对特征缩放和无关特征敏感**：可能会受到较大尺度特征或对预测无关特征的偏倚。

# 最终备注

K最近邻（KNN）回归器是机器学习中一个基本而强大的工具。它简单直观，非常适合初学者，而且其灵活性确保它对专家同样有用。

随着你对数据分析的了解逐渐深入，可以使用KNN来理解回归的基础知识，然后再探索更高级的方法。通过掌握KNN和如何计算最近邻，你将为处理更复杂的数据分析挑战打下坚实的基础。

# 🌟 K最近邻回归器代码概述

```py
# Import libraries
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import root_mean_squared_error
from sklearn.neighbors import KNeighborsRegressor
from sklearn.preprocessing import StandardScaler
from sklearn.compose import ColumnTransformer

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
df = pd.get_dummies(df, columns=['Outlook'])

# Convert 'Wind' column to binary
df['Wind'] = df['Wind'].astype(int)

# Split data into features and target, then into training and test sets
X, y = df.drop(columns='Num_Players'), df['Num_Players']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Identify numerical columns
numerical_columns = ['Temperature', 'Humidity']

# Create a ColumnTransformer to scale only numerical columns
ct = ColumnTransformer([
    ('scaler', StandardScaler(), numerical_columns)
], remainder='passthrough')

# Fit the ColumnTransformer on the training data and transform both training and test data
X_train_transformed = ct.fit_transform(X_train)
X_test_transformed = ct.transform(X_test)

# Convert the transformed data back to DataFrames
feature_names = numerical_columns + [col for col in X_train.columns if col not in numerical_columns]
X_train_scaled = pd.DataFrame(X_train_transformed, columns=feature_names, index=X_train.index)
X_test_scaled = pd.DataFrame(X_test_transformed, columns=feature_names, index=X_test.index)

# Initialize and train KNN Regressor
knn = KNeighborsRegressor(n_neighbors=5, 
                          algorithm='kd_tree', #'ball_tree', 'brute'
                          leaf_size=5) #default is 30
knn.fit(X_train_scaled, y_train)

# Make predictions
y_pred = knn.predict(X_test_scaled)

# Calculate and print RMSE
rmse = root_mean_squared_error(y_test, y_pred)
print(f"RMSE: {rmse:.4f}")
```

## 进一步阅读

有关[KNeighborsRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsRegressor.html)、[KDTree](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KDTree.html)、[BallTree](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.BallTree.html)及其在scikit-learn中的实现的详细说明，读者可以参考其官方文档。它提供了关于这些工具的使用及参数的全面信息。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.5版本。虽然讨论的概念普遍适用，但不同版本之间的具体代码实现可能会略有不同。

## 关于插图

除非另有说明，所有图片均由作者创建，并融合了来自Canva Pro的授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙍𝙚𝙜𝙧𝙚𝙨𝙨𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----df5052c8c889--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----df5052c8c889--------------------------------)5篇故事![一只扎着辫子戴着粉红色帽子的卡通娃娃。这个“假人”娃娃，凭借其基本设计和心形装饰的衬衫，形象地展示了机器学习中的假回归器概念。就像这个玩具般的形象是一个简化的、静态的人物表达一样，假回归器也是一个基本模型，用作更复杂分析的基准。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----df5052c8c889--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----df5052c8c889--------------------------------)8篇故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
