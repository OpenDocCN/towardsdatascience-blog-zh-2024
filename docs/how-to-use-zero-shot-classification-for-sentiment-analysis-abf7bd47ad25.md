# 如何使用零样本分类进行情感分析

> 原文：[https://towardsdatascience.com/how-to-use-zero-shot-classification-for-sentiment-analysis-abf7bd47ad25?source=collection_archive---------5-----------------------#2024-01-30](https://towardsdatascience.com/how-to-use-zero-shot-classification-for-sentiment-analysis-abf7bd47ad25?source=collection_archive---------5-----------------------#2024-01-30)

## 通过零样本分类探索心理健康见解

[](https://medium.com/@akaba_51202?source=post_page---byline--abf7bd47ad25--------------------------------)[![Aminata Kaba](../Images/7c96699f24ce3d4c5346d6993db786e0.png)](https://medium.com/@akaba_51202?source=post_page---byline--abf7bd47ad25--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--abf7bd47ad25--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--abf7bd47ad25--------------------------------) [Aminata Kaba](https://medium.com/@akaba_51202?source=post_page---byline--abf7bd47ad25--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abf7bd47ad25--------------------------------) ·阅读9分钟·2024年1月30日

--

![](../Images/5c82732f630d9c80cd801aaa631045b0.png)

**艺术作品由** [**Vivian Peng**](https://medium.com/@create_self) **创作 — 获得授权转载**

情感分析是自然语言处理（NLP）中的强大工具，用于探索文本中的公众意见和情绪。在心理健康领域，它可以为个体的整体健康提供引人入胜的见解。作为洛克菲勒基金会的暑期数据科学助理，我[进行了一个研究项目](https://github.com/akaba09/redditmentalhealth)，使用NLP技术探索了COVID-19大流行前后Reddit上关于抑郁症的讨论。为了更好地理解与心理健康和抑郁症相关的性别禁忌，我选择分析男性和女性发表的帖子之间的区别。

**不同类型的情感分析**

传统上，情感分析将文本中表达的整体情绪分类为三类：积极、消极或中性。但如果您有兴趣以更细粒度的方式探索情绪，比如期待、恐惧、悲伤、愤怒等，该怎么办呢？

有一些方法可以使用参考词库的情感模型来做到这一点，比如[The NRC Emotion Lexicon](https://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm)，它将文本与八种基本情绪（愤怒、恐惧、期待、信任、惊讶、悲伤、喜悦和厌恶）联系起来。然而，这种分析的设置可能会很复杂，而且权衡可能得不偿失。

我发现零-shot 分类可以轻松地用来产生类似的结果。术语“零-shot”来自于一个概念，即模型可以在没有事先接触过标签的情况下对数据进行分类。这消除了创建训练数据集的需求，而训练数据集通常需要耗费大量时间和资源。模型利用其对单词、短语和概念之间关系的普遍理解，将它们分配到不同的类别中。

我通过将情绪作为标签来重新利用零-shot 分类模型进行情感分析，从而对期待、愤怒、厌恶、恐惧、快乐和信任进行分类。

在这篇文章中，我将分享如何通过零-shot 分类在 5 个简单步骤中快速开始情感分析。

像 HuggingFace 这样的平台简化了这些模型的实现。你可以探索不同的模型并测试结果，以找出最适合使用的模型：

1.  访问[https://huggingface.co](https://huggingface.co)

1.  点击“模型”选项卡，选择你感兴趣的 NLP 任务类型

1.  选择一个模型卡片，这将引导你进入模型界面

1.  输入一段文本字符串，看看模型如何表现

**以下是情感分析模型与零-shot 模型相比的几个示例。**

**情感分析**

这些模型将文本分类为消极、中立和积极三类。

![](../Images/6cf49e13881fd3230c333d3adb3bfe26.png)

你可以看到，这里对情感的细微差别把握非常有限，几乎没有太多解释空间。你可以通过[这里](https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment-latest?text=I+dont+understand+what+going+on+with+me.+I%27ve+been+thinking+too+hard+lately.)访问上述模型进行测试或运行。

这类模型最适用于当你想要大致了解情感倾向时——即文本是偏向积极还是消极。

**零-shot 分类**

这些模型通过将类别标签作为输入，将文本分类到你想要的任何类别中。由于我关注的是心理健康相关的文本，因此我包括了情绪作为标签，包括紧急、快乐、悲伤、疲劳和焦虑。

![](../Images/9ac737e7590ffd3927e007b4e363f3b2.png)

你可以看到，使用零-shot 分类模型，我们可以轻松地将文本分类为更全面的人类情感表现，而无需任何标注数据。模型可以通过为每个标签提供准确度分数，识别文本中的情感细微差异和变化。这在心理健康应用中非常有用，因为情绪通常是一个连续的谱系。

现在我已经确定零-shot 分类模型更适合我的需求，我将展示如何将该模型应用于数据集。

**零-shot 模型的实现**

以下是运行此示例所需的要求：

```py
torch>=1.9.0
transformers>=4.11.3 
datasets>=1.14.0 
tokenizers>=0.11.0
pandas
numpy
```

**步骤 1\. 导入所需的库**

在这个例子中，我使用了来自Hugging Face的[DeBERTa-v3-base-mnli-fever-anli](https://huggingface.co/MoritzLaurer/DeBERTa-v3-base-mnli-fever-anli)零样本分类器。

```py
 # load hugging face library and model

from transformers import pipeline
classifier = pipeline("zero-shot-classification", model="MoritzLaurer/DeBERTa-v3-base-mnli-fever-anli")

# load in pandas and numpy for data manipulation
import pandas as pd
import numpy as np
```

Pipeline是用于调用HuggingFace中预训练模型的函数。在这里，我传递了两个参数。你可以从模型卡中获取这些参数的值：

+   `task`：模型正在执行的任务类型，以字符串形式传递

+   `model`：你正在使用的模型名称，以字符串形式传递

**步骤2：读取你的数据**

你的数据可以是任何形式，只要有一个文本列，其中每一行包含一段文本字符串。为了跟随这个例子，你可以在这里读取[Reddit抑郁数据集](https://raw.githubusercontent.com/akaba09/redditmentalhealth/main/code/dep.csv)。该数据集是根据《公共领域捐赠与许可证v1.0》发布的。

```py
#reading in data 
df = pd.read_csv("https://raw.githubusercontent.com/akaba09/redditmentalhealth/main/code/dep.csv")
```

这是我们将使用的数据集的预览：

![](../Images/2a916a26aebba2abd0d0a8b28ecc940d.png)

**步骤3：创建一个类列表，用于预测情感**

这个列表将作为标签，供模型预测每一段文本。例如，这段文本是否在探索诸如愤怒或厌恶等情感？在这个例子中，我传递了一个情感标签的列表。你可以根据需要使用任意数量的标签。

```py
# Creating a list of emotions to use as labels
text_labels = ["anticipation", "anger", "disgust", "fear", "joy", "trust"]
```

**步骤4：首先在一段文本上运行模型预测**

首先在一段文本上运行模型，以了解模型返回的结果以及你希望如何根据你的数据集来调整它。

```py
# Sample piece of text
sample_text = "still have depression symptoms not as bad as they used to be in fact my therapist says im improving a lot but for the past years ive been stuck in this state of emotional numbness feeling disconnected from myself others and the world and time doesnt seem to be passing"

# Run the model on the sample text
classifier(sample_text, text_labels, multi_label = False)
```

**classifier**函数是HuggingFace中的Transformers库的一部分，用于调用你想使用的模型。在这个例子中，我们使用的是[“DeBERTa-V4-base-mnli-fever-anli”](https://huggingface.co/MoritzLaurer/DeBERTa-v3-base-mnli-fever-anli)，它接受三个位置参数：

+   第一个位置：以字符串格式表示的文本。这个变量可以取任何名称。在这个例子中，我将它命名为`sample_text`

+   第二个位置：你希望预测的标签列表。这个变量可以取任何名称。在这个例子中，我将它命名为`text_labels`

+   第三个位置：`multi_label`接受布尔值（true或false）。它决定每段文本是否可以有多个标签，还是每段文本只有一个标签。在这个例子中，我只关心每段文本有一个标签。

**这是你从示例文本中得到的输出：**

```py
#output 
# {'sequence': ' still have depression symptoms not as bad as they used to be in fact my therapist says im improving a lot but for the past years ive been stuck in this state of emotional numbness feeling disconnected from myself others and the world and time doesnt seem to be passing',
 # 'labels': ['anticipation', 'trust', 'joy', 'disgust', 'fear', 'anger'],
 # 'scores': [0.6039842963218689,
   #0.1163715273141861,
   #0.074860118329525,
   #0.07247171550989151,
   #0.0699692890048027,
   #0.0623430535197258]}
```

模型返回一个包含以下键和值的字典：

+   “sequence”：我们传入的文本片段

+   “labels”：模型预测的标签列表，按置信度降序排列。

+   “scores”：这会返回一个分数列表，表示模型对其预测的置信度，按降序排列。顺序与标签相关联，因此分数列表中的第一个元素与标签列表中的第一个元素相对应。在这个例子中，模型以0.604的置信度预测了“anticipation”（期待）情感。

**步骤 5：编写一个自定义函数，对整个数据集进行预测，并将标签作为数据框的一部分** 通过查看模型的字典输出结构，我可以编写一个自定义函数，将预测应用于我的所有数据。在这个例子中，我只关心保留每段文本的一个情感。这个函数将接收你的数据框，并返回一个新的数据框，其中包括两个新列——一个用于情感标签，另一个用于模型得分。

```py
def predict_sentiment(df, text_column, text_labels):

"""
    Predict the sentiment for a piece of text in a dataframe.

    Args:
        df (pandas.DataFrame): A DataFrame containing the text data to perform sentiment analysis on.
        text_column (str): The name of the column in the DataFrame that contains the text data.
        text_labels (list): A list of text labels for sentiment classification.

    Returns:
        pandas.DataFrame: A DataFrame containing the original data with additional columns for the predicted 
        sentiment label and corresponding score.

    Raises:
        ValueError: If the DataFrame (df) does not contain the specified text_column.

    Example:
        # Assuming df is a pandas DataFrame and text_labels is a list of text labels
        result = predict_sentiment(df, "text_column_name", text_labels)
    """

    result_list = []
    for index, row in df.iterrows():
        sequence_to_classify = row[text_column]
        result = classifier(sequence_to_classify, text_labels, multi_label = False)
        result['sentiment'] = result['labels'][0]
        result['score'] = result['scores'][0]
        result_list.append(result)
    result_df = pd.DataFrame(result_list)[['sequence','sentiment', 'score']]
    result_df = pd.merge(df, result_df, left_on = "text", right_on="sequence", how = "left")
    return result_df
```

这个函数遍历你的数据框，并解析每一行的字典结果。由于我只关心得分最高的情感，我通过索引result['labels'][0]来选择第一个标签。如果你想要获取前面三个情感，比如，你可以更新为一个范围result['labels'][0:3]。同样，如果你想要获取前三个得分，可以更新为范围result['scores'][0:3]。

**现在你可以在你的数据框上运行这个函数了！**

```py
# run prediction on df

results_df = predict_sentiment(df=df, text_column ="text", text_labels= text_labels)
```

在这里，我传入了三个参数：

+   `df`：你的数据框的名称

+   `text_column`：数据框中包含文本的列名。将此参数作为字符串传递。

+   `text_labels`：一个用于情感分类的文本标签列表

这是你返回的数据框的预览：

![](../Images/3acf5ad446d2589b6a5dfe4f3aefa0cb.png)

对于每一段文本，你都可以获得相关的情感及其模型得分。

**结论**

经典的情感分析模型探讨文本中的积极或消极情感，但当你想要在文本中探索更多细微的情感时，这种方法就显得有限了。

虽然你可以通过情感分析模型探索情感，但通常需要一个带标签的数据集并付出更多的实现努力。零样本分类模型是多用途的，它可以在没有带标签数据或先前训练的情况下，广泛地概括各种情感。

正如我们在这个例子中所探讨的，零样本模型接收一个标签列表，并返回每段文本的预测结果。我们传入了一个情感标签列表，结果相当不错，考虑到模型并没有针对这类情感数据进行训练。此类分类是分析心理健康相关文本的有价值工具，它帮助我们更全面地理解情感层面，进而提高对心理健康的支持。

*除非另有说明，所有图片均来自作者。*

Low, D. M., Rumker, L., Torous, J., Cecchi, G., Ghosh, S. S., & Talkar, T. (2020). Natural Language Processing Reveals Vulnerable Mental Health Support Groups and Heightened Health Anxiety on Reddit During COVID-19: Observational Study. *Journal of medical Internet research*, *22*(10), e22635.
