# PyTorch原生FP8数据类型

> 原文：[https://towardsdatascience.com/pytorch-native-fp8-fedc06f1c9f7?source=collection_archive---------2-----------------------#2024-05-21](https://towardsdatascience.com/pytorch-native-fp8-fedc06f1c9f7?source=collection_archive---------2-----------------------#2024-05-21)

## 加速PyTorch训练工作负载与FP8 — 第二部分

[](https://chaimrand.medium.com/?source=post_page---byline--fedc06f1c9f7--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--fedc06f1c9f7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fedc06f1c9f7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fedc06f1c9f7--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--fedc06f1c9f7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fedc06f1c9f7--------------------------------) ·8分钟阅读·2024年5月21日

--

![](../Images/0ae1023a0975006e7a589c7b4963d87b.png)

照片由[Alex Lion](https://unsplash.com/@alexandrelion?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

随着基于AI的应用在我们日常生活中越来越普及，优化这些应用程序的运行时性能的挑战也在增加。减少表示浮点类型所使用的位数是加速AI应用并减少其内存占用的常见技术。事实上，许多现代AI硬件加速器都包括对8位浮点表示的专门支持。在[上一篇文章](/accelerating-pytorch-training-workloads-with-fp8-5a5123aec7d7)中，我们讨论了使用FP8训练的潜力（及其风险），并在基于H100的训练实例上通过PyTorch和[Transformer Engine](https://github.com/NVIDIA/TransformerEngine)（一个专门用于加速NVIDIA GPU上Transformer模型的库）进行了实际演示。自然，PyTorch原生支持FP8数据类型只是时间问题。在这篇文章中，我们将回顾当前的功能，并展示它们在另一款支持FP8的AI芯片——[NVIDIA L4 GPU](https://www.nvidia.com/en-us/data-center/l4/)上的应用。更具体地说，我们将在Google Cloud的[g2-standard-16](https://cloud.google.com/compute/docs/gpus#l4-gpus)虚拟机（配备单个L4 GPU）、专用[深度学习虚拟机镜像](https://cloud.google.com/deep-learning-vm/docs/release-notes)和PyTorch 2.3.0上运行实验。

重要的是，截止本文撰写时，PyTorch原生FP8支持仍是*高度*实验性的。对于心脏不够强大的用户，或者*容错性差*的用户，强烈不推荐使用。本文主要面向早期采纳者——那些像我们一样，热衷于AI模型性能优化，并对这一新技术的潜在好处充满兴趣的人。请记住，我们提到的API在您阅读本文时可能已经经过修订。

我们的重点将放在使用FP8对AI应用程序运行时性能可能产生的影响上。关于算法方面的影响，我们推荐读者参考专门的教程（例如，[此处](https://arxiv.org/pdf/2209.05433)和[此处](https://www.nvidia.com/en-us/on-demand/session/gtcspring23-s52166/)）。

非常感谢[Yitzhak Levi](https://www.linkedin.com/in/yitzhak-levi-49a217201/)为本文所做的贡献。

# PyTorch原生Float8类型

从版本2.2开始，PyTorch包括对`torch.float8_e4m3fn`和`torch.float8_e5m2`数据类型的“[有限支持](https://pytorch.org/docs/stable/tensors.html#id13)”（分别具有3个和2个尾数位），这两者都是[FP8格式在深度学习中的应用](https://arxiv.org/pdf/2209.05433)论文中指定的类型。在下面的代码片段中，我们展示了新类型与传统浮点类型相比的属性和动态范围：

```py
import torch
from tabulate import tabulate

f32_type = torch.float32
bf16_type = torch.bfloat16
e4m3_type = torch.float8_e4m3fn
e5m2_type = torch.float8_e5m2

# collect finfo for each type
table = []
for dtype in [f32_type, bf16_type, e4m3_type, e5m2_type]:
    numbits = 32 if dtype == f32_type else 16 if dtype == bf16_type else 8
    info = torch.finfo(dtype)
    table.append([info.dtype, numbits, info.max, 
                  info.min, info.smallest_normal, info.eps])

headers = ['data type', 'bits', 'max', 'min', 'smallest normal', 'eps']
print(tabulate(table, headers=headers))

'''
Output:

data type      bits          max           min  smallest normal          eps
-------------  ----  -----------  ------------  ---------------  -----------
float32          32  3.40282e+38  -3.40282e+38      1.17549e-38  1.19209e-07
bfloat16         16  3.38953e+38  -3.38953e+38      1.17549e-38    0.0078125
float8_e4m3fn     8          448          -448         0.015625        0.125
float8_e5m2       8        57344        -57344      6.10352e-05         0.25
'''
```

我们可以通过在张量初始化函数中指定*dtype*来创建FP8张量，如下所示：

```py
device="cuda"
e4m3 = torch.tensor(1., device=device, dtype=e4m3_type)
e5m2 = torch.tensor(1., device=device, dtype=e5m2_type)
```

我们还可以将传统类型转换为FP8。以下代码块展示了我们生成一个浮点数的随机张量，并将其转换为四种不同浮点类型的结果：

```py
x = torch.randn(2, 2, device=device, dtype=f32_type)
x_bf16 = x.to(bf16_type)
x_e4m3 = x.to(e4m3_type)
x_e5m2 = x.to(e5m2_type)

print(tabulate([[‘float32’, *x.cpu().flatten().tolist()],
                [‘bfloat16’, *x_bf16.cpu().flatten().tolist()],
                [‘float8_e4m3fn’, *x_e4m3.cpu().flatten().tolist()],
                [‘float8_e5m2’, *x_e5m2.cpu().flatten().tolist()]],
               headers=[‘data type’, ‘x1’, ‘x2’, ‘x3’, ‘x4’]))

'''
The sample output demonstrates the dynamic range of the different types:

data type                  x1              x2              x3              x4
-------------  --------------  --------------  --------------  --------------
float32        2.073093891143  -0.78251332044  -0.47084918620  -1.32557279110
bfloat16       2.078125        -0.78125        -0.4707031      -1.328125
float8_e4m3fn  2.0             -0.8125         -0.46875        -1.375
float8_e5m2    2.0             -0.75           -0.5            -1.25
-------------  --------------  --------------  --------------  --------------
'''
```

尽管创建FP8张量足够简单，但您可能很快发现，对FP8张量执行一些基本的算术运算在PyTorch 2.3.0（截止本文撰写时）中并不支持。唯一的（可以说是最重要的）例外是FP8矩阵乘法，它通过专用的`torch._scaled_mm`函数得到支持。如下所示的代码块演示了该函数，它接受两个FP8张量（相同类型）及其相关的缩放因子，以及一个可选的偏置张量：

```py
output, output_amax = torch._scaled_mm(
        torch.randn(16,16, device=device).to(e4m3_type),
        torch.randn(16,16, device=device).to(e4m3_type).t(),
        bias=torch.randn(16, device=device).to(bf16_type),
        out_dtype=e4m3_type,
        scale_a=torch.tensor(1.0, device=device),
        scale_b=torch.tensor(1.0, device=device)
    )
```

为了更好地了解当前API的能力和使用模式，您可以查看PyTorch仓库中的[API测试脚本](https://github.com/pytorch/pytorch/blob/v2.3.0/test/test_matmul_cuda.py)。

与我们在[上一篇文章](/accelerating-pytorch-training-workloads-with-fp8-5a5123aec7d7)中演示的[Transformer Engine](https://github.com/NVIDIA/TransformerEngine) 库中的 FP8 支持不同，PyTorch 本身支持显式定义和使用 FP8 数据类型。这为高级开发者提供了更大的灵活性，以设计和实现自定义的 FP8 算法。然而，正如我们在上一篇文章中讨论的，成功的 FP8 ML 模型训练通常需要一些创造性的技巧；许多用户希望有一个高级 API，能够自动应用经过实战验证的缩放和类型转换方案到他们现有的 AI 模型训练算法中。虽然在本文撰写时（截至目前）尚未成为官方 PyTorch 库的一部分，但此类功能通过[float8_experimental 库](https://github.com/pytorch-labs/float8_experimental/tree/cb55df259cfb22a856ca92107a778343edea5fc7)提供。

# 使用原生 PyTorch 进行 FP8 训练

在本节中，我们将演示如何使用[float8_experimental 库](https://github.com/pytorch-labs/float8_experimental/tree/cb55df259cfb22a856ca92107a778343edea5fc7)在一个简单的[视觉 Transformer](https://en.wikipedia.org/wiki/Vision_transformer)（ViT-Huge）分类模型上进行训练，该模型具有 6.32 亿个参数（使用流行的[timm](https://pypi.org/project/timm/) Python 包的版本 1.0.3）。请参见文档中的[安装说明](https://github.com/pytorch-labs/float8_experimental/tree/cb55df259cfb22a856ca92107a778343edea5fc7?tab=readme-ov-file#installation)以获取有关安装[float8_experimental 库](https://github.com/pytorch-labs/float8_experimental/tree/cb55df259cfb22a856ca92107a778343edea5fc7)的详细信息。我们将 ViT 骨干网络设置为使用*平均全局池化*，以避免当前实现中的一些问题（例如，参见[这里](https://github.com/pytorch/pytorch/issues/123761)）。在下面的代码块中，我们演示了使用[延迟缩放策略](https://github.com/pytorch-labs/float8_experimental/tree/cb55df259cfb22a856ca92107a778343edea5fc7?tab=readme-ov-file#float8-linear-with-delayed-scaling)在随机生成的数据集上进行 FP8 训练。我们包括了控制选项，用于切换浮点类型、使用[torch.compile](https://pytorch.org/docs/stable/generated/torch.compile.html)模式，并设置批量大小。

```py
import torch
from timm.models.vision_transformer import VisionTransformer
from torch.utils.data import Dataset, DataLoader
import os
import time

#float8 imports
from float8_experimental import config
from float8_experimental.float8_linear import Float8Linear
from float8_experimental.float8_linear_utils import (
    swap_linear_with_float8_linear,
    sync_float8_amax_and_scale_history
)

#float8 configuration (see documentation)
config.enable_amax_init = False
config.enable_pre_and_post_forward = False

# model configuration controls:
fp8_type = True # toggle to change floating-point precision
compile_model = True # toggle to enable model compilation
batch_size = 32 if fp8_type else 16 # control batch size

device = torch.device('cuda')

# use random data
class FakeDataset(Dataset):
    def __len__(self):
        return 1000000
    def __getitem__(self, index):
        rand_image = torch.randn([3, 256, 256], dtype=torch.float32)
        label = torch.tensor(data=[index % 1024], dtype=torch.int64)
        return rand_image, label

# get data loader
def get_data(batch_size):
    ds = FakeDataset()
    return DataLoader(
           ds,
           batch_size=batch_size, 
           num_workers=os.cpu_count(),
           pin_memory=True
         )

# define the timm model
def get_model():
    model = VisionTransformer(
        class_token=False,
        global_pool="avg",
        img_size=256,
        embed_dim=1280,
        num_classes=1024,
        depth=32,
        num_heads=16
    )
    if fp8_type:
        swap_linear_with_float8_linear(model, Float8Linear)
    return model

# define the training step
def train_step(inputs, label, model, optimizer, criterion):
    with torch.autocast(device_type='cuda', dtype=torch.bfloat16):
        outputs = model(inputs)
        loss = criterion(outputs, label)
    optimizer.zero_grad(set_to_none=True)
    loss.backward()
    if fp8_type:
        sync_float8_amax_and_scale_history(model)
    optimizer.step()

model = get_model()
optimizer = torch.optim.Adam(model.parameters())
criterion = torch.nn.CrossEntropyLoss()
train_loader = get_data(batch_size)

# copy the model to the GPU
model = model.to(device)
if compile_model:
    # compile model
    model = torch.compile(model)
model.train()

t0 = time.perf_counter()
summ = 0
count = 0

for step, data in enumerate(train_loader):
    # copy data to GPU
    inputs = data[0].to(device=device, non_blocking=True)
    label = data[1].squeeze(-1).to(device=device, non_blocking=True)

    # train step
    train_step(inputs, label, model, optimizer, criterion)

    # capture step time
    batch_time = time.perf_counter() - t0
    if step > 10:  # skip first steps
        summ += batch_time
        count += 1
    t0 = time.perf_counter()
    if step > 50:
        break

print(f'average step time: {summ / count}')
```

我们首先注意到，使用较低精度的数据类型可以释放 GPU 内存，这使得我们能够将批量大小翻倍。下面的表格总结了在使用不同配置设置进行训练时的性能结果（通过平均步骤时间测量）。如文档中所建议，使用[torch.compile](https://pytorch.org/docs/stable/generated/torch.compile.html)的 FP8 实验是在 PyTorch 的一个夜间版本上运行的（具体是版本 torch-2.4.0.dev20240520+cu121）。

![](../Images/6914ffc764ae0e4cf074631ed38b15d0.png)

实验结果（作者）

正如结果所示，使用FP8线性层使我们的玩具模型比基准实验提高了47%(!!)的性能，但*仅仅*在与[torch.compile](https://pytorch.org/docs/stable/generated/torch.compile.html)结合使用时才有效。当然，结果将根据模型的定义和大小有所不同。

# 与Transformer Engine的比较

为了便于比较，我们使用[Transformer Engine (TE)](https://github.com/NVIDIA/TransformerEngine)库（版本1.6）实现了相同的训练序列。尽管TE包含了自己优化的[TransformerLayer](https://github.com/NVIDIA/TransformerEngine/blob/67bc399d7ba7e49bf540746c1ef6a7e43eaed8f7/transformer_engine/pytorch/transformer.py#L70)（正如我们[之前的文章](/accelerating-pytorch-training-workloads-with-fp8-5a5123aec7d7)所展示的那样），我们手动将[torch.nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html#linear)层替换为[TE Linear](https://github.com/NVIDIA/TransformerEngine/blob/release_v0.12/transformer_engine/pytorch/module/linear.py#L442)层，以便将我们的比较评估仅限于FP8线性支持。在下面的代码块中，我们实现了一个简单的线性层交换工具（请自担风险！！）并将其应用于我们的ViT模型。我们还包含了使用TE进行FP8训练所需的训练步骤函数：

```py
import transformer_engine.pytorch as te

# swap all linear layers with te.Linear
def simple_swap(model):
    for submodule_name, submodule in model.named_modules():
        if isinstance(submodule, torch.nn.Linear):
            print(submodule_name)
            path_in_state_dict = submodule_name.split('.')
            current_module = model

            # traverse to leaf module
            leaf_path = path_in_state_dict[:-1]
            leaf_name = path_in_state_dict[-1]
            for child_name in leaf_path:
                current_module = getattr(current_module, child_name)

            # perform a swap
            old_leaf = getattr(current_module, leaf_name)
            new_leaf = te.Linear(old_leaf.in_features, 
                                 old_leaf.out_features, 
                                 old_leaf.bias is not None)
            setattr(current_module, leaf_name, new_leaf)

def get_model():
    model = VisionTransformer(
        class_token=False,
        global_pool="avg",
        img_size=256,
        embed_dim=1280,
        num_classes=1024,
        depth=32,
        num_heads=16
    )
    simple_swap(model)
    return model

def train_step(inputs, label, model, optimizer, criterion):
    with torch.autocast(device_type='cuda', dtype=torch.bfloat16):
        with te.fp8_autocast(enabled=True):
            outputs = model(inputs)
        loss = criterion(outputs, label)
    optimizer.zero_grad(set_to_none=True)
    loss.backward()
    optimizer.step()
```

以下是TE实验的结果：

![](../Images/d76a672e9da89ed29140d37f80038e24.png)

虽然未编译的TE FP8模型的表现明显优于我们之前的FP8模型，但编译后的PyTorch FP8模型仍然提供最佳结果。值得注意的是，截至本文撰写时，TE FP8模块不支持[模型编译](https://pytorch.org/docs/stable/generated/torch.compile.html)。因此，应用[torch.compile](https://pytorch.org/docs/stable/generated/torch.compile.html)将导致“部分编译”，即每次使用FP8时都会发生多个图断裂。

我们故意将测试限制在我们玩具模型的线性层。毫不奇怪，将TE的全部功能应用到我们的模型中，正如我们[之前的文章](/accelerating-pytorch-training-workloads-with-fp8-5a5123aec7d7)所展示的那样，将使性能提高72%（与我们的基准实验相比）。

如果要进行更详细的TE与PyTorch原生FP8操作符的比较，涵盖更广泛的矩阵大小，我们建议关注[这个GitHub问题](https://github.com/pytorch/pytorch/issues/123761)。

# 结论

尽管目前 PyTorch 原生 FP8 支持还处于初期阶段，在 API 覆盖和性能方面仍有明显改进空间，但我们已经成功展示了 PyTorch 原生 FP8 支持的一些潜在优势。首先，能够显式声明并操作 FP8 张量，将使开发者在定制基于 FP8 的算法时具有更大的自由度。其次，内置的 JIT 编译支持为运行时优化提供了更大的潜力。第三个优势（此处未展示）是能够支持更多范围的 FP8 支持设备。这与由 NVIDIA 开发的 TE 相反，后者高度针对其 GPU 进行了定制。

# 总结

人工智能模型的规模不断扩大，这需要先进的技术和算法来减少内存占用并提高运行时性能。在专用硬件加速器上使用 FP8 数据类型，能够实现这两者的平衡。尽管我们主要关注的是模型训练，但其在模型推理中的影响同样重要，因为将大模型加载到内存并运行所需的时间，可能会对用户体验产生决定性影响。

我们在本文中实验过的 PyTorch 原生 FP8 数据类型和运算符，必将有助于加速这一重要技术的应用与推广。我们期待看到这种原生支持如何发展和成熟。

欲了解更多关于 AI 模型优化的工具和技术，务必查看我们的[其他文章](https://chaimrand.medium.com/)。
