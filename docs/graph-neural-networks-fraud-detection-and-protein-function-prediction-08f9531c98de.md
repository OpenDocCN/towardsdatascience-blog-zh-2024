# 图神经网络：欺诈检测与蛋白质功能预测

> 原文：[https://towardsdatascience.com/graph-neural-networks-fraud-detection-and-protein-function-prediction-08f9531c98de?source=collection_archive---------0-----------------------#2024-11-21](https://towardsdatascience.com/graph-neural-networks-fraud-detection-and-protein-function-prediction-08f9531c98de?source=collection_archive---------0-----------------------#2024-11-21)

## 理解机器学习工程师在生物领域的AI应用

[](https://medium.com/@meghanheintz?source=post_page---byline--08f9531c98de--------------------------------)[![Meghan Heintz](../Images/9eaae6d3d8168086d83ff7100329c51f.png)](https://medium.com/@meghanheintz?source=post_page---byline--08f9531c98de--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--08f9531c98de--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--08f9531c98de--------------------------------) [Meghan Heintz](https://medium.com/@meghanheintz?source=post_page---byline--08f9531c98de--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--08f9531c98de--------------------------------) ·阅读时长7分钟·2024年11月21日

--

![](../Images/e56db49516d0f5fd8f3f3c247711da18.png)

图片由[Conny Schneider](https://unsplash.com/@choys_?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源：[Unsplash](https://unsplash.com/photos/a-blue-background-with-lines-and-dots-xuTJZ7uD7PI?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

金融交易网络和蛋白质结构有什么共同点？它们在欧几里得（x, y）空间中都表现得不佳，需要对复杂、大规模、异质图形进行编码，才能真正理解。

![](../Images/dd117c6208688bc1d86b447bba16305d.png)

左：欧几里得空间中的图像。右：非欧几里得空间中的图形。[来自《图神经网络：方法与应用综述》](https://www.sciencedirect.com/science/article/pii/S2666651021000012#bib56)

图是表示金融网络和蛋白质结构中关系数据的自然方式。它们捕捉实体之间的关系与互动，例如金融系统中账户之间的交易，或蛋白质中氨基酸之间的键合与空间接近度。然而，更广为人知的深度学习架构，如RNN/CNN和Transformer，无法有效地建模图形。

你可能会问，为什么我们不能把这些图形直接映射到3D空间？如果我们强行将它们放入3D网格：

+   我们将失去边缘信息，例如分子图中的键类型或交易类型。

+   映射可能需要填充或调整大小，从而导致扭曲。

+   稀疏的3D数据结果会导致许多未使用的网格单元，从而浪费内存和处理能力。

鉴于这些局限性，**图神经网络（GNNs）**作为一种强大的替代方案。在我们关于[生物学应用中的机器学习](https://medium.com/@meghanheintz/list/understanding-ai-applications-in-bio-for-ml-engineers-7b9e9551bb7f)系列的继续中，我们将探讨GNNs如何应对这些挑战。

一如既往，我们将从更熟悉的欺诈检测话题开始，然后学习这些类似的概念在生物学中的应用。

# 欺诈检测

为了清晰明确，让我们首先定义什么是图。我们记得在小学时在x、y轴上绘制图形，但我们当时真正做的是[绘制函数图](https://en.wikipedia.org/wiki/Graph_of_a_function)，在图上绘制f(x)=y的点。当我们在GNN（图神经网络）的背景下谈论“图”时，我们指的是建模对象之间的配对关系，其中每个对象是一个节点，而关系是边。

在金融网络中，节点是账户，边是交易。该图将由关联方交易（RPT）构建，并可以通过属性（例如时间、金额、货币）进行丰富。

![](../Images/287793aa4a3a07394fb807b1a432e099.png)![](../Images/5f4b41d38a1b7842a1cd0d66ed6792da.png)

左侧：函数图（我们不讨论的内容）（2024年3月15日）。摘自*维基百科*。[https://en.wikipedia.org/wiki/Graph_of_a_function](https://en.wikipedia.org/wiki/Graph_of_a_function) 右侧：具有节点和边的图（我们讨论的内容）（2024年10月25日）。摘自*维基百科*。[https://en.wikipedia.org/wiki/Graph_theory](https://en.wikipedia.org/wiki/Graph_theory)

传统的基于规则和机器学习的方法通常只处理单一的交易或实体。这一局限性未能考虑到交易如何与更广泛的网络连接。因为诈骗者通常跨多个交易或实体进行操作，所以诈骗可能未被发现。

通过分析图形，我们可以捕捉直接邻居和更远连接之间的依赖关系和模式。这对于检测洗钱至关重要，因为资金通过多个交易流动以掩盖其来源。GNNs揭示了洗钱方法所创造的密集子图。

![](../Images/6f0353065d78d76bb14b60891d56bfb8.png)

与[使用GNN检测基于关联方交易网络的金融欺诈](https://pdf.sciencedirectassets.com/280203/1-s2.0-S1877050922X00173/1-s2.0-S1877050922018956/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEN7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJIMEYCIQDmpYTBTxDWh1A1%2BpPCyckZYvi2Nibrmgp5emc9oNQXlQIhAJ3wUM5Vj4%2FCTjt6CDrkYO4ph%2FN3On8JBU5MdLhciDxyKrIFCHcQBRoMMDU5MDAzNTQ2ODY1IgwTobTDUoeog6MueskqjwWI8o3mdlZLaJ5z1t5V%2F%2B2ZcfGjr%2FscaRbGl9jWKT%2ByXW2xAxqvWqFTJpVl4l3Zzv7ACPsxVY8RClj6FOe7W32hm5NTaqQq%2BRi42f55tM3hmwQT4WWOPa06zyEiCwBMgRnAX7SWeTpNRBegsGlNvTLa6dPoziPWOzu7HMv3%2Bt%2BrG7o9fPHaP8dBVFlbXorIPwTeicPh3HnCYHMLaKSVMtR76ZjtOwMzzbUfpzPgXzzjMDuEP5UgNsktqQ4lMwj4Z1iuuYVWxaFIlAElm4n17b%2B0uVprUzf7268iDXWTFGZebUYm6YJt%2Fa6y2ycY7gWo4XUnmrN4GHkCLoQNiIcvMhwRtlF6eqib6Eo5JvJRMe1OmQEIdU%2BQq62WYOl3x3Dt1ibe7aQUfVxwrymXUt7HjuVe%2BfKfSbvtRL%2Fd21kgBcA%2F4RnP2Iqv5Tg2NMCXInCJ7EIaw2y1s2ZSPSPKSRt2rcX0YMFHP1vyXDcxONU6Gybp%2B%2B1fRkoXY6AkqfoC5QwVDS68aRYg3S%2FhdaW6zIWGY2vjOc35f3TEPu1mx1nMrdMMrn2ZHtBvdR6hNZG1MkkqbsCJvpqxSivlNR6LFTqQ5C4Wy419YLpv%2Fy%2F26B%2Ftxz8LEbHY9pTd%2BZ7YEKCGXfRs8e0Ju4ALcznreLzJh7mwh85gxkP5US%2Fa1FsbTvZxe5deLjmjBLX%2Fq2wEbOVJDusY8LYkLek%2Fd6kfEu35E1QNCIjKR3YCd7yBlgHZoOSuiVARMeGt4kGdwSMHGU6uuBufZNvCn0diYOxxnywJ45HnEwRzc9IoDildTvj7LBh6jC%2FfIo0OBXwoZkt5m9YkBcWuzNojkOaZSchchqmikcDQLGT05BfDZ4wWKQC5GFawAQGmMNex8rkGOrABqtUDkTpujnzOcs0IpHrAGx01hFPOfDBp6S%2Bq%2B3lmBEG1yhXPDJSebGsHBTYf09fOj9ql6UQGhEaYi%2BkOVzLnFmpmjoNsuw4PgSyVqevV5zGVxVeDqGVbdhQFUqJPXqRRb%2BXmjH%2FYbvxD0o9bfP1HERYS2li2CrE%2BIP5V4DwdVonFuZEfkgzwDNx97GrQI42i44ar2YRIqOO%2FXHFSRifLMpdfgV%2Fq85YvDuYIFOLHOHI%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20241119T144009Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTY4UDBK2ZU%2F20241119%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=42b1ff3fe7f83791b38aa18964428551a53184eaa2759a0389ab51df9d3828fa&hash=728fa1835f0699e9951cc0c2fbe4fb313cffe2740de875b7e60e68a2770188dc&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S1877050922018956&tid=spdf-19095051-fc91-4c7a-ab21-c03e79c4a978&sid=2594d8d573f9514c458af0a5cdffe931090bgxrqa&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=10145d00525003565d03&rr=8e50f42a1a8d7d00&cc=us)中的关联方转移网络示例

## 消息传递框架

与其他深度学习方法类似，目标是从数据集中创建表示或嵌入。在GNN中，这些节点嵌入是通过消息传递框架创建的。消息在节点之间迭代传递，使得模型能够学习图的局部和全局结构。每个节点的嵌入是基于其邻居特征的聚合来更新的。

该框架的概括如下：

+   **初始化**：嵌入向量*hv(0)*通过基于节点特征的嵌入、随机嵌入或预训练嵌入（例如账户名称的词嵌入）进行初始化。

+   **消息传递：** 在每一层 *t*，节点与它们的邻居交换消息。消息定义为发送节点的特征、接收节点的特征以及连接它们的边的特征，并通过一个函数进行组合。组合函数可以是简单的拼接，并使用固定权重方案（由[图卷积网络](https://arxiv.org/abs/1609.02907)，GCNs使用），也可以是加权注意力机制，其中权重基于发送节点和接收节点的特征（以及可选的边特征）进行学习（由[图注意力网络](https://arxiv.org/abs/1710.10903)，GATs使用）。

+   **聚合：** 在消息传递步骤之后，每个节点聚合接收到的消息（可以是均值、最大值、总和等简单操作）。

+   **更新：** 聚合后的消息通过更新函数（可能是像 ReLU 的多层感知器（MLP），门控循环单元（GRU）或注意力机制）更新节点的嵌入。

+   **最终化：** 像其他深度学习方法一样，当表示稳定或达到最大迭代次数时，嵌入会被最终化。

![](../Images/5eaee950880ff032dce1a690a4e6ecb4.png)

在消息传递神经网络（MPNN）层中，节点表示更新。节点接收所有直接邻居发送的消息。消息通过消息函数计算，该函数考虑了发送方和接收方的特征。图神经网络。（2024年11月14日）。摘自*维基百科*。[https://en.wikipedia.org/wiki/Graph_neural_network](https://en.wikipedia.org/wiki/Graph_neural_network)

在节点嵌入学习完成后，可以通过几种不同的方式计算欺诈分数：

+   **分类：** 最终的嵌入被传入分类器，如多层感知器（MLP），这需要一个全面的标注历史训练集。

+   **异常检测：** 嵌入被分类为异常，基于其与其他嵌入的差异。这里可以使用基于距离的分数或重构误差来进行无监督的方法。

+   **图级评分：** 嵌入被聚合到子图中，然后传入分类器，以检测欺诈环（同样需要一个标注的历史数据集）。

+   **标签传播：** 一种半监督方法，其中标签信息基于边的权重或图的连接性进行传播，从而对未标注的节点进行预测。

现在我们已经对图神经网络（GNN）有了基础理解，可以转向 GNN 的另一个应用：预测蛋白质的功能。

# 蛋白质功能预测

我们已经看到了通过AlphaFold [2](https://medium.com/towards-data-science/alphafold-2-through-the-context-of-bert-78c9494e99af)和[3](https://medium.com/towards-data-science/how-alphafold-3-is-like-dalle-2-and-other-learnings-1f809010afc7)在蛋白质折叠预测中的巨大进展，以及通过[RFDiffusion](https://www.nature.com/articles/s41586-023-06415-8)在蛋白质设计中的突破。然而，蛋白质功能预测仍然具有挑战性。功能预测对于许多原因至关重要，但特别在生物安全领域尤为重要，因为它有助于预测DNA在测序之前是否会发生孤雌生殖。传统方法如[BLAST](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-6-272)依赖于序列相似性搜索，并未结合任何结构数据。

今天，GNNs（图神经网络）开始通过利用蛋白质的图表示来建模残基之间的关系及其相互作用，在这一领域取得了有意义的进展。它们被认为非常适合蛋白质功能预测、识别小分子或其他蛋白质的结合位点，并基于活性位点几何形状对酶家族进行分类。

在许多例子中：

+   节点被建模为氨基酸残基

+   边缘作为它们之间的相互作用

这种方法背后的合理性在于，图结构具有捕捉序列中远距离但在折叠结构中接近的残基之间长程相互作用的固有能力。这类似于为什么transformer架构对AlphaFold 2如此有帮助，因为它允许在序列中的所有对之间进行并行计算。

为了使图形信息更加密集，每个节点可以通过残基类型、化学性质或进化保守性得分等特征进行丰富。边缘可以选择性地通过化学键类型、三维空间中的接近度以及静电或疏水相互作用等属性进行丰富。

[DeepFRI](https://github.com/flatironinstitute/DeepFRI/tree/master)是一种基于图神经网络（GCN）的方法，用于根据结构预测蛋白质功能。GCN是一种特定类型的GNN，它将卷积的概念（用于CNN）扩展到图数据。

![](../Images/ff53ff24633563fda818cf9677712867.png)

DeepFRI图示：LSTM语言模型，经过约200万Pfam蛋白质序列的预训练，用于提取PDB序列的残基级特征。（B）具有3个图卷积层的GCN，用于学习复杂的结构-功能关系。来自[基于结构的功能预测使用图卷积网络](https://www.biorxiv.org/content/10.1101/786236v1)

在DeepFRI中，每个氨基酸残基都是一个节点，具有如下一些属性：

+   氨基酸类型

+   物理化学性质

+   来自[MSA](https://en.wikipedia.org/wiki/Multiple_sequence_alignment)的进化信息

+   来自预训练LSTM的序列嵌入

+   结构背景，如溶剂可接近性。

每个边被定义为捕获蛋白质结构中氨基酸残基之间的空间关系。如果两个节点（残基）之间的距离低于一定阈值，通常为10 Å，则存在一条边。在这个应用中，边没有属性，它们只是无权重的连接。

图是用节点特征LSTM生成的序列嵌入以及从残基接触图创建的残基特异性特征和边信息进行初始化的。

一旦图被定义，消息传递发生在每个三层的基于邻接的卷积中。节点特征是通过使用图的邻接矩阵从邻居聚合的。堆叠多个GCN层允许嵌入从越来越大的邻域中捕获信息，从直接邻居开始，延伸到邻居的邻居等。

最终的节点嵌入是全局汇集的，用于创建蛋白质级别的嵌入，然后用于将蛋白质分类为层次相关的功能类别（GO术语）。分类是通过将蛋白质级别的嵌入通过具有sigmoid激活函数的全连接层（密集层）进行传递，使用二元交叉熵损失函数进行优化来执行的。分类模型是基于从蛋白质结构（例如来自蛋白质数据银行）和来自UniProt或Gene Ontology等数据库的功能注释的数据进行训练的。

# 总结思考

+   图对于建模许多非线性系统非常有用。

+   GNNs通过同时融合局部和全局信息来捕获传统方法难以建模的关系和模式。

+   GNNs有许多变体，但目前最重要的是图卷积网络和图注意力网络。

+   GNNs可以有效地识别使用监督和无监督方法在洗钱方案中存在的多跳关系。

+   GNNs可以通过整合结构数据改进仅基于序列的蛋白质功能预测工具，如BLAST。这使研究人员能够预测与已知蛋白质具有最小序列相似性的新蛋白质的功能，这是理解生物安全威胁和促进药物发现的关键步骤。

祝福您，如果您喜欢这篇文章，请查看我在[机器学习和生物学](https://medium.com/@meghanheintz/list/understanding-ai-applications-in-bio-for-ml-engineers-7b9e9551bb7f)上的其他文章。
