---
layout: default
title: "瑞利分布 rayleigh"
back_url: /index.html?lang=zh
---

# 瑞利分布 rayleigh
在无线通信的信道建模，高斯白噪声模拟 等方面，我们都会碰到 瑞利( Rayleigh)分布 , 也经常碰到 circularly symmetric random variable 的说法。



什么是circularly symmetric random variable ？ 就是形如 $$Z = X + j Y$$ 的随机变量，其中  X, Y 都是均值为0 相互独立具有相同高斯分布的随机变量。 则这个复随机变量的模长 |Z| 就符合 瑞利分布，其概率密度为：

$$
p(z) = \frac{z}{\sigma^2} e^{-\frac{z^2}{2\sigma^2}}, z \ge 0
$$

另外，角度 $$\theta$$ 满足$$[0,2\pi]$$ 之间的均匀分布。

为什么这个复数随机变量称之为 circularly symmetric ?

我们把复数的实部和虚部分别看成两个实值随机变量，均值都是 0 的独立同分布（高斯分布）。则其联合概率:

$$
p(x,y) = p(x)p(y) = 
\left ( \frac{1}{\sqrt{2\pi}\sigma}   e^{-\frac{x^2}{2\sigma^2}}\right )
\left ( \frac{1}{\sqrt{2\pi}\sigma}   e^{-\frac{y^2}{2\sigma^2}}\right )
= \frac{1}{\sqrt{2\pi}\sigma}   e^{-\frac{x^2+y^2}{2\sigma^2}}
$$

我们把这个联合概率的图，画在三维空间中，z 轴是概率的值，另外两个轴分布代表实部的 x 和虚部的 y，则其图形如下（摘自 wiki ):

![瑞利Rayleigh分布-1.png](/figure/通信基础/瑞利Rayleigh分布/瑞利Rayleigh分布-1.png)



可以看到 图形是对称的，且是 circularly.



转成极坐标


令

$$
\begin{aligned}
	Z &= \sqrt{X^2 + Y^2} \\
	\theta &= \text{tan}^{-1} \left( \frac{Y}{X}\right)
\end{aligned}
$$

则

$$
dxdy = zdzd\theta
$$

![瑞利Rayleigh分布-2.png](/figure/通信基础/瑞利Rayleigh分布/瑞利Rayleigh分布-2.png)



那么

$$
\begin{aligned}
	P(x\le X + dx, y\le Y + dy)
	&= P(z \le Z + dz, \theta \le \Theta + d\theta) \\
	& = \frac{1}{2\pi \sigma^2} e^{-\frac{z^2}{2\sigma^2}} z dz d\theta \\
	&=\frac{z}{\sigma^2} e^{-\frac{z^2}{2\sigma^2}} dz \frac{1}{2\pi} d\theta
\end{aligned}
$$

所以:

$$
p(z,\theta) = \frac{1}{2\pi \sigma^2} e^{-\frac{z^2}{2\sigma^2}}
$$

因为  相互独立，所以：

$$
\begin{aligned}
	p(z) &= \frac{z}{\sigma^2} e^{-\frac{z^2}{2\sigma^2}}, z \ge 0 \\
	p(\theta) &= \frac{1}{2\pi}, -\pi \le \theta \le \pi
\end{aligned}
$$

## 仿真



产生两个 0均值，相互独立，单位方差的高斯随机变量

用 hist() 函数，计算  $$z,\theta$$ 的概率密度



然后，用我们的理论公式也计算出来概率，画图比较，发现两者重合。


![瑞利Rayleigh分布-3.png](/figure/通信基础/瑞利Rayleigh分布/瑞利Rayleigh分布-3.png)

![瑞利Rayleigh分布-4.png](/figure/通信基础/瑞利Rayleigh分布/瑞利Rayleigh分布-4.png)


Matlab/Octave 代码

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

 本文基本是翻译的这个文章：Deriving PDF of Rayleigh random variable (dsplog.com) 