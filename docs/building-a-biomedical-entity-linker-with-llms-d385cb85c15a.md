# 使用 LLM 构建生物医学实体链接器

> 原文：[https://towardsdatascience.com/building-a-biomedical-entity-linker-with-llms-d385cb85c15a?source=collection_archive---------1-----------------------#2024-03-19](https://towardsdatascience.com/building-a-biomedical-entity-linker-with-llms-d385cb85c15a?source=collection_archive---------1-----------------------#2024-03-19)

## 如何有效地将 LLM 应用于生物医学实体链接？

[](https://medium.com/@anand.subu10?source=post_page---byline--d385cb85c15a--------------------------------)[![Anand Subramanian](../Images/096dc5504d6ada2493e0ac26959e60f0.png)](https://medium.com/@anand.subu10?source=post_page---byline--d385cb85c15a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d385cb85c15a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d385cb85c15a--------------------------------) [Anand Subramanian](https://medium.com/@anand.subu10?source=post_page---byline--d385cb85c15a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d385cb85c15a--------------------------------) ·阅读时长 26 分钟·2024年3月19日

--

![](../Images/0d4e8f951e2ffc6a22bf4f64e0b63818.png)

图片来源：[Alina Grubnyak](https://unsplash.com/@alinnnaaaa) 于 [Unsplash](https://unsplash.com/photos/low-angle-photography-of-metal-structure-ZiQkhI7417A)

生物医学文本是一个广泛的术语，通常包括如研究文章、临床试验报告和病人记录等文档，作为关于各种生物学、医学和科学概念的丰富信息库。生物医学领域的研究论文展示了药物发现、药物副作用和新疾病治疗等领域的突破性进展。临床试验报告提供了有关新药物或治疗方法的安全性、有效性和副作用的详细信息。同时，病历记录包含医生和医疗专业人员记录的全面的病史、诊断、治疗方案和治疗结果。

对这些文本进行挖掘可以帮助实践者提取有价值的见解，这些见解可以应用于各种下游任务。你可以挖掘文本来识别不良药物反应、构建自动化医学编码算法，或者实现信息检索或问答系统，从庞大的研究文献库中提取信息。然而，影响生物医学文档处理的一个问题是文本通常是非结构化的。例如，研究人员可能使用不同的术语来指代相同的概念。例如，一个研究人员可能称之为**“心脏病发作”**，而另一个研究人员则称之为**“心肌梗死”**。类似地，在与药物相关的文献中，技术名称和常用名称可能会互换使用。例如，**“对乙酰氨基酚”**是某种药物的技术名称，而**“扑热息痛”**则是其更常用的名称。缩写的普遍使用也增加了复杂性；例如，**“一氧化氮”**可能在另一种情况下被称为**“NO”**。尽管这些不同的术语指代相同的概念，但这些变化使得普通人或文本处理算法很难判断它们是否指的是同一个概念。因此，**实体链接**在这种情况下变得尤为重要。

# 目录：

1.  [什么是实体链接？](#558f)

1.  [LLM在这里有什么作用？](#9285)

1.  [实验设置](#5023)

1.  [处理数据集](#29ad)

1.  [使用LLM进行零样本实体链接](#5925)

1.  [使用带有检索增强生成的LLM进行实体链接](#1299)

1.  [使用LLM和外部知识库链接器进行零样本实体提取](#8d3c)

1.  [使用LLM和外部知识库链接器进行微调实体提取](#274a)

1.  [Scispacy基准测试](#63d3)

1.  [关键要点](#7e94)

1.  [局限性](#8e52)

1.  [参考文献](#fb75)

# 什么是实体链接？

当文本是非结构化时，准确识别和标准化医学概念变得至关重要。为了实现这一点，医学术语系统，如**统一医学语言系统（UMLS）**[1]、**医学临床术语系统化命名（SNOMED-CT）**[2]和**医学主题词表（MeSH）**[3]，在其中扮演着重要角色。这些系统提供了一套全面且标准化的医学概念，每个概念都由一个字母数字代码唯一标识。

实体链接涉及在文本中识别和提取实体，并将它们映射到大型术语库中的标准化概念。在此上下文中，**知识库（KB）**指的是一个详细的数据库，包含与术语库相关的标准化信息和概念，如医学术语、疾病和药物。通常，知识库由专家策划和设计，包含关于概念的详细信息，包括可能用来指代该概念的术语变体，或该概念与其他概念的关系。

![](../Images/2d49dc0785ee698265fce0c8c4a29456.png)

实体识别与链接管道概述。实体首先从文本中解析出来，然后将每个实体链接到知识库，以获取它们对应的标识符。本例中考虑的知识库是MeSH术语。示例文本来自BioCreative V CDR语料库[4,5,6,7,8]（图像来自作者）。

实体识别涉及提取在我们任务上下文中重要的单词或短语。在这个上下文中，通常指的是提取生物医学术语，如药物、疾病等。通常，基于查找的方法或基于机器学习/深度学习的系统常用于实体识别。将实体链接到知识库通常需要一个检索系统，该系统对知识库进行索引。该系统从前一步中提取每个实体，并从知识库中检索可能的标识符。这里的检索器也是一种抽象，可能是稀疏的（BM-25）、密集的（基于嵌入的）或甚至是生成性的系统（如大型语言模型（LLM）），它将知识库编码在其参数中。

# LLM在这里的作用是什么？

我一直很好奇如何将LLM集成到生物医学和临床文本处理管道中。鉴于实体链接是这类管道的重要组成部分，我决定探索LLM如何最好地应用于这个任务。具体来说，我研究了以下几种设置：

1.  **零-shot实体链接与LLM：** 利用LLM直接从输入的生物医学文本中识别所有实体和概念ID，而无需任何微调。

1.  **LLM与检索增强生成（RAG）：** 在RAG框架中使用LLM，通过在提示中注入有关相关概念ID的信息来进行实体链接。

1.  **使用外部知识库链接器的零-shot实体提取：** 利用LLM从生物医学文本中进行零-shot实体提取，并使用外部链接器/检索器将实体映射到概念ID。

1.  **与外部知识库链接器的微调实体提取：** 首先对LLM进行实体提取任务的微调，并将其用作实体提取器，与外部链接器/检索器结合，将实体映射到概念ID。

1.  **与现有管道的比较：** 与用于生物医学文本处理的常用库Scispacy相比，这些方法表现如何？

# 实验设置

> 与本文相关的所有代码和资源都可以在[此Github仓库](https://github.com/anand-subu/blog_resources)的entity_linking文件夹中找到。欢迎拉取仓库并直接运行笔记本，以进行这些实验。如果您有任何反馈或观察，或者发现任何错误，请告诉我！

为了进行这些实验，我们利用 [Mistral-7B Instruct](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) [9] 作为我们的 LLM。为了将医学术语与实体进行链接，我们使用 MeSH 术语。引用 [美国国家医学图书馆网站](https://www.nlm.nih.gov/mesh/meshhome.html) 的话：

> “医学主题词表（MeSH）是由美国国家医学图书馆（National Library of Medicine）制作的一个受控且层级组织的词汇表，用于对生物医学和健康相关信息进行索引、目录编制和检索。”

我们使用 BioCreative-V-CDR-Corpus [4,5,6,7,8] 进行评估。该数据集包含了疾病和化学实体的标注，以及它们对应的 MeSH ID。为了评估的目的，我们从测试集中随机抽取了 100 个数据点。我们使用了 Scispacy [10,11] 提供的 MeSH 知识库版本，其中包含了 MeSH 标识符的信息，例如定义和与每个 ID 对应的实体。

为了评估性能，我们计算两个指标。第一个指标与实体提取性能相关。原始数据集包含文本中所有实体的提及，并在子字符串级别进行标注。严格的评估会检查算法是否输出了所有实体的所有出现。然而，我们简化了这个过程以便于评估；我们将实体在真实标签中的大小写转换为小写并去重。然后，我们为每个实例计算精确度、召回率和 F1 得分，并计算每个指标的宏平均值。

假设你有一组实际实体，`ground_truth`，以及一个模型为每个输入文本预测的实体集，`pred`。**真正例** `TP` 可以通过识别 `pred` 和 `ground_truth` 之间的共同元素来确定，实际上就是计算这两个集合的交集。

对于每个输入，我们可以计算：

`precision = len(TP)/ len(pred)`，

`recall = len(TP) / len(ground_truth)` 和

`f1 = 2 * precision * recall / (precision + recall)`

最后，通过将所有指标加总并除以测试集中的数据点数，我们可以计算每个指标的宏平均值。

为了评估整体实体链接性能，我们再次计算相同的指标。在这种情况下，对于每个输入数据点，我们有一组元组，其中每个元组是一个 `(entity, mesh_id)` 对。其他指标的计算方式相同。

# 处理数据集

好的，让我们先从定义一些用于处理数据集的辅助函数开始。

```py
def parse_dataset(file_path):
    """
    Parse the BioCreative Dataset.

    Args:
    - file_path (str): Path to the file containing the documents.

    Returns:
    - list of dict: A list where each element is a dictionary representing a document.
    """
    documents = []
    current_doc = None

    with open(file_path, 'r', encoding='utf-8') as file:
        for line in file:
            line = line.strip()
            if not line:
                continue
            if "|t|" in line:
                if current_doc:
                    documents.append(current_doc)
                id_, title = line.split("|t|", 1)
                current_doc = {'id': id_, 'title': title, 'abstract': '', 'annotations': []}
            elif "|a|" in line:
                _, abstract = line.split("|a|", 1)
                current_doc['abstract'] = abstract
            else:
                parts = line.split("\t")
                if parts[1] == "CID":
                    continue
                annotation = {
                    'text': parts[3],
                    'type': parts[4],
                    'identifier': parts[5]
                }
                current_doc['annotations'].append(annotation)

        if current_doc:
            documents.append(current_doc)

    return documents

def deduplicate_annotations(documents):
    """
    Filter documents to ensure annotation consistency.

    Args:
    - documents (list of dict): The list of documents to be checked.
    """
    for doc in documents:
        doc["annotations"] = remove_duplicates(doc["annotations"])

def remove_duplicates(dict_list):
    """
    Remove duplicate dictionaries from a list of dictionaries.

    Args:
    - dict_list (list of dict): A list of dictionaries from which duplicates are to be removed.

    Returns:
    - list of dict: A list of dictionaries after removing duplicates.
    """
    unique_dicts = []  
    seen = set()

    for d in dict_list:
        dict_tuple = tuple(sorted(d.items()))
        if dict_tuple not in seen:
            seen.add(dict_tuple)
            unique_dicts.append(d)

    return unique_dicts
```

我们首先解析原始数据集中提供的文本文件中的数据集。原始数据集包括标题、摘要，以及所有标注的实体和它们的实体类型（疾病或化学物质）、它们在文本中的准确位置的子字符串索引，以及它们的MeSH ID。在处理我们的数据集时，我们进行了一些简化。我们忽略了子字符串索引和实体类型。此外，我们去重了具有相同实体名称和MeSH ID的标注。在这一阶段，我们仅进行区分大小写的去重处理，这意味着如果同一实体在文档中同时以大小写不同的形式出现，我们目前的处理方式会保留这两个实例。

# **使用LLM进行零-shot实体链接**

首先，我们旨在确定LLM是否由于其预训练而已经具备了MeSH术语的理解，以及它是否能够作为零-shot实体链接器。所谓零-shot，指的是LLM基于其内在知识，在没有依赖外部知识库链接器的情况下，直接将实体链接到其MeSH ID。这个假设并不完全不现实，因为MeSH相关的信息在网上是可以找到的，这意味着模型在预训练阶段可能接触过MeSH相关的信息。然而，即使LLM在训练时包含了这些信息，仅凭这些信息也不太可能使模型有效地执行零-shot实体链接，因为生物医学术语的复杂性和进行准确实体链接所需的精确性都非常高。

为了评估这一点，我们将输入文本提供给LLM，并直接提示它预测实体及其对应的MeSH ID。此外，我们通过从训练数据集中抽取三个数据点来创建一个few-shot提示。需要澄清的是，这里“zero-shot”和“few-shot”的使用有所区别：“zero-shot”指的是LLM在没有针对这一任务的特定训练的情况下进行实体链接，而“few-shot”指的是在该上下文中采用的提示策略。

![](../Images/fd42fe12a7a1f7a02ab36a6869cb6304.png)

LLM作为零-shot实体链接器（图像由作者提供）

为了计算我们的指标，我们定义了评估性能的函数：

```py
def calculate_entity_metrics(gt, pred):
    """
    Calculate precision, recall, and F1-score for entity recognition.

    Args:
    - gt (list of dict): A list of dictionaries representing the ground truth entities. 
                         Each dictionary should have a key "text" with the entity text.
    - pred (list of dict): A list of dictionaries representing the predicted entities.
                           Similar to `gt`, each dictionary should have a key "text".

    Returns:
    tuple: A tuple containing precision, recall, and F1-score (in that order).
    """
    ground_truth_set = set([x["text"].lower() for x in gt])
    predicted_set = set([x["text"].lower() for x in pred])

    # True positives are predicted items that are in the ground truth
    true_positives = len(predicted_set.intersection(ground_truth_set))

    # Precision calculation
    if len(predicted_set) == 0:
        precision = 0
    else:
        precision = true_positives / len(predicted_set)

    # Recall calculation
    if len(ground_truth_set) == 0:
        recall = 0
    else:
        recall = true_positives / len(ground_truth_set)

    # F1-score calculation
    if precision + recall == 0:
        f1_score = 0
    else:
        f1_score = 2 * (precision * recall) / (precision + recall)

    return precision, recall, f1_score

def calculate_mesh_metrics(gt, pred):
    """
    Calculate precision, recall, and F1-score for matching MeSH (Medical Subject Headings) codes.

    Args:
    - gt (list of dict): Ground truth data
    - pred (list of dict): Predicted data

    Returns:
    tuple: A tuple containing precision, recall, and F1-score (in that order).
    """
    ground_truth = []

    for item in gt:
        mesh_codes = item["identifier"]
        if mesh_codes == "-1":
            mesh_codes = "None"
        mesh_codes_split = mesh_codes.split("|")
        for elem in mesh_codes_split:
            combined_elem = {"entity": item["text"].lower(), "identifier": elem}
            if combined_elem not in ground_truth:
                ground_truth.append(combined_elem)

    predicted = []
    for item in pred:
        mesh_codes = item["identifier"]
        mesh_codes_split = mesh_codes.strip().split("|")
        for elem in mesh_codes_split:
            combined_elem = {"entity": item["text"].lower(), "identifier": elem}
            if combined_elem not in predicted:
                predicted.append(combined_elem)
    # True positives are predicted items that are in the ground truth
    true_positives = len([x for x in predicted if x in ground_truth])

    # Precision calculation
    if len(predicted) == 0:
        precision = 0
    else:
        precision = true_positives / len(predicted)

    # Recall calculation
    if len(ground_truth) == 0:
        recall = 0
    else:
        recall = true_positives / len(ground_truth)

    # F1-score calculation
    if precision + recall == 0:
        f1_score = 0
    else:
        f1_score = 2 * (precision * recall) / (precision + recall)

    return precision, recall, f1_score
```

现在，让我们运行模型并获取预测结果：

```py
model = AutoModelForCausalLM.from_pretrained("mistralai/Mistral-7B-Instruct-v0.2",  torch_dtype=torch.bfloat16).cuda()
tokenizer = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-Instruct-v0.2")
model.eval()

mistral_few_shot_answers = []
for item in tqdm(test_set_subsample):
    few_shot_prompt_messages = build_few_shot_prompt(SYSTEM_PROMPT, item, few_shot_example)
    input_ids = tokenizer.apply_chat_template(few_shot_prompt_messages, tokenize=True, return_tensors = "pt").cuda()
    outputs = model.generate(input_ids = input_ids, max_new_tokens=200, do_sample=False)    
    # https://github.com/huggingface/transformers/issues/17117#issuecomment-1124497554
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    mistral_few_shot_answers.append(parse_answer(gen_text.strip()))
```

在实体提取层面，考虑到LLM并未专门为此任务进行显式微调，其表现相当不错。然而，作为零-shot链接器，它的表现非常差，整体表现低于1%。这个结果是直观的，因为MeSH标签的输出空间非常庞大，将实体精确映射到特定的MeSH ID是一项艰巨的任务。

零-shot实体提取与实体链接得分

# **使用检索增强生成的LLM进行实体链接**

检索增强生成（RAG）[12] 指的是一种将 LLM 与外部知识库（KB）结合的框架，该知识库配备了查询功能，如检索器/链接器。对于每个输入查询，系统首先使用查询功能从 KB 中检索与查询相关的知识。然后，系统将检索到的知识与查询结合，将这个合并后的提示提供给 LLM 来执行任务。这种方法基于这样一个理解：LLM 可能没有所有必要的知识或信息来有效地回答输入查询。因此，知识通过查询外部知识源注入到模型中。

使用 RAG 框架可以提供几个优势：

1.  现有的 LLM 可以用于新的领域或任务**而无需领域特定的微调**，因为相关信息可以通过提示查询并提供给模型。

1.  LLM 有时在回答查询时可能会提供**不正确的答案（幻想）**。使用 RAG 与 LLM 结合可以显著减少这种幻想，因为 LLM 提供的答案**更有可能基于事实**，这是由于有外部知识的支持。

考虑到 LLM 对 MeSH 术语缺乏特定的知识，我们研究 RAG 设置是否能提高性能。在这种方法中，对于每个输入段落，我们使用 BM-25 检索器查询知识库（KB）。对于每个 MeSH ID，我们可以访问该 ID 的一般描述和与之相关的实体名称。检索后，我们通过提示将这些信息注入模型，以进行实体链接。

为了研究提供给模型的检索到的 ID 数量对实体链接过程的影响，我们运行此设置，提供前 10、30 和 50 个文档给模型，并量化其在实体提取和 MeSH 概念识别上的表现。

![](../Images/d148dffe6caaa1a993320d088efbe29e.png)

具有 RAG 的 LLM 作为实体链接器（图示由作者提供）

首先，我们定义我们的 BM-25 检索器：

```py
from rank_bm25 import BM25Okapi
from typing import List, Tuple, Dict
from nltk.tokenize import word_tokenize
from tqdm import tqdm

class BM25Retriever:
    """
    A class for retrieving documents using the BM25 algorithm.

    Attributes:
        index (List[int, str]): A dictionary with document IDs as keys and document texts as values.
        tokenized_docs (List[List[str]]): Tokenized version of the documents in `processed_index`.
        bm25 (BM25Okapi): An instance of the BM25Okapi model from the rank_bm25 package.
    """

    def __init__(self, docs_with_ids: Dict[int, str]):
        """
        Initializes the BM25Retriever with a dictionary of documents.

        Args:
            docs_with_ids (List[List[str, str]]): A dictionary with document IDs as keys and document texts as values.
        """
        self.index = docs_with_ids
        self.tokenized_docs = self._tokenize_docs([x[1] for x in self.index])
        self.bm25 = BM25Okapi(self.tokenized_docs)

    def _tokenize_docs(self, docs: List[str]) -> List[List[str]]:
        """
        Tokenizes the documents using NLTK's word_tokenize.

        Args:
            docs (List[str]): A list of documents to be tokenized.

        Returns:
            List[List[str]]: A list of tokenized documents.
        """
        return [word_tokenize(doc.lower()) for doc in docs]

    def query(self, query: str, top_n: int = 10) -> List[Tuple[int, float]]:
        """
        Queries the BM25 model and retrieves the top N documents with their scores.

        Args:
            query (str): The query string.
            top_n (int): The number of top documents to retrieve.

        Returns:
            List[Tuple[int, float]]: A list of tuples, each containing a document ID and its BM25 score.
        """
        tokenized_query = word_tokenize(query.lower())
        scores = self.bm25.get_scores(tokenized_query)
        doc_scores_with_ids = [(doc_id, scores[i]) for i, (doc_id, _) in enumerate(self.index)]
        top_doc_ids_and_scores = sorted(doc_scores_with_ids, key=lambda x: x[1], reverse=True)[:top_n]
        return [x[0] for x in top_doc_ids_and_scores]
```

我们现在处理我们的 KB 文件，并创建一个索引它的 BM-25 检索器实例。在索引 KB 时，我们通过将描述、别名和规范名称连接在一起，对每个 ID 进行索引。

```py
def process_index(index):
    """
    Processes the initial document index to combine aliases, canonical names, and definitions into a single text index.

    Args:
    - index (Dict): The MeSH knowledge base
    Returns:
        List[List[int, str]]: A dictionary with document IDs as keys and combined text indices as values.
    """
    processed_index = []
    for key, value in tqdm(index.items()):
        assert(type(value["aliases"]) != list)
        aliases_text = " ".join(value["aliases"].split(","))
        text_index = (aliases_text + " " +  value.get("canonical_name", "")).strip()
        if "definition" in value:
            text_index += " " + value["definition"]
        processed_index.append([value["concept_id"], text_index])
    return processed_index

mesh_data = read_jsonl_file("mesh_2020.jsonl")
process_mesh_kb(mesh_data)
mesh_data_kb = {x["concept_id"]:x for x in mesh_data}
mesh_data_dict = process_index({x["concept_id"]:x for x in mesh_data})
retriever = BM25Retriever(mesh_data_dict)
```

```py
mistral_rag_answers = {10:[], 30:[], 50:[]}

for k in [10,30,50]:
    for item in tqdm(test_set_subsample):
        relevant_mesh_ids = retriever.query(item["title"] + " " + item["abstract"], top_n = k)
        relevant_contexts = [mesh_data_kb[x] for x in relevant_mesh_ids]
        rag_prompt = build_rag_prompt(SYSTEM_RAG_PROMPT, item, relevant_contexts)
        input_ids = tokenizer.apply_chat_template(rag_prompt, tokenize=True, return_tensors = "pt").cuda()
        outputs = model.generate(input_ids = input_ids, max_new_tokens=200, do_sample=False)    
        gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
        mistral_rag_answers[k].append(parse_answer(gen_text.strip()))
```

```py
entity_scores_at_k = {}
mesh_scores_at_k = {}

for key, value in mistral_rag_answers.items():
    entity_scores = [calculate_entity_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, value)]
    macro_precision_entity = sum([x[0] for x in entity_scores]) / len(entity_scores)
    macro_recall_entity = sum([x[1] for x in entity_scores]) / len(entity_scores)
    macro_f1_entity = sum([x[2] for x in entity_scores]) / len(entity_scores)
    entity_scores_at_k[key] = {"macro-precision": macro_precision_entity, "macro-recall": macro_recall_entity, "macro-f1": macro_f1_entity}

    mesh_scores = [calculate_mesh_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, value)]
    macro_precision_mesh = sum([x[0] for x in mesh_scores]) / len(mesh_scores)
    macro_recall_mesh = sum([x[1] for x in mesh_scores]) / len(mesh_scores)
    macro_f1_mesh = sum([x[2] for x in mesh_scores]) / len(mesh_scores)
    mesh_scores_at_k[key] = {"macro-precision": macro_precision_mesh, "macro-recall": macro_recall_mesh, "macro-f1": macro_f1_mesh}
```

通常，与原始的零-shot 设置相比，RAG 设置改进了整体的 MeSH 标识过程。但提供给模型的信息文档数量有何影响呢？我们绘制了得分与提供给模型作为上下文的检索到的 ID 数量的关系。

![](../Images/6ae236d2474983aadfbb5d1e563cb577.png)![](../Images/b3d3431fb1870acf1eb3e99153a7c978.png)

在 RAG 设置中，实体提取和实体链接性能指标随着检索文档数量变化的图表（图示由作者提供）

在研究图表时，我们观察到了一些有趣的趋势。对于实体提取，检索到的文档数量增加与宏观精度的急剧提高相关，宏观精度得分略高于50%。这比模型的零-shot实体提取性能高出近10%。然而，宏观召回率的影响是任务相关的；对于实体提取任务，它保持不变，但对于实体链接任务则有所提高。总体而言，增加提供给模型的文档数量作为上下文，在MeSH标识设置中显著改善了所有指标，但在实体提取设置中效果不一。

在这个实验中需要考虑的一个重要限制是上游检索器的性能。如果检索器无法检索到相关文档，那么LLM的性能将受到影响，因为模型所提供的知识中并没有实际答案。

![](../Images/7295ffa080576fa90975f690981e945d.png)

作为输入文本每个MeSH ID被检索器检索到的MeSH ID中的地面真值百分比，作为检索到的ID总数的函数（图片由作者提供）

为了调查这一点，我们计算了每个输入文本中，检索器所提取的MeSH ID中包含的地面真值MeSH ID的平均百分比。我们的发现表明，BM-25检索器平均只能检索到约12.6%到17.7%的相关MeSH ID。检索器的选择和我们检索的方式因此成为RAG设置中的一个重要性能瓶颈，可能需要优化以提升性能。

# **LLM与外部知识库连接器的零-shot实体提取**

到目前为止，我们已经考察了LLM作为零-shot实体链接器的表现，以及RAG如何提升其性能。尽管与零-shot设置相比，RAG提高了性能，但这种方法也有其局限性。

在RAG设置中使用LLM时，我们一直将知识组件（KB + 检索器）**置于模型的上游**。RAG设置中的知识检索是**粗糙的**，即通过查询检索器使用整个生物医学文本来检索可能的MeSH ID。这在一定程度上保证了检索结果的**多样性**，因为获取的结果很可能对应文本中的不同实体，但这些结果的**精确度**较低。起初这似乎并不是问题，因为你可以通过在RAG设置中为模型提供更多相关结果作为上下文来在一定程度上缓解这个问题。然而，这有两个缺点：

1.  LLM通常在处理文本时有一个上下文长度的上限。LLM的上下文长度大致指的是LLM在生成新文本之前，可以考虑的最大标记数量（提示中的标记数）。这可能限制我们能提供给LLM的知识量。

1.  假设我们有一个能够处理长上下文长度的LLM。我们现在可以检索并附加更多的上下文到模型中。太棒了！然而，更长的上下文长度不一定与LLM的增强RAG能力相关联[13]。即使你通过检索更多结果将大量相关知识传递给LLM，也不能保证LLM会准确地提取出正确的答案。

这将我们带回最初描述的**传统管道**的实体链接。在这种设置中，知识组件被保持在模型的**下游**，在实体提取后，实体被提供给外部检索器以获取相关的MeSH ID。只要你有一个好的实体提取器，你就可以检索到更精确的MeSH ID。

早些时候，我们在完全零-shot的设置下观察到，虽然LLM在预测MeSH ID时表现较差，但它的实体提取表现相当不错。我们现在使用Mistral模型提取实体，并将其提供给外部检索器以获取MeSH ID。

![](../Images/fd74260b6f485d82ec964f16ec94c936.png)

使用LLM作为实体提取器和外部检索器的实体链接（作者提供的图像）

在此检索中，我们再次使用BM-25检索器作为我们的知识库链接器。然而，我们在这里做的一个小调整是将我们的ID索引基于连接它们的标准名称和别名。我们重新使用在第一次零-shot设置中提取的实体进行本次实验。现在让我们评估一下这个设置的表现如何：

```py
entity_mesh_data_dict = [[x["concept_id"] , " ".join(x["aliases"].split(",")) + " " + x["canonical_name"]] for x in mesh_data]
entity_retriever = BM25Retriever(entity_mesh_data_dict)

parsed_entities_few_shot = [[y["text"] for y in x] for x in mistral_few_shot_answers]
retrieved_answers = []

for item in tqdm(parsed_entities_few_shot):
    answer_element = []
    for entity in item:
        retrieved_mesh_ids = entity_retriever.query(entity, top_n = 1)
        answer_element.append({"text": entity, "identifier":retrieved_mesh_ids[0]})
    retrieved_answers.append(answer_element)

mesh_scores = [calculate_mesh_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, retrieved_answers)]
macro_precision_mesh = sum([x[0] for x in mesh_scores]) / len(metric_scores)
macro_recall_mesh = sum([x[1] for x in mesh_scores]) / len(metric_scores)
macro_f1_mesh = sum([x[2] for x in mesh_scores]) / len(metric_scores)
```

在这种设置下，性能在所有指标上都明显优于RAG设置。与最佳RAG设置（在50个文档进行检索）相比，我们在宏精度上提高了超过12%，在宏召回率上提高了20%，在宏F1得分上提高了16%。**再次强调**，这更类似于传统的实体提取管道，其中实体提取和链接是分开的组件。

Zero-Shot LLM 实体提取与外部检索器得分

# **使用LLM和外部知识库链接器的微调实体提取**

到目前为止，我们通过将LLM作为实体提取器放入更大的管道中获得了最佳性能。然而，我们是在零-shot的方式下进行实体提取的。我们是否可以通过特别为实体提取微调LLM来进一步提升性能？

对于微调，我们使用来自BioCreative V数据集的训练集，该数据集包含500个数据点。我们采用Q-Lora [14]来微调我们的LLM，这一过程包括将LLM量化为4位并冻结，同时微调低秩适配器。这种方法通常在参数和内存方面效率较高，因为适配器的权重仅占原始LLM的极小一部分，意味着我们微调的权重要远少于微调整个LLM的情况。它还使我们能够在单个GPU上微调模型。

让我们实现微调组件。对于这部分，我参考并修改了[Niels Rogge关于使用Q-Lora微调Mistral模型的笔记本](https://github.com/NielsRogge/Transformers-Tutorials/blob/master/Mistral/Supervised_fine_tuning_(SFT)_of_an_LLM_using_Hugging_Face_tooling.ipynb)，修改内容主要集中在正确准备和处理数据集。

```py
from datasets import load_dataset
import json
from tqdm import tqdm
from itertools import chain
from datasets import DatasetDict
from transformers import AutoTokenizer, BitsAndBytesConfig
import torch
from trl import SFTTrainer
from peft import LoraConfig
from transformers import TrainingArguments
from helpers import *

def read_jsonl_file(file_path):
    """
    Parses a JSONL (JSON Lines) file and returns a list of dictionaries.

    Args:
        file_path (str): The path to the JSONL file to be read.

    Returns:
        list of dict: A list where each element is a dictionary representing
            a JSON object from the file.
    """
    jsonl_lines = []
    with open(file_path, 'r', encoding="utf-8") as file:
        for line in file:
            json_object = json.loads(line)
            jsonl_lines.append(json_object)

    return jsonl_lines

def convert_to_template(data):
    messages = []
    messages.append({"role": "user", "content": data["question"]})
    messages.append({"role": "assistant", "content": data["answer"]})

    return tokenizer.apply_chat_template(messages, tokenize = False)

mesh_dataset = parse_dataset("CDR_TrainingSet.PubTator.txt")
```

现在，我们加载分词器并设置适当的参数：

```py
model_id = "mistralai/Mistral-7B-Instruct-v0.2"

tokenizer = AutoTokenizer.from_pretrained(model_id)

# set pad_token_id equal to the eos_token_id if not set
tokenizer.pad_token_id = tokenizer.eos_token_id
tokenizer.padding_side = "right"

# Set reasonable default for models without max length
if tokenizer.model_max_length > 100_000:
  tokenizer.model_max_length = 512
```

现在让我们准备并正确格式化我们的数据集。我们为模型定义提示，并将数据集格式化为预期的聊天模板。

```py
prepared_dataset = []
system_prompt = "Answer the question factually and precisely."
entity_prompt = "What are the chemical and disease related entities present in this biomedical text?"
prepared_dataset = []

def prepare_instructions(elem):
    entities = []
    for x in elem["annotations"]:
        if x["text"] not in entities:
            entities.append(x["text"])

    return {"question": system_prompt + "\n" + entity_prompt + "\n" + elem["title"] + " " + elem["abstract"] , "answer": "The entities are:" + ",".join(entities)}

questions = [prepare_instructions(x) for x in tqdm(mesh_dataset)]
chat_format_questions = [{"text": convert_to_template(x)} for x in tqdm(questions)]

df = pd.DataFrame(chat_format_questions)
train_dataset = Dataset.from_pandas(df)
```

现在让我们为微调模型定义适当的配置。我们为量化LLM定义配置：

```py
quantization_config = BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_quant_type="nf4",
            bnb_4bit_compute_dtype=torch.bfloat16,
)

device_map = {"": torch.cuda.current_device()} if torch.cuda.is_available() else None

model_kwargs = dict(
    torch_dtype=torch.bfloat16,
    use_cache=False, # set to False as we're going to use gradient checkpointing
    device_map=device_map,
    quantization_config=quantization_config,
)
```

现在，我们已经准备好微调我们的模型：

```py
output_dir = 'entity_finetune'

# based on config
training_args = TrainingArguments(
    bf16=True, # specify bf16=True instead when training on GPUs that support bf16
    do_eval=False,
    # evaluation_strategy="no",
    gradient_accumulation_steps=1,
    gradient_checkpointing=True,
    gradient_checkpointing_kwargs={"use_reentrant": False},
    learning_rate=1.0e-04,
    log_level="info",
    logging_steps=5,
    logging_strategy="steps",
    lr_scheduler_type="cosine",
    max_steps=-1,
    num_train_epochs=5,
    output_dir=output_dir,
    overwrite_output_dir=True,
    per_device_eval_batch_size=1, 
    per_device_train_batch_size=8,
    save_strategy="no",
    save_total_limit=None,
    seed=42,
)

# based on config
peft_config = LoraConfig(
        r=16,
        lora_alpha=16,
        lora_dropout=0.1,
        bias="none",
        task_type="CAUSAL_LM",
        target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
)

trainer = SFTTrainer(
        model=model_id,
        model_init_kwargs=model_kwargs,
        args=training_args,
        train_dataset=train_dataset,
        eval_dataset=train_dataset,
        dataset_text_field="text",
        tokenizer=tokenizer,
        packing = True,
        peft_config=peft_config,
        max_seq_length=tokenizer.model_max_length,
    )

train_result = trainer.train()
trainer.save_model(output_dir)
```

微调过程完成后，我们现在利用模型进行推理并获得性能指标：

```py
def parse_entities_from_trained_model(content):
    """
    Extracts a list of entities from the output of a trained model.

    Args:
    - content (str): The raw string output from a trained model.

    Returns:
    - list of str: A list of entities extracted from the model's output.
    """
    return content.split("The entities are:")[-1].split(",")

mistral_few_shot_answers = []
for item in tqdm(test_set_subsample):
    few_shot_prompt_messages = build_entity_prompt(item)
    # input_ids = tokenizer.apply_chat_template(few_shot_prompt_messages, tokenize=True, return_tensors = "pt").cuda()
    prompt = tokenizer.apply_chat_template(few_shot_prompt_messages, tokenize=False)
    tensors = tokenizer(prompt, return_tensors="pt")
    input_ids = tensors.input_ids.cuda()
    attention_mask = tensors.attention_mask.cuda()
    outputs = model.generate(input_ids = input_ids, attention_mask = attention_mask, max_new_tokens=200, do_sample=False)    
    # https://github.com/huggingface/transformers/issues/17117#issuecomment-1124497554
    gen_text = tokenizer.batch_decode(outputs.detach().cpu().numpy()[:, input_ids.shape[1]:], skip_special_tokens=True)[0]
    mistral_few_shot_answers.append(parse_entities_from_trained_model(gen_text.strip()))
```

```py
parsed_entities_few_shot = [[y["text"] for y in x] for x in mistral_few_shot_answers]
retrieved_answers = []

for item in tqdm(parsed_entities_few_shot):
    answer_element = []
    for entity in item:
        retrieved_mesh_ids = entity_ranker.query(entity, top_n = 1)
        answer_element.append({"identifier":retrieved_mesh_ids[0], "text":entity})
    retrieved_answers.append(answer_element)

entity_scores = [calculate_entity_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, retrieved_answers)]
macro_precision_entity = sum([x[0] for x in entity_scores]) / len(entity_scores)
macro_recall_entity = sum([x[1] for x in entity_scores]) / len(entity_scores)
macro_f1_entity = sum([x[2] for x in entity_scores]) / len(entity_scores)

mesh_scores = [calculate_mesh_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, retrieved_answers)]
macro_precision_mesh = sum([x[0] for x in mesh_scores]) / len(mesh_scores)
macro_recall_mesh = sum([x[1] for x in mesh_scores]) / len(mesh_scores)
macro_f1_mesh = sum([x[2] for x in mesh_scores]) / len(mesh_scores)
```

这个设置与之前的设置完全相同，仍然使用LLM作为实体提取器，外部检索器用于将每个实体链接到MeSH ID。微调模型带来了实体提取和链接方面的显著改进。

与零-shot实体提取相比，微调在所有指标上提高了最多或超过20%。类似地，与之前的设置相比，实体链接也在所有指标上提高了约12%至14%。这些结果并不令人惊讶，因为任务特定的模型预计会比零-shot设置表现更好。尽管如此，将这些改进量化出来仍然令人高兴！

微调的LLM作为实体提取器和外部检索器得分

# Scispacy基准测试

这个实现与现有的可以执行实体链接的工具相比如何？Scispacy是生物医学和临床文本处理中常用的工具，提供了实体提取和实体链接功能。特别地，Scispacy还提供了一种将实体链接到MeSH知识库的功能，而这个文件也是我们原始LLM实验中使用的知识库。让我们在我们的测试集上基准测试Scispacy的性能，并与我们的LLM实验进行比较。

我们使用Scispacy中的“**en_ner_bc5cdr_md**” [15]作为实体提取模块，因为该模型是专门在BioCreative V数据集上训练的。让我们评估其性能：

```py
from scispacy.linking import EntityLinker
import spacy, scispacy
import pandas as pd
from helpers import *
from tqdm import tqdm

#code for setting up MeSH linker referred from https://github.com/allenai/scispacy/issues/355
config = {
    "resolve_abbreviations": True,  
    "linker_name": "mesh", 
    "max_entities_per_mention":1
}

nlp = spacy.load("en_ner_bc5cdr_md")
nlp.add_pipe("scispacy_linker", config=config) 

linker = nlp.get_pipe("scispacy_linker")

def extract_mesh_ids(text):
    mesh_entity_pairs = []
    doc = nlp(text)
    for e in doc.ents:
        if e._.kb_ents:
            cui = e._.kb_ents[0][0]
            mesh_entity_pairs.append({"text": e.text, "identifier": cui})
        else:
            mesh_entity_pairs.append({"text": e.text, "identifier": "None"})

    return mesh_entity_pairs
```

```py
all_mesh_ids = []
for item in tqdm(test_set_subsample):
    text = item["title"] + " " + item["abstract"]
    mesh_ids = extract_mesh_ids(text)
    all_mesh_ids.append(mesh_ids)

entity_scores = [calculate_entity_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, all_mesh_ids)]
macro_precision_entity = sum([x[0] for x in entity_scores]) / len(entity_scores)
macro_recall_entity = sum([x[1] for x in entity_scores]) / len(entity_scores)
macro_f1_entity = sum([x[2] for x in entity_scores]) / len(entity_scores)

mesh_scores = [calculate_mesh_metrics(gt["annotations"],pred) for gt, pred in zip(test_set_subsample, all_mesh_ids)]
macro_precision_mesh = sum([x[0] for x in mesh_scores]) / len(entity_scores)
macro_recall_mesh = sum([x[1] for x in mesh_scores]) / len(entity_scores)
macro_f1_mesh = sum([x[2] for x in mesh_scores]) / len(entity_scores)
```

Scispacy评估得分

Scispacy在实体提取方面比微调后的LLM提高了10%的得分，实体链接方面提高了14%到20%！对于生物医学实体提取和链接任务，Scispacy仍然是一个强大的工具。

# 重点总结

![](../Images/c719dd37db7371d0764e5c9e4843dc01.png)

在所有设置下，实体提取和实体链接的宏F1得分

实验结束时，我们可以从中得到哪些具体的收获？

1.  **零-shot实体提取的优势：** Mistral-Instruct是一个不错的生物医学文本零-shot实体提取器。尽管它的参数化知识不足以进行零-shot的MeSH实体链接，但我们在实验中将其与外部KB检索器结合使用，作为实体提取器，从而获得了更好的性能。

1.  **RAG在零-shot预测上的改进：** 在RAG设置中，LLM在实体链接方面相比纯粹的零-shot方法表现出了一定的改进。然而，RAG设置中的检索器组件可能是一个显著的瓶颈，正如我们在案例中所看到的，BM-25检索器每个数据点只能检索到大约12-17%的相关ID。这表明需要更有效的检索方法。

1.  **流水线提取提供最佳性能：** 鉴于LLM作为实体提取器的能力，当将这些能力与包含外部检索器以将实体链接到MeSH知识库（KB）的更大管道结合时，可以实现最佳性能。这与传统设置相同，其中实体提取和KB链接保持为独立模块。

1.  **微调的好处：** 使用QLora对LLM进行微调以进行实体提取任务，可以显著提高实体提取和实体链接的性能，尤其是与外部检索器一起使用时。

1.  **Scispacy表现最佳：** 在我们的实验中，Scispacy在实体链接任务上优于所有基于LLM的方法。在生物医学文本处理方面，Scispacy依然是一款强大的工具。与需要良好GPU进行快速推理的LLM相比，它还需要较少的计算资源。相比之下，Scispacy只需要一台好的CPU。

1.  **优化机会：** 我们目前基于LLM的实体链接管道实现相当基础，存在较大的改进空间。一些可以优化的领域包括检索的选择以及检索逻辑本身。使用更多数据对LLM进行微调也能进一步提升其实体提取性能。

# 局限性

到目前为止，我们的实验存在一些局限性。

1.  **一个实体对应多个MeSH ID：** 在我们的数据集中，每个文档中的一些实体可能会与多个MeSH ID关联。在我们测试集中的100个文档中，共有968个实体，其中有15个案例（1.54%）存在这种情况。在Scispacy评估中，以及在所有我们使用外部知识库链接器（BM-25检索器）进行实体提取的LLM实验中，我们每个实体只链接一个MeSH概念。尽管Scispacy提供了为每个实体链接多个MeSH ID的可能性，但我们选择不使用这个功能，以确保与LLM实验的公平比较。扩展功能以支持链接多个概念也是一个有趣的补充。

1.  **不在知识库中的MeSH ID：** 在测试数据集中，有一些实体的MeSH ID不在我们的知识库中。具体而言，64个实体（占6.6%）拥有一个不在我们知识库中的MeSH ID。这个限制出在检索器端，可以通过更新知识库来解决。

1.  **缺乏MeSH ID的实体：** 同样，另有1.65%的实体（968个中的16个）无法映射到MeSH ID。在所有使用外部KB链接器进行实体提取的LLM实验中，我们目前无法确定一个实体是否没有MeSH ID。

# **参考文献**

> 我已经将本文中提到的所有论文和资源汇总在此。如果我遗漏了什么，请告诉我，我将添加它们！

[1] Bodenreider O. (2004). 统一医学语言系统(UMLS)：整合生物医学术语。*核酸研究*, *32*(数据库专刊)，D267–D270\. [https://doi.org/10.1093/nar/gkh061](https://doi.org/10.1093/nar/gkh061)

[2] [https://www.nlm.nih.gov/healthit/snomedct/index.html](https://www.nlm.nih.gov/healthit/snomedct/index.html)

[3] [https://www.nlm.nih.gov/mesh/meshhome.html](https://www.nlm.nih.gov/mesh/meshhome.html)

[4] Wei CH, Peng Y, Leaman R, Davis AP, Mattingly CJ, Li J, Wiegers TC, Lu Z. BioCreative V化学-疾病关系(CDR)任务概述，发表于《第五届BioCreative挑战评估工作坊论文集》，第154–166页，2015年

[5] Li J, Sun Y, Johnson RJ, Sciaky D, Wei CH, Leaman R, Davis AP, Mattingly CJ, Wiegers TC, Lu Z. 在生物医学文献中注释化学品、疾病及其相互作用，发表于《第五届BioCreative挑战评估工作坊论文集》，第173–182页，2015年

[6] Leaman R, Dogan RI, Lu Z. DNorm：通过成对学习排序进行疾病名称规范化，生物信息学 29(22):2909–17，2013年

[7] Leaman R, Wei CH, Lu Z. tmChem：一种高效的化学命名实体识别与规范化方法。J Cheminform, 7:S3, 2015年

[8] Li, J., Sun, Y., Johnson, R. J., Sciaky, D., Wei, C. H., Leaman, R., Davis, A. P., Mattingly, C. J., Wiegers, T. C., & Lu, Z. (2016). BioCreative V CDR任务语料库：化学-疾病关系抽取资源。*数据库：生物数据库与注释期刊*, *2016*, baw068\. [https://doi.org/10.1093/database/baw068](https://doi.org/10.1093/database/baw068)

[9] Jiang, A. Q., Sablayrolles, A., Mensch, A., Bamford, C., Chaplot, D. S., Casas, D. D. L., … & Sayed, W. E. (2023). Mistral 7B. *arXiv预印本arXiv:2310.06825*。

[10] Neumann, M., King, D., Beltagy, I., & Ammar, W. (2019年8月). ScispaCy：生物医学自然语言处理的快速且稳健的模型。发表于《第18届BioNLP研讨会及共享任务论文集》 (第319–327页)。

[11] [https://ai2-s2-scispacy.s3-us-west-2.amazonaws.com/data/kbs/2020-10-09/mesh_2020.jsonl](https://ai2-s2-scispacy.s3-us-west-2.amazonaws.com/data/kbs/2020-10-09/mesh_2020.jsonl)

[12] Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., … & Kiela, D. (2020). 用于知识密集型自然语言处理任务的检索增强生成。*神经信息处理系统进展*，*33*，9459–9474。

[13] Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P. (2024). 迷失在其中：语言模型如何使用长上下文。*计算语言学协会会刊*，*12*。

[14] Dettmers, T., Pagnoni, A., Holtzman, A., & Zettlemoyer, L. (2024). Qlora: 高效的量化大语言模型微调。*神经信息处理系统进展*，*36*。

[15] [https://s3-us-west-2.amazonaws.com/ai2-s2-scispacy/releases/v0.4.0/en_ner_bc5cdr_md-0.4.0.tar.gz](https://s3-us-west-2.amazonaws.com/ai2-s2-scispacy/releases/v0.5.4/en_ner_bc5cdr_md-0.5.4.tar.gz)
