# 理解思维缓冲区（BoT）——与大型语言模型推理

> 原文：[https://towardsdatascience.com/understanding-buffer-of-thoughts-bot-reasoning-with-large-language-models-391919d2f76f?source=collection_archive---------1-----------------------#2024-06-14](https://towardsdatascience.com/understanding-buffer-of-thoughts-bot-reasoning-with-large-language-models-391919d2f76f?source=collection_archive---------1-----------------------#2024-06-14)

## 一种用于复杂推理的新提示工具，与思维链（CoT）和思维树（ToT）进行比较

[](https://medium.com/@itshesamsheikh?source=post_page---byline--391919d2f76f--------------------------------)[![Hesam Sheikh](../Images/b8d5f4f285eef77634e4c1d4321580ed.png)](https://medium.com/@itshesamsheikh?source=post_page---byline--391919d2f76f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--391919d2f76f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--391919d2f76f--------------------------------) [Hesam Sheikh](https://medium.com/@itshesamsheikh?source=post_page---byline--391919d2f76f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--391919d2f76f--------------------------------) ·阅读时间：10分钟·2024年6月14日

--

> 如果你不是会员，[**免费阅读！**](https://hesamsheikh.substack.com/) **✨**

![](../Images/1dd8cf331a7b47649dd087344982ff56.png)

图片由[Etienne Girardet](https://unsplash.com/@etiennegirardet?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

提升在复杂推理任务中的能力并避免幻觉依然是大型语言模型（LLMs）中的一个主要研究课题。尽管进行了大量努力，LLMs 仍然在广泛推理能力上需要帮助。传统方法，如**思维链（CoT）**或**思维树（ToT）**，通常需要多个假设或大量的反复提示，这意味着需要强大的计算资源。

![](../Images/acb9be520c3976344727f77d1cbdfb81.png)

思维缓冲区（BoT）与其他提示方法的比较。（来源：[论文](https://arxiv.org/abs/2406.04271)）

论文中提出的新的方法，[**思维缓冲区：基于大语言模型的思维增强推理**](https://arxiv.org/abs/2406.04271) **[1]**，通过一个动态、自适应的高层次思维模板库——**元缓冲区（meta-buffer）**，来应对这些局限。在BoT中，一旦用户提出一个新问题，首先对问题进行简化和分析，以提取关键信息，然后从动态数据集中检索与之相关的思维模板。这使得通过修改和复杂的推理模式实现自适应和高效的问题解决。根据原始论文，效果如此显著，*“****Llama3–8B+BoT*** *有潜力超越* ***Llama3–70B模型。”***

**BoT**通过以下方式，在与其模板相似的问题中实现高效推理：

+   (1) 在新挑战中借用以前的解决方案，

+   (2) 提高效率，避免多次查询迭代（正如我们在思维图（Graph-of-Thoughts, GoT）或思维链（ToT）中所看到的），并

+   (3) 动态更新其模板库，以确保在遇到新任务时能够不断发展。

在本文中，我们将首先了解BoT如何工作的一般概述，理解每个关键部分的功能，并通过一个示例测试该过程。

# BoT是如何工作的？

一般的思维增强推理过程（如下图所示）从**问题提炼**开始，分析并浓缩传入的任务，提取出关键元素和约束条件，进而创建简化的问题陈述。

这些提炼的信息随后被用来查询**元缓冲区（Meta-Buffer）**，这是一个包含高层次思维模板的动态库。从思维模板中，检索出与提炼问题最相似的模板。接下来，在**实例化过程（Instantiation Process）**中，它会根据提炼问题的具体要求和信息进行实例化。

在整个过程中，**缓冲区管理器（Buffer Manager）**会主动监控**元缓冲区（Meta-Buffer）**。一旦检测到元缓冲区中未包含的新见解，**缓冲区管理器（Buffer Manager）**会更新元缓冲区，确保思维模板库的持续进化。

![](../Images/32292bd0be51f32c6798de545021c314.png)

BoT过程。（来源：[论文](http://Paper)）

让我们逐一了解这些关键部分，深入探讨每一部分的细节：

## 问题提炼器

问题提炼器可以看作是对输入任务的**预处理**，目的是……

+   (1) 提取问题的关键信息，并

+   (2) 简化复杂任务，以便更好地搜索和检索思维模板。

问题提炼器（Problem Distiller）通过元提示ϕ减轻了大语言模型（LLM）识别和提取问题的关键信息与约束的负担：

![](../Images/944bc53eb01b77fdf82f8608b1a19b46.png)

（来源：[论文](http://Paper)）

作者用于提炼任务关键信息的提示如下：

```py
[Problem Distiller]:
As a highly professional and intelligent expert in information distillation, you excel at
extracting essential information to solve problems from user input queries. You adeptly
transform this extracted information into a suitable format based on the respective type of the
issue.
Please categorize and extract the crucial information required to solve the problem from the
user’s input query, the distilled information should include.
1\. Key information:
Values and information of key variables extracted from user input, which will be handed over
to the respective expert for task resolution, ensuring all essential information required to solve
the problem is provided.
2\. Restrictions:
The objective of the problem and corresponding constraints.
3\. Distilled task:
Extend the problem based on 1 and 2, summarize a meta problem that can address the user
query and handle more input and output variations. Incorporate the real-world scenario of the
extended problem along with the types of key variables and information constraints from the
original problem to restrict the key variables in the extended problem. After that, use the user
query input key information as input to solve the problem as an example.
```

## 元缓冲区（Meta-Buffer）

元缓冲区是一个中央数据库，存储着高级的思维模板。这些模板是表示各种问题解决过程的高级抽象。其理念是LLM可以利用过去的问题和见解来解决当前的挑战。最棒的是，元缓冲区会动态更新，以确保新的未见过的问题也能被包括在内。元缓冲区不会强制思维模板遵循特定的指令。

**模板检索**：一旦任务被提炼，BoT会遍历思维模板并抓取与任务最相似的一个。通过计算任务与思维模板之间的嵌入相似度来完成此操作。

![](../Images/3877ad60c789b210ee2cd5f42b642b33.png)

检索器会计算输入任务f(xd)的嵌入与模板f(DTi)的嵌入之间的相似度。（来源：[论文](https://arxiv.org/abs/2406.04271)）

检索器会计算输入任务**f(xd)**的嵌入与模板**f(D*Ti*)**的嵌入之间的相似度。仅在相似度超过某个阈值δ（0.5–0.7）时才会进行此操作。如果没有思维模板与任务的相似度超过δ阈值，则**xd**会被识别为新任务。根据任务是否是新任务，将选择以下两条路径之一：

+   如果任务与某个思维模板相似，则会使用实例化提示（可以在论文中查看）用提炼过的信息实例化该模板。这个实例化过程可以表示为

![](../Images/872661a6c9986487d31d5d362f937a78.png)

实例化推理。（来源：[论文](https://arxiv.org/abs/2406.04271)）

+   如果任务是新的，则使用一个旨在解决广泛问题的通用思维模板。随着任务的处理，**缓冲区管理器**会观察并学习，并可能创建一个新的、更具体的思维模板并将其推送到元缓冲区。

## 缓冲区管理器

缓冲区管理器在维护和增强元缓冲区中发挥着至关重要的作用。根据从解决任务中获得的新见解和结果，它更新思维模板。同时，每当解决一个新的或截然不同的问题时，缓冲区管理器会评估是否创建一个新的思维模板。这是为了确保思维模板始终紧扣主题，不冗余。

![](../Images/64359637991b99ea155bceb95391d9d6.png)

检查新生成的模板是否与现有模板相似。（来源：论文）

通过采用上述公式，缓冲区管理器检查元缓冲区是否已经拥有解决问题所需的知识。

## BoT vs. 单查询 vs. 多查询

BoT 相较于之前的方法有什么突出之处？论文的作者在多个数据集和不同任务上评估了各种方法，如数据理解、Python 编程难题、语言学年级数学（MGSM）等。结果显示，**BoT** 在几乎所有任务中都表现出意想不到的优势。

![](../Images/498b49fc3dd24dbe76978a7fb2099b3f.png)

BoT 与之前的方法比较。最佳结果（标记为蓝色）全部由 BoT 实现。（来源：[Paper](https://arxiv.org/abs/2406.04271)）

BoT 的关键优势之一是其效率——与多查询提示方法相比，平均仅需 **12%** 的计算成本。这种多查询方法（如 ToT）通常因其高计算成本和延迟，在实际应用中变得不切实际。

> BoT+Llama3–8B 有潜力超越单一的 Llama3–70B 模型

![](../Images/d343e715b3bfa85f8ddd6a75569f32f8.png)

比较 BoT 在 Llama3–8B 和 70B 上的效果。已标注。（来源：[Paper](https://arxiv.org/abs/2406.04271)）

# 实践中的 Buffer of Thoughts

Buffer of Thoughts 的演示代码已发布在 [GitHub](https://github.com/YangLing0818/buffer-of-thought-llm) [2] 上。为了在实践中测试其功能，我将在一个自定义任务中使用该方法：词语重排。在这个任务中，LLM 必须接受一个乱序的句子，例如 *“Sam name is my”*，并返回一个语义上有意义的单词排列，在这个例子中是 *“my name is Sam”*，（这不是一个基准测试，且没有基准性能）。以下是一些乱序句子及其正确版本的示例：

```py
{"input": "<start> life plan and families to for social hospital workers outside with patients work the <end>", 
"target": "<start> social workers work with patients and families to plan for life outside the hospital <end>"}
{"input": "<start> yield plant refers dry total to production biological matter <end>", 
"target": "<start> biological yield refers to total plant dry matter production <end>"}
{"input": "<start> the bloodstream into alcohol from directly stomach goes the <end>", 
"target": "<start> alcohol goes directly from the stomach into the bloodstream <end>"}
```

我还将使用以下用户提示来解释任务：

```py
Reorder a list of words to make them semantically meaningful, 
for example "Sam name is my" -> "my name is Sam". 
Please provide only the reordered sentence without any explanations. 
Start the ordered sentence with <start> and end it with <end>
```

提供这个任务时，BoT 框架首先提炼我的任务并提取目标、限制条件以及我希望的输出方式，和其他关键信息。BoT 的完整流程如下终端输出：

```py
User prompt:
Reorder a list of words to make them semantically meaningful, for example "Sam name is my" -> "my name is Sam". Please provide only the reordered sentence without any explanations. Start the ordered sentence with <start> and end it with <end>
Input:
<start> the melting in solid to gold leaf metals is achieve made by desired gold and mixing color other <end>
Distilled information:Meta distiller Respond:

Distilled Information:

1\. Key information:
- Input: "<start> the melting in solid to gold leaf metals is achieve made by desired gold and mixing color other <end>"

2\. Restriction:
- Reorder the list of words such that the sentence is semantically meaningful
- The output should begin with "<start>" and end with "<end>"

3\. Distilled task:
- The task is to reorder the words to create a meaningful sentence from the provided mixed words inside the tags "<start>" and "<end>"

4\. Python transformation:
   (Optional, skip when Python tag is Not for Python) Input parameters:
     input_sentence = "<start> the melting in solid to gold leaf metals is achieve made by desired gold and mixing color other <end>"

5\. Answer form: (Optional, skip when there is no specific answer form)
     The output sentence should be in the form of "<start> ... <end>"

  **Note: The generation ends here. Do not show this message in your answer !**
Instantiated reasoning result: ```python

def ReorderWords(input_sentence):

    # 删除开始和结束标签，并将句子拆分成单词

    words = input_sentence.replace("<start>", "").replace("<end>", "").strip().split()

    # 初步重排以形成有意义的句子

    # 根据任务中提供的上下文，以下重排看起来是合乎逻辑的

    reordered_words = ["the", "melting", "in", "solid", "metals", "is", "achieved", "by", "mixing", "gold", "leaf", "and", "desired", "other", "color", "gold"]

    # 将单词合并成一个句子，并添加开始和结束标签

    reordered_sentence = "<start> " + " ".join(reordered_words) + " <end>"

    return reordered_sentence

# 任务中提供的输入句子

input_sentence = "<start> the melting in solid to gold leaf metals is achieve made by desired gold and mixing color other <end>"

print(ReorderWords(input_sentence))

```py

The result of code execution: <start> the melting in solid metals is achieved by mixing gold leaf and desired other color gold <end>
```

使用 BoT 重排的句子的一些示例：

```py
{"input": "<start> life plan and families to for social hospital workers outside with patients work the <end>", 
"result": "<start> Hospital workers work outside with social patients and plan to the families for life <end>\n"}
{"input": "<start> yield plant refers dry total to production biological matter <end>", 
"result": "<start> Plant yield refers to total dry matter biological production <end>\n"}
{"input": "<start> the bloodstream into alcohol from directly stomach goes the <end>", 
"result": "<start> the alcohol goes directly from the stomach into the bloodstream <end>\n"}
```

请注意，我使用的BoT仓库是一个演示代码，缺少原论文中提到的一些功能，比如通用思维模板、Meta-Buffer的动态更新，或者找到与用户任务最接近的模板嵌入。这些是框架的重要方面，没有它们，我们无法得出思想缓冲区在实际应用中的表现。

# 最后的话

总结来说，BoT在各个领域和任务中表现出色，既准确又高效。这是一种有趣的方法，通过将推理问题分解为基本限制和关键信息，并在之前的解决方案和模板基础上构建，以更好地构造任务，使LLM能够理解。

通过解决其他提示技术的一些局限性，思想缓冲区使LLM能够具备更复杂的思维模式，可能使较小的轻量级模型达到更大模型的性能水平。

允许小型LLM实现接近大型LLM的结果是当前许多研究论文中讨论的一个关键主题。目标是采用各种提示和微调技术，以低计算量和成本提取准确的AI输出。

思想缓冲区是一个新颖且有前景的提示框架，利用一系列技术引导LLM逐步进行推理过程。思想缓冲区技术的完整实践实现尚未到来，但在此期间，可以在提供的演示GitHub仓库[2]中测试基准。

如果你已经读到这里，考虑继续**阅读更多**：

[](/platonic-representation-hypothesis-c812813d7248?source=post_page-----391919d2f76f--------------------------------) [## 柏拉图式表征：人工智能深度网络模型是否趋同？

### 人工智能模型是否正在朝着现实的统一表征发展？柏拉图式表征……

towardsdatascience.com](/platonic-representation-hypothesis-c812813d7248?source=post_page-----391919d2f76f--------------------------------)

**🌟 加入+1000人一起学习** Python、机器学习 / MLOps / 人工智能、数据科学和LLM。 [**关注我**](https://medium.com/@itshesamsheikh/subscribe)并查看我的[**X/Twitter**](https://twitter.com/itsHesamSheikh)，我每天为你更新**。**

感谢阅读，

— Hesam

[1] 杨凌、余泽、张涛、曹胜、许梦、张伟、戈恩萨雷斯、崔博（2024）。《思想缓冲区：通过大型语言模型增强的思维推理》。*arXiv*。取自[https://arxiv.org/abs/2406.04271](https://arxiv.org/abs/2406.04271)

[2] buffer-of-thought-llm, [https://github.com/YangLing0818/buffer-of-thought-llm](https://github.com/YangLing0818/buffer-of-thought-llm)
