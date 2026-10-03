---
layout: default
title: " 卷积码的 BCJR 译码算法 (三)"
back_url: /index.html?lang=zh
---

## 卷积码的 BCJR 译码算法 (三)--计算 $$\alpha$$

录制的视频在[B站](https://www.bilibili.com/cheese/play/ss32595)

前面文章的分析，已经推导出如下这个公式：

$$
P(\psi_t=p,\psi_{t+1}=q|r)  = \frac{1}{p(r)} \times    p( \psi_t=p , r_{<t}) \times p(\psi_{t+1}=q, r_t |  \psi_t=p) \times  p(r_{>t} | \psi_{t+1}=q )                 \tag{1}
$$

进一步简写为

$$
P(\psi_t=p,\psi_{t+1}=q|r) = \alpha_t(p) \gamma_t(p,q) \beta_{t+1}(q)   \tag{2}
$$

其中

$$
\gamma_t(p,q) =  p(\psi_{t+1}=q, r_t |  \psi_t=p)
$$

已经可以计算出来。下面来分析另外两项如何计算。

我们先分析一下如何递推计算 $$\alpha_t(q)$$  (这里我们换了一个字母，把 p 换成了 q，以便后面分析时，状态都是从 p-->q 进行转移的）。
根据前面的公式

$$
\begin{aligned}
	\alpha_{t+1}(q) &= p(\psi_{t+1}=q,r_{<t+1})   \\  \\
	&= p(\psi_{t+1}=q,r_t, r_{<t})  \\ \\
	&=\sum_p  p(\psi_t=p,\psi_{t+1}=q,r_t, r_{<t})  \quad \quad \quad  (边缘概率公式)\\
	&=\sum_p  p(\psi_{t+1}=q,r_t|\psi_t=p, r_{<t}) p(\psi_t=p, r_{<t})  \quad \quad \quad  (条件概率公式)\\
	&=\sum_p  p(\psi_{t+1}=q,r_t|\psi_t=p) p(\psi_t=p , r_{<t})  \quad \quad \quad  (马尔可夫性)\\
	&=\sum_p  p(\psi_t=p , r_{<t})  p(\psi_{t+1}=q,r_t|\psi_t=p)   \quad \quad \quad  (调整顺序)\\
	&=\sum_p  \alpha_t(p) \lambda_t(p,q) 
\end{aligned}
$$

至此，我们得到了一个递推公式：

$$
\alpha_{t+1}(q) =\sum_p  \alpha_t(p) \lambda_t(p,q)   \tag{3}
$$

这个递推公式可以这样想：
t+1 时刻状态为 q, 且知道 t+1 时刻之前所有的接收数据，那么，从 t 时刻有很多个状态能走到 t+1 时刻的 q 状态，则这些能走到的路径的概率都加在一起，就是 t+1 时刻我们关心的 $$\alpha$$概率，用下图可以形象地表达出来：




![alpha_recursive_calculate.png](/figure/卷积码编码和译码/BCJR-turbo/alpha_recursive_calculate.png) 



举个例子，例如 t=6 时刻，令 t+1=7时刻的状态 q=3，根据状态栅格图，有状态 2 和状态 3 会转移到状态 3

![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png) 

则:

$$
\begin{aligned}
	\alpha_{t+1}(q) &= \alpha_7(3) = \sum_{p \in \{2,3\}}   \alpha_6(p) \lambda_6(p,3) \\
	\\
	&= \alpha_6(2) \lambda_6(2,3) + \alpha_6(3) \lambda_6(3,3)
\end{aligned}   \tag{4}
$$

然后公式 (4) 中的 $$\alpha_6(2),\alpha_6(3)$$  继续用递推公式计算：

$$
\begin{aligned}
	\alpha_6(2) &= \sum_{p \in \{0,1\}}   \alpha_5(p) \lambda_5(p,2) \\
	\\
	&= \alpha_5(0) \lambda_5(0,2) + \alpha_5(1) \lambda_6(1,2)
\end{aligned}   \tag{5}
$$

和

$$
\begin{aligned}\alpha_6(3) &= \sum_{p \in \{2,3\}}   \alpha_5(p) \lambda_5(p,3) \\
	\\
	&= \alpha_5(2) \lambda_5(2,3) + \alpha_5(3) \lambda_5(3,3)
\end{aligned}   \tag{6}
$$

实际上，在计算时，我们知道是从状态 0 开始的，所以， $$\alpha_0(0) = 1$$ ，其他状态的概率为 0，所以：

$$
\alpha_0(0) = 1 \\
\alpha_0(1) = 0 \\
\alpha_0(2) = 0 \\
\alpha_0(3) = 0
$$

在时刻 1：

$$
\alpha_1(0) =  \sum_{p \in \{0,1\}}   \alpha_0(p) \lambda_0(p,0) = \alpha_0(0) \lambda_0(0,0) +\alpha_0(1) \lambda_0(1,0)   \\ \quad \\
\alpha_1(1) =  \sum_{p \in \{2,3\}}   \alpha_0(p) \lambda_0(p,1) = \alpha_0(2) \lambda_0(2,1) +\alpha_0(3) \lambda_0(3,1)   \\ \quad \\
\alpha_1(2) =  \sum_{p \in \{0,1\}}   \alpha_0(p) \lambda_0(p,2) = \alpha_0(0) \lambda_0(0,2) +\alpha_0(1) \lambda_0(1,2)   \\ \quad \\
\alpha_1(3) =  \sum_{p \in \{2,3\}}   \alpha_0(p) \lambda_0(p,3) = \alpha_0(2) \lambda_0(2,3) +\alpha_0(3) \lambda_0(3,3)
$$

同理，在时刻 2，用时刻 1 的结果来计算：

$$
\alpha_2(0) =  \sum_{p \in \{0,1\}}   \alpha_1(p) \lambda_1(p,0) = \alpha_1(0) \lambda_1(0,0) +\alpha_1(1) \lambda_1(1,0)   \\ \quad \\
\alpha_2(1) =  \sum_{p \in \{2,3\}}   \alpha_1(p) \lambda_1(p,1) = \alpha_1(2) \lambda_1(2,1) +\alpha_1(3) \lambda_1(3,1)   \\ \quad \\
\alpha_2(2) =  \sum_{p \in \{0,1\}}   \alpha_1(p) \lambda_1(p,2) = \alpha_1(0) \lambda_1(0,2) +\alpha_1(1) \lambda_1(1,2)   \\ \quad \\
\alpha_2(3) =  \sum_{p \in \{2,3\}}   \alpha_1(p) \lambda_1(p,3) = \alpha_1(2) \lambda_1(2,3) +\alpha_1(3) \lambda_1(3,3)
$$