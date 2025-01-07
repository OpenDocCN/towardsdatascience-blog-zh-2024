# 使用生成式AI在Amazon Bedrock上分类Jira票务

> 原文：[https://towardsdatascience.com/classify-jira-tickets-with-genai-on-amazon-bedrock-69450d4d8b21?source=collection_archive---------4-----------------------#2024-11-04](https://towardsdatascience.com/classify-jira-tickets-with-genai-on-amazon-bedrock-69450d4d8b21?source=collection_archive---------4-----------------------#2024-11-04)

## 使用提示工程和大语言模型（LLM）替代传统的NLP方法来进行Jira票务文本分类。这是一个代码示例的详细讲解。

[](https://medium.com/@tannermcrae?source=post_page---byline--69450d4d8b21--------------------------------)[![Tanner McRae](../Images/bb80770681d29438860fe83aba8a22fb.png)](https://medium.com/@tannermcrae?source=post_page---byline--69450d4d8b21--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--69450d4d8b21--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--69450d4d8b21--------------------------------) [Tanner McRae](https://medium.com/@tannermcrae?source=post_page---byline--69450d4d8b21--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--69450d4d8b21--------------------------------) ·阅读时长8分钟·2024年11月4日

--

![](../Images/e829c8a66e2c4fbdce6ee9ed37ea3f46.png)

图片由[Annie Spratt](https://unsplash.com/@anniespratt?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

还记得以前将文本分类意味着开始一段机器学习之旅吗？如果你在机器学习领域待得够久，你可能已经亲眼见证过至少一个团队陷入了构建“完美”文本分类系统的无底洞。这个故事通常是这样的：

+   **第1个月：“我们就快速训练一个NLP模型！”**

+   **第2个月：“我们需要更多的训练数据……”**

+   **第3个月：“**这个已经足够好了”

多年来，文本分类一直是经典机器学习领域的一个部分。早在我的职业生涯初期，我就记得为电子邮件分类训练支持向量机（SVM）。那时需要大量的预处理、迭代、数据收集和标注。

但这里有个转折：现在是2024年，生成式AI模型已经能够**“通常”**开箱即用进行文本分类！你可以构建一个强大的票务分类系统，无需收集成千上万的标注训练数据，也不需要管理机器学习训练管道，或者维护定制的模型。

在这篇文章中，我们将讨论如何使用Amazon Bedrock上的大语言模型和其他AWS服务，搭建一个Jira票务分类系统。

**免责声明**：我在AWS担任生成式AI架构师，以下观点仅代表我个人意见。

## 为什么要分类Jira票务？

企业常见的需求之一是了解团队如何分配时间。Jira 有标签功能，但有时由于人为错误或缺乏粒度，可能会有所不足。通过进行这项工作，组织可以更好地洞察团队活动，从而做出基于数据的资源分配、项目投资和淘汰决策。

## 为什么不使用其他 NLP 方法？

传统的 ML 模型和像 BERT 这样的较小的变换器需要数百（或数千）个标注示例，而 LLM 可以开箱即用地进行文本分类。在我们的 Jira 票据分类测试中，提示工程方法的效果与传统的 ML 模型相当，甚至超越了它们，使用 Claude Haiku 处理 10k+ 年度票据，费用约为每年 ~$10（不包括其他 AWS 服务费用）。此外，提示比重新训练模型更容易更新。

# 代码示例

这个 [github 仓库](https://github.com/aws-samples/jira-ticket-classification)包含了一个示例应用程序，它连接到 Jira Cloud，分类票据，并以你最喜欢的仪表盘工具（如 Tableu、Quicksight 或任何支持 CSV 的工具）可以使用的格式输出它们。

> 重要通知：该项目使用 Terraform 在你的 AWS 环境中部署资源。你将为使用的 AWS 资源产生费用。请留意你所在 AWS 区域内 Lambda、Bedrock、Glue 和 S3 等服务的定价。

**前提条件**

你需要在你希望部署此代码的环境中安装 terraform 和 AWS CLI

+   [使用 tfenv 安装 Terraform](https://github.com/tfutils/tfenv)

+   [安装 AWS CLI 并配置](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

# 架构

架构非常简单。你可以在下面找到详细信息。

![](../Images/7a1dc84efb3bbf496059d6045ca74266.png)

图片来源：作者

**步骤 1：** 触发一个 AWS Lambda 函数，通过定时任务获取基于时间窗口的 Jira 票据。然后，这些票据会被格式化并推送到 S3 存储桶中的 **/unprocessed** 前缀。

**步骤 2：** 触发一个 Glue 任务，基于 **/unprocessed** 对象上传来运行 PySpark 去重任务，确保没有重复票据进入仪表盘。去重后的票据会被推送到 **/staged** 前缀。这对于手动上传票据以及依赖自动获取的情况很有用。**如果你可以确保没有重复，你可以移除此步骤。**

**步骤 3：** 通过调用 Amazon Bedrock 来启动新票据的分类任务，根据大语言模型（LLM）的提示对票据进行分类。分类后，完成的结果会推送到 **/processed** 前缀。从这里，你可以使用任何支持 CSV 的仪表盘工具提取处理过的 CSV 文件。

# 入门

要开始，请克隆上述 GitHub 仓库并转到 /terraform 目录

```py
$ git clone https://github.com/aws-samples/jira-ticket-classification.git

$ cd jira-ticket-classification/terraform
```

运行`terraform init`、`plan`和`apply`。确保你的计算机上已安装Terraform并配置了AWS CLI。

```py
$ terraform init

$ terraform plan

$ terraform apply
```

一旦基础设施部署到你的账户中，你可以导航到AWS Secrets Manager并更新密钥，使用你的Jira Cloud凭证。你需要一个API密钥、基本网址和电子邮件来启用自动拉取功能。

![](../Images/74f14dbab21c582550d50baf14a9b905.png)

图片由作者提供

就是这样！

你可以选择（1）等待Cron触发自动拉取，（2）将票据导出为CSV并上传到`/unprocessed` S3前缀，或者（3）通过测试手动触发Lambda函数。

# 它是如何工作的？

## Jira拉取：

Jira拉取使用Lambda函数和CloudWatch cron事件来触发。Lambda函数获取AWS Secrets，并使用while循环中的get请求，直到JQL查询完成，来检索分页结果：

```py
def fetch_jira_issues(base_url, project_id, email, api_key):
    url = f"{base_url}/rest/api/3/search"

    # Calculate the date 8 days ago
    eight_days_ago = (datetime.now() - timedelta(days=8)).strftime("%Y-%m-%d")

    # Create JQL
    jql = f"project = {project_id} AND created >= '{eight_days_ago}' ORDER BY created DESC"

    # Pass into params of request.
    params = {
        "jql": jql,
        "startAt": 0
    }
    all_issues = []

    auth = HTTPBasicAuth(email, api_key)
    headers = {"Accept": "application/json"}

    while True:
        response = requests.get(url, headers=headers, params=params, auth=auth)
        if response.status_code != 200:
            raise Exception(f"Failed to fetch issues for project {project_id}: {response.text}")

        data = json.loads(response.text)
        issues = data['issues']
        all_issues.extend(issues)

        if len(all_issues) >= data['total']:
            break

        params['startAt'] = len(all_issues)

    return all_issues
```

它会创建CSV的字符串表示，并将其上传到S3：

```py
def upload_to_s3(csv_string, bucket, key):
    try:
        s3_client.put_object(
            Bucket=bucket,
            Key=key,
            Body=csv_string,
            ContentType='text/csv'
        )
    except Exception as e:
        raise Exception(f"Failed to upload CSV to S3: {str(e)}")
```

## Glue作业

`/unprocessed`前缀上的S3事件触发了第二个Lambda函数，启动了AWS Glue作业。当Jira票据有多个入口点进入系统时，这非常有用。例如，如果你想进行回填操作。

```py
import boto3 

# Initialize Boto3 Glue client
glue_client = boto3.client('glue')

def handler(event, context):
    # Print event for debugging
    print(f"Received event: {json.dumps(event)}")

    # Get bucket name and object key (file name) from the S3 event
    try:
        s3_event = event['Records'][0]['s3']
        s3_bucket = s3_event['bucket']['name']
        s3_key = s3_event['object']['key']
    except KeyError as e:
        print(f"Error parsing S3 event: {str(e)}")
        raise

    response = glue_client.start_job_run(
        JobName=glue_job_name,
        Arguments={
            '--S3_BUCKET': s3_bucket,
            '--NEW_CSV_FILE': s3_key
        }
    )
```

Glue作业本身是用PySpark编写的，可以在代码仓库[这里](https://github.com/aws-samples/jira-ticket-classification/blob/main/src/glue/etl_script.py)找到。重要的要点是，它使用[左反连接](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-join.html#anti-join)将新CSV中的项与`/staged` CSV中的所有ID进行连接。

结果随后被推送到**/staged**前缀。

## 分类Jira票据：

这就是有趣的地方。事实证明，使用提示工程可以与文本分类模型的表现相媲美，甚至在某些技术上表现更好。

+   你可以在提示中定义分类及其描述，

+   让模型按步骤思考[(链式思维)](https://www.promptingguide.ai/techniques/cot)。

+   然后输出分类结果，而无需训练单个模型。请参见以下提示：

> **注意：** 使用人工策划的分类/标签票据子集来验证你的提示非常重要。你应该将此提示通过验证数据集运行，确保它与预期的票据分类一致。

```py
SYSTEM_PROMPT = '''
You are a support ticket assistant. You are given fields of a Jira ticket and your task is to classify the ticket based on those fields

Below is the list of potential classifications along with descriptions of those classifications.
<classifications>
ACCESS_PERMISSIONS_REQUEST: Used when someone doesn't have the write permissions or can't log in to something or they can't get the correct IAM credentials to make a service work.
BUG_FIXING: Used when something is failing or a bug is found. Often times the descriptions include logs or technical information.
CREATING_UPDATING_OR_DEPRECATING_DOCUMENTATION: Used when documentation is out of date. Usually references documentation in the text.
MINOR_REQUEST: This is rarely used. Usually a bug fix but it's very minor. If it seems even remotely complicated use BUG_FIXING.
SUPPORT_TROUBLESHOOTING: Used when asking for support for some engineering event. Can also look like an automated ticket.
NEW_FEATURE_WORK: Usually describes a new feature ask or something that isn't operational.
</classifications>

The fields available and their descriptions are below.
<fields>
Summmary: This is a summary or title of the ticket
Description: The description of the issue in natural language. The majority of context needed to classify the text will come from this field
</fields>

<rules>
* It is possible that some fields may be empty in which case ignore them when classifying the ticket
* Think through your reasoning before making the classification and place your thought process in <thinking></thinking> tags. This is your space to think and reason about the ticket classificaiton.
* Once you have finished thinking, classify the ticket using ONLY the classifications listed above and place it in <answer></answer> tags.
</rules>'''

USER_PROMPT = '''
Using only the ticket fields below:

<summary_field>
{summary}
</summary_field>

<description_field>
{description}
</description_field>

Classify the ticket using ONLY 1 of the classifications listed in the system prompt. Remember to think step-by-step before classifying the ticket and place your thoughts in <thinking></thinking> tags.
When you are finished thinking, classify the ticket and place your answer in <answer></answer> tags. ONLY place the classifaction in the answer tags. Nothing else.
'''
```

我们添加了一个帮助类，将调用Bedrock的操作串联起来，以加快速度：

```py
import boto3
from concurrent.futures import ThreadPoolExecutor, as_completed
import re
from typing import List, Dict
from prompts import USER_PROMPT, SYSTEM_PROMPT

class TicketClassifier:
    SONNET_ID = "anthropic.claude-3-sonnet-20240229-v1:0"
    HAIKU_ID = "anthropic.claude-3-haiku-20240307-v1:0"
    HYPER_PARAMS = {"temperature": 0.35, "topP": .3}
    REASONING_PATTERN = r'<thinking>(.*?)</thinking>'
    CORRECTNESS_PATTERN = r'<answer>(.*?)</answer>'

    def __init__(self):
        self.bedrock = boto3.client('bedrock-runtime')

    def classify_tickets(self, tickets: List[Dict[str, str]]) -> List[Dict[str, str]]:
        prompts = [self._create_chat_payload(t) for t in tickets]
        responses = self._call_threaded(prompts, self._call_bedrock)
        formatted_responses = [self._format_results(r) for r in responses]
        return [{**d1, **d2} for d1, d2 in zip(tickets, formatted_responses)]

    def _call_bedrock(self, message_list: list[dict]) -> str:
        response = self.bedrock.converse(
            modelId=self.HAIKU_ID,
            messages=message_list,
            inferenceConfig=self.HYPER_PARAMS,
            system=[{"text": SYSTEM_PROMPT}]
        )
        return response['output']['message']['content'][0]['text']

    def _call_threaded(self, requests, function):
        future_to_position = {}
        with ThreadPoolExecutor(max_workers=5) as executor:
            for i, request in enumerate(requests):
                future = executor.submit(function, request)
                future_to_position[future] = i
            responses = [None] * len(requests)
            for future in as_completed(future_to_position):
                position = future_to_position[future]
                try:
                    response = future.result()
                    responses[position] = response
                except Exception as exc:
                    print(f"Request at position {position} generated an exception: {exc}")
                    responses[position] = None
        return responses

    def _create_chat_payload(self, ticket: dict) -> dict:
        user_prompt = USER_PROMPT.format(summary=ticket['Summary'], description=ticket['Description'])
        user_msg = {"role": "user", "content": [{"text": user_prompt}]}
        return [user_msg]

    def _format_results(self, model_response: str) -> dict:
        reasoning = self._extract_with_regex(model_response, self.REASONING_PATTERN)
        correctness = self._extract_with_regex(model_response, self.CORRECTNESS_PATTERN)
        return {'Model Answer': correctness, 'Reasoning': reasoning}

    @staticmethod
    def _extract_with_regex(response, regex):
        matches = re.search(regex, response, re.DOTALL)
        return matches.group(1).strip() if matches else None
```

最后，分类的票据被转换为CSV并上传到S3。

```py
import boto3
import io
import csv

s3 = boto3.client('s3')

def upload_csv(data: List[Dict[str, str]]) -> None:
      csv_buffer = io.StringIO()
      writer = csv.DictWriter(csv_buffer, fieldnames=data[0].keys())
      writer.writeheader()
      writer.writerows(data)

      current_time = datetime.now().strftime("%Y%m%d_%H%M%S")
      filename = f"processed/processed_{current_time}.csv"

      s3.put_object(
          Bucket=self.bucket_name,
          Key=filename,
          Body=csv_buffer.getvalue()
      )
```

# 仪表板

该项目对仪表板工具没有依赖。任何流行的工具/服务都可以，只要它能够处理CSV。Amazon Quicksight、Tableau或任何介于两者之间的工具都可以使用。

# 结论

在这篇博客中，我们讨论了使用 Bedrock 来自动分类 Jira 工单。这些增强后的工单可以用来通过各种 AWS 服务或第三方工具创建仪表盘。要点是，自从采用了大型语言模型（LLM）后，文本分类变得更加简单，以前需要几周的工作，现在可以在几天内完成。

*如果你喜欢这篇文章，欢迎通过* [*LinkedIn*](https://www.linkedin.com/in/tanner-mcrae-aa728358/) *与我联系*
