# 双摆及其超越：通过多臂摆研究混沌

> 原文：[https://towardsdatascience.com/the-double-pendulum-and-beyond-investigating-chaos-with-multi-armed-pendulums-7a5751d059db?source=collection_archive---------2-----------------------#2024-04-03](https://towardsdatascience.com/the-double-pendulum-and-beyond-investigating-chaos-with-multi-armed-pendulums-7a5751d059db?source=collection_archive---------2-----------------------#2024-04-03)

## 使用VPython仿真模拟混沌运动，并探讨定义混沌系统的关键特征。

[](https://medium.com/@oliverwjohnson06?source=post_page---byline--7a5751d059db--------------------------------)[![Oliver W. Johnson](../Images/66fe30ee3ccc1556ee2a4753b9c02235.png)](https://medium.com/@oliverwjohnson06?source=post_page---byline--7a5751d059db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7a5751d059db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7a5751d059db--------------------------------) [Oliver W. Johnson](https://medium.com/@oliverwjohnson06?source=post_page---byline--7a5751d059db--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7a5751d059db--------------------------------) ·阅读时间12分钟·2024年4月3日

--

任何物理学学生都研究过低级的单摆，可能比他们想要的还要多。与弹簧上的质量一起，摆是简谐运动的典型例子。这里的关键词是*简*。你可能知道，单摆非常容易用数学建模。单摆的周期可以通过下面的公式表示，其中g是重力加速度，*L*是摆长。

![](../Images/96887b1a61af4f2ea0a3c35a8dee4f18.png)

角位移稍微有趣一些，但仍然不算太深奥。方程本身更复杂，但实际上它只是一个余弦波。下面的方程表示单摆的角位移，其中*θ*是角位移，*A*是最大角位移（在大多数情况下，这只是释放角度），*ω*是角频率，*t*是时间。

![](../Images/c90800b5ae3a905c1dce44149784a579.png)

说完这些，我们来看看更有趣的内容，尽管实际上它几乎不可能实现（你会明白的），数学和物理学。

如果你曾经操作过一个常规的摆，你或许对[双摆](https://en.wikipedia.org/wiki/Double_pendulum)有一定了解，它让人联想到臭名昭著的[三体问题](https://en.wikipedia.org/wiki/Three-body_problem)。三体问题展示了这样一个观点：在一个系统中，三个物体的运动分析解是无法解决的，因为每个物体的力同时与其他物体相互作用。简而言之，双摆无法通过解析方法解决。根据上面提到的摆的方程，真正无法解出的是*θ*，即两个摆的角位移。你也无法解出周期，但这主要是因为双摆并没有一个固定的周期。以下是我们讨论过的双摆系统的视觉表示，支点固定在墙上，角度用*θ1*和*θ2*标记，悬挂的质量用*m1*和*m2*标记。

![](../Images/e38afcd7c1abeb7543adb8d13d301e61.png)

重申一下——在我们当前的物理学中，我们无法解析预测双摆的运动。那么，为什么呢？实际上，运动并非完全随机，而是**混沌**的。我们如何证明混沌与随机不同呢？这是我们稍后将要解答的问题。

你可能在YouTube上看到过一些关于混沌或[混沌理论](https://en.wikipedia.org/wiki/Chaos_theory)的标题，我敢打赌缩略图看起来大概是这样的：

![](../Images/4d4f4ec500edaefb5351f70909b85d94.png)

那张图像，以及许多其他类似蝴蝶翅膀的图形，通常与混沌理论相关联。混沌理论中一个不常被认为是其一部分的概念是双摆、三摆以及其他多摆的情况。让我们探索混沌运动的一些特性，并尽可能地深入了解它，稍后我们将借助计算机程序来实现这一点。

我们可以调查的一个特性是，起始条件的微小变化对最终结果的影响。这个变化最终会变得微不足道，还是会导致与原始结果完全不同的结论呢？以下是双摆在t=50时的计算机仿真，其中*θ1*和*θ2*的起始角度分别为80°和0°。（角度与上图所示相同）

![](../Images/dae57a4e203cd9c19d0029cec3dc668d.png)

红线拖曳着摆的底部质量(*m2*)，并在画出后保持2秒钟。

现在，让我们进行**完全相同**的仿真，图像展示的是t=50，所有的质量和摆臂长度都是完全相同的。然而，*θ1*和*θ2*的角度分别为79°和0°。*θ1*的起始值变化了1°，让我们看看它如何影响结果。

![](../Images/4aca810f1e816675485b3d9b0788ded7.png)

不出所料（或者说出乎意料，我不确定你期待的是什么），仅仅改变1°，系统发生了剧烈变化，最终的位置和系统所描绘的轨迹与最初的图像完全不相似。这与任何外部变量无关，因为在我们的模拟中没有摩擦、空气阻力或其他任何障碍。这个例子展示了一个观点：当一个系统表现出混沌运动时，比如双摆，即使是最微小的条件变化，也能导致结果发生巨大变化。

所以，我们已经掌握了混沌运动的一个有趣方面——初始条件的微小变化能导致结果发生巨大变化。让我们再尝试识别一些其他方面。如果我们再次进行相同的双摆实验，会发生什么呢？从实验上来说，这几乎是不可能的；它需要在测量诸如引力场、质量、空气阻力和*实验中的每一个其他变量*时具有无限精度，然后使用你那无限精度的数据重新设置实验，*完全相同*。然而，我们有一个很好的模拟工具，可以使这个挑战消失。

让我们运行一个类似之前的双摆模拟，但改变了一些变量，以便图像与之前不同：t=50，*θ1=80°*，*θ2=15°*，摆的物理特性（长度、质量）保持不变。以下是结果：

![](../Images/fe96ea05ef66cc4cc16f28e8bdf40e79.png)

剧透提醒，第二次的结果完全相同……第三次、第四次、第五次和第六次也是一样。为了证明这一点，我们还可以尝试用三摆做这个实验。我们将使用相同的初始条件，但现在有了第三个角度*θ3*，所以我们将使用*θ1=80°*，*θ2=15°*，*θ3=0°*。不过，三摆的计算量要大得多，所以这张图片是在t=10时拍摄的。

![](../Images/17b74221319befc21c06be0c2834ec66.png)

在三摆的模拟中，红线是由第三个质量拖动的。

尽管三摆的模拟比双摆更复杂，但它表现出相同的特性——在相同初始条件下，三摆也会重复其模式。这种在相同条件下得到相同结果的思想证明了混沌运动和混沌系统是确定性的，而非随机的。再次强调，由于实验上很难证明这一点，或者在实践中运用它，这并不会对我们对现实世界的理解产生太大影响。然而，我们现在知道，混沌系统并非随机的，并且在掌握正确的知识下是可以预测的。此外，由于我们证明了微小的变化会产生巨大的影响，我们也知道，要有效预测混沌系统的结果，必须具备极高的精度。

我们现在已经确定了混沌运动的两个重要特性：第一个是微小的变化会对系统的结果产生巨大影响，第二个是混沌运动并非随机的，而是确定性的。让我们再找一个特性——正如文章一开始所讨论的，双摆中你无法求解的变量是角位移。然而，我们也说过，求解周期是徒劳的，因为双摆不会重复——让我们来验证这个理论。

首先，让我们以以下条件运行双摆实验：*θ1*=30°，*θ2*=30°，质量和长度与之前相同。这次，我们使用VPython的绘图功能记录底部质量随着时间变化的高度图表：

![](../Images/245e36981cd95792726efe700c3770e0.png)

如果你问我，那个看起来相当周期性。那么，这是否意味着上述系统不是一个混沌系统？如果运动不是混沌的，是否意味着我们能够找到双摆在能量低于某个阈值时的运动的解析解？这个问题我留给读者去思考。不过，为了做到这一点，你可能需要我在本文中编写并使用的程序的帮助，所以我们来一起看看。这个程序是用VPython编写的，并使用了[GlowScript](https://www.glowscript.org/)，因此请确保你在WebVPython系统上运行它，而不是在任何Python编辑器中。

首先，我们来定义程序中使用的物理常数。这些常数包括质量、臂长和重力。除此之外，我们还将定义系统的初始条件，其中theta1和theta2是初始角度，theta1dot和theta2dot是臂的初始角速度。最后，我们还将设置初始时间为0，并定义dt，它是在每次循环迭代中我们将向前推进的时间量。

```py
#lengths of the strings
L1 = 1.5 #top string
L2 = 1 #middle string

#masses
M1 = 2 #top moving ball
M2 = 1 #middle moving ball

#g
g = 9.8

#starting angles and velocities
theta1=30*pi/180 #release angle top ball
theta2= 30*pi/180 #release angle middle ball
theta1dot = 0
theta2dot = 0

t = 0
dt = 0.001
```

接下来，我们定义系统的所有部分，包括枢轴点、质量1（m1）、臂1（stick1），以及第二组质量和杆（m2，stick2）。

```py
pivot = sphere(pos=vector(0,L1,0), radius=0.05)

m1 = sphere(pos=pivot.pos-vector(0,L1,0),radius=0.05,color=color.white)
stick1 = cylinder(pos=pivot.pos,axis=m1.pos-pivot.pos,radius=0.015,color=color.yellow)

m2 = sphere(pos=m1.pos-vector(0,L2,0),radius=0.05,color=color.white)
stick2 = cylinder(pos=m1.pos, axis=m2.pos-m1.pos, radius=0.015,color=color.yellow)
```

现在我们将质量和杆的位置调整到反映初始角度的状态，如果你运行代码之前，它们只会是两根垂直的杆。为此，我们只需使用一些三角函数将质量放置到杆的末端位置，然后将杆设置为连接每个质量之间的距离。在这里，我们还使用了VPython的‘attach_trail’方法，让底部质量生成轨迹。

```py
m1.pos = pivot.pos+vector(L1*sin(theta1),-L1*cos(theta1),0)
m2.pos = m1.pos+vector(L2*sin(theta2),-L2*cos(theta2),0)

stick1.axis = m1.pos - pivot.pos
stick2.pos = m1.pos
stick2.axis = m2.pos - m1.pos

attach_trail(m2, retain=200, color=color.red)
```

接下来，我们开始程序的主循环；在这个模拟中，和许多物理模拟一样，我们将使用一个`while`循环，并在t=50时退出。我们还将使用`rate()`方法来设置程序运行的速度。

```py
while t <= 50:  
    rate(1000)
```

现在我们必须深入实际的数学运算。我们将使用一些微积分（其实并不是很复杂，但确实涉及到导数）、三角学和代数来首先计算角加速度，然后用它来增量角速度，接着将角速度应用到质量和杆的位置，以便根据它们计算出来的加速度来移动它们。

为了计算顶部质量的加速度，我们使用这个公式来表示角加速度，或者是*θ1*的二阶导数：

![](../Images/c54f11b38ca854d20a2ae8bf8f8e6afb.png)

你也可以使用以下代码表示相同的方程：

```py
theta1ddot = (-g*(2*M1 + M2)*sin(theta1) -M2*g*sin(theta1 -2*theta2)-2*sin(theta1 - theta2)*M2*(L2*theta2dot**2 + L1*cos(theta1 - theta2)*theta1dot**2))/(L1*(2*M1 + M2 - M2*cos(2*theta1 -2*theta2)))
```

对于第二个质量的加速度，我们必须使用不同的公式，因为它代表了系统中的另一个部分。

![](../Images/18f14126223490fb581e1857e28462ba.png)

这个方程在程序中写成：

```py
theta2ddot = (2*sin(theta1 - theta2)*((M1 + M2)*L1*theta1dot**2 + g*(M1 + M2)*cos(theta1) + L2*M2*cos(theta1 - theta2)*theta2dot**2))/(L2*(2*M1 + M2 - M2*cos(2*theta1 -2*theta2)))
```

接下来，让我们增加角速度变量‘theta1dot’和‘theta2dot’。为此，我们只需使用下面的方程，它通过角加速度乘以dt来增量角速度。虽然图像中只列出了theta1dot的方程，但*θ1*和*θ2*的计算是相同的。

![](../Images/ef888b487133dfd1f2ee7d4b88ce5530.png)

为了我们的目的，我们用代码表示这个公式：

```py
theta1dot = theta1dot + theta1ddot*dt
theta2dot = theta2dot + theta2ddot*dt
```

对于双摆运动的最终部分，我们必须通过角速度来增量*θ1*和*θ2*。这通过下面的方程表示，其中我们将角度*θ*设为前一个*θ*加上角速度乘以dt。同样，*θ1*和*θ2*的计算是完全相同的。

![](../Images/9d6094d5803391184fe6bc95a3d11ff1.png)

在代码中，我们将这个方程写成：

```py
theta1 = theta1 + theta1dot*dt
theta2 = theta2 + theta2dot*dt
```

现在，我们必须更新程序中质量和杆的位置，以便能够看到程序的结果。首先，我们将更新质量的位置。我们将采取一种直观的方法来更新这些位置；因为在系统中永远不会改变的一件事是杆的长度，所以中间的质量（m1）将与支点保持等于L1的距离。从逻辑上讲，我们可以构建以下关于m1位置的方程：

![](../Images/27ee0b395f6374c2e399627f663a321e.png)

相同的公式可以用来反映m2的位置公式，只是使用了*θ2*和L2，并且它是基于m1的位置，而非支点。请参见下面的公式。

![](../Images/26073c38ba5f04f5d87463639f641626.png)

这两个公式在我们的程序中写得非常简单，因为VPython的一个特点是它使得向量计算变得非常简单。所有对象的位置信息已经作为向量存储，所以你所需要做的就是使用vector()方法定义一个新的向量，并将其用于计算。

```py
m1.pos = pivot.pos + vector(L1*sin(theta1),-L1*cos(theta1),0)
m2.pos = m1.pos + vector(L2*sin(theta2),-L2*cos(theta2),0)
```

现在，我们必须迈出最后一步，通过更新杆的位置来实现我们计算的结果。从技术上讲，这并不是完全必要的，因为质量仍然会显示并遵循应该的模式。然而，为了视觉效果，让我们编写这段代码。数学部分并不太有趣，因此代码如下。首先，我们设置第一个杆的轴，使其填充从支点到质量 m1 的距离。接下来，我们将第二根杆的位置设置为质量 m1。最后，我们将第二根杆的轴设置为第一个和第二个质量之间的距离。最后一行是通过增量 dt 来更新 t，以使程序向前运行。

```py
stick1.axis = m1.pos - pivot.pos
stick2.pos = m1.pos
stick2.axis = m2.pos - m1.pos

t += dt
```

这里是最终的代码，方便复制和粘贴：

```py
Web VPython 3.2

#lengths of the strings
L1 = 1.5 #top string
L2 = 1 #middle string

#masses
M1 = 2 #top moving ball
M2 = 1 #middle moving ball

#g
g = 9.8

t = 0
dt = 0.001

#starting angles and velocities
theta1=80.5*pi/180 #release angle top ball
theta2= 0*pi/180 #release angle middle ball
theta1dot = 0
theta2dot = 0

pivot = sphere(pos=vector(0,L1,0), radius=0.05)

m1 = sphere(pos=pivot.pos-vector(0,L1,0),radius=0.05,color=color.white)
stick1 = cylinder(pos=pivot.pos,axis=m1.pos-pivot.pos,radius=0.015,color=color.yellow)

m2 = sphere(pos=m1.pos-vector(0,L2,0),radius=0.05,color=color.white)
stick2 = cylinder(pos=m1.pos, axis=m2.pos-m1.pos, radius=0.015,color=color.yellow)

m1.pos = pivot.pos+vector(L1*sin(theta1),-L1*cos(theta1),0)
m2.pos = m1.pos+vector(L2*sin(theta2),-L2*cos(theta2),0)

stick1.axis = m1.pos - pivot.pos
stick2.pos = m1.pos
stick2.axis = m2.pos - m1.pos

attach_trail(m2, retain=200, color=color.red)

eChart = gdisplay(x=500, y=0, width=600, height=400, title="", xtitle="", ytitle="", foreground=color.black, background=color.white)
ePlot = gdots(color=color.red)

while t < 50:  
    rate(1000)

    #angular acceleration
    theta1ddot = (-g*(2*M1 + M2)*sin(theta1) -M2*g*sin(theta1 -2*theta2)-2*sin(theta1 - theta2)*M2*(L2*theta2dot**2 + L1*cos(theta1 - theta2)*theta1dot**2))/(L1*(2*M1 + M2 - M2*cos(2*theta1 -2*theta2)))
    theta2ddot = (2*sin(theta1 - theta2)*((M1 + M2)*L1*theta1dot**2 + g*(M1 + M2)*cos(theta1) + L2*M2*cos(theta1 - theta2)*theta2dot**2))/(L2*(2*M1 + M2 - M2*cos(2*theta1 -2*theta2)))

    #angular velocity
    theta1dot = theta1dot + theta1ddot*dt
    theta2dot = theta2dot + theta2ddot*dt

    #angular position
    theta1 = theta1 + theta1dot*dt
    theta2 = theta2 + theta2dot*dt

    m1.pos = pivot.pos + vector(L1*sin(theta1),-L1*cos(theta1),0)
    m2.pos = m1.pos + vector(L2*sin(theta2),-L2*cos(theta2),0)
    stick1.axis = m1.pos - pivot.pos
    stick2.pos = m1.pos
    stick2.axis = m2.pos - m1.pos

    t = t+dt
```

感谢你能看到这里，我希望你喜欢我通过双摆和三摆分析混沌运动的内容。最后，我既不是专业的物理学家，也不是程序员，所以如果你有任何建议帮助我改进我的工作，请不要犹豫，随时告诉我。

此外，文中使用的所有图像都是我自己创建的，使用了 Python 和 Microsoft Word，这两个工具都具有令人惊叹的能力。

最后，我想以一个耐人寻味的思考作为结束，这将成为我下一个项目的基础。如果你拿一个已经呈现混沌运动的多臂摆，如何在不间断的情况下为其增加一个非常大质量的臂（可能是系统中最大质量的 50 倍），并且初始角度为 0°，它的运动会如何变化呢？
