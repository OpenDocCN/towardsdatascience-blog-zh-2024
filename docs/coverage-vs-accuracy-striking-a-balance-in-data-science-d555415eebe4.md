# 覆盖率与准确性：在数据科学中找到平衡

> 原文：[https://towardsdatascience.com/coverage-vs-accuracy-striking-a-balance-in-data-science-d555415eebe4?source=collection_archive---------7-----------------------#2024-04-16](https://towardsdatascience.com/coverage-vs-accuracy-striking-a-balance-in-data-science-d555415eebe4?source=collection_archive---------7-----------------------#2024-04-16)

## 快速获得成果的敏捷模型生产艺术

[](https://medium.com/@nadavgoo?source=post_page---byline--d555415eebe4--------------------------------)[![Nadav Har-Tuv](../Images/981fadd23cdfb60cfe0fa02dbb8edca6.png)](https://medium.com/@nadavgoo?source=post_page---byline--d555415eebe4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d555415eebe4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d555415eebe4--------------------------------) [Nadav Har-Tuv](https://medium.com/@nadavgoo?source=post_page---byline--d555415eebe4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d555415eebe4--------------------------------) ·阅读时间：7分钟·2024年4月16日

--

![](../Images/b5982740ce463719d4a444ab51fa163a.png)

封面图由chatGPT提供

本文由[Nadav Har-Tuv](https://medium.com/@nadavgoo?source=post_page---byline--d555415eebe4--------------------------------)与[Yuval Cohen](https://www.linkedin.com/in/yucohen/)共同撰写并获得启发

## 引言

每天都有大量的数据科学项目因预测精度不足而被舍弃。这是一个令人遗憾的结果，因为这些模型往往非常适合数据集中的某些子集。

数据科学家常常通过使用更复杂的模型和投入越来越多的数据来提升模型的表现。但很多时候，有一种更简单且更高效的方法：我们可以先在数据的简单部分做出准确的预测，只有在此基础上再去处理更难的部分。

这种方法可以大大提高我们解决现实问题的能力。我们从解决简单问题开始，迅速获得成果，然后再将精力集中在更难的问题上。

## 思考敏捷

敏捷生产意味着先关注简单的数据，只有在这些数据被适当建模之后，才转向更复杂的任务。这种方式支持一个迭代的、以价值为驱动的、协作的工作流程。

这种方法能够更快地获得结果，适应变化的环境，并持续改进，这些都是敏捷生产的核心理念。

1.  **迭代与增量方法：** 在短的迭代周期内工作。先在简单问题上取得高精度，然后再处理更难的部分。

1.  **专注于创造价值：** 解决对你的时间具有最高边际价值的问题。

1.  **灵活性与适应性：** 让自己适应变化的环境。例如，客户可能需要你专注于某个特定的数据子集——一旦你解决了那个小问题，环境可能发生变化，你可能需要完全转向一个不同的工作。将问题分解成小部分可以让你适应不断变化的环境。

1.  **反馈与持续改进：** 通过将问题分解，你可以保持持续不断的改进，而不是等待在大的块中取得重大进展。

1.  **协作：** 将问题分解成小部分有助于工作的并行化和团队成员之间的协作，而不是将所有工作集中到一个人身上。

## 解决复杂性问题

在真实世界的数据集中，复杂性往往是常态而非例外。以医学诊断任务为例，症状的微小变化可能决定是否为危及生命的疾病或只是轻微的不适。在这种情况下，由于数据中的噪音和细微差别，实现高精度可能非常具有挑战性，甚至是不可能的。

这时，**覆盖率**的概念就变得至关重要。覆盖率指的是模型能够高精度或高置信度成功预测或分类的数据部分。研究人员可以选择专注于那些预测相对简单的数据子集，而不是追求在整个数据集上实现高精度。通过这样做，他们可以在这个子集上实现高精度，同时承认数据中存在更加具有挑战性、尚未覆盖的部分。

举个例子，考虑一个在测试数据集上准确率为50%的训练模型。在这种情况下，如果我们能够识别并只选择我们非常确定的预测（虽然我们需要定义“非常确定”是什么意思），我们可能会得到一个覆盖较少案例的模型，假设只有60%的数据被覆盖，但其准确率可能会显著提高，达到85%。

**我不知道有哪个产品经理会在这种情况下说不，尤其是当没有生产中的模型时，这还是第一个模型。**

## 两步模型

我们希望将数据分成两个不同的子集：*已覆盖*和*未覆盖*。已覆盖数据是指初步模型能够实现高精度和高置信度的部分数据。未覆盖数据是指我们的模型无法给出有信心的预测，并且没有达到高精度的部分数据。

在第一步中，模型在数据上进行训练。一旦我们确定了一个模型在该数据子集上能够实现高精度，我们就可以部署该模型，并让其在这个子集上运行——即已覆盖的数据。

在第二步中，我们将注意力转向尚未覆盖的数据。我们通过收集更多数据、使用更先进的算法、进行特征工程以及结合领域知识来开发更好的模型，以便发现数据中的模式。

在这个步骤中，您首先应该做的是直观地查看错误。很多时候，您可以在不使用任何复杂技巧的情况下轻松识别出许多模式。

## 一个例子

这个例子将展示敏捷工作流的概念如何创造巨大价值。这是一个非常简单的例子，旨在形象地展示这一概念。现实生活中的例子可能不那么显而易见，但这里展示的理念同样具有相关性。

让我们来看一下我从三个大小相等的类别中模拟的这组二维数据。

```py
num_samples_A = 500
num_samples_B = 500
num_samples_C = 500

# Class A
mean_A = [3, 2]
cov_A = [[0.1, 0], [0, 0.1]]  # Low variance
class_A = np.random.multivariate_normal(mean_A, cov_A, num_samples_A)

# Class B
mean_B = [0, 0]
cov_B = [[1, 0.5], [0.5, 1]]  # Larger variance with some overlap with class C
class_B = np.random.multivariate_normal(mean_B, cov_B, num_samples_B)

# Class C
mean_C = [0, 1]
cov_C = [[2, 0.5], [0.5, 2]]  # Larger variance with some overlap with class B
class_C = np.random.multivariate_normal(mean_C, cov_C, num_samples_C)
```

![](../Images/3648a3956ec8fd8748307b3ea1554cf7.png)

来自三个类别的二维数据

现在我们尝试将一个机器学习分类器应用于这些数据，看起来使用高斯（‘rbf’）核的支持向量机（SVM）分类器可能能够解决问题：

```py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC

# Creating DataFrame
data = np.concatenate([class_A, class_B, class_C])
labels = np.concatenate([np.zeros(num_samples_A), np.ones(num_samples_B), np.ones(num_samples_C) * 2])
df = pd.DataFrame(data, columns=['x', 'y'])
df['label'] = labels.astype(int)

# Splitting data into train and test sets
X_train, X_test, y_train, y_test = train_test_split(df[['x', 'y']], df['label'], test_size=0.2, random_state=42)

# Training SVM model with RBF kernel
svm_rbf = SVC(kernel='rbf', probability= True)
svm_rbf.fit(X_train, y_train)

# Predict probabilities for each class
svm_rbf_probs = svm_rbf.predict_proba(X_test)

# Get predicted classes and corresponding confidences
svm_rbf_predictions = [(X_test.iloc[i]['x'], X_test.iloc[i]['y'], true_class, np.argmax(probs), np.max(probs)) for i, (true_class, probs) in enumerate(zip(y_test, svm_rbf_probs))]

svm_predictions_df = pd.DataFrame(svm_rbf_predictions).rename(columns={0:'x',1:'y' ,2: 'true_class', 3: 'predicted_class', 4: 'confidence'})
```

这个模型在我们的数据上表现如何？

```py
accuracy = (svm_predictions_df['true_class'] == svm_predictions_df['predicted_class']).mean()*100
print(f'Accuracy = {round(accuracy,2)}%')
```

精度 = 75.33%

75%的精度令人失望，但这是否意味着这个模型没用？

现在我们想看看最有信心的预测，并观察模型在这些预测上的表现。如何定义最有信心的预测？我们可以尝试不同的置信度（`predict_proba`）阈值，并查看每个阈值的覆盖率和精度，然后决定哪个阈值满足我们的业务需求。

```py
thresholds = [.5, .55, .6, .65, .7, .75, .8, .85, .9]
results = []

for threshold in thresholds:
    svm_df_covered = svm_predictions_df.loc[svm_predictions_df['confidence'] > threshold]
    coverage = len(svm_df_covered) / len(svm_predictions_df) * 100
    accuracy_covered = (svm_df_covered['true_class'] == svm_df_covered['predicted_class']).mean() * 100

    results.append({'Threshold': threshold, 'Coverage (%)': round(coverage,2), 'Accuracy on covered data (%)': round(accuracy_covered,2)})

results_df = pd.DataFrame(results)
print(results_df)
```

然后我们得到

![](../Images/bfe0f866be84c0e1aa19327441e52162.png)

按阈值划分的覆盖率和精度表

或者如果我们想更详细地查看，我们可以创建一个关于阈值的覆盖率和精度的图表：

![](../Images/e0e042bd92ac5bd5df2a02a1efa160a1.png)

精度和覆盖率与阈值的关系

我们现在可以选择适合我们业务逻辑的阈值。例如，如果我们公司的政策是保证至少90%的精度，那么我们可以选择0.75的阈值，并在62%的数据上获得90%的精度。这比完全抛弃模型要好得多，特别是如果我们没有任何在生产中的模型！

既然我们的模型在60%的数据上已经成功运行，我们可以将注意力转向其余的数据。我们可以收集更多数据，进行更多特征工程，尝试更复杂的模型，或者寻求领域专家的帮助。

## 权衡

这个两步模型使我们能够在追求精度的同时承认，首先只对部分数据达到高精度是完全可以接受的。在将模型部署到生产环境之前，强求模型在所有数据上的高精度是适得其反的。

本文介绍的敏捷方法旨在优化资源配置和提高效率。与其在所有数据上都花费计算资源以获得高精度，不如将资源集中在边际增益最高的地方。

## 结论

在数据科学中，我们尝试实现高准确度。然而，在杂乱数据的现实面前，我们需要找到一种聪明的方法，以最好的方式利用我们的资源。敏捷模型生产教我们专注于数据中模型最有效的部分，将模型部署在这些子集上，只有在此之后，才开始为更复杂的部分开发新模型。这一策略将帮助你在面对真实的数据科学问题时，最大限度地利用你的资源。

思考生产，思考敏捷。
