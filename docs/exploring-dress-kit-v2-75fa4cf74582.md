# 探索DRESS Kit V2

> 原文：[https://towardsdatascience.com/exploring-dress-kit-v2-75fa4cf74582?source=collection_archive---------7-----------------------#2024-10-16](https://towardsdatascience.com/exploring-dress-kit-v2-75fa4cf74582?source=collection_archive---------7-----------------------#2024-10-16)

## **探索DRESS Kit最新版本中的新特性和显著变化**

[](https://medium.com/@waihongchung?source=post_page---byline--75fa4cf74582--------------------------------)[![Waihong Chung](../Images/7e9f2bbb026abcf36e4430e920e61ee1.png)](https://medium.com/@waihongchung?source=post_page---byline--75fa4cf74582--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75fa4cf74582--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--75fa4cf74582--------------------------------) [Waihong Chung](https://medium.com/@waihongchung?source=post_page---byline--75fa4cf74582--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75fa4cf74582--------------------------------) ·12分钟阅读·2024年10月16日

--

![](../Images/c1c41836cf3a157090eb66b46f452e14.png)

图片来自[Google DeepMind](https://unsplash.com/@googledeepmind?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## **概述**

自从原始[DRESS Kit](https://github.com/waihongchung/dress)在2021年首次发布以来，它已经成功应用于若干生物医学研究项目。如果你之前从未听说过DRESS Kit，可能会对它感兴趣——它是一个完全开源、无依赖、纯ES6 JavaScript库，专为执行高级统计分析和机器学习任务而设计。DRESS Kit的目标是服务那些没有生物统计学训练、且无法使用专门统计软件的生物医学研究人员。

DRESS Kit不仅被证明是分析复杂数据集和构建机器学习模型的实用有效工具，而且这些现实世界的经验还为我们提供了宝贵的机会，帮助我们识别DRESS Kit潜在的改进领域。然而，为了支持某些新功能并实现显著的性能提升，原始代码库的许多部分必须从头开始重写。经过无数个不眠之夜和几杯咖啡后，我们终于准备好与大家分享——DRESS Kit ***V2***。

尽管DRESS Kit的新版本不再与之前的版本向后兼容，我们仍尽力保持方法签名（即方法名称和预期的参数）尽可能不变。这意味着，使用DRESS Kit V1实现的研究项目可以通过少量修改迁移到V2。然而，这也意味着许多功能增强可能仅通过浏览源代码并不容易发现。因此，我们将在本文中花些时间探讨DRESS Kit最新版本中的新特性和显著变化。

## **新特性**

**增量训练**

DRESS Kit V2中最令人兴奋的一个新特性是可以在任何回归或分类机器学习算法上执行增量训练。在DRESS Kit的先前版本中，只有kNN算法和多层感知机算法支持这一功能。此特性使得模型能够使用更大的数据集进行训练，同时以高效的资源方式运行，或者实时适应不断变化的数据源。

![](../Images/18a62a106f7946e89cea663324a415c7.png)

图片来自[Alessia Cocconi](https://unsplash.com/@alessia_cocconi?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

下面是使用随机森林算法实现增量训练的伪代码。

```py
// Create an empty model.
let model = DRESS.randomForst([], outcome, numericals, categoricals);
// Train the existing model using new samples. Repeat this step whenever a sufficient number of new training samples is accumulated. 
model.train(samples);
```

增量训练在不同的机器学习算法中有不同的实现方式。对于kNN算法，新的样本会被添加到现有的训练样本中，结果是模型会随着时间的推移而变大。对于逻辑回归或线性回归算法，现有的回归系数会使用新的训练样本进行更新。对于随机森林或梯度提升算法，现有的决策树或决策树的分支可以被修剪，并且可以根据新的训练样本添加新的树或新分支。对于多层感知机算法，神经网络的权重和偏置会随着新训练样本的加入而更新。

**模型调优**

DRESS Kit V2中的另一个令人兴奋的新特性是增加了`dress-modeling.js`模块，该模块包含了一些方法，用以简化机器学习模型微调这一繁琐过程。这些方法设计用于与使用`dress-regression.js`模块、`dress-tree.js`模块和`dress-neural.js`模块创建的任何回归或分类模型一起工作。由于所有这些任务都相当计算密集，因此这些方法默认是异步工作的。

+   **排列特征重要性**

    本模块中的第一种方法是`DRESS.importances`，它计算置换特征重要性。该方法通过随机置换一个特征的值，从而打破该特征与结果之间的关联，来估算每个特征对已训练模型的相对贡献。

```py
// Split a sample dataset into training/vadilation dataset
const [trainings, validations] = DRESS.split(samples);
// Create a model using a training dataset.
let model = DRESS.gradientBoosting(trainings, outcome, numericals, categoricals);
// Compute the permutation feature importances using a validation dataset.
DRESS.print(
 DRESS.importances(model, validations)
);
```

+   **交叉验证**

    本模块中的第二种方法是`DRESS.crossValidate`，它执行k折交叉验证。该方法自动将数据集分成k个（默认是5）大小相等的折，并在训练机器学习模型时，使用其中一个折作为验证集，其他k-1个折用于训练。它有助于更稳健地评估模型的性能。

```py
// Training parameters
const trainParams = [outcomes, features];
// Validation parameters
const validateParams = [0.5];
// Perform cross validation on sample dataset using the logistic regression algorithm. Note that the training parameters and validations parameters MUST be passed as arrays.
DRESS.print(
 DRESS.crossValidate(DRESS.logistic, samples, trainParams, validateParams)
);
```

+   **超参数优化**

    本模块中的第三种方法，可能也是最强大的方法，是`DRESS.hyperparameters`，它使用网格搜索方法并结合早停策略，执行自动超参数优化。它对任何数值型超参数进行优化，并使用`DRESS.crossValidate`方法内部评估模型性能。这个过程包含几个步骤。首先，需要指定超参数的初始值。任何没有明确指定的超参数将由机器学习算法设置为其默认值。其次，需要为每个正在优化的超参数指定搜索空间的结束值。指定这些超参数的顺序也决定了搜索的顺序，因此建议首先指定最相关的超参数。第三，需要选择一个性能度量指标（例如，分类任务使用`f1`，回归任务使用`r2`）来评估模型性能。以下是对多层感知机算法执行自动超参数优化的伪代码。

```py
// Specify the initial hyperparameter values. Hyperparameters that are not defined will be set to the default values by the multilayer perceptron algorithm itself.
const initial = {
 alpha: 0.001,
 epoch: 100,
 dilution: 0.1,
 layout: [20, 10]
}
// Specify the end values of the search space. Only hyperparameters that are being optimized are included.
const eventual = {
 dilution: 0.6, // the dilution hyperparameter will be searched first.
 epoch: 1000 // the epoch hyperparameter will be searched second.
 // the alpha hyperparameter will not be optimized.
 // the layout hyperparameter cannot be optimized since it is not strictly a numerical value.
}
// Specify the performace metric.
const metric = 'f1',
// Training parameters
const trainParams = [outcome, features];
DRESS.print(
 DRESS.hyperparameters(initial, eventual, metric, DRESS.multilayerPerceptron, samples, trainParams)
)
```

**模型导入与导出** 创建DRESS Kit的主要动机之一是使用纯JavaScript，而不是其他高性能语言，以确保跨平台兼容性并方便与其他技术的集成。因此，DRESS Kit V2现在包括了一些方法，以便于训练模型的分发。同时，模型的内部表示也已优化，以最大化其可移植性。

```py
// To export a model in JSON format.
DRESS.save(DRESS.deflate(model), 'model.json');
// To import a model from a JSON file.
DRESS.local('model.json').then(json => {
 const model = DRESS.inflate(json)
})
```

**数据集检查**

DRESS Kit V2最常被请求的功能之一是类似于Python中`pandas.DataFrame.info`的方法。因此，我们在`dress-descriptive.js`模块中发布了一种新方法`DRESS.summary`，用于从数据集中生成简洁的摘要。只需将对象数组作为参数传入，该方法会自动识别可枚举特征、数据类型（数值型或类别型），以及在这些对象中找到的`null`值的数量。

```py
// Print a concise summary of the specified dataset.
DRESS.print(
 DRESS.summary(samples)
);
```

**玩具数据集**

![](../Images/a9f7c1ab6f18fec05e873c4fd910ed56.png)

图片来源：[Rick Mason](https://unsplash.com/@egnaro?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最后但同样重要的是，DRESS Kit V2 配备了一个全新的玩具数据集，用于测试和学习各种统计方法和机器学习算法。这个玩具数据集包含了6000个合成样本，这些样本是基于一组患有各种慢性肝病的患者群体建模的。每个样本包含23个特征，这些特征包括数值型和分类特征的组合，且具有不同的基数。以下是每个样本的结构：

```py
{
 ID: number, // Unique identifier
 Etiology: string, // Etiology of liver disease (ASH, NASH, HCV, AIH, PBC)
 Grade: number, // Degree of steatotsis (1, 2, 3, 4)
 Stage: number, // Stage of fibrosis (1, 2, 3, 4)
 Admissions: number[], // List of numerical IDs representing hospital admissions
Demographics: {
 Age: number, // Age of subject
 Barriers: string[], // List of psychosocial barriers
 Ethnicity: string, // Ethnicity (white, latino, black, asian, other)
 Gender: string // M or F
 },
Exams: {
 BMI: number // Body mass index
 Ascites: string // Ascites on exam (none, small, large)
 Encephalopathy: string // West Haven encephalopathy grade (0, 1, 2, 3, 4)
 Varices: string // Varices on endoscopy (none, small, large)
 },
Labs: {
 WBC: number, // WBC count (1000/uL)
 Hemoglobin: number, // Hemoglobin (g/dL)
 MCV: number, // MCV (fL)
 Platelet: number, // Platelet count (1000/uL)
 AST: number, // AST (U/L)
 ALT: number, // ALT (U/L)
 ALP: number, // Alkaline Phosphatase (IU/L)
 Bilirubin: number, // Total bilirubin (mg/dL)
 INR: number // INR 
 }
}
```

这个精心设计的玩具数据集同时支持分类和回归任务。它的数据结构与真实患者数据非常相似，适合用来调试真实世界的工作流。以下是通过上述`DRESS.summary`方法生成的玩具数据集的简要总结。

```py
6000 row(s) 23 feature(s)
Admissions : categoric null: 4193 unique: 1806 [1274533, 631455, 969679, …]
Demographics.Age : numeric null: 0 unique: 51 [45, 48, 50, …]
Demographics.Barriers : categoric null: 3378 unique: 139 [insurance, substance use, mental health, …]
Demographics.Ethnicity: categoric null: 0 unique: 5 [white, latino, black, …]
Demographics.Gender : categoric null: 0 unique: 2 [M, F]
Etiology : categoric null: 0 unique: 5 [NASH, ASH, HCV, …]
Exams.Ascites : categoric null: 0 unique: 3 [large, small, none]
Exams.BMI : numeric null: 0 unique: 346 [33.8, 23, 31.3, …]
Exams.Encephalopathy : numeric null: 0 unique: 5 [1, 4, 0, …]
Exams.Varices : categoric null: 0 unique: 3 [none, large, small]
Grade : numeric null: 0 unique: 4 [2, 4, 1, …]
ID : numeric null: 0 unique: 6000 [1, 2, 3, …]
Labs.ALP : numeric null: 0 unique: 236 [120, 100, 93, …]
Labs.ALT : numeric null: 0 unique: 373 [31, 87, 86, …]
Labs.AST : numeric null: 0 unique: 370 [31, 166, 80, …]
Labs.Bilirubin : numeric null: 0 unique: 103 [1.5, 3.9, 2.6, …]
Labs.Hemoglobin : numeric null: 0 unique: 88 [14.9, 13.4, 11, …]
Labs.INR : numeric null: 0 unique: 175 [1, 2.72, 1.47, …]
Labs.MCV : numeric null: 0 unique: 395 [97.9, 91, 96.7, …]
Labs.Platelet : numeric null: 0 unique: 205 [268, 170, 183, …]
Labs.WBC : numeric null: 0 unique: 105 [7.3, 10.5, 5.5, …]
MELD : numeric null: 0 unique: 33 [17, 32, 21, …]
Stage : numeric null: 0 unique: 4 [3, 4, 2, …]
```

## 特征增强

**倾向与接近匹配**

`DRESS.propensity`方法，执行倾向评分匹配，现在支持将数值型和分类特征作为混杂变量。内部，该方法使用`DRESS.logistic`来估计倾向评分（如果只指定数值型特征）；否则，使用`DRESS.gradientBoosting`。我们还引入了一个新方法`DRESS.proximity`，该方法使用`DRESS.kNN`执行K最近邻匹配。

```py
// Split samples to controls and subjects.
const [controls, subjects] = DRESS.split(samples);
// If only numerical features are specified, then the method will build a logistic regression model.
let numerical_matches = DRESS.propensity(subjects, controls, numericals);
// If only categorical features (or both categorical and numberical features) are specified, then the method will build a gradient boosting regression model.
let categorical_matches = DRESS.propensity(subjects, controls, numericals, categoricals);
```

**分类与数值化**

`dress-transform.js`模块中的`DRESS.categorize`方法已经完全重写，并且现在的行为与以前大不相同，但更加直观。新的`DRESS.categorize`方法接受一个数值数组作为边界，并根据指定的边界将数值特征转换为分类特征。旧版的`DRESS.categorize`方法已被重命名为`DRESS.numericize`，该方法通过将特征值与一个有序的类别数组进行匹配，将分类特征转换为数值特征。

```py
// Define boundaries.
const boundaries = [3, 6, 9];
// Categorize any feature value less than 3 as 0, values between 3 and 6 as 1, values between 6 and 9 as 2, and values greater than 9 as 3.
DRESS.categorize(samples, [feature], boundaries);
// Define categories.
const categories = [A, [B, C], D];
// Numericize any feature value A to 0, B or C to 1, and D to 2\. 
DRESS.numericize(samples, [feature], categories);
```

**线性、逻辑回归与多项回归**

在DRESS Kit V1中，`DRESS.logistic`回归算法是通过牛顿法实现的，而`DRESS.linear`回归算法则使用矩阵方法。在DRESS Kit V2中，这两种回归算法都使用了相同的优化梯度下降回归方法，并且该方法支持学习率和岭回归（L2正则化）等超参数。我们还引入了一个新方法`DRESS.polytomous`，该方法内部使用`DRESS.logistic`，通过一对多的方法执行多类分类。

**精准率-召回率曲线**

`dress-roc.js`模块现在包含了一个方法`DRESS.pr`，用于基于一个或多个数值分类器生成[精准率-召回率曲线](https://en.wikipedia.org/wiki/Precision_and_recall)。这个方法的函数签名与`DRESS.roc`完全相同，可以直接替代后者使用。

```py
// Generate a receiver-operating characteristic (roc) curve.
let roc = DRESS.roc(samples, outcomes, classifiers);
// Generate a precision-recall (pr) curve.
let pr = DRESS.pr(samples, outcomes, classifiers);
```

## 重大变更

**JavaScript Promise**

DRESS Kit V2 使用 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) 完全处理所有异步操作。不再支持回调函数。最显著的是，向 `DRESS.local` 或 `DRESS.remote` 传递名为 `processJSON` 的自定义回调函数的编程模式（如 DRESS Kit V1 中的示例所示）不再有效。取而代之的是，推荐使用以下编程模式。

```py
DRESS.local('data.json').then(subjects => {
 // Do something with the subjects.
})
```

**kNN 模型**

对 `DRESS.kNN` 方法进行了几个破坏性更改。首先，模型的结果必须在训练阶段指定，而不是在预测阶段指定，类似于 DRESS 工具包中的其他机器学习模型，如 `DRESS.gradientBoosting`、`DRESS.multilayerPerceptron`。

kNN 填补功能已从 `DRESS.kNN` 方法返回的模型对象移至名为 `DRESS.nearestNeighbor` 的独立方法，该方法位于 `dress-imputation.js` 模块中，以更好地区分机器学习算法与其应用。

`importances` 参数已被移除，相关的特征重要性应作为超参数指定。

**模型性能**

用于评估/验证机器学习模型性能的方法已从 `model.performance` 更名为 `model.validate`，以提高语言的一致性（即所有方法名都是动词）。

**模块组织**

包含核心统计方法的模块已从 `dress-core.js` 更名为 `dress.js`，在以模块化方式使用 DRESS Kit V2 时，必须始终包含此模块。

包含基于决策树的机器学习算法（包括随机森林和梯度提升）的模块已从 `dress-ensemble.js` 更名为 `dress-tree.js`，以更好地描述底层学习算法。

加载和保存数据文件以及将文本输出打印到 HTML 文档的方法已从 `dress-utility.js` 移至 `dress-io.js`。同时，`DRESS.async` 方法已移至其独立模块 `DRESS-async.js`。

**默认布尔参数**

所有可选的布尔（true/false）参数都被分配了默认值 `false`，以保持语法的一致性。方法的默认行为经过精心设计，适合大多数常见用例。例如，kNN 机器学习模型的默认行为是使用加权 kNN 算法；因此，用于选择加权与非加权 kNN 算法的布尔参数已更名为 `unweighted`，并设置为默认值 `false`。

然而，由于这一变化，所有机器学习算法的默认行为已设置为生成回归模型，而非分类模型。

**已移除的方法**

以下方法已完全移除，因为它们被认为构建不当或冗余：

- `DRESS.effectMeasures` 来自 `dress-association.js` 模块。

- `DRESS.polynomial` 来自 `dress-regression.js` 模块。

- `DRESS.uuid` 来自 `dress-transform.js` 模块。

## 最后的说明

除了前面提到的主要新特性外，DRESS工具包中的几乎每个方法都进行了大量增强。大多数操作比以前明显更快，而压缩后的代码库几乎保持不变。如果您之前使用过 DRESS Kit V1，强烈建议升级到 V2。对于那些尚未将 DRESS Kit 纳入研究项目的人，现在是探索其功能的绝佳时机。我们非常感谢您对 DRESS Kit 的关注和持续支持。请随时分享您的反馈和评论，以便我们不断改进这个库。

请随时从其 [GitHub 仓库](https://github.com/waihongchung/dress) 获取 DRESS Kit 的最新版本并开始构建。
