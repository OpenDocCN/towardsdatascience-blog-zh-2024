# 闪存注意力（快速且内存高效的精确注意力与 I/O 感知）：深入探讨

> 原文：[https://towardsdatascience.com/flash-attention-fast-and-memory-efficient-exact-attention-with-io-awareness-a-deep-dive-724af489997b?source=collection_archive---------3-----------------------#2024-05-29](https://towardsdatascience.com/flash-attention-fast-and-memory-efficient-exact-attention-with-io-awareness-a-deep-dive-724af489997b?source=collection_archive---------3-----------------------#2024-05-29)

## 闪存注意力是一种优化能耗的变换器注意力机制，提供了 15% 的效率提升

[](https://medium.com/@anishdubey?source=post_page---byline--724af489997b--------------------------------)[![Anish Dubey](../Images/f85f17fb79718c819b4bd1c9a16338a7.png)](https://medium.com/@anishdubey?source=post_page---byline--724af489997b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--724af489997b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--724af489997b--------------------------------) [Anish Dubey](https://medium.com/@anishdubey?source=post_page---byline--724af489997b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--724af489997b--------------------------------) ·阅读时间：7 分钟·2024 年 5 月 29 日

--

![](../Images/bcd08ecda3389e4ce550e4a823d5c333.png)

图片由 [sander traa](https://unsplash.com/@sandertraa?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/a-field-with-trees-and-mountains-in-the-background-KV2giR3tbX4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

闪存注意力是一种优化能耗的变换器注意力机制，在墙钟速度上提供了 15% 的效率提升，且没有任何近似计算。

# 背景

鉴于变换器模型在长序列上的速度较慢且内存消耗大（时间和内存复杂度本质上是二次的），闪存注意力（[论文](https://arxiv.org/pdf/2205.14135)）在 BERT-large 上提供了 15% 的端到端墙钟加速，在 GPT-2 上提供了 3 倍的速度提升。

考虑到训练这些大型模型所消耗的巨大能量，结合软件和硬件优化的闪存注意力能够提供 15% 的效率提升，这是一个巨大的进步。

以下讨论有助于解释闪存注意力的一些基本概念，以及它是如何实现的。

## 计算与内存的基本概念

在我们深入讨论计算与内存之前，让我们先回顾一下它们：

什么是计算？

+   在 GPU 上进行实际浮点运算（FLOPS）所花费的时间

什么是内存？

+   在 GPU 内部传输张量所花费的时间

理想情况下，我们希望 gCPU 始终执行矩阵乘法，而不受内存的限制。但实际上，计算进展比内存更快，我们处在一个 gCPU 静待数据加载的世界。这通常被称为 **内存瓶颈** 操作。请参见下面的示意图以说明这一点。矩阵乘法被认为是计算，而内存则负责存储数据（可以将其视为仓库）。计算需要数据来处理，内存带宽必须支持这一操作。

![](../Images/fcc27ebfc81919bdb9d016a9290180ed.png)

图片来自 [https://horace.io/brrr_intro.html](https://horace.io/brrr_intro.html)

## 什么是内存层次结构？

A100 GPU 拥有 **40–80GB** 的高带宽内存，带宽为 **1.5–2.0 TB/s**，并且每个 108 个流式多处理器有 **192KB** 的片上 SRAM，带宽估计约为 **19TB/s**。

![](../Images/9b566bc7147d9ac2cc580a884d4259fc.png)

图片来自 [https://arxiv.org/abs/2205.14135](https://arxiv.org/abs/2205.14135)

# 自注意力架构的问题是什么？

在上述背景下，自注意力架构是 **内存瓶颈**。

![](../Images/ad69afb8ec7fde9260e39d68b245318e.png)

图片由作者提供

查看注意力数学，它是一个 softmax 操作，导致了内存瓶颈。

+   定量证据：如下所示，与矩阵乘法（Matmul）相比，像 softmax、dropout、masking 等操作占用了大部分时间。

![](../Images/383bf678304f14ad24715ef6a2c2d324.png)

图片来自 [https://arxiv.org/abs/2205.14135](https://arxiv.org/abs/2205.14135)

## 为什么 softmax 成为内存瓶颈操作？

它操作的规模是我们最大的瓶颈。在下面的图中

+   N -> 令牌的数量

+   d -> 嵌入维度的数量

+   当 Query 和 Key’ 相乘时，注意力矩阵会爆炸到 N * N，这需要大量内存。作为参考（d ~128；N ~128k 令牌；谷歌 Gemini: ~100 万令牌）

![](../Images/14c37c8a0960f8b4efef2858021b8a24.png)

图片来自 [FlashAttention — Tri Dao | Stanford MLSys #67](https://www.youtube.com/watch?v=gMOAud7hZg4&ab_channel=StanfordMLSysSeminars)

# [算法] 自注意力是如何实现的？

以下是实现自注意力机制的算法

![](../Images/8a4ac1211ce636bae0007eb2f0462a8c.png)

图片来自 [https://arxiv.org/abs/2205.14135](https://arxiv.org/abs/2205.14135)

如上节所述，将信息传输到 HBM（将 S 写入 HBM），然后从 HBM 加载回 gCPU 计算 softmax，再写回 HBM，涉及大量信息传输，导致它成为 **内存瓶颈操作**。

# [矩阵乘法] 自注意力是如何实现的？

配合图示，下面的步骤有助于解释自注意力是如何通过矩阵乘法来计算的

## 步骤 1:

+   我已经简化了这个过程。在实际应用中，每个标记都会加上位置编码以生成嵌入，然后将其输入到一个线性层中生成 <key, query 和 value>。为说明起见，我使用了 3 的维度（通常范围为 64 到 128）。这是标准的 Transformer 架构输入。

## 第 2 步

+   Key -> Key'（转置）被计算出来，并与 Query 相乘得到 QK'，其结果是 N*N。这包含了每个标记与其余标记之间的注意力。下图也展示了这种关系。由于这些是标记，我们需要计算每个标记与其他标记之间的重要性，因此将对每一行应用 softmax 操作，以将其归一化到 0-1 范围内。

+   这一步 **需要移动到 HBM，并且是最昂贵的操作**，正如我们所讨论的那样。整篇 Flash Attention 论文的核心就是如何优化这个过程。

## 第 3 步

+   Softmax(QK') * V 被计算为最终的输出矩阵。这里的维度与 Key、Query 和 Value 的输入嵌入相同。

+   输出矩阵中的最后一行

+   1*5 的意思是，“this”的嵌入应该被修改，以融入与其他标记的关系。

+   2*5 的意思是，“is”的嵌入应该被修改，以融入与其他标记的关系。

+   对其余的其他行同样操作

![](../Images/eaefc012968e0a6a8cfdc38a808b28e2.png)

作者提供的照片：自注意力机制工作原理的示意图

# Flash Attention 论文背后的基本思想

基本思想通过下面的图示得以解释，其中 key、query 和 value 的块从 HBM 传输到 SRAM，并通过一些数学技巧（在下文中解释），这里完成的计算不是近似值，而是实际的正确答案。

通过此实现，论文能够通过访问块中的信息来减少壁钟时间，而不会牺牲正确性。

![](../Images/9d12c7f05ad017b1df7db1f66667f067.png)

来自 [https://arxiv.org/abs/2205.14135](https://arxiv.org/abs/2205.14135) 的照片

## 论文背后的算法：Flash Attention 是如何实现的？

这是论文中最复杂的部分。让我们将这个问题分解为子方面并深入探讨。

下图将矩阵分解为块，展示了每个块如何用于计算部分 softmax，然后计算出正确的 softmax。

+   初始输入：Token：这是 Flash Attention 论文

+   Key：4（标记）X 3（维度），Query：4（标记）X 3（维度）和 Value：4（标记）X 3（维度）

![](../Images/bb787277f6cd09c9f7741daa795bc448.png)

图片由作者修改。原图来自 [https://arxiv.org/abs/2205.14135](https://arxiv.org/abs/2205.14135)

第 0 步

+   假设内存为 24 字节

+   SRAM 将被划分为 4 个块（Query、Key、Value 和输出矩阵）

+   Query、Key、Value 和 Output 将各自占用 6 字节存储其信息（12 字节/4）

+   每个维度为 3，因为每个嵌入不能被拆分，因此

+   Query: 6 字节 / 3（维度） = 2\。Value、Key 和 Output 同理

+   因此，[M/4d] 给出了每个块的大小。在这种情况下，块的大小是 2\。这意味着可以将 2 行数据提取到 SRAM 中。

+   一般来说，块大小是 [M/4d]，块的数量是 [N*4D/M]。

第一步和第二步：下面添加了一个表格，说明了第一步和第二步，展示了 flash attention 如何工作，并比较了其内存和计算方面的差异。

![](../Images/1f3283d51b3061c764359ef361e8e74a.png)

作者提供的照片：一步步拆解 flash attention 中的内存和计算使用。

下面的图表帮助可视化 flash attention 中逐块使用的矩阵乘法。

![](../Images/e22bba7a834d0f89407473bbae1181ef.png)

作者提供的照片：展示了 flash attention 机制如何工作的示意图。

## softmax 的数学方面是什么？

论文中一个最关键的方面是如何通过分解矩阵仍然能够计算 softmax 准确度。下面给出了一个数学示例，展示了如何将两个不同的矩阵合并来重新计算 softmax。

直觉

+   这就是指数的美丽性质，在这里得到了应用。

+   每个 softmax 都是单独计算的，但同时存储了该行的最大值和累加的指数值。

+   当与另一个矩阵合并时，我们需要检查最大值与两个矩阵的全局最大值的差异。由于指数的存在，分子和分母都通过 e^(current_max — global_max) 来调整，从而考虑到这一点。

逻辑相当复杂，因此下面提供了一个示例供大家学习。熟悉该示例后，上述直觉将变得非常有意义。

![](../Images/fa60177ed659c487bcb14b653e1919c6.png)

作者提供的照片：示例演示了如何将矩阵分解为子组件，最终将它们组合起来计算 softmax。

# 复杂度分析

让我们看看复杂度分析，了解事情如何发生变化。

自注意力

+   在计算 S = QK' 时，它变成了一个 N*N 的矩阵，需要传播回 HRAM 然后再从 HRAM 中拉取回来。

+   因此 O(N*N + N*N) = O(N*N) 就是 HBM 访问。

Flash attention

+   外部循环：Key 和 Query 将被访问 O(Nd) 次。

+   内部循环：只需要 O(Nd/M) 来从 HBM 中加载数据，因为是在操作块。

+   总体复杂度：O(N*N*d*d/M)

+   实际上，d 要远小于 M。d 的范围是 (64–128)，而 M 的范围从 100 KB 到 M，因此 HBM 访问得到了优化。

# 结论

+   我们的目标是优化 HBM 访问，通过这次复杂度分析，我们看到论文通过 (d*d/M) 因子优化了**HBM 访问，且没有做任何近似**。

这是一个复杂的论文，带来了巨大的效率提升。希望上述解释能为大家提供一些直觉，帮助理解 flash attention 如何优化并提高性能。我没有涉及块稀疏的 flash attention，也没有对比其他优化技术、前向传递优化等内容。希望在未来的文章中能够覆盖这些内容。

## 参考文献

+   论文：[FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness](https://arxiv.org/abs/2205.14135)

+   Tri Dao 的讲座: [FlashAttention — Tri Dao | Stanford MLSys #67](https://www.youtube.com/watch?v=gMOAud7hZg4&ab_channel=StanfordMLSysSeminars)

+   Medium 文章: [https://gordicaleksa.medium.com/eli5-flash-attention-5c44017022ad](https://gordicaleksa.medium.com/eli5-flash-attention-5c44017022ad)
