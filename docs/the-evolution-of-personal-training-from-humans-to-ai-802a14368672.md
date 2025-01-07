# 超越个人训练：从人类到AI

> 原文：[https://towardsdatascience.com/the-evolution-of-personal-training-from-humans-to-ai-802a14368672?source=collection_archive---------5-----------------------#2024-04-04](https://towardsdatascience.com/the-evolution-of-personal-training-from-humans-to-ai-802a14368672?source=collection_archive---------5-----------------------#2024-04-04)

## 一段将我的Garmin数据转化为量身定制的健身伴侣的旅程

[](https://medium.com/@andrea.ialenti?source=post_page---byline--802a14368672--------------------------------)[![Andrea Ialenti](../Images/1ff6e8084c4ae1e2ae7d7f69be972fcd.png)](https://medium.com/@andrea.ialenti?source=post_page---byline--802a14368672--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--802a14368672--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--802a14368672--------------------------------) [Andrea Ialenti](https://medium.com/@andrea.ialenti?source=post_page---byline--802a14368672--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--802a14368672--------------------------------) ·阅读时间 24 分钟·2024年4月4日

--

![](../Images/88ac54fb3121ffe442757deb4685151c.png)

图片来源：[Alora Griffiths](https://unsplash.com/@aloragriffiths?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 来自 [Unsplash](https://unsplash.com/photos/woman-carrying-barbell-aVrZMPgN_Vg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

我一直比运动更喜欢电子游戏，而在2000年代初，喜欢电子游戏并不会让你成为一个酷炫的主播或电子竞技选手；那时它只是让你变成了一个书呆子。

在我生命中的某个时刻，偶然之间，我爱上了举重。我喜欢把举重定义为一种书呆子的运动：**它是一个优化问题，你需要平衡训练和饮食，并且你可以通过数字看到自己的进步**；如果你这周比上周举得更多，那就说明你在进步。

我的旅程经历了不少波折。伤病、搬家、疫情以及失去兴趣都曾挡在我的路上。但一个月前，我突然受到启发，决定在家设立一个健身房：一个架子、哑铃、杠铃、重物和一张长椅。就这样，我又开始行动了。

## 挑战

过去，我曾在普通健身房训练，但从未请过私人教练。我认为他们太贵了，而且看不出他们能帮我什么，似乎我自己也能做到。那是个错误。独自训练导致了伤病，我不再进步，始终停留在平庸的水平。随着时间的推移，**我意识到拥有一位优秀的教练真的非常重要**（在人生中、在任何领域中）。
