# GPT是优秀的嵌入模型吗？

> 原文：[https://towardsdatascience.com/are-gpts-good-embedding-models-28d8ef6f3f63?source=collection_archive---------0-----------------------#2024-05-18](https://towardsdatascience.com/are-gpts-good-embedding-models-28d8ef6f3f63?source=collection_archive---------0-----------------------#2024-05-18)

## 一个令人惊讶的实验，表明细节决定成败

[](https://medium.com/@yuchengtsai84?source=post_page---byline--28d8ef6f3f63--------------------------------)[![Yu-Cheng Tsai](../Images/c0ec2d4b9fea512040c8e6e0250670fc.png)](https://medium.com/@yuchengtsai84?source=post_page---byline--28d8ef6f3f63--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--28d8ef6f3f63--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--28d8ef6f3f63--------------------------------) [Yu-Cheng Tsai](https://medium.com/@yuchengtsai84?source=post_page---byline--28d8ef6f3f63--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28d8ef6f3f63--------------------------------) ·6分钟阅读·2024年5月18日

--

![](../Images/0f89f4b2c267496534e57792622439db.png)

图像由作者使用DALL-E生成

随着可用的嵌入模型数量不断增加，选择适合自己机器学习应用的模型可能变得具有挑战性。幸运的是，[MTEB排行榜](https://huggingface.co/spaces/mteb/leaderboard)为各种自然语言处理任务提供了全面的排名指标。

![](../Images/608efe4c9a370b4f9a08332196481dfd.png)

截至2024年5月17日，来自[MTEB排行榜](https://huggingface.co/spaces/mteb/leaderboard)的前五大嵌入模型

当你访问这个网站时，你会注意到排名前五的嵌入模型是生成式预训练变换模型（GPTs）。这可能让你认为GPT模型是最适合用于嵌入的模型。但这是真的吗？让我们进行一个实验来找出答案。

# GPT嵌入

嵌入是文本的张量表示，它将文本标记ID转换并投影到张量空间中。

通过将文本输入到神经网络模型中并执行前向传递，你可以获得嵌入向量。然而，实际过程要复杂一些。让我们一步一步分解：

1.  将文本转换为标记ID

1.  将标记ID传递到神经网络中

1.  返回神经网络的输出

在第一步中，我将使用分词器来实现这一点。`model_inputs`是文本内容`"some questions."`的张量表示。

```py
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-Instruct-v0.1")

messages = [
        {
            "role": "user",
            "content": "some questions.",
        },
]

encodeds = tokenizer.apply_chat_template(messages, return_tensors="pt")
model_inputs = encodeds.to("cuda")
```

第二步很简单，将`model_inputs`前向传递给神经网络。生成的标记的logits可以通过`.logits`访问。`torch.no_grad()`表示我不希望模型的权重被更新，因为模型处于推理模式。

```py
import torch

with torch.no_grad():
    return model(model_inputs).logits
```

第三步有点棘手。GPT模型是解码器-only，其token生成是自回归的。简单来说，已完成句子的最后一个token已经看到了句子中的所有前面的tokens。因此，最后一个token的输出包含了来自前面tokens的所有相关性分数（注意力）。

> 完美！你最感兴趣的是最后一个token，因为在transformer中的注意力机制。

在Hugging Face中实现的GPT的输出维度是（批量大小，输入token大小，词汇表数量）。为了获取所有批次的最后一个token输出，我可以执行张量切片。

```py
import torch
with torch.no_grad():
    return model(model_inputs).logits[:, -1, :]
```

# 这些GPT嵌入的质量

要衡量这些GPT嵌入的质量，可以使用[余弦相似度](https://pytorch.org/docs/stable/generated/torch.nn.CosineSimilarity.html)。余弦相似度越高，句子的语义越接近。

```py
import torch
def compute_cosine_similarity(vec1, vec2):
    cos = torch.nn.CosineSimilarity(dim=1, eps=1e-6)
    return cos(vec1, vec2)
```

让我们创建一些实用函数，允许我们遍历问题和答案对列表并查看结果。[Mistral 7b v0.1 指令](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.1)，这是一个出色的开源模型，用于本实验。

```py
import torch
from termcolor import colored
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "mistralai/Mistral-7B-Instruct-v0.1"
)

tokenizer = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-Instruct-v0.1")

def generate_last_token_embeddings(question):
    messages = [
        {
            "role": "user",
            "content": question,
        },
    ]
    encodeds = tokenizer.apply_chat_template(messages, return_tensors="pt")
    model_inputs = encodeds.to("cuda")
    with torch.no_grad():
        return model(model_inputs).logits[:, -1, :]

def get_similarities(questions, answers):
    for question in questions:
        for answer in answers:
            q_embedding, a_embedding = (
                generate_last_token_embeddings(question),
                generate_last_token_embeddings(answer),
            )
            similarity = compute_cosine_similarity(q_embedding, a_embedding)
            print(colored(f"question: {question} and ans: {answer}", "green"))
            print(colored(f"result: {similarity}", "blue"))

questions = ["Where is the headquarter of OpenAI?", "What is GPU?"]
answers = [
    "OpenAI is based at San Francisco.",
    "A graphics processing unit (GPU) is an electronic circuit that can perform mathematical calculations quickly",
]
get_similarities(questions, answers)
```

![](../Images/c99fb95672048c42a47ba01ae6599ec2.png)

Mistral 7b v0.1 指令的余弦相似度（图像来源：作者）

# 结果与观察

对于第一个问题和答案对：

+   问题：“OpenAI的总部是什么？”

+   答案：“OpenAI总部位于旧金山。”

+   余弦相似度：0.96

对于第二个问题和答案对：

+   问题：“什么是GPU？”

+   答案：“图形处理单元（GPU）是一个能够快速执行数学计算的电子电路。”

+   余弦相似度：0.94

对于不相关的对：

+   问题：“OpenAI的总部在哪里？”

+   答案：“图形处理单元（GPU）是一个能够快速执行数学计算的电子电路。”

+   余弦相似度：0.90

对于最差的对：

+   问题：“什么是GPU？”

+   答案：“OpenAI总部位于旧金山。”

+   余弦相似度：0.93

这些结果表明，在这种情况下，使用GPT模型（如mistral 7b instruct v0.1）作为嵌入模型可能不会在区分相关和不相关的对方面产生很好的结果。但为什么GPT模型仍然位居前五名嵌入模型呢？

# 对比损失来解救

```py
tokenizer = AutoTokenizer.from_pretrained("intfloat/e5-mistral-7b-instruct")
model = AutoModelForCausalLM.from_pretrained(
  "intfloat/e5-mistral-7b-instruct"
)
```

![](../Images/a7d9806157a8c611ddf1ee437d3e12be.png)

`e5-mistral-7b-instruct`的余弦相似度（图像来源：作者）

使用不同的模型`e[5-mistral-7b-instruct](http://intfloat/e5-mistral-7b-instruct)`重复相同的评估过程，该模型是MTEB排行榜上排名前列的开源模型之一，并从mistral 7b instruct微调而来。我发现，相关问题和对之间的余弦相似度分别为OpenAI和GPU问题为0.88和0.84。对于不相关的问题和答案对，相似度降至0.56和0.67。这个发现表明，`e5-mistral-7b-instruct`是一个在嵌入方面有显著改进的模型。这种改进是如何实现的呢？

![](../Images/08fb21f2da6dcfa55fa80bdea3f81999.png)

[对比损失函数](/contrastive-loss-explaned-159f2d4a87ec)

深入研究`e5-mistral-7b-instruct`背后的[论文](https://arxiv.org/pdf/2401.00368)，关键在于使用[对比损失](https://lilianweng.github.io/posts/2021-05-31-contrastive/)进一步微调mistral模型。

> 与使用[交叉熵损失](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html)对预测标记和标签标记进行训练或进一步微调的GPT模型不同，对比损失旨在最大化负样本对之间的距离，同时最小化正样本对之间的距离。

这篇[博客文章](/contrastive-loss-explaned-159f2d4a87ec)详细介绍了这一概念。`sim`函数计算两个向量之间的余弦距离。对于对比损失，分母表示正例和负例之间的余弦距离。对比损失背后的原理是，我们希望相似的向量尽可能接近1，因为log(1) = 0表示最优损失。

# 结论

在这篇文章中，我强调了使用GPT作为嵌入模型而没有微调的常见陷阱。我的评估表明，通过对GPT进行对比损失微调，嵌入可以更加有意义和具有区分性。通过了解GPT模型的优缺点，并利用定制的损失函数，如对比损失，你可以在选择和使用嵌入模型时做出更明智的决策。希望这篇文章能帮助你为应用程序明智地选择GPT模型，并期待听到你的反馈！ :)

如果你对大规模微调LLM感兴趣，我还有另一篇相关的文章，可以帮助你实现这一目标。 :)

[](https://medium.com/sage-ai/fine-tuning-large-language-models-a-guide-into-distributed-parallel-training-with-deepspeed-ray-784914926a17?source=post_page-----28d8ef6f3f63--------------------------------) [## 微调大型语言模型：分布式并行训练指南（使用DeepSpeed和Ray）

### 开源LLM的路径

medium.com](https://medium.com/sage-ai/fine-tuning-large-language-models-a-guide-into-distributed-parallel-training-with-deepspeed-ray-784914926a17?source=post_page-----28d8ef6f3f63--------------------------------)

我还有一篇关于大型语言模型（LLMs）扩展法则的文章。它提供了更多关于为什么语言模型变得越来越大的背景，并解释了背后的原因。祝你学习愉快，分享愉快！干杯。

[](https://medium.com/sage-ai/demystify-transformers-a-comprehensive-guide-to-scaling-laws-attention-mechanism-fine-tuning-fffb62fc2552?source=post_page-----28d8ef6f3f63--------------------------------) [## 解密 Transformer：扩展法则的全面指南

### 解构 Transformer 技术与扩展策略

medium.com](https://medium.com/sage-ai/demystify-transformers-a-comprehensive-guide-to-scaling-laws-attention-mechanism-fine-tuning-fffb62fc2552?source=post_page-----28d8ef6f3f63--------------------------------)
