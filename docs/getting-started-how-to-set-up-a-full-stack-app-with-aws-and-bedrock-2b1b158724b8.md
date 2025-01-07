# 我希望我早就学到的 AWS Bedrock 教程：你需要了解的一切，以便为 AWS 基础设施准备你的机器

> 原文：[`towardsdatascience.com/getting-started-how-to-set-up-a-full-stack-app-with-aws-and-bedrock-2b1b158724b8?source=collection_archive---------7-----------------------#2024-10-22`](https://towardsdatascience.com/getting-started-how-to-set-up-a-full-stack-app-with-aws-and-bedrock-2b1b158724b8?source=collection_archive---------7-----------------------#2024-10-22)

## *第一部分：节省大量处理碎片化和不完整文档的时间，并在不到一小时内准备好你的开发环境*

[](https://medium.com/@minda?source=post_page---byline--2b1b158724b8--------------------------------)![Minda Myers](https://medium.com/@minda?source=post_page---byline--2b1b158724b8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2b1b158724b8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b1b158724b8--------------------------------) [Minda Myers](https://medium.com/@minda?source=post_page---byline--2b1b158724b8--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b1b158724b8--------------------------------) ·阅读时间 9 分钟·2024 年 10 月 22 日

--

![](img/3fe2ea60a6c195a0c4214993c5e19b37.png)

图片由作者使用 [Midjourney](https://www.midjourney.com/) 生成

如何将笔记本中的一个巧妙的小型机器学习原型，开发成一个强大的全栈 Web 应用程序？虽然这个过程看起来可能令人望而生畏，但这系列多篇文章将帮助你一步一步地应对学习曲线，带你解决即使是最棘手的权限问题（AWS 就是以此而闻名）。通过本系列的学习，你将拥有一个完全功能的语言翻译应用程序可以进行实验，并掌握快速扩展自己的 GenAI 流水线所需的概念性知识。

这是我们新系列的第一部分，主题是 🌊 《构建由 Amazon Bedrock 支持的全栈 GenAI 应用程序的广泛入门指南》。

*本系列文章与* [*Vlad Seredniy*](https://www.linkedin.com/in/vladyslav-seredniy-6b636b52/)* 合作撰写。*

# 本系列的内容是什么？

使用 Amazon Bedrock 开发全栈应用程序可能会带来相当陡峭的学习曲线。新开发者需要应对 AWS 基础设施的复杂性以及集成 AI 流程的各种细节。试图学习 AWS 技术栈很容易变得非常沮丧，尤其是当面对零散的文档和在前端与后端系统之间定制数据流的耗时过程时。开发者常常会花费数小时观看关于 AWS 技术栈的概念视频，但这些视频的实际应用往往非常有限。

在本系列中，我们将直接面对这些挑战。我们将一步一步地教你如何使用 AWS 基础设施、React、Node.js 和 Amazon Bedrock 构建一个全栈 AI 应用，帮助你全面理解并（希望）最小化困惑。

我们将使用[这个教程](https://aws.amazon.com/blogs/mobile/create-a-fullstack-sample-web-app-powered-by-amazon-bedrock/)作为起点。我们将填补所有空白，帮助你启动，从如何设置技术栈（在本教程中）开始，然后继续理解应用程序代码库，并添加新的功能，比如音频转录、翻译和生成——本质上是实现自定义的 AI 流程。从简化 AWS 设置和揭开权限的神秘面纱，到提供前端、后端和 Lambda 函数如何协同工作的全面代码概述，我们旨在减少摩擦，降低全栈集成的门槛。在这个过程中，我们将尽力彻底回答任何在工作环境中可能不敢提问的“傻问题”。在整个过程中结束时，我们希望能为你提供所有工具和信心，去追寻你自己的 AI 想法和创作！

> *本系列基于*这个项目*：* [***AWS AppSync AI Agent Playground***](https://github.com/aws-samples/appsync-bedrock-sample-with-ai-agent)*和*本教程*：* [***创建一个由 Amazon Bedrock 提供支持的全栈示例 Web 应用***](https://aws.amazon.com/blogs/mobile/create-a-fullstack-sample-web-app-powered-by-amazon-bedrock)

**在第一部分**（本部分）中，我们将详细讲解**设置 AWS 基础设施并使用 Amazon Bedrock 启动一个示例全栈应用的必要步骤**。我们将介绍如何创建你的 AWS 账户，提供关于配置 IAM 用户和获取密钥的教程视频，并安装 Node.js 和 AWS CDK 等工具。完成第一部分后，你将拥有一个**完全配置的环境，准备进行定制**。

**在第二部分**，我们将解决新开发者在开始使用 AWS 技术栈时面临的最大挑战之一：缺乏清晰的文档。我们将带你浏览项目的前端代码库，并详细介绍如何在 React 中创建组件，以**捕获并上传音频文件到 S3 存储桶**进行处理。

当我们与开发者交流时，他们会提到权限管理是使用 AWS 基础设施时最具挑战性的方面。在这一部分，我们将介绍一些复杂的 AWS 配置细节，如设置 IAM 角色和策略，以及为你的 S3 存储桶配置 CORS。

**第三部分**是最有趣的部分！我们将破解示例项目后端 Python 文件中的一个代理，集成我们的 GenAI 工具。具体来说，我们将利用**AWS Transcribe**来**转录音频**，并利用**Bedrock**的能力查询**Anthropic 的 Claude API**来**翻译**转录过程中的文本。

最后，在系列的结论部分，在第四部分，我们将与 Deepgram 的 AI 驱动语音生成 API 对接，**生成一个可以在应用界面中播放的翻译音频文件**。

当你完成这一系列内容时，你可以期待一个完整的、由 AI 驱动的语言翻译应用，并且能够理解如何用你自己的 GenAI 工具定制它。

## **什么时候 AWS 基础设施是你项目的正确选择？**

显然，回答这个问题超出了本文的范围。然而，我们希望简要地讨论一下这个话题，区分原型制作、测试应用和大规模部署。

根据我们的经验，在我们刚开始时，在学习或尝试阶段，使用像[Jupyter Notebook](https://jupyter.org/)、[Google Colab](https://colab.research.google.com)甚至[Lightning AI](https://lightning.ai/)这样的工具来实时测试和迭代想法往往要快得多。一旦我们选择了要使用的技术栈并具备了核心功能（并解决了技术问题），我们会构建一个简易的原型。在这个阶段，我们可能会使用一个比 AWS 更简单的系统，比如[Flutterflow](https://www.flutterflow.io/)或[Streamlit](https://streamlit.io/)。

然而，当一个应用需要处理增加的流量或强大的安全功能时，AWS 具有巨大的优势，因为它可以在基础设施需求增长时无缝扩展。

# 精简的 AWS 配置

本文的剩余部分将专注于正确设置示例 AWS Bedrock 应用，并使其能够在你的开发机器上本地运行。由于亚马逊网站上的文档碎片化，这一部分可能特别棘手。我们将扩展原教程[前提条件](https://aws.amazon.com/blogs/mobile/create-a-fullstack-sample-web-app-powered-by-amazon-bedrock/#:~:text=Kit%20(CDK)%20construct.-,Prerequisites,-To%20follow%20this)部分的说明，并详细描述正确安装所有要求的步骤。

[前提条件](https://aws.amazon.com/blogs/mobile/create-a-fullstack-sample-web-app-powered-by-amazon-bedrock/#:~:text=Kit%20(CDK)%20construct.-,Prerequisites,-To%20follow%20this)

📌 ***注意：sudo 和命令行使用***

> *接下来的很多部分都需要大量使用命令行 / 终端。如果你遇到权限错误，可以尝试在命令前加上* `***sudo***`*。*
> 
> *例如，不要使用* `***npm install -g aws-cdk***` *，可以试试* `***sudo npm install -g aws-cdk***`*。*

# 创建你的 AWS IAM 用户

对初学者来说，设置 AWS 账户并配置正确的凭证可能是最初的（也是最让人沮丧的）部分。下面是一个逐步的过程，让你不必花费数小时在 AWS 网站和文档中来回搜索，拼凑正确的操作顺序。

**注册一个** [**AWS 账户**](http://aws.amazon.com)**。**

然后登录并在控制台中导航到 IAM。点击用户 -> **创建用户**

现在，你需要在创建用户流程中自定义以下详细信息。

**步骤 1：指定用户详细信息**

+   勾选 **为用户提供 AWS 管理控制台访问权限**

+   选择 **我想创建一个 IAM 用户**

+   在控制台密码部分，选择 **自定义密码**。*输入一个安全且易于记住的密码。*

+   取消勾选 **用户首次登录时必须重置密码**，这样用户就不需要在第一次登录时重置密码。

**步骤 2：设置权限**

+   选择 **直接附加策略**。

+   在搜索栏中，输入 **AdministratorAccess**

+   在搜索结果中勾选 **AdministratorAccess** 策略旁的复选框。

接下来，在 **步骤 3** 中点击 **创建用户**，然后在 **步骤 4** 中点击 **返回用户列表**。*记得将你的密码保存在安全的地方。*

设置好 IAM 用户后，你需要安装所需的软件工具，从 Node.js 开始。

# 安装 Node 和 NPM（Node 包管理器）

在本地机器上设置任何 Amazon 开发工具之前，你必须已经安装了 NPM（Node 包管理器）。我们的下一步将是安装 Node.js，它包含了 NPM。示例项目要求 Node.js v18 或更高版本，因此我们将安装最新版本以满足这个依赖。

**对于 Mac：**

+   访问 [Node.js 官方网站](https://nodejs.org/en/download/package-manager)。你可以将命令复制粘贴到终端中，通过命令行下载并安装，或者直接下载 Mac 版本的安装程序。

**对于 Linux（Ubuntu）：**

```py
sudo apt install nodejs npm
```

验证安装：

```py
node --version 
npm --version
```

安装好 Node.js 后，下一步是使用 NPM 设置 AWS 云开发工具包（CDK）。

# 在开发机器上配置你的 AWS 账户

现在，我们将向你展示如何使用 NPM 安装 [AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html)。示例应用需要 AWS CDK 2.103 或更高版本，因此我们将下载最新版本（以确保满足此要求）。

输入以下命令来全局安装 AWS CDK：

`sudo npm install -g aws-cdk`

让我们验证一下安装：

`cdk --version`

安装好 CDK 后，我们可以安装 AWS CLI（命令行界面），它将帮助你配置 AWS 账户凭证。

## 安装和配置 AWS CLI

设置账户凭证对正确设置示例应用程序至关重要，因此我们来介绍如何安装 AWS CLI 并正确设置你的账户。

## 安装 AWS CLI

安装 AWS CLI 的方法在不同操作系统上稍有不同。

**对于 Linux**，下载并运行安装程序：

```py
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**对于 Mac**，从[AWS CLI 网站](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)下载 *.pkg 文件，并按照安装向导中的指示进行操作。

让我们通过验证安装是否成功来完成这一步：

`aws --version`

## 创建访问密钥

现在我们已经安装了 AWS CLI，可以继续创建访问密钥。如果你已经按照视频中的步骤 1 创建了访问密钥，那就跳过这一步。如果没有，请回顾视频结尾部分或按照以下指示操作：

+   在 IAM 控制台中，查找你想要创建访问密钥的用户。

+   在用户摘要部分，点击“创建访问密钥”

+   勾选“我理解上述推荐，并希望继续创建访问密钥”

+   将你的访问密钥和秘密访问密钥保存在安全的位置。

## 配置 AWS CLI

我们快完成了！只需最后一步，你的账户就能在开发机器上正确配置。

运行此命令启动配置过程：

`aws configure`

系统将要求你输入以下内容：

+   AWS 访问密钥 ID（从你的安全位置复制/粘贴）

+   AWS 秘密访问密钥（从你的安全位置复制/粘贴）

+   默认区域名称（我们使用了`*us-east-1*`）

+   默认输出格式（使用`*json*`）

完成此步骤后，你的账户信息将已配置完毕，可以继续准备部署 AWS CDK 基础设施。

# 启动 CDK

接下来，我们将展示如何在你希望部署的区域中启动 CDK。

```py
cdk bootstrap aws://ACCOUNT-NUMBER/REGION
```

在这里，你需要使用你的 12 位账户号码（可以在 AWS 控制台屏幕右上角找到）作为 **ACCOUNT-NUMBER**，以及你的区域（我们使用`*us-east-1*`）作为 **REGION**。

你快完成了！现在只需安装项目所需的其他工具，就能设置好示例应用程序。

# 安装 Yarn、Git 和 Docker

本项目使用 yarn 来管理依赖项。你可以通过 NPM 安装它：

`sudo npm install -g yarn`

你可以验证 yarn 是否正确安装：

`yarn --version`

## Git:

Git 在 Mac 和大多数 Linux 发行版上是预装的。你可以验证它是否已安装。

```py
git --version
```

如果尚未安装，在 Ubuntu 上你可以使用 apt-get `sudo apt-get install git`，而在 Mac 上可以从官方 Git 网站下载。

## Docker:

**在 Mac 上**，你可以从[官方 Docker 网站](https://www.docker.com/get-started/)下载 Docker Desktop for Mac，然后只需按照安装向导进行操作。

**对于 Linux（Ubuntu）**，你可以参考官方的[Ubuntu 上的 Docker 安装指南](https://docs.docker.com/desktop/install/linux/)。

# 结论

恭喜你！你已完成本系列的第一部分，现在应该已经设置好开发环境并安装了所有依赖项，以便你可以部署这个 AWS 示例应用。你现在可以从原始教程的[设置](https://aws.amazon.com/blogs/mobile/create-a-fullstack-sample-web-app-powered-by-amazon-bedrock/#:~:text=Docker-,Setup,-Cloning%20the%20repository)部分继续，使用 Git 下载项目代码。

## **下一步**：

AWS 服务和设置过程可能会有所变化，因此尽管我们已经尽力使这些说明保持最新，但如果遇到问题，你可以随时查阅 AWS 文档以获取最新信息。

我们将在接下来的几周内深入讨论为交互式翻译应用添加所有自定义功能的实现细节。这将为你提供机会，深入理解相关概念，并构建对技术栈的理解。

> 👉 ***你将在第二部分找到的内容：***
> 
> 在第二部分，我们将放大视角，概述示例应用的架构。我们将展示如何自定义前端以捕获并存储语音录音，以及如何通过 API 将其传递到后台。
