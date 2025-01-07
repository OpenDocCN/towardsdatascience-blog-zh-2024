# 利用 KeyBERT、HDBSCAN 和 Zephyr-7B-Beta 构建知识图谱

> 原文：[https://towardsdatascience.com/leverage-keybert-hdbscan-and-zephyr-7b-beta-to-build-a-knowledge-graph-33d7534ee01b?source=collection_archive---------0-----------------------#2024-01-07](https://towardsdatascience.com/leverage-keybert-hdbscan-and-zephyr-7b-beta-to-build-a-knowledge-graph-33d7534ee01b?source=collection_archive---------0-----------------------#2024-01-07)

## *增强型大语言模型自然语言处理与传统机器学习技术结合，用于从非结构化语料库中提取结构并构建知识图谱。*

[](https://medium.com/@silviaonofrei?source=post_page---byline--33d7534ee01b--------------------------------)[![Silvia Onofrei](../Images/198b04b2063b4269eaff52402dc5f8d5.png)](https://medium.com/@silviaonofrei?source=post_page---byline--33d7534ee01b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--33d7534ee01b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--33d7534ee01b--------------------------------) [Silvia Onofrei](https://medium.com/@silviaonofrei?source=post_page---byline--33d7534ee01b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33d7534ee01b--------------------------------) ·19 分钟阅读·2024年1月7日

--

![](../Images/f81dc37979040a1df7f80c88c9bfba77.png)

[设计者：Freepik](https://www.freepik.com/)

# 介绍

虽然大型语言模型（LLMs）是有用且高效的工具，但完全依赖它们的输出并不总是明智的，因为它们通常需要验证和基础支持。然而，将传统的自然语言处理方法与生成型人工智能的能力相结合，通常能取得令人满意的结果。一个很好的例子是，将 KeyBERT 与 KeyLLM 相结合以进行关键词提取。

在这篇博客中，我打算探讨将传统的自然语言处理和机器学习技术与大型语言模型的多功能性相结合的有效性。这一探索包括使用 KeyBERT 进行简单的关键词提取，利用 BERT 进行句子嵌入，以及使用 UMAP 进行降维，结合 HDBSCAN 进行聚类。所有这些技术与高性能的 Zephyr-7B-Beta 一起使用，最终将结果上传到知识图谱中，以便进行更深入的分析和发现。

我的目标是开发一个针对计算机科学领域非结构化 arXiv 文章标题的结构化方法。我根据摘要的长度选择了这些文章，而并非期待内在的主题聚类。事实上，初步的社区分析揭示了几乎与文章数量相等的聚类。因此，我正在探索另一种将这些标题联系起来的方法。尽管缺乏明显的社区，这些标题通常共享相同的词汇。通过提取并聚类这些关键词，我旨在揭示标题之间的潜在联系，为数据集结构化提供一种多功能的策略。

为了简化并增强数据探索，我将结果上传至 Neo4j 知识图谱。以下是输出的快照：

![](../Images/96e2d0b9ac4a38c4edcf1392d6108b9f.png)

这两个紫色节点代表标题：“可数范畴结构的核心”（左）和“通过集合解释转化结构”（右）。它们通过“数学逻辑”（浅褐色节点）这一共同主题，通过关键词“结构”连接在一起。— 图像由作者提供 —

以下是项目的步骤：

![](../Images/c16d1bc3d9daa4b48d465d17360fc24e.png)

— 图示由作者提供 —

+   收集并解析数据集，重点关注标题，同时保留摘要以提供上下文。

+   使用 KeyBERT 提取候选关键词，然后基于 Zephyr-7B-Beta，通过 KeyLLM 对其进行优化，生成增强后的关键词和关键短语列表。

+   收集所有提取的关键词和关键短语，并使用 HDBSCAN 对它们进行聚类。

+   再次使用 Zephyr-7B-Beta，为每个聚类推导标签和描述。

+   将这些元素结合到知识图谱中，节点代表文章、关键词和（聚类）主题。

需要注意的是，过程中的每一步都提供了实验替代方法、算法或模型的灵活性。

工作在 Google Colab Pro 上进行，配备 V100 GPU 和高内存设置，用于涉及 LLM 的步骤。笔记本被划分为自包含的部分，其中大多数部分可以独立执行，最小化对先前步骤的依赖。每个部分执行完毕后都会保存数据，以便在需要时能够在新会话中继续。此外，解析后的数据集和 Python 模块可以在这个 [Github 仓库](https://github.com/SolanaO/Blogs_Content/tree/master/keyllm_neo4j) 中找到。

# 数据准备

我使用的是一个来自 [arXiv 数据集](https://www.kaggle.com/datasets/Cornell-University/arxiv) 的子集，该数据集公开可用并主要由康奈尔大学维护。以机器可读格式提供，包含170万篇跨学科的学术论文，涵盖STEM领域，并提供文章标题、作者、类别、摘要、完整文本PDF等相关特征。该数据集定期更新。

数据集已清理完毕，并且格式简洁易用，因此我们可以集中精力处理任务，而无需花费过多时间进行数据预处理。为了进一步简化数据准备过程，我构建了一个Python模块，执行相关步骤。如果你想查看代码，可以在`[utils/arxiv_parser.py](https://github.com/SolanaO/Blogs_Content/blob/master/keyllm_neo4j/utils/arxiv_parser.py)`找到，或者继续使用Google Colab：

+   下载压缩的arXiv文件（1.2 GB），并选择一个目录进行存储，该目录标记为`data_path`，

+   下载`arxiv_parser.py`到`utils`目录，

+   在你的Google Colab笔记本中导入并初始化模块，

+   解压文件，这将提取出一个3.7 GB的文件：`archive-metadata-oai-snapshot.json`，

+   指定一个一般主题（我使用`cs`，即计算机科学），这样你将拥有一个更易于管理的数据集，

+   选择要保留的特征（下载的数据集中有14个特征），

+   摘要的长度可能差异较大，因此我添加了一个选项，可以选择摘要中的token数量在给定区间内的条目，并利用此功能来缩小数据集的规模，

+   尽管我选择使用`title`特征，但也有一个选项可以采用更常见的方法，即将标题和摘要合并为一个单一特征，称为`corpus`。

```py
# Import the data parser module
from utils.arxiv_parser import *

# Initialize the data parser
parser = ArXivDataProcessor(data_path)

# Unzip the downloaded file to extract a json file in data_path
parser.unzip_file()

# Select a topic and extract the articles on that topic
topic='cs'
entries = parser.select_topic('cs')

# Build a pandas dataframe with specified selections
df = parser.select_articles(entries, # extracted articles
                            cols=['id', 'title', 'abstract'], # features to keep
                            min_length = 100, # min tokens an abstract should have
                            max_length = 120, # max tokens an abstract should have
                            keep_abs_length = False, # do not keep the abs_length column
                            build_corpus=False) # do not build a corpus column

# Save the selected data to a csv file 'selected_{topic}.csv', uses data_path
parser.save_selected_data(df,topic)
```

使用上述选项，我提取了一个包含983篇计算机科学文章的数据集。我们准备好进入下一步。

> 如果你想跳过数据处理步骤，可以使用`cs`数据集，该数据集可以在Github仓库中找到。

# 使用KeyBERT和KeyLLM进行关键词提取

## 方法

[KeyBERT](https://maartengr.github.io/KeyBERT/guides/quickstart.html)是一种从文本中提取关键词或关键短语的方法。它利用文档和单词的嵌入，通过余弦相似度找到与文档最相似的子短语。KeyLLM是另一种最小化的关键词提取方法，但它基于LLM。两种方法都由Maarten Grootendorst开发和维护。

这两种方法可以结合使用以获得更好的结果。通过KeyBERT提取的关键词可以通过KeyLLM进行微调。相反，通过传统NLP技术识别的候选关键词有助于为LLM提供基础，从而最小化不希望产生的输出。

有关使用KeyLLM的不同方式，请参见[Maarten Grootendorst, 介绍KeyLLM — 使用LLM进行关键词提取](/introducing-keyllm-keyword-extraction-with-llms-39924b504813)。

![](../Images/f59f1b8d7f5ad0fadf9cd40a15394682.png)

— 作者制作的图表 —

使用KeyBERT [[source](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/_model.py)] 从每篇文档中提取关键词——这些是提供给LLM进行微调的候选关键词：

+   使用Sentence Transformers对文档进行嵌入，以构建文档级别的表示。

+   为N-gram单词/短语提取词嵌入，

+   使用余弦相似度来查找与每个文档最相似的单词或短语。

使用KeyLLM[[source](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/_llm.py)]微调通过KeyBERT提取的关键词，利用[transformers进行文本生成](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/llm/_textgeneration.py)[[source](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/llm/_textgeneration.py)]：

+   Sentence Transformers中的社区检测方法[[source](https://github.com/UKPLab/sentence-transformers/blob/master/sentence_transformers/util.py)]将相似文档分组，因此我们只会从每个组中的一个文档中提取关键词，

+   候选关键词由LLM提供，LLM为每个聚类微调关键词。

> 除了Sentence Transformers，KeyBERT还支持其他嵌入模型，见[[此处](https://maartengr.github.io/KeyBERT/guides/embeddings.html)]。
> 
> Sentence Transformers通过使用指定的阈值来促进社区检测。当文档缺乏固有的聚类时，可能不会出现明确的分组。在我的案例中，从983个标题中，大约识别出了800个不同的社区。更加自然聚类的数据往往能产生定义更明确的社区。

## 大型语言模型

在对各种较小的LLM进行实验后，我选择了[Zephyr-7B-Beta](https://arxiv.org/pdf/2310.16944.pdf)用于本项目。该模型基于[Mistral-7B](https://mistral.ai/news/announcing-mistral-7b/)，并且是首批使用直接偏好优化（DPO）进行微调的模型之一。它不仅在同类模型中表现优异，而且在一些基准测试中超过了Llama2–70B。欲了解有关此LLM的更多见解，请查看[Benjamin Marie, Zephyr 7B Beta: 一个好老师就是你所需要的一切](/zephyr-7b-beta-a-good-teacher-is-all-you-need-c931fcd0bfe7)。虽然可以直接在Google Colab Pro上使用该模型，但我选择了使用由[TheBloke](https://huggingface.co/TheBloke)准备的GPTQ量化版本。

首先按照[模型卡片](https://huggingface.co/TheBloke/zephyr-7B-beta-GPTQ)中的说明下载模型及其分词器

```py
# Required installs
!pip install transformers optimum accelerate
!pip install auto-gptq --extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/

# Required imports
from transformers import AutoModelForCausalLM, AutoTokenizer, pipeline

# Load the model and the tokenizer
model_name_or_path = "TheBloke/zephyr-7B-beta-GPTQ"

llm = AutoModelForCausalLM.from_pretrained(model_name_or_path,
                                             device_map="auto",
                                             trust_remote_code=False,
                                             revision="main") # change revision for a different branch
tokenizer = AutoTokenizer.from_pretrained(model_name_or_path, 
                     use_fast=True)
```

此外，构建文本生成管道：

```py
generator = pipeline(
    model=llm,
    tokenizer=tokenizer,
    task='text-generation',
    max_new_tokens=50,
    repetition_penalty=1.1,
)
```

## 关键词提取提示

这一过程中的实验至关重要。寻找最佳提示需要一些试验和错误，性能取决于所选择的模型。不要忘记，LLM是基于概率的，因此无法保证每次都返回相同的输出。为了开发下面的提示，我依赖了实验以及以下考虑因素：

+   [模型卡片](https://huggingface.co/TheBloke/zephyr-7B-beta-GPTQ)中提供的提示模板：

```py
prompt = "Tell me about AI"
prompt_template=f'''<|system|>
</s>
<|user|>
{prompt}</s>
<|assistant|>
'''
```

+   来自[KeyLLM博客文章](https://medium.com/towards-data-science/introducing-keyllm-keyword-extraction-with-llms-39924b504813)和[文档](https://maartengr.github.io/KeyBERT/guides/keyllm.html#2-extract-keywords-with-keyllm)中的建议，

+   对ChatGPT和KeyBERT进行了一些实验，以构建示例，

+   [KeyLLM文本生成封装的代码](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/llm/_textgeneration.py)。

这是我用来微调通过KeyBERT提取的关键词的提示：

```py
prompt_keywords= """
<|system|>
I have the following document:
Semantics and Termination of Simply-Moded Logic Programs with Dynamic Scheduling
and five candidate keywords:
scheduling, logic, semantics, termination, moded

Based on the information above, extract the keywords or the keyphrases that best describe the topic of the text.
Follow the requirements below:
1\. Make sure to extract only the keywords or keyphrases that appear in the text.
2\. Provide five keywords or keyphrases! Do not number or label the keywords or the keyphrases!
3\. Do not include anything else besides the keywords or the keyphrases! I repeat do not include any comments!

semantics, termination, simply-moded, logic programs, dynamic scheduling</s>

<|user|>
I have the following document:
[DOCUMENT]
and five candidate keywords:
[CANDIDATES]

Based on the information above, extract the keywords or the keyphrases that best describe the topic of the text.
Follow the requirements below:
1\. Make sure to extract only the keywords or keyphrases that appear in the text.
2\. Provide five keywords or keyphrases! Do not number or label the keywords or the keyphrases!
3\. Do not include anything else besides the keywords or the keyphrases! I repeat do not include any comments!</s>

<|assistant|>
"""
```

## 关键词提取与解析

现在我们拥有了进行关键词提取所需的一切。让我提醒你，我处理的是标题，因此输入文档较短，完全在BERT嵌入的令牌限制范围内。

从创建[TextGeneration管道封装器](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/llm/_textgeneration.py)开始，为LLM实例化[KeyBERT](https://github.com/MaartenGr/KeyBERT/blob/master/keybert/_model.py)。选择嵌入模型。如果未指定嵌入模型，默认模型为`all-MiniLM-L6-v2`。在这种情况下，我选择了句子嵌入的最高性能预训练模型，完整列表请参见[这里](https://www.sbert.net/docs/pretrained_models.html)。

```py
# Install the required packages
!pip install keybert
!pip install sentence-transformers

# The required imports
from keybert.llm import TextGeneration
from keybert import KeyLLM, KeyBERT
from sentence_transformers import SentenceTransformer

# KeyBert TextGeneration pipeline wrapper
llm_tg = TextGeneration(generator, prompt=prompt_keywords)

# Instantiate KeyBERT and specify an embedding model
kw_model= KeyBERT(llm=llm_tg, model = "all-mpnet-base-v2")
```

记住，数据集已经准备并保存为pandas数据框`df`。要处理标题，只需调用`extract_keywords`方法：

```py
# Retain the articles titles only for analysis
titles_list = df.title.tolist()

# Process the documents and collect the results
titles_keys = kw_model.extract_keywords(titles_list, thresold=0.5)

# Add the results to df
df["titles_keys"] = titles_keys
```

> `threshold` 参数决定了将文档分组到同一社区所需的最小相似度。较高的值会将几乎相同的文档分组，而较低的值则会将涵盖相似主题的文档聚类在一起。
> 
> 嵌入模型的选择显著影响合适的阈值，因此建议查阅模型卡片以获取指导。感谢Maarten Grootendorst强调这一点，正如[这里](https://github.com/MaartenGr/KeyBERT/issues/190)所示。
> 
> 需要注意的是，我的观察仅适用于句子变换器，因为我尚未尝试其他类型的嵌入模型。

让我们看一些输出：

![](../Images/c770ad00ee29463c4e22dd7621eb95fa.png)

**评论**：

+   在这里提供的第二个示例中，我们观察到原始文本中没有出现的关键词或关键短语。如果这对你构成问题，可以考虑启用`check_vocab=True`，如[[这里](https://maartengr.github.io/KeyBERT/guides/keyllm.html#2-extract-keywords-with-keyllm)]所做。然而，需要记住的是，这些结果受LLM选择的影响很大，量化的影响较小，提示的构造也有一定影响。

+   在处理较长的输入文档时，我注意到输出与预期结果的偏差增多。

+   一个一致的观察是，提取的关键词数量通常偏离五个。尤其在输入较简短时，常会遇到提取的关键词较少的标题。相反，一些标题会提取出多达10个关键词。让我们来看一下此次运行的关键词数量分布：

![](../Images/77b2a23d02c46e836763d01129a71041.png)

这些变化使得后续的解析步骤变得复杂。对此，有几种解决方案：我们可以详细调查这些情况，要求模型修正并修剪或重新整理关键词，或者简单地忽略这些情况，只专注于包含正好五个关键词的标题，正如我为这个项目决定的那样。

# 使用 HDBSCAN 进行关键词聚类

接下来的步骤是对关键词和关键短语进行聚类，以揭示文章中的共同话题。为此，我使用了两种算法：UMAP 用于降维，HDBSCAN 用于聚类。

## 算法：HDBSCAN 和 UMAP

[基于密度的层次空间聚类与噪声](https://hdbscan.readthedocs.io/en/latest/basic_hdbscan.html#) 或 **HDBSCAN**，是一种高效的无监督算法，旨在发现数据中的模式。它根据聚类的密度和接近度找到最优的聚类。这在聚类的数量和形状可能未知或难以确定的情况下尤其有用。

如果使用相同的超参数多次运行 HDBSCAN 聚类算法，其结果可能会有所不同。这是因为 HDBSCAN 是一个随机算法，这意味着聚类过程涉及一定程度的随机性。具体来说，HDBSCAN 使用随机初始化聚类层次结构，这可能导致每次运行算法时产生不同的聚类分配。

然而，算法在不同运行之间的变化程度可能取决于多个因素，例如数据集、超参数以及用于随机数生成器的种子值。在某些情况下，变化可能很小，而在其他情况下则可能较大。

HDBSCAN 提供了两种聚类选项。

+   主要的聚类算法，标记为 `hard_clustering`，将每个数据点分配到一个聚类或标记为噪声。这是硬性分配；没有混合的隶属关系。这种方法可能导致一个大的聚类被归类为噪声（聚类标签为 -1），而其他则为许多较小的聚类。微调超参数至关重要 [[参见这里](https://hdbscan.readthedocs.io/en/latest/faq.html)]，因为它选择了一个专门为该领域量身定制的嵌入模型。请查看相关的 [Google Colab](https://github.com/SolanaO/Blogs_Content/blob/master/keyllm_neo4j/KeyBERT_LLM_Neo4j.ipynb)，查看该项目数据集上硬聚类的结果。

+   `软聚类`是HDBSCAN库的一个新特性。在这种方法中，点不会被分配聚类标签，而是被分配一个概率向量。该向量的长度等于找到的聚类数。向量中每个位置的概率值表示该点是该聚类成员的概率。这使得点可能是多个聚类的混合。如果你想更好地理解软聚类是如何工作的，请参阅[HDBSCAN的软聚类工作原理](https://hdbscan.readthedocs.io/en/latest/soft_clustering_explanation.html)。这种方法更适合当前项目，因为它生成了一个较大的、相对相似大小的聚类集。

虽然HDBSCAN在低到中维度数据上表现良好，但随着维度的增加，性能通常会显著下降。一般而言，HDBSCAN在最多大约50维的数据上表现最佳，[[请参阅这里](https://hdbscan.readthedocs.io/en/latest/faq.html)]。

聚类用的文档通常使用BERT家族中的高效变换器进行嵌入，得到的是一个几百维的数据集。

为了减少嵌入向量的维度，我们使用**UMAP**（[统一流形近似与投影](https://umap-learn.readthedocs.io/en/latest/basic_usage.html)），这是一种非线性降维算法，并且是同类中表现最好的算法。它旨在学习数据的流形结构，并找到一个低维嵌入，从而保留该流形的基本拓扑结构。

研究表明，UMAP在将高维数据的整体结构保留到低维时非常有效，同时在性能上优于其他流行算法，如t-SNE和PCA。

## 关键词聚类

+   安装并导入所需的包和库。

```py
# Required installs
!pip install umap-learn
!pip install hdbscan
!pip install -U sentence-transformers

# General imports
import pandas as pd
import numpy as np
import re
import pickle

# Imports needed to generate the BERT embeddings
from sentence_transformers import SentenceTransformer

# Libraries for dimensionality reduction
import umap.umap_ as umap

# Import the clustering algorithm
import hdbscan
```

+   准备数据集，将每个标题的单独五元组中的所有关键词和关键短语聚合成一个独特关键词的单一列表，并将其保存为一个pandas数据框。

```py
# Load the data if needed - titles with 5 extracted keywords
df5 = pd.read_csv(data_path+parsed_keys_file) 

# Create a list of all sublists of keywords and keyphrases
df5_keys = df5.titles_keys.tolist()

# Flatten the list of sublists
flat_keys = [item for sublist in df5_keys for item in sublist]

# Create a list of unique keywords
flat_keys = list(set(flat_keys))

# Create a dataframe with the distinct keywords
keys_df = pd.DataFrame(flat_keys, columns = ['key'])
```

我从884个处理过的标题中获得了将近3000个独特的关键词和关键短语。以下是一个示例：n-可染图、实验、约束、树结构、复杂性等。

+   使用Sentence Transformers生成768维的嵌入。

```py
# Instantiate the embedding model
model = SentenceTransformer('all-mpnet-base-v2')

# Embed the keywords and keyphrases into 768-dim real vector space
keys_df['key_bert'] = keys_df['key'].apply(lambda x: model.encode(x))
```

+   使用UMAP进行降维。

```py
# Reduce to 10-dimensional vectors and keep the local neighborhood at 15
embeddings = umap.UMAP(n_neighbors=15, # Balances local vs. global structure.
                       n_components=10, # Dimension of reduced vectors
                       metric='cosine').fit_transform(list(keys_df.key_bert))

# Add the reduced embedding vectors to the dataframe
keys_df['key_umap'] = embeddings.tolist()
```

+   使用HDBSCAN对10维向量进行聚类。为了保持这篇博客简洁，我将省略与硬聚类相关的参数描述。有关每个参数的详细信息，请参阅[[HDBSCAN参数选择](https://hdbscan.readthedocs.io/en/latest/parameter_selection.html)]。

```py
# Initialize the clustering model
clusterer = hdbscan.HDBSCAN(algorithm='best',
                            prediction_data=True,
                            approx_min_span_tree=True,
                            gen_min_span_tree=True,
                            min_cluster_size=20,
                            cluster_selection_epsilon = .1,
                            min_samples=1,
                            p=None,
                            metric='euclidean',
                            cluster_selection_method='leaf')

# Fit the data
clusterer.fit(embeddings)

# Create soft clusters
soft_clusters = hdbscan.all_points_membership_vectors(clusterer)

# Add the soft cluster information to the data
closest_clusters = [np.argmax(x) for x in soft_clusters]
keys_df['cluster'] = closest_clusters
```

以下是关键词在各个聚类中的分布情况。通过检查关键词和关键短语在软聚类中的分布，发现总共有60个聚类，每个聚类中的元素分布较为均匀，数量从大约20个到近100个不等。

![](../Images/07b5e9852a75b59e177d37efc1149ad4.png)

# 提取聚类描述和标签

在对关键词进行聚类后，我们现在准备再次使用GenAI来增强和优化我们的发现。在这一步，我们将使用大型语言模型（LLM）分析每个聚类，总结关键词和关键短语，并为每个聚类分配一个简短的标签。

尽管这不是必要的，我选择继续使用相同的LLM——Zephyr-7B-Beta。如果您需要下载该模型，请参考相关章节。值得注意的是，我会调整提示，以适应此任务的不同特点。

以下函数旨在为每个聚类提取标签和描述，解析输出并将其集成到pandas数据框中。

```py
def extract_description(df: pd.DataFrame,
                        n: int     
                        )-> pd.DataFrame:
    """
    Use a custom prompt to send to a LLM
    to extract labels and descriptions for a list of keywords.
    """

    one_cluster = df[df['cluster']==n]
    one_cluster_copy = one_cluster.copy()
    sample = one_cluster_copy.key.tolist()

    prompt_clusters= f"""
    <|system|>
    I have the following list of keywords and keyphrases:
    ['encryption','attribute','firewall','security properties',
    'network security','reliability','surveillance','distributed risk factors',
    'still vulnerable','cryptographic','protocol','signaling','safe',
    'adversary','message passing','input-determined guards','secure communication',
    'vulnerabilities','value-at-risk','anti-spam','intellectual property rights',
    'countermeasures','security implications','privacy','protection',
    'mitigation strategies','vulnerability','secure networks','guards']

    Based on the information above, first name the domain these keywords or keyphrases 
  belong to, secondly give a brief description of the domain.
    Do not use more than 30 words for the description!
    Do not provide details!
    Do not give examples of the contexts, do not say 'such as' and do not list the keywords 
  or the keyphrases!
    Do not start with a statement of the form 'These keywords belong to the domain of' or 
  with 'The domain'.

    Cybersecurity: Cybersecurity, emphasizing methods and strategies for safeguarding digital information
    and networks against unauthorized access and threats.
    </s>

    <|user|>
    I have the following list of keywords and keyphrases:
    {sample}
    Based on the information above, first name the domain these keywords or keyphrases belong to, secondly
    give a brief description of the domain.
    Do not use more than 30 words for the description!
    Do not provide details!
    Do not give examples of the contexts, do not say 'such as' and do not list the keywords or the keyphrases!
    Do not start with a statement of the form 'These keywords belong to the domain of' or with 'The domain'.
    <|assistant|>
    """

    # Generate the outputs
    outputs = generator(prompt_clusters,
                    max_new_tokens=120,
                    do_sample=True,
                    temperature=0.1,
                    top_k=10,
                    top_p=0.95)

    text = outputs[0]["generated_text"]

    # Example string
    pattern = "<|assistant|>\n"

    # Extract the output
    response = text.split(pattern, 1)[1].strip(" ")
    # Check if the output has the desired format
    if len(response.split(":", 1)) == 2:
        label  = response.split(":", 1)[0].strip(" ")
        description = response.split(":", 1)[1].strip(" ")
    else:
        label = description = response

    # Add the description and the labels to the dataframe
    one_cluster_copy.loc[:, 'description'] = description
    one_cluster_copy.loc[:, 'label'] = label

    return one_cluster_copy
```

现在我们可以将上述函数应用于每个聚类并收集结果：

```py
import re
import pandas as pd

# Initialize an empty list to store the cluster dataframes
dataframes = []
clusters = len(set(keys_df.cluster))

# Iterate over the range of n values
for n in range(clusters-1):
    df_result = extract_description(keys_df,n)
    dataframes.append(df_result)

# Concatenate the individual dataframes
final_df = pd.concat(dataframes, ignore_index=True)
```

让我们来看一个输出示例。完整的输出列表请参见[Google Colab](https://github.com/SolanaO/Blogs_Content/blob/master/keyllm_neo4j/KeyBERT_LLM_Neo4j.ipynb)。

![](../Images/bd5b0a43c213efe4df24d1d6b122cb5f.png)

我们必须记住，LLM由于其固有的概率性，可能会表现出不可预测的行为。虽然它们通常遵循指令，但它们的遵从性并非绝对。即使是对提示或输入文本的微小修改，也可能导致输出结果的重大差异。在`extract_description()`函数中，我添加了一个功能，在*标签*和*描述*列中记录*响应*，以应对那些没有遵循*Label: Description*格式的情况，如上面第7个聚类的异常输出所示。所有60个聚类的输出可以在附带的[Google Colab](https://github.com/SolanaO/Blogs_Content/blob/master/keyllm_neo4j/KeyBERT_LLM_Neo4j.ipynb)笔记本中查看。

第二个观察是，每个聚类都由LLM独立解析，并且可能会得到重复的标签。此外，可能会出现从输入列表中提取的重复关键词。

该过程的有效性在很大程度上依赖于LLM的选择，使用高性能的LLM时，问题较少。输出结果还依赖于关键词聚类的质量以及聚类中是否存在固有的主题。

缓解这些挑战的策略取决于聚类的数量、数据集的特征以及项目所需的准确性。以下是两种选择：

+   手动修正每个问题，正如我在这个项目中所做的那样。只有60个聚类和三个错误的输出，手动调整以修正错误的输出，并确保每个聚类都有唯一的标签。

+   使用LLM进行修正，尽管这种方法无法保证完全无误。

# 构建知识图谱

## 上传到图谱的数据

有两个csv文件（或如果在单个会话中工作，则是pandas数据框）可以从中提取数据。

+   `articles` - 它包含每篇文章的唯一`id`、`title`、`abstract`和`titles_keys`，后者是提取的五个关键词或关键短语的列表；

+   `keywords` - 包含 `key`、`cluster`、`description` 和 `label` 等列，其中 `key` 包含一个完整的唯一关键词或关键短语列表，其他特征则描述关键词所属的集群。

## Neo4j 连接

要构建知识图谱，我们首先需要设置一个 Neo4j 实例，可以选择如 Sandbox、AuraDB 或 Neo4j Desktop 等选项。对于这个项目，我使用的是 AuraDB 的免费版本。启动一个空白实例并下载其凭证非常简单。

接下来，建立与 Neo4j 的连接。为了方便，我使用了一个自定义的 Python 模块，您可以在 `[utils/neo4j_conn.py](<https://github.com/SolanaO/Blogs_Content/blob/master/keyllm_neo4j/utils/neo4j_conn.py>)` 中找到。这个模块包含了连接和与图数据库交互的方法。

```py
# Install neo4j
!pip install neo4j

# Import the connector
from utils.neo4j_conn import *

# Graph DB instance credentials
URI = 'neo4j+ssc://xxxxxx.databases.neo4j.io'
USER = 'neo4j'
PWD = 'your_password_here'

# Establish the connection to the Neo4j instance
graph = Neo4jGraph(url=URI, username=USER, password=PWD)
```

我们即将构建的图有一个简单的架构，由三个节点和两个关系组成：

![](../Images/71d425143be8fb32291f099340ec6c1e.png)

— 作者图片 —

现在构建图表非常简单，只需两个 Cypher 查询：

```py
# Load Keyword and Topic nodes, and the relationships HAS_TOPIC
query_keywords_topics = """
    UNWIND $rows AS row
    MERGE (k:Keyword {name: row.key})
    MERGE (t:Topic {cluster: row.cluster, description: row.description, label: row.label})
    MERGE (k)-[:HAS_TOPIC]->(t)
    """
graph.load_data(query_keywords_topics, keywords)

# Load Article nodes and the relationships HAS_KEY
query_articles = """
    UNWIND $rows as row
    MERGE (a:Article {id: row.id, title: row.title, abstract: row.abstract})
    WITH a, row
    UNWIND row.titles_keys as key
    MATCH (k:Keyword {name: key})
    MERGE (a)-[:HAS_KEY]->(k)
    """
graph.load_data(query_articles, articles)
```

## 查询图谱

让我们查看节点和关系按类型的分布：

![](../Images/01877831cc27975b9ded0373be022c78.png)

我们可以通过计算与它们连接的关键词关联的文章数量，找出我们文章集合中最受欢迎的主题（或集群）：

![](../Images/cb582327dfffc75475762e64f9ecad96.png)

这是与集群 58 对应的 `Semantics` 节点的快照及其相关的关键词：

![](../Images/42523efd9df95b35eb5d9ef5c4743273.png)

— 作者图片 —

我们还可以通过以下查询来识别标题中常见的词汇：

![](../Images/0fe0b27744cbcc18c6df860cf2221184.png)

# 结论

我们已经看到，如何通过结构化和丰富一组看似无关的短文本条目。使用传统的 NLP 和机器学习，我们首先提取关键词，然后进行聚类。这些结果为 Zephyr-7B-Beta 进行的精炼过程提供了指导和基础。尽管仍然需要对 LLM 进行一些监督，但初步输出已经得到了显著的增强。知识图谱用于揭示语料库中新发现的连接。

我们的关键收获是，没有任何单一方法是完美的。然而，通过战略性地结合不同的技巧，认识到它们的优缺点，我们可以取得更优的结果。

# 参考文献

**Google Colab 笔记本和代码**

+   [关联的 Github 仓库链接。](https://github.com/SolanaO/Blogs_Content/tree/master/keyllm_neo4j)

**数据**

+   学术文章仓库：[arXiv 数据集](https://www.kaggle.com/datasets/Cornell-University/arxiv)，该数据集具有 [CC0: 公共领域](https://creativecommons.org/publicdomain/zero/1.0/) 许可。

**技术文档**

+   [KeyBERT 和 KeyLLM](https://maartengr.github.io/KeyBERT/guides/quickstart.html) — 仓库页面。

+   [HDBSCAN](https://hdbscan.readthedocs.io/en/latest/basic_hdbscan.html#) — 文档。

+   [UMAP](https://umap-learn.readthedocs.io/en/latest/) —— 文档。

**博客与文章**

+   [Maarten Grootendorst，介绍KeyLLM——使用LLM进行关键词提取](/introducing-keyllm-keyword-extraction-with-llms-39924b504813)，数据科学前沿，2023年10月5日。

+   [Benjamin Marie，Zephyr 7B Beta：一个好老师就是你所需要的一切](/zephyr-7b-beta-a-good-teacher-is-all-you-need-c931fcd0bfe7)，数据科学前沿，2023年11月10日。

+   H4团队，Zephyr：LM对齐的直接蒸馏，技术报告，[arXiv: 2310.16944](https://arxiv.org/pdf/2310.16944.pdf)，2023年10月25日。
