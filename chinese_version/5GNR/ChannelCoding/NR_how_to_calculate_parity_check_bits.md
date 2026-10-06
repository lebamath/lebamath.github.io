---
layout: default
title: "5G 协议中如何计算 Parity Check 比特"
back_url: /index.html?lang=zh
---

## 5G 协议中如何计算 Parity Check 比特

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1007761)

在 5G  Polar code 中，我们已经知道， CRC 级联  Polar code 来使用，但是，在短码的时候，也引入了 PC ( Parity Check) ，对 polar code 使用 PC 校验的理论分析，在论文 [1] 中有论述，我们这个文章主要看看 5G NR 中如何生成这个 PC 比特数据的。



上层原始数据，通过计算 CRC ，额外附加了 CRC 校验比特， CRC 校验比特是附着在原始数据尾部的，但是 PC 校验比特，不是放在末尾的，而是按照一定规则放在数据中间的位置的。

如下图所示，需要用到 PC校验比特的情况，是在上行 (Uplink) 情况下， A ( 数据比特长度) 比较小的情况，如下图中橙色背景所示。此图摘自论文 [2]


![Parity-Check-with-Polar-Code-in-NR-5G-1.png](/figure/5GNR/ChannelCoding/Parity-Check-with-Polar-Code-in-NR-5G-1.png)


首先，确定 PC 比特的位置，在 3GPP 38.212 的 5.3.1.2 节有描述：


![Parity-Check-with-Polar-Code-in-NR-5G-2-3GPP-PC-bit-position.png](/figure/5GNR/ChannelCoding/Parity-Check-with-Polar-Code-in-NR-5G-2-3GPP-PC-bit-position.png)

所有 $$n_{pc} 个$$PC 比特的位置，由 $$Q_{PC}^N$$  确定，其中有 $$n_{pc}-n_{pc}^{wm}$$ 个 是选择最不可靠 (least reliable) 的，剩余的 $$n_{pc}^{wm}$$ 个比特，在剩下的位置中，选择最小行重 (minimum row weight) 的位置，  $$n_{pc}^{wm}$$ 中的上标 wm 表示  Weight Minimum。详细的理论分析，可以参考文献 [3] ，本文重点想讲如何计算校验比特，所以，对 PC 比特位置的选择就一笔带过了。


接下来，重点讨论的是，如何计算这些校验比特，协议中是这样规定的：


![Parity-Check-with-Polar-Code-in-NR-5G-3-calculate-PC.png](/figure/5GNR/ChannelCoding/Parity-Check-with-Polar-Code-in-NR-5G-3-calculate-PC.png)


这类似一个循环移位加法器，大概结构如下（此图摘自文献 [3]）：

![Parity-Check-with-Polar-Code-in-NR-5G-4-PC-cyclic-shift-structure.png](/figure/5GNR/ChannelCoding/Parity-Check-with-Polar-Code-in-NR-5G-4-PC-cyclic-shift-structure.png)


也可以用下面的方法来理解这个过程，把输出的 $$u_n$$  按照每行 5 个元素进行排列，如果某个 $$u_i$$ 不是校验比特，就按照顺序填上 $$c_k$$ ，如果某个 $$u_i$$ 是 PC 校验比特，则这个校验比特的值是所在列的前面所有输出的异或求和。 参考下表：

![Parity-Check-with-Polar-Code-in-NR-5G-5-PC-generating-by-an-array.png](/figure/5GNR/ChannelCoding/Parity-Check-with-Polar-Code-in-NR-5G-5-PC-generating-by-an-array.png)




论文 [2]  的 IV 章的第 E 节 3) 里面，给了一个数学公式来计算 PC 校验比特，这个公式看起来不太好理解，但是，如果结合上面的表格来思考，就很容易理解了：

$$
u_i = \overset{q-1}{\underset{j=\lfloor i_{pc}/5 \rfloor}{\oplus}} \  u_{5j+p}
$$

其中,

$$i \in Q_{PC}^N$$,  表示 是 PC 校验比特的位置.

$$q = \lfloor i/5 \rfloor$$

$$p = i \ \text{mod} \ 5$$


$$i_{PC} \in Q_{PC}^N$$ 是比 i 小的里面最大的 index，并且满足 $$i_{PC} \  \text{mod} \ 5 = p$$, 如果没有这样的 index 存在 （即没有$$i_{PC} \  \text{mod} \ 5 = p$$)，则 $$i_{PC} = 0$$ 


额外补充一些延伸阅读的信息：

论文 [1] 中提到了 PC 极化码与 CRC 级联极化码相比的好处：
As a generalization, the PCC polar codes differ from the CRC-concatenated polar codes in two major aspects: (i) parity-check codes are more general than CRC codes, which makes more room for better code construction; (ii) the parity bits are allowed to be scattered within the unfrozen bit sequence for the PCC polar codes, whereas CRC bits are only located at the end of the unfrozen bit sequence for the CRC case. Scattering parity bits helps the SCL decoder to detect and prune error paths in a timely manner.

即：

概括来说，PC 极化码与 CRC 级联极化码相比，主要在两个方面有所不同：(i)  PC 校验码比 CRC 码更通用，这为更好的码构造提供了更多空间；(ii) 对于PC　极化码，校验位可以分散在未冻结比特序列中，而　CRC　位仅位于未冻结比特序列的末尾。分散校验位有助于SCL译码器及时检测并修剪错误路径。


文献 [1] 在文章的末尾提到了 PC  极化码在短码和低码率时具有更好的性能：

the PCC polar codes demonstrate a potential of error performance improvements over CRC-concatenated polar codes, especially when the codeword
length is short and code rate is low.

文献 [2] 中提到 PC 极化码与  CRC 级联极化码同时使用，用于提高在设计码时的灵活性:

3GPP decided to adopt a low complexity PC design based on shift registers [45] in conjunction with a classical CRC, as proposed in [46], to improve the code design flexibility.

关于在 5G 中 PC 极化码的设计和仿真结果，在文献 [3] 中有详细的论述和对比，有兴趣的可以进一步去阅读。 



[1] T. Wang, D. Qu and T. Jiang, "Parity-Check-Concatenated Polar Codes," in *IEEE Communications Letters*, vol. 20, no. 12, pp. 2342-2345, Dec. 2016

[2] V. Bioglio, C. Condo and I. Land, "Design of Polar Codes in 5G New Radio," in *IEEE Communications Surveys  Tutorials*, vol. 23, no. 1, pp. 29-40, Firstquarter 2021

[3] H. Zhang *et al*., "Parity-Check Polar Coding for 5G and Beyond," *2018 IEEE International Conference on Communications (ICC)*, Kansas City, MO, USA, 2018