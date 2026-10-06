---
layout: default
title: "SRS(Sounding Reference Signal) hopping"
back_url: /index.html?lang=zh
---
# SRS(Sounding Reference Signal) hopping
录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1362794)
这个文章讲一下 5G 通信协议中 SRS 信号的跳频问题，对如何进行跳频进行讨论，尤其是要重点讨论协议中跳频所使用的数学公式。
这个文章假定读者已经知道 SRS 是什么，主要作用是啥，网上有非常多的文章进行了介绍的。

本文章是分析 R15(38.211-fa0)版本。

先解释一下为啥需要做 SRS 的 跳频 (Hopping):

根据这个文章：

https://www.linkedin.com/pulse/5g-ul-channels-deep-dive-reference-signal-mohamed-eladawi/

其中提到：

SRS Parameters Description: freqHopping

Transmitting SRS across a large BW requires a relatively high UE transmit power. UEs which experience a high path loss can be allocated a smaller SRS BW to increase the received power density.....

之所以要跳频，是因为如果让 UE 在一个 OFDM 符号内把所有  SRS 带宽里都放上（可能是间隔 RE 着放）SRS 参考信号，则每个 RE 上所分配的功率就不足够多以便来对抗很高的路径衰减。所以，进行跳频，在一个符号上，只在 SRS 带宽的一段中放置 SRS 参考信号，下一个 OFDM 符号上，再跳到 SRS带宽中的另外一段频率上继续发送 SRS，这样，经过多个 OFDM 符号，则发送的 SRS 信号就覆盖了整个 SRS 带宽.

根据协议，用下面这个公式决定 SRS 信号在一个 OFDM 符号上频域的起点

$$
k_0^{(p_i)} = \bar{k}_0^{(p_i)} + \sum_{b=0}^{B_{\text{SRS}}} K_{TC} M_{\text{sc},b}^{\text{SRS}} n_b
\tag{1}
$$

其中 $$\bar{k}_0^{(p_i)}$$ 是总的频率起点，与跳频没有关系，跳频体现在 $$\sum_{b=0}^{B_{\text{SRS}}} K_{\text{TC}} M_{\text{sc},b}^{\text{SRS}} n_b$$

这里的 $$M_{\text{sc},b}^{\text{SRS}}$$ 表示在当前这个 OFDM symbol 上，SRS 参考信号实际占用了多少个 RE， 而 $$K_{\text{TC}}$$ 表示几个 RE 里面放置一个 SRS 参考信号。所以，$$K_{\text{TC}} M_{\text{sc},b}^{\text{SRS}}$$ 表示参考信号延展覆盖了多少个 RE(其中不是所有 RE 都放置了 SRS 参考信号，可能是跳着放的）。

接下来先讲一下总体的跳频规律，然后，再来看公式 (1) 中 $$\sum_{b=0}^{B_{\text{SRS}}} K_{\text{TC}} M_{\text{sc},b}^{\text{SRS}} n_b$$ 如何控制这个跳频过程的。

整个 SRS 带宽会被划分成多个段，而且是有不同的划分（即对不同的划分方式划分后的段长度不同），这个由表 Table 6.4.1.4.3-1: SRS bandwidth configuration 确定，如图 1。 表格中 $$m_{\text{SRS},b}$$ 表示每一段的长度，以 RB 为单位，$$N_b$$ 

![图1：SRS bandwidth table](/figure/5GNR/srs/srsHopping/SRS_bandwidth_table.png)

*图1：SRS bandwidth table*


以 $$C_{\text{SRS}}=29$$ 为例，如图 2所示，其分段方式如图 3 所示：

![图2：SRS bandwidth table,$$C_\text{SRS}=29$$](/figure/5GNR/srs/srsHopping/SRS_bandwidth_table_Csrs29.png)

*图2：SRS bandwidth table,$$C_\text{SRS}=29$$*

![图3：SRS bandwidth table,$$C_\text{SRS}=29$$](/figure/5GNR/srs/srsHopping/Csrs29_segments_show.png)

*图3：SRS bandwidth table,$$C_\text{SRS}=29$$*



上层需要给出三个参数，$$C_{\text{SRS}}, b=B_{\text{SRS}},  b_{\text{hop}}$$, 其中，$$C_{\text{SRS}}$$ 用来决定在表Table 6.4.1.4.3-1中的是哪一行。而$$b=B_{\text{SRS}}$$ 和 $$b_{\text{hop}}$$ 决定表格中从哪一列($$b_{\text{hop}}$$) 到哪一列 ($$b=B_{\text{SRS}}$$)。

当 $$b_{\text{hop}} < B_{\text{SRS}}$$ 

$$b_{\text{hop}}$$ 列对应的段的大小，是 SRS 的 bandwidth，即所有的 hopping 在这个范围内发生。而 $$B_{SRS}$$ 决定了每次（即在一个 OFDM symbol内）占多大的范围，即当前这一跳中，发送  SRS  需要覆盖的带宽范围。

现在来看一下公式 (1) 中 $$\sum_{b=0}^{B_{\text{SRS}}} K_{\text{TC}} M_{\text{sc},b}^{\text{SRS}} n_b$$ 如何控制这个跳频过程的。


假定 $$b_{\text{hop}}=0, B_{\text{SRS}}=3$$，则公式 (1) 中的求和项可以展开为：

$$
\sum_{b=0}^{B_{\text{SRS}}} K_{\text{TC}} M_{\text{sc},b}^{\text{SRS}} n_b
	= K_{\text{TC}} M_{\text{sc},0}^{\text{SRS}} n_0 + K_{\text{TC}} M_{\text{sc},1}^{\text{SRS}} n_1 + K_{\text{TC}} M_{\text{sc},2}^{\text{SRS}} n_2 + K_{\text{TC}} M_{\text{sc},3}^{\text{SRS}} n_3
\tag{2}
$$

协议中公式(2) 是以 RE 为单位的，我们为了简化表示，我们以 RB 为单位，即把 $$K_{\text{TC}} M_{\text{sc},b}$$ 看成一个整体，在下面的数量表示中，我们把 $$K_{\text{TC}} M_{\text{sc},b}$$ 都除以 12，换算成 RB.

计算 $$n_b$$ 协议中规定用的公式为：

$$
n_b = 
	\begin{cases} 
		\left\lfloor 4 n_{\text{RRC}}/m_{\text{SRS},b} \right\rfloor \mod N_b & b \le b_{hop} \\ 
		\left(
		F_b(n_{\text{SRS}}) + \left\lfloor 4 n_{\text{RRC}}/m_{\text{SRS},b} \right\rfloor 
		\right) \mod N_b & \text{otherwise}
	\end{cases}
\tag{3}
$$

其中 $$n_{\text{RRC}}$$ 是上层配置的一个偏移量，我们这里把他假定为 0. 则公式 (3)
简化为：

$$
n_b = 
	\begin{cases} 
		0 & \quad \quad b \le b_{hop} \\ 
		\left( F_b(n_{\text{SRS}}) \right) \mod N_b & \quad \quad \text{otherwise}
	\end{cases}
\tag{5}
$$

其中 $$F_b(n_{\text{SRS}})$$ 为：

$$
F_b(n_{\text{SRS}}) = 
	\begin{cases} 
		(N_b/2)     \left\lfloor 
		\frac{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{b} N_{b'}}{\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
		\right\rfloor +
		\left\lfloor 
		\frac{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{b} N_{b'}}{2 \prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
		\right\rfloor
		& \text{if } N_b \text{ is even} \\[10pt]
		\left\lfloor N_b / 2 \right\rfloor \cdot 
		\left\lfloor 
		\frac{n_{\text{SRS}}}{\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
		\right\rfloor, & \text{if } N_b \text{ is odd}
	\end{cases}
\tag{4}
$$

另外，协议中规定公式(4) 中的 $$N_{b_\text{hop}}=1$$。

## $$N_b$$ 是偶数的例子

为了演示和讲解，我们人为造一个例子，即在表Table 6.4.1.4.3-1: SRS bandwidth configuration 中，假定有这样一行，如图 4, 对应的段划分，如图 5 所示。

![图4：为了方便说明，制造了不是标准中的一行](/figure/5GNR/srs/srsHopping/bandwidth_table_one_own_row.png)

*图4：为了方便说明，制造了不是标准中的一行*



![图5：为了方便说明，制造了不是标准中的一行](/figure/5GNR/srs/srsHopping/bandwidth_table_one_own_row_on_grid_show.png)

*图5：为了方便说明，制造了不是标准中的一行*

假定 $$b_{\text{hop}}=0, B_{\text{SRS}}=3$$

**b=0时**， 计算 $$K_\text{TC} M_{\text{sc},0}^\text{SRS} n_0$$ 中的 $$n_0$$:

此时 $$b \le b_\text{hop}$$，利用公式 (5) 中的第一种情况， 则 $$n_0 = 0$$

**b=1时**， 计算 $$K_\text{TC} M_{\text{sc},1}^\text{SRS} n_1$$ 中的 $$n_1$$:

此时 $$b > b_\text{hop}$$，利用公式 (5) 中的第二种情况， 进而需要看公式(4), 此时，$$N_b= N_1 = 6$$ ,是偶数(Even)，所以，使用公式(4) 中的第一种情况。

$$
\prod_{b' = b_{\text{hop}}}^{b} N_{b'} = \prod_{b' = 0}^{1} N_{b'} = 
	N_0 \times N_1 = N_\text{hop} \times N_1
	= 1\times 6 = 6
$$

这个连乘$$\prod_{b' = b_{\text{hop}}}^{b} N_{b'}$$ 表示的是到当前这个 $$b$$ 时，总共有多少次跳频，也就是会分成多少段。 而分母中的连乘 $$\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}$$ 表示的是到当前这个 $$b$$ 之前时，总共有多少次跳频(即会分成多少段)

$$
\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'} = \prod_{b' = 0}^{0} N_{b'} = N_0 = N_\text{hop}= 1
$$

则:

$$
F_1(n_\text{SRS}) = N_1/2 \left\lfloor \frac{n_\text{SRS} \mod 6}{1} \right\rfloor +
	\left\lfloor \frac{n_\text{SRS} \mod 6}{2\times 1} \right\rfloor
$$

计算出来的结果，如表格 \ref{tab:srs_hopping_b1_results} 所示。 可以看到，在这个层次，其跳频的规律是，在第一半里面选择一个段，然后，跳到第二半里面选择同样偏移(相对于一半的位置)位置的一个段，然后，再跳回到第一半里面选择下一个偏移位置，

因为这是第一层的跳频，比较容易理解。后面层的跳频规律都是类似的，但是要考虑前面层要先跳转一遍，才能在本层中有跳跃的动作。
为了描述方便，我们令：

$$
\left (
	(N_1/2)     
	\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{b} N_{b'}}
	{\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
	\right\rfloor
	
	\right ) \mod N_1 = J_0
\tag{6}
$$

以及：

$$
\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{b} N_{b'}}
	{2 \prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
	\right\rfloor = J_1
$$

需要注意的是，公式 (6) 中多做了一个 $$\mod N_1$$，这是因为后续 $$F_b(n_\text{SRS})$$ 会被 $$\mod N_1$$，所以，为了更清楚看到最终的影响，我们在里面也做一次 $$\mod N_1$$,不影响最终结果。

而这体现在 $$J_0$$ 重复多少次后才变化（增加或者回退到 0），当然，也要求 $$J_1$$ 重复多少次才增加或者回退到 0，因为这是决定在一半中的频移位置，因此， $$J_1$$ 重复的次数是 $$J_0$$ 的两倍，因为，在上一层的所有跳频遍历一遍后，本层是跳转一半，等到把两半都跳转完，才能让在一半内的偏移做跳转。

\begin{table}
	\centering
	| $$n_\text{SRS}$$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| $$n_\text{SRS} \mod 6$$ | 0 | 1 | 2 | 3 | 4 | 5 | 0 | 1 | 2 | 3 | 4 | 5 | 0 |
| $$N_b/2 \left\lfloor \frac{n_\text{SRS} \mod 6}{1} \right\rfloor$$ | 0 | 3 | 6 | 9 | 12 | 15 | 0 | 3 | 6 | 9 | 12 | 15 | 0 |
| $$\left (N_b/2 \left\lfloor \frac{n_\text{SRS} \mod 6}{1} \right\rfloor \right ) \mod 6$$ | 0 | 3 | 0 | 3 | 0 | 3 | 0 | 3 | 0 | 3 | 0 | 3 | 0 |
| $$\left\lfloor \frac{n_\text{SRS} \mod 6}{1\times 2} \right\rfloor$$ | 0 | 0 | 1 | 1 | 2 | 2 | 0 | 0 | 1 | 1 | 2 | 2 | 0 |
| $$F_b(n_\text{SRS})$$ | 0 | 3 | 1 | 4 | 2 | 5 | 0 | 3 | 1 | 4 | 2 | 5 | 0 |
| $$F_b(n_\text{SRS}) \mod 6$$ | 0 | 3 | 1 | 4 | 2 | 5 | 0 | 3 | 1 | 4 | 2 | 5 | 0 |
	\caption{b=1}
	\label{tab:srs_hopping_b1_results}
\end{table}


**b=2时**， 计算 $$K_\text{TC} M_{\text{sc},2}^\text{SRS} n_2$$ 中的 $$n_2$$:

此时 $$b > b_\text{hop}$$，利用公式 (5) 中的第二种情况， 进而需要看公式(4), 此时，$$N_b= N_2 = 10$$ ,是偶数(Even)，所以，使用公式(4) 中的第一种情况。

$$
\prod_{b' = b_{\text{hop}}}^{b} N_{b'} = \prod_{b' = 0}^{2} N_{b'} = 
	N_0 \times N_1 \times N_2 = N_\text{hop} \times N_1 \times N_2
	= 1\times 6 \times 10  = 60
$$

这个连乘$$\prod_{b' = b_{\text{hop}}}^{b} N_{b'}$$ 表示的是到当前这个 $$b$$ 时，总共有多少次跳频，也就是会分成多少段。 而分母中的连乘 $$\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}$$ 表示的是到当前这个 $$b$$ 之前时，总共有多少次跳频(即会分成多少段)

$$
\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'} = \prod_{b' = 0}^{1} N_{b'} = N_0 \times N_1 = N_\text{hop} \times N_1 = 6
$$

则:

$$
F_2(n_\text{SRS}) = N_2/2 \left\lfloor \frac{n_\text{SRS} \mod 60}{6} \right\rfloor +
	\left\lfloor \frac{n_\text{SRS} \mod 60}{2 \times 6} \right\rfloor
$$

计算出来的结果，如表格 \ref{tab:srs_hopping_b2_results} 所示。 可以看到，在这个层次，其跳频的规律是，在第一半里面选择一个段，然后，跳到第二半里面选择同样偏移(相对于一半的位置)位置的一个段，然后，再跳回到第一半里面选择下一个偏移位置，

因为这是第一层的跳频，比较容易理解。后面层的跳频规律都是类似的，但是要考虑前面层要先跳转一遍，才能在本层中有跳跃的动作。
为了描述方便，我们令：

$$
\left (
	(10/2)     
	\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{2} N_{b'}}
	{\prod_{b' = b_{\text{hop}}}^{2-1} N_{b'}}
	\right\rfloor
	
	\right ) \mod 10 = J_0
\tag{7}
$$

以及：

$$
\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{2} N_{b'}}
	{2 \prod_{b' = b_{\text{hop}}}^{2-1} N_{b'}}
	\right\rfloor = J_1
$$

需要注意的是，公式 (7) 中多做了一个 $$\mod N_2$$，这是因为后续 $$F_b(n_\text{SRS})$$ 会被 $$\mod N_2$$，所以，为了更清楚看到最终的影响，我们在里面也做一次 $$\mod N_2$$,不影响最终结果。

而这体现在 $$J_0$$ 重复多少次后才变化（增加或者回退到 0），当然，也要求 $$J_1$$ 重复多少次才增加或者回退到 0，因为这是决定在一半中的频移位置，因此， $$J_1$$ 重复的次数是 $$J_0$$ 的两倍，因为，在上一层的所有跳频遍历一遍后，本层是跳转一半，等到把两半都跳转完，才能让在一半内的偏移做跳转。

从表中可以看到，$$J_0$$ 在 6 次才增加，对应于截至上一层是段的个数。$$j_1$$ 在 12 次才增加。

\begin{table}
	\centering
	| $$n_\text{SRS}$$ | 0-5 | 6-11 | 12-17 | 18-23 | 24-29 | 30-35 | 36-41 | 42-47 | 48-53 | 54-59 | 60-65 |  |  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| $$n_\text{SRS} \mod 60$$ | 0-5 | 6-11 | 12-17 | 18-23 | 24-29 | 30-35 | 36-41 | 42-47 | 48-53 | 54-59 | 0-5 |  |  |
| $$\left\lfloor \frac{n_\text{SRS} \mod 60}{6} \right\rfloor$$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 0 |  |  |
| $$\frac{10}{2} \left\lfloor \frac{n_\text{SRS} \mod 60}{6} \right\rfloor$$ | 0 | 5 | 10 | 15 | 20 | 25 | 30 | 35 | 40 | 45 | 0 |  |  |
| $$\left (\frac{10}{2} \left\lfloor \frac{n_\text{SRS} \mod 60}{6} \right\rfloor \right ) \mod 10$$ | 0 | 5 | 0 | 5 | 0 | 5 | 0 | 5 | 0 | 5 | 0 |  |  |
| $$\left\lfloor \frac{n_\text{SRS} \mod 60}{2\times 6} \right\rfloor$$ | 0 | 0 | 1 | 1 | 2 | 2 | 3 | 3 | 4 | 4 | 0 |  |  |
| $$F_2(n_\text{SRS})$$ | 0 | 5 | 1 | 6 | 2 | 7 | 3 | 8 | 4 | 9 | 0 |  |  |
	\caption{b=2 时的跳频情况}
	\label{tab:srs_hopping_b2_results}
\end{table}

**b=3时**， 计算 $$K_\text{TC} M_{\text{sc},3}^\text{SRS} n_3$$ 中的 $$n_3$$:

此时 $$b > b_\text{hop}$$，利用公式 (5) 中的第二种情况， 进而需要看公式(4), 此时，$$N_b= N_3 = 14$$ ,是偶数(Even)，所以，使用公式(4) 中的第一种情况。

$$
\prod_{b' = b_{\text{hop}}}^{b} N_{b'} = \prod_{b' = 0}^{3} N_{b'} = 
	N_0 \times N_1 \times N_2 \times N_3 = N_\text{hop} \times N_1 \times N_2
	\times N_3 = 1\times 6 \times 10 \times 14  = 840
$$

这个连乘$$\prod_{b' = b_{\text{hop}}}^{b} N_{b'}$$ 表示的是到当前这个 $$b$$ 时，总共有多少次跳频，也就是会分成多少段。 而分母中的连乘 $$\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}$$ 表示的是到当前这个 $$b$$ 之前时，总共有多少次跳频(即会分成多少段)

$$
\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'} = \prod_{b' = 0}^{2} N_{b'} = N_0 \times N_1 \times N_2 = N_\text{hop} \times N_1 \times N_2= 1\times 6 \times 10 = 60
$$

则:

$$
F_2(n_\text{SRS}) = N_2/2 \left\lfloor \frac{n_\text{SRS} \mod 840}{60} \right\rfloor +
	\left\lfloor \frac{n_\text{SRS} \mod 840}{2 \times 60} \right\rfloor
$$

计算出来的结果，如表格 \ref{srs_hopping_b3_results} 所示。 可以看到，在这个层次，其跳频的规律是，在第一半里面选择一个段，然后，跳到第二半里面选择同样偏移(相对于一半的位置)位置的一个段，然后，再跳回到第一半里面选择下一个偏移位置，

因为这是第一层的跳频，比较容易理解。后面层的跳频规律都是类似的，但是要考虑前面层要先跳转一遍，才能在本层中有跳跃的动作。
为了描述方便，我们令：

$$
\left (
	(14/2)     
	\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{3} N_{b'}}
	{\prod_{b' = b_{\text{hop}}}^{3-1} N_{b'}}
	\right\rfloor
	
	\right ) \mod 14 = J_0
\tag{8}
$$

以及：

$$
\left\lfloor 
	\frac
	{n_{\text{SRS}} \mod \prod_{b' = b_{\text{hop}}}^{3} N_{b'}}
	{2 \prod_{b' = b_{\text{hop}}}^{3-1} N_{b'}}
	\right\rfloor = J_1
$$

需要注意的是，公式 (8) 中多做了一个 $$\mod N_3$$，这是因为后续 $$F_b(n_\text{SRS})$$ 会被 $$\mod N_3$$，所以，为了更清楚看到最终的影响，我们在里面也做一次 $$\mod N_3$$,不影响最终结果。

而这体现在 $$J_0$$ 重复多少次后才变化（增加或者回退到 0），当然，也要求 $$J_1$$ 重复多少次才增加或者回退到 0，因为这是决定在一半中的频移位置，因此， $$J_1$$ 重复的次数是 $$J_0$$ 的两倍，因为，在上一层的所有跳频遍历一遍后，本层是跳转一半，等到把两半都跳转完，才能让在一半内的偏移做跳转。

从表中可以看到，$$J_0$$ 在 60 次才增加，对应于截至上一层是段的个数。$$j_1$$ 在 120 次才增加。


\begin{table}
	\centering
	\resizebox{\textwidth}{!}{%
		| $$n_\text{SRS}$$ | 0-59 | 60-119 | 120-179 | 180-239 | ... | 600-659 | 660-719 | 720-779 | 780-839 | 840-899 | 900-959 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| $$n_\text{SRS} \mod 840$$ | 0-59 | 60-119 | 120-179 | 180-239 | ... | 600-659 | 660-719 | 720-779 | 780-839 | 0-59 | 60-119 |
| $$\left\lfloor \frac{n_\text{SRS} \mod 840}{60} \right\rfloor$$ | 0 | 1 | 2 | 3 | ... | 10 | 11 | 12 | 13 | 0 | 1 |
| $$\frac{14}{2} \left\lfloor \frac{n_\text{SRS} \mod 840}{60} \right\rfloor$$ | 0 | 7 | 14 | 21 | ... | 70 | 77 | 84 | 91 | 0 | 7 |
| $$\left (\frac{14}{2} \left\lfloor \frac{n_\text{SRS} \mod 840}{60} \right\rfloor \right ) \mod 14$$ | 0 | 7 | 0 | 7 | ... | 0 | 7 | 0 | 7 | 0 | 7 |
| $$\left\lfloor \frac{n_\text{SRS} \mod 840}{2\times 60} \right\rfloor$$ | 0 | 0 | 1 | 1 | ... | 5 | 5 | 6 | 6 | 0 | 0 |
| $$F_3(n_\text{SRS})$$ | 0 | 7 | 1 | 8 | ... | 5 | 12 | 6 | 13 | 0 | 7 |
	}
	\caption{b=3 时的跳频情况}
	\label{tab:srs_hopping_b3_results}
\end{table}

## $$N_b$$ 是奇数的例子

当 $$N_b$$ 是奇数时，使用公式 (4) 中的第二种情况，即：

$$
F_b(n_{\text{SRS}}) = 
	\left\lfloor N_b / 2 \right\rfloor \cdot 
	\left\lfloor 
	\frac{n_{\text{SRS}}}{\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'}}
	\right\rfloor
$$

然后：

$$
n_b = F_b(n_{\text{SRS}}) \mod N_b
$$

我们以 $$N_b=7 为例子$$,则：

$$
\left\lfloor N_b/2 \right\rfloor =  \left\lfloor 7/2 \right\rfloor = 3
$$

并且假定 $$\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'} = 2$$，即在这个前面有2个段。


\begin{table}
	\centering
	| $$n_\text{SRS}$$ | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 |  |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| $\left\lfloor \frac{n_{\text{SRS}}}{\left (\prod_{b' = b_{\text{hop}}}^{b-1} N_{b'} \right )=2}
		\right\rfloor $ | 0 | 0 | 1 | 1 | 2 | 2 | 3 | 3 | 4 | 4 | 5 | 5 | 6 | 6 | 7 | 7 | 8 | 8 | 9 | 9 |  |
| $$F_b(n_\text{SRS})$$ | 0 | 0 | 3 | 3 | 6 | 6 | 9 | 9 | 12 | 12 | 15 | 15 | 18 | 18 | 21 | 21 | 24 | 24 | 27 | 27 |  |
| $$n_b$$ | 0 | 0 | 3 | 3 | 6 | 6 | 2 | 2 | 5 | 5 | 1 | 1 | 4 | 4 | 0 | 0 | 3 | 3 | 6 | 6 |  |
	\caption{$$N_b=7$$}
	\label{tab:srs_hopping_Nb_odd}
\end{table}

![图6：$$N_b=7$$ 时在时频格子中跳频情况](/figure/5GNR/srs/srsHopping/Nb_7_hopping_in_grid.png)

*图6：$$N_b=7$$ 时在时频格子中跳频情况*

## 一个总的例子

如图 7 和 图 8

![图7：一个总的例子-计算跳频](/figure/5GNR/srs/srsHopping/一个总的例子-计算跳频.png)

*图7：一个总的例子-计算跳频*

![图8：一个总的例子-跳频图](/figure/5GNR/srs/srsHopping/一个总的例子-跳频图.png)

*图8：一个总的例子-跳频图*