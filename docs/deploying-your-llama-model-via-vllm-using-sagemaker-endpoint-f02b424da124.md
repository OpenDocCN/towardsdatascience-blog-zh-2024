# 通过vLLM使用SageMaker端点部署Llama模型

> 原文：[https://towardsdatascience.com/deploying-your-llama-model-via-vllm-using-sagemaker-endpoint-f02b424da124?source=collection_archive---------8-----------------------#2024-09-12](https://towardsdatascience.com/deploying-your-llama-model-via-vllm-using-sagemaker-endpoint-f02b424da124?source=collection_archive---------8-----------------------#2024-09-12)

## 利用AWS的MLOps平台为LLM模型提供服务

[](https://medium.com/@teosiyang?source=post_page---byline--f02b424da124--------------------------------)[![Jake Teo](../Images/9687f43822fab69befb750a8ec58516d.png)](https://medium.com/@teosiyang?source=post_page---byline--f02b424da124--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f02b424da124--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f02b424da124--------------------------------) [Jake Teo](https://medium.com/@teosiyang?source=post_page---byline--f02b424da124--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f02b424da124--------------------------------) ·8分钟阅读·2024年9月12日

--

![](../Images/a52a22e39707bd0217ce2e9ce9454883.png)

需要推理端点的MLOps工作流中的实例（由作者创建）。

在任何机器学习项目中，目标是训练一个可以供他人使用，以得出良好预测的模型。为了实现这一目标，需要提供模型进行推理。在这个工作流中，几个部分需要这个推理端点，即在模型评估时，模型在发布到开发、预发布环境，最后到生产环境供最终用户使用之前。

在本文中，我将演示如何使用AWS的SageMaker端点和其DJL镜像，部署最新的LLM和服务技术，即Llama和vLLM。这些组件是什么，它们如何组成推理端点？

![](../Images/c015c60e62b49d2c87dc9949dcd2dd07.png)

各个组件如何协同工作，服务于AWS中的模型。SageMaker端点是GPU实例，DJL是模板Docker镜像，vLLM是模型服务器（由作者创建）。

**SageMaker**是AWS提供的一项服务，包含一整套工具和服务，用于管理机器学习生命周期。其推理服务被称为SageMaker端点。在底层，它本质上是由AWS自主管理的虚拟机。

**DJL**（Deep Java Library）是由AWS开发的开源库，用于开发LLM推理Docker镜像，包括vLLM[2]。该镜像用于…
