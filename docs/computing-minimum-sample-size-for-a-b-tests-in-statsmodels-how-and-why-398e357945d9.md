# 计算Statsmodels中A/B测试的最小样本量：如何计算以及为什么

> 原文：[https://towardsdatascience.com/computing-minimum-sample-size-for-a-b-tests-in-statsmodels-how-and-why-398e357945d9?source=collection_archive---------3-----------------------#2024-05-31](https://towardsdatascience.com/computing-minimum-sample-size-for-a-b-tests-in-statsmodels-how-and-why-398e357945d9?source=collection_archive---------3-----------------------#2024-05-31)

## 深入探讨Statsmodels如何以及为什么使用数值优化而非封闭形式的公式

[](https://jasonjiajs.medium.com/?source=post_page---byline--398e357945d9--------------------------------)[![Jason Jia](../Images/e3e3bdf0873cb48384dcf2f76a964ebd.png)](https://jasonjiajs.medium.com/?source=post_page---byline--398e357945d9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--398e357945d9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--398e357945d9--------------------------------) [Jason Jia](https://jasonjiajs.medium.com/?source=post_page---byline--398e357945d9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--398e357945d9--------------------------------) ·阅读时间10分钟·2024年5月31日

--

![](../Images/cfc8f9479bd992d2d46961c47bd201c2.png)

由DALL-E生成的图像

# 介绍

## 目前没有关于Statsmodels如何计算最小样本量的好资源。

在进行A/B测试之前，计算所需的最小样本量至关重要。一种常用的方法是在Python的Statsmodels包中调用[tt_ind_solve_power函数](https://www.statsmodels.org/stable/generated/statsmodels.stats.power.tt_ind_solve_power.html)，但目前在理解它的工作原理时存在两个空白：

1. 有很多优秀的文章（例如[Stan Nsky](/experiment-sample-size-calculation-using-power-analysis-81cb1bc5f74b)，TDS 2019）解释了参数的含义并提供了函数调用的示例。然而，它们并没有解释该函数是如何实际计算样本量的，也没有说明为什么该过程是正确的。

2. 也有许多优秀的文章（例如[Mintao Wei](/probing-into-minimum-sample-size-formula-derivation-and-usage-8db9a556280b#c172)，TDS 2023）解释了基于z检验的比例统计推导，例如转换率，这是许多在线样本量计算器（例如[Evan Miller的计算器](https://www.evanmiller.org/ab-testing/sample-size.html)）也常用的方法。然而，这并不是Statsmodels使用的方法，因此结果可能会有所不同。

## 这对数据科学家来说很重要，因为Statsmodels常用于Python中计算样本量。

数据科学家经常使用 Statsmodels 来获取最小样本量，但可能不知道它采用了与大多数文章描述的方式以及大多数在线计算器使用的方法不同的方式。了解该函数的工作原理是至关重要的，以便我们能够信任其结果。

## 本文通过解释 Statsmodels 的实际工作原理来填补这一空白。

本文旨在做出创新性贡献，解释 `tt_ind_solve_power` 如何实际计算样本量，为什么该过程是正确的，并且它相对于封闭形式解法带来的优势。[1]

**第1部分：** 它将首先解释样本量如何计算以及为什么该程序是正确的，分为两个步骤：

1.  显示样本量计算的统计推导。

1.  编写一个简化版的`tt_ind_solve_power`，它是统计推导的精确实现，并且产生与原始函数相同的输出。

**第2部分：** 接下来，它将解释它相对于封闭形式解法带来的两个优势：

1.  泛化性优势

1.  统计直觉的优势

# 第1部分：Statsmodels 如何计算最小样本量以及为什么它是正确的

## 1.1. 显示样本量计算的统计推导

## **核心思想**

一般的A/B测试是一个无配对的双样本t检验。Statsmodels 并不使用封闭形式的解法，而是通过两个步骤来获得最小样本量：

1.  对于给定的样本量，计算该检验的功效。

1.  运行数值优化算法，找到返回目标功效的样本量。

## **符号和概念**

以下是我们将在本文中使用的一些术语：

+   **n**：最小所需样本量。n = n_1 + n_2

+   **n_1, n_2**：治疗组和对照组的最小所需样本量

+   **比例：** n_2 = n_1 * 比例，对于50:50的分配，比例 = 1

+   **p**：p值

+   **𝛼:** 显著性水平 / I类错误

+   **𝛽:** II类错误；1-𝛽是检验的功效

+   **μ_1, μ_2**：治疗组和对照组的均值

+   **X̄1, X̄2:** 治疗组和对照组的样本均值

+   **t_(1-𝛼):** 临界值 / t-score，截断标准t分布的上100𝛼(%)部分。

+   **MDE:** 最小可检测效应，或给定所有其他参数下可以检测到的统计显著性差异的水平（例如，基础转换率为10%，预期提升为50%，因此预期的治疗转换率为15%，意味着MDE为15-10=5%=0.05）

+   **𝜎:** 每组观察值的标准差，假设是相同的

+   **d:** Cohen’s d / 标准化效应大小，由 MDE / 𝜎 给出

+   **H_0, H_1:** 原假设，备择假设

## **推导检验功效的公式**

1.  **定义原假设和备择假设：**

**2. 推导原假设下检验统计量的分布（H_0）：**

我们发现**在原假设下**，检验统计量t服从**t分布**，其**自由度为(n_1 + n_2 - 2)**。

这可以从以下内容得出：

其中X的样本方差计算如下：

**3\. 推导备择假设(H_1)下检验统计量的分布：**

我们发现**在备择假设下**，假设均值差异为最小可检测效应（MDE），检验统计量t服从**非中心t分布**，其**非中心性参数θ = d * sqrt((n1 * n2) / (n1 + n2))**，自由度为**(n_1 + n_2 - 2)**。

一个具有正非中心性参数的非中心t分布（nct）可以粗略地看作是一个标准t分布向右平移的结果。[2] 直观地说，标准t分布发生在原假设下，当我们期望平均效果为0时，而非中心t分布发生在备择假设下，当我们期望一个正的效果，并且该效果的平均值大致等于MDE。

定义：具有非中心性参数θ和自由度ν的非中心t分布随机变量T定义如下：

其中Z是标准正态随机变量，*V*是自由度为ν的卡方分布随机变量。

证明从以下观察开始：在备择假设下，真实的均值差异为MDE，因此我们可以减去MDE并除以总体标准差，从而得到标准正态变量。

**4\. 计算功效**

既然我们知道了原假设和备择假设下检验统计量的分布，并且两种分布的累积分布函数（cdf）都是已知的，我们可以轻松地计算功效，给定显著性水平和检验类型（双尾、大于、小于）。下图可视化了这一过程：

![](../Images/f7537362230bff4e8f958aaa8ca1cac5.png)

作者提供的图表

在Python中，实现如下：

```py
def power(self, effect_size, nobs1, alpha, ratio=1, df=None,
          alternative='two-sided'):
    nobs2 = nobs1*ratio
    if df is None:
        df = (nobs1 + nobs2 - 2)

    # Get non-centrality parameter
    nobs = nobs1 * nobs2 / (nobs1 + nobs2)
    d = effect_size
    nc_param = d * np.sqrt(nobs)

    # Get effective level of signifiance, alpha_
    if alternative in ['two-sided']:
        alpha_ = alpha / 2.
    elif alternative in ['smaller', 'larger']:
        alpha_ = alpha
    else:
        raise ValueError("alternative has to be 'two-sided', 'larger' " +
                        "or 'smaller'")

    # Compute power of a t-test
    power = 0
    if alternative in ['two-sided', 'larger']:
        crit_upp = stats.t.isf(alpha_, df) # isf = inverse survival function = value where Pr(t > value) = alpha
        power += 1 - special.nctdtr(df, nc_param, crit_upp)  # 1 - Pr(t < crit_upp) = Pr(t > crit_upp) for non-central t distribution
    if alternative in ['two-sided', 'smaller']:
        crit_low = stats.t.ppf(alpha_, df) # ppf = percent point function = value where Pr(t < value) = alpha
        power += special.nctdtr(df, nc_param, crit_low) # 
    return power
```

## **通过数值优化获取最小样本量**

既然我们现在知道了如何为一组给定的参数计算功效，我们就可以运行数值优化方法来找到实现目标功效的最小样本量。由于总样本量是治疗组样本量的函数（n = n_1 + 比例 * n_1），我们将求解n_1。

之所以有效，是因为功效随着样本量n_1的增加而单调增加。直观地说，更多的样本意味着A/B测试结果更加可靠，因此，如果备择假设为真，更多的值将拒绝原假设（见下图左侧子图）。

但这也意味着，减去目标功效后，得到的是一个单调递增的函数，起点为负，终点为正。根据介值定理和函数的单调性，存在一个唯一的根，对应于我们的最小样本量（见下图右侧子图）。

![](../Images/c66e6dffb8db57745b2cf9d9a2543f10.png)

作者提供的图表

一种流行且高效的数值优化方法是**Brent方法**。Brent方法是一种根求解算法，结合了如二分法、割线法和反二次插值等多种技术。有关其在Statsmodels中实现的更多细节，可以参见[此处](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.brentq.html)。

在Python中，实现如下：

```py
def solve_power(self, effect_size=None, nobs1=None, alpha=None, power=None,
                ratio=1., alternative='two-sided'):
    print('--- Arguments: ---')
    print('effect_size:', effect_size, 'nobs1:', nobs1, 'alpha:', alpha, 'power:', power, 'ratio:', ratio, 'alternative:', alternative, '\n')

    # Check that only nobs1 is None
    kwds = dict(effect_size=effect_size, nobs1=nobs1, alpha=alpha,
                power=power, ratio=ratio, alternative=alternative)
    key = [k for k,v in kwds.items() if v is None]
    assert(key == ['nobs1'])

    # Check that the effect_size is not 0
    if kwds['effect_size'] == 0:
        raise ValueError('Cannot detect an effect-size of 0\. Try changing your effect-size.')

    # Initialize the counter
    self._counter = 0

    # Define the function that we want to find the root of
    # We want to find nobs1 s.t. current power = target power, i.e. current power - target power = 0
    # So func = current power - target power
    def func(x):
        kwds['nobs1'] = x
        target_power = kwds.pop('power') # always the same target power specified in keywords, e.g. 0.8
        current_power = self.power(**kwds) # current power given the current nobs1, note that self.power does not have power as an argument
        kwds['power'] = target_power # add back power to kwds

        fval = current_power - target_power
        print(f'Iteration {self._counter}: nobs1 = {x}, current power - target power = {fval}')
        self._counter += 1
        return fval

    # Get the starting values for nobs1, given the brentq_expanding algorithm
    # In the original code, this is the self.start_bqexp dictionary set up in the __init__ method
    bqexp_fit_kwds = {'low': 2., 'start_upp': 50.}

    # Solve for nobs1 using brentq_expanding
    print('--- Solving for optimal nobs1: ---')
    val, _ = brentq_expanding(func, full_output=True, **bqexp_fit_kwds)

    return val
```

## 1.2\. 编写一个精简版的tt_ind_solve_power，它是统计推导的精确实现，并产生与原函数相同的输出

Statsmodels中的源文件可以在[此处](https://github.com/statsmodels/statsmodels/blob/main/statsmodels/stats/power.py)找到。虽然原函数被编写得更为强大，但它的普适性也使得我们很难理解代码是如何工作的。

因此，我逐行查看了源代码，并将其从1600行简化为160行，将10多个函数减少到仅2个，同时确保实现保持不变。

精简版的代码仅包含TTestIndPower类下的两个函数，完全遵循第一部分中解释的统计推导。

1.  **power**，用于计算给定样本量下的功效

1.  **solve_power**，使用Brent方法找到实现目标功效的最小样本量

这是精简版的完整代码，并包含一个测试，检查它是否产生与原函数相同的输出：

# 第2部分：Statsmodels使用的数值优化方法的优势

## 2.1\. 可推广性的好处

这种方法可以很容易地推广到查找其他感兴趣的参数（例如，查找显著性水平或最小可检测效应，而不是样本量）。

通过封闭解法方法，我们需要为每个参数找到一个方程，这可能是复杂的或不可行的。相比之下，同样的数值优化方法适用于任何参数。

## 2.2\. 对统计直觉的好处

这种方法可以说更直观，因为它是统计功效概念的自然扩展。此外，非中心t分布的概念提供了更清晰的洞见，帮助我们理解当其他参数变化时，最小样本量是如何变化的。

**案例1：参数变化导致θ增加，从而增加功效**

记住，非中心性参数是θ = d * sqrt((n1 * n2) / (n1 + n2))。θ的增加实际上会将非中心分布向右移动，减少在两种假设下分布的重叠部分。

这可以通过以下方式创建：

+   MDE的增加会增加Cohen's d，从而增加θ

+   人群标准差的减少会增加Cohen's d，从而增加θ

这会在相同的样本量下增加功效（见下图中的案例1），从而减少最小样本量。

**案例 2：参数变化直接导致功效增加而不改变θ**

+   显著性水平的提高意味着更多的值将导致拒绝零假设。这直接增加了功效（见下图中的案例 2），并减少了最小样本量。

**案例 3：目标功效的变化**

+   目标功效的增加意味着初始的样本量n将无法满足更高的目标功效，因此需要增加最小样本量。

![](../Images/5d68924648af71a50c77c3c4ec9f8591.png)

图表由作者提供

# 结论

在Statsmodels中解决最小样本量的函数功能强大，并依赖于数值优化。虽然与标准的封闭形式解法不同，但这种方法更容易理解计算样本量背后的统计直觉，并能够推广到计算其他感兴趣的参数。这是数据科学家，尤其是对营销和产品分析感兴趣的人员值得理解的一种方法。

# 参考文献

邓立（2020）。A/B测试所需的样本量。[https://towardsdatascience.com/required-sample-size-for-a-b-testing-6f6608dd330a](/required-sample-size-for-a-b-testing-6f6608dd330a)

Kohavi, R., Tang, D., & Xu, Y（2020）。值得信赖的在线对照实验：A/B测试实用指南。在*值得信赖的在线对照实验：A/B测试实用指南*（第I页）。剑桥：剑桥大学出版社。

Miller, E.（2013）。[样本量计算器](https://www.evanmiller.org/ab-testing/sample-size.html)。

Nsky, S.（2019）。使用功效分析计算实验样本量。[https://towardsdatascience.com/experiment-sample-size-calculation-using-power-analysis-81cb1bc5f74b](/experiment-sample-size-calculation-using-power-analysis-81cb1bc5f74b)

韦铭（2023）。探讨最小样本量公式：推导与应用。[https://towardsdatascience.com/probing-into-minimum-sample-size-formula-derivation-and-usage-8db9a556280b](/probing-into-minimum-sample-size-formula-derivation-and-usage-8db9a556280b)

# 脚注

[1] R中的等效函数是`pwr.t.test`。我们使用Python版本，因为开源代码供读者查看、比较和实践。

[2] 一般的非中心t分布不是对称的，也不以Cohen的d为中心，但随着自由度的增加，趋向于对称。
