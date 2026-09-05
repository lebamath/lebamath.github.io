---
layout: default
title: "时频偏估计"
back_url: /chinese_version.html
---
# 时频偏估计
## 频偏估计

这个小文章，讨论一下如何估计出来频率偏差。这个问题是这样的：



通过信道，传输某个频率的 sine wave，由于各种原因（可能是本地的晶振不准）造成了一个频率偏差，那么，接收端如何计算出来这个频率偏差？



sine or cosine 波形是有周期性的，我们以一个频率发送，例如 100 Hz，那么我们每 1/100=0.01 秒就会有一个从相位 0 开始的周期，如下图绿色线所示：

第一个图是 cosine 的

![频偏 cosine](/figure/通信基础/频谱估计_1.png)

*频偏 cosine*


第二个图是 sine 的

![频偏 sine](/figure/通信基础/频谱估计_2.png)

*频偏 sine*

如果 有 10 Hz 的频率偏差，则收到的信号就是如图上红色所示.



我们通过红色线来分析:

在第  0 秒的时候，相位是 $$2 \pi (f+\Delta f) * 0 = 0$$

在第 0.01 秒的地方，相位是 $$2 \pi (f+\Delta f) * 0.01  = 2\pi f * 0.01 + 2 \pi \Delta f * 0.01$$,  这个相位，由于前面是整数周期的，所以，我们看到相位偏差为 $$2 \pi \Delta f * 0.01$$

在第 0.02 秒的地方，相位是 $$2 \pi (f+\Delta f) * 0.02  = 2\pi f * 0.02 + 2 \pi \Delta f * 0.02$$,  这个相位，由于前面是整数周期的，所以，我们看到相位偏差为 $$2 \pi \Delta f * 0.02$$



如果我们得到这个相位偏差 $$\Delta \theta$$

则可以通过下面的公式计算出频率偏差：


$$
\Delta \theta = 2 \pi \Delta f * \Delta t
$$


则：


$$
\Delta f = \frac{\Delta \theta}{ 2 \pi  \Delta t}
$$


因为 


$$
\Delta \theta \in [-\pi, \pi]
$$


则能估计出来的频率偏差的范围为：


$$
\Delta f \in [ -\frac{1}{2\Delta t}, \frac{1}{2\Delta t}]
$$


例如 $$\Delta t = 0.01$$， 那么能估计的频率偏差的范围就在：


$$
\Delta f \in [ -\frac{1}{2*0.01}, \frac{1}{2*0.01}]
$$


即：


$$
\Delta f \in [ -50, 50]
$$



画图的 python 代码,请到 Github 上下载：[https://github.com/taichiorange/leba_math](https://github.com/taichiorange/leba_math)


## 时偏估计

这篇小文章试图分析一下如何做时间偏差的估计。所谓的时间偏差，这里指的是 按照整数周期来对齐，例如下面的 sin wave :

两个波形，一个是 100Hz 的，一个是 200Hz 的，然后按照 0.01 秒来划分，如果没有任何时偏，则应该是如下图所示的情况。

![频偏 sine](/figure/通信基础/时偏估计-1.png)

*频偏 sine*


如果上面的竖线，虽然竖线之间的时间间隔还是 0.01 秒，但是因为各种原因（本地时钟有了偏差等），导致竖线的位置不在 0.01秒的整数倍上，例如都向右偏了 0.002秒，则会出现如下图的结果：

![频偏 sine](/figure/通信基础/时偏估计-2.png)

*频偏 sine*


如果偏差太大的话，就会引起各种问题，所以，要有办法来估计这个时间偏差。



做时间偏差的估计，可以采用在一定时间窗口内，做滑动互相关，找相关值最大的地方，这是一种方法；这篇文章主要探讨在 OFDM 中通过参考信号的方式来估计时间偏差。

我们看上面第二个图，在没有对齐的情况下，两个不同频率的波形，其相位的偏差是不同的，根据这个特点，我们可以反推出来时间偏差。


$$
e^{j2\pi f_1 nT}   \quad \quad  n=0,1,2,\cdots
$$


另外不同频率的：


$$
e^{j2\pi f_2 nT}   \quad \quad  n=0,1,2,\cdots
$$


如果有个小的时间偏差 $$\Delta t$$：


$$
e^{j2\pi f_1 (nT+\Delta t)}   \quad \quad  n=0,1,2,\cdots
$$


另外一个不同频率的：


$$
e^{j2\pi f_2 (nT+\Delta t)}   \quad \quad  n=0,1,2,\cdots
$$


则这两个频率在相同的时间点上，其相位偏差就是(f1 和 f2 是倍数关系，且 T 是他们最大公约数
代表的频率的周期)：


$$
2\pi (f2-f1) \Delta t
$$


这个相位我们可以在系统中计算出来，如果是 $$\Delta \theta$$，则


$$
\Delta \theta = 2\pi (f2-f1) \Delta t
$$


那么就可以计算出时间偏差：


$$
\Delta t = \frac{\Delta \theta}{ 2\pi (f2-f1) }
$$


这里需要主要的是，在这个推导过程中，需要保证 f1 和 f2 是倍数关系，且 T 是他们最大公约数代表的频率的周期。

绘图的代码：请到 [Github](https://github.com/taichiorange/leba_math) 上下载：[https://github.com/taichiorange/leba_math](https://github.com/taichiorange/leba_math)