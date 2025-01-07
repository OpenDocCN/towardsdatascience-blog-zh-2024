# 如果ChatGPT实际上是来自另一个世界的导游呢？（第二部分）

> 原文：[https://towardsdatascience.com/what-if-chatgpt-is-actually-a-tour-guide-from-another-world-part-2-fa0e50927f1d?source=collection_archive---------1-----------------------#2024-04-13](https://towardsdatascience.com/what-if-chatgpt-is-actually-a-tour-guide-from-another-world-part-2-fa0e50927f1d?source=collection_archive---------1-----------------------#2024-04-13)

![](../Images/801243c3260808af2dffb8121d6c3a08.png)

## 我测试了一个直觉，并在GPT内部发现了一些既美丽又神秘的东西。

[](https://mayoinmotion.medium.com/?source=post_page---byline--fa0e50927f1d--------------------------------)[![John Mayo-Smith](../Images/105ea7583fa67c1f35276d7ad6dd874e.png)](https://mayoinmotion.medium.com/?source=post_page---byline--fa0e50927f1d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa0e50927f1d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fa0e50927f1d--------------------------------) [John Mayo-Smith](https://mayoinmotion.medium.com/?source=post_page---byline--fa0e50927f1d--------------------------------)

·发表于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--fa0e50927f1d--------------------------------) ·阅读时长6分钟·2024年4月13日

--

[第一部分](https://mayoinmotion.medium.com/what-if-chatgpt-is-actually-a-tour-guide-from-another-world-1652f378e907)的假设是*ChatGPT是一个导游，带领我们穿越计算机游戏Minecraft的高维版本*。

吓人吗？当然，但我还是测试了这个假设，并在GPT中发现了一些既美丽又神秘的东西。这就是我发现的内容，以及我揭开这一发现的步骤。

首先，我们将澄清“高维”是什么意思。然后，我们将从GPT-4收集维度数据，并将其与Minecraft进行比较。最后，为了好玩，我们将创建一个使用实际GPT-4数据结构的Minecraft世界，看看它是什么样子的。

为了澄清“维度”，请考虑以下引用：

> **“*我认为，理解并将GPT-4视为工具而非生物非常重要，虽然很容易搞混...*”** — OpenAI首席执行官Sam Altman，在2023年5月16日的参议院司法委员会隐私与技术小组听证会上的证词

马还是锤子？我们可以直接问ChatGPT。然而，答案将取决于ChatGPT的自我意识程度，而这又依赖于它的生物性，因此形成了一个死循环。

相反，我们将依赖直觉，并从不同的维度来观察这个问题。维度是某种可测量的范围。例如，在“工具”维度中，锤子似乎比马更“像工具”。在二维维度下，情况也类似。马更像生物，而不像工具，远不如锤子。

GPT适合在哪个位置？可能更接近两者中的锤子。

如果我们添加一个名为“智能”的第三维度会怎么样？这时事情变得有趣了。马比一袋锤子聪明，而 GPT 看起来也相当聪明。所以，在这三个维度中，GPT 实际上可能位于马和锤子之间的*某个地方*。

![](../Images/7cf488b6245fd71791cd06879aa90fb5.png)

锤子与马的插图。Rawpixel。[https://www.rawpixel.com/image/6439222/](https://www.rawpixel.com/image/6441770); [https://www.rawpixel.com/image/6440314](https://www.rawpixel.com/image/6440314)

可视化二维是很容易的，三维稍微难一些，但我们完全可以用成千上万的维度来描述马和锤子。实际上，使用多维度来衡量事物是有充分理由的，因为它能增强对事物的理解。GPT 的神奇之处在于，它似乎不仅仅绘制了马和锤子，而是几乎所有事物都在*成千上万*的维度中！

但是，GPT 是如何在成千上万维度中表示事物的呢？

使用一种叫做*嵌入*的技术。

嵌入是将单词、图片和其他数据转换成一组数字的方式，目的是让计算机能够理解它们的含义并进行比较。

假设我们希望计算机理解苹果和柠檬的含义。为每种水果分配一个数字可能行得通，但水果比一个数字要复杂得多。所以，我们使用一组数字，其中每个数字表示水果的外观、味道和营养成分等。这些数字列表就是*嵌入*，它们帮助 ChatGPT 明白苹果和柠檬都是水果，但味道不同。

遗憾的是，GPT 的嵌入表示超出了人类的理解和可视化范围。例如，仅仅是“apple”这个词的三千个嵌入看起来是这样的：

![](../Images/b5660d3e7de9d3751c589734471ec44f.png)

是否有可能在不影响整体结构的情况下减少维度数？幸运的是，这种情况时常发生——在一个阳光明媚的日子里，你的影子就是你三维身体的二维投影。虽然有很多复杂的数学方法可以进行降维，但我们将简化处理，直接取 OpenAI 给出的前三个嵌入，其他的则丢弃。

**这可能行得通吗？**

让我们来看看。我们将从选择几个单词开始实验：*horse, hammer, apple, 和 lemon*。然后，为了保持趣味性，我们还将选择一些可能（也可能不）在语义上相关的单词和短语：“cinnamon”，“given to teachers”，“pie crust”，“hangs from a branch”，以及“crushed ice”。

接下来，我们将查看它们的嵌入。OpenAI 提供了一个名为嵌入引擎的工具，它让这变得很简单。你输入一个单词或短语，它会返回三千个嵌入（准确来说是 3,072 个）。

通过使用一段[代码](https://github.com/mayosmith/3d-embeddings)，我们将提取每个单词的前三个嵌入，其他的则丢弃。结果如下：

![](../Images/cd4f466e3403c9ee203abc41871d5e87.png)

这些数字究竟是什么？如果我们说实话，没人真的知道；它们似乎能精准地定位每个单词和短语在GPT内部某个特定且神秘的维度中的位置。为了我们的目的，暂且将这些嵌入向量当作x、y、z坐标来处理。这种方法需要一个令人震惊的大胆假设，但我们不会在这上面停留——而是将它们绘制在图表上，看看到底会出现什么。

![](../Images/47b247a2b43f24df1a28c0af94dfc237.png)

图片由Plotly.com创建

**你看到了吗？！**

[John Firth](https://cs.brown.edu/courses/csci2952d/readings/lecture1-firth.pdf)一定会感到骄傲。像苹果一样的东西似乎是邻居（准备做个派）。碎冰和柠檬彼此相邻（准备做柠檬水）。锤子则在角落里。

如果你对这个结果没有感到完全震惊，也许是因为你是一名已经见惯了这些的 数据科学家。对我而言，我简直不敢相信刚刚发生的事情：我们查找了九个单词和短语的嵌入向量，丢弃了99.9%的数据，然后将剩余的部分绘制到一个3D图上——令人惊讶的是，这些位置直观上是有意义的！

还是不觉得震惊吗？那么也许你在想，这一切与Minecraft有什么关系。对于玩家们来说，我们即将把分析推向下一步。

使用[Minecraft Classic](https://classic.minecraft.net/)，我们将构建一个8x8x8的围墙花园，然后像我们在3D图中做的那样“绘制”这些单词和短语。看看它是如何呈现的：

![](../Images/7b336261991e200f9eabe73878cae1b4.png)

请注意，花园中单词和短语的位置与3D图中的位置一致。这是因为嵌入向量就像虚拟世界中的位置坐标——在这里，就是Minecraft。我们所做的，是将一个3,072维的嵌入空间缩减到Minecraft中的三维“影像”空间，然后像这样进行探索：

![](../Images/0aeb8e9310a663fc3291cfbac8eb2167.png)

谁是在我们花园中跳跃的探险者？那就是ChatGPT，高维的讲解员，精通复杂的数据结构——我们通往GPT优雅神秘世界的使者。当我们提交提示时，正是ChatGPT辨别我们的意图（这可不是小事，利用所谓的注意力机制），然后轻松地穿越数千维度，引领我们到达GPT宇宙中恰到好处的地方。

这一切是否意味着ChatGPT实际上是来自另一个世界的导游？它真的在一个高维游戏空间中运作吗？虽然我们不能确定，但GPT似乎比马或锤子更像一个游戏：

![](../Images/a1c5443e34bb8702bc5d5a581827ea09.png)

锤子与马的插图。Rawpixel。[https://www.rawpixel.com/image/6439222/](https://www.rawpixel.com/image/6441770); [https://www.rawpixel.com/image/6440314](https://www.rawpixel.com/image/6440314)

*除非另有说明，所有图片均由作者提供。*

—

*参考资料：*

“API参考。” OpenAI，[2024年4月4日]。[https://platform.openai.com/docs/api-reference](https://platform.openai.com/docs/api-reference)。

Sadeghi, Zahra, James L. McClelland, 和 Paul Hoffman. “你将通过单词的共现了解一个对象：基于对象在视觉场景中的共现所推导出的语义表示的研究。”《神经心理学》76（2015）：52–61。

Balikas, Georgios. “开放源代码与商业嵌入模型在问答中的比较分析。”《第32届ACM国际信息与知识管理大会论文集》。2023年。

Hoffman, Paul, Matthew A. Lambon Ralph, 和 Timothy T. Rogers. “语义多样性：基于词语上下文使用变异性的语义模糊度度量。”《行为研究方法》45（2013）：718–730。

Brunila, Mikael, 和 Jack LaViolette. “单词都与哪些公司有关？重新审视JR Firth和Zellig Harris的分布式语义。”arXiv预印本 arXiv:2205.07750（2022年）。

Gomez-Perez, Jose Manuel, 等. “理解词嵌入和语言模型。”《混合自然语言处理实用指南：结合神经模型和知识图谱进行NLP》 （2020年）：17–31。
