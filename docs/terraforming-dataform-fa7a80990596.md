# Terraform配置Dataform

> 原文：[https://towardsdatascience.com/terraforming-dataform-fa7a80990596?source=collection_archive---------7-----------------------#2024-05-31](https://towardsdatascience.com/terraforming-dataform-fa7a80990596?source=collection_archive---------7-----------------------#2024-05-31)

## MLOps：数据管道编排

## Dataform 101，第2部分：使用最小权限访问控制进行配置

[](https://koakande.medium.com/?source=post_page---byline--fa7a80990596--------------------------------)[![Kabeer Akande](../Images/5e1f083e75741690ae27b00d1e5f1dd3.png)](https://koakande.medium.com/?source=post_page---byline--fa7a80990596--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa7a80990596--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fa7a80990596--------------------------------) [Kabeer Akande](https://koakande.medium.com/?source=post_page---byline--fa7a80990596--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa7a80990596--------------------------------) ·阅读时间：7分钟·2024年5月31日

--

![](../Images/b3afbfd5e7ff061d52a5ae68268fab7e.png)

Dataform在数据管道中的典型定位 [作者提供的图片]

这是Dataform 101的结尾部分，展示了设置Dataform的基本原理，重点讲解其身份验证流程。第二部分重点讲解了在[第一部分](https://medium.com/towards-data-science/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb)中解释的流程的Terraform实现。

## Dataform配置

可以通过GCP控制台设置Dataform，但Terraform提供了一种优雅的方式来配置和管理诸如Dataform之类的基础设施。使用Terraform提供了可移植性、可重用性和基础设施版本控制等诸多好处。因此，在本节中，您需要具备Terraform知识。如果您熟悉Terraform，请前往[GitHub仓库](https://github.com/kbakande/dataform-001)并下载所有代码。如果不熟悉，可以参考Google Cloud Skills Boost提供的[资源](https://www.cloudskillsboost.google/course_templates/443)来入门。

![](../Images/a6e0ddfc9883b7c5ae7423967a91a66b.png)

单一仓库、多环境的Dataform架构流程

## 环境设置

我们首先设置`prod`和`staging`两个环境，如上面的架构流程图所示。需要注意的是，代码开发是在macOS系统上进行的，因此，Windows系统的用户可能需要做一些调整才能跟随操作。

```py
mkdir prod
mkdir staging
```

## **设置临时文件**

所有初始代码都写在`staging`目录中。这是因为所提议的架构在临时环境中配置了Dataform，而在生产环境中仅配置了少量资源。

让我们开始配置一个远程存储桶，用于在远程后端存储Terraform状态。这一步将手动完成，我们不会将存储桶纳入Terraform管理。这有点像“先有鸡还是先有蛋”的问题，即存储Terraform状态的存储桶是否应由同一Terraform管理。可以称其为一种进退两难的局面。因此，我们将在暂存环境中手动创建一个名为*dataform-staging-terraform-state*的存储桶，并在`staging`目录中添加以下内容：

```py
#staging/backend.tf
terraform {
  backend "gcs" {
    bucket = "dataform-staging-terraform-state"
    prefix = "terraform/state"
  }
```

接下来，向代码库中添加资源提供者。

```py
#staging/providers.tf
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = ">=5.14.0"
    }
    google-beta = {
      source  = "hashicorp/google-beta"
      version = ">=5.14.0"
    }
  }

  required_version = ">= 1.7.3"
}

provider "google" {
  project = var.project_id
}
```

然后我们创建一个变量文件，定义所有用于基础设施配置的变量。

```py
#staging/variables.tf
variable "project_id" {
  type        = string
  description = "Name of the GCP Project."
}

variable "region" {
  type        = string
  description = "The google cloud region to use"
  default     = "europe-west2"
}

variable "project_number" {
  type        = string
  description = "Number of the GCP Project."
}

variable "location" {
  type        = string
  description = "The google cloud location in which to create resources"
  default     = "EU"
}

variable "dataform_staging_service_account" {
  type        = string
  description = "Email of the service account Dataform uses to execute queries in staging env"
}

variable "dataform_prod_service_account" {
  type        = string
  description = "Email of the service account Dataform uses to execute queries in production"
}

variable "dataform_github_token" {
  description = "Dataform GitHub Token"
  type        = string
  sensitive   = true
}
```

`auto.tfvars`文件被添加，以确保变量能够自动发现。确保适当替换文件中的变量占位符。

```py
#staging/staging.auto.tfvars
project_id                       = "{staging-project-id}"
region                           = "{staging-project--region}"
project_number                   = "{staging-project-number}"
dataform_staging_service_account = "dataform-staging"
dataform_prod_service_account    = "{dataform-prod-service-account-email}"
dataform_github_token            = "dataform_github_token"
```

接下来是密钥配置阶段，其中存储了机器用户的令牌。

```py
#staging/secrets.tf
resource "google_secret_manager_secret" "dataform_github_token" {
  project = var.project_id
  secret_id = var.dataform_github_token
  replication {
    user_managed {
      replicas {
        location = var.region
      }
    }
  }
}
```

配置完密钥后，`data`资源将被添加到terraform代码库中，用于动态读取存储的密钥值，以便在配置完成后，Dataform能够访问机器用户的GitHub凭证。`data`资源依赖于密钥资源，确保只有在密钥已经配置好之后才会执行。

```py
#staging/data.tf
data "google_secret_manager_secret_version" "dataform_github_token" {
  project = var.project_id
  secret  = var.dataform_github_token

  depends_on = [
    google_secret_manager_secret.dataform_github_token
  ]
}
```

我们继续配置暂存环境所需的服务账户，并授予所需的权限以将数据展现到BigQuery。

```py
#staging/service_accounts.tf
resource "google_service_account" "dataform_staging" {
  account_id   = var.dataform_staging_service_account
  display_name = "Dataform Service Account"
  project      = var.project_id
}
```

还有BQ权限。

```py
#staging/iam.tf
resource "google_project_iam_member" "dataform_staging_roles" {
  for_each = toset([
    "roles/bigquery.dataEditor",
    "roles/bigquery.dataViewer",
    "roles/bigquery.user",
    "roles/bigquery.dataOwner",
  ])

  project = var.project_id
  role    = each.value
  member  = "serviceAccount:${google_service_account.dataform_staging.email}"

  depends_on = [
    google_service_account.dataform_staging
  ]
}
```

现在是关键时刻，因为我们已经具备了在暂存环境中配置Dataform所需的所有基础设施。

```py
#staging/dataform.tf
resource "google_dataform_repository" "dataform_demo" {
  provider        = google-beta
  name            = "dataform_demo"
  project         = var.project_id
  region          = var.region
  service_account = "${var.dataform_staging_service_account}@${var.project_id}.iam.gserviceaccount.com"

  git_remote_settings {
    url                                 = "https://github.com/kbakande/terraforming-dataform"
    default_branch                      = "main"
    authentication_token_secret_version = data.google_secret_manager_secret_version.dataform_github_token.id
  }

  workspace_compilation_overrides {
    default_database = var.project_id
  }

}

resource "google_dataform_repository_release_config" "prod_release" {
  provider   = google-beta
  project    = var.project_id
  region     = var.region
  repository = google_dataform_repository.dataform_demo.name

  name          = "prod"
  git_commitish = "main"
  cron_schedule = "30 6 * * *"

  code_compilation_config {
    default_database = var.project_id
    default_location = var.location
    default_schema   = "dataform"
    assertion_schema = "dataform_assertions"
  }

  depends_on = [
    google_dataform_repository.dataform_demo
  ]
}

resource "google_dataform_repository_workflow_config" "prod_schedule" {
  provider = google-beta
  project  = var.project_id
  region   = var.region

  name           = "prod_daily_schedule"
  repository     = google_dataform_repository.dataform_demo.name
  release_config = google_dataform_repository_release_config.prod_release.id
  cron_schedule  = "45 6 * * *"

  invocation_config {
    included_tags                            = []
    transitive_dependencies_included         = false
    transitive_dependents_included           = false
    fully_refresh_incremental_tables_enabled = false

    service_account = var.dataform_prod_service_account
  }

  depends_on = [
    google_dataform_repository.dataform_demo
  ]
}
```

*google_dataform_repository*资源配置一个Dataform仓库，指定目标远程仓库以及访问该仓库的令牌。接着我们配置发布设置，说明从哪个远程仓库分支生成编译，并配置定时任务的时间。

最后，工作流配置也将配置好，安排稍微滞后于发布配置，以确保在工作流配置运行时，最新的编译版本已经可用。

一旦Dataform配置完成，一个默认的服务账户将与其一同创建，格式为*service-{project_number}@gcp-sa-dataform.iam.gserviceaccount.com*。这个默认服务账户需要模拟暂存和生产环境中的服务账户，以便在这些环境中实现数据。

我们在暂存环境中修改`iam.tf`文件，以授予Dataform默认服务账户所需的角色，使其能够模拟暂存环境中的服务账户并访问已配置的密钥。

```py
#staging/iam.tf
resource "google_project_iam_member" "dataform_staging_roles" {
  for_each = toset([
    "roles/bigquery.dataEditor",
    "roles/bigquery.dataViewer",
    "roles/bigquery.user",
    "roles/bigquery.dataOwner",
  ])

  project = var.project_id
  role    = each.value
  member  = "serviceAccount:${google_service_account.dataform_staging.email}"

  depends_on = [
    google_service_account.dataform_staging
  ]
}

resource "google_service_account_iam_binding" "custom_service_account_token_creator" {
  service_account_id = "projects/${var.project_id}/serviceAccounts/${var.dataform_staging_service_account}@${var.project_id}.iam.gserviceaccount.com"

  role = "roles/iam.serviceAccountTokenCreator"

  members = [
    "serviceAccount:@gcp-sa-dataform.iam.gserviceaccount.com">service-${var.project_number}@gcp-sa-dataform.iam.gserviceaccount.com"
  ]
  depends_on = [
    module.service-accounts
  ]
}

resource "google_secret_manager_secret_iam_binding" "github_secret_accessor" {
  secret_id = google_secret_manager_secret.dataform_github_token.secret_id

  role = "roles/secretmanager.secretAccessor"

  members = [
    "serviceAccount:@gcp-sa-dataform.iam.gserviceaccount.com">service-${var.project_number}@gcp-sa-dataform.iam.gserviceaccount.com"
  ]

  depends_on = [
    google_secret_manager_secret.dataform_github_token,
    module.service-accounts,
  ]
}
```

基于**最小权限访问控制**原则，针对目标资源的IAM绑定用于授予默认服务账户精细化访问权限。

为了不让这篇文章过长，生产环境中资源配置的Terraform代码可以在[GitHub仓库](https://github.com/kbakande/dataform-001)中找到。我们只需要在生产环境中配置远程后端存储桶和服务账户（以及默认服务账户的细粒度权限）。如果配置成功，暂存环境中的Dataform状态应类似于下面的图片。

![](../Images/4ee94aeb09f5d4904218684464d849ec.png)

GCP中成功配置后的Dataform状态

以下是所提架构的一些优缺点：

## 优点

+   遵循版本控制的原则。所提架构只有一个版本，但代码可以在多个环境中实现。

+   实验仅限于暂存环境，这可以减少对生产数据的意外修改的可能性。

## 缺点

+   担心默认服务账户可能会在生产环境中做出意外更改，但通过最小权限访问控制可以缓解这个问题。

+   多个开发人员在暂存环境中并行工作时，可能会覆盖数据。尽管在本文中没有显示，但这个问题可以通过Dataform的[工作区编译覆盖](https://cloud.google.com/dataform/docs/workspace-compilation-overrides)和[模式后缀功能](https://cloud.google.com/dataform/docs/workspace-compilation-overrides#:~:text=When%20you%20set%20%24%7BworkspaceName%7D%20as%20the%20schema%20suffix%2C%20Dataform,workspace%20in%20the%20dedicated%20schema.)来缓解。

像任何架构一样，都会有优缺点。最终的决策应基于组织内部的具体情况。希望这篇文章能够帮助做出这个决策。

# 总结

在[第1部分](https://medium.com/towards-data-science/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb)中，我们介绍了在GCP Dataform中使用的一些术语，并演示了单个仓库、多环境Dataform设置的身份验证流程。本部分2提供了Terraform代码，并介绍了如何为服务账户实施最小权限访问控制的方法。

希望这篇文章能帮助你更好地理解Dataform。我们可以在[LinkedIn](https://www.linkedin.com/in/koakande/)上联系。

*图片来源*：本文中的所有图片均由作者创建

**参考文献**

1.  [https://medium.com/towards-data-science/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb](https://medium.com/towards-data-science/understanding-dataform-terminologies-and-authentication-flow-aa98c2fbcdfb)

1.  [https://github.com/kbakande/terraforming-dataform](https://github.com/kbakande/terraforming-dataform)

1.  [https://www.cloudskillsboost.google/course_templates/443](https://www.cloudskillsboost.google/course_templates/443)

1.  [https://cloud.google.com/dataform/docs/workspace-compilation-overrides](https://cloud.google.com/dataform/docs/workspace-compilation-overrides)
