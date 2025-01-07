# 一种被不公正遗忘的相关系数

> 原文：[https://towardsdatascience.com/an-undeservedly-forgotten-correlation-coefficient-86245ccb774c?source=collection_archive---------2-----------------------#2024-04-30](https://towardsdatascience.com/an-undeservedly-forgotten-correlation-coefficient-86245ccb774c?source=collection_archive---------2-----------------------#2024-04-30)

## 一种适用于日常任务的非线性相关度量

[](https://medium.com/@vadim.arzamasov?source=post_page---byline--86245ccb774c--------------------------------)[![Vadim Arzamasov](../Images/70ced2eafa6fc926052979875a0a4265.png)](https://medium.com/@vadim.arzamasov?source=post_page---byline--86245ccb774c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--86245ccb774c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--86245ccb774c--------------------------------) [Vadim Arzamasov](https://medium.com/@vadim.arzamasov?source=post_page---byline--86245ccb774c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86245ccb774c--------------------------------) ·阅读时长6分钟·2024年4月30日

--

![](../Images/1d1768870561985b726ed04a98c04904.png)

图片由作者使用 recraft.ai 创建

传统的相关系数，如皮尔逊*ρ*，斯皮尔曼或肯德尔*τ*，仅限于寻找线性或单调关系，并且在识别更复杂的关联结构时存在困难。关于一种新的相关系数 *ξ* 的[近期文章](https://medium.com/towards-data-science/a-new-coefficient-of-correlation-64ae4f260310) [1]，该系数旨在克服这些限制，已引起广泛关注并进行了深入讨论。评论中提出的一个问题是，*ξ* 相比基于互信息的非线性相关度量，具有哪些特别的优势。在这种辩论中，实验可能胜过千言万语。因此，在这个故事中，我将从多个方面实验性地比较 *ξ* 与基于互信息的系数 *R*，这些方面是一个非线性相关度量应满足的特性。基于结果，我强烈建议在大多数需要寻找非线性关联的常规操作中使用 *R*，而不是 *ξ*。

## 要求

让我首先总结并说服你，我们正在寻找的系数应具备的期望特性。我们希望找到一个关联度量 *A(x,y)*，它

+   是**非线性的**。也就是说，当 *x* 和 *y* 独立时，它的值为零；当变量之间存在确切的非线性关系时，如 *x = h(t), y=f(t)*，其中 *t* 是一个参数时，它的度量值为一；

+   是**对称的**。也就是说，*A(x,y)=A(y,x)*。反之则会令人困惑；

+   是**一致的**。也就是说，当*x, y*服从双变量正态分布时，它等于线性相关系数*ρ*，即它是*ρ*对其他分布的推广。这是因为*ρ*在实践中广泛使用，我们中的许多人已经培养出了对其数值与关系强度之间联系的直觉。此外，*ρ*对于标准正态分布有明确的意义，因为它完全定义了标准正态分布；

+   是**可扩展的**——即使对于具有大量观测值的数据集，也可以在合理的时间内计算相关性；

+   是**精确的**，即具有低方差的估计器。

下表总结了我的实验结果，其中绿色表示度量具有测试的特性，红色表示相反，橙色稍好于红色。现在让我带你走过这些实验；你可以在这个[Github仓库](https://github.com/Arzik1987/medium/tree/main/nonlinear_correlation) [2]中找到它们的代码，使用**R**编程语言编写。

![](../Images/1a43b98dfd9d8fba5a8c637943b133cf.png)

图片由作者创建

## **相关系数**

我使用以下系数的实现及其配置

+   对于线性相关系数*ρ*，我使用‘stats’包中的标准函数`cor()`；

+   对于*ξ*，我使用[‘XICOR’包](https://cran.r-project.org/web/packages/XICOR/index.html)中的`xicor()`函数[3]；

+   互信息（MI）取值范围是[0,∞)，并且有多种方法可以估计它。因此，对于R，必须选择（a）要使用的MI估计器和（b）将MI转换到[0,1]范围内的变换。

目前有基于直方图和基于最近邻的MI估计器。尽管许多人仍然使用基于直方图的估计器，但我认为Kraskov的最近邻估计器[4]是最好的之一。我将使用它在[‘FNN’包](https://cran.r-project.org/web/packages/FNN/index.html)中的实现`mutinfo()`[5]，并使用论文中建议的参数*k=2*。

> 如果你想了解更多关于这个特定的互信息（MI）估计器，请在评论中写下。

也有几种方法可以将MI归一化到[0,1]的区间。我将使用下面的方法，因为它已被证明具有一致性特性，我将在实验中展示这一点。

![](../Images/cfa6e398c2c11549289e5ed81d7de107.png)

> 这个度量R称为**互信息**系数[6]。然而，我注意到人们有时会把它与较新的**最大**信息系数（MIC）[7]混淆。后者已被证明比一些替代方法差[8]，并且缺乏它应有的一些特性[9]。

## 非线性

在下图中，我为一个具有不同甜甜圈厚度的10K个点的数据集计算了所有三个相关系数。如预期的那样，线性相关系数*ρ*没有捕捉到任何图形中的关系。相反，*R*正确地确定了*x*和*y*是相关的，并且对于右侧图形的数据，*R*的值为1，这对应于*x*和*y*之间无噪声的关系：*x = cos(t)*和*y = sin(t)*。然而，在后者的情况下，系数*ξ*仅为0.24。更重要的是，在左侧图中，尽管*x*和*y*并非独立，*ξ*却接近零。

![](../Images/617eddceef4ec977046f47c6680344e1.png)

图像由作者创建

## 对称性

在下图中，我为从不同分布生成的数据集计算了这些量。我得到了*ρ(x,y)=ρ(y,x)*和*R(x,y)=R(y,x)*，因此对于这些量我只报告一个单一的值。然而，*ξ(x,y)*和*ξ(y,x)*则差异很大。这可能是因为*y=f(x)*，但*x*并不是*y*的函数。这种行为在现实中可能并不理想，因为解读一个非对称的相关矩阵并不容易。

![](../Images/5581926845a4dd77c216c85036c7875c.png)

图像由作者创建

## 一致性

在这个实验中，我计算了所有系数，对于由具有给定相关系数0.4、0.7或1的双变量标准正态分布生成的数据集。*ρ*和*R*接近真实的相关性，而*ξ*则不是，即它没有上述定义的一致性特性。

![](../Images/7399a137d4e74b6087c503cf6cdf17f7.png)

图像由作者创建

## 可扩展性

为了检查估计量的性能，我生成了不同大小的数据集，这些数据集由两个独立且均匀分布的变量组成。下图展示了计算每个系数所需的时间（单位：毫秒）。当数据集包含50K个点时，*R*的计算速度比*ξ*慢大约1000倍，比*ρ*慢大约10000倍。然而，计算仍然需要大约10秒，这在计算适量的相关性时是合理的。考虑到上述提到的*R*的优势，我建议即使是计算大量相关性时也使用它——只需随机抽样你的数据至大约10K个点，此时计算*R*所需的时间不到一秒。

![](../Images/c16f10389e59b0999dc654b60d9b5a69.png)

图像由作者创建

## **精确度**

对于来自同一分布的不同样本，相关系数的估计值会有所不同。如果 *x* 和 *y* 之间存在关联，我们希望这些估计值的方差相较于相关系数的均值较小。对于一个度量 *A(x,y)*，可以计算 *precision=sd(A)/mean(A)*，其中 *sd* 是标准差。该值越小越好。下表展示了在不同大小的数据集上，计算的 *precision* 值，这些数据集具有不同的维度相关值。*ξ* 是最不精确的，而 *ρ* 是最精确的。

![](../Images/3f65054e0ebdd098a98507d66df51600.png)

图片由作者创建

## 参考文献

[1] [一种新的相关系数](https://medium.com/towards-data-science/a-new-coefficient-of-correlation-64ae4f260310)

[2] [我在 Github 上的实验](https://github.com/Arzik1987/medium/tree/main/nonlinear_correlation)

[3] [R 的 XICOR 包](https://cran.r-project.org/web/packages/XICOR/index.html)

[4] Kraskov, A., Stögbauer, H., & Grassberger, P. (2004). 估计互信息。*Physical review E*, *69*(6), 066138.

[5] [R 的 FNN 包](https://cran.r-project.org/web/packages/FNN/index.html)

[6] Granger, C., & Lin, J. L. (1994). 使用互信息系数识别非线性模型中的滞后。*Journal of time series analysis*, *15*(4), 371–384.

[7] Reshef, D. N., Reshef, Y. A., Finucane, H. K., Grossman, S. R., McVean, G., Turnbaugh, P. J., … & Sabeti, P. C. (2011). 在大数据集中检测新型关联。*science*, *334*(6062), 1518–1524.

[8] Simon, N., & Tibshirani, R. (2014). 对 Reshef 等人《在大数据集中检测新型关联》的评论，发表于《Science》2011年12月16日。*arXiv 预印本 arXiv:1401.7645*.

[9] Kinney, J. B., & Atwal, G. S. (2014). 公平性、互信息和最大信息系数。*Proceedings of the National Academy of Sciences*, *111*(9), 3354–3359.
