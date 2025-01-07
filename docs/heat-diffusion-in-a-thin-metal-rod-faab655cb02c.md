# 细金属棒中的热扩散

> 原文：[https://towardsdatascience.com/heat-diffusion-in-a-thin-metal-rod-faab655cb02c?source=collection_archive---------9-----------------------#2024-07-10](https://towardsdatascience.com/heat-diffusion-in-a-thin-metal-rod-faab655cb02c?source=collection_archive---------9-----------------------#2024-07-10)

## 热扩散方程的解满足傅里叶级数

[](https://sebastiengilbert.medium.com/?source=post_page---byline--faab655cb02c--------------------------------)[![Sébastien Gilbert](../Images/380f6588c3ef718947bcf82061f190eb.png)](https://sebastiengilbert.medium.com/?source=post_page---byline--faab655cb02c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--faab655cb02c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--faab655cb02c--------------------------------) [Sébastien Gilbert](https://sebastiengilbert.medium.com/?source=post_page---byline--faab655cb02c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--faab655cb02c--------------------------------) ·9 分钟阅读·2024年7月10日

--

如果你加热一段绝缘金属棒的某个小部分，并将其静置一段时间，会发生什么？我们对热扩散的日常经验使我们预测，温度会逐渐均匀，直到变得一致。在完美绝缘的情况下，热量将永远停留在金属中。

这是对该现象的正确定性描述，但如何定量描述它呢？

![](../Images/e5708f72a2768be191b2ec96c48572c3.png)

图片由 [Jonny Gios](https://unsplash.com/@supergios?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们考虑一个一维问题，即一个包裹在绝缘材料中的细金属棒。绝缘层阻止热量从金属棒的侧面逸出，但热量可以沿着棒的轴向流动。

你可以在 [这里](https://github.com/sebastiengilbert73/heated_metal_bar)找到本文中使用的代码。

# 热扩散方程

[热扩散方程](https://en.wikipedia.org/wiki/Heat_equation)是一个简单的二阶微分方程，涉及两个变量：

![](../Images/96c7d9be1ae5cd93d0483e0ea16da6e2.png)

x ∈ [0, L] 是杆件上的位置，t 是时间，u(x, t) 是温度，α 是材料的 [热扩散率](https://en.wikipedia.org/wiki/Thermal_diffusivity)。

> 通过分析热扩散方程，我们能获得哪些关于温度变化的直觉？

方程(1)说明了**温度变化的局部速率与曲率成正比，即温度分布对 x 的二阶导数**。

![](../Images/84d055f797137ed6543be68de86db4be.png)

图1：带有局部变化率的温度分布。图像由作者提供。

图1展示了一个包含三个部分的温度分布。第一部分是线性的；第二部分有负的二阶导数，第三部分有正的二阶导数。红色箭头显示了沿杆的温度变化速率。

如果达到了稳态，即∂u/∂t = 0，温度分布将会平滑直到温度分布变为线性。

# 热扩散方程的解

热扩散方程（1）的解¹是：

![](../Images/eaae09340e3de09db2e2238127f52b52.png)

你可以通过对方程（2）求导来验证它确实满足方程（1）。对于那些对推导过程感兴趣的人，请参见附录I。

系数{Aₙ}、{Bₙ}、{λₙ}、C、D和E是常数，必须根据初始条件和边界条件进行拟合。[我们关于傅里叶级数的研究将发挥作用](https://medium.com/towards-data-science/assorted-flavors-of-fourier-series-on-a-finite-domain-8c93904df6ae)！

# 边界条件

边界条件是施加在x=0和x=L处的约束条件。在实际情况下，我们会遇到两种类型的约束条件：

+   绝缘，意味着在杆的两端∂u/∂x=0。这个约束防止了热量的流入或流出；

+   杆端固定温度：例如，杆的尖端可以通过[热电冷却器](https://en.wikipedia.org/wiki/Thermoelectric_cooling)进行加热或冷却，保持在所需的温度。

约束类型的组合将决定傅里叶级数的适当形式来表示初始温度分布。

## 两端绝缘

当杆的两端绝缘时，温度分布的梯度在x=0和x=L处被设为零：

![](../Images/9a7098344bebe5a7e815b1d31dbb6f94.png)

初始条件是t=0时杆上的温度分布。假设由于某些难以理解的原因——也许杆被某种邪恶力量附身——温度分布如下：

![](../Images/45b31bea5072762b0bc7b84ced3002da.png)

图2：初始温度分布。图像由作者提供。

为了运行我们的温度演化仿真，我们需要将方程（2）在t=0时的值与该函数匹配。我们通过样本点知道初始温度分布，但不知道其解析表达式。这正是傅里叶级数展开的任务。

从[我们关于傅里叶级数的研究](https://medium.com/towards-data-science/assorted-flavors-of-fourier-series-on-a-finite-domain-8c93904df6ae)中，我们观察到*偶数半区扩展*会产生一个在两端导数为零的函数。这正是我们在本例中需要的。

![](../Images/30136d3aec7c6cb9148dfa2f34db6397.png)

图3展示了图2中函数的偶数半区扩展：

![](../Images/0c4a3ef073fbfaed978a0fd6b3af986c.png)

图 3：图 2 的函数的半范围展开。图片来自作者。

尽管重构中使用的有限项数在不连续点处产生了一些波动，但在极限点处导数为零。

将方程 (4)、(5)、(6) 和 (7) 与在 t=0 时评估的方程 (2) 进行等式比较：

![](../Images/d1dd8d44f0730f5cde5b3962664dc02a.png)

我们可以解出常数：

![](../Images/bf9163236cc5e4b1e2de472651693c17.png)

仔细看看 (14)。这个表达式表明 λₙ 与 n 的平方成正比，其中 n 是特定余弦项在区间 [0, L] 内经过的半周期数。换句话说，n 与空间频率成正比。方程 (2) 包含一个指数因子 exp(λₙt)，迫使每个频率分量随时间衰减。由于 λₙ与频率的平方成正比，我们预测初始温度轮廓的高频分量会比低频分量更快地衰减。

图 4 显示了 u(x, t) 在第一秒内的图像。我们观察到，右侧的高频分量在 0.1 秒内消失。中央部分的中等频率分量显著衰减，但在 1 秒后仍然可见。

![](../Images/d8cc276152f890c17726a93b0f0b0875.png)

图 4：图 2 温度轮廓的 1 秒模拟。图片来自作者。

当仿真运行 100 秒时，我们得到几乎均匀的温度：

![](../Images/67a5f6485c215b1d276f61196a994b50.png)

图 5：两端绝热条件下的 100 秒仿真。图片来自作者。

## 两端固定温度

当两端保持在常温时，我们有如下形式的边界条件：

![](../Images/045674bafc0a78fe4e4feaeed050d234.png)

我们在上一篇文章中研究的傅里叶级数集没有包括边界温度固定为非零值的情况。我们需要重新构造初始温度轮廓 u₀(x)，以开发一个在 x=0 和 x=L 处评估为 0 的函数。我们定义一个偏移的初始温度轮廓 û₀(x)：

![](../Images/4d8e0dae6370c5d99e05cd597ed42800.png)

新定义的函数 û₀(x) 线性地将初始温度轮廓 u₀(x) 移动，使得 û₀(0) = û₀(L) = 0。

作为示例，图 6 显示了一个任意的初始温度轮廓 u₀，设置 x=0 时温度为 30，x=0.3 时温度为 70。绿色线（Cx + D）从 (0, 30) 到 (0.3, 70)。橙色曲线表示 û₀(x) = u₀(x) — Cx — D：

![](../Images/01fde30377640d329b778557ac43d538.png)

图 6：任意的 u₀(x)、û₀(x) 以及直线 Cx + D。图片来自作者。

将偏移的初始温度轮廓 û₀(x) 代入，并在两端为零，可以通过[奇数半范围展开](https://medium.com/towards-data-science/assorted-flavors-of-fourier-series-on-a-finite-domain-8c93904df6ae)来展开：

![](../Images/86532552d61433ea2c95eacdf3b0f933.png)

将方程 (2) 与 (17)、(18)、(19)、(20) 和 (21) 进行等式比较：

![](../Images/b4ba4e2c477db86325e0d54666794a0b.png)

我们可以求解常数：

![](../Images/263f66782a4f41571c521ed1fbffb3dd.png)

现在可以运行温度曲线随时间变化的模拟u(x, t)，该方程来自于公式（2）：

![](../Images/a3d40d58d914dac009b047f5a93577e5.png)

图7：模拟温度演化，其中两端温度均保持恒定。图片由作者提供。

在稳态下，温度曲线在两端之间是线性的，恒定的热流通过金属杆传递。

## 左端绝热，右端温度固定

我们有以下边界条件：

![](../Images/34b515c4b24b3cbe8ae4f1674a7958a5.png)

我们基本上按照之前的相同程序进行。这一次，我们使用[偶数区间展开](https://medium.com/towards-data-science/assorted-flavors-of-fourier-series-on-a-finite-domain-8c93904df6ae)来模拟初始温度分布，以便在左端获得零导数，在右端获得固定值：

![](../Images/e98c81a09dbdf2a60939c37c12e3b3ed.png)

这导致了以下常数：

![](../Images/a4a0f0c918455a57b253e78e46afa441.png)

1000秒的模拟展示了预期的行为。左端温度梯度为零，右端保持恒定温度。稳态是一个温度均匀的金属杆：

![](../Images/84d2c75b40be889939d168f8096bbdd8.png)

图8：模拟温度演化，其中左端为绝热，右端温度保持恒定。图片由作者提供。

# 结论

我们回顾了薄金属杆中温度分布动态的问题。从控制方程出发，我们推导出了通解。

我们考虑了各种边界配置。这些边界情况促使我们根据前一篇文章中推导的[傅里叶级数形式](https://medium.com/towards-data-science/assorted-flavors-of-fourier-series-on-a-finite-domain-8c93904df6ae)来表示初始温度分布。初始温度分布的傅里叶级数表达式使我们能够求解积分常数，并运行u(x, t)的模拟。

感谢您的阅读。您可以在[这个仓库](https://github.com/sebastiengilbert73/heated_metal_bar)中试验代码。告诉我您的想法！

¹ 如果有遗漏的项，请在评论中告诉我。

# 附录I

我们想要证明热扩散方程的解是：

![](../Images/8b268f9c99bbe616f936f7716e5ef313.png)

是：

![](../Images/d8ada3abd6ca3fa718b52133693b6dcb.png)

首先我们需要确认，如果一个函数u*(x, t)满足（A1.1），那么函数u*(x, t) + Cx + D + Et + E/(2α) x²也满足（A1.1）。

证明：

![](../Images/3c5010fc8e84f6ee8681cb4d80df9073.png)

因此，通解必须包含Cx + D + Et + E/(2α) x²项。

现在，信念的飞跃：*可分离性假设*。

假设一个解u(x, t)具有以下形式：

![](../Images/34a184ee6e871ff2119b10c5faef8131.png)

> 为什么要做这样的假设？

因为这将使得解更容易找到。如果我们能够找到一个有效的解，证明将在*事后*出现。在这种情况下，我们不会冒着基于错误假设得出错误结论的风险，因为我们总是可以对找到的解进行微分并检查它是否满足(A1.1)。

![](../Images/3215ebbc060c36c8800d5b4a05130b08.png)

将(A1.4)和(A1.5)代入(A1.1)：

![](../Images/cf6cda988d71875c7b325e6b38e9dcff.png)

在方程(A1.9)中，我们观察到我们将t的函数与x的函数相等。满足此方程的唯一方法是让这两个函数都为常数。因此，我们引入了常数λₙ，它必须与这两个表达式匹配。

(a) 从(A1.9)解出Gₙ(t)：

![](../Images/a05f5ea581c2b222073af1187ba6520e.png)

将(A1.11)和(A1.12)代入(A1.10)：

![](../Images/ce34504e1da5a32fd51f785811f0e617.png)

将(A1.15)代入(A1.11)：

![](../Images/dd2393f23e7712bcf2582947c071985a.png)

(b) 从(A1.9)解出Fₙ(x)：

![](../Images/fa96699f01351df3c445abbc00d57126.png)

将(A1.18)和(A1.20)代入(A1.17)：

![](../Images/172c0c8b3e73a76e1ff2e0e71581fed9.png)

将(A1.24)代入(A1.18)：

![](../Images/6f69848a34f5e9891344f1085f154946.png)

我们假设Fₙ(x)是唯一的，并且正弦函数中的±符号被常数Bₙ吸收。

![](../Images/ee9482764691076f91ddbd9ec789995a.png)

将(A1.16)和(A1.26)代入一般解中：

![](../Images/faee39c64f059dd73f03492cfffa87df.png)

不失一般性，我们可以设置G₀=1，并让它被常数Aₙ和Bₙ吸收：

![](../Images/6c42546fb65332d8b8bf1a6908fe9936.png)

□
