# 使用Facebook Prophet预测时间序列数据入门

> 原文：[https://towardsdatascience.com/getting-started-predicting-time-series-data-with-facebook-prophet-c74ad3040525?source=collection_archive---------2-----------------------#2024-01-30](https://towardsdatascience.com/getting-started-predicting-time-series-data-with-facebook-prophet-c74ad3040525?source=collection_archive---------2-----------------------#2024-01-30)

## 本文旨在通过使用Prophet进行的动手教程，消除入门时间序列分析的障碍。

[](https://medium.com/@jonas_dieckmann?source=post_page---byline--c74ad3040525--------------------------------)[![Jonas Dieckmann](../Images/b3345d7d4ba503b08bd4dc715390d650.png)](https://medium.com/@jonas_dieckmann?source=post_page---byline--c74ad3040525--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c74ad3040525--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c74ad3040525--------------------------------) [Jonas Dieckmann](https://medium.com/@jonas_dieckmann?source=post_page---byline--c74ad3040525--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c74ad3040525--------------------------------) ·阅读时间：9分钟·2024年1月30日

--

当开始学习数据科学时，时间序列分析是人们常常希望自己尝试的一个内容！这里的基本理念是通过学习历史模式来预测未来。典型的应用案例可能是天气预测或销售预测。但是，下面这位聪明的预言家与这些有什么关系呢？！

![](../Images/6b84a482c13b16a83fedc78c40fa8e57.png)

由leonardo.ai生成的图像

本文旨在通过使用一种最简单的工具之一——[Facebook Prophet](https://facebook.github.io/prophet/)——在[Google Colab](https://colab.research.google.com/)中进行的动手教程，消除入门时间序列分析的障碍（这两个工具都是免费的！）。如果你想立即开始，可以跳过接下来的两章，我将在其中简要介绍时间序列的基本原理以及Facebook Prophet本身。祝你玩得开心！

本文分为三个主要部分：

#1 时间序列分析原理简要介绍

#2 Facebook Prophet简介

#3 如何在Google Colab中使用Prophet的动手教程（免费）

# #1 时间序列分析的基本原则

想象一下，你是一家消费品商店的经理，你想预测即将到来的产品需求，以更好地管理供应。在这种情况下，一个合理的机器学习方法是进行时间序列分析，这涉及到理解、建模并根据顺序数据点进行预测。[1]

以下图形展示了历史产品需求（深蓝色线条）的人工发展趋势，可以用来分析时间序列模式。我们的最终目标是尽可能准确地预测（红色虚线）实际的未来需求（浅蓝色线条）：

![](../Images/76be98eabce3d974413bec2d78f08e89.png)

绘制的时间序列示例。图像来自作者

时间序列通常分解为三个主要组件：

1.  **趋势**：数据中的长期变化或总体方向。

1.  **季节性**：在规律间隔时间内重复出现的波动或模式

1.  **残差/误差**：数据中剩余或遗留的变化

将时间序列分解为这三大组件，通常称为加性或乘性分解，能够帮助分析师更好地理解数据的潜在结构和模式。这种理解对于选择合适的预测模型以及基于历史数据做出准确预测至关重要。[2]

![](../Images/7014027115a20cfee7d61ec8779c0dd5.png)

将时间序列分解为趋势+季节性+误差的示例。图像来自作者

# #2 什么是 Facebook Prophet？

Prophet 是 Facebook 数据科学团队发布的一个开源工具，基于加性模型生成时间序列预测数据，其中非线性趋势与季节性和假期效应相匹配。其设计原理使得在没有太多关于底层模型的知识的情况下，也能进行参数调整，这使得该方法适用于统计知识较少的团队。[3]

Prophet 特别适用于商业预测应用，并因其易用性和在处理各种时间序列数据方面的有效性而获得广泛关注。和所有工具一样，需要注意的是，虽然 Prophet 功能强大，但选择预测方法仍然依赖于数据的具体特征和分析目标。一般来说，不能保证 Prophet 比其他模型表现更好。然而，Prophet 具备一些有用的功能，例如反映季节性变化的情况（例如新冠疫情前后的变化）或将封锁视为一次性假期。

若想深入了解 Meta（Facebook）本身的介绍，请观看下面的[YouTube 视频](https://www.youtube.com/watch?v=eJrbKU09h-0&ab_channel=MetaOpenSource)。

在接下来的教程中，我们将使用 Python 实现并应用 Prophet。不过，你也可以非常高兴地使用 R 进行分析！

# #3 使用 Prophet 的实操教程

如果你对编码环境的经验有限或没有访问权限，我建议使用[Google Colaboratory（“Colab”）](https://colab.research.google.com/)，它类似于“*一个* *免费的 Jupyter notebook 环境，无需设置，完全在云端运行*。”虽然本教程强调了 Colab 的简便性和优势，但也有一些缺点，如与专业云环境相比，计算能力较弱。然而，我认为 Colab 可能是一个不错的选择，用于与 Prophet 一起迈出第一步。

在 Colab 中设置基本的时间序列分析环境，你可以按照以下两个步骤操作：

1.  打开[https://colab.research.google.com/](https://colab.research.google.com/)并注册一个免费账户

1.  在 Colab 中创建一个新的*notebook*

1.  安装并使用*prophet*包：

```py
pip install prophet
```

```py
from prophet import Prophet
```

## 加载并准备数据

我上传了一个小型虚拟数据集，代表本地公交公司（2012–2023年）的月度乘客数量。你可以在[GitHub 上找到数据](https://raw.githubusercontent.com/jonasdieckmann/prophet_tutorial/main/passengers.csv)。

作为第一步，我们将使用 pandas 加载数据，并创建两个独立的数据集：一个包含2012到2022年的训练子集，以及一个包含2023年的测试子集。我们将使用第一个子集来训练我们的时间序列模型，并预测2023年的乘客数量。通过第二个子集，我们稍后将能够验证模型的准确性。

```py
import pandas as pd

df_data = pd.read_csv("https://raw.githubusercontent.com/jonasdieckmann/prophet_tutorial/main/passengers.csv")

df_data_train = df_data[df_data["Month"] < "2023-01"]
df_data_test = df_data[df_data["Month"] >= "2023-01"]

display(df_data_train)
```

显示命令的输出如下所示。数据集包含两列：年份-月份的组合标识，以及该月的乘客数量的数值列。默认情况下，Prophet 设计为处理每日（甚至每小时）的数据，但我们将确保月度模式也可以使用。

![](../Images/083ecb9d59735390a75f1cdfde35f4d6.png)

乘客数据集。图像由作者提供

## 分解训练数据

为了更好地理解我们虚拟数据中的时间序列组件，我们将进行快速分解。为此，我们从*statsmodels*库中导入该方法，并对我们的数据集进行分解。我们选择了加法模型，并指定数据中的一个周期包含12个元素（月）。如果是每日数据集，则周期=365。

```py
from statsmodels.tsa.seasonal import seasonal_decompose

decompose = seasonal_decompose(df_data_train.Passengers, model='additive', extrapolate_trend='freq', period=12)

decompose.plot().show()
```

这段简短的代码将为我们提供时间序列本身的视觉印象，特别是关于趋势、季节性和残差随时间变化的情况：

![](../Images/52c693392ede2122ae4d49aeb2225592.png)

乘客虚拟数据的分解元素。图像由作者提供

现在我们可以清晰地看到过去10年中，显著上升的趋势以及每年可识别的季节性模式。根据这些迹象，我们现在预计模型会预测未来年份夏季的乘客数量会有所增加，并且跟随季节性波峰。但让我们试试看——是时候应用一些机器学习了！

## 使用 Facebook Prophet 进行模型拟合

在Prophet中拟合模型时，至少需要有一个“ds”（日期戳）和一个“y”（要预测的值）列。我们应该确保我们的列名已被重命名，以反映这一点。

```py
df_train_prophet = df_data_train

# date variable needs to be named "ds" for prophet
df_train_prophet = df_train_prophet.rename(columns={"Month": "ds"})

# target variable needs to be named "y" for prophet
df_train_prophet = df_train_prophet.rename(columns={"Passengers": "y"})
```

现在，魔法可以开始了。拟合模型的过程相当直接。然而，请查看[文档](https://facebook.github.io/prophet/docs/handling_shocks.html)，以了解我们在这一步骤中可以调整的大量选项和参数。为了简化，我们暂时将拟合一个简单模型，不做进一步的调整——但请记住，现实世界中的数据永远不是完美的：你以后肯定需要进行参数调优。

```py
model_prophet  = Prophet()
model_prophet.fit(df_train_prophet)
```

这就是我们拟合模型所需做的一切。让我们进行一些预测吧！

## 进行预测

我们需要对一个包含“ds”列（包含你想要进行预测的日期）的表格进行预测。要设置这个表格，使用*make_future_dataframe*方法，它会自动包含历史日期。通过这种方式，你可以看到模型如何拟合过去的数据并预测未来。由于我们处理的是月度数据，我们会通过“freq=12”来指定频率，并要求预测的未来时间段为12个月（“periods=12”）。

```py
df_future = model_prophet.make_future_dataframe(periods=12, freq='MS')
display(df_future)
```

这个新的数据集包含了训练期和我们想要预测的额外12个月数据：

![](../Images/67f5957194177eec969937a413b16b0e.png)

未来数据集。图片来源：作者

要进行预测，我们只需调用Prophet的*predict*方法，并提供未来数据集。预测结果将包含一个包含许多不同列的大数据集，但我们只关注预测值*yhat*以及不确定性区间*yhat_lower*和*yhat_upper*。

```py
forecast_prophet = model_prophet.predict(df_future)
forecast_prophet[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].round().tail()
```

下表给我们一些关于如何生成和存储输出的概念。对于2023年8月，模型预测的乘客数量为532人。默认设置为80%的不确定性区间，简单来说，它告诉我们在这个月，大多数情况下，乘客数量可能在508人到556人之间。

![](../Images/2db589424d17336f514f1af97cb1c9ce.png)

预测子集。图片来源：作者

最后，我们想要可视化输出，以更好地理解预测结果和区间。

## 可视化结果

为了绘制结果，我们可以利用Prophet的内置绘图工具。通过plot方法，我们可以显示原始时间序列数据和预测值。

```py
import matplotlib.pyplot as plt

# plot the time series 
forecast_plot = model_prophet.plot(forecast_prophet)

# add a vertical line at the end of the training period
axes = forecast_plot.gca()
last_training_date = forecast_prophet['ds'].iloc[-12]
axes.axvline(x=last_training_date, color='red', linestyle='--', label='Training End')

# plot true test data for the period after the red line
df_data_test['Month'] = pd.to_datetime(df_data_test['Month'])
plt.plot(df_data_test['Month'], df_data_test['Passengers'],'ro', markersize=3, label='True Test Data')

# show the legend to distinguish between the lines
plt.legend()
```

除了常规的时间序列图，我们还添加了一条虚线，表示训练期的结束，从而标志着预测期的开始。此外，我们还使用了我们在一开始准备的真实测试数据集。

![](../Images/4f9654f9a8bee5bd9b32e54ca2a7f1df.png)

绘制的时间序列分析结果，包括真实测试数据和预测结果。图片来源：作者

可以看出，我们的模型表现得不错。大部分真实的乘客数据实际上都在预测的不确定性区间内。然而，夏季的预测似乎仍然偏乐观，这也是我们在往年数据中看到的模式。现在是开始探索我们可以与 Prophet 一起使用的参数和特征的好时机。

在我们的例子中，季节性并不是一个常数加性因子，而是随着时间的推移与趋势共同增长。因此，我们可能会考虑在模型拟合过程中将 *seasonality_mode* 从“加性”改为“乘性”。[4]

我们的教程将在这里结束，以便为您探索 Prophet 提供的众多可能性腾出时间。为了便于大家一起回顾完整代码，我将代码片段整合在这个[Python 文件](https://raw.githubusercontent.com/jonasdieckmann/prophet_tutorial/main/prophet_tutorial.py)中。此外，您还可以将[这个笔记本](https://raw.githubusercontent.com/jonasdieckmann/prophet_tutorial/main/Prophet_Tutorial.ipynb)直接上传到 Colab 并自行运行。让我知道它对您有何帮助！

# 结论

Prophet 是一个强大的工具，用于预测时间序列数据中的未来值，特别是当您的数据具有像月度或年度周期这样的重复模式时。它非常用户友好，并且能够快速为您的特定数据提供准确的预测。然而，了解它的局限性也非常重要。如果您的数据没有明确的模式，或者有一些模型未曾见过的重大变化，Prophet 可能无法达到最佳表现。了解这些局限性对于明智地使用该工具至关重要。

好消息是，强烈推荐您在自己的数据集上实验 Prophet！每个数据集都是独特的，调整设置并尝试不同的方法可以帮助您发现最适合您特定情况的方式。所以，快去探索吧，看看 Prophet 如何提升您的时间序列预测。

[](https://medium.com/@jonas_dieckmann?source=post_page-----c74ad3040525--------------------------------) [## Jonas Dieckmann - Medium

### 阅读 Jonas Dieckmann 在 Medium 上的文章。分析经理和产品负责人 @ philips | 热衷并撰写关于…

medium.com](https://medium.com/@jonas_dieckmann?source=post_page-----c74ad3040525--------------------------------)

我希望您觉得这很有用。请告诉我您的想法！也欢迎在 LinkedIn 上与我联系 [https://www.linkedin.com/in/jonas-dieckmann/](https://www.linkedin.com/in/jonas-dieckmann/) 或在 Medium 上关注我。

## 请参见我其他的一些文章：

[](https://pub.towardsai.net/how-to-use-chatgpt-api-for-direct-interaction-from-colab-or-databricks-39969a0ead5f?source=post_page-----c74ad3040525--------------------------------) [## 如何使用 ChatGPT API 从 Colab 或 Databricks 直接交互

### 您是否想过如何使用 OpenAI 的 API 直接与 GPT 算法交互？它很简单、免费，而且更…

[pub.towardsai.net](https://pub.towardsai.net/how-to-use-chatgpt-api-for-direct-interaction-from-colab-or-databricks-39969a0ead5f?source=post_page-----c74ad3040525--------------------------------) [](/how-to-set-started-with-tensorflow-using-keras-api-and-google-colab-5421e5e4ef56?source=post_page-----c74ad3040525--------------------------------) [## 如何使用 Keras API 和 Google Colab 入门 TensorFlow

### 分步教程：使用神经网络分析人类活动

towardsdatascience.com](/how-to-set-started-with-tensorflow-using-keras-api-and-google-colab-5421e5e4ef56?source=post_page-----c74ad3040525--------------------------------)

## 参考文献

[1] **Shumway, Robert H.; Stoffer, David S. (2017)**: 时间序列分析及其应用。Cham: Springer 国际出版。

[2] **Brownlee, Jason (2017)**: 使用 Python 进行时间序列预测入门

[3] **Rafferty, Greg (2021)**: 使用 Facebook Prophet 预测时间序列数据

[4] [https://facebook.github.io/prophet/docs/quick_start.html](https://facebook.github.io/prophet/docs/quick_start.html)
