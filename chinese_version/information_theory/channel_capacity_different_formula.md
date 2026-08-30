---
layout: default
title: "信道容量的几个易混淆的公式"
back_url: /chinese_version.html
---

## 信道容量的几个易混淆的公式

[录制的视频在](https://www.bilibili.com/video/BV1dg4y1E7oM/)：https://www.bilibili.com/video/BV1dg4y1E7oM/

本文章试图把信道容量背后的物理思想做一下解释，或者说是本人的一个粗浅理解。

首先我们抛出几个我之前经常碰到但是迷惑的信道容量公式：
$$
C = \frac{1}{2} log(1+\frac{S}{N})   \tag{1}
$$

$$
C = W log(1+\frac{S}{N})  \tag{2}
$$

以及
$$
C = log(1+\frac{S}{N})   \tag{3}
$$


我们来逐个分析一下，公式 (1) 是后面两个的基础，这个公式的推导是基于如下这个信道模型来推导的：
$$
Y_i = X_i + Z_i   \tag{4}
$$
其中 $$X_i$$ 是信道的输入，下标 i 表示第 i 时刻的输入， $$Y_i$$ 是信道的输出， $$Z_i$$ 是高斯白噪声。 这三个量都是实数，不是复数。根据信息论中的互信息和信息熵等概念和公式，可以得出上面这个信道的信道容量为公式 (1)，具体的证明可以看文献 [1] 9.1节的推导过程。

下面这个是本文想重点说的思想：上面这个信道模型(4)，可以理解为在一个周期内通过 cosine 波形传输信息的信道，即在现实中或者物理线路中，通过 cosine 波形来传递信息。例如  用四种 cosine 的电平来传递信息，每个电平就可以携带两个比特的信息。 我们又知道，在与这个 cosine 同样频率下的 sine 波形，是与 cosine 波形正交的，那么在同一个时间内，我们也可以用 sine 波形来传递信息（与同频的 cosine 波形同时传），因为 cosine 与 sine 正交，所以，两者不会相互干扰。

所以，在 1Hz 内，我们可以有两个如 (4) 这样的信道，所以，其 1Hz 能传输的最大数据量就是 (1) 的两倍，所以，就得到了公式 (3) ，公式 (3) 这个容量值的单位是 bits per second per Hz.

至此，来理解公式(2) 就比较容易了，假设我们的物理带宽（实际占用的频率范围，注意：是正的频率，不是负的频率）为 W 赫兹，那么在这个带宽内的信道容量就是公式 (2)，其单位是 bits per second.

​    用图形可以表示为：


![信道容量的浅析](/figure/information_theory/channel_capacity_tutorial.png)

*信道容量的浅析*




上面这个图可以认为是一个 2-dimension 的信道, 文献 [2] 中还提到了 4-dimension 的信道，个人理解是需要再另外一个频点上实现如上图所示的结构，两个频点各是 2-dimension 的，构成了一个 4-dimension 的信道。







[1] Elements of Information Theory - T M Cover- Second Edition

[2] P. E. McIllree, "Calculation of channel capacity for M-ary digital modulation signal sets," *Proceedings of IEEE Singapore International Conference on Networks/International Conference on Information Engineering '93*, Singapore, 1993, pp. 639-643 vol.2, doi: 10.1109/SICON.1993.515666.
