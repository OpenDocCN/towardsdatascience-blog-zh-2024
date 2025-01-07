# 类别编码器的基准与分类

> 原文：[https://towardsdatascience.com/a-benchmark-and-taxonomy-of-categorical-encoders-9b7a0dc47a8c?source=collection_archive---------6-----------------------#2024-03-29](https://towardsdatascience.com/a-benchmark-and-taxonomy-of-categorical-encoders-9b7a0dc47a8c?source=collection_archive---------6-----------------------#2024-03-29)

## 新的。全面的。可扩展的。

[](https://medium.com/@vadim.arzamasov?source=post_page---byline--9b7a0dc47a8c--------------------------------)[![Vadim Arzamasov](../Images/70ced2eafa6fc926052979875a0a4265.png)](https://medium.com/@vadim.arzamasov?source=post_page---byline--9b7a0dc47a8c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9b7a0dc47a8c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9b7a0dc47a8c--------------------------------) [Vadim Arzamasov](https://medium.com/@vadim.arzamasov?source=post_page---byline--9b7a0dc47a8c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b7a0dc47a8c--------------------------------) ·12分钟阅读·2024年3月29日

--

![](../Images/1468cfa0cf9668807f9c2fa1088108b2.png)

图片由作者使用 recraft.ai 创建

许多数据集包含类别特征。例如，在[UC Irvine机器学习库](https://archive.ics.uci.edu/) [1]中的665个数据集中，有42个是完全类别的，366个被报告为混合类型。然而，基于距离的机器学习模型和几乎所有[scikit-learn实现](https://scikit-learn.org/stable/modules/preprocessing.html#encoding-categorical-features)都要求特征采用数值格式。类别编码器通过将这些特征中的类别替换为实数来实现这一点。

存在多种类别编码器，但很少有尝试在多个数据集上、使用各种机器学习模型、以及在不同的管道中进行比较。本文介绍了我们[近期发表的论文](https://proceedings.neurips.cc/paper_files/paper/2023/hash/ac01e21bb14609416760f790dd8966ae-Abstract-Datasets_and_Benchmarks.html) [2]（[海报](https://nips.cc/media/PosterPDFs/NeurIPS%202023/73555.png?t=1699521284.38544)，[GitHub上的代码](https://github.com/DrCohomology/EncoderBenchmarking)）中的一个最新的编码器基准。在本文中，我将重点介绍补充出版物的内容，并且这些内容在实际中具有重要意义。特别地，除了我们基准结果的总结外，我还将：

+   提供55个类别编码器的列表，并附上大多数编码器的解释和实现链接。

+   解释你也可以使用我们的代码作为对`[Category Encoders](https://contrib.scikit-learn.org/category_encoders/)` Python模块的补充，来处理那些尚未在该模块中实现的编码器。

+   将编码器归类为不同的家族，这样你就不必记住每个单独的编码器，而是能够了解如何构建每个家族中的成员。

+   解释如何利用[2]中的代码和详细的基准数据，将你的编码器、数据集或机器学习模型纳入比较中，而无需重新运行现有的实验。根据你实验的范围和计算资源，这可以节省你几周的计算时间。

## **为什么要做另一个基准测试？**

已经有几项科学研究比较了分类编码器[3–12]，并且至少有一个分类编码器的[基准测试在TDS上](/benchmarking-categorical-encoders-9c322bd77ee8)[13]。我将在这里展示的研究与其他研究的主要区别在于范围：我们比较了来自不同编码器家族的代表性编码器，并在多种配置下进行了测试。我们实验了**5**个机器学习模型（决策树、kNN、SVM、逻辑回归、LGBM），**4**个质量评估指标（AUC、准确率、平衡准确率、F1分数），**3**个调优策略（我会稍后介绍），**50**个数据集，和**32**个编码器配置。

下表展示了我们基准测试中涵盖的编码器（深绿色列）以及其他实验比较中涉及的编码器（浅绿色列）。蓝色列展示了在另外两个来源中描述的编码器：一篇专门介绍对比编码器的文章[15]和Medium上的文章[14]：

[](https://medium.com/@vadim.arzamasov/navigating-categorical-encoder-maze-c04e49b165fe?source=post_page-----9b7a0dc47a8c--------------------------------) [## 分类编码：关键洞察

### 总结54篇Medium故事

medium.com](https://medium.com/@vadim.arzamasov/navigating-categorical-encoder-maze-c04e49b165fe?source=post_page-----9b7a0dc47a8c--------------------------------)

最后一列黄色列展示了`[Category Encoders](https://contrib.scikit-learn.org/category_encoders/)`模块[16]涵盖的编码器。需要注意的是，[2]中的代码实现了一些编码器——来自相似性、分箱和数据约束家族——这些编码器并不属于`Category Encoders`模块。此外，我们发现用R实现的GLMM编码器，其速度比`Category Encoders`中的GLMM编码器要快。因此，对于这些实现，你可能会发现我们的代码更有用。

![](../Images/d08fed9022b8930e1708be3269f919e3.png)

表1\. 编码器家族及其在各种资源中的覆盖情况。作者拥有版权

表1已经包含了多个编码器，但这个列表并非详尽无遗。为了更好地理解编码器的全貌，分类编码器是非常有用的，这有助于理解编码器设计的原理，而不是仅仅记住大量的单独编码器。

## 编码器家族

接下来，我们考虑一个具有`n`个长度和`k`个基数的分类特征。从高层次来看，分类编码器可以分为监督式或无监督式。

**1\. 无监督编码器** 在编码过程中不包括目标变量。

***1.1\. 标识符编码器*** 使用单射函数转换分类变量，即它们为每个类别分配一个唯一的数值或唯一的数值组合。它们会创建从 1 到 `k` 个新的特征。例如，独热编码器创建 `k` 个特征，标签编码器或有序编码器创建一个新特征，Base N 编码器创建 ⌈`log(k)`⌉ 个新特征，其中对数以 N 为底。

这些编码器对于表示唯一标识符的分类变量非常有用，例如产品代码或邮政编码。通常，除了有序编码器外，标识符编码器并不假设类别之间存在任何内在顺序，也不认为该顺序对分析有意义，因此在编码过程中忽略任何此类顺序。

***1.2\. 对比编码器*** 通过基于类别之间的比较分配数值来转换分类变量。通常，一组 `k-1` 个新特征代表一个具有 `k` 类别的分类变量。与标识符编码器不同，这些编码器专门设计用于探索回归分析中分类变量不同水平之间的关系。

要创建对比编码器，可以选择不同的 [编码方案](https://stats.oarc.ucla.edu/r/library/r-library-contrast-coding-systems-for-categorical-variables/#ORTHOGONAL) [15]。每种编码方式以特定方式将一个类别与其他类别对比，用以检验与数据相关的假设。例如，Helmert 编码将每个水平与随后的水平均值对比，而求和编码将每个水平与总体均值对比。

***1.3\. 频率编码器*** 用与数据集中类别出现频率相关的值替换分类变量的类别。与标识符编码器或对比编码器不同，频率编码器只创建一个新的特征，并不一定是单射函数。它们提供了类别出现频率的数值表示，这在机器学习模型需要理解类别值的普遍性时尤为有用。表 1 中的所有三种频率编码器都是频率的单调递增函数。然而，这并不是定义这一组编码器的必要条件。

***1.4\. 相似性编码器*** [5, 8, 18] 通过应用捕捉不同类别之间相似性或距离的度量，将分类数据转换为数值形式。

一类相似度编码器[8, 18]基于将两个类别视为字符串进行形态学比较。相似度度量的示例包括Levenshtein比率、Jaro-Winkler相似度或N-gram相似度。然后，类别变量被编码为一个向量，其中每个维度对应于参考类别与所有类别的成对比较，值表示计算出的相似度得分（类似于构造[方差-协方差](https://en.wikipedia.org/wiki/Covariance_matrix)矩阵）。这一组的编码器通常会创建`k`个新特征。这种编码特别适用于处理“脏”类别数据集，这些数据集可能包含拼写错误和冗余[18]。可以将One-Hot编码视为相似度编码的一种特例，其中相似度度量只能取两个值：0或1。

另一类相似度编码器[5]，包括如嵌入（Embeddings）、最小哈希（Min-Hash）或伽马-泊松矩阵分解（Gamma-Poisson matrix factorization），设计用于高基数类别（`k>>1`）。它们将类别特征投射到低维空间。因此，这一组与分箱编码器相似，分箱编码器同样在处理大`k`时特别有用，但不旨在保留类别的形态相似性。

**2. 有监督编码器** 使用目标变量的信息。对于回归或二分类任务，它们通常为每个类别变量创建一个新的特征。

***2.1. 简单目标编码器*** 捕捉类别特征与目标变量之间的关系。构建简单目标编码器涉及计算每个类别水平上基于目标变量的统计量。常见的统计量包括目标变量的均值、中位数或按每个类别条件的概率比。

该过程如下：

+   对每个类别水平，按类别变量对数据进行分组。

+   在每个组内，计算所需的统计量。

+   对数据中的每个实例，用相应的统计量替换原始类别值。

简单目标编码存在过拟合的风险，尤其是在数据集较小或类别观察值非常少的情况下。诸如平滑（正则化）和交叉验证等技术（我们稍后会介绍）可以帮助缓解这一风险。

***2.2. 平滑编码器*** 是简单目标编码器的推广，它引入了一个平滑参数。该平滑参数的目的是防止过拟合，并提高编码器对新数据的泛化能力，特别是在类别观察值较少时。平滑值的常见公式为：

![](../Images/7cd5d3947ecd4f59103ed2ed6c68d6ee.png)

其中，`m`是数据集中类别出现的次数。它可能会略有变化，见[13]。通过调整平滑参数，您可以控制类别统计量和整体统计量之间的平衡。较大的平滑参数使得编码对类别特定的目标统计量不那么敏感。将上述公式中的平滑参数设置为零将产生简单的目标编码。

***2.3. 数据约束编码器***使用数据集子集中的信息。数据约束编码器的一个例子是留一法编码器，它对于给定的分类值，计算类别出现的所有其他实例中的目标变量统计量，但不包括当前实例。这种方法确保给定数据集的编码值不包括其自身的目标值。

数据约束策略有助于创建更能代表未见数据中真实关系的编码，并且不易过拟合。编码列中的唯一值数量可能会超过原始分类列的基数`k`。还可以在数据约束编码中引入平滑。

**3. 分箱编码器**可以是有监督的也可以是无监督的。通常，它们的工作可以分为两步。第一步通过将原始类别排序到箱中来创建一个辅助的分类特征。第二步对这个辅助特征应用来自上述其他组中的一个编码器。两步可以独立地是有监督的或无监督的。例如，在第一步中，您可以通过将稀有类别分组在一起形成箱（无监督的，例如，One-Hot MC编码器）；或者，您可以应用简单的目标编码器，将具有相似编码值的类别分组在一起（有监督的，例如，离散化目标编码器）。

一些分箱编码器，例如Hashing，不具有这种两阶段结构。分箱编码器创建的新特征的数量通常是`<k`。

## 调优策略

在继续讨论结果之前，我将简要总结我们基准测试中的调优策略。这对于理解以下图表的范围以及重用我们基准测试中的数据和代码非常重要。三种调优策略如下：

**无调优：**

+   对训练数据中的分类列进行编码；

+   使用默认超参数训练机器学习模型。

**模型调优：**

+   对训练数据中的分类列进行编码；

+   将训练数据拆分为折叠；

+   使用交叉验证优化机器学习模型的超参数。

**完全调优：**

+   将训练数据拆分为折叠；

+   分别对每个训练折叠进行编码（如果数据限制了编码器家族，每个折叠将进一步拆分为嵌套折叠）；

+   使用交叉验证优化机器学习模型的超参数。

## **结果**

我将列出我们实验中获胜的编码器，指出哪种调优策略表现最佳并应成为你机器学习流程的一部分，同时展示各个编码器的运行时间。

**排名**

![](../Images/4b1bf883b2332d852d6578c9e105c2b3.png)

图 1\. 所有实验中编码器的性能。作者拥有版权

在图 1 中，箱线图展示了编码器在所有数据集、质量指标和调优策略下的排名分布。也就是说，每个箱线图包含大约 50×4×3=600 个单独模型的实验，以及大约 50×4×3×5=3000 个所有模型的实验，排除了因超时或其他原因失败的少数实验。我们观察到，四个编码器：**独热编码、二进制编码（图中标为‘Bin’）、求和编码和证据权重编码**，在所有实验中始终表现最佳。对于逻辑回归，这四个编码器与其他编码器的差异在统计上是显著的。这个结果有些令人惊讶，因为许多先前的研究（见 [13, 14]）报告了无监督编码器的缺点，特别是独热编码。在图 1 中，其他编码器缩写的具体含义对接下来的内容并不重要，但你可以在 [2] 中找到这些缩写的定义。

**调优策略**

![](../Images/6b6e69a811937e2cbecd10bed418b70e.png)

图 2\. **完全**调优与**不调优**策略的性能提升。作者拥有版权

![](../Images/dd90fae03433f49d0da8b9dcbc423385.png)

图 3\. **完全**调优与**模型**调优的性能提升。作者拥有版权

图 2 显示了完全调优与不调优策略之间的性能差异（如我上面所描述），针对每个类别编码器。每个图表都涵盖了不同数据集、机器学习模型和性能指标的实验。图 3 是一个类似的图表，比较了完全调优与模型调优策略。请注意，这些图表未包括一些机器学习模型和数据集，因为我们限制了计算复杂度。有关详细信息和更多图表，请参见 [2]。

基于这些结果，我一般建议坚持采用完全调优策略，即在优化机器学习模型的超参数时，分别为每个折叠单独编码数据；对于数据约束编码器来说，这一点尤其重要。

**运行时间**

![](../Images/1f192ae7dee9e53c1a7f3aa17f717e95.png)

图 4\. 编码器的运行时间。作者拥有版权

最后，图 4 绘制了数据集编码所需时间的对数坐标图。简单的目标和分箱编码器是最快的，而平滑和数据约束编码器最慢。表现最好的四个编码器——独热编码、二进制编码（图中标为‘Bin’）、求和编码和证据权重——在处理数据时所需时间合理。

## 重用代码和结果

我们基准测试的一个优点是你可以轻松扩展它来测试你自己的分类编码方法，并将其置于现有实验结果的背景中。例如，你可能想测试表1中不在我们基准测试中的编码器，或者你可能想测试自定义的复合编码器，例如 `n/k>a` 时使用 One-Hot MC，否则使用 One-Hot，其中 `k` 是特征基数，`n` 是数据集的大小，像之前一样。我们在[GitHub](https://github.com/DrCohomology/EncoderBenchmarking)上解释了这个过程，并在[Kaggle笔记本](https://www.kaggle.com/code/derhamcohomology/add-a-custom-encoder-to-the-benchmark) [17]中展示了如何实现。

为了便于说明，我们将示例限制在未调参的逻辑回归模型中。我们基准测试的相应结果如下图5所示。

![](../Images/d9bc9a5c5f412a80e934529375160241.png)

图5. 逻辑回归下无调参策略的编码器排名。作者拥有版权。

现在假设[**Yury Kashnitsky**](https://medium.com/@yurykashnitskiy)（他友善地对我们的论文提供了反馈）想知道[哈希](https://www.kaggle.com/code/kashnitsky/vowpal-wabbit-tutorial-blazingly-fast-learning)编码器是否与我们评估的编码器具有竞争力。在[17]中，我们展示了如何进行缺失的实验，并发现[哈希](https://www.kaggle.com/code/kashnitsky/vowpal-wabbit-tutorial-blazingly-fast-learning)编码器在我们选择的超参数下表现合理，如图6所示。

![](../Images/639099717759d1f5005308102064a25d.png)

图6. 添加哈希技巧编码器后的编码器排名。作者拥有版权。

## 结论

我总结了我们的分类编码器基准测试，并解释了你如何从我们共享的文档中受益。你可能学到了：

+   分类编码器，作为机器学习模型，可以是监督式的或无监督式的。

+   我介绍了八种编码器类别。某些编码器保持分类特征的基数，其他编码器则能减少基数（通常是分箱的情况，但也适用于其他一些编码器，如朴素目标编码）或增加基数（数据约束）。

+   在我们的实验中，One-Hot、Binary、Sum 和 Weight of Evidence 编码器的平均表现最好，特别是在逻辑回归模型下。

+   请参见[Kaggle笔记本](https://www.kaggle.com/code/derhamcohomology/add-a-custom-encoder-to-the-benchmark) [17]，其中展示了如何将所需的编码器添加到基准测试并绘制结果；必要的代码可以在[GitHub](https://github.com/DrCohomology/EncoderBenchmarking)上找到。

## 致谢

如果我的同事和[2]的第一作者，[Federico Matteucci](https://github.com/DrCohomology)，没有编写基准测试和Kaggle笔记本[17]的代码，这个故事就不会存在。

## 资源

[1] [加州大学欧文分校机器学习数据集](https://archive.ics.uci.edu/)

[2] Matteucci, F., Arzamasov, V., & Böhm, K. (2024). 分类编码器的基准测试，用于二分类。*神经信息处理系统进展*，*36*。（[**论文**](https://proceedings.neurips.cc/paper_files/paper/2023/hash/ac01e21bb14609416760f790dd8966ae-Abstract-Datasets_and_Benchmarks.html)**,** [**GitHub上的代码**](https://github.com/DrCohomology/EncoderBenchmarking)**,** [**海报**](https://nips.cc/media/PosterPDFs/NeurIPS%202023/73555.png?t=1699521284.38544)）

[3] Seca, D., & Mendes-Moreira, J. (2021). [名义特征编码在回归中的基准测试](https://link.springer.com/chapter/10.1007/978-3-030-72657-7_14)。在*世界信息系统与技术大会*（第146–155页）。Cham：Springer国际出版社。

[4] Pargent, F., Pfisterer, F., Thomas, J., & Bischl, B. (2022). [正则化目标编码在具有高基数特征的监督机器学习中优于传统方法](https://link.springer.com/article/10.1007/s00180-022-01207-6)。*计算统计学*，*37*(5)，2671–2692。

[5] Cerda, P., & Varoquaux, G. (2020). [高基数字符串分类变量编码](https://ieeexplore.ieee.org/abstract/document/9086128?casa_token=iHWayCtAoQIAAAAA%3AiqUqhSrqAcCcORtpzRHA589n_vgacNAG_xMFbG7NV5BYk7s7L8Uy46P5NUqDoGJ5E_C8eZ6opQ)。*IEEE知识与数据工程学报*，*34*(3)，1164–1176。

[6] Potdar, K., Pardawala, T. S., & Pai, C. D. (2017). [神经网络分类器的分类变量编码技术的比较研究](https://www.researchgate.net/profile/Kedar-Potdar-2/publication/320465713_A_Comparative_Study_of_Categorical_Variable_Encoding_Techniques_for_Neural_Network_Classifiers/links/59e6f9554585151e5465859c/A-Comparative-Study-of-Categorical-Variable-Encoding-Techniques-for-Neural-Network-Classifiers.pdf)。*国际计算机应用期刊*，*175*(4)，7–9。

[7] Dahouda, M. K., & Joe, I. (2021). [一种用于分类特征编码的深度学习嵌入技术](https://ieeexplore.ieee.org/abstract/document/9512057)。*IEEE Access*，*9*，114381–114391。

[8] Cerda, P., Varoquaux, G., & Kégl, B. (2018). [用于脏分类变量学习的相似性编码](https://link.springer.com/article/10.1007/s10994-018-5724-2)。*机器学习*，*107*(8)，1477–1494。

[9] Wright, M. N., & König, I. R. (2019). [随机森林中的分类预测变量分裂](https://peerj.com/articles/6339/)。*PeerJ*，*7*，e6339。

[10] Gnat, S. (2021). [分类变量编码对物业质量估值的影响](https://www.sciencedirect.com/science/article/pii/S1877050921018664)。*Procedia计算机科学*，*192*，3542–3550。

[11] Johnson, J. M., & Khoshgoftaar, T. M. (2021年8月). [高基数特征和集成学习器的编码技术](https://ieeexplore.ieee.org/abstract/document/9599119). 收录于 *2021 IEEE 第22届国际数据科学信息重用与集成会议 (IRI)*（第355-361页）。IEEE。

[12] Valdez-Valenzuela, E., Kuri-Morales, A., & Gomez-Adorno, H. (2021年). [使用合成数据测量类别编码器在机器学习任务中的效果](https://link.springer.com/chapter/10.1007/978-3-030-89817-5_7). 收录于 *计算智能进展：2021年墨西哥国际人工智能会议（MICAI 2021），墨西哥城，2021年10月25–30日，会议论文集，第I部分*（第92-107页）。Springer 国际出版。

[13] [类别编码器基准测试](/benchmarking-categorical-encoders-9c322bd77ee8)（TDS 上的故事）

[14] [类别编码：关键见解](https://medium.com/@vadim.arzamasov/navigating-categorical-encoder-maze-c04e49b165fe)（我在 Medium 上的故事）

[15] [R 语言类别变量对比编码系统](https://stats.oarc.ucla.edu/r/library/r-library-contrast-coding-systems-for-categorical-variables/#ORTHOGONAL)

[16] [类别编码器](https://contrib.scikit-learn.org/category_encoders/)（Python 模块）

[17] [**向基准添加自定义编码器**](https://www.kaggle.com/code/derhamcohomology/add-a-custom-encoder-to-the-benchmark)（Kaggle 笔记本）

[18] [使用 dirty_cat 进行脏类别的相似度编码](/similarity-encoding-for-dirty-categories-using-dirty-cat-d9f0b581a552)（TDS 上的故事）
