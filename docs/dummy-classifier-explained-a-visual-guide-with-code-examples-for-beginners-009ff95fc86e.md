# 虚拟分类器详解：面向初学者的视觉指南与代码示例

> 原文：[https://towardsdatascience.com/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e?source=collection_archive---------3-----------------------#2024-08-14](https://towardsdatascience.com/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e?source=collection_archive---------3-----------------------#2024-08-14)

## 分类算法

## 在机器学习中，通过简单的基线模型设定标准

[](https://medium.com/@samybaladram?source=post_page---byline--009ff95fc86e--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--009ff95fc86e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--009ff95fc86e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--009ff95fc86e--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--009ff95fc86e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--009ff95fc86e--------------------------------) ·阅读时间：7分钟·2024年8月14日

--

![](../Images/7d5f5043f52cd1660f09ce3ca7c6cdca.png)

`⛳️ 更多分类算法，详解： ▶ [虚拟分类器](/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e) · [K近邻分类器](/k-nearest-neighbor-classifier-explained-a-visual-guide-with-code-examples-for-beginners-a3d85cad00e1) · [伯努利朴素贝叶斯](/bernoulli-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-aec39771ddd6) · [高斯朴素贝叶斯](/gaussian-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-04949cef383c) · [决策树分类器](/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e) · [逻辑回归](/logistic-regression-explained-a-visual-guide-with-code-examples-for-beginners-81baf5871505) · [支持向量分类器](/support-vector-classifier-explained-a-visual-guide-with-mini-2d-dataset-62e831e7b9e9) · [多层感知机](/multilayer-perceptron-explained-a-visual-guide-with-mini-2d-dataset-0ae8100c5d1c)`

你是否曾经想过数据科学家是如何衡量他们的机器学习模型性能的？让我们来看一下虚拟分类器——一个简单却强大的数据科学工具。可以把它看作是游戏中的基线玩家，设定了其他更复杂模型需要超越的最低标准。

![](../Images/0a0897b06ce54ce8e54d961956cd7d50.png)

所有视觉效果：作者使用Canva Pro制作。优化了移动端显示；在桌面端可能显示过大。

# 定义

虚拟分类器是一个简单的机器学习模型，它使用基本规则进行预测，而不是真正地从输入数据中学习。它作为一个基准，用于比较更复杂模型的性能。虚拟分类器帮助我们了解我们的复杂模型是否真的在学习有用的模式，还是只是在猜测。

![](../Images/834c5b63a06e05517583e5ef01f746fd.png)

虚拟分类器是机器学习中的基础关键算法之一。

# 📊 数据集与库

在本文中，我们将使用这个简单的人工高尔夫数据集（灵感来源于[1]）作为示例。这个数据集根据天气条件预测一个人是否会打高尔夫。它包括如展望、温度、湿度和风等特征，目标变量是是否打高尔夫。

![](../Images/e66ab987f34572e8d046e05b218444ab.png)

列：‘展望’、‘温度’、‘湿度’、‘风’ 和 ‘打球’（目标特征）

```py
# Import libraries
from sklearn.model_selection import train_test_split
import pandas as pd

# Make a dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}
df = pd.DataFrame(dataset_dict)

# One-hot Encode 'Outlook' Column
df = pd.get_dummies(df, columns=['Outlook'],  prefix='', prefix_sep='', dtype=int)

# Convert 'Windy' (bool) and 'Play' (binary) Columns to 0 and 1
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Set feature matrix X and target vector y
X, y = df.drop(columns='Play'), df['Play']

# Split the data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)
```

# 主要机制

虚拟分类器依靠简单的策略做出预测。这些策略不涉及从数据中进行实际学习，而是使用诸如以下的基本规则：

1.  始终预测最常见的类别

1.  基于训练集的类别分布随机预测一个类别

1.  始终预测一个特定类别

![](../Images/e1b4e2a1f75224a4bcd2ba1463a3f662.png)

对于我们的高尔夫数据集，如果“是”——即打高尔夫是训练数据中最常见的结果，那么一个虚拟分类器可能始终预测“是”。

# 训练步骤

虚拟分类器的“训练”过程非常简单，并不涉及通常的学习算法。以下是一般的概述：

## 1\. 选择策略

选择以下策略之一：

+   **分层**：基于原始类别分布进行随机猜测。

+   **最频繁**：始终选择最常见的类别。

+   **均匀**：随机选择任何类别。

![](../Images/193f2e00ddd807b5bfa47b80495d4bf2.png)

根据策略的不同，虚拟分类器会做出不同的预测。

```py
from sklearn.dummy import DummyClassifier

# Choose a strategy for your DummyClassifier (e.g., 'most_frequent', 'stratified', etc.)
strategy = 'most_frequent'
```

## 2\. 收集训练标签

从训练数据集收集类别标签以确定策略参数。

![](../Images/958c4951d13c4f031f4c031406d4d28d.png)

该算法只是获取训练数据集中“最频繁”类别的信息——在这个例子中是“是”。

```py
# Initialize the DummyClassifier
dummy_clf = DummyClassifier(strategy=strategy)

# "Train" the DummyClassifier (although no real training happens)
dummy_clf.fit(X_train, y_train)
```

## 3\. 将策略应用于测试数据

使用选择的策略**生成测试数据的预测标签列表**。

![](../Images/7fef1b9ca434ce80d241417b6b352ff5.png)

如果我们选择“最频繁”策略，并发现“是”（打高尔夫）在训练数据中出现得更频繁，虚拟分类器将简单地记住始终预测“是”。

```py
# Use the DummyClassifier to make predictions
y_pred = dummy_clf.predict(X_test)
print("Label     :",list(y_test))
print("Prediction:",list(y_pred))
```

## 评估模型

![](../Images/1215f23c0f13b608c805f8ade38364ad.png)

虚拟分类器提供64%的准确率作为未来模型的基准。

```py
# Evaluate the DummyClassifier's accuracy
from sklearn.metrics import accuracy_score

accuracy = accuracy_score(y_test, y_pred)
print(f"Dummy Classifier Accuracy: {round(accuracy,4)*100}%")
```

# 关键参数

虽然虚拟分类器很简单，但它们确实有一些重要的参数：

1.  **策略**：这决定了分类器如何做出预测。常见的选项包括：

    - ‘最常见’：始终预测训练集中的最常见类别。

    - ‘分层’：根据训练集的类别分布生成预测。

    - ‘均匀’：生成均匀随机的预测。

    - ‘常量’：始终预测指定的类别。

1.  **随机状态**：如果使用涉及随机性的策略（如‘分层’或‘均匀’），此参数确保结果的可重复性。

1.  **常量**：使用‘常量’策略时，此参数指定始终预测的类别。

![](../Images/da1d5ccd4035c1ab005bddf2f648b443.png)

对于我们的高尔夫数据集，我们可能会选择‘最常见’策略，它不需要额外的参数。

# 优缺点

与机器学习中的任何工具一样，虚拟分类器有其优点和局限性。

**优点：**

1.  **简洁性**：易于理解和实现。

1.  **基线表现**：为其他模型提供最低表现基准。

1.  **过拟合检查**：通过将复杂模型的表现与虚拟分类器进行比较，帮助识别过拟合情况。

1.  **训练和预测快速**：需要的计算资源最少。

**缺点：**

1.  **有限的预测能力**：由于设计原因，它不会从数据中学习，因此其预测通常不准确。

1.  **没有特征重要性**：它不提供哪些特征对预测最重要的见解。

1.  **不适合复杂问题**：在具有复杂模式的真实世界场景中，虚拟分类器过于简单，无法单独发挥作用。

# 最终评论

了解虚拟分类器对于任何数据科学家或机器学习爱好者来说至关重要。它们作为一种现实检查，帮助我们确保我们的更复杂模型确实从数据中学习到了有用的模式。在你继续进行机器学习之旅时，记得始终将你的模型与这些简单的基线进行比较——你可能会对所学到的内容感到惊讶！

# 🌟 虚拟分类器代码概述

```py
# Import necessary libraries
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.dummy import DummyClassifier

# Make dataset
dataset_dict = {
    'Outlook': ['sunny', 'sunny', 'overcast', 'rain', 'rain', 'rain', 'overcast', 'sunny', 'sunny', 'rain', 'sunny', 'overcast', 'overcast', 'rain', 'sunny', 'overcast', 'rain', 'sunny', 'sunny', 'rain', 'overcast', 'rain', 'sunny', 'overcast', 'sunny', 'overcast', 'rain', 'overcast'],
    'Temperature': [85.0, 80.0, 83.0, 70.0, 68.0, 65.0, 64.0, 72.0, 69.0, 75.0, 75.0, 72.0, 81.0, 71.0, 81.0, 74.0, 76.0, 78.0, 82.0, 67.0, 85.0, 73.0, 88.0, 77.0, 79.0, 80.0, 66.0, 84.0],
    'Humidity': [85.0, 90.0, 78.0, 96.0, 80.0, 70.0, 65.0, 95.0, 70.0, 80.0, 70.0, 90.0, 75.0, 80.0, 88.0, 92.0, 85.0, 75.0, 92.0, 90.0, 85.0, 88.0, 65.0, 70.0, 60.0, 95.0, 70.0, 78.0],
    'Wind': [False, True, False, False, False, True, True, False, False, False, True, True, False, True, True, False, False, True, False, True, True, False, True, False, False, True, False, False],
    'Play': ['No', 'No', 'Yes', 'Yes', 'Yes', 'No', 'Yes', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'No', 'Yes', 'Yes', 'No', 'No', 'No', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'Yes', 'No', 'Yes']
}
df = pd.DataFrame(dataset_dict)

# Perform one-hot encoding on 'Outlook' column
df = pd.get_dummies(df, columns=['Outlook'], prefix='', prefix_sep='', dtype=int)

# Convert 'Wind' and 'Play' columns to binary indicators
df['Wind'] = df['Wind'].astype(int)
df['Play'] = (df['Play'] == 'Yes').astype(int)

# Split data into features (X) and target (y), then into training and test sets
X, y = df.drop(columns='Play'), df['Play']
X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.5, shuffle=False)

# Initialize and train the dummy classifier model
dummy_clf = DummyClassifier(strategy='most_frequent')
dummy_clf.fit(X_train, y_train)

# Make predictions on the test data
y_pred = dummy_clf.predict(X_test)

# Calculate and print the model's accuracy on the test data
print(f"Accuracy: {accuracy_score(y_test, y_pred)*100:.4f}%")
```

## 进一步阅读

对于[DummyClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.dummy.DummyClassifier.html)及其在scikit-learn中的实现的详细说明，读者可以参考官方文档[2]，该文档提供了有关其使用和参数的全面信息。

## 技术环境

本文使用Python 3.7和scikit-learn 1.5。尽管所讨论的概念一般适用，但不同版本的具体代码实现可能会有所不同。

## 关于插图

除非另有说明，所有图片均由作者创作，并结合了Canva Pro的授权设计元素。

![](../Images/408bdcc3b6471d42cad83e91e574d97e.png)

若要查看虚拟分类器的简洁视觉总结，请访问[配套Instagram帖子](https://www.instagram.com/p/C-ssgsAyFSI/)。

## 参考文献

[1] T. M. Mitchell, [机器学习](https://www.cs.cmu.edu/afs/cs.cmu.edu/user/mitchell/ftp/mlbook.html)（1997），McGraw-Hill Science/Engineering/Math，第59页

在此查看更多**分类算法**：

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----009ff95fc86e--------------------------------)

## **分类算法**

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----009ff95fc86e--------------------------------)8篇故事！[](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)

你可能还喜欢：

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----009ff95fc86e--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----009ff95fc86e--------------------------------)5篇故事！![一个卡通娃娃，扎着辫子，戴着粉色的帽子。这个“傀儡”娃娃，设计简单，穿着带心形图案的T恤，直观地代表了机器学习中的傀儡回归器的概念。就像这个玩具般的人物是一个简化的、静态的人物代表，傀儡回归器是一个基础模型，作为更复杂分析的基准。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----009ff95fc86e--------------------------------)

## 集成学习

[查看列表](https://medium.com/@samybaladram/list/ensemble-learning-673fc83cd7db?source=post_page-----009ff95fc86e--------------------------------)4篇故事！[](../Images/1bd2995b5cb6dcc956ceadadc5ee3036.png)![](../Images/22a5d43568e70222eb89fd36789a9333.png)![](../Images/8ea1a2f29053080a5feffc709f5b8669.png)
