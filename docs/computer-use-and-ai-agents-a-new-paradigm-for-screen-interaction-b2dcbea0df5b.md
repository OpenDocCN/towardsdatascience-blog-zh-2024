# 计算机使用与 AI 代理人：屏幕互动的新范式

> 原文：[`towardsdatascience.com/computer-use-and-ai-agents-a-new-paradigm-for-screen-interaction-b2dcbea0df5b?source=collection_archive---------6-----------------------#2024-10-30`](https://towardsdatascience.com/computer-use-and-ai-agents-a-new-paradigm-for-screen-interaction-b2dcbea0df5b?source=collection_archive---------6-----------------------#2024-10-30)

## 探索多模态 AI 代理人的未来以及屏幕互动的影响

[](https://medium.com/@tula.masterman?source=post_page---byline--b2dcbea0df5b--------------------------------)![Tula Masterman](https://medium.com/@tula.masterman?source=post_page---byline--b2dcbea0df5b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b2dcbea0df5b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2dcbea0df5b--------------------------------) [Tula Masterman](https://medium.com/@tula.masterman?source=post_page---byline--b2dcbea0df5b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2dcbea0df5b--------------------------------) ·7 分钟阅读·2024 年 10 月 30 日

--

![](img/415e6388c247739dd845b959f936aec5.png)

图片由作者使用 GPT4o 创作

# **介绍**：不断发展的 AI 代理人领域

最近来自 Anthropic、Microsoft 和 Apple 的公告正在改变我们对 AI 代理人的看法。今天，“AI 代理人”这个术语已经过度饱和——几乎每一个与 AI 相关的公告都提到代理人，但它们的复杂性和实用性差异很大。

在一个极端，我们有利用多个循环进行规划、工具执行和目标评估的先进代理人，它们会反复迭代，直到完成任务。这些代理人甚至可能创建并使用记忆，从过去的错误中学习，以推动未来的成功。确定一个有效代理人的标准是 AI 研究的一个非常活跃的领域。它涉及理解哪些属性构成一个成功的代理人（例如，代理人应如何规划，如何使用记忆，应该使用多少工具，如何跟踪任务）以及配置一组代理人的最佳方法。

在另一个极端，我们发现执行单一目的任务且几乎不需要推理的 AI 代理人。这些代理人通常更侧重于工作流。例如，一个持续总结文档并存储结果的代理人。这些代理人通常更容易实现，因为使用场景较为狭窄，需要较少的规划或跨多个工具的协调，以及较少的复杂决策。

随着 Anthropic、微软和苹果的最新公告，我们正在**见证从基于文本的 AI 代理到多模态代理的转变**。这为 AI 代理提供了书面或口头指令的潜力，并使其能够无缝地导航您的手机或计算机来完成任务。**这具有极大潜力改善跨设备的可访问性，但也伴随着重大风险。** Anthropic 的计算机使用公告强调了将 AI 给予无限制屏幕访问权限的风险，并提供了如在专用虚拟机或容器中运行 Claude、限制互联网访问仅允许特定域名、包括人工检查以及避免让模型访问敏感数据等风险缓解策略。他们还指出，提交给 API 的内容不会用于训练。

# Anthropic、微软和苹果的关键公告：

## **Anthropic 的 Claude 3.5 Sonnet：赋予 AI 使用计算机的能力**

+   **概述**：计算机使用的目标是赋予 AI 与人类一样与计算机互动的能力。理想情况下，Claude 应该能够打开和编辑文档，点击页面的各个区域，滚动和阅读页面，运行和执行命令行代码等等。目前，Claude 能够按照人的指示在计算机屏幕上移动光标，点击相关区域，并在虚拟键盘上输入内容。Claude 在 [OSWorld](https://os-world.github.io/) 基准测试中得分为 14.9%，高于其他 AI 模型，但仍远低于人类（人类通常得分为 70-75%）。

+   **工作原理**：Claude 会查看用户提交的截图并计算像素，以确定需要将光标移动到哪里以完成任务。研究人员指出，Claude 在训练期间没有互联网访问权限以确保安全，但 Claude 能够通过训练任务，如使用计算器和文本编辑器，推广到更复杂的任务。即使失败，Claude 也会重新尝试任务。计算机使用包括三种由 Anthropic 定义的工具：计算机、文本编辑器和 bash。计算机工具用于屏幕导航，文本编辑器用于查看、创建和编辑文本文件，bash 用于运行 bash shell 命令。

+   **挑战**：尽管 Claude 展现了有前景的表现，但其计算机使用能力仍然有很长的路要走。目前，它在滚动、整体可靠性方面存在困难，并且容易受到提示注入攻击。

+   **如何使用**：通过 Anthropic API 提供公开测试版。计算机使用可以与常规工具使用结合。

## **微软的 OmniParser 和 GPT-4V：让 AI 理解并操作屏幕**

+   **概述**：OmniParser 旨在解析用户界面的截图，并将其转化为结构化输出。这些输出可以传递给像 GPT-4V 这样的模型，以基于检测到的屏幕元素生成操作。OmniParser + GPT-4V 在多项基准测试中取得了分数，包括 [Windows Agent Arena](https://microsoft.github.io/WindowsAgentArena/)，该基准测试将 OSWorld 基准适配为 Windows 特定任务。这些任务旨在评估智能体的规划能力、理解屏幕的能力以及使用工具的能力，OmniParser 和 GPT-4V 的得分约为 20%。

+   **工作原理**：OmniParser 结合了多个微调的模型来理解屏幕。它使用了微调的可交互图标/区域检测模型（[YOLOv8](https://yolov8.com/)）、微调的图标描述模型（[BLIP-2](https://arxiv.org/abs/2301.12597) 或 [Florence2](https://arxiv.org/abs/2311.06242)）和 OCR 模块。这些模型用于检测图标和文本并生成描述，然后将这些输出传递给 GPT-4V，后者决定如何利用这些输出与屏幕进行交互。

+   **挑战**：目前，当 OmniParser 检测到重复的图标或文本并将其传递给 GPT-4V 时，GPT-4V 通常无法点击正确的图标。此外，OmniParser 受限于 OCR 输出，因此如果边界框偏移，整个系统可能无法点击正确的可点击链接区域。对于某些图标的理解也存在挑战，因为有时相同的图标用来表示不同的概念（例如，三个点用于加载与用于菜单项）。

+   **如何使用**：OmniParser 可在 [GitHub](https://github.com/microsoft/OmniParser/) 和 [HuggingFace](https://huggingface.co/microsoft/OmniParser) 上获取，你需要安装相关依赖并从 HuggingFace 加载模型，接下来你可以尝试运行示例笔记本，查看 OmniParser 如何解析图像。

## Apple 的 Ferret-UI：为移动 UI 带来多模态智能

+   **概述**：Apple 的 Ferret（随时随地在任何粒度上进行引用和定位）自 2023 年以来就已推出，但最近 Apple 发布了 Ferret-UI，一种多模态大型语言模型（MLLM），能够执行移动 UI 屏幕上的“引用、定位和推理任务”。引用任务包括小部件分类和图标识别等操作。定位任务包括查找图标或查找文本等操作。Ferret-UI 能够理解 UI 并按照指令与 UI 进行交互。

+   **工作原理**：Ferret-UI 基于 Ferret，并通过在“任何分辨率”下进行训练，适配了更精细的图像，从而能更好地理解移动端 UI。每个图像会被分割成两个子图像，并生成各自的特征。大型语言模型（LLM）使用完整图像、两个子图像、区域特征和文本嵌入来生成响应。

+   **挑战**：Ferret-UI 论文中引用的一些结果展示了 Ferret 在预测附近文本而非目标文本时出现的情况，或在屏幕上出现拼写错误时仍能预测有效的单词，有时它还会错误分类 UI 属性。

+   **如何使用**：Apple 在[GitHub](https://github.com/apple/ml-ferret/tree/main/ferretui)上发布了数据和代码，仅供研究使用。Apple 发布了两个 Ferret-UI 检查点，一个基于 Gemma-2b，另一个基于 Llama-3–8B。Ferret-UI 模型受 Gemma 和 Llama 许可证的约束，而数据集允许非商业用途。

## 总结：三种 AI 驱动的屏幕导航方法

总结来说，这些系统展示了不同的方式来构建多模态智能体，使其能够代表我们与计算机或移动设备进行交互。

Anthropic 的 Claude 3.5 Sonnet 专注于一般的计算机交互，其中 Claude 通过计算像素来适当地导航屏幕。微软的 OmniParser 解决了将用户界面分解为结构化输出的具体挑战，这些输出随后被发送到像 GPT-4V 这样的模型中以确定行动。Apple 的 Ferret-UI 专为移动 UI 理解而设计，能够识别图标、文本和小部件，同时还能够执行与 UI 相关的开放式指令。

在每个系统中，**工作流通常遵循两个关键阶段：一个是解析视觉信息，另一个是推理如何与之交互**。准确地解析屏幕对于正确规划如何与屏幕交互并确保系统可靠执行任务至关重要。

# 结论：构建更智能、更安全的 AI 智能体

在我看来，这些发展的最激动人心之处在于**多模态能力和推理框架的开始融合**。尽管这些工具提供了**有前景的功能**，但它们仍显著落后于人类的表现。同时，**AI 安全问题**也是在实施任何具有屏幕访问权限的智能体系统时需要解决的重要问题。

**智能体系统的最大优势之一是它们有可能通过将任务分解为专门的组件来克服单一模型的认知限制。** 这些系统可以通过多种方式构建。在某些情况下，用户看到的**单一智能体**可能在幕后实际上由**一组子智能体**组成——每个子智能体**管理不同的责任**，例如规划、屏幕交互或记忆管理。例如，一个推理智能体可能与另一个专门解析屏幕数据的智能体协调，同时，一个独立的智能体负责策划记忆，以提升未来的表现。

或者，这些功能可能会**结合在一个强大的代理中**。在这种设置下，代理可能会有多个内部规划模块——一个专注于规划屏幕交互，另一个专注于管理整体任务。如何构建代理的最佳方法仍有待观察，但目标始终不变：创建能够在多种模式下可靠执行、并且能够无缝适应用户需求的代理。

**参考文献：**

+   [开发计算机使用功能](https://www.anthropic.com/news/developing-computer-use) (Anthropic)

+   [介绍计算机使用、新版 Claude 3.5 Sonnet 和 Claude 3.5 Haiku](https://www.anthropic.com/news/3-5-models-and-computer-use) (Anthropic)

+   [计算机使用文档](https://docs.anthropic.com/en/docs/build-with-claude/computer-use) (Anthropic)

+   [OSWorld GitHub](https://os-world.github.io/)

+   [Windows Agent Arena](https://microsoft.github.io/WindowsAgentArena/)

+   [OmniParser 用于纯视觉基础的 GUI 代理](https://www.microsoft.com/en-us/research/articles/omniparser-for-pure-vision-based-gui-agent/)

+   [OmniParser GitHub](https://github.com/microsoft/OmniParser/)

+   [OmniParser HuggingFace](https://huggingface.co/microsoft/OmniParser)

+   [OmniParser ArXiv](https://arxiv.org/pdf/2408.00203)

+   [Ferret GitHub](https://github.com/apple/ml-ferret)

+   [Ferret-UI: 使用多模态 LLM 进行基于实际应用的移动 UI 理解](https://arxiv.org/pdf/2404.05719)

*有兴趣进一步讨论或合作吗？请通过* [*LinkedIn*](https://www.linkedin.com/in/tula-masterman/)*联系我们！*
