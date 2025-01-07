# 使用Unsloth超高效微调Llama 3.1

> 原文：[https://towardsdatascience.com/fine-tune-llama-3-1-ultra-efficiently-with-unsloth-7196c7165bab?source=collection_archive---------0-----------------------#2024-07-29](https://towardsdatascience.com/fine-tune-llama-3-1-ultra-efficiently-with-unsloth-7196c7165bab?source=collection_archive---------0-----------------------#2024-07-29)

## *适合初学者的先进监督微调指南*

[](https://medium.com/@mlabonne?source=post_page---byline--7196c7165bab--------------------------------)[![Maxime Labonne](../Images/a7efdd305e3cc77d5509bbb1076d57d8.png)](https://medium.com/@mlabonne?source=post_page---byline--7196c7165bab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7196c7165bab--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7196c7165bab--------------------------------) [Maxime Labonne](https://medium.com/@mlabonne?source=post_page---byline--7196c7165bab--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7196c7165bab--------------------------------) ·阅读时间：12分钟·2024年7月29日

--

![](../Images/442eac2ef94d8b6584ef18418dcbdb93.png)

由作者使用DALL-E 3生成的图片

最近发布的Llama 3.1提供了具有令人难以置信的性能水平的模型，缩小了封闭源模型与开放权重模型之间的差距。与使用冻结的、通用的LLM（如GPT-4o和Claude 3.5）不同，您可以根据特定的使用场景对Llama 3.1进行微调，从而在更低的成本下实现更好的性能和定制化。

![](../Images/e2bfb879bf9d5078c60062fe6e5f8c59.png)

图片来源：作者

在本文中，我们将全面概述监督微调。我们将其与提示工程进行比较，以了解何时使用它最为合适，详细介绍主要的技术及其优缺点，并介绍一些重要概念，如LoRA超参数、存储格式和聊天模板。最后，我们将在Google Colab中实践这一技术，使用Unsloth进行Llama 3.1 8B的微调，并应用最先进的优化方法。

本文中使用的所有代码都可以在[Google Colab](https://colab.research.google.com/drive/164cg_O7SV7G8kZr_JXqLd6VC7pd86-1Z?usp=sharing)和[LLM课程](https://github.com/mlabonne/llm-course)中找到。

# 🔧 监督微调

![](../Images/721214d6a9b43ef4eaa195651d4a8119.png)

图片来源：作者

监督微调（SFT）是一种**改进和定制**预训练 LLM 的方法。它涉及在一个较小的指令和答案数据集上对基础模型进行重新训练。其主要目标是将一个预测文本的基本模型转变为一个能够遵循指令并回答问题的助手。SFT 还可以提高模型的整体性能，添加新的知识，或将其适应特定的任务和领域。经过微调的模型可以通过一个可选的偏好对齐阶段（参见[我关于 DPO 的文章](https://mlabonne.github.io/blog/posts/Fine_tune_Mistral_7b_with_DPO.html)）来去除不需要的回答，修改其风格等。

下图展示了一个指令样本。它包括一个系统提示来引导模型，一个用户提示来提供任务，以及模型预期生成的输出。你可以在[💾 LLM 数据集](https://github.com/mlabonne/llm-datasets)的 GitHub 仓库中找到一份高质量的开源指令数据集列表。

![](../Images/4f1f2c16c4190c178e4fa8be8bccb8c9.png)

作者提供的图片

在考虑 SFT 之前，我建议尝试像**少量示例提示**或**检索增强生成**（RAG）这样的提示工程技术。实际上，这些方法可以在无需微调的情况下解决许多问题，适用于封闭源代码或开放权重的模型（例如，Llama 3.1 Instruct）。如果这种方法无法满足你的目标（如质量、成本、延迟等方面），那么当有指令数据可用时，SFT 就成为一个可行的选择。请注意，SFT 还提供了诸如额外的控制和定制化等好处，可以创建个性化的 LLM。

然而，SFT 也有局限性。它在利用已经存在于基础模型中的知识时效果最佳。学习完全新的信息，比如一种未知的语言，可能会面临挑战，并且更容易导致频繁的幻觉。对于基础模型未知的新领域，建议首先在原始数据集上持续进行预训练。

在这一系列的另一端，指令模型（即已经微调的模型）可能已经非常接近你的需求。例如，一个模型可能表现得非常好，但声明它是由 OpenAI 或 Meta 而不是你训练的。在这种情况下，你可能希望通过偏好对齐稍微调整指令模型的行为。通过为少量指令（100 到 1000 个样本）提供选择和拒绝样本，你可以迫使 LLM 声明是由你而不是 OpenAI 训练的。

# ⚖️ SFT 技术

三种最受欢迎的 SFT 技术是完全微调、LoRA 和 QLoRA。

![](../Images/a95694ba5600c25c2a3027f40c66b853.png)

作者提供的图片

**全量微调**是最直接的SFT技术。它涉及在指令数据集上重新训练预训练模型的所有参数。此方法通常提供最佳结果，但需要大量计算资源（对一个8B模型进行微调需要几张高端GPU）。由于它修改了整个模型，因此也是最具破坏性的方法，可能导致之前的技能和知识出现灾难性遗忘。

**低秩适应（LoRA）**是一种流行的参数高效微调技术。它不是重新训练整个模型，而是冻结权重，并在每个目标层引入小型适配器（低秩矩阵）。这使得LoRA训练的参数数量远低于全量微调（不到1%），从而减少了内存使用和训练时间。此方法是非破坏性的，因为原始参数被冻结，适配器可以随时更换或组合。

**QLoRA（量化感知低秩适应）**是LoRA的扩展，提供了更大的内存节省。与标准LoRA相比，它可额外节省最多33%的内存，使其在GPU内存受限的情况下尤为有用。尽管效率提高了，但它的训练时间更长，QLoRA的训练时间通常比普通LoRA多花费约39%的时间。

虽然QLoRA需要更长的训练时间，但其显著的内存节省使得在GPU内存受限的情况下，QLoRA成为唯一可行的选择。因此，接下来我们将在Google Colab中使用此技术对Llama 3.1 8B模型进行微调。

# 🦙 微调Llama 3.1 8B

为了高效地微调[Llama 3.1 8B](https://huggingface.co/meta-llama/Meta-Llama-3.1-8B)模型，我们将使用Daniel和Michael Han开发的[Unsloth](https://github.com/unslothai/unsloth)库。得益于其定制的内核，Unsloth相比其他选项提供了2倍更快的训练速度和60%的内存使用率，使其在像Colab这样的受限环境中表现尤为出色。不幸的是，Unsloth目前仅支持单GPU设置。对于多GPU设置，我推荐流行的替代方案，如[TRL](https://huggingface.co/docs/trl/en/index)和[Axolotl](https://github.com/OpenAccess-AI-Collective/axolotl)（这两者也都将Unsloth作为后端）。

在这个示例中，我们将在[mlabonne/FineTome-100k](https://huggingface.co/datasets/mlabonne/FineTome-100k)数据集上进行QLoRA微调。这是[arcee-ai/The-Tome](https://huggingface.co/datasets/arcee-ai/The-Tome)的一个子集（没有[arcee-ai/qwen2–72b-magpie-en](https://huggingface.co/datasets/arcee-ai/qwen2-72b-magpie-en)），我使用[HuggingFaceFW/fineweb-edu-classifier](https://huggingface.co/HuggingFaceFW/fineweb-edu-classifier)重新过滤过。请注意，这个分类器并不是为评估指令数据质量而设计的，但我们可以将其作为粗略的代理。生成的FineTome是一个超高质量的数据集，包括对话、推理问题、函数调用等内容。

让我们首先安装所有必需的库。

```py
!pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"
!pip install --no-deps "xformers<0.0.27" "trl<0.9.0" peft accelerate bitsandbytes
```

安装完成后，我们可以按如下方式导入它们。

```py
import torch
from trl import SFTTrainer
from datasets import load_dataset
from transformers import TrainingArguments, TextStreamer
from unsloth.chat_templates import get_chat_template
from unsloth import FastLanguageModel, is_bfloat16_supported
```

现在让我们加载模型。由于我们要使用QLoRA，我选择了预量化的[unsloth/Meta-Llama-3.1–8B-bnb-4bit](https://huggingface.co/unsloth/Meta-Llama-3.1-8B-bnb-4bit)。这个4位精度版本的[meta-llama/Meta-Llama-3.1–8B](https://markdown-to-medium.surge.sh/meta-llama/Meta-Llama-3.1-8B)比原始16位精度模型（16 GB）要小得多（5.4 GB），且下载速度更快。我们使用bitsandbytes库以NF4格式加载。

在加载模型时，我们必须指定最大序列长度，这限制了其上下文窗口。Llama 3.1支持最大128k的上下文长度，但由于它消耗更多计算资源和显存，在这个示例中我们将其设置为2,048。最后，`dtype`参数会自动检测你的GPU是否支持[BF16格式](https://mlabonne.github.io/blog/posts/Introduction_to_Weight_Quantization.html#background-on-floating-point-representation)，以便在训练过程中提供更多的稳定性（此功能仅适用于Ampere及更新的GPU）。

```py
max_seq_length = 2048
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="unsloth/Meta-Llama-3.1-8B-bnb-4bit",
    max_seq_length=max_seq_length,
    load_in_4bit=True,
    dtype=None,
)
```

现在我们的模型已经加载为4位精度，我们希望通过LoRA适配器为其准备好参数高效的微调。LoRA有三个重要的参数：

+   **秩**（r），决定LoRA矩阵的大小。秩通常从8开始，但可以达到256。更高的秩可以存储更多的信息，但会增加LoRA的计算和内存开销。我们在这里将其设置为16。

+   **Alpha**（α），更新的缩放因子。Alpha直接影响适配器的贡献，通常设置为秩值的1倍或2倍。

+   **目标模块**：LoRA可以应用于各种模型组件，包括注意力机制（Q、K、V矩阵）、输出投影、前馈块和线性输出层。尽管最初主要集中于注意力机制，但将LoRA扩展到其他组件也已证明具有益处。然而，适配更多模块会增加可训练参数的数量和内存需求。

在这里，我们将r=16，α=16，并将每个线性模块作为目标以最大化质量。我们不使用dropout和偏置以加快训练速度。

此外，我们将使用[Rank-Stabilized LoRA](https://arxiv.org/abs/2312.03732)（rsLoRA），它通过将LoRA适配器的缩放因子修改为与1/√r成比例，而不是与1/r成比例，来稳定学习（特别是对于更高的适配器秩），并允许随着秩的增加提高微调性能。梯度检查点由Unsloth处理，将输入和输出嵌入保存到磁盘，以节省显存（VRAM）。

```py
model = FastLanguageModel.get_peft_model(
    model,
    r=16,
    lora_alpha=16,
    lora_dropout=0,
    target_modules=["q_proj", "k_proj", "v_proj", "up_proj", "down_proj", "o_proj", "gate_proj"], 
    use_rslora=True,
    use_gradient_checkpointing="unsloth"
)
```

使用这个LoRA配置，我们只训练42百万个参数（在80亿个参数中占0.5196%）。这展示了LoRA相比完全微调的高效性。

现在我们加载并准备数据集。指令数据集以**特定格式**存储：可以是Alpaca、ShareGPT、OpenAI等。首先，我们需要解析这种格式以提取我们的指令和答案。我们的[mlabonne/FineTome-100k](https://huggingface.co/datasets/mlabonne/FineTome-100k)数据集使用ShareGPT格式，其中包含一个独特的“conversations”列，存储以JSONL格式的消息。与像Alpaca这样更简单的格式不同，ShareGPT非常适合存储多轮对话，这更接近用户与LLM（大语言模型）的交互方式。

一旦我们的指令-答案对被解析，我们需要重新格式化它们，以遵循**聊天模板**。聊天模板是一种构建用户和模型之间对话的方式。它们通常包含特殊的标记，用于标识消息的开始和结束，谁在说话等等。基础模型没有聊天模板，因此我们可以选择任何一种：ChatML、Llama3、Mistral等。在开源社区中，ChatML模板（最初来自OpenAI）是一个流行的选择。它仅添加了两个特殊标记（`<|im_start|>`和`<|im_end|>`）来指示说话者。

如果我们将此模板应用于前面的指令示例，得到的结果如下：

```py
<|im_start|>system
You are a helpful assistant, who always provide explanation. Think like you are answering to a five year old.<|im_end|>
<|im_start|>user
Remove the spaces from the following sentence: It prevents users to suspect that there are some hidden products installed on theirs device.
<|im_end|>
<|im_start|>assistant
Itpreventsuserstosuspectthattherearesomehiddenproductsinstalledontheirsdevice.<|im_end|>
```

在以下代码块中，我们通过`mapping`参数解析ShareGPT数据集，并包括ChatML模板。然后，我们加载并处理整个数据集，将聊天模板应用于每一对话。

```py
tokenizer = get_chat_template(
    tokenizer,
    mapping={"role": "from", "content": "value", "user": "human", "assistant": "gpt"},
    chat_template="chatml",
)

def apply_template(examples):
    messages = examples["conversations"]
    text = [tokenizer.apply_chat_template(message, tokenize=False, add_generation_prompt=False) for message in messages]
    return {"text": text}

dataset = load_dataset("mlabonne/FineTome-100k", split="train")
dataset = dataset.map(apply_template, batched=True)
```

我们现在准备好为我们的训练运行指定训练参数。我想简要介绍一下最重要的超参数：

+   **学习率**：它控制模型更新参数的强度。过低会导致训练缓慢并可能陷入局部最小值。过高则可能使训练变得不稳定或发散，进而降低性能。

+   **学习率调度器（LR scheduler）**：它在训练过程中调整学习率（LR），初期使用较高的学习率以加快进度，随后在训练后期逐渐降低学习率。线性调度器和余弦调度器是最常用的两种选择。

+   **批量大小**：在更新权重之前处理的样本数量。较大的批量大小通常会导致更稳定的梯度估计，并且可以提高训练速度，但它们也需要更多的内存。梯度累积通过在更新模型之前对多个前向/反向传递的梯度进行累积，实现在效果上使用更大的批量大小。

+   **训练轮数**：完成对训练数据集的所有遍历次数。更多的训练轮数使得模型能够多次看到数据，可能会提高性能。然而，过多的训练轮数可能会导致过拟合。

+   **优化器**：用来调整模型参数以最小化损失函数的算法。在实践中，强烈推荐使用AdamW 8位版本：它的表现与32位版本相当，但使用更少的GPU内存。AdamW的分页版本仅在分布式设置中才有意义。

+   **权重衰减**：一种正则化技术，通过在损失函数中添加对大权重的惩罚来防止过拟合，鼓励模型学习更简单、更具可泛化性的特征。然而，过强的权重衰减可能会阻碍学习。

+   **预热步骤**：训练开始时的一段时间，学习率从一个较小的值逐渐增加到初始学习率。预热可以帮助稳定早期训练，特别是在使用较大学习率或批量大小时，通过让模型在进行大幅更新之前适应数据分布。

+   **打包**：批次有一个预定义的序列长度。我们可以将多个小样本合并成一个批次，而不是为每个样本分配一个批次，从而提高效率。

我在整个数据集（100k个样本）上使用Google Colab上的A100 GPU（40 GB VRAM）进行了模型训练。训练用了4小时45分钟。当然，你可以使用具有更少VRAM和较小批量大小的较小GPU，但它们的速度远不如A100。例如，在L4上大约需要19小时40分钟，在免费的T4上需要整整47小时。

在这种情况下，我建议只加载数据集的一个子集来加快训练。你可以通过修改之前的代码块来实现，例如将`dataset = load_dataset("mlabonne/FineTome-100k", split="train[:10000]")`修改为仅加载10k个样本。或者，你可以使用像Paperspace、RunPod或Lambda Labs这样的更便宜的云GPU提供商。

```py
trainer=SFTTrainer(
    model=model,
    tokenizer=tokenizer,
    train_dataset=dataset,
    dataset_text_field="text",
    max_seq_length=max_seq_length,
    dataset_num_proc=2,
    packing=True,
    args=TrainingArguments(
        learning_rate=3e-4,
        lr_scheduler_type="linear",
        per_device_train_batch_size=4,
        gradient_accumulation_steps=4,
        num_train_epochs=1,
        fp16=not is_bfloat16_supported(),
        bf16=is_bfloat16_supported(),
        logging_steps=1,
        optim="adamw_8bit",
        weight_decay=0.01,
        warmup_steps=10,
        output_dir="output",
        seed=0,
    ),
)

trainer.train()
```

现在模型已经训练完成，让我们用一个简单的提示来测试它。这不是严格的评估，而只是一个快速检查，用来发现潜在的问题。我们使用`FastLanguageModel.for_inference()`来实现2倍更快的推理速度。

```py
model = FastLanguageModel.for_inference(model)

messages = [
    {"from": "human", "value": "Is 9.11 larger than 9.9?"},
]
inputs = tokenizer.apply_chat_template(
    messages,
    tokenize=True,
    add_generation_prompt=True,
    return_tensors="pt",
).to("cuda")

text_streamer = TextStreamer(tokenizer)
_ = model.generate(input_ids=inputs, streamer=text_streamer, max_new_tokens=128, use_cache=True)
```

模型的响应是“9.9”，这是正确的！

现在让我们保存训练好的模型。如果你还记得LoRA和QLoRA的部分，我们训练的不是模型本身，而是一组适配器。Unsloth中有三种保存方法：`lora`仅保存适配器，`merged_16bit`/`merged_4bit`则是将适配器与模型合并为16位/4位精度。

在接下来的步骤中，我们将它们合并为16位精度，以最大化质量。我们首先将其保存在“model”目录下，然后上传到 Hugging Face Hub。你可以在[mlabonne/FineLlama-3.1–8B](https://huggingface.co/mlabonne/FineLlama-3.1-8B)上找到训练好的模型。

```py
model.save_pretrained_merged("model", tokenizer, save_method="merged_16bit")
model.push_to_hub_merged("mlabonne/FineLlama-3.1-8B", tokenizer, save_method="merged_16bit")
```

Unsloth还允许你直接将模型转换为GGUF格式。这是一个为llama.cpp创建的量化格式，并与大多数推理引擎兼容，如[LM Studio](https://lmstudio.ai/)、[Ollama](https://ollama.com/)和oobabooga的[text-generation-webui](https://github.com/oobabooga/text-generation-webui)。由于你可以指定不同的精度（请参阅[我的关于GGUF和llama.cpp的文章](https://mlabonne.github.io/blog/posts/Quantize_Llama_2_models_using_ggml.html)），我们将遍历一个列表，使用`q2_k`、`q3_k_m`、`q4_k_m`、`q5_k_m`、`q6_k`、`q8_0`进行量化，并将这些量化文件上传至Hugging Face。 [mlabonne/FineLlama-3.1-8B-GGUF](https://huggingface.co/mlabonne/FineLlama-3.1-8B-GGUF)包含了我们所有的GGUF文件。

```py
quant_methods = ["q2_k", "q3_k_m", "q4_k_m", "q5_k_m", "q6_k", "q8_0"]
for quant in quant_methods:
    model.push_to_hub_gguf("mlabonne/FineLlama-3.1-8B-GGUF", tokenizer, quant)
```

恭喜你，我们从零开始微调了一个模型，并上传了你现在可以在你最喜欢的推理引擎中使用的量化文件。随时可以尝试在[mlabonne/FineLlama-3.1–8B-GGUF](https://huggingface.co/mlabonne/FineLlama-3.1-8B-GGUF)上使用最终模型。接下来该做什么呢？以下是一些使用你模型的建议：

+   **评估**它在[Open LLM排行榜](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard)上（你可以免费提交）或者使用其他评估工具，如[LLM AutoEval](https://github.com/mlabonne/llm-autoeval)。

+   **对齐**它并使用直接偏好优化（Direct Preference Optimization）与偏好数据集，如[mlabonne/orpo-dpo-mix-40k](https://huggingface.co/datasets/mlabonne/orpo-dpo-mix-40k)来提升性能。

+   **量化**它为其他格式，如EXL2、AWQ、GPTQ或HQQ，以便更快的推理或降低精度，使用[AutoQuant](https://colab.research.google.com/drive/1b6nqC7UZVt8bx4MksX7s656GXPM-eWw4?usp=sharing)。

+   **部署**它到Hugging Face Space，并使用[ZeroChat](https://colab.research.google.com/drive/1LcVUW5wsJTO2NGmozjji5CkC--646LgC)进行聊天模板的训练（约20k样本的训练）。

# 结论

本文提供了一个关于监督微调的全面概述，并说明了如何在实践中应用到Llama 3.1 8B模型。通过利用QLoRA的高效内存使用，我们成功地在有限的GPU资源下对8B大模型进行了微调，并使用了一个高质量的数据集。我们还提供了更高效的替代方案，以应对更大的运行任务，并提供了进一步步骤的建议，包括评估、偏好对齐、量化和部署。

希望这篇指南对你有所帮助。如果你有兴趣了解更多关于LLM的内容，我推荐你查看[LLM课程](https://github.com/mlabonne/llm-course)。如果你喜欢这篇文章，欢迎在X平台上关注我[@maximelabonne](https://x.com/maximelabonne)和在Hugging Face上关注[@mlabonne](https://huggingface.co/mlabonne)。祝你微调模型顺利！
