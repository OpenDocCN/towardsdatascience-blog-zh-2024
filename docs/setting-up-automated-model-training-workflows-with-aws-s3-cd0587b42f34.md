# 使用 AWS S3 设置自动化模型训练工作流

> 原文：[https://towardsdatascience.com/setting-up-automated-model-training-workflows-with-aws-s3-cd0587b42f34?source=collection_archive---------6-----------------------#2024-03-18](https://towardsdatascience.com/setting-up-automated-model-training-workflows-with-aws-s3-cd0587b42f34?source=collection_archive---------6-----------------------#2024-03-18)

## 工作流自动化的开源方法

[](https://khuyentran1476.medium.com/?source=post_page---byline--cd0587b42f34--------------------------------)[![Khuyen Tran](../Images/98aa66025ad29b618e875c75f1c400a5.png)](https://khuyentran1476.medium.com/?source=post_page---byline--cd0587b42f34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cd0587b42f34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cd0587b42f34--------------------------------) [Khuyen Tran](https://khuyentran1476.medium.com/?source=post_page---byline--cd0587b42f34--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cd0587b42f34--------------------------------) ·阅读时间 7 分钟 ·2024年3月18日

--

# 动机

假设你是一个电子商务平台，旨在增强推荐个性化。你的数据存储在 S3 中。

为了优化推荐，你计划每当 S3 中添加新文件时，使用最新的客户互动数据重新训练推荐模型。那么，如何具体处理这个任务呢？

![](../Images/19c91eea848de1654a1def0fce56e5a3.png)

除非另有说明，所有图片均由作者提供

# 解决方案

解决这个问题的两种常见方法是：

1.  **AWS Lambda：** AWS 提供的无服务器计算服务，允许在事件触发时执行代码，无需管理服务器。

1.  **开源调度器：** 自动化、调度和监控工作流与任务的工具，通常是自托管的。

使用开源调度器相较于 AWS Lambda 提供了以下优势：

+   **成本效益：** 在 AWS Lambda 上运行长时间任务可能会很昂贵。开源调度器允许你使用自己的基础设施，潜在地节省成本。

+   **更快的迭代：** 在本地开发和测试工作流能加快过程，使调试和优化变得更加容易。

+   **环境控制：** 对执行环境的完全控制使你能够根据自己的喜好自定义开发工具和集成开发环境（IDE）。

![](../Images/aa329b6f70ec6933a5f407cb563a3fd2.png)

虽然你可以在 Apache Airflow 中解决这个问题，但它需要复杂的基础设施和部署设置。因此，我们将使用 [Kestra](https://bit.ly/49OMyH9)，它提供了直观的用户界面，并且可以通过一个 Docker 命令启动。

欢迎在此处播放并分叉本文的源代码：

[](https://github.com/khuyentran1401/mlops-kestra-workflow?source=post_page-----cd0587b42f34--------------------------------) [## GitHub - khuyentran1401/mlops-kestra-workflow

### 通过在 GitHub 上创建账户，参与 khuyentran1401/mlops-kestra-workflow 的开发。

github.com](https://github.com/khuyentran1401/mlops-kestra-workflow?source=post_page-----cd0587b42f34--------------------------------)

# 工作流摘要

该工作流由两个主要组件组成：Python 脚本和编排。

## **编排**

+   Python 脚本和流程存储在 Git 中，并且会按计划同步到 Kestra。

![](../Images/ff05c4168232f7a136a385271928fd4b.png)

+   当 S3 桶的“new”前缀下出现新文件时，Kestra 会触发一系列 Python 脚本的执行。

![](../Images/19c91eea848de1654a1def0fce56e5a3.png)

## Python 脚本

+   [*download_files_from_s3.py*](https://github.com/khuyentran1401/mlops-kestra-workflow/blob/main/src/download_files_from_s3.py): 从桶中的“old”前缀下载所有文件。

+   [*merge_data.py*](https://github.com/khuyentran1401/mlops-kestra-workflow/blob/main/src/merge_data.py): 合并下载的文件。

+   [*process.py*](https://github.com/khuyentran1401/mlops-kestra-workflow/blob/main/src/process.py): 处理合并的数据。

+   [*train.py*](https://github.com/khuyentran1401/mlops-kestra-workflow/blob/main/src/train.py)*:* 使用处理后的数据训练模型。

![](../Images/f230c2f02a707f09663735925e93bbdd.png)

由于我们将在 Kestra 中执行从 Git 下载的代码，请确保将这些 Python 脚本提交到仓库中。

```py
git add .
git commit -m 'add python scripts'
git push origin main
```

# 编排

## 启动 Kestra

执行以下命令下载 Docker Compose 文件：

```py
curl -o docker-compose.yml \
https://raw.githubusercontent.com/kestra-io/kestra/develop/docker-compose.yml
```

确保 Docker 正在运行。然后，使用以下命令启动 Kestra 服务器：

```py
docker-compose up -d
```

通过在浏览器中打开 URL [http://localhost:8080](http://localhost:8080/) 访问 UI。

![](../Images/5b33ef5eb4c0a7f3801fb1ba4f5fa7e8.png)

## 从 Git 同步

由于 Python 脚本托管在 GitHub 上，我们将使用 [Git Sync](https://kestra.io/plugins/plugin-git/tasks/io.kestra.plugin.git.sync) 每分钟将代码从 GitHub 同步到 Kestra。要设置此功能，请在“_flows”目录下创建一个名为“sync_from_git.yml”的文件。

```py
.
├── _flows/
│   └── sync_from_git.yml
└── src/
    ├── download_files_from_s3.py
    ├── helpers.py
    ├── merge_data.py
    ├── process.py
    └── train.py
```

如果您使用的是 VSCode，可以使用 [Kestra 插件](https://marketplace.visualstudio.com/items?itemName=kestra-io.kestra) 来启用 `.yaml` 文件中的流程自动完成和验证功能。

![](../Images/5d963b494550c1f0572c20a04a2f7675.png)![](../Images/0d1942b43ca808d76b4744901d3bb420.png)

以下是从 Git 同步代码的流程实现：

```py
id: sync_from_git
namespace: dev

tasks:
  - id: git
    type: io.kestra.plugin.git.Sync
    url: https://github.com/khuyentran1401/mlops-kestra-workflow
    branch: main
    username: "{{secret('GITHUB_USERNAME')}}"
    password: "{{secret('GITHUB_PASSWORD')}}"
    dryRun: false  # if true, you'll see what files will be added, modified
    # or deleted based on the Git version without overwriting the files yet

triggers:
  - id: schedule
    type: io.kestra.core.models.triggers.types.Schedule
    cron: "*/1 * * * *" # every minute
```

只有在 GitHub 仓库是私有的情况下，才需要提供用户名和密码。要将这些密钥传递给 Kestra，请将它们放入“.env”文件中：

```py
# .env
GITHUB_USERNAME=mygithubusername
GITHUB_PASSWORD=mygithubtoken
AWS_ACCESS_KEY_ID=myawsaccesskey
AWS_SECRET_ACCESS_KEY=myawssecretaccesskey
# ! This line should be empty
```

接下来，使用以下 bash 脚本对这些密钥进行编码：

```py
while IFS='=' read -r key value; do
    echo "SECRET_$key=$(echo -n "$value" | base64)";
done < .env > .env_encoded
```

执行此脚本会生成一个包含编码后的密钥的“.env_encoded”文件：

```py
# .env_encoded
SECRET_GITHUB_USERNAME=bXlnaXRodWJ1c2VybmFtZQ==
SECRET_GITHUB_PASSWORD=bXlnaXRodWJ0b2tlbg==
SECRET_AWS_ACCESS_KEY_ID=bXlhd3NhY2Nlc3NrZXk=
SECRET_AWS_SECRET_ACCESS_KEY=bXlhd3NzZWNyZXRhY2Nlc3NrZXk=
```

在 Docker Compose 文件中包含编码后的环境文件，以便 Kestra 访问环境变量：

```py
# docker-compose.yml
  kestra:
    image: kestra/kestra:latest-full
    env_file:
      - .env_encoded
```

确保在“.gitignore”文件中排除环境文件：

```py
# .gitignore
.env
.env_encoded
```

最后，将新流程和Docker Compose文件都提交到Git：

```py
git add _flows/sync_from_git.yml docker-compose.yml
git commit -m 'add Git Sync'
git push origin main
```

现在，随着`sync_from_git`流程设置为每分钟运行一次，你可以方便地通过Kestra UI访问并触发Python脚本的执行。

![](../Images/720f5af3075edd1455253930ff0f021c.png)![](../Images/84846040a61cc99923ac42d464e5516c.png)

## 编排

我们将创建一个流程，当有新文件添加到“winequality-red”桶中的“new”前缀时触发。

一旦检测到新文件，Kestra会将其下载到内部存储并执行Python文件。最后，它将文件从“new”前缀移动到“old”前缀，以避免在随后的轮询中重复检测。

![](../Images/7108977a1b3c4ca3def8ec2ac3f9c195.png)

```py
id: run_ml_pipeline
namespace: dev
tasks:
  - id: run_python_commands
    type: io.kestra.plugin.scripts.python.Commands
    namespaceFiles:
      enabled: true
    env:
      AWS_ACCESS_KEY_ID: "{{secret('AWS_ACCESS_KEY_ID')}}"
      AWS_SECRET_ACCESS_KEY: "{{secret('AWS_SECRET_ACCESS_KEY')}}"
    docker:
      image: ghcr.io/kestra-io/pydata:latest
    beforeCommands:
      - pip install -r requirements.txt
    commands:
      - python src/download_files_from_s3.py
      - python src/merge_data.py
      - python src/process.py
      - python src/train.py
    outputFiles:
      - "*.pkl"
triggers:
  - id: watch
    type: io.kestra.plugin.aws.s3.Trigger
    interval: PT1S
    accessKeyId: "{{secret('AWS_ACCESS_KEY_ID')}}"
    secretKeyId: "{{secret('AWS_SECRET_ACCESS_KEY')}}"
    region: us-east-2
    bucket: winequality-red
    prefix: new
    action: MOVE
    moveTo:
      bucket: winequality-red
      key: old
```

`run_python_commands`任务使用：

+   使用`namespaceFiles`访问本地项目中的所有文件，并与Git仓库同步。

+   使用`env`来检索环境变量。

+   使用`docker`在docker容器`ghcr.io/kestra-io/pydata:latest`内执行脚本。

+   使用`beforeCommands`在执行命令之前从“requirements.txt”文件安装依赖。

+   使用`commands`按顺序执行命令列表。

+   使用`outputFiles`将所有pickle文件从本地文件系统发送到Kestra的内部存储。

最后，添加`upload`任务，将模型的pickle文件上传到S3。

![](../Images/0bf0fad92d75de999eb59d2fefd93602.png)

```py
id: run_ml_pipeline
namespace: dev
tasks:
  - id: run_python_commands
    type: io.kestra.plugin.scripts.python.Commands
    namespaceFiles:
      enabled: true
    env:
      AWS_ACCESS_KEY_ID: "{{secret('AWS_ACCESS_KEY_ID')}}"
      AWS_SECRET_ACCESS_KEY: "{{secret('AWS_SECRET_ACCESS_KEY')}}"
    docker:
      image: ghcr.io/kestra-io/pydata:latest
    beforeCommands:
      - pip install -r requirements.txt
    commands:
      - python src/download_files_from_s3.py
      - python src/merge_data.py
      - python src/process.py
      - python src/train.py model_path=model/model.pkl
    outputFiles:            
      - "*.pkl"    
  # ------------------------- ADD THIS ------------------------- #
  - id: upload              
    type: io.kestra.plugin.aws.s3.Upload
    accessKeyId: "{{secret('AWS_ACCESS_KEY_ID')}}"
    secretKeyId: "{{secret('AWS_SECRET_ACCESS_KEY')}}"
    region: us-east-2
    from: '{{outputs.run_python_commands.outputFiles["model/model.pkl"]}}'
    bucket: winequality-red
    key: model.pkl
  # ------------------------------------------------------------ #
triggers:
  ...
```

就这样！将此流程命名为“run_ml_pipeline.yml”并提交到 Git。

```py
git add run_ml_pipeline.yml
git commit -m 'add run_ml_pipeline'
git push origin main
```

## 触发流程

要启动流程，只需将新文件添加到S3的“winequality-red”桶中的“new”前缀。

![](../Images/1c3cf593aa7d5ea84478abcf8c5e57a5.png)

该操作将触发`run_ml_pipeline`流程，启动从“旧”前缀下载数据、合并所有文件、处理数据并训练模型。

![](../Images/6e9a0b6ab67bc14bc15e7eca149714ec.png)![](../Images/d3cfe0e9fa37c79f185715567c6f35f9.png)

一旦工作流执行完毕，“model.pkl”文件会被上传到S3。

![](../Images/5c20d3d5356ee53bdf05e9993a5e8938.png)

# 结论

本文展示了如何使用Kestra自动化执行数据科学任务的Python脚本，每当有新文件添加到S3时。如果你在寻找自动化机器学习管道的方式，可以尝试这个解决方案。

![](../Images/374d772107eebbec9b782240512405e3.png)

我喜欢写关于数据科学的概念，并玩弄各种数据科学工具。你可以通过以下方式保持关注我的最新文章：

+   订阅我的[Data Science Simplified](https://mathdatasimplified.com/)新闻通讯。

+   在[LinkedIn](https://www.linkedin.com/in/khuyen-tran-1401/)和[Twitter](https://twitter.com/KhuyenTran16)上与我连接。
