# BERT完整指南（附代码）

> 原文：[https://towardsdatascience.com/a-complete-guide-to-bert-with-code-9f87602e4a11?source=collection_archive---------0-----------------------#2024-05-13](https://towardsdatascience.com/a-complete-guide-to-bert-with-code-9f87602e4a11?source=collection_archive---------0-----------------------#2024-05-13)

## 历史、架构、预训练和微调

[](https://medium.com/@bradneysmith?source=post_page---byline--9f87602e4a11--------------------------------)[![Bradney Smith](../Images/32634347ac8cfd7c542eca402262fa81.png)](https://medium.com/@bradneysmith?source=post_page---byline--9f87602e4a11--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9f87602e4a11--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9f87602e4a11--------------------------------) [Bradney Smith](https://medium.com/@bradneysmith?source=post_page---byline--9f87602e4a11--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9f87602e4a11--------------------------------) ·阅读时间43分钟·2024年5月13日

--

**“从零开始构建大语言模型”系列的第4部分——完整指南，帮助你理解和构建大语言模型。如果你对这些模型是如何工作的感兴趣，我鼓励你阅读：**

+   [第1部分：分词——完整指南](https://medium.com/p/cedc9f72de4e)

+   [第2部分：从零开始使用word2vec进行词嵌入（Python版）](https://medium.com/p/eb9326c6ab7c/)

+   [第3部分：自注意力机制与代码解析](https://medium.com/p/d7a9f0f4d94e)

+   **第4部分：BERT完整指南（附代码）**

+   [第5部分：Mistral 7B 解析：走向更高效的语言模型](https://medium.com/p/7f9c6e6b7251)

# **简介**

双向编码器表示（BERT）是由谷歌AI语言团队开发的大型语言模型（LLM），在自然语言处理（NLP）领域取得了显著的进展。近年来，许多模型受到了BERT的启发，或是对BERT的直接改进，例如RoBERTa、ALBERT和DistilBERT等。原始的BERT模型是在OpenAI的生成式预训练变换器（GPT）之后不久发布的，二者都建立在前一年提出的变换器架构基础上。虽然GPT侧重于自然语言生成（NLG），但BERT则优先考虑自然语言理解（NLU）。这两项技术的发展重塑了NLP领域，成为机器学习进步的重要里程碑。

以下文章将探讨BERT的历史，并详细描述其创建时的背景。这将全面展示论文作者所做的架构决策，并帮助理解如何训练和微调BERT，以便在工业和业余应用中使用。我们将通过详细的架构分析和图示，逐步介绍如何编写代码从零开始微调BERT进行情感分析任务。

# 目录

[**1** — BERT的历史与关键特性](#acda)

[**2** — 架构与预训练目标](#320d)

[**3** — 微调BERT进行情感分析](#ff89)

[**4** — 结论](#86e2)

[**5** — 深入阅读](#7f46)

# 1 — BERT的历史与关键特性

BERT模型可以通过四个主要特性来定义：

+   仅编码器架构

+   预训练方法

+   模型微调

+   使用双向上下文

这些特性是论文作者做出的设计选择，可以通过考虑模型创建时的背景来理解。以下部分将逐一介绍这些特性，并展示它们是如何受到BERT同时代模型（Transformer和GPT）的启发，或是作为对它们的改进而提出的。

## 1.1 — **仅编码器架构**

2017年Transformer的问世，开启了一场基于其创新设计生产新模型的竞赛。OpenAI于2018年6月率先推出了GPT：一个**仅解码器**模型，在自然语言生成（NLG）任务中表现卓越，最终推动了ChatGPT在后续版本中的发展。谷歌随后在四个月后发布了BERT：一个专为自然语言理解（NLU）设计的**仅编码器**模型。这两种架构都可以生成非常强大的模型，但它们能够执行的任务略有不同。下面是对每种架构的概述。

**仅解码器模型：**

+   **目标：** 针对输入序列预测新的输出序列

+   **概述：** 在Transformer中，解码器模块负责根据提供给编码器的输入生成输出序列。仅解码器模型通过完全省略编码器模块，并将多个解码器堆叠在一个模型中来构建。这些模型接受提示作为输入，并通过预测下一个最可能的词（或更具体地说，token）逐个生成响应，这一任务被称为下一个词预测（Next Token Prediction，NTP）。因此，仅解码器模型在自然语言生成（NLG）任务中表现出色，例如：对话式聊天机器人、机器翻译和代码生成。这些模型可能是大众最为熟悉的，因为像ChatGPT这样的应用广泛使用了由仅解码器模型（GPT-3.5和GPT-4）驱动。

**仅编码器模型：**

+   **目标：** 对输入序列中的词进行预测

+   **概述：** Transformer 中的编码器块负责接收输入序列，并为每个单词（或更具体地说，为每个标记）创建丰富的数值向量表示。仅编码器模型省略了解码器，并堆叠多个 Transformer 编码器来生成一个单一模型。这些模型并不接受提示，而是接受输入序列进行预测（例如，预测序列中的缺失单词）。仅编码器模型缺少用于生成新词的解码器，因此不会像 GPT 那样用于聊天机器人应用。相反，仅编码器模型通常用于自然语言理解（NLU）任务，如命名实体识别（NER）和情感分析。编码器块创建的丰富向量表示赋予了 BERT 对输入文本的深刻理解。BERT 的作者认为，这一架构选择相比 GPT 会提高 BERT 的表现，特别是他们指出，仅解码器架构的模型是：

> “在句子级任务中表现 sub-optimal，并且在应用基于微调的方法处理如问答之类的标记级任务时可能非常有害” [1]
> 
> **注意：** 从技术上讲，可以使用 BERT 生成文本，但正如我们将看到的，这并非该架构的初衷，且其结果在任何方面都无法与仅解码器模型相媲美。

**Transformer、GPT 和 BERT 的架构图：**

以下是我们迄今为止讨论的三种模型的架构图。该图是通过改编原始 Transformer 论文《Attention is All You Need》中的架构图 [2] 创建的。模型中编码器或解码器块的数量由 `N` 表示。在原始 Transformer 中，`N` 对编码器和解码器都等于 6，因为它们分别由六个编码器和解码器块堆叠而成。

![](../Images/c6623d00dc4952aceb3bbacd507ca9f6.png)

对 Transformer、GPT 和 BERT 架构的比较。该图由作者根据《Attention is All You Need》论文中的 Transformer 架构图 [2] 改编而来。

## **1.2 — 预训练方法**

GPT在多个方面影响了BERT的发展。这个模型不仅是第一个仅解码器的Transformer衍生模型，而且GPT还普及了模型的**预训练**。预训练包括训练一个大型模型，以获得对语言的广泛理解（涵盖词汇使用和语法模式等方面），从而生成一个任务无关的**基础模型**。在上述图示中，基础模型由线性层下方的组件（显示为紫色）构成。训练完成后，这个基础模型的副本可以被**微调**以应对特定任务。微调只涉及训练线性层：一个小型的前馈神经网络，通常被称为**分类头**或简称**头**。模型其余部分的权重和偏差（即基础部分）保持不变，或称为**冻结**。

**类比：**

为了构建一个简短的类比，考虑情感分析任务。在这个任务中，目标是根据文本所表达的情感将其分类为`positive`（正面）或`negative`（负面）。例如，在一些电影评论中，像`I loved this movie`这样的文本会被分类为`positive`，而像`I hated this movie`这样的文本会被分类为`negative`。在传统的语言建模方法中，你可能需要从零开始训练一个新的架构，专门针对这个任务。你可以把这看作是通过展示电影评论来教某人英语，直到他们最终能够分类其中的情感。当然，这样的做法既慢又昂贵，并且需要大量的训练样本。而且，得到的分类器仍然只会精通这个任务。在预训练方法中，你会采用一个通用模型并对其进行微调，专门用于情感分析。你可以把这看作是将已经流利掌握英语的人带入情境，仅展示少量的电影评论，让他们熟悉当前的任务。显然，第二种方法要高效得多。

**早期的预训练尝试：**

预训练的概念并非由OpenAI发明，早在之前的几年，其他研究人员也已探索过这一方向。一个显著的例子是ELMo模型（来自语言模型的嵌入），该模型由Allen Institute的研究人员开发[3]。尽管有这些早期尝试，但没有其他研究人员能像OpenAI在其开创性论文中那样有力地证明预训练的有效性。用他们自己的话说，团队发现他们的

> “任务无关模型的表现超越了那些使用专门为每个任务精心设计架构的判别式训练模型，显著提高了当前的技术水平”[4]。

这一发现坚定地确立了预训练范式作为未来语言建模的主导方法。顺应这一趋势，BERT的作者们也完全采用了预训练方法。

## **1.3 — 模型微调**

**微调的好处：**

微调已经成为今天的常态，以至于我们容易忽略这种方法崭露头角的时间其实是很短的。2018年之前，通常每个不同的自然语言处理（NLP）任务都会引入一种新的模型架构。转向预训练不仅大幅减少了开发模型所需的训练时间和计算成本，而且还减少了所需的训练数据量。与其完全重新设计和从头开始重新训练一个语言模型，像GPT这样的通用模型只需少量任务特定的数据，就能在短时间内进行微调。根据任务的不同，分类头可以改变，以包含不同数量的输出神经元。这对于情感分析等分类任务非常有用。例如，如果BERT模型的目标输出是预测一个评论是`positive`还是`negative`，则可以将头部更改为包含两个输出神经元。每个神经元的激活表示该评论为`positive`或`negative`的概率。对于一个有10个类别的多类分类任务，头部可以改为有10个神经元的输出层，依此类推。这使得BERT更加多功能，使基础模型能够用于各种下游任务。

**BERT中的微调：**

BERT沿袭了GPT的做法，也采用了预训练/微调的方法。谷歌发布了两个版本的BERT：Base版和Large版，用户可以根据硬件限制选择不同的模型大小。两个版本都需要在多个TPU（张量处理单元）上预训练约4天，BERT Base在16个TPU上进行训练，BERT Large则在64个TPU上进行训练。对于大多数研究人员、爱好者和行业从业者来说，这种训练规模是不可行的。因此，花费仅几个小时在特定任务上对基础模型进行微调，依然是一个更具吸引力的选择。原始的BERT架构已经在多个任务和数据集上经历了数千次微调迭代，其中许多数据集可以通过Hugging Face等平台公开下载[5]。

## **1.4 — 使用双向上下文**

作为一种语言模型，BERT预测在观察到前一个单词的基础上，某个词出现的概率。这一基本特性是所有语言模型所共有的，无论其架构或任务如何。然而，正是这些概率的使用赋予了模型任务特定的行为。例如，GPT被训练用来预测序列中下一个最可能的词。也就是说，给定已经观察到的前一个词，模型预测下一个词。其他模型可能被训练用于情感分析，预测输入序列的情感，使用诸如`positive`或`negative`之类的文本标签，等等。要对文本做出任何有意义的预测，都需要理解其上下文，尤其是在自然语言理解（NLU）任务中。BERT通过其关键特性之一——**双向性**——确保了对上下文的良好理解。

双向性可能是BERT最重要的特性，也是其在自然语言理解任务中表现出色的关键所在，同时也是推动其编码器架构的原因。虽然Transformer编码器的自注意力机制计算双向上下文，但解码器则生成**单向**上下文，不能做到这一点。BERT的作者认为，GPT缺乏双向性使其无法像BERT那样达到相同深度的语言表示。

**定义双向性：**

那么，究竟“双向”上下文是什么意思呢？在这里，双向性意味着输入序列中的每个词都可以从前后两个方向的词（分别称为左侧上下文和右侧上下文）获取上下文。在技术术语中，我们可以说，注意力机制可以**关注**每个词的前后词汇。为了进一步解释，回顾一下，BERT仅对输入序列中的词做出预测，而不像GPT那样生成新的序列。因此，当BERT预测输入序列中的某个词时，它可以结合周围词语的上下文线索。这为BERT提供了双向的上下文，帮助它做出更加准确的预测。

与仅使用解码器的模型（如GPT）进行对比，在这些模型中，目标是逐个预测新词，以生成输出序列。每个预测的词只能利用前面词语提供的上下文（左侧上下文），因为后续的词（右侧上下文）尚未生成。因此，这些模型被称为**单向**模型。

![](../Images/4f9eb3300724e98c4926d726a87ec3f1.png)

单向上下文和双向上下文的比较。图片来源：作者。

**图片解析：**

上图展示了一个典型的 BERT 任务示例，使用双向上下文，而一个典型的 GPT 任务示例则使用单向上下文。对于 BERT，这里的任务是预测被 `[MASK]` 标记的词。由于该词的两侧都有词语，左右两边的词语都可以用于提供上下文。如果你作为人类，只凭左侧或右侧的上下文来阅读这句话，可能会很难预测被遮掩的词。然而，使用双向上下文时，更有可能猜测出被遮掩的词是 `fishing`。

对于 GPT，目标是执行经典的自然语言推理（NTP）任务。在这种情况下，目标是基于输入序列提供的上下文以及输出中已生成的词语，生成一个新的序列。假设输入序列指示模型写一首诗，且当前已生成的词语是 `Upon a`，你可能会预测下一个词是 `river`，接着是 `bank`。由于有许多潜在的候选词，GPT（作为一种语言模型）会计算其词汇表中每个词语出现的概率，并根据其训练数据选择最有可能的词语之一。

## **1.5 — BERT 的局限性**

作为一个双向模型，BERT 存在两个主要缺点：

**增加的训练时间：**

基于 Transformer 的模型提出双向性作为对当时普遍存在的从左到右上下文模型的直接改进。其思想是 GPT 只能以单向的方式获取输入序列的上下文信息，因此缺乏对词语间因果关系的完整理解。然而，双向模型提供了对词语间因果联系的更广泛理解，因此可能在 NLU 任务中表现更好。尽管双向模型在过去曾被探索过，但它们的成功是有限的，正如 1990 年代末期的双向 RNN 所示 [6]。通常，这些模型需要更多的计算资源进行训练，因此，在相同的计算能力下，你可以训练一个更大的单向模型。

**语言生成中的差劲表现：**

BERT 专门设计用于解决自然语言理解（NLU）任务，选择了使用编码器和处理输入序列以深入理解的能力，而放弃了解码器和生成新序列的能力。因此，BERT 最适合处理一部分 NLP 任务，如命名实体识别（NER）、情感分析等。值得注意的是，BERT 不接受提示，而是处理序列来制定预测。虽然 BERT 在技术上可以生成新的输出序列，但我们需要认识到，在后 ChatGPT 时代，我们对大语言模型（LLMs）的理解与 BERT 设计之间的差异。

# 2 — 架构与预训练目标

## **2.1 — BERT 预训练目标概述**

训练一个双向模型需要允许使用左侧和右侧上下文进行预测的任务。因此，作者精心构造了两个预训练目标，以建立BERT对语言的理解。这两个任务是：**掩码语言模型**（MLM）任务和**下一个句子预测**（NSP）任务。每个任务的训练数据都是从当时可用的所有英文维基百科文章（25亿个单词）以及额外的11,038本来自BookCorpus数据集的书籍（8亿个单词）中抓取的[7]。然而，原始数据首先根据具体任务进行预处理，如下所述。

## **2.2 — 掩码语言建模（MLM）**

**MLM概述：**

掩码语言建模（Masked Language Modelling, MLM）任务的创建是为了直接解决训练双向模型的需求。为了实现这一点，模型必须被训练成能够利用输入序列的左侧上下文和右侧上下文来进行预测。具体做法是随机**掩盖**训练数据中15%的单词，并训练BERT预测缺失的单词。在输入序列中，被掩盖的单词将被替换为`[MASK]`标记。例如，假设原始训练数据中有句子`A man was fishing on the river`，当将原始文本转换为MLM任务的训练数据时，单词`fishing`可能会被随机掩盖并替换为`[MASK]`标记，得到训练输入`A man was [MASK] on the river`，目标是`fishing`。因此，BERT的目标是预测缺失的单词`fishing`，而不是用缺失的单词填补输入序列。掩盖过程可以对所有可能的输入序列（例如句子）重复执行，以建立MLM任务的训练数据。这个任务在语言学文献中早已存在，被称为**完形填空（Cloze）**任务[8]。然而，在机器学习领域，由于BERT的流行，它通常被称为MLM。

**缓解预训练与微调之间的不匹配：**

然而，作者指出，由于`[MASK]`标记只会出现在训练数据中，而不会出现在实际数据（推理时），因此会导致预训练与微调之间的不匹配。为了缓解这一问题，并非所有被掩盖的单词都会被替换为`[MASK]`标记。相反，作者表示：

> 训练数据生成器随机选择15%的标记位置进行预测。如果选择了第i个标记，我们将第i个标记替换为（1）80%的时间替换为`[MASK]`标记（2）10%的时间替换为一个随机标记（3）10%的时间保留不变。

**计算预测单词与目标单词之间的误差：**

BERT 将接受一个最大包含 512 个标记的输入序列，无论是 BERT Base 还是 BERT Large。如果序列中的标记少于最大数量，则会通过使用 `[PAD]` 标记进行填充，直到达到 512 的最大数量。输出标记的数量也将严格等于输入标记的数量。如果输入序列的第 *i* 个位置存在掩码标记，BERT 的预测将位于输出序列的第 *i* 个位置。其他所有标记将在训练过程中被忽略，因此模型权重和偏差的更新将基于预测标记和目标标记之间的误差进行计算。误差通过损失函数进行计算，通常使用交叉熵损失（负对数似然）函数，正如我们稍后将看到的那样。

## **2.3 — 下一句预测（NSP）**

**概述：**

BERT 的第二个预训练任务是下一句预测（Next Sentence Prediction, NSP），其目标是判断一个片段（通常是句子）是否在逻辑上紧跟另一个片段。选择 NSP 作为预训练任务，特意是为了补充 MLM（掩码语言模型）并增强 BERT 的自然语言理解（NLU）能力，作者表示：

> 许多重要的下游任务，如问答（QA）和自然语言推理（NLI），都基于理解两个句子之间的关系，而这一点并没有被语言模型直接捕捉到。

通过为 NSP 进行预训练，BERT 能够发展出对散文文本中句子之间流畅连接的理解——这一能力对于广泛的自然语言理解问题非常有用，例如：

+   释义中的句子对

+   逻辑蕴含中的假设-前提对

+   问题-段落对（Question Passage Pairs）在问答任务中的应用

**BERT 中实现 NSP：**

NSP 的输入由第一个和第二个片段（分别表示为 A 和 B）组成，两个片段之间用 `[SEP]` 标记分隔，末尾还会有第二个 `[SEP]` 标记。实际上，BERT 期望每个输入序列中至少有一个 `[SEP]` 标记，用以标示序列的结束，无论是否执行 NSP。因此，WordPiece 分词器将在 MLM 任务以及任何不包含 `[SEP]` 标记的非 NSP 任务中，将其中一个标记附加到输入的末尾。NSP 形成一个分类问题，当片段 A 逻辑上紧跟片段 B 时，输出为 `IsNext`；如果不是，则输出为 `NotNext`。训练数据可以通过从任何单语语料库中生成：将句子与其后一句配对 50%，其余 50% 的句子则与随机句子配对。

**2.4 — BERT 的输入嵌入**

BERT 的输入嵌入过程包括三个阶段：位置编码、片段嵌入和标记嵌入（如下图所示）。

**位置编码：**

就像Transformer模型一样，位置相关的信息被注入到每个标记的嵌入中。然而，与Transformer不同，BERT中的位置编码是固定的，并不是通过函数生成的。这意味着BERT在输入序列中限制为512个标记，无论是BERT Base还是BERT Large。

**片段嵌入：**

每个标记所属片段的向量也会被加入。对于MLM预训练任务或任何其他非NSP任务（这些任务只包含一个`[SEP]`标记），输入中的所有标记都被视为属于片段A。对于NSP任务，第二个`[SEP]`之后的所有标记都被表示为片段B。

**标记嵌入：**

与原始的Transformer一样，每个标记学习到的嵌入随后会与其位置和片段向量相加，创建最终的嵌入，这将传递给BERT中的自注意力机制，以加入上下文信息。

![](../Images/adef59c49c388ce9dbe42aa2c153829f.png)

BERT嵌入过程的概述。图像来自BERT论文[1]。

## **2.5 — 特殊标记**

在上面的图像中，你可能注意到输入序列前面被加上了一个`[CLS]`（分类）标记。这个标记被添加用来封装整个输入序列的语义总结，帮助BERT执行分类任务。例如，在情感分析任务中，最终层中的`[CLS]`标记可以被分析以提取对输入序列情感是`正面`还是`负面`的预测。`[CLS]`和`[PAD]`等是BERT的**特殊标记**。这里需要注意的是，这是BERT特有的功能，因此你不应该期待在像GPT这样的模型中看到这些特殊标记。总的来说，BERT有五个特殊标记。下面提供了一个总结：

+   `[PAD]`（标记ID：`0`）——一个填充标记，用于将输入序列中的总标记数填充至512。

+   `[UNK]`（标记ID：`100`）——一个未知标记，用于表示BERT词汇表中没有的标记。

+   `[CLS]`（标记ID：`101`）——一个分类标记，预期每个序列的开头都会有一个，无论是否使用。这个标记封装了分类任务的类信息，可以被认为是一个聚合的序列表示。

+   `[SEP]`（标记ID：`102`）——一个分隔符标记，用于区分单个输入序列中的两个片段（例如，在下一个句子预测任务中）。每个输入序列至少应该有一个`[SEP]`标记，最多可以有两个。

+   `[MASK]`（标记ID：`103`）——一个掩码标记，用于在掩码语言建模任务中训练BERT，或在掩码序列上执行推理。

## **2.4 — BERT Base和BERT Large的架构对比**

从架构角度来看，BERT Base 和 BERT Large 非常相似，正如你所期望的那样。它们都使用 WordPiece 分词器（因此也期望与前面描述的相同的特殊符号），并且都具有最大 512 个词元的序列长度。BERT 的词汇表大小为 30,522，其中大约 1,000 个词元被标记为“未使用”。这些未使用的词元故意留空，以允许用户添加自定义词元，而无需重新训练整个分词器。这在处理特定领域的词汇时非常有用，比如医学和法律术语。BERT Base 和 BERT Large 都具有比原始 Transformer 更高的嵌入维度（*d_model*）。这对应于模型词汇中每个词元的学习向量表示的大小。对于 BERT Base，*d_model* = 768；对于 BERT Large，*d_model* = 1024（是原始 Transformer 512 的两倍）。

这两个模型主要在四个类别上有所不同：

+   **编码器块数量，** `**N**`**：** 堆叠在一起的编码器块的数量。

+   **每个编码器块的注意力头数：** 注意力头计算输入序列的上下文向量嵌入。由于 BERT 使用多头注意力，这个值指的是每个编码器层的头数。

+   **前馈网络中隐藏层的大小：** 线性层由一个隐藏层组成，该隐藏层具有固定数量的神经元（例如 BERT Base 为 3072），然后传递到输出层，输出层的大小可以有所不同。输出层的大小取决于任务。例如，二分类问题只需要两个输出神经元，而一个有十个类别的多分类问题则需要十个神经元，以此类推。

+   **总参数量：** 模型中所有权重和偏置的总数。在当时，具有数亿参数的模型被认为非常大。然而，以今天的标准来看，这些值相对较小。

下图展示了 BERT Base 和 BERT Large 在这些类别中的对比。

![](../Images/8af52573f39fd836601eef13baf77957.png)

BERT Base 和 BERT Large 的对比。图片来自作者。

# 3 — 微调 BERT 进行情感分析

本节涵盖了在 Python 中微调 BERT 的实际示例。代码采用任务无关的微调管道形式，使用 Python 类实现。接着，我们将实例化该类的对象，并用它来微调 BERT 模型以进行情感分析任务。该类还可以重复使用，用于微调 BERT 完成其他任务，如问答、命名实体识别等。**第 3.1 节到 3.5 节逐步讲解了微调过程，第 3.6 节展示了完整的管道。**

## **3.1 — 加载并预处理微调数据集**

微调的第一步是选择一个适合特定任务的数据集。在这个示例中，我们将使用斯坦福大学提供的情感分析数据集。该数据集包含来自互联网电影数据库（IMDb）的50,000条在线电影评论，每条评论标注为`正面`或`负面`。您可以直接从[斯坦福大学网站](https://ai.stanford.edu/~amaas/data/sentiment/)下载该数据集，或者您可以在[Kaggle](https://www.kaggle.com/datasets/lakshmi25npathi/imdb-dataset-of-50k-movie-reviews)上创建一个笔记本，并将您的工作与他人进行比较。

```py
import pandas as pd

df = pd.read_csv('IMDB Dataset.csv')
df.head()
```

![](../Images/dbc783b927e31a86bcb1bf6cc62aeb94.png)

显示在Pandas DataFrame中的IMDb数据集的前五行。图片来自作者。

与早期的NLP模型不同，基于Transformer的模型（如BERT）需要的预处理工作最小化。诸如去除停用词和标点符号等步骤，在某些情况下可能适得其反，因为这些元素为BERT提供了理解输入句子的宝贵上下文。尽管如此，仍然需要检查文本以查找任何格式问题或不必要的字符。总体而言，IMDb数据集相当干净。然而，似乎有一些抓取过程的遗留物，例如HTML换行标签（`<br />`）和不必要的空白，应该去除。

```py
# Remove the break tags (<br />)
df['review_cleaned'] = df['review'].apply(lambda x: x.replace('<br />', ''))

# Remove unnecessary whitespace
df['review_cleaned'] = df['review_cleaned'].replace('\s+', ' ', regex=True)

# Compare 72 characters of the second review before and after cleaning
print('Before cleaning:')
print(df.iloc[1]['review'][0:72])

print('\nAfter cleaning:')
print(df.iloc[1]['review_cleaned'][0:72])
```

```py
Before cleaning:
A wonderful little production. <br /><br />The filming technique is very

After cleaning:
A wonderful little production. The filming technique is very unassuming-
```

**编码情感：**

预处理的最后一步是将每个评论的情感编码为`0`表示`负面`或`1`表示`正面`。这些标签将用于稍后在微调过程中训练分类头。

```py
df['sentiment_encoded'] = df['sentiment'].\
    apply(lambda x: 0 if x == 'negative' else 1)
df.head()
```

![](../Images/f69c08f131a1534e3b654a28f0a7d813.png)

编码情感列后的IMDb数据集前五行。图片来自作者。

## **3.2 — 分词微调数据**

一旦预处理完成，微调数据就可以进行分词处理。这个过程：将评论文本拆分成单独的标记，添加`[CLS]`和`[SEP]`特殊标记，并处理填充。选择合适的分词器非常重要，因为不同的语言模型需要不同的分词步骤（例如，GPT不需要`[CLS]`和`[SEP]`标记）。我们将使用来自Hugging Face `transformers`库的`BertTokenizer`类，它是专为BERT模型设计的。有关分词如何工作的更深入讨论，请参见[本系列的第1部分](https://medium.com/p/cedc9f72de4e)。

`transformers`库中的分词器类提供了一种简单的方法，可以使用`from_pretrained`方法创建预训练的分词器模型。使用此功能的方法是：导入并实例化一个分词器类，调用`from_pretrained`方法，并传入一个包含在Hugging Face模型库中托管的分词器模型名称的字符串。或者，你可以传入一个包含分词器所需词汇文件的目录的路径[9]。在我们的示例中，我们将使用来自模型库的预训练分词器。在使用BERT时，有四个主要选项，每个选项都使用来自Google预训练分词器的词汇表。这些选项是：

+   `bert-base-uncased` — BERT的较小版本的词汇表，它对大小写不敏感（例如，`Cat`和`cat`会被视为相同的标记）

+   `bert-base-cased` — BERT的较小版本的词汇表，它对大小写敏感（例如，`Cat`和`cat`不会被视为相同的标记）

+   `bert-large-uncased` — BERT的较大版本的词汇表，它对大小写不敏感（例如，`Cat`和`cat`会被视为相同的标记）

+   `bert-large-cased` — BERT的较大版本的词汇表，它对大小写敏感（例如，`Cat`和`cat`不会被视为相同的标记）

BERT Base和BERT Large使用相同的词汇表，因此`bert-base-uncased`和`bert-large-uncased`之间实际上没有区别，`bert-base-cased`和`bert-large-cased`之间也没有区别。对于其他模型来说，情况可能不同，因此如果不确定，最好使用相同的分词器和模型大小。

**何时使用** `**cased**` **与** `**uncased**`**:**

在使用`cased`和`uncased`之间的选择取决于数据集的性质。IMDb数据集包含由互联网用户编写的文本，这些用户在使用大写字母时可能不一致。例如，一些用户可能会忽略应有的大写字母，或者为了强调情感（如表达兴奋、沮丧等）使用大写字母。因此，我们将选择忽略大小写，并使用`bert-base-uncased`分词器模型。

在其他情况下，通过考虑大小写可能会获得性能上的优势。一个例子可能是在命名实体识别任务中，目标是识别输入文本中的实体，如人名、组织名、地点等。在这种情况下，大写字母的存在可以非常有助于识别一个单词是否为某个人的名字或一个地方的名称，因此在这种情况下，选择`bert-base-cased`可能更为合适。

```py
from transformers import BertTokenizer

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
print(tokenizer)
```

```py
BertTokenizer(
  name_or_path='bert-base-uncased',
  vocab_size=30522,
  model_max_length=512,
  is_fast=False,
  padding_side='right',
  truncation_side='right',
  special_tokens={
    'unk_token': '[UNK]',
    'sep_token': '[SEP]',
    'pad_token': '[PAD]',
    'cls_token': '[CLS]',
    'mask_token': '[MASK]'},
  clean_up_tokenization_spaces=True),

added_tokens_decoder={
  0: AddedToken(
    "[PAD]",
    rstrip=False,
    lstrip=False,
    single_word=False,
    normalized=False,  
    special=True),

  100: AddedToken(
    "[UNK]",
    rstrip=False,
    lstrip=False,
    single_word=False,
    normalized=False,
    special=True),

  101: AddedToken(
    "[CLS]",
    rstrip=False,
    lstrip=False,
    single_word=False,
    normalized=False,
    special=True),

  102: AddedToken(
    "[SEP]",
    rstrip=False,
    lstrip=False,
    single_word=False,
    normalized=False,
    special=True),

  103: AddedToken(
    "[MASK]",
    rstrip=False,
    lstrip=False,
    single_word=False,
    normalized=False,
    special=True),
  }
```

**编码过程：将文本转换为标记再转换为标记ID**

接下来，可以使用分词器对清理后的微调数据进行编码。这个过程会将每条评论转换为一个标记ID的张量。例如，评论`I liked this movie`将通过以下步骤进行编码：

1. 将评论转换为小写（因为我们使用的是`bert-base-uncased`）

2\. 根据`bert-base-uncased`词汇表将评论拆分为单独的token：`['i', 'liked', 'this', 'movie']`

2\. 添加BERT期望的特殊token：`['[CLS]', 'i', 'liked', 'this', 'movie', '[SEP]']`

3\. 将token转换为它们的token ID，同样根据`bert-base-uncased`词汇表（例如`[CLS]` -> `101`，`i` -> `1045`，等等）

`BertTokenizer`类的`encode`方法使用上述过程对文本进行编码，并可以返回PyTorch张量、TensorFlow张量或NumPy数组格式的token ID张量。返回张量的数据类型可以通过`return_tensors`参数来指定，取值为：`pt`、`tf`和`np`，分别对应PyTorch、TensorFlow和NumPy。

> **注意：** 在Hugging Face中，token ID通常被称为`input IDs`，因此你可能会看到这两个术语交替使用。

```py
# Encode a sample input sentence
sample_sentence = 'I liked this movie'
token_ids = tokenizer.encode(sample_sentence, return_tensors='np')[0]
print(f'Token IDs: {token_ids}')

# Convert the token IDs back to tokens to reveal the special tokens added
tokens = tokenizer.convert_ids_to_tokens(token_ids)
print(f'Tokens   : {tokens}')
```

```py
Token IDs: [ 101 1045 4669 2023 3185  102]
Tokens   : ['[CLS]', 'i', 'liked', 'this', 'movie', '[SEP]']
```

**截断与填充：**

BERT Base和BERT Large都设计为处理恰好512个token的输入序列。但如果输入序列不符合这个限制，怎么办呢？答案是截断和填充！截断通过简单地删除超过特定长度的token来减少token的数量。在`encode`方法中，你可以将`truncation`设置为`True`，并指定一个`max_length`参数来强制对所有编码序列施加长度限制。数据集中有几条评论超过了512个token的限制，因此这里的`max_length`参数被设置为512，以从所有评论中提取尽可能多的文本。如果没有评论超过512个token，可以不设置`max_length`，它将默认使用模型的最大长度。或者，你也可以强制设置一个小于512的最大长度，从而在微调过程中减少训练时间，尽管这样会影响模型性能。对于长度小于512个token的评论（大多数评论是这样），会添加填充token，以将编码后的评论扩展到512个token。可以通过将填充`parameter`设置为`max_length`来实现。更多关于`encode`方法的细节，请参阅Hugging Face文档[10]。

```py
review = df['review_cleaned'].iloc[0]

token_ids = tokenizer.encode(
    review,
    max_length = 512,
    padding = 'max_length',
    truncation = True,
    return_tensors = 'pt')

print(token_ids)
```

```py
tensor([[  101,  2028,  1997,  1996,  2060, 15814,  2038,  3855,  2008,  2044,
          3666,  2074,  1015, 11472,  2792,  2017,  1005,  2222,  2022, 13322,

                                       ...

             0,     0,     0,     0,     0,     0,     0,     0,     0,     0,
             0,     0,     0,     0,     0,     0,     0,     0,     0,     0,
             0,     0]])
```

**使用注意力掩码与** `**encode_plus**`**：**

上面的示例展示了数据集中第一个评论的编码，其中包含119个填充token。如果直接用于微调，BERT可能会关注到这些填充token，从而可能导致性能下降。为了解决这个问题，我们可以应用一个注意力掩码，指示BERT忽略输入中的某些token（在这个例子中是填充token）。我们可以通过修改上面的代码，使用`encode_plus`方法，而不是标准的`encode`方法来生成这个注意力掩码。`encode_plus`方法返回一个字典（在Hugging Face中称为Batch Encoder），其中包含以下键：

+   `input_ids` — 与标准`encode`方法返回的token ID相同

+   `token_type_ids` — 用于区分句子A（id = 0）和句子B（id = 1）的段落ID，常用于下一个句子预测等句子对任务

+   `attention_mask` — 一个由0和1组成的列表，其中0表示在注意力过程中应该忽略该标记，而1表示该标记不应被忽略

```py
review = df['review_cleaned'].iloc[0]

batch_encoder = tokenizer.encode_plus(
    review,
    max_length = 512,
    padding = 'max_length',
    truncation = True,
    return_tensors = 'pt')

print('Batch encoder keys:')
print(batch_encoder.keys())

print('\nAttention mask:')
print(batch_encoder['attention_mask'])
```

```py
Batch encoder keys:
dict_keys(['input_ids', 'token_type_ids', 'attention_mask'])

Attention mask:
tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
         1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,

                                      ...

         0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
         0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
         0, 0, 0, 0, 0, 0, 0, 0]])
```

**编码所有评论：**

标记化阶段的最后一步是编码数据集中的所有评论，并将标记ID和对应的注意力掩码存储为张量。

```py
import torch

token_ids = []
attention_masks = []

# Encode each review
for review in df['review_cleaned']:
    batch_encoder = tokenizer.encode_plus(
        review,
        max_length = 512,
        padding = 'max_length',
        truncation = True,
        return_tensors = 'pt')

    token_ids.append(batch_encoder['input_ids'])
    attention_masks.append(batch_encoder['attention_mask'])

# Convert token IDs and attention mask lists to PyTorch tensors
token_ids = torch.cat(token_ids, dim=0)
attention_masks = torch.cat(attention_masks, dim=0)
```

## **3.3 — 创建训练和验证DataLoader**

现在每条评论都已编码完成，我们可以将数据分割为训练集和验证集。验证集将用于在微调过程中评估其效果，使我们能够在整个过程中监控模型的性能。我们期望随着模型在**epochs**中进一步微调，损失下降（因此模型准确性提高）。一个epoch指的是对训练数据的完整遍历。BERT的作者推荐进行2到4个epochs的微调[1]，意味着分类头将看到每条评论2到4次。

为了对数据进行划分，我们可以使用SciKit-Learn的`model_selection`包中的`train_test_split`函数。此函数需要我们打算划分的数据集、分配给测试集（或在我们的案例中是验证集）的项目百分比，以及一个可选参数，指示数据是否应随机打乱。为了确保可重复性，我们将`shuffle`参数设置为`False`。对于`test_size`，我们选择一个较小的值0.1（相当于10%）。在使用足够的数据来验证模型并准确评估其性能与保留足够数据来训练模型并提高其性能之间，需要找到平衡。因此，通常更倾向于选择较小的值，如`0.1`。在分割完标记ID、注意力掩码和标签后，我们可以将训练和验证的张量组合在一起，使用PyTorch的TensorDatasets。然后，我们可以通过将这些TensorDatasets分批来创建用于训练和验证的PyTorch DataLoader类。BERT论文推荐使用16或32的批量大小（即在重新计算分类头中的权重和偏置之前，模型需要先看到16条评论及其对应的情感标签）。使用DataLoader将使我们能够在微调过程中有效地加载数据，通过利用多个CPU核心进行并行化[11]。

```py
from sklearn.model_selection import train_test_split
from torch.utils.data import TensorDataset, DataLoader

val_size = 0.1

# Split the token IDs
train_ids, val_ids = train_test_split(
                        token_ids,
                        test_size=val_size,
                        shuffle=False)

# Split the attention masks
train_masks, val_masks = train_test_split(
                            attention_masks,
                            test_size=val_size,
                            shuffle=False)

# Split the labels
labels = torch.tensor(df['sentiment_encoded'].values)
train_labels, val_labels = train_test_split(
                                labels,
                                test_size=val_size,
                                shuffle=False)

# Create the DataLoaders
train_data = TensorDataset(train_ids, train_masks, train_labels)
train_dataloader = DataLoader(train_data, shuffle=True, batch_size=16)
val_data = TensorDataset(val_ids, val_masks, val_labels)
val_dataloader = DataLoader(val_data, batch_size=16)
```

## **3.4 — 实例化一个BERT模型**

下一步是加载一个预训练的BERT模型以供我们进行微调。我们可以像导入标记器一样，从Hugging Face模型库中导入一个模型。Hugging Face有许多带有预配置分类头的BERT版本，这使得这个过程非常便捷。带有预配置分类头的模型示例包括：

+   `BertForMaskedLM`

+   `BertForNextSentencePrediction`

+   `BertForSequenceClassification`

+   `BertForMultipleChoice`

+   `BertForTokenClassification`

+   `BertForQuestionAnswering`

当然，你也可以在PyTorch或TensorFlow中导入一个无头BERT模型，并从头开始创建自己的分类头。然而，在我们的案例中，我们可以简单地导入`BertForSequenceClassification`模型，因为它已经包含了我们需要的线性层。这个线性层是使用随机权重和偏置初始化的，这些将在微调过程中进行训练。由于BERT Base使用768个嵌入维度，隐藏层包含768个神经元，这些神经元与模型的最终编码器块相连。输出神经元的数量由`num_labels`参数决定，并对应于唯一情感标签的数量。IMDb数据集只有`positive`和`negative`两类情感标签，因此`num_labels`参数被设置为`2`。对于更复杂的情感分析，可能包括如`neutral`或`mixed`等标签，我们可以简单地增大或减小`num_labels`的值。

> **注意：** 如果你有兴趣查看预配置的模型如何在源代码中编写，Hugging Face的transformers库中的`modelling_bert.py`文件展示了如何加载一个无头BERT模型并添加线性层[12]。线性层是在每个类的`__init__`方法中添加的。

```py
from transformers import BertForSequenceClassification

model = BertForSequenceClassification.from_pretrained(
    'bert-base-uncased',
    num_labels=2)
```

## **3.5 — 实例化优化器、损失函数和调度器**

**优化器：**

在分类头处理完一批训练数据后，它会更新线性层中的权重和偏置，以提高模型在这些输入上的表现。在多个批次和多个训练轮次中，目标是让这些权重和偏置收敛到最优值。**优化器**用于计算每个权重和偏置所需的变化，可以从PyTorch的`optim`包中导入。Hugging Face在其示例中使用的是AdamW优化器，因此我们在这里也将使用该优化器[13]。

**损失函数：**

优化器通过确定分类头中权重和偏置的变化如何影响与一个称为**损失函数**的评分函数的损失来工作。损失函数可以很容易地从PyTorch的`nn`包中导入，如下所示。语言模型通常使用交叉熵损失函数（也称为负对数似然函数），因此这也是我们在此使用的损失函数。

**调度器：**

一个叫做**学习率**的参数用于确定分类头中权重和偏置变化的大小。在早期的批次和训练轮次中，较大的变化可能会证明是有利的，因为随机初始化的参数可能需要大量的调整。然而，随着训练的进行，权重和偏置趋于改善，过大的变化可能会适得其反。调度器的设计是随着训练的进行，逐渐减少学习率，从而减少每次优化步骤中对每个权重和偏置的变化大小。

```py
from torch.optim import AdamW
import torch.nn as nn
from transformers import get_linear_schedule_with_warmup

EPOCHS = 2

# Optimizer
optimizer = AdamW(model.parameters())

# Loss function
loss_function = nn.CrossEntropyLoss()

# Scheduler
num_training_steps = EPOCHS * len(train_dataloader)
scheduler = get_linear_schedule_with_warmup(
    optimizer,
    num_warmup_steps=0,
    num_training_steps=num_training_steps)
```

## **3.6 — 微调循环**

**使用带CUDA的GPU：**

计算统一设备架构（CUDA）是由NVIDIA创建的计算平台，旨在提升各个领域应用的性能，如科学计算和工程应用[14]。PyTorch的`cuda`包允许开发者在Python中利用CUDA平台，并使用图形处理单元（GPU）进行加速计算，从而在训练机器学习模型时提高计算效率。可以使用`torch.cuda.is_available`命令来检查GPU是否可用。如果不可用，代码会默认使用中央处理单元（CPU），但需要注意，这将导致训练时间变长。在随后的代码片段中，我们将使用PyTorch的`Tensor.to`方法将张量（包含模型权重和偏置等）转移到GPU上，以加快计算速度。如果设备设置为`cpu`，则张量将不会被移动，代码也不会受到影响。

```py
# Check if GPU is available for faster training time
if torch.cuda.is_available():
    device = torch.device('cuda:0')
else:
    device = torch.device('cpu')
```

训练过程将通过两个for循环进行：一个外循环，用于为每个epoch重复训练过程（这样模型可以多次看到所有训练数据）；一个内循环，用于对每个批次重复进行损失计算和优化步骤。为了解释训练循环，可以参考下面的步骤。训练循环的代码改编自Chris McCormick和Nick Ryan的这篇精彩博客文章[15]，我强烈推荐大家阅读。

**对于每个epoch：**

1\. 使用模型对象的`train`方法将模型切换到训练模式。这将导致模型在训练模式下的行为与评估模式时有所不同，特别是在处理批归一化（batchnorm）和丢弃层（dropout layers）时非常有用。如果你之前查看过`BertForSequenceClassification`类的源代码，可能已经注意到分类头确实包含一个丢弃层，因此，在微调过程中正确区分训练模式和评估模式是非常重要的。这些层只应在训练时启用，而不应在推理时启用，因此能够在训练和推理模式之间切换是一个非常有用的功能。

2\. 在epoch开始时将训练损失设为0。这个损失值用于跟踪模型在后续epoch中对训练数据的表现。如果训练成功，损失应该会随着每个epoch的进行而减少。

**对于每个批次：**

根据BERT作者的建议，每个epoch的训练数据会被分成若干个批次。对于每个批次，重复训练过程。

3\. 如果GPU可用，将token ID、注意力掩码和标签移动到GPU上以加速处理，否则这些数据将保留在CPU上。

4\. 调用`zero_grad`方法来重置上一轮迭代计算出的梯度。虽然在PyTorch中这不是默认行为，但一些原因解释了为什么像循环神经网络（RNN）这样的模型需要在迭代之间保留梯度，不能重置。

5\. 将批次数据传递给模型以计算logits（基于当前分类器权重和偏置的预测值）以及损失值。

6\. 增加该轮次的总损失值。损失值由模型以PyTorch张量形式返回，因此使用`item`方法提取浮动值。

7\. 执行模型的反向传播，并通过分类器头传播损失。这将使模型确定需要对权重和偏置进行哪些调整，以提高其在当前批次上的表现。

8\. 将梯度裁剪到最大值为1.0，以避免模型遭遇梯度爆炸问题。

9\. 调用优化器，根据反向传播计算的误差面朝向进行一步优化。

**每个批次训练后：**

10\. 计算训练过程中每轮的平均损失值和训练时间。

```py
for epoch in range(0, EPOCHS):

    model.train()
    training_loss = 0

    for batch in train_dataloader:

        batch_token_ids = batch[0].to(device)
        batch_attention_mask = batch[1].to(device)
        batch_labels = batch[2].to(device)

        model.zero_grad()

        loss, logits = model(
            batch_token_ids,
            token_type_ids = None,
            attention_mask=batch_attention_mask,
            labels=batch_labels,
            return_dict=False)

        training_loss += loss.item()
        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        optimizer.step()
        scheduler.step()

    average_train_loss = training_loss / len(train_dataloader)
```

验证步骤发生在外层循环中，因此会为每个epoch计算平均验证损失。随着epoch数的增加，我们预计验证损失会减少，分类器准确率会提高。验证过程的步骤如下所示。

**本轮验证步骤：**

11\. 使用`eval`方法将模型切换到评估模式——这将禁用dropout层。

12\. 将验证损失初始化为0。这用于追踪模型在后续轮次中在验证数据上的损失。若训练成功，损失应随着每轮的进行而减小。

13\. 将验证数据拆分成若干批次。

**每个批次：**

14\. 如果可用，将token IDs、注意力掩码和标签移动到GPU上以加速处理，否则这些数据将保留在CPU上。

15\. 调用`no_grad`方法，指示模型不要计算梯度，因为在这里我们不会进行任何优化步骤，只进行推理。

16\. 将批次数据传递给模型以计算logits（基于当前分类器权重和偏置的预测值）以及损失值。

17\. 从模型中提取logits和标签，并将它们移动到CPU（如果它们尚未在CPU上）。

18\. 增加损失值并根据验证数据加载器中的真实标签计算准确率。

19\. 计算平均损失和准确率。

```py
 model.eval()
    val_loss = 0
    val_accuracy = 0

    for batch in val_dataloader:

        batch_token_ids = batch[0].to(device)
        batch_attention_mask = batch[1].to(device)
        batch_labels = batch[2].to(device)

        with torch.no_grad():
            (loss, logits) = model(
                batch_token_ids,
                attention_mask = batch_attention_mask,
                labels = batch_labels,
                token_type_ids = None,
                return_dict=False)

        logits = logits.detach().cpu().numpy()
        label_ids = batch_labels.to('cpu').numpy()
        val_loss += loss.item()
        val_accuracy += calculate_accuracy(logits, label_ids)

    average_val_accuracy = val_accuracy / len(val_dataloader)
```

上面代码片段倒数第二行使用了我们尚未定义的函数`calculate_accuracy`，所以现在让我们来定义它。模型在验证集上的准确度由正确预测的比例给出。因此，我们可以取模型生成的logits，这些值存储在变量`logits`中，并使用NumPy中的`argmax`函数。`argmax`函数将简单地返回数组中最大元素的索引。如果文本`I liked this movie`的logits是`[0.08, 0.92]`，其中`0.08`表示文本是`negative`的概率，`0.92`表示文本是`positive`的概率，那么`argmax`函数将返回索引`1`，因为模型认为该文本更可能是正面的而不是负面的。然后，我们可以将标签`1`与我们在第3.3节（第19行）之前编码的`labels`张量进行比较。由于`logits`变量将包含批次中每个评论的正负概率值（总共16个），因此模型的准确度将从最多16个正确预测中计算得出。上面单元格中的代码显示了`val_accuracy`变量跟踪每个准确度得分，我们在验证结束时对其进行除法运算，以确定模型在验证数据上的平均准确度。

```py
def calculate_accuracy(preds, labels):
    """ Calculate the accuracy of model predictions against true labels.

    Parameters:
        preds (np.array): The predicted label from the model
        labels (np.array): The true label

    Returns:
        accuracy (float): The accuracy as a percentage of the correct
            predictions.
    """
    pred_flat = np.argmax(preds, axis=1).flatten()
    labels_flat = labels.flatten()
    accuracy = np.sum(pred_flat == labels_flat) / len(labels_flat)

    return accuracy
```

## **3.7 — 完整的微调管道**

至此，我们已经完成了微调的解释！下面的代码将上述所有内容汇总成一个单一、可重用的类，可以用于BERT的任何NLP任务。由于数据预处理步骤依赖于任务，因此它已被移出微调类之外。

**情感分析的预处理函数（使用IMDb数据集）：**

```py
def preprocess_dataset(path):
    """ Remove unnecessary characters and encode the sentiment labels.

    The type of preprocessing required changes based on the dataset. For the
    IMDb dataset, the review texts contains HTML break tags (<br/>) leftover
    from the scraping process, and some unnecessary whitespace, which are
    removed. Finally, encode the sentiment labels as 0 for "negative" and 1 for
    "positive". This method assumes the dataset file contains the headers
    "review" and "sentiment".

    Parameters:
        path (str): A path to a dataset file containing the sentiment analysis
            dataset. The structure of the file should be as follows: one column
            called "review" containing the review text, and one column called
            "sentiment" containing the ground truth label. The label options
            should be "negative" and "positive".

    Returns:
        df_dataset (pd.DataFrame): A DataFrame containing the raw data
            loaded from the self.dataset path. In addition to the expected
            "review" and "sentiment" columns, are:

            > review_cleaned - a copy of the "review" column with the HTML
                break tags and unnecessary whitespace removed

            > sentiment_encoded - a copy of the "sentiment" column with the
                "negative" values mapped to 0 and "positive" values mapped
                to 1
    """
    df_dataset = pd.read_csv(path)

    df_dataset['review_cleaned'] = df_dataset['review'].\
        apply(lambda x: x.replace('<br />', ''))

    df_dataset['review_cleaned'] = df_dataset['review_cleaned'].\
        replace('\s+', ' ', regex=True)

    df_dataset['sentiment_encoded'] = df_dataset['sentiment'].\
        apply(lambda x: 0 if x == 'negative' else 1)

    return df_dataset
```

**任务无关的微调管道类：**

```py
from datetime import datetime
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.optim import AdamW
from torch.utils.data import TensorDataset, DataLoader
from transformers import (
    BertForSequenceClassification,
    BertTokenizer,
    get_linear_schedule_with_warmup)

class FineTuningPipeline:

    def __init__(
            self,
            dataset,
            tokenizer,
            model,
            optimizer,
            loss_function = nn.CrossEntropyLoss(),
            val_size = 0.1,
            epochs = 4,
            seed = 42):

        self.df_dataset = dataset
        self.tokenizer = tokenizer
        self.model = model
        self.optimizer = optimizer
        self.loss_function = loss_function
        self.val_size = val_size
        self.epochs = epochs
        self.seed = seed

        # Check if GPU is available for faster training time
        if torch.cuda.is_available():
            self.device = torch.device('cuda:0')
        else:
            self.device = torch.device('cpu')

        # Perform fine-tuning
        self.model.to(self.device)
        self.set_seeds()
        self.token_ids, self.attention_masks = self.tokenize_dataset()
        self.train_dataloader, self.val_dataloader = self.create_dataloaders()
        self.scheduler = self.create_scheduler()
        self.fine_tune()

    def tokenize(self, text):
        """ Tokenize input text and return the token IDs and attention mask.

        Tokenize an input string, setting a maximum length of 512 tokens.
        Sequences with more than 512 tokens will be truncated to this limit,
        and sequences with less than 512 tokens will be supplemented with [PAD]
        tokens to bring them up to this limit. The datatype of the returned
        tensors will be the PyTorch tensor format. These return values are
        tensors of size 1 x max_length where max_length is the maximum number
        of tokens per input sequence (512 for BERT).

        Parameters:
            text (str): The text to be tokenized.

        Returns:
            token_ids (torch.Tensor): A tensor of token IDs for each token in
                the input sequence.

            attention_mask (torch.Tensor): A tensor of 1s and 0s where a 1
                indicates a token can be attended to during the attention
                process, and a 0 indicates a token should be ignored. This is
                used to prevent BERT from attending to [PAD] tokens during its
                training/inference.
        """
        batch_encoder = self.tokenizer.encode_plus(
            text,
            max_length = 512,
            padding = 'max_length',
            truncation = True,
            return_tensors = 'pt')

        token_ids = batch_encoder['input_ids']
        attention_mask = batch_encoder['attention_mask']

        return token_ids, attention_mask

    def tokenize_dataset(self):
        """ Apply the self.tokenize method to the fine-tuning dataset.

        Tokenize and return the input sequence for each row in the fine-tuning
        dataset given by self.dataset. The return values are tensors of size
        len_dataset x max_length where len_dataset is the number of rows in the
        fine-tuning dataset and max_length is the maximum number of tokens per
        input sequence (512 for BERT).

        Parameters:
            None.

        Returns:
            token_ids (torch.Tensor): A tensor of tensors containing token IDs
            for each token in the input sequence.

            attention_masks (torch.Tensor): A tensor of tensors containing the
                attention masks for each sequence in the fine-tuning dataset.
        """
        token_ids = []
        attention_masks = []

        for review in self.df_dataset['review_cleaned']:
            tokens, masks = self.tokenize(review)
            token_ids.append(tokens)
            attention_masks.append(masks)

        token_ids = torch.cat(token_ids, dim=0)
        attention_masks = torch.cat(attention_masks, dim=0)

        return token_ids, attention_masks

    def create_dataloaders(self):
        """ Create dataloaders for the train and validation set.

        Split the tokenized dataset into train and validation sets according to
        the self.val_size value. For example, if self.val_size is set to 0.1,
        90% of the data will be used to form the train set, and 10% for the
        validation set. Convert the "sentiment_encoded" column (labels for each
        row) to PyTorch tensors to be used in the dataloaders.

        Parameters:
            None.

        Returns:
            train_dataloader (torch.utils.data.dataloader.DataLoader): A
                dataloader of the train data, including the token IDs,
                attention masks, and sentiment labels.

            val_dataloader (torch.utils.data.dataloader.DataLoader): A
                dataloader of the validation data, including the token IDs,
                attention masks, and sentiment labels.

        """
        train_ids, val_ids = train_test_split(
                        self.token_ids,
                        test_size=self.val_size,
                        shuffle=False)

        train_masks, val_masks = train_test_split(
                                    self.attention_masks,
                                    test_size=self.val_size,
                                    shuffle=False)

        labels = torch.tensor(self.df_dataset['sentiment_encoded'].values)
        train_labels, val_labels = train_test_split(
                                        labels,
                                        test_size=self.val_size,
                                        shuffle=False)

        train_data = TensorDataset(train_ids, train_masks, train_labels)
        train_dataloader = DataLoader(train_data, shuffle=True, batch_size=16)
        val_data = TensorDataset(val_ids, val_masks, val_labels)
        val_dataloader = DataLoader(val_data, batch_size=16)

        return train_dataloader, val_dataloader

    def create_scheduler(self):
        """ Create a linear scheduler for the learning rate.

        Create a scheduler with a learning rate that increases linearly from 0
        to a maximum value (called the warmup period), then decreases linearly
        to 0 again. num_warmup_steps is set to 0 here based on an example from
        Hugging Face:

        https://github.com/huggingface/transformers/blob/5bfcd0485ece086ebcbed2
        d008813037968a9e58/examples/run_glue.py#L308

        Read more about schedulers here:

        https://huggingface.co/docs/transformers/main_classes/optimizer_
        schedules#transformers.get_linear_schedule_with_warmup
        """
        num_training_steps = self.epochs * len(self.train_dataloader)
        scheduler = get_linear_schedule_with_warmup(
            self.optimizer,
            num_warmup_steps=0,
            num_training_steps=num_training_steps)

        return scheduler

    def set_seeds(self):
        """ Set the random seeds so that results are reproduceable.

        Parameters:
            None.

        Returns:
            None.
        """
        np.random.seed(self.seed)
        torch.manual_seed(self.seed)
        torch.cuda.manual_seed_all(self.seed)

    def fine_tune(self):
        """Train the classification head on the BERT model.

        Fine-tune the model by training the classification head (linear layer)
        sitting on top of the BERT model. The model trained on the data in the
        self.train_dataloader, and validated at the end of each epoch on the
        data in the self.val_dataloader. The series of steps are described
        below:

        Training:

        > Create a dictionary to store the average training loss and average
          validation loss for each epoch.
        > Store the time at the start of training, this is used to calculate
          the time taken for the entire training process.
        > Begin a loop to train the model for each epoch in self.epochs.

        For each epoch:

        > Switch the model to train mode. This will cause the model to behave
          differently than when in evaluation mode (e.g. the batchnorm and
          dropout layers are activated in train mode, but disabled in
          evaluation mode).
        > Set the training loss to 0 for the start of the epoch. This is used
          to track the loss of the model on the training data over subsequent
          epochs. The loss should decrease with each epoch if training is
          successful.
        > Store the time at the start of the epoch, this is used to calculate
          the time taken for the epoch to be completed.
        > As per the BERT authors' recommendations, the training data for each
          epoch is split into batches. Loop through the training process for
          each batch.

        For each batch:

        > Move the token IDs, attention masks, and labels to the GPU if
          available for faster processing, otherwise these will be kept on the
          CPU.
        > Invoke the zero_grad method to reset the calculated gradients from
          the previous iteration of this loop.
        > Pass the batch to the model to calculate the logits (predictions
          based on the current classifier weights and biases) as well as the
          loss.
        > Increment the total loss for the epoch. The loss is returned from the
          model as a PyTorch tensor so extract the float value using the item
          method.
        > Perform a backward pass of the model and propagate the loss through
          the classifier head. This will allow the model to determine what
          adjustments to make to the weights and biases to improve its
          performance on the batch.
        > Clip the gradients to be no larger than 1.0 so the model does not
          suffer from the exploding gradients problem.
        > Call the optimizer to take a step in the direction of the error
          surface as determined by the backward pass.

        After training on each batch:

        > Calculate the average loss and time taken for training on the epoch.

        Validation step for the epoch:

        > Switch the model to evaluation mode.
        > Set the validation loss to 0\. This is used to track the loss of the
          model on the validation data over subsequent epochs. The loss should
          decrease with each epoch if training was successful.
        > Store the time at the start of the validation, this is used to
          calculate the time taken for the validation for this epoch to be
          completed.
        > Split the validation data into batches.

        For each batch:

        > Move the token IDs, attention masks, and labels to the GPU if
          available for faster processing, otherwise these will be kept on the
          CPU.
        > Invoke the no_grad method to instruct the model not to calculate the
          gradients since we wil not be performing any optimization steps here,
          only inference.
        > Pass the batch to the model to calculate the logits (predictions
          based on the current classifier weights and biases) as well as the
          loss.
        > Extract the logits and labels from the model and move them to the CPU
          (if they are not already there).
        > Increment the loss and calculate the accuracy based on the true
          labels in the validation dataloader.
        > Calculate the average loss and accuracy, and add these to the loss
          dictionary.
        """

        loss_dict = {
            'epoch': [i+1 for i in range(self.epochs)],
            'average training loss': [],
            'average validation loss': []
        }

        t0_train = datetime.now()

        for epoch in range(0, self.epochs):

            # Train step
            self.model.train()
            training_loss = 0
            t0_epoch = datetime.now()

            print(f'{"-"*20} Epoch {epoch+1} {"-"*20}')
            print('\nTraining:\n---------')
            print(f'Start Time:       {t0_epoch}')

            for batch in self.train_dataloader:

                batch_token_ids = batch[0].to(self.device)
                batch_attention_mask = batch[1].to(self.device)
                batch_labels = batch[2].to(self.device)

                self.model.zero_grad()

                loss, logits = self.model(
                    batch_token_ids,
                    token_type_ids = None,
                    attention_mask=batch_attention_mask,
                    labels=batch_labels,
                    return_dict=False)

                training_loss += loss.item()
                loss.backward()
                torch.nn.utils.clip_grad_norm_(self.model.parameters(), 1.0)
                self.optimizer.step()
                self.scheduler.step()

            average_train_loss = training_loss / len(self.train_dataloader)
            time_epoch = datetime.now() - t0_epoch

            print(f'Average Loss:     {average_train_loss}')
            print(f'Time Taken:       {time_epoch}')

            # Validation step
            self.model.eval()
            val_loss = 0
            val_accuracy = 0
            t0_val = datetime.now()

            print('\nValidation:\n---------')
            print(f'Start Time:       {t0_val}')

            for batch in self.val_dataloader:

                batch_token_ids = batch[0].to(self.device)
                batch_attention_mask = batch[1].to(self.device)
                batch_labels = batch[2].to(self.device)

                with torch.no_grad():
                    (loss, logits) = self.model(
                        batch_token_ids,
                        attention_mask = batch_attention_mask,
                        labels = batch_labels,
                        token_type_ids = None,
                        return_dict=False)

                logits = logits.detach().cpu().numpy()
                label_ids = batch_labels.to('cpu').numpy()
                val_loss += loss.item()
                val_accuracy += self.calculate_accuracy(logits, label_ids)

            average_val_accuracy = val_accuracy / len(self.val_dataloader)
            average_val_loss = val_loss / len(self.val_dataloader)
            time_val = datetime.now() - t0_val

            print(f'Average Loss:     {average_val_loss}')
            print(f'Average Accuracy: {average_val_accuracy}')
            print(f'Time Taken:       {time_val}\n')

            loss_dict['average training loss'].append(average_train_loss)
            loss_dict['average validation loss'].append(average_val_loss)

        print(f'Total training time: {datetime.now()-t0_train}')

    def calculate_accuracy(self, preds, labels):
        """ Calculate the accuracy of model predictions against true labels.

        Parameters:
            preds (np.array): The predicted label from the model
            labels (np.array): The true label

        Returns:
            accuracy (float): The accuracy as a percentage of the correct
                predictions.
        """
        pred_flat = np.argmax(preds, axis=1).flatten()
        labels_flat = labels.flatten()
        accuracy = np.sum(pred_flat == labels_flat) / len(labels_flat)

        return accuracy

    def predict(self, dataloader):
        """Return the predicted probabilities of each class for input text.

        Parameters:
            dataloader (torch.utils.data.DataLoader): A DataLoader containing
                the token IDs and attention masks for the text to perform
                inference on.

        Returns:
            probs (PyTorch.Tensor): A tensor containing the probability values
                for each class as predicted by the model.

        """

        self.model.eval()
        all_logits = []

        for batch in dataloader:

            batch_token_ids, batch_attention_mask = tuple(t.to(self.device) \
                for t in batch)[:2]

            with torch.no_grad():
                logits = self.model(batch_token_ids, batch_attention_mask)

            all_logits.append(logits)

        all_logits = torch.cat(all_logits, dim=0)

        probs = F.softmax(all_logits, dim=1).cpu().numpy()
        return probs
```

**使用该类进行情感分析（使用IMDb数据集）示例：**

```py
# Initialise parameters
dataset = preprocess_dataset('IMDB Dataset Very Small.csv')
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertForSequenceClassification.from_pretrained(
    'bert-base-uncased',
    num_labels=2)
optimizer = AdamW(model.parameters())

# Fine-tune model using class
fine_tuned_model = FineTuningPipeline(
    dataset = dataset,
    tokenizer = tokenizer,
    model = model,
    optimizer = optimizer,
    val_size = 0.1,
    epochs = 2,
    seed = 42
    )

# Make some predictions using the validation dataset
model.predict(model.val_dataloader)
```

# 4 — 结论

在本文中，我们探讨了BERT的各个方面，包括其创建时的背景、模型架构的详细解析，以及编写任务无关的微调管道，并通过情感分析进行了演示。尽管BERT是最早的LLM之一，但即使到今天，它仍然具有相关性，并继续在研究和工业中找到应用。理解BERT及其对自然语言处理领域的影响为使用最新的最先进模型奠定了坚实的基础。预训练和微调仍然是LLM的主导范式，因此希望本文能为你提供一些宝贵的见解，可以在自己的项目中加以应用！

# 5 — 进一步阅读

[1] J. Devlin, M. Chang, K. Lee, 和 K. Toutanova，[BERT：用于语言理解的深度双向变换器预训练](https://arxiv.org/abs/1810.04805)（2019），计算语言学学会北美分会

[2] A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit, L. Jones, A. N. Gomez, Ł. Kaiser, 和 I. Polosukhin, [注意力机制：一切你需要的](https://arxiv.org/pdf/1706.03762) (2017), 神经信息处理系统进展 30（NIPS 2017）

[3] M. E. Peters, M. Neumann, M. Iyyer, M. Gardner, C. Clark, K. Lee, 和 L. Zettlemoyer, [深度上下文化的词表示](https://arxiv.org/pdf/1802.05365) (2018), 2018年北美计算语言学协会年会：人类语言技术会议论文集，第1卷（长篇论文）

[4] A. Radford, K. Narasimhan, T. Salimans, 和 I. Sutskever (2018), [通过生成预训练改进语言理解](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf),

[5] Hugging Face, [微调后的BERT模型](https://huggingface.co/models?sort=trending&search=BERT) (2024), HuggingFace.co

[6] M. Schuster 和 K. K. Paliwal, [双向递归神经网络](https://www.researchgate.net/publication/3316656_Bidirectional_recurrent_neural_networks) (1997), IEEE 信号处理学报 45

[7] Y. Zhu, R. Kiros, R. Zemel, R. Salakhutdinov, R. Urtasun, A. Torralba, 和 S. Fidler, [对齐书籍和电影：通过观看电影和阅读书籍实现类似故事的视觉解释](https://arxiv.org/pdf/1506.06724v1.pdf) (2015), 2015年IEEE计算机视觉国际会议（ICCV）

[8] L. W. Taylor, [“完形填空程序”：衡量可读性的新工具](https://gwern.net/doc/psychology/writing/1953-taylor.pdf) (1953), 新闻学季刊，30(4)，415–433。

[9] Hugging Face, [预训练分词器](https://huggingface.co/docs/transformers/v4.40.1/en/internal/tokenization_utils#transformers.PreTrainedTokenizerBase.from_pretrained.pretrained_model_name_or_path) (2024) HuggingFace.co

[10] Hugging Face, [预训练分词器编码方法](https://huggingface.co/docs/transformers/en/main_classes/tokenizer#transformers.PreTrainedTokenizer.encode) (2024) HuggingFace.co

[11] T. Vo, [PyTorch DataLoader：特点、优势及如何使用](https://saturncloud.io/blog/pytorch-dataloader-features-benefits-and-how-to-use-it/#:~:text=There%20are%20several%20benefits%20of,time,%20especially%20for%20large%20datasets) (2023) SaturnCloud.io

[12] Hugging Face, [BERT建模](https://github.com/huggingface/transformers/tree/v4.40.1) (2024) GitHub.com

[13] Hugging Face, [运行Glue](https://github.com/huggingface/transformers/blob/5bfcd0485ece086ebcbed2d008813037968a9e58/examples/run_glue.py#L308), GitHub.com

[14] NVIDIA, [CUDA专区](https://developer.nvidia.com/cuda-zone#:~:text=CUDA%C2%AE%20is%20a%20parallel,harnessing%20the%20power%20of%20GPUs.) (2024), Developer.NVIDIA.com

[15] C. McCormick 和 N. Ryan, [BERT微调](https://mccormickml.com/2019/07/22/BERT-fine-tuning/#4-train-our-classification-model) (2019), McCormickML.com
