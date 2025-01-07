# 为什么深度学习模型在 GPU 上运行更快：CUDA 编程简要介绍

> 原文：[https://towardsdatascience.com/why-deep-learning-models-run-faster-on-gpus-a-brief-introduction-to-cuda-programming-035272906d66?source=collection_archive---------1-----------------------#2024-04-17](https://towardsdatascience.com/why-deep-learning-models-run-faster-on-gpus-a-brief-introduction-to-cuda-programming-035272906d66?source=collection_archive---------1-----------------------#2024-04-17)

## 对于那些想了解`.to("cuda")`的作用的人

[](https://medium.com/@lucasdelimanogueira?source=post_page---byline--035272906d66--------------------------------)[![Lucas de Lima Nogueira](../Images/76edd8ee4005d4c0b8bd476261eb06ae.png)](https://medium.com/@lucasdelimanogueira?source=post_page---byline--035272906d66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--035272906d66--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--035272906d66--------------------------------) [Lucas de Lima Nogueira](https://medium.com/@lucasdelimanogueira?source=post_page---byline--035272906d66--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--035272906d66--------------------------------) ·15 分钟阅读 ·2024年4月17日

--

![](../Images/38705e373a34d8ee180d497825e35868.png)

图片由作者和 AI 协助制作 ([https://copilot.microsoft.com/images/create](https://copilot.microsoft.com/images/create))

如今，当我们谈论深度学习时，通常会将其实现与利用 GPU 以提高性能联系在一起。

GPU（图形处理单元）最初是为了加速图像、2D 和 3D 图形的渲染而设计的。然而，由于其能够执行大量并行操作的能力，GPU 的应用范围超越了这一点，扩展到了深度学习等领域。

GPU 在深度学习模型中的使用始于 2000 年代中后期，并在 2012 年随着 AlexNet 的出现变得非常流行。AlexNet 是由 Alex Krizhevsky、Ilya Sutskever 和 Geoffrey Hinton 设计的卷积神经网络，它在 2012 年赢得了 ImageNet 大规模视觉识别挑战赛（ILSVRC）。这一胜利标志着深度神经网络在图像分类中的有效性以及 GPU 在训练大型模型中的重要作用。

在这一突破之后，GPU 在深度学习模型中的使用变得越来越普及，这促进了像 PyTorch 和 TensorFlow 这样的框架的创建。

如今，我们只需在 PyTorch 中写 `.to("cuda")` 就能将数据发送到 GPU，并期望训练过程加速。但是，深度学习算法是如何在实际中利用 GPU 的计算性能的呢？让我们一探究竟！

深度学习架构，如神经网络、卷积神经网络（CNN）、递归神经网络（RNN）和变换器（transformers），基本上是通过数学运算构建的，比如矩阵加法、矩阵乘法以及将一个函数应用于矩阵。因此，如果我们找到优化这些运算的方法，就可以提高深度学习模型的性能。

那么，让我们从简单的开始。假设你想要将两个向量相加 *C = A + B*。

![](../Images/2e831e31e1d8d8b0315146a0d898fab1.png)

下面是一个用 C 实现的简单例子：

```py
void AddTwoVectors(flaot A[], float B[], float C[]) {
    for (int i = 0; i < N; i++) {
        C[i] = A[i] + B[i];
    }
}
```

正如你所注意到的，计算机必须遍历向量，在每次迭代中顺序地添加每一对元素。但这些操作是相互独立的。第 *i* 对元素的加法并不依赖于任何其他对。因此，如果我们能够并行执行这些操作，将所有元素对同时相加，会怎么样呢？

一种直接的方法是使用 CPU 多线程来并行执行所有计算。然而，对于深度学习模型，我们处理的是大量的向量，包含数百万个元素。普通的 CPU 最多只能同时处理十几个线程。此时，GPU 就派上用场了！现代的 GPU 可以同时运行数百万个线程，从而增强这些数学运算在大规模向量上的性能。

# GPU 与 CPU 的对比

尽管单次操作时 CPU 的计算可能比 GPU 更快，但 GPU 的优势在于它的并行化能力。其原因在于 CPU 和 GPU 的设计目标不同。CPU 设计的目的是尽可能快地执行一系列操作（线程），并且只能同时执行几十个线程，而 GPU 的设计目标是并行执行成千上万的操作（尽管牺牲了每个线程的速度）。

请看下面的视频：

举个例子，假设 CPU 就像一辆法拉利，而 GPU 就像一辆公交车。如果你的任务是搬运一个人，法拉利（CPU）显然是更好的选择。然而，如果你要搬运多人，尽管法拉利（CPU）每次的速度更快，但公交车（GPU）能够一次性将所有人都运送到目的地，比起法拉利多次往返，公交车能够更快地完成运输。所以，CPU 更适合处理顺序操作，而 GPU 更适合处理并行操作。

![](../Images/a1a871c965e587ba2104a0c62b78a33c.png)

作者借助 AI 提供的图像（[https://copilot.microsoft.com/images/create](https://copilot.microsoft.com/images/create)）

为了提供更高的并行能力，GPU 的设计将更多的晶体管分配给数据处理，而非数据缓存和流控制，这与 CPU 的设计不同，CPU 通常将大量晶体管分配给这些目的，以优化单线程性能和复杂指令执行。

下图展示了 CPU 与 GPU 的芯片资源分配情况。

![](../Images/b2bc61a6cffc46a6ab7e554fa5f3daf3.png)

作者所用图像灵感来源于[CUDA C++ 编程指南](https://docs.nvidia.com/cuda/pdf/CUDA_C_Programming_Guide.pdf)

CPU拥有强大的核心和更复杂的缓存内存架构（为此分配了大量晶体管）。这种设计使得顺序操作处理更快。另一方面，GPU优先考虑拥有大量核心，以实现更高程度的并行性。

现在我们已经理解了这些基本概念，如何在实践中利用这些并行计算能力呢？

# CUDA简介

当你运行一些深度学习模型时，你可能会选择使用一些流行的Python库，如PyTorch或TensorFlow。然而，众所周知，这些库的核心在底层运行的是C/C++代码。另外，正如我们之前提到的，你可能会使用GPU来加速处理。这时，CUDA就派上用场了！CUDA代表*计算统一架构*，它是NVIDIA为其GPU上的通用处理开发的平台。因此，尽管DirectX被游戏引擎用来处理图形计算，CUDA使开发人员能够将NVIDIA的GPU计算能力集成到他们的通用软件应用中，超越了仅仅是图形渲染的应用。

为了实现这一点，CUDA提供了一个简单的基于C/C++的接口（CUDA C/C++），使得可以访问GPU的虚拟指令集和特定操作（如在CPU和GPU之间移动数据）。

在进一步探讨之前，让我们先理解一些基本的CUDA编程概念和术语：

+   *host*：指的是CPU及其内存；

+   *device*：指的是GPU及其内存；

+   *kernel*：指的是在设备（GPU）上执行的函数；

因此，在使用CUDA编写的基本代码中，程序在*host*（CPU）上运行，发送数据到*device*（GPU），并启动在*device*（GPU）上执行的*kernels*（函数）。这些内核由多个线程并行执行。执行后，结果从*device*（GPU）传回*host*（CPU）。

让我们回到添加两个向量的问题：

```py
#include <stdio.h>

void AddTwoVectors(flaot A[], float B[], float C[]) {
    for (int i = 0; i < N; i++) {
        C[i] = A[i] + B[i];
    }
}

int main() {
    ...
    AddTwoVectors(A, B, C);
    ...
}
```

在CUDA C/C++中，程序员可以定义C/C++函数，称为*kernels*，当调用时，这些内核会被N个不同的CUDA线程并行执行N次。

要定义一个内核，你可以使用`__global__`声明符，并且执行此内核的CUDA线程数可以通过`<<<...>>>`符号来指定：

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {
    ...
    // Kernel invocation with N threads
    AddTwoVectors<<<1, N>>>(A, B, C);
    ...
}
```

每个线程执行内核，并被赋予一个唯一的线程ID `threadIdx`，可以通过内置变量在内核中访问。上面的代码将两个大小为N的向量A和B相加，并将结果存储到向量C中。正如你所注意到的，与其使用循环顺序执行每对加法操作，CUDA允许我们同时执行所有这些操作，使用N个线程并行处理。

但在我们能够运行此代码之前，还需要进行另一个修改。重要的是要记住，*内核*函数是在设备（GPU）内运行的。因此，所有数据都需要存储在设备内存中。您可以通过以下CUDA内置函数来实现：

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // Size of the vectors
    float A[N], B[N], C[N]; // Arrays for vectors A, B, and C

    ...

    float *d_A, *d_B, *d_C; // Device pointers for vectors A, B, and C

    // Allocate memory on the device for vectors A, B, and C
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // Copy vectors A and B from host to device
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // Kernel invocation with N threads
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    // Copy vector C from device to host
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

}
```

我们需要使用指针，而不是直接将变量A、B和C传递给*内核*。在CUDA编程中，不能直接在内核启动（`<<<...>>>`）中使用*主机*数组（如示例中的`A`、`B`和`C`）。CUDA内核在设备内存上操作，因此需要将设备指针（`d_A`、`d_B`和`d_C`）传递给内核，以便它能够在设备上执行操作。

此外，我们需要使用`cudaMalloc`在*设备*上分配内存，并使用`cudaMemcpy`在*主机*和*设备*之间复制数据。

现在我们可以添加向量A和B的初始化，并在代码末尾刷新cuda内存。

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // Size of the vectors
    float A[N], B[N], C[N]; // Arrays for vectors A, B, and C

    // Initialize vectors A and B
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // Device pointers for vectors A, B, and C

    // Allocate memory on the device for vectors A, B, and C
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // Copy vectors A and B from host to device
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // Kernel invocation with N threads
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    // Copy vector C from device to host
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // Free device memory
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);
}
```

此外，我们在调用内核之后需要添加`cudaDeviceSynchronize();`。这是一个用于同步主机线程和设备的函数。调用此函数时，主机线程将等待，直到设备上所有先前发出的CUDA命令完成，才会继续执行。

此外，添加一些CUDA错误检查非常重要，这样我们才能在GPU上识别出错误。如果不添加此检查，代码将继续执行*主机*线程（CPU），并且很难识别与CUDA相关的错误。

以下是这两种技术的实现：

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoVectors(float A[], float B[], float C[]) {
    int i = threadIdx.x;
    C[i] = A[i] + B[i];
}

int main() {

    int N = 1000; // Size of the vectors
    float A[N], B[N], C[N]; // Arrays for vectors A, B, and C

    // Initialize vectors A and B
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // Device pointers for vectors A, B, and C

    // Allocate memory on the device for vectors A, B, and C
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // Copy vectors A and B from host to device
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // Kernel invocation with N threads
    AddTwoVectors<<<1, N>>>(d_A, d_B, d_C);

    // Check for error
    cudaError_t error = cudaGetLastError();
    if(error != cudaSuccess) {
        printf("CUDA error: %s\n", cudaGetErrorString(error));
        exit(-1);
    }

    // Waits untill all CUDA threads are executed
    cudaDeviceSynchronize();

    // Copy vector C from device to host
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // Free device memory
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);
}
```

要编译和运行CUDA代码，您需要确保CUDA工具包已安装在您的系统上。然后，您可以使用`nvcc`，NVIDIA CUDA编译器，来编译代码。如果您的计算机上没有GPU，可以使用Google Colab。只需要在运行时→笔记本设置中选择一个GPU，然后将代码保存到`example.cu`文件中并运行：

```py
%%shell
nvcc example.cu -o compiled_example # compile
./compiled_example # run

# you can also run the code with bug detection sanitizer
compute-sanitizer --tool memcheck ./compiled_example 
```

然而，我们的代码仍然没有完全优化。上面的示例使用了一个大小为N = 1000的向量。但这是一个小数字，无法充分展示GPU的并行化能力。此外，在处理深度学习问题时，我们通常会处理具有数百万个参数的大规模向量。然而，如果我们尝试设置，例如，N = 500000并使用`<<<1, 500000>>>`运行上面的内核，它将抛出一个错误。因此，为了改进代码并执行此类操作，我们首先需要了解CUDA编程中的一个重要概念：线程层次结构。

# 线程层次结构

内核函数的调用使用`<<<number_of_blocks, threads_per_block>>>`表示法进行。因此，在我们上面的示例中，我们运行1个块，包含N个CUDA线程。然而，每个块对其支持的线程数量有限制。这是因为每个块中的线程都需要位于同一个流式多处理器核心，并且必须共享该核心的内存资源。

您可以使用以下代码片段获取此限制：

```py
int device;
cudaDeviceProp props;
cudaGetDevice(&device);
cudaGetDeviceProperties(&props, device);
printf("Maximum threads per block: %d\n", props.maxThreadsPerBlock);
```

在当前的Colab GPU上，一个线程块最多可以包含1024个线程。因此，我们需要更多的块来执行更多的线程，以处理示例中的大向量。此外，块被组织成网格，如下所示：

![](../Images/748e7f97d26b7d7fa6f8f8ed799b0c0a.png)

[https://handwiki.org/wiki/index.php?curid=1157670](https://handwiki.org/wiki/index.php?curid=1157670) ([CC BY-SA 3.0)](https://creativecommons.org/licenses/by-sa/3.0/%7C)

现在，可以使用以下方式访问线程ID：

```py
int i = blockIdx.x * blockDim.x + threadIdx.x;
```

所以，我们的脚本变成了：

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoVectors(float A[], float B[], float C[], int N) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if (i < N) // To avoid exceeding array limit
        C[i] = A[i] + B[i];
}

int main() {
    int N = 500000; // Size of the vectors
    int threads_per_block;
    int device;
    cudaDeviceProp props;
    cudaGetDevice(&device);
    cudaGetDeviceProperties(&props, device);
    threads_per_block = props.maxThreadsPerBlock;
    printf("Maximum threads per block: %d\n", threads_per_block); // 1024

    float A[N], B[N], C[N]; // Arrays for vectors A, B, and C

    // Initialize vectors A and B
    for (int i = 0; i < N; ++i) {
        A[i] = 1;
        B[i] = 3;
    }

    float *d_A, *d_B, *d_C; // Device pointers for vectors A, B, and C

    // Allocate memory on the device for vectors A, B, and C
    cudaMalloc((void **)&d_A, N * sizeof(float));
    cudaMalloc((void **)&d_B, N * sizeof(float));
    cudaMalloc((void **)&d_C, N * sizeof(float));

    // Copy vectors A and B from host to device
    cudaMemcpy(d_A, A, N * sizeof(float), cudaMemcpyHostToDevice);
    cudaMemcpy(d_B, B, N * sizeof(float), cudaMemcpyHostToDevice);

    // Kernel invocation with multiple blocks and threads_per_block threads per block
    int number_of_blocks = (N + threads_per_block - 1) / threads_per_block;
    AddTwoVectors<<<number_of_blocks, threads_per_block>>>(d_A, d_B, d_C, N);

    // Check for error
    cudaError_t error = cudaGetLastError();
    if (error != cudaSuccess) {
        printf("CUDA error: %s\n", cudaGetErrorString(error));
        exit(-1);
    }

    // Wait until all CUDA threads are executed
    cudaDeviceSynchronize();

    // Copy vector C from device to host
    cudaMemcpy(C, d_C, N * sizeof(float), cudaMemcpyDeviceToHost);

    // Free device memory
    cudaFree(d_A);
    cudaFree(d_B);
    cudaFree(d_C);

}
```

# 性能比较

下面是不同向量大小下CPU和GPU进行这两个向量加法操作的比较。

![](../Images/b8e80e991576bd43865f4bd9783f4b52.png)

图片由作者提供

如你所见，GPU处理的优势只有在向量大小N非常大的情况下才会显现出来。此外，请记住，这个时间比较仅仅考虑了内核/函数的执行时间，并没有考虑在*主机*和*设备*之间复制数据的时间，尽管在大多数情况下这个时间可能不显著，但在我们这个简单的加法操作中，它相对来说是比较可观的。因此，重要的是要记住，GPU计算只有在处理高度计算密集且高度并行的计算时才展示其优势。

# 多维线程

好了，现在我们知道如何提高一个简单数组操作的性能。但在处理深度学习模型时，我们需要处理矩阵和张量操作。在我们之前的示例中，我们只使用了一维块和N个线程。然而，也可以执行多维线程块（最多支持3个维度）。因此，为了方便起见，如果你需要执行矩阵操作，你可以运行一个NxM线程的线程块。在这种情况下，你可以获取矩阵的行列索引，方法是`row = threadIdx.x, col = threadIdx.y`。同样，为了方便，你可以使用`dim3`变量类型来定义`number_of_blocks`和`threads_per_block`。

下面的示例演示了如何加法两个矩阵。

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoMatrices(float A[N][N], float B[N][N], float C[N][N]) {
    int i = threadIdx.x;
    int j = threadIdx.y;
    C[i][j] = A[i][j] + B[i][j];
}

int main() {
    ...
    // Kernel invocation with 1 block of NxN threads
    dim3 threads_per_block(N, N);
    AddTwoMatrices<<<1, threads_per_block>>>(A, B, C);
    ...
}
```

你也可以扩展这个示例来处理多个块：

```py
#include <stdio.h>

// Kernel definition
__global__ void AddTwoMatrices(float A[N][N], float B[N][N], float C[N][N]) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    int j = blockIdx.y * blockDim.y + threadIdx.y;
    if (i < N && j < N) {
        C[i][j] = A[i][j] + B[i][j];
    }
}

int main() {
    ...
    // Kernel invocation with 1 block of NxN threads
    dim3 threads_per_block(32, 32);
    dim3 number_of_blocks((N + threads_per_block.x - 1) ∕ threads_per_block.x, (N + threads_per_block.y - 1) ∕ threads_per_block.y);
    AddTwoMatrices<<<number_of_blocks, threads_per_block>>>(A, B, C);
    ...
}
```

你也可以使用相同的思路扩展这个示例来处理三维操作。

现在你知道如何操作多维数据了，还有一个重要且简单的概念需要学习：如何在内核中调用函数。基本上，这是通过使用`__device__`声明修饰符来完成的。这定义了可以由*设备*（GPU）直接调用的函数。因此，它们只能从`__global__`或另一个`__device__`函数中调用。下面的示例对向量应用了sigmoid操作（这是深度学习模型中非常常见的操作）。

```py
#include <math.h>

// Sigmoid function
__device__ float sigmoid(float x) {
    return 1 / (1 + expf(-x));
}

// Kernel definition for applying sigmoid function to a vector
__global__ void sigmoidActivation(float input[], float output[]) {
    int i = threadIdx.x;
    output[i] = sigmoid(input[i]);

}
```

所以，现在你已经了解了CUDA编程的一些基本重要概念，你可以开始创建CUDA内核。在深度学习模型的情况下，它们基本上是一些矩阵和张量操作，如加法、乘法、卷积、归一化等。例如，一个简单的矩阵乘法算法可以像下面这样进行并行化：

![](../Images/eea3441283b27a0b357d8f148ba9f6e5.png)

```py
// GPU version

__global__ void matMul(float A[M][N], float B[N][P], float C[M][P]) {
    int row = blockIdx.x * blockDim.x + threadIdx.x;
    int col = blockIdx.y * blockDim.y + threadIdx.y;

    if (row < M && col < P) {
        float C_value = 0;
        for (int i = 0; i < N; i++) {
            C_value += A[row][i] * B[i][col];
        }
        C[row][col] = C_value;
    }
}
```

现在，将这个与下面的普通CPU实现的两个矩阵乘法进行对比：

```py
// CPU version

void matMul(float A[M][N], float B[N][P], float C[M][P]) {
    for (int row = 0; row < M; row++) {
        for (int col = 0; col < P; col++) {
            float C_value = 0;
            for (int i = 0; i < N; i++) {
                C_value += A[row][i] * B[i][col];
            }
            C[row][col] = C_value;
        }
    }
}
```

你可以注意到，在GPU版本中，我们的循环次数更少，从而使得操作的处理速度更快。下面是CPU和GPU在NxN矩阵乘法性能对比：

![](../Images/fa9aef35ede725268ad91f948b4e9ca6.png)

作者提供的图片

正如你所观察到的，随着矩阵大小的增加，GPU处理的性能提升在矩阵乘法操作中表现得更加明显。

现在，考虑一个基本的神经网络，它主要涉及**y** = σ(W**x** + **b**)的操作，如下所示：

![](../Images/568620fec12aad34ce87ccd1c37caaca.png)

作者提供的图片

这些操作主要包括矩阵乘法、矩阵加法和对数组应用函数，所有这些你已经熟悉并行化技术。因此，现在你已经能够从零开始实现一个运行在GPU上的神经网络！

# 结论

在这篇文章中，我们介绍了关于GPU处理的基础概念，以提高深度学习模型的性能。然而，也需要提到的是，你所看到的这些概念仅仅是基础，还有很多东西需要学习。像PyTorch和TensorFlow这样的库实现了优化技术，涉及其他更复杂的概念，如优化内存访问、批处理操作等（它们利用了基于CUDA的库，如cuBLAS和cuDNN）。但我希望这篇文章能帮助你理清当你编写`.to("cuda")`并在GPU上执行深度学习模型时，背后发生了什么。

在未来的文章中，我将尝试带来更多关于CUDA编程的复杂概念。请在评论中告诉我你对这篇文章的看法，或者你希望我接下来写些什么！非常感谢你的阅读！😊

# 进一步阅读

[CUDA编程指南](https://docs.nvidia.com/cuda/pdf/CUDA_C_Programming_Guide.pdf) — NVIDIA CUDA编程文档。

[CUDA文档](https://docs.nvidia.com/cuda/) — NVIDIA完整的CUDA文档。

[CUDA神经网络训练实现](https://luniak.io/cuda-neural-network-implementation-part-1/) — 用纯CUDA C++实现的神经网络训练。

[CUDA LLM训练实现](https://github.com/karpathy/llm.c) — 用纯CUDA C实现的LLM训练。
