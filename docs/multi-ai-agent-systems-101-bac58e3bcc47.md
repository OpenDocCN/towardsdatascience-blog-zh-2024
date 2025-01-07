# 多智能体系统101

> 原文：[https://towardsdatascience.com/multi-ai-agent-systems-101-bac58e3bcc47?source=collection_archive---------0-----------------------#2024-06-16](https://towardsdatascience.com/multi-ai-agent-systems-101-bac58e3bcc47?source=collection_archive---------0-----------------------#2024-06-16)

## 使用CrewAI自动化数据源管理中的常规任务

[](https://miptgirl.medium.com/?source=post_page---byline--bac58e3bcc47--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--bac58e3bcc47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bac58e3bcc47--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bac58e3bcc47--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--bac58e3bcc47--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bac58e3bcc47--------------------------------) ·阅读时间：26分钟·2024年6月16日

--

![](../Images/a5a6e119bd80a1c3c4af2c7c64b02bb6.png)

图片来自 DALL-E 3

最初，当ChatGPT刚刚出现时，我们使用简单的提示来获得我们问题的答案。随后，我们遇到了幻觉问题，开始使用RAG（检索增强生成）来为LLM提供更多上下文。之后，我们开始尝试AI代理，其中LLM作为推理引擎，决定接下来做什么、使用什么工具以及何时返回最终答案。

下一步的进化是创建这样的代理团队，使其能够相互协作。这种方法是合乎逻辑的，因为它反映了人类的互动方式。我们在团队中工作，每个成员都有一个特定的角色：

+   产品经理提出下一个要处理的项目。

+   设计师创造其外观和感觉。

+   软件工程师开发解决方案。

+   分析师检查数据，以确保其按预期执行，并找出改进产品以满足客户需求的方法。

类似地，我们可以创建一个AI代理团队，每个代理专注于一个领域。它们可以协作并共同达成最终结论。正如专业化提升现实生活中的表现一样，它也能提高AI代理的表现。

这种方法的另一个优势是更高的灵活性。每个代理可以使用自己独立的提示、工具集甚至是LLM。例如，我们可以为系统的不同部分使用不同的模型。你可以为需要更多推理的代理使用GPT-4，而为只做简单提取的代理使用GPT-3.5。我们甚至可以为小型特定任务对模型进行微调，并将其用于我们的代理团队中。

这种方法的潜在缺点是时间和成本。多个智能体之间的交互和知识共享需要更多的 LLM 调用，并消耗额外的令牌。这可能导致更长的等待时间和增加的费用。

今天有几个框架可以用于多智能体系统。

下面是一些最流行的框架：

+   [AutoGen](https://github.com/microsoft/autogen?ref=blog.langchain.dev)：由微软开发，AutoGen 使用对话式的方法，是最早的多智能体系统框架之一。

+   [LangGraph](https://github.com/langchain-ai/langgraph)：虽然 LangGraph 并不是严格意义上的多智能体框架，但它允许使用图结构定义演员之间的复杂交互。因此，它也可以被改编为创建多智能体系统。

+   [CrewAI](https://github.com/joaomdmoura/crewAI?ref=blog.langchain.dev)：CrewAI 作为一个高级框架，便于创建由角色扮演智能体组成的“团队”，这些智能体能够以多种方式协作。

我决定从 CrewAI 开始尝试多智能体框架，因为它非常流行且易于使用。因此，它看起来是一个很好的入门选择。

在本文中，我将向你展示如何使用 CrewAI。作为分析师，我们是负责记录各种数据源并回答相关问题的领域专家。我们将探讨如何使用多智能体框架来自动化这些任务。

# 设置环境

让我们从设置环境开始。首先，我们需要安装 CrewAI 主包以及一个扩展来与工具一起使用。

```py
pip install crewai
pip install 'crewai[tools]'
```

CrewAI 是为了主要与 OpenAI API 配合使用而开发的，但我也想尝试在本地模型上使用它。根据 [ChatBot Arena 排行榜](https://chat.lmsys.org/?leaderboard=)，你可以在笔记本电脑上运行的最佳模型是 Llama 3（8b 参数）。这将是我们使用案例中最可行的选择。

我们可以通过 Ollama 访问 Llama 模型。安装过程非常简单。你需要从 [官方网站](https://ollama.com/download) 下载 Ollama，然后按照安装过程进行操作。就是这样。

现在，你可以通过运行以下命令在 CLI 中测试模型。

```py
ollama run llama3
```

例如，你可以提问类似这样的问题。

![](../Images/b47f272e89a01f843e909b4d4e5cf665.png)

让我们创建一个自定义的 Ollama 模型，以便在 CrewAI 中稍后使用。

我们将从 ModelFile 开始（[文档](https://github.com/ollama/ollama/blob/main/docs/modelfile.md)）。我只指定了基础模型（`llama3`）、温度和停止序列。不过，你可以添加更多的功能。例如，你可以使用 `SYSTEM` 关键字来确定系统消息。

```py
FROM llama3

# set parameters
PARAMETER temperature 0.5
PARAMETER stop Result
```

我已经将它保存在 `Llama3ModelFile` 文件中。

让我们创建一个 bash 脚本来加载 Ollama 的基础模型，并创建我们在 ModelFile 中定义的自定义模型。

```py
#!/bin/zsh

# define variables
model_name="llama3"
custom_model_name="crewai-llama3"

# load the base model
ollama pull $model_name

# create the model file
ollama create $custom_model_name -f ./Llama3ModelFile
```

让我们执行这个文件。

```py
chmod +x ./llama3_setup.sh
./llama3_setup.sh
```

> 您可以在GitHub上找到这两个文件：[Llama3ModelFile](https://github.com/miptgirl/miptgirl_medium/blob/main/crewai_answering_questions/Llama3ModelFile) 和 [llama3_setup.sh](https://github.com/miptgirl/miptgirl_medium/blob/main/crewai_answering_questions/llama3_setup.sh)

我们需要初始化以下环境变量以使用本地的Llama模型与CrewAI。

```py
os.environ["OPENAI_API_BASE"]='http://localhost:11434/v1'

os.environ["OPENAI_MODEL_NAME"]='crewai-llama3' 
# custom_model_name from the bash script

os.environ["OPENAI_API_KEY"] = "NA"
```

我们已经完成了设置，并准备继续我们的旅程。

# 用例：与文档一起工作

作为分析师，我们经常扮演数据和一些与数据相关工具的专家角色。在我之前的团队中，我们曾经有一个拥有近1K参与者的频道，我们在那里回答了很多关于我们的数据和我们用作存储的ClickHouse数据库的问题。我们花了相当多的时间来管理这个频道。看看这些任务是否可以通过LLM自动化执行将是很有趣的。

对于这个示例，我将使用ClickHouse数据库。如果您感兴趣，您可以在[我的先前文章](https://clickhouse.com/)中了解更多关于ClickHouse以及如何在本地设置它的信息。但是，我们不会使用任何特定于ClickHouse的功能，因此请随意使用您了解的数据库。

我创建了一个非常简单的数据模型来使用。在我们的DWH（数据仓库）中只有两个表：`ecommerce_db.users` 和 `ecommerce_db.sessions`。正如你可能猜到的那样，第一个表包含有关我们服务的用户的信息。

![](../Images/3de9376c5434676a728a3ee7f0d87037.png)

`ecommerce_db.sessions`表存储有关用户会话的信息。

![](../Images/6ac727891735b98720fced70326530c0.png)

关于数据源管理，分析师通常处理编写和更新文档以及回答有关这些数据的问题等任务。因此，我们将使用LLM为数据库中的表编写文档，并教它回答有关数据或ClickHouse的问题。

但在继续实施之前，让我们更多地了解CrewAI框架及其核心概念。

# CrewAI基本概念

多代理框架的基石是[**代理**](https://docs.crewai.com/core-concepts/Agents/)概念。在CrewAI中，代理由角色扮演驱动。角色扮演是一种策略，当你要求一个代理扮演一个角色并像顶尖后端工程师或乐于助人的客服代理一样行事时。因此，在创建CrewAI代理时，您需要指定每个代理的角色、目标和背景故事，以便LLM了解足够的信息来扮演这个角色。

代理的能力在没有[**工具**](https://docs.crewai.com/core-concepts/Tools/)（代理可以执行并获得结果的功能）的情况下是有限的。使用CrewAI，您可以使用预定义工具之一（例如，搜索互联网、解析网站或在文档上执行RAG），自己创建自定义工具或使用LangChain工具。因此，创建强大的代理非常容易。

让我们从智能体转到它们正在做的工作。智能体正在处理[**任务**](https://docs.crewai.com/core-concepts/Tasks/#task-attributes)（具体的任务）。对于每个任务，我们需要定义一个描述、期望的输出（完成的定义）、可用工具的集合和分配的智能体。我非常喜欢这些框架遵循管理最佳实践，例如对任务的清晰完成定义。

下一个问题是如何定义任务的执行顺序：首先做哪个任务，哪些任务可以并行执行，等等。CrewAI 实现了[**流程**](https://docs.crewai.com/core-concepts/Processes/)来协调任务。它提供了几个选项：

+   **顺序性** — 当任务一个接一个地被调用时，这是一种最直接的方式。

+   **层级化** — 当有一个经理（指定为LLM模型）创建并委派任务给智能体时。

此外，CrewAI 正在处理一个共识过程。在这样的过程中，智能体将能够通过民主的方式协作做出决策。

还有其他一些手段，你可以用来调整任务执行的过程：

+   你可以将任务标记为“异步”，然后它们将并行执行，这样你将能更快得到答案。

+   你可以在任务上使用“人工输入”标志，然后智能体将在最终确定该任务输出之前请求人类批准。这可以让你在过程中加入监督。

我们已经定义了所有的基本构件，并可以讨论CrewAI的圣杯——[**小组**](https://docs.crewai.com/core-concepts/Crews/#crew-attributes)概念。小组代表智能体团队和他们将要处理的任务集合。协作的方式（我们之前讨论的流程）也可以在小组级别进行定义。

此外，我们可以为一个小组设置[**记忆**](https://docs.crewai.com/core-concepts/Memory/)。记忆对于智能体之间的高效协作至关重要。CrewAI 支持三种记忆级别：

+   短期记忆存储与当前执行相关的信息。它帮助智能体在当前任务中协作。

+   长期记忆是存储在本地数据库中的关于先前执行的数据。这种类型的记忆使得智能体能够从早期的迭代中学习并随时间改进。

+   实体记忆捕捉并结构化关于实体的信息（如角色、城市等）。

目前，你只能为小组开启所有类型的记忆，而无法进行进一步的自定义。不过，这在Llama模型上不起作用。

我们已经学到了足够多的CrewAI框架内容，现在是时候将这些知识付诸实践了。

# 用例：编写文档

让我们从一个简单的任务开始：为我们的DWH编写文档。正如我们之前讨论过的，DWH中有两个表，我希望使用LLM创建它们的详细描述。

## 第一种方法

一开始，我们需要考虑团队结构。这就像一个典型的管理任务。你会为这样的工作雇佣谁？

我将把这个任务分为两部分：从数据库中检索数据和编写文档。因此，我们需要一位数据库专家和一位技术写作者。数据库专家需要访问数据库，而写作者则不需要任何特殊工具。

![](../Images/38ecf57b23d0f2c2cc2dd8f8031f6b1b.png)

现在，我们有了一个高层次的计划。让我们创建这些代理。

对于每个代理，我已经指定了角色、目标和背景故事。我尽力为代理提供了所有需要的上下文。

```py
database_specialist_agent = Agent(
  role = "Database specialist",
  goal = "Provide data to answer business questions using SQL",
  backstory = '''You are an expert in SQL, so you can help the team 
  to gather needed data to power their decisions. 
  You are very accurate and take into account all the nuances in data.''',
  allow_delegation = False,
  verbose = True
)

tech_writer_agent = Agent(
  role = "Technical writer",
  goal = '''Write engaging and factually accurate technical documentation 
    for data sources or tools''',
  backstory = ''' 
  You are an expert in both technology and communications, so you can easily explain even sophisticated concepts.
  You base your work on the factual information provided by your colleagues.
  Your texts are concise and can be easily understood by a wide audience. 
  You use professional but rather an informal style in your communication.
  ''',
  allow_delegation = False,
  verbose = True
)
```

我们将使用一个简单的顺序过程，因此代理之间无需相互委派任务。这就是为什么我指定了`allow_delegation = False`。

下一步是为代理设置任务。但在进行这些任务之前，我们需要创建一个自定义工具来连接数据库。

首先，我编写了一个函数，用于通过HTTP API执行ClickHouse查询。

```py
CH_HOST = 'http://localhost:8123' # default address 

def get_clickhouse_data(query, host = CH_HOST, connection_timeout = 1500):
  r = requests.post(host, params = {'query': query}, 
    timeout = connection_timeout)
  if r.status_code == 200:
      return r.text
  else: 
      return 'Database returned the following error:\n' + r.text 
```

在使用LLM代理时，确保工具具有容错性非常重要。例如，如果数据库返回错误（`status_code != 200`），我的代码不会抛出异常。相反，它会将错误描述返回给LLM，以便LLM尝试解决问题。

要创建CrewAI自定义工具，我们需要从`crewai_tools.BaseTool`派生类，实施`_run`方法，然后创建该类的实例。

```py
from crewai_tools import BaseTool

class DatabaseQuery(BaseTool):
  name: str = "Database Query"
  description: str = "Returns the result of SQL query execution"

  def _run(self, sql_query: str) -> str:
      # Implementation goes here
      return get_clickhouse_data(sql_query)

database_query_tool = DatabaseQuery()
```

现在，我们可以为代理设置任务。同样，向LLM提供清晰的指示和所有上下文是至关重要的。

```py
table_description_task = Task(
  description = '''Provide the comprehensive overview for the data 
  in table {table}, so that it's easy to understand the structure 
  of the data. This task is crucial to put together the documentation 
  for our database''',
  expected_output = '''The comprehensive overview of {table} in the md format. 
  Include 2 sections: columns (list of columns with their types) 
  and examples (the first 30 rows from table).''',
  tools = [database_query_tool],
  agent = database_specialist_agent
)

table_documentation_task = Task(
  description = '''Using provided information about the table, 
  put together the detailed documentation for this table so that 
  people can use it in practice''',
  expected_output = '''Well-written detailed documentation describing 
  the data scheme for the table {table} in markdown format, 
  that gives the table overview in 1-2 sentences then then 
  describes each columm. Structure the columns description 
  as a markdown table with column name, type and description.''',
  tools = [],
  output_file="table_documentation.md",
  agent = tech_writer_agent
)
```

你可能已经注意到，我在任务描述中使用了`{table}`占位符。我们将在执行团队任务时，将`table`作为输入变量，这个值将被插入到所有占位符中。

此外，我已经为表格文档任务指定了输出文件，以便将最终结果保存在本地。

我们拥有所需的一切。现在，是时候创建一个团队并执行流程，指定我们感兴趣的表格了。让我们试试“users”表。

```py
crew = Crew(
  agents = [database_specialist_agent, tech_writer_agent],
  tasks = [table_description_task,  table_documentation_task],
  verbose = 2
)

result = crew.kickoff({'table': 'ecommerce_db.users'})
```

这是一个令人兴奋的时刻，我真的很期待看到结果。如果执行过程需要一些时间，请不要担心。代理会多次调用LLM，因此花费几分钟是完全正常的。我在我的笔记本上花了2.5分钟。

我们要求LLM以Markdown格式返回文档。我们可以使用以下代码在Jupyter Notebook中查看格式化后的结果。

```py
from IPython.display import Markdown
Markdown(result)
```

第一眼看起来不错。我们得到了一个有效的Markdown文件，描述了“users”表。

![](../Images/e98ec48f91dc2754b39ac5b0c59424e2.png)

等等，这不对。让我们看看表格中有哪些数据。

![](../Images/3de9376c5434676a728a3ee7f0d87037.png)

文档中列出的列与我们数据库中的完全不同。这是LLM幻觉的一个例子。

我们已将`verbose = 2`设置为从CrewAI获取详细的日志。让我们浏览执行日志，以找出问题的根本原因。

首先，数据库专家由于引用符号的问题，无法查询数据库。

![](../Images/966fcf095524bc7809853c882d83ab57.png)

专家未能解决这个问题。最终，这个链条已被CrewAI终止，输出为：`Agent stopped due to iteration limit or time limit`。

这意味着技术写作者没有收到任何关于数据的事实性信息。然而，代理继续执行并产生了完全虚假的结果。最终我们得到了错误的文档。

## 修复问题

尽管我们的第一次迭代没有成功，但我们学到了很多。我们至少有两个可以改进的方面：

+   我们的数据库工具对模型来说太复杂，代理难以使用它。我们可以通过去掉查询开始和结束的引号来使工具更具容错性。这个解决方案并不理想，因为有效的SQL可能以引号结尾，但我们还是试试看。

+   我们的技术写作者没有基于数据库专家的输入来生成输出。我们需要调整提示，强调只提供事实性信息的重要性。

那么，让我们尝试修复这些问题。首先，我们将修复工具——我们可以利用`strip`来去除引号。

```py
CH_HOST = 'http://localhost:8123' # default address 

def get_clickhouse_data(query, host = CH_HOST, connection_timeout = 1500):
  r = requests.post(host, params = {'query': query.strip('"').strip("'")}, 
    timeout = connection_timeout)
  if r.status_code == 200:
    return r.text
  else: 
    return 'Database returned the following error:\n' + r.text
```

接下来，到了更新提示的时候。我已经在代理和任务定义中加入了强调坚持事实重要性的语句。

```py
 tech_writer_agent = Agent(
  role = "Technical writer",
  goal = '''Write engaging and factually accurate technical documentation 
  for data sources or tools''',
  backstory = ''' 
  You are an expert in both technology and communications, so you 
  can easily explain even sophisticated concepts.
  Your texts are concise and can be easily understood by wide audience. 
  You use professional but rather informal style in your communication.
  You base your work on the factual information provided by your colleagues. 
  You stick to the facts in the documentation and use ONLY 
  information provided by the colleagues not adding anything.''',
  allow_delegation = False,
  verbose = True
)

table_documentation_task = Task(
  description = '''Using provided information about the table, 
  put together the detailed documentation for this table so that 
  people can use it in practice''',
  expected_output = '''Well-written detailed documentation describing 
  the data scheme for the table {table} in markdown format, 
  that gives the table overview in 1-2 sentences then then 
  describes each columm. Structure the columns description 
  as a markdown table with column name, type and description.
  The documentation is based ONLY on the information provided 
  by the database specialist without any additions.''',
  tools = [],
  output_file = "table_documentation.md",
  agent = tech_writer_agent
)
```

让我们再次执行我们的团队任务，看看结果。

![](../Images/f79b2d4b9cfb0046ab1a6f48b6a4acb7.png)

我们取得了一些更好的结果。我们的数据库专家能够执行查询并查看数据，这是我们的一项重大胜利。此外，我们可以在结果表中看到所有相关的字段，尽管还有许多其他字段。所以，它仍然不是完全正确的。

我再次查看了CrewAI执行日志，试图弄清楚问题出在哪里。问题出在获取列列表时。没有按数据库进行过滤，因此它返回了一些与结果无关的列。

```py
SELECT column_name 
FROM information_schema.columns 
WHERE table_name = 'users'
```

此外，在查看了多次尝试后，我注意到数据库专家偶尔执行`select * from <table>`查询。它可能在生产环境中引发一些问题，因为它可能会生成大量数据并将其发送到LLM。

## 更专业的工具

我们可以为我们的代理提供更多专业的工具来改进我们的解决方案。目前，代理有一个可以执行任何SQL查询的工具，这个工具灵活且强大，但容易出错。我们可以创建更专注的工具，例如获取表结构和从表中提取前N行数据。希望这样可以减少错误的发生。

```py
class TableStructure(BaseTool):
  name: str = "Table structure"
  description: str = "Returns the list of columns and their types"

  def _run(self, table: str) -> str:
    table = table.strip('"').strip("'")
    return get_clickhouse_data(
      'describe {table} format TabSeparatedWithNames'\
        .format(table = table)
    )

class TableExamples(BaseTool):
  name: str = "Table examples"
  description: str = "Returns the first N rows from the table"

  def _run(self, table: str, n: int = 30) -> str:
    table = table.strip('"').strip("'")
    return get_clickhouse_data(
      'select * from {table} limit {n} format TabSeparatedWithNames'
        \.format(table = table, n = n)
    )

table_structure_tool = TableStructure()
table_examples_tool = TableExamples()
```

现在，我们需要在任务中指定这些工具并重新运行我们的脚本。在第一次尝试后，我从技术写作者那里得到了以下输出。

```py
Task output: This final answer provides a detailed and factual description 
of the ecommerce_db.users table structure, including column names, types, 
and descriptions. The documentation adheres to the provided information 
from the database specialist without any additions or modifications.
```

更加专注的工具帮助数据库专家检索了正确的表信息。然而，尽管写作者拥有所有必要的信息，我们仍未得到预期的结果。

如我们所知，LLM是概率性的，所以我又尝试了一次。太好了，这次结果非常好。

![](../Images/536e784233791190a9045928c534d537.png)

它还不完美，因为仍然包括一些无关的评论，并且缺少表格的整体描述。然而，提供更多专业工具显然是值得的。这也有助于防止在代理尝试加载表格中的所有数据时出现问题。

## 质量保证专家

我们已经取得了相当不错的结果，但让我们看看是否能进一步改善它们。多代理设置中的常见做法是质量保证，它在最终确定结果之前添加了最后的审核阶段。

![](../Images/b452393cdc89394850940fa99040c1ef.png)

让我们创建一个新的代理——质量保证专家，负责审核工作。

```py
qa_specialist_agent = Agent(
  role = "Quality Assurance specialist",
  goal = """Ensure the highest quality of the documentation we provide 
  (that it's correct and easy to understand)""",
  backstory = '''
  You work as a Quality Assurance specialist, checking the work 
  from the technical writer and ensuring that it's inline 
  with our highest standards.
  You need to check that the technical writer provides the full complete 
  answers and make no assumptions. 
  Also, you need to make sure that the documentation addresses 
  all the questions and is easy to understand.
  ''',
  allow_delegation = False,
  verbose = True
)
```

现在，到了描述审核任务的时候了。我使用了`context`参数，指定此任务需要`table_description_task`和`table_documentation_task`的输出。

```py
qa_review_task = Task(
  description = '''
  Review the draft documentation provided by the technical writer.
  Ensure that the documentation fully answers all the questions: 
  the purpose of the table and its structure in the form of table. 
  Make sure that the documentation is consistent with the information 
  provided by the database specialist. 
  Double check that there are no irrelevant comments in the final version 
  of documentation.
  ''',
  expected_output = '''
  The final version of the documentation in markdown format 
  that can be published. 
  The documentation should fully address all the questions, be consistent 
  and follow our professional but informal tone of voice.
  ''',
  tools = [],
  context = [table_description_task, table_documentation_task],
  output_file="checked_table_documentation.md",
  agent = qa_specialist_agent
)
```

让我们更新我们的团队并运行它。

```py
full_crew = Crew(
  agents=[database_specialist_agent, tech_writer_agent, qa_specialist_agent],
  tasks=[table_description_task,  table_documentation_task, qa_review_task],
  verbose = 2,
  memory = False # don't work with Llama
)

full_result = full_crew.kickoff({'table': 'ecommerce_db.users'})
```

由于加入了QA阶段，我们现在有了更结构化和详细的文档。

![](../Images/207c43201fa01049208b438839e28ebb.png)

## 委派

随着质量保证（QA）专家的加入，测试委派机制会变得非常有趣。QA专家代理可能会有一些问题或请求，可以将其委派给其他代理。

我尝试使用Llama 3进行委派，但结果不太理想。Llama 3在正确调用同事工具时遇到了困难，无法指定正确的同事姓名。

我们使用本地模型在任何笔记本电脑上都能取得相当不错的结果，但现在是时候换个思路，使用更强大的模型——GPT-4o了。

为了实现这一点，我们只需更新以下环境变量。

```py
os.environ["OPENAI_MODEL_NAME"] = 'gpt-4o'  
os.environ["OPENAI_API_KEY"] = config['OPENAI_API_KEY'] # your OpenAI key
```

为了开启委派功能，我们应该为QA专家代理指定`allow_delegation = True`。

此外，我们还可以使用OpenAI模型的便捷记忆功能，如上所述。记忆功能将允许我们的代理在执行过程中互相分享知识，并利用长期记忆从以前的执行中获取信息。

```py
full_crew = Crew(
  agents = [database_specialist_agent, tech_writer_agent, qa_specialist_agent],
  tasks = [table_description_task,  table_documentation_task, qa_review_task],
  verbose = 2,
  memory = True 
)

full_result = full_crew.kickoff({'table': 'ecommerce_db.users'})
```

让我们看看CrewAI的执行日志，了解委派是如何工作的。这里是QA专家的所有日志。我们可以看到它联系了数据库专家以核实信息。

```py
Thought: I need to review the provided draft documentation to ensure it 
fully answers all the questions and is complete, correct, and easy 
to understand. Specifically, I need to ensure that it explains the purpose 
of the table, its structure, and includes relevant examples without 
any irrelevant comments.

First, I will compare the two drafts to identify any discrepancies and 
missing information. Then, I will verify the accuracy and completeness of 
the information provided.

**Comparison:**
1\. **Introduction:**
   - First draft: Includes an introduction and examples.
   - Second draft: Adds a more detailed introduction about the purpose and importance of the table.

2\. **Columns:**
   - Both drafts have the same column structure and descriptions.

3\. **Additional Information:**
   - Both drafts include similar additional information, but the second draft is more concise.

4\. **Examples:**
   - The first draft includes examples, which are missing in the second draft.

5\. **Final Checks:**
   - Ensure there are no irrelevant comments.
   - Verify consistency with information provided by the database specialist.

Action: Ask question to co-worker
Action Input: {
"question": "Can you confirm the accuracy of the provided column descriptions 
  and examples for the `ecommerce_db.users` table?", 
"context": "We are finalizing the documentation for the `ecommerce_db.users`
 table. The table includes columns `user_id`, `country`, `is_active`, and 
  `age`. Here are the examples provided:

  | user_id | country        | is_active | age |
  |---------|----------------|-----------|-----|
  | 1000001 | United Kingdom | 0         | 70  |
  | 1000002 | France         | 1         | 87  |
  | 1000003 | France         | 1         | 88  |
  | 1000004 | Germany        | 1         | 25  |
  | 1000005 | Germany        | 1         | 48  |
  | 1000006 | France         | 1         | 78  |
  | 1000007 | Netherlands    | 1         | 65  |
  | 1000008 | Germany        | 1         | 31  |
  | 1000009 | Germany        | 1         | 66  |
  | 1000010 | Germany        | 1         | 73  |", 
"coworker": "database specialist"}

<-- omitted logs from the database specialist -->

Based on the provided context and examples for the `ecommerce_db.users` table, 
the column descriptions and examples appear accurate. Here are the details:
- `user_id`: This column contains a unique identifier for each user. 
  The provided examples (e.g., 1000001, 1000002) align with this description 
  as they are unique numeric identifiers.

- `country`: This column specifies the country of the user. The provided 
  examples (e.g., United Kingdom, France, Germany, Netherlands) are 
  all valid country names.

- `is_active`: This column indicates whether the user is active (1) or not (0). The provided examples (e.g., 0, 1) correctly represent this binary status.

- `age`: This column shows the age of the user. The provided examples (e.g., 70, 
  87, 88, 25, 48, 78, 65, 31, 66, 73) are all valid ages.

Therefore, the column descriptions and examples provided for the 
 `ecommerce_db.users` table are accurate and meet the expected criteria.

Thought: Based on the observation from the database specialist, 
the column descriptions and examples provided for the `ecommerce_db.users` 
table are accurate. Now, I will consolidate the best elements from both drafts,
 ensuring the documentation is complete, clear, and free of irrelevant comments.

Action: Finalize the documentation in markdown format, incorporating 
the detailed introduction, examples, and ensuring the tone is professional 
but informal.
```

当我第一次尝试委派时，没有启用记忆功能，这导致了不正确的结果。数据专家和技术写作者最初返回了正确的信息。然而，当QA专家返回后续问题时，他们开始出现幻觉。因此，看起来当启用记忆功能时，委派效果会更好。

这是GPT-4o的最终输出。现在结果看起来非常不错。我们完全可以使用LLM来自动化文档编写。

![](../Images/ccca4f521728af6e15a2d73bc450db2a.png)

所以，第一个任务已经解决！

我使用相同的脚本为`ecommerce_db.sessions`表生成文档。这对我们的下一个任务很有用。所以，让我们不要浪费时间，继续前进。

# 用例：回答问题

我们的下一个任务是根据文档回答问题，因为这对许多数据分析师（以及其他专家）来说是常见的。

我们将从简单开始，只创建两个代理人：

+   文档支持专家将根据文档回答问题，

+   支持QA代理人将在与客户分享答案之前对其进行审查。

![](../Images/211478ae8b1f633d5693c883fea42a40.png)

我们需要为文档专家提供一些工具，使他们能够查看存储在目录中的所有文件并阅读这些文件。这很简单，因为CrewAI已经实现了这样的工具。

```py
from crewai_tools import DirectoryReadTool, FileReadTool

documentation_directory_tool = DirectoryReadTool(
    directory = '~/crewai_project/ecommerce_documentation')

base_file_read_tool = FileReadTool()
```

然而，由于Llama 3在调用工具时仍然在处理引号的问题，我不得不在`FileReaderTool`之上创建一个自定义工具来解决这个问题。

```py
from crewai_tools import BaseTool

class FileReadToolUPD(BaseTool):
    name: str = "Read a file's content"
    description: str = "A tool that can be used to read a file's content."

    def _run(self, file_path: str) -> str:
        # Implementation goes here
        return base_file_read_tool._run(file_path = file_path.strip('"').strip("'"))

file_read_tool = FileReadToolUPD()
```

接下来，我们需要像之前一样创建代理人、任务和团队。

```py
data_support_agent = Agent(
  role = "Senior Data Support Agent",
  goal = "Be the most helpful support for you colleagues",
  backstory = '''You work as a support for data-related questions 
  in the company. 
  Even though you're a big expert in our data warehouse, you double check 
  all the facts in documentation. 
  Our documentation is absolutely up-to-date, so you can fully rely on it 
  when answering questions (you don't need to check the actual data 
  in database).
  Your work is very important for the team success. However, remember 
  that examples of table rows don't show all the possible values. 
  You need to ensure that you provide the best possible support: answering 
  all the questions, making no assumptions and sharing only the factual data.
  Be creative try your best to solve the customer problem. 
  ''',
  allow_delegation = False,
  verbose = True
)

qa_support_agent = Agent(
  role = "Support Quality Assurance Agent",
  goal = """Ensure the highest quality of the answers we provide 
  to the customers""",
  backstory = '''You work as a Quality Assurance specialist, checking the work 
  from support agents and ensuring that it's inline with our highest standards.
  You need to check that the agent provides the full complete answers 
  and make no assumptions. 
  Also, you need to make sure that the documentation addresses all 
  the questions and is easy to understand.
  ''',
  allow_delegation = False,
  verbose = True
)

draft_data_answer = Task(
  description = '''Very important customer {customer} reached out to you 
  with the following question:
  ```

{问题}

```py

  Your task is to provide the best answer to all the points in the question 
  using all available information and not making any assumprions. 
  If you don't have enough information to answer the question, just say 
  that you don't know.''',
  expected_output = '''The detailed informative answer to the customer's 
  question that addresses all the point mentioned. 
  Make sure that answer is complete and stict to facts 
  (without any additional information not based on the factual data)''',
  tools = [documentation_directory_tool, file_read_tool], 
  agent = data_support_agent
)

answer_review = Task(
  description = '''
  Review the draft answer provided by the support agent.
  Ensure that the it fully answers all the questions mentioned 
  in the initial inquiry. 
  Make sure that the answer is consistent and doesn't include any assumptions.
  ''',
  expected_output = '''
  The final version of the answer in markdown format that can be shared 
  with the customer. 
  The answer should fully address all the questions, be consistent 
  and follow our professional but informal tone of voice. 
  We are very chill and friendly company, so don't forget to include 
  all the polite phrases.
  ''',
  tools = [], 
  agent = qa_support_agent
)

qna_crew = Crew(
  agents = [data_support_agent, qa_support_agent],
  tasks = [draft_data_answer,  answer_review],
  verbose = 2,
  memory = False # don't work with Llama
)
```

让我们看看它在实践中是如何运作的。

```py
result = qna_crew.kickoff(
  {'customer': "Max", 
   'question': """Hey team, I hope you're doing well. I need to find 
    the numbers before our CEO presentation tomorrow, so I will really 
    appreciate your help.
    I need to calculate the number of sessions from our Windows users in 2023\. I've tried to find the table with such data in our data warehouse, but wasn't able to. 
    Do you have any ideas whether we store the needed data somewhere, 
    so that I can query it? """
  }
)
```

我们得到了一份有礼貌、实用和有帮助的答复。这真的很棒。

```py
**Hello Max,**

Thank you for reaching out with your question! I'm happy to help you 
find the number of sessions from Windows users in 2023\. 
After reviewing our documentation, I found that we do store data 
related to sessions and users in our ecommerce database, specifically in 
the `ecommerce_db.sessions` table.

To answer your question, I can provide you with a step-by-step guide 
on how to query this table using SQL. First, you can use the `session_id` 
column along with the `os` column filtering for "Windows" and 
the `action_date` column filtering for dates in 2023\. 
Then, you can group the results by `os` using the `GROUP BY` clause 
to count the number of sessions that meet these conditions.

Here's a sample SQL query that should give you the desired output:

```sql

SELECT COUNT(*)

FROM ecommerce_db.sessions

WHERE os = 'Windows'

AND action_date BETWEEN '2023-01-01' AND '2023-12-31'

GROUP BY os;

```py

This query will return the total number of sessions from Windows 
users in 2023\. I hope this helps! If you have any further questions or 
need more assistance, please don't hesitate to ask.
```

让我们稍微复杂化任务。假设我们不仅可以得到关于我们的数据的问题，还可以得到关于我们的工具（ClickHouse）的问题。因此，我们的团队中将有另一个代理人——ClickHouse专家。为了给我们的CH代理人一些知识，我将与它分享一个文档网站。

```py
from crewai_tools import ScrapeWebsiteTool, WebsiteSearchTool
ch_documenation_tool = ScrapeWebsiteTool(
  'https://clickhouse.com/docs/en/guides/creating-tables')
```

如果您需要处理长篇文档，可以尝试使用RAG（检索增强生成）—[WebsiteSearchTool](https://docs.crewai.com/tools/WebsiteSearchTool/)。它将计算嵌入并将其存储在ChromaDB中。在我们的情况下，我们将坚持使用简单的网站爬虫工具。

现在我们有了两位主题专家，我们需要决定谁将负责回答问题。因此，现在是使用分层过程并添加经理来协调所有任务的时候了。

![](../Images/1e8a9048cb1cf80662b17df62655fd37.png)

CrewAI提供了经理实现，因此我们只需要指定LLM模型。我选择了GPT-4o。

```py
from langchain_openai import ChatOpenAI
from crewai import Process

complext_qna_crew = Crew(
  agents = [ch_support_agent, data_support_agent, qa_support_agent],
  tasks = [draft_ch_answer, draft_data_answer, answer_review],
  verbose = 2,
  manager_llm = ChatOpenAI(model='gpt-4o', temperature=0),  
  process = Process.hierarchical,  
  memory = False 
)
```

> 在这一点上，我不得不再次从Llama 3切换到OpenAI模型以运行分层过程，因为使用Llama对我来说没有效果（类似于[此问题](https://github.com/joaomdmoura/crewAI/issues/657)）。

现在，我们可以尝试使用不同类型的问题来测试我们的新团队（无论是与我们的数据还是ClickHouse数据库相关的问题）。

```py
ch_result = complext_qna_crew.kickoff(
  {'customer': "Maria", 
   'question': """Good morning, team. I'm using ClickHouse to calculate 
   the number of customers. 
   Could you please remind whether there's an option to add totals 
   in ClickHouse?"""
  }
)

doc_result = complext_qna_crew.kickoff(
  {'customer': "Max", 
   'question': """Hey team, I hope you're doing well. I need to find 
    the numbers before our CEO presentation tomorrow, so I will really 
    appreciate your help.
    I need to calculate the number of sessions from our Windows users 
    in 2023\. I've tried to find the table with such data 
    in our data warehouse, but wasn't able to. 
    Do you have any ideas whether we store the needed data somewhere, 
    so that I can query it. """
  }
)
```

如果我们查看最终的答案和日志（*我在这里省略了它们，因为它们相当冗长，但* *你可以在* [*GitHub*](https://github.com/miptgirl/miptgirl_medium/blob/main/crewai_answering_questions/rag_documentation_poc_openai.ipynb) *找到它们和完整日志*），我们会看到管理器能够正确地协调并将任务委派给拥有相关知识的同事，以解答客户的问题。对于第一个（与ClickHouse相关）问题，我们得到了一个详细的答案，包含了示例以及使用`WITH TOTALS`功能的潜在影响。对于数据相关的问题，模型返回的信息大致与我们上面看到的相同。

因此，我们构建了一个可以根据文档回答各种问题的团队，无论是来自本地文件还是网站。我认为这是一个非常棒的结果。

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/tree/main/crewai_answering_questions)上找到所有的代码。

# 总结

在本文中，我们探讨了如何使用CrewAI多智能体框架创建一个基于表格撰写文档并回答相关问题的解决方案。

鉴于我们所使用的广泛功能，现在是总结这个框架的优缺点的时候了。

总体来说，我认为[CrewAI](https://www.crewai.com/)是一个非常有用的多智能体系统框架：

+   它简单明了，你可以快速构建你的第一个原型。

+   它的灵活性使得可以解决相当复杂的商业问题。

+   它鼓励像角色扮演这样的良好实践。

+   它提供了许多开箱即用的实用工具，如RAG和网站解析器。

+   支持不同类型的记忆增强了智能体之间的协作。

+   内置的防护措施有助于防止智能体陷入重复的循环中。

然而，仍有一些可以改进的地方：

+   尽管该框架简单且易于使用，但它的可定制性不强。例如，目前你无法创建自己的LLM管理器来协调这些过程。

+   有时，从文档中获取完整详细的信息是相当具有挑战性的。例如，显然CrewAI实现了一些防护措施，以防止重复的函数调用，但文档并没有完全解释它是如何工作的。

+   另一个改进的方向是透明度。我喜欢了解框架是如何在背后工作的。例如，在Langchain中，你可以使用`langchain.debug = True`来查看所有LLM调用。然而，我还没有弄清楚如何在CrewAI中获得相同级别的详细信息。

+   对本地模型的全面支持将是一个很大的补充，因为当前的实现要么缺少某些功能，要么难以正确运行。

LLM领域和工具正在迅速发展，因此我希望在不久的将来会看到许多进展。

> 非常感谢你阅读这篇文章。希望这篇文章对你有所启发。如果你有任何后续问题或评论，请在评论区留下。

# 参考资料

本文灵感来源于DeepLearning.AI的[“带CrewAI的多AI智能体系统”](https://www.deeplearning.ai/short-courses/multi-ai-agent-systems-with-crewai/)短期课程。
