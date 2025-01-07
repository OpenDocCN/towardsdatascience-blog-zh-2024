# 向商业利益相关者解释复杂模型

> 原文：[https://towardsdatascience.com/explaining-complex-models-to-business-stakeholders-5af0691bacd4?source=collection_archive---------11-----------------------#2024-04-30](https://towardsdatascience.com/explaining-complex-models-to-business-stakeholders-5af0691bacd4?source=collection_archive---------11-----------------------#2024-04-30)

![](../Images/10b1475735b39b40c9cbea27c8cbf262.png)

图片来自[niko photos](https://unsplash.com/@niko_photos?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)于[Unsplash](https://unsplash.com/photos/green-leaf-tree-under-blue-sky-tGTVxeOr_Rs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 解释LightGBM模型

[](https://medium.com/@fkarvoun?source=post_page---byline--5af0691bacd4--------------------------------)[![Frida Karvouni](../Images/49aad19f6bdd7ffdc68c212722079c6f.png)](https://medium.com/@fkarvoun?source=post_page---byline--5af0691bacd4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5af0691bacd4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5af0691bacd4--------------------------------) [Frida Karvouni](https://medium.com/@fkarvoun?source=post_page---byline--5af0691bacd4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5af0691bacd4--------------------------------) ·阅读时间：5分钟·2024年4月30日

--

商业利益相关者开始认识到机器学习模型为其运营带来的价值，深入了解其优缺点。同时，对更准确和更快速的机器学习模型的需求也在上升。

随着这些模型的快速发展，挑战逐渐显现，尽管模型的准确性不断提高，但其变得更加复杂且难以解释（被称为“黑箱”模型）。因此，数据科学家越来越难以：

+   向利益相关者解释方法和结果，从而阻碍模型的采纳率，

+   评估特征变化如何影响模型性能，

+   深入了解模型超参数调整如何影响其结构，

+   确保模型的公平性，特别是符合像GDPR（该法规禁止以可能对客户造成伤害或误导的方式使用个人数据）等法规的要求，**[1]**

+   识别模型中的漏洞。

# 全球和局部可解释性

LightGBM是一种基于树的提升模型，能够提供精确的结果，但由于其固有的复杂性，理解起来存在挑战。

我们将构建一个LightGBM模型，并深入探讨其内部机制。首先，我们将对来自scikit-learn的糖尿病数据集进行预处理。

```py
from sklearn.datasets import load_diabetes
from pandas import DataFrame
import pandas as pd

diabetes = load_diabetes()
X_raw, y_raw = diabetes.data, diabetes.target
X = DataFrame(X_raw, columns=diabetes.feature_names)
y = pd.Series(y_raw)
y.name = "progression"

pdf = pd.concat([X,y], axis=1)

# Rename columns
pdf = pdf.rename(columns= {"bp": "blood_pressure",
                           "s1": "total_cholestorol", 
                           "s2": "LDL",
                           "s3": "HDL",
                           "s4": "total_cholestorol/HDL",
                           "s5": "triglycerides",
                           "s6": "blood_sugar"})
```

数据集已经进行了标准化，在这种情况下，目标变量将表示糖尿病的进展，作为回归值。特征包括各种患者特征以及血液水平的测量值。

![](../Images/f256e220a4513ffeec1a8dd1edc4fe49.png)

使用的糖尿病数据集由scikit-learn提供： [3]

假设是较高的血压会导致糖尿病进展加剧：

```py
#Plot blood_sugar vs progression with a regression line
import seaborn as sns
import matplotlib.pyplot as plt

sns.lmplot(x="blood_sugar", y="progression", data=pdf)
plt.show()
```

![](../Images/987f228828f8260c999cb4d87f046b2b.png)

血糖与糖尿病进展之间的关系

对于较高的BMI指数，也存在相同的假设。

```py
# same for BMI
sns.lmplot(x="bmi", y="progression", data=pdf)
```

![](../Images/2d0b0e963b4041976cd774aadd958c36.png)

BMI与糖尿病进展之间的关系

如我们所见，标准化后的特征已经很难解释，并向相关方说明。LightGBM模型拟合如下：

```py
from sklearn.model_selection import train_test_split
X = pdf.drop("progression", axis=1)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)

import lightgbm
import shap

def fit_lightgbm(x_train, y_train, x_test, y_test):
    params = {
        "task": "train",
        "boosting_type": "gbdt",
        "objective": "rmse",
        "metric": ["l2", "rmse"],
        "learning_rate": 0.005,
        "num_leaves": 128,
        "max_bin": 512,
    } # basic parameters as a starting point

    model = lightgbm.sklearn.LGBMRegressor(**params)
    fitted_model = model.fit(x_train, y_train)
    y_pred = pd.Series(fitted_model.predict(x_test))

    return y_train, y_test, y_pred, fitted_model

y_train, y_test, y_pred, fitted_model = fit_lightgbm(
        X_train, y_train, X_test, y_test
    )
```

在高层次上，数据科学家必须理解模型的内部工作原理，确定其是否捕捉了与业务洞察一致的最相关特征，并识别出模型中遗漏的关键特征。此时，解释拟合的模型是具有挑战性的。

## 全局可解释性

评估LightGBM模型的全局可解释性需要计算特征重要性或Shapley值的均值。

## 特征重要性

模型的特征重要性计算如下：

```py
# calculate feature importance of LightGBM fitted_model using plot_importance
lightgbm.plot_importance(fitted_model, importance_type="gain", 
                         figsize=(20, 10), grid=False, color="grey", 
                         precision=2)
```

![](../Images/800e0761a663a8a8db8f37b332aff06a.png)

LightGBM模型的特征重要性

BMI指数是通过显著改进模型划分来提升模型预测的特征。甘油三酯水平、血糖和血压也是模型中的关键特征。这些发现是合理的，因为一个人的BMI指数，以及血糖和甘油三酯水平，很可能是糖尿病的贡献因素。

## Shapley值

Shapley值的均值应当与上述情况非常相似：

```py
# plot shap values, include intercept in the shap values
shap_values = shap.TreeExplainer(fitted_model).shap_values(X_train)
shap.summary_plot(shap_values, X_train, plot_type="bar", color="grey")
```

![](../Images/cbf7ccee357d6a385f53b1888582d83e.png)

每个特征对LightGBM模型的边际贡献的均值

确实，使用这两种技术时，结果非常相似。

## 局部可解释性

这些工具对于评估模型的整体性能非常宝贵。通过Shapley值，我们还可以对每个特征在数据点级别上的边际贡献进行深入分析。

![](../Images/a6cce9dfb1a834c513c76d8b459dd060.png)

非线性模型中的边际贡献。来源：[2]

例如，我们可以分析每个特征对特定患者糖尿病进展的影响：

```py
# plot shap values for a specific data point / patient
shap.initjs()
shap.force_plot(
    shap.TreeExplainer(fitted_model).expected_value,
    shap_values[0],
    X_train.iloc[0],
    matplotlib=True,
)
```

![](../Images/89b363a7abfaef3a25e56651630401a2.png)

每个特征对单个患者的LightGBM模型预测的边际贡献

这种视角将使我们能够为患者提供量身定制的建议，建议专注于优化其BMI指数。

# **摘要**

总结来说，尽管机器学习模型提供了显著的优势，但它们日益复杂的结构带来了关于可解释性、可理解性和合规性方面的挑战，这影响了它们的普及和效果。诸如 SHAP 和特征重要性等技术使数据科学家能够更好地理解他们的模型，从而有助于将预测结果解释给业务方。

# 资源

*除非另有说明，所有图像均由作者生成*

**[1]**[https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/principles/lawfulness-fairness-and-transparency/#:~:text=In%20general%2C%20fairness%20means%20that,also%20about%20whether%20you%20should](https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/principles/lawfulness-fairness-and-transparency/#:~:text=In%20general%2C%20fairness%20means%20that,also%20about%20whether%20you%20should)。

**[2]**[https://shap.readthedocs.io/en/latest/example_notebooks/overviews/An%20introduction%20to%20explainable%20AI%20with%20Shapley%20values.html](https://shap.readthedocs.io/en/latest/example_notebooks/overviews/An%20introduction%20to%20explainable%20AI%20with%20Shapley%20values.html) © 版权 2018，Scott Lundberg。修订版 dffc346f

**[3]**[https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_diabetes.html](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_diabetes.html) © 2007–2024，scikit-learn 开发者（BSD 许可）

LightGBM 文档：[https://lightgbm.readthedocs.io/en/stable/](https://lightgbm.readthedocs.io/en/stable/)

SHAP 文档：[https://shap.readthedocs.io/en/latest/index.html](https://shap.readthedocs.io/en/latest/index.html)
