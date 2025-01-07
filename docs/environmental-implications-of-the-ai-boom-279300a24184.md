# 人工智能繁荣的环境影响

> 原文：[`towardsdatascience.com/environmental-implications-of-the-ai-boom-279300a24184?source=collection_archive---------2-----------------------#2024-05-02`](https://towardsdatascience.com/environmental-implications-of-the-ai-boom-279300a24184?source=collection_archive---------2-----------------------#2024-05-02)

## 数字世界不能存在，没有自然资源来支撑它。我们用来构建和运行人工智能的技术代价是什么？

[](https://medium.com/@s.kirmer?source=post_page---byline--279300a24184--------------------------------)![Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--279300a24184--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--279300a24184--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--279300a24184--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--279300a24184--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--279300a24184--------------------------------) ·8 分钟阅读·2024 年 5 月 2 日

--

![](img/2e2731d1782760746f27aa4f4842fde4.png)

图片来源：[ANGELA BENITO](https://unsplash.com/@angelareinosa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在机器学习中有一个核心概念，我经常告诉外行人，以帮助澄清我所做工作的哲学。这个概念是：每个机器学习模型所处的世界在变化，往往是*因为*模型的存在，所以模型试图模仿和预测的世界总是过去的，而非现在或未来。从某种意义上说，模型是在预测未来——我们通常是这样看待它的——但在许多其他方面，模型实际上是在尝试将我们带回过去。

我喜欢谈论这个话题，因为机器学习的哲学帮助我们从机器学习实践者以及机器学习的用户和研究对象的角度，获得真实的视角。常读者会知道我常说“机器学习就是我们”——意思是，我们产生数据，进行训练，消费和应用模型的输出。模型试图遵循我们的指示，使用我们提供的原材料，而我们对这一过程的发生方式及其后果有着巨大的、几乎完全的控制权。

这个概念的另一个有用方面是提醒我们，模型并不是孤立存在于数字世界中，实际上它们与模拟的、物理的世界密切交织。毕竟，如果你的模型没有对我们周围的世界产生影响，那么就会引发一个问题：你的模型为什么存在？如果我们真的深入思考，数字世界和物理世界的区别仅仅在于我们作为用户/开发者如何与它互动，从某种有限的、人工的角度来看，数字世界与物理世界并不是完全分开的。

我今天想谈论的正是最后一点——物理世界如何塑造并影响机器学习，而机器学习/AI 又如何反过来影响物理世界？在我上一篇文章中，我承诺过会谈到物理世界资源的限制如何与机器学习和 AI 相交织，今天我们就要讨论这个问题。

# AI 需要物理世界

如果稍加思考，这一点大概很明显。有一个笑话说，我们可以通过关掉智能机器人或拔掉电脑的电源来击败这些有知觉的机器人霸主。但笑话归笑话，这里面确实有一定的真理。我们这些从事机器学习、AI 以及一般计算工作的人员，完全依赖于自然资源的存在，比如矿产金属、电力等。这与我去年写的一篇关于人类劳动对于机器学习存在的重要性的文章有一些相似之处，但今天我们要走一条不同的道路，讨论两个我们应该更加重视的关键领域，这些领域对我们的工作至关重要——采矿/制造和能源，主要是电力形式的能源。

如果你出去寻找，关于这两个领域的研究和报道非常丰富，不仅与 AI 直接相关，还涉及到早期的技术热潮，比如加密货币，在资源使用方面与 AI 有许多相似之处。我将对每个领域进行一般性的讨论，并提供进一步阅读的参考文献，以便你可以深入探索细节，了解学术来源。然而，很难找到考虑到过去 18 个月 AI 热潮的研究，所以我预计一些研究低估了新技术在生成型 AI 领域的影响。

# 采矿与制造

制造 GPU 芯片需要什么材料？我们知道这些芯片在现代机器学习模型的发展中至关重要，而 Nvidia，今天这些芯片的最大生产商，凭借加密货币的繁荣和人工智能的热潮，已经跻身全球最有价值的公司之一。其股价从 2021 年初的每股 130 美元涨到 2024 年 4 月我写这句话时的每股 877.35 美元，使其市值达到了[超过 2 万亿美元。](https://www.marketwatch.com/investing/stock/nvda) [2023 年第三季度，他们售出了超过 50 万颗芯片，收入超过 100 亿美元](https://www.pcgamer.com/nvidia-sells-half-a-million-ai-chips-in-just-three-months-netting-dollar10-billion-plus/)。 [估计他们 2023 年 H100 系列芯片的总销量为 150 万颗，](https://finance.yahoo.com/news/nvidia-50-billion-reasons-why-155759405.html?guccounter=1&guce_referrer=aHR0cHM6Ly93d3cuZ29vZ2xlLmNvbS8&guce_referrer_sig=AQAAAD-rp8_k5CKr4n-jT3NU6rog4qWTAOxWof2syroLJ3beWh1PooQlDO72qrqWrFg9RdkQyfpPYpDD2UExTsHZoO_FceXbj3GNXJZeUJnRpylxc6SAzuCzeGhF76HgjbTrRGJ9p9P2vvAr8P6kC1LsKiT-73jhjnUgETkel4cP7osz)，预计 2024 年将轻松超过这一数字。

GPU 芯片涉及多种不同的[特殊原材料，这些原材料相对稀有且难以获得，包括钨、钯、钴和钽。](http://www.designlife-cycle.com/nvidia-gpu) 其他元素可能更容易获得，但具有显著的健康和安全风险，例如汞和铅。开采这些元素和化合物对环境有重大影响，包括排放和对采矿地区环境的破坏。即使是最好的采矿作业，也会严重改变生态系统。这还不包括所谓的“冲突矿产”的风险，即在人类剥削、童工或奴役的情况下开采的矿物。（应给予肯定：Nvidia 一直在公开避免使用这种矿产，[特别提到刚果民主共和国](https://images.nvidia.com/content/includes/gcr/pdf/nvidia-responsible-minerals-policy.pdf)。）

此外，在原材料被开采后，所有这些材料都必须经过极为仔细的加工，才能生产出运行复杂计算的微小、高效能芯片。正如我们从过去 150 多年的工业历史中所知，工人在处理[重金属](https://www.osha.gov/semiconductors)时面临着[重大健康风险](https://www.osha.gov/toxic-metals)，例如[铅和汞](https://www.osha.gov/toxic-metals)。Nvidia 的芯片大多是在台湾的工厂生产，这些工厂由一家名为台积电（TSMC）的公司运营。因为[Nvidia 并不拥有或运营](https://www.designlife-cycle.com/nvidia-gpu)这些工厂，所以 Nvidia 能够避免因制造条件或排放而受到批评，而且数据也很难获取。制造所需的电力也不在 Nvidia 的账目上。顺便提一句：[台积电的产能已达到最大，正在努力提升产能。与此同时，Nvidia 计划在明年开始与 Intel 合作，提升制造能力。](https://www.tomshardware.com/pc-components/gpus/nvidia-reportedly-selects-intel-foundry-services-for-chip-packaging-production-could-produce-over-300000-h100-gpus-per-month#:~:text=Nvidia%20reportedly%20selects%20Intel%20Foundry,GPUs%20per%20month%20%7C%20Tom's%20Hardware)

一旦芯片生产完成，它的使用寿命可能会相当长——如果保养得当，可能是 3 到 5 年——然而，Nvidia 不断生产新的、更强大、更高效的芯片（每年生产 200 万颗可不是个小数字！），因此芯片的使用寿命可能受到过时以及磨损的限制。当芯片不再有用时，它就会进入所谓的“[电子废物](https://www3.weforum.org/docs/WEF_A_New_Circular_Vision_for_Electronics.pdf)”的处理流程。从理论上讲，芯片中许多稀有金属应当具有一定的回收价值，但正如你所料，芯片回收是一项非常专业且具有挑战性的技术任务，而且所有电子废物中，只有大约 20%能够被回收，其中包括手机等其他较为简单的硬件。回收过程还需要工人拆卸设备，这样他们就会再次接触到制造过程中使用的重金属和其他元素。

如果芯片没有被回收，另一方面，它很可能会被[倾倒在垃圾填埋场或焚烧，重金属通过水、空气或两者渗入环境](https://www.mdpi.com/1660-4601/18/16/8488)。这种情况发生在发展中国家，且常常直接影响到人们居住的地区。

然而，大多数关于机器学习碳足迹及其整体环境影响的研究，都集中在电力消耗方面。所以我们来看看这个方向。

# 电力

一旦我们具备了完成工作的硬件，AI 领域中最为显著的问题无疑是电力消耗。训练大型语言模型需要消耗巨量的电力，而部署和服务 LLM（大型语言模型）及其他先进的机器学习模型同样是一个电力黑洞。

在训练的情况下，一篇研究论文建议，训练拥有 1750 亿参数的 GPT-3 大约需要消耗[1,300 兆瓦时](https://www.theverge.com/24066646/ai-electricity-energy-watts-generative-consumption)（MWh）或 1,300,000 千瓦时（KWh）的电力。与此相比，GPT-4 使用了 1.76 *万亿*个参数，估计训练所需的电力消耗在[51,772,500 到 62,318,750 千瓦时](https://www.theverge.com/24066646/ai-electricity-energy-watts-generative-consumption)之间。作为参考，平均每个美国家庭每年使用的电力超过 10,000 千瓦时。因此，从保守估计来看，训练一次 GPT-4 的电力消耗足以为近 5,000 个美国家庭提供一年的电力。（这还没有考虑到在准备数据并为训练做准备时，几乎肯定需要进行的初步分析或测试所消耗的电力。）

鉴于 GPT-3 和 GPT-4 在训练过程中的电力使用量大约增加了 40 倍，我们必须关注这些模型的未来版本可能带来的电力消耗，以及用于训练生成视频、图像或音频内容的模型的电力消耗。

在训练过程之后，只有在模型的生命周期中进行一次的训练之外，推理任务的电力消耗也在快速增长，换句话说，每当你向 Chat-GPT 提问或尝试用 AI 工具生成有趣图像时，都会产生电力成本。[这种电力由数据中心吸收](https://e360.yale.edu/features/artificial-intelligence-climate-energy-emissions)，这些数据中心运行模型，提供全球范围的结果。国际能源署[预测到 2026 年，仅数据中心的电力消耗就将达到 1,000 太瓦时](https://iea.blob.core.windows.net/assets/6b2fd954-2017-408e-bf08-952fdd62118a/Electricity2024-Analysisandforecastto2026.pdf)，大致相当于日本的电力使用量。

AI 行业的主要参与者显然已经意识到，[电力消耗的这种增长是不可持续的](https://apple.news/AlpimrKJDSlO3j7WYIsWHfQ)。有估计表明，数据中心消耗了全球电力使用量的 0.5%到 2%，并且到 2030 年，数据中心可能占美国电力使用量的[25%](https://apple.news/Ai_OU0hg3Qv6mebb1mq7cNQ)。

美国的电力基础设施状况不佳——我们当然在尝试将更多可再生能源并入电网，但我们理应不是一个能够很好管理公共基础设施的国家。[德克萨斯州居民尤其](https://www.eenews.net/articles/texas-grid-has-gone-3-years-without-a-crisis-will-it-last/)深知我们电力系统的脆弱性，但在美国范围内，[气候变化带来的极端天气状况增加导致了电力中断](https://www.cfr.org/backgrounder/how-does-us-power-grid-work)，这种情况的发生频率正在不断上升。

电力基础设施的投资是否能够满足 AI 工具所带来的急剧增长的需求，尚需观察，而由于政府行动在实现这一目标中至关重要，因此持悲观态度是合理的。

与此同时，即使我们确实能以必要的速度生产电力，直到可再生和无排放的电力来源能够规模化使用之前，我们仍在通过使用这些 AI 工具显著增加全球的碳排放。[粗略估计，每千瓦时电力排放 0.86 磅碳](https://www.eia.gov/tools/faqs/faq.php?id=74&t=11)，训练 GPT-4 的过程中会向大气中排放超过 20,000 公吨的碳。（相比之下，普通美国人每年排放 13 公吨碳。）

# 好吧，那又怎么样呢？

正如你所预料的，我并不是在这里争论我们应该因为机器学习消耗自然资源而停止这项工作。我认为那些使我们生活成为可能的工人应当得到显著的工作安全保障和与风险相称的补偿，我也认为在面对可预防的、由人类引起的气候变化时，可再生电力来源应该是一个重要优先事项。

但我谈论这些，是因为了解我们的工作有多么依赖于物理世界、自然资源和地球，应该让我们更加谦逊，感恩我们所拥有的一切。当你进行训练或推理，或使用 Chat-GPT 或 Dall-E 时，你并不是这个过程的终点。你的行为会产生下游的后果，认识到这一点并据此做出明智的决策非常重要。你可能在租用别人 GPU 的几秒钟或几小时，但这仍然需要电力，并且会对 GPU 造成磨损，最终这些 GPU 会需要被处置。成为有伦理的世界公民的一部分，就是思考你的选择并考虑你对他人的影响。

此外，如果你有兴趣了解自己建模工作对碳足迹的影响，可以使用一个工具：[`www.green-algorithms.org/`](https://www.green-algorithms.org/)

阅读更多我的作品，请访问[www.stephaniekirmer.com.](http://www.stephaniekirmer.com.)
