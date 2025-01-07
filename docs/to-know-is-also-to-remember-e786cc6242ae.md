# 知道就是记得

> 原文：[https://towardsdatascience.com/to-know-is-also-to-remember-e786cc6242ae?source=collection_archive---------9-----------------------#2024-04-25](https://towardsdatascience.com/to-know-is-also-to-remember-e786cc6242ae?source=collection_archive---------9-----------------------#2024-04-25)

## 理解长短期记忆网络

[](https://medium.com/@manfred.james?source=post_page---byline--e786cc6242ae--------------------------------)[![Diego Manfre](../Images/2189d8e63df449a869526bf8b6c50440.png)](https://medium.com/@manfred.james?source=post_page---byline--e786cc6242ae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e786cc6242ae--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e786cc6242ae--------------------------------) [Diego Manfre](https://medium.com/@manfred.james?source=post_page---byline--e786cc6242ae--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e786cc6242ae--------------------------------) ·14分钟阅读·2024年4月25日

--

![](../Images/635afc20899548dd72d2b1a72031ff5e.png)

由作者使用[MidJourney](https://www.midjourney.com/)生成的图像

一男一女在临床研究中心的一个安静房间里交谈。女性提问后，等待男性回答并做笔记。乍一看，这似乎是一场普通的对话。然而，这绝非寻常。在女性的笔记本中，无论每页上写着什么日期，男性的回答总是相同的。尽管这些对话发生在80年代，但提到的事件却是10多年前的事情。[Jenni Ogden](https://en.wikipedia.org/wiki/Jenni_Ogden)是最早与病人H.M.交谈的研究人员之一，几年后，H.M.因对神经心理学的影响而广为人知，最终会以他的真实姓名[Henry Molaison](https://en.wikipedia.org/wiki/Henry_Molaison)被大家所熟知。经过多年的访谈和测试，研究人员得出结论，亨利失去了生成新记忆的能力，这与他27岁时接受的脑叶切除手术有关。亨利的案例帮助我们更好地理解了大脑功能与记忆之间的关系，并促成了短期记忆和长期记忆的概念诞生。这个概念为机器学习领域的开创性研究铺平了道路，在这个领域，科学家和开发者们试图从大脑神秘的内在机制中找到更好的方式来构建预测模型。

# 引言
