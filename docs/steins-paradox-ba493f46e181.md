# 斯坦因悖论

> 原文：[https://towardsdatascience.com/steins-paradox-ba493f46e181?source=collection_archive---------3-----------------------#2024-09-30](https://towardsdatascience.com/steins-paradox-ba493f46e181?source=collection_archive---------3-----------------------#2024-09-30)

## 为什么样本均值并不总是最优

[](https://medium.com/@tim.sumner?source=post_page---byline--ba493f46e181--------------------------------)[![Tim Sumner](../Images/34225cf53f510e5002042bb1be00f423.png)](https://medium.com/@tim.sumner?source=post_page---byline--ba493f46e181--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ba493f46e181--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ba493f46e181--------------------------------) [Tim Sumner](https://medium.com/@tim.sumner?source=post_page---byline--ba493f46e181--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ba493f46e181--------------------------------) ·阅读时间：8分钟·2024年9月30日

--

![](../Images/6b655a6b15a187994596266f9ae44958.png)

作者插图

平均值是统计学中最基本的工具之一，仅次于计数。虽然它的简单性可能让人觉得直观，但由于其强大的属性，平均值在许多数学概念中扮演着核心角色。概率论中的重要定理，如大数法则和中心极限定理，都强调了平均值不仅仅是方便——它在估计参数时往往是最优的。核心统计方法，如最大似然估计（MLE）和最小方差无偏估计（MVUE），也进一步证明了这一观点。

然而，这一长期以来的信念在1956年被颠覆[1]，当时查尔斯·斯坦因做出了突破，挑战了超过150年的估计理论。

# 历史

平均值一直被认为是估计随机变量分布的集中趋势的有效方法，特别是在正态分布的情况下。正态（或高斯）分布的特点是钟形曲线和两个关键参数：均值（θ）和标准差（σ）。均值表示曲线的中心，而标准差反映了数据的分布范围。

统计学家经常是倒推，通过观察数据来推断这些参数。高斯证明了样本均值最大化了观察数据的似然性，因此它是一个无偏估计量——这意味着它不会系统地高估或低估真实均值（θ）。

统计理论的进一步发展确认了样本均值的实用性，样本均值在与其他线性无偏估计量比较时，能够最小化预期的平方误差。像 R.A. 费舍尔和杰尔日·内曼这样的研究人员通过引入风险函数扩展了这些思想，风险函数用于衡量不同 θ 值下的平均平方误差。他们发现，尽管均值和中位数的风险是恒定的，但均值始终提供较低的风险，证明了其优越性。

然而，斯坦因定理表明，当同时估计三个或更多参数时，样本均值变得不可接受。在这些情况下，偏倚估计量通过提供更低的整体风险，可以优于样本均值。斯坦因的工作彻底改变了统计推断，提升了多参数估计的准确性。

# 詹姆斯-斯坦因估计量

詹姆斯-斯坦因[2]估计量是查尔斯·斯坦因发现的悖论中的关键工具。它挑战了样本均值总是最佳估计量的观点，尤其是在同时估计多个参数时。詹姆斯-斯坦因估计量的核心思想是将各个样本均值“收缩”向一个中央值（总均值），从而减少整体估计误差。

为了澄清这一点，我们首先考虑一个向量 **x**，它表示多个变量的样本均值（不一定独立）。如果我们将这些均值的平均值取出来，我们得到一个单一的值，记作 *μ*，我们称之为总均值。詹姆斯-斯坦因估计量的工作原理是将每个样本均值向这个总均值靠拢，从而减少它们的方差。

詹姆斯-斯坦因估计量的通用公式[3]是：

![](../Images/b6463074c2b0989a789b4f70b3cd145f.png)

其中：

+   **x** 是样本均值向量。

+   *μ* 是总均值（样本均值的平均值）。

+   *c* 是一个介于 0 和 1 之间的收缩因子。它决定了我们将各个均值拉向总均值的程度。

这里的目标是减少各个样本均值与总均值之间的距离。例如，如果某个样本均值离总均值很远，估计量会将其收缩向中心，从而平滑数据中的变异性。

*c* 的值，即收缩因子，取决于数据和估计的内容。样本均值向量遵循多元正态分布，因此如果我们试图估计的是这个分布，公式变为：

![](../Images/845bc8961fa121fa17e89599bfd7566d.png)

其中：

+   *p* 是正在估计的参数个数（即 **x** 的长度）。

+   *σ*² 是样本均值向量 **x** 的方差。

+   项 (*p* — 2)/**||x||**² 根据数据的方差和参数的个数调整收缩量。

**关键假设和调整**

使用James-Stein估计量的一个关键假设是方差*σ*²对所有变量是相同的，但在实际数据中这一假设通常不成立。然而，可以通过标准化数据来缓解这一假设，使得所有变量具有相同的方差。另一种方法是将各个变量的方差平均化为一个合并估计。这种方法在数据集较大时尤其有效，因为随着样本量的增加，方差差异通常会减小。

一旦数据被标准化或合并，收缩因子就可以应用，以适当调整每个样本均值。

**选择收缩因子**

收缩因子*c*至关重要，因为它控制样本均值被拉向总体均值的程度。*c*接近1表示几乎没有收缩，类似于常规样本均值的行为。相反，*c*接近0表示显著的收缩，几乎将样本均值完全拉向总体均值。

*c*的最优值取决于具体数据和估计的参数，但一般的指导原则是，参数越多（即*p*越大），收缩越有益，因为这减少了对噪声数据的过拟合风险。

**在代码中实现James-Stein估计量**

这里是James-Stein估计量在R、Python和Julia中的函数：

```py
## R ##
james_stein_estimator <- function(Xbar, sigma2 = 1) {
  p <- length(Xbar)
  norm_X2 <- sum(Xbar^2)
  shrinkage_factor <- max(0, 1 - (p - 2) * mean(sigma2) / norm_X2)
  return(shrinkage_factor * Xbar)
}

## Python ##
import numpy as np

def james_stein_estimator(Xbar, sigma2=1):
    p = len(Xbar)
    norm_X2 = np.sum(Xbar**2)
    shrinkage_factor = max(0, 1 - (p - 2) * np.mean(sigma2) / norm_X2)
    return shrinkage_factor * Xbar

## Julia ##
function james_stein_estimator(Xbar, sigma2=1)
    p = length(Xbar)
    norm_X2 = sum(Xbar.^2)
    shrinkage_factor = max(0, 1 - (p - 2) * mean(sigma2) / norm_X2)
    return shrinkage_factor * Xbar
end
```

# 示例

为了展示这一技术的多样性，我将生成一个6维的数据集，每一列都包含来自不同随机分布的数值数据。以下是我将使用的每个分布及其参数：

*X1 ~ t分布*（ν = 3） *X2 ~ 二项分布*（*n* = 10, *p* = 0.4） *X3 ~ Gamma分布*（*α =* 3, *β =* 2） *X4 ~ 均匀分布*（*a =* 0, *b* = 1） *X5 ~ 指数分布*（*λ* = 50） *X6 ~ 泊松分布*（*λ* = 2）

请注意，本数据集中的每一列都包含独立变量，即每一列之间不应存在相关性，因为它们是独立创建的。这并不是使用此方法的必要条件，仅仅是为了简化操作并展示此结果的悖论性质。如果你不完全熟悉这些分布中的任何一个或全部，我会附上一张简单的图，展示每个单变量列的随机生成数据。这仅仅是从上述每个分布中生成的1,000个随机变量中的一次迭代。

![](../Images/9f6c629c812641ed53f6c49971e0cd10.png)

从上面的直方图中应该可以清楚地看到，并非所有这些变量都遵循正态分布，这意味着整个数据集不是多元正态分布。

由于每个分布的真实情况已知，我们知道每个分布的真实平均值。该多变量数据集的平均值可以以向量形式表示，每行条目代表相应变量的平均值。在此示例中，

![](../Images/fb5b2428002e82d4f2bef3b8b086024e.png)

知道每个变量的真实平均值将帮助我们衡量样本均值或詹姆斯·斯坦估计器的接近程度，这意味着接近真实值越好。以下是我在R代码中进行的实验，该实验生成了6个随机变量，并使用均方误差（Mean Squared Error）与真实平均值进行比较。该实验随后进行了10,000次，使用了四种不同的样本大小：5、50、500和5,000。

```py
set.seed(42)
## Function to calculate Mean Squared Error ##
mse <- function(x, true_value)
  return( mean( (x - true_value)^2 ) )
## True Average ##
mu <- c(0, 4, 1.5, 0.5, 0.02, 2)
## Store Average and J.S. Estimator Errors ##
Xbar.MSE <- list(); JS.MSE <- list()
for(n in c(5, 50, 500, 5000)){ # Testing sample sizes of 5, 30, 200, and 5,000
  for(i in 1:1e4){ # Performing 10,000 iterations

    ## Six Random Variables ##
    X1 <- rt(n, df = 3)
    X2 <- rbinom(n, size = 10, prob = 0.4)
    X3 <- rgamma(n, shape = 3, rate = 2)
    X4 <- runif(n)
    X5 <- rexp(n, rate = 50)
    X6 <- rpois(n, lambda = 2)

    X <- cbind(X1, X2, X3, X4, X5, X6)

    ## Estimating Std. Dev. of Each and Standardizing Data ##
    sigma <- apply(X, MARGIN = 2, FUN = sd)

    ## Sample Mean ##
    Xbar <- colMeans(X)

    ## J.S. Estimator ##
    JS.Xbar <- james_stein_estimator(Xbar=Xbar, sigma2=sigma/n)

    Xbar.MSE[[as.character(n)]][i] <- mse(Xbar, mu)
    JS.MSE[[as.character(n)]][i] <- mse(JS.Xbar, mu)

  }
}
sapply(Xbar.MSE, mean) # Avg. Sample Mean MSE
sapply(JS.MSE, mean) # Avg. James-Stein MSE
```

从所有40,000次试验中，通过运行最后两行代码计算了每个样本大小的总平均MSE。每个结果可以在下表中看到。

![](../Images/b3f8fa5c9d6aece3d817aabd2c0191eb.png)

这次模拟的结果显示，詹姆斯·斯坦估计器在使用MSE时始终优于样本均值，但这种差异随着样本大小的增加而减小。

# **结论**

詹姆斯·斯坦估计器展示了一个估计的悖论：通过结合看似独立的变量的信息，竟然能够改善估计。虽然在样本量较大时，MSE的差异可能微不足道，但这一结果在首次提出时引发了广泛的争议。这一发现标志着统计理论的一个关键转折点，并且在今天的多参数估计中仍然具有重要意义。

如果你想进一步探索，请查看[关于斯坦因悖论的这篇详细文章](https://joe-antognini.github.io/machine-learning/steins-paradox)以及写作本文档时引用的其他资料。

# 参考文献

[1] Stein, C. (1956). [**多元正态分布均值的常用估计量不可接受性**](https://www.degruyter.com/document/doi/10.1525/9780520313880-018/html)。*第三届伯克利数学统计与概率研讨会论文集*，1，197–206。

[2] Stein, C. (1961). [**带有二次损失的估计**](https://link.springer.com/chapter/10.1007/978-1-4612-0919-5_30)。*在S. S. Gupta & J. O. Berger（编），统计决策理论与相关话题*（第1卷，第361–379页）。学术出版社。

[3] Efron, B., & Morris, C. (1977). [**斯坦因悖论在统计学中的应用**](https://link.springer.com/chapter/10.1007/978-0-387-75692-9_7)。*科学美国人*，236(5)，119–127
