# 不要让你的 RAG 知识库仅限于文本

> 原文：[https://towardsdatascience.com/dont-limit-your-rag-knowledgebase-to-just-text-d5e3b999843d?source=collection_archive---------1-----------------------#2024-08-19](https://towardsdatascience.com/dont-limit-your-rag-knowledgebase-to-just-text-d5e3b999843d?source=collection_archive---------1-----------------------#2024-08-19)

## 盗取这个即插即用的 Python 脚本，轻松将图像集成到你的聊天机器人知识库中

[](https://medium.com/@marcosss?source=post_page---byline--d5e3b999843d--------------------------------)[![Marcos Santiago](../Images/570c90fc27b75cbc22786ffcc4a38e6d.png)](https://medium.com/@marcosss?source=post_page---byline--d5e3b999843d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d5e3b999843d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d5e3b999843d--------------------------------) [Marcos Santiago](https://medium.com/@marcosss?source=post_page---byline--d5e3b999843d--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--d5e3b999843d--------------------------------) ·阅读时长7分钟·2024年8月19日

--

![](../Images/5744b566e9ae3f3bc18f08717fa7e57c.png)

图片来源：[Nitish Meena](https://unsplash.com/@nitishm?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在构建知识库时，一个常见的挑战是将所有内容转化为纯文本。当涉及到像幻灯片、PDF、图像等媒体源时，这可能会受到限制。

那么，我们如何合理利用那些不是纯文本的数据呢？

> ⛳ 没有 Medium 会员资格？我为你准备好了：使用这个[**免费文章链接**](https://medium.com/@marcosss/d5e3b999843d?sk=a139563c99eab8130ed29e14a47c268d)。请考虑留下**高亮**、**点赞**、**关注**和**评论** ⛳

由于最近人工智能的进展，现在比以往任何时候都更容易且更便宜。通过使用具有视觉能力的大型语言模型（LLMs），我们可以转录成千上万的图像，不仅捕捉文本，还能理解内容之间的关系。如果需要，这些模型甚至可以描述图像中的视觉对象，提供比光学字符识别（OCR）更丰富、更详细的转录。

我们将从这三个简单的步骤开始：

1.  **收集数据**：收集你计划使用的图像，确保它们井然有序，并且信息量不至于过载。

1.  **上传数据**：设置一个 AWS S3 桶来存储你的图像，确保基于云的 AI 模型能够……
