# 救命！我们被HECS绑住了

> 原文：[https://towardsdatascience.com/simulating-student-loan-maturity-in-australia-27366297c814?source=collection_archive---------11-----------------------#2024-05-01](https://towardsdatascience.com/simulating-student-loan-maturity-in-australia-27366297c814?source=collection_archive---------11-----------------------#2024-05-01)

## *《大学协议》建议的统计模拟*

[](https://mmgillin.medium.com/?source=post_page---byline--27366297c814--------------------------------)[![Murray Gillin](../Images/40619967eb8911fa1d651503143b940c.png)](https://mmgillin.medium.com/?source=post_page---byline--27366297c814--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--27366297c814--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--27366297c814--------------------------------) [Murray Gillin](https://mmgillin.medium.com/?source=post_page---byline--27366297c814--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--27366297c814--------------------------------) ·阅读时间15分钟·2024年5月1日

--

## 引言

在澳大利亚，学生可以通过政府贷款——高等教育贷款计划（HELP）来支付高等教育费用。为了确保贷款的价值不被贬值，这些贷款每年会根据消费者价格指数进行调整。候选人只有在税后收入超过大约$51k时才开始偿还贷款，并且有一个分级偿还率，当收入超过大约$151k时，最高偿还比例为10%。最近的贷款调整率创下7.1%的历史新高，这是自1990年代以来最高的，这使得许多人更加关注学生债务是如何逐步增加的。

![](../Images/002d159b8885704b5f56cf10eb5e886c.png)

摄影：由[Edwin Andrade](https://unsplash.com/@theunsteady5?utm_source=medium&utm_medium=referral)拍摄，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我就是其中之一，离开学校时完成了荣誉学士学位，后来又在COVID期间通过硕士课程重新培训。在我的20多岁时，我的收入不足以达到强制性缴费的门槛，所以债务逐渐累积。获得硕士学位（约4万）后，债务水平进一步提高。虽然现在我正在采取措施清除债务并恢复那些非生产性扣除的部分，但可以想象，这对于许多毕业生来说是一个负担。

[《大学协议审查》](https://www.education.gov.au/australian-universities-accord/resources/final-report)提出了关于债务偿还的以下建议性变更（建议16）。

> 为了减少学生学习的长期财务成本，澳大利亚政府应使学生贡献更加公平，并更好地反映学生通过学习获得的终生收益，同时通过引入更公平、更简便的指数调整和还款安排，减少HELP贷款的负担。
> 
> 这应包括：
> 
> A. 减少学生的贡献，以应对“就业准备毕业生”（JRG）计划的最重大影响，首先从人文学科、其他社会与文化、传播与人类运动学专业的学生开始，逐步过渡到基于预测的潜在终生收入的学生贡献系统。
> 
> B. 通过实施基于边际税率的HELP还款系统，减少低收入者的还款负担，并限制增加额外工作小时的反激励。
> 
> C. 通过改变HELP贷款的指数调整时间，减少还款时间，使得用于强制还款的金额在应用指数调整前能得到计算。
> 
> D. 确保HELP贷款的增长不会超过工资增长，通过将HELP的指数调整率设定为消费者价格指数（CPI）和工资价格指数（WPI）中的较低者。
> 
> E. 审查银行贷款实践，确保银行认识到HELP贷款不同于其他类型的贷款，且不会以不当方式限制人们的住房贷款借款能力。

让我们详细分析这些建议，特别是与学生贷款的指数调整和还款相关的部分，并将其框架化为统计问题。

B点表明当前的还款率可能会对低收入者产生反激励作用。尽管这是一种同情的观点，但忽视了长期拖欠债务的影响。债务每年都会复利增长，与个人的还款能力无关。我们将分析还款率对清偿年限的影响，并审查其他国家如何对学生贷款进行指数调整或加收利息。

C点是一个有趣的案例，乍看之下似乎公平，就像任何其他债务一样，扣款是从税后收入中进行的，应该在后续的指数调整前计入本金。我们可以进行相关模拟，并计算债务清偿年限在学生中的分布变化。

当前和提议的指数调整及还款影响

D点是一个有趣的尝试，旨在通过将HELP贷款的指数调整率设定为CPI或WPI中较低的一个，来限制债务增长。我们可以进行一个简单的统计练习，测量过去20年中，CPI低于WPI的次数。

为了回答这些问题，我们将尝试解答以下问题：

+   学生是否会受益于在CPI和WPI之间转换，以最小化指数调整率？

+   在三种情景下模拟学生贷款的轨迹：当前状态、上面提到的提议未来状态，以及对所有收入水平超过当前最低门槛的情况应用10%的偿还率。这个练习将帮助我们评估不同情景下清偿年限分布是否存在差异。

## 学生们是否会从应用CPI和WPI中的最小值中受益？

这是旨在减少指数化对学生影响的关键建议之一。它在逻辑上是有缺陷的，因为薪资增长和水平与债务水平是独立的。然而，让我们暂时接受这一点，并评估是否存在合理的差异，这种差异将使债务增长放缓，相比单纯使用CPI。相关数据已经来源并审查了[WPI](https://www.abs.gov.au/statistics/economy/price-indexes-and-inflation/wage-price-index-australia/latest-release)和[CPI](https://www.abs.gov.au/statistics/economy/price-indexes-and-inflation/consumer-price-index-australia/latest-release)，感谢澳大利亚统计局，数据均在CC BY 4.0许可下发布。

```py
wpi_df <- readr::read_csv("All sector WPI, quarterly and annual movement (%), seasonally adjusted (a).csv", 
    col_types = cols(`Quarterly (%)` = col_skip()), 
    skip = 1) %>% 
    rename(month_year = ...1,
           value = `Annual (%)`) %>% 
    drop_na() %>% 
    mutate(month_year = lubridate::my(month_year),
           rate = 'wpi')

cpi_df <- read_csv("All groups CPI, Australia, quarterly and annual movement (%).csv", 
    col_types = cols(`Change from previous quarter (%)` = col_skip()), 
    skip = 1) %>% 
    rename(month_year = ...1,
           value = `Annual change (%)`) %>% 
    drop_na() %>% 
    mutate(month_year = lubridate::my(month_year),
           rate = 'cpi')

rates_df <- bind_rows(cpi_df, wpi_df) %>% 
    filter(month_year >= '2014-03-01' & month_year <= '2023-12-01') %>% 
  mutate(rate = as_factor(rate))

rates_df %>% 
  ggplot(aes(month_year, value, color = rate)) +
    geom_point() +
    geom_line() +
    theme_ggdist() +
    scale_color_viridis_d(begin = 0.3, end = 0.7) +
    labs(x = 'Date', y = 'Index Value', color = 'Rate', title = 'Comparison of Annual CPI and WPI')
```

![](../Images/5e1da6e7c66a3f4c58efcd3a0ac3e68e.png)

按季度比较年度CPI和WPI（图片来源：作者）

我们可以看到，CPI的方差大于WPI，但随着时间推移，关于均值，两个基础分布之间是否存在实际差异？

我们为10年的历史数据拟合了一个贝叶斯模型，从后验分布的期望值中抽取样本，然后使用贝叶斯方法执行均值差异评估。

```py
indices <- 
  brm(
    bf(value ~ rate + 0,
       sigma ~ rate + 0),
    data = rates_df,
    prior = c(prior(normal(2, 2), class = 'b')),
    family = gaussian, 
    iter = 2000, chains = 4, seed = 246, cores = 4, sample_prior = 'yes'
  )

new_df <- tibble(rate = c('wpi', 'cpi'))

new_df %>% 
  add_epred_draws(indices) %>% 
  compare_levels(.epred, rate, comparison = list(c('cpi', 'wpi'))) %>% 
  ggplot(aes(.epred, fill = after_stat(x > 0))) +
    stat_halfeye() +
    geom_vline(xintercept = 0, lty = 2) +
    theme_ggdist() +
    scale_fill_manual(values = c("gray80", "skyblue")) +
    labs(y = 'Density', x = 'Difference in Posterior Means', title = 'Difference in Posterior Means of WPI and CPI',
         subtitle = "80% of Density is Greater then 0/nApplying ROPE of 10%, Difference is Neglible", fill = 'Value Greater Then 0')
```

![](../Images/f15bf250c308238cf796ba2f78fa54b1.png)

CPI和WPI的后验均值分布差异（图片来源：作者）

基于WPI和CPI的后验分布，CPI的均值比WPI的均值大0.3个基点。80%的后验分布差异大于0，并且在10%的ROPE范围内，我们可以认为这个差异是微不足道的。在WPI和CPI之间的切换在最好情况下会最小化通货膨胀的影响，但从长远来看，不会实质性地帮助学生克服这些债务。

## 模拟学生的指数化和偿还情景

让我们继续评估建议中的其他元素，主要是指数化时机和强制偿还率。

**关键模拟假设概述**

要模拟学生的结果，我们需要对关键变量有合理的先验知识。

为了模拟毕业生薪资，我们采用了相当宽泛的视角，使用68,000美元的毕业生中位数薪资，并应用了下面展示的对数正态分布。这样我们可以捕捉到大多数毕业生薪资集中在这一点附近，同时也考虑到某些行业可能提供的更高起薪。类似地，对于债务，我们使用了当前的平均学生债务，并应用了一个相对偏右的分布，以捕捉债务的广泛范围。

对于指数化率，我们简单地取了过去10年每季度的年度CPI平均值，并假设它遵循正态分布，如上所示。类似地，我们对薪水增长采取了保守且积极的看法，假设年均增长3%，并遵循对数正态分布，使得毕业生因晋升或新工作机会能够更快地提升。

![](../Images/c3ba75a80bdbe1cd8f06dade64a5a423.png)

模拟变量分布（图片来自作者）

模拟变量假设（图片来自作者）

```py
index <- 1:50000 #simulate 50000 students

year <- 0:19 #over 20 years

calculate_salary <- function(previous, basevalue, multiplier) {
  coalesce(basevalue, multiplier * previous)
}

set.seed(246)

base_df <- 
  crossing(index, year) %>% 
  group_by(index) %>% #salary and growth varies by individual
  mutate(salary_0 = rlnorm(1, mean = log(68000), sdlog = log(1.34)),
         debt_0 =  rlnorm(1, meanlog = 10.2, sdlog = 0.5)) %>% 
  group_by(year) %>% #indexation rate applies uniformly across all indices each year
  mutate(indexation_rate = rnorm(1, mean = 0.027, sd = 0.012),
         indexation_rate = round(indexation_rate, 3)) %>% 
  group_by(index, year) %>% 
  mutate(salary_growth = rlnorm(1, meanlog = -3.5, sdlog = 0.6) + 1,
         salary_growth = round(salary_growth, 3)) %>% 
  group_by(index) %>% 
  mutate(salary_0 = if_else(year > 0, NA, salary_0),
         salary_1 = accumulate2(salary_0, salary_growth[-1], calculate_salary),
         salary_1 = case_when(salary_1 < 18200 ~ salary_1 * (1-0), #calculate post-tax incomee
           salary_1 >= 18201 & salary_1 <= 45000 ~  salary_1 - 0 - (salary_1-18200)*(0.19),
           salary_1 >= 45001 & salary_1 <= 120000 ~ salary_1 - 5092 - (salary_1-45000)*(0.325),
           salary_1 >= 120001 & salary_1 <= 180000 ~ salary_1 - 29467 - (salary_1-120000)*(0.37),
           salary_1 >= 180001 ~ salary_1 - 51667 - (salary_1-180000)*(0.45)
         ))
```

上面的代码片段设置了模拟50,000名学生在20年内的情况。我们使用自定义函数`calculate_salary`和`purrr::accumulate`来迭代计算给定样本增长分布下的薪资增长，然后计算他们的税后收入。还需要注意的一点是，每个候选人从自己的增长分布中抽取数据，但指数化在所有学生中按相同的年增长率应用。

```py
calculate_remaining_debt <- function(principal, payment, interest_rate) {
interest = principal * interest_rate
remaining_debt = principal + interest - payment
remaining_debt = ifelse(remaining_debt < 0, 0, remaining_debt)
remaining_debt
}

set.seed(246)

df <- base_df %>% mutate(
         repayment_rate = case_when(
           salary_1 < 51550 ~ 0.0, # Repayment Rates Post-Tax Income
           salary_1 >= 51550 & salary_1 <= 59518 ~ 0.01,
           salary_1 >= 59519 & salary_1 <= 63089 ~ 0.02,
           salary_1 >= 63090 & salary_1 <= 66875 ~ 0.025,
           salary_1 >= 66876 & salary_1 <= 70888 ~ 0.03,
           salary_1 >= 70889 & salary_1 <= 75140 ~ 0.035,
           salary_1 >= 75141 & salary_1 <= 79649 ~ 0.04,
           salary_1 >= 79650 & salary_1 <= 84429 ~ 0.045,
           salary_1 >= 84430 & salary_1 <= 89494 ~ 0.05,
           salary_1 >= 89495 & salary_1 <= 94865 ~ 0.055,
           salary_1 >= 94866 & salary_1 <= 100557 ~ 0.06,
           salary_1 >= 100558 & salary_1 <= 106590 ~ 0.065,
           salary_1 >= 106591 & salary_1 <= 112985 ~ 0.07,
           salary_1 >= 112986 & salary_1 <= 119764 ~ 0.075,
           salary_1 >= 119765 & salary_1 <= 126950 ~ 0.08,
           salary_1 >= 126951 & salary_1 <= 134568 ~ 0.085,
           salary_1 >= 134569 & salary_1 <= 142642 ~ 0.09,
           salary_1 >= 142643 & salary_1 <= 151200 ~ 0.095,
           salary_1 > 151201 ~ 0.1),
         repayment = salary_1 * repayment_rate,
         debt_1 = accumulate(2:n(), .init = first(debt_0),
~ calculate_remaining_debt(.x, repayment[.y], indexation_rate[.y])
),
         repayment = if_else(debt_1 == 0, 0, repayment),
         debt_paid = if_else(debt_1 == 0, 'y', 'n'))

df <- 
df %>% 
  group_by(index) %>% 
  mutate(clearance_year = if_else(lag(debt_1, default = first(debt_1)) > 0 & debt_1 == 0, 1, 0),
         clearance_cum = cumsum(clearance_year)) %>% 
  filter(clearance_cum == 0 | clearance_year == 1) %>% 
  select(1:11) %>%
  mutate(is_paid = if_else(debt_paid == 'y', 1, 0),
         debt = round(debt_1, 2),
         salary = round(salary_1, 2),
         scenario = 'INDEXATION PRE PAYMENT; PROGRESSIVE PAYMENT RATE',
         scenario_l = 'A',
         group = if_else(max(is_paid) == 1, 'paid', 'unpaid'))

# Repeat Again with Different Calculation of Debt, Indexation Post Repayment

calculate_remaining_debt <- function(principal, payment, interest_rate) {
remaining_debt = principal - payment + (principal-payment) * interest_rate
remaining_debt = ifelse(remaining_debt < 0, 0, remaining_debt)
remaining_debt 
}

df2 <- 
df2 %>% 
  group_by(index) %>% 
  mutate(clearance_year = if_else(lag(debt_1, default = first(debt_1)) > 0 & debt_1 == 0, 1, 0),
         clearance_cum = cumsum(clearance_year)) %>% 
  filter(clearance_cum == 0 | clearance_year == 1) %>% 
  select(1:11) %>%
  mutate(is_paid = if_else(debt_paid == 'y', 1, 0),
         debt = round(debt_1, 2),
         salary = round(salary_1, 2),
         scenario = 'INDEXATION AFTER PAYMENT; PROGRESSIVE PAYMENT RATE',
         scenario_l = 'B',
         group = if_else(max(is_paid) == 1, 'paid', 'unpaid'))

# Last Scenario Applies a Flat Rate of 10% Repayment Above Minimum Threshold

df3 <- base_df %>% 
  mutate(repayment_rate = case_when(
           salary_1 < 51550 ~ 0.0,
           salary_1 >= 51550 ~ 0.1), 
         repayment = salary_1 * repayment_rate,
         debt_1 = accumulate(2:n(), .init = first(debt_0),
~ calculate_remaining_debt(.x, repayment[.y], indexation_rate[.y])
),
         repayment = if_else(debt_1 == 0, 0, repayment),
         debt_paid = if_else(debt_1 == 0, 'y', 'n'))

df3 <- 
df3 %>% 
  group_by(index) %>% 
  mutate(clearance_year = if_else(lag(debt_1, default = first(debt_1)) > 0 & debt_1 == 0, 1, 0),
         clearance_cum = cumsum(clearance_year)) %>% 
  filter(clearance_cum == 0 | clearance_year == 1) %>% 
  select(1:11) %>%
  mutate(is_paid = if_else(debt_paid == 'y', 1, 0),
         debt = round(debt_1, 2),
         salary = round(salary_1, 2),
         scenario = 'INDEXATION PRE PAYMENT; FLAT PAYMENT RATE',
         scenario_l = 'C',
         group = if_else(max(is_paid) == 1, 'paid', 'unpaid'))
```

![](../Images/f040ea314ad83bf7a68f56d15461d36d.png)

示例数据框（图片来自作者）

我们将这三种情景合并为一个单一视图，供后续分析使用。下面我们可视化了数据集中前9名学生的情况。

```py
df4 <- bind_rows(df, df2, df3)

df4 %>% 
  filter(index <= 9) %>% 
  ggplot(aes(year, debt, color = scenario)) +
    geom_point() +
    geom_line() +
    facet_wrap(~index) +
    theme_ggdist() +
    scale_color_brewer(palette = "Dark2") +
    theme(legend.position = 'bottom', legend.direction = 'vertical') +
    labs(x = 'Year', y = 'Debt', title = 'Scenarios of First Nine Students')
```

![](../Images/f33d6dac55f00d5d70ef3b43e6deb025.png)

让我们来解析一下，我们已经模拟了上述三种情景。我们模拟了10%的还款率，这是新西兰对学生债务的做法，从大约新西兰元25,000开始。有些人可能会认为这相当苛刻，然而，新西兰不会收取利息，也不会应用指数化，除非毕业生离开该国工作。我认为这是一个合理的情景，值得进行分析。上面的可视化展示了数据集中前9名学生在每种情景下的债务轨迹——我们总共模拟了50,000名学生。

索引4是薪水和债务指数化如何条件独立的一个很好的例子。在当前状态下的前14年，债务没有得到任何偿还。其次，直到第15年，毕业生的税后薪水才超过了大约51,000的强制付款阈值，但在此期间债务增加了近22,000，并且在20年的时间范围内依然无法清偿债务。

低的递增率意味着该学生可能永远无法偿还这笔债务，因为指数化将继续超过当前的还款速度。哪种结果更好：一个毕业生能够还清学生贷款，还是在职业生涯的余生中背负着这个负担，从他们的薪水中扣除低于指数化影响的增量？

我认为这突出了回顾中一个关键的遗漏点，我们在解决什么问题？在我看来，HELP系统必须使毕业生尽快偿还债务。

## 债务清偿年数建模

现在我们已经有了包含所有学生模拟数据的数据集，可以评估各个情景下债务清偿所需的年份分布。

```py
df4 %>% 
  filter(group == 'paid') %>% 
  group_by(index, scenario) %>%
  summarise(debt_clearance = max(year)) %>% 
  ggplot(aes(debt_clearance, fill = scenario)) +
    geom_histogram(binwidth = 1) +
    facet_grid(~scenario) +
    theme_ggdist() +
    scale_fill_brewer(palette = "Dark2", aesthetics = c('color', 'fill')) +
    theme(legend.position = 'none') +
    labs(x = 'Years to Debt Clearance', y = 'Count', title = 'Distribution of Years to Debt Clearance by Scenario')
```

![](../Images/0c8592b0118d8d9343c931bdfdc1063a.png)

按场景划分的债务清偿年限分布（图源：作者）

目前越来越清楚的是，改变指数化点之间几乎没有差异，然而设置一个比指数化率高得多的偿还率，可以使学生更早偿还债务。让我们完成一个贝叶斯方差分析（ANOVA），以了解后验均值年限的差异。鉴于我们处理的是计数数据，以下我们拟合了三种模型，改变了似然性和参数数量。

```py
set.seed(246)

# Take Sample of Total Dataframe
count_df_sample <- df4 %>%
  filter(group == 'paid') %>% 
  group_by(index, scenario, scenario_l) %>%
  summarise(debt_clearance = max(year))  %>% 
  group_by(scenario_l) %>% 
  slice_sample(n = 4000)

# Poisson Likelihood
m1a <- brm(
  debt_clearance ~ scenario_l + 0,
  data = count_df_sample,
  family = poisson,
  prior = c(prior(gamma(9, 1), class = 'b', lb = 0)),
  chains = 4, iter = 2000, cores = 4, threads = threading(2)
) %>% 
  add_criterion(c('loo', 'waic'),  moment_match = T)

# Negative Binomial Likelihood w/ Pooling
m1b <- brm(
  debt_clearance ~ scenario_l + 0,
  data = count_df_sample,
  family = negbinomial,
  prior = c(prior(gamma(9, 1), class = 'b', lb = 0)),
  chains = 4, iter = 2000, cores = 4, threads = threading(2)
) %>% 
  add_criterion(c('loo', 'waic'),  moment_match = T)

# Establish Prior for Non-Pooling Negative Binomial 

prior <- get_prior(
  bf(debt_clearance ~ scenario_l + 0,
  shape  ~ scenario_l + 0),
  data = count_df_sample,
  family = negbinomial,
  prior = c(prior(gamma(9, 1), class = 'b', lb = 0))) %>% 
  as_tibble() %>% 
  mutate(prior = if_else(class == 'b' & dpar == 'shape' & coef == '', 'gamma(9, 1)',  prior),
         lb = if_else(class == 'b' & dpar == 'shape' & coef == '', '0',  lb),
         prior = if_else(class == 'b' & dpar == '' & coef == '', 'gamma(6, 1)', prior),
         lb = if_else(class == 'b' & dpar == '' & coef == '', '0', lb)) %>% 
  as.brmsprior()

# Negative Binomial Likelihood w/o Pooling

m1c <- brm(
  bf(debt_clearance ~ scenario_l + 0,
  shape  ~ scenario_l + 0),
  data = count_df_sample,
  family = negbinomial,
  prior = prior,
  chains = 4, iter = 2000, cores = 4, threads = threading(2)
) %>% 
  add_criterion(c('loo', 'waic'),  moment_match = T)

loo_compare(m1a, m1b, m1c) %>% print(simplify = F)
```

![](../Images/86a2428512f9e06a51186858c23be7c2.png)

LOO 比较结果（图源：作者）

从我们创建的三个模型来看——没有合并的负二项模型显示出更好的外部样本预测能力。为了进行我们的方差分析（ANOVA），我们将使用这个模型。作为参考，情境 A 是预先指数化的渐进式，B 是事后指数化的渐进式，C 是预先指数化的固定税率。

```py
new_df <- tibble(scenario_l = c('A', 'B', 'C'))

m1c %>% 
  tidybayes::epred_draws(new_df, ndraws = 4000, seed = 111) %>% 
  compare_levels(.epred, scenario_l) %>% 
  ggplot(aes(.epred, scenario_l, fill = scenario_l)) + 
    stat_dist_halfeye() +
    geom_vline(xintercept = 0, linetype = 'dashed') +
    theme_ggdist() +
    scale_fill_brewer(palette = "Dark2") +
    labs(x = 'Difference in Posterior Means', y = 'Scenario', title = 'Differences in Posterior Mean Between Each Scenario', fill = 'Scenario')
```

![](../Images/6f895fcc97a31857163cc0b6eaca8db3.png)

上述结果告诉我们两件事——首先，改变指数化的时间对 HELP 债务的到期时间几乎没有影响。其次，设置 10% 的固定偿还率将在平均上减少偿还债务的时间约 4 年。

它还表明，债务的时间越短，复利对债务价值的影响就越小，这意味着毕业生将能够重新获得本应支付的薪水部分，从而实现有效的税后收入增长。

## 结语

这项分析已经以完全透明和可重复的方式定量评估了大学协议审查中提出的建议。

我们的分析旨在提供一种替代现状的最佳情况。我们的模拟结果是乐观的，但并非不切实际，考虑了债务和薪资在 20 年间的增长。

首先，我们评估了长期来看，是否在 CPI 和 WPI 之间交换有意义。根据我们的建模，长期来看，在两种指数之间进行切换对计算指数化的影响几乎可以忽略不计，CPI 更具优势，平均差距为 0.3 个基点。

其次，我们根据上述三种情景，模拟了50,000名毕业生在20年内的债务和工资轨迹，调整了指数化点并增加了偿还率。然后我们使用这些数据评估了清偿债务的平均年数差异，并注意到**改变指数化点不会产生实质性效果**，而**将偿还率增加到统一的10%是一个可行的选项，可以让毕业生更快地偿还债务**。我们可以通过我们的DAG来表达这些陈述，通过增加偿还，我们观察到债务水平下降得更快。此外，指数化的影响与工资无关，因此，任何基于收入水平减轻其影响的尝试都忽略了以下关系。

![](../Images/0e2a5516d1a49eef31455b26bc75c034.png)

HELP债务维护的因果图（作者提供的图片）

这变成了一个前期更多牺牲的案例，但随着时间的推移，避免了指数化对本金债务水平的复利效应。这一情形在一定程度上复制了新西兰的做法，采用一个平坦的、适用于更低最低门槛的广泛税率。

大学协议审查的作者有责任讨论其建议的影响。报告在没有验证或适当模拟的情况下做出了隐含的因果陈述。你不能在一句话中寻求缓解高等教育的财务压力，然后又不提供清晰的成本解决方案，帮助学生通过强制性和自愿性付款更及时地清偿债务。

联邦政府值得肯定地表示，HELP债务正在审查中，但根据报告中的建议，并根据我们所做的模拟，我怀疑这些措施是否能对改善毕业生的财务状况产生实质性效果。
