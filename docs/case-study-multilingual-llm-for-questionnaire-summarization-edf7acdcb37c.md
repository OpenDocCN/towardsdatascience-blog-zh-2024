# 案例研究：多语言LLM用于问卷总结

> 原文：[https://towardsdatascience.com/case-study-multilingual-llm-for-questionnaire-summarization-edf7acdcb37c?source=collection_archive---------5-----------------------#2024-07-30](https://towardsdatascience.com/case-study-multilingual-llm-for-questionnaire-summarization-edf7acdcb37c?source=collection_archive---------5-----------------------#2024-07-30)

## 基于LLM的学生开放式问卷回答总结方法

[](https://medium.com/@sria.louis?source=post_page---byline--edf7acdcb37c--------------------------------)[![Sria Louis](../Images/d65b17e9d4ace7e0222118abc70f3954.png)](https://medium.com/@sria.louis?source=post_page---byline--edf7acdcb37c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--edf7acdcb37c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--edf7acdcb37c--------------------------------) [Sria Louis](https://medium.com/@sria.louis?source=post_page---byline--edf7acdcb37c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--edf7acdcb37c--------------------------------) ·10分钟阅读·2024年7月30日

--

![](../Images/cbbea877f5f00d69ee043faac8a55599.png)

插图：Or Livneh

[Madrasa (希伯来语中的מדרסה)](https://madrasafree.com/)是一个以色列的非政府组织，致力于向希伯来语使用者教授阿拉伯语。最近，在学习阿拉伯语时，我发现这个非政府组织拥有独特的数据，而该组织可能从深入分析中受益。我和一位朋友作为志愿者加入了该组织，我们被要求处理下面描述的总结任务。

使这项总结任务如此有趣的是，文档包含三种语言——希伯来语、阿拉伯语和英语——而且还需要处理它们之间不准确的[转录](https://en.wikipedia.org/wiki/Transcription_(linguistics))。

关于隐私：数据可能包含个人身份信息（PII），因此目前无法公开。如果您认为自己能够提供帮助，请联系我们。

## 问题背景

作为其语言课程的一部分，Madrasa向学生分发问卷，其中包括需要数字回答的定量问题和要求学生用自然语言回答的开放式问题。

在这篇博客文章中，我们将集中讨论开放式自然语言回答。

## 问题描述

主要挑战是管理和从大量开放式问题的回答中提取洞见。具体而言，困难包括：

**多语种回应**：学生的回答主要是希伯来语，但也包含阿拉伯语和英语，形成了一个复杂的多语种数据集。此外，由于阿拉伯语口语课程中常用音译，我们发现学生有时会使用音译和阿拉伯字母书写两种方式回答问题。令我们惊讶的是，部分学生甚至将希伯来语和阿拉伯语音译成拉丁字母。

**细腻的情感**：学生的回答情感和语气变化很大，包括幽默、建议、感激和个人反思等。

**多样化的主题**：学生们涉及了从表扬教师到报告网站和应用程序的技术问题，再到个人抱负等广泛的主题。

## **数据**

课程数量不多。每门课程包含三份问卷，分别在课程的开始、中期和结束时发放。每份问卷包含几个开放性问题。

下表提供了两个问题的示例以及精选的学生回答。

![](../Images/f88ecea943d4381a5ff4241b367c0428.png)

问题和学生回答的示例。左侧：原始问题和学生回答。右侧：为博客读者翻译成英文的内容。注意语言的混合，包括阿拉伯语到希伯来语的音译，即使在同一句话中也涉及多种主题，且使用了不同的语言风格。来源：Sria Louis / Madarsa

![](../Images/7feb7527f2a36be8c1ca11edf55634f3.png)

一个问题和学生回答的示例。左侧：原始问题和学生回答。右侧：为博客读者翻译成英文的内容。注意语言的混合和音译，包括从英语到希伯来语以及从希伯来语到英语的音译。来源：Sria Louis / Madarsa

每个问题都有成千上万的学生回答，且在分句后（如下所述），每列最多可能有约100,000个句子。这一数据量是可管理的，可以在本地进行处理。

我们的目标是总结学生在各个主题上的意见，涵盖每门课程、问卷和开放性问题。我们旨在捕捉学生的“主要观点”，同时确保不忽视个别学生提供的“小众意见”或“有价值的见解”。

## 解决方案

为了解决上述挑战，我们实施了一种多步骤的自然语言处理（NLP）解决方案。

处理流程包括：

1.  句子分词（使用NLTK句子分词器）

1.  主题建模（使用BERTopic）

1.  主题表示（使用BERTopic + LLM）

1.  批量总结（使用LLM与迷你批次适应上下文大小）

1.  重新总结批次以创建最终的综合总结。

**句子分割：** 我们使用NLTK将学生的回答分割成单独的句子。这个过程至关重要，因为学生的回答通常在一个句子中涉及多个话题。例如，一个学生可能写道：“老师使用了日常生活中的例子。应用程序中的游戏非常好。”这里，每个句子讨论了他们体验的不同方面。尽管句子分割有时会由于句子间的交叉引用而导致上下文丢失，但通常通过将回答拆分为更易管理且具有特定主题的单元，它能增强整体分析效果。这个方法已被证明显著提高了最终结果。

> NLTK的句子分割器（`[nltk.tokenize.sent_tokenize](https://www.nltk.org/api/nltk.tokenize.sent_tokenize.html)`）使用语言学规则和模型来分割文档为句子，并确定句子的边界。默认的英语模型适用于我们的用例。

**使用BERTopic进行主题建模：** 我们利用BERTopic对分割后的句子进行主题建模，识别潜在主题，并为每个句子分配一个主题。在摘要之前，这一步骤非常关键，原因有几个。首先，学生回答中涉及的主题种类繁多，若没有主题建模，处理起来会非常困难。通过将学生的回答按主题拆分，我们可以更有效地管理和批量处理数据，从而提升分析性能。此外，主题建模确保了那些仅由少数学生提到的小众主题，在摘要过程中不会被主流话题所掩盖。

> [BERTopic](https://maartengr.github.io/BERTopic/index.html)是一个优雅的主题建模工具，它将文档嵌入向量，进行聚类，并为每个聚类建模其表示。它的主要优势在于模块化，我们利用这一优势进行希伯来语嵌入和超参数调优。

BERTopic的配置经过精心设计，以应对数据的多语言特性和回答中的特定细微差别，从而提高了主题分配的准确性和相关性。

具体来说，请注意我们使用了[希伯来语句子嵌入模型](https://huggingface.co/imvladikon/sentence-transformers-alephbert)。我们曾考虑使用词级嵌入，但句子嵌入证明能够捕捉到所需的信息。

在降维和聚类方面，我们分别使用了BERTopic标准模型[UMAP](https://umap-learn.readthedocs.io/en/latest/)和HDBSCAN，并通过一些超参数调优，最终的结果令我们满意。

> [这里有一场关于HDBSCAN的精彩演讲](https://youtu.be/dGsxd67IFiU?si=CrAHWrXgLnq6-3ul)，由作者之一John Healy讲解。这不仅是一次非常有教育意义的演讲，讲者也非常幽默和机智！绝对值得一看 :)

BERTopic 拥有出色的文档和支持社区，因此我将分享一个代码片段，展示它如何与高级模型配合使用。更重要的是，我们要强调一些超参数选择，旨在实现高聚类粒度并允许更小的主题。请记住，我们的目标不仅是总结大多数学生认同的“主流”观点，还要突出那些更为细微的视角和较为罕见的学生建议。这种方法的代价是处理速度较慢，并且可能会导致主题过多，但管理大约 40 个主题仍是可行的。

+   **UMAP 降维**：高于标准的组件数量和较小的 UMAP 邻居数量。

+   **HDBSCAN 聚类**：min_sample = 2 以提高敏感度，而 min_cluster_size = 7 允许形成非常小的聚类。

+   **BERTopic**：nr_topics = 40。

```py
from bertopic import BERTopic
from umap import UMAP 
from hdbscan import HDBSCAN
from sentence_transformers import SentenceTransformer 
from bertopic.vectorizers import ClassTfidfTransformer

topic_size_ = 7

# Sentence Embedding in Hebrew (works well also on English)
sent_emb_model = "imvladikon/sentence-transformers-alephbert"
sentence_model = SentenceTransformer(sent_emb_model)

# Initialize UMAP model for dimensionality reduction to improve BERTopic
umap_model = UMAP(n_components=128, n_neighbors=4, min_dist=0.0)

# Initialize HDBSCAN model for BERTopic clustering
hdbscan_model = HDBSCAN(min_cluster_size = topic_size_, 
                        gen_min_span_tree=True, 
                        prediction_data=True, 
                        min_samples=2)

# class-based TF-IDF vectorization for topic representation prior to clustering
ctfidf_model = ClassTfidfTransformer(reduce_frequent_words=True)

# Initialize MaximalMarginalRelevance for enhancing topic representation
representation_model = MaximalMarginalRelevance(diversity=0.1)

# Configuration for BERTopic
bert_config = {
    'embedding_model': sentence_model,  
    'top_n_words': 20,  # Number of top words to represent each topic
    'min_topic_size': topic_size_,  
    'nr_topics': 40, 
    'low_memory': False, 
    'calculate_probabilities': False, 
    'umap_model': umap_model, 
    'hdbscan_model': hdbscan_model, 
    'ctfidf_model': ctfidf_model, 
    'representation_model': representation_model
}

# Initialize BERTopic model with the specified configuration
topic_model = BERTopic(**bert_config)
```

## 主题表示与摘要

对于接下来的两个部分——主题表示和主题摘要——我们使用了基于聊天的语言模型（LLMs），并精心设计了系统和用户提示。简单的做法是设置系统提示来定义关键词提取和摘要任务，并使用用户提示输入一长串文档，只受限于上下文限制。

在深入讨论之前，让我们先讨论选择基于聊天的 LLM 及所使用的基础设施。为了快速验证概念并缩短开发周期，我们选择了 Ollama，因为它易于设置且摩擦小。在 Google Colab 上切换模型时遇到了一些挑战，因此我们决定在我的 M3 笔记本电脑上进行本地工作。Ollama 高效利用 Mac 的集成 GPU，并且满足了我的需求。

最初，我们测试了各种多语言模型，包括 LLaMA2、[LLaMA3](https://ai.meta.com/blog/meta-llama-3/) 和 LLaMA3.1。然而，最近发布了 Dicta 2.0 新版本，它立刻超越了其他模型。Dicta 2.0 不仅提供了更好的语义结果，还改进了希伯来语分词（约每个希伯来字符对应一个标记），允许更长的上下文长度，因此能够进行更大批量的处理，而不影响质量。

> [Dicta](https://dicta.org.il/dicta-lm) 是一个双语（希伯来语/英语）的大型语言模型（LLM），经过 Mistral-7B-v0.1 微调，并可在 [Hugging Face](https://huggingface.co/collections/dicta-il/dicta-lm-20-collection-661bbda397df671e4a430c27) 上找到。

**主题表示：** 主题建模中的这一关键步骤涉及通过代表性关键词或短语定义和描述主题，捕捉每个主题的核心要素。目标是创建清晰、简洁的描述，以便理解与每个主题相关的内容。虽然 BERTopic 提供了有效的主题表示工具，但我们发现使用外部 LLM 更为便捷。该方法允许进行更灵活的实验，如关键词提示工程，提供了对主题描述和优化的更大控制。

+   系统提示：

> “你的任务是从文本中找出最多五个关键词，并用句点分隔返回。确保每个选择的单词都来自文本，并且这些词彼此不同。最多返回五个不同的词，用希伯来语写成一行短句，不添加任何其他内容、没有编号、没有换行符，也不要做额外解释。”

+   用户的提示只是 BERTopic 默认表示模型（c-tf-idf）返回的关键词和代表性句子。

**使用大语言模型（LLM）进行批量总结：**对于每个主题，我们使用 LLM 来总结学生的回答。由于数据量庞大，回答被分批处理，每个批次的总结都会单独进行，然后将这些总结汇总成最终的全面概述。

+   系统提示：

> “你的任务是将其翻译成希伯来语，然后用希伯来语总结。输入是学生关于以下问题的回答 [<X>]。请用一段最多 10 个句子的段落进行总结。请确保答案仅基于给定的意见。语法上，请用第一人称单数形式撰写总结，好像你是其中一位学生。总结只能用希伯来语书写，前后不要附加任何内容。”

上面的 [<X>] 是我们试图总结的问题的字符串。

+   用户的提示是学生们的回答批次（如上例所示）

请注意，我们要求在总结之前先进行希伯来语翻译。没有这个要求时，如果输入内容包含多种语言，模型有时会用英语或阿拉伯语回答。

[有趣的是，Dicta 2.0 也能够用阿拉伯语进行对话。这让人感到惊讶，因为 Dicta 2.0 并没有在阿拉伯语上进行训练（根据其发布帖子，它是在 50% 英语和 50% 希伯来语的数据上进行训练的），而其基础模型 Mistral 也没有专门在阿拉伯语上进行训练。]

**重新分组批次：** 最后一个非简单的步骤是对汇总后的批次进行重新总结，以为每个主题和问题生成一个统一的总结。这要求精心设计提示，确保从每个批次中提取的相关见解得到准确捕捉并有效呈现。通过优化提示，我们引导 LLM 聚焦于关键点，从而生成全面且富有见地的总结。

这种多步骤的方法使我们能够有效管理多语言和复杂数据集，提取重要见解，并提供可操作的建议，以提升**مَدْرَسَة**（Madrasa）的教育体验。

## 评估

评估总结任务通常涉及通过人工评分来衡量总结质量。在我们的案例中，任务不仅仅是总结，还包括商业见解。因此，我们要求总结不仅能反映出大多数学生的回答，还要能够捕捉到少数学生的边缘案例以及稀有或激进的见解。

为了满足这些需求，我们将评估过程分为上述六个步骤，并采用面向业务的方式手动评估。如果您有更为严谨的整体评估方法，欢迎分享您的想法 :)

# 结果 — 示例

例如，我们来看一下初学者课程中间一个问卷中的问题。学生们被问到：“אנא שתף אותנו בהצעות לשיפור הקורס”（英文：“Please share with us suggestions for improving the course”）。

大多数学生回应了积极的反馈，但也有一些提供了具体的建议。建议的种类繁多，通过聚类（主题建模）和总结，我们可以为非政府组织的管理团队提炼出有价值的洞察。

这里是主题簇的图示，使用BERTopic可视化工具呈现。

![](../Images/4ff235d05a9f39828963f9ef2c5bf5f7.png)

层次聚类：为了可视化目的，我们展示了一组10个主题。然而，在某些情况下，我们的分析包括了对几十个主题的实验。致谢：Sria Louis / Madrasa。

最后，下面是七个主题（来自40个主题），总结了学生们对上述问题的回答。每个主题包括其关键词（由关键词提示生成）、来自该簇的三条代表性回答（使用表示模型选出），以及最终的总结。

总之，注意各种主题和富有洞察力的总结。

![](../Images/aa14eb3d8ebaca7422d86685dc12eeba.png)

一些主题包括：关键词、表示句子和总结。致谢：Sria Louis / Madrasa

## 下一步是什么？

我们有六个步骤：

1.  **优化**：尝试不同的架构和超参数。

1.  **鲁棒性**：理解并解决对某些超参数的意外敏感性。

1.  **幻觉问题**：处理幻觉问题，特别是在输入句子数量有限的小簇/主题中，这会导致模型生成“虚构”的信息。

1.  **丰富总结**：使用思维链技术。

1.  **丰富主题建模**：在聚类前添加情感分析。例如，如果在某个特定主题中，95%的回应是正面的，而5%是非常负面的，那么基于主题和句子情感进行聚类可能会有所帮助。这可能帮助摘要生成器避免收敛到平均值。

1.  **增强用户体验**：实施RAG或LLM可解释性技术。例如，给定一个具体的非平凡洞察，我们希望用户点击该洞察，并追溯到导致该洞察的确切学生回应。

如果你是LLM专家并且愿意分享你的见解，我们很乐意向你学习。你有什么建议可以改进我们的模型或方法吗？快来联系我们！

[sria.louis@gmail.com](mailto:sria.louis@gmail.com)

想了解更多关于Madarsa的信息吗？ [https://madrasafree.com/](https://madrasafree.com/)

代码可以在[项目GitHub仓库](https://github.com/gitLouis/madarse-summarization)中找到。

![](../Images/62500ce8f7182ccfc9e9822e71ec64bd.png)

插图：Or Livneh

关键词：NLP、主题建模、LLM、希伯来语、句子嵌入、BERTopic、llama、NLTK、Dicta 2.0、摘要、madrasa
