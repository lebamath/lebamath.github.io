---
layout: default
title: "MIMO 线性检测"
back_url: /index.html?lang=zh
---
# MIMO 线性检测

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1060677)

## 匹配滤波 Matched Filter

这篇文章讲解一下 MIMO 检测算法的一个小类别：线性算法。

我们的问题是已知接收的信号 Y，以及信道系数矩阵 H，估计发送的数据 X.


![System.png](/figure/MIMO检测/System.png)

这里说的线性算法，指的是通过对 Y 的线性组合来估计出发送的数据 X，可以表示为：

$$
\hat X = T Y
$$

其中 $$Y$$ 是接收到的信号构成的列向量，$$\hat X$$ 是估计出来的发送数据构成的列向量，$$T$$ 是一个矩阵，这样，估计出来的发送的数据，就是接收到的数据的线性组合，例如：

$$
\begin{aligned}
\hat x_1 = t_{11}y_1+t_{12}y_2+...+t_{1N_r}y_{N_r}  \\
... \\
\hat x_k = t_{k1}y_1+t_{k2}y_2+...+t_{kN_r}y_{N_r}  \\
...\\
\hat x_{N_t} = t_{k1}y_1+t_{k2}y_2+...+t_{kN_r}y_{N_r}
\end{aligned}
$$

这篇文章介绍三种典型的 MIMO 的线性 detector(检测)。

匹配滤波器(MF: Matched Filter)

$$
\hat X = H^H Y  -------公式(1)
$$

因为:

$$
\begin{aligned}
y_1 &= h_{11}x_1 + h_{12} x_2 + ... + h_{1N_t} x_{N_t} + n_1 \\
y_2 &= h_{21}x_1 + h_{22} x_2 + ... + h_{2N_t} x_{N_t} + n_2 \\
... \\
y_{N_r} &= h_{N_r1}x_1 + h_{N_r2} x_2 + ... + h_{N_rN_t} x_{N_t} + n_{N_r} \\
\end{aligned}
$$

而公式(1) 可以写成：

$$
\begin{aligned}
\hat x_1 &= h_{11}^* y_1 + h_{21}^* y_2 + ... + h_{N_r 1}^* y_{N_r} \\
\hat x_2 &= h_{12}^* y_1 + h_{22}^* y_2 + ... + h_{N_r 2}^* y_{N_r} \\
... \\
\hat x_{N_t} &= h_{12}^* y_1 + h_{22}^* y_2 + ... + h_{N_r 2}^* y_{N_r} \\
\end{aligned}
$$

我们把其中一个估计，展开看看：

$$
\begin{aligned}
\hat x_2 &= h_{12}^* y_1 + h_{22}^* y_2 + ... + h_{N_r 2}^* y_{N_r} \\ 
&=h_{12}^*h_{21}x_1 + h_{12}^*h_{12}x_2+...+h_{12}^*h_{1N_t}x_{N_t} + \\
&\ h_{22}^*h_{21}x_1 + h_{22}^*h_{22}x_2+...+h_{22}^*h_{2N_t}x_{N_t} + \\
&\ \ \ ... + \\
&\ h_{N_r 2}^*h_{N_r 1}x_1 + h_{N_r 2}^*h_{N_r 2}x_2+...+h_{N_r 2}^*h_{ N_r N_t}x_{N_t} \\
&= (h_{12}^*h_{12}+h_{22}^*h_{22}+...+h_{N_r 2}^*h_{N_r 2}) x_2 + .....
\end{aligned}
$$

我们用向量的方式来表示，更简洁，容易理解：

令:

$$
H = [h_1,h_2,...,h_{N_t}]
$$

则：

$$
Y = h_1 x_1 + h_2 x_2 + ...+ h_{N_t} x_{N_t} + n
$$

而:

$$
H^H = 
\begin{bmatrix}
	h_1^H \\
	h_2^H \\
	... \\
	h_{N_t}^H
\end{bmatrix}
$$

从而：

$$
\hat X = H^H Y = 
\begin{bmatrix}
	h_1^H h_1 x_1 + h_1^H h_2 x_2+...+h_1^H h_{N_t} x_{N_t} + h_1^Hn \\
	h_2^H h_1 x_1 + h_2^H h_2 x_2+...+h_2^H h_{N_t} x_{N_t} + h_2^Hn\\
	...\\
	h_{N_t}^H h_1 x_1 + h_{N_t}^H h_2 x_2+...+h_{N_t}^H h_{N_t} x_{N_t} + h_{N_t}^Hn 
\end{bmatrix}
$$

例如：

$$
\begin{aligned}
\hat x_2 &=h_2^H h_1 x_1 + h_2^H h_2 x_2+...+h_2^H h_{N_t} x_{N_t} + h_2^Hn\\
&= h_2^H h_2 x_2 + h_2^H h_1 x_1 + h_3^H h_3 x_3+...+h_2^H h_{N_t} x_{N_t}+ h_2^Hn
\end{aligned}
$$

除了第一项，后面都是干扰项。

如果满足正交性，则干扰就会都消失掉。


![MF_BER_Curve.png](/figure/MIMO检测/MF_BER_Curve.png)

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}


## ZeroForcing 和 MMSE
![System.png](/figure/MIMO检测/System.png)
### Zero Forcing

令 

$$
T = (H^H H)^{-1} H^H
$$

那么 Zero Forcing 的检测公式为：

$$
\hat X = T Y
$$

将

$$
Y=HX+ W
$$

代入有：

$$
\hat X = (H^H H)^{-1} H^H (HX + W) = X + (H^H H)^{-1} H^H W
$$

可以看到，来自其他发送天线的干扰被完全剔除了。



### MMSE

$$
G = (H^H H + \sigma^2 I)^{-1} H^H
$$

则：

$$
\hat X = G Y = G H X + GW = (H^H H + \sigma^2 I)^{-1} H^H HX + (H^H H + \sigma^2 I)^{-1} H^H W
$$

当噪声小的时候，应该类似于 Zero Forcing，当噪声比较大时，接近 Matched Filter.



这里有一个有趣的现象，当噪声比较小的时候，MMSE 还是比 ZF 要好。有人专门对这个现象进行了理论分析，感兴趣的可以去阅读文献：

Y. Jiang, M. K. Varanasi and J. Li, "Performance Analysis of ZF and MMSE Equalizers for MIMO Systems: An In-Depth Study of the High SNR Regime," in IEEE Transactions on Information Theory, vol. 57, no. 4, pp. 2008-2026, April 2011, doi: 10.1109/TIT.2011.2112070.






## MF ZF LMMSE 对比的代码
![MIMO_detector_MF_ZF_MMSE.jpg](/figure/MIMO检测/MIMO_detector_MF_ZF_MMSE.jpg)

见 github   \url{https://github.com/taichiorange/leba_math.git} 

这个程序运行会比较慢，可以修改 N，减小一点。 SNR 步长也可以调大一点。

天线数量也可以修改一下。

## zero forcing 算法的思想
MIMO 检测算法中， Zero Forcing 算法在很多书中是直接给出了计算公式的，本文试图从其最初始的思考点出发，来看一下这个算法背后的思想。



假设 MIMO 信道 模型为：

$$
Y = HX + n
$$

其中 H 为信道系数矩阵，是已知的（假设已经被准确地做了信道估计），Y 是接收到的信号，n 是高斯白噪声。



那么 Maximum Likelihood(ML) 算法是最优的检测，这个最优指的是使错误率最低（假定发送的 x 是等概率出现的），从最低错误率的角度出发，同时假定在每个天线处的高斯白噪声是独立同分布的，那么，这个 ML 算法的公式为：

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} ||Y-HX||^2   \tag{1}
$$

遍历 X 的所有可能取值，找到是公式 (1) 最小的。



因为公式 (1) 的计算量非常大，在实际中是不可行的。那么对公式  (1) 放开条件，让 X 的取值，不仅限于星座图中的值，而是任何值，那么，这个就是 **zero forcing(ZF)** 算法的出发点，则公式 (1) 就变成：

$$
\hat X = argmin_X ||Y-HX||^2   \tag{2}
$$

注意 argmin 的下表中的 X ，没有做任何限制。公式 (2) 就是一个无约束的最优化问题，我们令：

$$
f(X) =||Y-HX||^2 \tag{3}
$$

接下来对公式 (3) 做进一步的推导（我们约定所有的向量都是列向量）：

$$
\begin{aligned}
	f(X) &=||Y-HX||^2  \\
	&= (Y-HX)^H (Y-HX)  \\
	&= (Y^H - X^H H^H) (Y-HX) \\
	&= Y^HY - Y^H HX - X^H H^H Y + X^HH^HHX
\end{aligned}
\tag{4}
$$

把公式 (4) 对 $$X^*$$ 求导，公式 (4) 实际上是一个数，X 是一个向量，这个求导的过程，实际上就是对 (4) 用 $$X^*$$ 的每个分量分别求一次导数并令其等于 0，得到 N ( 假如  X 是 N 维的列向量) 个方程，联合起来可以求解出 X 的每个分量。用矩阵形式来写就是：

$$
\frac {\partial Y^H HX}{\partial X^*} = 0
$$

$$
\frac {\partial X^H H^H Y}{\partial X^*} = H^H Y
$$

$$
\frac {\partial X^HH^HHX}{\partial X} = H^H H X
$$

则

$$
0-0-H^HY+ H^H H X = 0
$$

进一步推导

$$
H^H H X = H^H Y
$$

最后：

$$
X = (H^H H )^{-1} H^H Y  \tag{5}
$$

如果 H 是方阵且 可逆，公式 (5) 可以写成：

$$
X = H^{-1} Y
$$

这样得出的值，就是检测后的估计值，即用 Zero Forcing 算法估计出来的值，我们写成：

$$
\tilde X = (H^H H )^{-1} H^H Y  \tag{6}
$$

或者简化后的（H 是方阵且可逆的情况下）：

$$
\tilde X = H^{-1} Y
$$

然后，再做解调检测

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} ||\tilde X-X||^2   \tag{7}
$$

![MIMO_detector_MF_ZF_MMSE.jpg](/figure/MIMO检测/MIMO_detector_MF_ZF_MMSE.jpg)

后续思考： Zero Forcing 算法比 Maximum Likelihood 算法性能差的原因是啥？

## Zero Forcing 算法比 ML 差的思考
MIMO 检测中， **maximum likelihood (ML)**  是最优的检测，其数学表达式为：

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} ||Y-HX||^2   \tag{1}
$$

$$\mathcal{X}$$ 表示星座图的集合.  $$Y$$ 是接收到的数据.



而 **zero forcing (ZF)** 是基于下面这个思想：

$$
\tilde X = argmin_{X} ||Y-HX||^2    \tag{2}
$$

公式 (2) 与公式 (1) 的区别在于对  X  的限制，公式 (1) 中的 X 要在调制的 constellation 星座图的点中选择，而公式 (2) 没有这个限制，可以自由选择，显然公式 (2) 的解会偏离公式 (1) 的解。



公式 (2) 的解，再用 maximum likelihood 方式进行解调：

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} || \tilde X - X||^2   \tag{3}
$$

由公式 (2) 得到的最优解为(当 H 是方阵且可逆)：

$$
\tilde X = H^{-1} Y  \tag{4}
$$

将 (4) 代入 (3) 有：

$$
\begin{aligned}
	\hat X &= argmin_{X\in \mathcal{X}^{M_t}} || \tilde X - X||^2  \\
	&=  argmin_{X\in \mathcal{X}^{M_t}} || H^{-1}Y - X||^2 \\
	&=  argmin_{X\in \mathcal{X}^{M_t}} ||H^{-1} (Y - HX)||^2 \\
\end{aligned} \tag{5}
$$

这里假定 H 是方阵且可逆的，则 H 的逆矩阵也可以做 SVD 分解：

$$
H^{-1} = U\Sigma V^H   \tag{6}
$$

则公式 5 中的：

$$
||H^{-1} (Y - HX)||^2 = || U\Sigma V^H  (Y - HX)||^2  \tag{7}
$$

我们把  $$\Sigma V^H  (Y - HX)$$ 看成一个向量，则矩阵 $$U$$ 因为是单位正交矩阵，所以，是一个刚性旋转，不改变被作用的向量的长度，因此 公式 (7) 可以继续推导为：

$$
||H^{-1} (Y - HX)||^2 = || \Sigma V^H  (Y - HX)||^2  \tag{8}
$$

公式 (8) 中，我们把 $$Y- HX$$ 看成一个向量，记为 $$Z$$，则 $$V^H$$  是对这个向量 $$Z$$ 做旋转，不改变向量长度，旋转后的向量记为 $$\hat Z$$， 则 $$\Sigma$$ 矩阵，是对向量各个分量进行缩放：

$$
\Sigma = \begin{bmatrix}
	\lambda_1 &  & \\
	& ... & \\
	&  & \lambda_N
\end{bmatrix}
$$

### 情况一： 所有特征值都相等

当 $$\lambda_1=\cdots=\lambda_N = \lambda$$ 时，公式 (8) 可以写成：

$$
||H^{-1} (Y - HX)||^2 = || \Sigma V^H  (Y - HX)||^2 = || \lambda I V^H  (Y - HX)||^2 = \lambda^2 ||V^H  (Y - HX)||^2\tag{9}
$$

因为 $$V^H$$ 是单位正交的矩阵，因此，是一个刚性旋转，不改变后面向量的长度，因此公式 (9) 可以写成：

$$
||H^{-1} (Y - HX)||^2 =  \lambda^2 || (Y - HX)||^2 \tag {10}
$$

这种情况下， Zero Forcing 算法就与 Maximum Likelihood 算法的性能一致。



### 情况二：噪声非常小，趋近于 0 

那么公式 (8) 中，一定能找到一个 X 向量，使得 $$Y - HX$$ 为 0 向量，因为是 0 向量，所以，这个就是最小值，不可能找到另外一个向量 X' ，使得  (8) 的结果比 0 小。所以，在噪声为 0 的情况下，Zero Forcing 算法就与 Maximum Likelihood 算法的性能一致。



### 情况三：特征值不全都相等，也含有噪声



则公式 (8) 可以写成：

$$
\begin{aligned}
	||H^{-1} (Y - HX)||^2 
	&= || \Sigma V^H  (Y - HX)||^2 \\
	\\
	&= || \begin{bmatrix}
		\lambda_1 \hat Z_1 \\
		...  \\
		\lambda_N  \hat Z_N
	\end{bmatrix}  ||^2 
	\\
	\\
	& = \lambda_1^2 ||\hat Z_1 ||^2 + \cdots + \lambda_1^N ||\hat Z_N ||^2   
\end{aligned}
\tag {11}
$$

公式 （11） 中第一个等号那里，相当于对向量 $$Y-HX$$ 先做旋转，然后再在各个维度上做缩放，例如都是都是逆时针旋转一个固定角度，两个维度分别是放大三倍和缩小一倍（以二维向量为例子，方便画图理解），则不同的向量，即使长度相同，但是角度不同，经过旋转和缩放后，长度将会不同。



下图蓝色是初始向量，旋转 $$\alpha$$ 后变成绿色向量，然后经过 X 轴放大 三倍， Y 轴缩小 1/2 ，变成右边紫色向量。


![MIMO_detection_MaximumLikelihood_vs_ZeroForcing-旋转缩放示意图 1.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing-旋转缩放示意图 1.png)



用相同的旋转和缩放，对下图蓝色初始向量进行操作，得到右边紫色向量。

![MIMO_detection_MaximumLikelihood_vs_ZeroForcing-旋转缩放示意图 2.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing-旋转缩放示意图 2.png)

从上面两个实例可以看出，旋转缩放前，第一个图的原始向量要比第二个图的原始向量要短，但是做了同样的旋转缩放后，第一个图的结果向量反而比第二个图的结果向量要长，可见，旋转缩放后，会改变向量长度的大小关系，进而影响 Zero Forcing 算法达不到 Maximum Likelihood 算法的效果，即可能找到的不是最优解。



现在我们来对比一下公式 (1) 和公式 (5) 中的第二行，为了讨论方便，我都列在这里：

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} ||Y-HX||^2    \tag {12}
$$

$$
\hat X =  argmin_{X\in \mathcal{X}^{M_t}} || H^{-1}Y - X||^2   \tag {13}
$$

这两个公式看起来很像，都是在 X 的所有可能取值中进行遍历，找对应向量长度的最小值，但是，从几何意义上，这两者有本质性的区别，我们接下来分析一下，并且能看出来  Zero Forcing 算法为什么比 Maximum Likelihood 算法的性能要差了。

我们以 H 矩阵是方阵且可逆的， H 做 SVD 分解为：

$$
H=U\Sigma V^H   \tag{14}
$$

其中 U ,V 都是单位正交矩阵，在几何上就是对向量做旋转，而 $$\Sigma$$ 是对角矩阵，对角线上的元素是其特征值。



公式 (12) 的含义是，发送的某个特定数据，经过 H 矩阵作用，然后加高斯噪声，得到的结果是 Y，然后遍历所有 X 的可能取值，对每个取值同样做 H 矩阵的作用，然后与收到的 Y 比较，最近的那个就是最佳估计。



公式 (13) 的含义，是收到的数据 Y  (发送的某个特定数据，经过 H 矩阵作用，然后加高斯噪声，得到的结果是 Y,  Y = HX + W) 通过 H 的反作用回到发送时的情况，然后再与 X 的各种取值进行比较。那么这个反作用的时候，是把噪声 W 也反作用了。本来噪声在各个天线上（体现在空间中的各个维度上），是同性分布的，即相同的概率分布，但是经过 H 的反作用后，噪声就会有的被放大，有的被缩小，导致各个维度上的概率分布是不同的。



我们以 2x2 MIMO 为例子，用 BPSK 调制，那么每根天线上发送的要么是 +1, 要么是 -1，则两根天线上发的数据就有四种组合，我们把在某个时刻两个天线上发送的数据看成一个向量，则这个向量是二维的，那么我们就可以在二维空间上来理解.



那么，我们发送的向量，在二维空间中，只有四个向量，分别在四个象限的中间点上，以 横坐标为第一个维度，纵坐标为第二个维度，则四个向量在二维空间中的坐标分别为 [+1,+1],  [-1,+1], [-1, -1], [+1, -1]. 如下图所示，以先旋转一个角度，再横坐标放大为原来的 3 倍，纵坐标缩小为原来的 1/2， 然后再做已给旋转。我们来看公式 (12)  和 (13) 对应的效果。

![MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-1.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-1.png)


旋转拉伸之后的

![MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-2.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-2.png)

原始噪声的分布

![MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-3.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-3.png)


反变换之后的噪声分布


![MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-4.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing_2x2例子-4.png)


下图是仿真结果，用 4x4 的 MIMO.

![MIMO_detection_MaximumLikelihood_vs_ZeroForcing.png](/figure/MIMO检测/MIMO_detection_MaximumLikelihood_vs_ZeroForcing.png)

代码 见 github   \url{https://github.com/taichiorange/leba_math.git} 

## MIMO ZF SIC 迫零逐次消除

这篇文章主要来讨论 基于MIMO Zero Forcing 算法的逐次消除算法 SIC（Successive Interference Cancellation），即 MIMO ZF SIC 检测。在前面的一些文章中，我们讨论了 Zero Forcing 算法的细节以及其背后的基本思想，在阅读本文之前需要了解基本的 MIMO ZF 检测算法。



MIMO ZF 算法，是例如如下的公式，同时把所有的发送数据都估计（检测）出来：

$$
\hat X = (H^HH)^{-1} H^H Y  \tag{1}
$$

其中， H 是信道系数矩阵，维度是 $$N_r \times N_t$$ 的， Y 是接收到的数据，是一个列向量，维度是 $$N_r$$，$$\hat X$$ 是对发送数据向量 $$X$$ 的估计（检测），维度是 $$N_t$$ 的。



而逐次消除算法 SIC，是按照某种顺序，逐个估计，估计出来的数据后，再把这个发送的数据，通过信道系数的作用后，从接收的数据 Y 中减除掉，然后再估计剩余的发送数据中的某个。我们以一个 4 根发射天线 4 根接收天线的情况为例子，即 $$N_t = N_r = 4$$。

**步骤一：**我们利用公式  (1) ，首先估计出 $$x_4$$，即向量 $$\hat X$$ 的第四行。从计算的角度，可以用公式 (1) 估计出 4 个发送数据，然后取出来 $$x_4$$，这样计算量稍大，矩阵乘法相关的知识，我们可以再求完矩阵的逆之后，只用第四行与 $$H^H Y$$ 相乘：

令  $$A = (H^HH)^{-1}$$，则取 A 的第 k 行，记为 $$a_k$$，注意，这个是一个行向量，含有 $$N_t$$ 个元素，则 $$x_k$$ 的估计值就是：

$$
\hat x_k = a_k H^H Y  \tag{2}
$$

在这个例子中：

$$
\hat x_4 = a_4 H^H Y  \tag{3}
$$

**步骤二：**从 Y 中减除掉已经估计出来的 $$x_4$$ 产生的影响

$$
Y^{(1)} = Y - h_4 x_4   \tag{4}
$$

其中 $$h_4$$ 是  H 的第四列，可以理解为第 4 根天线上发送的数据 $$x_4$$，发往 4 根接收天线上的比例系数，或者说增益系数。



**步骤三：**因为第四根发射天线上的数据已经被估计出来并从 Y 中减除了，那么，信道系数矩阵中第四根发射天线的系数就都不需要了，即:

$$
H^{(1)} = H \quad  移除第四列  \tag{5}
$$

则 $$H^{(1)}$$ 就是 4 行 3 列的矩阵。

**步骤四：** 用新生成的$$Y^{(1)}$$ 和 $$H^{(1)}$$， 重复步骤一来估计 $$x_3$$。



下图是 8x8 的 ZF 与 ZF SIC 的误比特率的对比图：


![MIMO_ZF_SIC_8x8.png](/figure/MIMO检测/MIMO_ZF_SIC_8x8.png)

代码 见 github   \url{https://github.com/taichiorange/leba_math.git} 

## MIMO ZF OSIC 迫零的优化排序逐次消除检测算法
这篇文章主要来讨论 基于MIMO Zero Forcing 算法的逐次消除算法 SIC（Successive Interference Cancellation）的改进版本。看本文之前需要理解 MIMO Zero Forcing 算法以及其 SIC 算法。

MIMO ZF 算法，是例如如下的公式，同时把所有的发送数据都估计（检测）出来：

$$
\hat X = (H^HH)^{-1} H^H Y  \tag{1}
$$

其中， H 是信道系数矩阵，维度是 $$N_r \times N_t$$ 的， Y 是接收到的数据，是一个列向量，维度是 $$N_r$$，$$\hat X$$ 是对发送数据向量 $$X$$ 的估计（检测），维度是 $$N_t$$ 的。



逐次消除算法 SIC，是按照某种顺序，逐个估计，估计出来的数据后，再把这个发送的数据，通过信道系数的作用后，从接收的数据 Y 中减除掉。而优化的 SIC ( Optimiazed SIC,  OSIC) 是按照某种最优准则来设计逐个估计的顺序。

比较直观的理解，是接收到的某个信号比较强，那我们就应该优先估计那个信号。

例如 4x4 的 MIMO 系统，第二根天线上发射的数据 $$x_2$$，在四根天线上收到的数据为：

$$
h_{12} x_2, \quad h_{22} x_2, \quad h_{32} x_2, \quad h_{42} x_2
$$

极端来讲，如果四个系数都是 0，即都被阻断了，那这个信号肯定是最差的，不能优先检测。

所以，从能量的角度看，接收端能收到的信号的能量，就可以写成：

$$
(|h_{12}|^2+|h_{22}|^2+|h_{32}|^2+|h_{42}|^2) |x_2|^2   \tag{2}
$$

我们一般假定发射的信号的功率是相同的，即从每个发射天线上发出的信号的能量是相等的，那么，从接收端来看，接收到的能量大的，对应的就是 系数模的平方和最大：

$$
\sum_j |h_{jk}|^2  \tag{3}
$$

例如 4x4 的，我们就比较下面四个，看哪个大就优先估计哪个：

$$
\begin{aligned}
(|h_{11}|^2+|h_{21}|^2+|h_{31}|^2+|h_{41}|^2)  \\
(|h_{12}|^2+|h_{22}|^2+|h_{32}|^2+|h_{42}|^2)  \\
(|h_{13}|^2+|h_{23}|^2+|h_{33}|^2+|h_{43}|^2)  \\
(|h_{14}|^2+|h_{24}|^2+|h_{34}|^2+|h_{44}|^2)  
\end{aligned}
$$

![MIMO_ZF_SIC_OSIC_4x4.png](/figure/MIMO检测/MIMO_ZF_SIC_OSIC_4x4.png)

代码 见 github   \url{https://github.com/taichiorange/leba_math.git} 

## MMSE SIC 迫零逐次消除检测算法
这篇文章主要来讨论 基于MIMO MMSE 算法的逐次消除算法 SIC（Successive Interference Cancellation）。在前面的一些文章中，我们讨论了 MMSE 算法的细节以及其背后的基本思想，在阅读本文之前需要了解基本的 MIMO MMSE 检测算法。



MIMO MMSE 算法，是例如如下的公式，同时把所有的发送数据都估计（检测）出来：

$$
\hat X = (H^HH+\sigma^2I)^{-1} H^H Y  \tag{1}
$$

其中， H 是信道系数矩阵，维度是 $$N_r \times N_t$$ 的， Y 是接收到的数据，是一个列向量，维度是 $$N_r$$，$$\hat X$$ 是对发送数据向量 $$X$$ 的估计（检测），维度是 $$N_t$$ 的，$$\sigma^2$$ 是 噪声能量（实际上这里应该是信噪比的倒数，假定发射信号的能量为 1，那么信噪比在数值上就等于噪声的能量值）。



而逐次消除算法 SIC，是按照某种顺序，逐个估计，估计出来的数据后，再把这个发送的数据，通过信道系数的作用后，从接收的数据 Y 中减除掉，然后再估计剩余的发送数据中的某个。我们以一个 4 根发射天线 4 根接收天线的情况为例子，即 $$N_t = N_r = 4$$。

步骤一：我们利用公式  (1) ，首先估计出 $$x_4$$，即向量 $$\hat X$$ 的第四行。从计算的角度，可以用公式 (1) 估计出 4 个发送数据，然后取出来 $$x_4$$，这样计算量稍大，矩阵乘法相关的知识，我们可以再求完矩阵的逆之后，只用第四行与 $$H^H Y$$ 相乘：

令  $$A = (H^HH+\sigma^2I)^{-1}$$，则取 A 的第 k 行，记为 $$a_k$$，注意，这个是一个行向量，含有 $$N_t$$ 个元素，则 $$x_k$$ 的估计值就是：

$$
\hat x_k = a_k H^H Y  \tag{2}
$$

在这个例子中：

$$
\hat x_4 = a_4 H^H Y  \tag{3}
$$

步骤二：从 Y 中减除掉已经估计出来的 $$x_4$$ 产生的影响

$$
Y^{(1)} = Y - h_4 x_4   \tag{4}
$$

其中 $$h_4$$ 是  H 的第四列，可以理解为第 4 根天线上发送的数据 $$x_4$$，发往 4 根接收天线上的比例系数，或者说增益系数。



步骤三：因为第四根发射天线上的数据已经被估计出来并从 Y 中减除了，那么，信道系数矩阵中第四根发射天线的系数就都不需要了，即:

$$
H^{(1)} = H \quad  移除第四列  \tag{5}
$$

则 $$H^{(1)}$$ 就是 4 行 3 列的矩阵。

步骤四：用新生成的$$Y^{(1)}$$ 和 $$H^{(1)}$$， 重复步骤一来估计 $$x_3$$。



下图是 4x4 的 MMSE 与 MMSE SIC 的误比特率的对比图：

代码 见 github   \url{https://github.com/taichiorange/leba_math.git} 

![MIMO_MMSE_SIC_4x4.png](/figure/MIMO检测/MIMO_MMSE_SIC_4x4.png)


## MMSE OSIC 迫零的优化排序逐次消除检测算法
这篇文章主要来讨论 基于MIMO MMSE 算法的逐次消除算法 SIC（Successive Interference Cancellation）的改进版本。看本文之前需要理解 MIMO MMSE 算法以及其 SIC 算法。

MIMO MMSE 算法，是例如如下的公式，同时把所有的发送数据都估计（检测）出来：

$$
\hat X = (H^HH+\sigma^2I)^{-1} H^H Y  \tag{1}
$$

其中， H 是信道系数矩阵，维度是 $$N_r \times N_t$$ 的， Y 是接收到的数据，是一个列向量，维度是 $$N_r$$，$$\hat X$$ 是对发送数据向量 $$X$$ 的估计（检测），维度是 $$N_t$$​ 的。$$\sigma^2$$ 是 噪声能量（实际上这里应该是信噪比的倒数，假定发射信号的能量为 1，那么信噪比在数值上就等于噪声的能量值）。



逐次消除算法 SIC，是按照某种顺序，逐个估计，估计出来的数据后，再把这个发送的数据，通过信道系数的作用后，从接收的数据 Y 中减除掉。而优化的 SIC ( Optimiazed SIC,  OSIC) 是按照某种最优准则来设计逐个估计的顺序。

比较直观的理解，是接收到的某个信号比较强，那我们就应该优先估计那个信号。

例如 4x4 的 MIMO 系统，第二根天线上发射的数据 $$x_2$$，在四根天线上收到的数据为：

$$
h_{12} x_2, \quad h_{22} x_2, \quad h_{32} x_2, \quad h_{42} x_2
$$

极端来讲，如果四个系数都是 0，即都被阻断了，那这个信号肯定是最差的，不能优先检测。

所以，从能量的角度看，接收端能收到的信号的能量，就可以写成：

$$
(|h_{12}|^2+|h_{22}|^2+|h_{32}|^2+|h_{42}|^2) |x_2|^2   \tag{2}
$$

我们一般假定发射的信号的功率是相同的，即从每个发射天线上发出的信号的能量是相等的，那么，从接收端来看，接收到的能量大的，对应的就是 系数模的平方和最大：

$$
\sum_j |h_{jk}|^2  \tag{3}
$$

例如 4x4 的，我们就比较下面四个，看哪个大就优先估计哪个：

$$
(|h_{11}|^2+|h_{21}|^2+|h_{31}|^2+|h_{41}|^2)  \\
(|h_{12}|^2+|h_{22}|^2+|h_{32}|^2+|h_{42}|^2)  \\
(|h_{13}|^2+|h_{23}|^2+|h_{33}|^2+|h_{43}|^2)  \\
(|h_{14}|^2+|h_{24}|^2+|h_{34}|^2+|h_{44}|^2)
$$

代码 见 github   \url{https://github.com/taichiorange/leba_math.git} 

![MIMO_MMSE_OSIC_4x4.png](/figure/MIMO检测/MIMO_MMSE_OSIC_4x4.png)

## MMSE 的公式的推导

这篇文章推导一下 MIMO MMSE 检测算法的数学公式。MMSE：Minimum Mean Squared Error.

$$
Y=HX+W   \tag{1}
$$

其中 H 是信道系数矩阵，是 MxN 的，X 是发送信号向量，Nx1的列向量，Y 是接收到的信号，是 Mx1的列向量，W 是加性高斯白噪声，也是 Mx1 的列向量。



我们的目标是找一个矩阵 G，用 G 作用在 Y 上来估计发送的 X，即：

$$
\hat X = GY  \tag{2}
$$

其中$$\hat X$$ 是对发送向量 X 的估计，由于矩阵乘法是一个线性操作，因此这个算法也更准确地被称为 Linear MMSE 检测算法。

我们要找一个合适的 G，使得下式最小：

$$
E_{X,W}||X-\hat X||^2 \tag{3}
$$

这个式子的含义是，$$X-\hat X$$  是估计的向量与原始的向量之间的误差向量，再对这个向量取其模长。

而这套系统中，我们是假定信道系数矩阵 H 已知，在这些条件下，那么只有 发送向量 X  和 噪声W 是未知的（由于 Y = HX +W ，因此 Y 也是未知的，但是可以用 X 和 W 计算出来），我们把他们看成随机变量，所以，我们从这两个随机变量的角度，看统计意义下的误差最小，即名称中 Mean 这个单词的含义。

也就是说，我们不是针对某个特定的发送向量，或者某个特定的白噪声，来计算 G，使得误差最小（误差的模长最小），而是看所有发送的向量的各种可能，所有可能的白噪声，考虑这些所有的可能后来使得误差最小。这是与 Zero Forcing 算法不太一样的地方（待详细叙述）。

把 (2) 代入 (3) ，同时，用数学语言来表示找最小，我们得到如下这个公式：

$$
\hat G = \underset{G}{argmin} \{E_{X,W}||X-GY||^2\} \tag{4}
$$

接下来就是纯数学推导了：

$$
\begin{aligned}
	||X-GY||^2 &= (X-GY)^H (X-GY) = (X^H-Y^H G^H)(X-GY) \\
	&=X^H X - X^H GY - Y^H G^H X + Y^H G^H GY   
\end{aligned}  \tag{5}
$$

对公式 (5) 相对 G 求导，利用如下公式 (a,b 均为列向量，G 为矩阵)

$$
\begin{aligned}
\frac{ \partial{a^H G b} } { \partial G} &= a^* b^{\text T}  \\
\frac{ \partial{a^H G^H b} } {\partial G} &= 0  \\
\frac{ \partial{a^H G^H G b} } {\partial G} &= G^*a^* b^H
\end{aligned}
$$

则：

$$
\begin{aligned}
\frac{\partial X^H GY}{\partial G} &= X^* Y^{\text T}  \\
\frac{\partial Y^H G^H X}{\partial G} &= 0 \\
\\
\frac{ \partial Y^H G^H GY}{\partial G} &= G^* Y^*Y^{\text T}
\end{aligned}
$$

那么

$$
\frac{\partial E_{X,W}||X-GY||^2}{\partial G} = E_{X,W}(-X^* Y^{\text T} + G^* Y^*Y^{\text T}) \tag{6}
$$

令公式（6）等于 0 ，即相当于求解公式 (5) 的极值问题，则：

$$
G^* E_{X,W}(Y^*Y^{\text T}) = E_{X,W}(X^* Y^{\text T})  \tag{7}
$$

两边再取共轭：

$$
G E_{X,W}(Y Y^{\text H}) = E_{X,W}(X Y^{\text H})
$$

当然，也可以对 $$G^*$$ 来求导，利用:

$$
\begin{aligned}
	\frac{ \partial{a^H G b} } { \partial G^*} &= 0  \\
	\frac{ \partial{a^H G^H b} } {\partial G^*} &= b a^{\text H}  \\
	\frac{ \partial{a^H G^H G b} } {\partial G^*} &= G b a^H
\end{aligned}
$$

则

$$
\begin{aligned}
	\frac{\partial X^H GY}{\partial G^*} &= 0  \\
	\frac{\partial Y^H G^H X}{\partial G^*} &= X Y^{\text H} \\
	\\
	\frac{ \partial Y^H G^H GY}{\partial G^*} &= G YY^{\text H}
\end{aligned}
$$

代入整理后可得同样结果。

其中两个相关矩阵的计算如下：

$$
\begin{aligned}
	E_{X,W}(YY^H) &= E_{X,W}(HX+W)(HX+W)^H \\
	&= E_{X,W} (HX+W)(X^H H^H+W^H) \\
	&= E_{X,W} (HXX^H H^H+HXW^H + WX^H H^H + W W^H) \\
	&= E_{X,W} (HXX^H H^H)+E_{X,W} (HXW^H) + E_{X,W} (WX^H H^H) + E_{X,W} (W W^H) \\
	&=  H E_{X,W}(XX^H) H^H+H E_{X,W} (XW^H) + E_{X,W} (WX^H) H^H + E_{X,W} (W W^H) \\
	&= H I H^H + H \times0+ 0 \times H^H + \sigma^2 I \\
	&= H H^H + \sigma^2 I
\end{aligned}  \tag{8}
$$

以及

$$
\begin{aligned}
	E_{X,W}(XY^H) &= E_{X,W}X(HX+W)^H \\
	&= E_{X,W} X(X^H H^H+W^H) \\
	&= E_{X,W} (XX^H H^H+XW^H) \\
	&= E_{X,W} (XX^H H^H)+E_{X,W} (XW^H) \\
	&=  E_{X,W}(XX^H) H^H+ 0  \\
	&= I H^H  \\
	&= H^H 
\end{aligned}  \tag{9}
$$

把 (8) (9) 代入 (7) :

$$
G(H H^H + \sigma^2 I)=H^H \tag {10}
$$

最终得到：

$$
G=H^H (H H^H + \sigma^2 I)^{-1} \tag {11}
$$

需要注意的是，公式 (11) 也可以写成：

$$
G= (H^H H + \sigma^2 I)^{-1} H^H \tag {12}
$$

即：

$$
H^H (H H^H + \sigma^2 I)^{-1} = (H^H H + \sigma^2 I)^{-1} H^H  \tag{13}
$$

## 证明：MIMO-MMSE检测公式的两种表达方法是相等的
我们从这个式子出发：

$$
H^HHH^H+\sigma^2  H^H   \tag{1}
$$

首先左边提取公因式得到：

$$
H^HHH^H+\sigma^2  H^H=H^H(HH^H+\sigma^2 I)  \tag{2}
$$

然后，从右边提取公因式得到：

$$
H^HHH^H+\sigma^2  H^H=(H^HH+\sigma^2 I)  H^H   \tag{3}
$$

所以：

$$
H^H(HH^H+\sigma^2 I) = (H^H H+\sigma^2 I)  H^H  \tag{4}
$$

再把括号中的矩阵分别放到对侧去得到：

$$
(H^HH+\sigma^2 I)^{-1} H^H =   H^H  (HH^H+\sigma^2 I)^{-1} \tag{5}
$$

## ML、ZF、MMSE 思想的探讨和对比
这篇文章试图来讨论一下 MIMO 检测中 Zero Forcing 算法和 MMSE 算法背后的思想。具体的公式推导，可以参考前面发的几篇文章。



最大似然检测 (ML)，是找如下的最优估计/检测：

Maximum Likelihood(ML) 算法是最优的检测，这个最优指的是使错误率最低（假定发送的 x 是等概率出现的），从最低错误率的角度出发，同时假定在每个天线处的高斯白噪声是独立同分布的，那么，这个 ML 算法的公式为：

$$
\hat X = argmin_{X\in \mathcal{X}^{M_t}} ||Y-HX||^2   \tag{1}
$$

遍历 X 的所有可能取值，找到是公式 (1) 最小的。

因为公式 (1) 的计算量非常大，在实际中是不可行的。那么对公式  (1) 放开条件，让 X 的取值，不仅限于星座图中的值，而是任何值，那么，这个就是 **zero forcing(ZF)** 算法的出发点，则公式 (1) 就变成：

$$
\hat X = argmin_X ||Y-HX||^2   \tag{2}
$$

注意 argmin 的下表中的 X ，没有做任何限制。



MMSE 算法：

$$
\hat G = \underset{G}{argmin} \{E_{X,W}||X-GY||^2\}, \quad \quad \text{where} \quad   X\in \mathcal{X}^{M_t} \tag{3}
$$

则：

$$
\hat X = \hat G Y \tag{4}
$$

ML 算法是把 X 的估计，约束在星座图上，在可能的取值（有限个）上找使得 与  Y 最接近的。

ZF 算法把星座图的约束去掉，则在所有可能取值（无限多）上找与 Y 最接近的，这个时候，是把噪声看成一个确定值，则这个噪声值的情况下，找一个 X 使得 HX 与 Y 最接近，因为 X 没有在星座图上的限制，则噪声就会让找到的 X 不仅去拟合发送的 X，而且还尽可能把噪声也拟合上：

$$
Y = HX + W =H \hat X \tag{5}
$$

如果噪声比较大，则估计出来的 X 就偏离原始发的 X 比较远。那么，我们自然想，能否不这么极端，能否用噪声的统计特性，知道噪声的某种均值，把这个均值考虑到估计中来？ 这样我们就得到公式 (3)，我们要找的 G，是使得在统计意义上 估计值与发送值之间的误差最小。因为要引入统计特性，因此，似乎不能用公式 (2) 的 $$||Y-HX||^2$$ 来求数学期望，要做一点转变，因为公式 (2) 中是把 X 当成某个未知的特定值（噪声也是未知的特定值），Y 是已知的特定值，因此是找确定值情况下的最小值问题，不涉及到统计（大量多次数据的统计特性）。

公式 (4) 找到的统计意义下最优的 G，使得 $$E_{X,W}||X-GY||^2$$ 最小。



ZF 是找 某个 X ，使得某个度量最小；

MMSE 是找个 G，使得某个度量最小。

## MMSE 的公式的推导-从最优化理论角度
这篇文章从最优化的角度推导一下 MIMO MMSE 检测算法的数学公式。MMSE：Minimum Mean Squared Error.

$$
Y=HX+W   \tag{1}
$$

其中 H 是信道系数矩阵，是 MxN 的，X 是发送信号向量，Nx1的列向量，Y 是接收到的信号，是 Mx1的列向量，W 是加性高斯白噪声，也是 Mx1 的列向量。



### zero forcing(ZF) 算法

$$
\hat X = argmin_X ||Y-HX||^2   \tag{2}
$$

### 当 特征值不全都相等，也含有噪声



则：

$$
\begin{aligned}
	||H^{-1} (Y - HX)||^2 
	&= || \Sigma V^H  (Y - HX)||^2 \\
	\\
	&= \left \| \begin{bmatrix}
		\lambda_1 \hat Z_1 \\
		...  \\
		\lambda_N  \hat Z_N
	\end{bmatrix}  \right \| ^2 
	\\
	\\
	& = \lambda_1^2 ||\hat Z_1 ||^2 + \cdots + \lambda_1^N ||\hat Z_N ||^2   
\end{aligned}
\tag {3}
$$

公式 （3） 中第一个等号那里，相当于对向量 $$Y-HX$$ 先做旋转，然后再在各个维度上做缩放。



当 H 的条件数大的时候，则性能不好。为了客服这个现象，从最优化学科角度出发，对公式 (2) 加入一个 regularization term:

$$
\hat X = argmin_X \left (||Y-HX||^2 + \sigma^2 ||X||^2  \right)  \tag{4}
$$

则对上面这个公式进行求解极值，也可以推导出 MMSE 检测用的公式（这是有趣！）

$$
\begin{aligned}kki+
	||Y-HX||^2 + \sigma^2 ||X||^2 & = (Y-HX)^H(Y-HX) + \sigma^2 X^H X \\
	&=(Y^H - X^HH^H)(Y-HX) + \sigma^2 X^H X \\ 
	&= Y^H Y - Y^H HX - X^HH^H Y + X^HH^H HX + \sigma^2 X^H X 
\end{aligned}  \tag{5}
$$

对 X 求导：

$$
\begin{aligned}
	& \frac{\partial (Y^H Y - Y^H HX - X^HH^H Y + X^HH^H HX + \sigma^2 X^H X )} {\partial X} \\
	&= 0 - H^HY - H^H Y + 2 H^HH X+ 2 \sigma^2 X =0
\end{aligned}  \tag{6}
$$

进一步推导有：

$$
(H^H H + \sigma^2 I) X = H^H Y  \tag{7}
$$

所以：

$$
X = (H^H H + \sigma^2 I )^{-1} H^H Y  \tag{8}
$$

这个就是 MMSE 算法的公式。虽然也能推导出来 MMSE 用的公式，但是名称 "MMSE" 的由来，却是从我们第一个推导或者说思想的出发点上得来的。


0、 ，kki+section{ MIMO 均衡后的SINR/SNR }

MIMO 系统模型如下：

$$
Y = HX + N
$$

MIMO 线性均衡是找到一个矩阵 $$G$$，得到发送信号 $$X$$ 的估计 $$\hat X$$:

$$
\hat X = GY
$$

本文试图分析均衡后的信干比或者信噪比，主要讨论两种均衡算法：迫零均衡和MMSE 均衡，其中 MMSE 均衡下的信干比最难推导，所以，本文大部分篇幅是推导 MMSE 均衡下的信干比。

## 迫零算法的信噪比

我们知道迫零均衡算法的均衡矩阵 $$G$$ 为：

$$
G = (H^{\text H} H)^{-1} H^H
$$

所以均衡后得到的估计信号$$\hat X$$ 为：

$$
\begin{aligned}
		\hat X &= (H^{\text H} H)^{-1} H^H ( HX + N) \\
		&= (H^{\text H} H)^{-1} H^H  HX + (H^{\text H} H)^{-1} H^H N \\
		&= X +  (H^{\text H} H)^{-1} H^H N
		&= X + \hat N
	\end{aligned}
$$

其中 $$\hat N = (H^{\text H} H)^{-1} H^H N$$ 表示均衡后的噪声列向量。

由于迫零算法没有信号间的干扰，因此，信干比(SINR) 其实就是信噪比(SNR).

则第 $$l$$ 路的噪声为：

$$
\begin{aligned}
		\text E\left [\hat N \hat N^{\text H}\right ]_{ll} &= \text E\left [(H^{\text H} H)^{-1} H^{\text H} N \left ((H^{\text H} H)^{-1} H^H N\right )^{\text H} \right ]_{ll}  \\
		&= \text E\left [(H^{\text H} H)^{-1} H^{\text H} N N^\text H H (H^{\text H}H)^{-1} \right ]_{ll} \\
		&= \left [(H^{\text H} H)^{-1} H^{\text H} E[N N^\text H] H (H^{\text H}H)^{-1} \right ]_{ll} \\
		&= \sigma^2 \left [(H^{\text H} H)^{-1} H^{\text H} H (H^{\text H}H)^{-1} \right ]_{ll} \\
		&= \sigma^2 \left [(H^{\text H} H)^{-1} \right ]_{ll}
	\end{aligned}
$$

则信噪比为：

$$
\text{SNR}_{\text{ZF}} = \frac{1}{\sigma^2 \left [(H^{\text H} H)^{-1} \right ]_{ll}}
$$

接下来我们分析一下这个信噪比。因为 $$H^{\text H} H$$ 是共轭对称矩阵，因此，其特征值分解可以表示为：

$$
H^{\text H} H = Q \Sigma Q^{\text H}
$$

其中 $$Q$$ 是单位正交复数矩阵，也称为酉矩阵(Unitary Matrix)。

则逆矩阵为：

$$
(H^{\text H} H)^{-1} = Q \Sigma^{-1} Q^{\text H}
$$

令 $$q_l$$ 表示 $$Q^{\text H}$$ 的第 $$l$$ 列，则 $$Q$$ 的第 $$l$$ 行是： $$q_l^{\text H}$$。

那么

$$
\left [(H^{\text H} H)^{-1} \right ]_{ll} = q_l^{\text H} \Lambda^{-1} |q_l|^2 = \sum_{i} q_{li}\lambda_i^{-1}
$$

其中 $$\lambda_i$$ 是 $$H^{\text H} H$$ 的第 $$i$$ 个特征值。

则信噪比的最终公式为：

$$
\text{SNR}_{\text{ZF}} = \frac{1}{\sigma^2  \sum_{i} |q_{li}|^2\lambda_i^{-1}}
$$

如果矩阵 $$H^\text H H$$的特征值中有非常小的，则会导致各路信噪比都变得很差，因为即使只有一个很小的特征值，则 $$\sum_{i} |q_{li}|^2\lambda_i^{-1}$$ 都会变得比较大，因此，各路信号的信噪比都变差。



## MMSE 均衡算法的信干比 SINR

我们知道，MMSE 均衡算法的均衡矩阵  $$G$$ 为：

$$
G_{\text {MMSE}} = (H^\text H H + \sigma^2 I ) ^{-1} H^\text H
$$

则得到估计的信号 $$\hat X$$ 为：

$$
\begin{aligned}
		\hat X &= G_{\text{MMSE}} Y = (H^\text H H + \sigma^2 I ) ^{-1} H^\text H (HX + N) \\
		&= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X + (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N 
	\end{aligned}
\tag{2}
$$

由于 MMSE 均衡没有完全消除信号间的干扰，所以，MMSE 均衡下的信干比就包含噪声和干扰，需要分别计算出噪声和干扰。

### 计算噪声功率
先计算噪声向量的协方差矩阵:

$$
\begin{aligned}
		R_N &=\text E\left [ \left ( (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N \right )
		\left ( (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N \right )^{\text H} \right ] \\
		&= \text E\left [  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N 
		N^\text H H (H^\text H H + \sigma^2 I ) ^{-1} \right ] \\
		&= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H \text E\left [N 
		N^\text H\right ] H (H^\text H H + \sigma^2 I ) ^{-1}  \\
		&= \sigma^2  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H  H (H^\text H H + \sigma^2 I ) ^{-1}
	\end{aligned}
\tag{1}
$$

由于 $$H^\text H H$$ 是共轭对称矩阵(Hermitian Matrix)，所以，其特征值分解可以表示为：

$$
H^\text H H = Q^\text H \Lambda Q
$$

其中 $$Q$$ 是单位正交复数矩阵，因此 $$Q^{-1}= Q^\text H$$.

代入噪声的协方差矩阵(1)有：

$$
\begin{aligned}
		R_{\text N} &= \sigma^2 (Q^\text H \Lambda Q+ \sigma^2 I)^{-1} Q^\text H \Lambda Q (Q^\text H \Lambda Q+ \sigma^2 I)^{-1} \\
		&=  \sigma^2 \left ( Q^\text H (\Lambda+ \sigma^2 I) Q\right )^{-1}  Q^\text H \Lambda Q   \left ( Q^\text H (\Lambda+ \sigma^2 I) Q\right )^{-1}  \\
		&= \sigma^2  Q^\text H (\Lambda+ \sigma^2 I)^{-1} Q Q^\text H \Lambda Q  Q^\text H (\Lambda+ \sigma^2 I)^{-1} Q \\
		&= \sigma^2  Q^\text H (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} Q
	\end{aligned}
$$

则第 $$l$$ 路信号上的噪声能量就是噪声协方差矩阵 $$R_N$$ 的第 $$l$$行 第 $$l$$ 列.

令：

$$
Q = \begin{bmatrix} q_1 & q_2 & \cdots & q_l & \cdots & \end{bmatrix}
$$

其中 $$q_l$$ 是 $$Q$$ 的列向量.

则第 $$l$$ 路信号上的噪声能量为：

$$
P_{\text{noise}} =R_{\text N}(l,l) = \sigma^2 q_l^\text H  (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} q_l
\tag{6}
$$

### 信号能量
公式 (2) 中信号 $$X$$ 的系数矩阵为 $$(H^\text H H + \sigma^2 I ) ^{-1} H^\text H H$$，将特征值分解代入后有：

$$
\begin{aligned}
		(H^\text H H + \sigma^2 I ) ^{-1} H^\text H H 
		&= (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}   Q^\text H \Lambda Q \\
		&= Q^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda Q
	\end{aligned}
$$

则第 $$l$$ 路信号（不包含其它路信号的干扰）的系数就是上面系数矩阵的第 $$l$$行 第 $$l$$ 列,因此，信号的系数为：

$$
q_l^\text H (\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l
\tag{3}
$$

第 $$l$$ 路信号的能量为：

$$
P_{\text{sig}} = q_l^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda q_l q_l^\text H \Lambda (\Lambda +   \sigma^2 I ) ^{-1} q_l
\tag{5}
$$

### 干扰的能量
干扰的能量不好直接推导出来，但是信号加干扰的能量方便计算，所以我们推导出信号加干扰的总能量，再减去信号的能量，则可以推导出干扰的能量。

公式 (3)系数矩阵中的第 $$l$$ 行，包含信号 $$X_l$$及其干扰的信号的系数，我们先推导协方差矩阵：

$$
\begin{aligned}
		R_X &= \text E \left [ ((H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X)((H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X)^\text H   \right ]  \\
		&= \text E \left [ (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X X^\text H  H^\text H H (H^\text H H + \sigma^2 I ) ^{-1}\right ]  \\
		&=  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H \text E \left [ X X^\text H\right ]  H^\text H H (H^\text H H + \sigma^2 I )^{-1} \\
		&= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H H^\text H H (H^\text H H + \sigma^2 I )^{-1} 
	\end{aligned}
$$

将特征值分解代入上式有：

$$
\begin{aligned}
		R_X &= (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}Q^\text H \Lambda Q Q^\text H \Lambda Q  (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}  \\
		&= Q^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} Q
	\end{aligned}
$$

所以，信号加干扰的能量，就是信号协方差矩阵 $$R_X$$ 的第 $$l$$ 行第 $$l$$ 列。

$$
R_X(l,l) = q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} q_l
\tag{4}
$$

则公式 (4)减去公式 (5)得到的就是干扰的能量：

$$
\begin{aligned}
		P_{\text{inter}} &= q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} q_l - q_l^\text H (\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l q_l^\text H \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l  \\
		&= q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda (I - q_l q_l^\text H) \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l
	\end{aligned}
\tag{7}
$$

### 噪声加干扰的能量
将噪声能量和干扰能量加在一起，即把公式 (6) 和 公式 (7) 加在一起：

$$
\begin{aligned}
		P_{\text{noise+inter}} &= P_{\text{noise}} + P_{\text{inter}} \\ 
		&=\sigma^2 q_l^\text H  (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} q_l + q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda (I - q_l q_l^\text H) \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l  \\
		&= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
		(\sigma^2 \Lambda + \Lambda^2 - \Lambda q_l q_l^\text H \Lambda)
		(\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
		&= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
		(\sigma^2 \Lambda + \Lambda^2)(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
		q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
		&= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
		(\sigma^2 I + \Lambda)\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
		q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
		&=q_l^\text H\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
		q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
		&= c - c^* c
	\end{aligned}
$$

其中 $$c = q_l^\text H\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l$$ 是一个复数标量，$$c^*$$ 表示复数 $$c$$的共轭.

### 信干比 SINR

公式 (5) 中的信号能量，可以写成：

$$
P_{\text{sig}} = c^* c
$$

则 MMSE 信干比：

$$
\begin{aligned}
		\gamma &= \frac{P_{\text{sig}}}{P_{\text{noise+inter}}} = \frac{c^* c}{c - c^* c} \\
		&= \frac{c^*}{1-c^*} \\
		&= \frac{1}{1 - c^*} - 1
	\end{aligned}
\tag{11}
$$

下面对 $$c*$$ 做进一步的推导：

$$
\begin{aligned}
		c^* &=  (q_l^\text H\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l)^* \\
		&= q_l^\text H(\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l
	\end{aligned}
\tag{9}
$$

根据矩阵求逆展开公式

$$
\left( \mathbf{A} + \mathbf{U} \mathbf{C} \mathbf{V} \right)^{-1}
	=
	\mathbf{A}^{-1}
	- \mathbf{A}^{-1} \mathbf{U}
	\left( \mathbf{C}^{-1} + \mathbf{V} \mathbf{A}^{-1} \mathbf{U} \right)^{-1}
	\mathbf{V} \mathbf{A}^{-1}
$$

令 $$(\Lambda  +   \sigma^2 I ) ^{-1}$$ 中 $$A = \Lambda, U = \sigma^2 I, C = I,V=I$$，则：

$$
\begin{aligned}
		(\Lambda  +   \sigma^2 I ) ^{-1} &= \Lambda^{-1} - \Lambda^{-1}  \sigma^2 I ( I + I \Lambda^{-1} \sigma^2 I )^{-1} I \Lambda^{-1}  \\
		&= \Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1}
	\end{aligned}
\tag{8}
$$

将公式 (8) 代入公式 (9):

$$
\begin{aligned}
		c^* &=  q_l^\text H \left (\Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1} \right ) \Lambda q_l \\
		&= q_l^\text H \left ( I - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \right ) q_l \\
		&=  1 - \sigma^2 q_l^\text H \Lambda^{-1}( I + \sigma^2 \Lambda^{-1})^{-1}  q_l  \\
		&= 1 - \sigma^2 q_l^\text H ( \Lambda + \sigma^2 I )^{-1}  q_l \\
		&= 1 - \sigma^2[Q^\text H ( \Lambda + \sigma^2 I )^{-1}  Q]_{ll}  \\
		&= 1 - \sigma^2[ (Q^\text H \Lambda Q + \sigma^2 Q^\text H I Q)^{-1}  ]_{ll} \\
		&= 1 - \sigma^2[ (Q^\text H \Lambda Q + \sigma^2 I )^{-1}  ]_{ll} \\
		&= 1 - \sigma^2[ (H^\text H H + \sigma^2 I )^{-1}  ]_{ll}
	\end{aligned}
\tag{10}
$$

将公式 (10) 代入 (11) 有：

$$
\begin{aligned}
		\gamma &= \frac{1}{1 - \left ( 1 - \sigma^2[ (H^\text H  H + \sigma^2 I )^{-1}  ]_{ll} \right )} - 1 \\[8pt]
		&=\frac{1}{\sigma^2[ (H^\text H  H + \sigma^2 I )^{-1}  ]_{ll}} - 1
	\end{aligned}
\tag{13}
$$

如果原始信号能量是 1，因为高斯白噪声的能量是 $$\sigma^2$$，所以，原始 SNR 为：

$$
\text{SNR} = 1/\sigma^2
$$

则 MMSE 均衡后的最终信干比(SINR) 是：

$$
\begin{aligned}
		\gamma 
		&=\frac{\text{SNR}}{[ (H^\text H  H + \frac{1}{\text{SNR}} I )^{-1}  ]_{ll}} - 1
	\end{aligned}
\tag{14}
$$

### 信干比 SINR 的第二种表达式

$$
\begin{aligned}
		\gamma = \frac{h_l^{\text H} R^{-1} h_l}{1-h_l^{\text H} R^{-1} h_l}
	\end{aligned}
\tag{12}
$$

其中 $$R=H H^{\text H} + \sigma^2 I$$.

因为 

$$
h_l^{\text H} R^{-1} h_l = [H^{\text H} R^{-1} H]_{ll}
$$

所以，这里对 $$H^{\text H} R^{-1} H$$ 先做一下推导。

先对 $$H$$ 做奇异值分解(SVD 分解):

$$
H = U \Lambda^{1/2} Q
$$

那么：

$$
\begin{aligned}
		H^{\text H} R^{-1} H &= Q^{\text H} \Lambda^{1/2} U^{\text H}\left (U \Lambda^{1/2} Q  (U \Lambda^{1/2} Q)^{\text H}  + \sigma^2 I \right )^{-1} U \Lambda^{1/2} Q \\
		&= Q^{\text H} \Lambda^{1/2} U\left (U \Lambda^{1/2} Q^{\text H}  Q \Lambda^{1/2} U^{\text H}  + \sigma^2 I \right )^{-1} U \Lambda^{1/2} Q \\
		&= Q^{\text H} \Lambda^{1/2} (  \Lambda + \sigma^2 I )^{-1} \Lambda^{1/2} Q
	\end{aligned}
$$

同理，把公式 (8) 代入上式有：

$$
\begin{aligned}
		H^{\text H} R^{-1} H &= Q^{\text H} \Lambda^{1/2} (  \Lambda + \sigma^2 I )^{-1} \Lambda^{1/2} Q \\
		&= Q^{\text H} \Lambda^{1/2} \left (\Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1} \right )  \Lambda^{1/2} Q \\
		&= Q^{\text H}  \left ( I - \sigma^2 \Lambda^{-1/2}( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1/2} \right )  Q \\
		&= Q^{\text H}  \left ( I - \sigma^2 ( \Lambda + \sigma^2 I)^{-1} \right )  Q \\
		&= I -  \sigma^2 Q^{\text H} ( \Lambda + \sigma^2 I)^{-1} Q \\
		&= I -  \sigma^2 ( Q^{\text H} \Lambda Q  + \sigma^2 I)^{-1} \\
		&= I - \sigma^2 ( H^{\text H} H  + \sigma^2 I)^{-1}
	\end{aligned}
$$

所以：

$$
h_l^{\text H} R^{-1} h_l = [H^{\text H} R^{-1} H]_{ll} = 1 - \sigma^2 \left [  (  H^{\text H} H + \sigma^2 I)^{-1} \right ]_{ll}
$$

将上式代入(12) 后稍加整理，也得到如公式 (13) 相同的结果，也就是 (14).

因此， MMSE 均衡后的最终信干比可以有两种表达方法，即：

$$
\gamma 
	=\frac{\text{SNR}}{[ (H^\text H  H + \frac{1}{\text{SNR}} I )^{-1}  ]_{ll}} - 1
$$

和

$$
\gamma = \frac{h_l^{\text H} R^{-1} h_l}{1-h_l^{\text H} R^{-1} h_l}
$$

## MMSE 信干比的第二种表达式--物理意义角度推导 

在之前讲 MMSE 均衡算法是讲到

$$
H^{\text H}\left ( H H^{\text H} + \sigma^2 I  \right )^{-1}  = \left ( H^{\text H} H + \sigma^2 I  \right )^{-1}H^{\text H}
$$

我们用左边的均衡矩阵表达式来推导 MMSE 均衡下的 SINR，这次是从物理含义的角度出发来推导。

为了推导书写的方便，我们令 $$R =  H H^{\text H} + \sigma^2 I$$。

### 信号功率

均衡后的信号为：

$$
\hat X = H^{\text H} R^{-1} Y = H^{\text H} R^{-1}(HX+N)
	= H^{\text H} R^{-1} H X + H^{\text H} R^{-1} N
$$

则第 $$l$$ 的均衡的信号中，$$x_l$$ 的系数为$$[H^{\text H} R^{-1} H]_{ll}$$， 即 $$h^{\text H}_l R^{-1} h_l$$。则原始有用信号均衡后的功率为:

$$
P_{\text{sig}} = h^{\text H}_l R^{-1} h_l \left (h^{\text H}_l R^{-1} h_l \right )^{H} = h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
\tag{17}
$$

### 干扰的功率

类似的，我们求出信号+干扰的总功率，再减去信号的功率就是干扰的功率。
信号+干扰的总功率为下面这个协方差矩阵的对角线上的元素：

$$
\begin{aligned}
		&E\left [\left ( H^{\text H} R^{-1} H X \right )\left ( H^{\text H} R^{-1} H X \right )^{\text H} \right ] \\
		&= 
		E\left [ H^{\text H} R^{-1} H X X^H H^{\text H} R^{-1}   H \right ]  \\[8pt]
		& = H^{\text H} R^{-1} H E[X X^H] H^{\text H} R^{-1} H \\[8pt]
		& =  H^{\text H} R^{-1} H H^{\text H} R^{-1} H
	\end{aligned}
$$

则第 $$l$$ 行 第$$l$$ 列就是信号加干扰的能量：

$$
P_{\text{sig+inter}} = h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l
$$

则干扰的功率为：

$$
\begin{aligned}
		P_{\text{inter}} &= P_{\text{sig+inter}} -  P_{\text{sig}} 
		&= h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
	\end{aligned}
\tag{15}
$$

### 噪声功率

噪声功率是噪声向量协方差矩阵对角线上的元素。噪声向量的协方差矩阵为：

$$
\begin{aligned}
		&E\left [ H^{\text H} R^{-1} N \left ( H^{\text H} R^{-1} N \right )^{\text H}   \right ] \\
		&=E\left [ H^{\text H} R^{-1} N  N^{\text H} R^{-1} H \right ]   \\
		&= H^{\text H} R^{-1} E\left [N  N^{\text H} \right ] R^{-1} H  \\
		&= \sigma^2 H^{\text H} R^{-1} R^{-1} H
	\end{aligned}
$$

对上面矩阵取第$$l$$行第$$l$$列即为噪声能量：

$$
P_{\text{noise}} =  \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l
\tag{16}
$$

### 干扰+噪声的能量
则把公式(15)干扰能量和公式(16)噪声的能量加在一起:

$$
\begin{aligned}
		P_{\text{inter+noise}} &= P_{\text{inter}} + P_{\text{noise}} \\[8pt]
		&= h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l  - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l + \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l  \\[8pt]
		& = h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l +  \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l  - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
		&=h^{\text H}_l R^{-1} \left ( H H^{\text H} + \sigma^2 I \right ) R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
		&= h^{\text H}_l R^{-1} R R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
		&= h^{\text H}_l R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
	\end{aligned}
$$

令 $$c = h^{\text H}_l R^{-1} h_l$$, 则上式为：

$$
P_{\text{inter+noise}} = c - c^2
$$

### 信干比
公式 (17) 中的信号功率为 $$P_{\text{sig}} = c^2$$, 则信干比为

$$
\begin{aligned}
		\text{SINR}_{\text{MMSE}} &= \frac{P_{\text{sig}}}{ P_{\text{inter+noise}}}  \\[8pt]
		&= \frac{c^2}{c - c^2}  \\[8pt]
		&= \frac{c}{1 - c} \\[8pt]
		&= \frac{h^{\text H}_l R^{-1} h_l}{1-h^{\text H}_l R^{-1} h_l}
	\end{aligned}
$$

证毕。