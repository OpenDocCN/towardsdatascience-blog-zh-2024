# 教授AI智能体记忆的关键洞察

> 原文：[https://towardsdatascience.com/key-insights-for-teaching-ai-agents-to-remember-c23deffe7f1a?source=collection_archive---------10-----------------------#2024-09-10](https://towardsdatascience.com/key-insights-for-teaching-ai-agents-to-remember-c23deffe7f1a?source=collection_archive---------10-----------------------#2024-09-10)

## 关于基于对Autogen“可教智能体”实验的记忆能力构建建议

[](https://medium.com/@sandibesen?source=post_page---byline--c23deffe7f1a--------------------------------)[![Sandi Besen](../Images/97361d97f50269f70b6621da2256bc29.png)](https://medium.com/@sandibesen?source=post_page---byline--c23deffe7f1a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c23deffe7f1a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c23deffe7f1a--------------------------------) [Sandi Besen](https://medium.com/@sandibesen?source=post_page---byline--c23deffe7f1a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c23deffe7f1a--------------------------------) ·阅读时间：16分钟·2024年9月10日

--

记忆无疑正成为智能体AI（Agentic AI）中的一个关键组成部分。随着AI智能体应用场景的复杂化，这些智能体需要从过去的经验中学习，利用存储的特定业务知识，并根据累积的信息适应不断变化的场景。

在我之前的文章“[AI中的记忆：关键好处与投资考量](https://medium.com/towards-data-science/the-important-role-of-memory-in-agentic-ai-896b22542b3e)”中，我探讨了记忆为何对AI至关重要，讨论了它在回忆、推理和持续学习中的作用。然而，本文将直接深入记忆的实施，通过在流行智能体框架[Autogen](https://microsoft.github.io/autogen/)中考察其“可教性”功能来分析其影响。

***注意****:* *尽管本文具有技术性，但它对技术专业人士和希望评估记忆在智能体AI系统中作用的商业领袖同样具有价值。我已将其结构化，以便读者可以跳过代码部分，依然能够理解记忆如何增强AI系统的响应。如果你不想深入跟随代码，你可以阅读每个步骤的描述，了解过程的概况……或者直接阅读主要发现与建议部分。*

![](../Images/8d57b87125a4809bbd544b77bc1db128.png)

来源：Dalle3 ，提示作者：Sandi Besen

# 主要发现与建议

我对Autogen的可教智能体进行的探索揭示了它们在处理简单和复杂记忆任务时的潜力与局限性。

初步使用时，Autogen的可教学代理的表现不如预期。该代理的推理能力将记忆混合在一起，产生了低效的结果，而且其内建的检索机制没有设置为适应回答复杂问题所需的多步搜索。**这一局限性表明，如果你想使用Autogen的可教学代理，必须进行大量定制，以补充推理能力并实现更复杂的记忆检索。**

**为了构建更强大的记忆能力，实施多步搜索功能至关重要**。单一的记忆搜索通常无法提供完成复杂任务所需的全面信息。实施一系列互相关联的搜索可以显著增强代理收集和综合相关信息的能力。

**“可教学性”功能虽然强大，但应谨慎使用**。**持续激活而没有监督会导致数据污染并危及可信信息源**。商业领袖和解决方案架构师应考虑实施“人类在环”方法，让用户批准系统学习的内容，而不是将每一个推断视为系统应学习的真实事实。Autogen当前的可教学代理设计中的这种监督缺失可能会带来与不受控制学习相关的重大风险。

最后，从知识库中进行记忆检索的方法在系统的有效性中起着重要作用。**超越简单的最近邻搜索（这是TeachableAgent的默认方式），采用更先进的技术，如混合搜索**（结合关键词和向量方法），**语义搜索或知识图谱的使用**，**可以显著提高检索信息的相关性和准确性**。

# 描述性代码实现

为了恰当地演示外部记忆如何发挥作用，我为一家汽车零部件制造厂创建了一个虚拟场景。按照下面的代码自行实现一个可教学代理。

***场景***：一家汽车零部件制造厂需要制定一个应急计划，以应对可能出现的能源限制。该计划需要灵活，并根据工厂能够使用的电力消耗量以及哪些零部件和型号的需求来调整。

**步骤 1：**

预先设置要求如果你的环境中没有安装autogen，需要通过pip安装autogen，并创建一个配置JSON文件。

一个兼容的配置文件示例，使用Azure OpenAI的服务模型GPT4–o：

```py
[{
    "model": "gpt-4o",
    "api_key": "<YOUR API KEY>",
    "azure_endpoint": "<YOUR ENDPOINT>",
    "api_type": "azure",
    "api_version": "2024-06-01"
}]
```

安装Autogen for python：

```py
pip install pyautogen
```

**步骤 2：**

将必要的库导入到你的笔记本或文件中，并加载配置文件。

```py
import autogen
from autogen.agentchat.contrib.capabilities.teachability import Teachability
from autogen import ConversableAgent, UserProxyAgent

config_list = autogen.config_list_from_json(
    env_or_file="autogenconfig.json", #the json file name that stores the config 
    file_location=".", #this means the file is in the same directory
    filter_dict={
        "model": ["gpt-4o"], #select a subset of the models in your config
    },
)
```

**步骤 3：**

创建代理。由于Autogen框架的工作方式，我们需要创建两个代理。我们使用一个UserProxyAgent来执行任务，并与人类进行互动或替代人类参与（具体取决于所需的人工干预程度）。我们还创建一个Conversable Agent作为“可教代理”（Teachable Agent），它旨在与其他代理互动（而非用户）。你可以在这里了解更多关于[UserProxyAgents](https://microsoft.github.io/autogen/docs/reference/agentchat/user_proxy_agent/)和[ConversableAgents](https://microsoft.github.io/autogen/docs/reference/agentchat/conversable_agent/)的信息。

```py
teachable_agent = ConversableAgent(
    name="teachable_agent",  # the name can't contain spaces
    llm_config={"config_list": config_list, "timeout": 120, "cache_seed": None},  # in this example we disable caching but if it is enabled it caches API requests so that they can be reused when the same request is used
) 

user = UserProxyAgent(
    name="user",
    human_input_mode="ALWAYS", #I want to have full control over the code executed so I am setting human_input_mode to ALWAYS. Other options are NEVER and TERMINATE.
    is_termination_msg=lambda x: True if "TERMINATE" in x.get("content") else False, #setting a termination  message is VERY important because it tells the agent when to finish.
    max_consecutive_auto_reply=0, #we don't need this agent to replies multiple times in a row
    code_execution_config={
        "use_docker": False
    },  # If you are planning on running code look into setting use_docker=True. For this example I am not because then I have to walk through the docker setup, but it is safer than running the code directly.
)
```

**步骤 4：**

为了建立基准，让我们在“教导”代理之前和之后，向代理提出相同的问题。我故意展示了不同难度级别的问题，以展示在需要单步与多步记忆检索的任务上的表现。要成功解决复杂的多步问题，检索机制必须从两个不同的记忆中提供信息，作为上下文供负责生成最终回应的语言模型使用。

**简单问题**：“该设施正面临 40% 的电力短缺。需要优先处理哪些型号？”

**复杂的多步问题**：“该设施正面临 40% 的电力短缺。请为我提供一份详细的机器停用和保留的机器清单。”

正如预期的那样，回复是通用的，并不特别适用于个别业务。

**启用长期记忆之前的回应：**

```py
SIMPLE QUESTION RESPONSE
===========================
>>>>>>>> USING AUTO REPLY...
teachable_agent (to user):

To address a power shortage of 30% effectively, it's crucial to prioritize models that are essential to critical operations, safety, and those that have the highest impact on productivity. Here are steps to determine which models to prioritize:

1\. **Identify Essential Systems:** Prioritize systems that are vital for safety, security, and essential operations. These might include:
   - Emergency lighting
   - Security systems (alarms, surveillance)
   - Critical environmental controls (e.g., refrigeration for hazardous materials)
   - Communication systems

2\. **Evaluate Impact on Core Operations:** Determine which models are crucial to the facility’s core functions. This can include machinery, servers, or any processes that are indispensable for maintaining productivity and operations.

3\. **Energy Efficiency:** Prioritize models and equipment that are more energy-efficient and can operate within the reduced power capacity.

4\. **Downtime Cost:** Consider the cost of downtime for each model. Prioritize those where downtime would result in significant financial losses or production delays.

Based on these principles, here’s a generic prioritization framework:

1\. **Safety and Security Systems:**
   - Emergency Lighting
   - Fire Alarms and Sprinkler Systems
   - Security Cameras and Sensors

2\. **Critical Operational Systems:**
   - Essential Production Machinery (that cannot be shut down without major issues)
   - Servers and IT Infrastructure (especially those critical for operations and data integrity)
   - HVAC Systems (if necessary for maintaining safe operating conditions)

3\. **Support Systems:**
   - Backup Power Systems (uninterruptible power supplies, generators)
   - Communication Systems (both internal and external communication)

Ensure that non-essential and high energy-consuming equipment is powered down or scheduled to operate during off-peak hours if possible.

Always re-evaluate periodically to ensure that the power allocation aligns with the dynamic needs of the facility. Communication with key stakeholders regarding priorities and decisions is also essential for smooth operations during a power shortage.

COMPLEX MULTISTEP QUESTION RESPONSE
====================================
>>>>>>>> USING AUTO REPLY...
teachable_agent (to user):

To effectively manage a facility experiencing a 30% power shortage, we need to prioritize essential operations and consider the criticality and energy consumption of each machine. Here is a detailed breakdown:

### Step 1: Identify Essential Operations

1\. **Critical Infrastructure**: Safety systems, security systems, and essential IT infrastructure.
2\. **Production Lines**: Machines involved in the primary production process.
3\. **Support Systems**: HVAC systems, lighting, and other ancillary equipment necessary to maintain a safe working environment.

### Step 2: Categorize Machines by Importance and Energy Consumption

#### A. Essential and High-Energy Machines

1\. **Safety and Security Systems**
   - Fire alarms and sprinklers
   - Surveillance cameras
   - Security access controls

2\. **Primary Production Machines**
   - Core production lines
   - Central processing units or mainframe servers (for IT-heavy operations)

3\. **Critical Support Systems**
   - Emergency lighting
   - Essential HVAC systems (for maintaining operation-critical temperature and humidity)

#### B. Non-Essential and High-Energy Machines

1\. **Secondary Production Lines**
   - Non-primary or non-critical production lines
   - Equipment used for non-essential or ancillary processes

2\. **Supplementary IT Infrastructure**
   - Servers used for non-essential operations
   - Backup systems that are not in immediate use

3\. **Non-Essential HVAC and Lighting**
   - HVAC systems for non-critical areas
   - Lighting in non-essential zones

#### C. Non-Essential and Low-Energy Machines

1\. **Office Equipment**
   - Computers, printers, and copiers in non-essential offices
2\. **Non-essential Lab or Research Equipment**
   - Devices not directly linked to current essential tasks

### Step 3: Prioritize Deactivation

#### Immediate Deactivation

1\. **Non-Essential and High-Energy Machines**
   - Secondary production lines
   - Non-essential backup servers

2\. **Non-Essential HVAC and Lighting**
   - HVAC in non-critical areas
   - Non-essential lighting

3\. **Non-Essential Office Equipment**
   - Printers, non-essential computers, and copiers

#### Limited Usage

1\. **Essential and High-Energy Machines**
   - Operate critical production lines at reduced capacity if possible
   - Implement rotational operation schedules for essential processes

2\. **Critical Support Systems**
   - Optimize HVAC settings (e.g., raising or lowering set points within acceptable ranges)
   - Reduce lighting to minimum safety standards

### Step 4: Implement Energy Conservation Measures

1\. **Optimize Operations**
   - Utilize energy-efficient settings
   - Implement energy-saving protocols and encourage staff to conserve energy

2\. **Scheduled Downtime**
   - Plan for downtime during off-peak hours for essential machinery to balance the load

3\. **Regular Monitoring**
   - Monitor energy consumption in real-time to make quick adjustments as needed

### Summary

#### Remain Active:
1\. Safety and security systems
2\. Main production lines
3\. Critical IT infrastructure
4\. Essential HVAC and lighting for operational areas

#### Deactivate or Limit Use:
1\. Non-essential production lines
2\. Redundant IT systems
3\. Non-critical HVAC and lighting
4\. Office equipment in non-essential areas

**Note**: These recommendations should be adjusted based on the specific operational requirements of your facility. Additionally, it could be beneficial to communicate these changes to all staff to ensure compliance and understanding.
```

提问基准问题的代码：

```py
 #simple question
user.initiate_chat(teachable_agent, message="The facility is experiencing a power shortage of 40%. What models need to be prioritized?", clear_history=True)
#multistep complex question
user.initiate_chat(teachable_agent, message="The facility is experiencing a power shortage of 30%. Provide me a detailed breakdown of what machines should be deactivated and which machines should remain active.", clear_history=True)
```

**步骤 5：**

创建“教导能力”，然后将其添加到代理中。Teachability 类继承自 AgentCapabiliy 类，基本上允许你向代理添加可定制的功能。

Teachability 类有许多可选参数，可以进一步探索[这里](https://github.com/microsoft/autogen/blob/main/notebook/agentchat_teachability.ipynb)。

默认的 Teachability 类是为代理添加长期记忆的一种快速便捷的方法，但在生产环境中使用时，可能需要进行定制化修改，正如在关键发现部分所概述的那样。它涉及将消息发送到一个分析代理（Analyzer Agent），该代理会评估用户消息是否适合存储和检索。分析代理寻找可能适用于未来类似任务的建议，然后总结并将任务与建议的对存储在一个二进制数据库中，作为代理的“记忆”。

```py
teachability = Teachability(
    verbosity=0,  # 0 for basic info, 1 to add memory operations, 2 for analyzer messages, 3 for memo lists.
    reset_db=True, # we want to reset the db because we are creating a new agent so we don't want any existing memories. If we wanted to use an existing memory store we would set this to false.
    path_to_db_dir="./tmp/notebook/teachability_db", #this is the default path you can use any path you'd like
    recall_threshold=1.5,  # Higher numbers allow more (but less relevant) memos to be recalled.
    max_num_retrievals=10 #10 is default bu you can set the max number of memos to be retrieved lower or higher
)

teachability.add_to_agent(teachable_agent)
```

**步骤 6：**

现在，教导代理（teachable_agent）已经配置完成，我们需要向其提供我们希望代理“学习”（存储到数据库并从中检索）的信息。

根据我们的场景，我希望代理对设施有基本的理解，设施内容包括：

+   制造工厂生产的组件类型

+   组件需要制作的汽车型号类型

+   每个组件使用的机器

此外，我还想提供一些关于设施优先事项的操作性指导，这取决于电力约束的程度。这包括：

+   50%以上能源容量约束情况下的指导

+   25-50%能源容量约束情况下的指导

+   0-25%能源容量约束情况下的指导

```py
business_info = """
# This manufacturing plant manufactures the following vehicle parts:
- Body panels (doors, hoods, fenders, etc.)
- Engine components (pistons, crankshafts, camshafts)
- Transmission parts
- Suspension components (springs, shock absorbers)
- Brake system parts (rotors, calipers, pads)

# This manufactoring plant produces parts for the following models:
- Ford F-150
- Ford Focus
- Ford Explorer
- Ford Mustang
- Ford Escape
- Ford Edge
- Ford Ranger

# Equipment for Specific Automotive Parts and Their Uses

## 1\. Body Panels (doors, hoods, fenders, etc.)
- Stamping presses: Form sheet metal into body panel shapes
- Die sets: Used with stamping presses to create specific panel shapes
- Hydraulic presses: Shape and form metal panels with high pressure
- Robotic welding systems: Automate welding of body panels and structures
- Laser cutting machines: Precisely cut sheet metal for panels
- Sheet metal forming machines: Shape flat sheets into curved or complex forms
- Hemming machines: Fold and crimp edges of panels for strength and safety
- Metal finishing equipment (grinders, sanders): Smooth surfaces and remove imperfections
- Paint booths and spraying systems: Apply paint and protective coatings
- Drying ovens: Cure paint and coatings
- Quality control inspection systems: Check for defects and ensure dimensional accuracy

## 2\. Engine Components (pistons, crankshafts, camshafts)
- CNC machining centers: Mill and drill complex engine parts
- CNC lathes: Create cylindrical parts like pistons and camshafts
- Boring machines: Enlarge and finish cylindrical holes in engine blocks
- Honing machines: Create a fine surface finish on cylinder bores
- Grinding machines: Achieve precise dimensions and smooth surfaces
- EDM equipment: Create complex shapes in hardened materials
- Forging presses: Shape metal for crankshafts and connecting rods
- Die casting machines: Produce engine blocks and cylinder heads
- Heat treatment furnaces: Alter material properties for strength and durability
- Quenching systems: Rapidly cool parts after heat treatment
- Balancing machines: Ensure rotating parts are perfectly balanced
- Coordinate Measuring Machines (CMMs): Verify dimensional accuracy

## 3\. Transmission Parts
- Gear cutting machines: Create precise gear teeth on transmission components
- CNC machining centers: Mill and drill complex transmission housings and parts
- CNC lathes: Produce shafts and other cylindrical components
- Broaching machines: Create internal splines and keyways
- Heat treatment equipment: Harden gears and other components
- Precision grinding machines: Achieve extremely tight tolerances on gear surfaces
- Honing machines: Finish internal bores in transmission housings
- Gear measurement systems: Verify gear geometry and quality
- Assembly lines with robotic systems: Put together transmission components
- Test benches: Evaluate completed transmissions for performance and quality

## 4\. Suspension Components (springs, shock absorbers)
- Coil spring winding machines: Produce coil springs to exact specifications
- Leaf spring forming equipment: Shape and form leaf springs
- Heat treatment furnaces: Strengthen springs and other components
- Shot peening equipment: Increase fatigue strength of springs
- CNC machining centers: Create precision parts for shock absorbers
- Hydraulic cylinder assembly equipment: Assemble shock absorber components
- Gas charging stations: Fill shock absorbers with pressurized gas
- Spring testing machines: Verify spring rates and performance
- Durability test rigs: Simulate real-world conditions to test longevity

## 5\. Brake System Parts (rotors, calipers, pads)
- High-precision CNC lathes: Machine brake rotors to exact specifications
- Grinding machines: Finish rotor surfaces for smoothness
- Die casting machines: Produce caliper bodies
- CNC machining centers: Mill and drill calipers for precise fit
- Precision boring machines: Create accurate cylinder bores in calipers
- Hydraulic press: Compress and form brake pad materials
- Powder coating systems: Apply protective finishes to calipers
- Assembly lines with robotic systems: Put together brake components
- Brake dynamometers: Test brake system performance and durability

"""

business_rules_over50 = """
- The engine components are critical and machinery should be kept online that corresponds to producing these components when capacity constraint is more or equal to 50%: engine components
- Components for the following models should be prioritized when capacity constraint is more or equal to 50%: 1.Ford F-150
"""

business_rules_25to50 = """
- The following components are critical and machinery should be kept online that corresponds to producing these components when capacity constraint is between 25-50%: engine components and transmission parts 
- Components for the following models should be prioritized when capacity constraint is between 25-50%: 1.Ford F-150 2.Ford Explorer
"""

business_rules_0to25 = """
- The following components are critical and machinery should be kept online that corresponds to producing these components when capacity constraint is between 0-25%: engine components,transmission parts, Brake System Parts
- Components for the following models should be prioritized when capacity constraint is between 0-25%: 1.Ford F-150 2.Ford Explorer 3.Ford Mustang 4.Ford Focus
"""
```

```py
user.initiate_chat(teachable_agent, message=business_info, clear_history=True)
```

```py
user.initiate_chat(teachable_agent, message=business_rules_over50, clear_history=True)
```

```py
user.initiate_chat(teachable_agent, message=business_rules_25to50, clear_history=True)
```

```py
user.initiate_chat(teachable_agent, message=business_rules_0to25, clear_history=True)
```

## **步骤7：**

现在我们已经将重要信息添加到长期记忆中，让我们再问代理相同的问题，并查看它的回答有什么不同。

**简单问题：**“设施目前面临40%的电力短缺。需要优先考虑哪些型号？”

**复杂的多步骤问题：**“设施目前面临40%的电力短缺。请提供一份详细的机器停用和应保持活动机器的列表。”

**实施长期记忆后的结果：**

可教代理能够部分回答简单问题。它在需要优先考虑的汽车型号上是正确的，但提供了关于哪些组件需要包括的额外错误信息。

复杂的多步骤问题未能得到充分解决。它错误地将0-25%和25-50%容量限制的两个不同业务指南合并为一个泛化的指南，导致了不准确的回答。此外，它仅提供了每个组件生产中应保持活动或应停用的部分机器列表，遗漏了完整操作协议中的关键细节。这表明从多个独立的记忆源中准确检索和综合信息来回答复杂问题的挑战。

```py
SIMPLE QUESTION RESPONSE
===========================
>>>>>>>> USING AUTO REPLY...
teachable_agent (to user):
Given the facility is experiencing a power shortage of 40%, this falls within the 25-50% capacity constraint range. According to your previously established priorities for such a scenario, the following models and components need to be prioritized:

### Components to Prioritize
- Engine components
- Transmission parts
- Brake System Parts

### Models to Prioritize
1\. Ford F-150
2\. Ford Explorer

You should ensure that the machinery necessary to produce these critical components for the Ford F-150 and Ford Explorer is kept online.

COMPLEX MULTISTEP QUESTION RESPONSE
====================================
>>>>>>>> USING AUTO REPLY...
teachable_agent (to user):

In response to the 40% power shortage, let's create a detailed plan to ensure the most critical machinery remains operational while deactivating less essential ones. 

### Critical Prioritization (0-50% capacity constraint):
- **Critical Components**:
  - Engine components (pistons, crankshafts, camshafts)
  - Transmission parts
  - Brake System Parts (rotors, calipers, pads)

- **Prioritized Models**:
  - Ford F-150
  - Ford Explorer

### Machines to Remain Active:
1\. **Engine Component Machines**:
   - Engine Block Milling Machine
   - Crankshaft Grinding Machine
   - Camshaft Milling Machine

2\. **Transmission Part Machines**:
   - Transmission Housing Caster
   - Gear Cutting Machine
   - Transmission Assembly Line

3\. **Brake System Part Machines**:
   - Brake Rotor Casting Machine
   - Brake Caliper Milling Machine
   - Brake Pad Press

### Machines to Deactivate:
1\. **Body Panel Machines**:
   - Door Stamping Machine
   - Hood Assembly Machine
   - Fender Bending Machine

2\. **Suspension Component Machines**:
   - Coil Spring Forming Machine
   - Shock Absorber Assembly Line

### Detailed Breakdown:

#### Active Machines:
- **Engine Block Milling Machine**:
  - Produces critical engine blocks, essential for Ford F-150 and Ford Explorer.
- **Crankshaft Grinding Machine**:
  - Ensures high precision crankshafts; crucial for the performance of Ford F-150 and Ford Explorer.
- **Camshaft Milling Machine**:
  - Produces camshafts, necessary for Ford F-150 and Ford Explorer engines.

- **Transmission Housing Caster**:
  - Important for casting the housing of transmissions used in Ford F-150 and Ford Explorer.
- **Gear Cutting Machine**:
  - Precision gears production, vital for Ford F-150 and Ford Explorer transmissions.
- **Transmission Assembly Line**:
  - Assembly of complete transmission units for Ford F-150 and Ford Explorer.

- **Brake Rotor Casting Machine**:
  - Produces brake rotors, crucial for the safe braking systems in Ford F-150 and Ford Explorer.
- **Brake Caliper Milling Machine**:
  - Machining of brake calipers, essential for Ford F-150 and Ford Explorer.
- **Brake Pad Press**:
  - Manufacturing of brake pads, necessary for ensuring braking efficiency in Ford F-150 and Ford Explorer.

#### Deactivated Machines:
- **Door Stamping Machine**:
  - While important, body panels like doors are less critical than engine and transmission components under current constraints.
- **Hood Assembly Machine**:
  - Deactivating hood assembly to free up power for critical components manufacturing.
- **Fender Bending Machine**:
  - Non-critical compared to engine and brake components during a significant power shortage.
- **Coil Spring Forming Machine**:
  - Suspension springs are lower priorities under current circumstances.
- **Shock Absorber Assembly Line**:
  - Suspension components aren’t critical enough to keep operational during this level of power shortage.

This plan ensures that production focuses on maintaining the critical components needed for primary models, the Ford F-150 and Ford Explorer, thereby maximizing the utility of the remaining power while managing production constraints effectively.
```

代码：

```py
#simple question
user.initiate_chat(teachable_agent, message="The facility is experiencing a power shortage of 40%. What models need to be prioritized?", clear_history=True)
#multistep complex question
user.initiate_chat(teachable_agent, message="The facility is experiencing a power shortage of 30%. Provide me a detailed breakdown of what machines should be deactivated and which machines should remain active.", clear_history=True)
```

# 结论

虽然 Autogen 提供了一个关于具有记忆的 AI 系统的直接介绍，但在有效处理复杂任务方面仍显不足。

***在开发具有记忆能力的AI代理系统时，请考虑专注于以下关键能力****：*

+   **实施多步骤搜索**以确保全面和相关的结果。这使得代理能够评估搜索结果的有效性，并利用检索到的信息解决查询的各个方面。此外，**考虑使用更先进的检索方法**，例如语义搜索、混合搜索或知识图谱，以获得最佳结果。

+   为了限制数据中毒的潜力，**制定一个深思熟虑的方法，决定谁可以“教”代理以及何时代理应“学习”**。根据业务或开发者设定的指南，还可以利用代理推理来判断是否应由谁将某些内容添加到记忆中。

+   通过**添加记忆衰减机制**，可以消除检索过时信息的可能性，该机制可判断何时记忆不再相关或何时需要用更新的记忆替换它。

+   **对于多智能体系统**，涉及群聊或智能体之间的信息共享时，**探索各种通信模式**。确定传递补充知识的最有效方法，并设定限制以防止信息过载。

注：本文中表达的观点仅代表作者个人意见，并不一定反映其所属单位的观点或政策。

如果你还有问题，或者认为某些内容需要进一步澄清，欢迎在[Linkedin](https://www.linkedin.com/in/sandibesen/)给我发私信！我总是乐于进行深思并不断完善我的工作。
