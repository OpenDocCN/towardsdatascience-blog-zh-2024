# 验证合成控制方法的因果影响

> 原文：[https://towardsdatascience.com/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=collection_archive---------6-----------------------#2024-06-08](https://towardsdatascience.com/validating-the-causal-impact-of-the-synthetic-control-method-2f3bf185f266?source=collection_archive---------6-----------------------#2024-06-08)

## 因果AI，探索因果推理与机器学习的结合

[](https://medium.com/@raz1470?source=post_page---byline--2f3bf185f266--------------------------------)[![Ryan O'Sullivan](../Images/7cd161d38d67d2c0b7da2d8f3e7d33fe.png)](https://medium.com/@raz1470?source=post_page---byline--2f3bf185f266--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2f3bf185f266--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2f3bf185f266--------------------------------) [Ryan O'Sullivan](https://medium.com/@raz1470?source=post_page---byline--2f3bf185f266--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2f3bf185f266--------------------------------) ·10分钟阅读·2024年6月8日

--

![](../Images/242fd31202ca191c89090516f0165385.png)

图片由[Irina Inga](https://unsplash.com/@irinainga05?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 这系列文章是关于什么的？

欢迎来到我的因果AI系列，在这里我们将探讨因果推理如何与机器学习模型集成。预计会探索多个在不同商业环境中的实际应用。

在上一篇文章中，我们讨论了*测量您的营销活动的内在因果影响*。在本文中，我们将继续探讨*验证合成控制的因果影响*。

如果你错过了上一篇关于内在因果影响的文章，可以在这里查看：

[](/measuring-the-intrinsic-causal-influence-of-your-marketing-campaigns-aa8354c26b7b?source=post_page-----2f3bf185f266--------------------------------) [## 测量您的营销活动的内在因果影响

### 因果AI，探索因果推理与机器学习的结合

towardsdatascience.com](/measuring-the-intrinsic-causal-influence-of-your-marketing-campaigns-aa8354c26b7b?source=post_page-----2f3bf185f266--------------------------------)

# 引言

在本文中，我们将重点了解合成控制方法，并探索我们如何验证估计的因果影响。

**以下方面将被涵盖：**

+   什么是合成控制方法？

+   它试图克服什么挑战？

+   我们如何验证估计的因果影响？

+   一个使用真实的 Google 趋势数据的 Python 案例研究，演示了我们如何验证合成控制的估计因果影响。

完整的笔记本可以在这里找到：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/synthetic%20controls%20-%20model%20training.ipynb?source=post_page-----2f3bf185f266--------------------------------) [## causal_ai/notebooks/synthetic controls - model training.ipynb 在 main 分支 · raz1470/causal_ai

### 本项目介绍了因果 AI 及其如何驱动商业价值。 - causal_ai/notebooks/synthetic controls - model…

github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/synthetic%20controls%20-%20model%20training.ipynb?source=post_page-----2f3bf185f266--------------------------------)

# 合成控制方法

## 它是什么？

合成控制方法是一种因果技术，可以在无法进行随机对照试验（RCT）或 A/B 测试时，评估干预或治疗的因果影响。该方法最早由 Abadie 和 Gardezabal 于 2003 年提出。以下论文包含了一个很好的案例研究，帮助你理解该方法：

[https://web.stanford.edu/~jhain/Paper/JASA2010.pdf](https://web.stanford.edu/~jhain/Paper/JASA2010.pdf)

![](../Images/4876ac9837822ae299d7afa75560fe05.png)

用户生成的图像

让我们自己来了解一些基础知识… 合成控制方法通过创建未接受干预或治疗的控制单位的加权组合，来创建处理单位的反事实版本。

+   **处理单位：** 接受干预的单位。

+   **控制单位：** 一组没有接受干预的相似单位。

+   **反事实：** 通过控制单位的加权组合创建。目标是为每个控制单位找到权重，使得反事实在干预前期与处理单位非常接近。

+   **因果影响：** 后期干预处理单位与反事实之间的差异。

如果我们想要简化问题，可以将其视为线性回归，其中每个控制单位是一个特征，处理单位是目标。干预前期是我们的训练集，我们使用模型来评分干预后期。实际与预测之间的差异就是因果影响。

以下是几个例子，帮助你理解何时可以考虑使用此方法：

+   在进行电视营销活动时，我们无法随机将观众分配到可以和不能看到活动的组别。然而，我们可以谨慎地选择一个地区进行试点，并使用其余地区作为控制单位。一旦我们测量了效果，活动可以推广到其他地区。这通常被称为地理提升测试（geo-lift test）。

+   政策变更仅在某些地区实施，而在其他地区不实施——例如，当地政府可能会实施一项政策以减少失业。在没有实施该政策的其他地区，可以用作控制单元。

## 它试图克服什么挑战？

当我们将高维度（大量特征）与有限的观测数据结合时，我们可能会得到一个过拟合的模型。

让我们以地理提升的例子来说明。如果我们使用过去一年的周数据作为干预前期，这将给我们提供 52 个观测值。如果我们决定在欧洲的多个国家进行干预测试，那么观测与特征的比例将是 1:1！

之前我们谈到了如何使用线性回归来实现合成控制方法。然而，由于观测与特征的比例，线性回归很可能会过拟合，从而导致干预后期的因果影响估算不准确。

在线性回归中，每个特征（控制单元）的权重（系数）可能为负或正，并且它们的总和可能大于 1。然而，合成控制方法在学习权重时会施加以下约束：

+   限制权重总和为 1

+   限制权重 ≥ 0

![](../Images/ab8a2f8a1d158f245bef617fcefd96b5.png)

用户生成的图像

这些约束有助于正则化，避免超出观测数据范围的外推。

值得注意的是，在正则化方面，岭回归和套索回归可以实现这一点，并且在某些情况下是合理的替代方案。但我们将在案例研究中进行测试！

## 我们如何验证估算的因果影响？

一个更大的挑战是我们无法验证干预后期的估算因果影响。

我的干预前期应持续多久？我们能确定没有对干预前期进行过拟合吗？我们如何知道我们的模型在干预后期是否能够很好地泛化？如果我想尝试合成控制方法的不同实现方式怎么办？

我们可以从干预前期随机选择一些观测值并将其保留用于验证——但我们已经指出，有限的观测值会带来挑战，这可能会使问题变得更糟！

如果我们可以进行某种干预前的模拟呢？这能帮助我们回答上述一些问题，并增加我们对模型估算因果影响的信心吗？所有这些将在案例研究中进行解释！

# 案例研究

## 背景

在说服财务部门品牌营销能够带来实质性价值后，市场营销团队找到你询问关于地理提升测试的问题。有人从 Facebook 告诉他们这是下一个大趋势（尽管也是同一个人告诉他们 Prophet 是一个很好的预测模型），他们想知道是否可以用它来衡量即将上线的新电视广告活动。

你有些担心，因为上次你进行 geo-lift 测试时，市场分析团队认为调整干预前期直到得到一个很大的因果影响是个好主意。

这次，你建议他们在测试开始前进行一次“干预前模拟”，之后提出在测试开始前先确定干预前期。

那么让我们来看看“干预前模拟”是什么样子的！

## 创建数据

为了使这一过程尽可能逼真，我提取了大部分欧洲国家的谷歌趋势数据。搜索的具体词语并不重要，只需要假装它是你公司（并且你在欧洲运营）的销售数据。

然而，如果你对我是如何获得谷歌趋势数据感兴趣，可以查看我的笔记本：

[](https://github.com/raz1470/causal_ai/blob/main/notebooks/synthetic%20controls%20-%20data%20collection.ipynb?source=post_page-----2f3bf185f266--------------------------------) [## causal_ai/notebooks/synthetic controls - 数据收集.ipynb 主页 · raz1470/causal_ai

### 这个项目介绍了因果 AI 以及它如何推动商业价值。 - causal_ai/notebooks/synthetic controls - 数据…

github.com](https://github.com/raz1470/causal_ai/blob/main/notebooks/synthetic%20controls%20-%20data%20collection.ipynb?source=post_page-----2f3bf185f266--------------------------------)

下面我们可以看到数据框架。我们有过去 3 年中 50 个欧洲国家的销售数据。市场团队计划在英国开展他们的电视广告活动。

![](../Images/38a31d9ec200cba9a45b6c09bc9347b3.png)

用户生成的图像

现在进入精彩的部分。我们将在时间序列的最后 7 周模拟一次干预。

```py
np.random.seed(1234)

# Create intervention flag
mask = (df['date'] >= "2024-04-14") & (df['date'] <= "2024-06-02")
df['intervention'] = mask.astype(int)

row_count = len(df)

# Create intervention uplift
df['uplift_perc'] = np.random.uniform(0.10, 0.20, size=row_count)
df['uplift_abs'] = round(df['uplift_perc'] * df['GB'])
df['y'] = df['GB']
df.loc[df['intervention'] == 1, 'y'] = df['GB'] + df['uplift_abs']
```

现在让我们绘制英国实际与反事实的销售数据，让我们所做的工作更生动：

```py
def synth_plot(df, counterfactual):

    plt.figure(figsize=(14, 8))
    sns.set_style("white")

    # Create plot
    sns.lineplot(data=df, x='date', y='y', label='Actual', color='b', linewidth=2.5)
    sns.lineplot(data=df, x='date', y=counterfactual, label='Counterfactual', color='r', linestyle='--', linewidth=2.5)
    plt.title('Synthetic Control Method: Actual vs. Counterfactual', fontsize=24)
    plt.xlabel('Date', fontsize=20)
    plt.ylabel('Metric Value', fontsize=20)
    plt.legend(fontsize=16)
    plt.gca().xaxis.set_major_formatter(plt.matplotlib.dates.DateFormatter('%Y-%m-%d'))
    plt.xticks(rotation=90)
    plt.grid(True, linestyle='--', alpha=0.5)

    # High the intervention point
    intervention_date = '2024-04-07'
    plt.axvline(pd.to_datetime(intervention_date), color='k', linestyle='--', linewidth=1)
    plt.text(pd.to_datetime(intervention_date), plt.ylim()[1]*0.95, 'Intervention', color='k', fontsize=18, ha='right')

    plt.tight_layout()
    plt.show()
```

```py
synth_plot(df, 'GB')
```

![](../Images/4876ac9837822ae299d7afa75560fe05.png)

用户生成的图像

现在我们已经模拟了一个干预，我们可以探索合成控制方法的效果如何。

## 预处理

除了英国（GB）外，所有欧洲国家都被设置为对照单元（特征）。治疗单元（目标）是应用干预的英国销售数据。

```py
# Delete the original target column so we don't use it as a feature by accident
del df['GB']

# set feature & targets
X = df.columns[1:50]
y = 'y'
```

## 回归

下面我设置了一个函数，我们可以用不同的干预前期和不同的回归模型（例如 Ridge、Lasso）进行重复使用：

```py
def train_reg(df, start_index, reg_class):

    df_temp = df.iloc[start_index:].copy().reset_index()

    X_pre = df_temp[df_temp['intervention'] == 0][X]
    y_pre = df_temp[df_temp['intervention'] == 0][y]

    X_train, X_test, y_train, y_test = train_test_split(X_pre, y_pre, test_size=0.10, random_state=42)

    model = reg_class
    model.fit(X_train, y_train)

    yhat_train = model.predict(X_train)
    yhat_test = model.predict(X_test)

    mse_train = mean_squared_error(y_train, yhat_train)
    mse_test = mean_squared_error(y_test, yhat_test)
    print(f"Mean Squared Error train: {round(mse_train, 2)}")
    print(f"Mean Squared Error test: {round(mse_test, 2)}")

    r2_train = r2_score(y_train, yhat_train)
    r2_test = r2_score(y_test, yhat_test)
    print(f"R2 train: {round(r2_train, 2)}")
    print(f"R2 test: {round(r2_test, 2)}")

    df_temp['pred'] = model.predict(df_temp.loc[:, X])
    df_temp['delta'] = df_temp['y'] - df_temp['pred']

    pred_lift = df_temp[df_temp['intervention'] == 1]['delta'].sum()
    actual_lift = df_temp[df_temp['intervention'] == 1]['uplift_abs'].sum()
    abs_error_perc = abs(pred_lift - actual_lift) / actual_lift
    print(f"Predicted lift: {round(pred_lift, 2)}")
    print(f"Actual lift: {round(actual_lift, 2)}")
    print(f"Absolute error percentage: {round(abs_error_perc, 2)}")

    return df_temp, abs_error_perc
```

为了开始，我们保持简单，使用线性回归来估算因果影响，使用一个较小的干预前期：

```py
df_lin_reg_100, pred_lift_lin_reg_100 = train_reg(df, 100, LinearRegression())
```

![](../Images/e48bd5832d392f66cc7bd5be1945b135.png)

用户生成的图像

从结果来看，线性回归的表现并不理想。但鉴于特征与观测值的比例，这并不令人惊讶。

```py
synth_plot(df_lin_reg_100, 'pred')
```

![](../Images/5995e5ef2df8c9ec4a57f4df245e8be3.png)

用户生成的图像

## 合成控制方法

让我们直接进入，看看它与合成控制方法的比较。下面我设置了一个类似之前的函数，但使用 sciPy 应用了合成控制方法：

```py
def synthetic_control(weights, control_units, treated_unit):

    synthetic = np.dot(control_units.values, weights)

    return np.sqrt(np.sum((treated_unit - synthetic)**2))

def train_synth(df, start_index):

    df_temp = df.iloc[start_index:].copy().reset_index()

    X_pre = df_temp[df_temp['intervention'] == 0][X]
    y_pre = df_temp[df_temp['intervention'] == 0][y]

    X_train, X_test, y_train, y_test = train_test_split(X_pre, y_pre, test_size=0.10, random_state=42)

    initial_weights = np.ones(len(X)) / len(X)

    constraints = ({'type': 'eq', 'fun': lambda w: np.sum(w) - 1})

    bounds = [(0, 1) for _ in range(len(X))]

    result = minimize(synthetic_control, 
                     initial_weights, 
                     args=(X_train, y_train),
                     method='SLSQP', 
                     bounds=bounds, 
                     constraints=constraints,
                     options={'disp': False, 'maxiter': 1000, 'ftol': 1e-9},
    )

    optimal_weights = result.x

    yhat_train = np.dot(X_train.values, optimal_weights)
    yhat_test = np.dot(X_test.values, optimal_weights)

    mse_train = mean_squared_error(y_train, yhat_train)
    mse_test = mean_squared_error(y_test, yhat_test)
    print(f"Mean Squared Error train: {round(mse_train, 2)}")
    print(f"Mean Squared Error test: {round(mse_test, 2)}")

    r2_train = r2_score(y_train, yhat_train)
    r2_test = r2_score(y_test, yhat_test)
    print(f"R2 train: {round(r2_train, 2)}")
    print(f"R2 test: {round(r2_test, 2)}")    

    df_temp['pred'] = np.dot(df_temp.loc[:, X].values, optimal_weights)
    df_temp['delta'] = df_temp['y'] - df_temp['pred']

    pred_lift = df_temp[df_temp['intervention'] == 1]['delta'].sum()
    actual_lift = df_temp[df_temp['intervention'] == 1]['uplift_abs'].sum()
    abs_error_perc = abs(pred_lift - actual_lift) / actual_lift
    print(f"Predicted lift: {round(pred_lift, 2)}")
    print(f"Actual lift: {round(actual_lift, 2)}")
    print(f"Absolute error percentage: {round(abs_error_perc, 2)}")

    return df_temp, abs_error_perc
```

我保持干预前的时间段不变，以便与线性回归进行公平比较：

```py
df_synth_100, pred_lift_synth_100 = train_synth(df, 100)
```

![](../Images/bb483c51df63e0e5df556624775d4e4e.png)

用户生成图像

哇！我必须承认，我没想到会有这么显著的改进！

```py
synth_plot(df_synth_100, 'pred')
```

![](../Images/8593cd13b5533eec365a1c92006b5ca2.png)

用户生成图像

## 结果比较

让我们还不要过于激动。接下来，我们将进行更多实验，探索模型类型和干预前的时间段：

```py
# run regression experiments
df_lin_reg_00, pred_lift_lin_reg_00 = train_reg(df, 0, LinearRegression())
df_lin_reg_100, pred_lift_lin_reg_100 = train_reg(df, 100, LinearRegression())
df_ridge_00, pred_lift_ridge_00 = train_reg(df, 0, RidgeCV())
df_ridge_100, pred_lift_ridge_100 = train_reg(df, 100, RidgeCV())
df_lasso_00, pred_lift_lasso_00 = train_reg(df, 0, LassoCV())
df_lasso_100, pred_lift_lasso_100 = train_reg(df, 100, LassoCV())

# run synthetic control experiments
df_synth_00, pred_lift_synth_00 = train_synth(df, 0)
df_synth_100, pred_lift_synth_100 = train_synth(df, 100)

experiment_data = {
    "Method": ["Linear", "Linear", "Ridge", "Ridge", "Lasso", "Lasso", "Synthetic Control", "Synthetic Control"],
    "Data Size": ["Large", "Small", "Large", "Small", "Large",  "Small", "Large", "Small"],
    "Value": [pred_lift_lin_reg_00, pred_lift_lin_reg_100, pred_lift_ridge_00, pred_lift_ridge_100,pred_lift_lasso_00, pred_lift_lasso_100, pred_lift_synth_00, pred_lift_synth_100]
}

df_experiments = pd.DataFrame(experiment_data)
```

我们将使用下面的代码来可视化结果：

```py
# Set the style
sns.set_style="whitegrid"

# Create the bar plot
plt.figure(figsize=(10, 6))
bar_plot = sns.barplot(x="Method", y="Value", hue="Data Size", data=df_experiments, palette="muted")

# Add labels and title
plt.xlabel("Method")
plt.ylabel("Absolute error percentage")
plt.title("Synthetic Controls - Comparison of Methods Across Different Data Sizes")
plt.legend(title="Data Size")

# Show the plot
plt.show()
```

![](../Images/36d5fbbe62a6070db05e30c377ffbfe3.png)

用户生成图像

小数据集的结果非常有趣！正如预期的那样，正则化有助于提高因果影响的估计。合成控制方法则更进一步！

大数据集的结果表明，较长的干预前期不一定更好。

然而，我希望你能记住的是，进行干预前的模拟是多么有价值。你可以使用自己的数据集探索许多不同的方向！

# 结束语

今天我们探讨了合成控制方法以及如何验证因果影响。我将留下几个最后的想法：

+   合成控制方法的简单性使其成为因果AI工具箱中最广泛使用的技术之一。

+   不幸的是，这也是最常被滥用的——让我们运行R的CausalImpact包，调整干预前期，直到我们看到一个满意的提升。 😭

+   在这里，我强烈建议进行干预前的模拟，以便事先确定测试设计。

+   合成控制方法是一个被广泛研究的领域。值得查看提出的改进方法：增强型SC、稳健型SC和惩罚型SC。

# 资源

*Alberto Abadie, Alexis Diamond & Jens Hainmueller (2010) Synthetic Control Methods for Comparative Case Studies: Estimating the Effect of California’s Tobacco Control Program, Journal of the American Statistical Association, 105:490, 493–505, DOI: 10.1198/jasa.2009.ap08746*

如果你想继续探索因果AI的旅程，欢迎关注我——在下一篇文章中，我们将探讨如何利用因果AI提升市场营销组合建模。
