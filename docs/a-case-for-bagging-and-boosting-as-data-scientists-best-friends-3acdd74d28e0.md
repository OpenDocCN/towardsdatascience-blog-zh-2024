# 《论集成方法——数据科学家的最佳朋友》

> 原文：[`towardsdatascience.com/a-case-for-bagging-and-boosting-as-data-scientists-best-friends-3acdd74d28e0?source=collection_archive---------8-----------------------#2024-12-17`](https://towardsdatascience.com/a-case-for-bagging-and-boosting-as-data-scientists-best-friends-3acdd74d28e0?source=collection_archive---------8-----------------------#2024-12-17)

## 在机器学习模型中利用群体智慧。

[](https://medium.com/@fmnobar?source=post_page---byline--3acdd74d28e0--------------------------------)![Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--3acdd74d28e0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3acdd74d28e0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3acdd74d28e0--------------------------------) [Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--3acdd74d28e0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3acdd74d28e0--------------------------------) ·14 分钟阅读·2024 年 12 月 17 日

--

![](img/98e546113b8fb841ce7edcd7ac141df4.png)

图片来源：[Luca Upper](https://unsplash.com/@lucaupper?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/balloon-on-sky-Z-4kOr93RCI?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

近年来，我们理所当然地使用维基百科或 Reddit 等资源——这些资源依赖于个体贡献者的集体知识，为我们提供*大多*准确的信息，这种现象有时被称为“群体智慧”。其思想是，集体决策可能比任何个人的判断更为准确，因为我们每个人可能有自己的隐性偏见和/或知识缺乏，从而导致判断中的一定误差。集体的这些误差可能会相互抵消——例如，我们可以弥补某人在某一领域知识/专业技能的不足，而他们则在其他领域弥补我们的不足。将这一思想应用到机器学习中就形成了“集成”方法。

从一个非常高的层次来看，我们训练机器学习模型是为了对未来进行预测。换句话说，我们为模型提供训练数据，希望模型能够对未来做出准确的预测。但如果我们能训练多个机器学习模型，然后以某种方式汇总它们对于预测的看法会怎样呢？事实证明，这是一种非常有用的方法，并且在行业中被广泛应用。
