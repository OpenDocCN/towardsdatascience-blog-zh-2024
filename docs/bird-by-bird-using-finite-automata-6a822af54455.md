# 逐步实现：使用有限自动机

> 原文：[https://towardsdatascience.com/bird-by-bird-using-finite-automata-6a822af54455?source=collection_archive---------8-----------------------#2024-05-14](https://towardsdatascience.com/bird-by-bird-using-finite-automata-6a822af54455?source=collection_archive---------8-----------------------#2024-05-14)

## 使用 Python 对物体检测的现实 AI 系统进行有限状态机建模与仿真

[](https://slipnitskaya.medium.com/?source=post_page---byline--6a822af54455--------------------------------)[![Sofya Lipnitskaya](../Images/9ea0dd0af32232eb4c8db0cb96f66449.png)](https://slipnitskaya.medium.com/?source=post_page---byline--6a822af54455--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6a822af54455--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6a822af54455--------------------------------) [Sofya Lipnitskaya](https://slipnitskaya.medium.com/?source=post_page---byline--6a822af54455--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6a822af54455--------------------------------) ·阅读时间 17 分钟·2024年5月14日

--

![](../Images/c81c58a8ab45f4b63344efe5ae0663f5.png)

图片由作者提供

# **背景**

*“当生活给你鸡时，让 AI 处理这些鸡毛蒜皮的事。” — 无名工程师*

为什么我们需要仿真？通过采样并获得平均值，我们到底能获得什么优势？但实际上，它从来不仅仅是这些。与我们在计算机科学课程中遇到的简单任务相比，现实生活通常要复杂得多。有时我们无法找到解析解，无法找到总体参数。有时我们必须建立一个模型，以反映系统动态的具体情况，我们必须运行仿真来研究潜在过程，从而更好地理解现实世界的情况。仿真建模为各行各业和应用中的系统设计与工程提供了无价的工具。它有助于分析系统性能，识别潜在的瓶颈和低效问题，从而允许进行迭代改进和优化。

说到我们这个非常特别的挑战，在这里，我们将创建一个FSM仿真，模拟一个AI辅助的草坪监控和清洁安全系统的行为。特别地，我们将处理模拟过程，通过物体检测和喷水子系统智能管理鸟类的进出。在[上一篇文章](/bird-by-bird-using-finite-automata-9d50b36bcbd3)中，你已经了解了有限状态机（FSM）的理论和设计原则，旨在解决臭名昭著的鸡与火鸡（CaT）问题，从而创建了一个在高层次抽象下描述复杂草坪场景的模型。通过本文，我们将进一步探讨基于FSM的仿真在利用实际系统操作方面的实际应用。此外，我们还将用Python实现FSM仿真，以便后续通过优化和XAI技术对其进行改进。在本教程结束时，你将拥有一个完全功能的FSM解决方案，并对通过仿真建模解决工程问题有更深入的理解。

***免责声明：*** *本作品是“*[*鸟瞰深度学习*](/bird-by-bird-using-deep-learning-4c0fa81365d7)*”系列的一部分，专注于使用有限自动机进行计算机视觉应用的实际系统建模与仿真。所有的参与者、状态、事件和输出仅为FSM设计过程中的教育性产物。与实际人物、鸟类或真实事件的任何相似之处纯属巧合。*

# 用有限自动机进行系统建模

*“当被问及没有抽象的系统设计时，只需描述现实场景中的‘如果-那么’循环，确保在处理多个条件时有所停顿。然后，优雅地退后，留下这些琐碎的事情。” — 未知工程师。*

## 让理论走向实践

仿真，作为数学建模的一种特殊形式，涉及创建简化的现实世界系统的表示，以了解其在各种条件下的行为。其核心是通过方程捕捉真实系统的内在模式，而仿真则是通过运行程序来近似这些方程的算法过程。这个过程能够生成仿真结果，便于与理论假设进行比较，并推动实际系统的改进。仿真建模可以为系统行为提供洞察，并在进行真实实验过于昂贵和/或困难时预测结果。尤其当无法获得解析解时（例如仓库管理过程），仿真会特别有用。

在处理 CaT 问题时，目标很明确：我们希望保持草坪的整洁并节省资源。我们不依赖传统的实验方法，而是选择基于模拟的方式，寻找一种能够最小化水资源使用和水费的设置。为了实现这一目标，我们将开发一个基于有限状态机（FSM）的模型，反映系统中的关键过程，包括鸟类侵入、鸟类检测和喷水。整个模拟过程中，我们将评估系统性能，以指导进一步的优化工作，从而提高鸟类检测的效率。

## 为什么不用 if-else 语句

使用 if-else 条件分支进行系统建模是一种幼稚的解决方案，最终会导致复杂性和易出错性增加，从而使得进一步开发和维护变得更加困难。下面你将看到如何（不）描述一个简单的草坪上的鸡系统，考虑我们之前讨论过的有限状态机的示例（请参见[图 1](/bird-by-bird-using-finite-automata-9d50b36bcbd3)，该图展示了简化的 CaT 系统场景下的 FSM 状态转换图）。

```py
# import functions with input events and actions
from events import (
   simulate_chicken_intrusion,
   initiate_shooing_chicken,
)
from actions import (
  spoil_the_lawn,
  start_lawn_cleaning,
  one_more_juice
)

# define states
START = 0
CHICKEN_PRESENT = 1
NO_CHICKEN = 2
LAWN_SPOILING = 3
ENGINER_REST = 4
END = 5

# initialise simulation step and duration
sim_step = 0
max_sim_steps = 8

# initialise states
prev_state = None
current_state = START

# monitor for events
while current_state != END:
   # update state transitions
   if current_state == START:
       current_state = NO_CHICKEN
       prev_state = START
   elif current_state == NO_CHICKEN:
       if prev_state == CHICKEN_PRESENT:
           start_lawn_cleaning()
       if simulate_chicken_intrusion():
           current_state = CHICKEN_PRESENT
       else:
           current_state = ENGINER_REST
       prev_state = NO_CHICKEN
   elif current_state == CHICKEN_PRESENT:
       if initiate_shooing_chicken():
           current_state = NO_CHICKEN
       else:
           current_state = LAWN_SPOILING
       prev_state = CHICKEN_PRESENT
   elif current_state == LAWN_SPOILING:
       spoil_the_lawn()
       current_state = CHICKEN_PRESENT
       prev_state = LAWN_SPOILING
   elif current_state == ENGINER_REST:
       one_more_juice()
       current_state = NO_CHICKEN
       prev_state = ENGINER_REST

   sim_step += 1
   if sim_step >= max_sim_steps:
       current_state = END
```

在这个代码片段中，我们定义常量来表示 FSM 的每个状态（例如，CHICKEN_PRESENT）。然后，我们将当前状态初始化为 START，并在一个 while 循环中持续监控事件，模拟简化系统的行为。根据当前状态和相关事件，我们使用 if-else 条件分支语句在状态之间切换，并调用相应的操作。状态转换可能会有副作用，例如启动鸡群污染草坪的过程并开始为工程师清理草坪。这里，输入事件和动作相关的功能表示可以自动化的过程，因此我们为了简化模拟，假设引入了相关的函数。请注意，虽然鸡可以几乎无限制地破坏草坪，但过量的果汁却存在过度水化的风险。请小心这一点，并且不要忘记为你的模拟设定时间限制。在我们的案例中，时间限制是一天的结束，由 `max_sim_steps` 变量定义。看起来很丑对吧？

这种方法应该是可行的，但想象一下，如果我们想扩展逻辑，需要不断更新 if-else 语句，重复相同的分支和状态切换。正如你可以想象的那样，随着状态和事件的增加，系统状态空间的规模迅速增长。与 if-else 分支不同，有限状态机（FSM）非常适合处理复杂任务，它允许将复杂系统分解为可管理的状态和转换，从而提高代码的模块化和可扩展性。在这里，我们即将开始使用有限自动机（FSM）实现系统行为，以减少 AI 系统操作的水资源使用，同时不影响鸟类检测的准确性。

# 用 Python 实现 FSM

*“好了，孩子，我们现在要做一只鸡。” — 未知工程师。*

## 从FSM到底层

在本节中，我们深入探讨了有限状态机实现的设计选择，阐明了简化模拟过程和最大化其在现实系统优化中效用的策略。为了构建模拟，我们首先需要基于对基础过程的假设创建一个系统模型。一种方法是从封装个体状态和过渡的功能开始。然后，我们可以将它们组合起来，通过复制真实系统行为来创建一系列事件。我们还希望跟踪每次模拟运行的输出统计数据，以提供其性能的概念。我们想要做的是观察系统如何随着时间的推移在条件变化下演变（例如，基于概率的鸟类孵化和破坏草坪的随机过程）。为此，让我们首先定义和安排我们稍后将要实现的构建模块。以下是计划：

1.  定义类契约。

1.  构建目标类的层次结构，描述个体目标。

1.  实现状态之间的过渡逻辑。

1.  实现单步模拟以及完整的运行。

1.  跟踪模拟运行的输出统计数据。

## 让我们谈谈抽象

首先，我们需要为我们的模拟创建一个类层次结构，从表示状态的基类到更具领域特定的院子模拟子类。我们将使用`@abc.abstractmethod`和`@property`装饰器来分别标记抽象方法和属性。在AbstractSimulation类中，我们将定义`step()`和`run()`抽象方法，以确保子类实现它们。

```py
class AbstractSimulation(abc.ABC):
   @abc.abstractmethod
   def step(self) -> Tuple[int, List['AbstractState']]:
       pass

   @abc.abstractmethod
   def run(self) -> Iterator[Tuple[int, List['AbstractState']]]:
       pass
```

对AbstractState也有类似的应用，它定义了一个抽象方法`transit()`，由子类实现：

```py
class AbstractState(abc.ABC):
   def __init__(self, state_machine: AbstractSimulation):
       super().__init__()
       self.state_machine = state_machine

   def __eq__(self, other):
       return self.__class__ is other.__class__

   @abc.abstractmethod
   def transit(self) -> 'AbstractState':
       pass
```

对于我们的有限状态机（FSM），系统模拟的更具体方面将封装在继承自AbstractSimulation的AbstractYardSimulation类中。如其名称所示，AbstractYardSimulation更精确地概述了模拟的领域，因此我们可以在CaT问题的背景下定义一些特定于院子模拟的额外方法和属性，包括`simulate_intrusion()`、`simulate_detection()`、`simulate_sprinkling()`、`simulate_spoiling()`。

我们还将创建一个中间抽象类AbstractYardState，以确保类层次结构中的类型一致性：

```py
class AbstractYardState(AbstractState, abc.ABC):
   state_machine: AbstractYardSimulation
```

现在，让我们看一下反映名为Target及其后代的继承树。

## 鸡和火鸡的创建

Target行为是我们模拟的基石，因为它影响着所有方面，助力构建有效的模型以及后续的优化。图1展示了我们将要实现的目标类的类图。

![](../Images/84fac7433b526cba359328705f488ccb.png)

图1\. 目标类的类层次结构（图示由作者提供）

对于我们的系统，重要的是要注意目标出现的频率，它可能会对草坪造成一定的损害，而且它还具有健康属性。后者与目标的大小有关，大小可能不同，因此水枪可以瞄准较小或较大的目标（这反过来会影响水的消耗）。因此，大目标具有较多的生命值，小水流无法有效地处理它。

为了模拟不同频率的目标穿越草坪，我们还创建了相关的属性。以下是代码：

```py
class AbstractTarget(int, abc.ABC):
   @property
   @abc.abstractmethod
   def health(self) -> float:
       pass

   @property
   @abc.abstractmethod
   def damage(self) -> float:
       pass

   @property
   @abc.abstractmethod
   def frequency(self) -> float:
       pass
```

请注意，在我们的实现中，我们希望目标对象是有效的整数，这将在模拟中用于建模随机性。

接下来，我们创建子类来实现不同类型的目标。以下是类Chicken的代码，我们在其中重写了从父类继承的抽象方法：

```py
class Chicken(AbstractTarget):
   @property
   def health(self) -> float:
       return 4

   @property
   def damage(self) -> float:
       return 10

   @property
   def frequency(self) -> float:
       return 9
```

我们对剩余的火鸡和空目标类执行类似的过程。对于火鸡，生命值和损害参数分别设置为7和17（让我们看看如何用我们的AI辅助系统处理这些笨重的家伙）。空目标是一个特殊类型的目标，表示草坪上没有任何鸟类。虽然我们不能给它的生命值和损害属性赋予其他值，除了0，但草坪上无鸟的无条件（即不是由工程师引起的）状态有非零的概率，这个概率反映在频率值为9的设定中。

## 从入侵到敌人被轻松发现

现在想象一只鸟在其自然栖息地中的样子。它可以表现出各种各样的敌对行为和展示。在面对挑战时，动物可能会根据具体情况采用一系列适应性策略，包括战斗、逃跑反应以及其他中间行为。接续上一篇关于FSM设计与建模的文章，你可能还记得我们已经描述了CaT系统的关键组成部分，我们将用它来进行实际实现（参见[表2](/bird-by-bird-using-finite-automata-9d50b36bcbd3)，其中列出了描述触发状态变化的FSM输入事件）。

在FSM模拟领域，一只鸟可以被看作是一个独立的触发一系列事件的行为体：侵犯院子、破坏草坪等等。特别地，在乐观的场景下（鸟类检测和识别成功，防御行为）：鸟类在可能被基于CV的鸟类检测器识别之前侵入院子，以便继续进行喷水模块，这些配置依赖于上游预测的入侵者类别。通过这种方式，鸟类可以成功地被赶走（击中）或未能被赶走（未击中）。在这个场景下（鸟类检测成功、类别预测、防御行为），最终，鸟类逃离了草坪。任务完成。哒哒！

你可能还记得，有限状态机（FSM）可以通过状态转移图来图形化表示，这一点我们在之前的教程中已经涉及过（参见[表3](/bird-by-bird-using-finite-automata-9d50b36bcbd3)，其中展示了FSM状态转移表及下一阶段的转移逻辑）。考虑到这一点，接下来我们将创建AbstractYardState的子类，并重写`transit()`方法，根据当前状态和事件来指定状态之间的转移。

Start是初始状态，状态机从该状态过渡到Spawn。

```py
class Start(AbstractYardState):
   def transit(self) -> 'Spawn':
       return Spawn(self.state_machine)
```

从Spawn状态，系统可以过渡到以下状态之一：Intrusion、Empty或End。

```py
class Spawn(AbstractYardState):
   def transit(self) -> Union['Intrusion', 'Empty', 'End']:
       self.state_machine.stayed_steps += 1

       self.state_machine.simulate_intrusion()

       next_state: Union['Intrusion', 'Empty', 'End']
       if self.state_machine.max_steps_reached:
           next_state = End(self.state_machine)
       elif self.state_machine.bird_present:
           next_state = Intrusion(self.state_machine)
       else:
           next_state = Empty(self.state_machine)

       return next_state
```

如果我们达到模拟时间步数的上限，状态机会过渡到End状态。如果有鸟侵入或者已经在草地上，状态机会切换到Intrusion状态，否则下一状态是Empty。

Intrusion和Empty状态都跟随一个检测尝试，因此它们共享转移逻辑。因此，我们可以通过创建一个父类——IntrusionStatus来封装这一逻辑，从而减少代码重复，同时使得子类能够在类型层面区分模拟中的实际状态Intrusion和Empty。

```py
class IntrusionStatus(AbstractYardState):
   intruder_class: Target

   def transit(self) -> Union['Detected', 'NotDetected']:
       self.state_machine.simulate_detection()
       self.intruder_class = self.state_machine.intruder_class

       next_state: Union['Detected', 'NotDetected']
       if self.state_machine.predicted_bird:
           next_state = Detected(self.state_machine)
       else:
           next_state = NotDetected(self.state_machine)

       return next_state
```

我们对Detected和NotDetected类采取类似的方法，那个超类DetectionStatus负责目标预测。

```py
class DetectionStatus(AbstractYardState):
   detected_class: Target

   def transit(self) -> 'DetectionStatus':
       self.detected_class = self.state_machine.detected_class

       return self
```

然而，与Intrusion/Empty组合状态不同，NotDetected类引入了额外的转移逻辑，用以指引模拟流程，特别是关于草地污染/损坏的情况。

```py
class Detected(DetectionStatus):
   def transit(self) -> 'Sprinkling':
       super().transit()

       return Sprinkling(self.state_machine)

class NotDetected(DetectionStatus):
   def transit(self) -> Union['Attacking', 'NotAttacked']:
       super().transit()

       next_state: Union['Attacking', 'NotAttacked']
       if self.state_machine.bird_present:
           next_state = Attacking(self.state_machine)
       else:
           next_state = NotAttacked(self.state_machine)

       return next_state
```

Detected类会无条件地过渡到Sprinkling状态。对于其对立面，有两个可能的下一个状态，取决于草地上是否真的有鸟。如果鸟不在，那显然不会有鸟屎，而如果有鸟屎的可能性，则可能需要进行草地清理（或者不需要，CaT世界充满了随机性）。

回到喷洒状态，它有两个可能的结果（命中或未命中），取决于系统是否成功将鸟驱赶走（至少这次是如此）。

```py
class Sprinkling(AbstractYardState):
   def transit(self) -> Union['Hit', 'Miss']:
       self.state_machine.simulate_sprinkling()

       next_state: Union['Hit', 'Miss']
       if self.state_machine.hit_successfully:
           next_state = Hit(self.state_machine)
       else:
           next_state = Miss(self.state_machine)

       return next_state
```

注：Hit状态没有专门的转移逻辑，它的存在是为了遵循关于草地上翼助攻击领域的语义。忽略它会导致Shooting状态直接过渡到Leaving。

```py
class Hit(AbstractYardState):
   def transit(self) -> 'Leaving':
       return Leaving(self.state_machine)
```

如果水喷洒器被激活且草地上没有鸟（检测器错误预测了鸟的存在），状态机将返回到Spawn状态。如果鸟实际上在场且我们没有检测到，草地上可能会有鸟屎。

```py
class Miss(AbstractYardState):
   def transit(self) -> Union['Attacking', 'Spawn']:
       next_state: Union['Attacking', 'Spawn']
       if self.state_machine.bird_present:
           next_state = Attacking(self.state_machine)
       else:
           next_state = Spawn(self.state_machine)

       return next_state
```

最终，攻击尝试可能会对草地造成实际的损害，正如Attacking类及其子类所体现的那样：

```py
class Attacking(AbstractYardState):
   def transit(self) -> Union['Attacked', 'NotAttacked']:
       self.state_machine.simulate_spoiling()

       next_state: Union['Attacked', 'NotAttacked']
       if self.state_machine.spoiled:
           next_state = Attacked(self.state_machine)
       else:
           next_state = NotAttacked(self.state_machine)

       return next_state

class Attacked(AfterAttacking):
   def transit(self) -> Union['Leaving', 'Spawn']:
       return super().transit()

class NotAttacked(AfterAttacking):
   def transit(self) -> Union['Leaving', 'Spawn']:
       return super().transit()
```

我们可以采用与Intrusion状态相同的思路，将共享的转移逻辑封装到一个名为AfterAttacking的超类中，从而得到Leaving或返回Spawn状态：

```py
class AfterAttacking(AbstractYardState):
   def transit(self) -> Union['Leaving', 'Spawn']:
       next_state: Union['Leaving', 'Spawn']
       if self.state_machine.max_stay_reached:
           next_state = Leaving(self.state_machine)
       else:
           next_state = Spawn(self.state_machine)

       return next_state
```

接下来会发生什么呢？当模拟达到步数限制时，它会卡在End状态：

```py
class End(AbstractYardState):
   def transit(self) -> 'End':
       return self
```

在实际操作中，我们不希望程序无休止地执行。因此，一旦模拟检测到过渡到结束状态，它将关闭。

# 模拟CaT系统

*“在鸟类探测的微妙世界中，请记住：当一个模型说“未检测到鸡”时，可能有一只狡猾的鸟正悄悄地站在草坪上，未被发现。这种差异提醒我们需要完善和增强我们的人工智能系统。” — 无名工程师。*

现在，我们希望模拟鸟类闯入草坪、破坏草坪并离开的过程。为此，我们将采用一种称为离散事件模拟的模拟建模方法。我们将通过分析系统各个元素之间最重要的关系，并基于有限自动机原理开发一个模拟系统来再现系统行为。为此，我们需要考虑以下几个方面：

1.  鸟类可以闯入房产的后院。

1.  基于计算机视觉的系统尝试检测和分类入侵对象。

1.  基于以上情况，如果对象被识别为某种特定的鸟类，我们将建模水洒喷头的过程以将其驱逐。

1.  还应该提到，有一个概率过程会导致鸟类破坏草坪（再次声明，没什么个人恩怨，只是羽毛）。

## 草坪模拟过程

现在，是时候探索概率的魔力，通过实现的有限状态机来模拟这些过程了。为此，我们需要创建一个`YardSimulation`类来封装模拟逻辑。如前所述，模拟不仅仅是有限状态机。模拟步骤与状态机过渡之间的对应关系也适用。也就是说，系统需要执行多个状态过渡才能切换到下一个时间步骤。

在这里，`step()`方法处理从当前状态到下一个状态的过渡，并调用有限状态机（FSM）的`transit()`方法，直到状态机返回到生成状态或达到结束状态。

```py
def step(self) -> Tuple[int, List[AbstractYardState]]:
   self.step_idx += 1

   transitions = list()
   while True:
       next_state = self.current_state.transit()
       transitions.append(next_state)
       self.current_state = next_state

       if self.current_state in (Spawn(self), End(self)):
           break

   return self.step_idx, transitions
```

在`run()`方法中，我们在循环中调用`step()`并生成其输出，直到系统过渡到结束步骤：

```py
def run(self) -> Iterator[Tuple[int, List[AbstractYardState]]]:
   while self.current_state != End(self):
       yield self.step()
```

`reset()`方法在鸟离开后重置有限状态机的记忆。

```py
def reset(self) -> 'YardSimulation':
   self.current_state = Start(self)
   self.intruder_class = Target.EMPTY
   self.detected_class = Target.EMPTY
   self.hit_successfully = False
   self.spoiled = False
   self.stayed_steps = 0

   return self
```

当一只鸟被水洒喷头成功喷到，或者它在草坪上停留太久（例如，假设它感到无聊）时，它就离开了。后者相当于鸟在草坪上停留了 5 个模拟步骤（即 5 分钟）。时间并不长，谁知道呢，也许邻居的草坪看起来更吸引人。让我们实现一些我们系统行为的核心部分。

首先，如果草坪上没有鸟类（即真正的入侵者类别），我们尝试生成一只。

```py
def simulate_intrusion(self) -> Target:
   if not self.bird_present:
       self.intruder_class = self.spawn_target()

   return self.intruder_class
```

这里，生成与非法入侵实体（鸟或无物）实时创建相关。

```py
@property
def bird_present(self) -> bool:
   return self.intruder_class != Target.EMPTY
```

然后，基于计算机视觉的系统——由类混淆矩阵描述——尝试检测和分类入侵对象。在这个过程中，我们模拟一个预测生成，同时牢记实际的入侵者类别（地面真相）。

```py
def simulate_detection(self) -> Target:
   self.detected_class = self.get_random_target(self.intruder_class)

   return self.detected_class
```

检测器在模拟的每个时间步上都在工作，因为模拟系统并不知道实际情况（否则我们为什么还需要检测器呢？）。如果检测器识别到鸟类，我们会尝试用水喷洒器把它赶走，水流量依赖于检测到的目标类别：

```py
def simulate_sprinkling(self) -> bool:
   self.hit_successfully = self.bird_present and (self.rng.uniform() <= self.hit_proba) and self.target_vulnerable

   return self.hit_successfully
```

无论喷水是否成功，系统都会消耗水。成功的判定标准包括以下条件：草坪上有鸟类存在（a），水喷洒器击中鸟类（b），喷洒的水量足够/适合处理给定大小的鸟类（c）。请注意，（c）鸡“喷洒”不会处理火鸡，但其他情况下适用。

草地污染部分——鸟类有可能弄脏草坪。如果发生这种情况，草坪损坏率就会增加（显然）。

```py
def simulate_spoiling(self) -> bool:
   self.spoiled = self.bird_present and (self.rng.uniform() <= self.shit_proba)
   if self.spoiled:
       self.lawn_damage[self.intruder_class] += self.intruder_class.damage

   return self.spoiled
```

现在我们具备了所有必要的条件来模拟我们将要处理的 CaT 问题的单一时间步。模拟时间开始！

## 鸟类逃跑

现在，我们已经准备好使用 FSM 模拟来模拟一个在不同设置下的 AI 辅助草坪安全系统。在进行草坪模拟时，`YardSimulation.run()` 方法会遍历一系列的状态转移，直到系统达到最大步数。在此过程中，我们实例化一个模拟对象（即状态机），设置 `num_steps` 参数，这个参数反映了模拟的总时间步数（比如 12 小时或白天），以及与计算机视觉（CV）基础的鸟类检测子系统的混淆矩阵相关的 `detector_matrix`，该子系统经过训练，用来预测鸡和火鸡：

```py
sim = YardSimulation(detector_matrix=detector_matrix, num_steps=num_steps)
```

现在我们可以运行 FSM 模拟，并打印 FSM 在每个时间步经历的状态转移：

```py
for step_idx, states in sim.run():
   print(f'\t{step_idx:0>3}: {" -> ".join(map(str, states))}')
```

此外，我们还积累了与鸟类喷水（`simulate_sprinkling`）和鸟类到达后的草地清理（`simulate_spoiling`）相关的水使用模拟统计数据。

```py
def simulate_sprinkling(self) -> bool:
   ...
   self.water_consumption[self.detected_class] += self.detected_class.health
   ...

def simulate_spoiling(self) -> bool:
   ...
   if self.spoiled:
       self.lawn_damage[self.intruder_class] += self.intruder_class.damage
   ...
```

当模拟达到其限制时，我们可以计算每个类别到一天结束时的总水消耗。我们希望看到的是每次模拟运行后的变化情况。

```py
water_sprinkling_total = sum(sim.water_consumption.values())
lawn_damage_total = sum(sim.lawn_damage.values())
```

最后，让我们进行实验，评估在计算机视觉子系统发生变化时，系统的表现如何。为此，我们将使用 `YardSimulation.run()` 方法进行 100 次试验，分别针对未经训练的（基准）和完美检测矩阵进行模拟：

```py
detector_matrix_baseline = np.full(
     (len(Target),) * 2,  # size of the confusion matrix (3 x 3)
     len(Target) ** -1    # prediction probability for each class is the same and equals to 1/3 
)
detector_matrix_perfect = np.eye(len(Target))
```

然后，我们可以聚合并比较不同实验设置下与目标喷水和草坪清理的总水使用量相关的输出统计数据：

![](../Images/358c0e78bfce985a406f93e3b1888da8.png)

图 2\. FSM 模拟输出统计数据，涵盖了鸟类检测子系统的边缘情况（图片由作者提供）

实验结果的总结对比显示，拥有一个更好的计算机视觉（CV）模型可以使得水使用量减少37.8%（70.9与44.1），相较于未经过训练的基线检测器，在给定输入参数和仿真条件下对鸟类的检测——这一概念既直观又在预期之中。但“更好”的定量意义是什么呢？是否值得费力地精细调整模型？数值结果展示了改进模型的价值，激励了进一步优化的努力。未来，我们将把这些统计结果作为全局优化的目标，以提高鸟类检测子系统的效率，并减少系统操作和维护中的水消耗，从而让工程师稍微高兴一点。

本教程所使用的源代码可以在此GitHub仓库中找到：[https://github.com/slipnitskaya/computer-vision-birds](https://github.com/slipnitskaya/computer-vision-birds)。

# **结论**

总结来说，仿真建模是一个有用的工具，可以用来估算过程的效率、快速测试预期的变化，并了解如何通过操作和维护改进过程。通过本文，你对仿真建模在解决工程问题中的实际应用有了更好的理解。特别是，我们已经涵盖了以下内容：

+   如何设计一个模型来近似一个复杂系统，从而改进其在鸟类检测和水洒布方面的操作。

+   如何创建现实世界过程的仿真，以便在不同条件下理解CaT系统的行为。

+   如何在Python中实现基于FSM的解决方案，使得系统能够追踪仿真过程中的相关统计数据。

# 接下来做什么

聚焦于提高资源效率，在后续文章中，你将发现如何通过应用蒙特卡洛方法和可解释AI（XAI）技术来解决水成本降低的非解析优化问题，从而增强基于计算机视觉的鸟类检测子系统，推动我们的仿真AI辅助草坪安防系统的发展。

在视觉项目的仿真建模和优化中，还有哪些重要的概念？请访问[Bird by Bird Tech](https://towardsdatascience.com/tagged/bird-by-bird-tech)了解更多。

# 参考文献

1.  Forsyth, David. 《计算机科学中的概率与统计》。第13卷。Cham: Springer International Publishing, 2018。

1.  Knuth, Donald Ervin. 《计算机程序设计的艺术》。第3卷。Reading, MA: Addison-Wesley, 1973。

1.  Wagner, Ferdinand 等. 《使用有限状态机建模软件：一种实用方法》。Auerbach Publications, 2006。
