# RLHF 的故事：起源、动机、技术和现代应用

> 原文：[`towardsdatascience.com/the-story-of-rlhf-origins-motivations-techniques-and-modern-applications-16dfac9e4a45?source=collection_archive---------7-----------------------#2024-02-29`](https://towardsdatascience.com/the-story-of-rlhf-origins-motivations-techniques-and-modern-applications-16dfac9e4a45?source=collection_archive---------7-----------------------#2024-02-29)

## 如何通过人类反馈学习彻底改变生成式语言模型……

[](https://wolfecameron.medium.com/?source=post_page---byline--16dfac9e4a45--------------------------------)![Cameron R. Wolfe, Ph.D.](https://wolfecameron.medium.com/?source=post_page---byline--16dfac9e4a45--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--16dfac9e4a45--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--16dfac9e4a45--------------------------------) [Cameron R. Wolfe 博士](https://wolfecameron.medium.com/?source=post_page---byline--16dfac9e4a45--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--16dfac9e4a45--------------------------------) ·31 分钟阅读·2024 年 2 月 29 日

--

![](img/aa2218c0fe05bde5d2b1dd7cd6368f4f.png)

（图片由[Towfiqu barbhuiya](https://unsplash.com/@towfiqu999999?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来自[Unsplash](https://unsplash.com/photos/a-row-of-yellow-stars-sitting-on-top-of-a-blue-and-pink-surface-0ZUoBtLw3y4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

长期以来，人工智能领域一直利用不同风格的语言模型（例如，[n-gram 模型](https://en.wikipedia.org/wiki/Word_n-gram_language_model)，[RNNs](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)，[transformers](https://cameronrwolfe.substack.com/i/108182616/different-transformer-architectures)等）来自动化生成性和判别性自然语言任务。2018 年，随着 BERT [10]的提出，这一研究领域迎来了前所未有的关注，BERT 展示了 transformer 架构、自监督预训练和监督迁移学习的强大结合。事实上，BERT 在当时应用的每个基准测试中都创下了新的最先进性能。尽管 BERT 不能用于生成任务，但我们通过[T5](https://cameronrwolfe.substack.com/p/t5-text-to-text-transformers-part) [11]的提出看到了监督迁移学习在这一领域的有效性。然而，尽管取得了这些成就，这些模型与如今如 GPT-4 等 LLM 的生成能力相比仍显得微不足道。要创建这样的模型，我们需要远远超越监督学习的训练技术。

> “我们的目标是以最有可能造福全人类的方式推进数字智能。” *— OpenAI* [*创始声明*](https://openai.com/blog/introducing-openai) *(2015 年 12 月)*
