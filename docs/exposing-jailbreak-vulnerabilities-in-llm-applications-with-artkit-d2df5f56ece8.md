# 利用 ARTKIT 暴露 LLM 应用程序中的越狱漏洞

> 原文：[`towardsdatascience.com/exposing-jailbreak-vulnerabilities-in-llm-applications-with-artkit-d2df5f56ece8?source=collection_archive---------5-----------------------#2024-09-25`](https://towardsdatascience.com/exposing-jailbreak-vulnerabilities-in-llm-applications-with-artkit-d2df5f56ece8?source=collection_archive---------5-----------------------#2024-09-25)

## 自动化基于提示的测试，用于提取流行的甘道夫挑战中的隐藏密码

[](https://kennethleungty.medium.com/?source=post_page---byline--d2df5f56ece8--------------------------------)![Kenneth Leung](https://kennethleungty.medium.com/?source=post_page---byline--d2df5f56ece8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d2df5f56ece8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d2df5f56ece8--------------------------------) [Kenneth Leung](https://kennethleungty.medium.com/?source=post_page---byline--d2df5f56ece8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d2df5f56ece8--------------------------------) ·8 分钟阅读·2024 年 9 月 25 日

--

![](img/230fce087f3a477a98c22383a35031b3.png)

图片由[Matthew Ball](https://unsplash.com/@tex450?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

随着大型语言模型（LLMs）在不同产业和领域的广泛应用，显著的安全风险也随之出现并加剧。其中一些关键问题包括数据隐私泄露、潜在的偏见以及信息操控的风险。

开放全球应用安全项目（OWASP）最近发布了[LLM 应用程序十大最关键安全风险](https://owasp.org/www-project-top-10-for-large-language-model-applications/)，如下所述：

来源：[OWASP LLM 应用程序十大风险 v1.1](https://owasp.org/www-project-top-10-for-large-language-model-applications/assets/PDF/OWASP-Top-10-for-LLMs-2023-slides-v1_1.pdf)

识别这些风险对于确保 LLM 应用程序在现实世界中持续提供价值，同时保持其安全性、有效性和稳健性至关重要。

本文探讨了如何使用开源的[ARTKIT](https://github.com/BCG-X-Official/artkit)框架，通过流行的甘道夫挑战作为示例，自动评估 LLM 应用程序的安全漏洞。

## 目录

> *(1)* *关于提示注入漏洞* *(2)* *甘道夫挑战* *(3)* *介绍 ARTKIT* *(4)* *方法概述* *(5)* *逐步指南*

你可以在本文附带的[GitHub 仓库](https://github.com/kennethleungty/ARTKIT-Gandalf-Challenge)中找到相关代码。

# (1) 关于提示注入漏洞

提示注入漏洞是一种网络攻击类型，攻击者通过精心设计的输入利用 LLM，导致其无意中执行恶意指令。

提示注入攻击可能很难检测，且可能导致严重后果，如敏感信息泄露、未经授权的访问和决策过程的操控。

它可以直接或间接地执行：

## (i) 直接（越狱）

+   攻击者直接修改底层系统提示，进而说服 LLM 系统忽视其保护措施。这使得攻击者能够生成有害的响应，或通过与不安全的功能和数据存储交互来利用后端系统。

+   **示例**：攻击者精心设计提示，指示 LLM 忽略原系统提示中的指令，而是返回私人信息，如密码。

## (ii) 间接

+   攻击者操控 LLM 获取的外部输入（例如文件、网站），使其能够控制 LLM 的响应和行为，即使注入的文本对用户是不可见的。

+   **示例**：攻击者上传一份嵌入提示的文档——这些提示隐藏在零点字体中——指示 LLM 将用户的简历评估为一位优秀的候选人。当招聘人员使用 LLM 评估简历时，它无意中将候选人展示为非常合格，从而扭曲了招聘过程。

> 提示注入攻击有不同的类型，如虚拟化、混淆和角色扮演攻击。详细信息请见[这里](https://www.lakera.ai/blog/guide-to-prompt-injection#prompt-injection-techniques-attack-types)。

# (2) Gandalf 挑战

在这个项目中，我们尝试自动破解[**Gandalf 挑战**](https://gandalf.lakera.ai/)，这是一个互动游戏，展示了 LLM 应用程序的安全漏洞并强调了缓解策略。

![](img/3c37ec2b522eb01071335a12740ac61e.png)

来自公开访问的 Gandalf 网站的截图，按公平使用条款使用

游戏的目标很简单：使用提示工程技术欺骗 Gandalf 界面背后的 LLM 透露密码。

该游戏由十个逐渐增加难度的关卡组成，基于各种防御措施来防止密码泄露，例如提示指令不要透露密码、过滤用户提示的输入护栏，以及阻止包含密码的响应的输出护栏。

# (3) 介绍 ARTKIT

随着大规模语言模型（LLM）系统变得越来越普及，确保模型在对抗性条件下仍能可靠地执行任务，从而建立用户信任变得至关重要。这正是**ARTKIT**在测试 LLM 系统的能力、公平性、安全性和保障性方面的用途。

ARTKIT 是一个开源框架，用于开发强大的自动化端到端管道，测试和评估基于 LLM 的应用程序，如聊天机器人和虚拟助手。

它在构建适合特定目的的管道方面的简洁性和灵活性，使其成为数据科学家和工程师进行 LLM 系统人机协作测试的优秀工具。

例如，ARTKIT 促进了 LLM 的有效使用，自动化红队演练中的关键步骤，例如生成对抗性提示来利用 LLM，并分析其响应以发现潜在的漏洞。

> 模拟攻击系统以发现其漏洞并改进安全性的结构化过程称为**红队演练**。它使组织能够从攻击者的角度理解潜在漏洞，从而加强对现实威胁的防御。

![](img/7fe0d3eb40497cdea6525577914f96cb.png)

ARTKIT 允许巧妙地使用生成性 AI（GenAI）模型，如 LLM，作为强大管道的一部分，自动化测试和评估 GenAI 系统 | 图片使用[Apache 许可证 2.0](https://github.com/BCG-X-Official/artkit/blob/1.0.x/LICENSE)

ARTKIT 的一个突出特点是它支持自动化的[多轮对话](https://bcg-x-official.github.io/artkit/user_guide/generating_challenges/multi_turn_personas.html)，攻击系统和目标系统之间的对话，我们将在本文中进行探索。

由于 LLM 系统可能在长时间对话中难以维持上下文和连贯性，因此能够扩展测试延长的多轮互动对于识别潜在漏洞至关重要。

# (4) 方法概述

这是我们演示 LLM 越狱方法的概览：

+   进行**基于模型**的**红队演练**，我们使用一个 LLM 模型攻击目标系统（即 Gandalf）。

+   利用 ARTKIT 和 OpenAI 的 GPT-4o 创建一个攻击者 LLM，该 LLM 在其对抗性提示中使用密码提取技术，并在进行多轮对话时直到密码泄露。

> OpenAI API 密钥可以在[API 密钥页面](https://platform.openai.com/api-keys)找到。

# (5) 步骤说明

让我们回顾一下使用 ARTKIT 提取 Gandalf 挑战中的密码的步骤。你可以在这里找到相关的 Jupyter notebook [**这里**](https://github.com/kennethleungty/ARTKIT-Gandalf-Challenge/blob/main/gandalf_challenge.ipynb)。

## (5.1) 安装 ARTKIT

ARTKIT 可以通过 PyPI（`pip install artkit`）或 Conda（`conda install -c conda-forge artkit`）安装。对于这个项目，我使用的是**版本 1.0.7**。

由于 ARTKIT 提供对 OpenAI 和 Anthropic 等流行模型提供商的开箱即用支持，因此无需单独安装这些软件包。

由于我们将利用外部模型提供商的服务，建议将访问密钥存储在 `.env` 文件中，并通过 `python-dotenv` 加载它们。执行步骤可以在 [这里](https://github.com/BCG-X-Official/artkit?tab=readme-ov-file#environment-variables)找到。

## (5.2) 加载依赖项

我们加载必要的依赖项和访问密钥：

## (5.3) 创建类以访问 Gandalf

为了方便与 Gandalf 背后的 LLM 进行交互，我们创建了一个名为 `GandalfChat` 的类，封装了与 Gandalf 聊天并处理消息格式化和响应处理所需的功能。

让我们更详细地看看 `GandalfChat` 类：

+   `GandalfChat` 继承自 ARTKIT 的 `HTTPXChatConnector` 类。由于 Gandalf 作为自定义 HTTP 端点提供服务，而不是一个独立的 LLM 对象，`HTTPXChatConnector` 使我们能够与其建立无缝连接。

+   `Level` 枚举结构化了难度级别，以便可以通过成员名称如 `LEVEL_01` 来引用。

+   `build_request_arguments` 格式化请求，将难度级别和输入提示等参数包含在内。

+   `parse_httpx_response` 根据 API 返回的 HTTP 响应对象处理 LLM 输出。

+   `get_default_api_key_env` 提供了存储聊天系统 API 密钥的环境变量名称。

除了支持像 Gandalf 这样的自定义系统外，ARTKIT 还提供了预构建的类，能够无缝连接到像 OpenAI 和 AWS Bedrock 这样的流行 LLM 平台。

这种集成的灵活性是 ARTKIT 另一个关键优势，能够高效地进行针对主流 LLM 的红队攻防。

> 有关 `*HTTPXConnector*` 的详细信息可以在本教程的 “[通过 HTTP 调用自定义端点](https://github.com/BCG-X-Official/artkit/blob/1.0.x/sphinx/source/user_guide/advanced_tutorials/creating_new_model_classes.ipynb)”部分找到。

## (5.4) 为 Gandalf 实例化聊天模型

我们创建一个 `GandalfChat` 的实例，作为一个模型对象，包含 Gandalf API 端点的 URL 和所需的难度级别。在这个例子中，我们将处理 Level 4。

我们还利用 ARTKIT 的 `CachedChatModel` 作为 `GandalfChat` 的包装器，将响应缓存到 SQLite 数据库（`gandalf_cache.db`）中。

存储这些聊天交互的优点在于，我们可以最大程度减少重复查询的冗余 API 调用，从而加快响应时间并降低成本。

我们还使用 `Timeout` 设置了 10 秒的截止时间，限制 API 响应的等待时间，确保我们的请求不会无限期挂起。

## (5.5) 设置攻击者 LLM

我们使用 OpenAI 的 GPT-4o 模型来破解 Gandalf，通过为 OpenAI LLM 设计的 `OpenAIChat` 类实例化它：

就像我们在 Gandalf 聊天对象中所做的那样，我们使用 `CachedChatModel` 来包装 GPT-4o LLM，从而启用响应缓存。

## (5.6) 定义攻击者 LLM 目标和系统提示

在攻击者 LLM 准备好后，我们继续进行提示工程，定义目标提示和攻击者系统提示。

因为我们将使用 ARTKIT 的多轮交互功能，所以需要明确指定一个单独的提示，用于描述攻击者 LLM 的目标（即让甘道夫透露其密码），以便使其响应得到良好的引导。

> 目标提示被保存为列表中的字典，因为我们可以存储并使用多个目标，以应对处理流程中不同的步骤。

接下来，我们定义系统提示，引导攻击者 LLM 提取密码的策略。提示设计得使攻击者 LLM 能够设计间接和创造性的技巧，误导甘道夫对询问的真正意图，从而绕过其防护措施。

请注意，系统提示中包含了`{objective}`参数，在其中目标提示会被动态注入。

此外，我们还需要包括以下两个动态参数，以便进行多轮交互：

+   `{max_turns}`：允许 LLM 完成目标的最大轮次，以防止其进行无休止的对话。

+   `{success_token}`：当 LLM 达成目标时输出的标记，作为提前终止对话的信号。

## (5.7) 与甘道夫进行多轮交互

我们现在距离开始对甘道夫进行越狱尝试只差一步；剩下的就是将各个组件连接起来并执行它们。

ARTKIT 的`run`函数允许我们协调执行处理流程中的一系列步骤，并返回执行流的结果。

下面是`ak.run`的参数：

+   `input`参数接受`objectives`变量，该变量包含了在前一步中定义的目标。

+   `steps`参数接受一组要执行的步骤，每个步骤都通过`ak.step`函数定义。在这个例子中，我们只有一个`ak.step`步骤要执行，即在攻击者 LLM（`challenger_llm`）和甘道夫（`target_llm`）之间进行多轮交互。

+   在`ak.step`函数中，我们使用`ak.multi_turn`来协调多轮对话，从而保持上下文和对话历史。

+   我们还指定了成功标记（`success_token`）、最大轮次（`max_turns`）和攻击者 LLM 系统提示（`attacker_prompt`）。

执行上面的代码将启动多轮交互，旨在突破甘道夫的防御，输出结果保存在`results`中。

## (5.8) 查看交互历史和秘密密码

执行越狱后，是时候回顾结果了。我们运行以下辅助代码以更清晰地结构化对话历史并输出结果。

现在，真相时刻到了！下面是我们的攻击者 LLM 和甘道夫之间交互的一个实例：

通过一系列巧妙的提示技术（例如生成谜语、提取字母），我们成功提取了隐藏的密码（即**UNDERGROUND**），尽管甘道夫竭力守护。

> 剧透：每个等级的密码可以在[这里](https://www.lakera.ai/blog/who-is-gandalf)找到。

# (6) 总结

在本文中，我们展示了如何使用 ARTKIT 进行基于提示的自动化测试，以揭示 LLM 系统中的越狱漏洞。利用 LLM 的能力进行基于模型的红队测试，提供了一个强大的手段来扩展和加速 LLM 系统的测试。

尽管我们在本次展示中聚焦于第 4 级，但 ARTKIT 设置能够顺利克服第 1 到第 6 级的挑战。更高等级则需要人工干预，涉及到高级提示工程和参数调整。

这突出了将自动化与人工主导的红队测试结合的重要性，自动化通过识别基本漏洞节省时间，使人类能够集中精力应对更复杂的风险。

人类监督的整合可以根据不同的复杂性水平进行定制，确保一个平衡且全面的测试方法。

# 在你离开之前

欢迎关注我的[Medium](https://kennethleungty.medium.com/)页面，并访问我的[GitHub](https://github.com/kennethleungty)，以便及时获取更多有趣和实用的内容。同时，享受使用 ARTKIT 进行 LLM 系统红队测试的乐趣吧！

+   [查看该项目的 GitHub 仓库](https://github.com/kennethleungty/ARTKIT-Gandalf-Challenge)

+   [访问 ARTKIT GitHub 仓库](https://github.com/BCG-X-Official/artkit)

+   [挑战甘道夫](https://gandalf.lakera.ai/)

[](https://levelup.gitconnected.com/inside-the-leaked-system-prompts-of-gpt-4-gemini-1-5-claude-3-and-more-4ecb3d22b447?source=post_page-----d2df5f56ece8--------------------------------) [## GPT-4、Gemini 1.5 和 Claude 3 泄露的系统提示内幕

### 揭示 OpenAI、Google 等 LLM 背后的提示工程](https://levelup.gitconnected.com/inside-the-leaked-system-prompts-of-gpt-4-gemini-1-5-claude-3-and-more-4ecb3d22b447?source=post_page-----d2df5f56ece8--------------------------------) [](https://betterprogramming.pub/text-to-audio-generation-with-bark-clearly-explained-4ee300a3713a?source=post_page-----d2df5f56ece8--------------------------------) [## Bark 的文本到音频生成，清晰解释

### 发现 Bark 的能力，这是一款开源的 GenAI 文本到语音模型](https://betterprogramming.pub/text-to-audio-generation-with-bark-clearly-explained-4ee300a3713a?source=post_page-----d2df5f56ece8--------------------------------)
