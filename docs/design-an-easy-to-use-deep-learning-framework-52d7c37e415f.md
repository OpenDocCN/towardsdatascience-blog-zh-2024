# 设计一个易于使用的深度学习框架

> 原文：[`towardsdatascience.com/design-an-easy-to-use-deep-learning-framework-52d7c37e415f?source=collection_archive---------6-----------------------#2024-04-10`](https://towardsdatascience.com/design-an-easy-to-use-deep-learning-framework-52d7c37e415f?source=collection_archive---------6-----------------------#2024-04-10)

## 作为开源贡献者，我学到的三条软件设计原则

[](https://haifeng-jin.medium.com/?source=post_page---byline--52d7c37e415f--------------------------------)![Haifeng Jin](https://haifeng-jin.medium.com/?source=post_page---byline--52d7c37e415f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--52d7c37e415f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--52d7c37e415f--------------------------------) [Haifeng Jin](https://haifeng-jin.medium.com/?source=post_page---byline--52d7c37e415f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--52d7c37e415f--------------------------------) ·9 分钟阅读·2024 年 4 月 10 日

--

![](img/f0f2539047f9ad89bd753971d4f517d5.png)

图片来自[Sheldon](https://unsplash.com/@hfestudio?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

深度学习框架的变迁非常迅速。如果你将今天人们使用的深度学习框架与八年前的框架进行比较，你会发现整个生态环境已经完全不同。曾经有 Theano、Caffe2 和 MXNet，它们都已经过时。今天最流行的框架，如 TensorFlow 和 PyTorch，刚刚向公众发布。

经过这些年的发展，Keras 作为一个支持不同后端（包括 TensorFlow、PyTorch 和 JAX）的高级用户库得以存活。作为 Keras 的贡献者，我学到了团队是多么重视软件的用户体验，并且他们如何通过遵循一些简单而强大的设计原则，确保了良好的用户体验。

在本文中，我将分享过去几年我通过为 Keras 做贡献而学到的三条最重要的软件设计原则，这些原则可能适用于所有类型的软件，并帮助你在开源社区中通过自己的项目产生影响。

## 为什么用户体验对开源软件很重要

在深入讨论主要内容之前，让我们快速讨论一下为什么用户体验如此重要。我们可以通过 PyTorch 与 TensorFlow 的案例来了解这一点。

这些框架由两家科技巨头 Meta 和 Google 开发，它们具有截然不同的文化优势。Meta 擅长产品，而 Google 擅长工程。因此，Google 的框架如 TensorFlow 和 JAX 运行速度最快，技术上也优于 PyTorch，因为它们在稀疏张量和分布式训练方面做得很好。然而，PyTorch 仍然从 TensorFlow 手中夺走了半壁江山，因为它优先考虑用户体验，而不是软件的其他方面。

更好的用户体验对于构建模型并将其传播给工程师的研究人员来说至关重要，因为工程师们不总是愿意将他们从研究人员那里获得的模型转换为另一个框架。他们会围绕 PyTorch 构建新的软件，以简化他们的工作流程，这样就会在 PyTorch 周围建立一个软件生态系统。

TensorFlow 也犯了一些错误，导致其用户流失。TensorFlow 的整体用户体验良好。然而，其 GPU 支持的安装指南多年来一直存在问题，直到 2022 年才得以修复。TensorFlow 2 破坏了向后兼容性，导致用户在迁移过程中损失了数百万美元。

所以，我们从这里学到的教训是，尽管技术上具有优势，用户体验决定了开源用户会选择哪款软件。

## 所有深度学习框架都在用户体验上投入了大量资金

所有深度学习框架——TensorFlow、PyTorch 和 JAX——都在用户体验上投入了大量资金。一个很好的证据是，它们的代码库中 Python 的比例相对较高。

深度学习框架的所有核心逻辑，包括张量操作、自动微分、编译和分布式处理，都是用 C++实现的。为什么他们要向用户暴露一套 Python API？这仅仅是因为用户喜欢 Python，并且他们想要打磨用户体验。

## 投资用户体验的回报率很高

想象一下，要让你的深度学习框架比其他框架稍微快一些需要多少工程努力。很多。

然而，为了更好的用户体验，只要你遵循一定的设计过程和一些原则，就能够实现这一目标。为了吸引更多用户，用户体验和框架的计算效率同样重要。因此，投资用户体验的回报率很高。

## 这三条原则

我将分享我在为 Keras 贡献代码过程中学到的三条重要软件设计原则，并附上来自不同框架的好坏代码示例。

## 原则 1：设计端到端工作流

当我们考虑设计一款软件的 API 时，可能会像这样。

```py
class Model:
    def __call__(self, input):
        """The forward call of the model.

        Args:
          input: A tensor. The input to the model.
        """
        pass
```

定义类并添加文档。现在，我们知道了所有的类名、方法名和参数。然而，这并不能帮助我们更好地理解用户体验。

我们应该做的是类似这样的事情。

```py
input = keras.Input(shape=(10,))
x = layers.Dense(32, activation='relu')(input)
output = layers.Dense(10, activation='softmax')(x)
model = keras.models.Model(inputs=input, outputs=output)
model.compile(
    optimizer='adam', loss='categorical_crossentropy'
)
```

我们希望写出整个用户使用软件的工作流。理想情况下，它应该是一个关于如何使用软件的教程。它提供了关于用户体验的更多信息。与仅仅写出类和方法相比，它可以帮助我们在设计阶段发现更多的用户体验问题。

让我们看另一个例子。这就是我在实现 KerasTuner 时，通过遵循这一原则发现用户体验问题的方式。

当使用 KerasTuner 时，用户可以使用这个 `RandomSearch` 类来选择最佳模型。我们有度量和目标作为参数。默认情况下，目标等于验证损失。因此，它帮助我们找到具有最小验证损失的模型。

```py
class RandomSearch:
    def __init__(self, ..., metrics, objective="val_loss", ...):
        """The initializer.

        Args:
            metrics: A list of Keras metrics.
            objective: String or a custom metric function. The
                name of the metirc we want to minimize.
        """
        pass
```

再次强调，它并没有提供太多关于用户体验的信息。所以，现在一切看起来都没问题。

然而，如果我们编写一个端到端的工作流，如下所示，它暴露了更多问题。用户试图定义一个名为 `custom_metric` 的自定义度量函数。目标变得不再那么直观。我们现在应该传递什么给目标参数呢？

```py
tuner = RandomSearch(
    ...,
    metrics=[custom_metric],
    objective="val_???",
)
```

它应该只是`"val_custom_metric"`。只需使用前缀`"val_"`和度量函数的名称。这样并不够直观。我们希望改进它，而不是强迫用户学习这个。通过编写这个工作流，我们很容易发现了一个用户体验问题。

如果你通过包含 `custom_metric` 函数的实现来更加全面地编写设计，你会发现你甚至需要学习如何编写 Keras 自定义度量。你必须遵循函数签名才能使其工作，如下面的代码片段所示。

```py
def custom_metric(y_true, y_pred):
    squared_diff = ops.square(y_true - y_pred)
    return ops.mean(squared_diff, axis=-1)
```

发现这个问题后，我们特别设计了一个更好的自定义度量工作流。你只需要重写`HyperModel.fit()`来计算自定义度量并返回它。不需要用字符串来命名目标。也不需要遵循函数签名。只需一个返回值。现在的用户体验要好得多。

```py
class MyHyperModel(HyperModel):
    def fit(self, trial, model, validation_data):
        x_val, y_true = validation_data
        y_pred = model(x_val)
        return custom_metric(y_true, y_pred)

tuner = RandomSearch(MyHyperModel(), max_trials=20)
```

还有一点需要记住的是，我们应该始终从用户体验开始。设计的工作流会反向影响实现。

## 原则 2：最小化认知负担

除非真的必要，否则不要强迫用户学习任何东西。让我们看看一些好的例子。

Keras 模型构建 API 是一个好的例子，如下面的代码片段所示。模型构建者已经掌握了这些概念，例如，模型是一个层的堆叠。它需要一个损失函数。我们可以用数据来拟合它，或者让它对数据进行预测。

```py
model = keras.Sequential([
    layers.Dense(10, activation="relu"),
    layers.Dense(num_classes, activation="softmax"),
])
model.compile(loss='categorical_crossentropy')
model.fit(...)
model.predict(...)
```

所以基本上，使用 Keras 并不需要学习新的概念。

另一个好的例子是 PyTorch 模型构建。代码就像 Python 代码一样执行。所有张量都是真正的张量，具有实际值。你可以依赖一个张量的值来决定路径，用纯 Python 代码实现。

```py
class MyModel(nn.Module):
    def forward(self, x):
        if x.sum() > 0:
            return self.path_a(x)
        return self.path_b(x)
```

你也可以使用 Keras 和 TensorFlow 或 JAX 后端来做这件事，但需要以不同的方式编写。所有的 `if` 条件需要通过这个 `ops.cond` 函数来编写，如下面的代码片段所示。

```py
class MyModel(keras.Model):
    def call(self, inputs):
        return ops.cond(
            ops.sum(inputs) > 0,
            lambda : self.path_a(inputs),
            lambda : self.path_b(inputs),
        )
```

这教会用户学习一种新的操作，而不是使用他们熟悉的 if-else 语句，这样做并不好。作为补偿，它在训练速度上带来了显著的提升。

这就是 PyTorch 灵活性的陷阱。如果你曾经需要优化模型的内存和速度，你将不得不自己使用以下 API 和新概念来实现，包括操作的 inplace 参数、并行操作 API 和显式设备分配。这为用户引入了相当高的学习曲线。

```py
torch.relu(x, inplace=True)
x = torch._foreach_add(x, y)
torch._foreach_add_(x, y)
x = x.cuda()
```

其他一些好的例子包括`keras.ops`、`tensorflow.numpy`、`jax.numpy`。它们只是 numpy API 的重新实现。当引入一定的认知负担时，只需重用人们已经熟悉的内容。每个框架都必须在这些框架中提供一些低级操作。与其让人们学习一套全新的 API（可能有上百个函数），不如直接使用最流行的现有 API。numpy 的 API 文档详尽，并且有大量相关的 Stack Overflow 问题和答案。

你在用户体验中能做的最糟糕的事情就是欺骗用户。让用户相信你的 API 是他们熟悉的，但实际上不是。我将给出两个例子，一个是 PyTorch 的，另一个是 TensorFlow 的。

如果你想将形状为`(100, 3, 32, 32)`的输入张量填充到`(100, 3, 1+32+1, 2+32+2)`或`(100, 3, 34, 36)`，那么在`F.pad()`函数中应该传递什么作为 pad 参数？

```py
import torch.nn.functional as F
# pad the 32x32 images to (1+32+1)x(2+32+2)
# (100, 3, 32, 32) to (100, 3, 34, 36)
out = F.pad(
    torch.empty(100, 3, 32, 32),
    pad=???,
)
```

我的第一直觉是它应该是`((0, 0), (0, 0), (1, 1), (2, 2))`，其中每个子元组对应四个维度中的一个，两个数字是现有值之前和之后的填充值。我的猜测来源于 numpy API。

然而，正确的答案是(2, 2, 1, 1)。没有子元组，只有一个普通的元组。此外，维度是反转的，最后一个维度变成了第一个。

以下是 TensorFlow 的一个坏例子。你能猜出以下代码片段的输出是什么吗？

```py
value = True

@tf.function
def get_value():
    return value

value = False
print(get_value())
```

如果没有`tf.function`装饰器，输出应该是 False，这个很简单。然而，使用了装饰器后，输出变为 True。这是因为 TensorFlow 会编译函数，任何 Python 变量都会被编译成一个新的常量。修改旧变量的值不会影响已创建的常量。

它让用户相信这是他们熟悉的 Python 代码，但实际上并不是。

## 原则 3：互动优于文档

如果用户能通过运行一些示例代码并自己调试就能搞明白问题，那么没有人愿意阅读冗长的文档。所以，我们尽量让软件的用户工作流遵循相同的逻辑。

这里有一个很好的例子，展示在下面的代码片段中。在 PyTorch 中，所有带有下划线的方法都是原地操作（inplace ops），而没有下划线的是非原地操作。从交互的角度来看，这样做很好，因为它们容易跟随，用户在需要使用方法的原地版本时无需查阅文档。然而，当然，这也带来了一些认知负担。用户需要知道什么是原地操作，以及何时使用它们。

```py
x = x.add(y)
x.add_(y)
x = x.mul(y)
x.mul_(y)
```

另一个好例子是 Keras 层。它们严格遵循相同的命名约定，正如下面的代码片段所示。通过清晰的命名约定，用户可以轻松记住层的名称，而无需查阅文档。

```py
from keras import layers

layers.MaxPooling2D()
layers.GlobalMaxPooling1D()
layers.GlobalAveragePooling3D()
```

用户与软件之间的交互中，另一个重要的部分就是错误信息。你不能指望用户第一次就能完全正确地编写代码。我们应该在代码中进行必要的检查，并尽量输出有用的错误信息。

让我们来看一下代码片段中的两个例子。第一个例子信息不多，只是简单地说了张量形状不匹配。

第二个例子则包含了更多有用的信息，帮助用户找到 bug。它不仅告诉你错误是由于张量形状不匹配，还显示了期望的形状以及收到的错误形状。如果你并不打算传递这个形状，你就能更清楚地知道问题所在。

现在的 bug。

```py
# Bad example:
raise ValueError("Tensor shape mismatch.")

# Good example:
raise ValueError(
    "Tensor shape mismatch. "
    "Expected: (batch, num_features). "
    f"Received: {x.shape}"
)
```

最好的错误信息应该是直接指引用户如何修复问题。下面的代码片段展示了一个典型的 Python 错误信息。它猜测了代码中的问题，并直接指向了修复方法。

```py
import math

math.sqr(4)
"AttributeError: module 'math' has no attribute 'sqr'. Did you mean: 'sqrt'?"
```

## 最后总结

到目前为止，我们已经介绍了我在为深度学习框架贡献时学到的三条最有价值的软件设计原则。首先，编写端到端工作流程以发现更多用户体验问题。其次，减少认知负担，除非必要，否则不要教给用户任何东西。第三，在 API 设计中遵循相同的逻辑，抛出有意义的错误信息，让用户通过与软件的交互来学习，而不是不断查阅文档。

然而，如果你想让你的软件更好，还有许多原则需要遵循。你可以参考 [Keras API 设计指南](https://github.com/keras-team/governance/blob/24401c1addf521e522fd363f6eb40e7c4c4881d5/keras_api_design_guidelines.md) 作为完整的 API 设计指南。
