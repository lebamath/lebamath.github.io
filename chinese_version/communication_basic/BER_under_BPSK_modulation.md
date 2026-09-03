---
layout: default
title: "BPSK 调制情况下的误比特率"
back_url: /chinese_version.html
---

# BPSK 调制情况下的误比特率

[录制的视频在 B 站](https://www.bilibili.com/video/BV1bs4y1d7zY)

这一章节的分析, 使用 BPSK 调制, 因为 BPSK 简单, 容易推导出优美的表达式出来.

## AWGN信道误比特率分析-BPSK
这篇文章主要是翻译了一篇英文博客的，自己略加修改。

原文链接： https://www.gaussianwaves.com/2012/07/intuitive-derivation-of-performance-of-an-optimum-bpsk-receiver-in-awgn-channel/



信道是 AWGN 信道，没有衰落，则可以表示为：   r = x + w

其中 x 是 BPSK 调制， w 是高斯白噪声，符合均值为 0 ，方差为  $$\frac{N_0}{2}$$ 的 高斯分布。

因为 x 是 BPSK  调制，我们假定其信号能量为 Es (因为是 BPSK，所以 Eb = Es).


![BPSK 符号映射图](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-BPSK_ideal_constellation.png)

*BPSK 符号映射图*

当传输的比特 是 0 时，则 $$S_0 = -\sqrt{E_s}$$， 因为 w 是均值为 0 ，方差为  $$\frac{N_0}{2}$$ 的 高斯分布，所以， r  是均值为 $$-\sqrt{E_s}$$ ，方差为  $$\frac{N_0}{2}$$ 的 高斯分布.

当传输的比特 是 1 时，则 $$S_1 = +\sqrt{E_s}$$， 因为 w 是均值为 0 ，方差为  $$\frac{N_0}{2}$$ 的 高斯分布，所以， r  是均值为 $$+\sqrt{E_s}$$ ，方差为  $$\frac{N_0}{2}$$ 的 高斯分布.

表示成数学公式为：
$$
p(r|0_T) = \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(-\sqrt{E_s}))^2}{N_0}}   \tag{1A}
$$

$$
p(r|1_T) = \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(+\sqrt{E_s}))^2}{N_0}}   \tag{1B}
$$

那么做最优判决的时候，如下图所示：


![BPSK 最优判决](img//awgn//AWGN信道下的误码率分析-BPSK-SISO-PDF_of_BPSK_symbols.png)

*BPSK 最优判决*

那么发生比特错误的情况为：

* 发送的是 0，但是被判决为 1
* 发送的是 1，但是被判决为 0

用数学公式表示为：
$$
P(error ) = P(判决为1, 发送0) +  P(判决为0, 发送1)  \tag{2}
$$
即：
$$
P(e) = P(1_D, 0_T) + P(0_D,1_T)  \tag{3}
$$
其中 D 表示 Decided，即被判决出来的; T 表示 transmit，即发送的。

用贝叶斯定理，公式 (3) 可以表示为：
$$
P(e) = P(1_D| 0_T)P(0_T) + P(0_D|1_T)P(1_T)  \tag{4}
$$
从下图的示意，我们可以知道 $$P(1_D| 0_T)$$ 和 $$P(0_D|1_T)$$ 的数学表达式，实际上表示的就是下图中红色的区域的概率。

![发生判决错误的区域](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-Calculating-Error-Probability.png)

*发生判决错误的区域*

则：
$$
P(1_D| 0_T) = \int_0^{+\infty} p(r|0_T) dr   \tag{5A}
$$
以及：
$$
P(1_D| 0_T) = \int_{+\infty}^0 p(r|1_T) dr   \tag{5B}
$$
把公式 (1A) 代入 (5A)，把公式 (1B) 代入 (5B) 有：
$$
P(1_D| 0_T) = \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r-(-\sqrt{E_s}))^2}{N_0}} dr 
= \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr 
\tag{6A}
$$
以及
$$
P(1_D| 0_T) = \int_{+\infty}^0 \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(+\sqrt{E_s}))^2}{N_0}}  dr  
= \int_{+\infty}^0 \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-\sqrt{E_s})^2}{N_0}}  dr
\tag{6B}
$$
因为这两个分布具有对称性，所以，公式 (4) 可以推导为（把公式 6 A  和 B 代入公式 (4) ，并利用对称性 ， 且发送的符号是等概率分布的）：
$$
\begin{aligned}
	P(e) &= \frac{1}{2}P(1_D| 0_T) + \frac{1}{2}P(0_D|1_T)\\  \\
	&= P(1_D| 0_T)\\  \\
	&=  \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr 
\end{aligned}
\tag{7}
$$
我们继续来分析公式 (7) 中的积分公式，做积分变量的变量代换，令：
$$
t = \frac{r+\sqrt{E_s}}{\sqrt{N_0/2}}
$$
则积分上下限就变成：
$$
r=0, t =\frac{\sqrt{E_s}}{\sqrt{N_0/2}} = \sqrt{\frac{E_s}{N_0/2}}  \\
r =+\infty, t=+\infty,
$$
则公式(7) 的积分就变成：
$$
\int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr
=\int_{\sqrt{\frac{E_s}{N_0/2}} }^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{8}
$$
这里就刚好是推导出来了 Q 函数的模样， 均值为 0 ，方差为 1 的标准正态分布，从 x 开始一直到无穷，计算其概率的大小，这是一个关于 x 的函数，即 Q(x)，定义如下：
$$
Q(x) =\int_x^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{9}
$$


把公式 (8) 和 (9) 逐级带回到 (7) 有：
$$
P(e) = Q\left (\sqrt{\frac{E_s}{N_0/2}} \right )
$$


![标准正太分布图Qx](img//awgn//AWGN信道下的误码率分析-BPSK-SISO-标准正太分布图Qx.png)

*标准正太分布图Qx*



画上图的 Python 代码

代码在 github:  \url{https://github.com/taichiorange/leba_math.git}


## Rayleigh衰落下的误码率分析-BPSK
这篇文章分析 Rayleigh 衰落信道下的误比特率，在 AWGN 信道误比特率的基础上推导有 Rayleigh 衰落情况下的误比特率。
$$
y = hx + w  \tag{1}
$$
发送符号 x 用 BPSK 调制，h 是复高斯分布的随机变量，其中每一维度（实部和虚部）是满足均值为 0 ，方差为 1/2 的高斯分布，则 h 的模长的平方，符合 Rayleigh 分布：
$$
p(z) = \frac{z}{\delta^2} e^{-\frac{z^2}{2\delta^2}} = 2z e^{-z^2}   \tag{2}
$$
如果信号的能量为$$E_s$$, 不考虑 h 情况下的信噪比为：
$$
SNR = \frac{Es}{N_0} = \mu  \tag{3}
$$
考虑 h 后，信噪比就变成
$$
\frac{|h|^2 E_s}{N_0} = a^2 \mu  \tag{4}
$$
其中 $$a = |h|$$,  根据高斯白噪声信道下 BPSK 调制后的误比特率公式：
$$
BER = Q(\sqrt {SNR}) = Q(\sqrt{a^2 u}) \tag{5}
$$
而 h 本身也是随机变量，所以，再根据 h 的概率分布，计算 公式 (1) 下的平均误比特率：
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da \tag{6}
$$
把 (2) 代入 (6) 有：
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da = \int_0^{+\infty} Q(\sqrt{a^2 u}) 2a e^{-a^2} da \tag{7}
$$
把 Q 函数也代入 (7):
$$
\int_0^{+\infty} 
\{ \int_{\sqrt{a^2u}}^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{t^2}{2}} dt   \} 
2a e^{-a^2} da \tag{8}
$$
对公式 (8) 的内层积分做积分变量代换，令: 
$$
y = \frac{t}{\sqrt{a^2 \mu}}
$$
则公式 (8) 变成（第二个等号是做积分顺序交换）：
$$
\begin{aligned}
	\int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} 2a e^{-a^2} da 
	&= \int_0^{+\infty} \int_1^{+\infty}  \frac{2a^2 \sqrt{\mu} }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + 2)}{2}}  dy  da
	\\  \\
	&= \int_1^{+\infty} \sqrt{\mu} \left \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + 2)}{2}} da \right \} dy 
	\\  \\
\end{aligned}
\tag{9}
$$
公式 (9) 的最内层积分中，我们令 
$$
sigma ^2 = \frac{1}{\mu y^2 + 2}   \tag{10}
$$
则公式 (9) 变成：
$$
\int_1^{+\infty} \sqrt{\mu} \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
e^{-\frac{a^2 }{2 \sigma^2}} da \} dy  = \int_1^{+\infty} \sqrt{\mu} \sigma \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi} \sigma}    
e^{-\frac{a^2 }{2 \sigma^2}} da \} dy  \tag{11}
$$
公式 (11) 中的内层积分，就是均值为 0 ，方差为 $$\sigma^2$$ 的高斯分布的方差，则公式 (10) 就推导为：
$$
\int_1^{+\infty} \sqrt{\mu} \sigma \sigma^2 dy = \int_1^{+\infty} \sqrt{\mu} \sigma^3 dy  \tag{12}
$$
将 (10) 代入 （12）有：
$$
\int_1^{+\infty} \sqrt{\mu} \frac{1}{(\mu y^2 + 2)^{\frac{3}{2}}} dy  \tag{13}
$$
对公式 (13) 的积分再做变量代换，令：
$$
y = \sqrt{\frac{2}{\mu}} tan\theta   \tag{14}
$$
则当：
$$
\begin{aligned}
	&y=1,   &\theta = tan^{-1}\sqrt{\frac{\mu}{2}}    \\
	&y=+\infty, & \theta = \frac{\pi}{2} 
\end{aligned}
\tag{15}
$$


则公式 (13) 变成：
$$
\int_1^{+\infty} \sqrt{\mu} (\mu y^2 + 2)^{-\frac{3}{2}} dy  
= \int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} }  \sqrt{\mu}(\mu(\sqrt{\frac{2}{\mu}} tan\theta)^2 + 2)^{-\frac{3}{2}} d(\sqrt{\frac{2}{\mu}} tan\theta)
\tag{16}
$$
其中：
$$
(\mu(\sqrt{\frac{2}{\mu}} tan\theta)^2 + 2)^{-\frac{3}{2}} = (2tan^2\theta + 2)^{-\frac{3}{2}} = 2^{-\frac{3}{2}} {cos^{3}\theta}   \tag{17}
$$
以及
$$
d(\sqrt{\frac{2}{\mu}} tan\theta) = \sqrt{\frac{2}{\mu}} \frac{1}{cos^2 \theta}  \tag {18}
$$
把公式 (17)(18) 代入公式 (16) 有：
$$
\int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } \frac{1}{2}  cos\theta d \theta  \tag{19} 
=\frac{1}{2}  sin\theta |_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } = \frac{1}{2}   \left (1 - sin(tan^{-1}\sqrt{\frac{\mu}{2}})\right )
$$
其中
$$
sin(x) = \sqrt{ \frac{tan^2 x}{1+tan^2 x}}
$$
则：
$$
\begin{aligned}
	sin(tan^{-1}\sqrt{\frac{\mu}{2}}) &= \sqrt{ \frac{tan^2 (tan^{-1}\sqrt{\frac{\mu}{2}})}{1+tan^2 (tan^{-1}\sqrt{\frac{\mu}{2}})}} \\  \\
	&=\sqrt{ \frac{ \sqrt{\frac{\mu}{2}}^2  }{1+\sqrt{\frac{\mu}{2}}^2} } \\ \\
	&= \sqrt{ \frac{\mu }{2+\mu} }
\end{aligned}
\tag {20}
$$
把 (20) 代入 （19）得到：
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{2+\mu} } \right )   \tag{21}
$$
注意： 很多教科书和资料中，公式 (21) 是写成：
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{1+\mu} } \right )   \tag{21}
$$
那是因为对信道系数 h 的统计特性，有不同的假设，即假设均值为 0 ，方差为 1 的，也就是公式 （2）变成：
$$
p(z) = \frac{z}{\delta^2} e^{-\frac{z^2}{2\delta^2}} = z e^{-\frac{z^2}{2}}   \tag {22}
$$



另外需要注意的是，使用 (2) 和 (22) 的公式得出的曲线，这两个是会不一样，因为 h 的概率分布变了，自然误比特率也有不同。



## Rayleigh衰落下的误码率分析-BPSK-SISO - 不固定方差的版本

这篇文章分析 Rayleigh 衰落信道下的误比特率，在 AWGN 信道误比特率的基础上推导有 Rayleigh 衰落情况下的误比特率。
$$
y = hx + w  \tag{1}
$$
发送符号 x 用 BPSK 调制，h 是复高斯分布的随机变量，其中每一维度（实部和虚部）是满足均值为 0 ，方差为 $$\sigma^2$$ 的高斯分布，则 h 的模长，符合 Rayleigh 分布：
$$
p(z) = \frac{z}{\sigma^2} e^{-\frac{z^2}{2\sigma^2}}   \tag{2}
$$
如果信号的能量为 $$E_s$$， 不考虑 h 情况下的信噪比为：
$$
SNR = \frac{Es}{N_0} = \mu  \tag{3}
$$
考虑 h 后，信噪比就变成
$$
\frac{|h|^2 E_s}{N_0} = a^2 \mu  \tag{4}
$$
其中 $$a = |h|$$,  根据高斯白噪声信道下 BPSK 调制后的误比特率公式：
$$
BER = Q(\sqrt {SNR}) = Q(\sqrt{a^2 u}) \tag{5}
$$
而 h 本身也是随机变量，所以，再根据 h 的概率分布，计算 公式 (1) 下的平均误比特率：
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da \tag{6}
$$
把 (2) 代入 (6) 有：
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da = \int_0^{+\infty} Q(\sqrt{a^2 u}) \frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} \tag{7}
$$
把 Q 函数也代入 (7):
$$
\int_0^{+\infty} 
\{ \int_{\sqrt{a^2u}}^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{t^2}{2}} dt   \} 
\frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} da \tag{8}
$$
对公式 (8) 的内层积分做积分变量代换，令: 
$$
y = \frac{t}{\sqrt{a^2 \mu}}
$$
那么内层积分做了变量代换后，积分上下限变成：


$$
t =\sqrt{a^2u}, \quad y=1  \\
t = +\infty, \quad \quad y = +\infty
$$


则公式 (8) 变成（第二个等号是做积分顺序交换），同时，为了表述方便，令 $$\gamma = \frac{1}{\sigma}$$ :
$$
\begin{aligned}
	&\int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} \frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} da  \\ \\
	&= \int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} a\gamma^2 e^{-\frac{a^2 \gamma^2}{2}} da 
	\\ \\
	&= \int_0^{+\infty} \int_1^{+\infty}  \frac{ \gamma^2 a^2 \sqrt{\mu} }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + \gamma^2)}{2}}  dy  da
	\\  \\
	&= \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \left \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + \gamma^2)}{2}} da \right \} dy 
	\\  \\
\end{aligned}
\tag{9}
$$
公式 (9) 的最内层积分中，我们令 
$$
\hat \sigma ^2 = \frac{1}{\mu y^2 + \gamma^2}   \tag{10}
$$
则公式 (9) 变成：
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
e^{-\frac{a^2 }{2 \hat \sigma^2}} da \} dy  = \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi} \hat \sigma}    
e^{-\frac{a^2 }{2 \hat \sigma^2}} da \} dy  \tag{11}
$$
公式 (11) 中的内层积分，就是均值为 0 ，方差为 $$\hat \sigma^2$$ 的高斯分布的方差，则公式 (10) 就推导为：
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma \hat \sigma^2 dy = \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma^3 dy  \tag{12}
$$
将 (10) 代入 （12）有：
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \frac{1}{(\mu y^2 + \gamma^2)^{\frac{3}{2}}} dy  \tag{13}
$$
对公式 (13) 的积分再做变量代换，令：
$$
y = \sqrt{\frac{\gamma^2}{\mu}} tan\theta   \tag{14}
$$
则当：
$$
\begin{aligned}
	&y=1,   &\theta = tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}    \\
	&y=+\infty, & \theta = \frac{\pi}{2} 
\end{aligned}
\tag{15}
$$


则公式 (13) 变成：
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 (\mu y^2 + \gamma^2)^{-\frac{3}{2}} dy  
= \int_{ tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}}^{\frac{\pi}{2} }  \frac{1}{2}\sqrt{\mu}\gamma^2(\mu(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)^2 + \gamma^2)^{-\frac{3}{2}} d(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)
\tag{16}
$$
其中：
$$
(\mu(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)^2 + \gamma^2)^{-\frac{3}{2}} = (\gamma^2tan^2\theta + \gamma^2)^{-\frac{3}{2}} = ({\gamma^2})^{-\frac{3}{2}} {cos^{3}\theta}   \tag{17}
$$
以及
$$
d(\sqrt{\frac{\gamma^2}{\mu}} tan\theta) = \sqrt{\frac{\gamma^2}{\mu}} \frac{1}{cos^2 \theta}  \tag {18}
$$
把公式 (17)(18) 代入公式 (16) 有：
$$
\int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } \frac{1}{2}\gamma^2 \frac{1}{\gamma^2}  cos\theta d \theta  \tag{19} 
=\frac{1}{2}  sin\theta |_{ tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}}^{\frac{\pi}{2} } = \frac{1}{2}   \left (1 - sin(tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})\right )
$$
其中
$$
sin(x) = \sqrt{ \frac{tan^2 x}{1+tan^2 x}}
$$
则：
$$
\begin{aligned}
	sin(tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}) &= \sqrt{ \frac{tan^2 (tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})}{1+tan^2 (tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})}} \\  \\
	&=\sqrt{ \frac{ \sqrt{\frac{\mu}{\gamma^2}}^2  }{1+\sqrt{\frac{\mu}{\gamma^2}}^2} } \\ \\
	&= \sqrt{ \frac{\mu }{\gamma^2+\mu} }
\end{aligned}
\tag {20}
$$
把 (20) 代入 （19）得到：
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\gamma^2+\mu} } \right )   \tag{21}
$$

## Rayleigh 信道与 AWGN 信道 BER 对比分析

Rayleigh 衰落信道的误比特率（BSPK调制）：


$$
BER_{Rayleigh} = \frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\frac{1}{\sigma^2}+\mu} } \right )   \tag{1}
$$
AWGN  信道的误比特率 (BPSK 调制)：
$$
BER_{AWGN} = Q(\mu) =\int_{\mu}^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{2}
$$
其中  $$\mu$$  是不考虑衰减系数情况下的信噪比：
$$
\mu = SNR = \frac{E_s}{N_0/2}  \tag{3}
$$



![发生判决错误的区域](img//awgn//Rayleigh衰落信道与 AWGN 信道 BER 对比.png)

*发生判决错误的区域*



a) Rayleigh 衰落信道，相同信噪比时，信道系数 h 的方差越大，误比特率越低： h 的方差大，可以理解为对信号的放大作用就越大，因此，等效为提高了信噪比。

b) AWGN 信道与 Rayleigh 衰落信道对比：

b.1) 当 Rayleigh 衰落信道的方差比较小时，即信号放大倍数小甚至信号被衰减了，则 AWGN 在高信噪比和低信噪比情况下都优于 Rayleigh 信道

b.2) 当 Rayleigh 衰落信道的方差比较大时，即信号放大的倍数比较大

b.2.1)在低信噪比，即噪声能量比较大时，由于信号的放大倍数足够大，所以，提高的信噪比而带来的改善，超过由于部分衰落引起的性能降低，所以，这种情况下 Rayleigh 衰落信道的误码率要比 AWGN 的低。

b.2.2)  在高信噪比，即噪声能力比较小时，虽然信号的放大倍数比较大，但是总有一些信号是被缩小的情况，信号被缩小的情况就是信噪比恶化的情况，导致误码率提高，而因为噪声小，AWGN 的性能就很好（BPSK的两个符号之间的能量差比较大，噪声干扰导致误比特的可能性非常低），而 rayleigh 信道系数的随机性，总有一些系数是把信号的能量缩小很多，从而导致信噪比恶化，进而误比特率上升。




从数学公式的角度看，就是比较公式 （1）和 (2) 两个误比特率：
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\frac{1}{\sigma^2}+\mu} } \right ) 
\quad \quad\quad\quad  \quad\quad\quad\quad  \quad\quad\quad\quad
\int_{\mu}^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt
$$
比较两者之间的大小关系，其中有两个是变量，即 $$\sigma^2$$ 和 $$\mu = SNR$$.

代码在 github:  \url{https://github.com/taichiorange/leba_math.git}