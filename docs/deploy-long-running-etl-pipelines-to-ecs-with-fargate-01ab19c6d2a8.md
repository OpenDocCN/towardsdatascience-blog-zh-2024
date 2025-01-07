# 将长时间运行的 ETL 流水线部署到 ECS 与 Fargate

> 原文：[https://towardsdatascience.com/deploy-long-running-etl-pipelines-to-ecs-with-fargate-01ab19c6d2a8?source=collection_archive---------4-----------------------#2024-03-03](https://towardsdatascience.com/deploy-long-running-etl-pipelines-to-ecs-with-fargate-01ab19c6d2a8?source=collection_archive---------4-----------------------#2024-03-03)

## 构建后端应用程序

## 为了保持简单并将成本降到最低

[](https://medium.com/@ilsilfverskiold?source=post_page---byline--01ab19c6d2a8--------------------------------)[![Ida Silfverskiöld](../Images/a2c0850bc0198688f70a5eca858cf8b5.png)](https://medium.com/@ilsilfverskiold?source=post_page---byline--01ab19c6d2a8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--01ab19c6d2a8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--01ab19c6d2a8--------------------------------) [Ida Silfverskiöld](https://medium.com/@ilsilfverskiold?source=post_page---byline--01ab19c6d2a8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--01ab19c6d2a8--------------------------------) ·阅读时间 17 分钟·2024 年 3 月 3 日

--

![](../Images/52f9ed67c372c5d6c4c962a6d7fc66e3.png)

ETL 流水线 | 作者提供的图像

ETL 代表 **提取**、**转换** 和 **加载**。ETL 流水线本质上就是一个数据转换过程——从一个地方提取数据，对其进行处理，然后将其加载回同一位置或不同位置。

如果你通过 API 从事自然语言处理工作，我猜大多数人都会开始这样做，那么在处理数据时，你很容易遇到 AWS Lambda 的超时限制，尤其是当至少一个函数执行超过 15 分钟时。所以，虽然 Lambda 很好，因为它快速且非常便宜，但超时问题可能会带来困扰。

**这里的选择是将代码部署为容器**，该容器可以根据需要运行并按计划执行。因此，与使用 Lambda 启动一个函数不同，我们可以启动一个容器，在 ECS 集群中使用 Fargate 运行。

![](../Images/0d9dbc9be8b617e2240b774166b931f7.png)

我们可以将 EventBridge 用于 Lambda 和 ECS | 作者提供的图像

*为了澄清，Lambda、ECS 和 EventBridge 都是 AWS 服务。*

**与 Lambda 一样，运行 **容器** 一两个小时的成本是 **最小的**。然而，它比运行无服务器函数要 **复杂** 一些。但如果你正在阅读这篇文章，那么你可能已经遇到了相同的问题，并且在思考过渡的最简单方法是什么。

我创建了一个非常简单的 ETL 模板，使用 *Google BigQuery* 来提取和加载数据。这个 [模板](https://github.com/ilsilfverskiold/etl-pipeline-fargate) 如果你跟着做，几分钟内就能运行起来。

*使用 BigQuery 完全是可选的，但我通常会将长期数据存储在那里。*

# 介绍

与其在这里构建复杂的东西，不如向你展示如何构建最简化的方案，并保持其精简。

如果你不需要并行处理数据，就不需要包含类似 Airflow 的工具。我看到有些文章不必要地设置了复杂的工作流，而这些对于简单的数据转换并不是严格必要的。

此外，如果你以后想对这个过程进行扩展，随时可以这样做。

## 工作流

我们将使用 Python 编写脚本，因为我们正在进行数据转换，然后将其与 Docker 打包并推送到 ECR 仓库。

从这里开始，我们可以使用 AWS Fargate 创建一个任务定义，并在 ECS 集群中按计划运行它。

![](../Images/7af5c5507689fd2d1264de7a77474a3d.png)

我们用来将代码部署到 AWS 的所有工具 | 图片来自作者

如果这感觉有些陌生，不用担心；随着我们继续学习，你会理解这些服务以及它们的作用。

## 技术

如果你是第一次使用容器，那么可以将 ECS（弹性容器服务）看作一个帮助我们设置环境的平台，在这个环境中我们可以同时运行一个或多个容器。

另一方面，Fargate 帮助我们简化了容器的管理和设置，使用 Docker 镜像 —— 在 AWS 中这些被称为任务。

![](../Images/6dccf5457f14e0ef24279dabbb9b75e8.png)

一个非常简化的图示 —— 任务运行的基础设施由 Fargate 管理 | 图片来自作者

有一种选择是使用 EC2 来设置你的容器，但那样你需要做更多的手动工作。Fargate 为我们管理底层实例，而在使用 EC2 时，你需要管理和部署自己的计算实例。因此，Fargate 常被称为“无服务器”选项。

我在 [Reddit](https://www.reddit.com/r/aws/comments/165wkns/ecs_fargate_vs_ec2/) 上找到了一篇讨论这个话题的 [帖子](https://www.reddit.com/r/aws/comments/165wkns/ecs_fargate_vs_ec2/)，如果你有兴趣了解用户如何看待 EC2 和 Fargate 的对比，可以阅读一下。这能让你了解人们如何比较 EC2 和 Fargate。

我并不是说 Reddit 是唯一可信的来源，但它对于了解用户观点非常有用。

## 成本

我通常关注的主要问题是如何让代码高效运行，同时管理总成本。

由于我们仅在需要时运行容器，因此只需为我们使用的资源付费。我们支付的价格由多个因素决定，例如运行的任务数量、每个任务的执行时长、任务所使用的虚拟 CPU (vCPUs) 数量以及内存使用量。

大致来说，运行一个任务的总 [成本](https://aws.amazon.com/fargate/pricing/) 大约为每小时 $0.01384，具体费用取决于你配置的资源（适用于 EU 区域）。

![](../Images/d258be4174b91c3966914d2a34913aa1.png)

Fargate 和 ECS 的小时定价（EU 区域）| 图片来自作者

如果我们将这个价格与 **AWS Glue** 进行比较，就能从中得到一些视角，判断它是否合适。

如果一个 ETL 作业需要 4 个 DPU（AWS Glue 作业的默认数量）并运行一个小时，它的费用将是 4 DPU * $0.44 = $1.76。这个费用仅针对一个小时，比运行一个简单的容器要高得多。

这当然是一个简化的计算，实际的 DPU 数量可能会根据任务有所不同。你可以在他们的定价 [页面](https://aws.amazon.com/glue/pricing/) 上查看 AWS Glue 定价的更多细节。

# 开始使用

为了跟随这篇文章，我已经创建了一个简单的 [ETL 模板](https://github.com/ilsilfverskiold/etl-pipeline-fargate)，帮助你快速入门。

这个模板使用 **BigQuery** 来提取和加载数据。它将提取几行数据，做一些简单的操作，然后将数据加载回 BigQuery。

当我运行管道时，我还会有其他的东西来转换数据——我使用自然语言处理的 API，它会在早上运行几个小时——但这些可以留给你稍后添加。**这只是为了给你一个模板，便于操作。**

按照这个教程，主要步骤如下：

+   设置你的本地代码。

+   设置 **IAM 用户** 和 **AWS CLI**。

+   **构建并推送 Docker 镜像** 到 AWS。

+   创建一个 **ECS 任务定义**。

+   创建一个 **ECS 集群**。

+   **安排**你的任务。

总体来说，使用我提供的代码，完成这一切不应该超过 20 分钟。如果你还没有 AWS 账户，可能需要再加 5 到 10 分钟。

## 代码

首先在本地创建一个新文件夹并进入该文件夹。

```py
mkdir etl-pipelines
cd etl-pipelines
```

确保你已经安装了 Python。

```py
python --version
```

如果没有，[本地安装它](https://www.python.org/downloads/)。

一旦准备好，你可以继续克隆我已经设置好的模板。

```py
git clone https://github.com/ilsilfverskiold/etl-pipeline-fargate.git
```

当它完成获取代码时，打开你的代码编辑器查看。

首先查看 ***main.py*** 文件，了解我如何构建代码，以便理解它的功能。

![](../Images/63327e5f4b603b37a1228e14e85fe8c1.png)

你的根文件夹中的 main.py 代码 | 图片来自作者

本质上，它将从 BigQuery 中你指定的表格中提取所有包含 **“Doe”** 的名称，转换这些名称后再将它们作为新行插入回同一个数据表。

你可以进入每个辅助函数，查看我们是如何设置 SQL 查询作业、转换数据然后再将其插入回 BigQuery 表的。

当然，目的是让你设置一个更复杂的东西，但这是一次简单的测试运行，目的是让你能够轻松调整代码。

## 设置 BigQuery

如果你想继续使用我准备好的代码，你需要在 BigQuery 中设置一些内容。否则，你可以跳过这部分。

你将需要以下几样东西：

+   一个包含 **‘name’ 字段为字符串类型的** **BigQuery 表**。

+   数据表中 **包含名称为“Doe”** 的 **几行数据**。

+   一个具有访问此数据集权限的 **服务账户**。

要获取服务账户，您需要导航到 Google Cloud 控制台中的 IAM，然后进入“服务账户”。

到了那里，创建一个新的服务账户。

创建后，您需要通过 IAM 为您的服务账户提供 **BigQuery 用户** 的全局访问权限。

![](../Images/8fb3f1783c18352ce18d73aea0b28f4e.png)

在 IAM 中授予对服务账户的访问权限 | 图片来源：作者

您还需要为此服务账户提供对数据集本身的访问权限，您可以直接在 BigQuery 中通过数据集的 **共享** 按钮进行操作，然后按 **添加主体**。

![](../Images/b9cb1463a9b5ae89f4777af1c796efc2.png)

为 BigQuery 中的数据集添加权限给服务账户 | 图片来源：作者

在您给用户适当权限后，确保返回到服务账户并下载一个密钥。这将为您提供一个 json 文件，您需要将其放在根文件夹中。

![](../Images/e6f873e95ca6e39f87bbd00517f0ddd4.png)

获取服务账户密钥以进行身份验证 | 图片来源：作者

现在，最重要的部分是确保代码能够访问 Google 凭证，并使用正确的数据表。

您需要将您下载的包含 Google 凭证的 json 文件放在根文件夹中，命名为 *google_credentials.json*，然后指定正确的表 ID。

![](../Images/2cb5c008b9902b48e0d803ec65159ddd.png)

更改代码中的表 ID 和服务账户密钥 json 文件 | 图片来源：作者

现在您可能会争辩说不想将凭证存储在本地，这是完全正确的。

您以后可以选择将您的 json 文件存储在 AWS Secrets Manager 中。然而，首先这样做会更简单。

## 本地运行 ETL 流水线

我们将首先在本地运行这段代码，只是为了验证它是否有效。

所以，设置一个 Python 虚拟环境并激活它。

```py
python -m venv etl-env
source etl-env/bin/activate  # On Windows use `venv\Scripts\activate`
```

然后安装依赖项。我们这里只有 *google-cloud-bigquery*，但理想情况下，您将有更多依赖项。

```py
pip install -r requirements.txt
```

运行主脚本。

```py
python main.py
```

这应该会在您的终端中记录 *‘新行已添加’*。这将确认代码按我们预期的方式工作。

## Docker 镜像

现在，为了将此代码推送到 ECS，我们需要将其打包成一个 Docker 镜像，这意味着您需要在本地安装 Docker。

如果您没有安装 Docker，可以在[这里](https://www.docker.com/get-started/)下载。

Docker 帮助我们将应用程序及其依赖项打包成一个镜像，能够在任何系统上轻松识别并运行。使用 ECS 时，我们需要将代码打包成 Docker 镜像，然后通过任务定义引用这些镜像作为容器运行。

我已经在您的文件夹中设置了一个 **Dockerfile**。您应该能在那里查看它。

```py
FROM --platform=linux/amd64 python:3.11-slim

WORKDIR /app

COPY . /app

RUN pip install --no-cache-dir -r requirements.txt

CMD ["python", "main.py"]
```

正如您所看到的，我已经将其保持得非常精简，因为我们这里并没有将 Web 流量连接到任何端口。

我们指定了 AMD64 架构，如果你使用的是非 M1 芯片的 Mac，你可能不需要指定，但指定也不会造成问题。这将向 AWS 指明 Docker 镜像的架构，以避免出现兼容性问题。

## 创建 IAM 用户

在使用 AWS 时，需要指定访问权限。你遇到的大多数问题都是权限问题。我们将使用本地的 CLI 工具，且为使其正常工作，我们必须创建一个 IAM 用户，并赋予它广泛的权限。

访问 [AWS 控制台](https://aws.amazon.com/)，然后导航到 IAM。创建一个新用户，添加权限，并创建一个新策略并附加到该用户。

我在 ***aws_iam_user.json*** 文件中指定了代码所需的权限。你可以在下面看到这个 JSON 文件的一个简短片段。

```py
{
 "Version": "2012-10-17",
 "Statement": [
  {
   "Sid": "VisualEditor0",
   "Effect": "Allow",
   "Action": [
    "logs:CreateLogGroup",
    "iam:CreateRole",
    "iam:AttachRolePolicy",
    "iam:PutRolePolicy",
    "ecs:DescribeTaskDefinition",
    ...more
   ],
   "Resource": "*"
  }
 ]
}
```

你需要进入这个文件以获取 **所有** 你需要设置的权限，这里只是一个简短的片段。我已经设置了很多权限，你可能想根据自己的需要稍后进行调整。

一旦你创建了 IAM 用户并为其添加了正确的权限，你将需要生成一个访问密钥。当询问你的使用案例时，选择 *“命令行接口（CLI）”*。

下载凭证。我们稍后将使用这些凭证进行认证。

## 设置 AWS CLI

接下来，我们将把终端连接到我们的 AWS 账户。

如果你还没有设置 CLI，可以按照[这里](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions)的说明进行操作。设置过程非常简单。

安装 AWS CLI 后，你需要使用我们刚刚创建的 IAM 用户进行认证。

```py
aws configure
```

使用我们在上一步中从 IAM 用户下载的凭证。

## 创建 ECR 仓库

现在，我们可以开始进行 DevOps 操作了。

我们首先需要在 Elastic Container Registry 中创建一个仓库。ECR 是我们存储和管理 Docker 镜像的地方。在设置任务定义时，我们可以从 ECR 引用这些镜像。

要创建一个新的 ECR 仓库，请在终端中运行此命令。这将创建一个名为 *bigquery-etl-pipeline* 的仓库。

```py
aws ecr create-repository --repository-name bigquery-etl-pipeline
```

记下你获得的仓库 URI。

从这里我们需要构建 Docker 镜像，然后将其推送到这个仓库。

为此，你可以进入 AWS 控制台，找到我们刚刚创建的 ECR 仓库。在这里，AWS 会让我们看到我们需要运行的所有推送命令，用以认证、构建并将 Docker 镜像推送到这个 ECR 仓库。

![](../Images/638fe2916b5fd5a8136c36a78884b8b7.png)

在 ECR 中直接找到推送命令 | 图片来源：作者

然而，如果你使用的是 Mac，我建议你在构建 Docker 镜像时指定架构，否则可能会遇到问题。

如果你在跟着我一起操作，那么请先按照如下方式认证你的 Docker 客户端。

```py
aws ecr get-login-password --region YOUR_REGION | docker login --username AWS --password-stdin YOUR_ACCOUNT_ID.dkr.ecr.YOUR_REGION.amazonaws.com
```

确保根据需要更改区域和账户 ID。

构建 Docker 镜像。

```py
docker buildx build --platform=linux/amd64 -t bigquery-etl-pipeline .
```

*这是我调整了命令以指定 linux/amd64 架构的地方。*

为 Docker 镜像打标签。

```py
docker tag bigquery-etl-pipeline:latest YOUR_ACCOUNT_ID.dkr.ecr.YOUR_REGION.amazonaws.com/bigquery-etl-pipeline:latest
```

推送 Docker 镜像。

```py
docker push YOUR_ACCOUNT_ID.dkr.ecr.YOUR_REGION.amazonaws.com/bigquery-etl-pipeline:latest
```

如果一切按计划顺利进行，你将在终端中看到类似这样的内容。

```py
9f691c4f0216: Pushed 
ca0189907a60: Pushed 
687f796c98d5: Pushed 
6beef49679a3: Pushed 
b0dce122021b: Pushed 
4de04bd13c4a: Pushed 
cf9b23ff5651: Pushed 
644fed2a3898: Pushed 
```

现在我们已经将 Docker 镜像推送到 ECR 仓库，我们可以使用它来通过 Fargate 设置我们的任务定义。

*如果你在这里遇到 EOF 问题，它很可能与 IAM 权限有关。确保给予它所需的所有权限，在此案例中是对 ECR 的完全访问权限，以便标记和推送镜像。*

## 角色和日志组

记住我之前告诉你过的，AWS 中你遇到的最大问题与不同服务之间的角色有关。

为了让这个流程顺利进行，我们需要确保在开始设置任务定义和 ECS 集群之前，先设置好一些必要的东西。

为了做到这一点，我们首先必须创建一个任务角色——这个角色需要从容器访问 AWS 生态系统中的服务——然后是执行角色——这样容器就能从 ECR 拉取 Docker 镜像。

```py
aws iam create-role --role-name etl-pipeline-task-role --assume-role-policy-document file://ecs-tasks-trust-policy.json
aws iam create-role --role-name etl-pipeline-execution-role --assume-role-policy-document file://ecs-tasks-trust-policy.json
```

我已经在你的文件夹中指定了一个名为 *ecs-tasks-trust-policy.json* 的 JSON 文件，它将用于创建这些角色。

对于我们正在推动的脚本，它不需要访问其他 AWS 服务的权限，因此目前**不需要为任务角色附加策略**。不过，你可能之后会需要这么做。

然而，对于**执行角色**，我们需要给予它 ECR 访问权限，以便拉取 Docker 镜像。

要将策略 *AmazonECSTaskExecutionRolePolicy* 附加到执行角色，运行这个命令。

```py
aws iam attach-role-policy --role-name etl-pipeline-execution-role --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
```

我们在此过程中还会创建最后一个角色——服务角色。如果你已经有了这个角色，则无需创建。

```py
aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com
```

如果你根本没有创建服务角色，当你尝试运行任务时，可能会遇到类似 *“无法假设服务链接角色。请验证 ECS 服务链接角色是否存在”* 的错误。

最后，我们创建一个日志组。创建日志组对于捕捉和访问容器生成的日志至关重要。

要创建日志组，你可以运行这个命令。

```py
aws logs create-log-group --log-group-name /ecs/etl-pipeline-logs
```

一旦你创建了执行角色、任务角色、服务角色以及日志组，我们就可以继续设置 ECS 任务定义。

## 创建 ECS 任务定义

任务定义是你的任务蓝图，指定使用哪个容器镜像、需要多少 CPU 和内存，以及其他配置。我们使用这个蓝图在 ECS 集群中运行任务。

我已经在你的代码中设置了任务定义，位于 *task-definition.json* 文件中。不过，你需要在其中设置你的账户 ID 和区域，以确保它按预期运行。

```py
{
    "family": "my-etl-task",
    "taskRoleArn": "arn:aws:iam::ACCOUNT_ID:role/etl-pipeline-task-role",
    "executionRoleArn": "arn:aws:iam::ACCOUNT_ID:role/etl-pipeline-execution-role",
    "networkMode": "awsvpc",
    "containerDefinitions": [
        {
            "name": "my-etl-container",
            "image": "ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/bigquery-etl-pipeline:latest", 
            "cpu": 256,
            "memory": 512,
            "essential": true,
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/etl-pipeline-logs",
                    "awslogs-region": "REGION",
                    "awslogs-stream-prefix": "ecs"
                }
            }
        }
    ],
    "requiresCompatibilities": ["FARGATE"],
    "cpu": "256",
    "memory": "512"
}
```

还记得我们在创建 ECR 仓库时获得的 URI 吗？我们将在这里使用它。记得执行角色、任务角色和日志组吗？我们也将在这里使用它们。

如果你将 ECR 仓库、角色和日志组的命名与我设置的完全相同，那么你只需更改帐户 ID 和区域在这个 JSON 文件中，或者确保 URI 是正确的。

你也可以在这里设置 CPU 和内存，来满足运行任务 —— 即运行你的代码 —— 的需求。我设置了 .25 vCPU 和 512 MB 的内存。

一旦你满意，可以在终端中注册任务定义。

```py
aws ecs register-task-definition --cli-input-json file://task-definition.json
```

现在你应该能够进入[**Amazon 弹性容器服务**](https://eu-north-1.console.aws.amazon.com/ecs/v2/getStarted?region=eu-north-1)，然后在**任务定义**中找到我们创建的任务。

![](../Images/738b981ded54f053375efbd240cd32cd.png)

在 ECS 中找到你创建的任务定义 | 作者提供的图片

这个任务 —— 即蓝图 —— 不会自动运行，我们稍后需要调用它。

## **创建一个 ECS 集群**

ECS 集群作为任务或服务的逻辑分组。当运行任务或创建服务时，你需要指定这个集群。

要通过 CLI 创建集群，请运行此命令。

```py
aws ecs create-cluster --cluster-name etl-pipeline-cluster
```

一旦你运行此命令，你将能够在 AWS 控制台的 ECS 中看到这个集群。

当我们为下一部分运行任务时，我们会将我们刚刚创建的**任务定义**附加到这个集群。

## 运行任务

在我们运行任务之前，我们需要获取可用的子网以及安全组 ID。

我们可以通过 CLI 在终端中直接执行此操作。

在终端中运行此命令以获取可用的子网。

```py
aws ec2 describe-subnets
```

你将返回一个对象数组，你需要查找每个对象的*SubnetId*。

> 如果你在这里遇到问题，请确保你的 IAM 拥有适当的权限。请查看根文件夹中的 aws_iam_user.json 文件，了解连接到 CLI 的 IAM 用户所需的权限。我强调这一点，因为这是我总是遇到的主要问题。

要获取安全组 ID，你可以运行此命令。

```py
aws ec2 describe-security-groups
```

你需要在终端中查找*GroupId*。

如果你至少得到了一个*SubnetId*和一个*GroupId*（安全组的 ID），我们就准备好运行任务，测试蓝图 —— 即任务定义 —— 是否有效。

```py
aws ecs run-task \
    --cluster etl-pipeline-cluster \
    --launch-type FARGATE \
    --task-definition my-etl-task \
    --count 1 \
    --network-configuration "awsvpcConfiguration={subnets=[SUBNET_ID],securityGroups=[SECURITY_GROUP_ID],assignPublicIp=ENABLED}"
```

请记得，如果你为集群和任务定义命名不同的名称，请更改这些名称。还要记得设置你的子网 ID 和安全组 ID。

现在，你可以导航到 AWS 控制台查看正在运行的任务。

![](../Images/c900f958079df0c32a1f170e3568613b.png)

查看你 ECS 集群中正在运行的任务 | 作者提供的图片

如果你遇到问题，可以查看日志。

如果成功，你应该能看到一些转换后的行被添加到 BigQuery。

## EventBridge 计划

现在，我们已经设置好任务在 ECS 集群中运行。但是我们感兴趣的是让它按计划运行。这就是 EventBridge 发挥作用的地方。

EventBridge 将设置我们的计划事件，我们也可以使用 CLI 来设置它。然而，在设置计划之前，我们首先需要创建一个新的角色。

> 这就是在使用AWS时的现实，每个服务都需要有相互交互的权限。

在这种情况下，EventBridge需要权限代表我们调用ECS集群。

在仓库中，你有一个名为*trust-policy-for-eventbridge.json*的文件，我已经放在那里，我们将使用这个文件来创建EventBridge角色。

将此粘贴到终端并运行。

```py
aws iam create-role \
    --role-name ecsEventsRole \
    --assume-role-policy-document file://trust-policy-for-eventbridge.json
```

然后我们需要将策略附加到这个角色。

```py
aws iam attach-role-policy \
    --role-name ecsEventsRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonECS_FullAccess
```

我们需要至少让它能够执行***ecs:RunTask***，但我们已经赋予它完全访问权限。如果你更喜欢限制权限，你可以创建一个只包含必要权限的自定义策略。

现在，让我们设置规则，安排任务在每个UTC时间上午5点使用任务定义运行。这通常是我希望它为我处理数据的时间，这样如果失败了，我可以在早餐后再查看。

```py
aws events put-rule \
    --name "ETLPipelineDailyRun" \
    --schedule-expression "cron(0 5 * * ? *)" \
    --state ENABLED
```

你应该会收到一个包含*RuleArn*字段的对象。这只是为了确认它是否成功。

下一步是将规则与ECS任务定义关联。

```py
aws events put-targets --rule "ETLPipelineDailyRun" \
    --targets "[{\"Id\":\"1\",\"Arn\":\"arn:aws:ecs:REGION:ACCOUNT_NUMBER:cluster/etl-pipeline-cluster\",\"RoleArn\":\"arn:aws:iam::ACCOUNT_NUMBER:role/ecsEventsRole\",\"EcsParameters\":{\"TaskDefinitionArn\":\"arn:aws:ecs:REGION:ACCOUNT_NUMBER:task-definition/my-etl-task\",\"TaskCount\":1,\"LaunchType\":\"FARGATE\",\"NetworkConfiguration\":{\"awsvpcConfiguration\":{\"Subnets\":[\"SUBNET_ID\"],\"SecurityGroups\":[\"SECURITY_GROUP_ID\"],\"AssignPublicIp\":\"ENABLED\"}}}}]"
```

记得在这里设置你自己的值，包括区域、账户号码、子网和安全组。

*使用我们之前获得的子网和安全组。你可以设置多个子网。*

一旦你运行了命令，任务将会被安排在每天的UTC时间5点执行，你可以在AWS控制台的“定时任务”中找到它。

你也可以直接在控制台中设置你的定时任务，这样更简单，因为子网ID和安全组已经为你设置好了。

## AWS Secrets Manager（可选）

所以，将你的Google凭证保存在根文件夹中并不是理想的做法，即使你已经限制了Google服务账户对数据集的访问权限。

在这里，我们可以选择将这些凭证移动到另一个AWS服务，然后从我们的容器中访问它。

为了使其工作，你需要将凭证文件移动到Secrets Manager，调整代码以便它能够获取凭证进行身份验证，并确保任务角色有权限代表你访问AWS Secrets Manager。

完成后，你可以将更新的docker镜像推送到之前设置的ECR仓库。

## 最终结果

现在，你已经在AWS上按计划运行了一个非常简单的ETL管道。目的是你可以在此基础上添加自己的数据转换。

希望这对任何过渡到简单、成本效益高且直接的方式在ECS上设置长期运行的数据转换脚本的人来说是有帮助的。

如果你遇到任何问题，请告诉我，以防我遗漏了什么。

❤
