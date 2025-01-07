# 训练 CausalLM 模型 第 1 部分：CausalLM 到底是什么？

> 原文：[https://towardsdatascience.com/training-causallm-models-part-1-what-actually-is-causallm-6c3efb2490ec?source=collection_archive---------4-----------------------#2024-03-04](https://towardsdatascience.com/training-causallm-models-part-1-what-actually-is-causallm-6c3efb2490ec?source=collection_archive---------4-----------------------#2024-03-04)

## 使用 HuggingFace 的 CausalLM 类的实用指南第一部分

[](https://tlebryk.medium.com/?source=post_page---byline--6c3efb2490ec--------------------------------)[![Theo Lebryk](../Images/c2e0d606f4a99831fad5575f59848544.png)](https://tlebryk.medium.com/?source=post_page---byline--6c3efb2490ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6c3efb2490ec--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6c3efb2490ec--------------------------------) [Theo Lebryk](https://tlebryk.medium.com/?source=post_page---byline--6c3efb2490ec--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6c3efb2490ec--------------------------------) ·6 分钟阅读·2024年3月4日

--

![](../Images/2a68f7d550af4571fd5c25099893c910.png)

因果语言模型将每个新词建模为所有先前词的函数。来源：[Pexels](https://www.pexels.com/photo/dog-eating-a-carrot-5255204/)

如果你曾在 [HuggingFace](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard) 上玩过最近的模型，那么你很可能遇到了因果语言模型。当你打开一个 [模型](https://huggingface.co/docs/transformers/main/en/model_doc/llama) 系列的文档时，你会看到一个页面，其中包含诸如 LlamaForCausalLM 或 LlamaForSequenceClassification 等“任务”。

如果你像我一样，从文档阅读到实际微调模型，可能会有些困惑。我们将专注于 CausalLM，从解释 CausalLM 是什么开始，并在接下来的文章中给出如何微调 CausalLM 模型的实际示例。

## 背景：编码器与解码器

目前许多最优秀的模型，如 LLAMA-2、GPT-2 或 Falcon，都是“仅解码器”模型。仅解码器模型：

1.  接受一系列*先前*的标记（即提示）

1.  将这些标记通过模型（通常是从标记生成嵌入并通过变换器块运行它们）

1.  输出单一结果（通常是下一个标记的概率）。

这与“仅编码器”或混合“编码器-解码器”架构的模型形成对比，这些模型将输入*整个*序列，而不仅仅是*前面的*标记。这一差异使得两种架构适用于不同的任务。解码器模型设计用于生成新的文本，而编码器模型设计用于需要查看整个序列的任务，如翻译或序列分类。事情变得复杂，因为你可以将一个仅解码器模型用于翻译，或者使用一个仅编码器模型生成新文本。如果你想深入了解编码器与解码器的区别，Sebastian Raschka 写了一篇很好的 [指南](https://magazine.sebastianraschka.com/p/understanding-encoder-and-decoder)，另外还有一篇 [medium 文章](/understanding-masked-language-models-mlm-and-causal-language-models-clm-in-nlp-194c15f56a5)，它更深入地探讨了掩码语言建模和因果语言建模之间的区别。

对于我们的目的，你只需要知道的是：

1.  CausalLM 模型通常是仅解码器模型

1.  仅解码器模型查看过去的标记来预测下一个标记

对于仅解码器的语言模型，我们可以将下一个标记的预测过程视为“因果语言建模”，因为前面的标记“导致”了每一个额外的标记。

## HuggingFace CausalLM

在 HuggingFace 的世界里，CausalLM（LM 代表语言建模）是一类模型，它接受一个提示并预测新的标记。实际上，我们是一次预测一个标记，但这个类抽象掉了需要一次一个标记遍历序列的繁琐过程。在推理过程中，CausalLM 会不断预测单个标记，直到满足某些停止条件，此时模型返回最终连接的标记。

在训练过程中，会发生类似的情况，我们给模型输入一串我们希望学习的标记序列。我们从给定第一个标记预测第二个标记开始，然后给定前两个标记预测第三个标记，依此类推。

因此，如果你想学习如何预测句子“the dog likes food”，假设每个单词是一个标记，你将进行 3 次预测：

1.  “the” → dog,

1.  “the dog” → likes

1.  “the dog likes” → food

在训练过程中，您可以将句子的三个快照视为训练数据集中的三条观察数据。手动将长序列拆分为每个标记的单独行会很繁琐，因此 HuggingFace 会为您处理这件事。

只要你给它一个标记序列，它会在后台将该序列分解成单个标记的预测。

你可以通过将一个常规字符串传递给模型的分词器来创建这个“标记序列”。分词器会输出一个类似字典的对象，包含 *input_ids* 和 *attention_mask* 作为键，就像任何普通的 HuggingFace 模型一样。

```py
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("bigscience/bloom-560m")
tokenizer("the dog likes food")
>>> {'input_ids': [5984, 35433, 114022, 17304], 'attention_mask': [1, 1, 1, 1]}
```

对于CausalLM模型，还多了一步，模型会期望有一个*labels*键。在训练过程中，我们使用“前一个”的*input_ids*来预测“当前”的*labels* token。然而，你**不**要把labels当作一个问答模型来看待，其中*labels*的第一个索引对应于*input_ids*的答案（也就是说，*labels*应该连接到*input_ids*的末尾）。相反，你希望*labels*和*input_ids*具有相同的形状，互为镜像。在代数符号中，为了预测索引k的*labels* token，我们会使用所有直到k-1的*input_ids*。

如果这让你感到困惑，实际上，你通常可以将*labels*直接复制为*input_ids*，就这么做即可。如果你想理解发生了什么，我们将通过一个例子来讲解。

## 一个快速的实例

让我们回到“the dog likes food”。为了简单起见，我们暂时将单词保留为单词，而不是将它们分配给token编号，但实际上这些应该是数字，你可以使用分词器将它们映射回它们的真实字符串表示。

我们的单元素批次输入如下所示：

```py
{
    "input_ids": [["the", "dog", "likes", "food"]],
    "attention_mask": [[1, 1, 1, 1]],
    "labels": [["the", "dog", "likes", "food"]],
}
```

双括号表示每个键的数组的形状技术上是batch_size x sequence_size。为了简化，我们可以忽略批处理，直接将它们视为一维向量。

在后台，如果模型正在预测序列中的第k个token，它将像这样进行：

```py
pred_token_k = model(input_ids[:k]*attention_mask[:k]^T)
```

> 请注意，这是伪代码。

为了我们的目的，我们可以忽略注意力掩码。对于CausalLM模型，我们通常希望注意力掩码为全1，因为我们希望关注所有前面的tokens。同时注意，[:k]实际上意味着我们使用从第0索引到第k-1索引的元素，因为切片中的结束索引是*排除*的。

考虑到这一点，我们得到：

```py
pred_token_k = model(input_ids[:k])
```

损失值通过将*labels[k]*与*pred_token_k*进行比较来计算。

实际上，二者都表示为1xv的向量，其中v是词汇表的大小。每个元素表示该token的概率。对于预测结果（*pred_token_k*），这些是真实的概率，由模型预测得出。对于真实标签（*labels[k]*），我们可以通过将实际的真实token设为1，词汇表中所有其他token设为0，来人为地使其具有正确的形状。

假设我们正在预测样本句子的第二个单词，也就是说k=1（我们使用的是零索引k）。第一个要点是我们用来生成预测的上下文，第二个要点是我们要预测的真实标签token。

k=1：

+   Input_ids[:1] == [the]

+   Labels[1] == dog

k=2：

+   Input_ids[:2] == [the, dog]

+   Labels[2] == likes

k=3：

+   Input_ids[:3] == [the, dog, likes]

+   Labels[3] == food

假设k=3，我们向模型输入“[the, dog, likes]”。模型的输出是：

```py
[P(dog)=10%, P(food)=60%,P(likes)=0%, P(the)=30%]
```

换句话说，模型认为下一个token是“dog”的概率是10%，下一个token是“food”的概率是60%，下一个token是“the”的概率是30%。

真实标签可以表示为：

```py
[P(dog)=0%, P(food)=100%, P(likes)=0%, P(the)=0%]
```

在实际训练中，我们会使用像交叉熵这样的损失函数。为了尽量直观，让我们简单使用绝对差异来大致感知损失。所谓绝对差异，我指的是预测概率与我们的“真实”概率之间的绝对差值：例如，*absolute_diff_dog = |0.10–0.00| = 0.10*。

即使使用这个粗略的损失函数，你也能看出，为了最小化损失，我们希望对实际标签（例如 food）预测出较高的概率，并对词汇表中的其他所有标记预测较低的概率。

举个例子，假设在训练后，当我们让模型根据[the, dog, likes]预测下一个标记时，输出结果如下：

现在，我们的损失已经变小，因为在给定这些输入后，我们学会了以较高的概率预测“food”。

训练过程就是不断重复这一过程：尝试将预测的概率与训练序列中所有标记的真实下一个标记对齐。

## 结论

希望你能对如何使用 HuggingFace 训练 CausalLM 模型的过程有一个直观的了解。你可能会有一些问题，比如：“为什么我们需要将 *labels* 作为一个单独的数组，而不是直接在每一步使用 *input_ids* 的第 k 个索引？有没有可能 *labels* 与 *input_ids* 不同？”

我会让你思考这些问题，暂时先停在这里。我们将在下一个帖子中继续解答这些问题并展示实际代码！
