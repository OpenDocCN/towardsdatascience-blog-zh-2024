# 完整指南：如何使用 Python、Markdown、Git 和 GitHub Pages 构建专业作品集

> 原文：[https://towardsdatascience.com/full-guide-to-build-a-professionnal-portfolio-with-python-markdown-git-and-github-page-for-66d12f7859f0?source=collection_archive---------0-----------------------#2024-07-20](https://towardsdatascience.com/full-guide-to-build-a-professionnal-portfolio-with-python-markdown-git-and-github-page-for-66d12f7859f0?source=collection_archive---------0-----------------------#2024-07-20)

## 如何通过高质量的作品集来提升你作为开发者或数据工作者的形象

[](https://medium.com/@petoulemonde?source=post_page---byline--66d12f7859f0--------------------------------)[![Pierre-Etienne Toulemonde](../Images/95fdda47e79e3c194fc96ef29f3a2188.png)](https://medium.com/@petoulemonde?source=post_page---byline--66d12f7859f0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--66d12f7859f0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--66d12f7859f0--------------------------------) [Pierre-Etienne Toulemonde](https://medium.com/@petoulemonde?source=post_page---byline--66d12f7859f0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--66d12f7859f0--------------------------------) ·阅读时长 9 分钟·2024年7月20日

--

![](../Images/9205955a834dc7af6a9ba134a2325593.png)

图片来源：[Product School](https://unsplash.com/@productschool?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 索引

1.  我的前一个作品集

1.  冒险开始

1.  代码

    3.1\. 所需软件

    3.2\. 配置本地环境

    3.3\. 创建工作文件夹

    3.4\. 准备 Git

    3.5\. 填充网站内容

    3.6\. 网站配置

    3.7\. 最终的 *mkdocs.yml* 文件

    3.8\. 最终文件结构

    3.9\. 部署工作

1.  GitHub Pages 配置

1.  迭代改进

1.  我的 2024 年版作品集！

# **我的前一个作品集**

2023年，我已经编写数据项目代码两年，开始着手创建我的第一个作品集，以展示我的数据科学项目。我发现了[Matt Chapman 的 TDS 文章](/the-portfolio-that-got-me-a-data-scientist-job-513cc821bfe4)和[Matt Chapman 的作品集](https://mattschapman.github.io/)。这篇文章与我当时的技术知识（Python、Git）完全吻合。感谢 Matt Chapman 的文章，我开始了我的第一个作品集！于是我决定深入探索这个方案，并弄清楚如何实现。我发现了[Matt Chapman 使用的参考资料](https://medium.com/@evanca/set-up-your-portfolio-website-in-less-than-10-minutes-with-github-pages-d0efa8ff56fd)和[相应的仓库](https://github.com/evanca/quick-portfolio)。我利用这个参考资料创建了我的作品集。

到 2024 年，我发现我的旧作品集相比现有的作品集显得有些过时，并且对数据爱好者或招聘人员吸引力不足。通过探索社区中已有的项目，我发现了几个文档非常棒的项目。以下是激励我的 2 个链接：[基于 GitHub Pages 的多页面文档](https://github.com/aphp/edsnlp)，以及 [基于 GitHub Pages 的 JavaScript 作品集](https://github.com/KeeganFernandesWork/KeeganFernandesDataScience) 和 [相关 Medium 文章](https://keeganfdes03.medium.com/making-a-data-science-portfolio-using-github-pages-for-free-ee831d4dec68)。

# **冒险开始**

对于我的新版本作品集，我的标准是：一个免费的解决方案，且配置最少。浏览现有的文档和作品集后，我有几个选择：

+   选项 1：保持我之前作品集的相同视觉和架构

+   选项 2：分叉上述提到的 JavaScript 作品集

+   选项 3：使用 [mkdocs](https://www.mkdocs.org/) Python 包

由于我不使用 JavaScript 编程，我很快会在自定义方面受到限制，所以我决定放弃。我之前的作品集中使用单一的 GitHub Pages 并不足以提升我的作品集。在我的研究中，我发现了 2 个 mkdocs 子包，它们的视觉效果特别吸引我：[mkdocs-material](https://squidfunk.github.io/mkdocs-material/) 和 [just-the-docs](https://just-the-docs.com/)。我最终选择了 mkdocs，主要基于以下 3 个原因：

+   该包（mkdocs）易于使用，而且我了解 Python 和 Git，这正是所需的 2 种技术。

+   网站内容采用 Markdown 格式，且该包可以在我的 GitHub 仓库中自动生成网站，我只需最少的操作。

+   生成的网站既美观又实用。

Mkdocs-material 允许使用 Google 标签，非常适合跟踪我作品集的流量！

# **代码**

在这个项目开始时，我已经设置好了我的 GitHub Pages，创建了我的仓库，并为我的之前的作品集创建了虚拟环境。为了让每个人都能跟随并复现这篇文章，我决定从头开始。对于那些已经拥有 GitHub Pages 作品集的你们来说，你们已经熟悉 Git 和 Python，能够毫无问题地处理分支。

在这篇文章中，我将分享一些 URL 链接。我的目标是让你们充分理解代码的各个方面，并在必要时提供资源，帮助你们深入了解某个主题或解决我文章中未描述的错误。

## **所需软件**

对于这项工作，你至少需要在计算机上安装并配置 Python 和 Git，并拥有一个 GitHub 账户。个人而言，我在 VSCode 和 [集成到 PowerShell 的 Miniconda](https://stackoverflow.com/questions/64149680/how-can-i-activate-a-conda-environment-from-powershell) 中工作，这样我可以将脚本和终端放在同一屏幕上。关于 Git 配置，请参考 [Git 官网](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) 中的 *Your identity* 部分。

## **配置本地环境**

我使用的是 [Miniconda](https://docs.anaconda.com/miniconda/)。如果你使用相同的工具，你会看到‘*(base)>’* 元素。如果不是，这个元素代表当前的虚拟 Python 环境（*base* 是 Miniconda 的默认虚拟环境）。元素 `*working_folder*` 是终端的当前文件夹。

1\. 第一步是为作品集项目创建虚拟环境：

```py
(base)> conda create -n "portfolio_env" # Create the new virtual env named portfolio_env
(base)> conda activate portfolio_env # Activate the new virtual env, (base) become (portfolio_env)
```

2\. 在这个新环境中，我们需要安装以下 Python 包：

```py
(portfolio_env)> pip install mkdocs mkdocs-material
```

3\. 为了保证你环境的可复现性，我们导出依赖包：

```py
(portfolio_env)> conda env export > "environnement.yml" # Export the environment.yml file, to ensure conda env repoductibility (including the python version, the conda env configuration, … )
(portfolio_env)> conda list > "requirements.txt" # Export packages installed only
```

## **创建工作文件夹**

我的前一个作品集没有使用 mkdocs，所以我创建了 mkdocs 结构：

```py
(portfolio_env)> mkdocs new "<your GitHub username>.github.io"
```

将 *<your GitHub username>* 替换为你的 GitHub 用户名。接下来我将称这个文件夹为 *<your GitHub username>.github.io working_folder*。新文件夹将具有以下结构：

```py
<your GitHub username>.github.io
|- mkdocs.yml
|- environment.yml
|- requirements.txt
|- docs/
    |- index.md
```

要了解 mkdocs 包，你可以在 [这里](https://www.mkdocs.org/) 查阅文档。

## **准备 Git**

如果你已经有了 GitHub Pages，你可以克隆你的 *<your GitHub username>.github.io* 仓库并跳过这部分。下一步是创建本地 Git 仓库。

1.  创建一个名为 *readme.md* 的文件。这个文件是任何 Git 仓库的经典 README 文件。

1.  打开 Git 终端并创建本地仓库

```py
working_folder> git init # Initiate the local repo
working_folder> git add . # Save the readme file
working_folder> git commit -m "Initiate repo" # Commit changes
```

3\. 在你的 GitHub 账户上，创建一个名为 *<your GitHub username>.github.io* 的新仓库（将 *<your GitHub username>* 替换为你的 GitHub 用户名）

4\. 将本地仓库与远程仓库连接。在 Git 终端中：

```py
working_folder> git remote add github https://github.com/<your GitHub username>.github.io
```

如果你不熟悉 GitHub Pages，[GitHub Pages 网站](https://pages.github.com/) 将向你介绍它们，并解释为什么我使用 *<your GitHub username>.github.io* 作为仓库名称。

工作文件夹将具有以下结构：

```py
<your GitHub username>.github.io
|- .git
|- readme.md
|- mkdocs.yml
|- environment.yml
|- requirements.txt
|- docs
    |- index.md
```

## **为网站提供内容**

Mkdocs 允许你展示网站并动态包含修改，这样你可以随时看到你的网站变化。动态生成网站的代码：

```py
mkdocs serve
```

这个命令返回一个本地 URL（例如 *127.0.0.1:8000*），可以粘贴到浏览器中。

**readme.md 和 index.md**

README 文件对应于远程仓库的主页。当你使用 mkdocs 包创建工作文件夹时，它会创建一个 *docs/index.md* 文件，对应网站的首页。

**菜单**

第一步是配置网站的菜单（左侧面板，用于在页面之间导航）。在*working_folder/mkdocs.yml*文件中，这是*nav*部分：

```py
# Page tree: refer to mkdocs documentation
nav:
- Home: index.md
- Current project:
  - "Health open data catalog": catalogue-open-data-sante/index.md
- Previous data science projects:
  - "Predict Health Outcomes of Horses": horse_health_prediction_project/readme.md
  …
- Previous skills based projects:
  - "Predict US stocks closing movements": US_stocks_prediction_project/readme.md
  …
```

*主页*元素很重要：这是网站的主页。你可以选择将*readme.md*文件复制到*index.md*文件中，这样在GitHub仓库和网站上都会有相同的主页，或者编写一个新的*index.md*文件，为你的作品集提供一个特定的主页。

让我们来拆解一下下面的块：

```py
- Previous data science projects:
  - "Predict Health Outcomes of Horses": horse_health_prediction_project/readme.md
  …
```

*先前的数据科学项目*：将代表导航栏中的一组页面的名称。*“预测马匹健康结果”*将是所指示文件菜单中显示的名称，在本例中是：*horse_health_prediction_project/readme.md*。Mkdocs会自动找到docs文件夹中需要显示的页面，因此无需在路径中指定。然而，由于马匹健康预测项目位于一个同名文件夹中，你必须指定显示该文件的文件夹位置。

在*docs/*文件夹中，我添加了我之前的项目：

```py
working_folder
|- docs
    |- horse_health_prediction_project
        |- readme.md
        |- notebook.ipynb
        |- notebook.html
    |- US_stocks_prediction_project
        |- reamd.me
        |- notebook.ipynb
        |- notebook.html
```

然后，我用以下语法将每个项目的展示添加到导航栏中：`*“<显示名称>”: <从docs到项目文件的路径>/<项目展示>.md`。

这里的缩进非常重要：它定义了导航栏的文件夹。并非所有docs文件夹中的文件都需要列在导航栏中。然而，如果它们没有列出，访客将无法直接访问。

## **网站配置**

然后我配置了我网站上不可见但非常重要的方面：

+   mkdocs-material要求的基本网站信息：

```py
# Project information
site_name: Pierre-Etienne's data science portfolio
site_url: https://petoulemmonde.github.io/
site_author: Pierre-Etienne Toulemonde
site_description: >-
  I'am Pierre-Etienne Toulemonde, PharmD and Data scientist, 
  and you are here on my data science portfolio
```

*site_name*对应浏览器标签上的名称。

+   一些仓库信息，允许mkdocs-material在托管网站的仓库右上角显示信息：

```py
# Repository: necessary to display the repo on the top right corner
repo_name: petoulemonde/petoulemonde.github.io
repo_url: https://github.com/petoulemonde/petoulemonde.github.io
```

+   我在这里定义了要使用的设计：

```py
# Configuration:
theme:
  name: material
```

这是一个非常重要的步骤，因为这一行告诉mkdocs：“使用mkdocs-material包来构建网站”。如果漏掉这一步，GitHub Pages将不会拥有mkdocs-material的视觉效果和功能！

我添加了一些额外的信息，以追踪我网站上的流量：

```py
# Additional configuration
extra:
  analytics:
    provider: google
    property: <your google analystics code>
```

*property*是来自Google Analytics的代码，用于追踪我作品集上的流量。该代码由Google Analytics生成并与我的Google帐户关联（你可以在[这里](https://www.w3schools.com/howto/howto_google_analytics.asp)找到创建代码的教程）。

当然，我不是一次性写完整个文件的。我先添加一个项目文件和信息到文件架构和导航栏中，然后是配置，再添加另一个项目，然后再配置，……

## **最终的*mkdocs.yml*文件**

我的最终*mkdocs.yml*文件是：

```py
# Project information
site_name: Pierre-Etienne's data science portfolio
site_url: https://petoulemonde.github.io/
site_author: Pierre-Etienne Toulemonde
site_description: >-
  I'am Pierre-Etienne Toulemonde, PharmD and Data scientist, 
  and you are here on my data science portfolio

# Repository
repo_name: petoulemonde/petoulemonde.github.io
repo_url: https://github.com/petoulemonde/petoulemonde.github.io

# Configuration
theme:
  name: material

# Additional configuration
extra:
  analytics:
    provider: google
    property: <google analystics code>

# Page tree
nav:
- Home: index.md
- Current project:
  - "Health open data catalog": catalogue-open-data-sante/index.md
- Previous data science projects:
  - "Predict Health Outcomes of Horses": horse_health_prediction_project/readme.md
  …
- Previous skills based projects:
  - "Predict US stocks closing movements": US_stocks_prediction_project/readme.md
  …
```

## **最终的文件结构**

在此步骤中，我的文件结构是：

```py
petoulemonde.github.io
|- .git
|- readme.md
|- mkdocs.yml
|- requirements.txt
|- environnement.yml
|- docs/
    |- index.md
    |- US_stocks_prediction_project/
        |- README.md
        |- notebook.ipynb
        |- notebook.html
    |- horse_health_prediction_project/
        |- README.md
        |- notebook.ipynb
        |- notebook.html
    |- … others projects …
```

## **部署工作**

Mkdocs允许通过一条命令生成网站的代码：

```py
mkdocs gh-deploy
```

Mkdocs 将所有 mkdocs 文件转换为 HTML 网站，就像魔术一样！Markdown 链接被转换为 HTML 链接，网站的站点地图也会生成。

然后，提交本地仓库中的所有更改，并将其推送到远程仓库。

```py
working_folder> git add .
working_folder> git commit -m "Create website"
working_folder> git push github master
```

# **GitHub Pages 配置**

设置 GitHub Pages 的步骤如下：

+   按照之前的步骤，推送一个最简版的作品集

+   在[GitHub](https://github.com)上，在远程仓库的首页，点击“*Settings*”（顶部菜单）

+   在左侧菜单中，点击“*Pages*”

+   配置页面：

    - *来源*: *从一个分支部署

    - 分支*: *gh-deploy* 和 */root* - 然后保存

在顶部菜单中，点击“*Actions*”。你应该能看到一个“workflow run”。就这样放着，等它变绿，表示完成，网站已经上线！干得好，你成功了！

你可以在 *https://<your GitHub username>.github.io* 上看到你的网站。

# **迭代改进**

我越是查看和展示我的作品集，就越发现其中的错误。要修正这些错误，方法非常简单：

1.  在你的电脑上进行修改（以快速查看修改内容，并在必要时进行其他修改，快速提醒：`*mkdocs serve*`）

1.  使用 `*mkdocs gh-deploy*` 重新生成网站

1.  提交所有更改

1.  推送到远程仓库。

然后，魔法就发生了：GitHub 会自动进行修改（查看“*Actions*”标签，看看 GitHub 进行到哪一步了）。

# **我的最终 2024 版本作品集！**

我的 2024 版本作品集可以在[这里](https://petoulemonde.github.io)找到，GitHub 仓库在[这里](https://github.com/petoulemonde.github.io)。未来，我希望能集成 JavaScript，让作品集更加动态。

为什么我没有为我的作品集购买一个网站？我希望能集中精力创作作品集内容和新项目，把这些任务的管理工作降到最低。而且，无论是购买网站还是使用 GitHub Pages，人们都会通过点击链接访问我的网站，因此购买网站与否，结果是一样的。

非常感谢您的关注，这是我的第一篇 Medium 文章。欢迎在文章下留言，我很想听听你的看法。再见！
