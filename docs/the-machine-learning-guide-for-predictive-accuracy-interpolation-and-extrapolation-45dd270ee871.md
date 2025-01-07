# 预测准确度的机器学习指南：插值与外推

> 原文：[https://towardsdatascience.com/the-machine-learning-guide-for-predictive-accuracy-interpolation-and-extrapolation-45dd270ee871?source=collection_archive---------4-----------------------#2024-07-04](https://towardsdatascience.com/the-machine-learning-guide-for-predictive-accuracy-interpolation-and-extrapolation-45dd270ee871?source=collection_archive---------4-----------------------#2024-07-04)

## 评估超越训练数据的机器学习模型

[](https://rkiuchir.medium.com/?source=post_page---byline--45dd270ee871--------------------------------)[![Ryota Kiuchi, Ph.D.](../Images/5459c434848898345d932320c4a01312.png)](https://rkiuchir.medium.com/?source=post_page---byline--45dd270ee871--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--45dd270ee871--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--45dd270ee871--------------------------------) [Ryota Kiuchi, Ph.D.](https://rkiuchir.medium.com/?source=post_page---byline--45dd270ee871--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--45dd270ee871--------------------------------) ·13 分钟阅读·2024年7月4日

--

# 介绍

近年来，数据驱动的方法，如机器学习（ML）和深度学习（DL），已经被应用于广泛的任务，包括机器翻译和个性化推荐。这些技术通过分析大量数据，从给定的训练数据集中揭示出一些模式。然而，如果给定的数据集存在偏差，并且不包含你希望了解或预测的数据，那么从训练后的模型中获得正确答案可能会很困难。

![](../Images/941658a16eabebab03560ebfa237dbf8.png)

图片由 [Stephen Dawson](https://unsplash.com/@dawson2406?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

让我们思考一下ChatGPT的案例。此时最新版本的ChatGPT是ChatGPT 4o，该模型是在2023年6月之前的数据上进行训练的（本文发布时的情况）。因此，如果你询问2024年发生的、训练数据中未包含的事情，你将无法获得准确的答案。这种情况被称为“幻觉”，OpenAI已经增加了预处理程序，在此类问题上返回固定的“无法回答”答案。另一方面，ChatGPT的训练数据基本上是基于英文文档的，因此它在非英语母语国家（如日本和法国）中的地方性领域知识上不太擅长。因此，许多公司和研究团队投入大量精力，通过使用RAG（检索增强生成）或微调，将地区或领域特定的知识融入到他们的LLM中。

因此，识别所使用的训练数据对于理解AI模型的适用性和局限性非常重要。另一方面，数据驱动方法面临的最大挑战之一是，这些技术通常需要在训练数据集的范围之外进行操作。这些需求通常出现在新产品开发、材料科学中的新药物化合物效果预测，以及在市场上推出产品时预测消费者行为等领域。这些场景要求在稀疏区域和训练数据之外做出正确的预测，这涉及到插值和外推。

![](../Images/4fc75309a8d53d6ae33f3b7315e6bf84.png)

图片来自 [Elevate](https://unsplash.com/@elevatebeer?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

插值是指在已知数据范围内进行预测。如果训练数据分布密集且均匀，则可以在该范围内获得准确的预测。然而，在实际应用中，准备这种数据并不常见。另一方面，外推是指在已知数据点范围之外进行预测。尽管在这些区域内的预测是高度期望的，但数据驱动的方法通常在此方面最为困难。因此，理解每个算法在插值和外推方面的表现非常重要。

![](../Images/60d5eb36eebb5fa24ed0c4f4c92269d2.png)

作者创建

本文考察了各种机器学习算法的插值和外推能力。我们准备了一个人工训练数据集，并通过可视化每个模型的预测结果来评估这些能力。机器学习算法的目标如下：

+   符号回归器

+   SVR（支持向量回归）

+   高斯过程回归器（GPR）

+   决策树回归器

+   随机森林回归器

+   XGBoost

+   LightGBM

此外，我们还评估了集成模型，如投票回归器和堆叠回归器。

## 代码

完整代码请见以下链接：

[](https://github.com/rkiuchir/blog_TDS/tree/main/02_compare_regression?source=post_page-----45dd270ee871--------------------------------) [## blog_TDS/02_compare_regression at main · rkiuchir/blog_TDS

### 数据科学之道博客内容。通过在GitHub上创建帐户，贡献于rkiuchir/blog_TDS的开发。

[github.com](https://github.com/rkiuchir/blog_TDS/tree/main/02_compare_regression?source=post_page-----45dd270ee871--------------------------------)

# 数据生成与预处理

首先，我们使用一个简单的非线性函数生成人工数据，该函数是从[gplearn中的符号回归教程](https://gplearn.readthedocs.io/en/stable/examples.html)稍作修改而来，修改部分为添加了指数项。该函数包含线性、二次和指数项，定义如下：

![](../Images/0172ad132b748c46722140f59278115d.png)

其中，*x₀*和*x₁*的范围为-1到1。实际值的平面如下所示：

由于我们在插值和外推方面评估每个机器学习模型的表现，因此每种情况都需要不同的数据集。

对于插值，我们在与训练数据集相同的范围内评估模型的表现。因此，每个模型都将在-1到1的范围内使用离散化的数据点进行训练，并在相同的范围内评估预测表面。

另一方面，对于外推，模型在训练数据集范围外的能力是必需的。我们将使用在-0.5到1的范围内离散化的数据点来训练模型，涵盖*x₀*和*x₁*，并在-1到1的范围内评估预测表面。因此，在-1到-0.5的范围内，*x₀*和*x₁*的实际值与预测表面之间的差异揭示了模型在外推方面的能力。

在本文中，还将通过检查20个点和100个点这两种情况，评估训练数据集点数的影响。

例如，100个数据点如下生成：

```py
import numpy as np

def target_function(x0, x1):
    return x0**2 - x1**2 + x1 + np.exp(x0) - 1

# Generate training data for interpolation
X_train = rng.uniform(-1, 1, 200).reshape(100, 2)
y_train = target_function(X_train[:, 0], X_train[:, 1])

# Generate training data for extrapolation
X_train = rng.uniform(-0.5, 1, 200).reshape(100, 2)
y_train = target_function(X_train[:, 0], X_train[:, 1])
```

# 机器学习算法简介

在本文中，我们评估了七种主要机器学习算法在插值和外推方面的表现。此外，还考虑了使用这七种算法的六种集成模型。每种算法都有不同的结构和特点，这些特点会对预测性能产生优缺点。我们在此总结了每种算法的特点如下：

1.  **符号回归**

+   训练后的模型通过基于遗传算法拟合的数学表达式来表示。

+   模型被定义为函数，具有很高的可解释性。

+   适合于目标变量可以表示为特征函数的任务。

+   擅长插值，但在外推方面可能有一定的潜力。

[](/find-hidden-laws-within-your-data-with-symbolic-regression-ebe55c1a4922?source=post_page-----45dd270ee871--------------------------------) [## 使用符号回归发现数据中的隐藏规律

### 自动发现基本公式，如开普勒定律和牛顿定律

towardsdatascience.com](/find-hidden-laws-within-your-data-with-symbolic-regression-ebe55c1a4922?source=post_page-----45dd270ee871--------------------------------)

**2\. 支持向量回归（SVR）**

+   基于支持向量机（SVM），可以通过核方法有效处理高维空间中的非线性关系

+   使用不同类型的核函数，如线性核、RBF核、多项式核和 sigmoid 核，模型可以表达复杂的数据模式

+   擅长插值，但在外推时稳定性较差

[](/the-complete-guide-to-support-vector-machine-svm-f1a820d8af0b?source=post_page-----45dd270ee871--------------------------------) [## 支持向量机（SVM）完全指南

### 理解其内部原理，并在四种不同场景下实现支持向量机（SVM）

towardsdatascience.com](/the-complete-guide-to-support-vector-machine-svm-f1a820d8af0b?source=post_page-----45dd270ee871--------------------------------)

**3\. 高斯过程回归（GPR）**

+   基于贝叶斯方法，预测结果以概率形式表示，包括预测值及其不确定性

+   由于不确定性估计，GPR 被用于贝叶斯优化

+   使用不同类型的核函数，如线性核、RBF核、多项式核和 sigmoid 核，模型可以表达复杂的数据模式

+   擅长插值，选择合适的核函数后，也具有一定的外推潜力

[](/quick-start-to-gaussian-process-regression-36d838810319?source=post_page-----45dd270ee871--------------------------------) [## 高斯过程回归快速入门

### 一份快速指南，帮助理解高斯过程回归（GPR）并使用 scikit-learn 的 GPR 包

towardsdatascience.com](/quick-start-to-gaussian-process-regression-36d838810319?source=post_page-----45dd270ee871--------------------------------)

**4\. 决策树**

+   简单的树状算法，通过不断分割数据

+   易于理解和解释，但容易过拟合

+   步骤状估计适用于插值，但不擅长外推

[](/decision-tree-in-machine-learning-e380942a4c96?source=post_page-----45dd270ee871--------------------------------) [## 机器学习中的决策树

### 决策树是一种类似流程图的结构，其中每个内部节点表示对特征的测试（例如是否…

towardsdatascience.com](/decision-tree-in-machine-learning-e380942a4c96?source=post_page-----45dd270ee871--------------------------------)

**5\. 随机森林**

+   一种基于集成的算法，称为“Bagging”，由多个决策树组成

+   通过结合多个不同的树，该算法能够降低过拟合的风险，并且具有较高的插值性能

+   比单个决策树具有更稳定的预测，但不擅长外推

[](/understanding-random-forest-58381e0602d2?source=post_page-----45dd270ee871--------------------------------) [## 理解随机森林

### 算法如何工作以及为什么如此有效

towardsdatascience.com](/understanding-random-forest-58381e0602d2?source=post_page-----45dd270ee871--------------------------------)

**6\. XGBoost**

+   一种基于集成的算法，叫做“提升法”，通过顺序减少错误将多个决策树结合起来

+   由于优秀的预测性能，通常用于Kaggle等竞赛

+   比单个决策树具有更稳定的预测，但不擅长外推

[](https://medium.com/sfu-cspmp/xgboost-a-deep-dive-into-boosting-f06c9c41349?source=post_page-----45dd270ee871--------------------------------) [## XGBoost：深入了解提升法

### 每天我们都听到关于人工智能突破的消息。然而，你有没有想过它面临的挑战是什么……

medium.com](https://medium.com/sfu-cspmp/xgboost-a-deep-dive-into-boosting-f06c9c41349?source=post_page-----45dd270ee871--------------------------------)

**7\. LightGBM**

+   类似于XGBoost，但具有更快的训练速度和更高的内存效率，更适用于较大的数据集

+   比单个决策树具有更稳定的预测，但不擅长外推

[](https://medium.com/@pushkarmandot/https-medium-com-pushkarmandot-what-is-lightgbm-how-to-implement-it-how-to-fine-tune-the-parameters-60347819b7fc?source=post_page-----45dd270ee871--------------------------------) [## 什么是LightGBM，如何实现它？如何调整参数？

### 你好，

medium.com](https://medium.com/@pushkarmandot/https-medium-com-pushkarmandot-what-is-lightgbm-how-to-implement-it-how-to-fine-tune-the-parameters-60347819b7fc?source=post_page-----45dd270ee871--------------------------------)

**8\. 投票回归器**

+   一种集成学习方法，通过结合多个模型的预测进行预测

+   混合不同模型的特性，能提供比单一模型更稳健的预测

+   本文中评估了三种组合：

    – 支持向量回归器 + 随机森林

    – 高斯过程回归器 + 随机森林

    – 随机森林 + XGBoost

[](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.VotingRegressor.html?source=post_page-----45dd270ee871--------------------------------) [## VotingRegressor

### 示例图库：绘制单个和投票回归预测

scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.VotingRegressor.html?source=post_page-----45dd270ee871--------------------------------)

**9\. 堆叠回归器**

+   一种集成学习方法，使用多个模型的预测作为最终预测模型的输入，称为“元模型”

+   元模型覆盖单个模型的弱点，并结合每个模型的优点

+   本文中评估了三种组合：

    – 基础模型：支持向量回归 + 随机森林；元模型：随机森林

    – 基础模型：高斯过程回归 + 随机森林；元模型：随机森林

    – 基础模型：随机森林 + XGBoost；元模型：随机森林

[](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.StackingRegressor.html?source=post_page-----45dd270ee871--------------------------------) [## StackingRegressor

### 画廊示例：使用堆叠法组合预测器

[scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.StackingRegressor.html?source=post_page-----45dd270ee871--------------------------------)

使用这些算法，我们将评估使用我们之前生成的数据集的插值和外推性能。在接下来的部分中，将解释每个模型的训练方法和评估方法。

# 模型训练与评估

## 预处理

基本上，除了基于树的方法，如随机森林、XGBoost 和 LightGBM，大多数机器学习算法都需要特征缩放。然而，由于我们在这个实践中只使用了两个特征，如*x₀*和*x₁*，它们的范围相同，分别为-1到1（插值）或-0.5到1（外推），因此我们将跳过特征缩放。

## 模型训练

为简化起见，除 LightGBM 外，所有算法都使用默认超参数，LightGBM 的默认参数适用于较大的数据集。

如前所述，我们将在模型训练期间使用不同的数据集来评估插值和外推。

## 评估与可视化

在模型训练后，我们将使用非常细致的数据进行预测。基于这些预测值，将使用[Plotly表面函数](https://plotly.com/python/3d-surface-plots/)绘制预测曲面。

这些过程由以下代码完成：

```py
class ModelFitterAndVisualizer:
    def __init__(self, X_train, y_train, y_truth, scaling=False, random_state=41):
        """
        Initialize the ModelFitterAndVisualizer class with training and testing data.

        Parameters:
            X_train (pd.DataFrame): Training data features
            y_train (pd.Series): Training data target
            y_truth (pd.Series): Ground truth for predictions
            scaling (bool): Flag to indicate if scaling should be applied
            random_state (int): Seed for random number generation
        """
        self.X_train = X_train
        self.y_train = y_train
        self.y_truth = y_truth

        self.initialize_models(random_state)

        self.scaling = scaling

    # Initialize models
    # -----------------------------------------------------------------
    def initialize_models(self, random_state):
        """
        Initialize the models to be used for fitting and prediction.

        Parameters:
            random_state (int): Seed for random number generation
        """

        # Define kernel for GPR
        kernel = 1.0 * RBF(length_scale=1.0) + WhiteKernel(noise_level=1.0)

        # Define Ensemble Models Estimator
        # Decision Tree + Kernel Method
        estimators_rf_svr = [
            ('rf', RandomForestRegressor(n_estimators=30, random_state=random_state)),
            ('svr', SVR(kernel='rbf')),
        ]
        estimators_rf_gpr = [
            ('rf', RandomForestRegressor(n_estimators=30, random_state=random_state)),
            ('gpr', GaussianProcessRegressor(kernel=kernel, normalize_y=True, random_state=random_state))
        ]
        # Decision Trees
        estimators_rf_xgb = [
            ('rf', RandomForestRegressor(n_estimators=30, random_state=random_state)),
            ('xgb', xgb.XGBRegressor(random_state=random_state)),
        ]

        self.models = [
            SymbolicRegressor(random_state=random_state),
            SVR(kernel='rbf'),
            GaussianProcessRegressor(kernel=kernel, normalize_y=True, random_state=random_state),
            DecisionTreeRegressor(random_state=random_state),
            RandomForestRegressor(random_state=random_state),
            xgb.XGBRegressor(random_state=random_state),
            lgbm.LGBMRegressor(n_estimators=50, num_leaves=10, min_child_samples=3, random_state=random_state),
            VotingRegressor(estimators=estimators_rf_svr),
            StackingRegressor(estimators=estimators_rf_svr, 
                              final_estimator=RandomForestRegressor(random_state=random_state)),
            VotingRegressor(estimators=estimators_rf_gpr),
            StackingRegressor(estimators=estimators_rf_gpr, 
                              final_estimator=RandomForestRegressor(random_state=random_state)),
            VotingRegressor(estimators=estimators_rf_xgb),
            StackingRegressor(estimators=estimators_rf_xgb, 
                              final_estimator=RandomForestRegressor(random_state=random_state)),
        ]

        # Define graph titles
        self.titles = [
            "Ground Truth", "Training Points", 
            "SymbolicRegressor", "SVR", "GPR",
            "DecisionTree", "RForest", 
            "XGBoost", "LGBM", 
            "Vote_rf_svr", "Stack_rf_svr__rf",
            "Vote_rf_gpr", "Stack_rf_gpr__rf",
            "Vote_rf_xgb", "Stack_rf_xgb__rf",
        ] 

    def fit_models(self):
        """
        Fit the models to the training data.

        Returns:
            self: Instance of the class with fitted models
        """
        if self.scaling:
            scaler_X = MinMaxScaler()
            self.X_train_scaled = scaler_X.fit_transform(self.X_train)
        else:
            self.X_train_scaled = self.X_train.copy()

        for model in self.models:
            model.fit(self.X_train_scaled, self.y_train)
        return self

    def visualize_surface(self, x0, x1, width=400, height=500,
                          num_panel_columns=5,
                          vertical_spacing=0.06, horizontal_spacing=0,
                          output=None, display=False, return_fig=False):
        """
        Visualize the prediction surface for each model.

        Parameters:
            x0 (np.ndarray): Meshgrid for feature 1
            x1 (np.ndarray): Meshgrid for feature 2
            width (int): Width of the plot
            height (int): Height of the plot
            output (str): File path to save the plot
            display (bool): Flag to display the plot
        """

        num_plots = len(self.models) + 2
        num_panel_rows = num_plots // num_panel_columns

        whole_width = width * num_panel_columns
        whole_height = height * num_panel_rows

        specs = [[{'type': 'surface'} for _ in range(num_panel_columns)] for _ in range(num_panel_rows)]
        fig = make_subplots(rows=num_panel_rows, cols=num_panel_columns, 
                            specs=specs, subplot_titles=self.titles,
                            vertical_spacing=vertical_spacing, 
                            horizontal_spacing=horizontal_spacing)

        for i, model in enumerate([None, None] + self.models):
            # Assign the subplot panels
            row = i // num_panel_columns + 1
            col = i % num_panel_columns + 1

            # Plot training points
            if i == 1:
                fig.add_trace(go.Scatter3d(x=self.X_train[:, 0], y=self.X_train[:, 1], z=self.y_train,
          mode='markers', marker=dict(size=2, color='darkslategray'),
          name='Training Data'), row=row, col=col) 

                surface = go.Surface(z=self.y_truth, x=x0, y=x1, 
                                     showscale=False, opacity=.4)
                fig.add_trace(surface, row=row, col=col)

            # Plot predicted surface for each model and ground truth
            else:
                y_pred = self.y_truth if model is None else model.predict(np.c_[x0.ravel(), x1.ravel()]).reshape(x0.shape)
                surface = go.Surface(z=y_pred, x=x0, y=x1, 
                                     showscale=False)
                fig.add_trace(surface, row=row, col=col)

            fig.update_scenes(dict(
                xaxis_title='x0',
                yaxis_title='x1',
                zaxis_title='y',
                ), row=row, col=col)

        fig.update_layout(title='Model Predictions and Ground Truth', 
                          width=whole_width, 
                          height=whole_height)

        # Change camera angle
        camera = dict(
         up=dict(x=0, y=0, z=1),
         center=dict(x=0, y=0, z=0),
         eye=dict(x=-1.25, y=-1.25, z=2)
        )
        for i in range(num_plots):
            fig.update_layout(**{f'scene{i+1}_camera': camera})

        if display:
            fig.show()

        if output:
            fig.write_html(output)

        if return_fig:
            return fig
```

# 插值性能评估

每个算法的预测曲面分别展示了100个和20个训练数据点的情况。

## 100 个训练点：

原始交互式图形可以从[这里](https://chart-studio.plotly.com/~rkiuchi/87)查看

![](../Images/d6738a6729ec30307118a6d37ce47b23.png)

## 20 个训练点：

原始交互式图形可以从[这里](https://chart-studio.plotly.com/~rkiuchi/89)查看

![](../Images/a5f138d525a873a3a2daeb3f6986afb3.png)

以下是每个算法的总结特征：

## 符号回归

该算法在使用100个数据点进行插值时几乎完美，但在使用20个数据点时表现中等。这是因为符号回归近似数学表达式，并且在这个实践中使用了简单的函数形式。由于这个特性，预测曲面显得特别平滑，这与后面介绍的基于树的算法有所不同。

## 支持向量回归（SVR）、高斯过程回归（GPR）

对于基于核的算法SVR和GPR，尽管预测的表面与真实值略有不同，但在100个数据点的情况下，插值性能通常较好。此外，这些模型得到的预测表面平滑，类似于符号回归器估算的表面。然而，在20个数据点的情况下，尤其是对于SVR，预测表面与真实值之间存在显著差异。

## 决策树、随机森林、XGBoost、LightGBM

首先，这五个基于树的模型估算的预测表面并不平滑，而是呈现出更多阶梯状的形态。这一特征源于决策树的结构和学习方法。决策树通过基于某一特征的阈值递归地划分数据。每个数据点会被分配到某个叶节点，节点内的值表示该节点中数据点的平均值。因此，预测值在每个叶节点内是恒定的，导致了阶梯状的预测表面。

单个决策树的估算清楚地展示了这一特性。另一方面，像随机森林、XGBoost和LightGBM等集成方法，由于每个模型内部包含许多决策树，生成的预测表面相对平滑，因为许多不同形状的决策树基于不同的阈值。

## Voting Regressor, Stacking Regressor

Voting Regressor通过平均两个算法的结果来组合它们。对于如随机森林+SVR和随机森林+GPR的组合，预测表面反映了基于核和基于树的模型特性的混合。另一方面，像随机森林和XGBoost这样的树基模型组合相较于单一模型估算的预测表面，相对减少了阶梯状形态。

Stacking Regressor使用一个元模型基于多个模型的输出计算最终预测，表现出阶梯状的预测表面，因为其元模型使用的是随机森林。若使用基于核的算法，如SVR或GPR，作为元模型，则此特性会发生变化。

# 外推性能评估

如前所述，每个模型的训练数据都包含 *x₀* 和 *x₁* 范围从 -0.5 到 1 的值，且这些性能将在 -1 到 1 的范围内进行评估。因此，我们可以通过检查预测表面在 *x₀* 和 *x₁* 范围从 -1 到 -0.5 的外推能力来了解模型的外推表现。

每个算法的预测表面分别显示了100和20个训练数据点的情况。

## 100个训练点：

原始的互动图表可以从[这里](https://chart-studio.plotly.com/~rkiuchi/91)查看

![](../Images/a2352079e0966edba848a71e21f702ba.png)

## 20个训练点：

原始的互动图表可以从[这里](https://chart-studio.plotly.com/~rkiuchi/93)查看

![](../Images/8b2bef5db2a4e1cf26276c879f3848cf.png)

## 符号回归器

使用100个数据点训练的符号回归器在外推范围内获得的预测曲面几乎与插值评估相似，准确地估算。然而，仅使用20个训练数据点时，预测曲面与真实值存在较大差异，尤其是在曲面边缘，表明得到的函数形式估算不准确。

## 支持向量回归器（SVR），高斯过程回归器（GPR）

尽管SVR和GPR都是基于核的算法，但得到的结果完全不同。对于20个和100个数据点，SVR的预测曲面估算得不理想，而GPR即使在外推范围内也几乎完美地预测了。

## 决策树，随机森林，XGBoost，LightGBM

尽管这些基于树的模型的结果存在一些差异，但在外推范围内的预测曲面是稳定的。这是因为决策树依赖于分割，而在外推区域没有生成分割，这导致了常数值。

## 投票回归器，堆叠回归器

如上所示，基于核的算法相比于基于树的算法具有更好的性能。结合随机森林和XGBoost的投票回归器，以及所有三个以随机森林为元模型的堆叠回归器在外推范围内预测稳定。另一方面，结合随机森林+SVR和随机森林+GPR的投票回归器的预测曲面，具有基于核和基于树的模型的混合特征。

# 总结

在本文中，我们评估了各种机器学习算法的插值和外推性能。由于我们使用的真实数据是以简单的函数形式表示的，符号回归器和基于核的算法在外推方面提供了比基于树的算法更好的性能。然而，对于无法通过数学公式表达的更复杂任务，可能会得出不同的结果。

非常感谢阅读这篇文章！希望这篇文章能帮助你理解机器学习模型的插值和外推性能，使你更容易为你的项目选择和应用合适的模型。

***你对这篇文章的点赞和对*** [***我的新闻通讯***](https://rkiuchir.medium.com/subscribe) ***的订阅将给我带来极大的动力！***

# 链接

## 其他文章

[](/how-openais-sora-is-changing-the-game-an-insight-into-its-core-technologies-bd1ad17170df?source=post_page-----45dd270ee871--------------------------------) [## OpenAI的Sora如何改变游戏规则：深入了解其核心技术

### 一项尖端技术的杰作

towardsdatascience.com](/how-openais-sora-is-changing-the-game-an-insight-into-its-core-technologies-bd1ad17170df?source=post_page-----45dd270ee871--------------------------------) [](/create-interactive-globe-earthquake-plot-in-python-b0b52b646f27?source=post_page-----45dd270ee871--------------------------------) [## 在 Python 中创建“交互式地球仪 + 地震图”

### 如何在 Python 中创建一个酷炫的交互式图形：由 Plotly 绘制的地球仪。

towardsdatascience.com](/create-interactive-globe-earthquake-plot-in-python-b0b52b646f27?source=post_page-----45dd270ee871--------------------------------) [](/pandas-cheat-sheet-for-data-preprocessing-cd1bcd607426?source=post_page-----45dd270ee871--------------------------------) [## Pandas 数据预处理备忘单

### 关于如何使用 Pandas 预处理数据的实用指南

towardsdatascience.com](/pandas-cheat-sheet-for-data-preprocessing-cd1bcd607426?source=post_page-----45dd270ee871--------------------------------)

## 个人网站

[](https://rkiuchir.github.io/?source=post_page-----45dd270ee871--------------------------------) [## R. Kiuchi — 地震学

### 编辑描述

rkiuchir.github.io](https://rkiuchir.github.io/?source=post_page-----45dd270ee871--------------------------------)
