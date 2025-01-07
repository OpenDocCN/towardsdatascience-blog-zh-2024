# PyTorch中的数据集和数据加载器完整指南

> 原文：[https://towardsdatascience.com/comprehensive-guide-to-datasets-and-dataloaders-in-pytorch-4d20f973d5d5?source=collection_archive---------6-----------------------#2024-06-15](https://towardsdatascience.com/comprehensive-guide-to-datasets-and-dataloaders-in-pytorch-4d20f973d5d5?source=collection_archive---------6-----------------------#2024-06-15)

## 完整指南：如何为PyTorch中的不同模型创建自定义数据集和数据加载器

[](https://medium.com/@rtdcunha?source=post_page---byline--4d20f973d5d5--------------------------------)[![Ryan D'Cunha](../Images/7a39859e2b5e5b09ef2c60aaf6bb75ac.png)](https://medium.com/@rtdcunha?source=post_page---byline--4d20f973d5d5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4d20f973d5d5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4d20f973d5d5--------------------------------) [Ryan D'Cunha](https://medium.com/@rtdcunha?source=post_page---byline--4d20f973d5d5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4d20f973d5d5--------------------------------) ·阅读时间：5分钟·2024年6月15日

--

![](../Images/6e4efac0de583f1e865e0158db53f194.png)

来源：GPT4o 生成

在构建机器学习模型之前，您需要将数据加载到数据集中。幸运的是，PyTorch提供了许多命令来帮助完成整个过程（如果你不熟悉PyTorch，建议先复习基础知识[这里](https://medium.com/@rtdcunha/a-beginners-guide-to-pytorch-6bc600ca4b8d)）。

PyTorch提供了丰富的文档来帮助完成这个过程，但我没有找到任何关于自定义数据集的全面文档或教程。首先，我将从创建基本的现成数据集开始，然后逐步学习如何为不同的模型从头开始创建数据集！

# 什么是数据集和数据加载器？

在我们深入探讨不同使用案例的代码之前，首先让我们理解这两个术语之间的区别。通常，你首先创建数据集，然后创建数据加载器。**数据集**包含将输入模型的每个数据点的特征和标签。**数据加载器**是一个自定义的PyTorch可迭代对象，它使得加载数据并附加额外功能变得更加容易。

```py
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None,
           batch_sampler=None, num_workers=0, collate_fn=None,
           pin_memory=False, drop_last=False, timeout=0,
           worker_init_fn=None, *, prefetch_factor=2,
           persistent_workers=False)
```

数据加载器中最常见的参数有*batch_size*、*shuffle*（通常仅用于训练数据）、*num_workers*（用于多进程加载数据）和*pin_memory*（将获取的数据张量放入固定内存中，从而加快数据传输到支持CUDA的GPU的速度）。

> 建议设置pin_memory = True，而不是指定num_workers，因为CUDA的多进程处理会引发一些复杂问题。

# 加载现成数据集

如果你的数据集是从在线或本地下载的，创建数据集将非常简单。我认为 PyTorch 在这方面有很好的[文档](https://pytorch.org/tutorials/beginner/basics/data_tutorial.html)，所以我会简要介绍。

如果你知道数据集来自 PyTorch 或 PyTorch 兼容的来源，只需调用必要的导入和选择的数据集：

```py
from torch.utils.data import Dataset
from torchvision import datasets
from torchvision.transforms imports ToTensor

data = torchvision.datasets.CIFAR10('path', train=True, transform=ToTensor())
```

每个数据集都会有独特的参数需要传入（可以在[这里](https://pytorch.org/vision/main/datasets.html)找到）。通常，它们包括数据集存储路径、一个布尔值表示是否需要下载（通常称为 download）、是否用于训练或测试，以及是否需要应用转换。

# 转换

我在上一节末尾提到过转换可以应用到数据集上，但什么是转换呢？

**transform** 是一种操作数据以进行图像预处理的方法。转换有很多不同的方面。最常见的转换 *ToTensor()* 会将数据集转换为张量（这是输入到任何模型所需要的）。PyTorch 内置的其他转换（*torchvision.transforms*）包括翻转、旋转、裁剪、归一化和图像平移。这些通常用于使模型能够更好地泛化，避免在训练数据上过拟合。如果需要，数据增强还可以用来人为地增加数据集的大小。

> 注意，大多数 torchvision 转换只接受 Pillow 图像或张量格式（不支持 numpy）。要进行转换，只需使用

要从 numpy 转换，可以创建一个 torch 张量，或者使用以下方法：

```py
From PIL import Image
# assume arr is a numpy array
# you may need to normalize and cast arr to np.uint8 depending on format
img = Image.fromarray(arr)
```

可以使用 *torchvision.transforms.compose* 同时应用多个转换。你可以根据数据集的需求组合尽可能多的转换。下面是一个示例：

```py
import torchvision.transforms.Compose

dataset_transform = transforms.Compose([
        transforms.RandomResizedCrop(256),
        transforms.RandomHorizontalFlip(),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
])
```

确保将已保存的转换作为参数传递给数据集，这样它才能在数据加载器中应用。

# 创建自定义数据集

在大多数开发自己模型的情况下，你需要一个自定义数据集。一个常见的用例是迁移学习，将自己的数据集应用于预训练模型。

PyTorch 数据集类有三个必需的部分：**初始化**、**长度**和**获取元素**。

**__init__**: 初始化数据集时，传入原始数据和标签数据。最佳实践是分别传入原始图像数据和标签数据。

**__len__**: 返回数据集的长度。在创建数据集之前，应该检查原始数据和标签数据是否大小相同。

**__getitem__**: 在这里，所有数据处理都会发生，返回给定索引（idx）的原始数据和标签数据。如果需要应用任何转换，数据必须先转换为张量并进行转换。如果初始化时包含了数据集的路径，必须打开路径并在返回之前访问/预处理数据。

语义分割模型的示例数据集：

```py
from torch.utils.data import Dataset
from torchvision import transforms

class ExampleDataset(Dataset):
    """Example dataset"""

    def __init__(self, raw_img, data_mask, transform=None):
        self.raw_img = raw_img
        self.data_mask = data_mask
        self.transform = transform

    def __len__(self):
        return len(self.raw_img)

    def __getitem__(self, idx):
        if torch.is_tensor(idx):
            idx = idx.tolist()

        image = self.raw_img[idx]
        mask = self.data_mask[idx]

        sample = {'image': image, 'mask': mask}

        if self.transform:
            sample = self.transform(sample)

        return sample
```

检查模型第一层的输入非常重要（特别是对于预训练模型），以确保数据的形状与输入形状匹配。如果不匹配，可能需要调整维度。如果输入图像是灰度的 n x n 数组，而模型需要一个通道维度（1 x 256 x 256），这种情况很常见。

在应用数据集和数据加载器后，数据的格式应该是 NCHW（批次大小，通道大小，高度，宽度）。可以在 `__getitem__` 方法中进行重新格式化，然后再输入模型。

## 拆分数据集

在创建数据集时，你可能希望将其拆分为训练集、测试集和验证集。可以使用 PyTorch 内置的函数并指定大小来完成此操作。确保数据集的拆分总和等于数据集的总长度。

```py
from torch.utils.data import random_split

train, val, test = random_split(dataset, [train_size, val_size, test_size])
```

## 数据标签

根据模型的不同，数据标签可能有所不同：分类、目标检测或分割。分类模型的标签如果是多分类，会包含类标签；如果是二分类，则是二进制数。目标检测模型的标签将包含一个坐标框。语义分割模型的标签将包含与原始图像数据大小匹配的二进制掩码。实例分割则包含原始图像数据中的所有掩码数据。

创建数据集是模型开发的基础。数据集存在问题时，会导致训练或评估模型时出现许多错误。最常见的错误是形状或类型不匹配。通过遵循这些指导并参考 PyTorch 文档，你应该能够创建一个有效的数据集！

# 参考文献

[## 数据集与 DataLoader - PyTorch 教程 2.3.0+cu121 文档](https://pytorch.org/tutorials/beginner/basics/data_tutorial.html?source=post_page-----4d20f973d5d5--------------------------------)

### 学习基础知识 || 快速入门 || 张量 || 数据集与 DataLoader || 转换 || 构建模型 || 自动求导 ||…

pytorch.org](https://pytorch.org/tutorials/beginner/basics/data_tutorial.html?source=post_page-----4d20f973d5d5--------------------------------) [](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html?source=post_page-----4d20f973d5d5--------------------------------) [## 编写自定义数据集、DataLoader 和转换 - PyTorch 教程 2.3.0+cu121 文档

### 作者：Sasank Chilamkurthy 在解决任何机器学习问题时，大量工作都集中在数据准备上…

pytorch.org](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html?source=post_page-----4d20f973d5d5--------------------------------)  [## 转换和增强图像 - Torchvision 0.18 文档

### Torchvision 支持在 torchvision.transforms 和 torchvision.transforms.v2 中常见的计算机视觉转换…

pytorch.org](https://pytorch.org/vision/stable/transforms.html?source=post_page-----4d20f973d5d5--------------------------------)  [## 组合 - Torchvision 主要文档

### 在本地运行 PyTorch 或快速开始使用支持的云平台之一

[pytorch.org](https://pytorch.org/vision/main/generated/torchvision.transforms.Compose.html?source=post_page-----4d20f973d5d5--------------------------------)
