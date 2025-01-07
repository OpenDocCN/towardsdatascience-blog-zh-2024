# 在单个 GPU 上运行 SOTA 7B 参数嵌入模型

> 原文：[https://towardsdatascience.com/running-a-sota-7b-parameter-embedding-model-on-a-single-gpu-bb9b071e2238?source=collection_archive---------5-----------------------#2024-08-09](https://towardsdatascience.com/running-a-sota-7b-parameter-embedding-model-on-a-single-gpu-bb9b071e2238?source=collection_archive---------5-----------------------#2024-08-09)

## 在 SageMaker 上运行 Qwen2

[](https://medium.com/@paluchasz?source=post_page---byline--bb9b071e2238--------------------------------)[![Szymon Palucha](../Images/37a33166ccb5b427d8aaf545e3376d59.png)](https://medium.com/@paluchasz?source=post_page---byline--bb9b071e2238--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bb9b071e2238--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bb9b071e2238--------------------------------) [Szymon Palucha](https://medium.com/@paluchasz?source=post_page---byline--bb9b071e2238--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb9b071e2238--------------------------------) ·16 分钟阅读·2024年8月9日

--

在这篇文章中，我将解释如何在单个 24GB GPU 上运行一个最先进的 7B 参数 LLM 基于的嵌入模型。我将首先讲解一些理论，然后展示如何用 HuggingFace Transformers 库在 Python 中仅用几行代码运行它！

我们将在[Qwen2](https://arxiv.org/abs/2407.10671)开源模型（[Alibaba-NLP/gte-Qwen2–7B-instruct](https://huggingface.co/Alibaba-NLP/gte-Qwen2-7B-instruct)）上运行该模型，该模型于 2024 年 6 月发布，截止本文完成时，它在 HuggingFace 上的[Massive Text Embeddings Benchmark](https://huggingface.co/spaces/mteb/leaderboard)中排名第四。

![](../Images/ae9c63c85dcebf04f2477d219fbc7493.png)

2024 年 7 月在 HuggingFace 上的[MTEB 排行榜](https://huggingface.co/spaces/mteb/leaderboard)截图。该模型在 2024 年 6 月排名第一，随后跌至第四名。这显示了当今 AI 发展速度的快速！

# 理论内存需求

## 加载模型

加载机器学习模型（例如 LLM 或嵌入模型）所需的内存可以通过其参数数量来计算。

例如，一个 7B 参数模型的 fp32（浮点32精度），意味着我们需要以 32 位精度存储 7B 个数字，以便在内存中初始化该模型并能够使用它。因此，回忆一下每个字节有 8 位，加载该模型所需的内存是

> 一个 7B 参数模型的 fp32 内存 = 7B * 32 位 = 7B * 32 / 8 字节 = 28B 字节 = **28GB**。

因此，为了运行这个模型，我们至少需要28GB的GPU内存。实际上，还有一些额外的开销，如[这篇文章](https://www.substratus.ai/blog/calculating-gpu-memory-for-llm)中所描述的那样。因此，要以全精度运行该模型，我们不能使用内存为16GB或24GB的小型且便宜的GPU。

所以没有更强大的GPU，如[NVIDIA的A100](https://www.nvidia.com/en-gb/data-center/a100/)，我们有哪些替代方案？事实证明，有几种不同的技术可以减少内存需求。最简单的一种方法是降低参数的精度。现在，大多数模型都可以在**半精度**下使用，而不会显著损失准确性。以fp16或[bf16](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format)加载模型所需的内存是

> 一个7B参数模型在fp16中的内存 = 7B * 16位 = 7B * 16 / 8字节 = 14B字节 = **14GB**。

虽然这对于在24GB GPU上加载模型已经足够好，但由于推理过程中额外的开销和要求，我们仍然难以在16GB的GPU上运行它。

如果继续降低精度，性能可能会受到影响，但有一种名为**量化**的技术，能够进一步减少精度（例如，降到8位或4位），而不会显著降低准确性。最近在LLM领域的研究甚至表明，使用1位精度（实际上是log_2(3) = 1.58位），即[1位LLM](https://arxiv.org/abs/2402.17764)的可能性。这些模型的参数只能取值1、-1或0！

要深入了解这些话题，我推荐阅读：

+   通过[以下教程](https://math.libretexts.org/Workbench/Numerical_Methods_with_Applications_(Kaw)/1%3A_Introduction/1.05%3A_Floating-Point_Binary_Representation_of_Numbers#:~:text=A%20machine%20stores%20floating%2Dpoint,the%20magnitude%20of%20the%20mantissa.)理解浮点数表示。

+   [Huggingface量化基础](https://www.deeplearning.ai/short-courses/quantization-fundamentals-with-hugging-face/) 由DeepLearning.AI提供的免费课程。

# 推理

上述计算仅告诉我们加载模型所需的内存量！除此之外，我们还需要额外的内存来实际通过模型运行一些输入。对于Qwen2嵌入模型和一般的LLM，额外的内存需求取决于上下文窗口的大小（即传入模型的文本长度）。

## 采用原始自注意力机制的旧模型

在现在广泛采用的[Flash Attention](https://arxiv.org/abs/2205.14135#)发布之前，许多较旧的语言模型使用的是Transformer架构中的原始自注意力机制。该机制需要额外的内存，并且该内存需求与输入序列的长度呈平方关系。为了说明为什么会这样，下面是一个很好的自注意力可视化示意图。

![](../Images/4a8c914370c08ec222bb4e23b4374895.png)

一个很好的自注意力机制的可视化示例。来源：Sebastian Raschka，[https://magazine.sebastianraschka.com/p/understanding-and-coding-self-attention](https://magazine.sebastianraschka.com/p/understanding-and-coding-self-attention)，经作者许可转载。

从图示中我们可以看到，除了在计算加载模型所需内存时已经考虑的模型权重（*Wq, Wk, Wv* 矩阵）外，还有许多额外的计算及其输出需要存储。例如，输入 X、*Q, K, V* 矩阵，以及 **注意力矩阵** *QK^T*。事实证明，随着输入序列长度 *n* 的增加，注意力矩阵成为额外内存需求中的主导因素。

为了理解这一点，我们可以进行一些简单的计算。例如，在原始的 Transformer 中，嵌入维度 *d* 为 512。因此，对于一个由 512 个词元组成的输入序列，输入 X 和注意力矩阵在 fp32 格式下各自需要额外的 1MB 内存。

> 512² 浮点数 = 512² * 32 位 = 512² * 4 字节 = 1MB

如果我们将输入序列长度增加到 8000 词元，额外的内存需求将是：

> 输入 X = 512 * 8000 * 4 字节 = 16MB；注意力矩阵 = 8000² * 4 字节 = 256MB。

如果我们将输入序列长度增加到 32k 词元，额外的内存需求将是：

> 输入 X = 512 * 32000 * 4 字节 = 65MB；注意力矩阵 = 32000² * 4 字节 = 4GB！

如你所见，随着上下文窗口的大小增加，所需的额外内存快速增长，并且很快就会被注意力矩阵中的 *n²* 个浮点数所主导！

上述计算仍然是简化版，因为实际上有更多的细节会导致更高的内存使用。例如，在原始的 Transformer 中，还涉及到多头注意力——即注意力计算是并行进行的，使用了多个不同的头（在原始实现中为 8 个）。因此，我们需要将所需内存乘以头的数量。类似地，上述计算是针对批量大小为 1 的情况，如果我们想一次处理多个不同的文本，可以增加批量大小，但这会增加额外的内存开销。有关不同内存需求的详细拆解，请参见以下[文章](https://huggingface.co/blog/mayank-mishra/padding-free-transformer)。

## 更近期的模型，如 Qwen2

自2017年Transformer发布以来，已经有大量研究提出替代的注意力机制，以避免n²瓶颈。然而，这些方法通常伴随着精度下降的折衷。2022年，一种精确的注意力机制发布，并针对GPU进行了优化，名为[Flash Attention](https://arxiv.org/abs/2205.14135)，并且已在LLMs中广泛应用。从那时起，已经出现了进一步的迭代版本，包括2024年7月发布的[Flash Attention 3](https://arxiv.org/abs/2407.08608)。对我们来说，最重要的收获是Flash Attention能够随着输入序列长度的增加线性扩展！

以下是一个理论推导，比较了不同序列长度和不同注意力机制下，20B参数模型的内存需求。`Padding-Free Transformer`是另一种优化方法，去除了[填充](https://huggingface.co/docs/transformers/en/pad_truncation)的需求——如果你的批次中有一长序列和多个短序列，这会非常有用。

![](../Images/a941c0fa93eea419f3b66db2022ce8ac.png)

不同注意力机制下20B参数模型的内存需求的理论估算。主要结论是二次与线性扩展。来源：Mayank Mishra，[在微调过程中使用Padding-Free Transformer层节省内存](https://huggingface.co/blog/mayank-mishra/padding-free-transformer)，已获作者许可转载。

Qwen2模型同时使用了Flash Attention和填充优化。现在，我们已经了解了理论，接下来让我们看看如何实际运行Qwen2模型！

# 使用HuggingFace Transformers运行7B Qwen2模型

## 设置

我们将要实验的模型是来自Transformers的`Alibaba-NLP/gte-Qwen2-7B-instruct`。模型卡可以在[这里](https://huggingface.co/Alibaba-NLP/gte-Qwen2-7B-instruct)查看。

为了进行这个实验，我使用了Python 3.10.8并安装了以下包：

```py
torch==2.3.0
transformers==4.41.2
xformers==0.0.26.post1
flash-attn @ https://github.com/Dao-AILab/flash-attention/releases/download/v2.5.8/flash_attn-2.5.8+cu122torch2.3cxx11abiFALSE-cp310-cp310-linux_x86_64.whl
accelerate==0.31.0
```

在安装运行此模型所需的`flash-attn`时，我遇到了一些[困难](https://github.com/Dao-AILab/flash-attention/issues/246)，因此必须安装上述特定版本。如果有人有更好的解决方法，请告诉我！

我在这个实验中使用的Amazon SageMaker实例是`ml.g5.2xlarge`。它配备有24GB的NVIDIA A10G GPU和32GB的CPU内存，费用为$1.69/小时。以下是AWS的截图，展示了实例的所有详细信息。

![](../Images/3a03efec36249f1333e76b131a3b3275.png)

来自[AWS文档](https://aws.amazon.com/sagemaker/pricing/)的SageMaker g5实例类型。

实际上，如果你运行`nvidia-smi`，你会看到该实例的GPU内存只有23GB，略低于广告所称的。此GPU的CUDA版本是12.2。

## 如何运行——详细步骤

如果你查看模型卡，其中一个建议的使用方式是通过`sentence-transformers`库，如下所示。

```py
from sentence_transformers import SentenceTransformer

# This will not run on our 24GB GPU!
model = SentenceTransformer("Alibaba-NLP/gte-Qwen2-7B-instruct", trust_remote_code=True)
embeddings = model.encode(list_of_examples)
```

Sentence-transformers是Transformers包的一个扩展，用于计算嵌入，非常有用，因为你只需要两行代码就可以让事情运行起来。缺点是你对如何加载模型的控制较少，因为它隐藏了标记化和池化的细节。上述代码**无法在我们的GPU实例上运行**，因为它尝试以完整的float32精度加载模型，这需要28GB的内存。当初始化句子变换器模型时，它会检查可用设备（GPU的cuda）并自动将Pytorch模型转移到设备上。结果，在加载了5/7的模型后，它卡住并崩溃了。

相反，我们需要在将模型移到GPU之前，能够以float16精度加载模型。因此，我们需要使用更底层的Transformers库。（我不确定是否有方法可以使用sentence-transformers实现，但如果有的话请告诉我！）我们按如下方式操作：

```py
import transformers
import torch

model_path = "Alibaba-NLP/gte-Qwen2-7B-instruct"
model = transformers.AutoModel.from_pretrained(model_path, trust_remote_code=True, torch_dtype=torch.float16).to("cuda")
```

使用`torch_dtype`参数，我们指定模型应立即以float16精度加载，从而仅需要14GB的内存。然后，我们需要将模型转移到GPU设备上，这可以通过`to`方法实现。使用上述代码，模型加载几乎需要2分钟！

由于我们使用的是`transformers`，我们需要单独加载标记化器来标记化输入文本，操作如下：

```py
tokenizer = transformers.AutoTokenizer.from_pretrained(model_path)
```

下一步是标记化输入文本，操作如下：

```py
texts = ["example text 1", "example text 2 of different length"]
max_length = 32768
batch_dict = tokenizer(texts, max_length=max_length, padding=True, truncation=True, return_tensors="pt").to(DEVICE)
```

Qwen2模型的最大长度为32678，但正如我们稍后将看到的，由于额外的内存需求，我们无法在24GB的GPU上运行如此长的序列。我建议将其减少到*不超过24,000*，以避免内存不足错误。填充确保批次中的所有输入具有相同的长度，而截断确保任何超过最大长度的输入将被截断。有关更多信息，请参见[文档](https://huggingface.co/docs/transformers/en/pad_truncation)。最后，我们确保返回的是PyTorch张量（默认情况下是列表），并将这些张量转移到GPU，以便可以传递给模型。

下一步是将输入通过我们的模型并进行池化。操作如下：

```py
with torch.no_grad():
    outputs = model(**batch_dict)
    embeddings = last_token_pool(outputs.last_hidden_state, batch_dict["attention_mask"])
```

使用`last_token_pool`，其形式如下：

```py
def last_token_pool(last_hidden_states: torch.Tensor, attention_mask: torch.Tensor) -> torch.Tensor:
    # checks whether there is any padding (where attention mask = 0 for a given text)
    no_padding = attention_mask[:, -1].sum() == attention_mask.shape[0]
    # if no padding - only would happen if batch size of 1 or all sequnces have the same length, then take the last tokens as the embeddings
    if no_padding:
        return last_hidden_states[:, -1]
    # otherwise use the last non padding token for each text in the batch
    sequence_lengths = attention_mask.sum(dim=1) - 1
    batch_size = last_hidden_states.shape[0]
    return last_hidden_states[torch.arange(batch_size, device=last_hidden_states.device), sequence_lengthsLet’s break down what happened in the above code snippets! 
```

+   `torch.no_grad()`上下文管理器用于禁用梯度计算，因为我们并不训练模型，因此可以加速推理过程。

+   然后，我们将标记化的输入传递到变换器模型中。

+   我们通过`last_hidden_state`属性从模型的最后一层获取输出。这是一个形状为*(batch_size, max_sequence_length, embedding dimension)*的张量。本质上，对于批次中的每个示例，变换器都会为序列中的所有标记输出嵌入。

+   我们现在需要一种方法将所有的标记嵌入合并为一个单一的嵌入，以表示输入文本。这称为`池化`，并且与训练模型时的池化方式相同。

+   在较旧的基于BERT的模型中，通常使用第一个标记（它代表特殊的分类[CLS]标记）。然而，Qwen2模型是基于LLM的，即基于变换器解码器。在解码器中，标记是自回归生成的（一个接一个），因此最后一个标记包含了关于句子的所有编码信息。

+   `last_token_pool`函数的目的是选择每个示例中最后一个生成的非填充标记的嵌入。

+   它使用了`attention_mask`，该掩码告诉模型在批次中每个示例哪些标记是填充标记（参见[文档](https://huggingface.co/docs/transformers/en/glossary)）。

## 注释示例

让我们看一个例子，稍微详细地了解一下。假设我们想在一个批次中嵌入两个示例：

```py
texts = ["example text 1", "example text 2 of different length"]
```

分词器的输出（`batch_dict`）将如下所示：

```py
>>> batch_dict
{'input_ids': tensor([[  8687,   1467,    220,     16, 151643, 151643, 151643],
        [  8687,   1467,    220,     17,    315,   2155,   3084]],
       device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 0, 0, 0],
        [1, 1, 1, 1, 1, 1, 1]], device='cuda:0')}
```

从中可以看出，第一句话被分成了四个标记（8687，1467，220，16），而第二句话被分成了七个标记。因此，第一句话会被填充（使用三个填充标记，id 为151643），填充到长度七 —— 这是该批次中的最大长度。注意力掩码反映了这一点 —— 它对第一个示例有三个零，表示填充标记的位置。两个张量的大小是相同的。

```py
>>> batch_dict.input_ids.shape
torch.Size([2, 7])
>>> batch_dict.attention_mask.shape
torch.Size([2, 7])
```

现在通过模型传递`batch_dict`，我们可以检索到模型的最后一个隐藏状态，其形状为：

```py
>>> outputs.last_hidden_state.shape
torch.Size([2, 7, 3584])
```

我们可以看到，这个形状是*(batch_size, max_sequence_length, embedding dimension)*。Qwen2的嵌入维度为3584！

现在我们进入`last_token_pool`函数。第一行检查是否存在填充，它通过对`attention_mask`的最后一列求和并与`batch_size`（由`attention_mask.shape[0]`给出）进行比较来实现。如果所有的注意力掩码中都存在1，即所有示例的长度相同，或者仅有一个示例时，这才会返回`True`。

```py
>>> attention_mask.shape[0]
2
>>> attention_mask[:, -1]
tensor([0, 1], device='cuda:0')
```

如果确实没有填充，我们只需简单地为每个示例选择最后一个标记的嵌入，方法是使用`last_hidden_states[:, -1]`。但是，由于有填充，我们需要选择每个批次中最后一个非填充标记的嵌入。为了选择这个嵌入，我们需要获取每个示例的索引。通过以下方式可以实现：

```py
>>> sequence_lengths = attention_mask.sum(dim=1) - 1
>>> sequence_lengths
tensor([3, 6], device='cuda:0')
```

所以现在我们只需要通过张量的正确索引，在前两个维度上进行索引。为了获取批次中所有示例的索引，我们可以使用`torch.arange`，如下所示：

```py
>>> torch.arange(batch_size, device=last_hidden_states.device)
tensor([0, 1], device='cuda:0')
```

然后我们可以使用这个和最后一个非填充标记的索引，从中提取每个示例的正确标记嵌入：

```py
>>> embeddings = last_hidden_states[torch.arange(batch_size, device=last_hidden_states.device), sequence_lengths]
>>> embeddings.shape
torch.Size([2, 3584])
```

然后我们得到了传入的两个示例的两个嵌入！

## 如何运行 — 简洁版

将完整的代码分成不同函数看起来像是：

```py
import numpy as np
import numpy.typing as npt
import torch
import transformers

DEVICE = torch.device("cuda")

def last_token_pool(last_hidden_states: torch.Tensor, attention_mask: torch.Tensor) -> torch.Tensor:
    # checks whether there is any padding (where attention mask = 0 for a given text)
    no_padding = attention_mask[:, -1].sum() == attention_mask.shape[0]
    # if no padding - only would happen if batch size of 1 or all sequnces have the same length, then take the last tokens as the embeddings
    if no_padding:
        return last_hidden_states[:, -1]
    # otherwise use the last non padding token for each text in the batch
    sequence_lengths = attention_mask.sum(dim=1) - 1
    batch_size = last_hidden_states.shape[0]
    return last_hidden_states[torch.arange(batch_size, device=last_hidden_states.device), sequence_lengths

def encode_with_qwen_model(
    model: transformers.PreTrainedModel,
    tokenizer: transformers.tokenization_utils.PreTrainedTokenizer | transformers.tokenization_utils_fast.PreTrainedTokenizerFast,
    texts: list[str],
    max_length: int = 32768,
) -> npt.NDArray[np.float16]:
    batch_dict = tokenizer(texts, max_length=max_length, padding=True, truncation=True, return_tensors="pt").to(DEVICE)

    with torch.no_grad():
        outputs = model(**batch_dict)
        embeddings = last_token_pool(outputs.last_hidden_state, batch_dict["attention_mask"])
    return embeddings.cpu().numpy()

def main() -> None:
    model_path = "Alibaba-NLP/gte-Qwen2-7B-instruct"
    tokenizer = transformers.AutoTokenizer.from_pretrained(model_path)
    model = transformers.AutoModel.from_pretrained(model_path, trust_remote_code=True, torch_dtype=torch.float16).to(DEVICE)
    print("Loaded tokeniser and model")

    texts_to_encode = ["example text 1", "example text 2 of different length"]
    embeddings = encode_with_qwen_model(model, tokenizer, texts_to_encode)
    print(embeddings.shape)

if __name__ == "__main__":
    main()
```

`encode_with_qwen_model` 返回一个 numpy 数组。为了将 PyTorch 张量转换为 numpy 数组，我们首先必须将其从 GPU 移回到 CPU，这可以通过 `cpu()` 方法实现。请注意，如果你打算处理较长的文本，应该将批处理大小减少为 1，每次只嵌入一个示例（即将 `texts_to_encode` 列表的长度缩减为 1）。

# 使用上下文长度进行的经验性内存使用测试

之前我们从理论角度看到了内存使用如何随输入文本大小变化。我们还可以实际测量 GPU 在嵌入不同长度的文本时实际使用了多少内存，并通过经验验证这种扩展性！这个想法来源于 HuggingFace 的一个很棒教程：[充分利用 LLM](https://huggingface.co/docs/transformers/v4.35.0/en/llm_tutorial_optimization)。

为了实现这一点，我们将利用一些额外的函数

```py
import gc

def flush() -> None:
    gc.collect()
    torch.cuda.empty_cache()
    torch.cuda.reset_peak_memory_stats()

def bytes_to_giga_bytes(bytes_: float) -> float:
    return bytes_ / 1024 / 1024 / 1024
```

以及

```py
torch.cuda.max_memory_allocated()
```

用于衡量 GPU 峰值使用情况的函数。`flush` 函数将在每次通过模型后清除并重置内存。我们将运行不同长度的文本，并输出峰值和有效 GPU 使用情况。有效 GPU 使用是从总使用量中减去模型大小使用量，给我们一个关于运行文本所需额外内存的概念。

我使用的完整代码如下：

```py
import gc

import numpy as np
import numpy.typing as npt
import torch
import transformers

DEVICE = torch.device("cuda")

def last_token_pool(last_hidden_states: torch.Tensor, attention_mask: torch.Tensor) -> torch.Tensor:
    # checks whether there is any padding (where attention mask = 0 for a given text)
    left_padding = attention_mask[:, -1].sum() == attention_mask.shape[0]
    # if no padding - only would happen if batch size of 1 or all sequences have the same length, then take the last tokens as the embeddings
    if left_padding:
        return last_hidden_states[:, -1]
    # otherwise use the last non padding token for each text in the batch
    sequence_lengths = attention_mask.sum(dim=1) - 1
    batch_size = last_hidden_states.shape[0]
    return last_hidden_states[torch.arange(batch_size, device=last_hidden_states.device), sequence_lengths]

def encode_with_qwen_model(
    model: transformers.PreTrainedModel,
    tokenizer: transformers.tokenization_utils.PreTrainedTokenizer | transformers.tokenization_utils_fast.PreTrainedTokenizerFast,
    texts: list[str] | str,
    max_length: int = 32768,
) -> npt.NDArray[np.float16]:
    batch_dict = tokenizer(texts, max_length=max_length, padding=True, truncation=True, return_tensors="pt").to(DEVICE)

    with torch.no_grad():
        outputs = model(**batch_dict)
        embeddings = last_token_pool(outputs.last_hidden_state, batch_dict["attention_mask"])
    return embeddings.cpu().numpy()

def flush() -> None:
    gc.collect()
    torch.cuda.empty_cache()
    torch.cuda.reset_peak_memory_stats()

def bytes_to_giga_bytes(bytes_: float) -> float:
    return bytes_ / 1024 / 1024 / 1024

def memory_usage_experiments(
    model: transformers.PreTrainedModel,
    tokenizer: transformers.tokenization_utils.PreTrainedTokenizer | transformers.tokenization_utils_fast.PreTrainedTokenizerFast,
) -> None:
    model_size = bytes_to_giga_bytes(torch.cuda.max_memory_allocated())
    print(f"Most gpu usage on model loaded: {model_size} GB\n")
    sentence = "This sentence should have minimum eight tokens. "
    all_texts = [sentence, sentence * 100, sentence * 1000, sentence * 2000, sentence * 3000, sentence * 4000]
    for texts in all_texts:
        batch_dict = tokenizer(texts, max_length=32768, padding=True, truncation=True, return_tensors="pt")
        encode_with_qwen_model(model, tokenizer, texts)
        max_mem = bytes_to_giga_bytes(torch.cuda.max_memory_allocated())
        print(f"Sequence length: {batch_dict.input_ids.shape[-1]}. Most gpu usage: {max_mem} GB. Effective usage: {max_mem - model_size} GB\n")
        flush()

def main() -> None:
    model_path = "Alibaba-NLP/gte-Qwen2-7B-instruct"
    tokenizer = transformers.AutoTokenizer.from_pretrained(model_path)
    model = transformers.AutoModel.from_pretrained(model_path, trust_remote_code=True, torch_dtype=torch.float16).to(DEVICE)
    print("Loaded tokeniser and model")

    memory_usage_experiments(model, tokenizer)

if __name__ == "__main__":
    main()
```

以及回溯信息是

```py
Most gpu usage on model loaded: 14.958292961120605 GB

Sequence length: 9\. Most gpu usage: 14.967926502227783 GB. Effective usage: 0.009633541107177734 GB

Sequence length: 801\. Most gpu usage: 15.11520528793335 GB. Effective usage: 0.15691232681274414 GB

Sequence length: 8001\. Most gpu usage: 16.45930576324463 GB. Effective usage: 1.5010128021240234 GB

Sequence length: 16001\. Most gpu usage: 17.944651126861572 GB. Effective usage: 2.986358165740967 GB

Sequence length: 24001\. Most gpu usage: 19.432421684265137 GB. Effective usage: 4.474128723144531 GB

torch.cuda.OutOfMemoryError: CUDA out of memory. Tried to allocate 1.13 GiB. GPU 
```

从这个结果我们可以看到，Qwen2 模型的确随着输入文本的大小线性扩展。例如，当我们将标记数从 8000 翻倍到 16000 时，有效内存使用量也大致翻倍。不幸的是，尝试运行长度为 32000 的序列时，出现了 CUDA OOM 错误，因此即使使用 24GB GPU 的浮点 16 精度，我们仍然无法充分利用模型的完整上下文窗口。

# 其他方面

## 在 fp32 精度下运行 Qwen2

要以全精度运行 Qwen2 模型，我们有两个选项。首先，我们可以访问更大的 GPU——例如 40GB 应该足够。然而，这可能会很昂贵。例如，Amazon SageMaker 就没有单个 40GB GPU 的实例，而是有一个包含 8 个 40GB GPU 的实例！但这并不实用，因为我们不需要其他 7 个 GPU 空闲等待。当然，我们也可以考虑其他提供商——现在有不少这样的提供商，且价格具有竞争力。

另一种选择是在具有多个较小 GPU 的实例上运行模型。模型可以被分割到不同的 GPU 上——即模型的不同层被放置在不同的 GPU 上，并且数据在推理过程中会在设备之间移动。要使用 HuggingFace 实现这一点，你可以使用

```py
model_path = "Alibaba-NLP/gte-Qwen2-7B-instruct"
model = transformers.AutoModel.from_pretrained(model_path, trust_remote_code=True, device_map="auto").to("cuda")
```

欲了解更多关于此如何工作的内容，请参阅以下[文档](https://huggingface.co/docs/accelerate/en/usage_guides/big_modeling)和[概念指南](https://huggingface.co/docs/accelerate/en/concept_guides/big_model_inference)。需要注意的是，这种方式非常慢——因为在不同GPU之间进行通信的开销，数据需要在各个设备之间移动以执行推理。此实现也没有经过优化，这意味着每个GPU的执行是按顺序进行的，而其他GPU处于空闲状态。如果你正在嵌入成千上万的文本或训练模型，理想情况下，你希望所有GPU都在不断工作。

## 在较小的GPU上运行Qwen2

为了在更小的GPU上运行此模型，你需要对模型进行量化。两个流行的选项是：

+   通过HuggingFace，它提供了多种方法可供选择（请参阅[文档](https://huggingface.co/docs/transformers/main/en/quantization/overview)）。

+   通过[vLLM](https://qwen.readthedocs.io/en/latest/deployment/vllm.html)包。

# 结论

总结：在本文中，我们展示了如何在单个24GB GPU上运行基于7B LLM的Qwen2嵌入模型。我们了解到，模型的大小是根据其参数数量计算的，并且为了将其加载到24GB GPU中，我们需要以float16精度加载模型。接着，我们发现实际上运行一个示例时需要额外的内存，这取决于上下文窗口的大小，并且根据所使用的底层注意力机制不同而有所变化。最后，我们展示了如何仅用几行代码，利用Transformers库完成这一切。
