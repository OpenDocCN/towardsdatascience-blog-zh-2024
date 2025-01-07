# 改善含有大量零值的对象（或细胞）计数分析

> 原文：[https://towardsdatascience.com/improving-the-analysis-of-object-or-cells-counts-with-lots-of-zeros-f343d1430637?source=collection_archive---------6-----------------------#2024-04-27](https://towardsdatascience.com/improving-the-analysis-of-object-or-cells-counts-with-lots-of-zeros-f343d1430637?source=collection_archive---------6-----------------------#2024-04-27)

## 使用brms的障碍模型和零膨胀模型方法

[](https://medium.com/@damanrique?source=post_page---byline--f343d1430637--------------------------------)[![Daniel Manrique-Castano](../Images/06f857ae6e82688113f1089c7f03be88.png)](https://medium.com/@damanrique?source=post_page---byline--f343d1430637--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f343d1430637--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f343d1430637--------------------------------) [Daniel Manrique-Castano](https://medium.com/@damanrique?source=post_page---byline--f343d1430637--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f343d1430637--------------------------------) ·13分钟阅读·2024年4月27日

--

![](../Images/7ca3306348a80ac2c32c3bb56be3f0cd.png)

小鼠大脑中，脑缺血后NeuN和GFAP的染色。[Manrique-Castano et al](https://www.biorxiv.org/content/10.1101/2023.10.04.560910v1)。 (2024) (CC-BY)。

计数是生物医学研究中的一项基本任务，特别是在分析细胞群体时。试想一下，凝视在一个微小的大脑区域内数不清的细胞——有时其数量达到数百甚至数千。然而，在其他区域，这些数量可能会减少到几乎没有。

挑战出现在我们如何分析这些计数值上。对于大数值，假设正态分布的线性模型通常可以作为合理的近似。虽然不是最优的，它们为初步分析提供了一个逻辑框架。然而，当细胞计数较低或主要为零时，情况会发生显著变化。在这种情况下，传统的线性模型（例如我们在GraphPad中运行的t检验）会失效，失去其有效性和相关性。

作为研究人员，我们必须追求更好的方法，超越t检验和方差分析（ANOVA）。本文旨在探索更准确反映我们数据现实的替代统计方法，特别是在处理低计数或零计数时。通过采用更适合的方法，我们可以提高研究结果的精确度，深化对细胞群体的理解。

首先，让我们加载必要的库，并为我们的图表创建一个视觉主题。

```py
library(ggplot2)
library(brms)
library(ggdist)
library(easystats)
library(dplyr)
library(modelr)
library(patchwork)
library(tibble)
library(tidybayes)

logit2prob <- function(logit){
  odds <- exp(logit)
  prob <- odds / (1 + odds)
  return(prob)
}

Plot_theme <- theme_classic() +
  theme(
      plot.title = element_text(size=18, hjust = 0.5, face="bold"),
      plot.subtitle = element_text(size = 10, color = "black"),
      plot.caption = element_text(size = 12, color = "black"),
      axis.line = element_line(colour = "black", size = 1.5, linetype = "solid"),
      axis.ticks.length=unit(7,"pt"),

      axis.title.x = element_text(colour = "black", size = 16),
      axis.text.x = element_text(colour = "black", size = 16, angle = 0, hjust = 0.5),
      axis.ticks.x = element_line(colour = "black", size = 1),

      axis.title.y = element_text(colour = "black", size = 16),
      axis.text.y = element_text(colour = "black", size = 16),
      axis.ticks.y = element_line(colour = "black", size = 1),

      legend.position="right",
      legend.direction="vertical",
      legend.title = element_text(colour="black", face="bold", size=12),
      legend.text = element_text(colour="black", size=10),

      plot.margin = margin(t = 10,  # Top margin
                             r = 2,  # Right margin
                             b = 10,  # Bottom margin
                             l = 10) # Left margin
      ) 
```

# 计数零值有什么问题？

在许多研究中，比如图表中显示的（[*1*](#ref-baraibar2020)），我们会遇到低细胞计数的挑战，特别是在其中一个组——这里标为黑色——似乎被零计数所主导。这种情况在生物医学文献中并不罕见，简单的线性模型通常被用来分析这些数据。

![](../Images/03b18f75422dabf28c42ebb43ddf279f.png)

图1：CD3+细胞，Baraibar等人（2020）（CC-BY）

然而，在低计数或零计数的情况下使用这些模型可能会存在问题。如果没有访问原始数据集——这也是研究人员通常不分享的——就很难全面评估分析的有效性。为了更好地理解这个问题，我们来看一下我自己研究中的一个独立数据集。几年前，我承担了在一个名为室管区（SVZ）的特定脑区内进行缺血事件后BrdU+细胞计数的任务。这一经历让我倾向于评估更合适的统计方法。

这些是数据：

```py
Svz_data <- read.csv("Data/CellCounts.csv")
Svz_data$Hemisphere <- factor(Svz_data$Hemisphere, levels = c("Contralateral", "Ipsilateral"))
head(Svz_data)
```

![](../Images/fc557806dbde71b37b57f919be9b7ea2.png)

我们可以通过可视化看到，对侧半球有大量的零细胞计数。如果我们使用箱型图，可以获得更清晰的角度：

```py
ggplot(Svz_data, aes(x = Hemisphere, y = Cells)) +
  geom_boxplot() +
  labs(x = "Hemisphere", y = "Number of cells", title = "Cells by hemisphere") +
  Plot_theme +
  theme(legend.position = "top", legend.direction = "horizontal")
```

![](../Images/1f2ef5bb688bbce545181496a6029bc4.png)

图2：按半球划分的细胞计数

[图2](#fig-EDA)生动地展示了半球之间在细胞计数上的显著差异。为了研究当我们将典型（而可怕的）线性模型应用于此类数据时会发生什么，我们将通过使用`brms`进行实践演示。这将帮助我们理解在传统框架下，基于正态分布假设对这些变异进行分析时的影响。

在这个示例中，我将拟合一个线性模型，其中因子变量“hemisphere”是唯一的细胞计数预测变量：

```py
lm_Fit <- brm(Cells ~ Hemisphere, 
           data = Svz_data, 
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-19_CountsZeroInflated/lm_Fit.rds",
           file_refit = "never")

# Add loo for model comparison
lm_Fit <- 
  add_criterion(lm_Fit, c("loo", "waic", "bayes_R2"))
```

你敢打赌结果会是什么吗？我们来看看：

![](../Images/ef819bf385242f34194a1aac4ad0180e.png)

如果你愿意，你可以使用`lm`拟合一个频率学派（OLS）模型，肯定会得到相同的结果。在这些结果中，截距代表对侧半球的细胞计数估计，作为我们的参考组。然而，当使用正态分布处理包含大量零计数或接近零值的数据时，会出现显著的不一致性。在这种情况下，模型会“不恰当地”预测我们半球的细胞计数可能是负值，例如-1到-2个细胞，并且置信区间范围为-1.5到3.7。这种预测是根本性错误的，因为它忽视了细胞作为只能取非负整数值的实体的固有性质。

这个问题源于我们的模型在当前形式下无法理解它所处理的数据的真实特征。相反，它仅仅按照我们的指令——尽管是错误的——将数据拟合到线性模型中。这种常见的疏忽常常导致研究人员通过应用t检验和方差分析（ANOVA）进一步加剧问题，从而在一个本质上不合理的模型上叠加额外的分析。作为研究人员，我们必须认识到并利用我们的能力和工具，开发和使用更合适、逻辑上更合理的建模方法，尊重数据的固有特性。

让我们使用伟大的`TidyBayes`包（[*2*](#ref-tidybayes)）绘制结果，作者是伟大的[Matthew Kay](https://www.mjskay.com/)

```py
Svz_data %>%
  data_grid(Hemisphere) %>%
  add_epred_draws(lm_Fit) %>%
  ggplot(aes(x = .epred, y = Hemisphere)) +
  labs(x = "Number of cells") +
  stat_halfeye() +
  geom_vline(xintercept = 0) +
  Plot_theme
```

![](../Images/c06d291722fd2e60a45af8adc86ee81b.png)

图 3：按半球划分的细胞计数的后验分布

如果我们执行`pp_check`来比较观测值与模型预测，我们也可以看到这种不一致性：

![](../Images/2ba8710d00422226c3c7c6fd1a7278fc.png)

图 4：后验预测检查高斯模型

我们再次遇到细胞计数预测低于零的非理性结果。作为科学家，反思我们的模型是否适合用来解释它们所要解释的数据至关重要。这个思考引导我们走向**生成模型**，这些模型的建立前提是它们能够合理地生成观察到的数据。显然，目前使用的线性模型未能满足这一标准。它预测了细胞计数不可能出现的负值。让我们尝试找到一个更好的模型。

# 处理大量零值

零膨胀模型有效地捕捉了以零为主的数据集的细微差别。它通过区分两个不同的过程来运作：1）确定结果是否为零，2）预测非零结果的值。这种双重方法特别适用于提出类似“是否存在细胞，如果有，多少个？”的问题。

对于处理具有大量零的数据集，我们采用如`hurdle_poisson()`和`Zero_inflated_poisson`等模型，这些模型专为标准计数模型（如泊松模型或负二项模型）无法有效应用的场景设计（[*3*](#ref-feng2021a)）。宽泛地说，`hurdle_poisson()`和`Zero_inflated_poisson`之间的一个关键区别在于，后者引入了一个额外的概率成分，专门用于处理零值，从而增强了它们处理零不仅仅是常见而且是显著的情况的能力。我们将在使用`brms`的建模策略中看到这些特性带来的影响。

## 拟合 `hurdle_poisson` 模型

让我们首先在我们的建模方案中使用`hurdle_poisson()`分布：

```py
Hurdle_Fit1 <- brm(Cells ~ Hemisphere, 
           data = Svz_data, 
           family = hurdle_poisson(),
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-19_CountsZeroInflated/Hurdle_Fit1.rds",
           file_refit = "never")

# Add loo for model comparison
Hurdle_Fit1 <- 
  add_criterion(Hurdle_Fit1, c("loo", "waic", "bayes_R2"))
```

让我们使用标准总结函数来看结果。

```py
summary(Hurdle_Fit1)
```

![](../Images/0d23609c27931943a7d384c039567cf6.png)

鉴于这种家族分布，估计值以对数尺度显示（mu = log）。从实际角度看，这意味着对侧脑室下区（SVZ）的细胞数量可以表示为exp(1.11) = 3.03。同样，同侧半球的细胞数量预计为exp(1.07) = 2.91倍。这些结果与我们的预期非常一致，并为两侧半球之间的细胞分布提供了连贯的解释。

此外，“家庭特定参数”中的`hu`参数揭示了观察到零细胞计数的可能性。它表明零发生的概率为38%。这个概率突出了零膨胀模型方法的必要性，并证明了在我们的分析中使用这一方法的合理性。

为了更好地可视化这些发现的意义，我们可以利用`conditional_effects`函数。`brms`包中的这个工具允许我们绘制不同预测变量对响应变量的估计效应，提供清晰的图形表示，展示预测变量如何影响预期的细胞计数。

```py
Hurdle_CE <- 
  conditional_effects(Hurdle_Fit1)

Hurdle_CE <- plot(Hurdle_CE, 
       plot = FALSE)[[1]]

Hurdle_Com <- Hurdle_CE + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Hurdle_CE_hu <- 
  conditional_effects(Hurdle_Fit1, dpar = "hu")

Hurdle_CE_hu <- plot(Hurdle_CE_hu, 
       plot = FALSE)[[1]]

Hurdle_hu <- Hurdle_CE_hu + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Hurdle_Com | Hurdle_hu
```

![](../Images/0dc34c66ed8f38f315e0fdf6d7f771f7.png)

图5：障碍拟合的条件效应

这些图表比我们的第一个模型更合乎逻辑。左侧的图展示了模型的两部分（“mu”和“hu”）。此外，如果该模型合适，我们应该能看到在使用`pp_check`时，预测结果更加一致：

```py
pp_check(Hurdle_Fit1, ndraws = 100) +
  labs(title = "Hurdle regression") +
  theme_classic()
```

![](../Images/2121effaca5730a4b7207ef53287e026.png)

图6：后验预测检查障碍模型

正如预期的那样，我们的模型预测在0处有一个下边界。

## 建模数据的离散性

从[图5](#fig-HurdleCE)右侧图表中观察数据，揭示了我们的实证发现与我们对该主题的理论理解之间的差异。根据已有的知识，我们期望在同侧大脑半球的脑室下区（SVZ）中，非零细胞计数的概率更高，特别是在受伤后。这是因为同侧SVZ通常会成为细胞活动的中心，受伤后会有显著的细胞增殖。我们的数据表明该区域普遍存在非零计数，支持这一生物学预期。

然而，当前模型的预测并未完全与这些见解相符。这种偏差凸显了在统计建模中融入科学理解的重要性。仅仅依赖标准测试而不进行情境适配，可能会导致误导性的结论。

为了解决这个问题，我们可以通过专门调整`hu`参数来优化模型，`hu`代表零发生的概率。这样可以更准确地反映同侧大脑半球SVZ（脑室下区）的预期生物学活动。然后我们建立了第二个障碍模型：

```py
Hurdle_Mdl2 <- bf(Cells ~ Hemisphere, 
                   hu ~ Hemisphere)

Hurdle_Fit2 <- brm(
           formula = Hurdle_Mdl2,
           data = Svz_data, 
           family = hurdle_poisson(),
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-19_CountsZeroInflated/Hurdle_Fit2.rds",
           file_refit = "never")

# Add loo for model comparison
Hurdle_Fit2 <- 
  add_criterion(Hurdle_Fit2, c("loo", "waic", "bayes_R2"))
```

首先让我们看看结果图是否符合我们的假设：

```py
Hurdle_CE <- 
  conditional_effects(Hurdle_Fit2)

Hurdle_CE <- plot(Hurdle_CE, 
       plot = FALSE)[[1]]

Hurdle_Com <- Hurdle_CE + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Hurdle_CE_hu <- 
  conditional_effects(Hurdle_Fit2, dpar = "hu")

Hurdle_CE_hu <- plot(Hurdle_CE_hu, 
       plot = FALSE)[[1]]

Hurdle_hu <- Hurdle_CE_hu + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Hurdle_Com | Hurdle_hu
```

![](../Images/1abbbbecb854b57c1c450cfd57e7d50c.png)

图7：障碍拟合的条件效应2

这种修订后的建模方法似乎是一个实质性的改进。通过专门考虑对侧半球零计数的更高概率（约75%），该模型现在与观测数据和我们的科学知识更为一致。这一调整不仅反映了该区域预期的较低细胞活动，还提高了我们估计的精确度。通过这些变化，模型现在能更细致地解释受伤后细胞动态。让我们查看总结以及`hu`参数的变换（不要看其他的），以便通过我们在开始时创建的`logit2prob` [函数](https://sebastiansauer.github.io/convert_logit2prob/)将其可视化为概率尺度。

```py
logit2prob(fixef(Hurdle_Fit2))
```

![](../Images/eab8d96043a26084cf01440fb870ea1e.png)

尽管细胞数量的估计值相似，`hu`参数（在logit尺度上）告诉我们，在对侧半球观察零计数的概率是：

![](../Images/64254e6b3d45eb207beacb2df19b24aa.png)

相反：

![](../Images/835e1ce50dbb7ba0c7e6563d5a4cf441.png)

显示受损（同侧）半球观察到零细胞计数的概率大幅下降至约0.23%。这是我们估计的一个显著变化。

现在，让我们探讨一下`zero_inflated_poisson()`分布家族是否改变了这些见解。

# 拟合零膨胀泊松模型

由于我们使用`hu`参数建模同侧和对侧半球之间细胞计数的广泛变化，我们也将在`zero_inflated_poisson()`模型中拟合这两个部分。这里，模型的计数部分使用“log”链接，而零膨胀部分则使用“logit”链接。这些是与分布家族相关的链接函数，我们将在此不讨论。

```py
Inflated_mdl1 <- bf(Cells ~ Hemisphere,
                    zi ~ Hemisphere)

Inflated_Fit1 <- brm(
           formula = Inflated_mdl1, 
           data = Svz_data, 
           family = zero_inflated_poisson(),
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-19_CountsZeroInflated/Inflated_Fit.rds",
           file_refit = "never")

# Add loo for model comparison
Inflated_Fit1 <- 
  add_criterion(Inflated_Fit1, c("loo", "waic", "bayes_R2"))
```

在查看结果之前，让我们做一些基本的诊断，以比较观测值和模型预测。

```py
set.seed(8807)

pp_check(Inflated_Fit1, ndraws = 100) +
  labs(title = "Zero-inflated regression") +
  theme_classic()
```

![](../Images/48058f3c848ad77b1ad76deb3726e23a.png)

图8：零膨胀回归的模型诊断

从[图8](#fig-ZeroInflatedDiag1)中，我们可以看到，预测结果与观测数据的偏差与障碍模型类似。因此，到目前为止，我们没有重大的变化。

让我们查看数值结果：

```py
logit2prob(fixef(Inflated_Fit1))
```

在这里，我们确实看到估计值有微小变化。细胞数量的估计相似，可信区间有小幅变化。否则，零数量的参数似乎经历了更大的变化。让我们看看这是否会影响我们的结论：

![](../Images/b0441637655fcc4cf8ea62529c43f93c.png)

表示在参考半球观察到零计数的概率大约为77%。现在，对于受损半球，我们有：

![](../Images/933eaff53d574b13b9cc95a322868a03.png)

再次，这表明在受伤（同侧）半球观察到零细胞计数的情况大幅减少。根据科学知识来评估结果，我认为这两个模型提供了类似合理的预测。我们的`zero_inflated_poisson`模型的图形结果如下：

```py
Inflated_CE <- 
  conditional_effects(Inflated_Fit1)

Inflated_CE <- plot(Inflated_CE, 
       plot = FALSE)[[1]]

Inflated_Com <- Inflated_CE + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Inflated_CE_zi <- 
  conditional_effects(Inflated_Fit1, dpar = "zi")

Inflated_CE_zi <- plot(Inflated_CE_zi, 
       plot = FALSE)[[1]]

Inflated_zi <- Inflated_CE_zi + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")

Inflated_Com | Inflated_zi
```

![](../Images/c63a4acd4ca85e86eed8b6ebb4db3f29.png)

图9：膨胀拟合的条件效应

结果似乎与hurdle模型相似。然而，我们可以观察到，对侧半球为零的概率估计在`zero_inflated_poisson`模型中更为严格。原因正如我一开始所解释的，零膨胀模型比`hurdle_poisson`分布定位了更多的零的概率。让我们比较一下这些模型，结束这篇文章。

# 模型比较

我们使用`loo`包执行留一交叉验证（[*4*](#ref-loo)，[*5*](#ref-vehtari2016)）。WAIC（[*6*](#ref-gelman2013)）是你可以在[这篇文章](https://medium.com/towards-data-science/do-not-over-think-about-outliers-use-a-student-t-distribution-instead-b6c584b91d5c)中探索的另一种方法。

```py
loo(Hurdle_Fit2, Inflated_Fit1)
```

![](../Images/f76a5e8c28e127995d0b6817b7b7c593.png)

即使我们的模型预测相似，"pareto-k-diagnostic"也表明`hurdle_poisson`模型有一个`非常差`的值，而这个值在`zero_inflated_poisson()`模型中转变为`差`。我判断这个“差和非常差”值可能是我们在[图2](#fig-EDA)中看到的对侧计数的极端观测值所导致的黑点。我们可以拟合另一个不包括该值的模型并重新评估结果。然而，这个程序的目标只是评估这个极端观测值的影响，并告知观众估计结果中可能存在的偏差（应该完全报告）。这是对数据进行透彻且透明解读的方式。我还写了一篇关于极端值的[文章](https://medium.com/towards-data-science/do-not-over-think-about-outliers-use-a-student-t-distribution-instead-b6c584b91d5c)，可能对你有兴趣。

我得出结论，这篇文章中执行的建模方法比基于高斯分布的简单线性模型更合适。科学家有责任使用最合适的统计建模工具，这是道德和专业的责任。幸运的是，`brms`是存在的！

我非常感谢你的评论或反馈，告诉我这段旅程是否对你有帮助。如果你想获得更多关于数据科学及其他主题的优质内容，你可以考虑成为[medium会员](https://medium.com/membership)。

将来，你可以在我的[GitHub网站](https://github.com/daniel-manrique/MediumBlog/blob/main/2024-04-19_CountsZeroInflated.qmd)找到这篇文章的更新版本。

+   除非另有说明，否则所有图像均是使用所展示的R代码生成的。

# 参考文献

1.I. Baraibar, M. Roman, M. Rodríguez-Remírez, I. López, A. Vilalta, E. Guruceaga, M. Ecay, M. Collantes, T. Lozano, D. Alignani, A. Puyalto, A. Oliver, S. Ortiz-Espinosa, H. Moreno, M. Torregrosa, C. Rolfo, C. Caglevic, D. García-Ros, M. Villalba-Esparza, C. De Andrea, S. Vicent, R. Pío, J. J. Lasarte, A. Calvo, D. Ajona, I. Gil-Bazo, [Id1与PD-1联合阻断通过刺激PD-L1表达和肿瘤浸润的CD8+ T细胞抑制KRAS突变型肺癌的肿瘤生长和存活](https://doi.org/10.3390/cancers12113169)。*癌症*。**12**，3169（2020）。

2\. M. Kay, *tidybayes: 适用于贝叶斯模型的整洁数据和几何图形*（2023；[http://mjskay.github.io/tidybayes/](http://mjskay.github.io/tidybayes/)）。

3\. C. X. Feng, 零膨胀计数数据建模的零膨胀模型与障碍模型比较。*统计分布与应用杂志*。**8**（2021），doi：[10.1186/s40488–021–00121–4](https://doi.org/10.1186/s40488-021-00121-4)。

4\. A. Vehtari, J. Gabry, M. Magnusson, Y. Yao, P.-C. Bürkner, T. Paananen, A. Gelman, Loo：高效的留一法交叉验证和WAIC用于贝叶斯模型（2022）（可在[https://mc-stan.org/loo/](https://mc-stan.org/loo/)获取）。

5\. A. Vehtari, A. Gelman, J. Gabry, [使用留一法交叉验证和WAIC进行贝叶斯模型的实用评估](https://doi.org/10.1007/s11222-016-9696-4)。*统计与计算*。**27**，1413–1432（2016）。

6\. A. Gelman, J. Hwang, A. Vehtari, [理解贝叶斯模型的预测信息准则](https://doi.org/10.1007/s11222-013-9416-2)。*统计与计算*。**24**，997–1016（2013）。
