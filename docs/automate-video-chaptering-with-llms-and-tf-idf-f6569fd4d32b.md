# 使用LLM和TF-IDF自动化视频章节划分

> 原文：[https://towardsdatascience.com/automate-video-chaptering-with-llms-and-tf-idf-f6569fd4d32b?source=collection_archive---------3-----------------------#2024-09-09](https://towardsdatascience.com/automate-video-chaptering-with-llms-and-tf-idf-f6569fd4d32b?source=collection_archive---------3-----------------------#2024-09-09)

## 将原始转录文本转化为结构良好的文档

[](https://ya-lb.medium.com/?source=post_page---byline--f6569fd4d32b--------------------------------)[![Yann-Aël Le Borgne](../Images/acc1c8b32373d7f345064b89b51869fd.png)](https://ya-lb.medium.com/?source=post_page---byline--f6569fd4d32b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f6569fd4d32b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f6569fd4d32b--------------------------------) [Yann-Aël Le Borgne](https://ya-lb.medium.com/?source=post_page---byline--f6569fd4d32b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6569fd4d32b--------------------------------) ·12分钟阅读·2024年9月9日

--

![](../Images/32d60a6f1a504bcb2a692f7a8846cb0e.png)

图片来源：[Jakob Owens](https://unsplash.com/@jakobowens1?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

视频章节划分是将视频分割成不同章节的任务。除了像YouTube章节那样作为导航工具使用外，它还是一系列下游应用的核心，从信息检索（例如RAG语义切分）到引用或[摘要](https://medium.com/p/d2c1e04e7a7b)等。

在一个最近的项目中，我需要自动化这个任务，结果发现可用的选择非常有限，尤其是在开源领域。虽然一些专业工具或付费API提供了这样的服务，但我找不到任何提供足够健全和准确解决方案的库或教程。如果你知道有类似的资源，请在评论区分享！

如果你在想，为什么不直接将转录文本复制粘贴到大型语言模型（LLM）中，并要求其生成章节标题，答案是，这样做有两个原因不会有效。首先，LLM无法始终如一地保留时间戳信息，并将其与章节标题关联。其次，LLM在处理长篇转录文本时，往往会忽视重要部分。

因此，我最终设计了一个自定义工作流程，依赖LLM来处理不同的语言处理子任务（如文本格式化、段落结构、章节划分和标题生成），并使用[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)统计方法在段落结构化后将时间戳信息添加回来。

![](../Images/780f882c38449694c48c995a6382887c.png)

LLM和TF-IDF的结合使得能够高效地编辑和结构化原始转录文本，同时保留时间戳——在这个[HuggingFace空间](https://huggingface.co/spaces/Yannael/video-chaptering)查看演示。

最终的工作流程效果很好，通常会生成与YouTube推荐章节相同或增强版的章节。该工具还允许将格式不佳的转录文本导出为结构化文档，如下所示的[HuggingFace空间](https://huggingface.co/spaces/Yannael/video-chaptering)。

本文旨在解释其主要步骤，步骤如下图所示：

![](../Images/ec059b1795fb2245cf75e684608ae62c.png)

视频章节化的提议工作流程，从原始转录文本的获取到结构化的Markdown和Gradio应用程序。

工作流程的关键步骤在于将转录文本结构化为段落（步骤2），然后将这些段落归类为章节，最终生成目录（步骤4）。请注意，这两个步骤可能依赖于不同的LLM：对于简单的文本编辑和段落识别任务，可以使用快速且便宜的LLM，如LLama 3 8B；而生成目录则需要更为复杂的LLM，如GPT-4o-mini。期间，使用TF-IDF将时间戳信息添加回结构化的段落。

本文的其余部分将更详细地描述每个步骤。

查看随附的[Github仓库和Colab笔记本](https://github.com/Yannael/automatic-video-chaptering)，亲自探索一下吧！

# 1) 获取视频/音频转录文本

以[课程“MIT 6.S191：深度学习导论”](https://www.youtube.com/watch?v=ErnWZxJovaM)的第一讲为例（[IntroToDeepLearning.com](http://introtodeeplearning.com)），该课程由Alexander Amini和Ava Amini讲授**（[根据MIT许可协议授权](http://introtodeeplearning.com/)）**。

![](../Images/5c2f97d5dd2e28cf1b72c2562e1e38cf.png)

课程YouTube页面的截图。课程资料受MIT许可证保护。

请注意，视频描述中已提供章节信息。

![](../Images/01a6fbed9e9f9dff298272404cc8bd7e.png)

章节信息已在YouTube描述中提供

这为我们提供了一个基准，用于在本文后面对我们的章节化效果进行定性比较。

## *YouTube 转录 API*

对于YouTube视频，YouTube通常会自动生成转录文本。检索该转录文本的便捷方法是调用Python *youtube_transcript_api*库中的*get_transcript*方法。该方法将YouTube的*video_id*作为参数：

```py
# https://www.youtube.com/watch?v=ErnWZxJovaM
video_id = "ErnWZxJovaM" # MIT Introduction to Deep Learning - 2024

# Retrieve transcript with the youtube_transcript_api library
from youtube_transcript_api import YouTubeTranscriptApi
transcript = YouTubeTranscriptApi.get_transcript(video_id, languages=["en"])
```

这将返回转录文本，内容为文本和时间戳键值对的列表：

```py
[{'text': '[Music]', 'start': 1.17}, 
{'text': 'good afternoon everyone and welcome to',  'start': 10.28}, 
{'text': 'MIT sus1 191 my name is Alexander amini',  'start': 12.88}, 
{'text': "and I'll be one of your instructors for",  'start': 16.84},
...]
```

然而，转录文本的格式较差：缺乏标点符号并且包含拼写错误（如“MIT sus1 191”应为“MIT 6.S191”，或“amini”应为“Amini”）。

## 使用Whisper进行语音转文本

或者，可以使用语音转文本库从视频或音频文件中推断转录内容。我们推荐使用[*faster-whisper*](https://github.com/SYSTRAN/faster-whisper)，它是一个快速实现的最新开源[*whisper*](https://github.com/openai/whisper)模型。

模型有不同的大小，最精确的是‘large-v3’，它能够在T4 GPU上每分钟转录约15分钟的音频（Google Colab提供免费使用）。

```py
from faster_whisper import WhisperModel

# Load Whisper model
whisper_model = WhisperModel("large-v3",
                              device="cuda" if torch.cuda.is_available() else "cpu",
                              compute_type="float16",
                            )

# Call the Whisper transcribe function on the audio file
initial_prompt = "Use punctuation, like this."
segments, transcript_info = whisper_model.transcribe(audio_file,  initial_prompt=initial_prompt, language="en")
```

转录的结果以片段形式提供，可以很容易地转换为文本和时间戳的列表，就像使用*youtube_transcript_api*库一样。

提示：Whisper有时可能[不包括标点符号](https://github.com/openai/whisper/discussions/194)。可以通过提供包含标点符号的小句子来使用*initial_prompt*参数引导模型添加标点符号。

以下是使用whisper large-v3转录我们视频示例的一部分摘录：

```py
[{'start': 0.0, 'text': ' Good afternoon, everyone, and welcome to MIT Success 191.'},
 {'start': 15.28, 'text': " My name is Alexander Amini, and I'll be one of your instructors for the course this year"},
 {'start': 19.32, 'duration': 2.08, 'text': ' along with Ava.'}
...]
```

请注意，与YouTube转录相比，标点符号已经添加。然而，某些转录错误仍然存在（例如，‘MIT Success 191’而不是‘MIT 6.S191’）。

# 2）将转录内容结构化为段落

一旦转录可用，第二阶段就开始对转录内容进行编辑和段落结构化。

转录编辑指的是为了提高可读性所做的修改。例如，添加缺失的标点符号、修正语法错误、去除口头习惯等。

将内容结构化为段落也有助于提高可读性，另外，这也作为第四阶段识别章节的预处理步骤，因为章节将通过将段落组合在一起形成。

段落编辑和结构化可以通过单次操作完成，使用LLM。下面我们演示了该阶段的预期结果：

![](../Images/8c5df77d97bd440adef5a65f05b5309f.png)

左：原始转录。右：编辑和结构化后的转录。

这个任务并不需要非常复杂的LLM，因为它主要是对内容进行重述。在撰写本文时，使用例如GPT-4o-mini或Llama 3 8B，可以获得不错的结果，配合以下的系统提示：

> 你是一个有帮助的助手。
> 
> 你的任务是提高用户输入的可读性：如果需要，添加标点符号，去除口头习惯，并将文本结构化为以‘\n\n’分隔的段落。
> 
> 尽量保持用词忠实于原文。
> 
> 将你的回答放在<answer></answer>标签内。

我们依赖于[OpenAI兼容的聊天完成API](https://platform.openai.com/docs/guides/chat-completions)来调用LLM，消息的角色可以是‘system’、‘user’或‘assistant’。下面的代码演示了使用[Groq](https://console.groq.com/docs/text-chat)实例化LLM客户端，使用LLama 3 8B：

```py
# Connect to Groq with a Groq API key
llm_client = Groq(api_key=api_key)
model = "llama-8b-8192"

# Extract text from transcript
transcript_text = ' '.join([s['text'] for s in transcript])

# Call LLM
response = client.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": system_prompt
            },
            {
                "role": "user",
                "content": transcript_text
            }
        ],
        model=model,
        temperature=0,
        seed=42
    ) 
```

给定一段原始的‘transcript_text’作为输入，它将返回一个包含在<answer>标签中的编辑文本：

```py
response_content=response.choices[0].message.content

print(response_content)
"""
<answer>
Good afternoon, everyone, and welcome to MIT 6.S191\. My name is Alexander Amini, and I'll be one of your instructors for the course this year, along with Ava. We're really excited to welcome you to this incredible course.

This is a fast-paced and intense one-week course that we're about to go through together. We'll be covering the foundations of a rapidly changing field, and a field that has been revolutionizing many areas of science, mathematics, physics, and more.

Over the past decade, AI and deep learning have been rapidly advancing and solving problems that we didn't think were solvable in our lifetimes. Today, AI is solving problems beyond human performance, and each year, this lecture is getting harder and harder to teach because it's supposed to cover the foundations of the field.
</answer>
"""
```

接下来，我们从<answer>标签中提取编辑后的文本，将其划分为段落，并将结果结构化为一个包含段落编号和文本片段的JSON字典：

```py
import re
pattern = re.compile(r'<answer>(.*?)</answer>', re.DOTALL)
response_content_edited =  pattern.findall(response_content)
paragraphs = response_content_edited.strip().split('\n\n')
paragraphs_dict = [{'paragraph_number': i, 'paragraph_text': paragraph} for i, paragraph in enumerate(paragraphs)

print(paragraph_dict)

[{'paragraph_number': 0,
  'paragraph_text': "Good afternoon, everyone, and welcome to MIT 6.S191\. My name is Alexander Amini, and I'll be one of your instructors for the course this year, along with Ava. We're really excited to welcome you to this incredible course."},
 {'paragraph_number': 1,
  'paragraph_text': "This is a fast-paced and intense one-week course that we're about to go through together. We'll be covering the foundations of a rapidly changing field, and a field that has been revolutionizing many areas of science, mathematics, physics, and more."},
 {'paragraph_number': 2,
  'paragraph_text': "Over the past decade, AI and deep learning have been rapidly advancing and solving problems that we didn't think were solvable in our lifetimes. Today, AI is solving problems beyond human performance, and each year, this lecture is getting harder and harder to teach because it's supposed to cover the foundations of the field."}]
```

注意，输入不应过长，否则大型语言模型（LLM）可能会‘忘记’部分文本。对于较长的输入，转录本必须分块以提高可靠性。我们发现GPT-4o-mini可以处理最多5000个字符，而Llama 3 8B只能处理最多1500个字符。该笔记本提供了*transcript_to_paragraphs*函数，它负责将转录本分块。

# 3) 使用TF-IDF推断段落时间戳

转录本现在已经被结构化为一个编辑过的段落列表，但时间戳在此过程中丢失了。

第三阶段是通过推断原始转录中哪个片段最接近每个段落，来重新添加时间戳。

![](../Images/61fb2633ad48094b726132474c7d221f.png)

TF-IDF用于找到哪个原始转录片段（右侧）最能匹配编辑后的段落开头（左侧）。

对于这项任务，我们依赖于[TF-IDF度量](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)。TF-IDF代表**词频-逆文档频率**，是一种用于比较两段文本相似度的度量方法。该度量通过计算相似单词的数量来工作，且对出现频率较低的单词给予更高权重。

作为预处理步骤，我们调整转录片段和段落的起始位置，以使它们包含相同数量的单词。文本片段应足够长，以便段落开头能够成功匹配到一个唯一的转录片段。我们发现，使用50个单词在实际操作中效果较好。

```py
 num_words = 50

transcript_num_words = transform_text_segments(transcript, num_words=num_words)
paragraphs_start_text = [{"start": p['paragraph_number'], "text": p['paragraph_text']} for p in paragraphs]
paragraphs_num_words = transform_text_segments(paragraphs_start_text, num_words=num_words) 
```

接着，我们依赖*sklearn*库及其[TfidfVectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)和[cosine_similarity](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_similarity.html#cosine-similarity)函数来运行TF-IDF，并计算每个段落开头和转录片段之间的相似度。以下是用于找到第一个段落在转录片段中最佳匹配索引的代码示例。

```py
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

# Paragraph for which to find the timestamp
paragraph_i = 0

# Create a TF-IDF vectorizer
vectorizer = TfidfVectorizer().fit_transform(transcript_num_words + paragraphs_num_words)
# Get the TF-IDF vectors for the transcript and the excerpt
vectors = vectorizer.toarray()
# Extract the TF-IDF vector for the paragraph
paragraph_vector = vectors[len(transcript_num_words) + paragraph_i]

# Calculate the cosine similarity between the paragraph vector and each transcript chunk
similarities = cosine_similarity(vectors[:len(transcript_num_words)], paragraph_vector.reshape(1, -1))
# Find the index of the most similar chunk
best_match_index = int(np.argmax(similarities))
```

我们将该过程封装在*add_timestamps_to_paragraphs*函数中，该函数为段落添加时间戳，同时添加匹配的片段索引和文本：

```py
paragraphs = add_timestamps_to_paragraphs(transcript, paragraphs, num_words=50)

#Example of output for the first paragraph:
print(paragraphs[0])

{'paragraph_number': 0,
  'paragraph_text': "Good afternoon, everyone, and welcome to MIT 6.S191\. My name is Alexander Amini, and I'll be one of your instructors for the course this year, along with Ava. We're really excited to welcome you to this incredible course.",
  'matched_index': 1,
  'matched_text': 'good afternoon everyone and welcome to',
  'start_time': 10}
```

在上面的示例中，第一个段落（编号为0）被发现与从时间10秒（秒为单位）开始的转录片段编号1匹配。

# 4) 生成目录

目录通过将连续的段落分组为章节，并识别有意义的章节标题来生成。此任务主要由LLM执行，LLM被指示将由JSON段落列表组成的输入转换为包含JSON章节标题及其起始段落编号的输出：

```py
system_prompt_paragraphs_to_toc = """

You are a helpful assistant.

    You are given a transcript of a course in JSON format as a list of paragraphs, each containing 'paragraph_number' and 'paragraph_text' keys.

    Your task is to group consecutive paragraphs in chapters for the course and identify meaningful chapter titles.

    Here are the steps to follow:

1\. Read the transcript carefully to understand its general structure and the main topics covered.
2\. Look for clues that a new chapter is about to start. This could be a change of topic, a change of time or setting, the introduction of new themes or topics, or the speaker's explicit mention of a new part.
3\. For each chapter, keep track of the paragraph number that starts the chapter and identify a meaningful chapter title.
4\. Chapters should ideally be equally spaced throughout the transcript, and discuss a specific topic.

    Format your result in JSON, with a list dictionaries for chapters, with 'start_paragraph_number':integer and 'title':string as key:value.

    Example: 
    {"chapters": 
        [{"start_paragraph_number": 0, "title": "Introduction"}, 
         {"start_paragraph_number": 10, "title": "Chapter 1"}
        ]
    }
""" 
```

一个重要的元素是特别要求输出JSON格式，这样可以增加获得正确格式的JSON输出的机会，后续可以在Python中重新加载。

本任务使用GPT-4o-mini，因为它比OpenAI的GPT-4o更具成本效益，并且通常能提供良好的结果。指令通过‘system’角色提供，段落则通过‘user’角色以JSON格式提供。

```py
# Connect to OpenAI with an OpenAI API key
llm_client_get_toc = OpenAI(api_key=api_key)
model_get_toc = "gpt-4o-mini-2024-07-18"

# Dump JSON paragraphs as text
paragraphs_number_text = [{'paragraph_number': p['paragraph_number'], 'paragraph_text': p['paragraph_text']} for p in paragraphs]
paragraphs_json_dump = json.dumps(paragraphs_number_text)

# Call LLM
response = client_get_toc.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": system_prompt_paragraphs_to_toc
            },
            {
                "role": "user",
                "content": paragraphs_json_dump
            }
        ],
        model=model_get_toc,
        temperature=0,
        seed=42
    )
```

完成！调用返回了包含章节标题及起始段落编号的JSON格式列表：

```py
print(response)

{
  "chapters": [
    {
      "start_paragraph_number": 0,
      "title": "Introduction to the Course"
    },
    {
      "start_paragraph_number": 17,
      "title": "Foundations of Intelligence and Deep Learning"
    },
    {
      "start_paragraph_number": 24,
      "title": "Course Structure and Expectations"
    }
....
  ]
}
```

如同步骤2所示，LLM可能会处理长输入时出现问题，并且忽略输入的一部分。解决方案依然是将输入拆分成多个部分，在笔记本中通过*paragraphs_to_toc*函数和*chunk_size*参数实现。

# 5）输出结构化的章节

最后的阶段将段落和目录结合起来，创建一个结构化的JSON文件，其中包含章节，示例如下所示，见[附带的Github仓库](https://github.com/Yannael/automatic-video-chaptering/blob/master/tmp/ErnWZxJovaM/ErnWZxJovaM.json)。

我们在下图中展示了最终的章节划分（右）与YouTube描述中提供的基准章节划分（左）的对比：

![](../Images/3f23d10c1159cfcf70370ae8be3055be.png)

并排比较YouTube的基准章节划分（左）和我们的划分（右）

这个对比主要是定性的，因为没有‘标准答案’。总体来说，本文中描述的方法识别了相似的章节，但提供了一个稍微更精细的划分。对两种章节划分的手动检查表明，基准章节划分在课程信息上存在偏差，实际上课程是在9:37开始，而不是7:25。

另有一些章节划分的示例，见这个[HuggingFace空间](https://huggingface.co/spaces/Yannael/video-chaptering)。整个工作流最终作为Gradio应用程序捆绑在[附带的笔记本](https://colab.research.google.com/github/Yannael/automatic-video-chaptering/blob/master/video_chapter_generator.ipynb)中，使得你可以更轻松地在自己的视频上进行测试。

![](../Images/780f882c38449694c48c995a6382887c.png)

[Gradio应用程序](https://colab.research.google.com/github/Yannael/automatic-video-chaptering/blob/master/video_chapter_generator.ipynb)，它将不同的步骤捆绑在一起，并从原始转录文本输出一个结构良好的文档

# 为了进一步深入

+   [从文本分割到智能章节划分：

    [用于结构化视频转录的新基准](https://arxiv.org/pdf/2402.17633v1)

+   [现有转录总结解决方案的评审](https://www.notta.ai/en/blog/transcript-summarizer)

+   [https://paperswithcode.com/task/text-segmentation](https://paperswithcode.com/task/text-segmentation)

+   [https://en.wikipedia.org/wiki/Video_chapter](https://en.wikipedia.org/wiki/Video_chapter)

注意：

+   除非另有说明，所有图片均为作者提供

*喜欢这篇文章吗？分享你的想法，给予掌声，或* [*在 LinkedIn 上与我联系*](https://www.linkedin.com/in/yannaelb/) *。*
