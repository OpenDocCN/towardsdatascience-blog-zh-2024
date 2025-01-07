# （非）客观机器：探讨机器学习中的历史偏见

> 原文：[`towardsdatascience.com/un-objective-machines-a-look-at-historical-bias-in-machine-learning-da5101d46169?source=collection_archive---------6-----------------------#2024-04-17`](https://towardsdatascience.com/un-objective-machines-a-look-at-historical-bias-in-machine-learning-da5101d46169?source=collection_archive---------6-----------------------#2024-04-17)

## 深入探讨机器学习中的偏见，重点关注历史性（或社会性）偏见。

[](https://medium.com/@greteltan21?source=post_page---byline--da5101d46169--------------------------------)![Gretel Tan](https://medium.com/@greteltan21?source=post_page---byline--da5101d46169--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da5101d46169--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da5101d46169--------------------------------) [Gretel Tan](https://medium.com/@greteltan21?source=post_page---byline--da5101d46169--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da5101d46169--------------------------------) ·10 分钟阅读·2024 年 4 月 17 日

--

人类有偏见。对于那些曾经与偏执的人、不公平的老板或压迫性系统打交道过的人——换句话说，所有人——这并不令人惊讶。因此，我们应该欢迎能够帮助我们做出更客观决策的机器学习模型，尤其是在医疗、执法或就业等关键领域，在这些领域中，偏见的人类可能会做出改变他人生命的判断，严重影响他人的生活……对吧？嗯，不对。尽管我们可能会误以为机器学习模型是客观和理性的，但偏见可能以多种方式内建在模型中。在这篇博文中，我们将重点讨论机器学习（ML）中的历史偏见。

# 什么是偏见？

在我们的日常生活中，当我们提到偏见时，我们通常是指“[基于先入为主的观念或偏见的判断，而非公正的事实评估](https://ainowinstitute.org/publication/ai-now-2017-report-2)”。统计学家也使用“偏见”一词来描述几乎任何可能导致“真实”参数与模型估计之间系统性差异的情况。

由于统计在机器学习模型中的作用很大，这些模型会受到统计偏见的影响。然而，这些模型也是由人类设计的，并且使用由人类生成的数据进行训练，这使得它们容易学习并延续人类的偏见。因此，或许反直觉的是，机器学习模型可能比人类更容易受到偏见的影响，而不是更少。

专家们对于算法偏见的具体数量存在分歧，但至少有 7 种潜在的有害偏见来源（[Suresh & Guttag, 2021](https://dl.acm.org/doi/fullHtml/10.1145/3465416.3483305)），它们在数据分析流程的不同阶段产生：

1.  历史偏见，源于数据生成阶段的世界；

1.  表示偏见，当我们从世界中抽取数据样本时就会产生这种偏见；

1.  测量偏见，我们使用的度量标准或收集的数据可能无法准确反映我们想要测量的内容；

1.  聚合偏见，我们将同一种方法应用于整个数据集，尽管某些子集需要不同的处理方式；

1.  学习偏见，由于我们定义模型的方式，导致了系统性的错误；

1.  评价偏见，我们在对模型表现进行“评分”时，使用的数据实际上并不反映我们希望模型应用的目标人群，最后；

1.  部署偏见，模型没有按照开发者原本预期的方式被使用。

![](img/d0e09d1aefa3353ee704e3e612c63403.png)

由[Hunter Harritt](https://unsplash.com/@hharritt?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄的照片，来源于[Unsplash](https://unsplash.com/photos/red-and-blue-lights-from-tower-steel-wool-photography-Ype9sdOPdYc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

虽然这些都是任何新兴数据科学家应该考虑的重要偏见，今天我将重点讨论历史偏见，它发生在数据分析流程的第一阶段。

> 嘘！想了解更多关于其他类型偏见的信息吗？观看这个有用的视频：

# 历史偏见

与其他类型的偏见不同，历史偏见并非源于机器学习过程，而是源于我们的世界。我们的世界历史上一直充斥着偏见，至今依然如此。因此，即使我们用来训练模型的数据完美地反映了我们所生活的世界，数据也可能捕捉到这些歧视性模式，这就是历史偏见的来源。历史偏见也可能表现在我们社会在追求平等方面取得进展的情况下，但我们的数据未能充分反映这些变化，反而体现了过去的不平等现象。

# 我们为什么要关心？

大多数社会都有反歧视法，旨在保护历史上受压迫的脆弱群体的权利。如果我们不小心，过去的歧视行为可能会因为历史偏见被我们的机器学习模型所学习并延续。随着机器学习模型在我们生活中的普及，从琐事到改变人生的重大决策，这带来了特别隐蔽的威胁——历史性偏见的机器学习模型有可能在前所未有的规模上延续不平等。数据科学家和数学家凯西·奥尼尔（Cathy O’Neil）将这样的模型称为“大规模数学毁灭性武器”（WMDs）——这些模型的工作机制是神秘的，产生的有害结果是受害者无法辩驳的，并且通常会惩罚社会中贫穷和受压迫的人群，同时让那些已经富有的人受益（奥尼尔，2017）。

![](img/3097e830cf16e7342353e058b0142e69.png)

图片来源：[engin akyurt](https://unsplash.com/@enginakyurt?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 来自 [Unsplash](https://unsplash.com/photos/woman-with-hands-tied-l1clu1ZKjSw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

这样的 WMD（大规模毁灭性武器）已经开始影响全球的脆弱群体。尽管我们会认为亚马逊，作为一家从向我们推荐我们从未听说过、但突然渴望的商品中获利的公司，应该已经掌握了机器学习，[研究发现](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8830968/)他们用来扫描简历的算法学会了性别偏见，因为科技领域中女性的数量历史上较少。更令人不寒而栗的是，[预测性警务工具](https://www.technologyreview.com/2021/02/05/1017560/predictive-policing-racist-algorithmic-bias-data-crime-predpol/#:~:text=It%27s%20no%20secret%20that%20predictive,lessen%20bias%20has%20little%20effect.)也被证明存在种族偏见，医疗领域使用的算法也是如此，[法院](https://www.technologyreview.com/2020/07/17/1005396/predictive-policing-algorithms-racist-dismantled-machine-learning-bias-criminal-justice/)的算法也是如此。这类工具的广泛传播显然会带来巨大的影响，特别是因为它们可能成为加固我们社会中根深蒂固的不平等现象的一种手段。我认为，与有偏见的人类相比，这些 WMD 在我们集体努力消除不平等方面构成了更大的障碍，原因有两个：

首先，很难深入了解为什么机器学习（ML）模型会做出某些预测。深度学习似乎是当下的流行词，复杂的神经网络风靡全球。虽然这些模型令人兴奋，因为它们有潜力模拟人类无法理解的复杂现象，但它们被认为是黑盒模型，因为它们的工作原理往往不透明，即便是它们的创造者也未必能完全理解。没有针对历史（以及其他）偏见的集中努力，很难判断它们是否无意中歧视了受保护群体。

其次，我认为，由于历史性偏见模型可能造成的损害规模是前所未有且被忽视的。由于人类需要休息，并需要时间有效地处理信息，单个有偏见的人所能造成的伤害是有限的。然而，仅一个有偏见的机器学习模型就能在几分钟内作出成千上万的歧视性判断，而且不需要休息。更危险的是，许多人还认为机器比人类更客观，这导致对潜在问题模型的监管减少。这让我特别担忧，因为随着像 ChatGPT 这样的大型语言模型取得巨大成功，越来越多的人对将机器学习模型应用于工作流程产生了兴趣，这可能会自动化地促进大规模杀伤性武器在我们社会中的兴起，带来灾难性的后果。

# 我们能做些什么？

尽管偏见模型的影响可能令人担忧，但这并不意味着我们必须完全放弃机器学习模型。人工智能（AI）伦理是一个不断发展的领域，研究人员和活动家们都在为解决问题而努力，旨在消除或至少减少模型中的偏见。值得注意的是，最近有一股推动[FAT](https://ainowinstitute.org/publication/ai-now-2017-report-2)或[FATE](https://www.sciencedirect.com/science/article/pii/S2666920X23000310)人工智能的浪潮——公平、问责、透明和伦理的人工智能，这可能有助于偏见（以及其他伦理问题）的检测和修正。虽然这不是一个全面的清单，但我将简要概述一些减少模型中历史偏见的方法，希望能对你在数据科学之旅中有所帮助。

## 统计学解决方案

由于问题源于现实世界数据中的不成比例结果，为什么不通过使我们收集的数据更加成比例来解决这个问题呢？这是 Suresh, H.和 Guttag, J.（2021）提出的处理历史偏见的统计方法。简单来说，它包括从某些群体收集更多数据，而从其他群体收集较少数据（系统性的过采样或欠采样），从而在我们的训练数据集中得到更为平衡的结果分布。

## 基于模型的解决方案

根据 FATE AI 的目标，模型的可解释性可以被构建进去，使其决策过程更加透明。可解释性使数据科学家能够看到模型做出决策的原因，从而提供机会发现并减轻模型中潜在的历史偏见。在现实世界中，这也意味着，机器歧视的受害者可以挑战之前无法理解的模型所做出的决策，并希望能够促使这些决策重新考虑。这将有助于增加我们模型的信任度。

更技术性地说，针对机器学习模型中的偏见，算法和模型也在不断开发中。[对抗去偏（Adversarial debiasing）](https://dl.acm.org/doi/pdf/10.1145/3278721.3278779) 是一个有趣的解决方案。这类模型本质上由两部分组成：一个预测器，旨在预测结果，比如可聘用性；一个对抗者，试图根据预测的结果预测受保护的属性。就像拳击手在拳台上一样，这两个部分来回交锋，争夺表现得比对方更好，当对抗者无法根据预测结果检测出受保护的属性时，模型就被认为已经去偏。这类模型与没有去偏的模型相比表现得相当好，表明我们在优先考虑公平时不必牺牲性能。[算法](https://dl.acm.org/doi/10.1145/3468264.3468537)也已经被开发出来，用于减少机器学习模型中的偏见，同时保持良好的性能。

## 基于人类的解决方案

最后，也许最为关键的一点是，必须记住，尽管我们的机器正在为我们工作，**我们**才是它们的创造者。数据科学从始至终都与我们——这些了解历史偏见、决定优先考虑公平，并采取措施减轻历史偏见影响的人类——息息相关。我们不应将权力交给我们的创造物，而应在数据分析的各个阶段保持参与。为此，我想为创建跨国第三方机构审计机器学习过程并执行最佳实践的呼声加上一把声音。虽然这不是灵丹妙药，但它是检查我们的机器学习模型是否公平且无偏的一个好方法，也有助于具体化我们对这一事业的承诺。在组织层面，我也为呼吁在数据科学和机器学习团队中增加多样性的声音感到欣慰，因为我相信这将有助于识别和纠正我们数据分析过程中现存的盲点。同时，商业领袖们也需要意识到人工智能的局限性，明智地使用它，而不是以生产力或利润为名滥用它。

作为数据科学家，我们也应对我们的模型负责，并记住它们所掌握的力量。历史上的偏见虽源于现实世界，但我相信机器学习工具同样有潜力帮助我们纠正当前的不公。例如，过去，种族主义或性别歧视的招聘人员可能会因为偏见在将候选人名单交给招聘经理之前就剔除合适的申请者，而一个公平的机器学习模型可能能够高效地找到合适的候选人，忽略他们的受保护属性，从而为以往被忽视的申请者提供宝贵的机会。当然，这不是一项容易的任务，而且本身充满了伦理问题。然而，如果我们的工具真的能塑造我们所生活的世界，为什么不让它们反映出我们想要生活的世界，而不仅仅是现有的世界呢？

# 结论

无论你是一个初学的数据科学家，一个机器学习工程师，还是一个对使用机器学习工具感兴趣的人，我希望这篇博客文章能为你阐明历史偏见如何放大并自动化不平等现象，带来灾难性的影响。虽然机器学习模型和其他人工智能工具让我们的生活变得更加便捷，并且与现代生活不可分割，但我们必须记住，它们并非无懈可击，需要进行充分的监管，确保我们的工具始终是有益的，而不是有害的。

# 想要了解更多吗？

下面是我在学习机器学习中的偏见与伦理学时发现的有用资源：

**视频**

+   [人工智能中的三种偏见 | 机器学习](https://www.youtube.com/watch?v=59bMh59JQDo)

+   [算法偏见与公平性：人工智能速成课程 #18](https://www.youtube.com/watch?v=gV0_raKR2UQ)（也在上面有链接）

+   [我如何与算法中的偏见作斗争 | Joy Buolamwini](https://www.youtube.com/watch?v=UG_X_7g63rY)

+   [数学毁灭武器 | 凯西·奥尼尔 | 谷歌演讲](https://www.youtube.com/watch?v=TQHs8SA1qpk&t=41s)

**书籍**

+   《数学毁灭武器》 作者：凯西·奥尼尔（强烈推荐！）

+   《隐形女性：为男性设计的世界中的数据偏见》 作者：卡罗琳·克里亚多-佩雷斯

+   《人工智能图谱》 作者：凯特·克劳福德

+   《人工智能伦理学》 作者：马克·科克尔伯赫

+   《数据女性主义》 作者：凯瑟琳·迪吉纳齐奥 和 劳伦·F·克莱因

**论文**

+   [克服算法的陷阱与危害：机器学习偏见的分类及缓解方法](https://www.sciencedirect.com/science/article/pii/S0148296322000881)

+   [机器学习中的偏见——它有什么用？](https://arxiv.org/pdf/2004.00686.pdf)

# 参考资料：

AI Now Institute. (2024 年 1 月 10 日). *Ai now 2017 报告*. [`ainowinstitute.org/publication/ai-now-2017-report-2`](https://ainowinstitute.org/publication/ai-now-2017-report-2)

Belenguer, L. (2022). 人工智能偏见：探索歧视性算法决策模型及其在制药行业的可能机器中心解决方案应用。*人工智能与伦理*，*2*(4)，771–787。 [`doi.org/10.1007/s43681-022-00138-8`](https://doi.org/10.1007/s43681-022-00138-8)

Bolukbasi, T., Chang, K.-W., Zou, J., Saligrama, V., & Kalai, A. (2016, July 21). *男人与计算机程序员的关系就像女人与家庭主妇的关系？消除词向量的偏见*。arXiv.org。 [`doi.org/10.48550/arXiv.1607.06520`](https://doi.org/10.48550/arXiv.1607.06520)

Chakraborty, J., Majumder, S., & Menzies, T. (2021). 机器学习软件中的偏见：为什么？怎么做？应该怎么办？*第 29 届 ACM 联合会议：欧洲软件工程会议与软件工程基础研讨会论文集*。 [`doi.org/10.1145/3468264.3468537`](https://doi.org/10.1145/3468264.3468537)

Gutbezahl, J. (2017, June 13). *在分析中避免的 5 种统计偏差*。商业洞察博客。 [`online.hbs.edu/blog/post/types-of-statistical-bias`](https://online.hbs.edu/blog/post/types-of-statistical-bias)

Heaven, W. D. (2023a, June 21). *预测性警务算法存在种族歧视，它们需要被拆除。* MIT 科技评论。 [`www.technologyreview.com/2020/07/17/1005396/predictive-policing-algorithms-racist-dismantled-machine-learning-bias-criminal-justice/`](https://www.technologyreview.com/2020/07/17/1005396/predictive-policing-algorithms-racist-dismantled-machine-learning-bias-criminal-justice/)

Heaven, W. D. (2023b, June 21). *预测性警务依然存在种族歧视——无论它使用何种数据*。MIT 科技评论。 [`www.technologyreview.com/2021/02/05/1017560/predictive-policing-racist-algorithmic-bias-data-crime-predpol/#:~:text=It%27s%20no%20secret%20that%20predictive,lessen%20bias%20has%20little%20effect.`](https://www.technologyreview.com/2021/02/05/1017560/predictive-policing-racist-algorithmic-bias-data-crime-predpol/#:~:text=It%27s%20no%20secret%20that%20predictive,lessen%20bias%20has%20little%20effect.)

Hellström, T., Dignum, V., & Bensch, S. (2020, September 20). *机器学习中的偏见——它有什么用？*。arXiv.org。 [`arxiv.org/abs/2004.00686`](https://arxiv.org/abs/2004.00686)

*人工智能系统中的历史偏见*。澳大利亚人权委员会。（2020 年 11 月 24 日）。 [`humanrights.gov.au/about/news/media-releases/historical-bias-ai-systems#:~:text=Historical%20bias%20arises%20when%20the,by%20women%20was%20even%20worse.`](https://humanrights.gov.au/about/news/media-releases/historical-bias-ai-systems#:~:text=Historical%20bias%20arises%20when%20the,by%20women%20was%20even%20worse.)

Memarian, B., & Doleck, T. (2023). 人工智能（AI）与高等教育中的公平、问责、透明度和伦理（FATE）：一项系统评审。*计算机与教育：人工智能*，*5*，100152\. [`doi.org/10.1016/j.caeai.2023.100152`](https://doi.org/10.1016/j.caeai.2023.100152)

Obermeyer, Z., Powers, B., Vogeli, C., & Mullainathan, S. (2019). 解剖用于管理群体健康的算法中的种族偏见。*科学*，*366*（6464），447–453\. [`doi.org/10.1126/science.aax2342`](https://doi.org/10.1126/science.aax2342)

O'Neil, C. (2017). *数学毁灭武器：大数据如何加剧不平等并威胁民主*。企鹅兰登书屋。

Roselli, D., Matthews, J., & Talagala, N. (2019). 管理人工智能中的偏见。*2019 年全球信息网大会的伴随论文集*。[`doi.org/10.1145/3308560.3317590`](https://doi.org/10.1145/3308560.3317590)

Suresh, H., & Guttag, J. (2021). 理解机器学习生命周期中伤害来源的框架。*算法、机制与优化中的公平性与可访问性*。[`doi.org/10.1145/3465416.3483305`](https://doi.org/10.1145/3465416.3483305)

van Giffen, B., Herhausen, D., & Fahse, T. (2022). 克服算法的陷阱与危机：机器学习偏见及其缓解方法的分类。*商业研究杂志*，*144*，93–106\. [`doi.org/10.1016/j.jbusres.2022.01.076`](https://doi.org/10.1016/j.jbusres.2022.01.076)

Zhang, B. H., Lemoine, B., & Mitchell, M. (2018). 通过对抗学习减轻不必要的偏见。*2018 年 AAAI/ACM 人工智能、伦理与社会会议论文集*。[`doi.org/10.1145/3278721.3278779`](https://doi.org/10.1145/3278721.3278779)
