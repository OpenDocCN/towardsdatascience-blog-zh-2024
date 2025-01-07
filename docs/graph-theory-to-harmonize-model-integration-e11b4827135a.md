# 图论在模型集成中的协调作用

> 原文：[https://towardsdatascience.com/graph-theory-to-harmonize-model-integration-e11b4827135a?source=collection_archive---------7-----------------------#2024-02-21](https://towardsdatascience.com/graph-theory-to-harmonize-model-integration-e11b4827135a?source=collection_archive---------7-----------------------#2024-02-21)

## 使用基于图的编排优化多模型协作

[](https://albarqawi.medium.com/?source=post_page---byline--e11b4827135a--------------------------------)[![Ahmad Albarqawi](../Images/f039f134cbb10d01a838a9ad864fd3c6.png)](https://albarqawi.medium.com/?source=post_page---byline--e11b4827135a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e11b4827135a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e11b4827135a--------------------------------) [Ahmad Albarqawi](https://albarqawi.medium.com/?source=post_page---byline--e11b4827135a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e11b4827135a--------------------------------) ·阅读时间：7分钟·2024年2月21日

--

![](../Images/50e51cf257ee05cfe54011610f330018.png)

管弦乐队 — 摄影师 Arindam Mahanta，图片来源于 Unsplash

整合各种AI模型的能力释放了潜力的交响乐，从自动化需要多种能力的复杂任务（如视觉、语音、写作和合成）到增强决策过程。然而，协调这些协作在管理内部关系和依赖性方面存在重大挑战。传统的线性方法往往力不从心，难以处理不同模型和动态依赖关系的复杂性。

通过将你的机器学习工作流程转化为图形，你可以可视化每个模型如何相互作用并为结合自然语言处理、计算机视觉和语音模型的整体结果做出贡献。采用图形方法时，节点代表模型或任务，边定义它们之间的依赖关系。这种基于图的映射提供了多个优势，可以识别哪些模型依赖于其他模型的输出，并利用并行处理来执行独立任务。此外，我们还可以根据任务的优先级，使用现有的图形导航策略，如广度优先或深度优先，来执行这些任务。

通向和谐的AI模型协作之路并非没有障碍。想象一下，指挥一个管弦乐队，每个乐器演奏者都说不同的语言，乐器之间相互独立。这个挑战反映了在整合不同AI模型时的沟通障碍，需要一个框架来管理模型之间的关系，并明确哪些模型可以接收每种输入格式。

# 从理论到实践：预期的使用案例

基于图的协调方法为各个领域开辟了激动人心的可能性：

**药物发现的协同任务**

![](../Images/e82bbc6d32b6602982657fa89fd639f5.png)

三个模型协作作为数据分析任务的一部分示意图 — 图片来自作者

研究人员可以通过一系列由人工智能驱动的助手加速药物发现过程，每个助手都专门为特定任务设计，例如，采用三步药物发现任务。第一步涉及使用语言模型扫描大量科学数据，突出与特定疾病密切相关的潜在蛋白质靶点，接着是视觉模型来解释复杂的图表或图像，为已识别的蛋白质结构提供详细见解。这个视觉模型对于理解潜在药物如何与蛋白质相互作用至关重要。最后，第三个模型结合语言和视觉模型的输入，预测化学化合物可能如何影响目标蛋白质，为研究人员提供有价值的见解，从而有效推动整个过程。

在模型集成过程中会出现若干挑战，以便交付完整的流程。将从扫描内容中提取相关图像并将其输入视觉模型并不像看起来那么简单。需要在文本扫描和视觉任务之间加入一个中介处理器，以过滤相关图像。其次，分析任务本身应当整合多个输入：数据扫描输出、视觉模型的解释以及用户指定的指令。这需要一个模板来将信息组合在一起，以便语言模型进行处理。以下部分将描述如何利用Python框架来处理这些复杂的关系。

**创意内容生成**

![](../Images/9002dfceb80310637827fd6c08a9cc86.png)

四个任务生成动画的示意图 — 图片来自作者

模型协作可以通过整合音乐创作、动画制作和设计模型等元素来促进互动内容的创作。例如，在基于图的协作方法中，第一个任务可以像导演一样规划一个场景，并传递每个音乐和图像生成任务的输入。最后，动画模型将使用艺术和音乐模型的输出生成一个短视频。

为了优化这个过程，我们旨在实现音乐和图形生成的并行执行，因为它们是独立的任务。因此，不需要等待图形完成才能生成音乐。此外，我们还需要解决动画任务的多样化输入格式问题。虽然一些模型如Stable Video Diffusion只处理图像，但音乐可以通过后处理器与之结合。

这些例子仅展示了图论在模型整合中的潜力。图集成方法允许您根据特定需求定制多个任务，开启创新解决方案。

# Intelli框架通过图论协调人工智能模型

![](../Images/0024fdb638ecb31b16278d49ed944fd2.png)

任务以图形形式表示 — 图片由作者提供

Intelli 是一个开源 Python 模块，用于协调 AI 工作流，利用图形原则通过三个关键组件实现：

1.  **代理** 充当你的 AI 模型的代表，你通过指定其类型（文本、图像、视觉或语音）、提供者（openai、gemini、stability、mistral 等）和任务来定义每个代理。

1.  **任务** 是 AI 工作流中的单个单元。每个任务利用代理执行特定操作，并应用用户提供的自定义预处理和后处理。

1.  **Flow** 将一切连接在一起，协调任务的执行，遵循你通过图形结构建立的依赖关系。流管理确保任务高效且按正确顺序执行，能够在可能的情况下支持顺序和并行处理。

使用流组件将任务关系管理为图形在连接多个模型时提供了几个好处，但对于仅有一个任务的情况，这可能有些过度，直接调用模型就足够了。

**扩展性**：随着项目复杂性的增长，增加更多模型和任务需要反复更新代码，以处理数据格式不匹配和复杂的依赖关系。图形方法通过定义一个新的节点来表示任务，简化了这一过程，框架会自动解决输入/输出差异，从而协调数据流。

**动态适应**：在传统方法中，复杂任务的变化会影响整个工作流，需要进行调整。而使用流时，它会自动处理添加、删除或修改连接。

**可解释性**：图形通过可视化模型如何交互并优化任务路径导航，增强了你对 AI 工作流的深入理解。

> 注意：作者参与了 Intelli 框架的设计和开发。它是一个具有 Apache 许可证的开源项目。

**开始使用**

首先，确保你使用的是 Python 3.7 及以上版本，因为 Intelli 利用了最新的 Python asyncio 特性，然后安装：

```py
pip install intelli
```

**代理：任务执行者**

Intelli 中的代理旨在与特定的 AI 模型接口。每个代理都包括一个统一的输入层，可以访问任何类型的模型，并提供一个字典，允许传递自定义参数，如最大大小、温度和模型版本。

```py
from intelli.flow.agents.agent import Agent

# Define agents for various AI tasks
text_agent = Agent(
    agent_type="text",
    provider="openai",
    mission="write social media posts",
    model_params={"key": OPENAI_API_KEY, "model": "gpt-4"}
)
```

**任务：构建块**

任务表示由代理执行的单个工作单元或操作，并包含处理前一个任务输出的逻辑。每个任务可以是一个简单的操作，如生成文本，或是一个更复杂的过程，如分析用户反馈的情感。

```py
from intelli.flow.tasks.task import Task
from intelli.flow.input.task_input import TextTaskInput

# Define a task for text generation
task1 = Task(
    TextTaskInput("Create a post about AI technologies"),
    text_agent,
    log=True
)
```

**处理器：优化的输入/输出**

处理器通过定义任务输入的自定义预处理和输出的后处理，提供了额外的控制层。下面的示例演示了在调用图像模型之前，创建一个函数来缩短上一步骤的文本输出。

```py
class TextProcessor:
    @staticmethod
    def text_head(text, size=800):
        retupytrn text[:size]

task2 = Task(
    TextTaskInput("Generate image about the content"),
    image_agent,
    pre_process=TextProcessor.text_head,
    log=True,
)
```

**Flow: 指定依赖关系**

Flow 将您的 AI 工作流转换为有向无环图（DAG），并利用图论进行依赖管理。这使得您可以轻松地可视化任务之间的关系，并优化任务的执行顺序。

```py
from intelli.flow.flow import Flow

flow = Flow(
    tasks={
        "title_task": title_task,
        "content_task": content_task,
        "keyword_task": keyword_task,
        "theme_task": description_theme_task,
        "image_task": image_task,
    },
    map_paths={
        "title_task": ["keyword_task", "content_task"],
        "content_task": ["theme_task"],
        "theme_task": ["image_task"],
    },
)

output = await flow.start()
```

map_paths 决定了任务的依赖关系，引导 Flow 编排执行顺序，确保每个任务从其前置任务获得必要的输出。

以下是 Flow 如何遍历节点的方式：

1.  映射工作流：Flow 使用任务作为节点、依赖关系作为边构建 DAG。这种可视化表示清晰地显示了任务执行顺序和数据流动。

1.  拓扑排序：Flow 分析图形，以确定最佳执行顺序。没有输入依赖的任务优先执行，确保每个任务在执行之前能够从前置任务中获得必要的输入。

1.  任务执行：框架遍历排序后的任务，逐个执行每个任务并提供相应的输入。根据依赖图，输入可能来自之前任务的输出或用户定义的值。

1.  输入准备：在执行之前，任务应用为任务定义的任何预处理功能，根据需要修改输入数据，并调用分配的代理。

1.  输出管理：代理返回输出，并将其存储在以任务名称为键的字典中，然后返回给用户。

要将您的工作流可视化为图形：

```py
flow.generate_graph_img()
```

![](../Images/159e0a980ca352141cf8619c60023480.png)

任务和分配代理的可视化 —— 由 Intelli 图形功能生成的图像

# 结论

使用图论已经改变了传统的线性 AI 模型编排方式，通过提供不同模型之间协作的交响乐式方法。

像 Intelli 这样的框架将您的工作流转化为可视化表示，其中任务成为节点，依赖关系被映射为边，从而创建一个完整的流程概览，用于自动化复杂的任务。

这种方法扩展到需要协作 AI 模型的各个领域，包括科学研究、商业决策自动化和互动内容创作。然而，有效的规模化需要在管理模型之间的数据交换方面进一步改进。

# 参考文献

+   Intelli git 仓库：[https://github.com/intelligentnode/Intelli](https://github.com/intelligentnode/Intelli)

+   图论：[https://www.britannica.com/topic/graph-theory](https://www.britannica.com/topic/graph-theory)
