---
layout: default
title: "root MUSIC（Multiple Signal Classification）算法"
back_url: /chinese_version.html
---

# 空域平滑 MUSIC 算法

[录制的视频在 B 站课程](https://www.bilibili.com/cheese/play/ep1486051)

在 **MUSIC（MUltiple SIgnal Classification）**算法中，为了提高信号的估计精度、减少噪声对信号的影响，通常会使用**块平均（Spatial Smoothing，空域平滑）**技术。

块平均主要用于克服高度相关信号（如相干信号）的问题，尤其是在**阵列天线的方向估计（DOA，Direction of Arrival）**中，多个信号之间可能由于多径传播等原因产生相干性，导致协方差矩阵的秩亏（rank deficiency），使得 MUSIC 算法无法正确分离信号和噪声子空间。

在 MUSIC 算法中，计算如下接收信号的协方差矩阵（假定信号的均值为 0)
$$
\mathbf y(t)=\sum_{i=1}^M s_i(t) \mathbf a_i+ \mathbf n(t)
$$

协方差矩阵为：
$$
\boldsymbol R_y=\boldsymbol A \boldsymbol R_s \boldsymbol A^H+\sigma^2 \boldsymbol I
$$




在 MUSIC 算法\cite{Schmidt1979}\cite{Schmidt_MUSIC_Algorithm}中，推导公式时我们需要分析信号的协方差矩阵 
$$
\textbf{R}_s = \textbf{E}[\mathbf{X} \mathbf{X}^H]
$$

但如果入射信号是完全相关的（即信号之间呈现线性相关性，如多径传播下的相干信号），那么 
这个信号的协方差矩阵可能会变得**奇异（秩亏）**，进而信号子空间的维度会降低，导致传统的特征值分解无法正确分离信号子空间和噪声子空间。

因为发送的信号的相关，导致信号的协方差矩阵不是满秩，即：
$$
\text{rank}(\textbf{R}_s) < M
$$

其中，M 是实际 beam 的数量。

为了解决这个问题，采用块平均（Spatial Smoothing）\cite{TIE_MUSIC_SPATIAL_SMOOTHING_1164649}，通过对阵列天线划分成多个子阵列，并计算多个子阵列的协方差矩阵的平均值，从而降低信号相关性的影响，使得 MUSIC 算法仍然可用。

### 块平均（Spatial Smoothing）方法

假设我们有一个 N 元素的均匀线性阵列（ULA），并且信号之间存在相干性，我们可以执行前向块平均（Forward Spatial Smoothing, FSS），方法如下：

**a) 划分子阵列**
设大阵列共有 N 个天线，我们选取 L 个天线作为子阵列的大小，则可以构造多个子阵列：

第 1 个子阵列：阵元 [1,2,3,...,L] 

第 2 个子阵列：阵元 [2,3,4,...,L+1] 

...... 

第 N−L+1 个子阵列：阵元 [N−L+1,...,N]

** b) 计算子阵列的协方差矩阵**
对于每个子阵列的接收信号 ，计算其协方差矩阵：
$$
\textbf{R}^{(i)}_y = \textbf{E}[\mathbf{Y}_i \mathbf{Y}^H_i]
$$

**c) 求取平均协方差矩阵**

计算所有子阵列的协方差矩阵的平均值：

$$
\textbf{R}_{\text{avg}} = \frac{1}{N-L+1} 
	\sum_{i=1}^{N-L+1}\mathbf{R}^{(i)}_y
\tag{5}
$$

这样可以降低信号的相干性，使得最终得到的协方差矩阵更接近满秩，提高 MUSIC 算法的性能。

需要强调的一点是，子阵列平滑的结果，是使得协方差矩阵从 [N,N] 的变成了 [L, L] 的, 信号子空间维度还是保持不变，但是噪声子空间维度降低了。影响：

**分辨能力下降：**因为协方差矩阵变小了，可用的阵列孔径（Aperture）缩小，角度分辨率变差。
**可检测信号数减少：**原始 MUSIC 算法能检测的信号数是 N−1，块平均后只能检L−1 个信号源。

### Spatial Smoothing方法的证明
这里把接收到的信号向量表示为：
$$
\textbf{Y} = 
	\begin{bmatrix}
		y_0 \\
		y_1 \\
		\vdots \\
		y_{(N-1)} \\
	\end{bmatrix}
$$

把流形向量表示为：

$$
\textbf{a}_m = 
	\begin{bmatrix}
		1 \\
		e^{j\theta_m} \\
		e^{j2\theta_m} \\
		\vdots \\
		e^{j(N-1)\theta_m} \\
	\end{bmatrix}
	=
	\begin{bmatrix}
		z^0_m \\
		z^1_m \\
		z^2_m \\
		\vdots \\
		z^{(N-1)}_m \\
	\end{bmatrix}
$$

我们用一个长度为  L 的窗口，从 Y 中从上到下截取，得到：
$$
\textbf{Y}_0 = 
	\begin{bmatrix}
		y_0 \\
		y_1 \\
		\vdots \\
		y_{L-1} \\
	\end{bmatrix}
$$

$$
\textbf{Y}_1 = 
	\begin{bmatrix}
		y_1 \\
		y_2 \\
		\vdots \\
		y_L \\
	\end{bmatrix}
$$

......

$$
\textbf{Y}_{N-L+1} = 
	\begin{bmatrix}
		y_{N-L+1} \\
		y_{N-L+2} \\
		\vdots \\
		y_{N-1} \\
	\end{bmatrix}
$$

对上述 B = N - L + 1 个接收信号的子向量，计算其协方差矩阵：

$$
\textbf{R}^{(i)}_y =  \boldsymbol{A}^{(i)}  \boldsymbol{R}_s (\boldsymbol{A}^{(i)})^H + \sigma^2 \boldsymbol{I}
\tag{3}
$$

其中:

$$
\textbf{A}^{(i)} = 
	\begin{bmatrix}
		z^i_0  & z^i_1 & \cdots & z^i_{(M-1)}  \\
		z^{i+1}_0  & z^{i+1}_1 & \cdots & z^{i+1}_{(M-1)}  \\
		\vdots & \vdots & \ddots & \vdots \\
		z^{i+L-1}_0  & z^{i+L-1}_1 & \cdots & z^{i+L-1}_{(M-1)} 
	\end{bmatrix}
\tag{1}
$$

把公式 (1) 再分解成：

$$
\begin{aligned}
		\textbf{A}^{(i)} &= 
		\begin{bmatrix}
			z^0_0  & z^0_1 & \cdots & z^0_{(M-1)}  \\
			z^1_0  & z^{1}_1 & \cdots & z^{1}_{(M-1)}  \\
			\vdots & \vdots & \ddots & \vdots \\
			z^{L-1}_0  & z^{L-1}_1 & \cdots & z^{L-1}_{(M-1)} 
		\end{bmatrix}
		\begin{bmatrix}
			z^i_0    & 0      & \cdots & 0  \\
			0      & z^i_1    & \cdots & 0  \\
			\vdots & \vdots & \ddots & \vdots \\
			0      & 0      & \cdots & z^i_{(M-1)} 
		\end{bmatrix}  \\
		&= \boldsymbol{A}^{(0)} 
		\begin{bmatrix}
			z^i_0    & 0      & \cdots & 0  \\
			0      & z^i_1    & \cdots & 0  \\
			\vdots & \vdots & \ddots & \vdots \\
			0      & 0      & \cdots & z^i_{(M-1)} 
		\end{bmatrix} \\
		&= \boldsymbol{A}^{(0)}  \boldsymbol{D}^{i}
	\end{aligned}
\tag{2}
$$

其中：
$$
\boldsymbol{D}^{i} =
	\begin{bmatrix}
		z^i_0    & 0      & \cdots & 0  \\
		0      & z^i_1    & \cdots & 0  \\
		\vdots & \vdots & \ddots & \vdots \\
		0      & 0      & \cdots & z^i_{(M-1)} 
	\end{bmatrix}
\tag{10}
$$

把公式 (2) 代入公式 (3):

$$
\textbf{R}^{(i)}_y =  \boldsymbol{A}^{(0)} \boldsymbol{D}^{i}   \boldsymbol{R}_s (\boldsymbol{D}^{i})^H (\boldsymbol{A}^{(0)})^H + \sigma^2 \boldsymbol{I}
\tag{4}
$$

把 (4) 代入公式 (5):

$$
\textbf{R}_{\text{avg}} = \frac{1}{B} 
	\left \{
	\boldsymbol{A}^{(0)} 
	\left [  
	\boldsymbol{R}_s +
	\boldsymbol{D}   \boldsymbol{R}_s (\boldsymbol{D})^H +
	\boldsymbol{D}^2   \boldsymbol{R}_s (\boldsymbol{D}^2)^H +
	\cdots
	\boldsymbol{D}^{N-L}   \boldsymbol{R}_s (\boldsymbol{D}^{N-L})^H
	\right ]
	(\boldsymbol{A}^{(0)})^H + \sigma^2 \boldsymbol{I}
	\right \}
\tag{6}
$$

我们把上式中间部分记为：

$$
\tilde{\textbf{R}}_s = 
	\frac{1}{B}
	\left [  
	\boldsymbol{R}_s +
	\boldsymbol{D}   \boldsymbol{R}_s (\boldsymbol{D})^H +
	\boldsymbol{D}^2   \boldsymbol{R}_s (\boldsymbol{D}^2)^H +
	\cdots
	\boldsymbol{D}^{N-L}   \boldsymbol{R}_s (\boldsymbol{D}^{N-L})^H
	\right ]
\tag{7}
$$

则公式 (6)可以写为：
$$
\textbf{R}_{\text{avg}} =  
	\boldsymbol{A}^{(0)} \tilde{\textbf{R}}_s (\boldsymbol{A}^{(0)})^H + 
	\sigma^2 \boldsymbol{I}
$$

所以，现在问题就转变成证明：
$$
\text{rank}(\tilde{\textbf{R}}_s) = M
$$

即需要证明新构造的信号的协方差矩阵是满秩的。

为了书写方便，我们把公式 (7) 中的那个系数 1/B 忽略，不影响我们分析矩阵的秩。

我们把 (7) 写成：
$$
\tilde{\textbf{R}}_s = 
	\begin{bmatrix} \boldsymbol{I} & \boldsymbol{D} & \cdots & \boldsymbol{D}^{B-1}   \end{bmatrix}
	\begin{bmatrix}
		\boldsymbol{R}_s & 0 & \cdots & 0 \\
		0 & \boldsymbol{R}_s & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		0 & 0 & \cdots & \boldsymbol{R}_s
	\end{bmatrix}
	\begin{bmatrix}
		I \\
		D^{H} \\
		\vdots \\
		(D^{B-1})^H
	\end{bmatrix}
\tag{8}
$$

我们找一个相同大小的矩阵，使得信号协方差矩阵分解为：
$$
\boldsymbol{R}_s = C C^H
$$

则公式 (8) 中间的那个矩阵可以分解成：
$$
\begin{bmatrix}
		\boldsymbol{R}_s & 0 & \cdots & 0 \\
		0 & \boldsymbol{R}_s & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		0 & 0 & \cdots & \boldsymbol{R}_s
	\end{bmatrix} =
	\begin{bmatrix}
		\boldsymbol{C} & 0 & \cdots & 0 \\
		0 & \boldsymbol{C} & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		0 & 0 & \cdots & \boldsymbol{C}
	\end{bmatrix}
	\begin{bmatrix}
		\boldsymbol{C}^H & 0 & \cdots & 0 \\
		0 & \boldsymbol{C}^H & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		0 & 0 & \cdots & \boldsymbol{C}^H
	\end{bmatrix}
$$

令
$$
\boldsymbol{G} = \begin{bmatrix} \boldsymbol{I} & \boldsymbol{D} & \cdots & \boldsymbol{D}^{B-1}   \end{bmatrix}
	\begin{bmatrix}
		\boldsymbol{C} & 0 & \cdots & 0 \\
		0 & \boldsymbol{C} & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		0 & 0 & \cdots & \boldsymbol{C}
	\end{bmatrix}
\tag{9}
$$

则公式 (8) 可以简写为：
$$
\tilde{\textbf{R}}_s = \boldsymbol{G} \boldsymbol{G}^H
$$

接下来我们只需要证明矩阵 G 是满秩的。

接下来把公式 (9) 进一步推导：
$$
\boldsymbol{G} = \begin{bmatrix}
		\boldsymbol{C} & \boldsymbol{DC} & \boldsymbol{D}^2\boldsymbol{C}
		& \cdots & \boldsymbol{D}^{B-1}\boldsymbol{C}
	\end{bmatrix}
\tag{11}
$$

把公式 (10) 代入 (11)，并把 C 矩阵的系数也写出来，则：
$$
\boldsymbol{G} = 
	\begin{bmatrix}
		c_{00} & \cdots & c_{0,M-1} &  \cdots &  c_{0,0}z^{B-1}_0 & \cdots & c_{0,M-1}z^{B-1}_0\\
		\vdots & \ddots & \vdots & \ddots & \vdots & \ddots & \vdots\\
		c_{M-1,0} & \cdots & c_{M-1,M-1} &\cdots &  c_{M-1,0}z^{B-1}_{M-1} & \cdots & c_{M-1,M-1}z^{B-1}_{M-1}\\
	\end{bmatrix}
\tag{12}
$$

对公式 (12) 中的列进行重排，并不影响矩阵 G 的秩,每隔 M 列取出来放到一起：
$$
\boldsymbol{G} = 
	\begin{bmatrix}
		c_{00} & \cdots & c_{0,0}z^{B-1}_0 & \cdots & c_{0,M-1}  & \cdots & c_{0,,M-1}z^{B-1}_0\\
		\vdots & \ddots & \vdots & \ddots & \vdots &  \ddots & \vdots \\
		c_{M-1,0} & \cdots & c_{M-1,0}z^{B-1}_{M-1} & \cdots & c_{M-1,M-1} & \cdots & c_{M-,,M-1}z^{B-1}_{M-1}
	\end{bmatrix}
\tag{13}
$$

我们定义一个行向量：
$$
\boldsymbol{b}_j = 
	\begin{bmatrix}
		1 & z_j & z^2_j & \cdots & z^{B-1}_j
	\end{bmatrix}
$$

则公式 (13) 可以简写为：
$$
\boldsymbol{G} = 
	\begin{bmatrix}
		c_{00} \boldsymbol{b}_0 & c_{01} \boldsymbol{b}_0 & \cdots & c_{0,M-1} \boldsymbol{b}_0 \\
		\vdots & \vdots & \ddots & \vdots \\
		c_{M-1,0} \boldsymbol{b}_{M-1} & c_{M-1,1} \boldsymbol{b}_{M-1} & \cdots & c_{M-1,M-1} \boldsymbol{b}_{M-1} \\
	\end{bmatrix}
\tag{14}
$$

首先，这些行向量如果组成一个矩阵，也是一个范德蒙矩阵，因此，只要向量的个数 M 小于等于向量的维度 B，那么这 M 个向量就是线性无关的，其构成的矩阵就是满秩的，即秩为 M.

其次，矩阵 C 的任何一行都不可能全为 0，因为全为 0 意味着某个发射信号的能量是 0. 因此，矩阵  G 的每一行，至少有一个行向量 b 存在。 极端情况，矩阵 G 中只有第一个子列被留下来，且系数 c 都是 1，那么矩阵
$$
\boldsymbol{G} = 
	\begin{bmatrix}
		\boldsymbol{b}_0 & 0 & \cdots & 0 \\
		\vdots & \vdots & \ddots & \vdots \\
		\boldsymbol{b}_{M-1} & 0 & \cdots & 0 \\
	\end{bmatrix}
$$

也是满秩的.

所以，矩阵 G 是满秩的，即秩为 M.


### 从列向量相关性的角度来直观理解
公式 (14)  中矩阵 G 的秩，因为 C 不是满秩的（我们讨论的是信号有相关性），所以，可能会直观的误认为 G 也不是满秩的。其实不是的。我们假定所有发射信号都完全相关，则 C 的秩是 1， C 中的每一个列向量都是相互成比例的。

G 的 第一个子列，都是 同一个列向量，而第二个子列，实际上是有 D 的列向量线性组合出来的，因此，不能说就一定与 C 相关，如果第二个子列是 C 乘以 D ，那么则 CD 也是由 C 的列向量所线性组合，那么G 就不是满秩的。

另外， DC 还可以看成是  C 的行向量的线性组合，但是，与 第一个子列 C 组合在一起的时候，并不是在行并排，而是直接接在了一起，这样就不能保证接到一起的向量还是相关的了，例如:

$$
\boldsymbol{C} = 
	\begin{bmatrix}
		1 & 1  \\
		1 & 1
	\end{bmatrix}
$$

$$
\boldsymbol{D} = 
	\begin{bmatrix}
		e^{j\theta_1} & 0  \\
		0 & e^{j\theta_2}
	\end{bmatrix}
$$


则：

$$
\boldsymbol{G} = 
	\begin{bmatrix}
		1 & 1 & e^{j\theta_1} & e^{j\theta_1} \\
		1 & 1 & e^{j\theta_2} & e^{j\theta_2}
	\end{bmatrix}
$$

只要两个角度不相等，向量 G 的秩就是 2.

若 G 中的 DC 变成 CD，则

$$
\boldsymbol{G} = 
	\begin{bmatrix}
		1 & 1 & e^{j\theta_1} & e^{j\theta_2} \\
		1 & 1 & e^{j\theta_1} & e^{j\theta_2}
	\end{bmatrix}
$$

那么矩阵 G 的秩就是 1，不是满秩了。

### 空间平滑的一个例子
以 N = 5, L = 3 ，M=2，即天线有5根，子列或者说子空间长度是 3，真实 Beam 数量为 2，这两个 beam 对应的发射信号完全相同，即完全相关。
则两个 beam 对应的流形向量分别为：

$$
\mathbf{a}_1 = 
	\begin{bmatrix}
		1 \\
		e^{j\theta_1} \\ 
		e^{j2\theta_1} \\ 
		e^{j3\theta_1} \\ 
		e^{j4\theta_1}
	\end{bmatrix} \quad \quad \quad \quad
	\mathbf{a}_1 = 
	\begin{bmatrix}
		1 \\
		e^{j\theta_2} \\ 
		e^{j2\theta_2} \\ 
		e^{j3\theta_2} \\ 
		e^{j4\theta_2}
	\end{bmatrix}
$$

因为信号完全相同，我们可以假设最极端的情况，两个信号完全相同，
$$
\mathbf{S} = 
	\begin{bmatrix}
		\sqrt{2}\\
		\sqrt{2}
	\end{bmatrix}
$$

我们可以合理认为信号的协方差矩阵为：

$$
\mathbf{R}_s = 
	\begin{bmatrix}
		2 & 2 \\
		2 & 2
	\end{bmatrix}
$$


那么第一个子阵收到的信号为(为了书写方便，我们暂时忽略噪声, 并且认为发送信号都是 1).

$$
\mathbf{Y}_0 = 
	\sqrt{2}
	\begin{bmatrix}
		1 \\
		e^{j\theta_1} \\ 
		e^{j2\theta_1} 
	\end{bmatrix}
	+
	\sqrt{2}
	\begin{bmatrix}
		1 \\
		e^{j\theta_2} \\ 
		e^{j2\theta_2} 
	\end{bmatrix}
$$

第二个子阵接收的信号为：

$$
\mathbf{Y}_1 = 
	\sqrt{2}
	\begin{bmatrix}
		e^{j\theta_1} \\ 
		e^{j2\theta_1} \\ 
		e^{j3\theta_1} \\ 
	\end{bmatrix}
	+
	\begin{bmatrix}
		e^{j\theta_2} \\ 
		e^{j2\theta_2} \\ 
		e^{j3\theta_2} \\ 
	\end{bmatrix}
	=
	\sqrt{2}
	e^{j\theta_1}
	\begin{bmatrix}
		1 \\
		e^{j\theta_1} \\ 
		e^{j2\theta_1} 
	\end{bmatrix}
	+
	\sqrt{2}
	e^{j\theta_2}
	\begin{bmatrix}
		1 \\
		e^{j\theta_2} \\ 
		e^{j2\theta_2} 
	\end{bmatrix}
$$

第三个子阵接收到的信号为：
$$
\mathbf{Y}_2 = 
	\sqrt{2}
	\begin{bmatrix}
		e^{2j\theta_1} \\ 
		e^{j3\theta_1} \\ 
		e^{j4\theta_1} \\ 
	\end{bmatrix}
	+
	\sqrt{2}
	\begin{bmatrix}
		e^{2j\theta_2} \\ 
		e^{j3\theta_2} \\ 
		e^{j4\theta_2} \\ 
	\end{bmatrix}
	=
	\sqrt{2}
	e^{j2\theta_1}
	\begin{bmatrix}
		1 \\
		e^{j\theta_1} \\ 
		e^{j2\theta_1} 
	\end{bmatrix}
	+
	\sqrt{2}
	e^{j2\theta_2}
	\begin{bmatrix}
		1 \\
		e^{j\theta_2} \\ 
		e^{j2\theta_2} 
	\end{bmatrix}
$$

则:

$$
\boldsymbol{A}^{(0)} = 
	\begin{bmatrix}
		1  & 1\\
		e^{j\theta_1} & e^{j\theta_2}\\ 
		e^{j2\theta_1} & e^{j2\theta_2} 
	\end{bmatrix}
$$

以及：
$$
\boldsymbol{D}^0 = 
	\begin{bmatrix}
		1 & 0\\ 
		0 & 1
	\end{bmatrix}
	= \boldsymbol{I}
$$
$$
\boldsymbol{D}^1 = 
	\begin{bmatrix}
		e^{j\theta_1} & 0\\ 
		0 & e^{j\theta_2}
	\end{bmatrix}
$$

$$
\boldsymbol{D}^2 = 
	\begin{bmatrix}
		e^{j2\theta_1} & 0\\ 
		0 & e^{j2\theta_2}
	\end{bmatrix}
$$

那么
$$
\mathbf{Y}_0 = 
	\begin{bmatrix}
		1  & 1\\
		e^{j\theta_1} & e^{j\theta_2}\\ 
		e^{j2\theta_1} & e^{j2\theta_2} 
	\end{bmatrix} 
	\begin{bmatrix}
		1 & 0 \\
		0 & 1
	\end{bmatrix}
	\begin{bmatrix}
		\sqrt{2} \\
		\sqrt{2}
	\end{bmatrix}
	= A^{(0)} D^0 S = A^{(0)} I S
$$
以及：
$$
\mathbf{Y}_1 = 
	\begin{bmatrix}
		1  & 1\\
		e^{j\theta_1} & e^{j\theta_2}\\ 
		e^{j2\theta_1} & e^{j2\theta_2} 
	\end{bmatrix} 
	\begin{bmatrix}
		e^{j\theta_1} & 0\\ 
		0 & e^{j\theta_2}
	\end{bmatrix}
	\begin{bmatrix}
		\sqrt{2} \\
		\sqrt{2}
	\end{bmatrix}
	= A^{(0)} D^1 S
$$
和
$$
\mathbf{Y}_2 = 
	\begin{bmatrix}
		1  & 1\\
		e^{j\theta_1} & e^{j\theta_2}\\ 
		e^{j2\theta_1} & e^{j2\theta_2} 
	\end{bmatrix} 
	\begin{bmatrix}
		e^{j2\theta_1} & 0\\ 
		0 & e^{j2\theta_2}
	\end{bmatrix}
	\begin{bmatrix}
		\sqrt{2} \\
		\sqrt{2}
	\end{bmatrix}
	= A^{(0)} D^2 S
$$

那么，可以推导出：
$$
\begin{aligned}
		\boldsymbol{R}^{(0)}_y &= \boldsymbol{A}^{(0)} I S S^H I^H (\boldsymbol{A}^{(0)})^H
		=\boldsymbol{A}^{(0)} I \boldsymbol{R}_s I^H (\boldsymbol{A}^{(0)})^H  \\
		\boldsymbol{R}^{(1)}_y &= \boldsymbol{A}^{(0)} D S S^H D ^H (\boldsymbol{A}^{(0)})^H
		=\boldsymbol{A}^{(0)} D \boldsymbol{R}_s D^H (\boldsymbol{A}_{(0)})^H  \\
		\boldsymbol{R}^{(2)}_y &= \boldsymbol{A}^{(0)} D^2 S S^H (D^2)^H (\boldsymbol{A}^{(0)})^H
		=\boldsymbol{A}^{(0)} D^2 \boldsymbol{R}_s (D^2)^H  (\boldsymbol{A}^{(0)})^H
	\end{aligned}
$$

对以上三个子协方差矩阵取平均，我们这里为了表述表述方便，省略了除以 3 ，不影响最终的证明。

$$
\begin{aligned}
		\boldsymbol{R}^{(0)}_y + \boldsymbol{R}^{(1)}_y + \boldsymbol{R}^{(2)}_y
		&= \boldsymbol{A}^{(0)} I \boldsymbol{R}_s I^H (\boldsymbol{A}^{(0)})^H +
		\boldsymbol{A}^{(0)} D^H \boldsymbol{R}_s D^H (\boldsymbol{A}_{(0)})^H +
		\boldsymbol{A}^{(0)} D^2 \boldsymbol{R}_s (D^2)^H  (\boldsymbol{A}^{(0)})^H  \\
		&= \boldsymbol{A}^{(0)} [\boldsymbol{R}_s +  D \boldsymbol{R}_s D^H + D^2 \boldsymbol{R}_s (D^2)^H] (\boldsymbol{A}^{(0)})^H
	\end{aligned}
$$

则：
$$
\tilde{\boldsymbol{R}}_s = \boldsymbol{R}_s +  D \boldsymbol{R}_s D^H + D^2 \boldsymbol{R}_s (D^2)^H
\tag{15}
$$
我们将 Rs 分解为：

$$
\boldsymbol{R}_s = 
	\begin{bmatrix}
		2 & 2 \\
		2 & 2
	\end{bmatrix}
	=
	\begin{bmatrix}
		1 & 1 \\
		1 & 1
	\end{bmatrix}
	\begin{bmatrix}
		1 & 1 \\
		1 & 1
	\end{bmatrix}
	= \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
$$

则式子(15) 可以写成：
$$
\begin{aligned}
		\tilde{\boldsymbol{R}}_s &= \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
		+ \boldsymbol{D} \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H \boldsymbol{D}^H
		+ \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H  (\boldsymbol{D}^2)^H \\
		&= 
		\begin{bmatrix}
			\boldsymbol{I} & \boldsymbol{D} & \boldsymbol{D}^2
		\end{bmatrix}
		\begin{bmatrix}
			\boldsymbol{R}_s^{1/2} & 0 & 0\\
			0 & \boldsymbol{R}_s^{1/2} & 0 \\
			0 & 0 & \boldsymbol{R}_s^{1/2}
		\end{bmatrix}
		\begin{bmatrix}
			\boldsymbol{R}_s^{1/2} & 0 & 0\\
			0 & \boldsymbol{R}_s^{1/2} & 0 \\
			0 & 0 & \boldsymbol{R}_s^{1/2}
		\end{bmatrix}^H
		\begin{bmatrix}
			\boldsymbol{I}^H \\
			\boldsymbol{D}^H \\
			(\boldsymbol{D}^2)^H
		\end{bmatrix}
	\end{aligned}
$$

则 G 矩阵为：
$$
\begin{aligned}
		\boldsymbol{G} &= \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
		+ \boldsymbol{D} \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H \boldsymbol{D}^H
		+ \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H  (\boldsymbol{D}^2)^H \\
		&= 
		\begin{bmatrix}
			\boldsymbol{I} & \boldsymbol{D} & \boldsymbol{D}^2
		\end{bmatrix}
		\begin{bmatrix}
			\boldsymbol{R}_s^{1/2} & 0 & 0\\
			0 & \boldsymbol{R}_s^{1/2} & 0 \\
			0 & 0 & \boldsymbol{R}_s^{1/2}
		\end{bmatrix} \\
		&=
		\begin{bmatrix}
			\boldsymbol{I}\boldsymbol{R}_s^{1/2} & \boldsymbol{D} \boldsymbol{R}_s^{1/2} & \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2}
		\end{bmatrix}
	\end{aligned}
\tag{16}
$$

把公式 (16) 展开后得：
$$
\begin{aligned}
		\boldsymbol{G} & = 
		\begin{bmatrix}
			1 & 1 & e^{j\theta_1} & e^{j\theta_1} &  e^{j2\theta_1} & e^{j2\theta_1}\\\\
			1 & 1 & e^{j\theta_2} & e^{j\theta_2} & e^{j2\theta_2}  & e^{j2\theta_2}
		\end{bmatrix} \\
		&= 
		\begin{bmatrix}
			1  & e^{j\theta_1} &  e^{j2\theta_1} & 1 & e^{j\theta_1} & e^{j2\theta_1}\\
			1 & e^{j\theta_2} & e^{j2\theta_2}  & 1 & e^{j\theta_2}  & e^{j2\theta_2}
		\end{bmatrix} \\
		&=
		\begin{bmatrix}
			\boldsymbol{b}_0 & \boldsymbol{b}_0 \\
			\boldsymbol{b}_1 & \boldsymbol{b}_1\\
		\end{bmatrix}_{2\times 6} \\
	\end{aligned}
$$

根据之前范德蒙矩阵的证明， b0 和 b1 是不相关的，所以，矩阵 G 的秩是 2， 是满秩的。

![图1：空间平滑的 MUSIC 算法 与 MUSIC 算法比较](/figure//mimo//music//SpatialSmoothingMUSIC.png)

*图1：空间平滑的 MUSIC 算法 与 MUSIC 算法比较*


## 用 SVD 分解来寻找噪声子空间
在 MUSIC 算法中，SVD（奇异值分解） 和 特征值分解（EVD, Eigenvalue Decomposition） 都可以用于获取噪声子空间，因为，对于共轭对称矩阵， SVD 分解和 EVD 分解的结果是一样的。但 SVD 有几个优势，特别是在实际计算中数值稳定性更好.

SVD 直接分解原始协方差矩阵 R，而 EVD 依赖于计算特征值和特征向量，容易受到 数值误差 影响，尤其是当信噪比（SNR）较低时。

在计算过程中，SVD 通过正交矩阵的分解，能减少小数值误差的影响，提高稳定性。	适用于低秩或退化矩阵
	
在某些情况下（如块平均后的协方差矩阵），R 可能是 退化矩阵（即某些特征值为零）。
	SVD 仍然可以计算出有效的噪声子空间，而 EVD 可能会受到影响





```
@ARTICLE{Schmidt_MUSIC_Algorithm,
  author={Schmidt, R.},
  journal={IEEE Transactions on Antennas and Propagation}, 
  title={Multiple emitter location and signal parameter estimation}, 
  year={1986},
  volume={34},
  number={3},
  pages={276-280},
  keywords={Parameter estimation;Sensor arrays;Sensor phenomena and characterization;Interference;Multiple signal classification;Direction of arrival estimation;Frequency estimation;Signal processing;Polarization;Working environment noise},
  doi={10.1109/TAP.1986.1143830}
}



@inproceedings{Schmidt1979,
  author    = {Schmidt, R.},
  title     = {Multiple emitter location and signal parameter estimation},
  booktitle = {Proceedings of RADC Spectrum Estimation Workshop},
  year      = {1979},
  publisher = {Saxpy Computer Corporation, USA},
  pages     = {243--258}
}



@ARTICLE{TIE_MUSIC_SPATIAL_SMOOTHING_1164649,
  author={Tie-Jun Shan and Wax, M. and Kailath, T.},
  journal={IEEE Transactions on Acoustics, Speech, and Signal Processing}, 
  title={On spatial smoothing for direction-of-arrival estimation of coherent signals}, 
  year={1985},
  volume={33},
  number={4},
  pages={806-811},
  keywords={Smoothing methods;Direction of arrival estimation;Sensor arrays;Signal analysis;Signal resolution;Yield estimation;Jamming;Computational complexity;Information systems;Frequency},
  doi={10.1109/TASSP.1985.1164649}
}
```

