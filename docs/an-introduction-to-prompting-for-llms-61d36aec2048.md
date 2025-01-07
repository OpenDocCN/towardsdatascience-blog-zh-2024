# 大语言模型提示简介

> 原文：[https://towardsdatascience.com/an-introduction-to-prompting-for-llms-61d36aec2048?source=collection_archive---------3-----------------------#2024-02-22](https://towardsdatascience.com/an-introduction-to-prompting-for-llms-61d36aec2048?source=collection_archive---------3-----------------------#2024-02-22)

## 我们如何与大语言模型（LLMs）有效沟通？

[](https://medium.com/@anand.subu10?source=post_page---byline--61d36aec2048--------------------------------)[![Anand Subramanian](../Images/096dc5504d6ada2493e0ac26959e60f0.png)](https://medium.com/@anand.subu10?source=post_page---byline--61d36aec2048--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--61d36aec2048--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--61d36aec2048--------------------------------) [Anand Subramanian](https://medium.com/@anand.subu10?source=post_page---byline--61d36aec2048--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--61d36aec2048--------------------------------) ·30分钟阅读·2024年2月22日

--

除非你完全断开了与社交媒体和新闻的联系，否则不太可能错过围绕大语言模型（LLMs）的热议。

![](../Images/eb60be0bd7ee67fd4f1a4f16239215c8.png)

LLMs的演变。图像来源于论文[1]（[来源](https://github.com/Mooler0410/LLMsPracticalGuide/blob/main/imgs/tree.png)）。即使在我添加这张图时，当前LLM发展的速度也让这张图片显得过时。

LLMs已变得无处不在，几乎每天都有新模型发布。它们也变得更加普及，得益于一个蓬勃发展的开源社区，该社区在减少内存需求和为LLMs开发高效微调方法方面发挥了关键作用，即使在计算资源有限的情况下。

LLMs最令人兴奋的应用之一是它们在没有明确训练的任务中表现出的卓越能力，仅通过任务描述和可选的少量示例。现在，你可以让一个强大的LLM生成你最喜欢的作者风格的故事，将长邮件总结成简洁的版本，甚至通过向模型描述任务来开发创新的营销活动，而无需微调模型。那么，如何最好地将你的需求传达给LLM呢？这就是提示技术的作用所在。

# 目录：

1.  [什么是提示？](#0f7f)

1.  [为什么提示很重要？](#a04b)

1.  [探索不同的提示策略](#f3d4)

1.  [我们如何实现这些技术？](#3a00) 4.1\. [使用零样本提示Llama 2 7B-Chat](#5921)

    4.2\. [使用少量示例提示Llama 2 7B-Chat](#0ac4)

    4.3\. [如果我们不遵循聊天模板，会发生什么？](#a04e)

    4.4\. [使用CoT提示法对Llama 2 7B-Chat进行提示](#bfe1)

    4.5\. [Llama 2中CoT的失败模式](#0ac0)

    4.6\. [使用零-shot提示法对GPT-3.5进行提示](#c938)

    4.7\. [使用Few-Shot提示法对GPT-3.5进行提示](#a575)

    4.8\. [使用CoT提示法对GPT-3.5进行提示](#18e9)

1.  [结论与要点](#913f)

1.  [可重复性](#f8e9)

1.  [参考文献](#42e4)

# **什么是提示？**

提示，或称为提示工程，是一种用于设计输入或提示的技术，旨在引导人工智能模型——特别是自然语言处理和图像生成领域的模型——生成特定的、期望的输出。提示的过程包括将你的需求结构化成一种输入格式，以便有效地向模型传达期望的结果，从而获得预期的输出。

大型语言模型（LLMs）展现了**上下文学习**的能力[2] [3]。这意味着这些模型能够仅仅根据任务描述和通过提示提供给模型的示例来理解和执行各种任务，而无需为每个新任务进行专门的微调。在这种背景下，提示非常重要，因为它是用户与模型之间的主要接口，帮助利用这种能力。一个定义清晰的提示有助于向LLM定义任务的性质和期望，并指导如何以可用的方式向用户提供输出。

你可能会认为提示LLM应该并不困难；毕竟，这只是用自然语言向模型描述你的需求，对吧？但实际上，这并不像看起来那么简单。你会发现不同的LLM有着不同的优势。有些模型可能更好地遵循你期望的输出格式，而其他模型则可能需要更详细的指令。你希望LLM执行的任务可能很复杂，要求精确而详尽的指令。因此，设计一个合适的提示通常需要大量的实验和基准测试。

# **为什么提示很重要？**

在实际应用中，大型语言模型（LLMs）对输入的结构和提供方式非常敏感。我们可以从多个角度分析这一现象，以更好地理解这种情况：

1.  **遵循提示格式**：LLMs通常采用不同的提示格式来接收用户输入。这通常发生在模型经过指令调优或针对聊天用例进行了优化时[4] [5]。从宏观角度来看，大多数提示格式包括***指令***和***输入***。指令描述模型需要执行的任务，而输入则包含任务需要处理的文本。例如，我们以Alpaca指令格式为例（来源于[https://github.com/tatsu-lab/stanford_alpaca](https://github.com/tatsu-lab/stanford_alpaca)）：

```py
Below is an instruction that describes a task, paired with an input that provides further context. Write a response that appropriately completes the request.

### Instruction:
{instruction}

### Input:
{input}

### Response:
```

由于模型是通过使用类似模板的指令调优的，因此当用户使用相同格式向模型发出提示时，模型期望能够最佳地执行任务。

2\. **描述输出格式以便解析：** 在向模型提供提示后，你可能希望从模型的输出中提取所需的信息。理想情况下，这些输出应该是你可以通过编程方法轻松解析的格式。根据任务的不同，比如文本分类，这可能需要使用正则表达式（regex）来筛选 LLM 的输出。相反，对于需要更细粒度数据的任务，如命名实体识别（NER），你可能更倾向于使用 JSON 格式来输出。

然而，你与 LLM 的合作越多，你越快意识到获得可解析的输出是具有挑战性的。LLM 往往难以精确地按用户要求的格式提供输出。虽然像少量示例提示这样的策略可以显著缓解这个问题，但要从 LLM 获得一致且程序化可解析的输出，还需要通过仔细的实验和调整。

3\. **优化性能的提示：** LLM 对任务描述非常敏感。如果提示不够精确或者留有太多解释空间，可能会导致较差的表现。想象一下你在向某人解释一项任务——你的解释越清晰、越详细，对方的理解就会越好。然而，达到理想提示并没有魔法公式。这需要通过仔细的实验和评估不同的提示，选择表现最好的提示。

# **探索不同的提示策略**

希望到目前为止，你已经认识到需要认真对待提示。如果提示是一个工具箱，那我们可以利用哪些工具呢？

**零-shot 提示：** 零-shot 提示[2][3]是指仅通过提示中描述的任务来指示 LLM 执行任务，而不提供示例。术语“零-shot”意味着模型必须完全依赖提示中的任务描述，因为它没有得到与任务相关的具体示范。

![](../Images/bf12c12c151c864333b4f2fb77809d97.png)

零-shot 提示概览。（图片由作者提供）

在许多情况下，零-shot 提示就足以指示 LLM 执行你希望的任务。然而，如果任务过于模糊、开放性或不明确，零-shot 提示可能会有局限性。假设你想让 LLM 按 1 到 5 的评分标准对答案进行排名。尽管模型可以使用零-shot 提示完成这个任务，但在这里可能会出现两个问题：

1.  LLM 可能无法客观理解评分标准中每个分数所代表的含义。如果任务描述缺乏细节，它可能难以判断何时给答案打 3 分或 4 分。

1.  LLM 可能有自己对 1 到 5 评分的理解，这可能与个人评分标准相冲突。你可能在评分时优先考虑答案的事实准确性，但模型可能会根据答案的写作质量来评估。

为了让模型更好地理解你的评分预期，你可以提供一些答案示例，并说明如何对它们进行评分。这样，模型就有了更多的上下文和参考，帮助它理解如何评分文档，从而减少任务中的模糊性。这引出了少量示例提示。

**少量示例提示（Few-shot Prompting）：** 少量示例提示通过少量的示例输入及其相应的输出丰富了任务描述 [3]。这种技术通过包含几个示范对，帮助模型更好地理解任务。

![](../Images/58cc2240a055d9a5cce738de853f7c5b.png)

少量示例提示概述。（图片由作者提供）

例如，为了指导 LLM 进行电影评论的情感分类，你可以提供几条评论及其情感评分。少量示例提示相比零示例提示的主要优势是，能够展示如何执行任务的示例，而不是仅凭描述期望 LLM 完成任务。

**思维链（Chain of Thought，CoT）：** 思维链（CoT）提示 [6] 是一种技术，能够通过将复杂问题分解为更简单的中间步骤，使 LLM 解决问题。这种方法鼓励模型“边思考边说”，使其推理过程变得透明，从而让 LLM 更有效地解决推理问题。正如文献 [6] 的作者所提到的，CoT 模拟了人类通过将问题分解为更简单的步骤并逐一解决它们，而不是直接跳到答案的方式来解决推理问题。

![](../Images/79baf1845e581cfe3140508309256a9c.png)

思维链提示概述。（图片由作者提供）

CoT 提示通常作为少量示例提示实现，其中模型接收任务描述和输入-输出对的示例。这些示例包括推理步骤，这些步骤系统性地引导至正确答案，展示如何处理信息。因此，为了有效地执行 CoT 提示，用户需要高质量的示范示例。然而，对于需要专门领域知识的任务，这可能是一个挑战。例如，使用大型语言模型（LLM）根据患者病史进行医学诊断时，需要领域专家的协助，如医生或医学专家，来阐明正确的推理步骤。此外，CoT 在具有足够大参数规模的模型中尤其有效。根据文献 [6]，CoT 在 137B 参数的 LaMBDA [7]、175B 参数的 GPT-3 [3] 和 540B 参数的 PaLM [8] 模型中最为有效。这个限制可能会限制其在小规模模型中的适用性。

![](../Images/7e9847b192ff473c6e794fd82530c80e.png)

从文献 [6] 中取图（[来源](https://arxiv.org/abs/2201.11903)）显示，CoT 提示所带来的性能提升随着模型规模的增大而显著提升。

CoT 提示的另一个特点是，它与标准提示的不同之处在于模型需要生成更多的标记才能得出最终答案。虽然这不一定是缺点，但如果你在推理时受限于计算资源，这是需要考虑的一个因素。

如果你想要更深入的概览，我推荐 OpenAI 的提示资源，可以在 [https://platform.openai.com/docs/guides/prompt-engineering/strategy-write-clear-instructions](https://platform.openai.com/docs/guides/prompt-engineering/strategy-write-clear-instructions) 查阅。

# **我们如何实现这些技术？**

> 本文相关的所有代码和资源都可以在 [这个 Github 仓库](https://github.com/anand-subu/blog_resources/tree/main) 中找到，位于 introduction_to_prompting 文件夹下。欢迎克隆仓库并直接运行 notebooks 进行实验。如果你有任何反馈或观察，或者发现任何错误，请告诉我！

我们可以在一个示例数据集上探索这些技术，以便更容易理解。为此，我们将使用 MedQA 数据集[9]，该数据集包含测试医学和临床知识的问题。我们将特别利用该数据集中的 USMLE 问题。这个任务非常适合分析各种提示技术，因为回答这些问题需要知识和推理。我们将测试 Llama-2 7B[10] 和 GPT-3.5[11] 在该数据集上的表现。

首先下载数据集。MedQA 数据集可以从 [这个链接](https://drive.google.com/file/d/1ImYUSLk9JbgHXOemfvyiDiirluZHPeQw/view) 下载。下载数据集后，我们可以解析并开始处理问题。测试集包含 1,273 个问题。我们从测试集中随机抽取 300 个问题来评估模型，并从训练集中随机选择 3 个示例作为我们对模型的少量示范。

```py
import json
import random
random.seed(42)

def read_jsonl_file(file_path):
    """
    Parses a JSONL (JSON Lines) file and returns a list of dictionaries.

    Args:
        file_path (str): The path to the JSONL file to be read.

    Returns:
        list of dict: A list where each element is a dictionary representing
            a JSON object from the file.
    """
    jsonl_lines = []
    with open(file_path, 'r', encoding="utf-8") as file:
        for line in file:
            json_object = json.loads(line)
            jsonl_lines.append(json_object)

    return jsonl_lines

def write_jsonl_file(dict_list, file_path):
    """
    Write a list of dictionaries to a JSON Lines file.

    Args:
    - dict_list (list): A list of dictionaries to write to the file.
    - file_path (str): The path to the file where the data will be written.
    """
    with open(file_path, 'w') as file:
        for dictionary in dict_list:
            # Convert the dictionary to a JSON string and write it to the file.
            json_line = json.dumps(dictionary)
            file.write(json_line + '\n')

# read the contents of the train and test set
train_set = read_jsonl_file("data_clean/questions/US/4_options/phrases_no_exclude_train.jsonl")
test_set = read_jsonl_file("data_clean/questions/US/4_options/phrases_no_exclude_test.jsonl")

# subsample test set samples and few-shot samples
test_set_subsampled = random.sample(test_set, 300)
few_shot_examples = random.sample(test_set, 3)

# dump the sampled questions and few-shot samples as jsonl files
write_jsonl_file(test_set_subsampled, "USMLE_test_samples_300.jsonl")
write_jsonl_file(few_shot_examples, "USMLE_few_shot_samples.jsonl")
```

## **使用零样本提示引导 Llama 2 7B-Chat**

> Llama 系列模型由 Meta 发布。它们是仅解码器的 LLM 系列，参数范围从 7B 到 70B。Llama-2 系列模型有两种变体：基础版本和聊天/指令微调版本。对于本次练习，我们将使用 Llama 2-7B 模型的聊天版本。

让我们看看如何有效地引导 Llama 模型回答这些医学问题。我们将模型加载到内存中：

```py
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer
from tqdm import tqdm

questions = read_jsonl_file("USMLE_test_samples_300.jsonl")

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-chat-hf")
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-chat-hf",  torch_dtype=torch.bfloat16).cuda()
model.eval()
```

> 如果你使用的是 Nvidia Ampere GPU，你可以使用 torch.bfloat16 加载模型。它可以加速推理，并比普通的 FP16/FP32 使用更少的 GPU 内存。

首先，让我们为我们的任务设计一个基本的提示：

```py
PROMPT = """You will be provided with a medical or clinical question, along with multiple possible answer choices. Pick the right answer from the choices. 
Your response should be in the format "The answer is <correct_choice>". Do not add any other unnecessary content in your response"""
```

我们的提示非常简单，包含任务的性质信息，并提供输出格式的指令。我们将看到这个提示在实践中的效果如何。

Llama-2 聊天模型有一个特定的聊天模板，需要遵循该模板来提示它们。

```py
<s>[INST] <<SYS>>
You will be provided with a medical or clinical question, along with multiple possible answer choices. Pick the right answer from the choices. 
Your response should be in the format "The answer is <correct_choice>". Do not add any other unnecessary content in your response
<</SYS>>

A 21-year-old male presents to his primary care provider for fatigue. He reports that he graduated from college last month and returned 3 days ago from a 2 week vacation to Vietnam and Cambodia. For the past 2 days, he has developed a worsening headache, malaise, and pain in his hands and wrists. The patient has a past medical history of asthma managed with albuterol as needed. He is sexually active with both men and women, and he uses condoms “most of the time.” On physical exam, the patient’s temperature is 102.5°F (39.2°C), blood pressure is 112/66 mmHg, pulse is 105/min, respirations are 12/min, and oxygen saturation is 98% on room air. He has tenderness to palpation over his bilateral metacarpophalangeal joints and a maculopapular rash on his trunk and upper thighs. Tourniquet test is negative. Laboratory results are as follows:

Hemoglobin: 14 g/dL
Hematocrit: 44%
Leukocyte count: 3,200/mm^3
Platelet count: 112,000/mm^3

Serum:
Na+: 142 mEq/L
Cl-: 104 mEq/L
K+: 4.6 mEq/L
HCO3-: 24 mEq/L
BUN: 18 mg/dL
Glucose: 87 mg/dL
Creatinine: 0.9 mg/dL
AST: 106 U/L
ALT: 112 U/L
Bilirubin (total): 0.8 mg/dL
Bilirubin (conjugated): 0.3 mg/dL

Which of the following is the most likely diagnosis in this patient?
Options:
A. Chikungunya
B. Dengue fever
C. Epstein-Barr virus
D. Hepatitis A [/INST]
```

任务描述应位于<<SYS>>标记之间，后跟模型需要回答的实际问题。提示以[/INST]标记结尾，表示输入文本的结束。

> 角色可以是“**user**”、“**system**”或“**assistant**”之一。系统角色为模型提供任务描述，用户角色包含模型需要响应的输入。这与我们稍后在与GPT-3.5交互时采用的约定相同。它相当于为Llama-2创建一个虚构的多轮对话历史，其中每一轮对应一个示范示例和模型的理想输出。

听起来很复杂？幸运的是，Huggingface Transformers库支持将提示转换为聊天模板。我们将利用这个功能来简化我们的工作。让我们从辅助功能开始，处理数据集并创建提示。

```py
def create_query(item):
    """
    Creates the input for the model using the question and the multiple choice options.

    Args:
        item (dict): A dictionary containing the question and options.
            Expected keys are "question" and "options", where "options" is another
            dictionary with keys "A", "B", "C", and "D".

    Returns:
        str: A formatted query combining the question and options, ready for use.
    """
    query = item["question"] + "\nOptions:\n" + \
            "A. " + item["options"]["A"] + "\n" + \
            "B. " + item["options"]["B"] + "\n" + \
            "C. " + item["options"]["C"] + "\n" + \
            "D. " + item["options"]["D"]
    return query

def build_zero_shot_prompt(system_prompt, question):
    """
    Builds the zero-shot prompt.

    Args:
        system_prompt (str): Task Instruction
        content (dict): The content for which to create a query, formatted as
            required by `create_query`.

    Returns:
        list of dict: A list of messages, including a system message defining
            the task and a user message with the input question.
    """
    messages = [{"role": "system", "content": system_prompt},
                {"role": "user", "content": create_query(question)}]
    return messages
```

该功能构建了提供给LLM的查询。MedQA数据集将每个问题存储为JSON元素，问题和选项作为键提供。我们解析JSON并构建问题及其选择。

让我们开始从模型中获取输出。当前任务是通过从多个选项中选择正确答案来回答提供的医学问题。与内容创作或总结等创造性任务不同，这些任务可能需要模型在输出中展现想象力和创造力，这是一个基于知识的任务，旨在测试模型根据其参数中编码的知识回答问题的能力。因此，在生成答案时，我们将使用贪婪解码。让我们定义一个辅助函数来解析模型的回答并计算准确度。

```py
pattern = re.compile(r"([A-Z])\.\s*(.*)")

def parse_answer(response):
    """
    Extracts the answer option from the predicted string.

    Args:
    - response (str): The string to search for the pattern.

    Returns:
    - str: The matched answer option if found or an empty string otherwise.
    """
    match = re.search(pattern, response)
    if match:
        letter = match.group(1)
    else:
        letter = ""

    return letter

def calculate_accuracy(ground_truth, predictions):
    """
    Calculates the accuracy of predictions compared to ground truth labels.

    Args:
    - ground_truth (list): A list of true labels.
    - predictions (list): A list of predicted labels.

    Returns:
    - float: The accuracy of predictions as a fraction of correct predictions over total predictions.
    """
    return sum([1 if x==y else 0 for x,y in zip(ground_truth, predictions)]) / len(ground_truth)
```

```py
ground_truth = []

for item in questions:
    ans_options = item["options"]
    correct_ans_option = ""
    for key,value in ans_options.items():
        if value == item["answer"]:
            correct_ans_option = key
            break

    ground_truth.append(correct_ans_option)
```

```py
zero_shot_llama_answers = []
for item in tqdm(questions):
    zero_shot_prompt_messages = build_zero_shot_prompt(PROMPT, item)
    input_ids = tokenizer.apply_chat_template(zero_shot_prompt_messages, tokenize=True, return_tensors="pt").cuda()  #modified on 09/03/2024   
    # prompt = tokenizer.apply_chat_template(zero_shot_prompt_messages, tokenize=False)
    # input_ids = tokenizer(prompt, return_tensors="pt", truncation=True).input_ids.cuda()    
    outputs = model.generate(input_ids=input_ids, max_new_tokens=10, do_sample=False)
    # https://github.com/huggingface/transformers/issues/17117#issuecomment-1124497554
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    zero_shot_llama_answers.append(gen_text.strip())

zero_shot_llama_predictions = [parse_answer(x) for x in zero_shot_llama_answers]
print(calculate_accuracy(ground_truth, zero_shot_llama_predictions))
```

在零样本设置下，我们的表现为3̶6̶%̶ 35%。这不是一个糟糕的开始，但让我们看看能否进一步提升表现。

> **编辑于2024年09月03日** — 我注意到提示格式和标记化方式中有一个小“bug”。具体来说，我使用了tokenizer.apply_chat_template(zero_shot_prompt_messages, tokenize = False)，然后调用tokenizer提前获取input_ids。这个方法在提示开始时额外添加了一个<s>标记。apply_chat_template已经将<s>标记添加到序列中，但在创建聊天模板后再次调用tokenizer会在开头再次添加特殊标记<s>。我通过将apply_chat_template中的tokenize设置为True来修正这个问题。我得到的新得分是35%，比原来的36%小幅下降了1%。修复这个“bug”导致得分略微下降，这有点好笑，但为了避免在使用聊天模板时引起混淆，我在此和代码中做了修正。本文的其他发现和结论不受此修复的影响。

## 用少量示例提示进行Llama 2 7B-Chat的提示

现在我们为模型提供任务演示。我们使用从训练集中随机抽取的三个问题，并将它们作为任务演示附加到模型上。幸运的是，我们可以继续使用Transformers库提供的聊天模板支持和分词器，通过最小的代码修改来附加我们的少量示例。

```py
def build_few_shot_prompt(system_prompt, content, few_shot_examples):
    """
    Builds the few-shot prompt using provided examples.

    Args:
        system_prompt (str): Task description for the LLM
        content (dict): The content for which to create a query, similar to the
            structure required by `create_query`.
        few_shot_examples (list of dict): Examples to simulate a hypothetical
            conversation. Each dict must have "options" and an "answer".

    Returns:
        list of dict: A list of messages, simulating a conversation with
            few-shot examples, followed by the current user query.
    """
    messages = [{"role": "system", "content": system_prompt}]
    for item in few_shot_examples:
        ans_options = item["options"]
        correct_ans_option = ""
        for key, value in ans_options.items():
            if value == item["answer"]:
                correct_ans_option = key
                break
        messages.append({"role": "user", "content": create_query(item)})
        messages.append({"role": "assistant", "content": "The answer is " + correct_ans_option + "."})
    messages.append({"role": "user", "content": create_query(content)})
    return messages

few_shot_prompts = read_jsonl_file("USMLE_few_shot_samples.jsonl")
```

让我们可视化一下我们的少量示例提示是什么样子的。

```py
<s>[INST] <<SYS>>
You will be provided with a medical or clinical question, along with multiple possible answer choices. Pick the right answer from the choices. 
Your response should be in the format "The answer is <correct_choice>". Do not add any other unnecessary content in your response
<</SYS>>

A 30-year-old woman presents to the clinic because of fever, joint pain, and a rash on her lower extremities. She admits to intravenous drug use. Physical examination reveals palpable petechiae and purpura on her lower extremities. Laboratory results reveal a negative antinuclear antibody, positive rheumatoid factor, and positive serum cryoglobulins. Which of the following underlying conditions in this patient is responsible for these findings?
Options:
A. Hepatitis B infection
B. Hepatitis C infection
C. HIV infection
D. Systemic lupus erythematosus (SLE) [/INST] The answer is B. </s><s>[INST] A 10-year-old child presents to your office with a chronic cough. His mother states that he has had a cough for the past two weeks that is non-productive along with low fevers of 100.5 F as measured by an oral thermometer. The mother denies any other medical history and states that he has been around one other friend who also has had this cough for many weeks. The patient's vitals are within normal limits with the exception of his temperature of 100.7 F. His chest radiograph demonstrated diffuse interstitial infiltrates. Which organism is most likely causing his pneumonia?
Options:
A. Mycoplasma pneumoniae
B. Staphylococcus aureus
C. Streptococcus pneumoniae
D. Streptococcus agalactiae [/INST] The answer is A. </s><s>[INST] A 44-year-old with a past medical history significant for human immunodeficiency virus infection presents to the emergency department after he was found to be experiencing worsening confusion. The patient was noted to be disoriented by residents and staff at the homeless shelter where he resides. On presentation he reports headache and muscle aches but is unable to provide more information. His temperature is 102.2°F (39°C), blood pressure is 112/71 mmHg, pulse is 115/min, and respirations are 24/min. Knee extension with hips flexed produces significant resistance and pain. A lumbar puncture is performed with the following results:

Opening pressure: Normal
Fluid color: Clear
Cell count: Increased lymphocytes
Protein: Slightly elevated

Which of the following is the most likely cause of this patient's symptoms?
Options:
A. Cryptococcus
B. Group B streptococcus
C. Herpes simplex virus
D. Neisseria meningitidis [/INST] The answer is C. </s><s>[INST] A 21-year-old male presents to his primary care provider for fatigue. He reports that he graduated from college last month and returned 3 days ago from a 2 week vacation to Vietnam and Cambodia. For the past 2 days, he has developed a worsening headache, malaise, and pain in his hands and wrists. The patient has a past medical history of asthma managed with albuterol as needed. He is sexually active with both men and women, and he uses condoms “most of the time.” On physical exam, the patient’s temperature is 102.5°F (39.2°C), blood pressure is 112/66 mmHg, pulse is 105/min, respirations are 12/min, and oxygen saturation is 98% on room air. He has tenderness to palpation over his bilateral metacarpophalangeal joints and a maculopapular rash on his trunk and upper thighs. Tourniquet test is negative. Laboratory results are as follows:

Hemoglobin: 14 g/dL
Hematocrit: 44%
Leukocyte count: 3,200/mm^3
Platelet count: 112,000/mm^3

Serum:
Na+: 142 mEq/L
Cl-: 104 mEq/L
K+: 4.6 mEq/L
HCO3-: 24 mEq/L
BUN: 18 mg/dL
Glucose: 87 mg/dL
Creatinine: 0.9 mg/dL
AST: 106 U/L
ALT: 112 U/L
Bilirubin (total): 0.8 mg/dL
Bilirubin (conjugated): 0.3 mg/dL

Which of the following is the most likely diagnosis in this patient?
Options:
A. Chikungunya
B. Dengue fever
C. Epstein-Barr virus
D. Hepatitis A [/INST]
```

由于我们附加了三个示例，提示相当长。现在让我们用少量示例提示运行Llama-2，并获取结果：

```py
few_shot_llama_answers = []
for item in tqdm(questions):
    few_shot_prompt_messages = build_few_shot_prompt(PROMPT, item, few_shot_prompts)
    input_ids = tokenizer.apply_chat_template(few_shot_prompt_messages, tokenize=True, return_tensors="pt").cuda() #modified on 09/03/2024
    # prompt = tokenizer.apply_chat_template(few_shot_prompt_messages, tokenize=False)
    # input_ids = tokenizer(prompt, return_tensors="pt", truncation=True).input_ids.cuda()
    outputs = model.generate(input_ids=input_ids, max_new_tokens=10, do_sample=False)
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    few_shot_llama_answers.append(gen_text.strip())

few_shot_llama_predictions = [parse_answer(x) for x in few_shot_llama_answers]
print(calculate_accuracy(ground_truth, few_shot_llama_predictions))
```

现在我们得到了总的准确率为4̵1̵.̵6̵7̵%̵ 40.33%。还不错，比使用相同模型的零-shot提示提高了近6̵%̵ 5%！

> **编辑于2024年09月03日** — 与零-shot设置类似，我修正了在少量示例设置中提示发送到模型的方式。新得分为40.33%，与旧设置下的得分41.67%相比略微下降了~1.3%。再次有些有趣的是，修复这个“bug”导致得分小幅下降，但我在这里和代码中进行了修正，以避免在使用聊天模板时产生任何混淆。本文中的所有发现和结论不受此修复影响。

## 如果我们不遵循聊天模板，会发生什么？

早些时候，我提到过，根据最初用于微调LLM的提示模板来构建提示是明智的。让我们验证一下如果不遵循聊天模板，是否会影响我们的表现。我们创建了一个函数，使用相同的示例构建一个少量示例提示，但没有遵循聊天格式。

```py
def build_few_shot_prompt_wo_chat_template(system_prompt, content, few_shot_examples):
    """
    Builds the few-shot prompt using provided examples, bypassing the chat-template
    for Llama-2.

    Args:
        system_prompt (str): Task description for the LLM
        content (dict): The content for which to create a query, similar to the
            structure required by `create_query`.
        few_shot_examples (list of dict): Examples to simulate a hypothetical
            conversation. Each dict must have "options" and an "answer".

    Returns:
        str: few-shot prompt in non-chat format
    """
    few_shot_prompt = ""
    few_shot_prompt += "Task: " + system_prompt + "\n"
    for item in few_shot_examples:
        ans_options = item["options"]
        correct_ans_option = ""
        for key, value in ans_options.items():
            if value == item["answer"]:
                correct_ans_option = key
                break
        few_shot_prompt += create_query(item) + "\n" + "The answer is " + correct_ans_option + "." + "\n"

    few_shot_prompt += create_query(content) + "\n"
    return few_shot_prompt
```

我们的提示现在如下所示：

```py
Task: You will be provided with a medical or clinical question, along with multiple possible answer choices. Pick the right answer from the choices. 
Your response should be in the format "The answer is <correct_choice>". Do not add any other unnecessary content in your response
A 30-year-old woman presents to the clinic because of fever, joint pain, and a rash on her lower extremities. She admits to intravenous drug use. Physical examination reveals palpable petechiae and purpura on her lower extremities. Laboratory results reveal a negative antinuclear antibody, positive rheumatoid factor, and positive serum cryoglobulins. Which of the following underlying conditions in this patient is responsible for these findings?
Options:
A. Hepatitis B infection
B. Hepatitis C infection
C. HIV infection
D. Systemic lupus erythematosus (SLE)
The answer is B.
A 10-year-old child presents to your office with a chronic cough. His mother states that he has had a cough for the past two weeks that is non-productive along with low fevers of 100.5 F as measured by an oral thermometer. The mother denies any other medical history and states that he has been around one other friend who also has had this cough for many weeks. The patient's vitals are within normal limits with the exception of his temperature of 100.7 F. His chest radiograph demonstrated diffuse interstitial infiltrates. Which organism is most likely causing his pneumonia?
Options:
A. Mycoplasma pneumoniae
B. Staphylococcus aureus
C. Streptococcus pneumoniae
D. Streptococcus agalactiae
The answer is A.
A 44-year-old with a past medical history significant for human immunodeficiency virus infection presents to the emergency department after he was found to be experiencing worsening confusion. The patient was noted to be disoriented by residents and staff at the homeless shelter where he resides. On presentation he reports headache and muscle aches but is unable to provide more information. His temperature is 102.2°F (39°C), blood pressure is 112/71 mmHg, pulse is 115/min, and respirations are 24/min. Knee extension with hips flexed produces significant resistance and pain. A lumbar puncture is performed with the following results:

Opening pressure: Normal
Fluid color: Clear
Cell count: Increased lymphocytes
Protein: Slightly elevated

Which of the following is the most likely cause of this patient's symptoms?
Options:
A. Cryptococcus
B. Group B streptococcus
C. Herpes simplex virus
D. Neisseria meningitidis
The answer is C.
A 21-year-old male presents to his primary care provider for fatigue. He reports that he graduated from college last month and returned 3 days ago from a 2 week vacation to Vietnam and Cambodia. For the past 2 days, he has developed a worsening headache, malaise, and pain in his hands and wrists. The patient has a past medical history of asthma managed with albuterol as needed. He is sexually active with both men and women, and he uses condoms “most of the time.” On physical exam, the patient’s temperature is 102.5°F (39.2°C), blood pressure is 112/66 mmHg, pulse is 105/min, respirations are 12/min, and oxygen saturation is 98% on room air. He has tenderness to palpation over his bilateral metacarpophalangeal joints and a maculopapular rash on his trunk and upper thighs. Tourniquet test is negative. Laboratory results are as follows:

Hemoglobin: 14 g/dL
Hematocrit: 44%
Leukocyte count: 3,200/mm^3
Platelet count: 112,000/mm^3

Serum:
Na+: 142 mEq/L
Cl-: 104 mEq/L
K+: 4.6 mEq/L
HCO3-: 24 mEq/L
BUN: 18 mg/dL
Glucose: 87 mg/dL
Creatinine: 0.9 mg/dL
AST: 106 U/L
ALT: 112 U/L
Bilirubin (total): 0.8 mg/dL
Bilirubin (conjugated): 0.3 mg/dL

Which of the following is the most likely diagnosis in this patient?
Options:
A. Chikungunya
B. Dengue fever
C. Epstein-Barr virus
D. Hepatitis A
```

现在让我们评估Llama 2在这些提示下的表现，并观察它的表现如何：

```py
few_shot_llama_answers_wo_chat_template = []
for item in tqdm(questions):
    prompt = build_few_shot_prompt_wo_chat_template(PROMPT, item, few_shot_prompts)
    input_ids = tokenizer(prompt, return_tensors="pt", truncation=True).input_ids.cuda()
    outputs = model.generate(input_ids=input_ids, max_new_tokens=10, do_sample=False)
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    few_shot_llama_answers_wo_chat_template.append(gen_text.strip())

few_shot_llama_predictions_wo_chat_template = [parse_answer(x) for x in few_shot_llama_answers_wo_chat_template]
print(calculate_accuracy(ground_truth, few_shot_llama_predictions_wo_chat_template))
```

我们达到了36%的准确率。这个结果比我们之前的少量示例准确率低了6̶%̶ 4.3%。这进一步证明了我们之前的观点：根据用于微调我们打算使用的大型语言模型（LLM）的模板来构建提示是至关重要的。提示模板很重要！

## **用CoT提示进行Llama 2 7B-Chat的提示调优**

让我们通过评估CoT提示来结束。请记住，我们的数据集包含旨在通过美国医学执照考试（USMLE）测试医学知识的问题。这类问题通常需要既有事实回忆，也需要概念推理才能回答。因此，它是测试CoT效果的完美任务。

首先，我们必须提供一个CoT提示示例给模型，演示如何推理一个问题。为此，我们将使用谷歌MedPALM论文中的一个提示[12]。

![](../Images/e947fa6ba06d18bae65fe3354e5778aa.png)

用于评估MedPALM模型在MedQA数据集上的五-shot CoT提示。该提示借用自[12]中的表A.18，第41页 ([来源](https://arxiv.org/abs/2212.13138))。

我们使用这个五-shot提示词来评估模型。由于这个提示风格与之前的提示略有不同，我们再次创建一些辅助函数来处理这些提示词并获取输出。在使用CoT提示时，我们生成较长的输出令牌，以便让模型在回答问题之前“思考”和“推理”。

```py
def create_query_cot(item):
    """
    Creates the input for the model using the question and the multiple choice options in the CoT format.

    Args:
        item (dict): A dictionary containing the question and options.
            Expected keys are "question" and "options", where "options" is another
            dictionary with keys "A", "B", "C", and "D".

    Returns:
        str: A formatted query combining the question and options, ready for use.
    """
    query = "Question: " + item["question"] + "\n" + \
            "(A) " + item["options"]["A"] + " " +  \
            "(B) " + item["options"]["B"] + " " +  \
            "(C) " + item["options"]["C"] + " " +  \
            "(D) " + item["options"]["D"]
    return query

def build_cot_prompt(instruction, input_question, cot_examples):
    """
    Builds the few-shot prompt for the GPT API using provided examples.

    Args:
        content (dict): The content for which to create a query, similar to the
            structure required by `create_query`.
        few_shot_examples (list of dict): Examples to simulate a hypothetical
            conversation. Each dict must have "question" and an "explanation".

    Returns:
        list of dict: A list of messages, simulating a conversation with
            few-shot examples, followed by the current user query.
    """

    messages = [{"role": "system", "content": instruction}]
    for item in cot_examples:
        messages.append({"role": "user", "content": item["question"]})
        messages.append({"role": "assistant", "content": item["explanation"]})

    messages.append({"role": "user", "content": create_query_cot(input_question)})

    return messages

def parse_answer_cot(text):
    """
    Extracts the choice from a string that follows the pattern "Answer: (Choice) Text".

    Args:
    - text (str): The input string from which to extract the choice.

    Returns:
    - str: The extracted choice or a message indicating no match was found.
    """
    # Regex pattern to match the answer part
    pattern = r"Answer: (.*)"

    # Search for the pattern in the text and extract the matching group
    match = re.search(pattern, text)

    if match:
        if len(match.group(1)) > 1:
            return match.group(1)[1]
        else:
            return ""
    else:
        return ""
```

```py
cot_llama_answers = []
for item in tqdm(questions):
    cot_prompt = build_cot_prompt(COT_INSTRUCTION, item, COT_EXAMPLES)
    input_ids = tokenizer.apply_chat_template(cot_prompt, tokenize=True, return_tensors="pt").cuda() #modified on 09/03/2024
    # prompt = tokenizer.apply_chat_template(cot_prompt, tokenize=False)
    # input_ids = tokenizer(prompt, return_tensors="pt", truncation=True).input_ids.cuda()    
    outputs = model.generate(input_ids=input_ids, max_new_tokens=100, do_sample=False)
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    cot_llama_answers.append(gen_text.strip())

cot_llama_predictions = [parse_answer_cot(x) for x in cot_llama_answers]
print(calculate_accuracy(ground_truth, cot_llama_predictions))
```

我们在使用CoT提示词进行Llama 2–7B模型测试时，性能下降至2̶0̶%̶ 21.33%。这一结果与CoT论文[6]的发现大致一致，论文中提到CoT是LLM模型的一个涌现特性，且随着模型规模的增大，CoT的表现会得到改善。话虽如此，我们来分析为什么性能会急剧下降。

> **2024年09月03日编辑** — 与零-shot设置类似，我修正了发送到模型的提示词方式，调整为CoT设置下的新方式。我得到的新分数为21.33%，相比原先20%的得分，提升了约1.33%。我在这里和代码中进行了修正，以避免使用聊天模板时的任何混淆。本文中的发现和结论并未受到此次修正的影响。

## Llama 2中CoT的失败模式

我们抽取了一些Llama 2在测试集问题中的回答，用以分析错误案例：

> 这些CoT样本和图表的分析不受小“bug”修复的影响。我已经验证过，这些图表中使用的预测结果，在旧设置和新设置中是相同的。

![](../Images/bafde2d1cb6d802d5b8c1c826acfc01c.png)

示例预测1 — 模型得出一个答案，但没有遵循格式，导致解析结果困难。（作者提供的图片）

![](../Images/9192f54c14ae9038111571c6319de6cc.png)

示例预测2 — 模型未能遵循提示格式，也未能给出确凿的答案。（作者提供的图片）

虽然CoT提示词让模型在给出最终答案之前能够“思考”，但在大多数情况下，模型要么无法得出确定的答案，要么给出的答案格式与我们的示例不一致。我没有在这里分析的一种失败模式，但可能值得探索的是，检查测试集中的一些案例，模型可能“推理”错误，从而得到错误的答案。这超出了当前文章和我的医学知识的范围，但肯定是我以后打算重新审视的问题。

## 使用零-shot提示词提示GPT-3.5

让我们开始定义一些辅助函数，帮助我们处理这些输入以便利用GPT API。你需要生成一个API密钥才能使用GPT-3.5 API。你可以在Windows中使用以下命令设置API密钥：

`setx OPENAI_API_KEY "your-api-key-here"`

或在Linux中使用：

`export OPENAI_API_KEY "your-api-key-here"`

在当前使用的会话中。

```py
from openai import OpenAI
import re
from tqdm import tqdm

# assuming you have already set the secret key using env variable
# if not, you can also instantiate the OpenAI client by providing the 
# secret key directly like so:
# I highly recommend not doing this, as it is a best practice to not store
# the api key in your code directly or in any plain-text file for security 
# reasons.
# client = OpenAI(api_key = "")

client = OpenAI() 
```

```py
 def get_response(messages, model_name, temperature = 0.0, max_tokens = 10):
    """
    Obtains the responses/answers of the model through the chat-completions API.

    Args:
        messages (list of dict): The built messages provided to the API.
        model_name (str): Name of the model to access through the API
        temperature (float): A value between 0 and 1 that controls the randomness of the output.
        A temperature value of 0 ideally makes the model pick the most likely token, making the outputs (mostly) deterministic.
        max_tokens (int): Maximum number of tokens that the model should generate

    Returns:
        str: The response message content from the model.
    """
    response = client.chat.completions.create(
        model=model_name,
        messages=messages,
        temperature=temperature,
        max_tokens=max_tokens
    )
    return response.choices[0].message.content
```

这个函数现在构建了适用于 GPT-3.5 API 格式的提示词。我们可以通过该库提供的 chat-completions API 与 GPT-3.5 模型进行互动。该 API 要求消息按字典列表的格式发送给 API。每个消息必须指定角色和内容。关于***“system”***、***“user”***和***“assistant”***角色的约定与之前为 Llama-7B 聊天模型描述的相同。

现在让我们使用 GPT-3.5 API 处理测试集并获取响应。在收到所有响应后，我们从模型的响应中提取选项并计算准确率。

```py
zero_shot_gpt_answers = []
for item in tqdm(questions):
    zero_shot_prompt_messages = build_zero_shot_prompt(PROMPT, item)
    answer = get_response(zero_shot_prompt_messages, model_name = "gpt-3.5-turbo", temperature = 0.0, max_tokens = 10)
    zero_shot_gpt_answers.append(answer)

zero_shot_gpt_predictions = [parse_answer(x) for x in zero_shot_gpt_answers]
print(calculate_accuracy(ground_truth, zero_shot_gpt_predictions))
```

目前我们的性能为 63%。这比 Llama 2–7B 的性能有了显著提升。这并不令人惊讶，因为 GPT-3.5 可能比 Llama 2–7B 大得多，并且训练了更多的数据，另外 OpenAI 可能还对模型进行了其他专有优化。现在让我们看看少量示例提示的效果如何。

## 使用少量示例提示词提示 GPT-3.5

为了向大型语言模型提供少量示例，我们重用从训练集中采样的三个示例，并将它们附加到提示词中。对于 GPT-3.5，我们创建一个包含示例的消息列表，类似于我们之前处理 Llama 2 的方式。输入通过“user”角色附加，相关的选项通过“assistant”角色呈现。我们重用了之前构建少量示例提示词的函数。

> 这相当于为 GPT-3.5 创建一个虚构的多轮对话历史，每一轮都对应一个示例演示。

现在让我们使用 GPT-3.5 获取输出。

```py
few_shot_gpt_answers = []
for item in tqdm(questions):
    few_shot_prompt_messages = build_few_shot_prompt(PROMPT, item, few_shot_prompts)
    answer = get_response(few_shot_prompt_messages, model_name= "gpt-3.5-turbo", temperature = 0.0, max_tokens = 10)
    few_shot_gpt_answers.append(answer)

few_shot_gpt_predictions = [parse_answer(x) for x in few_shot_gpt_answers]
print(calculate_accuracy(ground_truth, few_shot_gpt_predictions))
```

我们通过少量示例提示将性能从 63% 提高到了 67%！这是一项显著的提升，突显了向模型提供任务演示的价值。

## 使用 CoT 提示词提示 GPT-3.5

现在让我们评估使用 CoT 提示词的 GPT-3.5。我们重新使用相同的 CoT 提示词并获取输出：

```py
cot_gpt_answers = []
for item in tqdm(questions):
    cot_prompt = build_cot_prompt(COT_INSTRUCTION, item, COT_EXAMPLES)
    answer = get_response(cot_prompt, model_name= "gpt-3.5-turbo", temperature = 0.0, max_tokens = 100)
    cot_gpt_answers.append(answer)

cot_gpt_predictions = [parse_answer_cot(x) for x in cot_gpt_answers]
print(calculate_accuracy(ground_truth, cot_gpt_predictions))
```

使用 CoT 提示词与 GPT-3.5 提示进行的结果准确率为 71%！这比少量示例提示提高了 4%。看起来在回答问题之前让模型“思考”是有益的，这也与论文[6]的发现一致，CoT 解锁了大参数模型的性能提升。

# 结论与收获：

提示是使用大型语言模型（LLMs）时的一个关键技能，理解提示工具包中的各种工具可以帮助根据上下文从LLMs中提取更好的性能。我希望这篇文章能作为一个广泛且（希望！）易于理解的介绍。然而，它并不旨在提供所有提示策略的全面概述。提示仍然是一个高度活跃的研究领域，许多方法被提出，如ReAct [13]、Tree-of-Thought提示[14]等。我建议探索这些技术，以更好地理解它们并增强你的提示工具包。

# 可重复性

在本文中，我的目标是尽可能使所有实验具备确定性并且可重复。我们使用贪婪解码来获得零样本、少量样本和CoT提示下的输出，使用Llama-2模型。虽然这些得分从技术上讲应该是可重复的，但在少数情况下，Cuda/GPU相关或库问题可能导致稍微不同的结果。

同样，在从GPT-3.5 API获取响应时，我们使用温度值为0来获得结果，并且在所有提示设置中仅选择下一个最可能的标记，而不进行采样。这使得结果[“大多数是确定性的”](https://platform.openai.com/docs/guides/text-generation/reproducible-outputs)，因此再次发送相同的提示给GPT-3.5可能会导致稍有不同的结果。

我已提供在所有提示设置下模型的输出，以及子采样的测试集、少量样本提示示例和CoT提示（来自MedPALM论文），用于重现本文中报告的得分。

# **参考文献：**

> 本文中提到的所有论文都在此列出。如果我遗漏了任何参考文献，请告诉我，我会添加它们！

[1] Yang, J., Jin, H., Tang, R., Han, X., Feng, Q., Jiang, H., … & Hu, X. (2023). 实践中驾驭LLMs的力量：关于ChatGPT及其以后的调查。*arXiv 预印本 arXiv:2304.13712*。

[2] Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., & Sutskever, I. (2019). 语言模型是无监督的多任务学习者。*OpenAI 博客*，*1*(8)，9。

[3] Brown, T., Mann, B., Ryder, N., Subbiah, M., Kaplan, J. D., Dhariwal, P., … & Amodei, D. (2020). 语言模型是少量学习者。*神经信息处理系统进展*，*33*，1877–1901。

[4] Wei, J., Bosma, M., Zhao, V. Y., Guu, K., Yu, A. W., Lester, B., … & Le, Q. V. (2021). 微调语言模型是零样本学习者。*arXiv 预印本 arXiv:2109.01652*。

[5] Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C., Mishkin, P., … & Lowe, R. (2022). 训练语言模型以根据人类反馈遵循指令。*神经信息处理系统进展*，*35*，27730–27744。

[6] Wei, J., Wang, X., Schuurmans, D., Bosma, M., Xia, F., Chi, E., … & Zhou, D. (2022). 连锁思维提示激发大型语言模型的推理能力. *神经信息处理系统进展*, *35*, 24824–24837.

[7] Thoppilan, R., De Freitas, D., Hall, J., Shazeer, N., Kulshreshtha, A., Cheng, H. T., … & Le, Q. (2022). Lamda: 面向对话应用的语言模型. *arXiv预印本 arXiv:2201.08239*.

[8] Chowdhery, A., Narang, S., Devlin, J., Bosma, M., Mishra, G., Roberts, A., … & Fiedel, N. (2023). Palm: 利用路径扩展语言建模. *机器学习研究期刊*, *24*(240), 1–113.

[9] Jin, D., Pan, E., Oufattole, N., Weng, W. H., Fang, H., & Szolovits, P. (2021). 这个病人得了什么病？来自医学考试的大规模开放域问答数据集. *应用科学*, *11*(14), 6421.

[10] Touvron, H., Martin, L., Stone, K., Albert, P., Almahairi, A., Babaei, Y., … & Scialom, T. (2023). Llama 2: 开放的基础和微调的聊天模型. *arXiv预印本 arXiv:2307.09288*.

[11] [https://platform.openai.com/docs/models/gpt-3-5-turbo](https://platform.openai.com/docs/models/gpt-3-5-turbo)

[12] Singhal, K., Azizi, S., Tu, T., Mahdavi, S. S., Wei, J., Chung, H. W., … & Natarajan, V. (2023). 大型语言模型编码临床知识. *自然*, *620*(7972), 172–180.

[13] Yao, S., Zhao, J., Yu, D., Du, N., Shafran, I., Narasimhan, K. R., & Cao, Y. (2022年9月). ReAct: 协同推理与行动在语言模型中的结合. 发表在 *第十一届国际学习表示大会* 上。

[14] Yao, S., Yu, D., Zhao, J., Shafran, I., Griffiths, T., Cao, Y., & Narasimhan, K. (2024). 思维树：使用大型语言模型进行深思熟虑的问题解决. *神经信息处理系统进展*, *36*.
