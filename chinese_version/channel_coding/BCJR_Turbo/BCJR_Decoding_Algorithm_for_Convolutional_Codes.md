---
layout: default
title: " 卷积码的 BCJR 译码算法 (一)"
back_url: /index.html?lang=zh
---
## 卷积码的 BCJR 译码算法 (一)

录制的视频在[B站](https://www.bilibili.com/cheese/play/ss32595)

本文主要讲卷积码的 BCJR 译码算法。需要知道卷积码的基本原理和一些概率知识。我们会推导译码算法的公式，并以具体的例子来讲解 BCJR 的译码过程。

我们知道，卷积码是一种状态机，卷积码编码器中寄存器的数据就是状态，当前状态已知的条件下，当前状态的输出，至于当前的输入有关，而与过去的状态和输入无关，这就是马尔可夫（Markov）性。

在收到的数据为:

$$
r=(r_0,r_1,\cdots, r_N)
$$

则，为了译码第 t 时刻的发送比特，我们计算这个后验概率

$$
P(X_t = x|r)
$$

在 t 时刻，当前状态为

$$
\psi_t=p
$$

下一个状态为

$$
\psi_{t+1} = q
$$

我们把输入为 0 时的状态转移的集合，记为

$$
S_0
$$

我们把输入为 1 时的状态转移的集合，记为

$$
S_1
$$

则：

$$
P(X_t=0|r) = \sum_{S_0}P(\psi_t,\psi_{t+1}|r)
$$

和

$$
P(X_t=1|r) = \sum_{S_1}P(\psi_t,\psi_{t+1}|r)
$$

所以，关键点是计算如下这个概率：

$$
P(\psi_t=p,\psi_{t+1}=q|r)
$$

我们做一下推导：

$$
\begin{aligned}
	P(\psi_t=p,\psi_{t+1}=q|r) = \frac{p(\psi_t=p,\psi_{t+1}=q,r)}{p(r)}  \\
\end{aligned}
$$

因此，我们需要计算这个联合概率：

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r)
\end{aligned}
$$

我们把 r 分成三部分，一部分是 t 时刻以前的接收数据，一部分是 t 时刻接收的数据，一部分是 t 时刻之后接收的数据：

$$
r = r_{<t}  \cup r_t \cup  r_{>t}
$$

则：

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r)  
	&= p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t, r_{>t})  \\
	\\
	&= p(r_{>t} | \psi_t=p,\psi_{t+1}=q,r_{<t}, r_t ) p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)    \quad \quad  \text{(条件概率)}
	\\
	\\
	&= p(r_{>t} | \psi_{t+1}=q ) p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)    \quad \quad  \text{(马尔可夫性)}
\end{aligned}   \tag{1}
$$

我们再来分析公式 (1) 中后半部分

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)  &= p(\psi_{t+1}=q, r_t |  \psi_t=p, r_{<t}) p( \psi_t=p , r_{<t})  \\
	\\
	&=p(\psi_{t+1}=q, r_t |  \psi_t=p) p( \psi_t=p , r_{<t})    \quad \quad  \text{(马尔可夫性)}
\end{aligned}
\tag{2}
$$

把 (2) 代入 (1) 有：

$$
p(\psi_t=p,\psi_{t+1}=q,r)  =p( \psi_t=p , r_{<t}) p(\psi_{t+1}=q, r_t |  \psi_t=p)  p(r_{>t} | \psi_{t+1}=q ) \tag{3}
$$

我们来看一下公式 (3) 的含义：
我们要分析当前状态为 p，下一个状态为 q ，且接收到数据为 r  这个联合概率，那么这个概率由三部分相乘得到：
(1) t 时刻之前接收到的数据为 $$r_{<t}$$，且到达了 t 时刻的状态 为 p 的概率：

$$
p( \psi_t=p , r_{<t})
$$

(2) t 时刻状态为 p 的条件下，到达下一个状态 为 q 且收到数据为 $$r_t$$ 的概率

$$
p(\psi_{t+1}=q, r_t |  \psi_t=p)
$$

(3) t+1时刻状态为 q 的条件下，t 时刻之后的输出为 $$r_{>t}$$ 的概率

$$
p(r_{>t} | \psi_{t+1}=q )
$$

那么：

$$
P(\psi_t=p,\psi_{t+1}=q|r)  = \frac{1}{p(r)}   p( \psi_t=p , r_{<t}) p(\psi_{t+1}=q, r_t |  \psi_t=p)  p(r_{>t} | \psi_{t+1}=q )
$$

我们举个例子来说明：

我们使用卷积码：

$$
G(x) = \frac{1}{1+x^2}
$$

则结构如下：


![convolutionary_code_encoder_1.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_code_encoder_1.png) 


也可以画成下图，两者是等价的：


![convolutionary_code_encoder_2.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_code_encoder_2.png) 


则其状态转移栅格图为：

![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png) 


假如我们要编码 10 个比特，从状态 00 出发，编码结束后回到 00 状态，则所有可能路径有：

![encoder_10bits_trellis.png](/figure/卷积码编码和译码/BCJR-turbo/encoder_10bits_trellis.png) 


假如我们编码的比特  x=[1, 1, 0, 0, 1, 0,  1, 0, 1, 1]

则输出比特为  V=[11  11  01  01  10  01  11   01  10  10]

编码路径如上图中红色所示。



我们假如要计算如下这个概率

$$
P(X_6=1|r_0,r_1,\cdots,r_9)
$$

注意上面公式中每个 $$r_i$$  其实是接收到的两个数据，分别对应 $$v_t^{(0)}, v_t^{(1)}$$ 通过信道发送后得到的数据（这里要稍微注意一下，我们用 BPSK，则 0--> -1,  1---->1 ， 发送的是 -1 或者 +1).

输入 为 比特 1， 对应的状态转移有如下几种情况：

$$
\begin{aligned}
\psi_6=0 \quad \quad  ---->  \psi_7=2 \\
\psi_6=1 \quad \quad  ---->  \psi_7=0 \\
\psi_6=2 \quad \quad  ---->  \psi_7=3 \\
\psi_6=3 \quad \quad  ---->  \psi_7=1 \\
\end{aligned}
$$

所以：

$$
\begin{aligned}
	P(X_6=1|r_0,r_1,\cdots,r_9) = & P(\psi_6=0,\psi_7=2|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=1,\psi_7=0|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=3,\psi_7=1|r_0,r_1,\cdots,r_9) 
\end{aligned}
\tag{4}
$$

那么公式 (4) 中任何一个求和都可以按照下面这个例子来展开，我们以 $$P(\psi_6=2,\psi_7=3 \vert r_0,r_1,\cdots,r_9)$$ 为例：

$$
\begin{aligned}
	P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9) &= p( \psi_6=2 , r_{<6}) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_{>6} | \psi_7=3 ) \\
	&=p( \psi_6=2 , r_0,r_1,\cdots,r_5) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_7,\cdots,r_9 | \psi_7=3 )
\end{aligned}
$$