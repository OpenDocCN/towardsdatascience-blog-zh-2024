# 使用 Llama 3.1 创建合成数据集，以微调你的 LLM

> 原文：[https://towardsdatascience.com/create-a-synthetic-dataset-using-llama-3-1-405b-for-instruction-fine-tuning-9afc22fb6eef?source=collection_archive---------2-----------------------#2024-08-07](https://towardsdatascience.com/create-a-synthetic-dataset-using-llama-3-1-405b-for-instruction-fine-tuning-9afc22fb6eef?source=collection_archive---------2-----------------------#2024-08-07)

## 使用庞大的 Llama 3.1 405B 和 Nvidia Nemotron 4 奖励模型来创建用于指令微调的合成数据集。

[](https://medium.com/@itshesamsheikh?source=post_page---byline--9afc22fb6eef--------------------------------)[![Hesam Sheikh](../Images/b8d5f4f285eef77634e4c1d4321580ed.png)](https://medium.com/@itshesamsheikh?source=post_page---byline--9afc22fb6eef--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9afc22fb6eef--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9afc22fb6eef--------------------------------) [Hesam Sheikh](https://medium.com/@itshesamsheikh?source=post_page---byline--9afc22fb6eef--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9afc22fb6eef--------------------------------) ·9 分钟阅读·2024年8月7日

--

![](../Images/d59b24212f7745714c66c7591192d4a8.png)

由 AI 使用 Leonardo.AI 创建

数据是 AI 的核心，尽管它是宝贵的资产，但我们知道开发高质量数据集是多么具有挑战性和成本高昂。一个精心策划和过滤的数据集可以弥补模型复杂度的不足。对于大型语言模型来说也是如此，较小的模型通过利用良好的数据往往能优于更大的 LLM。

在本文中，我们将探讨如何使用**Llama 3.1 405B**来创建一个**自然语言中的 git 命令**合成数据集。我将展示如何在不需要并行运行数十个 GPU 的情况下，使用这款405B巨兽。拥有初步的指令和响应数据集后，我们将使用**Nvidia 的 Nemotron 4**作为奖励模型，过滤掉任何不良的提示/响应对。最后，我们将把这个数据集推送到 HuggingFace，以便稍后对我们的 LLM 进行微调。

这将是快速、免费的，并且让你掌握更多的控制权。

我将保持这篇文章简洁且充满干货，因此请确保**阅读到最后**，并熟悉这一重要技能。

# 🦙 为什么选择 Llama 3.1

Meta 在发布最新一代 LLM 家族后，牢牢占据了市场的份额，[Llama 3.1](https://huggingface.co/collections/meta-llama/llama-31-669fc079a0c406a149a5738f)。这个新家族包括了升级版的 8B 和 70B 模型，具有更强的推理能力，并推出了庞大的 405B 模型。

![](../Images/cbadf588e2561f45221657f93c764fa2.png)

Llama 3.1 405 在成功接近最佳闭源模型的基准方面取得了显著成就。（图表由 [Maxime Labonne](https://medium.com/u/dc89da634938?source=post_page---user_mention--9afc22fb6eef--------------------------------) 提供，已获许可）

Llama 3.1 405B 不仅在规模上令人印象深刻，而且通过缩小闭源和开源模型之间的差距，比以往任何时候都更加出色（见上图）。

405B 模型的这一能力使其非常适合一些最重要且最微妙的工作流，例如检索增强生成（RAG）、监督微调（SFT），以及最重要的**合成数据生成**。

## 为什么选择合成数据？

合成数据是通过使用人工模型再现真实世界数据的特征和特性来创建的。在某些时候，当你需要的数据超过你现有的数据时，你将需要使用它*。

我们的自然语言中 Git 命令数据集的示例可以完美地展示这一点。如果我们想要创建一个应用程序，它以用户需求为输入，然后为其建议正确的 git 命令，那么在这个应用程序的核心，我们需要一个专家级的 LLM。我们可以使用 GPT-4o 或 Claude，并且很可能会获得不错的结果。但问题在于成本。因此，另一种选择是**微调**一个小型语言模型（SML），例如 Llama 3.1 8B 或 Gemma 2 2B（我将在后续帖子中详细介绍）。

你猜我们为微调需要什么吗……数据！

由于我没有找到适合此任务的数据集，我们只有一个解决方案：使用 Llama 3.1 405B 合成创建我们的数据集。

# 🛠️ 构建数据集

为了使用 AI 构建一个合成数据集，我们将使用以下大纲。你可以选择任何我所选择的其他 LLM。

![](../Images/07a1f36079f8e08fdc8b2bb76c584e37.png)

我们创建合成数据集的大纲。（作者提供）

## 设置 API 密钥

我们将使用**Nvidia NIM API**来利用这些大型 LLM，而无需在本地运行它们。像 Llama 3.1 405B 这样的模型在设备上运行通常需要多个 H100 GPU，除非你在拥有这些资源的组织中工作，否则你需要使用外部 API。

要访问你的免费 Nvidia 积分，请访问 [Llama 3.1 on Nvidia NIM](https://build.nvidia.com/explore/discover#llama-3_1-405b-instruct)，然后点击**获取 API 密钥**。这将是我们在代码中或 `.env` 文件中使用的内容。一旦我们获得 API 密钥，就可以设置与 Nvidia 服务器的连接，以远程使用这些模型。

```py
client = OpenAI(
    base_url="https://integrate.api.nvidia.com/v1",
    api_key=os.environ["NVIDIA_API_KEY"]
)
MODEL = "meta/llama-3.1-405b-instruct"
```

## 生成子主题

理想情况下，我们希望我们的数据集能够尽可能涵盖各种场景和情况。一种确保这一点的方法是定义**子主题**，并要求 Llama 3.1 为每个子主题提供指令/响应对。我们可以自己选择这些子主题，也可以让 LLM 来决定。我在以下代码片段中采用了第二种方法。

```py
n_subtopics = 5

TOPIC_GENERATION_PROMPT_TEMPLATE = """\
I want to create a synthetic dataset of natural language and Git commands. Based on this context, give me {n_subtopics} subtopics
to cover what needs to be covered when working with Git. 

The list must be without numbers, and without any description of the subtopics. The subtopics should be separated by a comma. There must be no other text than the list.
"""

def generate_subtopics(client, n_subtopics):
    prompt = TOPIC_GENERATION_PROMPT_TEMPLATE.format(n_subtopics=n_subtopics)
    response = client.chat.completions.create(
        model=MODEL,
        messages=[
            {"role": "user",
             "content": prompt}
        ],
        temperature=0.2,
        top_p=0.7,
    )
    return response

responses = generate_subtopics(client, n_subtopics=n_subtopics)
print(responses.choices[0].message.content)
```

LLM建议了五个主题：分支、合并、提交、远程仓库和解决冲突。似乎这是一个合理的主题选择。

## 生成指令

在有五个子主题关于Git的工作时，我们需要Llama 3.1生成一组关于每个子主题的指令（或提示）。我要求每个主题生成一百条指令，因此理想情况下，我应该得到500条提示。

需要记住的一点是，当要求生成*N*条指令时：即使是这样一个大型模型，也很少会返回正好等于你想要的数量。

最终，我为五个子主题得到了总共335条指令，这与500条有很大差距。虽然有方法可以确保这种情况不发生，但为了简化起见，我们就不再深入讨论了。

```py
n_instructions = 100

INSTRUCTION_PROMPT_TEMPLATE = """\
The objective is to create a dataset of user instructions in natural language that should be returned by Git commands.
Given a topic in Git, generate {n_instructions} possible concise instructions that could be given to an AI assitant about that topic.
Write some of these instructions as if given by someone with limited knowledge of Git terminologies and knowledge, 
like a beginner programmer. Your response should be in a list format.

The topic is: {sub_topic}
The list must be without numbers. The questions/instructions should be separated by a newline character. There must be no other text than the list.
"""
subtopic_list = responses.choices[0].message.content.split(",")
def generate_instructions(client, sub_topic, n_instructions):
    print(f"Generating Instructions for {sub_topic}.")
    prompt = INSTRUCTION_PROMPT_TEMPLATE.format(sub_topic=sub_topic, n_instructions=n_instructions)
    response = client.chat.completions.create(
        model=MODEL,
        messages=[
            {"role": "user",
             "content": prompt}
        ],
        temperature=0.2,
        top_p=0.7,
    )
    return response.choices[0].message.content

def instructions_generator(client, subtopic_list, n_instructions):
    instruction_list = [generate_instructions(client, subtopic, n_instructions) for subtopic in subtopic_list]
    return instruction_list

instruction_list = instructions_generator(client, subtopic_list, n_instructions)

instruction_list_formatted = []
for instruction_set in instruction_list:
    instruction_list_formatted.extend([instruction.strip() for instruction in instruction_set.split("\n") if instruction])
print(instruction_list_formatted)
```

下面是一些生成的指令示例：

```py
'Make a branch that I can merge back into the main branch',
'I want to make a branch that is based on an older version of the code',
'Can you show me a log of all commits that have been made to the repository this year?',
```

## 响应生成

对于每一条提供的指令，我们还将要求给出响应。正如下面的代码片段所示，我特别要求我的响应是*切题、有信息量且简洁的*。到最后，我将得到一对`指令`和`响应`的列表。

```py
RESPONSE_PROMPT_TEMPLATE = """\
Given an question/instruction related to Git, generate a response that could be given. 
Keep the response on-topic, informative, concise.

The user prompt is: {instruction}
"""
def generate_responses(client, instruction):
    prompt = RESPONSE_PROMPT_TEMPLATE.format(instruction=instruction)
    response = client.chat.completions.create(
        model=MODEL,
        messages=[
            {"role": "user",
             "content": prompt}
        ],
        temperature=0.2,
        top_p=0.7,
        max_tokens=1024,
    )
    return response.choices[0].message.content

def response_generator(client, instruction_list):
    response_list = [generate_responses(client, instruction) for instruction in instruction_list]
    return response_list

instruction_response_list = response_generator(client, instruction_list_formatted)
instruction_response_pair_list = []
for instruction, response in zip(instruction_list_formatted, instruction_response_list):
    instruction_response_pair_list.append(
        {
            "instruction": instruction,
            "responses": response,
        }
    )
```

```py
{"instruction": "Can you make a branch for me and name it \"new-feature\"", 
"responses": "To create a new branch named \"new-feature\", you can use the following Git command:\n\n`git branch new-feature`\n\nThis will create a new branch with the specified name. If you want to switch to the new branch immediately, you can use:\n\n`git checkout -b new-feature`\n\nThis will create the branch and switch to it in one step."}
```

## 使用Nemotron 4筛选响应

即使我们拥有了指令/响应对，并不是所有的响应都是高质量的。它们可能冗长、复杂或者是错误的。这时，Nvidia的[**Nemotron 4 340B奖励**](https://huggingface.co/nvidia/Nemotron-4-340B-Reward)模型就派上用场了。它正是为我们的用例设计的，因为据Nvidia所说，它*“可以作为合成数据生成管道的一部分，用于生成训练数据，帮助研究人员和开发者构建自己的大型语言模型（LLMs）。”*

![](../Images/68716e2a3af1320dfe4da92f01e82d06.png)

Nemotron 4的示例使用（由作者提供）

我们将把每一对指令/响应交给Nemotron 4，并获得五个分数，范围从0到4。这五个分数是*有用性、正确性、一致性、复杂性和冗长性*。为了使用该模型，我将首先定义一个简单的函数，将指令和响应传递给模型，并以字典的形式接收五个分数。

```py
def get_scores_from_response(score_response_template):
    logprobs = score_response_template.choices[0].logprobs.content
    score_dict = {}
    for score in logprobs:
        score_dict[score.token] = score.logprob
    return score_dict

def get_response_and_scores(client, model, question, response_content):
    messages = [
        {
            "role": "user",
            "content": question
        },
        {
            "role": "assistant",
            "content": response_content
        }
    ]
    response = client.chat.completions.create(
        model=model,
        messages=messages,
    )
    scores = get_scores_from_response(response)
    return scores
```

一旦我们为数据集中的每一行得到了分数，就可以根据提供的五个标准对数据集进行筛选。我将根据**有用性**和**冗长性**筛选出不良响应，因为我希望我的回答简洁且富有信息。

```py
helpfulness_THRESHOLD = 3
verbosity_THRESHOLD = 2.5
synthetic_data = [data for i, data in enumerate(synthetic_data) 
                  if not (score_list[i]["helpfulness"] < helpfulness_THRESHOLD or 
                          score_list[i]["verbosity"] > verbosity_THRESHOLD)]
```

## 将数据集推送到HuggingFace

最后，一旦你拥有了完成的数据集，最好将其推送到HuggingFace，以便以后使用或与其他开发者分享。为此，首先登录HuggingFace并提供一个**令牌**，具体步骤可参考登录页面提供的链接。

```py
from huggingface_hub import login
login()
```

然后，你可以加载保存的数据集并将其上传到你的HuggingFace页面。

```py
with open(f'synthetic_data_filtered.jsonl', 'r') as f:
    data = [json.loads(line) for line in f]
dataset = Dataset.from_list(data)
dataset_dict = DatasetDict({"train": dataset})
dataset_dict.push_to_hub("hesamsheikh/git-prompt")
```

恭喜 🏆！到目前为止，你已经能够使用 Llama 3.1 创建一个指令和回答的数据集，并且使用 Nemotron 4 对数据集进行优化并过滤掉不好的回答。最后，我们看到将数据集推送到 HuggingFace 是如此轻松。[从一个主题创建合成数据集用于指令微调](https://www.youtube.com/watch?v=FAdRMVAWiak) 也是本文的一个重要灵感来源，如果你喜欢这个主题，我建议你观看它。

这里还有**代码仓库**，你可以在其中找到我使用的完整代码。如果你查看了它，别忘了**给仓库加星**⭐。

[***使用 Llama 3.1 405B 和 Nemotron 4 创建合成数据集***](https://github.com/hesamsheikh/dataset_git_commands)

T***感谢你*** *阅读本文！* 如果你认为需要任何修改，请分享你的意见和建议。

## 让我们保持联系！

*免费订阅以获取新文章的通知！你也可以在* [*LinkedIn*](https://www.linkedin.com/in/hesamsheikh/) *和* [*Twitter*](https://x.com/itsHesamSheikh)*找到我。*

[](https://medium.com/@itshesamsheikh/subscribe?source=post_page-----9afc22fb6eef--------------------------------) [## 每当 Hesam Sheikh 发布新文章时，您将收到电子邮件通知。

### 每当 Hesam Sheikh 发布新文章时，你将收到电子邮件通知。通过注册，如果你还没有 Medium 账户，你将创建一个账户……

medium.com](https://medium.com/@itshesamsheikh/subscribe?source=post_page-----9afc22fb6eef--------------------------------)

# 深入阅读

如果你已经阅读到这里，你可能会对以下文章感兴趣：

[](/what-we-still-dont-understand-about-machine-learning-699e0002a057?source=post_page-----9afc22fb6eef--------------------------------) [## 我们仍然不理解的机器学习

### 机器学习中的未知问题，研究人员一直在努力理解——从批量归一化到 SGD 隐藏的内容

towardsdatascience.com](/what-we-still-dont-understand-about-machine-learning-699e0002a057?source=post_page-----9afc22fb6eef--------------------------------) [](/a-comprehensive-guide-to-collaborative-ai-agents-in-practice-1f4048947d9c?source=post_page-----9afc22fb6eef--------------------------------) [## 实践中的协作 AI 代理全面指南

### 定义，并建立一个能够优化你简历和求职信的代理团队

towardsdatascience.com](/a-comprehensive-guide-to-collaborative-ai-agents-in-practice-1f4048947d9c?source=post_page-----9afc22fb6eef--------------------------------)
