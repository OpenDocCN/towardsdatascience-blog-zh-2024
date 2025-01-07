# 使用LLM2Vec将Llama 3转化为嵌入模型

> 原文：[https://towardsdatascience.com/turn-llama-3-into-an-embedding-model-with-llm2vec-8448005f99aa?source=collection_archive---------1-----------------------#2024-05-03](https://towardsdatascience.com/turn-llama-3-into-an-embedding-model-with-llm2vec-8448005f99aa?source=collection_archive---------1-----------------------#2024-05-03)

## 使用Llama 3进行生成和检索的RAG

[](https://medium.com/@bnjmn_marie?source=post_page---byline--8448005f99aa--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--8448005f99aa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8448005f99aa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8448005f99aa--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--8448005f99aa--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8448005f99aa--------------------------------) ·7分钟阅读·2024年5月3日

--

![](../Images/aa3e5f5e3c663da28873ad33454491ca.png)

使用DALL-E生成

嵌入模型是大语言模型（LLMs）中检索增强生成（RAG）的关键组成部分。它们对用户编写的知识库和查询进行编码。

使用为与LLM相同领域训练或微调的嵌入模型，可以显著提高RAG系统的效果。然而，找到或训练这样的嵌入模型通常是一个困难的任务，因为领域内的数据通常稀缺。

在本文中，我将展示如何使用LLM2Vec将LLM转化为文本嵌入模型。我们将看到如何用Llama 3来创建一个RAG系统，该系统只需Llama 3即可，不需要任何其他模型。

# LLM2Vec：你的LLM也是一个嵌入模型

本文介绍了LLM2Vec：

[LLM2Vec: 大型语言模型秘密强大的文本编码器](https://arxiv.org/pdf/2404.05961.pdf)

官方实现已在GitHub上发布：

+   [McGill-NLP/llm2vec](https://github.com/McGill-NLP/llm2vec)（MIT许可）

*LLM2Vec是如何工作的？*

LLMs是通过因果语言建模损失进行训练的。它们被训练用来预测给定一序列token后的下一个token。由于训练示例是完整的token序列，因此在序列中应用因果注意力掩码，这样当模型学习预测某个token时，序列中所有后续的token都会被掩盖，不会影响注意力计算。例如，如果我们有以下序列：

```py
The cat is sleeping in the kitchen <eos>
```

在训练过程中，将依次应用以下注意力掩码（在掩码下，我只展示未被掩盖的token）：

```py
1 0 0 0 0 0 0 0 0
The 

1 1 0 0 0 0 0 0
The cat

1 1 1 0 0 0 0 0
The cat is 

1 1 1 1 0 0 0 0
The cat is sleeping

1 1 1 1 1 0 0 0
The cat is sleeping in 

1 1 1 1 1 1 0 0
The cat is sleeping in the 

1 1 1 1 1 1 1 0
The cat is sleeping in the kitchen

1 1 1 1 1 1 1 1
The cat is sleeping in the kitchen <eos>
```

零值表示该token不会被用于注意力计算。

另一方面，文本嵌入模型被训练用来编码整个序列的标记，并且是双向的，即它们从左到右和从右到左编码一个序列。

LLM2Vec方法将LLM转换为嵌入模型的初步步骤包括将解码器-only LLMs中使用的因果注意力掩码替换为全1矩阵。这一改变使得序列中的每个标记都可以与所有其他标记交互，实质上将其转变为一个双向LLM。然而，解码器-only LLMs并没有被训练来编码未来的标记，因此，这一简单的修改会降低表示的质量。尽管如此，仍然可以训练模型有效利用其新的双向注意力能力。

他们建议采用一种新的掩码下一个标记预测（MNTP）目标。MNTP将下一个标记预测目标与BERT使用的掩码语言模型结合在一起。为了实现这一点，我们取一个任意的序列x = (x1, x2, . . . , xN)，掩码掉输入标记的一个子集，然后训练模型使用过去和未来的上下文来预测这些被掩码的标记。重要的是，当预测一个在位置i被掩码的标记时，损失是根据前一个位置i - 1的标记表示的logits计算的，而不是像BERT模型那样根据被掩码的位置本身计算。

将LLM转换为双向模型并随后进行MNTP训练，可以将任何解码器-only LLM转换为编码器。然而，这些步骤可能不足以解决序列表示的问题。实际上，像BERT这样的双向编码器通常还会在预训练中加入下一个句子预测任务，而LLM并未经过此类训练。LLM2Vec通过让模型对每个输入序列进行两次处理，每次使用不同随机选择的丢弃掩码，生成该序列的两个不同表示，来解决这一缺失的能力。训练目标是提高这两个表示之间的相似性，同时减少它们与同一训练批次中不同序列表示之间的相似性。他们将这最后一步称为“无监督对比学习”（SimCSE）。

LLM2Vec的完整过程通过论文中的这张图示说明：

![](../Images/8f30670da68e07a279a68d6d8c488f4d.png)

[source](https://arxiv.org/abs/2404.05961) (CC-BY)

他们评估了LLM2Vec生成的模型在各类任务中的表现，并显示它们可以超越标准的文本嵌入模型。你可以在论文的第3节和第4节找到相关结果。

# 使用LLM2Vec将Llama 3转换为文本嵌入模型

我的笔记本展示了如何将Llama 3转换为嵌入模型，可以在此查看：

[获取笔记本 (#65)](https://newsletter.kaitchup.com/p/notebooks)

将LLM转换为文本嵌入模型使用LLM2Vec是相当简单的。

首先，安装以下软件包：

```py
pip install llm2vec
pip install flash-attn --no-build-isolation
```

llm2vec包将会把LLM转换为嵌入模型。flash-attn是FlashAttention的包。虽然不是必需的，但它可以加速MNTP训练。它仅适用于安培架构（RTX 3xxx/4xxx、A100、H100等）的最新GPU。

然后，转换本身通过以下代码执行：

```py
import torch
from llm2vec import LLM2Vec

l2v = LLM2Vec.from_pretrained(
    "meta-llama/Meta-Llama-3-8B",
    device_map="cuda" if torch.cuda.is_available() else "cpu",
    torch_dtype=torch.bfloat16,
)

l2v.save("Llama-3-8B-Emb")
```

“torch_dtype=torch.bfloat16”是必要的，以便能在24GB的GPU上运行转换。如果不设置此参数，模型将比原始的float32参数模型大。

我已将该模型推送到中心，以防你不想自己进行此转换：

+   [kaitchup/Llama-3-8B-llm2vec-Emb](https://huggingface.co/kaitchup/Llama-3-8B-llm2vec-Emb)

这个模型已经可以使用了。你可以将其插入RAG流水线中。不过，它的性能通常不如标准嵌入模型（在大多数情况下）。

我们需要用MNTP目标训练Llama 3 8B。作者也提供了一个脚本来完成此操作：

+   [experiments/run_mntp.py](https://github.com/McGill-NLP/llm2vec/blob/main/experiments/run_mntp.py)

它目前支持Llama和Mistral架构的模型。

要使用它，请在本地克隆该仓库：

```py
git clone https://github.com/McGill-NLP/llm2vec.git
```

该脚本期望传入一个参数，即一个JSON格式的配置文件。它们在此处提供了几个示例：

+   [train_configs/mntp](https://github.com/McGill-NLP/llm2vec/tree/main/train_configs/mntp)

对于Llama 3 8B，我做了以下配置：

```py
JSON_CONFIG='''
{
    "model_name_or_path": "meta-llama/Meta-Llama-3-8B",
    "dataset_name": "wikitext",
    "dataset_config_name": "wikitext-103-raw-v1",
    "per_device_train_batch_size": 1,
    "per_device_eval_batch_size": 1,
    "gradient_accumulation_steps": 16,
    "do_train": true,
    "do_eval": true,
    "max_seq_length": 512,
    "mask_token_type": "blank",
    "data_collator_type": "all_mask",
    "mlm_probability": 0.8,
    "overwrite_output_dir": true,
    "output_dir": "Llama-3-8B-llm2vec-MNTP-Emb",
    "evaluation_strategy": "steps",
    "eval_steps": 100,
    "save_steps": 200,
    "stop_after_n_steps": 1000,
    "lora_r": 16,
    "gradient_checkpointing": true,
    "torch_dtype": "bfloat16",
    "attn_implementation": "flash_attention_2"
}
'''

with open("mntp_config.json", 'w') as f:
  f.write(JSON_CONFIG)
```

该脚本会以bfloat16参数加载模型。我将每个设备的批次大小设置为1，以便训练可以适应24GB的GPU。

然后，我们可以开始MNTP训练：

```py
python llm2vec/experiments/run_mntp.py mntp_config.json
```

使用24GB的GPU（例如Google Colab的L4）进行三轮训练需要4天时间。如果你等不及这么久，可能一轮就足够了。

在MNTP训练后，模型应能产生更好的结果，特别是在检索任务中。

对于最后一步，即SimCSE训练，作者尚未发布代码，但提到他们会发布。

# 设置Llama 3文本嵌入模型用于RAG

在上一部分创建的嵌入模型已准备好用于RAG流水线。例如，你可以通过[sentence-transformers](https://github.com/UKPLab/sentence-transformers)（Apache 2.0许可证）加载它。

```py
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("kaitchup/Llama-3-8B-llm2vec-Emb")
```

如果你使用[LlamaIndex](https://github.com/run-llama/llama_index)（MIT许可证），可以设置HuggingFaceEmbedding模型：

```py
Settings.embed_model = HuggingFaceEmbedding(model_name="kaitchup/Llama-3-8B-llm2vec-Emb", device='cpu')
```

我设置了device='cpu'，但使用CPU会使RAG系统运行变慢。你可以移除此参数以在GPU上运行。然而，请注意，模型是以全精度加载的，这使得它无法适应普通消费者的GPU。

在本教程中，我详细解释了如何使用LlamaIndex设置RAG系统：

[](https://newsletter.kaitchup.com/p/rag-for-mistral-7b-instruct-with?source=post_page-----8448005f99aa--------------------------------) [## RAG for Mistral 7B Instruct with LlamaIndex and Transformers

### 预算有限的RAG

newsletter.kaitchup.com](https://newsletter.kaitchup.com/p/rag-for-mistral-7b-instruct-with?source=post_page-----8448005f99aa--------------------------------)

# 结论

使用LLM2Vec，我们现在可以将LLM用作文本嵌入模型。转换过程简单且快速。使用同一个模型同时进行生成和检索在RAG系统中非常有吸引力，因为我们不需要寻找额外的嵌入模型。

然而，从LLM中提取的嵌入模型往往表现不如常规嵌入模型。LLM2Vec的作者提出了新的训练目标，MNTP和SimCSE，用于训练从LLM中提取的嵌入模型。根据作者的说法，尽管这种训练成本较高，但能显著提升嵌入模型的表现。

为了支持我的工作，考虑订阅我的新闻通讯，以获取更多关于AI最新进展的文章/教程：

[](https://newsletter.kaitchup.com/?source=post_page-----8448005f99aa--------------------------------) [## Kaitchup - 预算内的AI | 本杰明·马里 | Substack

### 每周教程、技巧和新闻，涉及在您的计算机上微调、运行和服务大型语言模型的内容。…

newsletter.kaitchup.com](https://newsletter.kaitchup.com/?source=post_page-----8448005f99aa--------------------------------)
