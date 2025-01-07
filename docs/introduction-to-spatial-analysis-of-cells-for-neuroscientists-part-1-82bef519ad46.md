# 神经科学家空间分析细胞的入门（第1部分）

> 原文：[https://towardsdatascience.com/introduction-to-spatial-analysis-of-cells-for-neuroscientists-part-1-82bef519ad46?source=collection_archive---------12-----------------------#2024-05-30](https://towardsdatascience.com/introduction-to-spatial-analysis-of-cells-for-neuroscientists-part-1-82bef519ad46?source=collection_archive---------12-----------------------#2024-05-30)

## 一种使用点模式分析（PPA）与`spatstat`的方式

[](https://medium.com/@damanrique?source=post_page---byline--82bef519ad46--------------------------------)[![Daniel Manrique-Castano](../Images/06f857ae6e82688113f1089c7f03be88.png)](https://medium.com/@damanrique?source=post_page---byline--82bef519ad46--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82bef519ad46--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--82bef519ad46--------------------------------) [Daniel Manrique-Castano](https://medium.com/@damanrique?source=post_page---byline--82bef519ad46--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82bef519ad46--------------------------------) ·10分钟阅读·2024年5月30日

--

![](../Images/f5616b948d25d4a114fb18d9ade8062a.png)

从点模式生成的密度核

作为一名神经科学家，近年来我一直致力于开发能够对大脑中细胞分布进行多模态评估的策略。我的动机是定量理解大脑损伤后神经胶质细胞的重排过程。在这个过程中，我遇到了`spatstat`(*1*)，这是一个基于点模式的空间分析的多功能R包，称为点模式分析（PPA）。这种方法在地理学、流行病学或生态学等领域得到了很好的发展，但在神经生物学中的应用非常有限，甚至几乎没有。我最近发布了一个简短的协议(*2*)，读者可以找到一篇预印本(*3*)，其中详细介绍了这种方法的应用。

在这篇文章中，我的目标是为那些有兴趣揭示不同组织中细胞空间分布的研究人员提供一种易于理解的方法介绍，而不拘泥于科学论文中的叙述框架。

# 什么是点模式分析（PPA）？

PPA是一种空间分析技术，用于研究给定区域（也称为观察窗口）内单个事件或物体的分布。该方法允许研究人员检查每单位面积内的物体数量（称为空间强度），以及这些点是否随机分布、聚集或规则间隔，并考察空间强度随不同协变量的变化。与原始且不可重复的细胞计数（例如100个细胞/mm²）不同，PPA保留了所有空间信息，并允许对点模式进行多次且可重复的操作。这使研究人员能够识别影响感兴趣物体分布的潜在过程或结构。

# PPA的要求

执行点模式分析（PPA）的唯一要求是拥有单个物体（细胞、蛋白质、亚细胞结构等）的xy坐标。在本文中，我们重点介绍二维PPA，尽管三维方法也可用。这些坐标随后使用 R 和 spatstat 函数进行处理，以创建点模式并将其存储为超框架（hyperframe）。

我使用 QuPath (*4*) 或 CellProfiler (*5*) 通过无偏的细胞检测/定量方法获取了单个细胞的坐标。我发现，圆形/圆状物体（如神经元，例如 NeuN）的检测和分割比不规则物体（如星形胶质细胞（GFAP）或小胶质细胞（IBA1））要容易，尤其是在细胞密度高且细胞重叠较多的情况下（例如，脑损伤后的胶质聚集）。不规则且高度聚集的物体的分割仍然是该领域的前沿问题。然而，QuPath 或 CellProfiler 提供了合理的准确性，最重要的是，它们是可重复的并且可以验证的。人工观察者也无法做得更好。因此，我建议如果在某些情况下你觉得某些物体仅对应于一个细胞的片段或多个细胞的组合，不必过于担心。调整参数以确保细胞检测/分割达到最佳效果。如果细胞间隔足够远（例如健康的大脑或细胞培养），那么不必担心。

# 创建点模式

在处理多个样本时，可以通过使用类似以下[链接](https://github.com/elalilab/Stroke_PDGFR-B_Reactivity/blob/main/Widefield_5x_Ipsilateral_Gfap-Pdgfrb_Handling.qmd)的函数来简化点模式的创建。该过程的核心是将包含单细胞坐标的单独.csv文件转换为点模式（使用`spatstat`的`ppp`函数），并将其组织成一个超框架，可以将其保存并作为.rds R对象共享。

在这里，我们将加载我在研究过程中创建的一个点模式（*3*）。该文件可以在[GitHub 仓库](https://github.com/daniel-manrique/MediumBlog/tree/main/Data)中找到，文件名为`PointPatterns_5x.rds`。请随意用于研究、教育或培训目的。

```py
library(brms)
library(dplyr)
library(ggplot2)
library(gtsummary)
library(modelr)
library(spatstat)
library(tidybayes)
```

```py
PointPatterns <- readRDS("Data/PointPatterns_5x.rds")
row.names(PointPatterns) <- PointPatterns$ID 

head(PointPatterns)
```

你可以看到超框架包含了几列变量。我们将重点关注前三列，它们包含了三种类型的大脑细胞的点模式：神经元、星形胶质细胞和小胶质细胞。我们将以自己的方式重写一些变量列，以便练习PPA的实现。首先，我们来看一下通过同时绘制所有点模式（以神经元为例）来观察它们的样子：

```py
plot(PointPatterns$Neurons)
```

![](../Images/2adba861dc4f6d37902aa83ab1a36f4e.png)

图1：多重点模式。

让我们通过查看任意一只大脑的细节来深入了解：

```py
plot(PointPatterns$Neurons$M05)
```

![](../Images/1a2324987371eff9602376ee68e6d752.png)

图2：单一的点模式。

我们可以稍微调整一下图形，通过在同一时间显示两种细胞类型（点模式），并改变它们的绘制方式（形状和颜色）。这里是一个示例：

```py
# We plot neurons in black with symbol (10)
plot(PointPatterns$Neurons$M05, pch = 10, cex = 0.4, main = "Neurons and Astrocytes")

# We add astrocytes in red with a different symbol (18)
plot(PointPatterns$Astrocytes$M05, pch = 18, cex = 0.4, col = "red", add = TRUE)
```

![](../Images/11ecbdaac7307350fd5eac5fe7eb28dc.png)

图3：不同细胞类型和美学效果。

这给出了细胞数量和分布的初步印象，但当然我们需要量化它。第一种方法是为每个点模式获取估计的空间强度。我们可以通过简单的代码为超框架中的每一行生成一个额外的列。为了这篇文章，我们只针对星形胶质细胞进行操作：

```py
PointPatterns$AstrocytesIntensity <- with(PointPatterns, summary(Astrocytes)$intensity)

head(PointPatterns[,9:11])
```

![](../Images/23a6ff8a53095d423a1fcff4eedfedaa.png)

你可以看到我们创建了一个新列，其中包含了星形胶质细胞的空间强度。接下来，我们将这些信息与分组变量一起提取到数据框中：

```py
Astrocytes_df <- as.data.frame(PointPatterns[,9:11])

# We make sure to organize our factor variable in the right order
Astrocytes_df$DPI <- factor(Astrocytes_df$DPI, levels = c("0D", "5D", "15D", "30D"))

gt::gt(Astrocytes_df[1:10,])
```

![](../Images/f5d5a972b85bf35a50803dbfbd52aebc.png)

这是一个不错的开始，你能够以可重复的方式使用无偏/自动细胞计数来获得单位面积内的细胞数量。让我们从这些数据中做一个简单的科学推断。

# 拟合一个统计模型来分析空间强度

和往常一样，在我的博客文章中，我们使用`brms` (*6*)拟合一个贝叶斯线性模型，在其中我们研究星形胶质细胞的空间强度，并根据DPI（即该数据集中的动物脑损伤后的天数）进行条件化。我们将建立一个异方差模型（预测sigma），因为（我知道）不同DPI之间的方差是不相等的。在0D时，它的方差要小得多。

```py
Astrocytes_Mdl <- bf(AstrocytesIntensity ~ DPI, 
                     sigma ~ DPI)

Astrocytes_Fit <- brm(formula = Astrocytes_Mdl,
                      family = student,
                      data = Astrocytes_df, 
                      # seed for reproducibility purposes
                      seed = 8807,
                      control = list(adapt_delta = 0.99),
                      # this is to save the model in my laptop
                      file    = "Models/2024-05-24_PPA/Astrocytes_Fit.rds",
                      file_refit = "never")

# Add loo for model comparison
Astrocytes_Fit <- 
  add_criterion(Astrocytes_Fit, c("loo", "waic", "bayes_R2"))
```

让我们看一下模型的摘要表：

![](../Images/c678c24671811924977346241aa61521.png)

我们看到，0D（截距）时动物的平均空间强度为4.3，95%的可信区间（CI）在0.73到2.90之间。那是非常少的细胞数量。另一方面，在15D时，我们看到一个峰值，平均空间强度为26.9，可信区间在22到31之间。

让我们使用强大的`TidyBayes`包来绘制结果（*7*），该包由伟大的[Matthew Kay](https://www.mjskay.com/)开发。

```py
Astrocytes_df %>%
  data_grid(Astrocytes_df) %>%
  add_epred_draws(Astrocytes_Fit) %>%
  ggplot(aes(x = .epred, y = DPI)) +
  labs(x = "Spatial intensity") +
  stat_halfeye() +
  geom_vline(xintercept = 0) +
  Plot_theme
```

![](../Images/68d0761bd0aa09e9875d6515bdd522cc.png)

图4：星形胶质细胞空间强度的后验分布。

图4中的`stat_halfeye()`是查看结果的一个很好的方法。这个过程类似于在给定区域内计数细胞。PPA的优点在于，你不必依赖学生计数细胞时的假设视力（所谓的专家不是计数的人），而是可以生成无偏的细胞计数，这些计数是可验证的，并且是可重复和可重用的。显然，亲爱的读者，我们可以通过PPA做得更多。

# 创建密度核

我们在加载的点模式中有可用的密度核，但我们将为这篇文章重新编写它们。密度核是一种估计变量概率密度函数的方法，在这种情况下，就是细胞位置。这提供了一个平滑的估计，用于描述生成观测数据的强度函数。

点模式的核密度估计可以表述如下：

![](../Images/f4fe1e192c2108651bc83c4e80563ffd.png)

我们将使用`spatstat`中的`density`函数重新创建星形胶质细胞和小胶质细胞的密度核。请确保此函数未被其他包覆盖。我发现sigma（带宽）为0.2时，点模式的密度读取较为合理。

```py
PointPatterns$Astrocytes_Dens <- with(PointPatterns, density(Astrocytes, sigma = 0.2, col = topo.colors))
```

在准备好这一切之后，我想给你举一个使用单一大脑示例来说明sigma在密度核中的影响：

```py
par(mfrow = c(1,3), mar=c(1,1,1,1), oma=c(1,1,1,1))

plot(density(PointPatterns$Astrocytes$M05, sigma = 0.02), col = topo.colors, main = "sigma = 0.02")
plot(density(PointPatterns$Astrocytes$M05, sigma = 0.2), col = topo.colors, main = "sigma = 0.2")
plot(density(PointPatterns$Astrocytes$M05, sigma = 2), col = topo.colors, main = "sigma = 2")
```

![](../Images/80a3138784acf26514e55584b6d3e98e.png)

图5：具有不同sigma的密度核。

图5显示，在第一个案例中，我们看到一个非常低的sigma会映射单个点。对于sigma = 0.2，我们看到在更大尺度上的映射，并且可以更好地区分星形胶质细胞的低密度和高密度区域。最后，sigma = 2提供了一种视角，在这种情况下，我们实际上无法精确地区分星形胶质细胞的不同密度。对于这种情况，sigma = 0.2是一个很好的折衷选择。

现在我们将拟合一个简单的点过程模型，以研究基于星形胶质细胞密度（通过密度核映射）条件下神经元的相对分布。

# 拟合点过程模型（ppm）

在这里，我们使用`spatstat`中的`mppm`函数来为超框架中的点模式拟合多点过程模型。不幸的是，`spatstat`中没有类似贝叶斯的方法来处理多点模式。

```py
# We fit the mppm model
Neurons_ppm <- mppm(Neurons ~ Astrocytes_Dens, data = PointPatterns)

# We check the results
summary(Neurons_ppm)
```

![](../Images/4c5f2b0bb516790213ef789aa4c326ad.png)

请记住，空间模型是通过使用对数链接函数的泊松分布来拟合的，以获得仅为正的结果。这意味着我们需要对表中的结果进行指数化，以将它们转换回原始尺度。因此，我们可以看到，在基线时神经元的空间强度（当星形胶质细胞密度为0时）为exp(3.54) = 34.4。这种强度会随着星形胶质细胞空间强度（由密度核定义）每单位增加而减少，减少量为ex(-0.002171358)=-0.99。换句话说，这个模型告诉我们，在星形胶质细胞更多的地方，神经元会更少。请注意，我们没有在回归中包括DPI，这是您可以进行的一个练习，以查看此估算是否会随着DPI的变化而改变。

PPA还有更多方面可以探索。然而，为了避免让这篇文章过长且繁重，我将在接下来的两篇文章中介绍这些内容。在这里，您可以学习如何计算和提取细胞的空间强度，创建密度核，并使用它们构建点过程模型。在下一篇文章中，我们将探索如何进行相对分布的计算以及如何使用栅格图层进一步探索细胞分布。

我非常感谢您的评论或反馈，告诉我这次旅程是否对您有用。如果您想获得更多关于数据科学和其他话题的优质内容，您可以考虑成为[Medium会员](https://medium.com/membership)。

您可以在我的[GitHub网站](https://github.com/daniel-manrique/MediumBlog/blob/main/2024-05-24_SpatialAnalysis_Part1.qmd)上找到这篇文章的完整/更新版本。

+   所有图像，除非另有说明，均由作者使用R代码生成。

# 参考文献

1.A. Baddeley, E. Rubak, R. Turner，*空间点模式：使用R的方法论和应用*（Chapman; Hall/CRC Press, London, 2015；[https://www.routledge.com/Spatial-Point-Patterns-Methodology-and-Applications-with-R/Baddeley-Rubak-Turner/p/book/9781482210200/](https://www.routledge.com/Spatial-Point-Patterns-Methodology-and-Applications-with-R/Baddeley-Rubak-Turner/p/book/9781482210200/)）。

2\. D. Manrique-Castano, A. ElAli，[无偏量化小鼠细胞空间分布的点模式分析](https://doi.org/10.1016/j.xpro.2024.102989)。*STAR Protocols*。**5**，102989（2024年）。

3\. D. Manrique-Castano, D. Bhaskar, A. ElAli，《通过空间点模式和拓扑数据分析解剖胶质疤痕形成》（2023），(可在[http://dx.doi.org/10.1101/2023.10.04.560910](http://dx.doi.org/10.1101/2023.10.04.560910)获取)。

4\. P. Bankhead, M. B. Loughrey, J. A. Fernández, Y. Dombrowski, D. G. McArt, P. D. Dunne, S. McQuaid, R. T. Gray, L. J. Murray, H. G. Coleman, J. A. James, M. Salto-Tellez, P. W. Hamilton, QuPath：数字病理图像分析的开源软件。*Scientific Reports*。**7**（2017），doi：[10.1038/s41598–017–17204–5](https://doi.org/10.1038/s41598-017-17204-5)。

5\. D. R. Stirling, M. J. Swain-Bowden, A. M. Lucas, A. E. Carpenter, B. A. Cimini, A. Goodman, CellProfiler 4: 在速度、功能性和可用性方面的改进。*BMC Bioinformatics*。**22** (2021)，doi：[10.1186/s12859–021–04344–9](https://doi.org/10.1186/s12859-021-04344-9)。

6\. P.-C. Bürkner, Brms：一个用于贝叶斯多层次模型的R包，使用Stan。**80** (2017)，doi：[10.18637/jss.v080.i01](https://doi.org/10.18637/jss.v080.i01)。

7\. M. Kay, *tidybayes: 用于贝叶斯模型的整洁数据和几何图形*（2023；[http://mjskay.github.io/tidybayes/](http://mjskay.github.io/tidybayes/)）。
