# LLM 的（较少为人知的）崛起应用

> 原文：[`towardsdatascience.com/the-lesser-known-rising-application-of-llms-775834116477?source=collection_archive---------3-----------------------#2024-05-13`](https://towardsdatascience.com/the-lesser-known-rising-application-of-llms-775834116477?source=collection_archive---------3-----------------------#2024-05-13)

[](https://medium.com/@vianney.mixtur_39698?source=post_page---byline--775834116477--------------------------------)![Vianney Mixtur](https://medium.com/@vianney.mixtur_39698?source=post_page---byline--775834116477--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--775834116477--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--775834116477--------------------------------) [Vianney Mixtur](https://medium.com/@vianney.mixtur_39698?source=post_page---byline--775834116477--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--775834116477--------------------------------) ·8 分钟阅读·2024 年 5 月 13 日

--

# 概述

**大语言模型**（LLMs）通常被称为**生成型人工智能**（GenAI），因为它们确实具有生成文本的能力。LLM 的第一个流行应用是聊天机器人，其中 ChatGPT 走在前列。随后，我们将其应用范围扩展到其他任务，如[**语义搜索**](https://vianmixt.notion.site/Practical-Semantic-Search-with-MongoDB-and-OpenAI-451692801b41465fae1bea5f70238279)和**检索增强生成**（RAG）。今天，我想谈谈 LLM 的一个崛起应用，即**结构化无结构数据**，我将通过将原始文本结构化为 JSON 数据来展示一个例子。

使用 LLM（大语言模型）进行**数据结构化和提取**是一个非常有前景的应用，具有很大的潜力。以下是原因：

+   **提高准确性：** LLM 能够理解人类语言的细微差别。这使得它们能够比传统的基于规则的系统更准确地从杂乱、无结构的文本中识别关键信息。

+   **自动化潜力：** 从无结构数据中提取信息可能是一项耗时且劳动密集的任务。LLM 可以自动化这个过程，从而释放人力资源用于其他任务，并加速对更大数据集的分析。

+   **适应性和学习能力：** LLM 则可以持续进行微调，并适应处理新的数据源和信息类型。随着它们接触到更多的无结构数据，它们能够学习并提高识别模式和提取相关信息的能力。

+   **业务成果：** 大量宝贵信息存在于诸如电子邮件、客户评论、社交媒体对话和内部文档等非结构化文本数据源中。然而，这些数据通常难以分析。大语言模型（LLM）可以通过将非结构化数据转换为结构化格式，解锁这些隐藏的潜力。这使得企业能够利用强大的分析工具来识别趋势并获取洞察。实质上，通过使用 LLM 将非结构化数据进行结构化，企业可以将一种负担（不可用的数据）转变为一种资产（有价值的洞察），从而推动更好的决策并提升整体业务成果。

# 一个例子

最近，我在为个人项目寻找开源食谱数据集时，除了[这个 GitHub 仓库](https://github.com/ronaldlong46/public-domain-recipes)外，什么也没找到，该仓库包含了在[publicdomainrecipes.com](https://publicdomainrecipes.com/)上显示的食谱。

![](img/04ad1e467b2d1359d7502b35c19f079e.png)

照片由[Jeff Sheldon](https://unsplash.com/@ugmonk?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

不幸的是，我需要一个更具可操作性的数据集，即更接近**表格数据**或**NoSQL 文档**的东西。这就是我开始考虑找到一种方法，将原始数据转换成更适合我需求的东西，而不需要花费数小时、数天甚至数周手动完成的原因。

让我展示一下我如何利用大语言模型的强大能力来自动化将原始文本转换为结构化文档的过程。

## 数据集

原始数据集是一个 markdown 文件的集合，每个文件代表一个食谱。

一个描述如何制作法式可丽饼的食谱的 markdown 文件示例。

如你所见，这并非完全无结构，文件顶部有一些漂亮的表格元数据，接下来有 4 个不同的部分：

+   一个介绍，

+   食材列表

+   步骤

+   一些提示。

基于这个观察，[Sebastian Bahr](https://www.linkedin.com/in/sebastianbahr/)开发了一个解析器，将 markdown 文件转换为 JSON 格式，[在这里](https://github.com/sebastianbahr/RecipeRecommender)。

解析器的输出已经变得更具可操作性，此外，Sebastian 利用它构建了一个食谱推荐聊天机器人。然而，仍然存在一些缺点。食材和步骤的键包含了原始文本，这些文本可以做得更有结构。

按原样，某些有用的信息被隐藏了。

例如，食材的数量，每个步骤的准备或烹饪时间。

## 代码

在本文的剩余部分，我将展示我采取的步骤，以便得到像下面这样的 JSON 文档。

```py
{
    "name": "Crêpes",
    "serving_size": 4,
    "ingredients": [
        {
            "id": 1,
            "name": "white flour",
            "quantity": 300.0,
            "unit": "g"
        },
        {
            "id": 2,
            "name": "eggs",
            "quantity": 3.0,
            "unit": "unit"
        },
        {
            "id": 3,
            "name": "milk",
            "quantity": 60.0,
            "unit": "cl"
        },
        {
            "id": 4,
            "name": "beer",
            "quantity": 20.0,
            "unit": "cl"
        },
        {
            "id": 5,
            "name": "butter",
            "quantity": 30.0,
            "unit": "g"
        }
    ],
    "steps": [
        {
            "number": 1,
            "description": "Mix flour, eggs, and melted butter in a bowl.",
            "preparation_time": null,
            "cooking_time": null,
            "used_ingredients": [1,2,5]
        },
        {
            "number": 2,
            "description": "Slowly add milk and beer until the dough becomes fluid enough.",
            "preparation_time": 5,
            "cooking_time": null,
            "used_ingredients": [3,4]
        },
        {
            "number": 3,
            "description": "Let the dough rest for one hour.",
            "preparation_time": 60,
            "cooking_time": null,
            "used_ingredients": []
        },
        {
            "number": 4,
            "description": "Cook the crêpe in a flat pan, one ladle at a time.",
            "preparation_time": 10,
            "cooking_time": null,
            "used_ingredients": []
        }
    ]
}
```

复现教程的代码在 GitHub[这里](https://github.com/VianneyMI/baker)。

我依赖了两个强大的库——用于与 LLM 提供商通信的`[langchain](https://www.langchain.com/)`，以及用于格式化 LLM 输出的`[pydantic](https://docs.pydantic.dev/latest/)`。

首先，我定义了食谱的两个主要组成部分——`Ingredient`类和`Step`类。

在每个类中，我定义了相关的属性，并提供了字段描述和示例。然后这些内容通过`langchain`传递给 LLM，从而获得更好的结果。

```py
"""`schemas.py`"""

from pydantic import BaseModel, Field, field_validator

class Ingredient(BaseModel):
    """Ingredient schema"""

    id: int = Field(
        description="Randomly generated unique identifier of the ingredient",
        examples=[1, 2, 3, 4, 5, 6],
    )
    name: str = Field(
        description="The name of the ingredient", 
        examples=["flour", "sugar", "salt"]
    )
    quantity: float | None = Field(
        None,
        description="The quantity of the ingredient",
        examples=[200, 4, 0.5, 1, 1, 1],
    )
    unit: str | None = Field(
        None,
        description="The unit in which the quantity is specified",
        examples=["ml", "unit", "l", "unit", "teaspoon", "tablespoon"],
    )

    @field_validator("quantity", mode="before")
    def parse_quantity(cls, value: float | int | str | None):
        """Converts the quantity to a float if it is not already one"""

        if isinstance(value, str):
            try:
                value = float(value)
            except ValueError:
                try:
                    value = eval(value)
                except Exception as e:
                    print(e)
                    pass

        return value

class Step(BaseModel):
    number: int | None = Field(
        None,
        description="The position of the step in the recipe",
        examples=[1, 2, 3, 4, 5, 6],
    )
    description: str = Field(
        description="The action that needs to be performed during that step",
        examples=[
            "Preheat the oven to 180°C",
            "Mix the flour and sugar in a bowl",
            "Add the eggs and mix well",
            "Pour the batter into a greased cake tin",
            "Bake for 30 minutes",
            "Let the cake cool down before serving",
        ],
    )
    preparation_time: int | None = Field(
        None,
        description="The preparation time mentioned in the step description if any.",
        examples=[5, 10, 15, 20, 25, 30],
    )
    cooking_time: int | None = Field(
        None,
        description="The cooking time mentioned in the step description if any.",
        examples=[5, 10, 15, 20, 25, 30],
    )
    used_ingredients: list[int] = Field(
        [],
        description="The list of ingredient ids used in the step",
        examples=[[1, 2], [3, 4], [5, 6], [7, 8], [9, 10], [11, 12]],
    )

class Recipe(BaseModel):
    """Recipe schema"""

    name: str = Field(
        description="The name of the recipe",
        examples=[
            "Chocolate Cake",
            "Apple Pie",
            "Pasta Carbonara",
            "Pumpkin Soup",
            "Chili con Carne",
        ],
    )
    serving_size: int | None = Field(
        None,
        description="The number of servings the recipe makes",
        examples=[1, 2, 4, 6, 8, 10],
    )
    ingredients: list[Ingredient] = []
    steps: list[Step] = []
    total_preparation_time: int | None = Field(
        None,
        description="The total preparation time for the recipe",
        examples=[5, 10, 15, 20, 25, 30],
    )
    total_cooking_time: int | None = Field(
        None,
        description="The total cooking time for the recipe",
        examples=[5, 10, 15, 20, 25, 30],
    )
    comments: list[str] = []
```

**技术细节**

+   这里需要注意不要使用过于严格的模型，否则 LLM 输出的 JSON 会失败 Pydantic 验证。一个好的方法是提供一些灵活性，比如根据目标输出类型，提供默认值如`None`或空列表`[]`。

+   注意`Ingredient`类中`quantity`属性上的`field_validator`，它帮助引擎解析数量。最初没有这个，但通过一些实验，我发现 LLM 经常将数量作为字符串提供，例如`1/3`或`1/2`。

+   `used_ingredients`用于正式地将食材与食谱的相关步骤联系起来。

输出模型一旦定义，后续过程就相对顺利。

在`prompt.py`文件中，我定义了一个`create_prompt`函数，用于轻松生成提示语。每个食谱都会生成一个“新”的提示语。所有提示语有相同的基础，但食谱本身作为变量传递给基础提示语，以创建一个新的提示。

```py
""" `prompt.py`

The import statements and the create_prompt function have not been included 
in this snippet.
"""
# Note : Extra spaces have been included here for readability.

DEFAULT_BASE_PROMPT = """
What are the ingredients and their associated quantities 
as well as the steps to make the recipe described 
by the following {ingredients} and {steps} provided as raw text ?

In particular, please provide the following information:
- The name of the recipe
- The serving size
- The ingredients and their associated quantities
- The steps to make the recipe and in particular, the duration of each step
- The total duration of the recipe broken 
down into preparation, cooking and waiting time. 
The totals must be consistent with the sum of the durations of the steps. 
- Any additional comments

{format_instructions}
Make sure to provide a valid and well-formatted JSON.

"""
```

与 LLM 逻辑的通信是在`core.py`文件的`run`函数中定义的，我这里为了简洁没有展示。

最后，我将所有这些组件结合在我的`demo.ipynb`笔记本中，以下是其内容。

```py
# demo.ipynb
import os
from pathlib import Path

import pandas as pd
from langchain.output_parsers import PydanticOutputParser
from langchain_mistralai.chat_models import ChatMistralAI
from dotenv import load_dotenv

from core import run
from prompt import DEFAULT_BASE_PROMPT, create_prompt
from schemas import Recipe
 # End of first cell

# Setup environment
load_dotenv()
MISTRAL_API_KEY = os.getenv("MISTRAL_API_KEY") #1
 # End of second cell

# Load the data
path_to_data = Path(os.getcwd()) / "data" / "input" #2
df = pd.read_json("data/input/recipes_v1.json")
df.head()
 # End of third cell

# Preparing the components of the system
llm = ChatMistralAI(api_key=MISTRAL_API_KEY, model_name="open-mixtral-8x7b")
parser = PydanticOutputParser(pydantic_object=Recipe)
prompt = create_prompt(
    DEFAULT_BASE_PROMPT, 
    parser, 
    df["ingredients"][0], 
    df["direction"][0]
)
#prompt  
  # End of fourth cell

# Combining the components  
example = await run(llm, prompt, parser)
#example
 # End of fifth cell
```

我使用了[MistralAI](https://mistral.ai/)作为 LLM 提供商，采用他们的`open-mixtral-8x7b`模型，这是一个非常好的开源替代方案，相较于[OpenAI](https://openai.com/)。`langchain`让你可以轻松切换提供商，只要你在提供商平台上创建了账户。

如果你试图复现这些结果：

+   (#1) — 确保你在`.env`文件中或在操作系统环境中有`MISTRAL_API_KEY`。

+   (#2) — 请小心数据的路径。如果你克隆了我的仓库，这将不是问题。

在整个数据集上运行代码的费用不到 2€。

通过此代码生成的结构化数据集可以在我的仓库中[这里](https://github.com/VianneyMI/baker/blob/main/data/output/parsed_recipes_all_8x7b.json)找到。

我对结果很满意，但仍然可以尝试对提示语、字段描述或所使用的模型进行迭代，以进一步改进它们。我可能会尝试 MistralAI 的最新模型`open-mixtral-8x22b`，或者通过简单地修改 2 到 3 行代码，借助`langchain`尝试另一个 LLM 提供商。

当我准备好时，我可以回到我的原始项目。如果你想知道它是什么，敬请关注。同时，如果你有任何想法，欢迎在评论中告诉我，你会如何利用最终的数据集？

# 结论

大型语言模型（LLMs）为结构化非结构化数据提供了强大的工具。它们理解和解释人类语言细微差别的能力、自动化繁琐任务的能力，以及适应不断变化的数据的能力，使它们成为数据分析中无价的资源。通过发掘非结构化文本数据中的潜在价值，企业可以将这些数据转化为有价值的洞察，推动更好的决策和商业成果。提供的例子——将原始食谱数据转化为结构化格式——只是 LLMs 所能提供的无数可能性中的一个。

随着我们继续探索和开发这些模型，未来我们可以预见到更多创新应用的出现。充分发挥 LLMs 潜力的旅程才刚刚开始，未来的道路充满了激动人心的前景。
