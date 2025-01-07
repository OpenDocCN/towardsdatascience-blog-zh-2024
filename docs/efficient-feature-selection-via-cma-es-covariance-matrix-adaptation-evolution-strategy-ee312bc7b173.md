# 通过CMA-ES（协方差矩阵适应进化策略）进行高效特征选择

> 原文：[https://towardsdatascience.com/efficient-feature-selection-via-cma-es-covariance-matrix-adaptation-evolution-strategy-ee312bc7b173?source=collection_archive---------4-----------------------#2024-01-12](https://towardsdatascience.com/efficient-feature-selection-via-cma-es-covariance-matrix-adaptation-evolution-strategy-ee312bc7b173?source=collection_archive---------4-----------------------#2024-01-12)

## *使用进化算法进行大数据集的快速特征选择*

[](https://florin-andrei.medium.com/?source=post_page---byline--ee312bc7b173--------------------------------)[![Florin Andrei](../Images/372ac3e80dbc03cbd20295ec1df5fa6f.png)](https://florin-andrei.medium.com/?source=post_page---byline--ee312bc7b173--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ee312bc7b173--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ee312bc7b173--------------------------------) [Florin Andrei](https://florin-andrei.medium.com/?source=post_page---byline--ee312bc7b173--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ee312bc7b173--------------------------------) ·阅读时间11分钟·2024年1月12日

--

*这是关于特征选择的两部分系列中的第一部分。阅读* [*第二部分在这里*](/efficient-feature-selection-via-genetic-algorithms-d6d3c9aff274)*。*

*想了解更多CMA-ES应用的例子，请查阅* [*Nomura和Shibata的这篇论文*](https://arxiv.org/abs/2402.01373)*；这篇文章在论文中作为通过CMA-ES和Margin优化的一个值得注意的应用被提到（参考文献[6]）。*

当你将模型拟合到数据集时，可能需要进行特征选择：只保留某些特征子集来拟合模型，同时丢弃其余的特征。这可能出于多种原因是必要的：

+   保持模型可解释性（特征过多会使解释变得更困难）

+   避免维度灾难

+   最大化/最小化与模型相关的某个目标函数（如R平方、AIC等）

+   避免过拟合等问题

如果特征的数量N较少，那么进行穷举搜索是可行的：你可以字面上尝试所有可能的特征组合，并保留最小化成本/目标函数的那个组合。但如果N较大，穷举搜索可能就不再可行。需要尝试的组合总数是`2^N`，如果N超过几十个，计算量将变得无法承受——这是一个指数函数。在这种情况下，你必须使用启发式方法：以高效的方式探索搜索空间，寻找能够最小化你用来进行搜索的目标函数的特征组合。

你要找的是一个长度为N的向量`[1, 0, 0, 1, 0, 1, 1, 1, 0, ...]`，其中的元素取值为`{0, 1}`。向量中的每个元素都分配给一个特征；如果元素为1，则选择该特征；如果元素为0，则丢弃该特征。你需要找到最小化目标函数的向量。搜索空间的维度与特征数量N一样多；沿着任何维度的可能值只有0和1。

找到一个好的启发式算法并不是一件简单的事情。R中的`regsubsets()`函数有一些选项可供使用。此外，scikit-learn提供了[几种方法](https://scikit-learn.org/stable/modules/feature_selection.html)来执行启发式特征选择，只要你的问题适合他们的技术。但是找到一个好的、通用的启发式算法——以最一般的形式——是一个困难的问题。在本系列文章中，我们将探讨一些选项，即使N很大，目标函数可以是你可以在代码中计算的任何东西，只要不太慢，可能也能工作得相当好。

# 数据集和完整代码

对于本系列文章中的所有优化技术，我正在使用[在Kaggle上非常流行的房价数据集](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data)（MIT许可证）——经过一些简单的特征转换后，它最终具有213个特征（N=213）和1453个观测值。我使用的模型是线性回归，`statsmodels.api.OLS()`，我试图最小化的目标函数是BIC——贝叶斯信息准则——一个衡量信息损失的指标，因此较低的BIC值更好。它类似于AIC——阿凯克信息准则——但BIC倾向于产生更简洁的模型：它更喜欢具有较少特征的模型。最小化AIC或BIC倾向于减少过拟合。但也可以使用其他目标函数，例如R平方（目标中解释的方差）或调整后的R平方——只需记住，较大的R平方值更好，因此这是一个最大化问题。

最终，这里的目标函数的选择是无关紧要的。重要的是，我们有一个我们一直试图使用各种技术来优化的目标函数。

在本系列文章中使用的完整代码包含在[我的特征选择存储库](https://github.com/FlorinAndrei/fast_feature_selection)中的一个笔记本中——也在末尾链接。我将在这里的文本中提供代码片段，但请查看笔记本获取完整上下文。

我们将尝试通过特征选择来最小化BIC，因此这是在进行任何特征选择之前从`statsmodels.api.OLS()`中得到的BIC的基线值——启用所有特征时：

```py
baseline BIC: 34570.166173470934
```

现在让我们来检查一个众所周知的、经过验证的特征选择技术，我们将与后面描述的更复杂的技术进行比较。

# SFS——顺序特征搜索

SFS，前向版本，相当简单。它从尝试选择单个特征开始，并选择最小化目标函数的特征。一旦选择了一个特征，它将永远保持选择状态。然后尝试添加另一个特征（总共2个特征），以最小化目标。每次增加一个已选择特征的数量，尝试找到最佳的新特征添加到现有集合中。当所有特征一起尝试时，搜索结束。无论哪种组合最小化目标，都获胜。

SFS是一种贪婪算法 — 每个选择都是局部最优的 — 它永远不会回头纠正自己的错误。但即使N相当大时，它也相当快。它尝试的总组合数为`N(N+1)/2`，这是一个二次多项式（而穷举搜索需要执行指数数量的试验）。

让我们看看SFS代码在Python中可能是什么样子，使用[mlxtend库](https://rasbt.github.io/mlxtend/)：

```py
import statsmodels.api as sm
from mlxtend.feature_selection import SequentialFeatureSelector as SFS
from sklearn.base import BaseEstimator

class DummyEstimator(BaseEstimator):
    # mlxtend wants to use an sklearn estimator, which is not needed here
    # (statsmodels OLS is used instead)
    # create a dummy estimator to pacify mlxtend
    def fit(self, X, y=None, **kwargs):
        return self

def neg_bic(m, X, y):
    # objective function
    lin_mod_res = sm.OLS(y, X, hasconst=True).fit()
    return -lin_mod_res.bic

seq_selector = SFS(
    DummyEstimator(),
    k_features=(1, X.shape[1]),
    forward=True,
    floating=False,
    scoring=neg_bic,
    cv=None,
    n_jobs=-1,
    verbose=0,
    # make sure the intercept is not dropped
    fixed_features=['const'],
)

n_features = X.shape[1] - 1
objective_runs_sfs = round(n_features * (n_features + 1) / 2)
t_start_seq = time.time()
# mlxtend will mess with your dataframes if you don't .copy()
seq_res = seq_selector.fit(X.copy(), y.copy())
t_end_seq = time.time()
run_time_seq = t_end_seq - t_start_seq
seq_metrics = seq_res.get_metric_dict()
```

它快速运行组合，以下是摘要结果：

```py
best k:         36
best objective: 33708.98602877906
R2 @ best k:    0.9075677543649224
objective runs: 22791
total run time: 42.448 sec
```

最佳特征数量是213个中的36个。最佳BIC为33708.986（特征选择之前的基准值为34570.166），在我的系统上不到1分钟即可完成。它调用目标函数22800次。

这些是作为特征选择数量函数的最佳BIC和R-squared值：

![](../Images/20e26ad1e5129f0b3912d6f93c631f45.png)

SFS的BIC和R-squared

欲了解更多信息，例如实际选择的特征名称，请查看存储库中的笔记本。

现在让我们尝试一些更复杂的东西。

# CMA-ES（协方差矩阵适应进化策略）

这是一个数值优化算法。它与遗传算法（它们都是进化算法）属于同一类，但CMA-ES与GA有很大不同。该算法是随机的，不需要计算目标函数的导数（不像梯度下降依赖于偏导数）。它在计算上是高效的，并且被用于各种数值优化库，如Optuna。我将在这里尝试对CMA-ES进行简要介绍；更详细的解释，请参考末尾链接部分的文献。

考虑二维Rastrigin函数：

![](../Images/b415948f91befc1f571507180fe93343.png)

Rastrigin函数

下面的热图显示了该函数的值 — 较亮的颜色表示较高的值。该函数在原点（0, 0）处具有全局最小值，但它布满许多局部极值。我们需要通过CMA-ES找到全局最小值。

![](../Images/3ab2749810f8ad107b61626be88b40b2.png)

Rastrigin函数热图

CMA-ES 基于多元正态分布。它从这个分布中生成搜索空间中的测试点。你需要猜测分布的原始均值和标准差，但之后算法会迭代地修改所有这些参数，在搜索空间中扫荡分布，寻找最佳的目标函数值。以下是测试点所来自的原始分布：

![](../Images/96ce2563d3ce0d11d42c6536bc729bbe.png)

CMA-ES分布

`xi` 是算法在每一步生成的点集，位于搜索空间中。`lambda` 是生成的点数。分布的均值将在每一步更新，并且希望最终能收敛到真实解。`sigma` 是分布的标准差——即测试点的分布范围。`C` 是协方差矩阵：它定义了分布的形状。根据`C`的值，分布可能呈“圆形”或更拉长的椭圆形。对`C`的修改使得CMA-ES可以“悄悄”进入搜索空间中的某些区域，或避开其他区域。

![](../Images/eb3ae64a92ba6cb4fec5961083196b0b.png)

测试点的第一次生成。

上图生成了6个点，这是优化器为此问题选择的默认种群大小。这是第一步。之后，算法需要：

+   为每个点计算目标函数（Rastrigin）。

+   更新均值、标准差和协方差矩阵，实际上基于从目标函数中学到的信息，创建一个新的多元正态分布。

+   从新的分布中生成一组新的测试点。

+   重复直到某个条件满足（无论是收敛到某个均值，还是超过最大步数等）。

我在这里不会展示所有分布参数的更新过程，否则这篇文章会变得很长——请查看文末的链接以获得完整的解释。但仅更新分布的均值是相当简单的，具体过程如下：在计算每个测试点的目标函数后，会给点赋予权重，权重较大的点会分配给目标值较好的点，并从这些点的位置计算加权和，作为新的均值。实际上，CMA-ES将分布的均值朝着具有更好目标值的点移动：

![](../Images/4474eeec4e64e343df3764a94a715f94.png)

更新CMA-ES分布的均值

如果算法收敛到真实解，则分布的均值将收敛到该解。标准差将收敛到0。协方差矩阵将根据目标函数的地理特征改变分布的形状（圆形或椭圆形），扩展到有前景的区域，并避开不良区域。

这里是一个动画 GIF，展示了 CMA-ES 在求解 Rastrigin 问题时测试点随时间演变的过程：

![](../Images/c7360fac3216f40f0db4b34caab8535e.png)

动画展示了 CMA-ES 的收敛过程

# CMA-ES 用于特征选择

2D Rastrigin 函数相对简单，因为它只有 2 个维度。而对于我们的特征选择问题，维度是 N=213。此外，空间不是连续的。每个测试点是一个 N 维向量，其组件值来自 `{0, 1}`。换句话说，每个测试点看起来像这样：[1, 0, 0, 1, 1, 1, 0, ...] —— 一个二进制向量。但除此之外，问题是一样的：我们需要找到那些最小化目标函数的点（或向量）：OLS 模型的 BIC 参数。

这是一个用于特征选择的 CMA-ES 代码的简单版本，使用了 [cmaes 库](https://github.com/CyberAgentAILab/cmaes)：

```py
def cma_objective(fs):
    features_use = ['const'] + [
        f for i, f in enumerate(features_select) if fs[i,] == 1
    ]
    lin_mod = sm.OLS(y_cmaes, X_cmaes[features_use], hasconst=True).fit()
    return lin_mod.bic

X_cmaes = X.copy()
y_cmaes = y.copy()
features_select = [f for f in X_cmaes.columns if f != 'const']

dim = len(features_select)
bounds = np.tile([0, 1], (dim, 1))
steps = np.ones(dim)
optimizer = CMAwM(
    mean=np.full(dim, 0.5),
    sigma=1 / 6,
    bounds=bounds,
    steps=steps,
    n_max_resampling=10 * dim,
    seed=0,
)

max_gen = 100
best_objective_cmaes_small = np.inf
best_sol_raw_cmaes_small = None
for gen in tqdm(range(max_gen)):
    solutions = []
    for _ in range(optimizer.population_size):
        x_for_eval, x_for_tell = optimizer.ask()
        value = cma_objective(x_for_eval)
        solutions.append((x_for_tell, value))
        if value < best_objective_cmaes_small:
            best_objective_cmaes_small = value
            best_sol_raw_cmaes_small = x_for_eval
    optimizer.tell(solutions)

best_features_cmaes_small = [
    features_select[i]
    for i, val in enumerate(best_sol_raw_cmaes_small.tolist())
    if val == 1.0
]
print(f'best objective: {best_objective_cmaes_small}')
print(f'best features:  {best_features_cmaes_small}')
```

CMA-ES 优化器使用一些初始猜测值来定义均值和标准差（sigma）。然后它循环多个代，创建测试点 `x_for_eval`，用目标函数评估它们，修改分布（均值、sigma、协方差矩阵）等。每个 `x_for_eval` 点是一个二进制向量 `[1, 1, 1, 0, 0, 1, ...]`，用于从数据集中选择特征。

请注意，使用的是 `CMAwM()` 优化器（带有边际的 CMA），而不是默认的 `CMA()`。默认优化器适用于常规的、连续的问题，但这里的搜索空间是高维的，并且只允许两个离散值（0 和 1）。默认优化器在这个空间中会卡住。`CMAwM()` 略微扩展了搜索空间（尽管它返回的解仍然是二进制向量），这似乎足以解锁它。

这个简单的代码确实有效，但远非最优。在附带的笔记本中，我有一个更复杂、优化过的版本，能够更快地找到更好的解。但由于代码比较大，我不会在这里展示 —— [请查看笔记本](https://github.com/FlorinAndrei/fast_feature_selection)。

下图展示了复杂、优化过的 CMA-ES 代码的历史，寻找最佳解的过程。热力图显示了每一代中特征的流行度（亮色 = 更受欢迎）。你可以看到某些特征始终非常受欢迎，而其他特征很快就过时，还有一些特征则是在后期“被发现”的。优化器选择的人口规模，根据该问题的参数，是 20 个点（个体），因此特征的流行度是在这 20 个点之间进行平均的。

![](../Images/9cec9295c8e62cbf369a4fad1e66e18a.png)

CMA-ES 优化历史

以下是优化后的 CMA-ES 代码的主要统计数据：

```py
best objective:  33703.070530508514
best generation: 921
objective runs:  20000
time to best:    48.326 sec
```

它能够找到比 SFS 更好的（更小的）目标值，调用目标函数的次数更少（20k 次），且所需时间大致相同。这在所有度量标准下都比 SFS 有明显的进展。

再次提醒，任何特征选择前的基准 BIC 是：

```py
baseline BIC: 34570.166173470934
```

旁注：在研究传统优化算法（遗传算法、模拟退火等）后，CMA-ES 给我带来了惊喜。它几乎没有超参数，计算开销轻量，仅需要一个小规模的个体（点）群体来探索搜索空间，然而它的表现相当不错。如果你需要解决优化问题，值得将其加入工具箱中。

*这是关于特征选择的两部分系列的第一部分。阅读* [*第二部分在这里*](/efficient-feature-selection-via-genetic-algorithms-d6d3c9aff274)*。*

# 备注和链接

感谢 [cmaes 团队](https://github.com/CyberAgentAILab/cmaes)的解锁支持，你们的解释真的帮了我大忙！

所有图片均由作者创建。

包含所有代码的代码库：[https://github.com/FlorinAndrei/fast_feature_selection](https://github.com/FlorinAndrei/fast_feature_selection)

房价数据集（MIT 许可证）：[https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data)

mlxtend 库：[https://github.com/rasbt/mlxtend](https://github.com/rasbt/mlxtend)

cmaes 库：[https://github.com/CyberAgentAILab/cmaes](https://github.com/CyberAgentAILab/cmaes)

*cmaes：一个简单但实用的 Python 库，用于 CMA-ES* — 由 Nomura M. 和 Shibata M.（2024）撰写的论文，描述了 CMA-ES 作为优化算法的实际应用：[https://arxiv.org/abs/2402.01373](https://arxiv.org/abs/2402.01373)

*CMA 演化策略：教程* — 由 Hansen N.（2016）撰写的论文，详细描述了 CMA-ES：[https://arxiv.org/abs/1604.00772](https://arxiv.org/abs/1604.00772)

Wikipedia 关于 CMA-ES 的条目: [https://en.wikipedia.org/wiki/CMA-ES](https://en.wikipedia.org/wiki/CMA-ES)
