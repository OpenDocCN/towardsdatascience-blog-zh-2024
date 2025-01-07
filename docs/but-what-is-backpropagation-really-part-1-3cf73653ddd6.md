# 但反向传播到底是什么呢？（第一部分）

> 原文：[https://towardsdatascience.com/but-what-is-backpropagation-really-part-1-3cf73653ddd6?source=collection_archive---------8-----------------------#2024-02-07](https://towardsdatascience.com/but-what-is-backpropagation-really-part-1-3cf73653ddd6?source=collection_archive---------8-----------------------#2024-02-07)

## 从零开始实现一个简单的神经网络框架

[](https://medium.com/@physboom?source=post_page---byline--3cf73653ddd6--------------------------------)[![Matthew Chak](../Images/88881eb5a7c8f08c15555bc8c3c613d3.png)](https://medium.com/@physboom?source=post_page---byline--3cf73653ddd6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3cf73653ddd6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3cf73653ddd6--------------------------------) [Matthew Chak](https://medium.com/@physboom?source=post_page---byline--3cf73653ddd6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3cf73653ddd6--------------------------------) ·阅读时间：11分钟·2024年2月7日

--

![](../Images/a32c4308353179756efb049de6ad07a3.png)

树——计算的核心。来源：[Adrian Infernus on Unsplash](https://unsplash.com/photos/a-forest-filled-with-lots-of-snow-covered-trees-E4O3FTh9V04)

尽管我在人工智能生态系统中做了一段时间的工作和研究，但直到最近，我才真正停下来思考神经网络中的反向传播和梯度更新。本文旨在纠正这一点，并希望通过从零开始实现一个简单（但有些强大的）神经网络框架，为读者提供一个深入且易于跟随的讲解。

# 基本操作——网络的核心

从本质上讲，神经网络只是从输入空间到目标输出空间的数学函数。实际上，我们可以有效地“解开”任何神经网络，转化为一个函数。比如，考虑下面这个简单的具有两层和一个输入的神经网络：

![](../Images/28d8d57c58d3bff09dc26bfd8b862275.png)

一个简单的神经网络，包含两层和一个ReLU激活。在这里，线性网络有权重wₙ和偏置bₙ

我们现在可以通过从输入层开始，逐层构建一个等效的函数。让我们逐层跟随我们的最终函数：

1.  在输入层，我们从恒等函数开始 *pred(x) = x*

1.  在第一层线性层，我们得到 *pred(x) = w*₁*x + b*₁

1.  ReLU 使我们得到 *pred(x) = max(0, w*₁*x + b*₁)

1.  在最终层，我们得到 *pred(x) = w*₂*(max(0, w*₁*x + b*₁)) + *b*₂

对于更复杂的网络，这些函数当然变得更加复杂，但关键是我们可以构建出神经网络的这种表示形式。

然而，我们可以更进一步——这种形式的函数对于计算来说并不是特别方便，但我们可以将其解析为更有用的形式，即语法树。对于我们的简单网络，树形结构如下所示：

![](../Images/2381ed9dd06321fd769d6bd9003b27b7.png)

我们函数的树形表示

在这种树形结构中，我们的叶节点是参数、常量和输入，其他节点是**基本操作**，它们的参数是它们的子节点。当然，这些基本操作不一定是二元的——例如，Sigmoid 操作是单元的（如果我们不将 ReLU 表示为 0 和 x 的最大值的话，ReLU 也是单元的），我们还可以选择支持多个输入的乘法和加法。

通过将我们的网络看作是这些基本操作的树形结构，我们现在可以通过递归轻松地完成许多任务，这将构成我们反向传播和前向传播算法的基础。在代码中，我们可以定义一个递归的神经网络类，类似于以下形式：

```py
from dataclasses import dataclass, field
from typing import List

@dataclass
class NeuralNetNode:
    """A node in our neural network tree"""
    children: List['NeuralNetNode'] = field(default_factory=list)

    def op(self, x: List[float]) -> float:
        """The operation that this node performs"""
        raise NotImplementedError

    def forward(self) -> float:
        """Evaluate this node on the given input"""
        return self.op([child.forward() for child in self.children])

    # This is just for convenience
    def __call__(self) -> List[float]:
        return self.forward()

    def __repr__(self):
        return f'{self.__class__.__name__}({self.children})'
```

# 反向传播 — 递归链式法则

现在假设我们有一个可微的损失函数，用于我们的神经网络，比如 MSE。回想一下，MSE（对于一个样本）的定义如下：

![](../Images/d309e28f0599f66e68be32b3cdc62c61.png)

MSE 损失函数

我们现在希望根据损失的值来更新我们的参数（树形表示中的绿色圆圈）。为此，我们需要计算损失函数关于每个参数的导数。然而，直接从损失计算这一点是非常困难的——毕竟，我们的 MSE 是根据神经网络预测的值计算的，而这个值可能是一个极其复杂的函数。

这时非常有用的数学工具——链式法则发挥了作用。我们不需要一开始就计算复杂的导数，而是可以计算一系列更简单的导数。

事实证明，链式法则与我们的递归树结构非常契合。基本的思想如下：假设我们有足够简单的基本操作，每个基本操作都知道其关于所有参数的导数。通过父操作的导数，我们可以通过简单的乘法计算每个子操作关于损失函数的导数。对于使用均方误差（MSE）的简单线性回归模型，我们可以将其图示化如下：

![](../Images/cf0d461dba33bca20044ddd701532bc2.png)

一个简单线性分类器的前向和反向传递图，权重为 w1，偏置为 b1。注意 *h*₁ 只是我们乘法操作返回的变量，就像我们的预测是通过加法返回的一样。

当然，我们的一些节点在处理其导数时并没有做任何操作——也就是说，只有我们的叶节点关心导数。但现在每个节点都可以通过这种递归过程得到其输出关于损失函数的导数。因此，我们可以向我们的 NeuralNetNode 类添加以下方法：

```py
def grad(self) -> List[float]:
    """The gradient of this node with respect to its inputs"""
    raise NotImplementedError

def backward(self, derivative_from_parent: float):
    """Propagate the derivative from the parent to the children"""
    self.on_backward(derivative_from_parent)
    deriv_wrt_children = self.grad()
    for child, derivative_wrt_child in zip(self.children, deriv_wrt_children):
        child.backward(derivative_from_parent * derivative_wrt_child)

def on_backward(self, derivative_from_parent: float):
    """Hook for subclasses to override. Things like updating parameters"""
    pass
```

**练习 1：** 尝试为一个简单的线性回归模型创建一个这样的树，并手动执行几步递归梯度更新。

*注意：为了简化起见，我们要求节点只有一个父节点（或者根本没有）。如果每个节点可以有多个父节点，那么我们的 `backwards()` 算法会变得稍微复杂一些，因为每个子节点需要将父节点的导数相加以计算自身的导数。我们可以通过拓扑排序迭代进行处理（例如，参见* [*这里*](https://www.cs.toronto.edu/~rgrosse/courses/csc321_2018/slides/lec10.pdf)*)，或者仍然递归地进行，即通过反向累积（尽管在这种情况下，我们需要做第二遍遍历以真正更新所有参数）。这并不特别困难，所以我将其作为练习留给读者（并将在第二部分详细讨论，敬请期待）。*

*编辑：在撰写本文第二部分时，我注意到我们当前的实现确实支持多个父节点——* `*on_backward*`* 将被多次调用，最终会正确更新权重（我鼓励你思考为什么这样做有效）。为了使一切正常工作，实施过程中需要进行一些小的更新（特别是在后面描述的 `*find_input_nodes*` 方法中），但算法本身无需更改。对此疏漏，我深感抱歉。*

## 构建模型

剩下的代码实际上只是实现参数、输入和操作，当然还包括运行训练。参数和输入是相对简单的构造：

```py
import random

@dataclass
class Input(NeuralNetNode):
    """A leaf node that represents an input to the network"""
    value: float=0.0

    def op(self, x):
        return self.value

    def grad(self) -> List[float]:
        return [1.0]

    def __repr__(self):
        return f'{self.__class__.__name__}({self.value})'

@dataclass
class Parameter(NeuralNetNode):
    """A leaf node that represents a parameter to the network"""
    value: float=field(default_factory=lambda: random.uniform(-1, 1))
    learning_rate: float=0.01

    def op(self, x):
        return self.value

    def grad(self):
        return [1.0]

    def on_backward(self, derivative_from_parent: float):
        self.value -= derivative_from_parent * self.learning_rate

    def __repr__(self):
        return f'{self.__class__.__name__}({self.value})'
```

操作稍微复杂一些，但并不太难——我们只需要正确计算它们的梯度。以下是一些有用操作的实现：

```py
import math

@dataclass
class Operation(NeuralNetNode):
    """A node that performs an operation on its inputs"""
    pass

@dataclass
class Add(Operation):
    """A node that adds its inputs"""
    def op(self, x):
        return sum(x)

    def grad(self):
        return [1.0] * len(self.children)

@dataclass
class Multiply(Operation):
    """A node that multiplies its inputs"""
    def op(self, x):
        return math.prod(x)

    def grad(self):
        grads = []
        for i in range(len(self.children)):
            cur_grad = 1
            for j in range(len(self.children)):
                if i == j:
                    continue
                cur_grad *= self.children[j].forward()
            grads.append(cur_grad)
        return grads

@dataclass
class ReLU(Operation):
    """
    A node that applies the ReLU function to its input.
    Note that this should only have one child.
    """
    def op(self, x):
        return max(0, x[0])

    def grad(self):
        return [1.0 if self.children[0].forward() > 0 else 0.0]

@dataclass
class Sigmoid(Operation):
    """
    A node that applies the sigmoid function to its input.
    Note that this should only have one child.
    """
    def op(self, x):
        return 1 / (1 + math.exp(-x[0]))

    def grad(self):
        return [self.forward() * (1 - self.forward())]
```

这里的操作超类目前并不重要，但稍后我们会需要它来更容易地找到模型的输入。

请注意，函数的梯度通常需要它们子节点的值，因此我们需要调用子节点的 `forward()` 方法。稍后我们会详细讨论这一点。

在我们的框架中定义一个神经网络有点冗长，但与构建树非常相似。例如，这里是我们框架中一个简单线性分类器的代码：

```py
linear_classifier = Add([
    Multiply([
        Parameter(),
        Input()
    ]),
    Parameter()
])
```

## 使用我们的模型

要用我们的模型进行预测，我们首先需要填充树中的输入，然后调用父节点的 `forward()`。但为了填充输入，我们首先需要找到它们，因此我们向 **Operation** 类中添加以下方法（我们不会将其添加到 NeuralNetNode 类中，因为该类中尚未定义 Input 类型）：

```py
def find_input_nodes(self) -> List[Input]:
    """Find all of the input nodes in the subtree rooted at this node"""
    input_nodes = []
    for child in self.children:
        if isinstance(child, Input):
            input_nodes.append(child)
        elif isinstance(child, Operation):
            input_nodes.extend(child.find_input_nodes())
    return input_nodes
```

现在我们可以将 `predict()` 方法添加到 `Operation` 类中：

```py
def predict(self, inputs: List[float]) -> float:
    """Evaluate the network on the given inputs"""
    input_nodes = self.find_input_nodes()
    assert len(input_nodes) == len(inputs)
    for input_node, value in zip(input_nodes, inputs):
        input_node.value = value
    return self.forward()
```

**练习 2：** 我们当前实现的 `predict()` 方法效率有些低，因为每次运行 `predict()` 时，我们都需要遍历树来找到所有输入。编写一个 `compile()` 方法，在执行时缓存操作的输入。

训练我们的模型现在非常直接：

```py
from typing import Callable, Tuple

def train_model(
    model: Operation, 
    loss_fn: Callable[[float, float], float], 
    loss_grad_fn: Callable[[float, float], float],
    data: List[Tuple[List[float], float]], 
    epochs: int=1000,
    print_every: int=100
):
    """Train the given model on the given data"""
    for epoch in range(epochs):
        total_loss = 0.0
        for x, y in data:
            prediction = model.predict(x)
            total_loss += loss_fn(y, prediction)
            model.backward(loss_grad_fn(y, prediction))
        if epoch % print_every == 0:
            print(f'Epoch {epoch}: loss={total_loss/len(data)}')
```

例如，下面是我们如何使用框架训练一个线性华氏度到摄氏度的分类器：

```py
def mse_loss(y_true: float, y_pred: float) -> float:
    return (y_true - y_pred) ** 2

def mse_loss_grad(y_true: float, y_pred: float) -> float:
    return -2 * (y_true - y_pred)

def fahrenheit_to_celsius(x: float) -> float:
    return (x - 32) * 5 / 9

def generate_f_to_c_data() -> List[List[float]]:
    data = []
    for _ in range(1000):
        f = random.uniform(-1, 1)
        data.append([[f], fahrenheit_to_celsius(f)])
    return data

linear_classifier = Add([
    Multiply([
        Parameter(),
        Input()
    ]),
    Parameter()
])

train_model(linear_classifier, mse_loss, mse_loss_grad, generate_f_to_c_data())
```

运行此操作后，我们得到

```py
print(linear_classifier)
print(linear_classifier.predict([32]))

>> Add(children=[Multiply(children=[Parameter(0.5555555555555556), Input(0.8930639016107234)]), Parameter(-17.777777777777782)])
>> -1.7763568394002505e-14
```

这正确地对应于一个线性分类器，权重为0.56，偏差为-17.78（这就是华氏度到摄氏度的公式）

当然，我们也可以训练更复杂的模型，例如，下面是一个用于预测一个点(x, y)是否位于y = x线的上方或下方的模型：

```py
def bce_loss(y_true: float, y_pred: float, eps: float=0.00000001) -> float:
    y_pred = min(max(y_pred, eps), 1 - eps)
    return -y_true * math.log(y_pred) - (1 - y_true) * math.log(1 - y_pred)

def bce_loss_grad(y_true: float, y_pred: float, eps: float=0.00000001) -> float:
    y_pred = min(max(y_pred, eps), 1 - eps)
    return (y_pred - y_true) / (y_pred * (1 - y_pred))

def generate_binary_data():
    data = []
    for _ in range(1000):
        x = random.uniform(-1, 1)
        y = random.uniform(-1, 1)
        data.append([(x, y), 1 if y > x else 0])
    return data

model_binary = Sigmoid(
    [
        Add(
            [
                Multiply(
                    [
                        Parameter(),
                        ReLU(
                            [
                                Add(
                                    [
                                        Multiply(
                                            [
                                                Parameter(),
                                                Input()
                                            ]
                                        ),
                                        Multiply(
                                            [
                                                Parameter(),
                                                Input()
                                            ]
                                        ),
                                        Parameter()
                                    ]
                                )
                            ]
                        )
                    ]
                ),
                Parameter()
            ]
        )
    ]
)

train_model(model_binary, bce_loss, bce_loss_grad, generate_binary_data())
```

然后我们合理地得到

```py
print(model_binary.predict([1, 0]))
print(model_binary.predict([0, 1]))
print(model_binary.predict([0, 1000]))
print(model_binary.predict([-5, 3]))
print(model_binary.predict([0, 0]))

>> 3.7310797619230176e-66
>> 0.9997781079343139
>> 0.9997781079343139
>> 0.9997781079343139
>> 0.23791579184662365
```

尽管这有合理的运行时间，但它的速度比我们预期的稍慢。这是因为我们必须调用`forward()`并且在调用`backwards()`时重新计算模型输入*很多次*。因此，以下是这个练习：

**练习 3：** 给我们的网络添加缓存。也就是说，在调用`forward()`时，如果输入自上次调用以来没有变化，模型应该返回上次调用`forward()`时缓存的值*仅当输入未改变时*。确保在输入发生变化时再次调用`forward()`。

就是这样！我们现在有一个工作的神经网络框架，在这个框架中，我们可以训练很多有趣的模型（尽管不能训练节点输入多个其他节点的网络。这个并不难添加——请参见链式法则讨论中的注释），但确实有点冗长。如果你想改进它，可以尝试以下一些方法：

**练习 4：** 当你深入思考时，我们网络中的“复杂”节点（例如，线性层）实际上只是“宏”——也就是说，如果我们有一个神经网络树，形状如下：

![](../Images/29319be00ff7ceadc6b3612ab061410d.png)

一个线性分类模型

你真正做的事情是：

![](../Images/16b4e8fbc11e64298ec81c1ca432a2b9.png)

我们线性网络的等效公式

换句话说，*Linear(inp)*实际上只是一个包含*|inp| + 1*个参数的树的宏，其中第一个参数是乘法中的权重，最后一个参数是偏置。因此，每当我们看到*Linear(inp)*时，我们可以将其替换为仅由基本操作组成的等效树。

对于这个练习，你的任务是实现**Macro**类。该类应该是一个**Operation**，它递归地将自身替换为基本操作。

*注意：这个步骤可以随时进行，尽管通常最容易的方法是向你必须在训练之前调用的Operation类中添加一个`compile()`方法（或者将其添加到你在练习2中已有的方法中）。当然，我们也可以以其他（或许更高效）的方式实现更复杂的节点，但这仍然是一个很好的练习。*

**练习 5：** 尽管我们实际上不需要内部节点输出除一个数字以外的任何其他内容，但有时对于树的根节点（即输出层）输出其他内容（例如，在Softmax的情况下是一个数字列表）是很有用的。实现**Output**类，并允许它输出一个`Listof[float]`而不仅仅是一个`float`。作为奖励，尝试实现SoftMax输出。

*注意：有几种方法可以实现这一点。你可以让 `Output` 类继承 `Operation`，然后修改 `NeuralNetNode` 类的 `op()` 方法，使其返回一个 `List[float]`，而不仅仅是一个浮点数。或者，你可以创建一个新的 `Node` 超类，既让 `Output` 又让 `Operation` 继承。这样可能更容易。*

*进一步注意，尽管这些输出可以生成列表，它们仍然只会从损失函数中返回一个导数——损失函数只会接受一个浮点数列表，而不是单一浮点数（例如，分类交叉熵损失）。*

**练习 6：** 还记得在文章前面我们提到过神经网络只是由基本操作组成的数学函数吗？在 `NeuralNetNode` 类中添加一个 *funcify()* 方法，将其转换为以人类可读的符号表示的函数（可以根据需要添加括号）。例如，神经网络 *Add([Parameter(0.1), Parameter(0.2)])* 应该简化为 “0.1 + 0.2”（或 “(0.1 + 0.2)”）。

*注意：为了使其正常工作，输入应该命名。如果你做了练习 2，请在 `compile()` 函数中为输入命名。如果没有，你需要想办法为输入命名——编写一个 `compile()` 函数仍然是最简单的方式。*

现在就这些！如果你想查看代码，可以查看 [这个 Google Colab](https://colab.research.google.com/drive/1H0qOvXuq0GMxGoTNJmDba0W46bfZRowP?usp=sharing)，其中包含了所有内容（除了每个练习的解答，但 #6 练习的解答可能会在第二部分添加）。

如有任何问题，请通过 [mchak@calpoly.edu](mailto:mchak@calpoly.edu) 联系我。

*除非另有说明，所有图片均由作者提供。*
