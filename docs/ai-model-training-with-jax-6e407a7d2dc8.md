# 使用 JAX 进行 AI 模型训练

> 原文：[https://towardsdatascience.com/ai-model-training-with-jax-6e407a7d2dc8?source=collection_archive---------5-----------------------#2024-05-29](https://towardsdatascience.com/ai-model-training-with-jax-6e407a7d2dc8?source=collection_archive---------5-----------------------#2024-05-29)

## 开启超快速的AI/ML开发之路

[](https://chaimrand.medium.com/?source=post_page---byline--6e407a7d2dc8--------------------------------)[![Chaim Rand](../Images/c52659c389f167ad5d6dc139940e7955.png)](https://chaimrand.medium.com/?source=post_page---byline--6e407a7d2dc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e407a7d2dc8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6e407a7d2dc8--------------------------------) [Chaim Rand](https://chaimrand.medium.com/?source=post_page---byline--6e407a7d2dc8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e407a7d2dc8--------------------------------) ·10分钟阅读·2024年5月29日

--

![](../Images/daf51b59d88858acc3ee4f29b8c04a88.png)

图片由 [Matt Foxx](https://unsplash.com/@foxxmd?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

![](../Images/142d8af6812f5b6fa49f682b50bceed6.png)

作者

在AI模型开发中，最关键的决策之一就是选择机器学习开发框架。多年来，许多库争夺着“AI开发者首选框架”的这一有利头衔。（还记得 [Caffe](https://caffe.berkeleyvision.org/) 和 [Theano](https://github.com/Theano/Theano) 吗？）在几年的时间里，[TensorFlow](https://www.tensorflow.org/)——它强调高性能、基于图形的计算——似乎一直是遥不可及的领先者（根据作者对学术论文中提及的次数和社区支持的强度的估计）。大约在十年之交，[PyTorch](https://pytorch.org/)——凭借其用户友好的 Pythonic 接口——似乎已经成为无可争议的“女王”。然而，近年来一个新兴的框架迅速流行起来，已经无法忽视。瞄准这一宝贵的桂冠，[JAX](https://jax.readthedocs.io/en/latest/quickstart.html) 旨在最大化 AI 模型训练和推理的性能，同时不妥协用户体验。

在本文中，我们将评估这一新框架，演示其使用，并分享我们对其优缺点的一些看法。值得注意的是，本文*并非*JAX教程。如果想了解JAX，建议您查阅[官方文档](https://jax.readthedocs.io/en/latest/quickstart.html)以及许多在线教程（例如，[这里](https://github.com/gordicaleksa/get-started-with-JAX)）。尽管我们关注的主要是AI模型训练，但需要注意的是，JAX在AI/ML领域及其他领域还有许多额外的应用。基于JAX构建了几个高级ML库。在本文中，我们将使用[Flax](https://flax.readthedocs.io/en/latest/quick_start.html)，它是截至目前看起来最受欢迎的库。

感谢[Ohad Klein](https://www.linkedin.com/in/ohad-klein-947aaa187/?originalSubdomain=il)和[Yitzhak Levi](https://www.linkedin.com/in/yitzhak-levi-49a217201/)对本文的贡献。

# JAX背后的秘密——XLA编译

让我们马上公开一点：对JAX没有任何不尊重的意思，JAX的真正强大之处在于其使用了[XLA](https://openxla.org/xla)编译。JAX所展示的卓越运行时性能，来自于XLA启用的硬件特定优化。而许多与JAX相关的功能特性，比如即时编译（JIT）和“函数式编程”范式，实际上都源自于XLA。事实上，XLA编译并不独特于JAX，[TensorFlow](https://www.tensorflow.org/api_docs/python/tf/config/optimizer/set_jit)和[PyTorch](https://pytorch.org/xla/release/2.3/index.html)都支持使用XLA的选项。然而，与其他流行的框架不同，JAX从底层开始就是为使用XLA而设计的。这使得它可以将其设计与实现紧密结合，包括[JIT](https://jax.readthedocs.io/en/latest/_autosummary/jax.jit.html#jax.jit)、自动微分（[grad](https://jax.readthedocs.io/en/latest/_autosummary/jax.grad.html#jax.grad)）、矢量化（[vmap](https://jax.readthedocs.io/en/latest/_autosummary/jax.vmap.html#jax.vmap)）、并行化（[pmap](https://jax.readthedocs.io/en/latest/_autosummary/jax.pmap.html)）、分片（[shard_map](https://jax.readthedocs.io/en/latest/notebooks/shard_map.html)）和其他功能（所有这些都值得非常尊重）与底层的XLA库紧密结合。（作为对比，参见[这篇](https://dev-discuss.pytorch.org/t/functionalization-in-pytorch-everything-you-wanted-to-know/965)有趣的文章，了解PyTorch“功能化”的历史。）

正如我们在 [上一篇文章中讨论的那样](/how-to-accelerate-your-pytorch-training-with-xla-on-aws-3d599bc8f6a9)，XLA JIT 编译器对与模型相关的计算图进行全面分析，将连续的张量操作融合为单个内核，移除冗余的图组件，并输出最适合底层加速器的机器码。这导致每个训练步骤所需的机器级操作数（FLOPS）减少，主机与加速器之间的通信开销减少（例如，减少需要加载到加速器中的内核数），内存占用减少，专用加速器引擎的利用率提高，等等。

除了运行时性能优化，XLA 的另一个重要特性是其可插拔的基础设施，使得它能够扩展对额外 AI 加速器的支持。XLA 是 OpenXLA 项目的一部分，并正在与机器学习领域的多个参与者合作构建。

与此同时，正如我们在 [上一篇文章中详细描述的](/how-to-accelerate-your-pytorch-training-with-xla-on-aws-3d599bc8f6a9)，依赖 XLA 也意味着一些局限性和潜在的陷阱。特别是，许多 AI 模型，包括具有动态张量形状的模型，可能无法在 XLA 中达到最优运行效果。需要特别小心以避免计算图断裂和图的重新编译。你还应该考虑代码调试性的影响。

# JAX 实战 — 简单示例

在本节中，我们将演示如何在 JAX 中使用（单个）GPU训练一个简单的 AI 模型，并将其与 PyTorch 进行比较。如今，有许多高层次的机器学习开发平台，包含多个机器学习框架的后端。这使得我们可以将 JAX 与其他框架的性能进行比较。在本节中，我们将使用 [HuggingFace](https://huggingface.co/) 的 [Transformers](https://huggingface.co/docs/transformers/en/index) 库，该库包括许多常见的 Transformer 支持的模型的 PyTorch 和 JAX 实现。更具体地说，我们将定义一个基于 [Vision Transformer](https://huggingface.co/docs/transformers/en/model_doc/vit)（ViT）的分类模型，使用 [ViTForImageClassification](https://huggingface.co/docs/transformers/v4.41.2/en/model_doc/vit#transformers.ViTForImageClassification) 和 [FlaxViTForImageClassification](https://huggingface.co/docs/transformers/en/model_doc/vit#transformers.FlaxViTForImageClassification) 模块，分别对应 PyTorch 和 JAX 实现。以下代码块包含了模型定义：

```py
import torch
import jax, flax, optax
import jax.numpy as jnp

def get_model(use_jax=False):
    from transformers import ViTConfig

    if use_jax:
        from transformers import FlaxViTForImageClassification as ViTModel
    else:
        from transformers import ViTForImageClassification as ViTModel

    vit_config = ViTConfig(
        num_labels = 1000,
        _attn_implementation = 'eager'  # this disables flash attention
    )

    return ViTModel(vit_config)
```

请注意，我们选择禁用 [flash attention](https://huggingface.co/docs/text-generation-inference/en/conceptual/flash_attention) 的使用，因为该优化仅为 PyTorch 模型实现（截至本文撰写时）。

由于我们在这篇文章中的兴趣集中在运行时性能上，因此我们将使用随机生成的数据集来训练我们的模型。我们利用JAX [支持使用 PyTorch 数据加载器](https://jax.readthedocs.io/en/latest/notebooks/Neural_Network_and_Data_Loading.html#data-loading-with-pytorch)的事实：

```py
def get_data_loader(batch_size, use_jax=False):
    from torch.utils.data import Dataset, DataLoader, default_collate

    # create dataset of random image and label data
    class FakeDataset(Dataset):
        def __len__(self):
            return 1000000

        def __getitem__(self, index):
            if use_jax: # use nhwc
                rand_image = torch.randn([224, 224, 3], dtype=torch.float32)
            else: # use nchw
                rand_image = torch.randn([3, 224, 224], dtype=torch.float32)
            label = torch.tensor(data=[index % 1000], dtype=torch.int64)
            return rand_image, label

    ds = FakeDataset()

    if use_jax:  # convert torch tensors to numpy arrays
        def numpy_collate(batch):
            from jax.tree_util import tree_map
            import jax.numpy as jnp
            return tree_map(jnp.asarray, default_collate(batch))
        collate_fn = numpy_collate
    else:
        collate_fn = default_collate

    ds = FakeDataset()
    dl = DataLoader(ds, batch_size=batch_size,
                    collate_fn=collate_fn)
    return dl
```

接下来，我们定义了 PyTorch 和 JAX 的训练循环。JAX 训练循环依赖于一个 [Flax TrainState](https://flax.readthedocs.io/en/latest/api_reference/flax.training.html#train-state) 对象，其定义遵循 [基本教程](https://flax.readthedocs.io/en/latest/quick_start.html#training-step)，用于在 Flax 中训练机器学习模型：

```py
@jax.jit
def train_step_jax(train_state, batch):
    with jax.default_matmul_precision('tensorfloat32'):
        def forward(params):
            logits = train_state.apply_fn({'params': params}, batch[0])
            loss = optax.softmax_cross_entropy(
                logits=logits.logits, labels=batch[1]).mean()
            return loss

        grad_fn = jax.grad(forward)
        grads = grad_fn(train_state.params)
        train_state = train_state.apply_gradients(grads=grads)
        return train_state

def train_step_torch(batch, model, optimizer, loss_fn, device):
    inputs = batch[0].to(device=device, non_blocking=True)
    label = batch[1].squeeze(-1).to(device=device, non_blocking=True)
    outputs = model(inputs)
    loss = loss_fn(outputs.logits, label)
    optimizer.zero_grad(set_to_none=True)
    loss.backward()
    optimizer.step()
```

现在让我们将一切整合在一起。在下面的脚本中，我们包含了用于使用基于图的 JIT 编译选项的控制，使用了 [torch.compile](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html) 和 [torch_xla](https://pytorch.org/xla/release/2.3/index.html)：

```py
def train(batch_size, mode, compile_model):
    print(f"Mode: {mode} \n"
          f"Batch size: {batch_size} \n"
          f"Compile model: {compile_model}")

    # init model and data loader
    use_jax = mode == 'jax'
    use_torch_xla = mode == 'torch_xla'
    model = get_model(use_jax)
    train_loader = get_data_loader(batch_size, use_jax)

    if use_jax:
        # init jax settings
        from flax.training import train_state
        params = model.module.init(jax.random.key(0), 
                                   jnp.ones([1, 224, 224, 3]))['params']
        optimizer = optax.sgd(learning_rate=1e-3)
        state = train_state.TrainState.create(apply_fn=model.module.apply,
                                              params=params, tx=optimizer)
    else:
        if use_torch_xla:
            import torch_xla
            import torch_xla.core.xla_model as xm
            import torch_xla.distributed.parallel_loader as pl
            torch_xla._XLAC._xla_set_use_full_mat_mul_precision(
                use_full_mat_mul_precision=False)

            device = xm.xla_device()
            backend = 'openxla'

            # wrap data loader
            train_loader = pl.MpDeviceLoader(train_loader, device)
        else:
            device = torch.device('cuda')
            backend = 'inductor'

        model = model.to(device)
        if compile_model:
            model = torch.compile(model, backend=backend)
        model.train()
        optimizer = torch.optim.SGD(model.parameters())
        loss_fn = torch.nn.CrossEntropyLoss()

    import time
    t0 = time.perf_counter()
    summ = 0
    count = 0

    for step, data in enumerate(train_loader):
        if use_jax:
            state = train_step_jax(state, data)
        else:
            train_step_torch(data, model, optimizer, loss_fn, device)

        # capture step time
        batch_time = time.perf_counter() - t0
        if step > 10:  # skip first steps
            summ += batch_time
        count += 1
        t0 = time.perf_counter()
        if step > 50:
            break

    print(f'average step time: {summ / count}')

if __name__ == '__main__':
    import argparse
    torch.set_float32_matmul_precision('high')

    parser = argparse.ArgumentParser(description='Toy Training Script.')
    parser.add_argument('--batch-size', type=int, default=32,
                        help='input batch size for training (default: 2)')
    parser.add_argument('--mode', choices=['pytorch', 'jax', 'torch_xla'],
                        default='jax',
                        help='choose training mode')
    parser.add_argument('--compile-model', action='store_true', default=False,
                        help='whether to apply torch.compile to the model')
    args = parser.parse_args()

    train(**vars(args))
```

## 关于基准比较的重要说明

在分析基准比较时，至关重要的是我们要对它们的执行过程非常谨慎和批判。尤其是在 AI 模型开发的情况下，基于不准确数据做出的决策可能会带来极其昂贵的后果。在比较训练模型的运行时性能时，有许多因素可能会对我们的测量产生主导作用，包括浮点数精度、矩阵乘法（matmul）精度、数据加载方法、是否使用闪存/融合注意力等。例如，如果 PyTorch 的默认矩阵乘法精度是 float32，而 JAX 是 tensorfloat32，那么我们无法从它们的性能比较中获得太多有价值的信息。这些设置可以通过像 [jax.default_matmul_precision](https://jax.readthedocs.io/en/latest/_autosummary/jax.default_matmul_precision.html) 和 [torch.set_float32_matmul_precision](https://pytorch.org/docs/stable/generated/torch.set_float32_matmul_precision.html) 这样的 API 来控制。在我们的脚本中，我们尝试将这些潜在问题隔离开来，但并不能保证我们已经成功解决了所有问题。

# 结果

我们在两台Google Cloud虚拟机上运行了训练脚本，一台是[g2-standard-16](https://cloud.google.com/compute/docs/gpus#l4-gpus)虚拟机（配备单个NVIDIA L4 GPU），另一台是[a2-highgpu-1g](https://cloud.google.com/compute/docs/gpus#a100-gpus)虚拟机（配备单个NVIDIA A100 GPU）。在每种情况下，我们都使用了一个专用的[深度学习虚拟机镜像](https://cloud.google.com/deep-learning-vm/docs/release-notes)（common-cu121-v20240514-ubuntu-2204-py310），并安装了PyTorch（2.3.0）、PyTorch/XLA（2.3.0）、JAX（0.4.28）、Flax（0.8.4）、Optax（0.2.2）以及[HuggingFace](https://huggingface.co/)的[Transformers](https://huggingface.co/docs/transformers/en/index)库（4.41.1）。有关GPU环境下[JAX](https://jax.readthedocs.io/en/latest/installation.html)和[PyTorch/XLA](https://github.com/pytorch/xla?tab=readme-ov-file#python-packages)的正确安装方法，请参阅官方文档。

以下表格展示了多个实验的运行时结果。请记住，比较性能可能会根据模型架构和运行环境发生显著变化。此外，代码的少量修改也有可能对结果产生可测量的影响。

![](../Images/701a8ba50a631a6814a29d74ceebcf5f.png)

NVIDIA L4 GPU上的结果（作者提供）

![](../Images/0e1ddbccc2699b93c0a3a6b7fec44d24.png)

NVIDIA A100 GPU上的结果（作者提供）

尽管JAX在L4 GPU上表现出比其替代方案更优越的性能，但在A100上它与PyTorch/XLA的表现不相上下。考虑到共同的XLA后端，这并不令人惊讶。JAX生成的任何XLA（HLO）图应该（至少在理论上）也能由PyTorch/XLA实现。torch.compile选项在两个平台上的表现都不尽如人意。鉴于我们选择了全精度浮动数，这在一定程度上是可以预见的。正如[上一篇文章](/tips-and-tricks-for-upgrading-to-pytorch-2-3127db1d1f3d)所述，torch.compile的真正价值体现在使用[自动混合精度（AMP）](https://pytorch.org/docs/stable/amp.html)时。

欲了解更多JAX和PyTorch之间性能比较的详细信息，请务必查看[HuggingFace](https://github.com/huggingface/transformers/tree/main/examples/flax/text-classification#runtime-evaluation)、[Google](https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/main/community-content/vertex_model_garden/benchmarking_reports/jax_vit_benchmarking_report.md)或[MLCommons](https://mlcommons.org/benchmarks/training/)编写的更全面的基准报告。

# 那么，为什么使用JAX？

在 JAX 中进行训练的一个常见动机是 JIT 编译所带来的潜在运行时性能优化。但是，考虑到 PyTorch 中的新的（PyTorch/XLA）和更新的（torch.compile）JIT 编译选项，这一说法很容易受到挑战。事实上，考虑到 PyTorch 拥有庞大的开发者社区以及 PyTorch 中原生支持的众多功能，而 JAX/FLAX 并不支持（例如，[自动混合精度](https://pytorch.org/tutorials/recipes/recipes/amp_recipe.html)，[高级注意力层](https://pytorch.org/tutorials/intermediate/scaled_dot_product_attention_tutorial.html#beta-implementing-high-performance-transformers-with-scaled-dot-product-attention-sdpa)，截至本文撰写时），有人可能会强烈主张*不*值得花时间学习 JAX。然而，我们认为现代 AI 开发团队必须熟悉 JAX 及其提供的机会。这对于那些像我们一样，执着于利用最新、最强大的模型训练方法的团队尤为重要。除了潜在的性能收益外，以下是一些额外的激励因素：

## 为 XLA 设计

与经过后期“功能化”的 PyTorch/XLA 相对，JAX 从一开始就是为 XLA 设计的。这意味着某些在 PyTorch/XLA 中可能显得复杂或混乱的操作，在 JAX 中可以优雅地完成。一个很好的例子就是在训练序列中混合使用 JIT 和非 JIT 函数——在 JAX 中非常简单，但在 PyTorch/XLA 中可能需要一些创造性处理。

如上所述，理论上 PyTorch/XLA 和 TensorFlow 可以生成一个与 JAX 创建的 XLA（HLO）图相同的图（因此也能实现相同的性能）。然而，实际上，生成的图的质量将取决于框架级实现如何转换为 XLA。更优的转换最终将导致更好的运行时性能。由于 JAX 本身对 XLA 的原生支持，它可能相对于其他框架具有优势。

## 支持 XLA 设备

JAX 的 XLA 兼容性使其特别吸引那些开发专用 AI 加速器的开发者，例如 [Google Cloud TPU](https://lightning.ai/docs/pytorch/stable/accelerators/tpu.html)、[Intel](https://developer.habana.ai/) [Gaudi](https://developer.habana.ai/) 和 [AWS Trainium](https://aws.amazon.com/machine-learning/trainium/) 芯片，这些芯片通常被称为“XLA 设备”。特别是那些在 TPU 上进行训练的团队，可能会发现 JAX 的支持生态系统比 PyTorch/XLA 更加先进。

## 高级功能

近年来，JAX在发布一些先进的训练功能方面，比其他框架早了很多。例如，[SPMD](https://jax.readthedocs.io/en/latest/sharded-computation.html)，这是一种先进的设备并行技术，提供了最先进的模型分片机会，几年前就在JAX中推出，最近才被引入到[PyTorch](https://pytorch.org/blog/pytorch-xla-spmd/)。另一个例子是[Palas](https://jax.readthedocs.io/en/latest/pallas/index.html)，它（终于）使得可以为XLA设备构建自定义内核。

## 开源模型

随着JAX框架日益流行，越来越多的开源AI模型开始以JAX的形式发布。一些经典的例子包括谷歌开源的[MaxText](https://github.com/google/maxtext/)（大语言模型）和[AlphaFold v2](https://github.com/google-deepmind/alphafold)（蛋白质结构预测）模型。要充分利用这些模型，你需要学习JAX，或者承担将其移植到其他语言的复杂任务。

我们坚信，这些考虑因素值得将JAX纳入任何机器学习开发工具包。

# 总结

本文我们探讨了崭露头角的JAX机器学习开发框架。我们描述了它依赖于XLA编译器，并展示了在一个简单例子中的使用。尽管PyTorch的JIT编译API（[torch.compile](https://pytorch.org/tutorials/intermediate/torch_compile_tutorial.html)和[PyTorch/XLA](https://pytorch.org/xla/release/2.3/index.html)）也支持类似的性能优化潜力，但JAX通常因其快速的运行时执行而被提及。每种选项的相对性能将大大依赖于模型的细节和运行时环境。

重要的是，每个机器学习（ML）开发框架选项可能具有独特的功能（例如，JAX中的SPMD自动分片和PyTorch中的SDPA注意力——截至本文写作时），这些功能可能对比较运行时性能产生决定性影响。因此，框架的最佳选择可能取决于你的模型能从这些功能中获益的程度。

总之，正如我们在许多[之前的文章](https://chaimrand.medium.com/)中强调的那样，要在不断发展的机器学习开发领域中保持相关性，就需要紧跟最新的工具和技术，包括JAX机器学习开发框架。
