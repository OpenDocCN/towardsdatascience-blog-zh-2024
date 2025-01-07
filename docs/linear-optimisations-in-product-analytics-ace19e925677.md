# 产品分析中的线性优化

> 原文：[https://towardsdatascience.com/linear-optimisations-in-product-analytics-ace19e925677?source=collection_archive---------2-----------------------#2024-12-18](https://towardsdatascience.com/linear-optimisations-in-product-analytics-ace19e925677?source=collection_archive---------2-----------------------#2024-12-18)

## 解决背包问题

[](https://miptgirl.medium.com/?source=post_page---byline--ace19e925677--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--ace19e925677--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ace19e925677--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ace19e925677--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--ace19e925677--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ace19e925677--------------------------------) ·阅读时间11分钟·2024年12月18日

--

![](../Images/c6f2f51099b2b5f7cfd3ee32ce1429e8.png)

由DALL-E 3生成的图像

这可能令人惊讶，但在本文中，我想谈谈背包问题，这个经典的优化问题已经被研究了一个多世纪。根据[维基百科](https://en.wikipedia.org/wiki/Knapsack_problem)，该问题的定义如下：

> 给定一组物品，每个物品都有一个重量和一个价值，确定应该包括哪些物品，以便总重量小于或等于给定的限制，同时总价值尽可能大。

虽然产品分析师可能不会亲自打包背包，但其背后的数学模型与我们许多任务高度相关。背包问题在产品分析中有许多现实世界的应用。以下是一些例子：

+   **营销活动：** 营销团队在不同渠道和区域运营活动时，预算和能力有限。他们的目标是最大化某个KPI，例如新用户数量或收入，同时遵守现有的约束条件。

+   **零售空间优化：** 一家零售商在店铺中有有限的物理空间，他们希望通过优化产品摆放来最大化收入。

+   **产品发布优先级：** 在发布新产品时，运营团队的能力可能有限，需要对特定市场进行优先排序。

这样的任务以及类似的任务非常常见，许多分析师会定期遇到它们。因此，在本文中，我将探讨解决此类问题的不同方法，涵盖从朴素的简单技巧到更先进的线性规划方法。

我选择这个话题的另一个原因是，线性规划是处方分析中最强大和最流行的工具之一——一种专注于为利益相关者提供可操作选项以做出明智决策的分析方法。因此，我认为这是任何分析师工具包中必备的技能。

# 案例

让我们直接进入将要探讨的案例。假设我们是一个营销团队的一部分，正在为下个月计划活动。我们的目标是最大化关键绩效指标（KPI），例如获取的用户数量和收入，同时在有限的营销预算内运营。

我们已估算了不同国家和渠道的各种营销活动的预期结果。以下是我们拥有的数据：

+   `country` — 我们可以进行一些促销活动的市场；

+   `channel` — 获取方式，例如社交网络或影响者活动；

+   `users` — 在促销活动一个月内预期获得的用户数量；

+   `cs_contacts` — 新用户产生的增量客户支持联系；

+   `marketing_spending` — 活动所需的投资；

+   `revenue` — 从已获取客户中产生的第一年LTV（客户终身价值）。

![](../Images/b047bf0905f1fde49ac919e1fbc9ce87.png)

> 请注意，数据集是合成的，随机生成的，因此不要尝试从中推断任何市场相关的见解。

首先，我计算了高级统计数据，以便查看这些数字。

![](../Images/eb3aa6704cff645dd793a2c778ce7d88.png)

让我们确定一组最佳的营销活动组合，在保持3000万美元营销预算的同时最大化收入。

# 暴力破解法

乍一看，这个问题可能显得简单：我们可以计算所有可能的营销活动组合并选择最佳的那一个。然而，这可能是一个具有挑战性的任务。

由于有62个数据段，每个数据段可以选择包含或排除，因此有2⁶²种可能的组合。这大约是4.6×10¹⁸种组合——一个天文数字。

为了更好地理解计算可行性，让我们考虑一个较小的15个数据段的子集，并估算一次迭代所需的时间。

```py
import itertools
import pandas as pd
import tqdm

# reading data
df = pd.read_csv('marketing_campaign_estimations.csv', sep = '\t')
df['segment'] = df.country + ' - ' + df.channel

# calculating combinations
combinations = []
segments = list(df.segment.values)[:15]
print('number of segments: ', len(segments))

for num_items in range(len(segments) + 1):
  combinations.extend(
      itertools.combinations(segments, num_items)
  )
print('number of combinations: ', len(combinations))

tmp = []
for selected in tqdm.tqdm(combinations):
    tmp_df = df[df.segment.isin(selected)]
    tmp.append(
        {
        'selected_segments': ', '.join(selected),
        'users': tmp_df.users.sum(),
        'cs_contacts': tmp_df.cs_contacts.sum(),
        'marketing_spending': tmp_df.marketing_spending.sum(),
        'revenue': tmp_df.revenue.sum()
        }
    )

# number of segments:  15
# number of combinations:  32768
```

处理15个数据段大约花费了4秒钟，这使我们能够每秒处理约7,000次迭代。根据这个估算，让我们计算处理62个数据段所需的执行时间。

```py
2**62 / 7000 / 3600 / 24 / 365
# 20 890 800.6
```

采用暴力破解法，回答我们问题大约需要2090万年——显然这不是一个可行的选项。

执行时间完全由数据段的数量决定。移除一个数据段可以将时间减少一半。考虑到这一点，让我们探讨合并数据段的可能方式。

像往常一样，较小的细分市场比大规模的更多，所以合并它们是一个合理的步骤。然而，值得注意的是，这种方法可能会降低准确性，因为多个细分市场被合并为一个。尽管如此，它仍然可以产生一个“足够好”的解决方案。

为了简化，我们将合并所有贡献少于0.1%收入的细分市场。

```py
df['share_of_revenue'] = df.revenue/df.revenue.sum() * 100
df['segment_group'] = list(map(
    lambda x, y: x if y >= 0.1 else 'other',
    df.segment,
    df.share_of_revenue
))

print(df[df.segment_group == 'other'].share_of_revenue.sum())
# 0.53
print(df.segment_group.nunique())
# 52
```

采用这种方法，我们将把十个细分市场合并为一个，占总收入的0.53%（可能的误差范围）。剩余52个细分市场后，我们可以在仅20.4千年内获得解决方案。虽然这是一个显著的改进，但仍然不够充分。

你可以考虑其他针对特定任务量身定制的启发式方法。例如，如果你的约束是一个比率（例如，联系率 = 客户服务联系数 / 用户 ≤ 5%），你可以将所有符合约束的细分市场归为一组，因为最优解会包括所有这些细分市场。然而，在我们的案例中，我看不出有任何其他策略来减少细分市场的数量，因此暴力计算似乎不切实际。

也就是说，如果组合的数量相对较小，并且暴力计算可以在合理的时间内执行，那么它可以是一个理想的方法。它简单易开发，并且提供准确的结果。

# 天真方法：查看表现最好的细分市场

由于暴力计算不适合计算所有组合，我们考虑使用一种更简单的算法来解决这个问题。

一种可能的方法是专注于表现最好的细分市场。我们可以通过计算每花费一美元所获得的收入来评估细分市场的表现，然后根据这个比率对所有活动进行排序，选择符合营销预算的表现最佳的活动。让我们来实现这个方法。

```py
df['revenue_per_spend'] = df.revenue / df.marketing_spending 
df = df.sort_values('revenue_per_spend', ascending = False)
df['spend_cumulative'] = df.marketing_spending.cumsum()
selected_df = df[df.spend_cumulative <= 30000000]
print(selected_df.shape[0])
# 48 
print(selected_df.revenue.sum()/1000000)
# 107.92
```

采用这种方法，我们选择了48个活动，并获得了107.92百万美元的收入。

不幸的是，尽管逻辑看起来合理，但它并不是最大化收入的最佳解决方案。让我们来看一个只有三个营销活动的简单例子。

![](../Images/5a45bbcb23204b349ee62ca17f855fd1.png)

使用选择最佳市场的方法，我们将选择法国，并获得6800万美元的收入。然而，如果选择另外两个市场，我们可以获得显著更好的结果——9750万美元。关键点在于，我们的算法不仅优化了最大化收入，还优化了选择的细分市场数量。因此，这种方法将无法得到最佳结果，尤其是考虑到它无法应对多个约束条件。

# 线性规划

由于所有简单的方法都失败了，我们必须回到基础理论，探讨这个问题背后的理论。幸运的是，背包问题已经研究了许多年，我们可以应用优化技术，在几秒钟内解决它，而不是几年。

我们试图解决的问题是一个整数规划问题，实际上它是线性规划的一个子领域。

我们稍后会讨论这个方法，但首先，让我们对优化过程的关键概念达成一致。每个优化问题都由以下几个部分组成：

+   **决策变量**：模型中可以调整的参数，通常代表我们希望做出的杠杆或决策。

+   **目标函数**：我们旨在最大化或最小化的目标变量。无需多言，它必须依赖于决策变量。

+   **约束条件**：对决策变量施加的条件，定义了它们的可能值。例如，确保团队不能工作负数小时。

牢记这些基本概念，我们可以将线性规划定义为满足以下条件的场景：

+   目标函数是线性的。

+   所有约束都是线性的。

+   决策变量是实数值的。

整数规划与线性规划非常相似，唯一的区别是：一些或所有的决策变量必须是整数。虽然这看起来像是一个小的变化，但它显著影响了解决方法，需要比线性规划更复杂的方法。一种常见的技术是[分支定界法](https://en.wikipedia.org/wiki/Branch_and_bound)。我们不会在这里深入探讨理论，但你总是可以在网上找到更详细的解释。

对于线性优化，我更喜欢广泛使用的Python包[PuLP](https://coin-or.github.io/pulp/)。不过，也有其他可用的选择，比如[Python MIP](https://pypi.org/project/mip/)或[Pyomo](https://github.com/Pyomo/pyomo)。让我们通过pip安装PuLP。

```py
! pip install pulp
```

现在，是时候将我们的任务定义为一个数学优化问题了。具体步骤如下：

+   **定义决策变量的集合**（我们可以调整的杠杆）。

+   **对齐目标函数**（我们将优化的变量）。

+   **制定约束条件**（在优化过程中必须满足的条件）。

让我们逐步进行。但首先，我们需要创建问题对象并设置目标——在我们的案例中是最大化。

```py
from pulp import *
problem = LpProblem("Marketing_campaign", LpMaximize)
```

下一步是定义决策变量——在优化过程中我们可以改变的参数。我们的主要决策是是否开展市场营销活动。因此，我们可以将其建模为每个细分市场的二元变量集（0或1）。让我们通过PuLP库来实现。

```py
segments = range(df.shape[0])  
selected = LpVariable.dicts("Selected", segments, cat="Binary")
```

之后，是时候对目标函数进行对齐了。如前所述，我们希望最大化收入。总收入将是所有选定细分市场的收入之和（其中`decision_variable = 1`）。因此，我们可以将此公式定义为每个细分市场的预期收入乘以决策二元变量的总和。

```py
problem += lpSum(
  selected[i] * list(df['revenue'].values)[i] 
  for i in segments
)
```

最后一步是添加约束。让我们从一个简单的约束开始：我们的市场营销支出必须低于3000万美元。

```py
problem += lpSum(
    selected[i] * df['marketing_spending'].values[i]
    for i in segments
) <= 30 * 10**6
```

> **提示**：你可以打印`problem`来仔细检查目标函数和约束条件。

现在我们已经定义了所有内容，可以运行优化并分析结果。

```py
problem.solve()
```

运行优化只需不到一秒钟，与暴力破解需要数千年相比，效率显著提升。

```py
Result - Optimal solution found

Objective value:                110162662.21000001
Enumerated nodes:               4
Total iterations:               76
Time (CPU seconds):             0.02
Time (Wallclock seconds):       0.02
```

让我们将模型执行的结果——表示每个区段是否被选择的决策变量——保存到我们的数据框中。

```py
df['selected'] = list(map(lambda x: x.value(), selected.values()))
print(df[df.selected == 1].revenue.sum()/10**6)
# 110.16
```

它就像魔法一样快速提供解决方案。此外，请注意，相比我们的直觉方法，我们达到了更高的收入：1.1016亿美元对比1.0792亿美元。

我们已经用一个简单的例子测试了整数规划，该例子仅涉及一个约束，但我们可以进一步扩展。例如，我们可以为CS联系方式添加额外的约束，以确保我们的运营团队能够以健康的方式处理需求：

+   额外的CS联系方式数量 ≤ 5000

+   联系率（CS联系方式/用户）≤ 0.042

```py
# define the problem
problem_v2 = LpProblem("Marketing_campaign_v2", LpMaximize)

# decision variables
segments = range(df.shape[0]) 
selected = LpVariable.dicts("Selected", segments, cat="Binary")

# objective function
problem_v2 += lpSum(
  selected[i] * list(df['revenue'].values)[i] 
  for i in segments
)

# Constraints
problem_v2 += lpSum(
    selected[i] * df['marketing_spending'].values[i]
    for i in segments
) <= 30 * 10**6

problem_v2 += lpSum(
    selected[i] * df['cs_contacts'].values[i]
    for i in segments
) <= 5000

problem_v2 += lpSum(
    selected[i] * df['cs_contacts'].values[i]
    for i in segments
) <= 0.042 * lpSum(
    selected[i] * df['users'].values[i]
    for i in segments
)

# run the optimisation
problem_v2.solve()
```

代码非常简单，唯一有点复杂的部分是将比例约束转换为更简单的线性形式。

另一个可能的约束条件是限制选定选项的数量，例如，限制为10个。这个约束在规范性分析中非常有用，比如当你需要选择最具影响力的前N个焦点区域时。

```py
# define the problem
problem_v3 = LpProblem("Marketing_campaign_v2", LpMaximize)

# decision variables
segments = range(df.shape[0]) 
selected = LpVariable.dicts("Selected", segments, cat="Binary")

# objective function
problem_v3 += lpSum(
  selected[i] * list(df['revenue'].values)[i] 
  for i in segments
)

# constraints
problem_v3 += lpSum(
    selected[i] * df['marketing_spending'].values[i]
    for i in segments
) <= 30 * 10**6

problem_v3 += lpSum(
    selected[i] for i in segments
) <= 10

# run the optimisation
problem_v3.solve()
df['selected'] = list(map(lambda x: x.value(), selected.values()))
print(df.selected.sum())
# 10
```

另一个可以调整我们问题的选项是改变目标函数。我们一直在优化收入，但假设我们希望同时最大化收入和新增用户。为此，我们可以稍微修改目标函数。

让我们考虑最好的方法。我们可以计算收入和新增用户的总和并力图最大化它。然而，由于收入通常比新增用户高出1000倍，结果可能会倾向于最大化收入。为了使这些指标更具可比性，我们可以基于它们的总和来规范化收入和用户数。然后，我们可以将目标函数定义为这些比例的加权和。我会给这两个指标赋予相等的权重（0.5），但你可以调整权重，使其中一个指标的权重更大。

```py
# define the problem
problem_v4 = LpProblem("Marketing_campaign_v2", LpMaximize)

# decision variables
segments = range(df.shape[0]) 
selected = LpVariable.dicts("Selected", segments, cat="Binary")

# objective Function
problem_v4 += (
    0.5 * lpSum(
        selected[i] * df['revenue'].values[i] / df['revenue'].sum()
        for i in segments
    )
    + 0.5 * lpSum(
        selected[i] * df['users'].values[i] / df['users'].sum()
        for i in segments
    )
)

# constraints
problem_v4 += lpSum(
    selected[i] * df['marketing_spending'].values[i]
    for i in segments
) <= 30 * 10**6

# run the optimisation
problem_v4.solve()
df['selected'] = list(map(lambda x: x.value(), selected.values()))
```

我们得到了最优目标函数值0.6131，收入为1.0436亿美元，新增用户为136,370人。

就这样！我们学会了如何使用整数规划来解决各种优化问题。

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/blob/main/discrete_optimisations/discrete_optimisation.ipynb)上找到完整的代码。

# 总结

在本文中，我们探讨了解决背包问题及其在产品分析中的类比问题的不同方法。

+   我们从暴力破解方法开始，但很快意识到它会占用不合理的时间。

+   接下来，我们尝试通过直觉选取表现最好的区段，但这种方法得出的结果是错误的。

+   最后，我们转向了整数规划，学习了如何将我们的产品任务转化为优化模型并有效地解决它们。

有了这个，我希望你已经为你的工具包增添了另一项有价值的分析工具。

> 非常感谢你阅读本文。我希望这篇文章对你有所启发。如果你有任何后续问题或意见，请在评论区留言。

# 参考资料

*除非另有说明，所有图片均由作者制作。*
