# 揭示LLM的内部工作原理：奇异值视角

> 原文：[https://towardsdatascience.com/unveiling-the-inner-workings-of-llms-a-singular-value-perspective-74c0c831e819?source=collection_archive---------5-----------------------#2024-06-14](https://towardsdatascience.com/unveiling-the-inner-workings-of-llms-a-singular-value-perspective-74c0c831e819?source=collection_archive---------5-----------------------#2024-06-14)

## 对Llama3–8B投影矩阵的奇异值分析

[](https://louisowen6.medium.com/?source=post_page---byline--74c0c831e819--------------------------------)[![Louis Owen](../Images/88faba8be8c36bf7e62233e7b78fbaae.png)](https://louisowen6.medium.com/?source=post_page---byline--74c0c831e819--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--74c0c831e819--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--74c0c831e819--------------------------------) [Louis Owen](https://louisowen6.medium.com/?source=post_page---byline--74c0c831e819--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74c0c831e819--------------------------------) ·9分钟阅读·2024年6月14日

--

![](../Images/d185588d8f57d93c7b3f9cd3a737c399.png)

由[Afif Ramdhasuma](https://unsplash.com/@javaistan?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你是否曾经想过，一个大型语言模型（LLM）训练得有多好？考虑到巨大的参数量，这些参数是否最大限度地捕捉了训练数据中的信息或知识？如果没有，我们是否可以从LLM中移除那些无用的参数，使其更高效？

在本文中，我们将尝试通过从奇异值的角度对Llama-3–8B模型进行深入分析，来回答这些问题。现在，让我们舒适地坐好，准备好应用SVD分析Llama-3–8B矩阵的质量！

# SVD重新审视

在奇异值分解（SVD）中，一个矩阵A被分解为三个其他矩阵：

> A=U Σ V_t

其中：

+   A是原始矩阵。

+   U是一个矩阵，其列是A的左奇异向量。

+   Σ是一个对角矩阵，包含A的奇异值。这些值始终是非负的，通常按从大到小的顺序排列。

+   V_t是V的转置矩阵，其中V的列是A的右奇异向量。

简而言之，SVD将矩阵的复杂变换分解为更简单、易于理解的步骤，这些步骤涉及旋转和缩放。Σ中的奇异值告诉我们缩放因子，而U和V_t中的奇异向量告诉我们应用矩阵前后这些缩放的方向。

我们可以将奇异值视为衡量矩阵在空间中不同方向上拉伸或收缩程度的方式。每个奇异值对应一对奇异向量：一个右奇异向量（输入空间中的方向）和一个左奇异向量（输出空间中的方向）。

> 因此，奇异值是表示“**幅度**”的缩放因子，而 U 和 V_t 矩阵分别表示变换空间和原始空间中的“**方向**”。

如果矩阵的奇异值呈现快速衰减（最大的奇异值明显大于较小的奇异值），则意味着矩阵的有效秩（即显著奇异值的数量）远小于矩阵的实际维度。这表明该矩阵可以通过低秩矩阵进行良好的近似。

> 大的奇异值捕捉了数据中大部分的重要信息和变异性，而较小的奇异值贡献较少。

在 LLM 的上下文中，权重矩阵（例如注意力机制或前馈层中的矩阵）将输入数据（如词嵌入）转化为输出表示。主导的奇异值对应于输入空间中通过变换最为放大的方向，表示模型在这些方向上最为敏感或最具表达能力。较小的奇异值对应于在变换过程中较不重要或影响较小的方向。

奇异值的分布可以影响模型的泛化能力和鲁棒性。缓慢衰减（许多大奇异值）可能导致过拟合，而快速衰减（少量大奇异值）则可能表明欠拟合或信息丢失。

# 重新审视 Llama-3 架构

以下是 `meta-llama/Meta-Llama-3–8B-Instruct` 模型的 `config.json` 文件。值得注意的是，这个 LLM 使用了分组查询注意力机制，`num_key_value_heads` 为 8，这意味着组大小为 32/8=4。

```py
{
  "architectures": [
    "LlamaForCausalLM"
  ],
  "attention_bias": false,
  "attention_dropout": 0.0,
  "bos_token_id": 128000,
  "eos_token_id": 128009,
  "hidden_act": "silu",
  "hidden_size": 4096,
  "initializer_range": 0.02,
  "intermediate_size": 14336,
  "max_position_embeddings": 8192,
  "model_type": "llama",
  "num_attention_heads": 32,
  "num_hidden_layers": 32,
  "num_key_value_heads": 8,
  "pretraining_tp": 1,
  "rms_norm_eps": 1e-05,
  "rope_scaling": null,
  "rope_theta": 500000.0,
  "tie_word_embeddings": false,
  "torch_dtype": "bfloat16",
  "transformers_version": "4.40.0.dev0",
  "use_cache": true,
  "vocab_size": 128256
}
```

# 对（Q, K, V, O）矩阵的奇异值分析

现在，让我们进入本文的重点：通过奇异值分析 Llama-3–8B-Instruct 模型的（Q, K, V, O）矩阵！

## 代码

首先，让我们导入所有进行此分析所需的必要包。

```py
import transformers
import torch
import numpy as np
from transformers import AutoConfig, LlamaModel
from safetensors import safe_open
import os
import matplotlib.pyplot as plt
```

然后，让我们下载模型并将其保存到本地 `/tmp` 目录中。

```py
MODEL_ID = "meta-llama/Meta-Llama-3-8B-Instruct"
!huggingface-cli download {MODEL_ID} --quiet --local-dir /tmp/{MODEL_ID}
```

如果你有强大的 GPU，以下代码可能对你不相关。然而，如果你像我一样 GPU 资源较少，以下代码将非常有用，它可以让你只加载 LLama-3–8B 模型的特定层。

```py
def load_specific_layers_safetensors(model, model_name, layer_to_load):
    state_dict = {}
    files = [f for f in os.listdir(model_name) if f.endswith('.safetensors')]
    for file in files:
        filepath = os.path.join(model_name, file)
        with safe_open(filepath, framework="pt") as f:
            for key in f.keys():
                if f"layers.{layer_to_load}." in key:
                    new_key = key.replace(f"model.layers.{layer_to_load}.", 'layers.0.')
                    state_dict[new_key] = f.get_tensor(key)

    missing_keys, unexpected_keys = model.load_state_dict(state_dict, strict=False)
    if missing_keys:
        print(f"Missing keys: {missing_keys}")
    if unexpected_keys:
        print(f"Unexpected keys: {unexpected_keys}")
```

我们这样做的原因是因为 Google Colab 的免费 GPU 配置即便使用 `fp16` 精度也不足以加载 LLama-3–8B。此外，由于 `np.linalg.svd` 的构建方式，这项分析要求我们使用 `fp32` 精度。接下来，我们可以定义主函数，以获取给定 `matrix_type`、`layer_number` 和 `head_number` 的奇异值。

```py
def get_singular_values(model_path, matrix_type, layer_number, head_number):
    """
    Computes the singular values of the specified matrix in the Llama-3 model.

    Parameters:
    model_path (str): Path to the model
    matrix_type (str): Type of matrix ('q', 'k', 'v', 'o')
    layer_number (int): Layer number (0 to 31)
    head_number (int): Head number (0 to 31)

    Returns:
    np.array: Array of singular values
    """
    assert matrix_type in ['q', 'k', 'v', 'o'], "Invalid matrix type"
    assert 0 <= layer_number < 32, "Invalid layer number"
    assert 0 <= head_number < 32, "Invalid head number"

    # Load the model only for that specific layer since we have limited RAM even after using fp16
    config = AutoConfig.from_pretrained(model_path)
    config.num_hidden_layers = 1
    model = LlamaModel(config)
    load_specific_layers_safetensors(model, model_path, layer_number)

    # Access the specified layer
    # Always index 0 since we have loaded for the specific layer
    layer = model.layers[0]

    # Determine the size of each head
    num_heads = layer.self_attn.num_heads
    head_dim = layer.self_attn.head_dim

    # Access the specified matrix
    weight_matrix = getattr(layer.self_attn, f"{matrix_type}_proj").weight.detach().numpy()
    if matrix_type in ['q','o']:
        start = head_number * head_dim
        end = (head_number + 1) * head_dim
    else:  # 'k', 'v' matrices
        # Adjust the head_number based on num_key_value_heads
        # This is done since llama3-8b use Grouped Query Attention
        num_key_value_groups = num_heads // config.num_key_value_heads
        head_number_kv = head_number // num_key_value_groups
        start = head_number_kv * head_dim
        end = (head_number_kv + 1) * head_dim

    # Extract the weights for the specified head
    if matrix_type in ['q', 'k', 'v']:
        weight_matrix = weight_matrix[start:end, :]
    else:  # 'o' matrix
        weight_matrix = weight_matrix[:, start:end]

    # Compute singular values
    singular_values = np.linalg.svd(weight_matrix, compute_uv=False)

    del model, config

    return list(singular_values)
```

值得注意的是，由于 [HuggingFace](https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py#L262-L264) 的实现方式，我们可以通过行切片提取指定头部的 K、Q 和 V 矩阵的权重。

![](../Images/7d918b9dcef2b68bd73db2bbc0666d63.png)

HuggingFace 中的 Q, K, V 矩阵实现。请注意，在 PyTorch 中，矩阵的维度将是 `(d_out,d_in)`。来源：图片由作者提供。

至于 O 矩阵，我们可以通过列切片提取指定头的 O 权重，这要归功于线性代数！详细信息可以参见下图。

![](../Images/7b6f2589f4a2b568f84d4478ede921d8.png)

为什么我们可以通过列切片提取指定头的 O 权重矩阵。来源：图片由作者提供。

## 结果

为了进行分析，我们需要在不同的头、层和矩阵类型之间运行 `get_singular_values()` 函数。为了能够比较所有这些不同的组合，我们还需要为分析定义几个辅助指标：

+   `Top-10 Ratio`：前 10 个奇异值之和与所有奇异值之和的比率

+   `First/Last Ratio`：最大奇异值与最小奇异值之比。

+   `Least-10 Ratio`：最小 10 个奇异值之和与所有奇异值之和的比率

**(第 0 层，第 0 头) 分析**

![](../Images/ad046ce133b1148b8e7ce45d84f05143.png)

第 0 层第 0 头的奇异值分布。来源：图片由作者提供。

+   Q（查询）矩阵具有最大的初始奇异值（约 10），其次是 K（键）矩阵（约 8）。这两个矩阵的初始奇异值显著高于 V（值）和 O（输出）矩阵的初始奇异值。

+   不仅是初始奇异值，如果我们检查 Q 和 K 矩阵的 `Top-10 Ratio` 和 `First/Last Ratio`，这两个矩阵的值比 V 和 O 矩阵要高得多。这表明 Q **和 K 矩阵主要集中在少数几个维度上**，而 V 和 O 矩阵则以更加分散的方式捕获信息，跨多个成分。

+   如果我们查看 `Least-10 Ratio` 指标，我们也可以看到，对于 Q 和 K 矩阵，奇异值接近零，并且相较于 V 和 O 矩阵，它们的值要低得多。这是一个证据，表明 Q **和 K 矩阵具有低秩结构**，这意味着这些维度对模型整体性能的贡献较小。这些权重可以**潜在地被修剪**，而不会显著影响模型的准确性。

**(第 0 层，多个头部) 分析**

![](../Images/97770dcd424cc5927921570cbbbb5237.png)

不同头部下第 0 层的奇异值分布。来源：图片由作者提供。

+   随着 `head_number` 的增加，Q 和 K 矩阵的 `Top-10 比率` 增长的速度通常远高于 V 和 O 矩阵。这一发现同样适用于 Q 和 K 矩阵的 `Least-10 比率`，随着 `head_number` 的增加，这些比率趋近于 0，而 V 和 O 矩阵则没有这种变化。

+   这表明，**具有更高** `**head_number**` 的 Q 和 K 矩阵相比于具有较低 `head_number` 的头部，**甚至具有更低的秩结构**。换句话说，随着 `head_number` 的增加，Q 和 K 矩阵倾向于在更低的维度中存储信息。

**跨层分析**

![](../Images/f9b3d7c725d1844704e1269635ae0e3d.png)

不同层次和头部之间的奇异值分布。来源：作者提供的图片。

+   当我们进入更深的层时，我们发现 **Q 和 K 矩阵的初始值在下降**，但与 V 和 O 矩阵相比，仍然相对较高。

+   当我们进入更深的层时，Q 和 K 矩阵的 `Top-10 比率` 和 `First/Last 比率` 在某个特定头部出现下降趋势。与此同时，`Least-10 比率` 稍微呈上升趋势。这表明，**在更深层的 Q 和 K 矩阵相较于较低层次的矩阵训练得更好**。

> 然而，在第 1 层发现一个异常，Q 和 K 矩阵的 `First/Last 比率` 非常高，没有遵循我们在更深层次中发现的下降趋势。

![](../Images/e7e76f1ada578ebe62b7acb6aa3969c2.png)

不同头部和层次之间的奇异值分布。来源：作者提供的图片。

+   在同一层内我们在“Layer 0, Multiple Heads”部分发现的头部间模式，在更深的层次中不再明显。

**总结**

+   K 和 Q 矩阵的秩通常低于 V 和 O 矩阵。如果我们想进行剪枝或降维方法，可以更多关注 K 和 Q 矩阵。

+   层数越深，所有 (K, Q, V, O) 矩阵训练得越好。如果我们想进行剪枝或降维方法，可以更多关注较低的层次。

+   除了剪枝，进行仅对几个初始层进行完全微调也是一个有趣的实验，甚至我们可以使用 LoRA 来实现这一点。

# 结语

![](../Images/9c5497365494cd53d20f0be96f3f4967.png)

照片由 [Quino Al](https://unsplash.com/@quinoal?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)。

恭喜你坚持到现在！希望你从本文中学到了一些新东西。将线性代数中的经典概念应用到理解 LLM 的训练效果上，确实很有趣。

如果你喜欢这种类型的内容，请关注我的 Medium 账号，获取其他未来文章的通知。

# 关于作者

[Louis Owen](https://louisowen6.github.io/)是来自印尼的数据科学家/人工智能研究工程师，他总是渴望获得新知识。在他的职业生涯中，他在多个行业领域工作过，包括非政府组织、电子商务、对话式人工智能、在线旅游代理、智慧城市和金融科技。在工作之外，他喜欢通过自己的文章或辅导课程，帮助数据科学爱好者成为数据科学家。

目前，Louis 是Yellow.ai*的自然语言处理研究工程师，Yellow.ai*是全球领先的客户体验自动化平台。访问[Louis的个人网站](http://louisowen6.github.io/)以了解更多关于他的信息！最后，如果你有任何问题或需要讨论的话题，请通过[LinkedIn](https://www.linkedin.com/in/louisowen/)联系Louis。
