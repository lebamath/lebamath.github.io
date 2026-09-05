---
layout: default
title: "浅析无线信道的数学表示"
back_url: /chinese_version.html
---

# 浅析无线信道的数学表示
## 多径传播的典型场景 
一个典型的多径传播场景

![multi-path.png](/figure/通信基础/无线信道的数学表示/multi-path.png)

用$$X_{LP}$$ 表示发送的原始信号（即没有被搬移到高频去 LP: Low Pass），称为低通信号或者基带信号，用$$X_{BP}$$ 表示搬移到高频的信号（BP： Band Pass)，称为带通信号. 则两者之间的关系为：

$$
X_{BP} = Re\{ X_{LP}(t)e^{j2\pi f_0 t}\} \quad --------(1)
$$

其中 $$f_0$$ 是高频，例如 1800MHz. 而$$X_{LP}$$ 可能就是 $$\pm 100MHz$$，并且以 0 频为中心。


## 多径信号基带的一般表达式

1）每个路径在时间上有不同的延时，而且不同时刻延时不同，即时变的，表示为$$\tau'_n(t)$$，表示在 t 时刻，第 n 条路径的时延

2）不同时间下，路径的条数也不同，即时变的，表示为 N(t)

3）每个路径上的幅度增益和相位偏移也是不同的，而且是时变的，幅度增益系数表示为 $$c_n(t)$$，表示在时刻 t 时，第 n 条路径的增益系数，这是一个正实数；相位偏移表示为$$e^{j\phi_n(t)}$$，表示在时刻 t 时，第 n 条路径的相位偏移，其中$$\phi_n(t)$$ 为偏移的相位

所以，根据公式 (1) ，此时多径的带通信号表达式  为：

$$
Y_{BP}(t) = Re\{ \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (t-\tau'_n(t))} \}
$$

其中 $$X_{LP}$$  是基站发出的原始基带信号。



则通过上式可以计算出来收到的等效基带信号。

$$
Y_{BP}(t) = Re\{ \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (-\tau'_n(t))}  e^{j2\pi f_0 t} \}
$$

则$$Y_{LP}$$ 就是等号左边去掉$$e^{j2\pi f_0 t}$$的部分，即：

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (-\tau'_n(t))}
$$

整理后得到：

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j(\phi_n(t)-2\pi f_0 \tau'_n(t))}  X_{LP}(t-\tau'_n(t))
$$

分开 e 指数部分的角度：

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)} e^{j2\pi f_0 (-\tau'_n(t)) }  X_{LP}(t-\tau'_n(t))
$$

根据上面的公式，可以得出多径信道的时变脉冲响应：

$$
h(\tau',t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j(\phi_n(t)-2\pi f_0 \tau'_n(t))}  \delta(\tau'-\tau'_n(t))\quad ----(2)
$$

分开角度的公式版本：

$$
h(\tau',t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j(\phi_n(t)} e^{-j2\pi f_0 \tau'_n(t)}  \delta(\tau'-\tau'_n(t))\quad ----(2.1)
$$

上面这个式子的含义，是在 t（可以认为是常量，是要分析的时刻点，变量是$$\tau'$$） 时刻，收到 “ $$t-\tau'$$时刻发送的脉冲 ” “经过了$$\tau'$$ 这么长时间的延时” 到达接收端的信号。注意的是，相同的 $$\tau'$$ 延时，可能有多个不同的路径。因此，在上面的公式中有个求和公式和一个 $$\delta$$函数。

( 备注：在那些不可分的，或者说延时很接近的那些路径组合在一起，构成一个独立的可识别的路径，可能有多个这种路径：每个路径之间的间隔和区隔是比较明显的，每个路径又是由许许多多延时相同但是角度和衰减不同但延时相同或者相近的子路径组成，如下图示例）



![multi-path2.png](/figure/通信基础/无线信道的数学表示/multi-path2.png)


上图上一共有三个不同延时的路径，而每个路径下又有多个不同的子路径组成，每个路径下的各个子路径之间，延时相同（衰减系数和相位，即复衰减系数可能不同）或者延时有微小差别。

## 多径信道传输函数的合理简化

由于公式 (2) 是时变的，非常难去分析，所以，需要做合理的简化。

假设在一个很短的观察窗口内，即很短的时间窗口 $$T_0$$ 内，我们可以假定公式 (2) 中传播路径的数量 N(t) 和增益系数 $$c_n(t)$$ 以及相位 $$\phi_n(t)$$ 都是非时变的，同时，各个路径中信道的到达角度 $$\alpha_n(t)$$ 以及终端的移动速度 $$v(t)$$ 都是非时变的，利用上述假设，令$$t\in [t_0,t_0+T_0]$$，公式 (2) 可以简化为：

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(t))}  \delta(\tau'-\tau'_n(t))\quad ----(3)
$$

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\phi_n}e^{-j2\pi f_0 \tau'_n(t)}  \delta(\tau'-\tau'_n(t))\quad ----(3)
$$

其中，$$\tau'_n(t)$$ 还是时变的，即使终端的移动速度 v 是非时变的，终端以恒定速度移动，也会造成各个路径的时延的改变，下面就推导一下时延，推导成不依赖时间 t 这个参数，我们以某一条路径为切入点来分析，不失一般性，令$$t_0=0$$，则$$t\in [0,T_0]$$，如果在 0 时刻，第n条路径的时延为$$\tau'_n(0)$$，则终端经过时间 t 的移动后：

移动的距离为 vt ，在电磁波方向上移动的距离为 $$vt cos(\alpha_n)$$，再除以光速，就得到这段移动的距离上电磁波传播需要的时间

$$
\frac{vt*cos(\alpha_n)}{c_0}
$$

其中 $$c_0$$ 为光速，综合以上分析有：

$$
\tau'_n(t) = \tau'_n(0) - \frac{vt cos(\alpha_n)}{c_0}\quad------(4)
$$

（这里容易混淆的是：手机移动的时间 t 与 延时时间 $$\tau'_n(t)$$是不同的)

上式中 $$vt cos(\alpha_n)$$是手机移动的距离在电磁波方向上的分量，如下图所示：


![doppler.png](/figure/通信基础/无线信道的数学表示/doppler.png)




而波长是电磁波的周期$$\frac{1}{f_0}$$乘以光速 $$c_0$$ 得到，那么距离除以波长，就是对应的周期数：

$$
\frac{vt cos(\alpha_n)}{\frac{1}{f_0}c_0} = \frac{f_0 vt cos(\alpha_n)}{c_0}
$$

上面的周期数再除以对应的时间 t ，则是频率（即频率偏移，多普勒频移），可以把上面公式中的 t 约掉而变成：

$$
\frac{f_0 v cos(\alpha_n)}{c_0}
$$

记为第 n 个路径的多普勒频移 $$f_n$$. 把不乘以角度的部分称为最大多普勒频移 $$f_{max}$$ :

$$
f_{max} = \frac{f_0 v}{c_0}
$$

则： $$f_n=f_{max} cos(\alpha_n)$$

$$
\tau'_n(t) = \tau'_n(0) - \frac{vt cos(\alpha_n)}{c_0} = \tau'_n(0) - t \frac{f_n}{f_0}
$$

把这个$$\tau'_n(t)$$  代入公式 (3)，考虑到 t 很小，而且 $$f_n$$一般远小于 $$f_0$$，可以做如下近似：

$$
\delta(\tau'-\tau'_n(t_0) + t \frac{f_n}{f_0} ) \approx \delta(\tau'-\tau'_n(t_0))
$$

由于公式 (3) 指数部分的 $$\tau'_n(t)$$是要与很大的 相 $$f_0$$ 作用，所以不能做近似。 



则

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(0)+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(4)
$$

把多普勒频移相关的部分分离出来，上式可以表示为：

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(0))} e^{j2\pi f_nt}  \delta(\tau'-\tau'_n(0)) \quad -----(4.1)
$$

其中指数中的第二项：

$$
2\pi f_0 \tau'_n(0) = 2\pi \frac{c_0}{\lambda_0} \tau'_n(0) = \frac{2\pi}{\lambda_0} c_0 \tau'_n(0) = K_0 D_n
$$

上式中的 $$k_0=\frac{2\pi}{\lambda_0}$$ 表示自由空间波数，仅仅与射频的频率有关系；

$$D_n = c_0 \tau'_n(0)$$ 是传输距离

可以看到，这个 $$k_0 D_n$$ 比较大的

公式 (4) 中的 $$2\pi f_n t$$ 是多普勒频移引起的相位偏差

$$c_n$$ 和 $$\phi_n$$ 是被传输信号与散射体相互作用的结果.

令$$\phi'_n = k_0 D_n$$，公式 (4) 可以写成

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-\phi'_n+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(5)
$$

## 多径信道模型：引入随机变量

可以比较合理的假定 散射体造成的相位差 $$\phi_n$$ 和 传输距离造成的相位差$$\phi'_n$$ 是相互独立的，且在$$[0,2\pi]$$之间均匀分布，由于复指数是周期函数，所以，可以认为$$\theta_n = \phi_n-\phi'_n$$ 在$$[0,2\pi]$$ 之间均匀分布的，虽然$$\beta=\phi_n-\phi'_n$$是在 $$[0,3\pi]$$之间的受限三角形，若考虑 $$\beta \quad mod \quad 2\pi$$是均匀分布在 $$[0,2\pi]$$ 之间的，则可以把 $$\theta_n = \phi_n-\phi'_n$$ 认为在$$[0,2\pi]$$之间均匀分布。

则公式 (5) 可以简化为

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\theta_n+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(5)
$$

即：

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0)) \quad -----(6)
$$

## 多径信道传递函数的频域分析

对公式 (6) 中，把 t 看成固定参数， 对$$\tau'$$ 进行傅里叶变换：

$$
H(f',t) = \sum_{n=1}^Nc_n e^{j(2\pi f_nt + \theta_n)} e^{-j2\pi\tau'_n(0)f'} \quad ----------(7)
$$

上式中$$f'$$ 是频率变量，即频谱中的变量。$$f_n$$ 和 $$\tau'_n(0)$$都是参数。



可以看到，$$e^{-j2\pi \tau'_n(0) f'}$$ 这一项在求和公式里面，所以，$$|H(f',t)|$$的值就与 $$f'$$ 有关，因此，不同的频率，其衰减系数也不一样，这是频率选择性信道。



如果要想是频率非选择性信道或者叫平坦衰落信道，则需要 $$e^{-j2\pi \tau'_n(0) f'}$$  这个与求和公式无关，即与 n无关。若符号长度 $$T_{sym}$$ 远大于 最大时延，即 $$max|\tau'_n - \tau'_m|<< T_{sym}$$，则不同的路径 n，$$\tau'_n(0)$$ 都取相同的值，令 $$\tau'_0 = \tau'_n(0)$$ ，那么公式 (7) 就可以写成：


$$
H(f',t) = [\sum_{n=1}^Nc_n e^{j(2\pi f_nt + \theta_n)}] e^{-j2\pi\tau'_0 f'} \quad ----------(8)
$$



从公式 (8) 可以看出，对不同的频率$$f'$$, 其衰减系数  $$|H(f',t)|$$ 都是一样的（因为$$e^{-j2\pi\tau'_0 f'}$$模等一. 因此，这种信道就称之为频率非选择性信道或者叫平坦衰落信道.

那么这种平坦衰落信道，在时域上的表达式，同样令 $$\tau'_0 = \tau'_n(0)$$, 从公式 (6) 可以推导出：

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n)}e^{ j2\pi f_n t}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

上面这个表达式，可以理解为，在某个 t 时刻上，所有的多个路径中，延时$$\tau'$$为$$\tau'_0$$ 的才有值，其它的都为 0. 所以，公式(9)的这个冲击响应，其实只是一个单值的。



我们对这个单值的情况进行分析，实际上也就是对下图中某个颜色标注的路径进行分析：


![multi-path2.png](/figure/通信基础/无线信道的数学表示/multi-path2.png)

例如对 $$\tau_1$$进行分析，则公式(9)就变成：

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau_1) \quad -----(9.1)
$$

假设发射的信号是$$x(t)$$ ，则与公式9.1卷积后得到接收的信号 ( 注意，公式9.1中的变量是$$\tau'$$)：

$$
\begin{aligned}
	y(t) &= \int_{-\infty}^{+\infty} h(\tau',t)x(t-\tau') d\tau'  \\
	&=\int_{-\infty}^{+\infty} [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau_1)x(t-\tau') d\tau'  \\
	&=[\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] x(t-\tau_1)  \\
	&= c_1 e^{j\theta_1} e^{j2\pi f_1 t} x(t-\tau_1)+ \cdots +c_n e^{j\theta_n} e^{j2\pi f_n t} x(t-\tau_1)
\end{aligned}
$$

上式最后一行，实际上是对原始信号 x(t) 分别乘以一个复数衰减 $$c_n e^{j\theta_n}$$，然后再乘以一个频率偏移项 $$e^{j2\pi f_n t}$$然后累加。

在频域上的表现，就是原始信号x(t) 的频谱，被多个频率偏移项 $$e^{j2\pi f_n t}$$ 频移了频率，当然偏移后也要乘以一个复数衰减 $$c_n e^{j\theta_n}$$.



频域上看，就是频率被延展了，例如 x(t) 是一个单频的 sine wave, 则这个单频的 sine wave 被延展出来多个频率出来，频率点分别是原来的频率被平移了 $$f_n$$ 的频率点。这个可以理解为在频域是一个频域的冲击响应，在频域做卷积，对应于在时域就是直接相乘了。

## 平坦衰落信道的仿真方法

从公式 (9) 可以看出，发送信号经过一个延时$$\tau'_0$$ ，再乘以一个衰减系数：

$$\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$， 记为$$\mu(t)$$，即$$\mu(t) =\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$



当 N 趋于无穷时，



$$\mu(t) =\underset{n \to \infty}{\lim} \sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$ 



一个复数值的高斯随机过程（t是某个固定时刻，则称为高斯随机变量)。



具有 0 均值，方差为

$$
2\sigma^2 = Var\{\mu(t)\} =\underset{n \to \infty}{\lim} \sum_{n=1}^N E(c_n^2)
$$

??????? 这个方差的计算，为什么可以把指数部分不管？

## 一个简单的方法

采样率为 fs = 10000=10k，若时域信号有 N 个，某一次仿真的总的时域的信号个数，假如我们要传输 1秒 的数据，则数据量为 10k 个。



根据公式 9 ( 复制下来在这里）：

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n)}e^{ j2\pi f_n t}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

一个单路径的 rayleight 信道，也就是非频率选择性衰落信道，则 公式 （9）中的角度是等概率分布，多个散射路径的入射角也是等概率分布，则可用下面的matlab代码实现：

matlab 代码：请到 Github 上下载：\url{https://github.com/taichiorange/leba_math}


时域采样点的个数为 10000 个，就是我们要考虑的非常短的一段时间内。

若 rayleigh 信道有 10 个 taps ($$h(\tau')$$  有10个 taps) ，则需要$$h(\tau')$$ 与 x(t) 做卷积。



若再没有多普勒频移，则：

$$
h(\tau') = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_i)
$$

$$\tau'_i$$ 是第  i 个路径的延时，这个路径上可能有 $$N_i$$ 个不可分离的子路径组成。

当 $$N_i$$  足够大时：

$$
\begin{aligned}
	h(\tau') &= [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_i)  \\
	&= [\sum_{n=1}^N c_n cos(\theta_n) + \sum_{n=1}^N c_n sin(\theta_n)] \delta(\tau' - \tau'_i)
\end{aligned}
$$

根据中央极限定理：

$$\sum_{n=1}^N c_n cos(\theta_n)$$ 和 $$\sum_{n=1}^N c_n sin(\theta_n)$$都是高斯分布。



若有多普勒效应，则：

$$
\begin{aligned}
	h(\tau',t) &= [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \\
	&=[c_1 e^{j \theta_1} e^{j2\pi f_1 t}+....+c_N e^{j \theta_N} e^{j2\pi f_N t}] \delta(\tau' - \tau'_0)
\end{aligned}
$$

下面是总结，分四种情况讨论



在一个很小的时间范围内，例如 OFDM 的几个 symbols 时间内

## A 非频率选择性衰落

即没有离散独立路径

A.1 若没有多普勒频移

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_0)
$$

可以看到，上面公式的右侧，与时间 t 无关，所以：

$$
h(\tau') = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_0)
$$

这个是平坦衰落，所以，只有一个时延

delta 函数保证 $$\tau'$$ 只在 $$\tau'_0$$ 这个位置有值。



A.2 若有多普勒频移

多普勒频移在时间域上就与 t 有关，所以，t 不能省：

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j\theta_n}e^{j 2\pi f_n t)}] \delta(\tau' - \tau'_0)
$$

## B. 频率选择性衰落

即有多个离散路径。

要从公式（6）开始分析：

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0)) \quad -----(6)
$$

B.1  若没有多普勒频移，但有多个离散独立路径，每个离散独立路径由多个不可区分的路径组成：



可以看到，上面公式的右侧，与时间 t 无关，所以：

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau'_n(0))
$$

假设有两个离散独立路径，延时分别为$$\tau_1, \tau_2$$ 

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau_1)
$$

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau_2)
$$

B.2  若有多普勒频移，且有多个离散独立路径

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0))
$$


​	
假设有两个离散独立路径，延时分别为 $$\tau_1, \tau_2$$  

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau_1)
$$

所有时延为 $$\tau_1$$ 的多个不可分的路径构成的一个独立离散路径

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau_2)
$$

这个是平坦衰落，所以，只有一个时延，即 $$\tau'_0$$,  这个 就$$\tau'_0$$是图上的 $$\tau_1$$

delta 函数保证 $$\tau'$$ 只在 $$\tau'_0$$ 这个位置有值。