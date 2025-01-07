# 德国坦克问题

> 原文：[https://towardsdatascience.com/the-german-tank-problem-3455237aaf43?source=collection_archive---------2-----------------------#2024-03-06](https://towardsdatascience.com/the-german-tank-problem-3455237aaf43?source=collection_archive---------2-----------------------#2024-03-06)

## 通过抽样估计中奖概率

[](https://medium.com/@doriandrost?source=post_page---byline--3455237aaf43--------------------------------)[![Dorian Drost](../Images/1795395ad0586eafd83d3e2f7b975ca8.png)](https://medium.com/@doriandrost?source=post_page---byline--3455237aaf43--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3455237aaf43--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3455237aaf43--------------------------------) [Dorian Drost](https://medium.com/@doriandrost?source=post_page---byline--3455237aaf43--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3455237aaf43--------------------------------) ·阅读时间 11 分钟·2024年3月6日

--

统计估计真的很有趣，不是吗？通过从一个总体中随机抽取几个样本，你可以推断出这个总体的一些特性，比如均值或方差。同样，在合适的条件下，你也可以估计总体的**总量**，正如我在本文中将要展示的那样。

我将使用抽奖的例子来估计彩票的总数，从而计算中奖的概率。更正式地说，这意味着在给定离散均匀分布的情况下估计总体规模。我们将看到不同的估计方法，并讨论它们的差异和局限性。此外，我还会向你介绍这种方法可以应用于其他的一些案例。

## 玩彩票

![](../Images/3a2591c798b46eb0e58bb2b309b57953.png)

我太焦虑，不敢乘坐那种东西，但如果你愿意的话…照片由 [Oneisha Lee](https://unsplash.com/@shapeyourbliss?utm_source=medium&utm_medium=referral) 提供，拍摄于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

假设我去参加一个州际博览会并购买彩票。作为一名数据科学家，我自然想知道赢得大奖的概率。假设只有一张彩票能够赢得大奖。那么，为了估计中奖的可能性，我需要知道彩票的总数N，因为我的中奖机会就是1/N（如果我买了k张彩票，那就是k/N）。但如果我只买了几张彩票（而且都没有中奖），我怎么估计这个N呢？

我将利用彩票上的号码这一事实，并假设这些号码是连续的（这意味着，我假设它们符合离散均匀分布）。假设我已经购买了一些彩票，它们按顺序的号码是[242,412,823,1429,1702]。那么，我现在知道的彩票总数是多少呢？显然，至少有1702张彩票（因为这是我到目前为止看到的最大号码）。这给了我彩票总数的第一个下限，但这对实际彩票总数的准确性有多大帮助呢？仅仅因为我抽到的最高号码是1702，并不意味着还有比它更大的号码。我很难遇到我样本中最高号码的那张彩票。

然而，我们可以从数据中获得更多信息。让我们考虑以下思路：如果我们知道所有彩票的中间号码，我们就可以很容易地从中推算出总数量：如果中间号码是*m*，那么在这个中间号码下面有*m-1*张彩票，在这个中间号码上面也有*m-1*张彩票。也就是说，总彩票数将是*(m-1) + (m-1) + 1*，（其中的*+1*是指中间号码m本身），等于*2m-1*。我们虽然不知道中间号码m，但可以通过我们样本的平均值或中位数来估算它。我的样本的（四舍五入后的）平均值是922，这就得出了2*922-1 = 1843。因此，通过这个计算，估算出的彩票总数是1843。

到目前为止，这个过程相当有趣，因为仅凭几个彩票号码，我就能够估算出彩票的总数量。然而，你可能会想，这是不是我们能得到的最佳估计。让我马上告诉你：并不是。

我们使用的方法有一些缺点。让我通过另一个例子来演示这一点：假设我们有号码[12,30,88]，这导致我们得到2*43–1 = 85。这意味着，公式表明总共有85张彩票。然而，我们的样本中有号码88，所以这个估计根本不成立！这个方法有一个普遍的问题：估算的N可能会低于样本中的最大号码。在这种情况下，这个估计就没有任何意义，因为我们已经知道样本中的最大号码是整体N的自然下限。

## 一种更好的方法：使用均匀间隔

![](../Images/60ee63bed681c8bf2a8a74cfe01c2319.png)

这些鸟在电线上间隔得相当均匀，这是我们下一个方法的一个重要概念。照片由[Ridham Nagralawala](https://unsplash.com/@ridham?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

好的，那么我们能做什么呢？让我们换个角度思考。我购买的福利彩票票是从一个从 1 到未知的 N 的分布中随机抽取的。我的票中最大的数字是 1702，我想知道，这个数字离成为最大票还有多远。换句话说，1702 和 N 之间的差距是多少？如果我知道这个差距，我就可以轻松地根据它计算出 N。那么，我对这个差距了解多少呢？好吧，我有理由假设，这个差距预期和样本中两个连续票之间的其他差距一样大。第一张票和第二张票之间的差距，平均来说，应该和第二张票和第三张票之间的差距一样大，依此类推。除了随机偏差外，没有理由认为这些差距会比其他的更大或更小。我独立抽取了我的福利彩票票，因此它们应该均匀分布在所有可能的票号范围内。平均来说，0 到 N 范围内的数字看起来就像电线上停的鸟儿，它们之间的间隔相等。

这意味着我预计 N-1702 等于所有其他间隔的平均值。其他间隔为 242–0=242，412–242=170，823–412=411，1429–823=606，1702–1429=273，得出平均值 340。因此，我估计 N 为 1702+340=2042。简而言之，这可以用以下公式表示：

![](../Images/02570dd48cf176f3be391c20b40f0e17.png)

这里，*x* 是观察到的最大数字（在我们的例子中是 1702），*k* 是样本数量（在我们的例子中是 5）。这只是我们刚才计算平均值的一种简短形式。

## 让我们做一个模拟

我们刚才看到了两种对总彩票数量的估算。首先，我们计算了 *2*m-1*，得到了 1843；然后我们使用了更复杂的方法 *x + (x-k)/k*，得到了 2042。我现在想知道，哪个估算更准确呢？我的中奖概率是 1/1843 还是 1/2042？

为了展示我们刚才使用的估算方法的一些性质，我做了一个模拟。我从一个最大值为 2000 的分布中抽取了不同大小 k 的样本，并重复了几百次。因此，我们可以预期，至少在平均值上，我们的估算值也应该接近 2000。这是模拟的结果：

![](../Images/fd0f5750fe18415a8727f13c5106914b.png)

对于不同的估算值，随着 k 的变化，其概率密度分布。请注意，真实值 N 是 2000。图像来源：作者。

我们在这里看到了什么？在横轴上，我们看到 k，即我们抽取的样本数。对于每个 k，我们可以看到基于几百次模拟的两个公式的估算值分布。黑色的点表示每个模拟的均值，这个均值总是 2000，无论 k 的值是多少。这个点非常有意思：如果这两种估算方法重复无数次，它们都会收敛到正确的值。

然而，除了常规的平均值外，这些分布的差异还是挺大的。我们可以看到，公式 *2*m-1* 的方差较大，也就是说，它的估计值比另一个公式更常偏离真实值。尽管如此，方差随着k的增大有降低的趋势。不过，这种下降并不总是完美的，因为这只是一个模拟，仍然受到随机因素的影响。然而，这种趋势是可以理解并且是预期的：我取的样本越多，我的估计就越精确。这是统计估计中非常常见的性质。

我们还看到偏差是对称的，也就是说，低估真实值的可能性和高估真实值的可能性是一样的。对于第二种方法，这种对称性并不成立：虽然大部分密度位于真实均值上方，但下方有更多且更大的离群值。为什么会这样呢？让我们回顾一下我们是如何计算这个估计的。我们取了样本中的最大值，并在其基础上加上了平均间隙大小。自然，样本中的最大值只能和总体中最大的值（我们想要估计的N）一样大。在这种情况下，我们将平均间隙大小加到N上，但我们无法得到比N更大的估计值。在另一个方向，最大值可能非常小。如果我们运气不好，可能会抽到样本[1,2,3,4,5]，在这种情况下，样本中的最大值（5）离实际的N非常远。这就是为什么低估真实值的偏差可能比高估真实值的偏差更大的原因。

## 哪个更好？

从我们刚刚看到的情况来看，现在哪个估计更好呢？嗯，两个估计平均来说都给出了正确的值。然而，公式 *x + (x-k)/k* 的方差较小，这是一个很大的优势。这意味着，你更常接近真实值。让我给你演示一下。在接下来的图中，你将看到两个估计在样本大小 *k=5* 时的概率密度图。

![](../Images/54497bfc9bf4dd95c6320bf84619f8c4.png)

对于k=5的两个估计的概率密度。曲线下方的彩色区域覆盖了从N=1750到N=2250的范围。图片由作者提供。

我用虚线标出了N=2000（N的真实值）这个点。首先，我们仍然可以看到之前已经见过的对称性。在左图中，密度在N=2000周围对称分布，而在右图中，密度向右偏移，且左侧尾部更长。现在让我们来看一下每个曲线下方的灰色区域。在两种情况下，这个区域都从N=1750到N=2250。然而，在左图中，这个区域占曲线下方总面积的42%，而在右图中，占总面积的73%。换句话说，在左图中，你的估计有42%的概率不会偏离真实值超过250点，而在右图中，这个概率是73%。也就是说，你更有可能接近真实值。然而，你更有可能稍微高估而非低估。

我可以告诉你，x*+ (x-k)/k*是所谓的**均匀最小方差无偏估计量**，即它是方差最小的估计量。你不会找到任何方差更小的估计，因此一般来说，这是你可以使用的最佳方法。

## 用例

![](../Images/2c9e8ec7cdff2ce5c710fb5f9bd6b7eb.png)

做爱，不做战争 💙。照片由[Marco Xu](https://unsplash.com/@marcoxu?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们刚才看到，如何在池中估算元素的总数，如果这些元素是由连续的数字表示的。从形式上讲，这是一个离散均匀分布。这种问题通常被称为**德国坦克问题**。在第二次世界大战中，盟军使用这种方法，通过仅仅使用他们已经摧毁或捕获的坦克的序列号，来估算德军拥有多少辆坦克。

现在我们可以想出更多可以使用这种方法的例子。包括：

+   如果产品上标有连续的序列号，你可以估算出该产品的生产数量。

+   如果你能够抽取一些用户或客户的ID，你就可以估算他们的数量。

+   如果你能够抽取一些学生的注册号，你就可以估算出你所在大学的学生人数（或者曾经有多少学生）。前提是大学在达到最大注册人数后尚未重新使用之前的号码。

然而，要使用这种方法，需要满足一些条件。最重要的一点是，你必须确保你的样本确实是随机且独立的。如果你询问所有在同一年入学的朋友们他们的注册号，那么这些注册号不会在所有注册号范围内均匀分布，而是会聚集在一起。同样地，如果你从商店购买带有连续编号的商品，你需要确保这些商品是随机分配的。如果它们是从编号1000到1050的产品中获得的，那么你就不是从整个池中随机抽取的。

## 结论

我们刚才看到，如何在离散均匀分布下估算池中实例的总数。尽管两种估算方法在长期中给出的期望值相同，但它们在方差上有所不同，其中一种优于另一种。这很有趣，因为两种方法既不是完全正确也不是完全错误。它们都基于合理的理论考虑，并且在频率主义统计学术语中正确地估算了真实的群体大小。

我现在知道我赢得州博会彩票的概率估计为1/2042 = 0.041%（如果我买了5张票，概率为0.24%）。也许我应该把钱投资到棉花糖上；那样我就能安全地获胜了。

## 参考文献与文献

本文讨论的估算方法的数学背景可以在这里找到：

+   Johnson, R. W. (1994). 估算一个群体的大小。*教学统计*，*16*(2)，50–52。

你也可以查看关于德国坦克问题及相关主题的维基百科文章，它们非常具有解释性：

+   [https://en.wikipedia.org/wiki/German_tank_problem](https://en.wikipedia.org/wiki/German_tank_problem)

+   [https://en.wikipedia.org/wiki/Discrete_uniform_distribution](https://en.wikipedia.org/wiki/Discrete_uniform_distribution)

这是用于进行模拟并创建文章中所示图表的脚本：

```py
import numpy as np
import random
from scipy.stats import gaussian_kde
import matplotlib.pyplot as plt

if __name__ == "__main__":
    N = 2000
    n_simulations = 500

    estimate_1 = lambda sample: 2 * round(np.mean(sample)) - 1
    estimate_2 = lambda sample: round(max(sample) + ((max(sample) - k) / k))

    estimate_1_per_k, estimate_2_per_k = [],[]
    k_range = range(2,10)
    for k in k_range:
        diffs_1, diffs_2 = [],[]
        # sample without duplicates:
        samples = [random.sample(range(N), k) for _ in range(n_simulations)]
        estimate_1_per_k.append([estimate_1(sample) for sample in samples])
        estimate_2_per_k.append([estimate_2(sample) for sample in samples])

    fig,axs = plt.subplots(1,2, sharey=True, sharex=True)
    axs[0].violinplot(estimate_1_per_k, positions=k_range, showextrema=True)
    axs[0].scatter(k_range, [np.mean(d) for d in estimate_1_per_k], color="purple")
    axs[1].violinplot(estimate_2_per_k, positions=k_range, showextrema=True)
    axs[1].scatter(k_range, [np.mean(d) for d in estimate_2_per_k], color="purple")

    axs[0].set_xlabel("k")
    axs[1].set_xlabel("k")
    axs[0].set_ylabel("Estimated N")
    axs[0].set_title(r"$2\times m-1$")
    axs[1].set_title(r"$x+\frac{x-k}{k}$")
    plt.show()

    plt.gcf().clf()
    k = 5
    xs = np.linspace(500,3500, 500)

    fig, axs = plt.subplots(1,2, sharey=True)
    density_1 = gaussian_kde(estimate_1_per_k[k])
    axs[0].plot(xs, density_1(xs))
    density_2 = gaussian_kde(estimate_2_per_k[k])
    axs[1].plot(xs, density_2(xs))
    axs[0].vlines(2000, ymin=0, ymax=0.003, color="grey", linestyles="dotted")
    axs[1].vlines(2000, ymin=0, ymax=0.003, color="grey", linestyles="dotted")
    axs[0].set_ylim(0,0.0025)

    a,b = 1750, 2250
    ix = np.linspace(a,b)
    verts = [(a, 0), *zip(ix, density_1(ix)), (b, 0)]
    poly = plt.Polygon(verts, facecolor='0.9', edgecolor='0.5')
    axs[0].add_patch(poly)
    print("Integral for estimate 1: ", density_1.integrate_box(a,b))

    verts = [(a, 0), *zip(ix, density_2(ix)), (b, 0)]
    poly = plt.Polygon(verts, facecolor='0.9', edgecolor='0.5')
    axs[1].add_patch(poly)
    print("Integral for estimate 2: ", density_2.integrate_box(a,b))

    axs[0].set_ylabel("Probability Density")
    axs[0].set_xlabel("N")
    axs[1].set_xlabel("N")
    axs[0].set_title(r"$2\times m-1$")
    axs[1].set_title(r"$x+\frac{x-k}{k}$")

    plt.show() 
```

*喜欢这篇文章吗？* [*关注我*](/@doriandrost) *以便收到我未来文章的通知。*
