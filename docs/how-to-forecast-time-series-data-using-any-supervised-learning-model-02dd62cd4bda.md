# 如何使用任何监督学习模型预测时间序列数据

> 原文：[https://towardsdatascience.com/how-to-forecast-time-series-data-using-any-supervised-learning-model-02dd62cd4bda?source=collection_archive---------0-----------------------#2024-02-22](https://towardsdatascience.com/how-to-forecast-time-series-data-using-any-supervised-learning-model-02dd62cd4bda?source=collection_archive---------0-----------------------#2024-02-22)

## 将时间序列数据特征化为标准表格格式，以便应用经典的机器学习模型，并通过使用AutoML提高准确性

[](https://medium.com/@mturk24?source=post_page---byline--02dd62cd4bda--------------------------------)[![Matthew Turk](../Images/2c000da20cc4e662d1fd21e0eca90988.png)](https://medium.com/@mturk24?source=post_page---byline--02dd62cd4bda--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--02dd62cd4bda--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--02dd62cd4bda--------------------------------) [Matthew Turk](https://medium.com/@mturk24?source=post_page---byline--02dd62cd4bda--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--02dd62cd4bda--------------------------------) ·10分钟阅读·2024年2月22日

--

![](../Images/c143b6e4b58b16095c1e0dda4e22ce73.png)

来源：[Ahasanara Akter](https://www.vecteezy.com/members/ahasanaraakter)

本文深入探讨了通过使用开源库将时间序列数据集转换为表格格式，提升每日能耗水平预测过程的效率。我们探索了流行的多类别分类模型的应用，并利用Cleanlab Studio与AutoML大幅提高了我们的样本外准确性。

本文的关键要点是，我们可以通过将时间序列数据转换为表格结构，利用更通用的方法来建模该数据集，甚至在预测时间序列数据时找到提升的空间。

# 拍摄快照

总体而言，我们将会：

+   通过将Prophet预测模型拟合到我们的时间序列数据，建立一个基准准确度

+   通过使用开源特征化库将我们的时间序列数据转换为表格格式，并展示如何通过标准的多类别分类（梯度提升）方法比我们的Prophet模型**减少67%的预测误差**（在样本外准确度上增加38%的原始百分比点）。

+   使用AutoML解决方案进行多类别分类，**相比我们的梯度提升模型，减少了42%的预测误差**（在样本外准确度上增加8%的原始百分比点），**相比我们的Prophet预测模型，减少了81%的预测误差**（在样本外准确度上增加46%的原始百分比点）。

要运行本文中演示的代码，请参阅[完整的笔记本](https://github.com/mturk24/blog_posts/blob/main/time_series_automl/time_series_automl.ipynb)。

# 检查数据

你可以[在这里](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption?select=PJME_hourly.csv)下载数据集。

数据代表PJM每小时的能源消耗（以兆瓦为单位）。PJM Interconnection LLC（PJM）是美国的一个区域传输组织（RTO）。它是东部电力互联系统的一部分，运营着一个电力传输系统，服务于多个州。

让我们来看一下我们的数据集。数据包括一个日期时间列（`object` 类型）和一个我们试图预测的Megawatt能源消耗列（`float64` 类型），作为离散变量（对应于每小时能源消耗水平的四分位数）。我们的目标是训练一个时间序列预测模型，能够预测明天的每日能源消耗水平，属于以下四个等级之一：`低`，`低于平均`，`高于平均` 或 `高`（这些等级是基于整体每日消耗分布的四分位数确定的）。我们首先演示如何将时间序列预测方法（如Prophet）应用于这个问题，但这些方法限制于某些适合时间序列数据的机器学习模型。接下来，我们演示如何将这个问题重新构造为一个标准的多类别分类问题，这样我们就可以应用任何机器学习模型，并展示如何通过使用强大的监督学习获得更优的预测结果。

我们首先将这些数据转换为每日平均能源消耗，并将列重命名为Prophet预测模型所期望的格式。这些实数值的每日能源消耗水平被转换为四分位数，这是我们试图预测的值。我们的训练数据如下所示，并标明了每个每日能源消耗水平所属的四分位数。四分位数是使用训练数据计算的，以防止数据泄漏。

现在，我们展示下面的训练数据，这是我们用来拟合预测模型的数据。

![](../Images/853f65f5e4e0336e3d5a2d015e420413.png)

包含每日能源消耗水平四分位数的训练数据

然后我们展示下面的测试数据，这是我们用来评估预测结果的数据。

![](../Images/7d8f8c364bbda6c109fefa760b02272d.png)

包含每日能源消耗水平四分位数的测试数据

# 训练和评估 Prophet 预测模型

如上图所示，我们将使用 `2015-04-09` 作为训练数据的结束日期，并从 `2015-04-10` 开始我们的测试数据。我们仅使用训练数据计算每日能源消耗的四分位数阈值。这避免了数据泄漏——即使用仅在未来才会获得的样本外数据。

接下来，我们将预测测试数据期间每日PJME能源消耗水平（以MW为单位），并将预测值表示为离散变量。该变量表示每日能源消耗水平属于哪个四分位数，分类表示为1（`低`）、2（`低于平均`）、3（`高于平均`）或4（`高`）。为了评估，我们将使用`scikit-learn`中的`accuracy_score`函数来评估模型的性能。由于我们是以这种方式构建问题，我们能够使用分类准确率来评估模型对次日预测的表现（并比较未来的模型）。

```py
import numpy as np
from prophet import Prophet
from sklearn.metrics import accuracy_score

# Initialize model and train it on training data
model = Prophet()
model.fit(train_df)

# Create a dataframe for future predictions covering the test period
future = model.make_future_dataframe(periods=len(test_df), freq='D')
forecast = model.predict(future)

# Categorize forecasted daily values into quartiles based on the thresholds
forecast['quartile'] = pd.cut(forecast['yhat'], bins = [-np.inf] + list(quartiles) + [np.inf], labels=[1, 2, 3, 4])

# Extract the forecasted quartiles for the test period
forecasted_quartiles = forecast.iloc[-len(test_df):]['quartile'].astype(int)

# Categorize actual daily values in the test set into quartiles
test_df['quartile'] = pd.cut(test_df['y'], bins=[-np.inf] + list(quartiles) + [np.inf], labels=[1, 2, 3, 4])
actual_test_quartiles = test_df['quartile'].astype(int)

# Calculate the evaluation metrics
accuracy = accuracy_score(actual_test_quartiles, forecasted_quartiles)

# Print the evaluation metrics
print(f'Accuracy: {accuracy:.4f}')
>>> 0.4249
```

样本外准确度相当低，仅为43%。通过这种方式建模我们的时间序列数据，我们仅能使用时间序列预测模型（这是可能的机器学习模型的一个有限子集）。在下一节中，我们将探讨如何通过适当的特征化，将时间序列转换为标准的表格数据集，从而更灵活地建模这些数据。一旦时间序列被转换为标准的表格数据集，我们就能够使用任何监督学习模型来预测每日的能源消耗数据。

# 通过特征化将时间序列数据转换为表格数据

现在，我们将时间序列数据转换为表格格式，并使用开源库`sktime`、`tsfresh`和`tsfel`对数据进行特征化。通过使用这些库，我们可以提取出广泛的特征，捕捉时间序列数据的潜在模式和特征。这些特征包括统计特征、时间特征，以及可能的频谱特征，它们提供了数据随时间变化的全面快照。通过将时间序列分解为单独的特征，变得更加容易理解数据的不同方面如何影响目标变量。

`TSFreshFeatureExtractor`是`sktime`库中的一个特征提取工具，它利用`tsfresh`的功能从时间序列数据中提取相关特征。`tsfresh`旨在自动计算大量的时间序列特征，这对于理解复杂的时间动态非常有用。在我们的应用场景中，我们使用`TSFreshFeatureExtractor`提取最小且必要的特征集来对数据进行特征化。

`tsfel`（时间序列特征提取库）提供了一套全面的工具，用于从时间序列数据中提取特征。我们利用一个预定义的配置，可以从能源消耗的时间序列数据中构建一系列丰富的特征（例如，统计特征、时间特征、频谱特征），捕捉到可能与分类任务相关的各种特征。

```py
import tsfel
from sktime.transformations.panel.tsfresh import TSFreshFeatureExtractor

# Define tsfresh feature extractor
tsfresh_trafo = TSFreshFeatureExtractor(default_fc_parameters="minimal")

# Transform the training data using the feature extractor
X_train_transformed = tsfresh_trafo.fit_transform(X_train)

# Transform the test data using the same feature extractor
X_test_transformed = tsfresh_trafo.transform(X_test)

# Retrieves a pre-defined feature configuration file to extract all available features
cfg = tsfel.get_features_by_domain()

# Function to compute tsfel features per day
def compute_features(group):
    # TSFEL expects a DataFrame with the data in columns, so we transpose the input group
    features = tsfel.time_series_features_extractor(cfg, group, fs=1, verbose=0)
    return features

# Group by the 'day' level of the index and apply the feature computation
train_features_per_day = X_train.groupby(level='Date').apply(compute_features).reset_index(drop=True)
test_features_per_day = X_test.groupby(level='Date').apply(compute_features).reset_index(drop=True)

# Combine each featurization into a set of combined features for our train/test data
train_combined_df = pd.concat([X_train_transformed, train_features_per_day], axis=1)
test_combined_df = pd.concat([X_test_transformed, test_features_per_day], axis=1)
```

接下来，我们通过删除与目标变量——平均每日能耗水平——有较高相关性（大于0.8）和那些与目标变量无关的特征来清理数据集。高相关性的特征可能导致过拟合，使得模型在训练数据上表现良好，但在未见数据上表现不佳。另一方面，无相关性的特征没有任何价值，因为它们与目标变量缺乏可定义的关系。

通过排除这些特征，我们旨在提高模型的泛化能力，并确保我们的预测是基于平衡且有意义的数据输入集。

```py
# Filter out features that are highly correlated with our target variable
column_of_interest = "PJME_MW__mean"
train_corr_matrix = train_combined_df.corr()
train_corr_with_interest = train_corr_matrix[column_of_interest]
null_corrs = pd.Series(train_corr_with_interest.isnull())
false_features = null_corrs[null_corrs].index.tolist()

columns_to_exclude = list(set(train_corr_with_interest[abs(train_corr_with_interest) > 0.8].index.tolist() + false_features))
columns_to_exclude.remove(column_of_interest)

# Filtered DataFrame excluding columns with high correlation to the column of interest
X_train_transformed = train_combined_df.drop(columns=columns_to_exclude)
X_test_transformed = test_combined_df.drop(columns=columns_to_exclude)
```

如果我们查看现在的训练数据的前几行，这是它的样子。我们现在有73个特征，这些特征是通过我们使用的时间序列特征化库添加的。我们将基于这些特征预测的标签是第二天的能耗水平。

![](../Images/b848d10e4e2b4dc50a8d87d45c91344c.png)

新特征化后的前5行训练数据，采用表格格式

需要注意的是，我们使用了一项最佳实践，即分别对训练数据和测试数据应用特征化过程，以避免数据泄漏（并且保留的测试数据是我们最新的观察数据）。

此外，我们计算了我们的离散四分位数值（使用我们最初定义的四分位数），并使用以下代码来获得我们的训练/测试能耗标签，这就是我们的y_labels。

```py
# Define a function to classify each value into a quartile
def classify_into_quartile(value):
    if value < quartiles[0]:
        return 1  
    elif value < quartiles[1]:
        return 2  
    elif value < quartiles[2]:
        return 3  
    else:
        return 4  

y_train = X_train_transformed["PJME_MW__mean"].rename("daily_energy_level")
X_train_transformed.drop("PJME_MW__mean", inplace=True, axis=1)

y_test = X_test_transformed["PJME_MW__mean"].rename("daily_energy_level")
X_test_transformed.drop("PJME_MW__mean", inplace=True, axis=1)

energy_levels_train = y_train.apply(classify_into_quartile)
energy_levels_test = y_test.apply(classify_into_quartile)
```

# 在特征化的表格数据上训练和评估 GradientBoostingClassifier 模型

使用我们特征化的表格数据集，我们可以应用任何监督式机器学习模型来预测未来的能耗水平。这里我们将使用 Gradient Boosting Classifier (GBC) 模型，它是大多数数据科学家处理表格数据时的首选武器。

我们的 GBC 模型是从`sklearn.ensemble`模块实例化的，并配置了特定的超参数，以优化其性能并避免过拟合。

```py
from sklearn.ensemble import GradientBoostingClassifier

gbc = GradientBoostingClassifier(
    n_estimators=150,
    learning_rate=0.1,
    max_depth=4,
    min_samples_leaf=20,
    max_features='sqrt',
    subsample=0.8,
    random_state=42
)

gbc.fit(X_train_transformed, energy_levels_train)

y_pred_gbc = gbc.predict(X_test_transformed)
gbc_accuracy = accuracy_score(energy_levels_test, y_pred_gbc)
print(f'Accuracy: {gbc_accuracy:.4f}')
>>> 0.8075
```

81%的样本外准确度明显优于我们之前的 Prophet 模型结果。

# 使用 AutoML 简化操作

现在我们已经了解了如何特征化时间序列问题以及应用强大机器学习模型如 Gradient Boosting 的好处，一个自然的问题浮现出来：我们应该应用哪种监督式机器学习模型？当然，我们可以尝试多种模型，调整它们的超参数，并将它们进行集成。一个更简单的解决方案是让 AutoML 来处理这一切。

这里我们将使用 [Cleanlab Studio](https://cleanlab.ai/) 提供的一个简单 AutoML 解决方案，该方案不需要任何配置。我们只需提供我们的表格数据集，平台会自动训练多种类型的监督式机器学习模型（包括 Gradient Boosting 等），调整它们的超参数，并确定哪些模型最适合合并成一个单一的预测器。以下是训练和部署 AutoML 监督分类器所需的所有代码：

```py
 from cleanlab_studio import Studio

studio = Studio()
studio.create_project(
    dataset_id=energy_forecasting_dataset,
    project_name="ENERGY-LEVEL-FORECASTING",
    modality="tabular",
    task_type="multi-class",
    model_type="regular",
    label_column="daily_energy_level",
)

model = studio.get_model(energy_forecasting_model)
y_pred_automl = model.predict(test_data, return_pred_proba=True)
```

以下是我们在AutoML平台上看到的模型评估结果，展示了所有自动拟合和评估的不同类型的机器学习模型（包括多个梯度提升模型），以及通过最佳组合它们的预测结果构建的集成预测器。

![](../Images/74c3bf0f045edd844f9b2651ce32701b.png)

不同类型模型的AutoML结果

在对我们的测试数据进行推理，获得次日能源消耗预测后，我们发现测试准确度为89%，相比我们之前的梯度提升方法，原始百分比提高了8个百分点。

![](../Images/9ab4cd48956b723421b807d7b19d0c00.png)

我们在日常能源消耗数据上的AutoML测试准确度

# 结论

对于我们的PJM日常能源消耗数据，我们发现将数据转换为表格格式并进行特征化处理，实现了**67%的预测误差减少**（相比我们用Prophet预测模型建立的基准准确度，样本外准确度提高了38%）。

我们还尝试了一个简单的AutoML方法进行多类别分类，该方法**使预测误差减少了42%**（相比我们的梯度提升模型，样本外准确度提高了8%），并且**使预测误差减少了81%**（相比我们的Prophet预测模型，样本外准确度提高了46%）。

通过采用如上所示的方法来建模时间序列数据集，超越仅考虑预测方法的局限性，我们可以应用更通用的监督学习技术，并在某些类型的预测问题上取得更好的结果。

除非另有说明，所有图片均由作者提供。
