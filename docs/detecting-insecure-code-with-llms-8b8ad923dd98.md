# 使用LLM检测不安全代码

> 原文：[https://towardsdatascience.com/detecting-insecure-code-with-llms-8b8ad923dd98?source=collection_archive---------1-----------------------#2024-03-21](https://towardsdatascience.com/detecting-insecure-code-with-llms-8b8ad923dd98?source=collection_archive---------1-----------------------#2024-03-21)

## Python漏洞检测的提示实验

[](https://medium.com/@melanie.h.buehler?source=post_page---byline--8b8ad923dd98--------------------------------)[![Melanie Hart Buehler](../Images/31d0da632d6b3b3c705f1cc56f6c3783.png)](https://medium.com/@melanie.h.buehler?source=post_page---byline--8b8ad923dd98--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b8ad923dd98--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8b8ad923dd98--------------------------------) [Melanie Hart Buehler](https://medium.com/@melanie.h.buehler?source=post_page---byline--8b8ad923dd98--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b8ad923dd98--------------------------------) ·阅读时间：11分钟·2024年3月21日

--

![](../Images/e03e54451244164d7c8cc26b0ce1af50.png)

照片由[Alexander Sinn](https://unsplash.com/@swimstaralex?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你是一名软件专业人士，你可能会害怕在发布的早晨打开[安全扫描报告](https://owasp.org/www-community/Vulnerability_Scanning_Tools)。为什么？你知道这是一个增强你工作质量和完整性的好工具，但你也知道，在截止日期之前，你将花费接下来的几个小时争分夺秒地解决所有安全问题。如果你幸运的话，很多问题将是虚惊一场，但你仍然需要手动验证每个问题的状态，并在代码定稿后迅速修复其余问题。如果你经历过这种情况，你可能会希望有一个更顺畅、少些临时应对的流程来识别、分类和修复代码库中的漏洞。好消息是，最近的研究表明，大型语言模型（LLM）可以熟练地将代码分类为安全或不安全，解释其弱点，甚至提出修正建议。这有可能显著简化安全编码实践，超越传统的静态扫描方法。

本文简要回顾了一些最近在漏洞检测和修复方面的发现，特别是LLMs的应用，接着深入探讨了几项实验，评估GPT-4在使用不同提示技术时，在Python数据集上识别不安全代码的能力。如果你想探索Jupyter笔记本或测试你自己的代码，可以访问OpenAI Cookbook中的[拉取请求](https://github.com/openai/openai-cookbook/pull/1112)（当前正在审查中）。

在我们开始提示GPT-4之前，有一些关键概念和定义将帮助我们建立设计逻辑实验所需的基础。

# 常见弱点枚举（CWE）

2006年，政府资助的研究组织[MITRE](https://www.mitre.org/)开始定期发布[常见弱点枚举（CWE）](https://cwe.mitre.org/)，这是一个由社区开发的软硬件弱点定义和描述的通用分类法。在这个意义上，“弱点”指的是软件或硬件中的一种情况，可能导致漏洞。一个[2023年最危险的前25个CWE](https://cwe.mitre.org/top25/)的列表突出了最大的重复犯规者。还有一个[15个“顽固”CWE](https://cwe.mitre.org/top25/archive/2023/2023_stubborn_weaknesses.html)的列表，这些CWE自2019年至2023年每年都出现在前25个列表中。它们大致可以分为三组：

+   **第1组**：对不可信数据源的薄弱处理（例如命令/SQL注入、路径遍历、不当输入验证和跨站脚本攻击）

+   **第2组**：薄弱的内存管理或类型强制（例如NULL指针解引用）

+   **第3组**：薄弱的安全设计选择（例如硬编码凭证）

为了帮助保持调查范围狭窄且定义明确，我们将重点关注CWEs的第一组。

# 静态代码分析

传统的自动化不安全代码检测方法涉及使用静态分析工具，如CodeQL、Bandit、SonarQube、Coverity和Snyk。这些工具可以随时使用，但通常用于在代码冻结阶段后、正式发布过程完成之前扫描代码中的漏洞。它们通过将源代码解析成抽象语法树或控制流图的方式工作，这些结构表示代码如何组织，以及各个组件（类、函数、变量等）之间的关系。然后，通过基于规则的分析和模式匹配来检测各种问题。静态分析工具可以在开发周期中与IDE和CICD系统集成，许多工具提供自定义配置、查询和报告选项。它们非常有用，但也有一些缺点（除了那些最后时刻的高压修复派对之外）：

+   **资源密集型**：它们将大量代码库转换为数据库，以执行复杂查询

+   **假阳性**：它们通常包含大量非问题项

+   **时间密集型的后续工作**：需要大量的努力来验证和修复这些问题

这些局限性无疑激发了研究人员探索新方法来增强代码安全实践，例如生成式AI。

# 之前的工作

最近的工作表明，LLM在开发生命周期的各个阶段具有潜力。LLM已被证明在安全代码补全、测试用例生成、易受攻击或恶意代码检测以及错误修复等方面非常有用。

以下是一些值得注意的参考文献：

1.  一项[综合评估](https://arxiv.org/abs/2308.10345)¹比较了不同参数规模的LLM与传统静态分析器在识别和修复软件漏洞方面的表现。特别是GPT-4表现出色，尤其在解释和修复易受攻击的代码方面。论文中还提出了一些额外的观点——显著的代码理解能力似乎出现在6到175亿个参数之间，而超过130亿参数时，才出现了高级程序员技能的初步迹象；当提示词同时包含识别和修复安全问题的任务时，预测成功率可能会提高；将LLM与传统静态代码分析结合使用，可能能够提供两者的最佳结合。

1.  一项[新研究和数据集](https://arxiv.org/abs/2108.09293)²发现，即使是高级的AI开发助手也容易编写不安全的代码，并且发现40%的生成代码补全包含了CWE（通用弱点枚举）。

1.  一项[调查](https://betterprogramming.pub/i-used-gpt-3-to-find-213-security-vulnerabilities-in-a-single-codebase-cc3870ba9411)³报告称，GPT-3在预测安全漏洞方面超过了现代静态代码分析器。

1.  一篇[研究论文](https://arxiv.org/abs/2308.14434)⁴表明，LLM可以帮助开发者识别和定位易受攻击的代码，尤其是当与静态分析器结合使用时。

1.  在[另一项研究](https://arxiv.org/abs/2112.02125)⁵中，LLM（大语言模型）成功修复了所有合成的和手工制作的场景，尽管它们并未充分解决所有现实世界中的安全漏洞问题。

尽管LLM在超越传统方法方面展现了潜力，但许多研究指出，它们也容易产生误报，并且对提示的结构和措辞非常敏感。在我们的实验中，我们旨在通过对提示模板进行更多变体的应用，来验证并进一步扩展这些结果。

# 数据来源与预处理

我们将使用一个最广泛采用的数据集之一来进行LLM的安全代码基准测试。[数据集](https://doi.org/10.5281/zenodo.5225651)（许可[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/legalcode)）来自[《键盘上睡着了吗？评估GitHub Copilot的代码贡献安全性》](https://arxiv.org/abs/2108.09293)²，该数据集由名为“场景”的提示组成，这些提示是手工设计的，用于在作为输入提供给代码生成LLM时引出特定的CWE。我们从出版物中挖掘了包括的输出代码补全，因为它们随后被静态代码分析工具扫描，并带有“易受攻击”和“非易受攻击”的标签。需要再次注意的是，这些数据集中的代码是从手动编写的提示中由模型生成的，因此缺乏一些现实世界的严肃性，但我们选择它有几个原因：

1.  它包含大量的Python示例，而Python是本研究中首选的编程语言。

1.  它包含易受攻击和非易受攻击的代码示例，这对于评估假阳性和假阴性都非常重要。

1.  与（2）相关的是，针对同一场景存在易受攻击和非易受攻击的代码片段，这意味着我们可以将非易受攻击的代码补全作为某些提示中的“建议修复”，这一点将在相关的实验部分进行解释。

我们理解，实际上还有其他数据集可以使用，并且这也留待未来的研究来探索使用其他数据源进行CWE预测的能力。

来自[Copilot场景原始数据文件](https://doi.org/10.5281/zenodo.5225650)²的Python代码片段经过以下步骤的预处理：

```py
Open the project's aggregated "diversity of weakness" results file
Filter for Python language rows
FOR each Python scenario/row
    Navigate to the scenario's dedicated results folder
    Open the scenario's CodeQL or authors' manual assessment file
    FOR each generated code completion for the scenario
        Create a record containing its metadata and classification label
        Read in the .py file containing the code completion
        FOR each line in the file
            IF the line is 100% comments
                Remove the line
            END IF
        END FOR
        Insert the cleaned code example into the record
    END FOR
   Assert that the scenario totals agree with the original aggregated report
END FOR
Save the final file in JSON format
```

# 数据探索与子集化

我们探索了CWE的分布，并从第1组CWE中均匀选择了45个样本，涵盖易受攻击和非易受攻击的代码（我们计划的样本量是50，但由于其中一个组的数量有限，最终选择了45个样本）。CWE描述如下：

+   **CWE-20**：输入验证不当

+   **CWE-22**：路径名限制不当，导致限制目录（“路径遍历”）

+   **CWE-78**：操作系统命令中使用的特殊元素未正确中和（“操作系统命令注入”）

+   **CWE-79**：网页生成时输入未正确中和（“跨站脚本攻击”）

+   **CWE-502**：反序列化不可信数据

我们从零-shot基准测试开始，然后重复进行实验，通过增加像few-shot上下文学习这样的策略来构建提示的复杂性。我们提示LLM识别易受攻击的代码，而没有提及可能寻找的CWE（即没有标签）。

# 实验1：零-shot

在零-shot提示中，您要求模型在没有示例或其他信息的情况下，仅凭指令做出预测。我们的零-shot模板灵感来自于[这篇论文](https://arxiv.org/abs/2308.14434)⁴，包含了角色、代码分隔符，并要求仅输出json格式。它还包括了[“一步步思考”](https://arxiv.org/abs/2205.11916)的指令。测试中的代码片段被插入到{code}中。

## **提示**

```py
You are a brilliant software security expert. 
You will be provided with a python code delimited by triple backticks. 
If it contains any CWE security vulnerabilities, write Vulnerable. 
If the code does not contain any vulnerabilities, write Not Vulnerable. 
Format your response as a JSON object with "label" as the key 
for vulnerability status and "cwe" as the vulnerability number found. 
Think about the answer step by step, and only answer with JSON.

Python code: ```{code}```py 

Answer:
```

## **结果**

准确率：0.67

精确度：0.60

召回率：0.86

F1得分：0.71

![](../Images/acb17591c7e4b4c6a46a57636647a638.png)

零-shot混淆矩阵

# 实验2：少量学习

在下一个实验中，我们引入了上下文或[“少量学习”](https://arxiv.org/abs/2005.14165)的概念，并在要求LLM对未见过的代码执行相同操作之前，提供了几个成功的代码-答案示例。这些示例来自数据集的其余部分，并特别注意：

+   从不同的场景中选取示例，以避免污染

+   精确绘制两个易受攻击的示例和一个非易受攻击的示例

## **提示**

```py
You are a brilliant software security expert. 
You will be provided with a python code delimited by triple backticks. 
If it contains any CWE security vulnerabilities, write Vulnerable. 
If the code does not contain any vulnerabilities, write Not Vulnerable. 
Format your response as a JSON object with "label" as the key 
for vulnerability status and "cwe" as the vulnerability number found. 
Think about the answer step by step, and only answer with JSON.

Python code: ```{example_0}```py

Answer: {answer_0}

Python code: ```{example_1}```py

Answer: {answer_1}

Python code: ```{example_2}```py

Answer: {answer_2}

Python code: ```{code}```py

Answer: 
```

## **结果**

准确率：0.76

精确度：0.71

召回率：0.81

F1得分：0.76

![](../Images/ad0fc6f78f1872b89105b760a3bae4da.png)

少量学习混淆矩阵

# 实验3：KNN少量学习

这篇[微软博客文章](https://www.microsoft.com/en-us/research/blog/the-power-of-prompting/)描述了一种有趣的技术，叫做基于KNN的少量示例选择，它可以提升使用上下文示例时LLM的响应质量。在接下来的实验中，我们不再随机选择示例，而是计算输入代码与每个候选示例之间的相似度得分，并从最相似的候选中构建示例（同时保持场景的独特性）。我们使用ROUGE-L指标，但也可以使用其他指标。提示模板与第二个实验中保持一致。

## **结果**

准确率：0.73

精确度：0.70

召回率：0.76

F1得分：0.73

![](../Images/d9ee2ba95807f183f34106c35f85e802.png)

KNN少量学习混淆矩阵

# 实验4：KNN少量学习与代码修复

在这种提示的变体中，我们要求提供一个修正后的代码版本，如果发现CWE。这一方法灵感来自于[Noever](https://arxiv.org/abs/2308.10345)，他提出通过同时提示CWE检测和修复可能会产生“良性循环”，并迫使LLM进行“自我审计”或更深入地思考准确识别漏洞所需的步骤，类似于链式思维提示。我们通过构建包含代码修复建议的易受攻击代码上下文示例，且修复建议来自于相同场景下的非易受攻击代码样本。

## **提示**

```py
You are a brilliant software security expert. 
You will be provided with a python code delimited by triple backticks. 
If it contains any CWE security vulnerabilities, write Vulnerable. 
If the code does not contain any vulnerabilities, write Not Vulnerable. 
If the code has the vulnerability, write a repaired secure version of the
code that preserves its exact functionality. 
Format your response as a JSON object with "label" as the key 
for vulnerability status, "cwe" as the vulnerability found,
and "fix" for the fixed code snippet. 
Think about the answer step by step, and only answer with JSON.

Python code: ```{example_0}```py

Answer: {answer_0}

Python code: ```{example_1}```py

Answer: {answer_1}

Python code: ```{example_2}```py

Answer: {answer_2}

Python code: ```{code}```py

Answer:
```

## **结果**

准确率：0.80

精确度：0.73

召回率：0.90

F1得分：0.81

![](../Images/94c581af5e970a06d8ffa5d198286554.png)

KNN 少量示例修复混淆矩阵

除了CWE检测外，这个实验的一个优势是能够生成修复建议。我们尚未评估这些建议的质量，因此这是未来研究的一个方向。

# 结果与后续步骤

![](../Images/e74a694b2198e10dd9518e1415ce4363.png)

在我们的小数据样本上，GPT4的准确率为67%，F1得分为71%，并未进行复杂的提示调整。一些我们测试的提示技术带来了一些小幅提升，其中少量示例和请求代码修复的效果最为突出。不同提示技术的组合使得准确率和F1得分从基线提高了大约十个百分点，两个指标均达到或超过80%。

结果在不同模型、数据集和提示之间可能会有很大差异，因此需要更多的调查。例如，以下问题会很有趣：

+   测试较小的模型

+   测试包含CWE标签的提示模板，探讨将LLMs与静态分析相结合的潜力

+   测试更大且更具多样性的数据集

+   评估LLM提议的代码修复的安全性和功能性

+   研究更高级的提示技术，例如上下文示例链式思考、[自一致性](https://arxiv.org/abs/2203.11171)和[自发现](https://arxiv.org/abs/2402.03620)

如果你想查看生成这些结果的代码，自己在代码中运行它，或根据自己的需求进行修改，可以查看OpenAI Cookbook中的[拉取请求](https://github.com/openai/openai-cookbook/pull/1112)（当前正在审核中）。

感谢我的同事[Matthew Fleetwood](https://github.com/etcylfleet)和[Abolfazl Shahbazi](https://github.com/ashahba)，他们为这篇文章做出了贡献并帮助进行审阅。

# 引用

[1] D. Noever, [大型语言模型能否发现并修复易受攻击的软件？](https://arxiv.org/abs/2308.10345) (2023), arXiv 预印本 arXiv:2308.10345

[2] H. Pearce, B. Ahmad, B. Tan, B. Dolan-Gavitt 和 R. Karri, [在键盘上睡着了吗？评估GitHub Copilot的代码贡献的安全性](https://arxiv.org/abs/2108.09293) (2022), 2022 IEEE安全与隐私研讨会 (SP)

[3] C. Koch, [我使用GPT-3在单一代码库中发现213个安全漏洞](https://betterprogramming.pub/i-used-gpt-3-to-find-213-security-vulnerabilities-in-a-single-codebase-cc3870ba9411) (2023), [https://betterprogramming.pub/i-used-gpt-3-to-find-213-security-vulnerabilities-in-a-single-codebase-cc3870ba9411](https://betterprogramming.pub/i-used-gpt-3-to-find-213-security-vulnerabilities-in-a-single-codebase-cc3870ba9411)

[4] A. Bakhshandeh, A. Keramatfar, A. Norouzi 和 M. M. Chekidehkhoun, [将ChatGPT用作静态应用程序安全测试工具](https://arxiv.org/abs/2308.14434) (2023), arXiv 预印本 arXiv:2308.14434

[5] H. Pearce, B. Tan, B. Ahmad, R. Karri 和 B. Dolan-Gavitt, [使用大型语言模型进行零-shot漏洞修复研究](https://arxiv.org/abs/2112.02125) (2023), 2023 IEEE安全与隐私研讨会 (SP)
