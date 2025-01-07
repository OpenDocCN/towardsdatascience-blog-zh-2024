# 使用深度学习预测在线平台中的参与度

> 原文：[https://towardsdatascience.com/deep-learning-to-predict-engagement-in-online-platforms-dfd79515b85e?source=collection_archive---------2-----------------------#2024-06-17](https://towardsdatascience.com/deep-learning-to-predict-engagement-in-online-platforms-dfd79515b85e?source=collection_archive---------2-----------------------#2024-06-17)

## 训练LSTM模型理解在线平台的使用情况。

[](https://medium.com/@danielmillerAI?source=post_page---byline--dfd79515b85e--------------------------------)[![Daniel Miller](../Images/9728d4c65817946169e66b228032980c.png)](https://medium.com/@danielmillerAI?source=post_page---byline--dfd79515b85e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dfd79515b85e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dfd79515b85e--------------------------------) [Daniel Miller](https://medium.com/@danielmillerAI?source=post_page---byline--dfd79515b85e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dfd79515b85e--------------------------------) ·11分钟阅读·2024年6月17日

--

![](../Images/6619ac38e48052814217f147bc32824c.png)

深度学习在时间序列用例中的应用示意图。由作者使用DALL-E/GPT4创建。

# 概要

在我爱丁堡大学的硕士论文中，我使用了深度学习技术来预测用户在Zooniverse上的参与度。Zooniverse是一个在线公民科学平台，允许非科学家贡献于特定领域，如行星探索。Zooniverse已经促成了[超过100篇论文的发表](https://www.zooniverse.org/#:~:text=Our%20researchers%20have%20used%20the,that%20encourage%20many%20fascinating%20discoveries.)**。** 在实际环境中，公民科学家证明了英国的农业径流正在增加污染，超过了[安全阈值](https://www.theguardian.com/environment/2024/jan/30/english-rivers-pollution-sewage-agriculture-uk#:~:text=4%20months%20old-,83%25%20of%20English%20rivers%20have%20evidence%20of,pollution%20from%20sewage%20and%20agriculture&text=Eighty%2Dthree%20per%20cent%20of,take%20place%20in%20the%20UK.)。

在这个背景下，参与度是指我们是否能够根据用户的历史行为预测他们未来对平台的使用情况。这可以表现为多种形式，例如：

+   预测在时间阈值内完成的任务数量

+   用户将在当前工作会话中停留的时间量

我们聚焦于任务段的定义，并试图预测用户是否会继续工作10、20或30分钟。这是通过将用户任务**T**分组到工作会话**W**中来计算的，其中：

这个定义之所以被使用，是因为它的灵活性，并且之前在 [Zooniverse](https://ojs.aaai.org/index.php/HCOMP/article/view/13076)、[StackOverflow](https://arxiv.org/pdf/2008.06125) 和 [Coursera](https://arxiv.org/pdf/1403.3100) 中也有使用。
