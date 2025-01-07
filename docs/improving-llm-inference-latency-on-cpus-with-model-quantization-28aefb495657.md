# 使用模型量化技术提升CPU上LLM推理速度

> 原文：[https://towardsdatascience.com/improving-llm-inference-latency-on-cpus-with-model-quantization-28aefb495657?source=collection_archive---------1-----------------------#2024-02-29](https://towardsdatascience.com/improving-llm-inference-latency-on-cpus-with-model-quantization-28aefb495657?source=collection_archive---------1-----------------------#2024-02-29)

![](../Images/ba70dd27842dfef63454e83252c90ac1.png)

图片版权所有 — 由Nightcafe创作

## 探索如何使用量化技术（如bf16、int8和int4精度）显著提升CPU上的推理延迟

[](https://eduand-alvarez.medium.com/?source=post_page---byline--28aefb495657--------------------------------)[![Eduardo Alvarez](../Images/8a51c754fdd3362aa82dee5acd2a68c5.png)](https://eduand-alvarez.medium.com/?source=post_page---byline--28aefb495657--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--28aefb495657--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--28aefb495657--------------------------------) [Eduardo Alvarez](https://eduand-alvarez.medium.com/?source=post_page---byline--28aefb495657--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28aefb495657--------------------------------) ·9分钟阅读·2024年2月29日

--

AI领域面临的最大挑战之一是需要计算资源来托管大规模的生产级基于LLM的应用程序。在大规模应用中，LLM应用需要冗余、可扩展性和可靠性，这些通常只能在像CPU这样的通用计算平台上实现。然而，目前的主流观点认为，CPU无法在延迟上与高端GPU相媲美，无法处理LLM推理。

生态系统中一个可以帮助解决CPU推理延迟挑战的开源工具是[Intel® Extension for PyTorch*](https://www.intel.com/content/www/us/en/developer/tools/oneapi/optimization-for-pytorch.html)（IPEX），它为Intel硬件提供了最新的特性优化，能进一步提升性能。IPEX提供了多种易于实现的优化，充分利用了硬件级指令。本文将深入探讨模型压缩的理论以及IPEX提供的现成模型压缩技术。这些压缩技术直接影响基于Intel第4代和第5代CPU等通用计算平台的LLM推理性能。

# 应用开发中的推理延迟

仅次于应用安全性和保障性，推理延迟是AI应用生产中的最关键参数之一。对于基于LLM的应用，延迟或吞吐量通常以token/秒为单位进行衡量。如下面简化的推理处理顺序所示，token由语言模型处理，然后被去token化为自然语言。

![](../Images/b2d4ae537a79c711aded3a1a056f8f26.png)

GIF 1\. 推理处理顺序 — 图片由作者提供

以这种方式解读推理有时可能会误导我们，因为我们在抽象化的AI应用组件分析时，忽略了传统生产软件范式。是的，AI应用有其独特之处，但归根结底，我们还是在谈论单位时间内的交易。如果我们开始从应用设计的角度将推理视为一种交易，就像任何其他交易一样，那么问题变得不那么复杂。例如，假设我们有一个聊天应用，其要求如下：

+   每小时平均**300个用户会话**

+   每个用户每次会话平均进行**5次交易**（LLM推理请求）

+   每次交易生成**100个token**的平均数量

+   每次会话的**10,000ms (10秒)**开销平均用于用户认证、护栏、网络延迟和前/后处理。

+   用户在与聊天机器人积极互动时，平均**需要30,000ms (30秒)**来响应。

+   平均总**会话时长目标为3**分钟或更短。

如下所示，通过一些简单的估算，我们可以得到LLM推理引擎所需的延迟大致计算值。

![](../Images/e81ec3c2fcdaa2f85ed293707e29dd9d.png)

图1\. 基于不同应用要求计算所需交易和token延迟的简单方程式。— 图片由作者提供

在生产环境中达到所需的延迟阈值是一项挑战，特别是当你需要在不增加额外计算基础设施成本的情况下实现这一目标时。本文余下部分将探讨一种通过模型压缩显著提高推理延迟的方法。

# 模型压缩

模型压缩是一个广泛的术语，因为它涉及多种技术，如模型量化、蒸馏、剪枝等。这些技术的核心目标是减少神经网络的计算复杂度。

![](../Images/a063d0f1575e7dbbb20b419bc28230c9.png)

GIF 2\. 推理处理顺序的说明 — 图片由作者提供

我们今天关注的方法是模型量化，它涉及减少权重的字节精度，有时还包括激活值的精度，从而减少矩阵运算的计算负担和移动较大、高精度值的内存负担。下图展示了将fp32权重量化为int8的过程。

![](../Images/82cd0e972f9a9a49ad1b4eff1cc721db.png)

图2\. 模型量化的可视化表示，从全精度（FP32）到四分之一精度（INT8），理论上将模型复杂度降低 4 倍。 — 图片由作者提供

值得注意的是，从 fp32（全精度）量化到 int8（四分之一精度）所带来的复杂度降低了 4 倍，但并不会导致推理延迟减少 4 倍，因为推理延迟不仅仅与模型本身的特性有关，还涉及更多的因素。

和许多事情一样，并没有一种通用的解决方案，本文将探索我最喜欢的三种使用 IPEX 进行模型量化的技术：

## **在 bf16 或 fp32 下进行推理**

该技术将神经网络中的权重量化为用户定义的精度。这项技术非常适合较小的模型，例如小于 1B 参数的 LLM。

![](../Images/b8ad85dbaf7f150a475f67a5031c129d.png)

图3\. 简单的 bf16/fp32 示意图，显示 FP32 权重为橙色，半精度量化的 bf16 权重为绿色。 — 图片由作者提供

实现方法相当简单：使用 hugging face transformers，可以将模型加载到内存中，并通过 IPEX 特定的优化函数 `ipex.llm.optimize(model, dtype=dtype)` 进行优化。通过设置 `dtype = torch.bfloat16,`，我们可以激活半精度推理功能，这比全精度（fp32）和原始模型提高了推理延迟。

```py
import sys
import os
import torch
import intel_extension_for_pytorch as ipex
from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

# PART 1: Model and tokenizer loading using transformers
tokenizer = AutoTokenizer.from_pretrained("Intel/neural-chat-7b-v3-3")
model = AutoModelForCausalLM.from_pretrained("Intel/neural-chat-7b-v3-3")

# PART 2: Use IPEX to optimize the model
#dtype = torch.float # use for full precision FP32
dtype = torch.bfloat16 # use for half precision inference
model = ipex.llm.optimize(model, dtype=dtype)

# PART 3: Create a hugging face inference pipeline and generate results
pipe = pipeline("text-generation", model=model, tokenizer=tokenizer)
st = time.time()
results = pipe("A fisherman at sea...",  max_length=250)
end = time.time()
generation_latency = end-st

print('generation latency: ', generation_latency)
print(results[0]['generated_text'])
```

我们将探索的三种压缩技术中，这种实现方式是最简单的（按代码行数计算），并且相对于未量化的基线，提供了最小的净改进。

## **SmoothQuant (int8)**

该技术解决了量化 LLM 的核心挑战，其中包括处理跨所有层和标记的激活通道中大幅度的离群值，这是传统量化技术难以有效处理的常见问题。这项技术对模型中的权重和激活值进行联合数学变换。该变换战略性地减少了激活值中离群值与非离群值之间的差异，尽管这会以增加权重离群值比例为代价。这个调整使得 Transformer 层“适合量化”，使得 int8 量化得以成功应用，而不会降低模型质量。

![](../Images/c723f913c23b0e8577171ad521f992c9.png)

图4\. 简单的 SmoothQuant 示意图，权重以圆形表示，激活值以三角形表示。该图展示了两个主要步骤：（1）应用缩放器进行平滑，和（2）量化为 int8 — 图片由作者提供

以下是一个简单的 SmoothQuant 实现 — 省略了创建 DataLoader 的代码，因为这是 PyTorch 的一个常见且文档完善的原则。SmoothQuant 是一种准确度感知的后训练量化方法，这意味着通过提供校准数据集和模型，你将能够提供一个基准并限制语言建模的退化。校准模型生成量化配置，然后将其与 SmoothQuant 映射一起传递给 `ipex.llm.optimize()`。执行后，应用 SmoothQuant，并可以使用 `.generate()` 方法测试模型。

```py
import torch
import intel_extension_for_pytorch as ipex
from intel_extension_for_pytorch.quantization import prepare
import transformers

# PART 1: Load model and tokenizer from Hugging Face + Load SmoothQuant config mapping
tokenizer = AutoTokenizer.from_pretrained("Intel/neural-chat-7b-v3-3")
model = AutoModelForCausalLM.from_pretrained("Intel/neural-chat-7b-v3-3")
qconfig = ipex.quantization.get_smooth_quant_qconfig_mapping()

# PART 2: Configure calibration
# prepare your calibration dataset samples
calib_dataset = DataLoader({Your dataloader parameters})
example_inputs = # provide a sample input from your calib_dataset
calibration_model = ipex.llm.optimize(
  model.eval(),
  quantization_config=qconfig,
)
prepared_model = prepare(
  calibration_model.eval(), qconfig, example_inputs=example_inputs
)
with torch.no_grad():
  for calib_samples in enumerate(calib_dataset):
    prepared_model(calib_samples)
prepared_model.save_qconf_summary(qconf_summary=qconfig_summary_file_path)

# PART 3: Model Quantization using SmoothQuant
model = ipex.llm.optimize(
  model.eval(),
  quantization_config=qconfig,
  qconfig_summary_file=qconfig_summary_file_path,
)

# generation inference loop
with torch.inference_mode():
    model.generate({your generate parameters})
```

SmoothQuant 是一种强大的模型压缩技术，并且显著提高了推理延迟，相较于全精度模型。尽管如此，它仍然需要一些前期工作来准备校准数据集和模型。

## **仅权重量化（int8 和 int4）**

与传统的同时对激活和权重进行 int8 量化相比，仅权重量化（WOQ）在性能和准确性之间提供了更好的平衡。值得注意的是，int4 WOQ 在计算之前需要去量化为 bf16/fp16（图 4），这会引入一定的计算开销。一个基本的 WOQ 技术，张量级的非对称最近舍入（RTN）量化，存在挑战，并且常常导致准确性下降（[来源](https://huggingface.co/blog/intel-starcoder-quantization)）。然而，文献（Zhewei Yao, 2022）建议，对模型权重进行分组量化有助于保持准确性。由于权重仅在计算时去量化，即便多出了这一步，仍然保持了显著的内存优势。

![](../Images/159691607f7555e9159295d849f92e4f.png)

图 5\. 简单的仅权重量化示意图，预量化的权重为橙色，量化后的权重为绿色。请注意，这显示的是初步量化到 int4/int8 和去量化到 fp16/bf16 以进行计算步骤。 — 图片来自作者

以下 WOQ 实现展示了使用此技术从 Hugging Face 量化模型所需的几行代码。与前面的实现一样，我们首先从 Hugging Face 加载模型和标记器。我们可以使用 `get_weight_only_quant_qconfig_mapping()` 方法来配置 WOQ 配方。然后将该配方与模型一起传递给 `ipex.llm.optimize()` 函数以进行优化和量化。量化后的模型可以通过 `.generate()` 方法用于推理。

```py
 # requirements
#intel-extension-for-pytorch==2.2
#transformers==4.35.2
#torch==2.2.0 

import torch
import intel_extension_for_pytorch as ipex
from transformers import AutoTokenizer, AutoModelForCausalLM

# PART 1: Model and tokenizer loading
tokenizer = AutoTokenizer.from_pretrained("Intel/neural-chat-7b-v3-3")
model = AutoModelForCausalLM.from_pretrained("Intel/neural-chat-7b-v3-3")

# PART 2: Preparation of quantization config
qconfig = ipex.quantization.get_weight_only_quant_qconfig_mapping(
  weight_dtype=torch.qint8, # or torch.quint4x2
  lowp_mode=ipex.quantization.WoqLowpMode.NONE, # or FP16, BF16, INT8
)
checkpoint = None # optionally load int4 or int8 checkpoint

# PART 3: Model optimization and quantization
model = ipex.llm.optimize(model, quantization_config=qconfig, low_precision_checkpoint=checkpoint)

inputs = tokenizer("I love learning to code...", return_tensors="pt").input_ids

# PART 4: Inference output generation
with torch.inference_mode():
    tokens = model.generate(
        inputs,
        max_new_tokens=64,
)

print(tokenizer.decode(tokens[0], skip_special_tokens=True))
```

如你所见，WOQ 提供了一种强大的方法，可以将模型压缩到原始大小的一小部分，同时对语言建模能力的影响有限。

# 结论与讨论

作为英特尔的工程师，我与英特尔的IPEX工程团队密切合作。这使我对其优势和开发路线图有了独特的洞察力，成为了IPEX的首选工具。然而，对于那些希望简化开发、无需管理额外依赖的开发者，PyTorch提供了[三种量化方法](https://pytorch.org/docs/stable/quantization.html)：急切模式、FX图模式（正在维护中）和PyTorch 2导出量化，提供了强大且不那么专业的替代方案。

无论选择哪种技术，模型压缩技术都会导致一定程度的语言建模性能损失，尽管在许多情况下低于1%。因此，在追求量化之前，评估应用的容错能力并建立基准是至关重要的，这包括全精度（FP32）和/或半精度（BF16/FP16）的模型性能。

在利用某些上下文学习的应用中，如检索增强生成（RAG），模型压缩可能是一个极好的选择。在这些情况下，关键任务知识在推理时被传输给模型，因此即使在容错性较低的应用中，风险也大大降低。

量化是解决LLM推理延迟问题的绝佳方法，无需升级或扩展计算基础设施。无论你的使用案例是什么，都值得探索，而IPEX提供了一个不错的起点，只需要几行代码。

**有几个激动人心的尝试：**

+   在英特尔开发者云的[免费Jupyter环境](https://console.idcservice.net/training)中测试本教程中的示例代码。

+   在加速器上运行的现有模型以完全精度测试，并在CPU上以int4/int8进行测试。

+   探索这三种技术，并确定哪种最适合你的使用案例。确保比较语言建模性能的损失，而不仅仅是延迟。

+   [将你的量化模型上传到Hugging Face模型中心](https://github.com/intel/ai-innovation-bridge/blob/master/workshops/ai-workloads-with-huggingface/6%20-%20Uploading%20and%20Sharing%20Models%20on%20Hugging%20Face%20Hub%20with%20Intel%20Optimizations.ipynb)！如果你上传了，请告诉我——我很想看看！

***感谢阅读！别忘了关注*** [***我的个人资料，获取更多类似的文章***](https://eduand-alvarez.medium.com/) ***！***
