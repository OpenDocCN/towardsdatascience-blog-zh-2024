# 为什么 GenAI 是数据删除和隐私的噩梦

> 原文：[https://towardsdatascience.com/why-genai-is-a-data-deletion-and-privacy-nightmare-bd79a3c0ed85?source=collection_archive---------0-----------------------#2024-09-20](https://towardsdatascience.com/why-genai-is-a-data-deletion-and-privacy-nightmare-bd79a3c0ed85?source=collection_archive---------0-----------------------#2024-09-20)

## 理解 LinkedIn OptOut 丑闻的关键线索

[](https://kozyrkov.medium.com/?source=post_page---byline--bd79a3c0ed85--------------------------------)[![Cassie Kozyrkov](../Images/ad18dd12979a4a3ec130bdf8b889af23.png)](https://kozyrkov.medium.com/?source=post_page---byline--bd79a3c0ed85--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bd79a3c0ed85--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bd79a3c0ed85--------------------------------) [Cassie Kozyrkov](https://kozyrkov.medium.com/?source=post_page---byline--bd79a3c0ed85--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--bd79a3c0ed85--------------------------------) ·阅读时间13分钟·2024年9月20日

--

# 要点

> 一旦训练数据被融入大型语言模型中，试图删除这些数据就像是想在蛋糕烤好后去掉糖分一样。你只能丢掉整个蛋糕，重新开始……

# 背景—什么是 LinkedIn OptOut？

在过去几天里，非欧盟地区的 LinkedIn 用户震惊地发现，他们已悄悄地被默认加入，允许 LinkedIn 及其关联公司在他们的内容上训练内容创作生成型 AI（GenAI）模型。在昨天的博客文章中，我解释了 LinkedIn OptOut 事件发生了什么，以及如何选择退出。可以在[第一部分](https://bit.ly/quaesita_linkedinoptin1)查看详细内容。

[](https://kozyrkov.medium.com/the-linkedin-optin-ai-scandal-52679dcc9ca6?source=post_page-----bd79a3c0ed85--------------------------------) [## LinkedIn OptOut AI 丑闻

### 你已经被默认加入了一些可疑的操作，以下是如何退出的方法

[kozyrkov.medium.com](https://kozyrkov.medium.com/the-linkedin-optin-ai-scandal-52679dcc9ca6?source=post_page-----bd79a3c0ed85--------------------------------)

在第二部分，我将解释为什么这个故事与数据删除法有着密切关系。以下是简要版本：

*当你的数据被用来训练 GenAI 模型时，即使作为用户，你的法律权利是删除数据，但也无法（在实际操作中）保证删除。尤其是当涉及到你倾注心血创作的内容时*……
