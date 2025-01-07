# 超级增强的 Pandas：用新颖的方法追踪依赖关系

> 原文：[`towardsdatascience.com/supercharged-pandas-tracing-dependencies-with-a-novel-approach-120b9567f098?source=collection_archive---------5-----------------------#2024-03-11`](https://towardsdatascience.com/supercharged-pandas-tracing-dependencies-with-a-novel-approach-120b9567f098?source=collection_archive---------5-----------------------#2024-03-11)

## 一种面向对象的方法，用于管理多个文件和数据框，以及追踪依赖关系。

[](https://jiweiliew.medium.com/?source=post_page---byline--120b9567f098--------------------------------)![Ji Wei Liew](https://jiweiliew.medium.com/?source=post_page---byline--120b9567f098--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--120b9567f098--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--120b9567f098--------------------------------) [Ji Wei Liew](https://jiweiliew.medium.com/?source=post_page---byline--120b9567f098--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--120b9567f098--------------------------------) ·阅读时长 7 分钟·2024 年 3 月 11 日

--

![](img/ffb40d7bb751829cffac88c7ba2def2c.png)

图片由[Sibel Yıldırım](https://unsplash.com/@sibelle?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/red-maple-leaf-and-eyeglasses-with-black-frames-on-white-bookpage-J6YInqMXD6c?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 这将如何对你有所帮助：

本文描述了一种面向对象的数据分析方法。它概述了两种新颖的方法：（a）通过将数据框分配给`Reports`对象的属性，减少重复的文件读取，和（b）递归地追踪依赖方法以构建属性。这些方法让我在工作中非常高效，希望你也能获得类似的好处。

## *适合谁阅读：*

+   你需要长时间分析相同的数据集。

+   你需要通过将不同来源的数据结合起来建立报告并准备统计数据。

+   你的同事们经常问你：“你是如何得到这些数据的？”而你无法回忆起你在 Excel 中为了准备报告而做的 N 步操作。

+   你已经使用 pandas 一段时间了，并且怀疑有一种更高效的做事方式。

## 本文内容包括：

+   单体脚本：它是如何开始的

+   可复用的函数：它是如何发展的

+   对象、方法和属性：它是如何发展的

+   追踪上游依赖关系：一种新颖的方法

## 前言

我尝试做的事情很难解释，所以如果本文的前半部分不太通顺，请耐心等待。我保证，到了后面，所有的内容都会变得值得。

# 单体脚本：它是如何开始的

假设你有 3 个 csv 文件：`file1.csv`、`file2.csv`、`file3.csv`。你编写了一些代码来读取它们中的每一个，然后按特定顺序将它们合并。

```py
df1 = pd.read_csv('file1.csv')
df2 = pd.read_csv('file2.csv')
df3 = pd.read_csv('file3.csv')

df1_2 = pd.merge(df1, df2, on='a', how='left')
df1_2_3 = pd.merge(df1_2, df3, on='b', how='inner')
```

这非常完美，你继续着自己的工作。接着，老板给了你`file4.csv`，这个文件需要与`file1.csv`合并，生成一个单独的报告。没问题，你知道该怎么做，更新了代码：

```py
df1 = pd.read_csv('file1.csv')
df2 = pd.read_csv('file2.csv')
df3 = pd.read_csv('file3.csv')
df4 = pd.read_csv('file4.csv')

df1_2 = pd.merge(df1, df2, on='a', how='left')
df1_2_3 = pd.merge(df1_2, df3, on='b', how='inner')
df1_4 = pd.merge(df1, df4, on='a', how='left')
```

代码顺利运行，你得到了预期的输出。老板拍了拍你的背，开玩笑地说：“这么快，你能更快一点吗？”

你抬头看着老板，但眼前只看见一连串的脏话在闪过。你努力控制自己内心的冲动，决定不让自己爆发出来，而是战胜了本能，聚集起所有的伪善能量，假装微笑并愉快地回答：“当然，给我个机会。”

随着一阵脏话渐行渐远，随着你耗尽所有的能量，你注意到一线希望：如果你只对`df1_4`感兴趣，那么就不需要读取`file2.csv`和`file3.csv`。你突然意识到，这种对宝贵时间和计算能力的挥霍，与自己对可持续性的承诺相悖，开始思考如何通过只读取必要的数据来提高代码的效率。

# 可复用的函数：它是如何发展的

你回忆起几年前参加的编程课程，并开始编写一些函数：

```py
files = {1: 'file1.csv', 2: 'file2.csv', 3:'file3.csv', 4:'file4.csv'}

def get_df(x):
    return pd.read_csv(files[x])

def get_df1_2():
    df1 = get_df(1)
    df2 = get_df(2)
    return pd.merge(df1, df2, on='a', how='left')  

def get_df1_2_3():
    df1_2 = get_df1_2()
    df3 = get_df(3)
    return pd.merge(df1_2, df3, on='b', how='inner')

def get_df1_4():
    df1 = get_df(1)
    df4 = get_df(4)
    return pd.merge(df1, df4, on='a', how='left')
```

你为自己感到高兴。虽然代码行数已经翻倍，但你安慰自己，从长远来看，这样更易于管理。此外，你为这种方法辩护，因为你可以获取特定的输出数据框，每个数据框只会读取所需的表格，其他的什么都不读。你感到一阵寒意袭来，内心的声音开始质疑你的思维。“你确定吗？”它用命令的语气大声质问道，听起来像个军训教官。空气中弥漫着一阵沉默，除了你脑海中想象中的齿轮转动声，什么都听不见……突然，你的眼睛一亮，意识到，如果你需要`df1_2`和`df1_4`，那么`file1.csv`将被读取两次！轰！

# 对象、方法和属性：它是如何演变的

再一次，你回忆起大学里的编程课程，记起你可以通过创建一个`Reports`对象来解决这个问题。当读取完一个数据框后，可以将其设置为`Reports`对象的一个属性，以便后续访问。

```py
files = {1: 'file1.csv', 2: 'file2.csv', 3:'file3.csv', 4:'file4.csv'}

class Reports:

    def __init__(self):
        self.df1 = pd.read_csv(files[1])

    def get_df1_2(self):
        self.df2 = pd.read_csv(files[2])
        self.df1_2 = pd.merge(self.df1, self.df2, on='a', how='left')
        return self.df1_2

    def get_df1_4(self):
        self.df4 = pd.read_csv(files[4])
        self.df1_4 = pd.merge(self.df1, self.df4, on='a', how='left')

    def get_df1_2_3(self):
        self.get_df1_2()  
        self.df3 = pd.read_csv(files[3])
        self.df1_2_3 = pd.merge(self.df1_2, self.df3, on='b', how='inner')
```

哇！你解决了多次读取同一文件的问题。但还有另一个问题：`get_df1_2_3`如果需要经过许多步骤（例如过滤、选择、布尔掩码、去重等），可能会变得非常复杂。

# 追踪上游依赖：一种新方法

你深深地吸了一口气，心想……代码能否搞明白，如果`self.df1_2`没有被设置，那么它应该调用`self.get_df1_2()`？更普遍地说，当访问的属性不存在时，能否识别出哪个方法负责设置它，然后调用这个方法？如果能做到这一点，那么你可以通过`x=Reports(); x.df1_2_3`在一个命令中得到所需的数据框。

> 这难道不值得为之奋斗吗？这难道不值得为之牺牲吗？——摩菲斯，《黑客帝国：重装上阵》，2003 年

就像一个疯狂的科学家在工作，你开始猛击键盘，偶尔抬头，用手指在空中画出编程抽象图，并将它们连接起来。从眼角的余光中，你注意到一个你从未见过的同事露出困惑的表情——或者可能是厌恶的表情，但你无法确定。你把所有的注意力都集中在进入心流状态，完全忽视了周围发生的一切。大楼可能着火了，但只要你可靠的 Notepad++继续显示你输入的每一个键，你根本不会察觉。

```py
files = {'df1': 'file1.csv', 'df2': 'file2.csv',
         'df3': 'file3.csv', 'df4': 'file4.csv'}

class Reports:

    def __init__(self):
        self._build_shortcuts()

    def _read(self, k):
        setattr(self, k, pd.read_csv(files[k]))  

    def _build_shortcuts(self):
        # Dict: Method -> list of attributes
        dict0 = {'get_df1_2': ['df1_2'],
                 'get_df1_4': ['df1_4'],
                 'get_df1_2_3': ['df1_2_3']}

        # Dict: Attribute -> method which creates the attribute
        dict1 = {v:k for k, values in dict0.items() for v in values}
        self._shortcuts = dict1

    def __getattr__(self, attr):
        if not attr in self.__dict__: # if the attr has not been created...
            if attr in self._shortcuts: 
                func = self._shortcuts[attr] 
                # `func` is the method responsible for creating attr
                self.__getattribute__(func)()
                return self.__getattribute__(attr)
            elif attr in files:
                self._read(attr)
                return self.__getattribute__(attr)
            else:
                raise AttributeError
        else:
            return self.__getattribute__(attr)

    def get_df1_2(self):
        self.df1_2 = pd.merge(self.df1, self.df2, on='a', how='left')
        return self.df1_2

    def get_df1_4(self):
        self.df1_4 = pd.merge(self.df1, self.df4, on='a', how='left')
        return self.df1_4

    def get_df1_2_3(self):
        self.df1_2_3 = pd.merge(self.df1_2, self.df3, on='b', how='inner')
        return self.df1_2_3
```

你停下来片刻，欣赏自己的创作，它的优雅与简洁。在短短的一瞬间，你想象着这将如何造福程序员和数据分析师。当你在愉悦的热气球上飞翔时，一个内心的声音像镣铐一样降临在你身上。“保持脚踏实地，”他说，“因为你可能不是第一个想到这个主意的人。”你收拾心情，开始记录你的工作，意识到几天后你可能会不明白自己写的是什么。

`__init__()` 不读取文件。它只是调用 `build_shortcuts()`。

+   `_build_shortcuts()` 和 `__getattr__` 密切配合，简化了后续方法中的代码。

+   `_build_shortcuts()`接受一个字典，字典的键是方法，值是属性的列表，然后将其反转，形成一个字典，字典的键是属性，值是设置属性的方法。

+   `__getattr__` 做了不少“魔法”。当调用 `self.<attr>` 时，如果 `attr` 不在 `self.__dict__` 中，但在 `self._shortcuts` 中，那么它会识别出负责创建 `self.<attr>` 的方法并调用它。每当你创建一个新方法，如果它设置了一个新属性，那么你只需在 `self._build_shortcuts()` 中更新 `dict0`。如果它在 `files` 字典的键中，那么它会读取相应的文件并将键设置为 `Reports` 对象的属性。

+   在没有显式编写循环或递归的情况下，`__getattr__` 和 `self._shortcuts` 协同工作，追踪上游依赖关系！

目前，这种方法有以下几个优点：

+   只有在绝对需要时，才会读取文件，浪费的时间最少。

+   当文件被读取时，它们只会被读取一次，并且数据会写入属性中。

+   当调用一个属性时，如果它没有被创建，系统会寻找负责设置该属性的方法，然后进行设置。

# 额外的好处

除了可以通过一个命令访问所需的数据框外，你还可以在`_build_shortcuts()`中将其他属性[1]添加到`dict0`的值中。

例如，你可能对`df1_2`中列`a`的唯一值列表感兴趣。只需将其添加到列表中，然后你可以使用`x = Reports(); x.unique_values_in_a`。

```py
 ...

    def _build_shortcuts(self):

        # Added 'unique_items_in_a' to the first list.
        dict0 = {'get_df1_2': ['df1_2', 'unique_values_in_a'],
                 'get_df1_4': ['df1_4'],
                 'get_df1_2_3': ['df1_2_3']}

        dict1 = {v:k for k, values in dict0.items() for v in values}
        self._shortcuts = dict1
    ...

    def get_df1_2(self):
        self.df1_2 = pd.merge(self.df1, self.df2, on='a', how='left')
        # Added the list of unique values of column 'a'
        self.unique_values_in_a = self.df1_2['a'].unique().tolist()
        return self.df1_2
```

# 结论

## 这对你意味着什么？

我强烈建议你在下次需要分析涉及多个数据框的数据时尝试这种方法。

+   对于 Python 新手，你只需复制粘贴`Reports`类、`__init__`、`__getattr__`和`_build_shortcuts`方法。显然，你需要编写自己的方法并更新`_build_shortcuts`中的`dict0`。

+   对于 Python 专家，我很想听听你对我的方法的看法，如果你正在做类似的事情，或做得更好，请分享。

## 免责声明

本叙述仅用于说明目的，绝不代表我或我所在公司或客户的任何观点或经验。

这是我第一次使用这种写作风格，如果你喜欢它，请通过点赞、关注和订阅表示感谢，谢谢！

[1] 非常感谢同伟的校对和建议。
