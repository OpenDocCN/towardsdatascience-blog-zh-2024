# 语言AI中的反事实

> 原文：[https://towardsdatascience.com/counterfactuals-in-language-ai-956673049b64?source=collection_archive---------5-----------------------#2024-07-23](https://towardsdatascience.com/counterfactuals-in-language-ai-956673049b64?source=collection_archive---------5-----------------------#2024-07-23)

## 使用开源语言模型和LLM

[](https://joshuabanksmailman.medium.com/?source=post_page---byline--956673049b64--------------------------------)[![Joshua Banks Mailman, Ph.D.](../Images/e28bf743fc5bdba644dcfb747eeb72b0.png)](https://joshuabanksmailman.medium.com/?source=post_page---byline--956673049b64--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--956673049b64--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--956673049b64--------------------------------) [Joshua Banks Mailman, Ph.D.](https://joshuabanksmailman.medium.com/?source=post_page---byline--956673049b64--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--956673049b64--------------------------------) ·27分钟阅读·2024年7月23日

--

![](../Images/3a8683cdc2f0845498a740e41bdcb8af.png)

由作者使用DALL-E 3生成

去年ChatGPT的“轰动”使人们意识到——对许多人来说是一个惊讶——假定的人工智能有时可能会毫不犹豫地给出非常错误的答案。这个现象通常被称为“幻觉”，而过去18个月中发展出的主要应对方法是通过引入事实，通常通过*检索增强生成*（RAG），有时也称为*相关答案生成*，它基本上是将GPT（*生成预训练变换器*语言模型）重新定向，去从已知包含相关事实的上下文中提取信息。

然而，幻觉并不是GPT可能犯错的唯一方式。从某些方面来看，其他类型的错误更深刻，也更值得我们考虑——特别是当人工通用智能（AGI）的前景经常被讨论时。我具体指的是被称为*反事实*（反事实推理）的现象，以及反事实性在决策中的关键作用，尤其是在*因果推理*方面。因此，事实性并不是有效LLM操作的唯一标准。

在这篇文章中，我将反思反事实如何帮助我们以不同的方式思考生成型AI的陷阱与潜力。我将通过一些具体示例，使用开源语言模型（特别是微软的Phi）来展示。我将展示如何在本地设置Ollama（也可以在Databricks中进行），无需太多复杂操作（包括有无Docker容器的设置），让你可以亲自尝试。我还将比较OpenAI的LLM对同样提示的反应。

我建议，如果我们想要开始思考“智能”是否存在于人工技术中，或由其散发出来，我们可能需要超越既定的机器学习范式，这种范式假设某种先前存在的事实正确性作为衡量标准。智能行为可能本质上是推测性的，尚缺乏足够的过去证据来明显证明其价值。艾萨克·牛顿、查尔斯·达尔文，或你的宠物猫，都能够就它们所处的物理世界进行实际推理，而这是大型语言模型（LLMs）——因为它们是*非*具身的——无法做到的。在一个机器能够流利写作的世界里，谈论比推测性的实际推理更为廉价。

![](../Images/42bb142715d76d80ab499d9bf7b2c778.png)

由[Florian Klauer](https://unsplash.com/@florianklauer?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **反事实**

什么是反事实，为什么我们要关心它？当然有一些无聊的猜测，有时还带有修辞性的转折：在一次年度会议上，一位股东问到，

> “…自1888年以来的回报本来会没有董事们的‘傲慢和贪婪’以及他们的奖金。” *¹*

…对此，退休银行家查尔斯·芒恩²回答道：

> “那是我不会回答的问题。我是个历史学家，我不处理反事实问题。” *¹*

这是*一种*回避问题的方法。毫无疑问，政治家们也曾使用过。尽管在法律事务中强调先例，反事实性仍然可以是一个合法的考虑因素。正如罗伯特·N·斯特拉斯费尔德所说：

> “就像生活中的其他事情一样，我们沉迷于，实际上，要求进行许多关于‘可能本该是怎样’的猜测。虽然这种反事实思维往往保持隐蔽或隐含，但每当我们确定一个原因时，我们就会遇到它，实际上我们常常在尝试制定补救措施时也会遇到它……然而，……在法律争议中，麻烦的‘可能曾经是’无处不在。我们在各种情况下都会被它绊倒，并以不一致的方式回应它，从肆无忌惮的自信到面对任务时的瘫痪。然而，当我们意识到这一行为本质时，我们的自信往往会消退，我们变得不安、困惑，并对整个过程产生怀疑。”³

他进一步提出：

> “…法律决策者无法避免反事实问题。因为这些问题是必要的，我们应该认真思考何时以及如何提出这些问题，以及如何区分好的回答与差的回答。”

反事实并不是一种“随便什么都行”的事务——远非如此。

这个问题贯穿于负责任的人工智能和可解释人工智能的讨论中（这两者往往会交织在一起）。请考虑欧盟《通用数据保护条例》（“GDPR”）中的“解释权”。⁴ 部分得益于[Julia Stoyanovich](https://nyudatascience.medium.com/until-you-try-to-regulate-you-wont-learn-how-julia-stoyanovich-discusses-responsible-ai-for-4cf119002b05)的努力，[纽约市在2021年通过了一项法律，要求被人工智能辅助招聘流程拒绝的求职者有权了解被拒绝的具体原因](https://rules.cityofnewyork.us/wp-content/uploads/2022/09/Stoyanovich_LL144_October24_2022.pdf)。⁵ ⁶ ⁷

如果你是数据科学家，提到“解释”（就模型而言）可能会让你想到SHAP（或LIME）。基本上，SHapley加性解释（SHAP）是通过逐个处理每个预测特征（每一列数据），并打乱该特征（列）的观察值（行），来评估打乱哪个特征（列）会对预测结果产生最大影响。例如，对于被拒绝的求职者，它可能会说：算法拒绝你的主要原因是“工作经验”，因为当我们随机替换（排列）其他候选人的“工作经验”时，它对算法对你的评分的影响大于当我们用你其他特征（如性别、教育等）做这种替换（排列）时的影响。它是在进行定量比较，类似于“如果…会怎样”。那么，SHAPley除了反事实之外还有什么呢？反事实性是解释的核心，因为它揭示了因果关系；而解释正是使人工智能变得负责任的关键。

暂时搁置责任和伦理问题，因果解释在商业中仍然有其他用途。至少在某些公司中，数据科学和人工智能被期望指导决策，这在因果推断的术语中意味着*进行干预*：调整价格或选择这个客户群体而不是那个，等等。干预是对现状的改变。*因果推断的根本问题*在于，我们无法观察到从未发生过的事情。因此，我们无法在进行干预之前观察到其结果。涉及风险时，我们不希望在没有充分预测结果的情况下进行干预。因此，我们希望提前推断，我们期望的结果可以通过我们的干预来实现。这意味着要对尚未成为事实的事件的因果效应进行推断。相反，这些事件是*反事实*的，也就是说，与事实相反。这就是为什么反事实被Judea Pearl等人描述为

***因果推断的根本问题* ⁸**

因此，“思想实验”这一概念，特别是在哲学中尤为重要——自路德维希·维特根斯坦普及以来，这一概念越来越被重视——作为一种探讨我们如何使用语言构建对世界理解的方式，并不仅仅是一个感伤的星愿。相反，反事实思维才是理性决策的核心。

在这方面，Eric Siegel在他近期的《AI Playbook》中提出的建议可以作为推论：Siegel建议将*变革管理*从事后思考调整为任何机器学习项目的前提条件。¹⁰ 如果从一开始就没有在机器学习项目中融入商业变革的构思，那么它的部署很可能永远只是幻想（永远的反事实）。解药是提前想象干预措施，并系统地推算其因果效应，这样你几乎可以品味到它们。如果所有有可能受益的各方都能预见——甚至持续地想象——其潜在收益，那么机器学习项目更有可能从幻想转变为现实。

正如Aleksander Molak在他近期的《Python中的因果推断与发现》（2023）一书中解释的那样

> “反事实可以被视为*假设*的或*模拟*的干预，假定一个*特定的世界状态*。” *¹¹*

*理性想象力*的能力在许多哲学对理性行为体的定义中都起着重要作用。¹² ¹³

> “[P]sychological research shows that rational *human* agents *do* learn from the past and plan for the future engaging in counterfactual thinking. Many researchers in artificial intelligence have voiced similar ideas (Ginsberg 1985; Pearl 1995; Costello & McCarthy 1999)” *¹³ ¹⁴ ¹⁵ ¹⁶*

正如Molak所展示的，“当我们满足某些假设时，我们可以计算反事实”（33）¹¹。 这意味着在某些情况下，我们可以判断反事实推理是正确还是错误，正确还是不正确。在这方面，即使是虚构的东西（与事实相对）也可以在某种意义上是*真实的*。

![](../Images/609a0178afba0bfbbba353287702fec7.png)

图片来源：[Hans Eiskonen](https://unsplash.com/@eiskonen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **超越“随机鹦鹉”的人工智能**

语言流畅性似乎成了新的亮眼之物。但这真的是思维吗？如果IBM的深蓝和DeepMind的AlphaGo的卓越表现仅仅归结于冷冰冰的计算，那么自2022年底以来，ChatGPT语言流畅性所散发的人类气息则极大提升——或者至少重新框定——了那个老问题：人工智能真的能够“思考”吗？还是说，ChatGPT背后的大型语言模型仅仅是一个“随机鹦鹉”，将极有可能的词语串联在一起，形成无数种新组合？不过，有时似乎某些被认为是人类思维的人——其中一些人甚至竞选国家最高领导职务——所做的也不过如此。那么，真正的智能能否站出来？

在她关于2024年达沃斯的简短文章《[2024年培养AI婴儿](https://fionajmcevoy.medium.com/raising-baby-ai-in-2024-1b7d704cd3d5)》中，Fiona McEvoy报道了Yann LeCun强调人工智能不仅要从文本中学习，还要从视频资料中学习的观点。¹⁷ 然而，这仍然是被动的；它仍然是试图从已经存在的视频“事实”（已有的视频资料）中学习；McEvoy报道道

> “[达芙妮]·科勒认为，要超越单纯的关联，达到类似人类因果推理的水平，系统需要以具身的方式与真实世界互动——例如，从‘野外’技术中获取输入，如增强现实和自动驾驶车辆。她补充说，这些系统还需要有空间进行实验，与世界互动，以学习、成长并超越人类所能教给它们的。” *¹⁷*

另一种表达方式是：人工智能至少需要以某种方式通过*具身*的方式与世界互动，才能磨练它们进行反事实推理的能力。我们都看过这些视频——或者近距离观察过——一只猫把物体从台面上推下来，似乎没有任何目的，除了让我们烦恼。人类的婴儿和幼儿也会这样做。然而，尽管看起来像是在表演，这其实并非单纯的行为表现。相反，在某种程度上，这些行为是*假设检验*。这些行为源于一种好奇心：如果我推倒这个花瓶，会发生什么？

![](../Images/e7ae42796b346703ff78cbc1ddb7476d.png)

由 DALL-E 3 生成并由作者编辑

请观看这个由[北多伦多猫咪救援](https://www.northtorontocatrescue.com/why-do-kitties-knock-things-over/)展示的[3秒动画 gif](https://www.northtorontocatrescue.com/wp-content/uploads/2020/05/source.gif)。¹⁸ 在这个简短的猫咪视频中，有一个额外的细节提供了更多的信息：猫咪即将跳跃；但在跳跃之前，它意识到有一个可以立即使用的物体，可以用来在跳跃之前测试距离或表面。她的跳跃是反事实的（她还没有跳跃）。她几乎已经准备好跳跃，说明她已经假设跳跃是可行的；猫咪迅速在脑海中模拟了跳跃；突然意识到台面上的瓶子提供了一个机会，可以进行干预，测试她的假设；这个行为是习惯性的。

我毫不怀疑她评估这种跳跃可行性的能力，源于她之前曾多次实际演练过类似的情境。一个没有实际“身体参与”的人工智能，会做出相同的判断吗？显然，人类在这方面的表现远远超过猫咪。这正是科学发现和技术发明发生的方式；但在更平凡的层面，这种智慧是所有生物常规运作的一部分，无论是猫咪跳到地板上，还是人类做出商业决策。

## 推理

通过进行干预来测试反事实似乎能提升我们进行查尔斯·桑德斯·皮尔士所称的*溯因推理*的能力。¹⁹ ²⁰ 与*归纳推理*（从重复的案例中推断出模式）和*演绎推理*（推导出逻辑含义）不同，溯因推理是提出一个假设。尽管数据科学家常常通过特征工程和超参数调优来探索假设场景，但溯因推理并不是机器学习应用的直接组成部分，因为机器学习通常是在基于事实的预设可能性空间上进行优化，而溯因推理则是在扩展可能性空间，超越已有的事实。因此，也许人工通用智能（AGI）还有很多需要追赶的地方。

这是一个假设：

+   缺乏进行干预的能力（或机会）的实体（无论是生物体还是人工体）不会培养出多少反事实推理能力。

反事实推理或溯因推理的价值主要在于，通过干预可以尝试验证这些假设。这就是它与动物（或人类）相关的原因。如果没有最终干预的机会，因果推理（溯因推理，假设）就是徒劳的，因此也不值得培养。

如果溯因推理（反事实推理）不能提供某种优势，那么它就不会在人类（或猫）身上进化出来。这样的优势只能与进行干预相关，因为溯因推理（反事实推理）按定义并不陈述当前世界状态的事实。这些观察促使了上述关于生物和人工实体的假设。

![](../Images/a4e2fbe0c2d8958844c18b5f37c1ee36.png)

图片由[Alice Alinari](https://unsplash.com/@alicealinari?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **大型语言模型**

如我上面提到的，RAG（检索增强生成，也称为相关答案生成）已成为指导基于LLM的生成型人工智能系统（如聊天机器人）朝向适当甚至最优响应的事实标准。RAG的前提是，如果将相关的真实文本片段与您的问题一起直接提供给生成型LLM，那么它不太可能产生幻觉，从而提供更好的响应。“幻觉”是人工智能行业的术语，指的是*编造错误的回应*。

正如大家所知，幻觉的产生是因为LLM尽管在大量人类编写的互联网文本上经过了充分的训练，但仍然不是无所不知的，反而倾向于以一种相对一致的自信语气发出回应。令人惊讶吗？实际上，这不应该令人惊讶。它是有道理的，正如著名的批评所说：LLM本质上是在复述它们所训练过的文本。因为LLM的训练并不是基于人们有时不确定或不断变化的内心想法，而是基于那些已经达到了足够确定性阈值的想法，这些想法已经足够清晰，能够让一个人将其发布到互联网上最大的公共论坛，供所有人阅读。所以，也许可以理解，LLM倾向于表现出过度自信——它们就是它们“吃”到的东西。

事实上，我认为可以公平地说，与许多诚实的人不同，LLM根本不会口头表达它们的确定性水平；它们不会调节语气来反映它们的确定性。因此，避免或减少幻觉的策略是通过将LLM所需的事实直接呈现在它面前，确保它无法忽视这些事实，从而为LLM的成功设置条件。这对于聊天机器人通常部署的场景是可行的，因为这些场景通常具有有限的范围。通常与该范围相关的文档会预先收集（在向量存储/数据库中），以便在需要时可以搜索到特别相关的文本片段，并与正在提出的问题一起提供给LLM，从而促使LLM在生成回应时能够利用这些片段。

## 从RAG到更丰富的

仍然存在许多可能导致问题的方式。为了解决这些问题，已经出现了一个完整的可配置工具包生态系统。NVIDIA的开源NeMo-guardrails可以过滤掉不安全和不恰当的回应，并帮助检查事实性。[John Snow Labs的LangTest](https://www.johnsnowlabs.com/langtest/) 宣称拥有“60+种测试类型，用于比较LLM和NLP模型的准确性、偏见、公平性、稳健性等。”两个最专注于回应真实性的工具包是 [Ragas](https://docs.ragas.io/) 和 [TruEra的TrueLens](https://www.trulens.org/)。

TrueLens（以及类似的Ragas）的核心理念是：每次调用RAG管道时，涉及三种相互关联的文本单元：*查询*、*检索的上下文* 和 *回应*；而管道的失败程度取决于这些单元之间是否存在语义差距。TruEra将其称为“RAG三元组”。换句话说，为了使RAG管道正常工作，必须成功地完成三件事：（1）检索的上下文必须足够相关；（2）生成的回应必须充分基于检索到的上下文；（3）生成的回应还必须与原始查询充分相关。该循环中的任何弱点都会导致RAG管道调用的弱点。例如：

**问题：** “哪个国家首先登月？”

**检索到的上下文**：“尼尔·阿姆斯特朗于 1969 年 7 月登上月球。巴兹·奥尔德林是飞行员。”

**生成的回应**：“尼尔·阿姆斯特朗和巴兹·奥尔德林在 1969 年登月。”

生成的回应与原始查询不够相关——第三个链接已损坏。

评估 RAG 还有许多其他细微差别，其中一些已经在[Adam Kamor 的 RAG 评估系列文章](https://www.tonic.ai/blog/rag-evaluation-series-validating-the-rag-performance-of-the-openais-rag-assistant-vs-googles-vertex-search-and-conversation)中讨论过。²¹

就真实性而言，RAG 策略是尽量通过从相关、可信的人工编写的现有文本中推导回应来避免幻觉。

## RAG 的不足之处

那么，RAG 策略如何与大众对语言 AI 的批评相协调呢？即它仅仅是在重复训练时使用的文本？它是否超越了简单的重复，能够处理反事实情况？RAG 策略基本上试图通过补充由人工精心策划的额外文本来避免幻觉，这些人工环节能够关注聊天机器人的特定应用场景。因此，人工环节通过提供相关事实文本的语料库来补充生成型大型语言模型（LLM）的训练。

小说类作品通常不包括在填充 RAG 向量存储的语料库中。即便是现有的虚构散文也无法涵盖理论上无限多的反事实命题，这些命题可能在某种意义上被认为是真实的或正确的。

但智力包括评估此类反事实命题的能力：

> “如果我踩进那个大水坑，我的脚踝以下会被打湿。”

在这种情况下，一个能够综合人类之前发出的言辞的 GenAI 系统——无论是来自 LLM 的训练集还是从下游检索并提供的上下文——并不令人印象深刻。与其说是原创推理，不如说它只是重复了别人已经说过的话。而当考虑到反事实情况时，重复已说过的话并不能达到目的。

## 这里有一对命题：

+   **在 GenAI 仅仅是在重复的情况下，它只是呈现或综合它所训练的言辞。**

+   **在 GenAI 能够克服单纯重复并进行准确推理的情况下，它能够成功处理反事实情况。**

Molak 解释的关于 **反事实** 的关键点是，它们“可以被认为是假设的或模拟的干预，*假定了一个特定的世界状态*”，或者正如 Pearl、Gilmour 和 Jewell 所描述的，反事实是对系统的最小修改（Molak, 28）。¹¹ ²² 重点是，正确回答反事实问题——甚至是合理回答——需要对世界有超越轶事的了解。对于 LLM，它们基于语料库的预训练，以及通过检索的事实文档填充的提示，使得它们的成功依赖于轶事的力量。而人类智能往往不需要，也无法依赖于轶事来进行反事实推理，这是为什么反事实推理在某些方面比忠实于事实更能衡量 LLM 的能力。

![](../Images/843511802bdffe6f89734d7a2d752556.png)

图片由 [sutirta budiman](https://unsplash.com/@sutirtab?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **运行开源 LLM：Ollama**

为了更具体地探讨与大型语言模型相关的反事实问题，我们通过运行一个生成模型来进行深入分析。为了减少障碍，我将通过下载一个模型并在本地计算机上运行来演示——因此你不需要 API 密钥。我们将使用 [Ollama](https://ollama.com/) 来完成这一过程。（如果你不想自己尝试，可以跳过本节的其余部分。）

Ollama 是一款免费工具，方便在本地计算机上运行开源 LLM。也可以在 DataBricks 以及可能的其他云平台上运行 Ollama。为了简便起见，我们将在本地运行。（对于这种本地设置，我要感谢 Iago Modesto Brandão 的实用教程 [Building Open Source LLM based Chatbots using Llama Index](https://medium.com/poatek/building-open-source-llm-based-chatbots-using-llama-index-e6de9999ee76)²³，以下内容即源自该教程。）

最简单的方法是：下载并安装 Docker（Docker 应用），然后在终端中运行几个命令来拉取并运行 Ollama 作为服务器，之后可以在 jupyter notebook 中访问（安装两个软件包后）。

下面是步骤：

1\. 下载并安装 Docker [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

2\. 启动终端并依次运行以下命令：

```py
docker pull ollama/ollama

docker run -d -v ollama:/root/.ollama -p 11434:11434 - name ollama ollama/ollama

pip install install llama-index==0.8.59

pip install openai==0.28.1
```

3\. 启动 jupyter：

`jupyter notebook`

4\. 在 jupyter notebook 中导入 Ollama 并创建 LLM 对象。为了加快速度，我们将使用一个相对较小的模型：微软的 *phi*。

现在我们已经准备好通过 Ollama 使用 Phi 生成文本来响应我们的提示。为此，我们使用 llm 对象的 **complete()** 方法。它会生成一个响应（可能需要一分钟左右），然后我们将其打印出来。

让我们尝试一个反事实：“水泥气球会浮起来吗？”

![](../Images/bb476bb26abbcb9d44fde17abb2c0175.png)

图片由 Haneen Krimly 提供，来自 Unsplash

接下来让我们看看 Phi 输出中的“推理”过程。

# **解读 LLM（生成式人工智能）对反事实问题的回答**

暂且不论微软的 Phi 不是一个非常强大的 GPT（生成预训练变换器），我们来看看它的回答：

```py
I don’t have personal beliefs or opinions, but based on my knowledge of 
physics, i can tell you that a cement balloon would not be able to float 
in air due to its low density. air is lighter than most materials used for
balloons, and the inflated rubber material of a typical balloon provides 
enough buoyancy to lift it into the sky. however, if you were to fill a 
cement balloon with helium gas or another lighter-than-air substance, it 
would likely float as long as the weight of the balloon itself is less than 
the upward force from the lifting gas.
```

这看起来有道理。如果一个普通气球（我直接用嘴吹起来的那种）不能在空气中浮起，那么一个外壳是水泥的气球也不会浮起来。但是，如果气球内充入的气体比空气轻，比如氦气，那么一个普通气球就会飘升到天空中。

![](../Images/6256a74db1c74ac7e8406c933abd12b9.png)

图片由 First Last @ rtisanyb 提供，来源于[Unsplash](https://unsplash.com/es/fotos/un-grupo-de-piedras-nOo9yg8pTgQ)

原始问题询问的是浮起的问题，但并没有明确指定介质；显然 Phi 假设它是空气。

那么现在让我们在问题中明确这一点，但选择水作为介质，毕竟这其实是我最初的意图。

```py
No, a cement balloon would not float in water. Cement is a very dense material
and does not have much air trapped inside it, so it cannot create buoyancy 
like a regular balloon filled with helium or air. Additionally, the weight of 
the cement balloon combined with its density would make it sink to the bottom 
of the water rather than floating on the surface.
```

很有趣。不仅得出它会下沉的结论，现在还假设介质是水而不是空气。它推理水泥的密度与空气相比，但没有考虑气球内部的空气。或者它可能在推理一个固体水泥球；不过，嗯，那可不是气球。除非，它所说的“水泥气球”是指类似于水气球：一个充满水的气球。

让我们再次修改问题，这次明确介质是水。

```py
No, a cement balloon filled with helium would not float on water. This is 
because helium is lighter than air and therefore lifts the balloon upwards 
when it is released into the air. However, cement is much denser than water 
and thus will sink to the bottom of the water. When you combine these two 
facts, the result is that the concrete balloon filled with helium would not 
float on water but instead sink to the bottom due to its weight.

In summary, while the helium in the balloon provides buoyancy to keep it 
afloat in air, the density of the cement itself makes it too heavy for the 
helium to lift and keep it afloat in water. Thus, a cement balloon filled 
with helium would not float on water but instead sink to the bottom due to 
its weight. 
```

嗯，这有点奇怪。根据这个最新的回答，同样一个会在空气中浮起的物体，在水中却会下沉，尽管水的密度比空气大。确实，这是一种反事实的情境——谁见过水泥气球呢？不过让我们尝试想象一下：你站在一个水池旁，或者你可能在一艘船上。你手里拿着这个球形物体，假设在腰部位置。你放开它，它开始向空中浮起，但你在它还没有超出你的接触范围之前抓住了它。现在你把这个同样的球形物体放到水面上。当它接触到水面时，你慢慢地轻轻放开它。你觉得会发生什么呢？

这个充满氦气的水泥气球，刚刚还开始飘升到天空，现在是否会突然被水吞噬，沉入水底呢？这个感觉靠谱吗？

请记住我们自身的体现性知识，这在这里可能是相关的：我们自己的肉体——它们甚至没有轻到可以自发浮起——当放在水面上时，实际上不会下沉，而是会浮起来。这种体现性知识表明，一个足够轻以至于能够浮起的物体，也就比我们轻，因此它也足够轻，能够在任何地方浮动，如果我们（比那个物体重）能够在水面上漂浮的话。（请参阅 Lakoff & Johnson 了解体现认知的概述²⁴）

当然，GPT语言模型像Phi这样的模型，显然是没有实体的；它缺乏体现的知识。在这种情况下，也许正是Phi缺乏的这种体现的知识，影响了它的反事实推理能力。

好的，但也许这里的问题是上下文和连续性。我上面展示的并不是一个真实的对话，就像与聊天机器人互动那样；我没有把之前的上下文传递给Phi，而是问了孤立的问题。所以，或许Phi在回答不同问题时，想象了不同的水泥与氦气的比例。毕竟，如果水泥足够厚，或者它内部的氦气腔室足够小，那么氦气相对于空气（或水）的轻盈就不足以抵消水泥的重量。所以让我们重新措辞问题，澄清这一点。

```py
It is unlikely that a cement balloon would be able to float in water since 
cement is denser than water. The reason why the cement balloon might float 
in air is due to the helium gas inside, which has a lower density than both 
air and cement. In water, the buoyant force exerted by the water on the cement 
balloon will be much less than the weight of the balloon itself. Hence, the 
cement balloon would sink in water rather than float as it does in the air.
```

好的，但似乎还有问题。在同一个回答中，它断言水泥气球足够轻，可以飘浮到空中，但又太重，无法在水中浮起。这不是意味着水比空气轻吗？嗯，

那件事有点不对劲——但那不是水的问题。

我不是第一个提出GPT语言模型（像Phi这样的模型，通过在大量文本语料库上的训练获得能力）有时缺乏基本推理能力的人。在这种情况下，我提出的是一些推理能力如何源于隐性体现的知识——我们通过身体在物质世界中的存在获得的知识（我们的身体不会漂浮到空中，但确实会漂浮在水上）。我还在建议，反事实问题如何有助于揭示这一点。

GPT模型，比如Phi，主要是通过联想来运作的。GPT和大型语言模型（LLMs）通常掌握得非常好的，正是语言使用中的联想或内涵方面。在内涵上，基于最常见的用法，‘水泥’一词与沉重相关。它和重金属铅有相同的联想——“Led Zeppelin”最初就是一种讽刺，暗示新“飞鸟”乐队将无法飞翔。虽然如果把足够的空气注入其中，水泥可能变得轻，但这不是一个容易想到的联想。相反，这是一个反事实，一个思想实验。即使我之前的措辞提到“非常薄的一层水泥……充满了氦气”，但“水泥”这个材料本身，太强烈地与沉重相关，使Phi无法想象它在水中漂浮——毕竟从来没有人造过水泥船！

![](../Images/5fc98ae150f9906b8e3018fd3fbf97a1.png)

图片由[Tim Huyghe](https://unsplash.com/@huyghetim?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

那我们就换个东西，把水泥换成一个没有那么沉重联想的物质。比如玻璃怎么样？

```py
As an AI language model, I do not have personal opinions or beliefs, but based 
on scientific principles, a glass balloon filled with helium would indeed 
float in water. This is because the density of helium gas is much lower than 
that of water, causing it to rise and displace its weight in water. 
Additionally, glass is less dense than water, which means that it would also 
be buoyant enough to keep the balloon afloat on top of the water’s surface. 
However, if the balloon were to be filled with a denser gas or a heavier 
object, such as sand, then it may sink or become too heavy to float.
```

啊哈！在联想（内涵）障碍被排除之后，Phi能够得出与人类推理相当的推理，充满了隐性具体现实的知识，即使在这种情况下，后者并非来源。在这个例子中，Phi揭示了关键点：“氦气的密度远低于水的密度”，尽管它在“附加”点中搞混了，声称玻璃的密度低于水的密度，这其实是幻觉，根本不是真的。

# **OpenAI的ChatGPT怎么样？**

然而，显而易见的一个问题是，微软的Phi距离成为最先进的LLM还很远。它足够小，可以在笔记本电脑上本地运行。那么，OpenAI的GPT模型怎么样呢？

要运行下面的代码，你需要在[https://openai.com](https://openai.com)上免费注册，获取一个OpenAI API访问令牌。

OpenAI的GPT对于氦气填充的玻璃气球在水中的表现有什么看法？

```py
No, a glass balloon filled with helium cannot float in water, regardless of 
its size.  The buoyancy of an object in a fluid is determined by the density 
of the object compared to the density of the fluid. Density is defined as mass 
divided by volume. In simple terms, if the object is less dense than the fluid,
it will float; if it is more dense, it will sink.  In this case, the glass 
balloon is filled with helium, which is a gas that is less dense than water. 
Helium-filled balloons float in the air because the density of helium is lower 
than that of the surrounding air.  However, when we place the helium-filled 
glass balloon in water, the density of the water is significantly higher than 
that of helium. Even though the glass balloon is very thin and lightweight, 
the overall density of the balloon, including the glass and helium, will still 
be higher than that of water.  Therefore, the balloon will not displace enough 
water to generate enough buoyant force to counteract the weight of the balloon 
itself. As a result, the balloon will sink in water rather than float.
```

哦不！就连OpenAI的GPT 3.5 turbo也陷入了水泥的内涵中。它知道“氦气充满的气球会浮在空气中”。它知道氦气比水的密度小。但随后，它错误地认为“气球的整体密度，包括玻璃和氦气，仍然会高于水的密度。”就像Phi上面做的那样，OpenAI的GPT 3.5 turbo暗示气球比水重，但比空气轻，这就意味着水比空气轻。

我们知道它是错误的；但它并不是因为缺乏事实或直接与事实相矛盾而错误：整个水泥气球的情景远非事实；它是反事实的。

事后我们可以运用*归谬法*推断出，Phi和OpenAI的GPT 3.5 turbo的负面结论确实与另一个事实相矛盾，即水比空气重。但在这一点上，反事实推理实际上是一种推理，而不仅仅是做白日梦。也就是说，反事实推理可以被证明是确定的真或假。尽管它偏离了事实，但它实际上和基于事实的推理一样，也是推理的一种形式。

# **事实、虚构与幻觉：反事实给我们展示了什么**

自从ChatGPT在2022年底席卷公众意识以来，随之而来并持续引发的主要担忧就是幻觉。哦，天哪，一个AI系统居然会声称一些没有事实依据的事情！但是，与其将事实作为AI系统的主要标准——就像许多商业用例中那样——现在看来，事实与虚构并不是衡量AI系统是否成功的唯一维度。即使一个AI系统的回应是基于事实，它仍然可能是无关的，或者是个不合逻辑的推论，这就是为什么像Ragas和TruVera这样的评估方法特别关注回应的相关性。

当在相关性标准上失败时，甚至不涉及事实与虚构的轴线。一个无关的回答可以与相关的回答一样是事实，并且根据定义，反事实推理，无论其是否正确，都不是字面意义上的事实，当然也不是由RAG系统所构成的那种事实。也就是说，反事实推理并不是通过检索与提出的问题主题相关的文档来实现的。反事实推理的强大之处在于它如何运用类比来引入那些看似完全与问题无关的事实体系。它可能像这样被图示化：  

![](../Images/601c8456b6defd2cc80c76c6dab2c57e.png)

图片由作者提供  

也可以以这种方式表示其中的一些方面：  

![](../Images/d741ae68d8857c9d169222ccb9e9d699.png)  

图片由作者提供  

**线性估计器与此有何关联？** 由于反事实推理并非基于看似相关的事实，而是基于系统化的事实，有时这些事实来自其他领域，或是与主题相远的内容，因此它并不是直接从文档存储检索系统中明显获益的东西。这里可以类比线性估计器的类型：梯度提升树线性估计器本质上无法在特征大大超出训练数据的数值范围时做出准确预测；这是因为决策切分点只能基于训练时提供的数据来做出。相比之下，回归模型（其可以有封闭解形式）能够在特征超出训练数据的数值范围时做出准确预测。  

从实践角度来看，这就是为什么线性模型在商业应用中可能会有帮助。只要你的线性模型准确，它可以帮助你预测提高或降低产品售价后，超出你曾经提供过的任何价格时的结果：一个*反事实价格*。而一个在验证中表现同样良好的梯度提升树模型并不能帮助你推理这些反事实，这有些讽刺，因为开发该模型的初衷可能正是为了应对这种情况。从这个意义上说，线性模型的*可解释性*与SHAP值提供的完全不同，因为后者对于模型训练数据分布之外的数据会显得毫无解释力。  

LLM的强大表现无疑证明了从人群外包写作的文本中巧妙合成的“智能”比预期的要强大得多。显然，这超越了过去将“智能”寄托于概念理解的趋势，特别体现在超越事实进行准确推理的能力上。因此，我发现挑战LLM达到这一标准是非常有趣的，这挑战了它们的基本特性。  

![](../Images/0c9a595810c03384dc2ad6a930b2b82b.png)  

Bonnie Moreland 来自 Freerange Stock  

# **反思**

反事实不仅仅是轻浮的娱乐，它们在科学进步中发挥了作用，体现了查尔斯·桑德斯·皮尔士所说的*归纳推理*，这与归纳（归纳推理）和演绎（演绎推理）不同。归纳基本上是指假设的提出。我们可能会问：我们应该期望LLM具备这种能力吗？有什么优势？我没有一个确定的答案，更像是一个猜测：在生成式AI（GenAI）社区中，广为人知的是，当给LLM提供提示，要求其“逐步推理”时，通常会得到更令人满意的回答，即使推理步骤本身并不是预期的回答。换句话说，出于某种尚未完全理解的原因，让LLM在某种程度上模拟人类推理中最可靠的过程（逐步思考）会导致更好的最终结果。或许，虽然LLM并没有像人类那样进行训练推理，但人类推理的传承普遍有助于AI的最终结果。在这种情况下，考虑到归纳在科学发展中的重要作用，AI的最终结果可能会随着LLM能够进行反事实推理而得到改进。

在[LinkedIn](https://www.linkedin.com/in/joshua-banks-mailman/)上访问我

[1] [底线](https://www.heraldscotland.com/business_hq/13103840.bottom-line/)。 (2013) 《赫拉尔德报》。 5月8日

[2] C. Munn, 《代际投资：联盟信托的历史》(2012)

[3] R. Strassfeld. [如果……：法律中的反事实](https://scholarlycommons.law.case.edu/cgi/viewcontent.cgi?referer=&httpsredir=1&article=1372&context=faculty_publications)。 (1992) 凯斯西储大学法学院学术共享平台

[4] S. Wachter, B. Mittelstadt, & C. Russell, [在不揭开黑箱的情况下进行反事实解释：自动化决策与GDPR](https://jolt.law.harvard.edu/assets/articlePDFs/v31/Counterfactual-Explanations-without-Opening-the-Black-Box-Sandra-Wachter-et-al.pdf)。2018年。 《哈佛法律与技术期刊》，第31卷，第2期

[5] J. Stoyanovich, [Julia Stoyanovich在纽约市消费者和工人保护部关于2021年地方法第144条与自动化就业决策工具（AEDTs）相关事宜的证词](https://rules.cityofnewyork.us/wp-content/uploads/2022/09/Stoyanovich_LL144_October24_2022.pdf)。 (2022) 负责任AI中心，纽约大学坦顿工程学院

[6] S. Lorr, [一项招聘法为人工智能监管开辟了道路](https://www.nytimes.com/2023/05/25/technology/ai-hiring-law-new-york.html) (2023) 《纽约时报》，5月25日

[7] J. Stoyanovich, [“只有在你尝试监管时，你才会学会如何做”：Julia Stoyanovich讨论《纽约时报》中的负责任AI](https://nyudatascience.medium.com/until-you-try-to-regulate-you-wont-learn-how-julia-stoyanovich-discusses-responsible-ai-for-4cf119002b05) (2023)，Medium。 6月21日

[8] J. Pearl, [因果推断基础](https://ftp.cs.ucla.edu/pub/stat_ser/r355-reprint.pdf)（2010）。《社会学方法学》。第40卷，第1期。

[9] L. Wittgenstein. 《哲学调查》（1953）由G.E.M. Anscombe和R. Rhees编，G.E.M. Anscombe翻译，牛津：Blackwell出版。

[10] E. Siegel. [AI行动指南：掌握机器学习部署的稀有艺术](https://mitpress.mit.edu/9780262048903/the-ai-playbook/)（2024）MIT出版社。

[11] A. Molak. [Python中的因果推断与发现](https://www.packtpub.com/product/causal-inference-and-discovery-in-python/9781804612989)（2023）Packt出版。

[12] R. Byrne. 《理性想象：人们如何创造现实的替代品》（2005）MIT出版社。

[13] W. Starr. [反事实](https://plato.stanford.edu/entries/counterfactuals/)（2019）斯坦福哲学百科全书。

[14] M. Ginsburg. [反事实](https://www.ijcai.org/Proceedings/85-1/Papers/015.pdf)（1985）第九届国际人工智能联合会议论文集。

[15] J. Pearl, Judea. 《因果关系、行动与反事实》（1995）收录于《计算学习与概率推理》。约翰·威利与儿子出版社。

[16] T. Costello和J. McCarthy. 《有用的反事实》（1999）Linköping电子计算机与信息科学文献，第4卷，第12期。

[17] F. McEvoy. [2024年抚养“宝宝AI”](https://fionajmcevoy.medium.com/raising-baby-ai-in-2024-1b7d704cd3d5)（2024）Medium。

[18] N. Berger. [为什么小猫喜欢把东西弄倒？](https://www.northtorontocatrescue.com/why-do-kitties-knock-things-over/)（2020）北多伦多猫救援。

[19] C. S. Peirce. 《查尔斯·桑德斯·皮尔士文集》（1931–1958），由C. Hartshorne、P. Weiss和A. Burks编辑。哈佛大学出版社。

[20] I. Douven. [皮尔士的溯因](https://plato.stanford.edu/entries/abduction/peirce.html)（2021）斯坦福哲学百科全书。

[21] A. Kamor. [RAG评估系列：验证OpenAI的RAG助手与谷歌的Vertex搜索与对话性能](https://www.tonic.ai/blog/rag-evaluation-series-validating-the-rag-performance-of-the-openais-rag-assistant-vs-googles-vertex-search-and-conversation)（2024）Tonic.ai。

[22] Pearl, J., Glymour, M., & Jewell, N. 《统计学中的因果推断：入门》（2016）。纽约：Wiley出版社。

[23] I. M. Brandão. [使用Llama Index构建基于开源LLM的聊天机器人](https://medium.com/poatek/building-open-source-llm-based-chatbots-using-llama-index-e6de9999ee76)（2023）Medium。

[24] G. Lakoff和M. Johnson. 《肉体中的哲学：具身心灵与它对西方思想的挑战》（1999）Basic Books出版社。
