# MIMO 信道容量的数学分析
## 通用推导
这篇文章，我们想分析一下 MIMO 信道的信道容量，有个前提：是基于非频率选择性衰落信道来分析的，即频率平稳衰落的信道。

$$
Y = \sqrt{ \frac{E_s}{N_T}} HS + W  \quad ------ \quad (1)
$$

信道的容量，就是分析互信息的最大值，用数学公式表示为：

$$
C = \underset{f(S)}{ max} I(S;Y)     \quad ------ \quad (2)
$$

其中 $$f(S)$$ 是向量 S 的 联合概率分布函数。上面的公式的含义，就是在所有可能的概率分布 $$f(S)$$ 中，找到使得互信息最大的那种概率分布。

下面来推导一下互信息 $$I(S;Y)$$ :

$$
I(S;Y) = H(Y) - H(Y|S)    \quad ------ \quad (3)
$$

先来分析公式 (3) 的后半部分 $$H(Y|S)$$
(注： S 是离散的， Y 是连续的）

$$
\begin{aligned}
	H(Y|S) 
	&= \sum_s p(S=s) H(Y|S=s)  \\
	&= \sum_s p(S=s) \int_y  p(y|S=s) log\frac{1}{p(y|S=s)} dy
\end{aligned}    \quad ------ \quad (4)
$$

利用公式 (1) 有

$$
y = \sqrt{ \frac{E_s}{N_T}} Hs + w  \quad ------ \quad (5)
$$

则公式 (4) 继续推导为：

$$
\begin{aligned}
	H(Y|S) 
	&= \sum_s p(S=s) \int_w  p(y = \sqrt{ \frac{E_s}{N_T}} Hs + w) log\frac{1}{p(y = \sqrt{ \frac{E_s}{N_T}} Hs + w)} dw  \\
	&= \sum_s p(S=s) \int_w  p(w = y - \sqrt{ \frac{E_s}{N_T}} Hs ) log\frac{1}{p(w=y - \sqrt{ \frac{E_s}{N_T}} Hs )} 
	dw
\end{aligned} \\   \quad ------ \quad (6)
$$

因为噪声 $$w$$ 的概率分布，与 $$s$$ 的取值无关，所以，公式 (6) 后面的积分就与 $$s$$ 的取值无关，则求和与求积分就可以独立开来：

$$
\begin{aligned}
	H(Y|S) 
	&= [\sum_s p(S=s)] [ \int_w  p(w = y - \sqrt{ \frac{E_s}{N_T}} Hs ) log\frac{1}{p(w=y - \sqrt{ \frac{E_s}{N_T}} Hs )} 
	dw ] \\
	&= \int_w  p(w = y - \sqrt{ \frac{E_s}{N_T}} Hs ) log\frac{1}{p(w=y - \sqrt{ \frac{E_s}{N_T}} Hs )} 
	dw \\
	&= \int_w  p(w ) log\frac{1}{p(w)} dw  \\
	&=  H(W)  \quad ------ \quad (7)
\end{aligned}
$$

把公式 (7) 代入公式 (3) ：

$$
I(S;Y) = H(Y) - H(W)   \quad ------ \quad (8)
$$

现在就相当于找 $$H(Y)$$ 的最大值。

这里给出两个不加证明（还不知道咋证明）的定理：

定理 1： 当给定 Y 的自相关矩阵 $$R_{YY}$$，那么 当 Y 满足 Zero-Mean Circularly Symmetric Complex Gaussian 分布时，Y的 differential entroy( 即连续熵) H(Y) 取最大值。

推论：根据公式 (1)， Y 是 Zero-Mean Circularly Symmetric Complex Gaussian 分布， 则 S 也需要是 Zero-Mean Circularly Symmetric Complex Gaussian 分布。

定理 2：
（1）当 Y 是Zero-Mean Circularly Symmetric Complex Gaussian 分布时：

$$
H(Y) = log_2(|\pi e R_{YY}|)   \quad \text{bps/Hz}   \quad ------ \quad (9)
$$

（2）当 W 是Zero-Mean Circularly Symmetric Complex Gaussian 分布时，因为各个分量的白噪声之间相互独立，则：

$$
H(W) = log_2(|\pi e  N_0 I_{N_R}|)   \quad \text{bps/Hz}   \quad ------ \quad (10)
$$

把 (9) (10) 代入公式 (8) ：

$$
\begin{aligned}
	I(S;Y) 
	&= H(Y) - H(W)  \\
	&=  log_2(|\pi e R_{YY}|)  - log_2(|\pi e  N_0 I_{N_R}|)\\
	&= log_2(|\frac{R_{YY}}{N_0}|)
\end{aligned}  \quad ------ \quad (11)
$$

注意： 本文中的 || 都是表示取方阵的行列式.

推导一下 $$R_{YY}$$ ：

$$
\begin{aligned}
	R_{YY} 
	&= E[(Y-EY)(Y-EY)^H]  \\
	&=E(YY^H)  \\
	&= E[(\sqrt{ \frac{E_s}{N_T}} HS + W) (\sqrt{ \frac{E_s}{N_T}} HS + W)^H] \\
	&= E[(\sqrt{ \frac{E_s}{N_T}} HS + W) (\sqrt{ \frac{E_s}{N_T}} S^H H^H + W^H)]  \\
	&= E[\frac{E_s}{N_T} HSS^H H^H + \sqrt{ \frac{E_s}{N_T}} HS W^H + W \sqrt{\frac{E_s}{N_T}} S^H H^H + WW^H]  \\
	&= E(\frac{E_s}{N_T} HSS^H H^H) + E( WW^H) \\
	&= \frac{E_s}{N_T} HE(SS^H) H^H + N_0 I_{N_R} \\
	&= \frac{E_s}{N_T} HR_{SS} H^H + N_0 I_{N_R} \quad ------ \quad (12)  
\end{aligned}
$$

把公式 (12) 代入公式 (11):

$$
\begin{aligned}
	I(S;Y) 
	&= H(Y) - H(W)  \\
	&= log_2(|\frac{R_{YY}}{N_0}|)  \\
	& = log_2(|\frac{ \frac{E_s}{N_T} HR_{SS} H^H + N_0 I_{N_R} }{N_0}|)  \\ 
	&=  log_2(|\frac{E_s}{N_T N_0} HR_{SS} H^H +  I_{N_R} |)
\end{aligned}  \quad ------ \quad (13)
$$

从公式 (13) 可以看出，这个互信息的最大取值位置，只与 $$R_{SS}$$ 有关。
这篇文章里面都假定 H 不是随机变量，对于接收方 H 是已知的，是确定的。
把公式(13) 代入公式（2）

$$
C = \underset{f(S)}{ max}[ log_2(|\frac{E_s}{N_T N_0} HR_{SS} H^H +  I_{N_R} |)   ]      \quad ------ \quad (14)
$$

在实际应用中 $$f(S)$$  这个分布，其实是要满足一个总能量一定的约束条件，我们假定能量都归一化了。总能量是 $$R_{SS}$$ 的迹：

$$
Tr(R_{SS}) = N_T
$$

那么公式 (14) 就变成

$$
C = \underset{Tr(R_{SS}) = N_T}{ max} log_2(|\frac{E_s}{N_T N_0} HR_{SS} H^H +  I_{N_R} |)      \quad ------ \quad (15)
$$

### 发送方不知道信道矩阵 H


我们只能在各个信道之间均匀分配能量，再假定各个发射天线上发送的信号是相互独立的，则：

$$
R_{SS} = I_{N_T}
$$

那么:

$$
C =  log_2(|\frac{E_s}{N_T N_0} H H^H +  I_{N_R} |)    \quad ------ \quad (16)
$$

因为 $$H H^H$$ 是共轭对称矩阵，所以，可以分解为：

$$
H H^H = Q \Lambda Q^H
$$

再利用  sylvesters-determinant-identity 等式：
A 是 mxn, B 是 nxm，则：

$$
|I_m + AB| = |I_n+BA|
$$

则公式 (16) 中的

$$
\begin{aligned}
	|\frac{E_s}{N_T N_0} H H^H +  I_{N_R} | 
	&= |\frac{E_s}{N_T N_0} Q \Lambda Q^H +  I_{N_R} |  \\
	&=|\frac{E_s}{N_T N_0} Q^H Q \Lambda  +  I_{N_R} |  \\
	&=|\frac{E_s}{N_T N_0} \Lambda  +  I_{N_R} |  \\
	&= \prod_{i=1}^{r} ( \frac{E_s}{N_T N_0} \lambda_i  +  1 )
\end{aligned}  \quad ------ \quad (17)
$$

其中 $$r$$ 是矩阵 H 的秩。

把 (17) 代入 (16) 有：

$$
C = \sum_{i=1}^{r}  log_2( \frac{E_s}{N_T N_0} \lambda_i  +  1 )  \quad ------ \quad (18)
$$

### 若发送方知道信道矩阵 H

则可以有更优的策略来分配能量。可以使用注水算法，达到信道容量最大化。

首先，我们需要对信道传输模型做一点小的修改，以便充分利用信道矩阵 H 的信息。我们需要对公式 (1) 做一点小的修改。

我们在发送方就已知信道矩阵 H，则可以通过对 H 做 SVD 分解：

$$
H = U \Sigma V^H
$$

我们把发送的信号记为 $$\hat S$$ ，是一个列向量，我们用 矩阵 V 做一个变换：

$$
S = V \hat S
$$

把 S 通过公式 (1) 表示的信道发送出去，则接收方接收到的数据为

$$
\begin{aligned}
	Y &=  \sqrt{\frac{E_s}{N_T}} HS + W  \\
	&= \sqrt{\frac{E_s}{N_T}} U\Sigma V^H  V\hat S + W  \\
	& = \sqrt{\frac{E_s}{N_T}} U\Sigma \hat S + W 
\end{aligned}
$$

那么，在接收方，用矩阵 U 对接受的数据做一个变换：

$$
\begin{aligned}
	\hat Y &= U^H Y \\
	&= U^H ( \sqrt{\frac{E_s}{N_T}} U\Sigma \hat S + W ) \\
	&= \sqrt{\frac{E_s}{N_T}} U^H U\Sigma \hat S + U^H W \\
	&= \sqrt{\frac{E_s}{N_T}} \Sigma \hat S + \hat W
\end{aligned} \quad -------\quad (19)
$$

其中 

$$
\Sigma = 
\begin{bmatrix}
	\sqrt {\lambda_1} & 0 & ... & 0 \\
	0 & \sqrt {\lambda_2} & ... & 0 \\
	& ... \\
	0 & 0 & ...\sqrt{ \lambda_r}.. & 0 \\
	& & ... \\
	0&0&...&0
\end{bmatrix}
$$

那么从 $$\hat S$$ 到 $$\hat Y$$ 这样的通信信道，从相互耦合的 MIMO 信道，就变成相互独立的 r 个信道：

$$
\hat y_1 = 、\sqrt{\frac{E_s}{N_T}} \sqrt{\lambda_1} \hat s_1 + \hat w_1\\
....  \\
\hat y_r = \sqrt{\frac{E_s}{N_T}} \sqrt{\lambda_r} \hat s_r + \hat w_r\\
$$

如果 $$\hat s_i$$ 的发射功率为 $$\gamma_i$$ , 那么第 i 个信道的信号功率为：

$$
\frac{E_s}{N_T} \lambda_i \gamma_i
$$

若噪声功率为 $$N_0$$，那么这个信道的信噪比为：

$$
\frac{\frac{E_s}{N_T} \lambda_i \gamma_i}{N_0} = \frac{E_s}{N_T N_0} \lambda_i \gamma_i
$$

则第 i 个信道的信道容量就为：

$$
C_i = log_2(1 + \frac{E_s}{N_T N_0} \lambda_i \gamma_i )   \quad \quad  \text{bps/Hz}
$$

则总的信道容量为：

$$
C = \sum_{i=1}^r log_2(1 + \frac{E_s}{N_T N_0} \lambda_i \gamma_i )   \quad \quad  \text{bps/Hz}
$$

至此，问题就变成如何分配总的信号能量 $$N_T$$ ，让上面的总信道容量最大：

$$
C_{max} = \underset{\gamma_1+...+\gamma_r = N_T}{ max} \sum_{i=1}^r log_2(1 + \frac{E_s}{N_T N_0} \lambda_i \gamma_i )   \quad \quad  \text{bps/Hz}
$$

这是一个最优化求解的问题，至此，可以用注水算法来分配能量，从而达到上面的最优解。



====================

我们也可以从公式 (19) 开始，利用公式 (11) 的结论来推导。我们需要分析 $$\hat Y$$ 的自相关矩阵：

推导一下 $$R_{\hat Y \hat Y}$$ ：

$$
\begin{aligned}
	R_{\hat Y \hat Y} 
	&= E[(\hat Y-E\hat Y)(\hat Y-E\hat Y)^H]  \\
	&=E(\hat Y \hat Y^H)  \\
	&= E[(\sqrt{\frac{E_s}{N_T}} \Sigma \hat S + \hat W) (\sqrt{\frac{E_s}{N_T}} \Sigma \hat S + \hat W)^H] \\
	&= E[(\sqrt{\frac{E_s}{N_T}} \Sigma \hat S + \hat W) (\sqrt{\frac{E_s}{N_T}}  \hat S^H \Sigma^H + \hat W^H)]  \\
	&= E[\frac{E_s}{N_T} \Sigma \hat S \hat S^H \Sigma^H + \sqrt{ \frac{E_s}{N_T}} \Sigma \hat S \hat W^H + \hat W\sqrt{\frac{E_s}{N_T}} \hat S^H \Sigma^H + \hat W \hat W^H]  \\
	&= E(\frac{E_s}{N_T}\Sigma \hat S \hat S^H \Sigma^H) + E( \hat W \hat W^H) \\
	&= \frac{E_s}{N_T} \Sigma E(\hat S\hat S^H) \Sigma^H + N_0 I_{N_R} \\
	&= \frac{E_s}{N_T} \Sigma R_{\hat S\hat S} \Sigma^H + N_0 I_{N_R} \quad ------ \quad (20)  
\end{aligned}
$$

把公式 (20) 代入公式 (11):

$$
\begin{aligned}
	I(\hat S;\hat Y) 
	&= H(\hat Y) - H(\hat W)  \\
	&= log_2(|\frac{R_{\hat Y \hat Y}}{N_0}|)  \\
	& = log_2(|\frac{ \frac{E_s}{N_T} \Sigma R_{\hat S \hat S} \Sigma^H + N_0 I_{N_R} }{N_0}|)  \\ 
	&=  log_2(|\frac{E_s}{N_T N_0} \Sigma R_{\hat S \hat S} \Sigma^H +  I_{N_R} |)
\end{aligned}  \quad ------ \quad (21)
$$

从公式 (21) 可以看出，这个互信息的最大取值位置，只与 $$R_{\hat S \hat S}$$ 有关。
这篇文章里面都假定 H 不是随机变量，对于接收方 H 是已知的，是确定的。
把公式(21) 代入公式（2）

$$
C = \underset{f(S)}{ max}[ log_2(|\frac{E_s}{N_T N_0} \Sigma R_{\hat S \hat S} \Sigma^H +  I_{N_R} |)   ]      \quad ------ \quad (22)
$$

在实际应用中 $$f(S)$$  这个分布，其实是要满足一个总能量一定的约束条件，我们假定能量都归一化了。总能量是 $$R_{SS}$$ 的迹：

$$
Tr(R_{\hat S \hat S}) = N_T
$$

那么公式 (14) 就变成

$$
C = \underset{Tr(R_{SS}) = N_T}{ max} log_2(|\frac{E_s}{N_T N_0} \Sigma R_{\hat S \hat S} \Sigma^H +  I_{N_R} |)      \quad ------ \quad (23)
$$

我们可以假设 $$\hat S$$  各个分量之间相互独立，当然，是 0 均值的。则：

$$
R_{\hat S \hat S} = 
\begin{bmatrix}
	{\gamma_1} & 0 & ... & 0 \\
	0 & \gamma_2 & ... & 0 \\
	& ... \\
	0&0&...&\gamma_{N_T}
\end{bmatrix}
$$

又：

$$
\Sigma = 
\begin{bmatrix}
	\sqrt {\lambda_1} & 0 & ... & 0 \\
	0 & \sqrt {\lambda_2} & ... & 0 \\
	& ... \\
	0 & 0 & ...\sqrt{ \lambda_r}.. & 0 \\
	& & ... \\
	0&0&...&0
\end{bmatrix}
$$

代入 (23) 有：

$$
C_{max} = \underset{\gamma_1+...+\gamma_r = N_T}{ max} \sum_{i=1}^r log_2(1 + \frac{E_s}{N_T N_0} \lambda_i \gamma_i )   \quad \quad  \text{bps/Hz} \quad ----- \quad (24)
$$

参考书：
Introduction to Space-Time Wireless Communications， Arogyaswami Paulraj，Cambridge University Press 2003


## 正交信道信道容量最大
在发送方不知道信道矩阵 H 的情况下，我们只能在各个发射天线上均匀分配能量，我们推导出来的信道容量公式为：

$$
C = \sum_{i=1}^r log_2(\frac{E_s}{N_T N_0  } \lambda_i + 1)  \quad ---- \quad ( 1)
$$

其中 $$\lambda_i$$ 是$$H H^H = Q \Sigma Q^H$$ 分解后 $$\Sigma$$ 的对角线上的元素，也就是 $$HH^H$$ 的特征值。

现在来讨论一个问题，信道矩阵 H 满足什么条件，能让 (1) 式的信道容量最大呢？我们以 $$N_R=N_T=M$$ 为特例来讨论。

这里，我们需要假定信道的转移系数满足一个固定的约束，即各个信道对能量的放大是一个定值：

$$
\left \| H  \right \|_F^2 = \sum_{i=1}^M \sum_{j=1}^M |h(i,j)|^2  = \zeta
$$

$$\left \|  H \right \|_F$$ 是 Frobenius 范数，就是各个元素的平方和。


根据线性代数的定理 ( 这里谁能帮忙提供一个证明？）：

$$
\left \|  H \right \|_F^2 = \sum_{i=1}^M \lambda_i
$$

其中 $$\lambda_i$$ 是 $$HH^H$$ 的特征值。

所以，问题就变成一个最优化求解的问题：

$$
max: C = \sum_{i=1}^M log_2(\frac{E_s}{M N_0  } \lambda_i + 1)   \\
s.t. :  \sum_{i=1}^M \lambda_i = \zeta
$$

用拉格朗日乘数法可以求得最优解，当：

$$
\lambda_1 = \lambda_2 = ... = \lambda_{N_R} = \frac{\zeta}{M}
$$

时取得最大值。

如果 $$|h(i,j)|^2 = 1$$ ，那么

$$
\left \| H  \right \|_F^2  = M^2
$$

则信道容量为：

$$
\begin{aligned}
	C 
	&= \sum_{i=1}^M log_2(\frac{E_s}{M N_0  } \frac{M^2}{M} + 1) \\
	&= \sum_{i=1}^M log_2(\frac{E_s}{N_0  }  + 1)  \\
	&= M  log_2(\frac{E_s}{N_0  }  + 1)\end{aligned}
$$

此时，这个信道是一个正交信道，即： H 是正交复数矩阵（这里似乎也需要一个证明？？）。

通俗地理解，就是各个信道之间的没有相互干扰，是正交的，所以，是可以相互分离开的。

$$
Y = H_1  X_1 + H_2 X_2 + ... + H_M X_M + W
$$

$$H_1,...,H_M$$ 两两正交，那么：

$$
\begin{aligned}
	H_i^H  Y &= H_i^H H_1 X_1+...+H_i^H H_i X_i + ... + H_i^H H_M X_M + H_i^H W \\
	&= M X_i + H_i^H W
\end{aligned}
$$

这样就解耦了，直接可以恢复出来 $$X_i$$.

## SIMO MISO

这个文章，我们来分析一下 SIMO 和 MISO 情况下的信道容量。我们从之前的两个结论出发：

公式 (1) 是发送方不知道信道矩阵的情况下的信道容量公式。

$$
C = \sum_{i=1}^r log_2(1+\frac{Es}{M_t N_0} \lambda_i) \quad ----- \quad (1)
$$

公式 (2) 是发送方知道信道矩阵情况下的信道容量公式。

$$
C= \underset{\gamma_1+ \cdots+\gamma_r = M_T}{max }  
\sum_{i=1}^r log_2(1+\frac{Es \gamma_i}{M_t N_0} \lambda_i) \quad ----- \quad (2)
$$

### SIMO （Single Input Multiple Output）

此时，信道矩阵是一个列向量：

$$
H = 
\begin{bmatrix}
	h_1 \\
	h_2 \\
	...  \\
	h_{M_R}
\end{bmatrix}
$$

我们知道

$$
r = rank(HH^H) = 1
$$

即秩为 1.
则：

如果 $$|h_i|=1$$

$$
\lambda_1 = \left \| H \right \|_F^2 = M_R
$$

则公式 (1) 就变成：

$$
C =  log_2(1+\frac{Es}{N_0} M_R) \quad ----- \quad (3)
$$

可见，随着接收天线数量的增加，信道容量呈对数增长。
在 SIMO 情况下，即使发送方知道信道矩阵，由于只有一根天线，也不能做什么事情，对提高信道容量没有任何作用。
用公式 (2)  来推导，一样可以得到公式 (3).

### MISO( Multiple Output Single Input)

此时，信道矩阵是一个行向量：

$$
H = 
\begin{bmatrix}
	h_1 &
	h_2 &
	... &
	h_{M_T}
\end{bmatrix}
$$

我们知道

$$
r = rank(HH^H) = 1
$$

即秩为 1.
则：

如果 $$|h_i|=1$$

$$
\lambda_1 = \left \| H \right \|_F^2 = M_T
$$

(1) 发送方不知道信道矩阵

则公式 (1) 就变成：

$$
C =  log_2(1+\frac{Es}{N_0} ) \quad ----- \quad (4)
$$

可见，随着接收天线数量的增加，信道容量不增长。特别注意的是，这个时候，发送的总能量是1，因为秩是1，被分配的能量是 1.

(2) 发送方知道信道矩阵
则公式 (2) 就变成：

$$
\begin{aligned}
	C&= \underset{\gamma_1= M_T}{max }  
	log_2(1+\frac{Es \gamma_1}{M_t N_0} \lambda_i)  \\
	&=  log_2(1+\frac{Es M_T}{M_T N_0} M_T)     \\
	&= log_2(1+\frac{Es }{ N_0} M_T)     
\end{aligned}
\quad ----- \quad (2)
$$

可见，随着发射天线数量的增加，信道容量呈对数增长。

### 对比分析
SIMO 情况与 MISO(发送方知道信道矩阵) 的情况，如果前者的接收天线数等于后者的发送天线数，则他们的信道容量是相同的。
需要注意的是：对于 MISO(发送方知道信道矩阵), 其发射的总能量不是1，二是 $$M_T$$，是牺牲了发射功率来换取了相同的信道容量的。我们给的总能量，平均来看，是给每个发射天线一个单位功率  1. 但是在具体分配上，是用了 SVD 分解的矩阵 V 来做功率分配的。

$$
H_{1\times M_T} = U_{1\times 1} \Sigma_{1\times M_T} V_{M_T\times M_T}^H
$$

因为秩为1，所以 我们只需要取 V 的第一列 $$V_1$$ ：

$$
S= V_1 \tilde s
$$

$$\tilde s$$ 是一个标量（复数），S 是一个列向量。 则 S 中各个元素被分配的能量就是：

$$
\frac{|V_1(i)|^2}{\left \| V_1 \right \| ^2}
$$

如果 $$\tilde s$$ 的能量是 1， 则此时的信道容量是没有 SIMO 的高。为了达到与 SIMO 一样的信道容量，则 $$\tilde s$$ 的能量是 $$M_T$$（ 当然，为了对比，此时的 $$M_T = M_R$$ ).

这与我们之前分析的发射分集与接收分集 的结论是一致的。

SIMO 相当于接收分集；
MISO 相当于发射分集。