---
layout: default
title: " 5G NR codebook typeII-回传向量"
back_url: /index.html?lang=zh
---
## 5G NR codebook typeII-回传向量的选择
录制的视频在 [B站](https://www.bilibili.com/cheese/play/ep556116)

5G NR 协议中，天线是平面摆放的，即水平方向和垂直方向都有，则 beam 角域空间的坐标轴，是用一个矩阵来表示的，

例如水平方向是第 $$l$$ 列，则对应的坐标轴是：

$$
b_1(l) = 
\begin{bmatrix}
	1 \\ 
	e^{j\frac{2\pi l}{O_1N_1}} \\ 
	\cdots \\ 
	e^{j\frac{2\pi l(N_1-1)}{O_1N_1}}
\end{bmatrix}
$$

垂直方向第 $$m$$ 行

$$
b_2(m) = [1\quad e^{j\frac{2\pi m}{O_2N_2}} \quad \cdots \quad e^{j\frac{2\pi m(N_2-1)}{O_2N_2}}]
$$

则对应的坐标轴（应理解为基中的向量）：

$$
\begin{aligned}
&b_1(l)\otimes b_2(m) =  \\
&\begin{bmatrix}
	1 & e^{j\frac{2\pi m}{O_2N_2}} & e^{j\frac{2\pi m}{O_2N_2}*2}&\cdots & e^{j\frac{2\pi m}{O_2N_2}(N_2-1)} \\
	e^{j\frac{2\pi l}{O_1N_1}}*1 & e^{j\frac{2\pi l}{O_1N_1}}*e^{j\frac{2\pi m}{O_2N_2}} & e^{j\frac{2\pi l}{O_1N_1}}*e^{j\frac{2\pi m}{O_2N_2}*2}&\cdots & e^{j\frac{2\pi l}{O_1N_1}}*e^{j\frac{2\pi m}{O_2N_2}(N_2-1)} \\
	e^{j\frac{2\pi l}{O_1N_1}*2}*1 & e^{j\frac{2\pi l}{O_1N_1}*2}*e^{j\frac{2\pi m}{O_2N_2}} & e^{j\frac{2\pi l}{O_1N_1}*2}*e^{j\frac{2\pi m}{O_2N_2}*2}&\cdots & e^{j\frac{2\pi l}{O_1N_1}*2}*e^{j\frac{2\pi m}{O_2N_2}(N_2-1)} \\
	\cdots & \cdots & \cdots & \cdots & \cdots\\
	e^{j\frac{2\pi l}{O_1N_1}*(N_1-1)}*1 & e^{j\frac{2\pi l}{O_1N_1}*(N_1-1)}*e^{j\frac{2\pi m}{O_2N_2}} & e^{j\frac{2\pi l}{O_1N_1}*(N_1-1)}*e^{j\frac{2\pi m}{O_2N_2}*2}&\cdots & e^{j\frac{2\pi l}{O_1N_1}*(N_1-1)}*e^{j\frac{2\pi m}{O_2N_2}(N_2-1)} \\
\end{bmatrix} \\
&=w_{m,l}
\end{aligned}
$$

其中 O1 和 O2 是 oversampling 系数。

如下图所示的情况，则一共有 4x4=16 个基：

$$
\begin{aligned}
w_{0,0}, w_{0,4},w_{4,0},w_{4,4} \\
w_{0,1}, w_{0,5},w_{4,1},w_{4,5} \\
w_{0,2}, w_{0,6},w_{4,2},w_{4,6} \\
w_{0,3}, w_{0,7},w_{4,3},w_{4,7} \\
\\
w_{1,0}, w_{1,4},w_{5,0},w_{5,4} \\
w_{1,1}, w_{1,5},w_{5,1},w_{5,5} \\
w_{1,2}, w_{1,6},w_{5,2},w_{5,6} \\
w_{1,3}, w_{1,7},w_{5,3},w_{5,7} \\
\\
w_{2,0}, w_{2,4},w_{6,0},w_{6,4} \\
w_{2,1}, w_{2,5},w_{6,1},w_{6,5} \\
w_{2,2}, w_{2,6},w_{6,2},w_{6,6} \\
w_{2,3}, w_{2,7},w_{6,3},w_{6,7} \\
\\
w_{3,0}, w_{3,4},w_{7,0},w_{7,4} \\
w_{3,1}, w_{3,5},w_{7,1},w_{7,5} \\
w_{3,2}, w_{3,6},w_{7,2},w_{7,6} \\
w_{3,3}, w_{3,7},w_{7,3},w_{7,7}
\end{aligned}
$$

![CSI-Report-003-5G NR codebook typeII-回传向量的选择](/figure/5GNR/CSI-REPORT/CSI-Report-003-5G-NR-codebook-typeII-回传向量的选择.png) 


在 38.214 协议中，表示一个基中的矩阵，用如下的符号：

$$
v_{m_1^{(i)},m_2^{(i)}}
$$

其中：

$$
m_1^{(i)} = O_1 n_1^{(i)}+q_1  \\
m_2^{(i)} = O_1 n_2^{(i)}+q_2  
$$

分别表示水平方向和垂直方向的 index.

其中，$$q_1,q_2$$ 在 CSI Report 的 $$i_{1,1}$$ 参数中发过来的，即基站通过解析 $$i_{1,1}$$ 来得到 $$q_1,q_2$$ ，这两个参数表示在 oversampling 中的偏移量。

$$n_1^{(i)},n_2^{(i)}$$ 是在 $$i_{1,2}$$ 中发送来的，其中上标 $$i$$，表示传送的是第几个 beam 到基站，例如回传两个 beam，则 $$i$$ 取值 0 和 1.



例如 $$q_1 = 1, q_2 = 3 , n_1^{0} = 0, n_2^{(0)}=1, n_1^{0} = 1, n_2^{(0)}=0$$

则两个 beam 的下标为：

$$
\begin{aligned}
m_1^{(0)} = 1, m_2^{(0)} = 7 \\
m_1^{(1)} = 5, m_2^{(1)} = 3
\end{aligned}
$$

即图中的 “第一行第七列”  和 “第五行第三列”。
## 5G NR codebook typeII-回传向量的系数
回传的向量(Beam 方向) 做线性组合，对于宽带（全频带）有个幅度系数，由 $$i_{1,4,l}$$ 确定；对于每个子带(subband) 也可能有个幅度系数，由 $$i_{2,2,l}$$ 决定，然后，对于每个子带，还有个相位的系数，由 $$i_{2,2,l}$$ 决定。

![CSI-Report-004-5G NR codebook typeII-回传向量的系数](/figure/5GNR/CSI-REPORT/"CSI-Report-004-5G-NR-codebook-typeII-回传向量的系数.png") 

上面表格中的 $$p_{l,i}^{(1)}$$ 和  $$p_{l,i+L}^{(1)}$$ 是宽带幅度系数，是由 $$i_{1,4,l}$$ 确定的，其中 $$L$$ 表示一共几个向量（波束）.

$$
i_{1,4,l} = [k_{l,0}^{(1)},k_{l,1}^{(1)},\cdots,k_{l,2L-1}^{(1)}]
$$

前 L 个，得到  $$p_{l,i}^{(1)}$$， 后 L 个得到 $$p_{l,i+L}^{(1)}$$



上面表格中的 $$p_{l,i}^{(2)}$$ 和  $$p_{l,i+L}^{(2)}$$ 是子带幅度系数，是由 $$i_{2,2,l}$$ 确定的，其中 $$L$$ 表示一共几个向量（波束）.

$$
i_{2,2,l} = [k_{l,0}^{(2)},k_{l,1}^{(2)},\cdots,k_{l,2L-1}^{(2)}]
$$

前 L 个，得到  $$p_{l,i}^{(2)}$$， 后 L 个得到 $$p_{l,i+L}^{(2)}$$



上表中的 $$\varphi_{l,i}$$  和 $$\varphi_{l,i+L}$$ 是子带相位系数，由 $$i_{2,1,l}$$ 确定：

$$
i_{2,1,l} = [c_{l,0},c_{l,1},\cdots,c_{l,2L-1}]
$$

前 L 个 确定 $$\varphi_{l,i}$$  ，后 L 个确定 $$\varphi_{l,i+L}$$ 



上面所有的 $$l$$，都表示第几个 layer，因此，若一共有两个  layer，则分别有 $$i_{1,4,1}, i_{1,4,2}$$ ， 其他的类似。