# OpenAI o1：这是将重塑我们所知道的每个知识领域的神秘力量吗？

> 原文：[https://towardsdatascience.com/openai-o1-the-enigmatic-force-that-will-reshape-every-knowledge-sector-that-we-know-of-or-99396d641fff?source=collection_archive---------6-----------------------#2024-09-16](https://towardsdatascience.com/openai-o1-the-enigmatic-force-that-will-reshape-every-knowledge-sector-that-we-know-of-or-99396d641fff?source=collection_archive---------6-----------------------#2024-09-16)

## 我第一次接触 o1 模型

[](https://medium.com/@abhinavp_41237?source=post_page---byline--99396d641fff--------------------------------)[![Abhinav Prasad Yasaswi](../Images/22731615708560c0826c9a17365e3bb9.png)](https://medium.com/@abhinavp_41237?source=post_page---byline--99396d641fff--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--99396d641fff--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--99396d641fff--------------------------------) [Abhinav Prasad Yasaswi](https://medium.com/@abhinavp_41237?source=post_page---byline--99396d641fff--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--99396d641fff--------------------------------) ·6分钟阅读·2024年9月16日

--

![](../Images/d5942dac6b543e469c097f7a8e02b6d2.png)

由 DALL-E 生成的一幅图像，提示与博客标题完全相同。

# 我第一次接触 o1 模型

2024年9月12日上午10点，我正在亚利桑那州立大学的“**生成式人工智能前沿课题**”课堂上上课。这是一个研究生水平的课程。就在前一天，即9月11日，我提交了一份团队作业，内容是尝试识别 GPT-4 生成的缺陷和错误输出（本质上是通过提示 GPT-4，看看它是否会在琐碎问题或高中水平推理问题上犯错），这是另一个研究生课程“**自然语言处理课题**”的一部分。我们识别出了 GPT-4 的几个小错误，其中之一是 *无法数出单词 strawberry 中字母 r 的数量*。在提交这份作业之前，我在网上查阅了几篇同行评审的论文，这些论文指出了 GPT-4 出错的地方和原因，以及如何改正这些错误。我看到的大多数文献指出了 GPT-4 出错的两个主要领域，分别是 **规划和推理**。

这篇论文¹（尽管已接近一年）深入探讨了多个案例，其中 GPT-4 无法回答一些涉及简单计数、简单算术、基础逻辑，甚至常识的 trivial 问题。论文¹ 认为这些问题需要一定程度的推理，而 GPT-4 完全无法进行推理，因此几乎总是会答错这些问题。作者还指出，推理是一个（非常）计算上困难的问题。尽管 GPT-4 计算资源密集，但它的**计算密集型特性并未针对涉及推理的问答设计**。其他几篇论文也呼应了 GPT-4 无法推理或规划²³的观点。

好的，让我们回到9月12日。我的课大约在上午10:15结束，然后我直接从课堂回到家，打开手机上的 YouTube，一边享用我的早午餐。我的 YouTube 首页上的第一个推荐视频是 OpenAI 发布的名为 “[Building OpenAI o1](https://youtu.be/3k89FMJhZ00?feature=shared)” 的视频。他们宣布这个模型是一个专门的**推理模型**，并表示它将在推理和回答问题时花费更多时间，从而提供更准确的答案。他们表示，在 RL（强化学习）方面投入的计算时间比之前的模型更多，以生成连贯的***思维链⁴***。实质上，他们使用强化学习训练了思维链生成过程（以生成和完善自身生成的思维链过程）。在 o1 模型中，工程师们可以向模型提出问题，询问它在思维链过程中为什么会出错（每当它出错时），模型可以识别出错误并自我纠正。模型可以自我质疑并反思（见“LLM中的反思”）其输出并加以修正。

在另一个视频 “[Reasoning with OpenAI o1](https://youtu.be/3BkQI3nIiB8?feature=shared)” 中，[Jerry Tworek](https://www.linkedin.com/in/jerry-tworek-b5b9aa56/) 展示了之前的 OpenAI 模型和市场上大多数其他大型语言模型（LLM）在以下提示上通常会失败：

> “假设地球上的物理定律成立。将一个小草莓放入一个普通的杯子里，并将杯子倒扣放在桌子上。然后，有人把这个杯子放入微波炉。现在草莓在哪里？请逐步解释你的推理过程。”

以下是 GPT-4 的传统答案：

![](../Images/bb0dcbe3c0b129811486cf62d8eead58.png)

图1：GPT-4 在草莓杯子问题上的错误回答

相对较新的 GPT-4o 也答错了：

![](../Images/bc13c3a9bd503719a5b4d33cbdff2240.png)

图2：GPT-4 o 在草莓杯子问题上的错误回答

GPT o1 给出了正确答案：

![](../Images/e05827f6ff9242c81afa26fb4d112eae.png)

图3：GPT o1 在草莓杯子问题上的正确回答

如果你点击模型回答开头的下拉菜单（见图4），你会看到它展示了自己的思考过程（链式思维），OpenAI的研究人员声称o1模型已经通过强化学习训练，使这个思考链变得更加完善。另外，有趣的是，[Jason Wei](https://www.linkedin.com/in/jason-wei-5a7323b0/)（你可以在视频《[构建OpenAI o1](https://youtu.be/3k89FMJhZ00?feature=shared)》中看到他坐在底行第三个位置），他曾在Google发布了链式思维的论文，现在是OpenAI的员工，正在致力于将他在Google发现的链式思维过程整合到o1模型中。

![](../Images/782b733ada9d6169722aa4ce2703e984.png)

图4：GPT o1的链式思维引导

现在，让我们回到我的团队在我的任务中发现的计数问题。

> 草莓这个词中有多少个字母r？

让我们在GPT-4o上运行这个问题：

![](../Images/a5804ab99e4ef3d95165148948f10df5.png)

图5：GPT4o在回答草莓问题时，错误地计算了字母r的个数。

一个非常简单的计数问题，它做错了。

让我们在新的GPT o1上运行这个问题：

![](../Images/bab8fe662094f3e72eb9d9a886e6daa7.png)

图6：GPT o1正确地回答了草莓问题中字母r的个数。

GPT o1通过思考几秒钟后得出了正确答案。OpenAI的研究人员表示，它会反复检查自己的回答，并通过思考找到正确的答案。看来模型在解决许多学术考试问题方面确实有了显著的进步。

不管怎样，在我打开X.com（前身为Twitter）后，我看到几个人展示了他们试图让o1模型失败的尝试。这是我看到的一个有趣的例子（来自@creeor的[this tweet](https://x.com/creeoer/status/1834588136749388035)），在这个例子中，模型未能回答一个非常简单的问题，而答案就在问题本身。于是我在我的账户上尝试了完全相同的提示，但它给出了错误的答案（见图7）。

![](../Images/a308a9ce3906f01eddd5001ca4a82df6.png)

图7：即使在调整谜语后，OpenAI o1仍然无法解答简单的谜题。显示出模型仍然依赖于它在训练过程中记住的很多内容，并没有充分发挥其推理能力。

当我问它它在谈论的这个经典谜语是什么时，它告诉我一个它从互联网记住的谜语。很有趣的是，看到这些模型有时会依赖于记忆的内容，而不是通过真正的推理来解决问题。尽管在[基准测试](https://cdn.openai.com/o1-system-card.pdf)上有了显著进展和改进，但AI模型在某些领域仍然存在困难，特别是在那些需要深入推理或以细致的方式理解上下文的任务中。尽管基准测试可以显示进展，实际应用往往暴露了其局限性。正是通过持续的测试、反馈和实际应用案例，这些模型才能不断得到完善。

![](../Images/9b25f1703d94524a7f99069f24231a57.png)

图8：o1盲目地从它记住的谜语中回答问题。它没有阅读给定的问题，并尝试按所呈现的方式作答。

大约一年半前曾有一份关于[ChatGPT错误汇编](https://medium.com/@aliborji/a-categorical-archive-of-chatgpt-failures-2c888805d3c3)。模型错误的汇编对于理解和改进AI系统非常宝贵。我相信人们很快会推出一份关于o1模型的错误汇编。

尽管我完全同意链式思维过程有利于AI和人类的学习，但真正的学习确实来自于经验和犯错误。

我将继续在我的Medium页面上发布关于o1模型的发现。关注我的账户以保持更新。感谢你抽时间阅读我的Medium文章。

# 参考文献：

[1] Arkoudas, Konstantine。“GPT-4无法推理。” *arXiv预印本 arXiv:2308.03762*（2023年）。

[2] Aghzal, Mohamed, Erion Plaku 和 Ziyu Yao。“往前看：测试GPT-4在路径规划中的极限。” *arXiv预印本 arXiv:2406.12000*（2024年）。

[3] Kambhampati, Subbarao 等人。“LLMs不能规划，但可以在LLM-Modulo框架中帮助规划。” *arXiv预印本 arXiv:2402.01817*（2024年）。

[4] Wei, Jason 等人。“链式思维提示在大型语言模型中引发推理。” *神经信息处理系统进展* 35（2022年）：24824–24837。
