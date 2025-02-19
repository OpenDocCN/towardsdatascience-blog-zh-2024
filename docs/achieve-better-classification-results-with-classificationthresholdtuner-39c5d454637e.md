# 使用**ClassificationThresholdTuner**实现更好的分类结果

> 原文：[`towardsdatascience.com/achieve-better-classification-results-with-classificationthresholdtuner-39c5d454637e?source=collection_archive---------0-----------------------#2024-09-07`](https://towardsdatascience.com/achieve-better-classification-results-with-classificationthresholdtuner-39c5d454637e?source=collection_archive---------0-----------------------#2024-09-07)

## 一款用于调整和可视化二分类和多分类问题阈值选择的 Python 工具

[](https://medium.com/@wkennedy934?source=post_page---byline--39c5d454637e--------------------------------)![W Brett Kennedy](https://medium.com/@wkennedy934?source=post_page---byline--39c5d454637e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--39c5d454637e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39c5d454637e--------------------------------) [W Brett Kennedy](https://medium.com/@wkennedy934?source=post_page---byline--39c5d454637e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39c5d454637e--------------------------------) ·阅读时长 30 分钟·2024 年 9 月 7 日

--

在分类问题中调整阈值（即调整用于决定预测属于某一类别还是另一类别的概率临界值）是一个有时会被忽略的步骤，但其实它非常简单，而且可以显著提高模型的质量。这是一个应该在大多数分类问题中执行的步骤（根据我们希望优化的内容，下面会描述一些例外情况）。

在本文中，我们将更深入地了解在进行此操作时实际发生了什么——特别是对于多分类问题，这可能有些微妙。我们还将介绍一款由我自己编写的开源工具——[ClassificationThresholdTuner](https://github.com/Brett-Kennedy/ClassificationThresholdTuner)，它自动化并向用户描述了这一过程。

由于在分类问题中调整阈值任务的普遍性，以及这个过程通常在不同项目之间的相似性，我已经能够在许多项目中使用这个工具。它消除了我为大多数分类问题添加的许多（几乎是重复的）代码，并提供了更多关于调整阈值的信息，这些是我以前无法得到的。

尽管**ClassificationThresholdTuner**是一个有用的工具，但你可能会发现本文中描述的工具背后的思想更为相关——它们足够简单，可以在你的分类项目中按需复制。

简而言之，[ClassificationThesholdTuner](https://github.com/Brett-Kennedy/ClassificationThresholdTuner)是一个用于优化分类问题中阈值设置的工具，并清晰展示不同阈值的效果。与大多数其他可用选项（以及我们最有可能自行开发的优化阈值的代码）相比，它有两个主要优势：

1.  它提供了可视化工具，帮助数据科学家理解使用已发现的最优阈值的含义，以及可能选择的替代阈值。当向其他利益相关者展示建模决策时，这也非常有价值，例如在需要找到假阳性和假阴性之间的良好平衡时。通常，这需要业务理解和数据建模知识，并且对阈值选择有清晰全面的理解，可以促进讨论并决定最佳平衡。

1.  它支持多类分类，这是机器学习中的常见问题类型，但与二分类相比，调整阈值更为复杂（例如，它需要识别多个阈值）。不幸的是，优化多类分类所使用的阈值并没有得到其他同类工具的良好支持。

虽然支持多类分类是[ClassificationThesholdTuner](https://github.com/Brett-Kennedy/ClassificationThresholdTuner)的一个重要特性，但由于二分类更易于理解，因此我们将从描述二分类开始。

# 分类中使用的阈值是什么？

几乎所有现代分类器（包括 scikit-learn、CatBoost、LGBM、XGBoost 以及大多数其他分类器）都支持同时生成预测和概率。

例如，如果我们创建一个二分类器来预测哪些客户将在明年流失，那么对于每个客户，我们通常可以生成一个二分类预测（对于每个客户，预测为“是”或“否”），或者可以为每个客户生成一个概率（例如，一个客户可能被估计在该时间范围内流失的概率为 0.862）。

给定一个能够生成概率的分类器，即使我们请求二分类预测，它通常在后台仍然会为每条记录生成一个概率。然后，它会将这些概率转换为类预测。

默认情况下，二分类器将在正类的预测概率大于或等于 0.5 时预测为正类，而在预测概率低于 0.5 时预测为负类。在这个例子中（预测流失），默认情况下，如果预测流失的概率≥0.5，它会预测为“是”，否则预测为“否”。

然而，这可能不是理想的行为，通常一个非 0.5 的阈值可能会更好用，可能是稍微低一点或稍微高一点，有时甚至可能是与 0.5 差距较大的阈值。这取决于数据、构建的分类器以及假阳性与假阴性之间的相对重要性。

为了创建一个强大的模型（包括平衡好假阳性和假阴性），我们通常希望优化某个指标，如 F1 分数、F2 分数（或 f-beta 系列中的其他指标）、马修斯相关系数（MCC）、卡帕分数或其他指标。如果是这样，优化这些指标的一个关键部分是适当地设置阈值，这通常会将阈值设置为非 0.5 的值。我们很快会描述这个过程是如何工作的。

这是一个关键点。通常并不容易立即清楚地知道最佳的阈值设置在哪里，但我们通常可以确定最适合优化的指标。一个例子是使用 F2 或 F3 分数，在这种情况下我们希望更强调正类的召回率。

# Scikit-learn 对阈值调优的支持

Scikit-learn 在其[调整分类预测决策阈值](https://scikit-learn.org/stable/modules/classification_threshold.html)页面中提供了关于阈值调优的良好背景信息。Scikit-learn 还提供了两个工具：[FixedThresholdClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.FixedThresholdClassifier.html)和[TunedThresholdClassifierCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TunedThresholdClassifierCV.html#sklearn.model_selection.TunedThresholdClassifierCV)（在 scikit-learn 1.5 版本中引入），用于帮助调节阈值。它们的工作原理与 ClassificationThresholdTuner 非常相似。

Scikit-learn 的工具可以视为便捷的方法，因为它们并不是严格必要的；正如所示，调优在任何情况下都相对简单（至少对于支持二分类的这些工具来说是这样的）。但有这些工具是很方便的——调用它们比自己编写代码要容易得多。

ClassificationThresholdTuner 作为这些工具的替代方案被创建，但当 Scikit-learn 的工具效果良好时，它们也是非常好的选择。特别是在你有一个二分类问题，并且不需要对找到的阈值进行任何解释或描述的情况下，Scikit-learn 的工具可以完美地工作，甚至可能略微更方便，因为它们让我们跳过了安装 ClassificationThresholdTuner 这个小步骤。

在需要对找到的阈值（包括有关阈值替代值的一些背景信息）进行解释，或者你有多分类问题时，ClassificationThresholdTuner 可能更有价值。

如前所述，有时可能是本文中描述的*思想*最为宝贵，而不是具体的工具，您可能最好开发自己的代码——也许是沿着类似的思路，但在执行时间方面进行优化，以更高效地处理您的数据，可能能够支持更多需要优化的其他指标，或者可能提供其他图表和阈值调优过程的描述，从而为您的项目提供相关信息。

# 二分类中的阈值

对于大多数 scikit-learn 分类器，以及 CatBoost、XGBoost 和 LGBM，可以通过调用 `predict_proba()` 来返回每个记录的概率。该函数输出每个记录每个类别的概率。在二分类问题中，它们将为每个记录输出两个概率，例如：

```py
[[0.6, 0.4], 
 [0.3, 0.7], 
 [0.1, 0.9],
  …
]
```

对于每一对概率，我们可以将第一个视为负类的概率，第二个视为正类的概率。

然而，在二分类问题中，一个类别的概率仅仅是另一个类别概率的 1.0 减去，因此严格来说，只需要一个类别的概率。事实上，在处理二分类问题中的类别概率时，我们通常只使用正类的概率，因此可以使用如下的数组：[0.4, 0.7, 0.9, …]。

在二分类中，阈值容易理解，因为它们可以简单地视为需要的最小预测概率，以便正类被预测为正类（在流失的例子中，就是预测客户流失）。如果我们设定阈值为 0.6，那么就可以轻松地将上面的概率数组转换为预测结果，在这个例子中就是：[否， 是， 是， …]。

通过使用不同的阈值，我们可以使模型更积极或更保守地预测正类。如果使用一个相对较低的阈值，比如 0.3，那么即使只有中等概率正确，模型也会预测为正类。与使用 0.5 作为阈值相比，更多的正类预测将会被做出，从而增加真正的正类和假正类，同时减少真正的负类和假负类。

在客户流失的情况下，如果我们希望重点关注捕捉大多数流失案例，即使这样做时，我们也预测许多客户会流失，但实际上他们并不会。也就是说，当漏报（漏掉流失）比误报（错误地预测流失）更为重要时，较低的阈值是有用的。

将阈值设置得更高，例如 0.8，将产生相反的效果：预测流失的客户会减少，但在那些被预测为流失的客户中，很多很可能真的会流失。我们会增加假阴性（漏掉一些实际上会流失的客户），但减少假阳性。这在只能跟进少量潜在流失客户并且希望只标记那些最可能流失的客户时是合适的。

在设定阈值时，几乎总是涉及到强烈的业务因素。像 ClassificationThresholdTuner 这样的工具可以使这些决策更加清晰，否则通常没有明显的阈值选择点。例如，单纯根据直觉选择阈值（可能认为 0.7 差不多合适）通常不会得到最优的结果，且一般不比直接使用默认值 0.5 更好。

设置阈值可能有点不直观：上下调整阈值一点，往往会比预期更大程度地影响模型。有时，例如，增加阈值可以大幅减少假阳性，而对假阴性只有小的影响；在其他情况下，情况可能正好相反。使用接收器操作特征曲线（ROC）是一种很好的方式，能够帮助可视化这些权衡。我们将在下面看到一些例子。

最终，我们会设置一个阈值，以优化某些指标（例如 F1 得分）。ClassificationThresholdTuner 只是一个自动化并描述该过程的工具。

# AUROC 和 F1 得分

通常，我们可以将用于分类的度量指标分为三种主要类型：

+   那些评估预测概率排名情况的指标，例如：接收器操作特征曲线下的面积（AUROC）、精确度-召回率曲线下的面积（AUPRC）。

+   那些评估预测概率校准程度的指标，例如：Brier 得分、对数损失。

+   那些评估预测标签正确性的指标，例如：F1 得分、F2 得分、MCC、Kappa 得分、平衡准确率。

这里列出的前两类度量指标基于预测的概率，而最后一类则基于预测的标签。

尽管每类指标中有很多不同的度量方式，但为了简便起见，我们暂时只考虑其中两种较为常见的度量方式，即接收器操作特征曲线下的面积（AUROC）和 F1 得分。

这两类指标之间有一个有趣的关系（就像 AUROC 与基于预测标签的其他指标之间的关系一样），ClassificationThresholdTuner 利用这一关系来调节和解释最佳阈值。

ClassificationThresholdTuner 背后的理念是，在模型已调优到具有强 AUROC 之后，利用这一点来优化其他指标——这些指标基于预测标签，例如 F1 得分。

# 基于预测标签的度量指标

很多时候，衡量预测标签正确性的指标是分类中最相关的。这种情况发生在模型用于为记录分配预测标签时，相关的是正确的正例、负例、假阳性和假阴性的数量。也就是说，如果下游使用的是预测标签，那么一旦标签被分配，基础预测概率的具体数值就不再重要，重要的只是最终的标签预测。

例如，如果模型为客户分配“是”或“否”标签，以表示他们是否预计在下一年会流失，而那些预测为“是”的客户接受一些处理，而那些预测为“否”的客户不接受，那么最相关的是这些标签的正确性，而不是最终预测概率的排序或校准程度（这些类预测是基于这些概率的）。不过，预测概率的排序如何，正如我们所看到的，对于准确分配预测标签是相关的。

这并不适用于每个项目：通常，像 AUROC 或 AUPRC 这样的指标，衡量预测概率排序如何，才是最相关的；而像 Brier Score 和 Log Loss 这样的指标，衡量预测概率的准确性，通常也最为重要。

调整阈值不会影响这些指标，而在这些指标最为相关的情况下，调整阈值没有必要。但对于本文来说，我们将考虑那些我们希望优化的基于预测标签的指标，例如 F1 得分或其他指标。

ClassificationThresholdTuner 从预测的概率开始（其质量可以通过 AUROC 来评估），然后致力于优化指定的指标（其中指定的指标基于预测标签）。

基于预测标签正确性的指标，都是通过不同方式从混淆矩阵中计算得出的。混淆矩阵又基于选择的阈值，如果使用低阈值或高阈值，其结果可能会大不相同。

# 调整阈值

AUROC 指标顾名思义是基于 ROC 曲线的，ROC 曲线展示了真正例率与假正例率的关系。ROC 曲线并不假设使用任何特定的阈值，但曲线上的每一个点都对应一个特定的阈值。

在下图中，蓝色曲线是 ROC 曲线。该曲线下方的面积（AUROC）衡量了模型的一般强度，基于所有潜在阈值的平均值。它衡量了概率的排序效果：如果概率排序良好，则分配给正类的高预测概率的记录，实际上更有可能属于正类。

例如，AUROC 为 0.95 意味着随机正样本有 95%的概率被排在随机负样本之前。

![](img/4f6b09a80e830f53671f89b2bb7cf940.png)

首先，拥有一个强大的 AUROC 模型非常重要 — 这是模型调优过程的工作（该过程可能会优化其他指标）。这在我们开始调整阈值之前完成，完成后，重要的是要有良好排名的概率，这意味着 AUROC 得分很高。

然后，如果项目需要为所有记录做出类别预测，则必须选择一个阈值（尽管可以使用默认值 0.5，但可能会得到次优结果），这相当于在 ROC 曲线上选择一个点。

上图展示了 ROC 曲线上的两个点。对于每个点，都画了垂直线和水平线，分别指示关联的真正例率和假正例率。

给定 ROC 曲线，当我们向左和向下移动时，表示使用更高的阈值（例如，从绿色线到红色线）。预测为正的记录会减少，因此真正例和假正例都会减少。

当我们向右和向上移动时（例如，从红线到绿线），表示使用更低的阈值。更多的记录会被预测为正例，因此真正例和假正例都会增加。

也就是说，在这里的图中，红线和绿线代表两个可能的阈值。从绿线移动到红线，我们看到真正例率略有下降，但假正例率大幅下降，这使得这个阈值选择比绿线所在的阈值更有可能是更好的选择。但这并不一定 — 我们还需要考虑假正例和假负例的相对成本。

但需要注意的是，从一个阈值移动到另一个阈值通常会比真正例率更大或更小地调整假正例率。

以下展示了一组给定 ROC 曲线的阈值。我们可以看到，从一个阈值移动到另一个阈值时，真正例率和假正例率的变化程度可能会大不相同。

![](img/fdb58fb5016981a701c926b0da9ed734.png)

这就是调整阈值背后的主要思路：通常可以在一个方面获得较大增益，同时在另一个方面仅遭受小幅损失。

可以查看 ROC 曲线，观察上下调整阈值的效果。基于这一点，我们在一定程度上可以凭目测选择一个看起来最佳平衡真正例和假正例的点（这也有效地平衡了假正例和假负例）。在某种意义上，这就是 ClassificationThesholdTuner 的作用，但它以一种有原则的方式进行，从而优化某一指定的指标（如 F1 分数）。

将阈值移动到 ROC 曲线上的不同点会生成不同的混淆矩阵，然后可以将其转换为评估指标（如 F1 分数、F2 分数、MCC 等）。然后，我们可以选择优化该指标的点。

只要模型经过训练，能够获得强大的 AUROC 值，我们通常可以找到一个好的阈值来获得高 F1 得分（或其他类似的度量）。

![](img/eabef9883aa792d33898de49b0ff719e.png)

在这个 ROC 图中，模型非常准确，AUROC 为 0.98。因此，可以选择一个能产生高 F1 得分的阈值，尽管仍然需要选择一个好的阈值，且最优阈值可能并不一定是 0.5。

排名良好的模型不一定是良好校准的，但这并不是必须的：只要正类记录的预测概率普遍高于负类记录的预测概率，我们就能找到一个合适的阈值，将预测为正类的记录与预测为负类的记录分开。

从另一个角度看，我们可以通过两个直方图来查看二元分类问题中概率的分布，如下所示（实际上使用的是 KDE 图）。蓝色曲线表示负类的概率分布，橙色曲线表示正类的概率分布。模型可能没有很好地校准：正类的概率始终远低于 1.0。但是，它们排名良好：正类的概率通常高于负类的概率，这意味着模型的 AUROC 值较高，并且如果使用适当的阈值（在这种情况下，可能是 0.25 或 0.3），模型能够很好地分配标签。尽管分布之间存在重叠，但无法实现完美的记录标签系统，F1 得分也永远不能达到 1.0。

![](img/51b21f477acb1721e97fdd4c126e8e07.png)

即使 AUROC 得分很高，也有可能 F1 得分很低：这通常发生在阈值选择不当时。例如，ROC 曲线可能像上面所示那样贴近轴线——非常低或非常高的阈值可能表现不好。数据不平衡时也可能出现 ROC 曲线紧贴 y 轴的情况。

对于这里展示的直方图，尽管模型校准良好并且 AUROC 得分很高，但如果选择不合适的阈值（如 0.5 或 0.6，这会导致所有预测都为负类），则会导致 F1 得分非常低。

即使 AUROC 较低，也可能出现高 F1 得分的情况。通过选择一个特别好的阈值（在大多数阈值下表现都很差），也可以实现这一点。

此外，虽然不常见，但可能会有不对称的 ROC 曲线，这可能会大大影响最合适的阈值位置。

# 使用 ClassificationThresholdTuner 进行二元分类示例

这是从一个[notebook](https://github.com/Brett-Kennedy/ClassificationThresholdTuner/blob/main/notebooks/binary_classification_threshold_demo.ipynb)中提取的，可以在 GitHub 网站上查看完整的代码。我们将在这里讨论主要的内容。对于这个例子，我们首先生成一个测试数据集。

```py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from threshold_tuner import ClassificationThresholdTuner

NUM_ROWS = 100_000

def generate_data():
    num_rows_per_class = NUM_ROWS // 2
    np.random.seed(0)
    d = pd.DataFrame(
      {"Y": ['A']*num_rows_per_class + ['B']*num_rows_per_class,
       "Pred_Proba": 
          np.random.normal(0.7, 0.3, num_rows_per_class).tolist() + \
          np.random.normal(1.4, 0.3, num_rows_per_class).tolist()
      })
    return d, ['A', 'B']

d, target_classes = generate_data()
```

这里为了简便，我们不生成原始数据或产生预测概率的分类器，而是仅使用一个包含真实标签和预测概率的测试数据集，因为`ClassificationThresholdTuner`正是处理这些内容，而这些内容足以选择最佳的阈值。

![](img/3277acbd53468e2b584a86c1f20a5360.png)

实际上，笔记本中还有一段代码用于对概率进行缩放，确保它们在 0.0 到 1.0 之间，但在这里，我们将假设概率已经很好地进行了缩放。

然后我们可以使用 0.5 的阈值来设置`Pred`列：

```py
d['Pred'] = np.where(d["Pred_Proba"] > 0.50, "B", "A")
```

![](img/ea3ac56911fab0f8dad15da7e4f2e786.png)

这模拟了分类器通常的做法，即简单地使用 0.5 作为阈值。这是我们将尝试超越的基准。

然后我们创建一个`ClassificationThresholdTuner`对象，并使用它来开始，仅仅是为了查看当前预测的强度，调用其 API 之一`print_stats_lables()`。

```py
tuner = ClassificationThresholdTuner()

tuner.print_stats_labels(
    y_true=d["Y"], 
    target_classes=target_classes,
    y_pred=d["Pred"])
```

这会显示两个类别的精确度、召回率和 F1 分数（以及这些分数的宏平均），并呈现混淆矩阵。

![](img/b80a327ff35d0606256f110bb7070bca.png)

这个 API 假定标签已经被预测；如果只有概率而没有标签，则无法使用此方法，尽管我们总是可以像这个例子一样选择一个阈值，并根据这个阈值设置标签。

我们还可以调用`print_stats_proba()`方法，它也会展示一些指标，在这个例子中与预测概率相关。它显示：Brier 分数、AUROC 和几个图表。图表需要一个阈值，若未指定，则使用 0.5，如本例所示：

```py
tuner.print_stats_proba(
    y_true=d["Y"], 
    target_classes=target_classes, 
    y_pred_proba=d["Pred_Proba"])
```

这显示了 0.5 的阈值效果。它展示了 ROC 曲线，ROC 曲线本身不需要阈值，但会在曲线上标出该阈值。然后，它展示了数据如何根据阈值被分成两个预测类别，首先以直方图显示，其次以群体图显示。这里有两个类别，类别 A 为绿色，类别 B（在这个例子中为正类）为蓝色。

![](img/bd0be7a84dad55c1c7c7b73c4e11435c.png)

在群体图中，任何被错误分类的记录都显示为红色。这些是那些真实类别为 A 但 B 的预测概率高于阈值（因此模型会预测为 B），以及那些真实类别为 B 但 B 的预测概率低于阈值（因此模型会预测为 A）的记录。

我们可以使用`plot_by_threshold()`来检查不同阈值的效果：

```py
tuner.plot_by_threshold(
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d["Pred_Proba"])
```

在这个例子中，我们使用默认的潜在阈值集合：0.1、0.2、0.3，……直到 0.9。对于每个阈值，模型会将预测概率超过该阈值的记录预测为正类，将低于该阈值的记录预测为负类。错误分类的记录显示为红色。

![](img/1869104b0c54c931020dc23e4b476f30.png)

为了节省本文的篇幅，这张图片仅展示了三个潜在阈值：0.2、0.3 和 0.4。对于每个阈值，我们可以看到：该阈值在 ROC 曲线上的位置、它导致的数据划分，以及由此产生的混淆矩阵（以及与该混淆矩阵相关的 F1 宏评分）。

我们可以看到，将阈值设为 0.2 时几乎所有的预测结果都是 B（正类）——几乎所有 A 类的记录都被误分类，因此用红色标出。随着阈值的增加，更多记录被预测为 A，B 类的预测减少（尽管在阈值为 0.4 时，大多数真实 B 类记录被正确预测为 B；直到阈值接近 0.8 时，几乎所有真实的 B 类记录都被错误预测为 A：很少有记录的预测概率超过 0.8）。

对从 0.1 到 0.9 的九个可能值进行检查，可以很好地概览潜在的阈值范围，但调用此函数显示一个更窄且更现实的可能值范围可能更有用，例如：

```py
tuner.plot_by_threshold(
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d["Pred_Proba"], 
    start=0.50, end=0.55, num_steps=6)
```

这将展示从 0.50 到 0.55 的每个阈值。展示其中的前两个：

![](img/398801066ab07d8afc4c9e286fc044b8.png)

这个 API 有助于展示不同阈值的影响。

我们还可以查看这个调用`describe_slices()`，它描述了潜在阈值对之间的数据（即数据的切片），以便更清楚地看到将阈值从一个潜在位置移动到下一个位置时的具体变化（我们可以看到每个真实类别会有多少被重新分类）。

```py
tuner.describe_slices(    
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d["Pred_Proba"], 
    start=0.3, end=0.7, num_slices=5)
```

这将以视觉和表格格式显示每个切片：

![](img/97914fa63002cc3fe5fb03ca6ec8d0c2.png)

这里的切片相对较薄，因此我们可以看到两个图，一个展示了它们在完整概率范围内的上下文（左图），另一个则是放大的（右图）。

我们可以看到，例如，将阈值从 0.38 调整到 0.46 时，我们会重新分类第 3 个切片中的点，该切片中有 17,529 个真实的 A 类实例和 1,464 个真实的 B 类实例。这在最右侧的散点图和表格中都有明显体现（在散点图中，第 3 个切片内的绿色点比蓝色点多得多）。

这个 API 也可以用于更窄且更现实的潜在阈值范围：

```py
tuner.describe_slices(    
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d["Pred_Proba"], 
    start=0.4, end=0.6, num_slices=10)
```

这将生成：

![](img/da5ab6866d1b86849bc9353e632302ca.png)

调用这些（或其他有用的 API，如`print_stats_table()`，这里为了简洁略过，但在 github 页面和示例笔记本中有描述）之后，我们可以对移动阈值的效果有所了解。

然后我们可以进入主要任务，使用`tune_threshold()` API 寻找最优阈值。对于一些项目来说，这可能是唯一会被调用的 API，或者它可能首先被调用，随后使用上述 API 来提供关于发现的最优阈值的上下文。

在此示例中，我们优化 F1 宏评分，尽管任何由 scikit-learn 支持并基于类别标签的指标都是可以的。某些指标需要额外的参数，这些参数也可以在此传递。在此示例中，scikit-learn 的 f1_score()需要‘average’参数，该参数作为 tune_threshold()的参数传递。

```py
from sklearn.metrics import f1_score

best_threshold = tuner.tune_threshold(
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d["Pred_Proba"],
    metric=f1_score,
    average='macro',
    higher_is_better=True,
    max_iterations=5
)
best_threshold
```

这将可选地显示一组图表，演示该方法在五次迭代过程中（在此示例中，max_iterations 指定为 5）如何逐步收敛到优化指定指标的阈值。

第一轮考虑了 0.0 到 1.0 之间的所有潜在阈值范围。然后它将范围缩小到 0.5 到 0.6，并在下一轮中更详细地检查该范围，依此类推。最后选择了 0.51991 作为阈值。

![](img/388aed8adf00e382e0b6b6e832540151.png)

之后，我们可以再次调用 print_stats_labels()，它会显示：

![](img/f97102f666acca6171907243366fb74b.png)

我们可以看到，在此示例中，宏 F1 得分从 0.875 提高到 0.881。在这种情况下，提升很小，但几乎是免费的。在其他情况下，提升可能更小或更大，有时甚至更大。它也从不会适得其反；最坏的情况是，找到的最佳阈值仍然是默认值 0.5000。

# 多类分类中的阈值

如所示，多类分类要比二类分类稍微复杂。在二类分类的情况下，选择一个单一的阈值，而在多类分类中，ClassificationThesholdTuner 会为每个类别识别一个最佳阈值。

与二类情况不同，我们需要指定一个类别作为默认类别。通过一个示例可以更清楚地了解为什么需要这样做。

在许多情况下，拥有一个默认类别是相当自然的。例如，如果目标列代表各种可能的医疗状况，默认类别可能是“无问题”，其他类别则分别与特定的疾病相关。对于这些疾病中的每一种，我们都会设定一个最低预测概率，要求该条件的预测需要达到此概率。

或者，如果数据表示网络日志，并且目标列涉及各种入侵类型，那么默认类别可能是“正常行为”，其他类别则分别与特定的网络攻击相关。

在网络攻击的示例中，我们可能有一个数据集，包含四个不同的目标值，目标列包含类别：“正常行为”、“缓冲区溢出”、“端口扫描”和“网络钓鱼”。对于我们运行预测的任何记录，都会得到每个类别的概率，这些概率的总和为 1.0。例如，我们可能会得到：[0.3, 0.4, 0.1, 0.2]（按照上述顺序，每个类别的概率）。

通常情况下，我们会预测“缓冲区溢出”，因为它具有最高的概率 0.4。然而，我们可以设置一个阈值，以修改这一行为，这将影响该类别的假阴性和假阳性率。

我们可以指定，例如：默认类是“正常行为”；“缓冲区溢出”的阈值是 0.5；“端口扫描”的阈值是 0.55；“钓鱼攻击”的阈值是 0.45。按照惯例，默认类的阈值设为 0.0，因为它实际上不使用阈值。因此，这里阈值的集合将是：0.0，0.5，0.55，0.45。

然后，为了对任何给定记录做出预测，我们仅考虑那些概率超过相关阈值的类。在这个示例中（预测值为[0.3, 0.4, 0.1, 0.2]），没有任何概率超过其阈值，因此预测为默认类“正常行为”。

如果预测概率为：[0.1, 0.6, 0.2, 0.1]，那么我们将预测为“缓冲区溢出”：概率（0.6）是最高的预测，并且超过其阈值（0.5）。

如果预测概率为：[0.1, 0.2, 0.7, 0.0]，那么我们将预测为“端口扫描”：概率（0.7）超过其阈值（0.55），这是最高的预测。

这意味着：如果一个或多个类的预测概率超过其阈值，我们将选择其中预测概率最高的类。如果没有类超过其阈值，则选择默认类。而且，如果默认类的预测概率最高，那么将预测为默认类。

因此，需要一个默认类来覆盖当没有任何预测值超过该类阈值的情况。

如果预测值为：[0.1, 0.3, 0.4, 0.2]，阈值为：[0.0, 0.55, 0.5, 0.45]，另一种看法是：通常会预测第三类：它具有最高的预测概率（0.4）。但是，如果该类的阈值是 0.5，那么 0.4 的预测值不够高，因此我们选择下一个最高的预测值，即第二类，预测概率为 0.3。这低于其阈值，所以我们继续选择下一个最高的预测值，即第四类，预测概率为 0.2。它也低于该目标类的阈值。在这里，我们所有类的预测值都相对较高，但都不够高，因此使用默认类。

这也突出了为何使用 0.0 作为默认类阈值是方便的——当检查默认类的预测时，我们无需考虑其预测是否低于或超过该类的阈值；我们总是可以做出默认类的预测。

实际上，原则上也可以有更复杂的策略——不仅使用单一的默认类，而是根据不同的条件选择多个类。但这些超出了本文的范围，通常是多余的，并且当前版本的 ClassificationThresholdTuner 不支持这些策略。在本文的其余部分，我们将假设指定了单一的默认类。

# 使用 ClassificationThresholdTuner 进行多类分类的示例

再次，我们将从创建测试数据开始（使用[示例笔记本](https://github.com/Brett-Kennedy/ClassificationThresholdTuner/blob/main/notebooks/multiclass_classification_threshold_demo.ipynb)中提供的一个测试数据集，进行多类分类），在这种情况下，有三个目标类别，而不仅仅是两个：

```py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from threshold_tuner import ClassificationThresholdTuner

NUM_ROWS = 10_000

def generate_data():
    num_rows_for_default = int(NUM_ROWS * 0.9) 
    num_rows_per_class = (NUM_ROWS - num_rows_for_default) // 2
    np.random.seed(0)
    d = pd.DataFrame({
      "Y": ['No Attack']*num_rows_for_default + ['Attack A']*num_rows_per_class + ['Attack B']*num_rows_per_class,
      "Pred_Proba No Attack": 
          np.random.normal(0.7, 0.3, num_rows_for_default).tolist() + \
          np.random.normal(0.5, 0.3, num_rows_per_class * 2).tolist(),
      "Pred_Proba Attack A": 
          np.random.normal(0.1, 0.3, num_rows_for_default).tolist() + \
          np.random.normal(0.9, 0.3, num_rows_per_class).tolist() + \
          np.random.normal(0.1, 0.3, num_rows_per_class).tolist(),
      "Pred_Proba Attack B": 
          np.random.normal(0.1, 0.3, num_rows_for_default).tolist() + \
          np.random.normal(0.1, 0.3, num_rows_per_class).tolist() + \
          np.random.normal(0.9, 0.3, num_rows_per_class).tolist()                    
    })
    d['Y'] = d['Y'].astype(str)
    return d, ['No Attack', 'Attack A', 'Attack B']

d, target_classes = generate_data()
```

笔记本中有一些代码用于缩放分数并确保它们的和为 1.0，但在这里，我们可以假设这已经完成，并且我们为每条记录准备了一组结构良好的每个类别的概率。

正如真实世界数据中常见的那样，其中一个类别（‘无攻击’类别）比其他类别频繁得多；该数据集是不平衡的。

然后我们设置目标预测，目前只是选择概率最高的类别：

```py
def set_class_prediction(d):    
    max_cols = d[proba_cols].idxmax(axis=1)
    max_cols = [x[len("Pred_Proba_"):] for x in max_cols]
    return max_cols   

d['Pred'] = set_class_prediction(d)
```

这会生成：

![](img/08bf1dae2829cd7aa9d1b78704229122.png)

选择概率最高的类别是默认行为，在这个例子中，这是我们希望超越的基准。

我们可以像处理二分类情况一样，调用`print_stats_labels()`，其功能类似，处理任意数量的类别：

```py
tuner.print_stats_labels(
    y_true=d["Y"], 
    target_classes=target_classes,
    y_pred=d["Pred"])
```

这会输出：

![](img/89efe0526a77487ebcefdbc27f89ccc7.png)

使用这些标签，我们得到的 F1 宏观评分仅为 0.447。

调用`print_stats_proba()`，我们还可以获得与预测概率相关的输出：

![](img/82488ef664db4d967a261c772e6c5cda.png)

这比二分类情况更为复杂，因为我们需要考虑三个概率：每个类别的概率。因此，我们首先展示数据如何相对于每个类别的概率对齐。在这种情况下，有三个目标类别，所以第一行有三个图表。

正如预期的那样，当基于‘无攻击’的预测概率绘制数据时（最左侧的图表），‘无攻击’记录被赋予了比其他类别更高的概率。对于‘攻击 A’（中间图表）和‘攻击 B’（最右侧的图表）也是类似的。

我们还可以看到，类别之间并未完全分离，因此没有一组阈值能产生完美的混淆矩阵。我们需要选择一组阈值，最好能够平衡每个类别的正确预测和错误预测。

在上图中，底部的图表显示每个点的真实类别的概率。对于真实类别为‘无攻击’的记录（绿色点），我们根据它们的‘无攻击’预测概率绘制这些点；对于真实类别为‘攻击 A’的记录（深蓝色），我们根据它们的‘攻击 A’预测概率绘制这些点；‘攻击 B’（深黄色）也是类似的。我们可以看到，模型对‘攻击 A’和‘攻击 B’的概率相似，并且这些类别的概率高于‘无攻击’。

上述图表未考虑任何可能使用的特定阈值。我们还可以选择性地生成更多输出，传入一组阈值（每个类别一个，使用 0.0 表示默认类别）：

```py
tuner.print_stats_proba(
    y_true=d["Y"], 
    target_classes=target_classes, 
    y_pred_proba=d[proba_cols].values,
    default_class='No Attack',
    thresholds=[0.0, 0.4, 0.4]
)
```

这对于绘制工具发现的最优阈值集可能最为有用，但也可以用于查看其他潜在的阈值集。

这为每个类别生成报告。为了节省空间，这里只展示 Class Attack A 的报告（完整的报告可以在示例笔记本中查看；查看其他两个类别的报告也有助于理解在此示例中使用[0.0, 0.4, 0.4]作为阈值的全部影响）：

![](img/b4fb08fda5476d5559a4847801913976.png)

由于这里指定了一组阈值，我们可以看到使用这些阈值的影响，包括每个类别会被正确和错误分类的数量。

我们首先查看阈值在 ROC 曲线上的位置。在这种情况下，我们查看的是 Class A 的报告，所以看到阈值为 0.4（0.4 在上面的 API 调用中为 Class A 指定）。

还展示了 AUROC 分数。这个指标仅适用于二分类预测，但在多分类问题中，我们可以通过将问题视为一系列的“一个对其他”问题，来为每个类别计算 AUROC 分数。在这里，我们可以将问题视为‘Attack A’与非‘Attack A’（对其他报告也可以做类似处理）。

接下来的图显示了每个类别相对于预测概率的分布。由于不同类别的数量不同，这里展示了两种方式：一种展示实际分布，另一种展示经缩放后便于比较的分布。前者更为相关，但后者可以让所有类别清晰可见，尤其是当某些类别远比其他类别稀有时。

我们可以看到，真实类别为‘Attack A’（深蓝色）的记录确实具有更高的‘Attack A’预测概率，但仍然需要决定具体设置阈值的位置。这里我们看到了使用 0.4 作为该类别阈值的效果。看起来 0.4 可能接近理想值，甚至可能就是理想值。

我们也可以以群体图的形式看到这一点（最右侧的图），误分类的点用红色表示。我们可以看到，使用更高的阈值（例如 0.45 或 0.5），会有更多的记录将真实类别为 Attack A 的误分类，但对于真实类别为‘No Attack’的误分类记录则较少。而使用更低的阈值（例如 0.3 或 0.35）则会产生相反的效果。

我们也可以调用 plot_by_threshold()来查看不同的潜在阈值：

```py
tuner.plot_by_threshold(
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d[proba_cols].values,
    default_class='No Attack'
)
```

这个 API 只是为了说明，并非用于调优，因此为了简便起见（对于每个潜在阈值），每个类别使用相同的阈值（默认类别除外）。展示这三个潜在阈值 0.2、0.3 和 0.4：

![](img/8d9a904beadcd0d787ce82839fa9206d.png)

第一行数字显示了使用 0.2 作为所有类别（默认类别除外）的阈值的含义（即，只有当攻击 A 的估计概率至少为 0.2 时才预测攻击 A；只有当攻击 B 的预测概率至少为 0.2 时才预测攻击 B——否则始终选择预测概率最高的类别）。类似地，第二行和第三行分别对应于 0.3 和 0.4 的阈值。

我们可以在这里看到，使用较低或较高阈值对每个类别的权衡，以及由此产生的混淆矩阵（以及与这些混淆矩阵相关的 F1 分数）。

在这个例子中，从 0.2 移动到 0.3，再到 0.4，我们可以看到模型越来越少地预测攻击 A 或攻击 B（提高阈值后，除了默认类别外，我们越来越少预测其他任何类别），而更多地预测“无攻击”，这导致真实类别为“无攻击”的误分类减少，但真实类别为攻击 A 或攻击 B 的误分类增加。

当阈值非常低时，比如 0.2，那么在真实类别为默认类别的记录中，只有那些预测为“无攻击”（No Attack）类别的概率最高的记录（大约是前半部分）被正确预测。

一旦阈值设置在 0.6 以上，几乎所有的预测都会是默认类别，因此所有真实类别为默认类别的情况都是正确的，而其他情况都是错误的。

正如预期的那样，设置较高的阈值意味着更频繁地预测默认类别，错过这些的情况较少，尽管错过其他类别的情况增多。使用较低阈值时，攻击 A 和攻击 B 通常会被正确预测，而使用较高阈值时则大多预测错误。

为了调整阈值，我们再次使用 tune_threshold()，代码如下：

```py
from sklearn.metrics import f1_score

best_thresholds = tuner.tune_threshold(
    y_true=d['Y'], 
    target_classes=target_classes,
    y_pred_proba=d[proba_cols].values,
    metric=f1_score,
    average='macro',
    higher_is_better=True,
    default_class='No Attack',
    max_iterations=5
)
best_thresholds
```

这输出： [0.0, 0.41257, 0.47142]。也就是说，它找到了大约 0.413 的阈值对于攻击 A 最为合适，而 0.471 对于攻击 B 最适合，以优化指定的指标——在这种情况下为宏 F1 分数。

再次调用 print_stats_proba()，我们得到：

```py
tuner.print_stats_proba(
    y_true=d["Y"], 
    target_classes=target_classes, 
    y_pred_proba=d[proba_cols].values,
    default_class='No Attack',
    thresholds=best_thresholds
)
```

其输出为：

![](img/055dbacf61aea08921cc545007ada920.png)

使用这里发现的阈值，宏 F1 分数从大约 0.44 提高到 0.68（结果会随着每次运行略有不同）。

# get_predictions()

提供了一个额外的 API，非常方便，即 get_predictions()，用于根据一组预测和阈值获取标签预测。它的调用方式如下：

```py
tuned_pred = tuner.get_predictions(
    target_classes=target_classes,
    d["Pred_Proba"], 
    None, 
    best_threshold)
```

# 使用真实数据集进行测试

还在许多真实数据集上进行了测试。通常情况下，发现的阈值与默认值差不多，但更常见的是，发现的阈值明显更有效。在 GitHub 页面上包括了一个[笔记本](https://github.com/Brett-Kennedy/ClassificationThresholdTuner/blob/main/notebooks/Real_Datasets.ipynb)，涵盖了少量（四个）真实数据集。这个笔记本的提供更多是为了展示如何使用该工具及其生成的图表（与解释工具时使用的合成数据不同），但也提供了一些例子，说明该工具确实能提高 F1 宏观得分。

简单总结一下，就发现的阈值和 F1 宏观得分的提升而言：

乳腺癌：发现了一个最佳阈值 0.5465，令宏观 F1 分数从 0.928 提升到 0.953。

钢板故障：发现了一个最佳阈值 0.451，令宏观 F1 分数从 0.788 提升到 0.956。

Phenome 发现了一个最佳阈值 0.444，令宏观 F1 分数从 0.75 提升到 0.78。

在数字数据集上，没有发现比默认阈值更好的结果，但使用不同的分类器或在其他不同条件下可能会有所不同。

# 安装

本项目使用了一个[单一 .py 文件](https://github.com/Brett-Kennedy/ClassificationThresholdTuner/blob/main/threshold_tuner.py)。

这必须复制到你的项目中并进行导入。例如：

```py
from threshold_tuner import ClassificationThesholdTuner

tuner = ClassificationThesholdTuner()
```

# 在多类别问题中设置阈值的影响

在多类别设置中设置阈值有一些微妙的点，这些点可能与任何特定项目相关，也可能无关。这些细节可能会超出你的工作需求，而且这篇文章已经相当长了，但在主 GitHub 页面上有一个部分专门讨论了这些相关情况。特别地，阈值设置在 0.5 以上时，与 0.5 以下的阈值相比，可能会有些不同的表现。

# 结论

虽然调整分类项目中使用的阈值并不总是能提高模型的质量，但通常情况下确实能提高，而且提高的幅度往往很大。这很容易做到，但使用 ClassificationThresholdTuner 可以让这个过程更轻松，特别是在多类别分类中，它可以特别有用。

它还提供了阈值选择的可视化图表，这对于理解和接受它发现的阈值，或选择其他阈值以更好地符合项目目标，都是很有帮助的。

在多类别分类中，理解调整阈值的效果可能仍然需要一些努力，但有了像这样的工具，理解起来比没有工具要容易得多，而且在许多情况下，简单地调整阈值并测试结果就足够了。

所有图片均由作者提供
