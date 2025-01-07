# 简化信息提取：GPT模型的可重用提示模板

> 原文：[https://towardsdatascience.com/simplify-information-extraction-a-reusable-prompt-template-for-gpt-models-d6d5f1bd25a0?source=collection_archive---------5-----------------------#2024-08-15](https://towardsdatascience.com/simplify-information-extraction-a-reusable-prompt-template-for-gpt-models-d6d5f1bd25a0?source=collection_archive---------5-----------------------#2024-08-15)

## 一个包含对我在超过十几个微妙的医学信息提取任务上有效的提示技术的提示模板

[](https://medium.com/@christabellecp?source=post_page---byline--d6d5f1bd25a0--------------------------------)[![Christabelle Pabalan](../Images/24187865b6e9d03ae1aabf873ce1e67c.png)](https://medium.com/@christabellecp?source=post_page---byline--d6d5f1bd25a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6d5f1bd25a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d6d5f1bd25a0--------------------------------) [Christabelle Pabalan](https://medium.com/@christabellecp?source=post_page---byline--d6d5f1bd25a0--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6d5f1bd25a0--------------------------------) ·8分钟阅读·2024年8月15日

--

![](../Images/c114de989a52b12d77d2540f1ad0a616.png)

由DALL·E生成的图像

# 介绍

如果我告诉你，我为信息提取任务创建了终极提示模板，它将保证您每次都能获得精确的期望性能，具有令人难以置信的回忆和精确度，并且每次都能保证输出格式化，您可能会嗤之以鼻。

而且理所当然 — 因为在LLMs的不可预测性质下，没有人能保证这些复选框。-**融化的脸部表情*-*

然而，这就是我能说的：在对超过十几个微妙的医学信息提取任务进行了广泛工作之后 — 每个任务都需要深入的领域专业知识 — 我开发了一个使用了对我有效的提示技术的提示模板，显著提升了性能并最小化了错误输出。这个模板帮助我简化了工作流程，减少了迭代周期，并为我的结果带来了可靠的一致性水平。

在本文中，我将介绍这个模板，解释每个部分背后的原理，并分享我在这个过程中学到的经验。我希望是…
