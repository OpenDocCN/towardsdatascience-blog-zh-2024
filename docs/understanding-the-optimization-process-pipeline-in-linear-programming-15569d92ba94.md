# 理解线性规划中的优化过程管道

> 原文：[https://towardsdatascience.com/understanding-the-optimization-process-pipeline-in-linear-programming-15569d92ba94?source=collection_archive---------4-----------------------#2024-12-27](https://towardsdatascience.com/understanding-the-optimization-process-pipeline-in-linear-programming-15569d92ba94?source=collection_archive---------4-----------------------#2024-12-27)

## 本文描述了线性规划中的后端和前端过程，包括数学编程系统（mps）文件、问题矩阵、优化过程、结果提取和解决方案文件，使用的是一个名为HiGHS的开源求解器以及其Python包装器highspy。

[](https://medium.com/@himalaya.birshrestha?source=post_page---byline--15569d92ba94--------------------------------)[![Himalaya Bir Shrestha](../Images/9766140c1c44381029d0a78154217775.png)](https://medium.com/@himalaya.birshrestha?source=post_page---byline--15569d92ba94--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--15569d92ba94--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--15569d92ba94--------------------------------) [Himalaya Bir Shrestha](https://medium.com/@himalaya.birshrestha?source=post_page---byline--15569d92ba94--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--15569d92ba94--------------------------------) ·7分钟阅读·2024年12月27日

--

在2021年[文章](https://medium.com/towards-data-science/linear-programming-with-python-and-julia-be9e045a5d17)中，我展示了如何使用Python中的Pyomo包和Julia中的JuMP包解决线性优化问题。我还介绍了不同类型的商业和非商业求解器，用于解决线性、混合整数或非线性优化问题。

在这篇文章中，我将介绍用于表示优化问题的数学编程系统（mps）文件、求解器的优化过程以及解决方案文件格式。为此，我将使用与上一篇文章相同的问题，但加入了额外的边界条件。为了实现这一目标，我将使用一个开源求解器[HiGHS](https://highs.dev/)。HiGHS被誉为在开源求解器中最强大的线性优化问题求解工具之一。在Python中，我只需通过`pip install highspy`安装`highpy` [包](https://ergo-code.github.io/HiGHS/dev/interfaces/python/)即可访问该求解器。

不再赘述，让我们开始吧。

![](../Images/bc13eb6eeae2ae8d5ca037f2172c3e3f.png)

图片来自[Unseen Studio](https://unsplash.com/@uns__nstudio?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 问题陈述

问题陈述如下。`x`和`y`是两个决策变量。目标是最大化利润，且需满足三个约束条件。x和y各自都有下界和上界。

```py
Profit = 90x + 75y
Objective: maximize Profit subject to:
3x+2y≤66
9x+4y≤180
2x+10y≤200

Bounds:
2≤x≤8
10≤y≤40
```

## 使用highspy进行优化

在下面的代码中，我将模型初始化为`h`。然后，我引入了决策变量`x`和`y`及其下界和上界，并为它们赋予了名字。接下来，我添加了三个约束不等式，分别命名为c0、c1和c2。每个约束都有x和y的系数以及一个RHS值。然后，我最大化了目标函数90x+75y的值。模型在这一行运行。

```py
import highspy
import numpy as np

#initiate the model
h = highspy.Highs()

#define decision variables
x = h.addVariable(lb = 2, ub = 8, name = “x”)
y = h.addVariable(lb = 10, ub = 40, name = “y”)

#h.setOptionValue("solver", "ipm")

#define constraints
h.addConstr(3*x + 2*y<=66) #c0
h.addConstr(9*x + 4*y<=180) #c1
h.addConstr(2*x + 10*y<=200) #c2

#objective
h.maximize(90*x + 75*y)
```

## 优化过程中后台发生了什么？

当模型运行时，可以在终端窗口看到以下进展。那么，究竟发生了什么？我将在下文描述：

![](../Images/cf6bb0a465fd15ca4313ce515293e6f2.png)

**问题规模：**

线性问题中的约束可以以矩阵形式表示为Ax≤b，其中，A是约束系数矩阵，x是包含决策变量的向量，b是RHS值矩阵。对于给定的问题，约束可以如下所示表示为矩阵形式：

![](../Images/3016e6b7873348fafadeaf26fdbec02c.png)

以矩阵形式表示约束。插图由作者提供。

问题矩阵的大小由行数、列数和非零元素的数量来描述。行数表示约束的数量（此处为3），列数表示决策变量的数量（此处为2），元素/非零元素表示系数，这些系数不为零。在所有三个约束中，都没有系数为零的情况。因此，总的非零元素数量是六个。

这是一个非常简单问题的例子。实际上，可能会有一些问题，其行数、列数和非零元素的数量可以达到成千上万或百万级别。问题规模的增加会导致模型的复杂度增加，从而延长求解所需的时间。

**系数范围**

问题中x和y的系数范围从2到10。因此，矩阵的系数范围显示为[2e+00, 1e+01]。

成本在此处指的是目标函数。x的系数是90，y的系数是75。因此，成本的系数范围是[8e+01, 9e+01]。

x和y的下界和上界范围为2到40。因此，Bound的系数范围是[2e+00, 4e+01]。

右边约束（RHS）的系数范围在66到200之间。因此，RHS的系数范围是[7e+01, 2e+02]。

**预处理** [Presolve](https://lpsolve.sourceforge.net/5.5/Presolve.htm#:~:text=Presolve%20is%20a%20preprocess%20of,model%20and%20likely%20solves%20faster)是求解器尝试解决优化问题时的初始过程，它首先会尝试简化模型。例如，它可能将超出某个值的系数视为无穷大。[预处理](https://support.gurobi.com/hc/en-us/articles/360024738352-How-does-presolve-work)的目的是创建一个更小版本的问题矩阵，具有相同的目标函数，并且可映射到原始问题的可行空间。简化后的问题矩阵会比原始矩阵更简单、更容易解决，并且解决速度更快。

在这种情况下，预处理步骤仅用了两次迭代就完成，结果是一个空矩阵。这也意味着已经获得了解决方案，且无需进一步优化。返回的目标值为2100，HiGHS求解器的运行时间仅为0.01秒。在从优化中获得解决方案后，求解器可以使用后处理/取消预处理步骤，将解决方案映射到原始问题的可行空间。

## 数学规划系统（MPS）格式

数学规划系统（MPS）是一种用于表示线性和混合整数线性规划问题的[文件格式](https://en.wikipedia.org/wiki/MPS_(format))。它是一种相对较老的格式，但所有商业线性程序求解器都接受这种格式。线性问题也可以用其他格式表示，如LP、AMPL和GAMS。

可以使用`highspy`通过简单地使用`h.writeModel("foo.mps")`来写入MPS文件。而读取MPS文件也很简单，只需使用`h.readModel("foo.mps")`。

![](../Images/5035e99707bba76399dca7eda3b78193.png)

给定线性规划问题的MPS格式。作者插图。

给定优化问题的MPS文件结构如上所示。它以线性规划问题的名称（NAME）开始。OBJSENSE表示问题是最小化（MIN）还是最大化（MAX），这里是后者。ROWS部分表示目标、所有约束的名称以及它们的等式/不等式类型。E代表等式，G代表大于或等于约束，L代表小于或等于约束，N代表无约束行。这里，三个约束被给出为__c0、__c1和__c2，而Obj是目标的缩写。

在COLUMNS部分，决策变量的名称（这里是x和y）列在左侧，属于目标函数或约束不等式的系数列在右侧。RHS部分包含模型约束的右侧向量。决策变量的上下界在BOUNDS部分定义。MPS文件以ENDATA结尾。

## 优化过程与结果获取

HiGHS 使用如 [单纯形法](https://ergo-code.github.io/HiGHS/dev/options/definitions/#option-solver) 或内点法等[算法](https://ergo-code.github.io/HiGHS/dev/options/definitions/#option-solver)进行优化过程。解释这些算法值得单独写一篇文章，我希望将来能涉及到它们。

提取结果的代码如下所示。模型状态为最优。我提取了目标函数值和决策变量的解值。此外，我还打印了迭代次数、原始解和对偶解的状态，以及基准有效性。

```py
solution = h.getSolution()
basis = h.getBasis()
info = h.getInfo()

model_status = h.getModelStatus()
print("Model status = ", h.modelStatusToString(model_status))
print()

#Get solution objective value, and optimal values for x and y
print("Optimal objective = ", info.objective_function_value)
print ("Optimal value of x:", solution.col_value[0])
print ("Optimal value of y:", solution.col_value[1])

#get model run characteristics
print('Iteration count = ', info.simplex_iteration_count)
print('Primal solution status = ', h.solutionStatusToString(info.primal_solution_status))
print('Dual solution status = ', h.solutionStatusToString(info.dual_solution_status))
print('Basis validity = ', h.basisValidityToString(info.basis_validity))
```

![](../Images/7f69ec7b81599082ddbdb184c9ac2de9.png)

打印上面代码的结果。插图由作者提供。

## 解文件

在优化过程之后，HiGHS 允许将解决方案写入一个 `.sol` 扩展名的解文件中。此外，解决方案可以按照[这里](https://ergo-code.github.io/HiGHS/dev/options/definitions/#write_solution_style)给出的不同格式写入。1 表示 HiGHS 精美格式，3 表示 Glpsol 精美格式。

![](../Images/04c4e9bb7f3902eafe29504488db543e.png)

HiGHS 可用的解决方案文件样式。插图来源于[HiGHS 文档](https://ergo-code.github.io/HiGHS/dev/options/definitions/#write_solution_style)。

为了以风格 3 获取解决方案，我使用了 `h.writeSolution("mysolution.sol", 3)`。问题统计信息在顶部提供。最优解值在活动列中提供。St 列指定了解的状态。例如，B 表示基本——变量或约束是基础解的一部分（最优）。NU 表示该解是非基础的，并且与上界相同。边际列中的值（通常称为影子价格或对偶值）[指的是](https://or.stackexchange.com/questions/3070/glpk-meaning-of-the-marginal-column-in-the-solution-output)当非基础变量单位变化时，目标函数的变化量。有关 GLPK 解文件信息的更多信息，可以参考[这里](https://en.wikibooks.org/wiki/GLPK/Solution_information)。

![](../Images/8f70b5d2ff64dc95a4efeca4af1fc8da.png)

Glpsol 精美风格下解决方案文件的结构。插图由作者提供。

## 结论

在这篇文章中，我展示了如何使用名为 HiGHS 的开源求解器和 Python 中的 `highspy` 包解决一个简单的线性优化问题。接下来，我解释了如何通过系数矩阵、决策变量向量和右侧向量来推断优化问题的大小。我介绍并解释了用于表示优化问题的数学规划系统（mps）文件的不同组成部分。最后，我演示了求解器的优化过程，提取结果的步骤以及分析解文件的过程。

本文的笔记本和相关文件可以在这个 GitHub [仓库](https://github.com/hbshrestha/Optimization)中找到。感谢您的阅读！
