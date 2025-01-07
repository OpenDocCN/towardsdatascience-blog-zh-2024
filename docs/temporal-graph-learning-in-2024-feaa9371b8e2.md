# 2024年时序图学习

> 原文：[https://towardsdatascience.com/temporal-graph-learning-in-2024-feaa9371b8e2?source=collection_archive---------1-----------------------#2024-01-18](https://towardsdatascience.com/temporal-graph-learning-in-2024-feaa9371b8e2?source=collection_archive---------1-----------------------#2024-01-18)

## 继续探索不断发展的网络

[](https://medium.com/@shenyanghuang1996?source=post_page---byline--feaa9371b8e2--------------------------------)[![Shenyang(Andy) Huang](../Images/ab63c37868db97b19480d536388930c5.png)](https://medium.com/@shenyanghuang1996?source=post_page---byline--feaa9371b8e2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--feaa9371b8e2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--feaa9371b8e2--------------------------------) [Shenyang(Andy) Huang](https://medium.com/@shenyanghuang1996?source=post_page---byline--feaa9371b8e2--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--feaa9371b8e2--------------------------------) ·阅读时间20分钟·2024年1月18日

--

许多复杂的网络随着时间的推移而演化，包括交易网络、交通网络、社交网络等。时序图学习（TGL）是一个快速发展的领域，旨在学习、预测和理解这些不断发展的网络。请参阅我们的[上一篇博客文章](/temporal-graph-learning-in-2023-d28d1640dbf2)，了解时序图学习的介绍以及去年的一些进展。

在2023年，我们看到学术界和工业界对TGL（时序图学习）发展的兴趣显著增加。与去年相比，[NeurIPS 2023时序图学习研讨会](https://sites.google.com/view/tglworkshop-2023/home)的投稿数量增长了三倍，最终有35篇论文被接收。此外，2023年2月启动的[时序图学习读书小组](https://www.cs.mcgill.ca/~shuang43/rg.html)至今已举办了28场研究报告（可以在[YouTube](https://www.youtube.com/@TGL_RG)上找到录音）。目前，已有近200名研究人员注册加入读书小组，我们很高兴看到该主题受到了关注，并且社区极其活跃。

![](../Images/dea62afaf0827b1d3fd11fc1ef12e1ab.png)

图片由作者提供，通过[DALL.E 3](https://openai.com/dall-e-3)生成

*本文由* [*Emanuele Rossi*](https://www.emanuelerossi.co.uk/), [*Michael Galkin*](https://migalkin.github.io/), [*Andrea Cini*](https://andreacini.github.io/) *和* [*Ingo Scholtes*](https://www.ingoscholtes.net/) *共同撰写*。

本文涵盖了时序图学习（TGL）领域的一些激动人心的发展，同时指出了2024年的研究方向。我们还邀请了领先的研究人员分享他们对时序图学习未来发展的看法。本文还旨在为那些希望深入了解时序图学习的读者提供参考资料，并作为学习起点。欢迎在评论区与我们分享你感兴趣的其他进展。如需了解更多图学习的进展，请查看[Michael Galkin](https://mgalkin.medium.com/)的精彩[博文](/graph-geometric-ml-in-2024-where-we-are-and-whats-next-part-i-theory-architectures-3af5d38376e1)。

**目录：**

1.  [时序图基准](#fffa)

1.  [链接预测的创新架构](#4d80)

1.  [时空图和图深度学习在时间序列处理中的应用](#3e08)

1.  [时序知识图谱](#1bf1)

1.  [因果感知的时序图学习](#bfbb)

1.  [可解释的时序图方法](#4876)

1.  [对时序图的对抗攻击](#328d)

1.  [库和基准](#00ae)

1.  [加入时序图学习社区](#31ef)

# 时序图基准

图学习快速发展的驱动力之一是标准化和多样化基准的出现，如[开放图基准](https://ogb.stanford.edu/)（OGB）、[长程图基准](https://openreview.net/forum?id=in7XC5RcjEn)和[GraphWorld](https://blog.research.google/2022/05/graphworld-advances-in-graph.html)。然而，这些基准是为静态图设计的，缺乏进行时序图学习所需的精细时间戳信息。因此，时序图学习的进展受限于缺乏大型高质量数据集，以及缺乏适当的评估方法，导致性能过于乐观。

为了填补这一空白，[时序图基准（TGB）](https://tgb.complexdatalab.com/)最近被提出，包括一系列具有挑战性和多样化的基准数据集，用于对时序图上的机器学习进行真实、可重现和鲁棒的评估。TGB提供了一个[pypi包](https://pypi.org/project/py-tgb/)，可以自动下载和处理来自五个不同领域的九个数据集，数据集包含最多7200万个边和3000万个时间戳。TGB还提供了基于真实应用的标准化评估。

![](../Images/2968f4a125d9de1a57a71b9aa3234353.png)

TGB：时序图学习的挑战性和真实基准。

图片来源：[黄等，2023](https://openreview.net/forum?id=qG7IkQ7IBO)，作者提供。

TGB 包含了链接和节点级任务，并对所有数据集上的最先进 TG 模型进行了广泛的实证比较。第一个任务是动态链接属性预测任务，预测未来某一时刻一对节点之间的链接属性（通常是存在性）。在 TGB 中，这个任务被建模为一个排序问题，并通过过滤后的平均倒数排名（MRR）指标进行评估。结果表明，模型排名在不同数据集上差异显著，数据集中的测试集边缘比例在训练过程中从未出现过。此外，随着更多负样本（不存在的边）用于评估，模型的表现会下降。有趣的是，表现下降的程度在不同模型之间也有所不同。

在动态节点属性预测任务中，目标是预测给定时间节点的属性。更具体地，我们关注节点亲和力预测任务，该任务建模用户对不同物品的偏好随时间的变化。在这里，我们使用前 10 个物品的标准化折扣累积增益（NDCG@10）来比较预测物品的相对顺序与真实值的顺序。有趣的是，我们发现单一启发式方法优于现有的 TG 模型，这突显了未来需要更多专注于节点级任务的模型。[TGB 排行榜](https://tgb.complexdatalab.com/docs/leader_linkprop/)是公开的，欢迎通过[Google 表单](https://docs.google.com/forms/d/e/1FAIpQLSfmvBRgRPeR8bK3ubiwvJd1k26PDI_yVZDRXXRGgU7uSqJWZg/viewform)提交您的模型。更多详情，请参阅[TGB 博客文章](https://medium.com/towards-data-science/temporal-graph-benchmark-bb5cc26fcf11)，由该博客的作者提供。

# 链接预测的新架构

> “在时序图学习领域，链接预测提出了巨大的挑战。学习算法必须超越传统信息传递架构（如 GNNs）通常所具有的有限表现力。此外，它们还必须强调计算效率。一个关键方面是确保在响应链接预测查询时具有低延迟，在动态和复杂的数据环境中平衡模型的表现力与预测速度。” —— 潘力，乔治亚理工学院助理教授

[Longa et al.](https://openreview.net/forum?id=pHCdMat0gI&referrer=%5BTMLR%5D%28%2Fgroup%3Fid%3DTMLR%29)最近的调查提供了关于时间GNN的全面概述。许多方法提出了专门的架构用于动态链接预测，通常旨在捕获重要的结构属性或相关性。例如，[Luo et al.](https://arxiv.org/abs/2209.01084)旨在显式地建模一组节点的联合邻域，用于未来的链接预测，他们设计了**N**eighborhood-**A**ware **T**emporal网络模型（NAT）。传统的图神经网络（GNN）方法无法捕获联合邻域，因为节点嵌入向量是为每个节点独立生成的。在下面的示例中，节点*v*和*w*具有相同的结构上下文，因此在GNN眼中是不可区分的。实际上，由于三元闭合法则，节点*u*和*v*在t₃时的链接更可能形成，而节点*u*和*w*在t₃时的链接则不确定。相比之下，NAT采用了一种新的字典型邻域表示，记录k跳邻域信息，并允许快速构建多个节点的联合邻域的结构特征。字典表示通过一种名为N-cache的高效缓存技术进行维护。N-cache使NAT能够为一批节点对快速构建联合邻域特征，用于快速链接预测。

![](../Images/613d7fba8ce836fcc6dad7fbd2dc2f51.png)

GNN嵌入的节点*v*和w将是相同的。

图片来源：[Luo et al. 2022](https://arxiv.org/abs/2209.01084)

其次，[Yu et al.](https://openreview.net/forum?id=xHNzWHbklj)旨在通过提出DyGFormer，一种基于Transformer的时间图学习架构，捕获长期时间依赖性。给定时间*t*时节点*u*和节点*v*之间的查询，第一步是提取时间*t*之前节点*u*和*v*的历史第一跳交互。这包括邻居的编码、链接、时间间隔以及每个邻居在*u*和*v*中的出现频率。假设如果*u*和*v*在过去有更多共同的历史邻居，那么它们在未来更可能发生交互。将历史交互编码为一个序列后，接下来将其划分为多个补丁，并输入到Transformer中以捕获时间依赖性。

![](../Images/213d11dc93944f290c9be0ba52cd291f.png)

DyGFormer框架

图片来源：[Yu et al. 2023](https://openreview.net/forum?id=xHNzWHbklj)

另一个问题是 *我们真的需要复杂的模型架构来处理时间网络吗？* 在一篇同名论文中，[Cong 等人](https://openreview.net/forum?id=ayPPc0SyLv1)考察了在时间图学习中常用模块（如递归神经网络（RNN）和自注意力机制）的必要性。他们表明，这些模块并不总是动态链接预测所必需的。特别地，他们提出的GraphMixer模型完全基于多层感知机（MLP）和邻居均值池化，并在与包含RNN和自注意力机制的基准模型比较时表现出色。GraphMixer包含三个模块：*链接编码器*总结来自时间链接的信息，*节点编码器*提取节点的信息，*链接分类器*结合以上信息进行预测。有趣的是，[Cong 等人](https://openreview.net/forum?id=ayPPc0SyLv1)认为可训练的时间编码函数可能在训练过程中导致不稳定，因此选择了固定时间编码函数 *z(t) = cos(tω)*，其中固定特征 *捕捉* 了两个时间戳之间的相对差异，如下所示。

![](../Images/f552a8d490b6e445f452de05df940622.png)

固定时间编码函数将 *t* 转换为向量 *cos(tω)*。

x轴表示向量维度，y轴表示余弦值。

图像来源：[Cong 等人 2023](https://openreview.net/forum?id=ayPPc0SyLv1)

最后，[Suresh 等人](https://dl.acm.org/doi/abs/10.1145/3543507.3583476)指出，现有方法在未来链接上独立最大化准确度，忽略了未来链接之间通常存在依赖关系的事实。这种情况在用户从一系列商品中选择购买物品或在社交网络中选择一组用户进行连接时尤为明显。因此，Suresh 等人将动态链接预测视为一个排序问题，并提出了**T**emporal **G**raph网络用于**RANK**ing（TGRank），通过该方法学习对候选项进行排名。TGRank的流程图如下所示。任务查询现在包含一个中心节点 *s*（在示例中）以及一组候选节点（子图中的所有其他节点），目标是对最可能的候选项进行排名，作为节点 *s* 的目标。为此，TGRank遵循三个步骤。首先，节点 *s* 被与其他节点标注不同。然后，使用GNN（图神经网络）将中心节点标签传播到每个排序候选项。这个参数化的标签传播步骤聚合了时间戳、重复性以及沿着网络从中心节点到所有候选项的历史交互特征，并为链接预测提供了明显更强的表达能力。最后，使用基于列表的损失函数来联合优化候选项之间的排名。从经验上看，使用列表式排名损失后，像[TGN](https://arxiv.org/abs/2006.10637)和[TGAT](https://arxiv.org/abs/2002.07962)等流行模型的表现也优于其原始设置中的二分类损失。

![](../Images/b452c8a5094427bee68c6aa119670a18.png)

TGRank的流程图。中心节点是节点*s*。

图像来源：[Suresh et al. 2023](https://dl.acm.org/doi/abs/10.1145/3543507.3583476)

# 时空图与图深度学习在时间序列处理中的应用

> “将我们的预测主要基于最相关的观察结果是明智的，但并不总是简单的，因为相关的数据关系常常隐藏在显而易见的地方。揭示这些关系是一个引人入胜的挑战，尤其是当它们是动态的，或者涉及超过两个实体时。” — Daniele Zambon，瑞士人工智能实验室IDSIA的博士后

在时序图学习社区中，*时空图*这一术语通常用来表示具有固定拓扑结构和随时间变化的节点特征的图，通常是在离散的时间步长上，这些时间步长对应于定期采样的观测数据。最近，处理具有这种结构的数据问题正在从不同的角度进行考虑，即将动态节点特征视为时间序列，而将边视为观测序列之间的函数依赖关系（[Cini et al. 2023](https://arxiv.org/abs/2310.15978)，[Ming et al. 2023](https://arxiv.org/abs/2307.03759)）。从这一角度来看，时空图表示法允许通过利用图神经网络的架构偏向来处理关联时间序列集合，这一视角与本文讨论的许多其他设定有显著的偏离。

![](../Images/1f7d08d5aa14384b4ae4e0d9bcd53c9e.png)

具有关联侧信息的时间序列

图像来源：[Cini et al. 2023](https://arxiv.org/abs/2310.15978)，作者提供。

这样的关联时间序列集可以通过传感器生成，无论是物理传感器还是非物理传感器。例如，在交通领域，时间序列可能对应于传感器读取的数据，这些传感器测量的是在交叉口通过的车辆数量。每个传感器将对应一个不同的节点，邻接矩阵可以通过与仅通过路段直接连接的传感器连接边来获得。除了交通预测（[Li et al. 2018](https://openreview.net/forum?id=SJiHXGWAZ)，[Yu et al. 2018](https://www.ijcai.org/proceedings/2018/505)），这些表示法还广泛应用于时间序列处理的各类应用中，涵盖了从空气质量监测（[Chen et al. 2021](https://dl.acm.org/doi/10.1145/3631713)）和能源分析（[Cini et al. 2023](https://ojs.aaai.org/index.php/AAAI/article/view/25880)）到生物医学数据处理（[Zhang et al. 2022](https://openreview.net/forum?id=Kwm8I7dU-l5)）和金融时间序列分析（[Matsunaga et al. 2019](https://arxiv.org/abs/1909.10660)）等各个领域。

![](../Images/d48b917c91b2d348893a61a1f6ad501c.png)

来自交通领域的关联时间序列示例。

图像来源：[ECML PKDD 2023教程](https://gmlg.ch/tutorials/graph-based-processing/ecml-2023)，作者提供。

为了处理这些数据，标准的消息传递框架需要更新，以处理来自每个节点邻域的观测序列。这可以通过替换适当的操作符（即消息和更新函数）为能够沿时间维度处理数据的操作符来轻松实现，例如递归单元（[Seo et al. 2018](https://arxiv.org/abs/1612.07659)），时空卷积（[Wu et al. 2019](https://www.ijcai.org/proceedings/2019/0264)）和基于注意力的架构（[Marisca et al. 2022](https://papers.nips.cc/paper_files/paper/2022/hash/cf70320e93c08b39b1b29a348097a376-Abstract-Conference.html)）。由此产生的模型被称为时空图神经网络（STGNNs），并且已经有大量研究致力于提出有效的架构（见 [Ming et al. 2023](https://arxiv.org/abs/2307.03759)）。使用STGNNs的主要优势之一是可以使用相同的参数集来预测任何子集的时间序列，同时在处理过程中考虑到依赖关系。这比标准的多元时间序列预测模型具有巨大的优势，因为后者通常需要单独预测每个时间序列或放弃参数共享。也可以考虑使用混合型STGNNs，带有一些特定于时间序列的参数，正如我们在[a recent NeurIPS paper](https://openreview.net/forum?id=x2PH6q32LR)中所展示的，往往优于那些共享所有参数的模型。除了模型架构之外，基于图的表示，如[Zambon et al.](https://openreview.net/forum?id=SFeKNSxect)所示，也可以通过将时空相关性分析聚焦于相互连接的节点，帮助评估预测模型的最优性。

![](../Images/5e4d1ef2c085ad6c04f2fe38a418f15d.png)

一个时空图神经网络。

作者提供的图片。

该领域固有的几个挑战，包括处理[不规则采样的时间序列](https://arxiv.org/abs/2012.00168)和缺失数据；实际上，这两种现象在处理实际的网络物理系统时非常常见。幸运的是，图模型在这一背景下也很有用，例如可以[基于相邻传感器的观察结果来条件化重建](https://openreview.net/forum?id=kOu3-S3wJ7)。可扩展性是另一个主要问题，因为与标准的图神经网络不同，消息传递通常是针对每个时间步进行的。现有的可扩展架构主要依赖于[子采样](https://assets.amazon.science/50/90/df9385f840c7b0363febf882a6ad/spatio-temporal-multi-graph-networks-fordemand-forecasting-in-online-marketplaces.pdf)和/或[预计算的节点特征](https://ojs.aaai.org/index.php/AAAI/article/view/25880)。当没有先验关系信息时，挑战就变成了如何直接从时间序列中学习潜在图。例如，问题已经被通过直接学习邻接矩阵（例如，[Wu 等，2019](https://www.ijcai.org/proceedings/2019/0264)）或在概率框架下，依赖于[重参数化技巧](https://proceedings.mlr.press/v80/kipf18a)和[基于评分的估计器](https://www.jmlr.org/papers/v24/22-1154.html)来解决。

![](../Images/29f300758be7f7f4f941ad96bcf14338.png)

可扩展的时空图神经网络

图片来源：[Cini 等，2023](https://ojs.aaai.org/index.php/AAAI/article/view/25880)。由作者提供

由于去年博客中没有涉及这一主题，因此本文的目的是简要概述相关设置以及可以建模的问题。目前有很多方向正在探索，包括[图状态空间模型](https://arxiv.org/abs/2301.01741)、[图卡尔曼滤波器](https://arxiv.org/abs/2303.12021)、[基于扩散的模型](https://dl.acm.org/doi/10.1145/3589132.3625614)以及[连续时空模型](https://openreview.net/forum?id=Oq5XKRVYpQ)。如果你有兴趣了解更多内容和/或在实践中使用这些模型，我们最近发布了一篇关于该主题的综合[教程](https://arxiv.org/abs/2310.15978)论文。你还可以查看我们的库[Torch Spatiotemporal (tsl)](https://torch-spatiotemporal.readthedocs.io/en/latest/)，它用于构建基于图的时间序列处理管道。

+   教程论文：[图深度学习在时间序列预测中的应用](https://arxiv.org/abs/2310.15978)

+   库：[Torch Spatiotemporal (tsl)](https://torch-spatiotemporal.readthedocs.io/en/latest/)

# 时序知识图

令人惊讶的是，今年在顶级机器学习会议上有很少的时序KG论文：**TILP**（[Xiong 等，2023](https://openreview.net/forum?id=_X12NmQKvX)）探讨了与神经方法竞争的时序规则学习，以及[Chen和Wang](https://openreview.net/forum?id=AtHJ7TLheF)的理论研究，旨在衡量时序GNN的表现力。事实上，最有趣的（对我来说）这类论文是在NeurIPS’23的TGL研讨会上找到的（这也是你应该关注该会议的一个原因！），例如，[Pop和Kostylev](https://openreview.net/forum?id=9B8ocBg4VJ)预测未来时间间隔，或[Pan等人](https://openreview.net/forum?id=UMokRwWfLW)在标准基准数据集中识别数据泄露。最后，我想提到[Ning等人](https://arxiv.org/abs/2306.11443)的**统一城市知识图谱（UUKG）**，它为时序KG数据集提供了一个全新的视角，真正具有实际意义并且有应用场景——建模城市中的交通流。

UUKG展示了时序知识图谱（KG）社区萎缩的最大问题——缺乏实际重要的任务和数据集，在这些任务中可以展示数据建模范式在现实世界任务中的实用性。也就是说，与几何深度学习在生物学或材料科学中的成功相比（或者与大型语言模型相比，但那是另一个话题），在10年历史的KG嵌入基准上增加1%的MRR/Hits@10如今显得毫无意义。希望未来我们能看到更多类似UUKG的实用数据集。

也许时序KG可能产生影响的另一个相关领域是异构图（通常具有类型化边缘），这些图在工业界的使用频率更高。例如，最近的[RelBench](https://relbench.stanford.edu/)（关系深度学习基准）提出了一个基于关系数据库的时序预测问题，这个问题可以轻松转化为KG或超图。

# 因果感知的时序图学习

> “爱因斯坦说，时间的箭头只朝一个方向飞去。[……]在我们当中，有谁愿意放弃重温那个曾让我们第一次体会到爱、狂喜，或做出一个永远改变我们未来的选择的日子或时刻，抹去可能曾有过的一种生活？这样的机会是很少有的。”——格雷格·艾尔斯，《安静的游戏》

时间图学习之所以有趣，部分原因是它——根据手头的数据——需要不同的视角。例如，考虑具有高分辨率（可能是连续）时间戳的时间图数据。在这种数据中，利用快照图序列的离散时间图学习技术需要对时间进行粗粒化，其中每个快照由在某个时间间隔内发生的边缘组成。这种粗粒化使得可以将（静态）图学习技术推广到时间序列数据。但它也带来了一个主要问题：每个快照都会丢弃关于边缘发生的时间顺序的信息，而时间顺序是*因果*或*时间尊重路径*的基础[(Kempe et al. 2000)](https://www.sciencedirect.com/science/article/pii/S0022000002918295)。像静态图中的路径一样，时间尊重路径很重要，因为它们告诉我们哪些节点可以间接地因果影响彼此。下面，我们通过一个简单的时间图进行说明，其中包含两条无向边*(a,b)*和*(b,c)*，分别发生在时间*t₁*和*t₂*。如果*(a,b)*发生在*(b,c)*之前，则*a*可以通过一条时间尊重路径（紫色路径）因果影响*c*，该路径经过*b*。如果边的时间顺序颠倒，*a*则无法因果影响*c*，因为任何影响必须在时间上逆向传播。请注意，*a*到*c*的影响是有方向的，这是由于时间的箭头方向，尽管这两条边都是无向的。此外，虽然在静态的、时间聚合图中，边*(a,b)*和*(b,c)*表示从*a*通过*b*到*c*的传递路径（紫色）以及反向路径（橙色），但在时间图中并非如此。

![](../Images/1c51adfe45fe86ccf53bc1661740a9a0.png)

从节点a到节点c的时间尊重路径。

图片来自作者。

多项研究表明——由于时间的箭头——时间图的*因果拓扑*，即哪些节点可以通过时间尊重路径因果影响彼此，与静态图相比有很大不同，这对流行病传播（[Pfitzner et al. 2013](https://link.aps.org/doi/10.1103/PhysRevLett.110.198701)）、扩散速度（[Scholtes et al. 2014](https://www.nature.com/articles/ncomms6024)）、节点中心性（[Rosvall et al. 2014](https://www.nature.com/articles/ncomms5630)）或社区检测（[Lambiotte et al. 2019](https://www.nature.com/articles/s41567-019-0459-y)）具有重要的影响。今年提出的进展表明，可以基于模型实现这一目标，这些模型将常用的时间图静态表示进行了一般化。考虑一个加权的时间聚合图，其中一条（有向）边*(a,b)*的权重为5，表示*(a,b)*在时间图中出现了五次。下图中2面板的底部展示了这种加权的时间聚合图。

![](../Images/c823d73a9ca3e993e9903aebfcf14986.png)

预测时间图中节点时间中心性的流程图。

图片来源：[Heeg 等人](https://arxiv.org/abs/2310.15865)，作者提供

时间图中的每条边都是长度为一的时间遵循路径。因此，经过加权时间聚合的图对应于时间图因果拓扑的一个一阶模型，能够捕捉时间遵循的长度为一的路径。它忽略了边的时间顺序，因为我们仅计算每条边出现的频率。通过图形变换，我们可以将这一思想推广到因果感知模型，以促进时间图的学习：我们只需将一阶图中的边替换为二阶图中的节点，即将边*(a,b)*和*(b,c)*转换为节点*“a→b”*和*“b→c”*。在生成的二阶图中（参见图2面板顶部图示），我们可以使用边表示长度为二的时间遵循路径，即边*(a→b, b→c)*表示*a*通过*b*因果地影响*c*。然而，边的反向顺序不被包含。如果边按相反顺序出现，我们就不会包含*(a→b, b→c)*。重要的是，这样的二阶图对边的时间顺序敏感，而一阶图则不敏感！在[Scholtes, 2017](https://dl.acm.org/doi/10.1145/3097983.3098145)中，这一思想被推广到更高阶，从而产生了**k阶德·布鲁因图模型，用于时间图的因果拓扑**。

[Qarkaxhija 等人](https://proceedings.mlr.press/v198/qarkaxhija22a/qarkaxhija22a.pdf)已证明，在此类高阶德·布鲁因图中进行神经消息传递能够产生一种**因果感知图神经网络架构**，用于时间图的建模。基于这些**德·布鲁因图神经网络（DBGNN）**，在今年的 TGL 研讨会上，[Heeg 和 Scholtes](https://arxiv.org/abs/2310.15865)提出了解决预测节点时间介入度和紧密度中心性的挑战。由于它们受到时间箭头的影响，时间节点的中心性可能与静态中心性大相径庭。而且，计算这些中心性是非常昂贵的！这个问题通过在时间图的第一个时间间隔上训练一个 DBGNN 模型来解决，然后使用训练好的模型预测其余数据中的时间中心性。整体方法如上图所示。实证结果令人鼓舞，展示了因果感知图学习的潜力。我们也希望在 2024 年，社区能更多关注在时间图上学习因果结构的问题。

对因果感知时序图学习感兴趣吗？那么有好消息！上述技术已在开源库 [pathpyG](https://www.pathpy.net/) 中实现，该库建立在 [PyG](https://pyg.org/) 之上。这里有一个 [入门视频和教程](https://www.pathpy.net/dev/tutorial/) 可供参考。此外，还有一场 [在时序图阅读小组中进行的讲座录音](https://youtu.be/IezbzMMp9QM?si=1_B8rMag5Gpk0Vqx)，为您提供了对基础研究的深入介绍。

# 可解释的时序图方法

> “现实世界中最重要的图结构数据本质上是时序性的。可解释的时序图模型有潜力揭示长期存在的问题，提供有效的策略和知识，以便用于时序预测，进而从深度学习模型中提取洞察，并辅助科学发现与预测。” — Rex Ying，耶鲁大学助理教授

2023年首次提出了时序GNN方法的可解释性。解释器在高风险应用中非常重要，比如欺诈检测和医疗保健中的疾病进展预测。[Xia 等人](https://openreview.net/forum?id=BR_ZhvcYbGJ)提出了 T-GNNExplainer，作为首个为时序图模型设计的解释器。T-GNNExplainer 是模型无关的，它从一组候选事件中找到最能解释模型预测的重要事件。Xia 等人将识别解释事件子集的问题视为一个组合优化问题，通过在给定大小的时序图子集内进行搜索来解决。为了解决这个问题，T-GNNExplainer采用了探索者-导航者框架。导航者通过多个目标事件的训练，捕捉事件间的归纳相关性，而探索者则基于蒙特卡洛树搜索寻找特定的事件组合，包括节点选择、节点扩展、奖励模拟和反向传播。哪些事件被剪枝由导航者推断得出。下图展示了 T-GNNExplainer 的框架。

![](../Images/2e8097a22cf7af9ee4686e1af17cf30e.png)

T-GNNExplainer 框架

图像来源：[Xia 等人，2023](https://openreview.net/forum?id=BR_ZhvcYbGJ)

最近，[Chen 等人](https://arxiv.org/abs/2310.19324)认为，要为时序图事件形成人类可理解的解释，解释需要是与预测事件在时间上接近且在空间上相邻的事件，称为*紧凑解释*。利用*时序模式*，即时序图中反复出现的子结构，是为时序图形成紧凑解释的自然解决方案。这是因为时序模式是引导未来事件生成过程的重要因素。在以下示例中，优先附着规则（通常在电子商务中促进影响者效应）和三分闭合规则（解释社交网络中的共同朋友规则）形成了紧凑且可信的解释。

![](../Images/15c2737fed1e3529dffee8d9bffab28f.png)

*连贯的解释*在时间上是近似的，并且在空间上是相邻的。

图片来源：[Chen et al. 2023](https://arxiv.org/abs/2310.19324)

因此，[Chen et al.](https://arxiv.org/abs/2310.19324)提出了TempME，一种基于时间模式的解释器，用于识别重要的时间模式以解释时间GNN。TempME的框架如下图所示。首先，提取围绕目标预测的时间模式。然后，这些候选模式通过*模式编码器*进行编码，该编码器利用事件匿名化、信息传递和图池化生成每个模式的嵌入。接着，基于信息瓶颈原则，TempME为这些模式分配重要性评分，评分由解释准确性和信息压缩共同约束。最后，通过从伯努利分布中采样，基于重要性评分构建解释。

![](../Images/9baa6fe57cec7a96c033167b51ca3dec.png)

TempME的框架，边缘上的数字表示时间顺序。

图片致谢：[Chen et al. 2023](https://arxiv.org/abs/2310.19324)

# 时间图上的对抗性攻击

> “随着时间图被用于重要任务，如欺诈检测，理解其在对抗性攻击下的失败点变得尤为重要。理解和量化这些盲点是创建强健可靠的时间GNN模型的第一步。这类努力对于确保这些模型在行业中的应用至关重要。” - 佐治亚理工学院助理教授 Srijan Kumar

对抗性攻击可以针对客户的隐私或影响金融系统中的关键决策。随着时间图模型被应用于推荐系统和欺诈检测等应用领域，研究攻击并设计针对TG模型的防御机制变得尤为重要。[Chen et al.](https://arxiv.org/abs/1911.10561)提出了针对离散时间动态图的第一个对抗性攻击，称为时间感知梯度攻击（TGA）。TGA通过改变原始网络中的少量链接，且通过梯度信息决定与预测链接最相关的链接。

![](../Images/ae39ba65201cfb39a0605af3c6e55601.png)

时间动态感知扰动攻击概述。

攻击者可以在避免检测的情况下翻转模型的预测结果。

图片来源：[Sharma et al. 2023](https://dl.acm.org/doi/pdf/10.1145/3580305.3599517)

最近，[Sharma et al.](https://dl.acm.org/doi/abs/10.1145/3580305.3599517)提出有效的时间图攻击必须同时优化边缘和时间扰动，同时保持原始图演化的完整性。这是因为，剧烈的攻击会扰乱图的演化，容易被异常检测方法发现。因此，Sharma et al. 将*演化保持攻击*定义为**T**emporal **D**ynamics-**A**ware **P**erturbation（TDAP）约束。TDAP主张，在给定时间戳下添加的扰动，只能是相对于前一个时间戳的实际变化数量的一小部分。TDAP被证明可以保持图的结构和嵌入空间的变化率。下图展示了TDAP的概览。Sharma et al. 随后提出了一种新型攻击方法——**T**emporal **D**ynamics-aware **P**rojected **G**radient **D**escent（TD-PGD），该方法在TDAP约束下具有封闭形式的投影算子。还提出了TD-PGD的在线版本，在该版本中，扰动可以实时添加。最后，实验证明，TDAP约束的扰动确实可以避开基于嵌入的异常检测方法的攻击。

# 库和基准测试

2023年，时间图学习领域推动了标准化库和基准测试的重大进展。[TGB](https://tgb.complexdatalab.com/)为节点和链路级任务提供了一个开放且标准化的基准。[DyGLib](https://github.com/yule-BUAA/DyGLib)是一个库，包含标准化的训练流程、可扩展的编码接口和全面的时间图学习评估策略。[Zhang et al.](https://openreview.net/forum?id=zr1e15kczE)提出了一个新颖的概念——[*Live Graph Lab*](https://livegraphlab.github.io/)，根据区块链交易提供实时图数据。Live Graph Lab提供了一套工具，用于下载、解析、清理和分析区块链交易，向研究人员提供随时提取最新时间图数据的机会，以供分析或测试。[Zhou et al.](https://dl.acm.org/doi/10.1145/3581784.3607056)注意到，TG模型中使用的节点内存更适合小批量训练，并且需要在所有训练器中同步维护。因此，他们提出了[DistTGL](https://github.com/amazon-science/disttgl)，一种高效且可扩展的解决方案，用于在分布式GPU集群上训练基于内存的TGNN。启用多GPU训练大规模数据集是部署TG模型在大数据集上的一个重要方向。我们呈现了一个关于时间图学习的库和基准测试的更新列表：

+   TGB [网站](https://tgb.complexdatalab.com/) 和 [pypi 安装](https://pypi.org/project/py-tgb/)

+   DyGLib [Github](https://github.com/yule-BUAA/DyGLib)

+   Live Graph Lab [网站](https://livegraphlab.github.io/) 和 [数据集](https://zenodo.org/records/8267012)

+   DistTGL [Github](https://github.com/amazon-science/disttgl)

+   Torch Spatiotemporal (TSL) [网站](https://torch-spatiotemporal.readthedocs.io/en/latest/) 和 [Github](https://github.com/TorchSpatiotemporal/tsl)

+   pathpyG [网站](https://www.pathpy.net/dev/) 和 [Github](https://github.com/pathpy/pathpyG)

+   RelBench [网站](https://relbench.stanford.edu/) 和 [Github](https://github.com/snap-stanford/relbench)

+   [Pytorch Geometric Temporal](https://pytorch-geometric-temporal.readthedocs.io/en/latest/modules/root.html)

+   TGL [论文](https://assets.amazon.science/88/aa/0323050941cab9403763ffdde180/tgl-a-general-framework-for-temporal-gnn-training-on-billion-scale-graphs-scalable-data-science.pdf) 和 [Github](https://github.com/amazon-science/tgl)

+   DGB [pypi 安装](https://pypi.org/project/dgb/), [论文](https://openreview.net/forum?id=1GVpwr2Tfdg) 和 [数据集](https://zenodo.org/records/7213796#.Y8QicOzMJB2)

+   Chartalist Blockchain Network [网站](https://www.chartalist.org/), [论文](https://openreview.net/forum?id=10iA3OowAV3) 和 [Github](https://github.com/cakcora/chartalist)

+   TKG预测评估 [论文](https://github.com/JuliaGast/JuliaGast.github.io/blob/master/files/gastinger_evaluation_paper_TKG.pdf) 和 [Github](https://github.com/nec-research/TKG-Forecasting-Evaluation)

# 加入时序图学习社区

要加入快速发展的 TG 社区，请点击 [此处](https://forms.gle/wausEzsgm7DvyLPs7) 注册每周的时序图阅读小组。访问 [阅读小组网站](https://www.cs.mcgill.ca/~shuang43/rg.html) 和 [Youtube](https://www.youtube.com/@TGL_RG) 查看所有即将举办和过去的录播讲座。我们还在网站上提供了 TG slack 的邀请链接（每月更新）。[2023年NeurIPS时序图学习研讨会](https://sites.google.com/view/tglworkshop-2023/home)第二版包括了激动人心的 [35篇海报](https://openreview.net/group?id=NeurIPS.cc%2F2023%2FWorkshop%2FTGL#tab-accept-long-paper)，展示了时序图领域的前沿研究。你还可以在 [NeurIPS虚拟网站](https://neurips.cc/virtual/2023/workshop/66544) 上找到讲座录音（一个月后公开）。如果你想成为下一版研讨会的审稿人，可以 [在此](https://forms.gle/4UuiTUDEqkvQ4pHC8) 注册。要了解更多关于本文作者的研究，请访问我们的个人网站：[沈阳（Andy）黄](https://cs.mcgill.ca/~shuang43/)，[Emanuele Rossi](https://www.emanuelerossi.co.uk/)，[Michael Galkin](https://migalkin.github.io/)，[Andrea Cini](https://andreacini.github.io/) 和 [Ingo Scholtes](https://www.ingoscholtes.net/)。期待在阅读小组或下次研讨会中见到你！

![](../Images/63a42f043a6b9654d313b283180a13ff.png)

NeurIPS 2023 时序图学习研讨会的 logo。

图片来自作者。
