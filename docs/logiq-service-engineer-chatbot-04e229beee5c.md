# 为什么你的服务工程师需要一个聊天机器人：故障排除的未来

> 原文：[https://towardsdatascience.com/logiq-service-engineer-chatbot-04e229beee5c?source=collection_archive---------4-----------------------#2024-09-29](https://towardsdatascience.com/logiq-service-engineer-chatbot-04e229beee5c?source=collection_archive---------4-----------------------#2024-09-29)

![](../Images/8ede3be3aba1187f0602522551c573a0.png)

图片：[The New Yorker / Widows](https://www.behance.net/gallery/73239099/The-New-Yorker-Widows) © [Matt Chinworth](https://www.behance.net/mattchinworth) | CC BY-NC-ND 4.0

## 作为2024年Google AI Sprint的一部分，我构建了一个多模态聊天机器人，结合了Gemini 1.5，下面是它如何彻底改变家电支持的方式

[](https://thisisashwinraj.medium.com/?source=post_page---byline--04e229beee5c--------------------------------)[![Ashwin Raj](../Images/7bfb5e302bba2c12beafb6e09268832d.png)](https://thisisashwinraj.medium.com/?source=post_page---byline--04e229beee5c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--04e229beee5c--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--04e229beee5c--------------------------------) [Ashwin Raj](https://thisisashwinraj.medium.com/?source=post_page---byline--04e229beee5c--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--04e229beee5c--------------------------------) ·8分钟阅读·2024年9月29日

--

在各行各业中，故障排除对于维持平稳运营、确保客户满意度以及优化服务流程效率至关重要。然而，现场故障排除家电设备可能是一项具有挑战性的任务。由于有各种型号和无数潜在问题，服务工程师常常不得不翻阅手册或在线搜索解决方案，这种方法既令人沮丧，又费时费力。

这就是配备全面服务知识并能访问最新故障排除手册的聊天机器人能够改变体验的地方。虽然人们可能认为检索增强生成（RAG）是此类任务的理想解决方案，但在这种情况下，它往往力不从心。因为这些手册通常包含表格、图片和图表等元素，而这些元素难以提取，且总结可能会忽略其中的复杂细节，使得它不适合用于生产部署。

在本文中，我们将着手构建一个使用Gemini的聊天机器人，帮助现场服务工程师更快速、更直观地找到所需信息。我们还将探讨Gemini提供的高级功能，如上下文缓存和文件API集成，以支持多模态提示。最后，我们将把这个聊天机器人封装在Streamlit界面中，以便于交互。

# 在你开始之前

为了构建聊天机器人，我们将使用Gemini、Python 3和Streamlit。首先，通过运行以下命令在你的本地机器上安装Streamlit：

```py
pip install streamlit
```

对于数据库，我们将依赖于Python预装的SQLite。我们还需要一个Gemini API密钥，以便使用Gemini 1.5 Flash进行推理。如果你还没有API密钥，你可以通过这个[链接](https://ai.google.dev/gemini-api/docs/api-key)免费创建一个。一旦你设置了密钥，运行以下命令安装Google AI Python SDK：

```py
pip install google-generativeai
```

你可以在我的GitHub仓库[这里](https://github.com/thisisashwinraj/LogIQ-Service-Engineer-Assistant)找到源代码和其他资源。

> ***致谢：*** *本项目提供了Google Cloud的信用额度，作为#AISprint 2024的一部分*

# 架构

在实现之前，让我们详细检查系统架构。该过程从从数据库中提取所需的产品手册并传递给Gemini开始。这充当我们的聊天机器人的知识库，为所选电器提供必要的故障排除信息。

![](../Images/7565ffd5e34ede62623634e9e9b8703d.png)

图片来自作者

一旦文档加载完成，我们利用Gemini的多模态文档处理能力，从产品手册中提取所需的信息。现在，当用户与聊天机器人互动时，模型将结合上传的服务手册数据、聊天历史和其他上下文信息，提供准确而深刻的回答，解答用户的查询。

为了提高性能，我们将实现上下文缓存，优化重复查询的响应时间。最后，我们将把这个架构封装在一个简单且直观的Streamlit Web应用程序中，允许服务工程师无缝地与聊天代理互动，并访问他们需要的信息。

# 将服务手册加载到数据库中

要开始构建聊天机器人，第一步是将故障排除指南加载到我们的数据库中以供参考。由于这些文件本身没有结构，我们无法直接将它们存储在数据库中。相反，我们存储它们的文件路径：

```py
class ServiceGuides:
    def __init__(self, db_name="database/persistent/general.db"):
        self.conn = sqlite3.connect(db_name)
        self.create_table()

    def add_service_guide(self, model_number, guide_name, guide_file_url):
        cursor = self.conn.cursor()

        cursor.execute('''
            INSERT INTO service_guides (model, guide_name, guide_url)
            VALUES (?, ?, ?)
        ''', (model_number, guide_name, guide_file_url))

        self.conn.commit()

    def fetch_guides_by_model_number(self, model_number):
        cursor = self.conn.cursor()
        cursor.execute(
            """SELECT guide_url FROM service_guides WHERE model = ?""",
            (model_number,),
        )
        return cursor.fetchone()
```

在这个项目中，我们将在本地目录中存储手册，并将它们的文件路径保存在SQLite数据库中。然而，为了更好的可扩展性，建议使用对象存储服务，例如Google Cloud Storage来存储这些文件，并在像Google Cloud SQL这样的数据库服务中维护文件的URL。

# 使用Gemini构建对话代理

一旦产品手册加载到数据库中，下一步是使用1.5 Flash构建代理。这个轻量级模型是Gemini家族的一部分，并通过一种称为“蒸馏”的过程进行了微调，其中来自更大模型的最重要的知识和技能被转移到一个更小、更高效的模型中，以支持各种高容量任务的规模。

![](../Images/e0b374608d80284f7b5c0abd18a97431.png)

来自[The Keyword](https://blog.google/technology/ai/google-gemini-update-flash-ai-assistant-io-2024/#gemini-model-updates)的图片，来源于Google

为了提高速度和操作效率，1.5 Flash模型在多模态推理方面非常高效，具有最多可达100万个令牌的上下文窗口，成为我们服务工程师使用场景的理想选择。

# 使用1.5 Flash进行多模态文档处理

要对我们的服务手册进行推理，首先需要将文件上传到Gemini。Gemini API支持将媒体文件与提示输入分开上传，使我们能够在多个请求中重复使用文件。File API每个项目支持最多20 GB的文件，每个文件最大支持2 GB：

```py
class ServiceEngineerChatbot:
    def __init__(self):
        genai.configure(api_key=st.secrets["GEMINI_API_KEY"])

    def post_service_guide_to_gemini(self, title, path_to_service_guide):
        service_guide = genai.upload_file(
            path=path_to_service_guide,
            display_name=title,
        )

        while service_guide.state.name == 'PROCESSING':
            print('Waiting for file to be processed.')
            time.sleep(2)
            service_guide = genai.get_file(service_guide.name)

        return service_guide
```

要上传文件，我们使用upload_file()方法，该方法的参数包括路径（要上传的文件路径）、名称（目标文件名，默认为系统生成的ID）、mime_type（指定文档的MIME类型，如果未指定，将进行推断）和display_name。

在继续之前，我们需要通过检查文件的元数据来验证API是否成功存储了上传的文件。如果文件的状态是PROCESSING，则尚不能用于推理。一旦状态变为ACTIVE，文件即可使用。如果状态为FAILED，表示文件处理未成功。

# 对话式响应生成

上传服务手册后，下一步是利用Gemini 1.5的多模态文档处理能力来生成响应。API的聊天功能允许我们收集多轮问题和答案，便于深入分析问题并逐步解决。

![](../Images/1540dd9ce6db00580a566619f3debedd.png)

作者提供的图片

初始化模型时，提供具体的指导方针和上下文以塑造聊天机器人的行为至关重要。这是通过向模型提供系统指令来完成的。系统指令有助于维持上下文，指导互动风格，确保一致性，并为聊天机器人的响应设定边界，同时尽量避免幻觉现象。

```py
class ServiceEngineerChatbot:
    def __init__(self):
        genai.configure(api_key=st.secrets["GEMINI_API_KEY"])

    def construct_flash_model(self, brand, sub_category, model_number):
        model_system_instruction = f"""
        Add your detailed system instructions here.
        These instructions should define the chatbot's behavior, tone, and 
        provide any necessary context. For example, you might include 
        guidelines about how to respond to queries, the structure of 
        responses, or information about what the chatbot should and should
        not do. Checkout my repo for this chatbot's system instructions.
        """

        model_generation_cofig = genai.types.GenerationConfig(
            candidate_count=1,
            max_output_tokens=1500,
            temperature=0.4,
        ),

        model = genai.GenerativeModel(
            model_name="gemini-1.5-flash",
            system_instruction=model_system_instruction,
            generation_config=model_generation_cofig,
        )
        return model
```

我们可以通过调整GenerationConfig类中的模型参数，进一步控制模型的响应生成。在我们的应用中，我们已将max_output_tokens设置为1500，定义了每个响应的最大令牌限制，并将temperature设置为0.4，以保持响应的确定性。

# 使用上下文缓存进行长上下文优化

在许多情况下，尤其是对于同一文档的重复查询，我们最终会将相同的输入令牌反复发送给模型。尽管这种方法可能有效，但对于大规模的生产级应用来说，它并不是最优选择。

这是 Gemini 的上下文缓存功能变得至关重要的地方，通过减少高 Token 工作负载的成本和延迟，提供更高效的解决方案。通过上下文缓存，我们可以在后续请求中引用缓存的 Token，而不是每次请求都发送相同的输入 Token。

![](../Images/1b41ca56dd2eb7728e323325dc577207.png)

图片由作者提供

在这个项目中，我们缓存了系统指令和服务手册文件。在大规模使用时，使用缓存的 Token 相比重复传输相同的数据显著降低了成本。默认情况下，这些缓存 Token 的生存时间（TTL）为 1 小时，但可以根据需要进行调整。一旦 TTL 到期，缓存的 Token 将自动从 Gemini 的上下文中移除。

```py
class ServiceEngineerChatbot:
    def _generate_context_cache(
        self,
        brand,
        sub_category,
        model_number,
        service_guide_title,
        service_guide,
        ttl_mins=70,
    ):
        context_cache = caching.CachedContent.create(
            model='models/gemini-1.5-flash-001',
            display_name=f"{service_guide_title}_cache",
            system_instruction=model_system_instruction,
            contents=[
                service_guide
            ],
            ttl=datetime.timedelta(
                minutes=ttl_mins
            ),
        )

        return context_cache
```

需要注意的是，只有当输入 Token 数量达到 32,768 或更多时，上下文缓存才可用。如果 Token 数量低于此阈值，则需要依赖 Gemini 1.5 Flash 的标准多模态提示功能。

# 集成聊天机器人与 Streamlit

在我们的聊天机器人响应生成能力就位后，最后一步是将其包装成一个 Streamlit 应用，创建一个直观的用户界面供用户使用。

![](../Images/d5221aaf47c78a7a5d1a43c8f0552755.png)

图片由作者提供

界面具有一个下拉菜单，用户可以选择他们正在使用的家电品牌和型号。选择后点击“配置聊天机器人”按钮，应用将把相应的服务手册传递给 Gemini，并展示聊天界面。从此，工程师可以输入他们的问题，聊天机器人将提供相关的回复。

# 未来展望

展望未来，有几个有前景的方向值得探索。聊天机器人的未来版本可以集成语音支持，允许工程师与聊天机器人进行更自然的交流，从而解决他们的问题。

此外，扩展系统以纳入预测性诊断功能，可以使工程师预先识别潜在问题，避免其导致设备故障。通过不断发展这个工具，目标是为服务工程师创建一个全面的支持系统，最终提升客户体验，从而改变故障排除的生态系统。

至此，我们已经结束了本文。如果您有任何问题或认为我有任何错误，请随时与我联系！您可以通过[电子邮件](mailto:rajashwin733@gmail.com)或[LinkedIn](https://www.linkedin.com/in/thisisashwinraj/)与我取得联系。直到那时，祝您学习愉快！
