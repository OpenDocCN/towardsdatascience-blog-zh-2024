# 优化在设计涉及小样本实验中的力量

> 原文：[https://towardsdatascience.com/the-power-of-optimization-in-designing-experiments-involving-small-samples-de87f9783d3b?source=collection_archive---------6-----------------------#2024-10-21](https://towardsdatascience.com/the-power-of-optimization-in-designing-experiments-involving-small-samples-de87f9783d3b?source=collection_archive---------6-----------------------#2024-10-21)

## 使用Python中的优化技术设计更精确实验的逐步指南

[](https://medium.com/@leandro.magga?source=post_page---byline--de87f9783d3b--------------------------------)[![Leandro Magga](../Images/75d2b6b31635ac2bd409bfb91d151ac4.png)](https://medium.com/@leandro.magga?source=post_page---byline--de87f9783d3b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--de87f9783d3b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--de87f9783d3b--------------------------------) [Leandro Magga](https://medium.com/@leandro.magga?source=post_page---byline--de87f9783d3b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--de87f9783d3b--------------------------------) ·10分钟阅读·2024年10月21日

--

实验是用科学严谨的方法检验假设的基础。在医学领域，实验用于评估新疗法对患者的效果；而在数字世界中，像[Amazon](https://www.amazon.jobs/en/teams/aeo)、[Netflix](https://netflixtechblog.com/experimentation-is-a-major-focus-of-data-science-across-netflix-f67923f8e985)和[Uber](https://www.uber.com/en-CL/blog/xp/)这样的科技巨头每年都会进行成千上万次的实验，以优化和改进他们的平台。

对于大规模实验，通常使用随机分配，并且被认为是“黄金标准”。在数据量充足的情况下，随机性往往能够生成可比较的组，其中重要的实验前因素被平衡，并且交换性假设得以成立。

然而，当实验的样本非常小的时候，随机分配通常无法创建统计上等效的组。因此，**我们如何高效地在处理组和控制组之间分配单元呢？**

## 你将学到的内容：

在这篇文章中，我将解释一种基于优化的方法，用于构建等效的实验组，这是Bertsimas等人在[这篇文章](https://medium.com/r?url=https%3A%2F%2Fpubsonline.informs.org%2Fdoi%2F10.1287%2Fopre.2015.1361)中提出的。

通过Python中的一个简单示例，我们将学习如何：

+   设计一个基于优化的实验。

+   使用自助法技术进行推断。

+   在Python中实现代码，以便你自己的实验使用

# 优化的力量

在深入研究我们的例子和Python代码之前，首先讨论一下使用基于优化的方法来设计实验的好处。

> 优化使得统计结果更加精确，从而可以进行更强有力的推断。

基于优化的方法**将实验组进行匹配，以最小化整体均值和方差的差异**。这样可以使统计结果更加精确，将其紧密集中在名义值周围，同时仍然保持无偏的估计。这种精度的提高使得推断能力更强（我们稍后会介绍使用的自助法算法）。

这使得研究人员能够通过更少的数据得出统计上有效的结论，从而减少实验成本——这是肿瘤学研究等学科中的一个重要优势，因为在小鼠癌症模型中测试化疗药物既繁琐又昂贵。此外，与其他适用于小样本量的方法相比，优化方法已经证明在效果上优于其他方法，正如我们稍后通过模拟实验看到的那样。

现在，让我们深入一个例子，看看如何使用Python应用这种方法！

# 在Python中实现算法

## 案例研究：20只小鼠的药物实验

假设我们有一个20只小鼠的实验，研究药物对肿瘤生长的影响，肿瘤大小初始值不同。假设初始肿瘤大小呈正态分布，均值为200 mg，标准差为300 mg（为了确保值为非负数而进行了截断）。我们可以通过以下Python代码生成小鼠种群：

```py
import numpy as np
import pandas as pd
import scipy.stats as stats

def create_experiment_data(n_mice, mu, sigma, seed):
      lower, upper = 0, np.inf
      initial_weights = stats.truncnorm(
          (lower - mu) / sigma,
          (upper - mu) / sigma,
          loc=mu,
          scale=sigma
      ).rvs(size=n_mice, random_state=seed)
      return pd.DataFrame({
          'mice': list(range(1, n_mice+1)),
          'initial_weight': initial_weights,
      })

tumor_data = create_experiment_data(n_mice=20, mu=200, sigma=300, seed=123)
print(tumor_data.head())
```

```py
>    mice  initial_weight
0     1      424.736888
1     2      174.691035
2     3      141.016478
3     4      327.518749
4     5      442.239789
```

现在，我们需要将20只啮齿动物分成两组，每组10只——一组将接受治疗，另一组将接受安慰剂。我们将通过优化来完成这一任务。

我们还假设肿瘤生长是在一天内观察的，并且遵循Gompertz模型（详见[*《癌症研究中的数学模型》*](https://medium.com/r?url=https%3A%2F%2Fwww.semanticscholar.org%2Fpaper%2FMathematical-models-in-cancer-research-Wheldon%2F9e3176627ff018f369b87b1aa8c97df203020f76)）。假设治疗具有确定性效果，可以减少肿瘤大小250 mg。

## 实验设计

我们的目标是将等效组的创建表述为一个优化问题，其中目标是最小化初始肿瘤重量的均值和方差差异。

为了实现这一目标，我们需要遵循三个步骤：

## 第1步：规范化初始肿瘤重量

首先，整个样本必须进行预处理，并且度量标准应该规范化，使其均值为零，方差为单位：

```py
mean = tumor_data['initial_weight'].mean()
std = tumor_data['initial_weight'].std()

tumor_data['norm_initial_weight'] = (tumor_data['initial_weight'] - mean) / std
```

## 第2步：使用优化创建组

接下来，我们需要实现一个通用的优化模型，该模型构建具有*k个受试者*的*m组*，并最小化*任意两组之间的最大差异*（有关模型变量的完整描述，请参见[文章](https://pubsonline.informs.org/doi/10.1287/opre.2015.1361)），并传递归一化度量：

![](../Images/73f213fb1e811cde82574a44e4a94e8a.png)

创建每个含有k个单位的m组的优化模型（来自[Bertsimas et al. 2015](https://pubsonline.informs.org/doi/10.1287/opre.2015.1361)）。

该数学模型可以使用Python中的ortools库和SCIP求解器实现，具体如下：

```py
from ortools.linear_solver import pywraplp
from typing import Union

class SingleFeatureOptimizationModel:
    """
    Implements the discrete optimization model proposed by Bertsimas et al. (2015) in "The Power of 
    Optimization Over Randomization in Designing Experiments Involving Small Samples". 
    See: https://doi.org/10.1287/opre.2015.1361.
    """

    def __init__(self, data: pd.DataFrame, n_groups: int, units_per_group: int, metric: str, unit: str):
        self.data = data.reset_index(drop=True)
        self.parameters = {
            'rho': 0.5,
            'groups': range(n_groups),
            'units': range(len(self.data)),
            'unit_list': self.data[unit].tolist(),
            'metric_list': self.data[metric].tolist(),
            'units_per_group': units_per_group,
        }
        self._create_solver()
        self._add_vars()
        self._add_constraints()
        self._set_objective()

    def _create_solver(self):
        self.model = pywraplp.Solver.CreateSolver("SCIP")
        if self.model is None:
            raise Exception("Failed to create SCIP solver")

    def _add_vars(self):
        self.d = self.model.NumVar(0, self.model.infinity(), "d")
        self.x = {}
        for i in self.parameters['units']:
            for p in self.parameters['groups']:
                self.x[i, p] = self.model.IntVar(0, 1, "")

    def _set_objective(self):
        self.model.Minimize(self.d)

    def _add_constraints(self):
        self._add_constraints_d_bounding()
        self._add_constraint_group_size()
        self._add_constraint_all_units_assigned()

    def _add_constraints_d_bounding(self):
        rho = self.parameters['rho']
        for p in self.parameters['groups']:
            for q in self.parameters['groups']:
                if p < q:
                    self.model.Add(self.d >= self._mu(p) - self._mu(q) + rho * self._var(p) - rho * self._var(q))
                    self.model.Add(self.d >= self._mu(p) - self._mu(q) + rho * self._var(q) - rho * self._var(p))
                    self.model.Add(self.d >= self._mu(q) - self._mu(p) + rho * self._var(p) - rho * self._var(q))
                    self.model.Add(self.d >= self._mu(q) - self._mu(p) + rho * self._var(q) - rho * self._var(p))

    def _add_constraint_group_size(self):
        for p in self.parameters['groups']:
            self.model.Add(
                self.model.Sum([
                    self.x[i,p] for i in self.parameters['units']
                ]) == self.parameters['units_per_group']
                )

    def _add_constraint_all_units_assigned(self):
        for i in self.parameters['units']:
            self.model.Add(
                self.model.Sum([
                    self.x[i,p] for p in self.parameters['groups']
                ]) == 1
                )

    def _add_contraint_symmetry(self):
        for i in self.parameters['units']:
            for p in self.parameters['units']: 
                if i < p:
                    self.model.Add(
                        self.x[i,p] == 0 
                        )

    def _mu(self, p):
        mu = self.model.Sum([
            (self.x[i,p] * self.parameters['metric_list'][i]) / self.parameters['units_per_group']
            for i in self.parameters['units']
            ])
        return mu

    def _var(self, p):
        var = self.model.Sum([
            (self.x[i,p]*(self.parameters['metric_list'][i])**2) / self.parameters['units_per_group']
            for i in self.parameters['units']
            ])
        return var

    def optimize(
            self,
            max_run_time: int = 60,
            max_solution_gap: float = 0.05,
            max_solutions: Union[int, None] = None,
            num_threads: int = -1,
            verbose: bool = False
    ):
        """
        Runs the optimization model.

        Args:
            max_run_time: int
                Maximum run time in minutes.
            max_solution_gap: float
                Maximum gap with the LP relaxation solution.
            max_solutions: int
                Maximum number of solutions until stop.
            num_threads: int
                Number of threads to use in solver.
            verbose: bool
                Whether to set the solver output.

        Returns: str
            The status of the solution.
        """
        self.model.SetTimeLimit(max_run_time * 60 * 1000)
        self.model.SetNumThreads(num_threads)

        if verbose:
            self.model.EnableOutput()

        self.model.SetSolverSpecificParametersAsString(f"limits/gap = {max_solution_gap}")
        self.model.SetSolverSpecificParametersAsString(f"limits/time = {max_run_time * 60}")

        if max_solutions:
            self.model.SetSolverSpecificParametersAsString(f"limits/solutions = {max_solutions}")

        status = self.model.Solve()

        if verbose:
            if status == pywraplp.Solver.OPTIMAL:
                print("Optimal Solution Found.")
            elif status == pywraplp.Solver.FEASIBLE:
                print("Feasible Solution Found.")
            else:
                print("Problem infeasible or unbounded.")

        self._extract_solution()
        return status

    def _extract_solution(self):
        tol = 0.01
        self.assignment = {}
        for i in self.parameters['units']:
            for p in self.parameters['groups']:
                if self.x[i,p].solution_value() > tol:
                    self.assignment.setdefault(p, []).append(self.parameters['unit_list'][i])

    def get_groups_list(self):
        return list(self.assignment.values())
```

```py
model = SingleFeatureOptimizationModel(
    data = tumor_data,
    n_groups = 2,
    units_per_group = 10,
    unit = 'mice',
    metric = 'norm_initial_weight',

)

status = model.optimize()
optimized_groups = model.get_groups_list()
print(f"The optimized mice groups are: {optimized_groups}")
```

```py
> The optimized mice groups are: [
   [1, 4, 5, 6, 8, 12, 14, 16, 17, 18], 
   [2, 3, 7, 9, 10, 11, 13, 15, 19, 20]
  ]
```

> ***注意：*** *参数* rho *控制最小化一阶矩和二阶矩之间差异的折衷，由研究人员选择。在我们的示例中，我们假设* rho *等于0.5。*

## Paso 3：随机化分配哪组接受哪种治疗

最后，我们随机决定哪一组实验小鼠将接受药物，哪一组将接受安慰剂：

```py
import random

def random_shuffle_groups(group_list, seed):
  random.seed(seed)
  random.shuffle(group_list)
  return group_list

randomized_groups = random_shuffle_groups(optimized_groups, seed=123)
treatment_labels = ["Placebo", "Treatment"]
treatment_dict = {treatment_labels[i]: randomized_groups[i] for i in range(len(randomized_groups))}
print(f"The treatment assignment is: {treatment_dict}")
```

```py
> The treatment assignment is: {
   'Placebo': [2, 3, 7, 9, 10, 11, 13, 15, 19, 20], 
   'Treatment': [1, 4, 5, 6, 8, 12, 14, 16, 17, 18]
}
```

让我们通过分析两组初始肿瘤权重的均值和方差来查看结果的质量：

```py
mice_assignment_dict = {inx: gp for gp, indices in treatment_dict.items() for inx in indices}
tumor_data['treatment'] = tumor_data['mice'].map(mice_assignment_dict)

print(tumor_data.groupby('treatment').agg(
    avg_initial_weight = ('initial_weight', 'mean'),
    std_initial_weight = ('initial_weight', 'std'),
).round(2))
```

```py
>          avg_initial_weight  std_initial_weight
treatment                                        
Placebo                302.79              202.54
Treatment              303.61              162.12
```

**这是预实验肿瘤权重均值和方差最小的组分配**！现在让我们进行实验并分析结果。

# 使用自助法进行推断

## **模拟肿瘤生长**

根据已确定的治疗分配，假设治疗组的效果为-250毫克，肿瘤生长在一天内使用Gompertz模型进行模拟：

```py
import numpy as np
from scipy.integrate import odeint

# Gompertz model parameters
a = 1 
b = 5

# Critical weight
wc = 400

def gompex_growth(w, t):
    """
    Gomp-ex differential equation model based on the initial weight.
    """
    growth_rate = a + max(0, b * np.log(wc / w))
    return w * growth_rate

def simulate_growth(w_initial, t_span):
    """
    Simulate the tumor growth using the Gomp-ex model.
    """
    return odeint(gompex_growth, w_initial, t_span).flatten()

def simulate_tumor_growth(data: pd.DataFrame, initial_weight: str, treatment_col: str, treatment_value: str, treatment_effect: float):
    """
    Simulate the tumor growth experiment and return the dataset.
    """
    t_span = np.linspace(0, 1, 2)
    final_weights = np.array([simulate_growth(w, t_span)[-1] for w in data[initial_weight]])

    experiment_data = data.copy()
    mask_treatment = data[treatment_col] == treatment_value
    experiment_data['final_weight'] = np.where(mask_treatment, final_weights + treatment_effect, final_weights)

    return experiment_data.round(2)

experiment_data = simulate_tumor_growth(
    data = tumor_data, 
    initial_weight = 'initial_weight', 
    treatment_col = 'treatment', 
    treatment_value = 'Treatment', 
    treatment_effect = -250
)

print(experiment_data.head())
```

```py
>   mice  initial_weight  norm_initial_weight  treatment  final_weight
0     1          424.74                 0.68  Treatment        904.55
1     2          174.69                -0.72    Placebo        783.65
2     3          141.02                -0.91    Placebo        754.56
3     4          327.52                 0.14  Treatment        696.60
4     5          442.24                 0.78  Treatment        952.13
```

```py
mask_tr = experiment_data.group == 'Treatment'
mean_tr = experiment_data[mask_tr]['final_weight'].mean()
mean_co = experiment_data[~mask_tr]['final_weight'].mean()
print(f"Mean difference between treatment and control: {round(mean_tr - mean_co)} mg")
```

```py
> Mean difference between treatment and control: -260 mg
```

现在我们已经得到了最终的肿瘤权重，我们观察到治疗组的平均最终肿瘤重量比对照组低260毫克。然而，为了确定这个差异是否具有统计学意义，我们需要应用以下自助法机制来计算p值。

## **基于优化设计的自助法推断**

> “在基于优化的设计中，诸如组间平均差异之类的统计数据变得更加精确，但不再遵循常规分布。因此，应使用自助法推断方法来得出有效结论。”

[Bertsimas et al. (2015)](https://medium.com/r?url=https%3A%2F%2Fpubsonline.informs.org%2Fdoi%2F10.1287%2Fopre.2015.1361) 提出的自助法推断方法包括使用带放回的抽样构建估计量的基准分布。在每次迭代中，使用优化进行组分配，最后得出*p值*，具体如下：

```py
from tqdm import tqdm
from typing import Any, List

def inference(data: pd.DataFrame, unit: str, outcome: str, treatment: str, treatment_value: Any = 1, n_bootstrap: int = 1000) -> pd.DataFrame:
    """
    Estimates the p-value using bootstrap for two groups.

    Parameters
    -----------
    data (pd.DataFrame): The experimental dataset with the observed outcome.
    unit (str): The experimental unit column.
    outcome (str): The outcome metric column.
    treatment (str): The treatment column.
    treatment_value (Any): The value referencing the treatment (other will be considered as control).
    n_bootstrap (int): The number of random draws with replacement to use.

    Returns
    -----------
    pd.DataFrame: The dataset with the results.

    Raise
    ------------
    ValueException: if there are more than two treatment values.
    """
    responses = data[outcome].values
    mask_tr = (data[treatment] == treatment_value).values
    delta_obs = _compute_delta(responses[mask_tr], responses[~mask_tr])
    deltas_B = _run_bootstrap(data, unit, outcome, n_bootstrap)
    pvalue = _compute_pvalue(delta_obs, deltas_B)
    output_data = pd.DataFrame({
        'delta': [delta_obs],
        'pvalue': [pvalue],
        'n_bootstrap': [n_bootstrap],
        'avg_delta_bootstrap': [np.round(np.mean(deltas_B), 2)],
        'std_delta_bootstrap': [np.round(np.std(deltas_B), 2)]
    })
    return output_data

def _run_bootstrap(data: pd.DataFrame, unit: str, outcome: str, B: int = 1000) -> List[float]:
    """
    Runs the bootstrap method and returns the bootstrapped deltas.

    Parameters
    -----------
    data (pd.DataFrame): The dataframe from which sample with replacement.
    outcome (str): The outcome metric observed in the experiment.
    B (int): The number of random draws with replacement to perfrom.

    Returns
    -----------
    List[float]: The list of bootstrap deltas.
    """
    deltas_bootstrap = []
    for i in tqdm(range(B), desc="Bootstrap Progress"):
        sample_b = _random_draw_with_replacement(data, unit)
        responses_b, mask_tr_b = _optimal_treatment_control_split(sample_b, unit, outcome, seed=i)
        delta_b = _compute_delta(responses_b[mask_tr_b], responses_b[~mask_tr_b])
        deltas_bootstrap.append(delta_b)

    return deltas_bootstrap

def _compute_delta(response_tr, responses_co):
    delta = np.mean(response_tr) - np.mean(responses_co)
    return delta

def _compute_pvalue(obs_delta, bootstrap_deltas):
    count_extreme = sum(1 for delta_b in bootstrap_deltas if abs(delta_b) >= abs(obs_delta))
    p_value = (1 + count_extreme) / (1 + len(bootstrap_deltas))
    return p_value

def _random_draw_with_replacement(data: pd.DataFrame, unit: str):
    sample = data.sample(frac=1, replace=True)
    sample[unit] = range(1, len(sample) + 1)
    return sample

def _optimal_treatment_control_split(data: pd.DataFrame, unit: str, outcome: str, seed: int):
    result = _sample(
        data = data, 
        unit = unit,
        normalized_feature = 'norm_initial_weight',
        seed = seed
    )
    treatment_dict = {inx: gp for gp, indices in result.items() for inx in indices}
    treatment = data[unit].map(treatment_dict)
    mask_tr = (treatment == 'Treatment').values
    responses = data[outcome].values
    return responses, mask_tr

def _sample(data: pd.DataFrame, unit: str, normalized_feature: str, seed: int):
    model = SingleFeatureOptimizationModel(
        data, 
        n_groups = 2, 
        units_per_group = 10, 
        unit = unit, 
        metric = normalized_feature,
    )
    status = model.optimize()
    optimized_groups = model.get_groups_list()
    randomized_groups = random_shuffle_groups(optimized_groups, seed=seed)
    treatment_labels = ["Placebo", "Treatment"]
    return {treatment_labels[i]: randomized_groups[i] for i in range(len(randomized_groups))}
```

```py
infer_result = inference(
    data = experiment_data, 
    unit = 'mice',
    outcome = 'final_weight',
    treatment = 'group',
    treatment_value = 'Treatment',
    n_bootstrap = 1000
)

print(infer_result)
```

```py
>     delta    pvalue  n_bootstrap  avg_delta_bootstrap  std_delta_bootstrap
0 -260.183  0.001998         1000                 2.02               112.61
```

组间观察到的-260毫克的差异在5%的显著性水平下是显著的（p值小于0.05）。因此，我们拒绝零假设（均值相等），并得出结论：治疗具有统计学显著效应。

# **1000只小鼠实验的结果**

我们可以多次模拟实验，生成具有不同初始肿瘤重量的小鼠群体，这些小鼠来自相同的正态分布，均值为200 mg，标准差为300 mg。

这使我们能够将基于优化的设计与其他实验设计进行比较。在下图中，我将优化方法与简单随机分配和分层随机分配进行了比较（其中，分层是使用基于初始肿瘤重量的k-means算法创建的）：

![](../Images/95504d0add5f8dacff5e62d5f7f6d1e5.png)

1000次模拟实验的结果，用于检测-250 mg的效应（图片来源：作者）。

在他们的文章中，作者还将基于优化的方法与重新随机化和配对匹配在不同效应大小和组大小下进行了比较。如果你有兴趣深入探索细节，我强烈推荐阅读完整的文章！

恭喜！你已经阅读完毕🎉如果你觉得这篇文章有趣，可以考虑关注我。我经常分享关于优化和因果推断的想法。
