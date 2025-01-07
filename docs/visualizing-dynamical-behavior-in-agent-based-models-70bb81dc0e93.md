# 在基于代理的模型中可视化动态行为

> 原文：[https://towardsdatascience.com/visualizing-dynamical-behavior-in-agent-based-models-70bb81dc0e93?source=collection_archive---------9-----------------------#2024-03-05](https://towardsdatascience.com/visualizing-dynamical-behavior-in-agent-based-models-70bb81dc0e93?source=collection_archive---------9-----------------------#2024-03-05)

![](../Images/535063d1630e3ce67be8af53598f0beb.png)

## 并在过程中遇到涌现的复杂性

[](https://medium.com/@Dani_Lisle?source=post_page---byline--70bb81dc0e93--------------------------------)[![Dani Lisle](../Images/2933bbbca26cf198e7964547a91b2751.png)](https://medium.com/@Dani_Lisle?source=post_page---byline--70bb81dc0e93--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--70bb81dc0e93--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--70bb81dc0e93--------------------------------) [Dani Lisle](https://medium.com/@Dani_Lisle?source=post_page---byline--70bb81dc0e93--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--70bb81dc0e93--------------------------------) ·阅读时间 9 分钟 ·2024年3月5日

--

在我进行博弈论问题中战略知识提取优化的研究时，我最近意识到我需要一种更好的方式来简单直观地可视化具有定义的动态行为的代理行为。

这促使我建立了一个简单的库，用于通过 PyPlot 将代理行为可视化为动画。但在进入教程之前，先简要回顾一下这里涉及的核心概念。

# 动态代理模型的快速入门

基于代理的建模（ABM）提供了一种优秀的方式来模拟许多博弈论环境中的玩家。它让我们能够模拟和研究每个个体玩家的行为和认知过程，而不仅仅是分析趋势。当使用简化的二元状态机（如细胞自动机网格）表示代理不切实际时，ABM 让我们通过在一个维度空间中表示代理的位置来捕捉场景，在这个空间中，每个维度都有自己独特的规则。

## 代理的位置和状态空间

通过利用空间维度和状态维度，我们可以准确地建模接近度，并结合允许细微交互的属性，比如特征的相似性。此外，将代理的“位置”存储在状态空间中，让我们能够跟踪并比较代理的详细和动态状态信息（例如，在一个足球模型中玩家的疲劳状态）。

与网络模型相比，后者通过两个对象之间的连接来表示关系，而位置-状态-空间信息则允许我们定义并探索代理之间更复杂、更高维的关系。在这种范式中，欧几里得距离在状态空间中的度量可以表示两个代理之间状态相似度的强度。

## 用于行为和交互的动力学系统理论

我们可以通过使用动力学系统理论来描述一个代理的状态如何影响其他代理的状态，从而使这种模型变得更加强大。它提供了一种强有力的方法来定义系统随时间演化的规则。

在社会经济系统中，微分方程可以用来模拟交流、模仿以及更复杂的适应性过程，并且描述个体行为如何产生群体动态。

# 在时间和空间中的可视化需求

对于我的问题，我希望能够一目了然地看到每个代理的位置及其随时间的移动，并且展示一些基本的状态信息（即每个代理的一个或两个变量）。

目前，有几个库满足了可视化代理基础模型（ABM）的需求，包括NetLogo和Mesa（Python版）。然而，它们主要是为了可视化离散空间模型，而非连续空间模型。对于我的目的，我对后者更感兴趣，因此开始了这个附带任务。如果你想跟着一起学习或亲自测试代码，所有代码都存储在[github.com/dreamchef/abm-viz](https://github.com/dreamchef/abm-dynamics-viz)。

# 在Python中构建类和动画

首先，我需要表示和存储代理和世界的状态及其动态。我选择使用Python类来实现这一点。我定义了一个Agent类，并为其设置了一组我认为对许多可能的建模任务有用的变量。我还为每个代理在类结构内定义了一个可绘制的圆形对象（使用PyPlot）。

```py
class Agent:

    def __init__(self, index, position, velocity, empathy=1, ... dVision=0, age=0, plotSize=10):

        self.index = index
        self.velocity = np.array(velocity)
        self.empathy = empathy
        ...
        self.dVision = dVision
        self.color = HSVToRGB([self.species,1,1])
        self.plotSize=plotSize

        self.pltObj = plt.Circle(self.position, self.plotSize, color=self.color)
```

然后，在进行了一些实验后，我发现按照良好的面向对象编程原则，定义一个世界（想象成游戏或系统）类是最好的做法。在这个类的结构中，既包括了世界的状态信息，也包含了图形和坐标轴的信息：

```py
class World:

    def __init__(self,population=1,spawnSize=400,worldSize=1200,worldInterval=300,arrows=False,agentSize=10):

        self.agents = []
        self.figure, self.ax = plt.subplots(figsize=(12,8))
        self.ax.set_xlim(-worldSize/2, worldSize/2)
        self.ax.set_ylim(-worldSize/2, worldSize/2)

        self.worldInterval = worldInterval
        self.worldSize = worldSize
        self.arrows = arrows
        self.agentSize = agentSize

        for i in range(population):

            print(i)

            newAgent = Agent(index=i, position=[rand()*spawnSize - spawnSize/2, rand()*spawnSize - spawnSize/2],
                                     velocity=[rand()*spawnSize/10 - spawnSize/20, rand()*spawnSize/10 - spawnSize/20], plotSize = self.agentSize)

            self.agents.append(newAgent)
            self.ax.add_patch(newAgent.pltObj)

            print('Created agent at',newAgent.position,'with index',newAgent.index)

        self.spawnSize = spawnSize
```

我编写这个类是为了让程序员能够简单地指定所需的代理数量（以及世界大小、生成区域大小等其他参数），而不是在主程序中手动创建和添加代理。

在定义了这个基本结构并且在世界中生成了具有随机位置和初始速度的代理后，我使用了PyPlot库的动画功能来创建一个方法，开始进行可视化：

```py
def start(self):

        ani = animation.FuncAnimation(self.figure, self.updateWorld, frames=100, interval=self.worldInterval, blit=True)

        plt.show()
```

这个函数引用了存储在World实例中的图形、动画的速度和时长的规格，以及一个更新函数，后者也在World类中定义：

```py
def updateWorld(self,x=0):

        pltObjects = []

        for agent in self.agents:

            agent.updatePosition(self.agents, self.worldSize)
            agent.pltObj.center = agent.position
            pltObjects.append(agent.pltObj)

            if self.arrows is True:

                velocityArrow = plt.Arrow(agent.position[0], agent.position[1], agent.velocity[0], agent.velocity[1], width=2, color=agent.color)

                self.ax.add_patch(velocityArrow)

                pltObjects.append(velocityArrow)

        return pltObjects
```

World.updatePosition 函数仅将每个代理的静态速度添加到当前位置。这项初步工作能够生成像这样的简单动画：

![](../Images/62630e7bd868ec7c0f0e00b752b71c05.png)

恒定速度

有了这个基本功能，我们现在希望能够可视化更有趣的动态。

# 可视化“社会”动态行为

我首先选择定义一个动态，其中每个代理根据周围其他代理的平均运动方向改变自己的运动方向。用方程形式表示：

![](../Images/3a277f060187732ccbdba91e6e82777d.png)

我通过 Agent.updatePosition() 和 Agent.updateVelocity() 方法在 Python 中编码了该动态，这些方法在每一帧动画中运行：

```py
def updatePosition(self, agents, worldSize):

        self.updateVelocity(agents)

        self.position += self.velocity

        ...

    def updateVelocity(self, agents):

        herd_velocity = self.herdVelocity(agents)

        herd_magnitude = np.linalg.norm(herd_velocity)
        self_magnitude = np.linalg.norm(self.velocity)

        if herd_magnitude > 0.1:

            herd_unit_velocity = herd_velocity/herd_magnitude

            self.velocity += herd_unit_velocity 
```

在下面的 PyPlot 动画中，代理从不同的速度开始，但很快就调整并开始朝同一方向移动。在这种情况下，平均方向最初大致是 Y 方向上的向上。

![](../Images/c71a52a10d0d946ff5df87bb544bff4b.png)

向上汇聚

这一次，群体初始化时大致以向左的速度运动，并且有一个“滞后者”，它很快调整过来。

![](../Images/fc77372a07139faeff1b8014388fb0f4.png)

单个滞后者向左汇聚

## 显示速度向量

接下来，我意识到将代理的速度显示得更加清晰会很有帮助，所以我实现了箭头来显示每个代理的大小和方向：

```py
velocityArrow = plt.Arrow(agent.position[0], agent.position[1], agent.velocity[0], agent.velocity[1], width=2, color=agent.color)
self.ax.add_patch(velocityArrow)
pltObjects.append(velocityArrow)
```

这一修改带来了更有帮助的动画，例如这个。我们仍然可以一眼看到速度汇聚的动态，但现在可以更清楚地看到加速度的变化率。

![](../Images/8322d6407c8d74f5dbba398bd6d03b5a.png)

速度箭头

对于上面的动画，我还调整了动态，使其依赖于一个视距变量。换句话说，代理只会调整它们的速度，以匹配附近的代理（在这种情况下为 300 单位以内）。

我还修改了代码，使得每个代理只修改其运动方向，而不是速度。请在下一个部分中记住这一点。

# 更复杂的状态—逐个动态

到目前为止，我只实现了考虑每个代理位置和速度的动态。但正如我在概述部分提到的，考虑非空间的状态信息也可以使我们的建模方法更加具有广泛的适用性。

## 使用代理的色调和饱和度来表示状态变量

我利用了一个与每个代理的 RGB 颜色相关联的辅助状态。预示着我研究的进化博弈理论目标，我将其称为代理的“物种”，并如下实现：

在 Agent.__init__ 方法中，我添加了随机物种生成，并将个体映射到代理在图中的标记颜色：

```py
self.species = rand()
self.color = HSVToRGB([self.species,1,1])
self.pltObj = plt.Circle(self.position, self.plotSize, color=self.color)
```

具体来说，我将这个分配给了物体的色调，将饱和度（大致是灰度）保留给其他潜在有趣的变量（例如剩余寿命或健康）。这种将色调和饱和度分开的做法在复数值函数的可视化中已有先例，我在[这篇文章](https://medium.com/towards-data-science/today-i-was-pouring-through-my-complex-variables-and-analytic-functions-book-written-by-the-e9205f71485d)中有详细介绍。

## 加速度对物种状态变量的依赖

在自然界中，群体行为通常发生在同一物种的动物之间，而不同物种之间则不会发生这种行为。基于这一点，我决定将我们的玩具动力学修改为考虑物种的因素。这一改变意味着，每个代理只会对世界中足够接近且物种相似的代理（即颜色相似的圆圈）调整方向。

现在，事情开始变得**非常，非常有趣**了。在继续阅读之前，问问自己：

+   *你期待在下一个动画中看到什么样的行为？*

一个相对天真的实验者（比如大多数时候的我）会期望代理们按物种组织自己，并成群结队地移动，每群代理通常朝不同方向移动，并大多数情况下互不理睬。为了看看我们是否正确，接着往下看。

为了编码这种行为，我修改了与代理相关的群体速度计算，方式如下：

```py
herd_velocity += neighbor.velocity * (0.5-np.sqrt(abs(self.species-neighbor.species)))
```

这一变化导致了像这样的动画。为了看到其中有趣的行为，我在动画开始大约20秒后开始录制。

![](../Images/72c034b975c1a23e2db32ccf1cc87b42.png)

涌现行为

如果行为没有立刻显现出来也没关系。这是微妙的。如果你认为自己预测正确，做得很好！

结果是，代理们并没有很好地按物种组织成群。相反，唯一似乎聚在一起的代理是那些既有相似物种又有相似旅行速度的代理。你可以最常见地看到这种现象发生在速度较慢的绿色代理、那对蓝色代理，以及在屏幕底部快速移动的蓝色和紫色代理之间。值得注意的是，代理似乎在“选择”与谁一起旅行时，更看重速度而非物种相似性。你可以在浅蓝色、深蓝色，甚至紫色代理之间最常看到这种现象。

这在我们定义的动态中是完全合理的，因为每个代理的速度是恒定的，速度不同的代理最终会落后或者甩开他们的同伴。然而，这有点出乎意料。

从根本上讲，这是因为行为是涌现的。换句话说，我们并没有明确告诉代理按这种方式作为一个群体来表现。相反，他们从我们给每个代理的相同编码格式的简单指令中“推导”出了这种行为。

# 可视化、涌现与数据洞察

我们开始这段旅程的简单目标是可视化一个ABM，以确保我们设置的动态在高层次上能够按预期工作。但除了实现这一目标外，我们还偶然发现了一个可能在创建模型时根本没有考虑过的涌现行为。

这说明了在数据科学、模拟和建模领域，关于可视化的重要合作关系。通过对模型或数据集的新视角，可以加速发现复杂系统中的涌现行为。这不仅仅适用于基于代理的建模，它同样适用于在数据科学的其他领域获得洞见。而且，创建新的富有创意的可视化图像提供了一种确保获得此类视角的有效途径。

如果你想进一步尝试这个模型和可视化，你可以在[github.com/dreamchef/abm-viz](https://github.com/dreamchef/abm-dynamics-viz)获取代码。我很想听听你发现的其他内容，或者对我的工作的看法，欢迎在评论中告诉我！也可以随时通过[Twitter](https://twitter.com/dani_lisle)和[LinkedIn](https://www.linkedin.com/in/danilisle/)与我联系。祝你有个愉快的一天！

*除非另有说明，所有图像和动画均由作者创建。*
