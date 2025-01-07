# 神经网络的消融测试：补偿性伪装

> 原文：[https://towardsdatascience.com/ablation-testing-neural-networks-the-compensatory-masquerade-ba27d0037a88?source=collection_archive---------5-----------------------#2024-01-07](https://towardsdatascience.com/ablation-testing-neural-networks-the-compensatory-masquerade-ba27d0037a88?source=collection_archive---------5-----------------------#2024-01-07)

## 破坏性地测试神经网络和其他机器学习架构的部分，目的是使其变得更加健壮

[](https://squoraishee.medium.com/?source=post_page---byline--ba27d0037a88--------------------------------)[![Shafik Quoraishee](../Images/439d3502b98af4d994a8fab33b8bb428.png)](https://squoraishee.medium.com/?source=post_page---byline--ba27d0037a88--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ba27d0037a88--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ba27d0037a88--------------------------------) [Shafik Quoraishee](https://squoraishee.medium.com/?source=post_page---byline--ba27d0037a88--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ba27d0037a88--------------------------------) ·8分钟阅读·2024年1月7日

--

![](../Images/17facce6e4c4b29da7fcbbde246cf1b7.png)

（图片由作者使用DALL-E生成）。有趣的是AI如何看待它自己的大脑。

类似于一个人的智力如何经受压力测试，人工神经网络也可以通过进行所谓的受控消融测试，来评估它们对不同类型干扰的鲁棒性。

在我们深入探讨[消融测试](https://en.wikipedia.org/wiki/Ablation_(artificial_intelligence))之前，让我们先谈谈一种许多学习机器学习和人工智能应用的人可能熟悉的“破坏性进化”技术：[正则化](https://en.wikipedia.org/wiki/Regularization_(mathematics))

## **正则化**

正则化是消融的一个非常著名的例子，或者说是选择性地破坏/停用神经网络的部分并重新训练它，使其成为一个更强大的分类器。

通过一种叫做[Dropout](https://en.wikipedia.org/wiki/Dilution_(neural_networks))的过程，神经元可以以受控的方式被停用，这使得之前由现在已停用的神经元处理的工作能够被附近的活跃神经元接管。

在自然界中，大脑实际上也能由于神经可塑性而经历类似的现象。如果一个人遭受脑损伤，在某些情况下，附近的神经元和大脑结构可以重新组织，帮助接管已死亡脑组织的一些功能。

或者，像是如果一个人失去了某种感官，比如视觉，通常其他感官会变得更强，以弥补其丧失的能力。

这也被称为[补偿性伪装](https://www.britannica.com/science/compensatory-masquerade)。

![](../Images/38cb217c01889fc9be7aaf32bad46223.png)

左侧是一个全连接神经网络，右侧是随机丢弃版本。在许多情况下，这些网络实际上可能表现得相对较好（图片由作者提供）

## **切除测试**

虽然正则化是神经网络和其他人工智能架构中用来通过人工的“[神经可塑性](https://en.wikipedia.org/wiki/Neuroplasticity)”来帮助神经网络更好地训练的技术，但有时我们希望仅仅对神经网络做类似的处理，看看它在准确性方面，在去激活的情况下会有什么表现。

我们可能会出于几个其他原因这么做：

+   **识别神经网络的关键部分：** 神经网络的某些部分可能比其他部分承担更为重要的工作。为了优化网络的资源使用和训练时间，我们可以选择性地去除“较弱的学习者”。

+   **减少神经网络的复杂度：** 有时神经网络可能变得非常庞大，尤其是在深度多层感知器（[多层感知器](https://en.wikipedia.org/wiki/Multilayer_perceptron)）的情况下。这可能使得从输入到输出映射它们的行为变得困难。通过选择性地关闭网络的某些部分，我们可以潜在地识别出过度复杂的区域，并去除冗余——简化我们的架构。

+   **容错性：** 在实时系统中，系统的某些部分可能会失败。神经网络的部分也是如此，因此依赖其输出的系统也会受到影响。我们可以借助切除研究来确定，如果摧毁神经网络的某些部分，是否会导致系统的预测或生成能力受损。

## **切除测试的类型**

实际上有很多不同种类的切除测试，下面我们将讨论三种特定类型的测试：

+   神经元切除

+   功能性切除

+   输入切除

快速说明，切除测试的效果会根据你测试的网络和数据本身而有所不同。一个切除测试可能会显示在特定数据集上网络某一部分的弱点，而在另一个切除测试中可能会显示神经网络的另一部分的弱点。这就是为什么在一个真正强健的切除测试系统中，你需要进行多种测试来准确地了解人工神经网络（[人工神经网络](https://en.wikipedia.org/wiki/Artificial_neural_network)）的弱点。

**神经元切除**

这是我们将要进行的第一种切除测试，它最简单且容易观察其效果并扩展。我们将简单地从神经网络中去除不同比例的神经元。

对于我们的实验，我们设置了一个简单的ANN来测试随机字符预测的准确性，使用我们老朋友的[MNIST数据集](https://en.wikipedia.org/wiki/MNIST_database)。

![](../Images/193196488e4f29b9aee031723542889b.png)

来自MNIST数据集的数字数据快照（作者提供）

这是我写的代码，作为一个简单的ANN测试工具，测试数字分类的准确性。

```py
import tensorflow as tf
from tensorflow.keras.datasets import mnist
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Dropout, Flatten
from tensorflow.keras.optimizers import Adam
import matplotlib.pyplot as plt

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

# Create the ANN Model
def create_model(dropout_rate=0.0):
    model = Sequential([
        Flatten(input_shape=(28, 28)),
        Dense(128, activation='relu'),
        Dropout(dropout_rate),
        Dense(10, activation='softmax')
    ])
    model.compile(optimizer=Adam(),
                  loss='sparse_categorical_crossentropy',
                  metrics=['accuracy'])
    return model

# Run the ablation study: Dropout percentages of neurons
dropout_rates = [0.0, 0.2, 0.4, 0.6, 0.8]
accuracies = []

for rate in dropout_rates:
    model = create_model(dropout_rate=rate)
    model.fit(x_train, y_train, epochs=5, validation_split=0.2, verbose=0)
    loss, accuracy = model.evaluate(x_test, y_test, verbose=0)
    accuracies.append(accuracy)

plt.plot(dropout_rates, accuracies, marker='o')
plt.title('Accuracy vs Dropout Rate')
plt.xlabel('Dropout Rate')
plt.ylabel('Accuracy')
plt.grid(True)
plt.show()
```

所以如果我们运行上述代码，我们会看到如下结果：逐步停用我们128节点MLP的一定比例。

在这个简单的示例中，结果相当有趣，正如你所看到的，丢弃80%的神经元几乎不影响准确性，这意味着去除多余的神经元肯定是我们在构建这个网络时可以考虑的一种优化方法。

![](../Images/8804627f95e4e423a2bdd894aef0979b.png)

为dropout切除测试生成的图表（作者提供）

**功能性切除**

对于功能性切除，我们将神经元的激活函数更改为不同的曲线，具有不同程度的非线性。我们使用的最后一个函数是一条直线，完全破坏了模型的非线性特征。

因为非线性模型在定义上比线性模型更复杂，而激活函数的目的是在分类上引入非线性效应，所以可以得出这样的推理：

*“如果我们可以通过使用线性函数代替非线性函数，并且仍然能得到很好的分类结果，那么或许我们可以简化我们的架构，降低其成本”*

**注意：** 除了正则化之外，你会注意到，某些类型的切除测试，如功能性切除，与[超参数调优](https://en.wikipedia.org/wiki/Hyperparameter_optimization)有相似之处。它们是相似的，但切除测试更多是指改变神经网络架构的部分（例如神经元、层等），而超参数调优则是指改变模型的结构参数。两者的目标都是优化。

```py
# Activation function ablation
activation_functions = ['relu', 'sigmoid', 'tanh', 'linear']
activation_ablation_accuracies = []
for activation in activation_functions:
    model = create_model(activation=activation)
    model.fit(x_train, y_train, epochs=5, validation_split=0.2, verbose=0)
    loss, accuracy = model.evaluate(x_test, y_test, verbose=0)
    activation_ablation_accuracies.append(accuracy)
```

当我们运行上述代码时，我们得到以下激活函数与准确度的比较。

![](../Images/a428a18957cb11484ff2823a55c3a840.png)

为功能性切除测试生成的图表（作者提供）

所以，确实看起来某种形式的非线性对分类非常重要，其中“[ReLU](https://en.wikipedia.org/wiki/Rectifier_(neural_networks))”和[双曲正切](https://en.wikipedia.org/wiki/Hyperbolic_functions)的非线性效果最为有效。这是有道理的，因为众所周知，数字分类最好作为一个非线性任务来处理。

**特征切除**

我们还可以从分类中移除特征，看看这对预测器的准确性有何影响。

通常在做机器学习或数据科学项目之前，我们通常会进行探索性数据分析（EDA）和特征选择，以确定哪些特征可能对我们的分类问题很重要。

但是有时我们可以观察到有趣的效果，特别是在处理神秘的神经网络时，通过在消融研究中移除特征并观察其对分类的影响。使用以下代码，我们可以按4列一组地从字母中移除像素列。

显然，消融特征有多种方式，除了按列扭曲字符之外，还可以通过其他方式。但我们可以从这个简单的例子开始并观察其效果。

```py
# Input feature ablation
input_ablation_accuracies = []
for i in range(0, 28, 4):  # Remove columns of pixels groups of 4
    x_train_ablated = np.copy(x_train)
    x_test_ablated = np.copy(x_test)
    x_train_ablated[:, :, i:min(i+4, 28)] = 0
    x_test_ablated[:, :, i:min(i+4, 28)] = 0

    model = create_model()
    model.fit(x_train_ablated, y_train, epochs=5, validation_split=0.2, verbose=0)
    loss, accuracy = model.evaluate(x_test_ablated, y_test, verbose=0)
    input_ablation_accuracies.append(accuracy)
```

在运行上述特征消融代码后，我们看到：

![](../Images/07a74f18d9fe0131f2c7bfc8f29ebea7.png)

为4列输入特征消融测试生成的图表（图片来自作者）

有趣的是，当我们移除第8到第12列时，准确度略有下降，然后再次上升。这表明平均而言，更“敏感”的字符几何形状位于这些中心列中，但其他列，特别是接近开始和结束的列，可能会被移除以优化效果。

这是对每次移除7列的相同测试，以及相应的列。通过可视化实际的扭曲字符数据，我们可以更好地理解结果，因为我们看到移除前几列对结果的影响较小，这是因为它们大多只是填充！

![](../Images/5fe4ea84cf30abc94770488eff6c5111.png)

为4列像素移除结果生成的图表（图片来自作者）

另一个有趣的消融研究例子是测试不同类型的噪声配置文件。下面是我写的代码，用于使用上述ANN模型渐进地为图像加噪声。

```py
# Ablation study with noise
noise_levels = [0, 0.1, 0.2, 0.3, 0.4, 0.5]
noise_ablation_accuracies = []

plt.figure(figsize=(12, 6))

for i, noise_level in enumerate(noise_levels):
    x_train_noisy = x_train + noise_level * np.random.normal(0, 1, x_train.shape)
    x_test_noisy = x_test + noise_level * np.random.normal(0, 1, x_test.shape)
    x_train_noisy = np.clip(x_train_noisy, 0, 1)
    x_test_noisy = np.clip(x_test_noisy, 0, 1)

    model = create_model()
    model.fit(x_train_noisy, y_train, epochs=5, validation_split=0.2, verbose=0)
    loss, accuracy = model.evaluate(x_test_noisy, y_test, verbose=0)
    noise_ablation_accuracies.append(accuracy)

    # Plot noisy test images
    plt.subplot(2, len(noise_levels), i + 1)
    for j in range(5):  # Display first 5 images
        plt.imshow(x_test_noisy[j], cmap='gray')
        plt.axis('off')
    plt.title(f'Noise Level: {noise_level}')
```

我们已经为网络在增加强度的[高斯噪声](https://en.wikipedia.org/wiki/Gaussian_noise#:~:text=In%20signal%20processing%20theory%2C%20Gaussian,can%20take%20are%20Gaussian%2Ddistributed.)环境中的稳健性创建了一个消融研究。请注意，随着噪声水平的增加，预测准确度预期并显著下降。

![](../Images/0f3ccd17fd6e9b8bc4ad14b5df6b461d.png)

为渐进噪声结果生成的图表（图片来自作者）

类似这样的情况让我们知道，可能需要增加神经网络的能力和复杂性来进行补偿。还要记住，消融研究可以相互结合进行，在不同类型的噪声与不同类型的失真结合的情况下进行。

**结论**

消融研究对优化和测试神经网络非常重要。我们在这篇文章中演示了一个小例子，但有无数种方法可以在不同和更复杂的网络架构上运行这些研究。如果你有任何想法，非常希望得到反馈，甚至可以将其纳入你的文章中。感谢阅读！
