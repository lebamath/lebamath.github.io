---
layout: default
title: " NR type I/II Codebook"
back_url: /index.html?lang=zh
---

## oversampling

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep555538)

在前面的文章中，讨论了角域空间，如果天线间距大于等于半波长，则可以找到 M 个 M 维向量构成的一个基：

$$
\begin{aligned}
\text{[}e^{j0*\frac{2\pi}{M}*0} \quad e^{j0*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j0*\frac{2\pi}{M}*(M-1)}\text{]}  \\
\text{[}e^{j1*\frac{2\pi}{M}*0} \quad e^{j1*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j1*\frac{2\pi}{M}*(M-1)}\text{]}  \\
\text{[}e^{j2*\frac{2\pi}{M}*0} \quad e^{j2*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j2*\frac{2\pi}{M}*(M-1)}\text{]}   \\
\cdots  \\
\text{[}e^{jk*\frac{2\pi}{M}*0} \quad e^{jk*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{jk*\frac{2\pi}{M}*(M-1)}\text{]}   \\
\cdots  \\
\text{[}e^{j(M-1)*\frac{2\pi}{M}*0} \quad e^{j(M-1)*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j(M-1)*\frac{2\pi}{M}*(M-1)}\text{]}   \\
\end{aligned}
$$

在 5G NR 通信系统中，UE(终端)在回传 precoding table 时，使用了 oversampling 的技术，用于提高精度。在 5G NR  Precoding codebook Type 1 中回传的是一个角度空间上的向量，因此，将角域空间分得越细越好。

但是在第二种，即 type 2 中，是回传多个角度向量，如果回传的角度向量是 M 个，则空间中的所有波束方向向量都能用上面的 M 个基向量表示出来，但是，5G 规定，最多回传 4 个，至于回传几个，还由上层来定义，如果回传的向量的个数少于 M 个，则对 M 个基向量做 oversampling，然后在多个基中选择一个子空间，用这个子空间上的基向量，来表示对应的波束方向向量。



因此 DFT-based oversamping 能提高精度的核心，是在于有个硬性的约束，即回传的波束的数量少于 M 个。



可以理解为 3维空间中的任何一个向量，如果只能让用两个基向量来线性表示，那么就只能是落在三维空间中的某个平面上，因此，选择一个合适的平面，则可以提高表示的精度，当然最理想的情况是选择的平面，是需要被表示的那个向量所在的平面。

## NR type I Codebook

Type I Codebook 其核心思想就是选择**一个** Beam，然后针对另外一个极化方向，乘以一个相位，数学上可以表示为：

$$
b_1(l) = 
\begin{bmatrix}
	1 \\ 
	e^{j\frac{2\pi l}{Q_1N_1}} \\ 
	\cdots \\ 
	e^{j\frac{2\pi l(N_1-1)}{Q_1N_1}}
\end{bmatrix}
\\
$$

$$
b_2(m) = [1\quad e^{j\frac{2\pi m}{Q_2N_2}} \quad \cdots \quad e^{j\frac{2\pi m(N_2-1)}{Q_2N_2}}]  \\ \\
b_0(n) =
\begin{bmatrix} 1 \\   \phi(n)
\end{bmatrix}
$$

然后通过下式计算出 codebook:

$$
w(l,m,n) = b_0(n) \otimes(b_1(l)\otimes b_2(m))
$$

上面的计算是克罗内克积。

在 38.214-5.2.2.2.1 中是用如下公式表达的：

$$
u_m = [1\quad e^{j\frac{2\pi m}{Q_2N_2}} \quad \cdots \quad e^{j\frac{2\pi m(N_2-1)}{Q_2N_2}}] \\
v_{l,m} = [u_m \quad e^{j\frac{2\pi l}{Q_1N_1}}u_m \quad \cdots \quad e^{j\frac{2\pi l(N_1-1)}{Q_1N_1}}u_m]^T
$$

然后以一个 layer 的为例子：

$$
W_{l,m,n} =\begin{bmatrix}
	v_{l,m,n}\\
	\varphi(n) v_{l,m,n}	
\end{bmatrix}
$$

## NR type II Codebook

Type II Codebook 的思想是用线性空间的基中几个正交向量做线性组合来得到 codebook ：

$$
W_1 = BA = [\vec{b_0} \quad \vec{b_1} \quad \vec{b_2} \quad \vec{b_3} ]
\begin{bmatrix}
	1 & 0 & 0 & 0 \\
	0 & a_1 & 0 & 0 \\
	0 & 0 & a_2 & 0 \\
	0 & 0 & 0 & a_3 \\
\end{bmatrix}
= [\vec{b_0} \quad a_1 \vec{b_1} \quad a_2 \vec{b_2} \quad a_3 \vec{b_3}]
$$

上面这个 W1, 是与宽带(Wideband)有关系的，下面这个 W2 是窄带有关系的。

例如总带宽 100MHz，则可能分成若干个 子带(Subband)，每个子带的 beam，则还需要用下面这个矩阵来调整。

可以理解为先给出一个总体的方向，再在这个总体的方向上根据不同子带来微调。

$$
W_2 =
\begin{bmatrix}
	1 & 0 & 0 & 0 \\
	0 & c_1 & 0 & 0 \\
	0 & 0 & c_2 & 0 \\
	0 & 0 & 0 & c_3 \\
\end{bmatrix}
\begin{bmatrix}
	1  \\
	e^{j\phi_1} \\
	e^{j\phi_2} \\
	e^{j\phi_3} \\
\end{bmatrix}
=
\begin{bmatrix}
	1  \\
	c_1 e^{j\phi_1} \\
	c_2 e^{j\phi_2} \\
	c_3 e^{j\phi_3} \\
\end{bmatrix}
$$

则：

$$
W = W_1W_2 = [\vec{b_0} \quad a_1 \vec{b_1} \quad a_2 \vec{b_2} \quad a_3 \vec{b_3}]
\begin{bmatrix}
	1  \\
	c_1 e^{j\phi_1} \\
	c_2 e^{j\phi_2} \\
	c_3 e^{j\phi_3} \\
\end{bmatrix}
= \vec{b_0} + a_1 c_1 e^{j\phi_1}  \vec{b_1} + a_2 c_2 e^{j\phi_2} \vec{b_2} + a_3 c_3 e^{j\phi_3} \vec{b_3}
$$

在 5G 通信系统 38.214 -表格 5.2.2.2.3-5 中，是用如下公式来表示的：

$$
\sum_{i=0}^{L-1} v_{m_1^{(i)},m_2^{(i)}} p_{l,i}^{(1)} p_{l,i}^{(2)}\varphi_{l,i}
$$

其中，$$v_{m_1^{(i)},m_2^{(i)}}$$ 就是选择的 beam 向量， L 是向量的总个数，$$p_{l,i}^{(1)}$$ 相当于 $$a_i$$,  $$p_{l,i}^{(2)}$$ 相当于 $$c_i$$， $$\varphi_{l,i}$$ 相当于$$e^{j\phi_i}$$. 另外，公式中的 $$l$$ 是第几个 layer，在 type II 中由于只支持最多两个 layer，因此， $$l\in\{1,2\}$$