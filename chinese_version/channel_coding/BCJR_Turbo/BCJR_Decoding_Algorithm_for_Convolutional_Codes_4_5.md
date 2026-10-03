---
layout: default
title: " 卷积码的 BCJR 译码算法 (四)"
back_url: /index.html?lang=zh
---
## 卷积码的 BCJR 译码算法 (四)--计算 $$\beta$$

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

已经可以计算出来。另外，$$\alpha_t(q)$$ 也已经在上一篇文章中推导出来了递归计算的公式。

现在我们来分析一下如何递推计算 $$\beta_{t+1}(p)$$  (这里我们换了一个字母，把 q 换成了 p，以便后面分析时，状态都是从 p-->q 进行转移的）。
根据前面的公式
$$
\begin{aligned}
	\beta_t(p) &= p(r_{>t-1} | \psi_t=p )   \\  \\
	&= p(r_t,r_{>t} | \psi_t=p)  \\ \\
	&=\sum_q  p(r_t,r_{>t}, \psi_{t+1}=q | \psi_t=p)   \quad \quad \quad  (边缘概率公式)\\
	&=\sum_q  p(r_{>t}  |r_t,\psi_{t+1}=q, \psi_t=p) p(r_t,\psi_{t+1}=q| \psi_t=p)  \quad \quad \quad  (条件概率公式)\\
	&=\sum_q  p(r_{>t}  |\psi_{t+1}=q) p(r_t,\psi_{t+1}=q| \psi_t=p)  \quad \quad \quad  (马尔可夫性)\\
	&=\sum_q  p(r_t,\psi_{t+1}=q| \psi_t=p)  p(r_{>t}  |\psi_{t+1}=q)  \quad \quad \quad  (调整顺序)\\
	&=\sum_q  \gamma_t(p,q)  \beta_{t+1}(q) 
\end{aligned}
$$

至此，我们得到了一个递推公式：

$$
\beta_t(p) = p(r_{>t-1} | \psi_t=p )  =\sum_q  \gamma_t(p,q)  \beta_{t+1}(q)  \tag{3}
$$

这个递推公式可以这样想：
t 时刻状态为 p, 且知道 t - 1 时刻之后所有的接收数据，那么，从 t 时刻 p 状态能走到 t+1 时刻多个 q 状态，则这些能走到的路径的概率都加在一起，就是 t 时刻我们关心的 $$\beta$$ 概率，用下图可以形象地表达出来：

(图中的 $$\lambda$$ 应该是 $$\gamma$$ )


![beta_recursive_calculate.png](/figure/卷积码编码和译码/BCJR-turbo/beta_recursive_calculate.png) 


举个例子，例如 t=6 时刻，令 t=6 时刻的状态 p=2，根据状态栅格图


![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png) 

从状态 2 可以走到状态 1 和状态 3, 则:

$$
\begin{aligned}
	\beta_t(p) &= \beta_6(2) = \sum_{q \in \{1,3\}}   \gamma_6(2,q) \beta_{7}(q) \\
	\\
	&= \gamma_6(2,1) \beta_{7}(1) + \gamma_6(2,3) \beta_{7}(3)
\end{aligned}   \tag{4}
$$

然后公式 (4) 中的 $$\beta_{7}(1),\beta_{7}(3)$$  继续用递推公式计算：

$$
\begin{aligned}
	\beta_7(1) &= \sum_{q \in \{0,2\}}   \gamma_7(1,q) \beta_{8}(q) \\
	\\
	&= \gamma_7(1,0) \beta_{8}(0) + \gamma_7(1,2) \beta_{8}(2)
\end{aligned}   \tag{5}
$$

和

$$
\begin{aligned}
	\beta_7(3) &= \sum_{q \in \{1,3\}}   \gamma_7(3,q) \beta_{8}(q) \\
	\\
	&= \gamma_7(3,1) \beta_{8}(1) + \gamma_7(3,3) \beta_{8}(3)
\end{aligned}   \tag{6}
$$

实际上，在计算时，我们知道是以状态 0 的，所以， $$\beta_9(0) = 1$$ ，其他状态的概率为 0，所以：

$$
\beta_9(0) = 1 \\
\beta_9(1) = 0 \\
\beta_9(2) = 0 \\
\beta_9(3) = 0
$$

在时刻 8：

$$
\beta_8(0) = \sum_{q \in \{0,2\}}   \gamma_8(0,q) \beta_{9}(q) = \gamma_8(0,0) \beta_{9}(0) + \gamma_8(0,2) \beta_{9}(2)
\\ \quad \\
\beta_8(1) = \sum_{q \in \{0,2\}}   \gamma_8(1,q) \beta_{9}(q) = \gamma_8(1,0) \beta_{9}(0) + \gamma_8(1,2) \beta_{9}(2)
\\ \quad \\
\beta_8(2) = \sum_{q \in \{1,3\}}   \gamma_8(2,q) \beta_{9}(q) = \gamma_8(2,1) \beta_{9}(1) + \gamma_8(2,3) \beta_{9}(3)
\\ \quad \\
\beta_8(3) = \sum_{q \in \{1,3\}}   \gamma_8(3,q) \beta_{9}(q) = \gamma_8(3,1) \beta_{9}(1) + \gamma_8(3,3) \beta_{9}(3)
$$

同理，在时刻 7，用时刻 8 的结果来计算：

$$
\beta_7(0) = \sum_{q \in \{0,2\}}   \gamma_7(0,q) \beta_{8}(q) = \gamma_7(0,0) \beta_{8}(0) + \gamma_7(0,2) \beta_{8}(2)
\\ \quad \\
\beta_7(1) = \sum_{q \in \{0,2\}}   \gamma_7(1,q) \beta_{8}(q) = \gamma_7(1,0) \beta_{8}(0) + \gamma_7(1,2) \beta_{8}(2)
\\ \quad \\
\beta_7(2) = \sum_{q \in \{1,3\}}   \gamma_7(2,q) \beta_{8}(q) = \gamma_7(2,1) \beta_{8}(1) + \gamma_7(2,3) \beta_{8}(3)
\\ \quad \\
\beta_7(3) = \sum_{q \in \{1,3\}}   \gamma_7(3,q) \beta_{8}(q) = \gamma_7(3,1) \beta_{8}(1) + \gamma_7(3,3) \beta_{8}(3)
$$

## 卷积码 BCJR 译码算法(五)--代码讲解以及总结
我们假如要计算如下这个概率

$$
P(X_6=1|r_0,r_1,\cdots,r_9)
$$

注意上面公式中每个 $$r_i$$  其实是接收到的两个数据，分别对应 $$v_t^{(0)}, v_t^{(1)}$$ 通过信道发送后得到的数据（这里要稍微注意一下，我们用 BPSK，则 0--> -1,  1---->1 ， 发送的是 -1 或者 +1).

输入 为 比特 1， 对应的状态转移有如下几种情况：

$$
\psi_6=0 \quad \quad  ---->  \psi_7=2 \\
\psi_6=1 \quad \quad  ---->  \psi_7=0 \\
\psi_6=2 \quad \quad  ---->  \psi_7=3 \\
\psi_6=3 \quad \quad  ---->  \psi_7=1 \\
$$

所以：

$$
\begin{aligned}
	P(X_6=1|r_0,r_1,\cdots,r_9) = & P(\psi_6=0,\psi_7=2|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=1,\psi_7=0|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=3,\psi_7=1|r_0,r_1,\cdots,r_9) 
	\quad  ---- \quad (4)
\end{aligned}
$$

那么公式 (4) 中任何一个求和都可以按照下面这个例子来展开，我们以 $$P(\psi_6=2,\psi_7=3\vert r_0,r_1,\cdots,r_9)$$ 为例：

$$
\begin{aligned}
	P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9) &= p( \psi_6=2 , r_{<6}) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_{>6} | \psi_7=3 ) \\
	&=p( \psi_6=2 , r_0,r_1,\cdots,r_5) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_7,\cdots,r_9 | \psi_7=3 ) \\
	&= \alpha_6(2)  \gamma_6(2,3)  \beta_7(3)
\end{aligned}
$$

![the_whole_pitcutre_of_BCJR.png](/figure/卷积码编码和译码/BCJR-turbo/the_whole_pitcutre_of_BCJR.png) 



Python 代码：代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}



![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png) 

编码前的数据为：x=[1,1,0,0,1,0,1,0,1,1]

编码后：v=[1,1,1,1,0,1,0,1,1,0,0,1,1,1,0,1,1,0,1,0]

编码的状态转移：$$\psi =[0,2,3,3,3,1,2,3,3,1,0]$$

r = [  (2.53008, 0.731636), (-0.523916, 1.93052), (-0.793262, 0.307327), (1.24029, 0.784426),(1.83461, -0.968171),  

(-0.433259, 1.26344),   (1.31717, 0.995695), ( -1.50301, 2.04413), (1.60015, -1.15293), (0.108878, -1.57889)]



如果根据 r 直接译码，则

[1,1,*<u>0</u>*,1,0,1,0,1,1,0,0,1,1,1,0,1,1,0,1,0]

第三个（从1开始算）比特是译码错了。


![numerial_result_of_alpha_belta_APB.png](/figure/卷积码编码和译码/BCJR-turbo/numerial_result_of_alpha_belta_APB.png)