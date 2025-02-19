# 与宇宙的沟通

> 原文：[`towardsdatascience.com/calculating-contact-can-alien-civilizations-communicate-part-3-of-the-drake-equation-series-d339e1f6558b?source=collection_archive---------4-----------------------#2024-09-08`](https://towardsdatascience.com/calculating-contact-can-alien-civilizations-communicate-part-3-of-the-drake-equation-series-d339e1f6558b?source=collection_archive---------4-----------------------#2024-09-08)

## **估算外星文明（德雷克方程系列第三部分）**

[](https://medium.com/@james.r.gearheart?source=post_page---byline--d339e1f6558b--------------------------------)![James Gearheart](https://medium.com/@james.r.gearheart?source=post_page---byline--d339e1f6558b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d339e1f6558b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d339e1f6558b--------------------------------) [James Gearheart](https://medium.com/@james.r.gearheart?source=post_page---byline--d339e1f6558b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d339e1f6558b--------------------------------) ·13 分钟阅读·2024 年 9 月 8 日

--

欢迎回来！在**第一部分**中，我们首先估算了银河系中有多少颗恒星可能拥有行星。在**第二部分**中，我们进一步缩小了范围，估算了有多少颗行星可能支持生命，以及其中有多少可能进化出智能文明。现在，在**第三部分**中，我们将进一步推进这些估算，估计有多少智能文明已经发展出通信技术。

但这里有一个大问题：**有多少文明现在可能正在与我们沟通？**

# 德雷克方程快速回顾

在我们深入讨论之前，让我们快速回顾一下到目前为止我们已经涵盖的**德雷克方程**步骤：

**德雷克方程：**

![](img/5fbbdb6c435d02ad45fc743c72a9d959.png)

1.  **R = 银河系中有多少颗恒星？**

1.  **f_p = 有多少颗恒星拥有行星？**

1.  **n_e = 有多少颗行星位于宜居带？**

1.  **f_l = 有多少颗行星发展出了生命？**

1.  **f_i = 有多少颗拥有生命的行星进化出智能文明？**

现在，我们关注的是**步骤 6**：有多少文明发展出了能够跨星际距离进行沟通的技术？

![](img/907e801de7fe717c757d590902835c36.png)

所有图片均由作者使用 Midjourney 制作。

# 步骤 6：发展通信技术的文明的比例（f_c）

我们已经确定智能生命可能存在，但这里有个关键点——仅仅因为一个文明很智能，并不意味着它们拥有（或有愿望）跨越太空进行通信的技术。事实上，许多先进文明可能正在使用我们完全无法探测到的技术。

就我们的目的而言，我们将**通信能力**定义为能够发展出允许文明发送我们当前仪器可以探测到的信号的技术——例如无线电波或激光。当然，某些文明可能使用更为特殊的方法（如引力波、量子纠缠等），但目前我们只关注我们能够实际探测到的信号。

## 为什么我们选择这个范围

我们估计大约**10%到 20%**的智能文明会发展出通信技术，平均值为**15%**。原因是什么呢？发展通信技术涉及多个“硬步骤”，并不是每个文明都会走同一条路。让我们来细分一下：

+   **幸存于灭绝级事件**：就像地球曾经历过大规模灭绝，其他文明也可能面临自然灾害或战争。如果一个文明没有存活下来，就无法进行通信。

+   **发展技术**：即使一个文明变得智能，也不能保证他们会优先发展通信技术。他们可能会保持地方性，甚至可能演化出超出我们探测能力的技术。

+   **以可探测的形式进行通信**：即使他们发展出技术，也必须以我们可以探测到的方式进行通信——比如无线电波。如果他们使用更先进的方式，我们将无法听到他们的信号。

让我们看看模拟中的表现。首先，我们将运行一些 SAS 代码，根据我们为这一步选择的分布生成 100,000 个数值。

## 第 6 步代码：计算拥有通信技术的文明比例

```py
/*Percent of Intelligent Life with Communication Ability*/

data perc_comm_ability(keep=perc_comm_ability);
  do i = 1 to 100000;
    do while (1);
      perc_comm_ability = rand("normal", 0.15, 0.015);
      /* Check if the value is within the desired range */
      if perc_comm_ability >= 0.1 and perc_comm_ability <= 0.2 then leave;
    end;
    output;
  end;
  drop i;

  format perc_comm_ability percent7.4;
run;
```

![](img/0c97f7689d066dc7ba57a51fa6997462.png)![](img/db490765ff7008f5d0d1fd2e095a937b.png)

## 第 6 步输出及说明：发展通信技术的文明

在运行模拟后，我们确定了**发展通信技术的文明平均比例约为 15%**，其值范围为**10%到 20%**。

## 结果分析

+   **每 6 个文明中有 1 个**：大约**每 6 个智能文明中就有 1 个**能发展出跨越太空进行通信的技术。

+   **分布**：结果大多数集中在**15%**左右，介于**10%到 20%**之间的波动。这为我们计算总的通信文明数目提供了坚实的基础。

## 为什么这些结果很重要

德雷克方程中的这一步至关重要。没有通信能力，即使智能文明存在，我们也永远不会知道。结果显示，虽然并非每个智能文明都会发展出通信技术，但相当一部分文明可能会。这增加了我们与其他文明接触的机会。

# 偏离传统德雷克方程的一小步

在这一点上，我们将从传统的德雷克方程中稍微偏离，继续进行文明总数的计算。我们不会在这一步直接将文明的通信持续时间**(L)**纳入方程，而是首先计算一个**原始估算值**，即曾经发展过通信能力的文明总数。

## 为什么会有这种偏离？

这种微调的原因是我们想要区分两个重要的概念：

1.  **曾经存在过的文明总数**：这为我们提供了一个原始估算值，表示在银河系中那些曾达到发展通信能力的文明总数。这个估算尚未考虑到文明可能会随时间的推移而兴衰。

1.  **它们与我们寿命的重叠**：这就是通信寿命概念出现的地方。我们在估算了文明的原始数量之后，稍后会调整这个估算，以了解这些文明中有多少可能与我们处于同一时期并保持活跃和沟通。

通过采取这条偏离路径，我们能更清楚地看到所有可能的通信文明的总潜力，然后**再**缩小范围，看看有多少文明可能与我们当前的时代重叠。这个方法使我们在考虑通信寿命之前，能全面了解银河系中曾经存在的文明总数。

现在，既然已经解释清楚，我们继续进行下一步计算：将所有分布相乘，估算银河系历史上有多少文明发展了通信能力。

# 将分布结果相乘

现在我们已经计算出了发展通信技术的智能文明的比例，接下来是将所有的分布结果相乘。这将为我们提供一个估算，表示在银河系中***曾经***发展过通信技术的外星文明的总数。

这一步骤涉及将到目前为止德雷克方程中每个部分的结果相乘：

+   恒星的总数

+   拥有行星的恒星比例

+   适宜生存带中行星的比例

+   生命发展行星的比例

+   发展出智能生命的行星比例

+   发展通信技术的智能文明比例

![](img/2d6b97c9244666ffb0c6bda7b0a93aaf.png)![](img/53cd3007ba5fbce924599b8fac4b5abf.png)![](img/4c38e523032d328848b8824ce35bf1d8.png)

# 输出及对总通信文明数量的解释

在完成最终计算后，我们估算出银河系历史上**有 96,828 个外星文明**已经发展了通信技术。

## 这些结果意味着什么？

+   **庞大的数量**：总数为**96,828 个文明**的数字极其庞大，但它包括了所有曾经存在并开发出通信技术的文明。这个数字涵盖了数十亿年的银河历史。

+   **银河潜力**：尽管这个数字的庞大令人印象深刻，但真正的问题是这些文明中有多少正在**现在**广播信号。

# 第 7 步：文明在“沟通阶段”停留多久？（L）

我们估算了可能发展出沟通能力的文明数量，但现在我们要问的是：**它们在这个阶段停留多久？** 文明可能会兴衰起落，而它们发送可探测信号的能力可能是短暂的。例如，人类仅在**100 年**左右的时间里开始向太空发送信号，且不确定我们还能持续多久。自然灾害、资源枯竭甚至自我毁灭可能会结束任何文明的沟通阶段。

## 为什么我们与传统的德雷克方程有所不同

在原始的德雷克方程中，文明的寿命是很早就被计算出来的。然而，在我们的方法中，我们首先计算所有已经发展出沟通能力的文明的**粗略估计**。然后，我们引入它们的寿命概念，看看其中有多少可能与我们自己的文明重叠。这帮助我们专注于那些目前仍然活跃的文明。

## 银河通信寿命

一个文明保持通讯状态的时间取决于几个因素：

+   **幸存于灭绝级事件**：像小行星撞击或核战争等自然灾害或人为灾难，可能很容易削减一个文明的沟通能力。

+   **技术发展**：一个文明必须发展出跨越太空传输信号的手段，这可能需要数千年。

+   **维持沟通**：即便文明发展了通信技术，它们也可能在进化过程中放弃这些技术或转而使用无法探测的方法。

## 人类的沟通时代：一个极小的窗口

从视角来看，虽然**智人**已经存在了**20 万年**，但我们仅仅在**100 多年**前才开始能够发送信号。这仅占我们存在时间的极小一部分。更有目的的沟通尝试，比如 1974 年的**阿雷西博信息**，也仅仅开始于**50 年前**。这表明任何文明的沟通阶段可能非常短暂。

## 模拟文明寿命

在我们的分析中，**L 因子**代表了一个文明保持通讯状态的时间长度，即发送可以被其他文明探测到的信号的时间。我们用一种分布模型来表示，从短命文明（例如几百年或几千年）到长期存在的文明之间的过渡。

## 为什么我们选择这个范围

我们有意将**L**设置为较低值——大约**100 年**——但之后会逐渐上升。这个设定的理由很简单：如果一个文明能够度过其技术发展初期的**100 到 500 年**，它更有可能存续更长时间，甚至可能达到**几千年**。

## 运行模拟

基于这些假设，我们运行模拟来估计文明维持通信的时间，考虑到短命文明和长寿文明的情况。

```py
/*Years of Communicative Abilities*/

data lifetime_comm_civ(keep=lifetime_comm_civ);
  skewness = -6; /* Control the left skewness */
  sigma = (log((1 + (skewness ** 2)) ** 0.5)) / skewness; /* Calculate sigma for Lognormal distribution */

  /* Generate random values from a left-skewed Lognormal distribution */
  do i = 1 to 100000;
    u = rand("uniform"); /* Uniform random variable */
    lifetime_comm_civ = 100 + (1000000 - 100) * exp(sigma * rand("lognormal", 0, 1));
    output;
  end;

run;
```

![](img/a39cc04062362aacd5878627f9bed309.png)![](img/0bde67af35c7737f7bc13b2c9723d0f8.png)

# 第 7 步的输出与解释：文明生命周期

在我们对**L 因子**（文明维持通信的时间长度）进行模拟后，结果显示出多样化的生命周期。让我们来拆解一下关键点：

## 关键统计数据

+   **平均生命周期**：680,330 年。

+   **中位生命周期**：739,262 年。

+   **范围**：最短为**100 年**，最长为**996,193 年**。

## 分布洞察

+   **平均值和中位数**：一个可通信文明的平均生命周期为**680,330 年**，但请注意，中位数稍微高一些，达到了**739,262 年**。这表明，尽管有些文明的生命周期较短，但分布更倾向于较长的文明——数十万年。

+   **最小值和最大值**：最短的生命周期为 100 年，代表那些迅速退出通信阶段的文明。**1%分位数**（46,758 年）显示，即使是处于底层的文明，其存在时间在宇宙尺度上依然相当长。在上端，一些文明可能会持续接近**100 万年**。

+   **偏态与上升**：正如预期的那样，分布是左偏的，这意味着大多数文明会迅速达到 10,000 年的标志，并随后延续到更长的时间框架。这一点在**1%分位数（46,758 年）**和**中位数（739,262 年）**之间的急剧增加中尤为明显。

## 这意味着什么？

分布表明，一旦文明克服了早期的难关（例如，10,000 到 50,000 年），它们中的许多能够存活更长时间，从而增加了持续通信的可能性。平均来看，文明可以存续**超过 600,000 年**，这对我们的搜索来说是个好消息——如果文明能够度过其初生期，那么它们很有可能在较长时间内保持通信，这也使得它们的信号有可能与我们文明的短暂通信窗口重叠。

## **从通信生命周期过渡到当前外星文明**

现在我们已经估算出文明在其通讯阶段的寿命，下一个关键问题是：这些文明中有多少***现在***存在？这就是我们与传统德雷克方程不同之处。我们不再只是询问曾经存在过多少文明，而是需要考虑它们的通讯阶段与我们自己的重叠部分。毕竟，只有在我们存在的短暂窗口期内发出信号的文明，我们才有机会探测到它们。

鉴于文明的寿命从几百年到几百万年不等，我们必须考虑到许多文明可能早在我们出现之前就已经兴起和消亡，或者可能在我们消失很久之后才会出现。

**为什么时间很重要：银河系时间框架** 宇宙大约已有 140 亿年历史，而仅仅是银河系就有大约 136 亿年。文明可能在数十亿年前就已经出现并消失。因此，时间重叠如此重要。即使一颗星球上发展出了智慧生命，这个文明也可能在我们存在之前或之后繁荣和消亡，从未与我们交集。

那么，我们如何计算与我们同时存在的文明的概率呢？

我们估计我们文明的年龄大约为 10,000 年。比苏美尔人早 5000 年，这也假设我们文明及其技术进步将长期持续下去，不会遭遇自我毁灭或环境崩溃等中断。

为了找出与我们重叠的文明，我们取总的通讯文明数，并计算出在我们特定时间框架内存在的比例。

## **计算重叠文明的概率的代码**

利用文明在其通讯阶段的寿命估计，我们将时间重叠部分与潜在文明的总数相乘。这个计算帮助我们弄清楚，可能有多少外星文明在与我们相同时期内处于通讯状态。

```py
/* Define the timeframe of our civilization */
%let civilization_timeframe = 10000; /* in years */

/* Calculate the estimated number of alien civilizations that exist at the same time */
data time;
  set drake;
  civilizations_same_time = min(&civilization_timeframe, lifetime_comm_civ) * total_civ / lifetime_comm_civ;
run;
/*Taking the min value in the numerator ensures that we consider only the overlapping period between 
our civilization and the alien civilizations*/
```

# 这里发生了什么？

**定义我们文明的时间框架：**

+   在这段代码中，我们将我们文明的“通讯阶段”设定为 10,000 年。这代表了人类文明可能具备发送和接收跨越太空信号能力的估计时间长度。我们假设人类比苏美尔人早出现 5000 年，这一数字也包括了我们自己的技术寿命。

**计算重叠文明：**

+   `total_civ` 代表了银河系中估计存在的总文明数（这是我们通过乘以德雷克方程所有项计算出来的数字）。

+   `lifetime_comm_civ` 是一个文明通讯阶段的寿命。

我们使用`min()`函数来找到两个值中的较小者。

+   **我们文明的通讯阶段** (`civilization_timeframe`)，或者

+   **外星文明的通讯阶段**（`lifetime_comm_civ`）。

这确保了我们只考虑那些通讯寿命与我们重叠的文明。

+   **为什么要除以** `**lifetime_comm_civ**`**？** 除以通讯寿命有助于标准化结果，确保我们只统计那些通讯窗口与我们重叠的文明。

# 重叠的重要性

文明可以在银河系 140 亿年的历史中兴衰更替。即便存在 96,000 个文明，许多文明可能在数百万年前就活跃过，现在已经不再发送信号。我们关心的只是那些与我们*同时存在*的文明。代码确保我们准确估算这种重叠，通过关注两个文明保持通讯能力的时间长度。只有重叠的寿命才会计入最终的可通讯文明数量。

## **我们一直在为这一刻做准备：现在有多少个文明存在？**

经过模拟后，考虑到恒星数量、宜居行星、生命承载行星以及具有通讯能力的智慧文明的发展，我们得出了一个关键问题：这些文明中有多少***现在***存在，就在这一刻？

请敲响鼓声……

![](img/b60ec804919377c3950874a0875560cb.png)

根据我们的计算，我们估计，平均而言，**目前在我们的银河系中有 2,363 个文明具备通讯能力。**没错——2,363 个文明，每个文明都可能在这一刻向宇宙广播它们的存在！

让我们换个角度来看：这不仅仅是一个数字。这代表了*数千个*可能分布在银河系中的智慧文明，它们可能也在像我们一样，思考着关于我们的事情。想到可能有成千上万的文明，其中一些可能比我们更先进，正在等待被发现，这种想法既令人震惊又令人兴奋。

但在我们过于激动之前，还有一个大问题需要解答……

**它们有多近？**

拥有一个估算的可通讯文明数量仅仅是战斗的一半。下一个挑战是确定它们在哪里。最近的外星文明可能在我们的技术范围内吗？或者它们可能远得连传输信号我们都可能永远无法接收到？在接下来的部分，我们将深入探讨这些文明之间令人难以置信的距离，并计算出我们需要走多远才能找到我们的宇宙邻居。

敬请关注第四部分，我们将探索将我们与潜在的改变生命的接触隔开的银河距离！

本系列的下一篇：[银河系的距离：外星文明有多远？（德雷克方程系列第四部分）](https://medium.com/@james.r.gearheart/how-far-are-we-from-alien-civilizations-part-4-of-the-drake-equation-series-2299ee009476)。或者，如果你错过了上一篇，可以返回[这里](https://medium.com/@james.r.gearheart/calculating-contact-moving-from-stars-to-life-part-2-of-the-drake-equation-series-c110c018f174)。

除非另有说明，所有图片均由作者提供  
