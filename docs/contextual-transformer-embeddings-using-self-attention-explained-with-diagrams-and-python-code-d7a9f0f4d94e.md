# 自注意力机制与代码解析

> 原文：[https://towardsdatascience.com/contextual-transformer-embeddings-using-self-attention-explained-with-diagrams-and-python-code-d7a9f0f4d94e?source=collection_archive---------1-----------------------#2024-02-09](https://towardsdatascience.com/contextual-transformer-embeddings-using-self-attention-explained-with-diagrams-and-python-code-d7a9f0f4d94e?source=collection_archive---------1-----------------------#2024-02-09)

## 大型语言模型如何创建丰富的上下文嵌入

[](https://medium.com/@bradneysmith?source=post_page---byline--d7a9f0f4d94e--------------------------------)[![Bradney Smith](../Images/32634347ac8cfd7c542eca402262fa81.png)](https://medium.com/@bradneysmith?source=post_page---byline--d7a9f0f4d94e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7a9f0f4d94e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d7a9f0f4d94e--------------------------------) [Bradney Smith](https://medium.com/@bradneysmith?source=post_page---byline--d7a9f0f4d94e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7a9f0f4d94e--------------------------------) ·32分钟阅读·2024年2月9日

--

## **“从零开始的LLMs”系列的第3部分 —— 完整指南，帮助你理解和构建大型语言模型。如果你有兴趣了解这些模型的工作原理，鼓励你阅读：**

+   [第1部分：分词 —— 完整指南](https://medium.com/p/cedc9f72de4e)

+   [第2部分：从零开始实现word2vec词嵌入（Python）](https://medium.com/p/eb9326c6ab7c)

+   **第3部分：自注意力机制与代码解析**

+   [第4部分：BERT完整指南与代码实现](https://medium.com/p/9f87602e4a11/)

+   [第5部分：Mistral 7B解析：迈向更高效的语言模型](https://medium.com/p/7f9c6e6b7251)

# 介绍

论文《Attention is All You Need》可能标志着过去10年自然语言处理（NLP）领域最大的进展：Transformer架构[1]。这一架构大幅简化了当时语言模型的复杂设计，同时取得了无与伦比的成果。最先进（SOTA）的模型，如GPT、Claude和Llama系列，都将其成功归功于这一设计，而其核心便是自注意力机制。在本深度解析中，我们将探讨这一机制的工作原理，以及它如何被transformer用于创建具有丰富上下文信息的嵌入，从而使这些模型能够表现得如此出色。

# 目录

**1** — [Transformer嵌入过程概述](#cc4f)

**2** — [位置编码](#365f)

**3** — [自注意力机制](#4489)

**4** — [Python中的Transformer嵌入](#cb34)

**5** — [结论](#126e)

**6** — [进一步阅读](#776b)

# 1 — Transformer嵌入过程概述

## 1.1 — Transformer回顾

在本系列的前导文章中，我们简要探讨了Transformer的历史及其对自然语言处理（NLP）的影响。回顾一下：Transformer是一种深度神经网络架构，是今天几乎所有大型语言模型（LLM）的基础。衍生模型通常被称为基于Transformer的模型，简称**Transformer**，因此这些术语将在本文中交替使用。像所有机器学习模型一样，Transformer处理的是数字和线性代数，而不是直接处理人类语言。由于这一点，它们必须通过几个步骤将用户的文本输入转换为数值表示。也许这些步骤中最重要的一步是应用自注意力机制，这是本文的重点。将文本表示为向量的过程称为**嵌入**（或**编码**），因此输入文本的数值表示被称为**Transformer嵌入**。

## 1.2 — 静态嵌入的问题

在[本系列的第2部分](https://medium.com/p/eb9326c6ab7c)中，我们以word2vec为例，探讨了语言模型的静态嵌入。这种嵌入方法早于Transformer，并且存在一个主要的缺点：缺乏上下文信息。具有多重含义的词（称为**多义**词）会被编码为具有某种程度模糊性的表示，因为它们缺乏精确意义所需的上下文信息。一个典型的多义词例子是`bank`。使用静态嵌入模型时，`bank`这个词在向量空间中的表示会与`money`和`deposit`等词有一定的相似度，同时与`river`和`nature`等词也会有一定的相似度。这是因为该词会出现在训练数据的多种不同上下文中。这就是静态嵌入的核心问题：它们不会根据上下文而变化——因此称为“静态”。

## 1.3 — 修正静态嵌入

Transformer通过生成自我上下文感知的Transformer嵌入来克服静态嵌入的局限性。在这种方法中，固定的词嵌入通过位置信息（词语在输入文本中的位置）和上下文信息（词语的使用方式）进行增强。这两个步骤分别发生在Transformer的不同组件中，即位置编码器和自注意力模块。我们将在接下来的章节中详细探讨这些内容。通过结合这些额外的信息，Transformer可以基于输入序列中的使用情况，生成更强大的词向量表示。扩展向量表示，超越静态嵌入，正是让基于Transformer的模型能够处理多义词，并相比之前的模型更深入地理解语言。

## 1.4 — 引入学习嵌入

类似于四年前发布的word2vec方法，Transformer通过一个线性层（一个小型神经网络）的权重存储每个标记的初始向量表示。在word2vec模型中，这些表示形成了静态嵌入，但在Transformer上下文中，这些被称为**学习到的嵌入**。在实践中，它们非常相似，但使用不同的名称强调这些表示仅仅是Transformer嵌入的起点，而不是最终的形式。

线性层位于Transformer架构的起始位置，只有权重，没有偏置项（每个神经元的偏置为0）。该层的权重可以表示为一个*V* × *d_model*大小的矩阵，其中*V*是词汇表的大小（训练数据中唯一词汇的数量），*d_model*是嵌入维度的数量。在上一篇文章中，我们将*d_model*表示为*N*，以符合word2vec的记法，但在这里我们将使用*d_model*，这是Transformer中更常见的术语。原始的Transformer提出时，*d_model*的大小为512维，但在实际应用中，可以使用任何合理的值。

![](../Images/d06e44ef1d0d9e9f5a7f5b0990725f24.png)

一张图展示了线性层在Transformer架构中的位置，该层存储了学习到的嵌入。图像由作者提供，改编自《Attention is All You Need》论文中的Transformer架构图[1]。

## **1.5 — 创建学习到的嵌入**

静态嵌入和学习到的嵌入之间的一个关键区别是它们的训练方式。静态嵌入在一个单独的神经网络中进行训练（使用Skip-Gram或Continuous Bag of Words架构），通过在给定的窗口大小内进行词预测任务进行训练。一旦训练完成，这些嵌入就会被提取，并与不同的语言模型一起使用。然而，学习到的嵌入是你正在使用的Transformer模型的一部分，并作为权重存储在模型的第一个线性层中。这些权重，进而词汇表中每个标记的学习到的嵌入，会在与其他模型参数相同的反向传播步骤中进行训练。下面是学习到的嵌入的训练过程总结。

**步骤 1：初始化**

在模型的开始阶段，随机初始化线性层中每个神经元的权重，并将偏置项设置为0。这个层也被称为**嵌入层**，因为它是存储学习到的嵌入的线性层。权重可以表示为一个*V* × *d_model*大小的矩阵，其中词汇表中每个词的词嵌入沿着矩阵的行存储。例如，词汇表中第一个词的嵌入存储在第一行，第二个词的嵌入存储在第二行，以此类推。

**步骤 2：训练**

在每个训练步骤中，Transformer 接收一个输入单词，目标是预测序列中的下一个单词——这一任务称为下一个标记预测（NTP）。最初，这些预测会非常差，因此网络中的每个权重和偏置项都会被更新，以改善相对于损失函数的性能，包括嵌入。在多次训练迭代后，学习到的嵌入应为词汇表中的每个单词提供强大的向量表示。

**第 3 步：提取学习到的嵌入**

当新的输入序列提供给模型时，单词会被转换为带有关联的标记 ID 的标记，这些标记 ID 对应于标记在分词器词汇表中的位置。例如，单词`cat`可能位于分词器词汇表中的位置`349`，因此其 ID 为`349`。标记 ID 用于创建独热编码向量，从权重矩阵中提取正确的学习嵌入（即，*V*维向量，其中每个元素都是 0，除了标记 ID 位置的元素为 1）。

> ***注意：*** *PyTorch 是一个非常流行的 Python 深度学习库，驱动了一些最著名的机器学习包，如 HuggingFace* `*Transformers*` *库[2]。如果你熟悉 PyTorch，可能已经遇到过* `*nn.Embedding*` *类，它通常用于构建 Transformer 网络的第一层（`*nn*` 表示该类属于神经网络包）。该类返回一个常规的线性层，该层以恒等函数作为激活函数进行初始化，并且没有偏置项。由于权重是模型在训练过程中学习的参数，它们会随机初始化。这实际上在一行简单的代码中完成了上述步骤。记住，`*nn.Embedding*` 层不会直接提供预训练的词嵌入，而是初始化一个空白的嵌入画布，供训练期间学习。这是为了让 Transformer 在训练阶段学习自己的嵌入。*

## 1.6 — Transformer 嵌入过程

一旦学习到的嵌入经过训练，嵌入层中的权重将不再变化。也就是说，每个单词（或更具体地说，标记）的学习嵌入总是提供单词向量表示的相同起点。从这里开始，位置和上下文信息将被添加进去，生成一个独特的单词表示，这个表示能够反映其在输入序列中的使用。

Transformer 嵌入是通过四个步骤创建的，下面使用示例提示词`Write a poem about a man fishing on a river bank.`来演示。请注意，前两步与我们之前看到的 word2vec 方法相同。第 3 步和第 4 步是进一步的处理，它们为嵌入添加上下文信息。

**第 1 步）分词：**

分词是将较长的输入序列拆分为单独的单词（以及部分单词），这些单独的单词称为“tokens”。在这种情况下，句子将被拆分为：

`write`, `a`, `poem`, `about`, `a`, `man`, `fishing`, `on`, `a`, `river`, `bank`

接下来，将每个token与其token ID关联，token ID是一个整数值，表示token在分词器词汇表中的位置（有关分词过程的详细介绍，请参见[本系列第1部分](https://medium.com/p/cedc9f72de4e)）。

**第2步）将tokens映射到学习到的嵌入：**

一旦输入序列被转换为一组token ID，tokens就会被映射到它们的学习到的嵌入向量表示，这些向量是在变换器训练过程中获得的。这些学习到的嵌入具有“查找表”行为，正如我们在[本系列第2部分](https://medium.com/p/eb9326c6ab7c)中的word2vec示例中所看到的那样。映射过程通过将由token ID创建的独热编码向量与权重矩阵相乘来进行，就像word2vec方法一样。学习到的嵌入在下图中表示为*V*。

**第3步）通过位置编码添加位置信息：**

**位置编码**用于将位置信息添加到单词嵌入中。与递归神经网络（RNNs）按顺序处理文本（一次处理一个单词）不同，变换器是并行处理所有单词的。这消除了每个单词在句子中位置的任何隐含信息。例如，句子`the cat ate the mouse`和`the mouse ate the cat`使用相同的单词，但具有完全不同的意义。为了保留单词顺序，会为每个单词生成位置编码向量，并将其添加到学习到的嵌入中。在下图中，位置编码向量表示为*P*，学习到的嵌入和位置编码的和表示为*X*。

**第4步）使用自注意力修改嵌入：**

最后一步是使用自注意力机制添加上下文信息。这决定了哪些单词为输入序列中的其他单词提供上下文。在下图中，变换器嵌入表示为*y*。

![](../Images/d8ef956e6f7cf243b5882eeafc7d5998.png)

从输入文本到变换器嵌入的变换器嵌入过程概述。图片来自作者。

# 2 — 位置编码

## 2.1 — 位置编码的需求

在应用自注意力机制之前，位置编码用于将tokens的顺序信息添加到学习到的嵌入中。这弥补了前面描述的变换器并行处理导致的位置信息丢失。对于注入这些信息，有多种可行的方法，但所有方法必须遵守一组约束。用于生成位置信息的函数必须产生以下值：

+   **有界性** — 值不应在正负方向上爆炸，而应受到限制（例如在 0 和 1、-1 和 1 之间等）

+   **周期性** — 该函数应生成一个重复的模式，模型可以学习识别并从中推断位置

+   **可预测性** — 位置信息应以一种方式生成，使得模型能够理解在其未训练过的序列长度中单词的位置。例如，即使模型没有见过长度恰好为 412 的序列，Transformer 也应该能够理解序列中每个嵌入的位置信息。

这些约束确保了位置编码器生成的位置信息使得单词能够**关注**（从中获得上下文）序列中任何其他重要单词，无论它们在序列中的相对位置如何。理论上，在足够强大的计算机上，单词应该能够从每个相关单词中获得上下文，甚至在一个无限长的输入序列中也能做到。在像 ChatGPT 这样的聊天机器人中，上下文包括当前的提示和所有之前的提示与响应（在上下文长度限制内）。这个限制通常在几千个词汇之间，GPT-3 支持最多 4096 个词汇，而 GPT-4 企业版的上限约为 128,000 个词汇 [3]。

## 2.2 — 论文《Attention is All You Need》中的位置编码

原始的 Transformer 模型提出了以下位置编码函数：

![](../Images/efb7118b8809dfcffe5ae7e06de1f2a0.png)

这张图展示了论文《Attention is All You Need》中提出的位置信息编码的方程式。[1] 图片由作者提供。

其中：

+   *pos* 是单词在输入中的位置，*pos = 0* 对应序列中的第一个单词

+   *i* 是每个嵌入维度的索引，范围从 *i=0*（第一个嵌入维度）到 *d_model*

+   *d_model* 是每个学习到的嵌入向量的维度数（因此也是每个位置编码向量的维度数）。在关于 word2vec 的文章中，这个值以前被表示为 *N*。

两个提出的函数分别接受 *2i* 和 *2i+1* 作为参数，实际上意味着正弦函数为每个单词向量的偶数维度生成位置信息（*i* 为偶数），而余弦函数为奇数维度生成位置信息（*i* 为奇数）。根据 Transformer 的作者：

> *“位置编码对应一个正弦波。波长从 2π 到 10000·2π 形成几何级数。我们选择这个函数，因为我们假设它可以使模型轻松学习通过相对位置进行关注，因为对于任何固定偏移量* k*，* PE_pos+k *可以表示为* PE_pos* 的线性函数。”*

分母中的常数值`10_000`在一些实验后发现是合适的，但这是作者的一个相对随意的选择。

## 2.3 — 其他位置编码方法

上面显示的位置编码被认为是**固定的**，因为它们是由已知的函数生成，具有确定性（可预测）的输出。这代表了最简单形式的位置编码。也可以通过随机初始化一些位置编码并使用反向传播训练它们，来使用**学习的位置编码**。BERT架构的衍生模型就是采用这种学习编码方法的例子。最近，旋转位置编码（RoPE）方法变得越来越流行，并在Llama 2和PaLM等模型中得到了应用，此外还有其他位置编码方法。

## 2.4 — 在Python中实现位置编码器

在Python中创建一个位置编码器类相当简单。我们可以从定义一个函数开始，该函数接受嵌入维度的数量（`d_model`）、输入序列的最大长度（`max_length`），以及对向量中每个值四舍五入的小数位数（`rounding`）。需要注意的是，transformer定义了一个最大输入序列长度，任何少于此限制的序列都会通过填充标记（padding tokens）直到达到该限制。为了考虑这种行为，我们在位置编码器中接受一个`max_length`参数。实际上，这个限制通常是数千个字符长。

我们还可以利用一个数学技巧来节省计算量。我们不需要为每个*PE_{pos, 2i}*和*PE_{pos, 2i+1}*计算分母，而是可以注意到，分母在连续的*i*对之间是相同的。例如，*i=0*和*i=1*的分母是相同的，*i=2*和*i=3*的分母也是相同的。因此，我们可以先为偶数值的*i*计算分母，并将其重复用于奇数值的*i*。

```py
import numpy as np

class PositionalEncoder():
    """ An implementation of positional encoding.

    Attributes:
        d_model (int): The number of embedding dimensions in the learned
            embeddings. This is used to determine the length of the positional
            encoding vectors, which make up the rows of the positional encoding
            matrix.
        max_length (int): The maximum sequence length in the transformer. This
            is used to determine the size of the positional encoding matrix.
        rounding (int): The number of decimal places to round each of the
            values to in the output positional encoding matrix.
    """

    def __init__(self, d_model, max_length, rounding):
        self.d_model = d_model
        self.max_length = max_length
        self.rounding = rounding

    def generate_positional_encoding(self):
        """ Generate positional information to add to inputs for encoding.

        The positional information is generated using the number of embedding
        dimensions (d_model), the maximum length of the sequence (max_length),
        and the number of decimal places to round to (rounding). The output
        matrix generated is of size (max_length X embedding_dim), where each
        row is the positional information to be added to the learned
        embeddings, and each column is an embedding dimension.
        """

        position = np.arange(0, self.max_length).reshape(self.max_length, 1)
        even_i = np.arange(0, self.d_model, 2)
        denominator = 10_000**(even_i / self.d_model)

        even_encoded = np.round(np.sin(position / denominator), self.rounding)
        odd_encoded = np.round(np.cos(position / denominator), self.rounding)

        # Interleave the even and odd encodings
        positional_encoding = np.stack((even_encoded, odd_encoded),2)\
        .reshape(even_encoded.shape[0],-1)

        # If self.d_model is odd remove the extra column generated
        if self.d_model % 2 == 1:
            positional_encoding = np.delete(positional_encoding, -1, axis=1)

        return positional_encoding

    def encode(self, input):
        """ Encode the input by adding positional information.

        Args:
            input (np.array): A two-dimensional array of embeddings. The array
                should be of size (self.max_length x self.d_model).

        Returns:
            output (np.array): A two-dimensional array of embeddings plus the
                positional information. The array has size (self.max_length x
                self.d_model).
        """
        positional_encoding = self.generate_positional_encoding()
        output = input + positional_encoding

        return output

MAX_LENGTH = 5
EMBEDDING_DIM = 3
ROUNDING = 2

# Instantiate the encoder
PE = PositionalEncoder(d_model=EMBEDDING_DIM,
                       max_length=MAX_LENGTH,
                       rounding=ROUNDING)

# Create an input matrix of word embeddings without positional encoding
input = np.round(np.random.rand(MAX_LENGTH, EMBEDDING_DIM), ROUNDING)

# Create an output matrix of word embeddings by adding positional encoding
output = PE.encode(input)

# Print the results
print(f'Embeddings without positional encoding:\n\n{input}\n')
print(f'Positional encoding:\n\n{output-input}\n')
print(f'Embeddings with positional encoding:\n\n{output}')
```

```py
Embeddings without positional encoding:

[[0.12 0.94 0.9 ]
 [0.14 0.65 0.22]
 [0.29 0.58 0.31]
 [0.69 0.37 0.62]
 [0.25 0.61 0.65]]

Positional encoding:

[[ 0\.    1\.    0\.  ]
 [ 0.84  0.54  0\.  ]
 [ 0.91 -0.42  0\.  ]
 [ 0.14 -0.99  0.01]
 [-0.76 -0.65  0.01]]

Embeddings with positional encoding:

[[ 0.12  1.94  0.9 ]
 [ 0.98  1.19  0.22]
 [ 1.2   0.16  0.31]
 [ 0.83 -0.62  0.63]
 [-0.51 -0.04  0.66]]
```

## 2.5 — 可视化位置编码矩阵

记住，生成的位置编码必须是有界的、周期性的和可预测的。之前提到的正弦函数的输出可以收集到一个矩阵中，然后可以通过逐元素加法轻松地与学习到的嵌入结合。绘制这个矩阵可以清楚地展示所需的属性。在下面的图中，负值的弯曲带（蓝色）从矩阵的左边缘发散出来。这些带状图案形成了一个模式，transformer可以很容易地学会预测这个模式。

```py
import matplotlib.pyplot as plt

# Instantiate a PositionalEncoder class
d_model = 400
max_length = 100
rounding = 4

PE = PositionalEncoder(d_model=d_model,
                       max_length=max_length,
                       rounding=rounding)

# Generate positional encodings
input = np.round(np.random.rand(max_length, d_model), 4)
positional_encoding = PE.generate_positional_encoding()

# Plot positional encodings
cax = plt.matshow(positional_encoding, cmap='coolwarm')
plt.title(f'Positional Encoding Matrix ({d_model=}, {max_length=})')
plt.ylabel('Position of the Embedding\nin the Sequence, pos')
plt.xlabel('Embedding Dimension, i')
plt.gcf().colorbar(cax)
plt.gca().xaxis.set_ticks_position('bottom')
```

![](../Images/f1e9343ea758e9c210dd01735917fbb3.png)

这是一个位置编码矩阵的可视化，模型的嵌入维度为400（d_model = 400），最大序列长度为100（max_length = 100）。图片来自作者。

# 3 — 自注意力机制

## 3.1 — 注意力机制概述

现在我们已经介绍了变压器嵌入和位置编码步骤的概述，我们可以将注意力集中在自注意力机制本身。简而言之，自注意力修改词汇的向量表示，以捕捉它们在输入序列中的使用上下文。自注意力中的“自”指的是该机制使用单一序列中周围的词来提供上下文。因此，自注意力要求所有词汇并行处理。这实际上是变压器的主要优势之一（尤其是与RNNs相比），因为这些模型能够利用并行处理来显著提高性能。近年来，关于这种方法的思考有所重新调整，未来我们可能会看到这一核心机制被替代[4]。

变压器中使用的另一种注意力形式是交叉注意力。与仅在单一序列内操作的自注意力不同，交叉注意力将输出序列中的每个词与输入序列中的每个词进行比较，穿越两个嵌入矩阵。请注意与自注意力的区别，后者完全专注于单一序列内。

## 3.2 — 可视化自注意力如何上下文化嵌入

下图展示了一组简化的二维嵌入向量。与自然和河流相关的词汇集中在图表的右上象限，而与金钱相关的词汇则集中在左下象限。由于`bank`一词具有多义性，它的向量被定位在两个聚类之间。自注意力的目标是将学习到的嵌入向量移动到向量空间中更准确地捕捉它们在输入序列中的含义的区域。在示例输入`Write a poem about a man fishing on a river bank.`中，目标是调整`bank`一词的向量，使其更多地反映自然和河流的含义，而减少与金钱和存款相关的含义。

> ***注意：*** *更准确地说，这里自注意力的目标是更新输入中每个词的向量，使得所有嵌入更好地表示它们被使用的上下文。这里并没有特别之处，词* `*bank*` *并不是变压器具备某些特殊知识的词——自注意力会应用于所有词。我们稍后会进一步探讨这一点，但现在，只考虑* `*bank*` *是如何受到自注意力影响的，就能很好地理解注意力块中发生了什么。在这个可视化过程中，位置编码信息没有被明确显示。它的影响是微乎其微的，但请注意，自注意力机制实际上会作用于学习的嵌入向量与位置编码的总和，而不仅仅是学习的嵌入向量。*

```py
import matplotlib.pyplot as plt

# Create word embeddings
xs = [0.5, 1.5, 2.5, 6.0, 7.5, 8.0]
ys = [3.0, 1.2, 0.5, 8.0, 7.5, 5.5]
words = ['money', 'deposit', 'withdraw', 'nature', 'river', 'water']
bank = [[4.5, 4.5], [6.7, 6.5]]

# Create figure
fig, ax = plt.subplots(ncols=2, figsize=(8,4))

# Add titles
ax[0].set_title('Learned Embedding for "bank"\nwithout context')
ax[1].set_title('Contextual Embedding for\n"bank" after self-attention')

# Add trace on plot 2 to show the movement of "bank"
ax[1].scatter(bank[0][0], bank[0][1], c='blue', s=50, alpha=0.3)
ax[1].plot([bank[0][0]+0.1, bank[1][0]],
           [bank[0][1]+0.1, bank[1][1]],
           linestyle='dashed',
           zorder=-1)

for i in range(2):
    ax[i].set_xlim(0,10)
    ax[i].set_ylim(0,10)

    # Plot word embeddings
    for (x, y, word) in list(zip(xs, ys, words)):
        ax[i].scatter(x, y, c='red', s=50)
        ax[i].text(x+0.5, y, word)

    # Plot "bank" vector
    x = bank[i][0]
    y = bank[i][1]

    color = 'blue' if i == 0 else 'purple'

    ax[i].text(x+0.5, y, 'bank')
    ax[i].scatter(x, y, c=color, s=50)
```

![](../Images/5f354932182eeb198f54b475bbb48d5f.png)

词语“bank”在嵌入空间中随着上下文信息的添加而移动的向量表示的可视化。图片由作者提供。

## 3.3 — 自注意力算法

在上面的部分中，我们提到自注意力的目标是将每个标记的嵌入移动到一个更好地代表其在输入序列中使用上下文的向量空间区域。我们没有讨论的是如何实现这一目标。在这里，我们将通过逐步示例展示自注意力机制是如何通过添加来自周围标记的上下文来修改`bank`的嵌入的。

![](../Images/b3d91328fafccbbaae297fafcc98024d.png)

自注意力块的简化概述（省略了键、查询和值矩阵）。图片由作者提供。

**步骤 1）使用点积计算词语之间的相似度：**

一个标记的上下文由句子中周围的标记提供。因此，我们可以使用输入序列中所有标记的嵌入来更新任何词的嵌入，例如`bank`。理想情况下，提供重要上下文的词（例如`river`）将对嵌入产生重大影响，而提供较少上下文的词（例如`a`）则影响较小。

一个词对另一个词贡献的上下文程度通过相似度得分来衡量。具有相似学习嵌入的标记比那些嵌入不相似的标记更可能提供更多上下文。相似度得分是通过将当前标记的嵌入（其学习嵌入加上位置信息）与序列中每个其他标记的当前嵌入进行点积来计算的。为清楚起见，本文中将当前的嵌入称为**自注意力输入**，并用*x*表示。

有几种方法可以衡量两个向量之间的相似度，通常可以分为：基于距离的度量和基于角度的度量。基于距离的度量使用它们之间的直线距离来表征向量的相似度。这个计算相对简单，可以理解为在*d_model*维空间中应用勾股定理。尽管直观，但这种方法在计算上非常昂贵。

对于基于角度的相似度度量，两个主要的候选方法是：**余弦相似度**和**点积相似度**。这两者都通过计算两个向量之间的夹角余弦值来表征相似度，θ。对于正交向量（彼此垂直的向量），*cos(θ)* = 0，表示没有相似度。对于平行向量，*cos(θ) = 1*，表示这些向量是相同的。单纯使用向量之间的角度（如余弦相似度所做的那样）并不理想，原因有二。其一是没有考虑向量的大小，因此偶然对齐的远距离向量会产生膨胀的相似度分数。其二是，余弦相似度需要首先计算点积，然后再除以向量大小的乘积——这使得余弦相似度成为一个计算开销较大的度量。因此，点积被用来确定相似度。下面给出了两个向量*x_1*和*x_2*的点积公式。

![](../Images/dd1c4f8b7ee67b05504226a91925408f.png)

两个向量x_1和x_2的点积公式。图片来自作者。

以下图所示是`bank`的自注意力输入向量*x_bank*与输入序列中每个标记的向量表示矩阵*X^T*的点积。我们还可以将*x_bank*写作*x_11*，以反映其在输入序列中的位置。矩阵*X*存储了输入序列中每个标记的自注意力输入，每一行表示一个标记的输入。该矩阵的列数由*L_max*给出，它是模型的最大序列长度。在这个示例中，我们假设最大序列长度等于输入提示中的单词数量，因此不需要任何填充标记（更多关于填充的内容请参见[本系列的第4部分](https://medium.com/p/9f87602e4a11)）。为了直接计算点积，我们可以对*X*进行转置，并使用公式*S_bank = x_bank ⋅ X^T*计算相似度分数向量*S_bank*。*S_bank*中的每个元素代表`bank`与输入序列中每个标记之间的相似度分数。

![](../Images/18b5ae2730dc2264ecc4a624d440c469.png)

对X_11与每个自注意力输入（输入序列中每个标记的学习到的嵌入和位置信息之和）进行相似度分数计算的示例。图片来自作者。

**步骤 2) 缩放相似度分数：**

点积方法缺乏任何形式的归一化（与余弦相似度不同），这可能导致相似度分数变得非常大。这会带来计算上的挑战，因此必须进行某种形式的归一化。最常见的方法是将每个分数除以√*d_model*，从而得到**缩放点积注意力**。缩放点积注意力不仅限于自注意力，它还用于变换器中的交叉注意力。

**步骤 3) 使用 Softmax 函数计算注意力权重：**

上一步的输出是向量 *S_bank*，它包含了`bank`与输入序列中每个标记之间的相似度分数。这些相似度分数被用作加权系数，从而通过对每个周围标记的嵌入进行加权求和，构造出`bank`的transformer嵌入。这些权重，称为**注意力权重**，是通过将 *S_bank* 输入softmax函数计算得出的。输出结果存储在一个名为 *W_bank* 的向量中。有关softmax函数的更多信息，请参阅上一篇关于word2vec的文章。

![](../Images/2517faf278824d683631ffda0065dc21.png)

基于与每个自注意力输入的相似度计算“bank”的注意力权重的示例。图片来自作者。

**步骤4）计算Transformer嵌入**

最后，`bank`的transformer嵌入是通过对`write`、`a`、`prompt`、…、`bank`的嵌入加权求和得到的。当然，`bank`与自身的相似度最高（因此具有最大的注意力权重），所以这一过程后的输出嵌入将与之前相似。这个行为是理想的，因为初始嵌入已经占据了一个编码`bank`意义的向量空间区域。目标是将嵌入稍微调整，朝向那些提供更多上下文的单词。提供较少上下文的单词，如`a`和`man`，其权重非常小。因此，它们对输出嵌入的影响极小。提供显著上下文的单词，如`river`和`fishing`，将具有更高的权重，因此会将输出嵌入拉近它们所在的向量空间区域。最终结果是一个新的嵌入，*y_bank*，它反映了整个输入序列的上下文。

![](../Images/05cded56f34d6004d381e21e7c9b16a1.png)

通过对序列中每个标记的其他嵌入进行加权求和，给出“bank”的新嵌入示例计算。图片来自作者。

## 3.4 — 使用矩阵扩展自注意力机制

上面，我们回顾了计算单一单词`bank`的transformer嵌入的步骤。输入由`bank`的学习嵌入向量及其位置信息组成，我们将其表示为 *x_11* 或 *x_bank*。这里的关键点是，我们仅考虑了一个向量作为输入。如果我们将矩阵 *X*（维度为 *L_max* × *d_model*）传递给自注意力模块，我们就可以同时计算输入提示中每个标记的transformer嵌入。输出矩阵 *Y* 包含了矩阵行中每个标记的transformer嵌入。这种方法使得transformer能够快速处理文本。

![](../Images/389598fcf770519eb023c556cb59def4.png)

自注意力模块的黑箱示意图。输入序列的词向量矩阵用X表示，输出序列的矩阵用Y表示。图片来自作者。

## 3.5 — 查询、键和值矩阵

上述描述概述了自注意力模块的核心功能，但仍然有一个关键部分没有提到。上面的简单加权和没有包含任何可训练的参数，但我们可以将一些可训练的参数引入到过程中。没有可训练的参数，模型的表现可能仍然很好，但通过允许模型从训练数据中学习更复杂的模式和隐藏特征，我们可以观察到模型表现明显更强。

自注意力的输入被使用三次来计算新的嵌入，这些输入包括 *x_bank* 向量、点积步骤中的 *X^T* 矩阵和加权和步骤中的 *X^T* 矩阵。这三个位置是引入一些权重的理想候选位置，这些权重以矩阵的形式添加（如图中红色所示）。当这些矩阵与各自的输入（如图中蓝色所示）相乘时，它们形成了 **键**、**查询**和**值**矩阵，分别为 *K*、*Q* 和 *V*（如图中紫色所示）。这些权重矩阵的列数是用户的架构选择。选择一个小于 *d_model* 的 *d_q*、*d_k* 和 *d_v* 值将导致维度降维，这可以提高模型的速度。最终，这些值是超参数，可以根据模型的具体实现和使用场景进行调整，如果不确定，通常会将它们都设置为 *d_model* [5]。

![](../Images/2ad982632d2b0892f30068585882c9cc.png)

一个完整的自注意力模块图，包括键、查询和值矩阵。图像来源：作者。

## 3.6 — 数据库类比

这些矩阵的名称来源于与数据库的类比，下面简要解释了这一点。

**查询：**

+   在数据库中，查询是你在执行搜索时要寻找的内容。例如，“展示数据库中所有销量超过1,000,000张唱片的专辑”。在自注意力模块中，我们基本上是在问同样的问题，只不过表达为“展示这个向量（例如 *x_bank*）的 Transformer 嵌入”。为了简单起见，我们只考虑了单个向量 *x_bank*，但请记住，我们可以通过将多个向量收集成矩阵来对任意多个向量执行自注意力步骤。因此，我们可以很容易地将矩阵 *X* 作为查询传入，这样问题就变成了“展示输入序列中每个向量的 Transformer 嵌入”。这就是基于 Transformer 的模型中实际发生的事情。

**键：**

+   数据库中的键是正在被搜索的属性或列。在前面给出的例子中，你可以将其视为“专辑销量”列，它存储了我们感兴趣的信息。在自注意力中，我们对输入提示中每个单词的嵌入感兴趣，因此我们可以计算一组注意力权重。因此，键矩阵是所有输入嵌入的集合。

**值：**

+   值对应于数据库中的实际数据，即我们示例中的实际销售数据（例如，2,300,000本）。对于自注意力机制，这与键矩阵的输入完全相同（以及我们刚刚讨论的查询矩阵）：一个包含所有输入嵌入的集合。因此，查询、键和值矩阵都以矩阵*X*作为输入。

## 3.7 — 关于多头注意力的说明

**将计算分布到多个头部：**

《Attention is All You Need》论文通过将注意力机制分为多个**头部**，将标准自注意力扩展为**多头注意力（MHA）**。在标准自注意力中，模型学习一组权重矩阵（*W_Q*、*W_K* 和 *W_V*），这些矩阵将词嵌入矩阵*X*转换为查询、键和值矩阵（*Q*、*K* 和 *V*）。这些矩阵然后用于计算注意力得分，并像我们上面看到的那样更新*X*，以获取上下文信息。

相比之下，MHA将注意力机制拆分成*H*个独立的头部，每个头部学习自己的一组较小的权重矩阵。这些权重用于计算一组较小的、头部特定的查询、键和值矩阵（分别表示为*Q^h*、*K^h* 和 *V^h*）。每个头部独立处理输入序列，生成不同的注意力输出。然后将这些输出连接在一起（堆叠在一起），并通过最终的线性层，生成更新后的*X*矩阵，如下图中的*Y*所示，带有丰富的上下文信息。

通过引入多个头部，MHA增加了注意力过程中的可学习参数数量，使模型能够捕捉数据中的更复杂关系。每个头部学习自己的一组权重矩阵，从而使它们能够专注于输入的不同方面，如长程依赖（远距离单词之间的关系）、短程依赖（相邻单词之间的关系）、语法结构等。总体效果是产生一个对输入序列有更细致理解的模型。

![](../Images/c975eac45848ff41a478756ae0f5683e.png)

多头注意力机制的概述。如需深入了解术语和每个过程步骤，请参阅本系列第5部分第2.8节。图片由作者提供。

以下段落重点介绍了如何建立这种工作原理的广泛直觉，以及为什么这个步骤是有用的。然而，如果你有兴趣深入了解MHA的实现细节，请参阅第5部分第2.8节——《Mistral 7B 完整指南及代码》[链接即将发布！]。

**使用多个头部的好处：**

核心思想是允许每个头部学习输入序列中单词之间的不同类型关系，并将它们结合起来创建深度文本表示。例如，一些头部可能学习捕捉长期依赖（文本中远距离单词之间的关系），而其他头部则可能专注于短期依赖（文本中相邻单词之间的关系）。

如果模型给定句子`A man withdrew money from the bank then sat on the river bank`，使用多头注意力机制可以让模型捕捉到`money`与第一个`bank`之间的短期依赖关系，以及`river`与第二个`bank`之间的独立依赖关系。因此，`bank`这个词的两个用法将通过不同的上下文信息得到正确更新，以对应它们各自的含义。

**构建多头注意力的直觉：**

为了加深对多头注意力机制有用性的直觉，考虑一个需要大量上下文的句子中的单词。例如，在句子`I ate some of Bob’s chocolate cake`中，单词`ate`应该关注`I`、`Bob’s`和`cake`来获得完整的上下文。这是一个相对简单的例子，但如果你将这个概念扩展到包含成千上万个单词的复杂序列，那么分配计算负载到独立的注意力机制上似乎是合理的。

![](../Images/a35f4b3cf73ce16a174a3bd127e35164.png)

一个示例，展示了注意力头如何捕捉输入序列中不同的单词依赖关系。图像由作者提供。

# 4 — Python中的变换器嵌入

## 4.1 — 从变换器模型中提取学习到的嵌入和变换器嵌入

Python有许多用于处理变换器模型的选项，但可能没有比Hugging Face更为知名的了。Hugging Face为NLP研究人员和开发者提供了一个集中的资源中心，包括如下工具：

+   `transformers`: Hugging Face的核心库，提供了使用、训练和微调预训练变换器模型的接口。

+   `tokenizers`: 一个用于处理多种变换器的分词器库，可以使用预先构建的分词器模型，也可以从头开始构建全新的分词器。

+   `datasets`: 一个用于训练各种任务模型的数据集集合，不仅仅限于NLP。

+   模型中心：一个大型的前沿模型库，包含了已发布论文中的模型、社区开发的模型以及介于两者之间的所有内容。这些模型是免费提供的，并可以通过`transformers` API轻松导入到Python中。

以下代码单元展示了如何使用`transformers`库将基于变换器的模型加载到Python中，并展示了如何提取单词的学习嵌入（无上下文）和变换器嵌入（有上下文）。本文的其余部分将分解此单元中展示的步骤，并描述在处理嵌入时可用的其他功能。

```py
import torch
from transformers import AutoModel, AutoTokenizer

def extract_le(sequence, tokenizer, model):
    """ Extract the learned embedding for each token in an input sequence.

    Tokenize an input sequence (string) to produce a tensor of token IDs.
    Return a tensor containing the learned embedding for each token in the
    input sequence.

    Args:
        sequence (str): The input sentence(s) to tokenize and extract
            embeddings from.
        tokenizer: The tokenizer used to produce tokens.
        model: The model to extract learned embeddings from.

    Returns:
        learned_embeddings (torch.tensor): A tensor containing tensors of
            learned embeddings for each token in the input sequence.
    """
    token_dict = tokenizer(sequence, return_tensors='pt')
    token_ids = token_dict['input_ids']
    learned_embeddings = model.embeddings.word_embeddings(token_ids)[0]

    # Additional processing for display purposes
    learned_embeddings = learned_embeddings.tolist()
    learned_embeddings = [[round(i,2) for i in le] \
                          for le in learned_embeddings]

    return learned_embeddings

def extract_te(sequence, tokenizer, model):
    """ Extract the tranformer embedding for each token in an input sequence.

    Tokenize an input sequence (string) to produce a tensor of token IDs.
    Return a tensor containing the transformer embedding for each token in the
    input sequence.

    Args:
        sequence (str): The input sentence(s) to tokenize and extract
            embeddings from.
        tokenizer: The tokenizer used to produce tokens.
        model: The model to extract learned embeddings from.

    Returns:
        transformer_embeddings (torch.tensor): A tensor containing tensors of
            transformer embeddings for each token in the input sequence.
    """
    token_dict = tokenizer(sequence, return_tensors='pt')

    with torch.no_grad():
        base_model_output = model(**token_dict)

    transformer_embeddings = base_model_output.last_hidden_state[0]

    # Additional processing for display purposes
    transformer_embeddings = transformer_embeddings.tolist()
    transformer_embeddings = [[round(i,2) for i in te] \
                              for te in transformer_embeddings]

    return transformer_embeddings

# Instantiate DistilBERT tokenizer and model
tokenizer = AutoTokenizer.from_pretrained('distilbert-base-uncased')
model = AutoModel.from_pretrained('distilbert-base-uncased')

# Extract the learned embedding for bank from DistilBERT
le_bank = extract_le('bank', tokenizer, model)[1]

# Write sentences containing "bank" in two different contexts
s1 = 'Write a poem about a man fishing on a river bank.'
s2 = 'Write a poem about a man withdrawing money from a bank.'

# Extract the transformer embedding for bank from DistilBERT in each sentence
s1_te_bank = extract_te(s1, tokenizer, model)[11]
s2_te_bank = extract_te(s2, tokenizer, model)[11]

# Print the results
print('------------------- Embedding vectors for "bank" -------------------\n')
print(f'Learned embedding:                  {le_bank[:5]}')
print(f'Transformer embedding (sentence 1): {s1_te_bank[:5]}')
print(f'Transformer embedding (sentence 2): {s2_te_bank[:5]}')
```

```py
------------------- Embedding vectors for "bank" -------------------

Learned embedding:                  [-0.03, -0.06, -0.09, -0.07, -0.03]
Transformer embedding (sentence 1): [0.15, -0.16, -0.17, -0.08, 0.44]
Transformer embedding (sentence 2): [0.27, -0.23, -0.23, -0.21, 0.79]
```

## 4.2 — 导入`Transformers`库

生成 Transformer 嵌入的第一步是从 Hugging Face `transformers` 库中选择一个模型。在本文中，我们将不使用模型进行推理，而仅仅用来检查它生成的嵌入。这不是一个标准的用例，因此我们需要进行一些额外的探索，以便访问嵌入。由于 `transformers` 库是用 PyTorch 编写的（在代码中称为 `torch`），我们可以导入 `torch` 来提取模型内部的数据。

## 4.3 — 选择一个模型

在这个例子中，我们将使用 DistilBERT，这是 Google 的 BERT 模型的一个较小版本，Hugging Face 于 2019 年 10 月发布 [6]。根据 Hugging Face 的文档 [7]：

> *DistilBERT 是一个小巧、快速、便宜且轻量的 Transformer 模型，通过蒸馏 BERT base 训练得到。与 `*bert-base-uncased*` 相比，它的参数少了 40%，运行速度快 60%，同时在 GLUE 语言理解基准测试中保留了超过 95% 的 BERT 性能*。

我们可以直接从 `transformers` 库中或使用 `AutoModel` 和 `AutoTokenizer` 类将 DistilBERT 及其相应的标记化器导入 Python。两者之间几乎没有区别，尽管通常更倾向于使用 `AutoModel` 和 `AutoTokenizer`，因为模型名称可以作为参数存储在字符串中，这使得更换所用模型变得更加简单。

```py
import torch
from transformers import DistilBertTokenizerFast, DistilBertModel

# Instantiate DistilBERT tokenizer and model
tokenizer = DistilBertTokenizerFast.from_pretrained('distilbert-base-uncased')
model = DistilBertModel.from_pretrained('distilbert-base-uncased')
```

```py
import torch
from transformers import AutoModel, AutoTokenizer

# Instantiate DistilBERT tokenizer and model
tokenizer = AutoTokenizer.from_pretrained('distilbert-base-uncased')
model = AutoModel.from_pretrained('distilbert-base-uncased')
```

导入 DistilBERT 及其相应的标记化器后，我们可以调用 `from_pretrained` 方法，为每个组件加载我们想要使用的 DistilBERT 模型和标记化器的特定版本。在这个例子中，我们选择了 `distilbert-base-uncased`，其中 `base` 指的是模型的大小，`uncased` 表示该模型是在没有大小写区分的文本上训练的（所有文本都转换为小写）。

## 4.4 — 创建一些示例句子

接下来，我们可以创建一些句子，为模型提供一些需要嵌入的单词。这两个句子 `s1` 和 `s2` 都包含单词 `bank`，但在不同的语境中。这里的目标是展示单词 `bank` 在两个句子中的学习嵌入是相同的，然后通过 DistilBERT 使用自注意力机制加以修改，从而为每个输入序列生成独特的、具语境的嵌入。

```py
# Create example sentences to produce embeddings for
s1 = 'Write a poem about a man fishing on a river bank.'
s2 = 'Write a poem about a man withdrawing money from a bank.'
```

## 4.5 — 对输入序列进行标记化

标记化器类可以用来标记化输入序列（如下所示），并将字符串转换为标记 ID 的列表。可选地，我们还可以传递一个 `return_tensors` 参数，将标记 ID 格式化为 PyTorch 张量（`return_tensors=pt`）或 TensorFlow 常量（`return_tensors=tf`）。如果不传递此参数，将返回一个 Python 列表形式的标记 ID。返回值是一个字典，包含 `input_ids`：包含标记 ID 的类似列表的对象，以及 `attention_mask`，我们暂时将忽略该部分。

> **注意：** 基于 BERT 的模型在每个序列的开头包括一个 `[CLS]` token，并在输入中使用 `[SEP]` token 来区分两个文本块。这些 token 存在是因为 BERT 最初的训练任务所需，可以在这里忽略不计。有关 BERT 特殊 token、模型大小、`cased` 与 `uncased` 以及注意力掩码的讨论，请参见 [本系列的第 4 部分](https://medium.com/p/9f87602e4a11)。

```py
token_dict = tokenizer(s1, return_tensors='pt')
token_ids = token_dict['input_ids'][0]
```

## 4.6 — 从模型中提取学习到的嵌入

每个 transformer 模型都可以通过 `embeddings.word_embeddings` 方法访问其学习到的嵌入。此方法接受一个 token ID 或一组 token ID，并返回作为 PyTorch 张量的学习嵌入。

```py
learned_embeddings = model.embeddings.word_embeddings(token_ids)
learned_embeddings
```

```py
tensor([[ 0.0390, -0.0123, -0.0208,  ...,  0.0607,  0.0230,  0.0238],
        [-0.0300, -0.0070, -0.0247,  ...,  0.0203, -0.0566, -0.0264],
        [ 0.0062,  0.0100,  0.0071,  ..., -0.0043, -0.0132,  0.0166],
        ...,
        [-0.0261, -0.0571, -0.0934,  ..., -0.0351, -0.0396, -0.0389],
        [-0.0244, -0.0138, -0.0078,  ...,  0.0069,  0.0057, -0.0016],
        [-0.0199, -0.0095, -0.0099,  ..., -0.0235,  0.0071, -0.0071]],
       grad_fn=<EmbeddingBackward0>)
```

## 4.7 — 从模型中提取 Transformer 嵌入

将缺乏上下文的学习嵌入转换为具有上下文感知的 transformer 嵌入需要进行一次模型的前向传播。由于我们在这里并不更新模型的权重（即不进行模型训练），因此可以使用 `torch.no_grad()` 上下文管理器来节省内存。这样我们就可以将 tokens 直接传递给模型，并计算 transformer 嵌入，而无需进行任何不必要的计算。一旦 tokens 被传入模型，模型会返回一个 `BaseModelOutput`，其中包含关于前向传播的各种信息。这里唯一关心的数据是最后一个隐藏状态中的激活值，它们构成了 transformer 嵌入。可以使用 `last_hidden_state` 属性访问这些值，如下所示，这也结束了本节代码单元的解释。

```py
with torch.no_grad():
    base_model_output = model(**token_dict)

transformer_embeddings = base_model_output.last_hidden_state
transformer_embeddings
```

```py
tensor([[[-0.0957, -0.2030, -0.5024,  ...,  0.0490,  0.3114,  0.1348],
         [ 0.4535,  0.5324, -0.2670,  ...,  0.0583,  0.2880, -0.4577],
         [-0.1893,  0.1717, -0.4159,  ..., -0.2230, -0.2225,  0.0207],
         ...,
         [ 0.1536, -0.1616, -0.1735,  ..., -0.3608, -0.3879, -0.1812],
         [-0.0182, -0.4264, -0.6702,  ...,  0.3213,  0.5881, -0.5163],
         [ 0.7911,  0.2633, -0.4892,  ..., -0.2303, -0.6364, -0.3311]]])
```

## 4.8 — 将 Token ID 转换为 Token

可以将 token ID 转换回文本 token，这样可以准确显示分词器是如何划分输入序列的。当使用子词分词器（如 WordPiece，BERT 基于模型使用）或字节对编码（Byte-Pair Encoding，例如 GPT 系列模型使用）时，这尤其有用，因为较长或较罕见的单词可能会被拆分成多个子词。

```py
tokens = tokenizer.convert_ids_to_tokens(token_ids)
tokens
```

```py
['[CLS]', 'write', 'a', 'poem', 'about', 'a', 'man', 'fishing', 'on', 'a',
 'river', 'bank', '.', '[SEP]']
```

# 5 — 结论

自注意力机制通过同时处理输入序列中的每个 token，生成丰富的、上下文感知的 transformer 嵌入。这些嵌入建立在静态词嵌入（如 word2vec）的基础上，并使得更强大的语言模型（如 BERT 和 GPT）成为可能。在这一领域的进一步研究将继续提升大型语言模型（LLM）和自然语言处理（NLP）的整体能力。

# 6 — 进一步阅读

[1] A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit, L. Jones, A. N. Gomez, Ł. Kaiser, 和 I. Polosukhin，[Attention is All You Need](https://arxiv.org/pdf/1706.03762)（2017），神经信息处理系统进展 30（NIPS 2017）

[2] Hugging Face，[Transformers](https://huggingface.co/docs/transformers/index)（2024），HuggingFace.co

[3] OpenAI，[ChatGPT 定价](https://openai.com/chatgpt/pricing/)（2024），OpenAI.com

[4] A. Gu 和 T. Dao， [Mamba: 线性时间序列建模与选择性状态空间](https://arxiv.org/abs/2312.00752)（2023），ArXiv abs/2312.00752

[5] J. Alammar, [插图版Transformer](https://jalammar.github.io/illustrated-transformer/) (2018). GitHub

[6] V. Sanh, L. Debut, J. Chaumond, 和 T. Wolf, [DistilBERT：BERT的蒸馏版——更小、更快、更便宜、更轻](https://arxiv.org/pdf/1910.01108) (2019)，第五届节能机器学习与认知计算研讨会——NeurIPS 2019

[7] Hugging Face, [DistilBERT文档](https://huggingface.co/docs/transformers/en/model_doc/distilbert) (2024) HuggingFace.co

[8] Hugging Face, [BERT文档](https://huggingface.co/docs/transformers/model_doc/bert) (2024) HuggingFace.co
