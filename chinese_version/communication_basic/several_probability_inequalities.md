---
layout: default
title: "几个概率不等式"
back_url: /chinese_version.html
---
# 几个概率不等式
## markov 和 Chebyshev限


这篇小文章讲一下概率中的几个界，也就是几个不等式。是为了给后面通信中的一些理论提供基础，例如极化码中极化特性的推导，需要用到 Chernoff-Hoeffding 不等式。



我们先讲讲这些界的出发点，即马尔可夫不等式 (Markov Inequality).

X 是一个非负的随机变量，均值是 $$\mu$$, 那么 $$\forall a > 0$$:

$$
P(X\ge a) \le \frac{\mu}{a}
$$

证明：

$$
\begin{aligned}
\mu = E(X) = \int_0^{+\infty} xF_X(x)dx = \int_0^a xF_X(x)dx + \int_a^{+\infty} xF_X(x)dx \ge
\int_a^{+\infty} xF_X(x)dx  \\
\ge \int_a^{+\infty} aF_X(x)dx = a  \int_a^{+\infty} F_X(x)dx = a P(X\ge a)
\end{aligned}
$$

简单整理后得到公式 (1).


如果再知道方差，那么我们可以得到更好的估计（Chebyshev 不等式)：

$$
P(|X-\mu|\ge a) \le \frac{\sigma^2}{a^2}
$$

其中 $$\sigma^2$$ 是随机变量 X 的方差。

证明：

构建一个新的随机变量

$$
Y = (X-\mu)^2
$$

则根据方差的定义

$$
E(Y) = E[(X-\mu)^2] = \sigma^2
$$

使用 Markov 不等式：

$$
P(Y\ge a^2) \le \frac{E(Y)}{a^2} = \frac{\sigma^2}{a^2}
$$

则：

$$
P(|X-\mu|\ge a) = P((X-\mu)^2 \ge a^2) = P(Y\ge a^2) \le \frac{\sigma^2}{a^2}
$$

注意：

​      在公式 (3) 中，不需要有 $$X>0$$ 这个条件。
## Chernoff bounds 初步:用高斯分布来演示
这个文章讲一下 Chernoff Bounds，这个限其实是一种思路，不是一种固定的限，所以，如果上网搜的话，会有各种各样的不等式都被称为 Chernoff Bounds.   另外，名称上也有叫  Chernoff-Hoeffding Bounds,  Chernoff 首先阐述了这种思想，用在抛硬币上，后来 Hoeffding 推广到更一般的情况。



这个限的思路如下：

1). 把不等式转换成 e 的指数形式，并引入一个自由变量

2). 使用 Markov 不等式

3). 应用其它各种已知的条件，例如独立性，例如其他纯数学上的不等式

4).把限看成是 t 的函数，求解以 t 为变量的这个函数的极值（求最小值，以便让上界尽可能地低）

我们以正态分布为例子来把上面的思路演示一下：

X 是满足 $$N(\mu,\sigma^2)$$ 的随机变量，则：

我们要看的概率是：

$$
P(X\ge a)
$$

1). 我们把上面的概率换成：

$$
P(X \ge a) = P(e^{tX} \ge e^{ta}) \quad \quad where \quad \forall t>0 \tag{1}
$$

2). 使用 Markov 不等式

$$
P(e^{tX} \ge e^{ta}) \le \frac{E(e^{tX})}{e^{ta}}  \tag{2}
$$

现在，来计算公式 (2) 中的数学期望

$$
\begin{aligned}
	E(e^{tX}) & = \int_{-\infty}^{+\infty} e^{tx} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu)^2}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu)^2 - 2\sigma^2 tx}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{x^2 - 2\mu x +\mu^2 - 2\sigma^2 tx}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{x^2 - 2(\mu +\sigma^2 t) x  + \mu^2}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x - (\mu +\sigma^2 t))^2 - (\mu +\sigma^2 t)^2 + \mu^2}{2\sigma^2}} dx \\ \\
	&= e^{ \frac{ (\mu +\sigma^2 t)^2 - \mu^2} {2\sigma^2} }\int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x - (\mu +\sigma^2 t))^2 }{2\sigma^2}} dx \\ \\
	& = e^{ \frac{ (\mu +\sigma^2 t)^2 - \mu^2} {2\sigma^2}} \\ \\
	& = e^{\frac{\sigma^2 t^2}{2} + \mu  t}
\end{aligned}  \tag{3}
$$

把公式 (3) 代入 公式 (2):

$$
P(e^{tX} \ge e^{ta}) \le \frac{e^\frac{\sigma^2 t^2}{2} + \mu  t}{e^{ta}} = e^{\frac{\sigma^2 t^2}{2} + \mu  t - ta}  \tag{4}
$$

我们的目标，是让公式 (4) 中右侧尽可能地小，也就是让这个概率的上界尽可能小。

我们对公式 (4) 中右侧的指数部分，求最小值：

$$
\frac{\partial(\frac{\sigma^2 t^2}{2} + \mu  t - ta) } { \partial t} = \sigma^2 t + \mu  - a = 0  \tag{5}
$$

所以得到：

$$
t = \frac{a-\mu}{\sigma^2}   \tag{6}
$$

把 (6) 代入 (4) 有：

$$
P(e^{tX} \ge e^{ta}) \le e^{-\frac{(a-\mu)^2}{2\sigma^2}}  \tag{7}
$$

即：

$$
P(X\ge a) \le  e^{-\frac{(a-\mu)^2}{2\sigma^2}}  \tag{8}
$$

我们用标准正态分布来代入看看$$N(0,1)$$

$$
P(X\ge a) \le  e^{-\frac{a^2}{2}}  \tag{9}
$$

其中 a > 0，是因为 公式 (6) 中要求 t > 0

## Chernoff bounds:用于泊松过程
这个文章讲一下基于泊松过程推导一下 Chernoff-Hoeffding Bounds.

定义随机变量：

$$
X_i =
\begin{cases}
	1 & p_i \\
	0 & 1-p_i
\end{cases}
$$

要求上面的随机变量相互独立。

定义随机变量：

$$
X = \sum_{i=1}^n X_i
$$

则我们想知道如下这个概率：

$$
P(X \ge m)
$$

其中 m 大于 X 的均值（数学期望），小于等于 n.

下面展开推导过程，具体细节会在视频中讲解。

$$
P(X \ge m ) = P(e^{tx} \ge e^{tm}) \le \frac{E(e^{tx})}{e^{tm}}  \tag{1}
$$

其中： t > 0

$$
E(e^{tx}) = E(e^{t\sum_{i=1}^n X_i}) = E(\prod_{i=1}^n e^{tX_i}) = \prod_{i=1}^n E(e^{tX_i})  \tag{2}
$$

其中：

$$
E(e^{tX_i}) = p_i e^{t\times 1} + (1-p_i) e^{t\times 0} = p_i e^t + 1-p_i \tag{3}
$$

把公式 (3) 代入公式 (2):

$$
E(e^{tx}) = \prod_{i=1}^n(p_i e^t + 1-p_i )  \tag{4}
$$

用 Arithmatic Mean/Geometric Mean Inequality （算术几何平均不等式）：

$$
\prod_{i=1}^n(p_i e^t + 1-p_i )  \le \left (    \frac{\sum_{i=1}^n(p_i e^t + 1-p_i ) }{n}               \right )^n  = (pe^t+1-p)^n  \tag{5}
$$

其中 : $$p = \frac{\sum_i=1^n p_i}{n}$$

把公式 (5) 代入公式 (4) :

$$
E(e^{tx}) \le (pe^t+1-p)^n  \tag{6}
$$

公式 (6) 代入公式 (1):

$$
P(X \ge m ) \le \frac{(pe^t+1-p)^n}{e^{tm}} = e^{ln \left \{  \frac{(pe^t+1-p)^n}{e^{tm}}  \right \} }
= e^{ n ln (pe^t+1-p)-tm}  \tag{7}
$$

求公式 (7) 右边的最小值：

$$
\frac {\partial \left [ n ln (pe^t+1-p)-tm \right ]}   { \partial t} = \frac{n}{pe^t+1-p} pe^t - m = 0
$$

经过推导有：

$$
e^t = \frac{(1-p)m}{(n-m)p}   \tag{8}
$$

所以：

$$
t = ln \frac{(1-p)m}{(n-m)p}  \tag{9}
$$

把公式 (8) 和 (9) 代入 (7):

$$
\begin{aligned}
	P(X \ge m ) & \le e^{ n ln \{\frac{(1-p)m}{(n-m) }+ 1 - p\}  - m ln\frac{(1-p)m}{(n-m)p} } \\  \\
	& = e^{ n ln \frac{(1-p)n}{(n-m) }  - m ln\frac{(1-p)m}{(n-m)p} } \\  \\
	&= e^{n\left \{    ln \{\frac{(1-p)}{(1-\frac{m}{n}) }\}  - \frac{m}{n} ln\frac{(1-p) \frac{m}{n} }{(1-\frac{m}{n})p}   \right \}}   \\  \\
	&= e^{n\left \{   (1-\frac{m}{n}) ln \frac{(1-p)}{(1-\frac{m}{n}) }  - \frac{m}{n} ln\frac{\frac{m}{n} }{ p}   \right \}} \\
	&= e^{-n\left \{   (1-\frac{m}{n}) ln \frac{(1-\frac{m}{n})}{(1-p) }  + \frac{m}{n} ln\frac{\frac{m}{n}}{p }   \right \}} \\ \\
	&= e^{-n D(\frac{m}{n}||p)}    
\end{aligned}  \tag{10}
$$

把公式 (10) 写成不是 e 的指数的形式:

$$
\begin{aligned}
	P(X \ge m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{11}
$$

## Chernoff bounds:用于泊松过程-不同形式


m 大于 E(X)，则：

$$
\begin{aligned}
	P(X \ge m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{1}
$$

用上一个文章中的推导过程，如果 m 小于 E(X)，则：

$$
\begin{aligned}
	P(X \le m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{2}
$$

两个公式看起来是一样的。

如果把 m 换成如下的表达式：

$$
m = \mu + \lambda   \tag{3}
$$

则：m 大于 E(X)

$$
\frac{m}{n} = \frac{\mu+\lambda}{n} = \frac{u}{n}+\frac{\lambda}{n} = p + \varepsilon
$$

m 小于 E(X)

$$
\frac{m}{n} = \frac{\mu-\lambda}{n} = \frac{u}{n}-\frac{\lambda}{n} = p - \varepsilon
$$

则公式 （1）和 (2) 就可以写成：m 大于 E(X)

$$
\begin{aligned}
	P(X \ge m )=P(\frac{X}{n} \ge p+\varepsilon ) & \le \left \{  \left [\frac{(1-p-\varepsilon)}{(1-p) } \right  ]^{(1-p-\varepsilon)}
	\left [ \frac{p+\varepsilon}{p } \right  ]^{p+\varepsilon}
	\right \} ^{-n}
\end{aligned}  \tag{4}
$$

以及: m 小于 E(X)

$$
\begin{aligned}
	P(X \le m )=P(\frac{X}{n} \le p-\varepsilon ) & \le \left \{  \left [\frac{(1-p+\varepsilon)}{(1-p) } \right  ]^{(1-p+\varepsilon)}
	\left [ \frac{p-\varepsilon}{p } \right  ]^{p-\varepsilon}
	\right \} ^{-n}
\end{aligned}  \tag{5}
$$

## 有最小值的证明

$$
e^{nln(pe^t+1-p) -tm}  \tag{1}
$$

证明公式 (1) 具有最小值，因为 e 的指数是单调函数，所以，只需证明指数部分有最小值即可。对指数部分求导数：

$$
\frac{\partial ({nln(pe^t+1-p) -tm}) }{\partial t} = n \frac{1}{pe^t+1-p} p e^t - m  \tag{2}
$$

当 

$$
e^t = \frac{(1-p)m}{(n-m)p}  \tag{3}
$$

公式 (2) 等于 0. 只有一个极值点。

再对公式 (2) 求导：

$$
\frac{\partial(n \frac{1}{pe^t+1-p} p e^t - m)}{\partial t} = \frac{npe^t(pe^t+1-p) - npe^t pe^t}{(pe^t+1-p)^2} = \frac{npe^t(1-p)}{(pe^t+1-p)^2} > 0  \tag{4}
$$

所以，公式 (1) 在 (3) 这个点上是最小值。

代码：请到 Github 上下载：\url{https://github.com/taichiorange/leba_math}

![05-有最小值的证明.png](/figure/通信基础/05-有最小值的证明.png)

## Chernoff bounds另外一种推导结果

这个文章再推导一种不同的 Chernoff bounds。

$$
P(X \ge (1+\delta)\mu) = P(e^{tX} \ge e^{t(1+\delta)\mu})  \le \frac{E(e^{tX})}{e^{t(1+\delta)\mu}}  \tag{1}
$$

其中 $$\delta > 0$$ 并且 

$$\mu = E(X)  = E(\sum_{i=1}^n X_i) = \sum_{i=1}^n E(X_i) = \sum_{i=1} p_i$$

进一步推导公式 (1) 中的那个数学期望：

$$
E(e^{tX})=E(e^{t\sum_{i=1}^n X_i}) = \prod_{i=1}^n E(e^{t X_i}) \tag{2}
$$

其中

$$
E(e^{t X_i}) = p_i e^{t\times 1} + (1-p_i)e^{t\times 0} \tag{3} = p_i e^t + 1-p_i = 1 + p_i (e^t -1 )
$$

从这一步开始，用了与之前视频/文章不同的缩放不等式，上一次是用代数几何平均不等式，这次是使用：

$$
1 + x \le e^x, \quad \text{当}  \quad  x \ge 0
$$

所以，公式 (3) 中的：

$$
1 + p_i (e^t -1 ) \le e^{p_i (e^t -1 )}  \tag{4}
$$

把公式 (4) 代入公式  (3):

$$
E(e^{t X_i}) \le e^{p_i (e^t -1 )} \tag{5}
$$

把公式 (5) 代入公式 (2):

$$
E(e^{tX}) \le \prod_{i=1}^n e^{p_i (e^t -1 )} = e^{ (e^t -1 ) \sum_{i=1}^n p_i } = e^{\mu (e^t -1 )} \tag{6}
$$

把公式 (6) 代入公式 (1):

$$
P(X \ge (1+\delta)\mu) \le \frac{e^{\mu (e^t -1 )}}{e^{t(1+\delta)\mu}} = e^{\mu [e^t -1 - t(1+\delta) ]} \tag{7}
$$

求公式 (7) 的最小值：

$$
\frac{\partial{\mu [e^t -1 - t(1+\delta) ]}}{\partial t} = e^t - (1+\delta) = 0  \\ \\
e^t = 1 + \delta \\
t = ln(1+\delta)
\tag{8}
$$

把公式 (8) 代入公式 (7):

$$
P(X \ge (1+\delta)\mu) \le e^{\mu[1+\delta - 1 - (1+\delta)(ln(1+\delta))]} = e^{\mu[\delta  - (1+\delta)(ln(1+\delta))]}  \\
= \left (\frac{e^\delta}{(1+\delta)^{(1+\delta)}} \right )^\mu   \tag{9}
$$

从这里出发，还有两种方法进一步缩放

方法 1：

如果 $$\delta > 2e-1$$

则 $$1 + \delta > 2e$$  ,  代入公式 (9)

$$
P(X \ge (1+\delta)\mu) < \left (\frac{e^\delta}{(2e)^{(1+\delta)}} \right )^\mu < \left (\frac{e^\delta}{(2e)^\delta} \right )^\mu  = e^{-\mu \delta}
$$

方法 2:

因为 $$ln(1+\delta) \ge \frac{\delta}{1+\delta/2}$$

所以，公式 (9)

$$
P(X \ge (1+\delta)\mu) \le  e^{\mu[\delta  - (1+\delta)(\frac{\delta}{1+\delta/2})]} =  e^{-\mu \frac{\delta^2}{1+\delta}}
$$

对于  $$P(X \le (1-\delta)\mu)$$ 用同样的方法可以证明，把前面的 t 换成 -t ，结果就是把公式(9) 中的 $$\delta$$ 换成  $$-\delta$$   ，可得到：

$$
P(X \le (1-\delta)\mu)  \le \left (\frac{e^{-\delta}}{(1-\delta)^{(1-\delta)}} \right )^\mu   \tag {10}
$$

再用：

$$
ln(1-\delta) = -\delta - \frac{\delta^2}{2} - \frac{\delta^3}{3}-\cdots  \\
\begin{aligned}
	(1-\delta)ln(1-\delta) = &-\delta - \frac{\delta^2}{2} - \frac{\delta^3}{3}-\cdots  \\
	&\quad \quad + \delta^2 + \frac{\delta^3}{2} + \frac{\delta^4}{3}+\cdots \\
	& = -\delta + \frac{\delta^2}{2} +  \frac{\delta^3}{6} + \cdots \\
	& \ge -\delta + \frac{\delta^2}{2}
\end{aligned}
$$

则：

$$
(1-\delta)^{(1-\delta)} \ge e^{-\delta + \frac{\delta^2}{2}}  \tag {11}
$$

把 (11) 代入 (10):

$$
P(X \le (1-\delta)\mu)  \le  \left (\frac{e^{-\delta}}{e^{-\delta + \frac{\delta^2}{2}}  } \right )^\mu    = e^{-\frac{\delta^2}{2}\mu} \tag {12}
$$

可以看到最终都能是随着 $$\delta$$ 递增而呈指数衰减.