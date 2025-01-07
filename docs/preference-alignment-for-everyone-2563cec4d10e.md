# 每个人的偏好对齐！

> 原文：[https://towardsdatascience.com/preference-alignment-for-everyone-2563cec4d10e?source=collection_archive---------2-----------------------#2024-11-08](https://towardsdatascience.com/preference-alignment-for-everyone-2563cec4d10e?source=collection_archive---------2-----------------------#2024-11-08)

## 亚马逊SageMaker上的节俭RLHF与多适配器PPO

[](https://medium.com/@aris.tsakpinis?source=post_page---byline--2563cec4d10e--------------------------------)[![Aris Tsakpinis](../Images/2cc1101aed68e1f71a0026bfdec28f58.png)](https://medium.com/@aris.tsakpinis?source=post_page---byline--2563cec4d10e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2563cec4d10e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2563cec4d10e--------------------------------) [Aris Tsakpinis](https://medium.com/@aris.tsakpinis?source=post_page---byline--2563cec4d10e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2563cec4d10e--------------------------------) ·阅读时间：26分钟·2024年11月8日

--

![](../Images/77e9a1dd091dae519aca481072d8bae4.png)

图片由StableDiffusionXL在亚马逊Web服务上提供

注意：除非另有注明，所有图片均为作者提供。

# 这是什么？为什么它很重要？

在过去的两年里，研究和实践提供了大量证据，表明偏好对齐（PA）是提升大语言模型（LLM）性能的游戏规则改变者，尤其是（但不限于）直接暴露于人类的模型。PA利用（人类）反馈来使模型行为与模型实际所处环境中的偏好保持一致，而不是像其他微调方法那样仅仅依赖代理数据集（正如我在[这篇博文](/stepping-out-of-the-comfort-zone-through-domain-adaptation-a-deep-dive-into-dynamic-prompting-4860c6d16224)中详细解释的微调变体）。这种在模型性能上的提升，正如人类用户所感知的，是使LLM和其他基础模型（FM）变得更加可访问和流行的关键因素，显著推动了当前生成式AI的兴奋热潮。

随着时间的推移，研究提出了各种PA方法，并迅速被一些实践者采纳。在这些方法中，RLHF（截至2024年秋季）无疑是最受欢迎且被验证有效的方法。

然而，由于实施复杂性、计算需求或训练协调等挑战，迄今为止，像RLHF这样的PA方法的适应主要限于高技能个人和组织，如FM生产商。此外，我找到的大多数实际示例和教程，展示了如何掌握像RLHF这样的方式，往往是有限或不完整的。

本文为您提供了 RLHF 的全面介绍，讨论了实施中的挑战，并建议使用多适配器 PPO 的 RLHF，这是一种轻量级的实现方法，解决了这些挑战中的一些关键问题。

接下来，我们在 Jupyter notebook 中展示了该方法的端到端（E2E）实现，涵盖了数据收集、准备、模型训练和部署。我们利用 HuggingFace 框架和 Amazon SageMaker 提供了一个用户友好的界面，用于实施、编排和计算资源。接下来的博客文章将引导您浏览这个 notebook 的关键部分，解释实现细节以及每个步骤背后的理论依据。这种实践方式让读者能够理解过程中的实际方面，并轻松复现结果。

# 强化学习从人类反馈（RLHF）的原理

人类反馈的强化学习是早期生成性 AI 热潮的一个重要技术支撑，它为使用大型解码器模型（如 Anthropic Claude 或 OpenAI 的 GPT 模型）所取得的突破提供了额外的推动，使其更加贴近用户需求。

PA 对 FMs 的巨大成功与以用户为中心的产品开发理念完美契合，这是敏捷产品开发的核心且已确立的原则。通过不断地结合实际目标用户的反馈，已被证明在开发卓越产品方面非常有效。这种方法使开发人员能够基于现实世界的用户偏好和需求不断地改进和完善他们的产品，最终带来更成功且用户友好的产品。

像持续预训练（CPT）或监督微调（SFT）等其他微调方法并没有涵盖这一方面，因为：

+   这些方法所使用的数据集（标注或未标注）是我们认为用户喜欢或需要的内容（即知识或信息、语言风格、缩写或任务特定的行为，如遵循指令、健谈等）的代理，这些数据集由负责模型训练或微调数据的少数人创建。

+   这些方法中使用的算法、训练目标和损失函数（即因果语言建模）将下一个词预测作为更高层次指标（例如准确率、困惑度等）的代理。

因此，PA 无疑是我们在致力于为用户创造卓越体验时应该采用的技术。这种方法可以显著提高 AI 生成响应的质量、安全性和相关性，从而带来更令人满意的互动并提高整体用户满意度。

## RLHF 是如何工作的？

*注意：本节内容是我在我的* [*关于不同微调变体的博客文章*](/stepping-out-of-the-comfort-zone-through-domain-adaptation-a-deep-dive-into-dynamic-prompting-4860c6d16224)*中的 RLHF 部分的改编版本。如果你想要了解微调的全面概述，可能还需要查看它。*

![](../Images/0c206faae0d0b57cb16f4bcbf0ba5631.png)

图 1：RLHF 的奖励模型训练（来源：Lambert 等，2022）

RLHF 采用两步过程，具体过程如图 13 和图 14 所示：

第一步（图 1）：首先，需要训练一个奖励模型，以便在实际的强化学习驱动的训练方法中使用。因此，模型将接收一个与目标对齐的提示数据集（例如，聊天/指令模型或特定领域任务目标），该数据集用于优化，并要求模型生成不止一个而是两个或更多推理结果。这些结果将提交给人工标注员进行评分（第一、第二、第三等），评分标准基于优化目标。也有一些开源的偏好排序数据集，其中包括[“Anthropic/hh-rlhf”](https://huggingface.co/datasets/Anthropic/hh-rlhf)（我们将在本博客的实践部分中使用该数据集），该数据集专门针对红队测试以及诚实性和无害性目标。经过归一化并将得分转换为奖励值后，使用每个样本-奖励对来训练奖励模型，其中每个样本是单个模型响应。奖励模型的架构通常与待微调的模型相似，只是在最后适配了一个小型头部，将潜在空间投射为奖励值，而不是标记的概率分布。然而，该模型的理想参数规模仍在研究中，过去不同的模型提供者采取了不同的方式。在本博客的实践部分中，奖励模型我们将使用与待微调模型相同的架构。

![](../Images/762043a6b8c3fb52e183b1886888c937.png)

图 2：基于 PPO 的强化学习模型调优用于 RLHF（来源：Lambert 等，2022）

第2步（图2）：我们的新奖励模型现在用于训练实际的模型。因此，另一组提示词被输入到需要微调的模型中（图示中的灰色框），每次得到一个响应。随后，这些响应被输入到奖励模型中，以检索各自的奖励。接着，使用基于策略的强化学习算法——近端策略优化（PPO），逐步调整模型的权重，以最大化分配给模型回答的奖励。与因果语言建模（CLM——详细解释见[这里](https://medium.com/towards-data-science/stepping-out-of-the-comfort-zone-through-domain-adaptation-a-deep-dive-into-dynamic-prompting-4860c6d16224)）不同，PPO方法不是采用梯度下降，而是利用梯度上升（或者说是对*1 — 奖励*进行梯度下降），因为我们现在试图最大化一个目标（奖励）。为了提高算法的稳定性，防止在训练过程中因RL方法（如PPO）引发的模型行为过度漂移，奖励项中加入了预测偏移惩罚，对偏离初始语言模型预测概率分布过多的回答进行惩罚。

## RLHF面临的挑战

RLHF的工作方式本身就带来了一些核心挑战，特别是在大规模实现和运行方面，以下是其中的一些挑战：

- **奖励模型训练的成本：** 选择合适的奖励模型架构和大小仍然是当前研究的热点。这些模型通常是类似于需要微调的模型的变换器模型，并配备一个修改过的头部，输出奖励分数而非词汇概率分布。这意味着，不论实际选择怎样，大多数奖励模型的参数数量都在数十亿级别。对这种奖励模型进行完整的参数训练需要大量的数据和计算资源。

- **训练集群的成本：** 在训练集群中，需要同时托管奖励模型（用于奖励值）、基础模型（用于KL预测偏移惩罚）和实际被微调的模型三个模型。这会导致巨大的计算需求，通常只有通过多节点的多GPU实例集群（通常部署在云端）才能满足，从而带来硬件和运营成本。

- **训练集群的协调：** RLHF算法需要在每次训练循环中同时进行推理和训练相关的操作。这要求在多节点多GPU集群中进行协调，并尽量减少通信开销，以达到最佳的训练吞吐量。

- **在高度专业化设置中的训练/推理成本：** PA通过将模型性能对齐到用户群体或目标领域而表现突出。由于大多数专业应用场景具有特定的领域和异质的用户群体，这导致了一个有趣的权衡：优化性能将导致训练和托管许多在性能上表现出色的专业化模型。然而，优化资源消耗（即成本）将导致模型的过度泛化，从而降低性能。

## 使用多适配器 PPO 的 RLHF

![](../Images/43d376a2044c3fc5511ddd397cca4912.png)

图3：通过动态多适配器加载最小化PPO的GPU占用

多适配器PPO是RLHF训练过程第二步中一种特别节省GPU资源的方法。它不是使用全参数微调，而是利用参数高效微调（PEFT）技术，显著减少基础设施和协调占用。与其在训练集群中并行托管三个独立的模型（微调模型、奖励模型、用于KL预测偏移惩罚的参考模型），这种方法在微调过程中使用低秩适配（LoRA）适配器，这些适配器会动态加载和卸载到训练集群的加速器中。

![](../Images/3fd297299c11b108133ded22488394b9.png)

图4：使用多适配器PPO进行端到端RLHF训练，用于无害的问答机器人

尽管这种方法的最终目标是对RLHF第二步采取资源和协调节约的方法，但它对第一步也有影响：

+   **奖励模型选择：** 选择与要微调的模型具有相同模型架构的奖励模型，并为其配备奖励分类头。

+   **奖励模型训练方法：** 如图4(2)所示，与全参数奖励模型训练不同，训练的是奖励模型的LoRA适配器，从而减少了训练的占用资源。

与此类似，第二步中进行的RLHF微调并不是采用全参数微调的方式。相反，训练的是LoRA适配器。如图4所示，在一次训练迭代中，首先加载RLHF模型适配器，以便生成当前训练批次提示的模型响应（4a）。然后，加载奖励模型适配器以计算相应的原始奖励值（4b）。为了完成奖励项，输入提示通过基础模型进行计算，以获得KL预测偏移惩罚。因此，所有适配器需要被卸载（4c，4d）。最后，RLHF模型适配器重新加载，以执行此次迭代步骤的权重更新（4e）。

这种RLHF方法显著减少了内存占用和协调复杂性。

# 在HuggingFace和Amazon SageMaker上运行使用多适配器 PPO 的RLHF

在接下来的内容中，我们将通过一个展示多适配器 PPO 在端到端 (E2E) 方式下使用强化学习与人类反馈（RLHF）的笔记本来进行讲解。因此，我们将使用 HuggingFace 和 Amazon SageMaker 提供一个特别用户友好的接口，用于实现、编排和计算层的操作。整个笔记本可以在[这里](https://github.com/aws-samples/build-language-models-on-aws/tree/main/align-models-with-amazon-sagemaker/rlhf-with-multi-adapter-ppo)找到。

# 场景

如今，模型发布的速度令人印象深刻。因此，我希望将我们所讨论的场景保持尽可能通用。

尽管现如今发布的大多数模型都已经经过多次微调步骤，如 SFT 或甚至 PA，但由于这些模型是通用型的，因此它们显然并未针对您的目标用户或目标领域进行定制。这意味着，即使我们使用一个已调优的预对齐模型（例如指令调优模型），为了在您的领域中优化模型性能，仍然需要进一步的对齐步骤。

在这篇博客中，我们假设模型应该优化以最大化在科学领域中进行单轮和多轮用户面对面的问答对话时的有用性。因此，我们将从一个通用的指令/Q&A 预训练 FM 开始。

# 模型

尽管这一选择较为通用，但我们仍需要为我们的任务选择一个模型。在这篇博客中，我们将使用 Meta 的 Llama3.1–8b-instruct 模型。这个模型是 Meta 在 2024 年夏季发布的一系列多语言预训练和指令调优解码器模型中最小的一个版本。更多细节可以在 Meta 官方网站的[文档](https://llama.meta.com/docs/model-cards-and-prompt-formats/llama3_1#llama-3.1-instruct)以及 HuggingFace 提供的[模型卡](https://huggingface.co/meta-llama/Meta-Llama-3.1-8B-Instruct)中找到。

![](../Images/92f35b3b8bd00507db12d2a4a26d3815.png)

图 5：HuggingFace Hub 上的 Llama-3.1–8b-instruct 模型卡

# 必备条件

我们将在笔记本的演示中开始一些必要的准备步骤。

![](../Images/448c07c74376c62d180935b6d48a716b.png)

图 6：通过 HuggingFace Hub 接受 Meta 的许可协议

我们将从 HuggingFace 模型 Hub 获取模型的权重。为了能够执行此操作，我们需要接受 Meta 的许可协议并提供一些信息。此操作可以通过 HuggingFace 模型 Hub 直接提交。

此外，为了存储奖励模型和偏好对齐模型的适配器权重，我们将使用 HuggingFace 模型 Hub 上的私人模型仓库。这需要一个 HuggingFace 账户。一旦登录到 HuggingFace 平台，我们需要创建两个模型仓库。为此，请点击 HuggingFace 登录页右上角的账户图标，并在菜单中选择“+ 新建模型”。

![](../Images/913dabd67e327e505f481f44bc411501.png)

图 7：在 HuggingFace 模型 Hub 上创建模型仓库

然后，我们可以创建两个私有模型库。您可以遵循我的命名约定，也可以选择自己喜欢的名字。如果您使用不同的名字，请确保在笔记本中的代码也进行相应调整。

创建后，我们可以在 HuggingFace 个人资料中看到模型库。

要在 HuggingFace 模型中心进行身份验证以拉取或推送模型，我们需要创建一个访问令牌，稍后将在笔记本中使用。为此，请点击 HuggingFace 登录页面右上角的帐户图标，然后在菜单中选择“设置”。

在设置中，我们选择菜单项“访问令牌”，然后点击“+ 创建新令牌”。

![](../Images/6444c90a1d12546dd6a9699087935638.png)

图 8：在 HuggingFace hub 上创建访问令牌

根据最小权限原则，我们希望创建一个具有细粒度权限配置的令牌。对于我们的目的，读写访问权限就足够了——因此我们在此部分勾选了所有三个选项。然后我们向下滚动并创建令牌。

创建后，访问令牌会以明文形式显示。由于令牌只会显示一次，因此最好将其以加密格式存储，例如使用密码管理器。

# 数据集

现在我们完成了先决条件，可以继续使用我们在这个任务中将要使用的数据集。

![](../Images/91ccb2b30be64e7b3eb90d0bb193d5cf.png)

图 9：HuggingFace hub 上的 Anthropic hh-rlhf 数据集

在训练我们的奖励模型时，我们将使用 Anthropic/hh-rlhf 数据集，该数据集根据 [MIT 许可协议](https://opensource.org/license/mit) 分发。这是一个由 Anthropic 开源的手工偏好数据集。它包含对同一提示输入的模型完成的选择和拒绝。除此之外，数据集还以不同的形式出现，针对无害性、有用性等对齐领域。为了演示，我们将使用“有用”子集，将我们的 Llama 模型对齐到更有用的答案上。

对于实际的 PA 步骤，我们需要一个额外的数据集，代表我们模型的目标领域，来配合 PPO 和之前训练的奖励模型。由于我们正在对指令模型进行微调以提高有用性，因此需要一组指令风格的提示。[斯坦福问答数据集（SQuAD）](https://rajpurkar.github.io/SQuAD-explorer/)，根据 [CC BY-SA 4.0 许可协议](https://creativecommons.org/licenses/by-sa/4.0/deed.en)**，** 提供了涵盖各个领域的问答对。对于我们的实验，我们将专注于单轮开放式问答。因此，我们只会使用数据集中的“问题”特性。

# 代码库

![](../Images/8558c4acfd289147c8bac0697f5e8d7e.png)

图 10：代码库

在查看了我们将使用的数据集后，接下来让我们看看目录结构以及在本演示中将使用的文件。该目录包含3个文件：config.yaml，一个用于通过远程装饰器运行 SageMaker 作业的配置文件；requirements.txt，用于扩展训练容器中已安装的依赖项；最后是包含我们 E2E PA 代码的 `rlhf-multi-adapter-ppo.ipynb` 笔记本。

前面提到的 config.yaml 文件包含了通过远程装饰器触发的训练作业的重要配置，例如训练实例类型或训练镜像。

# 笔记本

现在，让我们打开 `rlhf-multi-adapter-ppo.ipynb` 笔记本。首先，我们安装并导入所需的依赖项。

## 数据预处理奖励模型训练数据集

如前所述，我们将使用“Anthropic/hh-rlhf”数据集来训练我们的奖励模型。因此，我们需要将原始数据集转换为上述指定的结构，其中“input_ids”和“attention_mask”是输入标记化的输出。此格式被 HuggingFace trl RewardTrainer 类指定为接口定义，并使得在奖励模型训练过程中，接受的和拒绝的答案易于访问。

```py
DatasetDict({
    train: Dataset({
        features: ['input_ids_chosen', 'attention_mask_chosen', 'input_ids_rejected', 'attention_mask_rejected'],
        num_rows: ...
    })
    test: Dataset({
        features: ['input_ids_chosen', 'attention_mask_chosen', 'input_ids_rejected', 'attention_mask_rejected'],
        num_rows: ...
    })
})
```

我们登录 HuggingFace hub。然后，我们获取“Anthropic/hh-rlhf”数据集中的“helpful-base”。原始数据集结构如下所示，我们还查看了一个示例数据集项。

接下来，我们将对话解析为按对话轮次和角色分隔的数组。

```py
def extract_dialogue(input_text):
    # Split the input by lines and initialize variables
    lines = input_text.strip().split("\n\n")
    dialogue_list = []

    # Iterate through each line and extract the dialogue
    for line in lines:
        # Check if the line starts with "Human" or "Assistant" and split accordingly
        if line.startswith("Human:"):
            role = "user"
            content = line.replace("Human: ", "").strip()
        elif line.startswith("Assistant:"):
            role = "assistant"
            content = line.replace("Assistant: ", "").strip()
        else:
            # If the line doesn't start with "Human" or "Assistant", it's part of the previous message's content
            # Append it to the last message's content
            dialogue_list[-1]["content"] += "\n\n" + line.strip()
            continue

        # Append the extracted dialogue piece to the list
        dialogue_list.append({"role": role, "content": content})

    return dialogue_list

def process(row):
        row["chosen"] = extract_dialogue(row["chosen"])
        row["rejected"] = extract_dialogue(row["rejected"])
        row["prompt"] = row["chosen"][0]["content"]
        return row

ds_processed = ds.map(
        process,
        load_from_cache_file=False,
    )
```

基于其预训练过程，每个模型都有一套特定的语法和特殊标记，提示应针对这些语法和标记进行优化——这就是提示工程的核心，在微调时需要考虑。对于 Meta Llama 模型，相关信息可以在 llama-recipes [GitHub 仓库](https://github.com/meta-llama/llama-recipes)中找到。为了遵循这些提示指南并获得理想结果，我们将相应地编码我们的数据集。

```py
# Adjusting to llama prompt template format: https://github.com/meta-llama/llama-recipes
system_prompt = "Please answer the user's question to the best of your knowledge. If you don't know the answer respond that you don't know."

def encode_dialogue_turn(message):
    return f'<|start_header_id|>{message.get("role")}<|end_header_id|>{message.get("content")}<|eot_id|>'

def encode_dialogue(dialogue):
    if system_prompt:
        return f'<|begin_of_text|><|start_header_id|>system<|end_header_id|>{system_prompt}<|eot_id|>{functools.reduce(lambda a, b: a + encode_dialogue_turn(b), dialogue, "")}'
    else:
        return f'<|begin_of_text|>{functools.reduce(lambda a, b: a + encode_dialogue_turn(b), dialogue, "")}'

def encode_row(item):
    return {"chosen": encode_dialogue(item["chosen"]), "rejected": encode_dialogue(item["rejected"]), "prompt": item["prompt"]}

def encode_dataset(dataset):
    return list(map(encode_row, dataset))

encoded_dataset = ds_processed.map(encode_row)
```

然后，我们对“chosen”（选择的）和“rejected”（拒绝的）列进行分词。接着我们移除文本列，因为这些列不再需要。现在数据集已经转换为我们所期望的格式。

```py
# Tokenize and stack into target format
def preprocess_function(examples):
    new_examples = {
        "input_ids_chosen": [],
        "attention_mask_chosen": [],
        "input_ids_rejected": [],
        "attention_mask_rejected": [],
    }
    for chosen, rejected in zip(examples["chosen"], examples["rejected"]):
        tokenized_chosen = tokenizer(chosen)
        tokenized_rejected = tokenizer(rejected)

        new_examples["input_ids_chosen"].append(tokenized_chosen["input_ids"])
        new_examples["attention_mask_chosen"].append(tokenized_chosen["attention_mask"])
        new_examples["input_ids_rejected"].append(tokenized_rejected["input_ids"])
        new_examples["attention_mask_rejected"].append(tokenized_rejected["attention_mask"])

    return new_examples

tokenized_dataset_hhrlhf = encoded_dataset.map(
        preprocess_function,
        batched=True,
    ).remove_columns(["chosen", "rejected", "prompt"])
```

最后，我们将数据集上传到 Amazon S3。请调整存储桶路径，指向您账户中的存储桶路径。

## 数据预处理 PPO 数据集

如前所述，我们将使用斯坦福问题与回答数据集（SQuAD）进行实际的 PA 步骤与 PPO 结合。因此，我们需要将原始数据集转换为预定义的结构，其中“input_ids”是“query”（查询）问题的向量化格式，且该格式为问题的填充版本。

```py
DatasetDict({
    train: Dataset({
        features: ['input_ids', 'query'],
        num_rows: ...
    })
    test: Dataset({
        features: ['input_ids', 'query'],
        num_rows: ...
    })
})
```

这一次，我们不从 HuggingFace hub 拉取数据集，而是从 GitHub 仓库克隆数据集。

接下来，我们将对话解析为按对话轮次和角色分隔的数组。然后，我们根据 Meta Llama 提示指南对数据集进行编码，以获得理想的结果。

```py
def extract_questions(dataset):
    ret_questions = []
    for topic in dataset:
        paragraphs = topic['paragraphs']
        for paragraph in paragraphs:
            qas = paragraph['qas']
            for qa in qas:
                ret_questions.append([{
            "role": "system", "content": f'Instruction: Please answer the user\'s question to the best of your knowledge. If you don\'t know the answer respond that you don\'t know.',
        }, {
            "role": "user", "content": qa['question'],
        }])
    return ret_questions

# Adjusting to llama prompt template format: https://github.com/meta-llama/llama-recipes
def encode_dialogue_turn(message):
    message = message
    return f'<|start_header_id|>{message.get("role")}<|end_header_id|>{message.get("content")}<|eot_id|>'

def encode_dialogue(dialogue):
    return {'input': f'<|begin_of_text|>{functools.reduce(lambda a, b: a + encode_dialogue_turn(b), dialogue, "")}'}

def encode_dataset(dataset):
    #print(dataset)
    return list(map(encode_dialogue, dataset))

encoded_train = encode_dataset(extract_questions(d_train['data']))
encoded_test = encode_dataset(extract_questions(d_test['data']))
```

我们将训练样本填充到最多2048个标记，以减少训练时的内存占用。这可以调整到模型的最大上下文窗口。阈值应在特定用例或领域所需的提示长度和保持小的训练内存占用之间找到一个良好的折中。请注意，较大的输入标记大小可能需要扩展计算基础设施。

```py
# Restrict training context size (due to memory limitations, can be adjusted)
input_min_text_length = 1
input_max_text_length = 2048

def create_and_prepare_dataset(tokenizer, dataset):

    input_size = LengthSampler(input_min_text_length, input_max_text_length)

    def tokenize(example):
        text_size = input_size()
        example["input_ids"] = tokenizer.encode(example["input"])[:text_size]
        example["query"] = tokenizer.decode(example["input_ids"])
        return example

    dataset = dataset.map(tokenize, batched=False)

    dataset.set_format("torch")
    return dataset

tokenized_dataset_squad = create_and_prepare_dataset(tokenizer, dataset_dict).remove_columns(["input"])
```

最后，我们将数据集上传到s3。请将存储桶路径调整为指向您帐户中的存储桶的路径。

## 奖励模型训练

在奖励模型的训练中，我们定义了两个辅助函数：一个计算模型可训练参数的函数，用于展示LoRA如何影响可训练参数；另一个函数用于识别模型中的所有线性模块，因为这些模块将被LoRA所作用。

```py
def print_trainable_parameters(model):
    """
    Prints the number of trainable parameters in the model.
    """
    trainable_params = 0
    all_param = 0
    for _, param in model.named_parameters():
        all_param += param.numel()
        if param.requires_grad:
            trainable_params += param.numel()
    print(
        f"trainable params: {trainable_params} || all params: {all_param} || trainable%: {100 * trainable_params / all_param}"
    )

def find_all_linear_names(hf_model):
    lora_module_names = set()
    for name, module in hf_model.named_modules():
        if isinstance(module, bnb.nn.Linear4bit):
            names = name.split(".")
            lora_module_names.add(names[0] if len(names) == 1 else names[-1])

    if "lm_head" in lora_module_names:  # needed for 16-bit
        lora_module_names.remove("lm_head")
    return list(lora_module_names)
```

训练函数“train_fn”被装饰器remote装饰。这使我们能够将其作为SageMaker训练任务执行。在装饰器中，我们定义了一些参数，并与config.yaml中指定的参数一起使用。这些参数可以在实际调用训练任务时被覆盖。

在训练函数中，我们首先设置一个种子以确保可重复性。然后，我们初始化一个Accelerator对象来处理分布式训练。该对象将在4个rank（请注意装饰器参数中的*nproc_per_node=4*）的ml.g5.12xlarge实例上以数据并行方式协调我们的分布式训练（请注意*config.yaml*中的*InstanceType: ml.g5.12xlarge*）。

然后，我们登录HuggingFace Hub并加载并配置分词器。

```py
# Start training with remote decorator (https://docs.aws.amazon.com/sagemaker/latest/dg/train-remote-decorator.html). Additional job config is being pulled in from config.yaml. 
@remote(keep_alive_period_in_seconds=0, volume_size=100, job_name_prefix=f"train-{model_id.split('/')[-1].replace('.', '-')}-reward", use_torchrun=True, nproc_per_node=4)
def train_fn(
        model_name,
        train_ds,
        test_ds=None,
        lora_r=8,
        lora_alpha=32,
        lora_dropout=0.1,
        per_device_train_batch_size=8,
        per_device_eval_batch_size=8,
        gradient_accumulation_steps=1,
        learning_rate=2e-4,
        num_train_epochs=1,
        fsdp="",
        fsdp_config=None,
        chunk_size=10000,
        gradient_checkpointing=False,
        merge_weights=False,
        seed=42,
        token=None,
        model_hub_repo_id=None,
        range_train=None,
        range_eval=None
):

    set_seed(seed)

    # Initialize Accelerator object handling distributed training
    accelerator = Accelerator()

    # Login to HuggingFace
    if token is not None:
        login(token=token)

    # Load tokenizer. Padding side is "left" because focus needs to be on completion
    tokenizer = AutoTokenizer.from_pretrained(model_name, padding_side = "left")

    # Set tokenizer's pad Token
    tokenizer.pad_token = tokenizer.eos_token 
    tokenizer.pad_token_id = tokenizer.eos_token_id 
```

在接下来的步骤中，我们从S3加载训练数据，并将其加载到HuggingFace的DatasetDict对象中。由于这是一个演示，我们希望能够仅使用数据的一个子集进行训练，以节省时间和资源。为此，我们可以配置要使用的数据集项的范围。

```py
 # Load data from S3
    s3 = s3fs.S3FileSystem()
    dataset = load_from_disk(train_ds)  

    # Allow for partial dataset training
    if range_train:
        train_dataset = dataset["train"].select(range(range_train))
    else: 
        train_dataset = dataset["train"]

    if range_eval:
        eval_dataset = dataset["test"].select(range(range_eval))
    else:
        eval_dataset = dataset["test"]
```

我们使用HuggingFace的bitsandbytes库进行量化。在此配置中，bitsandbytes将用NF4层替换模型的所有线性层，并将计算及存储的数据类型设置为bfloat16。然后，模型使用这种量化配置从HuggingFace Hub加载，使用Flash Attention 2实现注意力机制，以进一步提高内存使用效率和计算效率。我们还会打印出此状态下模型的所有可训练参数。接着，模型为量化训练做好准备。

```py
 # Specify quantization config
    bnb_config = BitsAndBytesConfig(
        load_in_4bit=True,
        bnb_4bit_use_double_quant=True,
        bnb_4bit_quant_type="nf4",
        bnb_4bit_compute_dtype=torch.bfloat16,
        quant_storage_dtype=torch.bfloat16
    )

    # Load model with classification head for reward
    model = AutoModelForSequenceClassification.from_pretrained(
        model_name,
        #num_labels=1,
        trust_remote_code=True,
        quantization_config=bnb_config,
        attn_implementation="flash_attention_2",
        use_cache=False if gradient_checkpointing else True,
        cache_dir="/tmp/.cache"
    )

    # Pre-LoRA trainable paremeters
    print_trainable_parameters(model)     

    # Set model pad token id
    model.config.pad_token_id = tokenizer.pad_token_id

    # Prepare model for quantized training
    model = prepare_model_for_kbit_training(model, use_gradient_checkpointing=gradient_checkpointing)
```

接下来，我们发现模型中的所有线性层，并将它们传递到一个LoraConfig中，该配置指定了一些LoRA超参数。请注意，与传统的大型语言模型（LLM）训练不同，task_type不是“CAUSAL_LM”，而是“SEQ_CLS”，因为我们训练的是奖励模型，而不是文本生成模型。该配置应用于模型，训练参数再次被打印出来。请注意可训练参数和总参数的区别。

```py
 # Get lora target modules
    modules = find_all_linear_names(model)
    print(f"Found {len(modules)} modules to quantize: {modules}")

    # Specify LoRA config
    config = LoraConfig(
        r=lora_r,
        lora_alpha=lora_alpha,
        target_modules=modules,
        lora_dropout=lora_dropout,
        bias="none",
        task_type="SEQ_CLS"
    )

    # Make sure to not train for CLM
    if config.task_type != "SEQ_CLS":
        warnings.warn(
            "You are using a `task_type` that is different than `SEQ_CLS` for PEFT. This will lead to silent bugs"
            " Make sure to pass --lora_task_type SEQ_CLS when using this script."
        )

    # Create PeftModel
    model = get_peft_model(model, config)

    # Post-LoRA trainable paremeters
    print_trainable_parameters(model) 
```

我们定义了 RewardConfig，其中包含了重要的训练超参数，如训练批量大小、训练周期、学习率等。我们还定义了一个*max_length=512*。这将是用于奖励适配器训练的 prompt+response 对的最大长度，并将通过左侧填充来强制执行，以保持最后一次对话回合，从而标记所选样本与被拒绝样本之间的关键差异。同样，这个值可以调整到模型的最大上下文窗口，同时在遵循特定用例或领域所需的 prompt 长度与保持训练内存占用较小之间找到良好的折衷。

此外，我们初始化了 RewardTraining 对象，通过此配置管理训练，并进一步输入训练数据，如模型、分词器和数据集。然后我们启动训练。一旦训练完成，我们将奖励模型适配器的权重推送到我们一开始创建的奖励模型存储库。

```py
 # Specify training config
    reward_config = RewardConfig(
                        per_device_train_batch_size=per_device_train_batch_size,
                        per_device_eval_batch_size=per_device_eval_batch_size,
                        gradient_accumulation_steps=gradient_accumulation_steps,
                        gradient_checkpointing=gradient_checkpointing,
                        logging_strategy="steps",
                        logging_steps=100,
                        log_on_each_node=False,
                        num_train_epochs=num_train_epochs,
                        learning_rate=learning_rate,
                        bf16=True,
                        ddp_find_unused_parameters=False,
                        fsdp=fsdp,
                        fsdp_config=fsdp_config,
                        save_strategy="no",
                        output_dir="outputs",
                        max_length=512, 
                        remove_unused_columns=False,
                        gradient_checkpointing_kwargs = {"use_reentrant": False}
                        )

    # Initialize RewardTrainer object handling training
    trainer = RewardTrainer(
        model=model,
        tokenizer=tokenizer,
        args=reward_config,
        train_dataset=train_dataset,
        eval_dataset=eval_dataset,
    )

    trainer.train()

    trainer.model.save_pretrained("/opt/ml/model", safe_serialization=True)

    if model_hub_repo_id is not None:
        trainer.model.push_to_hub(repo_id=model_hub_repo_id)

    with accelerator.main_process_first():
        tokenizer.save_pretrained("/opt/ml/model")
```

现在，我们可以开始训练本身了。因此，我们调用训练函数，启动一个临时训练作业在 Amazon SageMaker 上运行。为此，我们需要将一些参数传递给训练函数，例如模型 ID、训练数据集路径和一些超参数。请注意，此演示中使用的超参数可以根据需要进行调整。在这个演示中，我们使用 100 个训练样本和 10 个评估样本，以减少资源和时间占用。对于实际的应用场景，应该考虑使用完整数据集进行训练。训练开始后，训练日志会被流式传输到笔记本中。

```py
# Start training job
train_fn(
    model_id,
    train_ds=dataset_path_hhrlhf,
    per_device_train_batch_size=8,
    per_device_eval_batch_size=8,
    gradient_accumulation_steps=2,
    gradient_checkpointing=True,
    num_train_epochs=1,
    token=hf_token,
    model_hub_repo_id=model_hub_repo_id,
    range_train=100,
    range_eval=10
)
```

## 多适配器 PPO

对于实际的 PA 步骤与 PPO，我们复用了计算模型可训练参数的函数，以展示 LoRA 如何影响可训练参数。与奖励模型训练步骤类似，训练函数“train_fn”被远程装饰器装饰，使我们能够将其作为 SageMaker 训练作业执行。

在训练函数中，我们首先设置了一个种子以确保结果的可重复性。然后，我们初始化了一个 Accelerator 对象，用于处理分布式训练。与奖励适配器训练类似，这个对象将通过数据并行方式在 ml.g5.12xlarge 实例上的 4 个 rank 进行分布式训练。

接下来，我们登录 HuggingFace hub，并加载和配置分词器。在下一步中，我们从 S3 加载训练数据，并将其加载到一个 HuggingFace 的 DatasetDict 对象中。由于这是一个演示，我们希望只用数据的一个子集进行训练，以节省时间和资源。为此，我们可以配置数据集项的使用范围。

```py
# Start training with remote decorator (https://docs.aws.amazon.com/sagemaker/latest/dg/train-remote-decorator.html). Additional job config is being pulled in from config.yaml. 
@remote(keep_alive_period_in_seconds=0, volume_size=100, job_name_prefix=f"train-{model_id.split('/')[-1].replace('.', '-')}-multi-adapter-ppo", use_torchrun=True, nproc_per_node=4)
def train_fn(
        model_name,
        train_ds,
        rm_adapter,
        log_with=None,
        use_safetensors=None,
        use_score_scaling=False,
        use_score_norm=False,
        score_clip=None,
        seed=42,
        token=None,
        model_hub_repo_id=None,
        per_device_train_batch_size=8,
        per_device_eval_batch_size=8,
        gradient_accumulation_steps=2,
        gradient_checkpointing=True,
        num_train_epochs=1,
        merge_weights=True,
        range_train=None,
        ):

    set_seed(seed)

    # Initialize Accelerator object handling distributed training
    accelerator = Accelerator()

    # Login to HuggingFace 
    if token is not None:
        login(token=token)

    # Load tokenizer. Padding side is "left" because focus needs to be on completion
    tokenizer = AutoTokenizer.from_pretrained(model_name, padding_side = "left")

    # Set tokenizer's pad Token
    tokenizer.pad_token = tokenizer.eos_token 
    tokenizer.pad_token_id = tokenizer.eos_token_id  

    # Load data from S3
    s3 = s3fs.S3FileSystem()
    dataset = load_from_disk(train_ds)  

    # Allow for partial dataset training
    if range_train:
        train_dataset = dataset["train"].select(range(range_train))
    else: 
        train_dataset = dataset["train"]
```

接下来，我们定义了一个 LoraConfig，指定 LoRA 的超参数。请注意，这次的 task_type 是“CAUSAL_LM”，因为我们旨在微调一个文本生成模型。

```py
 # Specify LoRA config
    lora_config = LoraConfig(
        r=16,
        lora_alpha=32,
        lora_dropout=0.05,
        bias="none",
        task_type="CAUSAL_LM",
    )
```

我们使用 HuggingFace 的 bitsandbytes 库进行量化。在这种配置中，bitsandbytes 会将模型的所有线性层替换为 NF4 层，并将计算改为 bfloat16。

```py
 # Specify quantization config
    bnb_config = BitsAndBytesConfig(
        load_in_4bit=True, bnb_4bit_quant_type="nf4", bnb_4bit_use_double_quant=True, bnb_4bit_compute_dtype=torch.bfloat16
    )
```

然后，模型将在 HuggingFace Hub 上加载，并使用指定的 LoraConfig 和 BitsAndBytesConfig 进行量化。请注意，这个模型并没有包装成一个简单的 AutoModelForCausalLM 类，而是使用了一个 AutoModelForCausalLMWithValueHead 类，它将我们的奖励模型适配器作为输入。这个模型类是专门为多适配器 PPO 构建的，在实际的训练循环中协调适配器加载和插件操作，稍后我们会讨论。为了完整性，我们还会打印出该状态下模型的所有可训练参数。

```py
 # Load model
    model = AutoModelForCausalLMWithValueHead.from_pretrained(
        model_name,
        #device_map='auto',
        peft_config=lora_config,
        quantization_config=bnb_config,
        reward_adapter=rm_adapter,
        use_safetensors=use_safetensors,
        #attn_implementation="flash_attention_2",
    )

    # Set model pad token id
    model.config.pad_token_id = tokenizer.pad_token_id

    if gradient_checkpointing:
        model.gradient_checkpointing_enable()

    # Trainable paremeters
    print_trainable_parameters(model) 
```

我们定义了包含重要训练超参数的 PPOConfig，如训练批次大小、学习率等。此外，我们初始化了 PPOTrainer 对象，它会使用该配置协调训练，并接受其他训练输入，如模型、分词器和数据集。需要注意的是，计算 KL 散度的 ref_model 并未指定。如前所述，在此配置中，PPOTrainer 使用与要优化的模型具有相同架构且共享层的参考模型。此外，定义了推理参数，以便根据查询从训练数据集中检索文本补全。

```py
 # Specify PPO training config
    config = PPOConfig(
        model_name,
        log_with=None,
        learning_rate=1e-5,
        batch_size=per_device_train_batch_size,
        mini_batch_size=1,
        gradient_accumulation_steps=gradient_accumulation_steps,
        optimize_cuda_cache=True,
        seed=42,
        use_score_scaling=False,
        use_score_norm=False,
        score_clip=None,
    )

    # Initialize PPOTrainer object handling training
    ppo_trainer = PPOTrainer(
        config,
        model,
        ref_model=None,
        tokenizer=tokenizer,
        dataset=train_dataset,
        data_collator=collator,
    )

    # Specifying inference params
    generation_kwargs = {
        "top_k": 0.0,
        "top_p": 0.9,
        "do_sample": True,
        "pad_token_id": tokenizer.pad_token_id,
        "max_new_tokens": 32,
    }
```

然后，我们在一批训练数据上执行实际的多适配器 PPO 训练循环：首先，应用我们正在进行 RLHF 微调的 LoRA 适配器进行推理，从训练数据集中基于查询检索文本补全。响应被解码为纯文本并与查询合并。接着，应用奖励适配器计算查询-补全对在标记化形式下的奖励。随后，奖励值与问题和响应张量一起用于优化步骤。需要注意的是，在后台计算了经过微调的模型与基础模型之间的 Kullback–Leibler 散度（KL 散度）（预测偏移惩罚），并将其作为额外的奖励信号集成项，在优化步骤中使用。由于这是基于相同的输入提示，KL 散度作为度量标准，用于评估这两个概率分布以及因此模型本身在训练过程中如何相互偏离。这个散度会从奖励项中扣除，惩罚与基础模型的偏离，以确保算法稳定性和语言一致性。最后，我们正在进行 RLHF 微调的适配器再次应用于反向传播。

然后我们启动训练。一旦训练完成，我们会将对齐的模型适配器权重推送到我们最初创建的 rlhf 模型仓库中。

```py
step = 0

    for _epoch, batch in tqdm(enumerate(ppo_trainer.dataloader)):

        question_tensors = batch["input_ids"]

        # Inference through model being fine-tuned
        response_tensors = ppo_trainer.generate(
            question_tensors,
            return_prompt=False,
            **generation_kwargs,
        )

        # Decode response
        batch["response"] = tokenizer.batch_decode(response_tensors, skip_special_tokens=True)

        # Concat query and response
        texts = [q + r for q, r in zip(batch["query"], batch["response"])]

        # Tokenize query - response pair
        inputs = tokenizer(texts, padding=True, truncation=True, return_tensors="pt").to(ppo_trainer.accelerator.device)

        # Compute reward score
        raw_rewards = ppo_trainer.accelerator.unwrap_model(ppo_trainer.model).compute_reward_score(**inputs)
        rewards = [raw_rewards[i, -1, 1] for i in range(len(raw_rewards))]  # take last token

        # Run PPO step
        stats = ppo_trainer.step(question_tensors, response_tensors, rewards)
        ppo_trainer.log_stats(stats, batch, rewards)

        step = step + 1      

    if accelerator.is_main_process:

        ppo_trainer.save_pretrained("/opt/ml/model", safe_serialization=True)

        if model_hub_repo_id is not None:
            ppo_trainer.push_to_hub(repo_id=model_hub_repo_id)
            tokenizer.push_to_hub(repo_id=model_hub_repo_id)

    with accelerator.main_process_first():
        tokenizer.save_pretrained("/opt/ml/model")
```

我们现在可以开始训练本身。因此，我们调用训练函数，在 Amazon SageMaker 中启动一个短暂的训练作业。为此，我们需要传递一些参数给训练函数，例如模型 ID、训练数据集路径、奖励模型路径以及一些超参数。请注意，演示中使用的超参数可以根据需要进行调整。为了保持资源和时间消耗较低，我们使用了 100 个训练样本。对于实际使用案例，应考虑使用完整数据集进行训练。一旦训练开始，训练日志将流式传输到笔记本中。

```py
train_fn(
    model_id,
    train_ds=dataset_path_squad,
    rm_adapter=rm_adapter,
    per_device_train_batch_size=4,
    per_device_eval_batch_size=4,
    gradient_accumulation_steps=4,
    gradient_checkpointing=True,
    num_train_epochs=1,
    token=hf_token,
    model_hub_repo_id=model_hub_repo_id,
    range_train=100
)
```

## 部署

最后，我们希望测试已调优的模型。因此，我们将其部署到 SageMaker 端点。我们首先导入所需的依赖项，并设置 SageMaker 会话和 IAM。

对于部署，我们使用了 [SageMaker — Huggingface 集成](https://aws.amazon.com/blogs/machine-learning/announcing-the-launch-of-new-hugging-face-llm-inference-containers-on-amazon-sagemaker/) 和 [TGI 容器](https://huggingface.co/docs/text-generation-inference/en/index)。我们定义了实例类型、镜像以及与模型相关的参数，如基础模型、LoRA 适配器、量化等。

```py
# sagemaker config
instance_type = "ml.g5.4xlarge"
number_of_gpu = 1
health_check_timeout = 300

# TGI config
config = {
'HF_MODEL_ID': "meta-llama/Meta-Llama-3.1-8B-Instruct",
'LORA_ADAPTERS': "**HF_REPO_ID**",
'SM_NUM_GPUS': json.dumps(1), # Number of GPU used per replica
'MAX_INPUT_LENGTH': json.dumps(1024),  # Max length of input text
'MAX_TOTAL_TOKENS': json.dumps(2048),  # Max length of the generation (including input text),
'QUANTIZE': "bitsandbytes", # comment in to quantize
'HUGGING_FACE_HUB_TOKEN': hf_token
}

image_uri = get_huggingface_llm_image_uri(
    "huggingface",
    version="2.0"
)

# create HuggingFaceModel
llm_model = HuggingFaceModel(
    role=role,
    image_uri=image_uri,
    env=config
)
```

然后我们部署模型。模型部署后，我们可以使用我们选择的提示进行模型推理测试。请注意，我们使用了在数据预处理过程中定义的 encode_dialogue 函数，以优化 Llama 模型的提示。

```py
# Deploy model to an endpoint
# https://sagemaker.readthedocs.io/en/stable/api/inference/model.html#sagemaker.model.Model.deploy
llm = llm_model.deploy(
    endpoint_name=f'llama-31-8b-instruct-rlhf-{datetime.now().strftime("%Y%m%d%H%M%S")}', # alternatively "llama-2-13b-hf-nyc-finetuned"
    initial_instance_count=1,
    instance_type=instance_type,
    container_startup_health_check_timeout=health_check_timeout, # 10 minutes to be able to load the model
)

parameters = {
        "top_p": 0.8,
        "temperature": 0.1,
        "return_full_text": True,
        "stop": [],
    }

encoded_message = encode_dialogue([{'content': 'Who won the FIFA World cup 2014 in Brazil?', 'role': 'user'}])

response = llm.predict({"inputs": encoded_message['input'], **parameters})
```

## 清理

最后，我们清理已部署的端点和模型实体，以便在资源使用上负责任。

```py
# Delete model and endpoint
llm.delete_model()
llm.delete_endpoint()
```

## 成本

奖励模型适配器训练和多适配器 PPO 训练都在一个 *ml.g5.12xlarge* 实例上执行，使用了从各自训练数据集中随机抽取的 100 行数据。每个步骤的平均训练时间大约为 400 秒。截至 2024 年 11 月，这种实例类型在 us-east-1 区域的价格为 **$7.09/小时**。

因此，采用多适配器 PPO 的 RLHF 实现的端到端训练成本不到 *($7.09 * 400s)/(3600s * 100)* **~ 每个训练样本 $0.0079**，每个训练步骤均适用。这意味着 **奖励模型训练的每千个训练令牌的成本不到 $0.015**，并且 **多适配器 PPO 步骤的每千个训练令牌的成本不到 $0.0039**。

对于推理，模型托管在一个 *ml.g5.4xlarge* 实例上。截至 2024 年 11 月，这种实例类型在 us-east-1 区域的价格为 **$2.03/小时**。

# 结论

在这篇博客中，我们探讨了多适配器 PPO 与 RLHF 的结合，这是一种节俭的、大型语言模型偏好对齐方法。我们涵盖了以下关键点：

1.  在提升 LLM 性能中，偏好对齐的重要性及其在人工智能民主化中的作用。

1.  RLHF 的原理及其涉及奖励模型训练和基于 PPO 的微调的两步过程。

1.  实现RLHF过程中面临的挑战，包括计算资源和协调复杂性。

1.  多适配器PPO方法作为减少基础设施和协调开销的解决方案。

1.  使用HuggingFace框架和Amazon SageMaker的详细端到端实现，包括数据预处理、奖励模型训练、多适配器PPO训练和模型部署。

这种节俭的强化学习与人类反馈（RLHF）方法使得偏好对齐变得更加易于被更广泛的从业者所接受，可能加速对齐AI系统的开发和部署。

通过减少计算需求并简化实现过程，多适配器PPO为根据特定领域或用户偏好微调语言模型开辟了新的可能性。

随着AI领域的不断发展，像这样的技术将在创建更高效、更有效且更对齐的语言模型方面发挥至关重要的作用。我鼓励读者尝试这种方法，将其适应到具体的使用场景中，并分享他们在构建负责任且以用户为中心的大型语言模型（LLMs）方面的成功经验。

***如果你有兴趣深入了解LLM的预训练和对齐，我推荐查看我和我的尊敬的同事们最近发布的*** [***AWS SkillBuilder课程***](https://explore.skillbuilder.aws/learn/course/external/view/elearning/17556/building-language-models-on-aws) ***，包括*** [***Anastasia***](https://anastasia-tzeveleka.medium.com/) ***和*** [***Gili***](https://medium.com/@gilinachum)***。***
