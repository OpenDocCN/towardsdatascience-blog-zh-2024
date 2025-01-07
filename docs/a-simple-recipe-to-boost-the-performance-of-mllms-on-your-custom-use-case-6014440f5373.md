# 提升 MLLMs 在自定义使用场景中性能的简单方法

> 原文：[https://towardsdatascience.com/a-simple-recipe-to-boost-the-performance-of-mllms-on-your-custom-use-case-6014440f5373?source=collection_archive---------14-----------------------#2024-06-11](https://towardsdatascience.com/a-simple-recipe-to-boost-the-performance-of-mllms-on-your-custom-use-case-6014440f5373?source=collection_archive---------14-----------------------#2024-06-11)

## 一份使用最新口袋大小 Mini-InternVL 模型的 MLLM QLoRA 微调教程

[](https://medium.com/@CVxTz?source=post_page---byline--6014440f5373--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--6014440f5373--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6014440f5373--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6014440f5373--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--6014440f5373--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6014440f5373--------------------------------) ·阅读时长 6 分钟·2024 年 6 月 11 日

--

![](../Images/ac67a9c7e6007446e729e1c9c26e9c26.png)

图片由 [Maarten van den Heuvel](https://unsplash.com/@mvdheuvel?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大型语言模型（LLMs）的世界在不断发展，新的技术进步层出不穷。一个令人兴奋的领域是多模态 LLM（MLLM）的发展，它能够理解并与文本和图像进行交互。这为文档理解、视觉问答等任务带来了无限可能。

我最近写了一篇关于这种模型的一般性文章，您可以在这里查看：

[](/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=post_page-----6014440f5373--------------------------------) [## 微软最新的 Phi-3 视觉语言模型的 6 个现实世界应用

### 探索 Phi-3-Vision 的可能应用场景，这是一个小巧而强大的 MLLM，可以本地运行（附代码示例）

towardsdatascience.com](/6-real-world-uses-of-microsofts-newest-phi-3-vision-language-model-8ebbfa317fe8?source=post_page-----6014440f5373--------------------------------)

但在这篇文章中，我们将探索一个强大的组合：InternVL 模型和 QLoRA 微调技术。**我们将重点讨论如何轻松定制这些模型以适应任何特定的使用场景。**我们将使用这些工具创建一个收据理解流水线，能够高精度地提取诸如公司名称、地址和总购金额等关键信息。

# 理解任务和数据集
