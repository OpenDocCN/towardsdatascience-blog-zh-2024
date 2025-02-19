# 评估长上下文大语言模型

> 原文：[`towardsdatascience.com/evaluating-long-context-large-language-models-fbd665b3544c?source=collection_archive---------9-----------------------#2024-07-31`](https://towardsdatascience.com/evaluating-long-context-large-language-models-fbd665b3544c?source=collection_archive---------9-----------------------#2024-07-31)

## 目前，越来越多的语言模型正在朝着更长的上下文窗口发展。但它们的效果如何？我们该如何评估它们呢？

[](https://medium.com/@artfish?source=post_page---byline--fbd665b3544c--------------------------------)![Yennie Jun](https://medium.com/@artfish?source=post_page---byline--fbd665b3544c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fbd665b3544c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbd665b3544c--------------------------------) [Yennie Jun](https://medium.com/@artfish?source=post_page---byline--fbd665b3544c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbd665b3544c--------------------------------) ·9 分钟阅读·2024 年 7 月 31 日

--

![](img/dc86bcc54c0578652766a0d4fdb2c64e.png)

近年来，语言模型的上下文窗口以指数级的速度增长。图由作者创建。

*本文最初发布于* [*Art Fish Intelligence*](https://www.artfish.ai/p/long-context-llms)*。*

# 引言

大型语言模型的上下文窗口——它们一次能处理的文本量——一直在以指数速度增长。

2018 年，像 [BERT](https://arxiv.org/abs/1810.04805)、[T5](https://arxiv.org/abs/1910.10683) 和 [GPT-1](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf) 等语言模型的输入限制为最多 512 个 token。现在，到了 2024 年夏季，这一数字已经跃升至 200 万个 token（在公开可用的大型语言模型中）。但这对我们意味着什么呢？我们该如何评估这些日益强大的模型？

# 什么是大型上下文窗口？

最近发布的 [Gemini 1.5 Pro 模型可以处理最多 200 万个 token](https://developers.googleblog.com/en/new-features-for-the-gemini-api-and-google-ai-studio/)。但 200 万个 token 到底意味着什么呢？

如果我们估算 4 个单词大约等于 3 个 token，那么 200 万个 token 几乎可以容纳整个《哈利·波特》系列和《指环王》系列的内容。

*(《哈利·波特》系列七本书的总字数为* [*1,084,625*](https://brokebybooks.com/the-word-count-of-175-favorite-novels/)*。 《魔戒》系列七本书的总字数为*[](https://brokebybooks.com/the-word-count-of-175-favorite-novels/#:~:text=Narnia%20series%20is-,345%2C535,-.%20That%E2%80%99s%20approximately%20the)[*481,103*](https://www.reddit.com/r/todayilearned/comments/fnheyt/til_that_tolkeins_lord_of_the_rings_trilogy_has_a/)*。(1,084,625 +*…
