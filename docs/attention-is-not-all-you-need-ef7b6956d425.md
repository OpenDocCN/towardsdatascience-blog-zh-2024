# 注意力（并非）你所需要的一切

> 原文：[https://towardsdatascience.com/attention-is-not-all-you-need-ef7b6956d425?source=collection_archive---------4-----------------------#2024-11-19](https://towardsdatascience.com/attention-is-not-all-you-need-ef7b6956d425?source=collection_archive---------4-----------------------#2024-11-19)

## 一种替代的文本生成变换器模型方法

[](https://medium.com/@thejoshtaylor?source=post_page---byline--ef7b6956d425--------------------------------)[![Josh Taylor](../Images/e3c9cb25df3e0b870d28b5844cd3ddff.png)](https://medium.com/@thejoshtaylor?source=post_page---byline--ef7b6956d425--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ef7b6956d425--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ef7b6956d425--------------------------------) [Josh Taylor](https://medium.com/@thejoshtaylor?source=post_page---byline--ef7b6956d425--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef7b6956d425--------------------------------) ·阅读时间：7分钟·2024年11月19日

--

![](../Images/8840625d116b00a261505a81da53e8a0.png)

分形图案能否帮助我们创造出更高效的文本生成模型？照片由[Giulia May](https://unsplash.com/@giuliamay?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

自2022年11月底ChatGPT发布以来，LLM（大型语言模型）几乎成为了家喻户晓的名字。

![](../Images/2dd39a5162081d74bf681e8a8b5c6908.png)

全球范围内对“LLM”的搜索兴趣。来源：[Google Trends](https://trends.google.com/trends/explore?date=today+5-y&q=llm&hl=en-GB)

这样做是有充分理由的；它们的成功归功于其架构，特别是**注意力机制**。它使模型能够将每个处理的单词与*其他所有*单词进行比较。

这使得LLM在理解和生成类人文本方面具有我们熟知的非凡能力。

然而，这些模型并非没有缺陷。它们训练时需要巨大的计算资源。例如，Meta的Llama 3模型训练耗时770万个GPU小时[1]。此外，它们对庞大数据集的依赖——涵盖了数万亿个标记——引发了关于可扩展性、可获取性以及环境影响的疑问。

尽管存在这些挑战，自从2017年中期发布论文《Attention is all you need》以来，人工智能领域的最新进展大多集中在进一步扩展注意力机制，而非探索根本全新的架构。
