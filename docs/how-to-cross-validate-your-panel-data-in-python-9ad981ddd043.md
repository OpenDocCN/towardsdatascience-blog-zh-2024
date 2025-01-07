# 如何在 Python 中对面板数据进行交叉验证

> 原文：[`towardsdatascience.com/how-to-cross-validate-your-panel-data-in-python-9ad981ddd043?source=collection_archive---------5-----------------------#2024-03-08`](https://towardsdatascience.com/how-to-cross-validate-your-panel-data-in-python-9ad981ddd043?source=collection_archive---------5-----------------------#2024-03-08)

![](img/9ba98d4b8300f30aeec7177892adf319.png)

西班牙加那利群岛兰萨罗特岛的 Salinas de Janubio。图片来源：[imag3s 4 u](https://www.instagram.com/imag3s_4_u/)

## 使用 PanelSplit 进行面板数据交叉验证简介

[](https://medium.com/@eric.frey?source=post_page---byline--9ad981ddd043--------------------------------)![Eric Frey](https://medium.com/@eric.frey?source=post_page---byline--9ad981ddd043--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ad981ddd043--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ad981ddd043--------------------------------) [Eric Frey](https://medium.com/@eric.frey?source=post_page---byline--9ad981ddd043--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ad981ddd043--------------------------------) ·6 分钟阅读·2024 年 3 月 8 日

--

**动机：** 作为一名处理面板数据的人员，我经常需要进行交叉验证。这涉及到在某个时间点之前进行训练，在一部分观察数据上进行测试，然后在更远的时间点继续训练，再在不同的观察数据子集上进行测试，并在面板数据集上反复进行这种过程。听起来很熟悉吧？手动实现这个过程可能会非常令人沮丧。为了简化这一过程，我创建了一个名为[PanelSplit](https://github.com/4Freye/panelsplit)的包，它能在处理面板数据时提供帮助。

本文展示了如何在处理面板数据时使用 PanelSplit；从特征工程到超参数调优，再到生成预测，PanelSplit 都可以为您提供帮助！

## **什么是面板数据？**

我所说的**面板数据**是指存在多个实体随时间变化的数据。这些实体可以是国家、个人、组织或任何其他分析单元。对这些多个实体在不同时间点进行了多次观察记录。

## 什么是交叉验证？

假设我们想要估计在使用模型时预测的准确性。我们该如何进行？标准方法是**交叉验证**，它涉及将数据分割成连续的多个折叠，每个折叠都有其独特的训练集和测试集。下方的可视化展示了时间序列数据的交叉验证过程。

![](img/17f39de9360687fc291f49de080a28d0.png)

时间序列交叉验证的示例。

虽然已经有一个名为 [**TimeSeriesSplit**](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html) 的 scikit-learn 函数可以用于时间序列交叉验证，但它不适用于面板数据。与单一实体的时间序列不同，面板数据包含多个实体，我们需要一个工具来处理多个实体。

这就是 PanelSplit 的作用所在。**PanelSplit** 是一个允许我们将 TimeSeriesSplit 扩展到面板数据的包。它还提供了转换、预测等功能，但在本文中，我将只介绍基本内容。

# 使用 PanelSplit 执行交叉验证

现在我们已经介绍了什么是面板数据，以及在这种环境下交叉验证的样子，让我们看看如何使用 PanelSplit 执行交叉验证。

首先，让我们生成一些示例数据进行操作：

```py
import pandas as pd
import numpy as np

# generate example data
num_countries = 3
years = range(2000, 2005)
num_years = len(years)

data = {
    'country_id': [c for c in range(1, num_countries + 1) for _ in years],
    'year': [year for _ in range(num_countries) for year in years],
    'y': np.random.normal(0, 1, num_countries * num_years),
    'x1': np.random.normal(0, 1, num_countries * num_years),
    'x2': np.random.normal(0, 1, num_countries * num_years)
}

panel_data = pd.DataFrame(data)

# display the generated panel data
display(panel_data)
```

![](img/ae47ac32ee235a6dc360a5ba0a5095db.png)

生成的面板数据。观察的国家有 3 个，时间范围是 2001–2004 年。

生成面板数据集后，我们现在可以应用 PanelSplit。

## 初始化 PanelSplit

当我们初始化 PanelSplit 时，需要定义我们将使用的交叉验证方法。

+   **periods** 参数接受时间序列。在这种情况下，序列是年份列。

+   **n_splits**、**gap** 和 **test_size** 是 [TimeSeriesSplit](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html) 用来拆分时间序列的参数。

+   通过指定 **plot=True**，可以生成一张可视化图，描述每次拆分中的训练集和测试集。

```py
!pip install panelsplit
from panelsplit import PanelSplit

panel_split = PanelSplit(periods = panel_data.year, n_splits = 3, gap = 0, test_size=1, plot=True)
```

![](img/46ac5e7af26beffef69e4b1ba42715cc.png)

初始化 PanelSplit 时的输出（当 plot=True）。根据我们提供的参数，拆分次数为 3 次，训练集和测试集之间没有间隔，每次拆分的测试集大小为一个周期。

## 理解 PanelSplit 的工作原理

为了更好地了解拆分的情况，让我们使用 **split()** 函数返回每次拆分中的不同训练集和测试集。

```py
splits = panel_split.split()
```

`splits` 对象包含了交叉验证过程中的 3 次拆分。在每次拆分中，有一个列表，其中包含训练集索引（第一个项）和测试集索引（第二个项）。这些索引是布尔值，表示某一行是否属于某个特定拆分的训练集/测试集。可以利用这些索引对数据进行过滤，选择不同的数据子集，如下图所示。

![](img/dcb54e616816b5223fcf3737cbc5acbc.png)

演示每次拆分中的不同训练集和测试集。

## 超参数调整

现在我们已经创建了 PanelSplit 的实例，让我们进行一些 **超参数调整**！

在这里，我们进行一个基本的超参数搜索，使用 Ridge 模型，并指定**GridSearchCV**的 cv 参数为 panel_split。在 GridSearchCV 的拟合过程中，它调用 panel_split 的 split()函数，返回每个训练集和测试集的索引。它使用这些索引来过滤提供给 fit()函数的 X 和 y 数据。

```py
from sklearn.linear_model import Ridge
from sklearn.model_selection import GridSearchCV

param_grid = {'alpha':[.1, .5]} # define the hyper-parameter grid space

# define the gridsearch and call fit, specifying panel_split for the cv argument
gridsearch = GridSearchCV(estimator = Ridge(), param_grid=param_grid, cv=panel_split)
gridsearch.fit(X = panel_data[['x1','x2']], y = panel_data['y'])

print(gridsearch.best_params_)
```

![](img/a442388e0853686faa7ca8edac110bd3.png)

在这次搜索中，Ridge 模型的最佳 alpha 值为 0.5。

太棒了！我们已经找到了最佳的超参数组合。现在我们可以使用这些参数进行预测。

*注意：在实际应用中，我们会区分用于超参数调整的测试集和用于评估性能的测试集，但在这个示例中我们将验证集和测试集保持一致。*

## 使用 cross_val_fit_predict 生成预测

使用 PanelSplit 生成预测非常简单。

使用**cross_val_fit_predict**，我们指定要使用我们最好的 Ridge 模型、我们的 X 和 y，PanelSplit 将在每个训练集上进行拟合，并在每个测试集上进行预测，每个分割都是如此。

```py
predictions, models = panel_split.cross_val_fit_predict(estimator = Ridge(gridsearch.best_params_), 
                                                   X = panel_data[['x1','x2']], 
                                                   y = panel_data['y'])
```

预测结果以及拟合模型会返回。如果我们想要在预测中包含标识符，可以使用**gen_test_labels**生成标签，然后在我们的 predictions_df DataFrame 中创建一个新的 Pandas Series。

```py
predictions_df = panel_split.gen_test_labels(panel_data[['country_id','year']])
predictions_df['y_pred'] = y_pred
display(predictions_df)
```

![](img/966f0a4c82ab4dfa922575405633e5cb.png)

预测的 DataFrame。

# PanelSplit 还能做什么？

这只是一个基础演示，但 PanelSplit 可以做更多的事情！例如：

+   使用**cross_val_fit_transform**，我们可以在训练集上进行拟合，在测试集上进行转换。如果我们有需要插补的缺失特征，这非常有用。

+   如果我们想要对数据进行缩放，并且每个分割需要它自己独立的“快照”以保持缩放变换的独立性怎么办？我们可以使用**gen_snapshots**来实现这一点！或者在 cross_val_fit_predict 中使用 scikit-learn 管道作为估算器 :)

+   如果我们缺少某个时间段怎么办？通过在初始化时使用**unique periods**参数和**drop_splits**参数，PanelSplit 可以处理这个问题，并在没有观测值的地方丢弃分割。

如果您想查看更多示例并亲自尝试 PanelSplit，可以查看我创建的[Jupyter notebook](https://github.com/4Freye/panelsplit/blob/main/examples/An%20introduction%20to%20PanelSplit.ipynb)，其中我介绍了一些额外的功能。

这是我写的第一个包，所以在这个项目中我学到了很多东西。感谢您的阅读，希望 PanelSplit 能在您的下一个面板数据项目中提供帮助！

*注意：除非另有说明，所有图片均由作者提供。*
