# 使用神经网络求解微分方程

> 原文：[https://towardsdatascience.com/solving-differential-equations-with-neural-networks-4c6aa7b31c51?source=collection_archive---------2-----------------------#2024-02-06](https://towardsdatascience.com/solving-differential-equations-with-neural-networks-4c6aa7b31c51?source=collection_archive---------2-----------------------#2024-02-06)

## 神经网络如何成为解决微分方程的强大工具，无需使用训练数据

[](https://medium.com/@rodrigopesilva?source=post_page---byline--4c6aa7b31c51--------------------------------)[![Rodrigo Silva](../Images/d260f05ed9887c5072e0590db1481be2.png)](https://medium.com/@rodrigopesilva?source=post_page---byline--4c6aa7b31c51--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c6aa7b31c51--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4c6aa7b31c51--------------------------------) [Rodrigo Silva](https://medium.com/@rodrigopesilva?source=post_page---byline--4c6aa7b31c51--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c6aa7b31c51--------------------------------) ·阅读时间：8分钟·2024年2月6日

--

![](../Images/8dd6dcc5577d527b7f03e38984b6ec26.png)

图片由[Linus Mimietz](https://unsplash.com/@linusmimietz?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/water-drops-macro-photography-XSQHuGGRO3g?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

微分方程是自然科学中的重要工具，广泛应用于工程学、生物学、经济学，甚至社会科学。简而言之，它们告诉我们某一量如何随时间（或其他参数，通常我们关注时间变化）变化。我们可以理解某个种群、股价，甚至某个社会对于某些主题的意见如何随时间变化。

通常，解决微分方程的方法并不是解析性的（即没有“封闭式解”），我们必须依赖数值方法。然而，从计算的角度来看，数值方法可能会非常昂贵，更糟糕的是：积累的误差可能会非常大。

本文将展示神经网络如何成为解决微分方程的有力助手，并且我们如何借鉴物理启发式神经网络的概念来解决这样一个问题：我们能否使用机器学习的方法来求解微分方程？

# 一点点物理启发式神经网络的元素

在本节中，我将简要介绍物理启发式神经网络。我猜你已经了解“神经网络”部分，但是什么使它们受物理学启发呢？嗯，它们并不是直接受物理学启发，而是受（微分）方程的启发。

通常，神经网络被训练来发现模式，并弄清楚一组训练数据的行为。然而，当你训练一个神经网络以遵循你的训练数据的行为，并希望它能适应未见过的数据时，你的模型高度依赖于数据本身，而不是系统的基本性质。这听起来几乎像是一个哲学问题，但实际上比这更具实用性：如果你的数据来自海洋洋流的测量数据，那么这些洋流必须遵循描述洋流的物理方程。然而，请注意，你的神经网络对这些方程完全无知，只是在试图拟合数据点。

这就是物理信息化发挥作用的地方。如果除了学习如何拟合你的数据外，你的模型还学习如何拟合支配该系统的方程，那么你的神经网络的预测将更加精确，并且更好地泛化，这只是物理信息化模型的一些优势。

请注意，系统的控制方程不一定要涉及物理学，“物理信息化”仅仅是一种术语（而且这一技术通常由物理学家使用）。如果你的系统是一个城市的交通，并且你恰好拥有一个想让神经网络预测遵循的良好数学模型，那么物理信息化神经网络非常适合你。

## 我们如何将这些信息传递给模型呢？

希望我已经说服你，值得费心让模型意识到支配我们系统的基本方程。然而，我们如何做到这一点呢？有几种方法，但主要的一种是调整损失函数，使其包含一个额外的项，用于表示控制方程，除了通常与数据相关的部分。也就是说，损失函数 *L* 将由以下和构成：

![](../Images/dcc1113601f71c77b0fa9933bacd9824.png)

这里，数据损失是通常的形式：均方差，或者是其他合适的损失函数形式；但方程部分才是最吸引人的部分。假设你的系统由以下微分方程支配：

![](../Images/47f34c0f8617929070a5eca85e17a597.png)

我们如何将其纳入损失函数呢？嗯，由于训练神经网络的任务是最小化损失函数，我们希望最小化以下表达式：

![](../Images/6808838b818b5eea8947e358a9851645.png)

所以，我们的方程相关损失函数最终变成了

![](../Images/7c629affee0d0c244f4cac4841b471ab.png)

也就是说，它是我们微分方程的均方差。如果我们能够最小化这个（也就是让这个项尽可能接近零），我们就自动满足了系统的控制方程。相当聪明，对吧？

现在，损失函数中的额外项 *L_IC* 需要被处理：它考虑了系统的初始条件。如果一个系统的初始条件未给出，那么该微分方程将有无穷多解。例如，从地面上抛出的球的轨迹与从 10 楼抛出的球轨迹由相同的微分方程决定；然而，我们知道这两个球的轨迹不会相同。这里变化的是系统的初始条件。我们的模型如何知道我们指的是哪些初始条件呢？此时，自然可以通过损失函数项来强制这一点！对于我们的微分方程，我们规定当 *t = 0* 时，*y = 1*。因此，我们希望最小化一个初始条件损失函数，表达式如下：

![](../Images/5a6c9d547fb8fbe2346528fa9fad2675.png)

如果我们最小化这一项，就能自动满足系统的初始条件。现在，剩下需要理解的是如何利用这个方法来求解微分方程。

# 求解微分方程

如果一个神经网络既可以通过损失函数中的数据相关项进行训练（这通常是在经典架构中进行的），也可以通过数据和方程相关项同时训练（这就是我刚刚提到的物理信息神经网络），那么它一定也可以被训练来最小化*仅仅*方程相关的项。这正是我们将要做的！这里使用的唯一损失函数将是 *L_equation*。希望下面的图示能帮助说明我刚才说的内容：今天我们目标是右下角类型的模型，即我们的微分方程求解神经网络。

![](../Images/b5397415a3249d038332a3d4300f1e2f.png)

图 1：展示不同神经网络类型与其损失函数关系的图示。在本文中，我们的目标是右下角的模型。图片由作者提供。

## 代码实现

为了展示我们刚刚学到的理论，我将使用 Python 代码实现提出的解决方案，并使用 PyTorch 库进行机器学习。

首先要做的是创建一个神经网络架构：

```py
import torch
import torch.nn as nn

class NeuralNet(nn.Module):
    def __init__(self, hidden_size, output_size=1,input_size=1):
        super(NeuralNet, self).__init__()
        self.l1 = nn.Linear(input_size, hidden_size)
        self.relu1 = nn.LeakyReLU()
        self.l2 = nn.Linear(hidden_size, hidden_size)
        self.relu2 = nn.LeakyReLU()
        self.l3 = nn.Linear(hidden_size, hidden_size)
        self.relu3 = nn.LeakyReLU()
        self.l4 = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        out = self.l1(x)
        out = self.relu1(out)
        out = self.l2(out)
        out = self.relu2(out)
        out = self.l3(out)
        out = self.relu3(out)
        out = self.l4(out)
        return out
```

这只是一个简单的 MLP，使用 LeakyReLU 激活函数。然后，我将定义损失函数，在训练循环中稍后进行计算：

```py
# Create the criterion that will be used for the DE part of the loss
criterion = nn.MSELoss()

# Define the loss function for the initial condition
def initial_condition_loss(y, target_value):
    return nn.MSELoss()(y, target_value)
```

现在，我们将创建一个时间数组，作为训练数据，并实例化模型，同时选择优化算法：

```py
# Time vector that will be used as input of our NN
t_numpy = np.arange(0, 5+0.01, 0.01, dtype=np.float32)
t = torch.from_numpy(t_numpy).reshape(len(t_numpy), 1)
t.requires_grad_(True)

# Constant for the model
k = 1

# Instantiate one model with 50 neurons on the hidden layers
model = NeuralNet(hidden_size=50)

# Loss and optimizer
learning_rate = 8e-3
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)

# Number of epochs
num_epochs = int(1e4)
```

最后，让我们开始训练循环：

```py
for epoch in range(num_epochs):

    # Randomly perturbing the training points to have a wider range of times
    epsilon = torch.normal(0,0.1, size=(len(t),1)).float()
    t_train = t + epsilon

    # Forward pass
    y_pred = model(t_train)

    # Calculate the derivative of the forward pass w.r.t. the input (t)
    dy_dt = torch.autograd.grad(y_pred, 
                                t_train, 
                                grad_outputs=torch.ones_like(y_pred), 
                                create_graph=True)[0]

    # Define the differential equation and calculate the loss
    loss_DE = criterion(dy_dt + k*y_pred, torch.zeros_like(dy_dt))

    # Define the initial condition loss
    loss_IC = initial_condition_loss(model(torch.tensor([[0.0]])), 
                                     torch.tensor([[1.0]]))

    loss = loss_DE + loss_IC

    # Backward pass and weight update
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

注意使用 `torch.autograd.grad` 函数自动对输出 *y_pred* 关于输入 *t* 进行求导，以计算损失函数。

## 结果

训练后，我们可以看到损失函数迅速收敛。图 2 显示了损失函数随训练轮数变化的图像，插图展示了损失函数下降最快的区域。

![](../Images/84b3cb68272023de4d8a4d0c14124f5b.png)

图2：按历时（epochs）变化的损失函数。在插图中，我们可以看到收敛最为迅速的区域。图像来源：作者。

你可能已经注意到，这个神经网络并不是一个常规的神经网络。它没有训练数据（我们的训练数据是一个手工制作的时间戳向量，简单来说就是我们希望研究的时间域），因此它从系统中获得的所有信息都以损失函数的形式呈现。它唯一的目的就是在它被设计用来解决的时间域内求解微分方程。因此，要测试它，公正的方法是使用它训练时的时间域。图3显示了神经网络预测与理论答案（即解析解）之间的对比。

![](../Images/d6fa652093b89341224469a1ca0dfa2d.png)

图3：神经网络预测与微分方程解析解的预测对比。图像来源：作者。

我们可以看到两者之间的高度一致性，这对于神经网络来说非常好。

这种方法的一个警告是，它对于未来的时间不太适用。图4显示了如果我们将时间数据点向前滑动五步会发生什么，结果就是一片混乱。

![](../Images/47df1064835a3b04232af56c64966f12.png)

图4：神经网络和解析解对于未见数据点的预测。图像来源：作者。

因此，这里得到的教训是，这种方法是作为一个数值解法器，用于在时间域内解决微分方程，不能被当作常规神经网络用来对未见的、超出训练域的数据进行预测并期望它能很好地泛化。

# 结论

毕竟，剩下的一个问题是：

> *为什么要训练一个不能很好地泛化到未见数据的神经网络，并且更糟糕的是，它显然比解析解更差，因为它具有内在的统计误差？*

首先，这里提供的示例是一个已知解析解的微分方程的例子。对于未知解，仍然必须使用数值方法。然而，正如所说，微分方程求解的数值方法通常会积累误差。这意味着如果你尝试在多个时间步长中求解方程，解的准确性会随着时间的推移而丧失。另一方面，神经网络解法器学习如何在每个训练历时中为所有数据点求解微分方程。

另一个原因是，神经网络擅长插值，因此，如果你想知道未见数据中函数的值（但这个“未见数据”必须位于你训练过的时间区间内），神经网络将迅速给出一个值，而经典的数值方法则无法迅速提供。

# 参考文献

[1] Marios Mattheakis 等，[用于求解运动方程的哈密顿神经网络](https://arxiv.org/abs/2001.11107)，*arXiv预印本 arXiv:2001.11107v5*，2022年。

[2] Mario Dagrada, [介绍物理信息神经网络](/solving-differential-equations-with-neural-networks-afdcf7b8bcc4)，2022年。
