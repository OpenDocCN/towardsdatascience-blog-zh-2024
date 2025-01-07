# 为工业应用构建视觉检查CNN

> 原文：[https://towardsdatascience.com/building-a-vision-inspection-cnn-for-an-industrial-application-138936d7a34a?source=collection_archive---------2-----------------------#2024-11-21](https://towardsdatascience.com/building-a-vision-inspection-cnn-for-an-industrial-application-138936d7a34a?source=collection_archive---------2-----------------------#2024-11-21)

## 使用PyTorch的逐步方法

[](https://medium.com/@ingo.nowitzky?source=post_page---byline--138936d7a34a--------------------------------)[![Ingo Nowitzky](../Images/00d3560055109732b871c001d2b51ab5.png)](https://medium.com/@ingo.nowitzky?source=post_page---byline--138936d7a34a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--138936d7a34a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--138936d7a34a--------------------------------) [Ingo Nowitzky](https://medium.com/@ingo.nowitzky?source=post_page---byline--138936d7a34a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--138936d7a34a--------------------------------) ·28分钟阅读·2024年11月21日

--

**在本文中，我们为汽车电子行业的视觉检查分类任务开发并编码了一个卷积神经网络（CNN）。在此过程中，我们深入研究了卷积层的概念和数学，并分析了CNN实际“看到”的内容以及哪些图像部分引导它们做出决策。**

# 目录

第一部分：概念背景

第二部分：定义和编码CNN

第三部分：在生产中使用训练好的模型

第四部分：CNN在“决策”中考虑了什么？

# 第一部分：概念背景

## 1.1 任务：将工业部件分类为良品或废品

在自动化装配线的一个工位上，带有两个突出金属销的线圈需要精确地放置在外壳中。金属销插入小的插座中。在某些情况下，销钉略微弯曲，因此无法通过机器连接。视觉检查的任务是识别这些线圈，以便它们可以被自动筛选出来。

![](../Images/5638395e5b713df5831c414d81e1e145.png)

图 1：线圈、外壳和插座 | 图像来源：作者

对于检查，每个线圈会单独拾起，并放置在屏幕前。在这个位置，摄像机会拍摄一张灰度图像。然后，这张图像会被CNN检查并分类为良品或废品。

![](../Images/f746b16fb6a040d9c1429f3f50dba39a.png)

图 2：视觉检查的基本设置和生成的图像 | 图像来源：作者

现在，我们想要定义一个卷积神经网络，能够处理图像并从预先分类的标签中学习。

## 1.2 什么是卷积神经网络（CNN）？

卷积神经网络是 **卷积滤波器** 和 **全连接神经网络**（NN）的组合。CNN 常用于 **图像处理**，如面部识别或视觉检测任务，像我们这个案例中的任务。**卷积滤波器** 是滑过图像并重新计算每个像素的矩阵操作。我们将在本文后面研究卷积滤波器。**滤波器的权重**是 **不是预设的**（例如 Photoshop 中的锐化功能），而是从数据中学习得到的。

## 1.3 卷积神经网络的架构

让我们检查一下 CNN 架构的一个例子。为了方便起见，我们选择 **稍后将实现**的模型。

![](../Images/851e467913c49ffafea1c1dee93d1985.png)

图 3：我们的视觉检测 CNN 架构 | 图片来自作者

我们希望将大小为 400 像素高和 700 像素宽的检测图像输入到 CNN 中。由于图像是灰度图像，相关的 PyTorch 张量大小为 1x400x700。如果使用彩色图像，则会有 3 个输入通道：一个用于红色，一个用于绿色，一个用于蓝色（RGB）。在这种情况下，张量的大小将是 **3**x400x700。

第一个 **卷积滤波器** 有 6 个大小为 5x5 的 **卷积核**，它们滑过图像并生成 6 个独立的新图像，这些图像被称为 **特征图**，大小略微减小（6x396x696）。**ReLU 激活函数** 在图 3 中没有明确显示。它不会改变张量的维度，但会将所有负值设置为零。ReLU 后面是 **MaxPooling** 层，卷积核大小为 2x2。它会将每个图像的宽度和高度减半。

这三层——卷积层、ReLU 层和 MaxPooling 层——会再次实现。这最终给我们带来 16 个特征图，图像的高度为 97 像素，宽度为 172 像素。接下来，所有矩阵值会被展平，并输入到同样大小的全连接神经网络的第一层。它的第二层已经缩减为 120 个神经元。第三层和输出层只有 2 个神经元：一个表示标签“OK”，另一个表示标签“not OK” 或“scrap”。

**如果你还不清楚维度变化的情况，请耐心等待。** 我们将在接下来的章节中详细研究不同类型的层——卷积层、ReLU 层和 MaxPooling 层——如何工作，并如何影响张量维度。

## 1.4 卷积滤波器层

卷积滤波器的任务是寻找图像中的**典型结构/模式**。常用的卷积核尺寸是3x3或5x5。卷积核的9个或25个权重并不是预先指定的，而是在训练过程中学习的（这里假设只有一个输入通道；否则，权重的数量会乘以输入通道数）。卷积核会以定义的**步幅**在图像的矩阵表示上滑动（每个输入通道都有自己的卷积核），在水平和垂直方向上进行卷积。卷积核与矩阵中对应的值相乘并求和。每个滑动位置的求和结果形成新的图像，我们称之为**特征图**。在一个卷积层中，我们可以指定多个卷积核。在这种情况下，我们会得到多个特征图作为结果。卷积核从左到右、从上到下滑动。因此，图4显示了卷积核在**第五个滑动位置**（不包括“...”）的状态。我们可以看到三个输入通道，分别表示红色、绿色和蓝色（RGB）。每个通道只有一个卷积核。在实际应用中，我们通常为每个输入通道定义多个卷积核。

![](../Images/c717dce253abdcbf436fbd671ddd02d6.png)

图4：具有3个输入通道，每个通道1个卷积核的卷积层 | 图片来源：作者

卷积核1在红色输入通道上执行其操作。在当前显示的位置，我们计算该位置在特征图中的新值，计算过程为 *(-0.7)*0 + (-0.9)*(-0.2) + (-0.6)*0.5 + (-0.6)*0.6 + 0.6*(-0.3) + 0.7*(-1) + 0*0.7 + (-0.1)*(-0.1) + (-0.2)*(-0.1) = (-1.33).* 对应的绿色通道（卷积核2）的计算结果为 *-0.14,* 蓝色通道（卷积核3）的结果为 *0.69*。为了得到该滑动位置的最终特征图值，我们将三个通道的值相加并加上偏置（偏置和所有卷积核权重在CNN训练过程中定义）： *(-1.33) + (-0.14) + 0.69 + 0.2 = -0.58*。该值被放置在特征图中黄色高亮的对应位置。

**最后，如果我们将输入矩阵的大小与特征图的大小进行比较，会发现通过卷积操作后，我们丢失了两行和两列。**

## 1.5 ReLU激活层

卷积之后，特征图会通过激活层。激活是必需的，目的是赋予网络**非线性能力**。最常用的激活方法是**Sigmoid**和**ReLU**（修正线性单元）。ReLU激活将所有负值设为零，而正值保持不变。

![](../Images/bc0f10b35021ae5d53fa77063e80234c.png)

图5：特征图的ReLU激活 | 图片来源：作者

在图5中，我们可以看到特征图的值逐元素通过了ReLU激活。

**ReLU激活对特征图的维度没有影响。**

## 1.6 最大池化层

池化层的主要任务是减少特征图的大小，同时保留对分类重要的信息。通常，我们可以通过计算卷积核区域的**平均值**或返回**最大值**来进行池化。在大多数应用中，MaxPooling更有益，因为它**减少了数据中的噪声**。池化的典型卷积核大小为2x2或3x3。

![](../Images/2210088b4c87a939a7793bfacc32185e.png)

图6：使用2x2卷积核的MaxPooling和AvgPooling | 图像来自作者

在图6中，我们看到使用2x2卷积核的MaxPooling和AvgPooling的示例。特征图被划分为与卷积核大小相同的区域，在这些区域内，我们选择最大值（→ MaxPooling）或平均值（→ AvgPooling）。

**通过使用2x2的卷积核进行池化，我们将特征图的高度和宽度减半。**

## 1.7 卷积神经网络中的张量维度

现在我们已经研究了卷积滤波器、ReLU激活函数和池化，我们可以回顾一下图3及张量的维度。我们从一个大小为400x700的图像开始。由于它是灰度图像，所以只有1个通道，相应的张量大小为1x400x700。我们对图像应用6个大小为5x5、步长为1x1的卷积滤波器。每个滤波器返回自己的特征图，因此我们获得6个特征图。由于与图4中使用的卷积核相比（5x5代替了3x3），这次卷积会丢失4列和4行。因此，返回的张量大小为6x396x696。

在下一步中，我们对特征图应用2x2卷积核的MaxPooling（每个图都有自己的池化核）。正如我们所学，这将特征图的维度缩小一倍。因此，张量现在的大小为6x198x348。

现在我们应用16个大小为5x5的卷积滤波器。每个滤波器的内核深度为6，这意味着每个滤波器为输入张量的6个通道提供一个单独的层。每个卷积核层在6个输入通道中的一个上滑动，如图4所示，6个返回的特征图相加合成一个。因此，到目前为止，我们只考虑了一个卷积滤波器，但实际上我们有16个滤波器。这就是为什么我们得到16个新的特征图，每个比输入小4列和4行。此时张量的大小是16x194x344。

再次，我们应用2x2卷积核的MaxPooling。由于这会将特征图的大小减半，所以现在我们得到的张量大小为16x97x172。

最后，张量被展平，这意味着我们将所有*16*97*172 = 266,944*个值排列成一行，并将它们输入到一个相应大小的全连接神经网络中。

# 第二部分：定义和编码CNN

从概念上讲，我们已经具备了所需的一切。接下来，让我们进入第1.1章中描述的工业应用案例。

## 2.1 加载所需的库

我们将使用一些PyTorch库来进行数据加载、采样以及模型本身的构建。此外，我们还加载`matplotlib.pyplot`用于可视化，`PIL`用于转换图像。

```py
import torch
import torch.nn as nn
from torch.utils.data import DataLoader, Dataset
from torch.utils.data.sampler import WeightedRandomSampler
from torch.utils.data import random_split
from torchvision import datasets, transforms
import matplotlib.pyplot as plt
import numpy as np
from PIL import Image
import os
import warnings
warnings.filterwarnings("ignore")
```

## 2.2 配置设备并指定超参数

在`device`中，我们存储`‘cuda’`或`‘cpu’`，具体取决于你的计算机是否有可用的GPU。`minibatch_size`定义了在模型训练过程中每次矩阵操作处理的图像数量。`learning_rate`指定了反向传播过程中参数调整的幅度，而`epochs`定义了我们在训练阶段处理整个训练数据集的频率。

```py
# Device configuration
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"Using {device} device")

# Specify hyperparameters
minibatch_size = 10
learning_rate = 0.01
epochs = 60
```

## 2.3 自定义加载器函数

为了加载图像，我们定义了一个`custom_loader`。它以二进制模式打开图像，裁剪图像的内部700x400像素，将其加载到内存中，并返回加载的图像。作为图像的路径，我们定义了相对路径`data/Coil_Vision/01_train_val_test`。请确保数据存储在你的工作目录中。你可以从我的Dropbox下载文件：[CNN_data.zip](https://www.dropbox.com/scl/fi/z8kkui6ync57rudlx10dx/CNN_data.zip?rlkey=27787pjnnbaa3mss5nu4nbi7u&st=4q3p56fh&dl=0)。

```py
# Define loader function
def custom_loader(path):
    with open(path, 'rb') as f:
        img = Image.open(f)
        img = img.crop((50, 60, 750, 460))  #Size: 700x400 px
        img.load()
        return img

# Path of images (local to accelerate loading)
path = "data/Coil_Vision/01_train_val_test"
```

## 2.4 定义数据集

我们将数据集定义为由图像数据和标签组成的元组，标签为*0*表示废品，*1*表示合格品。方法`datasets.ImageFolder()`从文件夹结构中读取标签。我们使用变换函数首先将图像数据加载为PyTorch张量（值介于0和1之间），然后用大约0.5的均值和0.5的标准差对数据进行归一化。经过变换后，图像数据大致呈标准正态分布（均值=0，标准差=1）。我们将数据集随机划分为50%的训练数据、30%的验证数据和20%的测试数据。

```py
# Transform function for loading
transform = transforms.Compose([transforms.ToTensor(),
                                transforms.Normalize((0.5), (0.5))])

# Create dataset out of folder structure
dataset = datasets.ImageFolder(path, transform=transform, loader=custom_loader)
train_set, val_set, test_set = random_split(dataset, [round(0.5*len(dataset)), 
                                                      round(0.3*len(dataset)), 
                                                      round(0.2*len(dataset))])
```

## 2.5 平衡数据集

我们的数据是不平衡的。合格样本远多于废品样本。为了减少训练过程中对多数类的偏倚，我们使用`WeightedRandomSampler`在采样时给少数类分配更高的概率。在`lbls`中，我们存储了训练数据集的标签。使用`np.bincount()`，我们统计*0*标签（`bc[0]`）和*1*标签（`bc[1]`）的数量。接下来，我们计算两个类的概率权重（`p_nOK`和`p_OK`），并按照数据集中的顺序将它们安排在列表`lst_train`中。最后，我们从`WeightedRandomSampler`实例化`train_sampler`。

```py
# Define a sampler to balance the classes
# training dataset
lbls = [dataset[idx][1] for idx in train_set.indices]
bc = np.bincount(lbls)
p_nOK = bc.sum()/bc[0]
p_OK = bc.sum()/bc[1]
lst_train = [p_nOK if lbl==0 else p_OK for lbl in lbls]
train_sampler = WeightedRandomSampler(weights=lst_train, num_samples=len(lbls))
```

## 2.6 定义数据加载器

最后，我们为训练、验证和测试数据定义了三个数据加载器。数据加载器按批次将数据集输入到神经网络中，每个批次包括图像数据和标签。

对于`train_loader`和`val_loader`，我们将批次大小设置为10，并打乱数据。`test_loader`使用打乱的数据和批次大小为1。

```py
# Define loader with batchsize
train_loader = DataLoader(dataset=train_set, batch_size=minibatch_size, sampler=train_sampler)
val_loader = DataLoader(dataset=val_set, batch_size=minibatch_size, shuffle=True)
test_loader = DataLoader(dataset=test_set, shuffle=True)
```

## 2.7 检查数据：绘制5个合格品和5个废品

为了检查图像数据，我们绘制了五个正常样本（“OK”）和五个废料样本（“nOK”）。为此，我们定义了一个`matplotlib`图形，包含2行5列，并共享x轴和y轴。在代码核心部分，我们嵌套了两个for循环。外层循环从`train_loader`接收数据批次。每个批次包含十张图片和相应的标签。内层循环枚举批次标签。在其主体中，我们检查标签是否等于*0*——如果是，则将图像绘制在第二行的“nOK”下——或者标签是否等于*1*——如果是，则将图像绘制在第一行的“OK”下。一旦`count_OK`和`count_nOK`都大于或等于5，我们就跳出循环，设置标题并显示图形。

```py
# Figure and axes object
fig, axs = plt.subplots(nrows=2, ncols=5, figsize=(20,7), sharey=True, sharex=True)

count_OK = 0
count_nOK = 0

# Loop over loader batches
for (batch_data, batch_lbls) in train_loader:

    # Loop over batch_lbls
    for i, lbl in enumerate(batch_lbls):

        # If label is 0 (nOK) plot image in row 1
        if (lbl.item() == 0) and (count_nOK < 5):
            axs[1, count_nOK].imshow(batch_data[i][0], cmap='gray')
            axs[1, count_nOK].set_title(f"nOK Part#: {str(count_nOK)}", fontsize=14)
            count_nOK += 1

        # If label is 1 (OK) plot image in row 0
        elif (lbl.item() == 1) and (count_OK < 5):
            axs[0, count_OK].imshow(batch_data[i][0], cmap='gray')
            axs[0, count_OK].set_title(f"OK Part#: {str(count_OK)}", fontsize=14)
            count_OK += 1

    # If both counters are >=5 stop looping
    if (count_OK >=5) and (count_nOK >=5):
        break

# Config the plot canvas
fig.suptitle("Sample plot of OK and nonOK Parts", fontsize=24)
plt.setp(axs, xticks=[], yticks=[]) 
plt.show()
```

![](../Images/085c2afc94e541383abe19d7c03f1578.png)

图7：OK（上排）和非OK（下排）部分的示例 | 图片由作者提供

在图7中，我们看到大多数nOK样本明显弯曲，但有少数样本肉眼难以区分（例如，右下角的样本）。

## 2.8 定义CNN模型

该模型对应于图3中所示的架构。我们将灰度图像（只有一个通道）输入到第一层卷积层，并定义6个大小为5（即5x5）的卷积核。卷积操作后接ReLU激活和一个大小为2（2x2）的最大池化层，步幅为2（2x2）。所有这三项操作都按照图3中显示的维度进行重复。在`__init__()`方法的最后一个模块中，16个特征图被展平，并输入到一个大小相等的线性层，该层有120个输出节点。它经过ReLU激活，并在第二个线性层中被缩减至仅2个输出节点。

在`forward()`方法中，我们简单地调用模型的各个层，并将`x`张量输入其中。

```py
class CNN(nn.Module):

    def __init__(self):
        super().__init__()

        # Define model layers
        self.model_layers = nn.Sequential(

            nn.Conv2d(in_channels=1, out_channels=6, kernel_size=5),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2),

            nn.Conv2d(in_channels=6, out_channels=16, kernel_size=5),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2),

            nn.Flatten(),
            nn.Linear(16*97*172, 120),
            nn.ReLU(),
            nn.Linear(120, 2)
        )

    def forward(self, x):
        out = self.model_layers(x)
        return out
```

## 2.9 实例化模型并定义损失函数和优化器

我们从CNN类实例化`model`，并将其放置在CPU或GPU上。由于我们进行的是分类任务，我们选择了CrossEntropyLoss函数。为了管理训练过程，我们调用了随机梯度下降（SGD）优化器。

```py
# Define model on cpu or gpu
model = CNN().to(device)

# Loss and optimizer
loss = nn.CrossEntropyLoss()

optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
```

## 2.10 检查模型的大小

为了了解模型的参数规模，我们遍历`model.parameters()`并进行求和，首先求出所有模型参数的总和（`num_param`），然后是那些在反向传播过程中会被调整的参数（`num_param_trainable`）。最后，我们打印结果。

```py
# Count number of parameters / thereof trainable
num_param = sum([p.numel() for p in model.parameters()])
num_param_trainable = sum([p.numel() for p in model.parameters() if p.requires_grad == True])

print(f"Our model has {num_param:,} parameters. Thereof trainable are {num_param_trainable:,}!")
```

![](../Images/70cbef867acfafd919547b274f3ec920.png)

打印输出告诉我们，模型有超过3200万个参数，其中所有参数都是可训练的。

## 2.11 定义用于验证和测试的函数

在开始模型训练之前，让我们准备一个函数来支持验证和测试。函数`val_test()`将`dataloader`和CNN `model`作为参数。它通过`torch.no_grad()`关闭梯度计算，并遍历`dataloader`。每次拿到一批图像和标签后，将图像输入到`model`中，并通过`output.argmax(1)`在返回的logits上确定模型预测的类别。该方法返回最大值的索引，在我们的例子中，代表类别的索引。

我们统计并汇总正确的预测结果，同时保存图像数据、预测类别和错误预测的标签。最后，我们计算准确率，并将其与误分类的图像一并作为函数的输出返回。

```py
def val_test(dataloader, model):
    # Get dataset size
    dataset_size = len(dataloader.dataset)

    # Turn off gradient calculation for validation
    with torch.no_grad():
        # Loop over dataset
        correct = 0
        wrong_preds = []
        for (images, labels) in dataloader:
            images, labels = images.to(device), labels.to(device)

            # Get raw values from model
            output = model(images)

            # Derive prediction
            y_pred = output.argmax(1)

            # Count correct classifications over all batches
            correct += (y_pred == labels).type(torch.float32).sum().item()

            # Save wrong predictions (image, pred_lbl, true_lbl)
            for i, _ in enumerate(labels):
                if y_pred[i] != labels[i]:
                    wrong_preds.append((images[i], y_pred[i], labels[i]))

        # Calculate accuracy
        acc = correct / dataset_size

    return acc, wrong_preds
```

## 2.12 模型训练

模型训练包含两个嵌套的for循环。外层循环遍历预定义的`epochs`次数，内层循环遍历`train_loader`。枚举返回一批图像数据及其对应的标签。图像数据（`images`）传递给模型，我们接收模型的输出logits（`outputs`）。`outputs`和真实的`labels`一起传入损失函数。根据损失`l`，我们执行反向传播，并通过`optimizer.step`更新参数。`outputs`是一个维度为*batchsize x output nodes*的张量，在我们这里是*10 x 2*。我们通过行中最大值的索引来接收模型的预测，值为*0*或*1*。

最后，我们统计正确预测的数量（`n_correct`）、正确的OK部分（`n_true_OK`）和样本的数量（`n_samples`）。每隔一个epoch，我们计算训练准确率、正确OK部分的比例，并调用验证函数（`val_test()`）。训练过程中，每个epoch的这三个值都会打印出来供参考。在最后一行代码中，我们将模型及其所有参数保存在`“model.pth”`文件中。

```py
acc_train = {}
acc_val = {}
# Iterate over epochs
for epoch in range(epochs):

    n_correct=0; n_samples=0; n_true_OK=0
    for idx, (images, labels) in enumerate(train_loader):
        model.train()
        # Push data to gpu if available
        images, labels = images.to(device), labels.to(device)

        # Forward pass
        outputs = model(images)
        l = loss(outputs, labels)

        # Backward and optimize
        optimizer.zero_grad()
        l.backward()
        optimizer.step()

        # Get prediced labels (.max returns (value,index))
        _, y_pred = torch.max(outputs.data, 1)

        # Count correct classifications
        n_correct += (y_pred == labels).sum().item()
        n_true_OK += (labels == 1).sum().item()
        n_samples += labels.size(0)

    # At end of epoch: Eval accuracy and print information
    if (epoch+1) % 2 == 0:
        model.eval()
        # Calculate accuracy
        acc_train[epoch+1] = n_correct / n_samples
        true_OK = n_true_OK / n_samples
        acc_val[epoch+1] = val_test(val_loader, model)[0]

        # Print info
        print (f"Epoch [{epoch+1}/{epochs}], Loss: {l.item():.4f}")
        print(f"      Training accuracy: {acc_train[epoch+1]*100:.2f}%")
        print(f"      True OK: {true_OK*100:.3f}%")
        print(f"      Validation accuracy: {acc_val[epoch+1]*100:.2f}%")

# Save model and state_dict
torch.save(model, "model.pth")
```

![](../Images/a702275ce6449b59a8c30efeb185e881.png)

训练在我笔记本的GPU上只需几分钟。强烈建议从本地驱动加载图像，否则训练时间可能会增加几个数量级！

训练的输出表明损失已经显著减少，验证准确率——即模型未用于更新参数的数据上的准确率——已达到98.4%。

如果我们绘制训练过程中每个epoch的训练和验证准确率，将能更好地了解训练进展。由于我们每隔一个epoch就保存一次值，因此可以轻松地做到这一点。

我们创建一个`matplotlib`图形和坐标轴，使用`plt.subplots()`并根据准确率字典的键绘制这些值。

```py
# Instantiate figure and axe object
fig, ax = plt.subplots(figsize=(10,6))
plt.plot(list(acc_train.keys()), list(acc_train.values()), label="training accuracy")
plt.plot(list(acc_val.keys()), list(acc_val.values()), label="validation accuracy")
plt.title("Accuracies", fontsize=24)
plt.ylabel("%", fontsize=14)
plt.xlabel("Epochs", fontsize=14)
plt.setp(ax.get_xticklabels(), fontsize=14)
plt.legend(loc='best', fontsize=14)
plt.show()
```

![](../Images/9cca05ab59a7fc07400f9869927832f6.png)

图8：模型训练期间的训练和验证准确率 | 图片由作者提供

## 2.13 加载训练好的模型

如果你想将模型用于生产而不仅仅是用于学习，强烈建议保存并加载包含所有参数的模型。保存已经是训练代码的一部分，从你的驱动器加载模型同样简单。

```py
# Read model from file
model = torch.load("model.pth")
model.eval()
```

## 2.14 使用测试数据再次检查模型准确度

请记住，我们保留了20%的数据用于测试。这些数据对模型来说是全新的，从未加载过。我们可以使用这组全新的数据来再次验证验证准确率。由于验证数据已经加载，但从未用于更新模型参数，我们预期其准确率与测试值相似。为了进行测试，我们在`test_loader`上调用`val_test()`函数。

```py
print(f"test accuracy: {val_test(test_loader,model)[0]*100:0.1f}%")
```

![](../Images/f93ebfbd16df5d984332370c25cf3a6c.png)

在这个特定的例子中，我们达到了99.2%的测试准确度，但这很大程度上取决于随机性（记住：图像随机分配到训练、验证和测试数据中）。

## 2.15 可视化错误分类的图像

错误分类图像的可视化非常直接。首先，我们调用`val_test()`函数。它返回一个元组，第一个索引位置是准确度值 *0*（`tup[0]`），第二个索引位置是另一个元组（`tup[1]`），包含错误分类图像的数据（`tup[1][0]`）、预测标签（`tup[1][1]`）和真实标签（`tup[1][2]`）。如果`tup[1]`不为空，我们会枚举它，并绘制错误分类图像，并加上适当的标题。

```py
%matplotlib inline

# Call test function
tup = val_test(test_loader, model)

# Check if wrong predictions occur
if len(tup[1])>=1:

    # Loop over wrongly predicted images
    for i, t in enumerate(tup[1]):
        plt.figure(figsize=(7,5))
        img, y_pred, y_true = t
        img = img.to("cpu").reshape(400, 700)
        plt.imshow(img, cmap="gray")
        plt.title(f"Image {i+1} - Predicted: {y_pred}, True: {y_true}", fontsize=24)
        plt.axis("off")
        plt.show()
        plt.close()
else:
    print("No wrong predictions!")
```

在我们的例子中，只有一张错误分类的图像，它占测试数据集的0.8%（我们有125张测试图像）。这张图像被分类为合格，但标签为nOK。坦白说，我也会错误分类它 :).

![](../Images/1dded0c32adb73555618ffa75c2b3bdc.png)

图 9：错误分类的图像 | 图片来自作者

# 第三部分：在生产中使用训练好的模型

## 3.1 加载模型、所需库和参数

在生产阶段，我们假设CNN模型已经训练好，并且参数可以加载。我们的目标是将新图像加载到模型中，让模型判断相应的电子组件是否适合用于组装（见章节 *1.1 任务：将工业组件分类为合格或废料*）。

我们首先加载所需的库，将设备设置为`‘cuda’`或`‘cpu’`，定义`CNN`类（与章节2.8完全相同），然后使用`torch.load()`从文件中加载模型。我们需要在加载参数之前定义`CNN`类，否则参数将无法正确分配。

```py
# Load the required libraries
import torch
import torch.nn as nn
from torch.utils.data import DataLoader, Dataset
from torchvision import datasets, transforms
import matplotlib.pyplot as plt
from PIL import Image
import os

# Device configuration
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

# Define the CNN model exactly as in chapter 2.8
class CNN(nn.Module):

    def __init__(self):
        super(CNN, self).__init__()

        # Define model layers
        self.model_layers = nn.Sequential(

            nn.Conv2d(in_channels=1, out_channels=6, kernel_size=5),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2),

            nn.Conv2d(in_channels=6, out_channels=16, kernel_size=5),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2),

            nn.Flatten(),
            nn.Linear(16*97*172, 120),
            nn.ReLU(),
            nn.Linear(120, 2),
            #nn.LogSoftmax(dim=1)
        )

    def forward(self, x):
        out = self.model_layers(x)
        return out

# Load the model's parameters
model = torch.load("model.pth")
model.eval()
```

通过运行这段代码，我们已经将CNN模型加载并在计算机内存中进行了参数化。

## 3.2 将图像加载到数据集中

对于训练阶段，我们需要准备图像以供CNN模型处理。我们从指定文件夹中加载图像，裁剪出内部700x400像素，并将图像数据转换为PyTorch张量。

```py
# Define custom dataset
class Predict_Set(Dataset):
    def __init__(self, img_folder, transform):
        self.img_folder = img_folder
        self.transform = transform
        self.img_lst = os.listdir(self.img_folder)

    def __len__(self):
        return len(self.img_lst)

    def __getitem__(self, idx):
        img_path = os.path.join(self.img_folder, self.img_lst[idx])
        img = Image.open(img_path)
        img = img.crop((50, 60, 750, 460))  #Size: 700x400
        img.load()
        img_tensor = self.transform(img)
        return img_tensor, self.img_lst[idx]
```

我们在一个自定义数据集类 `Predict_Set()` 中执行所有这些步骤。在 `__init__()` 中，我们指定图像文件夹，接受一个 `transform` 函数，并将图像从文件夹加载到列表 `self.img_lst` 中。方法 `__len__()` 返回图像文件夹中的图像数量。`__getitem__()` 从文件夹路径和图像名称组合出图像路径，裁剪图像的内部部分（如同我们对训练数据集所做的那样），并对图像应用 `transform` 函数。最后，它返回图像张量和图像名称。

## 3.3 路径、变换函数和数据加载器

数据准备的最后一步是定义一个数据加载器，它允许我们遍历图像进行分类。在这个过程中，我们指定了图像文件夹的 `path` 并定义了 `transform` 函数作为一个管道，首先将图像数据加载为 PyTorch 张量，其次将数据标准化到大约 -1 到 +1 的范围。我们将自定义数据集 `Predict_Set()` 实例化为变量 `predict_set`，并定义数据加载器 `predict_loader`。由于我们没有指定批处理大小，`predict_loader` 每次返回一张图像。

```py
# Path to images (preferably local to accelerate loading)
path = "data/Coil_Vision/02_predict"

# Transform function for loading
transform = transforms.Compose([transforms.ToTensor(),
                                transforms.Normalize((0.5), (0.5))])

# Create dataset as instance of custom dataset
predict_set = Predict_Set(path, transform=transform)

# Define loader
predict_loader = DataLoader(dataset=predict_set)
```

## 3.4 分类的自定义函数

到目前为止，图像数据的分类准备工作已经完成。然而，我们仍然缺少一个自定义函数，它将图像传输到 CNN 模型中，翻译模型的响应为分类结果，并返回分类结果。这正是我们通过 `predict()` 完成的任务。

```py
def predict(dataloader, model):

    # Turn off gradient calculation
    with torch.no_grad():

        img_lst = []; y_pred_lst = []; name_lst = []
        # Loop over data loader
        for image, name in dataloader:
            img_lst.append(image)
            image = image.to(device)

            # Get raw values from model
            output = model(image)

            # Derive prediction
            y_pred = output.argmax(1)
            y_pred_lst.append(y_pred.item())
            name_lst.append(name[0])

    return img_lst, y_pred_lst, name_lst
```

`predict()` 接受数据加载器和 CNN 模型作为其参数。在其核心，它会遍历数据加载器，将图像数据传输到模型中，并通过 `output.argmax(1)` 解析模型的响应作为分类结果——*0* 表示报废部件（nOK），*1* 表示合格部件（OK）。图像数据、分类结果和图像名称被附加到列表中，列表作为函数的结果返回。

## 3.5 预测标签并绘制图像

最后，我们希望利用自定义函数和加载器来分类新的图像。在文件夹 `“data/Coil_Vision/02_predict”` 中，我们预留了四张电子元件的图像，等待检查。请记住，我们希望 CNN 模型告诉我们，是否可以将这些元件用于自动组装，或者是否需要将它们筛选出来，因为这些引脚可能会在插入插座时造成问题。

我们调用自定义函数 `predict()`，它返回一组图像列表、分类结果列表和图像名称列表。我们遍历这些列表并绘制图像，名称和分类结果作为标题。

```py
# Predict labels for images
imgs, lbls, names  = predict(predict_loader, model)

# Iterate over classified images
for idx, image in enumerate(imgs):
    plt.figure(figsize=(8,6))
    plt.imshow(image.squeeze(), cmap="gray")
    plt.title(f"\nFile: {names[idx]}, Predicted label: {lbls[idx]}", fontsize=18)
    plt.axis("off")
    plt.show()
    plt.close()
```

![](../Images/c6ae72f8ff91d66a8f74c53bcfde4e82.png)

图 10：生产阶段的分类结果 | 图片来源：作者

我们可以看到，左侧的两张图片被分类为合格（标签*1*），右侧的两张则被分类为废品（标签*0*）。由于我们的训练数据，模型非常敏感，即使是针脚的微小弯曲也会导致它们被分类为废品。

# 第4部分：CNN在其“决策”中考虑了什么？

到目前为止，我们已经深入探讨了卷积神经网络（CNN）及其在工业应用中的使用案例。这似乎是一个很好的机会，可以进一步了解CNN模型在处理图像数据时“看到了”什么。为此，我们首先研究卷积层，然后检查图像的哪些部分对分类尤为重要。

## 4.1 研究卷积滤波器的维度

为了更好地理解卷积滤波器的工作原理及其对图像的影响，让我们更详细地检查我们工业示例中的层。

为了访问这些层，我们枚举`model.children()`，这是一个模型结构的生成器。如果某层是卷积层，我们将其添加到`all_layers`列表中，并将权重的维度保存在`conv_weights`中。如果层是ReLU或MaxPooling层，则没有权重。在这种情况下，我们将层和*“”*添加到相应的列表中。接下来，我们枚举`all_layers`，打印层的类型和权重的维度。

```py
# Empty lists to store the layers and the weights
all_layers = []; conv_weights = []

# Iterate over the model's structure
# (First level nn.Sequential)
for _, layer in enumerate(list(model.children())[0]):
    if type(layer) == nn.Conv2d:
        all_layers.append(layer)
        conv_weights.append(layer.weight)
    elif type(layer) in [nn.ReLU, nn.MaxPool2d]:
        all_layers.append(layer)
        conv_weights.append("*")

# Print layers and dimensions of weights
for idx, layer in enumerate(all_layers):
    print(f"{idx+1}. Layer: {layer}")
    if type(layer) == nn.Conv2d:
        print(f"          weights: {conv_weights[idx].shape}")
    else:
        print(f"          weights: {conv_weights[idx]}")
    print()
```

![](../Images/ca5c83dbeeab4d0d96467902c05f25d6.png)

图11：层及权重的维度

请将代码片段的输出与图3进行对比。第一层卷积层有一个输入——只有一个通道的原始图像——并返回六个特征图。我们应用六个卷积核，每个深度为1，大小为5x5。因此，权重的维度是`torch.Size([6, 1, 5, 5])`。相比之下，第4层接受六个特征图作为输入，并返回16个特征图作为输出。我们应用16个卷积核，每个深度为6，大小为5x5。因此，权重的维度为`torch.Size([16, 6, 5, 5])`。

## 4.2 可视化卷积滤波器的权重

现在，我们已经知道了卷积滤波器的维度。接下来，我们想查看它们在训练过程中获得的权重。由于我们有很多不同的滤波器（第一层有6个，第二层有16个），我们在这两种情况下都选择第一个输入通道（索引*0*）。

```py
import itertools

# Iterate through all layers
for idx_out, layer in enumerate(all_layers):

    # If layer is a convolutional filter
    if type(layer) == nn.Conv2d:

        # Print layer name
        print(f"\n{idx_out+1}. Layer: {layer} \n")

        # Prepare plot and weights
        plt.figure(figsize=(25,6))
        weights = conv_weights[idx_out][:,0,:,:] # only first input channel
        weights = weights.detach().to('cpu')

        # Enumerate over filter weights (only first input channel)
        for idx_in, f in enumerate(weights):
            plt.subplot(2,8, idx_in+1)
            plt.imshow(f, cmap="gray")
            plt.title(f"Filter {idx_in+1}")

            # Print texts
            for i, j in itertools.product(range(f.shape[0]), range(f.shape[1])):
                if f[i,j] > f.mean():
                    color = 'black'
                else:
                    color = 'white'
                plt.text(j, i, format(f[i, j], '.2f'), horizontalalignment='center', verticalalignment='center', color=color)

            plt.axis("off")
        plt.show()
        plt.close() 
```

我们遍历`all_layers`。如果某层是卷积层（`nn.Conv2d`），则打印该层的索引及其核心数据。接下来，我们准备一个图表，并以第一个输入层为例提取权重矩阵。我们枚举所有输出层并用`plt.imshow()`绘制它们。最后，我们在图像上打印权重的值，以便直观地展示卷积滤波器。

![](../Images/50934d1828dab3a74cf8b06eb2896806.png)

图12：6+16个卷积滤波器的可视化（输入层索引0）| 图片来源：作者

图 12 展示了第 1 层的六个卷积滤波器内核和第 4 层的 16 个内核（对于输入通道*0*）。右上角的模型示意图显示了带红色轮廓的滤波器。我们看到大部分值接近 0，部分值在正负 0.20–0.25 范围内。这些数字表示在图 4 中演示的卷积操作中使用的值。这些给出了特征图，接下来我们将检查这些特征图。

## 4.3 检查特征图

根据图 4，我们通过对输入图像进行卷积，获得了第一批特征图。因此，我们从 `test_loader` 中加载一张随机图像，并将其传送到 CPU（以防你在 GPU 上操作 CNN）。

```py
# Test loader has a batch size of 1
img = next(iter(test_loader))[0].to(device)
print(f"\nImage has shape: {img.shape}\n")

# Plot image
img_copy = img.to('cpu')
plt.imshow(img_copy.reshape(400,700), cmap="gray")
plt.axis("off")
plt.show()
```

![](../Images/96f8a1bc70ca7968e0edb54d93932387.png)

图 13：上述代码的随机图像输出 | 图片来源：作者

现在我们将图像数据 `img` 传递通过第一层卷积层（`all_layers[0]`），并将输出保存在 `results` 中。接下来，我们遍历 `all_layers`，并将前一层操作的输出传递给下一层。这些操作包括卷积、ReLU 激活和 MaxPooling。每个操作的输出我们都会追加到 `results` 中。

```py
# Pass the image through the first layer
results = [all_layers[0](img)]

# Pass the results of the previous layer to the next layer
for idx in range(1, len(all_layers)):  # Start at 1, first layer already passed!
    results.append(all_layers[idx](results[-1]))  # Pass the last result to the layer
```

最后，我们绘制了原始图像、经过第一层（卷积）、第二层（ReLU）、第三层（MaxPooling）、第四层（第二次卷积）、第五层（第二次 ReLU）和第六层（第二次 MaxPooling）处理后的特征图。

![](../Images/b900b20f062aa67d15afdef6e1d59a01.png)![](../Images/57693ff63b838e80604a9863903d5b0a.png)![](../Images/ca87184666e2ce86b0b433a7ebd3a147.png)![](../Images/631942ee46e10f6bb60caf9d733933ba.png)![](../Images/0693ac0798cf3c69b9a9d025d4039bd2.png)![](../Images/e6ae0d8816b7018a826fbcf3db74b4d9.png)![](../Images/2c85133da17c727830b0dd668fbfc08b.png)

图 14：经过卷积、ReLU 和 MaxPooling 层处理后的原始图像和特征图 | 图片来源：作者

我们看到卷积核（对比图 12）重新计算了图像的每个像素。这在特征图中表现为灰度值的变化。与原始图像相比，一些特征图被锐化，或者具有更强的黑白对比，而其他特征图则显得更模糊。

ReLU 操作将深灰色转为黑色，因为负值被设置为零。

MaxPooling 保持图像几乎不变，同时在两个维度上将图像大小减半。

## 4.4 可视化对分类影响最大的图像区域

在我们完成之前，让我们分析一下图像中哪些区域对分类为废料（索引*0*）或良品（索引*1*）至关重要。为此，我们使用梯度加权类别激活映射（gradCAM）。该技术计算训练好的模型相对于预测类别的梯度（梯度显示输入——图像像素——如何影响预测）。每个特征图（即卷积层的输出通道）梯度的平均值构成了计算热图时，用于与特征图相乘的权重。

但让我们逐步看一下。

```py
def gradCAM(x):

    # Run model and predict
    logits = model(x)
    pred = logits.max(-1)[-1] # Returns index of max value (0 or 1)

    # Fetch activations at final conv layer
    last_conv = model.model_layers[:5]
    activations = last_conv(x)

    # Compute gradients with respect to model's prediction
    model.zero_grad()
    logits[0,pred].backward(retain_graph=True)

    # Compute average gradient per output channel of last conv layer
    pooled_grads = model.model_layers[3].weight.grad.mean((1,2,3))

    # Multiply each output channel with its corresponding average gradient
    for i in range(activations.shape[1]):
        activations[:,i,:,:] *= pooled_grads[i]

    # Compute heatmap as average over all weighted output channels
    heatmap = torch.mean(activations, dim=1)[0].cpu().detach()

    return heatmap
```

我们定义一个函数`gradCAM`，它接受输入数据`x`，一个图像或特征图，并返回一个`heatmap`。

在第一个模块中，我们将`x`输入到CNN `model`中，并得到`logits`，这是一个形状为[1, 2]的张量，仅包含两个值。该值表示类别*0*和*1*的预测概率。我们选择较大值的索引作为模型的预测`pred`。

在第二个模块中，我们提取模型的前五层——从第一个卷积层到第二个ReLU层——并将它们保存在`last_conv`中。我们将`x`通过选择的层进行计算，并将输出存储在`activations`中。顾名思义，这些是第二个卷积层的激活值（即特征图）（经过ReLU激活后）。

在第三个模块中，我们对预测类别的logit值`logits[0,pred]`进行反向传播。换句话说，我们计算CNN对于预测的所有梯度。梯度显示了输入数据（原始图像像素）变化对模型输出（即预测结果）的影响。计算结果保存在PyTorch计算图中，直到我们通过`model.zero_grad()`删除它。

在第四个模块中，我们计算输入通道上的梯度平均值，以及图像或特征图的高度和宽度。结果，我们得到16个平均梯度，对应于从第二个卷积层返回的16个特征图。我们将其保存在`pooled_grads`中。

在第五个模块中，我们遍历从第二个卷积层返回的16个特征图，并使用平均梯度`pooled_grads`对其加权。此操作赋予对预测具有重要性（及其像素）的特征图更大的权重，反之亦然。从现在起，`activations`不再保存特征图，而是保存加权后的特征图。

最后，在最后一个模块中，我们计算`heatmap`，即所有`activations`的平均特征图。这就是函数`gradCAM`返回的结果。

在我们可以绘制图像和热力图之前，我们需要将两者进行转换以便叠加。请记住，特征图比原始图像小（参见第1.3章和第1.7章），热力图也是如此。这就是为什么我们需要`upsampleHeatmap()`函数的原因。该函数将像素值缩放到0到255的范围，并将其转换为8位整数格式（这是`cv2`库所需的格式）。它将热力图调整为400x700像素，并对图像和热力图应用颜色映射。最后，我们将70%的热力图和30%的图像叠加，并返回合成图以供绘制。

```py
import cv2

def upsampleHeatmap(map, img):
    m,M = map.min(), map.max()
    i,I = img.min(), img.max()
    map = 255 * ((map-m) / (M-m))
    img = 255 * ((img-i) / (I-i))
    map = np.uint8(map)
    img = np.uint8(img)
    map = cv2.resize(map, (700,400))
    map = cv2.applyColorMap(255-map, cv2.COLORMAP_JET)
    map = np.uint8(map)
    img = cv2.applyColorMap(255-img, cv2.COLORMAP_JET)
    img = np.uint8(img)
    map = np.uint8(map*0.7 + img*0.3)
    return map
```

我们希望将原始图像和热力图叠加展示在同一行中。为此，我们遍历数据加载器`predict_loader`，对图像运行`gradCAM()`函数，对热力图和图像运行`upsampleHeatmap()`函数。最后，我们使用`matplotlib.pyplot`将原始图像和热力图并排绘制。

```py
# Iterate over dataloader
for idx, (image, name) in enumerate(predict_loader):

    # Compute heatmap
    image = image.to(device)
    heatmap = gradCAM(image)
    image = image.cpu().squeeze(0).permute(1,2,0)
    heatmap = upsampleHeatmap(heatmap, image)

    # Plot images and heatmaps
    fig = plt.figure(figsize=(14,5))
    fig.suptitle(f"\nFile: {names[idx]}, Predicted label: {lbls[idx]}\n", fontsize=24)
    plt.subplot(1, 2, 1)
    plt.imshow(image, cmap="gray")
    plt.title(f"Image", fontsize=14)
    plt.axis("off")
    plt.subplot(1, 2, 2)
    plt.imshow(heatmap)
    plt.title(f"Heatmap", fontsize=14)
    plt.tight_layout()
    plt.axis("off")
    plt.show()
    plt.close()
```

![](../Images/e8ac8b63fb261f64205230e96bdc5e9c.png)

图 15：图像与热力图（输出的内两行）| 图像由作者提供

热力图中的蓝色区域对模型决策的影响较小，而黄色和红色区域则非常重要。我们看到，在我们的使用案例中，主要是电子元件的轮廓（尤其是金属引脚）对分类为废料或好件至关重要。当然，这一点非常合理，因为我们的用例主要处理的是弯曲引脚。

# 结论

卷积神经网络（CNN）如今已成为工业环境中常见且广泛使用的视觉检测工具。在我们的使用案例中，通过相对少量的代码行，我们成功定义了一个模型，能够高精度地将电子元件分类为好件或废料。与传统的视觉检测方法相比，最大的优势在于不需要过程工程师在图像中指定视觉标记来进行分类。相反，CNN通过标签化的示例进行学习，并能够将这种知识复制到其他图像。在我们的具体用例中，626张标签化的图像足以进行训练和验证。在更复杂的情况下，训练数据的需求可能会显著增加。

像gradCAM（梯度加权类激活映射）这样的算法在理解图像中哪些区域对模型的决策特别相关方面具有重要帮助。通过这种方式，它们通过增强对模型功能的信任，支持卷积神经网络（CNN）在工业环境中的广泛应用。

在本文中，我们探讨了卷积神经网络的许多内部工作细节。希望您喜欢这次旅程，并且深入理解了CNN的工作原理。
