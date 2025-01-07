# Welch t检验：比较两个方差不等的总体均值的可靠方法

> 原文：[https://towardsdatascience.com/welchs-t-test-the-reliable-way-to-compare-2-population-means-with-unequal-variances-bbf7a62967bc?source=collection_archive---------6-----------------------#2024-06-14](https://towardsdatascience.com/welchs-t-test-the-reliable-way-to-compare-2-population-means-with-unequal-variances-bbf7a62967bc?source=collection_archive---------6-----------------------#2024-06-14)

## 发现为什么即使方差不同，Welch t检验也是进行准确统计比较的首选方法。

[](https://vitorihaldijiran.medium.com/?source=post_page---byline--bbf7a62967bc--------------------------------)[![Vito Rihaldijiran](../Images/bc704b6dbbe4c36c5d35d1b83810094f.png)](https://vitorihaldijiran.medium.com/?source=post_page---byline--bbf7a62967bc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bbf7a62967bc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bbf7a62967bc--------------------------------) [Vito Rihaldijiran](https://vitorihaldijiran.medium.com/?source=post_page---byline--bbf7a62967bc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bbf7a62967bc--------------------------------) ·阅读时间 8 分钟·2024年6月14日

--

![](../Images/496b36f28f38276b8d3995b7d278c81d.png)

图片由 [Simon Maage](https://unsplash.com/@simonmaage) 提供，来源于 [Unsplash](https://unsplash.com/photos/two-pizzas-sitting-on-top-of-plates-on-a-table-eL_eKs7HkLU)

## 第一部分：背景

在我研究生的第一学期，我有机会选修了课程 STAT7055：商业与金融入门统计学。在整个课程中，我确实有时感到有些疲惫，但我获得的关于在不同情境下应用各种统计方法的知识，真的是无价的。在第8周的讲座中，有一个非常有趣的内容引起了我的注意，特别是比较两个总体时的假设检验概念。我发现了解如何根据样本是独立的还是配对的来决定方法的不同，以及在我们知道或不知道两个总体的方差的情况下该如何操作，甚至如何进行两个比例的假设检验，都是非常吸引人的。然而，有一个方面是材料中没有涉及的，它让我一直在思考如何处理这种特定情境，那就是在方差不相等的情况下，如何进行两个总体均值的假设检验，这就是**Welch t检验**。

为了理解Welch t检验是如何应用的，我们可以通过一个数据集来探讨这个示例案例。这个过程的每个阶段都涉及使用来自真实世界的数据集。

## 第二部分：数据集

我使用的数据集包含关于世界农业供应与需求估算（WASDE）的实际数据，并定期更新。WASDE数据集由世界农业展望委员会（WAOB）编制。这是一个每月发布的报告，提供关于小麦、大米、粗粮、油籽和棉花等全球各地区以及美国的年度预测。此外，数据集还涵盖了美国的糖、肉类、家禽、鸡蛋和牛奶的预测。该数据源自NASDAQ网站，您可以在这里免费访问：[WASDE数据集](https://data.nasdaq.com/databases/WASDE)。数据集中有三个子集，但我只使用第一个，即供应与需求数据。列定义可以在这里查看：

![](../Images/8a10961af52d6fff8213aa7d77495e7f.png)

图1：由[NASDAQ](https://data.nasdaq.com/databases/WASDE)定义的列

我将使用来自特定地区、商品和项目的两个不同样本来简化测试过程。此外，我们将使用R编程语言进行端到端的过程。

现在让我们进行适当的数据准备：

```py
library(dplyr)

# Read and preprocess the dataframe
wasde_data <- read.csv("wasde_data.csv") %>%
  select(-min_value, -max_value, -year, -period) %>%
  filter(item == "Production", commodity == "Wheat")

# Filter data for Argentina and Australia
wasde_argentina <- wasde_data %>%
  filter(region == "Argentina") %>%
  arrange(desc(report_month))

wasde_oz <- wasde_data %>%
  filter(region == "Australia") %>%
  arrange(desc(report_month))
```

我将两个样本分为两个不同的区域，即阿根廷和澳大利亚。重点是小麦商品的生产。

现在我们准备好了。但等一下..

在进一步探讨Welch t检验的应用之前，我不禁想知道，为什么有必要检验两个总体方差是否相等。

## 第三部分：检验方差的平等性

在进行假设检验以比较两个总体均值时，如果我们不了解总体方差，那么确认方差的平等性至关重要，以便选择适当的统计检验。如果方差相同，我们选择合并方差的t检验；否则，我们可以使用Welch的t检验。这个重要步骤保证了结果的准确性，因为使用不正确的检验可能会导致错误结论，从而增加I型和II型错误的风险。通过检验方差是否平等，我们确保假设检验过程基于准确的假设，从而得出更可靠和有效的结论。

那么我们如何检验两个总体方差呢？

我们需要生成如下的两个假设：

![](../Images/1badb6467e2577b9c1e5d5e12b045426.png)

图2：作者对检验方差平等性的零假设与备择假设

经验法则非常简单：

1.  如果检验统计量落入拒绝区域，那么拒绝H0或零假设。

1.  否则，我们无法拒绝H0或零假设。

我们可以这样设定假设：

```py
# Hypotheses: Variance Comparison
h0_variance <- "Population variance of Wheat production in Argentina equals that in Australia"
h1_variance <- "Population variance of Wheat production in Argentina differs from that in Australia"
```

现在我们应该进行检验统计量的计算。那么，我们如何得到这个检验统计量呢？我们使用**F检验**。

F 检验是一种统计检验，用于比较两个样本的方差或多个样本之间方差的比率。检验统计量 F 是一个随机变量，用于确定在零假设成立且假设误差项符合常规假设的条件下，所测试数据是否具有 F 分布。

![](../Images/cc246bde9db07f87f7f2c5037cc78124.png)

图 3：[维基百科](https://en.wikipedia.org/wiki/F-distribution)绘制的 F 分布概率密度函数（PDF）示意图

我们可以通过以下方式计算检验统计量值，即**通过两样本方差相除**：

![](../Images/939a42400c131cb5b197645d6c10bf33.png)

图 4：作者绘制的 F 检验公式

拒绝区域是：

![](../Images/105d6c38ba508ff4c923308c85641cdd.png)

图 5：作者绘制的 F 检验拒绝区域

其中，n 是样本大小，alpha 是显著性水平。所以，当 F 值落入拒绝区域时，我们拒绝零假设。

但是……

关键是：样本 1 和样本 2 的标记其实是随机的，所以**每次都要确保将较大的样本方差放在上面**。这样，我们的 F 统计量始终大于 1，并且**我们只需参考上界来在显著性水平 α 下拒绝 H0**。

我们可以通过以下方式进行：

```py
# Calculate sample variances
sample_var_argentina <- var(wasde_argentina$value)
sample_var_oz <- var(wasde_oz$value)

# Calculate F calculated value
f_calculated <- sample_var_argentina / sample_var_oz
```

我们将使用 5% 的显著性水平（0.05），因此决策规则是：

```py
# Define significance level and degrees of freedom
alpha <- 0.05
alpha_half <- alpha / 2
n1 <- nrow(wasde_argentina)
n2 <- nrow(wasde_oz)
df1 <- n1 - 1
df2 <- n2 - 1

# Calculate critical F values
f_value_lower <- qf(alpha_half, df1, df2)
f_value_upper <- qf(1 - alpha_half, df1, df2)

# Variance comparison result
if (f_calculated > f_value_lower & f_calculated < f_value_upper) {
  cat("Fail to Reject H0: ", h0_variance, "\n")
  equal_variances <- TRUE
} else {
  cat("Reject H0: ", h1_variance, "\n")
  equal_variances <- FALSE
}
```

结果是**我们在 5% 显著性水平下拒绝零假设**，换句话说，从这次检验中我们认为两个总体的方差不相等。现在我们知道为什么我们应该使用 Welch t 检验而不是合并方差 t 检验。

## 第四部分：主要内容，Welch t 检验

Welch t 检验，也称为 Welch 不等方差 t 检验，是一种用于比较两个独立样本均值的统计方法。与标准的合并方差 t 检验假设方差相等不同，Welch t 检验更为稳健，因为它不做这一假设。这一自由度的调整使得我们可以更精确地评估两个样本均值之间的差异。由于不假设方差相等，Welch t 检验在处理现实世界数据时能提供更可靠的结果，尤其是在方差不相等的情况下。由于其适应性和可靠性，它成为了首选方法，确保即使方差相等假设不成立，统计分析得出的结论依然有效。

检验统计量公式为：

![](../Images/8e868f241b0b45b6848abeb41ea68fac.png)

图 6：作者绘制的 Welch t 检验的检验统计量公式

其中：

![](../Images/a4f7446496cfeaeca33ea42a07ef1bdf.png)

自由度可以这样定义：

![](../Images/1f96a4ce53fcb1a6f40483856d451659.png)

图 7：作者绘制的自由度公式

Welch t 检验的拒绝区域取决于选择的显著性水平，以及检验是单尾还是双尾。

**双尾检验**：如果检验统计量|t|的绝对值大于t分布在ν自由度下的临界值（在α/2显著性水平下），则拒绝原假设。

+   ∣t∣>tα/2,ν​

**单尾检验**：如果检验统计量t大于t分布在ν自由度下的临界值（适用于上尾检验），或者t小于下尾检验的负临界值，则拒绝原假设。

+   **上尾检验**：t > tα,ν

+   **下尾检验**：t < −tα,ν

那么让我们做一个**单尾Welch t检验**的例子。

让我们生成假设：

```py
h0_mean <- "Population mean of Wheat production in Argentina equals that in Australia"
h1_mean <- "Population mean of Wheat production in Argentina is greater than that in Australia"
```

这是**上尾检验**，因此拒绝区域为：t > tα,ν

并且使用上述公式，保持相同的显著性水平（0.05）：

```py
# Calculate sample means
sample_mean_argentina <- mean(wasde_argentina$value)
sample_mean_oz <- mean(wasde_oz$value)

# Welch's t-test (unequal variances)
s1 <- sample_var_argentina
s2 <- sample_var_oz
t_calculated <- (sample_mean_argentina - sample_mean_oz) / sqrt(s1/n1 + s2/n2)
df <- (s1/n1 + s2/n2)^2 / ((s1^2/(n1^2 * (n1-1))) + (s2^2/(n2^2 * (n2-1))))
t_value <- qt(1 - alpha, df)

# Mean comparison result
if (t_calculated > t_value) {
  cat("Reject H0: ", h1_mean, "\n")
} else {
  cat("Fail to Reject H0: ", h0_mean, "\n")
}
```

结果是**我们在5%的显著性水平下未能拒绝H0，因此阿根廷的小麦生产量的总体均值等于澳大利亚的总体均值。**

这就是如何进行Welch t检验。现在轮到你了。祝你实验愉快！

## 第五部分：结论

在进行假设检验时，比较两个总体均值时，首先检查方差是否相等非常重要。这个初步步骤至关重要，因为它有助于决定使用哪种统计检验，确保结果的准确性和可靠性。如果方差确实相等，可以继续使用标准的合并方差t检验。然而，如果方差不相等，建议使用Welch的t检验。

Welch的t检验提供了一种强有力的解决方案，用于在方差不相等的情况下比较均值。通过调整自由度以适应不均匀的方差，Welch的t检验提供了一个更精确、可靠的评估，能够判断两个样本均值之间差异的统计显著性。其适应性使得它在许多实际情况中非常受欢迎，尤其是在样本大小和方差可能差异很大的情况下。

总结来说，检查方差是否相等，并在需要时使用Welch的t检验，可以确保假设检验的准确性。这种方法减少了第一类错误和第二类错误的发生概率，从而得出更可靠的结论。通过根据方差的相等性选择适当的检验，我们可以自信地分析结果，并根据实证证据做出明智的决策。

## 参考资料

+   *纳斯达克数据链接*。（无日期）。*纳斯达克数据链接*。检索于2024年6月14日，来源：[https://data.nasdaq.com](https://data.nasdaq.com)

+   *统计中的F检验*。（2024年6月14日）。*统计中的F检验*。[https://www.geeksforgeeks.org/f-test/](https://www.geeksforgeeks.org/f-test/)

+   *两个独立样本方差不等（Welch检验）— ENV710统计回顾网站*。（无日期）。*两个独立样本方差不等（Welch检验）— ENV710统计回顾网站*。于2024年6月14日从[https://sites.nicholas.duke.edu/statsreview/means/welch/](https://sites.nicholas.duke.edu/statsreview/means/welch/)检索。
