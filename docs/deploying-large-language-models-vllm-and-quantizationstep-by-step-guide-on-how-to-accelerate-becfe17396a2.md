# 部署大语言模型：vLLM与量化

> 原文：[https://towardsdatascience.com/deploying-large-language-models-vllm-and-quantizationstep-by-step-guide-on-how-to-accelerate-becfe17396a2?source=collection_archive---------1-----------------------#2024-04-05](https://towardsdatascience.com/deploying-large-language-models-vllm-and-quantizationstep-by-step-guide-on-how-to-accelerate-becfe17396a2?source=collection_archive---------1-----------------------#2024-04-05)

## 大语言模型加速的逐步指南

[](https://olafenwaayoola.medium.com/?source=post_page---byline--becfe17396a2--------------------------------)[![Ayoola Olafenwa](../Images/86914a74cbf83e711887fab896e318a9.png)](https://olafenwaayoola.medium.com/?source=post_page---byline--becfe17396a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--becfe17396a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--becfe17396a2--------------------------------) [Ayoola Olafenwa](https://olafenwaayoola.medium.com/?source=post_page---byline--becfe17396a2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--becfe17396a2--------------------------------) ·9分钟阅读·2024年4月5日

--

![](../Images/399881e6f19fe28227d9982dd335ab20.png)

[来源](https://unsplash.com/photos/a-computer-chip-with-the-letter-a-on-top-of-it-eGGFZ5X2LnA)

## 大语言模型（LLMs）的部署

我们生活在一个令人惊叹的时代，大语言模型（如ChatGPT、GPT-4和Claude）可以执行多个惊人的任务。在教育、医疗、艺术和商业等几乎所有领域，大语言模型都被用来提高服务的效率。在过去的一年中，许多杰出的开源大语言模型（如Llama、Mistral、Falcon和Gemma）相继发布。这些开源LLM可以供所有人使用，但部署它们可能非常具有挑战性，因为它们可能非常慢，并且需要大量的GPU计算能力来实现实时部署。为简化大语言模型的部署，已创建了不同的工具和方法。

为了提供更快的推理速度，许多部署工具已被创建来服务大语言模型（LLMs），如vLLM、c2translate、TensorRT-LLM和llama.cpp。同时，量化技术也被用来优化GPU，以便加载非常大的语言模型。本文将解释如何通过vLLM和量化技术来部署大语言模型。

## **延迟与吞吐量**

影响大型语言模型速度性能的主要因素包括GPU硬件要求和模型大小。模型越大，运行所需的GPU计算能力就越强。常用的基准度量标准来衡量大型语言模型的速度性能包括***延迟***和***吞吐量***。

**延迟：** 这是大型语言模型生成响应所需的时间，通常以秒或毫秒为单位进行测量。

**吞吐量：** 这是指每秒或每毫秒由大型语言模型生成的token数量。

## 安装所需的包

以下是运行大型语言模型所需的两个包：Hugging Face ***transformers***和***accelerate***。

```py
pip3 install transformers
pip3 install accelerate
```

## 什么是Phi-2？

***Phi-2***是微软推出的一个最先进的基础模型，拥有27亿个参数。它通过多种数据源进行预训练，包括代码和教科书等内容。了解更多关于***Phi-2***的信息，点击[这里](https://huggingface.co/microsoft/phi-2)。

## 使用Hugging Face Transformers基准测试LLM的延迟和吞吐量

## 生成的输出

```py
Latency: 2.739394464492798 seconds
Throughput: 32.36171766303386 tokens/second
Generate a python code that accepts a list of numbers and returns the sum. [1, 2, 3, 4, 5]
A: def sum_list(numbers):
    total = 0
    for num in numbers:
        total += num
    return total

print(sum_list([1, 2, 3, 4, 5]))
```

**逐步代码解析**

**第6–10行：** 加载了***Phi-2***模型并对提示“***生成一个接受数字列表并返回其和的Python代码***”进行了分词。

**第12-18行：** 从模型生成了一个响应，并通过计算生成响应所需的时间获得了***延迟***。

**第21-23行：** 获取了生成响应的token总长度，将其除以***延迟***并计算出***吞吐量***。

该模型在A1000（16GB GPU）上运行，达到了***延迟***为***2.7秒***，吞吐量为***32 tokens/秒***。

# 使用vLLM部署大型语言模型

vLLM是一个开源的LLM库，用于以低***延迟***和高***吞吐量***提供大型语言模型服务。

## vLLM的工作原理

Transformer是大型语言模型的构建模块。Transformer网络使用一种叫做***注意力机制***的机制，网络通过它来研究和理解单词的上下文。***注意力机制***由一系列数学计算矩阵组成，这些矩阵被称为注意力键和值。注意力键和值的交互所使用的内存影响着模型的速度。vLLM引入了一种新的注意力机制——***分页注意力(PagedAttention)***，它在生成token的过程中高效地管理了Transformer的注意力键和值的内存分配。vLLM的内存效率在低延迟和高吞吐量下运行大型语言模型时证明非常有用。

这是vLLM工作原理的高层次解释。要了解更多深入的技术细节，请访问vLLM文档。

[](https://blog.vllm.ai/2023/06/20/vllm.html?source=post_page-----becfe17396a2--------------------------------) [## vLLM：使用分页注意力机制，轻松、快速且廉价地提供大型语言模型服务

### GitHub | 文档 | 论文

[blog.vllm.ai](https://blog.vllm.ai/2023/06/20/vllm.html?source=post_page-----becfe17396a2--------------------------------)

**安装vLLM**

```py
pip3 install vllm==0.3.3
```

## **运行Phi-2与vLLM**

## 生成输出

```py
Latency: 1.218436622619629seconds
Throughput: 63.15334836428132tokens/second
 [1, 2, 3, 4, 5]
A: def sum_list(numbers):
    total = 0
    for num in numbers:
        total += num
    return total

numbers = [1, 2, 3, 4, 5]
print(sum_list(numbers))
```

**逐步代码解析**

**第1–3行：** 从vLLM导入了运行***Phi-2***所需的包。

**第5–8行：** 使用vLLM加载了***Phi-2***，定义了提示词并设置了运行模型的重要参数。

**第10–16行：** 使用***llm.generate***生成模型的响应，并计算***延迟***。

**第19–21行：** 获取从响应中生成的总token长度，将token的长度除以延迟以获得***吞吐量***。

**第23–24行：** 获取生成的文本。

我在同一个提示词下使用vLLM运行了***Phi-2***，***“生成一个接受数字列表并返回其和的Python代码。”*** 在相同的GPU（A1000，16GB GPU）上，vLLM的***延迟***为***1.2秒***，***吞吐量***为***63个token/秒***，而Hugging Face transformers的***延迟***为***2.85秒***，***吞吐量***为***32个token/秒***。使用vLLM运行大语言模型与使用Hugging Face得出的结果相同，但具有更低的延迟和更高的吞吐量。

**注意：** 我为vLLM获得的度量指标（延迟和吞吐量）是vLLM性能的估算基准。模型生成速度受多种因素影响，例如输入提示词的长度和GPU的大小。根据官方vLLM报告，在生产环境中使用像A100这样强大的GPU运行LLM模型，vLLM的**吞吐量比Hugging Face Transformers高出24倍**。

## 实时延迟和吞吐量基准测试

我计算Phi-2的延迟和吞吐量的方式是实验性的，我这样做是为了说明vLLM如何加速大语言模型的性能。在LLM的实际使用案例中，例如一个基于聊天的系统，模型在生成时会输出token，测量延迟和吞吐量要复杂得多。

基于聊天的系统依赖于流式输出token。一些主要的因素影响LLM的度量标准，如***首次token生成时间***（模型生成第一个token所需的时间）、***每个输出token的时间***（生成每个输出token所花费的时间）、***输入序列长度、预期输出、预期的总输出token数***以及***模型大小***。在基于聊天的系统中，延迟通常是***首次token生成时间***与***每个输出token的时间***乘以预期的总输出token数的组合。

输入序列长度越长，模型的响应速度越慢。一些实时运行 LLM 的方法包括将用户的输入请求或提示批处理，从而并发执行推理，这有助于提高吞吐量。一般来说，使用强大的 GPU 和高效的工具（如 vLLM）来提供 LLM 服务，可以提高实时的延迟和吞吐量。

**在 Google Colab 上运行 vLLM 部署**

[](https://colab.research.google.com/drive/171tVs8nndleyYHoFr1PVm6YRvYg6kBCx?usp=sharing&source=post_page-----becfe17396a2--------------------------------) [## Google Colaboratory

### 编辑描述

[colab.research.google.com](https://colab.research.google.com/drive/171tVs8nndleyYHoFr1PVm6YRvYg6kBCx?usp=sharing&source=post_page-----becfe17396a2--------------------------------)

# 大型语言模型的量化

量化是将机器学习模型从高精度转换为低精度的过程，通过将模型的权重压缩成更小的位数，通常是 ***8-bit*** 或 ***4-bit***。像 vLLM 这样的部署工具对于在低延迟和高吞吐量下提供大型语言模型推理服务非常有用。由于 ***Phi-2*** 是一个参数较小的 LLM，具有 ***27 亿参数***，因此我们能够方便地在 Google Colab 的 T4 GPU 上使用 Hugging Face 和 vLLM 运行它。然而，像 ***Mistral 7B*** 这样的 70 亿参数的模型无法在 Colab 上通过 Hugging Face 或 vLLM 运行。量化最适合管理大型语言模型的 GPU 硬件需求。当 GPU 资源有限且需要运行非常大的语言模型时，量化是将 LLM 加载到受限设备上的最佳方法。

## BitsandBytes

这是一个 Python 库，内置了自定义量化函数，用于将模型的权重缩小为更低的位数（***8-bit*** 和 ***4-bit***）。

**安装 BitsandBytes**

```py
pip3 install bitsandbytes
```

## ***Mistral 7B*** 模型的量化

***Mistral 7B*** 是 MistralAI 发布的一个拥有 70 亿参数的模型，是最先进的开源大型语言模型之一。我将逐步讲解如何使用不同的量化技术在 Google Colab 上的 T4 GPU 上运行 ***Mistral 7B***。

**使用 8-bit 精度进行量化**：这是将机器学习模型的权重转换为 8-bit 精度的过程。***BitsandBytes*** 已与 Hugging Face transformers 集成，以使用相同的 Hugging Face 代码加载语言模型，但进行了少量修改以实现量化。

**第 1 行：** 导入了运行模型所需的包，包括 ***BitsandBytesConfig*** 库。

**第 3-4 行：** 定义了量化配置，并将参数 ***load_in_8bit*** 设置为 true，以便以 ***8-bit*** 精度加载模型的权重。

**第7–9行：** 将量化配置传入加载模型的函数，设置参数***device_map***为***bitsandbytes***，以自动分配适当的GPU内存来加载模型。最后加载了分词器权重。

**4位精度量化**：这是将机器学习模型的权重转换为***4位***精度。

以4位精度加载***Mistral 7B***的代码与***8位***精度的代码类似，除了少数几处更改：

+   将***load_in_8bit***改为***load_in_4bit***。

+   新增了一个参数***bnb_4bit_compute_dtype***，它被引入到***BitsandBytesConfig***中，用于以***bfloat16***执行模型的计算。***bfloat16***是用于加载模型权重以加速推理的计算数据类型。它可以同时与**4位**和***8位***精度一起使用。如果是***8位***精度，您只需将参数从***bnb_4bit_compute_dtype***更改为***bnb_8bit_compute_dtype***。

## NF4（4位标准浮动）和**双重量化**

来自QLoRA的**NF4（4位标准浮动）**是一种最优的量化方法，比标准的4位量化产生更好的结果。它与双重量化相结合，量化过程发生两次；第一次量化的量化权重传递到下一阶段的量化，产生模型权重的最佳浮动范围值。根据QLoRA论文的报告，***NF4与双重量化***不会导致准确率下降。阅读更多关于NF4和双重量化的深入技术细节，请参考QLoRA论文：

[](https://arxiv.org/abs/2305.14314?source=post_page-----becfe17396a2--------------------------------) [## QLoRA：高效的量化LLM微调

### 我们提出了QLoRA，一种高效的微调方法，能够显著减少内存使用，从而能够微调一个65B参数的模型……

[arxiv.org](https://arxiv.org/abs/2305.14314?source=post_page-----becfe17396a2--------------------------------)

**第4–9行：** 设置了额外的参数，***BitsandBytesConfig：***

+   ***load_4bit：*** 以4位精度加载模型的设置为真。

+   ***bnb_4bit_quant_type：*** 量化类型设置为nf4。

+   ***bnb_4bit_use_double_quant：*** 双重量化设置为True。

+   ***bnb_4_bit_compute_dtype：*** 使用***bfloat16***计算数据类型，以加速推理。

**第11–13行：** 加载了模型的权重和分词器。

**模型量化的完整代码**

## 生成的输出

```py
<s> [INST] What is Natural Language Processing? [/INST] Natural Language Processing (NLP) is a subfield of artificial intelligence (AI) and
computer science that deals with the interaction between computers and human language. Its main objective is to read, decipher, 
understand, and make sense of the human language in a valuable way. It can be used for various tasks such as speech recognition, 
text-to-speech synthesis, sentiment analysis, machine translation, part-of-speech tagging, name entity recognition, 
summarization, and question-answering systems. NLP technology allows machines to recognize, understand,
 and respond to human language in a more natural and intuitive way, making interactions more accessible and efficient.</s>
```

量化是一种非常有效的方法，用于在小型GPU上优化大型语言模型的运行，并且可以应用于任何模型，如Llama 70B、Falcon 40B和mpt-30b。根据[LLM.int8论文](https://arxiv.org/abs/2208.07339)的报告，量化后，超大型语言模型在准确性下降方面的影响较小，远低于小型模型。量化最适用于超大型语言模型，对于小型模型而言，由于准确性的下降，效果并不好。

**在Google Colab上运行Mixtral 7B量化**

[](https://colab.research.google.com/drive/1aYPlWaHC4iy6DLFjR291iEMxFvgeM1ia?usp=sharing&source=post_page-----becfe17396a2--------------------------------) [## Google Colaboratory

### 编辑描述

[colab.research.google.com](https://colab.research.google.com/drive/1aYPlWaHC4iy6DLFjR291iEMxFvgeM1ia?usp=sharing&source=post_page-----becfe17396a2--------------------------------)

## 结论

在本文中，我提供了一个逐步的方法来衡量大型语言模型的速度性能，解释了vLLM是如何工作的，以及它如何被用来提升大型语言模型的延迟和吞吐量。最后，我解释了量化技术及其如何被用来在小规模GPU上加载大型语言模型。

**通过以下方式联系我：**

邮箱：[olafenwaayoola@gmail.com](https://mail.google.com/mail/u/0/#inbox)

LinkedIn: [https://www.linkedin.com/in/ayoola-olafenwa-003b901a9/](https://www.linkedin.com/in/ayoola-olafenwa-003b901a9/)

**参考文献**

[](https://blog.vllm.ai/2023/06/20/vllm.html?source=post_page-----becfe17396a2--------------------------------) [## vLLM：使用PagedAttention轻松、快速、低成本地提供LLM服务

### GitHub | 文档 | 论文

[blog.vllm.ai](https://blog.vllm.ai/2023/06/20/vllm.html?source=post_page-----becfe17396a2--------------------------------) [](https://huggingface.co/blog/4bit-transformers-bitsandbytes?source=post_page-----becfe17396a2--------------------------------) [## 使用bitsandbytes、4位量化和QLoRA使LLM更加易用

### 我们正在通过开源和开放科学的方式推进并普及人工智能。

[huggingface.co](https://huggingface.co/blog/4bit-transformers-bitsandbytes?source=post_page-----becfe17396a2--------------------------------) [](https://www.baseten.co/blog/understanding-performance-benchmarks-for-llm-inference/?source=post_page-----becfe17396a2--------------------------------) [## 理解LLM推理性能基准

### 本指南帮助你解读LLM性能指标，直接对比延迟、吞吐量和成本。

[www.baseten.co](https://www.baseten.co/blog/understanding-performance-benchmarks-for-llm-inference/?source=post_page-----becfe17396a2--------------------------------) [](https://www.tensorops.ai/post/what-are-quantized-llms?source=post_page-----becfe17396a2--------------------------------) [## 什么是量化的LLM？

### 发现量化LLMs的强大功能！了解模型量化如何减少模型大小，提升硬件使用效率，以及…

[www.tensorops.ai](https://www.tensorops.ai/post/what-are-quantized-llms?source=post_page-----becfe17396a2--------------------------------)
