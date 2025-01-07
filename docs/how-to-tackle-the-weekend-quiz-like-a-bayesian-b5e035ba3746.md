# 如何像贝叶斯一样应对周末测验

> 原文：[https://towardsdatascience.com/how-to-tackle-the-weekend-quiz-like-a-bayesian-b5e035ba3746?source=collection_archive---------5-----------------------#2024-10-28](https://towardsdatascience.com/how-to-tackle-the-weekend-quiz-like-a-bayesian-b5e035ba3746?source=collection_archive---------5-----------------------#2024-10-28)

## 你知道哪一个是malmsey吗？你能做出一个好的猜测吗？

[](https://medium.com/@juntaah?source=post_page---byline--b5e035ba3746--------------------------------)[![Junta Sekimori](../Images/7c8c1a9c3e7fccea11214452548caa79.png)](https://medium.com/@juntaah?source=post_page---byline--b5e035ba3746--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b5e035ba3746--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b5e035ba3746--------------------------------) [Junta Sekimori](https://medium.com/@juntaah?source=post_page---byline--b5e035ba3746--------------------------------)

·发表于[《Towards Data Science》](https://towardsdatascience.com/?source=post_page---byline--b5e035ba3746--------------------------------) ·9分钟阅读·2024年10月28日

--

几周前，这个问题出现在[《悉尼晨锋报周末测验》](https://www.instagram.com/goodweekendquiz/p/DAt26iSPKeE)中：

> malmsey是什么：轻微宿醉、女巫的诅咒，还是加烈酒？

假设我们对答案毫无头绪，在这种情况下有什么办法可以做出明智的猜测吗？我认为是有的。

在继续阅读之前，欢迎您先思考一下。

![](../Images/fbcdaf14ae352f0d6620a2ca5e4b6610.png)

一位因饮用加烈酒而稍感宿醉的女巫，使用Gemini Imagen 3创建

# 我们真的没有任何可以带到这个问题中的线索吗？

看着这个词，*感觉*它可能代表这些选项中的任何一个。当然，这个多项选择题的设计就是为了让人产生这种感觉。

但我们可以采取一种理性的方法，那就是认识到这些选项有不同的*基准概率*。也就是说，暂时不讨论什么是和不是malmsey，我们可以感觉到，宿醉的名称可能没有女巫的诅咒那么多，而且对于各种加烈酒的名称肯定更多。

为了进一步量化这个问题：

+   轻微宿醉的词汇大概有多少个？**也许只有1个**？

+   女巫的诅咒大概有多少个词呢？我不是专家，但我已经能想到一些同义词，**也许有10个**？

+   加烈酒的词汇大概有多少个？同样，我不是专家，但我能说出几个（波特酒，雪莉酒……），而且可能还有更多，所以**也许有100个**？

因此，在没有其他线索来指引正确答案的情况下，加烈酒将是一个经过充分推理的猜测。根据我上面的估算，加烈酒的正确概率是轻微宿醉的 100 倍，是女巫诅咒的 10 倍。

即使我对这些数量有误差，我至少对这些基准概率的顺序有信心，因此我会将加烈酒作为我的最佳猜测。

[宾果](https://www.madeirawineanddine.com/malmsey/)！

# 基准率忽视

这个推理看似简单，但在做类似判断时忽视基准率是 Kahneman、Tversky 以及许多其他人提到的重大认知偏误之一。一旦我们察觉到这一点，便能到处都看到它。

考虑一下 Rolf Dobelli 在 [*The Art of Thinking Clearly*](https://www.amazon.com.au/Art-Thinking-Clearly-Rolf-Dobelli/dp/0062219693) 中提到的以下智力游戏：

> Mark 是一个来自德国、戴眼镜的瘦男人，喜欢听莫扎特的音乐。哪个更可能？Mark 是 A) 一名卡车司机，还是 B) 法兰克福的一名文学教授？

诱惑是根据我们与描述相联系的刻板印象选择 B，但更合理的猜测应该是 A，因为德国有比法兰克福的文学教授更多的卡车司机。

这个难题是对 Kahneman 和 Tversky 的图书管理员-农民人物描绘的改编（参见 [*Judgment under Uncertainty*](https://www2.psych.ubc.ca/~schaller/Psyc590Readings/TverskyKahneman1974.pdf)）*，它也为伟大的 [3B1B 对贝叶斯定理的解释](https://www.youtube.com/watch?v=HZGCoVF3YvM) 提供了框架，在这个视频中，这种思维过程与贝叶斯公式的条件概率和边际概率（基准概率）对应。

# 识别思维陷阱

贝叶斯框架帮助我们更清楚地看到概率推理中的两个常见陷阱。用 Kahneman 和 Tversky 的语言来说，我们可以说它为系统二（“慢速”）思维提供了一种工具，以克服我们冲动且易犯错误的系统一（“快速”）思维。

第一个洞察是，条件概率 p(A|B) 并不等同于其反向概率 p(B|A)，尽管在日常生活中，我们常常会误以为它们是相同的。

在 Dobelli 的例子中，这就是以下的区别：

+   P(👓|🧑‍🏫) — 在已知 Mark 是法兰克福的文学教授的情况下，Mark 是一位来自德国、戴眼镜、喜欢听莫扎特的瘦男人的概率。

+   P(🧑‍🏫|👓) — 在已知 Mark 是一位来自德国、戴眼镜、喜欢听莫扎特的瘦男人的情况下，Mark 是法兰克福的文学教授的概率。

如果相信刻板印象，P(👓|🧑‍🏫) 看起来相当可能，而 p(🧑‍🏫|👓) 不太可能，因为我们会预期在德国有许多人符合相同的描述，但并不是文学教授。

第二个启示是，这两个条件概率是相关的，因此知道一个可以引导我们得到另一个。我们需要做的是连接这两个条件的 A 和 B 的个体基准率，比例因子实际上是这两个基准率的简单比率，如下所示：

![](../Images/9082f781092ee335e83b383083f774c3.png)

图片由作者创建

这是贝叶斯公式。

# 贝叶斯推理——逐步进行

那么这如何帮助我们呢？

除了教科书和示例中的问题，我们通常不会期望有所有数字可以直接代入贝叶斯公式，但它依然提供了一个有用的框架，用于组织我们的已知和未知并形式化一个有根据的猜测。

例如，在 Dobelli 情境中，我们可能从以下估算值开始：

+   戴眼镜且符合描述的教授的百分比：**25%**（每4人中有1人）

+   在法兰克福的德国文学教授所占的百分比：**0.0002%**（每50万人中有1人）

+   戴眼镜且符合描述的卡车司机的百分比：**0.2%**（每500人中有1人）

+   德国卡车司机所占的百分比：**0.1%**（每1000人中有1人）

+   穿眼镜且符合描述的一般人群百分比：**0.2%**（每500人中有1人）

+   德国人口：~**8500万**

所有这些参数都是基于我个人世界观的估算。只有德国人口是我可以查到的一个数据点，但这些估算有助于我理性地推理关于 Dobelli 问题。

下一步是将这些框架化为列联表，展示每个事件发生的相对频率，无论是同时发生还是单独发生。通过从总人口开始并应用我们的百分比估算，我们可以开始填写法兰克福教授和卡车司机的两张表格，每个符合描述（对于这一部分，您也可以跟随[这个电子表格](https://docs.google.com/spreadsheets/d/1DvPxzEFOeOEaKUc718MaqPCmS8PshAwZkRjPmYDNl-s)）：

![](../Images/7c6b253e9780c1a3d8284c785ac24afb.png)

图片和资源由作者创建——请见[这里](https://docs.google.com/spreadsheets/d/1aVMJUhzSCdj1s0IOWeO9-gVQ5NA84AV3RBwIrJr5msY)查看原始文档

四个白色框代表两种事件可能发生的四种方式：

+   A 和 B

+   A 但不是 B

+   B 但不 A

+   既不是 A 也不是 B

灰色阴影部分代表每个事件的总频率，不考虑重叠部分，这只是行和列的总和。基准率来源于这些边际频率，这也是为什么它们通常被称为*边际概率*。

接下来，我们可以像填数独一样填写空白，通过确保所有行和列的总和一致：

![](../Images/8166d201a8dd7d720dc62a672ec74e45.png)

图片和资源由作者创建——请见[这里](https://docs.google.com/spreadsheets/d/1aVMJUhzSCdj1s0IOWeO9-gVQ5NA84AV3RBwIrJr5msY)查看原始文档

现在，在我们的列联表完成后，我们有了关于基准率的估计以及个人资料与描述相匹配的可能性。贝叶斯公式中的所有条件概率和边际概率现在都可以在这里表示，并可以按以下方式计算：

![](../Images/9963f810b06d9a51b6ae50292ad1beb8.png)

作者创作的图像和资源 - 请见[这里](https://docs.google.com/spreadsheets/d/1aVMJUhzSCdj1s0IOWeO9-gVQ5NA84AV3RBwIrJr5msY)以查看原始文档

回到最初的问题，我们感兴趣的概率是上面列表中的第三个：给定描述，他们是教授/卡车司机的概率。

并且，基于我们的参数估计，我们看到卡车司机比教授更有可能符合要求，概率是4倍（0.001 / 0.00025）。与此相对的是反向条件概率，即描述更可能符合教授，而不是卡车司机，比例为125倍（0.25 / 0.002）！

# 回到马姆赛（Malmsey）

现在，回到我们从马姆赛（malmsey）例子开始的地方，希望直觉已经逐渐形成，并且基准率在做出猜测时的作用已经清晰。

在将思维与贝叶斯公式对照时，本质上，思维过程将是比较我们对以下三种情况的信念程度：

+   概率（A 答案是 *轻微宿醉* | B 词语是 *马姆赛*）

+   概率（A 答案是 *女巫的诅咒* | B 词语是 *马姆赛*）

+   概率（A 答案是 *加强型葡萄酒* | B 词语是 *马姆赛*）

因为在这种情况下我们完全不清楚“马姆赛”可能对应什么（如果我们有一些词源学上的怀疑，情况就会不同），我们可以说B是 *无信息* 的，因此要做出任何合理的猜测，我们只能依赖A的概率。在贝叶斯公式中，我们可以看到我们感兴趣的概率是随着A的基准率而变化的：

![](../Images/4fd36e1a4f8032faa5eb1c3452bb2f0d.png)

作者创作的图像

为了完整性，[这里](https://docs.google.com/spreadsheets/d/1DvPxzEFOeOEaKUc718MaqPCmS8PshAwZkRjPmYDNl-s/edit?gid=1825971281#gid=1825971281)是我们如何像Dobelli例子中的列联表那样，列出我们的信念程度。因为B没有提供有效信息，我们给出了50:50的几率，表示“马姆赛”这个词可以与任何其他词或概念匹配。虽然这有些过度，且一旦我们认识到可以简单地将我们的信念与基准率相结合，这种做法并非必要，但它展示了贝叶斯框架在这种更抽象问题中的适用性。

# 基准率忽视在假设（A/B）检验中的应用

我之前[写过关于检察官谬误的话题](/how-to-intuit-the-prosecutors-fallacy-and-run-better-hypothesis-tests-2d8561bfc5a6)（一种基准率忽视的形式），其中给出了更多基准率忽视的例子以及对分析实践者的启示。

在这里再次强调，在传统的A/B测试方法中，人们常常将看到测试结果的概率与假设本身为真的概率混淆。关于p值及其陷阱已经有很多相关文献（例如，[《肮脏的十二个：十二个关于p值的误解》](https://sixsigmadsi.com/wp-content/uploads/2020/10/A-Dirty-Dozen-Twelve-P-Value-Misconceptions.pdf)），但这是另一个地方，贝叶斯思维方式有助于澄清我们的推理，同时也提醒我们注意基准率忽视的概念，在这种情况下，基准率忽视指的是我们一开始对假设为真的信心（我们的*先验*）。

我鼓励你阅读[这篇文章](/how-to-intuit-the-prosecutors-fallacy-and-run-better-hypothesis-tests-2d8561bfc5a6)，以更好地理解这一概念。

# 关键点

+   涉及的概念：基准率忽视、条件概率与边际概率、贝叶斯公式、列联表。

+   小心不要在日常判断概率时将p(A|B)与p(B|A)混淆。

+   在判断新观察是否验证了你的假设时，考虑基准率。

+   今天学到：Malmsey是一种来自马德拉岛的加强酒。在莎士比亚的《理查三世》中，乔治·普兰塔根特（克拉伦斯公爵）死于一桶马尔梅西酒。

# 深入阅读

+   [《清晰思维的艺术》 by Rolf Dobelli](https://www.amazon.com.au/Art-Thinking-Clearly-Rolf-Dobelli/dp/0062219693) 提供了本文提到的教授-卡车司机谜题，并且汇集了许多日常生活中的思维陷阱，既易读又充实。除了关于基准率忽视的章节（第28章），我还喜欢书中对于均值回归（第19章）、指数增长（第34章）、虚假因果关系（第37章）等内容的论述。每个章节简洁明了，约2-3页，整本书非常适合作为常见偏见和谬误的参考手册。

+   [《颠覆性思维》 by Michael Lewis](https://www.amazon.com.au/Undoing-Project-Michael-Lewis/dp/0141983043)（《大空头》的作者）讲述了卡尼曼、特沃斯基和行为经济学的发展的故事。它涵盖了《思考，快与慢》中所有精华部分，是一本让人欲罢不能的书，完全有可能改编成电影。

+   [《统计学的艺术》 by David Spiegelhalter](https://www.amazon.com.au/Learning-Data-Statistics-David-Spiegelhalter/dp/0241258766) 书中有许多易懂的贝叶斯统计学章节。

+   [如何直觉性地理解检察官谬误（以及进行更好的假设检验）](/how-to-intuit-the-prosecutors-fallacy-and-run-better-hypothesis-tests-2d8561bfc5a6) 是我之前写的一篇关于类似话题的文章，当时我在努力理解p值的正确定义。

+   为了理解贝叶斯公式，我推荐观看Stat Quest的两段视频：[1. 条件概率](https://www.youtube.com/watch?v=_IgyaD7vOOA) 和 [2. 贝叶斯定理](https://www.youtube.com/watch?v=9wCnvr7Xw4E)。
