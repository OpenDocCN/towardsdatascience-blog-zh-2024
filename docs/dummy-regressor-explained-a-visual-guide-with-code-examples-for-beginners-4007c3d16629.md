# 虚拟回归器解释：初学者的视觉指南与代码示例

> 原文：[https://towardsdatascience.com/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629?source=collection_archive---------11-----------------------#2024-09-26](https://towardsdatascience.com/dummy-regressor-explained-a-visual-guide-with-code-examples-for-beginners-4007c3d16629?source=collection_archive---------11-----------------------#2024-09-26)

## 回归算法

## 天真地选择所有预测的最佳数字

[](https://medium.com/@samybaladram?source=post_page---byline--4007c3d16629--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--4007c3d16629--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4007c3d16629--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4007c3d16629--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--4007c3d16629--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4007c3d16629--------------------------------) ·阅读时间：7分钟·2024年9月26日

--

很多时候，我的学生会来找我，说他们想尝试最复杂的模型来完成机器学习任务，有时我开玩笑地说：“你尝试过*最棒的模型*了吗？”尤其在回归问题中（我们没有那种“100%准确率”的目标），一些机器学习模型表面上可能得到了很低的误差分数，但与虚拟模型进行比较后，实际上……并没有那么好。

所以，这就是虚拟回归器。[就像在分类器中一样](/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e)，回归任务也有其基准模型——你必须尝试的第一个模型，以大致了解你的机器学习模型能有多好。

![](../Images/a26c1218b6656b6c7a27afad643df65f.png)

所有视觉效果：作者使用 Canva Pro 创建，针对移动设备进行了优化；在桌面上可能会显示过大。

# 定义

虚拟回归器是一个简单的机器学习模型，它使用基本规则预测数值，而并非真正从输入数据中学习。像[其分类对手](/dummy-classifier-explained-a-visual-guide-with-code-examples-for-beginners-009ff95fc86e)一样，它作为基准模型，用于与更复杂的回归模型性能进行比较。虚拟回归器帮助我们理解我们的模型是否真的在学习有用的模式，还是仅仅在做天真的预测。

![](../Images/fb0de27cedea46784a68d59a04efe209.png)

虚拟回归器是最简单的机器学习模型。

# 📊 数据集与库

在本文中，我们将使用这个简单的人工高尔夫数据集作为示例。该数据集预测访问我们高尔夫球场的高尔夫球手数量。它包括如前景、温度、湿度和风速等特征，目标变量是高尔夫球手的数量。

![](../Images/6b391a1419ca551296c6405cfc93b4c9.png)

列：‘Outlook’（前景），‘Temperature’（温度，单位：华氏度），‘Humidity’（湿度，单位：%），‘Wind’（风，Yes/No），以及‘Number of Players’（玩家数量，数值型，目标特征）

```py
# Import libraries
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

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
```

# 评估回归结果

在深入讨论虚拟回归器之前，让我们回顾一下评估回归结果的方法。在分类问题中，检查模型的准确度是非常直观的（只需检查匹配值的比例），而在回归中则有所不同。

RMSE（均方根误差）就像是**回归模型的评分**。它告诉我们预测值与实际值之间的偏差。就像在分类问题中我们希望获得高准确率以得到更多正确答案一样，**在回归中我们希望RMSE较低**，以便更接近真实值。

人们喜欢使用RMSE，因为**它的值与我们试图预测的值类型相同**。

![](../Images/89a57cc0f48f0bd3d7d523be535fc9bd.png)

RMSE=3 可以解释为实际值与预测值的差异在±3范围内。

```py
from sklearn.metrics import mean_squared_error

y_true = np.array([10, 15, 20, 15, 10]) # True labels
y_pred = np.array([15, 11, 18, 14, 10]) # Predicted values

# Calculate RMSE using scikit-learn
rmse = mean_squared_error(y_true, y_pred, squared=False)

print(f"RMSE = {rmse:.2f}")
```

记住这一点，让我们开始讨论算法。

# 主要机制

虚拟回归器根据简单的规则进行预测，例如始终返回训练数据中目标值的**均值或中位数**。

![](../Images/39b0253d963d586062170f055900981d.png)

对于我们的高尔夫数据集，虚拟回归器可能始终预测“40.5”作为玩家数量，因为这是训练标签的中位数。

# 训练步骤

虚拟回归器的训练过程有点像是个骗局，不过无论如何，下面是一个大致的概述：

## 1\. 选择策略

选择以下策略之一：

+   **均值**：始终预测训练目标值的均值。

+   **中位数**：始终预测训练目标值的中位数。

+   **常数**：始终预测用户提供的常数值。

![](../Images/672b8a6fa897d96a46e726c4019d4983.png)

根据策略的不同，虚拟回归器会给出不同的数值预测。

```py
from sklearn.dummy import DummyRegressor

# Choose a strategy for your DummyRegressor ('mean', 'median', 'constant')
strategy = 'median'
```

## 2\. 计算度量

根据你的策略计算均值或中位数。

![](../Images/b7175d6ed9dd85ce474d5ced46169f34.png)

该算法仅仅是计算训练数据的中位数——在这个例子中，我们得到的中位数是40.5。

```py
# Initialize the DummyRegressor
dummy_reg = DummyRegressor(strategy=strategy)

# "Train" the DummyRegressor (although no real training happens)
dummy_reg.fit(X_train, y_train)
```

## 3\. 将策略应用于测试数据

使用所选策略**生成测试数据的预测数值标签列表**。

![](../Images/a76c34d4df043dad4fa5b8979674f65a.png)

如果我们选择“中位数”策略，计算出的中位数（40.5）将简单地作为所有预测的值。

```py
# Use the DummyRegressor to make predictions
y_pred = dummy_reg.predict(X_test)
print("Label     :",list(y_test))
print("Prediction:",list(y_pred))
```

## 评估模型

![](../Images/33152fd5f7de890cce4d0e70511619fa.png)

使用此策略的虚拟回归器给出了13.28的误差值，作为未来模型的基准。

```py
# Evaluate the Dummy Regressor's error
from sklearn.metrics import mean_squared_error

rmse = mean_squared_error(y_test, y_pred, squared=False)
print(f"Dummy Regression Error: {rmse.round(2)}")
```

# 关键参数

虚拟回归器中只有一个主要的关键参数，即：

1.  **策略**：这决定了回归器如何进行预测。常见的选项包括：

    - **均值**：提供一个平均基准，通常用于一般场景。

    - **中位数**：对异常值更具鲁棒性，适用于偏斜的目标分布。

    - **常数**：当领域知识建议进行特定常数预测时很有用。

1.  **常数**：当使用“常数”策略时，这个参数指定了始终预测的类别。

![](../Images/5c33e7107ab363c5c632e09294b96ff4.png)

无论使用什么策略，结果都同样糟糕，但可以确定我们的下一个回归模型的RMSE值应该低于12。

# 优缺点

作为懒惰预测器，虚拟回归器肯定有其优点和局限性。

**优点：**

1.  **简单基准**：快速展示其他模型应该超越的最小性能。

1.  **快速**：设置和运行几乎不需要时间。

**缺点：**

1.  **不学习**：仅使用简单规则，因此通常会被真正的模型超越。

1.  **忽略特征**：在进行预测时不考虑任何输入数据。

# 最后的备注

使用虚拟回归器应该是我们进行回归任务的第一步。它们提供了一个标准基准线，因此我们可以确认一个更复杂的模型实际上提供了比随机预测更好的结果。当你学习更多先进的技术时，千万不要忘记将你的模型与这些简单的基准进行比较——这些天真的预测可能正是你最初需要的！

# 🌟 虚拟回归器代码总结

```py
# Import libraries
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error
from sklearn.dummy import DummyRegressor

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

# Initialize and train the model
dummy_reg = DummyRegressor(strategy='median')
dummy_reg.fit(X_train, y_train)

# Make predictions
y_pred = dummy_reg.predict(X_test)

# Calculate and print RMSE
print(f"RMSE: {mean_squared_error(y_test, y_pred, squared=False)}")
```

## 进一步阅读

关于[DummyRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.dummy.DummyRegressor.html)及其在scikit-learn中的实现，读者可以参考官方文档，里面提供了关于其用法和参数的详细信息。

## 技术环境

本文使用的是Python 3.7和scikit-learn 1.5。虽然讨论的概念普遍适用，但具体的代码实现可能会随着不同版本有所不同。

## 关于插图

除非另有说明，所有图片均由作者创建，结合了Canva Pro授权设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝙍𝙚𝙜𝙧𝙚𝙨𝙨𝙞𝙤𝙣 𝘼𝙡𝙜𝙤𝙧𝙞𝙩𝙝𝙢𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----4007c3d16629--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----4007c3d16629--------------------------------)5个故事![一只戴着粉色帽子、扎着辫子的卡通娃娃。这个“傀儡”娃娃，以其基本设计和带有心形图案的衣服，形象地代表了机器学习中的“傀儡回归器”概念。就像这个玩具般的角色是一个简化的、静态的人物表现一样，傀儡回归器是作为更复杂分析的基准的基本模型。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----4007c3d16629--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----4007c3d16629--------------------------------)8个故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)
