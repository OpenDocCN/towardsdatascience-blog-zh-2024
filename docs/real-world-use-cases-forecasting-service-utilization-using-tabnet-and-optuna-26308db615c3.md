# 真实世界的应用案例：使用 Tabnet 和 Optuna 进行服务利用率预测

> 原文：[https://towardsdatascience.com/real-world-use-cases-forecasting-service-utilization-using-tabnet-and-optuna-26308db615c3?source=collection_archive---------8-----------------------#2024-08-15](https://towardsdatascience.com/real-world-use-cases-forecasting-service-utilization-using-tabnet-and-optuna-26308db615c3?source=collection_archive---------8-----------------------#2024-08-15)

![](../Images/316bd735324a679d8146caf7b72e8144.png)

图片由 Dall-e 生成

## 数据科学在实际应用中最为出色。我打算分享一些我参与的各种生产化项目中的见解。

[](https://medium.com/@hampusg?source=post_page---byline--26308db615c3--------------------------------)[![Hampus Gustavsson](../Images/a22f27fc40e4a612058379928d30f609.png)](https://medium.com/@hampusg?source=post_page---byline--26308db615c3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--26308db615c3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--26308db615c3--------------------------------) [Hampus Gustavsson](https://medium.com/@hampusg?source=post_page---byline--26308db615c3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--26308db615c3--------------------------------) ·阅读时间：7 分钟·2024年8月15日

--

在我作为数据科学家的这些年里，我遇到了许多有兴趣成为数据科学家的学生，或者是刚毕业的新手。开始数据科学的职业生涯，像任何领域一样，都需要经历一个陡峭的学习曲线。

我经常被问到一个非常好的问题：*我已经学到了很多数据科学的理论知识，但现实世界中的例子是什么样的呢？*

我希望分享一些小的工作片段，来自我整个职业生涯中参与的不同项目。虽然其中一些可能已有几年历史，但我只会写我仍然认为有价值的主题。我会尽量保持整体画面清晰简洁，让有志的同行能了解接下来可能会遇到的内容。但我也希望停下来深入探讨细节，期望有经验的开发者能从中获得一些启示。

**商业案例**

现在，让我们深入探讨推动这一项目的具体业务案例。团队包括项目经理、客户利益相关者和我本人。客户需要一种方法来预测特定服务的使用情况。这样做的原因是为了服务的资源分配和动态定价。关于服务使用行为的经验主要由经验丰富的同事掌握，而这个应用程序旨在增强对他们退休和相关知识流失的抵御能力。此外，使用这种工具可以使新员工的入职过程变得更加简便。

**数据和分析设置**

数据包含了很多特征，包括分类特征和数值特征。在此用例中，需要预测具有动态时间范围的使用情况，即需要对未来不同时间段的使用情况进行预测。还需要预测许多相关和不相关的值。

这些多变量时间序列使得关注点主要集中在基于时间序列的模型实验上。但最终，Tabnet被采纳为处理表格数据的模型。

Tabnet架构中有几个有趣的特性。本文不会深入探讨模型细节，但我建议进行一些相关的理论研究。如果找不到合适的资源，我认为[这篇文章](https://medium.com/@turkishtechnology/deep-learning-with-tabnet-b881236e28c1)是一个很好的概述，或者可以参考[这篇论文](https://arxiv.org/abs/1908.07442)进行更深入的探索。

作为超参数调优框架，使用了Optuna。虽然Python中还有其他框架可供选择，但我至今没有找到不使用Optuna的理由。Optuna被用作贝叶斯超参数调优，并保存到磁盘。其他功能包括提前停止和热启动。提前停止用于节省资源，防止没有前景的实验运行过长时间。热启动则是从先前的实验开始调整。我觉得当新数据到来时，这一点非常有用，可以避免从头开始调优。

初始参数宽度将根据[Tabnet文档](https://github.com/dreamquark-ai/tabnet)中的建议或[Tabnet论文](https://arxiv.org/abs/1908.07442)中讨论的参数范围进行设置。

为了表达残差的异方差性，采用了Tabnet作为分位回归模型。为此，或者说为了以这种方式实现任何模型，使用了*弹球损失函数*，并设置了适当的上下分位点。该损失函数具有偏斜性，根据误差是正值还是负值不平等地惩罚误差。

**代码演示**

这些代码片段所用的要求如下。

```py
pytorch-tabnet==4.1.0
optuna==3.6.1
pandas==2.1.4
```

定义模型的代码。

```py
import os

from pytorch_tabnet.tab_model import TabNetRegressor
import pandas as pd
import numpy as np

from utils import CostumPinballLoss

class mediumTabnetModel:

   def __init__(self,
                model_file_name,
                dependent_variables=None,
                independent_variables=None,
                batch_size=16_000,
                n_a=8,
                n_steps=3,
                n_independent=2,
                n_shared=2,
                cat_idxs=[],
                cat_dims=[],
                quantile=None):
       self.model_file_name = model_file_name
       self.quantile = quantile
       self.clf = TabNetRegressor(n_d=n_a,
                                  n_a=n_a,
                                  cat_idxs=cat_idxs,
                                  cat_dims=cat_dims,
                                  n_steps=n_steps,
                                  n_independent=n_independent,
                                  n_shared=n_shared)
       self.batch_size = batch_size
       self.independent_variables = independent_variables
       self.dependent_variables = dependent_variables
       self.cat_idxs = cat_idxs  # Indexes for categorical values.
       self.cat_dims = cat_dims  # Dimensions for categorical values.
       self.ram_data = None

def fit(self, training_dir, train_date_split):

   if self.ram_data is None:
       data_path = os.path.join(training_dir, self.training_data_file)
       df = pd.read_parquet(data_path)

       df_train = df[df['dates'] < train_date_split]
       df_val = df[df['dates'] >= train_date_split]

       x_train = df_train[self.independent_variables].values.astype(np.int16)
       y_train = df_train[self.dependent_variables].values.astype(np.int32)

       x_valid = df_val[self.independent_variables].values.astype(np.int16)
       y_valid = df_val[self.dependent_variables].values.astype(np.int32)

       self.ram_data = {'x_train': x_train,
                        'y_train': y_train,
                        'x_val': x_valid,
                        'y_val': y_valid}

   self.clf.fit(self.ram_data['x_train'],
                self.ram_data['y_train'],
                eval_set=[(self.ram_data['x_val'],
                           self.ram_data['y_val'])],
                batch_size=self.batch_size,
                drop_last=True,
                loss_fn=CostumPinballLoss(quantile=self.quantile),
                eval_metric=[CostumPinballLoss(quantile=self.quantile)],
                patience=3)

   feat_score = dict(zip(self.independent_variables, self.clf.feature_importances_))
   feat_score = dict(sorted(feat_score.items(), key=lambda item: item[1]))
   self.feature_importances_dict = feat_score 
   # Dict of feature importance and importance score, ordered.
```

作为数据处理框架，使用了Pandas。我还推荐使用Polars，它是一个更高效的框架。

Tabnet 实现包含了预构建的局部和全局特征重要性属性，已附加到拟合模型中。这方面的内部工作可以在之前发布的文章中进行研究，但就商业用例而言，这有两个目的：

+   Sanity check — 客户可以验证模型。

+   商业洞察 — 该模型可以为客户提供关于业务的新洞察。

与主题专家一起。在最终应用中，解释性被包括进来，以便展示给用户。由于数据匿名化，本篇文章不会深入探讨解释性，而是保留在能够讨论和展示模型中真实特征的案例中再进行说明。

拟合和搜索步骤的代码。

```py
import optuna
import numpy as np

def define_model(trial):
   n_shared = trial.suggest_int('n_shared', 1, 7)
   logging.info(f'n_shared: {n_shared}')

   n_independent = trial.suggest_int('n_independent', 1, 16)
   logging.info(f'n_independent: {n_independent}')

   n_steps = trial.suggest_int('n_steps', 2, 8)
   logging.info(f'n_steps: {n_steps}')

   n_a = trial.suggest_int('n_a', 4, 32)
   logging.info(f'n_a: {n_a}')

   batch_size = trial.suggest_int('batch_size', 256, 18000)
   logging.info(f'batch_size: {batch_size}')

   clf = mediumTabnetModel(model_file_name=model_file_name,
                           dependent_variables=y_ls,
                           independent_variables=x_ls,
                           n_a=n_a,
                           cat_idxs=cat_idxs,
                           cat_dims=cat_dims,
                           n_steps=n_steps,
                           n_independent=n_independent,
                           n_shared=n_shared,
                           batch_size=batch_size,
                           training_data_file=training_data_file)

   return clf

def objective(trial):
   clf = define_model(trial)

   clf.fit(os.path.join(args.training_data_directory, args.dataset),
           df[int(len(df) * split_test)])

   y_pred = clf.predict(predict_data)
   y_true = np.array(predict_data[y_ls].values).astype(np.int32)

   metric_value = call_metrics(y_true, y_pred)

   return metric_value

study = optuna.create_study(direction='minimize',
                            storage='sqlite:///db.sqlite3',
                            study_name=model_name,
                            load_if_exists=True)

study.optimize(objective,
               n_trials=50)
```

数据正在被分割为训练集、验证集和测试集。不同数据集的用途如下：

+   训练集。是模型学习的基准数据集。本项目中占数据的80%。

+   验证集。是Optuna计算其指标的数据集，因此该指标是优化的目标。本项目中占数据的10%。

+   测试集。这个数据集用于确定模型的真实表现。如果该指标不够好，可能值得回去调查其他模型。这个数据集还用于决定何时停止超参数调优。KPI的推导以及与利益相关者共享的可视化图表也是基于这个数据集。

最后需要注意的是，为了尽可能模拟模型部署时的行为，数据集是按时间分割的。这意味着，周期的前80%的数据用于训练部分，接下来的10%用于验证，最近的10%用于测试。

![](../Images/457c9b60de76deafea9d022d15c28ab4.png)

时间序列数据分割的示意图。图表由作者创建。

对于这里展示的例子，试验结果被保存到磁盘。更常见的方法是将其保存到云存储中，以便更好地访问和更容易维护。Optuna 还附带了一个可视化的 UI，可以通过在终端中运行以下命令启动：

```py
pip install optuna-dashboard

cd /path/to/directory_with-db.sqlite3/

optuna-dashboard sqlite:///db.sqlite3
```

用于 sanity check 的手动任务是查看最优参数与采样限制的接近程度。如果它们距离设置的边界合理地远，则无需进一步拓展搜索空间。

对于调优中显示的内容的深入分析，请查看[这里](https://optuna-dashboard.readthedocs.io/en/latest/getting-started.html)。

这里是一些结果的可视化。

![](../Images/2aa2d5280c0f9aebe258635ec2917aba.png)![](../Images/fa264aa50a22d3afb54580c6f7c987a0.png)![](../Images/5a099d16c8bb0be766afb92ee9ce6adf.png)

模型表现的可视化。图表由作者创建。

**结论和客户反馈。**

图表显示，随着预测服务使用情况的时间延长，预测的不确定性增加。这是预期中的情况，客户也确认了这一点。

如所注意到的，模型在识别不寻常的峰值时遇到了困难。在实际使用案例中，努力的重点是寻找更多的数据源，看看模型是否能够更好地预测这些异常值。

在最终产品中，还引入了对预测数据点的新颖性评分，使用了Deepchecks库。这是在与客户的讨论中提出的，目的是检测数据漂移，并为用户提供数据的见解。在另一篇文章中，会深入探讨如何开发这一功能。

**感谢阅读！**

希望你觉得这篇文章有用和/或启发。如果你有任何评论或问题，请随时联系我！你也可以在[LinkedIn](https://www.linkedin.com/in/hampus-gustavsson-23721a116/)上与我联系。
