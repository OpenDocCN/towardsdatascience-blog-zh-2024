# 精通 Git：高效版本控制的三种关键工作流

> 原文：[https://towardsdatascience.com/mastering-git-the-3-essential-workflows-for-efficient-version-controlling-9bffe1883bd1?source=collection_archive---------2-----------------------#2024-02-03](https://towardsdatascience.com/mastering-git-the-3-essential-workflows-for-efficient-version-controlling-9bffe1883bd1?source=collection_archive---------2-----------------------#2024-02-03)

## 为优雅的开发旅程提供 Git 工作流

[](https://medium.com/@Chim-SO?source=post_page---byline--9bffe1883bd1--------------------------------)[![Chayma Zatout](../Images/341c45f53ddf73dc0851d547cc7cb55a.png)](https://medium.com/@Chim-SO?source=post_page---byline--9bffe1883bd1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9bffe1883bd1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9bffe1883bd1--------------------------------) [Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--9bffe1883bd1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9bffe1883bd1--------------------------------) · 8 分钟阅读 · 2024年2月3日

--

![](../Images/5839fdae41dd84f47850125568297018.png)

图片来自 [Prateek Katyal](https://unsplash.com/@prateekkatyal?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你希望高效且优雅地使用 Git，那么你找对地方了！在阅读并在你的项目中应用了这些工作流后，我向你保证，你的项目将会提升到一个新的水平。采用 Git 工作流对我来说不仅是一种好习惯，更是必须的！即使你不与他人合作，你也可以应用它，我自从发现其优点后便一直如此。刚开始可能会觉得难以适应，但通过实践，你会接受它，并开始喜欢上它。那么，让我们开始探索这三种最重要的工作流吧。

还不是 Medium 会员？不用担心！通过这个 [*朋友链接*](/mastering-git-the-3-essential-workflows-for-efficient-version-controlling-9bffe1883bd1?sk=c1743a0edcfe87fff863c267d3a8260c) 继续阅读。

**目录：**

· [1\. 介绍](#4e94)

· [2\. 集中式工作流](#8970)

· [3\. 特性分支工作流](#6cf6)

· [4\. 分叉工作流](#580d)

· [5\. Gitflow 工作流](#6e44)

· [6\. 分支命名规范](#91ce)

· [7\. 结论](#f185)

# 1\. 介绍

嗯，当我还是一个初学者，做简单的小项目时，我只是用 Git 来保存我的项目和上传…
