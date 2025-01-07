# 时间序列预测：探索性数据分析实用指南

> 原文：[https://towardsdatascience.com/time-series-forecasting-a-practical-guide-to-exploratory-data-analysis-a101dc5f85b1?source=collection_archive---------0-----------------------#2024-05-09](https://towardsdatascience.com/time-series-forecasting-a-practical-guide-to-exploratory-data-analysis-a101dc5f85b1?source=collection_archive---------0-----------------------#2024-05-09)

## 如何利用探索性数据分析从时间序列数据中提取信息，并使用Python增强特征工程

[](https://medium.com/@maicolnicolini96?source=post_page---byline--a101dc5f85b1--------------------------------)[![Maicol Nicolini](../Images/97e78725ba70c95e340b76527c358498.png)](https://medium.com/@maicolnicolini96?source=post_page---byline--a101dc5f85b1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a101dc5f85b1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a101dc5f85b1--------------------------------) [Maicol Nicolini](https://medium.com/@maicolnicolini96?source=post_page---byline--a101dc5f85b1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a101dc5f85b1--------------------------------) ·15分钟阅读·2024年5月9日

--

![](../Images/32bbea6cf3c9513a36265783549ae925.png)

由[Ales Krivec](https://unsplash.com/@aleskrivec?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

时间序列分析无疑是数据科学和机器学习领域最广泛的主题之一：无论是预测金融事件、能源消耗、产品销售还是股市趋势，这个领域一直是企业关注的重点。

显然，数据可用性的极大增加，加上机器学习模型的不断进步，使得这个话题今天更加引人关注。除了传统的统计预测方法（如回归模型、ARIMA模型、指数平滑），与机器学习（如基于树的模型）和深度学习（如LSTM网络、CNN、基于Transformer的模型）相关的技术已经存在一段时间了。

尽管这些技术之间存在巨大差异，但无论模型是什么，都必须完成一个初步步骤：*探索性数据分析*。

在统计学中，**探索性数据分析**（EDA）是一门分析和可视化数据的学科，旨在总结其主要特征并从中获取相关信息。这在数据科学领域具有重要意义，因为它为另一个重要步骤奠定了基础：*特征工程*。即，创建、转换和提取数据集特征的实践，以便模型能够发挥最佳性能。

因此，本文的目标是定义一个清晰的探索性数据分析模板，专注于时间序列，可以总结并突出数据集的最重要特征。为此，我们将使用一些常见的Python库，如*Pandas*、*Seaborn*和*Statsmodel*。

# 数据

让我们首先定义数据集：为了本文的目的，我们将使用Kaggle的[**每小时能源消耗**](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption)数据集。该数据集与PJM每小时能源消耗数据有关，PJM是美国一个区域传输组织，向特拉华州、伊利诺伊州、印第安纳州、肯塔基州、马里兰州、密歇根州、新泽西州、北卡罗来纳州、俄亥俄州、宾夕法尼亚州、田纳西州、弗吉尼亚州、西弗吉尼亚州和哥伦比亚特区提供电力。

每小时的电力消耗数据来自PJM网站，单位为兆瓦（MW）。

# 探索性数据分析

现在让我们定义在处理时间序列时需要进行的最重要的分析。

当然，其中最重要的事情之一是绘制数据图表：图形可以突出显示许多特征，如模式、异常观察、随时间变化的变化以及变量之间的关系。如前所述，从这些图表中得出的见解必须尽可能地纳入预测模型中。此外，一些数学工具，如描述性统计和时间序列分解，也将非常有用。

也就是说，本文提出的探索性数据分析（EDA）包含六个步骤：描述性统计、时间图、季节性图、箱型图、时间序列分解、滞后分析。

## 1\. 描述性统计

描述性统计是定量描述或总结结构化数据集合特征的汇总统计量。

描述数据集时常用的一些指标包括：集中趋势度量（例如*均值*、*中位数*）、离散程度度量（例如*范围*、*标准差*）和位置度量（例如*百分位数*、*四分位数*）。所有这些指标都可以通过所谓的**五数汇总**进行总结，其中包括：最小值、第一四分位数（Q1）、中位数或第二四分位数（Q2）、第三四分位数（Q3）和分布的最大值。

在Python中，这些信息可以通过Pandas中著名的`describe`方法轻松检索：

```py
import pandas as pd

# Loading and preprocessing steps
df = pd.read_csv('../input/hourly-energy-consumption/PJME_hourly.csv')
df = df.set_index('Datetime')
df.index = pd.to_datetime(df.index)

df.describe()
```

![](../Images/569c28931bca7ee75e4366dbbd6b91db.png)

1\. PJME统计汇总。

## 2\. 时间图

开始时，最明显的图表是时间图。这意味着，观测结果是根据其观测时间绘制的，连续的观测点通过线条连接。

在 Python 中，我们可以使用 Pandas 和 Matplotlib：

```py
import matplotlib.pyplot as plt

# Set pyplot style
plt.style.use("seaborn")

# Plot
df['PJME_MW'].plot(title='PJME - Time Plot', figsize=(10,6))
plt.ylabel('Consumption [MW]')
plt.xlabel('Date')
```

![](../Images/d9bd55add95ba151a92842566151524f.png)

2.1 PJME 消耗时间图。

这个图表已经提供了若干信息：

1.  正如我们所预期的，图形显示了年度季节性变化。

1.  聚焦于单一年份时，似乎会有更多的模式出现。很可能，能耗在冬季会有一个高峰，夏季也会有一个高峰，原因是更大的电力消耗。

1.  该系列没有表现出明确的增长/下降趋势，平均能耗保持稳定。

1.  在2023年左右出现了一个异常值，可能在实现模型时应该进行补偿。

## 3. 季节性图

季节性图基本上是一个时间图，其中数据根据其所属的各个“季节”进行绘制。

关于能源消耗，我们通常有每小时的数据，因此可能会有几种季节性：*年度*、*每周*、*每日*。在深入分析这些图表之前，我们先在 Pandas 数据框中设置一些变量：

```py
# Defining required fields
df['year'] = [x for x in df.index.year]
df['month'] = [x for x in df.index.month]
df = df.reset_index()
df['week'] = df['Datetime'].apply(lambda x:x.week)
df = df.set_index('Datetime')
df['hour'] = [x for x in df.index.hour]
df['day'] = [x for x in df.index.day_of_week]
df['day_str'] = [x.strftime('%a') for x in df.index]
df['year_month'] = [str(x.year) + '_' + str(x.month) for x in df.index]
```

## 3.1 季节性图 — 年度消费

一个非常有趣的图表是关于按年分组的每月能耗图，这突出了年度季节性变化，并且可以告诉我们多年间的上升/下降趋势。

这是 Python 代码：

```py
import numpy as np

# Defining colors palette
np.random.seed(42)
df_plot = df[['month', 'year', 'PJME_MW']].dropna().groupby(['month', 'year']).mean()[['PJME_MW']].reset_index()
years = df_plot['year'].unique()
colors = np.random.choice(list(mpl.colors.XKCD_COLORS.keys()), len(years), replace=False)

# Plot
plt.figure(figsize=(16,12))
for i, y in enumerate(years):
    if i > 0:        
        plt.plot('month', 'PJME_MW', data=df_plot[df_plot['year'] == y], color=colors[i], label=y)
        if y == 2018:
            plt.text(df_plot.loc[df_plot.year==y, :].shape[0]+0.3, df_plot.loc[df_plot.year==y, 'PJME_MW'][-1:].values[0], y, fontsize=12, color=colors[i])
        else:
            plt.text(df_plot.loc[df_plot.year==y, :].shape[0]+0.1, df_plot.loc[df_plot.year==y, 'PJME_MW'][-1:].values[0], y, fontsize=12, color=colors[i])

# Setting labels
plt.gca().set(ylabel= 'PJME_MW', xlabel = 'Month')
plt.yticks(fontsize=12, alpha=.7)
plt.title("Seasonal Plot - Monthly Consumption", fontsize=20)
plt.ylabel('Consumption [MW]')
plt.xlabel('Month')
plt.show()
```

![](../Images/d81109dc87e66d8794e60ec39136e05a.png)

3.1 PJME 年度季节性图

该图显示每年实际上有一个非常预定义的模式：冬季消费显著增加，夏季达到峰值（由于供暖/制冷系统），而春季和秋季通常不需要供暖或制冷，因此消费最小。

此外，该图告诉我们，在整体能耗上，跨年并没有明确的增长/下降趋势。

## 3.2 季节性图 — 每周消费

另一个有用的图表是每周图表，它描绘了每月内一周的能耗变化，还可以提示我们能耗在单一年份中的变化情况。

让我们看看如何用 Python 来解决这个问题：

```py
# Defining colors palette
np.random.seed(42)
df_plot = df[['month', 'day_str', 'PJME_MW', 'day']].dropna().groupby(['day_str', 'month', 'day']).mean()[['PJME_MW']].reset_index()
df_plot = df_plot.sort_values(by='day', ascending=True)

months = df_plot['month'].unique()
colors = np.random.choice(list(mpl.colors.XKCD_COLORS.keys()), len(months), replace=False)

# Plot
plt.figure(figsize=(16,12))
for i, y in enumerate(months):
    if i > 0:        
        plt.plot('day_str', 'PJME_MW', data=df_plot[df_plot['month'] == y], color=colors[i], label=y)
        if y == 2018:
            plt.text(df_plot.loc[df_plot.month==y, :].shape[0]-.9, df_plot.loc[df_plot.month==y, 'PJME_MW'][-1:].values[0], y, fontsize=12, color=colors[i])
        else:
            plt.text(df_plot.loc[df_plot.month==y, :].shape[0]-.9, df_plot.loc[df_plot.month==y, 'PJME_MW'][-1:].values[0], y, fontsize=12, color=colors[i])

# Setting Labels
plt.gca().set(ylabel= 'PJME_MW', xlabel = 'Month')
plt.yticks(fontsize=12, alpha=.7)
plt.title("Seasonal Plot - Weekly Consumption", fontsize=20)
plt.ylabel('Consumption [MW]')
plt.xlabel('Month')
plt.show()
```

![](../Images/072c928e8b54ab3ce70e7607a0e5f7f1.png)

3.2 PJME 每周季节性图

## 3.3 季节性图 — 日常消费

最后，我想展示的最后一个季节性图是日常能耗图。如你所猜测，它表示能耗在一天内的变化。在这种情况下，数据首先按星期几分组，然后聚合计算均值。

这是代码：

```py
import seaborn as sns

# Defining the dataframe
df_plot = df[['hour', 'day_str', 'PJME_MW']].dropna().groupby(['hour', 'day_str']).mean()[['PJME_MW']].reset_index()

# Plot using Seaborn
plt.figure(figsize=(10,8))
sns.lineplot(data = df_plot, x='hour', y='PJME_MW', hue='day_str', legend=True)
plt.locator_params(axis='x', nbins=24)
plt.title("Seasonal Plot - Daily Consumption", fontsize=20)
plt.ylabel('Consumption [MW]')
plt.xlabel('Hour')
plt.legend()
```

![](../Images/9e0cc04edfadccf0216f2b1c90e5e291.png)

3.3 PJME 日常季节性图

通常，这个图显示出一个非常典型的模式，有人称之为“M型”，因为能耗似乎在一天内形成一个“M”形。有时这个模式很明显，有时则不明显（就像在这个案例中）。

然而，这些图表通常显示出一天中间的相对峰值（从上午10点到下午2点），然后是一个相对的最低值（从下午2点到6点），再到另一个峰值（从下午6点到8点）。最后，它还显示了周末和其他日期的消费差异。

## 3.4 季节性图表 — 特征工程

现在让我们看看如何使用这些信息进行特征工程。假设我们正在使用一些需要高质量特征的机器学习模型（例如ARIMA模型或基于树的模型）。

以下是来自季节性图表的主要证据：

1.  年度消费在几年间变化不大：这提示我们可以在可用的情况下，使用来自滞后或外生变量的年度季节性特征。

1.  每周的消费在各个月份间遵循相同的模式：这提示我们可以使用来自滞后或外生变量的每周特征。

1.  日常消费在普通工作日和周末有所不同：这提示我们可以使用类别特征来识别某一天是否为普通工作日。

## 4\. 箱型图

箱型图是识别数据分布的一种有用方式。简而言之，箱型图描绘了分位数，代表分布的第1四分位数（Q1）、第2四分位数（Q2/中位数）和第3四分位数（Q3），以及须状线，代表数据的范围。任何超出须状线的值都可以被视为*异常值*，更深入地说，须状线通常是通过以下方式计算的：

![](../Images/06e5f1fe1b65dbcd7f018e268cf2ba24.png)

4\. 须状线公式

## 4.1 箱型图 — 总消费

首先让我们计算关于总消费的箱型图，这可以通过*Seaborn*轻松完成：

```py
plt.figure(figsize=(8,5))
sns.boxplot(data=df, x='PJME_MW')
plt.xlabel('Consumption [MW]')
plt.title(f'Boxplot - Consumption Distribution');
```

![](../Images/bcb675b3d2364e13ed03d36cc3cbc140.png)

4.1 PJME 箱型图

即使这个图表看起来信息不多，它告诉我们我们正处理一个类似高斯分布的情况，且尾部在右侧更加突出。

## 4.2 箱型图 — 天月分布

一个非常有趣的图表是按天/月分箱图。它是通过创建一个“天 月”变量并按此变量对消费进行分组得到的。以下是代码，只涉及从2017年开始的数据：

```py
df['year'] = [x for x in df.index.year]
df['month'] = [x for x in df.index.month]
df['year_month'] = [str(x.year) + '_' + str(x.month) for x in df.index]

df_plot = df[df['year'] >= 2017].reset_index().sort_values(by='Datetime').set_index('Datetime')
plt.title(f'Boxplot Year Month Distribution');
plt.xticks(rotation=90)
plt.xlabel('Year Month')
plt.ylabel('MW')

sns.boxplot(x='year_month', y='PJME_MW', data=df_plot)
plt.ylabel('Consumption [MW]')
plt.xlabel('Year Month')
```

![](../Images/6ebf1b885874f81ddd75c7a79a975425.png)

4.2 PJME 年/月箱型图

可以看出，夏季/冬季月份的消费不确定性较低（即，当我们有峰值时），而春季/秋季则更为分散（即，当温度更为变化时）。最后，2018年夏季的消费高于2017年，可能是由于夏季气温较高。在进行特征工程时，请记得包含（如果有的话）温度曲线，它可能作为外生变量使用。

## 4.3 箱型图 — 天分布

另一个有用的图表是显示一周内消费分布的图表，这与每周消费季节性图表类似。

```py
df_plot = df[['day_str', 'day', 'PJME_MW']].sort_values(by='day')
plt.title(f'Boxplot Day Distribution')
plt.xlabel('Day of week')
plt.ylabel('MW')
sns.boxplot(x='day_str', y='PJME_MW', data=df_plot)
plt.ylabel('Consumption [MW]')
plt.xlabel('Day of week')
```

![](../Images/ad9f40a6f45d4957e36315b08229a7a7.png)

4.3 PJME 天箱型图

如前所见，消费在周末明显较低。不过，有几个异常值表明像“星期几”这样的日历特征确实有用，但可能无法完全解释该序列。

## 4.4 箱型图 — 小时分布

最后我们来看小时分布箱型图。它与每日消费季节性图类似，因为它展示了消费如何在一天内分布。接下来是代码：

```py
plt.title(f'Boxplot Hour Distribution');
plt.xlabel('Hour')
plt.ylabel('MW')
sns.boxplot(x='hour', y='PJME_MW', data=df)
plt.ylabel('Consumption [MW]')
plt.xlabel('Hour')
```

![](../Images/807ad6d26cec4a5520a05176ff392043.png)

4.4 PJME小时箱型图

注意，之前看到的“M”形状现在变得更加压缩。此外，有很多离群点，这告诉我们数据不仅依赖于日常季节性（例如今天12点的消费与昨天12点的消费相似），还依赖于其他因素，可能是某些外部气候因素，如温度或湿度。

## 5. 时间序列分解

如前所述，时间序列数据可能表现出多种模式。通常，将时间序列分解为多个成分，每个成分代表一个潜在的模式类别，这对分析非常有帮助。

我们可以将时间序列看作由三个成分组成：*趋势*成分、*季节性*成分和*残差*成分（包含时间序列中的其他所有因素）。对于某些时间序列（例如，能源消费序列），可能存在多个季节性成分，对应不同的季节周期（每日、每周、每月、每年）。

时间序列分解有两种主要类型：*加性*和*乘性*。

对于加性分解，我们将一个时间序列（𝑦）表示为季节性成分（𝑆）、趋势（𝑇）和残差（𝑅）的和：

![](../Images/e17ee10b8116c19d217f65fd55260dfd.png)

同样，乘性分解可以写成：

![](../Images/e242c6a97fc1dad2ad2fc5598e1b1ea4.png)

一般来说，加性分解最适合方差恒定的序列，而乘性分解最适合方差非平稳的时间序列。

在Python中，时间序列分解可以通过*Statsmodel*库轻松实现：

```py
df_plot = df[df['year'] == 2017].reset_index()
df_plot = df_plot.drop_duplicates(subset=['Datetime']).sort_values(by='Datetime')
df_plot = df_plot.set_index('Datetime')
df_plot['PJME_MW - Multiplicative Decompose'] = df_plot['PJME_MW']
df_plot['PJME_MW - Additive Decompose'] = df_plot['PJME_MW']

# Additive Decomposition
result_add = seasonal_decompose(df_plot['PJME_MW - Additive Decompose'], model='additive', period=24*7)

# Multiplicative Decomposition 
result_mul = seasonal_decompose(df_plot['PJME_MW - Multiplicative Decompose'], model='multiplicative', period=24*7)

# Plot
result_add.plot().suptitle('', fontsize=22)
plt.xticks(rotation=45)
result_mul.plot().suptitle('', fontsize=22)
plt.xticks(rotation=45)
plt.show()
```

![](../Images/1e6aac354ac11d476f1aa4d7b27c75ec.png)

5.1 PJME系列分解 — 加性分解。

![](../Images/ce50a9bfc23a07acefea8bb56927df5f.png)

5.2 PJME系列分解 — 乘性分解。

上述图表是指2017年。在这两种情况下，我们可以看到趋势有几个局部峰值，夏季的值较高。从季节性成分中，我们可以看到序列实际上有几个周期性，这个图突出显示了每周的周期性，但如果我们专注于同一年（2017年）中的某个月（如1月），日常季节性也会显现出来：

```py
df_plot = df[(df['year'] == 2017)].reset_index()
df_plot = df_plot[df_plot['month'] == 1]
df_plot['PJME_MW - Multiplicative Decompose'] = df_plot['PJME_MW']
df_plot['PJME_MW - Additive Decompose'] = df_plot['PJME_MW']

df_plot = df_plot.drop_duplicates(subset=['Datetime']).sort_values(by='Datetime')
df_plot = df_plot.set_index('Datetime')

# Additive Decomposition
result_add = seasonal_decompose(df_plot['PJME_MW - Additive Decompose'], model='additive', period=24*7)

# Multiplicative Decomposition 
result_mul = seasonal_decompose(df_plot['PJME_MW - Multiplicative Decompose'], model='multiplicative', period=24*7)

# Plot
result_add.plot().suptitle('', fontsize=22)
plt.xticks(rotation=45)
result_mul.plot().suptitle('', fontsize=22)
plt.xticks(rotation=45)
plt.show()
```

![](../Images/38852e4d20f443b471e67673aa590562.png)

5.3 PJME系列分解 — 加性分解，关注2017年1月。

![](../Images/c81aba4ff062cc4f588cb56dc5a7fe2f.png)

5.4 PJME系列分解 — 乘性分解，关注2017年1月。

## 6. 滞后分析

在时间序列预测中，滞后是指序列的过去值。例如，对于日常序列，第一个滞后是指序列前一天的值，第二个滞后是指前两天的值，以此类推。

滞后分析基于计算序列与该序列的滞后版本之间的相关性，这也称为*自相关*。对于k滞后的序列，我们定义自相关系数为：

![](../Images/012a2b1804fb4a04bed04ce8fa487838.png)

其中，*y* bar表示序列的均值，*k*表示滞后。

自相关系数构成了序列的*自相关函数*（ACF），这只是一个图表，展示了自相关系数与所考虑滞后数量之间的关系。

当数据具有趋势时，小滞后的自相关通常很大且为正，因为时间接近的观测值也通常数值相近。当数据呈现季节性时，与季节滞后（以及季节周期的倍数）对应的自相关值将大于其他滞后的自相关值。具有趋势和季节性的数据将表现出这些效应的组合。

实际上，更有用的函数是*部分自相关函数*（PACF）。它类似于ACF，只不过它只显示两个滞后之间的直接自相关。例如，滞后3的部分自相关指的是滞后1和滞后2无法解释的相关性。换句话说，部分自相关指的是某个滞后对当前时间值的直接影响。

在进入Python代码之前，需要强调的是，如果序列是*平稳*的，自相关系数会更为明显，因此通常最好先对序列进行差分以稳定信号。

话虽如此，下面是绘制不同时间段PACF的代码：

```py
from statsmodels.graphics.tsaplots import plot_pacf

actual = df['PJME_MW']
hours = range(0, 24, 4)

for hour in hours:
    plot_pacf(actual[actual.index.hour == hour].diff().dropna(), lags=30, alpha=0.01)
    plt.title(f'PACF - h = {hour}')
    plt.ylabel('Correlation')
    plt.xlabel('Lags')
    plt.show()
```

![](../Images/44dbff41f17b2e610d813960ecfb0c5b.png)

6.1 PJME滞后分析 — 部分自相关函数（h=0）。

![](../Images/d7d2d2e6976b720b7bff2e0a9c82c8e8.png)

6.2 PJME滞后分析 — 部分自相关函数（h=4）。

![](../Images/a32acfd15df172f5e3ace8d54843d633.png)

6.3 PJME滞后分析 — 部分自相关函数（h=8）。

![](../Images/30eaf2c891649106e9ec2e6a1ce93aa4.png)

6.4 PJME滞后分析 — 部分自相关函数（h=12）。

![](../Images/9337dca06ee8f7ca5b4ff221128e89f9.png)

6.5 PJME滞后分析 — 部分自相关函数（h=16）。

![](../Images/9f5f6bfa3c2cba4da0413c467eac4548.png)

6.6 PJME滞后分析 — 部分自相关函数（h=20）。

如你所见，PACF实际上就是绘制不同滞后的Pearson部分自相关系数。当然，未滞后的序列与自身有完美的自相关，因此滞后0的自相关系数永远是1。蓝色区域表示*置信区间*：如果某个滞后超过了这个区间，则说明该滞后在统计上是显著的，我们可以断言它具有重要性。

## 6.1 滞后分析 — 特征工程

滞后分析是时间序列特征工程中最具影响力的研究之一。如前所述，具有高相关性的滞后是序列中重要的滞后，因此应予以考虑。

一种广泛使用的特征工程技术是对数据集进行**按小时划分**。即将数据划分为24个子集，每个子集对应一天中的一个小时。这种做法有助于平滑信号，使其更加规律，从而更易于预测。

每个子集应进行特征工程，训练并进行微调。最终的预测将通过结合这24个模型的结果来实现。话虽如此，每个小时的模型都会有其特殊性，其中大部分将涉及重要的滞后。

在继续之前，让我们定义在进行滞后分析时可以处理的两种滞后类型：

1.  **自回归滞后**：接近滞后0的滞后，我们预期其值较高（近期的滞后更可能预测当前值）。它们是序列中趋势的体现。

1.  **季节性滞后**：指与季节性周期相关的滞后。当按小时划分数据时，它们通常代表每周的季节性。

请注意，自回归滞后1也可以被视为系列的*日常季节性滞后*。

现在让我们讨论一下上面打印的PACF图。

## 夜间小时

夜间小时（0, 4）的消费更多依赖于自回归滞后而非周滞后，因为最重要的滞后都集中在前五个。像7、14、21、28这样的季节性周期似乎并不太重要，这提醒我们在进行特征工程时要特别关注滞后1到5。

## 日间小时

白天小时（8, 12, 16, 20）的消费展现出自回归滞后和季节性滞后两者的特征。特别是对于8点和12点的小时——这时消费量特别高——而季节性滞后在接近夜间时变得不那么重要。对于这些子集，我们也应该包括季节性滞后以及自回归滞后。

最后，这里有一些关于特征工程滞后的建议：

+   不要考虑过多的滞后，因为这可能导致过拟合。通常，自回归滞后从1到7，而周滞后应该是7、14、21和28。然而，是否将它们作为特征并不是强制性的。

+   考虑非自回归或非季节性的滞后通常是一个坏主意，因为它们也可能导致过拟合。相反，尝试理解某个滞后为什么重要。

+   转换滞后通常会导致更强大的特征。例如，季节性滞后可以通过加权均值进行聚合，创建一个单一特征来表示序列的季节性。

# 免费资源

最后，我想提到一本非常有用（且免费的）解释时间序列的书，我个人使用过很多次：[Forecasting: Principles and Practice](https://otexts.com/fpp3/)。

尽管这本教材旨在使用R而非Python，但它为预测方法提供了很好的介绍，涵盖了时间序列分析的最重要方面。

# 结论

本文的目的是提供一个全面的时间序列预测探索性数据分析模板。

EDA（探索性数据分析）是任何数据科学研究中的基础步骤，因为它有助于理解数据的性质和特点，并为特征工程奠定基础，特征工程反过来可以显著提升模型的性能。

我们接着描述了一些最常用的时间序列EDA分析方法，这些方法既可以是统计/数学性的，也可以是图形化的。显然，本工作的目的是提供一个实践框架供大家入手，后续的研究需要根据所研究的历史序列类型和业务背景进行进一步的探索。

感谢你一直跟随我直到最后。

*除非另有说明，所有图片均来自作者。*
