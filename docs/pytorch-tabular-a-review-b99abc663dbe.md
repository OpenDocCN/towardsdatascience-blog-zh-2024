# PyTorch Tabular：一项评测

> 原文：[`towardsdatascience.com/pytorch-tabular-a-review-b99abc663dbe?source=collection_archive---------6-----------------------#2024-07-16`](https://towardsdatascience.com/pytorch-tabular-a-review-b99abc663dbe?source=collection_archive---------6-----------------------#2024-07-16)

## 快速上手并避免混淆的概览

[](https://medium.com/@s.kirmer?source=post_page---byline--b99abc663dbe--------------------------------)![Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--b99abc663dbe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b99abc663dbe--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b99abc663dbe--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--b99abc663dbe--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b99abc663dbe--------------------------------) ·阅读时间：7 分钟·2024 年 7 月 16 日

--

![](img/c5c6675af28ac52feb539471f8608a15.png)

摄影：由[Pao Dayag](https://unsplash.com/@wisdomsky?utm_source=medium&utm_medium=referral)拍摄，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们时常会考虑是否尝试新的工具或实验一个包，然而这样做也存在一定的风险。如果工具无法完成我的需求，或者需要几天才能运行，或者需要我没有的复杂知识该怎么办？今天，我将分享自己使用 PyTorch Tabular 搭建模型并成功运行的经验，并提供代码示例，帮助其他考虑使用这个工具的用户，快速开始并避免不必要的麻烦。

这个项目最初是基于一个高维度的 CatBoost 模型，这是一个具有多类分类结果的监督学习用例。数据集大约有 30 个高度不平衡的类别，我将在未来的文章中详细描述。我想尝试将神经网络应用于相同的用例，看看性能上会有哪些变化，于是我发现 PyTorch Tabular 是一个不错的选择。当然，应用神经网络到表格数据上还有其他的选择，包括直接使用基础的 PyTorch，但在此基础上添加一个专门设计以适应特定问题的层，通常能让开发更加容易和快速。PyTorch Tabular 让你不用去考虑如何将数据框转换为张量，并提供了一个简单的接口，方便进行模型自定义。

# 入门指南

[`pytorch-tabular.readthedocs.io/en/latest/`](https://pytorch-tabular.readthedocs.io/en/latest/)上的文档非常易读且易于理解，尽管主页会引导你到文档的开发版本，因此如果你是从 pypi 安装的，请记得这一点。

我使用 poetry 来管理我的工作环境和库，而 poetry 和 PyTorch 有时并不完全兼容，所以这是需要考虑的一点。的确，我花了几个小时才将所有东西安装并顺利运行，但这并不是 PyTorch Tabular 开发者的错。

如你所猜测的，这一切都是针对表格数据进行优化的，因此我将我的工程化特征数据集以 pandas 格式带入。正如你稍后将看到的，只要我的字段是数字型或布尔型，我可以直接将数据帧传递到训练函数中，无需重新格式化。

# 设置

当你开始构建代码时，你将创建一些 PyTorch Tabular 训练函数所需的对象：

+   **DataConfig**：准备数据加载器，包括设置加载的并行性。

+   **TrainerConfig**：设置批次大小和周期数，还可以让你确定将使用哪个处理器，比如是否使用 GPU。

+   **OptimizerConfig**：允许你添加任何你喜欢的优化器，还可以设置学习率调度器，以及每个优化器的参数。我没有为我的使用场景自定义这一部分，它[默认为](https://pytorch-tabular.readthedocs.io/en/latest/tutorials/02-Exploring%20Advanced%20Features%20with%20PyTorch%20Tabular/#3-optimizerconfig) `Adam`。

+   **LinearHeadConfig**：如果你想自定义模型头部，它可以让你创建模型头部。我在这里不需要添加任何特别的内容。

+   然后你还需要创建一个模型配置，但基础类会根据你打算创建的模型类型而有所不同。我为我的模型使用了基本的 CategoryEmbeddingModelConfig，这里是你将分配所有模型架构项的地方，例如层大小、顺序、激活函数、学习率和指标。

```py
data_config = DataConfig(
    target=[target_col],
    continuous_cols=features,
    num_workers=0,
)
trainer_config = TrainerConfig(
    batch_size=1024, 
    max_epochs=20, 
    accelerator="gpu")

optimizer_config = OptimizerConfig()

head_config = LinearHeadConfig(
    layers="",  # No additional layer in head, just a mapping layer to output_dim
    dropout=0.0,
    initialization="kaiming", 
).__dict__  # model config requires dict

model_config = CategoryEmbeddingModelConfig(
    task="classification",
    layers="1024-512-512",  
    activation="LeakyReLU", 
    head="LinearHead",
    head_config=head_config,
    learning_rate=1e-3,
[METRICS ARGUMENTS COME NEXT]
```

在这一部分，指标的设置有些困惑，因此我会暂停并简要解释一下。我希望在训练过程中能看到几种不同的指标，在这个框架中，这需要为不同的参数传递几个列表。

```py
metrics=["f1_score", "average_precision", "accuracy", "auroc"],
metrics_params=[
    {"task": "multiclass", "num_classes": num_classes},
    {"task": "multiclass", "num_classes": num_classes},
    {},
    {},
],  # f1_score and avg prec need num_classes and task identifier
metrics_prob_input=[
    True,
    True,
    False,
    True,
],  # f1_score, avg prec, auroc need probability scores, while accuracy doesn't
```

在这里你可以看到我返回了四个指标，每个指标有不同的实现要求，因此每个列表表示这四个指标及其属性。例如，平均精度需要一些参数来指示这是一个多类问题，并且需要提供涉及的类别数量。与准确率不同，它还要求输入概率结果，而不是原始模型输出。

一旦你完成了所有这些配置，事情就变得非常简单——你只需要将每个对象传递到 TabularModel 模块中。

```py
tabular_model = TabularModel(
    data_config=data_config,
    model_config=model_config,
    optimizer_config=optimizer_config,
    trainer_config=trainer_config,
    verbose=True,
)
```

现在你可以开始训练了！

# 训练

一旦创建了训练集、测试集和验证集，设置训练是非常简单的。

```py
tabular_model.fit(train=train_split_df, validation=val_split_df)
result = tabular_model.evaluate(test_split_df)
```

启用详细模式进行训练时，会显示一个进度条并告知你当前的批次和迭代次数。如果你没有在数据加载器中使用并行处理，它可能会告诉你存在数据加载瓶颈，可以通过增加更多的工作线程来改善——是否关注这个问题取决于你，但由于我的推理任务将在一个非常稀疏的环境中运行，我选择不在数据加载器中使用并行处理。

训练完成后，你可以通过两种不同的方式保存模型——一种是作为 PyTorch Tabular 输出，便于加载进行微调或在 PyTorch Tabular 可用的环境中进行推理。另一种是作为仅用于推理的版本，例如一个基础的 PyTorch 模型，这对我来说非常有价值，因为我需要在一个更加简单的生产环境中使用模型对象。

```py
tabular_model.save_model(
  f"data/models/tabular_version_{model_name}"
 ) # The PyTorch Tabular version

tabular_model.save_model_for_inference(
  f"data/models/{model_name}", kind="pytorch"
 ) # The base PyTorch version
```

`save_model_for_inference` 方法还有一些其他选项，你可以在文档中阅读相关内容。还要注意，PyTorch Tabular 模型对象在加载时无法从 CPU 转移到 GPU 或反之——你必须保持在用于训练的相同计算资源上，除非你将模型保存为 PyTorch 模型对象。

# 推理

为了以后进行推理过程时重新加载模型，我发现确实需要保存这两个对象。然而，由于 PyTorch Tabular 模型会输出一个名为 `datamodule.sav` 的文件，这是在将数据传递给模型之前，始终如一地格式化推理数据所必需的。你可能自己搭建一个管道来输入数据，但我发现这比按照文档指示使用该文件要麻烦得多。（另外注意，这个文件可能会相当大——我的文件超过了 100MB，因此我选择将其单独存储，而不是与其他代码一起放在部署目录下。）

在 PyTorch Tabular 中，有内置的推理帮助工具，但我发现要以适当的标签和干净有用的格式输出我的多类别预测，仍然需要提取一些帮助代码并在我的代码库中重新编写。对于非多类别应用，这可能不是必需的，但如果你最终走这条路，[这是我从中改编的脚本](https://github.com/manujosephv/pytorch_tabular/blob/main/src/pytorch_tabular/tabular_model.py#L1226)。

这就是推理过程在代码中的表现方式，特征工程等部分省略。（此代码在 AWS Lambda 上的 Docker 中运行。）

```py
model_obj = torch.load("classifier_pytorch")
datamodule = joblib.load("datamodule.sav")

...

inference_dataloader = datamodule.prepare_inference_dataloader(
  self.processed_event[pytorch_feature_list], batch_size=256
)

task = "classification"

point_predictions = []
for batch in tqdm(inference_dataloader, desc="Generating Predictions..."):
    for k, v in batch.items():
        print("New Batch")
        if isinstance(v, list) and (len(v) == 0):
            continue
        batch[k] = v.to(pytorch_model.device)
    y_hat, ret_value = pytorch_model.predict(batch, ret_model_output=True)
    point_predictions.append(y_hat.detach().cpu())
```

在此之后，预测结果会被格式化并应用 softmax 以获得不同类别的概率，我还可以选择将预测结果重新附加到原始数据集上，供后续评估使用。

# 结论

总体而言，我对 PyTorch Tabular 在我的应用场景中的表现非常满意，尽管我不确定最终是否会将此模型部署到生产环境中。我面临的最大挑战是确保我的训练过程得到了妥善设计，以确保推理任务（主要是数据加载器）能够在我的生产环境中高效运行，但一旦我解决了这个问题，一切都很好。坦率地说，不用再花时间去思考张量格式化的事情，真的很值得！

所以，如果你想尝试将经典框架（如 CatBoost 或 LightGBM）中的模型适配到 PyTorch，我推荐尝试 PyTorch Tabular——至少，它应该能让你快速上手运行，因此你的实验周转不会太繁琐。下次，我将写关于我究竟是如何使用 PyTorch Tabular 的，并描述在同一问题下比较 CatBoost 和 PyTorch 的性能指标。

阅读更多我的作品，请访问 [www.stephaniekirmer.com](http://www.stephaniekirmer.com)。

# 进一步阅读

[## 探索 PyTorch Tabular 的高级功能

### 先决条件：具备深度学习的中级知识，并对表格问题（如回归）有基本了解…

pytorch-tabular.readthedocs.io](https://pytorch-tabular.readthedocs.io/en/latest/tutorials/02-Exploring%20Advanced%20Features%20with%20PyTorch%20Tabular/?source=post_page-----b99abc663dbe--------------------------------) [](https://pytorch-tabular.readthedocs.io/en/latest/models/?source=post_page-----b99abc663dbe--------------------------------) [## 监督模型

### 选择使用哪种模型以及在这些模型中设置哪些参数是特定于某一数据集的。在 PyTorch 中…

pytorch-tabular.readthedocs.io](https://pytorch-tabular.readthedocs.io/en/latest/models/?source=post_page-----b99abc663dbe--------------------------------)  [## 不平衡分类 - PyTorch Tabular

### PyTorch Tabular 还通过自定义采样器允许自定义批处理策略，这在处理时非常有用…

pytorch-tabular.readthedocs.io](https://pytorch-tabular.readthedocs.io/en/latest/tutorials/06-Imbalanced%20Classification/?source=post_page-----b99abc663dbe--------------------------------)
