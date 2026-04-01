RNN 训练时，需在复用隐状态前[分离 (detach) 隐状态的梯度](https://www.yuque.com/tully/d2l/gez7tdq4dzgtvkwq#ua92bd420)，以保证[序列数据训练的数值稳定性](https://www.yuque.com/tully/d2l/gez7tdq4dzgtvkwq#WsiN2)。[一个时间步内的隐状态计算](https://www.yuque.com/tully/d2l/gez7tdq4dzgtvkwq#ay7yj)部分已详细阐述了循环神经网络的前向传播过程——隐状态在时间步**间**循环传递。

本节将进一步探讨序列模型中梯度的计算，以及 RNN 的反向传播原理与细节。
![[截屏2026-04-01 19.24.19.png]]
# <font style="background-color:#81BBF8;"> </font> RNN 中的梯度计算原理
## 长程梯度依赖的推导
### 前向传播
为了清晰地展示 RNN 中梯度的计算过程，忽略隐状态特性与更新细节后，第 $t$个时间步的计算过程为：
$$ \begin{align}h_t &= f(x_t, h_{t-1}, w_h) \\o_t &= g(h_t, w_o)\end{align} $$

其中，
+ $x_t$：第 $t$ 个时间步的输入；
+ $h_t$：第 $t$ 个时间步隐状态；
+ $o_t$：第 $t$ 个时间步的输出；
+ $w_h$ ：隐藏层权重；
+ $w_o$：输出层权重；
+ $f$：隐状态更新函数；
+ $g$：输出生成函数。

网络在各个时间步内遍历 $(x_t, h_t, o_t)$ ，由链式结构 $\{\ldots, (x_{t-1}, h_{t-1}, o_{t-1}), (x_t, h_t, o_t), \ldots\}$ 前向计算、传递信息。此时，整体的损失值 $L$ 是各时间步损失 $l$的均值：
$$ L(x_1, \ldots, x_T, y_1, \ldots, y_T, w_h, w_o) = \frac{\sum_{t=1}^T l(o_t, y_t)}{T} $$

### 反向传播
在随后的反向传播过程中，需要计算损失函数 $L$ 对所有参数的梯度：

+ **输出层权重** $w_o$ **的梯度**。输出 $o_t$ 仅与当前隐状态 $h_t$ 和 $w_o$有关，不需要沿时间步展开，计算简单：
$$ \begin{align}\frac{\partial L}{\partial w_o}&= \frac{1}{T} \sum_{t=1}^T \frac{\partial l(o_t, y_t)}{\partial w_o}  \\&= \frac{1}{T} \sum_{t=1}^T \left(\frac{\partial l(o_t, y_t)}{\partial o_t} \cdot \frac{\partial o_t}{\partial w_o}\right)  \\&= \frac{1}{T} \sum_{t=1}^T \left(\frac{\partial l(o_t, y_t)}{\partial o_t} \cdot \frac{\partial g(h_t, w_o)}{\partial w_o}\right)\end{align} $$

> 输出层权重 $w_o$ （以及其他参数）的梯度计算方法与前馈神经网络的梯度计算方法一致，无时间步的递归。
+ **隐藏层权重** $w_h$ **的梯度**：

$$ \begin{align}\frac{\partial L}{\partial w_h}&= \frac{1}{T}\sum_{t=1}^T \frac{\partial l(o_t, y_t)}{\partial w_h}  \\&= \frac{1}{T}\sum_{t=1}^T \left(\frac{\partial l(o_t, y_t)}{\partial o_t} \cdot \frac{\partial o_t}{\partial h_t} \cdot {\color{Red} \frac{\mathrm{d} h_t}{\mathrm{d} w_h}} \right) \\&= \frac{1}{T}\sum_{t=1}^T \left(\frac{\partial l(o_t, y_t)}{\partial o_t} \cdot \frac{\partial g(h_t, w_o)}{\partial h_t} \cdot {\color{Red} \frac{\mathrm{d} h_t}{\mathrm{d} w_h}} \right) \\\end{align} $$

在隐藏层权重 $w_h$ 的梯度计算的乘积累加过程中，损失函数对输出的偏导数 $\tfrac{\partial l(o_t, y_t)}{\partial o_t}$ 和输出函数对隐藏状态的偏导数 $\tfrac{\partial g(h_t, w_o)}{\partial h_t}$ 都很好计算。在数学表示上，需要用全导数 ${\color{Red} \tfrac{\mathrm{d} h_t}{\mathrm{d} w_h}}$ 表示隐藏层权重 $w_h$ 对隐状态 $h_t$ 的全部影响（梯度在时间维度上的累积效应），而隐状态与权重有递归的依赖关系（$h_t = f(x_t, h_{t-1}, w_h)$）。这里，$w_h$ 对 $h_t$ 的全部影响来自两条路径：
1. 当前时间步隐状态  $h_t$ 对权重 $w_h$ 的**直接依赖**；
2. 权重 $w_h$ 影响上一个时间步的隐状态 $h_{t-1}$ 后，**递归依赖**地影响当前时间步的隐状态 $h_t$ 。

因此，$w_h$ 对$h_t$ 的全导数 ${\color{Red} \tfrac{\mathrm{d} h_t}{\mathrm{d} w_h}}$ 表示为：

$$ \begin{align}{\color{Red} \frac{\mathrm{d} h_t}{\mathrm{d} w_h}}&= {\color{salmon} \underbrace{\frac{\partial h_t}{\partial w_h}}_{\text{直接依赖项}}} + {\color{royalblue} \underbrace{\frac{\partial h_t}{\partial h_{t-1}} \cdot \frac{\mathrm{d} h_{t-1}}{\mathrm{d} w_h}}_{\text{递归依赖项}}} \quad {\color{Gray} \text{\scriptsize （链式法则分解）}}  \\&= {\color{salmon} \frac{\partial f(x_t,h_{t-1},w_h)}{\partial w_h}} + {\color{royalblue} \frac{\partial f(x_t,h_{t-1},w_h)}{\partial h_{t-1}} \cdot \frac{\mathrm{d} h_{t-1}}{\mathrm{d} w_h}}  \\&= \frac{\partial f_t}{\partial w_h} + \frac{\partial f_t}{\partial h_{t-1}} \frac{\partial f_{t-1}}{\partial w_h} + \frac{\partial f_t}{\partial h_{t-1}} \frac{\partial f_{t-1}}{\partial h_{t-2}} \frac{\partial f_{t-2}}{\partial w_h} + \cdots + \left( \prod_{j=2}^t \frac{\partial f_j}{\partial h_{j-1}} \right) \frac{\partial f_1}{\partial w_h} \quad {\color{Gray} \scriptsize \text{（递归展开到 }t_1\text{）}}  \\&= {\color{salmon} \frac{\partial f_t}{\partial w_h}} + {\color{royalblue} \sum_{i=1}^{t-1} \left( \prod_{j=i+1}^t \frac{\partial f(x_j, h_{j-1}, w_h)}{\partial h_{j-1}}\right) \cdot \frac{\partial f(x_i, h_{i-1}, w_h)}{\partial w_h}} \quad {\color{Gray} \text{\scriptsize（递归依赖项求和）}}\end{align} $$

其中，
+ $i \in \{1, 2, \ldots, t-1\}$：梯度贡献的起始时间步索引（历史依赖）；
+ $j \in \{i+1, i+2, \ldots, t\}$：连乘项索引（传播路径）；
+ 特别地，当$t=1$ 时，$h_0$与$w_h$ 无关连乘项退化为 1：${\color{Firebrick}\frac{\mathrm{d} h_1}{\mathrm{d} w_h}} = \frac{\partial f(x_1, h_0, w_h)}{\partial w_h}$。

每个历史时间步的权重梯度贡献 $\tfrac{\partial f_i}{\partial w_h}$ 与连乘项 $\prod\ _{j=i+1}^t \tfrac{\partial f_j}{\partial h_{j-1}}$ 共同产生梯度累积效应，通过后续时间步的隐状态传播到当前时间步 $t$ 。

虽然${\color{Red} \tfrac{\mathrm{d} h_t}{\mathrm{d} w_h}}$ 在理论上满足可计算性，但完成计算的复杂度很高，为$\mathcal{O} (T^2)$ 。特别是当 $T$很长时，初始条件的微小变化将对结果产生巨大影响：当$\left| \tfrac{\partial f_j}{\partial h_{j-1}}\right| \gg 1$ 时，将诱发梯度爆炸，当 $\left| \tfrac{\partial f_j}{\partial h_{j-1}}\right| \ll 1$ ，将诱发梯度消失。这需要我们适时截断梯度计算的路径，控制依赖项的展开长度。

## 梯度计算路径的截断策略
限制梯度反向传播的时间跨度，以解决长程梯度依赖难题，减小计算代价、提高数值稳定性，需对 ${\color{Red} \tfrac{\mathrm{d} h_t}{\mathrm{d} w_h}}$ 完整梯度表达式中的求和项 $\sum\ _{i=1}^{t-1}$进行约束，这些截断策略本质上需显式或隐式地由[torch.detach()](https://www.yuque.com/tully/d2l/vmhmv5aml2byglfk#hCgzT)调用。

![[截屏2026-04-01 19.24.54.png]]
<img src="https://cdn.nlark.com/yuque/0/2025/jpeg/932482/1741352025184-a53ae3c9-80b5-4057-92d8-7a01d11494d7.jpeg" width="480" title="" crop="0,0,1,1" id="u0c027aa5" class="ne-image">

### 等距截断
等距截断策略以最近的 $\tau$ 个时间步为**固定窗口**，仅计算 $\sum\ _{i=\max(1, t-\tau)}^{t-1}$ （注意避免索引越界），使模型忽略长于 $\tau$ 个时间步的历史依赖，计算复杂度为 $\mathcal{O} (\tau T)$ 。等距截断近似法的表达式为：
$$ {\color{Red} \frac{\mathrm{d} h_t}{\mathrm{d} w_h}} \approx {\color{salmon} \frac{\partial f_t}{\partial w_h}} + \sum_{i=\max(1, t-\tau)}^{t-1} \left( \prod_{j=i+1}^t \frac{\partial f_j}{\partial h_{j-1}}\right) \cdot \frac{\partial f_i}{\partial w_h} $$

多数任务的建模本就偏向于短期依赖，等距截断策略通过固定求和的次数，显式地控制了计算范围。虽然一定程度地牺牲了理论精度，但特别适合稳定并行地处理等长序列。该策略也成为了 PyTorch、TensorFlow 等主流深度学习框架的默认实现。

### 随机截断
随机截断策略用**保留概率** $\pi_t$ 控制梯度回溯时每个时间步不被截断的可能性，同时向每个历史依赖引入能缩放各时间步梯度的**补偿权重**$\xi_t \in \{0, \pi_t^{-1}\}$ ，使计算复杂度降低至 $\mathcal{O}(\tfrac{T}{1-\pi})$ 。其中：
$$ \begin{cases}P(\xi_{t} = 0) &= 1 - \pi_{t}\\P(\xi_{t} = \pi_{t}^{-1}) &= \pi_{t}\end{cases} $$
$$ \begin{align}E[\xi_t]&= 0 \times (1-\pi_t) + \pi_{t}^{-1} \times \pi_t  \\&\equiv 1\end{align} $$

$\xi_t = \pi_t^{-1}$ 时，当前时间步的梯度能被回溯；$\xi_t = 0$ 时，梯度传播在当前时间步截断。一方面，引入的随机性既避免了模型训练时对特定历史信息的过度依赖，可动态调整梯度回溯路径，提升了模型泛化能力；另一方面，权重概率的设计也使每次梯度传播的期望值恒定，不对梯度的总贡献产生偏差。随机截断近似法的表达式为：
$$ {\color{Red} \frac{\mathrm{d} h_t}{\mathrm{d} w_h}} \approx {\color{salmon} \frac{\partial f_t}{\partial w_h}} + \sum_{i=1}^{t-1} \left( \prod_{j=i+1}^t \xi_j \cdot \frac{\partial f_j}{\partial h_{j-1}} \right) \cdot \frac{\partial f_i}{\partial w_h} $$

随机截断策略通过概率模型减轻了长程依赖的影响，用补偿权重限制了长路径出现的概率。但随机性产生了额外噪声，且保留概率$ \pi_t $的调度策略往往收益有限，使该策略只适用于长序列建模任务的探索性优化。

# <font style="background-color:#81BBF8;"> </font> 沿时间步反向传播的细节实现
总地来说，RNN 的梯度计算过程被称为**沿时间步的反向传播 (back propagation through time, BPTT)**，计算图沿时间步展开为长序列，使梯度像前馈神经网络一样通过[链式法则](https://www.yuque.com/tully/d2l/vmhmv5aml2byglfk#FUkoJ)得到。由于 RNN 在所有时间步中共享一套参数，最终用于参数更新的总梯度是全部时间步的梯度累加。

## 生成计算图
对于无偏置项、使用恒等映射函数$ \phi(x) = x $激活的 RNN，第$ t $个时间步的计算过程为：
$$ \begin{aligned}
\mathbf{h}_t &= \mathbf{W}_{hx} \mathbf{x}_t + \mathbf{W}_{hh} \mathbf{h}_{t-1} \\
\mathbf{o}_t &= \mathbf{W}_{qh} \mathbf{h}_t 
\end{aligned} $$

其中，单个样本输入为$\mathbf{x}_t \in \mathbb{R}^d$ ，输出为$y_t$ ，$\mathbf{W}_{hx} \in \mathbb{R}^{h \times d}$ 、$\mathbf{W}_{hh} \in \mathbb{R}^{h \times h}$ 和$\mathbf{W}_{qh} \in \mathbb{R}^{q \times h}$ 分别表示当前输入的隐状态权重、上一时间步的隐状态权重以及输出的隐状态权重。各个时间步的损失函数为$l(\mathbf{o}_t, y_t)$，则整体损失值为：
$$ L = \frac{\sum _{t=1}^T l(\mathbf{o}_t, y_t)}{T} $$

RNN 计算图在第 3 个时间步时，隐状态$\mathbf{h}_3$ 由输入 $\mathbf{x}_3$ 、权重$\mathbf{W}_{hx}$ 和$\mathbf{W}_{hh}$ 以及上一时间步的隐状态$\mathbf{h}_2$ 共同决定。变量与权重参数 $\mathbf{W}$ 的计算图依赖关系如下：

<img src="https://cdn.nlark.com/yuque/0/2025/svg/932482/1741435307611-fc45cc42-d5ef-42f0-8d91-6ecea53d0095.svg" width="400" title="用紫色圆表示运算" crop="0,0,1,1" id="u9319733d" class="ne-image">

## 梯度计算
模型训练的目的是通过优化过程得到合适的权重参数，使得模型能有效地从输入映射到输出（最小化整体损失）。需沿箭头反方向遍历上述计算图，计算并存储参数$\mathbf{W}$ 相对于总体损失$L$ 的梯度$\color{royalblue} \tfrac{\partial L}{\partial \mathbf{W}_{qh}}$、$\color{cadetblue} \tfrac{\partial L}{\partial \mathbf{W}_{hx}}$和$\color{slateblue} \tfrac{\partial L}{\partial \mathbf{W}_{hh}}$，为优化算法提供调整参数大小的“方向”。

> 与[前向传播、计算图和反向传播](https://www.yuque.com/tully/d2l/zs0vn4xd9yykw34o#BzkOe)一节类似，这里的计算同样采用通用的矩阵、向量或标量乘法。现代深度学习框架会自动缓存中间变量，避免重复计算。>

### 损失函数对于权重的梯度
计算损失函数 $L$ 对于权重 $\mathbf{W}_{qh}$ 的梯度：
$$ \begin{align}\color{royalblue} \frac{\partial L}{\partial \mathbf{W}_{qh}}&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{o}_t} \cdot \frac{\partial \mathbf{o}_t}{\partial \mathbf{W}_{qh}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{o}_t} \cdot \frac{\partial \mathbf{W}_{qh} \cdot \mathbf{h}_t }{\partial \mathbf{W}_{qh}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{o}_t} \mathbf{h}_t^\top\end{align} $$

计算损失函数 $L$ 对于权重 $\mathbf{W}_{hx}$ 的梯度：
$$ \begin{align}\color{cadetblue} \frac{\partial L}{\partial \mathbf{W}_{hx}}&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \frac{\partial \mathbf{h}_t}{\partial \mathbf{W}_{hx}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \frac{\partial (\mathbf{W}_{hx} \mathbf{x}_t + \mathbf{W}_{hh} \mathbf{h}_{t-1})}{\partial \mathbf{W}_{hx}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \left(\frac{\partial \mathbf{W}_{hx} \mathbf{x}_t}{\partial \mathbf{W}_{hx}} + \frac{\partial \mathbf{W}_{hh} \mathbf{h}_{t-1}}{{\partial \mathbf{W}_{hx}}}\right)  \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \left(\mathbf{x}_t^\top + 0 \right) \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \mathbf{x}_t^\top\end{align} $$

计算损失函数 $L$ 对于权重 $\mathbf{W}_{hh}$ 的梯度：
$$ \begin{align}\color{slateblue} \frac{\partial L}{\partial \mathbf{W}_{hh}}&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \frac{\partial \mathbf{h}_t}{\partial \mathbf{W}_{hh}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \frac{\partial (\mathbf{W}_{hx} \mathbf{x}_t + \mathbf{W}_{hh} \mathbf{h}_{t-1})}{\partial \mathbf{W}_{hh}} \\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \left (\frac{\partial \mathbf{W}_{hx} \mathbf{x}_t }{\partial \mathbf{W}_{hh}} + \frac{\partial \mathbf{W}_{hh} \mathbf{h}_{t-1}}{\partial \mathbf{W}_{hh}}\right )\\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \cdot \left (0 + \mathbf{h}_{t-1}^\top\right )\\&= \sum_{t=1}^T \frac{\partial L}{\partial \mathbf{h}_t} \mathbf{h}_{t-1}^\top\end{align} $$

### 损失函数对于隐状态的梯度
对于最后一个时间步$T$ ，损失函数$L$ 仅通过$\mathbf{o}_{T}$ 依赖于隐状态$\mathbf{h}_{T}$ 。于是，损失函数对于最后一个时间步隐状态的梯度 $\tfrac{\partial L}{\partial \mathbf{h}_T}$ 为：
$$ \begin{align}\frac{\partial L}{\partial \mathbf{h}_T}&= \frac{\partial L}{\partial \mathbf{o}_T} \cdot \frac{\partial \mathbf{o}_T}{\partial \mathbf{h}_T}  \\&= \frac{\partial L}{\partial \mathbf{o}_T} \cdot \frac{\partial \mathbf{W}_{qh} \mathbf{h}_T}{\partial \mathbf{h}_T}  \\&= \frac{\partial L}{\partial \mathbf{o}_T}\mathbf{W}_{qh}^\top\end{align} $$

对于时间步 ${\color{brown} \mathfrak{t}}<T$ ，损失函数 $L$ 同时通过$\mathbf{o}_{{\color{brown} \mathfrak{t}}}$ 和$\mathbf{h}_{{\color{brown} \mathfrak{t}}+1}$ 依赖于隐状态$\mathbf{h}_{{\color{brown} \mathfrak{t}}}$ 。于是，损失函数对于${\color{brown} \mathfrak{t}}<T$ 时间步隐状态的梯度 $\tfrac{\partial L}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}}$ 为：
$$ \begin{align}\frac{\partial L}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}}&= \frac{\partial L}{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}} \cdot \frac{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}} + \frac{\partial L}{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}} \cdot \frac{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}}  \\&= \frac{\partial L}{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}} \cdot \frac{\partial \mathbf{W}_{qh}\mathbf{h}_{{\color{brown} \mathfrak{t}}}}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}} + \frac{\partial L}{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}} \cdot \frac{\partial (\mathbf{W}_{hx} \mathbf{x}_{{\color{brown} \mathfrak{t}}+1} + \mathbf{W}_{hh} \mathbf{h}_t)}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}}  \\&= \frac{\partial L}{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}} \cdot \mathbf{W}_{qh}^\top + \frac{\partial L}{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}} \cdot \left(\frac{\partial \mathbf{W}_{hx} \mathbf{x}_{{\color{brown} \mathfrak{t}}+1}}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}} + \frac{\partial \mathbf{W}_{hh} \mathbf{h}_{\color{brown} \mathfrak{t}}}{\partial \mathbf{h}_{\color{brown} \mathfrak{t}}}\right)  \\&= \frac{\partial L}{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}} \cdot \mathbf{W}_{qh}^\top + \frac{\partial L}{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}} \cdot \left(0 + \mathbf{W}_{hh}^\top \right)  \\&= \frac{\partial L}{\partial \mathbf{o}_{\color{brown} \mathfrak{t}}}\mathbf{W}_{qh}^\top + \frac{\partial L}{\partial \mathbf{h}_{{\color{brown} \mathfrak{t}}+1}}\mathbf{W}_{hh}^\top\end{align} $$

损失函数在任意时间步 $t$ 的梯度 $\tfrac{\partial L}{\partial \mathbf{h}_t}$，由数学归纳法得
$$ \frac{\partial L}{\partial \mathbf{h}_t} = \sum_{i=t}^{T} \left( {\color{red} \mathbf{W}_{hh}^\top} \right)^{i - t} \mathbf{W}_{qh}^\top \frac{\partial L}{\partial \mathbf{o}_i} $$

可见，对于长序列（$T \gg 1$时）：若 ${\color{red} \mathbf{W}_{hh}^\top}$的最大奇异值$\sigma_{\max}$小于 1，该幂次矩阵的范数将以指数速率衰减，早期时间步的梯度贡献被抑制，直至发生梯度消失；若${\color{red} \mathbf{W}_{hh}^\top}$的最大奇异值$\sigma_{\max}$大于 1，该幂次矩阵的范数将以指数速率放大，梯度的更新过程剧烈震荡，直至发生梯度爆炸。将使用[梯度计算路径的截断策略](#g3cyp)，保证训练时的数值稳定性。在更复杂的序列模型中，将有更好的机制进一步缓解这一问题。

# <font style="background-color:#81BBF8;"> </font> RNN 中的关键概念比较
|     | [**等距截断**](#DfyVY)                        | [**随机截断**](#xMzDI)                         | [**torch.detach()**]           | [**梯度裁剪**](https://www.yuque.com/tully/d2l/gez7tdq4dzgtvkwq#cbKWq) |
| :-: | ----------------------------------------- | ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------ |
| 目标  | 降低计算复杂度                                   | 降低计算复杂度，且保持梯度估计的无偏性                        | 显式地截断梯度流，丢弃对历史梯度依赖，避免不必要的梯度计算  | 抑制梯度爆炸                                                             |
| 策略  | 将梯度反向传播的时间步跨度限制在固定大小的窗口内（需在窗口结束位置显式截断梯度流） | 用概率决定反向传播在各时间步回溯的可能性，用权重补偿梯度的期望（不必手动操作梯度流） | 以共享内存的方式返回一个新的张量，但新张量从当前计算图中分离 | 在参数更新前用梯度的范数约束梯度的数值范围，但不影响梯度流                                      |
| 场景  | 广泛的序列模型训练实践                               | 长程依赖建模探索                                   | 手动干预梯度流                        | 保证 RNN 的训练稳定                                                       |

