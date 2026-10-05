---
layout: default
title: "CIS-RS 的生成"
back_url: /index.html?lang=zh
---
# CIS-RS 的生成

这篇短文讨论一下 5G NR 中 CSI-RS 是如何在时频域网格中放置的，即重点讨论如下这个公式，在38.211 7.4.1.5.3节中：

$$
\begin{aligned}
		a_{k,l}^{(p,\mu)} &= \beta_{\text{CSIRS}} w_{\text{f}}(k') \cdot w_{\text{t}}(l') \cdot r_{l,n_{\text{s,f}}}(m') \\
		m' &= \lfloor n\alpha \rfloor + k' + \left\lfloor \frac{\bar{k}\rho}{N_{\text{sc}}^{\text{RB}}} \right\rfloor \\
		k &= nN_{\text{sc}}^{\text{RB}} + \bar{k} + k' \\
		l &= \bar{l} + l' \\
		\alpha &= 
		\begin{cases} 
			\rho & \text{for } X = 1 \\ 
			2\rho & \text{for } X > 1 
		\end{cases} \\
		n &= 0, 1, \dots
	\end{aligned}
$$

CSI-RS 是用于做下行信道估计的，估计的信道是从基站的天线端口 ( Antenna Port)到 UE 的天线端口之间的信道。为了简单起见，我们假设 UE是单天线端口的，基站是线阵天线，即天线端口排列成一条线。如下图所示：


在一个RB（ResouceBlock）资源网格中，会在指定的符号和 RE 上放置 CSI-RS。为了测量所有的天线端口，要为不同的天线端口都安排 CSI-RS，至少可以有两种策略，一种是一个 RE 就直接对应一个天线端口的参考信号，另外一种是通过码分复用，几个 RE 同时给几个天线端口放置参考信号，例如 4 个 RE 给 4 个天线端口放置参考信号，3GPP 协议中通过码分复用的方式。



我们先以 4 端口为例子来讲解。在 38.211 Table 7.4.1.5.3-1: CSI-RS locations within a slot 表格中，端口数 X = 4 的，有两种可选择的配置，至于具体选择哪个，是其它模块或者上层来配置和决定的。

例如选择第四行，即 Row=4.

我们先分析频域上放在什么位置，其中 $$\bar k$$ 和 $$k'$$ 决定频域位置如何摆放，在 Row 4 中，
$$\bar k = [k_0, k_0+2]$$ ，其中 $$k_0$$ 是上层决定的在一个 RB 中的频域偏移，即从第几个 RE 开始的。可以看到 $$\bar k$$ 有两个取值。而 $$k'$$ 有两个取值 $$[0,1]$$，所以，$$\bar k + k'$$ 就有四种取值：

$$
\begin{aligned}
		&k_0 + 0(\bar k = k_0, \ k'=0) \\
		&k_0 + 1(\bar k = k_0, \ k'=1) \\
		&k_0 + 2(\bar k = k_0+2, \ k'=0) \\
		&k_0 + 3(\bar k = k_0+2, \ k'=1) \\
	\end{aligned}
$$

然后，在各个 RB 中的放置位置，就由  $$k = nN_{\text{sc}}^{\text{RB}} + \bar{k} + k'$$，即再加上 RB 的偏移 $$nN_{\text{sc}}^{\text{RB}}$$.

需要注意的是当 density 是 0.5 时，在一个 OFDM symbol 内不是所有 RB 都放置参考信号，而是间隔着，要么是在偶数 RB 上放置，要么是在奇数 RB 上放置。

类似的，在时域的位置上，如果已经决定这个  slot 要放 CSI-RS 信号了（这个是由另外一个简单公式决定的，不是本文讨论的重点），那么要决定在哪些 OFDM symbol 上放。 这个是用 $$l = \bar{l} + l'$$ 决定的。 这个例子Row=4中，$$\bar l = l_0， \  l'=0$$，因此，$$l=\bar l + l' = l_0$$，因此，只在 $$l_0$$ 这个 OFDM symbol 上放置。

现在已经确定了在哪些位置上放置 CSI-RS 信号，接下来要看这些信号是怎么计算得到的。
分为两步，第一步是得到原始的参考信号 $$r_{l,n_{\text{s,f}}}(m')$$ ，然后做码分复用和功率控制 $$\beta_{\text{CSIRS}} w_{\text{f}}(k') \cdot w_{\text{t}}(l')$$.





$$r_{l,n_{\text{s,f}}}(m')$$ 这个序列的生成与 Symbol 符号 $$l$$ 以及 radio frame 中的 slot 号有关，不同的 Symbol 符号 $$l$$ 以及 radio frame 中的 slot 号, 则对应不同的参考信号序列。

$$m'$$ 是一个连续的整数索引，其生成公式是 

$$
m' = \lfloor n\alpha \rfloor + k' + \left\lfloor \frac{\bar{k}\rho}{N_{\text{sc}}^{\text{RB}}} \right\rfloor
$$

这个的个数等于  RB的数量  乘以 每个 RB 中用几个参考信号。需要注意的是，在一个 RB 中，参考信号可能被用多次。

### Row 4 例子

对于 **Row 4**（4 端口配置，$$X=4$$），计算 $$m'$$ 如下

**提取核心参数** \\
**端口数**：$$X = 4 > 1$$，根据协议规定，$$\alpha = 2\rho$$ 

**复用类型**：fd-CDM2，因此频域内部索引 $$k' \in \{0, 1\}$$ 

**频域起始点**：$$\bar{k}$$ 位于一个 PRB 内部（即 $$\bar{k} \le 11$$），且 $$N_{\text{sc}}^{\text{RB}} = 12$$

**代入公式化简** 

原始通用公式为：
$$
m' = \lfloor n\alpha \rfloor + k' + \left\lfloor \frac{\bar{k}\rho}{12} \right\rfloor
$$
由于 $$\bar{k} \le 11$$ 且 $$\rho = 1$$，其乘积除以 12 必定小于 1，所以向下取整项 $$\lfloor \frac{\bar{k}\rho}{12} \rfloor \equiv 0$$


**最终结论** 
将 $$\alpha = 2\rho$$ 和尾项为 0 代入，得到极其简洁的最终公式：
$$
m' = 2 n  + k'
$$

**具体取值示例** 

公式为 $$m' = 2n + k'$$（由于在所有 RB 上连续发送，实际有效 $$n = 0, 1, 2 \dots$$）

当 $$n = 0$$ 时，$$m'$$ 取值为 $$0, 1$$

当 $$n = 1$$ 时，$$m'$$ 取值为 $$2, 3$$

当 $$n = 2$$ 时，$$m'$$ 取值为 $$4, 5$$

序列索引每次消耗 2 个，完美连续递增


### Row 14 例子
对于 **Row 14**（24 端口配置，$$X=24$$），计算 $$m'$$ 的极简推导如下


**提取核心参数** 

**端口数**：$$X = 24 > 1$$，根据协议规定，$$\alpha = 2\rho$$ 

**复用类型**：\texttt{cdm4-FD2-TD2}，频域依然为 2 抽头，因此频域内部索引 $$k' \in \{0, 1\}$$ 

**频域起始点**：$$\bar{k}$$ 位于一个 PRB 内部（即 $$\bar{k} \le 11$$），且 $$N_{\text{sc}}^{\text{RB}} = 12$$


**代入公式化简** 
原始通用公式为：
$$
m' = \lfloor n\alpha \rfloor + k' + \left\lfloor \frac{\bar{k}\rho}{12} \right\rfloor
$$

由于 $$\bar{k} \le 11$$ 且 $$\rho \le 1$$（如 $$\rho=1$$ 或 $$0.5$$），其乘积除以 12 必定小于 1，所以向下取整项 $$\lfloor \frac{\bar{k}\rho}{12} \rfloor \equiv 0$$

**最终结论** 
将 $$\alpha = 2\rho$$ 和尾项为 0 代入，得到极其简洁的最终公式：
$$
m' = \lfloor 2\rho n \rfloor + k'
$$

**具体取值示例** 
当 $$\rho = 1$$ 时：公式为 $$m' = 2n + k'$$（由于在所有 RB 上连续发送，实际有效 $$n = 0, 1, 2 \dots$$）

当 $$n = 0$$ 时，$$m'$$ 取值为 $$0, 1$$

当 $$n = 1$$ 时，$$m'$$ 取值为 $$2, 3$$

当 $$n = 2$$ 时，$$m'$$ 取值为 $$4, 5$$

序列索引每次消耗 2 个，完美连续递增


当 $$\rho = 0.5$$ 时：公式为 $$m' = n + k'$$（假设 offset = 0，仅在偶数 RB 上发送，实际有效 $$n = 0, 2, 4 \dots$$）

当 $$n = 0$$ 时，$$m'$$ 取值为 $$0, 1$$

当 $$n = 2$$ 时，$$m'$$ 取值为 $$2, 3$$

当 $$n = 4$$ 时，$$m'$$ 取值为 $$4, 5$$

结合奇偶 RB 的物理过滤机制，虽然跳过了奇数 RB，但实际提取的序列索引依然是 $$0, 1, 2, 3, 4, 5$$，同样完美连续递增

![图1：Enter Caption](/figure/5GNR/CSI-RS-Alloc/resouceGridRow14p1.png)

*图1：Enter Caption*

![图1：Enter Caption](/figure/5GNR/CSI-RS-Alloc/resouceGridRow14p.5.png)

*图1：Enter Caption*

### 端口与 CDM 组的对应

是下面这段文字描述的：


The UE shall assume that a CSI-RS is transmitted using antenna ports $$p$$ numbered according to
$$
\begin{aligned}
p &= 3000 + s + jL; \\
	j &= 0, 1, \dots, N/L - 1 \\
	s &= 0, 1, \dots, L - 1;
\end{aligned}
$$
where $$s$$ is the sequence index provided by Tables 7.4.1.5.3-2 to 7.4.1.5.3-5, $$L \in \{1, 2, 4, 8\}$$ is the CDM group size, and $$N$$ is the number of CSI-RS ports. The CDM group index $$j$$ given in Table 7.4.1.5.3-1 corresponds to the time/frequency locations $$(\bar{k}, \bar{l})$$ for a given row of the table. The CDM groups are numbered in order of increasing frequency domain allocation first and then increasing time domain allocation.

UE（用户设备）应假设 CSI-RS 是使用按以下方式编号的天线端口 $$p$$ 来传输的：
$$
\begin{aligned}
p &= 3000 + s + jL; \\
	j &= 0, 1, \dots, N/L - 1 \\
	s &= 0, 1, \dots, L - 1;
\end{aligned}
$$
其中 $$s$$ 是由表 7.4.1.5.3-2 至 7.4.1.5.3-5 提供的序列索引，$$L \in \{1, 2, 4, 8\}$$ 是 CDM 组大小，且 $$N$$ 是 CSI-RS 端口的数量。表 7.4.1.5.3-1 中给出的 CDM 组索引 $$j$$ 对应于该表给定行的时频位置 $$(\bar{k}, \bar{l})$$。CDM 组的编号顺序为：首先按频域分配递增的顺序排序，然后再按时域分配递增的顺序排序。


所以，给定端口号 $$p$$， 则 $$(p-3000) \operatorname{mod} L = s$$， $$\left \lfloor \frac{(p-3000)}{L} \right \rfloor = j$$

$$j$$ 代表的是 CDM 组号， $$s$$ 代表组中的第几个天线端口，也是用于确定码分复用的正交码：where   is the sequence index provided by Tables 7.4.1.5.3-2 to 7.4.1.5.3-5

![图2：Enter Caption](/figure/5GNR/CSI-RS-Alloc/resouceGridRow18p1.png)

*图2：Enter Caption*

![图3：Enter Caption](/figure/5GNR/CSI-RS-Alloc/resouceGridRow18p.5.png)

*图3：Enter Caption*


![图1：Enter Caption](/figure/5GNR/CSI-RS-Alloc/csirs.png)

*图1：Enter Caption*

### 确定在 RB 中的什么位置开始

由如下公式得到：
$$
[b_3 \cdots b_0], \quad k_{i-1} = f(i) \quad \text{for row 1 of Table 7.4.1.5.3-1}
$$

$$
[b_{11} \cdots b_0], \quad k_{i-1} = f(i) \quad \text{for row 2 of Table 7.4.1.5.3-1}
$$

$$
[b_2 \cdots b_0], \quad k_{i-1} = 4f(i) \quad \text{for row 4 of Table 7.4.1.5.3-1}
$$

$$
[b_5 \cdots b_0], \quad k_{i-1} = 2f(i) \quad \text{for all other cases}
$$

其中 $$f(i)$$ 表示第$$i$$ 个设置为1的比特在比特流中的位置， $$i$$ 的取值范围是38.211 Table 7.4.1.5.3-1表格中 k 的下标取值范围，例如 Row=6，有$$k_0,k_1,k_2,k_3$$,则$$i$$ 的取值有 $$i=0,1,2,3$$.  那么 $$f(2)$$ 表示 $$[b_5 \cdots b_0]$$ 中从左到右第2个（从0开始计数）为1的比特在 $$[b_5 \cdots b_0]$$ 中的位置，例如  $$[b_5 \cdots b_0] = [1\textcolor{red}{1}1001]$$，则 $$f(2)=4$$.



### CSI-RS 在频域中的开始位置

协议中，在决定 CSI-RS 在一个 RB 的频域位置上，是按照如图4所示的公式，看起来不是很清晰，我们这个文章稍微做一下解释。

![图4：CSI-RS 在频域中的开始位置](/figure/5GNR/CSI-RS-Alloc/csirs-bxb0-calculation.png)

*图4：CSI-RS 在频域中的开始位置*

在一个 RB 内的频域上分派  RE，是按照一捆一捆来分的，可能是不连续挨着的几个 RE 为一捆（Row 1)，更多的情况是连续挨着的为一捆(Row 2 到 Row 18).

则上面4 中的 bx .. b0，每个比特就代表某一捆，如果这个比特是1，那么这个比特对应的那一捆就被分派被使用。

我们分四种情况来讨论。

**Row 1**  如图 5 所示，在 row 1 的情况，根据 38.211 中的表格 Table 7.4.1.5.3-1: CSI-RS locations within a slot， Row 1 情况下 RE 的分派是：

$$(k_0,l_0),(k_0+4,l_0),(k_0+8,l_0)$$

![图5：Row 1 的情况](/figure/5GNR/CSI-RS-Alloc/csirs-row1-b3b2b1b0.png)

*图5：Row 1 的情况*

所以，一旦 $$k_0$$  确定了，那么分派的资源就确定了，由于 $$k'$$ 取值只有 1，因此，分派的 RE 就是三个，在频域上分别在 $$k_0, k_0+4,k_0+8$$.

所以， 12/3=4，就有4捆，因此需要4个比特来表示哪一捆被使用了,$$b_3,b_2,b_1,b_0$$ 这 4 个比特就分别对应一捆，即如图5 中的四种颜色，就是4 捆。

例如 $$b_3b_2b_1b_0=0010$$, 则表示 $$b_1$$ 对应的那一捆被选中，就是 RE1, RE5,RE9  这三个 RE 被使用。


图4  中的第一种情况就是 ROW 1 的，其中 $$i$$  代表 从 $$b_0$$ 开始数，这是第几个 1. 例如 $$b_3b_2b_1b_0=0010$$,  则  $$b_1=1$$，这是第一个 1，因此，  $$i=1$$。而 $$f(i)$$ 代表这个 1 在 $$b_3b_2b_1b_0$$ 的下标，$$f(i)$$ 的取值就是 $$b_1$$ 的下标，因此， $$f(i) = 1$$。 $$i=1$$时，$$k_{i-1}$$ 就是 $$k_0$$，因此， $$k_0=1$$ ， 所以，三个被使用的  RE 是 RE1, RE5 和 RE 9.

因为只有 $$k_0$$ ， 因此，$$b_3b_2b_1b_0$$ 中只有有一个比特是 1.

**Row 2** 

在 row 1 的情况，根据 38.211 中的表格 Table 7.4.1.5.3-1: CSI-RS locations within a slot， Row 1 情况下 RE 的分派是：

$$(k_0,l_0)$$

$$k'$$  的取值只有 0. 因此，这种情况下一捆就是一个 RE，则一个 RB 内有 12 捆，那么就需要 12 个比特来对应 12 捆。

例如 $$b_{11}...b_0 = 0000\ 0100\ 0000$$， 则 $$b_6 = 1$$ ，其它 b 都是 0. 代表 RB 内第 6 个(第一个是代表 RE 0) RE 被使用。

因为只有 $$k_0$$ ， 因此，$$b_3b_2b_1b_0$$ 中只有一个比特是 1.


**Row 4**  如图 5 所示的第三种情况就是 row 4 的，根据表格 38.211 Table 7.4.1.5.3-1: CSI-RS locations within a slot ,  频域信息的相关信息为
$$
(k_0,l_0)\ (k_0+2,l_0), \ \ k'=\{0,1\}
$$
当 $$k_0$$ 取定后，分派的 RE  就是 $$k_0,k_0+1,k_0+2,k_0+3$$  这 4 个 RE.

![图6：Row 4 的情况](/figure/5GNR/CSI-RS-Alloc/csirs-row4-b2b1b0.png)

*图6：Row 4 的情况*

如图 5 所示，每种颜色代表一捆，因此， 需要三个比特来表示，$$b_2,b_1,b_0$$分别对应一捆，例如 $$b_2=1$$ 则，代表最上面那一捆， RE 是从 8 到 11 这 4 个 RE.

若 $$b_2b_1b_0=100$$
$$i$$  是表示这是第几个 1， 因为只有一个 是 1， 因此， $$i=1$$ ，要确定的是 $$k_{i-1}$$  即要确定的是 $$k_0$$， 而 $$f(i)=f(1)=2$$ ，对应是1的那个比特的 b 的下标，此处是 $$b_2=1$$，因此 $$f(1)=2$$.


**第四种情况** 如图 5 所示的第四种情况，适用于除 Row 1, Row 2 和 Row 4 之外的所有情况，这些情况都是需要 6 个比特来表示。我们以 Row 6 为例来讨论。

根据表格 38.211 Table 7.4.1.5.3-1: CSI-RS locations within a slot ,  频域信息的相关信息为
$$
(k_0,l_0)\ (k_1,l_0)\ (k_2,l_0)\ (k_3,l_0), \ \ k'=\{0,1\}
$$
这种情况，需要指定四个参数 $$k_0,k_1,k_2,k_3$$. 由于 $$k'$$  有两个取值，因此，每个 $$k$$ 确定后就对应连续的两个 RE，因此，将一个 RB 内的 12 个 RE 分成了6 组，
![图7：Row 6 的情况](/figure/5GNR/CSI-RS-Alloc/csirs-row6-b5b4b3b2b1b0.png)

*图7：Row 6 的情况*

四个 k， 需要在 6 个 $$b_5,...,b_0$$  中选择 4 个来一一对应。

例如：$$b_5b_4b_3b_2b_1b_0 = 110101$$

从 $$b_0$$  开始往左看，列入如下表格：

|  | b | i | $$f(i)$$ | $$2f(i)$$ | $$k_{i-1}$$ |
|---|---|---|---|---|---|
| $$b_0$$ | 1 | 1 | $$f(1)=0$$ | 0 | $$k_0=0$$ |
| $$b_1$$ | 0 |  |  |  |  |
| $$b_2$$ | 1 | 2 | $$f(2)=2$$ | 4 | $$k_1=4$$ |
| $$b_3$$ | 0 |  |  |  |  |
| $$b_4$$ | 1 | 3 | $$f(3)=4$$ | 8 | $$k_2=8$$ |
| $$b_5$$ | 1 | 4 | $$f(4)=5$$ | 10 | $$k_3=10$$ |


选择后的 RE 如图8 所示：

![图8：Row 6 选择后的情况](/figure/5GNR/CSI-RS-Alloc/csirs-row6-b5b4b3b2b1b0-selected.png)

*图8：Row 6 选择后的情况*


其它 Row 也是类似，都是分成六捆，每捆是连续的两个 RE，不同之处在于选择几捆来用，选择几捆，对应与表格 38.211 Table 7.4.1.5.3-1: CSI-RS locations within a slot 中相应行里有几个不同的 $$k_i$$.

选择一捆的： Row 3, Row 5

选择两捆的:    Row 7, Row 8

选择三捆的:    Row 10, Row 13, Row 14, Row 15

选择四捆的:    Row 6, Row 11, Row 12, Row 16, Row 17, Row 18

选择六捆的:    Row 9


例如 Row 10, 若 $$b_5b_4b_3b_2b_1b_0 = 100110$$

|         | b    | i    | $$f(i)$$   | $$2f(i)$$ | $$k_{i-1}$$ |
| ------- | ---- | ---- | ---------- | --------- | ----------- |
| $$b_0$$ | 0    |      |            |           |             |
| $$b_1$$ | 1    | 1    | $$f(1)=1$$ | 2         | $$k_0=2$$   |
| $$b_2$$ | 1    | 2    | $$f(2)=2$$ | 4         | $$k_1=4$$   |
| $$b_3$$ | 0    |      |            |           |             |
| $$b_4$$ | 0    |      |            |           |             |
| $$b_5$$ | 1    | 3    | $$f(3)=5$$ | 10        | $$k_2=10$$  |

选择后的 RE 如图9 所示：
![图9：Row 10 选择后的情况](/figure/5GNR/CSI-RS-Alloc/csirs-row10-b5b4b3b2b1b0-selected.png)

*图9：Row 10 选择后的情况*


### CDM 的解复用
$$
\begin{cases}
		y(0) = r(0)w(0,0)h_0 + r(0)w(0,1)h_1 + r(0)w(0,2)h_2 + r(0)w(0,3)h_3 \\
		y(1) = r(1)w(1,0)h_0 + r(1)w(1,1)h_1 + r(1)w(1,2)h_2 + r(1)w(1,3)h_3 \\
		y(2) = r(2)w(2,0)h_0 + r(2)w(2,1)h_1 + r(2)w(2,2)h_2 + r(2)w(2,3)h_3 \\
		y(3) = r(3)w(3,0)h_0 + r(3)w(3,1)h_1 + r(3)w(3,2)h_2 + r(3)w(3,3)h_3
	\end{cases}
$$

$$
y' = W \cdot \vec{h}
$$

$$
W^T \cdot y' = W^T \cdot W \cdot \vec{h} = \vec{h}
$$
其中 w(m,s) 的 s 表示组内第 s 个 天线端口，m 表示组内的RE的位置，是由 $$k'$$ 和 $$l'$$ 决定的。$$y(i)$$ 表示第 $$i$$ 个 CSI-RS RE 上被 UE 接收到的信号。

可以看到，通过可逆正交矩阵 $$W$$，可以计算出每个天线端口对应的信道系数 $$h_i$$.

以 cdm4-FD2-TD2 为例：
![图10：cdm4-FD2-TD2](/figure/5GNR/CSI-RS-Alloc/W_cdm4_FD2_TD2.png)

*图10：cdm4-FD2-TD2*

$$s=0$$  时，取图10中表格的第0行，
$$
W(0,0) = w_{\text{f},0}(0) \cdot w_{\text{t},0}(0) = 1 \times 1 = 1  \\
	W(1,0) = w_{\text{f},0}(1) \cdot w_{\text{t},0}(0) = 1 \times 1 = 1  \\
	W(2,0) = w_{\text{f},0}(0) \cdot w_{\text{t},0}(1) = 1 \times 1 = 1  \\
	W(3,0) = w_{\text{f},0}(1) \cdot w_{\text{t},0}(1) = 1 \times 1 = 1
$$

$$s=3$$  时，取图10中表格的第3行(从0开始计)，

$$
\begin{aligned}
		W(0,3) &= w_{\text{f},3}(0) \cdot w_{\text{t},3}(0) = 1 \times 1 &=& 1  \\
		W(1,3) &= w_{\text{f},3}(1) \cdot w_{\text{t},3}(0) = (-1) \times 1 &=& -1  \\
		W(2,3) &= w_{\text{f},3}(0) \cdot w_{\text{t},3}(1) = 1 \times (-1) &=& -1  \\
		W(3,3) &= w_{\text{f},3}(1) \cdot w_{\text{t},3}(1) = (-1) \times (-1) &=& 1  
	\end{aligned}
$$
## CDM码分复用的解耦

我们以cdm4-FD2-TD2为例子来解释。那么原始的参考信号(在没有做 CDM 码分复用操作之前的)有 4 个，虽然在时频域资源网格中是一个 2x2 的，但是，为了数学推导的方便，我们将之记为一个列向量：
$$
\mathbf{r} = \begin{bmatrix}
		r_0 \\
		r_1 \\
		r_2 \\
		r_3
	\end{bmatrix}
$$
对应四个端口有四个码分复用的编码向量，我们记为：
$$
\mathbf{U} = \begin{bmatrix}
		u_0 \\
		u_1 \\
		u_2 \\
		u_3
	\end{bmatrix}
	\ \ 
	\mathbf{V} = \begin{bmatrix}
		v_0 \\
		v_1 \\
		v_2 \\
		v_3
	\end{bmatrix}
	\ \ 
	\mathbf{W} = \begin{bmatrix}
		w_0 \\
		w_1 \\
		w_2 \\
		w_3
	\end{bmatrix}
	\ \ 
	\mathbf{F} = \begin{bmatrix}
		f_0 \\
		f_1 \\
		f_2 \\
		f_3
	\end{bmatrix}
$$
上面这四个向量是用表格 \ref{fig:csi_rs_cdm_table_cdm4FD2TD2} 中的对应行做 Kronecker 积产生的。
例如  $$\mathbf{F}$$ 是用表格中的第 3 行产生的：
$$
\mathbf U =
	\begin{bmatrix}
		w_f(0) \\
		w_f(1)
	\end{bmatrix}
	\otimes 
	\begin{bmatrix}
		w_t(0) \\
		w_t(1)
	\end{bmatrix}
	=
	\begin{bmatrix}
		1 \\
		-1
	\end{bmatrix}
	\otimes 
	\begin{bmatrix}
		1 \\
		-1
	\end{bmatrix}
	=
	\begin{bmatrix}
		1 \\
		-1 \\
		-1 \\
		1
	\end{bmatrix}
$$

容易证明，这四个向量是彼此正交的。

则每个端口发出去的参考信号为(其中 $$\cdot$$ 乘法，表示矩阵对应元素相乘)：
$$
\mathbf U \cdot \mathbf r
$$

$$
\mathbf V \cdot \mathbf r
$$

$$
\mathbf W \cdot \mathbf r
$$

$$
\mathbf F \cdot \mathbf r
$$
为了表达式的简洁，忽略接收端的高斯白噪声，四个天线端口对应信道的系数为 $$h_i, \  i=0,1,2,3$$, 则接收到的数据为：
$$
\mathbf Y =  h_0(\mathbf U \cdot \mathbf r)   + h_1 (\mathbf V \cdot \mathbf r)  + h_2 (\mathbf W \cdot \mathbf r)  + h_3 (\mathbf F \cdot \mathbf r )
$$
可以推导为：
$$
\mathbf Y =  (h_0\mathbf U) \cdot \mathbf r   + (h_1 \mathbf V )\cdot \mathbf r  + (h_2 \mathbf W) \cdot \mathbf r  + (h_3 \mathbf F) \cdot \mathbf r
$$
两边同时点除 $$\mathbf r$$:
$$
\mathbf Y'  = \mathbf Y ./ \mathbf r =  h_0\mathbf U   + h_1 \mathbf V   + h_2 \mathbf W  + h_3 \mathbf F
\tag{1}
$$
其中  $$./$$ 表示两个向量对应元素相除。

可以看到上式是 4 个正交向量的线性组合，组合的系数是信道系数，则再两段分别左乘码分复用的编码向量，则可以得到对应的系数，例如左乘 $$\mathbf V^{\text T}$$：
$$
\begin{aligned}
		\mathbf V^{\text T}\mathbf Y' &=  \mathbf V^{\text T}( h_0\mathbf U   + h_1 \mathbf V   + h_2 \mathbf W  + h_3 \mathbf F) \\
		&=  h_0\mathbf V^{\text T}\mathbf U   + h_1 \mathbf V^{\text T}\mathbf V   + h_2 \mathbf V^{\text T}\mathbf W  + h_3 \mathbf V^{\text T}\mathbf F \\
		& = h_0 * 0 + h_1 * |\mathbf V|^2 + h_2 * 0 + h_3*0 \\
		& = h_1 * |\mathbf V|^2
	\end{aligned}
$$


则可以解得 $$h_1$$:


$$
h_1 =   \mathbf V^{\text T}\mathbf Y' /|\mathbf V|^2
$$


从方程组 (1), 这个方程组有 4 个方程，有 4 个未知数$$h_0,h_1,h_2,h_3$$:


$$
\begin{aligned}
		y'_0 = h_0 u_0 + h_1 v_0 + h_2 w_0 + h_3 f_0 \\
		y'_1 = h_0 u_1 + h_1 v_1 + h_2 w_1 + h_3 f_1 \\
		y'_2 = h_0 u_2 + h_1 v_2 + h_2 w_2 + h_3 f_2 \\
		y'_3 = h_0 u_3 + h_1 v_3 + h_2 w_3 + h_3 f_3
	\end{aligned}
$$

其实只要保证这四个向量线性无关，就可以把四个未知系数接出来，但是用正交的好处是，避免复杂的求逆，用矩阵的转置就是矩阵的逆，大大减少运算量。