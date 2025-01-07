# 数据科学最佳实践，第二部分 — 一起工作

> 原文：[`towardsdatascience.com/data-science-better-practices-part-2-work-together-9ec019f8b79e?source=collection_archive---------8-----------------------#2024-01-05`](https://towardsdatascience.com/data-science-better-practices-part-2-work-together-9ec019f8b79e?source=collection_archive---------8-----------------------#2024-01-05)

## 你不能仅仅投入更多的数据科学家来处理这个模型，并期待准确率神奇地提高。

[](https://medium.com/@scf1984?source=post_page---byline--9ec019f8b79e--------------------------------)![Shachaf Poran](https://medium.com/@scf1984?source=post_page---byline--9ec019f8b79e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ec019f8b79e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ec019f8b79e--------------------------------) [Shachaf Poran](https://medium.com/@scf1984?source=post_page---byline--9ec019f8b79e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ec019f8b79e--------------------------------) ·阅读时间 10 分钟·2024 年 1 月 5 日

--

![](img/779611b5d766e019ee32e49510a48cf2.png)

图片来源：Joseph Ruwa：[`www.pexels.com/photo/set-of-chess-pieces-in-daylight-4038397/`](https://www.pexels.com/photo/set-of-chess-pieces-in-daylight-4038397/)

(第一部分在这里)

并非所有的数据科学项目都一样。

我所见过和构建的大多数数据科学项目，最初都是作为一次性证明概念的快速实现而诞生的。临时的单次性解决方案，用来让一些边缘性的工作得以完成。

其中一些项目最终可能会变成别的东西，也许会变得更大或在帮助组织目标实现方面更为核心。

只有少数项目能够在长期内不断成长和成熟。

这些特别的项目通常是解决对组织具有特殊意义的问题。例如，在线广告网络的 CTR 预测器，或视觉效果生成器的图像分割模型，或内容过滤服务的脏话检测器*。*

这些项目也通常会投入大量公司资源进行优化，理应如此。当某些准确性指标的微小改进可以直接带来更高的收入，或成为产品发布和融资轮次的成败关键时——组织应该毫不吝啬地投入资源。

我们在这篇文章中讨论的资源是**数据科学家**。

如果你从未管理过一个项目、一个团队或公司，可能会觉得把人当作“资源”来对待有些奇怪。但请记住，这些是*有着有限时间的专家，我们利用他们的时间完成有益于组织的任务*。

现在请注意：***资源必须被管理，并且它们的使用应当得到优化***。

一旦某个模型变得足够大并且如此核心，以至于有多个数据科学家共同致力于改进它，就必须确保他们能够在不相互干扰、不阻碍对方的情况下进行工作。相反，团队成员应当能够轻松地互相帮助，并在彼此的成功基础上进行建设。

我在不同地方见过的常见做法是，每个团队成员尝试自己的“东西”。根据项目的特殊性，这可能意味着不同的模型、优化算法、深度学习架构、工程特征等。

这种工作模式在成员之间可能看起来是垂直的，因为每个人都可以独立工作，不会创造出可能阻碍或阻挡他人进展的依赖关系。

然而，情况并非完全如此，正如我曾经在[这里抱怨过](https://digma.ai/blog/coding-horrors-refactoring-and-feature-creep/)。

例如，如果某个团队成员在某个特别有价值的特征上发现了重要突破，其他成员可能希望在他们的模型中尝试使用相同的特征。

在某个时间点，一个特定的模型可能会表现出性能的飞跃，很快我们会看到基于那个最佳模型的分支版本，每个版本与下一个稍有不同。这是因为优化过程往往会在当前最优解的附近寻找更好的最优解——不仅仅是通过梯度下降，也包括通过人类的创造力。

这种情况可能会导致比预期更高的耦合性和更多的依赖关系。

即便我们确保不是所有数据科学家都朝着同一个方向收敛，我们仍然应该尝试标准化他们的工作，可能还需要强制执行与下游使用者的合同，以便简化部署并节省机器学习工程师的时间。

# 前提

我们希望数据科学家们能够以一种既允许独立工作，又能同时重用他人工作的方式来解决相同的问题。

为了举例说明，我们假设自己是一个团队的成员，正在处理[Iris 花卉数据集](https://archive.ics.uci.edu/dataset/53/iris)。这意味着训练数据足够小，可以放入内存中的 pandas 数据框中，尽管我们提出的工具可以应用于任何类型和规模的数据。

我们希望能够允许创造性的自由，这意味着每个成员可以完全自由地选择自己的建模框架——无论是`scikit-learn`、`Keras`、仅用 Python 的逻辑等。

我们的主要工具将是应用面向对象编程（OOP）原则的过程抽象，以及将个人的工作标准化为统一的语言。

# 免责声明

在这篇文章中，我将举例说明如何将数据科学过程抽象化，以便促进团队协作。重点*不是*我们所提出的具体抽象。重点是***数据科学经理和领导者应努力促进数据科学家的工作***，无论是通过抽象、协议、版本控制、流程简化或其他任何方法。

*这篇博客文章绝不是在提倡* [*重新发明轮子*](https://en.wikipedia.org/wiki/Reinventing_the_wheel)*。是否使用现成的产品、开源工具或开发内部解决方案的决定，应当与与项目相关的数据科学和机器学习工程团队一起做出。*

既然这些已经处理完了，让我们直接切入正题。

# 从结尾开始

完成后，我们希望有一个统一的框架来将我们的模型贯穿于整个流程，从训练到预测。因此，我们从定义公共流程开始：

1.  首先，我们获取**训练数据**作为输入。

1.  我们可能想提取额外的**特征**来增强数据集。

1.  我们创建一个模型并**反复训练**，直到我们对其损失或指标满意为止。

1.  然后我们将**保存**模型到磁盘或其他持久化机制。

1.  我们稍后需要**加载**模型回到内存中。

1.  然后，我们可以对新的、未见过的数据进行**预测**。

根据上述流程，我们来声明一个基本结构（即接口）供模型使用：

```py
class Model:
    def add_features(self, x):
        ...
    def train(self, x, y, train_parameters=None):
        ...
    def save(self, model_dir_path):
        ...
    @classmethod
    def load(cls, model_dir_path):
        ...
    def predict(self, x):
        ...
```

请注意，这与我们在现有框架中使用的接口没有太大区别——然而，每个框架都有其独特之处，例如命名上的差异：“fit”与“train”或它们如何在磁盘上持久化模型。将流程封装在统一的结构内，可以避免我们在其他地方添加实现细节，例如在使用不同的模型进行部署时。

现在，一旦我们定义了基本结构，让我们讨论一下我们实际如何使用它。

# 系统设计

## 特征

我们希望“特征”作为可以轻松传递并添加到不同模型中的元素。我们还应当认识到，每个模型可能会使用多个特征。

我们将尝试为我们的`Feature`类实现一种插件式的基础设施。我们会有一个所有特征的基类，然后`Model`类在接收到输入数据时，可以顺序地在内存中实例化不同的特征。

## 封装的模型

我们还希望将实际的模型封装在我们的系统中，以便在团队成员之间进行传递。但我们希望保持能够在不写大量新代码的情况下更改模型参数的选项。

我们会将它们抽象到一个不同的类中，并命名为`ModelInterface`，以避免与我们的`Model`类混淆。后者将转而将相关的方法调用委托给前者。

# 特征

我们的特征可以视为以 pandas 数据框作为输入的函数。

如果我们为每个特征提供一个唯一名称，并将其封装在与其他特征相同的接口中，我们可以非常容易地重用这些特征。

让我们定义一个基类：

```py
class Feature(ABC):
    @abstractmethod
    def add_feature(self, data):
        ...
```

让我们创建一个实现，比如花萼对角线长度：

```py
class SepalDiagonalFeature(Feature):
    def add_feature(self, data):
        data['SepalDiagonal'] = (data.SepalLength ** 2 + \
                                 data.SepalWidth ** 2) ** 0.5
```

我们将使用这个类的一个实例，因此我创建了一个单独的文件来存储所有特征：

```py
sepal_diagonal = SepalDiagonalFeature()
```

这个特定的实现已经展示了一些我们做出的决策，无论是有意识的还是无意识的：

+   输出列的名称是函数代码中的字面量，并未保存在其他地方。这意味着我们无法轻松构建已知列的列表。

+   我们选择在`add_feature`函数中将新列添加到输入数据框中，而不是返回列本身并在外部作用域中添加它。

+   我们不知道，除非通过阅读函数代码，哪些列依赖于这个特征。如果我们知道，我们可以构建一个有向无环图（DAG）来决定特征创建的顺序。

此时，这些决策是容易可逆的，然而当我们建立了几十个这样的特征后，我们可能不得不重构所有这些特征，以便对基类进行更改。也就是说，**我们应该提前决定我们期望系统的表现**，并且意识到每个选择的影响。

让我们通过实现`add_features`函数来扩展我们的`Model`基类：

```py
 def __init__(self, features: Sequence[Feature] = tuple()):
        self.features = features

    def add_features(self, x):
        for feature in self.features:
            feature.add_feature(x)
```

现在，任何人都可以在创建模型实例时使用`sepal_diagonal`特征。

如果我们没有通过抽象来方便地重用这些特征，Alice 可能会选择复制 Bob 的逻辑，并稍作修改以适应她的预处理，同时在过程中使用不同的命名，通常会**增加技术债务**。

可能出现的问题是“那常见操作，比如加法呢？我们每次想使用加法时都需要实现一个加法吗？”。

答案是否定的。为此，我们可以通过`self`参数使用实例字段：

```py
@dataclass
class AdditionFeature(Feature):
    col_a: str
    col_b: str
    output_col: str  

    def add_feature(self, data):
        data[self.output_col] = data[self.col_a] + data[self.col_b]
```

比如说，如果我们想要添加花瓣长度和花瓣宽度，我们会通过`petal_sum = AdditionFeature('petalLength', 'petalWidth', 'petalSum')`来创建一个实例。

对于每个操作符/函数，你可能需要实现一个类，乍一看这可能让人觉得很有压力，但你会很快发现，这个列表相当简短。

# 模型接口

这是我为模型接口使用的抽象：

```py
class ModelInterface(ABC):
    @abstractmethod
    def initialize(self, model_parameters: dict):
        ...

    @abstractmethod
    def train(self, x, y, train_parameters: dict):
        ...

    @abstractmethod
    def predict(self, x):
        ...

    @abstractmethod
    def save(self, model_interface_dir_path: Path):
        ...

    @classmethod
    def load(cls, model_interface_dir_path: Path):
        ...
```

这里给出了一个使用`scikit-learn`模型的示例实现：

```py
class SKLRFModelInterface(ModelInterface):
    def __init__(self):
        self.model = None
        self.binarizer = None

    def initialize(self, model_parameters: dict):
        forest = RandomForestClassifier(**model_parameters)
        self.model = MultiOutputClassifier(forest, n_jobs=2)

    def train(self, x, y, w=None):
        self.binarizer = LabelBinarizer()
        y = self.binarizer.fit_transform(y)
        return self.model.fit(x, y)

    def predict(self, x):
        return self.binarizer.inverse_transform(self.model.predict(x))

    def save(self, model_interface_dir_path: Path):
        ...

    def load(self, model_interface_dir_path: Path):
        ...
```

如你所见，代码主要是将不同的操作委托给现成的模型。在`train`和`predict`中，我们还将目标值在枚举值和独热编码向量之间来回转换，实际上是在我们的业务需求和`scikit-learn`的接口之间转换。

现在我们可以更新我们的`Model`类，以适应`ModelInterface`实例。完整代码如下：

```py
class Model:
    def __init__(self, features: Sequence[Feature] = tuple(), model_interface: ModelInterface = None,
                 model_parameters: dict = None):
        model_parameters = model_parameters or {}

        self.features = features
        self.model_interface = model_interface
        self.model_parameters = model_parameters

        model_interface.initialize(model_parameters)

    def add_features(self, x):
        for feature in self.features:
            feature.add_feature(x)

    def train(self, x, y, train_parameters=None):
        train_parameters = train_parameters or {}
        self.add_features(x)
        self.model_interface.train(x, y, train_parameters)

    def predict(self, x):
        self.add_features(x)
        return self.model_interface.predict(x)

    def save(self, model_dir_path: Path):
        ...

    @classmethod
    def load(cls, model_dir_path: Path):
        ...
```

再次强调，我创建了一个文件来管理我的模型，并在其中包含这一行：

```py
best_model_so_far = Model([sepal_diagonal], SKLRFModelInterface(), {})
```

这个`best_model_so_far`是一个可重用的实例，但请注意它并未经过训练。要获得一个可重用的训练过的模型实例，我们需要将模型持久化。

# 保存和加载

我选择在本帖中省略保存和加载的细节，因为内容已经有些冗长，但欢迎查看我的[清洁数据科学 GitHub 仓库](https://github.com/scf1984/clean-data-science/tree/main/team_cooperation)，里面有一个完整操作的 Hey 示例。

# 摘要

本文提出的框架绝对不是一种适用于所有情况的解决方案，来规范数据科学团队在单个模型上的工作，也不应被视为一种。每个项目都有其独特的细节和需求，这些都需要特别处理。

相反，本文提出的框架应该仅仅作为进一步讨论的基础，把***促进数据科学家工作的主题***放在聚光灯下。

精简工作流程应该是数据科学团队领导和经理们的一项目标，而抽象只是工具箱中的一项内容。

# 常见问题

问：如果你只需要从子类获取特定功能，难道不应该使用协议（Protocol）而不是 ABC 吗？

答：我可以这样做，但这不是一个高级的 Python 课程。有句希伯来谚语说：“书呆子无法教书。”所以，你懂的。

问：那删除特征呢？这也很重要啊！

答：当然可以。你可以选择将它们存放在哪里！你可以使用一个带参数的`Feature`实现来删除列，或者在`ModelInterface`类中完成，例如。

问：那如何衡量模型之间的表现呢？

答：有一个更高级的机制来跟踪模型的指标将非常棒。这个问题超出了本帖的范围。

问：我如何跟踪已训练的模型？

答：这可以是你保存训练模型的路径列表。确保给它们起个有意义的名字。

问：我们是不是也应该将数据集的创建抽象出来（在传递给`train`函数之前）？

答：我本来打算提到这个的，但后来我膝盖中箭了。不过，是的，拥有不同的完整数据集样本，或者是我们像处理特征和模型接口一样可以传递的多个数据集，确实是个不错的主意。

问：我们是不是在给数据科学家增加难度？

答：我们应该权衡这件事的利弊。虽然习惯于这种抽象的限制性可能需要一些时间，但从长远来看，它可能会节省大量时间。
