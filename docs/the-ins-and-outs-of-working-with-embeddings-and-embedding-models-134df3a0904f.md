# 嵌入与嵌入模型的工作原理

> 原文：[`towardsdatascience.com/the-ins-and-outs-of-working-with-embeddings-and-embedding-models-134df3a0904f?source=collection_archive---------7-----------------------#2024-03-07`](https://towardsdatascience.com/the-ins-and-outs-of-working-with-embeddings-and-embedding-models-134df3a0904f?source=collection_archive---------7-----------------------#2024-03-07)

[](https://towardsdatascience.medium.com/?source=post_page---byline--134df3a0904f--------------------------------)![TDS 编辑团队](https://towardsdatascience.medium.com/?source=post_page---byline--134df3a0904f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--134df3a0904f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--134df3a0904f--------------------------------) [TDS 编辑团队](https://towardsdatascience.medium.com/?source=post_page---byline--134df3a0904f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--134df3a0904f--------------------------------) ·作为时事通讯发送 ·3 分钟阅读·2024 年 3 月 7 日

--

准备好深入探讨一个及时的技术话题了吗？我们希望是的，因为本周的《Variable》将带你进入嵌入（embeddings）的迷人世界。

嵌入和嵌入模型是近年来涌现的强大 AI 工具的重要构建块，这使得数据科学和机器学习从业者在这一领域提高流利度显得尤为重要。即使你过去曾经探讨过嵌入，也永远不算晚扩展你的知识，了解新兴的方法和应用场景。

本周的亮点内容涵盖从相对高层次到非常细致的内容，从理论到极其实践的内容。不论你对嵌入的了解有多少，我们相信你都会在这里找到一些引起你兴趣的东西。

+   **如何为你的 RAG 找到最佳的多语言嵌入模型**正如[Iulia Brezeanu](https://medium.com/u/5548b8f29f30?source=post_page---user_mention--134df3a0904f--------------------------------)强调所说，“除了拥有高质量的数据，选择一个好的嵌入模型是优化你的 RAG 应用程序中最重要且被低估的一步。”跟随她通俗易懂的指南，了解如何为你的项目做出最佳选择。

+   **OpenAI 与开源多语言嵌入模型的对比**为了从另一个角度了解当前多语言嵌入模型领域的选择，我们强烈推荐[Yann-Aël Le Borgne](https://medium.com/u/594429fc22d0?source=post_page---user_mention--134df3a0904f--------------------------------)的文章，其中详细对比了 OpenAI 最新一代嵌入模型与其开源对应模型的性能。

+   **如何从数据中创建强大的嵌入，以供你的 AI 使用**回顾模型选择问题，[Eivind Kjosbakken](https://medium.com/u/1b616317eda?source=post_page---user_mention--134df3a0904f--------------------------------)深入分析了将数据“从图像、文本和音频等格式转换为强大嵌入，并用于机器学习任务”的不同方法。

![](img/bd73f688ef3a8b6ab9be1bca8f26797d.png)

图片由[Alex Hu](https://unsplash.com/@alexandwich?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   **统计方法 scDEED 检测可疑的 t-SNE 和 UMAP 嵌入，并优化超参数**在最新的论文中，[Jingyi Jessica Li](https://medium.com/u/75697d40942e?source=post_page---user_mention--134df3a0904f--------------------------------) 和 Christy Lee 带领我们了解了从高维到二维空间投影中数据失真问题，并提出了在二维降维方法中优化超参数设置的框架。

+   **使用 AI 编辑图像中的文本**场景文本编辑——在图像中调整文本元素——是一个令人惊讶的复杂任务。[Julia Turc](https://medium.com/u/f758859396fc?source=post_page---user_mention--134df3a0904f--------------------------------)分享了研究人员在这一领域取得的最新进展，并扩展了嵌入模型在 STE 模型架构中的作用。

+   **增强推荐系统多样性的现实世界新方法**为了更具体地展示嵌入模型的强大能力，我们推荐[Christabelle Pabalan](https://medium.com/u/4200eb8e8b26?source=post_page---user_mention--134df3a0904f--------------------------------)的最新文章。文章解析了提升推荐系统输出多样性的难题，并展示了如何选择合适的嵌入模型，成为实现非常有前景的结果的关键一步。

对于本周想探索其他话题的读者，我们非常高兴推荐一些我们最近的亮点：

+   为了他们的 TDS 首秀，[Skylar Jean Callis](https://medium.com/u/21adf08dac48?source=post_page---user_mention--134df3a0904f--------------------------------) 分享了关于视觉变换器（Vision Transformers, ViT）的全面技术讲解，并附带了一个 PyTorch 实现。

+   跟踪 [Maarten Grootendorst](https://medium.com/u/22405c3b2875?source=post_page---user_mention--134df3a0904f--------------------------------) 的关于 Mamba（新型状态空间模型架构）的深度解析，保持与最新的 ML 研究同步，Mamba 旨在成为变换器的替代方案。

+   在一篇引人深思的文章中，[Louis Chan](https://medium.com/u/6d585e26760a?source=post_page---user_mention--134df3a0904f--------------------------------) 反思了一些常见的数据科学家与工程师之间的紧张关系来源（以及团队可以做些什么来缓解这些问题）。

+   随着近年来模型规模的膨胀，模型压缩的重要性也同步增长。[Nate Cibik](https://medium.com/u/82bf2304955e?source=post_page---user_mention--134df3a0904f--------------------------------) 刚刚发布了一系列精简方法的优秀文章，第一部分专注于剪枝。

+   好奇想了解模拟的强大力量吗? [Hennie de Harder](https://medium.com/u/fb96be98b7b9?source=post_page---user_mention--134df3a0904f--------------------------------) 的新解释文章聚焦于蒙特卡罗方法及其如何帮助解决复杂问题。

+   历史爱好者，欢呼吧：[Sachin Date](https://medium.com/u/b75b5b1730f3?source=post_page---user_mention--134df3a0904f--------------------------------) 刚刚发布了他关于关键数学概念起源的系列文章的最新一篇，这次聚焦于皮埃尔-西蒙·拉普拉斯与中心极限定理。

感谢你支持我们作者的工作！如果你受到启发，想加入他们的行列，何不[写下你的第一篇文章？我们期待阅读它](http://bit.ly/write-for-tds)。

直到下一个变量，

TDS 团队
