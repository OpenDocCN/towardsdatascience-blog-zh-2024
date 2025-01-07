# 亚马逊SageMaker训练作业的优先级调度器

> 原文：[https://towardsdatascience.com/a-priority-based-scheduler-for-amazon-sagemaker-training-jobs-a225327e0a94?source=collection_archive---------10-----------------------#2024-03-08](https://towardsdatascience.com/a-priority-based-scheduler-for-amazon-sagemaker-training-jobs-a225327e0a94?source=collection_archive---------10-----------------------#2024-03-08)

## 优化有限的AI训练加速器的使用——第二部分

[](https://chaimrand.medium.com/?source=post_page---byline--a225327e0a94--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--a225327e0a94--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a225327e0a94--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a225327e0a94--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--a225327e0a94--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a225327e0a94--------------------------------) ·阅读时间：12分钟·2024年3月8日

--

![](../Images/d4dd51c7492c9ad3b406d2ca1d86b429.png)

由[Adrien Aletti](https://unsplash.com/@ahda_gallery?utm_source=medium&utm_medium=referral)拍摄，图片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

本文与[Max Rabin](https://www.linkedin.com/in/maxrabin/)合作创作。

这是关于最大化稀缺 AI 资源利用的系列文章的第二部分。在[第一篇文章](/maximizing-the-utility-of-scarce-ai-resources-a-kubernetes-approach-0230ba53965b)中，我们提到，随着 AI 资源规模化能力的限制日益增加，AI 开发团队为保证 AI 计算能力，正逐渐倾向于通过构建内部 AI 服务器农场和/或在云中预留专用实例等方式来解决这一问题。AI 计算资源的稀缺性促使我们设计专门的调度解决方案，以最大程度地减少空闲时间，并优先处理关键工作负载。请参阅我们[之前的文章](/maximizing-the-utility-of-scarce-ai-resources-a-kubernetes-approach-0230ba53965b)，其中我们提出了此类解决方案的详细需求清单。我们采用的方法是利用现有的基于优先级的[调度器](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/)，该调度器与[Kubernetes](https://kubernetes.io/)一起提供，并使我们的训练开发工作流与其使用保持一致。在本文中，我们探讨了保持现有框架进行 AI 模型训练并通过自定义实现优先级调度器来增强该框架的选项。重要的是，这种类型的解决方案的需求通常不仅由 AI 资源的稀缺性驱动，还受到希望增加对训练工作负载的编排和优先级管理控制的推动，从而降低开发成本。例如，即使在容量充足的情况下，你也可能选择将训练实例的数量限制在一个固定范围内，以便控制训练开销。

本文假设我们选择的训练框架是 AWS 的 AI 模型训练托管服务[Amazon SageMaker](https://aws.amazon.com/sagemaker/)。我们将提出的解决方案将使用其他 AWS 服务，如[Amazon DynamoDB](https://aws.amazon.com/pm/dynamodb/)和[AWS Lambda](https://aws.amazon.com/pm/lambda/)。选择使用 AWS 服务来展示我们的解决方案不应被视为对其的推荐。市面上有许多基于云的服务可供选择，最适合您的解决方案将取决于您项目的具体细节。我们所描述的类似解决方案可以在其他云环境中设计，或使用其他云服务来实现。

# 启动 SageMaker 训练任务的传统方法

传统上，我们会使用[Amazon SageMaker Python SDK](https://sagemaker.readthedocs.io/en/stable/)启动 SageMaker 训练任务。在下面的代码块中，我们使用 SageMaker SDK（版本 2.208）在单个类型为[p5.48xlarge](https://aws.amazon.com/ec2/instance-types/p5/)的实例上运行 PyTorch 训练工作负载。

```py
from sagemaker.pytorch import PyTorch

# define job
estimator = PyTorch(
    role='<sagemaker role>',
    entry_point='train.py',
    instance_type='ml.p5.48xlarge',
    instance_count=1,
    framework_version='2.0.1',
    py_version='py310',
    tags=[{'Key': 'priority', 'Value': '100'}
)

# start job
estimator.fit()
```

当调用 *estimator.fit()* 函数时，SageMaker 库会将我们的代码上传到 Amazon S3，然后将请求转换为一个 boto3 SageMaker 客户端的[create_training_job](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sagemaker/client/create_training_job.html)请求（请参见[此处](https://github.com/aws/sagemaker-python-sdk/blob/v2.208.0/src/sagemaker/session.py#L976)）。

启动训练作业的这种方法依赖于所请求资源的可用性。对于我们所面临的稀缺 AI 资源场景，成功的可能性往往较低。尽管通过[为连续工作负载保留已配置的计算实例](https://medium.com/@chaimrand/retaining-amazon-sagemaker-instance-capacity-with-sagemaker-managed-warm-pools-f7cfd78fa34c)可以在一定程度上缓解这个问题，但 API *不*提供适当的工具来最大化它们的效用。假设我们希望使用恰好两台[p5.48xlarge](https://aws.amazon.com/ec2/instance-types/p5/)实例。为了简化讨论，我们假设每个训练工作负载都在单个实例上运行。通常，在 AI 模型开发周期中，会有一些时间段，训练工作负载的数量超过了两台实例的处理能力。现有的 API 会尝试启动第三台[p5.48xlarge](https://aws.amazon.com/ec2/instance-types/p5/)实例，并且很可能因为其可用性有限而失败。即使存在实例可用性，我们也可能希望将训练限制在我们指定的两台实例上，以便更好地控制训练的成本。

我们需要一个新的 API 来提交训练作业，它不会立即启动新的[p5.48xlarge](https://aws.amazon.com/ec2/instance-types/p5/)实例，而是将作业放入优先级队列中。我们还需要一个关联的作业调度器来管理这两台资源的使用，同时优先处理关键工作负载。

需要特别注意的是，截至本文撰写时，Amazon SageMaker *不*支持在[预留 Amazon EC2 实例](https://aws.amazon.com/ec2/pricing/reserved-instances/)上进行训练。尽管[Amazon SageMaker Savings Plans](https://aws.amazon.com/savingsplans/ml-pricing/)具有与实例预留相似的特性，但它*不*保证实例的容量。在[上一篇文章](https://chaimrand.medium.com/f7cfd78fa34c)中，我们讨论了这一限制，并建议使用[由 SageMaker 管理的热池](https://docs.aws.amazon.com/sagemaker/latest/dg/train-warm-pools.html)作为保留已配置实例访问权限的替代方法。在本文的其余部分，我们将假设无论是通过此方法还是其他方式，我们能够获得我们选择的两台实例。

# 基于优先级的调度对于 Amazon SageMaker

在本节中，我们将描述我们提出的解决方案的组件。我们将使用[AWS 无服务器应用程序模型 (SAM) 规范](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification.html)。更具体地说，我们将创建一个[AWS SAM 模板 YAML 文件](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)，并逐步添加我们需要的 AWS 资源。有关如何使用 AWS SAM 定义和部署无服务器解决方案的详细信息，请参见[文档](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)。

![](../Images/25932c7e09d19a30ed4bb4cffb192a95.png)

AWS 架构图（作者提供）

# 提交训练作业的私有 API

我们首先使用[Amazon API Gateway](https://aws.amazon.com/api-gateway/)来定义一个[私有 REST API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html)，用于提交训练作业请求。我们将 API 命名为*training-job-queue*。稍后，我们将添加一个名为*add-job*的 POST 方法，并修改我们的训练作业创建代码，以使用该方法，而不是使用 SageMaker 客户端的[create_training_job](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sagemaker/client/create_training_job.html) API。下面的代码块包含了在 SAM 中定义的私有[API 资源](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-api.html)。实际上，您可能希望为该 API 指定访问限制和/或授权方法。

```py
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Resources:
  InternalAPI:
    Type: AWS::Serverless::Api
      # Auth: # Add access control to API
      EndpointConfiguration:
        Type: PRIVATE
        # VPCEndpointIds: # Specify VPC Endpoint(s)
      Name: training-job-queue
      StageName: prod
```

## 定义一个用于存储训练作业请求的 AWS DynamoDB 表

我们将使用一个名为*sagemaker-queue*的[Amazon DynamoDB](https://aws.amazon.com/pm/dynamodb/)表来存储提交的训练工作负载。每个条目将包含以下字段：

1.  jobName: 存储训练作业的唯一名称。

1.  entryTime: 存储作业添加的日期和时间。

1.  jobState: 存储训练作业的当前状态。有效值为“pending”、“running”和“preempted”。

1.  priority: 存储表示作业相对优先级的整数值。

1.  jobDetails: 存储作业请求的详细信息。

我们在 SAM 模板 YAML 文件中使用[AWS::Serverless::SimpleTable](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-simpletable.html)资源定义我们的 DynamoDB 表。

```py
 DynamoSMQueue:
    Type: AWS::Serverless::SimpleTable
    Properties:
      PrimaryKey:
        Name: jobName
        Type: String
      TableName: sagemaker-queue
```

我们定义了一个函数，从给定的训练作业请求创建表项。我们假设请求包含与[create_training_job](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sagemaker/client/create_training_job.html) API的输入内容相同的JSON格式数据。我们进一步假设工作负载的*优先级*作为一个键值[标签](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_Tag.html)输入到训练作业定义中。

```py
import json, boto3, datetime

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('sagemaker-queue')

def add_job_entry(job_json):
    job_details = json.loads(job_json)

    # extract job_name
    job_name = job_details['TrainingJobName']
    print(f'add entry {job_name}')

    # get current time
    entry_time = datetime.now().strftime("%Y-%m-%dT%H:%M:%S")

    # default priority is 0
    priority = 0

    # update priority based on tags
    tags = job_details['Tags']
    for tag in tags:
        if tag['Key'] == 'priority':
            priority = int(tag['Value'])
            break

    # create entry
    entry = {
       'jobName': job_name,
       'entryTime': entry_time,
       'jobState': 'pending',
       'priority': priority,
       'jobDetails': job_json
    }
    table.put_item(Item=entry) #TODO handle errors
    print(f'Added job {job_name} to queue')
```

我们即将定义的REST API *add-job* 方法将被编程为调用 *add_job_entry* 函数。

我们定义了第二个函数，从数据库中提取待处理作业，并按优先级返回它们。如果多个作业具有相同的优先级，它们将根据在队列中等待的时间长短进行排序。

```py
from boto3.dynamodb.conditions import Attr

# Get a list of all pending jobs sorted by priority
def get_pending_jobs():
    response = table.scan(
        ProjectionExpression='jobName, priority, entryTime',
        FilterExpression=Attr('jobState').ne('running')
    )
    jobs = response.get('Items', [])

    # sort jobs, first by priority (descending) and then by entryTime
    sorted_jobs = sorted(jobs,
                         key=lambda x: (-x['priority'], x['entryTime']))

    return sorted_jobs
```

以下实用功能将在接下来的章节中派上用场。

```py
# Get a jobName -> priority mapping of all running jobs
def get_running_jobs_dict():
    # Get all running jobs
    response = table.scan(
        ProjectionExpression="jobName, priority",
        FilterExpression=Attr('jobState').eq('running')
    )
    jobs = response.get('Items', [])

    running_jobs = {job['jobName']: job['priority'] for job in jobs}

    return running_jobs

# Print the queue state
def print_queue_state():
    response = table.scan(
        ProjectionExpression='jobName, jobState, priority'
    )
    jobs = response.get('Items', [])

    print_table = []
    for job in jobs:
        print_table.append([job['jobName'], job['jobState'], job['priority']])

    # sort by priority
    sorted_table = sorted(print_table,
                         key=lambda x: -x[2])
    # Print the table
    from tabulate import tabulate
    print(tabulate(sorted_table, headers=['Job Name', 'State', 'Priority']))

# get job details
def get_job_details(job_name):
    response = table.get_item(
        Key={'jobName': job_name},
        ProjectionExpression='jobDetails'
    )
    return json.loads(response.get('Item').get('jobDetails'))

# get job state or None if the job does not exist
def get_job_state(job_name):
    response = table.get_item(
        Key={'jobName': job_name},
        ProjectionExpression='jobState'
    )
    job = response.get('Item')
    return job.get('jobState') if job else None

# update the job state
def update_job_state(job_name, new_state):
    table.update_item(
        Key={'jobName': job_name},
        UpdateExpression="SET jobState = :new_state",
        ExpressionAttributeValues={":new_state": new_state}
    )
    print(f'Update job {job_name} to {new_state}')

# remove a job entry
def remove_job(job_name):
    table.delete_item(
        Key={'jobName': job_name}
    )
    print(f'Removed job {job_name} from queue')
```

我们选择DynamoDB及其使用（例如，使用[Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) API而不是[Query](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Query.html) API）假设队列中的作业总数最多为几十个。对于更大规模的解决方案，您可能需要选择一个更强大的数据库（例如，能够为您执行排序操作的数据库）或更复杂的DynamoDB使用方式（例如，参见[这里](https://aws.amazon.com/blogs/database/implementing-priority-queueing-with-amazon-dynamodb/)）。

## 定义训练作业队列管理器

我们解决方案的主要组件是训练作业调度器。在这里，我们实现了一个相对简单的管理器，执行以下步骤：

1.  提取按优先级排序的队列中作业列表。如果没有作业，返回。

1.  发现未使用的实例容量。对于每个空闲实例，在SageMaker上启动一个待处理作业。如果没有剩余作业，返回。

1.  计算处于*停止*状态的SageMaker作业数量。如果超过待处理作业的数量，返回。

1.  通过将运行中的SageMaker作业的*优先级*与待处理作业的优先级进行比较，评估是否需要抢占正在运行的作业。

```py
# set the limit on total number of instances/jobs
MAX_CAPACITY = 2

sagemaker = boto3.client('sagemaker')

# apply a queue stamp to identify that the job came from the queue
def apply_qstamp(job_name):
    return f'{job_name}-qstamp-{datetime.now().strftime("%d%H%M")}'

# strip the queue stamp
def strip_qstamp(job_name):
    return job_name.split('-qstamp-')[0]

# start a SageMaker job and update job entry in queue
def start_job(job_name):
    print(f'start job {job_name}')
    job_details = get_job_details(job_name)
    job_details['TrainingJobName'] = apply_qstamp(job_name)
    if(job_details):
        # start job with detail from queue
        # (you may optinally overwrite fields such as the iam role)
        response = sagemaker.create_training_job(**job_details)
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            print(f'started job {job_name}')
            update_job_state(job_name, 'running')

# preempt a SageMaker job and update job entry in queue
def preempt_job(job_name):
    print(f'preempt job {job_name}')
    response = sagemaker.stop_training_job(TrainingJobName=job_name)
    if response['ResponseMetadata']['HTTPStatusCode'] == 200:
        print(f'preempted job {job_name}')
        update_job_state(strip_qstamp(job_name), 'preempted')

# get SageMaker jobs
def get_sagemaker_jobs(status):
    running = sagemaker.list_training_jobs(StatusEquals=status)
    return running.get('TrainingJobSummaries', [])

# queue manager
def manage_queue():
    # extract pending jobs to run
    pending = get_pending_jobs()

    if not pending:
        return

    if len(pending) > MAX_CAPACITY:
        pending = pending[:MAX_CAPACITY]

    # get running sagemaker jobs
    running = get_sagemaker_jobs('InProgress')
    total_running = len(running)

    # get stopping sagemaker jobs
    stopping = get_sagemaker_jobs('Stopping')
    total_stopping = len(stopping)

    # calculate the number of free instances    
    free_slots = MAX_CAPACITY - total_running - total_stopping

    jobs_to_start = min(len(pending), free_slots)

    # for each free instance, start a job
    for i in range(jobs_to_start):
        start_job(pending[i].get('jobName'))

    still_pending = pending[jobs_to_start:]

    if not still_pending:
        return

    # assume that 'total_stopping' number of jobs will start soon
    test_for_preemption = len(still_pending) - total_stopping
    if test_for_preemption <= 0:
        return

    # check if preemption is required
    test_priority = still_pending[total_stopping:]

    running_jobs = get_running_jobs_dict()
    priority_dict = {}
    for job in running:
        job_name = job['TrainingJobName']
        priority_dict[job_name] = running_jobs[strip_qstamp(job_name)]

    # sort running jobs from lowest to highest priority
    sorted_running = sorted(priority_dict.items(), key=lambda item: item[1])

    index = 0
    while index < test_for_preemption and \
          test_priority[index].get('priority') > sorted_running[index][1]:
        preempt_job(sorted_running[index][0])
        index = index + 1
```

重要提示：

1.  我们的实现非常乐观，假设所有插入的作业都是有效的，并且我们可以在SageMaker上顺利启动它们。实际上，应该添加适当的错误处理（例如，使用适当的日志记录从队列中移除有问题的作业）。

1.  在生产环境中，我们需要考虑当多个并发事件触发我们的[竞争条件](https://en.wikipedia.org/wiki/Race_condition)时，可能会发生的情况。解决此问题的方法有多种（例如，参见[这里](https://medium.com/@moradiyabhavik/race-condition-understanding-and-solution-926b6d2808cf)），包括强制原子性（例如，通过将我们的[Lambda函数并发](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html)设置为1）、使用某种形式的锁机制（例如，如[这里](https://aws.amazon.com/blogs/database/implementing-priority-queueing-with-amazon-dynamodb/)所做的），或使我们的函数具有[幂等性](https://en.wikipedia.org/wiki/Idempotence)。在这里，我们采用了我们所称的“乐观幂等性”方法，依赖于API的适当使用以及底层调用SageMaker API的幂等性。

1.  我们强调，我们的实现是初级的。实际上，我们建议使用更复杂的算法，1）考虑使用不同类型的实例和需要多个实例的任务，2）考虑所有边界情况，3）根据项目的具体需求量身定制。

## 定义AWS Lambda函数

解决方案的下一个组件是Lambda函数。以下代码块包括我们无服务器函数的[SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-function.html)定义。我们编写该函数以响应两种不同类型的事件：对我们私有API网关上的[*add-job*](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-api.html)的任何调用，以及[SageMaker训练任务状态的变化](https://docs.aws.amazon.com/sagemaker/latest/dg/automating-sagemaker-with-eventbridge.html)。

```py
 ManagedTrainingJobQueue:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: job-queue/ # the directory containing our index.py file
      Handler: index.lambda_handler
      Runtime: python3.12
      Architectures:
        - arm64 # use graviton
      Policies: # allow access to SageMaker and DynamoDB
        - !Sub "arn:${AWS::Partition}:iam::aws:policy/AmazonSageMakerFullAccess"
        - DynamoDBCrudPolicy:
            TableName: !Ref DynamoSMQueue
      Events:
        CreateTraining:
          Type: Api
          Properties:
            Path: /add-job
            Method: post
            RestApiId: !Ref InternalAPI
        SageMakerEvent:
          Type: EventBridgeRule
          Properties:
            Pattern:
              source:
                - aws.sagemaker
              detail-type:
                - SageMaker Training Job State Change
              detail:
                TrainingJobStatus:
                  - "Completed"
                  - "Failed"
                  - "Stopped"
```

*lambda_handler*函数实现如下：

```py
def lambda_handler(event, context):
    # identify source of event and take appropriate action
    if 'requestContext' in event and 'apiId' in event['requestContext']:
        print('Lambda triggerred by API Gateway')
        job_details = json.loads(event.get('body'))
        add_job_entry(job_details)
    elif 'source' in event and event['source'] == 'aws.sagemaker':
        print('Lambda triggerred by SageMaker job state change')
        job_name = event['detail']['TrainingJobName']
        job_status = event['detail']['TrainingJobStatus']
        print(f'{job_name} status changed to {job_status}')

        # strip qstamp from job_name
        job_name = strip_qstamp(job_name)

        if job_status in ['Completed' , 'Failed']:
            remove_job(job_name)
        elif job_status == 'Stopped':
            # check if it was manually stopped or preempted by queue manager
            if get_job_state(job_name) == 'preempted':
                print(f'job {job_name} preemption completed')
            else:
                print(f'job {job_name} {job_status}, remove from queue')
                remove_job(job_name)

    # in all cases invoke queue manager
    manage_queue()
```

## 拦截创建训练任务请求

为了完成我们的解决方案，最后需要修改的部分是拦截对SageMaker的[create_training_job](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sagemaker/client/create_training_job.html) API的调用，并将其重新路由到我们的*add-job*方法。我们通过重写[SageMaker会话类](https://sagemaker.readthedocs.io/en/stable/api/utility/session.html)的[_intercept_create_request](https://github.com/aws/sagemaker-python-sdk/blob/v2.208.0/src/sagemaker/session.py#L6212)函数来实现这一点：

```py
from sagemaker.pytorch import PyTorch
from sagemaker.session import Session
import requests, logging
logger = logging.getLogger('sagemaker')

def submit_to_training_queue(job):
    logger.info(f'Adding training-job {job['TrainingJobName']} to queue')
    logger.debug('train request: {json.dumps(job, indent=4)}')

    vpce='<vpc endpoint>' # insert id of vpc endpoint
    region='us-east-1' # specify region
    url=f'https://{vpce}.execute-api.{region}.vpce.amazonaws.com/prod/add-job'
    headers = {'x-apigw-api-id': '<api-id>'} # insert api gateway id

    # submit job
    response = requests.post(url, headers=headers, json=job)

class QueueTrainingJobSession(Session):
    def _intercept_create_request(self, request, create, func_name = None):
        """This function intercepts the create job request

        Args:
          request (dict): the create job request
          create (functor): a functor calls the sagemaker client create method
          func_name (str): the name of the function needed intercepting
        """
        if func_name == 'train':
            submit_to_training_queue(request)
        else:
            super()._intercept_create_request(request,create,func_name)

# define job
estimator = PyTorch(
    role='<sagemaker role>',
    entry_point='train.py',
    instance_type='ml.p5.48xlarge',
    instance_count=1,
    framework_version='2.0.1',
    py_version='py310',
    tags=[{'Key': 'priority', 'Value': '100'},
    keep_alive_period_in_seconds=60, # keep warm for 1 minute
    # use our custom Session class
    sagemaker_session=QueueTrainingJobSession()
)

estimator.fit(wait=False)
```

# 用例示例

为了测试我们的解决方案，我们提交以下任务序列。每次调用后，我们打印队列状态（使用*print_queue_state*函数），并暂停20秒。

1.  使用优先级1启动job1。

1.  使用优先级2启动job2。

1.  使用优先级1启动job3。

1.  使用优先级3启动job4。

前两个作业会立即提交到 SageMaker，并更新为*运行中*状态。由于第三个作业的优先级较低，并且我们恰好有两个训练实例，它会保持在*待处理*状态，等待轮到它。提交前三个作业后，队列状态如下所示：

```py
Job Name    State      Priority
----------  -------  ----------
job2        running           2
job1        running           1
job3        pending           1
```

我们提交的第四个作业优先级高于队列中所有作业。因此，优先级最低的正在运行作业*job1*会被抢占。相应的 SageMaker 作业被停止，一旦实例释放，队列状态变为：

```py
Job Name    State        Priority
----------  ---------  ----------
job4        running             3
job2        running             2
job1        preempted           1
job3        pending             1
```

正在运行的 SageMaker 作业*job2*是第一个完成的，*job2*从队列中移除，我们被抢占的作业恢复执行：

```py
Job Name    State      Priority
----------  -------  ----------
job4        running           3
job1        running           1
job3        pending           1
```

一旦*job4*完成，它也会从队列中移除，为*job3*腾出位置。剩余的作业也会运行到完成，最终使我们的队列为空。

# 摘要

获取 AI 计算能力的难度不断增加，迫使 AI 开发团队重新评估他们用于训练 AI 模型的流程。我们在这篇文章中展示的方法是通过自定义优先级队列和关联的作业调度器，增强传统的模型训练 API。重要的是，我们提出的方案应该被视为一个通用方案，而不是一个生产级解决方案。为了满足您项目的具体需求，需要进行适当的修改和增强。
