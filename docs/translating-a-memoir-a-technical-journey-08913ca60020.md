# 翻译回忆录：一段技术旅程

> 原文：[`towardsdatascience.com/translating-a-memoir-a-technical-journey-08913ca60020?source=collection_archive---------9-----------------------#2024-12-11`](https://towardsdatascience.com/translating-a-memoir-a-technical-journey-08913ca60020?source=collection_archive---------9-----------------------#2024-12-11)

## *利用 GPT-3.5 和非结构化 API 进行翻译*

[](https://medium.com/@ValeriaCortezVD?source=post_page---byline--08913ca60020--------------------------------)![Valeria Cortez](https://medium.com/@ValeriaCortezVD?source=post_page---byline--08913ca60020--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--08913ca60020--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--08913ca60020--------------------------------) [Valeria Cortez](https://medium.com/@ValeriaCortezVD?source=post_page---byline--08913ca60020--------------------------------)

·发表于[《Towards Data Science》](https://towardsdatascience.com/?source=post_page---byline--08913ca60020--------------------------------) ·阅读时长：6 分钟·2024 年 12 月 11 日

--

**这篇博客文章详细介绍了我如何利用 GPT 翻译我家族朋友的个人回忆录，使其能够被更广泛的读者群体阅读。** 具体来说，我使用了 GPT-3.5 进行翻译，并通过 Unstructured 的 API 实现高效的内容提取和格式化。

这本回忆录由我的家族朋友卡门·罗莎（Carmen Rosa）撰写，真挚地讲述了她在玻利维亚的成长经历以及在充满活力的 1970 年代，与一位伊朗男子在巴黎的浪漫旅程。原文为西班牙语，我们的目标是通过应用 LLM 技术，在保留她叙事精髓的同时，扩大其在英语读者中的影响力。

![](img/56510196d4a4728b280ce74035ebef0a.png)

《Un Destino Sorprendente》封面图，作者卡门·罗莎·维赫滕达尔（Carmen Rosa Wichtendahl）授权使用。

以下是您可以详细阅读翻译过程的内容，或者您可以[点击此处访问 Colab Notebook](https://colab.research.google.com/drive/1FxdtBO8iy1vnXG3VpjRIJ5yZEIEPHk9u#scrollTo=HDEwiI1j3mwH)。

# 翻译文档

我在翻译这本书时遵循了以下步骤：

1.  **导入图书数据：** 我通过 Unstructured API 将书籍从 Docx 文档中导入，并将其分为章节和段落。

1.  **翻译技术：** 我使用 GPT-3.5 翻译了每一章。在每个段落中，我提供了该章最新的三句翻译（如果有的话）。这种方法有两个目的：

+   ***风格一致性：*** 通过提供先前翻译的上下文，保持整个翻译过程中的风格一致性。

+   ***令牌限制：*** 限制一次处理的令牌数，以避免超出模型的上下文限制。

**3\. 将翻译导出为 Docx：** 我再次使用 Unstructured 的 API 将翻译内容保存为 Docx 格式。

# 技术实现

## 1\. 库

我们将从安装和导入必要的库开始。

```py
pip install --upgrade openai 
pip install python-dotenv
pip install unstructured
pip install python-docx
```

```py
import openai

# Unstructured
from unstructured.partition.docx import partition_docx
from unstructured.cleaners.core import group_broken_paragraphs

# Data and other libraries
import pandas as pd
import re
from typing import List, Dict
import os
from dotenv import load_dotenv
```

## 2\. 连接到 OpenAI 的 API

以下代码设置了用于 Python 项目的 OpenAI API 密钥。你需要将 API 密钥保存在 `.env` 文件中。

```py
import openai

# Specify the path to the .env file
dotenv_path = '/content/.env'

_ = load_dotenv(dotenv_path) # read local .env file
openai.api_key  = os.environ['OPENAI_API_KEY']
```

## 3\. 加载书籍

代码允许我们导入 Docx 格式的书籍，并将其分成单独的段落。

```py
elements = partition_docx(
    filename="/content/libro.docx", 
    paragraph_grouper=group_broken_paragraphs
)
```

以下代码返回 `elements` 中第 10 个索引的段落。

```py
print(elements[10])

# Returns: Destino sorprendente, es el título que la autora le puso ...
```

## 4\. 将书籍分组为标题和章节

下一步是创建章节列表。每个章节将作为一个字典，其中包含标题和段落列表。这个结构简化了逐章逐段翻译的过程。以下是这种格式的示例：

```py
[
  {"title": title 1, "content": [paragraph 1, paragraph 2, ..., paragraph n]},
  {"title": title 2, "content": [paragraph 1, paragraph 2, ..., paragraph n]},
  ...
  {"title": title n, "content": [paragraph 1, paragraph 2, ..., paragraph n]},
]
```

为了实现这一点，我们将创建一个名为 `group_by_chapter` 的函数。以下是涉及的关键步骤：

1.  **提取相关信息：** 我们可以通过调用 `element.category`*.* 来获取每个叙述文本和标题。这些是我们目前只关注翻译的类别。

1.  **识别叙述性标题：** 我们识别出某些标题应作为叙述文本的一部分。为此，我们假设斜体标题属于叙述段落的一部分。

```py
def group_by_chapter(elements: List) -> List[Dict]:
    chapters = []
    current_title = None

    for element in elements:

      text_style = element.metadata.emphasized_text_tags # checks if it is 'b' or 'i' and returns list
      unique_text_style = list(set(text_style)) if text_style is not None else None

      # we consider an element a title if it is a title category and the style is bold
      is_title = (element.category == "Title") & (unique_text_style == ['b'])

      # we consider an element a narrative content if it is a narrative text category or
      # if it is a title category, but it is italic or italic and bold
      is_narrative = (element.category == "NarrativeText") | (
          ((element.category == "Title") & (unique_text_style is None)) |
          ((element.category == "Title") & (unique_text_style == ['i'])) |
          ((element.category == "Title") & (unique_text_style == ['b', 'i']))
      )

      # for new titles
      if is_title:
        print(f"Adding title {element.text}")

        # Add previous chapter when a new one comes in, unless current title is None
        if current_title is not None:
          chapters.append(current_chapter)

        current_title = element.text
        current_chapter = {"title": current_title, "content": []}

      elif is_narrative:
        print(f"Adding Narrative {element.text}")
        current_chapter["content"].append(element.text)

      else:
        print(f'### No need to convert. Element type: {element.category}')

    return chapters
```

在下面的示例中，我们可以看到一个示例：

```py
book_chapters[2] 

# Returns 
{'title': 'Proemio',
 'content': [
    'La autobiografía es considerada ...',
    'Dentro de las artes literarias, ...',
    'Se encuentra más próxima a los, ...',
  ]
}
```

## 5\. 书籍翻译

为了翻译这本书，我们遵循以下步骤：

1.  **翻译章节标题：** 我们翻译每个章节的标题。

1.  **翻译段落：** 我们翻译每个段落，给模型提供最新的三个已翻译的句子作为上下文。

1.  **保存翻译：** 我们保存翻译后的标题和内容。

以下函数自动化了这个过程。

```py
def translate_book(book_chapters: List[Dict]) -> Dict:
  translated_book = []
  for chapter in book_chapters:
    print(f"Translating following chapter: {chapter['title']}.")
    translated_title = translate_title(chapter['title'])
    translated_chapter_content = translate_chapter(chapter['content'])
    translated_book.append({
        "title": translated_title,
        "content": translated_chapter_content
        })
  return translated_book
```

对于标题，我们要求 GPT 做一个简单的翻译，如下所示：

```py
def translate_title(title: str) -> str:
  response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages= [{
        "role": "system",
        "content": f"Translate the following book title into English:\n{title}"
        }]
  )
  return response.choices[0].message.content
```

为了翻译单个章节，我们提供相应的段落给模型。我们指导模型如下操作：

1.  **识别角色：** 我们告诉模型它是一本书的有用翻译者。

1.  **提供上下文：** 我们分享该章节中最新的三个已翻译句子。

1.  **请求翻译：** 我们要求模型翻译下一个段落。

在此过程中，函数将所有已翻译的段落合并为一个字符串。

```py
# Function to translate a chapter using OpenAI API
def translate_chapter(chapter_paragraphs: List[str]) -> str:
    translated_content = ""

    for i, paragraph in enumerate(chapter_paragraphs):

        print(f"Translating paragraph {i + 1} out of {len(chapter_paragraphs)}")

        # Builds the message dynamically based on whether there is previous translated content
        messages = [{
          "role": "system", 
          "content": "You are a helpful translator for a book."
        }]

        if translated_content:
            latest_content = get_last_three_sentences(translated_content)
            messages.append(
                {
                  "role": "system",
                  "content": f"This is the latest text from the book that you've translated from Spanish into English:\n{latest_content}"
                }
            )

        # Adds the user message for the current paragraph
        messages.append(
            {
              "role": "user", 
              "content": f"Translate the following text from the book into English:\n{paragraph}"
            }
        )

        # Calls the API
        response = client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=messages
        )

        # Extracts the translated content and appends it
        paragraph_translation = response.choices[0].message.content
        translated_content += paragraph_translation + '\n\n'

    return translated_content
```

最后，下面是获取最新三个句子的辅助函数。

```py
def get_last_three_sentences(paragraph: str) -> str:
    # Use regex to split the text into sentences
    sentences = re.split(r'(?<!\w\.\w.)(?<![A-Z][a-z]\.)(?<=\.|\?)\s', paragraph)

    # Get the last three sentences (or fewer if the paragraph has less than 3 sentences)
    last_three = sentences[-3:]

    # Join the sentences into a single string
    return ' '.join(last_three)
```

## 6\. 书籍导出

最后，我们将章节的字典传递给一个函数，该函数将每个标题添加为标题，并将每个内容添加为段落。在每个段落后，插入分页符以分隔章节。最终文档将本地保存为 Docx 文件。

```py
from docx import Document

def create_docx_from_chapters(chapters: Dict, output_filename: str) -> None:
    doc = Document()

    for chapter in chapters:
        # Add chapter title as Heading 1
        doc.add_heading(chapter['title'], level=1)

        # Add chapter content as normal text
        doc.add_paragraph(chapter['content'])

        # Add a page break after each chapter
        doc.add_page_break()

    # Save the document
    doc.save(output_filename)
```

# 限制

尽管使用 GPT 和 API 进行翻译快速高效，但与人工翻译相比，仍有一些关键限制：

+   **代词和指代错误：** GPT 确实在少数情况下误解了代词或指代，可能将某些行为或陈述错误地归因于叙事中的错误人物。人类翻译者可以更好地解决这些模糊性问题。

+   **文化背景：** GPT 未能准确捕捉一些微妙的文化参考和习语，这些是人类翻译者能够更精确地解读的。在这种情况下，几个独特于玻利维亚圣克鲁斯的俚语被保留在原语言中，未做额外的解释或上下文说明。

结合人工智能与人工审核可以平衡速度和质量，确保翻译既准确又真实。

# 结论

本项目展示了通过结合 GPT-3 和 Unstructured APIs 来翻译书籍的一种方法。通过自动化翻译过程，我们显著减少了所需的人工努力。尽管初始翻译输出可能需要进行一些小的人工修订，以精细化细微差别并确保最高质量，但这种方法为高效且有效的书籍翻译提供了坚实的基础。

如果您对如何改进此过程或翻译质量有任何反馈或建议，请随时在下面的评论中分享。

# 附录

+   链接到[Colab 笔记本](https://colab.research.google.com/drive/1FxdtBO8iy1vnXG3VpjRIJ5yZEIEPHk9u#scrollTo=XaX0tVYlP9NU)

+   链接到[原语言书籍（西班牙语）](https://docs.google.com/document/d/1slQQXHqSq4n3d4zCNg9KNF4cIq3RpY4Y/edit?usp=sharing&ouid=114182714261805101186&rtpof=true&sd=true)

+   链接到[翻译后的书籍（英文）](https://docs.google.com/document/d/1DGosQWbXsMlnFlgxha8UOyeX1OqxN2Lk/edit)
