# Bend 如何工作：一种“感觉像 Python，但扩展性像 CUDA”的并行编程语言

> 原文：[`towardsdatascience.com/how-bend-works-a-parallel-programming-language-that-feels-like-python-but-scales-like-cuda-48be5bf0fc2c?source=collection_archive---------1-----------------------#2024-06-26`](https://towardsdatascience.com/how-bend-works-a-parallel-programming-language-that-feels-like-python-but-scales-like-cuda-48be5bf0fc2c?source=collection_archive---------1-----------------------#2024-06-26)

## Lambda 演算、交互组合子以及它们如何用于在 Bend / HVM 上并行化操作的简要介绍。

[](https://medium.com/@lucasdelimanogueira?source=post_page---byline--48be5bf0fc2c--------------------------------)![Lucas de Lima Nogueira](https://medium.com/@lucasdelimanogueira?source=post_page---byline--48be5bf0fc2c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--48be5bf0fc2c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--48be5bf0fc2c--------------------------------) [Lucas de Lima Nogueira](https://medium.com/@lucasdelimanogueira?source=post_page---byline--48be5bf0fc2c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--48be5bf0fc2c--------------------------------) ·阅读时间：22 分钟·2024 年 6 月 26 日

--

![](img/398ffec82c1dd32cf300f28b5c5ae933.png)

图片来自作者

# 引言

如果你正在阅读这篇文章，你可能最近听说过 Bend，一种新的编程语言，旨在实现大规模并行计算，但你无需担心像线程创建等常见的并行编程概念。

如果你不知道我在说什么，请观看下面的视频：

他们声称“***它感觉像 Python，但扩展性像 CUDA***”。作为并行编程的爱好者，这立刻引起了我的注意。经过一番阅读，我发现 Bend 是由 HVM（高级虚拟机）驱动的，这就是所有魔法发生的运行时环境。也就是说，在 Bend 程序中，Bend 代码会被编译成 HVM，HVM 通过一些魔法使得程序以本质上并行的方式运行。从某种意义上说，所有可以并行化的操作都被这个运行时自动并行化了。

立刻，我就想了解所有 HVM 魔法是如何发生的。如何才能做到这一切？经过一些阅读，我了解到 HVM 背后的魔法主要基于交互组合子（Interaction Combinators），这是一种基于图形和图形规则的计算模型，由 Yves Lafont 在 1990 年代开发。因此，我打开了 Lafont 的[论文](https://core.ac.uk/download/pdf/81113716.pdf)，翻了几页，看到了这一部分：

![](img/8dd7792bc5d746abcd3c81f8bbbb06bf.png)

???? 交互组合子外星代码。图像来源于作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

我感觉就像是在电影 *降临* 中，外星人试图用一种奇怪的符号语言与我们交流。

就是在那时，我关闭了笔记本电脑，放弃了理解这个东西的尝试。

一段时间后，当我再次打开计算机时，那些符号依然在那里，盯着我看，仿佛它们在要求我理解它们。

经过大量阅读、观看视频和外星人的帮助后，我不知怎的开始理解这一点。

本文的目的是简要阐明 HVM 魔法是如何发生的，并通过解释一些你在学习过程中可能遇到的常见术语，帮助你进一步理解。为此，我们需要先了解一些基本概念。

# λ-计算（Lambda 计算）

Lambda 计算是由阿隆佐·丘奇（Alonzo Church）在 1930 年代创建的数学逻辑中的一个形式系统。它的目的是从纯数学的角度研究逻辑理论的某些方面。丘奇的目标是用数学术语定义什么是可计算性（即什么可以通过一组基本规则来计算）。让我们开始：

你可能已经使用过 Lambda 计算了。例如，假设有一个将数字乘以二的函数：

f(x) = 2 * x

在 Python 中，你可以这样表示一个命名函数：

```py
def multiply_by_two(x):
    return 2 * x

print(multiply_by_two(2))
# 4
```

但你也可以使用 lambda 来表示，它本质上是一个匿名函数：

```py
multiply_by_two_lambda = lambda x: x * 2

print(multiply_by_two_lambda(2))
# 4
```

所以，让我们回到数学。在 Lambda 计算中，你可以使用符号 *λx.2x* 来表示相同的函数，其中 *x* 是 *参数*，而 *2x* 是 *主体*。

*λ<parameter>.<body>*

这叫做 *抽象*。一个抽象 *λx.t* 表示一个匿名函数，该函数接受一个输入变量 *x* 并返回 *t*。例如，*λx.(x²+2x)* 是一个抽象，表示由 f(x) = x²+2x 定义的函数。所以，抽象基本上定义了一个函数，但并没有调用它。

你也可以有一个像 *λx.(x+y)* 这样的项，它是 f(x) = x+y 的定义。在这里，*y* 还没有定义。表达式 *λx.(x+y)* 是一个有效的抽象，表示一个将输入加上尚未知道的 *y* 的函数。

如果使用 *λx.2x* 定义一个函数，*(λx.2x)a* 就是“调用”一个带有参数“a”的函数。也就是说，我们基本上是将变量“x”替换为“a”。

f(x) = 2x

f(2) = 4

这与以下等价：

*λx.2x*

*(λx.2x)2 = 4*

这叫做 *应用*。我们正在将 *抽象 (λx.2x)* 应用到数字 *2* 上。

你也可以将一个 lambda 表达式应用到另一个 lambda 表达式中，像是嵌套函数：

设 f(x) = 2x 和 g(x) = x³

然后你想要 g(f(x))：

你可以使用 Lambda 表达式来表示它：

*λx.2x*

*λx.x³*

*=> (λx.x³)(λx.2x)*

现在不要急着解决它，先理解符号的含义，稍后我会展示如何解决！

重要的是不要混淆括号。例如：

*1 — λx.((λx.x)x)* 是一个 *抽象*（函数定义）。

*2 — (λx.(λx.x))x* 是一个 *应用*（函数应用）。

在示例 1 中，我们定义了一个函数 *λx.B*，其中 B 是表达式 (*λx.x)x*，即将匿名函数 *λx.x* 应用于输入 x。

在示例 2 中，我们将匿名函数 *λx.(λx.x)* 应用到输入 *x*。

*应用* 也可以表示为 *f* *x*（将函数 *f* 应用到变量 *x*）。

我们也可以使用 Lambda 计算表示具有 *n* 个参数的函数。这可以通过使用嵌套函数来完成，每个函数接受一个单一参数：*f(x,y,z) → λx.*λy.λz

因此，*f(x, y, z) = x + y + z* 可以通过 *抽象* 表示为：

*λx.*λy.λz.(x + y + z)。

使用这个 *抽象* 我们可以构造 *应用*：

*(λx.*λy.λz.(x + y + z))1 2 3 => 6

在学习 Lambda 计算时，还有两个常见的术语你可能会遇到：

Alpha 转换（**α-转换**）和 Beta 约简（**β-约简**）

## Alpha 转换

在评估更复杂的 Lambda 表达式时，你可能会得到这样的表达式：

*(λx.(λx.x+x)x)*

在这个表达式中，内部的 *x* 可能会被错误地解释为外部的 *x*。为了避免这种情况，我们可以重命名内部变量 x：

*(λx.(λy.y+y)x)*

这个过程就是所谓的 *α-转换*，虽然名字看起来有点复杂，但其实就是这样简单的重命名变量以避免错误。

*λx.x → λy.y (α-转换)*

两个表达式表示相同的函数。*α-转换* 并不会改变函数的行为，只是更改了变量的名称。

## Beta 约简

*β-约简* 仅仅是通过将函数应用于表达式来计算结果的过程。例如：

*(λx.xy)z*

在输出 *xy* 时，将每个出现的 x 替换为 z

= *zy*

你还可能会看到以下表示法：

*(λ param . output)input => output [param := input] => result*

这基本上意味着要得到结果，你需要查看 *输出*，并将每个出现的 *param* 替换为 *input*。在之前的表达式中：

*(λx.xy)z => (xy)[x := z] => zy*

## 示例

回到我们的例子 f(x) = 2x；g(x) = x³，我们想计算 g(f(1))。

为了避免错误混淆术语，我们可以重写：

f(x) = 2x 和 g(y) = y³

然后，我们将 f 替换到 g 中：

g(f(1)) = (f(1))³

=> g(f(1)) = (2*1)³

=> 8*x = 8.

现在使用 Lambda 计算：

*λx.2x*

*λx.x³*

*=> (λx.x³)((λx.2x)1)*

首先，应用 *α-转换* 以避免混淆：

*(λy.y³)((λx.2x)1)*

然后，对最内层的表达式进行 *β-约简* *(λx.2x)1*：

*(λ param . output)input => output [param := input] => result*

*(λx.2x)1 => 2x [x := 1] => 2*1 = 2.*

然后，再次对得到的表达式 *(λy.y³)2* 进行 *β-约简*：

*(λ param . output)input => output [param := input] => result*

*(λy.y³)2 => y³[y := 2] => 2³ => 8.*

我们得到了相同的结果！这真是太棒了吧？

_________________________________________________________________

⚠️ 如果此时你开始感到困惑，请不要关闭文章！！我明白一开始可能会很具挑战性，但我保证你，今天晚上睡觉时，明天醒来时，事情会更加清晰！所以，请继续阅读，享受接下来的内容 :)

_________________________________________________________________

在 Lambda 计算之后的几年，艾伦·图灵提出了图灵机的概念，这是一种能够模拟任何可以用数学描述的算法过程的计算机抽象数学模型。在 Church 和 Turing 的工作基础上，已确定 Lambda 计算与图灵机之间存在理论等价性。这种等价性意味着，尽管 Lambda 计算没有数字或布尔值，但任何可以由图灵机计算的问题也可以用 Lambda 计算的术语表达。因此，我们可以用 Lambda 计算来表达任何可计算的算法！让我们了解一下如何实现这一点。

## 数字

我之前提到过 Lambda 计算没有数字，只有 Lambda 表达式。但那么，我怎么能写出像 *λx.(x+2)* 这样的东西呢？

好吧，我骗了你…… 😞

但别生气，这只是为了便于理解 😀

现在，让我们了解一下 Church 如何仅通过 Lambda 表达式表示数字：

Church 数字表示法一开始可能有点复杂，但随着深入理解，它会变得更清晰。

Church 数字 `n` 被定义为一个函数，它接受一个函数 `f` 并返回将 `f` 应用于其参数 `n` 次。

0: λf.λx.x （将 `f` 应用于 `x` 0 次）

1: λf.λx.f x （将 `f` 应用于 `x` 1 次）

2: λf.λx.f(f x) （将 `f` 应用于 `x` 2 次）

3: λf.λx.f(f(f x)) （将 `f` 应用于 `x` 3 次）

依此类推……

这似乎很混乱，但稍微思考一下后，你会开始理解的。Church 数字 `n` 只是意味着做某事 `n` 次。

一种很好的说明方式是记住数字的概念来源于计数的过程。例如，假设你有一个 20 步的楼梯。当说到爬楼梯时，你需要爬 20 步，这意味着你要爬 20 次，每次爬一步，对吧？这正是 Church 编码的概念：你有一个函数 `f`，它表示“爬一步”，如果你想表达 20 步的概念，你就将 `f` 应用 20 次。

## 数值运算

定义了 Church 数字之后，我们可以定义数值运算。第一个是定义一个后继函数 ***s***。它基本上是一个将 Church 数字增加 1 的函数。因此，后继函数是一个接受表示数字 `n` 的 Church 数字，并返回表示 `n+1` 的 Church 数字的函数。

例如，如果 λf.λx.f(f x) 表示数字 2，当我们将后继函数 **s** 应用到它时，我们将得到 λf.λx.f(f(f x))（数字 3 的 Church 表示法）。

后继函数定义如下：

*s(n) =λn.λf.λx.f((n f) x)，* 其中 `n` 是 Church 数字 `n`。

让我们分析一下：

+   **输入**：`n`（一个教堂数字），`f`（一个函数），和 `x`（一个参数）

+   ***n 的应用**：术语 `(nf)x` 表示将函数 `f` 应用到参数 `x` 上 `n` 次。

+   **附加应用**：术语 `f((nf)x)` 将函数 `f` 再应用一次到 `(nf)x` 的结果。

如果教堂数字 `n` 表示做某事 `n` 次，`s n` 则表示做某事 `n+1` 次。

例如，让我们将后继函数应用于数字 1 的教堂数字表示法：

**教堂数字 2**：[*λf.λx.f(f x)*](http://.)

**应用此表达式的后继：**

我们知道 *s(n) = λn.λf.λx.f((n f) x)*

我们的 n = 2 = [*λf.λx.f(f x)*](http://.)

因此，我们对其应用后继函数：

s([**λf.λx.f(f x)**](http://.)*) = ( λn.λf.λx.f((n f) x) )(* [**λf.λx.f(f x)**](http://.) *)*

对 *应用* 表达式进行第一次 *β-约简*：

*(λ param . output)input => output [param := input] => result*

*( λn.λf.λx.f((n f) x) )(* [**λf.λx.f(f x)**](http://.) *) => λf.λx.f((n f) x) [n :=* [**λf.λx.f(f x)**](http://.)**]**

=> *λf.λx.f((*[**λf.λx.f(f x)**](http://.)*f x)*

现在，让我们分析内部的 *应用* 表达式：

*(*[**λf.λx.f(fx)**](http://.)*f x*

下划线部分是教堂数字 2，对吧？它可以解释为：

给定一个函数 *f*，将 *f* 应用两次于其参数 *x*。

*(*[**λf.λx.f(fx)**](http://.)*f x* 变成 *f(f x)*

将我们的表达式 *λf.λx.f((*[**λf.λx.f(fx)**](http://.)*f x),* 代入后，我们得到：

*λf.λx.f(f(f x)),* 这正是数字 3 的教堂数字表示法！

所以，我们刚刚定义了后继 lambda 操作。通过使用这个概念，如果我们定义 0 = λf.λx.x，我们可以通过递归地应用后继函数来获得其他教堂数字：

1 = **s** **0**

2 = s(**s** **0**)

3 = **s**(**s**(**s** **0**))

…

我们可以利用这些函数来实现其他操作，如加法和乘法。

两个数字 m + n 的加法定义为：

*ADD(m, n) = λm.λn.λf.λx.(m f)((n f) x)*

因此，如果我们将 *m* 和 *n* 定义为分别表示数字 3 和 4 的教堂数字表示法，然后应用这个加法函数，我们将得到数字 7 的教堂数字表示法。

同样的逻辑适用于两个数字 m * n 的乘法：

*MUL(m, n) = λm.λn.λf.λx.m (n f)*

任何时候都可以尝试应用自己！

## 布尔值

在进入教堂定义之前，让我们先将布尔值视为我们可以用来进行 *选择* 的某种操作。在两个选项 A 和 B 之间，依据某个条件，选择 A 或 B。

`IF [CONDITION] THEN [RESULT A] ELSE [RESULT B]`。

例如，在某个应用程序执行过程中，如果我们想使用布尔值来更改屏幕的背景颜色：

“red_theme = True”

这只有在程序的其他部分我们进行某种 *选择* 时才有用：

background_color = 如果 red_theme 为真，则为 red，否则为 white。

因此，我们从布尔值中需要的只是某种条件选择两个选项的方式。

基于此，在λ演算中，*true*和*false*的 Church 定义被定义为两个参数的函数：

+   *true*选择第一个参数。

+   *false*选择第二个参数。

*TRUE* = *λx.λy.x*

*FALSE* = *λx.λy.y*

看起来有点奇怪，对吧？但是让我们定义一些布尔操作，看看会发生什么：

**NOT**：接受一个布尔值并返回其相反值。

`NOT = λp. p FALSE TRUE`

这意味着：“取一个布尔函数`p`。将`p`应用到两个参数`FALSE`和`TRUE`。”

还记得 Church 编码中的布尔值定义吗？*TRUE*返回第一个参数，*FALSE*返回第二个参数？因此：

→ 如果`p`是`TRUE`，则返回`FALSE`。

→ 如果`p`是`FALSE`，则返回`TRUE`。

**AND**：接受两个布尔值，如果两个都为*TRUE*，则返回*TRUE*，否则返回*FALSE*。

`AND = λp.λq.p q p`

这意味着：“取两个布尔函数`p`和`q`。将`p`应用于`q`和`p`。”

让我们在实践中尝试：

*AND TRUE FALSE* = (*λp.λq.p q p) TRUE FALSE:

给定*TRUE*和*FALSE*，返回*TRUE FALSE TRUE*：

=> *TRUE* *FALSE* *TRUE* = *λx.λy.x FALSE TRUE*

给定*FALSE*和*TRUE*，返回第一个参数：

*λx.λy.x FALSE TRUE = FALSE*

其他布尔操作如 OR、XOR 等的定义遵循相同的思路。

## 实践

现在，让我们在实践中使用一些λ演算：

```py
# Lambda function abstraction
def L(f):
    return f

# Church numeral 0
ZERO = L(lambda f: L(lambda x: x))

# Successor function: λn.λf.λx.f (n f x)
SUCC = L(lambda n: L(lambda f: L(lambda x: f(n(f)(x)))))

# Addition: λm.λn.λf.λx.m f (n f x)
ADD = L(lambda m: L(lambda n: L(lambda f: L(lambda x: m(f)(n(f)(x))))))

# Multiplication: λm.λn.λf.m (n f)
MUL = L(lambda m: L(lambda n: L(lambda f: m(n(f)))))

# Convert integer to Church numeral
def to_church(n):
    if n == 0:
        return ZERO
    else:
        return SUCC(to_church(n - 1))

# Helper function to compare Church numerals
def church_equal(church_number_1, church_number_2):
    f = lambda x: x + 1
    return church_number_1(f)(0) == church_number_2(f)(0)

church_two = to_church(2)
church_three = to_church(3)
church_five = to_church(5)
church_six = to_church(6)

# Successor of 2 is 3
assert church_equal(SUCC(church_two), church_three)

# 2 + 3 = 5
assert church_equal(ADD(church_two)(church_three), church_five)

# 2 * 3 = 6
assert church_equal(MUL(church_two)(church_three), church_six)

print("All tests passed.")
```

如你所见，我们只使用λ函数执行数值操作！！此外，通过扩展这个方法并结合λ布尔逻辑，我们甚至可以仅使用λ函数实现 if/else、循环，甚至整个编程语言！是不是很神奇？

好的，在简要介绍完λ演算之后，我们可以进入我们旅程的下一个话题。

# 交互网

在直接进入交互组合子之前，我们先学习一下 Yves Lafont 的另一项早期工作：交互网。这一基础将使理解交互组合子变得更加容易。

交互网是由 Yves Lafont 在 1990 年创建的一种计算模型。它们使用类似图形的结构和一组交互规则来表示算法。

我们需要定义的第一件事是一个*单元*。它由某个符号*例如α*，一个主端口和 n 个辅助端口组成，如下图所示：

![](img/ba40de956a0e60e58c173fb65fa5a68b.png)

单元 — 图片由作者提供

当一个*单元*的辅助端口数 n = 0 时，它的表示如下：

![](img/8e7e876645ce29028268c7ee95d7299e.png)

arity n=0 的单元 — 图片由作者提供

通过将一组*单元*通过它们的*端口*用*电线*连接，我们构造一个*网络*。例如，一个包含*单元α, β*和*γ*的网络，其中[arity](https://en.wikipedia.org/wiki/Arity)分别为 n = 2, 1 和 0。

![](img/93ad5ba679bdd192864241e7bc4f7d17.png)

图片由作者提供，灵感来自[Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

请注意，一个*电线*可以连接同一个*单元*的两个*端口*，并且一个*网络*不一定需要完全连接。此外，在这个示例中有三个自由端口*x*、*y*和*z*。

每当一对单元通过它们的[***主要***](http://.) *端口*连接时，就会发生一次*交互*。*交互*是一条规则，它会修改*网络*。这些通过*活动*端口连接并准备交互的单元对称为*活动对*（或*红雷*）。

在前面的例子中，第一轮有两个可能的*交互*（*活动对*）。

![](img/5c5f9513d5084292e2586a53b4e278a6.png)

作者图像，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

在应用这些规则后，*网络*将被修改。然后，我们会将这些规则反复应用到结果生成的*网络*上，直到达到一个不可简化的形式，即无法再应用任何*交互*规则时为止。这个反复应用*交互*规则的过程也被称为归约。

*交互系统*是由一组可以毫不含糊地应用的交互规则构成的。也就是说，如果我们为*活动对（αi, αj）*定义了一个*交互*规则，那么对于所有出现的*(αi, αj)*，这个规则都是一样的。

在这个简短的解释之后，让我们做一些练习。

## *构建一个算术交互系统*

让我们构建一个用于做算术的交互系统。为了创建它，我们首先要忘记关于数字的基本直觉，尝试创建一个能够建模自然数的系统。在 1889 年，Giuseppe Peano 提出了五条公理来形式化自然数，这类似于欧几里得为几何定义的公理。Peano 的公理使得一个有限符号集和规则可以生成一个无限的集合。通过这些公理，Peano 为自然数及其算术性质定义了一些规则：

***0 → 表示数字零***

***s(n) → 表示后继函数。它返回下一个自然数。***

使用**s**和**0**，我们可以定义自然数，正如我们之前在λ演算学习中所看到的：

1 = **s(0)**

2 = **s(s(0))**

3 = **s(s(s(0)))**

依此类推……

***+ → 表示加法。它是一个递归定义的函数，如下所示：***

***基础情况：*** 0 + a = a

***递归：*** a + s(b) = s(a+b)

例如：

a + 3：

= a + s(2)

= s(a+2)

= s(a+s(1))

= s(s(a+1))

= s(s(a+s(0)))

= s(s(s(a+0)))

= s(s(s(a)))

***×：表示乘法。它是一个递归定义的函数，如下所示：***

***基础情况：*** b × **0** = **0**

***递归：*** s(a) × b = (a × b) + b

受此启发，Yves Lafont 构建了一个交互系统，用以建模自然数和算术。让我们来理解：

首先，他为**s**和**0**符号定义了*单元*：

![](img/4ea3ec73a34ccf2d96466542da9acaa7.png)

作者图像，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

然后，定义加法运算的*单元*：

![](img/d95bc121072804cd7d37597788eb8880.png)

作者图像，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

我知道这看起来很奇怪，但我保证它会变得更加有意义。

如果所有自然数都可以仅使用符号 **0** 和后继 **s** 来表示，那么对于加法，我们只需要定义两个 *交互* 规则：加法如何与后继和 **0** 进行交互。因此，Lafont 提出了以下两个 *交互* 规则：

![](img/889329bea2557cffd9453a2471b0bbfe.png)

图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

将这些规则与皮亚诺加法的方程进行比较，它们正好是相同的表达式：

s(x) + y = s(x+y)

**0** + y = y

现在，让我们理解乘法的 *交互* 规则。乘法的 *单元格* 定义如下：

![](img/8fd1125c9726eca7ace99e90589b24b7.png)

图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

现在，看看皮亚诺的方程：

y × **0** = **0**

s(x) × y = (x × y) + y

注意，第一个方程“抹除”了 y 变量（y 出现在方程的左侧，但在右侧没有出现）。在第二个方程中，y 被“复制”了，通过另一个乘法和加法。

因此，还需要另外两个符号：*ε (抹除器)* 和 *δ (复制器)*。

![](img/ad52d6c71df9df7fbe0efef5b4b1d0ec.png)

图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

这些符号的思想是，表示自然数的网络在连接到 *ε* 的主端口时会被抹除，而连接到 *δ* 的主端口时则会被复制。现在，乘法规则可以表示为：

![](img/5edbb6983572225d6bf4503b71f98e38.png)

图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

尝试反思它们与皮亚诺表达式的相似之处：

s(x) × y = (x × y) + y

y × **0** = **0**

*交互* 规则对于 *复制器* 和 *抹除器* 与 *后继* 和 0 的定义如下：

![](img/1fee674e8bd92a26f2008a095ee6487f.png)

图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

因此，我们有一组六个符号 **{0, s, +**, ×, *δ, ε*} 和以下八个 *交互* 规则：**{(s**, +), **(0, +)**, **(s**, ×), **(0**, ×), **(s***, δ*)*,* **(*0****, δ*)*,* **(*s****, ε*)*,* **(*0****, ε*)}。让我们通过操作 2 × 2 来实际分析它们。

![](img/7761269faea0c99e55e76dd3028a0622.png)

2 x 2。图像来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

如果你仔细看，会发现有一个活跃的对 (**s**, ×)，我们可以应用规则 #3。

![](img/4d416f15180cb9da8b6cf32136505daf.png)

应用*交互规则#3*。图片来源：作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

因此，运算通过应用*交互*规则来解决，直到我们得到一个不可约的形式：

![](img/67e138061a4d076406a1a498898e633e.png)

2x2 = 4。图片来源：作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

看看我们已经得到的最终形式：**s**(**s**(**s**(**s** **0**))).

![](img/5ada222ea12c2d41ccfdf3c448047307.png)

图片来源：作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

这正是数字 4 的定义，2 × 2 的结果！令人惊讶，对吧？经过一些奇怪符号的操作后，我们竟然能解出一个算术运算！😀

那么，为什么要做这么复杂的事情呢？使用这些操作来解决问题有什么优势呢？

Lafont 的网络有一个有趣的性质：如果一个网络*μ*能在一步内减少到两个不同的可能网络*v*或*v’*，那么*v*和*v’*都能在一步内减少到一个共同的网络ξ。

![](img/e93f8e38d3ba54b98c86d32876d794d3.png)

图片来源：作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

这种汇聚性质的结果是，如果一个网络*μ*在*n*步内减少到*v*，那么任何一系列的减少都会在*n*步内达到*v*。换句话说，*交互*规则的应用顺序并不重要，网络将在相同的步数内达到相同的形式！

你感受到这种性质的力量了吗？基本上，如果交互的顺序无关紧要，我们就可以并行应用它们！🤯

例如，在我们之前的 2 × 2 运算中，我们可以在类似的时刻并行应用这些规则，而不是一条条逐个应用：

![](img/9025d9f135f76590a34ec27617ad153c.png)

图片来源：作者，灵感来自于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

在实际执行中，这两条规则可以通过在两个独立的线程中并行运行，从而避免线程冲突和其他与并行性相关的常见问题。这也是 HVM/Bend 所基于的核心原则之一！基于这一点，所有可以并行化的操作都将被本质地并行化！

现在我们已经理解了交互网络，让我们再迈进一步。之前在本文中，我提到 HVM 是基于交互组合子的，那么让我们来理解这些概念之间的关系。

# 交互组合子

基于他早期的交互网工作，Yves Lafont 创建了交互组合子。这个思想是使用一个最小的基本符号集（称为组合子）来创建计算的表示。虽然交互网通过图重写显式地建模计算，交互组合子则通过专注于基本的组合逻辑来改进这一点。这一转变提供了一个更加抽象但更强大的框架，用于表达计算过程。

对于交互组合子，Lafont 定义了三个符号（也称为组合子）：γ（*构造子*），*δ*（*复制子*）和*ε*（*擦除子*）。

使用这三个组合子，总共创建了仅六条规则。这些规则分为：

*交换* — 当两个不同符号的单元互相作用时（γ*δ,* γ*ε, δε*）；

*湮灭* — 当两个相同符号的单元互相作用时（γγ，*δδ，εε*）。

规则如下所定义：

![](img/3aa98d277576638e82070613893d2955.png)

交换规则。图片来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

![](img/97e11564dd402b1e26d0184ab2c06034.png)

湮灭规则。图片来自作者，灵感来源于 [Lafont, 1997](https://www.sciencedirect.com/science/article/pii/S0890540197926432)

因此，仅使用这六条规则，你就可以建模任何可计算的算法！惊人吧，对吧？

然而，HVM 运行时使用的是 Lafont 的交互组合子的一个变体，称为[对称交互组合子 (SIC) (Mazza, 2007)](https://lipn.univ-paris13.fr/~mazza/papers/CombSem-MSCS.pdf)。这个变体是一个简化版，它对所有符号使用相同的重写规则：

![](img/0d41aba45a4959823ee000d41ec2d648.png)

对称交互组合子规则。图片来自作者，灵感来源于 [Mazza, 2007](https://lipn.univ-paris13.fr/~mazza/papers/CombSem-MSCS.pdf)

如你所见，唯一的区别是规则γγ和*δδ*现在是相似的*。* 关键的汇聚性质得以保持，保留了其并行化能力。

*从现在开始，我们将使用 SIC 规则进行示例，因此请专注于它们。*

## Lambda 演算 → 对称交互组合子

现在你可能会问：“我该如何使用这些写程序？如何将我的 Python 函数转换为交互组合子图形？”

我之前提到过，你可以使用λ演算表示任何可计算的算法，对吧？

现在有另外一条信息：你可以将λ演算转换为交互组合子！

因此，任何程序都可以转换为λ演算，然后转换为交互组合子，进行并行运行，再转换回来！

![](img/e076537acd1508b1239640bb52df6823.png)

图片来自作者

那么，让我们理解如何将λ转换为交互组合子！

Lambda *表达式 ( λ )* 和 *应用*（@）可以通过构造子γ表示。例如，lambda *表达式 λx.y* 可以表示为：

![](img/c58777ecbcf6e4a9491e3330b3b727ff.png)

使用 SIC 的 Lambda 表达式。作者提供的图片

对于给定的 *应用 f x*，我们可以将其表示为：

![](img/ebe37c5809c0a8c423f30e0b0fd100aa.png)

使用 SIC 的 Lambda 应用。作者提供的图片

使用这些表示法，我们可以表达恒等式 *表达式 λx.x（*给定 *x*，返回 *x* 本身*）：*

![](img/e373013cf236a706a208fb551c9163fb.png)

*λx.x。作者提供的图片*

现在，假设我们想进行 *应用*（*λx.x)y*：

![](img/a35facf020a380ffda8f89664bb32938.png)

*(λx.x)y* 作者提供的图片

如果我们简化表达式 (*λx.x)y，*我们会得到 *y* 作为结果。让我们分析一下使用 SIC 规则可以得到什么？

请注意，当一个 *应用* 应用于一个 lambda *表达式* 时，会有一个 *活动对*，我们可以简化它！在这种情况下，我们将应用交互规则 γγ。同时，从现在开始，我们将使用圆圈来标识我们感兴趣的最终计算结果。

![](img/cc9a7092abf3788fcfe1de34ad230f0f.png)

作者提供的图片

正如你所看到的，(*λx.x)y* 被正确地简化为 *y*！很神奇，对吧？

现在，假设我们想表达 *λf.ff（*给定 *f*，将 *f* 应用到它自身）。正如你所注意到的，参数 *f* 在主体部分是 *重复* 的。这时，*复制器*（*δ）就派上用场了！我们可以使用 *复制器* 来复制（重复）值：

![](img/278b9ee8e57d4cb0d5631b5852fa73f1.png)

作者提供的图片

让我们回到我们的表达式 *λf.ff*。首先，确认这是一个 *表达式*，它接受输入 *f*，并输出 *f 应用到它自身*。因此，它可以表示为：

![](img/0ce8478c7bffeffd294cca11cf909fd5.png)

“给定 *f*，输出 *f* 应用到 *f*”。作者提供的图片*

除了复制，变量也可以消失。例如，我们来看看教堂数字 **0 :=** *λf.λx.x*。这个表达式可以解读为“给定两个变量 *f* 和 *x*，返回 *x*”。正如你所注意到的，变量 *f* 在输出中没有被使用。如果我们试图用当前的 SIC 知识表示它，我们将得到：

![](img/9ae939db655d63c9f4ebd1d9413bd566.png)

作者提供的图片

*f* 线漂浮着。似乎有点问题，对吧？这就是我们需要 *橡皮擦 ε* 的原因！为了表示这个变量的消失，我们这样做：

![](img/6040c5d8fa64b89e2b89de2eb863ec5c.png)

作者提供的图片。

总结一下，我们可以通过以下方式使用对称交互组合子处理 Lambda 演算：

![](img/069a7921a0fe243a1b2ec902d8e22bc4.png)

作者提供的图片。灵感来源于 [`zicklag.katharos.group/blog/interaction-nets-combinators-calculus/`](https://zicklag.katharos.group/blog/interaction-nets-combinators-calculus/)

# 示例

现在，我们已经覆盖了这些转换，能够执行更复杂的操作了。

## 乔治·教堂数字

让我们画一些乔治·教堂数字！

![](img/71ff48be81fb16b7f945787eb7255ece.png)

作者提供的图片

在我们继续之前，试着自己做一下！拿张纸开始画吧！例如，让我们一起尝试画出教会数 4： *λf.λx.f(f(f(f x)))。*

我画的东西是外部的 lambda 表达式 *λf.____*

![](img/4baf00423b2cde5b9974dbf4bd517d25.png)

给定 f，输出 *λ*x.f(f(f(f x)))。图片由作者提供

然后，第二个 lambda 表达式 __.*λx.*____：

![](img/888164d02fd47ceb1a2abb3a2ff90029.png)

给定 x，输出 f(f(f(f x)))。图片由作者提供

现在，我们需要绘制 *应用*（@）。但首先，请注意我们有 *f* 重复了四次。因此，我们需要复制（重复） *f* 三次（所以我们需要三个连续的复制器）：

![](img/35deea54aa6b8d04016bec1d7ed8b016.png)

f 的复制。图片由作者提供

现在我们有了四个 *f* 的副本，我们可以按顺序绘制 *f* 到 *f* 的 *应用*！

![](img/5669ee7a20e95e2d0fd88ff9ed37c799.png)

使用 SIC 表示法的教会数 4。图片由作者提供

使用相同的策略，我们可以轻松构造其他表达式。

## 后继函数

让我们实现后继函数。它表示为 *λn.λf.λx.f((n f) x)。*

![](img/91eb0ac0fbac96178e46ccddbf24a7b5.png)

后继函数。图片由作者提供

让我们将后继函数应用于数字 *0* 并分析得到的结果。

![](img/73bd398bd363b80c9d6693a543157c27.png)

SUCC 0。图片由作者提供

让我们应用交互规则。为了便于阅读，我将 *复制器* δ 画为黑色单元，而 *构造器* γ 画为白色单元：

![](img/7483476d2c7eba05287c470db99a8219.png)

SUCC 0 的简化。图片由作者提供

好吧，我们应该已经得到了教会数 1，对吧？出了什么问题？看看与复制器 δ（黑色）辅助端口连接的 *橡皮擦 ε*：

![](img/8cc2c94526e90cb7650bcd2f8a8b940b.png)

图片由作者提供

这个 *橡皮擦* 让这个左侧辅助端口变得多余！通过这个复制器传递的所有信息都会被擦除。对于任何与这个 *复制器* 交互的 *单元*，其 *左侧* 部分将被 *擦除*。

因此，我们可以去除这个多余的 *复制器*，并直接连接 *电线*：

![](img/e662d51b21d3f99a9bf46d1bdad4415a.png)

图片由作者提供。

然后，*瞧！* 在简化 SUCC(0) 后，我们得到了正好是教会数 1，正如预期的那样！

让我们再次将 SUCC 应用到数字 1 上，看看我们能否得到数字 2：

![](img/ff417ab383f7dcf4fa7f00fbfc20c8f3.png)

SUCC 1。图片由作者提供

![](img/1c554d864f027d5ae5fd12834413a298.png)

SUCC 1 = 2。图片由作者提供

我们正好得到了教会数 2！很神奇，对吧？

## 加法

到目前为止，我们只进行了顺序化简。让我们做一个更复杂的操作，例如加法，来可视化交互组合器的完整并行化潜力。下面是加法的 SIC 表示法： *ADD(m, n) = λm.λn.λf.λx.(m f)((n f) x)。*

![](img/07cb4ec55cf4a0856b417a91a615ad90.png)

加法。图片由作者提供

让我们计算 ADD 1 1：

![](img/46ca9526345dc51c5260799e471fea3b.png)

ADD 1 1。图片由作者提供

执行简化操作：

![](img/05ad0dece29ad44eb9b72af6669863e0.png)

图片由作者提供

看一下这一步。这里有两个活跃的配对！像这种情况，我们可以并行地简化这两个。在实际的程序中，我们可以在两个不同的线程中运行它们。

让我们继续：

![](img/0a3e0763cf67f8158a12daa49fcfac41.png)

ADD 1 1 = 2\. 图片由作者提供

在简化 ADD 1 1 之后，我们得到了恰好是教堂数字 2 的表示！

这就是如何使用交互组合子并行化操作的过程。在每一步，如果有多个活跃的配对，它们都会在不同的线程中运行。

# 结论

在这篇文章中，我们涵盖了λ演算的基本概念、交互组合子以及它们如何结合以并行化操作。我希望我能简要地解释 Bend/HVM 是如何工作的，更多信息，请访问他们的[网站](https://higherorderco.com/)。

同时，关注我在这里和我的[LinkedIn 个人资料](https://www.linkedin.com/in/lucas-de-lima-nogueira/)，以便及时了解我的最新文章！

# 参考文献

[HigherOrderCO 网站](https://higherorderco.com/)

[Lafont 的交互组合子论文](https://core.ac.uk/download/pdf/81113716.pdf)

[HVM 工作原理视频](https://www.youtube.com/watch?v=sDPuQ-UjhVQ&t=1591s)

[交互组合子教程 1](https://zicklag.katharos.group/blog/interaction-nets-combinators-calculus/)

[交互组合子教程 2](https://blog.stevenhuyn.com/succ/)

[λ演算教程](https://learnxinyminutes.com/docs/lambda-calculus/)
