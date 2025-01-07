# 一劳永逸地打破人工智能的炒作泡沫

> 原文：[https://towardsdatascience.com/bursting-the-ai-hype-bubble-once-and-for-all-581a994fe762?source=collection_archive---------1-----------------------#2024-10-12](https://towardsdatascience.com/bursting-the-ai-hype-bubble-once-and-for-all-581a994fe762?source=collection_archive---------1-----------------------#2024-10-12)

## 错误信息和糟糕的研究：一个案例研究

[](https://medium.com/@paublascoroca?source=post_page---byline--581a994fe762--------------------------------)[![Pau Blasco i Roca](../Images/fa7b66107c8317498d4e53869baa2ff4.png)](https://medium.com/@paublascoroca?source=post_page---byline--581a994fe762--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--581a994fe762--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--581a994fe762--------------------------------) [Pau Blasco i Roca](https://medium.com/@paublascoroca?source=post_page---byline--581a994fe762--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--581a994fe762--------------------------------) ·9分钟阅读·2024年10月12日

--

不容忽视的事实是，人工智能模型，如ChatGPT，已经接管了互联网，遍布它的每个角落。

*大多数人工智能的应用在广泛的任务中（如医疗保健、工程学、计算机视觉、教育等）都非常有用且具有益处*，我们没有理由不投资时间和金钱来推动它们的发展。

这对于生成式人工智能（GenAI）来说并不适用，我将在本文中专门提到这一点。包括大语言模型（LLMs）和检索增强生成（RAGs）模型，如**ChatGPT、Claude、Gemini、Llama以及其他模型**。我们需要非常具体地界定我们所称的人工智能、所使用的模型及其对环境的影响。

![](../Images/2ccf886570f211c39bbd557ad0fade33.png)

[[1](https://trends.google.com/trends/explore?date=2021-01-01+2024-10-03&q=AI%2CChatGPT&hl=en)]: 在线关于“人工智能”和“ChatGPT”这两个词的兴趣趋势（过去四年）。截图由我拍摄。来源：[Google Trends](https://trends.google.com/trends/explore?date=2021-01-01+2024-10-03&q=AI%2CChatGPT&hl=en)

> 那么，人工智能正在接管世界吗？它的智商有120吗？它能比人类思考得更快、更好吗？

# 什么是人工智能的炒作？

AI炒作是社会对AI的广泛兴奋，特别是对变换器（类似GPT的）模型的兴奋。它已经渗透到每个领域——医疗、IT、经济学、艺术——以及生产链的每个层级。事实上，[43%的高管和CEO已经使用生成式AI来指导战略决策](https://newsroom.ibm.com/2023-06-27-IBM-Study-CEOs-Embrace-Generative-AI-as-Productivity-Jumps-to-the-Top-of-their-Agendas) [[2](https://newsroom.ibm.com/2023-06-27-IBM-Study-CEOs-Embrace-Generative-AI-as-Productivity-Jumps-to-the-Top-of-their-Agendas)]。以下链接的文章将科技公司裁员与AI使用联系起来，包括FAANG等大公司[[3](https://edition.cnn.com/2023/07/04/tech/ai-tech-layoffs/index.html)、[4](https://edition.cnn.com/2024/01/13/tech/tech-layoffs-ai-investment/index.html)、[5](https://www.bloomberg.com/news/articles/2024-02-08/ai-is-driving-more-layoffs-than-companies-want-to-admit)]。

AI炒作的影响也可以在股市中看到。英伟达公司（NVIDIA）的案例就是一个明确的例子：由于英伟达生产用于训练AI模型的关键硬件组件（GPU），其股价已经不可思议地上涨（可以说这并不反映公司真正的增长，而是更多地反映了人们对其重要性的认知）。

![](../Images/bd1677ac44007d04a509941560ace1cb.png)

英伟达公司在过去五年的股价变化。可以看到，去年股价的增长惊人，市值翻了三倍（52周最高值是52周最低值的3.5倍），过去三年甚至增长了更为惊人的27.58倍。截图由我拍摄，数据来自[Refinitiv](https://www.lseg.com/en/data-analytics)。

# 为什么这是个问题？

人类一直抵制采纳新技术，尤其是那些他们无法完全理解的技术。这是一个令人害怕的步骤。每一个突破都像是对未知的“赌博”——因此我们感到恐惧。在我们确信其效用和安全性足以证明风险是值得的之前，大多数人不会轻易转向新事物。嗯，直到某些东西打破了我们的直觉，而这种东西和恐惧一样，根源于情感：炒作。

生成式AI存在许多问题，其中大多数几乎无法解决。一些例子包括模型幻觉（例如，草莓中有多少个“r”？[[6](https://www.inc.com/kit-eaton/how-many-rs-in-strawberry-this-ai-cant-tell-you.html)]）、没有自动辨识能力（模型无法判断自己是否正确完成任务[[7](https://arxiv.org/abs/2409.04109)])，以及其他问题，如安全漏洞。

![](../Images/4a5bcca4d08b040620d5f3edaf1aab0a.png)

生成式AI幻觉的模拟对话示例。图像由我生成。示例类似于[[6](https://www.inc.com/kit-eaton/how-many-rs-in-strawberry-this-ai-cant-tell-you.html)]和[[17](https://www.thairath.co.th/money/tech_innovation/tech_companies/2814211)]中展示的案例。

# 而且，考虑到伦理问题……

当我们考虑伦理问题时，情况并没有好转。AI引发了一系列棘手问题：版权、隐私、环境和经济问题。为了简要总结，避免超出本文的篇幅：

**AI是通过盗用数据进行训练的**：大多数用于训练的内容，如果不是绝大多数，都是*盗用的*。在我们社会对著作权保护和合理使用界限进行反思的同时，AI引发的恐慌可能会带来与其真正盗窃行为同样严重的损害。《史密森学会》（[8](https://www.smithsonianmag.com/smart-news/are-ai-image-generators-stealing-from-artists-180981488/)）、《大西洋月刊》（[9](https://www.theatlantic.com/technology/archive/2024/06/chatgpt-citations-rag/678796/)）、IBM（[10](https://www.ibm.com/think/topics/ai-privacy)）和《自然》杂志（[11](https://www.nature.com/articles/d41586-024-02838-z)）都在讨论这一问题。

**经济不平等的延续**：CEO们进行的代理、大规模且回报低的投资通常会通过大规模裁员、降低工资或更糟的工作条件反噬工人阶级。这种情况延续了社会和经济的不平等，只是为了维持AI炒作的泡沫[[12](https://link.springer.com/article/10.1007/s43681-024-00461-2)]。

**对环境危机的贡献**：*地球*的研究[[13](https://earth.org/environmental-impact-chatgpt/)]声称，ChatGPT-3（1750亿参数）在训练过程中使用了700,000升淡水，并且每次与用户的对话消耗半升水。根据该研究的线性推算，ChatGPT-4（约1.8万亿参数）在训练中可能使用了700万升水，并且每次对话消耗5升水。

# 个案研究：误信息/研究不当的示例

最近，[Maxim Lott](https://x.com/maximlott)[[14](https://x.com/maximlott)] 发表了一项名为“[**AI智能的重大突破：OpenAI突破IQ 120**](https://substack.com/home/post/p-148891210)” [[15](https://substack.com/home/post/p-148891210)]的研究，结果非常有希望。他通过IQ测试评估AI，发现OpenAI的最新版本o1取得了120的IQ分数，远远领先于其他模型（Claude-3 Opus、GPT-4 Omni和Claude-3.5 Sonnet，它们的IQ分数仅略高于90）。

这是七次IQ测试的平均结果。作为背景说明，IQ 120的分数使得OpenAI在人类智力方面位于前10%。

来自Maxim Lott的[博客文章](https://www.maximumtruth.org/p/massive-breakthrough-in-ai-intelligence)中的图片。Mensa挪威IQ测试结果，问题在线（DuckDuckGo搜索“Mensa Norway iq test”可以找到，第一个结果[在这里](https://test.mensa.no/Home/Test/en-US)）。

这其中有什么陷阱吗？就这些吗？我们是否已经编程出一个比普通人更聪明的模型？机器是否超越了它的创造者？

关键在于，和往常一样，**训练集**。Maxim Lott声称**测试问题不在训练集中**，或者至少，它们是否在其中并不重要[[15](https://substack.com/home/post/p-148891210)]。值得注意的是，**当他用一个据称私密的、未公开（但已校准）的测试来评估模型时，智商分数被完全摧毁**：

来自Maxim Lott的[博客文章](https://www.maximumtruth.org/p/massive-breakthrough-in-ai-intelligence)中的图片。新测试包含了新的智商问题以及旧的、网上可得的问题。关于新旧问题的比例以及它们是否在复杂度上均匀分布，还不清楚。

## 为什么会发生这种情况？

之所以会发生这种情况，是因为**模型在它们的训练数据集中有这些信息**，通过搜索它们被问到的问题，它们能够得到答案，而不需要“思考”。

> 想象一下，在考试之前，如果一个人同时获得了问题和答案，并且只需要记住每一对问题-答案。你不会说他们因为得了100%就很聪明吧？

此外，视觉模型在两项测试中表现极差，智商计算得分在50到67之间。**它们的得分与一个随机回答的代理一致**，在挪威门萨的测试中，这相当于每答对1个问题（6个问题中答对1个）。根据M. Lott的观察以及实际测试如WAIS-IV的工作方式，如果25/35等于120智商，那么17.5/35相当于100智商，9/35则略高于80智商，而随机选择答案（约6/35正确）会得出69-70智商的分数。

不仅如此，大多数问题的推理似乎最多也就是明显偏离或完全错误。模型似乎会发现不存在的模式，或者生成预写的、重复使用的答案来为它们的选择辩护。

此外，尽管他声称测试是仅限离线的，但似乎测试在网上发布了若干小时。引述：“**我随后创建了一个调查，包含他的新问题以及一些挪威门萨的问题，并要求博客读者参与。大约40人参与了。*然后我删除了调查*。这样，这些问题就没有发布到搜索引擎等可以访问的公共互联网中，应该不会出现在AI训练数据中**。”**[[15](https://substack.com/home/post/p-148891210)]。

> 作者不断自相矛盾，提出模糊的说法，没有实际证据支持，并将这些说法作为真实证据呈现。

**所以不仅问题被发布到互联网上，而且测试还包括了以前的旧问题**（那些**在训练数据中出现过的**问题）。我们在这里再次看到Lott的矛盾说法。

可惜的是，我们没有详细的题目结果或比例的划分，无法区分新旧问题。结果一定会非常有趣。再次表现出研究的不完全性。

> 是的，确实有证据表明这些问题出现在训练数据中，而且没有任何模型真正理解它们在做什么，或者它们自己的“思维”过程。

更多的例子可以在[这篇](https://arxiv.org/abs/2409.04109)关于 AI 和创意生成的文章中找到。尽管它也在追逐炒作的浪潮，但它展示了模型如何无法区分好主意和坏主意，暗示它们并不理解其任务背后的基本概念[[7](https://arxiv.org/abs/2409.04109)]。

## 那么结果到底有什么问题呢？

根据科学方法，如果一个研究者得到了这些结果，接下来的逻辑步骤应该是接受 OpenAI **没有**取得任何重大突破（或者即使有突破，也无法通过 IQ 测试来衡量）。**然而，Lott 依然坚称他的“AI 巨大突破”论述**。这就是虚假信息开始蔓延的地方。

# 错误信息的影响：一场连锁反应

让我们闭合这个循环：这些类型的文章是如何推动 AI 炒作泡沫的？

文章的 SEO [[16](https://moz.com/learn/seo/what-is-seo)] 非常巧妙。标题和缩略图都极具误导性，这反过来又制造了非常吸引眼球的推文、Instagram 和 Linkedin 帖子。IQ 正态分布曲线上的神奇分数实在太好看，令人无法忽视。

在这一部分，我将回顾一些“新闻”是如何在社交媒体上传播的例子。请注意，嵌入的推文可能需要几秒钟才能加载。

CC：根据挪威门萨 IQ 测试，OpenAI o1 现在比大多数人类更聪明。它得到了 120 分，比普通人类高出 20 分，比其他高级 AI 模型如 Claude 高出 30 分。如果这是真的，简直疯狂。完整的 IQ 测试结果请见这里：（文章链接）[[18](https://x.com/rowancheung/status/1835529620508016823)]

这条推文声称结果“根据挪威门萨 IQ 测试”，但这不是真的。这个声明并不是由测试本身提出的，而是由第三方提出的。它再次将其陈述为事实，并在后面给出了合理的否认空间（“如果是真的，简直疯狂”）。让我们看看下一条：

CC：AI 现在比普通人类更聪明。这项来自 maximlott@ 的令人难以置信的研究非常精彩，我强烈推荐关注他。当所有模型都超越人类时会发生什么？（文章的第一部分图片）[[19](https://x.com/Greenbaumly/status/1837568393962025167)]

这条推文没有改变立场，直接将 Lott 的研究呈现为事实（“AI 现在比普通人类更聪明”）。除此之外，观众只看到了第一张图表的截图（训练数据中的问题-答案，膨胀的分数），这非常具有误导性。

CC：它发生了：OpenAI的新模型智商跃升了***30点***，达到了120智商。[……] “担心人工智能接管世界吗？你可能真的应该担心……（查看更多）。” 注意：作者maximlott@进行了另一次无污染测试，结果显示得分较低（约100——普通人类），但智商跃升相对相似。因此，不管你看哪个分数，跃升都是巨大的，趋势显而易见。时间不多了。[[20](https://x.com/AISafetyMemes/status/1835339785419751496)]

这个确实具有误导性。即使给出了某种免责声明，信息依然不准确。后续测试并没有做到无污染，因为报告中提到它包含了在线可用的题目，且在视觉测试部分表现仍然非常差。这里没有明显的趋势可供观察。

# 结论

对我们分享的信息进行双重甚至三重检查至关重要。虽然真理是一个无法完全达到的绝对值，但虚假或部分虚假的信息却是非常现实的。炒作、社会情绪的普遍化或类似的力量不应推动我们草率发布内容，无意中助长那些本应早已消亡的运动，而这些运动正在造成如此负面的经济和社会影响。

越来越多本应局限于情感和思想领域的内容正在影响我们的市场，股市每天变得更加波动。人工智能热潮的案例就是另一个炒作和错误信息如何结合的例子，以及它们可能带来的灾难性后果。

免责声明：一如既往，回复开放以供进一步讨论，我鼓励大家参与。任何形式的骚扰或仇恨言论，无论是针对原文作者、第三方，还是我本人，都将不被容忍。其他任何形式的讨论都非常欢迎，无论是建设性的还是尖锐的批评。研究应该始终能够被质疑和审查。

# 参考文献

[1] Google Trends，自2021年以来，网络上关于“AI”和“ChatGPT”的搜索可视化。[https://trends.google.com/trends/explore?date=2021-01-01%202024-10-03&q=AI,ChatGPT&hl=en](https://trends.google.com/trends/explore?date=2021-01-01+2024-10-03&q=AI%2CChatGPT&hl=en)

[2] IBM 2023年关于首席执行官及其如何看待和使用人工智能做出商业决策的研究。[https://newsroom.ibm.com/2023-06-27-IBM-Study-CEOs-Embrace-Generative-AI-as-Productivity-Jumps-to-the-Top-of-their-Agendas](https://newsroom.ibm.com/2023-06-27-IBM-Study-CEOs-Embrace-Generative-AI-as-Productivity-Jumps-to-the-Top-of-their-Agendas)

[3] CNN，技术行业裁员中的人工智能。[https://edition.cnn.com/2023/07/04/tech/ai-tech-layoffs/index.html](https://edition.cnn.com/2023/07/04/tech/ai-tech-layoffs/index.html)

[4] CNN，裁员与对人工智能的投资。[https://edition.cnn.com/2024/01/13/tech/tech-layoffs-ai-investment/index.html](https://edition.cnn.com/2024/01/13/tech/tech-layoffs-ai-investment/index.html)

[5] Bloomberg，AI导致的裁员比公司愿意承认的要多。 [https://www.bloomberg.com/news/articles/2024-02-08/ai-is-driving-more-layoffs-than-companies-want-to-admit](https://www.bloomberg.com/news/articles/2024-02-08/ai-is-driving-more-layoffs-than-companies-want-to-admit)

[6] INC，草莓里有多少个r？这个AI无法告诉你 [https://www.inc.com/kit-eaton/how-many-rs-in-strawberry-this-ai-cant-tell-you.html](https://www.inc.com/kit-eaton/how-many-rs-in-strawberry-this-ai-cant-tell-you.html)

[7] ArXiv，LLMs能否生成新颖的研究创意？一项涉及100多名NLP研究者的大规模人类研究。 [https://arxiv.org/abs/2409.04109](https://arxiv.org/abs/2409.04109)

[8] Smithsonian，AI图像生成器是在盗取艺术家的作品吗？ [https://www.smithsonianmag.com/smart-news/are-ai-image-generators-stealing-from-artists-180981488/](https://www.smithsonianmag.com/smart-news/are-ai-image-generators-stealing-from-artists-180981488/)

[9] The Atlantic，生成型AI无法引用其来源。 [https://www.theatlantic.com/technology/archive/2024/06/chatgpt-citations-rag/678796/](https://www.theatlantic.com/technology/archive/2024/06/chatgpt-citations-rag/678796/)

[10] IBM，关于AI隐私的话题 [https://www.ibm.com/think/topics/ai-privacy](https://www.ibm.com/think/topics/ai-privacy)

[11] Nature，知识产权与数据隐私：AI的隐藏风险。 [https://www.nature.com/articles/d41586-024-02838-z](https://www.nature.com/articles/d41586-024-02838-z)

[12] Springer，[AI炒作机制及其对地球和社会的成本](https://link.springer.com/article/10.1007/s43681-024-00461-2)

[13] Earth，ChatGPT-3的环境影响 [https://earth.org/environmental-impact-chatgpt/](https://earth.org/environmental-impact-chatgpt/)

[14] Twitter，用户“maximlott”。 [https://x.com/maximlott](https://x.com/maximlott)

[15] Substack，AI智能的重大突破：OpenAI IQ超过120。 [https://substack.com/home/post/p-148891210](https://substack.com/home/post/p-148891210)

[16] Moz，什么是SEO？ [https://moz.com/learn/seo/what-is-seo](https://moz.com/learn/seo/what-is-seo)

[17] Thairath技术创新，科技公司，AI幻觉示例 [https://www.thairath.co.th/money/tech_innovation/tech_companies/2814211](https://www.thairath.co.th/money/tech_innovation/tech_companies/2814211)

[18] Twitter，推文 1 [https://x.com/rowancheung/status/1835529620508016823](https://x.com/rowancheung/status/1835529620508016823)

[19] Twitter，推文 2 [https://x.com/Greenbaumly/status/1837568393962025167](https://x.com/Greenbaumly/status/1837568393962025167)

[20] Twitter，推文 3 [https://x.com/AISafetyMemes/status/1835339785419751496](https://x.com/AISafetyMemes/status/1835339785419751496)

![](../Images/4b4529fd1d5ef1a33f3704149b1b4a3f.png)

[[1](https://trends.google.com/trends/explore?date=2021-01-01+2024-10-03&q=AI%2CChatGPT&hl=zh)]: 关于“AI”和“ChatGPT”术语在网络上的兴趣变化。经过简化并修正了宽高比，适合用作缩略图。来源：[Google Trends](https://trends.google.com/trends/explore?date=2021-01-01+2024-10-03&q=AI%2CChatGPT&hl=zh)。由我编辑。
