# 构建一个能够写入Google Docs的研究助手（第二部分）

> 原文：[https://towardsdatascience.com/building-a-research-assistant-that-can-write-to-google-docs-part-2-ac9dcacff4ff?source=collection_archive---------9-----------------------#2024-11-20](https://towardsdatascience.com/building-a-research-assistant-that-can-write-to-google-docs-part-2-ac9dcacff4ff?source=collection_archive---------9-----------------------#2024-11-20)

![](../Images/84311f1635442fb3094ebc3095549255.png)

Dalle-3对“一个AI助手将文件随风抛向清澈蓝海”的解读。图像由作者生成。

## 可能对你做作业有所帮助的工具

[](https://medium.com/@rmartinshort?source=post_page---byline--ac9dcacff4ff--------------------------------)[![Robert Martin-Short](../Images/e3910071b72a914255b185b850579a5a.png)](https://medium.com/@rmartinshort?source=post_page---byline--ac9dcacff4ff--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ac9dcacff4ff--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ac9dcacff4ff--------------------------------) [Robert Martin-Short](https://medium.com/@rmartinshort?source=post_page---byline--ac9dcacff4ff--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ac9dcacff4ff--------------------------------) ·12分钟阅读·2024年11月20日

--

***本文是两部分系列中的第二部分，我们使用LangGraph和Tavily构建了一个简单的研究助手，该助手能够撰写和修改短篇文章。为了跟踪它生成的计划、文章和评论，我们为其增加了编程创建和编辑Google Docs文档的功能。在第一篇文章中，我们构建了该助手。现在，我们将构建文档连接功能。你可以在[这里](https://github.com/rmartinshort/research_assist)找到所有相关代码。***

在[本系列的第一部分](/building-a-research-agent-that-can-write-to-google-docs-part-1-4b49ea05a292)中，我们讨论了代理，并使用LangGraph和Tavily的工具构建了一个最小化的代理，能够进行研究、写作、审阅和修订短篇文章。这对于展示很有用，但如果我们实际上希望在笔记本外阅读这些文章怎么办？或者，更雄心勃勃的目标是，如何将这个代理做成一个工具，可能对某个学习新主题的人有实际帮助？这有潜力成为一个完整的项目，但在这里，我将专注于其中一个有趣的元素——赋予我们的系统上传文章到Google Docs的能力。回想一下，我们还保存了代理在得出最终答案过程中所采取的中间步骤——或许也值得对这些步骤进行记录。

# **1. 最小可行产品**

针对一个问题或话题提示，我们的代理会生成一长串输出。至少，我们希望将其导入到一个 Google 文档中，并附上标题和时间戳。我们还希望能够控制该文档写入 Google Drive 的位置，并最好能够创建和命名文件夹，以便将我们的文章进行有逻辑地存储。我们这里不会过多关注格式化——尽管使用 Google Docs API 完全可以实现——我们更关心的是将文本放入一个人们实际会阅读的地方。格式化可以稍后进行，或者直接留给读者的个人偏好。

一旦我们设置了文档连接，接下来有很多更高级的操作可以做——比如使用 LLM 来重新格式化它们以用于演示，并将其上传到 Google Slides 演示文稿中？或者抓取某个参考数据源并将其上传到 Google Sheets？我们可以将这些功能作为工具添加到代理的控制流程中，让它来决定执行什么。显然这里有很多选择，但最好还是从简单的开始。

# 2\. 连接到 Google Drive

让我们首先写一些代码，以基本方式与 Google Docs 进行交互。首先需要进行一些设置：您需要一个 Google Cloud 账户和一个新的项目。然后，您需要启用 Google Drive 和 Google Docs API。为了为此项目创建一些凭证，我们将使用[服务账户](https://cloud.google.com/iam/docs/service-account-overview#:~:text=A%20service%20account%20is%20a,is%20unique%20to%20the%20account.)，可以按照[这里](https://developers.google.com/zero-touch/guides/customer/quickstart/python-service-account)的说明进行设置。此过程将生成一个 `.json` 文件格式的私钥，您将其存储在本地计算机上。接下来，最好在您的 Google Drive 中为此项目创建一个“主文件夹”。完成后，您可以将您的服务账户添加到该文件夹并授予其写入权限。现在，您的服务账户就具备了以编程方式与该文件夹内容进行交互的权限。

```py
from google.oauth2 import service_account
from abc import ABC, abstractmethod
from googleapiclient.discovery import build
# path to your .json credentials file
from research_assist.gsuite.base.config import CREDENTIALS
from typing import Any

class GSuiteService(ABC):
    """
    An abstract base class for G Suite services.

    This class defines the structure for any G Suite service implementation,
    requiring subclasses to specify the scopes and service creation logic.

    Attributes:
        credential_path (str): The path to the credentials file.
        SCOPES (list): The scopes required for the service.
    """

    def __init__(self) -> None:
        """
        Initializes the GSuiteService with the credential path and scopes.
        """
        # The name of the file containing your credentials
        self.credential_path = CREDENTIALS
        self.SCOPES = self.get_scopes()

    @abstractmethod
    def get_scopes(self) -> list[str]:
        """
        Retrieves the scopes required for the G Suite service.

        Returns:
            list[str]: A list of scopes required for the service.
        """
        raise NotImplementedError("Subclasses must implement this method.")

    @abstractmethod
    def get_service(self, credentials: Any) -> Any:
        """
        Creates and returns the service object for the G Suite service.

        Args:
            credentials (Any): The credentials to use for the service.

        Returns:
            Any: The service object for the G Suite service.
        """
        raise NotImplementedError("Subclasses must implement this method.")

    def build(self) -> Any:
        """
        Builds the G Suite service using the provided credentials.

        Returns:
            Any: The constructed service object.
        """
        # Get credentials into the desired format
        creds = service_account.Credentials.from_service_account_file(
            self.credential_path, scopes=self.SCOPES
        )

        service = self.get_service(creds)
        return service

class GoogleDriveService(GSuiteService):
    """
    A service class for interacting with Google Drive API.

    Inherits from GSuiteService and implements the methods to retrieve
    the required scopes and create the Google Drive service.

    Methods:
        get_scopes: Returns the scopes required for Google Drive API.
        get_service: Creates and returns the Google Drive service object.
    """

    def get_scopes(self) -> list[str]:
        """
        Retrieves the scopes required for the Google Drive service.

        Returns:
            list[str]: A list containing the required scopes for Google Drive API.
        """
        SCOPES = ["https://www.googleapis.com/auth/drive"]
        return SCOPES

    def get_service(self, creds: Any) -> Any:
        """
        Creates and returns the Google Drive service object.

        Args:
            creds (Any): The credentials to use for the Google Drive service.

        Returns:
            Any: The Google Drive service object.
        """
        return build("drive", "v3", credentials=creds, cache_discovery=False)
```

代码是这样设置的，因为未来我们可能需要使用许多 GSuite API（如 drive、docs、sheets、slides 等）。它们都会继承自 `GSuiteService`，并将它们的 `get_service` 和 `get_scopes` 方法重写为该 API 的特定细节。

一旦这一切设置完成，您就可以开始与 Google Drive 进行交互了。这是一篇很棒的[文章](https://medium.com/@matheodaly.md/using-google-drive-api-with-python-and-a-service-account-d6ae1f6456c2)，展示了几种主要的操作方法。

在我们的实现中，我们与 Google Drive 的交互方式是通过 `GoogleDriveHelper` 的方法，该方法在初始化时创建一个 `GoogleDriveService` 实例。我们首先为它提供我们的主文件夹的名称。

```py
from research_assist.gsuite.drive.GoogleDriveHelper import GoogleDriveHelper

master_folder_name = ai_assistant_research_projects
drive_helper = GoogleDriveHelper(f"{master_folder_name}")
```

假设我们想创建一个关于“旅行者”系列太空探测器的项目。我们可以通过在主文件夹中设置一个文件夹来进行组织：

```py
project_folder_id = drive_helper.create_new_folder("voyager")
```

这会创建一个文件夹并返回其ID，我们可以利用这个ID在文件夹中创建文档。这个项目可能有多个版本，所以我们也可以创建相关的子文件夹。

```py
version_folder_id = drive_helper.create_new_folder(
  "v1", 
  parent_folder_id=project_folder_id
)
```

现在我们准备创建一个空白文档，这也可以通过驱动服务来完成。

```py
final_report_id = drive_helper.create_basic_document(
    "final report", parent_folder_id=version_folder_id
)
```

在后台，驱动助手运行以下代码，它传递了一些元数据，指示我们要通过`googleapiclient.discovery.build`的创建方法来创建文档（即运行`GoogleDriveService().build()`时返回的内容）。

```py
document_metadata = {
            "name": document_name,
            "mimeType": "application/vnd.google-apps.document",
            "parents": [parent_folder_id],
}
# make the document
doc = (
  self.drive_service.files()
  .create(body=document_metadata, fields="id")
  execute()
)
doc_id = doc.get("id")
```

正如你可能想象的那样，Google Drive API有很多不同的功能和选项，我们在这里并没有涉及。到目前为止，我找到的最全面的Python封装库是[这个](https://github.com/iterative/PyDrive2)，如果你想进一步探索，这是一个很好的起点。

# **3\. 写入Google Docs**

现在我们已经创建了一个空白文档，接下来让我们用最终的文章填充它！这时`GoogleDocsService`和`GoogleDocsHelper`就派上用场了。`GoogleDocsService`与`GoogleDriveService`非常相似，也继承自我们在第二节中讨论过的`GSuiteService`。`GoogleDocsHelper`包含一些工具，可以将文本和图像写入Google Docs。它们现在非常基础，但对于这个项目来说已经足够了。

我们可以首先使用我们在第一部分中构建的代理来写一篇关于“旅行者”的文章。

```py
from research_assist.researcher.Agent import ResearchAgent, load_secrets
from langchain_openai import ChatOpenAI
from tavily import TavilyClient

secrets = load_secrets()
model = ChatOpenAI(
    model="gpt-4o-mini", temperature=0, api_key=secrets["OPENAI_API_KEY"]
)
tavily = TavilyClient(api_key=secrets["TAVILY_API_KEY"])

agent = ResearchAgent(llm, tavily)
agent.run_task(
    task_description="The Voyager missions: What did we learn?", 
    max_revisions=3

)
```

记住，代理的各种输出会存储在它的内存中，可以通过以下方式进行探索。在代码中，你可以看到我们在这里使用“user_id = 1”作为占位符，但在有多个用户的应用程序中，这个ID将允许模型访问正确的内存存储。

```py
memories = agent.in_memory_store.search(("1", "memories"))
```

最终报告的文本可以在这里找到，关键名称与我们在第一部分中讨论过的AgentState相对应。它位于索引-3的位置，因为它后面跟着一个调用编辑器节点（它返回了“是”）和接受节点，当前接受节点只是返回“True”。接受节点可以很容易地扩展，实际上将这个报告自动写入文档。

```py
final_essay = agent.in_memory_store.search(("1", "memories"))[-3].dict()["value"][
    "memory"
]["write"]["draft"]
```

让我们看看如何将这些文本放入Google文档中。回想一下，在第二节中我们用`doc_id`创建了一个空白文档。`GoogleDocsHelper`有两个基本方法可以做到这一点。第一个方法用于提供标题和基本元数据，基本上就是文档编写的日期和时间。第二个方法则是将文本粘贴到文档中。

代码展示了如何控制文本的位置和格式，可能会有些混淆。我们定义了一个请求列表，包含像`insertText`这样的指令。当我们插入文本时，我们需要提供开始插入的索引，它对应于文档中的某个位置。

```py
def create_doc_template_header(self, document_title: str, doc_id: str) -> int:
     """
     Creates a header template for the document, 
     including the title and the current date.

     Args:
         document_title (str): The title of the document.
         doc_id (str): The ID of the document to update.

     Returns:
         int: The index after the inserted header.
     """
     # add template header
     title = f"""
     {document_title}
     """
     template = f"""
     Written on {datetime.date.today()} at {datetime.datetime.now().strftime("%H:%M:%S")}
     """
     requests: List[Dict[str, Any]] = [
            {
                "insertText": {
                    "location": {
                        "index": 1,
                    },
                    "text": template,
                }
            },
            {
                "insertText": {
                    "location": {
                        "index": 1,
                    },
                    "text": title,
                }
            },
            {
                "updateParagraphStyle": {
                    "range": {
                        "startIndex": 1,
                        "endIndex": len(title),
                    },
                    "paragraphStyle": {
                        "namedStyleType": "TITLE",
                        "spaceAbove": {"magnitude": 1.0, "unit": "PT"},
                        "spaceBelow": {"magnitude": 1.0, "unit": "PT"},
                    },
                    "fields": "namedStyleType,spaceAbove,spaceBelow",
                }
            },
            {
                "updateParagraphStyle": {
                    "range": {
                        "startIndex": len(title) + 1,
                        "endIndex": len(title) + len(template),
                    },
                    "paragraphStyle": {
                        "namedStyleType": "SUBTITLE",
                        "spaceAbove": {"magnitude": 1.0, "unit": "PT"},
                        "spaceBelow": {"magnitude": 1.0, "unit": "PT"},
                    },
                    "fields": "namedStyleType,spaceAbove,spaceBelow",
                }
            },
        ]
     result = (
            self.docs_service.documents()
            .batchUpdate(documentId=doc_id, body={"requests": requests})
            .execute()
     )
     end_index = len(title) + len(template) + 1
     return end_index

def write_text_to_doc(self, start_index: int, text: str, doc_id: str) -> int:
     """
     Writes text to the document at the specified index.

     Args:
         start_index (int): The index at which to insert the text.
         text (str): The text to insert.
         doc_id (str): The ID of the document to update.

     Returns:
         int: The index after the inserted text.
     """
     end_index = start_index + len(text) + 1

     requests: List[Dict[str, Any]] = [
            {
                "insertText": {
                    "location": {
                        "index": start_index,
                    },
                    "text": text,
                }
            },
            {
                "updateParagraphStyle": {
                    "range": {
                        "startIndex": start_index,
                        "endIndex": start_index + len(text),
                    },
                    "paragraphStyle": {
                        "namedStyleType": "NORMAL_TEXT",
                        "spaceAbove": {"magnitude": 1.0, "unit": "PT"},
                        "spaceBelow": {"magnitude": 1.0, "unit": "PT"},
                    },
                    "fields": "namedStyleType,spaceAbove,spaceBelow",
                }
            },
        ]

     result = (
            self.docs_service.documents()
            .batchUpdate(documentId=doc_id, body={"requests": requests})
            .execute()
        )

     return end_index
```

你可以在[这里](https://developers.google.com/docs/api/concepts/structure#indexes)了解有关索引如何定义的更多信息。当有多个 `insertText` 调用时，似乎先写最后一段文本更容易——例如在下面的代码中，`template`（即应出现在标题下方的元数据）首先出现在索引 1 的列表中。然后我们在索引 1 写入 `title`。这导致 `title` 在文档中首先出现，而 `template` 出现在其下方。注意，我们还需要指定 `paragraphStyle` 块的 `startIndex` 和 `endIndex`，才能改变文本的格式。

上面代码中的两种方法都会返回当前文本块的结束索引，以便它可以用作后续要附加文本块的起始索引。如果你打算在文档的样式和格式上更加富有创意，这份[指南](https://developers.google.com/docs/api/concepts/document)可能会有所帮助。

现在我们已经看到了底层代码，我们可以调用它来将最终报告写入文档。

```py
from research_assist.gsuite.docs.GoogleDocsHelper import GoogleDocsHelper

docs_helper = GoogleDocsHelper()

# add the document title 
title_end_index = docs_helper.create_doc_template_header(
    "voyager final report", doc_id
)

# add the text
doc_end_index = docs_helper.write_text_to_doc(
    start_index=title_end_index, text=final_essay, doc_id=doc_id
)
```

太好了！现在我们有了所有文档工具，可以用来编辑、格式化并分享我们的代理生成的报告。有趣的是，代理将文本格式化为 markdown，而 Google Docs 支持这种格式，但我没能找到一种方法来让文档自动识别并将 markdown 转换为漂亮的标题和副标题。毫无疑问，一定有办法做到这一点，这样报告看起来会更漂亮。

在运行上面的代码后，文档应该会呈现如下所示的内容。

![](../Images/dd497a4dcc365990a44e1855caaf2128.png)

包含代理生成的最终报告的文档截图。图像由作者生成

# **4. 那么其他代理输出呢？**

我们应该能够将代理内存中存储的所有信息写入文档，这样我们就可以轻松浏览每个阶段的结果。一种稍微黑客一点的方式如下：

```py
memories = agent.in_memory_store.search(("1", "memories"))

# this is needed because we may call some nodes several times 
# and we want to keep track of this so that we can make new documents
# for each call
seen_keys = set()
iterations = defaultdict(int)

# folder id where we want to write the documents
folder_id = f"{folder_id}"

for m in memories:
    data = m.dict()["value"]["memory"]
    available_keys = data.keys()
    node_key = list(available_keys)[0]
    unique_node_key = node_key + "_00"
    if unique_node_key in seen_keys:
        iterations[node_key] += 1
        unique_node_key = unique_node_key.replace("_00", "") + "_{:02d}".format(
            iterations[node_key]
        )

    print("-" * 20)
    print("Creating doc {}".format(unique_node_key))

    # get the text
    text = data[node_key][list(data[node_key].keys())[0]]

    # the tavily research output is a list, so convert it to a string
    if isinstance(text, List):
        text = "\n\n".join(text)

    # if anything else is not a string (e.g. the output of the accept node)
    # convert it to a string
    if not isinstance(text, str):
        text = str(text)

    # create document
    report_id = drive_service.create_basic_document(
        unique_node_key, parent_folder_id=folder_id
    )

    # create header
    end_index = docs_helper.create_doc_template_header(unique_node_key, report_id)

    # fill document
    end_index = docs_helper.write_text_to_doc(
        start_index=end_index, text=text, doc_id=report_id
    )

    seen_keys.add(unique_node_key)
```

这将生成 7 个文档，下面我们将查看一些示例截图。

![](../Images/e354c6d90ddac665527ba3f8e7124e08.png)

运行上述代码的输出。图像由作者生成

初步计划概述了报告的结构。有趣的是，模型似乎更倾向于使用大量简短的章节，我认为这很合适，因为提示要求将报告做得简洁且易于普通读者消化。

![](../Images/3a7eb62fe4a38648c0866643ef27dede.png)![](../Images/9b48c7b1ec4952ca764da720e9929c35.png)

上述代码片段编写的初步计划和研究文档的部分截图。图像由作者生成。

在研究阶段，调用了 Tavily 搜索，并返回了与使用的查询相关的格式良好的小块文本。其中一些小块被截断，这使得文档不太易读，但它很好地展示了从研究节点到写作节点传递的信息类型。

在审阅阶段，我们得到了对文章第一版本的精彩批评。通常这些审阅的结构与初始计划类似，提出许多非常笼统的建议，如“考虑使用更具描述性的标题”或“这一部分可以扩展，增加更多例子”。如果我们比较审阅前后的实际报告，通常会看到结构上的微小变化以及每个部分中增加的一些细节。这是否真正提高了文本的质量值得商榷，但通过在几个例子中进行尝试，我相信它确实有所帮助。

![](../Images/a1dd4e1dc0e37f1c84a80d1535b14765.png)![](../Images/0184229a9da34e4de535a5b1c7c25d0b.png)

这是通过上述代码片段生成的部分审阅和编辑反馈文档的截图。图片由作者生成。

最后，我们得到了编辑对审阅后草稿的判断。我目前使用的提示使得编辑相当宽容，因此它通常会说一些类似这里所示的内容。通过调整一些提示，我们可以鼓励它在需要时将更多的报告发送回审阅。

这就是本文及本小系列的全部内容。感谢阅读，希望你能从中找到一些对你自己项目有用的内容。在让研究代理更加健壮、对其输出进行适当评估以及更好地与Docs（或其他GSuite API）集成方面，这里有很多潜在的扩展。如果你有任何其他酷炫的想法，请告诉我！

作者与本文讨论的任何工具没有关联。
