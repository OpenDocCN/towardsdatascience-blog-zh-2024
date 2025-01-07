# KernelSHAP在预测变量相关时可能产生误导

> 原文：[https://towardsdatascience.com/kernelshap-can-be-misleading-with-correlated-predictors-9f64108f7cfb?source=collection_archive---------7-----------------------#2024-08-09](https://towardsdatascience.com/kernelshap-can-be-misleading-with-correlated-predictors-9f64108f7cfb?source=collection_archive---------7-----------------------#2024-08-09)

## 一个具体的案例研究

[](https://medium.com/@vanillaxiangshuyang?source=post_page---byline--9f64108f7cfb--------------------------------)[![Shuyang Xiang](../Images/36a5fd18fd9b7b88cb41094f09b83882.png)](https://medium.com/@vanillaxiangshuyang?source=post_page---byline--9f64108f7cfb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9f64108f7cfb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9f64108f7cfb--------------------------------) [Shuyang Xiang](https://medium.com/@vanillaxiangshuyang?source=post_page---byline--9f64108f7cfb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f64108f7cfb--------------------------------) ·阅读时长：7分钟·2024年8月9日

--

> “像许多其他基于置换的解释方法一样，Shapley值方法在特征相关时会遭遇不现实数据实例的引入。为了模拟一个特征值在一个联合体中缺失的情况，我们对该特征进行边际化处理……当特征之间存在依赖关系时，我们可能会抽取一些对于当前实例来说不合理的特征值。”—— [可解释机器学习书籍](https://christophm.github.io/interpretable-ml-book/shapley.html#disadvantages-13)。

SHAP（Shapley加性解释）值旨在根据合作博弈论中的Shapley值概念，公平地分配每个特征对机器学习模型预测的贡献。Shapley值框架具有几个理想的理论属性，并且原则上可以处理任何预测模型。然而，SHAP值可能会产生误导，特别是在使用KernelSHAP方法进行近似时。当预测变量之间存在相关性时，这些近似值可能会不准确，甚至可能有相反的符号。

在这篇博客文章中，我将展示原始的SHAP值如何与[SHAP框架](https://shap.readthedocs.io/en/latest/example_notebooks/overviews/An%20introduction%20to%20explainable%20AI%20with%20Shapley%20values.html)的近似值有显著差异，尤其是KernelSHAP，并讨论这些差异背后的原因。

# **案例研究：客户流失率**

考虑一个场景，我们旨在预测一个办公楼租赁的流失率，基于两个关键因素：入住率和报告问题的比例。

占用率对流失率有显著影响。例如，如果占用率过低，租户可能会因为办公室未被充分利用而离开。相反，如果占用率过高，租户可能会因为拥挤而离开，寻求更好的选择。

此外，我们假设报告问题的比率与占用率高度相关，具体而言，报告问题的比率是占用率的平方。

我们将流失率函数定义如下：

![](../Images/456107a845a24d3ffa3fb61d9ede4aa3.png)

作者提供的图像：流失率函数

该函数对于这两个变量的表示可以通过以下插图表示：

![](../Images/fac81573dd6ddaff630cd4e105c27f5c.png)

作者提供的图像：关于两个变量的流失

# 原始SHAP与Kernel SHAP之间的差异

## 使用Kernel SHAP计算的SHAP值

我们将使用以下代码计算预测变量的SHAP值：

```py
# Define the dataframe 
churn_df=pd.DataFrame(
    {
        "occupancy_rate":occupancy_rates,
        "reported_problem_rate": reported_problem_rates,
        "churn_rate":churn_rates,
    }
)
X=churn_df.drop(["churn_rate"],axis=1)
y=churn_df["churn_rate"]

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state = 42)
# append one speical point
X_test=pd.concat(objs=[X_test, pd.DataFrame({"occupancy_rate":[0.8], "reported_problem_rate":[0.64]})])

# Define the prediction
def predict_fn(data):
    occupancy_rates = data[:, 0]
    reported_problem_rates = data[:, 1]

    churn_rate= C_base +C_churn*(C_occ* occupancy_rates-reported_problem_rates-0.6)**2 +C_problem*reported_problem_rates
    return churn_rate

# Create the SHAP KernelExplainer using the correct prediction function
background_data = shap.sample(X_train,100)
explainer = shap.KernelExplainer(predict_fn, background_data)
shap_values = explainer(X_test)
```

上面的代码执行以下任务：

1.  数据准备：创建一个名为`churn_df`的DataFrame，包含`occupancy_rate`、`reported_problem_rate`和`churn_rate`列。然后从中创建变量和目标（`churn_rate`），并将数据拆分为训练集和测试集，训练集占80%，测试集占20%。注意，测试集`X_test`中添加了一个具有特定`occupancy_rate`和`reported_problem_rate`值的数据点。

1.  预测函数定义：定义了一个函数`predict_fn`，使用涉及预定义常量的特定公式计算流失率。

1.  SHAP 分析：使用预测函数和来自`X_train`的`background_data`样本初始化一个SHAP `KernelExplainer`。然后使用`explainer`计算`X_test`的SHAP值。

下面，您可以看到一个总结性的SHAP条形图，表示`X_test`的平均SHAP值：

![](../Images/4726e4abacf15f58d78cd540eb486327.png)

作者提供的图像：平均 SHAP 值

特别地，我们看到在数据点（0.8，0.64）处，两个特征的SHAP值分别为0.10和-0.03，如下图所示的力图所示：

![](../Images/f3be60805793ee4b29f17cadb80ce818.png)

作者提供的图像：单一数据点的力图

## 原始定义的SHAP值

让我们退后一步，根据SHAP的原始定义逐步计算确切的SHAP值。SHAP值的一般公式如下所示：

![](../Images/506fd5f8560513877e5e88618a511382.png)

其中：S是所有特征索引的子集，排除i，|S|是子集S的大小，M是特征的总数，f(XS∪{xi})是包含xi的S的特征所评估的函数，而f(XS)是S中缺少xi时评估的函数。

现在，让我们计算两个特征的SHAP值：占用率（表示为x1）和报告问题率（表示为x2），它们在数据点（0.8，0.64）处的值。回想一下，x1和x2之间的关系是x1 = x2²。

我们得到了数据点处占用率的SHAP值：

![](../Images/745d8cfe9265145b22870e14c1902dd9.png)

同样地，对于报告问题率这一特征：

![](../Images/3ea90e76aec7a3ba8c1e59d143dc01cf.png)

首先，让我们计算数据点处占用率的SHAP值：

1.  第一项是当X1固定为0.8且X2在其分布上取平均时模型输出的期望值。由于x1 = x2²的关系，这一期望值导致模型在特定点(0.8, 0.64)处的输出。

1.  第二项是模型输出的无条件期望值，其中X1和X2都在其分布上取平均。可以通过对背景数据集中的所有数据点的输出进行平均来计算这一期望值。

1.  第三项是模型在特定点(0.8, 0.64)处的输出。

1.  最后一项是当X1在其分布上取平均时，给定X2固定在特定点0.64时模型输出的期望值。同样，考虑到x1 = x2²的关系，这一期望值与模型在(0.8, 0.64)处的输出相符，类似于第一步。

因此，从原始定义计算的两个特征——占用率和报告问题率在数据点(0.8, 0.64)处的SHAP值分别为-0.0375和-0.0375，这与Kernel SHAP给出的值有很大不同。

差异从何而来？

# SHAP值差异的原因

如你所注意到的，两种方法之间的差异主要出现在第二步和第四步，这两步需要计算条件期望。这涉及到在X1被固定为0.8时，计算模型输出的期望值。

+   **精确SHAP**：在计算精确的SHAP值时，特征之间的依赖关系（例如我们示例中的x1=x_2²）被显式考虑。这通过考虑特征交互如何影响模型输出，确保了准确的计算。

+   **Kernel SHAP**：默认情况下，Kernel SHAP假设特征之间是独立的，这在特征实际上是相关的情况下会导致不准确的SHAP值。根据论文[*A Unified Approach to Interpreting Model Predictions*](https://arxiv.org/abs/1705.07874)，这一假设是一个简化。在实际应用中，特征通常是相关的，这使得在使用Kernel SHAP时很难获得准确的近似值。

![](../Images/536458182b111c3c9659970702905c74.png)

来自论文的截图

# 潜在的解决方案

不幸的是，基于原始定义直接计算SHAP值可能会非常耗费计算资源。以下是一些可供考虑的替代方法：

## TreeSHAP

+   专门为树模型（如随机森林和梯度提升机）设计的TreeSHAP高效地计算SHAP值，同时有效管理特征依赖关系。

+   该方法针对树集成进行了优化，使其比传统的SHAP计算更快且更具可扩展性。

+   在 SHAP 框架中使用 TreeSHAP 时，设置参数 `feature_perturbation = "interventional"` 以准确考虑特征之间的依赖性。

## 扩展 Kernel SHAP 以处理依赖特征

+   为了解决特征之间的依赖性，本文扩展了 Kernel SHAP。一个方法是假设特征向量服从多元高斯分布。在这种方法中：

+   条件分布被建模为多元高斯分布。

+   样本是通过使用来自训练数据的估计，从这些条件高斯分布中生成的。

+   该近似中的积分是基于这些样本计算的。

+   该方法假设特征服从多元高斯分布，但在实际场景中，特征可能展现出不同的依赖结构，因此该假设并不总是适用。

## 提升 Kernel SHAP 的准确性

+   **描述**：通过确保用于近似的背景数据集能够代表实际数据分布且特征独立，从而提高 Kernel SHAP 的准确性。

通过使用这些方法，您可以解决计算 SHAP 值时遇到的挑战，并提高它们在实际应用中的准确性。然而，需要注意的是，没有一种解决方案可以在所有场景中都适用。

# 结论

在这篇博文中，我们探讨了尽管 SHAP 值具有强大的理论基础并且在各种预测模型中具有广泛的适用性，但当预测变量之间存在相关性时，特别是在采用类似 KernelSHAP 这样的近似方法时，SHAP 值可能会出现准确性问题。理解这些局限性对于有效地解释 SHAP 值至关重要。通过识别潜在的差异并选择最合适的近似方法，我们可以在模型中实现更准确可靠的特征归因。
