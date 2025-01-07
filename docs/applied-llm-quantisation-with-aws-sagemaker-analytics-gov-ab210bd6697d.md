# 应用 LLM 量化与 AWS Sagemaker | Analytics.gov

> 原文：[https://towardsdatascience.com/applied-llm-quantisation-with-aws-sagemaker-analytics-gov-ab210bd6697d?source=collection_archive---------3-----------------------#2024-06-07](https://towardsdatascience.com/applied-llm-quantisation-with-aws-sagemaker-analytics-gov-ab210bd6697d?source=collection_archive---------3-----------------------#2024-06-07)

## *以两倍的速度和五分之一的成本托管生产就绪的 LLM 端点。*

[](https://medium.com/@james-teo?source=post_page---byline--ab210bd6697d--------------------------------)[![James Teo](../Images/393a3137764cce6bcc760d8bc980e78c.png)](https://medium.com/@james-teo?source=post_page---byline--ab210bd6697d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ab210bd6697d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ab210bd6697d--------------------------------) [James Teo](https://medium.com/@james-teo?source=post_page---byline--ab210bd6697d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ab210bd6697d--------------------------------) ·阅读时间 16 分钟·2024年6月7日

--

![](../Images/39d0752a052e81ea9d506acbe5077570.png)

作者提供的图像，使用 AWS Sagemaker Jumpstart - Stable Diffusion XL 1.0（开源）生成

*声明：我是一名数据工程师，隶属于新加坡政府技术局（GovTech）数据科学与人工智能部（DSAID）。作为 Analytics.gov 的核心开发者之一，我与各个政府部门合作，为公共部门开发数据科学和 AI/ML 能力，造福社会。*

# 目录

1.  [前言](#270c)

1.  [为什么使用开源模型？](#a831)

1.  [托管开源 LLM 的障碍](#9f25)

1.  [什么是量化，它如何帮助？](#1db5)

1.  [AWS Sagemaker 端点是如何工作的？](#fc28)

1.  [在 AG Sagemaker 上托管量化模型](#4036)

1.  [基准测试](#87cd)

1.  [结论](#9809)

# 1\. 前言

*如果你还没有阅读我们之前的发布文章，可以在这里查阅！*

[](https://medium.com/dsaid-govtech/accelerating-machine-learning-and-ai-impact-with-mlops-on-analytics-gov-ada449f216b6?source=post_page-----ab210bd6697d--------------------------------) [## 通过 MLOps 加速 Analytics.gov 上机器学习与 AI 的影响

### Analytics.gov 简介

[medium.com](https://medium.com/dsaid-govtech/accelerating-machine-learning-and-ai-impact-with-mlops-on-analytics-gov-ada449f216b6?source=post_page-----ab210bd6697d--------------------------------) [](https://medium.com/dsaid-govtech/productionising-llms-and-ml-models-with-analytics-gov-moms-journey-into-ai-solution-deployment-bad4ceb12df2?source=post_page-----ab210bd6697d--------------------------------) [## 使用Analytics.gov将LLM和机器学习模型生产化：MOM在AI解决方案部署中的旅程

### 特别感谢本文的共同贡献者：MOM前沿部署团队（Barry Tng，Ethan Mak，Joel Koo），以及...

[medium.com](https://medium.com/dsaid-govtech/productionising-llms-and-ml-models-with-analytics-gov-moms-journey-into-ai-solution-deployment-bad4ceb12df2?source=post_page-----ab210bd6697d--------------------------------)

Analytics.gov (AG)是由新加坡GovTech的数据科学与人工智能部门（DSAID）开发的中央机器学习操作（MLOps）平台，它将机器学习和人工智能用例推向全政府（WOG）生产化。该平台托管于政府商业云（GCC）2.0，采用最佳实践的网络和安全配置，为所有数据科学和AI需求提供安全的环境。通过AG，政府官员可以直接从其政府发放的笔记本电脑访问计算资源、托管的AI服务及其他工具，而无需管理或开发新的基础设施，从而加速了全政府的AI/ML项目。

AG提供定制功能，利用AWS Sagemaker Endpoints提供的能力，为量化模型创建和管理生产就绪的推理端点。仅需几行代码，最终用户即可快速为量化模型设置自己的私有推理端点，将可能需要几天或几周的工作缩短为几分钟。这大大降低了整个政府机构使用GenAI的门槛，从而提高了效率和成本效益。

在本文中，我们将探讨AG如何使政府机构高效且具有成本效益地运行大语言模型（LLM）。我们的目标是揭开模型量化的神秘面纱，展示我们如何简化在AWS Sagemaker中托管量化开源LLM的过程，并提供基准测试以评估性能和成本效益的提升。

# 2\. 为什么要使用开源模型？

*如需深入了解开源LLM，请阅读Sau Sheong的相关文章！（注：此为Medium会员专享内容）*

[](https://sausheong.com/programming-with-ai-open-llms-28091f77a088?source=post_page-----ab210bd6697d--------------------------------) [## 使用AI编程——开源LLM

### 在LLM应用中使用开源LLM

[sausheong.com](https://sausheong.com/programming-with-ai-open-llms-28091f77a088?source=post_page-----ab210bd6697d--------------------------------)

*我强烈推荐它，因为它为将开源LLM作为API托管提供了很好的启示，是本文的重要补充。*

**安全性与敏感性**

开源模型可以在你自己的设备或云环境中私密托管，这意味着向模型发出的查询不会被发送到第三方提供商。这在政府数据中尤为重要，因为其中大部分包含敏感信息。

**控制输出生成**

开源模型的使用可以在更细粒度的层面上进行控制。封闭源模型必须通过公开的商业API进行接口连接，这种方式简化了复杂性，但减少了对模型的控制程度。本地托管的开源模型则允许对输出生成进行完全控制，这一点非常重要，因为许多有用的库，如[LMQL](https://lmql.ai/docs/models/openai.html#openai-api-limitations)和[Guidance](https://github.com/guidance-ai/guidance?tab=readme-ov-file#vertex-ai)，在本地托管模型上表现更好。

**多样性**

截至目前，HuggingFace上已有超过60万个模型，包括由Meta和Google等大厂发布的模型以及独立贡献者发布的自定义版本。有些版本针对特定目的或任务进行了微调，可以直接使用。用户可以简单地重用这些模型，而无需自己进行微调。

例如，[AiSingapore的SEA-LION](https://huggingface.co/aisingapore/sea-lion-7b)模型经过指令调优，专为东南亚（SEA）地区的语言设计，其训练数据集包含了从马来语到泰语等多种语言。使用此模型可以节省大量获取不同语言数据集的工作量，同时减少微调的计算成本。

# 3. 托管开源LLM的障碍

语言模型有多种形式和大小，流行的模型从TinyLlama（1.1B）到即将发布的Llama-3 400B+不等。像TinyLlama这样的较小语言模型（SLM）适用于较小且更简单的用例，而复杂的用例通常需要“更智能”的大型语言模型（LLM）。毫无疑问，所有生成AI应用都将受益于来自大型LLM的更好输出质量，然而，模型的体积越大，也意味着更多的权衡。

为了最大化推理速度，模型必须完全加载到GPU内存中，因为任何磁盘和GPU内存或CPU和GPU内存之间的数据传输都会引入额外的开销，从而显著降低推理速度。

LLM需要大量内存来托管，LLM越大，所需的GPU内存就越多。大多数大型模型需要多个GPU才能完全加载到内存中，这使得这一任务成为极其资源密集且昂贵的工作。

自然地，随着模型规模的增大，每次推理任务所需的计算量也随之增加。因此，LLM越大，推理速度就越低。

![](../Images/d20e7536584ab714bc1046d07f70ab2e.png)

*按作者分类的变换器BF16推理基准*

## **这些模型究竟有多大？**

这些大型语言模型（LLM）的大小可以通过以下公式进行估算（注意，这只是一个简单估算，实际的模型大小几乎总是略大于此估算值。）

![](../Images/90355390f5ae733ad064c1f1a40e2472.png)

*按作者分类的简化模型大小计算公式，灵感来源于* [*https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/*](https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/)

使用这个公式，我们可以估算一些流行模型的模型大小：

![](../Images/9ef3247db8e9f4a67f14bbb4dd1be710.png)

*按作者分类的流行模型的模型大小表*

*注意：该公式仅估算模型大小，实际的GPU需求肯定会更大，并且会因其他因素而有所不同。（正如你将在后续基准部分看到的，实际的GPU需求远远超出了这些估算值）。"BF16"代表脑浮点16数字格式，而"FP16"代表浮点16格式。*

即将发布的Meta的Llama-3 400B+将是发布时最大的开放源代码模型之一。我们可以估算，这个巨型模型可能会大到800GB。作为对比，800GB的存储至少需要10张A100 80GB的GPU卡来托管，即使我们天真地假设零托管开销。

另一个流行但尺寸更为合理的模型——Llama-3 70B，采用bf16或每权重16位（bpw）精度发布，仍然需要141.2GB的GPU内存来进行推理托管。

## **为什么大GPU内存需求是一个问题？**

由于当前GPU供不应求且需求高，找到便宜的多个GPU芯片并不容易。因此，托管LLM的原始未量化格式可能是一项非常昂贵的业务，只有少数能够负担得起的特权人群才能使用。这对于那些需要LLM智慧的项目来说可能是一个限制，但它的价值不足以让其值得使用多块稀缺且昂贵的GPU。

更大的LLM模型尺寸导致推理速度变慢，从而也会导致：

1.  由于输出缓慢，用户体验更差。

1.  下游应用程序能够提取的总吞吐量减少。对于像文本摘要或报告生成这样的重令牌应用程序，吞吐量的减少可能会严重影响应用程序的可行性。

缓慢的推理速度和高昂的成本是制约生产级应用的因素，因此每个生成式AI应用都需要在输出质量、推理速度和成本之间做出权衡。

# 4. 什么是量化，它如何提供帮助？

## **什么是量化？**

*关于量化的更严谨解释，请参考以下两篇精彩的指南：* [*https://www.tensorops.ai/post/what-are-quantized-llms*](https://www.tensorops.ai/post/what-are-quantized-llms)*,* [*https://www.semianalysis.com/p/neural-network-quantization-and-number*](https://www.semianalysis.com/p/neural-network-quantization-and-number)

*为了简化，以下部分将仅讨论训练后量化（PTQ）*

简单来说，在 AI/ML 领域，量化是一种减少模型大小的技术。在内部，模型的权重作为数字存储。通常，这些权重以类似浮动点 16（FP16）或脑浮动点 16（BF16）这样的数字格式存储，顾名思义，这些格式需要 16 位来存储一个数字。

量化减少了存储每个数字所需的比特数，这使得模型的存储大小得以减少，因为每个模型权重所使用的比特数更少。

然而，使用更少的比特数表示每个权重意味着权重的精度降低。这就是为什么大多数文章都恰当地将量化描述为“减少模型权重的精度”。

对于视觉学习者，这里是 **π** 在不同精度下的表示：

![](../Images/89bce72766d2c9c1a3fdddc9d2dd25dc.png)

*作者在不同精度下表示的**π***

你可以使用这个 [*浮动点计算器*](https://observablehq.com/@benaubin/floating-point) 亲自尝试一下。

注意：现代量化方法可能使用定制的数字格式，而非 FP 系列来对模型进行量化。这些方法可以将量化精度降低到 1 位（Q1）。

如表中所示，随着比特数的减少，**π** 的精度也降低。这不仅影响小数位数，还会影响数字本身的近似值。

例如，3.141592502593994 不能在 FP8 中精确表示，因此它必须四舍五入到 FP8 能表示的最接近值——3.125，这也被称为浮动点误差。

## **它有什么帮助？**

随着每个权重所需比特数的减少，总的 GPU 内存需求也会减少。例如，将 FP16 转换为 8 位量化（Q8）可以将每个数字存储所需的比特数从 16 位减少到 8 位。这会使模型的大小减少 50%。

举个例子，一个未经量化的 FP16 Mistral 7B 估计大小约为 14.48 GB，而一个 Q8 Mistral 7B 仅为 7.24 GB。一个 Q4 Mistral 7B 仅为 3.62 GB，这使得它可以加载到一些移动设备中。

除了减少内存外，减少的内存需求还降低了托管模型所需的最低计算要求，同时提高了推理速度。

![](../Images/908427595f63835aba24552068d2ac8a.png)

*作者在不同量化下的 7B 模型基准测试*

## **有什么问题吗？**

当然，世界上没有免费的午餐！精度的降低会影响模型输出的质量。参考我们之前的表格“**π**的表示”，一个以 FP16 表示的 **π** 可能足够准确以通过数学考试，但一个 FP8 的 **π** 会让你得 F。

幸运的是，大多数LLM对较高精度的量化不太敏感。一般来说，8位量化或Q8模型几乎与原始模型相同。这一点在以下基准中有所体现，来自“[*低位量化LLAMA3模型效果如何？一项实证研究*](https://arxiv.org/pdf/2404.14047)”。

![](../Images/7da740454cc39e235647f639308254d8.png)

*提取的8位量化Llama-3与基准的对比表，来源：* [*https://arxiv.org/pdf/2404.14047*](https://arxiv.org/pdf/2404.14047)*.*

简而言之，这意味着通过将模型权重量化为Q8，您几乎可以**在几乎不损失的情况下将模型大小减少50%**。

![](../Images/44f12ec6af5c6a5efed577e63df93413.png)

*提取的4位量化Llama-3与基准的对比表，来源：* [*https://arxiv.org/pdf/2404.14047*](https://arxiv.org/pdf/2404.14047)*.*

对于模型大小减少75%，即Q4，使用更智能的量化技术（如AWQ）时，模型依然可以接受，尽管会有明显的质量损失。

![](../Images/7c0a10cc584a8a836c5f300ca8f506d4.png)

*提取的3位量化Llama-3与基准的对比表，来源：* [*https://arxiv.org/pdf/2404.14047*](https://arxiv.org/pdf/2404.14047)*.*

如果低于Q4，模型输出质量可能会严重下降。

请注意，量化对模型质量的影响可能因模型而异。确定最佳量化级别的最好方法，实际上是基于您自己的使用情况和测试。

## **选择哪个量化框架？**

*有关选择量化框架的更严谨讨论，请参见：* [*https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/*](https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/) *,* [*https://www.reddit.com/r/LocalLLaMA/comments/1anb2fz/guide_to_choosing_quants_and_engines/*](https://www.reddit.com/r/LocalLLaMA/comments/1anb2fz/guide_to_choosing_quants_and_engines/)

有许多量化框架可供选择，其中一些更流行的包括GGUF、GPTQ、EXL2和AWQ。最适合您的量化框架将取决于您的使用场景。以下是我根据个人使用经验给出的推荐。最适合您的选择将取决于您的使用场景，实际效果可能因人而异。

**GGUF**

由[Georgi Gerganov](https://github.com/ggerganov/llama.cpp)创建，GGUF旨在通过最小的设置和在任何硬件上实现最先进的LLM推理，无论是本地还是云端，成为AI/ML爱好者的必备工具，由于其易用性，GGUF已经成为许多LLM托管的首选。

如果你需要在普通硬件或仅有CPU的系统上托管模型，那么GGUF是最合适的选择，因为它是唯一支持CPU托管的框架。GGUF还允许你在旧款GPU上运行较新的模型。GGUF由于将模型权重打包为一个统一格式的单个文件，因此也是最稳定的框架。如果你需要在任何机器上可靠托管量化模型，比如你的笔记本电脑，那么GGUF是最好的选择。

GGUF的一个缺点是，旧版本的量化（Qx_0）使用的是较简单的量化方法，如四舍五入量化（RTN）。这可能会在一定程度上降低模型输出质量，但在较高的量化级别下影响较小。GGUF中的新量化方法（Qx_K或IQx_S）在较低的量化级别下能更好地保持模型质量。

**GPTQ、EXL2和AWQ**

GPTQ、EXL2和AWQ专为GPU使用而设计，它们都基于GPTQ格式。这些框架在GPU上运行时通常比GGUF更快，因为它们专门优化了GPU的运行性能。EXL2允许在模型内混合量化级别。AWQ则倾向于提供最佳的输出质量，因为它使用比GPTQ更“智能”的量化技术。EXL2和AWQ都致力于在较低量化级别时减少性能下降。GPTQ通常是下游推理引擎支持最广泛的格式。

总结来说，选择GGUF可以方便托管，EXL2适合混合量化级别，AWQ则适用于输出质量，而如果推理引擎不支持其他格式，可以选择GPTQ。

# 5\. AWS Sagemaker端点是如何工作的？

现在我们了解了量化是什么，那么如何将其引入到AG的AWS Sagemaker中，让用户能够为他们的用例托管自己的生产就绪模型推理端点呢？

## **什么是Sagemaker端点？**

AWS Sagemaker端点是AWS Sagemaker中的原生工具，用于托管模型推理。它的优势包括：

1.  **易于配置自动扩展**：只需几行代码即可将自动扩展添加到现有端点。

1.  **零停机更新**：Sagemaker端点的更新默认使用BlueGreen部署。

1.  **灵活性与自定义**：Sagemaker端点可以使用自定义容器。

1.  **访问AWS服务**：Sagemaker端点能够访问AWS服务，比如S3，这可以为处理推理请求时增加额外的步骤提供更大的灵活性。

这有助于节省用户的时间和专业知识，尤其是那些只想部署模型而不希望考虑在生产规模上管理它所需工程工作的用户，将原本可能需要数天/数周的工作转化为几分钟的工作。

## **Sagemaker端点如何工作？**

在背后，Sagemaker Endpoints使用基于[Sagemaker-Inference-Toolkit](https://github.com/aws/sagemaker-inference-toolkit)库的特殊推理容器来托管模型API。这些容器提供了一种快速简便的方法来运行推理，无需构建自己的容器镜像，并且支持许多不同的框架，从使用scikit-learn容器的简单scikit-learn模型，到使用[TensorRT-LLM](https://github.com/aws/deep-learning-containers/blob/master/available_images.md)容器的复杂LLM（以及它们的AWQ/GPTQ量化变体）。

然而，GGUF和EXL2量化仍然需要重度定制的推理框架。幸运的是，Sagemaker提供了使用自定义容器的灵活性，并且Sagemaker Endpoints使这一过程变得非常简单。只需记住几个细节即可使其工作：

1.  容器必须监听8080端口。

1.  容器必须响应/ping和/invocations

1.  容器将通过*‘docker run <image> serve’*命令运行，容器预计将使用ENTRYPOINT而不是CMD

1.  通过指定包含模型工件的tar.gz文件的S3路径，将模型工件引入‘/opt/ml/model’目录。这发生在容器运行时之前。

![](../Images/8fdb800e571624302a550ec307f56da5.png)

由作者提供的自定义Sagemaker容器要求的视觉表示，灵感来源于[https://docs.aws.amazon.com/sagemaker/latest/dg/your-algorithms-inference-code.html](https://docs.aws.amazon.com/sagemaker/latest/dg/your-algorithms-inference-code.html)

## **为开源推理引擎定制**

上图表示一个预打包了Sagemaker-Inference-Toolkit的容器。为了使用我们自己的推理引擎，我们可以简单地将预打包的包替换为我们自己的自定义包。

例如，我们策划的一个自定义容器使用户能够通过使用Abetlen的[Llama-cpp-python](https://github.com/abetlen/llama-cpp-python)作为推理引擎来托管GGUF模型。这个库是开源的，并且遵循宽松的MIT许可证。

在我们的dockerfile中，我们只需要写几行代码以符合sagemaker端点的要求：

1.  将监听端口更改为8080

1.  为/ping和/invocations添加路由

1.  在ENTRYPOINT中运行

# 6\. 在AG Sagemaker中托管量化模型

使用自定义容器，在AG的Sagemaker环境中托管量化LLM仅需几行代码。

```py
# Code will vary depending on how you have curated your own custom container.

from sagemaker.model import Model

endpoint_name = "<Name of endpoint>"
image_uri = "<ECR Image URI to Llama-cpp-python Image>"
model_artifact_location = "<S3 Path to Model Artifacts>"
model = "<Path to model file>"

# All other ENV variables defined in documentation
model_endpoint = Model(
  image_uri = image_uri,
  model_data = model_artifact_location,
  role = role,
  env = {
    "MODEL": model_file_path_in_container,
    "N_GPU_LAYERS": "999",
    "INVOCATIONS_ROUTE": "/v1/completions"
  }
)

model_endpoint.deploy(
  initial_instance_count=1,
  instance_type="ml.g4dn.xlarge",
  endpoint_name=endpoint_name
)
```

就是这样，简短而简单。有了这个，我们的用户可以专注于开发他们的LLM使用案例，而不被幕后复杂的工作所困扰。

# 7\. 基准测试

以下是基于单次查询推理的每秒生成的平均令牌数基准，测试了 5 次，共计 30 个提示，即每个候选者基于 150 次测试的平均值。所有测试中，我们使用了 CodeLlama 模型，因为它有多种大小可用，即 7、13、34 和 70 亿个参数。我们测试了量化和未量化的模型，使用 Transformers 作为基准，因为它通常是运行未量化模型的常见方式。

以下是基准测试的规格：

![](../Images/7347e3a11bf2fdb6cbcfc801cedd13b8.png)

*基准规格，作者提供*

*请注意，ExllamaV2 指的是推理引擎，而 EXL2 是 ExllamaV2 的本地量化格式，在这种情况下，ExllamaV2 也支持 GPTQ 的推理。ExllamaV2 将仅使用 Q4_0 进行基准测试，因为一些 Q8_0 量化在 HuggingFace 上找不到。*

## **通过 Transformers（基准）的未量化**

**BF16:**

![](../Images/9cad550d302275d1fb63d80b3eda99ae.png)

*Transformers BF16 推理基准，作者提供*

以下测试中的所有倍数都是基于使用 Transformers 作为基准。例如，GPTQ 7b Q4_0 模型在“每秒令牌”列中有一个“(3.42x)”的倍数，这意味着 GPTQ 在 7b 模型上比 Transformers 基准快 3.42 倍。

## **通过 Llama-cpp-python 的 GGUF**

*GGUF 可以支持在较旧的 Nvidia T4 设备上托管，来自 g4dn 实例系列，因此我们增加了额外的测试，优化成本时尽可能使用 g4dn 实例类型：*

Q4_0

![](../Images/4fedaab4ea800417fe750538a56b471e.png)

*GGUF Q4_0 推理（最小化成本）基准，作者提供*

Q8_0

![](../Images/b85997ee93e5411a97f96efccde3f9ba.png)

*GGUF Q8_0 推理（最小化成本）基准，作者提供*

*使用较新的 Nvidia A10g 来自 g5 实例系列：*

Q4_0

![](../Images/54d8f6e23d74914cd6672b0751417cd7.png)

*GGUF Q4_0 推理基准，作者提供*

Q8_0

![](../Images/14d5790a9458f501defae9e52bfcdad9.png)

*GGUF Q8_0 推理基准，作者提供*

在每一个案例中，GGUF 都能以更低的成本或相同的价格运行模型，但速度显著更快。例如，Q8 13B 模型比基准快 74%，但成本仅为基准的五分之一！

## **GPTQ — 通过 ExllamaV2**

*ExllamaV2 仅支持在较新的 Nvidia A10g 上托管，来自 g5 实例系列，而不支持 g4dn 实例系列。*

Q4_0

![](../Images/9559a0e2f5026a85a7dc9c5bf48cf8f4.png)

*GPTQ Q4_0 推理基准，作者提供*

GPTQ 在 ExllamaV2 上将性能提升带到了全新的水平，对于每个量化的模型大小，Q4_0 在速度上超过了基准的**三倍多**。

## **AWS Sagemaker Jumpstart**

AWS本身也提供一种名为JumpStart的服务，允许通过几次点击部署预训练模型。这些AWS Sagemaker容器实现了Sagemaker推理工具包，并预装了多种推理引擎。在这种情况下，使用的是HuggingFace的文本生成推理（TGI）框架作为推理引擎。

BF16：

![](../Images/c021db0da5c3b01e942d9a59c822e6e3.png)

*AWS Jumpstart TGI BF16推理基准测试，作者*

请注意，13B比7B更快。这是因为TGI容器能够利用更多的GPU内存来提高推理速度。在像34B和70B这样更大的参数规模上，使用AWS Sagemaker Jumpstart与TGI容器，甚至可以超越ExllamaV2上的GPTQ。

# 8. 结论

量化为LLM提供了显著的好处，因为它减少了托管所需的内存。内存需求的减少提高了推理速度并降低了成本。通过较高位数的量化，可以几乎零损失地提高输出质量，显著提高速度并降低成本——本质上是对使用未量化LLM的帕累托改进。

在AWS Sagemaker Endpoints基础上，AG提供的辅助功能使得整个公共部门的机构能够轻松访问创建和管理生产就绪的量化开放LLM API的能力。通过简化部署量化大语言模型的过程，AG大大降低了生成高效且成本效益高的GenAI应用程序的门槛，使政府机构能够专注于创新和开发对公共利益有益的技术。

与此相辅相成的是，AG将继续推进其GenAI事业，通过安全的跨云集成，提供访问闭源模型（如Azure OpenAI和VertexAI的Gemini），同时与我们现有的AWS Bedrock服务结合。通过强大而全面的产品，AG使用户能够根据其用例优化模型，从而在公共部门实现更好、更快和更便宜的GenAI应用。

# 参考文献

[1] Sau Sheong，《与AI编程 — 开放LLM》（2024），[https://sausheong.com/programming-with-ai-open-llms-28091f77a088](https://sausheong.com/programming-with-ai-open-llms-28091f77a088)

[2] S. Stoelinga，《为LLM提供服务时计算GPU内存》（2023），[*https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/*](https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/)

[3] M.C. Neves，《什么是量化LLM？》（2023），[*https://www.tensorops.ai/post/what-are-quantized-llms*](https://www.tensorops.ai/post/what-are-quantized-llms)

[4] D. Patel，《神经网络量化与数值格式从基础原理出发》（2024），[https://www.semianalysis.com/p/neural-network-quantization-and-number](https://www.semianalysis.com/p/neural-network-quantization-and-number)

[5] W. Huang，《低比特量化LLAMA3模型有多好？一项实证研究》（2024），[https://arxiv.org/pdf/2404.14047](https://arxiv.org/pdf/2404.14047)

[6] Oobabooga，《GPTQ、AWQ、EXL2、q4_K_M、q4_K_S 和 load_in_4bit 的详细比较：困惑度、VRAM、速度、模型大小和加载时间》（N.A.），[*https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/*](https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/)

[7] Sgsdxzy，《选择量化和引擎指南》（2024），[*https://www.reddit.com/r/LocalLLaMA/comments/1anb2fz/guide_to_choosing_quants_and_engines/*](https://www.reddit.com/r/LocalLLaMA/comments/1anb2fz/guide_to_choosing_quants_and_engines/)

[8] 亚马逊网络服务，《使用您自己的推理代码与托管服务》（N.A.），[https://docs.aws.amazon.com/sagemaker/latest/dg/your-algorithms-inference-code.html](https://docs.aws.amazon.com/sagemaker/latest/dg/your-algorithms-inference-code.html)
