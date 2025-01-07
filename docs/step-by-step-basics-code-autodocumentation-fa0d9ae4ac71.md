# 逐步基础：代码自动文档生成

> 原文：[https://towardsdatascience.com/step-by-step-basics-code-autodocumentation-fa0d9ae4ac71?source=collection_archive---------2-----------------------#2024-03-22](https://towardsdatascience.com/step-by-step-basics-code-autodocumentation-fa0d9ae4ac71?source=collection_archive---------2-----------------------#2024-03-22)

## 使用 Sphinx 生成完美的 Python 代码文档

[](https://medium.com/@lucydickinson?source=post_page---byline--fa0d9ae4ac71--------------------------------)[![Lucy Dickinson](../Images/5a075bb38f9133678d55a26b2683729f.png)](https://medium.com/@lucydickinson?source=post_page---byline--fa0d9ae4ac71--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa0d9ae4ac71--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fa0d9ae4ac71--------------------------------) [Lucy Dickinson](https://medium.com/@lucydickinson?source=post_page---byline--fa0d9ae4ac71--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa0d9ae4ac71--------------------------------) ·阅读时间：9分钟·2024年3月22日

--

![](../Images/0aa56838fa603355f02b854e8a8f9352.png)

图片来源：[Dustin Humes](https://unsplash.com/@dustinhumes_photography?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

> 你可以仅通过几步简单的操作，利用 docstring 构建美观、标准化和风格化的文档。

数据科学家在项目中承担着许多职责，其中一个通常会被拖延到最后的就是文档编写。也许你很勤奋地为类和函数编写了 docstring（做得好！）——但这应该是文档的最终形式吗？

在我看来，文档应该独立于代码存在。你的团队（或几个月后的你）不应该需要翻阅 Python 模块中的成百上千行代码才能理解其中的内容。你可以仅通过几步简单的操作，利用 docstring 构建美观、标准化和风格化的文档，让你的项目自我表达。

在本文中，我将重点介绍如何使用 [Sphinx 框架](https://www.sphinx-doc.org/en/master/index.html) 来自动生成 Python 模块的文档。我还将使用 [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/) 项目模板，并在 Visual Studio Code (VS Code) 中进行，因为它与 Sphinx 的无缝集成以及标准化的目录结构。虽然 [官方 Sphinx 教程文档](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/index.html) 是希望深入学习这一主题的绝佳资源，但本文的目标是提供一个帮助你快速入门的指南，带你完成关键步骤。祝你阅读愉快 :)

## 关于 docstring 的说明

良好的文档的关键是 docstring。这些是每个类、类方法和函数中的注释块，用于描述代码的性质，以及输入、输出和引发的错误。有三种核心的 docstring 格式，它们分别是 Google、reStructuredText (reST) 和 NumPy。它们包含相同的信息，唯一的区别是格式不同。你可以在[这里](https://realpython.com/documenting-python-code/#docstring-formats)查看每种 docstring 格式的示例。

我将使用 Google docstring 格式，因为它比其他格式更易于阅读且占用空间更小。下面的代码块是一个典型的 Google docstring 示例：

```py
"""Description of the function, class or method etc.

Args:
    varA (str): Description of varA
    varB (bool): Description of varB

Returns:
    list: Description of returned list

Raises:
    ValueError: Description of raised error
"""
```

*顶级技巧：在 VS Code 中下载‘autoDocstring — Python Docstring 生成器’，当你输入三个双引号（即 docstring 开始符号）时，它将自动生成 docstring。记得在生成 docstring 之前先完成函数编写，这样所有输入/输出/错误都会包含在为你生成的 docstring 模板中！*

让我们开始编写文档吧！

为了演示的目的，我创建了一个名为 `demo.py` 的 Python 模块，其中包含一个类和三个基本函数（除了一个函数，其他都注释了 docstring）。就是这个模块，我将在本文中为其编写文档。以下是 demo.py 模块的内容：

![](../Images/ea51927a8000f6c5b5725d577acb8c78.png)

demo.py 模块的内容需要记录。使用 VS Code 中的 CodeSnap 扩展截图。

## 1. 设置

首先，设置好一切。你需要安装 VS Code 并设置一个新项目，以及安装 Sphinx。这里有几个选项。你可以选择 a) 使用 Cookiecutter 设置一个新项目（在此过程中会自动生成相关的 Sphinx 设置和标准化目录），或者 b) 创建你自己的项目结构并单独安装 sphinx。

**选项 A — 安装 Cookiecutter**

在终端中，执行 pip install Cookiecutter，然后创建一个新项目：

```py
pip install cookiecutter
cookiecutter https://github.com/drivendata/cookiecutter-data-science
```

接下来，回答终端窗口中出现的问题，你的新项目将会被创建。Sphinx 框架将存储在项目的 /docs 目录中。

**选项 B — Sphinx 快速启动**

如果 Cookiecutter 模板不符合你的需求，你可以从头开始创建自己的项目结构并安装 sphinx。最好在一个文档目录中安装 sphinx。可以在终端中执行：

```py
mkdir docs
cd docs

pip install sphinx
sphinx-quickstart
```

## 2. 理解 Sphinx 文件夹结构

在你通过上述方式安装了 Sphinx 之后，项目中的文档目录下会出现一些文件。`conf.py` 文件是关键的配置文件，你需要编辑它以定制你的文档 —— 详细内容将在下一节介绍。`index.rst` 文件作为文档的目录。你可以在[这里](https://sphinx-rtd-tutorial.readthedocs.io/en/latest/build-the-docs.html)找到关于 `index.rst` 文件的更多信息。`getting-started.rst` 和 `commands.rst` 文件是推荐的文档模板。如果需要，你可以删除这些文件。`make` 文件（`make.bat` 和 `Makefile`）用于实际生成文档。你无需编辑这些文件，但在准备好生成文档时会在终端窗口调用它们。

![](../Images/34322e8b906de18a36631968b18687fb.png)

默认安装的 Sphinx 文件

## 3. Conf.py 文件

配置文件是魔法发生的地方。这个文件在构建过程中使用，因此确保正确设置它至关重要。以下是修改 `conf.py` 文件的一些步骤：

**取消注释 sys.path 行**（我的设置中的第 20 行）：

```py
#  sys.path.insert(0,  os.path.abspath('.')) 
```

**更改 os.path.abspath 的路径**，使其指向你想要记录的代码的相对位置（相对于 `conf.py` 文件）。例如，我想要记录的 Python 模块位于我项目的 `src/` 目录中。因此，我会将 os.path.abspath 更改为指向位于 `conf.py` 文件父文件夹中的 `/src` 目录。你可以使用 `.` 和 `/` 语法指定相对位置：

```py
sys.path.insert(0,  os.path.abspath('../src'))

"""
# you can use the following syntax to specify relative locations:

'.' # current path of conf.py
'..' # parent path of conf.py
'../..' # parent of the parent path of conf.py
"""
```

![](../Images/24776be77700370824f484404b8be450.png)

文档文件夹中包含 Python 模块的目录的相对位置。在这个示例中，‘demo.py’ 是要记录的模块，位于 `src/data/` 目录中。

**添加相关扩展**。你需要在 `conf.py` 文件中添加一些扩展，以便在创建文档时获得额外的功能。这些扩展是可选的，你可以在[这里](https://www.sphinx-doc.org/en/master/usage/extensions/index.html)探索可用的不同扩展。我建议至少添加以下五个扩展：

+   [**sphinx.ext.autodoc**](https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html)— 使用来自文档字符串的文档

+   [**autodocsumm**](https://pypi.org/project/autodocsumm/)— 通过仅列出文档字符串摘要来在 HTML 页面的顶部生成一个表格汇总。当你有很多文档字符串时非常有用。*注意：你需要在终端中使用 pip 安装 autodocsumm。*

+   [**sphinx.ext.napoleon**](https://www.sphinx-doc.org/en/master/usage/extensions/napoleon.html) — 使 Sphinx 能够解析 Google 风格的文档字符串

+   [**sphinx.ext.viewcode**](https://www.sphinx-doc.org/en/master/usage/extensions/viewcode.html) — 为每个模块添加一个链接，指向包含源代码的 HTML 页面

+   [**sphinx.ext.coverage**](https://www.sphinx-doc.org/en/master/usage/extensions/coverage.html) — 提供类/函数等有多少包含文档字符串的总结。良好的覆盖率表明代码库得到了很好的解释。

下面是如何在 `conf.py` 文件中包含这些扩展（在我的设置中是第 29 行）：

```py
#  add in the extension names to the empty list variable 'extensions'
extensions = [
      'sphinx.ext.autodoc', 
      'sphinx.ext.napoleon', 
      'autodocsumm', 
      'sphinx.ext.coverage'
]

# add in this line for the autosummary functionality
auto_doc_default_options = {'autosummary': True}
```

**更改主题**。文档的默认主题相当简洁，尽管你可能更喜欢通过将 'html_theme' 变量（在我的设置中是第 94 行）从 'default' 更改为标准的 [主题选项](https://www.sphinx-doc.org/en/master/usage/theming.html) 或一些 [第三方选项](https://sphinx-themes.org/)来尝试不同的选项。在这个示例中，我将展示默认主题和“Read the Docs”主题。

```py
html_theme = 'sphinx_rtd_theme' # read the docs theme. This variable is 'default' by default.
```

*注意：你需要通过 pip 安装任何非标准（第三方）主题。*

## 4\. 生成 html 页面

现在你的 `conf.py` 文件已经设置完毕，且代码中有了精彩的文档字符串，我们可以开始抓取并生成一些 html 页面了。

**生成 Python 包的 .rst 文件**

这些文件是 html 页面的前置文件，是 Sphinx 的本地格式。在生成 html 文件之前，需要先生成这些文件。你将使用 [sphinx.apidoc](https://www.sphinx-doc.org/en/master/man/sphinx-apidoc.html) 命令，它利用 autodoc 扩展来定位你在 `conf.py` 文件中指定的 sys.path 位置下的所有 Python 模块（例如任何 .py 文件）。在使用 apidoc 命令时，有一些可选参数可以包含，详情请参阅 [文档](https://www.sphinx-doc.org/en/master/man/sphinx-apidoc.html)，但我使用了以下模板：

*注意：在终端中，切换到项目根目录以运行以下代码。*

```py
sphinx-apidoc -f -o output_dir module_dir/
```

> **-f**（强制覆盖任何已生成的文件）。
> 
> **-o output_dir**（放置输出文件的目录。如果该目录不存在，将会创建）。注意：将 'output_dir' 替换为你选择的目录名称。我将我的设置为 /docs 目录。
> 
> **module_dir**（Python 包的位置，用于文档化）

运行该命令后，docs 文件夹中应该会生成新的 .rst 文件。

![](../Images/84b4615becbbe6f7809c3c9c02672edf.png)

运行 sphinx-apidoc 命令生成 .rst 文件后，文档文件夹的内容

请注意，已经生成了两个新的 .rst 文件：`data.rst` 和 `modules.rst`。除了 `modules.rst`，对于每个包含至少一个 Python 模块的目录，将会生成一个 .rst 文件。在我的示例中，由于我将 demo.py 文件保存在 src/data 目录中，因此生成了 `data.rst`。如果在你指定的 `conf.py` 文件中的 sys.path 位置包含多个 Python 模块目录，那么将生成多个 .rst 文件。*注意：这些文件目前还不包含抓取的文档，它们只包含 autodoc 所需的信息，以便在下一步生成 html 文件。*

**编辑 index.rst 文件**

请记住，`index.rst` 作为目录页，因此我们必须编辑此文件，以包括我们希望文档化的所有 Python 模块。幸运的是，`modules.rst` 引用了在 `sys.path` 中识别到的所有 Python 模块的源位置，因此你可以简单地将此文件添加到 `index.rst`。

为此，打开 `index.rst` 文件，并在 toctree（目录树）部分下方添加‘modules’。确保在 :maxdepth: 参数和 .rst 文件名称之间有一行空白。

*注意。‘getting-started’ 和 ‘commands’ 将已经出现在 `index.rst` 文件中。如果你不希望生成 HTML 页面，可以将它们从文件中删除（尽管 ‘getting-started’ 页面可能是个好主意！）*

![](../Images/8e03e40dede70fa331d9dc1a90c7fef3.png)

`index.rst` 文件的内容。我已添加‘modules’，以便在 HTML 生成过程中使用 `modules.rst` 文件。

**创建 HTML 文件**

现在我们可以使用文档目录中的 make 文件来构建 HTML 文件。这些文件将出现在文档文件夹中的 **_build/html/** 目录下。如果你下载了‘HTML Preview’扩展程序，可以在 VS Code 中预览这些文件。

切换到 `make.bat` 文件所在的目录，并在 cmd 终端中运行以下命令：

```py
make html
```

*注意*。*如果你使用的是 Windows PowerShell 终端（而非 cmd），请使用以下语法：*

```py
.\make.bat html
```

***顶级提示***。如果在使用 `make html` 命令时出现警告，提示 ‘autodoc: failed to import module’，这很可能是因为 autodoc 无法找到你的模块，因为 `sys.path` 在 `conf.py` 中未正确配置。确保它指向你的 Python 模块所在的目录。*

**编辑 HTML 文件**

如果你希望编辑文档字符串并将更改更新到 HTML 文件中，可以使用以下命令：

```py
make clean html
```

## 让我们来看看我们的文档！

如我之前提到的，我已经为我的 Python 模块 `demo.py` 创建了两种不同主题的文档，如下图所示；‘默认’（左侧图片）和‘Read the Docs’（右侧图片）。内容是相同的，但外观和感觉不同。让我们注意一下核心特点：

+   左侧的导航栏

+   模块所有类或函数的摘要，以表格形式显示在页面顶部（感谢 ‘autodocsumm’ 扩展）

+   所有函数和类的文档字符串组件的详细列表，位于总结下方

![](../Images/c7e255289a5c54caa52b9ecefa753d20.png)![](../Images/e7850408e20f8add5d9dbac68615fe35.png)

使用默认主题（左侧图片）和 Read the Docs 主题（右侧图片）的 Python 模块文档页面示例，通过 Sphinx 生成。

一旦你创建了 html 页面，你会注意到会生成多种层次的 html 页面。这些页面包括主页以及每个包和模块的页面。浏览这些页面，熟悉它们的结构，并阅读官方文档，了解如何进一步自定义它们。

例如，如果你希望在文档中查看每个函数的原始代码，可以将扩展 ‘sphinx.ext.viewcode’ 添加到 `conf.py` 文件中。这将为每个函数或类方法添加一个超链接，点击后可以显示原始代码，方便查看，无需深入代码库。

## 结语

就这样。通过几个简单的步骤使用 Sphinx 创建的 Python 模块文档，既简单又美观！希望你在了解自动化文档生成的过程中有所收获，并认为它是一个值得在项目中实现的有用工具。如果你有任何有用的建议，欢迎留言评论 :)
