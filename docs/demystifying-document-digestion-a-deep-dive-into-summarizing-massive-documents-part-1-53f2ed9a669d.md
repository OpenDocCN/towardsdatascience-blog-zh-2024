# 高效总结海量文档的基本指南，第一部分

> 原文：[`towardsdatascience.com/demystifying-document-digestion-a-deep-dive-into-summarizing-massive-documents-part-1-53f2ed9a669d?source=collection_archive---------5-----------------------#2024-09-14`](https://towardsdatascience.com/demystifying-document-digestion-a-deep-dive-into-summarizing-massive-documents-part-1-53f2ed9a669d?source=collection_archive---------5-----------------------#2024-09-14)

## 文档摘要在生成式人工智能（GenAI）应用中非常重要，但如果文档太大了怎么办！？继续阅读，了解我是如何解决这个问题的。

[](https://medium.com/@vinayak.sengupta?source=post_page---byline--53f2ed9a669d--------------------------------)![Vinayak Sengupta](https://medium.com/@vinayak.sengupta?source=post_page---byline--53f2ed9a669d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--53f2ed9a669d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--53f2ed9a669d--------------------------------) [Vinayak Sengupta](https://medium.com/@vinayak.sengupta?source=post_page---byline--53f2ed9a669d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--53f2ed9a669d--------------------------------) ·8 分钟阅读·2024 年 9 月 14 日

--

![](img/4a30c3d146fa1c3d85a1f30b369a5e24.png)

“总结大量文本”——使用 GPT-4o 生成的图像

如今，文档摘要已成为使用现代生成式人工智能（GenAI）技术解决的最常见问题之一（如果不是最常见的话）。检索增强生成（RAG）是一个常见且有效的解决架构，用于解决这一问题（如果你想深入了解 RAG 是什么，可以查看这篇[**博客**](https://medium.com/@vinayak.sengupta/exploring-the-core-of-augmented-intelligence-advancing-the-power-of-retrievers-in-rag-frameworks-3ef9fe273764)！）。但是，如果文档本身太大，以至于无法在一次 API 请求中全部发送该怎么办？或者，如果它生成了太多的片段，导致著名的“迷失于中间”上下文问题怎么办？在本文中，我将讨论我们在面对此类问题时所遇到的挑战，并逐步介绍我使用 Greg Kamradt 在其[**GitHub 仓库**](https://github.com/gkamradt/langchain-tutorials/blob/main/data_generation/5%20Levels%20Of%20Summarization%20-%20Novice%20To%20Expert.ipynb)提供的指导所应用的解决方案。

# 一些“*上下文*”

RAG 是一种广泛讨论并且广泛实施的解决方案，用于利用 GenAI 技术优化文档总结。然而，像任何新技术或解决方案一样，它也容易面临边缘案例的挑战，尤其是在今天的企业环境中。两个主要问题是上下文长度与每次提示的成本以及前面提到的“迷失在中间”的上下文问题。让我们深入探讨一下这些挑战。

> **注意**：*我将使用 LangChain、Scikit-Learn、Numpy 和 Matplotlib 库来进行 Python 练习，以便快速迭代。*

# 上下文窗口和成本限制

如今，借助 GenAI 启用的自动化工作流，分析大型文档已经成为行业的期望/要求。人们希望通过简单地提示 LLM，就能快速从医疗报告或财务审计中找到相关信息。但有一个警告，企业文档与我们在学术领域处理的文档或数据集不同，文档的大小通常要大得多，相关信息可能出现在文档的任何地方。因此，像数据清理/过滤这样的方式通常并不是一个可行的选择，因为这些文档的领域知识并不总是可用的。

此外，即使是像 OpenAI 的 GPT-4o 这样最新的 大型语言模型（LLM），上下文窗口为 128K 令牌，也不能一次性处理这些文档，即使能，响应的质量也无法达到标准，尤其是考虑到它所产生的成本。为了展示这一点，我们来看一个真实世界的例子：尝试总结 GitLab 的员工手册，可以从[**这里**](https://kocielnik.gitlab.io/gitlab_handbook_takeaway/about-the-handbook.html)下载。该文档在 MIT 许可证下免费提供，可以在他们的 GitHub [仓库](https://gitlab.com/kocielnik/gitlab_handbook_takeaway/-/blob/master/LICENSE)找到。

1 我们首先加载文档，并初始化我们的 LLM，为了使这个练习更有相关性，我将使用 GPT-4o。

```py
from langchain_community.document_loaders import PyPDFLoader

# Load PDFs
pdf_paths = ["/content/gitlab_handbook.pdf"]
documents = []

for path in pdf_paths:
    loader = PyPDFLoader(path)
    documents.extend(loader.load())

from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
```

2 然后，我们可以将文档分割成更小的块（这是为了 *嵌入*，我会在后续步骤中解释为什么这么做）。

```py
from langchain.text_splitter import RecursiveCharacterTextSplitter

# Initialize the text splitter
text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=0)

# Split documents into chunks
splits = text_splitter.split_documents(documents)
```

3 现在，让我们计算这个文档包含多少个令牌，为此我们将遍历每个文档块并计算出文档总共包含的令牌数。

```py
total_tokens = 0

for chunk in splits:
    text = chunk.page_content  # Assuming `page_content` is where the text is stored
    num_tokens = llm.get_num_tokens(text)  # Get the token count for each chunk
    total_tokens += num_tokens

print(f"Total number of tokens in the book: {total_tokens}")

# Total number of tokens in the book: 254006
```

正如我们所看到的，令牌数量为 254,006，而 GPT-4o 的上下文窗口限制为 128,000。这个文档无法通过 LLM 的 API 一次性发送出去。除此之外，考虑到该模型的定价为每 1K 输入令牌 $0.00500，一次请求 OpenAI 处理这个文档将花费 $1.27！直到你把它放在企业范畴中，面对多个用户以及大量类似的文档日常交互，尤其是在许多 GenAI 解决方案正在诞生的初创公司场景下，这个费用听起来并不那么可怕。

# 迷失在中间

大型语言模型（LLMs）面临的另一个挑战是*中间丢失*的上下文问题，具体内容可以参见这篇[**论文**](https://arxiv.org/abs/2307.03172)。我的研究和 RAG 系统在处理多个文档时的经验表明，LLMs 在从长上下文输入中推断信息时并不十分强大。当相关信息位于上下文的中间时，模型的表现会显著下降。然而，当所需信息位于上下文的开始或结尾时，表现则会有所提升。文档重排序（Document Re-ranking）已成为解决这个特定问题的研究主题。我将在另一篇文章中探讨这些方法。现在，让我们回到我们正在探索的解决方案，它利用了 K 均值聚类。

# 什么是 K 均值聚类？！

好吧，我承认在上一节我偷偷引入了一个技术概念，让我为你解释一下（如果你不熟悉该方法，我会为你详细说明）。

## 先从基础开始

为了理解 K 均值聚类，我们首先应当知道什么是聚类。假设我们有一张凌乱的桌子，上面有钢笔、铅笔和便签纸杂乱无章。为了整理，可以将相似的物品归为一组，比如所有钢笔放在一组，铅笔放在另一组，便签纸放在第三组，最终形成 3 个独立的群组（并非提倡隔离）。聚类就是这个过程，在数据集合中（在我们的案例中是文档文本的不同片段），将相似的数据或信息归为一组，从而在模型中形成清晰的分隔，使得我们的 RAG 系统能够更有效率地选择和提取信息，而不是像贪婪算法那样需要处理所有数据。

## K，是什么意思？

K 均值是一种特定的聚类方法（虽然还有其他方法，但我们先不讨论这些）。让我通过 5 个简单的步骤来解释它是如何工作的：

1.  **选择群组数量（K）**：我们希望将数据划分为多少个群组。

1.  **选择群组中心**：最初，为每个 K 个群组随机选择一个中心值。

1.  **群组分配**：然后，根据每个数据点与先前选择的中心的距离，将数据点分配给各个群组。示例：距离中心 1 最近的项被分配到群组 1，距离中心 2 最近的项被分配到群组 2……依此类推，直到 Kth 群组。

1.  **调整中心点**：在所有数据点被归类后，我们计算每个群组中项目位置的平均值，这些平均值将成为新的中心，以提高准确性（因为我们最初是随机选择的）。

1.  **重复进行**：通过新的中心点，数据点的分配会再次更新为 K 个群组。这一过程会持续进行，直到群组内部的差异（从数学角度来说是***欧几里得* *距离**）最小，同时与其他群组的其他数据点的差异最大，从而实现最优的分隔。

虽然这可能是一个相对简化的解释，但对于我的小伙伴们（科技爱好者），这算法的更详细和技术性的解释可以在[这里](https://www.analyticsvidhya.com/blog/2019/08/comprehensive-guide-k-means-clustering/)找到。

# 理论够了，让我们开始编码吧。

现在我们已经讨论了 K-means 聚类，这是我们优化之旅中的主角，让我们看看这个强大的算法如何在实际中应用，来总结我们的手册。

现在我们已经有了文档文本的块，我们将把它们嵌入到向量中。

```py
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings()

# Embed the chunks
chunk_texts = [chunk.page_content for chunk in splits]  # Extract the text from each chunk
chunk_embeddings = embeddings.embed_documents(chunk_texts)
```

## 可能稍微有点理论

好吧，好吧，也许这里还有更多要学习的——什么是嵌入？向量？！为什么呢？

## 嵌入与向量

想想计算机是如何做事的——它把所有东西看作二进制的，因此，教/指令它的最佳语言就是数字。因此，让复杂的机器学习系统理解我们的数据的最佳方法，就是把所有的文本看作数字，而我们进行这种转换的方法叫做**嵌入（Embedding）**。描述文本或单词的数字列表称为**向量（Vectors）**。

嵌入（Embeddings）可以根据我们如何描述数据以及选择的启发式方法而有所不同。假设我们想描述一个苹果，我们需要考虑它的颜色（红色）、形状（圆形）和大小。每一个特征都可以用数字来编码，比如‘红色’在 1 到 10 的范围内可以用 8 表示，圆形度可以用 9 表示，大小可以用 3 表示（宽度为 3 英寸）。因此，我们描述苹果的向量会是[8,9,3]。当描述文档的不同特征时，这一概念会变得更加复杂，我们希望每个数字都能映射到主题、语义关系等方面。这会导致向量包含数百个或更多的数字。

## 但是，为什么？！

现在，这种方法提供了什么改进呢？首先，正如我之前提到的，它使得 LLMs（大型语言模型）的数据解读变得更加容易，从而提高了模型推理的准确性。其次，它在内存优化（技术术语中称为空间复杂度）方面也有巨大帮助，通过将数据转换为向量来减少内存消耗。向量的范式被称为向量空间，例如：一篇包含 1000 个单词的文档可以被压缩成一个 768 维的向量表示，从而产生一个 768 个数字的表示，而不是 1000 个单词。

更深一点的数学（再次为我的小伙伴们准备的），在计算机语言中，"1234"（作为单词或字符串）将消耗 54 字节的内存，而数字形式的 1234（作为整数）只会消耗 8 字节！因此，如果考虑文档消耗的兆字节，我们也在减少内存管理成本（太棒了，预算！）。

## 我们回来了！

5 使用 Scikit-Learn Python 库进行简单实现时，我们首先选择我们想要的聚类数，在我们的例子中是 15 个。然后我们运行算法，将嵌入的文档拟合成 15 个聚类。参数‘random_state = 42’意味着我们正在打乱数据集，以防止模型中的模式偏差。

还需要注意的是，我们正在将嵌入向量列表转换为 Numpy 数组（Numpy 库中用于高级操作的向量数学表示）。这是因为 Scikit-learn 要求 K-means 操作使用 Numpy 数组。

```py
from sklearn.cluster import KMeans
import numpy as np

num_clusters = 15
# Convert the list of embeddings to a NumPy array
chunk_embeddings_array = np.array(chunk_embeddings)

# Perform K-means clustering
kmeans = KMeans(n_clusters=num_clusters, random_state=42).fit(chunk_embeddings)
```

# 课程结束了...暂时。

我认为这是一个休息的好时机！我们已经在代码和理论方面覆盖了很多内容。但别担心，我将发布第二部分，介绍我们如何利用这些聚类生成大型文档的丰富摘要。将会展示更多有趣的技术，当然，我也会尽我所能解释所有的理论和理解！

所以敬请关注！此外，我非常期待您的反馈和任何评论，这对我改进内容非常有帮助，像往常一样，非常感谢您的阅读，希望它值得您花时间阅读！

![](img/278c53b9d5784ee5ee150b91ba30a43d.png)

摄影：由[Priscilla Du Preez 🇨🇦](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)提供，照片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)
