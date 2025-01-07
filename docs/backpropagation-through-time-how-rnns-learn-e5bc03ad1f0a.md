# 时间反向传播 — RNN 如何学习

> 原文：[https://towardsdatascience.com/backpropagation-through-time-how-rnns-learn-e5bc03ad1f0a?source=collection_archive---------4-----------------------#2024-05-17](https://towardsdatascience.com/backpropagation-through-time-how-rnns-learn-e5bc03ad1f0a?source=collection_archive---------4-----------------------#2024-05-17)

## 时间反向传播算法的解释

[](https://medium.com/@egorhowell?source=post_page---byline--e5bc03ad1f0a--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--e5bc03ad1f0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e5bc03ad1f0a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e5bc03ad1f0a--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--e5bc03ad1f0a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e5bc03ad1f0a--------------------------------) ·阅读时间：9 分钟·2024年5月17日

--

![](../Images/ee340856a1b151a2630e3e799c8e9f7e.png)

“[https://www.flaticon.com/free-icons/neural-network](https://www.flaticon.com/free-icons/neural-network)" title=”neural network icons”>神经网络图标由 pojok d 创建 — Flaticon。

递归神经网络（RNNs）是常规前馈神经网络的变体，能够处理基于序列的数据，如时间序列和自然语言。

它们通过添加一个“递归”神经元来实现这一点，该神经元允许信息从过去的输入和输出传递到下一步。下图展示了一个传统的 RNN：

![](../Images/91392ef12cfd2972ccec9a5d9c622d1f.png)

RNN 的示例架构。图示由作者提供。

左侧是一个递归神经元，右侧是通过时间*展开的递归神经元*。注意如何将之前的执行结果传递给后续的计算。

这为系统增加了一些固有的“记忆”，帮助模型捕捉历史上发生的模式。

在预测***Y_1***时，递归神经元使用***X_1***的输入和上一个时间步的输出***Y_0***。这意味着***Y_0'***对***Y_1***的影响是直接的，并且它也间接影响***Y_2***。

如果你想要完整了解 RNN 及一些实际示例，查看我之前的文章。
