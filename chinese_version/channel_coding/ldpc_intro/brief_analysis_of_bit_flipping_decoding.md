---
layout: default
title: "LDPC 比特翻转译码浅析"
back_url: /index.html?lang=zh
---
## LDPC 比特翻转译码浅析

录制的视频在 [B 站](https://www.bilibili.com/cheese/play/ep1069405)

LDPC码（low-density parity-check）实际上就是一种线性分组码，但是，由于LDPC码的 low-density  特性，可以用比较高效的算法来实现译码。这篇小文不是探讨 LDPC 码的方方面面，而是在读者已经了解线性分组码的基本概念，以及校验矩阵的基础上，试图对 LDPC码中一个简单的译码算法进行一个描述。在 Gallager 博士论文 [1] 中非常简要描述了一种称之为 Bit-Flipping 的 hard decoder 方法来译码。下面这段英文摘自 Gallager 博士论文 [1] :

		......, the decoder computes all the parity checks and then changes any digit that is contained in more than some fixed number of unsatisfied parity-check equations. Using these new values, the parity checks are recomputed, and the process is repeated until the parity checks are all satisfied.


下面举个例子来具体说明这个过程。



假设校验矩阵是：

$$
A = \begin{bmatrix}
	1& 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1\\
	1& 0 & 1 & 0 & 1 & 1 & 0 & 1 & 1 & 0\\
	0& 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1 & 1\\
	0& 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1\\
	1& 1 & 0 & 1 & 0 & 0 & 1 & 1 & 1 & 0
\end{bmatrix}
$$

把接收到的码字表示为一个向量：

$$
r=\begin{bmatrix}
	r_1 & r_2 & r_3 & r_4 & r_5 & r_6 & r_7  & r_8 & r_9 & r_{10}
\end{bmatrix}
$$

则根据收到的码字 $$r$$ ，可以计算出伴随式 syndrome（这个词在医学里面是症状的意思，在这里，可以理解为用来“诊断”接收到的码字是否有错误，以及诊断哪些校验方程是没有被满足的）：

$$
s = r A^T
$$

计算后的结果$$s$$，是一个含有5个元素的向量，记为：

$$
s = \begin{bmatrix}
	s_1& s_2 & s_3 & s_4 & s_5 
\end{bmatrix}
$$

为了易于理解，我们把上面这个矩阵形式的方程，展开成 5 个校验方程：

$$
\begin{aligned}
	s_1 &= r_1 + r_2 + r_3 + r_6 + r_7 + r_{10} \\
	s_2 &= r_1 + r_3 + r_5 + r_6 + r_8 + r_{9} \\
	s_3 &= r_3 + r_4 + r_5 + r_7 + r_9 + r_{10} \\
	s_4 &= r_2 + r_4 + r_5 + r_6 + r_8 + r_{10} \\
	s_5 &= r_1 + r_2 + r_4 + r_7 + r_8 + r_{9} 
\end{aligned}
$$

现在，我们举一个具体的例子，发送一个码字，接收到的码字有一个错误，用 bit-flipping 算法做一个译码。

发送的码字为：

$$
c=\begin{bmatrix}
	0&  0&  0&  1&  0&  1&  0&  1&  0&1
\end{bmatrix}
$$

接收到的码字有错误：

$$
r=\begin{bmatrix}
	0&  0&  0&  1&  1&  1&  0&  1&  0&1
\end{bmatrix}
$$

可以看到 $$r_5$$ 是有错误的。下面，用 bit-flipping 算法，尝试做一个译码：

第一步，计算出伴随式：

$$
\begin{aligned}
	s_1 &= r_1 + r_2 + r_3 + r_6 + r_7 + r_{10} = 0+0+0+1+0+1=0 \\
	s_2 &= r_1 + r_3 + r_5 + r_6 + r_8 + r_{9}  = 0+0+1+1+1+0=1\\
	s_3 &= r_3 + r_4 + r_5 + r_7 + r_9 + r_{10} =0+1+1+0+0+1=1\\
	s_4 &= r_2 + r_4 + r_5 + r_6 + r_8 + r_{10} =0+1+1+1+1+1=1\\
	s_5 &= r_1 + r_2 + r_4 + r_7 + r_8 + r_{9} = 0+0+1+0+1+0=0
\end{aligned}
$$

注：上面是模 2 加法， $$1+1=0$$

第二步，看伴随式是否都为 0 ，若都为 0 ，则结束译码，此时的  $$r$$ 就是正确的码字；如果伴随式不为0，跳到第三步。

第三步，看 $$r_1$$ 在哪几个校验方程中出现，在出现$$r_1$$的校验方程中，有几个校验方程是不满足的，即$$s_i$$ 不等于0.

$$r_1$$ 出现在 $$s_1,s_2,s_5$$中，其中$$s_2=1$$，因此，出现$$r_1$$的校验方程中有 1 个方程不满足；

$$r_2$$ 出现在 $$s_1,s_4,s_5$$中，其中$$s_4=1$$，因此，出现$$r_2$$的校验方程中有 1 个方程不满足；

以此类推，可以列出如下这个表格：



| $$r_i$$ | $$r_i$$ 出现在哪几个校验方程中 | 不满足的校验方程 | 不满足的校验方程的个数 |
|---|---|---|:--|
| $$r_1$$ | $$s_1,s_2,s_5$$ | $$s_2$$ | 1 |
| $$r_2$$ | $$s_1,s_4,s_5$$ | $$s_4$$ | 1 |
| $$r_3$$ | $$s_1,s_2,s_3$$ | $$s_2,s_3$$ | 2 |
| $$r_4$$ | $$s_3,s_4,s_5$$ | $$s_3,s_4$$ | 2 |
| $$r_5$$ | $$s_2,s_3,s_4$$ | $$s_2,s_3,s_4$$ | 3 |
| $$r_6$$ | $$s_1,s_2,s_4$$ | $$s_2,s_4$$ | 2 |
| $$r_7$$ | $$s_1,s_3,s_5$$ | $$s_3$$ | 1 |
| $$r_8$$ | $$s_2,s_4,s_5$$ | $$s_2,s_4$$ | 2 |
| $$r_9$$ | $$s_2,s_3,s_5$$ | $$s_2,s_3$$ | 2 |
| $$r_{10}$$ | $$s_1,s_3,s_4$$ | $$s_3,s_4$$ | 2 |



在 "不满足的校验方程的个数" 中选择最大的，上表中最大的是 3 ，对应的是 $$r_5$$ 所在的那一列，因此，把 $$r_5$$从 1 翻转（Flipping）成 0，接收到的码字被翻转后，记为新的 $$r$$. 跳转到第一步继续执行。

此时的 $$r$$ 为：

$$
r=\begin{bmatrix}  0&  0&  0&  1&  0&  1&  0&  1&  0&1\end{bmatrix}
$$

再来计算伴随式 $$s$$，得出的结果为 

$$
s=\begin{bmatrix}  s_1&  s_2&  s_3&  s_4&  s_5\end{bmatrix}=\begin{bmatrix}  0&  0&  0&  0&  0\end{bmatrix}
$$

译码结束。

正确的码字为：

$$
r=\begin{bmatrix}  0&  0&  0&  1&  0&  1&  0&  1&  0&1\end{bmatrix}
$$

可见，与最初的发送码字相同：

$$
c=\begin{bmatrix}
	0&  0&  0&  1&  0&  1&  0&  1&  0&1
\end{bmatrix}
$$

通俗来理解，这种迭代算法，就是考虑收到的码字中的比特，如果参与的校验方程中，越多的方程不满足，则越说明这个比特出错的可能性比较大，优先对这个比特进行翻转来尝试。

[1]  R.G.Gallager, Low-Density Parity-Check Codes, IRE Trans.Info.Theory  IT-8:21-28. 1962. 