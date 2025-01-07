# 使用LLM进行高效文档分块：一次解锁一个知识块

> 原文：[https://towardsdatascience.com/efficient-document-chunking-using-llms-unlocking-knowledge-one-block-at-a-time-355717a88c5c?source=collection_archive---------0-----------------------#2024-10-21](https://towardsdatascience.com/efficient-document-chunking-using-llms-unlocking-knowledge-one-block-at-a-time-355717a88c5c?source=collection_archive---------0-----------------------#2024-10-21)

[](https://medium.com/@peronc79?source=post_page---byline--355717a88c5c--------------------------------)[![Carlo Peron](../Images/e6db9521113aa6a2dd43b0b2aa6687b5.png)](https://medium.com/@peronc79?source=post_page---byline--355717a88c5c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--355717a88c5c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--355717a88c5c--------------------------------) [Carlo Peron](https://medium.com/@peronc79?source=post_page---byline--355717a88c5c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--355717a88c5c--------------------------------) ·阅读时长：8分钟·2024年10月21日

--

![](../Images/8d39f802c75bb432d069186938148b8c.png)

划分两个块的过程——作者插图

本文解释了如何使用LLM（大语言模型）根据“想法”概念对文档进行分块。

我在这个示例中使用了OpenAI的gpt-4o模型，但同样的方法也可以应用于其他任何LLM，如Hugging Face、Mistral等。

每个人都可以免费访问这篇[文章](https://medium.com/@peronc79/355717a88c5c?sk=1cc4e46c40708d5057d54da391035cfa)。

**文档分块的考虑事项**

在认知心理学中，块代表一个“信息单元”。

这一概念也可以应用于计算：使用LLM（大语言模型），我们可以分析文档并生成一组块，通常是可变长度的，每个块表达一个完整的“想法”。

这意味着系统将文档划分为“文本块”，每个块表达一个统一的概念，而不会在同一块中混合不同的想法。

目标是创建一个由独立元素组成的知识库，这些元素之间可以相互关联，而不会在同一块中重叠不同的概念。

当然，在分析和划分过程中，如果某个想法在不同的部分中重复或在同一文档中以不同的方式表达，可能会有多个块表达相同的想法。

**开始使用**

第一步是确定一个将成为我们知识库一部分的文档。

这通常是一个PDF或Word文档，可以逐页或逐段阅读并转换为文本。

为简单起见，假设我们已经有一个像以下这样的文本段落列表，这些段落是从 ***环游世界八十天*** 中提取的：

```py
documents = [
    """On October 2, 1872, Phileas Fogg, an English gentleman, left London for an extraordinary journey. 
    He had wagered that he could circumnavigate the globe in just eighty days. 
    Fogg was a man of strict habits and a very methodical life; everything was planned down to the smallest detail, and nothing was left to chance.
    He departed London on a train to Dover, then crossed the Channel by ship. His journey took him through many countries, 
    including France, India, Japan, and America. At each stop, he encountered various people and faced countless adventures, but his determination never wavered.""",

    """However, time was his enemy, and any delay risked losing the bet. With the help of his faithful servant Passepartout, Fogg had to face 
    unexpected obstacles and dangerous situations.""",

    """Yet, each time, his cunning and indomitable spirit guided him to victory, while the world watched in disbelief.""",

    """With one final effort, Fogg and Passepartout reached London just in time to prove that they had completed their journey in less than eighty days. 
    This extraordinary adventurer not only won the bet but also discovered that the true treasure was the friendship and experiences he had accumulated along the way."""
]
```

假设我们正在使用一个接受有限 token 数量的 LLM 作为输入和输出，我们将其称为 *input_token_nr* 和 *output_token_nr*。

对于此示例，我们将 *input_token_nr* 设置为 300，*output_token_nr* 设置为 250。

这意味着为了成功分割，提示和要分析的文档的 token 数量必须少于 300，而由 LLM 生成的结果必须消耗不超过 250 个 token。

使用 [tokenizer](https://platform.openai.com/tokenizer) 工具，我们看到我们的知识库文档由 254 个 token 组成。

因此，一次性分析整个文档是不可行的，因为尽管输入可以在一次调用中处理，但它无法适应输出。

因此，作为准备步骤，我们需要将原始文档划分为不超过 250 个 token 的块。

然后，这些块将传递给 LLM，LLM 会进一步将其拆分成小块。

为了谨慎起见，我们将最大块大小设置为 200 个 token。

**生成块**

生成块的过程如下：

1.  考虑知识库（KB）中的第一个段落，确定它所需的 token 数量，如果小于 200，它就成为块的第一个元素。

1.  分析下一个段落的大小，如果与当前块的大小合并后少于 200 个 token，就将其添加到块中，并继续处理剩余的段落。

1.  当尝试添加另一个段落时，如果导致块的大小超过限制，则该块达到了最大尺寸。

1.  从第一步开始，重复执行，直到所有段落都被处理完毕。

块生成过程假设为了简单起见，每个段落小于最大允许的大小（否则，段落本身必须拆分成更小的元素）。

为了执行此任务，我们使用来自 LLMChunkizerLib/chunkizer.py 库的 *llm_chunkizer.split_document_into_blocks* 函数，可以在以下仓库中找到该库 — [LLMChunkizer](https://github.com/peronc/LLMChunkizer/)。

从视觉上看，结果如图 1 所示。

![](../Images/366d6fb7dc3c658b31fa9d0481492dca.png)

图 1 — 将文档拆分成最大为 200 个 token 的块 — 图像由作者提供

在生成块时，唯一需要遵守的规则是不得超过最大允许的大小。

不对文本的含义进行分析或假设。

**生成小块**

下一步是将块拆分为每个表达相同思想的块。

对于此任务，我们使用来自 *LLMChunkizerLib/chunkizer.py* 库的 *llm_chunkizer.chunk_text_with_llm* 函数，该库也可以在同一个仓库中找到。

结果如图 2 所示。

![](../Images/d38ce011f33c722eaeb5e5dea015397a.png)

图 2 — 将块拆分成小块 — 图像由作者提供

这个过程是线性进行的，允许LLM自由决定如何形成块。

**处理两个块之间的重叠**

如前所述，在块拆分过程中，仅考虑长度限制，而不考虑表达相同想法的相邻段落是否被拆分到不同块中。

如图1所示，“bla bla bla”概念（代表一个统一的想法）被拆分到两个相邻的块之间。

如图2所示，块分割器一次只处理一个块，这意味着LLM无法将此信息与下一个块关联（它甚至不知道存在下一个块），因此，将其放入最后一个拆分的块中。

这个问题在导入过程中经常发生，特别是在导入一个无法完全放入单个LLM提示中的长文档时。

为了解决这个问题，*llm_chunkizer.chunk_text_with_llm* 如图3所示：

1.  从前一个块生成的最后一个块（或最后N个块）将从“有效”块列表中移除，并将其内容添加到下一个要拆分的块中。

1.  *New Block2* 再次传递给分块函数。

![](../Images/17201eb773dba1ba31362002dc138ad4.png)

图3 — 处理重叠 — 图片由作者提供

如图3所示，块M的内容被更有效地拆分为两个块，保持了概念“bla bla bla”的连贯性。

这个解决方案背后的思想是，前一个块的最后N个块代表独立的想法，而不仅仅是无关的段落。

因此，将它们添加到新块中，可以让LLM生成类似的块，同时创建一个新块，将之前无视意义而被拆分的段落重新联合。

**分块结果**

最后，系统生成以下6个块：

```py
0: On October 2, 1872, Phileas Fogg, an English gentleman, left London for an extraordinary journey. He had wagered that he could circumnavigate the globe in just eighty days. Fogg was a man of strict habits and a very methodical life; everything was planned down to the smallest detail, and nothing was left to chance.  
1: He departed London on a train to Dover, then crossed the Channel by ship. His journey took him through many countries, including France, India, Japan, and America. At each stop, he encountered various people and faced countless adventures, but his determination never wavered.  
2: However, time was his enemy, and any delay risked losing the bet. With the help of his faithful servant Passepartout, Fogg had to face unexpected obstacles and dangerous situations.  
3: Yet, each time, his cunning and indomitable spirit guided him to victory, while the world watched in disbelief.  
4: With one final effort, Fogg and Passepartout reached London just in time to prove that they had completed their journey in less than eighty days.  
5: This extraordinary adventurer not only won the bet but also discovered that the true treasure was the friendship and experiences he had accumulated along the way.
```

**关于块大小的考虑**

让我们看看当原始文档被拆分成最大大小为1000个标记的较大块时会发生什么。

使用较大的块大小时，系统生成4个块而不是6个。

这种行为是预期的，因为LLM可以一次分析更大部分的内容，并能够使用更多的文本来表示一个单一的概念。

下面是此情况下的块：

```py
0: On October 2, 1872, Phileas Fogg, an English gentleman, left London for an extraordinary journey. He had wagered that he could circumnavigate the globe in just eighty days. Fogg was a man of strict habits and a very methodical life; everything was planned down to the smallest detail, and nothing was left to chance.  
1: He departed London on a train to Dover, then crossed the Channel by ship. His journey took him through many countries, including France, India, Japan, and America. At each stop, he encountered various people and faced countless adventures, but his determination never wavered.  
2: However, time was his enemy, and any delay risked losing the bet. With the help of his faithful servant Passepartout, Fogg had to face unexpected obstacles and dangerous situations. Yet, each time, his cunning and indomitable spirit guided him to victory, while the world watched in disbelief.  
3: With one final effort, Fogg and Passepartout reached London just in time to prove that they had completed their journey in less than eighty days. This extraordinary adventurer not only won the bet but also discovered that the true treasure was the friendship and experiences he had accumulated along the way.
```

**结论**

进行多次块分割尝试非常重要，每次都改变传递给块分割器的块大小。

每次尝试后，应该审查结果，以确定哪种方法最符合预期的结果。

**敬请期待**

在下一篇文章中，我将展示如何使用LLM来检索块 — [LLMRetriever](https://github.com/peronc/LLMRetriever)。

你可以在我的仓库中找到所有代码和更多示例 — [LLMChunkizer](https://github.com/peronc/LLMChunkizer/)。

如果你想进一步讨论，欢迎通过[LinkedIn](https://www.linkedin.com/in/carlo-peron)与我联系。
