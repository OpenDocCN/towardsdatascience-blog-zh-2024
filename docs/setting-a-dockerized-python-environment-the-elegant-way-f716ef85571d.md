# 设置 Docker 化的 Python 环境 — 优雅的方式

> 原文：[https://towardsdatascience.com/setting-a-dockerized-python-environment-the-elegant-way-f716ef85571d?source=collection_archive---------1-----------------------#2024-04-02](https://towardsdatascience.com/setting-a-dockerized-python-environment-the-elegant-way-f716ef85571d?source=collection_archive---------1-----------------------#2024-04-02)

## 本文提供了一个逐步指南，介绍如何使用 VScode 和 Dev Containers 扩展设置一个 Python Docker 化开发环境。

[](https://medium.com/@rami.krispin?source=post_page---byline--f716ef85571d--------------------------------)[![Rami Krispin](../Images/8af28c282d42a2a27c28aa8af4c8d482.png)](https://medium.com/@rami.krispin?source=post_page---byline--f716ef85571d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f716ef85571d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f716ef85571d--------------------------------) [Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--f716ef85571d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f716ef85571d--------------------------------) ·阅读时间 9 分钟·2024 年 4 月 2 日

--

在上一篇关于此主题的文章中，[设置 Docker 化的 Python 环境 — 较为困难的方式](https://medium.com/towards-data-science/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0)，我们讲解了如何通过命令行界面（CLI）设置 Docker 化的 Python 开发环境。在这篇文章中，我们将回顾一种更优雅且更稳健的方法，通过使用 VScode 和 [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 扩展来设置 Docker 化的 Python 开发环境。

相关文章：

[](/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0?source=post_page-----f716ef85571d--------------------------------) [## 设置 Docker 化的 Python 环境 — 较为困难的方式

### 本文将回顾通过命令行（CLI）运行 Docker 化的 Python 环境的不同方法。我是否……

towardsdatascience.com](/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0?source=post_page-----f716ef85571d--------------------------------)

到本教程结束时，你将能够使用 VScode 和 Dev Containers 扩展设置一个简单的 Python 开发环境。

![](../Images/8cbb25bfdc0e8741de15e6c984e79491.png)

VScode 插图（由作者使用 Midjourney 创建）

# 前提条件

要跟随本教程，你需要准备以下内容：

+   如果你使用的是 macOS 或 Windows 操作系统，请安装 Docker Desktop（或等效工具）；如果你使用的是 Linux 操作系统，请安装 Docker。

+   Docker Hub 账户，用于拉取镜像

+   安装了 [VScode IDE](https://code.visualstudio.com/) 和 [Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers) 扩展

在本教程中，我们将使用官方 Python 镜像 — `python:3.10`。

本文中的所有代码示例可以在这里找到：

[](https://github.com/RamiKrispin/vscode-python-medium?source=post_page-----f716ef85571d--------------------------------) [## GitHub - RamiKrispin/vscode-python-medium: 支持 Medium 文章的代码

### 支持 Medium 文章的代码。通过创建账户来贡献 RamiKrispin/vscode-python-medium 的开发…

github.com](https://github.com/RamiKrispin/vscode-python-medium?source=post_page-----f716ef85571d--------------------------------)

# Dev Containers 扩展

在开始之前，让我们解释一下什么是 Dev Containers 扩展，以及何时应该考虑使用它。

简而言之，VScode Dev Containers 扩展使您能够在 Docker 容器内无缝地打开一个隔离的 VScode 会话。隔离的层级包括以下三层：

+   环境

+   VScode 设置

+   VScode 扩展

`devcontainer.json` 文件定义了会话设置，使我们能够设置和定义上述三层。

要在容器内设置并启动项目文件夹，使用 Dev Containers 扩展，您需要以下两个组件：

+   安装 [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 扩展

+   在您的项目文件夹中创建一个名为 `.devcontainer` 的文件夹，并设置一个 `devcontainer.json` 文件

以下图示描述了 Dev Containers 的总体架构：

![](../Images/fd24fddd18b426e7269fa83217b55ce0.png)

Dev Containers 扩展架构（致谢 Rami Krispin）

启动时，Dev Containers 扩展会在容器内启动一个新的 VScode 会话。默认情况下，它会将本地文件夹挂载到容器中，这样可以保持代码的持久性，并与本地文件夹同步。您可以挂载其他文件夹，但这超出了本教程的范围。

在下一部分，我们将看到如何使用 `devcontainer.json` 文件设置 Python 环境。

# 设置一个 Docker 化的 Python 环境

在开始使用 `devcontainer.json` 设置之前，让我们首先定义开发环境的范围。它应包括以下功能：

+   Python 3.10

+   支持 Jupyter 笔记本

+   安装所需的库 — Pandas 和 VScode Jupyter 支持库

+   安装支持的扩展 — Python 和 Jupyter

在接下来的部分，我们将深入探讨 `devcontainer.json` 文件的核心功能。我们将从一个最简化的 Python 环境开始，展示如何通过添加不同的自定义层来进行定制。

## 构建与镜像

启动一个容器化会话的主要要求是定义镜像设置。设置镜像有两种方法：

+   在容器启动时使用 `build` 参数构建镜像并运行。此参数使你能够为构建定义一个 Dockerfile，并将参数传递给 `docker build` 函数。构建过程完成后，它将启动容器内部的会话

+   使用 `image` 参数启动现有镜像的会话

根据不同的使用场景，每种方法都有其优缺点。你应该在镜像完全满足环境需求时考虑使用 `image` 参数。同样，当你有一个基础镜像，但需要添加一些小的自定义设置时，`build` 参数是一个很好的选择。

在接下来的部分，我们将通过一个简单的例子开始，使用 `image` 参数导入官方 Python 镜像（`python:3.10`）来启动 Python 环境。

## 基本的 Docker 化 Python 环境

以下 `devcontainer.json` 文件提供了一个简单的示例，用于设置 Python 环境。它使用 `image` 参数将 `python:3.10` 镜像定义为会话环境：

`devcontainer.json`

```py
{
    "name": "Python Development Environment",
    "image": "python:3.10"
}
```

`name` 参数定义了环境名称。在此示例中，我们将其设置为 **Python 开发环境**。

在启动环境之前，请确保：

+   你的 Docker Desktop（或同等软件）已打开

+   你已登录到 Docker Hub（或提前拉取 Python 镜像）

+   `devcontainer.json` 文件位于项目文件夹中的 `.devcontainer` 文件夹下：

```py
.
└── .devcontainer
    └── devcontainer.json
```

此示例的代码可在 [此处](https://github.com/RamiKrispin/vscode-python-medium/tree/main/01_basic_example) 获取。

要启动会话，请点击左下角的 Dev Container `><` 图标，并选择下方截图所示的 `Reopen in Container` 选项：

![](../Images/9f199cefb98a0b705fb23d9eadfcc058.png)

使用 Dev Containers 扩展启动容器内部的会话（作者截图）

请注意，在首次启动会话时，Dev Containers 扩展将查找由 `image` 参数定义的镜像（在此例中是 `python:3.10`）。如果镜像在本地不可用，它将从 Docker Hub 拉取，可能需要几分钟时间。之后，启动会话通常只需几秒钟。

![](../Images/b4479a50c69ef05d66d4b3fb41943214.png)

容器内的 VScode 会话（作者截图）

在上面的截图中，你可以看到 `devcontainer.json` 参数与会话设置之间的映射。会话名称现在显示在右下角（紫色标记），并与 `name` 参数的值对齐。同样，会话现在运行在 `python:3.10` 容器内，你可以从终端启动 Python。

Python 容器带有默认的 Python 库。在接下来的部分，我们将展示如何通过 `build` 参数在 Python 基础镜像上添加更多层。

## 使用 Dockerfile 自定义 Python 环境

现在，让我们通过修改 `devcontainer.json` 来定制上述环境。我们将把 `image` 参数替换为 `build` 参数。`build` 参数使我们能够在会话启动时通过 Dockerfile 构建镜像，并向 `docker build` 函数传递参数。我们将遵循这篇[文章](https://medium.com/towards-data-science/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0)中展示的方法来设置 Python 环境：

+   导入 `python:3.10` 作为基础镜像

+   设置虚拟环境

+   安装所需的库

我们将使用以下 Dockerfile 来设置 Python 环境：

`Dockerfile`

```py
FROM python:3.10

ARG PYTHON_ENV=my_env

ENV PYTHON_ENV=$PYTHON_ENV

RUN mkdir requirements

COPY requirements.txt set_python_env.sh /requirements/

RUN bash ./requirements/set_python_env.sh $PYTHON_ENV
```

我们使用 `FROM` 参数来导入 Python 镜像，并使用 `ARG` 和 `ENV` 参数来设置虚拟环境作为参数和环境变量。此外，我们还使用以下两个辅助文件来设置虚拟环境并安装所需的库：

+   `requirements.txt` — 一个设置文件，其中列出了所需的库。在本演示中，我们将安装 Pandas 库（版本 2.0.3）和 Jupyter 支持库（ipykernel、ipywidgets、jupyter）。wheels 库是一个支持库，用于处理 C 依赖关系。

+   `set_python_env.sh` — 一个辅助的 Bash 脚本，用于设置虚拟环境并通过 `requirements.txt` 文件安装所需的库

`requirements.txt`

```py
wheel==0.40.0
pandas==2.0.3
ipykernel
ipywidgets
jupyter 
```

`set_python_env.sh`

```py
#!/usr/bin/env bash

PYTHON_ENV=$1

python3 -m venv /opt/$PYTHON_ENV  \
        && export PATH=/opt/$PYTHON_ENV/bin:$PATH \
        && echo "source /opt/$PYTHON_ENV/bin/activate" >> ~/.bashrc

source /opt/$PYTHON_ENV/bin/activate

pip3 install -r ./requirements/requirements.txt
```

最后，我们将使用以下测试文件来评估 Pandas 库是否正确安装并打印 **Hello World!** 消息：

`test1.py`

```py
import pandas as pd

print("Hello World!")
```

让我们在 `devcontainer.json` 文件中进行修改，将 `image` 参数替换为 `build` 参数：

`devcontainer.json`

```py
{
    "name": "Python Development Environment",
    "build": {
        "dockerfile": "Dockerfile",
        "context": ".",
        "args": {
            "PYTHON_ENV": "my_python_dev"
        }
    }
}
```

本示例的文件可以在[这里](https://github.com/RamiKrispin/vscode-python-medium/tree/main/02_build_example)找到。

`build` 子参数使我们能够通过向 `docker build` 函数传递参数来自定义镜像构建。我们使用以下参数来构建镜像：

+   `dockerfile` — Dockerfile 的路径和名称

+   `context` — 设置本地文件系统的路径，以便在构建时通过 `COPY` 参数访问文件。在这种情况下，我们使用 `devcontainer.json` 文件的当前文件夹（例如 `.devcontainer` 文件夹）。

+   `args` — 在构建过程中设置并传递参数给容器。我们使用 `PYTHON_ENV` 参数来设置虚拟环境并命名为 `my_python_dev`

你应该将这三个文件 — `Dockerfile`、`requirements.txt` 和 `set_python_env.sh` 存储在 `.devcontainer` 文件夹下，并与 `devcontainer.json` 文件一起使用：

```py
.
├── .devcontainer
│   ├── Dockerfile
│   ├── devcontainer.json
│   ├── requirements.txt
│   └── set_python_env.sh
└── test2.py
```

现在，让我们使用新设置启动会话，并使用 `test1.py` 文件进行测试：

![](../Images/839afb5683f5adfe2c5732bd9a28f5bd.png)

运行一个 Python 脚本以测试环境（截图来自作者）

如上图所示，我们成功地从终端（标记为紫色）运行了测试脚本，并且它按预期打印出了**Hello World!**消息（标记为绿色）。此外，我们在镜像中设置的虚拟环境（`my_python_dev`）默认已加载（标记为黄色）。

在下一部分中，我们将看到如何自定义Dev Containers会话的VScode设置。

## 自定义VScode设置

Dev Containers扩展的一个重要特点是，它将会话设置与主要的VScode设置隔离开来。这意味着你可以在项目级别完全自定义VScode设置。它扩展了开发环境的可复现性，不仅限于Python或操作系统设置。最后但同样重要的是，它使得与他人协作或在多台机器上工作变得无缝且高效。

我们将在接下来的示例中结束本教程，看看如何使用`customizations`参数自定义VScode设置。我们将向前面的示例添加该参数，并使用`vscode`子参数设置环境默认的Python解释器和所需的扩展：

`devcontainer.json`

```py
{
    "name": "Python Development Environment",
    "build": {
        "dockerfile": "Dockerfile",
        "context": ".",
        "args": {
            "PYTHON_ENV": "my_python_dev"
        }
    },
    "customizations": {
        "vscode": {
            "settings": {
                "python.defaultInterpreterPath": "/opt/my_python_dev/bin/python3",
                "python.selectInterpreter": "/opt/my_python_dev/bin/python3"
            },
            "extensions": [
                "ms-python.python",
                "ms-toolsai.jupyter"
            ]
        }
    }
}
```

该示例的文件可以在[这里](https://github.com/RamiKrispin/vscode-python-medium/tree/main/03_final_example)找到。

我们使用`settings`参数来定义镜像中定义的Python虚拟环境。此外，我们使用`extensions`参数安装[Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)和[Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)支持扩展。

> **注意：** 虚拟环境的路径由用于设置环境的应用程序类型定义。当我们使用`venv`并将其命名为`my_python_dev`时，路径为`opt/my_python_dev/bin/python3`。

在添加Python扩展后，我们可以使用扩展插件启动Python脚本，如下图所示。此外，我们还可以利用Jupyter扩展以交互模式执行Python代码：

![](../Images/4c8bda14019b0f7f505bb1532d14321c.png)

# 总结

在本教程中，我们回顾了如何使用VScode和Dev Containers扩展设置一个Docker化的Python环境。Dev Containers扩展使得容器与开发工作流的集成变得无缝且高效。我们了解了如何通过几个简单的步骤，使用`devcontainer.json`文件来设置和自定义Docker化的Python环境。我们回顾了两种设置会话镜像的方法：通过`image`和`build`参数，以及通过`customizations`参数来设置扩展。还有其他自定义选项未在本教程中涉及，我建议查看这些选项：

+   定义环境变量

+   挂载额外的卷

+   设置`docker run`命令的参数

+   启动后运行命令

如果你有兴趣深入了解更多细节，我推荐查看这个教程：

[](https://github.com/RamiKrispin/vscode-python?source=post_page-----f716ef85571d--------------------------------) [## GitHub - RamiKrispin/vscode-python: 使用 VScode 和 Docker 设置 Python 开发环境的教程…

### 使用 VScode 和 Docker 设置 Python 开发环境的教程 - RamiKrispin/vscode-python

github.com](https://github.com/RamiKrispin/vscode-python?source=post_page-----f716ef85571d--------------------------------)

# 资源

+   代码示例 — [https://github.com/RamiKrispin/vscode-python-medium](https://github.com/RamiKrispin/vscode-python-medium)

+   VScode — [https://code.visualstudio.com/](https://code.visualstudio.com/)

+   开发容器 — [https://code.visualstudio.com/docs/devcontainers/containers](https://code.visualstudio.com/docs/devcontainers/containers)

+   设置一个 Docker 化的 Python 开发环境，使用 GitHub 模板 — [https://medium.com/@rami.krispin/setting-a-dockerized-python-development-environment-template-de2400c4812b](https://medium.com/@rami.krispin/setting-a-dockerized-python-development-environment-template-de2400c4812b)
