# 5 个关于生成式 AI 的严峻真相，科技领导者必看

> 原文：[https://towardsdatascience.com/5-hard-truths-about-generative-ai-for-technology-leaders-4b119336bc85?source=collection_archive---------1-----------------------#2024-01-04](https://towardsdatascience.com/5-hard-truths-about-generative-ai-for-technology-leaders-4b119336bc85?source=collection_archive---------1-----------------------#2024-01-04)

## 产生真正商业价值的 GenAI 需要真正的努力。但这是值得的。

[](https://barrmoses.medium.com/?source=post_page---byline--4b119336bc85--------------------------------)[![Barr Moses](../Images/4c74558ee692a85196d5a55ac1920718.png)](https://barrmoses.medium.com/?source=post_page---byline--4b119336bc85--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4b119336bc85--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4b119336bc85--------------------------------) [Barr Moses](https://barrmoses.medium.com/?source=post_page---byline--4b119336bc85--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b119336bc85--------------------------------) ·8 分钟阅读·2024年1月4日

--

![](../Images/aebacd9bb52572b68e0d4abb2ced0bcb.png)

原始图片来自 [The Everett Collection](https://www.shutterstock.com/g/everett?q=) 在 [Shutterstock](https://www.shutterstock.com/image-photo/defendants-lawyer-jury-95079523) 上。图片由作者编辑。

GenAI 无处不在，来自各行各业的组织都在施加压力，要求他们的团队加入这场竞争——[77%的商业领袖](https://www.cio.com/article/657840/is-your-data-strategy-ready-for-gen-ai-lob-leaders-may-disagree.html#:~:text=Line%2Dof%2Dbusiness%20leaders%20are,to%20the%20Salesforce%20State%20of) 担心他们已经错失了 GenAI 的好处。

数据团队正在紧急响应这个需求。但要构建一个真正能推动商业价值的生成式 AI 模型是*困难*的。

从长远来看，快速与 OpenAI API 集成是行不通的。这是 GenAI，但它有什么护城河呢？为什么用户要选择你而不是 ChatGPT？

那种简单的勾选框式集成看似是前进的一步，但如果你还没有考虑如何将大语言模型（LLMs）与你的专有数据和商业背景连接起来，真正驱动差异化价值，那你已经落后了。

这不是夸张。我这一周仅在这个话题上就与半打数据领袖进行了对话。没有一个人没意识到这是一场竞赛。终点线前会有赢家和输家。那些是大片公司和 Netflix。

如果你觉得起跑枪已经响起，但你的团队还在起跑线前拉伸，讨论着“泡沫”和“炒作”，我总结了 5 个严峻的真相，帮助你打破自满情绪。

# 硬真相 #1：你的生成性人工智能功能没有被很好地采用，你的变现速度很慢。

“Barr，如果生成性人工智能如此重要，为什么我们目前实施的功能接受度如此低？”

好吧，有几个原因。其一，你的人工智能项目并不是对一系列明确的用户问题的回应。对大多数数据团队来说，原因在于你们都在赶时间，而这个过程还处于早期阶段，你们希望先获得一些经验。

然而，不久之后，你的用户会遇到一个最适合通过生成性人工智能解决的问题，而当那时发生——与你的“虎队”集思广益，探讨如何将生成性人工智能与用例结合相比，你会获得更好的采纳率。

而且因为处于早期阶段，已经整合的生成性人工智能功能只是“ChatGPT，但在这里而已”。

让我举个例子。想象一下你每天可能会用到的一个生产力应用，用来分享组织知识。这样的应用可能会提供一些命令执行功能，比如“总结此内容”、“延长内容”或“改变语气”等，用于处理结构化较差的文本块。一个命令等于一个 AI 信用点。

是的，这很有帮助，**但它并没有什么差异化**。

也许团队决定购买一些 AI 信用点，或者他们只需切换到 [另一个标签页并向 ChatGPT 提问](https://thomasjfrank.com/how-to-transcribe-audio-to-text-with-chatgpt-and-notion/#summarize-the-transcript-with-chatgpt-1)。我不想完全忽视或低估不将专有数据暴露给 ChatGPT 的好处，但这也是一种较小的解决方案和愿景，而非那些在全国各地的财报电话会议中描绘的愿景。

![](../Images/06363a5bf16d8650ff55b20495bbfe5b.png)

*从概念到价值的那个恼人的中间步骤。图片由* [*Joe Reis 在 Substack*](https://joereis.substack.com/p/ai-underpants-gnomes-c-suite-edition)*提供。*

那么请考虑一下：你的生成性人工智能的差异化和附加值是什么？让我给你一个提示：高质量的专有数据。

这就是为什么 RAG 模型（或有时是微调过的模型）对于生成性人工智能项目如此重要的原因。它为大语言模型（LLM）提供了对企业专有数据的访问权限。下面我会解释原因。

# 硬真相 #2：你害怕在生成性人工智能（Gen AI）上做得更多。

事实确实如此：生成性人工智能让人感到畏惧。

当然，你可以将你的 AI 模型更深入地整合到你所在组织的流程中，但这感觉风险很大。让我们面对现实：ChatGPT 会产生幻觉，而且是不可预测的。它存在知识截止的问题，使得用户容易得到过时的输出。数据处理不当和向消费者提供错误信息的法律后果，即使是无意的，也是存在的。

![](../Images/a6059b1245525c2b30d0246bd96d9440.png)

*听起来足够真实，对吧？Llama 2 确实这么认为。图片由* [*Pinecone*](https://www.pinecone.io/learn/options-for-solving-hallucinations-in-generative-ai/)*提供。*

你的数据失误会带来后果。这也是为什么必须准确了解你在输入给生成性人工智能的数据，以及确保数据的准确性。

在我们向数据领导者们发送的匿名[调查问卷](https://www.montecarlodata.com/blog-a-i-confidential-the-unvarnished-truth-from-5-anonymous-data-leaders/)中，我们询问了他们的团队距离实现生成型人工智能（GenAI）应用案例有多远，其中一位回答说：“我认为我们的基础设施并不是阻碍我们的因素。我们在这里非常谨慎——随着形势发展如此迅速，且‘失控’聊天机器人的声誉风险，我们决定按兵不动，等待一阵炒作平息。”

这是我与许多数据领导者交流时普遍感受到的情绪。如果数据团队突然展示了面向客户的安全数据，那么他们就得为此负责。数据治理是一个巨大的考虑因素，而且这是一个[高门槛](https://www.supervised.news/p/a-classic-data-problem-is-taking)。

这些是真实的风险，需要解决方案，但你不能通过袖手旁观来解决它们。还有一个实际的风险，就是眼睁睁看着你的业务被率先解决这些问题的团队彻底颠覆。

通过微调和RAG将大型语言模型（LLMs）与你的专有数据结合，是这个难题中的一大关键部分，但这并不容易……

# 严峻的事实#3：RAG很难。

我相信RAG（检索增强生成）和微调是企业生成型人工智能未来的核心组成部分。但尽管在大多数情况下RAG是较为简单的方法，开发RAG应用仍然可能很复杂。

![](../Images/36663639e58011f8e281f8c3f60091db.png)

*我们就不能直接开始使用RAG吗？这有何大不了的？图片来源于* [*Reddit*](https://www.reddit.com/r/datascience/comments/16bja0s/why_is_retrieval_augmented_generation_rag_not/?onetap_auto=true)*。*

RAG看起来可能是定制大型语言模型（LLM）的显而易见解决方案。但即使对于最优秀的数据工程师来说，RAG的开发也伴随着学习曲线。他们需要了解[提示工程](https://www.promptingguide.ai/techniques/rag)、向量数据库与嵌入向量、数据建模、数据编排、数据管道……这些都是为了RAG而需要掌握的。而且，因为它是新的（[Meta AI于2020年提出](https://ai.meta.com/blog/retrieval-augmented-generation-streamlining-the-creation-of-intelligent-natural-language-processing-models/)），许多公司尚未积累足够的经验，无法建立最佳实践。

![](../Images/5f344840f79178db81ad9e24fcfecf51.png)

*RAG应用架构。图片来源于* [*Databricks*](https://docs.databricks.com/en/generative-ai/retrieval-augmented-generation.html)*。*

以下是RAG应用架构的过度简化版：

1.  RAG架构将信息检索与文本生成模型结合，因此它可以在尝试回答用户问题时访问你的数据库。

1.  数据库必须是一个**可信**的来源，包含专有数据，并允许模型将**最新**且**可靠**的信息纳入其回答和推理过程中。

1.  在后台，数据管道将各种结构化和非结构化的数据源摄取到数据库中，以保持其准确性和最新性。

1.  RAG 链接接受用户查询（文本），从数据库中检索相关数据，然后将这些数据和查询传递给 LLM，以生成高度准确且个性化的响应。

这个架构有很多复杂之处，但它确实带来了一些重要的好处：

1.  它将您的 LLM 基于准确的专有数据，从而使其变得更加有价值。

1.  它将模型带到数据中，而不是将数据带到模型中，这是一种相对简单且具有成本效益的方法。

我们可以看到这在现代数据堆栈中成为现实。最大的参与者正在以惊人的速度工作，努力通过在其环境中提供 LLM 来简化 RAG，使得企业数据得以存储。

[Snowflake Cortex](https://www.snowflake.com/blog/use-ai-snowflake-cortex/) 现在使组织能够直接在 Snowflake 中快速分析数据并构建 AI 应用程序。Databricks 新发布的 [Foundation Model APIs](https://www.databricks.com/blog/build-genai-apps-faster-new-foundation-model-capabilities) 提供了直接在 Databricks 内访问 LLM 的即时能力。微软发布了 Microsoft Azure [OpenAI 服务](https://azure.microsoft.com/en-us/products/ai-services/openai-service/)，而亚马逊最近推出了 [Amazon Redshift 查询编辑器](https://aws.amazon.com/blogs/aws/amazon-redshift-adds-new-ai-capabilities-to-boost-efficiency-and-productivity/)。

![](../Images/88ebfcd611239e014bbd1cd1401120f4.png)

*Snowflake 数据云。图片来自 Umesh Patel，来源于* [*Medium*](https://medium.com/snowflake/snowflake-unlock-the-power-of-openai-using-snowpark-ebfb19437087)*。*

我相信所有这些功能都有很大机会推动高采纳率。但它们也加大了对这些数据存储中数据质量的关注。如果为您的 RAG 管道提供数据的数据存在异常、过时或其他不可靠的情况，您的生成式 AI 项目的未来将会如何？

# 严酷的事实 #4：您的数据反正还没准备好。

仔细审视您的数据基础设施。很有可能，即使您明天就能拥有一个完美的 RAG 管道、经过精调的模型和清晰的使用案例 (*而且* *那岂不是太好了？*)，您仍然无法将这些都与干净、良好建模的数据集结合使用。

假设您希望您的聊天机器人与客户进行交互。为了完成任何有用的操作，它需要了解该组织与客户之间的关系。如果您今天是一家企业组织，那么这种关系可能跨越 150 个数据源和 5 个孤岛数据库…其中 3 个仍然是在本地部署。

如果这描述了您的组织，那么可能您距离让数据基础设施具备 GenAI 准备状态还有一年（或两年！）。

这意味着，如果你希望*很快*能在生成式 AI 上做点*什么*，你需要在现代数据平台中创建有用、可靠、高度整合且文档完善的数据集……最好是昨天就开始做了。否则，当教练把你叫上场时，你的裤子可能还没穿好。

你的数据工程团队是确保数据健康的支柱。而现代数据架构使数据工程团队能够持续监控数据质量，确保未来的数据健康。

# 硬真相 #5：你可能在不知情的情况下忽视了关键的生成式 AI 参与者。

生成式 AI 是一项团队运动，尤其是在开发方面。许多数据团队犯了将关键成员排除在生成式 AI 精英团队之外的错误，而这从长远来看会给他们带来损失。

谁*应该*加入 AI 精英团队？领导层或主要业务利益相关者应率先推动这一举措，并提醒团队关注业务价值。软件工程师负责开发代码、用户端应用和 API 调用。数据科学家则考虑新的使用案例，优化模型，并推动团队朝新的方向发展。那么，谁还缺席呢？

数据工程师。

数据工程师对生成式 AI 项目至关重要。他们能够理解那些提供相对于 ChatGPT 的竞争优势的专有业务数据，并且他们将构建数据管道，使这些数据通过 RAG（检索增强生成）提供给大型语言模型（LLM）。

如果你的数据工程师没有参与其中，那么你的精英团队就没有充分的实力。那些在生成式 AI 领域最具创新性的公司告诉我，他们已经在所有开发小组中嵌入了数据工程师。

# 赢得生成式 AI 竞赛

如果这些硬真相适用于你，不必担心。生成式 AI 仍处于初期阶段，仍然有时间重新开始，这次要勇敢面对挑战。

后退一步，理解 AI 模型可以解决的客户需求，将数据工程师带入更早的开发阶段，从一开始就确保竞争优势，并花时间构建一个能够提供高质量、可靠数据的 RAG 管道。

而且，投资现代数据架构，将数据质量放在优先位置。因为没有高质量数据的生成式 AI，只是一堆空洞的噱头。
