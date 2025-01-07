# 如何与PDF文件对话而不使用专有模型：CLI + Streamlit + Ollama

> 原文：[https://towardsdatascience.com/how-to-talk-to-a-pdf-file-without-using-proprietary-models-cli-streamlit-ollama-6c22437ed932?source=collection_archive---------4-----------------------#2024-08-14](https://towardsdatascience.com/how-to-talk-to-a-pdf-file-without-using-proprietary-models-cli-streamlit-ollama-6c22437ed932?source=collection_archive---------4-----------------------#2024-08-14)

![](../Images/5bbf5cec507aee72cad3963558d8f2d7.png)

与PDF文件对话 *(GIF由作者提供)*

## 一项关于使用Streamlit和Meta AI的LLaMA模型创建本地执行的、免费的PDF聊天应用程序的贡献，没有API限制。

[](https://medium.com/@stefanpietrusky?source=post_page---byline--6c22437ed932--------------------------------)[![Stefan Pietrusky](../Images/f5abf75db277f3aec8d8e56877daafe4.png)](https://medium.com/@stefanpietrusky?source=post_page---byline--6c22437ed932--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6c22437ed932--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6c22437ed932--------------------------------) [Stefan Pietrusky](https://medium.com/@stefanpietrusky?source=post_page---byline--6c22437ed932--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6c22437ed932--------------------------------) ·阅读时间14分钟·2024年8月14日

--

我已经阅读了互联网上的各种文章，了解如何将开源框架Streamlit与机器学习结合使用，快速而轻松地创建有趣的交互式网页应用程序。这对于在没有广泛前端开发的情况下开发实验性应用程序非常有用。某篇文章展示了如何使用OpenAI语言模型创建一个对话链并执行它。创建了一个聊天模型实例*“gpt-3.5-turbo”*，定义了参数*“temperature”*，并设置其值为0，使模型以确定性的方式响应，最后实现了API密钥的占位符。后者在使用时需要进行身份验证。

```py
llm = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0, api_key="")
```

在评论中，我经常看到关于如何处理特定错误信息或如何解决该问题的问题。

*RateLimitError: 错误代码：429 — {‘error’: {‘message’: ‘您已超出当前配额，请检查您的计划和账单详情。有关此错误的更多信息，请阅读文档：* [*https://platform.openai.com/docs/guides/error-codes/api-errors.',*](https://platform.openai.com/docs/guides/error-codes/api-errors.',) *‘type’: ‘insufficient_quota’, ‘param’: None, ‘code’: ‘insufficient_quota’}}*

错误429表示发送到OpenAI API的请求超出了当前的使用配额。某个时间段内的可用API调用次数或订阅的一般使用限制已达到。这个错误很容易解决。你只需购买相应服务提供商的付费订阅，从而增加你的使用配额。这让我产生了一个想法：为什么不能简单地使用本地运行的开源模型，从而绕过这些限制，而无需支付任何费用。

在本文中，我将展示如何使用Streamlit创建一个应用程序，允许用户上传PDF文件，并根据文件内容提出问题，答案由集成的LLM生成。使用该应用时没有任何限制或费用。响应时间（输入-输出）可能会稍微延长，具体取决于系统，但仍然在合理的范围内。首先，我们来处理我们将使用的LLM。

## 一个为**美洲驼**而设的王国

我们将使用Meta AI的开源语言模型Llama。作为大型语言模型领域最近发展的一个部分，它将被应用在应用程序中，以理解和生成自然语言（NLP）。为了在本地使用LLM，我们首先需要在系统上安装Ollama。为此，我们访问以下[**官方网站**](https://ollama.com/)，下载开源平台。安装后，系统可能需要重启。

![](../Images/b2e80402088c8e5141c11a64a52bea18.png)

下载Ollama ([Public Domain](https://ollama.com/))

安装完Ollama后，我们点击*“Models”*，并在打开的概览中选择*“llama3.1”*模型。Llama基于Transformer架构，已在大规模且多样化的数据集上进行训练，提供不同大小的版本，并由于其开放性和可访问性，特别适合开发实际应用程序。在本文中，使用了最小的版本*“8B”*，以确保应用程序在性能较低的系统上也能运行。一旦选择了正确的模型，复制显示的命令并在终端中执行。

![](../Images/6bbb1a1ecbe418de4246df8470fddfcd.png)

在Ollama平台上的LLama3.1概述 ([Public Domain](https://ollama.com/library/llama3.1))

```py
ollama run llama3.1
```

一旦模型下载完成，你可以通过终端与其进行交互。接下来，我们进入应用程序的设置部分。简而言之，流程如下：PDF文件被上传并提取其中的文本。提取的文本被划分为更小的块，并存储在向量存储中。用户输入问题。问题，即输入，结合问题和上下文为模型准备。查询LLM并生成答案。

![](../Images/f6c3884b08f96c6d4738e89ed1795425.png)

应用程序流程 (*图片来自作者*)

## PDF聊天应用程序 [所需库]

该应用程序需要各种库才能正常运行，以下是简要说明。Python 中通过*“subprocess”*执行系统命令并与之通信。我们需要*“streamlit”*来创建 Web 应用。*“PyPDF2”* 用于读取 PDF 文档。文本的拆分由*“langchain.text_splitter.RecursiveCharacterTextSplitter”*完成。*“langchain_community.embeddings.SpacyEmbeddings”*库用于使用 Spacy 模型生成文本嵌入。向量存储*“langchain_community.vectorstores.FAISS”*使得高效保存和检索嵌入成为可能。聊天交互的提示模板定义使用了*“langchain_core.prompts.ChatPromptTemplate”*。通过*“os”* 获取操作系统功能，*“re”* 用于识别字符字符串中的模式。系统上还需要安装 Python。根据操作系统的不同，所需的执行文件可以从[**官方网站**](https://www.python.org/downloads/)下载。安装完成后，可以通过终端使用以下命令检查安装是否成功。

```py
python --version
```

所需的库可以通过终端使用以下命令安装：

```py
pip install streamlit PyPDF2 langchain langchain-community spacy faiss-cpu
```

*“subprocess”、“os”和“re”* 是 Python 的内置库，无需单独安装。然而，Spacy 语言模型必须通过以下命令单独下载。

```py
python -m spacy download en_core_web_sm
```

应用的依赖列表如下所示。

```py
import subprocess
import streamlit as st
from PyPDF2 import PdfReader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.embeddings.spacy_embeddings import SpacyEmbeddings
from langchain_community.vectorstores import FAISS
from langchain.tools.retriever import create_retriever_tool
from langchain_core.prompts import ChatPromptTemplate
import os
import re
import psutil
```

现在，所有所需的内容已经到位，让我们继续进行应用的设置。下面将描述脚本的各个组件。

## PDF CHAT APP [环境配置]

为了避免在加载库时，特别是在并行处理时出现问题，需要将环境变量 *“KMP_DUPLICATE_LIB_OK”* 设置为 *“TRUE”*。在本文的上下文中，这一配置是由于使用了 FAISS [Facebook AI 相似度搜索]，它在数据集搜索时使用了并行计算操作。

```py
os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"
```

## PDF CHAT APP [PDF 阅读功能]

*“pdf_read()”* 函数从 PDF 文件中读取整个文本。具体来说，*“PyPDF2”* 用于提取文本。然后，文本被合并成一个单一的字符字符串*“text”*，并返回该字符串。该函数对于使 PDF 文件的内容可用于进一步处理步骤非常重要。

```py
def pdf_read(pdf_doc):
    """Read the text from PDF document."""
    text = ""
    for pdf in pdf_doc:
        pdf_reader = PdfReader(pdf)
        for page in pdf_reader.pages:
            page_text = page.extract_text()
            if page_text:
                text += page_text
    return text
```

原则上，可以同时上传多个 PDF 文件，这些文件一起形成上下文。如果要分析单个文件，应一次只上传一个文件，然后删除该文件并上传新文件。上传多个文件，并将它们视为独立的上下文，已在应用的定制版本中实现。

## PDF CHAT APP [文本块功能]

上一个函数中合成的字符字符串在下一步中通过 *“create_text_chunks()”* 函数被拆分成更小的文本块。每个文本块的最大字符数 *“chunk_size”* 为 1000，临近文本块之间可以重叠的字符数 *“chunk_overlap”* 为 200。这个实现使得应用能够更高效地查询和处理更大数量的文本。防止超出模型的输入大小。通过拆分，搜索得到了优化，因为较小、具上下文的部分（粒度）可以更准确地查询（更详细的向量），从而整体提高了信息的提供。模型的准确性和处理速度也得到了提高。

```py
def create_text_chunks(text, chunk_size=1000, chunk_overlap=200):
    """Create text chunks from a large text block."""
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=chunk_size,
        chunk_overlap=chunk_overlap
    )
    text_chunks = text_splitter.split_text(text)
    return text_chunks
```

## PDF 聊天应用 [文本嵌入]

为了捕捉上传为 PDF 文件的文本的含义，必须创建一个用于文本嵌入的对象，该对象是使用 Spacy 模型的文本的数值表示。然后，这些嵌入被用来对文本进行向量化，从而将其存储在向量存储中，以便可以用于语义搜索。

```py
embeddings = SpacyEmbeddings(model_name="en_core_web_sm")
```

## PDF 聊天应用 [向量存储功能]

*“vector_store()”* 函数使用前面提到的 FAISS 来存储文本块的嵌入。向量存储能够基于现有的嵌入加速文本的检索和搜索。向量存储被保存在本地，以便以后可以访问。

![](../Images/fa02e6bddbeb2ed4097a99f593e4d89c.png)

从 PDF 到向量存储 *(图片由作者提供)*

```py
def vector_store(text_chunks):
    """Create a vector store for the text chunks."""
    vector_store = FAISS.from_texts(text_chunks, embedding=embeddings)
    vector_store.save_local("faiss_db")
```

向量通过将句子和单词转换为数学上可解释的空间来捕捉文本的含义和上下文。文本“*今天天气很好*”被转换成一个示例向量 [0.25, -0.47, 0.19, …]。这使得进行相似性搜索变得更加容易。

## PDF 聊天应用 [基于 CLI 的 LLaMA 请求]

*“query_llama_via_cli()”* 函数使得可以通过命令行与外部 LLaMA 模型进程进行通信。输入数据被发送，响应被接收、处理，并处理任何发生的错误 errors=’ignore’。这个功能允许 LLM 在应用程序工作流中实现，尽管它运行在通过 CLI（命令行界面）控制的独立环境中。CLI 作为命令行界面的优点是它们是平台独立的，这使得应用几乎可以在任何操作系统上运行。

```py
def query_llama_via_cli(input_text):
    """Query the Llama model via the CLI."""
    try:
        # Start the interactive process
        process = subprocess.Popen(
            ["ollama", "run", "llama3.1"],
            stdin=subprocess.PIPE,
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True,  # Ensure that communication takes place as text (UTF-8)
            encoding='utf-8',  # Set UTF-8 encoding explicitly
            errors='ignore',  # Ignore incorrect characters
            bufsize=1
        )

        # Send the input to the process
        stdout, stderr = process.communicate(input=f"{input_text}\n", timeout=30)

        # Check error output
        if process.returncode != 0:
            return f"Error in the model request: {stderr.strip()}"

        # Filter response and remove control characters
        response = re.sub(r'\x1b\[.*?m', '', stdout)  # Remove ANSI codes

        # Extract the relevant answer
        return extract_relevant_answer(response)

    except subprocess.TimeoutExpired:
        process.kill()
        return "Timeout for the model request"
    except Exception as e:
        return f"An unexpected error has occurred: {str(e)}"
```

以下是该功能的更详细解释。该过程由 *“subprocess.Popen()”* 启动。启动 LLM 的命令是 [“ollama”, “run”, “llama3.1”]。通过参数 *“stdin”*、*“stdout”* 和 *“stderr”*，可以访问进程的输入和输出流（发送数据和接收结果）。通信以 UTF-8 编码文本 *encoding=’utf-8'* 进行。为了提高交互性，I/O 操作的缓冲区大小设置为行缓冲 *“bufsize=1”*。

输入*“input_text”*被传递给进程，特别是LLM，在此生成响应*“stdout”*。最大等待时间（秒）直到进程必须返回响应是30秒“timeout=30”。如果超过该时间，超时错误将被触发*“stderr”*。返回码检查进程是否成功*“returncode == 0”*。如果不是，则返回错误信息。应用不会花费太长时间来返回响应。最后，响应“stdout”被处理。去除不需要的字符，并从输出中删除ANSI颜色和格式代码*“response = re.sub(r’\x1b\[.*?m’, ‘’, stdout)”*。为了从完整的模块响应中提取和格式化相关的响应，调用*“extract_relevant_answer”*。如果超时超过30秒，进程将通过*“process.kill()”*终止。通信过程中发生的错误会被拦截并作为通用错误信息返回。

## PDF聊天应用[提取相关答案]

通过*“extract_relevant_answer()”*函数从整个模型响应中提取相关响应。与此同时，该函数还会去除一些简单的格式问题，特别是去掉形成响应的复合字符串两端的空格*“strip()”*。根据应用的具体需求，该函数可以扩展以返回特定的关键词或句子（标记）。也可以集成额外的规则用于清理和格式化。

```py
def extract_relevant_answer(full_response):
    """Extract the relevant response from the full model response."""
    response_lines = full_response.splitlines()

    # Search for the relevant answer; if there is a marker, it can be used here
    if response_lines:
        # Assume that the answer comes as a complete return to be filtered
        return "\n".join(response_lines).strip()

    return "No answer received"
```

## PDF聊天应用[对话链]

对话链由函数*“get_conversational_chain()”*创建。该函数通过将特定的提示和上下文与用户的问题结合起来，为LLM准备输入。为了提供最佳答案，模型应该接收到清晰且结构化的输入。一个多级提示模式（系统消息、人类消息*“{input}”*和占位符）由*“ChatPromptTemplate.from_message()”*定义。模型的角色由系统消息“system”定义。人类消息包含用户的问题。提示（模型的行为）、上下文（PDF文件的内容）和问题（应用的用户）被结合到*“input_text”*中。准备好的输入通过CLI使用函数*“query_llama_via_cli(input_text)”*发送给LLM。输出作为*“response”*保存，并通过*“st.write(“PDF: “, response)”*在Streamlit应用中显示。

```py
def get_conversational_chain(context, ques):
    """Create the input for the model based on the prompt and context."""
    # Define the prompt behavior
    prompt = ChatPromptTemplate.from_messages(
        [
            (
                "system",
                """You are an intelligent and helpful assistant. Your goal is to provide the most accurate and detailed answers 
                possible to any question you receive. Use all available context to enhance your answers, and explain complex 
                concepts in a simple manner. If additional information might help, suggest further areas for exploration. If the 
                answer is not available in the provided context, state this clearly and offer related insights when possible.""",
            ),
            ("human", "{input}"),
            ("placeholder", "{agent_scratchpad}"),
        ]
    )

    # Combine the context and the question
    input_text = f"Prompt: {prompt.format(input=ques)}\nContext: {context}\nQuestion: {ques}"

    # Request to the model
    response = query_llama_via_cli(input_text)
    st.write("PDF: ", response)  # The answer is displayed here
```

## PDF聊天应用[用户输入处理]

用户输入通过*“user_input()”*函数处理并转发给LLM。具体来说，上传的PDF文件的整个文本作为上下文使用。调用对话链函数*“get_conversational_chain”*，最终使用上下文*“context”*回答用户的问题*“user_question”*。换句话说，用户和模型之间的交互由此函数启用。

```py
def user_input(user_question, pdf_text):
    """Processes the user input and calls up the model."""
    # Use the entire text of the PDF as context
    context = pdf_text

    # Configure and request
    get_conversational_chain(context, user_question)
```

## PDF聊天应用[主脚本]

Streamlit web 应用的主要逻辑由 *“main()”* 函数定义。具体来说，Streamlit 页面被设置并定义了布局。目前，布局仅包括上传 PDF 文件的选项 *“st.file_uploader”* 和一个输入框 *“st.text_input”*。用户的问题会在后者中输入。用户界面允许与模型进行交互。如果上传了 PDF 文件，它会被读取 *“pdf_text = pdf_read(pdf_doc)”*。如果还有问题并且输入已确认，则处理请求 *“user_input(user_question, pdf_text)”*。

```py
def main():
    """Main function of the Streamlit application."""
    st.set_page_config(page_title="CHAT WITH YOUR PDF")
    st.header("PDF CHAT APP")

    pdf_text = ""
    pdf_doc = st.file_uploader("Upload your PDF Files and confirm your question", accept_multiple_files=True)

    if pdf_doc:
        pdf_text = pdf_read(pdf_doc)  # Read the entire PDF text

    user_question = st.text_input("Ask a Question from the PDF Files")

    if user_question and pdf_text:
        user_input(user_question, pdf_text)

    # Monitor RAM consumption
    process = psutil.Process(os.getpid())
    memory_usage = process.memory_info().rss / (1024 ** 2)  # Conversion to megabytes
    st.sidebar.write(f"Memory usage: {memory_usage:.2f} MB")

if __name__ == "__main__":
    main()
```

描述的应用通过以下命令启动，界面如下。

```py
streamlit run pca1.py
```

![](../Images/f88464f892b955054d9be41ba9dffbd8.png)

Streamlit PDF 聊天应用 1 *图片来源：作者*

## 个性化定制选项

各个领域都有个性化定制选项。为了提高 LLM（大语言模型）响应的质量和相关性，可以调整系统提示或系统消息，见“对话链”。通过特定的指令和上下文可以直接控制模型的行为。你可以尝试不同的提示，以测试模型响应如何变化。当前的系统消息仍然提供了很多定制的潜力。

```py
 prompt = ChatPromptTemplate.from_messages(
        [
            (
                "system",
                """You are an intelligent and helpful assistant. Your goal is to provide the most accurate and detailed answers 
                possible to any question you receive. Use all available context to enhance your answers, and explain complex 
                concepts in a simple manner. If additional information might help, suggest further areas for exploration. If the 
                answer is not available in the provided context, state this clearly and offer related insights when possible.""",
            ),
```

另一个定制选项是替换当前使用的 LLM Llama3.1。OLLAMA 上提供了各种不同大小（例如 2B、8B、70B 等）的模型（如 gemma2、mistral、phi3、qwen2 等）。为了使用不同的模型，必须先下载该模型，然后在 Python 脚本中调整 *“query_llama_via_cli()”* 函数。具体来说，需要更改启动 LLM 的命令 *[“ollama”, “run”, “llama3.1”]*。

![](../Images/2f06154b207a96a2797fd38a6ce31ed9.png)

一些 Ollama 模型 *(图片来源：作者)*

使用其他模型时，必须确保可用的计算能力足以支持本地使用。你还需要考虑模型是在 GPU 还是 CPU 上执行。以下示例可以作为评估模型是否能在你自己的计算机上运行的经验法则：一个具有 10 亿参数（1B）的模型大约需要 2 到 3 GB 的内存（每个参数约 2–3 字节）。可以使用任务管理器（Windows）来检查应用程序执行对系统性能的影响。

![](../Images/409ebb2896dcb3a0f74598467322cb3a.png)

Windows 任务管理器 (*图片来源：作者)*

或者，你也可以直接将应用的内存使用情况集成到 Streamlit 中。这是通过使用 *“psutil”* 库实现的，该库必须先安装，然后在 Python 脚本中实现。

```py
 # Monitor RAM consumption
    process = psutil.Process(os.getpid())
    memory_usage = process.memory_info().rss / (1024 ** 2)  # Conversion to megabytes
    st.sidebar.write(f"Memory usage: {memory_usage:.2f} MB")
```

还可以自定义应用程序的布局和功能。例如，主要逻辑，特别是 *“main()”* 函数，可以扩展以允许同时上传多个 PDF 文件 *“accept_multiple_files=True”*。然后，文件会以列表形式显示，用户可以选择 *“selected_pdf_file”*。接着，处理照常进行。根据所选文件，提取的内容将与用户的问题一起转发给 LLM。定制的代码可以在 *“pca2.py”* 文件中找到。  

![](../Images/b7d2661ffafe3abed3580dd73f52935c.png)  

Streamlit PDF 聊天应用 2 *(图片由作者提供)*  

## PCA PYTHON 脚本 [下载]  

点击文件夹下载包含两个应用程序的压缩文件！  

[![](../Images/495251495ae1862c340639d9f89f25d4.png)](https://drive.google.com/file/d/15x3i1ov7GkOGWbml3LY_Os-DVR8kN98C/view?usp=sharing)  

点击文件夹加载 Python 脚本 (*图片由作者提供*)  

## 结论  

本文展示了如何将 Python 与 Streamlit、FAISS、Spacy、CLI、OLLAMA 以及 LLM Llama3.1 等工具结合使用，创建一个 Web 应用程序，允许用户从 PDF 文件中提取文本，保存为嵌入格式，并通过 AI 模型提问文件内容。通过进一步优化脚本（例如提示语）、使用其他模型并调整布局以加入附加功能，该应用程序可以在日常生活中提供额外价值，同时无需增加额外成本。享受定制和使用该应用程序的乐趣。  

![](../Images/6986252b10d7723e67c0529d265a19a5.png)  

你最多可以点击 50 次！  

![](../Images/127ee9bba2f9b657ec9f4cb2b3c1f6ed.png)  

仅用于缩略图（图片由作者提供）
