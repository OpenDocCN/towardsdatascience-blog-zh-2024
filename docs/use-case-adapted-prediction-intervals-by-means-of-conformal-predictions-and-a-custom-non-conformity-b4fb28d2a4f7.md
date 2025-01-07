# 通过保形预测和自定义非保形评分方法调整的预测区间

> 原文：[https://towardsdatascience.com/use-case-adapted-prediction-intervals-by-means-of-conformal-predictions-and-a-custom-non-conformity-b4fb28d2a4f7?source=collection_archive---------3-----------------------#2024-01-24](https://towardsdatascience.com/use-case-adapted-prediction-intervals-by-means-of-conformal-predictions-and-a-custom-non-conformity-b4fb28d2a4f7?source=collection_archive---------3-----------------------#2024-01-24)

## 我应该对机器学习模型在新数据点上的预测有多大的信心？我能否得到一个可能值的范围？

[](https://medium.com/@arnaud.gc.capitaine?source=post_page---byline--b4fb28d2a4f7--------------------------------)[![Arnaud Capitaine](../Images/3d2ef4ffd67289732c79b59c37771b70.png)](https://medium.com/@arnaud.gc.capitaine?source=post_page---byline--b4fb28d2a4f7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4fb28d2a4f7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b4fb28d2a4f7--------------------------------) [Arnaud Capitaine](https://medium.com/@arnaud.gc.capitaine?source=post_page---byline--b4fb28d2a4f7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4fb28d2a4f7--------------------------------) ·9分钟阅读·2024年1月24日

--

![](../Images/4a98c53a6c2c214aed040c14db0e4ced.png)

作者提供的图像

在进行监督任务时，可以使用机器学习模型来预测新样本的结果。然而，新数据点的**预测可能是错误的**。特别是在回归任务中，结果可能取无限多个值时，这一点尤为真实。

为了得到更有洞察力的预测，我们可能更希望（甚至需要）一个预测区间，而不是单一的预测点。**做出充分信息的决策应考虑不确定性**。例如，作为一名房地产投资者，如果预测区间是[100000–10000 ; 100000+10000]，我不会给出与预测区间为[100000–1000 ; 100000+1000]时相同的报价（尽管单点预测是相同的，即100000）。我可能会对第二个区间的单点预测比较信任，但对于第一个区间，我可能会深入研究，因为这个区间相当宽，利润空间也很大，最终价格可能与单点预测有显著差异。

# 预测区间与置信区间

在继续之前，我首先想澄清这两个定义之间的区别。当我开始学习保形预测时，这对我来说并不显而易见。由于我可能不是唯一一个感到困惑的人，所以我希望能提供一些额外的解释。

+   A (1-α) **置信区间** [[1](https://en.wikipedia.org/wiki/Confidence_interval)] 是基于两个统计量，ŝ_{lb} 和 ŝ_{ub}，的区间，具有大于(1-α)的概率包含我们试图估计的实际参数。这里的 θ 是一个参数（不是一个随机变量）。

> ℙ([ŝ_{lb} ; ŝ_{ub}] ∋ θ) ≥ 1-α

+   A (1-α) **预测区间** [[2](https://en.wikipedia.org/wiki/Prediction_interval)] 是基于两个统计量，ŝ_{lb} 和 ŝ_{ub}，的区间，具有以下性质：目标随机变量有大于(1-α)的概率位于这个预测区间内。这里的 Y 是一个随机变量（不是一个参数）。

> ℙ(Y∈[ŝ_{lb} ; ŝ_{ub}]) ≥ (1-α)

让我们考虑一个例子来说明它们的区别。假设有来自母分布 N(μ, σ²) 的一个 n 样本。ŝ 是 σ 的无偏估计量。<Xn> 是 n 样本的均值。我记 q 为 Student 分布（自由度为 n-1）的 1-α/2 分位数（为了简化公式）。

+   μ 的对称 **置信区间** 为：

> [<Xn>-q*ŝ/√(n) ; <Xn>+q*ŝ/√(n)]

+   对于来自同一分布 N(μ, σ²) 的 (n+1)th 随机变量 X(n+1)，其对称的 **预测区间** 为：

> [<Xn>-q*ŝ*√(1+1/n)) ; <Xn>+q*ŝ*√(1+1/n)]

现在我们已经澄清了这些定义，让我们回到我们的目标：设计有见地的预测区间，以做出充分知情的决策。设计预测区间的方法有很多种 [[2](https://en.wikipedia.org/wiki/Prediction_interval)] [[3](https://medium.com/@heinrichpeters/prediction-intervals-in-machine-learning-a2faa36b320c)]。我们将重点关注符合预测 [[4](https://en.wikipedia.org/wiki/Conformal_prediction)]。

# 符合预测

符合预测被引入用来生成具有较弱理论保证的预测区间。它只要求点是可交换的，这比 i.i.d. 假设（独立同分布的随机变量）要弱。对数据分布和模型没有假设。通过将数据分为训练集和校准集，可以得到一个训练好的模型和一些非一致性分数，我们可以用它们来构建新的数据点的预测区间（在可交换性假设成立的前提下，提供理论上的覆盖保证）。

现在让我们考虑一个例子。我想要获得一些关于房价的预测区间。我考虑了来自 OpenML 的房价数据集 [[5](https://www.openml.org/search?type=data&sort=runs&id=42165&status=active)]。我使用了实现符合预测的 MAPIE 库 [[6](https://github.com/scikit-learn-contrib/MAPIE)]。我训练了一个模型（我没有花时间优化它，因为这不是本文的目的）。我展示了测试集的预测点和区间以及实际价格。

有 3 个子图：

第一个图展示了单点预测（蓝色点）以及预测区间（垂直蓝线）与真实值（横坐标上的值）之间的关系。红色对角线是身份线。如果一条垂直线穿过红线，预测区间就包含了实际值，否则则不包含。

第二个图展示了预测区间的宽度。

第三个图展示了全局和局部覆盖率。覆盖率是指落在预测区间内的样本数量与总样本数的比值。全局覆盖率是指测试集所有点的比值。局部覆盖率是指测试集的子集上的比值。这些桶是通过实际价格的分位数创建的。

![](../Images/a40e31c43df8e167081d1baea69df2e1.png)

图片来自作者

我们可以看到，所有预测的预测宽度几乎相同。覆盖率为94%，接近所选的95%。然而，即使全局覆盖率接近理想值，如果我们看一下（我称之为）局部覆盖率（即对于价格几乎相同的数据点的子集的覆盖率），我们会发现**对于昂贵的房屋，覆盖率很差**（根据我的数据集，昂贵的房屋）。相反，对于便宜的房屋，覆盖率较好（根据我的数据集，便宜的房屋）。然而，**对于便宜的房屋，洞察力非常有限**。例如，对于一套便宜的房屋，预测区间可能是[0 ; 180000]，这对做决策帮助不大。

本能上，我希望预测区间的宽度与预测值成正比，以便预测宽度能与预测结果匹配。这就是为什么我查看了其他更适合我的用例的非一致性分数。

# 自定义非一致性分数的符合预测

尽管我不是房地产专家，但对于预测区间，我有一些预期。正如前面所说，我希望它们在某种程度上与预测值成正比。我希望当价格较低时，预测区间较小，而当价格较高时，预测区间较大。

因此，对于这个用例，我将实现两个不符合度得分，这两个得分符合不符合度得分必须满足的条件[[7](https://proceedings.mlr.press/v204/cordier23a/cordier23a.pdf)]（3.1节和附录C）。我从接口*ConformityScore*创建了两个类，该接口要求至少实现两个方法：*get_signed_conformity_scores*和*get_estimation_distribution*。*get_signed_conformity_scores*根据预测值和观察值计算不符合度得分。*get_estimation_distribution*计算估计的分布，然后用于获得预测区间（在提供所选覆盖率后）。我决定将我的第一个不符合度得分命名为*PoissonConformityScore*，因为它与泊松回归直观相关。当考虑泊松回归时，(Y-μ)/√μ具有0均值和方差为1。同样，对于*TweedieConformityScore*类，当考虑Tweedie回归时，(Y-μ)/(μ^(p/2))具有0均值和方差为σ²（假设对所有观察值都是相同的）。在这两个类中，*sym=False*，因为不符合度得分不预期是对称的。此外，*consistency_check=False*，因为我知道这两个方法是一致的，并且满足必要的要求。

```py
import numpy as np

from mapie._machine_precision import EPSILON
from mapie.conformity_scores import ConformityScore
from mapie._typing import ArrayLike, NDArray

class PoissonConformityScore(ConformityScore):
    """
    Poisson conformity score.

    The signed conformity score = (y - y_pred) / y_pred**(1/2).
    The conformity score is not symmetrical.
    y must be positive
    y_pred must be strictly positive

    This is appropriate when the confidence interval is not symmetrical and
    its range depends on the predicted values.
    """

    def __init__(
        self,
    ) -> None:
        super().__init__(sym=False, consistency_check=False, eps=EPSILON)

    def _check_observed_data(
        self,
        y: ArrayLike,
    ) -> None:
        if not self._all_positive(y):
            raise ValueError(
                f"At least one of the observed target is strictly negative "
                f"which is incompatible with {self.__class__.__name__}. "
                "All values must be positive."
            )

    def _check_predicted_data(
        self,
        y_pred: ArrayLike,
    ) -> None:
        if not self._all_strictly_positive(y_pred):
            raise ValueError(
                f"At least one of the predicted target is negative "
                f"which is incompatible with {self.__class__.__name__}. "
                "All values must be strictly positive."
            )

    @staticmethod
    def _all_positive(
        y: ArrayLike,
    ) -> bool:
        return np.all(np.greater_equal(y, 0))

    @staticmethod
    def _all_strictly_positive(
        y: ArrayLike,
    ) -> bool:
        return np.all(np.greater(y, 0))

    def get_signed_conformity_scores(
        self,
        X: ArrayLike,
        y: ArrayLike,
        y_pred: ArrayLike,
    ) -> NDArray:
        """
        Compute the signed conformity scores from the observed values
        and the predicted ones, from the following formula:
        signed conformity score = (y - y_pred) / y_pred**(1/2)
        """
        self._check_observed_data(y)
        self._check_predicted_data(y_pred)
        return np.divide(np.subtract(y, y_pred), np.power(y_pred, 1 / 2))

    def get_estimation_distribution(
        self, X: ArrayLike, y_pred: ArrayLike, conformity_scores: ArrayLike
    ) -> NDArray:
        """
        Compute samples of the estimation distribution from the predicted
        values and the conformity scores, from the following formula:
        signed conformity score = (y - y_pred) / y_pred**(1/2)
        <=> y = y_pred + y_pred**(1/2) * signed conformity score

        ``conformity_scores`` can be either the conformity scores or
        the quantile of the conformity scores.
        """
        self._check_predicted_data(y_pred)
        return np.add(y_pred, np.multiply(np.power(y_pred, 1 / 2), conformity_scores))
```

```py
class TweedieConformityScore(ConformityScore):
    """
    Tweedie conformity score.

    The signed conformity score = (y - y_pred) / y_pred**(p/2).
    The conformity score is not symmetrical.
    y must be positive
    y_pred must be strictly positive

    This is appropriate when the confidence interval is not symmetrical and
    its range depends on the predicted values.
    """

    def __init__(self, p) -> None:
        self.p = p
        super().__init__(sym=False, consistency_check=False, eps=EPSILON)

    def _check_observed_data(
        self,
        y: ArrayLike,
    ) -> None:
        if not self._all_positive(y):
            raise ValueError(
                f"At least one of the observed target is strictly negative "
                f"which is incompatible with {self.__class__.__name__}. "
                "All values must be positive."
            )

    def _check_predicted_data(
        self,
        y_pred: ArrayLike,
    ) -> None:
        if not self._all_strictly_positive(y_pred):
            raise ValueError(
                f"At least one of the predicted target is negative "
                f"which is incompatible with {self.__class__.__name__}. "
                "All values must be strictly positive."
            )

    @staticmethod
    def _all_positive(
        y: ArrayLike,
    ) -> bool:
        return np.all(np.greater_equal(y, 0))

    @staticmethod
    def _all_strictly_positive(
        y: ArrayLike,
    ) -> bool:
        return np.all(np.greater(y, 0))

    def get_signed_conformity_scores(
        self,
        X: ArrayLike,
        y: ArrayLike,
        y_pred: ArrayLike,
    ) -> NDArray:
        """
        Compute the signed conformity scores from the observed values
        and the predicted ones, from the following formula:
        signed conformity score = (y - y_pred) / y_pred**(1/2)
        """
        self._check_observed_data(y)
        self._check_predicted_data(y_pred)
        return np.divide(np.subtract(y, y_pred), np.power(y_pred, self.p / 2))

    def get_estimation_distribution(
        self, X: ArrayLike, y_pred: ArrayLike, conformity_scores: ArrayLike
    ) -> NDArray:
        """
        Compute samples of the estimation distribution from the predicted
        values and the conformity scores, from the following formula:
        signed conformity score = (y - y_pred) / y_pred**(1/2)
        <=> y = y_pred + y_pred**(1/2) * signed conformity score

        ``conformity_scores`` can be either the conformity scores or
        the quantile of the conformity scores.
        """
        self._check_predicted_data(y_pred)
        return np.add(
            y_pred, np.multiply(np.power(y_pred, self.p / 2), conformity_scores)
        )
```

然后，我使用了与之前相同的例子。除了默认的不符合度得分（我在图中将其命名为*AbsoluteConformityScore*）外，我还考虑了这两个额外的不符合度得分。

![](../Images/e4221fa85633ecfec2a22305d8c4bb59.png)

图片来源：作者

如我们所见，全球覆盖率都接近所选的95%。我认为这些小的变化是由于在训练集和测试集之间的随机拆分造成的。然而，预测区间的宽度在不同方法之间存在显著差异，局部覆盖率也是如此。再一次，我不是房地产专家，但我认为对于最后一个不符合度得分（图中的第三列），预测区间更为现实。对于新的两个不符合度得分，预测区间对于廉价房屋来说相当狭窄（尽管略低于95%但覆盖率良好），而对于昂贵房屋则相当宽。这是为了（几乎）达到所选的覆盖率（95%）。**我们来自*TweedieConformityScore*不符合度得分的新预测区间在整个价格范围内具有良好的局部覆盖率，并且更具洞察力，因为预测区间不会不必要地宽泛。**

# 结论

预测区间可能有助于做出更明智的决策。共形预测是一个工具，它可以通过理论覆盖保证和仅有的弱假设（数据可交换性）来构建预测区间。在考虑常用的非一致性得分时，尽管全球覆盖是所需的，但局部覆盖可能会因使用场景的不同而与所选覆盖差异显著。这就是为什么我最终考虑了适应所考虑使用场景的其他非一致性得分。我展示了如何在共形预测库MAPIE中实现这一点，并说明了这样做的好处。一个合适的非一致性得分有助于获得更有洞察力的预测区间（在目标值范围内具有良好的局部覆盖）。

# 参考文献

[置信区间 - 维基百科](https://en.wikipedia.org/wiki/Confidence_interval?source=post_page-----b4fb28d2a4f7--------------------------------)

### 在频率主义统计学中，置信区间（CI）是一个未知参数的估计范围。置信区间……

[预测区间 - 维基百科](https://en.wikipedia.org/wiki/Prediction_interval?source=post_page-----b4fb28d2a4f7--------------------------------)

### 在统计推断中，特别是在预测推断中，预测区间是一个估计的区间……

[机器学习中的预测区间](https://medium.com/@heinrichpeters/prediction-intervals-in-machine-learning-a2faa36b320c?source=post_page-----b4fb28d2a4f7--------------------------------)

### 机器学习模型是强大的工具——但我们如何量化与其相关的不确定性呢……

[共形预测 - 维基百科](https://en.wikipedia.org/wiki/Conformal_prediction?source=post_page-----b4fb28d2a4f7--------------------------------)

### 共形预测（CP）是一个用于不确定性量化的机器学习框架，可以生成预测区间……

[共形预测 - 维基百科](https://en.wikipedia.org/wiki/Conformal_prediction?source=post_page-----b4fb28d2a4f7--------------------------------)

### OpenML是一个开放平台，用于共享数据集、算法和实验——共同学习如何更好地学习。

[www.openml.org](https://www.openml.org/search?type=data&sort=runs&id=42165&status=active&source=post_page-----b4fb28d2a4f7--------------------------------) [](https://github.com/scikit-learn-contrib/MAPIE?source=post_page-----b4fb28d2a4f7--------------------------------) [## GitHub - scikit-learn-contrib/MAPIE: 一个与 scikit-learn 兼容的预测区间估计模块…

### 一个与 scikit-learn 兼容的预测区间估计模块。 - GitHub - scikit-learn-contrib/MAPIE: A…

[github.com](https://github.com/scikit-learn-contrib/MAPIE?source=post_page-----b4fb28d2a4f7--------------------------------)
