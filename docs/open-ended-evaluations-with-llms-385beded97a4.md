# 无监督的LLM评估

> 原文：[https://towardsdatascience.com/open-ended-evaluations-with-llms-385beded97a4?source=collection_archive---------2-----------------------#2024-11-02](https://towardsdatascience.com/open-ended-evaluations-with-llms-385beded97a4?source=collection_archive---------2-----------------------#2024-11-02)

## **大型语言模型输出评估实践指南**

[](https://medium.com/@volkot?source=post_page---byline--385beded97a4--------------------------------)[![Daniel Kharitonov](../Images/7d81129c1f88e4a0700462a342137227.png)](https://medium.com/@volkot?source=post_page---byline--385beded97a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--385beded97a4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--385beded97a4--------------------------------) [Daniel Kharitonov](https://medium.com/@volkot?source=post_page---byline--385beded97a4--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--385beded97a4--------------------------------) ·12分钟阅读·2024年11月2日

--

<TLDR>

> 评估AI生成的输出对于构建强大的大型语言模型应用至关重要，因为它可以将复杂的AI应用拆分为带有内建错误控制的简单阶段。
> 
> 在监督模式下评估生成输出相对简单，其中“正确答案”可以通过计算或由人工评估者提示。
> 
> 与此同时，在许多实际的LLM应用中，监督方法过于限制性，亟需能够应对开放式问题的评估方法。构建无监督评估器的最简单方法是让LLM自我评估。然而，生成模型在检测自己输出中的错误的能力尚未得到充分理解。
> 
> **我们证明了自我评估的质量可以通过迭代自我反思来提高**。类似于“思维链”技术，这种方法在推理时以计算量换取最终结果的稳健性。

</TLDR>

示例的Google Colab笔记本链接：

[https://colab.research.google.com/drive/1q_dChQBMbnUXZ377JVwYsjvn7lZ_7qlZ?usp=sharing](https://colab.research.google.com/drive/1q_dChQBMbnUXZ377JVwYsjvn7lZ_7qlZ?usp=sharing)

![](../Images/931a0e4ac56180b865ec6e8ed07dbc43.png)

图片来源：Flux 1\. 专业版提示“机器人评估其他机器人”

# 引言

在构建使用大型语言模型的处理管道时，常被提到的问题是生成输出的质量。如果有良好的评估过程，它可以突出表现不佳的情况，并触发LLM微调、提示调整、人工代理的介入——或者同时进行这些操作。

这里是一个典型的使用评估进行训练的工作流程：LLM遍历输入数据集，评估器检测到的任何输出差异都用来生成合成数据以微调模型。只有当目标质量指标达到时，应用程序才会部署。

![](../Images/a18dcaad57946ae5cada9bd8d2dea6c7.png)

作者提供的图像：LLM微调的评估循环

在生产环境中使用LLM评估器非常类似——只是检测到的差异通常会被发送给人工代理，以确保即使触发了错误标志，工作流仍然可以继续进行。

然而，构建一个好的LLM评估器并非易事。这个问题的复杂性来源于两个实际的限制：

**首先**，在评估中尽量减少人工参与是非常理想的。例如，想象一个聊天机器人与用户互动，并错过了一个常见的省略语模式（用一个词代替完整的输出句子）：

> *机器人：* 这是正确的吗？
> 
> *用户：* 正确
> 
> *机器人：* 对不起，我没有明白。请再试一次。
> 
> *用户：* 是的，这是正确的

给定这个对话部分，人工应该能够轻松地指出聊天机器人回答中的不足，并建议进行微调。然而，为了发现这个问题，评估者必须阅读整个对话（可能非常长）。这种方法在大规模应用中行不通——这意味着我们应该努力实现**无人工评估**。

**其次**，在不知道“真实答案”的情况下判断LLM输出的过程，其复杂度与原始任务相当。这意味着，最先进的LLM最多只能使用一个具有类似能力的评估器（很可能是它自己），从而引发关于**评估有效性**的问题。

# 监督评估

如果我们看一下今天广泛研究的LLM评估方法，我们会发现它们大多数集中在监督或半监督的应用场景中。

如果训练数据集附带“真实答案”，那么评估变得非常简单——甚至可以驱动像 [DSPy](https://github.com/stanfordnlp/dspy) 这样的优化框架。当测试企业LLM应用程序时，如果与历史案例进行对比（这些案例由人工代理处理），其中“真实答案”相当于那些代理的判断。

另一个检验输出与“真实答案”对比的机会出现在LLM输出可以被正式验证时——例如可以编译和测试的计算机代码。尽管计算机程序可以用多种不同的方式编写，但正确的代码应通过测试，无论选择哪种实现路径。

生成输出无法正式验证的情况通常需要将人工引入环路。例如，[RLHF](https://proceedings.neurips.cc/paper_files/paper/2017/hash/d5e2c0adad503c91f91df240d0cd4e49-Abstract.html) 可以根据人类偏好的序列对LLM输出进行评分，从而引导网络走向复杂且微妙的策略。

# 无监督自我评估

与此同时，有许多开放性评估案例，无法实现“地面真实”方法，而 RLHF 太长或太昂贵。这解释了人们对无监督自我评估技术的兴趣。

假设我们有一个开放性的大型语言模型评估问题，通常需要人工参与——比如“*这个聊天机器人如何改进*”——我们能做些什么来实现自动化呢？

如果我们假设当代的大型语言模型具备丰富的语义表示并且本身具有自我评估能力，那么就可以建立一个经济的评估框架。这意味着你可以直接要求模型评估其输出，或者使用另一个大型语言模型执行相同的任务，以避免它们的训练集之间的交叉污染。

**不幸的是，LLM 自我判断的简单实现可能会失败。** 其中有两个可能的原因。

**第一个原因** 是最显而易见的：**大型语言模型不能保证在其训练集中没有反映的主题上生成连贯的输出**，或者在无法通过检索工具（如网页搜索）获取的信息上生成连贯的输出。例如，考虑这个 Google 地图片段，它展示了加利福尼亚州一个名为 Mineral 的未合并小镇和一条小溪 (*Martin Creek*)，这条小溪流经该小镇。

![](../Images/5bdabfab93fe992dbe76ecf0dbb730e8.png)

Mineral, CA 附近（来源：Google 地图）

Mineral 小镇没有自己的维基百科页面，而且大多数大型语言模型在训练中不使用 GIS 数据，所以当被问到哪条小溪流经该小镇时，LLM 很容易产生幻觉：

![](../Images/399f7e82f5db8d091fac0196657dd68b.png)

GPT-4o 幻觉示例

*Mill Creek* 是一条真实的水体，绕过了 Mineral 所在的（更大的）普查指定地点。然而，*Mill Creek* 并没有穿过 Mineral 小镇本身，因此 GPT-4o 的回答是错误的。

> 直观地感觉，普通的 LLM 推理失败是因为主题超出了其知识集，并且无法通过常见工具（如网页搜索）获取，且这种情况不容易自我检测。

**第二个** 使自我评估失败的原因稍微微妙一些，且由**大型语言模型输出的概率特性**驱动。它在评估需要多次观察的情况下很容易展示。例如，考虑以下用户与聊天机器人的对话：

> *Bot:* 我是一个聊天机器人，帮助你找到最适合的手机套餐。
> 
> *User:* 我需要至少 500GB 的数据，且价格低于 $50
> 
> *Bot:* “Ultimate Freedom” 套餐提供无限制的互联网访问，价格为 $79.99
> 
> *User:* 这或许真的有效。
> 
> *Bot:* 你想更改当前的套餐吗？
> 
> *User:* “Ultimate Freedom” 仅限国内使用吗？我能开通国际漫游吗？
> 
> *Bot:* 对不起，我没听清楚。请再试一次。
> 
> *User:* 我现在已经好了，请不要做任何更改。
> 
> *Bot:* 我是一个聊天机器人，帮助你找到最适合的手机套餐。

如果我们反复要求LLM列出基于这段对话可以对机器人进行的改进，我们会注意到输出列表在不同运行之间并不稳定。一个版本可能是这样的：

```py
{["Ensure the bot can handle follow-up questions after suggesting a plan.", 
"Improve bot's recognition of farewell messages.", 
"Make sure the bot can assist with additional features or package inquiries."]} 
```

另一种可能是这样的：

```py
{["Ensure the bot can switch contexts and answer follow-up questions.", 
  "Enable the bot to recognize when a conversation ends.", 
  "Improve the bot's ability to provide options for additional services", 
  "Support input about cost limitations, e.g. users stating a desired price."]}
```

尽管这两个答案有很大重叠，但显然在任何一种情况下都没有生成类似于人类专家可能给出的那种详尽的建议列表。

# 自我反思的意外力量

一旦我们概述了评估的典型失败模式，使用LLM来评判自己似乎是个坏主意。毕竟，这听起来像是在要求一个勤奋的学生重新检查自己的答案。因为一个好学生不会犯很多拼写错误，重新检查仅仅反映现有的知识，不应导致改进。

*然而，这正是我们对LLM的直觉可能完全错误的地方。*

**事实上，大多数LLM都能进行纠正性自我评估，即使主题超出了它们的知识库**。

为了说明这一现象，我们回到GPT-4o的例子，其中幻觉涉及穿过矿物镇的水体*，*加州。有趣的是，这种特定的幻觉可以在自我评估过程中被消除：

![](../Images/41f9009c7df5bb633369a78802a7b35d.png)

GPT-4o中的自我评估能够逆转幻觉

**那么，魔力在哪里呢？**

在这个例子中，LLM没有知识或工具来得到正确的答案，所以它幻觉出“最有可能”的完成。然而，当被要求自我评估时，它得出结论：它可以访问的事实与之前的陈述并不一致。即使GPT-4o不知道正确答案，它也能否定错误的答案。

一个更复杂的模型（比如GPT-4o1）可能稍微难以以相同的方式处理，因为它倾向于生成更细致的回应：

![](../Images/021034203126a41c3695f70bef6a4b23.png)

GPT-4o1中的幻觉更加细致。

与其在自己无法验证的主题上产生幻觉，GPT-4o1可能会选择回答一个它从未被问到的问题——比如“*矿物镇附近流经的主要水体是哪一条？*”。这种回避意味着，像“*判断对错*”这样的直接自我评估提示可能会失败。

然而，以一种更加深思熟虑的方式要求自我评估，仍然可能成功，即使这需要多次迭代：

![](../Images/62ffba261147d669cf855fc565785bc2.png)

LLMs以迭代方式自我反思的能力，当然是众所周知的，并且在代码生成等应用中已经有所应用，我们这里只是将相同的技巧扩展到自我评估。

# 记忆化的“预期”能力

迭代反思的相同思路也适用于那些倾向于生成不完整输出的LLM任务。如果我们回顾机器人对话示例，并允许LLM在记忆化改进列表上进行迭代，我们将发现模型通常不会对第一次的结果“满意”。

换句话说，如果我们制定一个这样的提示：

```py
iterative_prompt = """
Consider the following dialog between the user and the chatbot.
The bot's goal is to suggest a cheaper mobile plan based on the information the user provides.
The user's responses are not guaranteed to be consistent or coherent at all times.

This dialog was evaluated by an LLM and this evaluation is provided below. 

You job is to assess the quality of evaluation and respond with "success"=True and repeat the original action list if there is nothing significant to add.
If there is something missing in evaluation, respond with "success"=False and a new list of action items to create better user experience integrating the old list with new suggestions. Make sure the list items are unique and not repetitive.

"""
```

然后通常需要对改进列表进行2到4轮的检查，直到LLM得出推荐并宣布评估任务成功：

```py
🍩 
success='False' action_items=['Enable bot to understand user inquiries about add-on packages related to international calls.', "Improve bot's understanding to handle informal or casual goodbyes such as 'byebye'."]
🍩 
success='False' action_items=['Enable bot to understand user inquiries about add-on packages related to international calls.', "Improve bot's understanding to handle informal or casual goodbyes such as 'byebye'.", "Enhance the bot's capability to suggest plans that are closer to the user's budget, such as recommending plans around $10 instead of $14 when the user specifies a $10 budget."]
🍩 
success='False' action_items=['Enable bot to understand user inquiries about add-on packages related to international calls.', "Improve bot's understanding to handle informal or casual goodbyes such as 'byebye'.", "Enhance the bot's capability to suggest plans that are closer to the user's budget, such as recommending plans around $10 instead of $14 when the user specifies a $10 budget.", 'Ensure the bot confirms if the user is interested in plans without inclusive international minutes given their travel habits.', 'Add functionality for the bot to suggest alternative communication methods like VoIP for international calls if budget constraints are strict.', "Improve the bot's ability to suggest plans that balance cost with user requirements, such as considering travel habits and required features."]
🍩 
success='True' action_items=['Enable bot to understand user inquiries about add-on packages related to international calls.', "Improve bot's understanding to handle informal or casual goodbyes such as 'byebye'.", "Enhance the bot's capability to suggest plans that are closer to the user's budget, such as recommending plans around $10 instead of $14 when the user specifies a $10 budget.", 'Ensure the bot confirms if the user is interested in plans without inclusive international minutes given their travel habits.', 'Add functionality for the bot to suggest alternative communication methods like VoIP for international calls if budget constraints are strict.', "Improve the bot's ability to suggest plans that balance cost with user requirements, such as considering travel habits and required features."]
```

在这一初步的“一轮热身”对话后，我们可以给模型提供更多的示例对话，看看会发生什么。

与人类评估者的做法类似，GPT-4o模型认为许多对话样本不值得生成新的推荐（一次模型运行就足够了）——然而，有些可能会引发更长时间的深思：

![](../Images/9632e0145ef5a5bbe01f4aae0ff708e3.png)

来自[ExpBot数据集](https://radar.kit.edu/radar/en/dataset/FdJmclKpjHzLfExE.ExpBot%2B-%2BA%2Bdataset%2Bof%2B79%2Bdialogs%2Bwith%2Ban%2Bexperimental%2Bcustomer%2Bservice%2Bchatbot)的前50个对话中的LLM调用次数，直到收敛（图表由作者提供）

最终的结果将是一个相当详尽的关于改进聊天机器人的推荐列表：

```py
Final recommendations: 

["Improve the bot's ability to avoid repetitive greetings and restarts when the user's input is vague or repeated, creating a more fluid conversation flow.", 
"Enhance the bot's active listening skills to acknowledge user needs and concerns before suggesting starting over, to better handle user dissatisfaction.", 
"Include a function allowing users to ask follow-up questions for more details about the suggested plan, such as data overage charges and roaming fees.", 
"Develop a mechanism for the bot to detect and correct minor typographical errors and currency symbol mismatches in user inputs.", 
"Provide alternative suggestions that might not fit all criteria but offer significant savings or benefits in other areas based on the provided user data.", 
"Implement a feedback system enabling users to rate the accuracy or helpfulness of the plan suggestion provided, allowing for iterative improvements.", 
"Incorporate a bot training mechanism to ensure it can handle responses that are non-standard in format or include extraneous details not directly related to the plan.", 
"Add the ability for the bot to suggest seeking human assistance when complex queries or dissatisfaction arise that the bot cannot resolve.", 
"Enhance the bot's language processing capabilities to accurately interpret various phrasings and informal expressions from the user.", 
"Increase the bot's capability for dynamic clarification requests, creating a smoother interaction flow.", 
"Refine the bot's ability to verify user information effectively to reduce misunderstandings and user frustration.", 
"Improve the bot's handling of unrealistic and inconsistent user inputs to guide the conversation back to relevant queries.", 
"Integrate a process for flagging nonsensical data entries and guide the user toward providing accurate information.", 
"Provide clearer explanations or breakdowns of the suggested plan's features, especially if different from the user's mentioned requirements.", 
"Improve response to questions unrelated to starting new calculations to avoid redundant loops."]
```

关于此示例的一些技术说明：

1.  为了简化，我们将评估和生成合并为一个提示，依赖于[OpenAI的结构化输出](https://platform.openai.com/docs/guides/structured-outputs)来生成期望的结果。

1.  记忆化的固有限制在于需要按顺序处理样本。这在处理大型数据集时可能需要一些时间，并且还阻止我们通过[批处理调用](https://cookbook.openai.com/examples/batch_processing)使用低成本推理。

为了进一步提高性能，我们可以利用这样一个事实：数据集中的大多数样本并不会生成新的见解。这意味着我们可以通过按顺序迭代一个小子集的样本来生成初步的推荐列表，并通过[DataChain库](https://github.com/iterative/datachain)（或通过[OpenAI API](https://cookbook.openai.com/examples/batch_processing)批量处理）并行服务剩余的数据集，以标记“有趣”的案例，从而节省30%到50%的时间（或费用），具体取决于您的偏好。

# 结论

LLM可以且应该用于无监督评估（包括自我评估）。关键是它需要一个经过深思熟虑的方法——这通常会发展成一种迭代的方式来改进和完善判断。

这是Google Colab中示例实现的链接：

[https://colab.research.google.com/drive/1q_dChQBMbnUXZ377JVwYsjvn7lZ_7qlZ?usp=sharing](https://colab.research.google.com/drive/1q_dChQBMbnUXZ377JVwYsjvn7lZ_7qlZ?usp=sharing)
