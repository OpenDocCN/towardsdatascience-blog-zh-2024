# 产品分析师的实用计算机模拟

> 原文：[https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-4d3a17957f64?source=collection_archive---------7-----------------------#2024-04-30](https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-4d3a17957f64?source=collection_archive---------7-----------------------#2024-04-30)

## 第二部分：使用自助法进行观测和 A/B 测试

[](https://miptgirl.medium.com/?source=post_page---byline--4d3a17957f64--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--4d3a17957f64--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4d3a17957f64--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4d3a17957f64--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--4d3a17957f64--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4d3a17957f64--------------------------------) ·21 分钟阅读·2024年4月30日

--

![](../Images/203b2849d9accb8982770f471c74a6a0.png)

图片来自 DALL-E 3

在[系列的第一部分](https://medium.com/towards-data-science/practical-computer-simulations-for-product-analysts-90b5deb6a54e)中，我们讨论了计算机模拟的基本思想，以及如何利用它们来回答“如果……会怎样”问题。在谈论模拟时，无法不提自助法。

统计学中的自助法是一种实用的计算机方法，用于估计概率分布的统计量。它基于通过蒙特卡罗方法从现有样本中反复生成样本。这种方法可以简单快捷地估计复杂模型的各种统计量（如置信区间、方差、相关性等）。

当我在统计学课程中学习自助法时，感觉有点像是黑科技。你不需要学习多个公式和不同情况下的标准，只需写几行代码，就能为任何自定义和复杂的用例获得置信区间估计。这听起来就像魔法一样。

这确实是事实。现在，甚至你的笔记本电脑都能在几分钟甚至几秒钟内运行成千上万次模拟，自助法是你分析工具包中一个强大的工具，可以帮助你在许多情况下解决问题。因此，我认为学习或刷新你对它的理解是值得的。

在本文中，我们将讨论自助法背后的思想，了解何时应该使用它，学习如何为不同的度量获取置信区间，并分析 A/B 测试的结果。

# 什么是自助法（bootstrap）？

事实上，引导法非常简单。我们需要通过有放回地从样本分布中抽取元素来进行模拟，然后基于这个分布得出结论。

让我们看一个简单的例子，假设我们有四个元素：1、2、3 和 4。然后，我们可以模拟许多其他包含4个元素的集合，每个元素可能是1、2、3 或4，且每个元素的概率相等，并使用这些模拟来了解例如均值如何变化。

![](../Images/92dd778412b7a02e18fa442f7acb1b72.png)

引导法的统计意义在于，我们认为实际总体与我们的样本具有完全相同的分布（或者总体由无数个我们的样本副本组成）。然后，我们假设我们了解总体，并利用它来理解数据中的变异性。

通常，在使用经典统计方法时，我们假设变量遵循某种已知分布（例如正态分布）。然而，在引导法（Bootstrap）中，我们不需要对分布的性质做任何假设。它非常方便，甚至可以帮助分析非常复杂的自定义指标。

几乎不可能搞错引导法估计。所以，在很多情况下，我更倾向于使用它而不是经典统计方法。唯一的缺点是计算时间。如果你在处理大数据时，模拟可能需要几个小时，而经典统计方法的估计则可以在几秒钟内完成。

然而，有些情况下，如果没有引导法，获得估计值会非常具有挑战性。让我们讨论一下引导法的最佳使用场景：

+   如果你的数据中有[异常值或有影响力的数据点](/linear-regression-models-and-influential-points-4ee844adac6d)；

+   如果你的样本相对较小（大约少于100个案例）；

+   如果你的数据分布与正态分布或其他理论分布差距较大，例如，它有多个峰值；

+   如果你在处理自定义指标（例如，在SLA内完成的案件占比或百分位数）。

引导法是一个奇妙且强大的统计概念。我们来尝试将它用于描述性统计。

# 处理观测数据

首先，让我们从观测数据开始，并使用一个合成数据集。假设我们正在帮助一家健身俱乐部设立一个新的健身计划，帮助客户为伦敦马拉松做准备。我们得到的第一组试验组包含12名客户，并测量了他们的结果。

这是我们拥有的数据。

![](../Images/ac17472b9283e4e11147d5428164d721.png)

我们为每个12名客户收集了三个字段：

+   `races_before` — 客户在我们的计划之前参加的比赛次数，

+   `kms_during_program` — 客户在我们计划中跑步的公里数，

+   `finished_marathon` — 该计划是否成功，客户是否完成了伦敦马拉松。

我们的目标是建立一个以目标为导向的公平项目，激励我们的客户更多地与我们一起训练，并取得更好的成绩。因此，我们希望在客户在准备过程中跑了至少150公里但未能完成马拉松时退还他们的费用。然而，在启动这个项目之前，我们想做一些估算：客户在准备期间跑了多远，以及预计退款的比例。我们需要这些数据来确保我们的业务盈利且可持续。

## 估计平均值

让我们从估计平均距离开始。我们可以尝试利用我们在数学统计学方面的知识，并使用置信区间的公式。

为此，我们需要对这个变量的分布做出假设。最常用的是正态分布。让我们试试。

```py
import numpy as np
from scipy.stats import norm, t

def get_normal_confidence_interval(data, confidence=0.95):
    # Calculate sample mean and standard deviation
    sample_mean = np.mean(data)
    sample_std = np.std(data, ddof=1)  
    n = len(data)

    # Calculate the critical value (z) based on the confidence level
    z = norm.ppf((1 + confidence) / 2)

    # Calculate the margin of error using standard error
    margin_of_error = z * sample_std / np.sqrt(n)

    # Calculate the confidence interval
    lower_bound = sample_mean - margin_of_error
    upper_bound = sample_mean + margin_of_error

    return lower_bound, upper_bound

get_normal_confidence_interval(df.kms_during_program.values)
# (111.86, 260.55)
```

另一种通常与真实数据一起使用的分布是t检验分布，它给出一个更宽的置信区间（因为它假设比正态分布更胖的尾部）。

```py
def get_ttest_confidence_interval(data, confidence=0.95):
    # Calculate sample mean and standard deviation
    sample_mean = np.mean(data)
    sample_std = np.std(data, ddof=1)  
    n = len(data)

    # Calculate the critical value (z) based on the confidence level
    z = t.ppf((1 + confidence) / 2, df=len(data) - 1)

    # Calculate the margin of error using standard error
    margin_of_error = z * sample_std / np.sqrt(n)

    # Calculate the confidence interval
    lower_bound = sample_mean - margin_of_error
    upper_bound = sample_mean + margin_of_error

    return lower_bound, upper_bound

get_ttest_confidence_interval(df.kms_during_program.values)
# (102.72, 269.69)
```

我们的样本中有一些例子。另外，还有一个离群值：一位拥有12场比赛经验的客户，成功跑了近600公里为马拉松做准备，而其他大多数客户的跑步距离不到200公里。

![](../Images/4f875275a3782cd77b94ddb1006ae8ca.png)

因此，这是一个使用自助法技术来更好地理解分布和置信区间的绝佳案例。

让我们创建一个函数来计算并可视化置信区间：

+   我们运行`num_batches`次模拟，进行有放回的抽样，并计算平均距离。

+   然后，基于这些变量，我们可以得到一个95%的置信区间：该分布的2.5%和97.5%百分位数。

+   最后，我们可以在图表上可视化分布。

```py
import tqdm
import matplotlib.pyplot as plt

def get_kms_confidence_interval(num_batches, confidence = 0.95):
    # Running simulations
    tmp = []
    for i in tqdm.tqdm(range(num_batches)):
        tmp_df = df.sample(df.shape[0], replace = True)
        tmp.append(
            {
                'iteration': i,
                'mean_kms': tmp_df.kms_during_program.mean()
            }
        )
    # Saving data
    bootstrap_df = pd.DataFrame(tmp)

    # Calculating confidence interval
    lower_bound = bootstrap_df.mean_kms.quantile((1 - confidence)/2)
    upper_bound = bootstrap_df.mean_kms.quantile(1 - (1 - confidence)/2)

    # Creating a chart
    ax = bootstrap_df.mean_kms.hist(bins = 50, alpha = 0.6, 
        color = 'purple')
    ax.set_title('Average kms during the program, iterations = %d' % num_batches)

    plt.axvline(x=lower_bound, color='navy', linestyle='--', 
        label='lower bound = %.2f' % lower_bound)

    plt.axvline(x=upper_bound, color='navy', linestyle='--', 
        label='upper bound = %.2f' % upper_bound)

    ax.annotate('CI lower bound: %.2f' % lower_bound, 
                xy=(lower_bound, ax.get_ylim()[1]), 
                xytext=(-10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    ax.annotate('CI upper bound: %.2f' % upper_bound, 
                xy=(upper_bound, ax.get_ylim()[1]), 
                xytext=(-10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    plt.xlim(ax.get_xlim()[0] - 20, ax.get_xlim()[1] + 20)
    plt.show()
```

让我们从少量的批次开始，以便快速查看初步结果。

```py
get_kms_confidence_interval(100)
```

![](../Images/63d618d07ccafb044e63995f57c17217.png)

使用自助法，我们得到了一个稍微窄一些并偏向右侧的置信区间，这与我们的实际分布相符：`(139.31, 297.99)` 与 `(102.72, 269.69)`。

然而，经过100次自助法模拟后，分布并不十分清晰。让我们尝试增加更多的迭代次数。我们可以看到我们的分布包含了多个模态——对于含有一个离群值、两个离群值、三个离群值等的样本。

![](../Images/9c3a6753c398f62c7e3c54bd0b3e9491.png)

随着更多迭代的进行，我们可以看到更多的模态（因为离群值的出现次数更少），但所有的置信区间都非常接近。

在自助法的情况下，增加更多的迭代次数并不会导致过拟合（因为每次迭代是独立的）。我会把它想象成是增加图像的分辨率。

由于我们的样本量较小，运行大量模拟不会花费太多时间。即使是100万次自助法迭代，也只需要大约1分钟。

## 估计自定义指标

正如我们讨论的，使用自助法在处理不像平均值那么直观的指标时非常有用。例如，你可能想要估计中位数或在SLA内完成的任务比例。

你甚至可能用bootstrap做一些更不寻常的事情。假设你想给客户提供折扣，如果你的配送延迟了：延迟15分钟给予5%的折扣，延迟1小时给予10%的折扣，延迟3小时给予20%的折扣。

理论上，使用简单的统计方法来获取这种情况下的置信区间可能会很有挑战性，因此bootstrap将非常有价值。

让我们回到我们的跑步程序，估算退款的比例（当一个客户跑了150公里却没能完成马拉松）。我们将使用类似的函数，但将计算每次迭代的退款比例，而不是均值。

```py
import tqdm
import matplotlib.pyplot as plt

def get_refund_share_confidence_interval(num_batches, confidence = 0.95):
    # Running simulations
    tmp = []
    for i in tqdm.tqdm(range(num_batches)):
        tmp_df = df.sample(df.shape[0], replace = True)
        tmp_df['refund'] = list(map(
            lambda kms, passed: 1 if (kms >= 150) and (passed == 0) else 0,
            tmp_df.kms_during_program,
            tmp_df.finished_marathon
        ))

        tmp.append(
            {
                'iteration': i,
                'refund_share': tmp_df.refund.mean()
            }
        )

    # Saving data
    bootstrap_df = pd.DataFrame(tmp)

    # Calculating confident interval
    lower_bound = bootstrap_df.refund_share.quantile((1 - confidence)/2)
    upper_bound = bootstrap_df.refund_share.quantile(1 - (1 - confidence)/2)

    # Creating a chart
    ax = bootstrap_df.refund_share.hist(bins = 50, alpha = 0.6, 
        color = 'purple')
    ax.set_title('Share of refunds, iterations = %d' % num_batches)
    plt.axvline(x=lower_bound, color='navy', linestyle='--',
        label='lower bound = %.2f' % lower_bound)
    plt.axvline(x=upper_bound, color='navy', linestyle='--', 
        label='upper bound = %.2f' % upper_bound)
    ax.annotate('CI lower bound: %.2f' % lower_bound, 
                xy=(lower_bound, ax.get_ylim()[1]), 
                xytext=(-10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    ax.annotate('CI upper bound: %.2f' % upper_bound, 
                xy=(upper_bound, ax.get_ylim()[1]), 
                xytext=(-10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    plt.xlim(-0.1, 1)
    plt.show()
```

即便只有12个样本，我们也获得了一个小于2倍的置信区间。我们可以以95%的置信度得出结论，少于42%的客户将有资格获得退款。

![](../Images/f3e12dd8de15bd4f9eb3f8ec61586b0f.png)

这是一个很好的结果，考虑到数据量如此之小。然而，我们还可以更进一步，尝试对因果效应进行估算。

## 效果估算

我们有关于这场马拉松之前的几场比赛的数据，并且可以看到这个值与预期距离之间的相关性。我们也可以使用bootstrap来处理这个问题。我们只需要在当前过程中加入线性回归步骤。

```py
def get_races_coef_confidence_interval(num_batches, confidence = 0.95):
    # Running simulations
    tmp = []
    for i in tqdm.tqdm(range(num_batches)):
        tmp_df = df.sample(df.shape[0], replace = True)
        # Linear regression model
        model = smf.ols('kms_during_program ~ races_before', data = tmp_df).fit()

        tmp.append(
            {
                'iteration': i,
                'races_coef': model.params['races_before']
            }
        )

    # Saving data
    bootstrap_df = pd.DataFrame(tmp)

    # Calculating confident interval
    lower_bound = bootstrap_df.races_coef.quantile((1 - confidence)/2)
    upper_bound = bootstrap_df.races_coef.quantile(1 - (1 - confidence)/2)

    # Creating a chart
    ax = bootstrap_df.races_coef.hist(bins = 50, alpha = 0.6, color = 'purple')
    ax.set_title('Coefficient between kms during the program and previous races, iterations = %d' % num_batches)
    plt.axvline(x=lower_bound, color='navy', linestyle='--', label='lower bound = %.2f' % lower_bound)
    plt.axvline(x=upper_bound, color='navy', linestyle='--', label='upper bound = %.2f' % upper_bound)
    ax.annotate('CI lower bound: %.2f' % lower_bound, 
                xy=(lower_bound, ax.get_ylim()[1]), 
                xytext=(-10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    ax.annotate('CI upper bound: %.2f' % upper_bound, 
                xy=(upper_bound, ax.get_ylim()[1]), 
                xytext=(10, -20), 
                textcoords='offset points',  
                ha='center', va='top',  
                color='navy', rotation=90) 
    # plt.legend() 
    plt.xlim(ax.get_xlim()[0] - 5, ax.get_xlim()[1] + 5)
    plt.show()

    return bootstrap_df
```

我们可以查看分布。由于置信区间大于0，因此我们可以以95%的置信度说存在某种效应。

![](../Images/010875e9356554ca9c7fb98b1b8bc0e2.png)

你可以发现分布是双峰的，每个峰值对应着一种情形：

+   12周围的组件与没有异常值的样本有关——它是对前几场比赛对程序中预期距离的影响的估算，如果我们忽略掉异常值的话。

+   第二个组件对应的是数据集中存在一个或多个异常值的样本。

所以，通过查看bootstrap分布，能对不同情境做出估算真的很酷。

我们已经学会了如何使用bootstrap与观测数据，但它的核心用途是A/B测试。因此，接下来让我们继续看第二个例子。

# A/B测试的模拟

bootstrap的另一个常见应用是设计和分析A/B测试。让我们看一个例子。它也将基于一个合成数据集，展示折扣对客户留存的影响。假设我们正在做一个电子杂货产品，想要测试我们推出的20欧元折扣营销活动是否会影响客户的消费。

关于每个客户，我们知道他们的居住国家、与他们同住的家庭成员数量、该国的平均年薪以及他们在我们商店里消费的金额。

![](../Images/06501e237f7f3ae792bd396a38e63d11.png)

## 功效分析

首先，我们需要设计实验，并且理解每个实验组中需要多少客户，以便我们可以有信心地得出结论。这个步骤叫做功效分析。

让我们快速回顾一下A/B测试的基本统计理论和主要指标。每个测试都基于原假设（即当前现状）。在我们的案例中，原假设是“*折扣不影响客户对我们产品的消费*”。然后，我们需要收集控制组和实验组的客户消费数据，并估算在原假设成立的情况下看到这样或更极端结果的概率。这个概率叫做p值，如果p值足够小，我们可以得出结论，认为我们有足够的数据来拒绝原假设，进而说明处理对客户的消费或留存产生了影响。

在这种方法中，有三个主要指标：

+   **效应大小**——我们希望能够检测到的度量的最小变化，

+   **统计显著性**等于假阳性率（拒绝原假设时实际没有效应的概率）。最常用的显著性水平是5%。然而，根据你的假阳性容忍度，你可能会选择其他值。例如，如果实施变更的成本较高，你可能希望使用较低的显著性阈值。

+   **统计功效**表示在实际存在与效应大小相等或更大的效应时，拒绝原假设的概率。人们通常使用80%的阈值，但在某些情况下（即你希望更有信心地排除负面效应），你可能会选择90%甚至99%。

我们需要所有这些数值来估算实验中的客户数量。让我们尝试在我们的案例中定义这些数值，以便更好地理解它们的含义。

我们将从效应大小开始：

+   我们预计通过我们的活动，客户的留存率将至少变化3个百分点，

+   我们希望能够发现客户消费变化达到20欧元或更多。

对于统计显著性，我将使用默认的5%阈值（这样，如果我们在A/B测试分析中看到效应，我们可以95%的信心确认效应存在）。我们将目标设定为90%的统计功效阈值，以便如果实际效应等于或大于效应大小，我们将在90%的情况下发现这种变化。

让我们从统计公式开始，这将帮助我们快速获得估算值。统计公式假设我们的变量具有特定的分布，但通常它们可以帮助你估算样本数量的大小。稍后，我们将使用自助法（bootstrap）获得更准确的结果。

对于留存率，我们可以使用标准的比例检验。我们需要知道实际值以估算标准化效应大小。我们可以通过实验前的历史数据获得这个值。

```py
import statsmodels.stats.power as stat_power
import statsmodels.stats.proportion as stat_prop

base_retention = before_df.retention.mean()
ret_effect_size = stat_prop.proportion_effectsize(base_retention + 0.03, 
    base_retention)

sample_size = 2*stat_power.tt_ind_solve_power(
    effect_size = ret_effect_size,
    alpha = 0.05, power = 0.9,
    nobs1 = None, # we specified nobs1 as None to get an estimation for it
    alternative='larger'
)

# ret_effect_size = 0.0632, sample_size = 8573.86
```

我们使用单边检验，因为从商业角度来看，是否存在负效应或没有效应并无区别，因为我们不会实施此变更。使用单边检验而非双边检验可以提高统计功效。

我们可以类似地估算客户价值的样本大小，假设正态分布。然而，实际分布并不是正态的，因此我们应该期待从自助法（bootstrap）中获得更精确的结果。

![](../Images/62466b93bdc08c3c004a1b41c77eda9f.png)

让我们写代码。

```py
val_effect_size = 20/before_df.customer_value.std()

sample_size = 2*stat_power.tt_ind_solve_power(
    effect_size = val_effect_size,
    alpha = 0.05, power = 0.9, 
    nobs1 = None, 
    alternative='larger'
)
# val_effect_size = 0.0527, sample_size = 12324.13
```

我们得到了每个测试所需的样本大小的估算。然而，也有一些情况是你拥有有限数量的客户，并且希望了解你可以获得的统计效能。

假设我们只有5000个客户（每组2500个）。那么，对于留存分析，我们将能够实现72.2%的统计效能，对于客户价值分析则是58.7%（假设期望的统计显著性和效应大小）。

唯一的区别是这次我们指定了`nobs1 = 2500`，并将`power`保留为`None`。

```py
stat_power.tt_ind_solve_power(
    effect_size = ret_effect_size,
    alpha = 0.05, power = None,
    nobs1 = 2500, 
    alternative='larger'
)
# 0.7223

stat_power.tt_ind_solve_power(
    effect_size = val_effect_size,
    alpha = 0.05, power = None,
    nobs1 = 2500, 
    alternative='larger'
)
# 0.5867
```

现在，是时候使用自助法进行效能分析了，我们将从客户价值测试开始，因为它更容易实现。

![](../Images/ada854ce8bf8eda594fcef32e652f4da.png)

让我们讨论一下使用自助法进行效能分析的基本思路和步骤。首先，我们需要明确我们的目标。我们希望估算统计效能与样本大小的关系。如果用更实际的术语来说，我们希望知道在客户消费增加20欧元或以上的情况下，我们能够拒绝原假设并实施这一变化的百分比。因此，我们需要模拟一系列这样的实验，并计算出在多少情况下，我们能够看到指标的统计显著变化。

让我们来看一个实验，并将其分解为步骤。第一步是生成实验数据。为此，我们需要从总体中随机抽取一个与样本大小相等的子集，随机将这些客户分配到控制组和实验组，并为处理组添加一个与效果大小相等的效应。所有这些逻辑都在下面的`get_sample_for_value`函数中实现。

```py
def get_sample_for_value(pop_df, sample_size, effect_size):
  # getting sample of needed size
  sample_df = pop_df.sample(sample_size)

  # randomly assign treatment
  sample_df['treatment'] = sample_df.index.map(
    lambda x: 1 if np.random.uniform() > 0.5 else 0)

  # add efffect for the treatment group
  sample_df['predicted_value'] = sample_df['customer_value'] \
    + effect_size * sample_df.treatment

  return sample_df
```

现在，我们可以像通常进行A/B测试分析那样处理这个合成实验数据，运行一系列自助法模拟，估算效果，然后为这个效果获取一个置信区间。

我们将使用线性回归来估算处理效果。如[上一篇文章](https://medium.com/towards-data-science/linear-regressions-for-causal-conclusions-34c6317c5a11)中讨论的那样，值得将能够解释结果变量（客户消费）的特征添加到线性回归中。我们将把家庭成员数量和平均工资加入回归中，因为它们与客户消费正相关。

```py
import statsmodels.formula.api as smf
val_model = smf.ols('customer_value ~ num_family_members + country_avg_annual_earning', 
    data = before_df).fit(disp = 0)
val_model.summary().tables[1]
```

![](../Images/ada854ce8bf8eda594fcef32e652f4da.png)

我们将把进行多个自助法模拟和估算处理效果的所有逻辑放入`get_ci_for_value`函数中。

```py
def get_ci_for_value(df, boot_iters, confidence_level):
    tmp_data = []

    for iter in range(boot_iters):
        sample_df = df.sample(df.shape[0], replace = True)
        val_model = smf.ols('predicted_value ~ treatment + num_family_members + country_avg_annual_earning', 
          data = sample_df).fit(disp = 0)
        tmp_data.append(
            {
                'iteration': iter,
                'coef': val_model.params['treatment']
            }
        )

    coef_df = pd.DataFrame(tmp_data)
    return coef_df.coef.quantile((1 - confidence_level)/2), 
        coef_df.coef.quantile(1 - (1 - confidence_level)/2)
```

下一步是将这些逻辑整合在一起，运行一系列这样的合成实验，并保存结果。

```py
def run_simulations_for_value(pop_df, sample_size, effect_size, 
    boot_iters, confidence_level, num_simulations):

    tmp_data = []

    for sim in tqdm.tqdm(range(num_simulations)):
        sample_df = get_sample_for_value(pop_df, sample_size, effect_size)
        num_users_treatment = sample_df[sample_df.treatment == 1].shape[0]
        value_treatment = sample_df[sample_df.treatment == 1].predicted_value.mean()
        num_users_control = sample_df[sample_df.treatment == 0].shape[0]
        value_control = sample_df[sample_df.treatment == 0].predicted_value.mean()

        ci_lower, ci_upper = get_ci_for_value(sample_df, boot_iters, confidence_level)

        tmp_data.append(
            {
                'experiment_id': sim,
                'num_users_treatment': num_users_treatment,
                'value_treatment': value_treatment,
                'num_users_control': num_users_control,
                'value_control': value_control,
                'sample_size': sample_size,
                'effect_size': effect_size,
                'boot_iters': boot_iters,
                'confidence_level': confidence_level,
                'ci_lower': ci_lower,
                'ci_upper': ci_upper
            }
        )

    return pd.DataFrame(tmp_data)
```

让我们为`sample_size = 100`运行这个模拟，并查看结果。

```py
val_sim_df = run_simulations_for_value(before_df, sample_size = 100, 
    effect_size = 20, boot_iters = 1000, confidence_level = 0.95, 
    num_simulations = 20)
val_sim_df.set_index('simulation')[['sample_size', 'ci_lower', 'ci_upper']].head()
```

我们得到了20个模拟实验的数据。我们知道每个实验的置信区间，现在我们可以估计功效。

![](../Images/8d3def66d5ffc6d720061e145fb7631d.png)

如果置信区间的下限大于零，我们将拒绝零假设，所以让我们计算这些实验的比例。

```py
val_sim_df['successful_experiment'] = val_sim_df.ci_lower.map(
  lambda x: 1 if x > 0 else 0)

val_sim_df.groupby(['sample_size', 'effect_size']).aggregate(
    {
        'successful_experiment': 'mean',
        'experiment_id': 'count'
    }
) 
```

![](../Images/7f1b330a368956463c81ccda4aa3ada7.png)

我们从仅20个模拟实验和1000个自助法模拟开始，以估计其置信区间。如此少的模拟可以帮助我们迅速获得一个低分辨率的图像。考虑到我们从经典统计中得到的估计，我们应该预期大约10K的样本量能够提供所需的统计功效。

```py
tmp_dfs = []
for sample_size in [100, 250, 500, 1000, 2500, 5000, 10000, 25000]:
    print('Simulation for sample size = %d' % sample_size)
    tmp_dfs.append(
        run_simulations_for_value(before_df, sample_size = sample_size, effect_size = 20,
                              boot_iters = 1000, confidence_level = 0.95, num_simulations = 20)
    )

val_lowres_sim_df = pd.concat(tmp_dfs)
```

我们得到了与理论估算相似的结果。让我们尝试使用更多的模拟实验（100次和500次实验）来运行估算。我们可以看到，12.5K的客户将足以达到90%的统计功效。

我已将所有功效分析结果添加到图表中，这样我们就可以清晰地看到关系。

![](../Images/9cfd0affee2e30603d64edd2a7ab2f89.png)

在这种情况下，您可能已经看到，自助法可能需要大量的时间。例如，仅仅为了对3个样本量进行500次实验模拟来准确估计功效，我花了将近2小时。

![](../Images/18a54f89d1ddcc2b1fe53735207068ad.png)

现在，我们可以估计对于12.5K样本量，效应大小与统计功效之间的关系。

```py
tmp_dfs = []
for effect_size in [1, 5, 10, 15, 20, 25, 30, 40, 50, 60, 70, 80, 90, 100]:
    print('Simulation for effect size = %d' % effect_size)
    tmp_dfs.append(
        run_simulations_for_value(before_df, sample_size = 12500, effect_size = effect_size,
                              boot_iters = 1000, confidence_level = 0.95, num_simulations = 100)
    )

val_effect_size_sim_df = pd.concat(tmp_dfs) 
```

我们可以看到，如果对客户消费的实际影响高于20欧元，我们将获得更高的统计功效，并且我们能够在超过90%的情况下拒绝零假设。但在不到50%的情况下，我们将无法发现10欧元的效应。

![](../Images/bf5823e1bf3c00693153435d78223891.png)

让我们继续进行保留率的功效分析。完整的代码与客户消费分析的结构相似。我们将在下文详细讨论其中的细节。

```py
import tqdm

def get_sample_for_retention(pop_df, sample_size, effect_size):
    base_ret_model = smf.logit('retention ~ num_family_members', data = pop_df).fit(disp = 0)
    tmp_pop_df = pop_df.copy()
    tmp_pop_df['predicted_retention_proba'] = base_ret_model.predict()
    sample_df = tmp_pop_df.sample(sample_size)
    sample_df['treatment'] = sample_df.index.map(lambda x: 1 if np.random.uniform() > 0.5 else 0)
    sample_df['predicted_retention_proba'] = sample_df['predicted_retention_proba'] + effect_size * sample_df.treatment
    sample_df['retention'] = sample_df.predicted_retention_proba.map(lambda x: 1 if x >= np.random.uniform() else 0)
    return sample_df

def get_ci_for_retention(df, boot_iters, confidence_level):
    tmp_data = []

    for iter in range(boot_iters):
        sample_df = df.sample(df.shape[0], replace = True)
        ret_model = smf.logit('retention ~ treatment + num_family_members', data = sample_df).fit(disp = 0)
        tmp_data.append(
            {
                'iteration': iter,
                'coef': ret_model.params['treatment']
            }
        )

    coef_df = pd.DataFrame(tmp_data)
    return coef_df.coef.quantile((1 - confidence_level)/2), coef_df.coef.quantile(1 - (1 - confidence_level)/2)

def run_simulations_for_retention(pop_df, sample_size, effect_size, 
                                  boot_iters, confidence_level, num_simulations):
    tmp_data = []

    for sim in tqdm.tqdm(range(num_simulations)):
        sample_df = get_sample_for_retention(pop_df, sample_size, effect_size)
        num_users_treatment = sample_df[sample_df.treatment == 1].shape[0]
        retention_treatment = sample_df[sample_df.treatment == 1].retention.mean()
        num_users_control = sample_df[sample_df.treatment == 0].shape[0]
        retention_control = sample_df[sample_df.treatment == 0].retention.mean()

        ci_lower, ci_upper = get_ci_for_retention(sample_df, boot_iters, confidence_level)

        tmp_data.append(
            {
                'experiment_id': sim,
                'num_users_treatment': num_users_treatment,
                'retention_treatment': retention_treatment,
                'num_users_control': num_users_control,
                'retention_control': retention_control,
                'sample_size': sample_size,
                'effect_size': effect_size,
                'boot_iters': boot_iters,
                'confidence_level': confidence_level,
                'ci_lower': ci_lower,
                'ci_upper': ci_upper
            }
        )

    return pd.DataFrame(tmp_data)
```

首先，由于我们有一个二元的保留结果（客户是否会在下个月回归），我们将使用逻辑回归模型，而不是线性回归。我们可以看到，保留率与家庭规模有关。可能是因为当你为家人购买许多不同类型的产品时，找到一个能满足你所有需求的其他服务变得更困难。

```py
base_ret_model = smf.logit('retention ~ num_family_members', data = before_df).fit(disp = 0)
base_ret_model.summary().tables[1]
```

![](../Images/1ddeb7384347e3d71911c74ac2721967.png)

此外，函数`get_sample_for_retention`具有一些较为复杂的逻辑来调整处理组的结果。让我们一步一步地分析。

首先，我们在整个样本数据上拟合一个逻辑回归模型，并使用该模型来预测保留的概率。

```py
base_ret_model = smf.logit('retention ~ num_family_members', data = pop_df)\
  .fit(disp = 0)
tmp_pop_df = pop_df.copy()
tmp_pop_df['predicted_retention_proba'] = base_ret_model.predict()
```

然后，我们获取了一个与样本量相等的随机样本，并将其分为对照组和实验组。

```py
sample_df = tmp_pop_df.sample(sample_size)
sample_df['treatment'] = sample_df.index.map(
  lambda x: 1 if np.random.uniform() > 0.5 else 0)
```

对于处理组，我们通过预期的效应大小来增加保留概率。

```py
sample_df['predicted_retention_proba'] = sample_df['predicted_retention_proba'] \
    + effect_size * sample_df.treatment
```

最后一步是基于概率定义客户是否被保留。我们使用了均匀分布（在 0 和 1 之间的随机数）来进行计算：

+   如果从均匀分布中抽取的随机值低于概率值，那么客户就会被保留（这是按照指定的概率发生的），

+   否则，客户已经流失。

```py
sample_df['retention'] = sample_df.predicted_retention_proba.map(
    lambda x: 1 if x > np.random.uniform() else 0)
```

你可以运行一些模拟来确保我们的抽样函数按预期工作。例如，通过这个调用，我们可以看到控制组的客户保留率为 64%，与总体一致，而实验组的客户保留率为 93.7%（这是在 `effect_size = 0.3` 时的预期结果）。

```py
get_sample_for_retention(before_df, 10000, 0.3)\
  .groupby('treatment', as_index = False).retention.mean()

# |    |   treatment |   retention |
# |---:|------------:|------------:|
# |  0 |           0 |    0.640057 |
# |  1 |           1 |    0.937648 | 
```

现在，我们还可以运行模拟，看看达到 90% 统计功效所需的样本量。我们可以看到，12.5K 的样本量对于客户保留来说也是足够的。

![](../Images/b7331f964aa85e0d71b4d54ce62ba00f.png)

## 结果分析

我们可以使用线性回归或逻辑回归分析结果，或者利用我们已经拥有的函数来计算 bootstrap CI。

```py
value_model = smf.ols(
  'customer_value ~ treatment + num_family_members + country_avg_annual_earning', 
  data = experiment_df).fit(disp = 0)
value_model.summary().tables[1]
```

![](../Images/853fb14a89e81f475b7005a0bcb024dc.png)

因此，我们得出了对于客户支出 25.84 欧元的统计显著结果，其 95% 置信区间为 `(16.82, 34.87)`。

使用 bootstrap 函数时，置信区间将非常接近。

```py
get_ci_for_value(experiment_df.rename(
    columns = {'customer_value': 'predicted_value'}), 1000, 0.95)
# (16.28, 34.63)
```

同样，我们也可以使用逻辑回归进行客户保留分析。

```py
retention_model = smf.logit('retention ~ treatment + num_family_members',
    data = experiment_df).fit(disp = 0)
retention_model.summary().tables[1]
```

![](../Images/50b7ae6e1f8999db0b45baacf8f01883.png)

再次强调，bootstrap 方法提供了接近的置信区间（CI）估计。

```py
get_ci_for_retention(experiment_df, 1000, 0.95)
# (0.072, 0.187)
```

对于逻辑回归，解释系数可能会有些棘手。不过，我们可以使用一种简便方法：对于数据集中的每个客户，计算客户在控制组和处理组中的概率，然后查看概率之间的平均差异。

```py
experiment_df['treatment_eq_1'] = 1
experiment_df['treatment_eq_0'] = 0

experiment_df['retention_proba_treatment'] = retention_model.predict(
    experiment_df[['retention', 'treatment_eq_1', 'num_family_members']]\
        .rename(columns = {'treatment_eq_1': 'treatment'}))

experiment_df['retention_proba_control'] = retention_model.predict(
    experiment_df[['retention', 'treatment_eq_0', 'num_family_members']]\
      .rename(columns = {'treatment_eq_0': 'treatment'}))

experiment_df['proba_diff'] = experiment_df.retention_proba_treatment \
    - experiment_df.retention_proba_control

experiment_df.proba_diff.mean()
# 0.0281
```

所以，我们可以估算出客户保留的影响为 2.8%。

恭喜！我们终于完成了完整的 A/B 测试分析，并能够估算客户支出和客户保留的影响。我们的实验是成功的，因此在实际生活中，我们会开始考虑将其投入生产。

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/tree/main/simulations)上找到这个示例的完整代码。

# 摘要

让我快速回顾一下我们今天讨论的内容：

+   Bootstrap 的主要思想是从你的样本中进行有放回的模拟，假设总体分布与我们拥有的数据分布相同。

+   Bootstrap 在数据点较少、数据中有异常值或数据偏离理论分布时表现尤为出色。Bootstrap 还可以帮助你估算自定义指标。

+   你可以使用 bootstrap 来处理观察数据，例如，获取你的值的置信区间。

+   此外，bootstrap 被广泛应用于 A/B 测试分析——既用于估算处理的影响，也用于进行功效分析来设计实验。

> 非常感谢你阅读这篇文章。如果你有任何后续问题或评论，请在评论区留言。

# 参考文献

*除非另有说明，所有图片均由作者制作。*

本文的灵感来自Florent Buisson的书籍[《使用R和Python进行行为数据分析》](https://www.oreilly.com/library/view/behavioral-data-analysis/9781492061366/)。
