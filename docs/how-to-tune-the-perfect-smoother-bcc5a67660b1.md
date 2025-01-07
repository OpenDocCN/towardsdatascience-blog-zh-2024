# 如何调节完美的平滑器

> 原文：[https://towardsdatascience.com/how-to-tune-the-perfect-smoother-bcc5a67660b1?source=collection_archive---------6-----------------------#2024-02-28](https://towardsdatascience.com/how-to-tune-the-perfect-smoother-bcc5a67660b1?source=collection_archive---------6-----------------------#2024-02-28)

## 使用 Whittaker-Eilers 平滑和留一交叉验证充分利用您的数据

[](https://medium.com/@anbowell?source=post_page---byline--bcc5a67660b1--------------------------------)[![Andrew Bowell](../Images/a23bade2986dd9ce01f9056d0a9b108f.png)](https://medium.com/@anbowell?source=post_page---byline--bcc5a67660b1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bcc5a67660b1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bcc5a67660b1--------------------------------) [Andrew Bowell](https://medium.com/@anbowell?source=post_page---byline--bcc5a67660b1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bcc5a67660b1--------------------------------) ·12 分钟阅读·2024年2月28日

--

在之前的文章中，我介绍了 Whittaker-Eilers 平滑器¹，作为[平滑噪声数据的完美方法](/the-perfect-way-to-smooth-your-noisy-data-4f3fe6b44440)。通过几行代码，该方法可以提供快速且可靠的平滑效果，内置的插值功能能够处理大量缺失数据。此外，只有一个参数 λ（lambda）控制数据的平滑程度。你会发现，任何平滑器都有这样的参数，调整它们可能非常繁琐。所以，让我向你展示，在使用正确的方法时，这个过程可以是多么无痛。

## Whittaker-Eilers 平滑

在平滑数据时，可能没有一个真实的标准答案；只有一些噪声干扰了对数据的分析。使用 Whittaker 平滑器，我们可以调整 λ 来改变从数据中移除噪声的程度。

![](../Images/880cdb8fb79fc97e30aff2e4312bd11e.png)

图 1) [星系的光学输出](https://dr10.sdss.org/spectrumDetail?plateid=1939&mjd=53389&fiber=138) 使用 Whittaker-Eilers 平滑器在三个不同 λ 下平滑后的效果²。

在图 1 中，λ 的范围从 10 到 10,000,000，我们如何知道哪个值最适合我们的数据？

## 留一交叉验证

为了了解在给定的 λ 下平滑效果的有效性，我们需要一个可以从每个平滑序列中计算的指标。由于我们无法依赖实际的标准答案，我们将使用*留一交叉验证*（LOOCV）估算标准的*预测平方误差*（PSE）。这是 k 折交叉验证的一种特殊情况，其中折数 *k* 等于数据集的长度 *n*。

计算很简单；我们去除一个测量值，平滑系列，然后计算我们平滑曲线与去除测量值之间的平方残差。对数据中的每个测量值重复此操作，取平均值，哇，我们计算出了*留一出交叉验证误差*（CVE）——我们对预测平方误差的估计。

![](../Images/3d98dcaf31d97b30924b4f13fec9bf12.png)

> 在上面的方程中，我们的函数*f*是平滑函数，*-i*符号表示我们平滑了数据，去掉了第*i*个测量值。从现在开始，我还将利用根交叉验证误差（RCVE），它只是我们交叉验证误差的平方根。

现在我们可以再次平滑光谱，计算各种λ的交叉验证误差。然后我们可以选择产生最低交叉验证误差的λ。

![](../Images/d7c56a3f4055a3c354661d70f67368d8.png)

图2) 使用最小交叉验证误差²选择的最佳λ平滑的[光谱](https://dr10.sdss.org/spectrumDetail?plateid=1939&mjd=53389&fiber=138)。

在图2中，您可以看到根交叉验证误差针对λ的绘制。对于这个特定的数据系列，λ约为10³会产生最佳配置。

在whittaker-eilers [Python](https://pypi.org/project/whittaker-eilers/) 和 [Rust](https://crates.io/crates/whittaker-eilers) 包中，我已经将这个实现为一个单一函数，该函数执行λ的搜索，并返回最佳平滑系列以及所有λ和交叉验证误差。

***Python****:* [*pip install whittaker-eilers*](https://pypi.org/project/whittaker-eilers/)

```py
from whittaker_eilers import WhittakerSmoother

data_to_smooth = [6.7, 8.0, 2.1, 8.4, 7.6, 3.4]

smoother = WhittakerSmoother(lmbda=1, order=2, data_length=len(data_to_smooth))

results = smoother.smooth_optimal(data_to_smooth, break_serial_correlation=False)

optimally_smoothed_series = results.get_optimal().get_smoothed()
optimal_lambda = results.get_optimal().get_lambda()
```

***Rust****:* [*cargo add whittaker-eilers*](https://crates.io/crates/whittaker-eilers)

```py
use whittaker_eilers::WhittakerSmoother;

let data_to_smooth = vec![6.7, 8.0, 2.1, 8.4, 7.6, 3.4];

let mut smoother = 
            WhittakerSmoother::new(1.0, 2, data_to_smooth.len(), None, None)
            .unwrap();

let results = smoother.smooth_optimal(&data_to_smooth,  false).unwrap();

println!("Optimal result: {:?}", results.get_optimal());
```

## 交叉验证产生了好的结果吗？

有了一点领域知识，我们可以通过交叉验证双重检查我们的平滑结果。在图2的左侧，波长约为4000埃的地方，有两个低谷，与钾和氢的吸收线以及氢的发射线对齐。

![](../Images/013ff087a77f41bcd59f1c246b90c38a.png)

图3) 光谱的放大图，覆盖了[SDSS网站](https://dr10.sdss.org/spectrumDetail?plateid=1939&mjd=53389&fiber=138)上的发射和吸收线²。

过度平滑导致这些低谷被合并，而不足平滑则使低谷分开但非常嘈杂。留一出交叉验证已经出色地选择了一个λ，它去除了绝大部分噪音，同时保留了基础信号。

## 更多例子

让我们看看用留一出交叉验证选择的最佳λ平滑的更多数据。

![](../Images/a6339a9a5cc9d36549c9afcd136c6f1d.png)

图4) 最佳平滑的[矿物骨密度变化](https://hastie.su.domains/ElemStatLearn/)和测试λ的根交叉验证误差³。这是Whittaker如何用来平滑散点图的一个例子。

![](../Images/173d3a3dd3d2436a181bc9a11120dcb1.png)

图5）意大利小镇的[绝对湿度](https://archive.ics.uci.edu/dataset/360/air+quality)经过优化平滑处理后的结果，以及测试的λ值对应的根交叉验证误差⁴。

第一个数据集噪声较大，因此小的λ值生成了最大的交叉验证误差。相反，第二个数据集几乎没有噪声，结果较大的λ值受到的惩罚远大于较小的λ值。

> 值得注意的是，λ值在不同数据集之间可能有所不同，不仅是由于噪声，还可能由于测量的采样率。

## **交叉验证的致命弱点**

序列相关数据——当数据与其滞后版本相关时——在使用留一交叉验证时会导致一个显著的问题。交叉验证要求测量误差是独立的（就像大多数统计技术一样），但是在序列相关数据中，测量误差很可能依赖于前一个误差。实际上，这会导致数据几乎没有被平滑化，只有在该尺度下，误差才是独立的。

Eilers提出了一种快速的解决方案，你可以每隔第5个（或第10个或第20个）点对数据序列进行采样，从而有效地去除数据中的序列相关性¹。在之前的代码片段中，你可以看到我已经通过公开一个名为*“break_serial_correlation”*的布尔选项来实现这一点。这个选项在平滑图3中的光谱时被关闭，因为没有序列相关性，但在平滑图5中的湿度数据时被开启。这是一个不错的解决方案，但并不是完美的。

## 像地面真值一样好吗？

通常，当你想评估模型如何拟合数据时，你会使用如*均方根误差*（RMSE）这样的指标，来计算模型估计值与地面真值之间的差异。那么我们来生成几个具有不同噪声水平的数据序列，并比较RMSE与我们的留一交叉验证误差的反应。

![](../Images/213407d0c8cdf6009cca8ac18e2673fe.png)

图6）在0到2**π**之间的余弦函数，加入不同水平的高斯噪声，然后使用优化调节的Whittaker进行平滑。

![](../Images/df30f83983e9242458d89babae9a2a9e.png)

图7）根交叉验证误差（RCVE）和均方根误差（RMSE）在相同图表中与λ值的关系，分别使用不同的坐标轴缩放。

正如预期的那样，当添加到测量中的误差增大时，选定的最优λ值也会增大，从而在序列上引入更多的平滑。可以看到，RCVE与RMSE之间大致呈线性关系，两者之间相差一个常数。这与文献中的预期一致⁵，因为CVE是我们对*预测平方误差*（PSE）的估计，而PSE与均方误差的关系为，

![](../Images/da4e0a2775e7c8798aead8ad089509e6.png)

其中σ是残差的标准差。我们在这里证明的是，在真正的随机*独立*误差的情况下，留一法交叉验证提供了一个很好的预测平方误差估计，进而也能估计平滑拟合的整体质量。

## 交叉验证速度较慢。让我们加速它。

在本文之前，我提供了计算交叉验证误差的公式。严格按照它，你需要对长度为*n-1*的数据序列进行*n*次平滑，即使使用快速方法，这也不是理想的做法。幸运的是，Whittaker平滑器是一个*保持常数的线性平滑器*，使我们能够推导出普通残差与“留一法”交叉验证残差之间的惊人关系⁵。这个关系的结果是？**只需对数据进行一次平滑即可计算交叉验证误差。**让我们深入了解一下。

[我之前展示过Whittaker平滑器背后的线性代数](https://medium.com/towards-data-science/the-perfect-way-to-smooth-your-noisy-data-4f3fe6b44440#:~:text=of%20pre%2Dprocessing!-,The%20Mathematics,-Now%20we%E2%80%99ve%20covered)以及如何通过计算平滑序列**z**与原始序列**y**之间的普通残差，

![](../Images/ce682b350b10e89e8cc03365c8036fe8.png)

然后通过平滑度的度量来平衡它们——即平滑序列中相邻点之间的平方差和，

![](../Images/08f3d2018814c3e3c2fe63844c58b77d.png)

你最终得到方程3，其中**λ**用于缩放数据的平滑度。

![](../Images/035ce655ca1104315ce733750db31758.png)

最小化**Q**就会得到针对任何给定**λ**的最优平滑序列，这可以归结为一个最小二乘问题，得到以下线性方程，

![](../Images/9a26dc37854174e779cecdde83fbb192.png)

其中**y**是原始数据点的向量，**z**是平滑数据点的向量，**A**是包含有关平滑常数**λ**信息的矩阵。我们可以调整这些，

![](../Images/77a02a4a506e737664e1a5de64de03a3.png)

并得到一个描述*线性平滑器*的通用方程。**H**在回归和平滑文献中被称为*平滑矩阵*或*帽子矩阵*——这很有道理，因为将我们的序列**y**与**H**相乘会得到平滑序列**z**（在某些符号中是**ŷ**，因此称为帽子矩阵）¹。平滑矩阵非常重要，因为它构成了我们普通残差与留一法交叉验证残差之间关系的基础。

让我们重新审视我在本节开始时所说的话。Whittaker平滑器是*保持常数*的；这意味着平滑器不会对底层信号添加偏差。由于这一点，我们可以假设平滑矩阵中的每一行的和为1。如果不是这样，当你用它乘以原始点时，它会将平滑系列从数据中的底层信号中偏移开来。将这一点与我们如何计算交叉验证平滑系列的方式联系起来，给了我们推导的起点。当我们从系列中移除一个点时，我们必须从**H**中移除一列。因此，一行就少了一个元素，需要重新归一化以使其和为1。我们可以将其形式化为

![](../Images/f96ebb8c5412d315942c1ca2b2b122bb.png)

其中**h**是我们平滑矩阵**H**的一个元素，我们实际上只是在描述一个跳过一列的矩阵乘法⁵。这里的*-i*符号与之前CVE方程式中的相同——它是*第i*个元素的预测值，其中*y*ᵢ没有用于计算拟合。

我们现在想尝试找出普通残差（通过平滑系列与原始系列之间的计算）与留一法交叉验证残差（通过用缺失输入点生成的平滑系列与原始系列之间的计算）之间的关系。首先，让我们扩展并重新排列，以去掉求和中的难看符号。

![](../Images/925015a6c58193ee951551ab45b8fdf6.png)![](../Images/518b8db938b48f5f34a8eb9cfe5963e7.png)![](../Images/7889d68a89fcf7c57bb35d1dc6aa1ad9.png)

求和现在已经考虑到所有元素，变成了产生标准平滑值**z**的方程，

![](../Images/ffb1b8bf039306bd456688d243b2a3f9.png)

然后可以将其代入，

![](../Images/322dd392de1380822f5b242ea9166987.png)![](../Images/661011418518fbbb172e1a95a4f2c685.png)![](../Images/ba41782c2eb9f1c5e40003e03b601b98.png)![](../Images/e66e4835b09003db3483a9008165daa0.png)![](../Images/0e6711bd2125da2a8d96d444756f1249.png)![](../Images/eb665e7045e28e1d8d161d70daad9774.png)

最终，这导致了通过平滑矩阵对角线直接关系留一法交叉验证残差与普通残差。非常简洁。现在，我们可以将原始的CVE方程式带入我们新的关系中，

![](../Images/6a9b84f8a607543c721f5743a727f868.png)

正如你所看到的，我们现在不需要每次都平滑系列了！我们只需要访问平滑矩阵对角线上的一个元素⁵。这样，我们就可以顺利过渡到*广义交叉验证*的方程式，其中，我们不是将每个对角线元素除以**H**，而是计算对角线的均值，并用它来除³。

![](../Images/fb4ec457ddd82f0c46164b520a1bbc8d.png)

## **为什么这仍然太慢**

在计算平滑矩阵时，我们需要对稀疏矩阵进行求逆——这不幸会导致一个密集矩阵。随着数据长度的增加，密集的平滑矩阵将以*n²*的速度增长。虽然计算这个过程仍然比平滑数据系列*n*次要快，但它并不是最快的。Eilers观察到，**H**的对角线对于任何长度的系列绘制出类似的形状，只需要根据长度比进行相应的缩放。我们本质上是在从**H**中创建一个样本，用来计算**H**对角线的平均值。对于100的样本大小，实施此方法可以让我们始终快速地计算交叉验证误差。

![](../Images/28b52efb81d448e8d8ea3a23215f77a0.png)

图8) 使用优化的平滑矩阵方法平滑***n***长度的序列所需的时间，以及仅通过平滑长度为***n-1***的序列***n***次所需的时间。

在图8中，我们可以看到，如果我们使用原始方程计算CVE，平滑数据系列*n*次，时间复杂度会随着*n²*的增长——当我们计算平滑矩阵长度达到100时也是如此。将抽样方法应用于*n=100*后，我们可以以极少的额外成本增加序列的长度。这种抽样很可能是图7中RCVE与RMSE之间小差异的原因。

## 结语与进一步阅读

Whittaker-Eilers方法是一个令人惊叹的工具，既能平滑数据，又能对噪声数据进行插值。通过稀疏矩阵实现该方法，能够得到一个极为快速且内存高效的方法，从而进行快速的交叉验证，在没有真实值的情况下，这是评估平滑器性能的有效手段。

对于序列相关的数据，稍作调整后的交叉验证仍然是一个不错的方法。最终，像L曲线和V曲线优化等更复杂的方法更适合用于这种数据的参数选择。也许不久之后，我会在whittaker-eilers包中实现这些方法。

所有用于生成这些结果的代码都可以在[whittaker-eilers](https://github.com/AnBowell/whittaker-eilers) GitHub 仓库中找到，其中包含了Python和Rust版本的代码包。我还包括了Eilers最初的MATLAB脚本，用于实现Whittaker方法和交叉验证¹。

再次感谢阅读！请务必访问我的[个人网站](https://www.anbowell.com/)和medium获取更多内容。

*本文中的所有图像均由作者根据相关数据生成。*

## 参考文献

[1] Eilers, Paul H. C., *A Perfect Smoother*, Analytical Chemistry **2003** *75* (14), 3631–3636, DOI: [10.1021/ac034173t](https://doi.org/10.1021/ac034173t)

[2] Kollmeier 等人, *SDSS-V 开创性的全景光谱学,* 美国天文学会公报, **2019** 51 (7), 274, Bibcode: [2019BAAS…51g.274K](https://ui.adsabs.harvard.edu/abs/2019BAAS...51g.274K/abstract)

[3] Hastie T, Tibshirani T, Friedman J, *统计学习的元素,* Springer, **2009,** URL: [https://hastie.su.domains/ElemStatLearn/](https://hastie.su.domains/ElemStatLearn/)

[4] Vito, Saverio, *空气质量,* UCI 机器学习资源库, **2016,** DOI: [10.24432/C59K5F](https://doi.org/10.24432/C59K5F) 许可：*创意共享署名 4.0 国际*

[5] Geyer, Charles J., *5601 笔记：平滑处理,* 明尼苏达大学, **2013,** URL: [https://www.stat.umn.edu/geyer/5601/notes/smoo.pdf](https://www.stat.umn.edu/geyer/5601/notes/smoo.pdf)

**SDSS 光谱数据的数据显示致谢**

*斯隆数字天空调查 V（SDSS-V）的资金由阿尔弗雷德·P·斯隆基金会、海辛-西蒙斯基金会、国家科学基金会和参与机构提供。SDSS 感谢犹他大学高性能计算中心的支持与资源。SDSS 的望远镜位于阿帕奇点天文台，由天体物理研究联合会资助并由新墨西哥州立大学运营，以及位于拉斯坎帕纳斯天文台，由卡内基科学研究所运营。SDSS 网站是* [*www.sdss.org*](https://www.sdss.org/)*。*

*SDSS 由天体物理研究联合会管理，服务于 SDSS 合作机构，包括加州理工学院、卡内基科学研究所、智利国家时间分配委员会（CNTAC）批准的研究人员、弗拉特铁研究所、哥谭参与小组、哈佛大学、海德堡大学、约翰霍普金斯大学、洛桑联邦理工学院（EPFL）、波茨坦莱布尼茨天体物理研究所（AIP）、海德堡马克斯普朗克天文学研究所（MPIA）、马克斯普朗克外星物理研究所（MPE）、南京大学、中国科学院国家天文台（NAOC）、新墨西哥州立大学、俄亥俄州立大学、宾夕法尼亚州立大学、史密森天体物理天文台、太空望远镜科学研究所（STScI）、恒星天体物理参与小组、墨西哥国立自治大学、亚利桑那大学、科罗拉多大学博尔德分校、伊利诺伊大学香槟分校、多伦多大学、犹他大学、弗吉尼亚大学、耶鲁大学和云南大学。*
