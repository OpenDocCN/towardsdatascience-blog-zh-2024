# 构建一个AI驱动的业务管理系统

> 原文：[https://towardsdatascience.com/building-an-ai-powered-business-manager-e2a31a2fe984?source=collection_archive---------3-----------------------#2024-04-23](https://towardsdatascience.com/building-an-ai-powered-business-manager-e2a31a2fe984?source=collection_archive---------3-----------------------#2024-04-23)

![](../Images/dc602a5cdef860b9a6b382b91264e95b.png)

使用 [DALL·E](https://labs.openai.com/s/1rNDsRujptitO6sPd57aWyZp) 创建

## 将AI代理与SQL数据库连接的逐步指南——系列文章的第二部分

[](https://medium.com/@lukas.kowejsza?source=post_page---byline--e2a31a2fe984--------------------------------)[![Lukasz Kowejsza](../Images/8d920478bee9ad674a6c79462128b0db.png)](https://medium.com/@lukas.kowejsza?source=post_page---byline--e2a31a2fe984--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2a31a2fe984--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2a31a2fe984--------------------------------) [Lukasz Kowejsza](https://medium.com/@lukas.kowejsza?source=post_page---byline--e2a31a2fe984--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2a31a2fe984--------------------------------) ·29分钟阅读·2024年4月23日

--

想象一下通过一个简单易用的手机界面来简化整个业务管理。虽然同时使用多个应用程序是常见做法，但未来的趋势是将所有互动整合到一个基于聊天的平台中，这个平台由大型语言模型（LLM）驱动。

对于小型企业来说，这种方法具有显著优势。通过将数据管理任务集中在统一的聊天界面中，企业主可以节省时间，减少复杂性，并最小化对不同软件工具的依赖。最终的结果是资源分配更为高效，能够将更多精力集中在核心业务增长活动上。

然而，这一潜力不仅限于小型企业。本教程中详细介绍的概念和技术同样适用于个人使用案例。从管理待办事项和跟踪开支到整理收藏，基于聊天的界面为与数据交互提供了一种直观且高效的方式。

本文是一个系列文章中的第二篇，旨在引导你完成从最初的概念到实际实现的整个软件开发过程。在上一篇文章中介绍的组件基础上，我们将建立我们应用程序的基础元素，包括：

+   设置数据库架构

+   定义核心应用功能

+   结构化项目仓库

+   创建能够使用自然语言命令与多个SQL数据库表交互的工具

到本教程结束时，你将清楚地理解如何设计一个基于聊天界面的架构，利用大语言模型（LLM）简化数据管理任务。无论你是希望优化运营的小企业主，还是寻求个人组织优化的个体，这里讲解的原则将为你的项目提供一个坚实的起点。

让我们先简要回顾一下上一篇文章的关键要点，为我们当前的目标设定背景。

# 回顾

[在本系列的第一部分](/leverage-openai-tool-calling-building-a-reliable-ai-agent-from-scratch-4e21fcd15b62)中，我们构建了一个原型代理工作流，能够与工具对象进行交互。我们的目标是减少底层语言模型生成的工具参数中的幻觉现象，在我们的案例中是`gpt-3.5-turbo`。

为了实现这一目标，我们实施了两个关键变更：

1.  删除了工具模式中的必需参数

1.  在执行所需功能之前增加了参数验证步骤

通过将所有工具参数设为可选，并手动检查缺失的参数，我们消除了代理/大语言模型生成缺失值的幻觉冲动。

在上一篇文章中介绍的关键对象是：

+   `OpenAiAgent`：主要的代理工作流类

+   `Tool`：一个表示代理可以使用的工具的类

+   `ToolResult` 和 `StepResult`：用于封装工具执行结果的类

这些组件构成了我们代理系统的基础，使其能够处理用户请求，选择合适的工具，并生成响应。

如果你想要更详细的解释或了解特定设计选择背后的原因，可以查看上一篇文章：[利用OpenAI工具调用：从零开始构建可靠的AI代理](https://medium.com/towards-data-science/leverage-openai-tool-calling-building-a-reliable-ai-agent-from-scratch-4e21fcd15b62)

记住这些回顾内容后，让我们进入项目的下一阶段——集成数据库功能以存储和管理业务数据。

# 为什么为小企业数据管理提供聊天界面

小企业在数据维护方面经常面临独特的挑战。与大公司一样，它们需要定期更新和维护各种类型的数据，如会计记录、时间跟踪、发票等。然而，现代ERP（企业资源规划）系统的复杂性和成本对小企业而言可能是一个障碍。因此，许多小企业不得不依赖一系列Excel电子表格来捕捉和维护关键数据。

这种方法的问题在于，小企业主通常并非完全专注于行政任务，无法投入大量时间和精力进行复杂的行政管理和控制流程。关键在于定义精简的流程，并在数据出现时及时更新，最小化数据管理的开销。

通过利用大型语言模型的强大功能并创建聊天界面，我们旨在简化和优化小型企业的数据管理。该聊天机器人将充当统一接口，允许用户输入数据、检索信息，并通过自然语言命令执行各种任务。这消除了需要在多个电子表格之间切换或开发具有多个表单和仪表盘的复杂 web 应用程序的需求。

在这一系列教程中，我们将逐步增强聊天机器人的功能，添加诸如基于角色的访问控制、先进的查询与评估、多模态支持，以及与流行的通讯平台（如 WhatsApp）的集成等功能。到系列结束时，您将拥有一个强大而灵活的工具，能够根据您的具体需求进行调整，无论您是经营一家小型企业，还是仅仅希望更高效地组织个人生活。

让我们开始吧！

# 1\. 项目结构

为了确保项目井井有条并易于维护，我们已经有系统地构建了我们的代码库，封装了不同的功能和组件。以下是代码库结构的概述：

```py
project-root/
│
├── database/
│ ├── db.py # Database connection and setup
│ ├── models.py # Database models/schemas
| └── utils.py # Database utilities
│
├── tools/
│ ├── base.py # Base class for tools
│ ├── add.py # Tool for adding data to the database
│ ├── query.py # Tool for querying data from the database
| └── utils.py # Tool utilities
│
├── agents/
│ ├── base.py # Main AI agent logic
│ ├── routing.py # Specialized agent for routing tasks
│ ├── task.py # Tool wrapper for OpenAI subagents
| └── utils.py # agent utilities
│
└── utils.py # Utility functions and classes
```

这种结构使得关注点分离更加清晰，简化了应用程序的开发、维护和扩展。

# 2\. 设置数据库

选择合适的数据库和 ORM（对象关系映射）库对我们的应用程序至关重要。对于这个项目，我们选择了以下框架：

+   SQLAlchemy：一个强大的 SQL 工具包和 Python 的对象关系映射（ORM）库。它提供了一套与数据库交互的工具，通过 Python 对象和类进行操作。

+   SQLModel：一个构建在 SQLAlchemy 和 Pydantic 之上的库，提供了一种简单直观的方式来定义数据库模型并执行数据库操作。

通过利用 SQLModel，我们可以与 Pydantic 和 SQLAlchemy 无缝集成，既能高效地进行数据验证和数据库操作，又能消除 SQL 注入攻击的风险。此外，SQLModel 使我们能够轻松构建我们之前设计的`Tool`类，该类使用 Pydantic 模型来创建工具模式。

为确保我们应用程序的安全性和稳健性，我们实施了以下措施：

1.  基于角色的访问控制：可执行的操作与用户角色绑定，确保用户只能执行他们被授权的操作。这为系统增加了额外的安全层，防止未经授权访问敏感数据。

1.  防止 SQL 注入攻击：通过利用 ChatGPT 的自然语言理解能力，我们可以验证和清理用户输入，从而减轻 SQL 注入漏洞的风险。SQLModel 与 Pydantic 的集成帮助我们强制执行严格的数据验证规则。

在确定了我们的技术栈后，让我们开始设置数据库并定义我们的模型。

# 2.1 数据库模型

为了开始构建我们的原型应用程序，我们将定义基本的数据库表和相应的SQLModel定义。对于本教程，我们将重点介绍三个核心表：

+   支出

+   收入

+   客户

这些表将作为我们应用程序的基础，允许我们演示关键功能和交互。

在`database`目录下创建一个名为`models.py`的新文件，并使用SQLModel定义表格：

```py
# database\models.py
from typing import Optional  

from pydantic import BeforeValidator, model_validator  
from sqlmodel import SQLModel, Field  
from datetime import time, datetime  
from typing_extensions import Annotated 

def validate_date(v):  
    if isinstance(v, datetime):  
        return v  

    for f in ["%Y-%m-%d", "%Y-%m-%d %H:%M:%S"]:  
        try:  
            return datetime.strptime(v, f)  
        except ValueError:  
            pass  

    raise ValueError("Invalid date format")  

def numeric_validator(v):  
    if isinstance(v, int):  
        return float(v)  
    elif isinstance(v, float):  
        return v  
    raise ValueError("Value must be a number")  

DateFormat = Annotated[datetime, BeforeValidator(validate_date)]  
Numeric = Annotated[float, BeforeValidator(numeric_validator)]

class Customer(SQLModel, table=True):  
    id: Optional[int] = Field(primary_key=True, default=None)
    company: str
    first_name: str  
    last_name: str  
    phone: str  
    address: str  
    city: str  
    zip: str  
    country: str  

class Revenue(SQLModel, table=True):  
    id: Optional[int] = Field(primary_key=True, default=None)  
    description: str  
    net_amount: Numeric  
    gross_amount: Numeric  
    tax_rate: Numeric  
    date: DateFormat  

class Expense(SQLModel, table=True):  
    id: Optional[int] = Field(primary_key=True, default=None)  
    description: str  
    net_amount: Numeric = Field(description="The net amount of the expense")  
    gross_amount: Numeric  
    tax_rate: Numeric  
    date: DateFormat
```

除了标准的SQLModel字段外，我们还定义了三个自定义类型注解：`DateFormat`、`TimeFormat`和`Numeric`。这些注解利用了Pydantic的`BeforeValidator`，以确保在将输入数据存储到数据库之前，它们被正确格式化。`validate_date`函数处理将字符串输入转换为适当的`datetime`。这种方法允许我们接受来自大型语言模型的各种日期格式，从而减少了在提示中对格式的严格要求。

# 2.2 数据库引擎

定义了模型之后，我们需要一个脚本来设置数据库引擎并创建相应的表格。让我们在`database`目录中创建一个`db.py`文件来处理这个任务：

```py
# database/db.py
from database.models import *  
from sqlmodel import SQLModel, create_engine  
import os  

# local stored database  
DATABASE_URL = "sqlite:///app.db"  

engine = create_engine(DATABASE_URL, echo=True) 

def create_db_and_tables():  
    SQLModel.metadata.create_all(engine)  

create_db_and_tables()
```

在这个脚本中，我们导入了我们的模型和必要的SQLModel组件。我们定义了`DATABASE_URL`，它指向名为`app.db`的本地SQLite数据库文件。我们使用SQLModel的`create_engine`创建了一个`engine`，并传入`DATABASE_URL`。`echo=True`参数启用了详细输出，便于调试。

`create_db_and_tables`函数使用`SQLModel.metadata.create_all`根据我们定义的模型生成相应的数据库表。最后，我们调用这个函数以确保在运行脚本时创建数据库和表格。

数据库设置完成后，我们现在可以专注于更新我们的`Tool`类，使其与SQLModel无缝协作，并增强我们的工具架构转换过程。

# 3. 工具类

在这一部分，我们将讨论对`Tool`类所做的更新，以处理SQLModel实例并改进验证过程。如需更详细的`Tool`类说明，请访问我之前的文章。

首先，我们通过`Union`类型提示将`Type[SQLModel]`作为`model`字段的可能类型。这使得`Tool`类能够接受Pydantic的`BaseModel`和SQLModel的`SQLModel`作为有效的模型类型。

接下来，我们引入了一个新的属性`exclude_keys`，其类型为`list[str]`，默认值为`["id"]`。这个属性的目的是指定哪些键应该从验证过程和OpenAI工具架构生成中排除。在这种情况下，默认排除的键是`id`，因为在使用`SqlModel`进行数据录入时，`id`会在数据导入过程中自动生成。

此外，我们在`Tool`类中引入了`parse_model`布尔属性。通过这个属性，我们可以决定工具函数是通过Pydantic/SQLModel调用，还是通过关键字参数调用。

在`validate_input()`方法中，我们添加了一个检查，以确保在验证过程中，`exclude_keys`中指定的键不会被视为缺失键。这对于像`id`这样的字段特别有用，因为这些字段是由SQLModel自动生成的，不应作为输入的必需项。

同样，在`openai_tool_schema`属性中，我们添加了一个循环，以从生成的模式中移除被排除的键。这确保了排除的键不会被包括在发送到OpenAI API的模式中。为了总结，我们使用`openai_tool_schema`属性从我们的工具模式中移除`required`参数。这是为了消除语言模型的幻觉。

此外，我们将导入语句从`from pydantic.v1 import BaseModel`更改为`from pydantic import BaseModel`。由于`SQLModel`基于Pydantic v2，我们希望在此时保持一致，使用Pydantic v2。

这是`Tool`类的更新代码：

```py
# tools/base.py
from typing import Type, Callable, Union

from tools.convert import convert_to_openai_tool
from pydantic import BaseModel, ConfigDict
from sqlmodel import SQLModel

class ToolResult(BaseModel):
    content: str
    success: bool

class Tool(BaseModel):
    name: str
    model: Union[Type[BaseModel], Type[SQLModel], None]
    function: Callable
    validate_missing: bool = True
    parse_model: bool = False
    exclude_keys: list[str] = ["id"]

    model_config = ConfigDict(arbitrary_types_allowed=True)

    def run(self, **kwargs) -> ToolResult:
        if self.validate_missing and model is not None:
            missing_values = self.validate_input(**kwargs)
            if missing_values:
                content = f"Missing values: {', '.join(missing_values)}"
                return ToolResult(content=content, success=False)

        if self.parse_model:
            if hasattr(self.model, "model_validate"):
                input_ = self.model.model_validate(kwargs)
            else:
                input_ = self.model(**kwargs)
            result = self.function(input_)

        else:
            result = self.function(**kwargs)
        return ToolResult(content=str(result), success=True)

    def validate_input(self, **kwargs):
        if not self.validate_missing or not self.model:
            return []
        model_keys = set(self.model.__annotations__.keys()) - set(self.exclude_keys)
        input_keys = set(kwargs.keys())
        missing_values = model_keys - input_keys
        return list(missing_values)

    @property
    def openai_tool_schema(self):
        schema = convert_to_openai_tool(self.model)
        # set function name
        schema["function"]["name"] = self.name

        # remove required field
        if schema["function"]["parameters"].get("required"):
            del schema["function"]["parameters"]["required"]
        # remove exclude keys
        if self.exclude_keys:
            for key in self.exclude_keys:
                if key in schema["function"]["parameters"]["properties"]:
                    del schema["function"]["parameters"]["properties"][key]
        return schema
```

这些对`Tool`类的更新提供了更多的灵活性和控制力，能够在处理SQLModel实例时进行更精细的验证过程和模式生成。

# 3.1 自定义工具模式转换

在我们的`Tool`类中，我们使用`convert_to_openai_tool`函数从Langchain创建一个Pydantic模型的模式。然而，这个函数是基于Pydantic v1的，而SQLModel使用的是Pydantic v2。为了使转换函数兼容，我们需要对其进行调整。让我们创建一个新的脚本，命名为`convert.py`：

```py
# tools/convert.py
from langchain_core.utils.function_calling import _rm_titles
from typing import Type, Optional
from langchain_core.utils.json_schema import dereference_refs
from pydantic import BaseModel

def convert_to_openai_tool(
        model: Type[BaseModel],
        *,
        name: Optional[str] = None,
        description: Optional[str] = None,
) -> dict:
    """Converts a Pydantic model to a function description for the OpenAI API."""
    function = convert_pydantic_to_openai_function(
        model, name=name, description=description
    )
    return {"type": "function", "function": function}

def convert_pydantic_to_openai_function(
        model: Type[BaseModel],
        *,
        name: Optional[str] = None,
        description: Optional[str] = None,
        rm_titles: bool = True,
) -> dict:
    """Converts a Pydantic model to a function description for the OpenAI API."""

    model_schema = model.model_json_schema() if hasattr(model, "model_json_schema") else model.schema()

    schema = dereference_refs(model_schema)
    schema.pop("definitions", None)
    title = schema.pop("title", "")
    default_description = schema.pop("description", "")
    return {
        "name": name or title,
        "description": description or default_description,
        "parameters": _rm_titles(schema) if rm_titles else schema,
    }
```

这个调整后的转换函数处理了Pydantic v1和v2之间的差异，确保我们的`Tool`类能够生成与OpenAI API兼容的模式。

接下来，在`tools/base.py`中更新导入语句，以使用新的`convert_to_openai_tool`函数：

```py
# tools/base.py
from typing import Type, Callable, Union

from tools.convert import convert_to_openai_tool
from pydantic import BaseModel
from sqlmodel import SQLModel
#...rest of the code ...
```

通过这些更改，我们的`Tool`类现在可以处理SQLModel实例并生成与OpenAI API兼容的模式。

> *注意：如果遇到依赖问题，您可以考虑完全移除Langchain依赖，并直接在`convert.py`文件中包含`*_rm_titles*`和`*dereference_refs*`函数。*

通过调整工具模式转换过程，我们确保了我们的应用能够与SQLModel和Pydantic v2无缝协作，使我们能够在保持与OpenAI API兼容的同时，利用这些库的优势。

# 4. 定义SQL工具

在本节中，我们将创建函数和工具，以便使用SQL与我们的数据库表进行交互。

## 4.1 添加数据工具

首先，让我们定义一个通用函数`add_row_to_table`，它接受一个SQLModel实例并将其添加到相应的表中：

```py
# tools/add.py
from sqlmodel import SQLModel, Session, select

def add_row_to_table(model_instance: SQLModel):  
    with Session(engine) as session:  
        session.add(model_instance)  
        session.commit()  
        session.refresh(model_instance)  
    return f"Successfully added {model_instance} to the table"
```

接下来，我们将创建一个特定模型的函数`add_expense_to_table`，它接受支出条目的输入参数并将其添加到表中：

```py
# tools/add.py
# ...
def add_expense_to_table(**kwargs):
    model_instance = Expense.model_validate(kwargs)
    return add_row_to_table(model_instance)
```

在`add_expense_to_table`中，我们使用`model_validate()`方法触发之前定义的BeforeValidator的执行，并确保数据验证。

为了避免为每个表或SQLModel编写单独的函数，我们可以动态生成这些函数：

```py
# example usage

def add_entry_to_table(sql_model: Type[SQLModel]):  
    # return a Callable that takes a SQLModel instance and adds it to the table  
    return lambda **data: add_row_to_table(model_instance=sql_model.model_validate(data))

add_expense_to_table = add_entry_to_table(Expense)
```

这种方法产生相同的结果，并且可以用来动态生成所有其他模型的函数。

在这些功能就位后，我们可以使用我们的`Tool`类创建工具，通过OpenAIAgent向数据库表中添加条目：

```py
add_expense_tool = Tool(
 name="add_expense_tool",
 description="useful for adding expenses to database",
 function=add_entry_to_table(Expense),
 model=Expense,
 validate_missing=True
)

add_revenue_tool = Tool(
 name="add_revenue_tool",
 description="useful for adding revenue to database",
 function=add_entry_to_table(Revenue),
 model=Revenue,
 validate_missing=True
)
```

# 4.2 查询工具

虽然我们需要为每个表创建一个add_xxx_tool，因为输入模式不同，但我们只需要一个查询工具来查询所有表。为了消除SQL注入的风险，我们将使用SQLAlchemy和SQLModel提供的SQL清理功能。这意味着我们将通过标准的Python类和对象来查询数据库，而不是直接解析SQL语句。

对于我们想在表上执行的查询，我们需要以下逻辑：

1.  **select语句** -> `SELECT * FROM table_name` 参数：`columns`、`table_name`

1.  **where语句** -> `WHERE column_name = value`

    参数：`column`、`operator`、`value`

在SQLModel中，当我们想在`Expense`表中查找所有咖啡的支出时，这对应于以下清理过的代码：

```py
result = database.execute(
  select(Expense).where(Expense.description == "Coffee")
)
```

将其抽象为一个pydantic模型：

```py
# tools/query.py
from typing import Union, Literal
from pydantic import BaseModel

class WhereStatement(BaseModel):  
    column: str  
    operator: Literal["eq", "gt", "lt", "gte", "lte", "ne", "ct"]  
    value: str  

class QueryConfig(BaseModel):  
    table_name: str  
    columns: list[str]  
    where: list[Union[WhereStatement, None]]
```

`QueryConfig`模型允许我们设置`table_name`、`columns`和`where`语句。`where`属性接受一个`WhereStatement`模型的列表或一个空列表（当我们想返回所有值且不进行进一步筛选时）。`WhereStatement`是一个子模型，定义了列、操作符和值。`Literal`类型用于将允许的操作符限制为预定义的集合。

接下来，我们定义一个根据`QueryConfig`执行查询的函数：

```py
# tools/query.py
# ...
from database.models import Expense, Revenue, Customer

TABLES = {
    "expense": Expense,
    "revenue": Revenue,
    "customer": Customer
}

def query_data_function(**kwargs) -> ToolResult:  
    """Query the database via natural language."""
    query_config = QueryConfig.model_validate(kwargs)

    if query_config.table_name not in TABLES:  
        return ToolResult(content=f"Table name {query_config.table_name} not found in database models", success=False)  

    sql_model = TABLES[query_config.table_name]  

    # query_config = validate_query_config(query_config, sql_model)  
    data = sql_query_from_config(query_config, sql_model)  

    return ToolResult(content=f"Query results: {data}", success=True)  

def sql_query_from_config(  
        query_config: QueryConfig,  
        sql_model: Type[SQLModel]):  

    with Session(engine) as session:  
        selection = []  
        for column in query_config.select_columns:  
            if column not in sql_model.__annotations__:  
                return f"Column {column} not found in model {sql_model.__name__}"  
            selection.append(getattr(sql_model, column))  

        statement = select(*selection)  
        wheres = query_config.where  
        if wheres:  
            for where in wheres:  

                if where.column not in sql_model.__annotations__:  # noqa  
                    return (f"Column {where['column']} not found "
                       "in model {sql_model.__name__}")

                elif where.operator == "eq":  
                    statement = statement.where(
                     getattr(sql_model, where.column) == where.value)  
                elif where.operator == "gt":  
                    statement = statement.where(
                     getattr(sql_model, where.column) > where.value)  
                elif where.operator == "lt":  
                    statement = statement.where(
                     getattr(sql_model, where.column) < where.value)  
                elif where.operator == "gte":  
                    statement = statement.where(
                     getattr(sql_model, where.column) >= where.value)  
                elif where.operator == "lte":  
                    statement = statement.where(
                     getattr(sql_model, where.column) <= where.value)  
                elif where.operator == "ne":  
                    statement = statement.where(
                     getattr(sql_model, where.column) != where.value)  
                elif where.operator == "ct":  
                    statement = statement.where(
                     getattr(sql_model, where.column).contains(where.value))  

        result = session.exec(statement)  
        data = result.all()  
        try:  
            data = [repr(d) for d in data]  
        except:  
            pass  
    return data
```

`query_data_function`作为从`TABLES`字典中选择我们的表模型的高级抽象，而`sql_query_from_config`是执行`QueryConfig`查询表（SQLModel）的底层函数。

> 在`QueryConfig`中，你可以选择将`table_names`定义为Literal类型，其中将可用的表名硬编码进去。你甚至可以通过我们的TABLES字典动态定义Literal。通过这样做，可以减少对`table_name`的错误传参。目前我选择不使用枚举对象，因为我将向代理提供关于当前可用表及其底层ORM架构的上下文信息。我计划为我们未来的代理添加一个工具，使其能够自己创建新表。虽然我可以动态更改代理的提示，但在运行中的服务器中更改`QueryConfig`中的枚举对象将不是一件简单的事。

最后，我们可以定义我们的查询工具：

```py
query_data_tool = Tool(  
    name="query_data_tool",  
    description = "useful to perform queries on a database table",
    model=QueryConfig,  
    function=query_data_function, 
)
```

有了这些工具，我们的OpenAIAgent现在能够使用自然语言命令向数据库表中添加和查询数据。

# 5. 配置代理

为了使我们之前定义的工具能够成功使用，前一篇文章中的 Agent 需要更多的上下文信息，尤其是用于查询工具时。Agent 的提示需要包括可用表格及其架构信息。由于目前我们只使用两个表格，我们可以在系统提示或用户提示中包含 ORM 架构和表格名称。这两种方式都可以，但我更倾向于在用户提示中包含像这样的变量信息。通过这样做，我们可以创建少量示例，演示基于上下文的工具使用。

为了使我们的 Agent 能够处理系统提示和用户提示中的变量上下文，我们可以按以下方式更新我们的 Agent 类：

```py
import colorama  
from colorama import Fore  
from openai import OpenAI  
from pydantic import BaseModel  
from tools.base import Tool, ToolResult  
from agents.utils import parse_function_args, run_tool_from_response  

class StepResult(BaseModel):  
    event: str  
    content: str  
    success: bool  

SYSTEM_MESSAGE = """You are tasked with completing specific objectives and must report the outcomes. At your disposal, you have a variety of tools, each specialized in performing a distinct type of task.  

For successful task completion:  
Thought: Consider the task at hand and determine which tool is best suited based on its capabilities and the nature of the work. If you can complete the task or answer a question, soley by the information provided you can use the report_tool directly.  

Use the report_tool with an instruction detailing the results of your work or to answer a user question.  
If you encounter an issue and cannot complete the task:  

Use the report_tool to communicate the challenge or reason for the task's incompletion.  
You will receive feedback based on the outcomes of each tool's task execution or explanations for any tasks that couldn't be completed. This feedback loop is crucial for addressing and resolving any issues by strategically deploying the available tools.  

Return only one tool call at a time.  

{context}  
"""

class OpenAIAgent:  

    def __init__(  
            self,  
            tools: list[Tool],  
            client: OpenAI = OpenAI(),  
            system_message: str = SYSTEM_MESSAGE,  
            model_name: str = "gpt-3.5-turbo-0125",  
            max_steps: int = 5,  
            verbose: bool = True,  
            examples: list[dict] = None,  
            context: str = None,  
            user_context: str = None  
    ):  
        self.tools = tools  
        self.client = client  
        self.model_name = model_name  
        self.system_message = system_message  
        self.step_history = []  
        self.max_steps = max_steps  
        self.verbose = verbose  
        self.examples = examples or []  
        self.context = context or ""  
        self.user_context = user_context  

    def to_console(self, tag: str, message: str, color: str = "green"):  
        if self.verbose:  
            color_prefix = Fore.__dict__[color.upper()]  
            print(color_prefix + f"{tag}: {message}{colorama.Style.RESET_ALL}")  

    def run(self, user_input: str, context: str = None):  

        openai_tools = [tool.openai_tool_schema for tool in self.tools]  
        system_message = self.system_message.format(context=context)  

        if self.user_context:  
            context = f"{self.user_context}\n{context}" if context else self.user_context  

        if context:  
            user_input = f"{context}\n---\n\nUser Message: {user_input}"  

        self.to_console("START", f"Starting Agent with Input:\n'''{user_input}'''")  

        self.step_history = [  
            {"role": "system", "content": system_message},  
            *self.examples,  
            {"role": "user", "content": user_input}  
        ]  

        step_result = None  
        i = 0  

        while i < self.max_steps:  
            step_result = self.run_step(self.step_history, openai_tools)  
            if step_result.event == "finish":  
                break  
            elif step_result.event == "error":  
                self.to_console(step_result.event, step_result.content, "red")  
            else:  
                self.to_console(step_result.event, step_result.content, "yellow")  

            i += 1  

        self.to_console("Final Result", step_result.content, "green")  

        return step_result.content  

    def run_step(self, messages: list[dict], tools):  

        # plan the next step  
        response = self.client.chat.completions.create(  
            model=self.model_name,  
            messages=messages,  
            tools=tools  
        )  
        # check for multiple tool calls  
        if response.choices[0].message.tool_calls and len(response.choices[0].message.tool_calls) > 1:  
            messages = [  
                *self.step_history,  
                {"role": "user", "content": "Error: Please return only one tool call at a time."}  
            ]  
            return self.run_step(messages, tools)  

        # add message to history  
        self.step_history.append(response.choices[0].message)  
        # check if tool call is present  
        if not response.choices[0].message.tool_calls:  
            msg = response.choices[0].message.content  
            step_result = StepResult(event="Error", content=f"No tool calls were returned.\nMessage: {msg}", success=False)  
            return step_result  

        tool_name = response.choices[0].message.tool_calls[0].function.name  
        tool_kwargs = parse_function_args(response)  

        # execute the tool call  
        self.to_console("Tool Call", f"Name: {tool_name}\nArgs: {tool_kwargs}", "magenta")  
        tool_result = run_tool_from_response(response, tools=self.tools)  
        tool_result_msg = self.tool_call_message(response, tool_result)  
        self.step_history.append(tool_result_msg)  

        if tool_name == "report_tool":  
            try:  
                step_result = StepResult(  
                    event="finish",  
                    content=tool_result.content,  
                    success=True  
                )  
            except:  
                print(tool_result)  
                raise ValueError("Report Tool failed to run.")  

            return step_result  

        elif tool_result.success:  
            step_result = StepResult(  
                event="tool_result",  
                content=tool_result.content,  
                success=True)  
        else:  
            step_result = StepResult(  
                event="error",  
                content=tool_result.content,  
                success=False  
            )  

        return step_result  

    def tool_call_message(self, response, tool_result: ToolResult):  
        tool_call = response.choices[0].message.tool_calls[0]  
        return {  
            "tool_call_id": tool_call.id,  
            "role": "tool",  
            "name": tool_call.function.name,  
            "content": tool_result.content,  
        }
```

相较于我们之前的版本，主要的变化如下：

+   我们在默认的系统提示中放置了一个“{context}”占位符。

+   我们将`context`和`user_context`作为输入参数添加到`__init__()`中。

+   我们将`context`添加到`run()`方法中。

+   在`run()`中，如果定义了`context`，我们将其添加到用户消息中。

+   我们还在`__init__()`中添加了一个`examples`属性，如果设置了它，将在`run()`中的系统和用户消息之间传递。

现在我们可以在初始化代理时定义系统上下文和用户上下文。此外，我们还可以在调用 run 方法时传递用户上下文。如果`context`被传递给 run 方法，它将覆盖初始化时的`user_context`，只在本次运行中有效。

# 5.1 向 Agent 提供上下文

在我们能够运行 Agent 之前，让我们定义一个生成上下文信息的函数。我们希望自动生成`user_context`，然后将其传递给如上所述的 Agent 的 run 函数。为了简单起见，我们希望每个表格的上下文信息仅用一行来表示，内容应该包括：

+   表格名称

+   列名称：`<type>`

经过几次尝试和错误，以下函数可以完成这个任务：

```py
# utils.py
from typing import Type  
import types  
import typing  

import sqlalchemy  
from pydantic import BaseModel

def orm_model_to_string(input_model_cls: Type[BaseModel]):  
    """Get the ORM model string from the input model"""  

    def process_field(key, value):  
        if key.startswith("__"):  
            return None  
        if isinstance(value, typing._GenericAlias):  
            if value.__origin__ == sqlalchemy.orm.base.Mapped:  
                return None  
            if isinstance(value, typing._AnnotatedAlias):  # noqa  
                return key, value.__origin__  
            elif isinstance(value, typing._UnionGenericAlias) or isinstance(value, types.UnionType):  
                return key, value.__args__[0]  
        return key, value  

    fields = dict(filter(None, (process_field(k, v) for k, v in input_model_cls.__annotations__.items())))  
    return ", ".join([f"{k} = <{v.__name__}>" for k, v in fields.items()])

def generate_context(*table_models) -> str:
   context_str = "You can access the following tables in database:\n"
   for table in table_models:
    context_str += f" - {table.__name__}: {orm_model_to_string(table)}\n" 
   return context_str
```

如果我们将`Expense`和`Revenue`传递给`generate_context()`，我们应该得到以下上下文字符串：

我们希望 Agent 知道当前的日期和星期几，以便我们可以引用正确的日期。因此，接下来我们将在工具类中添加一些日期解析函数：

```py
# utils.py
from datetime import datetime

#... rest of utils.py ...

def weekday_by_date(date: datetime):
    days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
    return days[date.weekday()]

def date_to_string(date: datetime):
    return f"{weekday_by_date(date)} {parse_date(date)}"

def parse_date(date: datetime):
    return date.strftime("%Y-%m-%d")
```

现在让我们为查询代理创建上下文

```py
# utils.py

# ...

def generate_query_context(*table_models) -> str:
    today = f"Today is {date_to_string(datetime.now())}"
    context_str = "You can access the following tables in database:\n"
    for table in table_models:
        context_str += f" - {table.__name__}: {orm_model_to_string(table)}\n" 
    return f"{today}\n{context_str}"
```

```py
from database.models import Expense, Revenue
print(generate_query_context(Expense, Revenue))
```

```py
Today is Sunday 2024-04-21
You can access the following tables in database:
 - Expense: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>
 - Revenue: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>
```

# 5.2 路由 Agent

随着我们添加更多的工具，我们的设置复杂性可能会开始限制像“gpt-3.5-turbo”这样的便宜模型的可用性。在下一篇文章中，我们可能会考虑切换到 Anthropic Claude，因为它们新发布的工具使用 API 功能似乎在同时处理多个工具时很有前景，甚至适用于更便宜的 HAIKU 模型。然而，目前我们将继续使用 OpenAI 的 GPT 模型。

在进行个人使用的开发并创建生产就绪应用程序之前，我发现优化工作流程以适应较小的模型（如本例中的`gpt-3.5-turbo`）是非常有用的。这种方法迫使我们创建一个精简的处理逻辑和提示系统。虽然我们可能无法在不使用最强大模型的情况下实现100%的可靠性，但我们能够发现缺陷并识别不清晰的指令。如果您的应用在10次中有9次能在较小模型下正常工作，您就拥有了一套生产就绪的逻辑，并且使用更强大的模型时，系统的表现会更好。

为了使多工具处理在`gpt-3.5-turbo`下更可靠，我们将实现一个路由代理，它的唯一目的是将用户查询路由到适当的任务代理。这使我们能够分离执行逻辑，减少复杂性。每个代理将有一个有限的范围，使我们能够在未来分离访问角色和操作。我观察到即使在使用gpt-4时，也会出现代理不知道何时任务完成的情况。

通过引入路由代理，我们可以将问题分解成更小、更易管理的部分。路由代理将负责理解用户的意图，并将查询引导到相关的任务代理。这种方法不仅简化了各个代理的职责，还使系统更加模块化，更易于维护。

此外，分离执行逻辑和复杂性将为未来实现基于角色的访问控制铺平道路。每个任务代理可以被分配特定的权限和访问级别，确保敏感操作仅由授权代理执行。

虽然路由代理在流程中增加了一个额外的步骤，但它最终会导致一个更强大、更具可扩展性的系统。通过优化较小的模型并专注于清晰、简洁的提示，我们可以创建一个坚实的基础，在切换到更强大的模型如Claude Opus或GPT-4时，系统的性能会更好。

让我们来看看路由代理的实现

```py
# agents/routing.py
from openai import OpenAI
import colorama
from agents.task_agent import TaskAgent
from agents.utils import parse_function_args

SYSTEM_MESSAGE = """You are a helpful assistant.
Role: You are an AI Assistant designed to serve as the primary point of contact for users interacting through a chat interface. 
Your primary role is to understand users' requests related to database operations and route these requests to the appropriate tool.

Capabilities: 
You have access to a variety of tools designed for Create, Read operations on a set of predefined tables in a database. 

Tables:
{table_names}
"""

NOTES = """Important Notes:
Always confirm the completion of the requested operation with the user.
Maintain user privacy and data security throughout the interaction.
If a request is ambiguous or lacks specific details, ask follow-up questions to clarify the user's needs."""

class RoutingAgent:

    def __init__(
            self,
            tools: list[TaskAgent] = None,
            client: OpenAI = OpenAI(),
            system_message: str = SYSTEM_MESSAGE,
            model_name: str = "gpt-3.5-turbo-0125",
            max_steps: int = 5,
            verbose: bool = True,
            prompt_extra: dict = None,
            examples: list[dict] = None,
            context: str = None
    ):
        self.tools = tools or ROUTING_AGENTS
        self.client = client
        self.model_name = model_name
        self.system_message = system_message
        self.memory = []
        self.step_history = []
        self.max_steps = max_steps
        self.verbose = verbose
        self.prompt_extra = prompt_extra or PROMPT_EXTRA
        self.examples = self.load_examples(examples)
        self.context = context or ""

    def load_examples(self, examples: list[dict] = None):
        examples = examples or []
        for agent in self.tools:
            examples.extend(agent.routing_example)
        return examples

    def run(self, user_input: str, employee_id: int = None, **kwargs):
        context = create_routing_agent_context(employee_id)
        if context:
            user_input_with_context = f"{context}\n---\n\nUser Message: {user_input}"
        else:
            user_input_with_context = user_input

        self.to_console("START", f"Starting Task Agent with Input:\n'''{user_input_with_context}'''")
        partial_variables = {**self.prompt_extra, "context": context}
        system_message = self.system_message.format(**partial_variables)

        messages = [
            {"role": "system", "content": system_message},
            *self.examples,
            {"role": "user", "content": user_input}
        ]

        tools = [tool.openai_tool_schema for tool in self.tools]

        response = self.client.chat.completions.create(
            model=self.model_name,
            messages=messages,
            tools=tools
        )
        self.step_history.append(response.choices[0].message)
        self.to_console("RESPONSE", response.choices[0].message.content, color="blue")
        tool_kwargs = parse_function_args(response)
        tool_name = response.choices[0].message.tool_calls[0].function.name
        self.to_console("Tool Name", tool_name)
        self.to_console("Tool Args", tool_kwargs)

        agent = self.prepare_agent(tool_name, tool_kwargs)
        return agent.run(user_input)

    def prepare_agent(self, tool_name, tool_kwargs):
        for agent in self.tools:
            if agent.name == tool_name:
                input_kwargs = agent.arg_model.model_validate(tool_kwargs)
                return agent.load_agent(**input_kwargs.dict())
        raise ValueError(f"Agent {tool_name} not found")

    def to_console(self, tag: str, message: str, color: str = "green"):
        if self.verbose:
            color_prefix = colorama.Fore.__dict__[color.upper()]
            print(color_prefix + f"{tag}: {message}{colorama.Style.RESET_ALL}")
```

与我们的`OpenAIAgent`相比，最大的区别在于：

+   **无开环：**我们希望路由代理将用户的查询路由到适当的代理。因此，我们通过工具调用选择所需的代理，并将用户查询传递给它，而不是创建一个开环。路由代理不应该执行其他任务或后续问题。

+   **代理作为工具**：与其称一个工具为路由代理，不如设置一个子代理。因此，我们之前定义的`OpenAIAgent`现在成为路由代理中的一个工具。

# 5.3 代理作为工具——任务代理

为了将我们的`OpenAIAgent`作为工具使用，我们需要引入某种专门为代理设计的工具类。我们希望为每个代理定义一个名称和描述，并自动化初始化过程。因此，我们为本教程定义了最后一个类——`TaskAgent`。

`TaskAgent`类的功能与`Tool`类类似。我们定义了一个名称、描述和一个输入模型，我们称之为`arg_model`。

```py
from typing import Type, Callable, Optional

from agents.base import OpenAIAgent
from tools.base import Tool
from tools.report_tool import report_tool
from pydantic import BaseModel, ConfigDict, Field

from tools.utils import convert_to_openai_tool

SYSTEM_MESSAGE = """You are tasked with completing specific objectives and must report the outcomes. At your disposal, you have a variety of tools, each specialized in performing a distinct type of task.

For successful task completion:
Thought: Consider the task at hand and determine which tool is best suited based on its capabilities and the nature of the work. 
If you can complete the task or answer a question, soley by the information provided you can use the report_tool directly.

Use the report_tool with an instruction detailing the results of your work or to answer a user question.
If you encounter an issue and cannot complete the task:

Use the report_tool to communicate the challenge or reason for the task's incompletion.
You will receive feedback based on the outcomes of each tool's task execution or explanations for any tasks that couldn't be completed. This feedback loop is crucial for addressing and resolving any issues by strategically deploying the available tools.

On error: If information are missing consider if you can deduce or calculate the missing information and repeat the tool call with more arguments.

Use the information provided by the user to deduct the correct tool arguments.
Before using a tool think about the arguments and explain each input argument used in the tool. 
Return only one tool call at a time! Explain your thoughts!
{context}
"""

class EmptyArgModel(BaseModel):
    pass

class TaskAgent(BaseModel):
    name: str
    description: str
    arg_model: Type[BaseModel] = EmptyArgModel

    create_context: Callable = None
    create_user_context: Callable = None
    tool_loader: Callable = None

    system_message: str = SYSTEM_MESSAGE
    tools: list[Tool]
    examples: list[dict] = None
    routing_example: list[dict] = Field(default_factory=list)

    model_config = ConfigDict(arbitrary_types_allowed=True)

    def load_agent(self, **kwargs) -> OpenAIAgent:

        input_kwargs = self.arg_model(**kwargs)
        kwargs = input_kwargs.dict()

        context = self.create_context(**kwargs) if self.create_context else None
        user_context = self.create_user_context(**kwargs) if self.create_user_context else None

        if self.tool_loader:
            self.tools.extend(self.tool_loader(**kwargs))

        if report_tool not in self.tools:
            self.tools.append(report_tool)

        return OpenAIAgent(
            tools=self.tools,
            context=context,
            user_context=user_context,
            system_message=self.system_message,
            examples=self.examples,
        )

    @property
    def openai_tool_schema(self):
        return convert_to_openai_tool(self.arg_model, name=self.name, description=self.description)
```

此外，我们将所有相关属性添加到我们的`TaskAgent`类中，这是我们需要为底层专用`OpenAIAgent`所做的：

+   `create_context` / `create_user_context`：在这里，我们可以传递一个函数来创建上下文或用户上下文，就像在第5.1节中一样。

+   `tool_loader`是另一个可调用函数，我们可能需要它来设置底层代理。正如我们之前解释的动态工具构建，我们可能需要根据用户输入或路由代理输入动态构建的工具。

+   `system_message`是代理的系统提示。在我们的示例中，它将是每个代理的默认系统提示，但它也可以是每个专门代理的优化版本。

+   `tools`：代理应该使用的预定义工具。

+   `examples`：包括在子代理消息历史中的示例。

+   `routing_example`：包括在路由代理消息历史中的示例。

此外，我们有一个空的BaseModel，叫做`EmptyArgModel`，它是我们`TaskAgent`中的默认`arg_model`。

![](../Images/296be241ea21139c029a09fa600081de.png)

作者创建：[mermaid](https://mermaid.live/)

让我们看看它是否都能协同工作！

# 运行代理

现在，是时候测试我们的路由和子代理是否能够很好地协作。由于我们引入了作为参数的示例，我们可以通过多次测试运行来检查执行中的主要缺陷，并为每个子代理定义示例用法。

让我们先定义我们的子代理：

```py
from database.models import Expense, Revenue, Customer
from agents.task import TaskAgent
from utils import generate_query_context

from tools.base import Tool
from tools.query import query_data_tool
from tools.add import add_entry_to_table

query_task_agent = TaskAgent(
    name="query_agent",
    description="An agent that can perform queries on multiple data sources",
    create_user_context=lambda: generate_query_context(Expense, Revenue, Customer),
    tools=[query_data_tool]
)

add_expense_agent = TaskAgent(
    name="add_expense_agent",
    description="An agent that can add an expense to the database",
    create_user_context=lambda: generate_query_context(Expense) + "\nRemarks: The tax rate is 0.19\. The user provide the net amount you need to calculate the gross amount.",
    tools=[
        Tool(
            name="add_expense",
            description="Add an expense to the database",
            function=add_entry_to_table(Expense),
            model=Expense
        )
    ]
)

add_revenue_agent = TaskAgent(
    name="add_revenue_agent",
    description="An agent that can add a revenue entry to the database",
    create_user_context=lambda: generate_query_context(Revenue) + "\nRemarks: The tax rate is 0.19\. The user provide the gross_amount you should use the tax rate to calculate the net_amount.",
    tools=[
        Tool(
            name="add_revenue",
            description="Add a revenue entry to the database",
            function=add_entry_to_table(Revenue),
            model=Revenue
        )
    ]
)

add_customer_agent = TaskAgent(
    name="add_customer_agent",
    description="An agent that can add a customer to the database",
    create_user_context=lambda: generate_query_context(Customer),
    tools=[
        Tool(
            name="add_customer",
            description="Add a customer to the database",
            function=add_entry_to_table(Customer),
            model=Customer
        )
    ]
)
```

正如您所看到的，我们为收入和支出代理添加了一些备注字符串到`create_user_context`。我们希望子代理能够处理税率，并自动计算净额或毛额，以测试我们的子代理的推理能力。

```py
from agents.routing import RoutingAgent

routing_agent = RoutingAgent(
    tools=[
        query_task_agent,
        add_expense_agent,
        add_revenue_agent,
        add_customer_agent
    ]
)

routing_agent.run("I have spent 5 € on a office stuff. Last Thursday")
```

```py
START: Starting Routing Agent with Input:
I have spent 5 € on a office stuff. Last Thursday

Tool Name: add_expense_agent
Tool Args: {}

START: Starting Task Agent with Input:
"""Today is Sunday 2024-04-21
You can access the following tables in database:
 - expense: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>

Remarks: The tax rate is 0.19\. The user provide the net amount you need to calculate the gross amount.
---
User Message: I have spent 5 € on a office stuff. Last Thursday"""

Tool Call: Name: add_expense
Args: {'description': 'office stuff', 'net_amount': 5, 'tax_rate': 0.19, 'date': '2024-04-18'}
Message: None
error: Missing values: gross_amount

Tool Call: Name: add_expense
Args: {'description': 'office stuff', 'net_amount': 5, 'tax_rate': 0.19, 'date': '2024-04-18', 'gross_amount': 5.95}
Message: None
tool_result: Successfully added net_amount=5.0 id=2 gross_amount=5.95 description='office stuff' date=datetime.datetime(2024, 4, 18, 0, 0) tax_rate=0.19 to the table

Error: No tool calls were returned.
Message: I have successfully added the expense for office stuff with a net amount of 5€, calculated the gross amount, and recorded it in the database.

Tool Call: Name: report_tool
Args: {'report': 'Expense for office stuff with a net amount of 5€ has been successfully added. Gross amount calculated as 5.95€.'}
Message: None

Final Result: Expense for office stuff with a net amount of 5€ has been successfully added. Gross amount calculated as 5.95€.
```

现在让我们添加一笔收入：

```py
routing_agent.run("Two weeks ago on Saturday we had a revenue of 1000 € in the shop")
```

```py
START: Starting Routing Agent with Input:
Two weeks ago on Saturday we had a revenue of 1000 € in the shop

Tool Name: add_revenue_agent
Tool Args: {}

START: Starting Task Agent with Input:
"""Today is Sunday 2024-04-21
You can access the following tables in database:
 - revenue: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>

Remarks: The tax rate is 0.19\. The user provide the gross_amount you should use the tax rate to calculate the net_amount.
---
User Message: Two weeks ago on Saturday we had a revenue of 1000 € in the shop"""

Tool Call: Name: add_revenue
Args: {'description': 'Revenue from the shop', 'gross_amount': 1000, 'tax_rate': 0.19, 'date': '2024-04-06'}
Message: None
error: Missing values: net_amount

Tool Call: Name: add_revenue
Args: {'description': 'Revenue from the shop', 'gross_amount': 1000, 'tax_rate': 0.19, 'date': '2024-04-06', 'net_amount': 840.34}
Message: None
tool_result: Successfully added net_amount=840.34 gross_amount=1000.0 tax_rate=0.19 description='Revenue from the shop' id=1 date=datetime.datetime(2024, 4, 6, 0, 0) to the table

Error: No tool calls were returned.
Message: The revenue entry for the shop on April 6, 2024, with a gross amount of 1000€ has been successfully added to the database. The calculated net amount after applying the tax rate is 840.34€.

Tool Call: Name: report_tool
Args: {'report': 'completed'}
Message: None

Final Result: completed
```

对于最后的测试，让我们尝试查询从数据库中创建的收入：

```py
routing_agent.run("How much revenue did we made this month?")
```

```py
START: Starting Routing Agent with Input:
How much revenue did we made this month?

Tool Name: query_agent
Tool Args: {}

START: Starting Agent with Input:
"""Today is Sunday 2024-04-21
You can access the following tables in database:
 - expense: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>
 - revenue: id = <int>, description = <str>, net_amount = <float>, gross_amount = <float>, tax_rate = <float>, date = <datetime>
 - customer: id = <int>, company_name = <str>, first_name = <str>, last_name = <str>, phone = <str>, address = <str>, city = <str>, zip = <str>, country = <str>

---

User Message: How much revenue did we made this month?"""

Tool Call: Name: query_data_tool
Args: {'table_name': 'revenue', 'select_columns': ['gross_amount'], 'where': [{'column': 'date', 'operator': 'gte', 'value': '2024-04-01'}, {'column': 'date', 'operator': 'lte', 'value': '2024-04-30'}]}
Message: None
tool_result: content="Query results: ['1000.0']" success=True

Error: No tool calls were returned.
Message: The revenue made this month is $1000.00.

Tool Call: Name: report_tool
Args: {'report': 'The revenue made this month is $1000.00.'}
Message: None

Final Result: The revenue made this month is $1000.00.
```

所有工具都按预期工作。路由代理运行得非常完美。对于任务代理，我不得不多次更新提示。

我建议在没有使用像gpt-4这样的最新模型时，为每个任务代理添加一些示例工具调用。一般来说，我建议通过示例和更直观的设计来解决缺陷，而不是使用提示工程。重复出现的缺陷是设计不够直观的信号。例如，当代理在计算毛额或净额时遇到困难，只需添加一个‘calculate_gross_amount_tool’或‘calculate_net_amount_tool’。另一方面，GPT-4会毫不犹豫地处理这种用例。

# 结论

在本文中，我们在创建一个全面的基于聊天的界面以管理小型企业的过程中迈出了重要的一步，利用大型语言模型（Large Language Models）。

通过设置我们的数据库模式、定义核心功能和构建项目仓库，我们为应用程序的开发奠定了坚实的基础。

我们从使用 SQLModel 设计数据库模型开始，这使我们能够无缝地与 Pydantic 和 SQLAlchemy 集成。该方法确保了高效的数据验证和数据库操作，同时最大限度地减少了 SQL 注入攻击的风险。

然后，我们更新了我们的 `Tool` 类，以处理 SQLModel 实例并改进验证过程。接着，我们实现了 SQL 工具，用于向我们的数据库表中添加数据，并使用自然语言命令查询数据。通过利用 SQLModel 和 Pydantic 的力量，我们能够创建一个强大而灵活的系统，能够处理各种用户输入并生成准确的 SQL 查询。

我们配置了 OpenAIAgent，以通过更新代理类来处理系统提示和用户提示中的可变上下文，从而提供上下文感知的工具使用。这使得我们的代理能够理解可用的表及其模式，从而实现更准确和高效的工具使用。虽然我们已经取得了显著进展，但仍有很多内容需要探索和实现。

为了进一步增强我们的聊天机器人，我们引入了 TaskAgent 类，它具有类似 Tool 类的功能。TaskAgent 允许我们为每个代理定义名称、描述和输入模型，自动化初始化过程。

最后，我们通过为查询数据、添加支出、增加收入定义子代理来测试我们的路由和子代理。我们展示了代理如何处理税率并自动计算净额或毛额，展示了我们子代理的推理能力。

## 下一步

在本系列的下一部分，我们将重点通过添加对更多工具的支持，并可能将Claude测试作为新的默认语言模型，来增强我们代理的能力。我们还将探索将我们的应用程序与流行的通讯平台（如 WhatsApp）集成，使其更加易于访问和用户友好。

随着我们不断完善和扩展应用程序，可能性是无穷无尽的。通过利用大语言模型的力量并创建直观的基于聊天的界面，我们可以彻底改变小型企业管理数据和简化操作的方式。敬请期待本系列的下一部分！

# 源代码

此外，所有涉及的项目源代码都可以在 GitHub 上找到。您可以通过 [https://github.com/elokus/ArticleDemo2](https://github.com/elokus/ArticleDemo2) 访问它。
