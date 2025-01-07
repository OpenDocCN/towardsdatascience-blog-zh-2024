# 设置一个Docker化的Python环境——硬方法

> 原文：[https://towardsdatascience.com/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0?source=collection_archive---------2-----------------------#2024-02-13](https://towardsdatascience.com/setting-a-dockerized-python-environment-the-hard-way-e62531bca7a0?source=collection_archive---------2-----------------------#2024-02-13)

[](https://medium.com/@rami.krispin?source=post_page---byline--e62531bca7a0--------------------------------)[![Rami Krispin](../Images/8af28c282d42a2a27c28aa8af4c8d482.png)](https://medium.com/@rami.krispin?source=post_page---byline--e62531bca7a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e62531bca7a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e62531bca7a0--------------------------------) [Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--e62531bca7a0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e62531bca7a0--------------------------------) ·阅读时长9分钟·2024年2月13日

--

本文将回顾从命令行（CLI）运行Docker化Python环境的不同方法。我是否推荐你从CLI运行Python环境？**绝对不！**

还有更好的方式来设置Python开发环境，比如使用[VScode和Dev Containers扩展](https://github.com/RamiKrispin/vscode-python)。但为了学习目的，我们将使用“硬方法”通过CLI来设置Python环境。我们将回顾使用run命令启动容器的不同方法，并了解如何通过Dockerfile定制内置镜像。

![](../Images/acbfde2cb642aa103b6f28af32df61d0.png)

一只由作者使用Midjourney创建的Pixal Python Snake

# 前提条件

要跟随本教程进行操作，您需要以下内容：

+   如果您使用的是macOS或Windows操作系统的机器，则需要安装Docker Desktop（或等效工具）；如果您使用的是Linux操作系统，则需要安装Docker。

+   您需要一个Docker Hub账户来拉取镜像。

在整个教程中，我们将使用官方的Python镜像——`python:3.10`。

# 入门

我们首先从Docker Hub拉取官方的Python 3.10镜像。首先，使用`docker login`命令登录到Docker Hub：

```py
docker login docker.io                                                                                                                                                                      ok
Authenticating with existing credentials...
Login Succeeded
```

接下来，我们将使用终端中的`docker pull`命令：

```py
docker pull python:3.10                                                                                                                                                                     ok
```

如果这是您第一次拉取镜像，您应该期待以下输出：

```py
3.10: Pulling from library/python
66932e2b787d: Pull complete
4afa7e263db1: Pull complete
c812910e5e62: Pull complete
f4e4299bb649: Pull complete
5213cc2f9120: Pull complete
4a3b5b2f0e66: Pull complete
c214ceb1cabf: Pull complete
f5336038b15c: Pull complete
Digest: sha256:f94601bb6374b0b63835a70c9e5c3ba1b19bc009133900a9473229a406018e46
Status: Downloaded newer image for python:3.10
docker.io/library/python:3.10
```

您可以使用`docker images`命令查看镜像详细信息：

```py
docker images                                                                                                                                                                          ok  11s
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
python       3.10      f7537c504c9a   7 days ago   1.01GB
```

在运行容器之前，让我们使用`docker inspect`命令来查看镜像元数据：

```py
docker inspect python:3.10
```

这将返回以下**JSON**输出：

```py
[
    {
        "Id": "sha256:f7537c504c9a91a22c9a255ee02048e7079cacdee583290e8238c605d17f9600",
        "RepoTags": [
            "python:3.10"
        ],
        "RepoDigests": [
            "python@sha256:f94601bb6374b0b63835a70c9e5c3ba1b19bc009133900a9473229a406018e46"
        ],
        "Parent": "",
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2024-02-03T10:49:13Z",
        "Container": "",
        "ContainerConfig": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": null,
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "DockerVersion": "",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D",
                "PYTHON_VERSION=3.10.13",
                "PYTHON_PIP_VERSION=23.0.1",
                "PYTHON_SETUPTOOLS_VERSION=65.5.1",
                "PYTHON_GET_PIP_URL=https://github.com/pypa/get-pip/raw/dbf0c85f76fb6e1ab42aa672ffca6f0a675d9ee4/public/get-pip.py",
                "PYTHON_GET_PIP_SHA256=dfe9fd5c28dc98b5ac17979a953ea550cec37ae1b47a5116007395bfacff2ab9"
            ],
            "Cmd": [
                "python3"
            ],
            "ArgsEscaped": true,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "arm64",
        "Variant": "v8",
        "Os": "linux",
        "Size": 1005570383,
        "VirtualSize": 1005570383,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/d2fd76e7396796018a959209b51fe8311a188c8eae8e339e9e556de0889ca0bd/diff:/var/lib/docker/overlay2/bbedb25c5aa6ec3f2fc632e62a53989a329b907143fac165f899209293627a69/diff:/var/lib/docker/overlay2/ed6a4bf49214e6b496b7716443b8de380481cd9416bc4a378f29183c9129786f/diff:/var/lib/docker/overlay2/ac9543f44a835c203fb0b0b28958d94df72d206c9060c9d83307b39f50355102/diff:/var/lib/docker/overlay2/94a9f92c36ea6251feda52be8e76ec4da4a9c66b744a29472e1ccfdf34a6f69d/diff:/var/lib/docker/overlay2/6ee22c274256834a64008022856d365d91373bb490ae9f2f1723cb524b246a29/diff:/var/lib/docker/overlay2/2fa272376e0ce68f4f34f18e5ecb1ddd58a32fb20a82e5a417514047f8e684a3/diff",
                "MergedDir": "/var/lib/docker/overlay2/f2d64d1affbe99afb05251435f7705cb97e2efa4f8febb494b4cbaa21e7f742a/merged",
                "UpperDir": "/var/lib/docker/overlay2/f2d64d1affbe99afb05251435f7705cb97e2efa4f8febb494b4cbaa21e7f742a/diff",
                "WorkDir": "/var/lib/docker/overlay2/f2d64d1affbe99afb05251435f7705cb97e2efa4f8febb494b4cbaa21e7f742a/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:9f8c60461a42fd9f275c56f4ec8fea8a8ea2d938493e316e830994a3814cf0aa",
                "sha256:927a28cdbf6c1705342b2cba0069457313815058dcebe1996e46cade38a09370",
                "sha256:e85139d96aee18e99fc964d02546be48cc6a4d4dfd9f465a91f172b7c535e55f",
                "sha256:f3168ba6a8d2ec30e12002ad5b7b497cf7409f3e19cc8d8f447f6cf4231a2482",
                "sha256:acbc6c8127209b09fa336e354037fdc630d3594e15174f2bc1bdbf31d5591376",
                "sha256:06c4da96c7dd2fbbbb541e000bd0ea4cfbf7c80b24f098a9d67f677ef5e6c63e",
                "sha256:1cdf208dc10679cf5db6d4f0e17ff6d5bfe754b4195ddd3b153b6d1ff51ce909",
                "sha256:5d6f554f67c7da9d34763916bac632a450902c6e6fdbf9244f888f110fd37740"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

`ispect` 命令提供了有关镜像的很多有用信息，例如层信息、镜像大小、硬件架构等。因为我们想运行该镜像，最有趣的细节是 [CMD](https://docs.docker.com/engine/reference/builder/#cmd) 设置。Dockerfile 中的 `CMD` 命令定义了容器启动时要执行的命令。我们可以通过上面的输出解析 `CMD` 信息，使用 [jq](https://jqlang.github.io/jq/) 包：

```py
docker inspect python:3.10 | jq '.[] | .Config | .Cmd'                                                                                                                                      ok
[
  "python3"
]
```

如你所见，镜像被设置为在容器运行时执行 `python3` 命令。现在，让我们使用 `run` 命令启动容器：

```py
docker run python:3.10
```

然后……没有任何反应。简而言之，原因是我们需要给 Docker 终端访问权限。我们将使用交互模式和 TTY 参数来运行镜像：

```py
docker run --interactive --tty python:3.10
```

这将把终端连接到容器并在容器内打开 Python：

![](../Images/b066a01e44128424e719f9428d280fc9.png)

在交互模式下运行 Python 镜像（截图由作者提供）

尽管我们能够在容器内启动 Python，但这样做并不太实用：

+   我们无法在 Python 解释器内创建、编辑和运行脚本

+   默认情况下，Python 镜像仅包含有限的库。在这种模式下，你无法添加额外的库

+   最后但同样重要的是，容器是短暂的。一旦你停止它，所有的工作都会丢失

例如，如果我们尝试加载 pandas，我们将遇到以下错误：

![](../Images/574f93ea2b2baadd9a6e412773b40e20.png)

尝试加载 Pandas 库（截图由作者提供）

在接下来的部分中，我们将通过创建 Dockerfile 和自定义基础镜像功能来解决这些问题。这包括添加以下功能：

+   设置虚拟环境并使用 requirements 文件安装包。为了简化，

+   安装一个 `vim` 编辑器以编辑文件

+   更改 `CMD` 命令以在启动时打开一个 shell 终端（而不是 Python 解释器）。这将使我们能够从终端创建新脚本、编辑并执行它们

# 自定义基础镜像

为了自定义 Python 环境并进行上述更改，我们将创建一个包含以下功能的 Dockerfile：

+   导入 Python 镜像 — `python:3.10`

+   设置虚拟环境

+   安装所需的库

+   安装 vim 编辑器

+   暴露一个 bash 终端

## 设置 Python 虚拟环境

为了设置一个 Python 虚拟环境，我们将使用以下两个辅助文件：

`requirements.txt`

```py
wheel==0.40.0
pandas==2.0.3
```

该文件定义了要在虚拟环境中安装的 Python 库列表。例如，在这种情况下，我们将安装 Pandas 库，版本为 2.0.3。通常，我还会安装 wheels 库，它处理 C 语言依赖。

下一个辅助文件是下面的 bash 脚本，它设置虚拟环境并从 `requirements.txt` 文件中安装所需的库。

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

**注意：** 我们使用一个变量（标记为`$1`）来定义环境名称，该名称将被赋值给`PYTHON_ENV`变量。在构建过程中使用变量是一种良好的做法，因为它使我们能够在不修改代码的情况下修改镜像的某些特性。我们将在Dockerfile中为变量赋值。

让我们解释一下来自上述bash脚本的以下拼接代码，它设置了虚拟环境：

```py
python3 -m venv /opt/$PYTHON_ENV  \
    && export PATH=/opt/$PYTHON_ENV/bin:$PATH \
    && echo "source /opt/$PYTHON_ENV/bin/activate" >> ~/.bashrc
```

上述三行代码包括三个拼接表达式：

+   第一，使用`python3 -m venv /opt/$PYTHON_ENV`命令设置一个虚拟环境。

+   第二，向`PATH`变量中添加虚拟环境路径。

+   第三，向`.bashrc`文件中添加环境激活命令。这将确保每次启动终端时，默认激活这个虚拟环境（否则，你需要在启动环境时手动激活）。

环境设置完成后，我们使用`source`命令激活环境，并使用`pip3`命令在环境中安装库。

## 创建Dockerfile

在审查了辅助文件后，让我们看看它们如何在下面的Dockerfile中被引用。

`Dockerfile`

```py
FROM python:3.10

ARG PYTHON_ENV=my_env
ENV PYTHON_ENV=$PYTHON_ENV

RUN mkdir requirements

COPY requirements.txt set_python_env.sh /requirements/

RUN bash ./requirements/set_python_env.sh $PYTHON_ENV

RUN apt-get update && \
    apt-get install -y \
    vim \
    && apt update

CMD ["/bin/sh", "-c", "bash"]
```

如你所见，我们使用相同的镜像——`python:3.10`作为我们的基础镜像。

接下来，我们使用`ARG`命令设置一个名为`PYTHON_ENV`的参数，用于定义虚拟环境名称。我们将`my_env`设置为默认值，并且可以在构建时使用`arg`参数进行修改。我们还使用`PYTHON_ENV`参数来设置一个环境变量。

在设置虚拟环境之前，我们将在镜像的根目录下创建一个名为`requirements`的新文件夹，并使用`COPY`命令将上述辅助文件——`requirements.txt`和`set_my_python.sh`复制到`requirements`文件夹中。

接下来，我们调用bash脚本`set_my_python.sh`，该脚本设置虚拟环境并安装所需的库。如上所述，我们使用`PYTHON_ENV`变量作为参数，配合`set_my_python.sh`文件动态设置虚拟环境名称。

我们使用`apt`命令安装**vim**——一个CLI编辑器。这样我们就可以通过容器CLI编辑代码了。

最后但同样重要的是，使用`CMD`命令启动一个bash终端：

```py
CMD ["/bin/sh", "-c", "bash"]
```

此时，我们在本地文件夹中有以下文件：

```py
.
├── Dockerfile
├── requirements.txt
└── set_python_env.sh
```

现在让我们继续使用`docker build`命令构建镜像：

```py
docker build .-f Dockerfile -t my_python_env:3.10
[+] Building 47.3s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                 0.2s
 => => transferring dockerfile: 389B                                                                 0.0s
 => [internal] load .dockerignore                                                                    0.2s
 => => transferring context: 2B                                                                      0.0s
 => [internal] load metadata for docker.io/library/python:3.10                                       0.0s
 => [1/5] FROM docker.io/library/python:3.10                                                         1.3s
 => [internal] load build context                                                                    0.4s
 => => transferring context: 460B                                                                    0.0s
 => [2/5] RUN mkdir requirements                                                                     0.5s
 => [3/5] COPY requirements.txt set_python_env.sh /requirements/                                     0.2s
 => [4/5] RUN bash ./requirements/set_python_env.sh my_env                                          26.8s
 => [5/5] RUN apt-get update &&     apt-get install -y     vim     && apt update                    17.4s
 => exporting to image                                                                               0.7s
 => => exporting layers                                                                              0.6s
 => => writing image sha256:391879baceea6154c191692d4bcb9ec9690de6dc4d5edd5b2ed13f6c579dd05c         0.0s
 => => naming to docker.io/library/my_python_env:3.10
```

让我们再次运行docker images命令，查看当前镜像：

```py
docker images                                                  
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
my_python_env   3.10      391879baceea   7 minutes ago   1.23GB
python          3.10      f7537c504c9a   8 days ago      1.01GB
```

正如你所注意到的，添加虚拟环境并安装包后，镜像大小增加了大约250MB。

## 运行Python环境

构建完镜像后，让我们使用`docker run`命令启动镜像，并检查上述属性是否按预期定义：

```py
docker run --interactive --tty my_python_env:3.10
```

这将以交互模式启动镜像，并按预期打开一个bash终端：

![](../Images/6898d42a4462a0bbb0ea5a3826cada26.png)

通过 shell 终端启动容器（截图由作者提供）

正如您在上面的截图中所看到的，它在 bash 终端内启动了容器，并且虚拟环境已按预期设置为`my_env`。`pandas`库已经安装并可以加载，我们现在可以从终端编辑文件。

需要注意的一个问题是容器仍然是临时的。因此，我们在镜像内创建的任何代码都无法导出，并且在停止容器运行后将会丢失。

一个简单的解决方案是使用`volume`参数挂载一个卷。为了简便起见，我们将继续挂载本地文件夹，其中包含 Dockerfile 和辅助文件，挂载到容器内一个名为`my_scripts`的新文件夹中：

```py
docker run -v .:/my_scripts  --interactive --tty my_python_env:3.10
```

这是输出结果：

![](../Images/a8623245254898dcff248d1bac468805.png)

将本地卷挂载到容器中（截图由作者提供）

一旦文件夹被挂载，容器内挂载文件夹中创建、修改或删除的任何文件将会反映到本地文件夹。这使得在停止容器时能够保持代码的更新。

# 总结

在本教程中，我们回顾了如何使用命令行设置 Docker 化的 Python 环境。虽然这既不是开发 Python 的实用方法，也不是推荐的方法，但它是学习 Docker 核心命令和基本功能的一个很好的体验。我们展示了如何轻松地使用内置镜像并根据我们的需求对其进行定制。最后但同样重要的是，我们展示了如何使用卷参数将本地文件夹挂载到容器中，将容器从临时模式转为持久模式。

# 资源

+   使用 VScode 和 Dev Containers 设置 Docker 化 Python 环境的完整教程 — [https://github.com/RamiKrispin/vscode-python](https://github.com/RamiKrispin/vscode-python)

+   使用 GitHub 模板设置 Docker 化 Python 开发环境 — [https://medium.com/@rami.krispin/setting-a-dockerized-python-development-environment-template-de2400c4812b](https://medium.com/@rami.krispin/setting-a-dockerized-python-development-environment-template-de2400c4812b)

+   Docker 与虚拟环境的对比 — [https://medium.com/@rami.krispin/running-python-r-with-docker-vs-virtual-environment-4a62ed36900f](https://medium.com/@rami.krispin/running-python-r-with-docker-vs-virtual-environment-4a62ed36900f)

+   Dockerfile 参考 — [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)
