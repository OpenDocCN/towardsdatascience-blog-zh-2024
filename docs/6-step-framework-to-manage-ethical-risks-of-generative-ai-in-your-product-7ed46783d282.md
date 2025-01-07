# 管理生成性AI产品中声誉与伦理风险的六步框架

> 原文：[https://towardsdatascience.com/6-step-framework-to-manage-ethical-risks-of-generative-ai-in-your-product-7ed46783d282?source=collection_archive---------7-----------------------#2024-02-19](https://towardsdatascience.com/6-step-framework-to-manage-ethical-risks-of-generative-ai-in-your-product-7ed46783d282?source=collection_archive---------7-----------------------#2024-02-19)

## 一份务实的指南，帮助理解AI风险，并通过负责任的AI开发与用户建立信任

[](https://medium.com/@sarthakh330?source=post_page---byline--7ed46783d282--------------------------------)[![Sarthak Handa](../Images/0c75ba0f085fdb22a221705450047c40.png)](https://medium.com/@sarthakh330?source=post_page---byline--7ed46783d282--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7ed46783d282--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7ed46783d282--------------------------------) [Sarthak Handa](https://medium.com/@sarthakh330?source=post_page---byline--7ed46783d282--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7ed46783d282--------------------------------) ·阅读时间：9分钟·2024年2月19日

--

![](../Images/a0471e3425a437220dcefcbd1449eb78.png)

来源：Dalle-3

在快速发展的技术领域，产品团队感受到将创新型AI产品迅速推向市场的巨大压力。然而，将速度置于伦理和安全考虑之上可能会带来严重的负面后果。当AI系统违反社会规范和价值观时，组织面临的风险包括长时间的声誉损害和失去用户信任。

企业正处于AI时代的现实“*囚徒困境*”之中——虽然集体遵循伦理开发是理想的，但公司往往更倾向于通过妥协这些关键标准来获得先发优势。这一紧张局势通过臭名昭著的**Facebook-Cambridge Analytica**丑闻得到了体现，在该丑闻中，采取伦理上的捷径导致了严重且持久的声誉损害。随着AI在我们日常生活中的进一步渗透，这种伦理赌博的风险只会加剧，行业中的每个参与者都在权衡短期利益与长期信任和责任的价值。

自2018年以来，随着各行业AI应用的增加，高曝光度的伦理违规事件急剧上升。现在，识别这些风险并制定缓解策略变得至关重要。本文探讨了AI风险的主要来源，回顾了几个高曝光度的失败案例，并提出了一个六步框架，用于构建能应对这些威胁的产品。

# 识别AI的风险

## ***1\. 隐私侵犯与同意问题***

**风险：** 可以说，最普遍的AI陷阱是侵犯隐私和版权法。这涉及两种不同的失败：未能获得数据使用的同意，以及将数据用于超出同意范围的目的。

**示例：** 艺术家和作家已对**OpenAI**和**MidJourney**提起集体诉讼，指控这些公司未经同意使用他们的作品来训练模型。同样，**Getty Images**也在起诉**Stability AI**，指控其使用其数据进行模型训练。即便在给予同意的情况下，如果数据被用于未预料的目的，也可能发生隐私泄露。例如，**Google DeepMind**使用了**皇家伦敦NHS基金会信托**的160万患者数据，构建了一个新的医疗应用程序。尽管存在隐性同意，即这些数据可以用于改善患者健康，但信托和DeepMind未明确告知患者他们的信息被用于构建应用程序。

[](https://www.reuters.com/legal/litigation/artists-take-new-shot-stability-midjourney-updated-copyright-lawsuit-2023-11-30/?source=post_page-----7ed46783d282--------------------------------) [## 艺术家再次起诉Stability，Midjourney，更新后的版权诉讼

### 一群视觉艺术家已经对Stability AI、Midjourney和其他公司提起了修改后的版权诉讼…

[为什么Google收购DeepMind Health让隐私专家感到恐惧](https://www.reuters.com/legal/litigation/artists-take-new-shot-stability-midjourney-updated-copyright-lawsuit-2023-11-30/?source=post_page-----7ed46783d282--------------------------------) [](https://www.wired.co.uk/article/google-deepmind-nhs-health-data?source=post_page-----7ed46783d282--------------------------------) [## 为什么Google收购DeepMind Health让隐私专家感到恐惧

### DeepMind Health曾遇到过数据保护问题，其完全并入Google后，问题可能更加严重…

[www.wired.co.uk](https://www.wired.co.uk/article/google-deepmind-nhs-health-data?source=post_page-----7ed46783d282--------------------------------)

## ***2. 算法偏见***

**风险**：此风险涉及AI系统做出有偏见的预测，这种预测会系统性地使某些群体处于不利或排除地位，基于如种族、性别或社会经济背景等特征。这类偏见可能对社会产生重大影响，特别是当AI被用来做出影响个人生活的关键决策时。

**示例：** 2019年，苹果因涉嫌男性在**Apple Credit Cards**的信用额度高于女性而遭到反对，尽管在某些情况下女性的信用评分更高。其他显著的例子包括AI驱动的招聘软件和刑事司法应用程序，例如**COMPAS**工具，因表现出种族和性别偏见而受到批评。

[https://www.cnn.com/2019/11/12/business/apple-card-gender-bias/index.html?source=post_page-----7ed46783d282--------------------------------](https://www.cnn.com/2019/11/12/business/apple-card-gender-bias/index.html?source=post_page-----7ed46783d282--------------------------------) [## 苹果卡被指控存在性别偏见。它是如何发生的 | CNN商业

### 一些苹果卡用户表示，该信用卡的发行方高盛银行为女性设置了远低于男性的信用额度，即使…

[www.cnn.com](https://www.cnn.com/2019/11/12/business/apple-card-gender-bias/index.html?source=post_page-----7ed46783d282--------------------------------) [## 观点 | 当算法帮助你被送进监狱时（2017年发布）

### 将判刑的责任交给计算机程序并不能消除偏见。

[www.nytimes.com](https://www.nytimes.com/2017/10/26/opinion/algorithm-compas-sentencing-bias.html?source=post_page-----7ed46783d282--------------------------------)

## ***3. 精确度风险与可解释性差距***

**风险：** 当用于做出重大决策的人工智能系统提供不准确的结果，或未能为其输出提供清晰的理由时，可能会产生重大风险。人工智能的“黑箱”性质使得用户难以理解和验证其结果，这模糊了问责制并导致信任丧失。

**例子：** **IBM沃森肿瘤学**旨在为临床医生提供个性化的癌症治疗推荐。然而，有报告显示沃森给出了不安全和不正确的治疗建议，这导致了对技术的信任丧失，并损害了IBM的声誉。

[## IBM的沃森超级计算机推荐了“危险且不正确”的癌症治疗方案，内部文件…](https://www.statnews.com/2018/07/25/ibm-watson-recommended-unsafe-incorrect-treatments/?source=post_page-----7ed46783d282--------------------------------)

### 去年夏天，IBM沃森健康部门的高管展示的幻灯片大多将问题归咎于…

[www.statnews.com](https://www.statnews.com/2018/07/25/ibm-watson-recommended-unsafe-incorrect-treatments/?source=post_page-----7ed46783d282--------------------------------)

## ***4. 安全风险***

**风险：** 使用人工智能生成深度伪造内容会带来显著的社会风险，这涉及到生成高度逼真的图像或视频，让人们看起来像是在说或做他们实际上从未做过的事情。深度伪造已被用于制造欺骗性且逼真的媒体，可用于实施欺诈、传播虚假信息或破坏声誉。此外，人工智能还可能被用于网络攻击和社会工程学，如量身定制钓鱼攻击，这可能引入广泛的安全漏洞。

**示例**：在政治领域，深度伪造（deepfake）被用来伪造演讲或行为，可能在关键时刻如选举期间左右公众舆论。在**俄乌冲突**期间，一段伪造的视频显示乌克兰总统似乎告诉他的士兵们放下武器投降，这段视频被广泛传播，这种行为可能使军队士气低落，并为俄罗斯提供了关键的战术优势。在网络安全领域，AI 驱动的声音模仿被用来伪造 CEO 的声音，成功地进行了一笔欺诈性资金转移。

[](https://www.npr.org/2022/03/16/1087062648/deepfake-video-zelenskyy-experts-war-manipulation-ukraine-russia?source=post_page-----7ed46783d282--------------------------------) [## 专家警告：泽连斯基的深度伪造视频可能是信息战的“冰山一角”](https://www.npr.org/2022/03/16/1087062648/deepfake-video-zelenskyy-experts-war-manipulation-ukraine-russia?source=post_page-----7ed46783d282--------------------------------)

### 一段伪造的视频显示乌克兰总统宣布失败并在社交媒体上传播。

www.npr.org](https://www.npr.org/2022/03/16/1087062648/deepfake-video-zelenskyy-experts-war-manipulation-ukraine-russia?source=post_page-----7ed46783d282--------------------------------) [](https://www.trendmicro.com/vinfo/mx/security/news/cyber-attacks/unusual-ceo-fraud-via-deepfake-audio-steals-us-243-000-from-u-k-company?source=post_page-----7ed46783d282--------------------------------) [## 通过深度伪造音频进行的异常 CEO 欺诈，窃取了英国公司 243,000 美元](https://www.trendmicro.com/vinfo/mx/security/news/cyber-attacks/unusual-ceo-fraud-via-deepfake-audio-steals-us-243-000-from-u-k-company?source=post_page-----7ed46783d282--------------------------------)

### 一起异常的 CEO 欺诈案件使用了深度伪造音频，这是一种由人工智能（AI）生成的音频，已被报道…

www.trendmicro.com](https://www.trendmicro.com/vinfo/mx/security/news/cyber-attacks/unusual-ceo-fraud-via-deepfake-audio-steals-us-243-000-from-u-k-company?source=post_page-----7ed46783d282--------------------------------)

# **缓解 AI 风险的六步框架**

管理 AI 风险需要在整个产品生命周期中采取深思熟虑的方法。下面是一个六步框架，按 AI 开发的不同阶段组织，供组织采纳，以确保其产品中 AI 技术的负责任使用。

![](../Images/ef48c5b206b4ec5173e6f452dc24bc0b.png)

来源：作者

## ***1\. 开发前：伦理基础和设计原则***

在编写任何一行代码之前，产品团队应当为产品打下基础。优先与广泛的利益相关者进行早期接触，包括用户、技术专家、伦理学家、法律专业人士以及可能受到 AI 应用影响的社区成员。目标是识别与产品使用案例相关的明显和微妙的风险。利用这些见解来制定一套伦理准则和产品能力，在产品发布之前嵌入到产品中，以预防性地解决已识别的风险。

## ***2\. 开发阶段：数据同意、完整性、多样性***

数据是人工智能的基石，同时也是人工智能风险的最重要来源。确保所有用于模型训练的数据都具有道德来源，并且获得了用于其预定目的的同意，至关重要。例如，**Adobe** 使用专有数据训练其图像生成模型（**Firefly**），使其能够为用户提供法律保护，避免版权诉讼。

此外，应该从用于训练模型的敏感数据集中删除个人身份信息（PII），以防止潜在伤害。对这些数据集的访问应适当设置门槛并进行跟踪，以保护隐私。同样重要的是，确保数据集能够代表用户群体的多样性和使用场景的广度，从而减少偏见和公平性风险。像**Runway** 这样的公司已经使用合成数据集训练其文本到图像模型，这些数据集包含了来自不同种族、性别、职业和年龄段的人工智能生成的人的图像，以确保其人工智能模型在生成内容时体现多样性。

[](https://www.nasdaq.com/articles/how-adobes-copyright-protection-will-make-it-an-ai-leader?source=post_page-----7ed46783d282--------------------------------) [## Adobe的版权保护如何使其成为AI领导者

### 人工智能（AI）正在从根本上改变许多行业。人工智能将带来许多好处，但它也…

[www.nasdaq.com](https://www.nasdaq.com/articles/how-adobes-copyright-protection-will-make-it-an-ai-leader?source=post_page-----7ed46783d282--------------------------------) [](https://research.runwayml.com/publications/mitigating-stereotypical-biases-in-text-to-image-generative-systems?source=post_page-----7ed46783d282--------------------------------) [## 缓解文本到图像生成系统中的刻板偏见 | Runway 研究

### 用人工智能重新定义创意。

[研究.runwayml.com](https://research.runwayml.com/publications/mitigating-stereotypical-biases-in-text-to-image-generative-systems?source=post_page-----7ed46783d282--------------------------------)

## ***3. 开发：稳健性测试与实施防护措施***

测试阶段在决定人工智能是否准备好公开发布中至关重要。这涉及将人工智能的输出与经过验证的结果集进行比较。有效的测试使用：

+   与用户目标和商业价值对齐的**性能指标**，

+   代表不同人口统计群体并涵盖各种使用场景（包括边缘情况）的**评估数据**，

除了性能测试，实施防护措施同样至关重要，以防止人工智能产生有害结果。例如，**ImageFX**，**Google** 的图像生成服务，主动阻止用户生成可能被认为不适当或用于传播虚假信息的内容。同样，**Anthropic** 已主动设定了防护措施，以避免其人工智能服务在2024年选举中被滥用。

[](https://blog.google/technology/ai/google-imagen-2/?source=post_page-----7ed46783d282--------------------------------#:~:text=Our%20responsible%20approach%20to%20building%20Imagen%202) [## 用Imagen 2创造图像的新方式和更好的方式

### 我们正在推出Imagen 2，这是我们图像生成技术的重大更新。今天就可以在Bard、Image FX中试用它……

blog.google](https://blog.google/technology/ai/google-imagen-2/?source=post_page-----7ed46783d282--------------------------------#:~:text=Our%20responsible%20approach%20to%20building%20Imagen%202) [](https://www.linkedin.com/posts/anthropicresearch_preparing-for-global-elections-in-2024-activity-7164707718656643073-VcaW?utm_source=share&utm_medium=member_desktop&source=post_page-----7ed46783d282--------------------------------) [## Anthropic在LinkedIn：为2024年全球选举做准备

### 随着全球2024年高风险选举的临近，我们正在揭示我们的一些工作……

[www.linkedin.com](https://www.linkedin.com/posts/anthropicresearch_preparing-for-global-elections-in-2024-activity-7164707718656643073-VcaW?utm_source=share&utm_medium=member_desktop&source=post_page-----7ed46783d282--------------------------------)

## ***4\. 开发：可解释性与赋能***

在需要建立信任的关键行业应用中，AI应当帮助人类担任辅助角色。这可以通过以下方式实现：

+   提供AI洞察来源的引文。

+   强调AI预测的 不确定性 或 信心级别。

+   提供让用户选择退出使用AI的选项。

+   创建应用程序工作流，确保人工监督并防止某些任务被完全自动化。

## ***5\. 部署：渐进式推出与透明度***

当你将AI系统从开发阶段过渡到实际部署时，采用渐进式的发布策略至关重要，这有助于在受控环境中评估风险并收集反馈。同时，清晰地向用户和利益相关者传达AI的预期使用案例、能力和局限性也非常重要。此阶段的透明度有助于管理期望，并减少与AI系统意外失败相关的声誉风险。

**OpenAI**，例如，通过其最新的文本转视频服务**Sora**展示了这种方法，最初仅向部分红队成员和创意专业人士提供该服务。它已经公开了Sora的能力和当前的局限性，比如在生成涉及复杂物理交互的视频时面临的挑战。这种程度的透明度确保了用户了解该技术的优势和可能失败的地方，从而管理期望，赢得用户的信任，并促进AI技术的负责任采用。

[](https://openai.com/sora?source=post_page-----7ed46783d282--------------------------------#safety) [## Sora：从文本创建视频

### Sora 是一个能够根据文本指令创建逼真和富有创意场景的 AI 模型。阅读技术报告 All…

[openai.com](https://openai.com/sora?source=post_page-----7ed46783d282--------------------------------#safety)

## ***6\. 部署：监控、反馈与适应***

在 AI 系统上线后，工作并未结束。接下来需要密切关注 AI 在实际环境中的表现，并根据观察结果进行调整。创建一个持续的机制，追踪性能漂移，并不断在新数据上测试和训练模型，以避免 AI 性能的下降。使用户能够轻松标记问题，并利用这些反馈调整 AI，持续更新保护措施以符合高伦理标准。这将确保 AI 系统保持可靠、值得信赖，并与其操作的动态世界保持同步。

# **结论：**

随着 AI 在我们生活中的进一步渗透，主动管理伦理风险已不再是可选的——它是必须的。通过在每一个将 AI 产品推向市场的步骤中嵌入伦理考量，公司不仅可以降低风险，还能与用户建立起根本性的信任。当面对比我们之前见过的任何技术都要强大的技术时，*快速行动并破坏一切*的时代已经不能再适用。在处理可能对社会产生深远影响的风险时，没有捷径可走。

AI 产品构建者有责任更加有意图和有目的地行动，将信任作为他们的真正指南针。AI 的未来成功与持续进展取决于今天是否正确处理伦理问题。

*感谢阅读！如果这些见解对你产生共鸣或激发了新的思考，让我们继续交流。请在下方评论区分享你的看法，或者在* [***LinkedIn***](https://www.linkedin.com/)* 上与我联系。*
