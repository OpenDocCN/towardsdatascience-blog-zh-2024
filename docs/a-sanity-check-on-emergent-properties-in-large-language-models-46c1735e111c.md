# 《大型语言模型中‘突现特性’的理性检查》

> 原文：[https://towardsdatascience.com/a-sanity-check-on-emergent-properties-in-large-language-models-46c1735e111c?source=collection_archive---------2-----------------------#2024-07-15](https://towardsdatascience.com/a-sanity-check-on-emergent-properties-in-large-language-models-46c1735e111c?source=collection_archive---------2-----------------------#2024-07-15)

## LLM通常被认为具有“突现特性”。但我们究竟指的是什么，且我们有哪些证据呢？

[](https://medium.com/@anna.rogers?source=post_page---byline--46c1735e111c--------------------------------)[![Anna Rogers](../Images/6a6422381ad09cabeff96abe776cc4b5.png)](https://medium.com/@anna.rogers?source=post_page---byline--46c1735e111c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--46c1735e111c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--46c1735e111c--------------------------------) [Anna Rogers](https://medium.com/@anna.rogers?source=post_page---byline--46c1735e111c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--46c1735e111c--------------------------------) ·12分钟阅读·2024年7月15日

--

![](../Images/57e91e506e2928bae4b9a95da2b994b8.png)

关于大型语言模型（LLM）的一个常被提到的观点，在我们[ICML’24 立场文件](https://openreview.net/forum?id=M2cwkGleRL)中有讨论，认为它们具有“突现特性”。不幸的是，在大多数情况下，发言人/作者没有明确说明他们所指的“突现”是什么意思。但在这个问题上的误解可能对研究议程以及公共政策产生重大影响。

从我在学术论文中看到的情况来看，NLP研究人员至少在四种意义上使用这个术语：

> 1\. 模型表现出的特性，即使它没有针对该特性进行明确的训练。例如，[Bommasani et al. (2021, p. 5)](https://arxiv.org/abs/2108.07258) 提到 GPT-3 的少样本表现 [(Brown et al., 2020)](https://papers.nips.cc/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html) 被称为“一个突现特性，既没有专门训练，也没有预期会出现”。
> 
> 2\. （与定义1相对）：模型从训练数据中学到的特性。例如，[Deshpande et al. (2023, p. 8)](https://aclanthology.org/2023.findings-acl.326/) 讨论突现作为“预训练优势”的证据。
> 
> 3\. 如果某一特性在较小的模型中不存在，但在较大的模型中存在，那么这个特性就是“突现的”([Wei et al., 2022, p. 2](https://openreview.net/pdf?id=yzkSU5zdwD))。
> 
> 定义（3）的一个版本，其中使得涌现特性“引人注目”的因素是“它们的尖锐性，从不存在到存在似乎瞬间过渡，以及它们的不可预测性，似乎在不可预见的模型尺度上出现”[(Schaeffer, Miranda, & Koyejo, 2023, p. 1)](https://proceedings.neurips.cc/paper_files/paper/2023/file/adc98a266f45005c403b8311ca7e8bd7-Paper-Conference.pdf)

对于一个技术术语来说，这种模糊性是令人遗憾的。如果许多人在没有澄清其含义的情况下重复“LLM具有涌现特性”这一说法，读者可能会推断出，根据*读者*自己的定义，科学界对此说法的真实性存在广泛的共识。

我写这篇文章是在全球各地的自然语言处理（NLP）研究小组中进行了许多关于这一主题的讲座后写的——包括美国的阿姆赫斯特和乔治城大学、英国的剑桥、卡迪夫和伦敦、丹麦的哥本哈根、瑞典的哥德堡、意大利的米兰、以及新加坡的Genbench研讨会（EMNLP'23）（感谢所有在场的观众！）。这给了我一个机会，向220位NLP研究员和博士生进行调查，了解他们对涌现的看法。根据他们的回答，到目前为止，最受欢迎的定义是（1），其次是（4）。

![](../Images/8d035a27fecb81b1dded9b2b0c973667.png)

定义（1）中表达的观点在公众话语中也经常被引用。例如，你可以在[关于谷歌的PaLM模型“知道”它没有接受过训练的语言的说法中看到这一点](https://www.buzzfeednews.com/article/pranavdixit/google-60-minutes-ai-claims-challenged)（这几乎肯定是错误的）。相同的观点也引发了美国参议员与梅拉尼·米切尔（Melanie Mitchell，一位著名的人工智能研究员，圣塔菲研究所的教授）之间的以下公开对话：

这段对话表明，定义（1）中的LLM“涌现特性”这一观点在研究领域之外有影响。它助长了[对超级人工智能（AGI）即将接管的焦虑](https://x.com/BasedNorthmathr/status/1797142896069488857)，以及[暂停研究的呼声](https://futureoflife.org/open-letter/pause-giant-ai-experiments/)。它可能会推动政策制定者走错方向，比如禁止开源研究——这将进一步巩固大科技实验室的资源，并确保它们几乎没有竞争对手。它还创造了LLM作为独立于开发者和部署者选择的实体的印象——这对*谁*对这些模型造成的任何伤害负责具有重大意义。对于研究界和社会来说，既然赌注如此之高，我们是否应该至少确保科学是可靠的？

# 这些关于“涌现”的概念在多大程度上有助于我们对LLM的科学理解？

上述关于LLM中‘涌现’的版本仍然存在很多争议：它们在多大程度上推动了科学讨论，与已经使用的其他术语和已知原理相比？我想强调的是，这一讨论与LLM是否有用或有价值的问题完全无关。无数模型在没有“涌现”声明的情况下已经是并将继续是实际有用的。

让我们从定义2开始：*模型从训练数据中学到的东西*。既然这正是机器学习模型应该做的事情，那么这种版本的“涌现”是否为“学习”增加了什么呢？

对于定义（3）（*只有大型模型才能做到的事情*），考虑到基本的机器学习原理，大型模型的更好性能是可以预期的：大型模型有更多的容量来学习训练数据中的模式。因此，这种“涌现”的版本也没有增加太多。除非我们期望更大的模型，而不是小模型，做一些它们未经过训练的事情——但这个定义又依赖于定义（1）。

对于定义（4），*性能急剧*变化的现象被证明是由于非连续的评估指标（例如，多项选择题回答类任务），而不是LLM本身的原因[(Schaeffer, Miranda, & Koyejo, 2023)](https://proceedings.neurips.cc/paper_files/paper/2023/file/adc98a266f45005c403b8311ca7e8bd7-Paper-Conference.pdf)。此外，J. Wei本人也承认，当前关于急剧变化的说法是基于只有少量尺寸（1B，7B，13B，70B，150B等）的模型的结果，如果我们有更多中间模型尺寸的结果，性能的提高可能会是平滑的[(Wei, 2023)](https://www.jasonwei.net/blog/common-arguments-regarding-emergent-abilities)。

定义（4）中的*不可预测性*部分被[J. Wei (2023)](https://www.jasonwei.net/blog/common-arguments-regarding-emergent-abilities)再次强调如下：“如果预测性存在较大差异，‘涌现’现象仍然很有趣：对于某些问题，大型模型的表现可以很容易地从比其小1000倍的模型的表现中推断出来，而对于其他问题，即使是从小2倍的模型也无法推断出来。”

然而，所引用的在计算量减少1,000倍时的可预测性，指的是GPT-4报告[(OpenAI, 2023)](https://arxiv.org/abs/2303.08774)，在该报告中，开发者事先知道目标评估，并为此进行了专门优化。鉴于此，从理论上讲，可预测的扩展性并不令人惊讶（尽管从工程的角度来看仍然令人印象深刻）。这与在计算量减少2倍时，对于未计划的BIG-Bench评估中的不可预测性[(Wei et al., 2022)](https://openreview.net/pdf?id=yzkSU5zdwD)形成对比。这种不可预测性是预期中的，原因仅仅是（a）训练数据与测试数据的相似性，和（b）足够的模型容量能够学习一些特定模式之间的未知交互。

因此，我们回到了定义（1）：突现属性是指模型没有专门训练的属性。这可以从两个角度来解释：

> 5\. 如果模型没有接触到该属性的训练数据，则该属性是突现的。
> 
> 6\. 即使模型已暴露于相关的训练数据中，某个属性仍然是突现的——只要模型开发者没有意识到这一点。

根据定义6，研究问题实际上是“网络上有哪些数据？”（或生成型AI公司的专有训练数据集中的数据），而我们正在将大规模语言模型（LLM）作为一种非常昂贵的方法来回答这个问题。例如，[ChatGPT可以生成看起来合理的国际象棋走法（但通常是非法的）](https://reddit.com/r/AnarchyChess/comments/10ydnbb/i_placed_stockfish_white_against_chatgpt_black/)。如果我们把ChatGPT看作是一个*语言*模型，这就很令人惊讶，但如果我们知道它是一个在网络语料库上训练的模型，这就不奇怪了，因为这样的语料库可能不仅包含自然语言的文本，还包括如国际象棋记录、ASCII艺术、MIDI音乐、编程代码等材料。术语“语言模型”实际上是一个误称——它们更像是*语料库*模型[(Veres, 2022)](https://doi.org/10.1109/ACCESS.2022.3182505)。

根据定义5，我们只能通过证明模型未曾接触到可能成为训练数据中模型输出基础的证据来证明某个属性是突现的。而且，这不能是由于在连续表示的潜在空间中幸运抽样的结果。如果我们可以生成任意多的样本并精心挑选，最终我们甚至能从一个随机初始化的模型中得到一些流畅的文本——但从定义（5）来看，这不应该算作是“突现属性”。

对于像ChatGPT这样没有公开训练数据的商业模型，这种证明是不可能的。但是即便是对于“开放”型LLM，这也仅仅是一个假设（如果不是一厢情愿的话），因为到目前为止，我们缺乏详细的研究（甚至是方法论）来考虑特定模型输出与训练文本数据中的证据量和种类之间的确切关系。在定义5中，涌现特性是机器学习中的“炼金术”——因此提出这一假设的标准应该相当高。

尤其是在面对相反证据时。

# 对LLM中“涌现特性”的反证

以下是一些实证结果，这些结果使得LLM具有“涌现特性”的定义（5）（模型未接触到该特性相关的训练数据）变得值得怀疑：

+   提示敏感性现象 ([Lu, Bartolo, Moore, Riedel, & Stenetorp, 2022](https://aclanthology.org/2022.acl-long.556/); [Zhao, Wallace, Feng, Klein, & Singh, 2021](https://proceedings.mlr.press/v139/zhao21c.html)): 大型语言模型（LLMs）对语义上应该等价的提示做出不同的响应。如果我们认为模型具备回答问题的涌现特性，那么提问的方式稍有不同，特别是少量示例的顺序不同，也不应该产生影响。提示敏感性的最可能解释是，模型更倾向于对那些在某些方面与训练数据更相似的提示做出更好的响应，这种相似性有助于模型的表现。

+   Liang等人评估了30个LLM，并得出结论：“再现（受版权保护的材料）风险与模型准确性显著相关”[(2022, p. 12)](https://arxiv.org/abs/2211.09110)。这表明，越是“记住”更多训练数据的模型，表现越好。

+   [McCoy, Yao, Friedman, Hardy, & Griffiths (2023)](https://arxiv.org/abs/2309.13638) 表明，LLM的性能依赖于网页文本中输出单词序列的概率。

+   [Lu, Bigoulaeva, Sachdeva, Madabushi, & Gurevych (2024)](https://aclanthology.org/2024.acl-long.279.pdf) 表明，18个LLM的“涌现”能力大多可以归因于上下文学习。指令调优促进了上下文学习，但似乎没有独立的影响。

+   至于上下文学习本身（首次在GPT-3中展示[(Brown et al., 2020)](https://papers.nips.cc/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html)，并被Bommasani等人用作“涌现”的例子[(2021, p. 5)](https://arxiv.org/abs/2108.07258)），[Chen, Santoro等人(2022)](https://proceedings.neurips.cc/paper_files/paper/2022/hash/77c6ccacfd9962e2307fc64680fc5ace-Abstract-Conference.html)的研究结果表明，这种现象只发生在经过序列训练的Transformer模型中，这些序列在结构上类似于上下文学习将被测试的序列。

+   [Liu et al. (2023)](https://arxiv.org/abs/2304.03439)报告称，ChatGPT和GPT-4在较旧的基准测试上表现优于新的基准测试，这表明许多评估结果可能因数据污染而被夸大。OpenAI在GPT-3的论文中[(Brown et al., 2020)](https://papers.nips.cc/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html)费尽心思展示了缓解这个问题的困难。由于我们对最新模型的训练数据一无所知，外部评估结果可能没有意义，而以商业服务出售其模型的公司所提供的内部报告则存在明显的利益冲突。

一个著名的努力是提出一种方法论，至少可以避免数据污染问题，那就是“AGI的火花”研究[(Bubeck et al., 2023)](http://arxiv.org/abs/2303.12712)。该研究通过新构建的测试案例，结合公共网页数据及其扰动进行检查，作者显著地得出结论，认为GPT-4具备“非常先进的心智理论”。至少有两项研究得出了相反的结论（[Sap, Le Bras, Fried, & Choi, 2022](https://aclanthology.org/2022.emnlp-main.248); [Shapira et al., 2024](https://aclanthology.org/2024.eacl-long.138/)）。这种方法论失败的最可能原因是，尽管我们可以检查网页上的直接匹配，我们仍然可能错过一些高度相似的案例（例如，文中提到的著名的由tikz绘制的独角兽[可能基于stackoverflow社区绘制的其他动物图像](https://twitter.com/DimitrisPapail/status/1644809234431848450?s=20)）。此外，像GPT-4这样的商业LLM也可能在并非公开的数据上进行训练。在OpenAI的案例中，在OpenAI更改其服务条款，默认不再使用通过API提交的数据进行训练之前，成百上千的研究人员和GPT-3的其他用户通过API提交了大量数据。

这并不是说大型语言模型（LLMs）绝对不可能在其训练分布之外表现良好。某种程度的泛化确实发生了，最好的情况是，这种泛化是由于对训练数据中各个单独观察到的模式进行插值，而这些模式在训练时并没有一起出现。但我们何时可以说结果是某种质的新的东西，什么样的与训练数据的相似性是重要的，以及我们如何识别这些相似性——这些仍然是未解决的研究问题。

# 自然语言处理（NLP）研究人员实际上并不相信LLM的“突现属性”

正如我提到的，我有机会在几个NLP研究小组中做关于此话题的讲座。在这些讲座的开始，*在*我展示上述讨论之前，我问了观众几个问题，包括他们是否个人相信LLM具有涌现特性（根据他们偏好的定义，如上所示，主要是(1)）。我还询问了他们对领域内共识的看法——他们认为大多数其他NLP研究者对此持什么观点？对于第一个问题，我得到了259位研究人员和博士生的回答，对于第二个问题，我得到了360个回复（提醒自己：给大家更多时间连接到调查）。

结果令人震惊：尽管大多数受访者对LLM的涌现特性持怀疑或不确定态度（只有39%的人同意该说法），但70%的人认为大多数*其他*研究者相信这一点。

![](../Images/1500c1639ea258df983856d2791205a9.png)

这与其他一些错误的社会学观念相符：例如，许多NLP研究者并不认为NLP排行榜特别有意义，也不认为规模化可以解决一切问题，但他们确实认为*其他*的NLP研究者认为[(Michael et al., 2023)](https://aclanthology.org/2023.acl-long.903/)。在我的样本中，认为LLM具有涌现特性的观点在少数研究者中占有一定地位，但却被误解为是大多数人的看法。即便在这少数人中，这一信念也并不十分坚定。在我的四场演讲中，在呈现上述讨论之后，我还询问了观众们现在的看法。在这70个回复的样本中，最初同意“LLM具有涌现特性”这一说法的83%的人，改为不同意（13.9%）或不确定（69.4%）。

回顾起来，“同意/不同意/不确定”并不是这项调查的最佳选项。作为科学家，我们几乎不可能做到百分之百的确定：正如Yann LeCun在[Munk辩论](https://munkdebates.com/debates/artificial-intelligence/)中所说的，我们甚至无法证明现在没有茶壶在绕木星运行。我们的工作不是陷入这些分心的“兔子洞”，而是制定和验证假设，推动我们对所研究现象的理解。对于LLM中的“涌现”，我认为我们仍然处于“假设制定”阶段——因为即使在完成了所有上述关于“涌现”的澄清工作后，我们仍然没有明确的研究问题，也没有清晰的方法来获得实证证据。

关键的未解问题是，现有模式的哪种插值才能被视为足够新颖，足以在自然语言数据领域被归类为‘涌现现象’。这个领域尤其困难，因为它融合了不同类型的信息（语言、社会、事实、常识），而且这些信息可能以不同的方式呈现（在上下文中是显式的、隐式的，或需要在长上下文中进行推理）。参见[Rogers, Gardner, & Augenstein (2023, 第8.2节)](https://arxiv.org/abs/2107.12708)，讨论了仅在问答任务中涉及的不同技能。

> *📢* 如果你（或你认识的人）希望解决LLM输出与其训练数据之间的关系问题——在美丽的哥本哈根，有资助的[博士后](https://candidate.hr-manager.net/ApplicationInit.aspx?cid=119&ProjectId=181730&DepartmentId=3439&MediaId=5) / [博士学位](https://candidate.hr-manager.net/ApplicationInit.aspx?cid=119&ProjectId=181734&DepartmentId=3439&MediaId=5)职位，可以从事相关研究！(申请截止日期：2024年11月15日/22日)

如果你想引用这篇文章：上述大多数观点，除了投票结果，基于这篇[ICML 2024 立场论文](https://openreview.net/forum?id=M2cwkGleRL)的一部分，该论文还探讨了‘涌现’的科学哲学意义及其在大语言模型（LLMs）中的适用性。我们还讨论了**我们到底是什么意思为‘大语言模型’**（与‘基础模型’和‘前沿模型’相对），以及几个其他常被重复的说法，这些说法伴随着大量的脚注：**大语言模型是强健的**，**大语言模型是最先进的**，**（大语言模型）规模就是一切**，**大语言模型是通用技术**。

致谢：

+   我的杰出合著者萨莎·卢奇奥尼

+   上述论文的所有匿名审稿人

+   Rob van der Goot, Christian Hardmeier, Yacine Jernite, Margaret Mitchell, Dennis Ulmer，他们阅读了论文的早期版本并提供了反馈

+   Ryan Cotterell, Ishita Dasgupta, Laura Gwilliams, Julia Haas, Anna Ivanova, Tal Linzen, Ben Lipkin, Asad Sayeed 感谢他们的见解和讨论

+   所有参与我投票的[剑桥LTL](https://talks.cam.ac.uk/show/archive/60438)、[卡迪夫NLP](https://cardiffnlp.github.io/)、[哥本哈根大学语言技术中心](https://cst.ku.dk/kalender/sprogteknologisk-konference-2023/)、[CLASP](https://www.gu.se/en/clasp)、[乔治城大学CL@Georgetown](https://gucl.georgetown.edu/)、[EMNLP23的Genbench](https://genbench.org/workshop/)、[米兰NLP](https://milanlproc.github.io/)、[QMUL](https://www.responsible-ai.science/)和[马萨诸塞大学阿姆赫斯特分校](https://people.cs.umass.edu/~miyyer/nlpseminar/index.html)的参与者。

*最初发表于* [*https://hackingsemantics.xyz*](https://hackingsemantics.xyz/2024/emergence/) *于2024年7月15日。*

*故事中的所有图片均由作者创作。头图包括* [*Edward von Lõngus的涂鸦“裸帝”作品，采用CC-BY-SA 4.0许可协议*](https://commons.wikimedia.org/wiki/File:Alasti_keiser,_Edward_von_L%C3%B5nguse_t%C3%B6%C3%B6_Tartus.JPG)*.*
