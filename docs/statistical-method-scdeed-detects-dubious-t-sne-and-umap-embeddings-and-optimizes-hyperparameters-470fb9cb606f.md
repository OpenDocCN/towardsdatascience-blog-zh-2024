# 统计方法scDEED检测可疑的t-SNE和UMAP嵌入并优化超参数

> 原文：[https://towardsdatascience.com/statistical-method-scdeed-detects-dubious-t-sne-and-umap-embeddings-and-optimizes-hyperparameters-470fb9cb606f?source=collection_archive---------13-----------------------#2024-03-05](https://towardsdatascience.com/statistical-method-scdeed-detects-dubious-t-sne-and-umap-embeddings-and-optimizes-hyperparameters-470fb9cb606f?source=collection_archive---------13-----------------------#2024-03-05)

## scDEED为每个2D嵌入分配一个可靠性评分，以表示数据点在2D空间中其中程邻居的变化程度。

[](https://medium.com/@jsb.ucla?source=post_page---byline--470fb9cb606f--------------------------------)[![Jingyi Jessica Li](../Images/60a8ac9adc41a6ec790d227af7fa3ebd.png)](https://medium.com/@jsb.ucla?source=post_page---byline--470fb9cb606f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--470fb9cb606f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--470fb9cb606f--------------------------------) [Jingyi Jessica Li](https://medium.com/@jsb.ucla?source=post_page---byline--470fb9cb606f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--470fb9cb606f--------------------------------) ·阅读时间6分钟·2024年3月5日

--

*本文由Christy Lee撰写，她是UCLA统计学与数据科学博士生。Christy是scDEED在《自然通讯》期刊发表文章的共同第一作者。*

![](../Images/ecb2fa51cc316498ea05c8cba6158d84.png)

图片来源：[Viktor Forgacs](https://unsplash.com/@sonance?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

t-SNE（t-分布随机邻域嵌入）和UMAP（统一流形逼近与投影）是用于可视化高维数据的非线性降维技术，尤其在单细胞分析中用于可视化细胞群集。然而，重要的是要注意，t-SNE和UMAP可能并不总是能够提供细胞群集之间相对距离的可信表示。

在我们的*《自然通讯》*论文[1]中，我们提供了一个框架，用于（1）识别从高维空间投影到二维（2D）空间中的数据扭曲，以及（2）优化二维降维方法中的超参数设置。

![](../Images/0d1a4c85065c3e14e97874202b2d2a32.png)

Xia, L., Lee, C. & Li, J.J. Statistical method scDEED for detecting dubious 2D single-cell embeddings and optimizing t-SNE and UMAP hyperparameters. *Nat Commun* **15**, 1753 (2024). [https://doi.org/10.1038/s41467-024-45891-y](https://doi.org/10.1038/s41467-024-45891-y)

考虑一个三维（3D）地球与一个二维（2D）地图。仅用二维就不可能准确地表示整个地球；距离可能不准确，一些国家的大小可能会被扭曲。通常，地图边缘的陆地，如南极洲，是变化最大的一部分。尽管存在这些扭曲，二维地图仍然适用于日常使用；学生或普通的大陆旅行者不会受到南极洲扭曲的影响，但一位勇敢的极地旅行者显然需要一张不同的地图。

同样，单细胞基因组学数据的表示通常需要从高维空间转换到二维空间，所谓的二维嵌入。与地球的转换类似，这可能会引入扭曲。二维嵌入后的空间可能无法准确表示预嵌入空间。更麻烦的是，流行的二维嵌入方法，如t-SNE和UMAP，对超参数选择非常敏感。虽然有一些通用的指导原则可以根据数据集的大小来调整超参数，如困惑度（perplexity）和邻居数（n.neighbors），但这些指导原则并不能帮助解答一个根本性的问题——可视化的哪些部分是误导的？

类似于制图师选择忠实重建哪些陆地、扭曲哪些陆地，研究人员必须优先考虑在嵌入后哪些预嵌入空间的方面是最重要的需要保留的。二维可视化的常见用途包括细胞轨迹和簇的注释与分析。尽管细胞轨迹和簇通常是在高维空间中计算的，但它们的结果通常通过二维嵌入进行可视化，在这种嵌入中，基因表达相似的细胞预计彼此接近。因此，我们得出结论，保留的最重要方面是细胞之间相对位置的保持。

这些想法构成了scDEED的动机，它是一个单细胞可疑嵌入检测器（见图1）。关键思想是，细胞的预嵌入和后嵌入邻居应该相似。值得注意的是，单细胞数据分析中的预嵌入空间通常是20到50维的，通常是主成分空间。对于每个细胞，我们计算一个**可靠性得分**，该得分反映了在二维嵌入空间和预嵌入空间中找到的邻居之间的视觉一致性。那些在嵌入过程中邻居发生显著变化的细胞被称为**“可疑”**细胞；这些细胞的相对位置是误导性的，并没有反映出基于预嵌入空间应有的位置。识别这些细胞提供了一种优化超参数的机制，通过选择那些能够产生最少可疑细胞嵌入的设置。

![](../Images/6dfe18932372f7611510bd022a9fbfb0.png)

**图 1.** scDEED的两个功能示意图。功能I通过计算*可靠性评分*来决定每个细胞的嵌入是否为*可信*或*可疑*，其中可靠性评分定义为该细胞在2D嵌入空间中与最近的50%邻近细胞的距离与该细胞在预嵌入空间中与最近的50%邻近细胞的距离之间的Pearson相关性。在功能I的支持下，功能II通过最小化可疑嵌入的数量，优化嵌入方法的超参数设置（例如t-SNE和UMAP）。该图像由作者创建。

在我们的论文[1]中，我们使用了多种数据集来展示如何通过识别可疑细胞和优化超参数来辅助分析。例如，在原始的单细胞RNA-seq Hydra数据集[2]的可视化中，神经元外胚层1（神经元ec1）细胞被分成两个簇，一个被scDEED标记为可疑，另一个则是可信的（图2a）。根据基因表达的相似性（图2c）以及作者分配的单一细胞类型的确认，这两个簇在生物学上并无区别，因此它们在t-SNE中的分离是误导性的。此外，如果我们将神经元ec1细胞与其邻近的簇进行比较，例如高亮显示的外胚层上皮细胞（ecEP_sc），它们的基因表达差异很大，而这一点在可视化中它们的位置接近时显得直觉上难以理解。然而，在scDEED优化后的困惑度下（图2b），神经元ec1细胞类型现在被统一了，进一步证明了最初细胞类型在簇中的分离是超参数设置的结果。此外，神经元ec1和ecEP_sc细胞现在彼此相距较远，这在基因表达的差异上更加合适。这突出了scDEED的两个应用：识别可疑细胞有助于辨别嵌入位置误导的细胞，而优化超参数设置则能得到更可信的可视化结果。

![](../Images/280c2dfe583492aea628e922bbbeed63.png)

**图 2.** 在Hydra数据集上，scDEED优化后的t-SNE嵌入评估。a–b，**对比t-SNE图**，突出显示了*ecEP_sc（外胚层上皮_单细胞）*、*神经元ec1中的可信细胞嵌入*和*神经元ec1中的可疑细胞嵌入*，分别在原始困惑度40（**a**）和通过scDEED优化的困惑度230（**b**）下。**c，**高亮细胞在**a**和**b**中的基因表达热图，细胞根据R函数heatmap.2()找到的默认层次聚类顺序排列。该图像由作者创建，并已发表于文章中。

一个有趣的应用是 RNA 速度 [3]，这是一项依赖于可视化的下游分析任务。RNA 速度利用未剪接和已剪接的 mRNA 数量来估算基因速度——基因表达的变化。估算的基因速度可以用来计算未来时间点的预测基因表达，这可以通过从细胞到细胞预测状态的箭头来进行可视化。对于大规模数据集，逐个绘制每个细胞的速度向量是不现实的；相反，细胞基于其 2D 嵌入进行分组，并将其速度向量进行聚合。对 2D 嵌入的变化不会影响个别细胞的估算基因速度或预测表达，但它会改变细胞分组，从而影响向量场的计算，进而影响可视化的 RNA 速度和分析。使用 scDEED 优化 t-SNE 的超参数困惑度 (图 3a) 大大增强了邻近细胞之间的一致性，并提供了比使用默认超参数值（图 3b）更清晰的 RNA 速度结果。此外，对于成熟颗粒，向量没有被夸大，这是预期的结果，因为细胞已经完全分化。超参数的优化仅增强了现有细胞轨迹。

![](../Images/81d52725a74c6b0c53fa4cf0891e610e.png)

**图 3\. 齿状回数据集的速度分析 [4]. a-b** 使用原始 t-SNE 困惑度为 30（**a**）和通过 scDEED 优化的困惑度 450（**b**）进行的速度可视化，使用默认的 Velocyto [3] 设置。细胞类型的缩写如下：Neuro1：神经母细胞 1；Neuro2：神经母细胞 2；nIPC：神经元中间前体细胞。

最近的研究 [5,6] 强调了几何特性，如测地线、流形和距离，这些特性无法完全重建，因为嵌入前后的空间不是同胚的。scDEED 可以通过找到准确捕捉中等范围细胞间关系的超参数设置，帮助减少不一致性，并识别出那些中等范围邻近关系发生剧烈变化的细胞。我们希望 scDEED 能作为现有分析管道的附加工具，提供更可靠的 2D 可视化。值得指出的是，scDEED 并不衡量数据所有方面的保留；正如制图师认为最重要的是保留五大洲一样，我们选择优先考虑细胞的相对位置。通过对可靠性评分（每个细胞嵌入一个）定义的一些调整，仍有兴趣保留嵌入前空间其他特征的研究人员，可能会发现 scDEED 框架依然具有实用价值。

**参考文献**

1\. Xia L, Lee C, Li JJ. 统计方法 scDEED 用于检测可疑的 2D 单细胞嵌入并优化 t-SNE 和 UMAP 的超参数。Nat Commun. 2024;15: 1753\. doi:10.1038/s41467-024-45891-y

2\. Siebert S, Farrell JA, Cazet JF, Abeykoon Y, Primack AS, Schnitzler CE, 等. 水螅干细胞分化轨迹在单细胞分辨率下解析。Science. 2019;365\. doi:10.1126/science.aav9314

3\. La Manno G, Soldatov R, Zeisel A, Braun E, Hochgerner H, Petukhov V, 等. 单细胞的 RNA 速度。Nature. 2018;560: 494–498.

4\. Hochgerner H, Zeisel A, Lönnerberg P, Linnarsson S. 通过单细胞 RNA 测序揭示了齿状回神经发生在出生后发育中的保守特性。Nat Neurosci. 2018;21: 290–299.

5\. Wang S, Sontag ED, Lauffenburger DA. 在单细胞“组学”数据的二维可视化中，哪些内容无法正确呈现？Cell Syst. 2023;14: 723–731.

6\. Chari T, Pachter L. 单细胞基因组学的虚假艺术。PLoS Comput Biol. 2023;19: e1011288.
