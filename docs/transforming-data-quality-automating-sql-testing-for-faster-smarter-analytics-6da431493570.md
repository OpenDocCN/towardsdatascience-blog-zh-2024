# 数据质量转型：自动化 SQL 测试以实现更快速、更智能的分析

> 原文：[https://towardsdatascience.com/transforming-data-quality-automating-sql-testing-for-faster-smarter-analytics-6da431493570?source=collection_archive---------0-----------------------#2024-10-26](https://towardsdatascience.com/transforming-data-quality-automating-sql-testing-for-faster-smarter-analytics-6da431493570?source=collection_archive---------0-----------------------#2024-10-26)

## 如何测试 SQL 和结果数据集的质量，以回答业务问题并增加客户信任

[](https://medium.com/@hello.akashm?source=post_page---byline--6da431493570--------------------------------)[![Akash Mukherjee](../Images/b083a1dd6d07935792eae58edd563ebf.png)](https://medium.com/@hello.akashm?source=post_page---byline--6da431493570--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6da431493570--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6da431493570--------------------------------) [Akash Mukherjee](https://medium.com/@hello.akashm?source=post_page---byline--6da431493570--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6da431493570--------------------------------) ·阅读时长 11 分钟·2024年10月26日

--

![](../Images/4436d79ec654db529fca075e12919af5.png)

图片由[Caspar Camille Rubin](https://unsplash.com/@casparrubin?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来自[Unsplash](https://unsplash.com/photos/macbook-pro-with-images-of-computer-language-codes-fPkvU7RDmCo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在软件开发中，有许多自动化测试工具和框架可以依赖。但是对于分析团队来说，手动测试和数据质量保证（QA）仍然是常态。许多时候，是客户或业务团队首先发现数据质量或完整性的问题，而不是分析团队。

这就是自动化能够产生巨大影响的地方。通过建立一个自动化系统，使用脚本在大规模上运行数据质量测试，你可以保持工作高效而不牺牲数据的准确性或完整性。

当然，当业务问题模糊或开放性时，情况会变得更加复杂。在这种情况下，规则基础逻辑与大语言模型（LLM）的结合可以提供极大帮助——让你生成场景并运行自动化检查。在本教程中，我们将展示如何构建一个自动化测试系统，用来评估和评分数据和 SQL 查询的质量，即使业务问题是以简单的英文表达的。

# 在我们开始之前，你需要具备的知识

要跟随本教程，请确保你具备以下内容：

+   对数据库和 SQL 有扎实的理解

+   有使用Python进行API调用和数据处理的经验

+   访问GPT-4 API令牌

+   用于测试的业务问题数据集

# 设计系统架构

为了构建一个自动化QA系统来评估SQL查询，架构必须集成基于规则的逻辑、LLM验证和自动评分。这个设置非常适合处理那些开放式的业务问题，帮助您将测试从手动过程扩展到自动化。

关键组件包括：

+   **查询引擎**：接收并执行SQL查询的地方。

+   **评估模块**：结合静态规则与基于LLM的验证结果。

+   **评分系统**：根据不同用户角色（如数据科学家、商业领袖和最终用户）对结果进行评分。

该架构包括一个反馈回路，记录问题类型——例如缺失数据、错误粒度或性能缓慢等。这些信息将存储在一个集中式数据库中，以便您可以随时间持续优化系统。我们将使用Python进行脚本编写，使用SQL跟踪后端问题，使用OpenAI的LLM解读自然语言输入。通过定期安排这些测试的运行，您将保持一致的数据质量和可扩展性，同时微调查询性能，以与商业目标对齐。

下图展示了数据如何流经系统——从SQL摄取到自动化测试、评分和问题跟踪——以便在大规模下保持高数据质量。

最终，这个系统不仅仅是发现错误——它推动持续改进，并使您的技术执行与业务目标保持一致。

![](../Images/64922a96612de5060deab240c1c68a64.png)

作者提供的图像：用来说明技术架构的图示

# 教程

## 第1步：准备测试问题与答案数据集

要开始，收集内部团队或客户经常向分析团队提出的真实业务问题。许多问题可能是临时的数据请求，因此通过准备多种问题，您可以确保测试具有相关性。以下是一些例子，帮助您入手：

+   **问题 #1**：“我们的Pro Plan用户中有多少是从试用版转化的？”

+   **问题 #2**：“2024年6月我们吸引了多少新用户？”

+   **问题 #3**：“目前哪些产品正在流行？”

+   **问题 #4**：“我们最畅销的产品目前的销售量是多少？”

## 第2步：构建您的评估与评分标准

**2a：定义您的评分员**

为了进行彻底的测试，从不同角度设置评分员，以确保覆盖所有方面：

+   **最终用户**：关注可用性和清晰度。结果是否易于解读？是否直接解决了原始的商业问题？

+   **数据科学家**：评估技术准确性和完整性。所有必要的数据集是否已包含？分析是否详细且可重复？

+   **商业领袖**：关注与战略目标的对齐。输出是否支持与业务目标一致的决策？

**2b：定义评分标准**

每个评分员应根据特定因素来评估查询：

+   **准确性**：查询是否提供正确的答案？是否有数据点缺失或误解？

+   **相关性**：输出是否包含所有必要的数据，并排除了无关信息？

+   **逻辑**：查询是否结构良好？连接、过滤和聚合是否正确应用？

+   **效率**：查询是否在没有额外复杂性或延迟的情况下进行了性能优化？

**2c：跟踪和记录问题类型**

为了涵盖所有方面，记录查询执行过程中常见的问题非常重要。这样可以更容易地标记并运行自动化评估。

+   **错误粒度**：数据返回的细节级别不正确。

+   **过多列**：结果中包含不必要的字段，造成杂乱。

+   **缺失数据**：输出中缺少关键数据。

+   **错误值**：计算或数值错误。

+   **性能问题**：查询运行效率低，执行时间过长。

```py
import openai
import json

# Set your OpenAI API key here
openai.api_key = 'your-openai-api-key'
def evaluate_sql_query(question, query, results):
    # Define the prompt with placeholders for question, query, and results
    prompt = f"""
    As an external observer, evaluate the SQL query and results against the client's question. Provide an assessment from three perspectives:
    1\. End User
    2\. Data Scientist
    3\. Business Leader

    For each role, provide:
    1\. **Overall Score** (0-10)
    2\. **Criteria Scores** (0-10):
       - Accuracy: How well does it meet the question?
       - Relevance: Is all needed data included, and is irrelevant data excluded?
       - Logic: Does the query make sense?
       - Efficiency: Is it concise and free of unnecessary complexity?
    3\. **Issue Tags** (2D array: ['tag', 'details']):
       - Examples: Wrong Granularity, Excessive Columns, Missing Data, Incorrect Values, Wrong Filters, Performance Issues.
    4\. **Other Observations** (2D array: ['tag', 'details'])

    Client Question:
    {question}

    SQL Query:
    {query}

    SQL Results:
    {results}

    Respond ONLY in this format:
    ```json

    {{

    "endUser": {{"overallScore": "", "criteriaScores": {{"accuracy": "", "relevance": "", "logic": "", "efficiency": ""}}, "issueTags": [], "otherObservations": []}},

    "dataScientist": {{"overallScore": "", "criteriaScores": {{"accuracy": "", "relevance": "", "logic": "", "efficiency": ""}}, "issueTags": [], "otherObservations": []}},

    "businessLeader": {{"overallScore": "", "criteriaScores": {{"accuracy": "", "relevance": "", "logic": "", "efficiency": ""}}, "issueTags": [], "otherObservations": []}}

    }}

    ```py
    """
    # Call the OpenAI API with the prompt
    response = openai.Completion.create(
        engine="gpt-4",  # or whichever model you're using
        prompt=prompt,
        max_tokens=500,  # Adjust token size based on expected response length
        temperature=0  # Set temperature to 0 for more deterministic results
    )
    # Parse and return the result
    return json.loads(response['choices'][0]['text'])
# Example usage
question = "How many Pro Plan users converted from trial?"
query = "SELECT COUNT(*) FROM users WHERE plan = 'Pro' AND status = 'Converted' AND source = 'Trial';"
results = "250"
evaluation = evaluate_sql_query(question, query, results)
print(json.dumps(evaluation, indent=4))
```

## 第 3 步：自动化测试

**3a：遍历问题**

一旦收集了业务问题，设置一个循环，将每个问题、相关的 SQL 查询以及结果传递到评估函数中。这让你能够自动化整个评估过程，确保每个查询都得到一致的评分。

**3b：安排定期运行**

通过定期调度脚本运行来自动化测试过程——理想情况下，在每次数据刷新或查询更新后运行。这保持测试与数据同步，能够在问题出现时及时捕捉。

**3c：在数据库中记录得分、标签和观察结果**

对于每次测试运行，在结构化数据库中记录所有得分、问题标签和观察结果。使用 Python 脚本将相关数据填充到表格（例如，**issue_catalog**）中。这为你提供了评估历史记录，以便跟踪趋势、找出常见问题，并优化未来的测试。

## 第 4 步：报告测试结果

**4a：按得分进行透视和分组**

利用 SQL 查询或 BI 工具创建透视表，根据总体得分和具体标准（如准确性、相关性、逻辑性和效率）对结果进行分组。这有助于你发现性能趋势，找出哪些方面需要更多关注。

为了计算每个查询在所有评分员中的总体得分，可以使用加权公式：

总得分 = w1​×准确性 + w2​×相关性 + w3​×逻辑性 + w4​×效率

其中 w1​、w2​、w3​、w4​ 是分配给每个评分标准的权重。这些权重的总和应为 1，以便进行归一化。

例如，你可以根据数据科学家的优先级赋予**准确性**更高的权重，而根据业务领导的优先级赋予**相关性**更高的权重。

**4b: 突出显示主要问题**

确定最频繁和最关键的问题——比如**缺失数据**、**错误的粒度**或**性能低效**。提供一份详细报告，列出这些问题发生的频率以及哪些类型的查询最受影响。

专注于如果不加以解决可能会导致更大错误的模式。例如，突出显示可能因为数据质量问题而扭曲决策或减缓业务流程的情况。

优先处理需要立即采取行动的问题，例如那些影响查询性能或关键数据集准确性的问题，并列出明确的后续步骤以解决它们。

![](../Images/b2484b2780ed989ca27648b77d518b00.png)

作者提供的图像：使用示例测试数据创建的图表

**4c: 分析评分者的方差**

仔细查看不同评分者（终端用户、数据科学家、业务领导）评分之间的差异。较大的差异可能揭示出技术执行和业务目标之间的潜在错位。

例如，如果某个查询在技术准确性上得分较高，但在与业务问题的相关性上得分较低，这表明在将数据洞察转化为可操作结果时存在差距。类似地，如果终端用户认为结果难以理解，而数据科学家认为它们在技术上是正确的，这可能指向沟通或呈现方面的问题。

通过追踪这些差异，你可以更好地将分析过程与技术精度和商业价值对齐，从而保持所有利益相关者的满意。

为了量化这种差异，你可以计算评分者评分的方差。首先，定义各个评分为：

+   **S-EndUser​:** 终端用户的总体评分。

+   **S-DataScientist​:** 数据科学家的总体评分。

+   **S-BusinessLeader**​：来自业务领导的总体评分。

三个评分者的平均分 **μ** 可以按如下公式计算：

μ = (S-EndUser​ + S-DataScientist​ + S-BusinessLeader​​) / 3

接下来，计算方差 **σ²**，这是每个评分者的评分与平均评分之间的平方差的平均值。方差的公式是：

σ**²** = (S-EndUser − μ)**²** + (S-DataScientist − μ)**² + (S-BusinessLeader − μ)**² / 3

通过计算这种方差，你可以客观地衡量评分者的评分差异。

大的差异表明一个或多个评分者对查询质量或相关性的看法不同，这可能意味着技术输出与业务需求之间需要更好的对齐。

## 步骤 5：创建反馈循环

**5a: 确定关键问题**

在整个测试过程中，你很可能会发现一些问题反复出现。它们可能包括**缺失的数据**、**错误的值**、**错误的粒度**或**性能低效**等问题。重要的是不仅要记录这些问题，还要对它们进行分类和优先级排序。

例如，如果缺少关键数据，应该立即解决，而性能优化可以视为长期的优化任务。通过专注于最有影响力和重复出现的问题，你将能够有效改善数据质量并更好地解决根本原因。

**5b：优化你的 SQL 查询**

现在，你已经识别出重复出现的问题，是时候更新 SQL 查询来解决它们了。这涉及到优化查询逻辑，以实现准确的连接、筛选和聚合。例如：

+   如果遇到**错误的粒度**，调整查询，以适当的级别聚合数据。

+   对于**缺失的数据**，确保所有相关的表正确连接。

+   如果出现**性能问题**，简化查询、添加索引或使用更高效的 SQL 函数。

目标是在此过程中，将你记录的反馈转化为 SQL 代码的实际改进，使你未来的查询更加精准、相关且高效。

**5c：重新测试以验证结果**

一旦你的查询得到优化，重新运行测试以验证改进效果。自动化这个步骤确保你更新后的查询始终与新的数据或业务问题进行评估。再次运行测试可以确认你的修改已解决问题并提高了整体数据质量。它还可以帮助确认你的 SQL 查询完全符合业务需求，从而能够更快速和准确地获得洞察。如果出现新的问题，只需将其反馈到循环中，持续改进。

**自动化反馈循环的示例代码**

为了自动化这个反馈循环，这里有一个 Python 脚本，可以处理多个测试用例（包括业务问题、SQL 查询和结果），通过 OpenAI 的 API 评估这些用例，并将结果存储到数据库中：

```py
for question, query, results in test_cases:
    # Call the OpenAI API to evaluate the SQL query and results
    response = openai.Completion.create(
        engine="text-davinci-003",  # Replace with GPT-4 or relevant engine
        prompt=prompt.format(question=question, query=query, results=results),
        max_tokens=1000
    )

    # Process and store the response
    process_response(response)

def store_results_in_db(test_run_id, question, role, scores, issue_tags, observations):
    # SQL insert query to store evaluation results in the issue catalog
    insert_query = """
    INSERT INTO issue_catalog 
    (test_run_id, question, role, overall_score, accuracy_score, relevance_score, logic_score, efficiency_score, issue_tags, other_observations)
    VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s);
    """
    db_cursor.execute(insert_query, (
        test_run_id, question, role, scores['overall'], scores['accuracy'], scores['relevance'], 
        scores['logic'], scores['efficiency'], json.dumps(issue_tags), json.dumps(observations)
    ))
    db_conn.commit()
```

**设置问题目录表**

*issue_catalog* 表作为存储详细测试结果的主要库，帮助你清晰地跟踪查询性能并标记长期存在的问题。通过使用 JSONB 格式存储问题标签和观察结果，你可以获得更大的灵活性，允许你记录复杂信息，而无需频繁更新数据库架构。以下是设置的 SQL 代码：

```py
CREATE TABLE issue_catalog (
    id SERIAL PRIMARY KEY,
    test_run_id INT NOT NULL,
    question TEXT NOT NULL,
    role TEXT NOT NULL,  -- e.g., endUser, dataScientist, businessLeader
    overall_score INT NOT NULL,
    accuracy_score INT NOT NULL,
    relevance_score INT NOT NULL,
    logic_score INT NOT NULL,
    efficiency_score INT NOT NULL,
    issue_tags JSONB,  -- Storing issue tags as JSON for flexibility
    other_observations JSONB,  -- Storing other observations as JSON
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**这个反馈循环的目的**

1.  **持续改进**：通过跟踪问题，随着时间的推移，你将能够细化 SQL 查询，并稳步提高其质量。每次测试运行都会提供可操作的洞察，通过针对最常见的问题，你的系统会随着每次运行变得更高效和更具弹性。

1.  **数据质量保证**：定期对更新的SQL查询进行测试，帮助你验证它们是否正确处理新数据和测试用例。这个持续的过程展示了你的调整是否真正提高了数据质量，并保持与业务需求的一致性，从而降低未来问题的风险。

1.  **与业务需求的一致性**：根据提出问题的人进行排序——无论是终端用户、数据科学家还是业务领导者——都能让你专注于对技术准确性和业务相关性都有重要意义的改进。随着时间的推移，这将建立一个技术努力直接支持有意义的业务洞察的系统。

1.  **可扩展的测试和优化**：这种方法在增加更多测试用例时能够平滑扩展。随着问题目录的扩展，模式逐渐显现，使得微调影响广泛业务问题的查询变得更加容易。随着每次迭代，你的测试框架变得更强大，推动数据质量在大规模上的持续改善。

# 总结

自动化SQL测试是分析团队的游戏规则改变者，它帮助他们早期发现数据问题并精确地解决它们。通过建立一个结合规则逻辑和大语言模型（LLM）的结构化反馈循环，你可以扩展测试，处理即使是最复杂的业务问题。

这种方法不仅提高了数据的准确性，而且使你的洞察与业务目标保持一致。分析的未来依赖于自动化与洞察之间的平衡——你准备好迈出这一步吗？
