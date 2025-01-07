# 探索多模态语言模型在音乐转录中的应用

> 原文：[https://towardsdatascience.com/exploring-music-transcription-with-multi-modal-language-models-af352105db56?source=collection_archive---------1-----------------------#2024-11-17](https://towardsdatascience.com/exploring-music-transcription-with-multi-modal-language-models-af352105db56?source=collection_archive---------1-----------------------#2024-11-17)

## 使用Qwen2-Audio将音乐转录为乐谱

[](https://medium.com/@jon.flynn2?source=post_page---byline--af352105db56--------------------------------)[![Jon Flynn](../Images/492cef280f4ea0b002e5d00ad2e083a5.png)](https://medium.com/@jon.flynn2?source=post_page---byline--af352105db56--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af352105db56--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af352105db56--------------------------------) [Jon Flynn](https://medium.com/@jon.flynn2?source=post_page---byline--af352105db56--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af352105db56--------------------------------) ·17分钟阅读·2024年11月17日

--

![](../Images/1c1e9d05246384c1d3a88590ce3d7033.png)

作者提供的图片

自动音乐转录是将音频文件（如MP3和WAV）转换为乐谱、吉他谱或任何音乐家希望用来学习歌曲的格式的过程。

我们将介绍目前最好的工具，它们基于深度学习，并且有一种新的方法来处理这个问题。

# 当前的最先进技术

当前这一任务的最先进技术来自于[Magenta](https://magenta.tensorflow.org/)，这是一个由现已解散（截至2023年4月）Google Brain团队开发的开源研究项目。

他们于2021年发布了一篇论文[Sequence-to-Sequence Piano Transcription with Transformers](https://arxiv.org/abs/2107.09142)，使用了一种受T5启发的变换器模型（类似于["t5-small"](https://huggingface.co/google-t5/t5-small)），该模型有5400万个参数，并使用[Maestro数据集](https://magenta.tensorflow.org/datasets/maestro)，取得了很好的成果。该问题被视为一个序列到序列的任务，使用编码器-解码器Transformer架构。编码器处理梅尔频谱图帧作为输入并生成嵌入，解码器则通过交叉注意力使用这些嵌入自回归地生成一系列MIDI样式的标记。他们的词汇表包括四种类型的标记：

+   音符标记（128个MIDI音高值）

+   音量标记（128个值，包括零表示音符关闭）

+   时间标记（6,000个值，10毫秒为单位的绝对时间）

+   EOS标记（用于标记序列结束）

请参见下图，了解架构的可视化以及他们自定义的MIDI标记的示例序列：

![](../Images/67b417bfc9720edd402c1c002ca058f8.png)

图 1 来自 [基于 Transformer 的序列到序列钢琴转录](https://arxiv.org/abs/2107.09142) 论文

> 我们的模型是一个通用的编码器-解码器 Transformer 架构，其中每个输入位置包含一个单一的频谱图帧，每个输出位置包含来自 MIDI 类似词汇表的一个事件。输出的标记是通过解码器自回归采样的，在每一步中选择具有最大概率的标记。

在 2022 年，他们发布了一篇论文，[MT3: 多任务多轨音乐转录](https://arxiv.org/abs/2111.03017)。这项实验采用了与之前相同的方法，但增加了额外的乐器标记来表示不同的乐器。同样，他们使用了类似的 T5 模型，并在许多训练数据集上取得了出色的表现，特别是在[Slakh](http://www.slakh.com/)、Maestro 和 [MusicNet](https://www.kaggle.com/datasets/imsparsh/musicnet-dataset)等数据集上。

[MR-MT3](https://arxiv.org/abs/2403.10024) 于次年发布，作为对 MT3 的轻微改进。

# 为什么要使用语言模型，而不是继续使用这些最先进的模型？

## **计算/GPU 资源**

尽管这些模型相比最小的语言模型规模要小得多，训练这一模型从零开始仍然需要巨大的资源。2021 年的论文中提到：

> “我们在 32 个 TPUv3 核心上训练了所有模型，导致每个核心的批量大小为 8。根据验证集结果，过拟合似乎不是问题，因此我们允许训练进行 400K 步，这大约花费了 2.5 天时间来训练我们的基准模型。”

MT3 论文没有提供具体的训练细节，仅说明他们训练了 100 万步。

## 其他局限性

这些模型在输出灵活性方面有一些固有的局限性。虽然语言模型通常具有庞大的词汇表（通常超过 30,000 个标记），并在多样的自然语言数据上进行了广泛的预训练，但 MT3 和类似的音乐转录模型使用的是一个更小、更专业化的标记词汇表（仅有几千个标记），专注于音乐事件。这种专业化意味着添加新标记，例如为新的乐器或弹奏技巧（如吉他的掌中静音或小提琴的拨弦）可能并不容易——它需要大量的再训练，以有效地将这些新标记与现有词汇表结合，通常还需要大量的训练数据来展示这些技巧。这与大型语言模型不同，后者通常可以在不做修改的情况下，用自然语言描述这些音乐细节，因为它们在广泛的预训练过程中已经遇到过这些概念。

## 迁移学习与零样本

我们可以利用来自大型开源预训练音频和语言模型的迁移学习。音乐生成模型的例子包括 [OpenAI 的 Jukebox](https://openai.com/index/jukebox/) 和 [Meta 的 MusicGen](https://audiocraft.metademolab.com/musicgen.html)。

# 现代多模态模型架构

[GPT-4o](https://openai.com/index/hello-gpt-4o/) 设计用于“原生”处理文本、音频和图像。尽管 OpenAI 尚未发布相关技术细节，但可以推测，网络中的某些权重会处理所有模态。也有可能该模型使用类似语言 GPT 模型的解码器架构，而不需要编码器组件将不同模态转换为密集表示。这种设计使得模型能够无缝处理和理解文本与图像的输入，从而在计算上和模型理解上可能带来性能提升。

许多多模态模型采取一种更简单的方式，类似于编码器-解码器架构：它们结合了两个预训练模型——一个用于特定输入模态（如视觉的 [ViT](https://huggingface.co/docs/transformers/main/en/model_doc/vit) 或音频的音频编码器）的编码器，以及一个大型语言模型（如 LLaMA、Gemma 或 Qwen）。这些模型通过投影层连接起来，投影层将它们的表示对齐到共享的潜在空间中，通常只使用一个线性层。这些投影层学习将编码器的输出转换为与 LLM 预期输入维度和特征匹配的格式。投影层从输入模态中创建新的嵌入/标记，然后可以将这些嵌入注入到 LLM 的输入序列中。[LLaVA](https://llava-vl.github.io/) 是一个典型的用于视觉-语言任务的架构示例，而 [Spotify的Llark](https://research.atspotify.com/2023/10/llark-a-multimodal-foundation-model-for-music/) 和 [Qwen-Audio](https://github.com/QwenLM/Qwen2-Audio) 则使用音频编码器代替视觉编码器，应用相同的原理。

下面是模型如何结合的伪代码：

```py
# Extract features from final layer of audio encoder
# Shape: [batch_size, audio_seq_len, encoder_dim=1024]
audio_features = audio_model(audio_input)

# Project audio features to match LLM's embedding dimension
# Shape: [batch_size, audio_seq_len, llm_embed_dim=4096]
audio_embeddings = projection_layer(audio_features)

# Get text embeddings from LLM's embedding layer
# Shape: [batch_size, text_seq_len, llm_embed_dim=4096]
text_embeddings = llm.embed_text(text_input)

# Concatenate along sequence length dimension
# Shape: [batch_size, audio_seq_len + text_seq_len, llm_embed_dim=4096]
combined_input = concatenate([audio_embeddings, text_embeddings], dim=1)

# Feed them into the LLM as normal for generation
output = llm(combined_input)
```

# **Spotify Llark 和 Qwen2-Audio**

## 架构概述

Llark 使用 [OpenAI的Jukebox](https://research.atspotify.com/2023/10/llark-a-multimodal-foundation-model-for-music/) 作为音频塔，而 Qwen2-Audio 使用 [OpenAI的Whisper](https://openai.com/index/whisper/) 作为音频塔。Jukebox 是一个音乐生成模型，但它也可以接收音频片段作为输入，并输出音频片段的延续。Whisper 用于将语音转录为文本。

根据其用途，音频模块的选择是明确的：Llark 专注于音乐分析，而 Qwen2Audio 主要专注于响应语音指令，并具备一些基本的音频和音乐分析能力。

确定从大型预训练模型中提取嵌入的最佳来源需要研究和实验。此外，决定是微调整个模块还是冻结其部分模块是一个至关重要的设计选择。例如，LlaVa的训练策略包括冻结视觉塔，并专注于微调投影层和语言模型。我们将在下面逐一介绍每个模型的这个方面。

## Llark: 为什么选择 Jukebox？这些嵌入在 2024 年 9 月时是最好的吗？

确定从大型模型中提取嵌入的最佳位置通常需要大量的探测。这包括通过反复试验的过程，在不同的分类任务上测试模型的各种激活或提取层。对于音乐生成模型，这可能包括像流派识别、乐器检测、情感检测、以及和声结构和时间模式的分析等任务。许多商业嵌入模型（例如 [OpenAI 的嵌入模型](https://openai.com/index/new-embedding-models-and-api-updates/)）是专门为嵌入生成而训练的，采用了特定的架构和训练目标，而不是现有语言模型的微调版本。

两个最大公开可用的音乐生成和音乐延续（即：能够接受音频作为输入的）模型是 Jukebox 和 MusicGen。MusicGen 更新且更快，因此我原本认为它是显而易见的选择。然而，根据 [这篇关于探测 MusicGen 的论文](https://www.merl.com/publications/docs/TR2024-032.pdf)，从 Jukebox 中提取的嵌入在分类任务中的表现似乎普遍优于 MusicGen。该论文的研究结果促使 Llark 的作者采用了以下提取嵌入的方法：

1.  嵌入来自 Jukebox 编码器第 36 层的输出，采用了 [Castellon 等人 (2021)](https://arxiv.org/abs/2107.05677) 中描述的方法。

1.  原始 Jukebox 编码：

    * 4800 维的向量，采样率为 345Hz

    * 对于一个 25 秒的片段：超过 4.14 * 10⁷ 个浮动点值

1.  作者使用了下采样方法：在 100 毫秒的帧内进行均值池化，得到：

    * 下采样频率：10Hz

    * 嵌入大小：对于 25 秒的音频片段为 1.2 × 10⁶。这意味着一个形状为 [240, 4800] 的二维数组。

    * 保留时间信息（与 Castellon 等人不同，他们在时间维度上进行平均）

（下采样后的嵌入大小大约是许多多模态视觉模型中使用的 CLIP ViT-L14 模型的 6 倍）

## Qwen2Audio: Whisper

Qwen2Audio 的嵌入提取在论文中没有详细提及。Whisper 是一种编码器-解码器架构，其中编码器生成音频的深度学习表示，解码器将这些表示解码为文本（转录）。在 Qwen2Audio 中，似乎它们从 Whisper 编码器的最后一层提取嵌入，尽管他们没有提到在训练过程中是否冻结这一层。

## **预训练权重、训练数据和数据集**

不幸的是，Spotify 并未向公众提供任何数据集或训练好的模型权重，说明：

> “关于输入：我们的模型输入是公共的、开源的、创意共享许可的音频及相关注释。然而，每个单独的音频文件可能有其自己潜在的更严格的许可。许多音频文件包括‘不可修改’的许可。我们鼓励数据集的用户熟悉这些许可的限制；为了遵守这些许可，我们不会在本文中发布任何来自训练数据的衍生作品（包括查询-响应对或训练好的模型权重）。”

他们使用了以下数据集：

+   MusicCaps（Agostinelli等，2023）

+   YouTube8M-MusicTextClips（McKee等，2023）

+   MusicNet（Thickstun等，2017）

+   FMA（Defferrard等，2017）

+   MTG-Jamendo（Bogdanov等，2019）

+   MagnaTagATune（Law等，2009）

Llark在以下摘录中详细说明了其训练数据生成过程：

> “我们使用ChatGPT的变体来提取所有实验的指令调优数据。然而，使用的具体语言模型根据数据集不同而有所不同。我们选择了OpenAI模型，如下所示：我们在所有推理任务中使用GPT-4。我们发现GPT-4在执行推理任务系列中的复杂指令时更加得心应手。对于样本超过25k的数据集，我们将推理数据限制为25k条随机子样本。”

这会产生如下的问答数据：

![](../Images/aeb50a9a892d9ed3b78415702057fc99.png)

*LLark提供的示例文本输入和输出，针对提供的音频。*

用于训练Qwen2Audio的数据集也没有公开，但训练好的模型广泛可用，并且已在`transformers`库中实现：

+   [Qwen2-Audio官方GitHub仓库](https://github.com/QwenLM/Qwen2-Audio)

+   [Qwen2-Audio变换器文档](https://huggingface.co/docs/transformers/main/en/model_doc/qwen2_audio)

对于这个项目，基于一个预训练的Llark模型进行微调将是最佳选择，因为据报道它在Spotify在论文中提出的评估基准上表现良好。

然而，由于它没有公开权重，从零开始训练这样一个模型既不可行，也需要相当的专业知识和资金。Spotify在以下数据集上进行了训练：

> 我们的模型在4个80GB的NVIDIA A100 GPU上进行训练。训练大约需要54小时。

通过像LambdaLabs这样的提供商，这将花费大约700美元。

由于上述原因，我选择了Qwen。然而，Qwen2-Audio在一些基本的音乐任务（如节奏和乐器检测）上的表现不佳。我在评估部分详细说明了这一点。这意味着模型可能不够大，或者预训练不足以完成该任务，但我的希望是至少能为未来在该任务上的微调设定一个起点和框架。正如阿里巴巴在他们的Qwen2-Audio [博客文章](https://qwenlm.github.io/blog/qwen2-audio/)中所述：

> 我们还计划构建更大的Qwen2-Audio模型，以探索音频语言模型的规模定律。

然而，为了我自己的学习，我尝试使用`torch`和`transformers`库中的预训练模型重新创建了这个模型。

我还为问答数据和嵌入创建了数据集。我为 URMP 数据集生成了简短形式的问答数据，例如：“这首曲子的节奏是多少”，“这段音频里有哪些乐器在演奏”。

[这是一个笔记本](https://colab.research.google.com/drive/1jdR5w-XlJQFog47ZJ36ckEVMW0F5qIpl) 用于在 Colab 环境中运行 Jukebox，以利用便宜的 T4 GPU。我将问答数据集和嵌入数据集上传到了 HuggingFace [这里](https://huggingface.co/jonflynn)。

[这是一个笔记本](https://colab.research.google.com/drive/1_V5B9ZrwrKtom-N4r-Om3mqlXKPacUBh#scrollTo=72Gv5raTIPqi) 其中包含了 Llark 的复现。

# 音乐转录的训练数据

## 转录格式

我选择了 [ABC 音乐符号](https://trillian.mit.edu/~jc/music/doc/ABC.html) 作为语言模型预期将音乐转录成的输出格式。以下是它的一个示例：

```py
X:1
M:4/4
L:1/16
K:none
Q:67

V:1 name="Electric Bass (finger)"
%%octave-default C4
GAA^2E3A2<A^2 | D^D^2E2A2A^4 A^2E2 | A2A^4A^2E2 A2A^4 | A^2E2A2A^4A^2E2A2 |
A^4 A^2E2 A2A^4A^2 E2 | A2A^4 |

V:2 name="Bright Acoustic Piano"
%%octave-default C5
[E3C3][E3C3][E3C3] [E3C3][A^,2E2A^2] | [E3A^3][E3A^3][E3A^3][E3A^3][E3A^3] |
[E3A^3][E3A^3][E3A^3] [E3A^3][E3A^3] | [E3A^3][E3A^3][E3A^3][E3A^3][E3A^3] |
[E3A^3][E3A^3][E3A^3] [E3A^3][E3A^3] | [E3A^3] |

V:3 name="Electric Guitar (jazz)"
%%octave-default C5
E'3C'3A^4E'3C'3 | A^4E'3 C'3A^4E'3C'3 | A^4 E'3C'3A^4 E'3C'3 | A^4E'3C'3A^4E'3C'3 |
A^4E'3C'3 A^4E'3C'3 | A^4 |
```

在这种符号中，我们在顶部定义了拍号和节奏，用 ‘M’ 和 ‘Q’ 表示。‘L’ 表示符号的默认音符长度，在此案例中为十六分音符，这是常规格式。接着，我们定义每个乐器及其在写作音符时应遵循的默认八度。以下是编写 ABC 音乐符号时一些关键语法点的总结：

+   音符由字母 A-G 表示，小写字母表示更高的八度

+   升音符在音符前用 ^ 表示，降音符用 _ 表示

+   自然音符用 = 表示

+   音符的长度用音符后的数字表示（C2 表示 C 的两倍时长）

+   点音符在音符后使用一个点（C. 是一个点四分音符）

+   休止符用 z 表示，持续时间用数字表示（z2 是一个二分休止符）

+   和弦用方括号括起来 [CEG]

+   连音符用连字符 - 表示

+   小节线用 | 表示

+   破节奏使用 > 或 < 来连接音符（C>D 意味着点四分音符 C 后跟十六分音符 D）

## 为什么选择 ABC？

选择这种符号格式的原因是：

1.  这是一种简约的音乐写作格式

1.  它广泛使用且受欢迎；语言模型由于在其上进行过广泛的预训练，已经对 ABC 符号有很好的理解。

1.  它具有灵活性，可以轻松扩展以包含节奏变化、拍号变化、如上所述的额外演奏风格等……

我使用 [这个库](https://github.com/sshlien/abcmidi) 将数据集提供的 MIDI 文件转换为 ABC 符号。创建数据集的笔记本在[这里](https://colab.research.google.com/drive/1CdQ_PUjhCvCR2VjGt3ya1hNowPrr0Xun)。

# 评估

为了评估原始模型和我之后进行的每一阶段微调，我从 URMP 数据集中随机选择了 30 个复杂度不同的样本，并在每个样本上运行了模型三次，手动检查所有响应。

通过手动测试，我发现最优的解码参数是温度为0.7，top_p为1.2。返回的最大标记数量被限制为2048。调整最大值似乎对性能几乎没有影响。

原始模型在这个评估集上的表现较差。尽管它偶尔能够正确预测节奏和乐器，但大部分时间都未能做到这一点。评估结果的文本文件可以在[这里](https://drive.google.com/file/d/1-0XgJDOhnj1kbffeHcQutgZ1td59WQjI/view?usp=drive_link)查看。

鉴于这个起点，如果没有一个强大的预训练模型，我们很难在这个实验中获得强劲的结果。然而，目标是制定能够在未来应用的策略，随着更先进的预训练模型的出现，这些策略将变得更加有效。

# 微调策略

我首先尝试了使用基础的交叉熵损失进行微调。使用交叉熵损失进行监督式微调是开始教导模型的快捷方式，但正如我们下面所看到的，这种基本的损失函数有其局限性。这个训练阶段的直觉是，它可以将模型推向正确的方向，让它从数据集中捕捉到任何模式或任何自定义的ABC符号表示，而这些是模型之前可能未曾见过的。

## 使用教师强制的交叉熵损失

首先，我们采用了一种典型的监督式微调方法来训练语言模型。我使用了`trl`库中的`SFTtrainer`，它使用交叉熵损失和教师强制，具体步骤如下所定义：

1.  模型预测序列中的下一个标记。

1.  损失是基于预测概率（logits）和实际下一个标记之间的差异计算的。

1.  在下一个预测中，模型被提供了实际的正确标记（真实值），而不是它自己的预测结果。这被称为教师强制，它有助于稳定训练，并显著加速训练，特别是在早期阶段。

这次训练阶段的结果较差。它降低了原模型的性能。原本能够很好地处理节奏和乐器识别的模型，现在大多错误地识别这些内容。它还开始输出乱码文本，并出现无休止的重复现象。即使设置了较低的学习率、应用了梯度裁剪，并使用了低的LoRA秩来减少对模型的大幅度调整，这些问题依然存在。总体而言，似乎模型对所应用的训练非常敏感。

然而，虽然这个训练阶段可能带来一些改进，但由于我们基本的损失函数存在局限性，它不会导致最佳性能。这个函数难以完全捕捉模型性能的细微差别。例如，在使用教师强制时，某些标记部分的乐器预测可能会导致 deceptively low loss（表面上较低的损失）。如果一个乐器名称以“V”开头，模型可能会基于我们的数据集自信地预测“Violin”或“Viola”，无论准确性如何。此外，损失函数可能无法准确反映接近错失的情况，例如预测195的节奏而不是200——这个小差异在合理的范围内，但可能会因为logits中概率分布的不同而受到严重惩罚。邻近的数字也可能具有较高的概率。

## 使用PPO的RLHF

由于这些局限性，我们可以创建我们自己的自定义损失函数，更准确地评估模型的响应。也就是说，对于模型预测的序列，损失函数可以根据好坏为其打分，分数在0到1之间。

然而，将这个自定义损失函数集成到监督微调中是一个重大挑战。问题源于自定义损失函数引入的非线性，这使得无法直接计算梯度。让我们来分解一下：

在传统的SFT与交叉熵损失中：

+   模型输出每个标记的logits（原始分数）

+   这些logits直接表示模型的预测概率

+   损失函数将这些概率与真实值进行比较

+   可以通过这种比较直接计算梯度

+   微积分的链式法则允许我们将这些梯度传播回模型中

使用我们的自定义损失函数：

+   模型必须首先生成完整的文本输出

+   这个生成过程涉及从概率分布中采样

+   然后我们的损失函数分析这个文本输出（检查节奏、音符等）

+   这在模型的logits与我们的损失计算之间创建了一个不可微分的步骤

+   采样和文本分析步骤打破了反向传播所需的梯度链

为了克服这个问题，可以采用强化学习技术，如近端策略优化（PPO）。PPO专门设计用来处理不可微分的损失函数，并可以通过考虑整个策略（模型的输出分布）来优化模型，而不是依赖于logits的梯度信息。

*注意，这里有很多很棒的文章* [*解释了PPO*](https://medium.com/search?q=PPO+explained) *！*

PPO的关键洞察在于，与其直接通过不可微分步骤反向传播，它：

1.  将模型的输出视为强化学习框架中的动作

1.  将自定义损失函数作为奖励信号

1.  更新模型的策略（其在标记上的概率分布），以最大化期望的奖励

1.  在确保更新的策略不会偏离当前策略太远的同时进行此操作

这种方法使我们能够有效地使用自定义损失函数训练模型，确保在不破坏核心训练动态的情况下提高性能。PPO算法的保守更新策略有助于在训练过程中保持稳定性，这在处理大型语言模型时尤为重要。

通常，这种评分函数会作为一个单独的LLM以“奖励模型”的形式实现，常用于通过RLHF微调模型时，这是在ChatGPT发布时首次引入的突破。由于该任务的性质，我们可以手动编写代码来评分响应，这样可以减少资源使用并加快速度。

对于时间签名和节奏识别，这很容易计算。我们通过正则表达式提取所有预测项，例如提取拍子：

```py
def extract_metre(self, abc_string):
  return re.search(r'M:(\S+)', abc_string).group(1)
```

模型应该在SFT阶段学习我们希望其输出的语法和结构。如果它输出的内容导致我们的正则表达式无法找到任何内容或发生错误，我们可以跳过该样本，假设它只是数据集中的少数情况。

我们提取预测的节奏并编写一个函数，它对小错误更宽容，但对较大错误惩罚更重：

+   对于较小的差异（≤10 BPM），使用线性缩放。

+   对于较大的差异，它切换到指数缩放。

+   最终的损失值被限制在0和1之间。

让我们来分解这个自定义损失的关键组成部分：

自定义损失的代码在[这里](https://colab.research.google.com/drive/1lpPfn9EFE2rBsasIJNv8Cy9qTvtfXzq-#scrollTo=mOs_gWcjrBgv)

**1\. 拍子损失**

拍子损失专注于作品的时间签名。它将预测的拍子与真实值进行比较，分别考虑分子和分母，以及它们的比率。这种方法允许进行细致的评估，可以准确处理各种时间签名。

拍子损失使用线性和指数缩放的组合来惩罚差异。较小的差异会导致损失线性增加，而较大的差异则会导致损失指数增加，最大值为1。

**2\. 节奏损失**

节奏损失评估预测的每分钟节拍数（BPM）的准确性。与拍子损失类似，它使用线性和指数缩放的组合。

对于较小的节奏差异（≤10 BPM），该函数应用线性缩放。较大的差异则触发指数缩放，确保显著的节奏不匹配受到更严厉的惩罚。

**3\. 音高损失**

音高损失可能是最关键的组件，因为它评估转录音符的准确性。此函数使用Levenshtein距离比较每个声部中的音符序列。

音高损失计算考虑了多个声部，将每个预测声部与最接近的真实声部进行匹配。这种方法允许在保持整体音高内容准确性的同时，对声部的顺序保持灵活性。

**4\. 乐器损失**

乐器损失评估每个声部的乐器选择准确性。

此函数考虑了精确匹配、同一家族的乐器，并使用字符串相似度进行更细致的比较。它提供了一个全面的评估，衡量模型如何识别并为每个声部分配乐器。

**5\. 综合损失**

最终损失是这些单独组件的加权组合：

```py
total_loss = (0.5 * pitch_loss +
 0.15 * metre_loss +
 0.15 * tempo_loss +
 0.2 * instrument_loss)
```

该加权方案优先考虑音高准确性，同时仍考虑音乐转录中的其他重要方面。

# 训练与超参数

由于几个原因，PPO训练通常需要比SFT更多的内存：

1.  多个策略评估——PPO需要同时保持当前策略（模型权重）和“旧”策略，以计算它们之间的概率比。这实际上使得内存中的模型参数翻倍。

1.  经验缓冲区——PPO存储一组经验（状态、动作、奖励等）以进行小批量更新。该缓冲区可能非常大，占用大量内存。

1.  优势估计——计算优势需要跟踪整个轨迹中的价值估计和回报，从而增加了额外的内存开销。

1.  附加优化目标——PPO跟踪多个损失组件（策略损失、价值损失、熵奖励）及其梯度，而SFT只有一个损失。

由于上述原因，我们在训练模型的大小和成本上比SFT更受限制。虽然上述训练我可以在Colab的A100 40GB上完成，但对于PPO训练，我需要更多内存。我使用H100 80GB进行训练，这可以训练一个秩为128、批量大小为8的LoRA。

我的超参数搜索范围较窄，选择了看起来最直观的设置，批量大小从1到16，学习率从2e-5到2e-4。

模型未对任务做出任何改进。结果的文本文件可以在[这里](http://asdf)找到。

我使用Weights & Biases（WandB）跟踪了各种训练指标。关键指标包括策略损失、价值损失、总损失、KL散度以及奖励模型的得分。

对所有超参数运行，日志显示奖励和损失随时间推移没有改善。KL散度保持在预定义的阈值内。

![](../Images/891e605d5da483ab80fff11d72e18d8b.png)![](../Images/de9c931b5e76c6c8b11bbaa5f855d234.png)

# 结论

尽管这个初步实验未能在音乐转录中达到预期的表现，我们为未来该领域的发展奠定了基础。所遇到的挑战为我们提供了对技术需求和解决这一复杂任务的潜在方法的宝贵见解。未来的工作可以探索几个有前景的方向：

+   随着更大规模预训练模型的出现，进行实验

+   扩展训练数据集，加入更多样化的音乐示例

+   进一步优化奖励函数，以捕捉更微妙的音乐关系

+   探索结合传统音乐处理技术与语言模型能力的混合方法

[这是我的笔记本](https://colab.research.google.com/drive/1lpPfn9EFE2rBsasIJNv8Cy9qTvtfXzq-) 用于运行这些与Qwen2-Audio的实验！另外，这是[我的GitHub](https://github.com/jonflynng)链接，里面包含所有的笔记本。
