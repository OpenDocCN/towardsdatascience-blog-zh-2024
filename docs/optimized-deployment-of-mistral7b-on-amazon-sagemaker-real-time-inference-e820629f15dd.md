# 在 Amazon SageMaker 实时推理上优化 Mistral7B 的部署

> 原文：[`towardsdatascience.com/optimized-deployment-of-mistral7b-on-amazon-sagemaker-real-time-inference-e820629f15dd?source=collection_archive---------5-----------------------#2024-02-21`](https://towardsdatascience.com/optimized-deployment-of-mistral7b-on-amazon-sagemaker-real-time-inference-e820629f15dd?source=collection_archive---------5-----------------------#2024-02-21)

## 利用由 DJL Serving 和 Nvidia TensorRT 驱动的大型模型推理容器

[](https://ram-vegiraju.medium.com/?source=post_page---byline--e820629f15dd--------------------------------)![Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--e820629f15dd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e820629f15dd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e820629f15dd--------------------------------) [Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--e820629f15dd--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e820629f15dd--------------------------------) ·9 分钟阅读·2024 年 2 月 21 日

--

![](img/9c7701b317d1f4292baac6499df4b53c.png)

图片来源：[Unsplash](https://unsplash.com/photos/a-close-up-of-a-human-brain-on-a-white-surface-9A9TcXEsy6c) 由[Kommers](https://unsplash.com/@kommers)

生成式人工智能领域以空前的速度持续扩展，每天都有更多的大型语言模型（LLM）家族问世。在每个家族中，也有不同规模的模型，例如 Llama7b、Llama13B 和 Llama70B。无论选择哪个模型，托管这些 LLM 进行推理时都会面临相同的挑战。

这些大型语言模型（LLM）的规模仍然是最紧迫的挑战，因为很难/几乎不可能将许多这样的 LLM 部署到单一的 GPU 上。为了解决这个问题，有几种不同的方法，比如模型分割。通过模型分割，你可以使用[管道并行或张量并行](https://colossalai.org/docs/concepts/paradigms_of_parallelism/#:~:text=There%20are%20generally%20two%20types,to%20parallelize%20computation%20between%20layers.)等技术，将模型分割到多个 GPU 上。除模型分割外，其他常用方法还包括将模型权重进行[量化](https://huggingface.co/docs/optimum/concept_guides/quantization)，通过降低精度来减少模型本身的大小，代价是精度的损失。

虽然模型的规模本身就是一个巨大的挑战，但在文本生成中，保留先前的推理/注意力也是一个挑战，对于基于解码器的模型。使用这些模型进行文本生成并不像传统方法那么简单……
