# 在单一端点托管多个LLM

> 原文：[https://towardsdatascience.com/hosting-multiple-llms-on-a-single-endpoint-32eda0201832?source=collection_archive---------6-----------------------#2024-01-11](https://towardsdatascience.com/hosting-multiple-llms-on-a-single-endpoint-32eda0201832?source=collection_archive---------6-----------------------#2024-01-11)

## 利用SageMaker推理组件，以高效的成本和性能方式托管Flan和Falcon

[](https://ram-vegiraju.medium.com/?source=post_page---byline--32eda0201832--------------------------------)[![Ram Vegiraju](../Images/07d9334e905f710d9f3c6187cf69a1a5.png)](https://ram-vegiraju.medium.com/?source=post_page---byline--32eda0201832--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--32eda0201832--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--32eda0201832--------------------------------) [Ram Vegiraju](https://ram-vegiraju.medium.com/?source=post_page---byline--32eda0201832--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--32eda0201832--------------------------------) ·10分钟阅读·2024年1月11日

--

![](../Images/58c1d77cfa743d90e720a09808a88264.png)

图片来源：[Unsplash](https://unsplash.com/photos/red-and-black-abstract-illustration-aQYgUYwnCsM) 作者：[**Michael Dziedzic**](https://unsplash.com/@lazycreekimages)

去年，随着多个新模型的推出，配合各种技术和工具帮助训练、托管和评估这些模型，大型语言模型（LLM）领域经历了一次爆炸式增长。具体来说，托管/推理是这些LLM以及机器学习技术在整体上被认可的地方，因为没有推理，这些模型就没有可视化的结果或实际意义。

正如我之前所记录的那样，[托管这些LLM](https://aws.plainenglish.io/four-different-ways-to-host-large-language-models-on-amazon-sagemaker-4d1b027812b5)可能相当具有挑战性，主要因为模型的体积以及如何高效利用与模型相关的硬件。尽管我们已经使用了如[DJL Serving](/deploying-llms-on-amazon-sagemaker-with-djl-serving-8220e3cfad0c)、[文本生成推理（TGI）](/deploying-large-language-models-with-huggingface-tgi-981747c669e3)和[Triton](/deploying-pytorch-models-with-nvidia-triton-inference-server-bb139066a387)等模型服务技术，配合像Amazon SageMaker这样的模型/基础设施托管平台来托管这些LLM，但随着我们尝试将LLM的使用场景推向生产化，另一个问题随之而来。**我们如何能够在多个LLM上做到这一点？**

为什么最初的问题会出现呢？当我们进入生产级别的使用场景时，通常会有多个模型可以使用。例如，可能在你的摘要任务中使用 Llama 模型，而在你的聊天机器人中使用 Falcon 模型。虽然…
