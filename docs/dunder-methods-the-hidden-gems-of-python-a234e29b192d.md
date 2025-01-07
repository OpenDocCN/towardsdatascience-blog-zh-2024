# 双下划线方法：Python 的隐藏宝石

> 原文：[https://towardsdatascience.com/dunder-methods-the-hidden-gems-of-python-a234e29b192d?source=collection_archive---------0-----------------------#2024-11-30](https://towardsdatascience.com/dunder-methods-the-hidden-gems-of-python-a234e29b192d?source=collection_archive---------0-----------------------#2024-11-30)

## 通过实际例子展示如何主动使用特殊方法可以简化编码并提高可读性。

[](https://medium.com/@federico.zabeo29?source=post_page---byline--a234e29b192d--------------------------------)[![Federico Zabeo](../Images/387bdad1380d507c9ace1cbea24a694c.png)](https://medium.com/@federico.zabeo29?source=post_page---byline--a234e29b192d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a234e29b192d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a234e29b192d--------------------------------) [Federico Zabeo](https://medium.com/@federico.zabeo29?source=post_page---byline--a234e29b192d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a234e29b192d--------------------------------) ·阅读时长 8 分钟·2024 年 11 月 30 日

--

双下划线方法，尽管可能是 Python 中的基础话题，但我常常注意到，即使是一些编程已经有相当经验的人，也常常只对其有表面的理解。

**免责声明：** 这是一个可以原谅的疏漏，因为在大多数情况下，主动使用双下划线方法（dunder methods）“简单地”加速和规范了那些本可以用其他方式完成的任务。即使它们的使用是必不可少的，程序员们也常常未意识到自己正在编写属于双下划线方法这一广泛类别的特殊方法。

无论如何，如果你在 Python 中编程并且不熟悉这个话题，或者你恰好是像我一样对编程语言更原生的方面感兴趣的代码极客，这篇文章可能正是你在寻找的内容。

## **表面现象往往能欺骗你……即使是在 Python 中！**

如果我在生活中学到了一件事，那就是并非一切如初看时所见，Python 也不例外。

![](../Images/07c8e26ee7f55de29278f00feb2ff468.png)

图片由 [Robert Katzki](https://unsplash.com/@ro_ka?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

让我们考虑一个看似简单的例子：

```py
class EmptyClass:
    pass
```

这是我们在 Python 中可以定义的“最空的”自定义类，因为我们没有定义任何属性或方法。它空得让你以为根本无法做任何事情。

然而，事实并非如此。例如，Python 并不会抱怨你尝试创建这个类的实例，甚至也不会在比较两个实例是否相等时出错：

```py
empty_instance = EmptyClass()
another_empty_instance = EmptyClass()

>>> empty_instance == another_empty_instance
False
```

当然，这不是魔法。简单来说，通过利用一个标准的 ***object*** 接口，Python 中的任何对象都会继承一些默认的属性和方法，这些方法允许用户始终与其进行一组最小的交互。

尽管这些方法看起来可能隐藏，但它们并不是不可见的。要访问可用的方法，包括 Python 本身分配的那些方法，只需使用内建函数 ***dir()***。对于我们的空类，我们得到：

```py
>>> dir(EmptyClass)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', 
'__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', 
'__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', 
'__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', 
'__str__', '__subclasshook__', '__weakref__']
```

正是这些方法能够解释我们之前观察到的行为。例如，由于该类实际上具有 ***__init__*** 方法，因此我们不应感到惊讶，能够实例化该类的对象。

## **认识 dunder 方法**

上一个输出中显示的所有方法都属于特殊的——猜猜看——dunder 方法。术语“dunder”是“double underscore”（双下划线）的缩写，指的是这些方法名称前后各有的双下划线。

它们之所以特殊，有几个原因：

1.  **它们被内建到每个对象中**：每个 Python 对象都配备了一组特定的 dunder 方法，这些方法由其类型决定。

1.  **它们是隐式调用的**：许多 dunder 方法会通过与 Python 的原生运算符或内建函数的交互自动触发。例如，用 ***==*** 比较两个对象等同于调用它们的 ***__eq__*** 方法。

1.  **它们是可定制的**：你可以重写现有的 dunder 方法，或者为你的类定义新的方法，以便为它们提供自定义行为，同时保持它们的隐式调用。

对于大多数 Python 开发者来说，遇到的第一个 dunder 方法是 ***__init__***，即构造方法。这个方法在你创建类的实例时自动调用，使用熟悉的语法 ***MyClass(*args, **kwargs)*** 来简化显式调用 ***MyClass.__init__(*args, **kwargs)***。

尽管是最常用的，***__init__*** 也是最具专业性的 dunder 方法之一。它并未完全展示 dunder 方法的灵活性和强大功能，因为 dunder 方法可以让你重新定义对象与 Python 本身功能的交互方式。

## 使对象更美观

让我们定义一个表示商店中待售物品的类，并通过指定名称和价格来创建一个实例。

```py
class Item:
    def __init__(self, name: str, price: float) -> None:
        self.name = name
        self.price = price

item = Item(name="Milk (1L)", price=0.99)
```

如果我们尝试显示 *item* 变量的内容，会发生什么？现在，Python 能做到的最好效果就是告诉我们它是什么类型的对象以及它在内存中的位置：

```py
>>> item
<__main__.Item at 0x00000226C614E870>
```

让我们尝试获取更具信息性和更漂亮的输出！

![](../Images/fd7e5e5454f250598838a4907fdd0424.png)

图片由 [Shamblen Studios](https://unsplash.com/@shamblenstudios?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

为了做到这一点，我们可以重写 ***__repr__*** dunder 方法，其输出将正是当在交互式 Python 控制台中输入类实例时打印的内容，但同样——一旦其他 dunder 方法 ***__str__*** 未被重写——当调用 ***print()*** 时也会发生同样的情况。

**注意**：通常的做法是让***__repr__***提供必要的语法来重新创建打印的实例。因此，在后者的情况下，我们期望输出为*Item(name="Milk (1L)", price=0.99)*。

```py
class Item:
    def __init__(self, name: str, price: float) -> None:
        self.name = name
        self.price = price

    def __repr__(self) -> str:
        return f"{self.__class__.__name__}('{self.name}', {self.price})"

item = Item(name="Milk (1L)", price=0.99)

>>> item # In this example it is equivalent also to the command: print(item)
Item('Milk (1L)', 0.99)
```

没什么特别的，对吧？你说得对：我们本可以实现相同的方法并将其命名为*my_custom_repr*，而无需使用双下划线方法。然而，虽然每个人都能立刻理解我们通过***print(item)***或仅仅***item***表示的意思，但我们能否说出类似***item.my_custom_repr()***的东西呢？

**定义对象与Python原生运算符之间的交互**

假设我们想要创建一个新的类*Grocery*，它允许我们构建一个*Item*的集合以及它们的数量。

在这种情况下，我们可以使用双下划线方法来允许一些标准操作，如：

1.  使用***+***运算符向*Grocery*中添加特定数量的*Item*

1.  使用***for***循环直接迭代*Grocery*类

1.  使用括号***[]***符号从Grocery类中访问特定的Item

为了实现这一点，我们将**定义**（我们已经看到，通用类默认没有这些方法）双下划线方法***__add__***、***__iter__***和***__getitem__***。

```py
from typing import Optional, Iterator
from typing_extensions import Self

class Grocery:

    def __init__(self, items: Optional[dict[Item, int]] = None):
        self.items = items or dict()

    def __add__(self, new_items: dict[Item, int]) -> Self:

        new_grocery = Grocery(items=self.items)

        for new_item, quantity in new_items.items():

            if new_item in new_grocery.items:
                new_grocery.items[new_item] += quantity
            else:
                new_grocery.items[new_item] = quantity

        return new_grocery

    def __iter__(self) -> Iterator[Item]:
        return iter(self.items)

    def __getitem__(self, item: Item) -> int:

        if self.items.get(item):
            return self.items.get(item)
        else:
            raise KeyError(f"Item {item} not in the grocery")
```

让我们初始化一个*Grocery*实例并打印其主要属性*items*的内容。

```py
item = Item(name="Milk (1L)", price=0.99)
grocery = Grocery(items={item: 3})

>>> print(grocery.items)
{Item('Milk (1L)', 0.99): 3}
```

然后，我们使用***+***运算符添加一个新的Item，并验证更改已生效。

```py
new_item = Item(name="Soy Sauce (0.375L)", price=1.99)
grocery = grocery + {new_item: 1} + {item: 2}

>>> print(grocery.items)
{Item('Milk (1L)', 0.99): 5, Item('Soy Sauce (0.375L)', 1.99): 1}
```

友好且明确，对吧？

***__iter__***方法允许我们根据方法中实现的逻辑循环遍历*Grocery*对象（即，隐式地，循环将迭代包含在可迭代属性*items*中的元素）。

```py
>>> print([item for item in grocery])
[Item('Milk (1L)', 0.99), Item('Soy Sauce (0.375L)', 1.99)]
```

同样，访问元素是通过定义***__getitem__***双下划线方法来处理的：

```py
>>> grocery[new_item]
1

fake_item = Item("Creamy Cheese (500g)", 2.99)
>>> grocery[fake_item]
KeyError: "Item Item('Creamy Cheese (500g)', 2.99) not in the grocery"
```

本质上，我们为Grocery类分配了一些标准的字典般行为，同时还允许了一些此数据类型本不具备的操作。

**增强功能：使类可调用以简化和增强功能。**

让我们通过一个最终示例来总结这个深入探讨的双下划线方法，展示它们如何成为我们工具库中的强大工具。

![](../Images/98a9fbb88437116ee9565a35777a3ad6.png)

图片由[Marek Studzinski](https://unsplash.com/@jccards?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

假设我们实现了一个根据特定输入执行确定性和缓慢计算的函数。为了简化，我们将使用一个带有内置***time.sleep***几秒钟的恒等函数作为示例。

```py
import time 

def expensive_function(input):
    time.sleep(5)
    return input
```

如果我们对相同的输入运行两次该函数会发生什么？好吧，现在计算会执行两次，这意味着我们要等待整个执行时间（即总共10秒）才能得到两次相同的输出。

```py
start_time = time.time()

>>> print(expensive_function(2))
>>> print(expensive_function(2))
>>> print(f"Time for computation: {round(time.time()-start_time, 1)} seconds")
2
2
Time for computation: 10.0 seconds
```

这样说有道理吗？为什么我们要对相同的输入执行相同的计算（并得到相同的输出），尤其是当它是一个缓慢的过程时？

一种可能的解决方案是将此函数的执行“包装”在类的 ***__call__*** 双下划线方法中。

这使得类的实例可以像函数一样调用——意味着我们可以使用简单的语法 ***my_class_instance(*args, **kwargs)*** ——同时也允许我们使用属性作为缓存，从而减少计算时间。

采用这种方法，我们还可以灵活地创建多个进程（即类实例），每个进程都有自己的本地缓存。

```py
class CachedExpensiveFunction:

    def __init__(self) -> None:
        self.cache = dict()

    def __call__(self, input):
        if input not in self.cache:
            output = expensive_function(input=input)
            self.cache[input] = output
            return output
        else:
            return self.cache.get(input)

start_time = time.time()
cached_exp_func = CachedExpensiveFunction()

>>> print(cached_exp_func(2))
>>> print(cached_exp_func(2))
>>> print(f"Time for computation: {round(time.time()-start_time, 1)} seconds")
2
2
Time for computation: 5.0 seconds
```

正如预期的那样，函数在第一次运行后被缓存，消除了第二次计算的需求，从而将整体时间减少了一半。

如前所述，我们甚至可以在需要时创建该类的独立实例，每个实例都有自己的缓存。

```py
start_time = time.time()
another_cached_exp_func = CachedExpensiveFunction()

>>> print(cached_exp_func(3))
>>> print(another_cached_exp_func (3))
>>> print(f"Time for computation: {round(time.time()-start_time, 1)} seconds")
3
3
Time for computation: 10.0 seconds
```

我们到了！这是一个简单而强大的优化技巧，通过双下划线方法（dunder methods）实现，不仅减少了冗余计算，还通过允许本地实例特定的缓存提供了灵活性。

## 我的最终思考

双下划线方法是一个广泛且不断发展的主题，这篇文章并不旨在成为该主题的详尽资料（如果需要，您可以参考[3. 数据模型 — Python 3.12.3 文档](https://docs.python.org/3/reference/datamodel.html)）。

我在这里的目标是清晰地解释它们是什么，以及如何有效地使用它们来处理一些常见的使用场景。

虽然它们对于所有程序员来说并非总是必需的，但一旦我掌握了它们的工作原理，它们对我产生了极大的帮助，并且希望它们也能对你有所帮助。

双下划线方法的确是一种避免重新发明轮子的方式。它们也与 Python 的哲学紧密契合，从而导致更简洁、可读且符合约定的代码。而这一点永远不会有坏处，对吧？
