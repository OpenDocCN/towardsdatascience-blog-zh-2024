# 在生产管道中验证数据：TFX方式

> 原文：[https://towardsdatascience.com/validating-data-in-a-production-pipeline-the-tfx-way-9770311eb7ce?source=collection_archive---------2-----------------------#2024-06-22](https://towardsdatascience.com/validating-data-in-a-production-pipeline-the-tfx-way-9770311eb7ce?source=collection_archive---------2-----------------------#2024-06-22)

## 深入了解使用 TensorFlow 数据验证进行的数据验证

[](https://medium.com/@akila29?source=post_page---byline--9770311eb7ce--------------------------------)[![Akila Somasundaram](../Images/5f3c58de8057c9c7ef42f6f5729fb395.png)](https://medium.com/@akila29?source=post_page---byline--9770311eb7ce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9770311eb7ce--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9770311eb7ce--------------------------------) [Akila Somasundaram](https://medium.com/@akila29?source=post_page---byline--9770311eb7ce--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9770311eb7ce--------------------------------) ·阅读时间 9 分钟·2024年6月22日

--

想象一下。我们有一个完全功能的机器学习管道，并且它是完美的。所以我们决定将它推向生产环境。一切在生产环境中都很顺利，直到有一天，我们的管道输入数据生成组件发生了一个小小的变化，导致管道崩溃。糟糕！

![](../Images/d53ddb0f90e923a8b1b9f06d777cadb7.png)

图片来源：[Sarah Kilian](https://unsplash.com/@rojekilian?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

为什么会发生这种情况？

由于机器学习模型在很大程度上依赖于所使用的数据，请记住那句古老的格言，“垃圾进，垃圾出”。只要数据正确，管道就能正常工作，一旦数据有所变动，管道可能就会出问题。

输入到管道中的数据大多数是通过自动化系统生成的，因此在生成数据的类型上控制较少。

那么，我们该怎么做呢？

数据验证是解决方案。

数据验证是一个监护系统，能够验证数据是否以合适的格式供管道使用。

阅读本文，了解为什么在机器学习管道中验证至关重要，以及机器学习验证的5个阶段。

[](/the-5-stages-of-machine-learning-validation-162193f8e5db?source=post_page-----9770311eb7ce--------------------------------) [## 机器学习验证的5个阶段

### 确保在整个机器学习生命周期中都能提供高质量的机器学习

towardsdatascience.com](/the-5-stages-of-machine-learning-validation-162193f8e5db?source=post_page-----9770311eb7ce--------------------------------)

# TensorFlow 数据验证

TensorFlow 数据验证（TFDV）是 TFX 生态系统的一部分，可以用于在机器学习管道中验证数据。

TFDV 通过比较训练数据和服务数据来计算描述性统计、模式并识别异常。这确保了训练数据和服务数据的一致性，避免在管道中出现破坏性问题或产生意外预测。

谷歌的团队希望 TFDV 从机器学习流程的最早阶段就能被使用。因此，他们确保 TFDV 可以与笔记本一起使用。我们将在这里做同样的事。

首先，我们需要使用 pip 安装 tensorflow-data-validation 库。最好创建一个虚拟环境并开始安装。

> **注意事项**：在安装之前，确保 TFX 库的版本兼容性

```py
pip install tensorflow-data-validation
```

以下是我们将在数据验证过程中遵循的步骤：

1.  从训练数据生成统计信息

1.  从训练数据推断模式

1.  生成评估数据的统计信息并与训练数据进行比较

1.  识别和修复异常

1.  检查漂移和数据偏斜

1.  保存模式

我们将在这里使用 3 种类型的数据集；训练数据、评估数据和服务数据，模拟实时使用。机器学习模型使用训练数据进行训练。评估数据，也就是测试数据，是在训练阶段完成后，用来测试模型的一部分数据。服务数据则在生产环境中提供给模型进行预测。

本文讨论的完整代码可以在我的 GitHub 仓库中找到。你可以通过 [这里](https://github.com/akila29/TF_Transform_Demo) 下载。

# 步骤 0：准备工作

我们将使用来自 Kaggle 的 spaceship titanic 数据集。你可以通过这个 [link](https://www.kaggle.com/competitions/spaceship-titanic) 了解更多并下载数据集。

![](../Images/ba359827d9aca475e8ef604afc070ae1.png)

Spaceship Titanic 数据集的示例视图

数据由数值数据和类别数据的混合组成。它是一个分类数据集，类别标签是 `Transported`，其值为 True 或 False。

![](../Images/9b5b6032c887d15e46860375ccd13c44.png)

数据描述

必要的导入已完成，csv 文件的路径已定义。实际数据集包含训练数据和测试数据。我手动引入了一些错误，并将文件保存为‘titanic_test_anomalies.csv’（此文件在 Kaggle 上不可用。你可以从我的 GitHub 仓库 [link](https://github.com/akila29/TF_Transform_Demo) 下载）。

在这里，我们将使用 ANOMALOUS_DATA 作为评估数据，使用 TEST_DATA 作为服务数据。

```py
import tensorflow_data_validation as tfdv
import tensorflow as tf

TRAIN_DATA = '/data/titanic_train.csv'
TEST_DATA = '/data/titanic_test.csv'
ANOMALOUS_DATA = '/data/titanic_test_anomalies.csv'
```

# 步骤 1：从训练数据生成统计信息

第一步是分析训练数据并识别其统计属性。TFDV 具有 `generate_statistics_from_csv` 函数，可以直接从 csv 文件读取数据。如果你有 `TFRecord` 格式的数据，TFDV 还提供了 `generate_statistics_from_tfrecord` 函数。

`visualize_statistics`函数展示了一个8点总结，以及一些有助于我们理解数据底层统计的图表。这被称为“Facets视图”。一些需要我们注意的关键细节以红色标出。这里还提供了许多其他用于分析数据的功能。可以多加尝试，进一步了解它。

```py
# Generate statistics for training data
train_stats=tfdv.generate_statistics_from_csv(TRAIN_DATA)
tfdv.visualize_statistics(train_stats)
```

![](../Images/083119251429614e2406c036aeabbae3.png)

为数据集生成的统计信息

在这里，我们看到**Age**和**RoomService**特征中有缺失值，需要进行填充。我们还看到**RoomService**中有65.52%的值为零。这是该数据的分布方式，因此我们不认为这是异常，我们将继续处理。

# 第2步：从训练数据中推断模式

一旦所有问题都得到满意解决，我们将使用`infer_schema`函数推断模式。

```py
schema=tfdv.infer_schema(statistics=train_stats)
tfdv.display_schema(schema=schema)
```

模式通常分为两个部分。第一部分呈现数据类型、存在性、有效性及其领域等详细信息。第二部分呈现该领域所包含的值。

![](../Images/9ab5c3d72692c0ed60ed099f0200770e.png)

第一部分：特征的详细信息

![](../Images/efd9ef3bd5d36330c2bf8bb9b17010ea.png)

第二部分：领域值

这是初始的原始模式，我们将在后续步骤中对其进行完善。

# 第3步：生成评估数据的统计信息并与训练数据进行比较

现在我们选择评估数据并生成统计信息。我们需要了解如何处理异常，因此我们将使用**ANOMALOUS_DATA**作为我们的评估数据。我们已经手动将异常引入到此数据中。

生成统计信息后，我们对数据进行可视化。可视化可以仅应用于评估数据（就像我们对训练数据做的那样），但将评估数据的统计信息与训练数据的统计信息进行比较更有意义。这样我们可以了解评估数据与训练数据之间的差异。

```py
# Generate statistics for evaluation data

eval_stats=tfdv.generate_statistics_from_csv(ANOMALOUS_DATA)

tfdv.visualize_statistics(lhs_statistics = train_stats, rhs_statistics = eval_stats,
                          lhs_name = "Training Data", rhs_name = "Evaluation Data")
```

![](../Images/6ac2d32e901e679439959c38c0ca4dbf.png)

训练数据和评估数据统计信息的比较

在这里，我们可以看到评估数据中缺少**RoomService**特征（大红旗）。其他特征看起来相当正常，因为它们展示的分布与训练数据相似。

然而，单靠人工观察在生产环境中是不够的，所以我们将要求TFDV实际分析并报告是否一切正常。

# 第4步：识别并修复异常

我们的下一步是验证从评估数据中获得的统计信息。我们将其与我们用训练数据生成的模式进行比较。`display_anomalies`函数将为我们提供TFDV已识别的异常的表格视图，并附有说明。

```py
# Identifying Anomalies
anomalies=tfdv.validate_statistics(statistics=eval_stats, schema=schema)
tfdv.display_anomalies(anomalies)
```

![](../Images/b76570fc516d2e98687a33433a4ab109.png)

TFDV提供的异常列表

从表格中，我们可以看到评估数据缺少2列（Transported和RoomService），Destination特征的领域中有一个额外的值“Anomaly”（在训练数据中没有出现），CryoSleep和VIP特征有“TRUE”和“FALSE”值，这些值在训练数据中没有，最后，5个特征包含整数值，而模式期望的是浮动点值。

这可真是个挑战。让我们开始工作吧。

修复异常有两种方法；要么手动处理评估数据，以确保它符合模式，要么修改模式以确保这些异常被接受。再次，领域专家必须决定哪些异常是可以接受的，哪些异常需要数据处理。

让我们从“Destination”特征开始。我们发现了一个新值“Anomaly”，它在训练数据的领域列表中缺失。让我们将其添加到领域中，并声明它也是该特征的一个可接受值。

```py
# Adding a new value for 'Destination'
destination_domain=tfdv.get_domain(schema, 'Destination')
destination_domain.value.append('Anomaly')

anomalies=tfdv.validate_statistics(statistics=eval_stats, schema=schema)
tfdv.display_anomalies(anomalies)
```

我们已经解决了这个异常，异常列表中不再显示它。让我们进入下一个。

![](../Images/5af7cb267e286bd12f213f7627219f4d.png)

Destination异常已解决

查看VIP和CryoSleep领域，我们发现训练数据使用的是小写值，而评估数据则使用相同的值但为大写。一个解决方案是预处理数据，并确保所有数据都转换为小写或大写。然而，我们决定将这些值添加到领域中。由于VIP和CryoSleep使用相同的值集（true和false），我们将CryoSleep的领域设置为使用VIP的领域。

```py
# Adding data in CAPS to domain for VIP and CryoSleep

vip_domain=tfdv.get_domain(schema, 'VIP')
vip_domain.value.extend(['TRUE','FALSE'])

# Setting domain of one feature to another
tfdv.set_domain(schema, 'CryoSleep', vip_domain)

anomalies=tfdv.validate_statistics(statistics=eval_stats, schema=schema)
tfdv.display_anomalies(anomalies)
```

![](../Images/dd243f90d74ec3b97e25f251f7709010.png)

已解决来自CryoSleep和VIP的异常

将整数特征转换为浮动点特征是相当安全的。因此，我们要求评估数据根据训练数据的模式推断数据类型。这解决了与数据类型相关的问题。

```py
# INT can be safely converted to FLOAT. So we can safely ignore it and ask TFDV to use schema

options = tfdv.StatsOptions(schema=schema, infer_type_from_schema=True)
eval_stats=tfdv.generate_statistics_from_csv(ANOMALOUS_DATA, stats_options=options)

anomalies=tfdv.validate_statistics(statistics=eval_stats, schema=schema)
tfdv.display_anomalies(anomalies)
```

![](../Images/92c72092ec74cac90fd4ad70c57f5ec0.png)

已解决数据类型问题

最后，我们遇到了最后一组异常：训练数据中存在的2列在评估数据中缺失。

“Transported”是类别标签，显然在评估数据中无法使用。为了处理训练数据和评估数据特征可能不同的情况，我们可以创建多个环境。在这里，我们创建了一个训练环境和一个服务环境。我们指定“Transported”特征将在训练环境中可用，但在服务环境中不可用。

```py
# Transported is the class label and will not be available in Evaluation data.
# To indicate that we set two environments; Training and Serving

schema.default_environment.append('Training')
schema.default_environment.append('Serving')

tfdv.get_feature(schema, 'Transported').not_in_environment.append('Serving')

serving_anomalies_with_environment=tfdv.validate_statistics(
    statistics=eval_stats, schema=schema, environment='Serving')

tfdv.display_anomalies(serving_anomalies_with_environment)
```

“RoomService”是一个必需的特征，但在服务环境中不可用。此类情况需要领域专家的手动干预。

继续解决问题，直到你得到这个输出。

![](../Images/6d1f86bc09f29744bb1e7e7ab96d015c.png)

所有异常已解决

所有异常已经解决

# 第5步：训练-服务漂移和偏差检测

下一步是检查漂移和偏差。偏差是由于数据分布的不规则性而发生的。最初，当模型训练完成时，它的预测通常是完美的。然而，随着时间的推移，数据分布发生变化，误分类错误开始增加，这就是漂移。这些问题需要重新训练模型。

L-infinity距离用于衡量偏差和漂移。基于L-infinity距离设置一个阈值。如果在训练和服务环境中分析的特征之间的差异超过给定的阈值，则认为该特征经历了漂移。偏差的判断也遵循类似的阈值方法。对于我们的示例，我们已将漂移和偏差的阈值都设置为0.01。

```py
serving_stats = tfdv.generate_statistics_from_csv(TEST_DATA)

# Skew Comparator
spa_analyze=tfdv.get_feature(schema, 'Spa')
spa_analyze.skew_comparator.infinity_norm.threshold=0.01

# Drift Comparator
CryoSleep_analyze=tfdv.get_feature(schema, 'CryoSleep')
CryoSleep_analyze.drift_comparator.infinity_norm.threshold=0.01

skew_anomalies=tfdv.validate_statistics(statistics=train_stats, schema=schema,
                                        previous_statistics=eval_stats,
                                        serving_statistics=serving_stats)
tfdv.display_anomalies(skew_anomalies)
```

我们可以看到‘Spa’的偏差水平是可以接受的（因为它没有列在异常列表中），然而，‘CryoSleep’表现出较高的漂移水平。在创建自动化管道时，这些异常可以用作触发自动模型重新训练的条件。

![](../Images/74229d1ba539bc27649b3fd74c0a075d.png)

CryoSleep中的高偏差

# 步骤6：保存架构

在解决所有异常后，架构可以作为工件保存，或者可以保存在元数据存储库中，并在机器学习管道中使用。

```py
# Saving the Schema
from tensorflow.python.lib.io import file_io
from google.protobuf import text_format

file_io.recursive_create_dir('schema')
schema_file = os.path.join('schema', 'schema.pbtxt')
tfdv.write_schema_text(schema, schema_file)
```

```py
# Loading the Schema
loaded_schema= tfdv.load_schema_text(schema_file)
loaded_schema
```

你可以通过以下[链接](https://github.com/akila29/TF_Transform_Demo)从我的GitHub仓库下载笔记本和数据文件。

# 其他值得关注的选项

你可以阅读以下文章，了解你的选择，并学习如何为你的机器学习管道项目选择合适的框架。

[](https://eitca.org/artificial-intelligence/eitc-ai-gcml-google-cloud-machine-learning/google-cloud-ai-platform/setting-up-ai-platform-pipelines/examination-review-setting-up-ai-platform-pipelines/what-are-the-advantages-and-differences-between-tfx-sdk-and-kubeflow-pipelines-sdk-and-how-should-you-choose-between-them-when-creating-your-own-pipeline/?source=post_page-----9770311eb7ce--------------------------------) [## TFX SDK和Kubeflow Pipelines SDK之间的优势和差异是什么？在创建自己的管道时，应该如何选择它们？

### TFX SDK（TensorFlow Extended软件开发工具包）和Kubeflow Pipelines SDK是两个强大的工具，可以...

eitca.org](https://eitca.org/artificial-intelligence/eitc-ai-gcml-google-cloud-machine-learning/google-cloud-ai-platform/setting-up-ai-platform-pipelines/examination-review-setting-up-ai-platform-pipelines/what-are-the-advantages-and-differences-between-tfx-sdk-and-kubeflow-pipelines-sdk-and-how-should-you-choose-between-them-when-creating-your-own-pipeline/?source=post_page-----9770311eb7ce--------------------------------) [](https://www.restack.io/docs/mlflow-knowledge-mlflow-vs-tensorflow-extended?source=post_page-----9770311eb7ce--------------------------------) [## MLflow与TensorFlow Extended的对比

### 探索MLflow与TensorFlow Extended在管理机器学习工作流方面的差异。

[www.restack.io](https://www.restack.io/docs/mlflow-knowledge-mlflow-vs-tensorflow-extended?source=post_page-----9770311eb7ce--------------------------------)

感谢阅读我的文章。如果你喜欢它，请给我一点掌声作为鼓励；如果你有不同的意见，请在评论中告诉我有哪些可以改进的地方。再见。

除非另有说明，所有图片均由作者提供。
