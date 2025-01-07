# 加速AI/ML模型训练的自定义操作符

> 原文：[https://towardsdatascience.com/accelerating-ai-ml-model-training-with-custom-operators-163ef2a04b12?source=collection_archive---------1-----------------------#2024-08-11](https://towardsdatascience.com/accelerating-ai-ml-model-training-with-custom-operators-163ef2a04b12?source=collection_archive---------1-----------------------#2024-08-11)

## 关于创建特定模型GPU内核的潜在好处及其在优化动态形状张量使用中的应用

[](https://chaimrand.medium.com/?source=post_page---byline--163ef2a04b12--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--163ef2a04b12--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--163ef2a04b12--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--163ef2a04b12--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--163ef2a04b12--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--163ef2a04b12--------------------------------) ·阅读时长15分钟·2024年8月11日

--

![](../Images/85652d4b9d9dc933840a03993bb7ae6b.png)

图片由[David Marioni](https://unsplash.com/@dgeneva68?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文是关于分析和优化AI/ML模型训练运行时性能的长篇[系列文章](/pytorch-model-performance-analysis-and-optimization-10c3c5822869)的延续。文章本可以标题为“PyTorch模型性能分析与优化——第七部分”，但考虑到当前主题的重要性，我们认为需要为此专门撰写一篇（或多篇）文章。在我们之前的文章中，我们详细讨论了分析和优化AI/ML工作负载的重要性，以及它对AI/ML模型开发的速度和成本可能产生的重大影响。我们提倡使用多种工具和技术来分析和优化训练性能，并且已经展示了许多实际应用。本文将讨论一种更为先进的优化技术——这项技术能够将真正的高手与普通业余者区分开来——在C++和CUDA中创建自定义的PyTorch操作符。

流行的机器学习框架，如 PyTorch、TensorFlow 和 JAX，通常使用为底层硬件（无论是 CPU、GPU，还是像 Google TPU 这样的 AI 专用 ASIC）优化的软硬件组件构建。然而，不可避免地，你可能会发现组成模型的某些计算模块的性能不尽如人意或不够优化。通常，调整低级代码模块——通常被称为 *内核（kernels）*——以满足 AI/ML 模型的特定需求，能够显著加速模型训练和推理的运行时性能。这样的加速可以通过实现之前不支持的功能（例如，先进的注意力模块）、将独立操作融合在一起（例如，参见[PyTorch 的乘加融合教程](https://pytorch.org/tutorials/advanced/cpp_custom_ops.html#cpp-custom-ops-tutorial)），和/或根据当前模型的特定属性优化现有内核来实现。重要的是，执行此类定制的能力依赖于 AI 硬件和 ML 框架的支持。尽管我们在本文中的重点是 NVIDIA GPU 和 PyTorch 框架，但应该注意，其他 AI 专用 ASIC 和 ML 框架也提供类似的定制内核功能。NVIDIA 通过其[CUDA 工具包](https://developer.nvidia.com/cuda-toolkit)支持开发自定义内核。PyTorch 也提供了专用的[API](https://pytorch.org/docs/stable/cpp_extension.html#torch.utils.cpp_extension.CUDAExtension)和[教程](https://pytorch.org/tutorials/advanced/cpp_custom_ops.html#cpp-custom-ops-tutorial)，用于公开这一功能并将其集成到模型设计中。  

本文的目的是引起大家对内核定制的强大功能和潜力的关注，并展示其在训练动态形状张量模型这一独特挑战中的应用。我们并非——绝对不是——要替代有关开发自定义操作的官方文档。此外，我们分享的示例仅供演示目的使用。我们没有对这些示例进行优化，也没有验证它们的稳健性、耐久性或准确性。如果你决定基于本文投资于 AI/ML 优化，通过自定义 CUDA 内核开发，你应该确保接受适当的培训。

# 玩具模型 — 动态形状张量的挑战  

AI模型中动态形状张量的普遍存在可能会在性能优化方面带来独特且令人兴奋的挑战。我们已经在[上一篇文章](/pytorch-model-performance-analysis-and-optimization-part-3-1c5876d78fe2)中看到过一个例子，展示了如何使用布尔掩码触发不希望发生的CPU-GPU同步事件，并且我们提倡避免使用它们。一般来说，AI加速器倾向于偏好固定形状的张量，而不是动态形状的张量。这不仅简化了内存资源的管理，还能为性能优化提供更大的空间（例如，使用[torch.compile](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html)）。下面的玩具示例展示了这一挑战。

假设我们的任务是为下一代数码相机创建一个人脸检测模型。为了训练这个模型，我们提供了一个包含一百万张 *256*×*256* 灰度图像的训练集，以及每张图像的对应地面真实框。自然，每张图像中的人脸数量可能会有很大差异，大多数图像包含五个或更少的人脸，只有少数图像包含数十个甚至数百个。我们的模型要求支持所有这些变化。具体来说，我们的模型需要支持在图像中检测最多 *256* 张人脸。

为了解决这个挑战，我们定义了以下简单的模型，该模型生成边界框并附带一个损失函数。具体来说，我们简单地基于目标框的数量截断模型输出，而不是执行某种[匹配算法](https://en.wikipedia.org/wiki/Hungarian_algorithm)来匹配边界框预测与地面真实目标之间的关系。我们（有些随意地）选择了[广义交并比（GIOU）](https://giou.stanford.edu/)损失函数。现实世界的解决方案可能会更加复杂（例如，它可能包括一个损失组件，针对假阳性进行惩罚）。

```py
import torch
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module):
    def __init__(self):
        super().__init__()
        conv_layers = []
        for i in range(4):
            conv_layers.append(nn.Conv2d(4 ** i, 4 ** (i + 1), 3,
                                         padding='same'))
            conv_layers.append(nn.MaxPool2d(2, 2))
            conv_layers.append(nn.ReLU())
        self.conv_layers = nn.Sequential(*conv_layers)

        self.lin1 = nn.Linear(256 * 256, 256 * 64)
        self.lin2 = nn.Linear(256 * 64, 256 * 4)

    def forward(self, x):
        x = self.conv_layers(x.float())
        x = self.lin2(F.relu(self.lin1(x.view((-1, 256 * 256)))))
        return x.view((-1, 256, 4))

def generalized_box_iou(boxes1, boxes2):
    # loosly based on torchvision generalized_box_iou_loss code
    epsilon = 1e-5

    area1 = (boxes1[..., 2]-boxes1[..., 0])*(boxes1[..., 3]-boxes1[..., 1])
    area2 = (boxes2[..., 2]-boxes2[..., 0])*(boxes2[..., 3]-boxes2[..., 1])

    lt = torch.max(boxes1[..., :2], boxes2[..., :2])
    rb = torch.min(boxes1[..., 2:], boxes2[..., 2:])

    wh = (rb - lt).clamp(min=0)
    inter = wh[..., 0] * wh[..., 1]

    union = area1 + area2 - inter
    iou = inter / union.clamp(epsilon)

    lti = torch.min(boxes1[..., :2], boxes2[..., :2])
    rbi = torch.max(boxes1[..., 2:], boxes2[..., 2:])

    whi = (rbi - lti).clamp(min=0)
    areai = (whi[..., 0] * whi[..., 1]).clamp(epsilon)

    return iou - (areai - union) / areai

def loss_fn(pred, targets_list):
    batch_size = len(targets_list)
    total_boxes = 0
    loss_sum = 0.
    for i in range(batch_size):
        targets = targets_list[i]
        num_targets = targets.shape[0]
        if num_targets > 0:
            sample_preds = pred[i, :num_targets]
            total_boxes += num_targets
            loss_sum += generalized_box_iou(sample_preds, targets).sum()
    return loss_sum / max(total_boxes, 1)
```

由于每个图像中面部的数量不同，损失是针对每个单独样本分别计算的，而不是一次性（针对整个批次）计算。具体来说，CPU将启动与损失函数相关的每个GPU内核，*B*次，其中 *B* 是选择的批量大小。根据批次的大小，这可能会带来显著的开销，正如我们下面所看到的那样。

在下面的代码块中，我们定义了一个生成随机图像和相关边界框的数据集。由于每张图像中人脸的数量不同，我们需要一个[自定义聚合函数](https://pytorch.org/docs/stable/data.html#working-with-collate-fn)来将样本分组为批次：

```py
from torch.utils.data import Dataset, DataLoader
import numpy as np

# A dataset with random images and gt boxes
class FakeDataset(Dataset):
    def __init__(self):
        super().__init__()
        self.size = 256
        self.img_size = [256, 256]

    def __len__(self):
        return 1000000

    def __getitem__(self, index):
        rand_image = torch.randint(low=0, high=256, 
                                   size=[1]+self.img_size,
                                   dtype=torch.uint8)

        # set the distribution over the number of boxes to reflect the fact
        # that the vast majority of images have fewer than 10 faces
        n_boxes = np.clip(np.floor(np.abs(np.random.normal(0, 3)))
                                      .astype(np.int32), 0, 255)

        box_sizes = torch.randint(low=1, high=self.size, size=(n_boxes,2))
        top_left = torch.randint(low=0, high=self.size-1, size=(n_boxes, 2))
        bottom_right = torch.clamp(top_left + box_sizes, 0, self.size -1)
        rand_boxes = torch.concat((top_left,bottom_right), dim = 1)
        return rand_image, rand_boxes.to(torch.uint8)

def collate_fn(batch):
    images = torch.stack([b[0] for b in batch],dim=0)
    boxes = [b[1] for b in batch]
    return images, boxes

train_loader = DataLoader(
    dataset = FakeDataset(),
    batch_size=1024,
    pin_memory=True,
    num_workers=16,
    collate_fn=collate_fn
)
```

通常，每个训练步骤以将训练批次从主机（CPU）复制到设备（GPU）开始。当我们的数据样本大小固定时，它们会批量复制。然而，图像中每个图样的面部数量变化的一个影响是，每个样本的边界框目标需要单独复制，这需要进行更多的单独复制操作。

```py
def data_to_device(data, device):
    if isinstance(data, (list, tuple)):
        return type(data)(
            data_to_device(val, device) for val in data
        )
    elif isinstance(data, torch.Tensor):
        return data.to(device=device, non_blocking=True)
```

最后，我们定义了训练/评估循环。为了便于讨论，我们选择仅关注训练循环的前向传递。注意，我们包含了一个 [PyTorch profiler](https://pytorch.org/tutorials/recipes/recipes/profiler_recipe.html) 对象，并且使用了显式的 [同步事件](https://pytorch.org/docs/stable/generated/torch.cuda.synchronize.html)（以便评估前向传递不同部分的性能）。

```py
device = torch.device("cuda:0")
model = torch.compile(Net()).to(device).train()

# forward portion of training loop wrapped with profiler object
with torch.profiler.profile(
   schedule=torch.profiler.schedule(wait=5, warmup=5, active=10, repeat=1),
   on_trace_ready=torch.profiler.tensorboard_trace_handler('/tmp/perf/'),
   profile_memory=True
) as prof:
    for step, data in enumerate(train_loader):

        with torch.profiler.record_function('copy data'):
            images, boxes = data_to_device(data, device)
            torch.cuda.synchronize(device)

        with torch.profiler.record_function('forward'):
            with torch.autocast(device_type='cuda', dtype=torch.bfloat16):
                outputs = model(images)
            torch.cuda.synchronize(device)

        with torch.profiler.record_function('calc loss'):
            loss = loss_fn(outputs, boxes)
            torch.cuda.synchronize(device)
        prof.step()
        if step > 30:
            break

    # filter and print profiler results
    event_list = prof.key_averages()
    for i in range(len(event_list) - 1, -1, -1):
        if event_list[i].key not in ['forward', 'calc loss', 'copy data']:
            del event_list[i]
    print(event_list.table())
```

## 性能分析

在一台 Google Cloud [g2-standard-16](https://cloud.google.com/compute/docs/gpus#l4-gpus) 虚拟机（配备单个 L4 GPU）、一个专用的 [深度学习虚拟机镜像](https://cloud.google.com/deep-learning-vm/docs/release-notes) 和 PyTorch 2.4.0 上运行我们的脚本，生成了以下输出（我们已将其裁剪以提高可读性）。

```py
-------------  ------------  ------------
         Name     CPU total  CPU time avg
-------------  ------------  ------------
    copy data     288.164ms      28.816ms
      forward        1.192s     119.221ms
    calc loss        9.381s     938.067ms
-------------  ------------  ------------
Self CPU time total: 4.018s
Self CUDA time total: 10.107s
```

尽管损失函数的操作要少得多，但它完全主导了整体步骤时间。每个批次中样本的底层 GPU 核心反复调用的开销在 [TensorBoard](https://pytorch.org/tutorials/intermediate/tensorboard_profiler_tutorial.html#use-tensorboard-to-view-results-and-analyze-model-performance) 的*Trace*视图中非常明显：

![](../Images/adbbc45d08a2f4f6b3d425b40a66e026.png)

每个批次样本调用损失函数的影响，如在 TensorBoard 中所见（作者）

## 通过串联优化

减少调用*损失*函数的次数的一种方法是使用 [串联](https://pytorch.org/docs/stable/generated/torch.concatenate.html) 将每个批次中所有有效的框组合在一起，如下代码块所示。

```py
def loss_with_concat(pred, targets_list):
    bs = len(targets_list)
    all_targets = torch.concat(targets_list, dim = 0)
    num_boxes = [targets_list[i].shape[0] for i in range(bs)]
    all_preds = torch.concat([pred[i,: num_boxes[i]] for i in range(bs)],
                              dim=0)
    total_boxes = sum(num_boxes)
    loss_sum = generalized_box_iou(all_targets, all_preds).sum()
    return loss_sum/max(total_boxes, 1)
```

这个优化的结果如下所示。

```py
-------------  ------------  ------------
         Name     CPU total  CPU time avg
-------------  ------------  ------------
    copy data     522.326ms      52.233ms
      forward        1.187s     118.715ms
    calc loss     254.047ms      25.405ms
-------------  ------------  ------------
Self CPU time total: 396.674ms
Self CUDA time total: 1.871s
```

串联优化使得损失函数的速度提高了37倍（！！）。然而，请注意，它并没有解决每个主机到设备的样本真值数据拷贝的开销。这个开销可以在下面的TensorBoard的*Trace*视图截图中看到：

![](../Images/f360f4261cdc01a8d47bd17bf7273872.png)

如 TensorBoard 中所见的每个批次样本的主机到设备拷贝的影响（作者）

## 通过填充优化

避免使用动态形状张量的一种常见方法是填充。在以下代码块中，我们修改了合并函数，将每个数据样本的真值边界框用零填充，直到最大支持的框数，即256个。（请注意，填充也可以在 Dataset 类中完成。）

```py
def collate_with_padding(batch):
    images = torch.stack([b[0] for b in batch],dim=0)
    padded_boxes = []
    for b in batch:
        p = torch.nn.functional.pad(
                       b[1], (0, 0, 0, 256 - b[1].shape[0]), value = 0)
        padded_boxes.append(p)
    boxes = torch.stack(padded_boxes,dim=0)
    return images, boxes
```

将样本填充为固定大小的张量使我们能够通过一次调用复制批次的真实标签。这也使我们能够通过一次调用损失函数来计算损失。请注意，这种方法需要屏蔽结果损失，如下所示，以便只考虑有效的框。

```py
def loss_with_padding(pred, targets):
    mask = (targets[...,3] > 0).to(pred.dtype)
    total_boxes = mask.sum()
    loss = generalized_box_iou(targets, pred)
    masked_loss = loss*mask
    loss_sum = masked_loss.sum()
    return loss_sum/torch.clamp(total_boxes, 1)
```

结果的运行时性能如下所示：

```py
-------------  ------------  ------------
         Name     CPU total  CPU time avg
-------------  ------------  ------------
    copy data      57.125ms       5.713ms
      forward        1.315s     131.503ms
    calc loss      18.438ms       1.844ms
-------------  ------------  ------------
Self CPU time total: 11.723ms
Self CUDA time total: 1.378s
```

注意到数据复制的性能提升近 10 倍，损失函数性能提升额外的 14 倍。请记住，填充可能会增加 GPU 内存的使用。在我们的案例中，这种增加不到 1%。

尽管我们的损失函数的运行时间显著提高，但我们注意到，损失函数中执行的大多数计算被立即屏蔽掉了。我们不禁想知道，是否有办法通过避免这些冗余操作进一步提高性能。在下一节中，我们将探讨使用自定义 CUDA 核心所提供的机会。

# 创建自定义 CUDA 核心

许多教程会强调创建 CUDA 核心的难度以及高门槛。虽然掌握 CUDA 开发并调优核心以最大化 GPU 的利用率确实可能需要多年的经验以及对 GPU 架构的深入理解，但我们坚信即使是一个初学者（但雄心勃勃的）CUDA 爱好者/机器学习开发者，也能在构建自定义 CUDA 核心时取得成功并从中获益。在本节中，我们将以 PyTorch（相对简单）的 C++/CUDA 扩展为例，并用 GIOU 核心进行增强。我们将分两个阶段进行：首先，我们将天真地将所有 GIOU 逻辑转移到 C++/CUDA，以评估*内核融合*对性能的影响。然后，我们将利用我们新获得的低级控制，添加条件逻辑并减少不必要的算术运算。

开发 CUDA 核心允许你确定每个 GPU 线程执行的核心逻辑，以及这些线程如何分配到底层的 GPU *流式多处理器*（SMs）上。以最优化的方式进行此操作需要对 GPU 架构有专家级的理解，包括 GPU 内存的不同层次、内存带宽、片上加速引擎（例如 TensorCores）、每个 SM 支持的并发线程数以及它们的调度方式，等等。更复杂的是，这些属性在不同的 GPU 代际和型号之间可能有所不同。请参见[这篇博客](https://developer.nvidia.com/blog/even-easier-introduction-cuda)，它提供了一个非常基础，但又非常简单的 CUDA 入门介绍。

## 步骤 1 — 内核融合

回顾我们上次实验的跟踪视图，您可能会注意到，我们的损失计算的前向传播包括大约三十个独立的算术操作，这些操作会导致启动和运行一个独立的 CUDA 内核（可以通过简单地数一下 *cudaLaunchKernel* 事件的数量看出）。这可能会通过多种方式对性能产生负面影响。例如：

1.  每个内核的启动都需要 CPU 和 GPU 之间的专门通信——这是我们始终尽量减少的。

1.  每个内核都需要等到前一个内核完成后才能运行。有时，这是无法避免的，但在某些情况下，比如我们的情况——大多数操作是“逐像素”进行的——是可以避免的。

1.  使用许多独立内核可能会影响 GPU 内存的使用方式。

通过 *内核融合* 优化旨在通过将这些操作合并成较少的内核来减少这种开销，从而减少多个内核的开销。

在下面的代码块中，我们定义了一个内核，它对单个边界框预测-目标对执行 GIOU 计算。我们使用一个 1-D 网格来分配大小为 *256* 的线程块，其中每个块对应训练批次中的一个样本，每个线程对应样本中的一个边界框。因此，每个线程 — 通过 *block* 和 *thread* ID 的组合唯一标识 — 接收预测值 (*boxes1*) 和目标值 (*boxes2*)，并对由 ID 确定的单个边界框执行 GIOU 计算。如前所述，边界框的“有效性”由目标框的值控制。特别地，当对应的框无效时，GIOU 会显式置为零。

```py
#include <torch/extension.h>

#include <cuda.h>
#include <cuda_runtime.h>

namespace extension_cpp {

__global__ void giou_kernel(const float* boxes1,
                            const float* boxes2, 
                            float* giou, 
                            bool* mask) {
  int idx = blockIdx.x * blockDim.x + threadIdx.x;
  bool valid = boxes2[4*idx+3] != 0;
  mask[idx] = valid;

  const float epsilon = 1e-5;

  const float* box1 = &boxes1[idx * 4];
  const float* box2 = &boxes2[idx * 4];

  // Compute area of each box
  float area1 = (box1[2] - box1[0]) * (box1[3] - box1[1]);
  float area2 = (box2[2] - box2[0]) * (box2[3] - box2[1]);

  // Compute the intersection
  float left = max(box1[0], box2[0]);
  float top = max(box1[1], box2[1]);
  float right = min(box1[2], box2[2]);
  float bottom = min(box1[3], box2[3]);

  float inter_w = max(right - left, 0);
  float inter_h = max(bottom - top, 0);
  float inter_area = inter_w * inter_h;

  // Compute the union area
  float union_area = area1 + area2 - inter_area;

  // IoU
  float iou_val = inter_area / max(union_area, epsilon);

  // Compute the smallest enclosing box
  float enclose_left = min(box1[0], box2[0]);
  float enclose_top = min(box1[1], box2[1]);
  float enclose_right = max(box1[2], box2[2]);
  float enclose_bottom = max(box1[3], box2[3]);

  float enclose_w = max(enclose_right - enclose_left, 0);
  float enclose_h = max(enclose_bottom - enclose_top, 0);
  float enclose_area = enclose_w * enclose_h;

  float result = iou_val - (enclose_area-union_area)/max(enclose_area, epsilon);
  // Generalized IoU
  giou[idx] = result * valid;
}

at::Tensor giou_loss_cuda(const at::Tensor& a, const at::Tensor& b) {
  TORCH_CHECK(a.sizes() == b.sizes());
  TORCH_CHECK(a.dtype() == at::kFloat);
  TORCH_CHECK(b.dtype() == at::kFloat);
  TORCH_INTERNAL_ASSERT(a.device().type() == at::DeviceType::CUDA);
  TORCH_INTERNAL_ASSERT(b.device().type() == at::DeviceType::CUDA);
  int bs = a.sizes()[0];
  at::Tensor a_contig = a.contiguous();
  at::Tensor b_contig = b.contiguous();
  at::Tensor giou = torch::empty({a_contig.sizes()[0], a_contig.sizes()[1]},
                                  a_contig.options());
  at::Tensor mask = torch::empty({a_contig.sizes()[0], a_contig.sizes()[1]},
                                  a_contig.options().dtype(at::kBool));
  const float* a_ptr = a_contig.data_ptr<float>();
  const float* b_ptr = b_contig.data_ptr<float>();
  float* giou_ptr = giou.data_ptr<float>();
  bool* mask_ptr = mask.data_ptr<bool>();

  // Launch the kernel
  // The number of blocks is set according to the batch size.
  // Each block has 256 threads corresponding to the number of boxes per sample
  giou_kernel<<<bs, 256>>>(a_ptr, b_ptr, giou_ptr, mask_ptr);

  at::Tensor total_boxes = torch::clamp(mask.sum(), 1);
  torch::Tensor loss_sum = giou.sum();
  return loss_sum/total_boxes;
}

// Registers CUDA implementations for giou_loss
TORCH_LIBRARY_IMPL(extension_cpp, CUDA, m) {
  m.impl("giou_loss", &giou_loss_cuda);
}

}
```

要完成内核创建，我们需要添加适当的 C++ 和 Python 运算符定义（请参见 [muladd.cpp](https://github.com/pytorch/extension-cpp/blob/master/extension_cpp/csrc/muladd.cpp#L68) 和 [ops.py](https://github.com/pytorch/extension-cpp/blob/master/extension_cpp/ops.py#L7)）

```py
// Add the C++ definition
m.def(“giou_loss(Tensor a, Tensor b) -> Tensor”);
```

```py
# define the Python operator
def giou_loss(a: Tensor, b: Tensor) -> Tensor:
    return torch.ops.extension_cpp.giou_loss.default(a, b)
```

要编译我们的内核，请在根目录下运行安装脚本（`pip install .`）。

以下代码块使用了我们新定义的 GIOU CUDA 内核：

```py
def loss_with_kernel(pred, targets):
    pred = pred.to(torch.float32)
    targets = targets.to(torch.float32)
    import extension_cpp
    return extension_cpp.ops.giou_loss(pred, targets)
```

请注意显式转换为 *torch.float32*。这是一个相当昂贵的操作，通过增强我们的 CUDA 内核支持，可以轻松避免。我们将此留给读者作为练习 :）。

运行我们自定义内核的脚本结果如下所示。

```py
-------------  ------------  ------------
         Name     CPU total  CPU time avg   
-------------  ------------  ------------
    copy data      56.901ms       5.690ms    
      forward        1.327s     132.704ms      
    calc loss       6.287ms     628.743us     
-------------  ------------  ------------
Self CPU time total: 6.907ms
Self CUDA time total: 1.380s
```

尽管我们的内核（以及我们在 CUDA 上的经验不足）非常简单，我们还是将损失函数的性能提高了大约 3 倍（628 微秒与 1.8 毫秒相比）。如上所述，这在不费太多力气的情况下还可以进一步改善。

## 第2步 — 条件执行

CUDA 提供的线程级控制使我们能够添加一个条件语句，避免在无效的边界框上进行计算：

```py
__global__ void giou_kernel(const float* boxes1,
                            const float* boxes2,
                            float* giou,
                            bool* mask) {
  int idx = blockIdx.x * blockDim.x + threadIdx.x;
  bool valid = boxes2[4*idx+3] != 0;
  mask[idx] = valid;
  if (valid)
  {
    const float* box1 = &boxes1[idx * 4];
    const float* box2 = &boxes2[idx * 4];
    giou[idx] = compute_giou(box1, box2);
  }
  else
  {
    giou[idx] = 0;
  }
}
```

就我们的内核而言，它对运行时性能的影响可以忽略不计。其原因（推测）是我们的内核相对较小，以至于其运行时间与加载和实例化所需的时间相比，可以忽略不计。我们的条件执行的影响可能只有在更大的内核中才会显现。（作为一个练习，评估内核大小对影响的函数，可以通过让我们的GIOU输出依赖于我们为固定步数运行的*for*循环来进行。这一点我们也留给你作为练习：)。）同样重要的是要考虑在CUDA的[SIMT架构](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#simt-architecture)上，条件执行流的表现，特别是当属于同一*warp*的线程发生分歧时，可能会带来的性能损失。

```py
-------------  ------------  ------------
         Name     CPU total  CPU time avg
-------------  ------------  ------------
    copy data      57.008ms       5.701ms
      forward        1.318s     131.850ms
    calc loss       6.234ms     623.426us
-------------  ------------  ------------
Self CPU time total: 7.139ms
Self CUDA time total: 1.371s
```

# 结果与下一步

我们在下表中总结了实验结果。

![](../Images/705347a88876ca779d62ab6e9af3704e.png)

损失运行时的平均总结（按作者）

重要的是，我们的工作并未结束。诚然，在我们分享的示例中，我们采取了一些捷径：

1.  为了在训练中使用我们的自定义内核，我们需要实现反向传播。通常，这可能比正向传播稍微复杂一些。

1.  我们已将张量类型固定为float32，并将张量形状固定为每个样本256个框。理想情况下，我们希望有一个更健壮的解决方案，支持不同的输入类型和形状。

1.  我们将实验限制在了一种GPU类型上。实际上，我们希望我们的实现能够支持（并且在）多种GPU上进行测试。

1.  我们完全忽视了内核优化的机会——其中一些可能需要比我们在此所展示的更高深的CUDA专业知识。

# 总结

在这篇文章中，我们展示了自定义CUDA内核在AI/ML应用程序运行时性能中的潜力。我们特别尝试利用CUDA提供的低级控制，介绍条件流，以限制动态形状输入情况下冗余算术操作的数量。尽管通过融合多个内核操作带来的性能提升显著，但我们发现内核的大小太小，无法从条件执行流中获益。

在我们许多的文章中，我们一直强调拥有多种工具和技术来优化机器学习（ML）并降低其成本的重要性。自定义内核开发是我们可用的最强大技术之一。然而，对于许多AI/ML工程师来说，它也是最具挑战性的技术之一。我们希望已经成功地说服你，这个机会对于任何ML开发者来说都是触手可及的，并且它并不需要对CUDA有深度的专业化理解。

近年来，引入了新的框架，旨在使AI/ML开发人员更容易进行自定义内核开发和优化。其中最受欢迎的框架之一是[Triton](https://triton-lang.org/main/index.html)。在我们的[下一篇文章](https://chaimrand.medium.com/unleashing-the-power-of-triton-mastering-gpu-kernel-optimization-in-python-160a3f52701e)中，我们将继续探讨自定义内核开发的主题，评估开发Triton内核的能力和潜在影响。
