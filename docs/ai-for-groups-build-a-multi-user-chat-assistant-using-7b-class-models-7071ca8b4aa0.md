# AI for Groups: 使用 7B 类模型构建多用户聊天助手

> 原文：[https://towardsdatascience.com/ai-for-groups-build-a-multi-user-chat-assistant-using-7b-class-models-7071ca8b4aa0?source=collection_archive---------6-----------------------#2024-01-22](https://towardsdatascience.com/ai-for-groups-build-a-multi-user-chat-assistant-using-7b-class-models-7071ca8b4aa0?source=collection_archive---------6-----------------------#2024-01-22)

## 你是否曾想过构建一个能够知道何时说话、何时保持沉默的助手？了解如何使用开源模型实现这一目标。

[](https://medium.com/@jjezabek?source=post_page---byline--7071ca8b4aa0--------------------------------)[![Jan Jezabek, Ph.D.](../Images/eb78f321cc347a9e619b2c474e7bc192.png)](https://medium.com/@jjezabek?source=post_page---byline--7071ca8b4aa0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7071ca8b4aa0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7071ca8b4aa0--------------------------------) [Jan Jezabek, Ph.D.](https://medium.com/@jjezabek?source=post_page---byline--7071ca8b4aa0--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7071ca8b4aa0--------------------------------) ·阅读时长 15 分钟 ·2024年1月22日

--

智能聊天助手已成为最近生成式 AI 进展带来的核心应用，ChatGPT 和 Bing Chat/Copilot 已成为家喻户晓的名字。通常，这种形式是用户提供提示或指令，而助手则根据这些信息进行回应。

一个相对较少关注的场景是，助手作为两个或多个用户之间对话的半主动参与者。这类互动的例子包括一群朋友一起计划活动时的对话——在适当的时候助手提供建议，其他时候保持沉默——或者是客服聊天，助手向客服代表提供建议。在这些情况下，助手并不需要每次都回应：如果它在朋友们的随意聊天中频繁插话，那会显得很尴尬。

![](../Images/2ae3c2938854108e952881ce1da19d1a.png)

（图片来源：DALL-E 3，作者后期处理去除多余的手指）

在这个系列中，我将介绍使用开源 LLM（大型语言模型）构建轻量级助手所需的步骤。在这个语境中，“轻量级”指的是训练和推理分别需要 16GB 和 8GB GPU 内存的模型，并且如果需要，它可以高效地在 CPU 上运行。为此，我将使用 Llama-2-7b-hf-chat、Zephyr-7b-beta 和 OpenChat-3.5-0106，这些模型都符合这个描述。

# ChatGPT-3.5-Turbo 基准

为了对任务有一个感觉，我们将首先使用 ChatGPT 实现它。这将为我们提供一个来自强大模型的参考点，并帮助我们估算任务的难度。

让我们思考一下我们用例中的一些独特之处：

+   我们不希望助手过于热心：它应该只有在被直接问及或有一些有趣的琐事可以补充时才插话。为此，助手需要能够保持沉默的选项。

+   对话中有多个用户。为了理清楚这一点，我们需要为每条聊天消息标明是哪个用户在发言。

对于第一个方面，我们需要定义助手选择保持沉默的机制。为此，我们将指示模型返回“(silence)”作为响应。这样的预测可以在后处理时进行过滤。另一种方法是要求模型返回空的预测，但根据经验，这在某些模型中似乎不太可靠（它们不习惯保持沉默！）。

对于第二个方面，OpenAI 的 API 方便地允许我们为对话中的每条消息提供参与者的姓名（有趣的是，这个功能在[Playground](https://platform.openai.com/playground)中并没有开放）。不幸的是，这对于常见的开源模型并不适用（我们需要一种解决方法），但对于 ChatGPT 来说，我们应该没问题。

这还剩下一个至关重要的决策：提示。对于我们的用例，我故意选择了一个简短而精确的提示（如果响应的语气有偏差，随时可以调整）：

```py
You are an assistant in a group conversation between multiple users.
Your task is to help with relevant information or when directly asked.
Do not be overzealous. If you do not have anything important to say,
respond with "(silence)".
```

现在我们已经拥有了所需的一切，让我们试试看。使用在[这个笔记本](https://colab.research.google.com/drive/18Lsn3ws5H3jCEJ-loLmuL4_195BOEEoa?usp=drive_link)中实现的聊天循环，我们得到了以下对话：

初步结果令人鼓舞，尽管不完美：助手偶尔选择保持沉默（遵循指令中的格式）或提供有用的信息，但有时也会回应一些不必要的闲聊。将提示修改为：

```py
You are an assistant in a group conversation between multiple users.
Your task is to help with relevant information or when you are directly
addressed as "assistant". Do not be overzealous, remember that most of
the time the users will be speaking to each other, not to you. If you
do not have anything important to say, respond with "(silence)".
```

并在每条用户消息后插入这个提醒系统消息：

```py
Remember that the users are most likely to be speaking to each other,
not to you. If you do not have anything important to say, respond with
"(silence)".
```

这似乎没有太大区别，正如在以下对话中所看到的：

模型的表现很可能通过对提示进行更多调整而显著改善，但现在这对于我们的目的已经足够：我们有了一个基准可以进行对比，同时也得到了一个表明问题是可解决的迹象，尽管不是简单的。

## 开源模型和微调

我们已经看到，尽管有一些小问题，ChatGPT-3.5-Turbo 能够充当群聊中的半主动参与者。不幸的是，对于常见的开源模型，尤其是7B 参数级别的模型，情况并非如此，它们在每个回合都回应。幸运的是，开源大型语言模型的一个优点是，我们可以通过微调将它们适配到我们的任务中。

值得指出的是，微调并不适用于每种情况。例如，如果你想教给模型新的事实，微调就不是合适的工具（一个更好的方法是检索增强生成）。然而，如果你想改变响应的语气或格式（正如我们在这里所做的），微调正是你需要的工具。

## 数据集生成

微调中一个关键的决定因素是数据集。我们需要提供一组良好的多用户对话示例，在这些对话中，助手大多数时间保持沉默，但偶尔会插入有帮助的信息。为了快速构建这样的数据集，我求助于Mixtral-8x7B-Instruct-v0.1，它托管在replicate.com上。具体来说，我使用以下提示生成了50个合成对话（并对讨论的主题和参与者名字进行了一些变化，详情见[这个笔记本](https://colab.research.google.com/drive/1JWAK3ecaO4EjUqIk1LBIfGQ8zV4oWmDk?usp=drive_link)）：

```py
Generate a conversation representing a chat between two users.
The users are Cynthia and Fred and they are discussing potential
Christmas gifts for friends. An assistant chimes in when it can fill
in trivia, otherwise it remains silent. The conversation should have
between 10 and 12 turns. Return the conversation in a JSON format,
like this:

[
  {
    "role": "user",
    "name": "Alice",
    "content": "Hi Grace! How are you?"
  },
  {
    "role": "user",
    "name": "Grace",
    "content": "I'm good, how about you?"
  },
  {
    "role": "user",
    "name": "Alice",
    "content": "Doing fine as well. I've been reading a book by the author of the Da Vinci code. Sorry, forgot his name"
  },
  {
    "role": "assistant",
    "content": "That’s Dan Brown! He also authored a few other books, for example \"Angels & Demons\" and \"Inferno\"."
  }
]
```

显然，结果并不是一个高质量的精心策划的数据集，因此不建议将其用于生产模型。我将在后续文章中讨论一些改进数据集质量的方法，以及评估最终模型的方式。然而，目前该数据集已经足够满足我们的需求，即验证一个小型模型能否适应作为多用户聊天助手的任务。

数据集生成笔记本可以在此找到，生成的数据集已上传到[这个HuggingFace库](https://huggingface.co/jjezabek/multi_user_chat_synthetic)。以下是一个生成的对话示例：

## 关于聊天模板的说明

在使用预训练的聊天模型时，确保输入的格式与模型训练时的格式相匹配是个好主意。2023年9月，HuggingFace引入了*apply_chat_template*方法，这使得这个任务变得更加容易。该方法负责将各种用户、系统和助手的提示和响应格式化成模型所期望的格式。

不幸的是，并非所有模型都已更新以具备聊天模板，因此我建议检查每个模型的*apply_chat_template*输出，并将其与模型的文档进行对比。

在微调的上下文中（与仅使用现成模型进行推理不同），我们不必严格遵循规定的格式。事实上，对于非聊天模型，定义自己的聊天模板是必须的。然而，对于聊天模型，保持现有聊天模板通常能让微调任务变得更容易，减少训练步骤，并且减少出现不良副作用的可能性（比如灾难性遗忘）。

对于我们选择的模型，Zephyr、Llama-7b-chat和OpenChat-3.5，我们很幸运：它们都有正确的聊天模板，并且*apply_chat_template*按预期工作。

# 微调

我们现在准备好开始微调了。如前所述，目标是将训练适配到16GB的GPU内存中，允许它在单个T4 GPU上运行（无需去寻找稀有的宝可梦…呃，我是说A100）。为了实现这一点，我们将使用4位量化和LoRA。如果你不熟悉这些术语，我强烈推荐[这篇文章](https://www.mercity.ai/blog-post/guide-to-fine-tuning-llms-with-lora-and-qlora)作为入门介绍。本节将介绍微调所需的主要步骤，完整的训练notebook可以通过[这里](https://colab.research.google.com/drive/1MDXvw8ie3XnpR0OujFhwLgTU0XIeepSv?usp=drive_link)访问。

在开始训练之前，我们需要稍微调整一下之前创建的合成数据集：

+   我们需要为每个用户回合添加说话者信息。还记得OpenAI API中有用的*name*字段，它帮助我们区分不同的人工说话者吗？遗憾的是，Zephyr、Llama和OpenChat的聊天模板中没有此字段。作为一种解决方法，我们将只是在每一行的开头加上“{name}: ”。

+   我们还需要在助手每次选择不回应时添加“(silence)”的助手行。此外，我们还将每个助手行前添加“(response)”。对于基本的对话情况，这不是绝对必要的，但它将促使模型回答，即使它本来想保持沉默（在评估过程中这会很有用，也可以作为产品功能）。

+   最后，我们还需要应用聊天模板。

数据集预处理实现如下：

```py
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained(HF_BASE_MODEL_NAME, use_fast=False)
```

```py
from datasets import Dataset
from huggingface_hub import hf_hub_download
import json

def build_dataset():
    local_filename = hf_hub_download(
        repo_id=HF_DATASET_NAME,
        filename=HF_DATA_FILE_NAME
    )
    with open(local_filename) as f:
        conversations = f.readlines()
        result = []
        for conversation in conversations:
            lines = json.loads(conversation)
            transformed_lines = []

            idx = 0
            while idx < len(lines):
                assert lines[idx]['role'] == 'user'
                transformed_lines.append({
                    'role': 'user',
                    'content': f"{lines[idx]['name']}: {lines[idx]['content']}",
                })

                idx += 1

                if idx == len(lines) or lines[idx]['role'] != 'assistant':
                    # Insert artificial (silence) response
                    transformed_lines.append({
                        'role': 'assistant',
                        'content': '(silence)',
                    })
                else:
                    transformed_lines.append({
                        'role': 'assistant',
                        'content': f"(response) {lines[idx]['content']}",
                    })
                    idx += 1

            result_row = {
                'text': tokenizer.apply_chat_template(tokenize=False, conversation=transformed_lines)
            }
            result.append(result_row)

    return result

dataset = Dataset.from_list(build_dataset())
```

请注意，没有包含系统提示。原因是我们正在为这个特定任务微调模型，因此向模型提供指令是多余的：它通过训练学会了该做什么。这样做有一个很好的副作用——训练时间较短，推理也稍微更快。

在完成数据集的准备工作后，我们现在加载量化模型：

```py
import torch
from transformers import AutoModelForCausalLM

torch_compute_type = torch.bfloat16 if USE_BFLOAT16 else torch.float16

model = AutoModelForCausalLM.from_pretrained(
    active_config['base_model_name'],
    torch_dtype=torch_compute_type,
    bnb_4bit_quant_type='nf4',
    bnb_4bit_compute_dtype=torch_compute_type,
    load_in_4bit=True,
    device_map={'':0},
    trust_remote_code=True,
    use_cache=True
)
```

接下来，我们定义适配器模型（即来自基础模型的低秩“差异”）：

```py
from peft import LoraConfig, get_peft_model

peft_config = LoraConfig(
        lora_alpha=16,
        lora_dropout=0.1,
        r=64,
        bias="none",
        task_type="CAUSAL_LM",
)

# Note: This is needed for Zephyr, otherwise we get this:
#       RuntimeError: element 0 of tensors does not require grad and does not have a grad_fn
model.enable_input_require_grads()
peft_model = get_peft_model(model, peft_config)
```

并实例化训练器和训练参数：

```py
from transformers import TrainingArguments

output_dir = "peft_model"

# These arguments (LR, gradient norm, etc.) seem to be fairly frequently
# used for QLoRA. Default arguments work too, but require about 50% more
# epochs. Also tried optim='lion_32bit' out of curiosity, the result was
# pretty much the same as the default (AdamW), but each epoch was 30-40%
# slower.
training_args = TrainingArguments(
    output_dir=output_dir,
    num_train_epochs=TRAIN_EPOCHS,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=2,
    gradient_checkpointing=True,
    logging_steps=1,
    bf16=USE_BFLOAT16,
    #optim='lion_32bit',
    learning_rate=2e-4,
    max_grad_norm=0.3,
    warmup_ratio=0.03,
    lr_scheduler_type="constant",
)
```

上述设置相对标准（我鼓励你根据需要调整它们）。真正重要的是epoch的数量、学习率和批量大小。上述配置是一个对我有效的特定设置，可能是一个不错的起点，但显然不能替代真正的超参数搜索。

我们现在准备好实例化训练器并启动训练：

```py
from trl import SFTTrainer

max_seq_length = 1024

trainer = SFTTrainer(
    model=peft_model,
    train_dataset=dataset,
    peft_config=peft_config,
    max_seq_length=max_seq_length,
    tokenizer=tokenizer,
    args=training_args,
    dataset_text_field='text',
)
```

```py
trainer.train()
```

那真是很快，仅用了8分钟在T4上！让我们通过创建一个对话管道和循环来测试它的表现，使用与OpenAI API案例相同的[notebook](https://colab.research.google.com/drive/18Lsn3ws5H3jCEJ-loLmuL4_195BOEEoa?usp=drive_link)。下面是使用从OpenChat-3.5–0106微调的模型进行的一个示例对话：

这真是令人鼓舞：模型遵循我们的格式要求，并且似乎在何时插入和何时保持沉默方面做出了合理的决定。

那么—我们完成了吗？关于训练，有一点需要注意的是，模型被教导预测每个样本中的所有标记，包括用户消息和任何特殊标记。接下来的部分将展示如何抑制这一点。

## 仅在完成任务时训练

首先要说的是：为什么我们要关心不让模型预测用户消息呢？有一个观点是基于隐私问题：如果真实对话被用作训练数据，模型可能会被最终用户说服，泄露一些用户消息（值得一提的是，助手的回答也可能包含敏感信息）。第二个理由是，尝试预测用户消息是没有必要的，因此也是浪费时间。这意味着你可能需要更长的时间来训练才能获得良好的结果，从而有可能带来不良的副作用（再说一次，这主要是灾难性遗忘）。

根据你的使用场景，这两个参数可能无关紧要，模型可能会在上述训练过程中表现得很好。不过，如果不是，或者你只是好奇，我鼓励你继续阅读。

HuggingFace的*trl*库为我们提供了一个工具来解决这个特定问题，它的实现是*DataCollatorForCompletionsOnlyLM*。这个合并器将代表用户消息的标记的标签更改为“忽略”标签，意味着模型不会被训练去预测它们。当然，用户消息仍然作为预测助手消息的上下文使用。

*DataCollatorForCompletionsOnlyLM*要求我们传递两个字符串，它可以用来找到用户消息的开始（*instruction_template*参数）和助手消息（*response_template*）。我们可以通过检查*apply_chat_template*的输出找到它们：在Zephyr的情况下，它们是“<|user|>”和“<|assistant|>”，在Llama的情况下，它们是“[INST]”和“[/INST]”。让我们试试看：

```py
trainer.data_collator = DataCollatorForCompletionOnlyLM(
    response_template="<|assistant|>",
    instruction_template="<|user|>",
    tokenizer=tokenizer
)

trainer.train()

### Output:
# UserWarning: Could not find response key `<|assistant|>` in the following instance: [...] This instance will be ignored in loss calculation. Note, if this happens often, consider increasing the `max_seq_length`.
```

哎呀，这看起来不太妙。基本上，训练器无法找到我们的模板片段，因此忽略了我们所有的样本。原因在于[这篇文章](https://huggingface.co/docs/trl/sft_trainer#using-tokenids-directly-for-responsetemplate)中有解释：根据前面的上下文，像“<|user|>”这样的字符串可能有不同的标记化表示形式。幸运的是，*DataCollatorForCompletionsOnlyLM*允许我们传递这些分隔符字符串的标记化版本，而不是字面上的字符串。为了找到这些标记化版本，我们可以检查聊天模板的标记化输出：

```py
conversation = [
  { 'role': 'user', 'content': "hi!" },
  { 'role': 'assistant', 'content': "Hello!" }
]

for token in tokenizer.apply_chat_template(conversation):
    print(f"Token Id: {token}, Value: '{tokenizer.decode([token])}'")

### Output
# Token Id: 523, Value: '<'
# Token Id: 28766, Value: '|'
# Token Id: 1838, Value: 'user'
# Token Id: 28766, Value: '|'
# Token Id: 28767, Value: '>'
# Token Id: 13, Value: '
# '
# Token Id: 5365, Value: 'hi'
# Token Id: 28808, Value: '!'
# Token Id: 2, Value: '</s>'
# Token Id: 28705, Value: ''
# Token Id: 13, Value: '
# '
# Token Id: 28789, Value: '<'
# Token Id: 28766, Value: '|'
# Token Id: 489, Value: 'ass'
# Token Id: 11143, Value: 'istant'
# Token Id: 28766, Value: '|'
# Token Id: 28767, Value: '>'
# Token Id: 13, Value: '
# '
# Token Id: 16230, Value: 'Hello'
# Token Id: 28808, Value: '!'
# Token Id: 2, Value: '</s>'
# Token Id: 28705, Value: ''
# Token Id: 13, Value: '
# '
```

从输出中我们可以推断出“<|assistant|>”被标记化为[28789, 28766, 489, 11143, 28766, 28767]，而“<|user|>”被标记化为[28789, 28766, 1838, 28766, 28767]。我已经在下面的表格中列出了几种常见模型的标记化序列。

有了这些信息，我们现在可以使用更新的数据收集器重新进行训练：

```py
response_template = [28789, 28766, 489, 11143, 28766, 28767]
instruction_template = [28789, 28766, 1838, 28766, 28767]

trainer.data_collator = DataCollatorForCompletionOnlyLM(
    response_template=response_template,
    instruction_template=instruction_template,
    tokenizer=tokenizer
)

trainer.train()
```

这消除了警告，并且训练损失开始下降。我们现在可以等待模型训练完成并将模型上传到HuggingFace Hub。

```py
peft_model.push_to_hub(active_config['finetuned_model_name'])
tokenizer.push_to_hub(active_config['finetuned_model_name'])
```

## 垃圾测试

现在让我们通过运行[这个笔记本](https://colab.research.google.com/drive/18Lsn3ws5H3jCEJ-loLmuL4_195BOEEoa?usp=drive_link)来看模型在实际中的表现（该笔记本可以在配备8GB显卡的消费级GPU上本地执行）。以下是一个示例对话，同样是从OpenChat-3.5–0106微调的模型：

那么——我们现在完成了吗？这取决于目标：我们确实有一个我喜欢称之为“语法上合格”的模型，这意味着它遵循我们定义的格式，并且能够决定何时说话，何时保持沉默。如果目标是一个玩具助手，这可能就足够了。然而，对于任何严肃的生产用途，仍然需要做相当多的工作，我将在后续的文章中讨论这些内容。

## 后续步骤

让我们列出一些值得考虑的后续步骤：

+   **高质量训练集：** 到目前为止，我们只使用了由Mixtral生成的合成训练集。这个数据集变化不大，可能包含虚假信息。它对于启动阶段很有用，但不足以用于生产用途。

+   **评估：** 到目前为止，我们只做了一些烟雾测试，但我们并不了解模型的表现如何：它是否诚实回答，是否能够判断何时插话？我们也不知道微调后的模型与基础模型有多少偏差。在后续的文章中，我将展示如何弄清楚这些问题。

+   **背景：** 我们不能期望一个只有7B参数的模型能够对每个话题都有所了解。事实上，出于实际用途的考虑，我们可能希望将模型限制在与我们产品相关的特定话题上。为此，我们可能希望为模型提供与用户问题相关的上下文信息，并使模型仅基于这些信息回答问题。这种方法称为检索增强生成（RAG），我将展示如何在我们的多用户环境中应用它。

# 资源和工件

用于训练和评估的笔记本可以在Colab上找到：[数据集生成](https://colab.research.google.com/drive/1JWAK3ecaO4EjUqIk1LBIfGQ8zV4oWmDk?usp=drive_link)，[训练](https://colab.research.google.com/drive/1MDXvw8ie3XnpR0OujFhwLgTU0XIeepSv?usp=drive_link) 和 [推理](https://colab.research.google.com/drive/18Lsn3ws5H3jCEJ-loLmuL4_195BOEEoa?usp=drive_link)。

合成数据集可以在[这里](https://huggingface.co/jjezabek/multi_user_chat_synthetic)获取。

最后，这些模型可以在HuggingFace上找到，已根据[Zephyr](https://huggingface.co/jjezabek/multi-user-chat-zephyr-7b-beta-completions-only)、[Llama-2](https://huggingface.co/jjezabek/multi-user-chat-llama-2-7b-chat-completions-only)和[OpenChat-3.5](https://huggingface.co/jjezabek/multi-user-chat-openchat-3.5-0106-completions-only)进行微调。如果你对基于完整对话（而非仅仅是完成）的模型感兴趣，它们也可以找到，微调自[Zephyr](https://huggingface.co/jjezabek/multi-user-chat-zephyr-7b-beta-full-conversations)、[Llama-2](https://huggingface.co/jjezabek/multi-user-chat-llama-2-7b-chat-full-conversations)和[OpenChat-3.5](https://huggingface.co/jjezabek/multi-user-chat-openchat-3.5-0106-full-conversations)。

# 故障排除

下面列出了一些在微调过程中我经常遇到的陷阱，这些可能在微调其他模型时也会派上用场。

## 填充标记

我在多个教程中看到填充标记被设置为EOS标记（在Zephyr模型中也默认如此）。然而，这与HuggingFace的数据合并器不兼容：*DataCollatorForLanguageModeling*中的[这一行](https://github.com/huggingface/transformers/blob/976189a6df796a2ff442dd81b022626c840d8c27/src/transformers/data/data_collator.py#L752)意味着模型不会被训练来预测填充标记。如果填充标记和EOS标记相同，最终可能会导致模型在生成标记时不停地继续生成。我的建议是将填充标记设置为UNK标记（如果可用，并且与EOS不同）。另外，你也可以使用分词器的*add_token*方法将其添加到词汇表中。

简而言之：**确保填充标记与EOS标记不同。** HuggingFace的最近版本已经开始添加这个警告，以提高问题的可见性：

```py
UserWarning: The pad_token_id and eos_token_id values of this tokenizer are identical. If you are planning for multi-turn training, it can result in the model continuously generating questions and answers without eos token. To avoid this, set the pad_token_id to a different value.
```

## 训练过程中损失下降至0.0

使用半精度浮点数（即torch.float16）时，我曾遇到过一种情况，即损失函数在几步之后降到0.0并保持在那里。具体来说，这种情况发生在我们使用Llama-2模型的训练笔记本中。网上也有类似问题的报告（例如[这里](https://gist.github.com/younesbelkada/9f7f75c94bdc1981c8ca5cc937d4a4da?permalink_comment_id=4634125#gistcomment-4634125)），有趣的是，它们当时是通过将分词器的*padding_side*设置为“right”来解决的。在我们的情况下，填充已经在右侧，因此这个解决方法并不适用。

解决方法是使用不同类型的训练：可以使用torch.bfloat16（这在较旧的实例如T4和V100上不可用）或torch.float32（这会导致训练时性能下降，但在其他情况下运行正常）。

## “RuntimeError: element 0 of tensors does not require grad…”

根据模型的不同，你可能会遇到以下错误：

```py
RuntimeError: element 0 of tensors does not require grad and does not have a grad_fn
```

简单的解决方法是在实例化模型后添加这一行：

```py
model.enable_input_require_grads()
```
