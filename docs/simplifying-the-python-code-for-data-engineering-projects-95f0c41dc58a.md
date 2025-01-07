# 简化Python代码以应对数据工程项目

> 原文：[https://towardsdatascience.com/simplifying-the-python-code-for-data-engineering-projects-95f0c41dc58a?source=collection_archive---------1-----------------------#2024-06-12](https://towardsdatascience.com/simplifying-the-python-code-for-data-engineering-projects-95f0c41dc58a?source=collection_archive---------1-----------------------#2024-06-12)

## 数据摄取、验证、处理和测试的Python技巧与技术：实用操作指南

[](https://medium.com/@johnleungTJ?source=post_page---byline--95f0c41dc58a--------------------------------)[![John Leung](../Images/ef45063e759e3450fa7f3c32b2f292c3.png)](https://medium.com/@johnleungTJ?source=post_page---byline--95f0c41dc58a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95f0c41dc58a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95f0c41dc58a--------------------------------) [John Leung](https://medium.com/@johnleungTJ?source=post_page---byline--95f0c41dc58a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--95f0c41dc58a--------------------------------) ·阅读时间10分钟·2024年6月12日

--

原始数据来自不同的来源和格式。在数据能够用来回答关键的商业问题之前，需要大量的努力和时间来进行数据工程。虽然基础的数据基础设施可能根据数据的量、速度和分析需求而有所不同，但一些基本的代码设计技巧仍然是相关的，可以简化和优化各种任务。

本文将探讨一般数据工程项目中的不同关键部分，从数据摄取到管道测试。Python是数据工程中最广泛使用的编程语言，我们将学习如何使用Python中的内置功能和高效库来处理这些用例。

![](../Images/128d4c278ec7ce5302584afdc96ee3f1.png)

图片来源：[Katerina Pavlyuchkova](https://unsplash.com/@kat_katerina?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

想象一下你拥有一家在线零售店，销售独特的各类节庆礼品。这个在线商店非常受欢迎，每分钟每秒的交易量都非常大。你希望通过分析当前交易的购买习惯，满足更多现有客户的需求，并服务更多的新客户，这促使你开始深入数据处理，准备交易记录。

## #0 模拟数据

我们首先使用[JSON Lines](https://jsonlines.org/)（JSONL）文本格式将一些交易数据模拟到文件中，其中每一行都是一个独立的JSON对象。这种格式在数据流处理领域非常有吸引力，例如网页/应用分析和日志管理。

在我们的文件中，数据字段属于不同的数据类型。它们包括客户和产品标识符（以整数/数组格式），支付方式（以字符串格式），以及交易总金额（以浮动数字格式）。

```py
import json
import random
import numpy as np
import datetime

# Remove existing 'retail_transactions.jsonl' file, if any
! rm -f /p/a/t/h retail_transactions.jsonl

# Set the no of transactions
no_of_iteration = 500000

# Open a file in write mode
with open('retail_transactions.jsonl', 'w') as f:
  for num in range(no_of_iteration):
    if (random.randint(1, 10000) != 5000):
      # Create a valid transaction
      new_txn = {
        'orderID': num,
        'customerID': random.randint(1, 100000),
        'productID': np.random.randint(10000, size=random.randint(1, 5)).tolist(),
        'paymentMthd': random.choice(['Credit card', 'Debit card', 'Digital wallet', 'Cash on delivery', 'Cryptocurrency']),
        'totalAmt': round(random.random() * 5000, 2),
        'invoiceTime': datetime.datetime.now().isoformat()
      }
    else:
      # Create an invalid transaction
      new_txn = {
        'orderID': "",
        'customerID': "",
        'productID': "",
        'paymentMthd': "",
        'totalAmt': "",
        'invoiceTime': ""
       }

     # Write the transaciton as a JSON line to the file
     f.write(json.dumps(new_txn) + "\n")
```

你可能会发现文件中有几条单独的交易数据，其中一些数据字段为空。这模拟了缺失数据的问题，这是现实世界中常见的数据质量问题之一。

## #1 数据摄取 — Yield

读取文件中的交易记录，最简单的方法之一是将数据集遍历到一个列表中，然后将其转换为Pandas DataFrame。

这个方法对于我们演示数据集中的500,000条交易非常有效。但如果现实世界中的数据集有数百万甚至数十亿行数据呢？如果不导致内存问题，我们可能需要长时间等待整个计算完成。

有时候，我们不关心整个结果，而是希望在加载最后一条记录之前先处理初步结果。在这种情况下，我们可以使用`yield`来控制[生成器](https://wiki.python.org/moin/Generators)的流向。

> 生成器不会将整个记录存储在内存中。相反，它一次只返回一个值，并在请求下一个值之前暂停函数执行。

在用户代码和库代码交替执行的过程中，还会强制顺序执行，这意味着你不能在到达第一条记录之前访问第二条记录。你可以在[Pydata 讲座视频](https://www.youtube.com/watch?v=7lmCu8wz8ro)中了解更多关于这个概念的详细解释。

`yield`语句有不同的实际用途。例如，我们可以遍历文件中的每一行，只返回非空记录。下面展示了如何执行实时数据过滤：

```py
import json

def read_json_file(file_name):
  # Read the JSONL file
  with open(file_name) as f:
    for line in f:
      txn = json.loads(line)
      # Yield valid transactions only
      if (txn['orderID'] != ""):
        yield(txn)

txn_generator = read_json_file('retail_transactions.jsonl')
```

这些代码的输出是一个Python生成器，一种特殊类型的迭代器。你可以在循环中使用`next`函数来逐个返回后续项目。除了实时数据过滤，另一个思路是设计一个生成器函数，预处理数据并以预定义的批量大小进行生成，这可以直接解析并供机器学习模型训练使用。而且，我们还可以用它来异步处理网页请求和响应，进行网页爬取。

## #2 数据验证 — Pydantic

假设你有一个包含交易记录信息的JSON数据列表，这是数据摄取后的一个示例交易：

```py
{
 'orderID': 10000,
 'customerID': 48316,
 'productID': [5620],
 'paymentMthd': 'Cash on delivery',
 'totalAmt': 9301.2,
 'invoiceTime': '2024-06-10T23:30:29.608443',
 'price': -1
}
```

对于每一条传入的数据，我们希望确保它经过验证，否则在运行后续的数据处理函数时，我们很容易遇到各种错误。这可以通过使用`pydantic`库来实现。

> 我们首先使用[PyDantic模型](https://docs.pydantic.dev/latest/api/base_model/)定义数据字段的模式，然后使用`model_validate()`函数验证我们的JSON数据。

```py
from datetime import datetime
from pydantic import BaseModel, ValidationError

# Define the data model for a transaction record
class TxnModel(BaseModel):
  orderID: int
  customerID: int
  productID: list[int]
  paymentMthd: str
  totalAmt: float
  invoiceTime: datetime

try:
  # Validate the sample case against the schema
  TxnModel.model_validate(sample_txn)
  print("Validated successfully!")
except ValidationError as exc:
  # Print error messages for any validation error
  print("Validation Error:")
  print(exc.errors())

# Output:
# Validated successfully
```

有时，我们发现需要应用更严格的验证规则。例如，Pydantic基础模型会尝试将字符串数据强制转换为整数。为避免这种情况，可以在模型级别或字段级别设置`strict=True`。

此外，我们还可以对数据字段应用自定义验证规则。例如，我们可能希望检查支付方式值是否符合我们的预期。为了方便测试，我们手动将示例案例的支付方式设置为“Bitcoin”，这是在线商店中不存在的选项，然后使用`AfterValidator`嵌入一个函数进行进一步检查。

```py
from typing import Annotated
from pydantic.functional_validators import AfterValidator

# Customize the validation rule
def validate_payment_mthd(paymentMthd: str):
  possible_values = ['Credit card', 'Debit card', 'Digital wallet', 'Cash on delivery', 'Cryptocurrency']
  if paymentMthd not in possible_values:
    raise ValueError(f"Invalid paymentMthd, payment type must be one of {possible_values}")
  return storage

# Define the data model for a transaction record
class TxnModel(BaseModel):
  orderID: int = Field(strict=True)
  customerID: int
  productID: list[int]
  paymentMthd: Annotated[str, AfterValidator(validate_payment_mthd)]
  totalAmt: Annotated[float, Field(strict=True, gt=0)]
  invoiceTime: datetime

# Manually define a non-existent payment method
sample_txn['paymentMthd'] = 'Bitcoin'

try:
  # Validate the sample case against the schema
  TxnModel.model_validate(sample_txn)
  print("Validated successfully!")
except ValidationError as exc:
  # Print error messages for any validation error
  print("Validation Error:")
  print(exc.errors()[0]['ctx'])

# Output
# Validation Error:
# {'error': ValueError("Invalid paymentMthd, payment type must be one of ['Credit card', 'Debit card', 'Digital wallet', 'Cash on delivery', 'Cryptocurrency']")}
```

验证器成功识别到支付方式不在可能值的列表中。这是通过应用Pydantic的内部验证逻辑，并随后使用自定义验证函数完成的。代码会引发一个`ValueError`，并填充`ValidationError`。

当触发错误时，我们可以采取后续行动进行纠正。这些功能有助于消除数据错误，从而确保数据的准确性和完整性。

## #3 数据处理

(1) Python 装饰器

数据验证后，我们开始处理数据密集型函数。随着数据管道的复杂化，执行时间可能会变长。我们希望找出根本原因，并优化函数的时间性能。一种简单的方法是，在每个函数的开始和结束时收集两个时间戳，然后逐一计算时间差。

为了确保数据管道中的代码更简洁，我们可以利用[Python装饰器](https://book.pythontips.com/en/latest/decorators.html)。

> 我们首先设计一个Python装饰器来测量执行时间。之后，我们为任何需要此功能的函数添加注解。

例如，您可以测量对所有交易进行分类所需的时间。

```py
import time

# Measure the excution time of a given function
def time_decorator(func):
  def wrapper(*args, **kwargs):
    begin_time = time.time()
    output = func(*args, **kwargs)
    end_time = time.time()
    print(f"Execution time of function {func.__name__}: {round(end_time - begin_time, 2)} seconds.")
    return output
  return wrapper

# Categorize the total amount of each transaction
@time_decorator
def group_txn_price(data):
  for txn in data:
    price = txn['totalAmt']
    if 0 <= price <= 1500:
      txn['totalAmtCat'] = 'Low'
    elif 1500 < price <= 3500:
      txn['totalAmtCat'] = 'Moderate'
    elif 3500 < price:
      txn['totalAmtCat'] = 'High'
    return data

txn_list = group_txn_price(txn_list)

# Output
# Execution time of function group_txn_price: 0.26 seconds.
```

装饰器方法使得代码在不改变原始函数源代码的情况下可以复用。类似地，我们可以应用装饰器的思想，用于记录函数完成情况或在任务失败时发送邮件警报。

(2) Map、reduce、filter

这些是常用的Python数组方法，许多开发者可能都很熟悉。但我仍然认为它们值得提及，原因有几点：（1）不可变性——这些函数不会修改原始列表的值；（2）链式灵活性——可以同时应用多个函数的组合；（3）简洁可读——只需一行代码。

假设我们有一个包含两个键的JSON对象列表：支付方式和总金额。让我们探索这些函数是如何工作的。

**Map：** 对列表中的所有元素执行相同的操作（例如，为支付方式的值添加后缀）。

```py
updated_txn_list = list(map(lambda x: {
                      'paymentMthd': f"{x['paymentMthd']}_2024",
                      "totalAmt": x["totalAmt"]
                   }, txn_list))

print(updated_txn_list)

# Output
# [{'paymentMthd': 'Cryptocurrency_2024', 'totalAmt': 3339.85},
# {'paymentMthd': 'Cash on delivery_2024', 'totalAmt': 872.52},
# ...]
```

**Filter**：获取符合某个条件的元素子集（例如，仅记录支付方式为加密货币的记录）。

```py
updated_txn_list = list(map(lambda x: x, filter(lambda y: y["paymentMthd"] == "Cryptocurrency", txn_list)))

print(updated_txn_list)

# Output
# [{'paymentMthd': 'Cryptocurrency', 'totalAmt': 3339.85},
# {'paymentMthd': 'Cryptocurrency', 'totalAmt': 576.15},
# ...]
```

**Reduce**：获取单一值的结果（例如，求和或将所有元素相乘）。

```py
from functools import reduce

total_amt_crypto = reduce(lambda acc, x: acc + x["totalAmt"], updated_txn_list, 0)

print(total_amt_crypto)

# Output
# 250353984.67000002
```

我们可以在数据科学项目的转换步骤中利用这些函数。例如，使用`map()`来缩放或标准化数据，使用`filter()`来去除异常值和不相关的数据点，使用`reduce()`来生成汇总统计数据。

## #4 数据管道测试 — Pytest

数据管道通常涉及数据摄取、数据清理和提取-转换-加载（ETL）操作。潜在错误的范围可以非常广泛且容易被忽视，尤其是当模型流和结果难以被用户解读时。这导致开发团队更依赖于测试工作。

> 通常会进行单元测试，以确保机器学习系统的每个组件按预期执行。

最受欢迎的Python测试框架之一是`[Pytest](https://docs.pytest.org/en/stable/contents.html)`。假设我们希望确保转化后的数据质量，技术团队和决策者都可以信任这些数据。我们可以测试我们之前处理的关于分类交易价格的函数。为了实现这一点，我们需要准备两个Python文件：

+   **feature_engineering.py**：包含之前构建的函数的文件

```py
# Categorize the total amount of each transaction
def add_features(sample_cases):
  for txn in sample_cases:
    price = txn[‘totalAmt’]
  if 0 <= price <= 1500:
    txn[‘totalAmtCat’] = ‘Low’
  elif 1500 < price <= 3500:
    txn[‘totalAmtCat’] = ‘Moderate’
  elif 3500 < price:
    txn[‘totalAmtCat’] = ‘High’

return sample_cases
```

+   **test_feature_engineering.py**：带有“test_”前缀的文件，Pytest仅在测试过程中识别此文件。

```py
from feature_engineering import add_features

def test_add_features():
  sample_cases = [{
      'orderID': 1,
      'customerID': 36536,
      'productID': [2209, 2262, 4912, 3162, 5734],
      'paymentMthd': 'Cryptocurrency',
      'totalAmt': 576.15,
      'invoiceTime': '2024–06–10T23:53:25.329928'
    }]

  # Call the function with the sample cases
  sample_cases = add_features(sample_cases)

  # Check the assertations
  for txn in sample_cases: 
    assert 'totalAmtCat' in list(txn.keys())
    assert len(txn) == 7
    assert len(txn['totalAmtCat']) != 0
```

上面的assert语句确保新的“totalAmtCat”数据字段已添加且其值非空，同时原始数据字段不受影响。通过执行命令`Pytest`，我们可以知道测试已经通过！

![](../Images/f5d42bc912f608940f41c8519fbb3498.png)

Pytest结果 — 测试通过（图片由作者提供）

对于一个更高级的案例，假设我们有三个函数，顺序如下：`load_data`、`clean_data`和`add_features`。我们应该如何设计测试文件来逐个验证这些函数的输出？

```py
import pytest
import json
from feature_engineering import load_data, clean_data, add_features

# Set up a temporary JSONL file
@pytest.fixture
def jsonl_file(tmp_path):
  sample_cases = [{'orderID': 10000,
    'customerID': 48316,
    'productID': [5620],
    'paymentMthd': 'Cash on delivery',
    'totalAmt': 9301.2,
    'invoiceTime': '2024-06-10T23:30:29.608443',
    'price': -1
  }]

  file_path = tmp_path + "/test_transactions.jsonl"

  with open(file_path, 'w') as f:
    for txn in sample_cases:
        f.write(json.dumps(txn) + "\n")

  return file_path

# Test function to validate the `load_data` function
def test_load_data(jsonl_file):
  data = load_data(jsonl_file)
  # assert statements here

# Test function to validate the `clean_data` function
def test_clean_data(jsonl_file):
  data = load_data(jsonl_file)
  data = clean_data(data)
  # assert statements here

# Test function to validate the `add_features` function
def test_add_features(jsonl_file):
  data = load_data(jsonl_file)
  data = clean_data(data)
  data = add_features(data)
  # assert statements here
```

我们应该为初始化定义一个固定的基准，例如一个包含样本测试用例的JSON Lines文件。在这里，我们使用`@pytest.fixture`装饰器，它类似于我们在Python装饰器部分讨论的`time_decorator`。这个装饰器有助于避免反复初始化样本文件。对于剩下的代码，我们涉及几个测试函数来运行管道函数，并使用assert语句来检测逻辑错误。

## 总结一下

我们遇到了数据工程项目中的几个关键方面，并探索了如何简化和优化Python代码以提高效率和可读性：

+   数据摄取，使用`yield`处理大数据集，同时实现高效的内存使用。

+   数据验证，利用`Pydantic`根据模式和自定义值模式验证数据字段。

+   数据处理，通过应用Python装饰器和内置库来启用额外的功能，而无需重复代码。

+   通过使用`Pytest`进行管道测试，以确保工作流中各个环节的函数输出质量。

## 在你继续之前

如果你喜欢这篇文章，我邀请你关注我的[Medium页面](https://medium.com/@johnleungTJ)和[LinkedIn页面](https://www.linkedin.com/in/john-leung-639800115/)。通过这种方式，你可以随时获取有关数据科学副项目、机器学习操作（MLOps）演示以及项目管理方法的最新内容。

[](/performing-customer-analytics-with-langchain-and-llms-0af4ea38f7b5?source=post_page-----95f0c41dc58a--------------------------------) [## 使用LangChain和LLMs进行客户分析

### 探索LangChain在客户分析中的潜力与局限性，并附带实际的实现过程…

towardsdatascience.com](/performing-customer-analytics-with-langchain-and-llms-0af4ea38f7b5?source=post_page-----95f0c41dc58a--------------------------------) [](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287?source=post_page-----95f0c41dc58a--------------------------------) [## 使用PySpark在Databricks上进行时间序列特征工程

### 探索PySpark在时间序列数据中的潜力：导入、提取和可视化数据，并附带实际操作…

towardsdatascience.com](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287?source=post_page-----95f0c41dc58a--------------------------------)
