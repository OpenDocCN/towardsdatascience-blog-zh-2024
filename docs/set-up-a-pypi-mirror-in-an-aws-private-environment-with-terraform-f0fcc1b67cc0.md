# 在 AWS 私有环境中使用 Terraform 设置 Pypi 镜像

> 原文：[https://towardsdatascience.com/set-up-a-pypi-mirror-in-an-aws-private-environment-with-terraform-f0fcc1b67cc0?source=collection_archive---------7-----------------------#2024-03-06](https://towardsdatascience.com/set-up-a-pypi-mirror-in-an-aws-private-environment-with-terraform-f0fcc1b67cc0?source=collection_archive---------7-----------------------#2024-03-06)

[](https://medium.com/@florentpajot?source=post_page---byline--f0fcc1b67cc0--------------------------------)[![Florent Pajot](../Images/ff375f53bcfdec60e84a24068431361d.png)](https://medium.com/@florentpajot?source=post_page---byline--f0fcc1b67cc0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f0fcc1b67cc0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f0fcc1b67cc0--------------------------------) [Florent Pajot](https://medium.com/@florentpajot?source=post_page---byline--f0fcc1b67cc0--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f0fcc1b67cc0--------------------------------) ·5 分钟阅读·2024年3月6日

--

如果没有互联网访问，如何在环境中安装 Python 包？我最近在为我的团队在 AWS 上创建 AWS Sagemaker Studio 环境时遇到了这个问题。

## 为 Sagemaker 构建 AWS 私有环境

对于这个特定项目，我在仅限 VPC 模式下设置了 Sagemaker，并且有一个保持架构私有的约束，这意味着创建 VPC 和私有子网，但没有互联网访问。

所有网络通信，包括与 AWS API 的应用通信，必须通过 VPC Endpoint 接口进行。这可以确保连接安全，因为发送和接收的数据不会通过互联网，而是使用 AWS 网络骨干进行传输。

这特别适用于限制暴露于安全风险，尤其是在处理个人信息或必须遵守某些安全标准时。

![](../Images/980cca82296b30632ecaa6058b1111f4.png)

图片来自 [Nadir sYzYgY](https://unsplash.com/@nadir_syzygy?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 从 AWS Sagemaker 访问 Pypi 包仓库

在我的团队中，数据科学家使用 Python 作为主要语言，有时需要一些[Sagemaker 预构建 Python 镜像中未提供的 Python 包](https://docs.aws.amazon.com/sagemaker/latest/dg/docker-containers-prebuilt.html)，因此我将重点讨论这个用例。幸运的是，解决方案也适用于其他语言和仓库，例如 npm。

用户通常会尝试通过 pip 命令安装所需的任何包。但是，由于不允许互联网访问，这个命令会失败，因为 pip 无法连接到 Pypi.org 服务器。

**开启互联网**

一种选择是开放互联网访问并允许向 Pypi.org 服务器使用的 Fastly CDN IP 地址发出外部 HTTP 连接。但在我们的情况下这是不可行的，因为我们不希望架构中有任何互联网连接。

**使用专用 Pypi 服务器**

[AWS 博客还提供了一个使用名为 Bandersnatch 的 Python 包的示例](https://aws.amazon.com/fr/blogs/machine-learning/hosting-a-private-pypi-server-for-amazon-sagemaker-studio-notebooks-in-a-vpc/)。这篇文章描述了如何设置一个服务器，作为堡垒主机，镜像 Pypi，并且只对你的私有子网可访问。

这不是一个可行的选项，因为你需要提前知道需要提供哪些 Python 包，并且你还得创建公共子网并为 Pypi 服务器镜像提供互联网访问权限。

**使用 AWS Codeartifact**

这最终是我想出的解决方案，并且在我的情况下是有效的。

[AWS Codeartifact](https://aws.amazon.com/fr/codeartifact/) 是 AWS 提供的工件管理解决方案。它与其他 AWS 服务兼容，如 AWS 服务目录，用于控制组织内资源的访问。

要使用它，你必须创建一个“域”，它作为一个总览来管理访问权限并在你的组织内应用政策。然后，你需要创建一个仓库，供不同的应用程序使用你的工件。

此外，一个仓库可以拥有上游仓库。因此，如果目标仓库中没有某个 Python 包，需求将传递到上游仓库以进行满足。

更准确地说，这个工作流考虑了包版本。官方文档提供了详细的工作流：

> 如果 `my_repo` 包含请求的包版本，它将返回给客户端。
> 
> 如果 `my_repo` 不包含请求的包版本，CodeArtifact 会在 `my_repo` 的上游仓库中查找它。如果找到该版本的包，将其引用复制到 `my_repo` 中，并将包版本返回给客户端。
> 
> 如果 `my_repo` 和它的上游仓库都不包含该包版本，客户端将收到 HTTP 404 `未找到` 响应。

酷吧？它甚至会缓存包版本，以便将来的请求使用。

这正是我们将使用的策略，因为 AWS Codeartifact 允许我们定义一个具有外部连接（如 Pypi）作为上游仓库的仓库。

## 使用 Terraform 创建 AWS Codeartifact 资源

由于 AWS Codeartifact 是 AWS 服务，你可以轻松地在你的环境 VPC 中创建一个 VPC 端点来连接它。

注：我正在使用 Terraform v1.6.4 和 AWS 提供程序 v5.38.0

```py
locals {
  region = "us-east-1"
}

resource "aws_security_group" "vpce_sg" {
  name        = "AllowTLS"
  description = "Allow TLS inbound traffic and all outbound traffic"
  vpc_id      = aws_vpc.your_vpc.id

  tags = {
    Name = "allow_tls_for_vpce"
  }
}

resource "aws_vpc_security_group_ingress_rule" "allow_tls_ipv4" {
  security_group_id = aws_security_group.allow_tls.id
  cidr_ipv4         = aws_vpc.your_vpc.cidr_block
  from_port         = 443
  ip_protocol       = "tcp"
  to_port           = 443
}

data "aws_iam_policy_document" "codeartifact_vpce_base_policy" {
  statement {
    sid    = "EnableRoles"
    effect = "Allow"
    actions = [
      "codeartifact:GetAuthorizationToken",
      "codeartifact:GetRepositoryEndpoint",
      "codeartifact:ReadFromRepository",
      "sts:GetServiceBearerToken"
    ]
    resources = [
      "*",
    ]
    principals {
      type = "AWS"
      identifiers = [
        aws_iam_role.your_sagemaker_execution_role.arn
      ]
    }
  }
}

resource "aws_vpc_endpoint" "codeartifact_api_vpce" {
  vpc_id            = aws_vpc.your_vpc.id
  service_name      = "com.amazonaws.${local.region}.codeartifact.api"
  vpc_endpoint_type = "Interface"
  subnet_ids        = aws_subnets.your_private_subnets.ids

  security_group_ids = [
    aws_security_group.vpce_sg.id,
  ]

  private_dns_enabled = true
  policy              = data.aws_iam_policy_document.codeartifact_vpce_base_policy.json
  tags = { Name = "codeartifact-api-vpc-endpoint" }
}
```

然后，你需要创建不同的资源，以便 Codeartifact 能通过镜像 Pypi 处理你对新 Python 包的请求：一个域名，一个具有外部连接的 Pypi 仓库，以及一个将 Pypi 定义为上游仓库的仓库。

```py
resource "aws_codeartifact_domain" "my_domain" {
  domain = "my-domain"

  encryption_key = ""

  tags = { Name = "my-codeartifact-domain" }
}

resource "aws_codeartifact_repository" "public_pypi" {
  repository = "pypi-store"
  domain     = aws_codeartifact_domain.my_domain.domain

  external_connections {
    external_connection_name = "public:pypi"
  }

  tags = { Name = "pypi-store-repository" }
}

resource "aws_codeartifact_repository" "my_repository" {
  repository = "my_repository"
  domain     = aws_codeartifact_domain.my_domain.domain

  upstream {
    repository_name = aws_codeartifact_repository.public_pypi.repository
  }

  tags = { Name = "my-codeartifact-repository" }
}

data "aws_iam_policy_document" "my_repository_policy_document" {
  statement {
    effect = "Allow"

    principals {
      type        = "AWS"
      identifiers = [aws_iam_role.your_sagemaker_execution_role.arn]
    }

    actions   = ["codeartifact:ReadFromRepository"]
    resources = [aws_codeartifact_repository.my_repository.arn]
  }
}

resource "aws_codeartifact_repository_permissions_policy" "my_repository_policy" {
  repository      = aws_codeartifact_repository.my_repository.repository
  domain          = aws_codeartifact_domain.my_domain.domain
  policy_document = data.aws_iam_policy_document.my_repository_policy_document.json
}
```

就是这样！你现在可以轻松为你的私有环境设置一个 Pypi 镜像。

为了使其可用，你还需要告诉 pip 命令将请求指向特定的索引。幸运的是，AWS 创建了一个 API 来为你完成繁重的工作。只需将以下代码添加到你的项目中，即可使其工作：

```py
aws codeartifact login --tool pip --repository $CODE_ARTIFACT_REPOSITOR_ARN --domain $CODE_ARTIFACT_DOMAIN_ID --domain-owner $ACCOUNT_ID --region $REGION
```

最后但同样重要的是，在你的 VPC 中为 AWS Codeartifact 添加一个 VPC 端点。

```py
data "aws_iam_policy_document" "codeartifact_vpce_base_policy" {
  statement {
    sid    = "EnableRoles"
    effect = "Allow"
    actions = [
      "codeartifact:GetAuthorizationToken",
      "codeartifact:GetRepositoryEndpoint",
      "codeartifact:ReadFromRepository",
      "sts:GetServiceBearerToken"
    ]
    resources = [
      "*",
    ]
    principals {
      type = "AWS"
      identifiers = [
        aws_iam_role.your_sagemaker_execution_role.arn
      ]
    }
  }
}

resource "aws_vpc_endpoint" "codeartifact_api_vpce" {
  vpc_id            = aws_vpc.your_vpc.id
  service_name      = "com.amazonaws.${local.region}.codeartifact.api"
  vpc_endpoint_type = "Interface"
  subnet_ids        = aws_subnets.your_private_subnets.ids

  security_group_ids = [
    aws_security_group.vpce_sg.id,
  ]

  private_dns_enabled = true
  policy              = data.aws_iam_policy_document.codeartifact_vpce_base_policy.json
  tags = { Name = "codeartifact-api-vpc-endpoint" }
}
```

如果你想接收有关 AWS 等方面的最新帖子通知，[请在此订阅](https://medium.com/@florentpajot/subscribe)。

*你知道你可以多次拍手吗？*
