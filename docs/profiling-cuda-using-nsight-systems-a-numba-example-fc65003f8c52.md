# 使用Nsight Systems进行CUDA性能分析：一个Numba示例

> 原文：[https://towardsdatascience.com/profiling-cuda-using-nsight-systems-a-numba-example-fc65003f8c52?source=collection_archive---------4-----------------------#2024-05-22](https://towardsdatascience.com/profiling-cuda-using-nsight-systems-a-numba-example-fc65003f8c52?source=collection_archive---------4-----------------------#2024-05-22)

## 通过检查并发和并行的Numba CUDA代码，在Nsight Systems中了解性能分析

[](https://medium.com/@cdacostaf?source=post_page---byline--fc65003f8c52--------------------------------)[![Carlos Costa, Ph.D.](../Images/fc5e03e455f11b963086355fe0ccc077.png)](https://medium.com/@cdacostaf?source=post_page---byline--fc65003f8c52--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc65003f8c52--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc65003f8c52--------------------------------) [Carlos Costa, Ph.D.](https://medium.com/@cdacostaf?source=post_page---byline--fc65003f8c52--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc65003f8c52--------------------------------) ·14分钟阅读·2024年5月22日

--

# 引言

优化是编写高性能代码的重要部分，无论你是在编写一个网络服务器，还是在开发计算流体力学模拟软件。性能分析使你能够对代码做出明智的决策。从某种意义上说，没有性能分析的优化就像盲目飞行：对于经验丰富的专业人员来说，这种方式大多可以应付，因为他们拥有专业知识和精确的直觉，但对于大多数人来说，这是灾难的配方。

![](../Images/d423edacc4ce187efb9a0e90523049ca.png)

图片由[Rafa Sanfilippo](https://unsplash.com/@rafasanfilippo?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 本教程内容

在我之前的系列《CUDA by Numba 示例》（参见第[1部分](https://colab.research.google.com/drive/1h0Savk8HSIgraT61burXQwbEUDMz4HT6?usp=sharing)，[2部分](https://colab.research.google.com/drive/1GkGLDexnYUnl2ilmwNxAlWAH6Eo5ZK2f?usp=sharing)，[3部分](https://colab.research.google.com/drive/1iRUQUiHUVdl3jlKzKucxQHQdDPElPb3M?usp=sharing)，和[4部分](https://colab.research.google.com/drive/1Eq1Xyuq8hJ440ma_9OBrEVdGm3bIyigt?usp=sharing)）之后，我们将学习未优化的单流代码与使用流并发和其他优化的略好版本之间的对比。我们将从零开始学习如何使用[NVIDIA Nsight Systems](https://developer.nvidia.com/nsight-systems)对CUDA代码进行分析和性能分析。本教程中的所有代码也可以在仓库[cako/profiling-cuda-nsight-systems](https://github.com/cako/profiling-cuda-nsight-systems/)中找到。

# Nsight Systems

NVIDIA 推荐遵循 [APOD 框架](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#assess-parallelize-optimize-deploy)（*评估、并行化、优化、部署*）作为最佳实践。市面上有多种专有、开源、免费的以及商业化的软件，用于不同类型的评估和分析。资深的 Python 用户可能熟悉一些基本的分析器，比如 `cProfile`、`[line_profiler](https://kernprof.readthedocs.io/en/latest/)`、`memory_profiler`（遗憾的是，从 2024 年开始不再维护）以及更先进的工具，如 [PyInstrument](https://pyinstrument.readthedocs.io/en/latest/) 和 [Memray](https://bloomberg.github.io/memray/)。这些分析器主要针对 "主机"（如 CPU 和内存）的不同方面。

然而，分析 "设备"（例如 GPU）代码及其与主机的交互，需要设备供应商提供的专业工具。对于 NVIDIA 的 GPU，可以使用 Nsight Systems、Nsight Compute 和 Nsight Graphics 来分析计算的不同方面。在本教程中，我们将重点使用 Nsight Systems，它是一个系统级的分析工具。我们将使用它来分析通过 Numba CUDA 与 GPU 交互的 Python 代码。

要开始使用，你需要安装 Nsight Systems 的 CLI 和 GUI。CLI 可以单独安装，并用于在支持 GPGPU 的系统上分析代码。完整版包括 CLI 和 GUI。请注意，即使系统没有 GPU，也可以安装这两个版本。你可以从 [NVIDIA 网站](https://developer.nvidia.com/nsight-systems/get-started)下载所需的版本。

为了更方便地在图形界面中可视化代码部分，NVIDIA 还提供了可以通过 Python 的 `pip` 和 `conda` 安装的库 `[nvtx](https://nvtx.readthedocs.io/en/latest/)`，我们将使用它来标注代码的各个部分。稍后会详细介绍。

# 配置一切：一个简单的例子

在本节中，我们将设置开发和分析环境。以下是两个非常简单的 Python 脚本：`kernels.py` 和 `run_v1.py`。前者将包含所有 CUDA 内核，后者将作为运行示例的入口点。在这个例子中，我们遵循文章 [*CUDA by Numba Examples Part 3: Streams and Events*](/cuda-by-numba-examples-7652412af1ee) 中介绍的 "reduce" 模式来计算一个数组的总和。

```py
#%%writefile kernels.py
import numba
from numba import cuda

THREADS_PER_BLOCK = 256
BLOCKS_PER_GRID = 32 * 40

@cuda.jit
def partial_reduce(array, partial_reduction):
    i_start = cuda.grid(1)
    threads_per_grid = cuda.blockDim.x * cuda.gridDim.x
    s_thread = numba.float32(0.0)
    for i_arr in range(i_start, array.size, threads_per_grid):
        s_thread += array[i_arr]

    s_block = cuda.shared.array((THREADS_PER_BLOCK,), numba.float32)
    tid = cuda.threadIdx.x
    s_block[tid] = s_thread
    cuda.syncthreads()

    i = cuda.blockDim.x // 2
    while i > 0:
        if tid < i:
            s_block[tid] += s_block[tid + i]
        cuda.syncthreads()
        i //= 2

    if tid == 0:
        partial_reduction[cuda.blockIdx.x] = s_block[0]

@cuda.jit
def single_thread_sum(partial_reduction, sum):
    sum[0] = numba.float32(0.0)
    for element in partial_reduction:
        sum[0] += element

@cuda.jit
def divide_by(array, val_array):
    i_start = cuda.grid(1)
    threads_per_grid = cuda.gridsize(1)
    for i in range(i_start, array.size, threads_per_grid):
        array[i] /= val_array[0]
```

```py
#%%writefile run_v1.py
import argparse
import warnings

import numpy as np
from numba import cuda
from numba.core.errors import NumbaPerformanceWarning

from kernels import (
    BLOCKS_PER_GRID,
    THREADS_PER_BLOCK,
    divide_by,
    partial_reduce,
    single_thread_sum,
)

# Ignore NumbaPerformanceWarning
warnings.simplefilter("ignore", category=NumbaPerformanceWarning)

def run(size):
    # Define host array
    a = np.ones(size, dtype=np.float32)
    print(f"Old sum: {a.sum():.3f}")

    # Array copy to device and array creation on the device.
    dev_a = cuda.to_device(a)
    dev_a_reduce = cuda.device_array((BLOCKS_PER_GRID,), dtype=dev_a.dtype)
    dev_a_sum = cuda.device_array((1,), dtype=dev_a.dtype)

    # Launching kernels to normalize array
    partial_reduce[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_reduce)
    single_thread_sum[1, 1](dev_a_reduce, dev_a_sum)
    divide_by[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_sum)

    # Array copy to host
    dev_a.copy_to_host(a)
    cuda.synchronize()
    print(f"New sum: {a.sum():.3f}")

def main():
    parser = argparse.ArgumentParser(description="Simple Example v1")
    parser.add_argument(
        "-n",
        "--array-size",
        type=int,
        default=100_000_000,
        metavar="N",
        help="Array size",
    )

    args = parser.parse_args()
    run(size=args.array_size)

if __name__ == "__main__":
    main()
```

这是一个简单的脚本，可以通过以下命令运行：

```py
$ python run_v1.py
Old sum: 100000000.000
New sum: 1.000
```

我们还会通过分析器运行此代码，这只需要在调用脚本之前使用一些选项调用 `nsys`：

```py
$ nsys profile \
  --trace cuda,osrt,nvtx \
  --gpu-metrics-device=all \
  --cuda-memory-usage true \
  --force-overwrite true \
  --output profile_run_v1 \
  python run_v1.py
GPU 0: General Metrics for NVIDIA TU10x (any frequency)
Old sum: 100000000.000
New sum: 1.000
Generating '/tmp/nsys-report-fb78.qdstrm'
[1/1] [========================100%] profile_run_v1.nsys-rep
Generated:
    /content/profile_run_v1.nsys-rep
```

你可以参考 [Nsight CLI 文档](https://docs.nvidia.com/nsight-systems/UserGuide/index.html)，了解 `nsys` CLI 所有可用的选项。在本教程中，我们将始终使用上述选项。现在我们来剖析一下这个命令：

+   `profile` 将 `nsys` 设置为分析模式。还有其他多种模式，如 `export` 和 `launch`。

+   `--trace cuda,osrt,nvtx` 确保我们“监听”所有CUDA调用（`cuda`）、操作系统运行时库调用（`osrt`）和 `nvtx` 注释（此示例中没有）。还有许多其他追踪选项，如 `cublas`、`cudnn`、`mpi`、`dx11` 等。有关所有选项，请查阅[文档](https://docs.nvidia.com/nsight-systems/UserGuide/index.html)。

+   `--gpu-metrics-device=all` 记录所有GPU的GPU指标，包括[Tensor Core](https://www.nvidia.com/en-us/data-center/tensor-cores/)的使用情况。

+   `--cuda-memory-usage` 跟踪内核的GPU内存使用情况。它可能显著减慢执行速度，并且需要 `--trace=cuda`。我们使用它是因为我们的脚本反正运行得很快。

# 浏览Nsight Systems图形界面

如果命令成功退出，我们将在当前文件夹中看到一个 `profile_run_v1.nsys-rep` 文件。我们将通过启动Nsight Systems图形界面，点击 `File > Open` 打开此文件。初始视图可能有些混乱。因此，我们将首先进行清理：将 `Events View` 窗口调整到底部，最小化 `CPU`、`GPU` 和 `Processes` 下的 `Timeline View` 窗口。现在只展开 `Processes > python > CUDA HW`。请参见图1a和图1b。

![](../Images/66267f5fd85f7b4f42ce705216edc596.png)

图1a：打开nsys报告并清理界面。图片来源：原创作品。CC BY-SA 4.0。

![](../Images/a0df7779681087a99fe5f56d8a325ed4.png)

图1b：nsys报告显示主机到设备内存操作（绿色）、设备到主机内存操作（红色）和CUDA内核（蓝色）。图片来源：原创作品。CC BY-SA 4.0。

首先，让我们找到我们的内核。在 `CUDA HW` 行中，你会看到绿色和红色的块，以及非常小的浅蓝色条形（见图1b）。如果你将鼠标悬停在这些位置，你会看到工具提示，红色和绿色显示“CUDA内存操作进行中”，浅蓝色显示“CUDA内核正在运行（89.7%）”。这些将是我们分析的重点。在这一行中，我们将能够看到内存何时以及如何被传输（红色和绿色），以及我们的内核何时以及如何运行（浅蓝色）。

让我们进一步分析一下我们的内核。你应该能看到三个非常小的蓝色条形，每个代表一个内核调用。我们将通过点击并拖动鼠标，从第一个内核调用开始前一直拖到最后一个内核调用结束后，然后按下 Shift + Z 来放大该区域。请参见图2。

![](../Images/6032ed5cc1bbb9414714374c4665e801.png)

图2：浏览nsys报告并放大感兴趣区域。图片来源：原创作品。CC BY-SA 4.0。

现在我们已经找到了我们的内核，接下来让我们看看一些指标。我们打开`GPU > GPU Metrics`标签页。 在这个面板中，可以找到计算内核的“Warp占用率”（米色）。优化CUDA代码的一种方法是确保warp占用率尽可能长时间接近100%。这意味着我们的GPU不会空闲。我们注意到这是第一个和最后一个内核的情况，但中间的内核则没有。这是预期中的情况，因为中间的内核启动了一个线程。最后要注意的是`GPU > GPU Metrics > SMs Active > Tensor Active / FP16 Active`这一行。这一行将显示是否正在使用张量核心。在这种情况下，你应该确认它们没有被使用。

现在，让我们简要查看一下事件视图。右键点击`Processes > python > CUDA HW`并选择“在事件视图中显示”。然后按持续时间降序排列事件。在图3中，我们看到最慢的事件是两个可分页内存传输。我们在[*CUDA by Numba Examples Part 3: Streams and Events*](/cuda-by-numba-examples-7652412af1ee)中看到，可分页内存传输可能效率较低，我们应该更倾向于使用页面锁定或“固定”内存传输。如果由于使用了可分页内存而导致内存传输变慢，事件视图是一个非常好的地方来识别这些慢速传输的位置。小贴士：你可以通过右键点击`Processes > python > CUDA HW > XX% Memory`来隔离内存传输。

![](../Images/1c760b926c35e13ae7463edf08b32a3f.png)

图3. Nsight Systems中的事件视图显示了一个可分页（非固定）内存传输。图片来源：原创作品。CC BY-SA 4.0。

在本节中，我们学习了如何分析使用CUDA的Python程序，并且如何在Nsight Systems GUI中可视化该程序的基本信息。我们还注意到，在这个简单的程序中，我们使用的是可分页内存而不是固定内存，某个内核并没有占满所有warp，GPU在运行内核之间有相当长的空闲时间，而且我们没有使用张量核心。

# 使用NVTX进行注解

在本节中，我们将学习如何通过在Nsight Systems中使用NVTX进行注解来改进我们的性能分析体验。NVTX允许我们标记代码中的不同区域。它可以标记范围和瞬时事件。要深入了解，请查看[文档](https://nvtx.readthedocs.io/en/latest/index.html)。以下是我们创建的`run_v2.py`，除了注解`run_v1.py`外，还修改了这一行：

```py
a = np.ones(size, dtype=np.float32)
```

改为：

```py
a = cuda.pinned_array(size, dtype=np.float32)
a[...] = 1.0
```

因此，除了注释之外，我们现在还使用了固定内存。如果你想了解更多关于CUDA支持的不同类型内存的信息，请参见[CUDA C++ 编程指南](https://developer.nvidia.com/blog/how-optimize-data-transfers-cuda-cc/)。需要注意的是，这并不是在Numba中固定数组的唯一方式。一个先前创建的Numpy数组也可以使用上下文创建，详情请参见[Numba 文档](https://numba.readthedocs.io/en/stable/cuda/memory.html#pinned-memory)。

```py
#%%writefile run_v2.py
import argparse
import warnings

import numpy as np
import nvtx
from numba import cuda
from numba.core.errors import NumbaPerformanceWarning

from kernels import (
    BLOCKS_PER_GRID,
    THREADS_PER_BLOCK,
    divide_by,
    partial_reduce,
    single_thread_sum,
)

# Ignore NumbaPerformanceWarning
warnings.simplefilter("ignore", category=NumbaPerformanceWarning)

def run(size):
    with nvtx.annotate("Compilation", color="red"):
        dev_a = cuda.device_array((BLOCKS_PER_GRID,), dtype=np.float32)
        dev_a_reduce = cuda.device_array((BLOCKS_PER_GRID,), dtype=dev_a.dtype)
        dev_a_sum = cuda.device_array((1,), dtype=dev_a.dtype)
        partial_reduce[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_reduce)
        single_thread_sum[1, 1](dev_a_reduce, dev_a_sum)
        divide_by[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_sum)

    # Define host array
    a = cuda.pinned_array(size, dtype=np.float32)
    a[...]  = 1.0
    print(f"Old sum: {a.sum():.3f}")

    # Array copy to device and array creation on the device.
    with nvtx.annotate("H2D Memory", color="yellow"):
        dev_a = cuda.to_device(a)
        dev_a_reduce = cuda.device_array((BLOCKS_PER_GRID,), dtype=dev_a.dtype)
        dev_a_sum = cuda.device_array((1,), dtype=dev_a.dtype)

    # Launching kernels to normalize array
    with nvtx.annotate("Kernels", color="green"):
        partial_reduce[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_reduce)
        single_thread_sum[1, 1](dev_a_reduce, dev_a_sum)
        divide_by[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_sum)

    # Array copy to host
    with nvtx.annotate("D2H Memory", color="orange"):
        dev_a.copy_to_host(a)
    cuda.synchronize()
    print(f"New sum: {a.sum():.3f}")

def main():
    parser = argparse.ArgumentParser(description="Simple Example v2")
    parser.add_argument(
        "-n",
        "--array-size",
        type=int,
        default=100_000_000,
        metavar="N",
        help="Array size",
    )

    args = parser.parse_args()
    run(size=args.array_size)

if __name__ == "__main__":
    main()
```

比较这两个文件，你可以看到，只需用一些GPU内核调用进行封装就能实现。

```py
with nvtx.annotate("Region Title", color="red"):
    ...
```

专业提示：你还可以通过在函数定义上方添加`@nvtx.annotate`装饰器来注释函数，或者通过使用`python -m nvtx run_v2.py`命令自动注释所有内容，或者在代码中选择性地启用或禁用`nvtx.Profile()`来应用自动注释器。详细信息请参见[文档](https://nvtx.readthedocs.io/en/latest/index.html)！

让我们运行这个新脚本，并在 Nsight Systems 中查看结果。

```py
$ nsys profile \
  --trace cuda,osrt,nvtx \
  --gpu-metrics-device=all \
  --cuda-memory-usage true \
  --force-overwrite true \
  --output profile_run_v2 \
  python run_v2.py
GPU 0: General Metrics for NVIDIA TU10x (any frequency)
Old sum: 100000000.000
New sum: 1.000
Generating '/tmp/nsys-report-69ab.qdstrm'
[1/1] [========================100%] profile_run_v2.nsys-rep
Generated:
    /content/profile_run_v2.nsys-rep
```

再次，我们从最小化所有内容开始，只保留`Processes > python > CUDA HW`打开。参见图 4\. 注意我们现在有了一条新线，`NVTX`。在时间轴窗口的这一行，我们应该能看到不同颜色的块，这些块对应于我们在代码中创建的注释区域。这些区域包括`Compilation`、`H2D Memory`、`Kernels`和`D2H Memory`。其中一些可能太小无法阅读，但如果你放大区域，它们将变得清晰可见。

![](../Images/5d39f2812209ec8b24bb77d6c9c10a41.png)

图 4\. NVTX 注释示例和带有固定内存的事件视图。图片来源：自作，CC BY-SA 4.0。

性能分析器确认该内存已被固定，确保我们的代码确实在使用固定内存。此外，`H2D Memory`和`D2H Memory`现在的时间不到之前的一半。一般来说，使用固定内存或预取的映射数组（Numba不支持）通常会获得更好的性能。

# 流并发性

现在我们将调查是否可以通过引入流来改进此代码。其思想是，在进行内存传输的同时，GPU可以开始处理数据。这允许一定程度的并发性，理论上将确保我们尽可能充分地利用warps。

![](../Images/1bbde1985c738ae6d4cc600f1aa61ad7.png)

图 5\. 使用不同流可能允许并发执行。图片来源：[Zhang et al. 2021](https://www.mdpi.com/1045598)（CC BY 4.0）。

在下面的代码中，我们将把数组的处理分成大致相等的部分。每个部分将在单独的流中运行，包括数据传输和数组求和。然后，我们同步所有流并求和它们的部分和。此时，我们可以为每个流独立启动归一化内核。

我们想回答以下几个问题：

1.  下面的代码真的会创建并发吗？我们会引入一个bug吗？

1.  它比使用单一流的代码更快吗？

1.  warp占用率更好吗？

```py
#%%writefile run_v3_bug.py
import argparse
import warnings
from math import ceil

import numpy as np
import nvtx
from numba import cuda
from numba.core.errors import NumbaPerformanceWarning

from kernels import (
    BLOCKS_PER_GRID,
    THREADS_PER_BLOCK,
    divide_by,
    partial_reduce,
    single_thread_sum,
)

# Ignore NumbaPerformanceWarning
warnings.simplefilter("ignore", category=NumbaPerformanceWarning)

def run(size, nstreams):
    with nvtx.annotate("Compilation", color="red"):
        dev_a = cuda.device_array((BLOCKS_PER_GRID,), dtype=np.float32)
        dev_a_reduce = cuda.device_array((BLOCKS_PER_GRID,), dtype=dev_a.dtype)
        dev_a_sum = cuda.device_array((1,), dtype=dev_a.dtype)
        partial_reduce[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_reduce)
        single_thread_sum[1, 1](dev_a_reduce, dev_a_sum)
        divide_by[BLOCKS_PER_GRID, THREADS_PER_BLOCK](dev_a, dev_a_sum)

    # Define host array
    a = cuda.pinned_array(size, dtype=np.float32)
    a[...] = 1.0

    # Define regions for streams
    step = ceil(size / nstreams)
    starts = [i * step for i in range(nstreams)]
    ends = [min(s + step, size) for s in starts]
    print(f"Old sum: {a.sum():.3f}")

    # Create streams
    streams = [cuda.stream()] * nstreams

    cpu_sums = [cuda.pinned_array(1, dtype=np.float32) for _ in range(nstreams)]
    devs_a = []
    with cuda.defer_cleanup():
        for i, (stream, start, end) in enumerate(zip(streams, starts, ends)):
            cpu_sums[i][...] = np.nan

            # Array copy to device and array creation on the device.
            with nvtx.annotate(f"H2D Memory Stream {i}", color="yellow"):
                dev_a = cuda.to_device(a[start:end], stream=stream)
                dev_a_reduce = cuda.device_array(
                    (BLOCKS_PER_GRID,), dtype=dev_a.dtype, stream=stream
                )
                dev_a_sum = cuda.device_array((1,), dtype=dev_a.dtype, stream=stream)
            devs_a.append(dev_a)

            # Launching kernels to sum array
            with nvtx.annotate(f"Sum Kernels Stream {i}", color="green"):
                for _ in range(50):  # Make it spend more time in compute
                    partial_reduce[BLOCKS_PER_GRID, THREADS_PER_BLOCK, stream](
                    dev_a, dev_a_reduce
                )
                    single_thread_sum[1, 1, stream](dev_a_reduce, dev_a_sum)
            with nvtx.annotate(f"D2H Memory Stream {i}", color="orange"):
                dev_a_sum.copy_to_host(cpu_sums[i], stream=stream)

        # Ensure all streams are caught up
        cuda.synchronize()

        # Aggregate all 1D arrays into a single 1D array
        a_sum_all = sum(cpu_sums)

        # Send it to the GPU
        with cuda.pinned(a_sum_all):
            with nvtx.annotate("D2H Memory Default Stream", color="orange"):
                dev_a_sum_all = cuda.to_device(a_sum_all)

        # Normalize via streams
        for i, (stream, start, end, dev_a) in enumerate(
            zip(streams, starts, ends, devs_a)
        ):
            with nvtx.annotate(f"Divide Kernel Stream {i}", color="green"):
                divide_by[BLOCKS_PER_GRID, THREADS_PER_BLOCK, stream](
                    dev_a, dev_a_sum_all
                )

            # Array copy to host
            with nvtx.annotate(f"D2H Memory Stream {i}", color="orange"):
                dev_a.copy_to_host(a[start:end], stream=stream)

        cuda.synchronize()
        print(f"New sum: {a.sum():.3f}")

def main():
    parser = argparse.ArgumentParser(description="Simple Example v3")
    parser.add_argument(
        "-n",
        "--array-size",
        type=int,
        default=100_000_000,
        metavar="N",
        help="Array size",
    )
    parser.add_argument(
        "-s",
        "--streams",
        type=int,
        default=4,
        metavar="N",
        help="Array size",
    )

    args = parser.parse_args()
    run(size=args.array_size, nstreams=args.streams)

if __name__ == "__main__":
    main()
```

让我们运行代码并收集结果。

```py
$ nsys profile \
  --trace cuda,osrt,nvtx \
  --gpu-metrics-device=all \
  --cuda-memory-usage true \
  --force-overwrite true \
  --output profile_run_v3_bug_4streams \
  python run_v3_bug.py -s 4
GPU 0: General Metrics for NVIDIA TU10x (any frequency)
Old sum: 100000000.000
New sum: 1.000
Generating '/tmp/nsys-report-a666.qdstrm'
[1/1] [========================100%] profile_run_v3_bug_4streams.nsys-rep
Generated:
    /content/profile_run_v3_bug_4streams.nsys-rep
```

程序运行并得出了正确的答案。但是当我们打开分析文件时（见图6），我们注意到只有两个流，而不是四个！而且其中一个几乎完全闲置！这是怎么回事？

![](../Images/0fb44b9158a16628a582cda7e564b1b3.png)

图6. 错误的多流代码示例。来源：自主创作。CC BY-SA 4.0。

在流的创建过程中存在一个错误。通过以下操作

```py
streams = [cuda.stream()] * nstreams
```

我们实际上是创建了一个流，并将其重复`nstreams`次。那么为什么我们看到的是两个流，而不是一个流呢？事实上，其中一个没有做多少计算应该是一个信号，表明有一个流我们并没有使用。这个流是默认流，我们在代码中完全没有使用它，因为所有GPU交互都指定了一个流，即我们创建的流。

我们可以通过以下方式修复此错误：

```py
streams = [cuda.stream() for _ in range(nstreams)]
# Ensure they are all different
assert all(s1.handle != s2.handle for s1, s2 in zip(streams[:-1], streams[1:]))
```

上面的代码还将确保它们真的是不同的流，因此，如果代码中存在错误，它也能被捕捉到。它通过检查流指针值来实现这一点。

现在我们可以分别用1个流和8个流运行修复后的代码进行比较。分别见图7和图8。

```py
$  nsys profile \
  --trace cuda,osrt,nvtx \
  --gpu-metrics-device=all \
  --cuda-memory-usage true \
  --force-overwrite true \
  --output profile_run_v3_1stream \
  python run_v3.py -s 1
GPU 0: General Metrics for NVIDIA TU10x (any frequency)
Old sum: 100000000.000
New sum: 1.000
Generating '/tmp/nsys-report-de65.qdstrm'
[1/1] [========================100%] profile_run_v3_1stream.nsys-rep
Generated:
    /content/profile_run_v3_1stream.nsys-rep
```

```py
$ nsys profile \
  --trace cuda,osrt,nvtx \
  --gpu-metrics-device=all \
  --cuda-memory-usage true \
  --force-overwrite true \
  --output profile_run_v3_8streams \
  python run_v3.py -s 8
GPU 0: General Metrics for NVIDIA TU10x (any frequency)
Old sum: 100000000.000
New sum: 1.000
Generating '/tmp/nsys-report-1fb7.qdstrm'
[1/1] [========================100%] profile_run_v3_8streams.nsys-rep
Generated:
    /content/profile_run_v3_8streams.nsys-rep
```

![](../Images/7da8e8b11bc79e7ca948e97a21759fb7.png)

图7. 单流代码示例。来源：自主创作。CC BY-SA 4.0。

![](../Images/2da5d025d8a9e310ce1b4eb55ddfd113.png)

图7. 正确的多流代码示例。来源：自主创作。CC BY-SA 4.0。

再次说明，两者都给出了正确的结果。通过打开带有8个流的那个文件，我们看到，确实，错误已经被修复（见图7）。实际上，现在我们看到了9个流（8个创建的流+默认流）。此外，我们还看到它们同时工作！因此，我们实现了并发！

不幸的是，如果我们再深入一点，就会发现并发代码不一定更快。在我的机器上，无论是哪个版本的代码，从内存传输开始到最后的GPU-CPU拷贝，都大约需要160毫秒。

一个可能的罪魁祸首是warp占用率。我们注意到，在单流版本中，warp占用率显著更好。在这个例子中，我们在计算上获得的增益可能因为没有高效利用GPU而被浪费掉。这可能与代码结构有关，该结构（人为地）调用了太多的内核。此外，如果所有线程都由一个流填充，则并发性没有增益，因为其他流必须等到资源释放后才能开始工作。

这个例子很重要，因为它表明我们对于性能的先入之见仅仅是假设，必须经过验证。

在 APOD 的这个阶段，我们已经完成了评估和并行化（通过线程和并发），因此下一步将是部署。我们还注意到并发导致了轻微的性能回退，因此在这个例子中，单流版本可能是最适合部署的。在生产环境中，下一步将是找到最适合并行化的代码段并重新启动 APOD。

# 结论

在本文中，我们介绍了如何设置、使用以及解读在 NVIDIA Nsight Systems 中对 Python 代码的性能分析结果。C 和 C++ 代码的分析方法非常相似，实际上大多数相关资料都使用了 C 和 C++ 示例。

我们还展示了性能分析如何帮助我们捕捉 bug 和对程序进行性能测试，确保我们引入的新功能确实能够提高性能，如果没有提高，也能找出原因。
