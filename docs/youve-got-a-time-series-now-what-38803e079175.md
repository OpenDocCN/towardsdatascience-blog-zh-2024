# 你有一个时间序列。那接下来该做什么？

> 原文：[https://towardsdatascience.com/youve-got-a-time-series-now-what-38803e079175?source=collection_archive---------1-----------------------#2024-04-20](https://towardsdatascience.com/youve-got-a-time-series-now-what-38803e079175?source=collection_archive---------1-----------------------#2024-04-20)

## 如何进行时间序列的探索性数据分析

[](https://medium.com/@alexroz?source=post_page---byline--38803e079175--------------------------------)[![Aleksei Rozanov](../Images/748b69bfaccf39c9aa568a9e6f41eec3.png)](https://medium.com/@alexroz?source=post_page---byline--38803e079175--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38803e079175--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--38803e079175--------------------------------) [Aleksei Rozanov](https://medium.com/@alexroz?source=post_page---byline--38803e079175--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38803e079175--------------------------------) ·阅读时长8分钟·2024年4月20日

--

![](../Images/92a060eae44518a5f0d674f9be8d1280.png)

图片由 [作者](https://medium.com/@alexroz) 提供。

最常见的数据类型之一就是时间序列。视频、图片、像素、信号，任何带有时间成分的东西都可以转化为时间序列。形式上，**时间序列**是对可观察变量在**等时间间隔**下的*历史*测量序列。

在本文中，我想建议一个小型的流程，任何人在分析时间序列时都可以使用它。它可以帮助你从数据本身提取有意义的洞察，准备数据进行建模，并得出一些初步结论。

由于我最喜欢的词是“地理空间”🌏，今天我们将分析一组气象时间序列。具体来说，我们将探讨位于东南西伯利亚的某点在2023年期间的2米气温、总降水量、地表净太阳辐射和地表气压，这些数据来自每小时的 [ERA5 Land](https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-land?tab=overview) [1] 气候重分析。

> 一如既往，要跟进本教程，你可以下载并运行这个笔记本 [**在这里**](https://github.com/alexxxroz/Medium/blob/main/Time_Series_EDA.ipynb)**。**

为了完成分析，我们需要导入几个库：

```py
import pandas as pd
import seaborn as sns
import numpy as np

import matplotlib.pyplot as plt
import xarray as xr

import statsmodels.api as sm
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
from scipy import stats
```

我还决定尝试使用来自两个库的新 matplotlib 风格，分别是 [opinionated](https://github.com/saforem2/opinionated) 和 [ambivalent](https://github.com/saforem2/ambivalent?tab=readme-ov-file#user-content-fn-1-97838fc778b9f9f773da6819bae071bb)：

```py
from ambivalent import STYLES
import opinionated
plt.style.use(STYLES['ambivalent'])
plt.style.use("dark_background")
```

# 1\. 折线图

首先，让我们上传并可视化我们拥有的数据。为了处理地理空间多维数组，我们将使用 [xarray](https://docs.xarray.dev/en/stable/) 库。

```py
data = xr.open_dataset('Medium_data.nc')
data
```

![](../Images/1185e28466ddf068044199a5b20f8d4a.png)

图片由[作者](https://medium.com/@alexroz)提供。

现在我们需要根据选择的位置切割数据，转换为 pandas 数据框，并创建一个折线图：

```py
df = data.sel(latitude=52.53, longitude=101.63, method='pad').to_pandas().drop(['latitude', 'longitude'], axis=1)
```

```py
fig, ax = plt.subplots(ncols = 2, nrows = 2, figsize=(16,9))
df['t2m'].plot(ax=ax[0,0])
ax[0,0].set_title('Air Temperature')
df['ssr'].plot(ax=ax[0,1])
ax[0,1].set_title('Surface Net Solar Radiation')
df['sp'].plot(ax=ax[1,0])
ax[1,0].set_title('Surface Pressure')
df['tp'].plot(ax=ax[1,1])
ax[1,1].set_title('Total Precipitation')
plt.tight_layout()
plt.show()
```

![](../Images/9e4ef447f5b5d88c1e3351cf263e5168.png)

图片由[作者](https://medium.com/@alexroz)提供。

从折线图中已经可以看出，所有四个时间序列具有不同的特征，因此让我们使用数学工具进一步分析它们。

# 2\. 分解与平稳性

任何时间序列都有三个重要属性需要考虑：

+   **趋势**，即时间序列中平滑的长期变化；

+   **季节性**，指的是时间序列中均值具有规律性周期性变化的特征；

+   **噪声（残差）**，即信号中的随机成分，其均值为零。

要单独获取这些组件，可以进行**经典分解**（加法型或乘法型）。此操作通过应用卷积滤波器来执行，因此每个时间序列组件定义为：

![](../Images/1fab0b93d051c464fe77f0949360141e.png)

加法分解。图片由[作者](https://medium.com/@alexroz)提供。

或

![](../Images/b7e7f1653141537fd4ffe0403de86f00.png)

乘法分解。图片由[作者](https://medium.com/@alexroz)提供。

其中 **y** — 为时间序列中的值，**S** — 为季节性成分，**T** — 为趋势成分，**n** — 为噪声。

要进行分解，除了选择分解类型外，还需要设置季节性周期（例如 p=1 表示年度，p=4 表示季度，p=12 表示月度数据等）。

需要特别提到的是，上述的经典分解方法非常简单且朴素。它有显著的局限性，如其线性假设、无法捕捉动态季节性以及在处理时间序列的非平稳性时的困难。然而，就本文的目的而言，这种方法足够了。

为了进行经典分解，我们将使用来自 statsmodels 库的 *seasonal_decompose* 函数，周期设置为 24，因为我们处理的是小时数据：

```py
vars = {'t2m': 'Air Temperature', 'tp': 'Total Precipitation', 'sp': 'Surface Pressure', 'ssr': 'Surface Net Solar Radiation'}
for var in df.columns:
  result = sm.tsa.seasonal_decompose(df[var], model='additive', period = 24)
  results_df = pd.DataFrame({'trend': result.trend, 'seasonal': result.seasonal, 'resid': result.resid, 'observed': result.observed})
  fig, ax = plt.subplots(ncols = 2, nrows = 2,figsize=(16,9))
  ax[0,0].plot(df.index, results_df.trend)
  ax[0,0].set_title('Trend')
  ax[0,0].set_ylabel('Value')

  ax[0,1].plot(df.index, results_df.seasonal)
  ax[0,1].set_title('Seasonal')

  ax[1,0].plot(df.index, results_df.resid)
  ax[1,0].set_title('Residual')
  ax[1,0].set_ylabel('Value')
  ax[1,0].set_xlabel('time')

  ax[1,1].plot(df.index, results_df.observed)
  ax[1,1].set_title('Observed')
  ax[1,1].set_xlabel('time')

  opinionated.set_title_and_suptitle(vars[var], f"Dickey-Fuller test: {round(sm.tsa.stattools.adfuller(df[var])[1],5)}", position_title=[0.45,1],
                                     position_sub_title=[0.95, 1])
  plt.tight_layout()
  plt.savefig(f'Seasonal_{var}.png')
  plt.show()
```

![](../Images/9c2814e11f60837ef7f83577d16bf39e.png)

图片由[作者](https://medium.com/@alexroz)提供。

![](../Images/3d544b1c9cd662ef6ebec53a0dd01671.png)

图片由[作者](https://medium.com/@alexroz)提供。

![](../Images/2eaee716dd06aa5037556f2a052b8d43.png)

图片由[作者](https://medium.com/@alexroz)提供。

![](../Images/b550fe4f8fec269dbbba972d9b9e81f7.png)

图片由[作者](https://medium.com/@alexroz)提供。

你可以看到，对于所有变量，季节性成分看起来很杂乱。由于我们分析的是小时数据，这些季节性变化出现在一天之内，因此并没有提供太多信息。在这种情况下，值得尝试将数据重新采样为日分辨率，并进行一天周期的分解。

```py
df_d = df.resample('1d').mean()
```

![](../Images/8cd6a2e794670aeaab6898ec9ad8da9b.png)

图片由[作者](https://medium.com/@alexroz)提供。

到目前为止，你们中的一些人可能已经注意到图表右上角的迪基-富勒检验标签。这是一个**平稳性**检验，使用了同一库中的*adfuller*函数。在时间序列的情况下，平稳性意味着时间序列的属性随时间不发生变化。这里的属性包括方差、季节性、趋势和自相关性。

在对时间序列应用增强型迪基-富勒（ADF）检验时，我们提出原假设，即时间序列是非平稳的。然后我们选择显著性水平α，通常为5%。本质上，α是错误拒绝原假设（实际上原假设为真）的概率。因此，在我们的案例中，α=5%，意味着有5%的风险得出结论认为时间序列是平稳的，实际上它并非如此。

测试结果将给出一个p值。如果p值小于0.05，我们可以拒绝原假设。

正如你所看到的，所有四个变量根据ADF检验都是平稳的。

通常，为了应用一些时间序列预测模型，如ARIMA等，平稳性是必需的，因此我们在这里很幸运。一般来说，气象和气候数据通常在各种时间序列相关的学习材料中被分析，因为它们在大多数情况下是平稳的。

# 3\. 分布

在得出所有时间序列都是平稳的结论后，让我们来看看它们是如何分布的。为此，我们将使用著名的seaborn库及其*pairplot*函数，该函数允许创建包含直方图和核密度估计的有信息量的图表。

```py
ax = sns.pairplot(df, diag_kind='kde')
ax.map_upper(sns.histplot, bins=20)
ax.map_lower(sns.kdeplot, levels=5, color='.1')
plt.show()
```

![](../Images/7e170e4e1b89387902da9d18d0263fc3.png)

图片由[作者](https://medium.com/@alexroz)提供。

让我们考虑t2m（1行1列）的例子。当分析核密度估计（kde）图时，显然这个变量的分布是多峰的，这意味着它有两个或更多的“钟形曲线”。因此，在本文的后续阶段，我们将尝试将该变量转化为**正态分布**。

第一列和第一行中的其他图表在提供的信息上是相同的，但它们的可视化方式不同。基本上，这些是散点图，允许我们识别两个变量之间的相关性。因此，点的颜色越深，或点越接近中央圆圈，说明该区域内的点密度越高。

# **4\. Box-Cox变换**

由于我们已经发现空气温度时间序列是平稳的，但不是正态分布的，让我们尝试使用Box-Cox变换来解决这个问题。为此，我们将使用scipy包及其*boxcox*函数。

```py
df_d['t2m_box'], _ = stats.boxcox(df_d.t2m)
fig, ax = plt.subplots(nrows=1, ncols=2, figsize=(15,7))
sns.histplot(df_d.t2m_box, kde=True, ax=ax[0])
sns.histplot(df_d.t2m, kde=True, ax=ax[1])
```

![](../Images/1b8b07eba99a1d17396bcfb5708d1747.png)

图片由[作者](https://medium.com/@alexroz)提供。

图的左侧是我们经过BoxCox变换后的时间序列分布，正如你所看到的，它仍然远未达到“正态”分布。但如果与右侧的分布进行比较，我们可以说它肯定已经更接近正态分布了。

我们可以做的另一件事来确保所执行的变换是有用的，就是创建一个概率图。本质上，我们将一个理论分布的分位数（在我们的例子中是正态分布）与我们的经验数据（即我们所考虑的时间序列）的样本进行比较。点越接近白线，效果越好。

```py
fig = plt.figure()

ax1 = fig.add_subplot(211)
prob = stats.probplot(df_d.t2m, dist=stats.norm, plot=ax1)
ax1.get_lines()[1].set_color('w')
ax1.get_lines()[0].set_color('#8dd3c7')
ax1.set_title('Probplot against normal distribution')

ax2 = fig.add_subplot(212)
prob = stats.probplot(df_d.t2m_box, dist=stats.norm, plot=ax2)
ax2.get_lines()[1].set_color('w')
ax2.get_lines()[0].set_color('#8dd3c7')
ax2.set_title('Probplot after Box-Cox transformation')
plt.tight_layout()fig = plt.figure()

ax1 = fig.add_subplot(211)
prob = stats.probplot(df_d.t2m, dist=stats.norm, plot=ax1)
ax1.set_title('Probplot against normal distribution')

ax2 = fig.add_subplot(212)
prob = stats.probplot(df_d.t2m_box, dist=stats.norm, plot=ax2)
ax2.set_title('Probplot after Box-Cox transformation')
plt.tight_layout()
```

![](../Images/b5ef301fb62270b7c6e400ffceac1241.png)

图片来源：[作者](https://medium.com/@alexroz)。

> 如果你打算将变换后的时间序列用于机器学习建模，请不要忘记应用逆BoxCox变换，否则你将不得不处理不适当的数值！

# **5. 自相关**

我们分析的最后一步是自相关。自相关函数（ACF）估计时间序列与其滞后版本之间的相关性。换句话说，它描述了时间序列的某一特定值与在不同时间间隔内其他先前值的相关性。

绘制部分自相关函数（PACF）也可能会很有帮助，PACF与自相关相同，但移除了短期滞后的相关性。因此，它估计的是在某个时间戳内的值之间的相关性，同时控制其他值的影响。

```py
for var in df.columns[:-1]:
  fig, (ax1, ax2) = plt.subplots(2,1,figsize=(10,8))
  plot_acf(df_d.t2m, ax = ax1)
  plot_pacf(df_d.t2m, ax = ax2)
  opinionated.set_title_and_suptitle(vars[var], '',position_title=[0.38,1],
                                     position_sub_title=[0.95, 1])
  plt.tight_layout()
  plt.show()
```

![](../Images/0dd2ecabf7f1fdb15e13be386904ee29.png)

图片来源：[作者](https://medium.com/@alexroz)。

正如你所看到的，地面气压时间序列在1天滞后时存在非常强的部分自相关。然后自相关显著减弱，经过3天滞后后几乎消失。这样的分析可以帮助你更好地理解所处理数据的性质，从而得出更有意义的结论。

就这样，这是一个非常简短且直接的管道，用于在处理时间序列时进行探索性数据分析。重要的是要记住，你应该使用的方法高度依赖于你需要解决的问题。

希望这篇文章对你有所启发！

===========================================

**参考文献：**

[1] Muñoz Sabater, J. (2019)：ERA5-Land 每小时数据，时间范围从1950年至今。哥白尼气候变化服务（C3S）气候数据存储（CDS）。DOI：10.24381/cds.e2161bac（访问日期：2024年4月16日）

===========================================

***我在Medium上的所有出版物都是免费的，公开访问的，因此如果你在这里关注我，我将非常感激！***

P.S. 我对（地理）数据科学、机器学习/人工智能以及气候变化非常热衷。如果你想一起合作进行某个项目，请在[LinkedIn](https://www.linkedin.com/in/alexxxroz/)上联系我。

🛰️关注我获取更多内容🛰️
