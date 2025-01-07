# 多重共线性是否正在破坏你在市场营销组合建模中的因果推断？

> 原文：[https://towardsdatascience.com/is-multi-collinearity-destroying-your-causal-inferences-in-marketing-mix-modelling-78cb56017c73?source=collection_archive---------1-----------------------#2024-09-10](https://towardsdatascience.com/is-multi-collinearity-destroying-your-causal-inferences-in-marketing-mix-modelling-78cb56017c73?source=collection_archive---------1-----------------------#2024-09-10)

## 因果AI，探索因果推理与机器学习的整合

[](https://medium.com/@raz1470?source=post_page---byline--78cb56017c73--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--78cb56017c73--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--78cb56017c73--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--78cb56017c73--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--78cb56017c73--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--78cb56017c73--------------------------------) ·阅读时长16分钟·2024年9月10日

--

![](../Images/40d69b0f59c481ffa0a18c4d63f0e25c.png)

图片来自[NOAA](https://unsplash.com/@noaa?utm_source=medium&utm_medium=referral)，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 本系列内容是什么？

欢迎来到我的因果AI系列，我们将在这里探讨因果推理与机器学习模型的整合。预计将探索在不同商业环境中的多个实际应用。

在上一篇文章中，我们探讨了*使用CUPED和双重机器学习驱动实验*。今天，我们将重点讨论多重共线性如何破坏你在市场营销组合建模中的因果推断。

如果你错过了上一篇关于使用CUPED和双重机器学习驱动实验的文章，可以在这里查看：

[](/powering-experiments-with-cuped-and-double-machine-learning-34dc2f3d3284?source=post_page-----78cb56017c73--------------------------------) [## 使用CUPED和双重机器学习驱动实验

### 因果AI，探索因果推理与机器学习的整合

towardsdatascience.com](/powering-experiments-with-cuped-and-double-machine-learning-34dc2f3d3284?source=post_page-----78cb56017c73--------------------------------)

# 介绍

在本文中，我们将探讨多重共线性可能带来的危害，并评估一些可以用来应对它的方法。以下方面将被覆盖：

+   什么是多重共线性？

+   为什么它在因果推断中是一个问题？

+   为什么它在市场营销组合建模中如此常见？

+   我们如何检测它？

+   我们该如何解决这个问题？

+   贝叶斯先验简介。

+   这是一个 Python 案例研究，探讨了贝叶斯先验和随机预算调整如何有助于缓解多重共线性。

完整的笔记本可以在此处找到：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/is%20multi-collinearity%20destroying%20your%20mmm.ipynb?source=post_page-----78cb56017c73--------------------------------) [## causal_ai/notebooks/is multi-collinearity destroying your mmm.ipynb at main · raz1470/causal_ai

### 本项目介绍了因果人工智能以及它如何驱动商业价值。 - causal_ai/notebooks/is multi-collinearity…

github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/is%20multi-collinearity%20destroying%20your%20mmm.ipynb?source=post_page-----78cb56017c73--------------------------------)

# 什么是多重共线性？

多重共线性发生在回归模型中的两个或更多自变量之间高度相关时。这种高度的相关性意味着它们提供了重叠的信息，使得模型难以区分每个变量的独立效应。

让我们以市场营销为例。你销售的产品需求高度季节性——因此，在需求高峰期间增加营销支出是有意义的。然而，如果电视和社交媒体支出都遵循相同的季节性模式，那么模型就会难以准确确定每个渠道的独立贡献。

![](../Images/4f9efd582aef0ef90cd4d3f990247a86.png)

用户生成的图片

# 为什么它在因果推断中是一个问题？

多重共线性可能导致相关变量的系数变得不稳定和偏倚。当存在多重共线性时，回归系数的标准误差往往会膨胀。这意味着估计的 uncertainty 增加，使得很难判断一个变量是否真的具有显著性。

让我们回到市场营销的例子，即使电视广告和社交媒体都推动了销售，模型可能也很难分离它们的影响，因为膨胀的标准误差使得系数估计不可靠。

我们可以通过在 Python 中模拟一些例子来更好地理解。请注意我们如何将社交媒体支出和电视支出的系数分别设置为 0.10 和 0.20。

***例子 1 —— 每个渠道的营销支出相等，导致系数偏倚：***

```py
# Example 1 - marketing spend on each channel is equal: biased coefficients
np.random.seed(150)

tv_spend = np.random.normal(0, 50, 1000)
social_spend = tv_spend
sales = 0.10 * tv_spend + 0.20 * social_spend
X = np.column_stack((tv_spend, social_spend))
clf = LinearRegression()
clf.fit(X, sales)

print(f'Coefficients: {clf.coef_}')
```

![](../Images/dce34bbbf08e16494d4361304dea2ba1.png)

用户生成的图片

***例子 2 —— 每个渠道的营销支出遵循相同的趋势，这次导致系数符号翻转：***

```py
# Example 2 - marketing spend on each channel follows the same trend: biased coefficients and sign flip
np.random.seed(150)

tv_spend = np.random.normal(0, 50, 1000)
social_spend = tv_spend * 0.50
sales = 0.10 * tv_spend + 0.20 * social_spend
X = np.column_stack((tv_spend, social_spend))
clf = LinearRegression()
clf.fit(X, sales)

print(f'Coefficients: {clf.coef_}')
```

![](../Images/ada2358ce580c187767d675ea8067ab4.png)

用户生成的图片

***例子 3 —— 添加随机噪声使得模型能够估计正确的系数：***

```py
# Example 3 - random noise added to marketing spend: correct coefficients
np.random.seed(150)

tv_spend = np.random.normal(0, 50, 1000)
social_spend = tv_spend * 0.50 + np.random.normal(0, 1, 1000)
sales = 0.10 * tv_spend + 0.20 * social_spend
X = np.column_stack((tv_spend, social_spend))
clf = LinearRegression()
clf.fit(X, sales)

print(f'Coefficients: {clf.coef_}')
```

![](../Images/c515596d8efc79fa0dfaff399b6ec728.png)

用户生成的图片

此外，多重共线性还可能导致所谓的符号翻转现象，即变量的效应方向（正向或负向）可能会意外反转。例如，尽管你知道社交媒体广告应该对销售产生正面影响，但模型可能会显示一个负系数，原因仅仅是它与电视支出的高度相关性。我们可以在示例2中看到这一点。

# 为什么在营销混合建模中如此常见？

我们已经提到一个关键问题：营销团队通常对需求模式有较强的理解，并利用这些知识来设定预算。通常，他们会在需求高峰期增加多个渠道的支出。虽然从战略角度来看这很有意义，但也可能无意中造成多重共线性问题。

即便对于需求相对稳定的产品，如果市场营销团队每周或每月按相同百分比调整每个渠道的投入，这也会导致多重共线性问题。

我在营销混合模型（MMM）中见到的另一个多重共线性原因是因果图（DAGs）指定不当。如果我们只是将所有变量放入一个平坦的回归模型中，很可能会遇到多重共线性问题。举个例子——如果付费搜索展示量可以通过电视和社交媒体支出来解释，那么将它与电视和社交媒体一起放入一个平坦的线性回归模型中，很可能会导致多重共线性。

![](../Images/bee63d262db8e900adee635716473865.png)

用户生成的图像

# 我们如何检测它？

检测多重共线性对于防止它扭曲因果推断至关重要。以下是一些常见的识别方法：

## 相关性

检测多重共线性的一种简单而有效的方法是检查相关矩阵。该矩阵显示了数据集中所有变量之间的成对相关性。如果两个预测变量的相关系数接近+1或-1，说明它们高度相关，这可能表明存在多重共线性。

## **方差膨胀因子（VIF）**

量化回归系数的方差由于多重共线性而被膨胀的程度：

![](../Images/dbdeadc19a78f363cf86b8cc91c7a39b.png)

用户生成的图像

R平方是通过将所有其他自变量回归到所选变量上得到的。如果R平方很高，意味着所选变量可以通过其他自变量来预测（这将导致所选变量的VIF很高）。

关于VIF检测多重共线性的经验法则阈值——然而，我没有找到任何有说服力的资源来支持这些阈值，因此这里不再引用它们。

## 标准误差

回归系数的标准误差告诉你该系数的估计精度。它是系数估计方差的平方根。较高的标准误差可能表明存在多重共线性。

## 仿真

另外，了解上面提到的三种方法是有帮助的，但要量化你是否存在严重的多重共线性问题仍然可能很困难。你可以采取的另一种方法是运行一个已知系数的模拟，然后查看你的模型能多好地估计这些系数。让我们通过一个营销组合模型（MMM）示例来说明：

+   像往常一样提取渠道支出和销售数据。

```py
-- example SQL code to extract data
select
  observation_date,
  sum(tv_spend) as tv_spend,
  sum(social_spend) as social_spend,
  sum(sales) as sales
from mmm_data_mart
group by
  observation_date;
```

+   创建数据生成过程，为每个渠道设置一个系数。

```py
# set coefficients for each channel using actual spend data
marketing_contribution = tv_spend * 0.10 + social_spend * 0.20

# calculate the remaining contribution
other_contribution = sales - marketing_contribution

# create arrays for regression
X = np.column_stack((tv_spend, social_spend, other_contribution))
y = sales
```

+   训练模型并将估计的系数与上一步设置的系数进行比较。

```py
# train regression model
clf = LinearRegression()
clf.fit(X, y)

# recover coefficients
print(f'Recovered coefficients: {clf.coef_}')
```

现在我们知道如何识别多重共线性，接下来让我们探讨如何应对它！

# 我们如何应对它？

解决多重共线性问题有几种策略：

1.  **去除一个相关变量**

    这是一种直接减少冗余的方式。然而，盲目去除一个变量是有风险的——尤其是当去除的变量是混杂变量时。一个有帮助的步骤是确定因果图（DAG）。理解因果关系可以帮助你评估是否去除一个相关变量仍然能够进行有效的推断。

1.  **合并变量**

    当两个或多个变量提供相似的信息时，你可以将它们合并。这种方法可以减少模型的维度，缓解多重共线性的风险，同时尽可能保留更多信息。与之前的方法一样，理解数据的因果结构至关重要。

1.  **正则化技术**

    正则化方法，如岭回归或套索回归，是应对多重共线性的强大工具。这些技术通过向模型复杂性添加惩罚，缩小相关预测变量的系数。岭回归专注于减少所有系数的大小，而套索回归则可以将某些系数缩小到零，从而有效地选择一个预测变量的子集。

1.  **贝叶斯先验**

    使用贝叶斯回归技术，你可以根据现有知识为参数引入先验分布。这使得模型能够基于这些先验进行“正则化”，从而减少多重共线性的影响。通过为参数值提供合理的范围，它可以防止对高度相关变量的过拟合。我们将在案例研究中深入探讨这种方法，以展示它的有效性。

1.  **随机预算调整**

    另一种策略，特别是在营销组合建模（MMM）中非常有用，是在渠道层面引入随机调整你的营销预算。通过随机调整预算，你可以开始观察每个渠道的独立效应。这个方法有两个主要挑战：(1) 营销团队的支持，(2) 一旦开始运行，可能需要几个月甚至几年才能收集到足够的数据用于模型。我们也将在案例研究中通过一些模拟来讨论这一点。

接下来我们将在案例研究中测试其中的一些策略。

# 贝叶斯先验简介

深入探讨贝叶斯先验超出了本文的范围，但为了确保我们能够跟上案例研究，让我们简要介绍一些其背后的直觉。

贝叶斯先验代表我们在观察到任何数据之前对参数值的初步信念。在贝叶斯方法中，我们将这些先验与实际数据（通过似然函数）结合起来，以更新我们的理解并计算后验分布，后者反映了先验信息和数据的结合。

简单来说：在构建 MMM 时，我们需要为每个变量的系数提供一些先验信念。我们不是提供一个固定的上下界，而是提供一个分布。然后模型会在这个分布中搜索，并使用数据来计算后验分布。通常，我们使用该后验分布的均值来获得我们的系数估计。

当然，贝叶斯先验不止这些，但上面的解释为我们提供了一个坚实的起点！

# 案例研究

你最近加入了一家初创公司，他们已经运行了几年的营销策略。现在他们想通过 MMM 来衡量效果，但他们早期的尝试得出了不合逻辑的结果（电视的贡献是负的！）。看起来他们的问题源于每个营销渠道的负责人都是根据需求预测来设定预算，这导致了多重共线性问题。你的任务是评估情况并提出下一步的建议。

## 数据生成过程

让我们从创建一个具有以下属性的 Python 数据生成函数开始：

+   需求由三个部分组成：趋势、季节性和噪音。

+   需求预测模型来自数据科学团队，能够在 +/- 5% 的准确度内做出预测。

+   这个需求预测模型由营销团队用来设定社交媒体和电视支出的预算——我们可以通过 `spend_rand_change` 参数为这些预算添加一些随机变化。

+   营销团队在电视上的支出是社交媒体的两倍。

+   销售由需求、社交媒体支出和电视支出的线性组合驱动。

+   社交媒体和电视支出的系数可以通过 `true_coef` 参数来设置。

```py
def data_generator(spend_rand_change, true_coef):
    '''
    Generate simulated marketing data with demand, forecasted demand, social and TV spend, and sales.

    Args:
        spend_rand_change (float): Random variation parameter for marketing spend.
    true_coef (list): True coefficients for demand, social media spend, and TV spend effects on sales.

    Returns:
        pd.DataFrame: DataFrame containing the simulated data.
    '''

    # Parameters for data generation
    start_date = "2018-01-01"
    periods = 365 * 3  # Daily data for three years
    trend_slope = 0.01  # Linear trend component
    seasonal_amplitude = 5  # Amplitude of the seasonal component
    seasonal_period = 30.44  # Monthly periodicity
    noise_level = 5  # Level of random noise in demand

    # Generate time variables
    time = np.arange(periods)
    date_range = pd.date_range(start=start_date, periods=periods)

    # Create demand components
    trend_component = trend_slope * time
    seasonal_component = seasonal_amplitude * np.sin(2 * np.pi * time / seasonal_period)
    noise_component = noise_level * np.random.randn(periods)

    # Combine to form demand series
    demand = 100 + trend_component + seasonal_component + noise_component

    # Initialize DataFrame
    df = pd.DataFrame({'date': date_range, 'demand': demand})

    # Add forecasted demand with slight random variation
    df['demand_forecast'] = df['demand'] * np.random.uniform(0.95, 1.05, len(df))

    # Simulate social media and TV spend with random variation
    df['social_spend'] = df['demand_forecast'] * 10 * np.random.uniform(1 - spend_rand_change, 1 + spend_rand_change, len(df))
    df['tv_spend'] = df['demand_forecast'] * 20 * np.random.uniform(1 - spend_rand_change, 1 + spend_rand_change, len(df))
    df['total_spend'] = df['social_spend'] + df['tv_spend']

    # Calculate sales based on demand, social, and TV spend, with some added noise
    df['sales'] = (
        df['demand'] * true_coef[0] + 
        df['social_spend'] * true_coef[1] + 
        df['tv_spend'] * true_coef[2]
    )
    sales_noise = 0.01 * df['sales'] * np.random.randn(len(df))
    df['sales'] += sales_noise

    return df
```

## 初步评估

现在让我们模拟一些数据，假设没有对营销团队如何设定预算进行随机变化——我们将尝试估计真实的系数。下面的函数用于训练回归模型：

```py
def run_reg(df, features, target):
    '''
    Runs a linear regression on the specified features to predict the target variable.

    Args:
        df (pd.DataFrame): The input data containing features and target.
    features (list): List of column names to be used as features in the regression.
    target (str): The name of the target column to be predicted.
    Returns:
        np.ndarray: Array of recovered coefficients from the linear regression model.
    '''

    # Extract features and target values
    X = df[features].values
    y = df[target].values

    # Initialize and fit linear regression model
    model = LinearRegression()
    model.fit(X, y)

    # Output recovered coefficients
    coefficients = model.coef_
    print(f'Recovered coefficients: {coefficients}')

    return coefficients
```

```py
np.random.seed(40)

true_coef = [0.35, 0.15, 0.05]

features = [
    "demand",
    "social_spend",
    "tv_spend"
]

target = "sales"

sim_1 = data_generator(0.00, true_coef)
reg_1 = run_reg(sim_1, features, target)

print(f"True coefficients: {true_coef}")
```

![](../Images/b67455771d7ccc09c657dc107ed4ebde.png)

用户生成的图像

我们可以看到，社交支出的系数被低估，而电视支出的系数被高估。幸好你没有将这个模型交给营销团队来优化他们的预算——否则结果可能会很糟糕！

在短期内，使用贝叶斯先验是否能得到更少偏差的系数？

从长远来看，随机调整预算是否能创建一个不受多重共线性影响的数据集？

让我们来找找看！

## 贝叶斯先验

让我们从探索贝叶斯先验开始……

我们将使用我最喜欢的MMM实现——pymc marketing：

[## Guide - pymc-marketing 0.8.0 文档

### 编辑描述

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/stable/guide/index.html?source=post_page-----78cb56017c73--------------------------------)

我们将使用在初步评估中生成的相同数据：

```py
date_col = "date"

y_col = "sales"

channel_cols = ["social_spend",
                "tv_spend"]

control_cols = ["demand"]

X = sim_1[[date_col] + channel_cols + control_cols]
y = sim_1[y_col]
```

在开始建模之前，让我们看看每个变量的贡献情况：

```py
# calculate contribution
true_contributions = [round(np.sum(X["demand"] * true_coef[0]) / np.sum(y), 2), 
                      round(np.sum(X["social_spend"] * true_coef[1]) / np.sum(y), 2), 
                      round(np.sum(X["tv_spend"] * true_coef[2]) / np.sum(y), 2)]
true_contributions
```

![](../Images/9e9c1b03eb82816012b3f54d1f83d85f.png)

用户生成的图片

## 贝叶斯（默认）先验

让我们看看如果使用默认先验会得到什么结果。下面你可以看到有很多先验！这是因为我们必须为截距、广告库存、饱和度变换等提供先验。我们关注的是饱和度β——这相当于我们试图估算的变量系数。

```py
mmm_default = MMM(
    adstock="geometric",
    saturation="logistic",
    date_column=date_col,
    channel_columns=channel_cols,
    control_columns=control_cols,
    adstock_max_lag=4,
    yearly_seasonality=2,
)

mmm_default.default_model_config
```

![](../Images/993475b7375f50b9e26666de97bda68a.png)

用户生成的图片

我们必须提供一个分布。HalfNormal 是一个合理的选择，用于渠道系数，因为我们知道它们不能为负数。下面我们将可视化这个分布，以帮助理解它：

```py
sigma = 2

x1 = np.linspace(0, 10, 1000)
y1 = halfnorm.pdf(x1, scale=sigma)

plt.figure(figsize=(8, 6))
plt.plot(x1, y1, 'b-')
plt.fill_between(x1, y1, alpha=0.2, color='blue')
plt.title('Saturation beta: HalfNormal Distribution (sigma=2)')
plt.xlabel('Saturation beta')
plt.ylabel('Probability Density')
plt.grid(True)
plt.show()
```

![](../Images/6518eafb76d33b0992832a1d80355f98.png)

用户生成的图片

现在我们准备好训练模型并提取每个渠道的贡献了。如前所述，我们的系数存在偏差（我们知道这一点，因为每个渠道的贡献值不正确——社交媒体应该是50%，电视应该是35%）。然而，令人有趣的是，与我们之前运行线性回归时相比，它们现在更接近真实贡献。这实际上是营销团队的一个合理起点！

```py
mmm_default.fit(X, y)
mmm_default.plot_waterfall_components_decomposition();
```

![](../Images/8817a5d2dfce6f12cf8a7d22899540ab.png)

用户生成的图片

## 贝叶斯（自定义）先验

在我们继续之前，让我们借此机会思考一下自定义先验。我们可以做出一个（非常大胆的）假设，即每个渠道的投资回报率是相似的（或者在我们没有收入的情况下，是每销售的成本）。因此，我们可以使用渠道的支出分布来设置一些自定义先验。

由于MMM类在目标和特征中都进行特征缩放，因此先验也需要在缩放后的空间中提供。正如你在下面的代码中看到的那样，这实际上让我们做起来非常容易：

```py
total_spend_per_channel = df[channel_cols].sum(axis=0)
spend_share = total_spend_per_channel / total_spend_per_channel.sum()

n_channels = df[channel_cols].shape[1]
prior_sigma = n_channels * spend_share.to_numpy()

spend_share
```

![](../Images/7acd357f45c30cf3fcc649125f2df86a.png)

用户生成的图片

然后我们需要将自定义的先验传入模型中。

```py
my_model_config = {'saturation_beta': {'dist': 'HalfNormal', 'kwargs': {'sigma': prior_sigma}}}

mmm_priors = MMM(
    model_config=my_model_config,
    adstock="geometric",
    saturation="logistic",
    date_column=date_col,
    channel_columns=channel_cols,
    control_columns=control_cols,
    adstock_max_lag=4,
    yearly_seasonality=2,
)

mmm_priors.default_model_config
```

![](../Images/124cb5d9d63721ef1666330967f0af0e.png)

用户生成的图片

当我们训练模型并提取系数时，我们看到先验已经起作用，电视现在的贡献最大（因为我们在电视上的支出超过了社交媒体）。然而，这实际上是非常错误的，说明了为什么我们在设置先验时必须如此小心！营销团队应该考虑进行一些实验来帮助他们设置先验。

```py
mmm_priors.fit(X, y)
mmm_priors.plot_waterfall_components_decomposition();
```

![](../Images/a835f00a98e53d3d699a913e41cfc76a.png)

## 随机预算调整

现在我们已经制定了短期计划，让我们来考虑长期计划。如果我们能够说服市场营销团队每个月对其营销渠道的预算进行小幅随机调整，是否能够创建一个没有多重共线性的数据集？

以下代码使用数据生成器函数，并模拟了一系列随机的支出调整：

```py
np.random.seed(40)

# Define list to store results
results = []

# Loop through a range of random adjustments to spend
for spend_rand_change in np.arange(0.00, 0.05, 0.001):
    # Generate simulated data with the current spend_rand_change
    sim_data = data_generator(spend_rand_change, true_coef)

    # Run the regression
    coefficients = run_reg(sim_data, features=['demand', 'social_spend', 'tv_spend'], target='sales')

    # Store the spend_rand_change and coefficients for later plotting
    results.append({
        'spend_rand_change': spend_rand_change,
        'coef_demand': coefficients[0],
        'coef_social_spend': coefficients[1],
        'coef_tv_spend': coefficients[2]
    })

# Convert results to DataFrame for easy plotting
results_df = pd.DataFrame(results)

# Plot the coefficients as a function of spend_rand_change
plt.figure(figsize=(10, 6))
plt.plot(results_df['spend_rand_change'], results_df['coef_demand'], label='Demand Coef', color='r', marker='o')
plt.plot(results_df['spend_rand_change'], results_df['coef_social_spend'], label='Social Spend Coef', color='g', marker='o')
plt.plot(results_df['spend_rand_change'], results_df['coef_tv_spend'], label='TV Spend Coef', color='b', marker='o')

# Add lines for the true coefficients
plt.axhline(y=true_coef[0], color='r', linestyle='--', label='True Demand Coef')
plt.axhline(y=true_coef[1], color='g', linestyle='--', label='True Social Spend Coef')
plt.axhline(y=true_coef[2], color='b', linestyle='--', label='True TV Spend Coef')

plt.title('Regression Coefficients vs Spend Random Change')
plt.xlabel('Spend Random Change')
plt.ylabel('Coefficient Value')
plt.legend()
plt.grid(True)
plt.show()
```

从结果中我们可以看到，仅仅对每个渠道的预算进行一点小的随机调整，就能摆脱多重共线性的困扰！

![](../Images/ca3eef8ed2ee2feb125463410751a119.png)

用户生成的图像

值得注意的是，如果我更改随机种子（几乎就像重新抽样一样），系数的起始点会有所不同——然而，不论我使用哪个种子，系数在支出随机变化 1% 后会稳定下来。我确信这会根据你的数据生成过程而有所不同，因此一定要使用你自己的数据进行测试！

# 最终思考

+   尽管本文的重点是多重共线性，但最重要的收获是模拟数据并尝试估计已知系数的重要性（记住，这些系数是你自己设置的，所以你知道它们）——如果你想对结果充满信心，这是一个必不可少的步骤！

+   在做 MMM 时，使用你实际的支出和销售数据作为模拟的基础是很有帮助的——这将帮助你了解是否存在多重共线性问题。

+   如果你使用实际的支出和销售数据，你也可以进行一个随机预算调整的模拟，帮助制定一个合适的随机化策略给市场营销团队。记住，我的模拟是为了阐明一个观点而简化的——我们可以设计一个更有效的策略，例如测试每个渠道响应曲线的不同区域。

+   贝叶斯方法可能是一个陡峭的学习曲线——我们还可以采取另一种方法，即使用受限回归，在这种方法中，你根据先验知识为每个渠道系数设定上下限。

+   如果你设置贝叶斯先验，非常重要的一点是要透明地说明它们是如何工作的，以及它们是如何被选择的。如果你选择使用渠道支出分布作为先验，那么假设每个渠道具有类似的投资回报率（ROI）需要得到相关利益相关者的批准。

+   贝叶斯先验并不是魔法！理想情况下，你应当使用实验结果来设置你的先验——值得查看 pymc 营销如何处理这个问题：

[](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_lift_test.html?source=post_page-----78cb56017c73--------------------------------) [## Lift Test 校准 - pymc-marketing 0.8.0 文档

### 你可能听过这句话：“所有模型都是错的，有些模型是有用的。”这在许多领域都适用，且确实如此……

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/stable/notebooks/mmm/mmm_lift_test.html?source=post_page-----78cb56017c73--------------------------------)

就是这样，希望你喜欢这篇文章！如果你想继续跟随我一起探索因果 AI，欢迎关注我——在下一篇文章中，我们将深入探讨*不良控制*的主题！
