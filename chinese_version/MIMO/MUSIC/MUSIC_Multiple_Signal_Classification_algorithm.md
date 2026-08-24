<head>
  <script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>

  <!-- Google tag (gtag.js) -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-V3E68S0VG4"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    gtag('config', 'G-V3E68S0VG4');
  </script>
</head>


[.返回主目录](/chinese_version.html) 

# MUSIC（Multiple Signal Classification）算法

[录制的视频在 B 站课程](https://www.bilibili.com/cheese/play/ep1486051)

MUSIC（多重信号分类）是一种 **基于子空间分解的高分辨率频谱估计算法**，常用于 **DOA（Direction of Arrival，入射方向，到达角）估计、f-k 频谱分析、雷达信号处理** 等领域。它利用 **信号子空间和噪声子空间的正交性** 来分辨不同信号的波束 k 或方向角 $$\theta$$，具有超分辨能力，可以分辨相邻很近的波束或到达角。

## MUSIC 算法基本思想

MUSIC 算法基于信号的协方差矩阵 $$\boldsymbol R_y$$ 进行特征值分解，将信号子空间与噪声子空间分离。然后，它利用信号子空间和噪声子空间的正交性来构造 **伪谱（pseudo-spectrum）**，并通过峰值搜索确定信号的波束或角度。

## MUSIC 算法步骤

### (1) 构造协方差矩阵

假设接收到的信号$$\mathbf{y}(t)$$ 由 $$M$$ 个平面波信号和噪声组成：
$$
\mathbf y(t)=\sum_{i=1}^M s_i(t) \mathbf a(k_i)+ \mathbf n(t)
\tag{1}
$$





其中：

- $$s_i(t)$$ 是第 $$i$$ 个信号的幅度

- $$\mathbf{a}(k_i)$$ 是对应波束 $$k_i$$ 的阵列流形向量 Manifold Vector

- $$\mathbf{n}(t)$$ 是噪声项



令天线数为 $$N$$.

若令 

$$
\mathbf A = [\mathbf{a}(k_1), \mathbf{a}(k_2), \dots, \mathbf{a}(k_M)], \quad \mathbf S(t) = [s_1(t),s_2(t),\dots,s_M(t)]^\text T
\tag{4}
$$

则公式 (1) 可以写成：

$$
\mathbf y(t)= \mathbf A \boldsymbol{S}(t) + \mathbf n(t)
\tag{2}
$$

计算信号的 **空间协方差矩阵**：
$$
\boldsymbol R_y=\text{E}[\mathbf y \mathbf y^H]
\tag{3}
$$


把公式 (2)代入 (3)并展开：
$$
\boldsymbol{R} = \text{E}\left [\left (\mathbf A \boldsymbol{S}(t) + \mathbf n(t) \right )
	\left (\mathbf A \boldsymbol{S}(t) + \mathbf n(t)\right )^H\right ]
$$

如果信号是平稳的，并且噪声是零均值高斯白噪声，另外，信号与噪声之间是不相关的，则：

$$
\boldsymbol R_y=\boldsymbol A \boldsymbol R_s \boldsymbol A^H+\sigma^2 \boldsymbol I
\tag{5}
$$


其中：

- $$\mathbf A = [\mathbf{a}(k_1), \mathbf{a}(k_2), \dots, \mathbf{a}(k_M)]$$ 是信号的阵列流形矩阵

- $$\boldsymbol R_s$$ 是信号的自相关矩阵

- $$\sigma^2 \boldsymbol I$$ 是噪声的协方差矩阵（假设噪声功率均匀）

### (2) 计算特征值分解（EVD）

对 $$\boldsymbol R_y$$ 进行特征值分解：
$$
\boldsymbol R_y = \boldsymbol U \boldsymbol\Lambda \boldsymbol U^H
$$


其中：

- $$\boldsymbol U = [\boldsymbol U_s, \boldsymbol U_n]$$ 是特征向量矩阵，由信号子空间 $$\boldsymbol U_s$$ 和噪声子空间 $$\boldsymbol U_n$$ 组成

- $$\boldsymbol \Lambda = \text{diag}(\lambda_1, \lambda_2, \dots, \lambda_N)$$ 是特征值矩阵

其中：

- **前 M 个较大的特征值** 对应信号子空间 $$\boldsymbol U_s$$

- **剩余较小的特征值** 对应噪声子空间 $$\boldsymbol U_n$$



则 
$$
\boldsymbol R_y = \boldsymbol U_s \boldsymbol\Lambda_s \boldsymbol U_s^H + \boldsymbol U_n \boldsymbol\Lambda_n \boldsymbol U_n^H
\tag{6}
$$

### (3) 计算 MUSIC 伪谱

由于噪声子空间 $$\boldsymbol U_n$$ 与信号的阵列流形 $$\mathbf{a}(k)$$ 正交，即：
$$
\boldsymbol U_n^H \mathbf{a}(k) \approx 0
$$


因此，可以构造 MUSIC 伪谱：
$$
P_{\text{MUSIC}}(k) = \frac{1}{\mathbf{a}^H(k) \boldsymbol U_n \boldsymbol U_n^H \mathbf{a}(k)}
$$


其中：

- $$\boldsymbol U_n$$ 是噪声子空间的特征向量矩阵

- $$\mathbf{a}(k)$$ 是待测的波束方向上的阵列流形

### (4) 通过峰值搜索估计波束

MUSIC 伪谱在正确的波束 $$k_i$$ 处会有峰值，因此：

- 通过搜索 $$P_{\text{MUSIC}}(k)$$ 的峰值，得到 $$k_1, k_2, \dots, k_M$$

- 这些峰值对应于信号的真实波束分量





## 伪谱的倒数结构

- MUSIC 伪谱的分母：
$$
\mathbf{a}^H(k) \boldsymbol U_n \boldsymbol U_n^H \mathbf{a}(k)
$$




- **如果 k 是真实波束**：$$\mathbf{a}(k)$$ 主要在信号子空间，不会在噪声子空间投影，分母趋近 0，使得 $$P_{\text{MUSIC}}(k)$$ 取最大值。

- **如果 k 不是真实波束**：$$\mathbf{a}(k)$$ 主要投影到噪声子空间，$$\boldsymbol U_n^H \mathbf{a}(k)$$ 非零，导致 $$P_{\text{MUSIC}}(k)$$ 取较小值。

✅ 所以，**真实波束的位置，MUSIC 伪谱会出现峰值**！

## 对协方差矩阵进行特征分解的含义

MUSIC 算法，是希望找到公式 (1) 中的 $$M$$ 个 $$**a**(k_i)$$. 也就是找到公式 (4) 中的 $$\mathbf{A}$$.

接下来分析这两个公式：公式 (5) 和 (6)。

### 1). 矩阵 $$A R_s A^H$$ 的结构

回顾信号的协方差矩阵：
$$
\boldsymbol R_y = \boldsymbol A \boldsymbol R_s \boldsymbol A^H + \sigma_n^2 \boldsymbol I
$$


其中：

- $$\boldsymbol A$$ 是 $$N \times M$$ 维的阵列流形矩阵，每一列 $$\mathbf{a}(k_i)$$ 是一个信号方向的流形向量： 

$$
\boldsymbol A = [\mathbf{a}(k_1), \mathbf{a}(k_2), ..., \mathbf{a}(k_M)]
$$



- $$\boldsymbol R_s$$ 是 $$M \times M$$ 的信号协方差矩阵，通常假设是对角矩阵： 

$$
\boldsymbol R_s = \text{diag}(\lambda_1, \lambda_2, ..., \lambda_M)
$$



其中 $$\lambda_i$$ 代表信号源的功率。

我们关注的是：

$$
\boldsymbol A \boldsymbol R_s \boldsymbol A^H
$$


它是一个 **$$N \times N$$ 的矩阵**，但其秩并不是 N，而是 **最多 M**。


### 2). 为什么 $$\boldsymbol A \boldsymbol R_s \boldsymbol A^H$$ 的秩最多是 M

**(a) 秩的基本定义**

矩阵的**秩（rank）是其线性无关列向量的个数**，即矩阵列空间的维度。

**(b) 先看 A的秩**

- **A 是 $$N \times M$$ 矩阵，列数为 M，但行数为 N**（通常 N>M）。

- A 的列向量张成的子空间，最多只有 M 维

- 直观上，A 只包含 M 个独立方向的信息，因此它的列空间最多是 M 维。

- 记 rank(A)=M（假设 A 满秩，否则会更低）。

**(c) 乘上 $$R_s$$ 之后**

$$
\boldsymbol A \boldsymbol R_s
$$



- **$$R_s$$ 是 $$M \times M$$ 的对角矩阵**，不会改变 A 的秩（假设所有信号功率 $$\lambda_i \neq 0$$）。

- $$\boldsymbol A \boldsymbol R_s$$ 仍然是一个 N×M 矩阵，它的列空间仍然是 A 的列空间。

- 再乘上 $$\boldsymbol A^H$$： 

$$
\boldsymbol A \boldsymbol R_s \boldsymbol A^H
$$

仍然只含有 M 个独立的方向，因为它仍然由 A 线性组合而成。

**(d) 结论**

- **$$A R_s A^H$$ 是一个 N×N 的矩阵，但它的秩至多是 M**，因为它最多只有 M 个线性独立的列向量。

- **换句话说，$$A R_s A^H$$ 只包含 M 维的信号方向信息，剩余的 N−M 维度没有任何信号信息（只有噪声）。**


### 3). 为什么 $$A R_s A^H$$ 的列空间由 A 的列向量张成？

因为：

$$
A R_s A^H = A (\text{diag}(\lambda_1, \lambda_2, ..., \lambda_M)) A^H
$$
我们来看每一步：

- **$$R_s$$ 只是对 A 进行缩放**（因为它是对角矩阵）。

- **$$A R_s$$ 仍然在 A 的列空间内**（因为 $$A R_s$$ 只是对 A 的列向量做了缩放）。

- **再乘上 $$A^H$$ 只是把这个空间投影回原来的列空间中**。

**结论**：

列空间($$\boldsymbol A\boldsymbol R_s\boldsymbol A^H$$)=列空间($$\boldsymbol A$$)

即：

- **矩阵 $$A R_s A^H$$ 的列向量，完全由 A 的列向量的线性组合构成**。

- **它的秩不会超过 A 的秩，因此最多是 M**。


### 4). 直观理解

你可以把 **$$A R_s A^H$$** 想象成一个**数据投影过程**：

- **A 把 $$s(t)$$ 映射到 N 维空间**。

- **$$R_s$$ 只是缩放，不改变方向**。

- **再乘 $$A^H$$ 只是把数据再映射回来**，但它仍然只能落在 A 所张成的子空间里。

所以，**$$A R_s A^H$$ 仍然处于 A 所张成的信号子空间中**，它的秩最多是 M。


### 5). 结合 MUSIC 算法

**(a) 信号子空间**

对 $$R_y = A R_s A^H + \sigma_n^2 I$$ 进行特征值分解：

$$
R_y = U \Lambda U^H
$$


- 由于 **$$A R_s A^H$$ 仅有 M 个非零特征值**，$$R_y$$ 也会有 M 个较大的特征值，对应信号子空间。

- 这 M 个特征向量（信号子空间的基）就是 **A 的列向量的线性组合**，因为 A 是 $$A R_s A^H$$ 的列空间的基。

**(b) 噪声子空间**

- $$R_y$$ 其余 N−M 个较小的特征值近似为 $$\sigma_n^2$$，对应噪声子空间。

- 噪声子空间的特征向量与 信号子空间正交，即：

$$
U_n^H A \approx 0
$$
这句话的解释，请参考下一节。




这正是 MUSIC 伪谱的核心：

$$
P_{\text{MUSIC}}(k) = \frac{1}{\mathbf{a}^H(k) U_n U_n^H \mathbf{a}(k)}
$$


- **真实的信号方向 $$k_i$$ 处，$$\mathbf{a}(k_i)$$ 在信号子空间内，MUSIC 伪谱取最大值（峰值）。**

- **错误的方向处，$$\mathbf{a}(k)$$ 主要投影到噪声子空间，MUSIC 伪谱值较小。**

## 解释：噪声子空间的特征向量与 信号子空间正交

**1). 为什么噪声子空间与信号子空间正交？**

从协方差矩阵 $$R_y$$ 的特征值分解（EVD）开始：
$$
R_y = U_s \Lambda_s U_s^H + U_n \Lambda_n U_n^H
$$
其中：

- **$$U_s$$ 是信号子空间的特征向量矩阵，对应于最大的 M 个特征值（信号能量较强）。**

- **$$U_n$$ 是噪声子空间的特征向量矩阵，对应于较小的特征值（通常接近噪声功率 $$\sigma_n^2$$）。**

- **由于特征向量矩阵 $$U = [U_s, U_n]$$ 是正交的，所以 $$U_s$$ 和 $$U_n$$ 互相正交。**

这意味着：
$$
U_n^H U_s = 0
$$
即**噪声子空间和信号子空间是完全正交的**。


**2). 为什么噪声子空间的特征向量与信号子空间正交？**

因为 **特征向量张成了子空间**，如果两个子空间正交，那么**属于一个子空间的任何向量都会与另一个子空间的任何向量正交**。

换句话说：

- $$U_n$$ 是噪声子空间的基向量，它的任何线性组合仍然属于噪声子空间。

- $$U_s$$ 是信号子空间的基向量，它的任何线性组合仍然属于信号子空间。

- 既然 $$U_s$$ 和 $$U_n$$ 正交，那么 $$U_n$$ 里面的每个特征向量都与 $$U_s$$ 里面的每个特征向量正交。

所以，我们可以更严谨地写成：
$$
U_n^H \mathbf{a}(k) \approx 0, \quad \forall k \in \text{信号方向}
$$


这意味着：

- 任何信号的流形向量 $$\mathbf{a}(k)$$ **几乎完全落在信号子空间内，而不会投影到噪声子空间**。

- **噪声子空间的特征向量与信号流形向量Manifold Vector正交**，这正是 MUSIC 伪谱利用的性质。


**3). 总结**

- **噪声子空间与信号子空间正交**。

- **因此，噪声子空间的特征向量与信号子空间的所有向量（包括信号流形向量）都正交**。

- **MUSIC 伪谱正是利用这个正交性来估计信号的真实波束或方向角**。

## 流形向量 Manifold Vector 构成的信号子空间中波束的唯一性

前面的总结可以看到， MUSIC 思想是通过找到噪声子空间（噪声子空间的基被找到），通过信号子空间与噪声子空间正交的特性来找信号子空间中的真实流形向量，即真正的入射 beam 方向。

那么问题是，与噪声子空间正交的信号子空间中，可以有无数多个向量，那么怎么就能确定唯一的真实信号向量（流形向量）呢？

我们需要证明，在所有的流形向量中，有且仅有 M 个唯一的向量与噪声子空间正交。
N 个接收天线，即流形向量是 N 行 1 列的向量。有 M 个流形向量（实际入射的 beam 方向) ，则噪声子空间是 N - M 维的。

首先，信号子空间是 M 维的，我们用反证法来证明，在所有流形向量中，有且仅有 M 个与 噪声子空间正交。

首先，流形向量构成的矩阵，是范德蒙矩阵，当矩阵中列向量的个数小于等于 N 个时，这 N 个列向量都是线性无关的，那么因为 M < N，所以，任选  M 个也是线性无关的。而信号空间是 M 维的，最多能找到  M 个不相关的流形向量（在信号空间内）。不可能找到 M + 1 个流形向量都与噪声子空间正交，因为如果有 M + 1 个流形向量与噪声子空间正交，则意味着这 M + 1 个流形向量都在信号子空间中，而信号子空间是 M 维的，所以， M + 1 个流形向量在信号子空间中，那么一定是线性相关的，而根据范德蒙矩阵的性质， M + 1 个列向量（当然，M + 1 <= N )都是线性无关的，这样就产生了矛盾。

## 范德蒙 Vandermonde 矩阵

对于一个 $$n \times n$$ 的 Vandermonde 矩阵：
$$
V = \begin{bmatrix} 1 & 1 & 1 & \cdots & 1 \\ \lambda_1 & \lambda_2 & \lambda_3 & \cdots & \lambda_n \\ \lambda_1^2 & \lambda_2^2 & \lambda_3^2 & \cdots & \lambda_n^2 \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ \lambda_1^{n-1} & \lambda_2^{n-1} & \lambda_3^{n-1} & \cdots & \lambda_n^{n-1} \end{bmatrix}
$$


其行列式（determinant）是：
$$
\det(V) = \prod_{1 \leq i < j \leq n} (\lambda_j - \lambda_i)
$$


- **如果所有的 $$\lambda_i$$ 都不相同**，则**行列式不为 0**，意味着 **Vandermonde 矩阵是满秩的，秩为 n**。
- **如果某些 $$\lambda_i$$ 相等**，则行列式变为 0，意味着 **矩阵不是满秩的，秩小于 n**。





而流形向量构成的矩阵，就是 Vandermonde 矩阵：
$$
V = \begin{bmatrix} 
		e^{j\theta_1\times0} & e^{j\theta_2\times0} & e^{j\theta_3\times0} & \cdots & e^{j\theta_n\times0} \\ 
		e^{j\theta_1\times1} & e^{j\theta_2\times1} & e^{j\theta_3\times1} & \cdots & e^{j\theta_n\times1} \\ 
		e^{j\theta_1\times2} & e^{j\theta_2\times2} & e^{j\theta_3\times2} & \cdots & e^{j\theta_n\times2} \\ 
		\vdots & \vdots & \vdots & \ddots & \vdots \\ 
		e^{j\theta_1\times(n-1)} & e^{j\theta_2\times(n-1)} & e^{j\theta_3\times(n-1)} & \cdots & e^{j\theta_n\times(n-1)} \\ 
	\end{bmatrix}
$$
写成类似 DFT 变换中的基的形式：
$$
V = \begin{bmatrix} 
		e^{j\frac{k_1}{n\times Q}\times0} & e^{j\frac{k_2}{n\times Q}\times0} & e^{j\frac{k_3}{n\times Q}\times0} & \cdots & e^{j\frac{k_n}{n\times Q}\times0} \\ 
		e^{j\frac{k_1}{n\times Q}\times1} & e^{j\frac{k_2}{n\times Q}\times1} & e^{j\frac{k_3}{n\times Q}\times1} & \cdots & e^{j\frac{k_n}{n\times Q}\times1} \\ 
		e^{j\frac{k_1}{n\times Q}\times2} & e^{j\frac{k_2}{n\times Q}\times2} & e^{j\frac{k_3}{n\times Q}\times2} & \cdots & e^{j\frac{k_n}{n\times Q}\times2} \\ 
		\vdots & \vdots & \vdots & \ddots & \vdots \\ 
		e^{j\frac{k_1}{n\times Q}\times(n-1)} & e^{j\theta_2\times(n-1)} & e^{j\frac{k_3}{n\times Q}\times(n-1)} & \cdots & e^{j\frac{k_n}{n\times Q}\times(n-1)} \\ 
	\end{bmatrix}
$$
$$k_i < n \times Q$$, 其中 $$Q$$ 是任意的正整数，表示 oversampling 的倍数。

代码在 github 上，https://github.com/taichiorange/leba\_math,目录为：leba\_math/MIMO/MIMO-beam-detection/beam-MUSIC-algorithm.py


![MUSIC 伪谱](/figure//mimo//music//music.png)

*MUSIC 伪谱*

## 协方差矩阵特征值分解与信号子空间

在前面文章中，我们推导出来协方差矩阵为：
$$
\mathbf{R}_y = \mathbf{A} \mathbf{R}_s \mathbf{A}^H + \sigma^2 \mathbf{I}
\tag{7}
$$

另外，我们使用了此协方差矩阵的特征值分解：
$$
\mathbf{R}_y = \mathbf{U} \Lambda \mathbf{U}^H
$$

我们先对公式 (7)  中信号有关的那部分做特征值分解 :
$$
\mathbf{A} \mathbf{R}_s \mathbf{A}^H = \mathbf{U} \hat \Lambda \mathbf{U}^H
\tag{9}
$$

由于只有 M 个信号，虽然 $$\mathbf{A} \mathbf{R}_s \mathbf{A}^H$$的维度是 N x N，但是，秩是 M (M < N), 因此：
$$
\hat \Lambda =
     \begin{bmatrix}
         \lambda_1 & \cdots & 0 & 0 & \cdots & 0 \\
         \vdots & \ddots & \vdots & \vdots & \ddots & 0\\
         0 & \cdots & \lambda_m & 0 & \cdots & 0\\
         0 & \cdots & 0 & 0 & \cdots & 0\\
         \vdots & \ddots & \vdots & \vdots & \ddots & \vdots\\
         0 &  \cdots & 0 & 0 & \cdots & 0
     \end{bmatrix}
\tag{8}
$$

将 (8) 代入 (9) 然后结果再代入 (7) 有：

$$
\begin{aligned}
    \mathbf{R}_y &= \mathbf{A} \mathbf{R}_s \mathbf{A}^H + \sigma^2 \mathbf{I} =
    \mathbf{U} \hat \Lambda \mathbf{U}^H + \sigma^2 \mathbf{I} \\
     &= 
         \begin{bmatrix}
    \mathbf{u}_1 & \cdots & \mathbf{u}_M 
    \end{bmatrix}
         \begin{bmatrix}
         \lambda_1 & \cdots & 0 \\
         \vdots & \ddots & \vdots \\
         0 & \cdots & \lambda_m 
     \end{bmatrix}
    \begin{bmatrix}
    \mathbf{u}_1^H \\
    \vdots \\
    \mathbf{u}_M^H
    \end{bmatrix}
    +  \sigma^2 \mathbf{I}
  \end{aligned}
\tag{10}
$$

将 (9) 代入 (7)有：

$$
\begin{aligned}
    \mathbf{R}_y &= \mathbf{U} \hat \Lambda \mathbf{U}^H + \sigma^2 \mathbf{I}  \\
                &= \mathbf{U} (\hat \Lambda + \sigma^2 \mathbf{I}) \mathbf{U}^H  \\
                &= \mathbf{U} \Lambda \mathbf{U}^H
  \end{aligned}
$$

其中：
$$
\Lambda = \hat \Lambda + \sigma^2 \mathbf{I}
    =
     \begin{bmatrix}
         \lambda_1+\sigma^2 & \cdots & 0 & 0 & \cdots & 0 \\
         \vdots & \ddots & \vdots & \vdots & \ddots & 0\\
         0 & \cdots & \lambda_m+\sigma^2 & 0 & \cdots & 0\\
         0 & \cdots & 0 & \sigma^2 & \cdots & 0\\
         \vdots & \ddots & \vdots & \vdots & \ddots & \vdots\\
         0 &  \cdots & 0 & 0 & \cdots & +\sigma^2
     \end{bmatrix}
$$

令：
$$
\mathbf{U} = 
    \begin{bmatrix}
    \mathbf{u}_1 & \cdots & \mathbf{u}_M & \mathbf{u}_{M+1} & \cdots \mathbf{u}_N   
    \end{bmatrix}
$$

则：
$$
\mathbf{R}_y = 
    \begin{bmatrix}
    \mathbf{u}_1 & \cdots & \mathbf{u}_M 
    \end{bmatrix}
         \begin{bmatrix}
         \lambda_1+\sigma^2 & \cdots & 0 \\
         \vdots & \ddots & \vdots \\
         0 & \cdots & \lambda_m+\sigma^2 
     \end{bmatrix}
    \begin{bmatrix}
    \mathbf{u}_1^H \\
    \vdots \\
    \mathbf{u}_M^H
    \end{bmatrix}
    +
    \begin{bmatrix}
    \mathbf{u}_{M+1} & \cdots & \mathbf{u}_N 
    \end{bmatrix}
         \begin{bmatrix}
         \sigma^2 & \cdots & 0 \\
         \vdots & \ddots & \vdots \\
         0 & \cdots & \sigma^2 
     \end{bmatrix}
    \begin{bmatrix}
    \mathbf{u}_{M+1}^H \\
    \vdots \\
    \mathbf{u}_N^H
    \end{bmatrix}
\tag{11}
$$

对比 (10)  和 (11) 可以看出，信号子空间是由前 M 个特征向量线性组合而成.



## 信号统计相关对 MUSIC 算法的影响
在前面的分析中，我们假定信号的自相关矩阵是一个对角矩阵，即不同 beam 上的信号，在统计上不相关。那么，问题是，如果相关了，那会得到什么样的结果？
$$
\boldsymbol R_y=\boldsymbol A \boldsymbol R_s \boldsymbol A^H+\sigma^2 \boldsymbol I
\tag{12}
$$

其中：
$$
R_s = \boldsymbol{\text{E}}
	\left [
	\begin{bmatrix}
		s_1  \\ 
		\vdots \\
		s_k\\
		s_{k+1}\\
		\vdots
		s_m
	\end{bmatrix}
	\begin{bmatrix}
		s^*_1 & \cdots & s^*_k & s^*_{k+1} & \cdots & s^*_m
	\end{bmatrix}
	\right ]
$$

以及：$$\mathbf A = [\mathbf{a}_1, \mathbf{a}_2, \dots, \mathbf{a}_M)]$$，其中 $$\mathbf{a}_i$$ 是列流形向量。

如果这 M 个信号统计上不相关，则：

$$
R_s =
	\begin{bmatrix}
		r_{11} & \cdots & 0      & 0         &\cdots  & 0 \\
		\vdots & \ddots & \vdots &\vdots     & \ddots &\vdots \\
		0      & \cdots & r_{kk} & 0         & \cdots & 0 \\
		0      & \cdots & 0      &r_{k+1,k+1}& \cdots & 0 \\
		\vdots & \ddots & \vdots &\vdots     & \ddots &\vdots \\
		0      & \cdots & 0      &0          &\cdots  & r_{MM}
	\end{bmatrix}
$$
这是一个对角矩阵。

若前 k 个信号统计相关，则：
$$
R_s =
	\begin{bmatrix}
		r_{11} & \cdots & r_{1,k}   & 0         &\cdots  & 0 \\
		\vdots & \ddots & \vdots &\vdots     & \ddots &\vdots \\
		r_{k,1}& \cdots & r_{kk} & 0         & \cdots & 0 \\
		0      & \cdots & 0      &r_{k+1,k+1}& \cdots & 0 \\
		\vdots & \ddots & \vdots &\vdots     & \ddots &\vdots \\
		0      & \cdots & 0      &0          &\cdots  & r_{MM}
	\end{bmatrix}
$$

其中
$$
\text{rank}
	\left \{
	\begin{bmatrix}
		r_{11} & \cdots & r_{1,k}   \\
		\vdots & \ddots & \vdots  \\
		r_{k,1}& \cdots & r_{kk} 
	\end{bmatrix}  
	\right \} = 1
$$

因为秩是 1， 所以，列之间必成比例,假设比例是 $$c_i$$，可以写成：

$$
\text{rank}
	\left \{
	\begin{bmatrix}
		r_{1} & \cdots & c_i r_1 & \cdots & c_k r_1   \\
		\vdots & \ddots & \vdots & \ddots & \vdots  \\
		r_{k}& \cdots & c_i r_k & \cdots & c_k r_k 
	\end{bmatrix}  
	\right \} = 1
$$

那么对公式 (12) 中 的 $$\boldsymbol A \boldsymbol R_s$$ 展开可以得到：

$$
\begin{aligned}
		& \boldsymbol A \boldsymbol R_s = \\
		& \begin{bmatrix}
			r_1\mathbf{a}_1+\cdots+r_k\mathbf{a}_k &
			c_2(r_1\mathbf{a}_1+\cdots+r_k\mathbf{a}_k) & \cdots &
			c_k(r_1\mathbf{a}_1+\cdots+r_k\mathbf{a}_k) &
			r_{k+1,k+1} \mathbf{a}_{k+1} &
			\cdots &
			r_{MM} \mathbf{a}_{M} &
		\end{bmatrix}
	\end{aligned}
\tag{13}
$$

可以看到，式子 (13) 中前 K 个向量，已经是相同方向的向量，仅仅相差一个比例系数，所以，这个式子的秩已经变成了 M-K+1. 前 K 个流形向量，已经线性组合出来了一个新的向量：$$r_1\mathbf{a}_1+\cdots+r_k\mathbf{a}_k$$.

因此，信号空间是由以下向量构成的：
$$
r_1\mathbf{a}_1+\cdots+r_k\mathbf{a}_k, \quad \mathbf{a}_{k+1},\quad \cdots,\quad \mathbf{a}_M
$$

噪声空间是与上述向量正交，因此，用 MUSIC 算法，我们只能找到
$$\mathbf{a}_{k+1},\quad \cdots,\quad \mathbf{a}_M$$ 这些流形向量。其它的 K 个，由于秩的降低，导致 k 个流形向量塌陷成一个非流形向量，故而无法找出。

在前面的例子中，有 5 个 beam，现在假如前 3 个 beam 对应的信号 s 是相同的，即完全相关，则得到如下的伪谱：

![信号有相关情况下的伪谱](/figure//mimo//music//MUSIC_rankReduced.png)

*信号有相关情况下的伪谱*

从图中可以看到，是找到了两个流形向量(beam 角度).

代码在 github 中：https://github.com/taichiorange/leba\_math/blob/main/MIMO/MIMO-beam-detection/beam-MUSIC-algorithm-k-signal-equal.py


## 证明正定

### 证明特征值都是实数
$$\boldsymbol R_y$$ 是接收到的信号的协方差矩阵，其定义是：

$$
\boldsymbol R_y= \boldsymbol{\text{E}}[\boldsymbol{y}\boldsymbol{y}^H]
$$

由于, $$\boldsymbol R_y$$ 是自身共轭转置的 Hermitian 矩阵（即 $$\boldsymbol R_y = \boldsymbol R_y^H$$，所以它的特征值必定是实数。

### 现在证明其正定性
为了证明 $$\boldsymbol R_y$$ 是正定矩阵或半正定矩阵，考虑对任意非零向量 $$\boldsymbol{x}$$ 计算二次型：

$$
\boldsymbol{x}^H \boldsymbol{R}_y \boldsymbol{x} = 
	\boldsymbol{x}^H ( \boldsymbol{A} \boldsymbol{R}_s \boldsymbol{A}^H + \sigma^2 \boldsymbol{I})\boldsymbol{x}
$$

将其拆开：
$$
\boldsymbol{x}^H \boldsymbol{R}_y \boldsymbol{x} = 
	\boldsymbol{x}^H  \boldsymbol{A} \boldsymbol{R}_s \boldsymbol{A}^H \boldsymbol{x} + \boldsymbol{x}^H  \sigma^2 \boldsymbol{I} \boldsymbol{x}
$$

其中：

$$\boldsymbol{x}^H  \sigma^2 \boldsymbol{I} \boldsymbol{x} = \sigma^2 \| \boldsymbol{x} \|^2 \ge 0$$

$$
\boldsymbol{x}^H  \boldsymbol{A} \boldsymbol{R}_s \boldsymbol{A}^H \boldsymbol{x} = 
\boldsymbol{x}^H  \boldsymbol{A} \boldsymbol{s}^H \boldsymbol{s}\boldsymbol{A}^H \boldsymbol{x}
= \| \boldsymbol{s}\boldsymbol{A}^H \boldsymbol{x}\|^2 \ge 0
$$

由于这两项相加的结果始终大于等于 0， 所以 $$\boldsymbol R_y$$ 是正定或半正定矩阵。 \\

从矩阵理论角度来看：

-正定矩阵的特征值严格为正数。

-半正定矩阵的特征值非负（即最小特征值可能为零，但不会是负数）。