# Transformer：它们如何转化你的数据？

> 原文：[https://towardsdatascience.com/transformers-how-do-they-transform-your-data-72d69e383e0d?source=collection_archive---------0-----------------------#2024-03-28](https://towardsdatascience.com/transformers-how-do-they-transform-your-data-72d69e383e0d?source=collection_archive---------0-----------------------#2024-03-28)

## 深入探索Transformer架构及其在语言任务中无敌的原因

[](https://medium.com/@maxwolf34?source=post_page---byline--72d69e383e0d--------------------------------)[![Maxime Wolf](../Images/259b3659d0e6dd1d0f0eec4ae92d02e9.png)](https://medium.com/@maxwolf34?source=post_page---byline--72d69e383e0d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--72d69e383e0d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--72d69e383e0d--------------------------------) [Maxime Wolf](https://medium.com/@maxwolf34?source=post_page---byline--72d69e383e0d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--72d69e383e0d--------------------------------) ·阅读时间11分钟·2024年3月28日

--

![](../Images/4dd0f4cd464558d94b73f8274580ac94.png)

作者提供的图片

在人工智能和机器学习飞速发展的今天，有一种创新脱颖而出，对我们处理、理解和生成数据的方式产生了深远影响：**Transformer**。Transformer彻底改变了自然语言处理（NLP）及其他领域，为今天一些最先进的AI应用提供了动力。但究竟什么是Transformer，它们又是如何以如此突破性的方式转换数据的呢？本文将揭秘Transformer模型的内部工作原理，重点讲解**编码器架构**。我们将从Python中Transformer编码器的实现入手，逐步解析其主要组件。然后，我们将可视化Transformer如何在训练过程中处理并适应输入数据。

虽然本博客并未涵盖所有架构细节，但它提供了一个实现并帮助你全面理解Transformer的变革性力量。想深入了解Transformer的工作原理，我建议你参考斯坦福大学的优秀CS224-n课程。

我还建议关注与本文相关的[GitHub 仓库](https://github.com/maxime7770/Transformers-Insights)以获取更多细节。😊

# 什么是Transformer编码器架构？

![](../Images/df39c5bc0e96c04388b637bb391a7fed.png)

来自[Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf)的Transformer模型

这张图片展示了原始Transformer架构，将编码器和解码器结合用于序列到序列的语言任务。

在本文中，我们将重点介绍编码器架构（图中的红色块）。这正是流行的BERT模型在后台使用的架构：其主要关注的是**理解和表示数据**，而不是生成序列。它可以用于多种应用：文本分类、命名实体识别（NER）、抽取式问答等。

那么，这些数据究竟是如何通过该架构进行转换的呢？我们将详细解释每个组件，但这里是过程的概述。

+   输入文本被**标记化**：Python字符串被转换成标记（数字）列表。

+   每个标记都通过一个**嵌入层**，该层输出每个标记的向量表示。

+   然后，这些嵌入会通过**位置编码层**进一步编码，添加每个标记在序列中的位置信息。

+   这些新的嵌入通过一系列**编码器层**进行转换，使用自注意力机制。

+   可以添加一个**任务特定的头**。例如，我们稍后将使用一个分类头，将电影评论分类为正面或负面。

重要的是要理解，Transformer架构通过将嵌入向量从高维空间中的一个表示映射到同一空间中的另一个表示，应用一系列复杂的变换来转换这些嵌入。

# 在Python中实现编码器架构

## 位置编码器层

与RNN模型不同，自注意力机制不利用输入序列的顺序。PositionalEncoder类通过使用两种数学函数：余弦和正弦，向输入嵌入添加位置编码。

![](../Images/578ef5784eef3551c213f00d3bfdbdd4.png)

位置编码矩阵定义来自[Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf)

注意，位置编码不包含可训练的参数：它们是确定性计算的结果，这使得该方法非常可处理。此外，正弦和余弦函数的值介于-1和1之间，并具有有助于模型学习**单词相对位置**的有用周期性特性。

```py
class PositionalEncoder(nn.Module):
    def __init__(self, d_model, max_length):
        super(PositionalEncoder, self).__init__()
        self.d_model = d_model
        self.max_length = max_length

        # Initialize the positional encoding matrix
        pe = torch.zeros(max_length, d_model)

        position = torch.arange(0, max_length, dtype=torch.float).unsqueeze(1)
        div_term = torch.exp(torch.arange(0, d_model, 2, dtype=torch.float) * -(math.log(10000.0) / d_model))

        # Calculate and assign position encodings to the matrix
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)
        self.pe = pe.unsqueeze(0)

    def forward(self, x):
        x = x + self.pe[:, :x.size(1)] # update embeddings
        return x
```

## 多头自注意力

自注意力机制是编码器架构的关键组件。我们暂时忽略“多头”部分。注意力是一种方法，用来确定每个标记（即每个嵌入）与**所有其他嵌入与该标记的相关性**，从而获得更精细和与上下文相关的编码。

![](../Images/cd6d84be3aae29b2a4108ac244d7dd6b.png)

“它”是如何关注序列中其他单词的？([The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/))

自注意力机制有三个步骤。

+   使用矩阵Q、K和V分别转换输入的“**查询**”、“**键**”和“**值**”。请注意，对于自注意力机制，查询、键和值都是等于我们的输入嵌入。

+   通过余弦相似度（点积）计算**查询**和**键**之间的注意力得分。得分会通过嵌入维度的平方根进行缩放，以稳定训练过程中的梯度。

+   使用 softmax 层将这些得分转化为**概率**。

+   输出是**值**的加权平均，使用注意力得分作为权重。

从数学角度来看，这对应于以下公式。

![](../Images/c5773d753ad9db7bab34c7e7739de18c.png)

[注意力机制来自《Attention Is All You Need》](https://arxiv.org/pdf/1706.03762.pdf)

“多头”是什么意思？基本上，我们可以并行多次应用上述自注意力机制过程，并将输出进行拼接和投影。这使得每个头可以**专注于句子的不同语义方面**。

我们首先定义头的数量、嵌入的维度（d_model）以及每个头的维度（head_dim）。我们还初始化了 Q、K 和 V 矩阵（线性层），以及最终的投影层。

```py
class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, num_heads):
        super(MultiHeadAttention, self).__init__()
        self.num_heads = num_heads
        self.d_model = d_model
        self.head_dim = d_model // num_heads

        self.query_linear = nn.Linear(d_model, d_model)
        self.key_linear = nn.Linear(d_model, d_model)
        self.value_linear = nn.Linear(d_model, d_model)      
        self.output_linear = nn.Linear(d_model, d_model)
```

使用多头注意力时，我们对每个注意力头应用一个减少维度的处理（使用 head_dim 而非 d_model），就像原论文中所述，这使得总的计算成本类似于一个全维度的单头注意力层。请注意，这只是一个逻辑上的拆分。多头注意力之所以强大，是因为它仍然可以通过单一的矩阵操作来表示，从而使得 GPU 上的计算非常高效。

```py
def split_heads(self, x, batch_size):
        # Split the sequence embeddings in x across the attention heads
        x = x.view(batch_size, -1, self.num_heads, self.head_dim)
        return x.permute(0, 2, 1, 3).contiguous().view(batch_size * self.num_heads, -1, self.head_dim)
```

我们计算注意力得分，并使用掩码避免在填充的标记上使用注意力。我们应用 softmax 激活函数将这些得分转化为概率。

```py
def compute_attention(self, query, key, mask=None):
      # Compute dot-product attention scores
      # dimensions of query and key are (batch_size * num_heads, seq_length, head_dim)
      scores = query @ key.transpose(-2, -1) / math.sqrt(self.head_dim)
      # Now, dimensions of scores is (batch_size * num_heads, seq_length, seq_length)
      if mask is not None:
          scores = scores.view(-1, scores.shape[0] // self.num_heads, mask.shape[1], mask.shape[2]) # for compatibility
          scores = scores.masked_fill(mask == 0, float('-1e20')) # mask to avoid attention on padding tokens
          scores = scores.view(-1, mask.shape[1], mask.shape[2]) # reshape back to original shape
      # Normalize attention scores into attention weights
      attention_weights = F.softmax(scores, dim=-1)

      return attention_weights
```

`forward` 属性执行多头逻辑拆分并计算注意力权重。然后，我们通过将这些权重与值相乘来获得输出。最后，我们重新调整输出的形状并通过线性层进行投影。

```py
def forward(self, query, key, value, mask=None):
      batch_size = query.size(0)

      query = self.split_heads(self.query_linear(query), batch_size)
      key = self.split_heads(self.key_linear(key), batch_size)
      value = self.split_heads(self.value_linear(value), batch_size)

      attention_weights = self.compute_attention(query, key, mask)

      # Multiply attention weights by values, concatenate and linearly project outputs
      output = torch.matmul(attention_weights, value)
      output = output.view(batch_size, self.num_heads, -1, self.head_dim).permute(0, 2, 1, 3).contiguous().view(batch_size, -1, self.d_model)
      return self.output_linear(output)
```

## 编码器层

这是该架构的主要组件，它利用了多头自注意力。我们首先实现一个简单的类，通过 2 个全连接层执行前向操作。

```py
class FeedForwardSubLayer(nn.Module):
    def __init__(self, d_model, d_ff):
        super(FeedForwardSubLayer, self).__init__()
        self.fc1 = nn.Linear(d_model, d_ff)
        self.fc2 = nn.Linear(d_ff, d_model)
        self.relu = nn.ReLU()

    def forward(self, x):
        return self.fc2(self.relu(self.fc1(x)))
```

现在我们可以编写编码器层的逻辑。我们首先对输入应用自注意力，得到一个相同维度的向量。然后我们使用带有层归一化层的小型前馈网络。请注意，我们在应用归一化之前还使用了跳跃连接。

```py
class EncoderLayer(nn.Module):
    def __init__(self, d_model, num_heads, d_ff, dropout):
        super(EncoderLayer, self).__init__()
        self.self_attn = MultiHeadAttention(d_model, num_heads)
        self.feed_forward = FeedForwardSubLayer(d_model, d_ff)
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, mask):
        attn_output = self.self_attn(x, x, x, mask)
        x = self.norm1(x + self.dropout(attn_output)) # skip connection and normalization
        ff_output = self.feed_forward(x)
        return self.norm2(x + self.dropout(ff_output)) # skip connection and normalization
```

## 将一切整合在一起

现在是时候创建我们的最终模型了。我们通过嵌入层将数据传递进去。这将原始标记（整数）转换为数值向量。然后我们应用位置编码器和若干（num_layers）编码器层。

```py
class TransformerEncoder(nn.Module):
    def __init__(self, vocab_size, d_model, num_layers, num_heads, d_ff, dropout, max_sequence_length):
        super(TransformerEncoder, self).__init__()
        self.embedding = nn.Embedding(vocab_size, d_model)
        self.positional_encoding = PositionalEncoder(d_model, max_sequence_length)
        self.layers = nn.ModuleList([EncoderLayer(d_model, num_heads, d_ff, dropout) for _ in range(num_layers)])

    def forward(self, x, mask):
        x = self.embedding(x)
        x = self.positional_encoding(x)
        for layer in self.layers:
            x = layer(x, mask)
        return x
```

我们还创建了一个 `ClassifierHead` 类，用于将最终的嵌入转换为分类任务的类别概率。

```py
class ClassifierHead(nn.Module):
    def __init__(self, d_model, num_classes):
        super(ClassifierHead, self).__init__()
        self.fc = nn.Linear(d_model, num_classes)

    def forward(self, x):
        logits = self.fc(x[:, 0, :]) # first token corresponds to the classification token
        return F.softmax(logits, dim=-1)
```

注意，稠密层和 softmax 层只应用于第一个嵌入（对应于输入序列的第一个标记）。这是因为在分词文本时，第一个标记是[CLS]标记，代表“分类”。[CLS]标记的设计目的是将整个序列的信息聚合成一个单一的嵌入向量，作为可以用于分类任务的摘要表示。

注意：包含[CLS]标记的概念源自 BERT，BERT 最初是在类似下一句预测任务上进行训练的。[CLS]标记被插入以预测句子 B 是否跟随句子 A，而[SEP]标记则分隔这两个句子。对于我们的模型，[SEP]标记只是标记输入句子的结束，如下所示。

![](../Images/d3feb19e839643803e928bfc05828ad2.png)

BERT 架构中的[CLS]标记 ([All About AI](https://seunghan96.github.io/dl/nlp/28.-nlp-BERT-%EC%9D%B4%EB%A1%A0/))

当你仔细想想，真是令人震惊，这个单一的[CLS]嵌入能够捕获如此多关于整个序列的信息，这要归功于自注意力机制能够权衡和综合每个文本片段之间相互关系的重要性。

# 训练与可视化

希望上一节能帮助你更好地理解我们的 Transformer 模型是如何转换输入数据的。接下来，我们将编写我们的训练流程，用于处理 IMDB 数据集（电影评论）的二分类任务。然后，我们将可视化训练过程中[CLS]标记的嵌入，看看我们的模型是如何转换它的。

我们首先定义超参数，并且使用 BERT 分词器。在 GitHub 仓库中，你可以看到我还编写了一个函数，来选择数据集的一个子集，其中包含 1200 个训练样本和 200 个测试样本。

```py
num_classes = 2 # binary classification
d_model = 256 # dimension of the embedding vectors
num_heads = 4 # number of heads for self-attention
num_layers = 4 # number of encoder layers
d_ff = 512\. # dimension of the dense layers in the encoder layers
sequence_length = 256 # maximum sequence length 
dropout = 0.4 # dropout to avoid overfitting
num_epochs = 20
batch_size = 32

loss_function = torch.nn.CrossEntropyLoss()

dataset = load_dataset("imdb")
dataset = balance_and_create_dataset(dataset, 1200, 200) # check GitHub repo

tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased', model_max_length=sequence_length)
```

你可以尝试在其中一个句子上使用 BERT 分词器：

```py
print(tokenized_datasets['train']['input_ids'][0])
```

每个序列应以 101 号标记开始，对应[CLS]，接着是一些非零整数，如果序列长度小于 256，则用零填充。注意，这些零在使用我们的“掩码”进行自注意力计算时会被忽略。

```py
tokenized_datasets = dataset.map(encode_examples, batched=True)
tokenized_datasets.set_format(type='torch', columns=['input_ids', 'attention_mask', 'label'])

train_dataloader = DataLoader(tokenized_datasets['train'], batch_size=batch_size, shuffle=True)
test_dataloader = DataLoader(tokenized_datasets['test'], batch_size=batch_size, shuffle=True)

vocab_size = tokenizer.vocab_size

encoder = TransformerEncoder(vocab_size, d_model, num_layers, num_heads, d_ff, dropout, max_sequence_length=sequence_length)
classifier = ClassifierHead(d_model, num_classes)

optimizer = torch.optim.Adam(list(encoder.parameters()) + list(classifier.parameters()), lr=1e-4)
```

现在我们可以编写训练函数：

```py
def train(dataloader, encoder, classifier, optimizer, loss_function, num_epochs):
    for epoch in range(num_epochs):        
        # Collect and store embeddings before each epoch starts for visualization purposes (check repo)
        all_embeddings, all_labels = collect_embeddings(encoder, dataloader)
        reduced_embeddings = visualize_embeddings(all_embeddings, all_labels, epoch, show=False)
        dic_embeddings[epoch] = [reduced_embeddings, all_labels]

        encoder.train()
        classifier.train()
        correct_predictions = 0
        total_predictions = 0
        for batch in tqdm(dataloader, desc="Training"):
            input_ids = batch['input_ids']
            attention_mask = batch['attention_mask'] # indicate where padded tokens are
            # These 2 lines make the attention_mask a matrix instead of a vector
            attention_mask = attention_mask.unsqueeze(-1)
            attention_mask = attention_mask & attention_mask.transpose(1, 2) 
            labels = batch['label']
            optimizer.zero_grad()
            output = encoder(input_ids, attention_mask)
            classification = classifier(output)
            loss = loss_function(classification, labels)
            loss.backward()
            optimizer.step()
            preds = torch.argmax(classification, dim=1)
            correct_predictions += torch.sum(preds == labels).item()
            total_predictions += labels.size(0)

        epoch_accuracy = correct_predictions / total_predictions
        print(f'Epoch {epoch} Training Accuracy: {epoch_accuracy:.4f}')
```

你可以在 GitHub 仓库中找到 collect_embeddings 和 visualize_embeddings 函数。它们存储了训练集每个句子的[CLS]标记嵌入，应用一种叫做 t-SNE 的降维技术将其转化为二维向量（而不是 256 维向量），并保存动画图表。

让我们来可视化结果。

![](../Images/23987d3afe279c76504d4c31a47e8066.png)

每个训练点的投影[CLS]嵌入（蓝色代表正向句子，红色代表负向句子）

通过观察每个训练点的[CLS]嵌入的投影图，我们可以看到经过若干轮训练后，正向（蓝色）和负向（红色）句子之间的明显区别。这一可视化展示了Transformer架构随着时间推移调整嵌入的显著能力，突出了自注意力机制的强大功能。数据以一种方式进行转化，使得每个类别的嵌入得到了良好的分离，从而大大简化了分类器头的任务。

# 结论

随着我们对Transformer架构的探索的结束，显然这些模型擅长将数据定制化以适应特定任务。通过使用位置编码和多头自注意力机制，Transformer不仅仅是处理数据：它们以一种前所未见的复杂程度来解释和理解信息。能够动态地权衡输入数据不同部分的相关性，使得对输入文本的理解和表示更加细致。这提升了在各种下游任务中的表现，包括文本分类、问答、命名实体识别等。

现在你已经更好地理解了编码器架构，你可以深入探讨解码器和编码器-解码器模型，这些模型与我们刚刚探讨的非常相似。解码器在生成任务中起着至关重要的作用，是流行的GPT模型的核心部分。

+   随时在[LinkedIn](https://www.linkedin.com/in/maxime-wolf/)上与我联系

+   请在[GitHub](https://github.com/maxime7770)上关注我，获取更多内容

+   访问我的网站: [maximewolf.com](http://maximewolf.com)

**参考文献**

[1] Vaswani, Ashish, 等人. “Attention Is All You Need.” *第31届神经信息处理系统会议（NIPS 2017）*, 美国加利福尼亚州长滩.

[2] “The Illustrated Transformer.” *Jay Alammar的博客*, 2018年6月, [http://jalammar.github.io/illustrated-transformer/](http://jalammar.github.io/illustrated-transformer/)

[3] Transformer架构的官方PyTorch实现. *GitHub代码库*, PyTorch, [https://github.com/pytorch/pytorch/blob/master/torch/nn/modules/transformer.py](https://github.com/pytorch/pytorch/blob/master/torch/nn/modules/transformer.py)

[4] Manning, Christopher, 等人. “CS224n: 使用深度学习进行自然语言处理.” *斯坦福大学*, 斯坦福CS224N NLP课程, [http://web.stanford.edu/class/cs224n/](http://web.stanford.edu/class/cs224n/)
