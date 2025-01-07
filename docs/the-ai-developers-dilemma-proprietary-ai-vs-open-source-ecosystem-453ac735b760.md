# AI 开发者的困境：专有 AI 与开源生态系统

> 原文：[https://towardsdatascience.com/the-ai-developers-dilemma-proprietary-ai-vs-open-source-ecosystem-453ac735b760?source=collection_archive---------6-----------------------#2024-09-30](https://towardsdatascience.com/the-ai-developers-dilemma-proprietary-ai-vs-open-source-ecosystem-453ac735b760?source=collection_archive---------6-----------------------#2024-09-30)

![](../Images/f2eb22a33aaa38f31c7baa594e4409d1.png)

*图片来源：Adobe Stock。*

## ***影响生成式人工智能（GenAI）大规模集成和部署的基本选择***

[](https://gadi-singer.medium.com/?source=post_page---byline--453ac735b760--------------------------------)[![Gadi Singer](../Images/293941f11306a6e2100c2375ccb1a85a.png)](https://gadi-singer.medium.com/?source=post_page---byline--453ac735b760--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--453ac735b760--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--453ac735b760--------------------------------) [Gadi Singer](https://gadi-singer.medium.com/?source=post_page---byline--453ac735b760--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--453ac735b760--------------------------------) ·阅读时间 17 分钟·2024年9月30日

--

在公司或开发者采用生成式人工智能（GenAI）之前，他们常常会思考如何从 AI 集成到业务中获得商业价值。考虑到这一点，一个基本问题随之而来：哪种方式能提供最佳的投资回报——是采用一个大型、包罗万象的专有模型，还是使用一个可以根据公司需求进行塑造和微调的开源 AI 模型？AI 采用策略范围广泛，从访问像 [OpenAI 的 GPT-4o](https://openai.com/index/hello-gpt-4o/) 这样的专有前沿大模型的云服务，到在公司的计算环境中构建一个使用公司数据索引的小型开源模型来执行一组特定任务的内部解决方案。当前的 AI 解决方案远远超出了模型本身，还包括一个完整的生态系统，其中包含检索系统、代理以及其他功能组件，如 AI 加速器，这些对于大模型和小模型都具有重要意义。跨行业合作的出现，如 [Open Platform for Enterprise AI (OPEA)](https://opea.dev/)，进一步推动了简化访问和构建端到端开源解决方案的承诺。

这种在开源生态系统和专有设置之间的基本选择会影响无数的商业和技术决策，使其成为“AI 开发者的困境”。我认为，对于大多数企业和其他商业部署，最初使用专有模型来了解 AI 的潜力并最小化早期资本支出（CapEx）是合理的。然而，对于广泛的持续部署，在许多情况下，企业将使用基于生态系统的开源定向解决方案，这提供了一种成本效益高、适应性强的战略，能够与不断发展的商业需求和行业趋势保持一致。

## **GenAI 从消费者向商业部署的过渡**

当 GenAI 于 2022 年末凭借 Open AI 的 GPT-3 和 ChatGPT 3.5 闯入市场时，主要吸引了消费者的兴趣。随着企业开始研究 GenAI，两种部署 GenAI 的方法在 2023 年迅速出现——使用像 ChatGPT 这样的巨大前沿模型，还是使用由 Meta 的 LLaMa 模型启发而来的新推出的小型开源模型。到 2024 年初，两种基本方法已逐渐固定，如图 1 中的列所示。采用专有 AI 方法的公司依赖于一个大型封闭模型来提供所需的所有技术价值。例如，以 GPT-4o 作为左侧列的代理，AI 开发者将使用 OpenAI 的技术来提供模型、数据、安全性和计算资源。而采用开源生态系统 AI 方法的公司或开发者，可能会选择合适大小的开源模型，使用公司或私有数据、定制功能以及必要的计算和安全性。

这两种方向都是有效的，并且各有优缺点。这不是一个绝对的划分，开发者可以从任一方法中选择组件，但选择专有的或基于生态系统的开源 AI 路径为公司提供了一个高度一致的战略。虽然预计两种方法都会广泛部署，但我认为，在初步的学习和过渡期后，大多数公司将会采用开源方法。根据使用情况和设置，开源内部 AI 可能带来显著的好处，包括能够微调模型，并利用公司当前的基础设施推动部署，将模型运行在边缘、客户端、数据中心或作为专用服务。随着新的 AI 微调工具的出现，深厚的专业知识已不再是障碍。

![](../Images/94deab0e4f8037a024f2d918e341385c.png)

*图 1\. AI 开发者困境的基础方法。图片来源：英特尔实验室。*

在各行各业中，AI 开发者正在使用生成式人工智能（GenAI）进行各种应用。2023年10月，[Gartner的调查](https://www.gartner.com/en/newsroom/press-releases/2023-10-03-gartner-poll-finds-55-percent-of-organizations-are-in-piloting-or-production-mode-with-generative-ai)显示，55%的组织报告称自2023年初以来增加了对生成式人工智能的投资，许多公司正在进行生成式人工智能的试点或生产模式。根据调查时的数据，企业主要投资于将生成式人工智能用于软件开发，其次是市场营销和客户服务功能。显然，人工智能应用的范围正在迅速增长。

## **大型专有模型与小型和大型开源模型**

![](../Images/aae966aaa55c7c687bb93089d425b2fb.png)

*图2：大型专有模型与小型和大型开源模型的优点。有关商业考虑，请参见图7中的资本支出和运营支出方面。图片来源：英特尔实验室。*

在我的博客[《适者生存：紧凑型生成式AI模型是大规模成本效益AI的未来》](/survival-of-the-fittest-compact-generative-ai-models-are-the-future-for-cost-effective-ai-at-scale-6bbdc138f618)中，我对大型模型与小型模型进行了详细评估。简而言之，自从[Meta于2023年2月发布的LLaMa开源模型](https://ai.meta.com/blog/large-language-model-llama-meta-ai/)以来，学术界和广泛的生态系统进入了一个创新和快速改进的良性循环，创造出比大型前沿模型小10倍到100倍的高效模型。到2024年，许多小型模型的参数数量通常不到300亿，它们可以[接近匹敌](https://azure.microsoft.com/en-us/blog/introducing-phi-3-redefining-whats-possible-with-slms/)拥有超过1000亿参数的ChatGPT风格大型模型的能力，尤其是在针对特定领域时。虽然生成式人工智能已经在各行业中广泛部署，用于各种商业用途，但紧凑型模型的使用正在上升。

此外，开源模型通常落后于专有模型[仅6到12个月](https://ark-invest.com/newsletter_item/1-openais-improved-chatgpt-should-delight-both-expert-and-novice-developers)。使用广泛的语言基准MMLU，开源模型的改进速度更快，差距似乎正在缩小。例如，OpenAI的[GPT-4o](https://openai.com/index/hello-gpt-4o/)于今年5月13日发布，带来了重要的多模态特性，而微软的小型开源[Phi-3-vision](https://azure.microsoft.com/en-us/blog/new-models-added-to-the-phi-3-family-available-on-microsoft-azure/)则在5月21日发布，仅晚了一周。在[初步比较](https://youtu.be/PZaNL6igONU?si=jCvhwvWBoZFnRG5X)中，视觉识别和理解方面，这些模型展示了相似的能力，几项测试甚至偏向Phi-3-vision模型。[Meta的Llama 3.2开源发布的初步评估](https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/)表明，其“视觉模型在图像识别和一系列视觉理解任务上与领先的基础模型、Claude 3 Haiku和GPT4o-mini具有竞争力”。

大型模型具有令人难以置信的多功能性。开发者可以选择各种大型商业化的专有GenAI模型，包括OpenAI的GPT-4o多模态模型。谷歌的[Gemini 1.5](https://deepmind.google/technologies/gemini/#introduction)原生多模态模型有四种尺寸：用于移动设备应用开发的Nano，小型的Flash模型用于特定任务，Pro用于广泛的任务，Ultra用于高度复杂的任务。此外，Anthropic的[Claude 3 Opus](https://www.anthropic.com/news/claude-3-family)，据说拥有[大约2万亿个参数](https://lifearchitect.substack.com/p/the-memo-special-edition-claude-3)，具有200K的上下文窗口，允许用户上传大量信息。还有一类即开即用的大型GenAI模型，企业可以用来提升员工的生产力和创造性发展。[Microsoft 365 Copilot](https://blogs.microsoft.com/blog/2023/03/16/introducing-microsoft-365-copilot-your-copilot-for-work/)集成了Microsoft 365应用套件、Microsoft Graph（来自电子邮件、文件、会议、聊天、日历和联系人等的内容与上下文），以及GPT-4。

大多数大型和小型开源模型通常对应用框架、工具生态系统、训练数据和评估平台更加透明。模型架构、超参数、响应质量、输入模式、上下文窗口大小和推理成本部分或完全公开。这些模型通常会提供数据集的信息，以便开发者判断是否符合版权或质量要求。这种透明度使得开发者可以轻松地交换模型，以便适应未来的版本。在越来越多的小型商业化开源模型中，Meta的[Llama 3和3.1](https://ai.meta.com/blog/meta-llama-3-1/)基于Transformer架构，提供8B、70B和405B参数版本。Llama 3.2多模态模型有11B和90B版本，较小的版本为1B和3B参数。与NVIDIA合作开发的Mistral AI的[Mistral NeMo](https://mistral.ai/news/mistral-nemo/)是一个12B模型，具有128k的大上下文窗口，而微软的[Phi-3](https://news.microsoft.com/source/features/ai/the-phi-3-small-language-models-with-big-potential/)（3.8B、7B和14B）提供用于推理和语言理解任务的Transformer模型。微软将Phi模型作为“[小型语言模型的惊人力量](https://www.microsoft.com/en-us/research/blog/phi-2-the-surprising-power-of-small-language-models/)”的例子，同时也在对OpenAI的大型模型进行大量投资。微软在生成式AI（GenAI）领域的多样化兴趣表明，这不是一个一刀切的市场。

## **模型融合数据（带有RAG）与检索为中心的生成（RCG）**

AI开发者需要解决的下一个关键问题是在哪里找到推理过程中使用的数据——是在模型的参数化记忆中，还是在模型之外（通过检索可访问）。这可能难以置信，但2022年11月推出的第一版ChatGPT并未访问模型之外的数据。它是在2022年9月21日训练的，且明显无法了解其训练日期之后的事件和数据。这一重大疏忽在2023年得到了修正，检索插件被添加进来。如今，大多数模型都与检索前端结合使用，只有在不期望访问大规模或持续更新信息的情况下，如专用编程模型，才会有所例外。

当前的模型通过增强解决方案平台，结合检索增强生成（RAG）前端，使得可以提取模型外部的信息，从而在这一问题上取得了显著进展。高效且安全的RAG是企业级GenAI部署的要求，微软于2023年底推出的[GPT-RAG](https://github.com/Azure/GPT-RAG/)便是一个例证。此外，在博客[知识检索成为核心](https://example.org/knowledge-retrieval-takes-center-stage-183be733c6e8)中，我探讨了如何在GenAI从消费级部署过渡到商业级部署时，解决方案应主要围绕模型外部的信息构建，采用以检索为核心的生成（RCG）方法。

![](../Images/0be3876b1e8c780ab5400ce962cc804f.png)

图3. RAG与RCG的优势对比。图片来源：英特尔实验室。

RCG模型可以定义为RAG GenAI解决方案的一个特例，专为那些绝大多数数据存在于模型的参数记忆之外，且在预训练或微调过程中大多没有涉及的系统设计。通过RCG，GenAI模型的主要角色是解释从公司已索引数据库或其他策划内容中检索到的丰富信息。与其说是记忆数据，不如说是聚焦于针对特定构造、关系和功能的微调。生成输出中的数据质量预计将接近100%的准确性和时效性。

![](../Images/591e4dc8864d1884a1e060c397f19d3a.png)

*图4. GenAI平台中检索的工作原理。图片来源：英特尔实验室。*

[OPEA](https://www.intel.com/content/www/us/en/developer/articles/news/introducing-the-open-platform-for-enterprise-ai.html)是一个跨生态系统的努力，旨在简化GenAI系统的采纳和调优。使用这一可组合的框架，开发者可以创建并评估“开放的、多供应商的、强大的且可组合的GenAI解决方案，充分利用生态系统中的最佳创新。”OPEA有望简化企业级复合GenAI解决方案的实施，包括RAG、代理和记忆系统。

![](../Images/51f3053a21ba51004ef80aa91d0c16d4.png)

*图5. GenAI实施的OPEA核心原则。* *图片来源：OPEA。*

## **通用多功能模型与定制化目标模型的对比**

像GPT-4o、Claude 3和Gemini 1.5这样的模型是通用型的全能基础模型。它们被设计用于执行广泛的GenAI任务，包括编码、聊天和总结。最新的模型已经迅速扩展到执行视觉/图像任务，将其功能从单一的大型语言模型扩展到大型多模态模型或视觉语言模型（VLMs）。开源基础模型也朝着集成多模态的方向发展。

![](../Images/2016c69e41178f63411396e9836e74cf.png)

*图6. 通用模型与定制化目标模型的优势对比。图片来源：英特尔实验室。*

然而，大多数企业选择采用某种形式的专业化，而不是直接使用第一波面向消费者的通用型生成式人工智能（GenAI）模型。当一家医疗保健公司部署GenAI技术时，他们不会使用一个通用模型来管理供应链、进行IT部门的编码工作以及进行深度医疗分析来管理患者护理。企业会根据不同的使用场景，部署该技术的更多专业化版本。企业可以通过多种方式构建专业化的GenAI解决方案，包括领域特定模型、针对性模型、定制化模型和优化模型。

*领域特定模型* 专注于特定的业务领域或兴趣领域。领域特定模型有专有的和开源的两种类型。例如，BloombergGPT是一款专门为金融领域设计的50B参数专有大语言模型，[在多个金融基准测试中超越了更大的GPT-3 175B参数模型](https://arxiv.org/pdf/2303.17564.pdf)。然而，小型的开源领域特定模型也可以提供出色的替代方案，正如[FinGPT](https://arxiv.org/pdf/2306.06031.pdf)所展示的，它提供了开发金融语言模型（FinLLMs）的可获取和透明的资源。FinGPT 3.3以Llama 2 13B为基础模型，专为金融领域设计。[在最近的基准测试中](https://github.com/AI4Finance-Foundation/FinGPT)，FinGPT在多个任务上超过了BloombergGPT，并在金融基准任务（如FPB、FiQA-SA和TFNS）上轻松战胜了GPT-4。为了理解这个小型开源模型的巨大潜力，值得注意的是，FinGPT可以以不到300美元的成本进行微调，以融入新的数据。

*针对性模型* 专注于一类任务或功能，例如针对[编码](https://huggingface.co/docs/transformers/v4.39.0/en/model_doc/starcoder2)、图像生成、问答或情感分析的单独针对性模型。最近的一个针对性模型例子是英特尔实验室、Hugging Face和UKP实验室联合推出的[SetFit](https://huggingface.co/blog/setfit)。这种针对Sentence Transformers进行微调的少量样本文本分类方法，在推理和训练时更为高效，能够在少量标注的训练数据下实现高精度，例如仅用每个类别八个标注样本的客户评价（CR）情感数据集。这个仅有355M参数的小型模型可以在多样化的RAFT基准测试中超过GPT-3 175B参数模型的表现。

需要注意的是，针对性模型与领域特定模型是独立的。例如，像[SetFitABSA](https://huggingface.co/blog/setfit-absa)这样的情感分析解决方案具有针对性的功能，可以应用于工业、娱乐或酒店等多个领域。然而，既具有针对性又具有领域专门化的模型可能会更有效。

*定制化模型*进一步进行微调和优化，以满足公司、组织或个人的特定需求和偏好。通过对特定内容进行索引以供检索，生成的系统在处理与这些数据（无论是私有还是公开）相关的任务时，变得高度具体且高效。开源领域提供了多种定制模型的选项。例如，Intel Labs使用直接偏好优化（DPO）对Mistral 7B模型进行改进，从而创建了开源的[Intel NeuralChat](https://huggingface.co/Intel/neural-chat-7b-v3-1)。开发人员还可以通过使用大型语言模型的低秩适应（[LoRA](https://arxiv.org/abs/2106.09685)）以及其更加节省内存的版本[QLoRA](https://arxiv.org/abs/2305.14314)，来微调和定制模型。

*优化能力*可用于开源模型。优化的目标是在保持模型功能和准确性的同时，显著减少其执行负担，从而显著提高成本、延迟和预期平台的最佳执行效率。用于模型优化的一些技术包括蒸馏、剪枝、压缩和量化（至8位甚至4位）。一些方法，如专家混合（MoE）和[推测解码](https://arxiv.org/pdf/2211.17192.pdf)，可以视为执行优化的形式。例如，[据报道GPT-4由](https://the-decoder.com/gpt-4-has-a-trillion-parameters/)八个较小的MoE模型组成，每个模型有220B个参数。执行仅激活模型的部分，从而使推理更加经济。

## **生成即服务云执行与托管执行环境的推理对比**

![](../Images/8292f287c7dbbac24afcde2430ef2789.png)

*图7. GaaS与托管执行的优势对比。图片来源：Intel Labs。*

开发人员需要考虑的另一个关键选择是执行环境。如果公司选择专有模型的方向，推理执行将通过API或查询调用来完成，这些调用连接到云中运行的模型的抽象化和隐蔽版本。模型的大小及其他实现细节并不重要，除非它们会影响可用性或一些关键费用（按令牌、查询次数，或无限计算许可证收费）。这种方法有时被称为[生成即服务（GaaS）](https://www.forbes.com/sites/steveandriole/2023/07/26/llama-chatgpt-bard-co-pilot--all-the-rest--how-large-language-models-will-become-huge-cloud-services-with-massive-ecosystems/?sh=78764e1175b7)云服务，是公司消费非常大的专有模型（如GPT-4o、Gemini Ultra和Claude 3）的一种主要方式。然而，GaaS也可以用于提供像Llama 3.2这样的小型模型。

使用GaaS进行外包智能方法有明确的积极方面。例如，访问通常是即时的，并且开箱即用，减轻了内部开发的工作量。还有一个隐含的承诺，即当模型或其环境升级时，AI解决方案开发者可以在不做大量努力或更改设置的情况下，获取最新的更新。此外，费用几乎完全是运营支出（OpEx），如果工作负载是初步的或有限的，这种方式更为优选。对于早期采用和间歇性使用，GaaS提供了更多支持。

相比之下，当公司选择内部智能方法时，模型推理周期被纳入并在计算环境和现有的业务软件设置中进行管理。这对于相对较小的模型（2024年约30B参数或更少）以及可能甚至是中等规模的模型（2024年50B到70B参数）在客户端设备、网络、本地数据中心或云环境中，使用如虚拟私有云（VPC）等服务提供商的设置，是一个可行的解决方案。

像Llama 3.1 8B这样的模型或类似模型可以在[开发者的本地机器](https://www.forbes.com/sites/steveandriole/2023/07/26/llama-chatgpt-bard-co-pilot--all-the-rest--how-large-language-models-will-become-huge-cloud-services-with-massive-ecosystems/?sh=78764e1175b7)（Mac或PC）上运行。通过使用像[量化](https://www.intel.com/content/www/us/en/developer/articles/case-study/q8-chat-efficient-generative-ai-experience-xeon.html#gs.36q4lk)这样的优化技术，可以在本地环境中实现所需的用户体验。使用像[Ollama](https://ollama.ai/)这样的工具和框架，开发者可以本地管理推理执行。推理周期可以在公司的数据中心通过传统的GPU、[Intel Xeon](https://www.intel.com/content/www/us/en/products/docs/processors/xeon-accelerated/ai-accelerators-product-brief.html)或[Intel Gaudi AI加速器](https://www.intel.com/content/www/us/en/products/details/processors/ai-accelerators/gaudi-overview.html)上运行。如果推理是在服务提供商处运行，则会按基础设施即服务（IaaS）计费，使用公司自己的设置和执行选项。

当推理执行在公司计算环境中（客户端、边缘、本地或IaaS）进行时，如果超出仅在现有硬件上添加工作负载的范围，则对计算设备的资本支出（CapEx）要求更高。虽然OpEx与CapEx的比较复杂且依赖于许多变量，但当部署需要广泛、持续、稳定的使用时，CapEx更为可取。尤其是随着较小模型和优化技术的出现，允许在主流设备和处理器上运行先进的开源模型，甚至在本地笔记本/台式机上运行时，这一点尤为真实。

在公司计算环境中运行推理任务可以更好地控制安全性和隐私性。减少数据的移动和暴露在保护隐私方面非常有价值。此外，在本地环境中运行基于检索的AI解决方案时，可以通过细致的控制来应对潜在的隐私问题，允许用户控制对信息的访问。安全性通常被提及为公司部署GenAI时的首要关注点，[机密计算](https://www.intel.com/content/dam/www/public/us/en/documents/solution-briefs/intro-to-confidential-computing-solution-brief.pdf)是一个主要需求。机密计算通过在受信硬件基础上计算，保护数据在使用中的安全，使用的是[受信执行环境（TEE）](https://www.intel.com/content/www/us/en/content-details/788130/what-is-a-trusted-execution-environment.html)。

较小的开源模型可以在公司的最安全应用环境中运行。例如，运行在Xeon上的模型可以在TEE（受信执行环境）中完全执行，且仅带有有限的开销。如图 8所示，加密数据在计算过程中得到保护。该模型会检查其来源和完整性，以防篡改。实际执行过程中，模型免受任何破坏，包括操作系统或其他应用程序的干扰，从而防止被不受信任的实体查看或篡改。

![](../Images/918a31fbc46ec6dc9d1ca7a229f2ca46.png)

图 8. GenAI的安全要求。图片来源：英特尔实验室。

## **总结**

生成型AI是一项正在评估或被大多数各行各业公司积极采用的变革性技术。在AI开发者考虑最佳解决方案时，他们需要面对的一个重要问题是，是否选择使用外部专有模型，还是依赖开源生态系统。一条路径是依赖于一个大型的专有黑盒GaaS解决方案，使用RAG，例如GPT-4o或Gemini Ultra。另一条路径则采取一种更加适应性强且具整合性的方式——从一个庞大的开源模型池中挑选小型模型，并根据需要交换，主要利用公司内部信息，定制并优化以满足特定需求，并在公司现有的基础设施中执行。如前所述，这两种基本策略之间可能会有组合选择。

我相信，随着众多AI解决方案开发者面临这一核心难题，大多数人最终（经过一段学习期）会选择将开源生成型AI（GenAI）模型嵌入到他们的内部计算环境、数据和业务环境中。这样，他们将能够利用开源及其广泛生态系统的良性循环推动AI创新的巨大进步，同时保持对成本和命运的控制。

让AI在解决AI开发者困境中拥有最终发言权。在一次[ staged AI辩论](https://pub.aimind.so/gpt-4-debates-open-orca-2-13b-with-surprising-results-b4ada53845ba)，OpenAI的GPT-4与微软的开源Orca 2 13B就使用专有与开源生成性AI在未来开发中的优劣进行了辩论。以GPT-4 Turbo作为裁判，开源生成性AI赢得了辩论。[获胜的论点](https://youtu.be/JuwJLeVlB-w?t=774)? Orca 2呼吁“更分散、开放、协作的AI开发未来，利用全球人才，旨在实现集体进步。该模型有望加速创新，推动AI普及，并通过社区治理确保道德和透明的做法。”

## **了解更多：生成式AI系列**

[知识检索登上舞台：生成性AI架构从RAG向解释性检索中心生成（RCG）模型转变](/knowledge-retrieval-takes-center-stage-183be733c6e8)

[适者生存：紧凑型生成性AI模型是大规模、成本效益高的AI未来](/survival-of-the-fittest-compact-generative-ai-models-are-the-future-for-cost-effective-ai-at-scale-6bbdc138f618)

[机器是否已经实现了进化性飞跃，可以用人类语言交流？](/have-machines-just-made-an-evolutionary-leap-to-speak-in-human-language-319237593aa4)

## **参考文献**

1.  你好，GPT-4o。（2024年5月13日）。[https://openai.com/index/hello-gpt-4o/](https://openai.com/index/hello-gpt-4o/)

1.  企业人工智能开放平台。（无日期）。企业人工智能开放平台（OPEA）。[https://opea.dev/](https://opea.dev/)

1.  Gartner调查发现55%的组织正在进行试点或生产中。（2023年10月3日）。Gartner。[https://www.gartner.com/en/newsroom/press-releases/2023-10-03-gartner-poll-finds-55-percent-of-organizations-are-in-piloting-or-production-mode-with-generative-ai](https://www.gartner.com/en/newsroom/press-releases/2023-10-03-gartner-poll-finds-55-percent-of-organizations-are-in-piloting-or-production-mode-with-generative-ai)

1.  Singer, G.（2023年7月28日）。适者生存：紧凑型生成性AI模型是大规模、成本效益高的AI未来。*Medium*。[https://towardsdatascience.com/survival-of-the-fittest-compact-generative-ai-models-are-the-future-for-cost-effective-ai-at-scale-6bbdc138f618](/survival-of-the-fittest-compact-generative-ai-models-are-the-future-for-cost-effective-ai-at-scale-6bbdc138f618)

1.  介绍LLaMA：一款基础性的65亿参数语言模型。（无日期）。[https://ai.meta.com/blog/large-language-model-llama-meta-ai/](https://ai.meta.com/blog/large-language-model-llama-meta-ai/)

1.  #392：OpenAI改进版的ChatGPT应让专家和初学者开发者都感到兴奋，&更多内容—ARK投资。（无日期）。Ark Invest。[https://ark-invest.com/newsletter_item/1-openais-improved-chatgpt-should-delight-both-expert-and-novice-developers](https://ark-invest.com/newsletter_item/1-openais-improved-chatgpt-should-delight-both-expert-and-novice-developers)

1.  Bilenko, M.（2024年5月22日）。Phi-3 系列新增模型，可在 Microsoft Azure 上使用。微软 Azure 博客。[https://azure.microsoft.com/en-us/blog/new-models-added-to-the-phi-3-family-available-on-microsoft-azure/](https://azure.microsoft.com/en-us/blog/new-models-added-to-the-phi-3-family-available-on-microsoft-azure/)

1.  Matthew Berman。（2024年6月2日）。开源视觉AI — 令人惊讶的结果！（Phi3 Vision 对比 LLaMA 3 Vision 对比 GPT4o）[视频]。YouTube。[https://www.youtube.com/watch?v=PZaNL6igONU](https://www.youtube.com/watch?v=PZaNL6igONU)

1.  Llama 3.2：通过开放、可定制的模型，革新边缘AI和视觉技术。[https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/](https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/)

1.  Gemini — Google DeepMind。（无日期）。[https://deepmind.google/technologies/gemini/#introduction](https://deepmind.google/technologies/gemini/#introduction)

1.  介绍下一代 Claude \ Anthropic。（无日期）。[https://www.anthropic.com/news/claude-3-family](https://www.anthropic.com/news/claude-3-family)

1.  Thompson, A. D.（2024年3月4日）。The Memo — 特别版：Claude 3 Opus。《The Memo》 by LifeArchitect.ai。[https://lifearchitect.substack.com/p/the-memo-special-edition-claude-3](https://lifearchitect.substack.com/p/the-memo-special-edition-claude-3)

1.  Spataro, J.（2023年5月16日）。介绍 Microsoft 365 Copilot — 你的工作副驾驶 — 官方微软博客。官方微软博客。[https://blogs.microsoft.com/blog/2023/03/16/introducing-microsoft-365-copilot-your-copilot-for-work/](https://blogs.microsoft.com/blog/2023/03/16/introducing-microsoft-365-copilot-your-copilot-for-work/)

1.  介绍 Llama 3.1：迄今为止我们最强大的模型。（无日期）。[https://ai.meta.com/blog/meta-llama-3-1/](https://ai.meta.com/blog/meta-llama-3-1/)

1.  Mistral AI。（2024年3月4日）。Mistral Nemo。Mistral AI | 让前沿人工智能触手可得。[https://mistral.ai/news/mistral-nemo/](https://mistral.ai/news/mistral-nemo/)

1.  Beatty, S.（2024年4月29日）。虽小却强大：Phi-3 小型语言模型的巨大潜力。微软研究院。[https://news.microsoft.com/source/features/ai/the-phi-3-small-language-models-with-big-potential/](https://news.microsoft.com/source/features/ai/the-phi-3-small-language-models-with-big-potential/)

1.  Hughes, A.（2023年12月16日）。Phi-2：小型语言模型的惊人力量。微软研究院。[https://www.microsoft.com/en-us/research/blog/phi-2-the-surprising-power-of-small-language-models/](https://www.microsoft.com/en-us/research/blog/phi-2-the-surprising-power-of-small-language-models/)

1.  Azure。（无日期）。GitHub — Azure/GPT-RAG。GitHub。[https://github.com/Azure/GPT-RAG/](https://github.com/Azure/GPT-RAG/)

1.  Singer, G. (2023年11月16日). 知识检索成为焦点 — 数据科学前沿. Medium. [https://towardsdatascience.com/knowledge-retrieval-takes-center-stage-183be733c6e8](/knowledge-retrieval-takes-center-stage-183be733c6e8)

1.  介绍企业AI开放平台. (无日期). Intel. [https://www.intel.com/content/www/us/en/developer/articles/news/introducing-the-open-platform-for-enterprise-ai.html](https://www.intel.com/content/www/us/en/developer/articles/news/introducing-the-open-platform-for-enterprise-ai.html)

1.  Wu, S., Irsoy, O., Lu, S., Dabravolski, V., Dredze, M., Gehrmann, S., Kambadur, P., Rosenberg, D., & Mann, G. (2023年3月30日). BloombergGPT: 一种面向金融的大型语言模型. arXiv.org. [https://arxiv.org/abs/2303.17564](https://arxiv.org/abs/2303.17564)

1.  Yang, H., Liu, X., & Wang, C. D. (2023年6月9日). FINGPT: 开源金融大型语言模型. arXiv.org. [https://arxiv.org/abs/2306.06031](https://arxiv.org/abs/2306.06031)

1.  AI4Finance-Foundation. (无日期). FinGPT. GitHub. [https://github.com/AI4Finance-Foundation/FinGPT](https://github.com/AI4Finance-Foundation/FinGPT)

1.  Starcoder2\. (无日期). GitHub. [https://huggingface.co/docs/transformers/v4.39.0/en/model_doc/starcoder2](https://huggingface.co/docs/transformers/v4.39.0/en/model_doc/starcoder2)

1.  SetFit: 无需提示的高效少量样本学习. (无日期). [https://huggingface.co/blog/setfit](https://huggingface.co/blog/setfit)

1.  SetFitABSA: 使用SetFit进行少量样本的基于方面的情感分析. (无日期). [https://huggingface.co/blog/setfit-absa](https://huggingface.co/blog/setfit-absa)

1.  Intel/neural-chat-7b-v3–1\. Hugging Face. (2023年10月12日). [https://huggingface.co/Intel/neural-chat-7b-v3-1](https://huggingface.co/Intel/neural-chat-7b-v3-1)

1.  Hu, E. J., Shen, Y., Wallis, P., Allen-Zhu, Z., Li, Y., Wang, S., Wang, L., & Chen, W. (2021年6月17日). LORA: 大型语言模型的低秩适应. arXiv.org. [https://arxiv.org/abs/2106.09685](https://arxiv.org/abs/2106.09685)

1.  Dettmers, T., Pagnoni, A., Holtzman, A., & Zettlemoyer, L. (2023年5月23日). QLORA: 量化LLMS的高效微调. arXiv.org. [https://arxiv.org/abs/2305.14314](https://arxiv.org/abs/2305.14314)

1.  Leviathan, Y., Kalman, M., & Matias, Y. (2022年11月30日). 通过推测解码实现快速推理. arXiv.org. [https://arxiv.org/abs/2211.17192](https://arxiv.org/abs/2211.17192)

1.  Bastian, M. (2023年7月3日). GPT-4拥有超过一万亿个参数 — 报告. THE DECODER. [https://the-decoder.com/gpt-4-has-a-trillion-parameters/](https://the-decoder.com/gpt-4-has-a-trillion-parameters/)

1.  Andriole, S. (2023年9月12日). LLAMA、ChatGPT、Bard、Co-Pilot 及其他。大规模语言模型如何成为庞大的云服务，并拥有巨大的生态系统。 Forbes. [https://www.forbes.com/sites/steveandriole/2023/07/26/llama-chatgpt-bard-co-pilot--all-the-rest--how-large-language-models-will-become-huge-cloud-services-with-massive-ecosystems/?sh=78764e1175b7](https://www.forbes.com/sites/steveandriole/2023/07/26/llama-chatgpt-bard-co-pilot--all-the-rest--how-large-language-models-will-become-huge-cloud-services-with-massive-ecosystems/?sh=78764e1175b7)

1.  Q8-Chat LLM：在 Intel® CPU 上高效的生成式 AI 体验. (无日期). Intel. [https://www.intel.com/content/www/us/en/developer/articles/case-study/q8-chat-efficient-generative-ai-experience-xeon.html#gs.36q4lk](https://www.intel.com/content/www/us/en/developer/articles/case-study/q8-chat-efficient-generative-ai-experience-xeon.html#gs.36q4lk)

1.  Ollama. (无日期). Ollama. [https://ollama.com/](https://ollama.com/)

1.  AI 加速 Intel® Xeon® 可扩展处理器产品简介. (无日期). Intel. [https://www.intel.com/content/www/us/en/products/docs/processors/xeon-accelerated/ai-accelerators-product-brief.html](https://www.intel.com/content/www/us/en/products/docs/processors/xeon-accelerated/ai-accelerators-product-brief.html)

1.  Intel® Gaudi® AI 加速器产品. (无日期). Intel. [https://www.intel.com/content/www/us/en/products/details/processors/ai-accelerators/gaudi-overview.html](https://www.intel.com/content/www/us/en/products/details/processors/ai-accelerators/gaudi-overview.html)

1.  保密计算解决方案 — Intel. (无日期). Intel. [https://www.intel.com/content/www/us/en/security/confidential-computing.html](https://www.intel.com/content/www/us/en/security/confidential-computing.html)

1.  什么是受信执行环境？ (无日期). Intel. [https://www.intel.com/content/www/us/en/content-details/788130/what-is-a-trusted-execution-environment.html](https://www.intel.com/content/www/us/en/content-details/788130/what-is-a-trusted-execution-environment.html)

1.  Adeojo, J. (2023年12月3日). GPT-4 与 Open Orca-2–13B 辩论，结果令人惊讶！ Medium. [https://pub.aimind.so/gpt-4-debates-open-orca-2-13b-with-surprising-results-b4ada53845ba](https://pub.aimind.so/gpt-4-debates-open-orca-2-13b-with-surprising-results-b4ada53845ba)

1.  Data Centric. (2023年11月30日). 惊人的辩论对决：GPT-4 Turbo 对决 Orca-2–13B — 使用 AutoGen 编程！ [视频]. YouTube. [https://www.youtube.com/watch?v=JuwJLeVlB-w](https://www.youtube.com/watch?v=JuwJLeVlB-w)
