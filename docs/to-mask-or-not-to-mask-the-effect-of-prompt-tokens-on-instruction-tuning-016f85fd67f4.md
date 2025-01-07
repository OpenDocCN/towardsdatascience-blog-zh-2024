# 屏蔽还是不屏蔽：提示令牌对指令微调的影响

> 原文：[https://towardsdatascience.com/to-mask-or-not-to-mask-the-effect-of-prompt-tokens-on-instruction-tuning-016f85fd67f4?source=collection_archive---------4-----------------------#2024-09-30](https://towardsdatascience.com/to-mask-or-not-to-mask-the-effect-of-prompt-tokens-on-instruction-tuning-016f85fd67f4?source=collection_archive---------4-----------------------#2024-09-30)

## 实现提示丢失权重，并且为什么我们应该用提示加权替代提示屏蔽

[](https://medium.com/@davidsvaughn?source=post_page---byline--016f85fd67f4--------------------------------)[![David Vaughn](../Images/74a3d9c03f6a67f01d8f781795041715.png)](https://medium.com/@davidsvaughn?source=post_page---byline--016f85fd67f4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--016f85fd67f4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--016f85fd67f4--------------------------------) [David Vaughn](https://medium.com/@davidsvaughn?source=post_page---byline--016f85fd67f4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--016f85fd67f4--------------------------------) ·30分钟阅读·2024年9月30日

--

![](../Images/ac4d2d01af7244482a4c7cdc453a8730.png)

图片由 Midjourney 制作

[`*完整代码链接在* [*GitHub*](https://github.com/davidsvaughn/prompt-loss-weight)`][`*通过* [*LinkedIn*](https://www.linkedin.com/in/davidsvaughn/) 与我联系`]

在过去几个月中，我注意到关于是否在针对提示-完成风格数据（即指令微调）进行微调时对提示令牌进行零屏蔽（忽略）的问题，出现了不少讨论，包括在[这里](https://yonigottesman.github.io/2024/05/13/mask-user-tokens.html)、[这里](https://magazine.sebastianraschka.com/p/llm-research-insights-instruction?open=false#%C2%A7instruction-masking-during-instruction-finetuning)、[那里](https://github.com/huggingface/trl/issues/632)，甚至[这里](https://x.com/corbtt/status/1806336011804484017)。我看到使用了不同的术语，例如：

+   *指令屏蔽*

+   *提示屏蔽*

+   *用户屏蔽*

+   *仅完成训练*

无论你怎么称呼它，似乎没有明确的共识关于应该采用什么标准做法。根据你使用的开源库，微调的默认设置可能差异很大。

例如，[水陆两栖兽](https://hamel.dev/notes/llm/finetuning/09_template_free.html)库默认屏蔽提示令牌（通过其`train_on_inputs=False`默认设置）。然而，广受欢迎的[HuggingFace Trainer](https://huggingface.co/docs/transformers/en/main_classes/trainer)默认*不*屏蔽提示令牌。可以选择使用`[DataCollatorForCompletionOnlyLM](https://huggingface.co/docs/trl/main/en/sft_trainer#train-on-completions-only)`来屏蔽提示，但这带来了一些[显著的限制](https://github.com/huggingface/trl/issues/1385)——尤其是缺乏对[*样本打包*](https://www.hopsworks.ai/dictionary/sample-packing)的支持——这在处理大型数据集时可能成为致命问题，就像我遇到的情况一样。（*注意：这里提出了一个不错的解决方案*[*在此*](https://github.com/huggingface/trl/issues/632#issuecomment-1972630547)）。

许多我遇到的关于LLM微调的指南、演示、笔记本、教程等，*没有*提到提示屏蔽，例如：

+   [如何在2024年使用Hugging Face微调LLM](https://www.philschmid.de/fine-tune-llms-in-2024-with-trl)

+   [如何微调LLM-第二部分-指令微调-Llama-2](https://wandb.ai/capecape/alpaca_ft/reports/How-to-Fine-Tune-an-LLM-Part-2-Instruction-Tuning-Llama-2--Vmlldzo1NjY0MjE1)

+   [HuggingFace对齐手册](https://github.com/huggingface/alignment-handbook/blob/main/scripts/run_sft.py)

+   Niels Rogge的[SFT教程](https://github.com/NielsRogge/Transformers-Tutorials/blob/master/Mistral/Supervised_fine_tuning_(SFT)_of_an_LLM_using_Hugging_Face_tooling.ipynb)

+   这个[微调Llama 2笔记本](https://github.com/mlabonne/llm-course/blob/main/Fine_tune_Llama_2_in_Google_Colab.ipynb)

但也可以找到带有*默认*提示屏蔽的示例：

+   这个[FastChat示例](https://github.com/lm-sys/FastChat/blob/main/fastchat/train/train.py#L150)

+   PyTorch/[torchtune](https://github.com/pytorch/torchtune/blob/4efd7fdb48677a4ac6d78f394be7b4ecfbabf7de/torchtune/datasets/_instruct.py#L44C66-L44C80)

+   [水陆两栖兽](https://hamel.dev/notes/llm/finetuning/09_template_free.html)（上文提到）

*剧透警告*：本文并*不*尝试一劳永逸地解决这个问题。它始于一个简单的想法，灵感来自一次谦虚的调查——*我想比较带有和不带有*提示屏蔽*的微调*，并在这两种情况下*分别*跟踪验证集的提示损失和完成损失*。

我的假设是，这可能为提示屏蔽问题提供有价值的见解。然后，我遇到了***提示损失权重***这一概念，它是将*二进制令牌屏蔽*优雅地推广到*实值令牌加权*（加权发生在损失函数内部，正如我们所看到的）。

将*提示损失权重*（PLW）参数集成到微调流程中，可以更平滑、更精细地控制提示标记对微调过程的影响。简单来说：*PLW=0*等同于提示掩蔽，而*PLW=1*等同于不进行掩蔽。此外，使用0<*PLW<1*可以在这两种极端之间平滑地调节提示标记的影响。

通过这种重新框定，*是否掩蔽*提示标记的问题被更深层次的问题所涵盖，即*如何加权*提示标记。最优的加权方式可能根据具体的使用案例和数据集有所不同。通过将*提示损失权重*添加到你的工具包中，你将获得实验不同加权策略的灵活性，从而实现更有效的微调结果，更好地满足你的特定需求。

由于我找不到任何*提示损失权重*的实现，因此我决定尝试自己实现它。我将指导你完成对标准HuggingFace LLM工具集的几部分自定义修改，以便使其生效。之后，我们将使用更新后的工具集，通过在[RACE数据集](https://huggingface.co/datasets/ehovy/race)（一个托管在HuggingFace上的多项选择问答数据集）上运行一些微调实验，来探索关于提示标记的原始问题。

# 一些LLM背景知识

LLMs的操作基于*标记*而非*单词*。为了本篇文章的目的，我们将这两个术语交替使用，但值得注意的是，它们有所不同。标记被定义为*频繁出现的字符序列*，通常大致与单词相同（有时甚至包括前面的空格）。一个有趣的练习是玩一下[GPT-4分词器](https://platform.openai.com/tokenizer)，我用它生成了以下示例（颜色编码揭示了底层的标记）：

![](../Images/1386e5fce4f3a6e55312fb3c2f3b1cec.png)

来自[https://platform.openai.com/tokenizer](https://platform.openai.com/tokenizer)的截图

我们日常使用的大多数生成性LLM是*下一个标记预测*机器。它们已经在大量人类生成的文本（书籍、报纸、互联网等）上进行了训练（有时称为*预训练*），因此当输入一段合理的文本片段时，它们非常擅长预测下一个单词应该是什么。这有时被称为*因果语言建模*。当这一*自回归文本生成*过程反复应用时，可以生成非常类似人类的句子、段落、文章等。

我们经常希望使用这些已经在大量文本上进行预训练的*基础模型*LLM（例如[Meta的Llama模型家族](https://huggingface.co/meta-llama)），然后继续进行进一步训练，也就是在一个更小的文本数据集上对其进行*微调*。这一做法源自于更广泛的*迁移学习*领域。

这里的目标是轻微调整或定制LLM的*下一个标记预测*行为，而不破坏或损坏模型权重中体现的基本“智能”——这使得LLM能够保留基础模型的大部分突现能力（如阅读理解、对话能力、推理能力等），但现在专门化于特定任务。例如，*指令调优*意味着对LLM进行微调，使其能够遵循指令。

在[HuggingFace数据集中心](https://huggingface.co/datasets)上有许多可用的指令调优数据集，按任务组织。一些数据集用于[问答](https://huggingface.co/datasets?task_categories=task_categories%3Aquestion-answering)，或者[文本摘要](https://huggingface.co/datasets?task_categories=task_categories%3Asummarization)。在绝大多数情况下，这些数据集都共享相同的基本底层架构，每个数据样本包含：

1.  *一个* ***提示***，即*指令*

1.  *一个* ***完成***，即*回应*

在这种设置下，微调的目标是增加（最终是*最大化*）LLM在输入*提示*时生成*完成内容*的概率。换句话说，回应“*完成*”了提示。我们很少，甚至几乎没有兴趣改变LLM生成提示本身的概率……因为提示只是LLM的输入。

![](../Images/d7e84fbf0c1f4bafe9f5883f11183b9a.png)

文本摘要示例（图像来自作者）

以[文本摘要](https://huggingface.co/datasets?task_categories=task_categories%3Asummarization)为例。一个典型的*提示*可能包括一个总结长篇新闻文章的指令，以及文章本身，而*完成内容*则是请求的摘要（参见HuggingFace上的[EdinburghNLP/xsum](https://huggingface.co/datasets/EdinburghNLP/xsum)数据集）。在这个数据集上对基础LLM进行微调的目标是增加LLM在给定指令+文章时生成摘要的可能性，而*不是*生成文章本身，或者如果只显示文章的前半部分，则生成文章的后半部分。

然而，针对*提示-完成*风格数据集的LLM微调中，出现了一种流行的方法，就是基本忽略*提示-完成*的区别，直接对整个文本序列进行微调——基本上延续了预训练基础模型时使用的相同过程，尽管指令调优的目标与预训练的目标完全不同。这导致了*教会LLM生成提示以及完成内容*。

我不完全确定为什么会这样，但最可能的原因是，这种习惯仅仅是从旧的基础模型训练协议中继承下来的，最初并没有这种区分。从我所了解的情况来看，基本的态度似乎是：*好吧，这有什么害处呢？只需在整个序列上进行微调，模型仍然会学会你想要的（根据提示生成结果）……它只是也会学到一些额外的东西。*

# 提示遮罩 - 与 - 提示削弱

最明显的解决方案是将提示标记从学习过程中剔除（或*零遮罩*）。PyTorch 允许通过 [CrossEntropyLoss](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html#torch.nn.CrossEntropyLoss) 函数的 `ignore_index=-100` 参数手动遮罩输入标记。将所有与提示标记对应的标签 ID 设置为 `-100`，迫使 CrossEntropyLoss 忽略这些标记的损失计算，从而仅对完成标记进行训练（在我看来，这是一个非常文档化不足的功能——我只是偶然发现的——在 [这里](https://huggingface.co/docs/transformers/model_doc/llama2#transformers.LlamaForCausalLM.forward.cache_position) 的 Llama 文档中埋藏着一处引用）。

单独来看，这并不真正是对提示遮罩的解决方案。它只是通过其他方式找到某些标记后，遮罩这些标记的一种手段。一些之前提到的提示遮罩方法采用了这种技术，而另一些则明确创建了一个二进制遮罩来实现相同的效果。虽然有用，但这个解决方案仍然是一个二进制开关，而不是*prompt-loss-weight*所允许的连续调节器。

然而，这引出了一个问题：如果提示遮罩*确实*能够改善指令微调，那么设置一个非零的*prompt-loss-weight*究竟有什么意义呢？为什么我们要仅仅*削弱*提示标记的影响，而不是完全消除它？

最近有一篇论文发布在 *arxiv* 上，标题是 [Instruction Fine-Tuning: Does Prompt Loss Matter?](https://arxiv.org/abs/2401.13586) 作者建议，少量的提示学习可能作为微调过程中的一种*正则化器*，防止模型过拟合完成文本。他们假设：

> …[非零] PLW 提供了一种独特的正则化效果，这种效果无法轻易地通过其他正则化方法替代…

即使是 OpenAI 的人员似乎也承认使用一个小的但非零的提示损失权重（PLW）是有益的。显然，他们曾通过微调 API 曝露了这个 PLW 参数，并且 [在线上仍然有一些文档](https://docs.google.com/document/d/1rqj7dkuvl7Byd5KQPUJRxc19BJt8wo0yHNwK84KfU3Q/edit#heading=h.8gg2gpi3wek2)，其中指出：

> 少量的提示学习有助于保持或增强模型理解输入的能力（来自[最佳实践：微调GPT-3进行文本分类](https://docs.google.com/document/d/1rqj7dkuvl7Byd5KQPUJRxc19BJt8wo0yHNwK84KfU3Q/edit)）

尽管他们后来移除了这个参数。然而，根据旧版[文档](https://docs.google.com/document/d/1rqj7dkuvl7Byd5KQPUJRxc19BJt8wo0yHNwK84KfU3Q/edit#heading=h.8gg2gpi3wek2)，他们使用了`PLW=0.1`（10%）的默认值，这意味着提示词的权重是完成词的1/10。

# 生成比率

在之前提到的论文中（[Instruction Fine-Tuning: Does Prompt Loss Matter?](https://arxiv.org/abs/2401.13586)），作者介绍了一个有用的量。给定一个指令数据集，他们定义了**生成比率**，或称***Rg***：

> 生成比率**Rg**是完成长度与提示长度的比率。然后我们将指令数据分为两大类。**Rg<1**的数据为短完成数据，**Rg>1**的数据为长完成数据。当应用于整个数据集时，我们取**R̅g**为平均的完成与提示比率。

对于具有较小**R̅g**值的数据集（即完成部分比提示部分*短*），他们发现PLW确实*很重要*（即使用错误的PLW值会降低性能）。如果你仔细想想，*许多*常见的指令微调数据集实际上具有完成部分比提示部分短的特性，这几乎是有意为之（比如：*文本摘要、信息提取*）

作为一个有趣的练习，我计算了在HuggingFace上几个流行指令数据集的**R̅g**值（[代码在这里](http://gen_ratios.py)）：

+   **7.6** | [Alpaca](https://huggingface.co/datasets/yahma/alpaca-cleaned)（通用指令）

+   **6.0** | [OpenHermes](https://huggingface.co/datasets/teknium/openhermes)（通用指令）

+   **3.6** | [Python-18k](https://huggingface.co/datasets/iamtarun/python_code_instructions_18k_alpaca)（代码指令）

+   **2.0** | [Databricks-Dolly-15k](https://huggingface.co/datasets/databricks/databricks-dolly-15k)（通用指令）

+   **1.1** | [OpenOrca](https://huggingface.co/datasets/polinaeterna/OpenOrca)（通用指令）

+   **0.2** | [SAMSum](https://huggingface.co/datasets/knkarthick/samsum)（文本摘要）

+   **0.1** | [XSum](https://huggingface.co/datasets/EdinburghNLP/xsum)（文本摘要）

+   **0.01** | [RACE](https://huggingface.co/datasets/ehovy/race)（问答/多项选择）

![](../Images/e303e9214ea3b772300d4dfb7e898950.png)

一些指令数据集的平均生成比率（**R**̅g）（图片来自作者）

在通过平均值总结任何一组数据时，良好的做法是查看数据的完整分布以进行合理性检查。算术均值在数据高度偏斜或偏离大致正态分布时可能具有误导性。我绘制了显示每个数据集**Rg**分布的直方图（顶部行）。底部行显示了相同的直方图，但x轴进行了对数缩放：

![](../Images/e3c3bb49517c6bbfaeaf2c2a52ad25e8.png)

线性和对数缩放的**Rg**直方图（图片来自作者）

这些图表表明，当数据集的**Rg**分布跨越多个数量级或在**Rg>1**和**Rg<1**区域都有显著表示（例如在[OpenOrca](https://huggingface.co/datasets/polinaeterna/OpenOrca)和其他**R̅g>1**的数据集中），分布可能会高度偏斜。因此，算术均值可能会受到较大值的不成比例影响，从而可能会误导分布的集中趋势。在这种情况下，在对数空间中计算均值（然后可以选择将其转换回原始尺度）可能会提供更有意义的汇总统计量。换句话说，使用*几何均值*可能更为合理：

## RACE阅读理解数据集

基于上面的**R̅g**表格，我决定将[RACE **R**e**A**ding **C**omprehension Dataset from **E**xaminations](https://huggingface.co/datasets/ehovy/race)（**R̅g=0.01**）作为一个很好的研究对象。多项选择的QA似乎是探索提示屏蔽效果的理想测试平台，因为与完成任务相比，提示通常非常长。无论提示长度如何，完成任务*始终*只有1个字符，即***A***、***B***、***C***或***D***（如果忽略特殊标记、分隔符等）。我的直觉是，*如果*有任何来自调节提示标记权重的影响，它们肯定会在这里显现出来。

如在[*数据集卡片*](https://huggingface.co/datasets/ehovy/race#dataset-card-for-race)中所述：

> RACE是一个大规模的阅读理解数据集，包含超过28,000篇文章和近100,000个问题。该数据集来源于中国的英语考试，面向中学和高中学生。该数据集可以用作机器理解的训练集和测试集。

QA模式很简单：提示提供一个*问题*，可能会有一些上下文（*文章*字段），然后列出四个*选项*。完成（*答案*）总是其中之一：A、B、C、D。这个[数据集查看器](https://huggingface.co/datasets/ehovy/race/viewer/all/train)托管在HuggingFace上，允许浏览完整的数据集，但这里是一个小例子：

![](../Images/11d3acd64e1dcc054e819da9cdc05dec.png)

RACE示例（截图来自[https://huggingface.co/datasets/ehovy/race/viewer/all/train](https://huggingface.co/datasets/ehovy/race/viewer/all/train))

# 交叉熵损失

在我们开始全面实施*提示-损失权重*并尝试在RACE数据集上进行实验之前，我们需要先了解损失的基本概念及其来源。简单来说，损失是衡量我们的模型（LLM）如何“拟合”（解释、预测）数据的一个指标。在微调（以及预训练）过程中，我们通过调整网络权重使模型更接近数据，从而减少损失。[链式法则（微积分）](https://en.wikipedia.org/wiki/Chain_rule)为我们提供了一个精确的算法，用于在给定损失函数和网络架构的情况下计算这些调整。

在LLM微调中，最常见的损失函数叫做*交叉熵损失*（CEL）。因此，大多数关于CEL的讨论都是围绕[交叉熵](https://en.wikipedia.org/wiki/Cross-entropy)的定义展开的，而交叉熵来源于信息论。虽然“交叉熵”在名称中就提到了，但通过*最大似然估计*（MLE）的角度来看，更直观的理解可以帮助我们理解CEL。我会从两个角度来尝试解释。

我们已经明确LLM是为了*下一个令牌预测*而设计的。这意味着LLM本质上是一个数学函数，它将令牌序列作为输入，并输出整个令牌词汇**V**中下一个令牌的*条件概率分布*。换句话说，它输出一个维度为**|V|**的概率值向量，并且这些概率的总和为1。（在集合符号中，**|S|**表示集合**S**的元素数量，或称为*基数*）

让我们用一个小的玩具例子来说明这个过程。假设我们的训练数据包含以下4个令牌序列：`The bird flew away`。给定前3个令牌（`The bird flew`），LLM可能会输出每一个可能的第4个令牌的概率向量——为了简单起见，我们假设列出的5个候选令牌（用洋红色标出）是唯一的可能性（即**|V|**=5）。函数***p(***⋅***)***表示LLM输出的条件概率（注意它们的和为1）：

![](../Images/90250fa83a062b9d6e7288751b823954.png)

（图片来自作者）

在训练（或微调）LLM时，我们会逐个遍历令牌序列，比较LLM生成的*下一个令牌分布*与序列中的*实际下一个令牌*，然后基于此计算该令牌的CEL。

这里需要注意的是，序列中的实际第4个令牌（`away`）在表中的概率并*不是*最高的。在训练过程中，我们希望稍微调整权重，以增加`away`的概率，同时减少其他令牌的概率。*关键*是选择正确的损失函数……它可以帮助我们精确计算每个令牌的每个权重需要调整多少。

一旦计算出每个 token 的损失，最终的损失将计算为所有 token 的*每 token 损失的平均值*。但首先，我们必须为这个每 token 的损失建立公式。

## 信息论解释

继续这个玩具问题，要计算第四个 token 位置的 CEL，我们将*实际的*第四个 token 与所有五个*可能的*第四个 token 的生成分布 ***p(***⋅***)*** 进行比较。事实上，我们将实际的第四个 token 视为一个分布 ***q(***⋅***)***（尽管它是一个退化的分布），其在数据 -`away`- 中出现的 token 值为 1，其他所有可能的第四个 token 的值为 0（这有时称为*独热编码*）。

![](../Images/81d0d62dc8f46ee4732ffc2a1d94ae0c.png)

（图片由作者提供）

我们将训练数据变形成这种奇怪的*独热*编码的概率表示 ***q(***⋅***)*** 的原因，是为了能够应用 *交叉熵* 公式，这是衡量两个离散概率分布之间*散度*的一种方法（顺便提一下，它对 q 和 p 不是对称的）：

![](../Images/22ded3dd0a7b6c664adecd0f0a103d4a.png)

其中 *x* 索引所有可能的状态（即 5 个 token）。这可以表示为：

![](../Images/f9fbd5b86aad874b097af917647bc44c.png)

所以，基本上 CEL 就是使用 ***q*** 向量从 ***p*** 向量中选择与数据中*实际*出现的 token -`away`- 对应的单个值（即将其乘以 1），并丢弃所有其他值（即乘以 0）。因此，我们只是遍历所有可能的状态（token），只选择一个并忽略其他所有。

## 最大似然估计（MLE）解释

在微调 LLM 时，我们寻求最大化在给定这些权重下训练数据的概率的 LLM 权重 θ，这通常称为这些权重的*似然* ℒ(θ) = ℙ(D|θ)。因此，我们需要一个表达式来计算这一数量。幸运的是，LLM 已经为我们提供了从下一个 token 概率中计算这一点的简便方法。

从*另一个* [链式法则（概率）](https://en.wikipedia.org/wiki/Chain_rule_(probability))开始，我们将 token 序列 **S** 的联合概率分解为*条件概率的乘积*：

![](../Images/662b2b28ff0a03a1ab0eb95b7d2d0af2.png)

链式法则（概率）

这个分解建立了下一个 token 预测与完整 token 序列联合概率之间的联系——联合概率只是所有条件概率的乘积。

使用 *i* 索引 token 序列 ***S*** *= (t₁,t₂,t₃,…, tᵢ ,…)* 中的 token，我们将使用以下简写来表示 LLM 为序列中第 *i* 个 token 输出的条件概率，给定 LLM 权重 θ 和前 *i-1* 个 token：

![](../Images/a9c88478067a2b32bf304997b5fef52e.png)

应该强调的是，*pᵢ*在这里**不是**一个向量（即不是对所有可能的下一个标记的分布），而只是表示为实际的*iᵗʰ*标记计算的概率，即上面示例中的黄色高亮行。

如果我们取序列的联合概率的对数，乘积会变成和（因为对数是单调的，这不会影响优化）：

![](../Images/0d0d77cf33b63e3499587c73e8d0518b.png)

现在我们可以将最终的对数和表达式（就在这里☝）️与标记序列上的*平均交叉熵损失* ***L*** 的公式连接起来：

![](../Images/1273a070cbec685a1eaedc31f0bed68a.png)

这就是因果语言模型的目标函数。通常，名称中的“*平均*”会被省略，直接称为“*交叉熵损失*”，但记住，从技术上讲，CEL是按标记级别计算的，然后对所有标记进行平均。希望从这个最终表达式中可以清楚地看出，*最小化CEL*等同于*最大化标记序列的概率*，这正是MLE所追求的目标。

这种表达式的一大便利之处在于，如果我们想要计算*任何子集*标记的损失，修改起来非常容易。回想一下，我们有时可能有兴趣找到能够最大化给定提示下完成的概率的LLM权重θ：

![](../Images/f48bcca2c0d444a7816af6bea26f8ba1.png)

我们可以通过仅对完成标记进行平均，轻松调整此情形下的损失。如果我们使用“𝕀c”表示所有完成标记的索引集，那么我们可以将*完成损失*表示为：

![](../Images/8c7af5c95c0b21617495b92bf0560582.png)

由于每个标记的损失已经基于序列中所有先前的标记进行了条件化，这意味着即使我们只对完成标记进行平均，提示也会自动包含在条件中。

# 提示损失权重

现在我们已经确定CEL是标记序列上每个标记损失的*平均*值，我们可以定义CEL的*加权平均*版本：

![](../Images/695a0bfe03541c9f8f5e9b7fc9968c4e.png)

根据我们如何设置权重*wᵢ*，我们可以使用这个公式定义多种损失。例如，如果我们将所有权重*wᵢ =1*，那么我们就恢复了之前的标准全序列CEL。然而，如果我们仅对完成标记设置*wᵢ =1*，对提示标记设置*wᵢ = 0*，那么我们得到*完成损失*。同样，*提示损失*通过仅在提示标记上设置*wᵢ =1*，其他情况下设置*wᵢ = 0*来定义。

由于我们很少（如果有的话）希望对完成令牌进行下调权重，我们将完成令牌的权重固定为*wᵢ =1*，但对于提示令牌，我们可以在[0:1]区间上定义一个连续值，称为`prompt_loss_weight`。这样，我们可以在训练过程中调节提示令牌的权重，从*wᵢ = 0*（完成损失）一直到*wᵢ = 1*（标准完整序列损失）。或者，我们甚至可以使用*wᵢ = 0.1*，为提示令牌赋予一个小但非零的权重。

## 损失实现

让我们深入了解损失是如何在[HuggingFace Transformers](https://huggingface.co/docs/transformers/en/index#-transformers)包中通常计算的。由于我们将在实验中微调[Llama-2–7b-chat-hf](https://huggingface.co/meta-llama/Llama-2-7b-chat-hf)模型，我们将查看[LlamaForCausalLM](https://github.com/huggingface/transformers/blob/52920b5dd5ad3b5e94209ef392ab5ceccbb1c869/src/transformers/models/llama/modeling_llama.py#L1104)，特别是[前向传播](https://github.com/huggingface/transformers/blob/52920b5dd5ad3b5e94209ef392ab5ceccbb1c869/src/transformers/models/llama/modeling_llama.py#L1216)，其中在训练过程中计算损失。

回想一下，损失是将每个*实际*令牌与LLM对该令牌的*预测*进行比较的一种方式（给定前面的实际令牌）——因此损失函数需要访问这两个数据结构。在这种情况下，损失输入两个张量：`logits`和`labels`。`labels`张量保存实际令牌（准确来说是*令牌ID*）。`logits`张量保存预测的下一个令牌概率，在*softmax*归一化之前（归一化会将这些概率的和强制为1——事实证明，保持这些值在原始、未经归一化的形式中更为高效）。

`logits`张量是3维的，形状为`[B,N,|V|]`，其中`B`是批量大小，`N`是序列长度（单位为令牌），`|V|`是令牌词汇大小。2维的`labels`张量仅包含令牌序列本身，因此它的形状是`[B,N]`。以下是通常计算CEL（交叉熵损失）的关键代码部分：

```py
# Shift-by-1 so that tokens < n predict n
shift_logits = logits[..., :-1, :].contiguous()
shift_labels = labels[..., 1:].contiguous()

# Flatten the tensors
shift_logits = shift_logits.view(-1, self.config.vocab_size)
shift_labels = shift_labels.view(-1)

# Enable model parallelism
shift_labels = shift_labels.to(shift_logits.device)

# Compute loss
loss_fct = CrossEntropyLoss()
loss = loss_fct(shift_logits, shift_labels)
```

对于`logits`的第2维度上每个位置的*i*，这个张量包含了在给定所有前面的令牌（直到第*i*个令牌）的情况下，预测*下一个*令牌（令牌*i+1*）的概率。这些概率需要与`labels`中实际的*i+1*ˢᵗ令牌进行比较。这就是为什么在前几行中会发生*偏移-1*的原因——为了使这两个值在每个令牌上对齐。

![](../Images/b7d018f5390df31764fa30e84168a1a4.png)

（图像来源：作者，灵感来自：[https://wandb.ai/capecape/alpaca_ft/reports/How-to-Fine-Tune-an-LLM-Part-2-Instruction-Tuning-Llama-2--Vmlldzo1NjY0MjE1](https://wandb.ai/capecape/alpaca_ft/reports/How-to-Fine-Tune-an-LLM-Part-2-Instruction-Tuning-Llama-2--Vmlldzo1NjY0MjE1)）

接下来发生的事情是，前两个维度被合并成一个（展平），然后张量被传递给 `[CrossEntropyLoss()](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html#torch.nn.CrossEntropyLoss)`，这是一个 PyTorch 函数，它输出最终的损失值。

## 自定义损失函数

默认情况下，`[CrossEntropyLoss()](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html#torch.nn.CrossEntropyLoss)` 会对所有标记进行平均，以输出一个单一的标量值。这一最终的平均（对所有标记的平均）称为*归约*操作。但如果我们用*无*归约操作来实例化损失：

```py
loss_fct = CrossEntropyLoss(reduction="none")
```

那么*不*进行平均，最终的损失将是一个一维张量（长度为 `BxN`），包含每个标记的损失（损失张量将是二维的，形状为 `[B,N]`，没有先前的展平步骤）。这就是我们如何获得每个标记的损失，以便计算我们自己的*加权*平均值。

在分词过程中（[查看完整代码详情](https://github.com/davidsvaughn/prompt-loss-weight)），我们为每个序列创建了两个额外的二进制掩码，即*提示掩码*和*完成掩码*。二进制掩码仅仅是由 1 和 0 组成的向量。提示掩码将所有提示标记标记为 1（否则为 0），而完成掩码则相反。然后，我们可以通过这两个掩码的简单线性组合，得到加权平均版本的 CEL 的权重 *wᵢ*，即将提示掩码乘以 PLW 并加到完成掩码上：

![](../Images/92685972975d64282b6f7fbd95bec926.png)

损失权重 = prompt_loss_weight * prompt_mask + completion_mask（图像由作者提供）

我们从 HuggingFace 的 [Trainer](https://huggingface.co/docs/transformers/en/main_classes/trainer) 子类化，定义了一个名为 `PLWTrainer` 的新训练器类。我们将首先重写两个函数：

+   `__init__()`: 构造函数接收额外的 `prompt_loss_weight` 参数

+   `compute_loss()`: 使用 `prompt_loss_weight` 计算加权损失

```py
class PLWTrainer(Trainer):
    def __init__(self, *args, prompt_loss_weight=1.0, **kwargs):
        super().__init__(*args, **kwargs)
        self.plw = prompt_loss_weight

    def compute_loss(self, model, inputs, return_outputs=False):
        # get outputs without computing loss (by not passing in labels)
        outputs = model(input_ids=inputs["input_ids"], 
                        attention_mask=inputs["attention_mask"])
        logits = outputs.get("logits")
        labels = inputs.pop("labels")

        # compute per-token weights
        weights = self.plw * inputs["prompt_mask"] + inputs["completion_mask"]

        # Shift-by-1 so that tokens < n predict n
        shift_logits = logits[..., :-1, :].contiguous()
        shift_labels = labels[..., 1:].contiguous()
        shift_weights = weights[..., 1:].contiguous()

        # Enable model parallelism
        shift_labels = shift_labels.to(shift_logits.device)
        shift_weights = shift_weights.to(shift_logits.device)

        # Compute per-token losses
        loss_fct = CrossEntropyLoss(reduction="none")
        token_losses = loss_fct(shift_logits.view(-1, shift_logits.size(-1)), 
                                shift_labels.view(-1))

        # Compute weighted average of losses
        loss = token_losses @ shift_weights.view(-1) / shift_weights.sum()
        return (loss, outputs) if return_outputs else loss
```

如果构造函数没有显式传入 `prompt_loss_weight` 的值，默认值（`prompt_loss_weight=1`）意味着我们会恢复到原始 [Trainer](https://huggingface.co/docs/transformers/en/main_classes/trainer) 的继承行为（即最小化完整序列的损失）。然而，如果我们传入其他的 `prompt_loss_weight` 值，我们就可以得到一系列不同的损失函数。

我们几乎准备好尝试新的损失函数了！但在此之前，我们需要确保能够观察和理解它对微调过程产生的影响（如果有的话）…

# 验证指标

## 分别跟踪提示和完成的损失

在微调过程中，通常的做法是跟踪模型在*验证集*上的表现，以决定何时结束训练。*验证集*，也称为*保留集*，是从训练数据中“保留”的一个随机子集，以确保模型不会学习或记住它。模型在此集上的表现被视为模型在实际应用中面对新数据时的表现估计。这就是大多数初级机器学习课程中所讲的经典“训练与验证曲线”产生的原因：

![](../Images/7833346133535cecf9c7e0bde26846c3.png)

（图片来自作者）

这里的教训是，绿色（验证）曲线的最小点代表了*最佳*的训练步数，超过这个点，模型开始*过拟合*或记住训练数据，而不是继续从数据中学习可泛化的模式。虽然无法知道*真正*的最佳停止点，但通过跟踪验证集上的度量，我们可以相对准确地估计它。然而，这也有一个权衡：更大的验证集会带来更好的估计，但也意味着训练集会变小，所以我们不希望保留过多的样本。5%–15%是一个很好的经验法则。

通常，在微调大型语言模型（LLM）时，最小化训练集上的目标损失函数也成为用于跟踪验证集表现的默认度量，从而确定最佳停止时机。讨论通常集中在**两种选择**上：

1.  最小化训练集上的*完整序列损失*——并在验证集上跟踪

1.  最小化训练集上的*完成损失*——并在验证集上跟踪

但是——我们可以自由地在验证集上跟踪*任何*度量（或多个度量），不仅仅是用作训练目标的损失。这引出了启发本文的最初想法——我想尝试**第三种选择**：

![](../Images/423fd1c1435ec4c639a2c13363cc66fa.png)

然而，在围绕PLW调整我的方法后，这一过程演变成了：

![](../Images/4110bdc6e30da009b53840226b43f8bc.png)

为了实现这一点，我们首先需要编写一个自定义度量，将验证的完整序列损失分解为提示损失和完成损失，我们将在下一节中进行说明。我们将使用在自定义损失函数中使用过的相同技巧。

***插曲***：你可能会注意到，在大型语言模型（LLM）社区中，实践者有时完全绕过了*停止准则*问题，遵循像*仅微调一个周期*之类的简单规则。有时这很有道理，比如当微调模型生成更多*主观*内容时，如电子邮件、诗歌或笑话。但是当微调数据集更侧重于*正确性*时，比如编写代码、解决数学问题或多选问答（我们将在下文中看到的一个例子），那么确实有必要监控验证损失和/或其他验证度量。所以我们需要确保谨慎操作。

然而，这并不意味着标记序列的*正确性*是单个标记正确性的简单线性函数。标记序列的语义意义可以是个复杂的、高度*非线性*的函数，依赖于各个标记的意义。这就是为什么很容易构造出许多例子，其中标记级别的一个微小变化可以显著改变整个句子的意义——只需在合适的位置插入“not”就能完全颠倒句子的意思！

尽管如此，在许多情况下，平均每个标记的损失仍然可以作为训练/微调过程中LLM预测整体质量的一个良好指标。这是因为[教师强制](https://en.wikipedia.org/wiki/Teacher_forcing)的标准做法确保每个标记的预测是基于训练/验证数据中“正确”（即地面真实值）的前一个标记，而不是基于模型自身的前一个标记预测（这是推理/文本生成过程中发生的情况）。

但没有一个度量是完美的，这就是为什么始终使用多种评估方法，包括特定任务的度量和人工评估，变得非常重要。

## 定义自定义`度量`

定义自定义验证度量的常见方法之一，在使用[HuggingFace Trainer](https://huggingface.co/docs/transformers/en/main_classes/trainer)时，是重写Trainer的默认`compute_metrics()`函数，该函数在训练期间会定期在验证集上运行。然而，默认情况下，这个函数并不会接收到足够的信息来计算提示损失或完成损失。

具体来说，对于每个验证集序列，`compute_metrics()`接收到的是*预测*标记和*实际*标记。这仅适用于计算某些度量，例如标记准确性，但不适用于计算损失。幸运的是，我们可以通过重写另一个函数`preprocess_logits_for_metrics()`来修改传递给`compute_metrics()`的数据。

为了计算*损失*，我们需要访问`logits`中包含的实际概率分布。回想一下，一个用于下一个标记预测的LLM，在标记序列的每个点上，会为下一个标记生成一个覆盖词汇表中所有可能标记的概率分布（`|V|=32000`）。这个分布存储在`logits`中，其形状为`[B,N,|V|]`。

默认情况下，`preprocess_logits_for_metrics()`将对这个`logits`张量沿最后一维（即`|V|`维度）进行*argmax*操作，并将这些标记索引传递给`compute_metrics()`。

```py
# from preprocess_logits_for_metrics
predictions = logits.argmax(-1)[..., :-1]
```

这些预测表示 LLM *本该* 为每个验证序列中的每个令牌位置预测的令牌，给定前面的令牌（最后一个令牌的预测被截断，因为没有真实值与之比较）。但正如我们所见，要计算每个令牌的损失，我们实际上不需要知道最高概率的令牌（通过 *argmax* 返回的预测）——我们需要知道 LLM 为每个验证序列中的 *实际* 令牌分配的概率，给定前面的令牌。

一种解决方案是直接将整个 `logits` 张量传递给 `compute_metrics()`*，* 然后在其中计算损失以及其他任何指标，比如准确度*。* 然而，这种方法有一个严重的问题：由于 [Trainer](https://huggingface.co/docs/transformers/en/main_classes/trainer) 的设置，`preprocess_logits_for_metrics()` 函数是在 GPU（按批次）上运行的，而 `compute_metrics()` 是在 CPU 上运行的（针对整个验证集，即所有批次重新组合后）。并且，`preprocess_logits_for_metrics()` 在 GPU 上运行的 *原因* 是 `logits` 张量可能变得 **极其** 大。

仅仅给你一个概念我的实验中，批次大小（B）为 8，序列长度（N）为 2048，这导致一个张量包含 B x N x |V| = 8 x 2048 x 32000 ≈ **42 亿** 个值（每个 GPU）！

GPU 可以处理这个巨大的张量，但如果我们尝试传递它，CPU 会爆炸。我们必须首先在 `preprocess_logits_for_metrics()` 内执行某种缩减操作，以去除这个巨大的第三维度。

这没有单一正确的方法。一种选择是从 `logits` 中选择为每个实际（真实）令牌生成的概率，并将其传递给 `compute_metrics()`，然后在 CPU 上计算损失*。* 这当然可以工作*。* 然而，更好的主意是使用 GPU 的全部处理能力，在 `preprocess_logits_for_metrics()` 内进行更多的计算，然后再将任务交给 CPU 端。

回想一下，交叉熵损失是针对令牌序列的 *平均* *每个令牌* *损失*。因此，我们可以使用 `preprocess_logits_for_metrics()` 来计算包含所有 *每个令牌* 损失的张量，并将这个张量传递给 `compute_metrics()` 以便稍后进行平均*。*

一个小的复杂问题是，`preprocess_logits_for_metrics()` 被设置为将一个 *单一* 值传递给 `compute_metrics()`*。* 然而，我们需要传递 *两个* 单独的张量。因为我们有兴趣在验证集上跟踪多个指标（如提示损失和完成损失，以及完成令牌准确度）——我们需要两个张量：*predictions* 用于完成准确度，*per-token-losses* 用于两个损失。幸运的是，从 `preprocess_logits_for_metrics()` 传递到 `compute_metrics()` 的单一值可以是单个张量或张量元组。

具体来说，`compute_metrics()`接收一个单一的参数`data`，它是一个工具类`[transformers.EvalPrediction](https://github.com/huggingface/transformers/blob/174890280b340b89c5bfa092f6b4fb0e2dc2d7fc/src/transformers/trainer_utils.py#L149)`的实例。`preprocess_logits_for_metrics()`返回的值被分配给`EvalPrediction`的`.predictions`字段（在批次被聚合成一个张量并转换为numpy数组之后）。[spec for](https://github.com/huggingface/transformers/blob/174890280b340b89c5bfa092f6b4fb0e2dc2d7fc/src/transformers/trainer_utils.py#L161) `[.predictions](https://github.com/huggingface/transformers/blob/174890280b340b89c5bfa092f6b4fb0e2dc2d7fc/src/transformers/trainer_utils.py#L161)`表示它可以容纳一个单一的数组或一个数组元组（`predictions: Union[np.ndarray, Tuple[np.ndarray]]`），因此我们可以继续进行。

```py
# uses PyTorch tensors (on GPU)
def preprocess_logits_for_metrics(logits, labels):
    # get predictions
    token_preds = logits.argmax(-1)[..., :-1]

    # compute per-token losses
    loss_fct = CrossEntropyLoss(reduction="none")
    shift_logits = logits[..., :-1, :].contiguous()
    shift_labels = labels[..., 1:].contiguous()
    token_losses = loss_fct(shift_logits.transpose(1, 2), shift_labels)

    # pass predictions and losses to compute_metrics()
    predictions = (token_preds, token_losses)
    return predictions
```

现在我们可以定义`compute_metrics()`…

```py
# uses numpy arrays (on CPU)
def compute_metrics(data):
    # data.predictions contains the tuple (token_preds, token_losses)
    # from preprocess_logits_for_metrics()
    token_preds, token_losses = data.predictions

    # shift labels and masks
    labels = data.label_ids[..., 1:]
    shift_prompt_mask = prompt_mask[..., 1:]
    shift_comp_mask = completion_mask[..., 1:]

    # average both losses (prompt and completion) over their respective tokens
    prompt_loss = token_losses.reshape(-1) @ shift_prompt_mask.reshape(-1) / shift_prompt_mask.sum()
    completion_loss = token_losses.reshape(-1) @ shift_comp_mask.reshape(-1) / shift_comp_mask.sum()

    # compute response token accuracy
    nz = np.nonzero(shift_comp_mask)
    idx = np.where(np.isin(labels[nz], ABCD_token_ids))
    accuracy = np.mean(preds[nz][idx] == labels[nz][idx])

    return {
        'comp_loss': completion_loss,
        'prompt_loss': prompt_loss,
        'acc': accuracy,
    }
```

这一切看起来应该很熟悉，因为我们正在使用与定义自定义损失函数时相同的思想。同样，我们依赖于`prompt_mask`和`completion_mask`来选择适当的令牌子集以计算每个损失。如果你在想`prompt_mask`和`completion_mask`是在哪里定义的，它们发生在函数范围之外，但通过[函数闭包](https://stackoverflow.com/questions/13857/can-you-explain-closures-as-they-relate-to-python)使其可用，这是一种在“函数工厂”中经常使用的方法（[查看完整脚本了解详情](https://github.com/davidsvaughn/prompt-loss-weight/blob/main/run_plw.py)）。

完成令牌的*准确性*仅在实际的多项选择答案令牌（即*A,B,C,D*）上计算，而完成*损失*则包括在聊天模板中使用的其他特殊令牌（即空格、*bos_token*、*eos_token*等）。引用的`ABCD_token_ids`使我们能够隔离答案令牌并忽略其他令牌。

# 实验

最后，让我们在改变PLW的同时进行一些微调运行……

## 完整序列训练：PLW=1

实现细节：我使用了[Llama-2–7b-chat-hf](https://huggingface.co/meta-llama/Llama-2-7b-chat-hf)作为基础模型，并通过HuggingFace的[PEFT](https://huggingface.co/docs/peft/index)（参数高效微调）库，使用[LoRA](https://arxiv.org/pdf/2106.09685.pdf)（低秩适应）方法对[RACE阅读理解数据集](https://huggingface.co/datasets/ehovy/race)的一个子集进行了微调。我通过微软的[DeepSpeed](https://github.com/microsoft/DeepSpeed)库，使用多GPU训练显著加速了微调过程。再次提醒，[查看完整代码](https://github.com/davidsvaughn/prompt-loss-weight/blob/main/run_plw.py)了解所有细节。

以下第一张图展示了在*最小化标准的完整序列损失*时，所有验证集指标的演变。每条曲线都有自己的 y 轴标签（按颜色编码），因为它们都在不同的尺度上（除了提示和完整序列损失，它们使用相同的尺度，位于左侧）。可以看到，响应准确率与完成损失的变化非常接近，但方向相反，正如预期的那样。我在完成损失和完整序列损失的最小值处绘制了虚线蓝色和绿色线条，显示它们与准确率交点的位置。

![](../Images/b21a59b05a8cbdc459de2d5a853ca7f6.png)

RACE 验证集指标（图像由作者使用[*Matplotlib*](https://matplotlib.org/)制作）

主要需要观察的是**提示损失和完成损失的最小值完全不同步**——由于提示损失主导了完整序列损失（记住**R̅g = 0.01**），因此完整序列损失基本上只是提示损失略微下移的结果，并且它们共享相同的最小值。

这意味着如果你盲目遵循流行做法，使用验证集完整序列损失的最小值作为停止标准——刚好在第 2 个 epoch 之前——此时完成损失仍然很高——**微调后的模型准确率仅为 53%！**

但是，**仅仅通过*追踪*完成损失**（与使用 PLW=0 在我们自定义损失函数中进行直接最小化相对，我们接下来会这样做），你将继续微调到 4.5 个 epoch，在这个点上完成损失达到最小值，并且**准确率提高到 75%！**

## 仅完成训练：PLW=0

现在，我们将转向范围的另一端，完全屏蔽掉提示符令牌。我们所需要做的就是将 `PLWTrainer` 初始化为 `prompt_loss_weight=0`。以下是这些结果的绘图：

![](../Images/b8283e524f2a14f9a107312392e9473f.png)

RACE 验证集指标（图像由作者使用[*Matplotlib*](https://matplotlib.org/)制作）

有两件重要的事情发生了变化：

1.  **微调收敛*速度快得多*** **到达最小完成损失** - **并且最优准确率 - 只需不到 2 个 epoch（而不是 4.5 个 epoch）**

1.  **最优准确率也提高了——从 75% 跃升到 80%**

另一个有趣的现象是，提示损失并没有像之前的图一样下降，而是保持在一个水平上，甚至稍微上升（请特别注意提示损失的 y 轴刻度——在左侧）。换句话说，*提示符令牌完全没有学习*，而从微调中剔除它们反而提高了收敛速度和最大准确率。看起来是双赢！

## 探索完整的 PLW 范围

请记住，如果我们使用任何分数值 `0 < PLW < 1`，则提示符令牌对总损失的影响会被减弱，但不会消除。下面我绘制了在六个不同 `PLW` 值下的验证集完成损失和 QA 准确率：[1, 0.5, 0.2, 0.1, 0.01, 0]

![](../Images/bd4e5ddd4711e58372372e2a9d760156.png)

RACE 验证集指标（图像由作者使用 [*Matplotlib*](https://matplotlib.org/) 制作）

最引人注目的是，三个最小 `PLW` 值 `[0,0.01,0.1]` 的完成损失收敛到最小值的速度要快得多。最快的收敛似乎发生在 `PLW=0` 时，但与下两个最小值相比，只有微小的差异。查看准确率后，似乎这三个最小的 `PLW` 值中的任何一个都将在大约第 2 个周期时达到最优准确率（~80%）。

同样有趣的是，将每个完成损失曲线的收敛行为与其相应的准确率曲线进行比较。在达到最小值后，所有六条完成损失曲线都开始缓慢上升，而所有准确率曲线则趋于平稳而不下降……我们如何解释这一现象呢？

## 离题：损失还是 token 准确率 — 哪个更值得追踪？

记住，下一 token 的预测是通过选择给定前一个 tokens 的条件下概率最高的 token 来完成的。token 准确率的公式只考虑 token 是否正确，而交叉熵损失的公式实际上考虑了这些概率的 *值*。那么，解释这两张图之间差异的原因可能是什么呢？

好吧，由于 token 准确率保持稳定，这意味着具有最高概率的 token（即 *argmax* token）保持相对恒定，但这些 *最大* *值* 必须在稳步下降——换句话说，*模型对其（大部分正确的）token 预测的信心在减弱*。这可以被视为一种轻度的过拟合情况，其中最大值受到影响，但不足以影响 argmax 值。

这个例子说明了为什么有些人说 [追踪 token 准确率比追踪验证损失更好](https://twitter.com/Teknium1/status/1703227799979663408)。就我个人而言，我认为争论哪一个更好是愚蠢的，因为你不必选择……可以同时追踪两者！两者都是有价值的指标。token 准确率可能最终是你关心的最大化目标（在很多情况下，至少是这样）。但我也想知道模型是否以及何时对其（大部分）正确的预测变得不那么自信（就像我们上面看到的那样），所以我也会追踪完成损失。

更好的是，最优策略（在我看来）应该是同时跟踪模型在基准测试或*多个基准测试*上的表现，以便更全面地了解模型在微调过程中的演变。可能出现的情况是，尽管你的LLM在验证集上的纯粹标记准确性不断提高，但它的*回应变得更加重复和机械化*，因为验证集不够多样（实际上我在我的日常工作中见过这种情况）。始终记住真正的最终目标是什么……在几乎所有情况下，验证集上的标记准确性最多只是你真正目标的一个平庸代理。

# 结论

我们对变化的提示-损失权重（PLW）对大型语言模型（LLM）指令微调效果的探索突出了几个重要概念：

+   **将训练目标与验证指标解耦**：即使不改变训练目标函数中提示词的权重，我们也发现，通过*跟踪*正确的验证指标（例如完成损失或准确度），我们可以改善结果。

+   **PLW *确实可以*影响模型性能**：通过减少PLW，我们发现微调后的模型性能有所提高。令人惊讶的是，实际上并不需要完全的提示屏蔽来实现最大改进，因为将PLW减少到0.1以下似乎不会产生额外的效果。是否这种行为可以推广到其他数据集，必须逐个案例评估。

+   **PLW *确实可以*影响收敛速度**：同样地，通过减少PLW，我们发现微调后的模型收敛速度更快，能够更快达到最佳状态。这个效应可能在很大程度上独立于模型性能的变化——即根据数据集的不同，这两个效应可能会独立出现。

+   **数据集特定优化**：根据具体的数据集和任务，最优的提示-损失权重（PLW）很可能会有很大差异。在许多情况下，PLW甚至可能没有任何效果。在RACE数据集上看到的显著改进可能无法推广到所有的微调场景，这凸显了实验的重要性。

未来的研究方向可能包括：

+   探索PLW对更多数据集的影响，超越指令数据集，像那些具有更大生成比率或更长对话的聊天数据集

+   开发在微调过程中动态调整的自适应PLW策略

+   研究PLW对模型性能其他方面的影响，例如泛化能力和鲁棒性

*希望你觉得这稍微有点用。我随时欢迎反馈和修改！你可以通过* [*LinkedIn*](https://www.linkedin.com/in/davidsvaughn/) *联系我。本文中的图片是我的，除非另有说明。*

# 资源

+   与本教程相关的所有代码可以通过[此链接](https://github.com/davidsvaughn/prompt-loss-weight)访问。
