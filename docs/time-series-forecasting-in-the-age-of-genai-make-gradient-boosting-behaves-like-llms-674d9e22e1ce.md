# 生成型人工智能时代的时间序列预测：让梯度提升像大型语言模型一样工作

> 原文：[https://towardsdatascience.com/time-series-forecasting-in-the-age-of-genai-make-gradient-boosting-behaves-like-llms-674d9e22e1ce?source=collection_archive---------2-----------------------#2024-07-04](https://towardsdatascience.com/time-series-forecasting-in-the-age-of-genai-make-gradient-boosting-behaves-like-llms-674d9e22e1ce?source=collection_archive---------2-----------------------#2024-07-04)

## 使用标准机器学习模型进行零样本预测

[](https://medium.com/@cerlymarco?source=post_page---byline--674d9e22e1ce--------------------------------)[![Marco Cerliani](../Images/ddc7943bfef3a7d59e36cc525dd5442e.png)](https://medium.com/@cerlymarco?source=post_page---byline--674d9e22e1ce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--674d9e22e1ce--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--674d9e22e1ce--------------------------------) [Marco Cerliani](https://medium.com/@cerlymarco?source=post_page---byline--674d9e22e1ce--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--674d9e22e1ce--------------------------------) ·6分钟阅读·2024年7月4日

--

![](../Images/ceb06c99955f15e354c0d7778da1f3f7.png)

图片来源：[David Menidrey](https://unsplash.com/@cazault?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

生成型人工智能和大型语言模型（LLMs）的兴起吸引了全球的关注，引发了各个领域的革命。虽然这种技术的主要焦点一直放在文本序列上，但现在更多的关注开始转向扩展其能力，处理和处理除文本输入之外的其他数据格式。

和大多数人工智能领域一样，时间序列预测也没有免受大型语言模型（LLMs）出现的影响，但这可能对所有人来说都是一件好事。时间序列建模被认为更像是一门艺术，其中结果在很大程度上依赖于先前的领域知识和适当的调优。相反，LLMs因其任务无关性而受到赞赏，拥有巨大的潜力，能够运用其知识解决来自不同领域的多样化任务。通过这两个领域的结合，新的时间序列预测模型的前沿可能会诞生，未来能够实现以前不可思议的结果。

![](../Images/b0435374fc806308f6b0d1b2eb8f1787.png)

[由作者提供的图片]
