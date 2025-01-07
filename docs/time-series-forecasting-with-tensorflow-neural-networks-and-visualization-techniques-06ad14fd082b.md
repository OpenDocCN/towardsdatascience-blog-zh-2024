# 使用TensorFlow进行时间序列预测和通过可视化技术进行验证期外预测

> 原文：[https://towardsdatascience.com/time-series-forecasting-with-tensorflow-neural-networks-and-visualization-techniques-06ad14fd082b?source=collection_archive---------11-----------------------#2024-02-24](https://towardsdatascience.com/time-series-forecasting-with-tensorflow-neural-networks-and-visualization-techniques-06ad14fd082b?source=collection_archive---------11-----------------------#2024-02-24)

## 如何将预测扩展到验证期之外

[](https://medium.com/@paulamaranon?source=post_page---byline--06ad14fd082b--------------------------------)[![Paula Maranon](../Images/40b163c740105e0d7506eea3335aa268.png)](https://medium.com/@paulamaranon?source=post_page---byline--06ad14fd082b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--06ad14fd082b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--06ad14fd082b--------------------------------) [Paula Maranon](https://medium.com/@paulamaranon?source=post_page---byline--06ad14fd082b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--06ad14fd082b--------------------------------) ·阅读时长8分钟·2024年2月24日

--

![](../Images/947426607d6525714baa868ba36c92e6.png)

作者提供的图片

在本文中，我将带领你通过使用TensorFlow构建时间序列模型的过程。TensorFlow是一个强大的框架，用于构建和训练神经网络。我将展示多种用于时间序列预测的神经网络架构，从简单的模型如SimpleRNN到更复杂的模型如LSTM。此外，我还将介绍我用来进行验证期外预测的高级可视化技术。

# 设置环境

我使用了以下库：TensorFlow与Keras用于构建神经网络，Matplotlib用于可视化，NumPy用于数值计算，Scikit-Learn用于数据预处理。

```py
import numpy as np
import tensorflow as tf
from matplotlib import pyplot as plt
from sklearn.preprocessing import MinMaxScaler
```

# 数据准备

数据准备对任何机器学习模型的成功至关重要。在这一部分，我将执行几个步骤，为训练和验证准备数据。

## 分离数据和时间步

第一步是将时间步从实际数据中分离出来。

**对于短时间序列数据（存储在数组中的数据）：**我们可以使用‘np.arange()’创建一个时间步数组：

```py
#For short time series data, data stored in an array, I'll do the following:
dummy_data = np.array([1, 2, 3,...])
time_step = np.arange(len(dummy_data))
```

**对于存储在文件中的大型数据集（例如CSV文件）：**我们可以从文件中读取数据及其对应的时间步：

```py
#For larger datasets stored in files, such as CSV files
import csv

time_step = []
data = []

with open("file.txt", "r", encoding="utf-8") as f:
    csv_reader = csv.reader(f, delimiter=",")

    # Skip the header
    next(csv_reader)

    # Skip lines with NUL characters
    lines = (line for line in csv_reader if "\0" not in line)

    # Iterate through non-null lines
    for line in lines:
        # Assuming the first column is the date and the second column is the number
        time_step.append(datetime.strptime(line[0], "%Y-%m-%d"))
        data.append(float(line[1]))
```

## 将数据划分为训练集和验证集

在获取时间步和数据后，我们将其划分为训练集和验证集，以训练和评估模型的性能。

```py
# Determine the split point between training and validation data
split_time = 

# Split time steps into training and validation sets
time_train = time_step[:split_time]
time_valid = time_step[split_time:]

# Split data into training and validation sets
x_train = dummy_data[:split_time]
x_valid = dummy_data[split_time:]

# Use Min-Max scaling
# Initialize MinMaxScaler
scaler = MinMaxScaler()

# Reshape and scale the training data
x_train_scaled = scaler.fit_transform(np.array(x_train).reshape(-1, 1)).flatten()

# Scale the validation data using the same scaler
x_valid_scaled = scaler.transform(np.array(x_valid).reshape(-1, 1)).flatten()
```

## 生成窗口数据集

之后，我创建了一个函数，用于生成用于训练和验证的窗口化数据集。每个窗口由固定数量的数据点组成。例如，使用窗口大小为 4 时，我会使用每个窗口中的最后四个数据点来预测下一个数据点。

窗口化的数据集生成了两维的数据批次（批次大小和时间戳）。

```py
def windowed_dataset(series, window_size):
    # Create a TensorFlow dataset from the input series
    dataset = tf.data.Dataset.from_tensor_slices(series)

    # Window the dataset into fixed-size windows with a specified window_size,
    # shifting the window by 1 at each step, and drop any remaining data that
    # doesn't fit into a complete window
    dataset = dataset.window(window_size, shift=1, drop_remainder=True)

    # Flatten the dataset of windows into individual windows and batch them
    dataset = dataset.flat_map(lambda window: window.batch(window_size))

    # Map each window to a tuple where the first element contains all but the last
    # element of the window and the second element contains the last element of the window
    dataset = dataset.map(lambda window: (window[:-1], window[-1]))

    # Batch the dataset with a batch size of 1 and prefetch it for improved performance
    return dataset.batch(1).prefetch(1)

# Window size
window_size = 

# Create windowed dataset for training
dataset_train = windowed_dataset(x_train_scaled, window_size)

# Create windowed dataset for validation
dataset_valid = windowed_dataset(x_valid_scaled, window_size)
```

在遵循这些步骤后，数据已被适当准备好以便导入模型中。

# 定义模型

Keras 提供了许多可以用于时间序列预测的模型。我将简要描述我使用过的一些模型，从较简单的结构开始，逐渐增加复杂性。值得注意的是，这些结构仅仅是示例，单元数量和层数需要根据所使用的数据集进行微调。

## 简单的递归神经网络

RNN 是用于处理数据序列的神经网络，能够保留来自早期时间步的信息。然而，它们可能无法记住长序列中的信息。

在每个时间步，不同批次的输入数据被送入 RNN 单元。每个时间步的 RNN 单元输出不仅依赖于当前的输入批次，还依赖于单元的前一个状态，而前一个状态捕捉了早期时间步的信息。

![](../Images/bab7657e545017b31a99ad53b7947bd7.png)

图片由作者提供

这是一个简单的 RNN 模型示例，包含两个递归层和一个最终输出层。在使用 RNN 时，输入数据需要进行调整，因为 RNN 通常期望输入数据为 3D 张量。[1]

```py
model = tf.keras.models.Sequential([
  tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=-1),
                      input_shape=[window_size]),
  tf.keras.layers.SimpleRNN(units, return_sequences=True),
  tf.keras.layers.SimpleRNN(units),
  tf.keras.layers.Dense(1)
  ])
```

## 长短期记忆网络

LSTM 网络是一种递归神经网络，以能够在多个时间步内保留信息而闻名。LSTM 通过结合一个记忆单元，使信息能够从一个单元传递到另一个单元，并在网络内从一个时间步传递到另一个时间步来实现这一点。

![](../Images/e4eb866a9f2085b0f0ded5f9d7eec462.png)

图片由作者提供

此外，LSTM 可以是双向的，允许它们不仅分析输入数据的正向方向，还能分析反向方向。

这里是一个包含两个双向 LSTM 层的 LSTM 模型示例。根据我的经验，在 LSTM 中使用多个层通常比单层模型表现更好，因为它能够同时捕捉低层次和高层次的特征。[2]

```py
 model = tf.keras.models.Sequential([ 
        tf.keras.layers.Lambda(lambda x: tf.expand_dims(x, axis=-1),input_shape=[None]),
        tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(units, return_sequences=True)),
        tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(units)),
        tf.keras.layers.Dense(1)
    ]) 
```

# 调整学习率

这些架构需要根据每个数据集进行调整。然而，适合我的方法是先调整学习率，然后再尝试其他层数和单元数的组合。

请查看这篇文章中的代码，了解如何调整学习率和层数：[通过调整学习率和隐藏层单元选择优化神经网络性能](https://medium.com/@paulamaranon/optimizing-neural-network-performance-through-learning-rate-tuning-and-hidden-layer-unit-selection-ed6acc00f9a6)。

重要提示：我希望很久以前就知道的一个非常有用的建议：除了实验不同的层数和单元数外，我建议你在训练模型时始终调整权重。有关更多详细信息和代码，请查看我名为[Achieving Reproducibility in Neural Network Predictions](https://medium.com/@paulamaranon/achieving-reproducibility-in-neural-network-predictions-71bf6bfbadf6)的文章。

# 在验证数据集上评估模型

为了能够可视化模型的性能和预测结果，让我们编译并训练该模型。

```py
# Compile the model
model.compile(loss="mse", optimizer=tf.keras.optimizers.SGD(),metrics=["mae"])

# Train the model
history = model.fit(dataset_train, epochs=, validation_data=dataset_valid)

# Evaluate the model
evaluation_result = model.evaluate(dataset_valid)
print("Validation Loss:", evaluation_result)
```

在时间序列预测中，通常使用均方误差（MSE）或平均绝对误差（MAE）来验证模型的性能。与MSE不同，MAE不对误差进行平方处理，而是使用误差的绝对值。这种方法不会对较大的误差进行过度惩罚，因此适用于那些需要平等对待所有误差的场景。

# 可视化训练和验证损失

现在让我们创建两个图表，展示训练和验证数据的损失（Loss）和平均绝对误差（MAE）曲线。

```py
 # Plot training and validation loss over epochs
plt.figure(figsize=(10, 6))

# Plot training loss
plt.subplot(1, 2, 1)
plt.plot(history.history['loss'], label='Training Loss', color='blue')
plt.plot(history.history['val_loss'], label='Validation Loss', color='red')
plt.title('Training and Validation Loss')
plt.xlabel('Epoch')
plt.ylabel('Loss')
plt.legend()

# Plot training and validation MAE over epochs
plt.subplot(1, 2, 2)
plt.plot(history.history['mae'], label='Training MAE', color='blue')
plt.plot(history.history['val_mae'], label='Validation MAE', color='red')
plt.title('Training and Validation MAE')
plt.xlabel('Epoch')
plt.ylabel('MAE')
plt.legend()

plt.tight_layout()
plt.show()
```

这是可以得到的图表示例：

![](../Images/d084eaafc808a0e2290818a0ae08743c.png)

图片由作者提供

# 在验证数据集上进行预测

现在我已经训练了模型，并使用MSE指标验证了其性能，是时候将模型应用于验证数据集进行预测了。

以下是对验证数据集进行预测的代码。此外，我还包括了如何打印验证期之外的预测结果，并将其与实际数据在每个时间步骤进行对比。

```py
# Predict on the validation dataset
num_predictions_beyond_validation = 2
validation_predictions = []

# Use the last window_size from the training set for predictions
current_window = x_train_scaled[-window_size:]

# Adjust time steps for validation predictions
validation_time_steps = np.arange(len(x_valid_scaled))

for time in range(len(validation_time_steps) + num_predictions_beyond_validation):

    # Predict the next values using the model on the validation dataset
    predicted_value_scaled = model.predict(np.array(current_window[-window_size:]).reshape(1, -1))[0]
    # Append the predicted value to the list of predictions
    validation_predictions.append(predicted_value_scaled)

    # Print the actual and predicted values during validation along with the time step
    if time < len(x_valid_scaled):
        actual_value_scaled = x_valid_scaled[time]
        # Denormalize the actual value
        actual_value_denormalized = scaler.inverse_transform(np.array(actual_value_scaled).reshape(1, -1)).flatten()
        # Denormalize the predicted value
        predicted_value_denormalized = scaler.inverse_transform(np.array(predicted_value_scaled).reshape(1, -1)).flatten()
        print(f'Time: {time_valid[time]}, Actual: {actual_value_denormalized}, Predicted: {predicted_value_denormalized}')

    # Update the current window for the next iteration using the true value from the validation set
    if time < len(x_valid_scaled):
        current_window = np.append(current_window, x_valid_scaled[time])[1:]

    else:
        # Print the predicted value beyond validation along with the time step
        # Denormalize the predicted value beyond validation
        predicted_value_denormalized = scaler.inverse_transform(np.array(predicted_value_scaled).reshape(1, -1)).flatten()
        print(f'Time: {time_valid[-1] + time - len(x_valid_scaled) + 1}, Predicted (Beyond Validation): {predicted_value_denormalized}')
        current_window = np.append(current_window, predicted_value_scaled)[-window_size:]
```

你还可以在图表中可视化验证期之外的预测结果。

```py
 # Plot the original data, training data, validation data, and predictions
plt.figure(figsize=(10, 6))

# Plot original data in blue
plt.plot(time_step, dummy_data, label='Original Data', marker='o', linestyle='-', color='black')

# Plot training data in green
plt.plot(time_step[:split_time], dummy_data[:split_time], label='Training Data', color='blue')

# Plot validation data in orange
plt.plot(time_valid, dummy_data[split_time:], label='Validation Data', color='red')

# Denormalize validation predictions
validation_predictions_denormalized = scaler.inverse_transform(np.array(validation_predictions).reshape(-1, 1)).flatten()

# Plot predictions (Validation)
plt.plot(time_valid, validation_predictions_denormalized[:len(time_valid)], label='Predictions (Validation)', color='orange')

# Highlight the last few predictions beyond the validation set in red
last_predictions_beyond_validation_denormalized = scaler.inverse_transform(np.array(validation_predictions[-num_predictions_beyond_validation:]).reshape(-1, 1)).flatten()
time_last_predictions_beyond_validation = np.arange(split_time + len(time_valid), split_time + len(time_valid) + num_predictions_beyond_validation)
plt.scatter(time_last_predictions_beyond_validation, last_predictions_beyond_validation_denormalized, color='red', marker='X', label='Last Predictions Beyond Validation')

plt.legend()
plt.title('Original Data, Training Data, Validation Data, and Predictions')
plt.xlabel('Time Step')
plt.ylabel('Values')
plt.show()
```

这是一个基于虚拟数据集的示例，展示了上面代码生成的图像。

![](../Images/601fa0cb259e516bb75d316fdbfe1ed1.png)

图片由作者提供

# 结论

可视化时间序列数据和模型预测是理解模型性能的基础。本文中，我解决了一个我曾多次遇到的问题：如何在验证期之外进行时间序列预测并可视化，这在事后检查模型在实际数据上的表现时可能很有用。

# 参考文献

[1]SimpleRNN:[https://www.tensorflow.org/api_docs/python/tf/keras/layers/SimpleRNN#call_arguments](https://www.tensorflow.org/api_docs/python/tf/keras/layers/SimpleRNN)

[2]LSTM:[https://www.tensorflow.org/api_docs/python/tf/keras/layers/LSTM](https://www.tensorflow.org/api_docs/python/tf/keras/layers/LSTM)
