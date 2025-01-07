# 你的文档正在告诉你什么是相关的：通过链接实现更好的 RAG

> 原文：[https://towardsdatascience.com/your-documents-are-trying-to-tell-you-whats-relevant-better-rag-using-links-386b7433d0f2?source=collection_archive---------3-----------------------#2024-09-21](https://towardsdatascience.com/your-documents-are-trying-to-tell-you-whats-relevant-better-rag-using-links-386b7433d0f2?source=collection_archive---------3-----------------------#2024-09-21)

## 文档数据集本身已经具有结构，应该加以利用。

[](https://medium.com/@briangodsey?source=post_page---byline--386b7433d0f2--------------------------------)[![Brian Godsey](../Images/1a657e68741618b79bf470f34f9f3b26.png)](https://medium.com/@briangodsey?source=post_page---byline--386b7433d0f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--386b7433d0f2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--386b7433d0f2--------------------------------) [Brian Godsey](https://medium.com/@briangodsey?source=post_page---byline--386b7433d0f2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--386b7433d0f2--------------------------------) ·13 分钟阅读·2024年9月21日

--

![](../Images/bb16a49705e46a551ca67dcf91e7de42.png)

图片由 [Jayne Harris](https://unsplash.com/@jayneharr33?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

构建增强检索生成（RAG）应用程序存在多层次的挑战。文档检索是 RAG 工作流中的重要部分，它本身就是一个复杂的过程，可以根据具体应用场景采用不同的方法。

RAG 系统很难找到与微妙输入提示相关的最佳文档集，特别是在完全依赖向量搜索来寻找最佳候选文档时。然而，往往是文档本身在告诉我们在哪里可以找到更多相关信息——通过引用、交叉引用、脚注、超链接等。在本文中，我们将展示一种新的数据模型——关联文档——如何通过使我们能够解析并保留这些直接指向其他文本的引用，从而解锁性能改进，并使它们可以同时被检索，无论这些引用是否被向量搜索忽视。

# AI 捕捉复杂性，但无法捕捉结构

在回答需要来自不同文档的支持性细节的复杂或微妙问题时，RAG 系统通常难以找到所有相关的文档，从而无法提供充分且完整的回答。然而，我们仍几乎完全依赖文本嵌入和向量相似度来定位和检索相关文档。

一个常被低估的事实：在解析、分块和嵌入文本的过程中，很多文档信息都会丢失。文档结构——包括章节层级、标题、脚注、交叉引用、引文和超链接——几乎在典型的文本到向量工作流程中完全丧失，除非我们采取特定的措施来保留它们。当结构和元数据告诉我们哪些文档与我们正在阅读的内容直接相关时，为什么我们不保留这些信息？

特别地，在典型的分块和嵌入过程中，链接和引用通常会被忽略，这意味着它们无法被AI用来帮助回答查询。但是，链接和引用是有价值的信息片段，通常指向更有用的文档和文本——那么，为什么我们不在查询时检查这些目标文档，以防它们有用呢？

程序化解析和跟随链接与引用并不困难，在本文中我们展示了一种简单而强大的实现，专为RAG系统设计。我们展示了如何使用**文档链接**来保留文档片段之间已知的连接，而这些连接通常是向量嵌入和检索无法实现的。

# 文档连接在向量空间中丢失

向量存储中的文档本质上是嵌入到高维向量空间中的知识片段。这些向量本质上是LLM的内部“语言”——给定一个LLM及其所有的内部参数值，包括先前的上下文和状态，一个向量是模型生成文本的起点。因此，向量存储中的所有向量都是LLM可能用来生成响应的嵌入文档，同样，我们也将提示嵌入到向量中，然后用它们在语义向量空间中搜索最近的邻居。这些最近的邻居对应的文档很可能包含能够回答提示的信息。

在向量存储中，向量的相似度在语义上表示文档的相似性，但除了相似性之外，并没有真正的连接概念。然而，彼此接近的文档（通常是一起检索的）可以被视为这些知识片段之间的一种连接，形成一个隐式的知识图谱，其中每个文本片段与其最近的邻居相连接。从这个意义上构建的图谱不会像大多数知识图谱那样是静态或僵化的；它会随着新文档的添加或搜索参数的调整而变化。因此，这并不是一个完美的比较，但这个隐式图谱可以作为一个有用的概念框架，帮助我们理解在RAG系统中，文档检索是如何运作的。

就现实世界的知识而言——与向量表示相比——语义相似性只是文本片段可能相关的许多方式之一。即使在计算机和数字数据表示出现之前，我们也已经将知识连接了几个世纪：词汇表、索引、目录、目录表、字典和交叉引用都是连接知识片段的方式。在软件中实现这些非常简单，但它们通常没有包含在向量存储、RAG 系统和其他生成 AI 应用中。我们的文档正在告诉我们其他哪些知识是重要且相关的；我们只需要为我们的知识库提供理解和跟随这些连接的能力。

# 通过文档链接连接知识

我们已经开发了文档链接，以应对文档告诉我们哪些其他知识相关，但我们的向量存储未能捕捉到这一点，导致文档检索过程出现问题。文档链接是一种简单而强大的方法，用于表示文档之间的定向连接。它概括了我们发现和浏览知识的所有传统方式，无论是通过目录、词汇表、关键字——当然，最容易让程序化解析器跟随的：超链接。这种文档链接的概念允许创建不对称的关系，或者可以用定性元数据标记，供过滤或其他目的使用。链接不仅易于构思和操作，而且能有效扩展到大型动态数据集，支持强大且高效的检索。

# 链接的数据模型

作为一种数据类型，文档链接非常简单。链接信息与文档向量一起作为元数据存储。这意味着，检索给定文档时，自动检索与该文档相关的链接信息。外部链接指向在文档上下文中可能有用的更多信息，内部链接则显示哪些其他文档可能受到给定文档的支持，双向（或无向）链接可以表示其他类型的连接。链接还可以标记附加的元数据，提供定性信息，用于链接或文档的过滤、排名和图遍历算法。

如在文章《通过消除边缘扩展知识图谱》中[更详细地描述](https://thenewstack.io/scaling-knowledge-graphs-by-eliminating-edges/)，我们并不像典型的图数据库实现那样单独存储每个链接，而是采用链接类型和链接组作为中间数据类型，这大大减少了在图遍历过程中对存储和计算的需求。这种实现方式在例如两个文档组密切相关时具有很大优势。

假设我们有一组关于西雅图市的文档（称为 A 组），还有另一组提到西雅图的文档（B 组）。我们希望确保提到西雅图的文档能够找到所有关于西雅图市的文档，因此我们希望将它们链接起来。我们可以将 B 组中的所有文档链接到 A 组中的所有文档，但除非这两组很小，否则这样做会产生大量的连接！我们处理这个问题的方法是创建一个表示关键词“西雅图”（`kw:seattle`）的链接类型对象，然后创建从 B 组中的文档到这个 `kw:seattle` 对象的有向链接，同时创建从 `kw:seattle` 对象到 A 组中文档的链接。这样，每个文档只需要存储一个链接，大大减少了需要存储的链接数，而且没有丢失任何信息。

# 在检索过程中使用文档链接

RAG 系统中检索过程的主要目标是找到一组足够回答给定查询的文档。标准的向量搜索和检索方法会找到与查询在语义上最“相关”的文档，但如果文档的整体内容与查询内容不紧密匹配，可能会遗漏一些支持性文档。

例如，假设我们有一组与西雅图相关的文档集合，如上所述。我们有以下关于太空针塔的问题，这是西雅图的一个著名地标：

> ***“太空针塔附近有什么？”***

从这个提示开始的向量搜索将直接检索提到太空针塔的文档，因为从语义内容角度来看，太空针塔是提示文本中最突出的特征。提到太空针塔的文档很可能也会提到它在西雅图的位置。如果不使用任何文档链接，RAG 系统将不得不主要使用提到太空针塔的文档来回答该提示，无法保证没有直接提到太空针塔的其他有用文档也会被检索并使用。

接下来，我们基于这个太空针塔数据集和查询构建一个实际示例（有代码！）。继续阅读以了解在没有使用链接时，RAG 系统如何遗漏有用文档，然后仅通过遵循原始文档中包含的链接信息再次“找到”有用文档。

# 文档链接的实际应用

为了说明文档链接是如何工作的，以及它如何在其他情况下可能会被遗漏的情况下，建立文档与知识之间的联系，我们来看一个简单的例子。

我们将从包含维基百科页面文本的两个相关文档开始：一个是[太空针塔](https://en.wikipedia.org/wiki/Space_Needle)页面的文档，另一个是太空针塔所在的[Lower Queen Anne](https://en.wikipedia.org/wiki/Lower_Queen_Anne,_Seattle)社区页面的文档。太空针塔文档中有一个指向Lower Queen Anne文档的HTML链接，但反过来没有链接。太空针塔文档的内容如下：

```py
'url': 'https://en.wikipedia.org/wiki/Space_Needle'

The Space Needle is an observation tower in Seattle, Washington, 
United States. Considered to be an icon of the city, it has been 
designated a Seattle landmark. Located in the Lower Queen Anne 
neighborhood, it was built in the Seattle Center for the 1962 
World's Fair, which drew over 2.3 million visitors...
```

除了这两个来自真实、信息丰富的源文档外，我们还添加了四个非常简短、无信息量的文档 —— 两个提到太空针塔，两个没有提到。这些文档（及其虚假的URL）旨在成为无关或不具信息量的文档，例如仅仅评论太空针塔和西雅图的社交媒体帖子，内容如下：

> “太空针塔很高。”

并且

> “Queen Anne是一个人。”

完整的文档集包含在[Colab笔记本](https://drive.google.com/file/d/1Fhf92TXCCtj7IUf8Vg4xLxhExN1zcliW/view?usp=sharing)中。这些是HTML文档，我们使用[BeautifulSoup4](https://beautiful-soup-4.readthedocs.io/en/latest/)以及[LangChain的`HtmlLinkExtractor`](https://python.langchain.com/v0.2/api_reference/community/graph_vectorstores/langchain_community.graph_vectorstores.extractors.html_link_extractor.HtmlLinkExtractor.html)进行处理，将这些链接通过`add_links`函数添加回`Document`对象，专门用于在`GraphVectorStore`中使用它们，这是[LangChain代码库](https://api.python.langchain.com/en/latest/community_api_reference.html#module-langchain_community.graph_vectorstores)中的相对较新功能，[由我在DataStax的同事们贡献](https://www.datastax.com/blog/now-in-langchain-graph-vector-store-add-structured-data-to-rag-apps)。所有这些都是开源的。

每个文档的处理流程如下：

```py
from langchain_core.documents import Document
from langchain_core.graph_vectorstores.links import add_links
from langchain_community.graph_vectorstores.extractors.html_link_extractor import HtmlInput, HtmlLinkExtractor

soup_doc = BeautifulSoup(html_doc, 'html.parser')
doc = Document(
        page_content=soup_doc.get_text(),
        metadata={"source": url}
    )
doc.metadata['content_id'] = url  # the ID for Links to point to this document
html_link_extractor = HtmlLinkExtractor()add_links(doc, html_link_extractor.extract_one(HtmlInput(soup_doc, url)))
```

[使用`cassio`](https://cassio.org/)，我们如下初始化`GraphVectorStore`：

```py
from langchain_openai import OpenAIEmbeddings
from langchain_community.graph_vectorstores.cassandra import CassandraGraphVectorStore

# Create a GraphVectorStore, combining Vector nodes and Graph edges.
EMBEDDING = 'text-embedding-3-small'
gvstore = CassandraGraphVectorStore(OpenAIEmbeddings(model=EMBEDDING)) 
```

我们以标准方式为RAG链设置了LLM和其他助手 — [查看笔记本以了解详细信息](https://drive.google.com/file/d/1Fhf92TXCCtj7IUf8Vg4xLxhExN1zcliW/view?usp=sharing)。请注意，虽然这里使用的几乎所有内容都是开源的，但在笔记本中我们使用了两个SaaS产品，OpenAI和DataStax的*Astra* — 分别是LLM和向量数据存储 — 它们都有免费的使用层级。详情请参考[LangChain文档](https://python.langchain.com/docs/integrations/text_embedding/)以了解替代方案。

# 使用默认设置运行RAG

我们可以使用`depth=0`的图形检索器端到端运行RAG系统 —— 这意味着完全不进行图遍历 —— 并使用其他默认参数如下：

```py
retriever = gvstore.as_retriever(
    search_kwargs={
        "depth": 0,  # depth of graph traversal; 0 is no traversal at all
    }
)
```

这将产生如下输出：

```py
Question:
 What is close to the Space Needle? 

Retrieved documents:
['https://TheSpaceNeedleisGreat',
 'https://TheSpaceNeedleisTALL',
 'https://en.wikipedia.org/wiki/Space_Needle',
 'https://SeattleIsOutWest',
 'https://en.wikipedia.org/wiki/Lower_Queen_Anne,_Seattle',
 'https://QueenAnneWasAPerson']

LLM response:
('The Space Needle is close to several locations in the Lower Queen Anne '
 'neighborhood, including Climate Pledge Arena, the Exhibition Hall, McCaw '
 'Hall, Cornish Playhouse, Bagley Wright Theater, the studios for KEXP radio, '
 'SIFF Cinema Uptown, and On the Boards.')
```

当然，在实际场景中，RAG 系统不会像我们这里一样检索完整的文档集。

# 一个更现实的场景：我们无法检索所有文档。

对每个查询检索所有文档在一些情况下既不实际也不可能。这也违背了使用向量搜索的初衷。在所有实际场景中，每次查询只能检索到一小部分文档，这就是为什么将最相关和最有帮助的文档放在列表前面如此重要的原因。

为了让我们的示例更加贴近现实，我们将检索器的设置更改为`k=3`，这意味着每次向量搜索最多返回三份文档。这意味着在六份文档中——根据向量相似性，最不相似或最不相关的三份文档将被排除在返回的文档集之外。我们可以像这样更改检索器的设置：

```py
retriever = gvstore.as_retriever(
    search_kwargs={
        "depth": 0,  # depth of graph traversal; 0 is no traversal at all
        "k": 3       # number of docs returned by initial vector search---not including graph Links
    }
)
```

使用这些设置查询系统时，会得到以下输出：

```py
Question:
 What is close to the Space Needle? 

Retrieved documents:
['https://TheSpaceNeedleisGreat',
 'https://TheSpaceNeedleisTALL',
 'https://en.wikipedia.org/wiki/Space_Needle']

LLM response:
('The context does not provide specific information about what is close to the '
 'Space Needle. It only mentions that it is located in the Lower Queen Anne '
 'neighborhood and built for the Seattle Center for the 1962 World's Fair.')
```

我们可以看到，这个最终的响应比之前的响应要少得多的信息，因为我们现在只能访问到一半的文档集，而不是能够使用六份文档来生成回应。

这里有一些重要的注意点。

1.  一份被遗漏的文档是*Lower Queen Anne* 文档，它是唯一一份描述太空针塔所在附近一些重要地点的文档。

1.  *Lower Queen Anne* 文档并未特别提及太空针塔，而另外三份文档则提到了。因此，最初的查询“太空针塔附近有什么？”返回这三份文档是有道理的。

1.  关于太空针塔的主要文档直接链接到*Lower Queen Anne*，任何好奇的人可能都会点击这个链接来了解该地区。

1.  如果没有任何链接或图遍历的意识，这个RAG系统会检索最语义相似的文档——包括两份信息量较少的文档——并错过了那篇包含最多信息来回答查询的文章。

现在，让我们看看文档链接如何影响结果。

# 跟踪链接可以找到缺失的信息。

对我们的检索器设置进行简单的更改——将`depth=1`——使得检索器能够跟踪初步通过向量搜索检索到的文档中的任何链接。（作为参考，设置`depth=2`不仅会跟踪初始文档集中的链接，还会跟踪结果文档集中下一组链接——但我们暂时不往这个方向走。）

我们像这样更改了检索器的`depth`参数：

```py
retriever = gvstore.as_retriever(
    search_kwargs={
        "depth": 1,  # depth of graph traversal; 0 is no traversal at all
        "k": 3       # number of docs returned by initial vector search---not including graph Links
    }
)
```

这将产生以下输出：

```py
Question:
 What is close to the Space Needle? 

Retrieved documents:
['https://TheSpaceNeedleisGreat',
 'https://TheSpaceNeedleisTALL',
 'https://en.wikipedia.org/wiki/Space_Needle',
 'https://en.wikipedia.org/wiki/Lower_Queen_Anne,_Seattle']

LLM response:
('The Space Needle is located in the Lower Queen Anne neighborhood, which '
 'includes Climate Pledge Arena, Exhibition Hall, McCaw Hall, Cornish '
 'Playhouse, Bagley Wright Theater, the studios for KEXP radio, a three-screen '
 'movie theater (SIFF Cinema Uptown), and On the Boards, a center for '
 'avant-garde theater and music.')
```

我们可以看到，通过向量搜索检索到的前`k`个文档与之前一样是那三篇文档，但设置`depth=1`指示系统跟踪这三篇文档的链接，并将这些链接的文档也包含进来。因此，从*太空针塔*文档到*Lower Queen Anne*的直接链接也包括了该文档，这使得LLM能够获得其正确回答查询所需的邻近信息。

# 文档链接可以提升RAG应用程序的效果。

这种向量和图检索的混合方法可以显著增强RAG应用程序结果的上下文相关性和多样性。通过确保系统检索到最符合上下文且多样化的内容，能够减少幻觉现象，并提高结果的质量。

除了提高RAG系统的响应质量外，文档链接在生产系统中的实现还具有一些优势。其有利的特性包括：

1.  **无损** — 原始内容在节点中保持完整，确保在图形创建过程中不会丢失任何信息。这保留了数据的完整性，减少了随着需求变化而频繁重新索引的需要，并且利用了LLM从上下文线索中提取答案的优势。

1.  **无需人工干预** — 该方法不需要专家介入来完善知识提取。相反，通过在现有的向量搜索管道中添加基于关键词、超链接或其他文档属性的边缘提取功能，可以实现自动添加链接。

1.  **可扩展** — 图形创建过程涉及对内容的直接操作，而无需使用LLM来生成知识图谱。

性能基准和更详细的文档链接扩展分析可参考[之前提到的文章](https://thenewstack.io/scaling-knowledge-graphs-by-eliminating-edges/)。

一如既往地，这里也存在一些限制。如果您的文档集确实没有链接或其他结构，本文所提出的策略效果会很有限。此外，尽管构建和遍历图形连接可以非常强大，但它也会增加检索过程的复杂性，可能会带来调试和优化上的挑战，尤其是在图形遍历深度达到2层或更多时。

总体而言，将文档链接纳入RAG系统结合了传统的确定性软件方法、图算法和现代AI技术的优势。通过明确地定义文档之间的链接，我们增强了AI像人类研究者一样浏览知识的能力，不仅提高了检索的准确性，还增加了响应的上下文深度。这种方法创造了更为强大、能够更好适应复杂人类求知方式的系统。

# 开始使用文档链接

本文的完整代码可以在[此Colab笔记本](https://drive.google.com/file/d/1Fhf92TXCCtj7IUf8Vg4xLxhExN1zcliW/view?usp=sharing)中找到。另外，查看我在DataStax的同事发布的[这篇介绍性博客文章](https://www.datastax.com/blog/now-in-langchain-graph-vector-store-add-structured-data-to-rag-apps?utm_medium=byline&utm_source=tds&utm_campaign=graph&utm_content=doc-linking)，或者查看LangChain中[GraphVectorStore](https://api.python.langchain.com/en/latest/community_api_reference.html#module-langchain_community.graph_vectorstores)的[文档](https://api.python.langchain.com/en/latest/community_api_reference.html#module-langchain_community.graph_vectorstores)，了解详细的API信息以及如何使用文档链接增强你的RAG应用，并推动知识系统的能力边界。

*作者：Brian Godsey，博士（*[*LinkedIn*](https://bit.ly/4enqFRa)*)——数学家、数据科学家和工程师 // 在* [*DataStax*](https://www.datastax.com/) *从事人工智能产品工作* // 编写了书籍* [*像数据科学家一样思考*](https://manning.com/books/think-like-a-data-scientist?a_aid=thinklikeadatascientist&a_bid=eb49dc22)
