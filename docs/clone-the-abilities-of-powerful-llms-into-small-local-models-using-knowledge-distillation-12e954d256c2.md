# 使用知识蒸馏将强大 LLM 的能力克隆到小型本地模型中

> 原文：[`towardsdatascience.com/clone-the-abilities-of-powerful-llms-into-small-local-models-using-knowledge-distillation-12e954d256c2?source=collection_archive---------4-----------------------#2024-04-02`](https://towardsdatascience.com/clone-the-abilities-of-powerful-llms-into-small-local-models-using-knowledge-distillation-12e954d256c2?source=collection_archive---------4-----------------------#2024-04-02)

## 利用大规模模型的监督提升本地 LLM 的性能

[](https://medium.com/@CVxTz?source=post_page---byline--12e954d256c2--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--12e954d256c2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12e954d256c2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12e954d256c2--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--12e954d256c2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12e954d256c2--------------------------------) ·7 分钟阅读·2024 年 4 月 2 日

--

![](img/75ff2d16cad0eb78895e199d33f7552f.png)

图片由 [matthew Feeney](https://unsplash.com/@matt__feeney?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/person-wearing-black-and-gray-jacket-in-front-of-bookshelf-Nwkh-n6l25w?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在自然语言处理（NLP）领域，前沿的大型语言模型（LLMs）提供了出色的少样本学习和推理能力。然而，这些模型的计算需求和延迟有时会使它们在某些应用中变得不切实际。例如，如果您的目标是开发一项翻译服务，您可能并不需要后端的 LLM 具备开玩笑或向幼儿解释量子物理的能力。这突显了对专门化、小规模模型的需求。

针对这一挑战的可行解决方案是构建量身定制的 LLM，精确满足您的特定用例。这涉及到对大量数据进行标注，然后对像 Tiny-llama 这样的小型模型进行微调，以适应您的需求。这种方法不仅确保了模型与您的需求紧密契合，还减少了与大型 LLM 相关的计算和部署成本。然而，必须承认这种方法的缺点：数据标注过程通常是繁琐且耗时的。
