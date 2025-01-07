# 面向产品分析师的实用计算机仿真

> 原文：[https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-fe61e2b577f5?source=collection_archive---------8-----------------------#2024-05-24](https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-fe61e2b577f5?source=collection_archive---------8-----------------------#2024-05-24)

## 第三部分：建模运营队列

[](https://miptgirl.medium.com/?source=post_page---byline--fe61e2b577f5--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--fe61e2b577f5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe61e2b577f5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fe61e2b577f5--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--fe61e2b577f5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe61e2b577f5--------------------------------) ·阅读时长23分钟·2024年5月24日

--

![](../Images/71a5c5982659d4e38fd756c9ae246157.png)

图像由DALL-E 3生成

今天，我想给大家展示一个离散事件仿真方法的例子。我们将模拟客户支持团队，并决定采用何种策略来提高其绩效。但首先，请允许我分享一下我的个人经历。

我第一次在大学里学习离散仿真。我的一门课程是[排队论](https://en.wikipedia.org/wiki/Queueing_theory)，为了获得期末成绩，我需要实现一个机场仿真并计算一些关键绩效指标（KPI）。不幸的是，由于我已经全职工作，我错过了所有的研讨会，因此对这一主题背后的理论以及如何进行操作毫无头绪。

我下定决心要获得一个优异的成绩，因此找了一本书，读懂了基础知识，并花了几个晚上进行实现。虽然这对我来说相当有挑战性，因为一段时间没写代码了，但我还是搞明白了，并且得到了A的成绩。

在那时（就像很多学生一样），我感觉这些信息对我未来的工作没有什么帮助。然而，后来我意识到，许多分析性任务都可以通过这种方法来解决。所以，我想和大家分享这个方法。

代理基础仿真最明显的应用场景之一是运营分析。大多数产品都有客户支持，客户可以在此寻求帮助。客户支持团队通常会关注以下指标：

+   ***平均解决时间*** — 客户向客服提出问题到收到第一次回复所经历的时间，

+   ***队列大小***，即目前我们积压了多少任务。

没有适当的模型，理解我们的变化（例如，增加夜班或仅仅增加代理数量）如何影响 KPI 可能会比较困难。模拟将帮助我们做到这一点。

所以，让我们不要浪费时间，继续前进。

# 模拟和建模的基础

从最开始开始。我们将建模***系统***。系统是一个由实体（例如人、服务器甚至机械工具）组成的集合，这些实体相互作用以实现某个逻辑目标（即回答客户问题或通过机场的边检）。

你可以根据研究目标定义系统所需的粒度级别。例如，在我们的案例中，我们希望研究代理的效率和时间表的变化如何影响平均的 CS 工单解决时间。因此，系统将仅是一个代理集合。然而，如果我们想要模拟将一些工单外包给不同的外包公司，我们就需要在模型中包含这些合作伙伴。

系统由一组变量描述——例如队列中的工单数量或当前在工作时刻的代理数量。这些变量定义了***系统状态***。

系统有两种类型：

+   ***离散*** *—* 当系统状态瞬间发生变化时，例如，新的工单被添加到队列中，或者代理完成了他们的班次。

+   ***连续——***当系统不断演变时。例如，飞行中的飞机，坐标、速度、高度等参数在飞行过程中持续变化。

对于我们的任务，我们可以将系统视为离散的，并使用***离散事件模拟方法***。这是指系统只能在有限的时间点发生变化。这些时间点是事件发生的时刻，并立即改变系统状态。

因此，整个方法基于事件。我们将逐一生成和处理事件，以模拟系统如何运作。我们可以使用时间线的概念来结构化事件。

由于这个过程是动态的，我们需要跟踪模拟时间的当前值，并能够将其从一个值推进到另一个值。在模拟模型中，显示当前时间的变量通常称为***模拟时钟***。

我们还需要一个机制来推进模拟时间。推进时间有两种方法：

+   ***下一事件时间推进*** — 我们从一个事件的时间戳移动到下一个事件的时间戳。

+   ***固定增量时间推进*** — 我们选择一个时间段，例如 1 分钟，每次按照这个时间段调整时钟。

我认为第一种方法更容易理解、实现和调试。因此，在本文中我将坚持使用这种方法。

让我们回顾一个简单的例子，理解它是如何运作的。我们将讨论一个简化的 CS 工单队列案例。

我们开始模拟，初始化模拟时钟。有时候，人们会使用零作为初始值。我更喜欢使用真实的时间数据和实际的日期时间。

这是我们系统的初始状态。我们的时间线上有两个与客户请求相关的事件。

![](../Images/dd95b05f1a22997ef9e856012f5d64c6.png)

下一步是将模拟时钟推进到我们的时间线上的第一个事件——9:15的客户请求。

![](../Images/31f4bc945689d456b0ed1715677cc691.png)

现在是处理这个事件的时候了。我们应该找到一个代理来处理这个请求，分配请求给他们，并生成一个完成任务的事件。事件是我们模拟的主要驱动力，所以如果一个事件创造了另一个事件是可以的。

查看更新后的时间线，我们可以看到，最紧迫的事件不是第二个客户请求，而是第一个任务的完成。

![](../Images/39e090293ad9228610e4c0829fc9c08c.png)

所以，我们需要将时钟推进到9:30，并处理下一个事件。请求的完成不会创造新的事件，因此在此之后，我们将转到第二个客户请求。

![](../Images/cef08b7308d87f030a8239ecdf7ccdd0.png)

我们将重复这个从一个事件到另一个事件的过程，直到模拟结束。

为了避免无限循环的过程，我们需要定义停止标准。在这种情况下，我们可以使用以下逻辑：如果时间线上没有更多事件，我们应该停止模拟。在这个简化的例子中，我们的模拟将在完成第二个任务后停止。

我们已经讨论了离散事件模拟的理论，并理解了它是如何工作的。现在，是时候实践并在代码中实现这种方法了。

# 程序架构

## 面向对象编程

在我的日常工作中，我通常使用过程式编程范式。我为一些重复性的任务创建函数，但除此之外，我的代码是相当线性的。这是数据处理任务中的一种标准方法。

在这个例子中，我们将使用[面向对象编程](https://en.wikipedia.org/wiki/Object-oriented_programming)。所以，如果你之前没有使用过 Python 类，或者需要复习一下这个主题，那就花点时间来复习它吧。

面向对象编程（OOP）基于对象的概念。对象由数据（称为属性的某些特征）和行为（函数或方法）组成。整个程序描述了不同对象之间的交互。例如，如果我们有一个代表客户服务代理的对象，它可以具有以下属性：

+   ***属性***：名称、代理开始工作的日期、他们在任务上花费的平均时间或当前状态（`"out of office"`、`"working on task"` 或 `"free"`）。

+   ***方法***：返回名称，更新状态或开始处理客户请求。

为了表示这样的一个对象，我们可以使用 Python 类。让我们为客户服务代理编写一个简单的类。

```py
class CSAgent:
  # initialising class
  def __init__(self, name, average_handling_time):
      # saving parameters mentioned during object creation
      self.name = name  
      self.average_handling_time = average_handling_time
      # specifying constant value
      self.role = 'CS agent'
      print('Created %s with name %s' % (self.role, self.name))

  def get_name(self):
    return self.name

  def get_handling_time(self):
    return self.average_handling_time

  def update_handling_time(self, average_handling_time):
    print('Updating time from %.2f to %.2f' % (self.average_handling_time, 
      average_handling_time))
    self.average_handling_time = average_handling_time
```

这个类定义了每个代理人的姓名、平均处理时间和角色。我还添加了一些可以返回内部变量的函数，遵循封装模式。另外，我们有一个`update_handling_time`函数，可以让我们更新代理人的表现。

我们创建了一个类（一个解释任何类型CS代理的对象）。现在让我们创建该对象的实例——代理人John Doe。

```py
john_agent = CSAgent('John Doe', 12.3)
# Created CS agent with name John Doe
```

当我们创建类的实例时，`__init__`函数会被执行。我们可以使用`__dict__`属性将类字段以字典的形式展示出来。这在很多情况下都很有用，例如，如果你想把一系列对象转换为数据框架。

```py
print(john_agent.__dict__)
# {'name': 'John Doe', 'average_handling_time': 12.3, 'role': 'CS agent'}
```

我们可以尝试执行一个方法并更新代理人的表现。

```py
john_agent.update_handling_time(5.4)
# Updating time from 12.30 to 5.40

print(john_agent.get_handling_time())
# 5.4
```

今天我们将使用的面向对象编程的基本概念之一是继承。继承允许我们有一个高层的祖先类，并在子类中使用其特性。想象一下，我们不仅想要CS代理人，还想要KYC代理人。我们可以创建一个高层的`Agent`类，包含共同功能，并仅为KYC和CS代理人定义一次。

```py
class Agent:
  # initialising class
  def __init__(self, name, average_handling_time, role):
    # saving parameters mentioned during object creation
    self.name = name  
    self.average_handling_time = average_handling_time
    self.role = role
    print('Created %s with name %s' % (self.role, self.name))

  def get_name(self):
    return self.name

  def get_handling_time(self):
    return self.average_handling_time

  def update_handling_time(self, average_handling_time):
    print('Updating time from %.2f to %.2f' % (self.average_handling_time, 
      average_handling_time))
    self.average_handling_time = average_handling_time
```

现在，我们可以为这些代理类型创建独立的类，并定义稍微不同的`__init__`和`get_job_description`函数。

```py
class KYCAgent(Agent):
  def __init__(self, name, average_handling_time):
    super().__init__(name, average_handling_time, 'KYC agent')

  def get_job_description(self):
    return 'KYC (Know Your Customer) agents help to verify documents'

class CSAgent(Agent):
  def __init__(self, name, average_handling_time):
    super().__init__(name, average_handling_time, 'CS agent')

  def get_job_description(self):
    return 'CS (Customer Support) answer customer questions and help resolving their problems'
```

为了指定继承关系，我们在当前类名后面的括号中提到基类。使用`super()`，我们可以调用基类的方法，例如`__init__`，用自定义的`role`值创建对象。

让我们创建对象并检查它们是否按预期工作。

```py
marie_agent = KYCAgent('Marie', 25)
max_agent = CSAgent('Max', 10)

print(marie_agent.__dict__)
# {'name': 'Marie', 'average_handling_time': 25, 'role': 'KYC agent'}
print(max_agent.__dict__)
# {'name': 'Max', 'average_handling_time': 10, 'role': 'CS agent'}
```

让我们更新Marie的处理时间。尽管我们没有在`KYCAgent`类中实现这个函数，它使用的是基类中的实现，效果很好。

```py
marie_agent.update_handling_time(22.5)
# Updating time from 25.00 to 22.50
```

我们还可以调用我们在类中定义的方法。

```py
print(marie_agent.get_job_description())
# KYC (Know Your Customer) agents help to verify documents

print(max_agent.get_job_description())
# CS (Customer Support) answer customer questions and help resolving their problems
```

所以，我们已经涵盖了面向对象编程范式和Python类的基础。我希望这对你有所帮助，起到了复习作用。

现在，是时候回到我们的任务和我们需要的仿真模型了。

## 架构：类

如果你之前没怎么使用过面向对象编程，可能会觉得从过程式编程切换到面向对象编程有些困难。这需要一些时间来完成思维方式的转变。

一个生活小窍门是使用现实世界的类比（例如，很明显代理人是一个具有一些特征和行为的对象）。

同时，不要害怕犯错。程序架构有好有坏：有些架构随时间推移更容易阅读和维护。然而，即使在成熟的软件工程师之间，对于最佳实践也有很多争论，所以我建议不要过于纠结于让它在分析性临时研究中变得完美。

让我们思考一下在仿真中需要哪些对象：

+   `System`——我们任务中最顶层的概念。系统将表示当前状态并执行仿真。

+   正如我们之前讨论的，系统是一个实体的集合。因此，我们需要的下一个对象是`Agent`。这个类将描述在任务中工作的代理人。

+   每个代理人都有自己的时间表：代理人工作的小时数，所以我将其提取到一个单独的类`Schedule`中。

+   我们的代理人将处理客户请求。所以，这不言而喻——我们需要在系统中表示它们。此外，我们将在`System`对象中存储一个已处理请求的列表，以便在仿真结束后获取最终统计数据。

+   如果没有空闲的代理人接手新的客户请求，该请求将被放入队列中。因此，我们将拥有一个`RequestQueue`对象来存储所有客户请求，采用FIFO逻辑（先进先出）。

+   以下是一个重要的概念，即`TimeLine`，它代表了我们需要按时间顺序处理的事件集合。

+   `TimeLine`将包括事件，因此我们也将为它们创建一个`Event`类。由于我们将有许多不同类型的事件需要以不同方式处理，我们可以利用面向对象编程的继承机制。我们将在下一节中更详细地讨论事件类型。

就这样。我已经把所有的类和它们之间的关系放入了图表中，以便更清晰地表达它。在开始实现之前，我使用这样的图表来获得系统的高层次视图——它有助于提前考虑架构。

![](../Images/b29d63495089da7a70b25081a396022f.png)

正如你可能已经注意到的，图表并不是特别详细。例如，它没有包含所有字段名称和方法。这是故意的。这个架构将作为一个宏观视图来指导开发。因此，我不想花太多时间列出所有字段和方法的名称，因为这些细节可能会在实现过程中发生变化。

## 架构：事件类型

我们已经讨论了程序架构，现在是时候思考我们仿真的主要驱动因素——事件。

让我们讨论一下我们需要生成哪些事件来保持系统的运转。

+   我将从"*Agent Ready*"事件开始。它表示代理人开始工作并准备接手任务（如果队列中有等待的任务）。

+   我们需要知道代理人何时开始工作。这些工作时间可以依赖于代理人以及星期几。可能的话，我们甚至希望在仿真过程中更改这些时间表。由于在系统初始化时我们并不知道完成仿真需要多长时间，创建所有的"*Agent Ready*"事件是相当具有挑战性的。因此，我建议使用一个周期性的"*Plan Agents Schedule*"事件来为第二天创建准备工作的事件。

+   另一个必不可少的事件是"*New Customer Request*"——一个表示我们收到了新的客户服务请求，我们需要么开始处理它，么将其放入队列中的事件。

+   最后的事件是"*Agent Finished Task*"，它表示代理人完成了他正在处理的任务，并且可能准备好接手一个新的任务。

就这样。这四个事件足以运行整个仿真。

类似于类的定义，对于系统建模没有对错之分。你可以使用略有不同的事件集。例如，你可以添加一个“*开始任务*”事件，以便明确表示。

# 实现

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/blob/main/simulations/queue_simulation.ipynb)上找到完整的实现。

我们已经定义了我们解决方案的高层结构，因此我们准备好开始实现它了。让我们从我们的仿真核心——系统类开始。

## 初始化系统

让我们从系统类的`__init__`方法开始。

首先，让我们思考一下我们希望为仿真指定的参数：

+   `agents` — 将在CS团队中工作的代理集，

+   `queue` — 当前的客户请求队列（如果有的话），

+   `initial_date` — 由于我们同意使用实际时间戳而非相对时间戳，我将指定开始仿真的日期，

+   `logging` — 定义我们是否希望打印一些调试信息的标志，

+   `customer_requests_df` — 包含我们希望处理的客户请求集的信息的数据框。

除了输入参数，我们还将创建以下内部字段：

+   `current_time` — 我们将初始化为指定初始日期的00:00:00的仿真时钟，

+   `timeline`对象，我们将用它来定义事件的顺序，

+   `processed_request` — 一个空列表，我们将在其中存储处理过的客户请求，以便在仿真后获取数据。

现在是时候采取必要的行动来初始化系统了。只剩下两个步骤：

+   计划代理在第一天工作。我将生成并处理一个带有初始时间戳的相应事件。

+   通过将相应的“*新客户请求*”事件添加到时间线上来加载客户请求。

这是执行所有这些初始化系统操作的代码。

```py
class System:
  def __init__(self, agents, queue, initial_date,
  customer_requests_df, logging = True):
    initial_time = datetime.datetime(initial_date.year, initial_date.month, 
      initial_date.day, 0, 0, 0)
    self.agents = agents
    self.queue = RequestQueue(queue)
    self.logging = logging
    self.current_time = initial_time

    self._timeline = TimeLine()
    self.processed_requests = []

    initial_event = PlanScheduleEvent('plan_agents_schedule', initial_time)
    initial_event.process(self)
    self.load_customer_request_events(customer_requests_df)
```

它还没有工作，因为它链接到一些未实现的类和方法，但我们会逐一解决这些问题。

## 时间线

让我们从系统定义中使用的类开始。第一个是`TimeLine`。它唯一的字段是事件列表。此外，它实现了一些方法：

+   添加事件（并确保它们按时间顺序排列），

+   返回下一个事件并将其从列表中删除，

+   显示剩余的事件数量。

```py
class TimeLine:
  def __init__(self):
    self.events = []

  def add_event(self, event:Event):
    self.events.append(event)
    self.events.sort(key = lambda x: x.time)

  def get_next_item(self):
    if len(self.events) == 0:
      return None
    return self.events.pop(0)

  def get_remaining_events(self):
    return len(self.events)
```

## 客户请求队列

我们在初始化中使用的另一个类是`RequestQueue`。

没有什么意外的情况：请求队列由客户请求组成。让我们从这个构建块开始。我们知道每个请求的创建时间以及处理该请求时，代理需要多少时间。

```py
class CustomerRequest:
  def __init__(self, id, handling_time_secs, creation_time):
    self.id = id
    self.handling_time_secs = handling_time_secs
    self.creation_time = creation_time

  def __str__(self):
    return f'Customer Request {self.id}: {self.creation_time.strftime("%Y-%m-%d %H:%M:%S")}'
```

它是一个简单的数据类，仅包含参数。这里唯一的新内容是我重写了`__str__`方法，以改变打印函数的输出。这对调试非常有用。你可以自己比较。

```py
test_object = CustomerRequest(1, 600, datetime.datetime(2024, 5, 1, 9, 42, 1))
# without defining __str__
print(test_object)
# <__main__.CustomerRequest object at 0x280209130>

# with custom __str__
print(test_object)
# Customer Request 1: 2024-05-01 09:42:01
```

现在，我们可以继续处理请求队列。与时间轴类似，我们实现了方法来添加新请求、计算队列中的请求并获取队列中的下一个请求。

```py
class RequestQueue:
  def __init__(self, queue = None):
    if queue is None:
      self.requests = []
    else: 
      self.requests = queue

  def get_requests_in_queue(self):
    return len(self.requests)

  def add_request(self, request):
    self.requests.append(request)

  def get_next_item(self):
    if len(self.requests) == 0:
      return None
    return self.requests.pop(0)
```

## 代理

我们需要初始化系统的另一件事是代理。首先，每个代理都有一个时间表——根据工作日来决定他们的工作时间。

```py
class Schedule:
  def __init__(self, time_periods):
    self.time_periods = time_periods

  def is_within_working_hours(self, dt):
    weekday = dt.strftime('%A')

    if weekday not in self.time_periods:
      return False

    hour = dt.hour
    time_periods = self.time_periods[weekday]
    for period in time_periods:
      if (hour >= period[0]) and (hour < period[1]):
        return True
    return False
```

我们唯一有的关于时间表的方法是检查在指定时刻代理是否在工作。

让我们定义代理类。每个代理将具有以下属性：

+   `id`和`name` — 主要用于日志记录和调试目的，

+   `schedule` — 我们刚刚定义的代理时间表对象，

+   `request_in_work` — 连接到客户请求对象，显示代理当前是否忙碌。

+   `effectiveness` — 显示代理与解决特定任务的预期时间相比效率的系数。

我们为代理实现了以下方法：

+   理解他们是否能接受新的任务（即他们是否空闲并仍在工作）。

+   启动和结束处理客户请求。

```py
class Agent:
  def __init__(self, id, name, schedule, effectiveness = 1):
    self.id = id
    self.schedule = schedule
    self.name = name
    self.request_in_work = None
    self.effectiveness = effectiveness

  def is_ready_for_task(self, dt):
    if (self.request_in_work is None) and (self.schedule.is_within_working_hours(dt)):
      return True
    return False

  def start_task(self, customer_request):
    self.request_in_work = customer_request
    customer_request.handling_time_secs = int(round(self.effectiveness * customer_request.handling_time_secs))

  def finish_task(self):
    self.request_in_work = None
```

## 将初始客户请求加载到时间轴中

我们在系统的`__init__`函数中唯一缺失的部分（除了稍后我们将详细讨论的事件处理）是`load_customer_request_events`函数的实现。这个相当直接。我们只需要将其添加到我们的`System`类中。

```py
class System:
  def load_customer_request_events(self, df):
    # filter requests before the start of simulation
    filt_df = df[df.creation_time >= self.current_time]
    if filt_df.shape[0] != df.shape[0]:
      if self.logging:
        print('Attention: %d requests have been filtered out since they are outdated' % (df.shape[0] - filt_df.shape[0]))

    # create new customer request events for each record
    for rec in filt_df.sort_values('creation_time').to_dict('records'):
      customer_request = CustomerRequest(rec['id'], rec['handling_time_secs'], 
        rec['creation_time'])

      self.add_event(NewCustomerRequestEvent(
        'new_customer_request', rec['creation_time'],
         customer_request
      ))
```

很好，我们已经弄清楚了主要类。那么，接下来我们就开始实现事件。

## 处理事件

如前所述，我将使用继承方法并创建一个`Event`类。目前，它只实现了`__init__`和`__str__`函数，但它可能帮助我们为所有事件提供额外的功能。

```py
class Event:
  def __init__(self, event_type, time):
    self.type = event_type
    self.time = time

  def __str__(self):
    if self.type == 'agent_ready_for_task':
      return '%s (%s) - %s' % (self.type, self.agent.name, self.time)
    return '%s - %s' % (self.type, self.time)
```

然后，我为每种可能具有略微不同初始化方式的事件类型实现了一个单独的子类。例如，对于`AgentReady`事件，我们还有一个`Agent`对象。更重要的是，每个事件类都实现了`process`方法，该方法以`system`作为输入。

```py
 class AgentReadyEvent(Event):
  def __init__(self, event_type, time, agent):
    super().__init__(event_type, time)
    self.agent = agent

  def process(self, system: System):
    # get next request from the queue
    next_customer_request = system.queue.get_next_item()

    # start processing request if we had some
    if next_customer_request is not None:
      self.agent.start_task(next_customer_request)
      next_customer_request.start_time = system.current_time
      next_customer_request.agent_name = self.agent.name
      next_customer_request.agent_id = self.agent.id

      if system.logging:
        print('<%s> Agent %s started to work on request %d' % (system.current_time, 
          self.agent.name, next_customer_request.id))

      # schedule finish processing event
      system.add_event(FinishCustomerRequestEvent('finish_handling_request', 
        system.current_time + datetime.timedelta(seconds = next_customer_request.handling_time_secs), 
        next_customer_request, self.agent)) 

class PlanScheduleEvent(Event):
  def __init__(self, event_type, time):
    super().__init__(event_type, time)

  def process(self, system: System):     
    if system.logging:
        print('<%s> Scheeduled agents for today' % (system.current_time))
    current_weekday = system.current_time.strftime('%A')

    # create agent ready events for all agents working on this weekday
    for agent in system.agents:
      if current_weekday not in agent.schedule.time_periods:
        continue

      for time_periods in agent.schedule.time_periods[current_weekday]:
        system.add_event(AgentReadyEvent('agent_ready_for_task', 
          datetime.datetime(system.current_time.year, system.current_time.month, 
          system.current_time.day, time_periods[0], 0, 0), 
          agent))

    # schedule next planning
    system.add_event(PlanScheduleEvent('plan_agents_schedule', system.current_time + datetime.timedelta(days = 1)))

class FinishCustomerRequestEvent(Event):
  def __init__(self, event_type, time, customer_request, agent):
    super().__init__(event_type, time)
    self.customer_request = customer_request
    self.agent = agent

  def process(self, system):
    self.agent.finish_task()
    # log finish time
    self.customer_request.finish_time = system.current_time
    # save processed request
    system.processed_requests.append(self.customer_request)

    if system.logging:
      print('<%s> Agent %s finished request %d' % (system.current_time, self.agent.name, self.customer_request.id))

    # pick up the next request if agent continue working and we have something in the queue
    if self.agent.is_ready_for_task(system.current_time):
      next_customer_request = system.queue.get_next_item()
      if next_customer_request is not None:
        self.agent.start_task(next_customer_request)
        next_customer_request.start_time = system.current_time
        next_customer_request.agent_name = self.agent.name
        next_customer_request.agent_id = self.agent.id

        if system.logging:
            print('<%s> Agent %s started to work on request %d' % (system.current_time, 
              self.agent.name, next_customer_request.id))
        system.add_event(FinishCustomerRequestEvent('finish_handling_request', 
          system.current_time + datetime.timedelta(seconds = next_customer_request.handling_time_secs), 
          next_customer_request, self.agent)) 

class NewCustomerRequestEvent(Event):
  def __init__(self, event_type, time, customer_request):
    super().__init__(event_type, time)
    self.customer_request = customer_request

  def process(self, system: System):
    # check whether we have a free agent
    assigned_agent = system.get_free_agent(self.customer_request)

    # if not put request in a queue
    if assigned_agent is None:
      system.queue.add_request(self.customer_request)
      if system.logging:
          print('<%s> Request %d put in a queue' % (system.current_time, self.customer_request.id))
    # if yes, start processing it
    else:
      assigned_agent.start_task(self.customer_request)
      self.customer_request.start_time = system.current_time
      self.customer_request.agent_name = assigned_agent.name
      self.customer_request.agent_id = assigned_agent.id
      if system.logging:
          print('<%s> Agent %s started to work on request %d' % (system.current_time, assigned_agent.name, self.customer_request.id))
      system.add_event(FinishCustomerRequestEvent('finish_handling_request', 
        system.current_time + datetime.timedelta(seconds = self.customer_request.handling_time_secs), 
        self.customer_request, assigned_agent))
```

实际上，事件处理的业务逻辑就到此为止。我们需要完成的唯一部分就是将所有内容组合起来，运行我们的仿真。

## 在系统类中将所有内容整合在一起

正如我们讨论的那样，`System`类将负责运行仿真。因此，我们将在那里完成剩下的部分。

这是剩余的代码。让我简要地向你介绍一下要点：

+   `is_simulation_finished`定义了我们的仿真停止标准——队列中没有请求，且时间轴中没有事件。

+   `process_next_event`从时间线中获取下一个事件并对其执行`process`。这里有一个小细节：我们可能会遇到一种情况，模拟永远不会结束，因为"*Plan Agents Schedule*"事件不断发生。因此，在处理这种事件时，我会检查时间线中是否还有其他事件，如果没有，我就不再处理它，因为我们不再需要安排代理了。

+   `run_simulation`是控制我们世界的函数，但由于我们的架构相当不错，它只有几行代码：我们检查是否能完成模拟，如果不能，我们就处理下一个事件。

```py
class System:
  # defines the stopping criteria
  def is_simulation_finished(self):
    if self.queue.get_requests_in_queue() > 0: 
      return False
    if self._timeline.get_remaining_events() > 0:
      return False
    return True

  # wrappers for timeline methods to incapsulate this logic
  def add_event(self, event):
    self._timeline.add_event(event)

  def get_next_event(self):
    return self._timeline.get_next_item()

  # returns free agent if we have one
  def get_free_agent(self, customer_request):
    for agent in self.agents:
      if agent.is_ready_for_task(self.current_time):
        return agent

  # finds and processes the next event
  def process_next_event(self):
    event = self.get_next_event()
    if self.logging:
      print('# Processing event: ' + str(event))
    if (event.type == 'plan_agents_schedule') and self.is_simulation_finished():
      if self.logging:
        print("FINISH")
    else:
      self.current_time = event.time        
      event.process(self)

  # main function
  def run_simulation(self):
    while not self.is_simulation_finished():
      self.process_next_event()
```

这是一段漫长的旅程，但我们做到了。做得好！现在，我们拥有了所有需要的逻辑。让我们进入有趣的部分，使用我们的模型进行分析。

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/blob/main/simulations/queue_simulation.ipynb)上找到完整的实现。

# 分析

我将使用一个合成的客户请求数据集来模拟不同的运营设置。

![](../Images/3766fb4263d15047736abfe15e6b3b64.png)

首先，让我们运行我们的系统并查看指标。我将从15个工作正常时间的代理开始。

```py
# initialising agents
regular_work_week = Schedule(
  {
    'Monday': [(9, 12), (13, 18)],
    'Tuesday': [(9, 12), (13, 18)],
    'Wednesday': [(9, 12), (13, 18)],
    'Thursday': [(9, 12), (13, 18)],
    'Friday': [(9, 12), (13, 18)]
  }
)

agents = []
for id in range(15):
  agents.append(Agent(id + 1, 'Agent %s' % id, regular_work_week))

# inital date
system_initial_date = datetime.date(2024, 4, 8)

# initialising the system 
system = System(agents, [], system_initial_date, backlog_df, logging = False)

# running the simulation 
system.run_simulation()
```

执行结果后，我们得到了`system.processed_requests`中的所有统计数据。让我们编写几个辅助函数，方便分析结果。

```py
# convert results to data frame and calculate timings
def get_processed_results(system):
  processed_requests_df = pd.DataFrame(list(map(lambda x: x.__dict__, system.processed_requests)))
  processed_requests_df = processed_requests_df.sort_values('creation_time')
  processed_requests_df['creation_time_hour'] = processed_requests_df.creation_time.map(
      lambda x: x.strftime('%Y-%m-%d %H:00:00')
  )

  processed_requests_df['resolution_time_secs'] = list(map(
      lambda x, y: int(x.strftime('%s')) - int(y.strftime('%s')),
      processed_requests_df.finish_time,
      processed_requests_df.creation_time
  ))

  processed_requests_df['waiting_time_secs'] = processed_requests_df.resolution_time_secs - processed_requests_df.handling_time_secs

  processed_requests_df['waiting_time_mins'] = processed_requests_df['waiting_time_secs']/60
  processed_requests_df['handling_time_mins'] = processed_requests_df.handling_time_secs/60
  processed_requests_df['resolution_time_mins'] = processed_requests_df.resolution_time_secs/60
  return processed_requests_df

# calculating queue size with 5 mins granularity
def get_queue_stats(processed_requests_df):
  queue_stats = []

  current_time = datetime.datetime(system_initial_date.year, system_initial_date.month, system_initial_date.day, 0, 0, 0)
  while current_time <= processed_requests_df.creation_time.max() + datetime.timedelta(seconds = 300):
    queue_size = processed_requests_df[(processed_requests_df.creation_time <= current_time) & (processed_requests_df.start_time > current_time)].shape[0]
    queue_stats.append(
      {
          'time': current_time,
          'queue_size': queue_size
      }
    )

    current_time = current_time + datetime.timedelta(seconds = 300)

  return pd.DataFrame(queue_stats)
```

此外，让我们做几个图表并计算每周的指标。

```py
def analyse_results(system, show_charts = True):
  processed_requests_df = get_processed_results(system)
  queue_stats_df = get_queue_stats(processed_requests_df)

  stats_df = processed_requests_df.groupby('creation_time_hour').aggregate(
      {'id': 'count', 'handling_time_mins': 'mean', 'resolution_time_mins': 'mean',
       'waiting_time_mins': 'mean'}
  )

  if show_charts:
    fig = px.line(stats_df[['id']], 
      labels = {'value': 'requests', 'creation_time_hour': 'request creation time'},
      title = '<b>Number of requests created</b>')
    fig.update_layout(showlegend = False)
    fig.show()

    fig = px.line(stats_df[['waiting_time_mins', 'handling_time_mins', 'resolution_time_mins']], 
      labels = {'value': 'time in mins', 'creation_time_hour': 'request creation time'},
      title = '<b>Resolution time</b>')
    fig.show()

    fig = px.line(queue_stats_df.set_index('time'), 
      labels = {'value': 'number of requests in queue'},
      title = '<b>Queue size</b>')
    fig.update_layout(showlegend = False)
    fig.show()

  processed_requests_df['period'] = processed_requests_df.creation_time.map(
      lambda x: (x - datetime.timedelta(x.weekday())).strftime('%Y-%m-%d')
  )
  queue_stats_df['period'] = queue_stats_df['time'].map(
      lambda x: (x - datetime.timedelta(x.weekday())).strftime('%Y-%m-%d')
  )

  period_stats_df = processed_requests_df.groupby('period')\
    .aggregate({'id': 'count', 'handling_time_mins': 'mean',
      'waiting_time_mins': 'mean', 
      'resolution_time_mins': 'mean'})\
    .join(queue_stats_df.groupby('period')[['queue_size']].mean())

  return period_stats_df

# execution
analyse_results(system)
```

现在，我们可以使用这个函数来分析模拟结果。显然，15个代理对于我们的产品来说不够，因为经过三周后，我们队列中有超过4000个请求，平均解决时间大约是十天。如果我们只有15个代理，客户对我们的服务肯定会非常不满意。

![](../Images/f8de16ff06157ff13f33a00afa3c4fc4.png)![](../Images/febfa76237f01782abdc2d85c423424c.png)

让我们找出需要多少个代理才能应对需求。我们可以进行多个模拟，使用不同数量的代理并比较结果。

```py
tmp_dfs = []

for num_agents in tqdm.tqdm(range(15, 105, 5)):
  agents = []
  for id in range(num_agents):
    agents.append(Agent(id + 1, 'Agent %s' % id, regular_work_week))
  system = System(agents, [], system_initial_date, backlog_df, logging = False)
  system.run_simulation()

  tmp_df = analyse_results(system, show_charts = False)
  tmp_df['num_agents'] = num_agents
  tmp_dfs.append(tmp_df)
```

我们可以看到，在大约25到30个代理的情况下，不同周的指标大致相同，因此有足够的容量来处理不断增加的请求，队列不会一周一周地增长。

![](../Images/e010166cb7d3325c2b92f2992fa55b4c.png)![](../Images/eefc26c7547bf7a3d19eda50a903becc.png)

如果我们模拟30个代理的情况，可以看到从星期二到星期五，队列从13:50开始一直到工作日结束都为空。代理们会在星期一处理我们在周末积累的大量队列。

![](../Images/28e8e206ea36958c081ae0b870719357.png)

在这种设置下，平均解决时间为500.67分钟，平均队列长度为259.39。

让我们尝试思考一些可能改善我们运营团队的方案：

+   我们可以再雇佣五个代理，

+   我们可以开始利用LLMs，减少30%的处理时间，

+   我们可以调整代理的工作时间表，以便在周末和晚间时段提供覆盖。

既然我们现在有了模型，我们可以轻松地估算所有机会并选择最可行的方案。

前两种方法很简单。接下来我们讨论如何调整代理的工作时间安排。我们所有的代理都在周一至周五，从9点到18点工作。我们尝试让他们的覆盖时间分布更均匀一些。

首先，我们可以覆盖更早和更晚的时间段，将代理分为两组。一组的工作时间是从7点到16点，另一组是从11点到20点。

其次，我们可以更加均匀地分配他们的工作日。我使用了一种相当直接的方法。

![](../Images/58d97ada596f273dd5961143534c44e9.png)

实际上，你可以进一步减少周末的代理人数，因为我们在周末的需求要少得多。这可以进一步改善你的指标。然而，额外的效果会是边际性的。

如果我们对所有这些情境进行仿真，令人惊讶的是，如果我们只是调整代理的工作时间，KPI会有显著的提升。如果我们再雇佣五个员工或提高代理的绩效30%，也无法取得如此显著的改善。

![](../Images/feab62766dc8914d050a73e643a049f1.png)

让我们看看代理工作时间的变化如何影响我们的关键绩效指标（KPI）。分辨时间只会在非工作时间（从20点到7点）增长，队列大小也永远不会超过200个案件。

![](../Images/60dc8a0dd103e62122945661f028ea08.png)![](../Images/ec4402a2bc799068f60099f32244c5d2.png)

这是一个非常好的结果。我们的仿真模型帮助我们优先考虑运营变更，而不是雇佣更多员工或投资于大语言模型工具的开发。

我们在本文中讨论了这种方法的基本原理。如果你想深入了解并将其应用于实践，这里有一些可能有用的建议：

+   在开始将这些模型投入生产之前，值得先进行测试。最简单的方法是模拟你当前的状况，并比较主要KPI。如果它们相差很大，那么你的系统并没有很好地反映现实世界，你需要在决策前使其更加准确。

+   当前的指标是以客户为中心的。我使用了平均解决时间作为主要KPI来做决策。在商业中，我们还关心成本。因此，从运营角度来看这个问题也是值得的，也就是说，衡量代理没有任务可做的时间百分比（这意味着我们在为他们支付薪水，但他们什么都不做）。

+   在现实生活中，可能会出现突发情况（例如，由于产品中的一个bug，客户请求数量翻倍），因此我建议你使用这些模型来确保你的客服团队能够应对这种情况。

+   最后但同样重要的是，我使用的模型完全是确定性的（每次运行返回相同的结果），因为处理时间是为每个客户请求定义的。为了更好地理解指标的变动性，您可以为每个代理指定处理时间的分布（取决于任务类型、星期几等），并在每次迭代中从该分布中获取处理时间。然后，您可以多次运行仿真并计算您的指标的置信区间。

# 摘要

那么，让我们简要总结一下今天讨论的要点：

+   我们已经学习了离散事件仿真方法的基础，这种方法有助于模拟具有可计数事件的离散系统。

+   我们已经复习了Python中的面向对象编程和类，因为这种范式比数据分析师通常使用的常规过程化代码更适合此任务。

+   我们已经构建了CS团队的模型，并能够估算不同潜在改进对我们的KPI（解决时间和队列大小）的影响。

> 非常感谢您阅读本文。如果您有任何后续问题或评论，请在评论区留言。

# 参考

*除非另有说明，否则所有图片均由作者制作。*
