# 机器学习工程 101：对错误“DataLoader worker (pid(s) xxx) exited unexpectedly”的全面解释

> 原文：[https://towardsdatascience.com/ml-engineering-101-a-thorough-explanation-of-the-error-dataloader-worker-pid-s-xxx-exited-f3a6a983911e?source=collection_archive---------6-----------------------#2024-06-03](https://towardsdatascience.com/ml-engineering-101-a-thorough-explanation-of-the-error-dataloader-worker-pid-s-xxx-exited-f3a6a983911e?source=collection_archive---------6-----------------------#2024-06-03)

## 深入探讨 PyTorch DataLoader 与多进程

[](https://mengliuz.medium.com/?source=post_page---byline--f3a6a983911e--------------------------------)[![赵梦柳](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--f3a6a983911e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f3a6a983911e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f3a6a983911e--------------------------------) [赵梦柳](https://mengliuz.medium.com/?source=post_page---byline--f3a6a983911e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f3a6a983911e--------------------------------) ·阅读时长 6 分钟·2024年6月3日

--

作为日常使用 PyTorch 库的众多用户之一，我相信许多机器学习工程师在训练过程中迟早会遇到“DataLoader worker (pid(s) xxx) exited unexpectedly”这个问题。

这令人沮丧。

当使用 *num_workers > 0* 调用 DataLoader 时，通常会触发这个错误。许多在线帖子提供了简单的解决方案，比如将 num_workers 设置为 0，这样当前的问题就会消失，但实际上会引发新的问题。

本文将向你展示一些可能有助于解决这个问题的技巧。我将深入探讨 Torch.multiprocessing 模块，并展示一些有用的虚拟内存监控和泄漏防止技术。在极少数情况下，即使没有内存泄漏，torch.multiprocessing 工作进程的异步内存占用和释放仍然可能触发该问题。最终解决方案是优化虚拟内存的使用，理解 torch.multiprocessing 的行为，并在 __getitem__ 方法中进行垃圾回收。

注意：我使用的平台是 Ubuntu 20.04。为了适应其他平台，许多终端命令需要做相应的调整。

![](../Images/506f6a7810c2c2408d0d19a4c61dc831.png)

图片来源：[https://pxhere.com/en/photo/1379760#google_vignette](https://pxhere.com/en/photo/1379760#google_vignette)

**暴力解决方案及其缺点**

如果你在网上搜索，大多数遇到相同问题的人都会告诉你暴力解决方案；只需将 DataLoader 中的 num_workers 设置为 0，问题就会消失。

如果你的数据集较小，并且能容忍较长的训练时间，这将是最简单的解决方案。然而，根本问题依然存在，如果你有一个非常大的数据集，设置 *num_workers=0* 将导致非常慢的性能，有时会慢 10 倍。因此，我们必须进一步研究问题并寻找替代解决方案。

**监控你的虚拟内存使用情况**

当 DataLoader 工作进程退出时，究竟发生了什么？

要捕捉系统中的最后一个错误日志，请在终端中运行以下命令，它将为你提供更详细的错误信息。

```py
dmesg -T
```

通常，你会看到真实的原因是“内存不足”。但是为什么会出现内存不足的问题呢？具体是什么导致了额外的内存消耗？

当我们在 DataLoader 中设置 *num_workers=0* 时，只有一个主进程运行训练脚本。只要数据批次能够装入内存，它就会正常运行。

然而，当设置 *num_workers > 0* 时，情况就不一样了。DataLoader 会启动子进程，并将 *prefetch_factor*num_workers* 的数据预加载到内存中以加速训练。默认情况下，*prefetch_factor = 2*。预加载的数据将消耗机器的虚拟内存（但好消息是它不会占用 GPU，因此你不需要缩小批次大小）。所以，我们需要做的第一件事是监控系统的虚拟内存使用情况。

监控虚拟内存使用情况的最简单方法之一是使用 *psutil* 包，它将监控虚拟内存的使用百分比

```py
import psutil
print(psutil.virtual_memory().percent)
```

你还可以使用 tracemalloc 包，它将为你提供更详细的信息：

```py
snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')
for stat in top_stats[:10]:
    print(stat)
```

当实际的 RAM 满时，空闲的数据将流入交换空间（因此它是你虚拟内存的一部分）。要检查交换空间，使用以下命令：

```py
free -m
```

在训练期间临时更改交换空间大小（例如，增加到 16G），在终端中执行以下命令：

```py
swapoff -a
fallocate -l 16G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

*/dev/shm*（或者在某些情况下，*/run/shm*）是用于存储临时文件的另一种文件系统，应该进行监控。只需运行以下命令，你将看到文件系统中的驱动器列表：

```py
df -h
```

要临时调整它的大小（例如，增加到 16GB），只需运行：

```py
sudo mount -o remount,size=16G /dev/shm
```

**Torch.multiprocessing 最佳实践**

然而，虚拟内存只是问题的一部分。如果在调整交换磁盘后问题仍然存在怎么办？

问题的另一面是 torch.multiprocessing 模块的底层问题。官方网页上有许多最佳实践建议：

[## 多进程最佳实践 - PyTorch 2.3 文档](https://pytorch.org/docs/stable/notes/multiprocessing.html?source=post_page-----f3a6a983911e--------------------------------#)

### torch.multiprocessing 是 Python 模块的直接替代品。它支持完全相同的操作，但进行了扩展……

[pytorch.org](https://pytorch.org/docs/stable/notes/multiprocessing.html?source=post_page-----f3a6a983911e--------------------------------#)

除了这些，还应该考虑另外三种方法，特别是关于内存使用的方面。

**第一个问题是共享内存泄漏**。泄漏意味着每次子进程运行后，内存没有正确释放，你可以通过监控运行时的虚拟内存使用情况来观察这个现象。内存消耗会不断增加，直到达到“内存不足”的程度。这是典型的内存泄漏现象。

那么，是什么导致了内存泄漏呢？

让我们来看看DataLoader类本身：

[https://github.com/pytorch/pytorch/blob/main/torch/utils/data/dataloader.py](https://github.com/pytorch/pytorch/blob/main/torch/utils/data/dataloader.py)

查看DataLoader的内部结构时，我们会看到，当`nums_worker > 0`时，调用的是`_MultiProcessingDataLoaderIter`。在`_MultiProcessingDataLoaderIter`内部，Torch.multiprocessing创建了工作队列。Torch.multiprocessing使用两种不同的内存共享和缓存策略：*file_descriptor*和*file_system*。虽然*file_system*不需要文件描述符缓存，但它容易导致共享内存泄漏。

要检查你的机器使用的是哪种共享策略，只需在脚本中添加以下内容：

```py
torch.multiprocessing.get_sharing_strategy()
```

要获取系统文件描述符限制（Linux），请在终端运行以下命令：

```py
ulimit -n
```

要将共享策略切换为*file_descriptor*：

```py
torch.multiprocessing.set_sharing_strategy(‘file_descriptor’)
```

要统计已打开的文件描述符数量，请运行以下命令：

```py
ls /proc/self/fd | wc -l
```

只要系统允许，建议使用*file_descriptor*策略。

**第二个问题是多进程工作者启动方法**。简而言之，这是关于是否使用fork或spawn作为工作者启动方法的争论。Fork是Linux中启动多进程的默认方式，可以避免某些文件复制，因此速度更快，但在处理CUDA张量和第三方库（如OpenCV）时，可能会遇到问题。

要使用spawn方法，你只需将参数`*multiprocessing_context=* “spawn”`传递给DataLoader。

**第三，确保数据集对象可序列化/可拾取**

有一篇非常不错的文章进一步讨论了进程折叠中的“按需复制”效应：[https://ppwwyyxx.com/blog/2022/Demystify-RAM-Usage-in-Multiprocess-DataLoader/](https://ppwwyyxx.com/blog/2022/Demystify-RAM-Usage-in-Multiprocess-DataLoader/)

简而言之，**不再推荐**在`__getitem__`方法中创建文件名列表并加载它们。可以创建一个numpy数组或pandas数据框来存储文件名列表，以便进行序列化。如果你熟悉HuggingFace，使用CSV/数据框是加载本地数据集的推荐方法：[https://huggingface.co/docs/datasets/v2.19.0/en/package_reference/loading_methods#datasets.load_dataset.example-2](https://huggingface.co/docs/datasets/v2.19.0/en/package_reference/loading_methods#datasets.load_dataset.example-2)

**如果你的数据加载器非常慢怎么办？**

好的，现在我们对多进程模块有了更好的理解。但这就是故事的结局吗？

听起来真的很疯狂。如果你有一个大且重的数据集（例如，每个数据点 > 5 MB），就有可能遇到上述问题，我将告诉你为什么。秘密就在于多进程工作进程的异步内存释放。

诀窍很简单：黑进 `torch` 库，在 _MultiProcessingDataLoaderIter 类的数据队列前后添加一行 *psutil.virtual_memory().percent*：

[](https://github.com/pytorch/pytorch/blob/70d8bc2da1da34915ce504614495c8cf19c85df2/torch/utils/data/dataloader.py?source=post_page-----f3a6a983911e--------------------------------#L1130) [## pytorch/torch/utils/data/dataloader.py 在 70d8bc2da1da34915ce504614495c8cf19c85df2 ·…

### 使用强大的 GPU 加速的 Python 中的张量和动态神经网络 - pytorch/torch/utils/data/dataloader.py 在…

github.com](https://github.com/pytorch/pytorch/blob/70d8bc2da1da34915ce504614495c8cf19c85df2/torch/utils/data/dataloader.py?source=post_page-----f3a6a983911e--------------------------------#L1130)

类似这样的：

```py
print(“before clearing”, psutil.virtual_memory().percent)
data = self._data_queue.get(timeout=timeout)
print("after", psutil.virtual_memory().percent)
```

在我的情况下，我将 DataLoader 的 `num_workers` 设置为 8，并观察到如下情况：

![](../Images/f36f97f1e7987c83092f6e9ea35452e5.png)

所以内存不断上涨——但这算是内存泄漏吗？其实不算。问题的根本原因是 dataloader 的工作进程加载速度快于释放速度，创建了 8 个任务，却只释放了 2 个。这就是内存溢出的根本原因。解决方案很简单：只需在你的 *__getitem__* 方法的开头添加一个垃圾回收器即可：

```py
import gc
def __getitem__(self, idx):
    gc.collect()
```

现在你已经做好了准备！

![](../Images/3be6a5dc984f45eb7fb8957b5f146e25.png)

**参考文献**

+   [https://pytorch.org/docs/stable/multiprocessing.html#sharing-strategies](https://pytorch.org/docs/stable/multiprocessing.html#sharing-strategies)

+   [https://stackoverflow.com/questions/76491885/how-many-file-descriptors-are-open](https://stackoverflow.com/questions/76491885/how-many-file-descriptors-are-open)

+   [https://psutil.readthedocs.io/en/latest/index.html#psutil.virtual_memory](https://psutil.readthedocs.io/en/latest/index.html#psutil.virtual_memory)

+   [https://stackoverflow.com/questions/4970421/whats-the-difference-between-virtual-memory-and-swap-space](https://stackoverflow.com/questions/4970421/whats-the-difference-between-virtual-memory-and-swap-space)

+   [https://ploi.io/documentation/server/change-swap-size-in-ubuntu](https://ploi.io/documentation/server/change-swap-size-in-ubuntu)

+   [https://stackoverflow.com/questions/58804022/how-to-resize-dev-shm](https://stackoverflow.com/questions/58804022/how-to-resize-dev-shm)

+   [https://pytorch.org/docs/stable/data.html](https://pytorch.org/docs/stable/data.html)

+   [https://britishgeologicalsurvey.github.io/science/python-forking-vs-spawn/](https://britishgeologicalsurvey.github.io/science/python-forking-vs-spawn/)

+   [https://stackoverflow.com/questions/64095876/multiprocessing-fork-vs-spawn](https://stackoverflow.com/questions/64095876/multiprocessing-fork-vs-spawn)
