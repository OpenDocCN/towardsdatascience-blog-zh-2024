# 生成性AI是企业应在2024年接受的赌注

> 原文：[https://towardsdatascience.com/generative-ai-is-a-gamble-enterprises-should-take-in-2024-9120e54f6349?source=collection_archive---------15-----------------------#2024-01-04](https://towardsdatascience.com/generative-ai-is-a-gamble-enterprises-should-take-in-2024-9120e54f6349?source=collection_archive---------15-----------------------#2024-01-04)

## 目前，大型语言模型（LLMs）在大规模应用时存在不准确性，但这并不意味着你应该通过等待来采用生成性AI，从而失去竞争优势。

[](https://databrett.medium.com/?source=post_page---byline--9120e54f6349--------------------------------)[![Brett A. Hurt](../Images/d120f98c1d5e06b14add6b5b9f4eb936.png)](https://databrett.medium.com/?source=post_page---byline--9120e54f6349--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9120e54f6349--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9120e54f6349--------------------------------) [Brett A. Hurt](https://databrett.medium.com/?source=post_page---byline--9120e54f6349--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9120e54f6349--------------------------------) ·阅读时长6分钟·2024年1月4日

--

![](../Images/69c21cf26e4eac65662be8762774c3a1.png)

构建具有AI准备能力的工作队伍，使用data.world的OWLs，正如OpenAI的GPT-4所设想的那样

每项企业技术都有其存在的目的，否则它就不会存在。生成性AI的企业目的是从技术、商业和语言数据中快速且大规模地生成可供人类使用的输出，以推动生产力、效率和商业收益。但生成性AI的这一主要功能——提供巧妙的答案——也是大型语言模型（LLMs）在企业采用过程中面临的最大障碍：所谓的“幻觉”问题。

为什么幻觉会发生呢？因为，从本质上讲，大型语言模型（LLMs）是复杂的统计匹配系统。它们分析数十亿个数据点，努力确定模式，并预测对任何给定提示的最可能回应。但是，尽管这些模型可能通过它们的回答给我们留下深刻印象，展示了有用性、深度和创造力，并诱使我们每次都信任它们，但它们远非可靠。Vectara的最新[研究](https://vectara.com/measuring-hallucinations-in-rag-systems/)发现，聊天机器人“发明”新信息的频率*高达27%*。在企业环境中，问题的复杂性可能变化很大，这个数字还会更高。数据.world的AI实验室进行的[最新基准测试](https://arxiv.org/pdf/2311.07509.pdf)使用了真实的商业数据，发现当作为独立解决方案部署时，大型语言模型仅*25.5%的时间*能返回准确的基本商业查询回答。对于中级或专家级问题，这些问题仍在典型的数据驱动企业查询的范围内，*准确率降至零*！

幻觉的倾向对那些仅在小范围或新奇用途上玩耍的个人来说可能无关紧要。但当涉及到企业部署时，幻觉却构成了系统性的风险。其后果从不便（例如，服务聊天机器人在客户互动中提供无关信息）到灾难性（例如，在美国证券交易委员会（SEC）报告中输入错误的数字）不等。

如今，生成式AI对企业来说仍然是一场赌博。然而，这也是一场必不可少的赌博。正如我们在OpenAI的第一次开发者大会上所了解到的，[92%的财富500强公司](https://www.theverge.com/2023/11/6/23948386/chatgpt-active-user-count-openai-developer-conference)正在使用OpenAI的API。这项技术在企业中的潜力如此具有变革性，以至于未来的路径已经非常明确：开始采用生成式AI——明白回报与风险并存。另一种选择是将自己与风险隔离开来，迅速落后于竞争对手。[不可避免的生产力提升](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4573321)现在已经如此明显，以至于不利用它可能会对企业的生存构成存在性的威胁。所以，在面对这种选择的幻象时，组织应该如何在将生成式AI整合到工作流程中，同时降低风险呢？

**首先，你需要优先考虑你的数据基础。** 就像任何现代企业技术一样，生成式 AI 解决方案的好坏取决于其所建立的数据基础——根据思科最近发布的[AI 准备度指数](https://www.cisco.com/c/dam/m/en_us/solutions/ai/readiness-index/documents/cisco-global-ai-readiness-index.pdf)，意图正在超越能力，尤其是在数据方面。思科发现，虽然全球 84% 的公司认为 AI 将对其业务产生重大影响，但 81% 的公司缺乏实现 AI 工具潜力所需的数据集中化，而只有 21% 的公司表示其网络具有支持高负载 AI 工作负载的“最佳”延迟。关于数据治理，情况也相似；目前只有三分之一的受访者拥有全面的 AI 政策和协议，只有四分之一的受访者拥有系统性的 AI 偏见和公平性修正流程。

如基准测试所示，LLM 已经很难可靠地检索事实性答案。如果再加上数据质量差、缺乏数据集中化/管理能力，以及有限的治理政策，幻觉现象的风险——以及随之而来的后果——*将急剧上升*。简单来说，拥有强大数据架构的公司可以更好、更准确地获得信息，进而其 AI 解决方案能够做出更好的决策。使用数据目录或评估内部治理和数据录入流程可能并不是采用生成式 AI 最令人兴奋的部分。但正是这些考虑——数据治理、血统和质量——可能决定生成式 AI 项目的成败。它不仅使组织能够更快、更负责任地部署企业级 AI 解决方案，还能够帮助它们在技术发展的过程中跟上市场的步伐。

**第二，你需要建立一个具备 AI 知识的员工队伍。** 研究表明，[高级提示工程](https://amatriain.net/blog/hallucinations#advancedprompting)等技术对于识别和减轻幻觉现象有帮助。其他方法，如微调，已被证明能显著提高大语言模型（LLM）的准确性，甚至在某些情况下超过更大、更先进的通用模型。然而，员工只有在接受了最新的培训和教育后，才能应用这些技巧。说实话，大多数员工并未具备这种能力。自 2022 年 11 月 30 日 ChatGPT 发布至今，已经过去了一年多！

当像Databricks或Snowflake这样的主要供应商发布新功能时，组织通常会涌向网络研讨会、会议和工作坊，以确保他们能够利用最新的功能。生成式人工智能应该也不例外。在2024年，建立一种文化，使得教育您的团队掌握人工智能最佳实践成为您的默认选项；例如，通过提供针对人工智能的L&D项目补贴或聘请外部培训顾问（比如我们在data.world与[Rachel Woods](https://medium.com/u/4c866ecaad84?source=post_page---user_mention--9120e54f6349--------------------------------)的合作，她在我们的顾问委员会中任职，并创办并领导了The AI Exchange）。我们还提升了[Brandon Gadoci](https://medium.com/u/fa1e06871025?source=post_page---user_mention--9120e54f6349--------------------------------)，我和我的共同创始人之外的第一位data.world员工，担任我们的人工智能运营副总裁。我们在内部生产力上已经取得的惊人提升，简直令人鼓舞（我在[这个三篇系列文章](https://data.world/blog/meet-archie-who-is-helping-all-of-us-get-smarter-than-any-of-us-at-the-speed-of-ai/)中写到了这点）。Brandon[昨天刚报告](https://bgadoci.com/thoughts/my-new-adventure-vp-of-ai-ops)我们通过在2023年全职岗位中使用内部人工智能工具，团队的生产力惊人地提高了25%！采纳这种文化将大大帮助您的组织理解、识别和减轻幻觉威胁。

**第三，您需要保持对新兴人工智能生态系统的关注。** 就像任何新的范式改变技术一样，人工智能周围充斥着大量新兴的实践、软件和流程，旨在最小化风险并最大化价值。尽管大型语言模型（LLMs）可能会带来巨大变革，但令人欣慰的事实是，我们才刚刚踏上人工智能演变的漫长道路。

曾经对您的组织而言陌生的技术可能会变得至关重要。我们发布的前述[基准测试](https://data.world/blog/generative-ai-benchmark-increasing-the-accuracy-of-llms-in-the-enterprise-with-a-knowledge-graph/)显示，结合知识图谱的LLMs——一种用于在三维中情境化数据的数十年历史架构（像人类大脑一样映射和关联数据）*—可以提高准确性300%*！同样，向量数据库和增强检索生成（RAG）等技术也因其在解决LLMs的幻觉问题方面的能力而崭露头角。从长远来看，人工智能的雄心远超目前主要LLM提供商的API，因此在企业的人工智能投资中，保持好奇心和灵活性是至关重要的。

像任何新技术一样，生成式人工智能解决方案并不完美，它们产生幻觉的倾向对其目前在企业广泛部署中的可行性构成了现实威胁。然而，这些幻觉不应阻止组织进行实验并将这些模型集成到工作流程中。事实上，正如人工智能先驱、沃顿商学院创业教授Ethan Mollick在[推文](https://x.com/emollick/status/1716156085445239265?s=20)中所言：“……理解来自实验。”相反，幻觉带来的风险应当成为促使企业决策者认识到风险所在、采取相应措施来降低风险，并在过程中获得大语言模型（LLM）早期收益的推动力。2024年是你的企业应当跨出这一步的一年。
