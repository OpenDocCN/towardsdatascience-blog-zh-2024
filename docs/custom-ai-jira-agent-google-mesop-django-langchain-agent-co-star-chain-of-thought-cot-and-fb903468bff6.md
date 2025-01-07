# 构建一个自定义AI Jira代理

> 原文：[https://towardsdatascience.com/custom-ai-jira-agent-google-mesop-django-langchain-agent-co-star-chain-of-thought-cot-and-fb903468bff6?source=collection_archive---------0-----------------------#2024-12-25](https://towardsdatascience.com/custom-ai-jira-agent-google-mesop-django-langchain-agent-co-star-chain-of-thought-cot-and-fb903468bff6?source=collection_archive---------0-----------------------#2024-12-25)

## 我是如何使用Google Mesop、Django、LangChain Agents、CO-STAR以及链式思维（CoT）提示结合Jira API来更好地自动化Jira的。

[](https://medium.com/@ljamesdatascience?source=post_page---byline--fb903468bff6--------------------------------)[![Lewis James | 数据科学](../Images/fdbb8a326f6873a9776725ffaa0b4309.png)](https://medium.com/@ljamesdatascience?source=post_page---byline--fb903468bff6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fb903468bff6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fb903468bff6--------------------------------) [Lewis James | 数据科学](https://medium.com/@ljamesdatascience?source=post_page---byline--fb903468bff6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fb903468bff6--------------------------------) ·阅读时间19分钟·2024年12月25日

--

![](../Images/3244eeafdbf9aec0882a5238b4395036.png)

图片由[Google DeepMind](https://unsplash.com/@googledeepmind?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/a-bonsai-tree-growing-out-of-a-concrete-block-K2V_fqM2RY8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

这个项目的灵感来自于我为内部用户开发的一个Web应用程序中托管的Jira工单创建工具。我还在系统错误时加入了自动创建Jira工单的功能。

用户和系统错误通常会创建类似的工单，因此我想看看LLM的推理能力是否可以用来通过链接相关问题、创建用户故事、验收标准和优先级，自动分流工单。

此外，给用户和产品/管理利益相关者提供更便捷的方式，通过自然语言直接与Jira互动，而无需任何技术能力，也是一个很有趣的前景。

[Jira](https://www.atlassian.com/software/jira)已经在软件开发中无处不在，现在已成为项目管理的领先工具。

具体来说，大型语言模型（LLM）和智能代理研究的进展表明，在这一领域有机会实现显著的生产力提升。

与Jira相关的任务非常适合自动化，因为这些任务以文本形式呈现，具有高度重复性，风险较低，复杂性也较低。

在下文中，我将展示我的开源项目——AI Jira助手：一个与Jira交互的聊天界面，通过自定义AI代理工具来分诊新创建的Jira票据。

所有代码都已通过文章末尾的GitHub仓库提供。

该项目利用LangChain代理，通过Django（配合PostgreSQL）和Google Mesop提供服务。服务在Docker中运行，以便本地使用。

提示策略包括CO-STAR系统提示、链式思维（CoT）推理和少量示例提示。

本文将包括以下章节。

1.  定义

1.  Mesop接口：Streamlit还是Mesop？

1.  Django REST框架

1.  自定义LangChain代理工具和提示

1.  Jira API示例

1.  下一步

# 1\. 定义

首先，我想介绍一些对项目至关重要的高层定义。

## AI Jira助手

本文展示的开源项目，在本地运行时如下所示。

包括一个用于用户提示的聊天界面，示例提示预填充聊天界面，一个用于显示模型响应的框，以及一个清除模型响应的按钮。

讨论了项目中各个技术难题的代码片段。

![](../Images/536a02c183e1a1f86064776e5afc3c97.png)

AI Jira助手（图像由作者提供）

## **什么是Google Mesop？**

Mesop是一个相对较新的（2023年）Python web框架，Google用于快速AI应用程序开发。

> “Mesop提供30个多功能组件，从低级构建模块到高级AI聚焦组件。这个灵活性使您能够快速原型化机器学习应用程序或构建自定义UI，所有这些都在一个适应您项目用例的单一框架内进行。” — [Mesop首页](https://google.github.io/mesop/)

## 什么是AI代理？

代理软件范式的起源来自“代理”一词，指的是一种能够观察其环境并采取行动的软件程序。

> “人工智能（AI）代理是一种能够与其环境交互、收集数据并使用这些数据执行自我决定任务以实现预定目标的软件程序。”
> 
> “人类设定目标，但AI代理独立选择为实现这些目标所需执行的最佳行动。”
> 
> AI代理是理性代理。它们基于感知和数据做出理性决策，以产生最佳表现和结果。
> 
> “AI代理通过物理或软件接口感知其环境。” — [AWS官网](https://aws.amazon.com/what-is/ai-agents/)

## 什么是CO-STAR提示？

本文是一个提示格式指南，要求包含以下标题：上下文、目标、风格、语气、受众和回应。广泛接受这种格式，以提升大型语言模型（LLM）的输出质量。

> “CO-STAR框架是GovTech新加坡的数据科学与AI团队的创意，是一个便捷的模板，用于构建提示结构。”
> 
> 它考虑了所有影响LLM响应效果和相关性的关键因素，从而生成更优化的响应。” — [Sheila Teo的Medium文章](/how-i-won-singapores-gpt-4-prompt-engineering-competition-34c195a93d41)

## 什么是链式思维（CoT）提示？

最初由谷歌的一篇论文提出；[Wei等人（2022年）](https://arxiv.org/pdf/2201.11903)，链式思维（CoT）提示意味着提供少量示例的中间推理步骤。这被证明能够改善模型输出的常识推理能力。

## 什么是Django？

Django是一个广泛使用的复杂Python框架之一。

> “Django是一个高级Python Web框架，鼓励快速开发和干净、务实的设计。它是免费的，也是开源的。” — [Django官网](https://www.djangoproject.com/)

## 什么是LangChain？

LangChain是支持LLM应用程序的更为知名的开源库之一，包括与本项目相关的代理和提示。

> “LangChain的灵活抽象和以AI为先的工具包使其成为开发者在构建生成式AI时的首选工具。”
> 
> 加入超过1百万的构建者，在LangChain的Python和JavaScript框架中标准化他们的LLM应用开发。” — [LangChain官网](https://www.langchain.com/langchain)

# 2\. Mesop接口：Streamlit还是Mesop？

![](../Images/f457d98dc5446aeb5960d0d8e95a50c9.png)

图片由[Konsepta Studio](https://unsplash.com/@konseptastudio?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，发布于[Unsplash](https://unsplash.com/photos/black-framed-eyeglasses-on-white-surface-DSW79QZtjGM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

我在专业工作中广泛使用了[Streamlit](https://streamlit.io/)来托管生成式AI应用，我的一个工作示例可以在[这里](https://wan-ifra.org/2024/10/how-uks-reach-is-using-ai-to-help-produce-more-content-faster/)找到。

从高层次来看，Streamlit是一个类似的开源Python Web框架。

关于Streamlit的更多信息，请参见我在Medium上的另一篇文章，那里详细讨论了这一话题。

[](https://medium.com/@ljamesdatascience/ai-grant-application-writer-autogen-postgresql-rag-langchain-fastapi-and-streamlit-93ba439dcb7d?source=post_page-----fb903468bff6--------------------------------) [## AI资助申请写作工具 — AutoGen, PostgreSQL RAG, LangChain, FastAPI和Streamlit]

### 写资助申请可能是一个既耗时又乏味的工作，生成式AI似乎是一个自然的解决方案，尽管它可能显得有些天真……

[medium.com](https://medium.com/@ljamesdatascience/ai-grant-application-writer-autogen-postgresql-rag-langchain-fastapi-and-streamlit-93ba439dcb7d?source=post_page-----fb903468bff6--------------------------------)

这是第一次在实际使用中应用Mesop——所以我认为做个比较可能会很有用。

Mesop 旨在提供对组件的 CSS 样式更精细的控制，并且原生集成了 JS Web 评论。Mesop 还提供了有用的调试工具，可以在本地运行时使用。从经验来看，我还想说，多个页面应用功能的使用要更为便捷。

然而，这确实意味着，对于不太精通 CSS 样式的机器学习从业者（包括我自己），存在更大的门槛。Streamlit 也有更大的社区支持。

从代码片段来看，我们可以设置不同的页面路由。项目只包含两个页面：主页面和错误页面。

```py
import mesop as me

# local imports
try:
    from .utils import ui_components
except Exception:
    from utils import ui_components

@me.page(path="/")
def page(security_policy=me.SecurityPolicy(dangerously_disable_trusted_types=True)):
    with me.box(
        style=me.Style(
            background="#fff",
            min_height="calc(100% - 48px)",
            padding=me.Padding(bottom=16),
        )
    ):
        with me.box(
            style=me.Style(
                width="min(800px, 100%)",
                margin=me.Margin.symmetric(horizontal="auto"),
                padding=me.Padding.symmetric(
                    horizontal=16,
                ),
            )
        ):
            ui_components.header_text()
            ui_components.example_row()
            ui_components.chat_input()
            ui_components.output()
            ui_components.clear_output()
    ui_components.footer()

@me.page(path="/error")
def error(security_policy=me.SecurityPolicy(dangerously_disable_trusted_types=True)):
    with me.box(
        style=me.Style(
            background="#fff",
            min_height="calc(100% - 48px)",
            padding=me.Padding(bottom=16),
        )
    ):
        with me.box(
            style=me.Style(
                width="min(720px, 100%)",
                margin=me.Margin.symmetric(horizontal="auto"),
                padding=me.Padding.symmetric(
                    horizontal=16,
                ),
            )
        ):
            ui_components.header_text()
            ui_components.render_error_page()
    ui_components.footer()
```

错误页面包含一个按钮，用于重定向到主页。

![](../Images/a62ff27dc85ddaa92e445800afda8b06.png)

AI Jira 助手错误页面（图片来自作者）

触发重定向到主页的代码在这里包含。

```py
def navigate_home(event: me.ClickEvent):
    me.navigate("/")

def render_error_page():
    is_mobile = me.viewport_size().width < 640
    with me.box(
        style=me.Style(
            position="sticky",
            width="100%",
            display="block",
            height="100%",
            font_size=50,
            text_align="center",
            flex_direction="column" if is_mobile else "row",
            gap=10,
            margin=me.Margin(bottom=30),
        )
    ):
        me.text(
            "AN ERROR HAS OCCURRED",
            style=me.Style(
                text_align="center",
                font_size=30,
                font_weight=700,
                padding=me.Padding.all(8),
                background="white",
                justify_content="center",
                display="flex",
                width="100%",
            ),
        )
        me.button(
            "Navigate to home page", 
            type="flat",
            on_click=navigate_home
        )
```

我们还必须创建 State 类，这样可以在事件循环中保持数据的持久性。

```py
import mesop as me

@me.stateclass
class State:
    input: str
    output: str
    in_progress: bool
```

要清除界面中的模型输出，我们可以将输出变量赋值为空字符串。也有不同的[按钮支持类型](https://google.github.io/mesop/components/button/#overview)，截至撰写时，包括：默认、提升、平面和带边框。

```py
def clear_output():
    with me.box(style=me.Style(margin=me.Margin.all(15))):
        with me.box(style=me.Style(display="flex", flex_direction="row", gap=12)):
            me.button("Clear output", type="flat", on_click=delete_state_helper)

def delete_state_helper(ClickEvent):
    config.State.output = ""
```

为了自动填充聊天界面中的示例提示，我们使用按钮的 onclick 事件，通过更新状态来实现。

```py
def example_row():
    is_mobile = me.viewport_size().width < 640
    with me.box(
        style=me.Style(
            display="flex",
            flex_direction="column" if is_mobile else "row",
            gap=10,
            margin=me.Margin(bottom=40),
        )
    ):
        for example in config.EXAMPLE_PROMPTS:
            prompt_box(example, is_mobile)

def prompt_box(example: str, is_mobile: bool):
    with me.box(
        style=me.Style(
            width="100%" if is_mobile else 200,
            height=250,
            text_align="center",
            background="#F0F4F9",
            padding=me.Padding.all(16),
            font_weight=500,
            line_height="1.5",
            border_radius=16,
            cursor="pointer",
        ),
        key=example,
        on_click=click_prompt_box,
    ):
        me.text(example)

def click_prompt_box(e: me.ClickEvent):
    config.State.input = e.key
```

类似地，要向 Django 服务发送请求，我们使用以下代码片段。我们使用[海象运算符](https://www.geeksforgeeks.org/walrus-operator-in-python-3-8/) (:=) 来判断请求是否收到了有效的响应（即状态码为200且非 None），并将输出追加到状态中，以便在 UI 中渲染，否则我们会将用户重定向到前面讨论的错误页面。

```py
def chat_input():
    with me.box(
        style=me.Style(
            padding=me.Padding.all(8),
            background="white",
            display="flex",
            width="100%",
            border=me.Border.all(me.BorderSide(width=0, style="solid", color="black")),
            border_radius=12,
            box_shadow="0 10px 20px #0000000a, 0 2px 6px #0000000a, 0 0 1px #0000000a",
        )
    ):
        with me.box(
            style=me.Style(
                flex_grow=1,
            )
        ):
            me.native_textarea(
                value=config.State.input,
                autosize=True,
                min_rows=4,
                placeholder="Enter your prompt",
                style=me.Style(
                    padding=me.Padding(top=16, left=16),
                    background="white",
                    outline="none",
                    width="100%",
                    overflow_y="auto",
                    border=me.Border.all(
                        me.BorderSide(style="none"),
                    ),
                ),
                on_blur=textarea_on_blur,
            )
        with me.content_button(type="icon", on_click=click_send):
            me.icon("send")

def click_send(e: me.ClickEvent):
    if not config.State.input:
        return
    config.State.in_progress = True
    input = config.State.input
    config.State.input = ""
    yield

    if result := api_utils.call_jira_agent(input):
        config.State.output += result
    else:
        me.navigate("/error")

    config.State.in_progress = False
    yield

def textarea_on_blur(e: me.InputBlurEvent):
    config.State.input = e.value
```

为了完整起见，我提供了向 Django 端点发送请求的代码，用于运行 AI Jira Agent。

```py
import requests

# local imports
from . import config

def call_jira_agent(request):
    try:
        data = {"request": request}
        if (response := requests.post(f"{config.DJANGO_URL}api/jira-agent/", data=data)) and \
        (response.status_code == 200) and \
        (output := response.json().get("output")):
            return f"Request: {request}<br>Output: {output}<br><br>"
    except Exception as e:
        print(f"ERROR call_jira_agent: {e}")
```

为了在本地运行，我已包含了相关的 Docker 和 Docker compose 文件。

运行 Mesop 的 Docker 文件是通过 Mesop 项目主页提供的。

Docker compose 文件由三个服务组成：后端 Django 应用、前端 Mesop 应用和一个与 Django 应用配合使用的 PostgreSQL 数据库实例。

我想特别指出传递给 Mesop Docker 容器的环境变量，PYTHONUNBUFFERED=1 确保 Python 的输出、标准输出（stdout）和标准错误输出（stderr）流被发送到终端。在使用 Mesop 应用程序推荐的 Docker 镜像时，我花了一些时间才找出未能看到应用程序输出的根本原因。

DOCKER_RUNNING=true 环境变量是一种约定，用于简单地判断应用程序是否在 Docker 中运行，或者例如在虚拟环境中运行。

需要指出的是，环境变量将通过配置文件 'config.ini' 填充，该文件位于 Docker compose 文件中的 env_file 元素所引用的 config 子目录中。

要运行该项目，你必须用你的 Open AI 和 Jira 凭证填充这个配置文件。

# 3\. Django REST 框架

![](../Images/e4bf7b8e0f7357749d194a4ed4c9ca64.png)

图片由 [Faisal](https://unsplash.com/@faisaldada?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/black-and-yellow-box-on-white-table-BI465ksrlWs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Django 是一个 Python web 框架，内置了大量有用的功能。

它可以与 Flask 或 FastAPI 等框架进行比较，尽管它需要一些额外的配置，并且入门时学习曲线较陡峭。

如果你想了解更多关于 Flask 的内容，请参阅我下面的文章。

[](https://medium.com/analytics-vidhya/canal-boat-pricing-with-catboost-machine-learning-5b2389999116?source=post_page-----fb903468bff6--------------------------------) [## Canal Boat Pricing With Catboost Machine Learning

### 来自 YouTube 推荐引擎的一个更有趣的建议，结合了完全远程工作的转变……

medium.com](https://medium.com/analytics-vidhya/canal-boat-pricing-with-catboost-machine-learning-5b2389999116?source=post_page-----fb903468bff6--------------------------------)

在本文中，我将涵盖应用程序、模型、序列化器、视图以及 PostgreSQL 数据库的集成。

一个应用程序是一个逻辑上分离的 web 应用，具有特定的目的。

在我们的实例中，我们将应用命名为“api”，并通过运行以下命令创建。

```py
django-admin startapp api
```

在 views.py 文件中，我们定义了我们的 API 端点。

> “视图函数，简称 *视图*，是一个 Python 函数，它接收一个 web 请求并返回一个 web 响应。这个响应可以是一个网页的 HTML 内容，或者是重定向，或者是 404 错误，或者是 XML 文档，或者是图片……实际上可以是任何内容。视图本身包含返回该响应所需的任意逻辑。” — [Django 官网](https://docs.djangoproject.com/en/5.1/topics/http/views/)

路由到 Django 视图的端点定义在应用的 urls.py 文件中，如下所示。urls.py 文件是在应用初始化时创建的。我们在这个项目中有三个端点；一个健康检查端点，一个用于返回数据库中所有记录的端点，以及一个用于处理调用 AI 代理的端点。

视图是声明为类的，这是 Django 中的标准约定。请查看完整的文件内容。

大部分代码都不言自明，尽管这个代码片段很重要，因为它将把模型的数据保存到数据库中。

```py
modelRequest = models.ModelRequest(request=request, response=response)
modelRequest.save()
```

以下代码片段返回数据库中 ModelRequest 模型的所有记录，接下来我将介绍模型。

```py
class GetRecords(APIView):
    def get(self, request):
        """Get request records endpoint"""
        data = models.ModelRequest.objects.all().values()
        return Response({'result': str(data)})
```

> “模型是关于你的数据的唯一、权威的信息来源。它包含了你存储的数据的基本字段和行为。通常，每个模型对应一个数据库表。” — [Django官网](https://docs.djangoproject.com/en/5.1/topics/db/models/#:~:text=A%20model%20is%20the%20single,.db.models.Model%20.)

我们的模型对于这个项目来说很简单，因为我们只需要存储用户请求和最终模型输出，这两个字段都是文本字段。

__str__ 方法是Python中的一个常见约定，例如，它在print函数中默认被调用。该方法的目的是返回对象的可读字符串表示。

序列化器将模型中的字段映射到验证输入和输出，并将更复杂的数据类型转换为Python数据类型。这可以在之前详细介绍的views.py中看到。

> “ModelSerializer通常指的是Django REST框架(DRF)中的一个组件。Django REST框架是一个用于在Django应用中构建Web API的流行工具包。它提供了一组工具和库，以简化构建API的过程，其中包括序列化器。”
> 
> ModelSerializer类提供了一种快捷方式，允许你自动创建一个Serializer类，该类的字段与Model字段相对应。
> 
> ModelSerializer 类与常规的 Serializer 类相同，不同之处在于：
> 
> 它将基于模型自动生成一组字段。
> 
> 它将自动为序列化器生成验证器，如unique_together验证器。
> 
> 它包括.create()和.update()的简单默认实现。” — [Geeks for geeks](https://www.geeksforgeeks.org/serializers-django-rest-framework/)

项目的完整serializers.py文件如下所示。

对于PostgreSQL数据库集成，settings.py文件中的配置必须与database.ini文件匹配。

默认的数据库设置必须更改为指向PostgreSQL数据库，因为这不是Django的默认数据库集成。

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'vectordb', 
        'USER': 'testuser',
        'PASSWORD': 'testpwd',
        'HOST': 'db' if DOCKER_RUNNING else '127.0.0.1', 
        'PORT': '5432',
    }
}
```

database.ini 文件在初始化时定义了PostgreSQL数据库的配置。

为确保在Docker容器启动后应用数据库迁移，我们可以使用bash脚本来应用迁移，然后启动服务器。自动运行迁移将意味着每当Django源代码中的定义发生变化时，数据库总是会被修改，这从长远来看能节省时间。

Dockerfile的入口点然后通过CMD指令修改为指向bash脚本。

# 4\. 自定义LangChain代理工具和提示

![](../Images/8c59bb257dfadce27140c4cfc1f3da92.png)

照片由 [Vlad Tchompalov](https://unsplash.com/@tchompalov?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/green-bird-kuTZUOVRpOo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

我正在使用现有的 LangChain 代理功能，并结合 [Jira 工具包](https://python.langchain.com/docs/integrations/tools/jira/)，这是 Atlassian Python API 的一个封装。

默认库开箱即用相当有用，尽管有时需要在提示上进行一些反复试验，不过我认为随着该领域的研究进展，应该会有所改进。

然而，对于这个项目，我希望在代理中添加一些自定义工具。这可以通过下面的 `triage` 函数与 @tool 装饰器来实现。

工具的函数类型提示和注释描述是必要的，以便向代理传达调用时的预期。当返回的字符串被代理观察到时，在这个实例中，我们只是返回“任务完成”，以便代理停止执行下一个步骤。

自定义分诊工具执行以下步骤；

+   获取该项目的所有未解决的 Jira 工单

+   获取代理正在进行分诊的 Jira 问题键的描述和摘要

+   与所有未解决的工单进行基于异步 LLM 的比较，并通过文本到文本的比较自动标记那些看起来相关的工单，然后使用 Jira API 将它们链接起来

+   然后，使用 LLM 生成用户故事、验收标准和优先级，并将此模型结果作为注释留在主工单上

```py
from langchain.agents import AgentType, initialize_agent
from langchain_community.agent_toolkits.jira.toolkit import JiraToolkit
from langchain_community.utilities.jira import JiraAPIWrapper
from langchain_openai import OpenAI
from langchain.tools import tool
from langchain_core.prompts import ChatPromptTemplate, FewShotChatMessagePromptTemplate

llm = OpenAI(temperature=0)

@tool
def triage(ticket_number:str) -> None:
    """triage a given ticket and link related tickets"""
    ticket_number = str(ticket_number)
    all_tickets = jira_utils.get_all_tickets()
    primary_issue_key, primary_issue_data = jira_utils.get_ticket_data(ticket_number)
    find_related_tickets(primary_issue_key, primary_issue_data, all_tickets)
    user_stories_acceptance_criteria_priority(primary_issue_key, primary_issue_data)
    return "Task complete"

jira = JiraAPIWrapper()
toolkit = JiraToolkit.from_jira_api_wrapper(jira)
agent = initialize_agent(
    toolkit.get_tools() + [triage], 
    llm, 
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, 
    verbose=True, 
    max_iterations=5,
    return_intermediate_steps=True
)
```

这两个 LLM 任务都使用了 CO-STAR 系统提示和链式思维的少量示例提示策略。因此，我将这些任务抽象成了一个 LLMTask 类。

它们在以下代码片段中被实例化。可以说，我们本可以为每个任务尝试不同的 LLM，尽管出于时间考虑，我并没有进行相关实验——如果你拉取了代码库并有经验分享，请随时在下方评论。

```py
class LLMTask:
    def __init__(self, system_prompt, examples, llm):
        self.system_prompt = system_prompt
        self.examples = examples
        self.llm = llm

    def construct_prompt(self):
        example_prompt = ChatPromptTemplate.from_messages(
            [
                ("human", "{input}"),
                ("ai", "{output}"),
            ]
        )       
        few_shot_prompt = FewShotChatMessagePromptTemplate(
            example_prompt=example_prompt,
            examples=self.examples,
        )       
        return ChatPromptTemplate.from_messages(
            [
                ("system", self.system_prompt),
                few_shot_prompt,
                ("human", "{input}"),
            ]
        )

    def run_llm(self, input):
        chain = self.construct_prompt() | self.llm 
        return chain.invoke({"input": input})

product_model = LLMTask(system_prompts.get("system_prompt_product"), example_prompts.get("examples_product"), llm)
linking_model = LLMTask(system_prompts.get("system_prompt_linking"), example_prompts.get("examples_linking"), llm)
```

对于链接任务，CO-STAR 系统提示如下。Context（上下文）、Objective（目标）、Style（风格）、Tone（语气）、Audience（受众）和 Response（响应）是 CO-STAR 方法的标准标题。我们定义了上下文和输出，包括标记模型结果的每个元素。

明确界定受众、风格和语气有助于确保模型输出适合商业环境。

```py
# CONTEXT #
I want to triage newly created Jira tickets for our software company by comparing them to previous tickets.
The first ticket will be in <ticket1> tags and the second ticket will be in <ticket2> tags. 

# OBJECTIVE #
Determine if two tickets are related if the issue describes similar tasks and return True in <related> tags, also include your thinking in <thought> tags.

# STYLE #
Keep reasoning concise but logical.

# TONE #
Create an informative tone.

# AUDIENCE #
The audience will be business stake holders, product stakeholders and software engineers.

# RESPONSE #
Return a boolean if you think the tickets are related in <related> tags and also return your thinking as to why you think the tickets are related in <thought> tags.
```

对于执行产品风格的工单评估（用户故事、验收标准和优先级），系统提示如下。我们明确将优先级定义为低（LOW）、中（MEDIUM）或高（HIGH）。

我们还要求模型具有产品负责人/经理的风格，因为这项任务通常由该职位执行。

```py
# CONTEXT #
You are a product owner working in a large software company, you triage new tickets from their descriptions in <description> tags as they are raised from users.

# OBJECTIVE #
From the description in <description> tags, you should write the following; user stories in <user_stories> tags, acceptance criteria in <acceptance_criteria> tags and priority in <priority>.
Priority must be either LOW, MEDIUM OR HIGH depending on the what you deem is most appropriate for the given description.
Also include your thinking in <thought> tags for the priority.

# STYLE #
Should be in the style of a product owner or manager.

# TONE #
Use a professional and business oriented tone.

# AUDIENCE #
The audience will be business stake holders, product stakeholders and software engineers.

# RESPONSE #
Respond with the following format.
User stories in <user_stories> tags.
Acceptance criteria in <acceptance_criteria> tags.
Priority in <priority> tags.
```

现在，我将提供用于链接 Jira 工单的链式思维少量示例提示，我们将分别在 `<issue1>` 和 `<issue2>` 标签中附上两个工单的摘要和描述。模型的思考过程通过 `<thought>` 标签捕获，这就是链式思维元素。

少量示例（few-shot）这个术语来自于向模型输入多个示例的做法。

<related>标签包含判断两个工单是否相关的信息。如果模型认为它们相关，则返回 True 值。

我们稍后会正则解析模型输出，并使用一个辅助函数通过 Jira API 链接相关工单，所有此项目的 Jira API 辅助函数将在文章后续部分提供。

```py
 "examples_linking": [
        {
            "input": "<issue1>Add Jira integration ticket creation Add a Jira creation widget to the front end of the website<issue1><issue2>Add a widget to the front end to create a Jira Add an integration to the front end to allow users to generated Jira tickets manually<issue2>",
            "output": "<related>True<related><thought>Both tickets relate to a Jira creation widget, they must be duplicate tickets.<thought>"
        },
        {
            "input": "<issue1>Front end spelling error There is a spelling error for the home page which should read 'Welcome to the homepage' rather than 'Wellcome to the homepage'<issue1><issue2>Latency issue there is a latency issue and the calls to the Open AI should be made asynchronous<issue2>",
            "output": "<related>False<related><thought>The first ticket is in relation to a spelling error and the second is a latency, therefore they are not related.<thought>"
        },
        {
            "input": "<issue1>Schema update We need to add a column for model requests and responses<issue1><issue2>Update schema to include both model requests and model responses Add to two new additional fields to the schema<issue2>",
            "output": "<related>True<related><thought>Both tickets reference a schema update with two new fields for model requests and model responses, therefore they must be related.<thought>"
        }
    ]
```

同样地，对于工单评估，用户故事被提供在<user_stories>标签中，验收标准在<acceptance_criteria>标签中，优先级在<priority>标签中。<thought>标签也用于捕捉模型给出优先级时的推理过程。

所有示例都由我手动标注。

```py
 "examples_product": [
        {
            "input": "<description>Add Jira integration ticket creation Add a Jira creation widget to the front end of the website<description>",
            "output": "<user_stories>As a user, I want to be able to create a Jira ticket directly from the website.\nAs a product owner, I want to streamline the process of creating Jira tickets for our team.<user_stories>\n<acceptance_criteria>The Jira creation widget should be easily accessible on the front end of the website.\nThe widget should allow users to input all necessary information for a Jira ticket, such as title, description, and assignee.\nOnce submitted, the widget should create a new Jira ticket in the designated project.\nThe widget should have proper error handling in case of any issues with creating the ticket.<acceptance_criteria>\n<priority>MEDIUM<priority>\n<thought>By adding a Jira integration and creation widget to the website, we can improve the efficiency and accuracy of creating Jira tickets for our team. This will ultimately save time and resources for both the business and the development team. Though there is no immediate need for this to be completed. Therefore, I believe this ticket should be prioritized as MEDIUM.<thought>"
        },
        {
            "input": "<description>Urgent schema update We need to add a column for model requests and responses for compliance<description>",
            "output": "<user_stories>As a developer, I want to be able to view and track model requests and responses for compliance purposes.\nAs a product owner, I want to ensure our system is compliant with regulations and standards.<user_stories>\n<acceptance_criteria>A new column should be added to the schema for model requests and responses.\nThe column should accurately track and store all relevant information for compliance purposes.\nThe column should be easily accessible and viewable for authorized users.\nThe update should not disrupt any existing functionality of the system.<acceptance_criteria>\n<priority>HIGH<priority>\n<thought>This ticket is marked as urgent, indicating that it is a high priority for the business. Compliance is a critical aspect of our business and it is important that we address this issue as soon as possible. By adding a new column for model requests and responses, we can ensure that our system is compliant and avoid any potential legal or regulatory issues. Therefore, I believe this ticket should be prioritized as HIGH.<thought>"
        },
        {
            "input": "<description>Homepage CSS error There is a CSS error for the homepage which is affecting a call to action button and negatively impacting conversion<description>",
            "output": "<user_stories>As a user, I want the CSS error on the homepage to be fixed so that I can easily access the call to action button.\nAs a product owner, I want to improve the conversion rate of our website by fixing any CSS errors.<user_stories>\n<acceptance_criteria>The CSS error should be identified and fixed on the homepage.\nThe call to action button should be easily visible and accessible for users.\nThe fix should not affect any other functionality of the website.<acceptance_criteria>\n<priority>HIGH<priority>\n<thought>This CSS error is directly impacting the conversion rate of our website, which is a key metric for our business. It is important that we address this issue as soon as possible to improve the user experience and ultimately increase conversions. Therefore, I believe this ticket should be prioritized as HIGH.<thought>"
        }
    ],
```

这段代码使用了多线程方法来并发链接 Jira 问题。这将大大减少与项目中所有开放工单进行配对比较所需的时间，以确定它们是否相关。

```py
def check_issue_and_link_helper(args):
    key, data, primary_issue_key, primary_issue_data = args
    if key != primary_issue_key and \
    llm_check_ticket_match(primary_issue_data, data):
        jira_utils.link_jira_issue(primary_issue_key, key) 

def find_related_tickets(primary_issue_key, primary_issue_data, issues):
    args = [(key, data, primary_issue_key, primary_issue_data) for key, data in issues.items()]
    with concurrent.futures.ThreadPoolExecutor(os.cpu_count()) as executor:
        executor.map(check_issue_and_link_helper, args)

def llm_check_ticket_match(ticket1, ticket2):
    llm_result = linking_model.run_llm(f"<ticket1>{ticket1}<ticket1><ticket2>{ticket2}<ticket2>")
    if ((result := jira_utils.extract_tag_helper(llm_result))) \
    and (result == 'True'):
        return True 
```

工具的一个示例工作流程：创建工单并进行分诊。

![](../Images/0f3c81d705a4d1f7191dadb25d8da9e7.png)

工作工单创建和分诊（图片由作者提供）

这些操作的结果被记录在 Jira 工单中。相关的工单已被自动链接，用户故事、验收标准、优先级和思考过程已作为 Jira 评论进行记录。

![](../Images/c890e01fb7882abe71c735539126e3e9.png)

已链接的工单、用户故事、验收标准和优先级（图片由作者提供）

我们可以在 Docker 容器的打印语句中看到代理的中间步骤。

![](../Images/b69b00afcdbde221f214e9a7692fac6c.png)

代理输出的分诊步骤（图片由作者提供）

# 5. Jira API 示例

![](../Images/4a8dc1971040892936d3712169b7fea6.png)

AI Jira 助手正在寻找进行中的工单（图片由作者提供）

在本项目中，所有我明确使用的[Jira REST API](https://developer.atlassian.com/server/jira/platform/rest/v10002/intro/#gettingstarted)示例都已在下方列出，供参考。

用于解析模型结果的正则表达式提取辅助函数也包含在内。此外，还有一个 Jira 的 Python SDK，尽管在此示例中我选择使用了 requests 库，以便更容易地转化为其他编程语言。

# 6. 下一步

自然的下一步是通过与源代码管理系统集成来实现代码生成，从而实现几乎完全自动化的软件开发生命周期。通过在此过程中加入人工参与，这可能是一个可行的解决方案。

我们已经可以看到，AI 代码生成正在对企业产生影响——如果业务日常任务可以部分自动化，那么软件开发人员/产品实践者就可以专注于更有趣和更有意义的工作。

如果这篇文章引起了广泛的兴趣，或许我可以将其作为后续项目进行研究。

[](https://uk.pcmag.com/ai/155094/ai-now-writes-over-25-of-code-at-google?source=post_page-----fb903468bff6--------------------------------) [## AI 现在在 Google 写超过 25% 的代码

### 谷歌CEO桑达尔·皮查伊表示，公司正在使用AI编写代码，然后由工程师进行审核。但这会如何发展……

[https://uk.pcmag.com/ai/155094/ai-now-writes-over-25-of-code-at-google?source=post_page-----fb903468bff6--------------------------------](https://uk.pcmag.com/ai/155094/ai-now-writes-over-25-of-code-at-google?source=post_page-----fb903468bff6--------------------------------)

我希望你觉得这篇文章有启发性，正如承诺的那样——你可以在Github仓库[这里](https://github.com/lewisExternal/Custom-AI-Jira-Agent/tree/main)找到所有代码，也可以随时在[LinkedIn](https://www.linkedin.com/in/lewisjames1/)上与我联系。

# 参考资料

+   [https://google.github.io/mesop/getting-started/quickstart/#starter-kit](https://google.github.io/mesop/getting-started/quickstart/#starter-kit)

+   [https://www.django-rest-framework.org/#example](https://www.django-rest-framework.org/#example)

+   [https://blog.logrocket.com/dockerizing-django-app/](https://blog.logrocket.com/dockerizing-django-app/)

*除非另有说明，所有图片均由作者提供。*
