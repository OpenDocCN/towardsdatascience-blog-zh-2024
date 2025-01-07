# 动态 GitHub Pages - 面板 (pyodide-worker)

> 原文：[https://towardsdatascience.com/dynamic-github-pages-panel-pyodide-worker-17c56ca88455?source=collection_archive---------8-----------------------#2024-10-04](https://towardsdatascience.com/dynamic-github-pages-panel-pyodide-worker-17c56ca88455?source=collection_archive---------8-----------------------#2024-10-04)

## 如何创建交互式且客户端运行的 GitHub Pages？这是一个雄心勃勃的构建大厦的第一步

[](https://medium.com/@petoulemonde?source=post_page---byline--17c56ca88455--------------------------------)[![Pierre-Etienne Toulemonde](../Images/95fdda47e79e3c194fc96ef29f3a2188.png)](https://medium.com/@petoulemonde?source=post_page---byline--17c56ca88455--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17c56ca88455--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--17c56ca88455--------------------------------) [Pierre-Etienne Toulemonde](https://medium.com/@petoulemonde?source=post_page---byline--17c56ca88455--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17c56ca88455--------------------------------) ·阅读时长10分钟·2024年10月4日

--

![](../Images/462c2466e934eadb68aada8cc91dbecb.png)

摄影师：[Joshua Sortino](https://unsplash.com/@sortino?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 供图

**索引**：

1.  引言

1.  方法

1.  结果

1.  讨论

# 1. 引言

这些年来，我一直梦想着拥有一个漂亮的作品集，展示我作为一名初学数据科学家的项目。在经过将近一年的反思、试验、失败和一些成功之后，我在 GitHub Pages 上创建了我的第一个作品集。为这个个人成就感到高兴后，我写了一篇文章来与社区分享我研究的成果，文章可以在[这里](/full-guide-to-build-a-professionnal-portfolio-with-python-markdown-git-and-github-page-for-66d12f7859f0)找到。

这个作品集是使用 mkdocs Python 包创建的。Mkdocs 是一个非常适合此类项目的工具包，但也有一些不足之处，主要是完全缺乏与读者的互动。随着我在创建作品集的过程中深入，缺乏互动的情况让我感到越来越沮丧。当时我的限制（至今依然如此）是要求一切都能够免费执行并且在客户端进行，因此 GitHub Pages 解决方案完全符合我的需求。

随着我在静态作品集创建过程中的深入，拥有一个动态作品集系统的想法越来越浮现在我脑海中。我的目标很明确：寻找一种解决方案，创建一个可以与读者互动的 GitHub Pages 作品集。在我的研究中，我几乎没有找到处理这个问题的相关文章，于是我开始寻找相关的软件、包和代码片段来解决这个问题。

本文的研究问题是：如何创建一个动态的全客户端网站？我的技术限制如下：使用GitHub Pages。

关于仪表板包，我选择将自己局限于holoviz套件中的Panel，因为它是一个很棒的包，我希望能在这个包上提升自己的技能。

就本文而言，我搜索并找到了许多或多或少相似的解决方案。因此，本文是一个系列文章的第一篇，旨在展示针对同一研究问题的不同解决方案。

但是，动态GitHub页面有什么意义呢？GitHub Pages是一个非常有趣的解决方案，用于组织/项目展示，100%由GitHub托管，免费，配置最小且无需服务器维护。能够包含动态内容是一种强有力的方式来展示你的组织或项目。对于数据专业人士来说，这是一个非常有用的解决方案，能够快速生成动态且有趣的作品集。

Holoviz是一个令人兴奋且极其丰富的包集合。它是一个完整的可视化和仪表板解决方案，对于中等规模和大数据非常强大。这个解决方案支持所有主要的输入数据处理包（polars、pandas、dask、X-ray等），并提供了高层次的语法，能够以最少的代码生成交互式可视化。该包还允许你自定义输出，特别是可以选择你的可视化后端，例如pandas（如果你想了解更多，我写过[一篇文章](https://medium.com/towards-data-science/the-power-of-pandas-plots-backends-6a08d52071d2)）。要了解更多关于这个强大包集合的信息，我建议阅读[这篇文章](/3-ways-to-build-a-panel-visualization-dashboard-6e14148f529d)。

# 2. 方法

对于这项工作，我的技术背景带来了一些应急情况：

+   我目前还不能够熟练地使用JavaScript编写完整的脚本并直接用JavaScript写代码，

+   我将使用Panel作为仪表板包，以提高我的技能。如果有需要，我不会排除使用其他仪表板包（例如Dash、streamlit、NiceGUI等）重复练习。不过，这不是我的优先事项。

对于本文，我的技术环境如下：

+   python包：Panel

我使用conda和VSCode进行脚本和环境管理。如果你使用其他解决方案，不用担心，这不会影响其余部分。

在我的研究过程中，我从我的研究中找出了3个不同复杂度和视觉吸引力的脚本，这些将作为很好的测试标准：

+   一个简单的应用叫做“简单应用”：

```py
import panel as pn

pn.extension(design="material")

slider = pn.widgets.IntSlider(name="Select a value", value=10, start=0, end=100)
pn.Column(
    "# Hello Panel + Quarto!",
    pn.rx("You selected: {}").format(slider),
).servable()
```

[来源](https://awesome-panel.github.io/holoviz-quarto/getting-started.html)

+   一个更复杂的应用叫做“大应用”：

```py
import io
import panel as pn
import pandas as pd
import hvplot.pandas

pn.extension(template='fast')

pn.state.template.title = 'hvPlot Explorer'

upload = pn.widgets.FileInput(name='Upload file', height=50)
select = pn.widgets.Select(options={
    'Penguins': 'https://raw.githubusercontent.com/mwaskom/seaborn-data/master/penguins.csv',
    'Diamonds': 'https://raw.githubusercontent.com/mwaskom/seaborn-data/master/diamonds.csv',
    'Titanic': 'https://raw.githubusercontent.com/mwaskom/seaborn-data/master/titanic.csv',
    'MPG': 'https://raw.githubusercontent.com/mwaskom/seaborn-data/master/mpg.csv'
})

def add_data(event):
    b = io.BytesIO()
    upload.save(b)
    b.seek(0)
    name = '.'.join(upload.filename.split('.')[:-1])
    select.options[name] = b
    select.param.trigger('options')
    select.value = b

upload.param.watch(add_data, 'filename')

def explore(csv):
    df = pd.read_csv(csv)
    explorer = hvplot.explorer(df)
    def plot_code(**kwargs):
        code = f'```python\n{explorer.plot_code()}\n```py'
        return pn.pane.Markdown(code, sizing_mode='stretch_width')
    return pn.Column(
        explorer,
        '**Code**:',
        pn.bind(plot_code, **explorer.param.objects())
    )

widgets = pn.Column(
    "Select an existing dataset or upload one of your own CSV files and start exploring your data.",
    pn.Row(
        select,
        upload,
    )
).servable()  

output = pn.panel(pn.bind(explore, select)).servable()

pn.Column(widgets, output)
```

[来源](https://panel.holoviz.org/getting_started/build_app.html)

+   一个使用“Material”Panel模板的仪表板，我称之为“material dashboard”：

```py
import hvplot.pandas
import numpy as np
import pandas as pd
import panel as pn

PRIMARY_COLOR = "#0072B5"
SECONDARY_COLOR = "#B54300"
CSV_FILE = (
    "https://raw.githubusercontent.com/holoviz/panel/main/examples/assets/occupancy.csv"
)

pn.extension(design="material", sizing_mode="stretch_width")

@pn.cache
def get_data():
  return pd.read_csv(CSV_FILE, parse_dates=["date"], index_col="date")

data = get_data()

data.tail()

def transform_data(variable, window, sigma):
    """Calculates the rolling average and identifies outliers"""
    avg = data[variable].rolling(window=window).mean()
    residual = data[variable] - avg
    std = residual.rolling(window=window).std()
    outliers = np.abs(residual) > std * sigma
    return avg, avg[outliers]

def get_plot(variable="Temperature", window=30, sigma=10):
    """Plots the rolling average and the outliers"""
    avg, highlight = transform_data(variable, window, sigma)
    return avg.hvplot(
        height=300, legend=False, color=PRIMARY_COLOR
    ) * highlight.hvplot.scatter(color=SECONDARY_COLOR, padding=0.1, legend=False)

get_plot(variable='Temperature', window=20, sigma=10)

variable_widget = pn.widgets.Select(name="variable", value="Temperature", options=list(data.columns))
window_widget = pn.widgets.IntSlider(name="window", value=30, start=1, end=60)
sigma_widget = pn.widgets.IntSlider(name="sigma", value=10, start=0, end=20)

bound_plot = pn.bind(
    get_plot, variable=variable_widget, window=window_widget, sigma=sigma_widget
)

widgets = pn.Column(variable_widget, window_widget, sigma_widget, sizing_mode="fixed", width=300)
pn.Column(widgets, bound_plot)

pn.template.MaterialTemplate(
    site="Panel",
    title="Getting Started App",
    sidebar=[variable_widget, window_widget, sigma_widget],
    main=[bound_plot],
).servable();
```

[来源](https://panel.holoviz.org/getting_started/build_app.html)

为了实现同时部署 Web 和 GitHub Pages 的目标，我将测试每个部署的实现：

+   在本地 Python 服务器上，通过 `python -m http.server` 生成，

+   GitHub Pages。

## **可视化仪表板应具有的最佳操作**

在开始测试之前，我需要有一个基准，了解每个应用程序在理想情况下应该如何工作。为此，我使用：

```py
panel serve simple_app.py --dev
```

解释：

+   `panel serve simple_app.py` ：可视化仪表板

+   ` — dev` ：每次修改底层文件时重新加载仪表板（可能需要安装一个或多个其他包，特别是用于跟踪底层文件是否已被修改）

这是预期的结果：

+   简单应用：

![](../Images/f01f08fb29001d1607a287353acda296.png)

简单应用可视化，图片由作者提供

+   大应用：

![](../Images/3451016c654c5ae9a8506c4249b3a130.png)

大应用可视化，图片由作者提供

+   材料仪表板：

![](../Images/51fbe807607905dd269a0725744af1f1.png)

材料仪表板可视化，图片由作者提供

这些可视化将帮助我在部署测试期间检查一切是否顺利运行，且时间符合预期。

# **结果**

## **第一步：将 Python 脚本转化为 HTML 交互式脚本**

Panel 包将 Python 脚本转换成 HTML 应用程序，只需一行代码：

```py
panel convert simple_app.py --to pyodide-worker --out docs
```

解释：

+   `panel convert` ：Python 脚本转换 Panel 包命令

+   `simple_app.py` ：待转换的 Python 脚本

+   ` — to pyodide-worker` ：Panel 可以将 Python 应用程序转化为几种支持类型，便于集成到 HTML 生产中。在本文中，我关注的是输出的 `pyodide-worker`

+   ` — out docs` ：输出文件夹，用于存放生成的两个文件（HTML 和 JavaScript）。

在 docs 文件夹（代码行中的‘ — to docs’部分），应出现两个与 Python 脚本同名、扩展名分别为‘html’和‘js’的文件。这些脚本将使我们能够将应用程序集成到 Web 内容中。这种代码转换（从 Python 到 HTML 再到 JavaScript）得益于 WebAssembly。Pyodide 是 CPython 移植到 WebAssembly/Emscripten 的版本（更多信息请见：[https://pyodide.org/en/stable/](https://pyodide.org/en/stable/)）。

如果你不熟悉 WebAssembly，我建议你阅读 [Mozilla 的文章](https://developer.mozilla.org/en-US/docs/WebAssembly)来深入了解。我将会写一篇关于 WebAssembly 的历史、范围及其潜在影响的文章，我认为 WebAssembly 将在未来几年内带来真正的变革。

## **第一次测试：本地 Web 服务器部署**

1. 使用 Python 模拟本地 Web 服务器：`python -m http.server`。此命令将返回一个本地 URL，供浏览器连接（如：127.0.0.1:8000）。

2. 点击我们 Python 应用程序的 HTML 脚本

*信息*：在通过 HTML 服务器浏览文件时，为了在打开文件夹时自动启动所需的应用程序，HTML 和 JavaScript 文件应命名为 ‘index.html’ 和 ‘index.js’。示例：

```py
app/
|- index.html
|- index.js
```

当应用文件夹在本地 HTML 服务器中打开时，index.html 会自动启动。

本地 HTML 服务器上的部署测试报告：

+   简单应用：✅

+   大型应用：✅

+   Material Dashboard:✅

在测试上述 3 个应用程序之后，这个方案在所有应用程序中都完美运行，加载和使用应用程序时没有任何速度损失。

## **第二次测试：GitHub Pages 部署**

在本文中，我将快速回顾 GitHub Pages 在 GitHub 上的配置部分，正如我在[上一篇文章](/full-guide-to-build-a-professionnal-portfolio-with-python-markdown-git-and-github-page-for-66d12f7859f0)中详细描述的那样。

1.  第一步的警告：托管 HTML 和 JavaScript 脚本的 ‘docs’ 文件夹必须命名为 ‘docs’，并且放置在 git 仓库的根目录下。这是部署应用到 GitHub Pages 的 2 个前提条件。文件夹的名称和位置不能更改。

1.  2 种可能性：

    2.a. 将应用程序文件重命名为 ‘index.html’ 和 ‘index.js’，并将其直接放置在 ‘docs’ 文件夹中。此方案将直接在应用程序上打开您的 GitHub Pages。

    2.b. 在 ‘docs’ 中直接创建一个 ‘index.html’ 文件，并添加指向应用程序 HTML 文件的路径。

这是我在部署测试中创建的‘index.html’文件的内容：

```py
1\. <a href="https://petoulemonde.github.io/article_dynamic_webpages/simple_app_pyodide/simple_app.html">Simple app</a>
<br/>
2\. <a href="https://petoulemonde.github.io/article_dynamic_webpages/big_app_pyodide/big_app.html">Big app</a>
<br/>
3\. <a href="https://petoulemonde.github.io/article_dynamic_webpages/material_dashboard_pyodide/material_dashboard.html">Material dashboard</a>
```

说明：

+   `https://petoulemonde.github.io/`: 我的作品集的 URL

+   `article_dynamic_webpages/`: 我的工作仓库，用于此文章

+   `simple_app_pyodide/simple_app.html`: 要打开的 HTML 文件夹/应用程序。在仓库中，该文件存储在 **docs**/simple_app_pyodide/simple_app.html，但在绝对路径中不要提及 ‘docs’。为什么文件浏览器和链接之间有这个差异？GitHub 是从 docs 文件夹进行部署的，‘docs’ 是其工作根目录。

3\. 推送到远程仓库（在我之前的示例中，是‘article_dynamic_webpages’仓库）。

4\. 在仓库中启用 GitHub 项目页面的创建。在配置页面中，以下是如何配置 GitHub 页面：

![](../Images/0a1c760fffbea7c6d821c578de2c7436.png)

GitHub Pages 部署配置，图片由作者提供

这是‘docs’文件夹对于我们部署应用程序至关重要的地方，否则我们将无法在‘master’分支中进行任何部署操作。

测试报告：在 GitHub Pages 上部署：

+   简单应用：✅

+   大型应用：✅

+   Material dashboard: ✅

关于解决方案2.b.：这是一个特别有趣的解决方案，因为它允许我们为网站或个人作品集创建一个静态主页，然后将其分发到特殊的动态项目页面。这为静态和动态的GitHub Pages打开了大门，使用mkdocs处理静态部分及其精美的设计，使用Panel处理交互式页面。我可能会将下篇文章写成关于这种mkdocs + Panel（pyodide-worker）部署解决方案的内容，非常高兴能再次把你列为我的读者。

## **遇到的问题**

目前测试过的仪表盘无法分发到站点/作品集的其他页面，因此识别出的唯一替代方案是创建一个静态主页，然后在站点内重新分发到仪表盘页面。是否可以在不使用静态页面的情况下创建一个包含多个页面的站点？答案是肯定的，因为仪表盘本身可以集成链接，包括指向同一站点上其他仪表盘的链接。

我已经修改了Material应用程序代码，添加了一个链接（添加了`pn.pane.HTML(…)`）：

```py
pn.template.MaterialTemplate(
  site="Panel",
  title="Getting Started App",
  sidebar=[
    pn.pane.HTML('<a href="127.0.0.1:8000/docs/big_app_pyodide/big_app.html">Big app</a>'), # New line !
    variable_widget,
    window_widget,
    sigma_widget],
  main=[bound_plot],
).servable();
```

这会将一个链接添加到应用程序的侧边栏：

![](../Images/aefbb332533fce49f06f2b038e7884a2.png)

带有链接可视化的Material仪表盘，图片来自作者

尽管这里的证明看起来不太完美，但它表明一个仪表盘可以集成指向其他页面的链接，因此仅使用Panel也可以创建一个包含多个页面的站点——太棒了！实际上，我在这里主要专注于Panel的仪表盘部分，但Panel也可以用来创建静态页面，因此即使不掌握mkdocs，你也可以通过结合静态和动态元素来创建多个页面的站点。

# **讨论**

Panel是一个非常有趣且强大的工具包，它使得创建动态网站变得简单，并能够托管在GitHub Pages上，尤其得益于WebAssembly的魔力。这个工具包真正让你可以专注于创建仪表盘，然后通过几行代码将仪表盘转换为网页内容。再加上GitHub Pages的易用性，Panel使得数据仪表盘的快速部署成为可能。

尽管这个解决方案很棒，但在我的测试过程中，我遇到了一些局限性。第一个问题是无法集成用户可编辑和可执行的代码。我希望能够让用户以自己的方式探索数据，通过与他们分享我编写的代码，使他们能够修改代码并以自己的方式探索数据。

第二个也是最后一个局限性是，定制仪表盘不像创建仪表盘那么容易。Hvplot通过*explorer*工具提供了一种可视化的解决方案来探索数据并创建图表。但一旦进入代码中，我发现定制起来有点困难。这个工具包在功能和强大性方面非常棒，我可能仍然缺乏一些技能，因此我认为这主要是由于我对这个工具包的练习不足，而非工具包本身的问题。

如果你已经看到这里，感谢你的关注！你在我上一篇文章上的评论非常有帮助。多亏了你，我发现了Quarto，并获得了一些关于如何让我的文章对你，读者，更加有趣的想法。请留下评论告诉我如何改进我的文章，无论是从技术上还是视觉上，这样我下次就能为你写出更加有趣的文章。

祝你在Python冒险中好运！

皮埃尔·埃蒂安
