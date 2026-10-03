---
layout: default
title: " BCJR应用-ISI信道均衡"
back_url: /index.html?lang=zh
---

## BCJR应用-ISI信道均衡



录制的视频在[B站](https://www.bilibili.com/cheese/play/ss32595)



这个文章，我们讲一下有码间干扰信道的均衡问题，也算是 BCJR 算法的一个应用。

我们先看一下这个问题的背景。假设一个信道，数据发送后由于走不同的路径（可能原因之一），到达接收端有不同的延迟以及不同的衰减系数，例如 在 k=0 时刻发送了一个数据，接收端在  k=0, k=1, k=2, k=4 这几个时刻都收到了 k=0 时刻发送的数据，每一个时刻收到的数据，都对应一个衰减系数，我们这里不考虑相位的变化，只考虑幅度的衰减，则 h0, h1, h2, h4 这样四个衰减系数。

则不同时刻收到的数据可以表示为：

$$
\begin{aligned}
	r_0 &= h_0 x_0 + n_0 \\
	r_1 &= h_0 x_1 + h_1 x_0 + n_1\\
	r_2 &= h_0 x_2 + h_1 x_1 + h_2 x_0  + n_2\\
	r_3 &= h_0 x_3 + h_1 x_2 + h_2 x_1 + 0 x_0  + n_3\\
	r_4 &= h_0 x_4 + h_1 x_3 + h_2 x_2 + 0 x_1 + h_4 x_0  + n_4\\
	r_5 &= h_0 x_5 + h_1 x_4 + h_2 x_3 + 0 x_2 + h_4 x_1  + n_5\\
	r_6 &= h_0 x_6 + h_1 x_5 + h_2 x_4 + 0 x_3 + h_4 x_2  + n_6\\
	... \\
	r_k &= h_0 x_k + h_1 x_{k-1} + h_2 x_{k-2} + 0 x_{k-3} + h_4 x_{k-4} + n_k
\end{aligned}
$$

实际上就是一个卷积的过程，如果令 $$h_3=0$$， 则：

$$
r_k = \sum_{i=0}^4 h_i x_{k-i}+ n_k
$$

可以画成如下的图形：



图一:


![ISI-channel-trellis.png](/figure/卷积码编码和译码/BCJR-turbo/ISI-channel-trellis.png) 




对于这样一个信道，我们可以想到，在收到 N 个接收数据 r 后，如何估计出来 发送的数据 X ? 这就是所谓的信道均衡或者说信道 detection 的问题。
用概率公式的方式，可以表示为：

$$
p(x_0,x_1,\cdots ,x_{N-1}|r_0,r_1,\cdots, r_{N-1})   \tag{1}
$$

由于我们做的是卷积计算，因此，公式 (1) 不能写成多个概率的乘积，所以，计算量会非常大。我们可以按照每个输入时刻来计算概率：

$$
p(x_k=x|r_0,r_1,\cdots, r_{N-1})   \tag{2}
$$

即

$$
p(x_k=x|r)   \tag{3}
$$

其中 r 是一个 N 维向量.

我们可以把上面的图一，看成是码率为 1 的卷积码，那么，我们就可以用 BCJR 算法来计算公式 (2) 的概率。
用这种方法做的均衡(equalization)，是基于栅格的方法(Trellis-based method)，当然还有其他方法，例如线性滤波的方法。

从公式 (3) 出发：

$$
p(x_k=x|r)=\sum_{(p,q)\in S_x} p(\psi_k=p,\psi_{k+1}=q|r)   \tag{4}
$$

其中 $$S_x$$ 表示在 t 时刻，输入 x 引起的所有可能的状态转移.

我们接着分析公式 (4) 中的 $$p(\psi_k=p,\psi_{k+1}=q|r)$$

$$
p(\psi_k=p,\psi_{k+1}=q|r)  = \frac{p(\psi_k=p,\psi_{k+1}=q,r)}{p(r)}  \tag{5}
$$

继续分析公式 (5) 中分子的部分：

$$
\begin{aligned}
	p(\psi_k=p,\psi_{k+1}=q,r)
	&= p(\psi_k=p,\psi_{k+1}=q,r_{<k},r_k,r_{>k})  \\
	&= p(r_{<k},\psi_k=p,r_k,\psi_{k+1}=q,r_{>k})  \quad \quad  \text{时间顺序重排}  \\
	&=p(r_k,\psi_{k+1}=q,r_{>k}|r_{<k},\psi_k=p) p(r_{<k},\psi_k=p) \quad \quad  \text{条件概率，前两个}  \\
	&=p(r_{<k},\psi_k=p) p(r_k,\psi_{k+1}=q,r_{>k}|\psi_k=p)  \quad \quad  \text{马尔科夫性}  \\
	&=p(r_{<k},\psi_k=p) p(r_{>k}|r_k,\psi_{k+1}=q,\psi_k=p)p(r_k,\psi_{k+1}=q|\psi_k=p)  \quad  \text{条件概率，前两个}  \\
	&=p(r_{<k},\psi_k=p) p(r_{>k}|\psi_{k+1}=q)p(r_k,\psi_{k+1}=q|\psi_k=p)  \quad  \text{马尔科夫性}\\
	&=p(r_{<k},\psi_k=p) p(r_k,\psi_{k+1}=q|\psi_k=p) p(r_{>k}|\psi_{k+1}=q)  \quad  \text{重排}\\
	&:= \alpha(p) \gamma(p,q) \beta(q)
\end{aligned}  \tag{6}
$$

对于 $$\alpha$$ 概率

$$
\begin{aligned}
	\alpha_k(q) 
	&=p(r_{<k},\psi_k=q)  \\
	&= \sum_{p->q}p(r_{<k},\psi_k=q,\psi_{k-1}=p)  \quad \text{全概率/边缘概率}  \\
	&= \sum_{p->q}p(r_{<k-1},r_{k-1},\psi_k=q,\psi_{k-1}=p)   \\
	&= \sum_{p->q}p(r_{<k-1},\psi_{k-1}=q,r_{k-1},\psi_k=p)   \quad \text{按时间重排}\\
	&= \sum_{p->q}p(r_{k-1},\psi_k=q|r_{<k-1},\psi_{k-1}=p)p(r_{<k-1},\psi_{k-1}=p)   \quad \text{前两个，条件概率}\\
	&= \sum_{p->q}p(r_{k-1},\psi_k=q|\psi_{k-1}=p)p(r_{<k-1},\psi_{k-1}=p)   \quad \text{马尔科夫性}\\
	&= \sum_{p->q}p(r_{<k-1},\psi_{k-1}=p)p(r_{k-1},\psi_k=q|\psi_{k-1}=p)   \quad \text{重排}\\
	&=\sum_{p->q} \alpha_{k-1}(p) \gamma_{k-1}(p,q)
\end{aligned} \tag{7}
$$

对于 $$\beta$$ 概率

$$
\begin{aligned}
	\beta_k(p)
	&= p(r_{>k-1}|\psi_k=p)  \\
	&= \sum_{p->q} p(r_{>k-1},\psi_{k+1}=q|\psi_k=p)  \quad \text{全概率/边缘概率}  \\
	&= \sum_{p->q} p(r_k,r_{>k},\psi_{k+1}=q|\psi_k=p)  \quad  \\
	&= \sum_{p->q} p(r_k,\psi_{k+1}=q,r_{>k}|\psi_k=p)  \quad  \text{按时间重排}\\
	&= \sum_{p->q} p(r_{>k}|r_k,\psi_{k+1}=q,\psi_k=p)p(r_k,\psi_{k+1}=q|\psi_k=p)  \quad \text{前两个，条件概率}\\
	&= \sum_{p->q} p(r_{>k}|\psi_{k+1}=q)p(r_k,\psi_{k+1}=q|\psi_k=p)  \quad \text{马尔科夫性}\\
	&= \sum_{p->q} p(r_k,\psi_{k+1}=q|\psi_k=p) p(r_{>k}|\psi_{k+1}=q)  \quad \text{重排}\\
	&= \sum_{p->q} \gamma_k(p,q) \beta_{k+1}(q)
\end{aligned} \tag{8}
$$

因此，把公式 (6) 代入公式 (4)

$$
p(x_k=x|r)=\sum_{(p,q)} \alpha(p) \gamma(p,q) \beta(q)
$$

$$\alpha, \beta$$ 概率又可以根据公式 (7) (8) 递推得到。

关于 $$\gamma$$ 概率，可以根据先验概率和信道给的信息计算出来：

$$
\begin{aligned}
	\gamma(p,q) 
	&= p(r_k,\psi_{k+1}=q|\psi_k=p)  \\
	&= p(r_k|\psi_{k+1}=q,\psi_k=p) p(\psi_{k+1}=q|\psi_k=p)  \\
	&= p(r_k|x_k) p(x_k)
\end{aligned}  \tag{9}
$$

所以，我们把这种有码间干扰 (Inter-Symbol Interference)的信道，可以看成等效的码率为1的卷积码，从而可以使用 BCJR 算法做概率计算，从而做信道detection(也成为信道均衡  channel equalization).

这也算 BCJR 算法的一个具体应用吧。