# 集成文本和图像，进行更智能的数据分类

> 原文：[https://towardsdatascience.com/integrating-text-and-images-for-smarter-data-classification-6a53252d8a73?source=collection_archive---------6-----------------------#2024-11-18](https://towardsdatascience.com/integrating-text-and-images-for-smarter-data-classification-6a53252d8a73?source=collection_archive---------6-----------------------#2024-11-18)

[](https://medium.com/@CVxTz?source=post_page---byline--6a53252d8a73--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--6a53252d8a73--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6a53252d8a73--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6a53252d8a73--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--6a53252d8a73--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6a53252d8a73--------------------------------) ·7分钟阅读·2024年11月18日

--

这是一篇关于如何利用多模态人工智能分类混合文本和图像数据的技术演示，内容包括详细的说明、可执行的代码示例以及有效实现的技巧。

![](../Images/88c05e548892522a34fec7ecb8d4b602.png)

图片来源：[Tschernjawski Sergej](https://unsplash.com/@mrt1987?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在人工智能领域，最令人兴奋的增长领域之一是**多模态学习**，即模型处理并结合不同类型的数据——如图像和文本——以更好地理解复杂场景。这种方法在现实世界应用中尤其有用，因为信息通常在文本和视觉之间分割。

以电子商务为例：一个产品列表可能包括一张显示商品外观的图片，以及一段提供其功能细节的描述。为了全面分类和理解该产品，需要将这两种信息来源结合起来考虑。像**Gemini 1.5**、**Llama 3.2、Phi-3 Vision**这样的多模态大型语言模型（LLMs），以及**LlaVA、DocOwl**等开源工具，专门开发用于处理这些类型的输入。

## 为什么多模态模型很重要

图像和文本中的信息可以互相补充，而单一模态系统可能无法捕捉到这些信息：

+   一个产品的描述可能提到其尺寸或材质，这仅凭图片是无法清楚表达的。

+   另一方面，一张图片可能揭示出如风格或颜色等文本无法充分描述的关键方面。

如果我们单独处理图像或文本，就有可能错过关键信息。多模态模型通过在处理过程中结合这两种来源来解决这个问题，从而获得更准确、更有用的结果。

## 本教程中你将学习的内容

本教程将指导你创建一个旨在处理**图像-文本分类**的管道。你将学习如何处理和分析结合了视觉和文本元素的输入，从而获得比仅使用文本系统更准确的结果。

如果你的项目仅涉及文本分类，我的[另一篇博客文章](https://medium.com/towards-data-science/building-a-reliable-text-classification-pipeline-with-llms-a-step-by-step-guide-87dc73213605)可能会对你有所帮助——它专门讨论了这些方法。

[](/building-a-reliable-text-classification-pipeline-with-llms-a-step-by-step-guide-87dc73213605?source=post_page-----6a53252d8a73--------------------------------) [## 使用LLM构建可靠的文本分类管道：一步一步的指南

### 克服基于LLM的文本分类中的常见挑战

[towardsdatascience.com](/building-a-reliable-text-classification-pipeline-with-llms-a-step-by-step-guide-87dc73213605?source=post_page-----6a53252d8a73--------------------------------)

要成功构建一个多模态的图像-文本分类系统，我们需要三个关键组件。下面是每个元素的详细介绍：

## 1\. 一个可靠的LLM提供商

本教程的核心是**托管的LLM作为服务**。在试验了几种选项后，我发现并非所有LLM都能提供一致的结果，特别是在处理结构化输出时。以下是我的经验总结：

+   **Groq** 和 **Fireworks.ai**：这些平台提供无服务器、按令牌付费的多模态LLM。虽然它们看起来很有前景，但它们的API在处理结构化输出请求时存在问题。例如，当发送一个带有预定义架构的查询时，返回的输出并没有遵循预期的格式，使得它们在需要精确的任务中不可靠。Groq的Llama 3.2仍处于预览阶段，所以我可能稍后会再次尝试它们。Fireworks.ai通常不响应错误报告，所以我从现在起就将它们从我的选项中删除。

+   **Gemini 1.5**：经过一些反复尝试，我选择了Gemini 1.5。它始终返回期望格式的结果，并且至今表现良好。尽管它仍然有一些奇怪的小毛病，如果你长时间使用它会发现（比如不能使用过大的枚举值……）。我们将在本文后续讨论这些问题。这将是我们在本教程中使用的LLM。

## 2\. Python库：LangChain

为了与LLM接口并处理多模态输入，我们将使用**LangChain**库。LangChain特别适合这项任务，因为它使我们能够：

+   将文本和图像数据作为输入注入LLM。

+   定义不同LLM作为服务提供商的通用抽象。

+   定义结构化输出模式，以确保结果符合我们需要的格式。

结构化输出对分类任务尤其重要，因为它们涉及预定义的类别，输出必须符合这些类别。LangChain确保了这一结构的执行，使其成为我们用例的理想选择。

## 3. 分类任务：摄影图片的关键词建议

本教程中我们将关注的任务是**摄影相关图片的关键词建议**。这是一个**多标签分类**问题，这意味着：

+   每张图片可以同时属于多个类别。

+   可能的类别列表是预定义的。

例如，一个由图片及其描述组成的输入可能会被分类为如*landscape, sunset*和*nature*等关键词。虽然一个输入可以应用多个关键词，但这些关键词必须从预定义的类别集中选择。

# 步骤指南：设置Gemini 1.5和LangChain的多模态图像-文本分类

现在我们已经覆盖了基础概念，让我们深入实现部分。这个逐步指南将引导你完成配置Gemini 1.5、设置LangChain以及为摄影相关图片构建关键词建议系统的过程。

## 第一步：获取你的Gemini API密钥

第一步是获取你的**Gemini API 密钥**，你可以在[Google AI Studio](https://aistudio.google.com/app/apikey)生成该密钥。获得密钥后，将其导出到名为`GOOGLE_API_KEY`的环境变量中。你可以选择：

+   将其添加到`.env`文件中：

```py
GOOGLE_API_KEY=your_api_key_here
```

+   直接在终端中导出：

```py
export GOOGLE_API_KEY=your_api_key_here
```

## 第2步：安装并初始化客户端

接下来，安装必要的库：

```py
pip install langchain-google-genai~=2.0.4 langchain~=0.3.6
```

安装完成后，初始化客户端：

```py
import os
from langchain_google_genai import ChatGoogleGenerativeAI

GOOGLE_MODEL_NAME = os.environ.get("GOOGLE_MODEL_NAME", "gemini-1.5-flash-002")

llm_google_client = ChatGoogleGenerativeAI(
    model=GOOGLE_MODEL_NAME,
    temperature=0,
    max_retries=10,
)
```

## 第3步：定义输出模式

为了确保LLM生成有效的结构化结果，我们使用**Pydantic**定义输出模式。这个模式充当过滤器，验证模型返回的类别是否与我们预定义的可接受类别列表匹配。

```py
from typing import List, Literal
from pydantic import BaseModel, field_validator

def generate_multi_label_classification_model(list_classes: list[str]):
    assert list_classes  # Ensure classes are provided

    class ClassificationOutput(BaseModel):
        category: List[Literal[tuple(list_classes)]]

        @field_validator("category", mode="before")
        def filter_invalid_categories(cls, value):
            if isinstance(value, list):
                return [v for v in value if v in list_classes]
            return []  # Return an empty list if input is invalid

    return ClassificationOutput
```

**为什么** `**field_validator**` **作为变通方法是必要的：**

在定义模式时，我们遇到了Gemini 1.5（以及类似的LLM）的一项限制：它们不严格执行**枚举**。这意味着，即使我们提供了固定的类别集，模型也可能返回超出此集合的值。例如：

+   预期：`["landscape", "forest", "mountain"]`

+   返回：`["landscape", "ocean", "sun"]` *(其中“ocean”和“sun”是无效类别)*

如果不处理这个问题，无效的类别可能会导致错误或降低分类的准确性。为了解决这个问题，可以使用`field_validator`方法作为一种变通方法。它充当过滤器，确保：

1.  仅有效的类别会从`list_classes`中包含到输出中。

1.  无效或意外的值将被移除。

这个保障措施确保模型的结果与任务要求相符。虽然我们必须这样做很麻烦，但似乎是我测试过的所有LLM提供商的共同问题。如果你知道哪家提供商能很好地处理枚举，请告诉我。

# 第4步：将模式绑定到LLM客户端

接下来，将模式绑定到客户端进行结构化输出处理：

```py
list_classes = [
    "shelter", "mesa", "dune", "cave", "metropolis",
    "reef", "finger", "moss", "pollen", "daisy",
    "fire", "daisies", "tree trunk",  # Add more classes as needed
]

categories_model = generate_multi_label_classification_model(list_classes)
llm_classifier = llm_google_client.with_structured_output(categories_model)
```

## 第5步：构建查询并调用LLM

定义预测函数以将图像和文本输入发送到LLM：

```py
...
    def predict(self, text: str = None, image_url: str = None) -> list:
        assert text or image_url, "Provide either text or an image URL."

        content = []

        if text:
            content.append({"type": "text", "text": text})

        if image_url:
            image_data = base64.b64encode(httpx.get(image_url).content).decode("utf-8")
            content.append(
                {
                    "type": "image_url",
                    "image_url": {"url": f"data:image/jpeg;base64,{image_data}"},
                }
            )

        prediction = self.llm_classifier.invoke(
            [SystemMessage(content=self.system_prompt), HumanMessage(content=content)]
        )

        return prediction.category
```

要将图像数据发送到Gemini LLM API，我们需要将图像编码为模型可以处理的格式。这时**base64编码**就派上用场了。

**什么是Base64？**

Base64是一种二进制到文本的编码方案，将二进制数据（如图像）转换为文本格式。当传输与基于文本的系统（如API）不兼容的数据时，这非常有用。通过将图像编码为base64，我们可以将其作为有效载荷的一部分，在向LLM发送数据时一起发送。

## 第6步：以多标签关键词的形式获取结果

最后，运行分类器并查看结果。让我们用一个例子来测试：

## 示例输入1：

+   **图片**：

![](../Images/6a7a0ee9460973d78f72f4b816a1fbed.png)

图片由[Calvin Ma](https://unsplash.com/@mkwcalvin?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来自[Unsplash](https://unsplash.com/photos/classic-red-and-white-bus-parked-beside-road-VaH2X8eHKVg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

+   **描述**：

> 经典的红白相间的公交车停放在路旁

## 结果：

+   **图片 + 文本**：

```py
['transportation', 'vehicle', 'road', 'landscape', 'desert', 'rock', 'mountain']
```

+   **仅文本**：

```py
['transportation', 'vehicle', 'road']
```

如图所示，当使用文本和图像输入时，结果与实际内容更为相关。仅使用文本输入时，LLM给出了正确但不完整的值。

## 示例输入2：

+   **图片**：

![](../Images/124e22659b64a9cdbe4ec3d8bdcf71bf.png)

图片由[Tadeusz Lakota](https://unsplash.com/@tadekl?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来自[Unsplash](https://unsplash.com/photos/black-and-white-coated-dog-bLQFCJDImnc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

+   **描述**：

> 黑白毛色的狗

结果：

+   **图片 + 文本**：

```py
['animal', 'mammal', 'dog', 'pet', 'canine', 'wildlife']
```

**仅文本**：

```py
['animal', 'mammal', 'canine', 'dog', 'pet']
```

# 结论

多模态分类结合了文本和图像数据，为创建更具上下文感知和更有效的AI系统提供了一种方法。在本教程中，我们使用Gemini 1.5和LangChain构建了一个关键词建议系统，解决了结构化输出处理和图像数据编码等关键挑战。

通过结合文本和视觉输入，我们展示了这种方法如何比单独使用任何一种模式更准确、更有意义地进行分类。实际例子突显了将数据类型结合使用的价值，从而更好地捕捉给定场景的完整上下文。

# 接下来做什么？

本教程聚焦于文本和图像分类，但这些原理可以应用于其他多模态设置。以下是一些可以进一步探索的思路：

+   **文本和视频**：通过结合视频帧采样和文本输入（如字幕或元数据），扩展系统以分类或分析视频。

+   **文本和PDF**：开发能够处理丰富内容的文档分类器，如科研论文、合同或简历，结合视觉布局和文本数据。

+   **现实世界应用**：将此管道集成到电子商务网站、教育工具或社交媒体内容审查系统等平台中。

这些方向展示了多模态方法的灵活性及其解决多样化现实世界挑战的潜力。随着多模态AI的进化，尝试不同的输入组合将为更智能、更具响应性的系统开辟新的可能性。

完整代码：[llmclassifier/llm_multi_modal_classifier.py](https://github.com/CVxTz/llmclassifier/blob/master/llmclassifier/llm_multi_modal_classifier.py)
