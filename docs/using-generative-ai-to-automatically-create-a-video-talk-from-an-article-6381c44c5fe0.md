# 使用生成式AI从文章中自动创建视频讲座

> 原文：[https://towardsdatascience.com/using-generative-ai-to-automatically-create-a-video-talk-from-an-article-6381c44c5fe0?source=collection_archive---------2-----------------------#2024-09-22](https://towardsdatascience.com/using-generative-ai-to-automatically-create-a-video-talk-from-an-article-6381c44c5fe0?source=collection_archive---------2-----------------------#2024-09-22)

## 使用Gemini + 文本转语音 + MoviePy创建视频，以及这对生成式AI（GenAI）迅速变得有用的意义

[](https://lakshmanok.medium.com/?source=post_page---byline--6381c44c5fe0--------------------------------)[![Lak Lakshmanan](../Images/9faaaf72d600f592cbaf3e9089cbb913.png)](https://lakshmanok.medium.com/?source=post_page---byline--6381c44c5fe0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6381c44c5fe0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6381c44c5fe0--------------------------------) [Lak Lakshmanan](https://lakshmanok.medium.com/?source=post_page---byline--6381c44c5fe0--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6381c44c5fe0--------------------------------) ·10分钟阅读·2024年9月22日

--

像大多数人一样，我对[NotebookLM及其生成播客的能力](https://blog.google/technology/ai/notebooklm-audio-overviews/)感到震惊。然后，我开始思考：“他们是怎么做到的？我在哪里可以获得这种魔力？”复制这一过程会有多容易？

## 目标：从文章中创建视频讲座

我不想创建播客，但我常常希望能够从我的博客文章中生成幻灯片和视频讲座——有些人喜欢翻阅幻灯片，另一些人则喜欢观看视频，这将是一个很好的方式来满足他们的需求。在本文中，我将展示如何做到这一点。

本文的[完整代码](https://github.com/lakshmanok/lakblogs/blob/main/genai_seminar/create_lecture.ipynb)在GitHub上——如果你想和我一起操作，可以参考。目标是从[这篇文章](https://lakshmanok.medium.com/what-goes-into-bronze-silver-and-gold-layers-of-a-medallion-data-architecture-4b6fdfb405fc)创建视频：

视频是使用本文描述的代码自动创建的。视频由作者生成。

## 1. 初始化LLM

我将使用Google Gemini Flash，因为（a）它是目前最便宜的前沿LLM，（b）它是多模态的，能够读取和理解图像，（c）它支持受控生成，这意味着我们可以确保LLM的输出符合预期的结构。

```py
import pdfkit
import os
import google.generativeai as genai
from dotenv import load_dotenv

load_dotenv("../genai_agents/keys.env")
genai.configure(api_key=os.environ["GOOGLE_API_KEY"])
```

请注意，我使用的是 Google Generative AI，而不是 Google Cloud Vertex AI。这两个包是不同的。Google 的那个支持用于受控生成的 Pydantic 对象；而 Vertex AI 目前只支持 JSON。

## 2\. 获取文章的 PDF 文件

我使用 Python 下载了文章并保存为 PDF，然后将其上传到 Gemini 可以读取的临时存储位置：

```py
ARTICLE_URL = "https://lakshmanok.medium...."
pdfkit.from_url(ARTICLE_URL, "article.pdf")
pdf_file = genai.upload_file("article.pdf")
```

不幸的是，由于 Medium 的一些原因，pdfkit 无法获取文章中的图片（可能是因为它们是 webm 格式而不是 png 格式……）。所以，我的幻灯片将仅基于文章的文本，而不包含图片。

## 3\. 创建 JSON 格式的讲义

在这里，我需要的数据格式是每一页幻灯片都有一个标题、要点和一组讲义。整个讲座也应该有一个标题和归属。

```py
class Slide(BaseModel):
    title: str
    key_points: List[str]
    lecture_notes: str

class Lecture(BaseModel):
    slides: List[Slide]
    lecture_title: str
    based_on_article_by: str
```

让我们告诉 Gemini 我们希望它做什么：

```py
lecture_prompt = """
You are a university professor who needs to create a lecture to
a class of undergraduate students.

* Create a 10-slide lecture based on the following article.
* Each slide should contain the following information:
  - title: a single sentence that summarizes the main point
  - key_points: a list of between 2 and 5 bullet points. Use phrases, not full sentences.
  - lecture_notes: 3-10 sentences explaining the key points in easy-to-understand language. Expand on the points using other information from the article.
* Also, create a title for the lecture and attribute the original article's author.
"""
```

这个提示词相当简单——要求 Gemini 阅读文章，提取要点并创建讲义。

现在，调用模型，传入 PDF 文件，并要求它填充所需的结构：

```py
 model = genai.GenerativeModel(
    "gemini-1.5-flash-001",
    system_instruction=[lecture_prompt]
)
generation_config={
    "temperature": 0.7,
    "response_mime_type": "application/json",
    "response_schema": Lecture
}
response = model.generate_content(
    [pdf_file],
    generation_config=generation_config,
    stream=False
)
```

上述代码需要注意的几点：

+   我们将提示词作为系统提示传递，这样就不需要在每次输入时都传入新的提示词。

+   我们将期望的响应类型指定为 JSON，模式为 Pydantic 对象

+   我们将 PDF 文件发送给模型，并告诉它生成响应。我们会等待它完成（无需流式传输）。

结果是 JSON 格式，因此可以将其提取为一个 Python 对象：

```py
lecture = json.loads(response.text)
```

例如，这是第三张幻灯片的样子：

```py
{'key_points': [
    'Silver layer cleans, structures, and prepares data for self-service analytics.',
    'Data is denormalized and organized for easier use.',
    'Type 2 slowly changing dimensions are handled in this layer.',
    'Governance responsibility lies with the source team.'
  ],
 'lecture_notes': 'The silver layer takes data from the bronze layer and transforms it into a usable format for self-service analytics. This involves cleaning, structuring, and organizing the data. Type 2 slowly changing dimensions, which track changes over time, are also handled in this layer. The governance of the silver layer rests with the source team, which is typically the data engineering team responsible for the source system.',
 'title': 'The Silver Layer: Data Transformation and Preparation'
}
```

## 4\. 转换为 PowerPoint

我们可以使用 Python 包 pptx 创建带有讲义和要点的演示文稿。创建幻灯片的代码如下：

```py
for slidejson in lecture['slides']:
    slide = presentation.slides.add_slide(presentation.slide_layouts[1])
    title = slide.shapes.title
    title.text = slidejson['title']
    # bullets
    textframe = slide.placeholders[1].text_frame
    for key_point in slidejson['key_points']:
        p = textframe.add_paragraph()
        p.text = key_point
        p.level = 1
    # notes
    notes_frame = slide.notes_slide.notes_text_frame
    notes_frame.text = slidejson['lecture_notes']
```

结果是一个 PowerPoint 演示文稿，格式如下：

![](../Images/532193d938ec39c67ae8e4b4c5ca81e8.png)

从要点和讲义生成的 PowerPoint 文件。截图由作者提供。

并不复杂，但如果你要做演讲，这绝对是一个很好的起点，可以用于编辑。

## 5\. 大声朗读笔记并保存音频

好吧，我们的灵感来源于播客，所以我们来看看如何只制作一个有人总结文章的音频。

我们已经有了讲义，所以我们来为每一张幻灯片创建音频文件。

这是用来将一些文本转化为 AI 语音并朗读出来的代码。我们将生成的音频保存为 mp3 文件：

```py
from google.cloud import texttospeech

def convert_text_audio(text, audio_mp3file):
    """Synthesizes speech from the input string of text."""
    tts_client = texttospeech.TextToSpeechClient()    
    input_text = texttospeech.SynthesisInput(text=text)

    voice = texttospeech.VoiceSelectionParams(
        language_code="en-US",
        name="en-US-Standard-C",
        ssml_gender=texttospeech.SsmlVoiceGender.FEMALE,
    )
    audio_config = texttospeech.AudioConfig(
        audio_encoding=texttospeech.AudioEncoding.MP3
    )

    response = tts_client.synthesize_speech(
        request={"input": input_text, "voice": voice, "audio_config": audio_config}
    )

    # The response's audio_content is binary.
    with open(audio_mp3file, "wb") as out:
        out.write(response.audio_content)
        print(f"{audio_mp3file} written.")
```

上述代码发生了什么？

+   我们使用的是 Google Cloud 的文字转语音 API

+   要求它使用标准的美国口音女性声音。如果你是在做播客，你可以在这里传入一个“说话者映射”，每个说话者对应一种声音。

+   然后我们将其作为输入文本，要求它生成音频。

+   将音频保存为 mp3 文件。请注意，这必须与音频编码匹配。

现在，遍历幻灯片并传入讲义，生成音频：

```py
for slideno, slide in enumerate(lecture['slides']):
        text = f"On to {slide['title']} \n"
        text += slide['lecture_notes'] + "\n\n"
        filename = os.path.join(outdir, f"audio_{slideno+1:02}.mp3")
        convert_text_audio(text, filename)
        filenames.append(filename)
```

结果是一堆音频文件。你可以使用 pydub 将它们连接在一起：

```py
combined = pydub.AudioSegment.empty()
for audio_file in audio_files:
    audio = pydub.AudioSegment.from_file(audio_file)
    combined += audio
    # pause for 4 seconds
    silence = pydub.AudioSegment.silent(duration=4000)
    combined += silence
combined.export("lecture.wav", format="wav")
```

但结果是我并不需要这么做。每张幻灯片的单独音频文件，正是我需要用来创建视频的内容。当然，对于播客，你会希望有一个单一的mp3或wav文件。

## 6\. 创建幻灯片图像

有点烦人的是，使用Python将PowerPoint幻灯片渲染为图像没有简单的办法。你需要一台安装了Office软件的机器来完成这个任务——这不是一种容易自动化的操作。也许我应该使用Google Slides…无论如何，渲染图像的简单方法是使用Python图像库（PIL）：

```py
def text_to_image(output_path, title, keypoints):
    image = Image.new("RGB", (1000, 750), "black")
    draw = ImageDraw.Draw(image)
    title_font = ImageFont.truetype("Coval-Black.ttf", size=42)
    draw.multiline_text((10, 25), wrap(title, 50), font=title_font)
    text_font = ImageFont.truetype("Coval-Light.ttf", size=36)
    for ptno, keypoint in enumerate(keypoints):
        draw.multiline_text((10, (ptno+2)*100), wrap(keypoint, 60), font=text_font) 
    image.save(output_path)
```

结果图像效果不算很好，但还算能用（你可以看出来，现在没有人支付我写生产级代码了）：

![](../Images/417e2c0e11b84caacc8069f9a4e922d8.png)

与音频片段一起使用的图像是这样的。图像由作者生成。

## 7\. 创建视频

现在我们已经有了一组音频文件和图像文件，可以使用Python包moviepy来创建一个视频片段：

```py
clips = []
for slide, audio in zip(slide_files, audio_files):
    audio_clip = AudioFileClip(f"article_audio/{audio}")
    slide_clip = ImageClip(f"article_slides/{slide}").set_duration(audio_clip.duration)
    slide_clip = slide_clip.set_audio(audio_clip)
    clips.append(slide_clip)
full_video = concatenate_videoclips(clips)
```

现在我们可以写出来了：

```py
full_video.write_videofile("lecture.mp4", fps=24, codec="mpeg4", 
                           temp_audiofile='temp-audio.mp4', remove_temp=True)
```

最终结果？我们从article.pdf自动生成了四个产物：

```py
lecture.json  lecture.mp4  lecture.pptx  lecture.wav
```

包括：

+   一个包含关键点、讲义等内容的JSON文件

+   一个可以修改的PowerPoint文件。幻灯片上有关键点，幻灯片的注释部分则包含“讲义”内容。

+   一个由AI语音朗读讲义的音频文件

+   一部mp4电影（我上传到YouTube）包含音频和图像。这就是我打算创建的视频讲座。

很酷吧？

## 8\. 这对软件未来的意义

我们作为一个社区，正在探索这项非常酷的技术（生成式AI）可以用于什么。显然，你可以用它来创建内容，但它创建的内容适合用于头脑风暴，而不能直接使用。三年的技术进步并没有解决GenAI生成空洞内容和不成熟代码的问题。

这带我们进入了GenAI所打开的一些辅助功能。而这些功能非常有用。本文展示了GenAI的四个功能。

**(1) 将非结构化数据转化为结构化数据**

《Attention》论文是为了解决翻译问题而写的，结果发现基于Transformer的模型在翻译方面表现非常优秀。我们不断发现这个技术的应用场景。但不仅仅是[日语到英语](https://mse238blog.stanford.edu/2017/08/jchoi8/machine-learning-transforms-google-translate-overnight/)，还包括[Java 11到Java 17](https://digiday.com/media/how-amazons-genai-tool-for-developers-is-saving-4500-years-of-work-260-million-annually/)，[文本到SQL](https://paperswithcode.com/task/text-to-sql)，文本到语音，不同数据库方言之间的转换，……现在，还可以将文章转为音频脚本。事实证明，这就是利用GenAI创建播客、讲座、视频等内容的起点。

我所要做的就是提示LLM从文章中构建一系列幻灯片内容（关键点、标题等），它就做到了。它甚至以结构化格式返回数据，便于从计算机程序中使用。具体来说，*GenAI在将非结构化数据转化为结构化数据方面非常出色*。

**(2) 代码搜索和编程帮助如今变得更加出色**

另一个GenAI表现得非常出色的领域是动态适配代码示例。我并不是每天都写代码来创建演示文稿、文本转语音或电影剪辑。两年前，我可能会使用谷歌搜索，查看Stack Overflow页面，并手动调整代码。现在，谷歌搜索直接给我提供了可以直接使用的代码：

![](../Images/318fc9e541190e93ace8486d8dcd05f1.png)

谷歌搜索返回了适应我特定查询的代码示例。截图来自作者。

当然，如果我使用的是Python IDE（而不是Jupyter笔记本），我完全可以避免搜索步骤——我只需写一个注释，系统就会为我生成代码。这非常有帮助，并且加快了使用通用API的开发速度。

**(3) GenAI Web服务既强大又易于使用**

让我们不要忘记，我使用了谷歌云文本转语音服务将我的音频脚本转化为实际的音频文件。文本转语音本身就是一个生成式AI模型（也是翻译超级能力的另一个例子）。谷歌的TTS服务于[2018年推出](https://cloud.google.com/blog/products/ai-machine-learning/introducing-cloud-text-to-speech-powered-by-deepmind-wavenet-technology)（此后可能有所改进），是最早投入生产的生成式AI服务之一，并通过API提供使用。

在本文中，我使用了两个生成式AI模型——TTS和Gemini，它们作为Web服务提供。我所需要做的就是调用它们的API。

**(4) 提供最终用户定制化比以往任何时候都更加容易**

我没有这样做，但你可以稍微眯起眼睛，看到未来的方向。如果我将演示创建、音频创建和视频创建的代码封装成服务，那么我本可以让一个提示生成调用这些服务的函数调用。同时，还可以加上一个请求处理代理，允许你通过文本来更改幻灯片的外观或视频中朗读者的声音。

为你构建的软件添加开放式定制化变得极其容易。

## 总结

受到NotebookLM播客功能的启发，我开始构建一个应用程序，将我的文章转化为视频讲座。关键步骤是提示LLM从文章中生成幻灯片内容，另一个GenAI模型将音频脚本转化为音频文件，并利用现有的Python API将它们整合成一个视频。

本文展示了GenAI解锁的四项能力：各种类型的翻译、编程帮助、强大的Web服务和最终用户定制化。

我很喜欢能够轻松快速地从我的文章中创建视频讲座。但我更为兴奋的是，我们在手中拥有的这一新工具中不断发现的潜力。

## 进一步阅读

1.  本文的完整代码：[https://github.com/lakshmanok/lakblogs/blob/main/genai_seminar/create_lecture.ipynb](https://github.com/lakshmanok/lakblogs/blob/main/genai_seminar/create_lecture.ipynb)

1.  我将这篇文章转换为视频的源文章：[https://lakshmanok.medium.com/what-goes-into-bronze-silver-and-gold-layers-of-a-medallion-data-architecture-4b6fdfb405fc](https://lakshmanok.medium.com/what-goes-into-bronze-silver-and-gold-layers-of-a-medallion-data-architecture-4b6fdfb405fc)

1.  生成的视频：[https://youtu.be/jKzmj8-1Y9Q](https://youtu.be/jKzmj8-1Y9Q)

1.  结果发现[Sascha Heyer写了一篇关于如何使用GenAI生成播客的文章](https://medium.com/google-cloud/building-a-dynamic-podcast-generator-inspired-by-googles-notebooklm-and-illuminate-e585cfcd0af1)，这正是Notebook LM的使用场景。他的方法和我有些相似，区别在于没有视频，只有音频。在一个有趣的变化中，他使用了自己的声音作为播客的讲解者之一！

1.  当然，这里是用该视频中展示的技术创建的文章视频演讲。理想情况下，我们会从文章中提取代码片段和图片，但这只是一个开始……
