# 最小化医疗人工智能偏差的数据策划实践

> 原文：[https://towardsdatascience.com/data-curation-practices-to-minimize-bias-in-medical-ai-379bf6983de2?source=collection_archive---------8-----------------------#2024-07-17](https://towardsdatascience.com/data-curation-practices-to-minimize-bias-in-medical-ai-379bf6983de2?source=collection_archive---------8-----------------------#2024-07-17)

## 确保医疗人工智能应用程序的公平和公正的健康结果

[](https://medium.com/@fimafurman?source=post_page---byline--379bf6983de2--------------------------------)[![Fima Furman](../Images/5d25a93fa0bf4f5ebb2c7a684709635c.png)](https://medium.com/@fimafurman?source=post_page---byline--379bf6983de2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--379bf6983de2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--379bf6983de2--------------------------------) [Fima Furman](https://medium.com/@fimafurman?source=post_page---byline--379bf6983de2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--379bf6983de2--------------------------------) ·阅读时间 8 分钟·2024年7月17日

--

![](../Images/75a75a38b2d02a467ceedd983e778149.png)

人工智能训练数据中的潜在偏差来源。图形由作者创建。

[人工智能偏见](https://www.ibm.com/topics/ai-bias#:~:text=AI%20bias%2C%20also%20called%20machine,outputs%20and%20potentially%20harmful%20outcomes.) 是指当人工智能系统由于训练数据中的偏见而对不同群体产生不平等的结果时，所发生的歧视现象。如果不加以缓解，人工智能和机器学习模型中的偏见可能会使历史上被边缘化的群体面临的歧视制度化并加剧，将歧视嵌入到决策算法中。

训练数据中的问题，如数据集不具代表性或不平衡、历史偏见嵌入数据中以及数据收集方法存在缺陷，都会导致模型产生偏差。例如，如果一个 [贷款决策应用程序](https://www.forbes.com/sites/korihale/2021/09/02/ai-bias-caused-80-of-black-mortgage-applicants-to-be-denied/)是基于历史决策进行训练的，而历史决策中黑人贷款申请者遭受了系统性的歧视，那么模型将在其决策过程中嵌入这一歧视模式。偏差还可能在特征选择和工程阶段引入，其中某些特征可能无意中充当种族、性别或社会经济地位等敏感特征的替代指标。例如，美国的 [种族和邮政编码](https://engineering.cmu.edu/news-events/news/2018/12/11-datta-proxies.html)通常是密切相关的，因此，使用邮政编码数据训练的算法可能会间接地将种族信息嵌入到其决策过程中。

医疗领域中的人工智能涉及使用机器学习模型和算法来辅助诊断、治疗计划制定和患者护理。在这些情况下，AI偏见可能特别有害，导致医疗服务和结果的显著差异。例如，使用主要以浅色皮肤图像训练的[皮肤癌预测模型](https://healthcare-in-europe.com/en/news/ai-in-skin-cancer-detection-darker-skin-inferior-results.html)可能在肤色较深的患者身上表现不佳。这样的系统可能导致误诊或延迟治疗，从而导致较高的死亡率。鉴于医疗应用中的高风险，数据科学家必须采取措施减少其应用中的AI偏见。本文将重点讨论数据科学家可以采取哪些数据策划技术，以在模型训练之前消除训练集中的偏见。

# 如何衡量AI偏见？

为了减轻AI偏见，了解[模型偏见和公平性如何定义](https://haas.berkeley.edu/wp-content/uploads/What-is-fairness_-EGAL2.pdf)（PDF）并进行衡量是非常重要的。公平/无偏的模型确保其预测在不同群体之间是公平的。这意味着模型的行为（如准确性和选择概率）在由敏感特征（如种族、性别、社会经济地位）定义的子群体之间是可比的。

通过使用量化的AI公平性/偏见指标，我们可以衡量并改善自己的模型。这些指标比较历史上处于特权群体和非特权群体之间的准确率和选择概率。常用的三种衡量AI模型对不同群体是否公平的指标是：

**统计平衡差异——**比较群体之间有利结果的比率。此测试表明，模型的预测与敏感群体成员身份无关，旨在实现群体间的平等选择率。在需要群体之间有相等正向结果的情况下非常有用，例如招聘。

![](../Images/2b27f17da60ccb8313d09b5ff2c7335a.png)

**平均赔率差异——**比较不同群体之间的假阳性和真阳性率差异。此指标比统计平衡差异更为严格，因为它旨在确保群体之间假阳性和真阳性率相等。在假阳性和真阳性都有重大后果的情况下非常有用，例如刑事司法领域。

![](../Images/19f2b6cfa46b7f20330df3ba95fb37e9.png)

**机会平等差异——**比较不同群体之间的真阳性率。它检查不同群体中的合格个体是否有平等的机会被AI系统选中。它不考虑假阳性率，这可能会导致群体之间在错误正预测上的差异。

![](../Images/5d4c980dd327776116cc5b8ca233eeb5.png)

数据科学家可以使用 Python 库，如微软的 [Fairlearn](https://fairlearn.org/) 包或 IBM 的 [AI Fairness 360](https://aif360.res.ibm.com/) 工具包，来计算模型的公平性/偏见度量指标。对于所有这些指标，值为零表示数学上公平的结果。

# 什么样的数据管理实践可以最小化人工智能偏见？

为了减少人工智能训练数据集中的偏见，模型构建者拥有一系列的数据管理技术，这些技术可以分为定量方法（使用数学包进行数据转换）和定性方法（数据收集的最佳实践）。

## **定量实践**

[**移除与敏感特征的相关性**](https://fairlearn.org/v0.10/user_guide/mitigation/preprocessing.html)

![](../Images/b8c9e4419af21c19d12d483a1b4c4279.png)

在一个示例数据集中移除相关性的示意图。图片来自作者。

即使在模型训练中排除了敏感特征（例如，种族、性别），其他特征仍可能与这些敏感特征相关联，从而引入偏见。例如，在美国，邮政编码与种族有很强的相关性。为了确保这些特征不会引入隐藏的偏见，数据科学家应当预处理输入数据，移除其他输入特征与敏感特征之间的相关性。

这可以通过 [Fairlearn 的 CorrelationRemover](https://fairlearn.org/v0.10/user_guide/mitigation/preprocessing.html) 函数来实现。该函数通过数学方法转换特征值，移除相关性，同时保留特征的大部分预测价值。下面是一个示例代码。

```py
from fairlearn.preprocessing import CorrelationRemover
import pandas as pd
data = pd.read_csv('health_data.csv')
X = data[["patient_id", "num_chest_nodules", "insurance", "hospital_code"]]
X = pd.get_dummies(X)
cr = CorrelationRemover(sensitive_feature_ids=['insurance_None'])
cr.fit(X)
X_corr_removed = cr.transform(X)
```

[**使用重新加权和重采样创建平衡样本**](https://link.springer.com/article/10.1007/s10115-011-0463-8)

重新加权和重采样是类似的过程，它们通过创建更平衡的训练数据集来纠正特定群体在输入集中的过度或不足表示。重新加权涉及为数据样本分配不同的权重，以确保被低估的群体对模型学习过程有相应的影响。重采样则是通过过采样少数类实例或欠采样多数类实例来实现数据集的平衡。

如果某个敏感群体在与一般人群相比时被低估，数据科学家可以使用 [AI Fairness 的 Reweighing](https://github.com/Trusted-AI/AIF360/blob/main/aif360/algorithms/preprocessing/reweighing.py) 函数来转换数据输入。下面是示例代码。

```py
from aif360.algorithms.preprocessing import Reweighing
import pandas as pd
data = pd.read_csv('health_data.csv')
X = data[["patient_id", "num_chest_nodules", "insurance_provider", "hospital_code"]]
X = pd.get_dummies(X)
rw = Reweighing(unprivileged_groups=['insurance_None'], 
  privileged_groups=['insurance_Aetna', 'insurance_BlueCross'])
rw.fit(X)
X_reweighted = rw.transform(X)
```

[**使用不均衡影响移除器转换特征值**](https://arxiv.org/abs/1412.3756)

移除训练数据中嵌入的偏见的另一种技术是使用不均衡影响移除器转换输入特征。这项技术通过调整特征值，在由敏感特征定义的群体之间提高公平性，同时保持群体内数据的排序。这既能保持模型的预测能力，又能减少偏见。

要转化特征以消除不公平影响，可以使用[AI公平性工具中的不公平影响消除器](https://github.com/Trusted-AI/AIF360/blob/main/aif360/algorithms/preprocessing/disparate_impact_remover.py)。请注意，该工具仅会根据单一保护属性转化输入数据的公平性，因此无法改善多个敏感特征或敏感特征交集中的公平性。以下是示例代码。

```py
from aif360.algorithms.preprocessing import disparate_impact_remover
import pandas as pd
data = pd.read_csv('health_data.csv')
X = data[["patient_id", "num_chest_nodules", "insurance_provider", "hospital_code"]]
dr = DisparateImpactRemover(repair_level=1.0, sensitive_attribute='insurance_provider')
X_impact_removed = dr.fit_transform(X)
```

[**利用多样化的专家数据标注来最小化标注偏差**](https://arxiv.org/abs/2312.10198)

对于监督学习的应用场景，通常需要对响应变量进行人工数据标注。在这些情况下，不完美的人工数据标注员会将个人偏见引入数据集，然后这些偏见会被机器学习到。当标注员群体较小且缺乏多样性时，这种偏差会更加严重。

为了最小化数据标注过程中的偏差，使用高质量的数据标注解决方案，利用多样化的专家意见，如[Centaur Labs](https://hubs.li/Q02GXtBT0)。通过使用优越的标签置信度度量来算法性地合成多个意见，这样的解决方案可以缓解个体偏差的影响，并[推动标注准确性的巨大提升](https://hubs.li/Q02GXBxn0)，从而提高数据集的标注质量。

![](../Images/73e3cdf4d69095d0aba30161e1608c6d.png)

展示如何通过汇总多个专家意见来提高医疗数据标注的准确性。图像由作者提供。

## 定性实践

**实施包容性和具有代表性的数据收集实践**

医疗AI训练数据必须包含来自所有患者人口群体和条件的足够样本，以便准确地为多样化的患者群体做出预测。为了确保数据集满足这些需求，应用开发者应与相关的医学专家和利益相关者合作，代表受影响的患者群体来定义数据要求。数据科学家可以使用分层抽样来确保他们的训练集不会过度或不足地代表感兴趣的群体。

数据科学家还必须确保收集技术不会引入数据偏差。例如，如果医疗影像设备在不同样本之间不一致，这将会引入系统性的差异。

**确保数据清理实践不引入偏差**

为避免在数据清洗过程中产生偏差，数据科学家必须谨慎处理缺失数据并填补缺失值。当数据集中存在敏感特征（如患者年龄）的缺失值时，简单的策略（如填补平均年龄）可能会扭曲数据，特别是当某些年龄组的样本不足时。相反，可以使用分层插补技术，根据相关子组（例如按年龄段或人口统计类别）内的分布填补缺失值。根据具体情况，像[多重插补](https://www.publichealth.columbia.edu/research/population-health-methods/missing-data-and-multiple-imputation)这样的高级方法，也可以生成多个合理的值并对其进行平均，以考虑不确定性。数据清洗完成后，数据科学家应记录插补过程，并确保清理后的数据集仍然代表性强且无偏，符合预定的标准。

**发布数据整理实践，征求利益相关者的意见**

数据科学家在制定数据整理程序时，应将其发布以征求利益相关者的意见，从而促进透明度和问责制。当利益相关者（例如患者群体代表、研究人员和伦理学家）审查并提供有关数据整理方法的反馈时，有助于在开发过程中尽早识别和解决潜在的偏差来源。此外，利益相关者的参与通过展示对道德和包容性实践的承诺，促进了对AI系统的信任和信心。这种信任对于推动AI系统的部署后使用至关重要。

**定期审计和审查输入数据和模型性能**

定期审计和审查实时模型的输入数据可确保训练集中的偏差不会随着时间的推移而产生。随着医学、患者人口统计和数据来源的变化，之前无偏的模型如果输入数据不再准确代表当前的群体，可能会变得有偏。持续监控有助于识别并修正任何新出现的偏差，确保模型保持公平和有效。

# 总结

![](../Images/4c7e7655123eabedbf45d564a27771ed.png)

图片由[Planet Volumes](https://unsplash.com/@planetvolumes)提供，来自[Unsplash](https://unsplash.com/)

数据科学家必须采取措施，减少医疗 AI 模型中的偏见，以实现公平的患者结果，推动利益相关者的支持，并获得监管批准。数据科学家可以利用来自如[Fairlearn](https://fairlearn.org/)（微软）或[AI Fairness 360 Toolkit](https://aif360.res.ibm.com/)（IBM）等库中的新兴工具来衡量和改善 AI 模型的公平性。尽管这些工具和像统计平衡差异（Statistical Parity Difference）这样的量化指标非常有用，开发人员仍需记住，要采取一种全面的公平性方法。这需要与专家和受影响群体的利益相关者合作，以理解患者群体和 AI 应用的影响。如果数据科学家遵循这一实践，他们将迎来一个更加公正且优越的全民医疗新时代。
