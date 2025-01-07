# 理解与实现Medprompt

> 原文：[https://towardsdatascience.com/understanding-and-implementing-medprompt-77bbd2777c91?source=collection_archive---------0-----------------------#2024-07-06](https://towardsdatascience.com/understanding-and-implementing-medprompt-77bbd2777c91?source=collection_archive---------0-----------------------#2024-07-06)

## 深入探讨提示框架背后的细节

[](https://medium.com/@anand.subu10?source=post_page---byline--77bbd2777c91--------------------------------)[![Anand Subramanian](../Images/096dc5504d6ada2493e0ac26959e60f0.png)](https://medium.com/@anand.subu10?source=post_page---byline--77bbd2777c91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--77bbd2777c91--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--77bbd2777c91--------------------------------) [Anand Subramanian](https://medium.com/@anand.subu10?source=post_page---byline--77bbd2777c91--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--77bbd2777c91--------------------------------) ·14分钟阅读·2024年7月6日

--

![](../Images/17dd8d46598db7fb9ab33ac9280bb740.png)

Medprompt策略的各个组成部分示意图（图片来自Medprompt论文中的图6 [1] ([https://arxiv.org/abs/2311.16452](https://arxiv.org/abs/2311.16452) ))

在我的 [第一篇博客文章](https://medium.com/towards-data-science/an-introduction-to-prompting-for-llms-61d36aec2048) 中，我探讨了提示（prompting）及其在大语言模型（LLMs）中的重要性。提示对于从LLMs获取高质量输出至关重要，因为它指导模型的响应并确保其与当前任务相关。在此基础上，当尝试使用LLMs解决实际问题时，常常会出现两个关键问题：仅凭提示能够将性能推向多远？以及何时需要做出决定，认为微调模型可能更加有效？

在做出关于利用提示的设计决策时，需要考虑多个因素。像少量示例提示和思维链（CoT）[2] 提示等技术可以帮助提高大多数任务的LLM性能。检索增强生成（RAG）管道可以通过适应新领域而无需微调，进一步提升LLM性能，同时提供对生成输出的可控性，减少幻觉现象。总体而言，我们有一套工具可以在不依赖微调的情况下，推动LLM性能的提升。

微调本身就伴随着一系列挑战和复杂性，包括对标注数据的需求以及与 LLM 训练和部署相关的成本。微调在某些情况下还可能增加 LLM 的幻觉 [3]。将这些因素汇总，我们可以看到，通过提示来优化 LLM 性能，在采取微调之前，确实具有重要的价值。

那么，我们该如何进行呢？在本文中，我们探讨了 Medprompt [1]，这是一种由微软推出的复杂提示策略。Medprompt 将少量样本提示、思维链提示和 RAG 原理结合起来，以提高 GPT-4 在医疗健康领域的表现，而无需任何领域特定的微调。

## 目录：

1.  [**MedPrompt 解释**](#90ba)

1.  [**MedPrompt 组件**](#d584)

1.  [**实现 MedPrompt**](#6d10)

1.  [**评估性能**](#b207)

1.  [**结论**](#b79b)

1.  [**参考文献**](#2755)

# MedPrompt 解释

LLMs 在各个领域展示了令人印象深刻的能力，尤其是在医疗健康领域。去年，Google 推出了 MedPaLM [4] 和 MedPaLM-2 [5]，这两款 LLM 不仅在医学多项选择题回答（MCQA）数据集上表现出色，还在开放性医学问题回答中具有竞争力，甚至超越了临床医生。这些模型通过指令微调和使用临床医生编写的思维链模板，特别针对医疗健康领域进行了优化，显著提升了它们的表现。

在此背景下，微软的论文《**通用基础模型能否超越专用调优？医学领域的案例研究**》[1] 提出了一个引人深思的问题：

> 是否可以在不依赖领域特定微调或专家编写资源的情况下，提升像 GPT-4 这样的通用模型在特定领域的表现？

作为本研究的一部分，论文介绍了**Medprompt**，一种创新的提示策略，不仅能提高模型的性能，还能超越像 MedPaLM-2 这样的专用模型。

![](../Images/56bd75bc7bca6dbb22de6d88744d8ff5.png)

各种 LLM 在医学知识基准测试中的比较。使用 Medprompt 的 GPT-4 在所有这些数据集上都优于 Med-PaLM 2。（来自 Medprompt 论文 [1] 的表格 1 图像 [https://arxiv.org/abs/2311.16452](https://arxiv.org/abs/2311.16452)）

使用 Medprompt 的 GPT-4 在所有医学 MCQA 基准测试中都超越了 Med-PaLM 2，而无需任何**领域特定的微调**。让我们来探索一下 Medprompt 的组件。

# Medprompt 组件

Medprompt 将少量样本提示、思维链提示（CoT）和检索增强生成（RAG）原理结合起来。具体来说，这个流程包含三个组件：

## 动态少量样本选择`

少量示例提示是指利用输入-输出对作为上下文来提示 LLM。如果这些少量示例是静态的，缺点是它们可能不是对新输入最相关的示例。**动态少量示例选择**，即 Medprompt 中的第一个组件，通过基于每个新任务输入选择少量示例来克服这一点。该方法通过在训练集上训练一个 K 最近邻（K-NN）算法，基于嵌入空间中的余弦相似度来检索与测试输入最相似的训练集示例。此策略高效地利用现有的训练数据集，为 LLM 检索相关的少量示例进行提示。

## 自生成的 CoT

正如论文 [1] 中所指出的，CoT 传统上依赖于手工制作的少量示例，这些示例包括详细的推理步骤，MedPaLM-2 就使用了这种由医学专业人员编写的提示。Medprompt 引入了**自生成的 CoT**，作为第二个模块，在该模块中，LLM 用来生成其推理过程的详细逐步解释，最终得出一个答案选择。通过自动生成每个训练数据点的 CoT 推理步骤，可以绕过手工制作示例的需求。为了确保仅保留具有推理步骤的正确预测，并过滤掉错误的回答，GPT-4 生成的答案会与真实答案进行交叉验证。

## 选择混洗集成

选择混洗集成技术是 Medprompt 引入的第三种技术。该技术旨在应对可能影响模型决策过程的固有偏见，特别是在多项选择设置中的位置偏见。答案选项的顺序会被打乱，并且这一过程会重复 k 次，以创建 k 个不同版本的相同问题，且每个版本的答案选项顺序不同。在推理过程中，每个版本都会生成一个答案，并对所有版本进行多数投票，以选择最终的预测选项。

## 这些组件如何在预处理和推理阶段中使用？

现在我们来看看 Medprompt 中的预处理和推理阶段。

## 预处理阶段

在预处理管道中，我们首先从训练数据集中提取每个问题，并在提示中加入详细的指令，以指导生成答案及其相关推理步骤。大语言模型（LLM）会被提示生成答案和推理步骤。在获取生成的回答后，我们通过将预测的答案与该特定问题的真实答案进行对比，来验证其准确性。

![](../Images/c963d79a34de678026ba379c9833720a.png)

Medprompt 预处理管道（图片由作者提供）

如果预测错误，我们将此实例从相关问题的数据库中排除。如果预测正确，我们将使用文本嵌入模型对问题进行嵌入。然后，我们将问题、问题嵌入、答案和思维链（CoT）推理存储到缓冲区中。一旦所有问题都处理完毕，我们利用这些嵌入来训练 KNN 模型。这个训练好的 KNN 模型作为我们 RAG 管道中的检索器，使我们能够高效地基于嵌入空间中的余弦相似度查询并检索 top-k 最相似的数据点。

**推理管道**

在推理阶段，我们首先使用文本嵌入模型对测试集中的每个问题进行嵌入。然后，我们使用 KNN 模型来识别最相似的 top-k 问题。对于每个检索到的数据点，我们可以访问自生成的思维链（CoT）推理和预测答案。我们将这些元素——问题、CoT 推理和答案——格式化为最终提示的少量示例。

![](../Images/71c38445d50f25f5fca81cb0d6352615.png)

Medprompt 推理管道（作者插图）

我们现在通过洗牌每个测试问题的答案选项顺序，进行 **选择洗牌集成**，从而创建相同问题的多个变体。然后，LLM 会被提示这些变体，以及相应的少量示例，以生成推理步骤和每个变体的答案。最后，我们对所有变体的预测结果进行多数投票，选择最终的预测。

# 实现 Medprompt

> 与此实现相关的代码可以在这个 [github repo 链接](https://github.com/anand-subu/blog_resources/tree/main/medprompt) 中找到。

我们使用 MedQA [6] 数据集来实现和评估 Medprompt。我们首先定义帮助函数来解析 jsonl 文件。

```py
def write_jsonl_file(file_path, dict_list):
    """
    Write a list of dictionaries to a JSON Lines file.

    Args:
    - file_path (str): The path to the file where the data will be written.
    - dict_list (list): A list of dictionaries to write to the file.
    """
    with open(file_path, 'w') as file:
        for dictionary in dict_list:
            json_line = json.dumps(dictionary)
            file.write(json_line + '\n')

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
```

## 实现自生成的 CoT

在我们的实现中，我们利用了 MedQA 的训练集。我们实现了一个零-shot CoT 提示，并处理了所有的训练问题。我们在实现中使用了 **GPT-4o**。对于每个问题，我们生成 CoT 和相应的答案。我们定义了一个基于 Medprompt 论文中提供的模板的提示。

```py
system_prompt = """You are an expert medical professional. You are provided with a medical question with multiple answer choices.
Your goal is to think through the question carefully and explain your reasoning step by step before selecting the final answer.
Respond only with the reasoning steps and answer as specified below.
Below is the format for each question and answer:

Input:
## Question: {{question}}
{{answer_choices}}

Output:
## Answer
(model generated chain of thought explanation)
Therefore, the answer is [final model answer (e.g. A,B,C,D)]"""
```

```py
def build_zero_shot_prompt(system_prompt, question):
    """
    Builds the zero-shot prompt.

    Args:
        system_prompt (str): Task Instruction for the LLM
        content (dict): The content for which to create a query, formatted as
            required by `create_query`.

    Returns:
        list of dict: A list of messages, including a system message defining
            the task and a user message with the input question.
    """
    messages = [{"role": "system", "content": system_prompt},
                {"role": "user", "content": create_query(question)}]
    return messages

def build_few_shot_prompt(system_prompt, question, examples, include_cot=True):
    """
    Builds the few-shot prompt.

    Args:
        system_prompt (str): Task Instruction for the LLM
        content (dict): The content for which to create a query, formatted as
            required by `create_query`.

    Returns:
        list of dict: A list of messages, including a system message defining
            the task and a user message with the input question.
    """
    messages = [{"role": "system", "content": system_prompt}]

    for elem in examples:
        messages.append({"role": "user", "content": create_query(elem)})
        if include_cot:
            messages.append({"role": "assistant", "content": format_answer(elem["cot"], elem["answer_idx"])})        
        else:           
            answer_string = f"""## Answer\nTherefore, the answer is {elem["answer_idx"]}"""
            messages.append({"role": "assistant", "content": answer_string})

    messages.append({"role": "user", "content": create_query(question)})
    return messages

def get_response(messages, model_name, temperature = 0.0, max_tokens = 10):
    """
    Obtains the responses/answers of the model through the chat-completions API.

    Args:
        messages (list of dict): The built messages provided to the API.
        model_name (str): Name of the model to access through the API
        temperature (float): A value between 0 and 1 that controls the randomness of the output.
        A temperature value of 0 ideally makes the model pick the most likely token, making the outputs deterministic.
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

我们还定义了帮助函数，用于解析 LLM 响应中的推理和最终答案选项。

```py
def matches_ans_option(s):
    """
    Checks if the string starts with the specific pattern 'Therefore, the answer is [A-Z]'.

    Args:
    s (str): The string to be checked.

    Returns:
    bool: True if the string matches the pattern, False otherwise.
    """
    return bool(re.match(r'^Therefore, the answer is [A-Z]', s))

def extract_ans_option(s):
    """
    Extracts the answer option (a single capital letter) from the start of the string.

    Args:
    s (str): The string containing the answer pattern.

    Returns:
    str or None: The captured answer option if the pattern is found, otherwise None.
    """
    match = re.search(r'^Therefore, the answer is ([A-Z])', s)
    if match:
        return match.group(1)  # Returns the captured alphabet
    return None 

def matches_answer_start(s):
    """
    Checks if the string starts with the markdown header '## Answer'.

    Args:
    s (str): The string to be checked.

    Returns:
    bool: True if the string starts with '## Answer', False otherwise.
    """
    return s.startswith("## Answer")

def validate_response(s):
    """
    Validates a multi-line string response that it starts with '## Answer' and ends with the answer pattern.

    Args:
    s (str): The multi-line string response to be validated.

    Returns:
    bool: True if the response is valid, False otherwise.
    """
    file_content = s.split("\n")

    return matches_ans_option(file_content[-1]) and matches_answer_start(s)

def parse_answer(response):
    """
    Parses a response that starts with '## Answer', extracting the reasoning and the answer choice.

    Args:
    response (str): The multi-line string response containing the answer and reasoning.

    Returns:
    tuple: A tuple containing the extracted CoT reasoning and the answer choice.
    """
    split_response = response.split("\n")
    assert split_response[0] == "## Answer"
    cot_reasoning = "\n".join(split_response[1:-1]).strip()
    ans_choice = extract_ans_option(split_response[-1])
    return cot_reasoning, ans_choice
```

我们现在处理 MedQA 训练集中的问题。我们获取所有问题的 CoT 响应和答案，并将它们存储到一个文件夹中。

```py
train_data = read_jsonl_file("data/phrases_no_exclude_train.jsonl")

cot_responses = []
os.mkdir("cot_responses")

for idx, item in enumerate(tqdm(train_data)):
    prompt = build_zero_shot_prompt(system_prompt, item)
    try:
        response = get_response(prompt, model_name="gpt-4o", max_tokens=500)
        cot_responses.append(response)
        with open(os.path.join("cot_responses", str(idx) + ".txt"), "w", encoding="utf-8") as f:
            f.write(response)           
    except Exception as e :
        print(str(e))
        cot_responses.append("")
```

我们现在遍历所有生成的响应，以检查它们是否有效并符合提示中定义的预测格式。我们丢弃那些不符合要求格式的响应。之后，我们将每个问题的预测答案与真实答案进行比对，只保留那些预测答案与真实答案匹配的问题。

```py
questions_dict = []
ctr = 0
for idx, question in enumerate(tqdm(train_data)):
    file = open(os.path.join("cot_responses/", str(idx) + ".txt"), encoding="utf-8").read()
    if not validate_response(file):
        continue

    cot, pred_ans = parse_answer(file)

    dict_elem = {}
    dict_elem["idx"] = idx
    dict_elem["question"] = question["question"]
    dict_elem["answer"] = question["answer"]
    dict_elem["options"] = question["options"]
    dict_elem["cot"] = cot
    dict_elem["pred_ans"] = pred_ans
    questions_dict.append(dict_elem)        

filtered_questions_dict = []
for item in tqdm(questions_dict):
    pred_ans = item["options"][item["pred_ans"]]
    if pred_ans == item["answer"]:
        filtered_questions_dict.append(item)
```

## 实现 KNN 模型

在处理了训练集并获得了所有这些问题的 CoT 响应后，我们现在使用 OpenAI 的 **text-embedding-ada-002** 嵌入所有问题。

```py
def get_embedding(text, model="text-embedding-ada-002"):
    return client.embeddings.create(input = [text], model=model).data[0].embedding

for item in tqdm(filtered_questions_dict):
    item["embedding"] = get_embedding(item["question"])
    inv_options_map = {v:k for k,v in item["options"].items()}
    item["answer_idx"] = inv_options_map[item["answer"]] 
```

我们现在使用这些问题的嵌入训练一个 KNN 模型。该模型在推理时充当检索器，帮助我们从训练集中检索出与测试集中的问题最相似的数据点。

```py
import numpy as np
from sklearn.neighbors import NearestNeighbors

embeddings = np.array([d["embedding"] for d in filtered_questions_dict])
indices = list(range(len(filtered_questions_dict)))

knn = NearestNeighbors(n_neighbors=5, algorithm='auto', metric='cosine').fit(embeddings)
```

## 实现动态少样本和选项洗牌集成逻辑

我们现在可以进行推理。我们从 MedQA 测试集中子抽取 500 个问题进行评估。对于每个问题，我们使用 KNN 模块从训练集中检索 5 个最相似的问题，并获取它们各自的 CoT 推理步骤和预测答案。然后，我们构建一个少样本提示来使用这些示例。

对于每个问题，我们还会将选项的顺序洗牌 5 次，以创建不同的变体。然后，我们利用构造的少样本提示，通过每个变体的洗牌选项获取预测答案。

```py
def shuffle_option_labels(answer_options):
    """
    Shuffles the options of the question.

    Parameters:
    answer_options (dict): A dictionary with the options.

    Returns:
    dict: A new dictionary with the shuffled options.
    """
    options = list(answer_options.values())
    random.shuffle(options)
    labels = [chr(i) for i in range(ord('A'), ord('A') + len(options))]
    shuffled_options_dict = {label: option for label, option in zip(labels, options)}

    return shuffled_options_dict
```

```py
test_samples = read_jsonl_file("final_processed_test_set_responses_medprompt.jsonl")

for question in tqdm(test_samples, colour ="green"):
    question_variants = []
    prompt_variants = []
    cot_responses = []
    question_embedding = get_embedding(question["question"])
    distances, top_k_indices = knn.kneighbors([question_embedding], n_neighbors=5)
    top_k_dicts = [filtered_questions_dict[i] for i in top_k_indices[0]]
    question["outputs"] = []

    for idx in range(5):
        question_copy = question.copy()
        shuffled_options = shuffle_option_labels(question["options"])
        inv_map = {v:k for k,v in shuffled_options.items()}

        question_copy["options"] = shuffled_options
        question_copy["answer_idx"] = inv_map[question_copy["answer"]]
        question_variants.append(question_copy)
        prompt = build_few_shot_prompt(system_prompt,  question_copy, top_k_dicts)
        prompt_variants.append(prompt)

    for prompt in tqdm(prompt_variants):
        response = get_response(prompt, model_name="gpt-4o", max_tokens=500)
        cot_responses.append(response)

    for question_sample, answer in zip(question_variants, cot_responses):
        if validate_response(answer):
            cot, pred_ans = parse_answer(answer)

        else:
            cot = ""
            pred_ans = ""

        question["outputs"].append({"question": question_sample["question"], "options": question_sample["options"], "cot": cot, "pred_ans": question_sample["options"].get(pred_ans, "")})
```

我们现在评估 Medprompt 在测试集上的表现。对于每个问题，我们通过集成逻辑生成五个预测。我们取每个问题的众数，即出现频率最高的预测，作为最终预测并进行评估。这里可能出现两个极端情况：

1.  两个不同的答案选项被预测了两次，但没有明确的赢家。

1.  生成的响应存在错误，意味着我们没有预测的答案选项。

对于这两种极端情况，我们认为 LLM 对这些问题的回答是错误的。

```py
def find_mode_string_list(string_list):
    """
    Finds the most frequently occurring strings.

    Parameters:
    string_list (list of str): A list of strings.
    Returns:
    list of str or None: A list containing the most frequent string(s) from the input list.
                         Returns None if the input list is empty.
    """    
    if not string_list:
        return None  

    string_counts = Counter(string_list)
    max_freq = max(string_counts.values())
    mode_strings = [string for string, count in string_counts.items() if count == max_freq]
    return mode_strings

ctr = 0 
for item in test_samples:
    pred_ans = [x["pred_ans"] for x in item["outputs"]]
    freq_ans = find_mode_string_list(pred_ans)

    if len(freq_ans) > 1:
        final_prediction = ""
    else:
        final_prediction = freq_ans[0]

    if final_prediction == item["answer"]:
        ctr +=1

print(ctr / len(test_samples))
```

# 性能评估

我们评估了 Medprompt 在 MedQA 测试子集上与 GPT-4o 相关的准确性表现。此外，我们还基准测试了零样本提示、随机少样本提示和随机少样本 CoT 提示的表现。

![](../Images/5689439bdcd82036f6539f1e4fbd3b44.png)

我们的评估结果（图片由作者提供）

我们观察到，Medprompt 和随机少样本 CoT 提示比零样本和少样本提示基准表现更好。然而，令人惊讶的是，我们注意到随机少样本 CoT 的表现超出了我们的 Medprompt 表现。这可能是由于以下几个原因：

1.  原始的 Medprompt 论文基准测试了 GPT-4 的表现。我们观察到，GPT-4o 在各种文本基准测试中显著优于 GPT-4T 和 GPT-4（[https://openai.com/index/hello-gpt-4o/](https://openai.com/index/hello-gpt-4o/)），这表明 Medprompt 在像 GPT-4o 这样的更强模型上的影响可能较小。

1.  我们将评估限制在从 MedQA 中子抽取的 500 个问题。Medprompt 论文评估了其他医学 MCQA 数据集以及 MedQA 的完整版。对 GPT-4o 进行完整版数据集的评估可能能更全面地展示整体表现。

# 结论

Medprompt 是一个有趣的框架，用于创建复杂的提示管道，特别是在不需要微调的情况下将通用的 LLM 调整到特定领域。它还突出了在各种使用场景中在提示和微调之间做出选择时需要考虑的因素。探索提示能够推动 LLM 性能提升的极限非常重要，因为这为微调提供了一种高效且低成本的替代方案。

# **参考文献：**

[1] Nori, H., Lee, Y. T., Zhang, S., Carignan, D., Edgar, R., Fusi, N., … & Horvitz, E. (2023). 通用基础模型能否超越专用微调？医学领域的案例研究。*arXiv 预印本 arXiv:2311.16452*。([https://arxiv.org/abs/2311.16452](https://arxiv.org/abs/2311.16452))

[2] Wei, J., Wang, X., Schuurmans, D., Bosma, M., Xia, F., Chi, E., … & Zhou, D. (2022). 思维链提示引发大型语言模型的推理。*神经信息处理系统进展*，*35*，24824–24837\. ([https://openreview.net/pdf?id=_VjQlMeSB_J](https://openreview.net/pdf?id=_VjQlMeSB_J))

[3] Gekhman, Z., Yona, G., Aharoni, R., Eyal, M., Feder, A., Reichart, R., & Herzig, J. (2024). 在新知识上微调 LLM 是否会鼓励幻觉？*arXiv 预印本 arXiv:2405.05904*。([https://arxiv.org/abs/2405.05904](https://arxiv.org/abs/2405.05904))

[4] Singhal, K., Azizi, S., Tu, T., Mahdavi, S. S., Wei, J., Chung, H. W., … & Natarajan, V. (2023). 大型语言模型编码临床知识。*自然*，*620*(7972)，172–180\. ([https://www.nature.com/articles/s41586-023-06291-2](https://www.nature.com/articles/s41586-023-06291-2))

[5] Singhal, K., Tu, T., Gottweis, J., Sayres, R., Wulczyn, E., Hou, L., … & Natarajan, V. (2023). 通过大型语言模型实现专家级医学问答。*arXiv 预印本 arXiv:2305.09617*。([https://arxiv.org/abs/2305.09617](https://arxiv.org/abs/2305.09617))

[6] Jin, D., Pan, E., Oufattole, N., Weng, W. H., Fang, H., & Szolovits, P. (2021). 这个病人得了什么病？一个来自医学考试的大规模开放领域问答数据集。*应用科学*，*11*(14)，6421\. ([https://arxiv.org/abs/2009.13081](https://arxiv.org/abs/2009.13081))（原始数据集发布在 MIT 许可下）
