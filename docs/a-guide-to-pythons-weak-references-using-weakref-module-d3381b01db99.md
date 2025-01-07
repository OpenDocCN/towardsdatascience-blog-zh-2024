# 使用 weakref 模块深入了解 Python 的弱引用

> 原文：[`towardsdatascience.com/a-guide-to-pythons-weak-references-using-weakref-module-d3381b01db99?source=collection_archive---------6-----------------------#2024-06-25`](https://towardsdatascience.com/a-guide-to-pythons-weak-references-using-weakref-module-d3381b01db99?source=collection_archive---------6-----------------------#2024-06-25)

## 了解 Python 中弱引用的所有内容：引用计数、垃圾回收以及 `weakref` 模块的实际应用

[](https://medium.com/@martin.heinz?source=post_page---byline--d3381b01db99--------------------------------)![Martin Heinz](https://medium.com/@martin.heinz?source=post_page---byline--d3381b01db99--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d3381b01db99--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3381b01db99--------------------------------) [Martin Heinz](https://medium.com/@martin.heinz?source=post_page---byline--d3381b01db99--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3381b01db99--------------------------------) ·阅读时间：6 分钟·2024 年 6 月 25 日

--

![](img/85fbf17d8afe2376e51b58139e0a9847.png)

图片来源：[Dan Cristian Pădureț](https://unsplash.com/@dancristianpaduret?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/blue-and-white-abstract-painting-SMSLyc9FHl0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

你可能从未接触过，甚至可能没听说过 Python 的 `weakref` 模块。尽管它在你的代码中可能不常用，但它对许多库、框架甚至 Python 本身的内部工作至关重要。因此，在这篇文章中，我们将探索它是什么，它如何有用，以及你如何将它应用到你的代码中。

# 基础知识

要理解 `weakref` 模块和*弱引用*，我们首先需要简单了解 Python 中的垃圾回收机制。

Python 使用 *引用计数* 作为垃圾回收的机制——简单来说——Python 为我们创建的每个对象保持一个引用计数，当代码中引用该对象时，引用计数会增加；当对象被取消引用时（例如变量设置为 `None`），引用计数会减少。如果引用计数降到零，内存将被回收（垃圾回收）。

让我们通过一些代码来更好地理解它：

```py
import sys

class SomeObject:
    def __del__(self):
        print(f"(Deleting {self=})")

obj = SomeObject()

print(sys.getrefcount(obj))  # 2

obj2 = obj
print(sys.getrefcount(obj))  # 3

obj = None
obj2 = None

# (Deleting self=<__main__.SomeObject object at 0x7d303fee7e80>)
```

在这里，我们定义了一个只实现 `__del__` 方法的类，该方法在对象被垃圾回收（GC）时调用——我们这样做是为了看到垃圾回收发生的时刻。

在创建了这个类的实例后，我们使用`sys.getrefcount`来获取当前对该对象的引用数。我们本来会期望得到`1`，但`getrefcount`返回的计数通常比预期的要高 1，因为当我们调用`getrefcount`时，引用会按值复制到函数的参数中，从而暂时增加对象的引用计数。

接下来，如果我们声明`obj2 = obj`并再次调用`getrefcount`，我们会得到`3`，因为它现在被`obj`和`obj2`两个变量引用。相反，如果我们将`None`赋值给这些变量，引用计数将减少到零，最终我们将通过`__del__`方法收到对象被垃圾回收的消息。

那么，弱引用如何适应这一点呢？如果对象的唯一剩余引用是*弱引用*，那么 Python 解释器就可以自由地回收这个对象。换句话说——对一个对象的弱引用不足以保持该对象的存活：

```py
import weakref

obj = SomeObject()

reference = weakref.ref(obj)

print(reference)  # <weakref at 0x734b0a514590; to 'SomeObject' at 0x734b0a4e7700>
print(reference())  # <__main__.SomeObject object at 0x707038c0b700>
print(obj.__weakref__)  # <weakref at 0x734b0a514590; to 'SomeObject' at 0x734b0a4e7700>

print(sys.getrefcount(obj))  # 2

obj = None

# (Deleting self=<__main__.SomeObject object at 0x70744d42b700>)

print(reference)  # <weakref at 0x7988e2d70590; dead>
print(reference())  # None
```

这里我们再次声明了一个`obj`变量，作为我们类的实例，但这次我们并没有为这个对象创建第二个强引用，而是在`reference`变量中创建了弱引用。

如果我们检查引用计数，我们可以看到它没有增加，并且如果我们将`obj`变量设置为`None`，我们可以看到它会立即被垃圾回收，尽管弱引用仍然存在。

最后，如果尝试访问已经被垃圾回收的对象的弱引用，我们将得到一个*“死”*引用，返回值为`None`。

同时请注意，当我们使用弱引用访问对象时，必须将其作为函数调用（`reference()`）来检索对象。因此，通常使用*代理*会更加方便，特别是当你需要访问对象属性时：

```py
obj = SomeObject()

reference = weakref.proxy(obj)

print(reference)  # <__main__.SomeObject object at 0x78a420e6b700>

obj.attr = 1
print(reference.attr)  # 1
```

# 何时使用弱引用

既然我们已经了解了弱引用是如何工作的，接下来我们来看看它们如何能派上用场。

弱引用的一个常见用例是树状数据结构：

```py
class Node:
    def __init__(self, value):
        self.value = value
        self._parent = None
        self.children = []

    def __repr__(self):
        return "Node({!r:})".format(self.value)

    @property
    def parent(self):
        return self._parent if self._parent is None else self._parent()

    @parent.setter
    def parent(self, node):
        self._parent = weakref.ref(node)

    def add_child(self, child):
        self.children.append(child)
        child.parent = self

root = Node("parent")
n = Node("child")
root.add_child(n)
print(n.parent)  # Node('parent')

del root
print(n.parent)  # None
```

在这里，我们使用`Node`类实现了一个树，其中子节点对父节点有弱引用。在这种关系中，子`Node`可以在没有父`Node`的情况下存在，这允许父节点在没有任何警告的情况下被删除/垃圾回收。

或者，我们可以反过来做：

```py
class Node:
    def __init__(self, value):
        self.value = value
        self._children = weakref.WeakValueDictionary()

    @property
    def children(self):
        return list(self._children.items())

    def add_child(self, key, child):
        self._children[key] = child

root = Node("parent")
n1 = Node("child one")
n2 = Node("child two")
root.add_child("n1", n1)
root.add_child("n2", n2)
print(root.children)  # [('n1', Node('child one')), ('n2', Node('child two'))]

del n1
print(root.children)  # [('n2', Node('child two'))]
```

在这里，相反地，父节点维护了一个子节点的弱引用字典。这使用了`WeakValueDictionary`——每当字典中引用的元素（弱引用）在程序的其他地方被取消引用时，它会自动从字典中移除，因此我们不需要管理字典项的生命周期。

`weakref`的另一个用途是在[*观察者*设计模式](https://en.wikipedia.org/wiki/Observer_pattern)中：

```py
class Observable:
    def __init__(self):
        self._observers = weakref.WeakSet()

    def register_observer(self, obs):
        self._observers.add(obs)

    def notify_observers(self, *args, **kwargs):
        for obs in self._observers:
            obs.notify(self, *args, **kwargs)

class Observer:
    def __init__(self, observable):
        observable.register_observer(self)

    def notify(self, observable, *args, **kwargs):
        print("Got", args, kwargs, "From", observable)

subject = Observable()
observer = Observer(subject)
subject.notify_observers("test", kw="python")
# Got ('test',) {'kw': 'python'} From <__main__.Observable object at 0x757957b892d0>
```

`Observable`类保持对其观察者的弱引用，因为它不关心它们是否被移除。和之前的例子一样，这避免了管理依赖对象生命周期的需要。正如你可能注意到的，在这个例子中我们使用了`WeakSet`，它是`weakref`模块中的另一个类，行为与`WeakValueDictionary`类似，但它是通过`Set`实现的。

本部分的最后一个例子来自于`[weakref](https://docs.python.org/3/library/weakref.html#comparing-finalizers-with-del-methods)`文档：[docs](https://docs.python.org/3/library/weakref.html#comparing-finalizers-with-del-methods)：

```py
import tempfile, shutil
from pathlib import Path

class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()
        self._finalizer = weakref.finalize(self, shutil.rmtree, self.name)

    def __repr__(self):
        return "TempDir({!r:})".format(self.name)

    def remove(self):
        self._finalizer()

    @property
    def removed(self):
        return not self._finalizer.alive

tmp = TempDir()
print(tmp)  # TempDir('/tmp/tmp8o0aecl3')
print(tmp.removed)  # False
print(Path(tmp.name).is_dir()) # True
```

这展示了`weakref`模块的另一个特性，即`weakref.finalize`。顾名思义，它允许在相关对象被垃圾回收时执行一个终结函数/回调。在这个例子中，我们实现了一个`TempDir`类，用于创建临时目录——理想情况下，我们总是会记得在不需要`TempDir`时清理它，但如果忘记了，终结器会在`TempDir`对象被垃圾回收时自动执行`rmtree`，这包括程序完全退出时。

# 现实世界中的例子

前一部分展示了几个`weakref`的实际用途，但我们也来看看一些现实世界中的例子——其中之一是创建缓存实例：

```py
import logging
a = logging.getLogger("first")
b = logging.getLogger("second")
print(a is b)  # False

c = logging.getLogger("first")
print(a is c)  # True
```

上面展示的是 Python 内建`logging`模块的基本用法——我们可以看到，它允许只将一个日志实例与给定名称关联——这意味着当我们多次获取相同的日志器时，它始终返回相同的缓存日志实例。

如果我们想实现这一点，可能会是这样的：

```py
class Logger:
    def __init__(self, name):
        self.name = name

_logger_cache = weakref.WeakValueDictionary()

def get_logger(name):
    if name not in _logger_cache:
        l = Logger(name)
        _logger_cache[name] = l
    else:
        l = _logger_cache[name]
    return l

a = get_logger("first")
b = get_logger("second")
print(a is b)  # False

c = get_logger("first")
print(a is c)  # True
```

最后，Python 本身也使用了弱引用，例如在`OrderedDict`的实现中：

```py
from _weakref import proxy as _proxy

class OrderedDict(dict):

    def __new__(cls, /, *args, **kwds):
        self = dict.__new__(cls)
        self.__hardroot = _Link()
        self.__root = root = _proxy(self.__hardroot)
        root.prev = root.next = root
        self.__map = {}
        return self
```

上面是来自[CPython](https://github.com/python/cpython/blob/0c0348adbfca991f78b3aaa6790e5c26606a1c0f/Lib/collections/__init__.py#L103)的`[collections](https://github.com/python/cpython/blob/0c0348adbfca991f78b3aaa6790e5c26606a1c0f/Lib/collections/__init__.py#L103)`[模块](https://github.com/python/cpython/blob/0c0348adbfca991f78b3aaa6790e5c26606a1c0f/Lib/collections/__init__.py#L103)的代码片段。在这里，`weakref.proxy`用于防止循环引用（更多细节请参见文档字符串）。

# 结论

`weakref`是一个相当晦涩但在某些时候非常有用的工具，你应该将其保留在你的工具箱中。当实现缓存或包含引用循环的数据结构时，它非常有帮助，比如双向链表。

话虽如此，应该注意到`weakref`的支持——这里以及文档中提到的内容是针对 CPython 的，其他 Python 实现会有不同的`weakref`行为。此外，许多内建类型不支持弱引用，例如`list`、`tuple`或`int`。

*本文最初发布于* [*martinheinz.dev*](https://martinheinz.dev/blog/112)

你可能还会喜欢……

[](/everything-you-can-do-with-pythons-textwrap-module-0d82c377a4c8?source=post_page-----d3381b01db99--------------------------------) ## 你可以用 Python 的 textwrap 模块做什么

### 了解你可以使用 Python 的 textwrap 模块完成的所有操作，包括格式化、文本换行、修剪等…

towardsdatascience.com
