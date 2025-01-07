# 从零开始到深度分位数预测

> 原文：[https://towardsdatascience.com/from-scratch-to-deep-quantile-forecasting-366d84b7dd22?source=collection_archive---------5-----------------------#2024-07-16](https://towardsdatascience.com/from-scratch-to-deep-quantile-forecasting-366d84b7dd22?source=collection_archive---------5-----------------------#2024-07-16)

## 使用Tensorflow、NeuralForecast和零样本大型语言模型进行多步分位数预测的端到端实证分享。

[](https://jinhangjiang.medium.com/?source=post_page---byline--366d84b7dd22--------------------------------)[![Jinhang Jiang](../Images/35a4006e02b358f732af46a78d8b7bac.png)](https://jinhangjiang.medium.com/?source=post_page---byline--366d84b7dd22--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--366d84b7dd22--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--366d84b7dd22--------------------------------) [金航江](https://jinhangjiang.medium.com/?source=post_page---byline--366d84b7dd22--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--366d84b7dd22--------------------------------) ·11分钟阅读·2024年7月16日

--

![](../Images/d9966f81b1656b76977dac86cc86b3af.png)

图片来源：作者

# 内容

1.  简短介绍

1.  数据

1.  构建一个分位数递归预测器的玩具版本

1.  使用最先进模型进行分位数预测

1.  使用大型语言模型进行零样本分位数预测

1.  结论

# 简短介绍

分位数预测是一种统计技术，用于预测响应变量分布的不同分位数（例如，中位数或第90百分位数），提供对未来潜在结果的更全面的视角。与传统的均值预测只估计平均值不同，分位数预测使我们能够理解各种可能结果的范围和发生的可能性。

分位数预测在具有不对称损失函数或不同风险偏好的决策过程中至关重要。例如，在供应链管理中，预测需求的第90百分位数可以确保足够的库存水平，以避免短缺，而预测第10百分位数则有助于最小化过剩库存及相关成本。这种方法在金融、气象和能源等领域尤为有利，因为在这些领域中，理解分布的极端值与了解均值一样重要。

量化预测和符合性预测都处理不确定性，但它们的方法论差异显著。量化预测直接对响应变量的特定分位数进行建模，提供关于其分布的详细洞察。相反，符合性预测是一种与模型无关的技术，它围绕预测构建预测区间，保证真实值以指定的概率落入该区间。量化预测提供精确的分位数估计，而符合性预测则提供更广泛的区间保证。

量化预测的实现可以显著提升决策制定，通过提供对未来不确定性的深刻理解。这种方法使得组织能够根据不同的风险水平量身定制策略，优化资源分配，并提高运营效率。通过捕捉广泛的潜在结果，量化预测使得组织能够做出明智、数据驱动的决策，从而减轻风险并提高整体表现。

# 数据

为了演示该工作，我选择使用M4竞赛的数据作为示例。该数据采用[CC0: 公共领域](https://creativecommons.org/publicdomain/zero/1.0/)许可证，可以通过[这里](https://www.kaggle.com/datasets/yogesh94/m4-forecasting-competition-dataset)访问。数据也可以通过datasetsforecast包加载：

```py
# Install the package
pip install datasetsforecast
# Load Data
df, *_ = M4.load('./data', group='Weekly')
# Randomly select three items
df = df[df['unique_id'].isin(['W96', 'W100', 'W99'])]
# Define the start date (for example, "1970-01-04")
start_date = pd.to_datetime("1970-01-04")
# Convert 'ds' to actual week dates
df['ds'] = start_date + pd.to_timedelta(df['ds'] - 1, unit='W')
# Display the DataFrame
df.head()
```

![](../Images/b2fc47f942462df4c37eb76000b6218c.png)

图片由作者提供

原始数据包含超过300个独特的时间序列。为了演示，我随机选择了三个时间序列：W96、W99和W100，因为它们的历史长度相同。原始时间戳被掩码为整数（即1-2296），我手动将其转换回正常的日期格式，第一天为1970年1月4日。以下图为W99的预览：

![](../Images/7218697744c1bb3df13b004055f82502.png)

图片由作者提供

# 构建量化回归预测器的玩具版

首先，让我们从零开始构建一个量化预测器，以了解目标数据如何流经整个管道，以及预测是如何生成的。我从Wen等人的论文[A Multi-Horizon Quantile Recurrent Forecaster](https://arxiv.org/pdf/1711.11053)中汲取灵感。作者提出了一个多水平量化回归神经网络（MQ-RNN）框架，结合了序列到序列神经网络、量化回归和直接多水平预测，用于准确且稳健的多步时间序列预测。通过利用神经网络的表达能力、量化回归的非参数特性以及一种名为分叉序列的新训练方案，该模型能够有效处理变化的季节性、已知的未来事件以及冷启动问题，适用于大规模预测应用。

我们不能在这个简短的博客中复现所有内容，但我们可以尝试使用TensorFlow包进行部分复现作为演示。如果你对论文的实现感兴趣，可以利用一个正在进行的项目：[MQRNN](https://github.com/tianchen101/MQRNN?tab=readme-ov-file)。

首先加载必要的包并定义一些全局参数。我们将使用LSTM模型作为核心，并且需要对数据进行一些预处理，以获得滚动窗口，然后进行拟合。`input_shape`设置为(104, 1)，这意味着我们每个训练窗口使用两年的数据。在本次演示中，我们仅关注80%的置信区间，并将中位数作为点预测值，这意味着分位数 = [0.1, 0.5, 0.9]。我们将使用最后12周作为测试数据集，因此`output_steps`或预测范围为12，`cut_off_date`将是‘2013–10–13’。

```py
# Install the package
pip install tensorflow

# Load the package
from sklearn.preprocessing import StandardScaler
from datetime import datetime
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, LSTM, Dense, concatenate, Layer

# Define Global Parameters
input_shape = (104, 1)
quantiles = [0.1, 0.9]
output_steps = 12
cut_off_date = '2013-10-13'
tf.random.set_seed(20240710)
```

接下来，我们将数据转换为滚动窗口，这正是RNN模型所需的输入形状：

```py
# Preprocess The Data
def preprocess_data(df, window_size = 104, forecast_horizon = 12):
    # Ensure the dataframe is sorted by item and date

    df = df.sort_values(by=['unique_id', 'ds'])
    # List to hold processed data for each item
    X, y, unique_id, ds = [], [], [], []
    # Normalizer
    scaler = StandardScaler()
    # Iterate through each item
    for key, group in df.groupby('unique_id'):
        demand = group['y'].values.reshape(-1, 1)
        scaled_demand = scaler.fit_transform(demand)
        dates = group['ds'].values
        # Create sequences (sliding window approach)    
        for i in range(len(scaled_demand) - window_size - forecast_horizon + 1):
            X.append(scaled_demand[i:i+window_size])
            y.append(scaled_demand[i+window_size:i+window_size+forecast_horizon].flatten())
            unique_id.append(key)
            ds.append(dates[i+window_size:i+window_size+forecast_horizon])
    X = np.array(X)
    y = np.array(y)
    return X, y, unique_id, ds, scaler
```

然后我们将数据分成训练集、验证集和测试集：

```py
# Split Data
def split_data(X, y, unique_id, ds, cut_off_date):
    cut_off_date = pd.to_datetime(cut_off_date)
    val_start_date = cut_off_date - pd.Timedelta(weeks=12)
    train_idx = [i for i, date in enumerate(ds) if date[0] < val_start_date]
    val_idx = [i for i, date in enumerate(ds) if val_start_date <= date[0] < cut_off_date]
    test_idx = [i for i, date in enumerate(ds) if date[0] >= cut_off_date]

    X_train, y_train = X[train_idx], y[train_idx]
    X_val, y_val = X[val_idx], y[val_idx]
    X_test, y_test = X[test_idx], y[test_idx]

    train_unique_id = [unique_id[i] for i in train_idx]
    train_ds = [ds[i] for i in train_idx]
    val_unique_id = [unique_id[i] for i in val_idx]
    val_ds = [ds[i] for i in val_idx]
    test_unique_id = [unique_id[i] for i in test_idx]
    test_ds = [ds[i] for i in test_idx]

    return X_train, y_train, X_val, y_val, X_test, y_test, train_unique_id, train_ds, val_unique_id, val_ds, test_unique_id, test_ds
```

MQRNN的作者利用了与预测范围相关的局部上下文，这是实现时间感知和季节性映射所必需的，同时也利用了与预测范围无关的全局上下文来捕获非时间敏感信息，从而增强了学习的稳定性和生成预测的平滑度。为了构建一个类似于MQRNN的模型，我们需要编写一个分位数损失函数，并添加捕获局部上下文和全局上下文的层。我为此添加了一个注意力层，向你展示如何将注意力机制融入这样的过程：

```py
# Attention Layer
class Attention(Layer):
    def __init__(self, units):
        super(Attention, self).__init__()
        self.W1 = Dense(units)
        self.W2 = Dense(units)
        self.V = Dense(1)
    def call(self, query, values):
        hidden_with_time_axis = tf.expand_dims(query, 1)
        score = self.V(tf.nn.tanh(self.W1(values) + self.W2(hidden_with_time_axis)))
        attention_weights = tf.nn.softmax(score, axis=1)
        context_vector = attention_weights * values
        context_vector = tf.reduce_sum(context_vector, axis=1)
        return context_vector, attention_weights

# Quantile Loss Function
def quantile_loss(q, y_true, y_pred):
    e = y_true - y_pred
    return tf.reduce_mean(tf.maximum(q*e, (q-1)*e))

def combined_quantile_loss(quantiles, y_true, y_pred, output_steps):
    losses = [quantile_loss(q, y_true, y_pred[:, i*output_steps:(i+1)*output_steps]) for i, q in enumerate(quantiles)]
    return tf.reduce_mean(losses)

# Model architecture
def create_model(input_shape, quantiles, output_steps):
    inputs = Input(shape=input_shape)
    lstm1 = LSTM(256, return_sequences=True)(inputs)
    lstm_out, state_h, state_c = LSTM(256, return_sequences=True, return_state=True)(lstm1)
    context_vector, attention_weights = Attention(256)(state_h, lstm_out)
    global_context = Dense(100, activation = 'relu')(context_vector)
    forecasts = []
    for q in quantiles:
        local_context = concatenate([global_context, context_vector])
        forecast = Dense(output_steps, activation = 'linear')(local_context)
        forecasts.append(forecast)
    outputs = concatenate(forecasts, axis=1)
    model = Model(inputs, outputs)
    model.compile(optimizer='adam', loss=lambda y, f: combined_quantile_loss(quantiles, y, f, output_steps))
    return model
```

下面是绘制的预测结果：

![](../Images/4ea0f97ef17dc6a16096bdb67e253103.png)![](../Images/aa8e3a035e57c8e122e5a273c123f59e.png)![](../Images/45150e4f2fcc64899238d7a063f66b11.png)

我们还评估了每个项的SMAPE（对称平均绝对百分比误差），以及区间覆盖率（即实际值有多少被区间覆盖）。结果如下：

![](../Images/3e7cd6245fd2ac4b3a1f5ebc96c21338.png)

这个简化版本可以作为开始进行分位数预测的良好基准。此设置未配置分布式训练，且模型架构也没有为大规模预测进行优化，因此可能存在速度问题。在下一部分中，我们将研究一个包，它允许你使用最先进的深度学习模型进行分位数预测。

# 使用SOTA模型进行分位数预测

[neuralforecast](https://nixtlaverse.nixtla.io/neuralforecast/index.html)包是一个优秀的Python库，允许你使用大多数SOTA（最先进的）深度神经网络模型进行时间序列预测，如PatchTST、NBEATs、NHITS、TimeMixer等，且实现简单。在这一部分，我将以[PatchTST](https://arxiv.org/pdf/2211.14730)为例，展示如何进行分位数预测。

首先，加载必要的模块并定义PatchTST的参数。调整模型需要一些经验，并且会依赖于具体的项目。如果你有兴趣为你的数据获取潜在的最优参数，你可以查看neuralforecast中的自动模块。它们允许你使用Ray进行超参数调优，效率非常高！neuralforecast包包含了一系列基于不同采样方法的模型。使用base_window方法的模型可以让你使用MQLoss或HuberMQLoss，你可以指定你要寻找的分位数级别。在本工作中，我选择了HuberMQLoss，因为它对异常值更为稳健。

```py
# Install the package
pip install neuralforecast

# Load the package
from neuralforecast.core import NeuralForecast
from neuralforecast.models import PatchTST
from neuralforecast.losses.pytorch import HuberMQLoss, MQLoss

# Define Parameters for PatchTST
PARAMS = {'input_size': 104, 
          'h': output_steps, 
          'max_steps': 6000, 
          'encoder_layers': 4,
          'start_padding_enabled': False,
          'learning_rate': 1e-4,
          'patch_len': 52,  # Length of each patch
          'hidden_size': 256,  # Size of the hidden layers
          'n_heads': 4,  # Number of attention heads
          'res_attention': True,
          'dropout': 0.1,  # Dropout rate
          'activation': 'gelu',  # Activation function
          'dropout': 0.1,
          'attn_dropout': 0.1,
          'fc_dropout': 0.1,
          'random_seed': 20240710,
          'loss': HuberMQLoss(quantiles=[0.1, 0.5, 0.9]),
          'scaler_type': 'standard',
          'early_stop_patience_steps': 10}

# Get Training Data
train_df = df[df.ds<cut_off_date] 

# Fit and predict with PatchTST
models = [PatchTST(**PARAMS)]
nf = NeuralForecast(models=models, freq='W') 
nf.fit(df=train_df, val_size=12)
Y_hat_df = nf.predict().reset_index()
```

这是绘制的预测结果：

![](../Images/4a38d8c1bebc0c8f483c2d49256c5179.png)![](../Images/d58e7495e1d8e68bbd54a5762d0dab56.png)![](../Images/2c352b71e91e103da2630ea6ea9576ef.png)

这是指标：

![](../Images/c293307afec86c1ea8c10d27dd0e7dc7.png)

通过这个演示，你可以看到模型的实现是多么简单，以及模型性能是如何提升的。然而，如果你在想是否有更简单的方法来完成这个任务，答案是肯定的。在接下来的部分，我们将介绍一个基于T5的模型，允许你进行零-shot分位数预测。

# 零-shot分位数预测与大语言模型（LLMs）

我们一直在见证一个趋势，即NLP的进展将进一步推动时间序列预测的边界，因为预测下一个词是预测下一个时间段值的合成过程。鉴于大语言模型（LLMs）在生成任务中的快速发展，研究人员也开始考虑在数百万个时间序列上对大型模型进行预训练，使用户能够进行零-shot预测。

然而，在我们将LLMs和零-shot时间序列任务画上等号之前，我们必须回答一个问题：训练语言模型和训练时间序列模型之间有什么区别？答案是：“来自有限字典的tokens与来自无限范围的值”。亚马逊最近发布了一个名为[Chronos](https://github.com/amazon-science/chronos-forecasting?tab=readme-ov-file)的项目，它很好地解决了这个挑战并实现了大规模时间序列模型。如作者所述：“Chronos通过简单的缩放和量化实值，将时间序列分割成离散的箱子。通过这种方式，我们可以在这种‘时间序列语言’上训练现成的语言模型，无需更改模型架构。”原始论文可以在[此处](https://arxiv.org/pdf/2403.07815)找到。

目前，Chronos有多个版本可用。它可以通过[autogluon API](https://auto.gluon.ai/stable/tutorials/timeseries/forecasting-chronos.html)加载并使用，只需要几行代码。

```py
# Get Training Data and Transform
train_df = df[df.ds<cut_off_date] 
train_df_chronos = TimeSeriesDataFrame(train_df.rename(columns={'ds': 'timestamp', 'unique_id': 'item_id', 'y': 'target'}))

# Zero-shot forecast with Chronos
predictor = TimeSeriesPredictor(prediction_length=output_steps, freq='W', quantile_levels = [0.1, 0.9]).fit(
            train_df_chronos, presets="chronos_base", 
            random_seed = 20240710
        )
Y_hat_df_chronos = predictor.predict(train_df_chronos).reset_index().rename(columns={'mean': 'Chronos', 
                                                                                     '0.1': 'P10', 
                                                                                     '0.9': 'P90',
                                                                                     'timestamp': 'ds',
                                                                                     'item_id': 'unique_id'})
```

这是绘制的预测结果：

![](../Images/6d0ddda57ed01a537385312010594c39.png)![](../Images/c3a8bec841edbc3b6eaa37e050c79b9d.png)![](../Images/391aa8dbfff1f7334ef9eaa094e26e46.png)

这里是一些评估指标：

![](../Images/1e32cc1bbfbe54001b6019a2e6860001.png)

如你所见，Chronos与PatchTST相比表现得非常出色。然而，这并不意味着它已经超越了PatchTST，因为很有可能Chronos已经在M4数据上进行了训练。在他们的原始论文中，作者还评估了该模型在未曾训练过的数据集上的表现，结果显示Chronos仍然与SOTA模型的表现非常接近。

目前正在开发更多的大型时间序列模型。其中一个叫做[TimeGPT](https://docs.nixtla.io/docs/getting-started-about_timegpt)，由NIXTLA开发。这类模型的发明不仅让预测任务变得更容易、更可靠和一致，而且也是在有限历史数据的情况下对时间序列做出合理猜测的一个良好起点。

# 结论

从构建量化回归预测器的玩具版，到利用最先进的模型和零样本大语言模型，本博客展示了量化预测的强大功能和多样性。通过整合像TensorFlow的LSTM、NeuralForecast的PatchTST和Amazon的Chronos等模型，我们可以实现准确、稳健且计算高效的多步时间序列预测。量化预测不仅通过提供对未来不确定性的细致理解来增强决策支持，还帮助组织优化战略和资源分配。神经网络和零样本学习模型的进步进一步推动了这一领域的边界，使得量化预测成为现代数据驱动行业中的关键工具。

注：所有的图片、数字和表格均由作者生成。完整代码可以在这里找到：[Quantile Forecasting](https://github.com/jinhangjiang/Medium_Demo/blob/4ed201b4130c940106dd70ec7fe8a0d7b5b847a8/From%20Scratch%20to%20Deep%20Quantile%20Forecasting/Quantile_Forecast.ipynb)。
