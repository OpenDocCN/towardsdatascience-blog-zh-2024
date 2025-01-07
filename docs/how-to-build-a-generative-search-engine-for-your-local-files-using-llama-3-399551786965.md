# 如何使用Llama 3构建本地文件的生成式搜索引擎

> 原文：[https://towardsdatascience.com/how-to-build-a-generative-search-engine-for-your-local-files-using-llama-3-399551786965?source=collection_archive---------0-----------------------#2024-06-08](https://towardsdatascience.com/how-to-build-a-generative-search-engine-for-your-local-files-using-llama-3-399551786965?source=collection_archive---------0-----------------------#2024-06-08)

## 使用Qdrant、NVIDIA NIM API或Llama 3 8B在本地构建您的本地GenAI助手

[](https://datawarrior.medium.com/?source=post_page---byline--399551786965--------------------------------)[![Nikola Milosevic (Data Warrior)](../Images/ebea6501c00030561a59a4a12ab7a79a.png)](https://datawarrior.medium.com/?source=post_page---byline--399551786965--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--399551786965--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--399551786965--------------------------------) [Nikola Milosevic (Data Warrior)](https://datawarrior.medium.com/?source=post_page---byline--399551786965--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--399551786965--------------------------------) ·阅读时间：12分钟·2024年6月8日

--

5月23日，我收到了一封来自Nvidia的邮件，邀请我参加[NVIDIA和LangChain的生成式AI代理开发者大赛](https://www.nvidia.com/en-us/ai-data-science/generative-ai/developer-contest-with-langchain/?ncid=em-anno-571922&DkwibgoBGQo58Ndn2XA_QZk4Oek6oAyhMHrbuo7f2iF8fmTr0phnYJSKrENRDiGu3MOeEry08HydZtz_EiC0eg=&mkt_tok=MTU2LU9GTi03NDIAAAGTQxOVSz583h1Gr6TvRfnNz4TJLyss1TypLIotdWccXzlkMpJ8mAtsKiyWooZ5pnhPM3ALyJdxJh6gpak9YASo8xEIOWv-5FZaaptj4FmiBLsaCVMdI5w)。我第一反应是时间太紧，考虑到我们最近有了孩子，而且我的父母也正好要来，我应该没时间参与。但接着我又有了第二个想法，我决定可以编写一些代码并提交。我思考了几天应该做什么，最终有一个想法深深打动了我——一个开源生成式搜索引擎，允许你与本地文件进行交互。微软Copilot已经提供了类似的功能，但我想我可以做一个开源版本，出于兴趣，并分享我在快速编码系统过程中收获的一些经验。

# 系统设计

为了构建一个本地生成式搜索引擎或助手，我们需要几个组件：

+   一个包含本地文件内容的索引，以及一个信息检索引擎，用于根据给定的查询/问题检索最相关的文档。

+   一个语言模型，用于从本地文档中选择内容并生成总结性回答

+   一个用户界面

下面的图表展示了各个组件如何交互。

![](../Images/93ac4331f5c89da60084b967ac949115.png)

系统设计与架构。Qdrant用于向量存储，而Streamlit用于用户界面。Llama 3通过Nvidia NIM API（70B版本）或通过HuggingFace下载（8B版本）使用。文档切块使用Langchain完成。图像由作者提供。

首先，我们需要将本地文件编入可以查询本地文件内容的索引。然后，当用户提问时，我们将使用创建的索引，以及一些非对称的段落或文档嵌入来检索可能包含答案的最相关文档。这些文档的内容和问题将传递给部署的大型语言模型，模型将使用这些文档内容生成答案。在指令提示中，我们会要求大型语言模型还返回使用文档的参考资料。最终，所有内容将通过用户界面展示给用户。

现在，让我们更详细地看看每个组件。

# 语义索引

我们正在构建一个语义索引，它将根据文件内容的相似性和给定查询提供最相关的文档。为了创建这样的索引，我们将使用Qdrant作为向量存储。值得注意的是，[Qdrant客户端库](https://github.com/qdrant/qdrant-client)不需要完整安装[Qdrant服务器](https://qdrant.tech/)，并且可以进行文档相似性匹配，只要文档能够适应工作内存（RAM）。因此，我们所需要做的就是通过pip安装Qdrant客户端。

我们可以通过以下方式初始化Qdrant（请注意，hf参数稍后根据故事流程定义，但在使用Qdrant客户端时，您已经需要定义所使用的向量化方法和度量标准）：

```py
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams
client = QdrantClient(path="qdrant/")
collection_name = "MyCollection"
if client.collection_exists(collection_name):
    client.delete_collection(collection_name)

client.create_collection(collection_name,vectors_config=VectorParams(size=768, distance=Distance.DOT))
qdrant = Qdrant(client, collection_name, hf)
```

为了创建一个向量索引，我们需要将文档嵌入硬盘中。对于嵌入，我们需要选择合适的嵌入方法和向量比较度量。有几种段落、句子或单词的嵌入方法可以使用，结果各不相同。基于文档创建向量搜索的主要问题是非对称搜索问题。非对称搜索问题在信息检索中很常见，当查询较短而文档较长时就会发生。单词或句子嵌入通常会进行微调，以根据相似大小的文档（句子或段落）提供相似度评分。一旦情况发生变化，正确的信息检索可能会失败。

然而，我们可以找到一种适用于非对称搜索问题的嵌入方法。例如，在MSMARCO数据集上微调的模型通常效果不错。MSMARCO数据集基于Bing搜索查询和文档，由微软发布。因此，它非常适合我们正在处理的问题。

对于这个特定的实现，我选择了一个已经微调过的模型，名为：

```py
sentence-transformers/msmarco-bert-base-dot-v5
```

该模型基于BERT，并使用点积作为相似度度量进行了微调。我们已经初始化了qdrant客户端，以便在线使用点积作为相似度度量（请注意该模型的维度为768）：

```py
client.create_collection(collection_name,vectors_config=VectorParams(size=768, distance=Distance.DOT))
```

我们可以使用其他度量标准，如余弦相似度，然而，鉴于该模型是使用点积微调的，我们使用该度量标准能获得最佳性能。此外，从几何角度来看：余弦相似度仅关注角度差异，而点积则同时考虑了角度和幅度。通过将数据归一化为具有相同幅度，两个度量标准变得等效。在忽略幅度有利的情况下，余弦相似度是有用的。然而，如果幅度重要，点积是更合适的相似度度量。

初始化MSMarco模型的代码如下（如果您有可用的GPU，请使用它。无论如何）：

```py
 model_name = "sentence-transformers/msmarco-bert-base-dot-v5"
    model_kwargs = {'device': 'cpu'}
    encode_kwargs = {'normalize_embeddings': True}
    hf = HuggingFaceEmbeddings(
        model_name=model_name,
        model_kwargs=model_kwargs,
        encode_kwargs=encode_kwargs
    )
```

下一个问题是：我们需要处理的是BERT类模型的上下文大小受限，由于变换器模型的二次内存需求。在许多BERT类模型中，这个上下文大小被设置为512个标记。有两个选项：（1）我们可以仅根据前512个标记生成答案，忽略文档的其余部分，或（2）创建一个索引，其中一个文档会被拆分成多个块，并作为块存储在索引中。在第一种情况下，我们将失去大量重要信息，因此我们选择了第二种方案。为了拆分文档，我们可以使用LangChain中的预构建拆分器：

```py
from langchain_text_splitters import TokenTextSplitter
text_splitter = TokenTextSplitter(chunk_size=500, chunk_overlap=50)
texts = text_splitter.split_text(file_content)
metadata = []
for i in range(0,len(texts)):
    metadata.append({"path":file})
qdrant.add_texts(texts,metadatas=metadata)
```

在提供的代码部分中，我们将文本拆分为500个标记的块，每个块有50个重叠的标记。这样，我们在块的结束或开始处保留了一些上下文。在代码的其余部分，我们创建了带有文档路径的元数据，并将这些块和元数据添加到索引中。

然而，在我们将文件内容添加到索引之前，需要先读取它。甚至在读取文件之前，我们需要获取所有需要索引的文件。为了简化，在此项目中，用户可以定义他/她希望索引的文件夹。索引器将递归地从该文件夹及其子文件夹中检索所有文件，并索引支持的文件（我们将探讨如何支持PDF、Word、PPT和TXT文件）。

我们可以以递归方式检索给定文件夹及其子文件夹中的所有文件：

```py
def get_files(dir):
    file_list = []
    for f in listdir(dir):
        if isfile(join(dir,f)):
            file_list.append(join(dir,f))
        elif isdir(join(dir,f)):
            file_list= file_list + get_files(join(dir,f))
    return file_list
```

一旦所有文件都被检索到列表中，我们就可以读取包含文本的文件内容。在这个工具中，初始支持MS Word文档（扩展名为“.docx”）、PDF文档、MS PowerPoint演示文稿（扩展名为“.pptx”）和纯文本文件（扩展名为“.txt”）。

为了读取MS Word文档，我们可以使用docx-python库。将文档读取为字符串变量的函数大致如下所示：

```py
import docx
def getTextFromWord(filename):
    doc = docx.Document(filename)
    fullText = []
    for para in doc.paragraphs:
        fullText.append(para.text)
    return '\n'.join(fullText)
```

对于MS PowerPoint文件，也可以做类似的操作。为此，我们需要下载并安装pptx-python库，并编写一个类似这样的函数：

```py
from pptx import Presentation
def getTextFromPPTX(filename):
    prs = Presentation(filename)
    fullText = []
    for slide in prs.slides:
        for shape in slide.shapes:
            fullText.append(shape.text)
    return '\n'.join(fullText)
```

阅读文本文件非常简单：

```py
f = open(file,'r')
file_content = f.read()
f.close()
```

对于PDF文件，在这种情况下我们将使用PyPDF2库：

```py
reader = PyPDF2.PdfReader(file)
for i in range(0,len(reader.pages)):
    file_content = file_content + " "+reader.pages[i].extract_text()
```

最后，整个索引函数大致如下所示：

```py
file_content = ""
    for file in onlyfiles:
        file_content = ""
        if file.endswith(".pdf"):
            print("indexing "+file)
            reader = PyPDF2.PdfReader(file)
            for i in range(0,len(reader.pages)):
                file_content = file_content + " "+reader.pages[i].extract_text()
        elif file.endswith(".txt"):
            print("indexing " + file)
            f = open(file,'r')
            file_content = f.read()
            f.close()
        elif file.endswith(".docx"):
            print("indexing " + file)
            file_content = getTextFromWord(file)
        elif file.endswith(".pptx"):
            print("indexing " + file)
            file_content = getTextFromPPTX(file)
        else:
            continue
        text_splitter = TokenTextSplitter(chunk_size=500, chunk_overlap=50)
        texts = text_splitter.split_text(file_content)
        metadata = []
        for i in range(0,len(texts)):
            metadata.append({"path":file})
        qdrant.add_texts(texts,metadatas=metadata)
    print(onlyfiles)
    print("Finished indexing!")
```

正如我们所说，我们使用LangChain中的TokenTextSplitter将文本分割成每个包含500个令牌并有50个令牌重叠的片段。现在，当我们创建了索引后，可以创建一个Web服务来查询索引并生成答案。

# 生成式搜索API。

我们将使用FastAPI创建一个Web服务来托管我们的生成式搜索引擎。该API将访问我们在上一节中创建的索引数据，使用向量相似度度量进行搜索，使用最相关的片段与Llama 3模型生成答案，并最终将答案返回给用户。

为了初始化和导入生成式搜索组件的库，我们可以使用以下代码：

```py
from fastapi import FastAPI
from langchain_community.embeddings import HuggingFaceEmbeddings
from langchain_qdrant import Qdrant
from qdrant_client import QdrantClient
from pydantic import BaseModel
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM
import environment_var
import os
from openai import OpenAI

class Item(BaseModel):
    query: str
    def __init__(self, query: str) -> None:
        super().__init__(query=query)
```

如前所述，我们使用FastAPI来创建API接口。我们将利用qdrant_client库访问我们创建的索引数据，并使用langchain_qdrant库提供额外支持。对于嵌入和本地加载Llama 3模型，我们将使用PyTorch和Transformers库。此外，我们还将使用OpenAI库调用NVIDIA NIM API，API密钥存储在我们创建的environment_var（包括Nvidia和HuggingFace）文件中。

我们创建了一个Item类，继承自Pydantic中的BaseModel，用于作为请求函数的参数传递。它将包含一个字段，名为query。

现在，我们可以开始初始化我们的机器学习模型。

```py
model_name = "sentence-transformers/msmarco-bert-base-dot-v5"
model_kwargs = {'device': 'cpu'}
encode_kwargs = {'normalize_embeddings': True}
hf = HuggingFaceEmbeddings(
    model_name=model_name,
    model_kwargs=model_kwargs,
    encode_kwargs=encode_kwargs
)

os.environ["HF_TOKEN"] = environment_var.hf_token
use_nvidia_api = False
use_quantized = True
if environment_var.nvidia_key !="":
    client_ai = OpenAI(
        base_url="https://integrate.api.nvidia.com/v1",
        api_key=environment_var.nvidia_key
    )
    use_nvidia_api = True
elif use_quantized:
    model_id = "Kameshr/LLAMA-3-Quantized"
    tokenizer = AutoTokenizer.from_pretrained(model_id)
    model = AutoModelForCausalLM.from_pretrained(
        model_id,
        torch_dtype=torch.float16,
        device_map="auto",
    )
else:
    model_id = "meta-llama/Meta-Llama-3-8B-Instruct"
    tokenizer = AutoTokenizer.from_pretrained(model_id)
    model = AutoModelForCausalLM.from_pretrained(
        model_id,
        torch_dtype=torch.float16,
        device_map="auto",
    )
```

在前几行代码中，我们加载了针对MSMARCO数据进行微调的基于BERT的模型权重，这个模型也用于索引我们的文档。

然后，我们检查是否提供了nvidia_key，如果提供了，我们使用OpenAI库调用NVIDIA NIM API。当我们使用NVIDIA NIM API时，可以使用一个大版本的Llama 3指令模型，拥有70B参数。如果未提供nvidia_key，我们将本地加载Llama 3。然而，在本地环境下，至少对于大多数消费电子产品，无法加载70B参数模型。因此，我们将加载Llama 3 8B参数模型或已量化的Llama 3 8B参数模型。通过量化，我们节省了空间，并使模型能够在较少的RAM上运行。例如，Llama 3 8B通常需要大约14GB的GPU RAM，而量化后的Llama 3 8B则可以在6GB的GPU RAM上运行。因此，我们将根据参数加载完整模型或量化模型。

现在，我们可以初始化Qdrant客户端。

```py
client = QdrantClient(path="qdrant/")
collection_name = "MyCollection"
qdrant = Qdrant(client, collection_name, hf)
```

同时，使用FastAPI并创建第一个模拟GET函数。

```py
app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

这个函数将返回JSON格式的数据 {“message”:”Hello World”}

然而，为了使这个API能够正常工作，我们将创建两个功能，一个仅执行语义搜索，另一个则执行搜索并将前10个片段作为上下文生成答案，并引用它使用的文档。

```py
@app.post("/search")
def search(Item:Item):
    query = Item.query
    search_result = qdrant.similarity_search(
        query=query, k=10
    )
    i = 0
    list_res = []
    for res in search_result:
        list_res.append({"id":i,"path":res.metadata.get("path"),"content":res.page_content})
    return list_res

@app.post("/ask_localai")
async def ask_localai(Item:Item):
    query = Item.query
    search_result = qdrant.similarity_search(
        query=query, k=10
    )
    i = 0
    list_res = []
    context = ""
    mappings = {}
    i = 0
    for res in search_result:
        context = context + str(i)+"\n"+res.page_content+"\n\n"
        mappings[i] = res.metadata.get("path")
        list_res.append({"id":i,"path":res.metadata.get("path"),"content":res.page_content})
        i = i +1

    rolemsg = {"role": "system",
               "content": "Answer user's question using documents given in the context. In the context are documents that should contain an answer. Please always reference document id (in squere brackets, for example [0],[1]) of the document that was used to make a claim. Use as many citations and documents as it is necessary to answer question."}
    messages = [
        rolemsg,
        {"role": "user", "content": "Documents:\n"+context+"\n\nQuestion: "+query},
    ]
    if use_nvidia_api:
        completion = client_ai.chat.completions.create(
            model="meta/llama3-70b-instruct",
            messages=messages,
            temperature=0.5,
            top_p=1,
            max_tokens=1024,
            stream=False
        )
        response = completion.choices[0].message.content
    else:
        input_ids = tokenizer.apply_chat_template(
                messages,
                add_generation_prompt=True,
                return_tensors="pt"
            ).to(model.device)

        terminators = [
            tokenizer.eos_token_id,
            tokenizer.convert_tokens_to_ids("<|eot_id|>")
            ]

        outputs = model.generate(
            input_ids,
            max_new_tokens=256,
            eos_token_id=terminators,
            do_sample=True,
            temperature=0.2,
            top_p=0.9,
        )
        response = tokenizer.decode(outputs[0][input_ids.shape[-1]:])
    return {"context":list_res,"answer":response}
```

两个功能都是POST方法，我们使用我们的Item类通过JSON体传递查询。第一个方法返回10个最相似的文档片段，包含路径，并分配文档ID从0到9。因此，它仅执行使用点积作为相似度度量的简单语义搜索（这一点在Qdrant的索引过程中定义——记住那一行包含distance=Distance.DOT）。

第二个功能叫做ask_localai，稍微复杂一些。它包含来自第一个方法的搜索机制（因此可以更容易地通过代码理解语义搜索），但增加了生成部分。它为Llama 3生成了一个提示，包含一个系统提示消息，内容如下：

> 使用上下文中提供的文档回答用户的问题。在上下文中是应该包含答案的文档。请始终引用用于做出声明的文档ID（用方括号表示，例如[0]、[1]）。根据需要使用足够多的引用和文档来回答问题。

用户的消息包含一个文档列表，结构为ID（0–9），后跟文档片段在下一行。为了保持ID与文档路径之间的映射，我们创建了一个名为list_res的列表，其中包含ID、路径和内容。用户提示以“Question”一词结束，后面跟着用户的查询。

响应包含上下文和生成的答案。然而，答案是由Llama 3 70B模型（使用NVIDIA NIM API）、本地Llama 3 8B或本地量化的Llama 3 8B生成的，这取决于传递的参数。

API可以从一个包含以下代码行的单独文件中启动（假设我们的生成组件位于名为api.py的文件中，Uvicorn的第一个参数映射到文件名）：

```py
import uvicorn

if __name__=="__main__":
    uvicorn.run("api:app",host='0.0.0.0', port=8000, reload=False,  workers=3)
```

# 简单的用户界面

我们的本地生成搜索引擎的最终组件是用户界面。我们将使用[Streamlit](https://streamlit.io/)构建一个简单的用户界面，包含一个输入框、一个搜索按钮、一个显示生成答案的区域，以及一个可以打开或下载的参考文档列表。

Streamlit中用户界面的全部代码不到45行（准确来说是44行）：

```py
import re
import streamlit as st
import requests
import json
st.title('_:blue[Local GenAI Search]_ :sunglasses:')
question = st.text_input("Ask a question based on your local files", "")
if st.button("Ask a question"):
    st.write("The current question is \"", question+"\"")
    url = "http://127.0.0.1:8000/ask_localai"

    payload = json.dumps({
      "query": question
    })
    headers = {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    }

    response = requests.request("POST", url, headers=headers, data=payload)

    answer = json.loads(response.text)["answer"]
    rege = re.compile("\[Document\ [0-9]+\]|\[[0-9]+\]")
    m = rege.findall(answer)
    num = []
    for n in m:
        num = num + [int(s) for s in re.findall(r'\b\d+\b', n)]

    st.markdown(answer)
    documents = json.loads(response.text)['context']
    show_docs = []
    for n in num:
        for doc in documents:
            if int(doc['id']) == n:
                show_docs.append(doc)
    a = 1244
    for doc in show_docs:
        with st.expander(str(doc['id'])+" - "+doc['path']):
            st.write(doc['content'])
            with open(doc['path'], 'rb') as f:
                st.download_button("Downlaod file", f, file_name=doc['path'].split('/')[-1],key=a
                )
                a = a + 1
```

最终效果将是这样的：

![](../Images/a81808986db73b743ff6767665018ad7.png)

用户界面中已回答问题的示例。截图由作者提供。

# 可用性

该项目的完整代码可以在GitHub上找到，网址为[https://github.com/nikolamilosevic86/local-genAI-search](https://github.com/nikolamilosevic86/local-genAI-search)。过去，我曾参与多个生成式搜索项目，并且也有一些相关的出版物。你可以查看[https://www.thinkmind.org/library/INTERNET/INTERNET_2024/internet_2024_1_10_48001.html](https://www.thinkmind.org/library/INTERNET/INTERNET_2024/internet_2024_1_10_48001.html)或[https://arxiv.org/abs/2402.18589](https://arxiv.org/abs/2402.18589)。

# 结论

本文展示了如何利用生成式AI结合Qdrant进行语义搜索。它通常是一个检索增强生成（RAG）管道，作用于本地文件，并提供引用本地文档声明的指令。整个代码大约有300行，我们甚至增加了复杂性，给用户提供了在3个不同的Llama 3模型之间选择的选项。对于这个使用案例，8B和70B参数模型都能很好地工作。

我想解释一下我所做的步骤，希望这能对将来某些人有所帮助。不过，如果你想使用这个特定的工具，最简单的方式就是直接从[GitHub](https://github.com/nikolamilosevic86/local-genAI-search)获取，它是完全开源的！
