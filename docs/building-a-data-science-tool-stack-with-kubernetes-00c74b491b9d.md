# 使用 Kubernetes 构建数据科学平台

> 原文：[`towardsdatascience.com/building-a-data-science-tool-stack-with-kubernetes-00c74b491b9d?source=collection_archive---------7-----------------------#2024-07-11`](https://towardsdatascience.com/building-a-data-science-tool-stack-with-kubernetes-00c74b491b9d?source=collection_archive---------7-----------------------#2024-07-11)

## Kubernetes 如何作为后端工具，支持数据科学团队实现从模型开发到部署的端到端机器学习生命周期

[](https://avinashknmr.medium.com/?source=post_page---byline--00c74b491b9d--------------------------------)![Avinash Kanumuru](https://avinashknmr.medium.com/?source=post_page---byline--00c74b491b9d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--00c74b491b9d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00c74b491b9d--------------------------------) [Avinash Kanumuru](https://avinashknmr.medium.com/?source=post_page---byline--00c74b491b9d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--00c74b491b9d--------------------------------) ·阅读时长 6 分钟·2024 年 7 月 11 日

--

![](img/6e99dc373ba82e9468ce30f2575fb1af.png)

图片来源：[Growtika](https://unsplash.com/@growtika?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当我开始担任数据科学经理的新职位时，我对为团队搭建数据科学平台几乎一无所知。在我之前的所有职位中，我主要工作是构建模型，并在一定程度上进行模型部署（或者至少是支持进行模型部署的团队），但我从未需要从零开始搭建什么（我指的是基础设施）。那时，数据科学团队还不存在。

所以，我的第一个目标是搭建一个平台，不仅仅是为数据科学团队单独使用，而是能够与数据工程和软件团队集成。这就是我第一次直接接触 Kubernetes（k8s）的时候。我之前听说过它，但没有超出创建 docker 镜像的范围，其他人会在某些基础设施上进行部署。

那么，为什么数据科学团队需要 Kubernetes 呢？数据科学团队面临哪些挑战？

+   根据需求可扩展的计算——作为数据科学家，我们每天处理不同的问题，每个问题的资源需求各不相同。没有一种通用的计算机。即使有，也不能提供给数据科学团队的每个成员。

+   版本问题——在团队中工作时，或者在我们部署到生产环境时，Python 和包的版本问题

+   不同的技术和平台 — 有些预处理和模型构建需要使用 Spark，而有些可以通过 Pandas 完成。所以再次强调，本地计算机上并没有适用于所有情况的“一刀切”方案。

+   团队内部的工作共享 — 在 Excel 表格中共享和跟踪模型结果，并在每次迭代后进行分发

+   最重要的是，生产部署 —— 我如何将完成的模型部署到生产环境中？模型往往无法用于实时场景，因为我们作为数据科学家不懂得围绕模型构建 API/系统。最终，我们往往只能在批处理模式中运行模型评分。

我探讨过包括云平台解决方案（AWS SageMaker、GCP AI Platform、Azure Machine Learning）在内的方案，但我们的主要考虑因素是成本，其次是云无关性。如果成本不是问题，那么可以使用上述提到的云平台服务。

我们发现 Kubernetes 是一个理想的平台，能够满足大多数这些需求 —— 它能扩展并服务容器化的镜像。通过这种方式，我们也保持了云无关性。如果必须迁移到其他供应商，只需最小的修改，便能轻松迁移一切。

许多工具提供了完整/类似的解决方案，比如 KubeFlow、Weights & Biases、Kedro 等，但我最终部署了以下三项服务作为数据科学平台的第一版。尽管这些工具没有提供完整的 MLOps 框架，但它们为我们提供了构建数据科学平台和团队的起点。

1.  **JupyterHub** — 为开发模型提供容器化的交互式 Jupyter Notebook 用户环境

1.  **MLflow** — 实验跟踪和存储模型工件

1.  **Seldon Core** — 简化的 Kubernetes 模型部署方式

通过这三个服务，我可以让我的团队在 JupyterHub 中构建模型，包括大数据处理，跟踪不同的调优参数和指标，并使用 MLflow 存储工件，通过 Seldon-Core 提供生产环境中的模型。

## JupyterHub

部署是最棘手的部分。相比于 Kubernetes 安装，单独的 JupyterHub 安装要简单一些。但这里有许多必需的配置 —

[](https://z2jh.jupyter.org/?source=post_page-----00c74b491b9d--------------------------------) [## 使用 Kubernetes 从零开始搭建 JupyterHub

### JupyterHub 允许用户通过网页与计算环境进行交互。由于大多数设备都能访问...

z2jh.jupyter.org](https://z2jh.jupyter.org/?source=post_page-----00c74b491b9d--------------------------------)

由于我们希望使用 Spark 进行一些数据处理，我们创建了 2 个 Docker 镜像 —

1.  基本笔记本 — 扩展自`jupyter/minimal-notebook:python-3.9`

1.  Spark 笔记本 — 在上述基础上扩展，增加了 Spark 配置。

这些笔记本 Docker 镜像的代码和使用这些 Docker 镜像安装 JupyterHub 的 Helm 配置可以在[这里](https://github.com/avinashknmr/data-science-tools)找到。

[](https://github.com/avinashknmr/data-science-tools?source=post_page-----00c74b491b9d--------------------------------) [## GitHub - avinashknmr/data-science-tools  

### 通过在 GitHub 上创建账户，参与开发 avinashknmr/data-science-tools 项目。  

[github.com](https://github.com/avinashknmr/data-science-tools?source=post_page-----00c74b491b9d--------------------------------)  

为了启用 Google Oauth，我做了很多调整，包括将 Notebook 作为 root 用户启动，但以个别用户身份运行它们，检索用户名、用户级别的权限、持久卷声明和服务账户等，这些都花费了我几天时间才能让它工作，尤其是身份验证部分。但代码库中的这段代码，可以为你提供一个框架，帮助你开始使用。  

## MLflow  

设置 MLFlow 很简单。  

[](https://mlflow.org/docs/latest/introduction/index.html?source=post_page-----00c74b491b9d--------------------------------) [## 什么是 MLflow？  

### 进入机器学习（ML）领域是一次令人兴奋的旅程，但它常常伴随着一些复杂性，这可能…  

[mlflow.org](https://mlflow.org/docs/latest/introduction/index.html?source=post_page-----00c74b491b9d--------------------------------)  

MLflow 提供模型追踪、模型注册和模型服务能力。但对于模型服务，我们使用下一个工具（Seldon-Core）。  

构建一个包含所需 Python 包的 Docker 镜像。  

```py
FROM python:3.11-slim

RUN pip install mlflow==2.0.1 boto3==1.26.12 awscli==1.27.22 psycopg2-binary==2.9.5

EXPOSE 5000
```

一旦创建并推送 Docker 镜像到你选择的容器注册中心，我们会为 Kubernetes 创建一个部署和服务文件（类似于任何其他 Docker 镜像部署）。下面给出了部署 yaml 的一个片段。  

```py
containers:
- image: avinashknmr/mlflow:2.0.1
  imagePullPolicy: IfNotPresent
  name: mlflow-server
  command: ["mlflow", "server"]
  args:
  - --host=0.0.0.0
  - --port=5000
  - --artifacts-destination=$(MLFLOW_ARTIFACTS_LOCATION)
  - --backend-store-uri=postgresql+psycopg2://$(MLFLOW_DB_USER):$(MLFLOW_DB_PWD)@$(MLFLOW_DB_HOST):$(MLFLOW_DB_PORT)/$(MLFLOW_DB_NAME)
  - --workers=2
```

这里有两个主要的配置，花了我一些时间才理解和配置完成——

1.  artifact 的位置  

1.  后端存储  

artifact 的位置将是一个 Blob 存储，你的模型文件将存储在此处，并可用于模型服务。然而，在我们的案例中，这个位置是 AWS S3，所有的模型都存储在这里，并且它对我们来说是一个模型注册中心。还有其他几种选择可以在服务器本地存储模型，但每当 Pod 重启时，数据会丢失，而且 PersistentVolume 只能通过服务器访问。通过使用云存储，我们可以与其他服务进行集成——例如，Seldon-Core 可以从这个位置加载并服务模型。后端存储则保存运行应用所需的所有元数据，包括模型追踪——每次实验/运行的参数和指标。  

## Seldon-Core  

三者中第二个最棘手的是 Seldon-Core。  

Seldon-Core 就像是你模型的封装器，它可以打包、部署和监控 ML 模型。这消除了对 ML 工程师的依赖，避免了需要他们来创建部署流水线。  

[](https://github.com/SeldonIO/seldon-core?source=post_page-----00c74b491b9d--------------------------------) [## GitHub - SeldonIO/seldon-core：一个用于打包、部署、监控和管理成千上万的生产机器学习模型的 MLOps 框架…  

### 一个 MLOps 框架，用于打包、部署、监控和管理成千上万的生产机器学习模型…  

[github.com](https://github.com/SeldonIO/seldon-core?source=post_page-----00c74b491b9d--------------------------------)

我们使用了 Helm chart 和 Istio 进行安装，以实现 ingress。Ingress 有两种选择 —— Istio 和 Ambassador。我不会深入讲解 Istio 的设置，因为这是由 DevOps 团队完成的。Seldon 是通过以下 Helm 和 Kubectl 命令安装的。

```py
kubectl create namespace seldon-system
kubectl label namespace seldon-system istio-injection=enabled

helm repo add seldonio https://storage.googleapis.com/seldon-charts
helm repo update

helm install seldon-core seldon-core-operator \
    --repo https://storage.googleapis.com/seldon-charts \
    --set usageMetrics.enabled=true \
    --set istio.enabled=true \
    --set istio.gateway=seldon-system/seldon-gateway \
    --namespace seldon-system
```

假设你已经设置好了 Istio，下面是设置我们 Seldon 的 Gateway 和 VirtualService 的 Yaml 配置。

```py
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: seldon-gateway
  namespace: seldon-system
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: seldon-vs
  namespace: seldon-system
spec:
  hosts:
  - "*"
  gateways:
  - seldon-gateway
  http:
  - match:
    - uri:
        prefix: /seldon
    route:
    - destination:
        host: seldon-webhook-service.seldon-system.svc.cluster.local
        port:
          number: 8000
```

下面是一个示例的 k8s 部署文件，用于从 GCS 提供 iris 模型服务。如果使用 `scikit-learn` 包进行模型开发，模型应通过 `joblib` 导出，并命名为 `model.joblib`。

```py
apiVersion: machinelearning.seldon.io/v1
kind: SeldonDeployment
metadata:
  name: iris-model
  namespace: prod-data-science
spec:
  name: iris
  predictors:
  - graph:
      implementation: SKLEARN_SERVER
      modelUri: gs://seldon-models/v1.16.0-dev/sklearn/iris
      name: classifier
    name: default
    replicas: 1
```

在这个示例中，我们使用 SKLEARN_SERVER，但它也支持 MLFLOW_SERVER 和 TF_SERVER，分别用于 MLflow 和 TensorFlow。

Seldon-Core 不仅支持 REST API，还支持 gRPC，能够实现无缝的服务器间调用。

## 结论

这些工具是开源的，可以部署在 Kubernetes 上，因此对于小团队来说具有成本效益，并且是云中立的。它们解决了数据科学团队的大部分挑战，比如集中式的 Jupyter Notebook 用于协作，避免版本问题，同时无需专门的机器学习工程师就能提供模型服务。

JupyterHub 和 Seldon-Core 都利用了 Kubernetes 的能力。JupyterHub 会在用户登录时启动一个 Pod，并在空闲时将其销毁。Seldon-Core 会将模型封装并在几分钟内作为 API 提供服务。MLflow 是唯一一个独立安装的工具，它连接了模型开发和模型部署。MLflow 作为一个模型注册库，用于追踪模型并存储后续使用的工件。
