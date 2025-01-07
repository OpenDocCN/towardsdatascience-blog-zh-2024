# 使用Metaflow、AWS和Weights & Biases优化物体检测

> 原文：[https://towardsdatascience.com/streamlining-object-detection-with-metaflow-aws-and-weights-biases-b44a14cb2e11?source=collection_archive---------1-----------------------#2024-07-19](https://towardsdatascience.com/streamlining-object-detection-with-metaflow-aws-and-weights-biases-b44a14cb2e11?source=collection_archive---------1-----------------------#2024-07-19)

## 如何为物体检测创建生产级管道

[](https://medium.com/@ed.izaguirre?source=post_page---byline--b44a14cb2e11--------------------------------)[![Ed Izaguirre](../Images/c9eded1f06c47571baa662107428483f.png)](https://medium.com/@ed.izaguirre?source=post_page---byline--b44a14cb2e11--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b44a14cb2e11--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b44a14cb2e11--------------------------------) [Ed Izaguirre](https://medium.com/@ed.izaguirre?source=post_page---byline--b44a14cb2e11--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b44a14cb2e11--------------------------------) ·14分钟阅读·2024年7月19日

--

![](../Images/c157f2b7af842d1ff59de906324c2968.png)

项目流程概述。图片来自作者。

**目录**

1.  [介绍（或标题中的内容）](#ed7d)

1.  [没有Ops的MLOps现实](#658e)

1.  [有效管理依赖关系](#5cc4)

1.  [如何调试生产流程](#04ab)

1.  [找到合适的步长](#de07)

1.  [要点总结](#1c44)

1.  [参考文献](#d14a)

**相关链接**

+   [GitHub 仓库](https://github.com/EdIzaguirre/plant-object-detection)

+   [链接到讨论该项目开发版本的早期文章](https://medium.com/towards-data-science/object-detection-using-retinanet-and-kerascv-b07940327b6c)

# 介绍（或标题中的内容）

在数据科学职位名称的世界中导航可能令人不知所措。以下是我最近在LinkedIn上看到的一些例子：

+   数据科学家

+   机器学习工程师

+   MLOps工程师

+   数据科学家/机器学习工程师

+   机器学习性能工程师

+   …

话题还可以继续深入。让我们关注两个关键角色：**数据科学家**和**机器学习工程师**。根据Chip Huyen在她的书《*Introduction to Machine Learning Interviews*》中的描述[1]：

> 数据科学的目标是**生成商业洞察**，而机器学习工程的目标是**将数据转化为产品**。这意味着数据科学家往往更擅长统计学，而机器学习工程师则通常是更优秀的工程师。机器学习工程师肯定需要了解机器学习算法，而许多数据科学家则可以在不涉及机器学习的情况下完成他们的工作。

明白了。所以数据科学家必须懂得统计学，而机器学习工程师则必须了解机器学习算法。但如果数据科学的目标是产生商业洞察，并且到2024年，最强大的算法，特别是深度学习，往往能够产生最佳洞察，那么两者之间的界限就变得模糊了。或许这也能解释我们之前看到的*数据科学家/机器学习工程师*这一职位的结合？

Huyen 接着说：

> 随着公司对机器学习的采用逐步成熟，可能希望拥有一个专门的机器学习工程团队。然而，随着越来越多的预构建和预训练模型可以即插即用，开发机器学习模型可能不再需要那么多的机器学习知识，机器学习工程与数据科学将更加统一。

这是2020年写的。到2024年，机器学习工程和数据科学之间的界限确实变得模糊了。那么，如果实现机器学习模型的能力不是分界线，那么究竟是什么呢？

这一界限因实践者而异。如今，典型的数据科学家和机器学习工程师的区别如下：

+   **数据科学家：** 使用 Jupyter notebook，从未听说过 Airflow，Kaggle 专家，管道由手动按正确顺序执行代码单元组成，擅长超参数调优，Docker？夏天穿的好鞋！专注于开发。

+   **机器学习工程师：** 编写 Python 脚本，听说过 Airflow，但不喜欢它（支持 Prefect！），Kaggle 中级选手，自动化管道，模型调优交给数据科学家，Docker 爱好者。专注于生产环境。

在大公司中，数据科学家开发机器学习模型来解决业务问题，然后交给机器学习工程师。工程师将这些模型投入生产并进行部署，确保其可扩展性和鲁棒性。简而言之：**今天数据科学家和机器学习工程师之间的根本区别，不在于谁在使用机器学习，而在于你是否专注于开发还是生产环境**。

但如果你没有一家大公司，而是处于初创公司或小型公司的情况下，预算只能雇佣一位或几位数据科学团队成员呢？他们可能希望招聘能够兼做*数据科学家/机器学习工程师*的人员！为了成为这个神话般的“[全栈数据科学家](https://podcasts.apple.com/us/podcast/dataframed/id1336150688?i=1000661883935)”，我决定将我之前的一个项目，*使用 RetinaNet 和 KerasCV 进行物体检测*，进行生产化（请参阅上述链接获取相关文章和代码）。原始项目是使用 Jupyter notebook 完成的，但存在一些不足之处：

+   以前没有模型版本控制、数据版本控制甚至代码版本控制。如果我的 Jupyter notebook 在某次运行时有效，而在随后的运行中无效，那时没有任何系统化的方法可以回到有效的脚本/模型（Ctrl + Z？Kaggle 中的保存 notebook 选项？）

+   模型评估相当简单，使用了 Matplotlib 和一些 KerasCV 图表。没有存储评估结果。

+   我们的计算资源受限于 Kaggle 免费的 20 小时 GPU。无法使用更大的计算实例，也不能并行训练多个模型。

+   该模型从未部署到任何端点，因此无法在 Jupyter notebook 以外的地方进行预测（没有业务价值）。

为了完成这个任务，我决定尝试使用 [Metaflow](https://docs.metaflow.org/introduction/why-metaflow)。Metaflow 是一个开源的机器学习平台，旨在帮助数据科学家训练和部署机器学习模型。Metaflow 主要有两个功能：

+   一个**工作流编排工具**。Metaflow 将一个工作流分解为多个步骤。将一个 Python 函数转化为 Metaflow 步骤非常简单，只需在函数上方添加 `@step` 装饰器即可。Metaflow 并不一定具备像 [Airflow](https://github.com/apache/airflow) 这样的工作流工具所提供的所有功能，但它简单、符合 Python 风格，并且可以设置使用 AWS Step Functions 作为外部编排器。此外，使用像 [Airflow 或 Prefect 与 Metaflow 配合使用](https://github.com/jacopotagliabue/you-dont-need-a-bigger-boat)也是完全没问题的。

+   一个**基础设施抽象工具**。这正是 Metaflow 的真正优势所在。通常，数据科学家需要手动设置基础设施，将模型训练任务从他们的笔记本电脑发送到 AWS。这可能需要了解基础设施方面的知识，如 API 网关、虚拟私有云（VPC）、Docker/Kubernetes、子网掩码等。听起来这更像是机器学习工程师的工作！然而，通过使用 Cloud Formation 模板（基础设施即代码文件）和 `@batch` Metaflow 装饰器，数据科学家能够以简单可靠的方式将计算任务发送到云端。

本文详细介绍了我使用 Metaflow、AWS 和 Weights & Biases 生产化物体检测模型的历程。我们将在这个过程中探讨四个关键的学习经验：

1.  “没有 Ops 的 MLOps”现实

1.  有效的依赖管理

1.  生产环境工作流的调试策略

1.  优化工作流结构

通过分享这些见解，我希望能指导你们这些数据从业者，从开发转向生产相关的工作，突出在这一过程中遇到的挑战和解决方案。

在深入具体内容之前，让我们先来看一下我们 Metaflow 管道的高层结构。这将为你提供一个鸟瞰视图，帮助你了解本文中讨论的工作流：

```py
from metaflow import FlowSpec, Parameter, step, current, batch, S3, environment

class main_flow(FlowSpec):
    @step
    def start(self):
        """
        Start-up: check everything works or fail fast!
        """

        self.next(self.augment_data_train_model)

    @batch(gpu=1, memory=8192, image='docker.io/tensorflow/tensorflow:latest-gpu', queue="job-queue-gpu-metaflow")
    @step
    def augment_data_train_model(self):
        """
        Code to pull data from S3, augment it, and train our model.
        """

        self.next(self.evaluate_model)

    @step
    def evaluate_model(self):
        """
        Code to evaluate our detection model, using Weights & Biases.
        """

        self.next(self.deploy)

    @step
    def deploy(self):
        """
        Code to deploy our detection model to a Sagemaker endpoint
        """

        self.next(self.end)

    @step
    def end(self):
        """
        The final step!
        """

        print("All done. \n\n Congratulations! Plants around the world will thank you. \n")
        return

if __name__ == '__main__':
    main_flow()
```

这个结构构成了我们生产级目标检测流水线的骨架。Metaflow 是 Python 风格的，使用装饰器将函数标记为流水线中的步骤，处理依赖关系管理，并将计算任务移到云端。步骤通过 `self.next()` 命令按顺序执行。更多关于 Metaflow 的内容，请参见 [文档](https://docs.metaflow.org/introduction/why-metaflow)。

# 没有运维的 MLOps 现实

Metaflow 的一个承诺是数据科学家应该能够专注于他们关心的事情；通常是模型开发和特征工程（想想 Kaggle），同时将他们不关心的事情（计算任务在哪儿运行，数据存储在哪儿，等等）抽象化。对此有一句话：“*没有运维的 MLOps*”。我以为这意味着我能够抽象化 MLOps 工程师的工作，而无需自己学习或做太多运维工作。我以为我可以不用了解 Docker、CloudFormation 模板、[EC2 实例类型](https://aws.amazon.com/ec2/instance-types/)、AWS 服务配额、[Sagemaker 端点](https://sagemaker.readthedocs.io/en/stable/frameworks/tensorflow/sagemaker.tensorflow.html)以及 AWS 批量配置。

不幸的是，这是天真了。我意识到许多 Metaflow 教程中链接的 [CloudFormation 模板](https://github.com/Netflix/metaflow-tools/blob/master/aws/cloudformation/metaflow-cfn-template.yml)并没有提供从 AWS 配置 GPU 的方法（！）。这是在云端做数据科学的一个基本部分，因此缺乏文档令我感到惊讶。（[我不是第一个对缺乏文档感到疑惑的人](https://github.com/Netflix/metaflow/issues/250)）

以下是一个代码片段，演示了在 Metaflow 中将作业发送到云端的样子：

```py
@pip(libraries={'tensorflow': '2.15', 'keras-cv': '0.9.0', 'pycocotools': '2.0.7', 'wandb': '0.17.3'})
@batch(gpu=1, memory=8192, image='docker.io/tensorflow/tensorflow:latest-gpu', queue="job-queue-gpu-metaflow")
@environment(vars={
    "S3_BUCKET_ADDRESS": os.getenv('S3_BUCKET_ADDRESS'),
    'WANDB_API_KEY': os.getenv('WANDB_API_KEY'),
    'WANDB_PROJECT': os.getenv('WANDB_PROJECT'),
    'WANDB_ENTITY': os.getenv('WANDB_ENTITY')})
@step
def augment_data_train_model(self):
  """
  Code to pull data from S3, augment it, and train our model.
  """
```

注意指定所需库和必要环境变量的重要性。因为计算任务是在云端运行的，它将无法访问你本地计算机上的虚拟环境或 `.env` 文件中的环境变量。使用 Metaflow 装饰器来解决这个问题既优雅又简单。

确实，你不必成为 AWS 专家才能在云端运行计算任务，但不要指望仅仅安装 Metaflow，使用默认的 CloudFormation 模板就能成功。*没有运维的 MLOps* **实在**太美好，难以置信；也许这个短语应该是 *没有运维的 MLOps；在学习了一些运维之后*。

# 有效管理依赖关系

将一个开发项目转变为生产项目时，最重要的考虑因素之一是如何管理依赖关系。依赖关系指的是 Python 包，例如 TensorFlow、PyTorch、Keras、Matplotlib 等。

依赖管理类似于管理食谱中的食材，以确保一致性。一个食谱可能会说“*加入一汤匙盐*”。这在某种程度上是可重复的，但有经验的读者可能会问“[*Diamond Crystal 还是 Morton*](https://www.simplyrecipes.com/how_to_swap_morton_kosher_salt_for_diamond_crystal_and_vice_versa/)？”指定使用的盐的确切品牌可以最大程度地提高食谱的可重复性。

类似地，在机器学习中，依赖管理有不同的层次：

+   使用`requirements.txt`文件。这种简单的方式列出了所有带有固定版本的Python包。它工作得相当不错，但也有局限性：虽然你可以固定这些高层依赖，但无法固定任何传递依赖（依赖的依赖）。这使得创建可重复的环境变得非常困难，并且因为包被下载和安装，运行时也会变慢。例如：

```py
pinecone==4.0.0
langchain==0.2.7
python-dotenv==1.0.1
pandas==2.2.2
streamlit==1.36.0
iso-639==0.4.5
prefect==2.19.7
langchain-community==0.2.7
langchain-openai==0.1.14
langchain-pinecone==0.1.1
```

这工作得相当不错，但也有局限性：虽然你可以固定这些高层依赖，但无法固定任何传递依赖（依赖的依赖）。这使得创建可重复的环境变得非常困难，并且因为包被下载和安装，运行时也会变慢。

+   使用Docker容器。这是黄金标准。它封装了整个环境，包括操作系统、库、依赖项和配置文件，使其非常一致且可重复。不幸的是，使用Docker容器可能会比较复杂，尤其是当数据科学家没有平台使用经验时。

[Metaflow](https://docs.metaflow.org/scaling/dependencies/libraries) `[@pypi/@conda](https://docs.metaflow.org/scaling/dependencies/libraries)` [装饰器](https://docs.metaflow.org/scaling/dependencies/libraries)在这两种选项之间找到了一个折中方案，既轻量且简单，便于数据科学家使用，同时比`requirements.txt`文件更具鲁棒性和可重复性。这些装饰器基本上执行以下操作：

+   为流程中的每一步创建独立的虚拟环境。

+   锁定Python解释器版本，而简单的`requirements.txt`文件做不到这一点。

+   为每一步解析完整的依赖图，并将其锁定以确保稳定性和可重复性。这个锁定的图被存储为元数据，便于审计和一致的环境重建。

+   将本地解析的环境传输到远程执行，即使远程环境的操作系统和CPU架构与客户端不同。

这比仅仅使用`requirements.txt`文件要好得多，而且不需要数据科学家额外学习任何内容。

让我们回顾一下训练步骤，看看一个示例：

```py
@pypi(libraries={'tensorflow': '2.15', 'keras-cv': '0.9.0', 'pycocotools': '2.0.7', 'wandb': '0.17.3'})
@batch(gpu=1, memory=8192, image='docker.io/tensorflow/tensorflow:latest-gpu', queue="job-queue-gpu-metaflow")
@environment(vars={
    "S3_BUCKET_ADDRESS": os.getenv('S3_BUCKET_ADDRESS'),
    'WANDB_API_KEY': os.getenv('WANDB_API_KEY'),
    'WANDB_PROJECT': os.getenv('WANDB_PROJECT'),
    'WANDB_ENTITY': os.getenv('WANDB_ENTITY')})
@step
def augment_data_train_model(self):
  """
  Code to pull data from S3, augment it, and train our model.
  """
```

我们所要做的就是指定库和版本，Metaflow会处理剩下的部分。

不幸的是，事情并非完全顺利。我的个人笔记本电脑是 Mac，但 AWS Batch 中的计算实例采用的是 Linux 架构。这意味着我们必须为 Linux 机器创建隔离的虚拟环境，而不是 Mac。这就需要所谓的**交叉编译**。我们只有在处理 `.whl`（二进制）包时才能进行交叉编译。我们不能在尝试交叉编译时使用 `.tar.gz` 或其他源代码发行版。这是 `pip` 的一个特点，而不是 Metaflow 的问题。使用 `@conda` 装饰器是有效的（`conda` 似乎能够解决 `pip` 不能解决的问题），但如果我想利用 GPU 进行计算，则必须使用 conda 中的 `tensorflow-gpu` 包，这也带来了自己的问题。虽然有一些解决方法，但它们为我希望保持简洁的教程增加了太多复杂性。因此，我实际上不得不选择了 `pip install -r requirements.txt`（使用了自定义 Python `@pip` 装饰器来实现）。虽然不太理想，但它确实有效。

# 如何调试生产环境中的流程

最初，使用 Metaflow 感觉有些慢。每次步骤失败时，我都需要添加打印语句并重新运行整个流程——这是一个耗时且代价高昂的过程，尤其是在计算密集型步骤中。

一旦我发现可以将[流程变量作为工件](https://docs.metaflow.org/metaflow/client)存储，并且之后可以在 Jupyter notebook 中访问这些工件的值，我的迭代速度大大提升。例如，在处理 `model.predict` 调用的输出时，我将变量作为工件存储，以便于调试。以下是我如何做到的：

```py
image = example["images"]
self.image = tf.expand_dims(image, axis=0)  # Shape: (1, 416, 416, 3)

y_pred = model.predict(self.image)

confidence = y_pred['confidence'][0]
self.confidence = [conf for conf in confidence if conf != -1]

self.y_pred = bounding_box.to_ragged(y_pred)
```

在这里，`model` 是我经过充分训练的目标检测模型，`image` 是一张示例图像。当我在处理这个脚本时，我遇到了处理 `model.predict` 调用输出的问题。输出是什么类型的？输出的结构是什么样的？拉取示例图像的代码是否有问题？

为了检查这些变量，我使用 `self._` 语法将它们作为工件存储。任何可以被[pickle](https://docs.python.org/3/library/pickle.html)序列化的对象都可以作为 Metaflow 工件存储。如果你跟随我的教程，这些工件将被存储在 Amazon S3 存储桶中，供以后引用。为了检查示例图像是否正确加载，我可以在我的本地计算机的同一代码库中打开 Jupyter notebook，并通过以下代码访问该图像：

```py
import matplotlib.pyplot as plt

latest_run = Flow('main_flow').latest_run
step = latest_run['evaluate_model']
sample_image = step.task.data.image
sample_image = sample_image[0,:, :, :]

one_image_normalized = sample_image / 255

# Display the image using matplotlib
plt.imshow(one_image_normalized)
plt.axis('off')  # Hide the axes
plt.show()
```

在这里，我们获取流程的最新运行，并通过在 Flow 调用中指定 `main_flow` 来确保获取到流程的信息。我存储的工件来自 `evaluate_model` 步骤，因此我指定了这一步骤。我通过调用 `.data.image` 获取图像数据。最后，我们可以绘制图像来检查并查看我们的测试图像是否有效，或者是否在管道的某个环节被破坏了：

![](../Images/190557d883fea877399a116a9732e6e1.png)

在我的 Jupyter notebook 中输出的图像。图像来源：作者。

很棒，这和从PlantDoc数据集中下载的原始图像一致（尽管颜色看起来有些奇怪）。为了查看我们物体检测模型的预测结果，我们可以使用以下代码：

```py
latest_run = Flow('main_flow').latest_run
step = latest_run['evaluate_model']
y_pred = step.task.data.y_pred
print(y_pred)
```

![](../Images/8d79e9da2d852fa191d977303249ae23.png)

来自物体检测模型的预测。图片由作者提供。

输出结果似乎表明这个图像没有预测的边界框。这一点很有意思，可能有助于解释某个步骤为何表现异常或出现错误。

所有这些都可以通过一个简单的Jupyter笔记本完成，这是所有数据科学家都很熟悉的。那么，何时应将变量作为工件存储在Metaflow中呢？Ville Tuulos给出了一个启发式的方法[2]：

> 一条经验法则：使用实例变量（例如self）来存储任何可能在步骤外部有用的数据和对象。仅将本地变量用于中间的临时数据。如果不确定，使用实例变量，因为它们使调试更加容易。

如果你在使用Metaflow，请从我的经验中吸取教训：**充分利用工件和Jupyter笔记本，使调试在生产级项目中变得轻松。**

关于调试的另一个注意事项：如果一个流程在某个特定步骤失败，且你希望从该失败步骤重新运行流程，可以在Metaflow中使用`resume`命令。这样可以加载之前步骤的所有相关输出，而无需重新执行它们，从而节省时间。直到我尝试了[Prefect](https://docs.prefect.io/latest/)，才意识到那里并没有一个简单的方法来做到这一点。

# 寻找合适的步骤大小

[Goldilocks](https://en.wikipedia.org/wiki/Goldilocks_and_the_Three_Bears)步骤的大小应该是多少？理论上，你可以把整个脚本塞进一个巨大的`pull_and_augment_data_and_train_model_and_evaluate_model_and_deploy`步骤中，但这样并不可取。如果流程中的某个部分失败，你就不能轻松使用`resume`功能跳过重新运行整个流程。

相反，将脚本拆分为一百个微小步骤也是可能的，但这同样不推荐。存储工件和管理步骤会带来一定的开销，拥有一百个步骤会占据大部分执行时间。为了找到一个合适的步骤大小，Tuulos告诉我们：

> 一条经验法则：将工作流结构化为逻辑清晰、容易解释和理解的步骤。如果不确定，倾向于选择较小的步骤。小步骤往往比大步骤更容易理解和调试。

最初，我将我的流程结构设计为这些步骤：

+   增强数据

+   训练模型

+   评估模型

+   部署模型

在增强数据后，我需要将数据上传到一个S3存储桶，然后在`train`步骤中下载增强后的数据，用于模型训练，原因有两个：

+   `augment`步骤将在我的本地笔记本上进行，而`train`步骤则会发送到云端的GPU实例。

+   Metaflow 的工件通常用于在步骤之间传递数据，但它无法处理 TensorFlow 数据集对象，因为它们不能被 pickle。于是我将它们转换为 `tfrecords` 格式并上传到 S3。

这个上传/下载过程花费了很长时间。因此，我将数据增强和训练步骤合并为一个步骤。这样减少了流程的运行时间和复杂性。如果你感兴趣，可以查看我 GitHub 仓库中的 `separate_augement_train` 分支，该版本包含了分开的步骤。

# 主要收获

在本文中，我讨论了在将我的目标检测项目投入生产时所经历的一些高峰和低谷。简要总结如下：

+   你必须学习一些操作，才能在没有操作的情况下实现 MLOps。但在学习了一些基础的设置后，你将能够仅使用 Python 装饰器将计算任务发送到 AWS。本文附带的代码库介绍了如何在 AWS 中配置 GPU，因此如果这是你的目标之一，请仔细研究。

+   依赖管理是生产中的一个关键步骤。`requirements.txt` 文件是最基本的要求，Docker 是黄金标准，而 Metaflow 提供了一条中间路径，适用于许多项目。只不过这个项目不适用，不幸的是。

+   在 Metaflow 中，使用工件和 Jupyter 笔记本可以方便地进行调试。使用 `resume` 功能可以避免重新运行时间或计算密集型的步骤。

+   在将脚本拆分为适合 Metaflow 流程的步骤时，尽量将步骤拆分成合理大小的小步骤，倾向于使用较小的步骤。但如果开销过大，也不要害怕合并步骤。

这个项目仍然有一些我希望改进的方面。一个方面是添加更多的数据，这样我们就能在更多种类的植物上检测疾病。另一个方面是为项目添加前端，允许用户上传图片并按需获取物体检测。像 Streamlit 这样的库非常适合这个功能。最后，我希望最终模型的性能能达到最先进的水平。Metaflow 具备并行训练多个模型的能力，这将有助于实现这一目标。不幸的是，这需要大量的计算资源和资金，但这是任何最先进模型所必需的。

# 参考文献

[1] C. Huyen, [《机器学习面试简介》](https://huyenchip.com/ml-interviews-book/)(2021), 自出版

[2] V. Tuulos, [《高效的数据科学基础设施》](https://www.manning.com/books/effective-data-science-infrastructure) (2022), Manning 出版社
