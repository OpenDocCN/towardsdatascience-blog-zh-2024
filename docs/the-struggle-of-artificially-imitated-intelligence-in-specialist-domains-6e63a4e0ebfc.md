# 人工模仿智能在专业领域中的挣扎

> 原文：[`towardsdatascience.com/the-struggle-of-artificially-imitated-intelligence-in-specialist-domains-6e63a4e0ebfc?source=collection_archive---------4-----------------------#2024-05-08`](https://towardsdatascience.com/the-struggle-of-artificially-imitated-intelligence-in-specialist-domains-6e63a4e0ebfc?source=collection_archive---------4-----------------------#2024-05-08)

## 为什么通向真正智能的道路要经过本体论和知识图谱

[](https://medium.com/@konstantin.vasilev.phd?source=post_page---byline--6e63a4e0ebfc--------------------------------)![Konstantin Vasilev](https://medium.com/@konstantin.vasilev.phd?source=post_page---byline--6e63a4e0ebfc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e63a4e0ebfc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e63a4e0ebfc--------------------------------) [Konstantin Vasilev](https://medium.com/@konstantin.vasilev.phd?source=post_page---byline--6e63a4e0ebfc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e63a4e0ebfc--------------------------------) ·阅读时间 15 分钟·2024 年 5 月 8 日

--

关注我的人可能还记得一年前，我以“脾气暴躁的风险经理”这一化名发过一篇类似的 [AI rant](https://www.linkedin.com/posts/konstantin-vasilev_ai-ml-chatgpt-activity-7018125021319450624-J8sq?utm_source=share&utm_medium=member_desktop) 。现在我回来了，比以前更加暴躁，带来了具体的例子，但也有解决方案的想法！

![](img/e7f6e3d27957d8ee3f07ad6a49f780f1.png)

来源：作者拼贴

# 介绍

+   大型语言模型（LLMs）如 ChatGPT，在用自然语言讨论一般话题方面展现出令人印象深刻的能力。

+   然而，它们在医学、金融和法律等专业领域中表现得非常吃力。

+   这是因为缺乏真正的理解，更多侧重于模仿而非智力。

大型语言模型（LLMs）正处于 hype（炒作）的巅峰。由于它们能够用自然语言回答和讨论泛泛的话题，因此被认为是“智能”的。

然而，一旦你深入到医学、金融、法律等专业/复杂领域，就容易发现逻辑不一致、明显错误以及所谓的“幻觉”。简而言之，大型语言模型表现得就像一个拥有非常丰富词汇的学生，试图假装他们已经为考试做了准备，并知道所有的答案，但实际上他们并不懂！他们只是因为手头有大量的信息而假装聪明，但他们使用这些信息进行推理的能力非常有限。我甚至愿意进一步说：

> 所谓的人工智能（AI）实际上常常是人工模仿智能（AII）。在像医学或金融这样专业的领域尤其如此，因为在这些领域中的错误可能会导致人类伤害和财务损失。

让我举一个我过去 10 年所从事的领域——金融风险的真实例子。它被认为是“专业”的一个有力证据是，必须提供大量背景信息，以便普通人理解这个话题：

1.  银行必须遵守监管资本要求。

1.  资本可以看作是一个吸收财务损失的缓冲区。

1.  资本持有要求确保银行有足够的能力吸收损失，从而降低破产和金融危机的可能性。

1.  第一部分设定要求的规则是基于风险比例原则：

    **→** 银行承担的业务越风险越大

    **→** 更高的风险权重

    **→** 更高的资本要求

    **→** 更大的损失缓冲区

    **→** 稳定的银行

1.  第四部分中风险程度通常以银行与其合作的公司信用评级的形式进行衡量。

1.  信用评级来自不同的评级机构，并且格式各异。

1.  为了标准化评级，监管机构已经制定了从每种评级格式到标准化信用质量步骤（CQS）的映射规则，范围从 1 到 6。

1.  那么第四部分中确定风险权重的监管规则是基于 CQS 的。

1.  第八部分中针对欧洲银行的规则设定在资本要求监管条例（CRR）中。

上面 9 个陈述中的话题看起来很复杂，确实是如此，存在许多附加的复杂情况和案例，但我故意避开了这些，因为它们甚至不必要用来说明 AII 与这些话题的斗争。此外，复杂性并不是来自单个的 9 条规则本身，而是来自它们的组合，许多概念的定义是基于其他几个概念，从而产生了一个语义网络/图谱，连接着这些概念和规则。

现在让我们只关注第四部分的核心概念和规则，可以总结如下：**评级 → CQS → 风险权重 → 资本要求**。如果我们还看一下一个例子，[CRR 第 122 条](https://www.eba.europa.eu/regulation-and-policy/single-rulebook/interactive-single-rulebook/16060)，我们可以看到一个映射表**CQS → 风险权重**，该表应当用于公司暴露的情况。

![](img/431f996bf908c5eae9e04dd8c578e027.png)

资本要求监管条例（CRR 122）

这个映射规则非常简单，普通人通过一点背景信息就能轻松理解。它也为许多银行专家所熟知，他们中的大多数并不记住确切的规则，而是仅仅知道上面 9 个陈述中的信息，并知道在哪里查找以便看到确切的映射。

# 人工模仿智能

现在让我们提出一个问题，看看 AII 能否处理它，以下是我问 ChatGPT 3.5 的问题：

> 根据 CRR，针对信用质量步骤为 5 的企业，风险权重是多少？

这是答案：

> 在《资本要求条例》（CRR）中，企业实体的风险暴露的风险权重取决于其信用质量步骤。**在信用质量步骤的背景下，信用质量步骤为 5 通常代表最高的信用质量，表明违约风险非常低**。
> 
> 在 CRR 中概述的标准化方法中，企业的风险暴露根据其信用评级或信用质量步骤分配风险权重。针对企业的风险暴露的风险权重范围从 20%到 150%，取决于信用质量步骤以及该暴露是短期还是长期。
> 
> **由于信用质量步骤为 5 表示非常低的风险，分配给这种风险暴露的风险权重将是最低之一。在这种情况下，对于信用质量步骤为 5 的企业，风险权重可能为 20%**或类似，表示银行持有这种暴露的资本需求较低。

你能找出用**粗体**标出的事实错误吗？

1.  第一个非常大的错误是，AII 完全误解并颠倒了**CQS → 风险权重**的关系，CQS 为 5 的高信用质量并不代表低风险，而恰恰相反，它代表的是最高风险之一！**这实际上是回答这个问题时可能犯的最大错误！**

1.  即使在第 1 个错误之后，我仍然希望 AII 能够查阅文章中的表格，并得出结论：当 CQS 低于 5 时，风险权重为 150%。但不，AII 自信地声称风险权重为 20%，因为风险很低……

1.  尽管不应得，但我仍然给予 AII 怀疑的余地，通过询问同样的问题，并明确指出具体的 CRR 条款：122。厚颜无耻，但自信满满，AII 此时回应称风险权重应该是 100%，仍然声称 CQS 为 5 的信用质量较好**→** 另一个明显的错误。

> 我感到自己的工作是安全的，而且金融行业仍然需要我，因此我开始考虑解决方案，讽刺的是，这些解决方案可能会在未来让我的工作变得不安全……

# 为什么是本体论和知识图谱？

引入本体论，本体论是某一特定领域的知识表示形式。可以通过以下方式思考本体论：将不同的知识表示方法从最简单到最复杂进行排序：

1.  **数据字典：** 包含字段名称和元数据属性的表格

1.  **分类法：** 通过关系的嵌套形式，添加数据类型和子类型的表格（例如：鸽子 <是> 鸟类的一种）

1.  **本体论（Ontology）**：具有多种关系类型的多维分类法（例如，鸟类 <吃> 种子）“分类法与面向对象编程的邪恶结合” ([Kurt Cagle, 2017](https://www.linkedin.com/pulse/data-dictionaries-arent-ontologies-kurt-cagle/?trackingId=NvradWFCRsuQ8ucCGeqPPw%3D%3D))

为什么要在数据中引入如此复杂的关系结构？以下是一些好处，稍后将通过示例加以说明：

1.  **统一的** 表现形式：**结构**、**数据**和**逻辑**。在上述示例中，鸟类是一个类，它是一个具有通用属性的模板 = **结构**。在本体论中，我们还可以定义许多实际的鸟类实例，并为它们赋予各自的属性 = **数据**。最后，我们还可以添加**逻辑**（例如，如果一只鸟 <吃> 超过 5 粒种子，则 <它> 不再饿）。这实际上是通过将一些逻辑作为数据本身的一部分，使数据“变得智能”，从而使其成为可重用的知识。它还使信息既能被人类阅读，也能被机器读取，这在机器学习中尤其有用。

1.  **可解释性与溯源（Explainability and Lineage）**：本体论最常见的实现方式是通过资源描述框架（RDF）以图形形式表现。这些图形随后可以进行查询，以评估现有规则和实例或添加新的规则和实例。此外，通过图形的节点和边，可以追溯思维链条，解释查询结果，避免机器学习中的黑箱问题。

1.  **推理与推断（Reasoning and Inference）**：当新增信息时，语义推理器可以评估该信息对图形的影响。此外，新的知识还可以通过“假如”问题从现有知识中推导出来。

1.  **一致性（Consistency）**：任何冲突的规则或偏离通用类属性的实例会被推理器自动识别为错误，并且不能成为图形的一部分。这非常有价值，因为它强制执行特定领域内知识的一致性，消除任何主观解释。

1.  **互操作性与可扩展性（Interoperability and Scalability）**：可重用的知识可以专注于特定的专业领域，或连接不同的领域（例如，金融领域的[财务本体（FIBO）](https://github.com/edmcouncil/fibo)，数学领域的[OntoMathPRO](https://ontomathpro.org/)，医学领域的[OGMS](https://www.ebi.ac.uk/ols4/ontologies/ogms)）。此外，用户还可以下载一个通用的行业本体，并以实例和自定义规则的形式，通过私有企业数据扩展它。

本体论可以被认为是 AI 最早和最纯粹的形式之一，远在大型机器学习模型成为主流之前，完全基于通过结构化使数据智能化的理念。在这里，AI 指的是真正的智能——本体论能够解释给定规则评估结果的原因是因为它对事物的运作方式有语义理解！这一概念最早在 2000 年代初期作为语义网思想而流行，代表着从链接应用的互联网（Web 2.0）和链接页面的互联网（Web 1.0）到链接数据的互联网（Web 3.0）的演变。

知识图谱（KGs）是存储数据的一种更为通用的图形格式术语，它不一定遵循本体论和语义原则，而后者通常以 KG 的形式呈现。如今，随着大型语言模型（LLMs）的崛起，KGs 常被视为解决其在专业领域弱点的良好候选者，这反过来又复兴了本体论及其 KG 表示的概念。

这导致了非常有趣的范式趋同：

1.  本体论旨在通过结构化数据使数据变得智能，从而生成智能。

1.  LLMs 旨在通过将数据保持非结构化的同时，使模型变得非常大且结构化，从而生成智能：ChatGPT 具有约 1750 亿个参数！

> 显然，目标是相同的，数据是否成为模型的一部分，或者模型是否成为数据的一部分，实际上只是参考框架的问题，最终不可避免地导致一种**信息奇点**的形式。

# 为什么要在银行业中使用本体论？

1.  **专业化：** 如上所述，LLMs 在金融等专业领域面临挑战。尤其在错误代价高昂的领域，这一问题尤为严重。此外，在专家较少的专业领域，自动化所带来的附加值，往往远高于通用领域的自动化（例如，替代银行专家与支持人员）。

1.  **审计追踪：** 当财务项目在财务报表中被评估和汇总时，监管机构和审计师期望能够从所有细粒度的输入和规则到最终的汇总结果之间，保持持续的审计追踪。

1.  **可解释性：** 专业人士依赖于对银行运营机制的深入理解，以及风险驱动因素对其投资组合和商业决策的影响。此外，监管机构明确要求通过定期的“如果”演练（如压力测试）来获得这种理解。这也是机器学习在核心银行领域采用不佳的原因之一——即所谓的“黑箱问题”。

1.  **客观性和标准化：** 缺乏解释性和主观性确保了行业中的公平竞争和规章制度的有效性，进而保障了金融稳定性。

现在，想象一个完美的世界，其中如 CRR 这样的法规以本体论的形式提供，而非自由文本。

1.  每家银行都可以导入本体标准，并用自己的私有数据和投资组合特征扩展它，进而评估所有的监管规则。

1.  此外，单独的企业战略也可以与监管约束相结合，从而实现自动化的财务规划和优化决策。

1.  最后，大规模规则和数据图的复杂复合影响可以被解开，以解释最终结果，并提供对之前不明显关系的洞察。

以下示例旨在通过最小的努力实现最大的效果来说明这些想法！

# 示例

在寻找解决所示 LLM 弱点的过程中，我设计了以下示例：

1.  创建一个以知识图谱形式呈现的本体。

1.  定义实体的**结构**，添加单个**实例/数据**以及**控制**它们交互的逻辑，遵循 CRR 法规。

1.  使用知识图谱来评估风险权重。

1.  请 KG 解释它是如何得出这个结果的。

为了创建简单的本体，我使用了[CogniPy](https://cognipy.org/index.html)库，其主要优点包括：

+   使用受控自然语言（CNL）来编写和查询本体，意味着无需了解特定的图查询语言。

+   可视化的实体化知识图谱。

+   具有解释结果能力的推理器。

# 结构

首先，让我们从定义本体的结构开始。这类似于在面向对象编程中定义不同属性和约束的类。

在第一个 CNL 语句中，我们定义了公司类及其属性。

```py
Every company has-id one (some integer value) and has-cqs one (some integer value) and has-turnover (some double value).
```

需要注意的几点是，类名使用小写字母（company）。不同的关系和属性使用连字符命名法定义，而数据类型则定义在括号中。渐渐地，这看起来越来越像是一种基于普通英语的完全成熟的编程语言。

接下来，我们展示另一种通过通用类语句标识公司唯一性的方法，基于其 ID。

```py
Every X that is a company is-unique-if X has-id equal-to something.
```

# 数据

现在让我们添加一些数据或公司类的实例，实例名以大写字母开头。

```py
Lamersoft is a company and has-id equal-to 123 and has-cqs equal-to 5 and has-turnover equal-to 51000000.
```

在这里，我们添加了一个特定公司 Lamersoft 的数据点，并为其属性赋值。当然，我们不限于一个数据点，我们可以在同一本体中拥有成千上万或数百万个数据点，它们可以与**结构**或**逻辑**组件一起或单独导入。

现在我们已将**数据**添加到**结构**中，可以第一次查询本体，获取所有公司，返回与查询匹配的实例的 DataFrame：

```py
onto.select_instances_of("a thing that is a company")
```

![](img/459dc93ed116228cec57669b95e9505c.png)

包含查询结果的 DataFrame

我们还可以绘制我们的知识图谱，展示 Lamersoft 实例与一般公司类之间的关系：

```py
onto.draw_graph(layout='hierarchical')
```

![](img/d49486c8659c942f3ef52d7ca14970eb.png)

本体图

# 逻辑

最后，让我们添加一些简单的规则，实现 CRR 风险权重的公司相关规定。

```py
If a company has-turnover greater-than 50000000 then the company is a corporate.
If a corporate has-cqs equal-to 5 then the corporate has-risk-weight equal-to 1.50.
```

第一条规则定义了什么是公司，通常是年营业额超过 5000 万的公司。第二条规则实现了部分 CRR 映射表**CQS → 风险权重**，这对于大语言模型（LLM）来说是非常难以理解的。

在添加规则后，我们已经完成了本体的构建，可以重新绘制知识图谱：

![](img/de5bb6782706d8277b078d70e65dc6f1.png)

已评估规则的本体图

值得注意的是，知识图谱在我们将**逻辑**添加到**结构**和**数据**后，自动做出了两个重要的推论：

1.  Lamersoft 由于其营业额属性和公司分类规则，被识别为公司。

1.  Lamersoft 的风险权重已经根据其 CQS 属性和 CRR 规则进行了评估。

这一切都源于本体中所有信息的魔法般自动一致性（无冲突）。如果我们添加任何与现有信息相矛盾的规则或实例，推理器将报错，并且知识图谱将无法生成。

现在我们还可以与推理器进行互动，询问为何做出某一评估，或者是什么样的思维链和审计追踪导致了该评估：

```py
printWhy(onto,"Lamersoft is a corporate?")
```

```py
{
  "by": [
    {
      "expr": "Lamersoft is a company."
    },
    {
      "expr": "Lamersoft has-turnover equal-to 51000000."
    }
  ],
  "concluded": "Lamersoft is a corporate.",
  "rule": "If a company has-turnover greater-than 50000000 then the company is a corporate."
}
```

无论输出格式如何，我们仍然可以清晰地看到，通过两个表达式来定义 Lamersoft 作为公司及其特定的营业额，得出的结论是，它符合特定营业额条件，因此被认定为公司。不幸的是，当前的库实现似乎不支持对风险权重结果的解释，这为未来的想法部分提供了思考。

尽管如此，我认为这个例子是成功的，因为它成功地将**结构**、**数据**和**逻辑**统一到一个单一的可扩展本体中，且仅用了最少的努力和资源，使用的是自然英语。此外，它还能够对规则进行评估并用完整的审计追踪来解释它们。

有人可能会说，好的，我们到底实现了什么呢？这不过是另一种更接近自然英语的编程语言，人们可以用 Python 类、实例和断言来做同样的事情。这个说法是对的，任何编程语言本质上都是人机之间的通信协议。同时，我们也可以清楚地看到，编程语法正在逐步向人类语言靠拢的趋势，从专注于实现实际业务概念和互动的领域驱动设计（DDD），到集成开发环境（IDE）中的 LLM 插件，用自然语言生成代码。这已经成为一种明确的趋势：

> 程序员作为业务和技术之间中介的角色正在发生变化。如果前者可以直接从业务问题的自然语言规范中生成，后者则可以通过解释者生成逻辑的自然语言定义，那么我们还需要代码和业务文档吗？

# 结论

想象一个世界，其中所有银行监管要求由监管机构集中提供，而不是以文本形式，而是以本体论或智能数据的形式，包含所有结构和逻辑。各个银行可以导入中央本体论并用自己的数据进行扩展，从而自动评估所有规则和要求。这将消除任何主观性和解释的空间，并确保结果的完整审计追踪。

除了监管之外，企业还可以开发自己的本体论，编码、自动化并重用他们专家的知识或不同的计算方法和治理流程。在企业层面，这样的本体论可以增加价值，强制执行一个共同的词汇表和规则理解，减少因解释和分歧所浪费的精力，这些精力可以转向以本体论形式构建更多的知识。相同的概念也可以应用于任何专业领域，其中：

+   单纯的文本关联是不够的，LLMs 在这方面仍然存在困难。

+   进行有效机器学习训练的大数据尚不可得。

+   高素质的专家可以得到真正人工智能的帮助，从而减少成本和错误的风险。

> 如果如今数据被视为与黄金一样有价值，那么我相信真正的“钻石”是结构化数据，我们可以称之为知识。这种以本体论和知识图谱形式呈现的知识，也可以像数据现在为营销目的而交易一样，在公司之间进行交易。**谁知道，也许这会发展成一种按节点付费的商业模式，其中智能数据形式的专业知识可以作为产品或服务出售。**

那么，我们可以将智能定义为我们积累知识并查询这些知识以获得可操作见解的能力。这可以发展成专门的人工智能，通过接入本体论来获得某一领域的专业知识，从而减少错误推断（hallucinations）。

LLMs（大型语言模型）已经开始对公司利润产生影响 —— [Klarna 预计通过 ChatGPT 处理大部分客户服务对话，将提升 4000 万美元的利润](https://www.pymnts.com/buy-now-pay-later/2024/klarna-boosts-profits-with-chatgpt-bnpl-firms-tap-artificial-intelligence/)，从而减少了人工客服的成本。

然而，注意 LLM 的具体应用领域！这并不是金融公司（如 Klarna）中更为专业的领域，如金融/产品规划或资产和负债管理。这是一般的客户支持服务，许多公司中这是入门级职位，已经使用了许多标准化的响应或程序。这是最容易应用人工智能的领域，但也是增值可能不最大的位置。此外，由于缺乏真正的智能，LLM 的错误推断风险依然存在。尤其在金融服务行业，LLM 提供的任何“金融建议”都可能带来法律和监管的后果。

# 未来的想法

LLMs 已经在所谓的检索增强生成（RAG）中利用了知识图谱。然而，这些图谱是通用概念，可能包含任何数据结构，并不一定代表本体，而 LLMs 使用本体的情况相对较少探索。这给了我以下下一篇文章的构思：

1.  使用通俗的英语查询本体，避免依赖特定的 CNL 语法——这可以通过 NLP 模型实现，模型生成查询以访问存储本体的知识图谱——[与 KGs 对话](https://www.youtube.com/watch?v=Kla1c_p5v0w)。

1.  使用更强大的方式生成本体——[CogniPy](https://cognipy.org/index.html)库对于快速演示非常有用，但对于扩展使用，应当使用更成熟的本体导向编程框架，如[Owlready2](https://owlready2.readthedocs.io/en/latest/index.html)。

1.  第一点使得普通用户无需了解任何编程知识即可从本体中获取信息，而第二点则意味着需要软件开发人员来定义和写入本体（这有其优缺点）。然而，如果我们想要完成 AI 闭环，那么专家应该能够使用自然语言定义本体，而无需开发人员。这将更难实现，但类似的例子已经存在：[LLM 与 KG 接口](https://www.youtube.com/watch?v=XMwa9q6wWkw&t=1s)，实体解析。

实现上述所有三点的概念验证可以称之为真正的 AI，它应该能够在智能数据结构中开发知识，该结构既可供人类阅读，也可供机器读取，并通过自然语言查询以获得可操作的洞察，同时具有完全的透明度和审计追踪。

请关注我的第二部分！
