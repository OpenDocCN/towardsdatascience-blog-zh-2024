# 理解Dataform术语和身份验证流程

> 原文：[https://towardsdatascience.com/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb?source=collection_archive---------11-----------------------#2024-05-15](https://towardsdatascience.com/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb?source=collection_archive---------11-----------------------#2024-05-15)

## MLOps：数据管道编排

## Dataform 101系列第一部分：单仓库多环境Dataform的基础知识，带有最小权限访问控制和基础设施即代码设置

[](https://koakande.medium.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------)[![Kabeer Akande](../Images/5e1f083e75741690ae27b00d1e5f1dd3.png)](https://koakande.medium.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------) [Kabeer Akande](https://koakande.medium.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aa98c2fbcdfb--------------------------------) ·阅读时长8分钟·2024年5月15日

--

![](../Images/086397a760f9b97d80411a839a05f989.png)

Dataform在数据管道中的典型定位 *[图片来自作者]*

[Dataform](https://cloud.google.com/dataform?hl=en)是一个集成在GCP服务套件中的新服务，使团队能够开发和操作复杂的基于SQL的数据管道。Dataform使得软件工程的最佳实践得以应用，如测试、环境管理、版本控制、依赖管理、编排和自动化文档等，这些都可以用于数据管道。它是GCP中一个无服务器的SQL工作流编排引擎。通常，如上图所示，Dataform将原始数据进行转化，应用所有工程最佳实践，并输出一个结构良好的数据，准备好供后续使用。

这篇文章的灵感来自于我在将我们一个项目的遗留Dataform从Web UI迁移到GCP BigQuery时的经历。在迁移过程中，我发现像发布配置、工作流配置和开发工作区等术语非常令人困惑，难以理解。这也促使我写这篇文章，解释GCP Dataform中使用的一些新术语。此外，我还会简要介绍在GCP中使用单仓库多环境Dataform操作的一些基本流程。设置Dataform有多种方式，务必查看Google提供的[最佳实践](https://cloud.google.com/dataform/docs/best-practices)。

这是关于 Dataform 基础知识和设置的两部分系列中的第一部分。在第二部分中，我将提供 Terraform 设置的演示，展示如何在配置 Dataform 时实现最小权限控制。如果你想先睹为快，确保查看 [repo](https://github.com/kbakande/terraforming-dataform)。

# 术语

Dataform 的实现类似于 GitHub 工作流。我将对两者之间的相似性进行对比，并创建类比以帮助理解。可以把 Dataform 想象成一个本地的 GitHub 仓库。在设置 Dataform 时，它会请求配置一个远程仓库，类似于本地 GitHub 与远程 origin 配对。考虑到这种情境设置，我们快速浏览一些 Dataform 的术语。

## 开发工作空间

这类似于本地 GitHub 分支。类似于从 GitHub 主分支创建分支，新的 Dataform 开发工作空间会检出主 Dataform 仓库代码的可编辑副本。开发工作空间彼此独立，类似于 GitHub 分支。代码的开发和实验会在开发工作空间中进行，当代码被提交并推送后，会创建一个与开发工作空间名称相似的远程分支。值得一提的是，用于检出可编辑代码到开发工作空间的 GitHub 仓库是可配置的，可以是主分支或远程仓库中的任何其他分支。

## 发布配置

Dataform 使用 `.sqlx` 脚本和 Javascript `.js` 混合进行数据转换和逻辑处理。因此，它首先会生成代码库的编译版本，以获得标准化和可重复的代码库管道表示，并确保脚本能够转化为数据。发布配置是这一编译过程的自动化流程。在配置的时间，Dataform 会检出远程主仓库中的代码（这可以配置并修改为指向任何远程分支），并将其编译成 JSON 配置文件。检出代码和生成编译的过程正是发布配置所涵盖的内容。

## 工作流配置

因此，发布配置的输出是一个 `.json` 配置文件。工作流配置决定了何时运行该配置文件、由哪个身份运行以及该配置文件的输出将被写入哪个环境。

由于工作流配置需要使用发布配置的输出，因此合理的做法是确保它在发布配置之后运行。原因是发布配置首先需要对远程仓库进行身份验证（这一过程有时会失败），然后检出代码并进行编译。虽然这些步骤在几秒钟内完成，但在网络连接失败的情况下可能需要更多时间。由于工作流配置需要发布配置生成的`.json`编译文件，因此将其安排在发布配置之后执行是有意义的。如果同时安排，工作流配置可能会使用之前的编译结果，意味着直到下一个工作流配置运行时，BQ表中的最新更改才会被反映出来。

## 环境

![](../Images/0c7a26009baa8f1952328168bac79c50.png)

单一仓库、多环境Dataform的架构流程

Dataform的一个特点是它能够将代码部署到不同的环境中，例如开发、暂存和生产环境。使用多个环境带来了如何设置Dataform的挑战。是否应该在多个环境中创建仓库，还是只在一个环境中创建？谷歌在Dataform最佳[实践](https://cloud.google.com/dataform/docs/best-practices)部分讨论了这些折衷。本篇文章展示了如何为暂存和生产环境设置Dataform，并将数据从一个仓库部署到两个环境中。

这些环境被设置为GCP项目，并为每个项目配置了自定义服务帐户。Dataform仅在暂存环境/项目中创建，因为我们会进行大量更改，最好在暂存（或非生产）环境中进行实验。此外，暂存环境被选为开发代码部署的环境。这意味着从开发工作空间生成的数据集和表会在暂存环境中进行部署。

一旦开发完成，代码就会被提交并推送到远程仓库。从那里，可以提出一个PR（Pull Request），并在审核后合并到主仓库。在预定的工作流中，发布和工作流配置都会执行。Dataform被配置为从主分支编译代码，并在生产环境中执行。因此，只有经过审核的代码会进入生产环境，任何开发中的代码会停留在暂存环境中。

总结一下，从上述Dataform架构流程来看，在开发工作空间中开发的代码会在暂存环境中进行部署，或者推送到远程GitHub，在那里经过同行评审并合并到主分支。发布配置从主分支编译代码，而工作流配置则取用编译后的代码并将其数据部署到生产环境。因此，只有GitHub主分支中经过审核的代码才会在生产环境中部署。

# 身份验证

Dataform 的身份验证可能会很复杂，尤其是在为多个环境配置时。我将使用预发布和生产环境的示例来说明如何完成此过程。让我们逐步解析身份验证需要在哪里进行，以及如何完成身份验证。

![](../Images/e69235544bfe4ac5096098a5cd028f03.png)

用于追踪身份验证的 Dataform 流程

上图展示了一个简单的 Dataform 工作流，我们可以用它来追踪身份验证所需的地方以及对应的资源。该流程描述了当 Dataform 在开发工作区和计划（发布及工作流配置）中运行时发生的事情。

**机器用户**

让我们来讨论一下机器用户。当 Dataform 在检出存储在远程仓库中的代码时，需要凭证来访问 GitHub。虽然可以使用个人凭证，但最佳做法是使用组织中的机器用户。这种做法确保 Dataform 流水线编排不依赖于个人身份，且不会因其离职而受到影响。设置机器用户意味着使用与个人无关的身份来创建 GitHub 账户，具体设置请参见 [这里](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys#machine-users)。对于 Dataform，机器用户账户会生成一个个人访问令牌（PAT），并将其作为密钥存储在 GCP 秘密管理器中。机器用户还应作为外部协作者添加到 Dataform 的远程仓库，并授予读取和写入权限。稍后我们将在 Terraform 代码中看到如何配置 Dataform 以访问这些秘密。如果用户决定使用自己的身份而不是机器用户，则应按照 [这里](https://cloud.google.com/dataform/docs/connect-repository#:~:text=You%20can%20authenticate%20GitHub%20and,on%20behalf%20of%20the%20developers.) 的说明生成令牌。

**GitHub 身份验证流程**

![](../Images/60723122dd91404c3301ccf121abfaf4.png)

使用机器用户的 GitHub 身份验证流程

Dataform 使用其默认服务账户进行操作，因此在执行 Dataform 操作时，首先使用默认服务账户。我假设你已经设置好了机器用户，将该用户添加为远程仓库的协作者，并将用户的 PAT 作为秘密添加到 GCP 秘密管理器中。为了进行 GitHub 身份验证，默认服务账户需要从秘密管理器中提取秘密。默认服务账户需要 *secretAccessor* 角色来访问该秘密。一旦访问了秘密，默认服务账户便可以模拟机器用户，并且由于机器用户已作为协作者添加到远程 Git 仓库，默认服务账户现在可以作为协作者访问远程 GitHub 仓库。该流程如 GitHub 身份验证流程图所示。

**开发工作区身份验证**

当从开发工作空间触发执行时，默认服务账户会假设暂存环境自定义服务账户的身份，以便在暂存环境中展示输出结果。为了能够模拟暂存环境自定义服务账户，默认服务账户需要在暂存服务账户上拥有*iam.serviceAccountTokenCreator*角色。这样，默认服务账户就可以创建一个短期有效的令牌，类似于用于模拟机器用户的PAT（个人访问令牌），从而模拟暂存自定义服务账户。因此，暂存自定义服务账户被授予写入BQ表格所需的所有权限，且默认服务账户在模拟该账户时将继承这些权限。

**工作流配置认证**

在检出代码库后，发布配置将生成一个已编译的配置`.json`文件，工作流配置将从中生成数据。为了将数据写入生产环境的BQ表格，默认服务账户需要在生产环境自定义服务账户上拥有*iam.serviceAccountTokenCreator*角色。与暂存自定义服务账户的操作类似，生产服务账户被授予写入生产环境BQ表格所需的所有权限，且默认服务账户在模拟该账户时将继承所有这些权限。

**总结**

总结来说，默认服务账户是主要的执行者。它通过模拟机器用户，使用机器用户的PAT来认证GitHub上的协作者身份。它还通过模拟各自的自定义服务账户来认证暂存和生产环境，使用的是通过*serviceAccountTokenCreator*角色生成的短期有效令牌。了解了这一点后，我们就可以开始使用Terraform在GCP中配置Dataform了。敬请期待本文的第二部分，或查看[代码库](https://github.com/kbakande/terraforming-dataform)以获取代码。

*图片来源*: 本文中的所有图片均由作者制作

## 参考资料

1.  [https://cloud.google.com/dataform?hl=en](https://cloud.google.com/dataform?hl=en)

1.  [https://cloud.google.com/dataform/docs/migration](https://cloud.google.com/dataform/docs/migration)

1.  [https://cloud.google.com/dataform/docs/best-practices](https://cloud.google.com/dataform/docs/best-practices)
