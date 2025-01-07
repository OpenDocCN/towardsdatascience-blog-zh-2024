# 🚪🚪🐐 从蒙提霍尔问题中学习决策技巧

> 原文：[`towardsdatascience.com/lessons-in-decision-making-from-the-monty-hall-problem-a6032f4b1032?source=collection_archive---------1-----------------------#2024-10-24`](https://towardsdatascience.com/lessons-in-decision-making-from-the-monty-hall-problem-a6032f4b1032?source=collection_archive---------1-----------------------#2024-10-24)

## 三种直觉的探索：常识、贝叶斯和因果

[](https://eyal-kazin.medium.com/?source=post_page---byline--a6032f4b1032--------------------------------)![Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--a6032f4b1032--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6032f4b1032--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6032f4b1032--------------------------------) [Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--a6032f4b1032--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6032f4b1032--------------------------------) ·25 分钟阅读·2024 年 10 月 24 日

--

![](img/a3d45650ff7d47314680564588529126.png)

通过 Gemini Imagen 3 生成

蒙提霍尔问题是一个著名的脑筋急转弯，我们可以从中学习到重要的决策技巧，这些技巧对于数据科学家尤其有用。

如果你不熟悉这个问题，准备好感到困惑吧🤯。如果你已经知道这个问题，希望我能为你照亮一些你可能未曾考虑到的方面💡。

我介绍了这个问题，并通过三种类型的直觉来解决它：

+   **常识** — 本文的核心关注的是如何利用我们的*常识*来解决这个问题。我们将探索它为何失败 😕 ，以及我们如何直观地克服这个问题，让解决方案变得清晰明了 🤓。我们将通过使用视觉图示 🎨、定性论证和一些基本概率（保证不太深奥）来实现这一点。

+   **贝叶斯** — 我们将简要讨论信念传播的重要性。

+   **因果** — 我们将使用图模型来可视化在实际场景中使用蒙提霍尔问题所需的条件。

    🚨剧透警告 🚨 我还没有被说服认为有什么重要的结论，但这种思维过程非常有用。

我通过讨论更好的数据决策技巧总结了所学的经验。
