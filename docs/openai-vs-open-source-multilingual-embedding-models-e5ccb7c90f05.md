# OpenAI 与开源多语言嵌入模型

> 原文：[https://towardsdatascience.com/openai-vs-open-source-multilingual-embedding-models-e5ccb7c90f05?source=collection_archive---------0-----------------------#2024-02-24](https://towardsdatascience.com/openai-vs-open-source-multilingual-embedding-models-e5ccb7c90f05?source=collection_archive---------0-----------------------#2024-02-24)

## 选择最适合你数据的模型

[](https://ya-lb.medium.com/?source=post_page---byline--e5ccb7c90f05--------------------------------)[![Yann-Aël Le Borgne](../Images/acc1c8b32373d7f345064b89b51869fd.png)](https://ya-lb.medium.com/?source=post_page---byline--e5ccb7c90f05--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e5ccb7c90f05--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e5ccb7c90f05--------------------------------) [Yann-Aël Le Borgne](https://ya-lb.medium.com/?source=post_page---byline--e5ccb7c90f05--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e5ccb7c90f05--------------------------------) ·阅读时间12分钟·2024年2月24日

--

![](../Images/d4106437a23e5edaba980ce8486937d9.png)

我们将使用欧盟人工智能法案作为我们的嵌入模型比较的数据语料库。图像由Dall-E 3生成。

OpenAI最近发布了他们的新一代嵌入模型，称为*embedding v3*，他们[描述](https://openai.com/blog/new-embedding-models-and-api-updates)这些模型是性能最强的嵌入模型，具有更高的多语言性能。该模型有两种类型：一种较小的，称为`text-embedding-3-small`，另一种较大且更强大的，称为`text-embedding-3-large`。

关于这些模型的设计和训练方式，公开的信息非常少。与他们之前的嵌入模型发布（2022年12月，ada-002模型类）一样，OpenAI再次选择了一种闭源的方法，模型只能通过付费API访问。

但是，性能真的那么好吗，值得付费吗？

**这篇文章的动机是通过实证比较这些新模型与它们的开源对手的表现**。我们将依赖一个数据检索工作流，在该工作流中，必须根据用户查询找到语料库中最相关的文档。

我们的语料库将是[欧洲人工智能法案](https://artificialintelligenceact.eu/)，该法案目前正处于最终验证阶段。这个语料库的一个有趣特点是，除了它是全球首个关于人工智能的法律框架外，它还提供24种语言版本。这使得我们能够比较**不同语言家族之间的数据检索准确性**。

本文将涵盖以下两个主要步骤：

+   从多语言文本语料库中生成一个定制的合成问答数据集

+   比较OpenAI与最先进的开源嵌入模型在此自定义数据集上的准确性。

为了重现本文中展示的结果，代码和数据已经公开在[这个Github仓库](https://github.com/Yannael/multilingual-embeddings)中。请注意，《欧盟人工智能法案》作为示例使用，本文中遵循的方法可以适应其他数据语料库。

# 生成自定义的问答数据集

让我们首先开始生成自定义数据上的问答数据集（Q/A），用于评估不同嵌入模型的性能。生成自定义问答数据集有两个好处。首先，它避免了偏见，确保数据集未参与嵌入模型的训练，这种情况可能发生在参考基准上，如[MTEB](https://huggingface.co/spaces/mteb/leaderboard)。其次，它使得评估可以根据特定数据语料库进行定制，这在检索增强应用（RAG）等情况下尤为重要。

我们将遵循[Llama Index文档中](https://blog.llamaindex.ai/fine-tuning-embeddings-for-rag-with-synthetic-data-e534409a3971)建议的简单流程。首先将语料库拆分成多个块。然后，对于每个块，使用大型语言模型（LLM）生成一组合成问题，使得问题的答案位于相应的块中。该过程如下图所示：

![](../Images/debcd01ca6179cf42bb71ec7c684627d.png)

为你的数据生成问答数据集，方法参考[Llama Index](https://blog.llamaindex.ai/fine-tuning-embeddings-for-rag-with-synthetic-data-e534409a3971)

实现这个策略在使用像Llama Index这样的LLM数据框架时非常直接。语料库的加载和文本的拆分可以通过高阶函数方便地完成，如下面的代码所示。

```py
from llama_index.readers.web import SimpleWebPageReader
from llama_index.core.node_parser import SentenceSplitter

language = "EN"
url_doc = "https://eur-lex.europa.eu/legal-content/"+language+"/TXT/HTML/?uri=CELEX:52021PC0206"

documents = SimpleWebPageReader(html_to_text=True).load_data([url_doc])

parser = SentenceSplitter(chunk_size=1000)
nodes = parser.get_nodes_from_documents(documents, show_progress=True)
```

在这个示例中，语料库是《欧盟人工智能法案》的英文版，直接从网络上获取，使用的是这个[官方网址](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A52021PC0206)。我们使用的是2021年4月的草案版本，因为最终版本尚未提供所有欧盟语言。在这个版本中，网址中的英文可以替换为其他23种欧盟官方语言中的任何一种，以获取不同语言的文本（例如，BG代表保加利亚语，ES代表西班牙语，CS代表捷克语，等等）。

![](../Images/b228519bd3b36c7cec689ffd01b3226c.png)

下载《欧盟人工智能法案》在24种官方欧盟语言中的链接（来自[欧盟官网](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A52021PC0206)）

我们使用SentenceSplitter对象将文档拆分成1000个标记的块。对于英文文本，这大约会生成100个块。

然后，每个文档块作为上下文提供给以下提示（[Llama Index库中建议的默认提示](https://github.com/run-llama/llama_index/blob/c058f2531ea86ee74822cb1421ceaeee7098a99f/llama_index/finetuning/embeddings/common.py#L51)）：

```py
prompts={}
prompts["EN"] = """\
Context information is below.

---------------------
{context_str}
---------------------

Given the context information and not prior knowledge, generate only questions based on the below query.

You are a Teacher/ Professor. Your task is to setup {num_questions_per_chunk} questions for an upcoming quiz/examination.
The questions should be diverse in nature across the document. Restrict the questions to the context information provided."
"""
```

该提示的目的是生成关于文档块的问题，就像老师在准备即将到来的小测验一样。每个文档块要生成的问题数量通过参数‘num_questions_per_chunk’传递，我们将其设置为两个。然后，可以通过调用Llama Index库中的generate_qa_embedding_pairs来生成问题：

```py
from llama_index.llms import OpenAI
from llama_index.legacy.finetuning import generate_qa_embedding_pairs

qa_dataset = generate_qa_embedding_pairs(
    llm=OpenAI(model="gpt-3.5-turbo-0125",additional_kwargs={'seed':42}),
    nodes=nodes,
    qa_generate_prompt_tmpl = prompts[language],
    num_questions_per_chunk=2
)
```

我们在此任务中依赖于OpenAI的GPT-3.5-turbo-0125模型，根据OpenAI的说法，这是该系列的旗舰模型，支持16K的上下文窗口，并针对对话进行了优化（[https://platform.openai.com/docs/models/gpt-3-5-turbo](https://colab.research.google.com/corgiredirector?site=https%3A%2F%2Fplatform.openai.com%2Fdocs%2Fmodels%2Fgpt-3-5-turbo)）。

生成的对象‘qa_dataset’包含问题和答案（文档块）对。作为生成问题的示例，以下是前两个问题的结果（其中‘答案’是第一个文档块的文本）：

> 1) 根据说明性备忘录，关于人工智能的统一规则提案（人工智能法案）的主要目标是什么？
> 
> 2) 根据上下文信息，关于人工智能的统一规则提案如何在促进人工智能在欧盟的应用的同时，解决与使用人工智能相关的风险？

文档块和问题的数量取决于语言，从英语的约100个文档块和200个问题，到匈牙利语的200个文档块和400个问题不等。

# OpenAI嵌入模型的评估

我们的评估函数遵循[Llama Index文档](https://docs.llamaindex.ai/en/stable/examples/finetuning/embeddings/finetune_embedding.html)，由两个主要步骤组成。首先，所有答案（文档块）的嵌入存储在一个VectorStoreIndex中，以便高效检索。然后，评估函数循环遍历所有查询，检索最相似的前k个文档，并通过MRR（[平均倒数排名](https://en.wikipedia.org/wiki/Mean_reciprocal_rank)）来评估检索的准确性。

```py
def evaluate(dataset, embed_model, insert_batch_size=1000, top_k=5):
    # Get corpus, queries, and relevant documents from the qa_dataset object
    corpus = dataset.corpus
    queries = dataset.queries
    relevant_docs = dataset.relevant_docs

    # Create TextNode objects for each document in the corpus and create a VectorStoreIndex to efficiently store and retrieve embeddings
    nodes = [TextNode(id_=id_, text=text) for id_, text in corpus.items()]
    index = VectorStoreIndex(
        nodes, embed_model=embed_model, insert_batch_size=insert_batch_size
    )
    retriever = index.as_retriever(similarity_top_k=top_k)

    # Prepare to collect evaluation results
    eval_results = []

    # Iterate over each query in the dataset to evaluate retrieval performance
    for query_id, query in tqdm(queries.items()):
        # Retrieve the top_k most similar documents for the current query and extract the IDs of the retrieved documents
        retrieved_nodes = retriever.retrieve(query)
        retrieved_ids = [node.node.node_id for node in retrieved_nodes]

        # Check if the expected document was among the retrieved documents
        expected_id = relevant_docs[query_id][0]
        is_hit = expected_id in retrieved_ids  # assume 1 relevant doc per query

        # Calculate the Mean Reciprocal Rank (MRR) and append to results
        if is_hit:
            rank = retrieved_ids.index(expected_id) + 1
            mrr = 1 / rank
        else:
            mrr = 0
        eval_results.append(mrr)

    # Return the average MRR across all queries as the final evaluation metric
    return np.average(eval_results)
```

嵌入模型通过`embed_model`参数传递给评估函数，对于OpenAI模型来说，这是一个初始化了模型名称和模型维度的OpenAIEmbedding对象。

```py
from llama_index.embeddings.openai import OpenAIEmbedding

embed_model = OpenAIEmbedding(model=model_spec['model_name'],
                              dimensions=model_spec['dimensions'])
```

`dimensions` API参数可以缩短嵌入（即移除序列末尾的一些数字），而不会失去其表示概念的特性。OpenAI例如在它们的[公告](https://openai.com/blog/new-embedding-models-and-api-updates)中建议，在MTEB基准测试中，嵌入可以缩短到256的大小，同时仍然优于未缩短的`text-embedding-ada-002`嵌入（其大小为1536）。

我们在四个不同的OpenAI嵌入模型上运行了评估函数：

+   两个版本的`text-embedding-3-large`：一个是最低维度（256），另一个是最高维度（3072）。它们分别称为‘OAI-large-256’和‘OAI-large-3072’。

+   OAI-small：`text-embedding-3-small`嵌入模型，维度为1536。

+   OAI-ada-002：传统的`text-embedding-ada-002`模型，维度为1536。

每个模型都在四种不同的语言上进行了评估：英语（EN）、法语（FR）、捷克语（CS）和匈牙利语（HU），分别涵盖了日耳曼语系、罗曼语系、斯拉夫语系和乌拉尔语系的示例。

```py
embeddings_model_spec = {
}

embeddings_model_spec['OAI-Large-256']={'model_name':'text-embedding-3-large','dimensions':256}
embeddings_model_spec['OAI-Large-3072']={'model_name':'text-embedding-3-large','dimensions':3072}
embeddings_model_spec['OAI-Small']={'model_name':'text-embedding-3-small','dimensions':1536}
embeddings_model_spec['OAI-ada-002']={'model_name':'text-embedding-ada-002','dimensions':None}

results = []

languages = ["EN", "FR", "CS", "HU"]

# Loop through all languages
for language in languages:

    # Load dataset
    file_name=language+"_dataset.json"
    qa_dataset = EmbeddingQAFinetuneDataset.from_json(file_name)

    # Loop through all models
    for model_name, model_spec in embeddings_model_spec.items():

        # Get model
        embed_model = OpenAIEmbedding(model=model_spec['model_name'],
                                      dimensions=model_spec['dimensions'])

        # Assess embedding score (in terms of MRR)
        score = evaluate(qa_dataset, embed_model)

        results.append([language, model_name, score])

df_results = pd.DataFrame(results, columns = ["Language" ,"Embedding model", "MRR"])
```

结果中的准确度（以MRR衡量）如下所示：

![](../Images/e85932db410223b301977602a81afcd7.png)

OpenAI模型表现的总结

正如预期的那样，对于大型模型，随着嵌入大小增大到3072，表现有所提升。与小型和传统Ada模型相比，大型模型的表现虽有所提升，但依然比我们预期的要小。为进行比较，我们还在下方报告了OpenAI模型在MTEB基准测试中的表现。

![](../Images/d7f95f6dd6fc1fa806136b1f0a8236bd.png)

OpenAI嵌入模型的表现，详见它们的[官方公告](https://openai.com/blog/new-embedding-models-and-api-updates)。

值得注意的是，在我们的评估中，大型、小型和Ada模型之间的表现差异，比MTEB基准测试中观察到的差异要小，这反映了一个事实，即在大型基准测试中观察到的平均表现，并不一定能反映在定制数据集上获得的结果。

# 开源嵌入模型的评估

关于嵌入的开源研究非常活跃，新的模型定期发布。一个不错的保持最新发布模型的地方是[Hugging Face 😊 MTEB排行榜](https://huggingface.co/spaces/mteb/leaderboard)。

本文中的比较，我们选择了一组最近发布的四种嵌入模型（2024年）。选择标准是它们在MTEB排行榜上的平均得分以及处理多语言数据的能力。以下是所选模型的主要特征总结。

所选的开源嵌入模型

+   [***E5-Mistral-7B-instruct***](https://huggingface.co/intfloat/e5-mistral-7b-instruct)(E5-mistral-7b)：微软的这款E5嵌入模型是从[Mistral-7B-v0.1](https://huggingface.co/mistralai/Mistral-7B-v0.1)初始化的，并在多语言数据集的混合上进行微调。该模型在MTEB排行榜上表现最佳，但也是最大的模型（14GB）。

+   [***multilingual-e5-large-instruct***](https://huggingface.co/intfloat/multilingual-e5-large-instruct)(ML-E5-large)：微软的另一款E5模型，旨在更好地处理多语言数据。它是从[xlm-roberta-large](https://huggingface.co/xlm-roberta-large)初始化的，并在多语言数据集的混合上进行训练。它比E5-Mistral小得多（小10倍），但上下文大小也更小（514）。

+   [***BGE-M3***](https://huggingface.co/BAAI/bge-m3)：该模型由北京人工智能研究院设计，是其针对多语言数据的最先进嵌入模型，支持100多种工作语言。截至2024年2月22日，它尚未在MTEB排行榜上进行基准测试。

+   [***nomic-embed-text-v1***](https://huggingface.co/nomic-ai/nomic-embed-text-v1)(Nomic-Embed)：该模型由[Nomic](https://home.nomic.ai/)设计，声称在性能上优于OpenAI Ada-002和text-embedding-3-small，同时仅为0.55GB大小。有趣的是，该模型是第一个完全可复现和可审计的模型（开放数据和开源训练代码）。

评估这些开源模型的代码与用于OpenAI模型的代码类似。主要的变化在于模型规格，必须指定附加的细节，如最大上下文长度和池化类型。然后，我们对四种语言中的每种模型进行了评估：

```py
embeddings_model_spec = {
}

embeddings_model_spec['E5-mistral-7b']={'model_name':'intfloat/e5-mistral-7b-instruct','max_length':32768, 'pooling_type':'last_token', 
                                        'normalize': True, 'batch_size':1, 'kwargs': {'load_in_4bit':True, 'bnb_4bit_compute_dtype':torch.float16}}
embeddings_model_spec['ML-E5-large']={'model_name':'intfloat/multilingual-e5-large','max_length':512, 'pooling_type':'mean', 
                                      'normalize': True, 'batch_size':1, 'kwargs': {'device_map': 'cuda', 'torch_dtype':torch.float16}}
embeddings_model_spec['BGE-M3']={'model_name':'BAAI/bge-m3','max_length':8192, 'pooling_type':'cls', 
                                 'normalize': True, 'batch_size':1, 'kwargs': {'device_map': 'cuda', 'torch_dtype':torch.float16}}
embeddings_model_spec['Nomic-Embed']={'model_name':'nomic-ai/nomic-embed-text-v1','max_length':8192, 'pooling_type':'mean', 
                                      'normalize': True, 'batch_size':1, 'kwargs': {'device_map': 'cuda', 'trust_remote_code' : True}}

results = []

languages = ["EN", "FR", "CS", "HU"]

# Loop through all models
for model_name, model_spec in embeddings_model_spec.items():

    print("Processing model : "+str(model_spec))

    # Get model
    tokenizer = AutoTokenizer.from_pretrained(model_spec['model_name'])
    embed_model = AutoModel.from_pretrained(model_spec['model_name'], **model_spec['kwargs'])

    if model_name=="Nomic-Embed":
        embed_model.to('cuda')

    # Loop through all languages
    for language in languages:

        # Load dataset
        file_name=language+"_dataset.json"
        qa_dataset = EmbeddingQAFinetuneDataset.from_json(file_name)

        start_time_assessment=time.time()

        # Assess embedding score (in terms of hit rate at k=5)
        score = evaluate(qa_dataset, tokenizer, embed_model, model_spec['normalize'], model_spec['max_length'], model_spec['pooling_type'])

        # Get duration of score assessment
        duration_assessment = time.time()-start_time_assessment

        results.append([language, model_name, score, duration_assessment])

df_results = pd.DataFrame(results, columns = ["Language" ,"Embedding model", "MRR", "Duration"])
```

下面报告了以MRR为标准的准确度结果。

![](../Images/f77ab24c4d15f7334aa730420a285de6.png)

开源模型的性能总结

BGE-M3表现最佳，平均表现紧随其后的是ML-E5-Large、E5-mistral-7b和Nomic-Embed。BGE-M3模型尚未在MTEB排行榜上进行基准测试，我们的结果表明它可能会排在其他模型之前。有趣的是，虽然BGE-M3是针对多语言数据进行优化的，但它在英语上的表现优于其他模型。

我们还报告了每个嵌入模型的处理时间。

![](../Images/026e18ce209b4a9150cc60b529e46e89.png)

通过英文问答数据集的处理时间（以秒为单位）

E5-mistral-7b比其他模型大10倍以上，毫不意外地是最慢的模型。

# 结论

让我们将这八个测试模型的性能并排展示在一个图中。

![](../Images/2098db260ef495cac46c32a79518bcc3.png)

八个测试模型的性能总结

从这些结果中可以得出以下关键观察：

+   **开源模型的表现最好**。由北京人工智能学会开发的 [BGE-M3](https://huggingface.co/BAAI/bge-m3) 模型表现最佳。该模型的上下文长度与 OpenAI 模型相同（8K），大小为 2.2GB。

+   **OpenAI 模型范围的一致性**。大（3072）、小型和遗留版 OpenAI 模型的表现非常相似。然而，减少大模型（256）的嵌入大小会导致性能下降。

+   **语言敏感性**。几乎所有模型（除了 ML-E5-large）在英语上的表现最好。在捷克语和匈牙利语等语言中，性能差异较大。

那么，你是应该选择付费的 OpenAI 订阅，还是托管一个开源嵌入模型？

OpenAI 的 [最近价格调整](https://openai.com/pricing)使得访问其 API 更加实惠，现在每百万个 token 的费用为 $0.13。处理每月百万次查询（假设每次查询大约涉及 1K token）大约需要 $130。根据你的使用场景，租用和维护自己的嵌入服务器可能不具有成本效益。

然而，成本效益并非唯一的考虑因素。其他因素如延迟、隐私和数据处理工作流的控制也可能需要被考虑。开源模型提供了完全控制数据的优势，从而增强了隐私性和定制性。另一方面，OpenAI 的 API 存在延迟问题，有时会导致响应时间延长。

总结来说，选择开源模型还是像 OpenAI 这样的专有解决方案并不是一个简单的答案。开源嵌入模型提供了令人信服的选择，结合了性能和对数据的更大控制。相反，OpenAI 的产品可能仍然吸引那些优先考虑便利性的用户，尤其是当隐私问题不是主要关注点时。

# 有用的链接

+   配套的 Github 仓库：[https://github.com/Yannael/multilingual-embeddings](https://github.com/Yannael/multilingual-embeddings)

+   [你想知道的关于句子嵌入的一切（也许还有更多）](https://osanseviero.github.io/hackerllama/blog/posts/sentence_embeddings/)

+   [OpenAI 博客公告：新的嵌入模型和 API 更新](https://openai.com/blog/new-embedding-models-and-api-updates)

+   [嵌入：OpenAI 指南](https://platform.openai.com/docs/guides/embeddings/embedding-models)

+   [MTEB: 大规模文本嵌入基准](https://arxiv.org/abs/2210.07316) 和 [Hugging Face MTEB 排行榜](https://huggingface.co/spaces/mteb/leaderboard)

+   [文本嵌入：全面指南](/text-embeddings-comprehensive-guide-afd97fce8fb5)

+   [实践者指南：检索增强生成（RAG）](https://cameronrwolfe.substack.com/p/a-practitioners-guide-to-retrieval)

+   [如何为你的 RAG 找到最佳的多语言嵌入模型](/how-to-find-the-best-multilingual-embedding-model-for-your-rag-40325c308ebb)

注意：

+   除非另有说明，所有图片均由作者提供

+   《欧盟人工智能法案草案》根据[委员会的文件重用政策](https://eur-lex.europa.eu/content/legal-notice/legal-notice.html)发布，基于[决定 2011/833/EU](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32011D0833)，可用于商业或非商业目的。

*喜欢这篇文章吗？分享你的想法，给它点个赞，或* [*在 LinkedIn 上与我联系*](https://www.linkedin.com/in/yannaelb/)*。*
