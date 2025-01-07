# 机器学习算法作为空间之间的映射：从支持向量机（SVM）到流形学习

> 原文：[https://towardsdatascience.com/machine-learning-algorithms-as-a-mapping-between-spaces-from-svms-to-manifold-learning-b1dfe1046e4f?source=collection_archive---------3-----------------------#2024-02-07](https://towardsdatascience.com/machine-learning-algorithms-as-a-mapping-between-spaces-from-svms-to-manifold-learning-b1dfe1046e4f?source=collection_archive---------3-----------------------#2024-02-07)

## 探索支持向量机（SVM）、自编码器和流形学习（等距映射）算法中空间之间映射的美

[](https://medium.com/@salih.salih?source=post_page---byline--b1dfe1046e4f--------------------------------)[![Salih Salih](../Images/220f3c5363989d94c5593eca7ff72c67.png)](https://medium.com/@salih.salih?source=post_page---byline--b1dfe1046e4f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b1dfe1046e4f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b1dfe1046e4f--------------------------------) [Salih Salih](https://medium.com/@salih.salih?source=post_page---byline--b1dfe1046e4f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1dfe1046e4f--------------------------------) ·12分钟阅读·2024年2月7日

--

![](../Images/85f97ec0087c788a144bd88bd57fa8ae.png)

图片来源：Evgeni Tcherkasski，Unsplash

# 介绍

在机器学习中，理解算法如何处理、解释和分类数据，通常依赖于“空间”这一概念。在这种情况下，空间是一个数学结构，数据点根据其特征在空间中定位。空间中的每个维度代表数据的一个特定属性或特征，使得算法能够在结构化的表示中进行导航。

## 特征空间与输入空间

这段旅程始于特征空间或输入空间，在这个空间中，每个数据点都是一个向量，代表数据集中的一个实例。简化一下，想象一张图像，其中每个像素是这个空间中的一个维度。空间的复杂性和维度取决于特征的数量和性质。在高维空间中工作，对数据从业者来说既可以是令人愉快的，也可以是令人沮丧的。

## 低维空间中的挑战

在低维空间中，并非所有的数据关系或模式都能容易地被识别。线性可分性，即通过简单的线性边界将不同类别分开，通常是无法实现的。这一局限性在复杂数据集中尤为明显，因为特征之间的交互产生了非线性模式，而这些模式无法通过简单的线性模型捕捉。

在本文中，我们将从不同空间之间的映射和交互的角度来探索机器学习算法。我们将以支持向量机（SVM）作为一个简单的例子开始，然后介绍自编码器，最后讨论流形学习和等距映射（Isomaps）。

**请注意，本文中的代码示例仅用于演示，可能没有经过优化。我鼓励你修改、改进并尝试使用不同的数据集运行代码，以加深理解并获得更多的见解。**

# 支持向量机

支持向量机（SVM）是已知的机器学习算法，在数据分类方面表现优异。正如我们在开头提到的：

> 在低维空间中，线性可分性通常是不可能的，这意味着用简单的线性边界划分类别非常困难。

SVM通过将数据转换为更高维的空间，克服了这一困难，从而使得数据更容易分离和分类。为了说明这一点，下面的代码生成了在原始空间中明显无法线性分离的合成数据。

```py
import numpy as np
import matplotlib.pyplot as plt
from sklearn.datasets import make_circles

# Generate synthetic data that is not linearly separable
X, y = make_circles(n_samples=100, factor=0.5, noise=0.05)

# Visualize the data
plt.scatter(X[:, 0], X[:, 1], c=y, cmap=plt.cm.coolwarm, edgecolors='k')
plt.xlabel('Feature 1')
plt.ylabel('Feature 2')
plt.title('Original 2D Data')
plt.show()
```

![](../Images/b7e601d9fede398564838a0b0ec32f82.png)

图片来源：作者

支持向量机（SVM）通过空间之间的映射来分离不同的类别。它们将数据从低维空间提升到高维空间。在这个新空间中，SVM找到最优的超平面，这是一个决策边界，用于分离类别。这就像是在二维图中找到完美的直线来划分群体，但在更复杂的多维宇宙中进行。

在提供的数据中，一类数据靠近原点，而另一类数据远离原点。让我们通过一个典型的例子来理解，当数据被转换到更高维度时，如何变得可分。

我们将每个二维点(x, y)转换为三维点(x, y, z)，其中z = x² + y²。这个变换根据二维空间中到原点的平方距离，增加了一个新的第三维度。在三维空间中，距离原点更远的点将在空间中更高，因为它们的平方距离较大。

```py
from mpl_toolkits.mplot3d import Axes3D

# Transform the 2D data to 3D for visualization
Z = X[:, 0]**2 + X[:, 1]**2  # Use squared distance from the origin as the third dimension

# Visualize the 3D data
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(X[:, 0], X[:, 1], Z, c=y, cmap=plt.cm.coolwarm)

# Set labels
ax.set_xlabel('Feature 1')
ax.set_ylabel('Feature 2')
ax.set_zlabel('Transformed Feature')

# Set the viewpoint
elevation_angle = 15  # Adjust this to change the up/down angle
azimuth_angle = 45  # Adjust this to rotate the plot
ax.view_init(elev=elevation_angle, azim=azimuth_angle)

plt.show()
```

![](../Images/2a47086d619d1e375b7a139452c47163.png)

图片来源：作者

从上面的输出可以看出，在此变换之后，我们的数据变得可以通过一个二维超平面线性可分。

另一个例子是药物剂量的有效性。只有当剂量处于某个特定范围内时，病人才会康复。剂量过低或过高都是无效的。这个场景自然会创建一个不可线性分离的数据集，因此它是展示多项式核如何帮助解决问题的一个好例子。

```py
# Train the SVM model on the 2D data
svc = SVC(kernel='linear', C=1.0)
svc.fit(X, y)

# Create a function to plot decision boundary
def plot_svc_decision_function(model, plot_support=True):
    """Plot the decision function for a 2D SVC"""
    ax = plt.gca()
    xlim = ax.get_xlim()
    ylim = ax.get_ylim()

    # create grid to evaluate model
    xx = np.linspace(xlim[0], xlim[1], 30)
    yy = np.linspace(ylim[0], ylim[1], 30)
    YY, XX = np.meshgrid(yy, xx)
    xy = np.vstack([XX.ravel(), YY.ravel()]).T
    Z = model.decision_function(xy).reshape(XX.shape)

    # plot decision boundary and margins
    ax.contour(XX, YY, Z, colors='k', levels=[-1, 0, 1], alpha=0.5,
               linestyles=['--', '-', '--'])
    # plot support vectors
    if plot_support:
        ax.scatter(model.support_vectors_[:, 0], model.support_vectors_[:, 1],
                   s=300, linewidth=1, facecolors='none', edgecolors='k')

# Adjust the figure size for better visualization
plt.figure(figsize=(8, 5))

# Scatter plot for original dosage points
plt.scatter(dosages, np.zeros_like(dosages), c=y, cmap='bwr', marker='s', s=50, label='Original Dosages')

# Scatter plot for dosage squared points
plt.scatter(dosages, squared_dosages, c=y, cmap='bwr', marker='^', s=50, label='Squared Dosages')

# Calling the function to plot the SVM decision boundary
plot_svc_decision_function(svc)

# Expanding the limits to ensure all points are visible
plt.xlim(min(dosages) - 1, max(dosages) + 1)
plt.ylim(min(squared_dosages) - 10, max(squared_dosages) + 10)

# Adding labels, title and legend
plt.xlabel('Dosage')
plt.ylabel('Dosage Squared')
plt.title('SVM Decision Boundary with Original and Squared Dosages')
plt.legend()

# Display the plot
plt.show()
```

![](../Images/5f9d86f94306290f327605ff250d64af.png)

图片来源：作者

> 在上面的两个例子中，我们利用了关于数据的知识。例如，在第一个例子中，我们知道有两个类别：一个接近原点，另一个远离原点。这正是算法通过训练和微调所做的——它找到一个适合的空间，在这个空间中数据可以被线性分开。

这里的一个优点是，支持向量机（SVM）并不会将数据映射到更高维度，因为这样在计算上会非常复杂。相反，它们使用点积计算数据之间的关系，就好像数据存在于更高维度中一样。这被称为“核技巧”。我将在另一篇文章中解释SVM的核函数。

# 自编码器

自编码器是非常令人惊叹且美丽的架构，深深吸引了我的想象力。它们在各个领域有广泛的应用，利用了多种类型的自编码器。

它们基本上由**编码器**和**解码器**组成，编码器接收输入并对其进行编码/压缩，这是一个将高维空间转化为更加紧凑、低维空间的过程。真正有趣的是，解码器如何接着利用这种压缩后的表示，并在高维空间中重建原始数据。自然的疑问是：如何从显著降低维度的表示中恢复回原始空间呢？

让我们考虑一张分辨率为720x720像素的高清图片。存储和传输这张图片需要大量的内存和带宽。自编码器通过将图片压缩到一个较低维度的空间中，例如一个32x32的表示（称为“瓶颈”），来解决这个问题。此时编码器的工作已完成。接下来由解码器接手，尝试从这个压缩的形式中重建原始图片。

这个过程类似于在像WhatsApp这样的平台上分享图片。图片被编码成较低质量以进行传输，然后在接收端解码。原始图片和接收图片之间的质量差异被称为“重建误差”，这是自编码器中常见的现象。

在自编码器中，我们可以将其视为三个空间之间的相互作用：

1.  输入空间。

1.  潜在表示空间（瓶颈）。

1.  输出空间。

这里的美妙之处在于，我们可以将自编码器视为在这三个空间中操作的东西。它利用潜在空间来去除输入空间中的任何噪声或不必要的信息，从而生成一个非常紧凑的表示，包含关于输入空间的核心信息。它通过尝试在输出空间中镜像输入空间来实现这一点，从而减少两者之间的差异或重建误差。

## 卷积自编码器：将复杂性编码为简洁

以下代码展示了一个卷积自编码器的示例，这是一种在处理图像时表现良好的自编码器。我们将使用流行的MNIST数据集[LeCun, Y., Cortes, C., & Burges, C.J. (1998). 手写数字的MNIST数据库。 [来源于TensorFlow](https://www.tensorflow.org/datasets/catalog/mnist)，CC BY 4.0]，该数据集包含28x28像素的灰度手写数字图像。编码器在将数据的维度从784个元素压缩到更小的形式时起着至关重要的作用。然后，解码器试图从这种较低维度的表示中重建原始的高维数据。然而，这种重建并不完美，部分信息丢失。自编码器通过学习优先处理数据中最重要的特征来克服这一挑战。

```py
import tensorflow as tf
from tensorflow.keras import layers
from tensorflow.keras.datasets import mnist
import matplotlib.pyplot as plt

# Load MNIST dataset
(x_train, _), (x_test, _) = mnist.load_data()
x_train = x_train.astype('float32') / 255.
x_test = x_test.astype('float32') / 255.
x_train = x_train.reshape((len(x_train), 28, 28, 1))
x_test = x_test.reshape((len(x_test), 28, 28, 1))

# Define the convolutional autoencoder architecture
input_img = layers.Input(shape=(28, 28, 1))

# Encoder
x = layers.Conv2D(16, (3, 3), activation='relu', padding='same')(input_img)
x = layers.MaxPooling2D((2, 2), padding='same')(x)
x = layers.Conv2D(8, (3, 3), activation='relu', padding='same')(x)
encoded = layers.MaxPooling2D((2, 2), padding='same')(x)

# Decoder
x = layers.Conv2D(8, (3, 3), activation='relu', padding='same')(encoded)
x = layers.UpSampling2D((2, 2))(x)
x = layers.Conv2D(16, (3, 3), activation='relu', padding='same')(x)
x = layers.UpSampling2D((2, 2))(x)
decoded = layers.Conv2D(1, (3, 3), activation='sigmoid', padding='same')(x)

# Autoencoder model
autoencoder = tf.keras.Model(input_img, decoded)
autoencoder.compile(optimizer='adam', loss='binary_crossentropy')
autoencoder.fit(x_train, x_train, epochs=10, batch_size=64, validation_data=(x_test, x_test))

# Visualization
# Sample images
sample_images = x_test[:8]
# Reconstruct images
reconstructed_images = autoencoder.predict(sample_images)

# Plot original images and reconstructed images
fig, axes = plt.subplots(nrows=2, ncols=8, figsize=(14, 4))
for i in range(8):
    axes[0, i].imshow(sample_images[i].squeeze(), cmap='gray')
    axes[0, i].set_title("Original")
    axes[0, i].axis('off')
    axes[1, i].imshow(reconstructed_images[i].squeeze(), cmap='gray')
    axes[1, i].set_title("Reconstructed")
    axes[1, i].axis('off')
plt.show()
```

![](../Images/3e836d1a5deab08a354758f18b8d0b4c.png)

图片由作者提供

上面的输出展示了自编码器的效果。它显示了成对的图像：原始数字图像及其在编码和解码后重建的结果。这个例子证明了编码器能够以更小的形式捕捉数据的本质，而解码器则能够近似原始图像，尽管在压缩过程中有些信息丢失。

现在，让我们进一步可视化学习到的潜在空间（瓶颈）。我们将使用PCA和t-SNE这两种降维技术，将压缩后的数据点显示在二维平面上。这个步骤非常重要，因为它帮助我们看到自编码器是如何在潜在空间中组织数据的，并展示了相似数字的自然聚类。我们使用PCA和t-SNE一起进行比较，看看它们的效果如何。

```py
# Encode all the test data
encoded_imgs = encoder.predict(x_test)

# Reduce dimensionality using PCA
pca = PCA(n_components=2)
pca_result = pca.fit_transform(encoded_imgs)

# Reduce dimensionality using t-SNE
tsne = TSNE(n_components=2, perplexity=30, n_iter=300)
tsne_result = tsne.fit_transform(encoded_imgs)

# Visualization using PCA
plt.figure(figsize=(20, 10))
plt.subplot(1, 2, 1)
plt.scatter(pca_result[:, 0], pca_result[:, 1], c=y_test, cmap=plt.cm.get_cmap("jet", 10))
plt.colorbar(ticks=range(10))
plt.title('PCA Visualization of Latent Space')

# Visualization using t-SNE
plt.subplot(1, 2, 2)
plt.scatter(tsne_result[:, 0], tsne_result[:, 1], c=y_test, cmap=plt.cm.get_cmap("jet", 10))
plt.colorbar(ticks=range(10))
plt.title('t-SNE Visualization of Latent Space')

plt.show()
```

![](../Images/849a0320906aa2409e41565a0f82a497.png)

图片由作者提供

比较这两个生成的图表，t-SNE在将不同类别的数字在潜在空间中分离时，比PCA更为出色（它捕捉了非线性）。它创建了明显的聚类，且不同类别之间的重叠最小。自编码器将图像压缩到较低的维度空间，但仍然捕捉到足够的信息以区分不同的数字，正如t-SNE图所示。

> 这里有一个重要的说明，t-SNE是一种非线性技术，用于可视化高维数据。它保持局部数据结构，使其在视觉上有助于识别集群和模式。然而，它通常不用于机器学习中的特征降维。

**但是这个自编码器可能学习了什么呢？**

一般来说，可以说像这样的自编码器学习基本的简单边缘和纹理，逐步学习数字的一部分，比如循环和线条以及它们如何排列，最后理解完整的数字（层次特征），在此过程中，它以紧凑的形式捕捉每个数字的独特本质。它能够猜测图像缺失的部分，并且能够识别数字书写中的常见模式。

# 流形学习：非均匀性的福音

在上一篇文章《[维度灾难：一种直观的探索](/curse-of-dimensionality-an-intuitive-exploration-1fbf155e1411)》中，我探讨了“维度灾难”这一概念，指的是在处理高维数据时遇到的问题和挑战，这使得许多机器学习算法的工作变得更加困难。

现在出现了流形学习算法，这些算法受到非均匀性祝福的驱动，即数据点在给定空间或数据集中的不均匀分布或变化。

> 流形学习的基本假设是，高维数据实际上位于高维空间内某个低维流形上或附近。这个概念基于这样的想法：尽管数据可能存在于高维空间中（因为它的测量或记录方式），但有效描述数据及其结构的内在维度要低得多。

让我们生成著名的瑞士卷数据集，并将其作为高维空间中非均匀性的示例。在其原始形式中，这个数据集看起来像是一个混乱的数据点堆积。然而，在这混乱的表面下，隐藏着秩序——一个低维结构，包含了数据的重要特征。流形学习技术，如Isomap，利用了这种非均匀性。通过将数据点从高维空间映射到低维空间，Isomap展示了瑞士卷的内在形状。它保留了原始数据的丰富性，同时揭示了底层结构——一个二维投影，捕捉了高维空间的非均匀性：

```py
import numpy as np
import matplotlib.pyplot as plt
from sklearn import manifold, datasets

# Generate a Swiss Roll dataset
X, color = datasets.make_swiss_roll(n_samples=1500)

# Apply Isomap for dimensionality reduction
iso = manifold.Isomap(n_neighbors=10, n_components=2)
X_iso = iso.fit_transform(X)

# Plot the 3D Swiss Roll
fig = plt.figure(figsize=(15, 8))

# Create a 3D subplot
ax = fig.add_subplot(121, projection='3d')
ax.scatter(X[:, 0], X[:, 1], X[:, 2], c=color, cmap=plt.cm.Spectral)

# Set the viewing angle
elevation_angle = 30  # adjust this for elevation
azimuthal_angle = 45  # adjust this for azimuthal angle
ax.view_init(elev=elevation_angle, azim=azimuthal_angle)

ax.set_title("Original Swiss Roll")

# Plot the 2D projection after Isomap
ax = fig.add_subplot(122)
ax.scatter(X_iso[:, 0], X_iso[:, 1], c=color, cmap=plt.cm.Spectral)
plt.axis('tight')
ax.set_title("2D projection by Isomap")

# Show the plots
plt.show()
```

![](../Images/3a4a17ae7e0217abc2d3e0597d1dec59.png)

图像来自作者

让我们看看上面的输出：

我们有两幅色彩丰富的插图。在左侧，是一个3D瑞士卷，五光十色的颜色呈螺旋状交织在一起。它展示了每个颜色过渡到下一个颜色的过程，标记着穿越瑞士卷的路径。

现在，右侧显示的是相同颜色的二维分布。尽管形状已经改变，颜色的顺序和流动仍然讲述着原始数据的相同故事。点与点之间的顺序和连接被保留，就像瑞士卷被小心地展开到平面上一样，让我们能够一次性看到整个模式。

# **结论**

本文从探讨空间的概念开始，空间是数据点根据其特征/属性位置的数学构造。我们考察了**支持向量机（SVMs）**如何利用将数据映射到高维空间的思路，来应对低维空间中**非线性可分性**的挑战。

然后我们讨论了**自编码器**，一种优雅且真正美丽的架构，它在**3个空间**之间进行映射，**输入空间**被压缩到一个更低的**潜在表示（瓶颈）**，接着是解码器发挥作用，目标是从这个较低的表示中重建原始输入，**同时最小化重建误差**。

我们还探讨了**流形学习**，以及我们从非均匀性中获得的好处，它作为一种方法，通过简化复杂的数据集而不丢失重要细节，从而克服了**维度诅咒**。

如果你看到这里，我想感谢你花时间阅读这篇文章，我希望你觉得它既有趣又有用，若有任何错误或误解，请随时指出，我非常感激你的反馈和建议。
