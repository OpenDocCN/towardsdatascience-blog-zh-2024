# 衡量AI的创造力：视觉字谜

> 原文：[https://towardsdatascience.com/measuring-ais-creativity-with-visual-word-puzzles-cb1bd2f3f4bb?source=collection_archive---------14-----------------------#2024-02-13](https://towardsdatascience.com/measuring-ais-creativity-with-visual-word-puzzles-cb1bd2f3f4bb?source=collection_archive---------14-----------------------#2024-02-13)

## AI模型能多好地解答（并创造）字谜？

[](https://medium.com/@artfish?source=post_page---byline--cb1bd2f3f4bb--------------------------------)[![Yennie Jun](../Images/b635e965f21c3d55833269e12e861322.png)](https://medium.com/@artfish?source=post_page---byline--cb1bd2f3f4bb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cb1bd2f3f4bb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cb1bd2f3f4bb--------------------------------) [Yennie Jun](https://medium.com/@artfish?source=post_page---byline--cb1bd2f3f4bb--------------------------------)

·发布在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb1bd2f3f4bb--------------------------------) ·11分钟阅读 ·2024年2月13日

--

![](../Images/5ee4f9e37b3f8fdb2988a3533d9d9df4.png)

GPT-4：当提示为：``为“视觉字谜”创建一个字谜``时

*这篇文章最初发布在* [*Art Fish Intelligence*](https://www.artfish.ai/p/measuring-ais-creativity-with-visual)

# 引言

对AI来说，*创造力*意味着什么？

去年，我写过一篇关于如何使用几种基于文字的创造力测试来衡量大型语言模型（LLMs）创造力的文章。

[](/exploring-creativity-in-large-language-models-from-gpt-2-to-gpt-4-1c2d1779be57?source=post_page-----cb1bd2f3f4bb--------------------------------) [## 探索大型语言模型中的创造力：从GPT-2到GPT-4

### 通过创造力测试分析大型语言模型创作过程的演变

[towardsdatascience.com](/exploring-creativity-in-large-language-models-from-gpt-2-to-gpt-4-1c2d1779be57?source=post_page-----cb1bd2f3f4bb--------------------------------)

从那时起，人工智能发展迅速，能够处理和创造文本*和*图像。这些模型，有时被称为“多模态大型语言模型”（MLLMs），非常强大，能够理解复杂的文本和视觉输入。

在本文中，我探讨了如何衡量两种流行的多模态大型语言模型（MLLMs）中的创造力：OpenAI的 [GPT-4 Vision](https://openai.com/research/gpt-4v-system-card) 和 [Google的Gemini Pro Vision](https://cloud.google.com/vertex-ai/docs/generative-ai/model-reference/gemini)。我使用了字谜，这是一种需要结合视觉和语言线索来解答的文字谜题。

创造力是极其多面的，很难将其定义为单一的特征……
