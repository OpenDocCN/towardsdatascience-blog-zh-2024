# PyTorch 和 MLX 在 Apple Silicon 上的应用

> 原文：[`towardsdatascience.com/pytorch-and-mlx-for-apple-silicon-4f35b9f60e39?source=collection_archive---------0-----------------------#2024-03-08`](https://towardsdatascience.com/pytorch-and-mlx-for-apple-silicon-4f35b9f60e39?source=collection_archive---------0-----------------------#2024-03-08)

## 并排的 CNN 实现与比较

[](https://mikecvet.medium.com/?source=post_page---byline--4f35b9f60e39--------------------------------)![Mike Cvet](https://mikecvet.medium.com/?source=post_page---byline--4f35b9f60e39--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4f35b9f60e39--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4f35b9f60e39--------------------------------) [Mike Cvet](https://mikecvet.medium.com/?source=post_page---byline--4f35b9f60e39--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4f35b9f60e39--------------------------------) ·9 分钟阅读·2024 年 3 月 8 日

--

![](img/8ca713060459ca7ca5b77df6b814fd81.png)

所有图片由作者提供

几个月前，[苹果悄然发布了](https://www.theverge.com/2023/12/6/23990678/apple-foundation-models-generative-ai-mlx)其第一个公开版本的[MLX 框架](https://ml-explore.github.io/mlx/build/html/index.html)，该框架填补了[PyTorch](https://pytorch.org)、[NumPy](https://numpy.org/doc/stable/index.html)和[Jax](https://github.com/google/jax)之间的空白，但专为 Apple Silicon 进行优化。与这些库类似，MLX 是一个基于 Python 的 API，其底层操作大部分是用 C++实现的。

以下是 MLX 和 PyTorch 之间的一些相似性和差异的观察。我使用 PyTorch 及其 Apple Silicon GPU 硬件支持实现了一个定制的卷积神经网络，并在几个不同的数据集上进行了测试。特别是，[MNIST 数据集](http://yann.lecun.com/exdb/mnist/)，以及[CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html)和[CIFAR-100](https://www.cs.toronto.edu/~kriz/cifar.html)数据集。

下文讨论的所有代码可以[在此找到](https://github.com/mikecvet/cnn/)。

+   方法

+   关于 MLX 的笔记

+   性能

+   总结思考

# 方法

我首先用 PyTorch 实现了该模型，因为我对这个框架更为熟悉。该模型包含一系列卷积层和池化层，随后是一些带有 dropout 的线性层。

```py
# First block: Conv => ReLU => MaxPool
self.conv1 = Conv2d(in_channels=channels, out_channels=20, kernel_size=(5, 5), padding=2)
self.relu1 = ReLU()
self.maxpool1 = MaxPool2d(kernel_size=(2, 2), stride=(2, 2))

# Second block: Conv => ReLU => MaxPool
self.conv2 = Conv2d(in_channels=20, out_channels=50, kernel_size=(5, 5), padding=2)
self.relu2 = ReLU()
self.maxpool2 = MaxPool2d(kernel_size=(2, 2), stride=(2, 2))

# Third block: Conv => ReLU => MaxPool layers
self.conv3 = Conv2d(in_channels=50, out_channels=final_out_channels, kernel_size=(5, 5), padding=2)
self.relu3 = ReLU()
self.maxpool3 = MaxPool2d(kernel_size=(2, 2), stride=(2, 2))

# Fourth block: Linear => Dropout => ReLU layers
self.linear1 = Linear(in_features=fully_connected_input_size, out_features=fully_connected_input_size // 2)
self.dropout1 = Dropout(p=0.3)
self.relu3 = ReLU()

# Fifth block: Linear => Dropout layers
self.linear2 = Linear(in_features=fully_connected_input_size // 2, out_features=fully_connected_input_size // 4)
self.dropout2 = Dropout(p=0.3)

# Sixth block: Linear => Dropout layers
self.linear3 = Linear(in_features=fully_connected_input_size // 4, out_features=classes)
self.dropout3 = Dropout(p=0.3)

self.logSoftmax = LogSoftmax(dim=1)
```

这个架构对于 MNIST 数据集的分类来说有些过于复杂，但我希望能够构建一个具有一定复杂度的模型来对比这两个框架。我将其在 CIFAR 数据集上进行了测试，达到了约 40%的准确率；虽然不是特别出色，但对于一个非[ResNet](https://en.wikipedia.org/wiki/Residual_neural_network)模型来说，应该算是不错的。

完成这个实现之后，我写了一个利用 MLX 的并行实现。我高兴地发现，在导入必要的 MLX 模块并替换掉 PyTorch 模块后，**大部分**的 PyTorch 实现可以直接重用。

例如，上述代码的 MLX 版本可以[在这里](https://github.com/mikecvet/cnn/blob/main/src/python/mlx/model.py#L42)找到；除了几个命名参数的差异外，它是完全相同的。

# MLX 的注意事项

MLX 具有一些值得注意的有趣特性。

## 数组

MLX 的 `[array](https://ml-explore.github.io/mlx/build/html/python/array.html)` 类代替了 `[Tensor](https://pytorch.org/docs/stable/tensors.html)`；许多文档将其与 NumPy 的 `[ndarray](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)` 进行比较，但它也是该框架中各种[神经网络层](https://ml-explore.github.io/mlx/build/html/python/nn.html)使用和返回的数据类型。

`array` 的工作方式大致符合预期，尽管我在深度嵌套的 `np.ndarrays` 和 `mlx.arrays` 之间来回转换时遇到了一些问题，这需要一些[列表类型转换](https://github.com/mikecvet/cnn/blob/main/src/python/imagedata.py#L107)才能使其正常工作。

## 懒计算

[MLX 中的操作](https://ml-explore.github.io/mlx/build/html/python/ops.html)是[懒评估](https://ml-explore.github.io/mlx/build/html/usage/lazy_evaluation.html)的；这意味着，在懒加载构建的计算图中，只有程序*实际使用*的输出才会执行计算。

有两种方法可以强制评估操作结果（如推理）：

+   调用 `mlx.eval()` 进行输出评估

+   出于任何原因引用变量的值；例如在记录日志或条件语句中

当试图管理代码的性能时，这可能有点棘手，因为对任何值的引用（即使是偶然的）都会触发该变量以及图中所有中间变量的评估。例如：

```py
def classify(X, y):
  model = MyModel()                   # Not yet initialized
  p = model(X)                        # Not yet computed
  loss = mlx.nn.losses.nll_loss(p, y) # Not yet computed

  print(f"loss value: {loss}") # Inits `model`, computes `loss` _and_ `p`
  mlx.eval(p)                  # No-op

  # Without the print() above, would return `p` and lazy `loss`
  return p, loss 
```

这种行为也让在 PyTorch 和基于 MLX 的模型之间建立一对一基准测试变得有些困难。由于训练循环可能不会在循环内部评估输出，因此需要强制执行计算，以便跟踪实际操作的时间。

```py
test_start = time.perf_counter_ns() # Start time block
accuracy, _ = eval(test_data_loader, model, n)
mx.eval(accuracy) # Force calculation within measurement block
test_end = time.perf_counter_ns() # End time block
```

在积累一个庞大的隐式计算图和在训练过程中定期强制评估该图之间存在权衡。例如，我能够懒加载地遍历所有的训练周期，仅用几秒钟就完成了整个数据集的训练。然而，最终评估（假设非常庞大的）隐式计算图所需的时间与在每个批次后进行 `eval` 的时间差不多。这可能并不总是如此。

## 编译

MLX 提供了通过[编译](https://ml-explore.github.io/mlx/build/html/usage/compile.html)优化纯函数执行的能力。可以直接调用`mlx.compile()`，或者在纯函数（没有副作用）上使用注解（`@mlx.compile`）。

使用编译函数时有一些与状态变异相关的陷阱；这些内容在[文档中讨论](https://ml-explore.github.io/mlx/build/html/usage/compile.html#pure-functions)。

[看起来像](https://ml-explore.github.io/mlx/build/html/dev/extensions.html)这导致了逻辑的编译成[金属着色语言](https://developer.apple.com/documentation/metal)，以便在 GPU 上运行（我之前在这里探索过 MSL）。

## API 兼容性和代码约定

如上所述，将我的 PyTorch 代码转换为基于 MLX 的等价代码相当容易。不过，也有一些区别：

+   一些神经网络层会特别期望不同的输入配置。例如，`[mlx.nn.Conv2d](https://ml-explore.github.io/mlx/build/html/python/nn/_autosummary/mlx.nn.Conv2d.html#mlx.nn.Conv2d)`期望输入图像为`NHWC`格式（其中`C`表示通道维度），而`[torch.nn.Conv2d](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html)`则期望`NCHW`；还有一些其他类似的例子。这需要一些[条件张量/数组重排](https://github.com/mikecvet/cnn/blob/main/src/python/imagedata.py#L62)。

+   不幸的是，目前 MLX 并没有类似于 PyTorch 的[数据集和数据加载器](https://pytorch.org/tutorials/beginner/basics/data_tutorial.html)那样的相对*乐趣*；相反，我不得不手动构建[一些类似的东西](https://github.com/mikecvet/cnn/blob/main/src/python/mlx/dataset.py)。

+   从`nn.Module`派生的模型实现不需要重写`forward()`，而是重写`__call__()`进行推理。

+   我推测，由于可能涉及到函数编译，以及上面提到的延迟计算支持，使用 MLX 优化器进行训练的过程与典型的 PyTorch 模型有所不同。在后者中，通常习惯于如下的标准格式：

```py
for X, y in dataloader:
  p = model(X)
  loss = loss_fn(p, y)
  optimizer.zero_grad()
  loss.backward()
  optimizer.step()
```

MLX 鼓励并似乎期望采用类似以下格式，这取自[文档](https://ml-explore.github.io/mlx/build/html/examples/mlp.html)和[一个代码库示例](https://github.com/ml-explore/mlx-examples/blob/main/mnist/main.py)：

```py
def loss_fn(model, X, y):
  return nn.losses.cross_entropy(model(X), y, reduction="mean")

loss_and_grad_fn = nn.value_and_grad(model, loss_fn)

@partial(mx.compile, inputs=model.state, outputs=model.state)
def step(X, y):
  loss, grads = loss_and_grad_fn(model, X, y)
  optimizer.update(model, grads)
  return loss

# batch_iterate is a custom generator function
for X, y in batch_iterate(batch_size, train_images, train_labels):
  loss = step(X, y)
```

这没有问题，但比我预期的要复杂一些。否则，一切都显得非常熟悉。

# 性能

请注意，以下所有结果均来自我的 MacBook Air M2。

该 CNN 有三种配置：`PyTorch CPU`、`PyTorch GPU`和`MLX GPU`。作为一致性检查，在 30 个训练周期中，三者在准确性和损失方面的对比如下：

![](img/dfbc12d31193594274e89c2795303e3c.png)

30 个 epoch 的准确率和损失；可视化代码可在链接的仓库中找到

这里的结果都大致相同，尽管有趣的是，基于 MLX 的模型似乎比基于 PyTorch 的模型更快收敛。

此外，似乎 MLX 模型的准确率始终略低于基于 PyTorch 的模型。我不确定造成这种差异的原因。

在运行时性能方面，我得到了其他一些有趣的结果：

![](img/9602a40fc15378305f4337bb63c9f5bb.png)

三种模型配置的训练 epoch 运行时间方差

在训练模型时，基于 PyTorch 的 CPU 模型毫不意外地花费了最多的时间，每个 epoch 最少为 36 秒，最多为 45 秒。基于 MLX 的模型，在 GPU 上运行，时间范围大约为 21-27 秒每个 epoch。基于 GPU 运行的 PyTorch，通过`MPS 设备`，在这方面明显更快，epoch 时间范围为 10-14 秒。

对测试数据集的 1 万张图像进行分类讲述了不同的故事。

![](img/f823d919de4e496efd43687e2ee17212.png)

每个模型变种分类所有 1 万张图像所花费的总时间；批次大小为 512

当 CPU 模型分类所有 1 万张图像（每批次 512 张）时，花费了大约 1700ms，而基于 GPU 的模型则分别为 MLX 为 1100ms，PyTorch 为 850ms。

然而，当单独分类图像而不是批量分类时：

![](img/eedcde9e07e536cdae2ad04d5d076ef9.png)

每个模型变种分类所有 1 万张图像所花费的总时间；每次分类单张图像，总计超过一万张。

Apple Silicon 使用了一个[统一内存模型](https://www.apple.com/newsroom/2021/10/introducing-m1-pro-and-m1-max-the-most-powerful-chips-apple-has-ever-built/)，这意味着当在 PyTorch 中将数据和模型的 GPU 设备设置为`mps`（例如通过`.to(torch.device(`mps`))`）时，[数据并不会真正移到物理 GPU 专用内存](https://ml-explore.github.io/mlx/build/html/usage/unified_memory.html)。因此，看起来 PyTorch 在初始化 Apple Silicon GPU 执行代码时的开销相当大。如上所述，它在并行批量工作负载中运行良好。但在训练后进行单个记录分类时，MLX 在后台进行 GPU 执行启动的处理方式显然要更快。

## 性能分析

快速查看基于 MLX 的模型的`cProfile`输出，按累计执行时间排序：

```py
 ncalls  tottime  percall  cumtime  percall filename:lineno(function)
      426   86.564    0.203   86.564    0.203 {built-in method mlx.core.eval}
        1    2.732    2.732   86.271   86.271 /Users/mike/code/cnn/src/python/mlx/cnn.py:48(train)
    10051    0.085    0.000    0.625    0.000 /Users/mike/code/cnn/src/python/mlx/model.py:80(__call__)
    30153    0.079    0.000    0.126    0.000 /Users/mike/Library/Python/3.9/lib/python/site-packages/mlx/nn/layers/pooling.py:23(_sliding_windows)
    30153    0.072    0.000    0.110    0.000 /Users/mike/Library/Python/3.9/lib/python/site-packages/mlx/nn/layers/convolution.py:122(__call__)
        1    0.062    0.062    0.062    0.062 {built-in method _posixsubprocess.fork_exec}
    40204    0.055    0.000    0.055    0.000 {built-in method relu}
    10051    0.054    0.000    0.054    0.000 {built-in method mlx.core.mean}
      424    0.050    0.000    0.054    0.000 {built-in method step}
```

我们在一些层函数中花了一些时间，其中大部分时间花费在`mlx.core.eval()`上，这也合乎逻辑，因为在图中正是这一点开始进行实际计算。

使用`[asitop](https://github.com/tlkh/asitop)`可视化来自 MacOS 的底层时序`powertools`数据：

![](img/5a312cd1ca69b90f989e437fbd571f67.png)

asitop 功率历史 — MLX 模型运行

你可以看到，在训练此模型时，GPU 已经完全饱和，达到了其[最大时钟频率 1398 MHz](https://www.notebookcheck.net/Apple-MacBook-Air-M2-review-The-faster-10-core-GPU-isn-t-worth-it.640427.0.html)。

现在与 PyTorch GPU 变种进行比较：

```py
 ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    15585   41.385    0.003   41.385    0.003 {method 'item' of 'torch._C.TensorBase' objects}
    20944    6.473    0.000    6.473    0.000 {built-in method torch.stack}
    31416    1.865    0.000    1.865    0.000 {built-in method torch.conv2d}
    41888    1.559    0.000    1.559    0.000 {built-in method torch.relu}
    31416    1.528    0.000    1.528    0.000 {built-in method torch._C._nn.linear}
    31416    1.322    0.000    1.322    0.000 {built-in method torch.max_pool2d}
    10472    1.064    0.000    1.064    0.000 {built-in method torch._C._nn.nll_loss_nd}
    31416    0.952    0.000    7.537    0.001 /Users/mike/Library/Python/3.9/lib/python/site-packages/torch/utils/data/_utils/collate.py:88(collate)
      424    0.855    0.002    0.855    0.002 {method 'run_backward' of 'torch._C._EngineBase' objects}
        5    0.804    0.161   19.916    3.983 /Users/mike/code/cnn/src/python/pytorch/cnn.py:176(eval)
```

有趣的是，最顶层的函数似乎是`Tensor.item()`，它在代码的各个地方被调用，用来计算损失和准确度，可能也在堆栈中更低层的一些层中被引用。在训练过程中移除损失和准确度的追踪，可能会显著提高整体训练性能。

![](img/fc384f2bf49b488781ec37aa290cc236.png)

asitop 功率历史 — PyTorch GPU 模型运行

与 MLX 模型相比，PyTorch 变种似乎在训练过程中没有饱和 GPU（我没有看到它超过 95%），并且 CPU 的[E 核和 P 核](https://developer.apple.com/news/?id=vk3m204o)的使用更为均衡。

有趣的是，MLX 模型更依赖 GPU，但训练速度明显较慢。

两个模型（基于 CPU 或 GPU 的）似乎都没有启用 ANE（[Apple Neural Engine](https://github.com/hollance/neural-engine)）。

# 最后的思考

MLX 很容易上手，对于有使用 PyTorch 和 NumPy 经验的人来说应该也没有问题。尽管一些[开发者文档](https://ml-explore.github.io/mlx/build/html/usage/quick_start.html)有点薄弱，考虑到该工具旨在提供与这些框架 API 兼容的工具，填补任何空白通过相应的 PyTorch 或 NumPy 文档（例如，SGD [[1](https://ml-explore.github.io/mlx/build/html/python/optimizers/_autosummary/mlx.optimizers.SGD.html)] [[2](https://pytorch.org/docs/stable/generated/torch.optim.SGD.html)]）应该很容易。

MLX 模型的整体性能相当不错；我不确定自己是否期望它始终优于 PyTorch 的`mps`设备支持。虽然通过 PyTorch 在 GPU 上训练似乎要快得多，但对于该模型，单个项预测，尤其是在大规模时，MLX 的速度要快得多。无论这是否是我的 MLX 配置的效果，还是框架本身的属性，很难说（如果是前者——欢迎在 GitHub 上留下问题！）。
