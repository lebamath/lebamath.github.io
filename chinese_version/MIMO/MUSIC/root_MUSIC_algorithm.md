---
layout: default
title: "root MUSIC（Multiple Signal Classification）算法"
back_url: /chinese_version.html
---

#  root MUSIC（Multiple Signal Classification）算法
[录制的视频在 B 站课程](https://www.bilibili.com/cheese/play/ep1486051)

从前面的文章，我们已经介绍了  MUSIC 算法，其核心就是遍历足够多的流形向量 Manifold Vector，看公式 (1) 中的 MUSIC 谱，找其中比较大的值。

$$
P_{\text{MUSIC}}(k) = \frac{1}{\mathbf{a}^\text{H}(k) \boldsymbol U_\text{n} \boldsymbol U_\text{n}^\text{H} \mathbf{a}(k)}
\tag{1}
$$

现在，我们再进一步推导一个算法，简化上面的遍历的过程（降低运算量）。

公式 (1) 里的分母，其实是求 $$\boldsymbol U_n^H \mathbf{a}(k)$$ 的模长的平方。 $$\boldsymbol U_n^H \mathbf{a}(k)$$ 是个列向量，所以其模长的平方就是：
$$
\left [\boldsymbol U_\text{n}^\text{H} \mathbf{a}(k) \right ]^\text{H} \left [ \boldsymbol U_\text{n}^\text{H} \mathbf{a}(k)\right ] = \mathbf{a}^\text{H}(k) \boldsymbol U_\text{n} \boldsymbol U_\text{n}^\text{H} \mathbf{a}(k)
\tag{6}
$$

假设天线数为 N，则 $$\mathbf{a}(k)$$ 是一个 N 行的列向量。假设有 M 个 beam，那么噪声子空间就是 N-M 维的，所以，噪声子空间的特征向量矩阵 $$\boldsymbol U_n$$ 就是一个 $$N \times (N-M)$$ 的矩阵。

为了表述方便，我们令：

$$
\boldsymbol{R}_\text{n} = \boldsymbol U_\text{n} \boldsymbol U_\text{n}^\text{H}
\tag{3}
$$

则 $$\boldsymbol{R}_\text{n}$$ 是一个 $$N\times N$$ 的方阵。容易证明： $$\boldsymbol{R}_\text{n} = \boldsymbol{R}^\text{H}_\text{n}$$.

进一步令：
$$
\boldsymbol{R}_\text{n} = 
	\begin{bmatrix} 
		r_{00} & r_{01} & r_{02} & \cdots & r_{0\text{N-1}} \\ 
		r_{10}^* & r_{11} & r_{12} & \cdots & r_{1\text{N-1}} \\ 
		r_{20}^* & r_{21}^* & r_{22} & \cdots & r_{2\text{N-1}} \\ 
		\vdots & \vdots & \vdots & \ddots & \vdots \\ 
		r_{\text{N-1},0}^* & r_{\text{N-1},1}^* & r_{\text{N-1},2}^* & \cdots & r_{\text{N-1},\text{N-1}} \\ 
	\end{bmatrix}
\tag{2}
$$

因为我们要寻找的流形向量 $$\mathbf{a}(k)$$ 是一个等比的列向量，我们可以将之写成：
$$
\mathbf{a}(k) =
	\begin{bmatrix} 
		1 \\
		z \\
		z^2 \\
		\vdots \\
		z^{N-1}
	\end{bmatrix}
\tag{4}
$$

其中 $$z$$  是模长为 1 的复数。

则:

$$
\mathbf{a}^\text{H}(k) =
	\begin{bmatrix} 
		1 & z^{-1} & z^{-2} & \dots & z^{-(N-1)}
	\end{bmatrix}
\tag{5}
$$

结合公式 (2) 和 (3)，同时，也将公式 (4) 和 (5) 代入公式 (6), 得到：

$$
\mathbf{a}^\text{H}(k) \boldsymbol U_\text{n} \boldsymbol U_\text{n}^\text{H} \mathbf{a}(k) = 
	\begin{bmatrix} 
		1 & z^{-1} & z^{-2} & \dots & z^{-(N-1)}
	\end{bmatrix}
	\begin{bmatrix} 
		r_{00} & r_{01} & r_{02} & \cdots & r_{0\text{N-1}} \\ 
		r_{10}^* & r_{11} & r_{12} & \cdots & r_{1\text{N-1}} \\ 
		r_{20}^* & r_{21}^* & r_{22} & \cdots & r_{2\text{N-1}} \\ 
		\vdots & \vdots & \vdots & \ddots & \vdots \\ 
		r_{\text{N-1},0}^* & r_{\text{N-1},1}^* & r_{\text{N-1},2}^* & \cdots & r_{\text{N-1},\text{N-1}} \\ 
	\end{bmatrix}
	\begin{bmatrix} 
		1 \\
		z \\
		z^2 \\
		\vdots \\
		z^{N-1}
	\end{bmatrix}
\tag{7}
$$

将公式 (7) 展开，得到
$$
\begin{aligned}
		\mathbf{a}^\text{H}(k) \boldsymbol U_\text{n} \boldsymbol U_\text{n}^\text{H} \mathbf{a}(k) 
		&= r_{0\text{N-1}} z^{(N-1)} + \\
		& (r_{0\text{N-2}}+r_{1\text{N-1}}) z^{(N-2)} + \\
		& (r_{0\text{N-3}}+r_{1\text{N-2}}+r_{2\text{N-1}}) z^{(N-3)} + \\
		& \cdots + \\
		& (r_{00}+r_{11}+\cdots+r_{\text{N-1},\text{N-1}})+ \\
		& \cdots + \\
		& (r_{0\text{N-3}}+r_{1\text{N-2}}+r_{2\text{N-1}}) z^{-(N-3)} + \\
		& (r_{0\text{N-2}}+r_{1\text{N-1}}) z^{-(N-2)} + \\
		& r_{0\text{N-1}} z^{-(N-1)}  \\
	\end{aligned}
\tag{8}
$$

因为找公式(1) 的最大值点就是找 公式(6) 的零点，因此可以令公式 (8) 等于 0，这样就形成了一个对多项式求根的问题：

$$
\begin{aligned}
		&r_{0\text{N-1}} z^{(N-1)} + \\
		& (r_{0\text{N-2}}+r_{1\text{N-1}}) z^{(N-2)} + \\
		& (r_{0\text{N-3}}+r_{1\text{N-2}}+r_{2\text{N-1}}) z^{(N-3)} + \\
		& \cdots + \\
		& (r_{00}+r_{11}+\cdots+r_{\text{N-1},\text{N-1}})+ \\
		& \cdots + \\
		& (r_{0\text{N-3}}+r_{1\text{N-2}}+r_{2\text{N-1}}) z^{-(N-3)} + \\
		& (r_{0\text{N-2}}+r_{1\text{N-1}}) z^{-(N-2)} + \\
		& r_{0\text{N-1}} z^{-(N-1)}  \\
		= 0
	\end{aligned}
$$

两边同时乘以 $$z^{-(N-1)}$$,得到：

$$
\begin{aligned}
		&r_{0\text{N-1}} z^{(2N-2)} + \\
		& (r_{0\text{N-2}}+r_{1\text{N-1}}) z^{(2N-3)} + \\
		& (r_{0\text{N-3}}+r_{1\text{N-2}}+r_{2\text{N-1}}) z^{(2N-4)} + \\
		& \cdots + \\
		& (r_{00}+r_{11}+\cdots+r_{\text{N-1},\text{N-1}})z^{(N-1)}+ \\
		& \cdots + \\
		& (r^*_{0\text{N-3}}+r^*_{1\text{N-2}}+r^*_{2\text{N-1}}) z^2 + \\
		& (r^*_{0\text{N-2}}+r^*_{1\text{N-1}}) z + \\
		& r^*_{0\text{N-1}}  \\
		&= 0
	\end{aligned}
\tag{9}
$$

对公式 (9) 求根，可以得到 $$2N-2$$ 个根，因为我们要求流形向量中每个元素的模长为 1，所以，在这些根中，找 $$N-M$$ 个模长最接近 1 的根，就是对应的流形向量中的 $$z$$. 如果把根画在复平面上，则是最接近单位圆的 $$N-M$$ 个根。

![root MUSIC 根分布](/figure//mimo//music//rootmusic.png)

*root MUSIC 根分布*


注意，上图显示的是根的分布，根和 beam 的角度，还有一个换算公式，根的角度是 $$\phi$$
, 对应 beam 角度是  $$\theta$$ ,则根是 $$e^{j\phi}$$ ，且：
$$
\phi = \frac{d 2 \pi \sin{\theta}}{\lambda}
$$

其中 $$d$$ 是 天线间距， $$\lambda$$ 是波长。

例如在 $$d$$ 是半波长的情况下， $$\theta = 20^o * \pi /180^o$$ , 则 $$\phi = 61.6^o * \pi/180^o$$


代码在 github 上，https://github.com/taichiorange/leba\_math,目录为：leba\_math/MIMO/MIMO-beam-detection/root-MUSIC-algorithm.py

