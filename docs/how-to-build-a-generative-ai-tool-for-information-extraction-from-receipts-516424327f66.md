# 如何构建一个用于从收据中提取信息的生成式 AI 工具

> 原文：[https://towardsdatascience.com/how-to-build-a-generative-ai-tool-for-information-extraction-from-receipts-516424327f66?source=collection_archive---------2-----------------------#2024-04-10](https://towardsdatascience.com/how-to-build-a-generative-ai-tool-for-information-extraction-from-receipts-516424327f66?source=collection_archive---------2-----------------------#2024-04-10)

![](../Images/486a5de0d5063d0a6db0c23a138ff29d.png)

DALLE-2 对“未来主义的工业文档扫描设施”的诠释

## 使用 LangChain 和 OpenAI 工具从存储在 Google Drive 中的收据图片中提取结构化信息

[](https://medium.com/@rmartinshort?source=post_page---byline--516424327f66--------------------------------)[![Robert Martin-Short](../Images/e3910071b72a914255b185b850579a5a.png)](https://medium.com/@rmartinshort?source=post_page---byline--516424327f66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--516424327f66--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--516424327f66--------------------------------) [Robert Martin-Short](https://medium.com/@rmartinshort?source=post_page---byline--516424327f66--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--516424327f66--------------------------------) ·阅读时间 15 分钟·2024年4月10日

--

> **本文详细介绍了如何使用开源 Python 包，例如 LangChain、pytesseract 和 PyPDF，结合 gpt-4-vision 和 gpt-3.5-turbo，从收据图片中识别并提取关键信息。生成的数据集可以用于“与收据对话”应用程序。完整代码请查看** [**这里**](https://github.com/rmartinshort/receiptchat/tree/main)**。**

纸质收据有各种风格和格式，代表了一个有趣的自动化信息提取目标。它们还提供了大量逐项列出的费用，如果将这些费用汇总到数据库中，将非常有助于那些希望比银行对账单提供的更详细地追踪支出的人。

如果你能拍一张收据的照片，上传到某个应用程序，然后提取其信息并将其附加到你的个人支出数据库中，接着可以用自然语言查询这些信息，那不是很酷吗？你可以像“我上次去宜家时买了什么？”或者“我在Safeway花最多钱买了哪些物品？”这样提问。这种系统也可能自然地扩展到公司财务和支出跟踪。在本文中，我们将构建一个简单的应用程序，处理这个过程的第一部分——即提取收据中的信息，准备存储到数据库中。我们的系统将监控一个Google Drive文件夹中新添加的收据，处理它们并将结果附加到.csv文件中。

# **1\. 背景和动机**

从技术角度讲，我们将进行一种自动化的信息提取，称为模板填充。我们有一个预定义的字段架构，旨在从收据中提取这些字段，任务是填写这些字段，或者在适当的地方留空。这里的一个主要问题是，收据图像或扫描中的信息是非结构化的，尽管光学字符识别（OCR）或PDF文本提取库在查找文本时可能做得相当好，但它们在保留文档中单词相对位置方面做得不够好，这可能会使得例如将某个项目的价格与其成本匹配变得困难。

传统上，这个问题通过模板匹配来解决，其中会创建一个预定义的文档几何模板，然后只在已知包含重要信息的区域进行提取。关于这个方法的一个很好的描述可以在[这里](https://aicha-fatrah.medium.com/how-to-extract-information-from-documents-template-matching-e0540ae79599)找到。然而，这个系统缺乏灵活性。如果添加了新的收据格式怎么办？

为了绕过这个问题，像[AWS Textract](https://docs.aws.amazon.com/textract/latest/dg/invoices-receipts.html)和[AWS Rekognition](https://aws.amazon.com/rekognition/image-features/?nc=sn&loc=3&dn=3&refid=f5358c45-1ee6-4a07-b08d-0f669e6cd865)这样的更高级服务使用了预训练的深度学习模型结合物体检测、边界框生成和命名实体识别（NER）。我实际上还没有在手头的问题上尝试过这些服务，但如果能进行尝试，比较它们与我们使用OpenAI的LLMs构建的结果将会非常有趣。

像gpt-3.5-turbo这样的“大型语言模型”（LLM）在从非结构化文本中进行信息提取和模板填充方面也表现出色，尤其是在给定一些示例后。这使得它们比模板匹配或微调更加灵活，因为添加一些新的收据格式示例比重新训练模型或构建新的几何模板要快得多且更便宜。

如果我们要在从收据中提取的文本上使用gpt-3.5-turbo，那么问题就是如何构建它可以学习的示例？当然我们可以手动完成，但这样做的扩展性不好。这里我们将探索使用gpt-4-vision的选项。这个版本的gpt-4可以处理包含图像的对话，并且[特别擅长描述图像内容](https://medium.com/@nageshmashette32/gpt4-vision-and-its-alternatives-6ed9d39508cd)。给定一张收据的图像和我们想要提取的关键信息的描述，gpt-4-vision应该能够一举完成任务，前提是图像足够清晰。

为什么我们不单独使用gpt-4-vision来完成这个任务，而是放弃gpt-3.5-turbo或其他较小的LLM？从技术上讲，我们当然可以这样做，而且结果可能会更准确。但gpt-4-vision非常昂贵，且API调用次数有限，因此该系统也无法扩展。或许在不远的未来，视觉LLM会成为从文档中提取信息这一领域的标准工具。

这篇文章的另一个动机是探索如何使用Langchain构建这个系统，Langchain是一个流行的开源LLM编排库。为了强制LLM返回结构化输出，需要进行提示工程，而Langchain有一些非常棒的工具可以做到这一点。我们还将尝试确保我们的系统构建得具有可扩展性，因为这只是可能成为更大“与收据对话”项目的第一部分。

简要的背景介绍完毕后，让我们开始写代码吧！在这里，我将使用Python3.9和Langchain 0.1.14，完整的细节可以在[仓库](https://github.com/rmartinshort/receiptchat/tree/main)中找到。

# **2. 连接到Google Drive**

我们需要一个方便的地方来存储我们的原始收据数据。Google Drive是一个选择，它提供了相对易于使用的Python API。为了捕捉收据，我使用了[GeniusScan](https://thegrizzlylabs.com/genius-scan/)应用，它可以将.pdf、.jpeg或其他文件类型从手机直接上传到Google Drive文件夹。该应用还进行一些有用的预处理，如自动裁剪文档，这有助于提取过程。

要设置Google Drive的API访问权限，您需要创建服务账户凭据，可以按照[这里](https://developers.google.com/drive/api/quickstart/python)的说明生成。作为参考，我在我的驱动器中创建了一个名为“receiptchat”的文件夹，并设置了一个密钥对，使其能够读取该文件夹中的数据。

以下代码可以用来设置一个驱动服务对象，它提供了访问各种方法来查询Google Drive的功能：

```py
import os
from googleapiclient.discovery import build
from oauth2client.service_account import ServiceAccountCredentials

class GoogleDriveService:

    SCOPES = ["https://www.googleapis.com/auth/drive"]

    def __init__(self):
        # the directory where your credentials are stored
        base_path = os.path.dirname(os.path.dirname(os.path.dirname(__file__)))

        # The name of the file containing your credentials
        credential_path = os.path.join(base_path, "gdrive_credential.json")
        os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = credential_path

    def build(self):

        # Get credentials into the desired format
        creds = ServiceAccountCredentials.from_json_keyfile_name(
            os.getenv("GOOGLE_APPLICATION_CREDENTIALS"), self.SCOPES
        )  

        # Set up the Gdrive service object
        service = build("drive", "v3", credentials=creds, cache_discovery=False)

        return service
```

在我们的简单应用中，我们实际上只需要做两件事：列出驱动器文件夹中的所有文件，并下载其中一些文件。以下类处理了这一点：

```py
import io
from googleapiclient.errors import HttpError
from googleapiclient.http import MediaIoBaseDownload
import googleapiclient.discovery
from typing import List

class GoogleDriveLoader:

    # These are the types of files we want to download
    VALID_EXTENSIONS = [".pdf", ".jpeg"]

    def __init__(self, service: googleapiclient.discovery.Resource):

        self.service = service

    def search_for_files(self) -> List:
        """
        See https://developers.google.com/drive/api/guides/search-files#python
        """

        # This query searches for objects that are not folders and 
        # contain the valid extensions
        query = "mimeType != 'application/vnd.google-apps.folder' and ("
        for i, ext in enumerate(self.VALID_EXTENSIONS):
            if i == 0:
                query += "name contains '{}' ".format(ext)
            else:
                query += "or name contains '{}' ".format(ext)
        query = query.rstrip()
        query += ")"

        # create drive api client
        files = []
        page_token = None
        try:
            while True:
                response = (
                    self.service.files()
                    .list(
                        q=query,
                        spaces="drive",
                        fields="nextPageToken, files(id, name)",
                        pageToken=page_token,
                    )
                    .execute()
                )
                for file in response.get("files"):
                    # Process change
                    print(f'Found file: {file.get("name")}, {file.get("id")}')

                    file_id = file.get("id")
                    file_name = file.get("name")

                    files.append(
                        {
                            "id": file_id,
                            "name": file_name,
                        }
                    )

                page_token = response.get("nextPageToken", None)
                if page_token is None:
                    break

        except HttpError as error:
            print(f"An error occurred: {error}")
            files = None

        return files

    def download_file(self, real_file_id: str) -> bytes:
        """
        Downloads a single file
        """

        try:
            file_id = real_file_id
            request = self.service.files().get_media(fileId=file_id)
            file = io.BytesIO()
            downloader = MediaIoBaseDownload(file, request)
            done = False
            while done is False:
                status, done = downloader.next_chunk()
                print(f"Download {int(status.progress() * 100)}.")

        except HttpError as error:
            print(f"An error occurred: {error}")
            file = None

        return file.getvalue()
```

运行这个代码会得到以下结果：

```py
service = GoogleDriveService().build()
loader = GoogleDriveLoader(service)
all_files loader.search_for_files() #returns a list of unqiue file ids and names 
pdf_bytes = loader.download_file({some_id}) #returns bytes for that file
```

太好了！现在我们可以连接到 Google Drive，并将图像或 PDF 数据导入本地机器。接下来，我们必须处理这些数据并提取文本。

# 3\. 从 PDF 和图像中提取原始文本

有多个文档化良好的开源库可以从 PDF 和图像中提取原始文本。对于 PDF，我们将在这里使用 `PyPDF`，尽管为了更全面地了解类似的包，我推荐这篇[文章](/extracting-text-from-pdf-files-with-python-a-comprehensive-guide-9fc4003d517)。对于 JPEG 格式的图像，我们将使用 `pytesseract`，它是 `tesseract` OCR 引擎的包装器。关于如何安装它的说明可以在[这里](https://tesseract-ocr.github.io/tessdoc/Installation.html)找到。最后，我们还希望能够将 PDF 转换为 JPEG 格式。这可以通过 `pdf2image` 包实现。

`PyPDF` 和 `pytesseract` 都提供了从文档中提取文本的高级方法。它们也都提供了调优的选项。例如，`pytesseract` 可以提取文本和边界框（请参见[这里](https://pypi.org/project/pytesseract/)），如果我们将来想要为 LLM 提供有关其处理的收据文本格式的更多信息，这可能会很有用。`pdf2image` 提供了一种将 PDF 字节转换为 JPEG 图像的方法，这正是我们在这里想做的。为了将 JPEG 字节转换为可视化的图像，我们将使用 `PIL` 包。

```py
from abc import ABC, abstractmethod
from pdf2image import convert_from_bytes
import numpy as np
from PyPDF2 import PdfReader
from PIL import Image
import pytesseract
import io

DEFAULT_DPI = 50

class FileBytesToImage(ABC):

    @staticmethod
    @abstractmethod
    def convert_bytes_to_jpeg(file_bytes):
        raise NotImplementedError

    @staticmethod
    @abstractmethod
    def convert_bytes_to_text(file_bytes):
        raise NotImplementedError

class PDFBytesToImage(FileBytesToImage):

    @staticmethod
    def convert_bytes_to_jpeg(file_bytes, dpi=DEFAULT_DPI, return_array=False):
        jpeg_data = convert_from_bytes(file_bytes, fmt="jpeg", dpi=dpi)[0]
        if return_array:
            jpeg_data = np.asarray(jpeg_data)
        return jpeg_data

    @staticmethod
    def convert_bytes_to_text(file_bytes):
        pdf_data = PdfReader(
            stream=io.BytesIO(initial_bytes=file_bytes) 
        )
        # receipt data should only have one page
        page = pdf_data.pages[0]
        return page.extract_text()

class JpegBytesToImage(FileBytesToImage):

    @staticmethod
    def convert_bytes_to_jpeg(file_bytes, dpi=DEFAULT_DPI, return_array=False):
        jpeg_data = Image.open(io.BytesIO(file_bytes))
        if return_array:
            jpeg_data = np.array(jpeg_data)
        return jpeg_data

    @staticmethod
    def convert_bytes_to_text(file_bytes):
        jpeg_data = Image.open(io.BytesIO(file_bytes))
        text_data = pytesseract.image_to_string(image=jpeg_data, nice=1)
        return text_data
```

上面的代码使用了抽象基类的概念来提高可扩展性。假设我们未来想要添加对另一种文件类型的支持。如果我们编写相关的类并从`FileBytesToImage`继承，就必须在其中编写`convert_bytes_to_image`和`convert_bytes_to_text`方法。这可以减少我们在大型应用程序中引入错误的可能性。

代码可以如下使用：

```py
bytes_to_image = PDFBytesToImage()
image = PDFBytesToImage.convert_bytes_to_jpeg(pdf_bytes)
text = PDFBytesToImage.convert_bytes_to_jpeg(pdf_bytes)
```

![](../Images/9eeed45beddda0c8af8e2d5deaf3917c.png)

使用上述代码从 PDF 文档中提取的文本示例。由于收据包含个人身份信息（PII），这里我们仅使用上传到 Google Drive 的随机文档进行演示。图像由作者生成。

# 4\. 使用 gpt-4-vision 提取信息

现在，让我们使用 Langchain 提示 gpt-4-vision 从我们的收据中提取一些信息。我们可以通过使用 Langchain 对 Pydantic 的支持来创建一个输出模型。

```py
from langchain_core.pydantic_v1 import BaseModel, Field
from typing import List

class ReceiptItem(BaseModel):
    """Information about a single item on a reciept"""

    item_name: str = Field("The name of the purchased item")
    item_cost: str = Field("The cost of the item")

class ReceiptInformation(BaseModel):
    """Information extracted from a receipt"""

    vendor_name: str = Field(
        description="The name of the company who issued the reciept"
    )
    vendor_address: str = Field(
        description="The street address of the company who issued the reciept"
    )
    datetime: str = Field(
        description="The date and time that the receipt was printed in MM/DD/YY HH:MM format"
    )
    items_purchased: List[ReceiptItem] = Field(description="List of purchased items")
    subtotal: str = Field(description="The total cost before tax was applied")
    tax_rate: str = Field(description="The tax rate applied")
    total_after_tax: str = Field(description="The total cost after tax")
```

这非常强大，因为 Langchain 可以使用这个 Pydantic 模型为 LLM 构建格式说明，这些说明可以包含在提示中，强制它生成带有指定字段的 JSON 输出。添加新字段就像更新模型类一样简单。

接下来，让我们构建提示，这将只是静态的：

```py
from dataclasses import dataclass

@dataclass
class VisionReceiptExtractionPrompt:
    template: str = """
       You are an expert at information extraction from images of receipts.

       Given this of a receipt, extract the following information:
       - The name and address of the vendor
       - The names and costs of each of the items that were purchased
       - The date and time that the receipt was issued. This must be formatted like 'MM/DD/YY HH:MM'
       - The subtotal (i.e. the total cost before tax)
       - The tax rate
       - The total cost after tax

       Do not guess. If some information is missing just return "N/A" in the relevant field.
       If you determine that the image is not of a receipt, just set all the fields in the formatting instructions to "N/A". 

       You must obey the output format under all circumstances. Please follow the formatting instructions exactly.
       Do not return any additional comments or explanation. 
       """
```

现在，我们需要构建一个类，该类将接收图像，并将其与提示和格式说明一起发送给 LLM。

```py
from langchain.chains import TransformChain
from langchain_core.messages import HumanMessage
from langchain_core.runnables import chain
from langchain_core.output_parsers import JsonOutputParser
import base64
from langchain.callbacks import get_openai_callback

class VisionReceiptExtractionChain:

    def __init__(self, llm):
        self.llm = llm
        self.chain = self.set_up_chain()

    @staticmethod
    def load_image(path: dict) -> dict:
        """Load image and encode it as base64."""

        def encode_image(path):
            with open(path, "rb") as image_file:
                return base64.b64encode(image_file.read()).decode("utf-8")

        image_base64 = encode_image(path["image_path"])
        return {"image": image_base64}

    def set_up_chain(self):
        extraction_model = self.llm
        prompt = VisionReceiptExtractionPrompt()
        parser = JsonOutputParser(pydantic_object=ReceiptInformation)

        load_image_chain = TransformChain(
            input_variables=["image_path"],
            output_variables=["image"],
            transform=self.load_image,
        )

        # build custom chain that includes an image
        @chain
        def receipt_model_chain(inputs: dict) -> dict:
            """Invoke model"""
            msg = extraction_model.invoke(
                [
                    HumanMessage(
                        content=[
                            {"type": "text", "text": prompt.template},
                            {"type": "text", "text": parser.get_format_instructions()},
                            {
                                "type": "image_url",
                                "image_url": {
                                    "url": f"data:image/jpeg;base64,{inputs['image']}"
                                },
                            },
                        ]
                    )
                ]
            )
            return msg.content

        return load_image_chain | receipt_model_chain | JsonOutputParser()

    def run_and_count_tokens(self, input_dict: dict):
        with get_openai_callback() as cb:
            result = self.chain.invoke(input_dict)

        return result, cb
```

这里需要理解的主要方法是 `set_up_chain`，我们将逐步讲解这些步骤。这些步骤的灵感来自于这篇 [博客文章](https://medium.com/@bpothier/generating-structured-data-from-an-image-with-gpt-vision-and-langchain-34aaf3dcb215)。

+   初始化提示，在这种情况下，它只是一个包含一些通用指令的文本块。

+   从我们上面创建的 Pydantic 模型中创建一个 `JsonOutputParser`。这将把模型转换为一组格式化指令，能够添加到提示中。

+   创建一个 `TransformChain`，允许我们将自定义函数（在这种情况下是 `load_image` 函数）集成到整体链中。请注意，链将接受一个名为 `image_path` 的变量，并输出一个名为 `image` 的变量，这是一个表示图像的 base64 编码字符串。这是 gpt-4-vision 接受的格式之一。

+   据我所知，`ChatOpenAI` 目前还不支持同时发送文本和图像。因此，我们需要创建一个自定义链，将我们创建的 `ChatOpenAI` 实例与编码图像、提示和格式化指令一起调用。

请注意，我们还利用了 openai 回调函数来统计每次调用的令牌数和相关的支出。

为了运行这个，我们可以按以下步骤操作：

```py
from langchain_openai import ChatOpenAI
from tempfile import NamedTemporaryFile

model = ChatOpenAI(
  api_key={your open_ai api key},
  temperature=0, model="gpt-4-vision-preview", 
  max_tokens=1024
)

extractor = VisionReceiptExtractionChain(model)

# image from PDFBytesToImage.convert_bytes_to_jpeg()
prepared_data = {
    "image": image
}

with NamedTemporaryFile(suffix=".jpeg") as temp_file:
    prepared_data["image"].save(temp_file.name)
    res, cb = extractor.run_and_count_tokens(
        {"image_path": temp_file.name}
    )
```

给定我们上面的随机文档，结果如下所示：

```py
{'vendor_name': 'N/A',
 'vendor_address': 'N/A',
 'datetime': 'N/A',
 'items_purchased': [],
 'subtotal': 'N/A',
 'tax_rate': 'N/A',
 'total_after_tax': 'N/A'}
```

可能不太令人兴奋，但至少它按照正确的方式进行了结构化！当提供有效的收据时，这些字段将被填写，并且根据我对不同收据进行的一些测试评估，结果非常准确。

我们的回调函数如下所示：

```py
Tokens Used: 1170
Prompt Tokens: 1104
Completion Tokens: 66
Successful Requests: 1
Total Cost (USD): $0.01302
```

这是跟踪成本所必需的，因为在测试像 gpt-4 这样的模型时，成本可能会迅速增长。

# 5\. 使用 gpt-3.5-turbo 进行信息提取

假设我们已经使用第 4 部分中的步骤生成了一些示例并将它们保存为 json 文件。每个示例包含一些提取的文本和根据我们的 `ReceiptInformation` Pydantic 模型定义的相应关键信息。现在，我们想将这些示例注入到对 gpt-3.5-turbo 的调用中，期望它能将从这些示例中学到的东西推广到新的收据。少量示例学习是提示工程中的一种强大工具，如果它有效，对于这个用例将非常有用，因为每当检测到新的收据格式时，我们可以使用 gpt-4-vision 生成一个示例，并将其附加到用于提示 gpt-3.5-turbo 的示例列表中。然后，当出现类似格式的收据时，可以使用 gpt-3.5-turbo 来提取其内容。从某种程度上说，这就像模板匹配，但不需要手动定义模板。

有很多方法可以促使基于文本的 LLM 从一段文本中提取结构化信息。我发现的最新且最强大的方法之一可以在 Langchain 文档的 [此处](https://python.langchain.com/docs/use_cases/extraction/how_to/examples/) 找到。其思路是创建一个包含占位符的提示语，用于存放一些示例，然后将这些示例注入到提示语中，就像它们是通过某个 LLM 调用的函数返回的一样。这是通过 `model.with_structured_output()` 功能实现的，您可以在 [此处](https://python.langchain.com/docs/modules/model_io/chat/structured_output/) 阅读相关信息。请注意，目前这个功能还处于测试阶段，可能会发生变化！

让我们看一下代码，了解这是如何实现的。我们将首先编写提示语。

```py
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

@dataclass
class TextReceiptExtractionPrompt:
    system: str = """
       You are an expert at information extraction from images of receipts.

       Given this of a receipt, extract the following information:
       - The name and address of the vendor
       - The names and costs of each of the items that were purchased
       - The date and time that the receipt was issued. This must be formatted like 'MM/DD/YY HH:MM'
       - The subtotal (i.e. the total cost before tax)
       - The tax rate
       - The total cost after tax

       Do not guess. If some information is missing just return "N/A" in the relevant field.
       If you determine that the image is not of a receipt, just set all the fields in the formatting instructions to "N/A". 

       You must obey the output format under all circumstances. Please follow the formatting instructions exactly.
       Do not return any additional comments or explanation.
       """

    prompt: ChatPromptTemplate = ChatPromptTemplate.from_messages(
        [
            (
                "system",
                system,
            ),
            MessagesPlaceholder("examples"),
            ("human", "{input}"),
        ]
    )
```

提示语文本与第 4 节中的完全相同，只是我们现在有了一个 `MessagesPlaceholder` 来保存我们即将插入的示例。

```py
class Example(TypedDict):
    """A representation of an example consisting of text input and expected tool calls.

    For extraction, the tool calls are represented as instances of pydantic model.
    """

    input: str
    tool_calls: List[BaseModel]

class TextReceiptExtractionChain:

    def __init__(self, llm, examples: List):

        self.llm = llm
        self.raw_examples = examples
        self.prompt = TextReceiptExtractionPrompt()
        self.chain, self.examples = self.set_up_chain()

    @staticmethod
    def tool_example_to_messages(example: Example) -> List[BaseMessage]:
        """Convert an example into a list of messages that can be fed into an LLM.

        This code is an adapter that converts our example to a list of messages
        that can be fed into a chat model.

        The list of messages per example corresponds to:

        1) HumanMessage: contains the content from which content should be extracted.
        2) AIMessage: contains the extracted information from the model
        3) ToolMessage: contains confirmation to the model that the model requested a tool correctly.

        The ToolMessage is required because some of the chat models are hyper-optimized for agents
        rather than for an extraction use case.
        """
        messages: List[BaseMessage] = [HumanMessage(content=example["input"])]
        openai_tool_calls = []
        for tool_call in example["tool_calls"]:
            openai_tool_calls.append(
                {
                    "id": str(uuid.uuid4()),
                    "type": "function",
                    "function": {
                        # The name of the function right now corresponds
                        # to the name of the pydantic model
                        # This is implicit in the API right now,
                        # and will be improved over time.
                        "name": tool_call.__class__.__name__,
                        "arguments": tool_call.json(),
                    },
                }
            )
        messages.append(
            AIMessage(content="", additional_kwargs={"tool_calls": openai_tool_calls})
        )
        tool_outputs = example.get("tool_outputs") or [
            "You have correctly called this tool."
        ] * len(openai_tool_calls)
        for output, tool_call in zip(tool_outputs, openai_tool_calls):
            messages.append(ToolMessage(content=output, tool_call_id=tool_call["id"]))
        return messages

    def set_up_examples(self):

        examples = [
            (
                example["input"],
                ReceiptInformation(
                    vendor_name=example["output"]["vendor_name"],
                    vendor_address=example["output"]["vendor_address"],
                    datetime=example["output"]["datetime"],
                    items_purchased=[
                        ReceiptItem(
                            item_name=example["output"]["items_purchased"][i][
                                "item_name"
                            ],
                            item_cost=example["output"]["items_purchased"][i][
                                "item_cost"
                            ],
                        )
                        for i in range(len(example["output"]["items_purchased"]))
                    ],
                    subtotal=example["output"]["subtotal"],
                    tax_rate=example["output"]["tax_rate"],
                    total_after_tax=example["output"]["total_after_tax"],
                ),
            )
            for example in self.raw_examples
        ]

        messages = []

        for text, tool_call in examples:
            messages.extend(
                self.tool_example_to_messages(
                    {"input": text, "tool_calls": [tool_call]}
                )
            )

        return messages

    def set_up_chain(self):

        extraction_model = self.llm
        prompt = self.prompt.prompt
        examples = self.set_up_examples()
        runnable = prompt | extraction_model.with_structured_output(
            schema=ReceiptInformation,
            method="function_calling",
            include_raw=False,
        )

        return runnable, examples

    def run_and_count_tokens(self, input_dict: dict):

        # inject the examples here
        input_dict["examples"] = self.examples
        with get_openai_callback() as cb:
            result = self.chain.invoke(input_dict)

        return result, cb
```

`TextReceiptExtractionChain` 将接受一个包含多个示例的列表，每个示例都有 `input` 和 `output` 键（注意这些键是如何在 `set_up_examples` 方法中使用的）。对于每个示例，我们将创建一个 `ReceiptInformation` 对象。然后，我们将结果格式化为一个消息列表，可以传递给提示语。`tool_examples_to_messages` 中的所有工作只是为了在不同的 Langchain 格式之间进行转换。

运行这段代码与我们之前使用视觉模型时非常相似：

```py
# Load the examples 
EXAMPLES_PATH = "receiptchat/datasets/example_extractions.json"
with open(EXAMPLES_PATH) as f:
    loaded_examples = json.load(f)

loaded_examples = [
    {"input": x["file_details"]["extracted_text"], "output": x}
    for x in loaded_examples
]

# Set up the LLM caller
llm = ChatOpenAI(
  api_key=secrets["OPENAI_API_KEY"], 
  temperature=0, 
  model="gpt-3.5-turbo"
)
extractor = TextReceiptExtractionChain(llm, loaded_examples)

# convert a PDF file form Google Drive into text
text = PDFBytesToImage.convert_bytes_to_text(downloaded_data)

extracted_information, cb = extractor.run_and_count_tokens(
            {"input": text}
)
```

即使是 10 个示例，这个调用的成本也不到 gpt-4-vision 的一半，而且返回速度也更快。随着示例数量的增加，您可能需要使用 gpt-3.5-turbo-16k，以避免超出上下文窗口。

# 输出数据集

收集了一些收据后，您可以运行第 4 和第 5 节中描述的提取方法，并将结果收集到一个数据框中。然后，它将被存储，并且每当 Google Drive 中出现新的收据时，可以将其附加到这个数据框中。

![](../Images/07908e8c252f1d406db696da159899c3.png)

输出数据集的示例，显示了从多个收据中提取的字段。图片由作者生成

一旦我的提取的收据信息数据库变得更大一些，我计划在此基础上探索基于 LLM 的问答功能，敬请期待相关的文章！我还对为这个项目探索一种更正式的评估方法并将其与 AWS Textract 或类似产品的结果进行比较感到好奇。

感谢您看到最后！请随时在此处探索完整的代码库 [https://github.com/rmartinshort/receiptchat](https://github.com/rmartinshort/receiptchat)。任何关于改进或扩展功能的建议都将不胜感激！
