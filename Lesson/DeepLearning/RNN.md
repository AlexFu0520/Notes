在上一章，我们探索了[卷积神经网络 (Convolutional Neural Network, CNN)](https://www.yuque.com/tully/d2l/cp2tdq0cpah4m0gg)，利用其卷积核实现的局部感知、权重共享和平移不变性，能高效处理具有局部关联性和固定空间结构的数据（特别是图像数据）。在 CNN 的监督学习任务中，为了进行有效的训练与推导，需假设所有样本都满足**独立同分布 (Independent and Identically Distributed, i.i.d.)**：每一个样本完全独立，其取值不受其他样本的影响。有助于简化参数估计，将联合概率用各样本概率乘积表示；所有样本都来自同一个未知分布。保证模型学习数据特征时的稳定性与泛化能力。但现实中相当一部分数据并不适用 i.i.d. 的假设，如：文本、音频信号、视频帧等**序列数据 (sequential data)**；气象信息、股价、销量等**时序数据 (time series data)**；描述社交与概念等关联的**网络数据 (graph data)** 等。

**循环神经网络 (Recurrent Neural Network, RNN)** 就是为序列信息而生的，引入隐藏状态捕获序列的先后依赖性，结合上下文与当前输入预测未来可能的输出。本章以文本数据为例，通过学习预处理与语言模型构建方法、汲取 RNN 的设计灵感。

在介绍深度学习领域的序列模型之前，先以从 1984 年 1 月 1日到 2025 年 1 月 15 日的富时 100 指数 (FTSE 100 IDX) 数据为例，介绍两个统计学视角下较流行的序列模型。


<img src="https://cdn.nlark.com/yuque/0/2025/png/932482/1736936084646-f81d4eb6-c860-4dcf-a0f9-f6c04b4e6c9f.png" width="800" title="" crop="0,0,1,1" id="u699720ae" class="ne-image">


设富时 100 指数图的横轴用离散的正整数表示时间$t \in \mathbb{Z}^{+}$ $，纵轴为价格$ $y$ 。需根据已有的历史数据，设计一个统计学模型，预测给定的时间 $t$ 位置股价的条件概率分布：
$$y_{t} \sim P (y_{t} \mid y_{t - 1}, \ldots, y_{1})$$

若考虑全序列的联合概率分布，由链式法则有：

$\begin{align}P(y_1, \ldots, y_T)&= P(y_1) \cdot P(y_2|y_1) \cdot P(y_3|y_2, y_1) \cdot \ldots \cdot P(y_T|y_{T-1}, \ldots, y_1)\\&=\prod_{t=1}^{T} P (y_{t} \mid y_{t - 1}, \ldots, y_{1})\end{align}$

+ 虽然在纯数学的角度，概率分布可反向写作 $P(y_1, \ldots, y_T) = \prod_{t=T}^1 P (y_t \mid y_{t+1}, \ldots, y_T)$ ，但在实际问题中，序列通常是从过去指向未来的矢量。于是，从因果关系的角度，默认前向进行解释和预测；
+ 股价 $y_t$ 与之前所有时间步的历史状态 $y_{t - 1}, \ldots, y_{1}$ 有关；
+ 不适合用[回归模型](https://www.yuque.com/tully/d2l/gx7xgwt9alvybb4l)预测序列数据，因为时间步越大，所需历史状态的数据量也越庞大，模型低效。通常引入新的假设求上述条件概率的近似解。  
#  自回归模型

**自回归 (AutoRegressive, AR) 模型**假设股价 $y_t$ 仅依赖于从时间 $t$ 开始回溯的 $\tau$ 个有限时间步的值，则股价的条件概率可近似表示为：
$$y_t \sim P(y_t \mid y_{t-1}, y_{t-2}, \ldots, y_{t-\tau})$$
  
此时，$y_t$ 的自回归模型预测值为：
$$\begin{align}y_t&= \phi_1 y_{t-1} + \phi_2 y_{t-2} + \ldots + \phi_\tau y_{t-\tau} + \epsilon_t \\&= \sum_{i=1}^{\tau} \phi_i y_{t-i} + \epsilon_t\end{align}$$
其中：
+ $\tau$ ：自回归模型的阶数（历史窗口长度），表示将$ \tau $个最近的历史数据纳入对当前时间步的预测；
+ $\phi_i$ ：自回归模型的参数，表示第 $i$ 个历史数据对当前值的权重；
+ $\epsilon_t$ ：暂不能用自回归模型解释的随机误差。彼此独立，$\epsilon_t \sim N(0, \sigma^2)$。

这里列举 3 种常用的确定模型的阶数 $\tau$ 的方法：

1. 若已积累足够的领域知识时，可直接根据数据特性手动设置 $\tau$ 值；
2. 用可视化的方式观察自相关函数 (AutoCorrelation Function, ACF) 图和偏自相关函数 (Partial Autocorrelation Function, PACF) 的截断点，选择合适的 $\tau$ 值；
3. 使用赤池信息准则 (Akaike Information Criterion, AIC) 或贝叶斯信息准则 (Bayesian Information Criterion, BIC) 选择合适的 $\tau$ 值。

随后用最小二乘法 (Ordinary Least Squares, OLS) 估计模型最优的参数$ \phi_i $：

  

$ \arg \min_{\phi_i} \sum_{t=\tau+1}^{T} \left(y_t - \sum_{i=1}^\tau \phi_i y_{t-i}\right)^2 $

  

这样，就可以使用自回归模型单步预测了。也可以递归地预测多步。

  

> 自回归模型简单、高效、可解释性强，但其容量有限，难以捕捉非线性关系，只适合较短趋势的建模。

>

  

:::tips

当[自回归模型](#Xdfti)单纯且有限的历史窗口无法有效捕捉复杂且隐式的依赖时，需要引入**隐变量**$ h_t $概括历史信息中未被直接观测到的部分，而不直接依赖历史值。

  

时间步位置的隐藏状态$ h_t $可以用函数$ g $从上一个时间步的隐藏状态$ h_{t-1} $和观测值$ y_{t-1} $映射得到：

  

$ h_t = g(h_{t-1}, y_{t-1}) $

  

于是，当前时间步的预测值$ y_t $可由函数$ f $从其隐藏状态$ h_t $映射得到。$ \epsilon_t $彼此独立，$ \epsilon_t \sim N(0, \sigma^2) $：

  

$ y_t = f(h_t) + \epsilon_t $

  

由于隐藏状态不可见，无法用最小二乘法得到最优参数的解析解，需用极大似然估计法等方法迭代拟合模型参数与隐藏状态。

  

:::

  

# <font style="background-color:#81BBF8;"> </font> 马尔可夫模型

实际上，用最近的一段历史（而不是全部历史）估计近似概率分布的思想满足了**马尔可夫模型 (Markov Model)** 成立的条件。即，当前状态$ x_t $仅依赖于最近的$ \tau $个状态，而与更早的历史状态无关。

  

当$ \tau = 1 $时，一阶马尔可夫模型中$ y_t $的概率分布：

  

$ P(y_t \mid y_{t-1}) $

  

当$ y_{t-1} $已知时，可直接估计$ P(y_{t + 1} \mid y_{t - 1}) $：

  

$ \begin{aligned}

P(y_{t+1} \mid y_{t-1})

&= \frac{P(y_{t+1},\,y_{t-1})}{P(y_{t-1})}

\quad && \text{\scriptsize[条件概率公式]}\\

&= \frac{\sum_{y_t} P(y_{t+1},\,y_t,\,y_{t-1})}{P(y_{t-1})}

\quad && \text{\scriptsize[对 $y_t$ 进行求和（边缘化）]}\\

&= \frac{\sum_{y_t} P\bigl(y_{t+1}\mid y_t,\;y_{t-1}\bigr)\,P(y_t,\,y_{t-1})}{P(y_{t-1})}

\quad && \text{\scriptsize[联合概率分解]}\\

&= \frac{\sum_{y_t} P\bigl(y_{t+1}\mid y_t\bigr)\,P(y_t\mid y_{t-1})\,P(y_{t-1})}{P(y_{t-1})}

\quad && \text{\scriptsize[一阶马尔可夫及二元分解]}\\

&= \sum_{y_t} P\bigl(y_{t+1}\mid y_t\bigr)\,P(y_t\mid y_{t-1})

\quad && \text{\scriptsize[抵消 $P(y_{t-1})$，完成推导]}

\end{aligned} $

  

由此推广，可用动态规划的方式一步步计算多个历史范围的概率分布：

  

$ P(y_{t+k} \mid y_{t})

= \sum_{y_{t+1}} \sum_{y_{t+2}} \cdots \sum_{y_{t+k-1}}

\prod_{j=t}^{t+k-1} P\bigl(y_{j+1}\mid y_j\bigr) $

  

# <font style="background-color:#81BBF8;"> </font> 实践

## 模拟数据生成

首先在正弦函数$ \sin(0.01 \cdot t) $上添加噪声$ \epsilon \sim N(0, 0.2^2) $模拟一段序列数据（设历史窗口$ \tau $大小为 4）：

  

```python

import torch

from matplotlib import pyplot as plt

  

sample_size = 1000

tau = 4 # 历史窗口大小

  

time_steps = torch.arange(start=1, end=sample_size + tau + 1, dtype=torch.float)

values = torch.sin(time_steps * 0.01) + torch.normal(mean=0.0, std=0.2, size=(sample_size + tau,))

  

plt.figure(figsize=(10, 5))

plt.plot(time_steps, values, label='Sine Wave with Noise')

plt.xlabel('time_steps')

plt.ylabel('values')

plt.legend()

plt.grid()

plt.show()

```

  

<img src="https://cdn.nlark.com/yuque/0/2025/png/932482/1737026057701-97c06200-9130-403f-b184-a6fc80d389a3.png" width="640" title="" crop="0,0,1,1" id="ucf1364cb" class="ne-image">

  

将`values`序列转换为用于监督模型的特征-标签对的形式：

  

```python

import torch

  

sample_size = 1000

tau = 4 # 历史窗口大小

  

time_steps = torch.arange(start=1, end=sample_size + tau + 1, dtype=torch.float)

values = torch.sin(time_steps * 0.01) + torch.normal(mean=0.0, std=0.2, size=(sample_size + tau,))

  

features = torch.stack([values[i:i + tau] for i in range(sample_size)], dim=0)

labels = values[tau:]

  

print(f'{features.shape = }')

print(f'{labels.shape = }')

```

  

```plain

features.shape = torch.Size([1000, 4])

labels.shape = torch.Size([1000])

```

  

## 模型训练

构建一个具有两个全连接层的 MLP，模型训练如下：

  

```python

import torch

from torch import nn, Tensor, optim

from torch.utils.data import DataLoader, TensorDataset

  
  

def generate_data_from_sin(mean: float, std: float, size: int, tau: int) -> tuple[Tensor, Tensor]:

time_steps = torch.arange(start=1, end=size + tau + 1, dtype=torch.float)

values = torch.sin(time_steps * 0.01) + torch.normal(mean, std, size=(size + tau,))

  

return time_steps, values

  
  

def get_features_and_labels(values: Tensor, size: int, tau: int) -> tuple[Tensor, Tensor]:

features = torch.stack([values[i:i + tau] for i in range(size)], dim=0)

labels = values[tau:]

  

return features, labels

  
  

def get_dataloader(features: Tensor, labels: Tensor, train_size: int, batch_size: int) -> tuple[DataLoader[tuple[Tensor, ...]], DataLoader[tuple[Tensor, ...]]]:

train_dataset = TensorDataset(features[:train_size], labels[:train_size])

valid_dataset = TensorDataset(features[train_size:], labels[train_size:])

  

return DataLoader(train_dataset, batch_size, shuffle=True), DataLoader(valid_dataset, batch_size)

  
  

sample_size = 1000

tau = 4

epochs = 5

lr = 0.01

  

# 模型、损失函数、优化器

net = nn.Sequential(

nn.Linear(4, 10), nn.ReLU(),

nn.Linear(10, 1)

)

loss_fn = nn.MSELoss()

optimizer = optim.Adam(net.parameters(), lr)

  

# 数据生成

time_steps, values = generate_data_from_sin(mean=0.0, std=0.2, size=sample_size, tau=tau)

features, labels = get_features_and_labels(values=values, size=sample_size, tau=tau)

train_loader, valid_loader = get_dataloader(features, labels, train_size=int(0.8 * sample_size), batch_size=16)

  

# 训练与评估

for epoch in range(epochs):

net.train()

train_loss_accu = 0

for batch_features, batch_labels in train_loader:

optimizer.zero_grad()

predictions = net(batch_features)

loss = loss_fn(predictions.squeeze(), batch_labels)

loss.backward()

optimizer.step()

train_loss_accu += loss.item()

  

net.eval()

valid_loss_accu = 0

with torch.no_grad():

for batch_features, batch_labels in valid_loader:

predictions = net(batch_features)

loss = loss_fn(predictions.squeeze(), batch_labels)

valid_loss_accu += loss.item()

  

train_loss = train_loss_accu / len(train_loader)

valid_loss = valid_loss_accu / len(valid_loader)

print(f"第 {epoch + 1}/{epochs} 轮，训练损失：{train_loss :.4f}，测试损失：{valid_loss :.4f}")

```

  

```plain

第 1/5 轮，训练损失：0.1081，测试损失：0.0414

第 2/5 轮，训练损失：0.0537，测试损失：0.0419

第 3/5 轮，训练损失：0.0522，测试损失：0.0417

第 4/5 轮，训练损失：0.0524，测试损失：0.0390

第 5/5 轮，训练损失：0.0512，测试损失：0.0404

```

  

## 模型评估

获得以上训练好的模型`net`后，可传入多组（这里是每组 4 个）历史值以预测下一个值——**单步预测 (one-step-ahead prediction)**。或者从一组历史值开始，每次（k 次）将预测到的值追加为最新的历史值、弹出超过窗口大小的最旧的历史值进行**多步预测 (k-step-ahead prediction)**。预测结果的可视化展示如下：

  

```python

import torch

from matplotlib import pyplot as plt

from torch import nn, Tensor, optim

from torch.utils.data import DataLoader, TensorDataset

  
  

def generate_data_from_sin(mean: float, std: float, size: int, tau: int) -> tuple[Tensor, Tensor]:

time_steps = torch.arange(start=1, end=size + tau + 1, dtype=torch.float)

values = torch.sin(time_steps * 0.01) + torch.normal(mean, std, size=(size + tau,))

  

return time_steps, values

  
  

def get_features_and_labels(values: Tensor, size: int, tau: int) -> tuple[Tensor, Tensor]:

features = torch.stack([values[i:i + tau] for i in range(size)], dim=0)

labels = values[tau:]

  

return features, labels

  
  

def get_dataloader(features: Tensor, labels: Tensor, train_size: int, batch_size: int) -> tuple[DataLoader[tuple[Tensor, ...]], DataLoader[tuple[Tensor, ...]]]:

train_dataset = TensorDataset(features[:train_size], labels[:train_size])

valid_dataset = TensorDataset(features[train_size:], labels[train_size:])

  

return DataLoader(train_dataset, batch_size, shuffle=True), DataLoader(valid_dataset, batch_size)

  
  

def multi_step_predict(features: Tensor, step_begin: int, step_num: int) -> Tensor:

data = features[step_begin]

result = []

for i in range(step_num):

predicted = net(data).detach()

result.append(predicted.squeeze())

data = torch.cat((data[1:], predicted), dim=0)

return torch.stack(result)

  
  

sample_size = 1000

tau = 4

epochs = 5

lr = 0.01

  

# 模型、损失函数、优化器

net = nn.Sequential(

nn.Linear(4, 10), nn.ReLU(),

nn.Linear(10, 1)

)

loss_fn = nn.MSELoss()

optimizer = optim.Adam(net.parameters(), lr)

  

# 数据生成

time_steps, values = generate_data_from_sin(mean=0.0, std=0.2, size=sample_size, tau=tau)

features, labels = get_features_and_labels(values=values, size=sample_size, tau=tau)

train_loader, valid_loader = get_dataloader(features, labels, train_size=int(0.8 * sample_size), batch_size=16)

  

# 训练与评估

for epoch in range(epochs):

net.train()

train_loss_accu = 0

for batch_features, batch_labels in train_loader:

optimizer.zero_grad()

predictions = net(batch_features)

loss = loss_fn(predictions.squeeze(), batch_labels)

loss.backward()

optimizer.step()

train_loss_accu += loss.item()

  

net.eval()

valid_loss_accu = 0

with torch.no_grad():

for batch_features, batch_labels in valid_loader:

predictions = net(batch_features)

loss = loss_fn(predictions.squeeze(), batch_labels)

valid_loss_accu += loss.item()

  

train_loss = train_loss_accu / len(train_loader)

valid_loss = valid_loss_accu / len(valid_loader)

print(f"第 {epoch + 1}/{epochs} 轮，训练损失：{train_loss :.4f}，测试损失：{valid_loss :.4f}")

  

# 单步预测与多步预测

step001_predi = net(features).detach().squeeze()

step500_predi = multi_step_predict(features, step_begin=400, step_num=500)

  

# 可视化

plt.figure(figsize=(10, 5))

plt.plot(time_steps, values, label='Sine Wave with Noise', lw=0.8, ls='-', color='#2E7CEE')

plt.plot(time_steps[:sample_size], step001_predi, label='Predict 001 step(s)', lw=0.8, ls='-.', color='#FCC526')

plt.plot(time_steps[400:400 + 500], step500_predi, label='Predict 500 step(s)', lw=0.8, ls='-.', color='#E53E31')

plt.xlabel('time_steps')

plt.ylabel('values')

plt.legend()

plt.grid()

plt.show()

```

  

<img src="https://cdn.nlark.com/yuque/0/2025/png/932482/1737099489905-9d9a9e66-cb3c-4d38-bd6d-a34dd51c7ccf.png" width="640" title="" crop="0,0,1,1" id="ud0827d5d" class="ne-image">

  

<font style="color:rgba(252,197,38,1);">黄色</font>的单步预测结果较为理想，但<font style="color:rgba(229,62,49,1);">红色</font>的多步预测就没有这么令人满意了。**随着预测步骤的向前推进，预测结果逐渐偏离真实值并最终衰减到一个常数。这是由于误差累积导致的。**我们需要更好的设计改进这一点。