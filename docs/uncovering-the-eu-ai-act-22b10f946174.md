# 揭开欧盟人工智能法案的面纱

> 原文：[`towardsdatascience.com/uncovering-the-eu-ai-act-22b10f946174?source=collection_archive---------7-----------------------#2024-03-14`](https://towardsdatascience.com/uncovering-the-eu-ai-act-22b10f946174?source=collection_archive---------7-----------------------#2024-03-14)

## 欧盟已经开始规范机器学习。这个新法案对数据科学家意味着什么？

[](https://medium.com/@s.kirmer?source=post_page---byline--22b10f946174--------------------------------)![Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--22b10f946174--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--22b10f946174--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--22b10f946174--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--22b10f946174--------------------------------)

·发布于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--22b10f946174--------------------------------) ·9 分钟阅读·2024 年 3 月 14 日

--

![](img/2c2abb2e138e0fc7cc74086f14e9acca.png)

图片由[Hansjörg Keller](https://unsplash.com/@kel_foto?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

[欧盟人工智能法案刚刚通过了欧洲议会](https://artificialintelligenceact.eu/)。你可能会想，“我不在欧盟，关我什么事，”但是相信我，这实际上比你想象的对全球的数据科学家和个人更为重要。欧盟人工智能法案是一个重要的举措，旨在规范和管理在欧盟内部或影响欧盟公民的某些机器学习模型的使用，并且其中包含了一些严格的规则和严重的违法惩罚。

这项法律有很多关于风险的讨论，这里的风险指的是对欧盟公民健康、安全和基本权利的威胁。它不仅仅是某种理论上的人工智能末日风险，而是关于日常生活中，因你所构建的模型或销售的产品，使得真实人的生活在某种程度上变得更糟。如果你熟悉今天关于人工智能伦理的许多辩论，那么这应该听起来很熟悉。嵌入式歧视和侵犯人们的权利，以及对人们健康和安全的危害，都是当前人工智能产品和公司面临的严重问题，而这项法律是欧盟保护人们的首次努力。

# 定义人工智能

常读者都知道，我总是希望“人工智能”有清晰的定义，对于模糊不清的定义，我总是感到不满。在这种情况下，[该法案将“人工智能”定义如下](https://artificialintelligenceact.eu/article/3/)：

> 一种机器系统，设计用于在不同的自主程度下操作，可能在部署后展现出适应性，并且为了明确或隐含的目标，从接收到的输入中推断如何生成输出，如预测、内容、推荐或决策，这些输出可能影响物理或虚拟环境。

那么，这到底意味着什么呢？我的理解是，产生输出并用于影响世界（尤其是人们的物理或数字状况）的机器学习模型，属于这个定义范围。它不需要实时适应或自动重训，尽管如果它这样做，也会受到涵盖。

但如果你正在构建用于执行以下任务的机器学习模型……

+   决定人们的风险等级，如信用风险、违法或犯罪风险等

+   确定人们在线上看到什么内容，如信息流或广告

+   针对相同产品，向不同人展示不同价格

+   推荐最佳治疗、护理或服务给人们

+   推荐人们是否采取某些行动

如果你的模型影响到任何欧盟公民，这些都将受到法律的约束——这些仅仅是举几个例子。

# 分类 AI 应用

然而，并非所有 AI 都是相同的，法律也承认这一点。某些 AI 应用将被完全禁止，而其他的则会受到更高程度的审查和透明度要求。

## 不可接受风险的 AI 系统

这些系统现在被称为“不可接受风险的 AI 系统”，并且**是明确不允许**的。法律的这一部分将在六个月后首先生效。

+   使用行为操控或欺骗手段让人们做出他们平时不会做的事情

+   针对年龄或残疾等因素，改变人们的行为和/或利用他们

+   生物识别分类系统，用于尝试根据高度敏感的特征对人进行分类

+   导致社会评分或差别待遇的个性特征评估

+   用于执法的“实时”生物识别身份识别，除非是针对特定的使用场景（寻找失踪或被绑架的人、迫在眉睫的生命或安全威胁/恐怖主义，或某一特定犯罪的起诉）

+   预测性警务（预测人们将来会犯罪）

+   广泛的人脸识别/生物识别扫描或数据抓取

+   在教育或工作中，未经医学或安全目的推测情绪的系统

这意味着，例如，你不能建立（或被迫提交）一个筛查系统，用来判断你是否“足够开心”来获得零售工作。人脸识别仅限于特定、选定的具体情况。（[Clearview AI 绝对是这类例子之一](https://www.theverge.com/23919134/kashmir-hill-your-face-belongs-to-us-clearview-ai-facial-recognition-privacy-decoder)）。预测性警务，这是我在学术界早期工作时参与的内容，现在我非常后悔，这也是不允许的。

“生物特征分类”这一点是指那些根据政治、宗教、哲学信仰、性取向、种族等敏感特征对人群进行分组的模型。使用人工智能试图将人们根据这些类别进行标签化，是法律明确禁止的。

## 高风险人工智能系统

另一方面，这个列表涵盖了那些没有被禁用但受到高度审查的系统。所有这些系统都有具体的规则和规定，以下将会描述。

+   医疗设备中的人工智能

+   车辆中的人工智能

+   情感识别系统中的人工智能

+   警务中的人工智能

这排除了上述描述的特定使用场景。所以，情感识别系统可能被允许使用，但不能在工作场所或教育领域使用。医疗设备和车辆中的人工智能被认为对健康和安全存在严重风险或潜在风险，这一点是有充分理由的，必须非常小心地推进。

## 其他

剩下的两个类别是“低风险人工智能系统”和“通用人工智能模型”。通用模型比如 GPT-4、Claude 或者 Gemini——这些系统有着非常广泛的应用场景，通常会被用于其他下游产品中。因此，GPT-4 本身并不属于高风险或禁用类别，但你将其嵌入使用的方式受到此处描述的其他规则的限制。你不能将 GPT-4 用于预测性警务，但 GPT-4 可以用于低风险场景。

# 透明度与审查

假设你正在开发一个**高风险**的人工智能应用，并且你希望遵循所有规则并获得批准，如何开始呢？

对于高风险人工智能系统，你将负责以下事项：

+   **保持并确保数据质量**：你在模型中使用的数据是你的责任，因此你需要仔细筛选和管理它。

+   **提供文档和可追溯性**：你的数据来源是什么，你能证明吗？你能展示对任何更改或编辑的记录吗？

+   **提供透明度**：如果公众正在使用你的模型（比如聊天机器人）或模型是你产品的一部分，你必须告知用户这一点。不能假装模型只是客服热线或聊天系统中的一个真人。*这一点实际上适用于所有模型，包括低风险模型。*

+   **使用人工监督**：仅仅说“模型表示……”是行不通的。人类将对模型结果负责，最重要的是，如何使用这些结果。

+   **保护网络安全和健壮性**：你需要确保模型在面对网络攻击、数据泄露和无意的隐私侵犯时保持安全。由于代码漏洞或你未修复的安全漏洞导致模型出错或被黑客攻击，最终的责任将由你承担。

+   **遵守影响评估**：如果你在构建高风险模型，你需要对其可能产生的影响进行严格评估（即使你并无意为之），尤其是对用户或公众的健康、安全和权利。

+   **对于公共实体，需在公共欧盟数据库中注册**：这个注册表是作为新法案的一部分创建的，提交要求将适用于“公共当局、机构或组织”——主要是政府机构，而非私人企业。

## 测试

该法律还指出，如果你正在开发一个高风险的人工智能解决方案，你需要有一种方法来测试它，以确保你遵循指导方针，因此[一旦获得知情同意，就允许进行测试](https://artificialintelligenceact.eu/article/54b/)。我们来自社会科学领域的人会觉得这很熟悉——它类似于获得机构审查委员会的批准来进行研究。

## 效力

该法律有分阶段实施：

+   6 个月后，对不可接受风险的人工智能的禁令生效

+   12 个月后，通用人工智能治理生效

+   24 个月后，法律中的所有剩余规则生效

注意：该法律不涵盖纯粹的个人非职业活动，除非它们属于前面列出的禁用类型，所以你的小型开源副项目不太可能构成风险。

# 处罚

那么，如果你的公司未遵守法律，并且影响到欧盟公民，会发生什么呢？[法律中有明确的处罚条款。](https://artificialintelligenceact.eu/article/71/)

如果你做了上述描述的禁用形式的人工智能：

+   最高**3500 万欧元**的罚款，或者如果你是企业，**上年度全球收入的 7%**（以较高者为准）

不在禁用范围内的其他违规行为：

+   最高**1500 万欧元**的罚款，或者如果你是企业，**上年度全球收入的 3%**（以较高者为准）

对当局就以下事项撒谎：

+   最高**750 万欧元**的罚款，或者如果你是企业，**上年度全球收入的 1%**（以较高者为准）

注意：对于中小型企业，包括初创公司，罚款将以较低者为准，而非较高者。

# 数据科学家应该做什么？

如果你正在根据该法案的定义构建使用人工智能的模型和产品，你应该首先**熟悉法律及其要求**。即使你今天没有影响到欧盟公民，这也可能对该领域产生重大影响，你应该意识到这一点。

然后，注意你自己企业或组织中可能的违规行为。你有时间发现并修正问题，但被禁的人工智能形式将首先生效。在大型企业中，你可能会有法务团队，但不要认为他们会为你处理所有这些事情。你是机器学习方面的专家，因此你是企业发现和避免违规行为的重要一环。你可以使用[欧盟人工智能法案网站上的合规检查工具](https://artificialintelligenceact.eu/assessment/eu-ai-act-compliance-checker/)来帮助你。

今天在企业和组织中使用的许多 AI 形式是新法律所不允许的。我在上文提到过 Clearview AI，以及预测性警务。情感测试也是一个非常真实的现象，求职面试过程中人们常常会受到这种测试（我邀请你搜索“情感测试求职”并看到众多公司提供此服务），此外，还有高频率的面部或其他生物特征数据收集。我们所有人都将非常关注这个问题，看到执法如何展开，一旦法律全面生效，情况将非常有趣且重要。

我想花一点时间说几句，关于我一位亲爱的朋友，他在这周与癌症进行了艰苦的斗争后去世。Ed Visel，在线昵称为`alistaire`，是一位杰出的数据科学家，他将大量的时间和才华奉献给了更广泛的数据科学社区。[如果你在过去十年里在 StackOverflow 上问过 R 问题，可能他曾帮助过你](https://stackoverflow.com/users/4497050/alistaire)。他总是耐心和善良，因为像我一样，作为一名自学成才的数据科学家，他深知从零开始学习这些东西的艰难，因此始终保持着同理心。

![](img/29104dd21d0af55f5b74cbfb872b7176.png)

作者照片

我有幸与 Ed 共事了几年，并且成为了他的朋友多年。我们失去了他，实在是太早了。我请求你们在他的记忆中帮助一位朋友或同事解决一个技术问题。没有他，数据科学社区将变得不再那么友好。

此外，如果你曾在线或亲自认识 Ed，家属已请求捐款给[Severson Dells 自然中心](https://www.seversondells.com/)，这是一个对他意义非凡的地方。

*在此阅读更多我的内容* [*www.stephaniekirmer.com.*](http://www.stephaniekirmer.com.)

# 参考文献和进一步阅读

[](https://artificialintelligenceact.eu/ai-act-explorer/?source=post_page-----22b10f946174--------------------------------) [## AI 法案探索器

### 已将所有“章节”添加到 AI 法案探索器中。添加了缺失的条款：3、66、68、68a（1）、68f、68g、68h、68i、68j、68k……

artificialintelligenceact.eu](https://artificialintelligenceact.eu/ai-act-explorer/?source=post_page-----22b10f946174--------------------------------)

[`www.theverge.com/23919134/kashmir-hill-your-face-belongs-to-us-clearview-ai-facial-recognition-privacy-decoder`](https://www.theverge.com/23919134/kashmir-hill-your-face-belongs-to-us-clearview-ai-facial-recognition-privacy-decoder)

[](https://artificialintelligenceact.eu/assessment/eu-ai-act-compliance-checker/?source=post_page-----22b10f946174--------------------------------) [## 欧盟 AI 法案合规检查器

### 欧盟委员会现在正在支持欧盟理事会和欧洲议会完成……

[人工智能法案](https://artificialintelligenceact.eu/assessment/eu-ai-act-compliance-checker/?source=post_page-----22b10f946174--------------------------------) [](https://www.reuters.com/technology/stalled-eu-ai-act-talks-set-resume-2023-12-08/?source=post_page-----22b10f946174--------------------------------) [## 欧洲达成具有里程碑意义的人工智能法规协议

### 通过这一政治协议，欧盟朝着成为全球首个实施人工智能法律的主要大国迈出了步伐。

[www.reuters.com](https://www.reuters.com/technology/stalled-eu-ai-act-talks-set-resume-2023-12-08/?source=post_page-----22b10f946174--------------------------------)
