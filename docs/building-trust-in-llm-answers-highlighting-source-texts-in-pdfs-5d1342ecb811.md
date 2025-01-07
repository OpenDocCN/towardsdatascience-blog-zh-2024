# 构建LLM答案的信任：在PDF中突出源文本

> 原文：[https://towardsdatascience.com/building-trust-in-llm-answers-highlighting-source-texts-in-pdfs-5d1342ecb811?source=collection_archive---------0-----------------------#2024-12-27](https://towardsdatascience.com/building-trust-in-llm-answers-highlighting-source-texts-in-pdfs-5d1342ecb811?source=collection_archive---------0-----------------------#2024-12-27)

## 100%的准确性并不是一切：帮助用户导航文档才是真正的价值所在。

[](https://medium.com/@angela.shi?source=post_page---byline--5d1342ecb811--------------------------------)[![Angela & Kezhan Shi](../Images/e6bd57b0ca397b78cf810733c7262e18.png)](https://medium.com/@angela.shi?source=post_page---byline--5d1342ecb811--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5d1342ecb811--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5d1342ecb811--------------------------------) [Angela & Kezhan Shi](https://medium.com/@angela.shi?source=post_page---byline--5d1342ecb811--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5d1342ecb811--------------------------------) ·6分钟阅读·2024年12月27日

--

所以，你正在构建一个RAG系统，或者使用LLM与文档进行对话。但用户经常会问：我们如何信任答案呢？

此外，我们经常听到“幻觉”这一现象，它会破坏用户的信任。

如果我们构建了一个应用程序，但没有向用户展示答案的来源，某些情况下这个应用可能变得无法使用。

在这篇文章中，我将分享一种方法来解决这个问题。通过将LLM生成的每个答案与文档中的源文本链接起来，我们可以建立透明度和信任。这种方法不仅提供了明确的答案证据，还允许用户直接在PDF中验证结果。

有时候，生成的答案可能并不完全准确，但能够找到正确的源文本对用户已经非常有帮助。

让我们以[这篇论文](https://arxiv.org/pdf/2410.05229)为例，来自arxiv.org。我们可以想象这个使用场景：

![](../Images/31cb0653d8ae3ea4091e6e82d5781b0a.png)

作者提供的图片——文档展示

# 第一步：从PDF中提取文本

该方法的第一步是从PDF中提取结构化格式的文本。
