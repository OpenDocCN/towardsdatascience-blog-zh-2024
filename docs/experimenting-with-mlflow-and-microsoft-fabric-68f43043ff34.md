# 与 MLFlow 和 Microsoft Fabric 的实验

> 原文：[https://towardsdatascience.com/experimenting-with-mlflow-and-microsoft-fabric-68f43043ff34?source=collection_archive---------7-----------------------#2024-04-22](https://towardsdatascience.com/experimenting-with-mlflow-and-microsoft-fabric-68f43043ff34?source=collection_archive---------7-----------------------#2024-04-22)

## Fabric 疯狂系列第4部分

[](https://medium.com/@roger_noble?source=post_page---byline--68f43043ff34--------------------------------)[![Roger Noble](../Images/869b5b0f237f24b119ca6c41c2e31162.png)](https://medium.com/@roger_noble?source=post_page---byline--68f43043ff34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68f43043ff34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--68f43043ff34--------------------------------) [Roger Noble](https://medium.com/@roger_noble?source=post_page---byline--68f43043ff34--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68f43043ff34--------------------------------) ·10分钟阅读·2024年4月22日

--

![](../Images/e7c1d4c5510a3052bfb3ab90be98619c.png)

图片来源：作者和 ChatGPT。“设计一幅插图，展示数据实验的图像，聚焦于篮球数据”的提示。ChatGPT，4，OpenAI，2024年4月15日。[https://chat.openai.com.](https://chat.openai.com./)

*特别感谢* [*Martim Chaves*](https://medium.com/@mgrc99) *共同撰写了这篇文章并开发了示例脚本。*

毋庸置疑，机器学习（ML）系统需要精心调优才能真正发挥作用，而模型在第一次运行时完美工作是极为罕见的情况！

在开始你的 ML 之旅时，一个容易陷入的陷阱是尝试很多不同的方式来提高性能，但却没有记录这些配置。这会导致你很难知道哪个配置（或配置组合）表现最佳。

在开发模型时，有许多可以调整的“旋钮”和“杠杆”，通常提高性能的最佳方法是尝试不同的配置，看看哪个效果最好。这些内容包括[改进使用的特征](https://medium.com/@roger_noble/feature-engineering-with-microsoft-fabric-and-pyspark-16d458018744)、尝试不同的模型架构、调整模型的超参数等。实验需要系统化，并且结果需要记录。因此，拥有一个良好的实验设置对于任何实用的 ML 系统开发至关重要，就像源代码管理对于代码开发的重要性一样。

这是*实验*开始发挥作用的地方。实验是一种跟踪不同配置及其结果的方法。

在Fabric中使用实验的好处是，它们实际上是[MLFlow](https://mlflow.org/)的一个封装，MLFlow是一个非常流行的开源平台，用于管理端到端的机器学习生命周期。这意味着我们可以使用MLFlow提供的所有强大功能，但又不必担心设置一个需要协作环境的MLFlow基础设施。这使我们可以专注于更有趣的部分 😎！

在这篇文章中，我们将讨论如何在Fabric中使用实验，以及如何记录和分析这些实验的结果。具体来说，我们将涵盖：

+   MLFlow是如何工作的？

+   创建和设置实验

+   运行实验和记录结果

+   分析结果

从高层次来看，MLFlow是一个帮助管理端到端机器学习生命周期的平台。它是一个帮助跟踪实验、将代码打包成可重现运行、共享和部署模型的工具。它本质上是一个专门用于跟踪你运行的各种实验配置和结果的数据库。

在MLFlow中有两个主要的组织结构——**实验**和**运行**。

实验是一个运行的集合，其中每个运行是执行一段代码、一个函数或一个脚本。这可能是训练一个模型，但也可以用于跟踪任何在不同运行间可能会变化的内容。实验是一种将相关运行进行分组的方式。

对于每个运行，可以记录信息并将其附加到该运行上——这些信息可以是指标、超参数、标签、工件（例如图表、文件或其他有用的输出），甚至是模型！通过将模型附加到运行上，我们可以追踪哪个模型在某个运行中被使用，以及它的表现如何。可以将其视为模型的版本控制，这也是我们将在下一篇文章中深入探讨的内容。

运行可以被过滤和比较。这使我们能够了解哪些运行更成功，并选择表现最佳的运行，使用其配置（例如，在部署中）。

现在我们已经介绍了MLFlow的基本工作原理，接下来让我们了解如何在Fabric中使用它！

# 创建和设置实验

就像在Fabric中的一切一样，创建项目可以通过几种方式完成，既可以通过工作区中的**+ 新建**菜单，也可以使用数据科学体验或通过代码。在这种情况下，我们将使用数据科学体验。

![](../Images/be643f7ab148a28cc673d70288df619f.png)

图1——使用UI创建实验。图像来源：作者。

一旦完成，为了在Notebook中使用该实验，我们需要`import mlflow`并设置实验名称：

```py
import mlflow

experiment_name = "[name of the experiment goes here]"

# Set the experiment
mlflow.set_experiment(experiment_name)
```

另外，实验也可以通过代码创建，这需要一个额外的命令：

```py
import mlflow

experiment_name = "[name of the experiment goes here]"

# First create the experiment
mlflow.create_experiment(name=experiment_name)

# Then select it
mlflow.set_experiment(experiment_name)
```

请注意，如果已存在相同名称的实验，`create_experiment`将抛出一个错误。我们可以通过先检查实验是否存在，只有在不存在时才创建它来避免这个问题：

```py
# Check if experiment exists
# if not, create it
if not mlflow.get_experiment_by_name(experiment_name):
    mlflow.create_experiment(name=experiment_name)
```

现在我们已经在当前上下文中设置了实验，我们可以开始运行将保存到该实验中的代码。

# 运行实验并记录结果

为了开始将我们的结果记录到实验中，我们需要启动一个运行。这个操作是通过`start_run()`函数完成的，并返回一个`run`上下文管理器。以下是如何启动一个运行的示例：

```py
 # Start the training job with `start_run()`
with mlflow.start_run(run_name="example_run") as run:
    # rest of the code goes here
```

一旦运行开始，我们就可以开始记录度量、参数和工件。下面是一个使用简单模型和数据集的代码示例，我们记录了模型的得分和使用的超参数：

```py
# Set the hyperparameters
hyper_params = {"alpha": 0.5, "beta": 1.2}

# Start the training job with `start_run()`
with mlflow.start_run(run_name="simple_training") as run:
 # Create model and dataset
 model = create_model(hyper_params)
 X, y = create_dataset()

 # Train model
 model.fit(X, y)

 # Calculate score
 score = lr.score(X, y)

 # Log metrics and hyper-parameters
 print("Log metric.")
 mlflow.log_metric("score", score)

 print("Log params.")
 mlflow.log_param("alpha", hyper_params["alpha"])
 mlflow.log_param("beta", hyper_params["beta"])
```

在我们上面的示例中，训练了一个简单的模型，并计算了其得分。请注意，如何使用`mlflow.log_metric("metric_name", metric)`来记录度量，并使用`mlflow.log_param("param_name", param)`来记录超参数。

## 数据

现在让我们看一下用于训练我们基于篮球比赛结果的模型的代码。我们所查看的数据来自2024年美国大学篮球锦标赛，这些数据来自2024年3月机器学习狂热Kaggle竞赛，相关细节可以在[此处](https://www.kaggle.com/competitions/march-machine-learning-mania-2024/overview)找到，且该数据集使用CC BY 4.0许可协议。

在我们的设置中，我们想尝试三种不同的模型，这些模型使用了越来越多的参数。对于每个模型，我们还想尝试三种不同的学习率（一个控制我们在每次迭代中调整网络权重多少的超参数）。目标是找到最佳的模型和学习率组合，以便在测试集上获得最佳的[Brier得分](https://en.wikipedia.org/wiki/Brier_score)。

## 模型

为了定义模型架构，我们使用了TensorFlow，创建了三个简单的神经网络。以下是帮助定义模型的函数。

```py
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

def create_model_small(input_shape):
    model = Sequential([
        Dense(64, activation='relu', input_shape=(input_shape,)),
        Dense(1, activation='sigmoid')
    ])
    return model

def create_model_medium(input_shape):
    model = Sequential([
        Dense(64, activation='relu', input_shape=(input_shape,)),
        Dense(64, activation='relu'),
        Dense(1, activation='sigmoid')
    ])
    return model

def create_model_large(input_shape):
    model = Sequential([
        Dense(128, activation='relu', input_shape=(input_shape,)),
        Dense(64, activation='relu'),
        Dense(64, activation='relu'),
        Dense(1, activation='sigmoid')
    ])
    return model
```

通过这种方式创建模型，使我们可以轻松地尝试不同的架构，并查看它们的表现。我们可以使用字典创建一个小型的*模型工厂*，让我们能够轻松地创建我们想要实验的模型。

我们还定义了输入形状，即可用特征的数量。我们决定将模型训练100个epoch，这应该足以让模型收敛🤞。

```py
model_dict = {
    'model_sma': create_model_small,   # small
    'model_med': create_model_medium,  # medium
    'model_lar': create_model_large    # large
}

input_shape = X_train_scaled_df.shape[1]
epochs = 100
```

在这初步设置之后，是时候对模型字典进行迭代了。对于每个模型，都会创建一个实验。请注意，我们使用了之前的代码片段，其中我们首先检查实验是否存在，只有在实验不存在时才会创建它。否则，我们只需设置它。

```py
import mlflow

for model_name in model_dict:

    # create mlflow experiment
    experiment_name = "experiment_v2_" + model_name

    # Check if experiment exists
    # if not, create it
    if not mlflow.get_experiment_by_name(experiment_name):
        mlflow.create_experiment(name=experiment_name)

    # Set experiment
    mlflow.set_experiment(experiment_name)
```

设置完实验后，我们针对每个模型进行了三次运行，尝试不同的学习率`[0.001, 0.01, 0.1]`。

```py
for model_name in model_dict:

 # Set the experiment
 ...

 learning_rate_list = [0.001, 0.01, 0.1]

    for lr in learning_rate_list:

        # Create run name for better identification
        run_name = f"{model_name}_{lr}"
        with mlflow.start_run(run_name=run_name) as run:
   ...
   # Train model
   # Save metrics
```

然后，在每次运行中，我们初始化了一个模型，编译并训练它。编译和训练是在一个单独的函数中完成的，接下来我们将详细讲解。由于我们希望设置学习率，因此必须手动初始化 Adam 优化器。我们使用均方误差（MSE）损失函数作为指标，保存具有最佳验证损失的模型，并记录训练和验证损失，以确保模型在收敛。

```py
def compile_and_train(model, X_train, y_train, X_val, y_val, epochs=100, learning_rate=0.001):
    # Instantiate the Adam optimiser with the desired learning rate
    optimiser = Adam(learning_rate=learning_rate)

    model.compile(optimizer=optimiser, loss='mean_squared_error', metrics=['mean_squared_error'])

    # Checkpoint to save the best model according to validation loss
    checkpoint_cb = ModelCheckpoint("best_model.h5", save_best_only=True, monitor='val_loss')

    history = model.fit(X_train, y_train, validation_data=(X_val, y_val),
                        epochs=epochs, callbacks=[checkpoint_cb], verbose=1)

    # Load and return the best model saved during training
    best_model = load_model("best_model.h5")
    return history, best_model
```

在初始化模型、编译并训练它之后，接下来的步骤是记录训练和验证损失，计算测试集的 Brier 分数，然后记录得分和使用的学习率。通常我们还会使用 `step` 参数在 `log_metric` 中记录训练和验证损失，像这样：

```py
# Log training and validation losses
for epoch in range(epochs):
 train_loss = history.history['loss'][epoch]
 val_loss = history.history['val_loss'][epoch]
 mlflow.log_metric("train_loss", train_loss, step=epoch)
 mlflow.log_metric("val_loss", val_loss, step=epoch)
```

然而，我们选择自己使用 `matplotlib` 创建训练和验证损失图，并将其记录为一个工件。

以下是绘图函数：

```py
import matplotlib.pyplot as plt

def create_and_save_plot(train_loss, val_loss, model_name, lr):
    epochs = range(1, len(train_loss) + 1)

    # Creating the plot
    plt.figure(figsize=(10, 6))
    plt.plot(epochs, train_loss, 'b', label='Training loss')
    plt.plot(epochs, val_loss, 'r', label='Validation loss')
    plt.title('Training and Validation Loss')
    plt.xlabel('Epochs')
    plt.ylabel('Loss')
    plt.legend()
    plt.grid(True)

    plt.title(f"Training and Validation Loss (M: {model_name}, LR: {lr})")

    # Save plot to a file
    plot_path = f"{model_name}_{lr}_loss_plot.png"
    plt.savefig(plot_path)
    plt.close()

    return plot_path
```

将所有内容整合起来，以下是该代码的样子：

```py
 with mlflow.start_run(run_name=run_name) as run:
 # Create model and dataset
 model = model_dict[model_name](input_shape)

 # Train model
 history, best_model = compile_and_train(model,
           X_train_scaled_df, y_train,
           X_validation_scaled_df, y_validation,
           epochs,
           lr)

 # Log training and validation loss plot as an artifact
 train_loss = history.history['loss']
 val_loss = history.history['val_loss']

 plot_path = create_and_save_plot(train_loss, val_loss, model_name, lr)
 mlflow.log_artifact(plot_path)

 # Calculate score
 brier_score = evaluate_model(best_model, X_test_scaled_df, y_test)

 # Log metrics and hyper-parameters
 mlflow.log_metric("brier", brier_score)

 # Log hyper-param
 mlflow.log_param("lr", lr)

 # Log model
 ...
```

对于每次运行，我们还记录了模型，这对后续会很有用。

实验已被运行，为每个模型创建了一个实验，并为每个实验进行了三次不同的运行，使用了不同的学习率。

# 分析结果

现在我们已经运行了一些实验，是时候分析结果了！为此，我们可以回到工作区，在那里我们可以找到新创建的实验以及多个运行。

![](../Images/716c608c377213fdf76833c2e0823194.png)

图 2 — 实验列表。图片由作者提供。

点击一个实验后，以下是我们将看到的内容：

![](../Images/b9c60a7efb769462fc52aca550c959b2.png)

图 3 — 实验界面。图片由作者提供。

在左侧，我们会看到与该实验相关的所有运行。在这种情况下，我们正在查看小模型实验。对于每次运行，都会有两个工件，即验证损失图和训练好的模型。还有关于运行的属性信息——状态和持续时间，以及记录的指标和超参数。

通过点击**查看运行列表**，在**比较运行**部分下，我们可以比较不同的运行。

![](../Images/1eece2a8179dad8814505c5fdb9f7e7e.png)

图 4 — 比较运行。图片由作者提供。

在运行列表视图中，我们可以选择希望比较的运行。在**指标比较**选项卡中，我们可以找到展示 Brier 分数与学习率关系的图表。在我们的案例中，看起来学习率越低，得分越好。我们甚至可以进一步创建更多图表，展示不同指标与其他超参数的关系（如果不同的指标和超参数已被记录的话）。

![](../Images/44a7a6f448f6cbd157dbfee2415b5bb4.png)

图 5 — 展示 Brier 分数与学习率关系的图表。图片由作者提供。

也许我们希望筛选运行——可以使用**筛选器**来完成此操作。例如，我们可以选择 Brier 分数低于 0.25 的运行。您可以根据记录的指标和参数以及运行的属性创建筛选器。

![](../Images/66491b7a477f7c74f04dd736a281a55b.png)

图 6 — 根据 Brier 得分筛选运行。图像由作者提供。

通过这样做，我们可以直观地比较不同的运行并评估哪个配置带来了最佳性能。这也可以通过代码实现 —— 这将是下一篇文章进一步探讨的内容。

使用实验 UI，我们能够直观地探索不同的实验和运行，按需进行比较和筛选，以了解哪个配置效果最佳。

# 结论

这就是我们对 Fabric 实验的探索总结！

我们不仅介绍了如何创建和设置实验，还讲解了如何运行实验并记录结果。我们还展示了如何分析结果，使用实验 UI 来比较和筛选运行。

在下一篇文章中，我们将讨论如何选择最佳模型，并展示如何部署它。敬请期待！

*原文发布于* [*https://nobledynamic.com*](https://nobledynamic.com/posts/fabric-madness-4/) *，发布时间为 2024年4月22日。*
