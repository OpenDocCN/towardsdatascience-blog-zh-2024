# 解码时间：揭开LSTM与N-BEATS在精确时间序列预测中的力量

> 原文：[https://towardsdatascience.com/decoding-time-unraveling-the-power-of-lstm-vs-n-beats-for-accurate-time-series-forecasting-ca5fdd20dbc?source=collection_archive---------11-----------------------#2024-04-19](https://towardsdatascience.com/decoding-time-unraveling-the-power-of-lstm-vs-n-beats-for-accurate-time-series-forecasting-ca5fdd20dbc?source=collection_archive---------11-----------------------#2024-04-19)

![](../Images/7f89a7d4b8fac9fdb276d5798b65164e.png)

图片来源：[Aron Visuals](https://unsplash.com/@aronvisuals?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/selective-focus-photo-of-brown-and-blue-hourglass-on-stones-BXOXnQ26B7o?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 比较两个深度学习模型在短期和长期的表现

[](https://medium.com/@fkarvoun?source=post_page---byline--ca5fdd20dbc--------------------------------)[![Frida Karvouni](../Images/49aad19f6bdd7ffdc68c212722079c6f.png)](https://medium.com/@fkarvoun?source=post_page---byline--ca5fdd20dbc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ca5fdd20dbc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ca5fdd20dbc--------------------------------) [Frida Karvouni](https://medium.com/@fkarvoun?source=post_page---byline--ca5fdd20dbc--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ca5fdd20dbc--------------------------------) ·6分钟阅读·2024年4月19日

--

时间序列预测在各个领域中扮演着至关重要的角色，通过预测未来的趋势提供了重要的决策支持。本次探索聚焦于两个著名的深度学习模型，深入分析它们各自的优势与局限。

**LSTM**（长短期记忆）作为RNN的一个特殊变种，在捕捉具有长期依赖关系的序列数据模式方面表现突出。它通过有效地解决梯度消失问题，增强了传统的RNN，能够建模更长时间的依赖关系。LSTM通过引入记忆单元和门控机制（包括输入门、输出门和遗忘门）来实现这一点，能够选择性地保留或遗忘信息。LSTM模型的一个固有限制是，预测的时间范围必须与训练时使用的输入序列的长度相匹配。

![](../Images/c70ad5ef5d4fc304d3824aaa7beb19b1.png)

LSTM架构基于RNN，**[1]**

**N-BEATS**（时间序列的神经基础扩展分析）代表了一种非递归架构，以其准确预测多个时间序列的能力而闻名。该模型由不同的构建模块组成，采用层次化结构，其中每个模块专门负责预测特定的时间范围。 “回溯”模块深入历史时间范围，而“预测”模块则专注于预测未来的时间段，这些时间段的长度可能不同。

![](../Images/111c8a4dd4deeb5c0524e170e7a55906.png)

N-BEATS的回溯窗口和预测时间范围可能具有不同的大小**[2]**

![](../Images/2bbb57d8b26c59c0414a6df3c507d82e.png)

N-BEATS架构**[2]**

![](../Images/dd8de56ec473f89ccbec96981f92f6fb.png)

N-BEATS架构中的每个堆栈（左）和每个堆栈内的每个块（右）**[2]**

N-BEATS在建模季节性方面表现突出，它通过将时间序列分解为趋势和季节性，类似于STL（基于LOESS的季节性趋势分解）方法。这种分解方法使得模型能够分别捕捉短期波动（季节性）和长期趋势。它利用快速傅里叶变换（Fast Fourier Transform）有效建模季节性，这是时间序列分析中的一个关键组成部分。N-BEATS被设计为能够适应不同类型的季节性模式，包括规律性和不规律性模式。

# 实际示例

为了更好地理解上述理论概念，让我们通过应用两种模型来深入探讨一个实际的例子。首先，我们将生成具有上升趋势和季节性模式的每日频率合成数据。

```py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Generate synthetic time series data
def generate_synthetic_data():
    # Create a time range with daily frequency
    start_time = pd.Timestamp("1990-01-01 00:00:00")
    end_time = pd.Timestamp("2023-12-31 23:59:00")
    time = pd.date_range(start=start_time, end=end_time, freq='D')

    # Weekly seasonality and ascending trend
    weekly_seasonality = 10 * np.sin(2 * np.pi * 
                         np.arange(len(time)) / (7 * 24 * 60))

    ascending_trend = 0.01 * np.arange(len(time))

    # Combine the components to generate synthetic data
    uplift = 100
    x = weekly_seasonality + ascending_trend + uplift

    noise_level = 5
    noise = white_noise(len(time), noise_level, seed=42)
    x += noise
    return time, x

def white_noise(length, noise_level=1, seed=None):
    rnd = np.random.RandomState(seed)
    return rnd.randn(length) * noise_level

time_series_data = generate_synthetic_data_minute()
data = pd.DataFrame({'ds': time_series_data[0], 'y': time_series_data[1]})
data= data.set_index("ds")

# Plot the generated synthetic data
plt.plot(data["y"])
plt.title("Synthetic Time Series Data")
plt.show()
```

模拟数据如下所示：

![](../Images/918cbd7e9a9a1c60336653338cc19873.png)

合成时间序列数据

数据集展示了季节性、上升趋势和噪声，使得大多数模型很难进行准确的预测。这一难点源于许多模型专门识别长期或短期的模式，通常是各自独立的。因此，准确预测这类数据需要一个能够同时捕捉这两种模式的模型。为此，数据已被缩放，以确保与LSTM模型兼容。

```py
data = data.reset_index()
train_data = data[data.ds<='2022-01-01']
test_data = data[data.ds>'2022-01-01']

# Normalize the data
scaler = MinMaxScaler()
train_data['x'] = scaler.fit_transform(train_data[['x']])
test_data['x'] = scaler.transform(test_data[['x']])

# Create sequences for the LSTM model
sequence_length = 10
train_sequences = []
test_sequences = []

for i in range(len(train_data) - sequence_length):
    train_sequences.append(train_data['x'].iloc[i:i+sequence_length].values)

for i in range(len(test_data) - sequence_length):
    test_sequences.append(test_data['x'].iloc[i:i+sequence_length].values)

train_sequences = np.array(train_sequences)
test_sequences = np.array(test_sequences)

# Prepare train and test targets
train_targets = train_data['x'].iloc[sequence_length:].values
test_targets = test_data['x'].iloc[sequence_length:].values

import time
start_time = time.time()

# Create and train an LSTM model
model = Sequential()
model.add(LSTM(50, activation='relu', input_shape=(sequence_length, 1)))
model.add(Dense(1))
model.compile(optimizer='adam', loss='mean_squared_error')
model.fit(train_sequences.reshape(-1, sequence_length, 1), train_targets, 
epochs=5, batch_size=32)

# Make predictions
test_predictions = model.predict(
                   test_sequences.reshape(-1, sequence_length, 1))

print(time.time() - start_time)

# Inverse transform the predictions to the original scale
test_predictions = scaler.inverse_transform(test_predictions).flatten()
test_targets = scaler.inverse_transform(test_targets.reshape(-1, 1))
```

该数据集的模型训练用了23秒。测试集和预测覆盖了2022年1月1日之后的时间段。以下是预测的值：

```py
# Plot the original data and LSTM predictions
plt.figure(figsize=(10, 6))
plt.plot(test_data['ds'].iloc[sequence_length:], test_targets, 
label="Actual Data", linestyle='-')
plt.plot(test_data['ds'].iloc[sequence_length:], test_predictions, 
label="LSTM Predictions", linestyle='--')
plt.xlabel("Time")
plt.ylabel("Value")
plt.legend()
plt.grid(False)
plt.show()
```

![](../Images/2fc38115dc852fb4ab1814b60fe30c4a.png)

LSTM模型对测试集的预测

![](../Images/5349877e2859c072600d8185b0a7aad9.png)

LSTM模型对整个数据集的预测

预测结果似乎有效地捕捉到了上升趋势和季节性，这令人乐观。然而，预测中似乎存在相当大的噪声。此外，随着预测范围的延长，预测值与测试数据之间的偏差变得明显。

现在，让我们评估加权绝对百分比误差（WAPE）：

```py
test_predictions_df = pd.DataFrame(test_predictions, columns = ["LSTM"])
test_targets_df = pd.DataFrame(test_targets, columns = ["actuals"])
predictions = pd.concat([test_predictions_df, test_targets_df], axis=1)

wape = (predictions['actuals'] - predictions['LSTM']).abs().sum() 
        / predictions['actuals'].sum()

print(wape * 100)
```

```py
WAPE = 1.89%
```

结果确实很有前景。

接下来，让我们探索如何将N-BEATS模型拟合到相同的数据集，并检查其性能。

```py
data = generate_data_specific_column(col_name = "y")

# create column unique_id specifically for N-BEATS model with only 1.0
data['unique_id'] = 1.0

data = data.reset_index()
train_data = data[data.ds<='2022-01-01']
test_data = data[data.ds>'2022-01-01']

from neuralforecast.models import NBEATS, NHITS
from neuralforecast import NeuralForecast
import time
start_time = time.time()

horizon = len(test_data)

models = [NBEATS(input_size=2 * horizon, h=horizon, max_steps=50)]

nf = NeuralForecast(models=models, freq='D')
nf.fit(df=train_data) # default optimizer is MAE
test_predictions = nf.predict().reset_index()

print(time.time() - start_time)
```

N-BEATS模型在83秒内完成了训练，虽然速度较快，但仍然比LSTM模型慢。

现在，让我们来看看N-BEATS模型生成的预测。

```py
# Plot predictions
predictions = test_data.merge(test_predictions, how='left', 
on=['unique_id', 'ds'])

plt.figure(figsize=(10, 6))
plt.plot(predictions['ds'], predictions['y'], label="Actual Data", 
linestyle='-')
plt.plot(predictions['ds'], predictions['NBEATS'], 
label="NBEATS Predictions", linestyle='--')
plt.xlabel("Time")
plt.ylabel("Demand per Minute")
plt.legend()
plt.grid(False)
plt.show()
```

![](../Images/5793207e79e22f0a125334e38873eef2.png)

N-BEATS在测试集中的预测

![](../Images/d8f833e2eee729cefde99d30afc8e5d8.png)

N-BEATS在整个数据集中的预测

```py
wape = (predictions['y'] - predictions['NBEATS']).abs().sum() 
        / predictions['y'].sum()

print(wape * 100)
```

```py
WAPE = 1.80%
```

两个模型根据所选指标展示了高准确性。有趣的是，N-BEATS生成的预测似乎并不会随着预测时间段的结束而恶化。这表明N-BEATS可能特别适合需要长期预测的项目。

# 总结

N-BEATS和LSTM为预测领域带来了独特的优势。N-BEATS在捕捉长期预测中的多样模式方面表现突出，同时具有较高的可解释性。而LSTM则是一个快速、稳定且复杂的神经网络机制，能够实现准确的预测。

# 参考文献

*除非另有说明，所有图像均由作者生成

**[1]** Van Houdt, Greg & Mosquera, Carlos & Nápoles, Gonzalo. (2020). 长短期记忆模型综述。人工智能评论。53。10.1007/s10462–020–09838–1.

**[2]** Binte Habib, Adria. (2022). N-BEATS架构工作流程的详细解释。10.13140/RG.2.2.36379.34083.

[](https://medium.com/mlearning-ai/building-a-neural-network-zoo-from-scratch-the-long-short-term-memory-network-1cec5cf31b7?source=post_page-----ca5fdd20dbc--------------------------------) [## 从零开始构建神经网络动物园：长短期记忆网络

### 长短期记忆（LSTM）网络是最著名的递归神经网络类型之一。最初…

medium.com](https://medium.com/mlearning-ai/building-a-neural-network-zoo-from-scratch-the-long-short-term-memory-network-1cec5cf31b7?source=post_page-----ca5fdd20dbc--------------------------------) [](https://forecastegy.com/posts/multiple-time-series-forecasting-nbeats-python/?source=post_page-----ca5fdd20dbc--------------------------------) [## 使用N-BEATS进行Python中的多时间序列预测

### 想象一下拥有一个强大的预测解决方案，能够处理多时间序列数据，而不依赖于复杂的…

[forecastegy.com](https://forecastegy.com/posts/multiple-time-series-forecasting-nbeats-python/?source=post_page-----ca5fdd20dbc--------------------------------)

[Neural forecast — Nixtla](https://nixtlaverse.nixtla.io/neuralforecast/index.html)

[如何为LSTM缩放数据](https://machinelearningmastery.com/how-to-scale-data-for-long-short-term-memory-networks-in-python/)
