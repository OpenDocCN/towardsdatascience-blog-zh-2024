# 简洁而甜美：通过约束性思维链提升 LLM 表现

> 原文：[`towardsdatascience.com/short-and-sweet-enhancing-llm-performance-with-constrained-chain-of-thought-c4479361d995?source=collection_archive---------4-----------------------#2024-08-07`](https://towardsdatascience.com/short-and-sweet-enhancing-llm-performance-with-constrained-chain-of-thought-c4479361d995?source=collection_archive---------4-----------------------#2024-08-07)

## |LLM|提示工程|COT|推理|

## 有时候几句话就足够了：缩短输出长度以提高准确性

[](https://salvatore-raieli.medium.com/?source=post_page---byline--c4479361d995--------------------------------)![Salvatore Raieli](https://salvatore-raieli.medium.com/?source=post_page---byline--c4479361d995--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c4479361d995--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4479361d995--------------------------------) [萨尔瓦托雷·雷耶利](https://salvatore-raieli.medium.com/?source=post_page---byline--c4479361d995--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4479361d995--------------------------------) ·9 分钟阅读·2024 年 8 月 7 日

--

![](img/46d3ba9bc12c1d09841c8f19a65b017b.png)

由作者使用 AI 创作的图片

> 简洁是雄辩的魅力所在。——马库斯·图利乌斯·西塞罗
> 
> 简洁和精炼是正确的根源。——霍西亚·巴卢

[大语言模型（LLMs）](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=Large%20Language%20Models,-What%20is%20a) 在推理领域展现了有趣的能力。随着其应用的推广，出现了一个新的应用领域： [提示工程](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=What%20is%20a%20prompt%3F%20What%20is%20prompt%20engineering%3F)。实际上，与这些模型的交互是通过使用提示进行的，因此，开发出了许多技术来提升 LLM 的这些能力。

[](https://pub.towardsai.net/prompt-engineering-to-leverage-in-context-learning-in-large-language-models-72296e1f09c3?source=post_page-----c4479361d995--------------------------------) [## 提升大语言模型表现的提示工程：借力上下文学习

### 如何修改文本提示，以便在不训练的情况下从 LLM 中获取最佳效果

pub.towardsai.net](https://pub.towardsai.net/prompt-engineering-to-leverage-in-context-learning-in-large-language-models-72296e1f09c3?source=post_page-----c4479361d995--------------------------------)

最具吸引力的技术之一是[链式思维（CoT）提示](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=What%20is%20Chain%2Dof%2DThought%20(CoT)%3F)；该技术提高了解题过程中的正确性，并解释了模型是如何得出解决方案的（或出现推理错误的原因）。
