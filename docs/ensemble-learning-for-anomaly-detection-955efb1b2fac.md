# 集成学习在异常检测中的应用

> 原文：[https://towardsdatascience.com/ensemble-learning-for-anomaly-detection-955efb1b2fac?source=collection_archive---------5-----------------------#2024-10-30](https://towardsdatascience.com/ensemble-learning-for-anomaly-detection-955efb1b2fac?source=collection_archive---------5-----------------------#2024-10-30)

## *深入探讨隔离森林模型，用于检测时间序列数据中的异常值*

[](https://medium.com/@adavis08?source=post_page---byline--955efb1b2fac--------------------------------)[![Alex Davis](../Images/f773cce9438a68856cb8ba486ac8b051.png)](https://medium.com/@adavis08?source=post_page---byline--955efb1b2fac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--955efb1b2fac--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--955efb1b2fac--------------------------------) [Alex Davis](https://medium.com/@adavis08?source=post_page---byline--955efb1b2fac--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--955efb1b2fac--------------------------------) ·阅读时间：7分钟·2024年10月30日

--

异常检测是任何组织都必备的能力。通过检测异常值和离群点，我们不仅能识别出看似可疑（或可能错误）的数据，还能建立“正常”数据的标准。异常检测对于强大的数据治理系统至关重要，它能够识别数据错误。而在分析中，离群点在某些情况下，如欺诈检测和预测性维护，可能成为重点。

然而，随着数据量的增加，异常检测变得越来越困难。高维数据通常带有噪声，使得其难以用于分析和提取洞见。大规模数据集也可能包含错误和/或特殊情况。幸运的是，集成学习通过提高速度和效率，帮助我们处理高维数据并检测异常值。

# 什么是集成学习？

集成学习是一种机器学习技术，它通过结合多个独立模型的预测，来获得比任何单一模型更好的预测性能。每个模型被视为一个“弱学习器”，并在数据的一个小子集上进行训练以进行预测。然后它进行投票。每个弱学习器都会被调查，最终通过多数票决定预测结果。

![](../Images/68ea0c492fe208b9a5695a9c1515eda7.png)

图片来源：Wikimedia Commons（[https://commons.wikimedia.org/wiki/File:Random_forest_explain.png](https://commons.wikimedia.org/wiki/File:Random_forest_explain.png)）

集成模型（在高质量数据上训练）具有鲁棒性、准确性、高效性，且能够有效避免过拟合。它们有许多应用场景，如分类、优化，以及在我们这个案例中的异常检测。

# 隔离森林模型

孤立森林模型是一种由多棵树组成的集成模型，能够隔离那些稀疏的观测值。它与流行的“随机森林”模型非常相似，但不同的是，孤立森林生成的是“孤立树”森林，而不是决策树森林。

那么它是如何工作的呢？让我们来看一棵孤立树。

![](../Images/673e94aca10cdd4218bd7d158965e237.png)

图片由作者提供

看一下上面的数据。我们可以看到一个数据点离其他数据较远（我们怀疑是异常值）。**每棵孤立树会随机选择一个“分割值”来开始隔离观测值。** 在这个例子中，怀疑的异常值会立即被隔离。由于它与其他数据点的距离较远，这种情况在大多数孤立树中都会发生。

![](../Images/6aca0034350e7247a47cbe55fb66a21e.png)

图片由作者提供

接下来，它选择另一个分割。这次，怀疑是“正常”数据的部分开始被切分。**这个过程会重复，直到每个观测值被隔离。** 最终，模型通过随机选择一个特征，并在该特征的最大值和最小值之间随机选择一个分割值来“隔离”观测值。

![](../Images/0ffa29702aebb86c93866eec8b90d6e7.png)

图片由作者提供

现在每个观测值都已经被隔离，我们需要问：**隔离每个观测值需要多少次分割？** 换句话说，每个数据点的分割路径有多长？假设结果如下：

![](../Images/e6f94e6695942edff26916a20c5fd124.png)

图片由作者提供

现在我们知道了隔离每个观测值需要多少次分割，我们计算出分割的平均次数。在我们的例子中，平均而言，隔离一个观测值需要 2.6 次分割。**那些分割路径明显较短，或者隔离所需的分割次数明显较少的观测值，很有可能是异常值或离群值。** 它们与平均分割次数的差异程度是模型中的一个参数。最后，孤立树判断观测值 G 是异常值。

**孤立森林模型的最后一步是让每棵孤立树对哪些观测值是异常值进行“投票”。** 如果大多数孤立树认为观测值 G 是异常值，那么模型就会判断它为异常值。

# 时间序列数据中的异常检测

让我们看一个简单的例子，使用孤立森林模型来检测时间序列数据中的异常值。下面，我们导入了一个包含订单日期、产品信息、客户地理信息和销售额的销售数据集。为了简化这个例子，我们只看一个特征（销售额）随时间的变化。

*查看数据：* [*https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting*](https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting) *(GPL 2.0)*

```py
#packages for data manipulation
import pandas as pd
from datetime import datetime

#packages for modeling
from sklearn.ensemble import IsolationForest

#packages for data visualization
import matplotlib.pyplot as plt
```

```py
#import sales data
sales = pd.read_excel("Data/Sales Data.xlsx")

#subset to date and sales
revenue = sales[['Order Date', 'Sales']]
revenue.head()
```

![](../Images/78621fd24438dc0ad35003bbcd78ff71.png)

图片由作者提供

如上所示，我们有每个特定日期所有订单的总销售额。由于我们有足够的数据（四年数据），让我们尝试检测出总销售额显著高于或低于预期总销售额的月份。

首先，我们需要进行一些预处理，并汇总每个月的销售额。然后，进行每月销售额的可视化。

```py
#format the order date to datetime month and year
revenue['Order Date'] = pd.to_datetime(revenue['Order Date'],format='%Y-%m').dt.to_period('M')

#sum sales by month and year
revenue = revenue.groupby(revenue['Order Date']).sum()

#set date as index
revenue.index = revenue.index.strftime('%m-%Y')
```

```py
#set the fig size
plt.figure(figsize=(8, 5))

#create the line chart
plt.plot(revenue['Order Date'],
         revenue['Sales'])

#add labels and a title
plt.xlabel('Moth')
plt.ylabel('Total Sales')
plt.title('Monthly Sales')

#rotate x-axis labels by 45 degrees for better visibility
plt.xticks(rotation = 90)

#display the chart
plt.show()
```

![](../Images/af77b3a53519ce3fe0cc456da4e61675.png)

图片来自作者

使用上面的折线图，我们可以看到，虽然销售额月度波动，但总销售额随时间趋势向上。理想情况下，我们的模型将识别出总销售额波动超过预期并且对整体趋势有较大影响的月份。

现在我们需要初始化并拟合我们的模型。下面的模型使用了默认参数。我已经突出显示了这些参数，因为它们对模型的表现至关重要。

+   **n_estimators**：集成中基础估计器的数量。

+   **max_samples**：从X中选择用于训练每个基础估计器的样本数量（如果为“auto”，则`max_samples = min(256, n_samples)`）。

+   **contamination**：数据集的污染程度，即数据集中异常值的比例。用于拟合时定义样本得分的阈值。

+   **max_features**：从X中选择用于训练每个基础估计器的特征数量。

```py
#set isolation forest model and fit to the sales
model = IsolationForest(n_estimators = 100, max_samples = 'auto', contamination = float(0.1), max_features = 1.0)
model.fit(revenue[['Sales']])
```

接下来，让我们使用模型来显示异常及其异常得分。异常得分是基础估计器中每个观察值的常态性均值衡量。得分越低，观察值越异常。负分表示异常值，正分表示正常值。

```py
#add anomaly scores and prediction
revenue['scores'] = model.decision_function(revenue[['Sales']])
revenue['anomaly'] = model.predict(revenue[['Sales']])
```

![](../Images/ca56501e9d6e920ba5ccf90664793ca8.png)

图片来自作者

最后，让我们展示之前的相同折线图，但用plt.scatter突出显示异常值。

![](../Images/4f5c5e6e39a801c2abed486f849e43b1.png)

图片来自作者

模型似乎表现不错。由于数据在月度间波动较大，一个担忧是可能会将正常值标记为异常，但由于**模型的自助采样**，这种情况并未发生。异常值似乎是销售额偏离趋势较“显著”的较大波动。

然而，了解数据在这里非常重要，因为一些异常应该附带警告。我们来看第一个（2015年2月）和最后一个（2018年11月）检测到的异常。首先，我们可以看到它们都与平均值存在较大的波动。

然而，第一个异常（2015年2月）只是我们记录销售的第二个月，且业务可能刚刚开始运营。销售额肯定很低，接下来一个月出现了大幅上涨。但仅仅因为销售额低就将业务的第二个月标记为异常，是否合理？还是这对一个新企业来说是正常现象？

对于我们最后一个异常（2018年11月），我们看到销售量出现了巨大的波动，似乎偏离了整体趋势。然而，我们已经没有更多的数据。随着数据的持续记录，这可能并不是一个异常，而可能是一个识别出更陡峭上升趋势的信号。

## 结论

总之，异常检测是强有力的数据治理和严格分析中不可或缺的能力。尽管在大规模数据中检测离群值和异常值可能很困难，但集成学习方法能够提供帮助，因为它们在处理大规模表格数据时既稳健又高效。

隔离森林模型通过使用一组“弱学习者”来隔离稀少且分散的观测值，从而检测这些异常。

*希望你喜欢我的文章！欢迎发表评论、提问或提出其他话题的建议。*
