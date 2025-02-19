# 使用特征子集更有效地执行异常值检测

> 原文：[`towardsdatascience.com/perform-outlier-detection-more-effectively-using-subsets-of-features-d984bde99981?source=collection_archive---------1-----------------------#2024-11-24`](https://towardsdatascience.com/perform-outlier-detection-more-effectively-using-subsets-of-features-d984bde99981?source=collection_archive---------1-----------------------#2024-11-24)

## 确定相关子空间：特征的子集，它们可以让你在表格数据上更有效地进行异常值检测

[](https://medium.com/@wkennedy934?source=post_page---byline--d984bde99981--------------------------------)![W Brett Kennedy](https://medium.com/@wkennedy934?source=post_page---byline--d984bde99981--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d984bde99981--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d984bde99981--------------------------------) [W Brett Kennedy](https://medium.com/@wkennedy934?source=post_page---byline--d984bde99981--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d984bde99981--------------------------------) ·阅读时长 28 分钟·2024 年 11 月 24 日

--

本文是系列文章的一部分，涉及在数据中识别异常值的挑战和可以使用的技术，包括使用 PCA、距离度量学习、共享最近邻、频繁模式异常因子、计数异常值检测器（一种基于多维直方图的方法），以及[doping](https://medium.com/towards-data-science/doping-a-technique-to-test-outlier-detectors-3f6b847ab8d4)技术。本文还包含了我书中的一段摘录，[Python 中的异常值检测](https://www.manning.com/books/outlier-detection-in-python)。

在这里，我们探讨了一种技术，旨在创建一系列较小的异常值检测器，而不是单一的异常值检测器来检查数据集中的所有特征，每个检测器都只处理特征的子集（称为*子空间*）。

# 异常值检测中的挑战

在对表格数据进行异常值检测时，我们关注的是数据中最不寻常的记录——这些记录要么与同一数据集中的其他记录相比最为特殊，要么与之前的数据相比最为特殊。

寻找最有意义的异常值存在许多挑战，尤其是没有明确的*统计异常*定义来确定哪些数据中的异常值应被视为最强的异常值。此外，最*相关*的异常值（而不一定是最统计异常的）将取决于你的项目，并可能随着时间的推移而变化。

异常值检测中还存在许多技术挑战，其中之一是数据中存在许多特征时所出现的困难。如在之前与 Counts Outlier Detector 和 Shared Nearest Neighbors 相关的文章中所述，当特征很多时，我们通常会面临一个称为*维度灾难*的问题。

这对异常值检测有多个影响，包括使得距离度量变得不可靠。许多异常值检测算法依赖于计算记录之间的距离——为了识别作为异常值的记录，这些记录与其他记录相比非常相似，且与大多数其他记录有显著不同——也就是说，记录与其他记录的距离是*近*少数记录，*远*多数记录。

例如，如果我们有一个包含 40 个特征的表格，数据中的每条记录可以被视为 40 维空间中的一个点，并且其异常性可以通过该点与该空间中其他点之间的距离来评估。因此，这就需要一种衡量记录之间距离的方法。使用了多种度量方法，其中欧几里得距离相当常见（假设数据是数值型的，或已转换为数值）。因此，每条记录的异常性通常是根据它与数据集中其他记录之间的欧几里得距离来衡量的。

然而，当我们处理许多特征时，这些距离计算可能会出现问题，实际上，即使只有十个或二十个特征，距离度量的问题也可能出现，尤其是当特征数量达到三十、四十个甚至更多时。

需要注意的是，处理大量特征的问题并非所有异常值检测器都会遇到。例如，在使用单变量测试（例如 z-score 或四分位数范围测试，这些测试逐个考虑每个特征，独立于其他特征——在[A Simple Example Using PCA for Outlier Detection](https://medium.com/towards-data-science/a-simple-example-using-pca-for-outlier-detection-ab2773b98e4a)中有更详细的描述）时，它们通常不会显得很重要，或者在使用像[FPOF](https://medium.com/towards-data-science/interpretable-outlier-detection-frequent-patterns-outlier-factor-fpof-0d9cbf51b17a)这样的类别型异常值检测器时。

然而，常用的大多数异常值检测器都是数值型多变量异常值检测器——这些检测器假设所有特征都是数值型的，并且通常会同时处理所有特征。例如，LOF（局部异常因子）和 KNN（k-近邻）是最广泛使用的两种检测器，它们都基于记录与其他记录在高维空间中的距离来评估每个记录的异常性。

# 基于记录与其他数据点的距离判断异常值的示例

请看下面的图。这展示了一个包含六个特征的数据集，并通过三个二维散点图表示。图中包括了两个可以合理认为是异常值的点，P1 和 P2。

现在来看 P1，它至少在特征 A 上与其他点相距较远。也就是说，仅考虑特征 A 时，P1 很容易被标记为异常值。然而，大多数检测器会考虑每个点到其他点的距离，使用所有六个维度，这不幸的是意味着 P1 未必会因为高维空间中的距离计算方式而显得突出。P1 在其他五个特征上是相当典型的，因此它在六维空间中的距离可能是相对正常的。

然而，我们可以看到，这种通用的异常值检测方法——通过检查每个记录到其他记录的距离——是相当合理的：P1 和 P2 是异常值，因为它们与其他点的距离较远（至少在某些维度上）。

![](img/61f3d772fbd9a9a7ef17de2d324cf2bb.png)

# KNN 和 LOF 算法

由于 KNN 和 LOF 是非常常用的异常值检测器，我们将在这里仔细研究它们，然后特别探讨如何在这些算法中使用子空间。

使用 KNN 异常值检测器时，我们选择一个 k 值，决定每个记录与多少个邻居进行比较。假设我们选择 10（在实际应用中，这通常是一个相对典型的值）。

对于每个记录，我们测量它与其 10 个最近邻的距离，这有助于我们了解每个点的孤立性和远离程度。然后，我们需要根据这 10 个距离为每个记录创建一个单一的异常值评分（即一个数字）。通常，我们会取这些距离的均值或最大值。

假设我们选择最大值（使用均值、中位数或其他函数也类似，尽管每个方法有其细微差别）。如果某个记录与其第 10 近邻的距离异常大，这意味着最多有 9 个记录与其相对较近（可能更少），而它与大多数其他点的距离则异常远，因此可以认为它是异常值。

使用 LOF 离群点检测器时，我们采用类似的方法，尽管其工作方式有所不同。我们同样查看每个点到其 k 个最近邻的距离，然后将其与这些 k 个邻居到*它们*的 k 个最近邻的距离进行比较。因此，LOF 通过衡量每个点相对于其邻域内其他点的离群程度来评估离群点。

也就是说，虽然 KNN 使用全局标准来判断与邻居之间的距离是否异常大，但 LOF 则使用局部标准来判断这些距离是否异常大。

LOF 算法的细节实际上要复杂一些，这两个算法（以及这些算法的多种变体）在具体差异上的含义在[《Python 中的离群点检测》](https://www.manning.com/books/outlier-detection-in-python)中有更详细的介绍。

这些本身就是有趣的考虑因素，但此处的主要观点是，KNN 和 LOF 都根据记录与其最近邻的距离来评估记录。而且，如果同时使用大量特征，这些距离度量可能会表现不佳（甚至完全失效），而通过一次只处理少量特征（子空间）可以大大减少这种情况。

使用子空间的思想在检测器不使用距离度量的情况下仍然有用，但在使用基于距离计算的检测器时，使用子空间的一些好处可能会更加明显。而且，像 KNN 和 LOF 这样的距离使用方法在检测器中是相当常见的。除了 KNN 和 LOF 之外，例如，Radius、ODIN、INFLO、LoOP 检测器，以及基于采样和聚类的检测器，都使用距离。

然而，维度灾难的问题也可能出现在其他检测器中。例如，ABOD（基于角度的离群点检测器）使用记录之间的角度来评估每条记录的离群程度，而不是使用距离。但其思路相似，使用子空间在使用 ABOD 时也同样有效。

此外，我将在下面介绍的其他子空间的好处同样适用于许多检测器，无论是否使用距离计算。不过，维度灾难在离群点检测中是一个严重的问题：当检测器使用距离计算（或类似的度量，如角度计算），且特征数量很多时，这些距离计算可能会失效。在上面的图示中，P1 和 P2 在仅考虑六个维度的情况下可能被良好检测到，甚至在使用 10 或 20 个特征时也可能如此，但如果有 100 个维度的话，所有点之间的距离最终可能会非常相似，P1 和 P2 就不再显得异常了。

# 特征数适中时的问题

除了与处理大量特征相关的问题外，即便是在特征数量相对较少的情况下，我们在试图识别数据集中最不寻常的记录时也可能会遇到困难。

虽然大量的特征可能会使记录之间计算出的距离变得毫无意义，但即使是适度数量的特征，也可能使仅在一两个特征上表现异常的记录更难被识别出来。

再次考虑之前展示的散点图，并在这里重复展示。点 P1 在特征 A 上是异常值（但在其他五个特征上并非如此）。点 P2 在特征 C 和 D 上表现异常，但在其他四个特征上并非如此。然而，当考虑这些点到其他点在 6 维空间中的欧几里得距离时，它们可能并不能可靠地突出表现为异常值。使用曼哈顿距离以及大多数其他距离度量时也是如此。

![](img/61f3d772fbd9a9a7ef17de2d324cf2bb.png)

左侧窗格展示了点 P1 在 2D 数据空间中的位置。考虑到特征 A，这个点是异常的，但如果使用 6D 数据空间中的欧几里得距离，甚至在此图所示的 2D 数据空间中，它的异常性就较小了。这是一个使用额外特征可能适得其反的例子。在中间窗格中，我们看到另一个点——点 P2，它在 C-D 子空间中是异常值，但在 A-B 或 E-F 子空间中并不是。我们只需要特征 C 和 D 来识别这个异常值，再次强调，包含其他特征只会让 P2 更难被识别出来。

例如，即使在最左侧图表中显示的二维空间中，P1 与大多数其他点的距离也并不异常远。它的异常之处在于周围没有其他点（KNN 和 LOF 会检测到这一点），但是 P1 到该二维空间中其他点的距离并不异常：它与大多数其他点对之间的距离相似。

使用 KNN 算法，我们很可能能够检测到这一点，至少当 k 设置得相对较低时，例如设为 5 或 10——大多数记录的第 5 个（以及第 10 个）最近邻距离比 P1 更近。不过，当将所有六个特征纳入计算时，这一点就不如仅查看特征 A 或仅查看最左侧的图表（仅包含特征 A 和 B）时那么明显。

当仅考虑特征 C 和 D 时，点 P2 很明显是异常值。使用 KNN 检测器，假设 k 值为 5，我们可以识别它的 5 个最近邻，并且这些点到 P2 的距离会比该数据集中的典型点距离要大。

使用 LOF 检测器，同样设置 k 值为 5，我们可以比较 P1 或 P2 与其 5 个最近邻的距离与它们的 5 个最近邻之间的距离，在这种情况下，P1 或 P2 到它们的 5 个最近邻的距离会被发现异常大。

至少当只考虑特征 A 和 B，或特征 C 和 D 时，这一点比较直接，但当考虑完整的 6 维空间时，它们变得更难以识别为异常值。

尽管许多离群点检测器可能仍然能够在六个或稍多维度的情况下识别 P1 和 P2，但显然使用较少的特征会更容易且更可靠。要检测 P1，我们实际上只需要考虑特征 A；而要识别 P2，我们实际上只需要考虑特征 C 和 D。将其他特征包含进来反而会增加难度。

这实际上是离群点检测中的一个常见主题。我们通常处理的数据集有许多特征，每个特征都可能有用。例如，如果我们有一个包含 50 个特征的表格，可能所有 50 个特征都是相关的：无论是这些特征中的任何一个出现稀有值都可能是有趣的，还是这些 50 个特征中的两个或多个特征出现稀有值组合都可能是有趣的。那么，对于分析来说，保留所有 50 个特征是值得的。

但是，要识别任何一个异常，通常只需要少量特征。事实上，记录在所有特征中异常的情况非常罕见。基于许多特征的稀有组合来判断记录是否异常也非常罕见（请参阅[Counts Outlier Detector](https://medium.com/towards-data-science/counts-outlier-detector-interpretable-outlier-detection-ead0d469557a)以获取更多解释）。

任何给定的离群点可能在一两个特征上具有罕见的值，或在一对特征中具有罕见的值组合，或者在三个或四个特征的组合中具有罕见的值。即使其他特征可能对于检测其他行的异常很重要，识别该行的异常时，只有这些特征是必要的。

# 子空间

为了解决这些问题，离群点检测中的一个重要技术是使用子空间。术语*子空间*仅指特征的子集。在上面的例子中，如果我们使用以下子空间：A-B、C-D、E-F、A-E、B-C、B-D-F，以及 A-B-E，那么我们有七个子空间（五个二维子空间和两个三维子空间）。创建这些子空间后，我们会在每个子空间上运行一个（或多个）检测器，因此每条记录至少会运行七个检测器。

从现实角度看，当特征数超过六个时，子空间变得更加有用，通常即使是子空间本身也会有超过六个特征，而不仅仅是二三个特征，但目前来看，使用特征数量较少的小子空间的简单情况相对容易理解。

使用这些子空间，我们可以更可靠地将 P1 和 P2 识别为离群点。P1 很可能会被运行在特征 A-B、A-E 和 A-B-E 上的检测器打分较高。P2 很可能会被运行在特征 C-D 上的检测器识别出来，可能还会被运行在特征 B-C 上的检测器检测到。

然而，我们必须小心：仅使用这七个子空间，而不是覆盖所有特征的单一 6 维空间，将会错过任何罕见的特征组合，例如 A 和 D，或 C 和 E。这些组合可能会被覆盖所有六个特征的检测器检测到，也可能不会被检测到，但绝对无法通过一组检测器来检测到，因为这些检测器根本不会检查这些特征组合。

使用子空间确实有一些显著的好处，但也存在错过相关离群点的风险。我们将在下面介绍一些生成子空间的技术，以缓解这个问题，但仍然可以考虑在完整的数据空间上运行一个或多个离群点检测器。通常来说，使用离群点检测时，除非我们应用多种技术，否则很难找到我们感兴趣的完整离群点集。尽管子空间的使用非常重要，但仍然通常有必要使用多种技术，这可能包括在完整数据上运行一些检测器。

类似地，在每个子空间中，我们可能会执行多个检测器。例如，我们可能会同时使用 KNN 和 LOF 检测器，以及 Radius、ABOD，甚至可能是其他一些检测器——再次强调，使用多种技术可以帮助我们更好地覆盖希望检测的异常范围。

# 子空间的进一步动机

因此，我们已经看到了一些使用子空间的动机：我们可以缓解维度灾难，并且可以减少那些基于小数量特征且在众多特征中丢失的异常情况，从而无法可靠识别的情况。

除了处理类似的情况外，使用子空间进行离群点检测还有许多其他优势。包括：

+   **由于使用集成方法的精度提升** — 使用多个子空间使我们能够创建集成（离群点检测器集合），这使我们能够结合多个检测器的结果。通常，使用检测器的集成方法比使用单一检测器提供更高的精度。这与集成预测器在分类和回归问题中通常比单一预测器更强的方式类似（尽管也存在一些实际差异）。在这里，使用子空间时，每个记录会被多次检查，这比任何单一检测器提供了更稳定的评估。

+   **可解释性** — 结果可以更具可解释性，而可解释性通常是离群点检测中的一个关键问题。在离群点检测中，我们经常标记异常记录，认为它们可能是某种程度上的问题或关注点，通常这些记录会被手动检查。了解为什么它们不寻常是有效且高效地进行此操作所必需的。通过检测器标记的异常点，尤其是那些检查了许多特征的检测器，手动评估起来可能尤其困难；另一方面，通过只使用少数特征的检测器标记的异常点则更易于评估。

+   **更快的系统** — 使用更少的特征可以帮助我们创建更快（且占用内存更少）的检测器。这可以加速拟合和推理过程，尤其是在处理执行时间与特征数量呈非线性关系的检测器时（例如，许多检测器的执行时间与特征数量的平方成正比）。根据检测器的不同，使用例如 20 个检测器，每个覆盖 8 个特征，可能比使用一个覆盖 100 个特征的单一检测器执行得更快。

+   **并行执行** — 由于我们使用许多小检测器而不是一个大型检测器，因此可以在硬件资源支持的情况下，将拟合和预测步骤并行执行，从而加快执行速度。

+   **随时间调节的简易性** — 使用许多简单的检测器可以创建一个更易于随时间调节的系统。在离群点检测中，我们通常只是在评估一个单一的数据集，并希望识别其中的离群点。但通常也会定期执行离群点检测系统，例如监控工业过程、网站活动、金融交易、输入到机器学习系统或其他软件应用的数据、这些系统的输出等。在这些情况下，我们通常希望随着时间的推移改进离群点检测系统，使我们能够更好地关注更相关的离群点。拥有一组简单的检测器，每个检测器基于少量特征，可以使这项工作更具可管理性。这使得我们能够随着时间的推移，增加更有用的检测器的权重，减少不太有用的检测器的权重。

# 选择子空间

如前所述，我们需要针对每个被评估的数据集，确定适当的子空间。然而，找到相关的子空间集合可能很困难，或者至少难以找到最优的子空间集合。也就是说，假设我们有兴趣找到任何异常的值组合，确定哪些特征集包含最相关的异常组合可能是困难的。

例如，如果一个数据集有 100 个特征，我们可以训练 10 个模型，每个模型覆盖 10 个特征。我们可以使用例如前 10 个特征作为第一个检测器，接下来的 10 个特征作为第二个检测器，依此类推。如果前两个特征存在一些具有异常值组合的行，我们将检测到这一点。但如果存在与第一个特征相关的异常值组合，而这些异常值组合与其余 90 个未包含在同一模型中的特征相关，我们将无法检测到这些。

我们可以通过使用更多的子空间来提高将相关特征组合在一起的可能性，但要确保所有应该在一起的特征集至少出现一次是很困难的，特别是在数据中存在基于三个、四个或更多特征的相关异常值时——这些特征必须至少在一个子空间中一起出现才能被检测到。例如，在一个员工费用表格中，你可能希望识别部门、费用类型和金额的稀有组合。如果是这样，这三个特征必须至少在一个子空间中一起出现。

所以，我们有以下几个问题：每个子空间中应该包含多少个特征，哪些特征应该放在一起，应该创建多少个子空间。

需要考虑的组合非常多。如果有 20 个特征，那么可能的子空间有²²⁰个，略多于一百万个。如果有 30 个特征，则有超过十亿个。如果我们提前决定每个子空间中包含多少个特征，组合的数量会减少，但仍然非常庞大。如果有 20 个特征，我们希望每个子空间有 8 个特征，则有 20 选 8，即 125,970 个组合。如果有 30 个特征，我们希望每个子空间有 7 个特征，则有 30 选 7，即 2,035,800 个组合。

我们可能希望采取的一种方法是保持子空间较小，这样有助于提高可解释性。最具可解释性的选项是每个子空间使用两个特征，它还允许简单的可视化。然而，如果我们有 d 个特征，我们将需要 d*(d-1)/2 个模型来覆盖所有组合，这可能是不可行的。如果有 100 个特征，我们将需要 4,950 个检测器。我们通常需要每个检测器使用至少几个特征，但不一定是大量特征。

我们希望使用足够的检测器，每个检测器使用足够的特征，使得每对特征理想情况下至少出现一次，并且每个检测器中的特征足够少，以便这些检测器之间具有显著不同的特征。例如，如果每个检测器使用 100 个特征中的 90 个，我们将很好地覆盖所有特征组合，但子空间仍然会相当大（从而抵消使用子空间的很多好处），而且所有子空间也会彼此非常相似（从而抵消创建集成方法的很多好处）。

虽然每个子空间中使用的特征数量需要平衡这些问题，但创建的子空间数量则稍微直观一些：从准确性角度看，使用更多子空间是绝对更好的，但计算开销更大。

寻找有用子空间的方法有几种广泛的策略。我在这里快速列出这些方法，然后在下面详细讨论一些。

+   **基于领域知识**——在这里我们考虑哪些特征组合可能具有我们认为值得注意的值组合。

+   **基于关联** — 只有当一组特征以某种方式关联时，才可能出现值的异常组合。在预测问题中，我们通常希望最小化特征之间的相关性，但在异常值检测中，这些特征是最有用的，应该一起考虑。如果有异常情况出现，具有最强关联的特征将包含最有意义的异常值。

+   **基于找到非常稀疏的区域** — 如果记录与数据中的大多数其他记录不同，通常会被视为异常值，这意味着它们位于数据的稀疏区域。因此，可以将包含大且几乎为空的区域的子空间视为有用的子空间。

+   **随机** — 这是稍后将介绍的一种技术，称为*FeatureBagging*，虽然它可能不是最优的，但它避免了昂贵的关联搜索和稀疏区域搜索，并且在使用多个子空间的情况下，能够较好地工作。

+   **穷举搜索** — 这是[Counts Outlier Detector](https://medium.com/towards-data-science/counts-outlier-detector-interpretable-outlier-detection-ead0d469557a)使用的方法。这仅限于具有少量特征的子空间，但结果是高度可解释的。它还避免了与选择可能子空间的子集相关的任何计算或偏差。

+   **使用与已知异常值相关的特征** — 如果我们有一组已知的异常值，并且能够确定它们为何是异常值（相关特征），并且我们不希望识别未知的异常值（仅识别这些特定的异常值），那么我们可以利用这一点，确定与每个已知异常值相关的特征集，并为所需的各个特征集构建模型。

接下来我们将更详细地探讨其中的一些内容。

## **领域知识**

让我们以一个数据集为例，特别是下面展示的开销表。如果我们查看此表格，我们可能能够确定我们感兴趣或不感兴趣的异常值类型。帐户和金额的异常组合，以及部门和帐户的异常组合，可能会引起我们的兴趣；而费用日期和时间的组合可能不会是有用的组合。我们可以继续以这种方式进行，创建少数几个子空间，每个子空间通常包含两个、三个或四个特征，这可以实现非常高效且可解释的异常值检测，标记出最相关的异常值。

![](img/b937a9fb973ad691e3cc5eec0b4e1cfd.png)

开销表

这种方法可能会遗漏数据中存在某些关联的情况，尽管这种关联并不显而易见。因此，除了利用领域知识外，搜索数据中的关联也许是值得的。例如，我们可以发现特征之间的关系，例如，测试是否可以通过其他特征使用简单的预测模型准确预测某些特征。如果发现了这样的关联，它们值得进一步调查。

然而，发现这些关联可能对某些目的有用，但可能对异常值检测过程有用也可能无用。例如，如果账户和时间之间存在关系，这可能仅仅是因为人们通常使用的报销流程，而这种偏差可能是值得关注的，但更可能并不重要。

## 随机特征子空间

如果没有领域知识可以借鉴，随机创建子空间可能是有效的。这种方法快速且能够创建一组子空间，这些子空间往往能够捕捉到最强的异常值，尽管它也可能遗漏一些重要的异常值。

以下代码提供了创建一组随机子空间的示例。这个示例使用了一组八个特征，命名为 A 到 H，并基于这些特征创建子空间。

每个子空间首先选择目前使用最少的特征（如果有平局，则随机选择一个）。它使用一个名为*ft_used_counts*的变量来跟踪这一点。接着，它会逐一添加特征到这个子空间，每一步选择在当前子空间中与其他子空间中出现次数最少的特征。它使用一个名为*ft_pair_mtx*的特征来跟踪每对特征已经一起出现在多少个子空间中。通过这种方式，我们创建了一组子空间，使得每对特征大致相同的频率出现在子空间中。

```py
import pandas as pd
import numpy as np

def get_random_subspaces(features_arr, num_base_detectors,
                         num_feats_per_detector):
    num_feats = len(features_arr)
    feat_sets_arr = []
    ft_used_counts = np.zeros(num_feats) 
    ft_pair_mtx = np.zeros((num_feats, num_feats))  

    # Each loop generates one subspace, which is one set of features
    for _ in range(num_base_detectors):  
        # Get the set of features with the minimum count      
        min_count = ft_used_counts.min() 
        idxs = np.where(ft_used_counts == min_count)[0]    

        # Pick one of these randomly and add to the current set
        feat_set = [np.random.choice(idxs)]   

        # Find the remaining set of features
        while len(feat_set) < num_feats_per_detector: 
            mtx_with_set = ft_pair_mtx[:, feat_set]
            sums = mtx_with_set.sum(axis=1)
            min_sum = sums.min()
            min_idxs = np.where(sums==min_sum)[0]
            new_feat = np.random.choice(min_idxs)
            feat_set.append(new_feat)
            feat_set = list(set(feat_set))

            # Updates ft_pair_mtx
            for c in feat_set: 
                ft_pair_mtx[c][new_feat] += 1
                ft_pair_mtx[new_feat][c] += 1

        # Updates ft_used_counts
        for c in feat_set: 
            ft_used_counts[c] += 1

        feat_sets_arr.append(feat_set)

    return feat_sets_arr

np.random.seed(0)
features_arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'] 
num_base_detectors = 4
num_feats_per_detector = 5

feat_sets_arr = get_random_subspaces(features_arr, 
                                     num_base_detectors, 
                                     num_feats_per_detector)
for feat_set in feat_sets_arr:    
    print([features_arr[x] for x in feat_set])
```

通常，我们会创建更多的基础检测器（每个子空间通常对应一个基础检测器，尽管我们也可以在每个子空间上运行多个基础检测器），但是为了简化，本示例仅使用了四个。这将输出以下子空间：

```py
['A', 'E', 'F', 'G', 'H']
['B', 'C', 'D', 'F', 'H']
['A', 'B', 'C', 'D', 'E']
['B', 'D', 'E', 'F', 'G']
```

这里的代码将创建具有相同特征数量的子空间。让子空间覆盖不同数量的特征也有一个好处，因为这可以引入更多的多样性（这在创建集成模型时非常重要），但无论如何，通过使用不同的特征已经能够提供强大的多样性（只要每个子空间使用相对较少的特征，从而使得子空间大体上具有不同的特征）。

拥有相同数量的特征有几个好处。它简化了模型的调优，因为许多异常值检测器使用的参数依赖于特征的数量。如果所有子空间都有相同数量的特征，它们也可以使用相同的参数。

它还简化了分数组合，因为检测器之间的可比性更强。如果使用不同数量的特征，可能会生成在不同尺度上的分数，难以进行比较。例如，使用 k-最近邻（KNN）时，如果特征更多，我们预期邻居之间的距离会更大。

## 基于相关性的特征子空间

在创建子空间时，其他条件相同的情况下，尽可能将关联特征放在一起是非常有用的。在下面的代码中，我们提供了一个示例，展示如何根据相关性选择子空间。

测试关联性有几种方法。我们可以创建预测模型，尝试从每个特征中预测其他单一特征（这将捕捉到特征之间甚至是相对复杂的关系）。对于数值特征，最简单的方法可能是检查斯皮尔曼相关性，这种方法虽然无法检测到非单调关系，但能发现大多数强关联关系。以下代码示例使用的就是这种方法。

要执行代码，我们首先指定所需的子空间数量以及每个子空间中的特征数量。

该方法首先通过寻找特征之间的所有配对相关性并将其存储在一个矩阵中来执行。然后我们创建第一个子空间，方法是首先找到相关矩阵中的最大相关性（这会将两个特征添加到该子空间），然后遍历待添加到该子空间的其他特征数量。对于每个特征，我们从相关矩阵中选择一对特征，要求其中一个特征已经在子空间内，而另一个特征还未在子空间内。一旦该子空间包含足够的特征，我们就创建下一个子空间，继续选择相关矩阵中剩余的最大相关性，以此类推。

在这个例子中，我们使用一个真实数据集——[baseball](https://www.openml.org/search?type=data&sort=version&status=any&order=asc&exact_name=baseball&id=185)数据集，来自 OpenML（该数据集具有公开许可）。这个数据集包含了一些较强的相关性。例如，击球次数（At bats）和得分（Runs）之间的相关性为 0.94，这意味着任何显著偏离这一模式的值很可能是异常值。

```py
import pandas as pd
import numpy as np
from sklearn.datasets import fetch_openml

# Function to find the pair of features remaining in the matrix with the 
# highest correlation
def get_highest_corr(): 
    return np.unravel_index(
        np.argmax(corr_matrix.values, axis=None), 
        corr_matrix.shape)

def get_correlated_subspaces(corr_matrix, num_base_detectors, 
                                num_feats_per_detector):
    sets = []

    # Loop through each subspace to be created
    for _ in range(num_base_detectors): 
        m1, m2 = get_highest_corr()

        # Start each subspace as the two remaining features with 
        # the highest correlation
        curr_set = [m1, m2] 
        for _ in range(2, num_feats_per_detector):
            # Get the other remaining correlations
            m = np.unravel_index(np.argsort(corr_matrix.values, axis=None), 
                                 corr_matrix.shape) 
            m0 = m[0][::-1]
            m1 = m[1][::-1]
            for i in range(len(m0)):
                d0 = m0[i]
                d1 = m1[i]
                # Add the pair if either feature is already in the subset
                if (d0 in curr_set) or (d1 in curr_set): 
                    curr_set.append(d0)
                    curr_set = list(set(curr_set))
                    if len(curr_set) < num_feats_per_detector:
                        curr_set.append(d1)
                        # Remove duplicates
                        curr_set = list(set(curr_set)) 
                if len(curr_set) >= num_feats_per_detector:
                    break

            # Update the correlation matrix, removing the features now used 
            # in the current subspace
            for i in curr_set: 
                i_idx = corr_matrix.index[i]
                for j in curr_set:
                    j_idx = corr_matrix.columns[j]
                    corr_matrix.loc[i_idx, j_idx] = 0
            if len(curr_set) >= num_feats_per_detector:
                break

        sets.append(curr_set)
    return sets

data = fetch_openml('baseball', version=1)
df = pd.DataFrame(data.data, columns=data.feature_names)

corr_matrix = abs(df.corr(method='spearman'))
corr_matrix = corr_matrix.where(
    np.triu(np.ones(corr_matrix.shape), k=1).astype(np.bool))
corr_matrix = corr_matrix.fillna(0)

feat_sets_arr = get_correlated_subspaces(corr_matrix, num_base_detectors=5, 
                                         num_feats_per_detector=4)
for feat_set in feat_sets_arr:    
    print([df.columns[x] for x in feat_set]) 
```

这将产生：

```py
['Games_played', 'At_bats', 'Runs', 'Hits']
['RBIs', 'At_bats', 'Hits', 'Doubles']
['RBIs', 'Games_played', 'Runs', 'Doubles']
['Walks', 'Runs', 'Games_played', 'Triples']
['RBIs', 'Strikeouts', 'Slugging_pct', 'Home_runs']
```

# PyOD

[PyOD](https://github.com/yzhao062/pyod)可能是目前 Python 中最全面、最常用的数值表格数据异常值检测工具。它包括大量的检测器，涵盖从非常简单到非常复杂的多种方法——其中包括几种基于深度学习的方法。

现在我们已经了解了子空间在异常值检测中的作用，接下来我们将看看 PyOD 提供的两个处理子空间的工具：SOD 和 FeatureBagging。两个工具都会识别一组子空间，对每个子空间执行检测器，并将结果合并为每条记录的单一分数。

无论是否使用子空间，都必须确定使用什么基础检测器。如果不使用子空间，我们将选择一个或多个检测器，并在整个数据集上运行它们。如果使用子空间，我们同样选择一个或多个检测器，并在每个子空间上运行这些检测器。如上所述，LOF 和 KNN 可以是合理的选择，但 PyOD 还提供了许多其他的选择，这些选择在每个子空间上执行时也能很好地工作，例如基于角度的离群点检测器（ABOD）、基于高斯混合模型（GMM）的模型、核密度估计（KDE）等。PyOD 之外的其他检测器也能非常有效地工作。

# SOD（子空间离群点检测）

SOD（子空间离群点检测）专门设计来处理如上所示的情况。SOD 的工作方式类似于 KNN 和 LOF，通过为每个点识别 k 个邻居的邻域，这些邻居被称为*参考集*。不过，参考集是通过不同的方式找到的，使用的是一种叫做共享最近邻（SNN）的方法。

共享最近邻在这篇文章中有详细描述，但一般来说，如果两个点是由相同机制生成的，它们不仅会接近，而且往往会有许多相同的邻居。因此，任何两个记录的相似度可以通过它们共享的邻居数量来衡量。基于这一点，邻域可以通过使用不仅是欧几里得距离最小的点集（如 KNN 和 LOF 方法所做的），还可以使用共享最多邻居的点来识别。这种方法即使在高维空间中，甚至在有许多无关特征的情况下，也能保持鲁棒性：即使在这些情况下，邻居的排名顺序仍然保持有意义，因此即使在无法计算具体距离的情况下，最近邻集仍然能够可靠地找到。

一旦我们得到了参考集，就可以用它来确定子空间，这里指的是解释参考集方差最大的一组特征。一旦识别出这些子空间，SOD 就会检查每个点到数据中心的距离。

下面我提供了一个使用 SOD 的快速示例。假设已经安装了 pyod，安装步骤如下：

```py
pip install pyod
```

我们将使用一个合成数据集作为示例，这使我们可以通过实验数据和模型超参数，更好地理解每个检测器的优缺点。这里的代码提供了一个处理 35 个特征的示例，其中两个特征（特征 8 和 9）是相关的，其他特征是无关的。一个单独的离群点是通过这两个相关特征的不寻常组合创建的。

SOD 能够将已知的一个异常点识别为最显著的异常点。我将污染率设置为 0.01，以指定返回（假设有 100 条记录）仅一个异常点。然而，当测试超过 35 个特征时，SOD 将此点的得分大大降低。此示例将参考集的大小指定为 3；使用不同的值可能会得到不同的结果。

```py
import pandas as pd
import numpy as np
from pyod.models.sod import SOD

np.random.seed(0)
d = np.random.randn(100, 35)
d = pd.DataFrame(d)

#A Ensure features 8 and 9 are correlated, while all others are irrelevant
d[9] = d[9] + d[8] 

# Insert a single outlier
d.loc[99, 8] = 3.5 
d.loc[99, 9] = -3.8

#C Execute SOD, flagging only 1 outlier
clf = SOD(ref_set=3, contamination=0.01) 
d['SOD Scores'] = clf.fit (d)
d['SOD Scores'] = clf.labels_
```

我们下面展示了四个散点图，显示了 35 个特征中的四对特征。在每个图中，已知的异常点被标记为星号。我们可以在第二个面板中看到特征 8 和 9（这两个相关特征），并且可以看到该点明显是一个异常点，尽管它在所有其他维度中是典型的。

![](img/5a3159315c07d883e72c50cda17ea3e0.png)

测试 SOD 在 35 维数据上的表现。数据中插入了一个异常点，并且可以在第二个面板中清楚地看到特征 8 和 9。尽管该点在其他维度中是典型的，它还是被 SOD 标记为最显著的异常点。第三个面板也包含特征 9，我们可以看到该点在这里有些不同寻常，尽管与其他维度中的许多点相比并无太大区别。特征 8 和 9 之间的关系最为相关，而 SOD 似乎能够检测到这一点。

# FeatureBagging

FeatureBagging 的设计目的是解决与 SOD 相同的问题，尽管它采用了不同的方法来确定子空间。它完全随机地创建子空间（与上面的示例略有不同，后者会记录每对特征一起被放入子空间的频率，并尝试平衡这一点）。它还会对每个基本检测器进行行采样，从而在检测器之间提供更多的多样性。

使用指定数量的基本检测器（默认值为 10，尽管使用更多的检测器会更好），每个检测器都会选择一组随机的行和特征。对于每个检测器，可以选择的最大特征数被指定为一个参数，默认为所有特征。因此，对于每个基本检测器，FeatureBagging 会：

+   确定要使用的特征数量，直到达到指定的最大值。

+   随机选择如此多的特征。

+   随机选择一组行。这是一个与行数相同大小的自助样本。

+   创建一个 LOF 检测器（默认为此；也可以使用其他基本检测器）来评估子空间。

一旦完成，每一行将通过每个基本检测器进行评分，然后必须将这些评分合并为每一行的单一最终评分。PyOD 的 FeatureBagging 提供了两种合并评分的选项：使用最大评分和使用平均评分。

正如我们在上面的散点图中所看到的，某些子空间中的点可能是强异常值，而在其他子空间中则不是，从这些子空间中取平均分数，可能会稀释它们的得分，进而削弱使用子空间的好处。在其他异常值检测的集成方法中，使用均值通常有效，但在处理多个子空间时，使用最大值通常是两个选项中更好的。这样，我们根据记录在最异常的子空间中的得分来给每个记录打分。这也不是完美的，可能还有更好的选择，但使用最大值既简单又几乎总是比均值更可取。

任何检测器都可以在子空间中使用。PyOD 默认使用 LOF，就像最初描述 FeatureBagging 的论文一样。LOF 是一个强大的检测器，是一个合理的选择，尽管你可能会发现使用其他基础检测器能得到更好的结果。

在原始论文中，子空间是随机创建的，每个子空间使用 d/2 到 d-1 之间的特征，其中 d 是特征的总数。一些研究人员指出，原始论文中使用的特征数量可能远大于适当的数量。

如果特征的总数较大，同时使用超过一半的特征将使维度灾难生效。而且，在每个检测器中使用许多特征将导致检测器之间的相关性（例如，如果所有基础检测器都使用 90%的特征，它们将使用大致相同的特征，并且倾向于为每条记录打上相似的分数），这也可能会消除创建集成模型的许多好处。

PyOD 允许设置每个子空间中使用的特征数量，通常应该设置得比较低，并创建大量的基础估计器。

# 使用其他检测器

在本文中，我们探讨了子空间作为改善异常值检测的一种方式，包括减少维度灾难、提高可解释性、支持并行执行、简化时间上的调优等。每个因素都是重要的考虑因素，使用子空间通常非常有帮助。

然而，通常还有其他方法可以用于这些目的，有时作为替代方案，有时与使用子空间结合使用。例如，为了提高可解释性，重要的是尽可能选择本身具有可解释性的模型类型（例如，单变量测试，如 z 分数测试、计数异常值检测器，或 PyOD 提供的一个名为 ECOD 的检测器）。

当主要关注点是减少维度灾难时，在这里再次，查看那些在许多特征上表现良好的模型类型可能会很有用，例如隔离森林（Isolation Forest）或计数异常值检测器（Counts Outlier Detector）。此外，执行单变量测试或应用[PCA](https://medium.com/towards-data-science/a-simple-example-using-pca-for-outlier-detection-ab2773b98e4a)也是有益的。

# 正在进行的异常值检测项目

在构建子空间时需要注意的一点是，如果子空间是基于相关性或稀疏区域形成的，那么随着数据的变化，相关的子空间可能会发生变化。随着新特征之间的关联出现或新的稀疏区域形成，这些区域可能对识别离群点有用，但如果不定期重新计算子空间，可能会错过这些信息。通过这种方式找到相关的子空间可以非常有效，但可能需要在某些时间表上进行更新，或者当已知数据发生变化时进行更新。

# 结论

在表格数据的离群点检测项目中，通常值得考虑使用子空间，特别是在我们有许多特征的情况下。使用子空间是一种相对简单的技术，并具有许多显著的优势。

在面临大数据量、执行时间或内存限制等问题时，使用[PCA](https://medium.com/towards-data-science/a-simple-example-using-pca-for-outlier-detection-ab2773b98e4a)也可能是一种有用的技术，并且在某些情况下可能比创建子空间更有效，尽管使用子空间（因此，使用原始特征而不是 PCA 生成的组件）通常更具可解释性，而可解释性在离群点检测中往往非常重要。

子空间可以与其他技术结合使用，以提高离群点检测的效果。例如，使用子空间可以与其他方法结合来创建集成：通过子空间（在集成中的不同检测器使用不同的特征）以及不同的模型类型、不同的训练数据、不同的预处理等，能够创建更大的集成。这可以带来一些额外的好处，但也会增加计算量。

所有图片均由作者提供
