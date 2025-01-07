# 使用本地文档运行STORM AI研究系统

> 原文：[https://towardsdatascience.com/running-the-storm-ai-research-system-with-your-local-documents-e413ea2ae064?source=collection_archive---------3-----------------------#2024-10-28](https://towardsdatascience.com/running-the-storm-ai-research-system-with-your-local-documents-e413ea2ae064?source=collection_archive---------3-----------------------#2024-10-28)

## 使用AI辅助研究FEMA灾难响应文档

[](https://medium.com/@astrobagel?source=post_page---byline--e413ea2ae064--------------------------------)[![Matthew Harris](../Images/4fa3264bb8a028633cd8d37093c16214.png)](https://medium.com/@astrobagel?source=post_page---byline--e413ea2ae064--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e413ea2ae064--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e413ea2ae064--------------------------------) [Matthew Harris](https://medium.com/@astrobagel?source=post_page---byline--e413ea2ae064--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--e413ea2ae064--------------------------------) ·16分钟阅读·2024年10月28日

--

![](../Images/451f325bbb6fd2a154c72081210f8eb8.png)

STORM通过视角引导的问题提问在模拟对话中研究主题。[来源](https://arxiv.org/abs/2402.14207)

TL;DR

*LLM代理的使用在处理多步骤、长上下文的研究任务时变得越来越普遍，因为传统的RAG直接提示方法有时会遇到困难。在本文中，我们将探讨由斯坦福大学开发的一种新型且有前景的技术* ***S****ynthesis of* ***T****opic* ***O****utlines through* ***R****etrieval and* ***M****ulti-perspective Question Asking (*[*STORM*](https://arxiv.org/abs/2402.14207)*), 它使用LLM代理模拟“视角引导的对话”以实现复杂的研究目标，并生成丰富的研究文章，供人们在写作前研究使用。STORM最初是为了从网络资源中收集信息而开发的，但也支持搜索本地文档向量存储。在本文中，我们将展示如何实现STORM以支持基于AI的本地PDF研究，使用美国FEMA灾难准备和援助文档。*

看到利用大语言模型（LLM）进行知识检索在相对较短的时间内取得的进展，真是令人惊叹。自从2020年发布的[首次关于检索增强生成（RAG）论文](https://arxiv.org/abs/2005.11401)以来，我们见证了这个生态系统的发展，现已涵盖了[一系列可用的技术](https://arxiv.org/html/2312.10997v5#S2)。其中更先进的技术之一是代理RAG，LLM代理通过迭代和优化文档检索来解决更复杂的研究任务。这类似于人类进行研究的方式，通过探索多种不同的搜索查询来建立更清晰的背景，有时还会与其他人讨论这一主题，并将所有信息整合成最终的结果。而单回合RAG，即便采用了查询扩展和重新排序等技术，在应对像这样的复杂多跳研究任务时，仍然存在困难。

使用代理框架（如[Autogen](https://microsoft.github.io/autogen/0.2/)、[CrewAI](https://www.crewai.com)和[LangGraph](https://www.langchain.com/langgraph)）以及特定的AI研究助手（如[GPT Researcher](https://github.com/assafelovic/gpt-researcher)）进行知识检索的模式有很多。在本文中，我们将探讨斯坦福大学开发的一个由LLM驱动的研究写作系统，名为**S**ynthesis of **T**opic **O**utlines through **R**etrieval and **M**ulti-perspective Question Asking（[STORM](https://arxiv.org/abs/2402.14207)）。

# STORM AI研究写作系统

STORM应用了一种巧妙的技术，其中LLM代理模拟“视角引导对话”以达成研究目标，并扩展了“基于大纲的RAG”，以生成更丰富的文章内容。

该系统配置为生成类似维基百科风格的文章，并在一组10名经验丰富的维基百科编辑者中进行了测试。

![](../Images/0e52360332e7847eb9aa2237a73652ac.png)

10名经验丰富的维基百科编辑者对STORM在实际使用中的感知有用性的调查结果。[来源](https://arxiv.org/abs/2402.14207)。

总体反响积极，70%的编辑认为该工具在他们的*写作前*阶段研究一个主题时会非常有用。希望未来的调查能够包括超过10名编辑，但需要注意的是，作者也通过使用FreshWiki（一组近期高质量的维基百科文章数据集）对传统文章生成方法进行了基准测试，其中STORM被发现优于以往的方法。

![](../Images/4076dab0bb937a746d51e0eb93b03985.png)

由10名经验丰富的维基百科编辑者对STORM和*oRAG*生成的20对文章进行人工评估。每对文章由两名维基百科编辑者评估。[来源](https://arxiv.org/abs/2402.14207)。

STORM是[开源的](https://github.com/stanford-oval/storm/tree/main)，并作为一个[Python包](https://pypi.org/project/knowledge-storm/)提供，另外还支持使用如[LangGraph](https://langchain-ai.github.io/langgraph/tutorials/storm/storm/)等框架的实现。最近，STORM已被增强以支持一种名为[Co-STORM](https://www.arxiv.org/abs/2408.15232)的人类-AI协作知识策划方法，将人类置于AI辅助研究环路的中心。

尽管它在自动化和人工评估中都显著优于基准方法，但作者也承认存在一些局限性。目前它还不是多模态的，生成的内容也未达到经验丰富的人工质量——我觉得它目前还不适合这一点，更多的是针对*写作前*的研究，而非最终文章——另外，参考文献方面也存在一些需要进一步改进的细节。话虽如此，如果你有一个深入的研究任务，值得一试。

你可以在[网上](https://storm.genie.stanford.edu/)试用STORM——它非常有趣！——并配置为使用网络信息进行研究。

# **但如果用自己的数据运行STORM呢？**

许多组织希望将AI研究工具与他们自己的内部数据结合使用。STORM的作者们做得很好，记录了如何将STORM与不同的LLM提供者以及本地向量数据库结合使用，这意味着你可以在自己的文档上运行STORM。

那么，让我们来试试吧！

# 设置与代码

你可以在[这里](https://github.com/dividor/storm-with-local-docs)找到本文的代码，包含了环境设置说明以及如何收集一些示例文档来进行演示。

# FEMA灾难准备和援助文档

我们将使用34份由美国联邦应急管理局（[FEMA](https://www.fema.gov)）创建的PDF文档，帮助人们为灾难做准备并进行响应。这些文档可能通常不是人们用来撰写深入研究文章的内容，但我很想看看AI如何帮助人们为灾难做好准备。

……我已经编写了处理FEMA报告的代码，这些代码来自之前的一些博客文章，已经包含在上面链接的代码库中。😊

# 解析与切分

一旦我们有了文档，就需要将其拆分成更小的文档，以便STORM能够在语料库中查找特定的主题。由于STORM最初旨在生成类似维基百科的文章，我选择尝试两种方法：（i）简单地通过[LangChain的PyPDFLoader](https://python.langchain.com/docs/integrations/document_loaders/pypdfloader/)按页将文档拆分为子文档，从而粗略模拟一个包含多个子主题的维基百科页面。许多FEMA的PDF是单页文档，看起来与维基百科文章相差不大；（ii）进一步将文档切分为更小的部分，更可能涵盖一个离散的子主题。

这些当然是*非常*基础的解析方法，但我想看看这两种技术在结果上的差异。任何对STORM在本地文档中的严肃使用都应该投入所有常见的配对优化工作。

```py
def parse_pdfs():
    """
    Parses all PDF files in the specified directory and loads their content.

    This function iterates through all files in the directory specified by PDF_DIR,
    checks if they have a .pdf extension, and loads their content using PyPDFLoader.
    The loaded content from each PDF is appended to a list which is then returned.

    Returns:
        list: A list containing the content of all loaded PDF documents.
    """
    docs = []
    pdfs = os.listdir(PDF_DIR)
    print(f"We have {len(pdfs)} pdfs")
    for pdf_file in pdfs:
        if not pdf_file.endswith(".pdf"):
            continue
        print(f"Loading PDF: {pdf_file}")
        file_path = f"{PDF_DIR}/{pdf_file}"
        loader = PyPDFLoader(file_path)
        docs = docs + loader.load()
        print(f"Loaded {len(docs)} documents")

    return docs

docs = parse_pdfs()

text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
chunks = text_splitter.split_documents(docs)
```

# 元数据增强

[STORM的示例文档](https://github.com/stanford-oval/storm/blob/main/examples/storm_examples/README.md)要求文档具有元数据字段‘URL’，‘title’，和‘description’，其中‘URL’应该是唯一的。由于我们正在拆分PDF文档，因此没有单独页面和块的标题和描述，因此我选择使用简单的LLM调用来生成这些内容。

对于URLs，我们有每个PDF页面的链接，但对于页面内的块，复杂的知识检索系统可以通过布局检测模型生成元数据，这样文本块区域就可以在相应的PDF中高亮显示，但对于这个演示，我只是简单地在URL中添加了一个‘_id’查询参数，这个参数不会做任何事情，只是确保它们对于不同的块是唯一的。

```py
def summarize_text(text, prompt):
    """
    Generate a summary of some text based on the user's prompt

    Args:

    text (str) - the text to analyze
    prompt (str) - prompt instruction on how to summarize the text, eg 'generate a title'

    Returns:

    summary (text) - LLM-generated summary

    """
    messages = [
        (
            "system",
            "You are an assistant that gives very brief single sentence description of text.",
        ),
        ("human", f"{prompt} :: \n\n {text}"),
    ]
    ai_msg = llm.invoke(messages)
    summary = ai_msg.content
    return summary

def enrich_metadata(docs):
    """
    Uses an LLM to populate 'title' and 'description' for text chunks

    Args:

    docs (list) - list of LangChain documents

    Returns:

    docs (list) - list of LangChain documents with metadata fields populated

    """
    new_docs = []
    for doc in docs:

        # pdf name is last part of doc.metadata['source']
        pdf_name = doc.metadata["source"].split("/")[-1]

        # Find row in df where pdf_name is in URL
        row = df[df["Document"].str.contains(pdf_name)]
        page = doc.metadata["page"] + 1
        url = f"{row['Document'].values[0]}?id={str(uuid4())}#page={page}"

        # We'll use an LLM to generate a summary and title of the text, used by STORM
        # This is just for the demo, proper application would have better metadata
        summary = summarize_text(doc.page_content, prompt="Please describe this text:")
        title = summarize_text(
            doc.page_content, prompt="Please generate a 5 word title for this text:"
        )

        doc.metadata["description"] = summary
        doc.metadata["title"] = title
        doc.metadata["url"] = url
        doc.metadata["content"] = doc.page_content

        # print(json.dumps(doc.metadata, indent=2))
        new_docs.append(doc)

    print(f"There are {len(docs)} docs")

    return new_docs

docs = enrich_metadata(docs)
chunks = enrich_metadata(chunks)
```

# 构建向量数据库

STORM已经支持[Qdrant向量存储](https://www.google.com/url?sa=t&rct=j&opi=89978449&url=https%3A%2F%2Fqdrant.tech%2F&ved=2ahUKEwiHuK-_766JAxXutokEHbnUMhwQFnoECAgQAQ&usg=AOvVaw1SKthNlGkmNDis3BK1WPSq)。我喜欢在可能的情况下使用像LangChain和Llama Index这样的框架，这样可以在将来更容易地更换提供商，因此我选择使用LangChain构建一个[持久化到本地文件系统的本地Qdrant向量数据库](https://python.langchain.com/docs/integrations/vectorstores/qdrant/#local-mode)，而不是STORM的自动向量数据库管理。我认为这提供了更多的控制，并且对于那些已经有填充文档向量存储的管道的人来说更具可识别性。

```py
def build_vector_store(doc_type, docs):
    """
    Givena  list of LangChain docs, will embed and create a file-system Qdrant vector database.
    The folder includes doc_type in its name to avoid overwriting.

    Args:

    doc_type (str) - String to indicate level of document split, eg 'pages',
                     'chunks'. Used to name the database save folder
    docs (list) - List of langchain documents to embed and store in vector database

    Returns:

    Nothing returned by function, but db saved to f"{DB_DIR}_{doc_type}".

    """

    print(f"There are {len(docs)} docs")

    save_dir = f"{DB_DIR}_{doc_type}"

    print(f"Saving vectors to directory {save_dir}")

    client = QdrantClient(path=save_dir)

    client.create_collection(
        collection_name=DB_COLLECTION_NAME,
        vectors_config=VectorParams(size=num_vectors, distance=Distance.COSINE),
    )

    vector_store = QdrantVectorStore(
        client=client,
        collection_name=DB_COLLECTION_NAME,
        embedding=embeddings,
    )

    uuids = [str(uuid4()) for _ in range(len(docs))]

    vector_store.add_documents(documents=docs, ids=uuids)

build_vector_store("pages", docs)
build_vector_store("chunks", docs)
```

# 运行STORM

STORM的代码库有[一些很棒的示例](https://github.com/stanford-oval/storm/blob/main/examples/storm_examples/README.md)，包括不同的搜索引擎和LLM的使用，以及如何使用Qdrant向量存储。我决定结合这些示例中的各种功能，并添加一些额外的后处理，如下所示：

1.  增加了与OpenAI或Ollama一起运行的功能

1.  增加了支持传入向量数据库目录的功能

1.  增加了一个功能来解析参考文献元数据文件，将参考文献添加到生成的精炼文章中。STORM将这些参考文献生成在一个JSON文件中，但没有自动将它们添加到输出文章中。我不确定这是不是由于我错过了某个设置，但参考文献对于评估任何AI研究技术都至关重要，因此我增加了这个自定义后处理步骤。

1.  最后，我注意到开放模型在模板和角色设定方面提供了更多的指导，因为它们执行指令的准确度不如商业模型。我喜欢这些控制的透明性，并将其保留在OpenAI中，以便将来在工作中进行调整。

这里是所有内容（查看[repo notebook](https://github.com/dividor/storm-with-local-docs/blob/main/storm-local-docs.ipynb)以获取完整代码）…

```py
def set_instructions(runner):
    """
    Adjusts templates and personas for the STORM AI Research algorithm.

    Args:

    runner - STORM runner object

    Returns:

    runner - STORM runner object with extra prompting

    """

    # Open LMs are generally weaker in following output format.
    # One way for mitigation is to add one-shot example to the prompt to exemplify the desired output format.
    # For example, we can add the following examples to the two prompts used in StormPersonaGenerator.
    # Note that the example should be an object of dspy.Example with fields matching the InputField
    # and OutputField in the prompt (i.e., dspy.Signature).
    find_related_topic_example = Example(
        topic="Knowledge Curation",
        related_topics="https://en.wikipedia.org/wiki/Knowledge_management\n"
        "https://en.wikipedia.org/wiki/Information_science\n"
        "https://en.wikipedia.org/wiki/Library_science\n",
    )
    gen_persona_example = Example(
        topic="Knowledge Curation",
        examples="Title: Knowledge management\n"
        "Table of Contents: History\nResearch\n  Dimensions\n  Strategies\n  Motivations\nKM technologies"
        "\nKnowledge barriers\nKnowledge retention\nKnowledge audit\nKnowledge protection\n"
        "  Knowledge protection methods\n    Formal methods\n    Informal methods\n"
        "  Balancing knowledge protection and knowledge sharing\n  Knowledge protection risks",
        personas="1\. Historian of Knowledge Systems: This editor will focus on the history and evolution of knowledge curation. They will provide context on how knowledge curation has changed over time and its impact on modern practices.\n"
        "2\. Information Science Professional: With insights from 'Information science', this editor will explore the foundational theories, definitions, and philosophy that underpin knowledge curation\n"
        "3\. Digital Librarian: This editor will delve into the specifics of how digital libraries operate, including software, metadata, digital preservation.\n"
        "4\. Technical expert: This editor will focus on the technical aspects of knowledge curation, such as common features of content management systems.\n"
        "5\. Museum Curator: The museum curator will contribute expertise on the curation of physical items and the transition of these practices into the digital realm.",
    )
    runner.storm_knowledge_curation_module.persona_generator.create_writer_with_persona.find_related_topic.demos = [
        find_related_topic_example
    ]
    runner.storm_knowledge_curation_module.persona_generator.create_writer_with_persona.gen_persona.demos = [
        gen_persona_example
    ]

    # A trade-off of adding one-shot example is that it will increase the input length of the prompt. Also, some
    # examples may be very long (e.g., an example for writing a section based on the given information), which may
    # confuse the model. For these cases, you can create a pseudo-example that is short and easy to understand to steer
    # the model's output format.
    # For example, we can add the following pseudo-examples to the prompt used in WritePageOutlineFromConv and
    # ConvToSection.
    write_page_outline_example = Example(
        topic="Example Topic",
        conv="Wikipedia Writer: ...\nExpert: ...\nWikipedia Writer: ...\nExpert: ...",
        old_outline="# Section 1\n## Subsection 1\n## Subsection 2\n"
        "# Section 2\n## Subsection 1\n## Subsection 2\n"
        "# Section 3",
        outline="# New Section 1\n## New Subsection 1\n## New Subsection 2\n"
        "# New Section 2\n"
        "# New Section 3\n## New Subsection 1\n## New Subsection 2\n## New Subsection 3",
    )
    runner.storm_outline_generation_module.write_outline.write_page_outline.demos = [
        write_page_outline_example
    ]
    write_section_example = Example(
        info="[1]\nInformation in document 1\n[2]\nInformation in document 2\n[3]\nInformation in document 3",
        topic="Example Topic",
        section="Example Section",
        output="# Example Topic\n## Subsection 1\n"
        "This is an example sentence [1]. This is another example sentence [2][3].\n"
        "## Subsection 2\nThis is one more example sentence [1].",
    )
    runner.storm_article_generation.section_gen.write_section.demos = [
        write_section_example
    ]

    return runner

def latest_dir(parent_folder):
    """
    Find the most recent folder (by modified date) in the specified parent folder.

    Args:
        parent_folder (str): The path to the parent folder where the search for the most recent folder will be conducted. Defaults to f"{DATA_DIR}/storm_output".

    Returns:
        str: The path to the most recently modified folder within the parent folder.
    """
    # Find most recent folder (by modified date) in DATA_DIR/storm_data
    # TODO, find out how exactly storm passes back its output directory to avoid this hack
    folders = [f.path for f in os.scandir(parent_folder) if f.is_dir()]
    folder = max(folders, key=os.path.getmtime)

    return folder

def generate_footnotes(folder):
    """
    Generates footnotes from a JSON file containing URL information.

    Args:
        folder (str): The directory path where the 'url_to_info.json' file is located.

    Returns:
        str: A formatted string containing footnotes with URLs and their corresponding titles.
    """

    file = f"{folder}/url_to_info.json"

    with open(file) as f:
        data = json.load(f)

    refs = {}
    for rec in data["url_to_unified_index"]:
        val = data["url_to_unified_index"][rec]
        title = data["url_to_info"][rec]["title"].replace('"', "")
        refs[val] = f"- {val} [{title}]({rec})"

    keys = list(refs.keys())
    keys.sort()

    footer = ""
    for key in keys:
        footer += f"{refs[key]}\n"

    return footer, refs

def generate_markdown_article(output_dir):
    """
    Generates a markdown article by reading a text file, appending footnotes, 
    and saving the result as a markdown file.

    The function performs the following steps:
    1\. Retrieves the latest directory using the `latest_dir` function.
    2\. Generates footnotes for the article using the `generate_footnotes` function.
    3\. Reads the content of a text file named 'storm_gen_article_polished.txt' 
       located in the latest directory.
    4\. Appends the generated footnotes to the end of the article content.
    5\. Writes the modified content to a new markdown file named 
       STORM_OUTPUT_MARKDOWN_ARTICLE in the same directory.

    Args:

    output_dir (str) - The directory where the STORM output is stored.

    """

    folder = latest_dir(output_dir)
    footnotes, refs = generate_footnotes(folder)

    with open(f"{folder}/storm_gen_article_polished.txt") as f:
        text = f.read()

    # Update text references like [10] to link to URLs
    for ref in refs:
        print(f"Ref: {ref}, Ref_num: {refs[ref]}")
        url = refs[ref].split("(")[1].split(")")[0]
        text = text.replace(f"[{ref}]", f"\[[{ref}]({url})\]")

    text += f"\n\n## References\n\n{footnotes}"

    with open(f"{folder}/{STORM_OUTPUT_MARKDOWN_ARTICLE}", "w") as f:
        f.write(text)

def run_storm(topic, model_type, db_dir):
    """
    This function runs the STORM AI Research algorithm using data
    in a QDrant local database.

    Args:

    topic (str) - The research topic to generate the article for
    model_type (str) - One of 'openai' and 'ollama' to control LLM used
    db_dir (str) - Directory where the QDrant vector database is

    """
    if model_type not in ["openai", "ollama"]:
        print("Unsupported model_type")
        sys.exit()

    # Clear lock so can be read
    if os.path.exists(f"{db_dir}/.lock"):
        print(f"Removing lock file {db_dir}/.lock")
        os.remove(f"{db_dir}/.lock")

    print(f"Loading Qdrant vector store from {db_dir}")

    engine_lm_configs = STORMWikiLMConfigs()

    if model_type == "openai":

        print("Using OpenAI models")

        # Initialize the language model configurations
        openai_kwargs = {
            "api_key": os.getenv("OPENAI_API_KEY"),
            "temperature": 1.0,
            "top_p": 0.9,
        }

        ModelClass = (
            OpenAIModel
            if os.getenv("OPENAI_API_TYPE") == "openai"
            else AzureOpenAIModel
        )
        # If you are using Azure service, make sure the model name matches your own deployed model name.
        # The default name here is only used for demonstration and may not match your case.
        gpt_35_model_name = (
            "gpt-4o-mini"
            if os.getenv("OPENAI_API_TYPE") == "openai"
            else "gpt-35-turbo"
        )
        gpt_4_model_name = "gpt-4o"
        if os.getenv("OPENAI_API_TYPE") == "azure":
            openai_kwargs["api_base"] = os.getenv("AZURE_API_BASE")
            openai_kwargs["api_version"] = os.getenv("AZURE_API_VERSION")

        # STORM is a LM system so different components can be powered by different models.
        # For a good balance between cost and quality, you can choose a cheaper/faster model for conv_simulator_lm
        # which is used to split queries, synthesize answers in the conversation. We recommend using stronger models
        # for outline_gen_lm which is responsible for organizing the collected information, and article_gen_lm
        # which is responsible for generating sections with citations.
        conv_simulator_lm = ModelClass(
            model=gpt_35_model_name, max_tokens=10000, **openai_kwargs
        )
        question_asker_lm = ModelClass(
            model=gpt_35_model_name, max_tokens=10000, **openai_kwargs
        )
        outline_gen_lm = ModelClass(
            model=gpt_4_model_name, max_tokens=10000, **openai_kwargs
        )
        article_gen_lm = ModelClass(
            model=gpt_4_model_name, max_tokens=10000, **openai_kwargs
        )
        article_polish_lm = ModelClass(
            model=gpt_4_model_name, max_tokens=10000, **openai_kwargs
        )

    elif model_type == "ollama":

        print("Using Ollama models")

        ollama_kwargs = {
            # "model": "llama3.2:3b",
            "model": "llama3.1:latest",
            # "model": "qwen2.5:14b",
            "port": "11434",
            "url": "http://localhost",
            "stop": (
                "\n\n---",
            ),  # dspy uses "\n\n---" to separate examples. Open models sometimes generate this.
        }

        conv_simulator_lm = OllamaClient(max_tokens=500, **ollama_kwargs)
        question_asker_lm = OllamaClient(max_tokens=500, **ollama_kwargs)
        outline_gen_lm = OllamaClient(max_tokens=400, **ollama_kwargs)
        article_gen_lm = OllamaClient(max_tokens=700, **ollama_kwargs)
        article_polish_lm = OllamaClient(max_tokens=4000, **ollama_kwargs)

    engine_lm_configs.set_conv_simulator_lm(conv_simulator_lm)
    engine_lm_configs.set_question_asker_lm(question_asker_lm)
    engine_lm_configs.set_outline_gen_lm(outline_gen_lm)
    engine_lm_configs.set_article_gen_lm(article_gen_lm)
    engine_lm_configs.set_article_polish_lm(article_polish_lm)

    max_conv_turn = 4
    max_perspective = 3
    search_top_k = 10
    max_thread_num = 1
    device = "cpu"
    vector_db_mode = "offline"

    do_research = True
    do_generate_outline = True
    do_generate_article = True
    do_polish_article = True

    # Initialize the engine arguments
    output_dir=f"{STORM_OUTPUT_DIR}/{db_dir.split('db_')[1]}"
    print(f"Output directory: {output_dir}")

    engine_args = STORMWikiRunnerArguments(
        output_dir=output_dir,
        max_conv_turn=max_conv_turn,
        max_perspective=max_perspective,
        search_top_k=search_top_k,
        max_thread_num=max_thread_num,
    )

    # Setup VectorRM to retrieve information from your own data
    rm = VectorRM(
        collection_name=DB_COLLECTION_NAME,
        embedding_model=EMBEDDING_MODEL,
        device=device,
        k=search_top_k,
    )

    # initialize the vector store, either online (store the db on Qdrant server) or offline (store the db locally):
    if vector_db_mode == "offline":
        rm.init_offline_vector_db(vector_store_path=db_dir)

    # Initialize the STORM Wiki Runner
    runner = STORMWikiRunner(engine_args, engine_lm_configs, rm)

    # Set instructions for the STORM AI Research algorithm
    runner = set_instructions(runner)

    # run the pipeline
    runner.run(
        topic=topic,
        do_research=do_research,
        do_generate_outline=do_generate_outline,
        do_generate_article=do_generate_article,
        do_polish_article=do_polish_article,
    )
    runner.post_run()
    runner.summary()

    generate_markdown_article(output_dir)
```

我们准备好运行STORM了！

对于研究课题，我选择了一个典型的RAG系统难以回答的主题，且在PDF数据中没有很好覆盖的内容，这样我们可以看到归因效果如何……

“***比较不同类型灾害的财务影响及其对社区的影响***”

在两个数据库上运行此操作……

```py
query = "Compare the financial impact of different types of disasters and how those impact communities"

for doc_type in ["pages", "chunks"]:
    db_dir = f"{DB_DIR}_{doc_type}"
    run_storm(query=query, model_type="openai", db_dir=db_dir)
```

使用OpenAI时，这个过程在我的Macbook Pro M2（16GB内存）上大约花费了6分钟。我需要指出的是，其他一些简单查询，尤其是底层文档中有更多支持内容的查询，要快得多（有些情况下不到30秒）。

# STORM结果

STORM生成了一组输出文件……

![](../Images/9352963a2332997ba080d259b2f9151b.png)

由STORM生成的文件，其中一个Markdown文件结合了润色后的文章和参考脚注。

审视**conversation_log.json**和**llm_call_history.json**文件，查看以视角引导的对话组件，十分有趣。

对于我们的研究课题……

“***比较不同类型灾害的财务影响及其对社区的影响***”

你可以在这里找到生成的文章……

+   [STORM生成的文章 — 使用按页面拆分的文本](https://github.com/dividor/storm-with-local-docs/blob/main/data/storm_output/pages/Compare_the_financial_impact_of_different_types_of_disasters_and_how_those_impact_communities/storm_gen_article_polished.md)

+   [STORM生成的文章 — 使用递归文本拆分器进一步拆分的文本](https://github.com/dividor/storm-with-local-docs/blob/main/data/storm_output/chunks/Compare_the_financial_impact_of_different_types_of_disasters_and_how_those_impact_communities/storm_gen_article_polished.md)

**一些快速观察**

这个演示没有进行正式的评估 — 这可能比单跳RAG系统[更为复杂](https://arxiv.org/abs/2401.15391) — 但这里有一些可能有用的主观观察……

1.  按页面或较小块拆分的解析会生成合理的预读报告，人类可以用来研究与灾害财务影响相关的领域

1.  两种配对方法都在整个过程中提供了引用，但使用更小的文本块似乎产生了更少的引用。请参见上述两篇文章中的总结部分。更多的引用可以为分析提供更坚实的基础！

1.  按较小块拆分的解析有时会产生不相关的引用，这是STORM论文中提到的引用问题之一。请参见[总结部分](https://github.com/dividor/storm-with-local-docs/blob/main/data/storm_output/chunks/Compare_the_financial_impact_of_different_types_of_disasters_and_how_those_impact_communities/storm_gen_article_polished.md)中源‘10’的引用，该引用与参考句子不符。

1.  总体来说，正如我预期的那样，基于 Wiki 文章开发的算法，按 PDF 分割文本似乎能生成一篇[更具凝聚力和基础性的文章](https://github.com/dividor/storm-with-local-docs/blob/main/data/storm_output/pages/Compare_the_financial_impact_of_different_types_of_disasters_and_how_those_impact_communities/storm_gen_article_polished.md)（对我来说！）

尽管输入的研究主题在基础文档中并没有被深入探讨，但生成的报告为进一步的人类分析提供了一个很好的起点。

# 未来的工作

我们在本文中没有讨论[Co-Storm](https://www.arxiv.org/abs/2408.15232)，它将人类引入过程。这似乎是一个非常适合 AI 驱动研究的方向，我正在研究这个方向。

未来的工作还可以考虑将系统提示和人物角色调整到具体的商业案例中。目前，这些提示是为类似维基百科的过程设计的……

![](../Images/41fbcc46e187420f6725280d4e787d88.png)

STORM 系统提示，展示了如何强调创建维基百科风格的文章。[来源](https://arxiv.org/abs/2402.14207)

另一个可能的方向是将 STORM 的连接器扩展到 Qdrant 以外的系统，例如，支持其他向量存储，或者更好的是，支持 Langchain 和 Llama index 向量存储的通用支持。作者鼓励这一类的工作，涉及[这个文件](https://github.com/stanford-oval/storm/blob/main/knowledge_storm/rm.py)的 PR 可能是我未来的工作方向。

在没有互联网连接的情况下运行 STORM 将是一件令人兴奋的事情，因为它为现场的 AI 助手开辟了新的可能性。正如从演示代码中可以看到的，我增加了运行 STORM 的能力，使用 Ollama 本地托管的模型，但由于令牌吞吐率过低，LLM 代理讨论阶段没有完成，因此系统在我使用小型量化模型的笔记本电脑上没有完成任务。这或许是未来博客文章的一个话题！

最后，尽管[在线用户界面非常友好](https://storm.genie.stanford.edu)，但[随代码仓库附带的演示界面](https://github.com/stanford-oval/storm/tree/main/frontend/demo_light)非常基础，无法用于生产环境。也许斯坦福团队将发布更先进的界面——可能已经有了？——如果没有的话，仍然需要在这方面进行改进。

# 结论

这是一个简短的演示，旨在帮助人们开始使用 STORM 处理自己的文档。我没有进行系统的评估，如果在实际环境中使用 STORM，显然需要进行这项工作。尽管如此，我还是对 STORM 能够生成相对细致的研究主题和生成有良好引用的预写作研究内容感到印象深刻，这将帮助我进行自己的研究。

# 参考文献

[知识密集型 NLP 任务的检索增强生成](https://arxiv.org/abs/2005.11401)，Lewis 等人，2020年

[大规模语言模型的检索增强生成：综述](https://arxiv.org/html/2312.10997v5#S2)，Yunfan 等人，2024年

[从零开始利用大型语言模型协助撰写类似 Wikipedia 的文章](https://arxiv.org/abs/2402.14207)，Shao 等，2024

[进入未知的未知：通过参与语言模型代理对话进行积极的人类学习](https://www.arxiv.org/abs/2408.15232)，Jiang 等，2024

[MultiHop-RAG：多跳查询的检索增强生成基准测试](https://arxiv.org/abs/2401.15391)，Tang 等，2024

你可以在[这里](https://github.com/dividor/storm-with-local-docs)找到本文的代码

***如果喜欢这篇文章，请点赞，如果你能关注我，我将非常高兴！你可以在*** [***这里***](/@astrobagel) ***找到更多文章***，***或者在*** [***LinkedIn***](https://www.linkedin.com/in/matthew-harris-4018865/) ***上联系我。***
