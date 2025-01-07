# 在 TPU 上实现顺序算法

> 原文：[https://towardsdatascience.com/implementing-sequential-algorithms-on-tpu-41d75c6aaa95?source=collection_archive---------8-----------------------#2024-10-07](https://towardsdatascience.com/implementing-sequential-algorithms-on-tpu-41d75c6aaa95?source=collection_archive---------8-----------------------#2024-10-07)

## 加速 AI/ML 模型训练与自定义运算符 — 第 3.A 部分

[](https://chaimrand.medium.com/?source=post_page---byline--41d75c6aaa95--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--41d75c6aaa95--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--41d75c6aaa95--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--41d75c6aaa95--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--41d75c6aaa95--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--41d75c6aaa95--------------------------------) ·阅读时间 11 分钟·2024 年 10 月 7 日

--

![](../Images/a2daa59de127264e6b8d93713b38f15d.png)

图片来源：[Bernd Dittrich](https://unsplash.com/@hdbernd?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是关于使用 [Pallas](https://jax.readthedocs.io/en/latest/pallas/index.html) 实现自定义 TPU 运算的主题的直接续篇，前文可见 [上一篇文章](https://chaimrand.medium.com/the-rise-of-pallas-unlocking-tpu-potential-with-custom-kernels-67be10ab846a)。特别感兴趣的是那些利用 TPU 架构独特特性，优化运行时性能的自定义内核。本文将尝试通过应用 Pallas 的强大功能，展示如何在主要可并行化的深度学习 (DL) 工作负载中，处理穿插其中的顺序算法问题。

我们将以[非最大抑制 (NMS)](https://learnopencv.com/non-maximum-suppression-theory-and-implementation-in-pytorch/)的边界框提议作为代表性算法，并探讨如何优化其实现。NMS是计算机视觉（CV）[目标检测](https://en.wikipedia.org/wiki/Object_detection)解决方案中的一个重要组成部分（例如，[Mask RCNN](https://arxiv.org/abs/1703.06870)），通常用于筛选重叠的边界框，只保留“最佳”框。NMS接收一组边界框提议、一组关联的得分列表，以及一个[IOU](https://en.wikipedia.org/wiki/IOU)阈值，然后*贪婪地*和*迭代地*选择剩余得分最高的框，并排除所有与其IOU超过给定阈值的其他框。选择的框在第*n*次迭代中依赖于前*n-1*次算法步骤，这决定了其实现的顺序性。有关NMS及其实现的更多信息，请参见[此处](https://learnopencv.com/non-maximum-suppression-theory-and-implementation-in-pytorch/)和/或[此处](https://medium.com/analytics-vidhya/non-max-suppression-nms-6623e6572536)。尽管我们选择专注于一个特定的算法，但我们的大部分讨论应适用于其他顺序算法。

## 将顺序算法卸载到CPU

在主要可并行化的机器学习模型（例如，Mask R-CNN）中存在顺序算法，提出了一个有趣的挑战。尽管GPU通常用于这种工作负载，擅长执行并行操作（如矩阵乘法），但在处理顺序算法时，它们的表现往往远不如CPU。这通常导致计算图中存在GPU和CPU之间的交叉点，其中GPU处理并行操作，而CPU处理顺序操作。NMS就是一个常被卸载到CPU的顺序算法的典型例子。事实上，仔细分析[torchvision](https://pytorch.org/vision/stable/index.html)的“CUDA”实现中的[NMS](https://github.com/pytorch/vision/blob/v0.19.1/torchvision/csrc/ops/cuda/nms_kernel.cu)会发现，它甚至将算法的一个重要部分运行在[CPU](https://github.com/pytorch/vision/blob/v0.19.1/torchvision/csrc/ops/cuda/nms_kernel.cu#L136C33-L136C41)上。

尽管将顺序操作卸载到CPU可能会提高运行时性能，但也有几个潜在的缺点需要考虑：

1.  CPU和GPU之间的跨设备执行通常需要多个同步点，这通常导致GPU在等待CPU完成任务时出现空闲时间。考虑到GPU通常是训练平台中最昂贵的组件，我们的目标是最小化这种空闲时间。

1.  在标准的 ML 工作流程中，CPU 负责准备并将数据传递给模型，而模型则位于 GPU 上。如果数据输入管道涉及计算密集型处理，这可能会给 CPU 带来压力，导致 GPU 出现“输入饥饿”现象。在这种情况下，将模型的一部分计算卸载到 CPU 上可能会进一步加剧这个问题。

为了避免这些缺点，您可以考虑替代方法，比如用一个可比较的替代方法（例如，[这里](https://hertasecurity.com/wp-content/uploads/work-efficient-parallel-non-maximum-suppression.pdf) 提出的方案）替换顺序算法，或者选择一个较慢/次优的 GPU 实现顺序算法，或者将工作负载运行在 CPU 上——每种方法都有其潜在的折衷。

## TPU 上的顺序算法

这正是 TPU 独特架构可能带来机会的地方。与 GPU 相反，TPU 是顺序处理器。尽管它们在执行高度矢量化的操作时，由于其能够进行矩阵乘法等并行操作，因此在运行可以并行化的操作时与 GPU 竞争，但它们的顺序特性使其在运行包含顺序和并行组件混合的 ML 工作负载时，可能具有独特的优势。借助 [Pallas 扩展](https://jax.readthedocs.io/en/latest/pallas/index.html) 和我们的 [新型 TPU 核心创建工具](https://chaimrand.medium.com/the-rise-of-pallas-unlocking-tpu-potential-with-custom-kernels-67be10ab846a)，我们将通过实现和评估一个自定义的 TPU 上的 NMS 实现来评估这一机会。

## 免责声明

以下我们分享的 NMS 实现仅供演示使用。我们并没有做出任何显著努力来优化它们，或者验证它们的鲁棒性、耐用性或准确性。请记住，截至本文写作时，Pallas 仍然是一个 *实验性* 功能——仍在积极开发中。我们分享的代码（基于 [JAX](https://pypi.org/project/jax/) 版本 0.4.32）可能在您阅读时已经过时。请务必参考最新的 API 和资源，供您进行 Pallas 开发使用。请不要将我们提到的任何算法、库或 API 视为对其使用的推荐。

# CPU 上的 NMS

我们从一个简单的 [numpy](https://numpy.org/) 实现的 NMS 开始，它将作为性能比较的基准：

```py
import numpy as np

def nms_cpu(boxes, scores, max_output_size, threshold=0.1):
    epsilon = 1e-5

    # Convert bounding boxes and scores to numpy
    boxes = np.array(boxes)
    scores = np.array(scores)

    # coordinates of bounding boxes
    start_x = boxes[:, 0]
    start_y = boxes[:, 1]
    end_x = boxes[:, 2]
    end_y = boxes[:, 3]

    # Compute areas of bounding boxes
    areas = (end_x - start_x) * (end_y - start_y)

    # Sort by confidence score of bounding boxes
    order = np.argsort(scores)

    # Picked bounding boxes
    picked_boxes = []

    # Iterate over bounding boxes
    while order.size > 0 and len(picked_boxes) < max_output_size:

        # The index of the remaining box with the highest score
        index = order[-1]

        # Pick the bounding box with largest confidence score
        picked_boxes.append(index.item())

        # Compute coordinates of intersection
        x1 = np.maximum(start_x[index], start_x[order[:-1]])
        x2 = np.minimum(end_x[index], end_x[order[:-1]])
        y1 = np.maximum(start_y[index], start_y[order[:-1]])
        y2 = np.minimum(end_y[index], end_y[order[:-1]])

        # Compute areas of intersection and union
        w = np.maximum(x2 - x1, 0.0)
        h = np.maximum(y2 - y1, 0.0)

        intersection = w * h
        union = areas[index] + areas[order[:-1]] - intersection

        # Compute the ratio between intersection and union
        ratio = intersection / np.clip(union, min=epsilon)

        # discard boxes above overlap threshold
        keep = np.where(ratio < threshold)
        order = order[keep]

    return picked_boxes
```

为了评估我们的 NMS 功能的性能，我们生成了一批随机框和分数（作为 JAX 张量），并在 [Google Cloud TPU v5e](https://cloud.google.com/tpu/docs/v5e) 系统上运行该脚本，使用与我们 [之前的帖子](https://chaimrand.medium.com/the-rise-of-pallas-unlocking-tpu-potential-with-custom-kernels-67be10ab846a) 中相同的环境和基准测试工具。对于此实验，我们将 CPU 指定为 [JAX 默认设备](https://jax.readthedocs.io/en/latest/_autosummary/jax.default_device.html)：

```py
import jax
from jax import random
import jax.numpy as jnp

def generate_random_boxes(run_on_cpu = False):
    if run_on_cpu:
        jax.config.update('jax_default_device', jax.devices('cpu')[0])
    else:
        jax.config.update('jax_default_device', jax.devices('tpu')[0])

    n_boxes = 1024
    img_size = 1024

    k1, k2, k3 = random.split(random.key(0), 3)

    # Randomly generate box sizes and positions
    box_sizes = random.randint(k1,
                               shape=(n_boxes, 2),
                               minval=1,
                               maxval=img_size)
    top_left = random.randint(k2,
                              shape=(n_boxes, 2),
                              minval=0,
                              maxval=img_size - 1)
    bottom_right = jnp.clip(top_left + box_sizes, 0, img_size - 1)

    # Concatenate top-left and bottom-right coordinates
    rand_boxes = jnp.concatenate((top_left, bottom_right),
                                 axis=1).astype(jnp.bfloat16)
    rand_scores = jax.random.uniform(k3, 
                                     shape=(n_boxes,),
                                     minval=0.0,
                                     maxval=1.0)

    return rand_boxes, rand_scores

rand_boxes, rand_scores = generate_random_boxes(run_on_cpu=True)

time = benchmark(nms_cpu)(rand_boxes, rand_scores, max_output_size=128)
print(f'nms_cpu: {time}')
```

结果的平均运行时间为2.99毫秒。请注意，这里假设输入和输出张量位于CPU上。如果它们位于TPU上，则还应考虑它们在设备之间复制的时间。

# TPU上的NMS

如果我们的NMS函数是一个在TPU上运行的大型计算图中的组件，我们可能更倾向于选择一个TPU兼容的实现，以避免跨设备执行的缺点。下面的代码块包含了一个JAX实现的NMS，专门设计用来通过JIT编译加速。假设框的数量为*N*，我们首先计算*N(N-1)*对框之间的IOU，并准备一个*N*x*N*的布尔张量（*mask_threshold*），其中(*i,j*)位置的值表示框*i*和框*j*之间的IOU是否超过了预定的阈值。

为了简化框的迭代选择，我们创建了一个掩码张量（*mask_threshold2*）的副本，其中对角线元素被置零，以防止框抑制自身。我们进一步定义了两个评分跟踪张量：*out_scores*，它保留已选择框的评分（并将被淘汰框的评分置零），以及*remaining_scores*，它保持仍在考虑中的框的评分。接着，我们使用[jax.lax.while_loop](https://jax.readthedocs.io/en/latest/_autosummary/jax.lax.while_loop.html)函数来迭代地选择框，同时更新*out_scores*和*remaining_scores*张量。请注意，这个函数的输出格式与之前的函数不同，可能需要调整以适应计算图的后续步骤。

```py
import functools

# Given N boxes, calculates mask_threshold an NxN boolean mask
# where the (i,j) entry indicates whether the IOU of boxes i and j
# exceed the threshold. Returns mask_threshold, mask_threshold2
# which is equivalent to mask_threshold with zero diagonal and
# the scores modified so that all values are greater than 0
def init_tensors(boxes, scores, threshold=0.1):
    epsilon = 1e-5

    # Extract left, top, right, bottom coordinates
    left = boxes[:, 0]
    top = boxes[:, 1]
    right = boxes[:, 2]
    bottom = boxes[:, 3]

    # Compute areas of boxes
    areas = (right - left) * (bottom - top)

    # Calculate intersection points
    inter_l = jnp.maximum(left[None, :], left[:, None])
    inter_t = jnp.maximum(top[None, :], top[:, None])
    inter_r = jnp.minimum(right[None, :], right[:, None])
    inter_b = jnp.minimum(bottom[None, :], bottom[:, None])

    # Width, height, and area of the intersection
    inter_w = jnp.clip(inter_r - inter_l, 0)
    inter_h = jnp.clip(inter_b - inter_t, 0)
    inter_area = inter_w * inter_h

    # Union of the areas
    union = areas[None, :] + areas[:, None] - inter_area

    # IoU calculation
    iou = inter_area / jnp.clip(union, epsilon)

    # Shift scores to be greater than zero
    out_scores = scores - jnp.min(scores) + epsilon

    # Create mask based on IoU threshold
    mask_threshold = iou > threshold

    # Create mask excluding diagonal (i.e., self IoU is ignored)
    mask_threshold2 = mask_threshold * (1-jnp.eye(mask_threshold.shape[0],
                                                  dtype=mask_threshold.dtype))

    return mask_threshold, mask_threshold2, out_scores

@functools.partial(jax.jit, static_argnames=['max_output_size', 'threshold'])
def nms_jax(boxes, scores, max_output_size, threshold=0.1):
    # initialize mask and score tensors
    mask_threshold, mask_threshold2, out_scores = init_tensors(boxes,
                                                           scores,
                                                           threshold)

    # The out_scores tensor will retain the scores of the chosen boxes
    # and zero the scores of the eliminated ones
    # remaining_scores will maintain non-zero scores for boxes that
    # have not been chosen or eliminated
    remaining_scores = out_scores.copy()

    def choose_box(state):
        i, remaining_scores, out_scores = state
        # choose index of box with highest score from remaining scores
        index = jnp.argmax(remaining_scores)
        # check validity of chosen box
        valid = remaining_scores[index] > 0
        # If valid, zero all scores with IOU greater than threshold
        # (including the chosen index)
        remaining_scores = jnp.where(mask_threshold[index] *valid,
                                     0,
                                     remaining_scores)
        # zero the scores of the eliminated tensors (not including
        # the chosen index)
        out_scores = jnp.where(mask_threshold2[index]*valid,
                               0,
                               out_scores)

        i = i + 1
        return i, remaining_scores, out_scores

    def cond_fun(state):
        i, _, _ = state
        return (i < max_output_size)

    i = 0
    state = (i, remaining_scores, out_scores)

    _, _, out_scores = jax.lax.while_loop(cond_fun, choose_box, state)

    # Output the resultant scores. To extract the chosen boxes,
    # Take the max_output_size highest scores:
    # min = jnp.minimum(jnp.count_nonzero(scores), max_output_size)
    # indexes = jnp.argsort(out_scores, descending=True)[:min]
    return out_scores

# nms_jax can be run on either the CPU the TPU
rand_boxes, rand_scores = generate_random_boxes(run_on_cpu=True)

time = benchmark(nms_jax)(rand_boxes, rand_scores, max_output_size=128)
print(f'nms_jax on CPU: {time}')

rand_boxes, rand_scores = generate_random_boxes(run_on_cpu=False)

time = benchmark(nms_jax)(rand_boxes, rand_scores, max_output_size=128)
print(f'nms_jax on TPU: {time}')
```

该NMS实现的运行时间在CPU和TPU上分别为1.231毫秒和0.416毫秒。

# 自定义NMS Pallas内核

我们现在介绍一个自定义的NMS实现，在该实现中，我们显式地利用了在TPU上Pallas内核是以[顺序方式](https://jax.readthedocs.io/en/latest/pallas/grid_blockspec.html#grid-a-k-a-kernels-in-a-loop)执行的这一事实。我们的实现使用了两个布尔矩阵掩码和两个评分保持张量，类似于我们前一个函数中的方法。

我们定义了一个内核函数，*choose_box*，负责选择下一个框并更新评分保持张量，这些张量存储在临时内存中。我们在一维网格上调用该内核，其中步数（即网格大小）由*max_output_size*参数确定。

请注意，由于Pallas支持的操作存在一些[限制](https://jax.readthedocs.io/en/latest/pallas/tpu/details.html#supported-operations)（截至本文撰写时），为了实现“argmax”函数和对选中框的有效性检查，需要进行一些技巧性操作。为了简洁起见，我们省略了技术细节，并将感兴趣的读者指向下面代码中的注释部分。

```py
from jax.experimental import pallas as pl
from jax.experimental.pallas import tpu as pltpu

# argmax helper function
def pallas_argmax(scores, n_boxes):
    # we assume that the index of each box is stored in the
    # least significant bits of the score (see below)
    idx = jnp.max(scores.astype(float)).astype(int) % n_boxes
    return idx

# Pallas kernel definition
def choose_box(scores, thresh_mask1, thresh_mask2, ret_scores,
               scores_scratch, remaining_scores_scratch, *, nsteps, n_boxes):
    # initialize scratch memory on first step
    @pl.when(pl.program_id(0) == 0)
    def _():
        scores_scratch[...] = scores[...]
        remaining_scores_scratch[...] = scores[...]

    remaining_scores = remaining_scores_scratch[...]

    # choose box
    idx = pallas_argmax(remaining_scores, n_boxes)

    # we use any to verfiy validity of the chosen box due
    # to limitations on indexing in pallas
    valid = (remaining_scores>0).any()

    # updating score tensors
    remaining_scores_scratch[...] = jnp.where(thresh_mask1[idx,...]*valid,
                                              0,
                                              remaining_scores)
    scores_scratch[...] = jnp.where(thresh_mask2[idx,...]*valid,
                                    0,
                                    scores_scratch[...])

    # set return value on final step
    @pl.when(pl.program_id(0) == nsteps - 1)
    def _():
        ret_scores[...] = scores_scratch[...]

@functools.partial(jax.jit, static_argnames=['max_output_size', 'threshold'])
def nms_pallas(boxes, scores, max_output_size, threshold=0.1):
    n_boxes = scores.size
    mask_threshold, mask_threshold2, scores = init_tensors(boxes, 
                                                           scores,
                                                           threshold)

    # In order to work around the Pallas argsort limitation
    # we create a new scores tensor with the same ordering of
    # the input scores tensor in which the index of each score
    # in the ordering is encoded in the least significant bits
    sorted = jnp.argsort(scores, descending=True)

    # descending integers: n_boxes-1, ..., 2, 1, 0
    descending = jnp.flip(jnp.arange(n_boxes))

    # new scores in descending with the least significant
    # bits carrying the argsort of the input scores
    ordered_scores = n_boxes * descending + sorted

    # new scores with same ordering as input scores
    scores = jnp.empty_like(ordered_scores
                            ).at[sorted].set(ordered_scores)

    grid = (max_output_size,)
    return pl.pallas_call(
        functools.partial(choose_box, 
                          nsteps=max_output_size,
                          n_boxes=n_boxes),
        grid_spec=pltpu.PrefetchScalarGridSpec(
            num_scalar_prefetch=0,
            in_specs=[
                pl.BlockSpec(block_shape=(n_boxes,)),
                pl.BlockSpec(block_shape=(n_boxes, n_boxes)),
                pl.BlockSpec(block_shape=(n_boxes, n_boxes)),
            ],
            out_specs=pl.BlockSpec(block_shape=(n_boxes,)),
            scratch_shapes=[pltpu.VMEM((n_boxes,), scores.dtype),
                            pltpu.VMEM((n_boxes,), scores.dtype)],
            grid=grid,
        ),
        out_shape=jax.ShapeDtypeStruct((n_boxes,), scores.dtype),
        compiler_params=dict(mosaic=dict(
            dimension_semantics=("arbitrary",)))
    )(scores, mask_threshold, mask_threshold2)

rand_boxes, rand_scores = generate_random_boxes(run_on_cpu=False)

time = benchmark(nms_pallas)(rand_boxes, rand_scores, max_output_size=128)
print(f'nms_pallas: {time}')
```

我们的自定义NMS操作符的平均运行时间为0.139毫秒，约为我们JAX原生实现的三倍速度。这一结果突显了根据TPU架构的独特特性定制顺序算法实现的潜力。

请注意，在我们的Pallas内核实现中，我们将完整的输入张量加载到[TPU VMEM内存](https://jax.readthedocs.io/en/latest/pallas/tpu/pipelining.html#constraints-of-using-vmem-smem)中。鉴于VMEM的容量有限，扩大输入大小（即增加边界框数量）可能会导致内存问题。通常，这些限制可以通过使用BlockSpecs对输入进行[分块](https://jax.readthedocs.io/en/latest/pallas/grid_blockspec.html#blockspec-a-k-a-how-to-chunk-up-inputs)来解决。不幸的是，应用这种方法会破坏当前的NMS实现。实现跨输入块的NMS需要不同的设计，这超出了本文的范围。

# 结果

我们实验的结果总结在下面的表格中：

![](../Images/3f06e6fa7106deb64169f9bfcf4f9b70.png)

NMS实验结果（越低越好） — 作者

这些结果展示了在TPU上运行完整的机器学习计算图的潜力，即使它们包含顺序组件。特别是我们的Pallas NMS操作符所展示的性能提升，突显了通过定制内核来利用TPU优势的机会。

# 概要

在我们的[上一篇文章](https://chaimrand.medium.com/the-rise-of-pallas-unlocking-tpu-potential-with-custom-kernels-67be10ab846a)中，我们了解了使用Pallas扩展为JAX构建自定义TPU操作符的机会。最大化这一机会需要根据TPU架构的特性量身定制内核实现。在本文中，我们专注于TPU处理器的顺序特性及其在优化自定义NMS内核中的应用。尽管将该解决方案扩展以支持无限数量的边界框需要进一步的工作，但我们讨论的核心原理依然适用。

Pallas仍处于开发的实验阶段，存在一些限制，可能需要创造性的解决方法。但其强大性和潜力是显而易见的，我们预计随着框架的成熟，这些优势将会进一步增强。
