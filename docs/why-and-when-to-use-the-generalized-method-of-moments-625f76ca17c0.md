# 为什么以及何时使用广义矩法

> 原文：[https://towardsdatascience.com/why-and-when-to-use-the-generalized-method-of-moments-625f76ca17c0?source=collection_archive---------0-----------------------#2024-05-04](https://towardsdatascience.com/why-and-when-to-use-the-generalized-method-of-moments-625f76ca17c0?source=collection_archive---------0-----------------------#2024-05-04)

## 这是一种高度灵活的估计技术，可以应用于多种情况

[](https://l-f-rodrigues.medium.com/?source=post_page---byline--625f76ca17c0--------------------------------)[![Luis Felipe de Souza Rodrigues](../Images/44ce5f46a90847ac1383962cd0a9c4c5.png)](https://l-f-rodrigues.medium.com/?source=post_page---byline--625f76ca17c0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--625f76ca17c0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--625f76ca17c0--------------------------------) [Luis Felipe de Souza Rodrigues](https://l-f-rodrigues.medium.com/?source=post_page---byline--625f76ca17c0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--625f76ca17c0--------------------------------) ·阅读时间 7分钟·2024年5月4日

--

![](../Images/f6dd88aee8fd66668538b4cc23be02dd.png)

图片来源：[Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Hansen（1982年）率先引入了广义矩法（GMM），为金融领域的实证研究，特别是资产定价领域做出了显著贡献。该模型的创建动机是为了在遵循模型中隐含的理论约束的同时估计经济模型中的参数。例如，如果经济模型表明两者应该是独立的，GMM将尝试找到一个解，使它们乘积的平均值为零。因此，理解GMM对于那些需要一个理论条件至关重要的模型，但由于数据的性质，传统模型无法满足这些条件的人来说，是一个强有力的替代方案。

# 概念

这种估计技术在计量经济学和统计学中广泛应用，用于解决回归分析中的内生性和其他问题。GMM估计量的基本概念是通过选择使数据样本矩与总体矩尽可能接近的参数来最小化准则函数。基本的GMM估计量的方程可以表达为：

![](../Images/40d2e529d8d4bce971693f089cefc307.png)

GMM估计量的目标是找到能够最小化该准则函数的参数向量θ，从而确保数据的样本矩尽可能地与总体矩对齐。通过优化该准则函数，GMM估计量提供了一致的经济计量模型参数估计。

一致性意味着随着样本量趋近于无穷大，估计量会以概率收敛到真实的参数值（渐近正态）。这个性质对于确保估计量随着数据量增加能够提供可靠的估计至关重要。即使在存在遗漏变量的情况下，只要矩条件有效且工具变量正确设定，GMM仍能提供一致的估计量。然而，遗漏相关变量可能会影响估计量的效率和参数的解释。

为了提高效率，GMM在Z矩量上使用广义最小二乘法（GLS），以提高经济计量模型中参数估计的精度和效率。GLS通过根据观察值的方差加权来处理异方差性和自相关性。在GMM中，Z矩量被投影到工具变量的列空间中，这类似于GLS方法。通过聚焦于Z矩量并应用GLS技术，这可以最小化方差并提高参数估计的精度。

# **假设**

然而，重要的是要认识到GMM估计量受到一系列假设的约束，这些假设在应用过程中必须加以考虑，具体假设如下：

+   矩的存在性：需要满足某一阶数的要求，并且数据的分布应具有有限的尾部。

+   正确的模型设定：底层模型必须正确设定，包括函数关系和误差项的分布。

+   可识别性：必须存在一个唯一的解用于估计参数。

+   矩条件：必须正确指定矩条件，并且在模型假设下其均值应为零。

+   有效工具变量：如果适用，工具变量必须相关且有效。

+   独立性与同方差性（条件性）：理想情况下，误差应在矩条件下独立且同方差。

+   对异方差性的稳健性：如果加权矩阵是一致估计的，GMM对异方差性具有稳健性。

+   多重共线性：GMM能够处理多重共线性，但它可能会影响估计量的效率。

+   离群值：GMM对离群值较为敏感，除非在建模过程中妥善处理。

+   大样本：在大样本中，GMM更为高效。

+   渐近理论：一致性和效率等性质是渐近性质。

# **何时使用**

因此，GMM是一种高度灵活的估计技术，可以应用于各种情况，广泛用作计量经济学和统计学中的参数估计技术。它允许在不同的模型规范和数据结构下高效估计参数。其主要用途包括：

+   带有工具变量的模型：当模型中存在内生变量时使用。它提供了一种在解释变量与误差相关时，修正参数估计偏差的方法。

+   带有测量误差的模型：GMM可以用来修正由变量测量误差引入的偏差。

+   带有矩量限制的模型：在某些情况下，模型必须满足多个矩条件。GMM允许你同时利用所有这些信息，以实现更高效的估计。

+   时间序列模型：GMM常用于ARMA（自回归滑动平均）模型和其他时间序列模型中。

+   面板数据模型：它可以应用于面板数据模型，处理横截面单位内的异方差性和自相关问题。

+   非线性模型：GMM也可以扩展到非线性模型，为经典方法如最大似然估计不可行时提供一种稳健的估计技术。

# **与OLS的比较**

普通最小二乘法（OLS）方法与广义矩估计法（GMM）的对比突出了它们各自的优点。OLS在经典的线性假设下证明了其高效性，作为最小方差（BLUE）的无偏线性估计量。线性回归模型的基本假设包括：变量之间的线性关系、无完全多重共线性、误差的均值为零、同方差性（误差的方差恒定）、误差无自相关性和误差正态分布。因此，OLS是一种无偏、一致和高效的估计量。此外，它的计算复杂度相对较低。

然而，GMM提供了更多的灵活性，适用于各种情境，如带有测量误差、内生变量、异方差性和自相关的模型。它对误差的分布不做假设，并且适用于非线性模型。GMM在我们遗漏了重要变量、具有多个矩条件、非线性模型以及具有异方差性和自相关的数据集的情况下表现突出。

# **与MLE的比较**

相反，在比较GMM和最大似然估计（MLE）时，它突出了两者处理数据假设的方式。GMM通过使用数据和总体矩条件构造估计量，提供了灵活性和适应性，适用于假设较少的模型，特别是在强假设关于数据分布可能不成立的情况下更为有利。

最大似然估计（MLE）通过最大化给定数据的似然函数来估计参数，这取决于关于数据分布的特定假设。当假设的分布与真实的数据生成过程紧密匹配时，MLE表现最优；而GMM能够适应多种分布，在数据可能不符合单一特定分布的情况下表现出其价值。

# **Python中的估计**

在Python中演示的假设示例中，我们利用linearmodels.iv库来估计一个使用IVGMM函数的GMM模型。在这个模型中，消费作为因变量，年龄和性别（男性用虚拟变量表示）作为外生变量。此外，我们假设收入是内生变量，而子女数量和教育水平是工具变量。

```py
import pandas as pd
from linearmodels.iv import IVGMM

# Read the Excel file
df = pd.read_excel('example.xlsx')

# Dependent variable
dependent = 'YConsumption'

# Exogenous variables
exog_vars = ['XAge', 'XMale1']

# Endogenous variable
endog_vars = ['XIncomeEndo']

# Instrumental variables
instruments = ['ZChildQuantity6', 'ZEducation']

# Construct the formula for GMM
formula = "{dep} ~ 1 + {exog} + [{endog} ~ {instr}]".format(
    dep=dependent, 
    exog='+'.join(exog_vars), 
    endog=endog_vars[0], 
    instr='+'.join(instruments)
)

# Estimate the GMM model
model = IVGMM.from_formula(formula, df)
result = model.fit(cov_type='robust')

# Displaying GMM results
print(result)
```

GMM模型中的工具变量用于解决内生性问题，它们提供了一种与内生回归量相关但与误差项不相关的外生变动源。IVGMM函数专为在GMM框架内使用工具变量估计模型而设计。

![](../Images/1a9b5e5ee65e4a4e27d9606ddafa01da.png)

因此，通过将消费作为因变量，并采用外生变量（年龄和性别）以及工具变量（子女数量和教育水平）来解决内生性问题，本示例适用于广义矩量法（GMM）框架。

总结来说，广义矩量法（GMM）被视为一种强大且多用途的技术，广泛应用于计量经济学和统计学领域，在某些情况下具有相对于其他方法的优势。与传统的最小二乘法（OLS）和最大似然估计法（MLE）相比，这种方法允许更广泛的模型规格和数据结构，因为它对所需满足的假设限制较少。

在内生性、测量误差、动量约束等问题中，GMM尤其具有优势。此外，在数据存在非线性、异方差性和自相关等问题时，该模型表现更好。GMM利用矩条件和工具变量提供一致且高效的参数估计，因此，对于那些理论条件至关重要且常规方法因假设违背或数据结构复杂而无法应用的实证研究者，GMM是一个极好的选择。

因此，GMM成为一种多功能的计量工具，适用于广泛的实证场景，帮助研究者有效处理复杂的建模任务，并提供模型参数的正确估计。

# 参考文献

Hansen, L. P. (1982). 大样本广义矩量法估计量的性质。*计量经济学*，*50*(4)，1029–1054\. [https://doi.org/10.2307/1912775](https://doi.org/10.2307/1912775)
