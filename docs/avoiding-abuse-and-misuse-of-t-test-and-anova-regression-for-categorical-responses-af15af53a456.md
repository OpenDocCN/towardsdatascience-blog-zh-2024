# 避免滥用和误用T检验和方差分析：分类响应的回归分析

> 原文：[https://towardsdatascience.com/avoiding-abuse-and-misuse-of-t-test-and-anova-regression-for-categorical-responses-af15af53a456?source=collection_archive---------2-----------------------#2024-04-07](https://towardsdatascience.com/avoiding-abuse-and-misuse-of-t-test-and-anova-regression-for-categorical-responses-af15af53a456?source=collection_archive---------2-----------------------#2024-04-07)

## 使用基于 brms 的贝叶斯回归方法

[](https://medium.com/@damanrique?source=post_page---byline--af15af53a456--------------------------------)[![Daniel Manrique-Castano](../Images/06f857ae6e82688113f1089c7f03be88.png)](https://medium.com/@damanrique?source=post_page---byline--af15af53a456--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af15af53a456--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af15af53a456--------------------------------) [Daniel Manrique-Castano](https://medium.com/@damanrique?source=post_page---byline--af15af53a456--------------------------------)

·Published in [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af15af53a456--------------------------------) ·11 min read·Apr 7, 2024

--

在神经科学及其他生物医学科学中，常使用行为测试来评估对实验条件或治疗的反应。我们可以评估许多方面，从基本的运动和探索行为到记忆和学习。这些变量中的许多是连续的（数值型）反应，即它们可以在给定范围内取（有限或无限的）值。开放场中的时间、动物体重或大脑区域内的细胞数量是一些例子。

然而，在我们的实验中还有其他类型的变量需要记录。一个非常常见的变量是有序的类别变量，也叫做顺序变量。这些是具有自然顺序的类别变量，类似于我们在常见的调查中对陈述做出同意或不同意的回答，0表示强烈不同意，5表示强烈同意。为了方便在印刷或数字数据表中记录这些变量，我们通常将它们编码为数字。这就是在小鼠模型的脑缺血研究中使用的5分贝德森评分（[*1*](#ref-bieber2019)），其编码如下：

- 0 = 无可观察的缺陷

- 1 = 前肢屈曲

- 2 = 前肢屈曲和对侧推力的抗拒减少

- 3 = 转圈

- 4 = 转圈并围绕头尾轴旋转

- 5 = 无自发运动

请注意，数字仅仅是简单的约定。我们也可以使用a、b、c、d、e；优秀、良好、不太好、差、非常差、几乎死亡；等等。我认为强调这一点的显而易见性并不自负。

> 然而，令人惊讶的是，[图1](#fig-sample)代表了科学家在这个领域多年来一直在实践的一种特定且普遍的恶习：他们使用t检验和方差分析（ANOVA）来分析这种有序分类变量。

![](../Images/dfe23d879272aa75262729034a662f78.png)

图1：左：Onufriev等人（2021）的神经评分（CC-BY）。右：Liu等人（2022）的神经评分（CC-BY）。

我仍然无法找到一个合乎逻辑的解释，说明为什么那么多作者、审稿人和编辑对这种情况感到舒适。它不仅不合逻辑，更重要的是，它导致了偏倚的效应量、低检出率和I型错误率等问题 ([*2*](#ref-bürkner2019))。

当我扮演审稿人2的角色，并强调这一点，问作者们为什么要用专为处理连续数值变量设计的统计检验来评估分类响应时，我得到的是一长串遵循这种非理性做法的已发布文章。于是我最终找到了他们为什么这么做的答案：

> 这是Gerd Gigerenzer（2004）所说的“盲目统计的仪式” ([*3*](#ref-gigerenzer2004))。

事实上，我们大多数科学家对于我们如何处理数据几乎没有概念，只是在简单地重复世界各地实验室代代相传的常见错误做法。

在本文中，我们接下来将探讨使用`R`、`brms`（[*4*](#ref-brms-2)）和`tidyverse`（[*5*](#ref-tidyverse)）中的元素来分析有序分类变量的更可行的替代方法。

# 面对盲目统计的仪式

为了避免盲目统计的仪式，我们将通过肉眼判断数据点并将它们组织成表格，重新创建图1中Liu等人（2022）数据集：

```py
# We Define the observations
observations <- list(
  Sham = c(rep(0, 7)),
  tMCAO = c(rep(2, 3), rep(3, 2), rep(4, 2)),
  tMCAO_C = c(rep(1, 3), rep(2, 3), rep(3, 1))
)

# We create an empty data frame to populate
df <- data.frame(Group = character(), Response = integer())

# We populate the data frame
for (group in names(observations)) {
  for (response in unique(observations[[group]])) {
    df <- rbind(df, data.frame(Group = rep(group, sum(observations[[group]] == response)), 
                               Response = rep(response, sum(observations[[group]] == response))))
  }
}

head(df)
```

![](../Images/0f4acb0cfd1ce8d29027d0a591b80196.png)

如果此时你查看R代码（在R-studio中），你会注意到变量`Response`被识别为从0到4的数字。当然，我们完全清楚这个响应不是数值型的，而是一个有序（序数）分类变量。因此，我们显式地进行转换：

```py
df$Response <- factor(df$Response, levels = c("0", "1", "2", "3", "4"), ordered = TRUE)

str(df)
```

![](../Images/b51543a383becaf2fd36a51d2f134e5a.png)

现在我们可以验证它被识别为有序分类变量。有了这一点，我们可以轻松地进入可视化和建模阶段。

# 探索性数据可视化

首先，让我们加载必要的库并为我们的图表创建一个视觉主题。

```py
library(ggplot2)
library(brms)
library(ggdist)
library(easystats)
library(dplyr)
library(tibble)

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

可视化分类数据的一个简单方法是使用条形图。

```py
ggplot(df, aes(x = factor(Response), fill = Group)) +
  geom_bar(position = "dodge") +
  labs(x = "Response", y = "Count", title = "Response by Group") +
  theme_minimal() +
  scale_fill_brewer(palette = "Set1") +
  Plot_theme +
theme(legend.position = "top", legend.direction = "horizontal")
```

![](../Images/562e64721f5f471abd117d05802250b4.png)

图2：按组着色的响应

[图2](#fig-Fig2)展示了每组的频率。这比箱型图更符合分类变量的逻辑，而箱型图更适用于连续数值变量。现在让我们进行回归分析，揭示这个数据集的奥秘（如果有的话）。

# 使用brms拟合有序回归

我们将使用`brms`来拟合一个累积模型。这个模型假设神经学得分Y来自一个（假设是潜在的、但不可观察或测量的）连续变量Y˜的分类([*2*](#ref-bürkner2019))。像大多数`brms`教程中一样，我必须为跳过“先验”问题表示歉意。让我们假设一种“我不知道”的心态，并让默认的（平坦的）`brms`先验来做脏活。

我们通过遵循常规公式语法并添加`cumulative("probit")`作为家族来拟合`cumulative`模型（假设潜在变量及其对应的误差项呈正态分布）。我们只有一个预测变量，即每个动物所属的实验组。

```py
Ordinal_Fit <- brm(Response ~ Group, 
           data = df, 
           family = cumulative("probit"),
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-03_UseAndAbuseANOVA/Ordinal_Fit.rds",
           file_refit = "never")

# Add loo for model comparison
Ordinal_Fit <- 
  add_criterion(Ordinal_Fit, c("loo", "waic", "bayes_R2"))
```

在查看结果之前，让我们先进行一些模型诊断，以比较观测值和模型预测值。

# 模型诊断

```py
pp_check(Ordinal_Fit, ndraws = 100) +
  labs(title = "Ordinal regression") +
  theme_classic()
```

![](../Images/5ae2a42533a599da282f6f969168a389.png)

图3：有序回归的模型诊断

从[图3](#fig-OrdinalDiag1)中，我推测这些偏差是由于各组得分的分布（方差）不均匀所导致的。稍后，我们将看看预测方差是否能得到更好的估计。现在，我们可以继续进行。

# 检查有序回归的结果

让我们使用`bayestestR`包中的`describe_posterior`函数来查看后验分布（[*6*](#ref-bayestestR)），作为典型的`summary`函数的替代方法。

```py
describe_posterior(Ordinal_Fit,
                   centrality = "mean",
                   dispersion = TRUE,
                   ci_method = "HDI",
                   test = "rope",
                   )
```

![](../Images/ec92131815f91c915210971045a9a935.png)

在这个模型中，阈值（得分阈值）被标记为“截距”，适用于我们的基线“假处理”条件。“GrouptMCAO”和“GrouptMCAO_C”的系数表示在潜在Y˜尺度上与假处理动物的差异。因此，我们看到“GrouptMCAO”在潜在Y˜尺度上得分高出8.6个标准差。

我想指出一个至关重要（且并非微不足道）的问题，作为未来文章的提醒（敬请关注）。在我看来，比较一个没有分布的组（在所有情况下为0），比如假处理组，与一个有分布的组（实验组）是没有意义的。如果仔细思考，这个过程的目的就是空洞的。但是，让我们遵循现代科学文化的思维方式，不再过多纠结这个问题。

当然，如果我们通过`brms`的`conditional effects`功能以可视化的方式查看，区分各组之间的差异就变得更加可行。

```py
Ordinal_CondEffects <- 
  conditional_effects(Ordinal_Fit, "Group", categorical = TRUE)

Ordinal_CondEffects <- plot(Ordinal_CondEffects, 
       plot = FALSE)[[1]]

Ordinal_CondEffects + 
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")
```

![](../Images/aea59d20dceceba49b75a796eea90fb4.png)

图4：有序模型的条件效应

很奇怪（或者说，可能从MCMC仿真框架来看并不那么奇怪），模型估计虚拟组可能具有1的值，尽管我们在数据中没有看到这一点。一种可能的原因是模型对所有组估计了一个共同的方差。我们稍后会看看当我们将方差作为响应来建模时，这一方面是否发生变化。

否则，对于 tMCAO 和 tMCO_C 组获得的估计值与数据的匹配度要高得多。这使我们能够做出更精确的陈述，而不是运行 ANOVA（对于有序变量来说是不正确的）并说“组与组之间存在显著差异”，这种说法毫无意义。例如，模型告诉我们 tMCAO 组的 2-4 分数有相似的概率（约 25%）。对于 tMCAO_C 组情况不同，在神经学评分为 2 的概率较高（尽管存在相当大的不确定性），而其他分数的概率较低。如果我面对这个数据集和这个模型，我会声称 tMCAO_C 组表现出较少神经损伤（基于 1 和 2 分数）的概率高于 tMCAO 组。

我们能否得到量化不同组之间评分差异及其不确定性的精确数值？当然可以！我们可以使用 `emmeans` 包（[*7*](#ref-emmeans)）。但这将是另一个帖子的话题（敬请期待）。

# 将方差作为响应变量

对于这种累积模型，没有 `sigma` 参数。相反，为了考虑不等方差，我们需要使用一个叫做 `disc` 的参数。关于这一方面的更多内容，请参见 `brms` 的创建者 Paul-Christian Bürkner 的有序回归教程（[*2*](#ref-bürkner2019)）。

```py
Ordinal_Mdl2 <- bf (Response ~ Group) +
                     lf(disc ~  0 + Group, cmc = FALSE)

Ordinal_Fit2 <- brm( 
           formula = Ordinal_Mdl2,
           data = df, 
           family = cumulative("probit"),
           # seed for reproducibility purposes
           seed = 8807,
           control = list(adapt_delta = 0.99),
           # this is to save the model in my laptop
           file    = "Models/2024-04-03_UseAndAbuseANOVA/Ordinal_Fit2.rds",
           file_refit = "never")

# Add loo for model comparison
Ordinal_Fit2 <- 
  add_criterion(Ordinal_Fit2, c("loo", "waic", "bayes_R2"))
```

# 模型诊断

我们按之前的方法进行模型诊断：

```py
pp_check(Ordinal_Fit2, ndraws = 100) +
  labs(title = "Student-t") +
  theme_classic()
```

![](../Images/a0cccc3b90e1d782d3fd4b77a5ceaa29.png)

图 5：我们的模型诊断，预测方差

[图 5](#fig-OrdinalDiag2)显示了我的预期没有得到满足。将方差作为响应变量纳入模型并没有改善数据拟合。趋势保持不变，但预测结果仍然存在显著差异。然而，这是我们可以考虑作为**生成模型**的另一种候选模型。

# 检查我们新模型的结果

我们可视化了该模型的后验分布：

```py
describe_posterior(Ordinal_Fit2,
                   centrality = "mean",
                   dispersion = TRUE,
                   ci_method = "HDI",
                   test = "rope",
                   )
```

![](../Images/2b05941efb854bc676667692429f78ba.png)

我们可以看到与第一个模型相比，系数有了有意义的差异。 “GrouptMCAO”的系数从 8.6 增加到 15.9，而“GrouptMCAO_C”的系数从 7 增加到 10.8。毫无疑问，这个模型给了我们一个不同的结果。否则，方差项以“disc_GrouptMCAO”和“disc_GrouptMCAO_C”的名称呈现。我们可以看到，这两个方差与我们的“假手术”基线有很大的不同。

让我们绘制结果：

```py
Ordinal_CondEffects2 <- 
  conditional_effects(Ordinal_Fit2, categorical = TRUE)

Ordinal_CondEffects2 <- plot(Ordinal_CondEffects2, 
       plot = FALSE)[[1]]

Ordinal_CondEffects2 +
  Plot_theme +
  theme(legend.position = "bottom", legend.direction = "horizontal")
```

![](../Images/bbc2d410397bbf402c3c31daf1979d7e.png)

图 6：我们的模型中，包括方差作为响应的条件效应

与我的预期相反，该模型仍然预测假手术组的动物有小概率得分为1。我们在这里确认的是，这一预测并不基于（错误的）假设，即所有组的方差相同。然而，在这个框架（序数回归）中，这仍然是一个合逻辑的预测（而非不理性），基于阈值的假设。如果我们参考Richard McElreath的《统计思维》([*8*](#ref-mcelreath2020k))，我们会发现猴子拉杠的情况也相同。拟合一个更受约束的模型将需要使用信息先验。将这个话题留到未来的帖子中。我知道我在这里做了三个承诺，但我会履行它们。

在这个模型中，tMCAO组不同结果的概率略有变化。然而，鉴于高不确定性，我不会根据这个模型改变我对该组表现的结论。另一方面，tMCAO_C组的预测变化并不明显，不容易被眼睛察觉。让我们通过比较这两个模型来结束这篇博客文章。

# 模型比较

我们使用`loo`包（[*9*](#ref-loo)，[*10*](#ref-vehtari2016)）进行模型比较，以实现留一法交叉验证。对于使用WAIC标准的替代方法（[*11*](#ref-gelman2013)），我建议您阅读[这篇文章](https://medium.com/towards-data-science/do-not-over-think-about-outliers-use-a-student-t-distribution-instead-b6c584b91d5c)，它同样由[TDS编辑](https://medium.com/u/7e12c71dfa81?source=post_page---user_mention--af15af53a456--------------------------------)发布。

```py
loo(Ordinal_Fit, Ordinal_Fit2)
```

在这个方案下，模型的表现非常相似。实际上，第一个模型在样本外预测上略优。考虑到方差并没有在这种特定情况下提供太大帮助，（或许）依赖信息先验可以开启科学推断的下一步。

如果您愿意，欢迎提供您的评论或反馈，让我知道这段旅程对您是否有帮助。如果您想获得更多关于数据科学和其他主题的优质内容，您可以考虑成为[medium会员](https://medium.com/membership)。

未来，您可能会在我的[GitHub网站](https://github.com/daniel-manrique/MediumBlog/blob/main/2024-04-03_UseAndAbuseANOVA.qmd)上找到这篇文章的更新版本。

# 参考文献

1.M. Bieber, J. Gronewold, A.-C. Scharf, M. K. Schuhmann, F. Langhauser, S. Hopp, S. Mencl, E. Geuss, J. Leinweber, J. Guthmann, T. R. Doeppner, C. Kleinschnitz, G. Stoll, P. Kraft, D. M. Hermann, [小鼠中脑动脉闭塞后神经学评分的有效性和可靠性](https://doi.org/10.1161/strokeaha.119.026652)。*Stroke*。**50**，2875–2882（2019年）。

2.P.-C. Bürkner, M. Vuorre, [心理学中的序数回归模型：教程](https://doi.org/10.1177/2515245918823199)。*Advances in Methods and Practices in Psychological Science*。**2**，77–101（2019年）。

3\. G. Gigerenzer, [无知统计学](https://doi.org/10.1016/j.socec.2004.09.033)。*社会经济学期刊*。**33**，587–606（2004年）。

4\. P.-C. Bürkner, Brms：一个使用Stan的贝叶斯多层次模型的R包。**80**（2017年），doi：[10.18637/jss.v080.i01](https://doi.org/10.18637/jss.v080.i01)。

5\. H. Wickham, M. Averick, J. Bryan, W. Chang, L. D. McGowan, R. François, G. Grolemund, A. Hayes, L. Henry, J. Hester, M. Kuhn, T. L. Pedersen, E. Miller, S. M. Bache, K. Müller, J. Ooms, D. Robinson, D. P. Seidel, V. Spinu, K. Takahashi, D. Vaughan, C. Wilke, K. Woo, H. Yutani, [欢迎使用tidyverse](https://doi.org/10.21105/joss.01686)。**4**，1686（2019年）。

6\. D. Makowski, M. S. Ben-Shachar, D. Lüdecke, [bayestestR：描述贝叶斯框架下效应及其不确定性、存在性与显著性](https://doi.org/10.21105/joss.01541)。**4**，1541（2019年）。

7\. R. V. Lenth, Emmeans：估计的边际均值，亦即最小二乘均值（2023年）（可在[https://CRAN.R-project.org/package=emmeans](https://CRAN.R-project.org/package=emmeans)查看）。

8\. R. McElreath，*统计思维*（Chapman；Hall/CRC，2020年；[http://dx.doi.org/10.1201/9780429029608](http://dx.doi.org/10.1201/9780429029608)）。

9\. A. Vehtari, J. Gabry, M. Magnusson, Y. Yao, P.-C. Bürkner, T. Paananen, A. Gelman, Loo：用于贝叶斯模型的高效留一交叉验证和WAIC（2022年）（可在[https://mc-stan.org/loo/](https://mc-stan.org/loo/)查看）。

10\. A. Vehtari, A. Gelman, J. Gabry, [实用贝叶斯模型评估方法：使用留一交叉验证和WAIC](https://doi.org/10.1007/s11222-016-9696-4)。*统计学与计算*。**27**，1413–1432（2016年）。

11\. A. Gelman, J. Hwang, A. Vehtari, [理解贝叶斯模型的预测信息准则](https://doi.org/10.1007/s11222-013-9416-2)。*统计学与计算*。**24**，997–1016（2013年）。
