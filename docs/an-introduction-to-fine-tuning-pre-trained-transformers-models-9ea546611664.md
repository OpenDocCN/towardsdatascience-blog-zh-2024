# 介绍：微调预训练的Transformers模型

> 原文：[https://towardsdatascience.com/an-introduction-to-fine-tuning-pre-trained-transformers-models-9ea546611664?source=collection_archive---------3-----------------------#2024-02-17](https://towardsdatascience.com/an-introduction-to-fine-tuning-pre-trained-transformers-models-9ea546611664?source=collection_archive---------3-----------------------#2024-02-17)

## 简化了利用HuggingFace的trainer对象

[](https://ram-vegiraju.medium.com/?source=post_page---byline--9ea546611664--------------------------------)[![Ram Vegiraju](../Images/07d9334e905f710d9f3c6187cf69a1a5.png)](https://ram-vegiraju.medium.com/?source=post_page---byline--9ea546611664--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ea546611664--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9ea546611664--------------------------------) [Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--9ea546611664--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ea546611664--------------------------------) ·阅读时间：5分钟·2024年2月17日

--

![](../Images/cebb71ff4d5c4ddacfeb817e61408a13.png)

图片来自[Unsplash](https://unsplash.com/photos/matrix-movie-still-iar-afB0QQw)，作者是[Markus Spiske](https://unsplash.com/@markusspiske)

[HuggingFace](https://huggingface.co/)是许多流行的开源NLP模型的聚集地。这些模型大多已经非常有效，但通常需要某种形式的训练或微调，以提高在特定应用场景中的表现。随着LLM的快速发展，在本文中，我们将回顾一下HuggingFace提供的核心构建模块，这些模块能够简化NLP模型的训练过程。

传统上，NLP模型可以通过普通的PyTorch、TensorFlow/Keras以及其他流行的ML框架进行训练。虽然你可以选择这种方式，但这需要你对所使用的框架有更深入的理解，并且需要编写更多的代码来实现训练循环。借助HuggingFace的[Trainer类](https://huggingface.co/docs/transformers/main_classes/trainer)，你可以以更简单的方式与想要使用的NLP Transformers模型进行交互。

Trainer是一个专门针对[Transformers](https://github.com/huggingface/transformers)模型优化的类，并且与其他Transformers库（如[Datasets](https://huggingface.co/docs/datasets/en/index)和[Evaluate](https://huggingface.co/docs/evaluate/en/index)）紧密集成。Trainer在更高级的层次上也支持分布式训练库，并且可以轻松集成到如Amazon SageMaker这样的基础设施平台中。
