# 最大化稀缺AI资源的利用：一种Kubernetes方法

> 原文：[https://towardsdatascience.com/maximizing-the-utility-of-scarce-ai-resources-a-kubernetes-approach-0230ba53965b?source=collection_archive---------12-----------------------#2024-02-13](https://towardsdatascience.com/maximizing-the-utility-of-scarce-ai-resources-a-kubernetes-approach-0230ba53965b?source=collection_archive---------12-----------------------#2024-02-13)

## 优化有限AI训练加速器的使用

[](https://chaimrand.medium.com/?source=post_page---byline--0230ba53965b--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--0230ba53965b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0230ba53965b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0230ba53965b--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--0230ba53965b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0230ba53965b--------------------------------) ·13分钟阅读·2024年2月13日

--

![](../Images/cffd5cd8990a848557b12e0d977c3003.png)

照片由[Roman Derrick Okello](https://unsplash.com/@okello_1?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在AI开发不断变化的环境中，没有什么比古老的格言（归功于赫拉克利特）更能体现现实：“生活中唯一不变的就是变化”。对于AI而言，似乎*变化*确实是常态，而*变化的速度*却在不断加快。在这个独特而激动人心的时代，要保持相关性就意味着AI团队必须前所未有地考验其不断适应和调整开发流程的能力。那些未能适应或适应迟缓的AI开发团队，很可能会迅速变得过时。 

近年来，AI开发面临的最具挑战性的进展之一是获取训练AI模型所需硬件的难度不断增加。无论是由于[全球供应链持续危机](https://en.wikipedia.org/wiki/2021%E2%80%932023_global_supply_chain_crisis)，还是AI芯片需求的显著增加，获得所需的GPU（或替代训练加速器）变得更加困难。新GPU订单的巨大[等待时间](https://www.nextplatform.com/2023/11/17/what-to-do-when-you-cant-get-nvidia-h100-gpus/)以及曾经提供几乎无限GPU计算能力的云服务提供商（CSP）现在也难以满足需求，均证明了这一点。

时代的变化迫使那些曾依赖无限容量AI加速器的AI开发团队，适应一个减少可访问性、在某些情况下成本更高的世界。曾经理所当然的能够随意启动新的GPU机器的开发过程，现在必须做出调整，以应对一个AI资源稀缺、且通常由多个项目和/或团队共享的世界。那些未能适应的人，将面临被淘汰的风险。

在这篇文章中，我们将展示如何在AI资源稀缺的世界中，使用[Kubernetes](https://kubernetes.io/)来协调AI模型训练工作负载。我们将从指定我们希望实现的目标开始，然后描述为什么Kubernetes是解决这个挑战的合适工具。最后，我们将提供一个简单的演示，展示如何使用Kubernetes最大化稀缺的AI计算资源的利用。在随后的文章中，我们计划进一步完善基于Kubernetes的解决方案，并展示如何将其应用于基于云的训练环境。

## 免责声明

虽然本文并不假设读者具有Kubernetes的先前经验，但一些基本的了解肯定会有所帮助。本文不应被视为Kubernetes教程。要学习Kubernetes，我们建议读者参考许多关于此主题的优秀在线资源。这里，我们仅讨论Kubernetes在最大化和优先考虑资源利用方面的一些特性。

对于我们在此提出的方法，有许多替代工具和技术，每种工具和技术都有其优缺点。我们在本文中的意图纯粹是为了教育目的；请不要将我们所做的任何选择视为推荐。

最后，Kubernetes平台以及AI开发领域中的许多框架和工具仍在不断发展。请注意，在您阅读本文时，某些声明、示例和/或外部链接可能已经过时，因此在做出自己的设计决策之前，请务必参考最新的解决方案。

# 适应稀缺的AI计算资源

为了简化我们的讨论，假设我们只有一个单一的工作节点用于训练模型。这个节点可以是配备GPU的本地机器，或者是云端的[预留实例](https://aws.amazon.com/ec2/pricing/reserved-instances/)，例如AWS中的[p5.48xlarge](https://aws.amazon.com/ec2/instance-types/p5/)实例，或者GCP中的[TPU节点](https://cloud.google.com/tpu)。在下面的示例中，我们将称这个节点为“我的宝贝”。通常，我们会为这台机器花费大量资金。我们还假设，我们有多个训练任务在争夺这个唯一的计算资源，每个任务可能需要从几分钟到几天的时间。自然，我们希望最大化计算资源的利用率，确保它始终处于工作状态，并优先处理最重要的任务。我们需要的是某种形式的[优先级队列](https://en.wikipedia.org/wiki/Priority_queue)以及相关的基于优先级的[调度](https://en.wikipedia.org/wiki/Scheduling_(computing))算法。让我们更具体地讨论一下我们希望达到的行为。

## 调度需求

1.  **最大化利用率：** 我们希望我们的资源能够持续使用。具体来说，当一个任务完成后，资源会立即（并自动）开始处理下一个任务。

1.  **队列中的待处理任务：** 我们需要存在一个*队列*，用于存放等待我们唯一资源处理的训练任务。我们还需要相关的API来创建和提交新的任务到队列，以及监控和管理队列的状态。

1.  **支持优先级：** 我们希望每个训练任务都具有关联的优先级，使得优先级高的任务会在优先级低的任务之前运行。

1.  **抢占：** 此外，如果在资源正在处理低优先级任务时，队列中有一个紧急任务提交，我们希望能够将当前正在运行的任务抢占，并由紧急任务替代。被抢占的任务应该返回队列中。

一种满足这些需求的解决方案可能是，使用现有的提交任务到训练资源的API，并用一个定制的优先级队列实现来包装它，确保具备所需的属性。最基本的，这种方法需要一个数据结构来存储待处理任务的列表，一个专门的进程来从队列中选择并提交任务到训练资源，以及某种机制来识别任务何时完成并且资源变得可用。

一种替代的方法，也是我们在本文中采用的方法，是利用现有的基于优先级的 [调度](https://en.wikipedia.org/wiki/Scheduling_(computing)) 解决方案，该解决方案能够满足我们的需求，并将我们的训练开发工作流程与其使用对齐。Kubernetes 默认的 [调度器](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/) 就是这样一个解决方案的例子。在接下来的部分，我们将演示如何使用它来解决优化稀缺 AI 训练资源使用的问题。

# 使用 Kubernetes 进行 ML 编排

在这一部分，我们将对 Kubernetes 在 ML 训练工作负载编排中的应用进行一些哲学性的探讨。如果你对这些讨论没有耐心（完全可以理解），并且希望直接查看实际示例，请随时跳过到下一部分。

Kubernetes 是（另一个）那种在许多开发者中引发强烈反应的软件/技术解决方案。有人对它推崇备至，并广泛使用；也有人认为它压迫性强、笨重且不必要（例如，参见 [这里](https://cloudnativejourney.wordpress.com/2023/04/19/kubernetes-advantages-and-disadvantages/) 一些支持和反对使用 Kubernetes 的论点）。正如许多其他激烈争论一样，作者认为真相介于两者之间——在一些情况下，Kubernetes 提供了一个理想的框架，能够显著提高生产力，而在其他情况下，它的使用几乎是对软件开发职业的侮辱。一个大问题是，ML 开发处于这个光谱的哪个位置？Kubernetes 是否是训练 ML 模型的合适框架？尽管快速的在线搜索可能给人一种普遍共识是明确的“是”，但我们将提出一些理由，说明为什么情况可能并非如此。但首先，我们需要澄清一下“使用 Kubernetes 进行 ML 训练编排”到底是什么意思。

虽然有许多在线资源讨论使用Kubernetes进行机器学习（ML）的话题，但需要注意的是，它们并不总是指同一种使用方式。一些资源（例如，[这里](/distributed-deep-learning-training-with-horovod-on-kubernetes-6b28ac1d6b5d)）仅使用Kubernetes来部署集群；一旦集群启动并运行，它们便在Kubernetes之外启动训练任务。另一些资源（例如，[这里](https://aws.amazon.com/blogs/machine-learning/introducing-amazon-sagemaker-operators-for-kubernetes/)）使用Kubernetes定义一个流水线，其中一个专用模块启动一个训练任务（及其相关资源），该模块使用完全不同的系统。与这两个例子不同，许多其他资源将训练工作负载定义为[Kubernetes Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/)工件，该工件在[Kubernetes Node](https://kubernetes.io/docs/concepts/architecture/nodes/)上运行。然而，它们在专注的具体属性上也存在很大差异。一些资源（例如，[这里](https://medium.com/pytorch/a-step-by-step-guide-to-building-a-distributed-spot-based-training-platform-on-aws-using-b54acd06ecb2)）强调自动扩展特性，而其他资源（例如，[这里](https://blog.realvarez.com/get-more-out-of-gpus-with-nvidia-multi-instance-gpu/)）则强调多实例GPU（[MIG](https://www.nvidia.com/en-eu/technologies/multi-instance-gpu/)）支持。它们在实现的细节上也差异很大，例如表示训练任务的精确工件（例如，[Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/)扩展），例如[ElasticJob](https://github.com/pytorch/elastic/tree/master/kubernetes)，[TrainingWorkload](https://docs.run.ai/v2.15/admin/workloads/workload-overview-admin/)，[JobSet](https://github.com/kubernetes-sigs/jobset)，[VolcanoJob](https://volcano.sh/en/docs/vcjob/)等。在本文的语境下，我们也将假设训练工作负载被定义为[Kubernetes Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/)。然而，为了简化讨论，我们将专注于核心Kubernetes对象，关于[针对ML的Kubernetes扩展](https://kubernetes.io/docs/concepts/extend-kubernetes/)的讨论将留待以后文章中详细展开。

## 反对Kubernetes用于机器学习的论点

下面是一些可能反对使用Kubernetes进行机器学习模型训练的论点。

1.  **复杂性：** 即使是最强烈的支持者也不得不承认 Kubernetes 可能很难使用。有效使用 Kubernetes 需要较高的专业知识，学习曲线陡峭，且从现实角度来看，通常需要专门的 DevOps 团队。基于 Kubernetes 设计训练解决方案增加了对专门专家的依赖，并进一步增加了出现问题的风险，且可能导致开发延迟。许多替代的机器学习训练解决方案使开发者拥有更高的独立性和自由度，并降低了开发过程中出现漏洞的风险。

1.  **固定资源需求：** Kubernetes 最为人称道的特性之一是其[可扩展性](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)——它能够根据作业数量、客户端数量（对于服务应用程序的情况）、资源容量等，自动、无缝地扩展和缩减其计算资源池。然而，可以说，在机器学习训练工作负载的情况下，由于所需资源的数量（通常）在训练过程中是固定的，自动扩展显得不必要。

1.  **固定实例类型：** 由于 Kubernetes 管理*容器化*应用程序，Kubernetes 在其节点池中对于机器类型提供了很大的灵活性。然而，在机器学习中，我们通常需要非常特定的硬件，配备专用加速器（如 GPU）。此外，我们的工作负载通常会调整为在某一特定实例类型上运行得最优。

1.  **单体应用架构：** 在现代应用开发中，常见的做法是将应用分解为称为[微服务](https://en.wikipedia.org/wiki/Microservices)的小元素。Kubernetes 通常被视为这种设计中的关键组件。机器学习训练应用通常在设计上是相当单体化的，可以说，它们并不自然地适合微服务架构。

1.  **资源开销：** 运行 Kubernetes 所需的专用进程需要在其池中的每个节点上占用一些系统资源。因此，这可能会对我们的训练任务产生一定的性能损失。考虑到训练所需资源的成本，我们可能更倾向于避免这种情况。

诚然，我们在 Kubernetes 与 ML 争论中采取了非常片面的观点。仅根据上述论点，你可能会得出结论，认为我们需要一个非常充分的理由才能选择 Kubernetes 作为 ML 训练的框架。我们认为，本文提出的挑战，即最大化稀缺的 AI 计算资源的效用，正是支持使用 Kubernetes 的理由，尽管上述论点提出了反对意见。正如我们将展示的那样，内置的 [调度器](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/) 和对 [优先级和抢占](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/) 的支持，使其成为满足上述需求的有力候选者。

# 示例

在本节中，我们将分享一个简短的示例，展示 Kubernetes 内建的优先级调度支持。为了演示的目的，我们将使用 [Minikube](https://minikube.sigs.k8s.io/docs/)（版本 v1.32.0）。Minikube 是一个工具，可以让你在本地环境中运行 Kubernetes 集群，非常适合用来实验 Kubernetes。请参阅官方文档了解 [Minikube 的安装与入门](https://minikube.sigs.k8s.io/docs/start/)。

## 集群创建

让我们首先使用 [Minikube start](https://minikube.sigs.k8s.io/docs/commands/start/) 命令创建一个包含两个节点的集群：

```py
minikube start --nodes 2
```

结果是一个本地 Kubernetes 集群，包含一个名为 *minikube* 的主节点（“控制平面”）和一个名为 *minikube-m02* 的工作节点，它将模拟我们的单一 AI 资源。让我们应用 [标签](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) *my-precious* 来标识它为一个独特的资源类型：

```py
kubectl label nodes minikube-m02 node-type=my-precious
```

我们可以使用 [Minikube 仪表板](https://minikube.sigs.k8s.io/docs/handbook/dashboard/) 来可视化结果。在另一个 shell 中运行下面的命令，并打开生成的浏览器链接。

```py
minikube dashboard
```

如果你点击左侧面板上的 *Nodes* 标签，你应该能够看到集群节点的总结：

![](../Images/77af57927c95d3cd62defaac704ecf15.png)

Minikube 仪表板中的节点列表（由作者捕获）

## PriorityClass 定义

接下来，我们定义两个 [PriorityClasses](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass)，*低优先级* 和 *高优先级*，如下面显示的 *priorities.yaml* 文件所示。新作业默认将分配为 *低优先级*。

```py
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 0
globalDefault: true

---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
```

为了将我们的新类应用到集群中，我们运行：

```py
kubectl apply -f priorities.yaml
```

## 创建作业

我们使用下面的代码块定义一个简单的作业，使用*job.yaml*文件。为了演示的目的，我们定义了一个[Kubernetes作业](https://kubernetes.io/docs/concepts/workloads/controllers/job/)，该作业只是简单地睡眠100秒。我们使用[busybox](https://hub.docker.com/_/busybox)作为它的Docker镜像。在实际应用中，这将被替换为一个训练脚本和合适的机器学习Docker镜像。我们通过[nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector)字段指定作业在我们特殊的实例*my-precious*上运行，并指定资源需求，确保一次只能在该实例上运行一个作业。作业的优先级默认为上面定义的*低优先级*。

```py
apiVersion: batch/v1
kind: Job
metadata:
  name: test
spec:
  template:
    spec:
      containers:
        - name: test
          image: busybox
          command: # simple sleep command
            - sleep
            - '100'
          resources: # require all available resources
            limits:
              cpu: "2"
            requests:
              cpu: "2"
      nodeSelector: # specify our unique resource
          node-type: my-precious
      restartPolicy: Never
```

我们使用以下命令提交作业：

```py
kubectl apply -f job.yaml
```

## 创建作业队列

为了展示Kubernetes如何排队处理作业，我们创建了三个与上面定义的作业相同的副本，分别命名为*test1*、*test2*和*test3*。我们将这三个作业组合成一个文件*jobs.yaml*，并提交它们进行处理：

```py
kubectl apply -f jobs.yaml
```

下图显示的是我们集群的*工作负载状态*，该状态是在提交作业后不久，通过[Minikube仪表盘](https://minikube.sigs.k8s.io/docs/handbook/dashboard/)捕捉到的。你可以看到，*my-precious*已经开始处理*test1*，而其他作业则处于*待处理*状态，等待轮到它们。

![](../Images/6efd9f7f525b7319dd48870d0fc7c7af.png)

集群工作负载状态（由作者捕获）

一旦*test1*完成，*test2*的处理将开始：

![](../Images/6fd10d8f89a5fef5073ae2411edeb91c.png)

集群工作负载状态 — 自动调度（由作者捕获）

只要没有其他更高优先级的作业提交，我们的作业将继续按顺序处理，直到所有作业完成。

## 作业抢占

我们通过展示提交第四个作业（这次设置为*高优先级*）时发生的情况，来演示Kubernetes内置对[作业抢占](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/)的支持：

```py
apiVersion: batch/v1
kind: Job
metadata:
  name: test-p1
spec:
  template:
    spec:
      containers:
        - name: test-p1
          image: busybox
          command:
            - sleep
            - '100'
          resources:
            limits:
              cpu: "2"
            requests:
              cpu: "2"
      restartPolicy: Never
      priorityClassName: high-priority # high priority job
      nodeSelector:
          node-type: my-precious
```

*工作负载状态*的影响如下面的图所示：

![](../Images/62ebad9df10e25effd21dbea9d2865ac.png)

集群工作负载状态 — 抢占（由作者捕获）

*test2*作业已被*抢占*——它的处理被停止，并且返回到*待处理*状态。取而代之的是，*my-precious*开始处理优先级更高的*test-p1*作业。只有当*test-p1*完成后，低优先级作业的处理才会恢复。（如果被抢占的作业是一个机器学习训练工作负载，我们会编程让它从最近保存的模型[模型检查点](https://pytorch.org/tutorials/recipes/recipes/saving_and_loading_a_general_checkpoint.html)继续恢复。）

下图显示了所有作业完成后的*工作负载状态*。

![](../Images/f242a5d5bfa89d01de0134dba77273c3.png)

集群工作负载状态 — 完成（由作者捕获）

# Kubernetes 扩展

我们展示的基于优先级调度和抢占的解决方案仅依赖于Kubernetes的核心组件。在实际应用中，你可以选择利用如[Kueue](https://github.com/kubernetes-sigs/kueue)等扩展引入的基本功能增强，和/或在Kubernetes之上构建的平台提供的专用ML特性，例如[Run:AI](https://www.run.ai/)或[Volcano](https://volcano.sh/en/)。但请记住，为了满足最大化稀缺AI计算资源效用的基本要求，我们所需要的只是Kubernetes的核心功能。

# 总结

专用AI硅芯片的可用性减少迫使ML团队调整他们的开发流程。与过去不同，开发人员可以随意创建新的AI资源，现在他们面临AI计算能力的限制。这就需要通过购买专用单元和/或预定云实例等方式来采购AI实例。此外，开发人员还必须接受与其他用户和项目共享这些资源的可能性。为了确保稀缺的AI计算能力得到最大效用的分配，必须定义专用调度算法，减少空闲时间并优先处理关键工作负载。在本文中，我们展示了如何利用[Kubernetes调度器](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/)实现这些目标。如上所述，这只是解决最大化稀缺AI资源效用的众多方法之一。自然，你选择的方案以及实现的细节将取决于你AI开发的具体需求。
