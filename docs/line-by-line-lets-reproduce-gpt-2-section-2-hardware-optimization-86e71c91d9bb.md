# 逐行分析，让我们重现 GPT-2：第2节 — 硬件优化

> 原文：[https://towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-2-hardware-optimization-86e71c91d9bb?source=collection_archive---------15-----------------------#2024-07-31](https://towardsdatascience.com/line-by-line-lets-reproduce-gpt-2-section-2-hardware-optimization-86e71c91d9bb?source=collection_archive---------15-----------------------#2024-07-31)

## 本文将逐行分析 Andrej Karpathy 在《让我们重现 GPT-2（124M）》第2节中的硬件优化内容。

[](https://medium.com/@mgunton7?source=post_page---byline--86e71c91d9bb--------------------------------)[![Matthew Gunton](../Images/6f5a9530ad5252aa3f2fae87b3f272b1.png)](https://medium.com/@mgunton7?source=post_page---byline--86e71c91d9bb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--86e71c91d9bb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--86e71c91d9bb--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--86e71c91d9bb--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86e71c91d9bb--------------------------------) ·阅读时间：11分钟·2024年7月31日

--

![](../Images/6eaae334a9023076271e9cf731d1385a.png)

图片来源 — SDXL

快速回顾一下，[在第1节](https://medium.com/towards-data-science/line-by-line-lets-reproduce-gpt-2-section-1-b26684f98492)中，我们逐行分析了 Karpathy 编写的代码，目的是原始地训练 GPT-2。现在我们已经有了我们的设置，Karpathy 向我们展示了如何在我们的 NVIDIA GPU 上加速模型训练！虽然我们知道训练一个好的模型需要花费大量时间，但通过优化每次运行，我们可以节省数天甚至数周的训练时间。这自然为我们提供了更多的迭代次数来改善模型。在本文的最后，你将看到如何通过使用 Ampere 系列的 Nvidia GPU 来大幅加速训练（提高 10 倍）。

为了写这篇博客文章，我在 Google Colab 提供的免费 NVIDIA T4 GPU 和 Lambda Labs 的 NVIDIA A100 40GB SXM4 GPU 上进行了优化。Karpathy 提到的大多数优化方法专为 A100 或更强大的显卡设计，但在较弱的 GPU 上仍然可以获得一些性能提升。

让我们开始吧！

# 代码计时

首先，我们希望创建一种方式来查看我们的优化效果。为此，我们将在训练循环中添加以下代码：

```py
for i in range(50):
    t0 = time.time() # start timer
    x, y = train_loader.next_batch()
    x, y = x.to(device), y.to(device)
    optimizer.zero_grad() 
    logits, loss = model(x, y)
    loss.backward() 
    optimizer.step()
    torch.cuda.synchronize() # synchronize with GPU
    t1 = time.time() # end timer
    dt = (t1-t0)*1000 # milliseconds difference
    print(f"loss {loss.item()}, step {i}, dt {dt:.2f}ms")
```

我们首先捕获循环开始时的时间，但在捕获结束时间之前，我们运行 `torch.cuda.synchronize()`。默认情况下，我们只关注 CPU 停止的时间。因为我们已经将大部分主要计算移至 GPU，所以我们需要确保定时器在此处考虑到 GPU 停止计算的时间。同步操作将使 CPU 等待，直到 GPU 完成其工作队列，从而为我们提供循环完成时的准确时间。一旦我们有了准确的时间，自然就能计算出开始时间和结束时间之间的差异。

# 批量大小

我们还希望确保每轮通过尽可能多的数据。我们实现这一目标的方式是设置批量大小。在我们的 `DataLoaderLite` 类中，我们可以调整 2 个参数（B 和 T），以便在不超出限制的情况下，使用 GPU 中尽可能多的内存。

使用 A100 GPU，你可以按照 Karpathy 的示例，其中我们将 T 设置为最大 `block_size` 的 1024，并将 B 设置为 16，因为它是一个“漂亮”的数字（容易被 2 的幂次方整除），并且是我们可以在内存中容纳的最大“漂亮”数字。

```py
train_loader = DataLoaderLite(B=16, T=1024)
```

如果你尝试输入一个太大的值，你会在终端看到 CUDA 提示 `OutOfMemoryError` 错误。我发现对于 T4 GPU，最佳的值是 B = 4 和 T = 1024（*当在 Google Colab 中尝试不同的 B 值时，请注意，你可能需要重启会话，以确保不会得到错误的 `OutOfMemoryError`*）。

在 A100 和 T4 上运行时，我得到了以下图表，显示了开始训练的时间（平均大约在 T4 上是 1100ms，A100 上是 1040ms）

![](../Images/4982d21ba78b861a4b97a097448bf3a7.png)

作者提供的图像 — A100 训练未进行优化

![](../Images/ffb306ee31f999c89ad7ab83e1e33a44.png)

作者提供的图像 — T4 训练未进行优化

# 浮点优化

现在我们将重点关注在模型内部表示数据时所做的更改。

如果你查看我们在第一节代码中权重的 `dtype`，你会看到我们默认使用 32 位浮点数（fp32）。Fp32 表示我们使用 32 位表示数字，遵循下面的 IEEE 浮动点标准：

![](../Images/dc819a07b517ac882a19fa1a53a3de13.png)

作者提供的图像 — IEEE 浮动点 32（FP32）表示

正如 Karpathy 在视频中所说，我们通过实验证明，fp32 并不是训练高质量模型所必需的——我们可以使用更少的数据来表示每个权重，并且仍然得到高质量的输出。加速计算的一种方法是使用 NVIDIA 的 TensorCore 指令。它将通过将操作数转换为下面所示的 Tensor Float 32（TF32）格式来处理矩阵乘法：

![](../Images/b15aba27d3e3263698eb170290bd1844.png)

作者提供的图像 — Tensor Float 32（TF32）

![](../Images/eddcc5b41eeeae51b530a74cb8e2ec35.png)

作者提供的图像 — TF32 数据流通过 Tensor Core 经过优化

从代码角度来看，我们所有的变量（输入、输出）都是FP32格式，但NVIDIA GPU会将中间矩阵转换为TF32格式以加速。根据NVIDIA的说法，这能带来8倍的加速[相对于FFMA指令](https://docs.nvidia.com/gameworks/content/developertools/desktop/analysis/report/cudaexperiments/kernellevel/achievedflops.htm)。要在PyTorch中启用TF32，我们只需要添加以下代码行（high = TF32, highest = FP32, medium = BF16（稍后会详细介绍））：

```py
torch.set_float32_matmul_precision("high")
```

TensorCore是NVIDIA独有的功能，您只能在A100 GPU或更高版本上运行TF32，因此一些开发者使用浮点16（FP16）作为训练方式。这种表示法的问题在于，FP16能够表示的数据范围比FP32要小，导致无法表示训练所需的相同数据范围。虽然通过梯度扩展可以绕过这一点，但这会增加更多的计算，因此最终会陷入“前进一步，退两步”的困境。

![](../Images/c607218da80ec3a96abac4629db209e6.png)

作者提供的图片 — IEEE浮点16（FP16）表示法

但在他的演示视频中，Karpathy使用的数据优化方法是脑浮点（BF16）。在这种表示法中，我们与FP32有相同数量的指数位，因此可以表示相同的范围，但尾数位较少。这意味着尽管我们有较少的位数，表示数字的精度较低。通过经验来看，这并没有导致性能大幅下降，所以这是一个我们愿意接受的折衷。要在NVIDIA芯片上使用此格式，您需要具备A100。

![](../Images/0d7891f96b11cdaa642777ec0cf19edb.png)

作者提供的图片 — 脑浮点16（BF16）

使用PyTorch时，我们不需要大幅修改代码就可以使用新的数据类型。文档建议我们只在模型的前向传递和损失计算过程中使用这些数据类型。由于我们的代码在1行中完成了这两个操作，我们可以按如下方式修改代码：

```py
for i in range(50):
    t0 = time.time() 
    x, y = train_loader.next_batch()
    x, y = x.to(device), y.to(device)
    optimizer.zero_grad() 
    with torch.autocast(device_type=device, dtype=torch.bfloat16): # bf16 change
        logits, loss = model(x, y)
    loss.backward() 
    optimizer.step()
    torch.cuda.synchronize() 
    t1 = time.time()
    dt = (t1-t0)*1000 
    print(f"loss {loss.item()}, step {i}, dt {dt:.2f}ms")
    loss_arr.append(loss.item())
```

就这样，我们的代码现在已经开始使用BF16运行。

在我们的A100上运行时，现在平均每步大约需要330毫秒！我们已经将运行时间减少了大约70%，而且我们才刚刚开始！

![](../Images/a2fd83867ed991a7df45314ca29e3492.png)

作者提供的图片 — 数据类型优化后的A100训练

# Torch Compile

我们可以通过利用PyTorch Compile功能进一步改善训练时间。这样可以在不调整代码的情况下大幅提升性能。

从高层次来看，每个计算机程序都是以二进制执行的。因为大多数人觉得用二进制编程很困难，所以我们创建了更高层次的语言，让我们能以更容易理解的方式编写代码。当我们编译这些语言时，它们会被转换回我们实际运行的二进制代码。有时，在这种转换过程中，我们可以找到更快的方式来完成相同的计算——比如重用某个变量，甚至干脆跳过某些操作。

```py
# ...
model = GPT(GPTConfig(vocab_size=50304))
model.to(device)
model = torch.compile(model) # new line here
# ...
```

这将我们带到了机器学习和 PyTorch。Python 是一种高级语言，但我们仍然在用它做计算密集型的计算。当我们运行`torch compile`时，我们花费更多时间在编译代码上，但由于我们为了找到这些优化所做的额外工作，我们最终看到我们的运行时间（在这里指的是训练）变得更快。

Karpathy 给出了一个 PyTorch 如何改善计算的示例。我们的 GELU 激活函数可以写成如下：

```py
class TanhGELU(nn.Module):
    def forward(self, input):
        return 0.5 * input * (1.0 + torch.tanh(math.sqrt(2.0/math.pi) * (input + 0.044715 * torch.pow(input, 3.0))))
```

对于你在上面的函数中看到的每个计算，我们都必须在 GPU 中调度一个内核。这意味着，当我们开始进行三次方计算时，我们从高带宽内存（HBM）中提取输入到 GPU 核心，并进行计算。然后，我们将结果写回 HBM，然后开始下一次计算，并重复整个过程。自然，这样的顺序导致我们在等待内存传输时浪费了大量时间。

PyTorch 编译让我们能够看到像这样的低效，并且更加小心地控制何时启动新的内核，从而实现显著的加速。这被称为内核融合。

在这个话题上，我想指出一个优秀的开源项目叫做 Luminal，它将这个思想进一步扩展了。[Luminal 是一个独立的框架，你可以在其中编写训练/推理代码](https://github.com/jafioti/luminal)。通过使用这个框架，你可以访问它的编译器，该编译器通过只考虑有限数量的计算，能够为你找到更多优化。如果你喜欢通过编译快速 GPU 代码来提升运行时间的思路，可以看看这个项目。

当我们现在运行上面的代码时，我们会看到每一步大约需要 145 毫秒（比之前减少了 50%，比原始版本减少了约 86%）。我们为此付出了第一轮大约 40,000 毫秒的代价！由于大多数训练序列的步骤数远超过 50，这个权衡是我们愿意做出的。

![](../Images/77e1cd818db8c6ec28081e44b6f4e121.png)

作者提供的图片 — A100 训练在 Torch Compile 优化后的运行结果

# Flash Attention

我们做的另一个优化是使用 Flash Attention（[查看论文](https://arxiv.org/pdf/2205.14135)）。对于我们来说，代码本身的改动非常简单，但背后的思路值得深入探讨。

```py
y = F.scaled_dot_product_attention(q, k, v, is_causal=True)
```

类似于我们如何将 `TanhGELU` 类压缩成尽可能少的内核，我们也将相同的思维方式应用于注意力机制。在他们的论文《[“FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness”](https://arxiv.org/pdf/2205.14135)》中，作者展示了如何通过融合内核实现 7.6 倍的加速。虽然理论上 `torch compile` 应该能够找到类似的优化，但在实践中我们尚未看到它找到这一点。

![](../Images/0a620953fcf8999cd84bae98c7f64114.png)

图 1 来自《[“FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness”](https://arxiv.org/pdf/2205.14135)》

这篇论文值得深入研究，但简要概述一下，FlashAttention 被设计为对 I/O 友好，从而避免了不必要（且耗时）的内存调用。通过减少这些调用，它们可以显著加速计算。

实施了这个优化后，我们发现现在每一步的平均时间约为 104 毫秒。

![](../Images/a1f72e2c0244950e414977351ca9932a.png)

作者提供的图像 — Flash Attention 优化后的 A100 训练

# 词汇大小变化

最后，我们可以检查所有硬编码的数字，评估它们有多“合适”。当我们这样做时，我们发现词汇大小不能被许多 2 的幂整除，因此在 GPU 内存加载时会更加耗时。我们通过将词汇大小从 50,257 调整为下一个“合适”的数字 50,304 来解决这个问题。这个数字之所以“合适”，是因为它可以被 2、4、8、16、32、64 和 128 整除。

```py
model = GPT(GPTConfig(vocab_size=50304))
```

你可能还记得上一篇博客中提到的，我们的词汇大小并不是一个任意的值 — 它是由我们使用的分词器决定的。因此，问题就来了，当我们任意地增加词汇大小时，会发生什么？在训练过程中，模型会发现这些新的词汇从未出现过，因此它会开始将这些词汇的概率推向 0 —— 所以我们的性能是安全的。不过，这并不意味着没有权衡。通过加载从未使用过的词汇到内存中，我们浪费了时间。然而，从经验上看，我们可以看到加载“合适”的数字能大大弥补这一成本。

通过最后的优化，我们现在每一步的平均时间大约是 100 毫秒。

![](../Images/6e684ff36d2019e35a382dc8544b893e.png)

作者提供的图像 — 词汇大小优化后的 A100 训练

通过这一最终优化，我们发现我们的训练从最初开始已经提高了约 10 倍！

# 哪些优化在 T4 GPU 上有效？

如果你一直在关注但仅能使用消费者级 T4 GPU，你可能会想知道哪些优化是可以使用的。回顾一下，我们不能使用 BF16 表示法，但可以使用词汇大小变化、Flash Attention 和 torch compile。[要查看这段代码的实际应用，查看我为 T4 优化的 Google Colab 笔记本](https://colab.research.google.com/drive/1NhX0dDHF8mpzQGG9vjUJ16qiMbL7QI5Y)。

从下图中我们可以看到，尽管第一次运行 torch compile 确实需要很长时间，但接下来的几轮与未优化版本相比并没有显著提高（在 T4 上大约下降了 8%，而在 A100 上下降了 90%）。

![](../Images/f95034c6a9cd38614158cf644977592e.png)

作者提供的图像 — 在 T4 GPU 上优化运行

尽管如此，当 OpenAI 在训练 GPT-2 时，它运行的是比 T4 更先进的硬件。今天我们能够在 T4 上运行这个工作负载，表明硬件要求正在变得不那么苛刻，这有助于创造一个硬件不再成为机器学习工作障碍的未来。

# 结束语

通过优化我们的代码，我们看到了显著的速度提升，并且也了解了一些训练过程中主要的瓶颈。首先，数据类型对于速度至关重要，因为仅这一改变就大幅提升了速度。其次，我们发现硬件优化在加速计算中发挥着重要作用——因此，GPU 硬件真的是物有所值。最后，编译器优化在这里也起到了关键作用。

要查看我在 A100 上运行的代码，[请查看这个 Gist](https://gist.github.com/matthewjgunton/3e2d9c9be60499cf6460d197917b976e)。如果你有任何关于如何进一步优化硬件的建议，我很乐意在评论区看到它们！

现在是构建的激动人心的时刻！

[1] Karpathy, A., [“让我们重现 GPT-2 (124M)”](https://youtu.be/l8pRSuU81PU?feature=shared) (2024), YouTube

[2] Dao, T. 等人 [“FlashAttention: 快速且内存高效的精确注意力机制，具备 I/O 感知能力”](https://arxiv.org/pdf/2205.14135) (2022), arXiv

[3] Krashinsky, R. 等人 “[NVIDIA 安培架构深度解析](https://developer.nvidia.com/blog/nvidia-ampere-architecture-in-depth/)” (2020), NVIDIA 开发者
