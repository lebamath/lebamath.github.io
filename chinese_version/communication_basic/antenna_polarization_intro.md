---
layout: default
title: "天线极化"
back_url: /index.html?lang=zh
---

# 天线极化
录制的视频在 [B 站](https://www.bilibili.com/video/BV1oc411b71H)


## 极化
(这一节翻译自书籍：Advanced Antenna Systems for 5G Network Deployments- Bridging the Gap Between Theory and Practice-Henrik Asplund AAS for 5G
 的 3.2.3 节)

如上所述，辐射的电磁波是横波，意味着电场和磁场矢量与传播方向正交。因此，在三维空间中有两个局部维度可用于场的振荡，也就是说，这种波被认为有两个自由度。我们将波的极化定义为在沿传播方向观察时,电场在这两个维度中振荡的方向。在本书中讨论极化时，我们将使用球坐标而不是笛卡尔坐标，因为前者更适合描述通过天线传输的波的场和极化。

数学上，沿着 $$\hat{\boldsymbol{r}}$$ 方向传播的球面波的电场可以表示为：

$$
\boldsymbol{E}_0 = E_{\theta} exp(j\phi_{\theta}) \hat{\boldsymbol{\theta}} +  E_{\varphi} exp(j\phi_{\varphi}) \hat{\boldsymbol{\varphi}} \tag{3.11}
$$

其中，若把横波电场沿基本单位向量 $$\hat{\boldsymbol{\theta}}$$ 和 $$\hat{\boldsymbol{\varphi}}$$ 进行分解，$$E_{\theta}$$ 和 $$E_{\varphi}$$ 分别是这两个基向量对应的振幅，$$\phi_{\theta}$$ 和 $$\phi_{\varphi}$$ 是对应的相位。请注意，这些向量可以表示为在 3D 空间中的坐标,即一个 $$3\times 1$$ 向量（有关如何在不同坐标系统之间进行转换，请参见附录1.5）。

相量之间的关系决定了波的极化，即电场矢量的时变方向和相对幅度。在本书中，波的极化是沿着波传播方向观察的。一些特定的极化类型如下（也请参见图3.5）：


![图1：图3.5 沿波传播方向观察, 电场矢量的轨迹表现出不同极化类型：线性极化（左）、圆极化（中）和椭圆极化（右）。](/figure/通信基础/天线极化/fig_3.5.png)

*图1：图3.5 沿波传播方向观察, 电场矢量的轨迹表现出不同极化类型：线性极化（左）、圆极化（中）和椭圆极化（右）。* 


**线性极化**: 发生在电场矢量沿着一个恒定方向振荡的情况下，即，当 ，$$\phi_{\theta} = \phi_{\varphi}$$ ，或者当 $$E_{\theta}$$ 或 $$E_{\varphi}$$ 中的任意一个为零时。$$E_{\theta}$$ 和 $$E_{\varphi}$$ 之间的振幅关系决定了电场矢量的倾斜角度。在本书中，垂直极化（vertical polarization，VP）[注2]被定义为当电场沿着 $$\hat{\boldsymbol{\theta}}$$ 轴振荡时的极化，即当  $$E_{\varphi} = 0$$。类似地，水平极化（horizontal polarization，HP）被定义为当电场沿着  $$\hat{\boldsymbol{\varphi}}$$ 轴振荡时的极化（$$E_{\theta} = 0$$）。其他线性极化包括 +45° 极化（$$E_{\theta}=E_{\varphi}$$）和 -45° 极化（$$E_{\theta}=-E_{\varphi}$$）。

注2：在文献中，水平和垂直极化有时是相对于笛卡尔坐标系中的地平线定义的。然而，这种定义在描述天线特性时会导致问题，因为天线方向性和极化将变得不可分离，可参见方程（3.30）。

**圆极化**:发生在电场矢量在横向平面内以恒定的振幅旋转时，即当 $$E_{\theta}=E_{\varphi}$$ 且$$\phi_{\theta} = \phi_{\varphi} \pm 90°$$ 时。旋转的方向可以是顺时针或逆时针，分别导致右旋圆极化（right-hand circular polarization，RHCP）或左旋圆极化（lefthand circular polarization，LHCP）。

相量之间的其他关系导致\textit{椭圆极化}，即电场矢量的振幅和旋转角都发生变化。

波的极化通常可以用一个$$2\times 1$$ 的复值元素向量 $$\hat{\boldsymbol{\psi}}$$ 来表示，它被用来重新表达电场矢量，即：

$$
\begin{aligned}
	\boldsymbol{E}_0 &= E_{\theta} exp(j \phi_{\theta }) \hat{\boldsymbol{\theta}} +
	E_{\varphi} exp(j \phi_{\varphi }) \hat{\boldsymbol{\varphi}}  \\
	&= \sqrt{E_{\theta}^2+E_{\varphi}^2}
	\begin{bmatrix}
		\hat{\boldsymbol{\theta}} & \hat{\boldsymbol{\varphi}}  
	\end{bmatrix} \hat{\underline{\boldsymbol{\psi}}}
	\overset{\bigtriangleup}{=} \sqrt{E_{\theta}^2+E_{\varphi}^2} \hat{\boldsymbol{\psi}}
\end{aligned}   \tag{3.12}
$$

其中，

$$
\hat{\boldsymbol{\psi}}   \overset{\bigtriangleup}{=} = 
\frac{1}{\sqrt{E_{\theta}^2+E_{\varphi}^2}} 
\begin{bmatrix}
	E_{\theta} exp(j \phi_{\theta })\\  
	E_{\varphi} exp(j \phi_{\varphi })
\end{bmatrix}
\tag{3.13}
$$

包含了极化矢量在基向量$$\hat{\boldsymbol{\theta}}$$ 和 $$\hat{\boldsymbol{\varphi}}$$上的坐标.
这里，
$$
\begin{bmatrix}
\hat{\boldsymbol{\theta}} & \hat{\boldsymbol{\varphi}}  
\end{bmatrix}
$$
是一个 $$3\times 2$$ 的实数矩阵，其中包含了基向量 $$\hat{\boldsymbol{\theta}}$$ 和 $$\hat{\boldsymbol{\varphi}}$$ 作为其元素。

一般而言，本章采用符号 $$\underline{x}$$ 来表示 $$x$$ 在不同基坐标系中的向量。基坐标系由上下文给定，通常是以发射机或接收机坐标系中的 量$$\hat{\boldsymbol{\theta}}$$ 和 $$\hat{\boldsymbol{\varphi}}$$ 为基。在以下内容中，将交替使用两种不同方式表示的同一个向量。与 $$x$$ 对应的向量 $$\underline{x}$$ 可以称为与之对应的 Jones 向量[注3]。

注3：本章后面引入的极化状态及极化散射矩阵 $$\psi$$ 的表示是由 R.C. Jones 引入的，用于确定极化的相关数学运算因此被称为 Jones 微积分。

利用这种符号表示法，可以定义如下的极化向量：

$$
\hat{\underline{\boldsymbol{\psi}}}_{VP} = \begin{bmatrix}
	1 \\  0
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{HP} = \begin{bmatrix}
	0 \\  1
\end{bmatrix} \tag{3.15}
$$

$$
\hat{\underline{\boldsymbol{\psi}}}_{LHCP} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  j
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{RHCP} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  -j
\end{bmatrix} \tag{3.16}
$$

$$
\hat{\underline{\boldsymbol{\psi}}}_{+45°} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  1
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{-45°} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  -1
\end{bmatrix} \tag{3.17}
$$

对于任何极化 $$\hat{\boldsymbol{\psi}}_1$$ ，总存在一个正交的极化 $$\hat{\boldsymbol{\psi}}_2$$, 使得 $$\hat{\boldsymbol{\psi}}_1 \cdot \hat{\boldsymbol{\psi}}_2 = \hat{\boldsymbol{\psi}}_1^* \hat{\boldsymbol{\psi}}_2^* = \hat{\underline{\boldsymbol{\psi}}}_1^* \hat{\underline{\boldsymbol{\psi}}}_2^* = 0$$  例如 $$\hat{\boldsymbol{\psi}}_{VP}$$ 正交于 $$\hat{\boldsymbol{\psi}}_{HP}$$,  $$\hat{\boldsymbol{\psi}}_{+45°}$$ 正交于 $$\hat{\boldsymbol{\psi}}_{-45°}$$, $$\hat{\boldsymbol{\psi}}_{LHCP}$$正交于$$\hat{\boldsymbol{\psi}}_{RHCP}$$。任何椭圆极化都会有一个正交的椭圆极化，但旋转方向相反，且这两个椭圆极化的相对倾斜角为直角。任意极化都可以描述为两个正交极化的线性组合，例如，用垂直和水平极化做线性组合，或 用 +45° 和 -45° 极化做线性组合。

$$
\hat{\underline{\boldsymbol{\psi}}} = a  \hat{\underline{\boldsymbol{\psi}}}_{VP} + 
b  \hat{\underline{\boldsymbol{\psi}}}_{HP}
= c  \hat{\underline{\boldsymbol{\psi}}}_{+45°} +
d  \hat{\underline{\boldsymbol{\psi}}}_{-45°}
\tag{3.18}
$$

正如将在第6.3节中描述的那样（参见图6.21），波的两个极化自由度可以用于通信目的，以实现更健壮的信号传输，甚至通过在不同的极化上传输不同的信息。


## 椭圆极化的推导
电场可以表示为

$$
a e^{j2\pi f_c+\theta} \hat{\boldsymbol{x}} + b e^{j2\pi f_c+\varphi} \hat{\boldsymbol{y}}
$$

则对应两个坐标轴下的分量，分别为：

$$
x = a \text{cos}(2\pi f_c t + \theta)  \\
y = b \text{cos}(2\pi f_c t + \varphi)
$$

则：

$$
x = a \text{cos}(2\pi f_c t) \text{cos}(\theta) - a \text{sin}(2\pi f_c t) \text{sin}(\theta) \\
y = b \text{cos}(2\pi f_c t) \text{cos}(\varphi) - b \text{sin}(2\pi f_c t) \text{sin}(\varphi) \\
\tag{1}
$$

令：

$$
a_1 = a \text{cos}(\theta),\quad  a_2 = - a\text{sin}(\theta) \\
b_1 = b \text{cos}(\varphi),\quad  b_2 = - b\text{sin}(\varphi) \\
$$

则 (1) 式子可以简写为：

$$
x = a_1 \text{cos}(2\pi f_c t) + a_2 \text{sin}(2\pi f_c t) \\
y = b_1 \text{cos}(2\pi f_c t) + b_2 \text{sin}(2\pi f_c t) 
\tag{2}
$$

(2) 中第一个方程，两边同时乘以 $$b_2$$, 第二个方程同时乘以 $$a_2$$:

$$
b_2 x = a_1 b_2 \text{cos}(2\pi f_c t) + a_2 b_2 \text{sin}(2\pi f_c t) \\
a_2 y = a_2 b_1 \text{cos}(2\pi f_c t) + a_2 b_2 \text{sin}(2\pi f_c t)
$$

则可以解出来：

$$
\text{cos}(2\pi f_c t) = \frac
{b_2 x - a_2 y}
{a_1 b_2 - a_2 b_1}  \tag{3}
$$

用同样的方法，(2) 中第一个方程，两边同时乘以 $$b_1$$, 第二个方程同时乘以 $$a_1$$:

$$
b_1 x = a_1 b_1 \text{cos}(2\pi f_c t) + a_2 b_1\text{sin}(2\pi f_c t) \\
a_1 y = a_1 b_1 \text{cos}(2\pi f_c t) + a_1 b_2 \text{sin}(2\pi f_c t)
$$

则可以解出来：

$$
\text{sin}(2\pi f_c t) = \frac
{- b_1 x + a_1 y}
{a_1 b_2 - a_2 b_1}  \tag{4}
$$

(3)和(4) 联立有：

$$
\left (\frac{b_2 x - a_2 y }{a_1 b_2 - a_2 b_1} \right )^2 +
\left (\frac{- b_1 x + a_1 y}{a_1 b_2 - a_2 b_1}\right )^2
=1  \tag{5}
$$

若令：

$$
t = b_2 x - a_2 y \\
h = - b_1 x + a_1 y
$$

则 (5) 变成：

$$
t^2 + h^2 = (a_1 b_2 - a_2 b_1)^2  \tag{6}
$$

这个在以 t, h 为坐标的坐标系中是一个圆，但是，从 x, y 到 t, h 的变换不是一个刚性变换，虽然是线性变换：

$$
\begin{bmatrix}  t \\ h   \end{bmatrix} =
\begin{bmatrix}  
	b_2   & - a_2\\
	-b_1  & a_1 
\end{bmatrix}
\begin{bmatrix}  x \\ y   \end{bmatrix}
$$

由于矩阵 

$$
A=\begin{bmatrix}  
	b_2   & - a_2\\
	-b_1  & a_1 
\end{bmatrix} =
\begin{bmatrix}  
	- b\text{sin}(\varphi)   & a\text{sin}(\theta)\\
	-b \text{cos}(\varphi)  &  a \text{cos}(\theta) 
\end{bmatrix}
$$

不是正交矩阵，因此，这是一个有不同拉伸并旋转的变换，这个矩阵可以用 SVD 分解 来考虑。

当  $$a=b \quad \text{且} \quad  \varphi = \theta \pm \frac{\pi}{2}$$  这个矩阵是正交矩阵（不是单位的），所以是一个刚性变换，不改变形状。

如果 a ,b 任何一个都不为零，且 $$\varphi \neq \theta$$ , 并且也不满足  “ $$a=b \quad \text{且} \quad  \varphi = \theta \pm \frac{\pi}{2}$$”， 则上面的矩阵就不是正交矩阵：

$$
A A^T = \begin{bmatrix}  
	- b\text{sin}(\varphi)   & a\text{sin}(\theta)\\
	-b \text{cos}(\varphi)  &  a \text{cos}(\theta) 
\end{bmatrix}  \begin{bmatrix}  
	- b\text{sin}(\varphi)   & -b \text{cos}(\varphi)\\
	a\text{sin}(\theta)    &  a \text{cos}(\theta) 
\end{bmatrix}
=
\begin{bmatrix}
	b^2 \text{sin}^2(\varphi)+a^2 \text{sin}^2(\theta) & & &
	b^2 \text{sin}(\varphi) \text{cos}(\varphi) + a^2 \text{sin}(\theta)\text{cos}(\theta) \\
	b^2 \text{sin}(\varphi) \text{cos}(\varphi) +a^2 \text{sin}(\theta)\text{cos}(\theta) & & &
	b^2 \text{cos}^2(\varphi) + a^2 \text{cos}^2(\theta)
\end{bmatrix}
$$

如果 A 是正交矩阵，则 上式中最右侧矩阵需要满足：

$$
b^2 \text{sin}^2(\varphi)+a^2 \text{sin}^2(\theta) = b^2 \text{cos}^2(\varphi) + a^2 \text{cos}^2(\theta)  \\
b^2 \text{sin}(\varphi) \text{cos}(\varphi) + a^2 \text{sin}(\theta)\text{cos}(\theta) = 0
$$

则：

$$
-\frac{b^2}{a^2} = \frac{\text{cos}^2(\theta) - \text{sin}^2(\theta)}{\text{cos}^2(\varphi) - \text{sin}^2(\varphi)}
=\frac{\text{cos}(2\theta)}{\text{cos}(2\varphi)}   \tag{7}
$$

以及：

$$
-\frac{b^2}{a^2} = \frac{\text{sin}(\theta)\text{cos}(\theta)}{\text{sin}(\varphi)\text{cos}(\varphi)}
=\frac{\text{sin}(2\theta)}{\text{sin}(2\varphi)}  \tag{8}
$$

则结合 (7) (8）有：

$$
\frac{\text{cos}(2\theta)}{\text{cos}(2\varphi)} = \frac{\text{sin}(2\theta)}{\text{sin}(2\varphi)}
$$

进一步推导有：

$$
\frac{\text{sin}(2\varphi)}{\text{cos}(2\varphi)} = \frac{\text{sin}(2\theta)}{\text{cos}(2\theta)}
$$

即：

$$
tan(2\varphi) = tan(2\theta)
$$

则：

$$
2\theta = 2\varphi \pm k\pi
$$

即：

$$
\theta = \varphi \pm  \frac{\pi}{2}k， \quad k=0,1,2,\cdots   \tag{9}
$$

把 (9) 代入 (8) 有：

$$
b = a
$$

与前面的条件相矛盾，因此，不是正交矩阵。