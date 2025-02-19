# 大型语言模型（LLMs）能否用于数据标注？

> 原文：[`towardsdatascience.com/can-large-language-models-llms-label-data-2a8334e70fb8?source=collection_archive---------5-----------------------#2024-04-02`](https://towardsdatascience.com/can-large-language-models-llms-label-data-2a8334e70fb8?source=collection_archive---------5-----------------------#2024-04-02)

## 简要概述

[](https://medium.com/@majapavlo?source=post_page---byline--2a8334e70fb8--------------------------------)![Maja Pavlovic](https://medium.com/@majapavlo?source=post_page---byline--2a8334e70fb8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2a8334e70fb8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2a8334e70fb8--------------------------------) [Maja Pavlovic](https://medium.com/@majapavlo?source=post_page---byline--2a8334e70fb8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2a8334e70fb8--------------------------------) ·9 分钟阅读·2024 年 4 月 2 日

--

本文旨在提供一个简单易懂的总结，概述了通过大型语言模型（LLMs）进行数据标注的研究**¹****。** 我们将讨论目前关于使用 LLMs 标注文本数据的观点，并且提出一些在自己的项目中需要考虑的事项。

**概述：**

+   为什么要使用 LLM？

+   当前观点

+   使用 LLMs 作为标注工具时需要考虑的事项

+   总结 | ***简而言之***

![](img/eb85d873b755934ed4cb4b9ae8915e2d.png)

来源：Pexels

# 为什么要使用 LLM 进行标注？

高质量的标注数据为训练和评估机器学习模型在不同任务中的表现奠定了基础。目前，标注数据集的最常见方法是雇佣人群工人*(例如亚马逊机械土耳其人)*，或者在需要专业知识时雇佣领域专家。

这些方法通常既昂贵又耗时，这也是为什么许多人现在在思考 LLMs 是否足够胜任数据标注的原因。预算有限的企业可以通过构建专门的模型来满足其特定需求，从中受益。在医疗等敏感领域，通过让专家审核和修正 LLM 标签，而不是从零开始，可能有助于加速标注过程。

此外，卡内基梅隆大学和谷歌的研究人员发现，人们受到保护人工标注者免受标注过程中可能造成的心理伤害*(例如仇恨言论)*以及数据中观点多样性的动机，可能有助于推动这一领域的发展。

# 各个研究中的当前观点

关于 LLMs 作为注释员的潜力，研究之间的意见有所分歧。尽管一些研究对其能力持乐观态度，但也有其他研究仍然保持怀疑。表 1 提供了来自 12 项研究的方法和结果概述。您可以在参考文献中找到这些研究的来源（*见本文末尾*）。

![](img/79bc9b2ff8ff92d0ee358b024f25a71f.png)

**表 1** — z:零样本，f:少样本，z&f:零样本&少样本；en+:主要为英语 | 图片由作者提供

## Model²

**模型家族的数量**突出了大多数研究仅测试一个模型家族，且当我们查看它们使用了哪些模型时，可以看到几乎所有的研究（除了 2 项）**都使用了 GPT**。研究[7]是唯一专注于探索开源 LLM 的研究（表 2）。

![](img/3fff610c6de85ad358c8b378fca9f6ff.png)

**表 2** | 图片由作者提供

## 数据集

表 1 的第三列包含了用于标注目的的**数据集数量**。不同的研究探索了不同的任务，从而也涉及了多种数据集。大多数研究探索了多个数据集上的表现。研究[3]特别突出，因为它测试了 LLM 在 20 个不同数据集上的分类性能。更多关于使用了哪些数据集的详细信息请见下方的表 3，这可以帮助您找到与您最相关的研究。

![](img/03cdf5b34f7dda8b4d5fe27aa3809e46.png)

**表 3** | 图片由作者提供

> **如果您手头没有标注数据：** 请查看现有的**标注**数据集，选择与您的任务相似的任务，并使用 LLM 对数据进行标注。通过详细检查错误和潜在问题，将 LLM 生成的标签与人工标签进行对比。这将帮助您了解 LLM 在您的任务中表现如何，以及时间和成本节省是否能够得到合理的证明。

## 观点主义方法

**观点主义方法**意味着认识到理解数据集或解决问题没有唯一的“正确”方式。不同的视角可以揭示不同的见解或解决方案。与此相对，传统上，大多数数据集是使用多数投票方法进行标注的，即视为“地面真相”的是最常被选择的标签：

![](img/134e765061c32993d940b03ea01b0776.png)

多数投票与观点主义方法 | 图片由作者提供

在表 1 中，标注方法根据研究是否使用多数投票或观点主义思维方式进行分类。我们可以看到，大多数研究在标注工作中采取了多数投票方法。

## LLM 作为注释员？

最后一列总结了每项研究的发现，勾选的标记表示倾向于认为 LLM 在标注过程中可以发挥作用。虽然有些人对其潜力相当乐观，建议替代人工标注员，但也有一些人认为它们更像是一种支持工具，而非人类的替代品。无论如何，即使是在这些对 LLM 持积极态度的研究中，仍有*某些任务*是 LLM 表现不佳的。

此外，三项研究中有两项采用视角主义方法，得出的结论是它们不适合用于数据标注。另有一项研究（*未包含在表格中*）采用了不同的方法，并显示当前通过单一奖励函数对大语言模型（LLM）进行对齐的方式并未能捕捉到不同人类子群体之间的偏好差异，尤其是少数群体的观点。

# 使用 LLM 作为标注者时需要考虑的事项

## 提示：零样本与少样本

从大语言模型（LLM）中获得有意义的回答可能会有些挑战。那么，如何最佳地提示一个 LLM 来标注数据呢？正如我们从表格 1 中看到的，上述研究探讨了零样本提示、少样本提示或两者结合的方式。**零样本**提示是在没有提供任何示例的情况下期望 LLM 给出答案。而**少样本**提示则在提示中包含多个示例，使 LLM 知道期望的回答是什么样子的：

![](img/37392df9567b3466d7f3ec16c74c5dea.png)

零样本与少样本提示 | [示例来源 (amitsangani)](https://github.com/amitsangani/Llama-2/blob/main/Building_Using_Llama.ipynb) | 图片由作者提供

各项研究在关于哪种方法能获得更好结果的问题上存在分歧。有些在他们的任务中使用了少样本提示，另一些则使用了零样本提示。因此，你可能需要探索哪种方法最适合你的特定用例和模型。

> 如果你还在想如何开始进行有效的提示，[Sander Schulhoff](https://medium.com/u/94d7839def70?source=post_page---user_mention--2a8334e70fb8--------------------------------) 和 [Shyamal H Anadkat](https://medium.com/u/e3a305374f9a?source=post_page---user_mention--2a8334e70fb8--------------------------------)创建了[**LearnPrompting**](https://learn-prompting.webflow.io/testimonials)，它可以帮助你掌握基础知识，也能教你一些更高级的技巧。

## 提示：敏感性

LLMs 对提示的微小修改非常敏感。改变提示中的一个单词可能会影响回应。如果你希望在某种程度上考虑这种变化，可以参考研究[3]中的方法。首先，让任务专家提供初始提示。然后，使用 GPT 生成 4 个具有相似意义的提示，并对这 5 个提示的结果进行平均。或者，你也可以尝试远离手写提示，替换为签名，由 DSPy 来优化提示，正如[Leonie Monigatti](https://medium.com/u/3a38da70d8dc?source=post_page---user_mention--2a8334e70fb8--------------------------------)的博客文章中所示。

## 模型选择

![](img/47c78f3eb88071c5102e84d4fb571100.png)

你应该选择哪个模型来标注你的数据集？这里有几个因素需要考虑。我们简要讨论一些关键的考量点：

+   **开源与闭源：** 你是选择最新的、表现最好的模型吗？还是更重视开源定制？你需要考虑预算、性能要求、定制和拥有权偏好、安全需求以及社区支持需求等因素。

+   **安全防护：** LLMs 内置了安全防护机制，以防止它们生成不良或有害内容。如果你的任务涉及敏感内容，模型可能会拒绝标注你的数据。此外，LLMs 在安全防护的强度上有所不同，因此你应该进行探索和比较，以找到最适合你任务的模型。

+   **模型大小：** LLMs 有不同的规模，更大的模型可能表现更好，但也需要更多的计算资源。如果你倾向于使用开源 LLMs 且计算资源有限，可以考虑量化。对于封闭源模型，目前较大的模型每次提示的费用更高。但更大就一定更好吗？

## 模型偏见

根据研究[3]，较大的、经过指令调优的**³**模型在标注表现上更为出色。然而，研究并未评估其结果中的偏见。另一项研究表明，偏见随着规模的增大和模糊上下文的增加而增加。多项研究也警告，模型可能存在偏左倾向，并且在准确表达少数群体（例如老年人或代表性不足的宗教群体）的观点时能力有限。总的来说，当前的大型语言模型（LLMs）展示了显著的文化偏见，并且回应通常带有对少数群体的刻板印象。根据你的任务及其目标，这些都是在项目的每个阶段需要考虑的因素。

![](img/73ffb7e66f0c8310862a2da6c31096c1.png)

> “默认情况下，LLM 的回答往往更接近某些人群的观点，例如来自美国以及一些欧洲和南美国家的观点” *——摘自研究[2]*

## 模型参数：温度

在表格 1 中的大多数研究中，常提到的一个参数是温度参数，它调整 LLMs 输出的*“创造性”*。研究[5]和[6]实验了较高和较低的温度，发现 LLMs 在较低温度下的回答一致性更高，并且没有牺牲准确性；因此，他们建议***在标注任务中使用较低值***。

## 语言限制

正如我们在表格 1 中看到的，大多数研究测量了 LLMs 在英语数据集上的标注表现。研究[7]探讨了法语、荷兰语和英语任务，发现非英语语言的表现显著下降。目前，**LLMs 在英语中表现更好**，但正在进行替代方案的研究，以将其好处扩展到非英语用户。其中两个项目包括：由[阿列克萨·戈尔季奇](https://medium.com/u/37f02ae83e8c?source=post_page---user_mention--2a8334e70fb8--------------------------------)发起的 YugoGPT（适用于塞尔维亚语、克罗地亚语、波斯尼亚语、黑山语）和由[*Cohere for AI*](https://cohere.com/research/aya)*.*发起的 Aya（支持 101 种不同语言）。

## 人类推理与行为（自然语言解释）

除了简单地要求 LLM 提供标签外，我们还可以要求它提供所选标签的解释。研究[10]发现，GPT 返回的解释与人类提供的解释相当，甚至可能更清晰。然而，卡内基梅隆大学和谷歌的研究人员也指出，LLMs 目前尚不能[模拟人类决策](https://medium.com/@majapavlo/references-for-llms-as-annotators-1c2886b50b86#9e13)，也没有表现出[类似人类的行为](https://medium.com/@majapavlo/references-for-llms-as-annotators-1c2886b50b86#fb0c)。他们发现，经过指令调优的模型表现出更少的人类行为，并表示 LLMs 不应被用来替代人类在标注流程中的作用。我也建议在这个阶段谨慎使用自然语言解释。

> “替代削弱了三个价值：参与者利益的代表性；参与者在开发过程中的包容性和赋权” *——摘自阿格纽（2023）*

# 摘要 | TL;DR

![](img/484758ee333481ef8e1fb3568793d07c.png)

优势与劣势 | 作者提供的图片

+   对于预算有限且任务相对客观的情况，LLMs 可能是一个选择，在这些任务中，你关心的是最可能的标签。对于那些关于正确标签的意见可能有很大分歧的主观任务要小心！

+   避免使用 LLMs 来模拟人类推理和行为。

+   对于更关键的任务（*例如医疗健康*），你可以使用 LLM 来加速标注过程，通过让人类修正已标注的数据；但不要完全去除人类在标注过程中的角色！

+   批判性地评估你的结果，识别偏见和其他问题，并考虑错误的代价是否值得他们可能带来的麻烦。

> 本评论绝不是一个详尽的比较。如果你有[其他来源](https://medium.com/@majapavlo/references-for-llms-as-annotators-1c2886b50b86)可以为讨论提供贡献，或者有关于 LLM 数据标注的个人经验，请在评论中分享。

**参考文献**

+   如果你想单独查看这些研究和其他资源，这里有一个列出所有用于创建此博客文章的论文的清单：[**博客参考文献**](https://medium.com/@majapavlo/references-for-llms-as-annotators-1c2886b50b86)**。**

+   如果你想获得更多关于*表 1 和研究*的见解，以下是[LREC-COLING 的[*NLPerspectives 工作坊论文*](https://aclanthology.org/2024.nlperspectives-1.11.pdf)]的链接

*脚注*

**¹***这不是一篇关于所有文献的全面综述，而只覆盖了我在进行该主题阅读时找到的论文。此外，我的重点主要是分类任务。*

**²***鉴于 LLM 发展的速度，现在有比这些研究中测试的模型更强大的模型可用。*

**³***指令调优模型是通过关注理解和生成基于给定指令/提示的准确且连贯的回应进行训练的。*
