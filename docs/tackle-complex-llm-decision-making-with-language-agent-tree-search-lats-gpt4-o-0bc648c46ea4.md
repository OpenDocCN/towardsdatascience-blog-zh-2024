# 应对复杂LLM决策制定：语言代理树搜索（LATS）与GPT-4o的结合

> 原文：[https://towardsdatascience.com/tackle-complex-llm-decision-making-with-language-agent-tree-search-lats-gpt4-o-0bc648c46ea4?source=collection_archive---------2-----------------------#2024-08-26](https://towardsdatascience.com/tackle-complex-llm-decision-making-with-language-agent-tree-search-lats-gpt4-o-0bc648c46ea4?source=collection_archive---------2-----------------------#2024-08-26)

## 增强LLM决策制定：将语言代理树搜索与GPT-4o结合，实现卓越的问题解决能力

[](https://cloudatlas.me/?source=post_page---byline--0bc648c46ea4--------------------------------)[![Ozgur Guler](../Images/0fa0f9412fddfff81c9004af33e277e8.png)](https://cloudatlas.me/?source=post_page---byline--0bc648c46ea4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0bc648c46ea4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0bc648c46ea4--------------------------------) [Ozgur Guler](https://cloudatlas.me/?source=post_page---byline--0bc648c46ea4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0bc648c46ea4--------------------------------) ·9分钟阅读·2024年8月26日

--

![](../Images/1af94ed3cff9126b91f6176f1e210758.png)

作者图片：midjourney — 抽象拼图

大型语言模型（LLMs）在执行涉及复杂推理的自然语言任务中展现出了卓越的能力。因此，这些模型已经发展成能够规划、制定战略并解决复杂问题的代理。然而，当面临不确定性时，决策仍然存在挑战，尤其是在结果不是确定性的情况下，或者当在变化的环境中需要适应性决策时，尤其是在多步骤场景中，每一步都会影响到下一步。我们需要更先进的能力……

这是GPT-4的高级推理能力和语言代理树搜索（LATS）结合在一起，解决这些挑战的地方。LATS结合了一种动态的基于树的搜索方法，增强了GPT-4O的推理能力。通过将蒙特卡洛树搜索（MCTS）与大语言模型（LLMs）结合，LATS统一了推理、行动和规划，创造了一个更加深思熟虑且适应性强的问题解决框架。这一强大的组合提高了决策制定的能力，更加稳健地应对复杂任务，为语言模型作为自主代理的应用设立了新的标准。

## “搜索”是生成式AI问题解决中的缺失环节吗？

![](../Images/2a16198d5a97e718d980dec57e08a408.png)

作者图片：midjourney — 抽象拼图

计算问题解决可以广泛定义为“***在组合问题空间中搜索***”，通常表现为一棵树。[**深度优先搜索（DFS）**](https://en.wikipedia.org/wiki/Depth-first_search)和[**广度优先搜索（BFS）**](https://en.wikipedia.org/wiki/Breadth-first_search)是探索这些解空间的基本方法。深度搜索的一个显著例子是AlphaGo的“[第37手](https://en.wikipedia.org/wiki/AlphaGo_versus_Lee_Sedol)”，它展示了如何通过广泛探索涌现出创新的、超越人类的解决方案。

与遵循**预定义路径**的传统方法不同，LLMs可以通过根据上下文预测潜在的结果、策略或行动，动态生成解空间中的新分支。这一能力使得LLMs不仅能够导航，还能扩展问题空间，使它们在问题结构尚不完全明确、不断演变或高度复杂的情况下具有非凡的能力。

## 基于推理时间的推理与元生成算法（MGA）

![](../Images/6678b242a08f9b2c8c2ea23a1627ce23.png)

作者提供的图像：midjourney — 抽象拼图

在训练过程中扩展计算能力被广泛认可为提高模型性能的有效方法。**在推理过程中扩展计算的好处仍然没有得到充分探索。** MGA通过在推理过程中增强计算资源提供了一种新颖的方法……

与传统的标记级生成方法不同，元生成算法采用更高阶的控制结构，如规划、带有多个模型调用的循环、自我反思、任务分解和动态调节。这些机制使得模型能够端到端地执行任务，模仿通常被称为系统2思维的高级认知过程。

![](../Images/398973dd49349edcc22c35cf5384dc0f.png)

作者提供的图像：推理时间推理方法 — 摘要

**因此，一种单向的元生成算法可能通过将搜索整合到生成过程中，增强LLM的推理能力**。在推理过程中，MGA动态地探索更广泛的解空间，使模型能够实时地推理潜在结果并调整策略。通过生成多个路径并评估其可行性，元生成算法使LLMs能够模拟出类似于传统搜索方法的更深层次、更复杂的推理。这种方法不仅扩展了模型生成新颖见解的能力，还改善了在信息不完整或不断变化的情况下的决策能力。

技术如**思想树（ToT）**和**思想图（GoT）**被用来高效地在组合解空间中导航。

+   **ToT** (*2**)通过将潜在结果结构化为树枝来实现分层决策，使得探索多个路径成为可能。

+   **GoT (***6****)** 映射了思想之间的复杂关系，使得模型能够动态调整并优化其推理路径。

+   **CoT** (*5**)提供逐步推理，链接顺序思维，增强生成的连贯性和深度。

# 为什么MCTS更好？

在思想树（ToT）方法中，传统方法如深度优先搜索（DFS）或广度优先搜索（BFS）可以遍历这棵树，但它们计算开销大，因为它们系统性地和穷举地探索每一条路径。

蒙特卡洛树搜索（MCTS）通过模拟不同的行动结果并根据这些模拟更新树结构，改进了这一点。它采用一种“选择”过程，通过平衡探索（尝试新路径）和利用（选择已知有效路径）的策略来选择决策节点。这一过程由一个称为上置信界（UCB）的公式指导。

UCB公式有两个关键部分：

1.  **探索项：** 这代表选择某个节点的潜在奖励，通过模拟进行计算。

1.  **利用项：** 这个值随着你进入某条路径的深度而减少，这意味着如果某条路径被过度探索，算法可能会转向一个探索较少的路径，即使该路径最初看起来不那么有前景。

通过使用UCB选择节点，利用LLM模拟结果（奖励），并将奖励反向传播到树的上层，MCTS有效地平衡了探索新策略和利用已知成功策略之间的关系。

UCB公式的第二部分是**‘利用项’**，它随着你在特定路径中的探索深度增加而减少。这种减少可能导致选择算法切换到决策树中的另一条路径，即使那条路径的即时奖励较低，因为当该路径较少被探索时，利用项会保持较高。

使用UCB进行节点选择、通过LLM模拟进行奖励计算以及反向传播是MCTS的核心。

## 一种实现——金融决策……

![](../Images/3710dd691db8b2ce49c431938a9ad80f.png)

LATS 操作 (1*) [https://arxiv.org/pdf/2310.04406](https://arxiv.org/pdf/2310.04406)

为了演示，我们将使用LATS来解决在当今宏观经济环境中制定最佳投资策略的挑战性问题。我们将使用“国际货币基金组织世界经济展望报告”中的宏观经济状况作为上下文，将文档简要总结后输入LLM。RAG不使用。以下是LATS如何在解空间中进行搜索的示例……

## 迭代1：

1.  **选择：** 我们从根节点开始，由于这是第一次LATS迭代，我们将选择LLM生成的所有初始决策节点（A、B和C节点）并模拟它们的结果。

1.  **模拟与反向传播：** NextLLM根据它所拥有的上下文“模拟”每个策略，并为每个“节点”分配以下“奖励”——投资回报。

+   **策略A**：$5,000

+   **策略B**：$7,000

+   **策略C**：$4,000

3. **扩展：** 根据选择，**策略 B** 具有最高的 UCB1 值（因为所有节点处于相同深度），所以我们仅通过模拟其子节点来扩展 **策略 B**。

![](../Images/dbce1fa357313bed6d2079283a726f17.png)

作者图示：由于模拟奖励值更高，B 节点被扩展

## 第 2 次迭代：

1.  **选择：** 由于 B1 和 B2 策略没有被模拟，它们的 UCB 分数存在平局，因此两个节点都将被模拟。

1.  **模拟两个节点**：

+   **模拟 B1**：LLM 预测 **B1** 的回报为 **$8,500**。

+   **模拟 B2**：LLM 预测 **B2** 的回报为 **$7,500**。

3. **反向传播：**

每次模拟后，模拟结果会被反向传播到树中，更新父节点的值。此步骤确保新信息的影响在整个树中得到反映。

**更新策略 B 的值：** **策略 B** 现在需要反映 **B1** 和 **B2** 的结果。一种常见的方法是平均 **B1** 和 **B2** 的奖励，以更新 **策略 B** 的值。现在，**策略 B** 的更新值为 **$8,000**，基于其子节点的结果。

![](../Images/bc33d70a8576e8f639748eacc8d2360a.png)

作者图示：策略 B 的奖励值在反向传播后被更新

4. **重新计算 UCB 分数：**

反向传播后，**所有树中节点的 UCB 分数**都会重新计算。此重新计算使用更新后的值（平均奖励）和访问次数，确保每个节点的 UCB1 分数准确反映其潜在奖励和已被探索的程度。

UCB(s) = (探索/奖励项) + (利用项)

再次注意，对于所有路径上持续被更深探索的节点，其利用项会减小。

**5. 下一步选择与模拟：**

**B1** 被选中进行进一步扩展（因为它具有更高的奖励），进入其子节点：

+   **B1a**：“投资于人工智能公司”

+   **B1b**：“投资于绿色技术”

![](../Images/da47d0a75d12cd098eebbf2a64845349.png)

作者图示：由于有更高的奖励，B1 节点进一步扩展

6. **反向传播：**

![](../Images/0e80b6cb8ff2fcd14c6e1672b6db1165.png)

作者图示：子节点的奖励值被反向传播到父节点

B1 的奖励值更新为 (9200 + 6800) / 2 = 8000

B 的奖励更新为 (8000 + 7500) / 2 = 7750

**7. UCB 计算：**

在反向传播后，所有节点的 UCB 值都会重新计算。假设由于衰减的探索因子，**B2** 现在的 UCB 分数高于 **B1a** 和 **B1b**。如果 **B1** 已经被广泛探索，导致其子节点的探索项减少，那么这种情况就会发生。算法会转而探索 **B2**，因为 **B2** 因为未被探索的潜力变得更有吸引力，即其更高的利用价值。

![](../Images/1f27254ac66074364aa2ee0ff364b4c4.png)

作者提供的图片：当深入探索节点的路径时，节点的进一步利用价值会下降，这可能会触发分支切换——即通过新的决策节点进一步探索的新路径

该示例展示了MCTS如何基于新信息动态调整其搜索路径，确保算法在进行过程中仍然高效，并集中关注最有前景的策略。

## 使用Azure OpenAI GPT-4o的实现

接下来我们将使用GPT-4o构建一个“财务顾问”，实现LATS。（代码请参阅GitHub仓库[这里](https://github.com/ozgurgulerx/llm-reasoning-lats/tree/main)。）

*(为了进行准确分析，我正在使用* [*IMF世界经济展望报告*](https://www.imf.org/en/Publications/WEO) *（2023年7月24日发布）作为我的LLM上下文进行模拟，即用于生成子节点并为决策节点分配奖励…)*

这是代码运行的方式…

LATS迭代MCTS遍历决策树，创建新节点并探索树

该代码利用`graphviz`库可视化表示在执行投资策略模拟过程中生成的决策树。由于决策树过于宽广，无法容纳在一张图片中，因此我已经添加了关于树形图样式的片段。您可以在GitHub仓库[这里](https://github.com/ozgurgulerx/llm-reasoning-lats/blob/main/investment_decision_tree_with_optimal_path.pdf)找到一个决策树示例…

![](../Images/be7c8818cadaa56d25b6e271fecc3611.png)

作者提供的图片：MCTS代码运行示例，用于在当前宏观经济环境下寻找最佳投资策略

![](../Images/b8b3f1f0093188458ab090f95a3b765d.png)

作者提供的图片：来自生成的决策树的屏幕截图

以下是LATS推断出的最佳策略…

```py
Optimal Strategy Summary: The optimal investment strategy is structured around several key steps influenced by the IMF report. Here's a concise summary of each step and its significance:
1\. **Diversification Across Geographies and Sectors:**
 - **Geographic Diversification:** This involves spreading investments across regions to mitigate risk and tap into different growth potentials. Advanced economies like the U.S. remain essential due to their robust consumer spending and resilient labor market, but the portfolio should include cautious weighting to manage risks. Simultaneously, emerging markets in Asia, such as India and Vietnam, are highlighted for their higher growth potential, providing opportunities for higher returns.
 - **Sector Diversification:** Incorporating investments in sectors like green energy and sustainability reflects the growing global emphasis on renewable energy and environmentally friendly technologies. This also aligns with regulatory changes and consumer preferences, creating future growth opportunities.
2\. **Green Energy and Sustainability:**
 - Investing in green energy demonstrates foresight into the global shift toward reducing carbon footprints and reliance on fossil fuels. This is significant due to increased governmental supports, such as subsidies and policy incentives, which are likely to propel growth within this sector.
3\. **Fintech and E-Commerce:**
 - Allocating capital towards fintech and e-commerce companies capitalizes on the digital transformation accelerated by the global shift towards digital platforms. This sector is expected to grow due to increased adoption of online services and digital payment systems, thus presenting promising investment opportunities.
```

## 结论：

通过整合LATS，我们利用大型语言模型（LLMs）的推理能力，动态模拟和评估潜在的策略。这种结合不仅能构建出代表决策逻辑进展的决策树，还能根据LLM通过模拟和反思提供的变化的背景和见解进行自适应。

（除非另有注明，所有图片均来自作者）

## 参考文献：

*[1] 语言代理树搜索：统一推理、行动和规划在语言模型中的应用* 由Zhou等人撰写

*[2] 思维树：利用大型语言模型进行深思熟虑的问题解决* 由Yao等人撰写

*[3] 新兴AI代理架构在推理、规划和工具调用中的应用：一项调查* 由Tula Masterman、Mason Sawtell、Sandi Besen和Alex Chao撰写

*[4] 从解码到元生成：大型语言模型的推理时间算法* 由Sean Welleck、Amanda Bertsch、Matthew Finlayson、Hailey Schoelkopf、Alex Xie、Graham Neubig、Ilia Kulikov和Zaid Harchaoui撰写。

*[5] 连锁思维提示引发大语言模型的推理*，作者：Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, Fei Xia, Ed H. Chi, Quoc V. Le 和 Denny Zhou。

*[7] 思维图谱：使用大语言模型解决复杂问题*，作者：Maciej Besta, Nils Blach, Ales Kubicek, Robert Gerstenberger, Michał Podstawski, Lukas Gianinazzi, Joanna Gajda, Tomasz Lehmann, Hubert Niewiadomski, Piotr Nyczyk 和 Torsten Hoefler。

*[8] 从解码到元生成：大语言模型的推理时算法*，作者：Sean Welleck, Amanda Bertsch, Matthew Finlayson, Hailey Schoelkopf, Alex Xie, Graham Neubig, Ilia Kulikov 和 Zaid Harchaoui。
