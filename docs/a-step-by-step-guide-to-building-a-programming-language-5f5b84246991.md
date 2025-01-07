# 构建编程语言的逐步指南

> 原文：[https://towardsdatascience.com/a-step-by-step-guide-to-building-a-programming-language-5f5b84246991?source=collection_archive---------0-----------------------#2024-06-19](https://towardsdatascience.com/a-step-by-step-guide-to-building-a-programming-language-5f5b84246991?source=collection_archive---------0-----------------------#2024-06-19)

## 在几小时内从零开始构建一种编程语言

[](https://medium.com/@physboom?source=post_page---byline--5f5b84246991--------------------------------)[![Matthew Chak](../Images/88881eb5a7c8f08c15555bc8c3c613d3.png)](https://medium.com/@physboom?source=post_page---byline--5f5b84246991--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5f5b84246991--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5f5b84246991--------------------------------) [Matthew Chak](https://medium.com/@physboom?source=post_page---byline--5f5b84246991--------------------------------)

·发布在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5f5b84246991--------------------------------) ·阅读时间：17分钟·2024年6月19日

--

![](../Images/c544cad7b26dd0bb3008c29eb2dba5e4.png)

树：计算机科学的层次核心。来源：[Jeremy Bishop 在 Unsplash](https://unsplash.com/@jeremybishop)

世界上充满了各种具有不同用途的编程语言。然而，这些语言大多数都是为非常通用的目的构建的——有时，我们可能想要设计一种语言来适应非常特定的用例（例如，Facebook设计了React来简化其Web应用程序的开发，而苹果最近开发了 [Pkl](https://github.com/apple/pkl)，一种旨在简化配置的语言。这个例子在各个领域中有很多）。因此，知道如何构建编程语言是一个非常有用的技能。

在本文中，我们将从零开始构建一种解释型编程语言，并在此过程中学习一些关于λ演算和编程语言的知识。我们构建的语言将是相当深奥的，但这个过程应该能给你一个如何设计自己特定用途语言的概念（并教会你一些关于编程语言如何在幕后运行的有用信息）。

# 游戏计划

由于我们正在构建一种解释型语言¹，整体流程大致如下：

![](../Images/9f171ab3d352f80924bec9732bde250b.png)

我们语言的广泛流程图

基本上，我们从一些用我们**目标语言**（我们要编写的语言）编写的**具体语法**（代码）开始，传递给某个**解析器**，它将其转换为**抽象语法树**（代码的树形表示，易于处理），然后将其传递给**解释器**，由它“运行”抽象语法树，给我们最终结果。请注意，解析器和解释器是用某种已经存在的**宿主语言**编写的——例如，C的原始解析器和编译器就是用汇编语言编写的。

*** 注意：我这里所使用的“解析器”涵盖了整个解析过程。通常，词法分析是在“语法分析”之前完成的，但在这种情况下，语法分析只是将具体语法转换为抽象语法的过程，无论抽象语法是什么样子。*

举个例子，考虑以下一个简单语言的基本算术规范：

```py
EXPR = number
       | EXPR + EXPR
       | EXPR - EXPR
       | EXPR * EXPR
       | EXPR / EXPR
       | (EXPR)
```

顺便说一下，上面的是**EBNF**（扩展巴科斯范式）形式的**上下文无关文法²**。我不会在这里深入探讨这意味着什么，但所有用这种形式编写的编程语言都可以通过CYK算法在多项式时间内进行解析。对于这个EBNF，像`(4 + 4) * 3`这样的表达式是一个有效的程序，但像`def f(x): return 5; f(5)`这样的代码就不是。

假设现在给定了具体语法`(4 + 4) * 3`。解析后，我们应该得到如下的抽象语法树（AST）：

![](../Images/6bd4ba20c268ed4ff1093f4a20ce1820.png)

我们具体语法的抽象语法树（AST）

然后我们的解释器将从根节点开始，递归地遍历树，直到我们得到答案，也就是24。

请注意，这个语法是**模糊的**——例如，表达式`4 + 4 * 3`应该如何解析？它可以解析为上面那样（即`(4 + 4) * 3`），也可以解析为`4 + (4 * 3)`——在我们指定的语言中，两种解析方式都没有本质上更“正确”的，因它们都是有效的解析树。在这种情况下，解析器将不得不任意决定如何解析我们的语言。

# 设计我们的语法

根据流程图，我们的第一步应该是设计我们的具体语法。你选择使用什么语法完全取决于你自己。我决定创建*EmojiLang*，这是一种（糟糕的）语言，可以确保你在输入时屏幕非常五彩斑斓。语法如下：

```py
grammar EmojiLang;

program: '🏃‍♂️🏃‍♂️🏃‍♂️' expr '🛑🛑🛑' EOF;

expr: '(' (ID
         | atom
         | ifCond
         | anonFunctionDefn
         | funApplication
         | varAssignment
         | READ_FLOAT
         | READ_STRING
         | printExpr
         | sequentialOp
         | baseComputation) ')';

atom: NUMBER | BOOLEAN | STRING;
ifCond: '🤔' cond=expr '❓' ontrue=expr ':' onfalse=expr;
anonFunctionDefn: '🧑‍🏭' arg=ID '⚒️' body=expr;
funApplication: '🏭' fun=expr arg=expr;
varAssignment: '📦' var=ID '🔜' val=expr;
printExpr: '🖨️' expr;
sequentialOp: '📋' first=expr second=expr;
baseComputation: left=expr op=('➕' | '➖' | '✖️' | '➗' | '🟰' | '≤') right=expr;

ID: [a-zA-Z_][a-zA-Z0-9_]*;
NUMBER: [0-9]+ ('.' [0-9]+)?;
BOOLEAN: '👍' | '👎';
STRING: '"' ~[\r\n"]* '"';
READ_FLOAT: '🔢';
READ_STRING: '🔤';

WHITESPACE: [ \t\r\n]+ -> skip;
COMMENT: '😴' .*? '⏰' -> skip;
LINE_COMMENT: '🥱' ~[\r\n]* -> skip;
```

*** 注意：上面的规范是为名为ANTLR的工具编写的，我们很快就会回到这个话题。*

这个语言当然很荒谬，但有几个原因让它很有趣。首先，我们的所有表达式都需要加括号。这使得编写代码极为麻烦，但也让我们的语法不含歧义。其次，注意到我们只能定义匿名函数——没有类似于Python中`def`的语法。最后，这种语言中的所有函数（除了基本计算）*只有一个参数*。我们稍后在使用这种语言时会探讨最后两个设计决策的影响。

# 解析

我们当然可以自己编写解析器。不过幸运的是，有一些工具可以解析任意的上下文无关文法。在本教程中，我们将使用**ANTLR**（你可以在[这里](https://www.antlr.org/)下载）。ANTLR是一个非常好用且易于使用的工具，它可以根据类似上面那样的语法规范生成多种目标语言的解析器（在本教程中，我们将使用Python）。

使用它相当简单，只需按照以下步骤操作：

1.  从[这里](https://www.antlr.org/download.html)下载ANTLR的Java二进制文件

1.  为你的语法创建一个.g4文件（如上所示）。注意，ANTLR实际上无法很好地处理表情符号，所以如果你打算在语言中使用表情符号，请运行以下Python脚本来去表情化你的语法：

```py
import emoji
import sys

def demojify_file(input_file, output_file):
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    input_text = emoji.demojize(input_text)

    with open(output_file, "w", encoding="utf-8") as f:
        f.write(input_text)

if __name__ == "__main__":
    input_file = sys.argv[1]
    output_file = sys.argv[2]
    demojify_file(input_file, output_file)
```

3. 运行`java -Xmx500M -cp <path_to_antlr.jar> org.antlr.v4.Tool -Dlanguage=Python3 <your_grammar.g4>`来生成解析器

然后，你可以导入生成的解析文件并按如下方式使用它们：

```py
from antlr4 import *
from EmojiLangLexer import EmojiLangLexer
from EmojiLangParser import EmojiLangParser
from EmojiLangListener import EmojiLangListener
import emoji

if __name__ == "__main__":
    input_file = sys.argv[1]
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    input_text = emoji.demojize(input_text)
    input_stream = InputStream(input_text)
    lexer = EmojiLangLexer(input_stream)
    token_stream = CommonTokenStream(lexer)
    parser = EmojiLangParser(token_stream)
    tree = parser.program()

    if parser.getNumberOfSyntaxErrors() > 0:
        exit(1)
```

你可能不需要做去表情化的步骤，在这种情况下你可以使用antlr4的`FileStream`代替`InputStream`，但这其实并不重要。现在，我们有了一个非常不错的抽象语法树，容易处理，我们可以进入更难的部分——解释³

# 构建解释器

因为我们正在处理树结构，所以我们的解释器自然会是递归的实体。不过，我们确实有一些选择，决定如何具体实现其某些特性。对于本教程，我们将构建一个使用**环境**将变量绑定到地址，然后使用可变的**存储**将地址映射到值的解释器。这个想法相当常见，尽管并非无处不在，它允许我们保持正确的作用域并支持变量的变化。为了简化实现，我们还将让我们的解释器返回一个通用的值结构。

## 值、存储与环境

首先，让我们定义我们的解释器可以输出什么。在我们的EBNF中有三个明显的基本情况（即布尔值、字符串和数字），因此我们将为这些创建值对象：

```py
class Value:
    def __init__(self, value):
        self.value = value

    def __str__(self) -> str:
        return str(self.value)

class NumValue(Value):
    def __init__(self, value: float):
        super().__init__(value)

class StringValue(Value):
    def __init__(self, value: str):
        super().__init__(value)

class BoolValue(Value):
    def __init__(self, value: bool):
        super().__init__(value) 
```

为了存储变量到值的映射，我们还将创建一个环境和一个存储：

```py
class EnvLookupException(Exception):
    pass

class Environment:
    def __init__(self):
        self.vars = {}

    def set_var(self, name, addr: int):
        self.vars[name] = addr

    def get_var(self, name):
        if name not in self.vars:
            raise EnvLookupException(f"Variable {name} not found in environment")
        return self.vars[name]

    def copy(self):
        new_env = Environment()
        new_env.vars = self.vars.copy()
        return new_env

class Store:
    def __init__(self):
        self.store = []

    def alloc(self, value: Value):
        self.store.append(value)
        return len(self.store) - 1

    def get(self, addr: int):
        if addr >= len(self.store):
            raise EnvLookupException(f"Address {addr} not found in store")
        return self.store[addr]

    def set(self, addr: int, value: Value):
        if addr >= len(self.store):
            raise EnvLookupException(f"Address {addr} not found in store")
        self.store[addr] = value
```

实际上，我们的环境将存储变量→地址绑定，而我们的存储将保存地址→值绑定。存储可能在我们当前的功能集下不是必需的，但如果我们允许通过引用传递变量的变化，它将会有用⁴。

理想情况下，我们还希望将函数作为变量传递，因此我们需要一个额外的值来表示它们。为此，我们创建了一个**闭包**，它包含了函数的参数、主体以及函数所在的环境：

```py
class ClosureValue(Value):
    # Body should be an antlr4 tree
    def __init__(self, param: str, body: object, env: 'Environment'):
        super().__init__(None)
        self.param = param
        self.body = body
        self.env = env

    def __str__(self) -> str:
        return f"<function>"
```

你可能会问，为什么我们需要将环境存储在函数中。假设我们有一个像这样的函数值：

```py
class FunctionValue(Value):
    # Body should be an antlr4 tree
    def __init__(self, param: str, body: object):
        super().__init__(None)
        self.param = param
        self.body = body

    def __str__(self) -> str:
        return f"<function>"
```

现在，假设我们在我们的语言中有与以下代码等价的内容：

```py
# ----------------
# ENV MUST PERSIST
# ----------------
def f(x):
  def g(y):
    return x + y
  return g(x)

print((f(4))(5)) # 9

# ----------------
# ENV MUST CLEAR
# ----------------
def f2(x):
  return x + y

def g2(y):
  return f(5)

print(f(4)) # Should crash
```

为了确保 `y` 在顶级情况下仍然在 `g` 的作用域内，我们必须实现动态作用域（作用域在程序运行时变量被加入到环境中，而不会被清除），而不使用闭包，这意味着底部代码实际上会运行并打印 `9`。但是，为了让底部代码正确地崩溃，我们不能实现动态作用域。因此，我们希望函数能够有效地记住它们是在哪个环境中创建的——这就是为什么我们要在闭包类中添加环境的原因⁵。

## 解释器

现在，我们准备编写实际的解释器了。在 ANTLR 中，我们的解释器将扩展 `EmojiLangListener` 类。我们还需要创建一个顶级环境，并给解释器提供一个存储：

```py
class EmojiLangException(Exception):
    pass

TOP_ENV = Environment()

class Interpreter(EmojiLangListener):
    def __init__(self):
        self.store = Store()
```

现在，我们需要创建一个 `interp` 方法，来处理我们所有的表达式案例。最终代码将如下所示：

```py
def interp(self, prog, env: Environment) -> Value:
      if prog.ID():
          return self.interp_id(prog.ID())
      elif prog.atom():
          return self.interp_atom(prog.atom())
      elif prog.anonFunctionDefn():
          return self.interp_function_defn(prog.anonFunctionDefn())
      elif prog.READ_FLOAT():
          return self.interp_read_float()
      elif prog.READ_STRING():
          return self.interp_read_string()
      elif prog.printExpr():
          return self.interp_print_expr()
      elif prog.ifCond():
          return self.interp_if(prog.ifCond(), env)
      elif prog.sequentialOp():
          return self.interp_sequential_op(prog.sequentialOp(), env)
      elif prog.baseComputation():
          return self.interp_base_computation(prog.baseComputation(), env)
      elif prog.varAssignment():
          return self.interp_var_assignment(prog.varAssignment(), env)
      elif prog.funApplication():
          return self.interp_fun_application(prog.funApplication(), env)
```

我们的基础案例（ID、原子值、函数定义、读取和打印）相对简单，因此我们可以直接写出来：

```py
def interp(self, prog, env: Environment) -> Value:
    if prog.ID():
        return self.store.get(env.get_var(prog.ID().getText()))
    elif prog.atom():
        return self.interp_atom(prog.atom())
    elif prog.anonFunctionDefn():
        return ClosureValue(prog.anonFunctionDefn().arg.text, prog.anonFunctionDefn().body, env)
    elif prog.READ_FLOAT():
        try:
            return NumValue(float(input("> ")))
        except ValueError:
            raise EmojiLangException("Expected float input")
    elif prog.READ_STRING():
        return StringValue(input("> "))
    elif prog.printExpr():
        value = self.interp(prog.printExpr().expr(), env)
        if isinstance(value, StringValue):
            # print without quotes
            print(str(value)[1:-1])
        else:
            print(value)
        return value
    # ...

def interp_atom(self, atm):
    if atm.NUMBER():
        return NumValue(float(atm.NUMBER().getText()))
    elif atm.BOOLEAN():
        return BoolValue(atm.BOOLEAN().getText() == ":thumbs_up:")
    elif atm.STRING():
        return StringValue(atm.STRING().getText())
    # THIS SHOULD NEVER HAPPEN
    raise EmojiLangException(f"Unknown atom {atm.getText()}")
```

我们的 if 条件也相当简单。我们只需要解释条件，然后如果条件为真，就返回解释真分支的结果，如果为假，就返回解释假分支的结果。因此，代码就是这样：

```py
def interp_if(self, if_cond, env: Environment):
    cond = self.interp(if_cond.cond, env)
    if not isinstance(cond, BoolValue):
        raise EmojiLangException(f"Expected boolean when evaluating if condition, got {cond}")
    return self.interp(if_cond.ontrue if cond.value else if_cond.onfalse, env)
```

顺序操作同样简单，它们只需要先解释第一个表达式，再解释第二个表达式。因此，我们可以像这样替换该代码块：

```py
def interp(self, prog, env: Environment) -> Value:
  # ...
  elif prog.sequentialOp():
    self.interp(prog.sequentialOp().first, env)
    return self.interp(prog.sequentialOp().second, env)
  # ...
```

接下来是基础计算。由于我们需要处理很多操作，所以这段代码相对较长，但并不复杂：

```py
def interp_base_computation(self, base_computation, env: Environment):
    left, right = self.interp(base_computation.left, env), self.interp(base_computation.right, env)
    if base_computation.op.text == ":plus:":
        if isinstance(left, NumValue) and isinstance(right, NumValue):
            return NumValue(left.value + right.value)
        elif isinstance(left, StringValue) and isinstance(right, StringValue):
            return StringValue(left.value + right.value)
        raise EmojiLangException(f"Cannot add {left} and {right}")
    if base_computation.op.text == ":heavy_equals_sign:":
        if type(left) != type(right):
            return BoolValue(False)
        if isinstance(left, ClosureValue):
            raise EmojiLangException("Cannot compare functions")
        return BoolValue(left.value == right.value)

    # NUMBERS ONLY COMPUTATIONS
    if not isinstance(left, NumValue) or not isinstance(right, NumValue):
        raise EmojiLangException(f"Expected numbers when evaluating base computation, got {left} and {right}")
    if base_computation.op.text == ":minus:":
        return NumValue(left.value - right.value)
    elif base_computation.op.text == ":multiply:":
        return NumValue(left.value * right.value)
    elif base_computation.op.text == ":divide:":
        if right.value == 0:
            raise EmojiLangException("Division by zero")
        return NumValue(left.value / right.value)
    elif base_computation.op.text == "≤":
        return BoolValue(left.value <= right.value) 
```

也许通过使用例如字典的方式，这部分代码还可以稍微清理一下，但这并不是特别重要。接下来是变量赋值，也不太复杂。我们在这里有几个选择，具体想让它做什么——即，它是允许新变量的出现，还是只修改现有的变量？我选择后一种情况，这样我们的代码就像这样：

```py
def interp_var_assignment(self, var_assign, env: Environment):
    value = self.interp(var_assign.val, env)
    addr = env.get_var(var_assign.var.text)
    self.store.store[addr] = value
    return value
```

最后，我们来看看函数应用。在这里，我们需要执行四个步骤。首先，我们解释我们正在调用的函数，以获得我们的闭包。接着，我们解释我们的参数。然后，我们将参数绑定到闭包环境的副本中。最后，我们在新的环境中解释闭包的主体。代码最终如下所示：

```py
def interp_fun_application(self, fun_app, env: Environment):
    closure = self.interp(fun_app.fun, env)
    if not isinstance(closure, ClosureValue):
        raise EmojiLangException(f"Expected function when evaluating function application, got {closure}")
    arg = self.interp(fun_app.arg, env)
    new_env = closure.env.copy()
    new_env.set_var(closure.param, self.store.alloc(arg))
    return self.interp(closure.body, new_env)
```

现在，我们的解释器功能完备！我们只需要修改我们的主程序，运行它来解释一个程序：

```py
if __name__ == "__main__":
    input_file = sys.argv[1]
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    # Preprocess input to replace emojis with demojized names
    input_text = emoji.demojize(input_text)

    input_stream = InputStream(input_text)
    lexer = EmojiLangLexer(input_stream)
    token_stream = CommonTokenStream(lexer)
    parser = EmojiLangParser(token_stream)
    tree = parser.program()

    if parser.getNumberOfSyntaxErrors() > 0:
        exit(1)

    interpreter = Interpreter()
    interpreter.interp(tree.expr(), TOP_ENV)
```

# 玩转我们的语言

我们现在终于准备好开始编写我们的语言程序了。下面是一个简单的 emoji 语言（EML）版本的 hello world 程序：

```py
🏃‍♂️🏃‍♂️🏃‍♂️
    (🖨️ ("Hello World!"))
🛑🛑🛑
```

要运行它，我们只需执行：

```py
> python emoji_lang.py helloworld.eml
Hello World!
```

（上述内容，当然假设程序存在于名为`helloworld.eml`的文件中）

## 柯里化

在第一部分中，我提到我们的编程语言有趣之处在于函数只能接受一个参数。那么，我们如何创建类似于多元函数的效果呢？例如，考虑以下Python代码：

```py
def f(x, y):
  return x + y

print(f(3, 4))
```

这里的`f`有**阶数**2——也就是说，它接受两个参数。然而，我们可以编写等效的代码，只使用一元函数（除了加法），如下所示：

```py
def f(x):
  def g(y):
    return x + y
  return g

print((f(3))(4))
```

上述将高阶函数转化为一元函数的概念叫做**柯里化**。它适用于任何阶数的函数——对于一个阶数为*n*的函数，我们可以简单地进行柯里化*n-1*次。在表情语言中，这使得我们能够编写如下程序：

```py
🏃‍♂️🏃‍♂️🏃‍♂️
    (📋
        (🖨️ ("Enter two numbers to compute their sum."))
        (🖨️
            (🏭
                (🏭 
                    (🧑‍🏭 x ⚒️ 
                        (🧑‍🏭 y ⚒️
                            ((x) ➕ (y))
                        )
                    )
                (🔢))
            (🔢))
        )
    )
🛑🛑🛑
```

其Python翻译为

```py
print("Enter two numbers to compute their sum.")
print(((lambda x: (lambda y: x + y)))(float(input()))(float(input())))
```

或者，更易读地，

```py
print("Enter two numbers to compute their sum.")

def f(x):
  def g(y):
    return x + y
  return g

x = float(input())
y = float(input())

print(f(x)(y))
```

还要注意，第一次的Python迭代中没有使用命名函数。事实证明，我们实际上并不需要它们，尽管它们当然有助于提高可读性。然后我们得到

```py
> python emoji_lang.py currying.eml
Enter two numbers to compute their sum
> 4
> 5
9.0
```

## 递归

那么，在这个语言中我们如何实现循环或递归呢？我们没有`for`或`while`的语法，而且没有函数的名称，看起来似乎递归是不可能的。

然而，我们可以做一个小技巧。因为函数是值，我们可以在调用它们时将函数传递给*它们自己*，从而使它们能够调用自己。

比如，考虑以下Python代码：

```py
n = int(input())
while n > 0:
  print(n)
  n -= 1
```

我们可以使用类似下面的代码将其转换为递归版本⁶：

```py
def while_loop(condition, body):
  """
  A recursive implementation of a while loop.

  Arguments
  -------------
  - condition: Some function of zero arguments that returns a boolean value
  - body: Some function of zero arguments to run while the condition returns true
  """
  if condition():
    body()
    while_loop(condition, body)
  else:
    return False

class Box:
    def __init__(self, n):
        self.n = n

    def set_n(self, n):
        self.n = n

    def get_n(self):
        return self.n

n = Box(int(input()))

def body():
    print(n.get_n())
    n.set_n(n.get_n() - 1)

while_loop(lambda: n.get_n() > 0, body)
```

但我们在这里确实遇到了一个问题——即注意到`while_loop`是如何调用它自己的。我们在只有匿名函数的语言中无法做到这一点，那么我们该如何解决呢？答案是，我们可以做类似下面这样的操作：

```py
def while_loop(self, condition, body):
  if condition():
    body()
    self(self, condition, body)
  else:
    return False

# ...
# (define n as a box)
# ...

def body():
    print(n.get_n())
    n.set_n(n.get_n() - 1)

def call_while(loop_func, condition, body):
    loop_func(loop_func, condition, body)

call_while(while_loop, lambda: n.get_n() > 0, body)
```

实际上，我们让`while_loop`将*它自己*作为参数。然后，我们可以在`while_loop`内部调用`self`，使得`while_loop`能够递归地调用自己。

当然，我们仍然需要将这个进行lambda化并进行柯里化，以适应我们的语言，因此我们需要编写与以下等效的代码：

```py
(((lambda while_loop: 
    lambda n: 
        while_loop(while_loop)
                  (lambda bogus: n.get_n() > 0)
                  (lambda bogus: print(n.get_n()) or n.set_n(n.get_n() - 1)))
(lambda self:
    lambda cond:
        lambda body:
            (body("BOGUS") or self(self)(cond)(body)) if cond("BOGUS") else False))
(Box(int(input()))))
```

这个有点难以理解，但它确实有效。在表情语言中，这就变成了

```py
🏃‍♂️🏃‍♂️🏃‍♂️
    (🏭
        (🏭
            (🧑‍🏭 while ⚒️
                (🧑‍🏭 n ⚒️
                    (🏭 (🏭 (🏭 (while) (while))
                        (🧑‍🏭 bogus ⚒️ (🤔 ((n) ≤ (0)) ❓ (👎) : (👍))))
                        (🧑‍🏭 bogus ⚒️ (📋
                            (🖨️ (n))
                            (📦 n 🔜 ((n) ➖ (1)))
                        )))
                ))
            😴
                Below is our while function. Note that it takes
                itself as an argument - this allows for recursion
                (there are other ways to do this, but recursion via self
                passing is fairly simple)

                ARGS: 
                1\. self(itself)
                2\. condition_func (function of zero arguments that returns a boolean)
                3\. body (function of zero arguments that returns nothing to run while true)

                RETURNS:
                false when finished
            ⏰
            (🧑‍🏭 self ⚒️
                (🧑‍🏭 condition_func ⚒️
                    (🧑‍🏭 body ⚒️
                        (
                            🤔 (🏭 (condition_func) ("BOGUS")) ❓ 
                                (📋 
                                    (🏭 (body) ("BOGUS")) 
                                    (🏭 (🏭 (🏭 (self) (self))
                                            (condition_func))
                                            (body))) : 
                                (👎)
                        ))))
        )
    (🔢))       
🛑🛑🛑
```

然后我们得到

```py
> python emoji_lang.py while_loop.eml
> 4
4.0
3.0
2.0
1.0
```

## 奖金：Y组合子

每次想调用`while`时传递`while`给它自己确实有些麻烦，那么如果我们能创建一个已经将自己进行柯里化的`while`版本呢？事实证明，我们可以通过一个叫做**Y组合子**的东西来实现。Y组合子是一个函数，形式如下：

```py
Y = lambda g: (lambda f: g(lambda arg: f(f)(arg))) (lambda f: g(lambda arg: f(f)(arg)))
```

这完全荒谬，但它允许我们有效地“存储”一个函数到它自己中。我不会详细讲解它，但如果你想了解更多，我建议阅读[这篇优秀的文章](https://www.playfulpython.com/y-combinator-in-python/)

然而，使用组合子后，我们可以将代码更改为以下内容：

```py
🏃‍♂️🏃‍♂️🏃‍♂️
    (🏭
        (🏭
            (🏭
                (🧑‍🏭 y_combinator ⚒️
                    (🧑‍🏭 while ⚒️
                        (🧑‍🏭 n ⚒️
                            (📋
                                🥱y-combinate our while
                                (📦 while 🔜 (🏭 (y_combinator) (while)))
                                (🏭 (🏭 (while)
                                        (🧑‍🏭 bogus ⚒️ (🤔 ((n) ≤ (0)) ❓ (👎) : (👍))))
                                        (🧑‍🏭 bogus ⚒️ (📋
                                                (🖨️ (n))
                                                (📦 n 🔜 ((n) ➖ (1)))
                                        ))
                                )
                            )
                        )
                    )
                )
                😴
                    Our y combinator function - this allows for recursion without e.g. self passing
                    by effectively currying the function and passing it to itself.
                ⏰
                (🧑‍🏭 fn_nr ⚒️
                    (🏭
                       (🧑‍🏭 cc ⚒️
                            (🏭 (fn_nr) 
                                (🧑‍🏭 x ⚒️ (🏭 (🏭 (cc) (cc)) (x)))
                            )
                       )
                       (🧑‍🏭 cc ⚒️
                            (🏭 (fn_nr) 
                                (🧑‍🏭 x ⚒️ (🏭 (🏭 (cc) (cc)) (x)))
                            )
                       )
                    )
                )
            )
            (🧑‍🏭 while ⚒️
                    (🧑‍🏭 condition_func ⚒️
                        (🧑‍🏭 body ⚒️
                            (
                                🤔 (🏭 (condition_func) ("BOGUS")) ❓ 
                                    (📋 
                                        (🏭 (body) ("BOGUS")) 
                                        (🏭 (🏭 (while)
                                                (condition_func))
                                                (body))) : 
                                    (👎)
                            ))))
        )  
    (🔢))    
🛑🛑🛑
```

现在，注意我们对`while`的调用在经过Y组合子化⁷后，仅涉及传递条件和主体——我们不需要传递它本身。我们仍然能够得到

```py
> python emoji_lang.py y_comb_while.eml
> 4
4.0
3.0
2.0
1.0
```

# 结论

恭喜！你现在可能已经构建了自己的编程语言，并用它编写了一些有趣的东西。尽管像 EmojiLang 这样的语言显然没有太多实际用途，但你可以想象创建自己语言的一些酷用例，例如通过创建一个超具体的脚本语言来加速常见任务。

如果你想挑战自己，可以尝试以下练习：

**练习 1：** 为以下语言构建一个简单的解析器和解释器，*不*使用 ANTLR。确保括号始终具有最高优先级，并且其他操作符具有相等优先级（例如，`4 + 4 * 3` 应该计算为 `24`，而不是 `16`）

```py
EXPR = number
       | EXPR + EXPR
       | EXPR - EXPR
       | EXPR * EXPR
       | EXPR / EXPR
       | (EXPR)
```

**练习 2：** 修改你之前的代码，以添加操作符优先级

**练习 3（棘手）**：我们不*必*将所有函数都定义为匿名函数。试着为以下语言实现一个解释器（你可以使用 ANTLR，但你需要自己编写 .g4 文件）：

```py
program = (FUNDEF | EXPR)* // one or more function definitions or expressions

// NOTE: <something> implies that something is a string
// also, feel free to ignore whitespace or add semicolons or parenthesize
// expressions/fundefs if you please

EXPR = number
     | functionApplication
     | computation

FUNDEF = 'def' <name> '(' <args>* '):' EXPR

functionApplication = <name> '(' EXPR* ')' // e.g. f(1, 2+2, g(3))
computation = EXPR + EXPR
             | EXPR - EXPR
             | EXPR * EXPR
             | EXPR / EXPR
             | (EXPR)
```

**练习 4（简单 → 非常非常难）：** 可以在[这里](https://github.com/antlr/grammars-v4)找到大量实际语言的 .g4 文件。为其中任何一个实现一个解释器。

*如有任何疑问，请联系 mchak@calpoly.edu*

*附言：感谢 Brian Jones，我的 CS 430 课程教授，教会了我很多这些内容。*

*除非另有说明，所有图片均由作者提供。*

1.  编译型语言略有不同。生成的抽象语法树会传递给编译器，编译器将其转换为其他现有语言的代码（通常是汇编语言）。然后，输出的代码可以运行。

1.  从技术上讲，这其实并不完全是 EBNF，但它足够接近。如果你感兴趣，可以在[这里](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form)阅读更多关于 EBNF 的内容

1.  注意：一些语言可能会在将抽象语法树（AST）传递给解释器之前进行一些优化。我们这里不会进行这些操作。

1.  我们仍然可以通过在环境中使用盒装值来允许变异，但存储更类似于大多数语言的做法，也更直观，至少对我来说是这样。

1.  请参阅 [7 Functions Anywhere (brown.edu)](https://cs.brown.edu/courses/cs173/2012/book/higher-order-functions.html#%28part._.Implementing_.Closures%29) 获取更多信息

6\. 请注意，我们必须在这里使用一个盒子，因为 Python 的环境与我们的环境工作方式不同，例如我们不能这样做：

```py
# ...
def get_body_and_cond(inp):
    n = inp
    condition = lambda: n > 0

    def body():
        print(n)
        n -= 1
    return condition, body

c, b = get_body_and_cond(float(input()))

while_loop(c, b)
```

7\. 事实上，由于我们的语言是急切地评估内容的，所以它自始至终都是 Z 组合的，但效果是一样的
