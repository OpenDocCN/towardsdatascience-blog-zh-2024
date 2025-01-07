# 使用 Terraform 和本地 yaml 文件管理开发和生产环境

> 原文：[`towardsdatascience.com/manage-dev-and-prod-using-terraform-and-local-yaml-files-a0db7afb10a2?source=collection_archive---------4-----------------------#2024-06-24`](https://towardsdatascience.com/manage-dev-and-prod-using-terraform-and-local-yaml-files-a0db7afb10a2?source=collection_archive---------4-----------------------#2024-06-24)

## 使用 yaml 文件让你的 terraform 在多个项目中运作

[](https://medium.com/@danlowgw?source=post_page---byline--a0db7afb10a2--------------------------------)![Daniel Low](https://medium.com/@danlowgw?source=post_page---byline--a0db7afb10a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a0db7afb10a2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0db7afb10a2--------------------------------) [Daniel Low](https://medium.com/@danlowgw?source=post_page---byline--a0db7afb10a2--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0db7afb10a2--------------------------------) ·3 分钟阅读·2024 年 6 月 24 日

--

在企业生产环境中，通常会将云项目分为开发（dev）和生产（prod）环境，以隔离这两个环境。作为工程师，能够在（dev）环境中进行必要的测试和开发，并在批准后快速高效地将它们移到（prod）环境中，是非常重要的。

![](img/d804850b95a696cc600926446ef80166.png)

图片来源：[Alvaro Reyes](https://unsplash.com/@alvarordesign?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

正如预期的那样，解决方案有很多，terragrunt 是我首先想到的一个。然而，并不是每个人都熟悉 terraform，更不用说 terragrunt 了。我如何简化这个模板，让团队中的其他人只需使用 terraform 和 yaml 文件就能完成？以下是我对简化实现的看法。

**1\. 仓库结构**

作为本文的参考，以下是我如何构建我的仓库的。

```py
.
├── environment            # Folder to contain both dev and prod yaml
│   ├── dev-config.yaml
│   └── prod-config.yaml
├── modules                # Folder to contain terraform code by services
│   ├── module1
│   │   └──main.tf
│   └── module2
│       └── main.tf
├── backend.tf             # backend file to configure terraform backend
├── locals.tf              # locals file to point to environment yaml
└── main.tf                # main file to point to modules
```

**2\. 创建你的 terraform locals.tf 文件**

locals.tf 文件是 terraform 与本地存储文件交互的默认方式。你的 locals.tf 文件应指向你想要工作的 terraform 工作区，这样就能调用正确的配置文件。

```py
# Select the workspace
terraform workspace select dev
# This should run terraform plan against 'environment/dev-config.yaml' file
terraform plan
```

使用可以通过 git 提交的本地文件比在多个开发人员之间分别维护多个 .tfvars 文件更为推荐。请务必小心，不要在 yaml 文件中提交敏感信息。

此外，terraform 确实允许使用其他类型的配置文件，而不仅仅是 yaml，包括 json 和文本文件 [查看 [link](https://developer.hashicorp.com/terraform/language/functions/yamldecode)]。

```py
# filename: locals.tf
locals {
    config = yamldecode(file("environment/${terraform.workspace}-config.yaml"))
}
```

**2\. 为开发和生产环境创建配置文件**

在环境目录中，创建你的配置文件。请确保在命名时，文件名与 Terraform 工作区的名称一致，因为我们将使用变量`${terraform.workspace}`来区分这两个文件。

```py
# filename: dev-config.yaml
project_id: "cloud-project-dev"  
terraform_state_bucket_name: "terraform_state_bucket-name"
some_dataset_map:
  dataset_1:
    description: "dataset_1"
  dataset_2:
    description: "dataset_2"
```

```py
# filename: prod-config.yaml
project_id: "cloud-project-prod"  
terraform_state_bucket_name: "terraform_state_bucket-name"
```

**3. 模块**

我使用模块块来分隔所使用的资源。对于模块中应包含哪些内容没有严格的规则，但我通常会将为同一服务启动的资源组合在一起（例如，一个将数据写入 SQL 表的 Airflow 任务，或者一个虚拟机及其关联的负载均衡器）。

这里的关键是你的模块指向本地的 config，这里指的是我们创建的 yaml 文件。然后，locals.tf 文件应处理其余的部分。

```py
# filename: main.tf
module "module1" {
  source                      = "./modules/module1"
  terraform_state_bucket_name = local.config.terraform_state_bucket_name
  location                    = local.config.location
}

module "module2" {
  source                      = "./modules/module2"
  project_id                  = local.config.project_id
}
```

**来源**

+   Terraform 工作区： [链接](https://developer.hashicorp.com/terraform/language/state/workspaces)

+   Terraform 本地值： [链接](https://developer.hashicorp.com/terraform/language/values/locals)
