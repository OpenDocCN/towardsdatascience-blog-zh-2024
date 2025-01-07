# 在Google Colab上训练语言模型

> 原文：[https://towardsdatascience.com/training-language-models-on-google-colab-6e145ff092bf?source=collection_archive---------6-----------------------#2024-12-04](https://towardsdatascience.com/training-language-models-on-google-colab-6e145ff092bf?source=collection_archive---------6-----------------------#2024-12-04)

## 迭代微调与序列化指南

[](https://john-hawkins.medium.com/?source=post_page---byline--6e145ff092bf--------------------------------)[![John Hawkins](../Images/4c36312a7b99f0b1b2575fd7184d60b5.png)](https://john-hawkins.medium.com/?source=post_page---byline--6e145ff092bf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e145ff092bf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6e145ff092bf--------------------------------) [John Hawkins](https://john-hawkins.medium.com/?source=post_page---byline--6e145ff092bf--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e145ff092bf--------------------------------) ·阅读时间5分钟·2024年12月4日

--

![](../Images/059f892fb6a9dc178619df35de9be181.png)

图片由[Shio Yang](https://unsplash.com/@shioyang?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

所以，你最近发现了[Hugging Face](https://huggingface.co/)和一系列开源模型，如BERT、Llama、BART，以及由[Mistral AI](https://mistral.ai/)、[Facebook](http://facebook.com)、[Salesforce](https://www.salesforce.com/)等公司推出的众多生成性语言模型。现在你想为自己的副项目尝试微调一些大型语言模型。一开始一切都很顺利，但接着你发现这些模型计算资源消耗巨大，而你手头并没有GPU处理器。

[Google Colab](https://colab.google/)慷慨地为你提供了免费计算资源，帮助你解决这个问题。缺点是，你需要在一个过渡性的基于浏览器的环境中完成所有操作。更糟的是，整个环境是有时间限制的，所以似乎不管你做什么，最终都会在内核关闭并且环境被清除时失去你珍贵的微调模型和所有结果。

别担心。解决这个问题的方法是：利用[Google Drive](https://drive.google.com)保存你的中间结果或模型参数。这样你就可以在稍后的阶段继续实验，或者将训练好的模型拿去其他地方进行推理。

为了做到这一点，您需要一个 Google 账户，且该账户必须有足够的 Google Drive 空间来存储您的训练数据和模型检查点。我假设您已经在 Google Drive 中创建了一个名为`data`的文件夹，里面包含您的数据集。然后另创建一个名为`checkpoints`的空文件夹。

在您的 Google Colab Notebook 中，您可以使用以下命令挂载您的 Google Drive：

```py
from google.colab import drive
drive.mount('/content/drive') 
```

现在，您可以在一个新的单元格中使用以下两个命令列出数据和检查点目录的内容：

```py
!ls /content/drive/MyDrive/data
!ls /content/drive/MyDrive/checkpoint
```

如果这些命令有效，您现在可以在笔记本中访问这些目录。如果命令无效，可能是您错过了授权步骤。上述的`drive.mount`命令应该会弹出一个窗口，要求您点击并授权访问。您可能错过了弹出窗口，或者没有选择所有必要的访问权限。请尝试重新运行单元格并检查。

一旦解决了访问权限问题，您可以编写脚本，使得模型和结果被序列化到 Google Drive 目录中，从而在会话间得以保留。在理想情况下，您会编写训练作业的代码，使得任何运行时间过长的脚本可以从上一次会话加载部分训练好的模型，并从那个点继续训练。

实现这一点的一种简单方法是创建一个保存和加载函数，供训练脚本使用。训练过程应始终检查是否存在部分训练的模型，然后再初始化一个新的模型。以下是一个保存函数的示例：

```py
def save_checkpoint(epoch, model, optimizer, scheduler, loss, model_name, overwrite=True):
    checkpoint = {
        'epoch': epoch,
        'model_state_dict': model.state_dict(),
        'optimizer_state_dict': optimizer.state_dict(),
        'scheduler_state_dict': scheduler.state_dict(),
        'loss': loss
    }
    direc = get_checkpoint_dir(model_name)
    if overwrite:
        file_path = direc + '/checkpoint.pth'
    else:
        file_path = direc + '/epoch_'+str(epoch) + '_checkpoint.pth'
    if not os.path.isdir(direc):
       try:
          os.mkdir(direc)
       except:
          print("Error: directory does not exist and cannot be created")
          file_path = direc +'_epoch_'+str(epoch) + '_checkpoint.pth'
    torch.save(checkpoint, file_path)
    print(f"Checkpoint saved at epoch {epoch}")
```

在此情况下，我们将模型状态以及一些元数据（如`epochs`和`loss`）保存在一个字典结构中。我们提供了一个选项来覆盖单个检查点文件，或为每个 epoch 创建一个新的文件。我们使用的是 torch 的保存函数，但原则上，您可以使用其他序列化方法。关键思想是，您的程序打开文件并确定现有文件的训练 epoch 数量。这允许程序决定是否继续训练或跳过。

类似地，在加载函数中，我们传入一个我们希望使用的模型引用。如果已经存在一个序列化的模型，我们将参数加载到模型中，并返回训练的 epoch 数量。这个 epoch 值将决定还需要多少额外的训练轮次。如果没有模型，则返回默认的零个 epoch，表明模型仍然是初始化时的状态。

```py
def load_checkpoint(model_name, model, optimizer, scheduler):
    direc = get_checkpoint_dir(model_name)
    if os.path.exists(direc):
        file_path = get_path_with_max_epochs(direc)
        checkpoint = torch.load(file_path, map_location=torch.device('cpu'))
        model.load_state_dict(checkpoint['model_state_dict'])
        optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
        scheduler.load_state_dict(checkpoint['scheduler_state_dict'])
        epoch = checkpoint['epoch']
        loss = checkpoint['loss']
        print(f"Checkpoint loaded from {epoch} epoch")
        return epoch, loss
    else:
        print(f"No checkpoint found, starting from epoch 1.")
        return 0, None
```

这两个函数需要在您的训练循环中调用，并且您需要确保返回的`epochs`值用于更新训练迭代中的`epochs`值。这样，您就拥有了一个可以在内核崩溃时重新启动的训练过程，它会从中断的地方继续。

核心训练循环可能看起来像下面这样：

```py
 EPOCHS = 10
for exp in experiments: 
    model, optimizer, scheduler = initialise_model_components(exp)
    train_loader, val_loader = generate_data_loaders(exp)
    start_epoch, prev_loss = load_checkpoint(exp, model, optimizer, scheduler)
    for epoch in range(start_epoch, EPOCHS):
        print(f'Epoch {epoch + 1}/{EPOCHS}')
        # ALL YOUR TRAINING CODE HERE
        save_checkpoint(epoch + 1, model, optimizer, scheduler, train_loss, exp) 
```

注：在这个例子中，我正在实验训练多个不同的模型配置（在一个名为`experiments`的列表中），可能会使用不同的训练数据集。辅助函数`initialise_model_components`和`generate_data_loaders`负责确保我为每个实验获取正确的模型和数据。

上面的核心训练循环允许我们重用整体代码结构，以训练并序列化这些模型，确保每个模型都能完成所需的训练轮次。如果我们重新启动该过程，它会再次遍历实验列表，但会放弃那些已经达到最大训练轮次的实验。

希望你能使用这个样板代码，在Google Colab中设置自己的深度学习语言模型训练实验流程。请发表评论并告诉我你正在构建什么以及如何使用这段代码。

非常感谢[Aditya Pramar](https://medium.com/@adityapramar15)提供的初始脚本，正是这些脚本促成了这篇作品的诞生。
