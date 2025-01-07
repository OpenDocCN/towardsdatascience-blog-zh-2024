# 在 AWS Inferentia 和 Trainium 上优化 AI 模型

> 原文：[`towardsdatascience.com/ai-model-optimization-on-aws-inferentia-and-trainium-cfd48e85d5ac?source=collection_archive---------3-----------------------#2024-10-20`](https://towardsdatascience.com/ai-model-optimization-on-aws-inferentia-and-trainium-cfd48e85d5ac?source=collection_archive---------3-----------------------#2024-10-20)

## 提升 ML 性能的 AWS Neuron SDK 技巧

[](https://chaimrand.medium.com/?source=post_page---byline--cfd48e85d5ac--------------------------------)![Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--cfd48e85d5ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfd48e85d5ac--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfd48e85d5ac--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--cfd48e85d5ac--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfd48e85d5ac--------------------------------) ·阅读时间 9 分钟·2024 年 10 月 20 日

--

![](img/6cf33bb6d494f40d4338317fda1b9fb6.png)

图片来源：[julien Tromeur](https://unsplash.com/@julientromeur?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们正处于人工智能的黄金时代，前沿模型正在颠覆各行各业，并有望改变我们所知的生活方式。这些进步的推动力是日益强大的 AI 加速器，如[NVIDIA H100 GPUs](https://www.nvidia.com/en-eu/data-center/h100/)、[Google Cloud TPUs](https://cloud.google.com/tpu)、[AWS 的 Trainium](https://aws.amazon.com/machine-learning/trainium/)和[Inferentia](https://aws.amazon.com/machine-learning/inferentia/)芯片等。随着选项数量的增加，面临的挑战是选择最优平台来处理我们的机器学习（ML）工作负载——这是一个至关重要的决策，因为与 AI 计算相关的成本非常高。重要的是，全面评估每个选项需要确保我们正在最大化其利用率，以充分发挥其能力。

在本文中，我们将回顾几种优化 AWS 定制 AI 芯片上的 ML 工作负载的技术，使用的是[AWS Neuron SDK](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/index.html)。这继续了我们关于跨各种平台和环境的 ML 模型性能分析和优化的系列文章（例如，参见此处和此处）。虽然我们的主要关注点将是 ML 训练工作负载和 AWS Inferentia2，但所讨论的技术同样适用于 AWS Trainium。（回想一下，尽管 AWS Inferentia 主要设计为 AI 推理芯片，但我们之前展示过它在训练任务中的有效性。）

一般来说，性能优化是一个迭代过程，其中包括性能分析步骤，用于适当识别性能瓶颈和资源利用不足（例如，参见此处）。然而，由于我们将讨论的技术是通用的（即，它们可能适用于任何模型，无论其性能特征如何），我们将[Neuron SDK 性能分析](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/frameworks/torch/torch-neuronx/programming-guide/torch-neuronx-profiling-dev-guide.html)的讨论推迟到未来的文章中。

## 免责声明

我们分享的代码仅用于演示目的——我们不对其准确性、最优性或健壮性做任何声明。请不要将本文视为官方[Neuron SDK 文档](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/index.html)的替代品。请不要将我们提到的任何平台、库或优化技术解读为对其使用的支持。最适合您的选项将极大地依赖于您的具体用例，并且需要您自己深入的调查和分析。

以下实验是在一个[Amazon EC2 inf2.xlarge](https://aws.amazon.com/ec2/instance-types/inf2/)实例上运行的（该实例包含两个[Neuron 核心](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/arch/neuron-hardware/neuron-core-v2.html#neuroncores-v2-arch)和四个 vCPU）。我们使用了当时可用的最新版本[Deep Learning AMI for Neuron](https://aws.amazon.com/releasenotes/aws-deep-learning-ami-neuron-ubuntu-22-04/)，即“Deep Learning AMI Neuron（Ubuntu 22.04）20240927”，并配备了[AWS Neuron 2.20](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/release-notes/index.html)和[PyTorch 2.1](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/torch-neuronx/introducing-pytorch-2-1.html)。有关设置和安装的更多详细信息，请参见[SDK 文档](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/setup/neuron-setup/multiframework/multi-framework-ubuntu22-neuron-dlami.html#setup-ubuntu22-multi-framework-dlami)。请记住，Neuron SDK 正在积极开发中，我们提到的 API 和我们报告的运行时测量结果在您阅读本文时可能已经过时。请确保与最新的 SDK 和文档保持同步。

# 玩具模型

为了方便讨论，我们引入以下简单的[Vision Transformer](https://en.wikipedia.org/wiki/Vision_transformer)（ViT）分类模型（基于[timm](https://pypi.org/project/timm/)版本 1.0.10）：

```py
from torch.utils.data import Dataset
import time, os
import torch
import torch_xla.core.xla_model as xm
import torch_xla.distributed.parallel_loader as pl
from timm.models.vision_transformer import VisionTransformer

# use random data
class FakeDataset(Dataset):
  def __len__(self):
    return 1000000

  def __getitem__(self, index):
    rand_image = torch.randn([3, 224, 224], dtype=torch.float32)
    label = torch.tensor(data=index % 1000, dtype=torch.int64)
    return rand_image, label

def train(batch_size=16, num_workers=0):
  # Initialize XLA process group for torchrun
  import torch_xla.distributed.xla_backend
  torch.distributed.init_process_group('xla')

  # multi-processing: ensure each worker has same initial weights
  torch.manual_seed(0)
  dataset = FakeDataset()
  model = VisionTransformer()

  # load model to XLA device
  device = xm.xla_device()
  model = model.to(device)
  optimizer = torch.optim.Adam(model.parameters())
  data_loader = torch.utils.data.DataLoader(dataset,
                                            batch_size=batch_size,
                                            num_workers=num_workers)

  data_loader = pl.MpDeviceLoader(data_loader, device)
  loss_function = torch.nn.CrossEntropyLoss()
  summ = 0
  count = 0
  t0 = time.perf_counter()

  for step, (inputs, targets) in enumerate(data_loader, start=1):
    optimizer.zero_grad()
    outputs = model(inputs)
    loss = loss_function(outputs, targets)
    loss.backward()
    xm.optimizer_step(optimizer)
    batch_time = time.perf_counter() - t0
    if step > 10:  # skip first steps
      summ += batch_time
      count += 1
    t0 = time.perf_counter()
    if step > 500:
      break
  print(f'average step time: {summ/count}')

if __name__ == '__main__':
  train()

# Initialization command:
# torchrun --nproc_per_node=2 train.py
```

在我们的 AWS Inferentia 实例的两个核心上运行基准模型，结果是每秒训练 251.98 个样本。

在接下来的部分中，我们将迭代应用多种潜在的优化技术，并评估它们对步长时间性能的影响。虽然我们不会详细介绍每种方法，但会提供进一步阅读的参考（例如，这里）。重要的是，我们将呈现的列表并非全面涵盖——还有许多技术超出了我们的讨论范围。我们将这些方法分为三类：PyTorch 优化、OpenXLA 优化和特定于 Neuron 的优化。然而，展示的顺序并不固定。实际上，某些技术是相互依赖的——例如，应用混合精度优化可能会释放出足够的设备内存，从而允许增加批量大小。

# PyTorch 性能优化

在之前的文章中（例如，这里），我们已经广泛讨论了在 GPU 上进行 PyTorch 模型性能分析和优化的话题。我们讨论的许多技术也适用于其他 AI 加速器。在本节中，我们将重新审视其中的一些技术，并将其应用于 AWS Inferentia。

## 多进程数据加载

在[多进程数据加载](https://pytorch.org/docs/stable/data.html#single-and-multi-process-data-loading)中，输入数据是在一个或多个专用的 CPU 进程中准备的，而不是在运行训练步骤的相同进程中进行处理。这允许数据加载和训练过程重叠，从而提高系统利用率并显著加速。进程数由[PyTorch DataLoader](https://pytorch.org/docs/stable/data.html#torch.utils.data.DataLoader)的*num_workers*参数控制。在下面的代码块中，我们将*num_workers*设置为 1 来运行脚本：

```py
train(num_workers=1)
```

这种变化导致训练速度为每秒 253.56 个样本，提升不到 1%。

## 批量大小优化

另一个可能影响训练速度的重要超参数是训练批量大小。我们发现，增加批量大小通常可以提高系统利用率并带来更好的性能。然而，效果会根据模型和平台的不同而有所变化。在我们的 AWS Inferentia 玩具模型中，我们发现每个神经元核心使用 8 个样本的批量大小训练时，速度为每秒 265.68 个样本，比每核心 16 个样本的批量大小快大约 5%。

```py
train(batch_size=8, num_workers=1)
```

## PyTorch 自动混合精度

提升性能的另一个常用方法是使用较低精度的浮点数，例如 16 位的 BFloat16。值得注意的是，某些模型组件可能与降低精度的浮点数不兼容。PyTorch 的[自动混合精度（AMP）](https://pytorch.org/tutorials/recipes/recipes/amp_recipe.html)模式会尝试自动将最合适的浮点类型匹配到每个模型操作上。尽管 Neuron 编译器提供了不同的[混合精度](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/neuronx-cc/neuronx-cc-training-mixed-precision.html#mixed-precision-and-performance-accuracy-tuning-neuronx-cc)选项，它也[支持使用 PyTorch AMP](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/frameworks/torch/torch-neuronx/programming-guide/training/pytorch-neuron-programming-guide.html#automatic-mixed-precision)的选项。在下面的代码块中，我们包括了使用 PyTorch AMP 所需的修改。

```py
def train(batch_size=16, num_workers=0):
  # Initialize XLA process group for torchrun
  import torch_xla.distributed.xla_backend
  torch.distributed.init_process_group('xla')

  # multi-processing: ensure each worker has same initial weights
  torch.manual_seed(0)
  dataset = FakeDataset()
  model = VisionTransformer()

  # load model to XLA device
  device = xm.xla_device()
  model = model.to(device)
  optimizer = torch.optim.Adam(model.parameters())
  data_loader = torch.utils.data.DataLoader(dataset,
                                            batch_size=batch_size,
                                            num_workers=num_workers)

  data_loader = pl.MpDeviceLoader(data_loader, device)
  loss_function = torch.nn.CrossEntropyLoss()
  summ = 0
  count = 0
  t0 = time.perf_counter()

  for step, (inputs, targets) in enumerate(data_loader, start=1):
    optimizer.zero_grad()

    # use PyTorch AMP
    with torch.autocast(dtype=torch.bfloat16, device_type='cuda'):
      outputs = model(inputs)
      loss = loss_function(outputs, targets)
    loss.backward()
    xm.optimizer_step(optimizer)
    batch_time = time.perf_counter() - t0
    if step > 10:  # skip first steps
      summ += batch_time
      count += 1
    t0 = time.perf_counter()
    if step > 500:
      break
  print(f'average step time: {summ/count}')

if __name__ == '__main__':
  # disable neuron compilar casting
  os.environ["NEURON_CC_FLAGS"] = "--auto-cast=none"
  torch.cuda.is_bf16_supported = lambda: True
  train(batch_size=8, num_workers=1)
```

结果训练速度为每秒 196.64 个样本，比 Neuron 编译器的[默认混合精度](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/neuronx-cc/neuronx-cc-training-mixed-precision.html#neuronx-cc-training-mixed-precision)设置低约 26%。需要注意的是，虽然本文重点讨论性能，但在实际应用场景中，我们还需要评估所选择的混合精度策略对[模型准确性](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/neuronx-cc/neuronx-cc-training-mixed-precision.html#performance-accuracy-tradeoffs)的影响。

# OpenXLA 优化

正如之前的文章中讨论的那样，Neuron Cores 被视为[XLA 设备](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/frameworks/torch/torch-neuronx/programming-guide/training/pytorch-neuron-programming-guide.html#neuron-xla-device)，并且[torch-neuronx](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/quick-start/torch-neuron.html) Python 包实现了[PyTorch/XLA](https://github.com/pytorch/xla/) API。因此，OpenXLA 框架提供的任何优化机会，特别是 PyTorch/XLA API 所提供的优化，都可以在 AWS Inferentia 和 Trainium 上利用。在本节中，我们将考虑其中的一些优化机会。

## BFloat16 精度

OpenXLA 支持通过 XLA_USE_BF16 环境变量将所有浮动数值转换为 BFloat16，如下代码块所示：[将所有浮动数值转换为 BFloat16](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/frameworks/torch/torch-neuronx/programming-guide/training/pytorch-neuron-programming-guide.html#automatic-casting-of-float-tensors-to-bfloat16)：

```py
if __name__ == '__main__':
  os.environ['XLA_USE_BF16'] = '1'
  train(batch_size=8, num_workers=1)
```

结果训练速度为每秒 394.51 个样本，比[默认混合精度](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/neuronx-cc/neuronx-cc-training-mixed-precision.html#neuronx-cc-training-mixed-precision)选项的速度快近 50%。

## 多进程设备加载

PyTorch/XLA 的[MpDeviceLoader](https://pytorch.org/xla/master/_modules/torch_xla/distributed/parallel_loader.html#MpDeviceLoader)及其内部的[ParallelLoader](https://pytorch.org/xla/master/_modules/torch_xla/distributed/parallel_loader.html)，负责将输入数据加载到加速器上，包括多个参数，用于控制数据从主机到设备的传输。在下面的代码块中，我们调节[*batches_per_execution*](https://github.com/pytorch/xla/blob/v2.1.0/torch_xla/distributed/parallel_loader.py#L86)设置，该设置决定了每次[ParallelLoader](https://pytorch.org/xla/master/_modules/torch_xla/distributed/parallel_loader.html)执行周期中，复制到设备的批次数。通过增加该设置，我们旨在减少主机与设备之间通信的开销：

```py
data_loader = torch.utils.data.DataLoader(dataset,
                                          batch_size=batch_size,
                                          num_workers=num_workers
                                          )
data_loader = pl.MpDeviceLoader(data_loader, 
                                device, batches_per_execution=10)
```

由于这种优化，训练速度提高到每秒 1,027.39 个样本，代表着额外的 260%的加速。

## 使用 OpenXLA 后端进行 Torch 编译

在之前的文章中（例如，这里），我们演示了使用[PyTorch 的图编译](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html)所带来的潜在性能提升。尽管[OpenXLA](https://openxla.org/xla)包含了自己的图创建和即时编译机制，[torch.compile](https://pytorch.org/xla/master/torch_compile.html)可以通过消除每一步追踪模型操作的需求，提供额外的加速。以下代码片段演示了如何使用专用的*openxla*后端来编译模型：

```py
model = model.to(device)
model = torch.compile(backend='openxla')
```

尽管 torch.compile 目前[尚不支持](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/release-notes/torch/torch-neuronx/index.html#known-limitations)Neuron SDK，但我们提前提及它，以期望其未来发布。

# Neuron SDK 优化

在本节中，我们将考虑 AWS Neuron SDK 提供的一些优化机会，特别是 Neuron 编译器所提供的优化。

## 混合精度

Neuron SDK 支持多种[混合精度](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/general/appnotes/neuronx-cc/neuronx-cc-training-mixed-precision.html#mixed-precision-and-performance-accuracy-tuning-neuronx-cc)设置。在下面的代码块中，我们通过*NEURON_CC_FLAGS*环境变量编程，指示编译器将所有浮点数转换为 BFloat16。

```py
if __name__ == '__main__':
  os.environ["NEURON_CC_FLAGS"] = "--auto-cast all --auto-cast-type bf16"
  train(batch_size=8, num_workers=1)
```

这导致（不足为奇）与上文提到的 OpenXLA BFloat16 实验相似的训练速度。

## FP8

NeuronCoreV2 的一个独特特点是它支持八位浮点类型 fp8_e4m3。下面的代码块演示了如何配置 Neuron 编译器，以自动将所有浮点操作转换为 FP8：

```py
if __name__ == '__main__':
 os.environ["NEURON_CC_FLAGS"] = "--auto-cast all --auto-cast-type fp8_e4m3"
 train(batch_size=8, num_workers=1)
```

虽然 FP8 在某些情况下可以加速训练，但由于其精度和动态范围的降低，保持稳定的收敛性比使用 BFloat16 时更具挑战性。请参阅我们的上一篇文章，了解有关 FP8 训练的潜在好处和挑战。

在我们的模型中，使用 FP8 实际上会比 BFloat16 更影响运行时性能，将训练速度降低到每秒 940.36 个样本。

## 编译器优化

[Neuron 编译器](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/compiler/neuronx-cc/api-reference-guide/neuron-compiler-cli-reference-guide.html)包含多个控制项，用于优化编译后的图的运行时性能。两个关键设置是*model-type*和*opt-level*。*model-type*设置应用于特定模型架构的优化，如变换器（transformers），而*opt-level*设置则允许在编译时间和运行时性能之间找到平衡。在下面的代码块中，我们将*model-type*设置为*tranformer*，将*opt-level*设置为最高性能选项。我们还进一步指定*target*运行设备为*inf2*，以确保模型针对目标设备进行了优化。

```py
if __name__ == '__main__':
  os.environ['XLA_USE_BF16'] = '1'
  os.environ["NEURON_CC_FLAGS"] = "--model-type transformer " \
                                  "--optlevel 3" \
                                  " --target inf2"
  train(batch_size=8, num_workers=1)
```

上述配置实现了每秒 1093.25 个样本的训练速度，带来了约 6%的改善。

# 结果

我们在下表中总结了实验的结果。请记住，我们讨论的每种优化方法的效果将极大地依赖于模型和运行时环境。

![](img/fd373857d4f25e1c26fe23f03a8ce4d0.png)

实验结果（作者）

我们采用的技术使性能相比基准实验提高了 435%。通过重新审视和微调我们讨论的一些方法，或者应用本文未涉及的其他优化技术，可能还会实现进一步的加速。

我们的目标是展示一些可用的优化策略，并展示它们对运行时性能的潜在影响。然而，在实际场景中，我们需要评估这些优化如何影响模型的收敛性。在某些情况下，可能需要调整模型配置，以确保在不牺牲准确性的情况下实现最佳性能。此外，使用性能分析工具来识别瓶颈并衡量系统资源利用率，对于指导和促进我们的优化活动至关重要。

# 总结

如今，我们有幸能够在各种各样的系统上运行我们的机器学习工作负载。无论我们选择哪个平台，我们的目标都是最大化其能力。在本文中，我们重点介绍了 AWS Inferentia，并回顾了几种加速在其上运行的机器学习工作负载的技术。一定要查看我们的[其他文章](https://chaimrand.medium.com/)，了解更多关于各类 AI 加速器的优化策略。
