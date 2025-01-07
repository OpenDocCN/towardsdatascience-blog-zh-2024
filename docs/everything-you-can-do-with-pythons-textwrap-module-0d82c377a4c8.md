# 你可以用 Python 的 textwrap 模块做的一切

> 原文：[`towardsdatascience.com/everything-you-can-do-with-pythons-textwrap-module-0d82c377a4c8?source=collection_archive---------2-----------------------#2024-02-07`](https://towardsdatascience.com/everything-you-can-do-with-pythons-textwrap-module-0d82c377a4c8?source=collection_archive---------2-----------------------#2024-02-07)

## 了解你可以用 Python 的`textwrap`模块做的所有事情，包括格式化、文本换行、修剪等等。

[](https://medium.com/@martin.heinz?source=post_page---byline--0d82c377a4c8--------------------------------)![Martin Heinz](https://medium.com/@martin.heinz?source=post_page---byline--0d82c377a4c8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0d82c377a4c8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d82c377a4c8--------------------------------) [Martin Heinz](https://medium.com/@martin.heinz?source=post_page---byline--0d82c377a4c8--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d82c377a4c8--------------------------------) ·阅读时长 5 分钟·2024 年 2 月 7 日

--

![](img/c3d8bf462ca3ad135cf342ed93cd2b6f.png)

图片来自[Hello Sunday](https://unsplash.com/@hello_sunday?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)，[Unsplash](https://unsplash.com/photos/black-white-blue-and-pink-floral-textile-COFQgC0V4ks?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Python 有许多格式化字符串和文本的选项，包括 f-string、`format()`函数、模板等等。然而，有一个模块很少有人知道，它叫做`textwrap`。

本模块专门为帮助你处理换行、缩进、修剪等任务而构建，在本文中，我们将探讨你可以用它做的所有事情。

# Shorten

让我们从`textwrap`模块中的一个非常简单但非常实用的函数——`shorten`开始：

```py
from textwrap import shorten

shorten("This is a long text or sentence.", width=10)
# 'This [...]'
shorten("This is a long text or sentence.", width=15)
# 'This is a [...]'
shorten("This is a long text or sentence.", width=15, placeholder=" <...>")
# 'This is a <...>'
```

顾名思义，`shorten`允许我们在指定的字符串过长时将文本修剪为一定长度（`width`）。默认情况下，修剪后的文本占位符为`[...]`，但可以通过`placeholder`参数覆盖此默认值。

# Wrap

这个模块中的另一个更有趣的函数是`wrap`。它的明显用途是将长文本拆分为相同长度的多行，但我们还能用它做更多的事情：

```py
from textwrap import wrap
s = '1234567890'
wrap(s, 3)
# ['123', '456', '789', '0']
```

在这个示例中，我们将一个字符串分成相等的块，这在批处理时比仅仅格式化更有用。

然而，使用这个函数时需要注意一些事项：

```py
s = '12\n3  45678\t9\n0'
wrap(s, 3)
# ['12', '3  ', '456', '78', '9 0']
# the first ("12") element "includes" newline
# the 4th element ("78") "includes" tab
wrap(s, 3, drop_whitespace=False, tabsize=1)
# ['12 ', '3  ', '456', '78 ', '9 0']
```

使用`wrap`时，您应该小心空格——上面您可以看到换行符、制表符和空格字符的行为。您可以看到第一个元素（`12`）*"包含"*换行符，第 4 个元素（`78`）*"包含"*制表符，但这些默认会被丢弃，因此这些元素的字符数只有 2 个，而不是 3 个。

我们可以指定`drop_whitespace`关键字参数来保留它们并保持块的适当长度。

这可能显而易见，但`wrap`对于将整个文件重新格式化为特定的行宽也是非常有用的：

```py
with open("some-text.md", "r", encoding="utf-8") as f:
    formatted = wrap(f.read(), width=80)  # List of lines
    formatted = fill(f.read(), width=80)  # Single string that includes line breaks 
    # ... write it back
```

我们还可以使用`fill`函数，它是`"\n".join(wrap(text, ...))`的简写。这两者的区别在于，`wrap`会给我们一个行列表，我们需要自己将它们连接起来，而`fill`则会给我们一个已经使用换行符连接的单一字符串。

# TextWrapper

`textwrap`模块还包括一个更强大的`wrap`函数，它是一个`TextWrapper`类：

```py
import textwrap

w = textwrap.TextWrapper(width=120, placeholder=" <...>")
for s in list_of_strings:
    w.wrap(s)
    # ...
```

如果我们需要多次使用相同参数调用`wrap`，如上所示，这个类及其`wrap`方法是非常有用的。

在查看`TextWrapper`时，让我们也尝试一些其他的关键字参数：

```py
user = "John"
prefix = user + ": "
width = 50
wrapper = TextWrapper(initial_indent=prefix, width=width, subsequent_indent=" " * len(prefix))
messages = ["...", "...", "..."]
for m in messages:
    print(wrapper.fill(m))

# John: Lorem Ipsum is simply dummy text of the
#       printing and typesetting industry. Lorem
# John: Ipsum has been the industry's standard dummy
#       text ever since the 1500s, when an
# John: unknown printer took a galley of type and
#       scrambled it to make a type specimen
```

在这里我们可以看到`initial_indent`和`subsequent_indent`的使用，分别用于缩进段落的第一行和后续行。还有一些其他选项，您可以在[文档](https://docs.python.org/3/library/textwrap.html#textwrap.TextWrapper)中找到。

此外，由于`TextWrapper`是一个类，我们还可以扩展它并完全重写它的一些方法：

```py
from textwrap import TextWrapper

class DocumentWrapper(TextWrapper):

    def wrap(self, text):
        split_text = text.split('\n')
        lines = [line for par in split_text for line in TextWrapper.wrap(self, par)]
        return lines

text = """First line,

Another, much looooooonger line of text and/or sentence"""
d = DocumentWrapper(width=50)
print(d.fill(text))

# First line,
# Another, much looooooonger line of text and/or
# sentence
```

这是一个很好的例子，展示了如何修改`wrap`方法，以保留现有的换行符并正确打印它们。

要了解如何使用`TextWrapper`处理多个段落的完整示例，请查看[这篇文章](https://code.activestate.com/recipes/358228-extend-textwraptextwrapper-to-handle-multiple-para/)。

# 缩进

最后，`textwrap`还包括两个用于缩进的函数，第一个是`dedent`：

```py
# Ugly formatting:
multiline_string = """
First line
Second line
Third line
"""

from textwrap import dedent

multiline_string = """
                   First line
                   Second line
                   Third line
                   """

print(dedent(multiline_string))

# First line
# Second line
# Third line

# Notice the leading blank line...
# You can use:
multiline_string = """\
                   First line
                   Second line
                   Third line
                   """

# or
from inspect import cleandoc
cleandoc(multiline_string)
# 'First line\nSecond line\nThird line'
```

默认情况下，Python 中的多行字符串会保留字符串中使用的任何缩进，因此我们需要使用上面片段中第一个变量所示的丑陋格式。但我们可以使用`dedent`函数来改善格式——我们只需按自己喜欢的方式缩进变量值，然后在使用它之前调用`dedent`。

另外，我们也可以使用`inspect.cleandoc`，它也会去除前导换行符。然而，这个函数会将空格编码为特殊字符（`\n`和`\t`），因此您可能需要重新格式化它。

自然，当有`dedent`时，也需要有`indent`函数：

```py
from textwrap import indent

indented = indent(text, "    ", lambda x: not text.splitlines()[0] in x)
```

我们只需提供文本和每行将缩进的字符串（这里是 4 个空格，我们可以——例如——使用`>>>`使其看起来像 REPL）。此外，我们还可以提供一个谓词，用来决定该行是否应该缩进。在上面的例子中，`lambda`函数使得字符串的第一行（段落）不被缩进。

# 结束语

`textwrap` 是一个简单的模块，只有少数几个函数/方法，但它再次证明了 Python 确实为那些不一定需要标准库的功能提供了*"开箱即用"*的支持，当你恰好需要它们时，它们能为你节省大量时间。

如果你恰好做很多文本处理工作，那么我也推荐你查看一下专门处理文本的[整个文档部分](https://docs.python.org/3/library/text.html)。这里有许多你可能没意识到需要的模块和小功能。😉

*本文最初发布在* [*martinheinz.dev*](https://martinheinz.dev/blog/108)

你可能还喜欢…

[](https://betterprogramming.pub/everything-you-can-do-with-pythons-bisect-module-40bdaadbc22f?source=post_page-----0d82c377a4c8--------------------------------) [## 你可以用 Python 的 Bisect 模块做的一切

### 学习如何使用 Python 的“bisect”模块来优化搜索并保持数据排序

[betterprogramming.pub](https://betterprogramming.pub/everything-you-can-do-with-pythons-bisect-module-40bdaadbc22f?source=post_page-----0d82c377a4c8--------------------------------)
