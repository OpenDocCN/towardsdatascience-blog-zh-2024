# 如何实现基于 Amazon EC2 的定制训练解决方案

> 原文：[`towardsdatascience.com/how-to-implement-a-custom-training-solution-based-on-amazon-ec2-c91fcc2b145a?source=collection_archive---------15-----------------------#2024-01-30`](https://towardsdatascience.com/how-to-implement-a-custom-training-solution-based-on-amazon-ec2-c91fcc2b145a?source=collection_archive---------15-----------------------#2024-01-30)

## 云端 ML 训练管理的简单解决方案 — 第二部分

[](https://chaimrand.medium.com/?source=post_page---byline--c91fcc2b145a--------------------------------)![Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--c91fcc2b145a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c91fcc2b145a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c91fcc2b145a--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--c91fcc2b145a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c91fcc2b145a--------------------------------) ·11 分钟阅读·2024 年 1 月 30 日

--

![](img/811d45664ed39ded00ae5b3892017d18.png)

图片由 [Vlad D](https://unsplash.com/@hiking_corgi?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是对上一篇文章的续集，上一篇文章讨论了如何使用低级实例配置服务构建定制的云端机器学习（ML）模型开发解决方案。在本文中，我们的重点将放在 [Amazon EC2](https://aws.amazon.com/ec2/) 上。

云服务提供商（CSP）通常会提供完全托管的解决方案，用于在云端训练机器学习模型。例如，[Amazon SageMaker](https://aws.amazon.com/sagemaker/)是亚马逊提供的机器学习开发托管服务，它显著简化了训练过程。SageMaker 不仅自动化了端到端的训练执行 —— 从自动配置所需的实例类型、设置训练环境、执行训练任务，到保存训练产物并关闭所有服务 —— 还提供了多个辅助服务，支持机器学习开发，如[自动模型调优](https://docs.aws.amazon.com/sagemaker/latest/dg/automatic-model-tuning.html)、[平台优化的分布式训练库](https://docs.aws.amazon.com/sagemaker/latest/dg/distributed-training.html)等。然而，正如高层次解决方案常有的情况，SageMaker 的易用性提高的同时，也伴随着对底层流程控制的一定丧失。

在我们的上一篇文章中，我们提到了一些由托管训练服务（如 SageMaker）有时强加的限制，包括用户权限减少、某些实例类型无法访问、对多节点设备放置的控制减少等。一些场景需要对环境规范和训练流程有更高的自主权。在本文中，我们通过在 Amazon EC2 上构建一个自定义训练解决方案，展示了解决这些情况的一种方法。

非常感谢 [Max Rabin](https://www.linkedin.com/in/maxrabin/) 对本文的贡献。

# 穷人的托管训练在 Amazon EC2 上

在我们上一篇文章中，我们列出了自动化训练解决方案所需的最小功能集，并逐步展示了如何在 [Google Cloud Platform (GCP)](https://cloud.google.com/) 上实现这些功能。尽管相同的步骤序列可以应用于任何其他云平台，但由于每个平台的独特细节，实际情况可能会有所不同。本文的目的是提出一个基于 Amazon EC2 的实现方案，使用 [AWS Python SDK](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)（版本 1.34.23）的 [create_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/service-resource/create_instances.html) 命令。与上一篇文章一样，我们将从一个简单的 EC2 实例创建命令开始，并逐步补充其他组件，以加入我们需要的管理功能。[create_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/service-resource/create_instances.html) 命令支持许多控制选项。在本演示中，我们将只关注与我们解决方案相关的部分。我们假设已存在一个 [默认 VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) 和一个具有适当权限的 [IAM 实例配置文件](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html)（包括访问 Amazon EC2、S3 和 CloudWatch 服务的权限）。

请注意，使用 Amazon EC2 来满足我们定义的最小功能集有多种方式。我们选择演示其中一种可能的实现方式。请不要将我们选择 AWS、EC2 或我们所选特定实现的任何细节视为推荐。适合您的最佳机器学习训练解决方案将极大地依赖于您项目的具体需求和细节。

# 1\. 创建 EC2 实例

我们从一个最小的单个 EC2 实例请求示例开始。我们选择了一个 GPU 加速的 [g5.xlarge](https://aws.amazon.com/ec2/instance-types/g5/) 实例类型和一个最新的 [深度学习 AMI](https://docs.aws.amazon.com/dlami/)（带有 Ubuntu 20.4 操作系统）。

```py
import boto3

region = 'us-east-1'
job_id = 'my-experiment' # replace with unique id
num_instances = 1
image_id = 'ami-0240b7264c1c9e6a9' # replace with image of choice
instance_type = 'g5.xlarge' # replace with instance of choice

ec2 = boto3.resource('ec2', region_name=region)

instances = ec2.create_instances(
    MaxCount=num_instances,
    MinCount=num_instances,
    ImageId=image_id,
    InstanceType=instance_type,
)
```

# 2\. 自动启动训练

我们希望应用的第一个增强功能是，使我们的训练工作负载在实例启动并运行后自动开始，无需任何人工干预。为了实现这一目标，我们将利用[create_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/service-resource/create_instances.html) API 的*UserData*参数，该参数使你能够[指定启动时运行的内容](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)。在下面的代码块中，我们提出了一系列命令，用于设置训练环境（即更新*PATH*环境变量，使其指向我们镜像中包含的预构建 PyTorch 环境）、从[Amazon S3](https://aws.amazon.com/s3/)下载我们的训练代码、安装项目依赖项、运行训练脚本，并将输出产物同步到持久化的 S3 存储。示例假设训练代码已经创建并上传到云端，并且包含两个文件：一个是依赖文件（*requirements.txt*），另一个是独立的训练脚本（*train.py*）。实际上，启动序列的具体内容将取决于项目需求。我们还包括了指向我们预定义的 IAM 实例配置文件的指针，该配置文件是访问 S3 所必需的。

```py
import boto3

region = 'us-east-1'
job_id = 'my-experiment' # replace with unique id
num_instances = 1
image_id = 'ami-0240b7264c1c9e6a9' # replace with image of choice
instance_type = 'g5.xlarge' # replace with instance of choice
instance_profile_arn = 'instance-profile-arn' # replace with profile arn

ec2 = boto3.resource('ec2', region_name=region)

script = """#!/bin/bash
         # environment setup
         export PATH=/opt/conda/envs/pytorch/bin/python:$PATH

         # download and unpack code
         aws s3 cp s3://my-s3-path/my-code.tar .
         tar -xvf my-code.tar

         # install dependencies
         python3 -m pip install -r requirements.txt

         # run training workload
         python3 train.py

         # sync output artifacts
         aws s3 sync artifacts s3://my-s3-path/artifacts
         """

instances = ec2.create_instances(
    MaxCount=num_instances,
    MinCount=num_instances,
    ImageId=image_id,
    InstanceType=instance_type,
    IamInstanceProfile={'Arn':instance_profile_arn},
    UserData=script
 )
```

请注意，上面的脚本只会在训练结束时同步训练产物。一种更具容错性的解决方案是在整个训练任务期间同步中间模型检查点。

# 3\. 完成后自毁

当你使用托管服务进行训练时，实例会在脚本完成后自动关闭，以确保你只为需要的部分付费。在下面的代码块中，我们在*UserData*脚本的末尾附加了一个自毁命令。我们通过 AWS CLI [terminate-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) 命令来实现这一点。该命令要求我们知道实例的*instance-id*和托管*region*，这些信息可以从[实例元数据](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html)中提取。我们的更新脚本假设我们的 IAM 实例配置文件具有适当的实例终止权限。

```py
script = """#!/bin/bash
         # environment setup
         TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H \
                              "X-aws-ec2-metadata-token-ttl-seconds: 21600")
         INST_MD=http://169.254.169.254/latest/meta-data
         CURL_FLAGS="-H \"X-aws-ec2-metadata-token: ${TOKEN}\" -s"
         INSTANCE_ID=$(curl $CURL_FLAGS $INST_MD/instance-id)
         REGION=$(curl $CURL_FLAGS $INST_MD/placement/region)
         export PATH=/opt/conda/envs/pytorch/bin/python:$PATH

         # download and unpack code
         aws s3 cp s3://my-s3-path/my-code.tar .
         tar -xvf my-code.tar

         # install dependencies
         python3 -m pip install -r requirements.txt

         # run training workload
         python3 train.py

         # sync output artifacts
         aws s3 sync artifacts s3://my-s3-path/artifacts

         # self-destruct
         aws ec2 terminate-instances --instance-ids $INSTANCE_ID \
                                     --region $REGION
         """
```

我们强烈建议引入额外的机制来验证适当的实例删除，以避免系统中出现未使用的（“孤立”）实例，从而积累不必要的费用。在一篇[最近的文章](https://medium.com/towards-data-science/using-server-less-functions-to-govern-and-monitor-cloud-based-training-experiments-755c43fba26b)中，我们展示了如何使用无服务器函数来解决这种问题。

# 4\. 为 EC2 实例应用自定义标签

Amazon EC2 允许你通过[使用 EC2 实例标签](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)为实例应用自定义元数据。这使得你可以将关于训练工作负载和/或训练环境的信息传递给实例。在这里，我们使用*TagSpecifications*设置传入实例名称和唯一的训练任务 ID。我们使用该唯一 ID 来为我们的任务工件定义一个专用的 S3 路径。请注意，我们需要通过*MetadataOptions*设置显式启用实例访问元数据标签的功能。

```py
import boto3

region = 'us-east-1'
job_id = 'my-experiment' # replace with unique id
num_instances = 1
image_id = 'ami-0240b7264c1c9e6a9' # replace with image of choice
instance_type = 'g5.xlarge' # replace with instance of choice
instance_profile_arn = 'instance-profile-arn' # replace with profile arn

ec2 = boto3.resource('ec2', region_name=region)

script = """#!/bin/bash
         # environment setup
         TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H \
                              "X-aws-ec2-metadata-token-ttl-seconds: 21600")
         INST_MD=http://169.254.169.254/latest/meta-data
         CURL_FLAGS="-H \"X-aws-ec2-metadata-token: ${TOKEN}\" -s"
         INSTANCE_ID=$(curl $CURL_FLAGS $INST_MD/instance-id)
         REGION=$(curl $CURL_FLAGS $INST_MD/placement/region)
         JOB_ID=$(curl $CURL_FLAGS $INST_MD/tags/instance/JOB_ID)
         export PATH=/opt/conda/envs/pytorch/bin/python:$PATH

         # download and unpack code
         aws s3 cp s3://my-s3-path/$JOB_ID/my-code.tar .
         tar -xvf my-code.tar

         # install dependencies
         python3 -m pip install -r requirements.txt

         # run training workload
         python3 train.py

         # sync output artifacts
         aws s3 sync artifacts s3://my-s3-path/$JOB_ID/artifacts

         # self-destruct
         aws ec2 terminate-instances --instance-ids $INSTANCE_ID \
                                     --region $REGION
         """

instances = ec2.create_instances(
    MaxCount=num_instances,
    MinCount=num_instances,
    ImageId=image_id,
    InstanceType=instance_type,
    IamInstanceProfile={'Arn':instance_profile_arn},
    UserData=script,
    MetadataOptions={"InstanceMetadataTags":"enabled"},
    TagSpecifications=[{
        'ResourceType': 'instance',
        'Tags': [
            {'Key': 'NAME', 'Value': 'test-vm'},
            {'Key': 'JOB_ID', 'Value': f'{job_id}'}
        ]
    }],
)
```

使用元数据标签将信息传递给我们的实例，在接下来的部分中将特别有用。

# 5\. 将应用程序日志写入持久存储

自然，我们需要能够分析训练过程中的和训练后的应用程序输出日志。这要求日志能够定期同步到持久化日志中。在这篇文章中，我们使用[Amazon CloudWatch](https://docs.aws.amazon.com/cloudwatch/)来实现这一点。以下我们定义了一个最小的 JSON 配置文件，用于启用 CloudWatch 日志收集，并将其添加到我们的源代码 tar 包中，文件名为*cw_config.json*。有关[CloudWatch 设置和配置](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html)的详细信息，请参阅官方文档。

```py
{
    "logs": {
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/output.log",
                        "log_group_name": "/aws/ec2/training-jobs",
                        "log_stream_name": "job-id"
                    }
                ]
            }
        }
    }
}
```

在实际操作中，我们希望*log_stream_name*能够唯一标识训练任务。为此，我们使用[sed](https://www.gnu.org/software/sed/manual/sed.html)命令将通用的“job-id”字符串替换为上一节中的作业 ID 元数据标签。我们增强后的脚本还包括 CloudWatch 启动命令，并修改了将标准输出管道到 CloudWatch 配置文件中定义的*output.log*的部分。

```py
script = """#!/bin/bash
         # environment setup
         TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H \
                              "X-aws-ec2-metadata-token-ttl-seconds: 21600")
         INST_MD=http://169.254.169.254/latest/meta-data
         CURL_FLAGS="-H \"X-aws-ec2-metadata-token: ${TOKEN}\" -s"
         INSTANCE_ID=$(curl $CURL_FLAGS $INST_MD/instance-id)
         REGION=$(curl $CURL_FLAGS $INST_MD/placement/region)
         JOB_ID=$(curl $CURL_FLAGS $INST_MD/tags/instance/JOB_ID)
         export PATH=/opt/conda/envs/pytorch/bin/python:$PATH

         # download and unpack code
         aws s3 cp s3://my-s3-path/$JOB_ID/my-code.tar .
         tar -xvf my-code.tar

         # configure cloudwatch
         sed -i "s/job-id/${JOB_ID}/g" cw_config.json
         /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
                  -a fetch-config -m ec2 -c file:cw_config.json -s

         # install dependencies
         python3 -m pip install -r requirements.txt 2>&1 | tee -a output.log

         # run training workload
         python3 train.py 2>&1 | tee -a output.log

         # sync output artifacts
         aws s3 sync artifacts s3://my-s3-path/$JOB_ID/artifacts

         # self-destruct
         aws ec2 terminate-instances --instance-ids $INSTANCE_ID \
                                     --region $REGION
         """
```

# 6\. 支持多节点训练

现在，训练任务在多个节点并行运行已经相当常见。修改我们的实例请求代码以支持多个节点，只需简单修改*num_instances*设置即可。挑战在于如何以支持分布式训练的方式配置环境，即一种能够 — 并且优化 — 实例之间数据传输的方式。

为了最小化实例之间的网络延迟并最大化吞吐量，我们在 ec2 实例请求的*Placement*字段中添加了一个指向预定义的[集群放置组](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-cluster)的指针。以下命令行演示了如何创建一个集群放置组。

```py
aws ec2 create-placement-group --group-name cluster-placement-group \
    --strategy cluster
```

为了让我们的实例能够相互通信，它们需要知道彼此的存在。在这篇文章中，我们将演示运行[数据并行训练](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html)所需的最小环境配置，使用[PyTorch](https://pytorch.org/)。对于 PyTorch 的[DistributedDataParallel](https://pytorch.org/docs/stable/nn.html#module-torch.nn.parallel)（DDP），每个实例需要知道主节点的 IP 地址、主端口、实例总数以及在所有节点中的序列*rank*。下面的脚本展示了如何使用环境变量*MASTER_ADDR*、*MASTER_PORT*、*NUM_NODES*和*NODE_RANK*配置[数据并行训练](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html)作业。

```py
import os, ast, socket
import torch
import torch.distributed as dist
import torch.multiprocessing as mp

def mp_fn(local_rank, *args):
    # discover topology settings
    num_nodes = int(os.environ.get('NUM_NODES',1))
    node_rank = int(os.environ.get('NODE_RANK',0))
    gpus_per_node = torch.cuda.device_count()
    world_size = num_nodes * gpus_per_node
    node_rank = nodes.index(socket.gethostname())
    global_rank = (node_rank * gpus_per_node) + local_rank
    print(f'local rank {local_rank} '
          f'global rank {global_rank} '
          f'world size {world_size}')
    # init_process_group assumes the existence of MASTER_ADDR
    # and MASTER_PORT environment variables
    dist.init_process_group(backend='nccl',
                            rank=global_rank, 
                            world_size=world_size)
    torch.cuda.set_device(local_rank)
    # Add training logic

if __name__ == '__main__':
    mp.spawn(mp_fn,
             args=(),
             nprocs=torch.cuda.device_count())
```

节点的 rank 可以从[ami-launch-index](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMI-launch-index-examples.html)中获取。节点的数量和主端口在[create_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/service-resource/create_instances.html)调用时已知，并可以作为 EC2 实例标签传递。然而，主节点的 IP 地址仅在主实例创建后确定，并且只能在[create_instances](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/service-resource/create_instances.html)调用之后传递给其他实例。在下面的代码块中，我们选择通过专门调用[AWS Python SDK](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)的[create_tags](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/client/create_tags.html)API 将主节点地址传递给每个实例。我们还使用相同的调用根据每个实例的启动索引值更新它们的名称标签。

多节点训练的完整解决方案如下：

```py
import boto3

region = 'us-east-1'
job_id = 'my-multinode-experiment' # replace with unique id
num_instances = 4
image_id = 'ami-0240b7264c1c9e6a9' # replace with image of choice
instance_type = 'g5.xlarge' # replace with instance of choice
instance_profile_arn = 'instance-profile-arn' # replace with profile arn
placement_group = 'cluster-placement-group' # replace with placement group

ec2 = boto3.resource('ec2', region_name=region)

script = """#!/bin/bash
         # environment setup
         TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H \
                              "X-aws-ec2-metadata-token-ttl-seconds: 21600")
         INST_MD=http://169.254.169.254/latest/meta-data
         CURL_FLAGS="-H \"X-aws-ec2-metadata-token: ${TOKEN}\" -s"
         INSTANCE_ID=$(curl $CURL_FLAGS $INST_MD/instance-id)
         REGION=$(curl $CURL_FLAGS $INST_MD/placement/region)
         JOB_ID=$(curl $CURL_FLAGS $INST_MD/tags/instance/JOB_ID)
         export NODE_RANK=$(curl $CURL_FLAGS $INST_MD/ami-launch-index)
         export NUM_NODES=$(curl $CURL_FLAGS $INST_MD/NUM_NODES)
         export MASTER_PORT=$(curl $CURL_FLAGS $INST_MD/tags/instance/MASTER_PORT)
         export PATH=/opt/conda/envs/pytorch/bin/python:$PATH         

         # download and unpack code
         aws s3 cp s3://my-s3-path/$JOB_ID/my-code.tar .
         tar -xvf my-code.tar

         # configure cloudwatch
         sed -i "s/job-id/${JOB_ID}_${NODE_RANK}/g" cw_config.json
         /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
                  -a fetch-config -m ec2 -c file:cw_config.json -s

         # install dependencies
         python3 -m pip install -r requirements.txt 2>&1 | tee -a output.log

         # retrieve master address
         # should be available but just in case tag application is delayed...
         while true; do
           export MASTER_ADDR=$(curl $CURL_FLAGS $INST_MD/tags/instance/MASTER_ADDR)
           if [[ $MASTER_ADDR == "<?xml"* ]]; then
             echo 'tags missing, sleep for 5 seconds' 2>&1 | tee -a output.log
             sleep 5
           else
             break
           fi
         done

         # run training workload
         python3 train.py 2>&1 | tee -a output.log

         # sync output artifacts
         aws s3 sync artifacts s3://my-s3-path/$JOB_ID/artifacts

         # self-destruct
         aws ec2 terminate-instances --instance-ids $INSTANCE_ID \
                                     --region $REGION
         """

instances = ec2.create_instances(
    MaxCount=num_instances,
    MinCount=num_instances,
    ImageId=image_id,
    InstanceType=instance_type,
    IamInstanceProfile={'Arn':instance_profile_arn},
    UserData=script,
    MetadataOptions={"InstanceMetadataTags":"enabled"},
    TagSpecifications=[{
        'ResourceType': 'instance',
        'Tags': [
            {'Key': 'NAME', 'Value': 'test-vm'},
            {'Key': 'JOB_ID', 'Value': f'{job_id}'},
            {'Key': 'MASTER_PORT', 'Value': '7777'},
            {'Key': 'NUM_NODES', 'Value': f'{num_instances}'}
        ]
    }],
    Placement={'GroupName': placement_group}
)

if num_instances > 1:

    # find master_addr
    for inst in instances:
        if inst.ami_launch_index == 0:
            master_addr = inst.network_interfaces_attribute[0]['PrivateIpAddress']
            break

    # update ec2 tags
    for inst in instances:
        res = ec2.create_tags(
            Resources=[inst.id],
            Tags=[
                {'Key': 'NAME', 'Value': f'test-vm-{inst.ami_launch_index}'},
                {'Key': 'MASTER_ADDR', 'Value': f'{master_addr}'}]
        )
```

# 7\. 支持使用 Spot 实例

一种减少训练成本的流行方法是使用折扣[Amazon EC2 Spot 实例](https://aws.amazon.com/ec2/spot/?cards.sort-by=item.additionalFields.startDateTime&cards.sort-order=asc)。有效地利用 Spot 实例要求你实现一种检测中断的方式（例如，通过监听[终止通知](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-instance-termination-notices.html)）并采取适当的行动（例如，恢复未完成的工作负载）。在下面，我们展示了如何修改脚本，使用*InstanceMarketOptions* API 设置来使用 Spot 实例。

```py
import boto3

region = 'us-east-1'
job_id = 'my-spot-experiment' # replace with unique id
num_instances = 1
image_id = 'ami-0240b7264c1c9e6a9' # replace with image of choice
instance_type = 'g5.xlarge' # replace with instance of choice
instance_profile_arn = 'instance-profile-arn' # replace with profile arn
placement_group = 'cluster-placement-group' # replace with placement group

instances = ec2.create_instances(
    MaxCount=num_instances,
    MinCount=num_instances,
    ImageId=image_id,
    InstanceType=instance_type,
    IamInstanceProfile={'Arn':instance_profile_arn},
    UserData=script,
    MetadataOptions={"InstanceMetadataTags":"enabled"},
    TagSpecifications=[{
        'ResourceType': 'instance',
        'Tags': [
            {'Key': 'NAME', 'Value': 'test-vm'},
            {'Key': 'JOB_ID', 'Value': f'{job_id}'},
        ]
    }],
    InstanceMarketOptions = {
        'MarketType': 'spot',
        'SpotOptions': {
            "SpotInstanceType": "one-time",
            "InstanceInterruptionBehavior": "terminate"
        }
    }
)
```

请查看我们之前的文章（例如，这里和这里），了解一些如何实现 Spot 实例生命周期管理解决方案的思路。

# 概要

针对 AI 开发的托管云服务可以简化模型训练，并降低潜在从业者的入门门槛。然而，在某些情况下，需要对训练过程有更大的控制权。在这篇文章中，我们展示了一种基于 Amazon EC2 构建定制化托管训练环境的方法。当然，解决方案的具体细节将大大依赖于具体项目的需求。

一如既往，欢迎随时通过评论、提问或修改来回复此帖子。
