# 在 SQL 中从随机分布中抽样

> 原文：[https://towardsdatascience.com/drawing-from-a-random-distribution-in-sql-b6c97c89e45c?source=collection_archive---------10-----------------------#2024-02-09](https://towardsdatascience.com/drawing-from-a-random-distribution-in-sql-b6c97c89e45c?source=collection_archive---------10-----------------------#2024-02-09)

## 从概率密度函数到随机样本

[](https://medium.com/@samiabboud?source=post_page---byline--b6c97c89e45c--------------------------------)[![Sami Abboud](../Images/546a33fe92a194068816e6173eaa854a.png)](https://medium.com/@samiabboud?source=post_page---byline--b6c97c89e45c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b6c97c89e45c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b6c97c89e45c--------------------------------) [Sami Abboud](https://medium.com/@samiabboud?source=post_page---byline--b6c97c89e45c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b6c97c89e45c--------------------------------) ·8分钟阅读·2024年2月9日

--

![](../Images/22d2395939286048baeef0d7e72e1687.png)

图片来自 [Moritz Kindler](https://unsplash.com/@moritz_photography?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) ，发布于 [Unsplash](https://unsplash.com/photos/white-and-black-polka-dot-ceramic-mug-with-water-okiy0SxOaBg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在每次迭代中，更新强化学习代理的策略有不同的方法。几周前，我们开始尝试用贝叶斯推理步骤替换当前的方法。我们代理中的一些数据工作负载是用 SQL 编写的，并在 GCP 的 BigQuery 引擎上执行。我们使用这个技术栈，因为它提供了可扩展的计算能力、机器学习包和一个直观的 SQL 接口。

我们计划实现的贝叶斯推理步骤使用的是 [贝塔分布的替代参数化方式](https://en.wikipedia.org/wiki/Beta_distribution#Mean_and_sample_size)。这意味着我们需要能够在 SQL 中从贝塔分布中抽样。在研究这个问题时，我发现 SQL 中从随机分布中抽样是一个几乎没有好文档示例的话题。所以我在这里写一下。

# 第一步：这有多难？

BigQuery 没有贝塔分布。它也没有从*任何*随机分布中抽样的能力。所以我最初的想法是，拿贝塔分布的定义，用 SQL 写出来，使用 CTA 设置参数，从 0 到 1 之间抽一个随机数，并计算该函数的值。

但这是 2024 年，所以我问了 ChatGPT 它会怎么做：

**我**：“如何在 BigQuery 中从贝塔分布中抽取随机样本？”

**ChatGPT**：

```py
WITH beta_draws AS (
 SELECT
 (alpha — 1) / (alpha + beta — 2) AS a,
 (beta — 1) / (alpha + beta — 2) AS b
 FROM
 (SELECT 2 AS alpha, 5 AS beta) — Set your desired alpha and beta values
)
SELECT
 (a * POW(RAND(), alpha - 1) * POW(1 - RAND(), beta - 1) + b) AS beta_draw
FROM
 beta_draws,
 UNNEST(GENERATE_ARRAY(1, 1000)) AS rand_num
```

**我** *自言自语*：对了，这显然行不通。

你在代码中看到了问题吗？ChatGPT 为假定的 Beta 分布 PDF 绘制了两个 *不同* 的 x 值。我修正了这个问题，稍微清理了一下查询，并抽样了 1,000 个值。下面是执行此操作的 SQL 代码：

```py
WITH raw_data AS (
 SELECT
 ‘user_a’ AS contact_id
 , 0.674 AS probability
 , 72 AS lifetime_messages_received
), parameters AS (
 SELECT
 contact_id
 , probability*lifetime_messages_received AS alpha
 , (1.0 — probability)*lifetime_messages_received AS beta
 , RAND() AS x
 FROM raw_data
 CROSS JOIN UNNEST(GENERATE_ARRAY(1, 1000)) AS draw_id
)
SELECT
 contact_id
 , ARRAY_AGG(POW(x, alpha — 1.0) * POW(1.0 — x, beta — 1)) AS beta_x
FROM parameters
GROUP BY contact_id
```

感谢大家，今天就到这里 🎁 下篇文章见！

错误！🔴

让我们用相同的参数，取一个经过验证的 Beta 分布抽样实现，并对比结果。我使用了 Python 中 SciPy 的 `beta.rvs()`，这里有两个 100-bin 的直方图，可以用来比较这两个抽样分布。

```py
from scipy.stats import beta

alpha_param = 0.674 * 72
beta_param = (1–0.674) * 72

scipy_beta_draw = beta.rvs(alpha_param, beta_param, size=1000)
```

![](../Images/8fd72857adf2b4861d92580c4316de1f.png)

（**左侧**）：使用 BigQuery 的简单抽样。（**右侧**）：使用 SciPy 的 beta.rvs() 抽样

好吧，仔细看就能发现分布是不同的。我回过头查看了 Beta 分布的定义，意识到可能是因为 Beta 分布也有一个依赖于 [伽马函数](https://en.wikipedia.org/wiki/Gamma_function) 的缩放常数，而我在计算中没有包括它 🤦。

**问题**：伽马函数没有 [封闭式表达式](https://en.wikipedia.org/wiki/Closed-form_expression)，而 BigQuery 也没有提供近似的实现。因此，在这一点上，我决定转向 Python，这是我更熟悉的语言，并且可以提高我的实验效率。我想到，如果在 Python 中搞定了，我就能把它转到 SQL 中去。虽然我仍然需要某种方法来近似伽马函数，但一步一步来。

# 步骤 2：从随机分布抽样到底意味着什么？

让我们在 Python 中实现从 Beta 分布手动抽样，但这次使用 SciPy 的伽马函数来正确计算常数：

```py
import numpy as np
from scipy.special import gamma
from scipy.stats import uniform

alpha_param = 0.674 * 72
beta_param = (1–0.674) * 72

constant = gamma(alpha_param + beta_param) / (gamma(alpha_param) * gamma(beta_param))
scipy_manual_beta_draw = np.array([
 constant*pow(x, alpha_param-1)*pow(1-x, beta_param-1)
 for x in uniform.rvs(size=1000)
])
```

让我们再次用一个 100-bin 的直方图来检查分布：

![](../Images/de29f9bc9661da1f062515f5e5ea0db7.png)

使用 Python 的简单抽样

我们首先注意到的是，规模现在不同了，但分布仍然看起来像是在 BigQuery 中绘制的那样。

*... 有些地方不对...* 是时候去散步思考一下了 🚶

…

*短暂散步后：*

从随机分布抽样到底意味着什么？到目前为止我实现的是从 Beta 概率密度函数（PDF）中随机抽样，但它并没有成功。

所以我不得不翻阅一些统计学课程。

这里有一些很好的复习资料：

+   [连续随机变量的概率密度函数（PDF）和累积分布函数（CDF）](https://stats.libretexts.org/Courses/Saint_Mary's_College_Notre_Dame/MATH_345__-_Probability_(Kuter)/4%3A_Continuous_Random_Variables/4.1%3A_Probability_Density_Functions_(PDFs)_and_Cumulative_Distribution_Functions_(CDFs)_for_Continuous_Random_Variables)，以及

+   我发现非常有用的 [从概率分布生成样本](https://web.mit.edu/urban_or_book/www/book/chapter7/7.1.3.html)。

简而言之，结论是从随机变量中抽样实际上意味着**从逆累积分布函数（CDF）中抽样**，而不是像我之前做的那样从概率密度函数（PDF）中抽样。

当然了 🤦。我的[概率论教授](https://web.iem.technion.ac.il/dmitry-dima-ioffe-1963-2020/)，我刚得知他在 2020 年因病去世了，他本该鼓励我在这个时候“复习基础知识”。

好的。让我们回顾一下 Python 代码，现在它是从我们 beta 分布的逆 CDF（也称为[分位数函数](https://en.wikipedia.org/wiki/Quantile_function)）中抽样，并与使用 SciPy 的 beta.rvs() 抽样的分布进行比较：

```py
import numpy as np
from scipy.special import gamma
from scipy.stats import uniform, beta

alpha_param = 0.674 * 72
beta_param = (1–0.674) * 72
n_draws = 1000

# Use SciPy RVS for comparison
scipy_beta_draw = beta.rvs(alpha_param, beta_param, size=n_draws)

# Manual beta draw with the help of the SciPy Gamma function

# We start with a discrete analogue of the Beta PDF we wish to draw from.
# This is just sampling from the PDF at fixed intervals but do check out
# this review for a more in-depth treatment of the subject:
# https://jsdajournal.springeropen.com/articles/10.1186/s40488-015-0028-6

# Set the resolution for generating the discrete PDF
n_samples = 1000

# The beta distribution is supported on the range [0, 1], so we set the
# pdf min and max parameters accordingly
pdf_min = 0.0
pdf_max = 1.0

x_span = np.linspace(pdf_min, pdf_max, n_samples)
constant = gamma(alpha_param + beta_param) / (gamma(alpha_param) * gamma(beta_param))
beta_pdf = np.array([
 constant * pow(x, alpha_param — 1) * pow(1 — x, beta_param — 1)
 for x in x_span
])

# Using the discrete Beta PDF, we now compute a discrete Beta CDF.
# To do that, we integrate the PDF. For each point x, we sum the PDF until
# that point and multiple with the width of each sample.
freq = 1.0 / n_samples
beta_cdf = beta_pdf.cumsum() * freq

def inv(cdf, q):
 “””Return inverse CDF for value q using the quantile function”””
 return x_span[np.argmin(cdf < q)]

# Finally, we can now draw n_draws from the discrete inverse of CDF, aka
# generate random samples from a beta distribution
manual_beta_draw = np.array([
 inv(beta_cdf, x)
 for x in uniform.rvs(size=n_draws)
])
```

*呼* 这个看起来好多了：

![](../Images/eb08d3d0bc8cb8c045eba5cf4cbc73fe.png)

两个直方图的叠加，比较使用 SciPy 的 beta.rvs() 和手动抽样的 1,000 次抽样结果

# 第 3 步：回到 SQL

现在我们已经正确地从随机变量中抽样，是时候回到 SQL 了。为了简便起见，并且因为 BigQuery 并未直接提供 Gamma 函数的实现¹，我将从[逻辑斯蒂分布](https://en.wikipedia.org/wiki/Logistic_distribution)中抽样（参数 a=0，b=1）。

```py
 — The following 3 parameters need to be adjusted based on the support of the
 — PDF of the distribution you wish to draw from. This values are set for a logistic
 — distribution with a=0 and b=1

DECLARE pdf_min INT64 DEFAULT -10;
DECLARE pdf_max INT64 DEFAULT 10;
DECLARE n_samples INT64 DEFAULT 5000;
DECLARE sampling_step FLOAT64 DEFAULT (pdf_max — pdf_min) / n_samples;

— The number of random draws you wish to perform
DECLARE n_draws INT64 DEFAULT 1000;

WITH pdf AS (

 — The discrete sampling of the logistic distribution PDF

 SELECT
 x
 , exp(-x) / pow(1 + exp(-x), 2) AS y — a=0, b=1
 FROM UNNEST(GENERATE_ARRAY(pdf_min, pdf_max, sampling_step)) AS x
), cdf AS (

 — The discrete CDF

 SELECT
 x
 , SUM(y)
 OVER (
 ORDER BY x
 ) * (1.0 / n_samples) AS y
 FROM pdf
), random_draws AS (

 — Random draws in the range of [0, max(cdf)]

 SELECT
 RAND() * (SELECT MAX(y) FROM cdf) as q
 , draw_id
 FROM UNNEST(GENERATE_ARRAY(1, n_draws)) AS draw_id
)

 — Calculate the inverse CDF per draw using the quantile function by generating
 — and array of the discrete support of the distribution and returning the value
 — of the index just before the randomly generated number is larger than the CDF

SELECT
 ARRAY_AGG(x ORDER BY x)[OFFSET(SUM(CAST(y < q AS INT64)))] AS x
FROM random_draws
JOIN cdf
ON TRUE
GROUP BY draw_id;
```

现在让我们比较这三种抽样方法的分布：

1.  SciPy 的 `logistic.rvs()`

1.  在 Python 中手动从逻辑斯蒂分布的 PDF 中抽样，并按照上面第 2 步的方式进行随机抽样

1.  在 SQL 中执行相同操作

![](../Images/48e0f6a2ad13082a144664db8a95f8dc.png)

三个直方图的叠加，比较使用 SciPy 的 beta.rvs()、Python 中的手动抽样和 SQL 中的手动抽样的 1,000 次抽样结果

看起来这是一次成功！💪

上面的 SQL 代码从逻辑斯蒂分布中抽样，但它应该适用于任何分布，只要你能通过在一致的间隔内抽样来获得概率密度函数（PDF）的离散表示！

[1] 我确实曾尝试寻找 SQL 中的 Gamma 函数近似实现，最后放弃了。很可能在 SQL 中编写 Gamma 函数的近似是可能的，但这需要更多的研究，而我分配的时间并不足够。请小心盲目复制粘贴一个近似实现（例如，将 Rosetta Code 中的某段代码翻译成 SQL），因为这些实现会假设 Gamma 参数，而这些假设并不总是显而易见。例如，Python 代码（由 Ada 转译而来）对于小 Gamma 值才是准确的。

—

**这就是我们为不断改进 Aampe 所做的工作，Aampe 是一个强化学习代理，能够为用户个性化电子邮件、网页/推送通知、短信和 WhatsApp 消息。**

*除非另有说明，所有图片均由作者提供。*
