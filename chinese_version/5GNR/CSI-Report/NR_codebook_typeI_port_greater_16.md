---
layout: default
title: "type I 天线端口大于 16 的情况"
back_url: /index.html?lang=zh
---
##  type I 天线端口大于 16 的情况
录制的视频在[B站](https://www.bilibili.com/cheese/play/ep556265)

 5G 协议中， Type I 的 codebook，当 天线端口数量大于等于 16 且 layers 数量是 3 或者 4 时，使用了一种特殊一点的 codebook.
我们知道， Type I 类型的 codebook 的思想，是让 UE 在众多的 beam 中选择一个 beam（而不是像 Type II 中是由多个，可能是 4 个 beam 来线性组合出来新的 beam),对于上面说的情况（天线端口数量大于等于 16 且 layers 数量是 3 或者 4）, 5G 协议使用了稍微不同一点的 codebook table.

对这种 codebook table，不是特别容易直观理解，通过搜索 3GPP 组织的提案文档，我们尝试去理解一下这种 codebook table.

Table 5.2.2.2.1-7: Codebook for 3-layer CSI reporting using antenna ports 3000 to 2999+PCSI-RS

!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-3Layers-.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-3Layers-.png") 



Table 5.2.2.2.1-8: Codebook for 4-layer CSI reporting using antenna ports 3000 to 2999+PCSI-RS

!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-4Layers-.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-4Layers-.png") 


我们先不考虑另外一个极化方向，那么，上面的表格，可以简化成如下：

!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-3Layers-Remove-one-polarization.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-3Layers-Remove-one-polarization.png") 


!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-4Layers-Remove-one-polarization.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4-3GPP-PMI-Table-4Layers-Remove-one-polarization.png") 


我们假定 N2 = 1，即只考虑一个线阵（而不是面阵），这样方便推导和理解。

则：

$$
\tilde{v}_{l,m} = \begin{bmatrix}
	1\\
	e^{j\frac{4\pi l}{O_1N_1}}\\
	\cdots  \\
	e^{j\frac{4\pi l(N_1/2-1)}{O_1N_1}}
\end{bmatrix}
$$

需要特别注意的是，上面这个向量是一个 $$N_1/2$$ 维的列向量，而不是 $$N_1$$ 维的列向量。
构成一个 beam 的向量如下：

$$
\begin{bmatrix}
	\tilde{v}_{l,m}\\
	e^{j\frac{p\pi}{4}}\tilde{v}_{l,m}
\end{bmatrix}
$$

这样构成了一个 $$N_1$$ 维的列向量，则这个是 $$N_1$$ 个线阵天线构成的角域空间中的一个 beam 向量。

通过这样的方法构成一个 beam 向量有什么特殊和好处吗？为什么不能直接用类似下面的 beam 向量：

$$
v_{l,m}= \begin{bmatrix}
	1\\
	e^{j\frac{2\pi l}{O_1N_1}}\\
	\cdots  \\
	e^{j\frac{2\pi l(N_1-1)}{O_1N_1}}
\end{bmatrix}
$$

上面这个向量直接就是 $$N_1$$ 维的。

后来找到了两个文章，提到了这样做的好处，但是，也没有特别深入地去讲解，这篇文章尝试做一些更深入的补充讨论。
书籍 [1] 的 9.3.5.4 Details of Type I single-panel codebook 中提到：

For rank 3 and 4 with 16 ports or more, the antenna ports of the first dimension are split into
two equal parts, hence having N1/2 ports. The reason is that the number of ports of this dimension is getting large and the beamwidth in this dimension may be too narrow for some propagation channels. Therefore a mechanism to widen the beam is introduced as follows: A DFT beam is selected as to be transmitted from only half (N1/2) of the antenna ports. The same selected DFT beam is used for both parts but the beam direction of the second part can be shifted relative to the beam direction of the first part.

[翻译] 当秩为 3 或者 4，且具有 16 个或更多端口时，第一维的天线端口被分为两个相等的部分，因此分别具有 N1/2 个端口。 原因是端口数量越来越大，波束宽度对于某些传播信道来说可能太窄。 因此，引入了如下加宽波束的机制：选择（N1/2）的 DFT波束且从（N1/2）天线端口发射。 两个部分使用相同的 DFT 波束，但第二部分的光束方向可以相对于第一部分的光束方向做 shift。 

在 3GPP 的提案 [2] 中的第 6 节 6. Rank 3 或者 4 codebook：

By applying $$b_(k_1,k_2 )$$ to each antenna group, a broader DFT beam is created. The inter-group co-phasing then creates a narrower beam within the envelope of the DFT beam, different cophasing will slightly alter the beam direction. 

通过将 $$b_(k_1,k_2 )$$ 应用于每个天线组，可以创建更宽的 DFT 波束。 然后，通过组间相位旋转 在 DFT 波束的包络内创建较窄的光束，不同的组间相位旋转将稍微改变光束方向。


从上面两个文献的描述中看到，似乎是通过这样的方式增加了波束的宽度，创建了更宽的波束。但是，这种操作确实不太好理解，也不太好从线性空间的角度去理解。

这篇文章尝试把各种情况的波束都绘制出来，放在一起比较一下，形成一个稍微直观一点的理解。

以 N = 8 O1 = 4  为例子，NR 标准中的 beam  向量为：

$$
\tilde{v}_{l,m} = \begin{bmatrix}
	e^{j\frac{4\pi l}{4 \times 8}\times 0}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 1}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 2}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 3}
\end{bmatrix}
$$

构成最终的 beam 向量：

$$
\begin{bmatrix}
	\tilde{v}_{l,m}\\
	e^{j\frac{p\pi}{4}}\tilde{v}_{l,m}
\end{bmatrix}
=
\begin{bmatrix}
	e^{j\frac{4\pi l}{4 \times 8}\times 0}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 1}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 2}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 3} \\
	e^{j\frac{p\pi}{4}} e^{j\frac{4\pi l}{4 \times 8}\times 0}\\
	e^{j\frac{p\pi}{4}} e^{j\frac{4\pi l}{4 \times 8}\times 1}\\
	e^{j\frac{p\pi}{4}} e^{j\frac{4\pi l}{4 \times 8}\times 2}\\
	e^{j\frac{p\pi}{4}} e^{j\frac{4\pi l}{4 \times 8}\times 3}
\end{bmatrix}  \tag{1}
$$

协议中，p 可以取值 0,1,2,3.

我们需要来考察一下如下这个 beam 的情况：

$$
\begin{bmatrix}
	e^{j\frac{4\pi l}{4 \times 8}\times 0}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 1}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 2}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 3}
\end{bmatrix} \tag{2}
$$

公式 (2) 这个 beam 将构成一个最大的外围轮廓，把公式 (1） 所表示的所有的 beam 都包在里面（当然，前提是都是相同的 $$l$$ 值).

下图是用 $$\frac{p \pi}{4}$$ 做相位调整的， p 分别取值 0，1，2，3. 可以看到最外面胖的 beam 是公式 (2) 的 beam，将内部的 4 个 beam 包在里面。

（代码见附录一）


!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-4.png") 

\clearpage 

其中打点的那个 beam, 是用 8 个天线，直接生成的 beam，公示如 (3):

$$
\begin{bmatrix}
	e^{j\frac{4\pi l}{4 \times 8}\times 0}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 1}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 2}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 3} \\
	e^{j\frac{4\pi l}{4 \times 8}\times 4}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 5}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 6}\\
	e^{j\frac{4\pi l}{4 \times 8}\times 7}
\end{bmatrix} \tag{3}
$$

下图是用更精细的相位调整 $$\frac{p \pi}{P}$$,p 分别取值 0，1，2，..., P-1.
从图中可以看到，这些依然被公式 (2) 的 beam 包在里面。

(代码见附录二)

!["PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-any-P.png"](/figure/5GNR/CSI-REPORT/"PMI-TypeI-port-is-greater-16-layer-3-4-cophasing-pi-over-any-P.png") 

\clearpage

其中打点的那个 beam, 是用 8 个天线，直接生成的 beam，使用的是公式 (3).



不过，虽然从画图的角度看了一下主要区别，但是，还不是特别理解，这样做的好处在哪里？是因为分叉的波束显得更胖，当 UE 移动一点的时候，不会造成波束覆盖不到这个 UE?

一种解释是，当 UE 绕着基站移动了一点距离后， beam 依然能覆盖这个 UE，而不需要 UE 再回报给基站新的 PMI，减少了由于 beam 太窄导致的需要频繁上报 PMI.



---
[1]《Advanced Antenna Systems for 5G Network Deployments: Bridging the Gap Between Theory and Practice》 by Asplund, H. and Astely, D. and von Butovitsch, P. and Chapman, T. and Frenne, M. and Ghasemzadeh, F. and Hagstrom, M. and Hogan, B. and Jongren, G. and Karlsson, J. and others
ISBN：9780128200469

[2] R1-1708687  \url{https://www.3gpp.org/ftp/tsg_ran/WG1_RL1/TSGR1_89/Docs/R1-1708687.zip}

附录一   代码在 github

附录二   代码在 github