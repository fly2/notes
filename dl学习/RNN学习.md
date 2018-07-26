## RNN学习

参考

[gluon-tutorials-zh](https://github.com/mli/gluon-tutorials-zh)

### SRN(Simple Recurrent Networks)

早期的rnn由于结构简单，也被称为SRN。典型的有elman network和jordan network，在实践中，简单rnn无法很好的计算长时依赖。

#### Elman network

$$
h_t=\sigma_t(W_hx_t+U_hh_{t-1}+b_h)\\
y_t=\sigma_y(W_yh_t+b_y)
$$

#### Jordan network

$$
h_t=\sigma_t(W_hx_t+U_hy_{t-1}+b_h)\\
y_t=\sigma_y(W_yh_t+b_y)
$$

在上式中：$x_t$表示输入向量，$h_t$表示隐层向量，$y_t$是输出向量。$W,U$和$b$表示参数矩阵和向量。$\sigma_h$和$\sigma_y$为激活函数。

### RNN

首先回忆一下单隐含层的前馈神经网络的定义，例如[多层感知机](../chapter_supervised-learning/mlp-scratch.md)。假设隐含层的激活函数是$\phi$，对于一个样本数为$n$特征向量维度为$x$的批量数据$\mathbf{X} \in \mathbb{R}^{n \times x}$（$\mathbf{X}$是一个$n$行$x$列的实数矩阵）来说，那么这个隐含层的输出就是

$$\mathbf{H} = \phi(\mathbf{X} \mathbf{W}_{xh} + \mathbf{b}_h)$$

假定隐含层长度为$h$，其中的$\mathbf{W}_{xh} \in \mathbb{R}^{x \times h}$是权重参数。偏移参数 $\mathbf{b}_h \in \mathbb{R}^{1 \times h}$在与前一项$\mathbf{X} \mathbf{W}_{xh} \in \mathbb{R}^{n \times h}$ 相加时使用了[广播](../chapter_crashcourse/ndarray.md)。这个隐含层的输出的尺寸为$\mathbf{H} \in \mathbb{R}^{n \times h}$。

把隐含层的输出$\mathbf{H}$作为输出层的输入，最终的输出

$$\hat{\mathbf{Y}} = \text{softmax}(\mathbf{H} \mathbf{W}_{hy} + \mathbf{b}_y)$$

假定每个样本对应的输出向量维度为$y$，其中 $\hat{\mathbf{Y}} \in \mathbb{R}^{n \times y}, \mathbf{W}_{hy} \in \mathbb{R}^{h \times y}, \mathbf{b}_y \in \mathbb{R}^{1 \times y}$且两项相加使用了[广播](../chapter_crashcourse/ndarray.md)。

将上面网络改成循环神经网络，我们首先对输入输出加上时间戳$t$。假设$\mathbf{X}_t \in \mathbb{R}^{n \times x}$是序列中的第$t$个批量输入（样本数为$n$，每个样本的特征向量维度为$x$），对应的隐含层输出是隐含状态$\mathbf{H}_t  \in \mathbb{R}^{n \times h}$（隐含层长度为$h$），而对应的最终输出是$\hat{\mathbf{Y}}_t \in \mathbb{R}^{n \times y}$（每个样本对应的输出向量维度为$y$）。在计算隐含层的输出的时候，循环神经网络只需要在前馈神经网络基础上加上跟前一时间$t-1$输入隐含层$\mathbf{H}_{t-1} \in \mathbb{R}^{n \times h}$的加权和。为此，我们引入一个新的可学习的权重$\mathbf{W}_{hh} \in \mathbb{R}^{h \times h}$：

$$\mathbf{H}_t = \phi(\mathbf{X}_t \mathbf{W}_{xh} + \mathbf{H}_{t-1} \mathbf{W}_{hh}  + \mathbf{b}_h)$$

输出的计算跟前面一致：

$$\hat{\mathbf{Y}}_t = \text{softmax}(\mathbf{H}_t \mathbf{W}_{hy}  + \mathbf{b}_y)$$

一开始我们提到过，隐含状态可以认为是这个网络的记忆。该网络中，时刻$t$的隐含状态就是该时刻的隐含层变量$\mathbf{H}_t$。它存储前面时间里面的信息。我们的输出是只基于这个状态。最开始的隐含状态里的元素通常会被初始化为0。

### GRU

我们先介绍门控循环单元的构造。它比循环神经网络中的隐含层构造稍复杂一点。

#### 重置门和更新门

门控循环单元的隐含状态只包含隐含层变量$\mathbf{H}$。假定隐含状态长度为$h$，给定时刻$t$的一个样本数为$n$特征向量维度为$x$的批量数据$\mathbf{X}_t \in \mathbb{R}^{n \times x}$和上一时刻隐含状态$\mathbf{H}_{t-1} \in \mathbb{R}^{n \times h}$，重置门（reset gate）$\mathbf{R}_t \in \mathbb{R}^{n \times h}$和更新门（update gate）$\mathbf{Z}_t \in \mathbb{R}^{n \times h}$的定义如下：

$$\mathbf{R}_t = \sigma(\mathbf{X}_t \mathbf{W}_{xr} + \mathbf{H}_{t-1} \mathbf{W}_{hr} + \mathbf{b}_r)$$

$$\mathbf{Z}_t = \sigma(\mathbf{X}_t \mathbf{W}_{xz} + \mathbf{H}_{t-1} \mathbf{W}_{hz} + \mathbf{b}_z)$$

其中的$\mathbf{W}_{xr}, \mathbf{W}_{xz} \in \mathbb{R}^{x \times h}$和$\mathbf{W}_{hr}, \mathbf{W}_{hz} \in \mathbb{R}^{h \times h}$是可学习的权重参数，$\mathbf{b}_r, \mathbf{b}_z \in \mathbb{R}^{1 \times h}$是可学习的偏移参数。函数$\sigma$自变量中的三项相加使用了[广播](../chapter_crashcourse/ndarray.md)。

需要注意的是，重置门和更新门使用了值域为$[0, 1]$的函数$\sigma(x) = 1/(1+\text{exp}(-x))$。因此，重置门$\mathbf{R}_t$和更新门$\mathbf{Z}_t$中每个元素的值域都是$[0, 1]$。

#### 候选隐含状态

我们可以通过元素值域在$[0, 1]$的更新门和重置门来控制隐含状态中信息的流动：这通常可以应用按元素乘法符$\odot$。门控循环单元中的候选隐含状态$\tilde{\mathbf{H}}_t \in \mathbb{R}^{n \times h}$使用了值域在$[-1, 1]$的双曲正切函数tanh做激活函数：

$$\tilde{\mathbf{H}}_t = \text{tanh}(\mathbf{X}_t \mathbf{W}_{xh} + \mathbf{R}_t \odot \mathbf{H}_{t-1} \mathbf{W}_{hh} + \mathbf{b}_h)$$

其中的$\mathbf{W}_{xh} \in \mathbb{R}^{x \times h}$和$\mathbf{W}_{hh} \in \mathbb{R}^{h \times h}$是可学习的权重参数，$\mathbf{b}_h \in \mathbb{R}^{1 \times h}$是可学习的偏移参数。

需要注意的是，候选隐含状态使用了重置门来控制包含过去时刻信息的上一个隐含状态的流入。如果重置门近似0，上一个隐含状态将被丢弃。因此，重置门提供了丢弃与未来无关的过去隐含状态的机制。

#### 隐含状态

隐含状态$\mathbf{H}_t \in \mathbb{R}^{n \times h}$的计算使用更新门$\mathbf{Z}_t$来对上一时刻的隐含状态$\mathbf{H}_{t-1}$和当前时刻的候选隐含状态$\tilde{\mathbf{H}}_t$做组合，公式如下：

$$\mathbf{H}_t = \mathbf{Z}_t \odot \mathbf{H}_{t-1}  + (1 - \mathbf{Z}_t) \odot \tilde{\mathbf{H}}_t$$

需要注意的是，更新门可以控制过去的隐含状态在当前时刻的重要性。如果更新门一直近似1，过去的隐含状态将一直通过时间保存并传递至当前时刻。这个设计可以应对循环神经网络中的梯度衰减问题，并更好地捕捉时序数据中间隔较大的依赖关系。

我们对门控循环单元的设计稍作总结：

- 重置门有助于捕捉时序数据中短期的依赖关系。
- 更新门有助于捕捉时序数据中长期的依赖关系。

输出层的设计可参照[循环神经网络](rnn-scratch.md)中的描述。

### LSTM

在我们先介绍长短期记忆的构造。长短期记忆的隐含状态包括隐含层变量$\mathbf{H}$和细胞$\mathbf{C}$（也称记忆细胞）。它们形状相同。

#### 输入门、遗忘门和输出门

假定隐含状态长度为$h$，给定时刻$t$的一个样本数为$n$特征向量维度为$x$的批量数据$\mathbf{X}_t \in \mathbb{R}^{n \times x}$和上一时刻隐含状态$\mathbf{H}_{t-1} \in \mathbb{R}^{n \times h}$，输入门（input gate）$\mathbf{I}_t \in \mathbb{R}^{n \times h}$、遗忘门（forget gate）$\mathbf{F}_t \in \mathbb{R}^{n \times h}$和输出门（output gate）$\mathbf{O}_t \in \mathbb{R}^{n \times h}$的定义如下：

$$\mathbf{I}_t = \sigma(\mathbf{X}_t \mathbf{W}_{xi} + \mathbf{H}_{t-1} \mathbf{W}_{hi} + \mathbf{b}_i)$$

$$\mathbf{F}_t = \sigma(\mathbf{X}_t \mathbf{W}_{xf} + \mathbf{H}_{t-1} \mathbf{W}_{hf} + \mathbf{b}_f)$$

$$\mathbf{O}_t = \sigma(\mathbf{X}_t \mathbf{W}_{xo} + \mathbf{H}_{t-1} \mathbf{W}_{ho} + \mathbf{b}_o)$$

其中的$\mathbf{W}_{xi}, \mathbf{W}_{xf}, \mathbf{W}_{xo} \in \mathbb{R}^{x \times h}$和$\mathbf{W}_{hi}, \mathbf{W}_{hf}, \mathbf{W}_{ho} \in \mathbb{R}^{h \times h}$是可学习的权重参数，$\mathbf{b}_i, \mathbf{b}_f, \mathbf{b}_o \in \mathbb{R}^{1 \times h}$是可学习的偏移参数。函数$\sigma$自变量中的三项相加使用了[广播](../chapter_crashcourse/ndarray.md)。

和[门控循环单元](gru-scratch.md)中的重置门和更新门一样，这里的输入门、遗忘门和输出门中每个元素的值域都是$[0, 1]$。

#### 候选细胞

和[门控循环单元](gru-scratch.md)中的候选隐含状态一样，长短期记忆中的候选细胞$\tilde{\mathbf{C}}_t \in \mathbb{R}^{n \times h}$也使用了值域在$[-1, 1]$的双曲正切函数tanh做激活函数：

$$\tilde{\mathbf{C}}_t = \text{tanh}(\mathbf{X}_t \mathbf{W}_{xc} + \mathbf{H}_{t-1} \mathbf{W}_{hc} + \mathbf{b}_c)$$

其中的$\mathbf{W}_{xc} \in \mathbb{R}^{x \times h}$和$\mathbf{W}_{hc} \in \mathbb{R}^{h \times h}$是可学习的权重参数，$\mathbf{b}_c \in \mathbb{R}^{1 \times h}$是可学习的偏移参数。

#### 细胞

我们可以通过元素值域在$[0, 1]$的输入门、遗忘门和输出门来控制隐含状态中信息的流动：这通常可以应用按元素乘法符$\odot$。当前时刻细胞$\mathbf{C}_t \in \mathbb{R}^{n \times h}$的计算组合了上一时刻细胞和当前时刻候选细胞的信息，并通过遗忘门和输入门来控制信息的流动：

$$\mathbf{C}_t = \mathbf{F}_t \odot \mathbf{C}_{t-1} + \mathbf{I}_t \odot \tilde{\mathbf{C}}_t$$

需要注意的是，如果遗忘门一直近似1且输入门一直近似0，过去的细胞将一直通过时间保存并传递至当前时刻。这个设计可以应对循环神经网络中的梯度衰减问题，并更好地捕捉时序数据中间隔较大的依赖关系。

#### 隐含状态

有了细胞以后，接下来我们还可以通过输出门来控制从细胞到隐含层变量$\mathbf{H}_t \in \mathbb{R}^{n \times h}$的信息的流动：

$$\mathbf{H}_t = \mathbf{O}_t \odot \text{tanh}(\mathbf{C}_t)$$

需要注意的是，当输出门近似1，细胞信息将传递到隐含层变量；当输出门近似0，细胞信息只自己保留。