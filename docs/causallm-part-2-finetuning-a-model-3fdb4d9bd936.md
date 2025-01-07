# CausalLM 第二部分：微调模型

> 原文：[`towardsdatascience.com/causallm-part-2-finetuning-a-model-3fdb4d9bd936?source=collection_archive---------13-----------------------#2024-03-14`](https://towardsdatascience.com/causallm-part-2-finetuning-a-model-3fdb4d9bd936?source=collection_archive---------13-----------------------#2024-03-14)

## 微调 CausalLM 模型的三种方式：用于聊天数据

[](https://tlebryk.medium.com/?source=post_page---byline--3fdb4d9bd936--------------------------------)![Theo Lebryk](https://tlebryk.medium.com/?source=post_page---byline--3fdb4d9bd936--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3fdb4d9bd936--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3fdb4d9bd936--------------------------------) [Theo Lebryk](https://tlebryk.medium.com/?source=post_page---byline--3fdb4d9bd936--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3fdb4d9bd936--------------------------------) ·7 分钟阅读·2024 年 3 月 14 日

--

![](img/e96c0450be2b342ca68c996282211eae.png)

在本教程中，我们将对一个 CausalLM 模型进行微调，以实现简单的翻译功能。图片来自 [Rob Wilson](https://unsplash.com/@ventanamedia?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在 上一篇文章 中，我们讨论了 CausalLM 是什么，以及 Hugging Face 期望数据如何格式化。在本篇文章中，我们将通过一个简化的笔记本，介绍三种格式化数据以微调模型的方法。第一种方法是在上一篇文章的直觉基础上构建的，只需将 input_ids 复制到 labels。第二种方法使用了掩码技术，学习文本的特定部分。第三种方法使用了一个独立的库，[TRL](https://huggingface.co/docs/trl/main/en/index)，这样我们就不需要手动掩盖数据。

我将省略一些函数定义以保持可读性，因此最好参考 [完整的笔记本](https://github.com/tlebryk/CausalLM/blob/main/finetune_casuallm.ipynb) 来获取所有代码。

## 使用从 input_ids 复制的 labels 进行微调

我们将使用 [Bloom-560m](https://huggingface.co/bigscience/bloom-560m)，这是一款多语言模型，足够小巧，可以在标准笔记本电脑上进行微调。

```py
model_name = "bigscience/bloom-560m"
tokenizer = AutoTokenizer.from_pretrained(
    model_name, trust_remote_code=True, padding_side="right"
)  # padding side should be right for CausalLM models
# overfit to 5 made up examples
str1 = '\n\n### Human: How do you say "dog" in Spanish?\n\n### Assistant: perro'
str2 = '\n\n### Human: How do you say "water" in Spanish?\n\n### Assistant: agua'
str3 = '\n\n### Human: How do you say "hello" in Spanish?\n\n### Assistant: hola'
str4 = '\n\n### Human: How do you say "tree" in Spanish?\n\n### Assistant: árbol'
str5 = '\n\n### Human: How do you say "mother" in Spanish?\n\n### Assistant: madre'
train_data = {
    "text": [str1, str2, str3, str4, str5],
}
dataset_text = Dataset.from_dict(train_data)

# to test if we learn how to generate an unknown word. 
holdout_str = (
    '\n\n### Human: How do you say "day" in Spanish?\n\n### Assistant:<s>'  # día
)
device = "cuda" if torch.cuda.is_available() else "cpu"
holdout_input = tokenizer(holdout_str, return_tensors="pt").to(device)
```

让我们首先进行一些预处理。我们将添加一些特殊的标记，分别是“序列结束”（eos）和“序列开始”（bos）。这些特殊标记可以帮助模型知道何时开始和停止生成文本。

```py
INSTRUCTION_TEMPLATE_BASE = "\n\n### Human:"
RESPONSE_TEMPLATE_BASE = "\n\n### Assistant:"
def add_special_tokens(
    example: Dict,
    tokenizer: PreTrainedTokenizerBase,
) -> Dict:
    # add eos_token before human text and bos_token before assistant text
    example["text"] = (
        example["text"]
        .replace(
            INSTRUCTION_TEMPLATE_BASE, tokenizer.eos_token + INSTRUCTION_TEMPLATE_BASE
        )
        .replace(RESPONSE_TEMPLATE_BASE, RESPONSE_TEMPLATE_BASE + tokenizer.bos_token)
    )
    if not example["text"].endswith(tokenizer.eos_token):
        example["text"] += tokenizer.eos_token
    # Remove leading EOS tokens
    while example["text"].startswith(tokenizer.eos_token):
        example["text"] = example["text"][len(tokenizer.eos_token) :]
    return example

dataset_text = dataset_text.map(lambda x: add_special_tokens(x, tokenizer))
print(f"{dataset_text=}")
print(f"{dataset_text[0]=}")
>>> dataset_text=Dataset({
    features: ['text'],
    num_rows: 5
})
>>> dataset_text[0]={'text': '\n\n### Human: How do you say "dog" in Spanish?\n\n### Assistant:<s> perro</s>'}
```

现在，我们将进行上次学习的内容：创建一个带有从 input_ids 复制过来的 labels 键的输入。

```py
# tokenize the text
dataset = dataset_text.map(
    lambda example: tokenizer(example["text"]), batched=True, remove_columns=["text"]
)
# copy the input_ids to labels
dataset = dataset.map(lambda x: {"labels": x["input_ids"]}, batched=True)
print(f"{dataset=}")
print(f"{dataset[0]['input_ids']=}")
print(f"{dataset[0]['labels']=}")
>>> dataset=Dataset({
    features: ['input_ids', 'attention_mask', 'labels'],
    num_rows: 5
})
>>> dataset[0]['input_ids']=[603, 105311, 22256, 29, 7535, 727, 1152, 5894, 20587, 744, 5, 361, 49063, 7076, 105311, 143005, 29, 1, 82208, 2]
>>> dataset[0]['labels']=[603, 105311, 22256, 29, 7535, 727, 1152, 5894, 20587, 744, 5, 361, 49063, 7076, 105311, 143005, 29, 1, 82208, 2]
```

首先，labels 和 input_ids 是相同的。让我们看看训练一个这样的模型会发生什么。

```py
# training code inspired by
#https://mlabonne.github.io/blog/posts/Fine_Tune_Your_Own_Llama_2_Model_in_a_Colab_Notebook.html
model = load_model(model_name)
output_dir = "./results"
# How many times to iterate over the entire dataset
num_train_epochs = 15
# We're not aligning the sequence length (ie padding or truncating)
# so batch training won't work for our toy example.
per_device_train_batch_size = 1

training_arguments = TrainingArguments(
    output_dir=output_dir,
    num_train_epochs=num_train_epochs,
    per_device_train_batch_size=per_device_train_batch_size,
    seed=1,
)
trainer = Trainer(
    model=model,
    train_dataset=dataset,
    args=training_arguments,
)
training1 = trainer.train()

# Sample generate prediction on holdout set
“\n\n### Human: How do you say "good" in Spanish?\n\n### Assistant:”
# the correct output is “bueno</s>”

sample_generate(model, tokenizer, holdout_inputs, max_new_tokens=5)
>>> ‘</s>’
```

经过 15 个 epoch 后，我们仍然有些困惑。我们输出了‘</s>’，这虽然接近，但我们真正想输出的是“bueno</s>”。让我们再学习 15 个 epoch。

```py
trainer.train()
sample_generate(model, tokenizer, holdout_input, max_new_tokens=5)
>>> bueno </s>
```

经过 30 个 epoch 后，我们学到了应该学到的内容！

让我们通过迭代地根据前面的标记逐个预测提示，来模拟训练中发生的情况。

```py
print_iterative_generate(model, tokenizer, inputs)
>>>
#
: How do you say "how morning in Spanish?

### Assistant: gu buenopu
```

这与实际的提示非常接近，正如我们预期的那样。但任务是翻译，所以我们并不真正关心是否能预测用户的提示。有办法只学习回应部分吗？

## 掩蔽方法

Hugging Face 允许你通过“掩蔽”你不关心的标记来只学习预测特定的标记。这与注意力掩蔽不同，后者隐藏的是我们用来生成新标记的*先前*标记。掩蔽标签会将你应该在特定位置输出的标记从损失函数中隐藏。请注意措辞：Hugging Face 的实现是这样的，在训练过程中，我们仍然会为被掩蔽的标记生成预测。然而，由于我们隐藏了真实标签以与预测进行比较，因此我们不会直接学习如何改进该预测。

我们通过将那些标记在 labels 键中翻转为-100 来创建“掩蔽”。

```py
def create_special_mask(example: Dict) -> Dict:
    """Mask human text and keep assistant text as it is.

    Args:
        example (Dict): Result of tokenizing some text

    Returns:
        Dict: The dict with the label masked
    """
    # setting a token to -100 is how we "mask" a token
    # and tell the model to ignore it when calculating the loss
    mask_token_id = -100
    # assume we always start with a human text
    human_text = True
    for idx, tok_id in enumerate(example["labels"]):
        if human_text:
            # mask all human text up until and including the bos token
            example["labels"][idx] = mask_token_id
            if tok_id == tokenizer.bos_token_id:
                human_text = False
        elif not human_text and tok_id == tokenizer.eos_token_id:
            # don’t mask the eos token, but the next token will be human text to mask
            human_text = True
        elif not human_text:
            # leave example['labels'] text as it is when assistant text
            continue
    return example

dataset_masked = dataset.map(create_special_mask)
# convert dataset from lists to torch tensors
dataset_masked.set_format(type="torch", columns=["input_ids", "attention_mask", "labels"])
print(f"{dataset_masked[0]["labels"]=}")

>>> dataset[0]["labels"]=tensor([ -100,  -100,  -100,  -100,  -100,  -100,  -100,  -100,  -100,  -100, -100,  -100,  -100,  -100,  -100,  -100,  -100,  -100, 82208,     2])
```

```py
model = load_model(model_name)
trainer = Trainer(
    model=model,
    train_dataset=dataset_masked,
    args=training_arguments,
)

training2 = trainer.train()

print(f"{training2.metrics['train_runtime']=}")
print(f"{training1.metrics['train_runtime'] =}")
print(
    f"{100*round((training1.metrics['train_runtime']  - training2.metrics['train_runtime']) / training1.metrics['train_runtime'] , 2)}%"
)

>>> training2.metrics['train_runtime']=61.7164
>>> training1.metrics['train_runtime'] =70.8013
>>> 13.0%
```

首先，这次我们的速度提高了超过 10%。可以推测，标记计算量较少使得训练速度变得更快。

我不会指望速度提升这么大——我们的示例在很多方面偏向人工文本而不是生成文本。但当训练时间达到小时级时，每一个小百分比都非常有用。

最大的问题是：我们学到了任务吗？

```py
sample_generate(model, tokenizer, holdout_input, max_new_tokens=5)
>>> bueno </s>
```

这次我们只需要 15 个 epoch 来学习任务。让我们回过头看看训练过程中背后的实际情况。

```py
print_iterative_generate(model, tokenizer, inputs)
>>>#include
 code
 to I get "we" in English?
A: Spanish: How bueno
```

与我们最初的训练方法相比，迭代地预测提示会导致无意义的结果。这是合理的：在训练过程中我们对提示进行了掩蔽，因此在真正的目标——助手的回应到来之前，我们没有学会如何预测任何东西。

## 使用 TRL 的监督微调训练器

Hugging Face 最近推出了一个 TRL（变换器强化学习）库，以为 LLM 训练过程提供端到端的支持。一个特点是监督微调。通过使用 DataCollatorForCompletionOnlyLM 和 SFTTrainer 类，我们可以像使用*create_special_mask*一样，仅通过少量配置就创建标签。

```py
model = load_model(model_name)

# a hugging face function to do the copying of labels for you.
# using the instruction and response templates will mask everything between the instruction template and the start of the response_template
collator = DataCollatorForCompletionOnlyLM(
    instruction_template=tokenizer.eos_token,
    response_template=tokenizer.bos_token,
    tokenizer=tokenizer,
)

trainersft = SFTTrainer(
    model,
    train_dataset=dataset_text,
    dataset_text_field="text",
    data_collator=collator,
    args=training_arguments,
    tokenizer=tokenizer,
)
sftrain = trainersft.train()

sample_generate(model, tokenizer, holdout_input, max_new_tokens=5)
>>> ' bueno</s>'
```

成功了！如果深入挖掘，实际上使用 SFT 的训练时间更长。这可能归因于我们必须在训练时进行标记化，而不是像掩蔽方法那样在预处理步骤中进行。然而，这种方法为我们提供了免费的批处理（如果使用掩蔽方法来正确批处理，你需要调整标记化过程），这在长远来看应该能加速训练。

完整的笔记本还探讨了其他一些内容，比如从多轮对话中进行训练，以及使用 special_tokens 来区分人类和聊天文本。

很明显，这个例子有些基础。然而，希望你能开始看到使用因果语言模型（CausalLM）的强大之处：你可以想象通过一个大型、可靠的模型进行交互，然后使用上述技巧在大型模型的输出上微调一个较小的模型。这就是所谓的知识蒸馏。

如果我们从过去几年关于大型语言模型（LLM）的研究中学到了一些东西，那就是通过训练预测下一个词，我们可以做出一些令人惊讶的智能行为。因果语言模型就是为了实现这一点而设计的。即使最初 Hugging Face 的类有些令人困惑，一旦你习惯了它，你就能拥有一个非常强大的接口来训练你自己的生成模型。
