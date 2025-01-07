# 从并行计算原理到CPU和GPU架构的编程

> 原文：[https://towardsdatascience.com/from-parallel-computing-principles-to-programming-for-cpu-and-gpu-architectures-dd06e1f30586?source=collection_archive---------5-----------------------#2024-11-12](https://towardsdatascience.com/from-parallel-computing-principles-to-programming-for-cpu-and-gpu-architectures-dd06e1f30586?source=collection_archive---------5-----------------------#2024-11-12)

## 适用于早期的机器学习工程师和数据科学家，旨在帮助他们理解内存基础、并行执行，以及如何为CPU和GPU编写代码。

[](https://medium.com/@shreyashukla04?source=post_page---byline--dd06e1f30586--------------------------------)[![Shreya Shukla](../Images/202aca58677a445ec618494f1151d2d7.png)](https://medium.com/@shreyashukla04?source=post_page---byline--dd06e1f30586--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dd06e1f30586--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dd06e1f30586--------------------------------) [Shreya Shukla](https://medium.com/@shreyashukla04?source=post_page---byline--dd06e1f30586--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dd06e1f30586--------------------------------) ·19分钟阅读·2024年11月12日

--

![](../Images/655d3adf6d073a78237e2072eedbb398.png)

照片由[Olav Ahrens Røtne](https://unsplash.com/@olav_ahrens?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文旨在解释并行计算的基础知识。我们从基础开始，包括理解共享架构与分布式架构以及这些系统之间的通信。我们将探讨GPU架构以及如何通过编写代码（使用C++ Kokkos）将架构原理映射到代码实现。最后，我们将使用运行Kokkos代码所获得的运行时数据来测量性能指标（加速），这些数据来自CPU和GPU架构下的向量-矩阵乘法，这是机器学习领域中最常见的操作之一。

本文的核心主题是探索和解答问题。虽然这似乎是一次漫长的旅程，但它将是值得的。让我们开始吧！

# 系统架构基础

## 我理解并行计算可以通过同时执行多个操作来节省时间。但是我听说系统时间与人类时间或挂钟时间不同。它们有什么不同之处？

计算中的最小时间单位称为**时钟周期**。它代表执行一个操作所需的最短时间，例如获取数据、执行计算或进行通信。**时钟周期**从技术上讲是指执行指令所需的状态变化。状态可以是处理器状态、数据状态、内存状态或控制信号。在一个时钟周期内，可能执行完整的指令、部分指令或多个指令。

**CPU允许每秒进行有限数量的状态变化**。例如，一个3GHz时钟频率的CPU每秒可以进行30亿次状态变化。时钟频率有上限，因为每个时钟周期都会产生热量，过高的频率会因为产生的热量损坏CPU芯片。

因此，我们希望通过使用并行计算方法来充分利用可用的计算能力。目的是隐藏**内存延迟**（从内存获取第一条数据所需的时间），提高**内存带宽**（每单位时间传输的数据量），并增强**计算吞吐量**（每个时钟周期内执行的任务数量）。

为了比较**性能**，例如在计算并行程序效率时，我们使用壁钟时间而非时钟周期，因为壁钟时间包括了所有的实际时间开销，如内存延迟和通信延迟，这些是无法直接转换为时钟周期的。

## 基本系统的架构是什么样的？

一个系统可以由单个处理器、一个节点甚至一个集群组成。系统的一些**物理**构件是——

1.  **节点** — 一个包含多个处理器芯片的物理计算单元。多个节点组合形成一个**集群**。

1.  **处理器芯片** — 芯片包含多个被称为核心的处理单元。

1.  **核心** — 每个核心都能够运行一个独立的**线程**。

在设定的术语中，一个节点可以与处理器芯片建立一对多的关系，而每个处理器芯片可以与核心建立一对多的关系。下图给出了包含处理器和核心的节点的可视化描述。

![](../Images/c153ab76de53b118f94f98245679085f.png)

现代节点具有四个八核心处理器，共享一个公共内存池。参考：[康奈尔虚拟研讨会](https://cvw.cac.cornell.edu/parallel/hpc/nodes)

系统的**非物理**组件包括线程和进程——

1.  **线程** — 线程是操作系统视为一个单独单元进行调度和执行的**CPU指令序列**。

1.  **进程** — 在计算中，进程是一个**资源分配的整体单元**，包括内存、文件处理器、端口和设备。一个进程可以管理多个线程的资源。[线程可以作为进程的组件进行建模。](https://cvw.cac.cornell.edu/parallel/terminology/threads-processes)

## 那么，线程是在同一系统的核心上运行，还是可以分布在不同系统中执行单个程序？无论哪种情况，它们如何通信？这些线程的内存是如何处理的？它们共享内存，还是每个线程都有自己的独立内存？

单个程序可以在同一系统/节点或不同的系统/节点上的多个核心上执行。系统和程序的设计决定了它是否符合预期的执行策略。

在设计系统时，必须考虑三个关键方面：**执行**（线程如何运行）、**内存访问**（内存如何分配给这些线程）和**通信**（线程如何通信，特别是当它们需要更新相同的数据时）。需要注意的是，这些方面大多是相互依赖的。

**执行**

**串行执行** — *这使用单个执行线程在任何时候处理单一数据项。*

**并行执行** — 在这种方式中，多个任务同时发生。在计算中，这可以是—

1.  **单个工人** — 单个执行线程同时处理多个数据项（CPU中的向量指令）。想象一个人根据花色整理扑克牌。由于有四种花色要分类，这个人必须逐一浏览整副牌，整理每种花色的卡片。

1.  **协作工作** — 单个进程中的多个执行线程。它相当于多个人一起合作，根据花色对一副扑克牌进行排序。

1.  **独立工作** — 多个进程可以处理同一个问题，利用相同的节点或多个节点。在这种情况下，每个人都会单独整理自己的扑克牌。

1.  以上的任意组合。

![](../Images/6d28c89a540999a37bf692c49d4ab184.png)

**协作工作**：两个工人需要插入同一花色的卡片。工人A持有部分结果

对于梅花花色，工人B被暂时阻塞。参考：[康奈尔虚拟研讨会](https://cvw.cac.cornell.edu/parallel/intro/working-together)

**内存访问**

1.  **共享内存** — 当程序在同一系统上的多个核心（**单个多线程进程**）中运行时，进程中的每个线程都可以访问相同虚拟地址空间中的内存。

1.  **分布式内存** — 当程序使用**多个进程**（无论是在单个节点上还是跨不同节点）时，采用分布式内存设计。在这种架构中，每个进程拥有数据的一部分，其他进程必须向数据拥有者发送消息以更新它们各自的部分。即使多个进程运行在单个节点上，每个进程仍有自己的虚拟内存空间。因此，这类进程应使用分布式内存编程进行通信。

1.  **混合策略** — **多线程进程**可以在同一节点或不同节点上运行，旨在通过共享内存编程利用单一节点上的多个核心。同时，它们还可以采用分布式内存策略与其他节点上的进程进行协调。可以想象，在上图中，多个人或线程在多个隔间工作。相同隔间中的工作者通过共享内存编程进行通信，而不同隔间中的工作者则通过分布式内存编程进行交互。

![](../Images/55c24c2dcde05cd6ce2f376d807bdda8.png)

在分布式内存设计中，多个并行工作者被分配到不同的隔间（进程）。参考：[康奈尔虚拟工作坊](https://cvw.cac.cornell.edu/parallel/terminology/tasks)

**通信**

通信机制依赖于内存架构。在**共享内存架构**中，像**OpenMP**（开放多处理）这样的应用程序接口可以实现共享内存和数据的线程之间的通信。另一方面，**MPI**（消息传递接口）可以用于在**分布式内存架构**中，运行在同一节点或不同节点上的进程之间的通信。

# 并行化策略和性能

## 我们如何判断我们的并行化策略是否有效？

有多种方法，但在这里我们讨论的是效率和加速比。在并行计算中，效率指的是在计算过程中实际利用的资源与可用资源的比例。它是通过将实际资源利用率与峰值性能进行比较来确定的，也就是最优资源利用率。

**实际处理器利用率**指的是在特定时间段内执行的浮点运算（FLOP）数量。

**峰值性能**假设每个处理器核心在每个时钟周期内执行最大可能的浮点运算（FLOP）。

**并行代码的效率**是每秒实际浮点运算（FLOPS）与可能的峰值性能之比。

**加速比**用于评估效率，其计算公式为：

![](../Images/917b99538054a2358cb3c512063c8ceb.png)

[当程序受限于处理器的计算速度时，速度提升不能超过并行资源的数量](https://cvw.cac.cornell.edu/parallel/efficiency/about-efficiency)。

使用加速比，测量并行效率的公式为：

![](../Images/5979f6439a7de8b14daa2056e1bdd6b5.png)

> [*因此，优化并行程序的一个重要目标是将其加速比尽可能接近核心数量。*](https://cvw.cac.cornell.edu/parallel/efficiency/about-efficiency)

假设代码的串行执行时间为300秒。在使用50个核心并行化任务后，整体的墙钟时间为6秒。在这种情况下，速度提升可以通过将串行执行的墙钟时间除以并行执行的墙钟时间来计算，结果为300秒/6秒 = 50。我们通过将速度提升除以核心数来获得并行效率，即50/50 = 1。这是最佳情况的示例：工作负载完美地进行了并行化，且所有核心得到了高效利用。

## 如果数据大小或任务数量增加，增加更多的计算单元是否会持续提高性能？

并不总是如此。在并行计算中，我们有两种类型的扩展性，分别是基于问题规模或并行任务数量的扩展性。

**强扩展性** — 在保持问题规模不变的情况下增加并行任务的数量。然而，即使我们增加计算单元（核心、处理器或节点）来并行处理更多任务，也会有与这些单元之间或与主程序之间的通信相关的开销，例如发送和接收数据所花费的时间。

> [**强扩展性表现受到通信时间与计算时间比率的影响。比率越大，强扩展性表现越差。**](https://cvw.cac.cornell.edu/parallel/efficiency/scaling)

理想情况下，随着并行任务数量的增加，执行时间会减少。然而，如果代码在强扩展性下没有加速，可能表明我们为当前工作量使用了过多的任务。

**弱扩展性** — 在这种情况下，随着任务数量的增加，问题规模也会增加，因此每个任务的计算量保持不变。如果您的程序具有良好的弱扩展性，您可以在相同的墙钟时间内，用两倍的节点运行一个规模为原来的两倍的问题。

## 确实，存在一些限制，无法并行化某些操作，对吗？

是的，将某些顺序操作并行化确实可能相当具有挑战性。并行化依赖于多个指令流和/或多个数据流。

![](../Images/826d67454a004c65b755cbb6b327bc25.png)

不同类型的并行计算架构。参考：[康奈尔虚拟工作坊](https://cvw.cac.cornell.edu/parallel/hpc/taxonomy-parallel-computers)

要了解什么可以并行化，我们来看看CPU中的SIMD，它是通过矢量化实现的。

> *矢量化是一种编程技术，其中操作是一次性应用到整个数组，而不是逐个处理单个元素。它是通过使用处理器中的矢量单元实现的，矢量单元包括矢量寄存器和矢量指令。*

考虑这样一种情况，我们遍历数组，并在for循环中对单个元素执行多个操作。当数据是独立的时，编写可矢量化的代码变得非常简单；请参见下面的示例：

```py
do i, n
  a(i) = b(i) + c(i)
  d(i) = e(i) + f(i)
end do
```

在这个循环中，每次迭代都是独立的——意味着 `a(i)` 的处理与 `a(i+1)` 等是独立的。因此，这段代码是可向量化的，允许使用来自 `b` 和 `c` 的元素并行计算数组 `a` 的多个元素，示例如下：

```py
b:  | b(i) | b(i+1) | b(i+2) | b(i+3) | ... |
c:  | c(i) | c(i+1) | c(i+2) | c(i+3) | ... |
------------------------------------------------
           Vectorized Addition (SIMD)

Vector Register 1 (loaded with b values):
      | b(i)   | b(i+1) | b(i+2) | b(i+3) | ... |

Vector Register 2 (loaded with c values):
      | c(i)   | c(i+1) | c(i+2) | c(i+3) | ... |

------------------------------------------------
Result in Vector Register 3:
      | a(i)   | a(i+1) | a(i+2) | a(i+3) | ... |
```

> *现代编译器通常能够分析这样的循环并将其转化为向量操作序列。* [*当某次迭代中的操作依赖于前一次迭代的结果时，问题就出现了。在这种情况下，自动向量化可能导致错误的结果。这种情况被称为数据依赖性。*](https://cvw.cac.cornell.edu/vector/coding/data-dependencies)

科学代码中常遇到的数据依赖性包括 -

**读后写（RAW）** — *不可向量化*

```py
do i, n
  a(i) = a(i-1) +b(i)
```

**写后读（WAR）** — *可向量化*

```py
do i, n
  a(i) = a(i+1) +b(i)
```

**写后写（WAW）** — *不可向量化*

```py
do i, n
  a(i%2) = a(i+1) +b(i)
```

**读后读（RAR）** — *可向量化*

```py
do i, n
  a(i) = b(i%2) + c(i)
```

遵循某些标准规则进行向量化——例如确保循环迭代中的独立赋值、避免随机数据访问以及防止迭代之间的依赖性——可以帮助编写可向量化的代码。

# GPU 架构与跨架构代码

## 当数据量增加时，尽可能将更多可并行化的操作进行并行化，以创建可扩展的解决方案是有意义的，但这意味着我们需要更大的系统，拥有更多的核心。难道这就是为什么我们使用 GPU 吗？它们与 CPU 有何不同，是什么导致了它们的高吞吐量？

是的！

![](../Images/7b996318622b4ea4b0acd28a9998e877.png)

比较 CPU 和 GPU 架构中基本元素的相对能力。参考：[康奈尔虚拟研讨会](https://cvw.cac.cornell.edu/gpu-architecture/gpu-characteristics/design)

**GPU（图形处理单元）** 相比 CPU，拥有更多的处理单元（绿色）和更高的整体 *内存带宽*（每单位时间传输的数据量）。而 CPU 则拥有更复杂的指令处理和更快的时钟速度。如上所示，CPU 的缓存内存比 GPU 多。然而，CPU 的算术逻辑单元（ALU）和浮点单元（FPU）少于 GPU。考虑到这些因素，**使用 CPU 处理复杂的工作流，而将 GPU 用于计算密集型任务是直观的。**

[GPU 被设计用来利用其大规模并行架构产生高计算吞吐量](https://cvw.cac.cornell.edu/gpu-architecture/gpu-characteristics/performance)。其计算潜力可以通过每秒数十亿次浮点运算（GFLOPS）来衡量。GPU 硬件通常以标准显卡（如 NVIDIA Quadro）、高端加速卡（如 NVIDIA Tesla）等形式出现。

图形管线的两个关键特性使得并行化成为可能，从而实现高吞吐量——

1.  **对象的独立性** — 一个典型的图形场景由许多独立的对象组成；每个对象可以独立处理，而不依赖于其他对象。

1.  **统一处理步骤 —** 处理步骤的顺序对于所有对象都是相同的。

## 因此，GPU的多个核心可以同时处理不同的数据，像SIMD（单指令多数据）架构一样并行执行计算。任务是如何在核心之间划分的？每个核心是否像CPU一样只运行一个线程？

在GPU中，**流式多处理器（SMs）**类似于CPU中的核心。GPU中的核心类似于CPU中的向量通道。SM是容纳核心的硬件单元。

当一个被称为**内核（kernel）**的函数或计算在GPU上执行时，它通常会被分解成**线程块（thread blocks）**。这些线程块包含**多个线程（multiple threads）**；每个SM可以管理其核心上的多个线程。如果线程块的数量超过SM的数量，多个线程块可以被分配到单个SM上。同时，多个线程可以在单个核心上运行。

[每个SM进一步将**线程块（thread blocks）**划分为称为**warps**的组，每个warp由**32个线程**组成。](https://cvw.cac.cornell.edu/gpu-architecture/gpu-characteristics/simt_warp) 这些线程在不同的数据元素上执行相同的指令流，遵循**单指令多数据（SIMD）**模型。warp大小被设置为32，因为在NVIDIA的架构中，CUDA核心是按32个一组进行分组的。这使得warp中的所有线程可以通过32个CUDA核心并行处理，从而实现高效率和优化的资源利用。

在**SIMD（单指令多数据）**中，单一指令对所有数据元素执行相同的操作，每个数据元素以完全相同的方式进行处理。**SIMT（单指令多线程）**，这是GPU中常用的方式，放宽了这一限制。在SIMT中，线程可以被激活或停用，因此指令和数据在激活的线程中被处理；然而，局部数据在非激活线程中保持不变。

## 我想理解如何编写代码以利用不同的架构。相似的代码可以同时在CPU和GPU架构上工作吗？我们可以使用哪些参数和方法来确保代码有效地利用底层硬件架构，无论是CPU还是GPU？

代码通常用高级语言如C或C++编写，并且必须通过编译器转换为二进制代码，因为计算机无法直接处理高级指令。虽然GPU和CPU都可以执行相同的内核，但正如我们将在示例代码中看到的，我们需要使用指令或参数来运行代码在特定架构上，编译并生成该架构的指令集。这种方法使我们能够利用架构特定的功能。为了确保兼容性，我们可以为编译器指定适当的标志，以生成针对所需架构优化的二进制代码，无论是CPU还是GPU。

各种编码框架，如 SYCL、CUDA 和 Kokkos，广泛用于为不同架构编写内核或函数。在本文中，我们将使用来自 Kokkos 的示例。

**关于 Kokkos —** 一个开源的 C++ 编程模型，旨在实现性能可移植性，用于编写内核：它作为一个模板库，构建在 CUDA、OpenMP 和其他后端之上，旨在描述性地定义我们想要做的事情，而非规定性地定义我们想如何做。Kokkos Core 提供了一种并行算法的编程模型，适用于多核芯片，并在这些核心之间共享内存。

**内核**有三个组件—

1.  **模式 —** 计算的结构：for、scan、reduction、task-graph

1.  **执行策略 —** 计算如何执行：静态调度、动态调度、线程组。

1.  **计算体 —** 执行每个工作单元的代码。例如，循环体

模式和策略驱动计算体。在下面的示例中，仅为说明用途，‘for**’** 是 *模式*，控制模式的 *条件*（element=0; element<n; ++element）是 *策略*，而 *计算体* 是模式内执行的代码

```py
for (element=0; element<n; ++element){
  total = 0;
  for(qp = 0; qp < numQPs; ++qp){
    total += dot(left[element][qp], right[element][qp]);
  } 
  elementValues[element] = total;
}
```

Kokkos 框架允许开发人员根据三个关键因素来定义参数和方法：代码将在哪运行 **（执行空间）**，将使用哪些内存资源 **（内存空间）**，以及数据如何结构化和管理 **（数据结构与数据管理）**。

我们主要讨论如何编写 Kokkos 内核来实现**向量矩阵乘法**，以了解这些因素如何在不同架构上实现。

但在此之前，我们先来讨论一下我们想要编写的内核的构建块。

**内存空间 —**

Kokkos 提供了多种内存空间选项，使用户能够控制内存管理和数据在不同计算平台上的布置。一些常用的内存空间包括—

1.  **HostSpace —** 此内存空间代表 CPU 的主内存。它用于 CPU 上的计算，通常是基于 CPU 的系统的默认内存空间。

1.  **CudaSpace —** CudaSpace 用于具有 CUDA 的 NVIDIA GPU。它提供 GPU 设备的内存分配和管理，支持高效的数据传输和计算。

1.  **CudaUVMSpac —** 对于统一虚拟内存（UVM）系统，如某些 NVIDIA GPU 上的 UVM，CudaUVMSpac 使得可以分配从 CPU 和 GPU 都可以访问的内存，而无需显式的数据传输。Cuda 运行时自动处理数据移动，但会有性能损失。

讨论**内存布局**也非常重要，它指的是内存中数据的组织和安排。Kokkos 提供了几种内存布局选项，帮助用户优化不同计算的数据显示方式。一些常用的内存布局包括—

![](../Images/1049e781fcb83e45fec8455b8c008e2d.png)

矩阵的行主序与列主序迭代。参考：[Wikipedia](https://en.wikipedia.org/wiki/Row-_and_column-major_order)

1.  **LayoutRight（也称为行主序）**是 C 和 C++ 中多维数组的默认内存布局。在 LayoutRight 中，最右边的索引在内存中变化最快。如果未选择布局，HostSpace 的默认布局为 LayoutRight。

1.  **LayoutLeft（也称为列主序）—** 在 LayoutLeft 中，最左边的索引在内存中变化最快。如果未选择布局，CudaSpace 的默认布局为 LayoutLeft。

在下面的程序实现中，我们根据编译器标志 ENABLE_CUDA 定义了内存空间和布局的宏，当我们希望在 GPU 上运行代码时，ENABLE_CUDA 为 True，CPU 上则为 False。

```py
 // ENABLE_CUDA is a compile time argument with default value true
    #define ENABLE_CUDA true

    // If CUDA is enabled, run the kernel on the CUDA (GPU) architecture
    #if defined(ENABLE_CUDA) && ENABLE_CUDA
        #define MemSpace Kokkos::CudaSpace
        #define Layout Kokkos::LayoutLeft
    #else
    // Define default values or behavior when ENABLE_CUDA is not set or is false
        #define MemSpace Kokkos::HostSpace
        #define Layout Kokkos::LayoutRight
    #endif
```

**数据结构与数据管理 —**

**Kokkos 视图 *—*** 在 Kokkos 中，"视图"是一个**基础数据结构**，表示一维和多维数组，可用于高效地存储和访问数据。Kokkos 视图为数据管理提供了高级抽象，并设计为与不同的执行空间和内存布局无缝协作。

```py
 // View for a 2d array of data type double
    Kokkos::View<double**> myView("myView", numRows, numCols);
    // Access Views
    myView(i, j) = 42.0; 
    double value = myView(i, j);
```

**Kokkos 数据管理的镜像技术** — 镜像是等效数组的视图，这些数组可能位于不同的内存空间中，适用于在 CPU 和 GPU 架构中都需要数据的场景。这项技术在读取 CPU 上的文件数据并随后在 GPU 上处理时非常有用。Kokkos 的镜像技术创建了数据的镜像视图，允许在 CPU 和 GPU 执行空间之间无缝共享，并促进数据传输和同步。

为了创建主数据的镜像副本，我们可以使用 Kokkos 的 *create_mirror_view()* 函数。该函数在指定的执行空间（例如 GPU）中生成一个具有与主视图相同数据类型和维度的镜像视图。

```py
 // Intended Computation -
    // <y, A*x> = y^T * A * x
    // Here:
    // y and x are vectors.
    // A is a matrix.

    // Allocate y, x vectors and Matrix A on device
    typedef Kokkos::View<double*, Layout, MemSpace>   ViewVectorType;
    typedef Kokkos::View<double**, Layout, MemSpace>  ViewMatrixType;

    // N and M are number of rows and columns
    ViewVectorType y( "y", N );
    ViewVectorType x( "x", M );
    ViewMatrixType A( "A", N, M );

    // Create host mirrors of device views
    ViewVectorType::HostMirror h_y = Kokkos::create_mirror_view( y );
    ViewVectorType::HostMirror h_x = Kokkos::create_mirror_view( x );
    ViewMatrixType::HostMirror h_A = Kokkos::create_mirror_view( A );

    // Initialize y vector on host.
    for ( int i = 0; i < N; ++i ) {
        h_y( i ) = 1;
    }

    // Initialize x vector on host.
    for ( int i = 0; i < M; ++i ) {
        h_x( i ) = 1;
    }

    // Initialize A matrix on host.
    for ( int j = 0; j < N; ++j ) {
        for ( int i = 0; i < M; ++i ) {
            h_A( j, i ) = 1;
        }
    }

    // Deep copy host views to device views.
    Kokkos::deep_copy( y, h_y );
    Kokkos::deep_copy( x, h_x );
    Kokkos::deep_copy( A, h_A );
```

**执行空间 —**

在 Kokkos 中，**执行空间**指的是并行操作和计算执行的特定计算环境或硬件平台。Kokkos 抽象了执行空间，使得代码可以以描述性方式编写，同时适应各种硬件平台。

我们讨论两个主要的执行空间 —

1.  **Serial**：Serial 执行空间是一种主要且可移植的选项，适用于单线程的 CPU 执行。它通常用于调试、测试以及作为性能比较的基准。

1.  **Cuda**：Cuda 执行空间用于 NVIDIA GPU，并依赖 CUDA 技术进行并行处理。它可以实现高效的 GPU 加速和 GPU 内存管理。

可以定义 ExecSpace，也可以根据内存空间动态确定，如下所示：

```py
 // Execution space determined based on MemorySpace
using ExecSpace = MemSpace::execution_space;
```

## 我们如何利用这些构件来编写实际的内核？我们能否用它来比较不同架构之间的性能？

为了编写一个内核并进行性能比较，我们使用以下计算：

```py
<y, A*x> = y^T * (A * x)

Here:

y and x are vectors.

A is a matrix.

<y, A*x> represents the inner product or dot product of vectors y 
and the result of the matrix-vector multiplication A*x.

y^T denotes the transpose of vector y.

* denotes matrix-vector multiplication.
```

这个操作在Kokkos中的内核—

```py
 // Use a RangePolicy.
    typedef Kokkos::RangePolicy<ExecSpace>  range_policy;

    // The below code is run for multiple iterations across different 
    // architectures for time comparison
    Kokkos::parallel_reduce( "yAx", range_policy( 0, N ),
                KOKKOS_LAMBDA ( int j, double &update ) {
                    double temp2 = 0;

                    for ( int i = 0; i < M; ++i ) {
                        temp2 += A( j, i ) * x( i );
                    }
                    update += y( j ) * temp2;
            }, result );
```

对于上述内核，`parallel_reduce`作为模式，`range_policy`定义了策略，而实际的操作构成了计算体。

我在一台配有NVIDIA Quadro RTX 5000 GPU的TACC Frontera节点上执行了此内核实验。实验使用了不同的**N**值，**N**代表向量**y**和**x**的长度，以及矩阵**A**的行数。计算执行了100次以获得显著结果，并记录了内核在串行（主机）和CUDA执行空间中的执行时间。我使用了`**ENABLE_CUDA**`编译器标志在执行环境之间切换：**True**表示GPU/CUDA执行空间，**False**表示CPU/串行执行空间。以下展示了这些实验的结果及相应的加速比。

有关CPU与GPU架构下内核执行时间和加速比的数据参考：作者提供的表格

![](../Images/29171354662a8b3bac463f48c99db523.png)

不同数据大小下的加速比趋势（GPU vs CPU）参考：作者提供的图片

我们注意到，随着N值的增大，加速比显著增加，这表明CUDA实现对于较大规模的问题变得越来越有优势。

目前为止就这些！希望本文能在探索计算领域时为您提供一个良好的开端。理解GPU架构的基础至关重要，本文介绍了一种我实验过的跨架构代码编写方法。然而，还有多种值得探索的方法和技术。

虽然我不是该领域的专家，但本文反映了我在德州奥斯汀TACC短暂工作的学习经历。我欢迎反馈和讨论，如果您有任何问题或想了解更多内容，我很乐意提供帮助。请参考下面的优秀资源以进一步学习。祝计算愉快！

## 致谢

本文参考了三个主要来源。第一个来源是**SDS394: UT Austin的科学与技术计算研究生课程**，该课程提供了关于单核多线程系统的基础知识。第二个来源是[**Cornell虚拟工作坊：并行编程概念与高性能计算**](https://cvw.cac.cornell.edu/parallel)，这是一个关于并行计算的优秀学习资源。Kokkos代码实现主要基于[**GitHub上的Kokkos教程**](https://github.com/kokkos/kokkos-tutorials)中的材料。这些都是任何有兴趣学习并行计算的人的绝佳资源。

— — — — — — — — — —

C++ Kokkos内核的开发，用于在不同架构之间进行性能比较，作为由**Intel OneAPI卓越中心**和**TACC STAR学者计划**支持的项目的一部分，该项目得到了TACC行业合作伙伴的慷慨资助，其中包括**Intel、Shell、Exxon**和**Chevron**。

— — — — — — — — — —

参考资料/资源：

[](https://github.com/VictorEijkhout/TheArtofHPC_pdfs/tree/main?source=post_page-----dd06e1f30586--------------------------------) [## GitHub - VictorEijkhout/TheArtofHPC_pdfs：Victor Eijkhout《高性能计算艺术》书籍及课程的所有pdf]

### Victor Eijkhout《高性能计算艺术》书籍及课程的所有pdf - VictorEijkhout/TheArtofHPC_pdfs

[github.com](https://github.com/VictorEijkhout/TheArtofHPC_pdfs/tree/main?source=post_page-----dd06e1f30586--------------------------------) [](https://docs.tacc.utexas.edu/hpc/frontera/?source=post_page-----dd06e1f30586--------------------------------) [## Frontera - TACC HPC文档]

### 最近更新：2024年10月24日 重要提示：请注意TACC的新SU收费政策。Frontera由国家资助…

[docs.tacc.utexas.edu](https://docs.tacc.utexas.edu/hpc/frontera/?source=post_page-----dd06e1f30586--------------------------------) [## 康奈尔虚拟研讨会：并行编程概念与高性能计算]

### 本路线图解释了并行编程概念，以及并行编程如何与高性能计算相关……

[康奈尔虚拟研讨会：了解GPU架构](https://cvw.cac.cornell.edu/parallel?source=post_page-----dd06e1f30586--------------------------------)

### 本路线图面向那些相对较新的GPU使用者或希望深入了解其中的计算机技术的人员……

[cvw.cac.cornell.edu](https://cvw.cac.cornell.edu/gpu-architecture?source=post_page-----dd06e1f30586--------------------------------) [## 康奈尔虚拟研讨会：矢量化]

### 本路线图描述了与计算硬件、编译器和编码实践相关的矢量化过程……

[cvw.cac.cornell.edu](https://cvw.cac.cornell.edu/vector?source=post_page-----dd06e1f30586--------------------------------) [](https://github.com/kokkos/kokkos-tutorials?source=post_page-----dd06e1f30586--------------------------------) [## GitHub — kokkos/kokkos-tutorials：Kokkos C++ 性能可移植性编程教程]

### Kokkos C++ 性能可移植性编程生态系统教程 — kokkos/kokkos-tutorials

[github.com](https://github.com/kokkos/kokkos-tutorials?source=post_page-----dd06e1f30586--------------------------------) [](https://github.com/kokkos/kokkos-tutorials/wiki/Kokkos-Lecture-Series?source=post_page-----dd06e1f30586--------------------------------) [## Kokkos讲座系列]

### Kokkos C++ 性能可移植性编程生态系统教程 - Kokkos讲座系列 ·…

[github.com](https://github.com/kokkos/kokkos-tutorials/wiki/Kokkos-Lecture-Series?source=post_page-----dd06e1f30586--------------------------------)
