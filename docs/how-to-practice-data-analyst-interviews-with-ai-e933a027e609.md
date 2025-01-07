# 如何用 AI 练习数据分析师面试

> 原文：[https://towardsdatascience.com/how-to-practice-data-analyst-interviews-with-ai-e933a027e609?source=collection_archive---------12-----------------------#2024-08-12](https://towardsdatascience.com/how-to-practice-data-analyst-interviews-with-ai-e933a027e609?source=collection_archive---------12-----------------------#2024-08-12)

## 使用大型语言模型（LLMs）生成合成数据和代码

[](https://medium.com/@mathewnxwang?source=post_page---byline--e933a027e609--------------------------------)[![Mathew Wang](../Images/487d76ba764d66af4f4bdd72f395d58f.png)](https://medium.com/@mathewnxwang?source=post_page---byline--e933a027e609--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e933a027e609--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e933a027e609--------------------------------) [Mathew Wang](https://medium.com/@mathewnxwang?source=post_page---byline--e933a027e609--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e933a027e609--------------------------------) ·阅读时间 8 分钟·2024年8月12日

--

![](../Images/94cdfdd742c1f83f94ea057bf6d28d57.png)

图片来源：[Scott Graham](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 简介

我最近一直在做一些周末的 LLM 项目。在考虑要做什么时，两个想法突然浮现：

1.  **与软件工程和产品管理等其他职位相比，练习数据分析面试的资源较少**。在我准备第一次数据分析师面试时，我依赖于行业中的朋友，自己编写 SQL 和 Python 面试问题。

1.  **大型语言模型（LLMs）在生成合成数据集和编写代码方面非常擅长。**

于是，我构建了 **AI 数据分析面试官**，它可以自动生成独特的数据集，并为你生成 Python 面试问题供你解决！

本文概述了其工作原理和技术实现。你可以在 [这里](https://github.com/mathewnxwang/data_analysis_interview_tool/tree/main)查看该项目的代码库。

# 演示

当我启动这个网页应用时，系统会提示我提供关于我想要练习的面试类型的详细信息，具体是公司和数据集描述。假设我正在面试 Uber 的数据分析师职位，重点分析乘车数据：

![](../Images/75aed9c03ef8ff4d364477cc0e47299a.png)

在点击提交并等待 GPT 执行其魔法后，我收到了 AI 生成的问题、答案，以及一个可以在 AI 生成的数据集上执行代码的输入框：

![](../Images/e18b8f7b3ee76ed23e2a78d99c3ab8a6.png)

太棒了！让我们来解决第一个问题：计算每天行驶的总距离。按照良好的分析实践，我们从数据探索开始：

![](../Images/73dcc32b41105c3c97ecd042f74041a4.png)

看起来我们需要按ride_date字段分组，并对distance_miles字段求和。让我们编写并提交这段Pandas代码：

![](../Images/a541d03751698530798f9994483d4862.png)

看起来不错！AI的答案是否同意我们的做法？

![](../Images/f4c0dda442a99b7a9183da79e71dc9d4.png)

AI答案使用略有不同的方法，但本质上以相同的方式解决问题。

我可以反复练习，直到感觉完全准备好再去面试。面试Airbnb？这个工具能帮你。它会生成这些问题：

![](../Images/4cc47cd2ff09cdba93d2765d1d7d09c3.png)

以及一个可以执行代码的数据集：

![](../Images/2fffe8f5b44a4c0ab747bc7cf4ab41a2.png)

# 如何使用该应用

查看仓库的readme [这里](https://github.com/mathewnxwang/data_analysis_interview_tool/blob/main/README.md#how-to-run)以在本地运行该应用。不幸的是，我没有托管它，但未来可能会！

# 高层设计

本文接下来将介绍我如何创建AI数据分析面试官的技术细节。

## LLM架构

我使用了OpenAI的gpt-4o，因为它目前是我常用的LLM模型（不过换成其他模型也很容易。）

进行的LLM调用有3种类型：

1.  **数据集生成**：我们请求LLM生成一个适合分析面试的数据集。

1.  **问题生成**：我们请求LLM从该数据集生成几个分析面试问题。

1.  **答案生成**：我们请求LLM为每个面试问题生成答案代码。

## 前端

我使用Flask构建了前端。它简单且不太有趣，所以我将重点讲解LLM的细节。欢迎查看[仓库中的代码](https://github.com/mathewnxwang/data_analysis_interview_tool/tree/main)！

# 设计细节

## LLM管理器

LLMManager是一个简单的类，负责进行LLM API调用。它从本地的秘密文件中获取我们的OpenAI API密钥，并进行OpenAI API调用，将提示传递给LLM模型。在每个LLM项目中你都会看到类似的形式。

```py
class LLMManager():
    def __init__(self, model: str = 'gpt-4o'):
        self.model = model

        load_dotenv("secrets.env")
        openai_api_key = os.getenv("OPENAI_API_KEY")
        self.client = OpenAI(api_key=openai_api_key)

    def call_llm(self, system_prompt: str, user_prompt: str, temperature: float) -> str:
        print(f"Calling LLM with system prompt: {system_prompt}\n\nUser prompt: {user_prompt}")
        response: ChatCompletion = self.client.chat.completions.create(
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_prompt}
            ],
            model=self.model,
            temperature=temperature
        )
        message = response.choices[0].message.content
        print(response)
        return message
```

## 数据集生成

这就是有趣的部分开始！

我们首先通过以下提示请求LLM生成一个数据集：

```py
SYSTEM_TEMPLATE = """You are a senior staff data analyst at a world class tech company.
You are designing a data analysis interview for hiring candidates."""

DATA_GENERATION_USER_TEMPLATE = """Create a dataset for a data analysis interview that contains interesting insights.
Specifically, generate comma delimited csv output with the following characteristics:
- Relevant to company: {company}
- Dataset description: {description}
- Number of rows: 100
- Number of columns: 5
Only include csv data in your response. Do not include any other information.
Start your output with the first header of the csv: "id,".
Output: """
```

让我们分解一下：

+   许多LLM模型遵循一种提示结构，LLM接受系统消息和用户消息。系统消息旨在定义一般行为，用户消息则提供具体指令。在这里，我们通过系统消息要求LLM成为一位世界级面试官。虽然这听起来有些傻，但激励LLM是一个经过验证的提示技巧，可以获得更好的表现。

+   我们通过字符串变量{company}和{description}将用户输入的公司和数据集信息传递到用户模板中，供其练习面试使用。

+   我们提示LLM以csv格式输出数据。这似乎是LLM生成数据的最简单的表格格式，之后我们可以将其转换为Pandas DataFrame进行代码分析。JSON格式也可能可行，但由于其更复杂且冗长的语法，可能会不那么可靠。

+   我们希望LLM的输出是可解析的csv，但gpt-4o往往会生成多余的文本，可能是因为它被训练得非常乐于助人。用户模板的结尾强烈指示LLM只输出可解析的csv数据，但即便如此，我们仍需要对其进行后处理。

DataGenerator类处理所有数据生成相关的工作，并包含generate_interview_dataset方法，该方法进行LLM调用以生成数据集：

```py
 def generate_interview_dataset(self, company: str, description: str, mock_data: bool) -> str:
        if not mock_data:
            data_generation_user_prompt = DATA_GENERATION_USER_TEMPLATE.format(company=company, description=description)
            dataset = self.llm_manager.call_llm(
                system_prompt=SYSTEM_TEMPLATE,
                user_prompt=data_generation_user_prompt,
                temperature=0
            )

            dataset = self.clean_llm_dataset_output(dataset)
            return dataset

        return MOCK_DATASET

    def clean_llm_dataset_output(self, dataset: str) -> str:
        cleaned_dataset = dataset[dataset.index("id,"):]
        return cleaned_dataset
```

请注意，clean_llm_dataset_output方法执行了上面提到的轻度后处理。它去除了“id”之前的任何多余文本，“id”表示csv数据的开始。

LLM只能输出字符串，因此我们需要将字符串输出转换为可分析的Pandas DataFrame。convert_str_to_df方法处理了这个问题：

```py
 def convert_str_to_df(self, dataset: str) -> pd.DataFrame:
        csv_data = StringIO(dataset)

        try:
            df = pd.read_csv(csv_data)
        except Exception as e:
            raise ValueError(f"Error in converting LLM csv output to DataFrame: {e}")

        return df
```

## 问题生成

我们可以使用以下提示，提示LLM根据生成的数据集生成面试问题：

```py
QUESTION_GENERATION_USER_TEMPLATE = """Generate 3 data analysis interview questions that can be solved with Python pandas code based on the dataset below:

Dataset:
{dataset}

Output the questions in a Python list where each element is a question. Start your output with [".
Do not include question indexes like "1." in your output.
Output: """
```

再次分解：

+   这里使用的是相同的系统提示，因为我们仍然希望LLM在编写面试问题时体现出世界级面试官的水平。

+   从数据集生成调用得到的字符串输出被传递到{dataset}字符串变量中。注意，我们必须维护数据集的两种表示方式：1\. 一个LLM可以理解的字符串表示，用于生成问题和答案；2\. 一个结构化表示（即DataFrame），我们可以在其上执行代码。

+   我们提示LLM返回一个列表。我们需要输出是结构化的，以便在答案生成步骤中遍历问题，为每个问题生成一个答案。

LLM调用是通过DataGenerator的generate_interview_questions方法进行的：

```py
 def generate_interview_questions(self, dataset: str) -> InterviewQuestions:

        question_generation_user_prompt = QUESTION_GENERATION_USER_TEMPLATE.format(dataset=dataset)
        questions = self.llm_manager.call_llm(
            system_prompt=SYSTEM_TEMPLATE,
            user_prompt=question_generation_user_prompt,
            temperature=0
        )

        try:
            questions_list = literal_eval(questions)
        except Exception as e:
            raise ValueError(f"Error in converting LLM questions output to list: {e}")

        questions_structured = InterviewQuestions(
            question_1=questions_list[0],
            question_2=questions_list[1],
            question_3=questions_list[2]
        )

        return questions_structured
```

## 答案生成

在有了数据集和问题之后，我们最终通过以下提示生成答案：

```py
ANSWER_GENERATION_USER_TEMPLATE = """Generate an answer to the following data analysis interview Question based on the Dataset.

Dataset:
{dataset}

Question: {question}

The answer should be executable Pandas Python code where df refers to the Dataset above.
Always start your answer with a comment explaining what the following code does.
DO NOT DEFINE df IN YOUR RESPONSE.
Answer: """
```

+   我们根据问题的数量生成相应数量的答案生成LLM调用，因此是3次，因为我们将问题生成提示硬编码为请求生成3个问题。从技术上讲，你可以要求LLM在一次调用中为所有3个问题生成所有3个答案，但我怀疑这会导致性能下降。我们希望最大化LLM生成准确答案的能力。一个（也许显而易见的）经验法则是，任务越难，LLM完成得越不好。

+   提示指示LLM将数据集称为“df”，因为当用户代码通过下面的CodeExecutor类执行时，我们的面试数据集以DataFrame形式被称为“df”。

```py
class CodeExecutor():

    def execute_code(self, df: pd.DataFrame, input_code: str):

        local_vars = {'df': df}
        code_prefix = """import pandas as pd\nresult = """
        try:
            exec(code_prefix + input_code, {}, local_vars)
        except Exception as e:
            return f"Error in code execution: {e}\nCompiled code: {code_prefix + input_code}"

        execution_result = local_vars.get('result', None)

        if isinstance(execution_result, pd.DataFrame):
            return execution_result.to_html()

        return execution_result
```

# 结论

我希望本文能为如何构建一个简单而有用的LLM项目提供一些启示，这个项目在多种方式上利用了LLM！

如果我继续开发这个项目，我会专注于：

1.  增加对来自 LLMs 的结构化输出（即可解析的 csv 或列表）的更多验证。我已经覆盖了一些边缘案例，但 LLMs 非常不可预测，因此需要加固。

2\. 添加更多功能，如

+   生成多个关系表和需要连接的查询

+   除了 Python 之外的 SQL 面试

+   自定义数据集上传

+   难度设置
