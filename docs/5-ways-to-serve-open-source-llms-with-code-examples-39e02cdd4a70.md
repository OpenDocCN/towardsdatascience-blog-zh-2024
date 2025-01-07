# 5种服务开源LLM的方法（带代码示例）

> 原文：[https://towardsdatascience.com/5-ways-to-serve-open-source-llms-with-code-examples-39e02cdd4a70?source=collection_archive---------8-----------------------#2024-04-09](https://towardsdatascience.com/5-ways-to-serve-open-source-llms-with-code-examples-39e02cdd4a70?source=collection_archive---------8-----------------------#2024-04-09)

## 适用于Llama 2 7B的每种方法的代码和说明

[](https://medium.com/@CVxTz?source=post_page---byline--39e02cdd4a70--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--39e02cdd4a70--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--39e02cdd4a70--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--39e02cdd4a70--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--39e02cdd4a70--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39e02cdd4a70--------------------------------) ·阅读时间：10分钟·2024年4月9日

--

![](../Images/41df84ad14cb436bcece2b94c2995562.png)

图片由[Kyaw Tun](https://unsplash.com/@kyawthutun?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来自[Unsplash](https://unsplash.com/photos/blue-siamese-fighting-fish-k6BHLfw_jUg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在快速发展的大型语言模型（LLMs）领域，服务它们的工具和技术正以前所未有的速度进步，几乎与这些模型本身的速度一样快。与xgboost或MNIST分类器CNN等传统模型不同，LLM模型在规模和复杂性上都要庞大得多，因此在部署时需要更加细致的关注。

本文的焦点集中在开源LLM上，它们因可调性和可操作性而脱颖而出，可能是最具优势的选择，因为它们允许任何人参与并推动这一领域的进步。

我的目标是引导你了解多种服务LLM的方法，满足不同的使用场景。我将介绍五种不同的选项，每种选项都附有详细的复制指南，并对其各自的优缺点进行全面分析。

我们将探讨本地部署和使用托管服务的选项。更重要的是，我们将讨论的这些服务提供了丰厚的免费额度，帮助你在不花一分钱的情况下进行实验。

以下是可用的选项：

1.  本地服务器：Anaconda + CPU

1.  本地服务器：Anaconda + GPU
