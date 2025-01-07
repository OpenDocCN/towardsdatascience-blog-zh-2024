# 2024年机器学习工程师面试生存指南

> 原文：[https://towardsdatascience.com/2024-survival-guide-for-machine-learning-engineer-interviews-e74eccef4645?source=collection_archive---------1-----------------------#2024-12-24](https://towardsdatascience.com/2024-survival-guide-for-machine-learning-engineer-interviews-e74eccef4645?source=collection_archive---------1-----------------------#2024-12-24)

## 面向初级MLE的年终面试准备总结

[](https://mengliuz.medium.com/?source=post_page---byline--e74eccef4645--------------------------------)[![Mengliu Zhao](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--e74eccef4645--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e74eccef4645--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e74eccef4645--------------------------------) [Mengliu Zhao](https://mengliuz.medium.com/?source=post_page---byline--e74eccef4645--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e74eccef4645--------------------------------) ·14分钟阅读·2024年12月24日

--

找工作真不容易！

在今天的市场中，寻求与机器学习相关职位的工作比以往任何时候都更加复杂。尽管公开报告宣称机器学习工程师（MLE）的[职位需求](https://365datascience.com/career-advice/career-guides/machine-learning-engineer-skills/?utm_medium=social&utm_source=linktree&utm_campaign=linktree-machine-learning-engineer-job-market&utm_content=post#h_56513925515981716376290404)正在快速增长，但事实上，市场在过去几年已经转变为雇主市场。在2020年、2022年和2024年寻找机器学习工作可能会是完全不同的体验。更重要的是，一些因素会导致在地理位置、领域以及职位级别之间求职难度的差异：

+   **地理位置**：根据[AI领域人才报告](https://www.peopleinai.com/blog/2024-job-market-for-machine-learning-engineers)，2024年北美招聘最多的城市包括湾区、纽约市、西雅图等。如果我们通过专业人士与职位的比例来评估找工作成功的概率，那么湾区的成功率为3.6%。然而，如果你住在洛杉矶或多伦多，需求量则大幅降低，导致成功率下降至1.4%，仅为湾区的40%。如果你住在其他城市，成功率可能还会更低。

+   **领域：** 不同领域的机器学习工程师所需的技能集差异很大。以深度学习模型为例，计算机视觉（CV）通常使用像ResNet、Yolo等模型，而自然语言处理（NLP）则涉及理解RNN、LSTM、GRU和Transformers；欺诈检测使用SpinalNet；大语言模型（LLM）注重Llama和GPT的知识；推荐系统则包括对Word2Vec和Item2Vec的理解。然而，并非所有领域都招聘相同数量的机器学习工程师。如果我们查找[来自Evidently AI的系统设计案例研究](https://www.evidentlyai.com/ml-system-design)中的标签，CV对应30个案例，欺诈检测对应29个，NLP对应48个，LLM对应81个，而推荐系统对应82个。推荐系统的用例几乎是计算机视觉的2.7倍。这个比例可能存在偏差，并不完全反映就业市场的实际情况，但它显示了在推荐系统领域对于机器学习工程师的更多机会。

+   **资历要求。** 根据[365 DataScience报告](https://365datascience.com/career-advice/career-guides/machine-learning-engineer-skills/)，尽管72%的职位没有明确说明所需的工作年限（YOE），但具有2-4年经验的工程师需求最高。这意味着你可能会面临更大的挑战来获得入门级工作机会。（大多数需求集中在2-4年经验的工程师身上，可能是因为五年前MLE还不是一个典型职位，[正如这篇博客文章中所解释的](https://blog.qwasar.io/blog/why-its-so-difficult-to-find-senior-machine-learning-engineers)。）

本文将总结MLE面试准备的材料和策略。但请记住，这只是我收集到的经验列表，可能适用于你的背景或即将到来的面试，也可能不适用。希望本文能够为你职业发展的旅程提供一些启示或指导。

![](../Images/f8ffa54e66c37a6aa8ec9db27b7e6032.png)

图片来源：[https://pxhere.com/en/photo/655321](https://pxhere.com/en/photo/655321)

## 面试前 — 该期待什么？

面试过程可能会被延长、痛苦且孤独。当你开始申请工作时，有些事情你需要提前思考并做出相应的计划：

+   面试时间表

+   职位类型

+   公司类型

+   领域

+   地点

**面试时间表。** 每个公司的时间表不同。对于规模较小的公司（<500人），通常处于种子轮或A/B轮阶段，面试时间通常较短，你可以预期在几周内完成申请过程。然而，对于规模较大的公司（> 1万人或FAANG公司），从申请提交到最终录用阶段，可能需要3到6个月，甚至更长时间。

**职位类型。** 我建议参考 [Chip Huyen的《机器学习面试》书](https://huyenchip.com/ml-interviews-book/contents/1.1-different-ml-roles.html) 以获取有关不同机器学习相关职位的详细讨论。MLE的职位可能有不同的名称，例如机器学习工程师、机器学习科学家、深度学习工程师、机器学习开发者、应用机器学习科学家、数据科学家等。归根结底，**典型的机器学习工程师角色是端到端的**，这意味着你将从与产品经理（有时是客户）沟通并定义机器学习问题开始，准备数据集、设计和训练模型、定义评估指标、服务和扩展模型，并持续改进结果。有时公司会混淆职称，例如将MLE与ML Ops混在一起。重要的是职责，而不是职称。

**公司类型。** 再次， [Chip Huyen的《机器学习面试》书](https://huyenchip.com/ml-interviews-book/contents/1.1-different-ml-roles.html)讨论了应用型公司与工具型公司、大公司与初创公司、B2B与B2C公司的差异。此外，还值得考虑公司是上市公司还是私有公司，是否以销售为驱动或以产品为导向。这些概念不容忽视，特别是如果你正在寻找第一份行业工作，因为它们将构建你“职业的眼镜”，我们将在面试策略部分详细讨论。

**领域。** 如在引言部分所述，MLE的工作可能涉及不同的领域，如推荐系统和大型语言模型（LLMs），你需要花时间准备基础知识。你需要确定一个或两个最感兴趣的领域，以最大化你的机会；然而，准备所有不同的领域几乎是不可能的，因为这会分散你的精力和注意力，使你准备不足。

**地点。** 除了上述所有因素外，地点是一个重要的因素。如果你住在像湾区或纽约这样的高需求地区，寻找MLE职位会更容易。如果无法搬迁，你可能需要为获取令人满意的工作邀请计划更长的时间线；然而，如果你保持开放的搬迁选项，申请高需求地区的职位可能是一个好主意。

## 面试过程中 — 如何准备？

一旦你开始申请并开始获得面试机会，有几件事是你需要搜索和准备的：

+   面试形式

+   推荐、网络

+   LinkedIn或作品集

+   面试资源和材料

+   策略：规划、跟踪、演变、提示、评估你的水平、戴上“职业的眼镜”、找到面试伙伴、注意警示信号

+   接受offer

**面试形式**

面试的形式在不同公司之间有所不同。没有两家公司对 MLE 职位的面试形式完全相同，因此你必须提前做足“功课”，研究面试的形式。例如，即使是 FAANG 公司，苹果也以其创业型的面试形式著称，这种形式在不同团队之间可能会有所不同。另一方面，Meta 往往在公司层面上有一个一致的面试形式，通常包括一到两轮 LeetCode 编程题和 ML 系统设计题。通常情况下，招聘人员会提供关于大公司面试形式的详细信息，这样你就不会感到意外。然而，小公司可能会有更少的结构化流程，且更频繁地变化。有时候，小公司会用其他编程题代替 LeetCode 编程题，且只会轻度触及建模部分，而不是进行完整的 ML 系统设计环节。你应该在像 Prepfully、Glassdoor、Interview Query 或其他付费网站上查找关于面试形式和流程的信息，以便更好地提前准备。最后，**不要被面试形式所限制**，因为它并不是一个标准化测试——在技术面试中可能会涉及行为元素，而在招聘经理的面试环节中可能会有技术性问题。**做好准备，但要灵活，并准备好应对意外。**

**推荐和网络建设**

许多网络文章会夸大推荐信的好处，但拥有推荐信只是让你更快通过招聘人员筛选，直接进入第二轮面试（通常是招聘经理面试）。除了拥有推荐信，面对面的网络建设几乎同样重要，例如，利用黑客马拉松的机会与公司交流，参加现场招聘会，参与由你感兴趣的公司赞助的线下志愿者活动。请不要*依赖推荐信或网络建设来找工作，而是将它们作为增加你获得更多招聘人员和招聘经理面试机会的机会，以最大化面试效率*。

**LinkedIn 或 个人作品集。** LinkedIn 和个人作品集只是广告工具，帮助招聘人员了解你在简历中的文字信息之外的内容。作为一名初级 MLE，将课程项目和 Kaggle 挑战放在你的 GitHub 仓库中，能够展示更多相关经验，这会有所帮助；然而，随着你职位的晋升，玩具项目的意义就不那么大了，但在大型开源项目中的 PR、深刻的文章和分析、SOTA 研究或工具箱的教程，将使你从其他候选人中脱颖而出。

**面试资源和材料**

一般来说，你需要涵盖五个领域的材料：i) 编程，ii) 行为，iii) 机器学习/深度学习基础，iv) 机器学习系统设计，以及 v) 一本关于 MLE 面试的通用建议书。

i) 编程。如果你不是 LeetCode 专家，那么我建议你从以下资源开始：

[## NeetCode

### 更好的编程面试准备方法。

[neetcode.io](https://neetcode.io/roadmap?source=post_page-----e74eccef4645--------------------------------) [](https://www.goodreads.com/en/book/show/55014663-cracking-the-coding-interview?source=post_page-----e74eccef4645--------------------------------) [## 破解编程面试：189个编程问题

### 阅读来自全球最大读者社区的23条评论。我不是招聘人员。我是一个软件工程师。而且……

[www.goodreads.com](https://www.goodreads.com/en/book/show/55014663-cracking-the-coding-interview?source=post_page-----e74eccef4645--------------------------------) [](https://www.goodreads.com/book/show/218272975-coding-interview-patterns?source=post_page-----e74eccef4645--------------------------------) [## 编程面试模式：让你成功通过下一次编程面试

### 阅读来自全球最大读者社区的评论。未定义

[www.goodreads.com](https://www.goodreads.com/book/show/218272975-coding-interview-patterns?source=post_page-----e74eccef4645--------------------------------)

ii) 行为：

[](https://www.goodreads.com/book/show/133135995-behavioral-interviews-for-software-engineers?source=post_page-----e74eccef4645--------------------------------) [## 软件工程师的行为面试：所有面试技巧

### 阅读来自全球最大读者社区的评论。最快的方式提高你的薪资和补偿……

[www.goodreads.com](https://www.goodreads.com/book/show/133135995-behavioral-interviews-for-software-engineers?source=post_page-----e74eccef4645--------------------------------) [](https://www.goodreads.com/book/show/61058107-the-staff-engineer-s-path?source=post_page-----e74eccef4645--------------------------------) [## 员工工程师之路：个体贡献者的指南

### 阅读来自全球最大读者社区的163条评论。多年来，公司一直奖励他们最……

[www.goodreads.com](https://www.goodreads.com/book/show/61058107-the-staff-engineer-s-path?source=post_page-----e74eccef4645--------------------------------)

iii) 机器学习/深度学习基础：

[](https://www.goodreads.com/book/show/198282489-deep-learning?source=post_page-----e74eccef4645--------------------------------) [## 深度学习：基础与概念

### 阅读来自全球最大读者社区的5条评论。本书提供了一个全面的介绍……

[www.goodreads.com](https://www.goodreads.com/book/show/198282489-deep-learning?source=post_page-----e74eccef4645--------------------------------)

iv) 机器学习系统设计：

[](https://www.goodreads.com/book/show/120532868-machine-learning-system-design-interview?source=post_page-----e74eccef4645--------------------------------) [## 机器学习系统设计面试

### 阅读来自全球最大读者社区的7条评论。机器学习系统设计面试是最……

www.goodreads.com](https://www.goodreads.com/book/show/120532868-machine-learning-system-design-interview?source=post_page-----e74eccef4645--------------------------------) [](https://www.goodreads.com/book/show/60715378-designing-machine-learning-systems?source=post_page-----e74eccef4645--------------------------------) [## 设计机器学习系统：一个迭代过程...

### 阅读来自全球最大读者社区的76条评论。机器学习系统既复杂又独特…

www.goodreads.com](https://www.goodreads.com/book/show/60715378-designing-machine-learning-systems?source=post_page-----e74eccef4645--------------------------------)

v) 一本通用的MLE面试建议书：

[## 机器学习面试书简介](https://www.goodreads.com/book/show/152155500-inside-the-machine-learning-interview?source=post_page-----e74eccef4645--------------------------------)

### 您可以在此处阅读该书的网页友好版。源代码可以在 GitHub 上找到。讨论可以通过 Discord 进行…

huyenchip.com](https://huyenchip.com/ml-interviews-book/?source=post_page-----e74eccef4645--------------------------------) [](https://www.goodreads.com/book/show/152155500-inside-the-machine-learning-interview?source=post_page-----e74eccef4645--------------------------------) [## 机器学习面试揭秘：151个真实问题…

### 阅读来自全球最大读者社区的评论。让曾任亚马逊工程领导的彭绍（Peng Shao）为您分享经验…

www.goodreads.com](https://www.goodreads.com/book/show/152155500-inside-the-machine-learning-interview?source=post_page-----e74eccef4645--------------------------------)

您还需要有一些面试伙伴——现在，您可以订阅在线面试准备服务（不要选择那些收费高昂、要您花费上千美元的服务；总有更便宜的替代品），并与其他MLE候选人进行技能和信息交换。

**策略：规划、跟踪、演变、提示、提升面试水平、戴上“职业发展的眼镜”、寻找面试伙伴、警惕红旗**

规划、跟踪和进化。理想情况下，[正如本文所描述的](https://acompa.net/mle-job-hunt-2024.html)，你应该至少收到几次招聘人员的电话，并将面试按兴趣级别进行分类。一方面，招聘市场在不断变化，没人能够在第一次面试时就规划出最佳策略。另一方面，你会在面试过程中不断学习和成长，因此你会与几个月前的自己不同，尤其是在求职初期。所以，即使你是市场上最有才华的候选人，也非常有必要将面试分布在几个月的时间内，并且**从你最不感兴趣的对话开始**，以便熟悉市场并磨练面试技巧，而把最重要的对话留到后期。**在面试过程中跟踪你的进展、反馈和思考**。**设定具体的学习目标，并随着面试不断进化**。过去几年你可能从未接触过生成式人工智能（GenAI）知识，但你可以利用面试过程，通过在线课程学习并构建小型副项目。**最好的结果是面试后拿到工作，第二好的结果是即使没有获得工作邀请，你也学到了一些有用的东西**。如果你在每次面试中都不断学习，最终，它将大大提高你获得下一份工作邀请的机会。

提示。现在是大型语言模型（LLM）的时代，你应该明智地利用它。查找职位描述或职责中的关键词。如果有涉及“软件工程原则”的面试，那么你可以提示你喜欢的LLM，要求它提供一份关于机器学习的软件工程原则的清单，以备准备之用。再次强调，提示的答案不应是你唯一的知识来源，但它可以弥补你日常阅读资料中的一些盲点。

提升一个职位层级的面试。有时，职位层级之间的界限并不明确。除非你在这个领域是绝对的新手，否则你可以尝试申请比自己当前层级高一层的机会，并为职位邀请阶段准备低一层的机会。如果你正在面试高级职位，准备或申请更高层次的职位并不会有什么坏处。它不总是有效，但有时它能为你打开一些大门。

戴上你的“职业眼镜”。面试时不要只是单纯地想着面试本身。除非你因为某种特殊原因急需这份工作，否则请问问自己，**这份工作在你的整体职业规划中扮演着什么角色**？这个问题有两个层面的意义：首先，它帮助你选择你想要加入的公司，例如，一家初创公司可能短期内提供更高的薪水，但如果它不重视软件工程的基本原则，那么你将失去在长期内成长为一份有前途的职业的机会；其次，它帮助你诊断面试的结果，例如，你的拒绝大多来自初创公司，但最终你收到了知名上市公司的offer，那么你就会意识到这些拒绝并不意味着你不合格，而是因为在初创公司面试需要不同的技巧，而那些技巧并不属于你的职业路径。

结伴而行。五年前，还没有所谓的面试伙伴。不过现在，互联网上有各种各样的面试服务，从极其昂贵（我不推荐）到几百美元不等。记住，这是一个不断变化的市场，因此没有人能掌握全貌。获得信息的最佳方式是与非竞争领域的同行合作（例如，你在简历领域，而你的伙伴在推荐系统领域），共同练习并一起提高。比起单纯的合作，**你应该主动寻求合作——尽量寻找那些在更高职位的人，趁你还能为他们提供有价值的帮助时**。你可能会问，这怎么可能？为什么比我资深的人愿意和我一起练习？记住，没有人是完美的，你总是能为别人提供一些东西。有些资深的软件工程师希望转型成MLE（机器学习工程师），你可以用你的机器学习知识交换他们的软件工程最佳实践。有些产品经理需要机器学习相关的输入，你可以请求他们提供行为面试练习作为回报。即使是那些没有行业经验的初级人员，你也可以向他们请求编码练习，或者听听他们的生活故事，获得灵感。作为MLE，特别是在高级/职员级别，你需要展示领导力，而你能展示的最佳领导力就是集结不同层级的专业人士，帮助你实现你追求的目标——你的梦想offer。

红色警示信号。一些明显的红色警示信号，比如要求你加班过多或面试后“失联”，是显而易见的。然而，有些红色警示信号则更为微妙或故意伪装。例如，招聘经理可能会礼貌地解释他们的情况，并希望你“刚开始时不要抱有过高的期望，几个月后再决定是否离开”——这听起来非常体贴，然而它掩盖了员工流动性高的事实。避免红色警示信号的最佳策略是通过阅读Glassdoor评论和在面试过程中了解公司文化。具体来说，“文化”并不是指公司网站上定义的“文化宣言”，而是你与团队之间的实际互动。面试官是否只是在问事先准备好的问题，而没有试图了解你的问题解决能力？当你提出问题时，面试官能否理解并给出一个有助于你更好理解公司价值的回答？最后，**始终记得听从你的直觉，决定你是否喜欢未来的团队**。毕竟，如果你决定接受这份工作，你将每天与这些人相处八个小时，未来几年都是如此；如果你的直觉告诉你不喜欢他们，那么无论如何你都不会感到快乐。

**接受录取通知。** 一旦你度过了所有的沮丧、失望和辛苦的工作，是时候讨论录取通知了。很多论坛讨论了谈判薪资的必要性，但我建议在这个雇主市场中要谨慎。如果你想谈判，最好的做法是手里有两个可比的录取通知，并准备好最坏的情况。同时，像Levels.fyi和Glassdoor这样的站点可以用来研究薪酬范围。

## 面试后——接下来该做什么？

恭喜！现在你已经接受了录取通知，准备开始你的新旅程，但还有什么其他事情可以做吗？

**总结一下你的面试过程。** 面试过程漫长且充满挑战，但你也从中获得了许多经验！现在你已经放松并感到开心，这是一个反思的最佳时机，感谢那些在面试过程中帮助过你的人，并与那些还在为面试过程挣扎的人分享一些信息。另外，过去几个月你一定积累了很多笔记和待办事项清单，却从未有时间坐下来整理，现在正是整理它们的最佳时机！

**规划你的职业路径。** 在过去几个月中，你的自我认知可能发生了变化；现在，你更好地了解了自己在压力下的学习能力和解决问题的能力。你与许多初创公司和大公司进行了交流，开始更清楚地了解自己在未来五年的发展方向。如果你处于中高层级，那么你可能已经与许多资深员工交流，获得了关于下一阶段工作的更清晰的认知。现在正是进行所有这些规划的好时机！

**持续学习。** 如果你来自学术背景，你可能已经习惯于阅读论文，了解最新的机器学习技术。然而，MLE（机器学习工程师）的角色远不止学术领域。它结合了研究、应用机器学习实践和软件工程，以对业务产生实际影响。现在是时候思考最佳策略，利用多种来源持续学习，以保持自己的知识更新。

**为新角色做好准备。** 你已经与新公司的其他MLE进行了交流，了解了他们使用的模型或技术栈。在某些情况下，你可能已经非常熟悉这些技术栈，但大多数时候，你需要为新角色学习很多新知识。制定一个学习计划，并设定小的里程碑来实现目标。此外，了解你的新公司，浏览其官网并理解其业务目标。这将有助于你在开始新工作时树立一个良好的印象，并与新同事交流。

毕竟，每个人的面试旅程都是不同的。你的经验水平、专注领域、长期职业目标和个性都共同塑造了一个独特的面试旅程。希望本文能为你提供面试准备材料和策略上的一些启发。也希望每个人最终都能拿到心仪的工作邀请！

**致谢：** 特别感谢[Ben Cardoen](https://www.linkedin.com/in/ben-cardoen/)和[Rostam Shirani](https://www.linkedin.com/in/rostams/)的校对和富有洞察力的建议，帮助本文达到了最终版本。

## 参考文献

+   Sophie Magnet，“2024年最受欢迎的机器学习工程师技能”，链接：[https://365datascience.com/career-advice/career-guides/machine-learning-engineer-skills](https://365datascience.com/career-advice/career-guides/machine-learning-engineer-skills)

+   显然AI，[“机器学习与大语言模型系统设计：500个案例研究”](https://www.evidentlyai.com/ml-system-design)

+   Sam Jones，“2024年机器学习工程师的就业市场”，链接：[https://www.peopleinai.com/blog/2024-job-market-for-machine-learning-engineers](https://www.peopleinai.com/blog/2024-job-market-for-machine-learning-engineers)

+   詹妮弗·罗伯逊，《为什么很难找到资深机器学习工程师》，链接：[https://blog.qwasar.io/blog/why-its-so-difficult-to-find-senior-machine-learning-engineers](https://blog.qwasar.io/blog/why-its-so-difficult-to-find-senior-machine-learning-engineers)

+   奇普·许恩，《机器学习面试入门书》，链接：[https://huyenchip.com/ml-interviews-book/](https://huyenchip.com/ml-interviews-book/)

+   NeetCode路线图，链接：[https://neetcode.io/roadmap](https://neetcode.io/roadmap)

+   格雷尔·拉克曼·麦克道尔，《破解编程面试：189个编程问题与解决方案》，2015年

+   亚历克斯·徐，《编程面试模式：掌握你的下一次编程面试》，2024年

+   亚历杭德罗·孔帕尼奥尼，《2024年作为机器学习工程师求职指南》，链接：[https://acompa.net/mle-job-hunt-2024.html](https://acompa.net/mle-job-hunt-2024.html)

+   彭绍，《机器学习面试揭秘：来自FAANG的151个真实问题及其解答》，2023年

+   奇普·许恩，《设计机器学习系统：生产就绪应用的迭代过程》，2022年

+   阿里·阿米尼安，亚历克斯·徐，《机器学习系统设计面试》，2023年

+   克里斯托弗·M·毕晓普，休·毕晓普，《深度学习：基础与概念》，2023年

+   塔尼娅·瑞利，《资深工程师之路：个体贡献者在成长与变革中如何导航》，2022年

+   梅莉亚·斯特凡诺维奇，《软件工程师行为面试：所有必须知道的问题以及能让你获得工作的证明策略与答案》，2023年
