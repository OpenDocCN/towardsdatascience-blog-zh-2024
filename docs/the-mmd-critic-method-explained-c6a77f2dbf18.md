# MMD-Critic 方法解析

> 原文：[`towardsdatascience.com/the-mmd-critic-method-explained-c6a77f2dbf18?source=collection_archive---------7-----------------------#2024-08-27`](https://towardsdatascience.com/the-mmd-critic-method-explained-c6a77f2dbf18?source=collection_archive---------7-----------------------#2024-08-27)

## 一种强大但鲜为人知的数据总结和可解释性 AI 方法

[](https://medium.com/@physboom?source=post_page---byline--c6a77f2dbf18--------------------------------)![Matthew Chak](https://medium.com/@physboom?source=post_page---byline--c6a77f2dbf18--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c6a77f2dbf18--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6a77f2dbf18--------------------------------) [Matthew Chak](https://medium.com/@physboom?source=post_page---byline--c6a77f2dbf18--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6a77f2dbf18--------------------------------) ·11 分钟阅读·2024 年 8 月 27 日

--

尽管 MMD-Critic 方法是一个强大的数据总结工具，但它的使用和“覆盖面”却出奇地少。也许这是因为存在一些更简单且更成熟的数据总结方法（例如 K-medoids，参见 [1] 或更简单地，查看 [维基百科页面](https://en.wikipedia.org/wiki/K-medoids)），又或者是因为在此之前并没有 Python 包支持该方法（直到现在）。无论如何，[原始论文](https://papers.nips.cc/paper_files/paper/2016/hash/5680522b8e2bb01943234bce7bf84534-Abstract.html) [2] 中展示的结果比 MMD-Critic 当前的应用更值得广泛使用。因此，我将在这里尽可能清晰地解释 MMD-Critic 方法。我还发布了一个 [开源 Python 包](https://pypi.org/project/mmd-critic/)，其中实现了该技术，方便你轻松使用。

# 原型和批评

在深入探讨 MMD-Critic 方法之前，值得讨论一下我们究竟想要实现什么目标。最终，我们希望通过一个数据集，找到代表性的数据示例（**原型**），以及可能干扰我们机器学习模型的边缘案例（**批评**）。

![](img/71c22de7c81736578e26d81da3a21ce2.png)

来自 [2] 的 MNIST 数据集的原型和批评示例。

这种方法可能有很多有用的地方：

+   通过查看典型和非典型的示例，我们可以获得对数据集的一个非常好的总结视图

+   我们可以通过测试模型在批评样本上的表现，来了解它们如何处理边缘案例（显而易见，这是非常重要的）

+   虽然可能没有那么实用，我们可以使用原型来创建一个类似 K-means 的自然可解释算法，其中最接近新数据点的原型被用来标记该数据点。然后解释就很简单，因为我们只需要向用户展示最相似的数据点。

+   更多

你可以在[这本书](https://f0nzie.github.io/interpretable_ml-rsuite/proto.html#examples-5)的第 6.3 节中找到更多关于该应用的信息（以及关于 MMD-Critic 的一个不错的解释），但可以简单地说，找到这些例子在很多方面都是有用的。MMD-Critic 使我们能够做到这一点。

# 最大均值差异

不幸的是，我不能声称对最大均值差异（MMD）有超严格的理解，因为这种理解需要有较强的泛函分析背景。如果你有这样的背景，你可以在[这里](https://jmlr.csail.mit.edu/papers/volume13/gretton12a/gretton12a.pdf)找到介绍该度量的论文。

简单来说，MMD 是一种确定两个概率分布之间差异的方法。正式地，对于两个概率分布*P*和*Q*，我们定义它们的 MMD 为

![](img/806f69f4f5fb7bdfddc1e2b7e198f203.png)

两个分布 P，Q 的 MMD 公式

这里，*F*是任何**函数空间**——即，具有相同定义域和陪域的函数集合。还要注意，符号*x~P*意味着我们将*x*视为从分布*P*中抽取的随机变量——即，*x*是**由** *P*描述的。因此，这个公式找到当*X*和*Y*通过我们空间*F*中的某个函数变换时，它们期望值之间的最大差异。

这可能有点难以理解，但这是一个例子。假设*X*是`Uniform(0, 1)`（即从 0 到 1 随机选择一个数的分布），而*Y*是`Uniform(-1, 1)`。我们还假设*F*是一个包含三个函数的简单函数族——*f(x) = 0*，*f(x) = x*，和*f(x) = x²*。在我们的空间中对每个函数进行迭代，得到：

1.  在*f(x)* = 0 的情况下，当*x ~ P*时，E[f(x)]为 0，因为无论选择什么*x*，*f(x)*都会是 0。对于*x ~ Q*的情况也一样。因此，我们得到一个均值差异为 0。

1.  在*f(x) = x*的情况下，我们有 E[f(x)] = 0.5（对于 P 的情况）和 0（对于 Q 的情况），因此我们的均值差异为 0.5。

1.  在*f(x) = x²*的情况下，我们注意到

![](img/ea72888c6295343ec4a233c883265a0a.png)

变换函数 f 作用下随机变量*x*的期望值公式

因此在 P 的情况下，我们得到

![](img/f96988df2e03471d79f8585d36fc3596.png)

在分布 P 下，f(x)的期望值

在 Q 的情况下，我们得到

![](img/25a4e021d3c2c5b36bcaaa3c774de110.png)

在分布 Q 下，f(x)的期望值

因此，在这种情况下我们的差异也是 0。我们函数空间上的上确界为 0.5，所以这就是我们的 MMD。

现在你可能会注意到我们 MMD 的一些问题。它似乎高度依赖于我们选择的函数空间，并且在大型或无限函数空间中计算起来也显得非常昂贵（甚至是不可能的）。不仅如此，它还要求我们知道分布*P*和*Q*，这是不现实的。

后一个问题很容易解决，因为我们可以重写我们的 MMD 度量，利用基于数据集的 P 和 Q 的估计：

![](img/2140a40093f3dd0d76850321ceb07100.png)

使用 P 和 Q 的估计的 MMD

这里，我们的*x*是从数据集中抽样得到的*P*的样本，而*y*是从*Q*中抽取的样本。

前两个问题可以通过一些额外的数学手段来解决。无需过多细节，事实证明，如果*F*是一个叫做**再生核希尔伯特空间**（RKHS）的东西，我们就能预先知道哪个函数将给我们提供 MMD。即，它是下面这个函数，称为**见证函数**：

![](img/e46b5d340b5f3db6a69efe75e2b8cc47.png)

我们在 RKHS 中的最优 f(x)

其中*k*是与 RKHS 相关的**核函数**（内积）***¹***。直观地说，这个函数在点*x*处“见证”了*P*和*Q*之间的差异。

因此，我们只需要选择一个足够具表现力的 RKHS/核函数——通常使用 RBF 核，其核函数为：

![](img/f5ea568dd5266a59359ef3961d188f9c.png)

RBF 核，其中 sigma 是一个超参数

这通常能得到相当直观的结果。例如，这里是使用 RBF 核估计的见证函数的图（以与之前相同的方式进行估计——即用求和替代期望）在从`Uniform(-0.5, 0.5)`和`Uniform(-1, 1)`两个数据集抽样时的图示：

![](img/fd302a4cc896b43064206d874d7409da.png)

在不同点上，见证函数对于两个均匀分布的值

生成上述图形的代码如下：

```py
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

def rbf(v1, v2, sigma=0.5):
  return np.exp(-(v2 - v1) ** 2/(2 * sigma**0.5))

def comp_wit_fn(x, d1, d2):
  return 1/len(d1) * sum([rbf(x, dp) for dp in d1]) - 1/len(d2) * sum([rbf(x, dp) for dp in d2])

low1, high1 = -0.5, 0.5  # Range for the first uniform distribution
low2, high2 = -1, 1  # Range for the second uniform distribution

# Generate data for the uniform distributions
data1 = np.random.uniform(low1, high1, 10000)
data2 = np.random.uniform(low2, high2, 10000)

# Generate a range of x values for which to compute comp_wit_fn
x_values = np.linspace(min(low1 * 2, low2 * 2), max(high1 * 2, high2 * 2), 100)

comp_wit_values = [comp_wit_fn(x, data1, data2) for x in x_values]
sns.kdeplot(data1, label=f'Uniform({low1}, {high1})', color='blue', fill=True)
sns.kdeplot(data2, label=f'Uniform({low2}, {high2})', color='red', fill=True)
plt.plot(x_values, comp_wit_values, label='Witness Function', color='green')

plt.xlabel('Value')
plt.ylabel('Density / Wit Fn')
plt.legend()
plt.show()
```

# 最后的 MMD-Critic 方法

MMD-Critic 背后的思路现在相当简单——如果我们想找到*k*个原型，我们需要找到最能匹配原始数据集分布的原型集，通过它们的平方 MMD。换句话说，我们希望找到一个子集*P*，它的基数是*k*，并且最小化*MMD²(F, X, P)*。无需过多细节，平方 MMD 由以下公式给出：

![](img/0dff52d10b914ee9e6082e9b47cb6292.png)

平方 MMD 度量，假设 X ~ P，Y ~ Q，k 是我们 RKHS F 的核函数

在找到这些原型之后，我们接着选择那些假设原型分布与数据集分布在最不同的点作为批评点。正如我们之前所见，两个分布在某一点的差异可以通过我们的见证函数来衡量，所以我们只需要在*X*和*P*的上下文中找到最大化其绝对值的点。换句话说，我们将批评“得分”定义为：

![](img/d19b02d21f693952776695457da054ee.png)

一个批评 c 的“得分”

或者，以更易用的近似形式，

![](img/2a763ae077e95c0041dba13b5c4fab86.png)

对于批评 c 的近似 S(c)

然后，为了找到我们想要的*批评*数量，假设是*m*个，我们只需要找到大小为*m*的集合*C*，使得它能够最大化

![](img/8eacec3442f5cc088cb6704f587a7190.png)

为了促进选择更多样化的批评，论文还建议添加一个正则化项，鼓励选中的批评尽可能分开。论文中建议的正则化项是对数行列式正则化项，尽管这不是必须的。我在这里不会详细讲解，因为它不是关键内容，但论文建议参考[6]***²***。

因此，我们可以实现一个***极其*天真的**MMD-Critic，没有批评正则化，如下所示（**不要使用这个**）：

```py
import math
import itertools

def euc_distance(p1, p2):
    return math.sqrt(sum((x - y) ** 2 for x, y in zip(p1, p2)))

def rbf(v1, v2, sigma=0.5):
  return math.exp(-euc_distance(v1, v2) ** 2/(2 * sigma**0.5))

def mmd_sq(X, Y, sigma=0.5):
  sm_xx = 0
  for x in X:
    for x2 in X:
      sm_xx += rbf(x, x2, sigma)

  sm_xy = 0
  for x in X:
    for y in Y:
      sm_xy += rbf(x, y, sigma)

  sm_yy = 0
  for y in Y:
    for y2 in Y:
      sm_yy += rbf(y, y2, sigma)

  return 1/(len(X) ** 2) * sm_xx \
          - 2/(len(X) * len(Y)) * sm_xy \
          + 1/(len(Y) ** 2) * sm_yy

def select_protos(X, n, sigma=0.5):
  min_score, min_sub = math.inf, None
  for subset in itertools.combinations(X, n):
    new_mmd = mmd_sq(X, subset, sigma)
    if new_mmd < min_score:
      min_score = new_mmd
      min_sub = subset
  return min_sub

def criticism_score(criticism, prototypes, X, sigma=0.5):
  return abs(1/len(X) * sum([rbf(criticism, x, sigma) for x in X])\
             - 1/len(prototypes) * sum([rbf(criticism, p, sigma) for p in prototypes]))

def select_criticisms(X, P, n, sigma=0.5):
  candidates = [c for c in X if c not in P]
  max_score, crits = -math.inf, []
  for subset in itertools.combinations(candidates, n):
    new_score = sum([criticism_score(c, P, X, sigma) for c in subset])
    if new_score > max_score:
      max_score = new_score
      crits = subset

  return crits
```

# 优化 MMD-Critic

上面的实现如此不实用，以至于当我运行时，未能在一个包含 25 个数据点的数据集中，在合理的时间内找到 5 个原型。这是因为我们的 MMD 计算是*O(max(|X|, |Y|)²)*，并且遍历每个长度为 n 的子集的时间复杂度是*O(C(|X|, n))*（其中 C 是组合函数），这导致了非常可怕的运行时复杂度。

忽略使用更高效的计算方法（例如，使用纯 numpy/numexpr/矩阵计算而不是循环/其他）以及缓存重复计算，我们在理论层面上可以进行一些优化。首先，最明显的性能瓶颈是我们在原型和批评方法中对*C(|X|, n)*子集的循环。我们可以用一个近似方法来代替，循环*n*次，每次贪婪地选择最优原型。这样，我们可以将原型选择代码更改为

```py
def select_protos(X, n, sigma=0.5):
  protos = []
  for _ in range(n):
    min_score, min_proto = math.inf, None
    for cand in X:
      if cand in protos:
        continue
      new_score = mmd_sq(X, protos + [cand], sigma)
      if new_score < min_score:
        min_score = new_score
        min_proto = cand
    protos.append(min_proto)
  return protos
```

批评的情况也是类似的。

还有一个重要的引理，使得这个问题更易于优化。事实证明，通过将原型选择问题转化为最小化问题，并向成本中添加一个正则化项，我们可以通过矩阵运算高效地计算成本函数。我不会在这里详细讨论，但你可以查看原始论文了解详情。

# 使用`MMD-Critic`包进行实验

现在我们已经理解了 MMD-Critic 方法，终于可以开始试验它了！你可以通过运行以下命令来安装它

```py
pip install mmd-critic
```

包中的实现比这里展示的要快得多，所以不必担心。

我们可以使用如下方式运行一个简单的示例，使用斑点数据：

```py
from sklearn.datasets import make_blobs
from mmd_critic import MMDCritic
from mmd_critic.kernels import RBFKernel

n_samples = 50  # Total number of samples
centers = 4       # Number of clusters
cluster_std = 1 # Standard deviation of the clusters

X, _ = make_blobs(n_samples=n_samples, centers=centers, cluster_std=cluster_std, n_features=2, random_state=42)
X = X.tolist()

# MMD critic with the kernel used for the prototypes being an RBF with sigma=1,
# for the criticisms one with sigma=0.025
critic = MMDCritic(X, RBFKernel(1), RBFKernel(0.025))
protos, _ = critic.select_prototypes(centers)
criticisms, _ = critic.select_criticisms(10, protos)
```

然后绘制点和批评结果得到

![](img/ad6879f3f0b021cff94f57e71b28503f.png)

绘制找到的原型（绿色）和批评（红色）

你会注意到我提供了使用单独内核进行原型和批评选择的选项。这是因为我发现，批评的结果尤其对 sigma 超参数*极其*敏感。这是 MMD Critic 方法以及核方法的一项不幸限制。总体而言，我发现使用较大的 sigma 值选择原型，较小的 sigma 值选择批评，效果较好。

我们当然也可以使用更复杂的数据集。例如，这里是该方法在 MNIST 上的应用***³***：

```py
from sklearn.datasets import fetch_openml
import numpy as np
from mmd_critic import MMDCritic
from mmd_critic.kernels import RBFKernel

# Load MNIST data
mnist = fetch_openml('mnist_784', version=1)
images = (mnist['data'].astype(np.float32)).to_numpy() / 255.0
labels = mnist['target'].astype(np.int64)

critic = MMDCritic(images[:15000], RBFKernel(2.5), RBFKernel(0.025))
protos, _ = critic.select_prototypes(40)
criticisms, _ = critic.select_criticisms(40, protos)
```

这为我们提供了以下原型

![](img/6b5e036ce193ebc12d8db963cca7fcfb.png)

MMD Critic 方法为 MNIST 找到的原型。MNIST 在 GPL-3.0 许可协议下可以免费用于商业用途。

以及批评意见

![](img/37ca1ea60129a1dc22e293b08e4442e7.png)

由 MMD Critic 方法发现的批评意见

相当不错吧？

# 结论

这就是 MMD-Critic 方法的全部内容。核心非常简单，使用起来不错，除了需要调整 Sigma 超参数。我希望新发布的 Python 包能带来更多的应用。

*如有任何疑问，请联系 mchak@calpoly.edu。所有图片均由作者提供，除非另有说明。*

## 脚注

[1] 如果你曾经学习过 SVM 和核技巧，你可能会熟悉 RKHS 和核——在这些方法中使用的核实际上只是*某些*RKHS 中的内积。最常见的是 RBF 核，其相关的 RKHS 函数是一个无限维的平滑函数集。

[2] 我没有详细阅读此来源，只是简单浏览了一下。它似乎大部分无关紧要，而且对数行列式正则化项的实现相对简单。不过，如果你想阅读它，尽管去看。

[3] 出于法律原因，你可以在[这里](https://github.com/sharmaroshan/MNIST-Dataset/tree/master)找到包含 MNIST 数据集的代码库。该数据集在 GPL-3.0 许可协议下可以免费用于商业用途。

## 参考文献

[1] [`onlinelibrary.wiley.com/doi/book/10.1002/9780470316801`](https://onlinelibrary.wiley.com/doi/book/10.1002/9780470316801)

[2][`proceedings.neurips.cc/paper_files/paper/2016/file/5680522b8e2bb01943234bce7bf84534-Paper.pdf`](https://papers.nips.cc/paper_files/paper/2016/hash/5680522b8e2bb01943234bce7bf84534-Abstract.html)

[3] [`f0nzie.github.io/interpretable_ml-rsuite/proto.html#examples-5`](https://f0nzie.github.io/interpretable_ml-rsuite/proto.html#examples-5)

[4] [`jmlr.csail.mit.edu/papers/volume13/gretton12a/gretton12a.pdf`](https://jmlr.csail.mit.edu/papers/volume13/gretton12a/gretton12a.pdf)

[5] [`www.stat.cmu.edu/~ryantibs/journalclub/mmd.pdf`](https://www.stat.cmu.edu/~ryantibs/journalclub/mmd.pdf)

[6] [`jmlr.org/papers/volume9/krause08a/krause08a.pdf`](https://jmlr.org/papers/volume9/krause08a/krause08a.pdf)
