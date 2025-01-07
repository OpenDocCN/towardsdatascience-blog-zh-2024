# **离散优化中的构造性启发式**

> 原文：[https://towardsdatascience.com/constructive-heuristics-in-discrete-optimization-f6a41bf26d01?source=collection_archive---------5-----------------------#2024-05-30](https://towardsdatascience.com/constructive-heuristics-in-discrete-optimization-f6a41bf26d01?source=collection_archive---------5-----------------------#2024-05-30)

## 使用Python示例为组合优化问题获得初始解

[](https://medium.com/@bruscalia12?source=post_page---byline--f6a41bf26d01--------------------------------)[![Bruno Scalia C. F. Leite](../Images/1042cd04be047c0811fef79ecd04e69c.png)](https://medium.com/@bruscalia12?source=post_page---byline--f6a41bf26d01--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f6a41bf26d01--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f6a41bf26d01--------------------------------) [Bruno Scalia C. F. Leite](https://medium.com/@bruscalia12?source=post_page---byline--f6a41bf26d01--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6a41bf26d01--------------------------------) ·阅读时间11分钟·2024年5月30日

--

![](../Images/a97a50e5ff6012c41ccf3d6b3143c9d5.png)

表示构造性启发式从基集选择元素的过程。（图像来源：作者）

离散优化或组合优化是运筹学（OR）和计算机科学中的一个重要研究领域，致力于从有限的可能解集中识别最佳（或合适的）解。其应用包括车辆路径规划、操作调度和网络设计等。通常，这类问题无法通过精确方法在可接受的运行时间内解决。因此，启发式方法提供了一个有趣的替代方案，能够在合理的计算时间内提供快速且质量较好的解决方案，指导操作。

**构造性启发式**不仅可以作为独立的技术使用，还可以与其他算法结合，以提高它们的运行时间、成本函数或其他性能方面。例如，为混合整数规划（MIP）求解器提供初始解，可以建立一个双重界限，帮助剪枝搜索空间。此外，这个初始解还可以使求解器更有效地结合局部搜索启发式方法，从而可能加速收敛并提升整体解的质量。

在这篇文章中，你将找到离散优化的基本定义，并介绍了**构造性启发式**方法。文章将通过Python示例来阐释相关主题，并应用于*背包问题*和*最大独立集*问题。我们将分析随机选择和贪婪选择元素的过程，并通过这些方法来创建解决方案。

除了几个其他优化示例外，针对这些问题的完整代码可以在我的[GitHub 仓库](https://github.com/bruscalia/optimization-demo-files)中找到。

# 离散优化

广义上，数值优化问题旨在找到目标函数*f*的最佳值，目标函数*f*是决策变量***x***的函数，且可能受到某些等式和不等式约束的限制，这些约束也是***x***的函数。目标可以定义为*最小化*或*最大化*。

离散优化是指一类优化问题，其中决策变量只能取离散值。因此，面临的是一个有限的（尽管可能很大）可行解集合*S*，必须从中选择一个可行解，使目标函数达到最佳。

## 解构建

> 许多组合优化问题的算法通过从头构建解决方案，逐步增加基础集合中的元素，在每一步中将一个基础集合元素添加到正在构建的部分解中。每一步添加的基础集合元素不能与之前添加的一个或多个元素结合，导致不可行性*(Resende & Ribeiro, 2016)*。

假设我们有一个元素的基础集合*E*，这些元素可能用于构建一个解* S*。假设*F*是*E*的一个子集，将*F*中的元素加入到部分解* S*中不会导致不可行，并且会改善整体结果。一个构造性启发式算法的伪代码可以描述如下。

```py
function constructive(E){
    S = {}
    F = {i for i in E if S union {i} is feasible}
    while F is not empty{
        i = choose(F)
        S = S union {i}
        F = {i for i in E if S union {i} is feasible}
    }
    return S
}
```

下一步选择添加到解决方案中的元素可能会根据问题和采用的策略有所不同。在某些情况下，选择一个能够在部分解中带来最佳即时效果的元素可能是一个有趣的替代方案，而在其他情况下，随机效果可能是可取的。我们将在本文的剩余部分中对两种方法在两个不同问题中的表现进行比较。

在一些问题中，即使采用贪心的增量方法，也有精确的构造算法可以在多项式时间内完成，正如本文将展示的那样。一个有趣的例子是最小生成树（MST）问题。然而，对于本文将介绍的问题，情况并非如此。

# 背包问题

在背包问题中，给定一组具有重量和价值属性的物品，必须选择最有价值的物品放入一个预定义容量的背包中，且所选物品的总重量不能超过背包容量。在这个问题中，我们可以将可用物品视为我们的*基础集合*。

那么，让我们创建一个 Python 类来表示我们每个可用物品。

```py
class Item:

    index: int
    weight: float
    value: float
    density: float
    selected: bool

    def __init__(self, index, weight, value) -> None:
        self.index = index
        self.weight = weight
        self.value = value
        self.density = value / weight
        self.selected = False

    @classmethod
    def from_dict(cls, x: dict):
        index = x["index"]
        weight = x["weight"]
        value = x["value"]
        return cls(index, weight, value)
```

我们还创建了属性`density`，表示给定物品的“每单位重量的价值”比率，`index`表示它的标识符，`selected`表示该物品是否为我们最终解的一部分。*classmethod* `from_dict`用于从一个包含*index*、*weight*和*value*键的Python `dict`初始化一个新物品。

现在，让我们考虑背包问题的一种构造性启发式抽象。它的初始化参数是背包容量和一个物品列表（以字典形式提供）。这两个参数应作为我们类的属性，以便在解决过程中使用。

```py
from typing import Dict, List, Union

class BaseConstructive:

    items: List[Item]
    capacity: float
    solution: List[Item]

    def __init__(self, capacity: float, items: List[Dict[str, Union[int, float]]]) -> None:
        self.items = []
        self.capacity = capacity
        for new_element in items:
            item = Item.from_dict(new_element)
            self.items.append(item)
        self.solution = []

    @property
    def cost(self):
        return sum(i.value for i in self.solution)
```

一个简单的解决过程可以遍历我们的物品集合，并在下一个物品的重量小于或等于剩余容量时将其包括在解中。

```py
class BaseConstructive:
    # Check previous definition

    def solve(self):
        remaining = self.capacity
        for item in self.items:
            if remaining >= item.weight:
                item.selected = True
                self.solution.append(item)
                remaining = remaining - item.weight
```

然而，这种方法可能会导致较差的解。假设在列表的开头有一个重量较大的物品，但其价值较小。它将被纳入解中，占据了本可以用于更有价值物品的可用空间。

更好的选择可能是先按*密度*对物品进行排序，然后执行之前的步骤，依次从输入中选择下一个物品，前提是它能够适应剩余空间。这将引导我们进入*贪心*选择方法。

## 贪心选择

> 贪心近似算法是一种迭代算法，通过逐步构建部分解来产生结果。每次迭代都会对当前部分解进行局部最优或次优的增量，从而在算法结束时得到一个全局次优解*(Wan, 2013)*。

在背包问题的背景下，我们可以像之前建议的那样，基于*密度*优先选择下一个元素。在这种情况下，贪心方法并不能保证解的最优性，但它可以成为快速且高质量结果的有趣替代方案。在我们的Python代码中，我们可以通过在应用解法之前对物品进行排序来实现这一点。

```py
class GreedyConstructive(BaseConstructive):

    def solve(self):
        self.items.sort(key=lambda x: x.density, reverse=True)
        super().solve()
```

在我的[GitHub 仓库](https://github.com/bruscalia/optimization-demo-files/tree/main/mip/knapsack/heuristics)中，你可能会找到一个包含10个物品的实例，我对这些物品应用了两种方法。基于原始输入序列的选择产生的解总价值为68，而基于密度的选择则得到了总价值91。我会选择贪心方法，因为它能够提供高质量且快速的解。

# 最大独立集问题

下一个例子是一个经典的子集划分问题，其中我们的目标是从无向图*G*(*V*, *E*)中找到一个元素子集，使得子集中的元素数最多且没有任何一对元素之间存在边连接。

让我们从创建类来处理此问题的图元素开始。`Node`类将用于表示我们无向图中的一个顶点（或节点）。它将具有以下属性：

+   `neighbors`：一个邻接顶点的列表

+   `index`：它的标识符

+   `selected`：一个布尔值，用于表示何时将其包含在解集中。

每当从我们的可行元素子集删除一个`Node`实例时，我们必须将其从邻居的邻居列表中移除，因此我们创建一个`delete`方法以简化这一过程。

属性`degree`计算给定节点的邻居数量，并将作为我们选择下一个元素的*贪心*方法的标准。

```py
import copy
from typing import Dict, List, Optional, Tuple

class Node:

    neighbors: List['Node']
    index: int
    selected: bool

    def __init__(self, index):
        self.index = index
        self.neighbors = []
        self.selected = False

    def __repr__(self) -> str:
        return f"N{self.index}"

    def add_neighbor(self, node: 'Node'):
        if node not in self.neighbors:
            self.neighbors.append(node)

    def delete(self):
        for n in self.neighbors:
            n.neighbors.remove(self)

    @property
    def degree(self):
        return len(self.neighbors)
```

现在，让我们创建我们的`Graph`类。它应该通过一个边的列表和一个可选的节点列表来实例化。它应该有一个属性`N`，该属性是一个包含现有节点（或顶点）的字典。

属性`queue`应返回一个节点列表，这些节点尚未被选中，以便我们在每一步的构造性启发式方法中考虑将其纳入解集中。

每当选择一个新的`Node`实例时，应该调用`select`方法，它会更改其`selected`属性并调用其`delete`方法。

```py
class Graph:

    N: Dict[int, Node]

    def __init__(
        self,
        edges: List[Tuple[int, int]],
        nodes: Optional[List[int]] = None
    ):
        # Start the set
        if nodes is None:
            self.N = {}
        else:
            self.N = {i: Node(i) for i in nodes}

        # Include all neighbors
        for i, j in edges:
            self._new_edge(i, j)

    @property
    def active_nodes(self):
        return [node for node in self.N.values() if node.selected]

    @property
    def inactive_nodes(self):
        return [node for node in self.N.values() if not node.selected]

    @property
    def nodelist(self):
        return list(self.N.values())

    @property
    def queue(self):
        return [n for n in self.nodelist if not n.selected]

    def _new_node(self, i: int):
        if i not in self.N:
            self.N[i] = Node(i)

    def _new_edge(self, i: int, j: int):
        self._new_node(i)
        self._new_node(j)
        self.N[i].add_neighbor(self.N[j])
        self.N[j].add_neighbor(self.N[i])

    def select(self, node: Node):
        node.selected = True
        selected_neighbors = node.neighbors.copy()
        for n in selected_neighbors:
            other = self.N.pop(n.index)
            other.delete()

    def deactivate(self):
        for n in self.N.values():
            n.selected = False

    def copy(self):
        return copy.deepcopy(self)
```

现在，让我们为我们的构造性启发式方法创建一个抽象。它应该像对应的`Graph`一样，通过一个边的列表和一个可选的节点列表来实例化。在实例化时，它的属性`graph`将从问题实例的原始图定义。

```py
from abc import ABC, abstractmethod
from mis.graph import Graph, Node
from typing import List, Optional, Tuple

class BaseConstructive(ABC):

    graph: Graph

    def __init__(
        self,
        edges: List[Tuple[int, int]],
        nodes: Optional[List[int]] = None,
    ):
        self.graph = Graph(edges, nodes)
```

`solve`方法将是我们求解过程的核心。它应该返回一个候选解的子图*G*(*V*, *E*)。当使用解程序的实例作为可调用对象时，它应该根据`solve`方法返回的结果覆盖其节点的`selected`属性。

请注意，`choice`方法在这里是一个抽象，尚未被子类覆盖。

```py
class BaseConstructive(ABC):
    # Check previous definitions

    def __call__(self, *args, **kwargs):
        S = self.solve(*args, **kwargs)
        for i, n in S.N.items():
            self.graph.N[i].selected = n.selected

    @property
    def cost(self):
        return len(self.graph.active_nodes)

    def solve(self, *args, **kwargs) -> Graph:
        self.graph.deactivate()
        G = self.graph.copy()
        for i in range(len(G.N)):
            n = self.choice(G)
            G.select(n)
            if len(G.queue) == 0:
                assert len(G.N) == i + 1, "Unexpected behavior in remaining nodes and iterations"
                break

        return G

    @abstractmethod
    def choice(self, graph: Graph) -> Node:
        pass
```

让我们首先创建一个随机选择下一个要加入解集的节点的算法。

```py
import random

class RandomChoice(BaseConstructive):

    rng: random.Random

    def __init__(
        self,
        edges: List[Tuple[int, int]],
        nodes: Optional[List[int]] = None,
        seed=None
    ):
        super().__init__(edges, nodes)
        self.rng = random.Random(seed)

    def choice(self, graph: Graph) -> Node:
        return self.rng.choice(graph.queue)
```

它已经可以在我们的求解过程中使用，并且生成*最大独立集*的可行解（不是*最大*的）。然而，它的性能会根据随机序列的不同而变化，我们可能会受到较差结果的影响。

## 自适应贪心

或者，在每一步中，我们可以选择对“可行元素池”影响最小的下一个节点。这意味着选择子图中邻居数量最少的下一个元素。换句话说，选择具有最小`degree`属性的下一个元素。这是Feo等人（1994）采用的相同方法。

请注意，我们节点的`degree`可能会随着部分解的变化和元素从子图中移除而变化。因此，它可以定义为一个*自适应贪心*过程。

> 还有一些情况，元素贡献的成本会受到算法先前选择的元素的影响。我们将这些称为自适应贪心算法*(Resende & Ribeiro, 2016)*。

然后让我们实现一个算法，选择子图中`degree`最小的元素作为下一个元素。

```py
class GreedyChoice(BaseConstructive):

    def choice(self, graph: Graph) -> Node:
        return min([n for n in graph.queue], key=lambda x: x.degree)
```

尽管它并未提供最优性的证明，但自适应贪婪方法仍然是为此问题提供快速且高质量解的一个有趣策略。试着多次运行随机方法…在某些情况下，它可能会优于贪婪策略（至少在一次或几次运行中）。那为什么不实现一个多启动框架呢？

## 多启动

在这种方法中，执行多个独立的运行，并记录最佳解。最终返回最好的解。

```py
class MultiRandom(RandomChoice):

    def solve(self, n_iter: int = 10) -> Graph:
        best_sol = None
        best_cost = 0
        for _ in range(n_iter):
            G = super().solve()
            if len(G.N) > best_cost:
                best_cost = len(G.N)
                best_sol = G
        return best_sol
```

在我的[GitHub 仓库](https://github.com/bruscalia/optimization-demo-files/blob/5546625793917492f4abe1aca0a571527e256e32/graph-coloring/max_independent_set.ipynb)中，您将找到一个32节点图的例子，其中*自适应贪婪*方法找到了一个5个顶点的子集，但使用多启动的随机框架找到了一个包含6个顶点的解。解决过程如下所示。

![](../Images/abb86a60627ac000096137ee77138ed8.png)

解决构造性启发式应用于最大独立集问题的过程。（由作者制作的动画）。

# 进一步阅读

在本文开头，我提到过构造性启发式可以与局部搜索技术结合。一种探索这一方法的极具潜力的元启发式方法叫做*贪婪随机自适应搜索过程*（GRASP）。

GRASP的思路是使用一个多启动框架，其中随机元素将引导构造阶段生成不同的初始解，接着对其应用局部搜索。这样，解决过程能够避免局部最优解。对于那些有兴趣更详细探讨启发式和元启发式方法的人，值得访问[毛里西奥·雷森德教授的官网](https://mauricio.resende.info/)，他是最初提出GRASP的作者之一。在该网站上，他列出了自己的一些工作和对运筹学学术界的贡献。

对于有兴趣了解GRASP代码示例的人，也可以查看我的[GitHub 仓库](https://github.com/bruscalia/jobshop)，其中包含用于作业车间调度问题的应用。

对于那些有兴趣深入探讨更多优化问题和解决技术的人，我在Medium上有一些其他故事，可以在一个综合列表中找到。

![Bruno Scalia C. F. Leite](../Images/0c7396e41d4b598be2349eaea982c984.png)

[Bruno Scalia C. F. Leite](https://medium.com/@bruscalia12?source=post_page-----f6a41bf26d01--------------------------------)

## 优化时代的故事

[查看列表](https://medium.com/@bruscalia12/list/tales-of-the-optimization-age-c15faf64a6ca?source=post_page-----f6a41bf26d01--------------------------------)15个故事![](../Images/848ca03a7d7366b8a040f720f5d51f5c.png)![](../Images/b79fd62ce301f6295199d983f7633588.png)![](../Images/a6e8cbe0e088f4e7b1edcf27c524b072.png)

# 结论

在本文中，介绍并应用了离散优化背景下的构造性启发式方法，具体应用于*背包问题*和*最大独立集问题*。阐述了如何选择基本元素以构建解决方案的直觉，举例说明了贪心选择、随机因素和多重起始的使用。完整的代码可以在我的[GitHub 仓库](https://github.com/bruscalia/optimization-demo-files)中找到。

# 参考文献

Feo, T. A., Resende, M. G., & Smith, S. H., 1994\. 一种用于最大独立集的贪心随机自适应搜索过程。*运筹学*，*42*(5)，860–878。

Resende, M. G., & Ribeiro, C. C., 2016\. *通过 GRASP 进行优化*。Springer Science+ Business Media 纽约。

Wan, PJ., 2013\. 贪心近似算法。载：Pardalos, P., Du, DZ., Graham, R.（编辑）《组合优化手册》。Springer, 纽约, NY。
