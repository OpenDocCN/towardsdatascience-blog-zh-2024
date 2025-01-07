# 应用 Python 编年史：Pydantic 简介

> 原文：[`towardsdatascience.com/applied-python-chronicles-a-gentle-intro-to-pydantic-a630f797e933?source=collection_archive---------2-----------------------#2024-07-25`](https://towardsdatascience.com/applied-python-chronicles-a-gentle-intro-to-pydantic-a630f797e933?source=collection_archive---------2-----------------------#2024-07-25)

## 无论你是数据工程师、机器学习工程师还是 Web 开发者，你都应该熟悉这个工具。

[](https://medium.com/@ilija.lazarevic?source=post_page---byline--a630f797e933--------------------------------)![Ilija Lazarevic](https://medium.com/@ilija.lazarevic?source=post_page---byline--a630f797e933--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a630f797e933--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a630f797e933--------------------------------) [Ilija Lazarevic](https://medium.com/@ilija.lazarevic?source=post_page---byline--a630f797e933--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a630f797e933--------------------------------) ·阅读时间：11 分钟·2024 年 7 月 25 日

--

![](img/63778e7e341a0790bc2b994ab5b0ebc3.png)

夕阳如何照耀 Pydantic 用户。图片由[弗拉基米尔·季莫费耶夫](https://www.istockphoto.com/photo/sunset-above-acropolis-of-lindos-doric-columns-the-ancient-temple-athena-lindia-the-gm1009651096-272195895?clarity=false)提供，授权给伊利亚·拉扎列维奇。

在许多使用场景中，Pydantic 几乎可以无缝地适配。数据处理等任务也能从使用 Pydantic 中受益。不过，它也可以在 Web 开发中用于解析和结构化预期格式的数据。

今天的主题是定义几个痛点，并展示如何使用 Pydantic。我们从最常见的使用场景开始，那就是数据解析和处理。

假设我们有一个包含十几列和几千行的 CSV 文件。在数据分析中，常见的情况是将这个 CSV 文件加载到 Pandas DataFrame 中，然后开始处理它。通常，你会开始检查数据和列的数据类型，删除其中的一些列，并创建新的列。这个过程是基于你对数据集的先前知识。然而，这对其他人来说并不总是透明的。他们要么需要打开 CSV 文件（或任何其他数据源），要么需要浏览代码，搞清楚哪些列正在使用或被创建。这对于数据分析和研究的初始步骤是没问题的。然而，一旦数据集被分析完，并且我们准备创建一个数据管道来加载、转换并用于分析或机器学习目的时，我们就需要一种标准化的方法，确保数据集和数据类型符合预期格式。这就是为什么我们需要一个可以声明或定义这些的库。有一些库可以做到这一点，它们大多数是开源的，而 Pydantic 作为开源库，也已经进入了不同的框架，并在各种用例中得到广泛接受。

好的，让我们开始吧。

# Python — 类型提示

在我们深入我之前提到的示例之前，我想先讲一些 Python 的基础知识。

Python 在其各个版本中引入了类型提示。什么是类型提示，为什么我们需要它呢？嗯，正如我们所知，Python 是一种动态类型的脚本语言。这意味着数据类型是在运行时推断的。这在工程师能够更快地编写代码上有其好处。坏处是，直到运行代码时，你才会发现类型不匹配。那时，可能有些晚了，无法迅速修复错误。由于 Python 仍然是一种动态类型语言，因此引入了所谓的“类型提示”，其目的是弥合这一差距，工程师可以使用它来通知读者和 IDE 预期的数据类型。

示例：

```py
def add(a, b):
	return a + b

add(4, 3)
> 7

add(.3, 4)
> 4.3

add('a', 'b')
> 'ab'
```

这是一个简短的示例，展示了一个定义好的函数如何在多个用例中使用，其中一些用例并非其编写者所预见。对于那些足够坚持的人，你将不得不引入许多限制条件，以确保代码按预期方式使用。

类型提示是怎样的呢？

```py
def add(a: int, b: int) -> int:
	return a + b

add(4, 3)
> 7

add(.3, 4)
> 4.3

add('a', 'b')
> 'ab'
```

这个也有效！为什么？因为这仍然是“类型提示”，而不是“类型强制”。如前所述，它是用来“通知”读者和“用户”预期的使用方式。代码的“用户”之一是 IDE，而你选择的 IDE 应该能够识别并在你尝试绕过数据类型声明时发出警告。

为什么我们要描述这些内容呢？因为 Pydantic 是建立在这种类型提示基础上的。它使用类型提示来定义数据类型和结构，并进行验证。

# Pydantic — 第一步

正如我之前提到的，Pydantic 用于验证数据结构和数据类型。你可以通过四种方式来使用它。今天我将介绍其中两个最重要的：

+   `validate_call` 用于基于类型提示和注解来验证函数调用，

+   `BaseModel` 用于通过类定义来定义和验证模型。

# Pydantic — validate_call

所以，没有比直接开始新事物更好的方法了。这就是我们开始学习 Pydantic 的方式。

在你能够使用它之前，你需要先安装它：

```py
pip install pydantic
```

为了清晰起见，我在这里也标注一下 Python 和 Pydantic 的版本：

```py
python version: 3.10.5
pydantic version: 2.5.3
```

然后，你需要创建一个新的 Python 项目，创建你的第一个 Python 脚本，导入 Pydantic，并开始使用它。第一个示例是修改我们之前的函数，并使用 Pydantic 来确保它按预期的方式使用。示例：

```py
import pydantic

@pydantic.validate_call
def add(a: int, b: int) -> int:
 return a + b

# ----

add(4, 4)
> 8

# ----

add('a', 'a')
> ValidationError: 2 validation errors for add
0
  Input should be a valid integer, unable to parse string as an integer [type=int_parsing, input_value='a', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_parsing>
1
  Input should be a valid integer, unable to parse string as an integer [type=int_parsing, input_value='a', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_parsing>

# ----

add(.4, .3)
> ValidationError: 2 validation errors for add
0
  Input should be a valid integer, got a number with a fractional part [type=int_from_float, input_value=0.4, input_type=float]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_from_float>
1
  Input should be a valid integer, got a number with a fractional part [type=int_from_float, input_value=0.3, input_type=float]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_from_float>

# ----

add('3', 'a')
> ValidationError: 1 validation error for add
1
  Input should be a valid integer, unable to parse string as an integer [type=int_parsing, input_value='a', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_parsing>

# ----

add('3', '3')
> 6

# ----

add('3', '3.3')
> ValidationError: 1 validation error for add
1
  Input should be a valid integer, unable to parse string as an integer [type=int_parsing, input_value='3.3', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/int_parsing>
```

需要澄清几点：

+   `validate_call` 被用作装饰器。它本质上是包裹在声明的函数周围，增加了在函数定义时以及调用时可以执行的额外逻辑。在这里，它用于确保传递给函数调用的数据符合预期的数据类型（提示）。

+   验证过的函数调用会在你以非预期的方式使用函数时抛出 `ValidationError`。这个错误信息很详细，能说明为什么会抛出这个错误。

+   根据慈悲原则，Pydantic 尝试推断你的意思并进行类型强制转换。这可能导致传递给函数调用的字符串值被隐式转换为预期的类型。

+   类型强制转换并不总是可能的，在这种情况下，`ValidationError` 会被抛出。

不知道什么是 Python 的 *decorator* 函数？阅读我之前关于这个主题的文章之一：

[](/advanced-python-functions-3be6810f92d1?source=post_page-----a630f797e933--------------------------------) ## 高级 Python：函数

### 阅读标题后，你可能会问自己：“Python 中的函数是高级...”

[towardsdatascience.com

那么默认值和参数提取呢？

```py
from pydantic import validate_call

@validate_call(validate_return=True)
def add(*args: int, a: int, b: int = 4) -> int:
 return str(sum(args) + a + b)

# ----
add(4,3,4)
> ValidationError: 1 validation error for add
a
  Missing required keyword only argument [type=missing_keyword_only_argument, input_value=ArgsKwargs((4, 3, 4)), input_type=ArgsKwargs]
    For further information visit <https://errors.pydantic.dev/2.5/v/missing_keyword_only_argument>

# ----

add(4, 3, 4, a=3)
> 18

# ----

@validate_call
def add(*args: int, a: int, b: int = 4) -> int:
 return str(sum(args) + a + b)

# ---- 

add(4, 3, 4, a=3)
> '18'
```

这个例子的收获：

+   你可以注解变量的可变参数声明（*args）。

+   即使你正在注解变量的数据类型，默认值仍然是一个可选项。

+   `validate_call` 接受 `validate_return` 参数，它使得函数返回值也进行验证。在这种情况下，也会应用数据类型强制转换。默认情况下，`validate_return` 设置为 `False`。如果保持默认值不变，函数返回的值可能与类型提示中声明的值不一致。

如果你想验证数据类型，但同时也限制该变量可以取的值怎么办？示例：

```py
from pydantic import validate_call, Field
from typing import Annotated 

type_age = Annotated[int, Field(lt=120)]

@validate_call(validate_return=True)
def add(age_one: int, age_two: type_age) -> int:
 return age_one + age_two

add(3, 300)
> ValidationError: 1 validation error for add
1
  Input should be less than 120 [type=less_than, input_value=200, input_type=int]
    For further information visit <https://errors.pydantic.dev/2.5/v/less_than>
```

这个示例展示了：

+   你可以使用`Annotated`和`pydantic.Field`不仅验证数据类型，还可以添加 Pydantic 用来约束变量值和格式的元数据。

+   `ValidationError`再次非常详细地说明了函数调用中哪里出了问题，这非常有帮助。

这里有一个如何验证并约束变量值的示例。我们将模拟一个 payload（字典），在它被验证后，你希望在函数中处理它：

```py
from pydantic import HttpUrl, PastDate
from pydantic import Field
from pydantic import validate_call
from typing import Annotated

Name = Annotated[str, Field(min_length=2, max_length=15)]

@validate_call(validate_return=True)
def process_payload(url: HttpUrl, name: Name, birth_date: PastDate) -> str:
 return f'{name=}, {birth_date=}'

# ----

payload = {
 'url': 'httpss://example.com',
 'name': 'J',
 'birth_date': '2024-12-12'
}

process_payload(**payload)
> ValidationError: 3 validation errors for process_payload
url
  URL scheme should be 'http' or 'https' [type=url_scheme, input_value='httpss://example.com', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/url_scheme>
name
  String should have at least 2 characters [type=string_too_short, input_value='J', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/string_too_short>
birth_date
  Date should be in the past [type=date_past, input_value='2024-12-12', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/date_past>

# ----

payload = {
 'url': '<https://example.com>',
 'name': 'Joe-1234567891011121314',
 'birth_date': '2020-12-12'
}

process_payload(**payload)
> ValidationError: 1 validation error for process_payload
name
  String should have at most 15 characters [type=string_too_long, input_value='Joe-1234567891011121314', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/string_too_long>
```

这就是验证函数参数及其返回值的基本方法。

现在，我们将进入 Pydantic 用于验证和处理数据的第二种最重要的方法：通过定义模型。

# Pydantic — BaseModel

这一部分对于数据处理目的来说更为有趣，正如你将看到的那样。

到目前为止，我们已经使用`validate_call`装饰器对函数进行了装饰，并指定了函数参数及其对应的类型和约束。

在这里，我们通过定义模型类来定义模型，在模型类中指定字段、字段类型和约束。这与我们之前做的非常相似。通过定义继承自 Pydantic `BaseModel`的模型类，我们使用了一个隐藏的机制来进行数据验证、解析和序列化。这样我们就能创建符合模型规范的对象。

这里有一个例子：

```py
from pydantic import Field
from pydantic import BaseModel

class Person(BaseModel):
 name: str = Field(min_length=2, max_length=15)
 age: int = Field(gt=0, lt=120)

# ----

john = Person(name='john', age=20)
> Person(name='john', age=20)

# ----

mike = Person(name='m', age=0)
> ValidationError: 2 validation errors for Person
name
  String should have at least 2 characters [type=string_too_short, input_value='j', input_type=str]
    For further information visit <https://errors.pydantic.dev/2.5/v/string_too_short>
age
  Input should be greater than 0 [type=greater_than, input_value=0, input_type=int]
    For further information visit <https://errors.pydantic.dev/2.5/v/greater_than>
```

你也可以在这里使用注解，并且还可以为字段指定默认值。我们来看另一个例子：

```py
from pydantic import Field
from pydantic import BaseModel
from typing import Annotated 

Name = Annotated[str, Field(min_length=2, max_length=15)]
Age = Annotated[int, Field(default=1, ge=0, le=120)]

class Person(BaseModel):
 name: Name
 age: Age

# ----

mike = Person(name='mike')
> Person(name='mike', age=1)
```

当你的用例变得有些复杂时，事情变得非常有趣。还记得我们定义的`payload`吗？我将定义另一个更复杂的结构，我们将对其进行逐步处理和验证。为了让它更有趣，我们来创建一个 payload，用于查询一个充当我们与 LLM 提供商之间中介的服务。然后我们会对其进行验证。

这里有一个例子：

```py
from pydantic import Field
from pydantic import BaseModel
from pydantic import ConfigDict

from typing import Literal
from typing import Annotated
from enum import Enum

payload = {
 "req_id": "test",
 "text": "This is a sample text.",
 "instruction": "embed",
 "llm_provider": "openai",
 "llm_params": {
  "llm_temperature": 0,
  "llm_model_name": "gpt4o"
 },
 "misc": "what"
}

ReqID = Annotated[str, Field(min_length=2, max_length=15)]

class LLMProviders(str, Enum):
 OPENAI = 'openai'
 CLAUDE = 'claude'

class LLMParams(BaseModel):
 temperature: int = Field(validation_alias='llm_temperature', ge=0, le=1)
 llm_name: str = Field(validation_alias='llm_model_name', 
                       serialization_alias='model')

class Payload(BaseModel):
 req_id: str = Field(exclude=True)
 text: str = Field(min_length=5)
 instruction: Literal['embed', 'chat']
 llm_provider: LLMProviders
 llm_params: LLMParams

 # model_config = ConfigDict(use_enum_values=True)

# ----

validated_payload = Payload(**payload)
validated_payload
> Payload(req_id='test', 
         text='This is a sample text.', 
          instruction='embed', 
          llm_provider=<LLMProviders.OPENAI: 'openai'>, 
          llm_params=LLMParams(temperature=0, llm_name='gpt4o'))

# ----          

validated_payload.model_dump()
> {'text': 'This is a sample text.',
 'instruction': 'embed',
 'llm_provider': <LLMProviders.OPENAI: 'openai'>,
 'llm_params': {'temperature': 0, 'llm_name': 'gpt4o'}}

# ----

validated_payload.model_dump(by_alias=True)
> {'text': 'This is a sample text.',
 'instruction': 'embed',
 'llm_provider': <LLMProviders.OPENAI: 'openai'>,
 'llm_params': {'temperature': 0, 'model': 'gpt4o'}}

# ----

# After adding 
#     model_config = ConfigDict(use_enum_values=True)
# in Payload model definition, you get

validated_payload.model_dump(by_alias=True)
> {'text': 'This is a sample text.',
 'instruction': 'embed',
 'llm_provider': 'openai',
 'llm_params': {'temperature': 0, 'model': 'gpt4o'}}
```

从这个详细的例子中我们可以得到一些重要的见解：

+   你可以使用枚举或`Literal`来定义期望的特定值列表。

+   如果你想将模型字段命名为与验证数据中的字段名不同的名称，可以使用`validation_alias`。它指定验证数据中的字段名。

+   `serialization_alias`用于当模型的内部字段名称不一定与序列化模型时你想使用的名称相同时。

+   可以使用`exclude=True`将字段排除在序列化之外。

+   模型字段也可以是 Pydantic 模型。在这种情况下，验证过程是递归进行的。这一部分非常棒，因为 Pydantic 在验证嵌套结构时会深入执行验证。

+   在模型定义中未考虑的字段不会被解析。

# Pydantic — 用例

在这里，我将展示一些代码片段，展示你如何在日常任务中使用 Pydantic。

# 数据处理

假设你有需要验证和处理的数据。这些数据可以存储在 CSV、Parquet 文件中，或者例如在 NoSQL 数据库中以文档的形式存储。让我们以 CSV 文件为例，假设你想处理其内容。

这里是 CSV 文件（`test.csv`）示例：

```py
name,age,bank_account
johnny,0,20
matt,10,0
abraham,100,100000
mary,15,15
linda,130,100000
```

下面是它如何进行验证和解析的：

```py
from pydantic import BaseModel
from pydantic import Field 
from pydantic import field_validator
from pydantic import ValidationInfo
from typing import List
import csv

FILE_NAME = 'test.csv'

class DataModel(BaseModel):
 name: str = Field(min_length=2, max_length=15)
 age: int = Field(ge=1, le=120)
 bank_account: float = Field(ge=0, default=0)

 @field_validator('name')
 @classmethod
 def validate_name(cls, v: str, info: ValidationInfo) -> str:
  return str(v).capitalize()

class ValidatedModels(BaseModel):
 validated: List[DataModel]

validated_rows = []

with open(FILE_NAME, 'r') as f:
 reader = csv.DictReader(f, delimiter=',')
 for row in reader:
  try:
   validated_rows.append(DataModel(**row))
  except ValidationError as ve:
   # print out error
   # disregard the record
   print(f'{ve=}')

validated_rows
> [DataModel(name='Matt', age=10, bank_account=0.0),
 DataModel(name='Abraham', age=100, bank_account=100000.0),
 DataModel(name='Mary', age=15, bank_account=15.0)]

validated = ValidatedModels(validated=validated_rows)
validated.model_dump()
> {'validated': [{'name': 'Matt', 'age': 10, 'bank_account': 0.0},
  {'name': 'Abraham', 'age': 100, 'bank_account': 100000.0},
  {'name': 'Mary', 'age': 15, 'bank_account': 15.0}]}
```

# FastAPI 请求验证

FastAPI 已经与 Pydantic 集成，因此这一部分会非常简短。FastAPI 处理请求的方式是将它们传递给处理路由的函数。通过将请求传递给一个函数，验证会自动进行。类似于我们在本文开头提到的 `validate_call`。

用于运行基于 FastAPI 的服务的 `app.py` 示例：

```py
from fastapi import FastAPI
from pydantic import BaseModel, HttpUrl

class Request(BaseModel):
    request_id: str
    url: HttpUrl

app = FastAPI()

@app.post("/search/by_url/")
async def create_item(req: Request):
    return item
```

# 结论

Pydantic 是一个非常强大的库，具有很多机制，适用于多种不同的使用场景和边缘情况。今天，我讲解了如何使用它的最基础部分，并且在下面提供了参考资料，供那些不畏困难的人查阅。

去探索一下吧。我相信它会在不同方面对你有所帮助。

# 参考文献

+   [Python 类型提示](https://docs.python.org/3/library/typing.html)。

+   [Pydantic 模型](https://docs.pydantic.dev/latest/concepts/models/)。

+   [验证调用装饰器](https://docs.pydantic.dev/latest/concepts/validation_decorator/)。

+   [Pydantic 字段](https://docs.pydantic.dev/latest/concepts/fields/)。

+   [Pydantic 验证器](https://docs.pydantic.dev/latest/concepts/validators/)。
