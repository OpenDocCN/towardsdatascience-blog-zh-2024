# 微调 Llama 3 与 ORPO

> 原文：[`towardsdatascience.com/fine-tune-llama-3-with-orpo-56cfab2f9ada?source=collection_archive---------3-----------------------#2024-04-19`](https://towardsdatascience.com/fine-tune-llama-3-with-orpo-56cfab2f9ada?source=collection_archive---------3-----------------------#2024-04-19)

## *一种更便宜、更快速的统一微调技术*

[](https://medium.com/@mlabonne?source=post_page---byline--56cfab2f9ada--------------------------------)![Maxime Labonne](https://medium.com/@mlabonne?source=post_page---byline--56cfab2f9ada--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--56cfab2f9ada--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56cfab2f9ada--------------------------------) [Maxime Labonne](https://medium.com/@mlabonne?source=post_page---byline--56cfab2f9ada--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56cfab2f9ada--------------------------------) ·阅读时长 8 分钟 ·2024 年 4 月 19 日

--

![](img/0024fbce8d1d7e5a96348655f9f40d35.png)

图片由 DALL-E 3 生成，由作者提供

ORPO 是一种**新兴的令人兴奋的微调技术**，它将传统的监督微调和偏好对齐阶段合并为一个单一过程。这减少了训练所需的计算资源和时间。此外，实证结果表明，ORPO 在多种模型规模和基准测试中超越了其他对齐方法。

在本文中，我们将使用 ORPO 和 TRL 库微调新的 Llama 3 8B 模型。代码可以在 [Google Colab](https://colab.research.google.com/drive/1eHNWg9gnaXErdAa8_mcvjMupbSS6rDvi?usp=sharing) 和 GitHub 上的 [LLM 课程](https://github.com/mlabonne/llm-course) 中找到。

# ⚖️ ORPO

指令微调和偏好对齐是将大型语言模型（LLMs）适应特定任务的关键技术。传统上，这涉及到一个多阶段的过程：1/ **监督微调**（SFT），以使模型适应目标领域，然后是 2/ **偏好对齐方法**，如人类反馈强化学习（RLHF）或直接偏好优化（DPO），以提高生成首选响应而非被拒绝响应的概率。

![](img/29cac2fc31790a8a74230a95bd7e7b4b.png)

图片由作者提供

然而，研究人员发现这种方法存在局限性。虽然 SFT 可以有效地将模型适应所需领域，但它无意中**增加了生成不良答案的概率**，而这些答案与首选答案一起出现。这就是为什么偏好对齐阶段是必要的，它能拉大首选输出和被拒绝输出之间的概率差距。

![](img/d27c0588ee55984b02754a279bb715f4.png)

注意，经过监督微调后，被拒绝的响应概率是如何增加的（图片来自 ORPO 论文）。

由[Hong and Lee (2024)](https://arxiv.org/abs/2403.07691)提出，ORPO 通过将指令微调和偏好对齐结合到一个单一的训练过程中，提供了一个优雅的解决方案。ORPO 修改了标准的语言建模目标，将负对数似然损失与赔率比（OR）项结合起来。这个 OR 损失在惩罚被拒绝的响应时相对较弱，而在奖励偏好的响应时则较强，从而使模型能够同时学习目标任务并与人类偏好对齐。

![](img/246e9df3fef5d97400c82a00263c7f5d.png)

ORPO 已经在主要的微调库中实现，例如[TRL](https://github.com/huggingface/trl)、[Axolotl](https://github.com/OpenAccess-AI-Collective/axolotl)和[LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)。在下一部分中，我们将看到如何与 TRL 一起使用。

# 💻 使用 ORPO 微调 Llama 3

[Llama 3](https://github.com/meta-llama/llama3/tree/main)是 Meta 开发的最新 LLM 系列。这些模型在一个庞大的数据集上进行了训练，共**15 万亿个标记**（相比之下，Llama 2 为 2 万亿标记）。已经发布了两种模型大小：一个 70 亿参数模型和一个较小的 80 亿参数模型。70B 模型已经展示了出色的性能，在 MMLU 基准测试中得分为 82，在 HumanEval 基准测试中得分为 81.7。

Llama 3 模型还将上下文长度增加到 8,192 个标记（Llama 2 为 4,096 个标记），并可能通过 RoPE 扩展到 32k。此外，模型使用了一个新的分词器，具有 128K 标记的词汇表，将编码文本所需的标记数减少了 15%。这个词汇表也解释了从 7B 到 8B 参数的提升。

![](img/1fb24c5aea241733ae340216214e0bb8.png)

*来自 ORPO-DPO-mix-40k 的样本（图片由作者提供）。*

ORPO 需要一个偏好数据集，包括一个提示语、一个选择的答案和一个被拒绝的答案。在这个示例中，我们将使用`[mlabonne/orpo-dpo-mix-40k](https://huggingface.co/datasets/mlabonne/orpo-dpo-mix-40k)`，它是以下高质量 DPO 数据集的组合：

+   `[argilla/distilabel-capybara-dpo-7k-binarized](https://huggingface.co/datasets/argilla/distilabel-capybara-dpo-7k-binarized)`：得分较高的选择答案>=5（2,882 个样本）

+   `[argilla/distilabel-intel-orca-dpo-pairs](https://huggingface.co/datasets/argilla/distilabel-intel-orca-dpo-pairs)`：得分较高的选择答案>=9，但不在 GSM8K 中（2,299 个样本）

+   `[argilla/ultrafeedback-binarized-preferences-cleaned](https://huggingface.co/datasets/argilla/ultrafeedback-binarized-preferences-cleaned)`：得分较高的选择答案>=5（22,799 个样本）

+   `[argilla/distilabel-math-preference-dpo](https://huggingface.co/datasets/argilla/distilabel-math-preference-dpo)`：得分较高的选择答案>=9（2,181 个样本）

+   `[unalignment/toxic-dpo-v0.2](https://huggingface.co/datasets/unalignment/toxic-dpo-v0.2)`（541 个样本）

+   `[M4-ai/prm_dpo_pairs_cleaned](https://huggingface.co/datasets/M4-ai/prm_dpo_pairs_cleaned)`（7,958 个样本）

+   `[jondurbin/truthy-dpo-v0.1](https://huggingface.co/datasets/jondurbin/truthy-dpo-v0.1)`（1,016 个样本）

感谢[argilla](https://huggingface.co/argilla)、[unalignment](https://huggingface.co/unalignment)、[M4-ai](https://huggingface.co/M4-ai)和[jondurbin](https://huggingface.co/jondurbin)提供源数据集。

如常，我们从安装所需的库开始：

```py
pip install -U transformers datasets accelerate peft trl bitsandbytes wandb
```

一旦安装完成，我们就可以导入必要的库并登录 W&B（可选）：

```py
import gc
import os

import torch
import wandb
from datasets import load_dataset
from google.colab import userdata
from peft import LoraConfig, PeftModel, prepare_model_for_kbit_training
from transformers import (
    AutoModelForCausalLM,
    AutoTokenizer,
    BitsAndBytesConfig,
    TrainingArguments,
    pipeline,
)
from trl import ORPOConfig, ORPOTrainer, setup_chat_format
wb_token = userdata.get('wandb')
wandb.login(key=wb_token)
```

如果你有一块较新的 GPU，你应该也能够使用[Flash Attention 库](https://github.com/Dao-AILab/flash-attention)，将默认的急切注意力实现替换为更高效的实现。

```py
if torch.cuda.get_device_capability()[0] >= 8:
    !pip install -qqq flash-attn
    attn_implementation = "flash_attention_2"
    torch_dtype = torch.bfloat16
else:
    attn_implementation = "eager"
    torch_dtype = torch.float16
```

在接下来的步骤中，我们将通过[bitsandbytes](https://github.com/TimDettmers/bitsandbytes)以 4 位精度加载 Llama 3 8B 模型。然后，我们使用[PEFT](https://github.com/huggingface/peft)为 QLoRA 设置 LoRA 配置。我还使用了方便的`setup_chat_format()`函数，来修改模型和分词器以支持[ChatML](https://huggingface.co/docs/transformers/en/chat_templating#what-template-should-i-use)。它会自动应用这个聊天模板，添加特殊标记，并调整模型的嵌入层大小以匹配新的词汇表大小。

请注意，你需要提交请求以访问[meta-llama/Meta-Llama-3-8B](https://huggingface.co/meta-llama/Meta-Llama-3-8B)，并且登录你的 Hugging Face 账户。或者，你可以加载没有权限限制的模型副本，如[NousResearch/Meta-Llama-3-8B](https://huggingface.co/NousResearch/Meta-Llama-3-8B)。

```py
# Model
base_model = "meta-llama/Meta-Llama-3-8B"
new_model = "OrpoLlama-3-8B"

# QLoRA config
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch_dtype,
    bnb_4bit_use_double_quant=True,
)

# LoRA config
peft_config = LoraConfig(
    r=16,
    lora_alpha=32,
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM",
    target_modules=['up_proj', 'down_proj', 'gate_proj', 'k_proj', 'q_proj', 'v_proj', 'o_proj']
)

# Load tokenizer
tokenizer = AutoTokenizer.from_pretrained(base_model)

# Load model
model = AutoModelForCausalLM.from_pretrained(
    base_model,
    quantization_config=bnb_config,
    device_map="auto",
    attn_implementation=attn_implementation
)
model, tokenizer = setup_chat_format(model, tokenizer)
model = prepare_model_for_kbit_training(model)
```

现在模型已经准备好进行训练，我们可以开始处理数据集。我们加载`[mlabonne/orpo-dpo-mix-40k](https://huggingface.co/datasets/mlabonne/orpo-dpo-mix-40k)`，并使用`apply_chat_template()`函数将“选择的”和“拒绝的”列转换为 ChatML 格式。请注意，我只使用了 1,000 个样本，而不是整个数据集，因为运行整个数据集需要的时间太长。

```py
dataset_name = "mlabonne/orpo-dpo-mix-40k"
dataset = load_dataset(dataset_name, split="all")
dataset = dataset.shuffle(seed=42).select(range(1000))

def format_chat_template(row):
    row["chosen"] = tokenizer.apply_chat_template(row["chosen"], tokenize=False)
    row["rejected"] = tokenizer.apply_chat_template(row["rejected"], tokenize=False)
    return row

dataset = dataset.map(
    format_chat_template,
    num_proc= os.cpu_count(),
)
dataset = dataset.train_test_split(test_size=0.01)
```

首先，我们需要设置一些超参数：

+   `learning_rate`：ORPO 使用的学习率比传统的 SFT 或 DPO 要低得多。这个 8e-6 的值来自原论文，粗略对应 SFT 学习率的 1e-5 和 DPO 学习率的 5e-6。我建议将其增加到 1e-6 左右，以进行真正的微调。

+   `beta`：它是论文中的$\lambda$参数，默认值为 0.1。原论文的附录展示了如何通过消融实验来选择这个值。

+   其他参数，如`max_length`和批处理大小，设置为尽可能使用所有的 VRAM（在此配置下约为 20GB）。理想情况下，我们会将模型训练 3-5 个周期，但在这里我们将只训练 1 个周期。

最后，我们可以使用 ORPOTrainer 进行模型训练，它充当了一个封装器。

```py
orpo_args = ORPOConfig(
    learning_rate=8e-6,
    beta=0.1,
    lr_scheduler_type="linear",
    max_length=1024,
    max_prompt_length=512,
    per_device_train_batch_size=2,
    per_device_eval_batch_size=2,
    gradient_accumulation_steps=4,
    optim="paged_adamw_8bit",
    num_train_epochs=1,
    evaluation_strategy="steps",
    eval_steps=0.2,
    logging_steps=1,
    warmup_steps=10,
    report_to="wandb",
    output_dir="./results/",
)

trainer = ORPOTrainer(
    model=model,
    args=orpo_args,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"],
    peft_config=peft_config,
    tokenizer=tokenizer,
)

trainer.train()
trainer.save_model(new_model)
```

在这些 1,000 个样本上训练模型花费了大约 2 小时，使用的是 L4 GPU。让我们检查一下 W&B 的图表：

![](img/4ea31a21aa2d957cab4982835b4be4e9.png)

虽然损失下降，但选择答案和拒绝答案之间的差异并不明显：平均边际和准确率分别仅略高于零和 0.5。

在原始论文中，作者在`[Anthropic/hh-rlhf](https://huggingface.co/datasets/Anthropic/hh-rlhf)`数据集（161k 样本）上训练模型，并进行了 10 个 epochs 的训练，训练时间远长于我们的快速运行。他们还对 Llama 3 进行了实验，并友好地[分享了他们的日志](https://huggingface.co/orpo-explorers/hf-llama3-8b-orpo-v0.0/tensorboard)给我（感谢[Jiwoo Hong](https://twitter.com/jiwoohong98)）。

在本教程的结尾，让我们将 QLoRA 适配器与基础模型合并，并将其推送到 Hugging Face Hub。

```py
# Flush memory
del trainer, model
gc.collect()
torch.cuda.empty_cache()

# Reload tokenizer and model
tokenizer = AutoTokenizer.from_pretrained(base_model)
model = AutoModelForCausalLM.from_pretrained(
    base_model,
    low_cpu_mem_usage=True,
    return_dict=True,
    torch_dtype=torch.float16,
    device_map="auto",
)
model, tokenizer = setup_chat_format(model, tokenizer)

# Merge adapter with base model
model = PeftModel.from_pretrained(model, new_model)
model = model.merge_and_unload()
model.push_to_hub(new_model, use_temp_dir=False)
tokenizer.push_to_hub(new_model, use_temp_dir=False)
```

恭喜，我们完成了 Llama 3 的快速微调：[mlabonne/OrpoLlama-3–8B](https://huggingface.co/mlabonne/OrpoLlama-3-8B)。你可以通过这个[Hugging Face Space](https://huggingface.co/spaces/mlabonne/OrpoLlama-3-8B)来玩这个模型（这里有一个[notebook](https://colab.research.google.com/drive/1LcVUW5wsJTO2NGmozjji5CkC--646LgC?usp=sharing)来创建你自己的模型）。尽管如 W&B 曲线所示，模型还未经过充分训练，我在 Nous 的基准套件上使用[LLM AutoEval](https://github.com/mlabonne/llm-autoeval)进行了一些评估。

![](img/127c1583eb4512d807a080e6d4bc4223.png)

我们的 ORPO 微调实际上相当不错，且在每个基准测试中都提升了基础模型的表现。这是令人鼓舞的，且很可能意味着对整个 40k 样本进行微调将取得很好的结果。

这是开源社区的激动人心时刻，越来越多高质量的开源权重模型被发布。闭源模型和开源权重模型之间的差距正在慢慢缩小，微调是获取最佳性能的必备工具。

![](img/885096bd13d294fa53f7589664afdd0b.png)

图片来源：作者

# 结论

在本文中，我们介绍了 ORPO 算法，并解释了它如何将 SFT 和偏好对齐阶段统一为一个过程。然后，我们使用 TRL 对 Llama 3 8B 模型进行微调，基于自定义的偏好数据集。最终的模型显示出令人鼓舞的结果，突出了 ORPO 作为一种新的微调范式的潜力。

希望这对你有帮助，我建议你运行[Colab notebook](https://colab.research.google.com/drive/1eHNWg9gnaXErdAa8_mcvjMupbSS6rDvi?usp=sharing)来微调你自己的 Llama 3 模型。在未来的文章中，我们将讨论如何创建高质量的数据集——这是一个经常被忽视的重点。如果你喜欢这篇文章，请在[Hugging Face](https://huggingface.co/mlabonne/)和 Twitter 上关注我[@maximelabonne](https://twitter.com/maximelabonne)。

# 参考文献

+   J. Hong, N. Lee 和 J. Thorne，[ORPO: 无需参考模型的单体偏好优化](https://arxiv.org/abs/2403.07691)。2024 年。

+   L. von Werra 等人, TRL: Transformer 强化学习. GitHub, 2020\. [在线]. 可用链接: [`github.com/huggingface/trl`](https://github.com/huggingface/trl)

+   Bartolome, A., Martin, G., & Vila, D. (2023). Notus. 在 GitHub 仓库中。GitHub. [`github.com/argilla-io/notus`](https://github.com/argilla-io/notus)

+   Meta 的人工智能，[介绍 Meta Llama 3](https://ai.meta.com/blog/meta-llama-3/)，2024 年。
