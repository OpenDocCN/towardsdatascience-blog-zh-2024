# 时间差分学习：将动态规划与蒙特卡罗方法结合用于强化学习

> 原文：[`towardsdatascience.com/temporal-difference-learning-combining-dynamic-programming-and-monte-carlo-methods-for-e0c2f0829a51?source=collection_archive---------10-----------------------#2024-10-17`](https://towardsdatascience.com/temporal-difference-learning-combining-dynamic-programming-and-monte-carlo-methods-for-e0c2f0829a51?source=collection_archive---------10-----------------------#2024-10-17)

## 强化学习的里程碑：Q 学习与双重 Q 学习

[](https://medium.com/@hrmnmichaels?source=post_page---byline--e0c2f0829a51--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e0c2f0829a51--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e0c2f0829a51--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e0c2f0829a51--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e0c2f0829a51--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e0c2f0829a51--------------------------------) ·15 分钟阅读·2024 年 10 月 17 日

--

我们继续深入研究 Sutton 的书《强化学习：导论》[1]，在这篇文章中介绍时间差分（TD）学习，这是该书的第六章内容。

TD 学习可以看作是动态规划（DP）与蒙特卡罗（MC）方法的结合，我们在前两篇文章中介绍了这两种方法，并且标志着强化学习（RL）领域的一个重要里程碑——结合了上述方法的优点：TD 学习不需要模型，仅从经验中学习，类似于 MC，但也具有“自举”——使用先前的估计值，类似于 DP。

![](img/18ffe8d06e7bcab87fb8b4c749756123.png)

图片来源：[Brooke Campbell](https://unsplash.com/@bcampbell?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在[Unsplash](https://unsplash.com/photos/brass-colored-analog-clock-Rw2-Y0nSIKQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在这里，我们将介绍这类方法的理论基础，并展示相关的实用算法，如 Q 学习——并附上 Python 代码。与往常一样，所有代码都可以在[GitHub](https://github.com/hermanmichaels/rl_book)上找到。

我们首先介绍背景和动机，然后从预测问题开始——与之前的帖子类似。接着，我们深入探讨理论，并讨论 TD 学习找到的解决方案。随后，我们转向控制问题，并提出…
