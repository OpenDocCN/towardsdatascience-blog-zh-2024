# 提高Transformer模型效率：通过优化注意力层

> 原文：[https://towardsdatascience.com/increasing-transformer-model-efficiency-through-attention-layer-optimization-fefa6f87b1d6?source=collection_archive---------2-----------------------#2024-11-18](https://towardsdatascience.com/increasing-transformer-model-efficiency-through-attention-layer-optimization-fefa6f87b1d6?source=collection_archive---------2-----------------------#2024-11-18)

## 如何通过“更好”地关注来推动机器学习成本节省

[](https://chaimrand.medium.com/?source=post_page---byline--fefa6f87b1d6--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--fefa6f87b1d6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fefa6f87b1d6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fefa6f87b1d6--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--fefa6f87b1d6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fefa6f87b1d6--------------------------------) ·13分钟阅读·2024年11月18日

--

![](../Images/392d04054c201f7813635598c7dd5502.png)

图片由[Andrew Seaman](https://unsplash.com/@amseaman?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在2017年具有里程碑意义的论文[*“Attention Is All You Need”*](https://arxiv.org/abs/1706.03762)（Vaswani等人，2017）中首次提出，Transformer架构被广泛认为是过去十年最具影响力的科学突破之一。Transformer的核心是注意力机制，这是一种新颖的方法，使得人工智能模型能够通过根据手头任务集中关注输入序列的不同部分，来理解复杂的结构。最初在自然语言处理领域展示成功后，Transformer架构的成功迅速扩展到许多其他领域，包括语音识别、场景理解、强化学习、蛋白质结构预测等。然而，注意力层是高度资源密集型的，随着这些层成为越来越大模型的标准，其训练和部署的成本也急剧上升。这催生了减少这一核心层计算成本的迫切需求，以提高基于Transformer的人工智能模型的效率和可扩展性。

在本文中，我们将探索几个用于优化[PyTorch](https://pytorch.org/)中注意力的工具。我们将重点介绍那些保持注意力层准确性的方法。包括[PyTorch SDPA](https://pytorch.org/tutorials/intermediate/scaled_dot_product_attention_tutorial.html)、[FlashAttention](https://pytorch.org/blog/flashattention-3/)、[TransformerEngine](https://github.com/NVIDIA/TransformerEngine)注意力、[FlexAttention](https://pytorch.org/blog/flexattention/)和[xFormer](https://github.com/facebookresearch/xformers)注意力。其他通过近似注意力计算来减少计算成本的方法（例如，[DeepSpeed的稀疏注意力](https://www.deepspeed.ai/tutorials/sparse-attention/)、[Longformer](https://github.com/allenai/longformer)、[Linformer](https://arxiv.org/abs/2006.04768)等）将不予考虑。此外，我们也不会讨论那些对注意力性能有益，但与注意力计算本身无关的通用优化技术（例如，[FP8训练](/accelerating-pytorch-training-workloads-with-fp8-5a5123aec7d7)、[模型分片](https://pytorch.org/blog/introducing-pytorch-fully-sharded-data-parallel-api/)和[更多](/pytorch-model-performance-analysis-and-optimization-10c3c5822869)）。

值得注意的是，注意力优化是一个活跃的研究领域，新方法几乎定期发布。我们的目标是提高你对一些现有解决方案的认知，并为进一步探索和实验提供基础。我们下面分享的代码仅用于演示目的——我们并不对其准确性、最优性或健壮性做出任何声明。请不要将我们提到的任何平台、库或优化技术视为对其使用的认可。最适合你的选项将很大程度上依赖于你自己用例的具体情况。

非常感谢[伊兹哈克·莱维](https://www.linkedin.com/in/yitzhak-levi-49a217201/)对本文的贡献。

# 玩具模型

为了便于讨论，我们使用流行的[timm](https://pypi.org/project/timm/) Python包（版本0.9.7）构建了一个基于[视觉Transformer](https://en.wikipedia.org/wiki/Vision_transformer)（ViT）的分类模型。我们将使用这个模型来说明各种注意力内核对性能的影响。

我们首先定义了一个简化的Transformer块，通过将注意力函数传递给其构造函数来进行编程。由于注意力实现假设特定的输入张量格式，我们还提供了一个选项来控制格式，确保与我们选择的注意力内核兼容。

```py
# general imports
import os, time, functools

# torch imports
import torch
from torch.utils.data import Dataset, DataLoader
import torch.nn as nn

# timm imports
from timm.models.vision_transformer import VisionTransformer
from timm.layers import Mlp

IMG_SIZE = 224
BATCH_SIZE = 128

# Define ViT settings
NUM_HEADS = 16
HEAD_DIM = 64
DEPTH = 24
PATCH_SIZE = 16
SEQ_LEN = (IMG_SIZE // PATCH_SIZE)**2 # 196

class MyAttentionBlock(nn.Module):
    def __init__(
            self,
            attn_fn,
            format = None,
            dim: int = 768,
            num_heads: int = 12,
            **kwargs
    ) -> None:
        super().__init__()
        self.attn_fn = attn_fn
        self.num_heads = num_heads
        self.head_dim = dim // num_heads
        self.norm1 = nn.LayerNorm(dim)
        self.norm2 = nn.LayerNorm(dim)
        self.qkv = nn.Linear(dim, dim * 3, bias=False)
        self.proj = nn.Linear(dim, dim)
        self.mlp = Mlp(
            in_features=dim,
            hidden_features=dim * 4,
        )
        permute = (2, 0, 3, 1, 4)
        self.permute_attn = functools.partial(torch.transpose,dim0=1,dim1=2)

        if format == 'bshd':
            permute = (2, 0, 1, 3, 4)
            self.permute_attn = nn.Identity()
        self.permute_qkv = functools.partial(torch.permute,dims=permute)

    def forward(self, x_in: torch.Tensor) -> torch.Tensor:
        x = self.norm1(x_in)
        B, N, C = x.shape
        qkv = self.qkv(x).reshape(B, N, 3, self.num_heads, self.head_dim)
        # permute tensor based on the specified format
        qkv = self.permute_qkv(qkv)
        q, k, v = qkv.unbind(0)
        # use the attention function specified by the user
        x = self.attn_fn(q, k, v)
        # permute output according to the specified format
        x = self.permute_attn(x).reshape(B, N, C)
        x = self.proj(x)
        x = x + x_in
        x = x + self.mlp(self.norm2(x))
        return x
```

我们定义了一个随机生成的数据集，我们将在训练过程中将其输入到我们的模型中。

```py
# Use random data
class FakeDataset(Dataset):
    def __len__(self):
        return 1000000

    def __getitem__(self, index):
        rand_image = torch.randn([3, IMG_SIZE, IMG_SIZE],
                                 dtype=torch.float32)
        label = torch.tensor(data=index % 1000, dtype=torch.int64)
        return rand_image, label 
```

接下来，我们定义我们的ViT训练函数。尽管我们的示例主要演示了*训练*工作负载，但必须强调的是，在模型*推理*过程中优化注意力层同样重要，甚至更为重要。

我们定义的训练函数接受定制的Transformer块和一个控制是否使用[torch.compile](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html)的标志。

```py
def train_fn(block_fn, compile):
    torch.random.manual_seed(0)
    device = torch.device("cuda:0")
    torch.set_float32_matmul_precision("high")

    # Create dataset and dataloader
    train_set = FakeDataset()
    train_loader = DataLoader(
        train_set, batch_size=BATCH_SIZE,
        num_workers=12, pin_memory=True, drop_last=True)

    model = VisionTransformer(
       img_size=IMG_SIZE,
       patch_size=PATCH_SIZE,
       embed_dim=NUM_HEADS*HEAD_DIM,
       depth=DEPTH,
       num_heads=NUM_HEADS,
       class_token=False,
       global_pool="avg",
       block_fn=block_fn
    ).to(device)

    if compile:
        model = torch.compile(model)

    # Define loss and optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = torch.optim.SGD(model.parameters())

    model.train()

    t0 = time.perf_counter()
    summ = 0
    count = 0
    for step, data in enumerate(train_loader):
        # Copy data to GPU
        inputs = data[0].to(device=device, non_blocking=True)
        label = data[1].to(device=device, non_blocking=True)
        with torch.amp.autocast('cuda', enabled=True, dtype=torch.bfloat16):
            outputs = model(inputs)
            loss = criterion(outputs, label)
        optimizer.zero_grad(set_to_none=True)
        loss.backward()
        optimizer.step()

        # Capture step time
        batch_time = time.perf_counter() - t0
        if step > 20:  # Skip first steps
            summ += batch_time
            count += 1
        t0 = time.perf_counter()
        if step > 100:
            break
    print(f'average step time: {summ / count}')

# define compiled and uncompiled variants of our train function
train = functools.partial(train_fn, compile=False)
train_compile = functools.partial(train_fn, compile=True)
```

在下面的代码块中，我们定义了一个PyTorch原生的注意力函数，并用它来训练我们的ViT模型：

```py
def attn_fn(q, k, v):
    scale = HEAD_DIM ** -0.5
    q = q * scale
    attn = q @ k.transpose(-2, -1)
    attn = attn.softmax(dim=-1)
    x = attn @ v
    return x

block_fn = functools.partial(MyAttentionBlock, attn_fn=attn_fn)

print('Default Attention')
train(block_fn)
print('Compiled Default Attention')
train_compile(block_fn)
```

我们在一台配备[NVIDIA H100](https://www.nvidia.com/en-eu/data-center/h100/)的机器上运行此测试，使用[CUDA 12.4](https://developer.nvidia.com/cuda-toolkit)和[PyTorch](https://pytorch.org/) 2.5.1。未经编译的变体平均步骤时间为370毫秒（ms），而编译后的变体提高到242毫秒。我们将使用这些结果作为基准，在考虑其他执行注意力计算的解决方案时进行对比。

# PyTorch SDPA

提升PyTorch中注意力层性能的最简单方法之一是使用[scaled_dot_product_attention](https://pytorch.org/docs/stable/generated/torch.nn.functional.scaled_dot_product_attention.html)（SDPA）函数。目前在beta阶段，PyTorch SDPA整合了多个内核级优化，并根据输入的特性动态选择最有效的优化方法。支持的后端（截至目前）包括：[FlashAttention-2](https://arxiv.org/abs/2307.08691)，[Memory-Efficient Attention](https://github.com/facebookresearch/xformers)，一个基于C++的数学注意力，和[CuDNN](https://pytorch.org/blog/pytorch2-5/#beta-cudnn-backend-for-sdpa)。这些后端将高级操作融合在一起，同时利用GPU级优化来提高计算效率和内存利用率。

SDPA正在不断发展，新的和改进的后端实现定期推出。保持跟进最新的PyTorch发布版本是利用最新性能改进的关键。例如，[PyTorch 2.5](https://pytorch.org/blog/pytorch2-5/)引入了更新的[CuDNN后端](https://pytorch.org/blog/pytorch2-5/#beta-cudnn-backend-for-sdpa)，其中包含一个专门为[NVIDIA Hopper架构](https://www.nvidia.com/en-us/data-center/technologies/hopper-architecture/) GPU训练量身定制的[SDPA原语](https://developer.nvidia.com/blog/accelerating-transformers-with-nvidia-cudnn-9/)。

在下面的代码块中，我们遍历支持的后端列表，并评估每个后端的训练运行时性能。我们使用一个辅助函数，*set_sdpa_backend*，来编程SDPA后端：

```py
from torch.nn.functional import scaled_dot_product_attention as sdpa

def set_sdpa_backend(backend):
    torch.backends.cuda.enable_flash_sdp(False)
    torch.backends.cuda.enable_mem_efficient_sdp(False)
    torch.backends.cuda.enable_math_sdp(False)
    torch.backends.cuda.enable_cudnn_sdp(False)

    if backend in ['flash_sdp','all']:
        torch.backends.cuda.enable_flash_sdp(True)
    if backend in ['mem_efficient_sdp','all']:
        torch.backends.cuda.enable_mem_efficient_sdp(True)
    if backend in ['math_sdp','all']:
        torch.backends.cuda.enable_math_sdp(True)
    if backend in ['cudnn_sdp','all']:
        torch.backends.cuda.enable_cudnn_sdp(True)

for backend in ['flash_sdp', 'mem_efficient_sdp',
                'math_sdp', 'cudnn_sdp']:
    set_sdpa_backend(backend)
    block_fn = functools.partial(MyAttentionBlock,
                                 attn_fn=sdpa)

    print(f'PyTorch SDPA - {backend}')
    train(block_fn)
    print(f'Compiled PyTorch SDPA - {backend}')
    train_compile(block_fn)
```

我们在下面的表格中总结了我们的阶段性结果

![](../Images/7f98ef38f1ec30988ed4b79706748b20.png)

各种注意力函数的步骤时间（越低越好）— 按作者分类

尽管在 Eager 模式下选择 SDPA 后端会对性能产生显著影响，但通过 [模型编译](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html) 进行的优化似乎掩盖了不同注意力内核之间的差异。再次提醒，我们不应仅从这些结果得出结论，因为不同注意力函数的性能影响可能会根据特定的模型和使用场景而有所不同。

# 第三方注意力内核

虽然 PyTorch SDPA 是一个很好的起点，但使用第三方注意力内核可以进一步加速你的 ML 工作负载。这些替代方案通常提供更多的灵活性，提供更广泛的注意力配置选项。某些方案还可能包括针对特定硬件加速器或新一代 GPU 架构的优化。

在本节中，我们将探讨一些可用的第三方注意力核，并评估它们对运行时性能的潜在影响。

## FlashAttention-3

虽然 Pytorch SDPA 支持 [FlashAttention](https://arxiv.org/abs/2307.08691) 后端，但更先进的 FlashAttention 实现可以在 [flash-attn](https://pypi.org/project/flash-attn/) 库中找到。在这里，我们将探讨 [FlashAttention-3](https://pytorch.org/blog/flashattention-3/) 的测试版发布，它的速度比 FlashAttention-2 快 2 倍。鉴于其开发处于早期阶段，FlashAttention-3 只能直接从 [GitHub 仓库](https://github.com/HazyResearch/flash-attention) 安装，并且其使用仅限于特定的头维度。此外，它尚不支持模型编译。在下面的代码块中，我们将配置我们的 Transformer 块以使用 flash-attn-3，并将注意力输入格式设置为 "bshd"（batch，sequence，head，depth），以符合该库的要求。

```py
# flash attention 3
from flash_attn_interface import flash_attn_func as fa3
attn_fn = lambda q,k,v: fa3(q,k,v)[0]
block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=attn_fn,
                             format='bshd')

print(f'Flash Attention 3')
train(block_fn)
```

结果的步骤时间为 240 毫秒，比 SDPA flash-attn 快 5%。

## Transformer Engine

[Transformer Engine](https://github.com/NVIDIA/TransformerEngine)（TE）是一个专门设计的库，用于加速 NVIDIA GPU 上的 Transformer 模型。TE 定期更新，采用优化技术，充分利用最新 NVIDIA 硬件和软件的能力，使用户能够在这些功能被集成到通用框架如 PyTorch 之前，便可以访问到专门的内核。

在下面的代码块中，我们使用了来自 [TE 版本 1.11.0](https://pypi.org/project/transformer-engine/) 的 [DotProductAttention](https://github.com/NVIDIA/TransformerEngine/blob/main/transformer_engine/pytorch/attention.py#L7271)。与 PyTorch SDPA 类似，TE 支持多种后端，这些后端通过环境变量控制。在此我们演示了 *NVTE_FUSED_ATTN* 后端的使用。

```py
def set_te_backend(backend):
    # must be applied before first use of
    # transformer_engine.pytorch.attention
    os.environ["NVTE_FLASH_ATTN"] = '0'
    os.environ["NVTE_FUSED_ATTN"] = '0'
    os.environ["NVTE_UNFUSED_ATTN"] = '0'
    if backend == 'flash':
        os.environ["NVTE_FLASH_ATTN"] = '1'
    if backend == 'fused':
        os.environ["NVTE_FUSED_ATTN"] = '1'
    if backend == 'unfused':
        os.environ["NVTE_UNFUSED_ATTN"] = '1'

from transformer_engine.pytorch.attention import DotProductAttention
set_te_backend('fused')
attn_fn = DotProductAttention(NUM_HEADS, HEAD_DIM, NUM_HEADS,
                              qkv_format='bshd',
                              # disable masking (default is causal mask)
                              attn_mask_type='no_mask')

block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=attn_fn,
                             format='bshd')

print(f'Transformer Engine Attention')
train(block_fn)
print(f'Compiled Transformer Engine Attention')
train_compile(block_fn)
```

TE 注意力在 Eager 和编译模型变体中的平均步骤时间分别为 243 毫秒和 204 毫秒。

## XFormer 注意力

PyTorch SDPA 的内存高效后端基础是由 [xFormers](https://github.com/facebookresearch/xformers/tree/main) 库提供的注意力内核。我们可以再次访问源代码，以便受益于最新的内核优化和完整的 API 功能。在以下代码块中，我们使用了来自 [xFormers 版本 0.0.28](https://pypi.org/project/xformers/) 的 [memory_efficient_attention](https://facebookresearch.github.io/xformers/components/ops.html#xformers.ops.memory_efficient_attention) 操作符。

```py
# xformer memory efficient attention
from xformers.ops import memory_efficient_attention as mea
block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=mea,
                             format='bshd')

print(f'xFormer Attention ')
train(block_fn)
print(f'Compiled xFormer Attention ')
train_compile(block_fn)
```

这种急切模型变体的平均步长时间为 246 毫秒，比 SDPA 内存高效内核快了 10.5%。编译版的步长时间为 203 毫秒。

## 结果

以下表格总结了我们的实验：

![](../Images/8e3051e670033c5e99bcdf855549632c.png)

各种注意力函数的步长时间（较低的值更好）— 作者

对于急切模型，获胜者是 flash-attn-3，其平均步长时间比我们的基准模型快了 54%。这相当于训练成本减少了 54%。在编译模式下，优化内核的性能大致相等，最快的实现达到了 202 毫秒，比基准实验提升了 20%。

如前所述，节省的精确影响在很大程度上取决于模型定义。为了评估这种变化性，我们使用修改后的设置重新进行了实验，将注意力序列长度增加到 3136 个标记。

```py
IMG_SIZE = 224
BATCH_SIZE = 8

# Define ViT settings
NUM_HEADS = 12
HEAD_DIM = 64
DEPTH = 6
PATCH_SIZE = 4
SEQ_LEN = (IMG_SIZE // PATCH_SIZE)**2 # 3136
```

结果总结在下面的表格中：

![](../Images/54ce4ff619bd4657bb51114e22be1772.png)

大规模序列长度的结果（较低的值更好）— 作者

我们的初步观察是，当序列长度较大时，注意力内核的性能影响更为明显。再次，flash-attn-3 在急切执行模式下领先，这一次相比于 PyTorch 本地函数性能提升约 5 倍。对于编译后的模型，我们看到 TE 内核在整体最优步长时间 53 毫秒的表现下脱颖而出。

# 使用 FlexAttention 自定义注意力机制

到目前为止，我们主要关注了标准的注意力函数。然而，有时我们可能希望使用典型注意力计算的变体，其中我们要么屏蔽掉部分中间张量的值，要么对它们应用某些操作。这类变化可能会干扰我们使用前面介绍的优化注意力块的能力。在本节中，我们将讨论一些解决方法：

**利用高级内核 API**

许多优化过的注意力内核提供了广泛的 API，允许定制注意力计算。在实现新方案之前，请先探索这些 API，看看它们是否已经支持所需的功能。

**实现自定义内核：** 如果现有的 API 无法满足你的需求，可以考虑创建自己的自定义注意力实现。在之前的文章中（例如，[这里](/unleashing-the-power-of-triton-mastering-gpu-kernel-optimization-in-python-160a3f52701e)）我们讨论了自定义内核开发的一些优缺点。实现最佳性能可能非常困难。如果你选择这条路径，一种方法可能是从现有的（最佳）内核开始，进行最小的修改以集成所需的变化。

**使用 FlexAttention：** PyTorch 最近新增了 [FlexAttention](https://pytorch.org/blog/flexattention/)，它使用户能够实现各种各样的注意力变体，而无需在性能上做出妥协。用 *score* 表示查询和键令牌的点积结果，[flex_attention](https://github.com/pytorch/pytorch/blob/v2.5.1/torch/nn/attention/flex_attention.py#L927) 允许编程一个 [*score_mod*](https://pytorch.org/blog/flexattention/#score-mod-examples) 函数或一个自动应用于 *score* 张量的 [*block_mask*](https://pytorch.org/blog/flexattention/#mask-mods) 屏蔽。请参见 [文档](https://pytorch.org/blog/flexattention/) 和附带的 [attention-gym](https://github.com/pytorch-labs/attention-gym) 仓库，了解 API 支持的操作类型示例。

[FlexAttention](https://pytorch.org/blog/flexattention/) 通过将 *score_mod* 操作符 [编译](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html) 到注意力操作符中，从而创建一个单一的融合内核。它还利用 *block_masks* 的稀疏性来避免不必要的计算。[FlexAttention](https://pytorch.org/blog/flexattention/) 文档中报告的 [基准测试](https://pytorch.org/blog/flexattention/#performance) 显示了在多种使用案例中取得了显著的性能提升。

让我们来看一下 *score_mod* 和 *block_mask* 的实际应用。

## Score Mod 示例 — 使用 Tanh 进行软限制

Soft-capping 是一种常用的技术，用于控制 logit 大小（例如，见 [这里](https://arxiv.org/pdf/1611.09940)）。以下代码块扩展了我们的 PyTorch 原生注意力内核，添加了软限制：

```py
def softcap_attn(q, k, v):
    scale = HEAD_DIM ** -0.5
    q = q * scale
    attn = q @ k.transpose(-2, -1)
    # apply soft-capping
    attn = 30 * torch.tanh(attn/30)
    attn = attn.softmax(dim=-1)
    x = attn @ v
    return x
```

在下面的代码块中，我们先用我们的 PyTorch 原生内核训练模型，然后使用优化过的 Flex Attention API。这些实验是在 3136 长度的序列设置下运行的。

```py
# flex attention imports
from torch.nn.attention.flex_attention import (
    create_block_mask,
    create_mask,
    flex_attention
)
compiled_flex = torch.compile(flex_attention)

# score_mod definition
def tanh_softcap(score, b, h, q_idx, kv_idx):
    return 30 * torch.tanh(score/30)

block_fn = functools.partial(MyAttentionBlock, attn_fn=softcap_attn)

print(f'Attention with Softcap')
train(block_fn)
print(f'Compiled Attention with Softcap')
train_compile(block_fn)

flex_fn = functools.partial(flex_attention, score_mod=tanh_softcap)
compiled_flex_fn = functools.partial(compiled_flex, score_mod=tanh_softcap)

block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=flex_fn)
compiled_block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=compiled_flex_fn)

print(f'Flex Attention with Softcap')
train(compiled_block_fn)
print(f'Compiled Flex Attention with Softcap')
train_compile(block_fn)
```

实验结果记录在下表中：

![](../Images/de916d94926beddd350137bca752fa5b.png)

Soft-cap 步骤时间结果（越低越好） — 作者

Flash Attention 核心的影响显而易见，在贪婪模式下带来了约 3.5 倍的性能提升，在编译模式下提升了 1.5 倍。

## Mask Mod 示例 — 邻域屏蔽

我们通过对注意力*得分*应用稀疏掩码来评估*mask_mod*功能。回想一下，我们序列中的每个标记都代表我们二维输入图像中的一个补丁。我们修改了核函数，使得每个标记仅关注位于相应二维标记数组中5x5窗口内的其他标记。

```py
# convert the token id to a 2d index
def seq_indx_to_2d(idx):
    n_row_patches = IMG_SIZE // PATCH_SIZE
    r_ind = idx // n_row_patches
    c_ind = idx % n_row_patches
    return r_ind, c_ind

# only attend to tokens in a 5x5 surrounding window in our 2D token array
def mask_mod(b, h, q_idx, kv_idx):
    q_r, q_c = seq_indx_to_2d(q_idx)
    kv_r, kv_c = seq_indx_to_2d(kv_idx)
    return torch.logical_and(torch.abs(q_r-kv_r)<5, torch.abs(q_c-kv_c)<5)
```

作为我们实验的基准，我们使用支持传递注意力掩码的PyTorch SDPA。以下代码块包含了掩码SDPA实验以及Flex Attention实现：

```py
# materialize the mask to use in SDPA
mask = create_mask(mask_mod, 1, 1, SEQ_LEN, SEQ_LEN, device='cuda')

set_sdpa_backend('all')
masked_sdpa = functools.partial(sdpa, attn_mask=mask)
block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=masked_sdpa)
print(f'Masked SDPA Attention')
train(block_fn)
print(f'Compiled Masked SDPA Attention')
train_compile(block_fn)

block_mask = create_block_mask(mask_mod, None, None, SEQ_LEN, SEQ_LEN)
flex_fn = functools.partial(flex_attention, block_mask=block_mask)
compiled_flex_fn = functools.partial(compiled_flex, block_mask=block_mask)

block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=flex_fn)
compiled_block_fn = functools.partial(MyAttentionBlock,
                             attn_fn=compiled_flex_fn)

print(f'Masked Flex Attention')
train(compiled_block_fn)
print(f'Compiled Masked Flex Attention')
train_compile(block_fn)
```

实验结果如下所示：

![](../Images/3e73e0cff7eb528d567982bc709f8bb4.png)

掩码注意力步骤时间结果（时间越短越好）——由作者提供

再次证明，Flex Attention提供了显著的性能提升，急切模式下提升了2.19倍，编译模式下提升了2.59倍。

## Flex Attention的局限性

尽管我们成功展示了Flex Attention的强大潜力，但仍有一些局限性需要注意：

1.  **修改范围有限**：使用Flex Attention（截至本文写作时）只能修改注意力得分（即查询和键标记之间的点积结果）。它不支持在注意力计算的其他阶段进行修改。

1.  **依赖于torch.compile：** 鉴于对torch.compile的依赖，必须小心避免过多的重新编译，因为这可能会显著降低运行时性能。例如，虽然[文档掩码](https://pytorch.org/blog/flexattention/#document-maskingjagged-sequences)的支持非常有吸引力，但只有当所有文档的长度之和保持固定时，才能按预期执行。

1.  **不支持*score_mod*中的可训练参数：** 在撰写本文时，Flex Attention不支持包含*可训练*参数的*score_mod*实现。例如，尽管文档中提到支持[相对位置编码](https://pytorch.org/blog/flexattention/#relative-position-encodings)，但这些通常是通过*可训练*参数（而非固定值）实现的，而目前无法支持这一点。

面对这些限制，我们可以回到前面讨论的其他优化机会之一。

# 总结

随着机器学习模型中对变换器架构和注意力层的依赖增加，优化这些组件的工具和技术的需求也随之增加。在这篇文章中，我们探讨了多种注意力核函数变体，每种变体都有其独特的属性、能力和局限性。重要的是，一种方法并不适用于所有情况——不同的模型和应用场景需要使用不同的核函数和不同的优化策略。这强调了拥有各种各样的工具和技术以优化注意力层的重要性。

在这篇[续篇](https://chaimrand.medium.com/optimizing-transformer-models-for-variable-length-input-sequences-19fb88fddf71)中，我们将进一步探讨注意力层优化，重点应用我们讨论过的一些工具来应对处理可变长度输入序列的挑战。敬请期待…
