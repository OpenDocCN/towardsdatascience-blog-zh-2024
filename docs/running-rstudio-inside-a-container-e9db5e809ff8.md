# 在容器内运行 RStudio

> 原文：[`towardsdatascience.com/running-rstudio-inside-a-container-e9db5e809ff8?source=collection_archive---------5-----------------------#2024-03-09`](https://towardsdatascience.com/running-rstudio-inside-a-container-e9db5e809ff8?source=collection_archive---------5-----------------------#2024-03-09)

## 逐步指南：在容器内设置 RStudio 服务器并使用你的本地 RStudio 设置

[](https://medium.com/@rami.krispin?source=post_page---byline--e9db5e809ff8--------------------------------)![Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--e9db5e809ff8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e9db5e809ff8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9db5e809ff8--------------------------------) [Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--e9db5e809ff8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9db5e809ff8--------------------------------) ·6 分钟阅读·2024 年 3 月 9 日

--

这是一个逐步指南，介绍如何在容器内设置 RStudio 服务器并使用本地 RStudio 设置。我们将使用[Rocker RStudio 镜像](https://hub.docker.com/r/rocker/rstudio)，并展示如何通过`docker run`命令及其参数进行自定义。

到本教程结束时，你将能够：

+   在容器内启动 RStudio 服务器

+   挂载本地文件夹

+   克隆本地 RStudio 设置（颜色主题、代码片段等）

+   加载本地 Renviron 设置

![](img/7758b9511dbe9829b8d18880f155bf26.png)

一只鲸鱼携带着一个 R 容器（由作者使用 Midjourney 创建）

# 介绍

RStudio 是 R 编程语言的主要 IDE。与其他通用 IDE（如 VScode）不同，RStudio 专为 R 用户及其需求而设计。正是这一点使得 RStudio 在 R 用户中非常受欢迎。默认情况下，RStudio 不支持 Docker。运行 RStudio 服务器版本是将 RStudio 设置和运行在容器中的主要方法。这需要在容器内安装并设置服务器，这对于某些用户来说可能是一个入门障碍。幸运的是，[Rocker 项目](https://rocker-project.org/)——R 镜像的主要来源，提供了内置且现成可用的带有 RStudio 服务器的镜像。

在本教程中，我们将使用 Rocker RStudio 镜像，该镜像可以在[Docker Hub](https://hub.docker.com/r/rocker/rstudio/tags)找到。

# 前提条件

要跟随本教程并运行以下代码，你需要：

+   [Docker Desktop](https://www.docker.com/products/docker-desktop/)（或任何替代品）

+   [Docker Hub](https://hub.docker.com/) 账户

+   [docker run](https://docs.docker.com/reference/cli/docker/container/run/) 命令的基本理解

# 开始使用 Rocker

[Rocker 项目](https://rocker-project.org/)是用于构建 R 镜像的主要平台。它提供了多种具有不同 R 环境设置的镜像，如 base-r、tidyverse、ML-verse、shiny、geospatial，当然，还有 RStudio 服务器镜像。所有可用 R 镜像的完整列表可以在 Rocker 的 Docker Hub 页面找到。

![](img/fc1b996c4b0016643c33407f2c1880e5.png)

Rocker 按下载量排序的热门镜像（作者从 Docker Hub 截图）

我们将使用 `rocker/rstudio` 镜像，顾名思义，它已经安装并准备好使用 RStudio 服务器。通过 `docker run` 命令，我们可以以交互模式启动该容器，并通过浏览器访问 RStudio 服务器。

让我们通过 `docker pull` 命令拉取镜像，开始使用：

```py
>docker pull rocker/rstudio                                                                                                                                            ok
Using default tag: latest
latest: Pulling from rocker/rstudio
a4a2c7a57ed8: Pull complete
d0f9831967fe: Pull complete
e78811385d51: Pull complete
c61633a20287: Pull complete
832cef14f2fb: Pull complete
8395fbba6231: Pull complete
fb53abdcfb34: Pull complete
c942edef0d7f: Pull complete
Digest: sha256:8e25784e1d29420effefae1f31e543c792d215d89ce717b0cc64fb18a77668f3
Status: Downloaded newer image for rocker/rstudio:latest
docker.io/rocker/rstudio:latest
```

你可以使用 `docker images` 命令来验证镜像是否成功下载：

```py
>docker images                                                                                                                                                    ok  36s
REPOSITORY              TAG          IMAGE ID       CREATED       SIZE
rocker/rstudio          latest       7039fb162243   2 days ago    1.94GB
```

现在，让我们使用 Rocker 项目推荐的命令，通过 `docker run` 命令在容器内启动 RStudio：

```py
>docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 rocker/rstudio
```

在我们打开浏览器上的 RStudio 服务器之前，先回顾一下我们之前使用的`run`参数：

+   `rm` — 容器退出时自动删除容器（从终端按 `control + c`）

+   `ti` — 以交互模式运行容器

+   `e` — 设置环境变量，在这种情况下，定义服务器登录密码为 `yourpassword`

+   `p` — 定义端口映射。在这种情况下，我们将容器的 `8787` 端口与本地机器上的 `8787` 端口映射

执行命令后，你可以通过本地的 8787 端口访问 RStudio 服务器（例如，[`localhost:8787`](http://localhost:8787)）。这将弹出登录页面，你应使用：

+   用户名：`rstudio`

+   密码：`yourpassword`（在 `run` 命令中设置）

你应该看到以下输出：

![](img/e8094acf875b3185fd4cd5beaabec26d.png)

RStudio 服务器的初始视图（作者截屏）

> **注意：** 你可以通过在终端点击 `control+c` 来停止运行的容器。

# 哎呀！它是临时的！

默认情况下，Docker 容器以临时模式运行。任何在容器内创建并保存的代码或输入都将在终止容器运行时丢失。如果你希望使用 Docker 作为开发环境，这样就既不实际也不有用。为了解决这个问题，我们将使用 **volume**（或 `v`）参数，它使得可以将本地文件夹挂载到容器的文件系统中。

以下代码演示了如何使用 volume 参数将我们执行 `run` 命令所在的文件夹（例如，`.`）与 RStudio 服务器的主文件夹挂载：

```py
docker run --rm -ti \
-v .:/home/rstudio \
-e PASSWORD=yourpassword \
-p 8787:8787 rocker/rstudio
```

> volume 参数使用以下格式 `source:target` 将本地文件夹（例如，`source`）映射到容器（例如，`target`）。

现在，让我们回到浏览器并使用本地地址重新打开 RStudio 服务器 —— [`localhost:8787`](http://localhost:8787)。你应该能在 RStudio 文件部分看到挂载到容器的本地文件夹和/或文件。在我的例子中，我将挂载我的教程文件夹，它包含以下文件夹：

```py
.
├── Introduction-to-Docker
├── awesome-ds-setting
├── forecast-poc
├── forecasting-at-scale
├── lang2sql
├── postgres-docker
├── python
├── rstudio-docker
├── sdsu-docker-workshop
├── shinylive-r
├── statistical-rethinking-2024
├── vscode-python
├── vscode-python-template
├── vscode-r
└── vscode-r-template
```

如下图所示，本地文件夹现在可以通过 RStudio 服务器访问（以紫色矩形标出）：

![](img/d2d338f2101155644d12e5b19282414a.png)

带有本地卷的 RStudio 服务器（作者提供的截图）

> 注意：你可以使用卷参数挂载多个卷。例如，一个用于你的项目，另一个用于包含数据文件夹的文件夹。

这使我们能够在容器运行时从本地文件夹中读取和写入文件。

# 克隆你的本地 RStudio 设置

在上一部分中，我们了解了如何使用卷参数将本地文件夹挂载到容器中。这使我们可以在容器内工作时将代码保存在本地。在本节中，我们将学习如何利用卷参数将本地 RStudio 设置与容器中的设置进行挂载。这里的思路是启动容器并使用我们的本地设置运行 RStudio 服务器，而无需在每次重新启动容器时更新设置。这包括加载本地设置，如颜色主题设置、代码片段、环境变量等。

在我们更新 `docker run` 命令时，使用本地的 RStudio 配置文件夹之前，我们需要确定本地和容器中的配置文件夹路径。例如，我机器上的路径是 `~/.config/rstudio`，并且它包含以下文件夹和文件：

```py
.
├── dictionaries
│   └── custom
├── rstudio-prefs.json
└── snippets
    └── r.snippets
```

同样，容器中的 `.config/rstudio` 文件夹位于 `/home/rstudio/` 下。因此，我们将使用以下映射：

```py
$HOME/.config/rstudio:/home/rstudio/.config/rstudio
```

同样地，我们希望将 `.Renviron` 文件与本地环境变量挂载。`.Renviron` 文件位于本地机器的根文件夹下，我们使用相同的方法将本地文件与容器中的文件映射：

```py
$HOME/.Renviron:/home/rstudio/.Renviron
```

现在，让我们将所有设置放在一起并重新启动容器：

```py
docker run --rm -ti \
-v .:/home/rstudio \
-v $HOME/.config/rstudio:/home/rstudio/.config/rstudio \
-v $HOME/.Renviron:/home/rstudio/.Renviron \
-e PASSWORD=yourpassword \
-p 8787:8787 rocker/rstudio
```

在将本地 RStudio 配置文件夹与容器中的文件夹挂载之后，服务器设置现在与我机器上的本地 RStudio 设置映射在一起：

![](img/9681c75361b6e632fd25354a3f02ddf1.png)

带有本地设置的 RStudio 服务器（作者提供的截图）

# 总结

本教程重点介绍如何使用`docker run`命令定制 Rocker 的 RStudio 镜像。我们使用`volume`参数将本地文件夹挂载到容器的工作目录中。这使我们能够在容器化环境中工作，并将工作保存到本地。此外，我们使用`volume`参数将本地的 RStudio 设置克隆到容器中。这使得从本地环境到容器化环境的过渡更加顺畅。随着我们添加和使用更多参数，命令可能会变得长且复杂。一旦确定了`run`设置，下一步是通过[Docker Compose](https://docs.docker.com/compose/)将其转换为 YAML 文件。除了简化容器启动过程，Docker Compose 还使您能够管理更复杂的场景，例如启动多个容器。

# 资源

+   RStudio — [`posit.co/products/open-source/rstudio/`](https://posit.co/products/open-source/rstudio/)

+   Rocker 项目 — [`rocker-project.org/`](https://rocker-project.org/)

+   Docker Hub — [`hub.docker.com/`](https://hub.docker.com/)
