# 神经网络与通信：初步

## 一个简单的例子
首先说明，这个例子应该是非常低效的，且可能也根本没有什么工程上实际的用途，这个只是为了演示如何用神经网络来实现一个最简单的通信模块。

参考代码在我的 github 上：https://github.com/taichiorange/leba\_math
在子目录：人工只能与无线通信/AWGN 信道下用神经网络做解调/下，文件名为：neuralNetwork-demapping-under-AWGN-channel.py


这个例子是实现图 1 中红色的神经网络，是替代经典的解调器 (Demapper).

![图1：总体结构和模块](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/communication_link_structure.png)

*图1：总体结构和模块*


这个神经网络总体结构如图 2所示。

![图2：demapping 用的神经网络示意图](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/nn-for-awgn-demmaping.png)

*图2：demapping 用的神经网络示意图*

先讲一下神经网络中各个模块是如何做计算的。 

卷积模块：卷积，从数学概念上看，其实就是一个按照系数比例做加权求和，例如：

$$
y = \sum_{k=0}^{K} \mathbf x(k) \mathbf w(k)
$$

其中 $$K$$ 表示卷积深度。

Sionna( Nvidia 推出了通信库，方便加入神经网络）是基于 TensorFlow 实现的， Tensorflow 中的卷积函数，以一维为例，在上面的卷积的基础上，又引入了输入的通道(channels)数，假如通道数是 $$C_{\text {in}}$$ 的话，那卷积的基本形式就变为：

$$
y = \sum_{k=0}^{\mathbf K} \sum_{c=0}^{C_{\text{in}}} \mathbf x(k,c) \mathbf w(k,c)
$$

进一步地，为了有效提取输入数据中的不同特征，又引入了输出通道的概念，输出通道的个数，也成为卷积核的 Filter 的数量，记为 $$\mathbf M$$，那么对于任意一个输出通道 $$m$$，使用的数据数据都是相同的，但是，权重系数不同（这个是需要训练出来的），那么，上面的卷积可以进一步写成：

$$
y(m) = \sum_{k=0}^{\mathbf K} \sum_{c=0}^{C_{\text{in}}} \mathbf x(k,c) \mathbf w(k,c,m)
$$

再考虑输出的数据 y，也是源源不断地输出，或者在通信中，可以理解为每个采样时间点上的输出，记为 n 的话，那么，上面的卷积公式就是：

$$
y(n,m) = \sum_{k=0}^{\mathbf K} \sum_{c=0}^{C_{\text{in}}} \mathbf x(n+k,c) \mathbf w(k,c,m)
$$

最后，为了加速并行计算以及稳定找最优系数，一般会提供多组数据同时进行计算，一组数据称之为一个 Batch。则卷积公式变为：

$$
y(b,n,m) = \sum_{k=0}^{\mathbf K} \sum_{c=0}^{C_{\text{in}}} \mathbf x(b,n+k,c) \mathbf w(b,k,c,m)
$$

其中 $$b$$  表示第几个 batch，$$n$$ 表示第几个采样时刻，$$m$$ 表示第几个输出通道，$$c$$ 是表示第几个输入通道，k 表示卷积核中的第几个系数。

把所有 Batch 的损失函数的值统一考虑，在找最优解的时候避免来回跳动。当然，在时间域内可以比如是一帧来统一考虑损失函数的值。

在我们这个高斯信道下解调的神经网络，训练时定义了一个 BLOCK( 一块) 这样的时长。在一个 BLOCK 内，多个 BATCH 一起，统一计算损失函数的值。

一维卷积层的输入输出关系如图 3 所示：

![图3：一维卷积示意图](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/conv-basic.png)

*图3：一维卷积示意图*


对于第一个卷积层，输入的 channels 数量是 3，输出 channel 数量是 268，用了特殊的数字来方便 debug 时分析用。如图 4 所示。

![图4：第一个卷积层的输入示意图](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/convInput-input.png)

*图4：第一个卷积层的输入示意图*


对于中间的卷积层，即残差卷积层中的卷积层，输入 channel 数与输出channel  数相等，都是 268。如图 5 所示。

![图5：第一个卷积层的输出示意图](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/convInput-output.png)

*图5：第一个卷积层的输出示意图*

最后一个卷积层，输入 channel 数量是 268，输出 channel 数量与调制方式对应，对应一个调制符号携带的比特数，例子中是 QAM 64 调制，所以输出 channel 数量是 6.如图 6 所示。

![图6：第一个卷积层的输出示意图](/figure/NeuralNetwork/0_First_sample_demapper_under_AWGN/convOutput-output.png)

*图6：第一个卷积层的输出示意图*