# 精通 Python 中的市场营销组合建模

> 原文：[https://towardsdatascience.com/mastering-marketing-mix-modelling-in-python-7bbfe31360f9?source=collection_archive---------1-----------------------#2024-09-26](https://towardsdatascience.com/mastering-marketing-mix-modelling-in-python-7bbfe31360f9?source=collection_archive---------1-----------------------#2024-09-26)

## 实践指南的第 1 部分，帮助你掌握 **pymc** 中的 MMM

[](https://medium.com/@raz1470?source=post_page---byline--7bbfe31360f9--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--7bbfe31360f9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7bbfe31360f9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7bbfe31360f9--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--7bbfe31360f9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7bbfe31360f9--------------------------------) ·阅读时间 21 分钟·2024年9月26日

--

![](../Images/963728066476cce1befa283fb3499efc.png)

用户生成的图像

# 这个系列是关于什么的？

欢迎来到我的市场营销组合建模（MMM）系列的第一部分，这是一本实践指南，旨在帮助你掌握 MMM。在这个系列中，我们将涵盖关键主题，如模型训练、验证、校准和预算优化，所有这些都使用强大的 **pymc-marketing** Python 包。无论你是 MMM 的新手，还是想要提升技能，本系列将为你提供实用的工具和见解，帮助你改进营销策略。

# 介绍

在本文中，我们将首先介绍贝叶斯 MMM 的一些背景知识，包括以下主题：

+   我们可以使用哪些开源包？

+   什么是贝叶斯 MMM？

+   什么是贝叶斯先验？

+   **pymc-marketing** 中的默认先验是否合理？

然后我们将进入使用 **pymc-marketing** 包的 Python 演示，深入探讨以下领域：

+   模拟数据

+   训练模型

+   验证模型

+   参数恢复

完整的笔记本可以在这里找到：

[](https://github.com/raz1470/pymc_marketing/blob/main/notebooks/1.%20training%20marketing%20mix%20models%20%28MMM%29%20in%20python.ipynb?source=post_page-----7bbfe31360f9--------------------------------) [## pymc_marketing/notebooks/1\. 在 Python 中训练市场营销组合模型（MMM）.ipynb 主要内容 ·…

### 一个 pymc_marketing 包的演示。通过创建一个账户来贡献给 raz1470/pymc_marketing 的开发…

github.com](https://github.com/raz1470/pymc_marketing/blob/main/notebooks/1.%20training%20marketing%20mix%20models%20%28MMM%29%20in%20python.ipynb?source=post_page-----7bbfe31360f9--------------------------------)

# 1.0 MMM 背景

让我们先简要概述一下营销组合模型（MMM）。我们将探索可用的各种开源包，并深入了解贝叶斯MMM的原理，包括先验概念。最后，我们将评估**pymc-marketing**中使用的默认先验，以衡量它们的适用性和有效性。

## 1.1 我们可以使用哪些开源包？

在谈到MMM时，我们可以使用几个开源包：

![](../Images/52f6723ecd7065016292c73060be7eba.png)

用户生成的图片

有几个令人信服的理由，在本系列中我们要专注于**pymc-marketing**：

1.  **Python兼容性**：与仅支持R的Robyn不同，pymc-marketing服务于更多喜欢使用Python的用户。

1.  **当前可用性**：Meridian尚未发布（截至2024年9月23日），这使得pymc-marketing现在是一个更容易访问的选择。

1.  **未来考虑**：一旦Meridian发布，LightweightMMM将被淘汰，这进一步巩固了寻找可靠替代品的必要性。

1.  **积极开发**：pymc-marketing通过其庞大的贡献者社区不断进行增强，确保它始终跟上新特性和改进。

你可以在这里查看pymc-marketing包，他们提供了一些很棒的笔记本，展示了该包的一些功能：

[## 使用方法 - pymc-marketing 0.9.0 文档

### 这里你会找到一系列示例和使用PyMC-Marketing MMM及CLV模型的使用指南。

[www.pymc-marketing.io](https://www.pymc-marketing.io/en/stable/notebooks/index.html?source=post_page-----7bbfe31360f9--------------------------------)

## 1.2 什么是贝叶斯营销组合模型（MMM）？

你会注意到，上面突出的4个包中有3个采用了贝叶斯方法。所以让我们花点时间了解一下贝叶斯MMM是什么样的！对于初学者来说，贝叶斯分析有点像进入了一个兔子洞，但我们可以将其分解为5个关键点：

![](../Images/fe739acf49ab2eca4bf2ebf2e8a8805b.png)

用户生成的图片

1.  **贝叶斯定理** — 在贝叶斯MMM中，贝叶斯定理用于在我们收集到新数据时更新我们关于营销渠道如何影响销售的信念。例如，如果我们对电视广告如何影响销售有一些初步的看法，贝叶斯定理允许我们在看到实际的销售和电视广告支出数据后，完善这一看法。

1.  **P(θ)** — **先验**代表我们对模型中参数的初步信念，例如电视广告支出对销售的影响。例如，如果我们做了一个地理提升测试，估计电视广告使销售增长了5%，我们可能会根据这个估计设置一个先验。

1.  **P(Data | θ)** — **似然函数**，它捕捉了在特定营销投入水平下观察到销售数据的概率。例如，如果你在社交媒体上花费了£100,000并看到了相应的销售增长，似然函数会告诉我们，根据社交媒体的假定效果，这种销售增长的可能性有多大。

1.  **P(θ | 数据)** — **后验分布**是我们在贝叶斯MMM中最关心的内容——它是我们在结合数据和先验假设后，对不同营销渠道如何影响销售的更新信念。例如，在观察到新的销售数据后，我们最初认为电视广告会带来5%的销售增长，可能会调整为更精确的估计，比如4.7%。

1.  **采样** — 由于贝叶斯MMM涉及多个参数的复杂模型（例如广告效果、饱和度、营销渠道效应、控制效应等），直接计算后验分布可能很困难。MCMC（马尔可夫链蒙特卡洛）方法通过从每个参数的分布中生成样本来近似后验分布。该方法在处理难以解析求解的模型时特别有用。

## 1.3 什么是贝叶斯先验？

贝叶斯先验作为概率分布提供。我们不是为参数指定一个固定值，而是提供一系列潜在的值，并附上每个值为真实值的可能性。常用的先验分布包括：

+   **正态分布**: 用于期望参数值围绕均值聚集的情况。

+   **半正态分布**: 用于希望强制参数为正的情况。

+   **Beta**: 用于参数约束在0和1之间的情况。

+   **伽马分布**: 用于正值且偏斜的参数。

你可能会听到“信息性先验”这个术语。理想情况下，我们根据专家知识或随机实验提供这些先验。信息性先验反映了对参数值的强烈信念。然而，当这种做法不可行时，我们可以使用非信息性先验，这些先验将概率分布在广泛的值范围内。非信息性先验允许数据主导后验分布，从而防止先验假设过度影响结果。

## 1.4 **pymc-marketing**中的默认先验是否合理？

在使用**pymc-marketing**包时，默认先验设计为弱信息性，这意味着它们提供广泛的指导，而不是过于具体。它们引导模型，但不会过度限制模型。这样的平衡确保了先验指导模型，但又不会遮盖数据本身。

要建立可靠的模型，理解默认先验是至关重要的，而不是盲目使用它们。在接下来的部分中，我们将探讨在**pymc-marketing**中使用的各种先验，并解释为什么默认选择对于营销组合模型是合理的。

我们可以通过以下代码开始检查默认先验：

```py
dummy_model = MMM(
    date_column="",
    channel_columns=[""],
    adstock=GeometricAdstock(l_max=4),
    saturation=LogisticSaturation(),
)
dummy_model.default_model_config
```

![](../Images/9400cb34fdda8bf521e61419a8d59c17.png)

用户生成的图片

上面我已打印出包含7个默认先验的字典——让我们首先简要了解每个先验是什么：

+   **截距** — 在没有任何营销支出或其他变量的情况下，销售或目标变量的基准水平。它为模型设定了起点。

+   **likelihood** — 当您更侧重于可能性时，模型将更多依赖观察到的数据，较少依赖先验数据。这意味着模型将更加数据驱动，使得观察结果对参数估计的影响更大。

+   **gamma_control** — 控制外部因素的变量，如宏观经济条件、节假日或其他可能影响销售的非营销变量。

+   **gamma_fourier** — 用于建模数据中季节性的傅里叶项，捕捉销售中反复出现的模式或周期。

+   **adstock_alpha** — 控制广告库存效应，决定营销支出的影响力随时间衰减的程度。

+   **saturation_lamda** — 定义饱和曲线的陡峭度，决定随着营销支出的增加，回报递减的速度。

+   **saturation_beta** — 营销支出系数，衡量营销支出对目标变量（例如销售）的直接影响。

接下来，我们将专注于深入理解营销和控制变量的参数。

## 1.4.1 广告库存阿尔法

广告库存反映了一个观点，即营销活动的影响是延迟的，并且随着时间的推移逐渐积累。广告库存阿尔法（衰减率）控制影响力随时间衰减的速度，决定营销活动的影响力在多长时间内持续影响销售。

使用贝塔分布作为广告库存阿尔法的先验。我们首先可视化贝塔分布：

```py
alpha = 1
beta_param = 3

x1 = np.linspace(0, 1, 100)
y1 = beta.pdf(x1, alpha, beta_param)

plt.figure(figsize=(8, 5))
plt.plot(x1, y1, color='blue')
plt.fill_between(x1, y1, color='blue', alpha=0.3)
plt.title('Geometric Adstock: Beta distribution (alpha=1, beta=3)')
plt.xlabel('Adstock alpha')
plt.ylabel('Probability density')
plt.grid(True)
plt.show()
```

![](../Images/e3671fd16838d7163f2631f2d0ded101.png)

用户生成图像

我们通常将广告库存阿尔法值限制在0到1之间，因此贝塔分布是一个合理的选择。具体而言，使用beta(1, 3)作为广告库存阿尔法的先验，反映了在大多数情况下，衰减率应该相对较高，这意味着营销活动的影响很快就会消失。

为了进一步加深直觉，我们可以可视化不同广告库存阿尔法值的影响：

```py
raw_spend = np.array([1000, 900, 800, 700, 600, 500, 400, 300, 200, 100, 0, 0, 0, 0, 0, 0])

adstock_spend_1 = geometric_adstock(x=raw_spend, alpha=0.20, l_max=8, normalize=True).eval().flatten()
adstock_spend_2 = geometric_adstock(x=raw_spend, alpha=0.50, l_max=8, normalize=True).eval().flatten()
adstock_spend_3 = geometric_adstock(x=raw_spend, alpha=0.80, l_max=8, normalize=True).eval().flatten()

plt.figure(figsize=(10, 6))

plt.plot(raw_spend, marker='o', label='Raw Spend', color='blue')
plt.fill_between(range(len(raw_spend)), 0, raw_spend, color='blue', alpha=0.2)

plt.plot(adstock_spend_1, marker='o', label='Adstock (alpha=0.20)', color='orange')
plt.fill_between(range(len(adstock_spend_1)), 0, adstock_spend_1, color='orange', alpha=0.2)

plt.plot(adstock_spend_2, marker='o', label='Adstock (alpha=0.50)', color='red')
plt.fill_between(range(len(adstock_spend_2)), 0, adstock_spend_2, color='red', alpha=0.2)

plt.plot(adstock_spend_3, marker='o', label='Adstock (alpha=0.80)', color='purple')
plt.fill_between(range(len(adstock_spend_3)), 0, adstock_spend_3, color='purple', alpha=0.2)

plt.xlabel('Weeks')
plt.ylabel('Spend')
plt.title('Geometric Adstock')
plt.legend()
plt.grid(True)
plt.show()
```

![](../Images/bdaf927c95b02e76d85e9809154dbf5c.png)

用户生成图像

+   较低的阿尔法值影响较小，适用于那些具有直接反应的渠道，例如付费社交广告，目标群体为已访问过您网站的潜在客户，且广告有明确的行动号召。

+   较高的阿尔法值具有更强的影响力，适用于那些具有长期效果的渠道，例如品牌建设视频，目标群体为广泛的潜在客户，且没有明确的行动号召。

## 1.4.2 饱和度λ

随着营销支出的增加，销售的增量影响逐渐减小——这就是所谓的饱和效应。饱和度λ控制饱和曲线的陡峭度，决定营销支出增加时回报递减的速度。

使用伽马分布作为饱和度λ的先验。我们首先来了解伽马分布的形态：

```py
alpha = 3
beta = 1

x2 = np.linspace(0, 10, 1000)
y2 = gamma.pdf(x2, alpha, scale=1/beta)

plt.figure(figsize=(8, 6))
plt.plot(x2, y2, 'b-')
plt.fill_between(x2, y2, alpha=0.2, color='blue')
plt.title('Logistic Saturation: Gamma Distribution (alpha=3, beta=1)')
plt.xlabel('Saturation lamda')
plt.ylabel('Probability density')
plt.grid(True)
plt.show()
```

![](../Images/479cf4d94568e019efdd618fce3ed7df.png)

用户生成图像

初看之下，可能很难理解为什么 gamma 分布是一个合理的先验选择，但绘制不同 lamda 值的影响有助于阐明它的适用性：

```py
scaled_spend = np.linspace(start=0.0, stop=1.0, num=100)

saturated_spend_1 = logistic_saturation(x=scaled_spend, lam=1).eval()
saturated_spend_2 = logistic_saturation(x=scaled_spend, lam=2).eval()
saturated_spend_4 = logistic_saturation(x=scaled_spend, lam=4).eval()
saturated_spend_8 = logistic_saturation(x=scaled_spend, lam=8).eval()

plt.figure(figsize=(8, 6))
sns.lineplot(x=scaled_spend, y=saturated_spend_1, label="1")
sns.lineplot(x=scaled_spend, y=saturated_spend_2, label="2")
sns.lineplot(x=scaled_spend, y=saturated_spend_4, label="4")
sns.lineplot(x=scaled_spend, y=saturated_spend_8, label="8")

plt.title('Logistic Saturation')
plt.xlabel('Scaled Marketing Spend')
plt.ylabel('Saturated Marketing Spend')
plt.legend(title='Lambda')
plt.grid(True)
plt.show()
```

![](../Images/5609476231ba26040181014d24f8e204.png)

用户生成的图像

+   lamda 值为 1 时，关系保持线性。

+   随着 lamda 值的增加，饱和曲线的陡峭度增加。

+   从图表中我们希望能够达成一致意见，即当 lamda 值为 8 时，似乎不太可能出现比我们看到的更陡峭的饱和曲线。

## 1.4.3 饱和 beta

饱和 beta 对应于营销渠道系数，用于衡量营销支出的影响。

使用半正态先验，它强制要求正值，这是一个非常合理的假设，例如营销不应该产生负面效果。当 sigma 设置为 2 时，它倾向于低值。这有助于正则化系数，除非数据中有强烈的证据表明某个特定渠道有显著的影响，否则它会将系数拉向较低的值。

```py
sigma = 2

x3 = np.linspace(0, 10, 1000)
y3 = halfnorm.pdf(x3, scale=sigma)

plt.figure(figsize=(8, 6))
plt.plot(x3, y3, 'b-')
plt.fill_between(x3, y3, alpha=0.2, color='blue')
plt.title('Saturation beta prior: HalfNormal Distribution (sigma=2)')
plt.xlabel('Saturation beta')
plt.ylabel('Probability Density')
plt.grid(True)
plt.show()
```

![](../Images/eccd7a85e2d0c231a137f92cad24fc2c.png)

用户生成的图像

请记住，营销和目标变量都已标准化（范围从 0 到 1），因此 beta 先验必须处于这个标准化空间中。

## 1.4.4 Gamma 控制

gamma 控制参数是控制变量的系数，用于考虑外部因素，如宏观经济条件、假期或其他非营销变量。

使用正态分布，它允许正负效应同时存在：

```py
mu = 0
sigma = 2

x = np.linspace(mu - 4*sigma, mu + 4*sigma, 100)
y = norm.pdf(x, mu, sigma)

plt.figure(figsize=(8, 5))
plt.plot(x, y, color='blue')
plt.fill_between(x, y, color='blue', alpha=0.3)
plt.title('Control: Normal distribution (mu=0, sigma=2)')
plt.xlabel('Control value')
plt.ylabel('Probability density')
plt.grid(True)
plt.show()
```

![](../Images/707d7b29c3f5727586082ecd0a4dfb96.png)

用户生成的图像

控制变量已标准化，值范围从 -1 到 1，因此 gamma 控制先验必须处于这个标准化空间中。

# 2.0 Python 操作指南

现在我们已经涵盖了一些理论，让我们将其中的一些应用到实践中！在这个操作指南中，我们将介绍：

+   模拟数据

+   训练模型

+   验证模型

+   参数恢复

目标是创建一些现实的训练数据，在其中我们自己设置参数（如广告效应、饱和度、beta 等），使用 pymc-marketing 包训练并验证模型，然后评估我们的模型在恢复参数方面的表现。

当涉及到真实世界的 MMM 数据时，你不会知道参数，但这个参数恢复的练习是学习并在 MMM 中建立信心的好方法。

## 2.1 模拟数据

让我们开始模拟一些数据来训练模型。这种方法的好处是我们可以自己设置参数，这使我们能够进行参数恢复练习，以测试我们的模型表现如何！

以下函数可用于模拟具有以下特征的一些数据：

1.  具有一定增长的趋势成分。

1.  一个带有围绕 0 振荡的季节性成分。

1.  趋势和季节性成分用于创建需求。

1.  创建一个需求代理变量，它将可供模型使用（需求将是一个未观察到的混杂因素）。

1.  需求是销售的关键驱动因素。

1.  每个营销渠道也都会对销售产生贡献，营销支出与需求相关，且适当的转换方法已应用（缩放、广告库存、饱和度）。

```py
import pandas as pd
import numpy as np
from pymc_marketing.mmm.transformers import geometric_adstock, logistic_saturation
from sklearn.preprocessing import MaxAbsScaler

def data_generator(start_date, periods, channels, spend_scalar, adstock_alphas, saturation_lamdas, betas, freq="W"):
    '''
    Generates a synthetic dataset for a MMM with trend, seasonality, and channel-specific contributions.

    Args:
        start_date (str or pd.Timestamp): The start date for the generated time series data.
        periods (int): The number of time periods (e.g., days, weeks) to generate data for.
        channels (list of str): A list of channel names for which the model will generate spend and sales data.
        spend_scalar (list of float): Scalars that adjust the raw spend for each channel to a desired scale.
        adstock_alphas (list of float): The adstock decay factors for each channel, determining how much past spend influences the current period.
        saturation_lamdas (list of float): Lambda values for the logistic saturation function, controlling the saturation effect on each channel.
        betas (list of float): The coefficients for each channel, representing the contribution of each channel's impact on sales.

    Returns:
        pd.DataFrame: A DataFrame containing the generated time series data, including demand, sales, and channel-specific metrics.
    '''

    # 0\. Create time dimension
    date_range = pd.date_range(start=start_date, periods=periods, freq=freq)
    df = pd.DataFrame({'date': date_range})

    # 1\. Add trend component with some growth
    df["trend"]= (np.linspace(start=0.0, stop=20, num=periods) + 5) ** (1 / 8) - 1

    # 2\. Add seasonal component with oscillation around 0
    df["seasonality"] = df["seasonality"] = 0.1 * np.sin(2 * np.pi * df.index / 52)

    # 3\. Multiply trend and seasonality to create overall demand with noise
    df["demand"] = df["trend"] * (1 + df["seasonality"]) + np.random.normal(loc=0, scale=0.10, size=periods)
    df["demand"] = df["demand"] * 1000

    # 4\. Create proxy for demand, which is able to follow demand but has some noise added
    df["demand_proxy"] = np.abs(df["demand"]* np.random.normal(loc=1, scale=0.10, size=periods))

    # 5\. Initialize sales based on demand
    df["sales"] = df["demand"]

    # 6\. Loop through each channel and add channel-specific contribution
    for i, channel in enumerate(channels):

        # Create raw channel spend, following demand with some random noise added
        df[f"{channel}_spend_raw"] = df["demand"] * spend_scalar[i]
        df[f"{channel}_spend_raw"] = np.abs(df[f"{channel}_spend_raw"] * np.random.normal(loc=1, scale=0.30, size=periods))

        # Scale channel spend
        channel_transformer = MaxAbsScaler().fit(df[f"{channel}_spend_raw"].values.reshape(-1, 1))
        df[f"{channel}_spend"] = channel_transformer .transform(df[f"{channel}_spend_raw"].values.reshape(-1, 1))

        # Apply adstock transformation
        df[f"{channel}_adstock"] = geometric_adstock(
            x=df[f"{channel}_spend"].to_numpy(),
            alpha=adstock_alphas[i],
            l_max=8, normalize=True
        ).eval().flatten()

        # Apply saturation transformation
        df[f"{channel}_saturated"] = logistic_saturation(
            x=df[f"{channel}_adstock"].to_numpy(),
            lam=saturation_lamdas[i]
        ).eval()

        # Calculate contribution to sales
        df[f"{channel}_sales"] = df[f"{channel}_saturated"] * betas[i]

        # Add the channel-specific contribution to sales
        df["sales"] += df[f"{channel}_sales"]

    return df
```

现在，我们可以使用一些现实的参数调用数据生成函数：

+   3年的每周数据。

+   来自营销漏斗不同部分的3个渠道。

+   每个渠道有不同的广告库存、饱和度和beta参数。

```py
np.random.seed(10)

# Set parameters for data generator
start_date = "2021-01-01"
periods = 52 * 3
channels = ["tv", "social", "search"]
adstock_alphas = [0.50, 0.25, 0.05]
saturation_lamdas = [1.5, 2.5, 3.5]
betas = [350, 150, 50]
spend_scalars = [10, 15, 20]

df = dg.data_generator(start_date, periods, channels, spend_scalars, adstock_alphas, saturation_lamdas, betas)

# Scale betas using maximum sales value - this is so it is comparable to the fitted beta from pymc (pymc does feature and target scaling using MaxAbsScaler from sklearn)
betas_scaled = [
    ((df["tv_sales"] / df["sales"].max()) / df["tv_saturated"]).mean(),
    ((df["social_sales"] / df["sales"].max()) / df["social_saturated"]).mean(),
    ((df["search_sales"] / df["sales"].max()) / df["search_saturated"]).mean()
]

# Calculate contributions - these will be used later on to see how accurate the contributions from our model are
contributions = np.asarray([
    round((df["tv_sales"].sum() / df["sales"].sum()), 2),
    round((df["social_sales"].sum() / df["sales"].sum()), 2),
    round((df["search_sales"].sum() / df["sales"].sum()), 2),
    round((df["demand"].sum() / df["sales"].sum()), 2)
])

df[["date", "demand", "demand_proxy", "tv_spend_raw", "social_spend_raw", "search_spend_raw", "sales"]]
```

![](../Images/7ca303d078ee53b448bb1ee9b57a01a5.png)

用户生成的图像

在我们开始训练模型之前，让我们花些时间理解我们所生成的数据。

+   我们推导需求的直觉是，随着每年高需求和低需求时期的加入，天然增长的趋势逐渐上升。

```py
plt.figure(figsize=(8, 5))

sns.lineplot(x=df['date'], y=df['trend']*1000, label="Trend", color="green")
sns.lineplot(x=df['date'], y=df['seasonality']*1000, label="Seasonality", color="orange")
sns.lineplot(x=df['date'], y=df['demand'], label="Demand", color="blue")

plt.title('Components', fontsize=16)
plt.xlabel('Date', fontsize=12)
plt.ylabel('Value', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.legend()
plt.show()
```

![](../Images/18d3746085c06c6c3d5c76a0b81d6cfc.png)

用户生成的图像

+   我们为需求创建了一个代理变量，用作模型中的控制变量。这里的想法是，在现实中需求是一个不可观察的混杂因素，但有时我们可以找到需求的代理变量。一个例子是你所销售产品的谷歌搜索趋势数据。

```py
plt.figure(figsize=(8, 5))

sns.scatterplot(x=df['demand_proxy'], y=df['demand'], color="blue")

plt.title('Demand proxy vs demand', fontsize=16)
plt.xlabel('Demand proxy', fontsize=12)
plt.ylabel('Demand', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.show()
```

![](../Images/bb3c5867096a652e29c78489641372fc.png)

用户生成的图像

+   各个营销渠道的花费趋势相同，但我们加入的随机噪声应能帮助模型分析每个渠道对销售的贡献。

```py
plt.figure(figsize=(8, 5))

sns.lineplot(x=df['date'], y=df['tv_spend_raw'], label=channels[0], color="orange")
sns.lineplot(x=df['date'], y=df['social_spend_raw'], label=channels[1], color="blue")
sns.lineplot(x=df['date'], y=df['search_spend_raw'], label=channels[2], color="green")
plt.title('Marketing Channel Spend', fontsize=16)
plt.xlabel('Date', fontsize=12)
plt.ylabel('Value', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.legend()
plt.show()
```

![](../Images/2d95c270ebfb611c102827cb886fa0c2.png)

用户生成的图像

+   我们对营销数据应用了两种转换方法：广告库存（adstock）和饱和度。下面我们可以观察不同参数对各渠道饱和度的影响，其中搜索的饱和度变化最陡，而电视几乎呈线性变化。

```py
plt.figure(figsize=(8, 5))

sns.lineplot(x=df['tv_adstock'], y=df['tv_saturated'], label=channels[0], color="orange")
sns.lineplot(x=df['social_adstock'], y=df['social_saturated'], label=channels[1], color="blue")
sns.lineplot(x=df['search_adstock'], y=df['search_saturated'], label=channels[2], color="green")

plt.title('Marketing Spend Saturation', fontsize=16)
plt.xlabel('Adstocked spend', fontsize=12)
plt.ylabel('Saturated spend', fontsize=12)
plt.legend()
plt.show()
```

![](../Images/f29c738a4a8ac910bf98e1128814e3d0.png)

用户生成的图像

+   下面我们可以看到，我们的变量高度相关，这在MMM数据中非常常见，因为营销团队在高峰期花费更多。

```py
plt.figure(figsize=(8, 8))
sns.heatmap(df[["demand", "demand_proxy", "tv_spend_raw", "social_spend_raw", "search_spend_raw", "sales"]].corr(), annot=True, cmap='coolwarm', vmin=-1, vmax=1)
plt.title('Correlation Heatmap')
plt.show()
```

![](../Images/51b7a970f682018e63613dfd3777be03.png)

用户生成的图像

+   最后，让我们看看销售情况 —— 记住我们的目标是理解营销如何促成销售。

```py
plt.figure(figsize=(8, 5))

sns.lineplot(x=df['date'], y=df['sales'], label="sales", color="green")

plt.title('Sales', fontsize=16)
plt.xlabel('Date', fontsize=12)
plt.ylabel('Value', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.legend()
plt.show()
```

![](../Images/5279c48b359969074e3dd862faba5bbf.png)

用户生成的图像

现在我们已经对数据生成过程有了较好的理解，让我们深入到模型训练中吧！

## 2.2 训练模型

现在是时候开始训练模型了。首先，我们需要通过以下步骤准备训练数据：

+   将数据拆分为特征和目标。

+   创建训练集和超时切片的指标 —— 超时切片将帮助我们验证模型。

```py
# set date column
date_col = "date"

# set outcome column
y_col = "sales"

# set marketing variables
channel_cols = ["tv_spend_raw",
                "social_spend_raw",
                "search_spend_raw"]

# set control variables
control_cols = ["demand_proxy"]

# split data into features and target
X = df[[date_col] + channel_cols + control_cols]
y = df[y_col]

# set test (out-of-sample) length
test_len = 8

# create train and test indices
train_idx = slice(0, len(df) - test_len)
out_of_time_idx = slice(len(df) - test_len, len(df))
```

接下来我们初始化MMM类。MMM中的一个主要挑战是参数与训练观察值的比率很高。我们可以通过对转换方式进行务实选择来缓解这一问题：

+   我们选择了几何广告库存（adstock），它每个渠道有1个参数，而与使用威布尔广告库存相比，后者有2个参数。

+   我们选择了逻辑饱和度，它每个渠道有1个参数，而与使用Hill饱和度相比，后者有2个参数。

+   我们使用需求的代理，并决定不包括季节性成分或时间变化趋势，这进一步减少了我们的参数数量。

+   我们决定不包括时间变化的媒体参数，因为尽管确实存在性能随时间变化的情况，但我们最终希望通过响应曲线来帮助我们优化预算，而对训练数据集进行平均性能处理是一种不错的方法。

总结一下我的观点，模型的复杂度越高，我们越是操控营销支出变量以适应数据，从而给我们一个“更好的模型”（例如，较高的 R 方和较低的均方误差）。但这也有可能偏离真实的数据生成过程，从而导致我们得到偏倚的因果效应估计。

```py
mmm_default = MMM(
    adstock=GeometricAdstock(l_max=8),
    saturation=LogisticSaturation(),
    date_column=date_col,
    channel_columns=channel_cols,
    control_columns=control_cols,
)

mmm_default.default_model_config
```

![](../Images/9400cb34fdda8bf521e61419a8d59c17.png)

用户生成的图像

现在让我们使用训练集索引来拟合模型。我传递了一些可选的关键字参数，花点时间理解它们的作用：

+   **Tune** — 这是为采样器设置的调优步骤数。在调优过程中，采样器会调整其参数，以有效地探索后验分布。这些初始的 1,000 个样本会被丢弃，并且不用于推断。

+   **Chains** — 这是指定运行的独立马尔可夫链的数量。运行多条链有助于评估收敛性，并提供更稳健的后验抽样。

+   **Draws** — 这是设置每条链从后验分布中抽取的样本数（调优后）。

+   **Target accept** — 这是采样器的目标接受率。它有助于平衡参数空间的探索与效率。

```py
fit_kwargs = {
    "tune": 1_000,
    "chains": 4,
    "draws": 1_000,
    "target_accept": 0.9,
}

mmm_default.fit(X[train_idx], y[train_idx], **fit_kwargs)
```

我建议在这里使用默认设置，只有在后续步骤中遇到发散问题时才考虑更改。

## 2.3 验证模型

在拟合模型后，第一步是检查发散情况。发散表明模型或采样过程可能存在问题。虽然由于其复杂性，深入分析发散问题超出了本文的范围，但必须注意它们在模型验证中的重要性。

下面，我们检查模型中的发散数量。0 次发散表示一个良好的开始。

```py
mmm_default.idata["sample_stats"]["diverging"].sum().item()
```

![](../Images/777b7fb5560a8528aeecf40c563bc8a9.png)

用户生成的图像

接下来我们可以对 MCMC 抽样结果进行全面总结。让我们聚焦于几个关键点：

+   **mean** — 所有样本中参数的平均值。

+   **hdi_3% 和 hdi_97%** — 94% 最高密度区间（HDI）的下限和上限。94% HDI 表示基于观测数据和先验信息，参数的真实值有 94% 的概率落在该区间内。

+   **rhat** — Gelman-Rubin 统计量，用于衡量链的收敛性。接近 1（通常 < 1.05）的值表示良好的收敛性。

我们的R-hat值都非常接近1，这在没有发散的情况下是可以预期的。在下一节进行参数恢复练习时，我们将重新审视均值参数值。

```py
az.summary(
    data=mmm_default.fit_result,
    var_names=[
        "intercept",
        "y_sigma",
        "saturation_beta",
        "saturation_lam",
        "adstock_alpha",
        "gamma_control",
    ],
)
```

![](../Images/a240798d58cfb3beced35bc3f44392ea.png)

用户生成的图像

接下来，我们生成诊断图，这对评估MCMC采样质量至关重要：

+   **后验分布（左）：** 显示每个参数在MCMC采样过程中的值。理想情况下，这些分布应平滑且单峰，所有链条应显示出相似的分布。

+   **轨迹图（右）：** 显示每个参数在MCMC采样过程中的值。任何趋势或缓慢漂移可能表明混合不良或非收敛，而未重叠的链条可能表明它们陷入了后验分布的不同模式。

从我们的诊断图看，没有出现明显的警告信号。

```py
_ = az.plot_trace(
    data=mmm_default.fit_result,
    var_names=[
        "intercept",
        "y_sigma",
        "saturation_beta",
        "saturation_lam",
        "adstock_alpha",
        "gamma_control",
    ],
    compact=True,
    backend_kwargs={"figsize": (12, 10), "layout": "constrained"},
)
plt.gcf().suptitle("Model Trace", fontsize=16);
```

![](../Images/562fd3b92302d6301106a3300701dbd8.png)

用户生成的图像

对于每个参数，我们都有一个可能值的分布，反映了参数估计的不确定性。对于下一组诊断，我们首先需要从后验分布中采样。这将使我们能够进行预测，我们将对训练数据进行预测。

```py
mmm_default.sample_posterior_predictive(X[train_idx], extend_idata=True, combined=True)
```

我们可以通过直观评估观察数据是否落在预测范围内来开始后验预测检验诊断。看起来我们的模型表现良好。

```py
mmm_default.plot_posterior_predictive(original_scale=True);
```

![](../Images/943e1bad64df9f23b6c48ad2687d8052.png)

用户生成的图像

接下来，我们可以计算后验预测均值和实际数据之间的残差。我们可以绘制这些残差随时间变化的图形，以检查是否有模式或自相关性。看起来残差围绕0波动，正如预期的那样。

```py
mmm_default.plot_errors(original_scale=True);
```

![](../Images/69861606a8e25d688f660ac44ea6b4b6.png)

用户生成的图像

我们还可以检查残差是否围绕0正态分布。再次，我们通过了这个诊断测试。

```py
errors = mmm_default.get_errors(original_scale=True)

fig, ax = plt.subplots(figsize=(8, 6))
az.plot_dist(
    errors, quantiles=[0.25, 0.5, 0.75], color="C3", fill_kwargs={"alpha": 0.7}, ax=ax
)
ax.axvline(x=0, color="black", linestyle="--", linewidth=1, label="zero")
ax.legend()
ax.set(title="Errors Posterior Distribution");
```

![](../Images/68f34d1d28ffb345180286225eee8eba.png)

用户生成的图像

最后，评估我们的模型在训练样本之外的预测能力是一个良好的实践。首先，我们需要再次从后验分布中采样，但这次使用的是超出时间的数据。然后我们可以将观察到的销售额与预测的销售额进行比较。

在下图中，我们可以看到观察到的销售额大致落在区间内，模型似乎做得很好。

```py
y_out_of_sample = mmm_default.sample_posterior_predictive(
    X_pred=X[out_of_time_idx], extend_idata=False, include_last_observations=True
)

def plot_in_sample(X, y, ax, n_points: int = 15):
    (
        y.to_frame()
        .set_index(X[date_col])
        .iloc[-n_points:]
        .plot(ax=ax, marker="o", color="black", label="actuals")
    )
    return ax

def plot_out_of_sample(X_out_of_sample, y_out_of_sample, ax, color, label):
    y_out_of_sample_groupby = y_out_of_sample["y"].to_series().groupby("date")

    lower, upper = quantiles = [0.025, 0.975]
    conf = y_out_of_sample_groupby.quantile(quantiles).unstack()
    ax.fill_between(
        X_out_of_sample[date_col].dt.to_pydatetime(),
        conf[lower],
        conf[upper],
        alpha=0.25,
        color=color,
        label=f"{label} interval",
    )

    mean = y_out_of_sample_groupby.mean()
    mean.plot(ax=ax, marker="o", label=label, color=color, linestyle="--")
    ax.set(ylabel="Original Target Scale", title="Out of sample predictions for MMM")
    return ax

_, ax = plt.subplots()
plot_in_sample(X, y, ax=ax, n_points=len(X[out_of_time_idx])*3)
plot_out_of_sample(
    X[out_of_time_idx], y_out_of_sample, ax=ax, label="out of sample", color="C0"
)
ax.legend(loc="upper left");
```

![](../Images/d38d396327df862fae164b3befa5a31a.png)

用户生成的图像

根据我们在本节中的发现，我们相信我们有一个稳健的模型。在下一节中，让我们进行一个参数恢复练习，看看我们接近真实参数的程度。

## 2.4 参数恢复

在上一节中，我们按照实际场景验证了模型。在本节中，我们将进行参数恢复练习：记住，我们之所以能够做到这一点，是因为我们自己模拟了数据并存储了真实参数。

## 2.4.1 参数恢复 — 广告库存效应（Adstock）

让我们从将广告库存参数的后验分布与我们之前存储在adstock_alphas列表中的真实值进行比较开始。模型表现得相当不错，达到了正确的排名顺序，并且真实值始终位于后验分布范围内。

```py
fig = mmm_default.plot_channel_parameter(param_name="adstock_alpha", figsize=(9, 5))
ax = fig.axes[0]
ax.axvline(x=adstock_alphas[0], color="C0", linestyle="--", label=r"$\alpha_1$")
ax.axvline(x=adstock_alphas[1], color="C1", linestyle="--", label=r"$\alpha_2$")
ax.axvline(x=adstock_alphas[2], color="C2", linestyle="--", label=r"$\alpha_3$")
ax.legend(loc="upper right");
```

![](../Images/892a862a32784f26e6da5ec8f9b39a90.png)

用户生成的图片

## 2.4.2 参数恢复 — 饱和度

当我们检查饱和度时，模型在恢复电视的lambda方面表现非常好，但在恢复社交和搜索的真实值时表现较差，尽管结果并不灾难性。

```py
fig = mmm_default.plot_channel_parameter(param_name="saturation_lam", figsize=(9, 5))
ax = fig.axes[0]
ax.axvline(x=saturation_lamdas[0], color="C0", linestyle="--", label=r"$\lambda_1$")
ax.axvline(x=saturation_lamdas[1], color="C1", linestyle="--", label=r"$\lambda_2$")
ax.axvline(x=saturation_lamdas[2], color="C2", linestyle="--", label=r"$\lambda_3$")
ax.legend(loc="upper right");
```

![](../Images/418eee6dc457947cc8bcc7aae7732297.png)

用户生成的图片

## 2.4.3 参数恢复 — 渠道贝塔

关于渠道贝塔参数，模型达到了正确的排名顺序，但它高估了所有渠道的值。在下一节中，我们将以贡献为单位量化这一点。

```py
fig = mmm_default.plot_channel_parameter(param_name="saturation_beta", figsize=(9, 5))
ax = fig.axes[0]
ax.axvline(x=betas_scaled[0], color="C0", linestyle="--", label=r"$\beta_1$")
ax.axvline(x=betas_scaled[1], color="C1", linestyle="--", label=r"$\beta_2$")
ax.axvline(x=betas_scaled[2], color="C2", linestyle="--", label=r"$\beta_3$")
ax.legend(loc="upper right");
```

![](../Images/5e5d4ae64fa29861fd4c5733a829deaa.png)

用户生成的图片

## 2.4.4 参数恢复 — 渠道贡献

首先，我们计算每个渠道的真实贡献。我们还将计算需求的贡献：请记住，我们包含的是需求的代理，而不是需求本身。

```py
channels = np.array(["tv", "social", "search", "demand"])

true_contributions = pd.DataFrame({'Channels': channels, 'Contributions': contributions})
true_contributions= true_contributions.sort_values(by='Contributions', ascending=False).reset_index(drop=True)
true_contributions = true_contributions.style.bar(subset=['Contributions'], color='lightblue')

true_contributions
```

![](../Images/036cd2b56ddb8b1aa652170a03e81b41.png)

用户生成的图片

现在，让我们绘制模型的贡献。需求、电视、社交和搜索的排名顺序是正确的。然而，电视、社交和搜索的贡献都被高估了。这似乎是由于需求代理的贡献没有真实需求那么大。

```py
mmm_default.plot_waterfall_components_decomposition(figsize=(10,6));
```

![](../Images/9222b02732fff116d22c5b9cb5e18b7d.png)

用户生成的图片

# 结束语

在2.3节中，我们验证了模型，并得出结论认为它是稳健的。然而，参数恢复实验表明，我们的模型大大高估了营销效果。这一高估是由需求这一混杂因素引起的。在现实生活中，不可能进行参数恢复实验。那么，你如何识别模型中营销贡献的偏差？一旦识别出来，你又会如何解决这个问题呢？这引出了我们下一篇关于模型校准的文章！

希望你喜欢这第一篇文章！如果你想继续走向掌握MMM的道路，记得关注我——在下一篇文章中，我们将把焦点转向使用实验的有用先验进行模型校准。
