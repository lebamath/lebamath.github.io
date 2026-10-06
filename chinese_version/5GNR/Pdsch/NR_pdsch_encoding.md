---
layout: default
title: " NR 5G PDSCH 编码流程"
back_url: /index.html?lang=zh
---



## NR 5G PDSCH 编码流程-001-从 CRC 到 LDPC 编码完成-不包含 rate match 及其以后的步骤

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep569648)

这篇文章主要来讲一个 5G NR 协议中 PDSCH（下行共享物理信道）的 LDPC 编码，从 CRC 开始到 LDPC 编码结束，不包括速率匹配 (Rate Match)及其之后的流程。本文主要目的是解释 5G NR 的 LDPC 编码，只是为了完整性，为了更容易理解，我们从 CRC 开始讲起，这样读者就有一个上下文的环境来理解 LDPC 编码。

完整的 PDSCH 编码流程如下图所示，此图摘自网上的文章 [1]:

![NR_PDSCH_Flow.png](/figure/5GNR/pdsch/NR_PDSCH_Flow.png)

所以，本文章会讲述上图中的第 1、2、3 和 4 步。核心目的是为了讲第 4 步。



### Transport block CRC attachment

（对应协议 38.212 - 7.2.1）

将要发送的实际的数据，记为 

$$
[a_0,a_1,a_2,a_3,\dots,a_{A-1}]
$$

其中，$$A$$ 是数据的长度，即比特数。

根据 38.212 - 7.2.1 的描述，有两种情况：

1)  A > 3824，则增加 24 比特的 CRC 校验位，用38.212 - 5.1 节描述的 $$g_{CRC24A}(D)$$ 公式来产生 24 比特的校验位
2) 否则，即 $$A\le 3824$$, 则增加 16 比特的 CRC 校验位，用38.212 - 5.1 节描述的 $$g_{CRC16}(D)$$ 公式来产生 16 比特的校验位

将 CRC 校验位的长度记为 $$L$$，则经过 CRC 之后的数据长度 $$B=A+L$$，记为：

$$
[b_0,b_1,b_2,b_3,\dots,b_{B-1}]   \tag{1}
$$

下图摘自网上的文章 [1]，清晰地演示了上面说的过程：

![NR_PDSCH_TransportBlockCRC_01.png](/figure/5GNR/pdsch/NR_PDSCH_TransportBlockCRC_01.png)


### LDPC base graph selection

（对应协议 38.212 - 7.2.2）

这一步，是选择一个基础表格，用这个表格最终会生成 LDPC 用的校验矩阵和生成矩阵。这个表格的具体用途会在后面详细解释，在这里只需要知道有这么两个表格，用于应对不同数据长度和码率的情况。

这两个表格分别称为 Base Graph 1 和 Base Graph 2. 具体如何选择，协议中是这样写的：

-	if  $$A\le 292$$ , or if  $$A\le 3824$$  and $$R\le 0.67$$  , or if $$R\le 0.25$$  , 选择使用 LDPC base graph 2 ;

- 否则, 选择 LDPC base graph 1 

文章[1] 中有几个图来解释这个选择范围，比较清楚：

![NR_PDSCH_LDPC_basegraph_selection_02.png](/figure/5GNR/pdsch/NR_PDSCH_LDPC_basegraph_selection_02.png)


![NR_PDSCH_LDPC_basegraph_selection_03.png](/figure/5GNR/pdsch/NR_PDSCH_LDPC_basegraph_selection_03.png)

总的来讲，或者粗略来看，Base Graph 1 用于数据比较长，或者码率比较高（冗余度小）的情况。当码长较小或者码率比较低时，倾向于选择 Base Grapha 2.



### Code block segmentation and code block CRC attachment

（对应协议 38.212 - 7.2.3）

这一步是保证在 Transport Block 比较大的时候，也能让 LDPC 进行有效率地编码，通过与某个值比较(称为最大的 code bock 尺寸， 记为 $$K_{cb}$$，如果大于这个值，则对 Transport Block 进行分段，每一段单独做 LDPC 编码(并增加 CRC 校验比特)。注意最上面的第一幅图中，第三步结束后的第四步，有几个并行的长方形，每个长方形表示一个独立的 LDPC 编码。

注意这里面有两个名词，一个是 Transport Block，一个是 Code Block，前者是总共要发的数据，即要传输(Transport)的数据，后者表示一次 LDPC 编码所处理的数据。

下图是文章[1] 中一个图，比较形象地说明了这个步骤在干啥：

![NR_PDSCH_CodeBlockSegmentation_01.png](/figure/5GNR/pdsch/NR_PDSCH_CodeBlockSegmentation_01.png)

需要注意的是，上面图中，分割后的 CodeBlock 绿色部分不是仅仅把 Transport Block 绿色那一段 进行分割而得到的，实际上也包括灰色的那个 CRC.



具体过程如下：

1)  确定最大的 code block 尺寸 $$K_{cb}$$， 这个值随着 Base Graph 不同而不同
- 如果 Base Graph 1，则 $$K_{cb}=8448$$

- 如果 Base Graph 2，则 $$K_{cb}=3840$$

​         可以看到，因为 Base Graph 1 是用于较长数据情况的，因此，其允许的最大值也比 Base Graph 2 情况下的大。

2. 确定有多少块 Code Block，即分段后有多少个段


if  B(Transport Block) < $$K_{cb}$$( 最大 Code Block 尺寸)\\
\indent \indent //表示不需要分段   \\
\indent \indent L= 0 //无需额外增加CRC校验了，因为每个 Transport Block 已经有CRC校验了  \\
​\indent \indent C( Code Block的块数 ) = 1  //就一段，没有分段  \\
\indent \indent B' = B  \\
\indent else  \\
\indent \indent L = 24   // 24 bits CRC  \\
\indent \indent C = Ceiling(B/($$K_{cb}$$ - L))  \\
\indent \indent B' = B + C * L  \\
end


其中 C = Ceiling(B/($$K_{cb}$$ - L)) 这个是计算分段之后有多少块，需要说明的是每一个块的最大长度 $$K_{cb}$$ 是包含要增加的 CRC 校验位的，而 Transport Block 长度 B 是不包含 CRC 校验位的，因此，在计算有多少段时，每段的长度去掉校验位长度 L，再和 B 做除法。



3.  计算每一个块的大小，这里需要注意的是，最终送到 LDPC 编码时，每个块的大小是一样的，所以，可能有些需要填充 NULL （后面在 LDPC 编码前会把 NULL 换成比特 0）。每个块的大小为 22 $$Z_c$$ (对于 Base Graph 1)  或者 10 $$Z_c$$ (对于 Base Graph 2)，下面就是需要确定 $$Z_c$$


\noindent// 先确定 $$K_b$$, 注意，这里用的 是 B 不是 B'  \\
For LDPC base graph 1,  \\
\indent \indent  $$K_{b} = 22$$  \\
For LDPC base graph 2,  \\
\indent \indent if B > 640  \\
\indent \indent \indent  $$K_{b} = 10$$ \\
\indent \indent else if B > 560 \\
\indent \indent \indent$$K_{b} = 9$$  \\
\indent \indent else if B > 192  \\
​\indent \indent \indent $$K_{b} = 8$$  \\
\indent \indent else  \\
\indent \indent \indent $$K_{b} = 6$$ \\
\indent \indent end if

\noindent // 再用 $$K_b$$，以及 B' 来确定 $$Z_c$$ \\
​          $$K'= B'/C$$   \\   
// B' 是 包含分段后额外的 CRC 比特，但还不包括填充的 NULL，所以 K' 是个有效数据（含所有 CRC 比特）的平均长度.  这里能整除吗？不一定，看 matlab 代码，是 $$K'= \lceil B'/C \rceil$$

​          在表格 38.212  Table 5.3.2-1 的第二列，即最右边那一列中，找最小的 Z，使得  $$K_b * Z \ge K'$$ ， 这个最小的 Z ，就记为 $$Z_c$$； 同时，根据 $$Z_c$$ 所在的行，我们得到 $$i_{LS}$$ 这个值，$$i_{LS}$$  是为了在 Base Graph 中再细分选择不同的 LDPC 编码图，即不同的生成矩阵/校验矩阵。



​                                                 38.212  Table 5.3.2-1:Sets of LDPC lifting size Z  \\
| Set index ( $$i_{LS}$$ ) | Set of lifting sizes ( Z ) |  |
|---|---|---|
| 0 | {2, 4, 8, 16, 32, 64, 128, 256} |  |
| 1 | {3, 6, 12, 24, 48, 96, 192, 384} |  |
| 2 | {5, 10, 20, 40, 80, 160, 320} |  |
| 3 | {7, 14, 28, 56, 112, 224} |  |
| 4 | {9, 18, 36, 72, 144, 288} |  |
| 5 | {11, 22, 44, 88, 176, 352} |  |
| 6 | {13, 26, 52, 104, 208} |  |
| 7 | {15, 30, 60, 120, 240} |  |
\\


​           $$Z_c$$ 确定后，就可以知道每个块（送给 LDPC 编码前的数据）的大小了。

​	   这里需要再说明一下，如果原始数据，即公式(1) 的原始数据  $$[b_0,b_1,b_2,b_3,\dots,b_{B-1}]$$ ，不能被 C 个块平均分的话，则需要在后面填充 0，补到 能被 C 个块平均分。

​	

4. 构造每个块的数据，以便供下一步做 LDPC 编码

这个新构造的数据块，记为

$$
[c_{r0},c_{r1},c_{r2},\cdots,c_{rk}]
$$

\noindent s = 0;  \\
for r=0 to C-1  \\
\indent // 这个循环，是把有效数据复制过来   \\
\indent for k=0 to K'-L-1   \\
​\indent \indent $$c_{rk}=b_s$$;  \\
\indent \indent s = s + 1  \\
\indent end for  \\
\indent // 如果是有多个 code block，则对每一个都要添加 CRC 比特  \\
\indent // 输入 $$c_{r0},c_{r2},c_{r3},c_{r3},\dots,c_{r(K'-L-1)}$$，根据 5.1 的生成多项式 $$g_{CRC24B}(D)$$ \\
\indent //生成校验比特 $$p_{r0},p_{r1},p_{r2},\dots,p_{r(L-1)}$$  \\
​\indent  if   C > 1             \\    
\indent \indent   //添加 CRC 比特  \\
\indent \indent for k=K'-L  to K'-1  \\
\indent \indent \indent $$c_{rk} = P_{r(k+L-K')}$$;   \\
\indent \indent end for  \\
\indent end if  \\
\indent //最后放入填充比特  \\
\indent for  k=K' to K - 1    -- Insertion of filler bits  \\
\indent \indent $$c_{rk} = <NULL>$$   \\
\indent end for  \\
end for  \\



经过这一步，如果有 Segmentation，则每个 code block 大小是相等的，都是 K 这么长。 $$K = 22 Z_c$$ (对于 Base Graph 1)  或者 $$K=10 Z_c$$ (对于 Base Graph 2)。



###  Channel coding

（对应协议 38.212 - 7.2.4，具体步骤在 38.212 - 5.3.2 节描述 ）

前面在确定 $$Z_c$$ 的过程中，得到了一个值  $$i_{LS}$$, 这个值用来构造 LDPC 的校验矩阵，进而得到编码矩阵和过程。

这个值用来在表格 38.212 - 5.3.2-2 和 5.3.2-3 中选择一列.

![LDPC_Base_Graph_1_HBG_Vij_table_5.3.2-2.png](/figure/5GNR/pdsch/LDPC_Base_Graph_1_HBG_Vij_table_5.3.2-2.png)

i 和 j 对应的是 $$H_{BG}$$ 矩阵的行和列，例如上图，假定我们得到的 $$i_{LS} = 1$$ , 则选择这个表格的图中绿色那一列，注意，那个不是两列，是因为表格太大，分成左右两块写的。那么当 i=16, j=20 时，我们可以得到 $$V_{i=16,j=20}=289$$， 如上图中红色圈圈，再对 289 这个值用 $$Z_c$$ 取模，得到的余数，放到 $$H_{BG}$$ 矩阵的第 i 行 第 j 列那个位置。

对所有的 i 和 j 遍历，都填充到 $$H_{BG}$$ 矩阵中，最后，这个矩阵中没有被填充到的地方都填  -1.



$$H_{BG}$$(0,0) , 第 0 行第 0 列的数据是 307 mod $$Z_c$$ \\
$$H_{BG}$$(0,1) , 第 0 行第 1 列的数据是  19 mod $$Z_c$$\\
$$H_{BG}$$(0,2) , 第 0 行第 2 列的数据是 50 mod $$Z_c$$\\
$$H_{BG}$$(0,3) , 第 0 行第 3 列的数据是 369 mod $$Z_c$$\\
$$H_{BG}$$(0,4) , 第 0 行第 4 列的数据是  -1，因为在上面的表格中没有.\\
$$H_{BG}$$(0,5) , 第 0 行第 5 列的数据是 181 mod $$Z_c$$\\
$$H_{BG}$$(0,6) , 第 0 行第 6 列的数据是 216 mod $$Z_c$$\\
$$H_{BG}$$(0,7) , 第 0 行第 7 列的数据是  -1，因为在上面的表格中没有.\\
$$H_{BG}$$(0,8) , 第 0 行第 8 列的数据是  -1，因为在上面的表格中没有.\\
$$H_{BG}$$(0,9) , 第 0 行第 9 列的数据是 317 mod $$Z_c$$\\

........



会得到类似下面两个图中的矩阵，摘自文章[2]：

![BG1.png](/figure/5GNR/pdsch/BG1.png)

![BG2.png](/figure/5GNR/pdsch/BG2.png)

展开后得到类似与下面这个矩阵，摘自文章[2]:

![HBG_matrix_example.png](/figure/5GNR/pdsch/HBG_matrix_example.png)

最终的校验矩阵是对上面这个矩阵再进一步扩展，用一个 $$Z_c \times Z_c$$ 的单位矩阵，来代替上面矩阵中的每个元素，代替的方法为：

数字 -1 的，填写 $$Z_c \times Z_c$$ 的 0 矩阵

数字 0 的，填写  $$Z_c \times Z_c$$ 的单位矩阵

数字大于 0 的，对 $$Z_c \times Z_c$$ 的单位矩阵进行循环右移，用右移后的矩阵来填写，例如 $$Z_c=4$$，上面矩阵中某个元素数字为 1，则

对单位矩阵 $$\begin{bmatrix}  1 & 0 & 0 & 0 \\0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1  \end{bmatrix}$$  循环右移一位，得到 $$\begin{bmatrix} 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 1 & 0 & 0 & 0  \end{bmatrix}$$ ; 如果上面的数字是 2，则对单位矩阵循环右移两位得到$$\begin{bmatrix} 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0  \end{bmatrix}$$



至此，对于 Base Graph 1，我们得到的 $$H_{BG}$$ 矩阵是 $$46Z_c$$ 行，$$68 Z_c$$ 列的矩阵；对于 Base Graph 2, 我们得到的 $$H_{BG}$$ 矩阵是 $$42Z_c$$ 行，$$52 Z_c$$ 列的矩阵.



后续，将使用这个 $$H_{BG}$$ 矩阵进行 LDPC 的编码，编码后的数据长度，若是 Base Graph 1，则为 $$66 Z_c$$； 若是 Base Graph 2，则为 $$50 Z_c$$.

具体编码过程，留待下一篇文章介绍。



[1] \url{https://www.sharetechnote.com/html/5G/5G_PDSCH.html}  PDSCH (Physical Data Shared Channel) in a Nutshell [5G | ShareTechnote]

[2] 5G-NR DL-SCH LDPC Channel Coding Base Graph selection and Coding Procedure \\ 
\noindent\url{https://www.linkedin.com/pulse/5g-nr-dl-sch-ldpc-channel-coding-base-graph-selection-chelikani}

## NR LDPC编码过程
根据上一篇文章的介绍，送到 LDPC 编码器的数据，是按照 code block 来，一个 code block 的长度用 $$K*Z_c$$ 来表示。输入的数据表示为

$$
[s_0,s_1,s_2,\cdots,s_{K_{bc}-1}] = S
$$

注意：上面向量中的每个元素，都是一个 $$Z_c$$ 长度的向量。



经过 LDPC 编码后的数据，长度为 $$N$$，当使用 Base Graph 1 时， $$N=66Z_c$$; 当使用 Base Graph 2 时， $$N=50Z_c$$;  编码后的数据记为

$$
d_0,d_1,d_2,\cdots,d_{N-1}
$$

编码的基本过程是：用原始数据 $$s_0,s_1,s_2,\cdots,s_{K-1}$$ 来生成校验数据，然后按照一定规则，把原始数据和校验数据放在一起，构成 $$d_0,d_1,d_2,\cdots,d_{N-1}$$， 所以，核心是如何生成校验数据。

本文主要参考网上文章 [1]，在此表示感谢。本文基本采用文章 [1] 中的符号，与 3GPP 协议中略有不同，注意区分。

根据上一篇文章，生成的 $$H_{BG}$$ 矩阵，形状如下图：


![BG2.png](/figure/5GNR/pdsch/BG2.png)


M 行， N 列的矩阵 $$M \times N$$   





生成的校验数据，分成两个部分，一个是 $$P_b$$， 含有 $$4Z_c$$ 个比特；一个是 $$P_c$$， 含有的数量与校验矩阵的行数有关，是 $$(行数-4) Z_c$$ .

则构造一个新的矩阵：

$$
C =  
[s_1 , s_2  , \dots , s_K , p_{b_1} , p_{b_2} , p_{b_3}  , p_{b_4} , p_{c_1} , p_{c_2} , \dots , p_{c_{M-4}}]
$$

注意，其中每个元素都是一个 $$Z_c$$ 长度的行向量。



编码的原理就是找 $$P_A$$ 和 $$P_C$$，使其满足：

$$
H_{BG} \begin{bmatrix}
	S^T \\
	P_b^T \\
	P_c^T
\end{bmatrix} = 0   \tag{1}
$$

将 $$H_{BG}$$ 写成

$$
H_{BG} = \begin{bmatrix}
	A & B & 0 \\
	C_1 & C_2 & I
\end{bmatrix}   \tag{2}
$$

其中

$$
\begin{aligned}
A = \begin{bmatrix}
	a_{1,1} & a_{1,2} & \dots & a_{1,K} \\
	a_{2,1} & a_{2,2} & \dots & a_{2,K} \\
	a_{3,1} & a_{3,2} & \dots & a_{3,K} \\
	a_{4,1} & a_{4,2} & \dots & a_{4,K} \\
\end{bmatrix} \quad  \quad 
C_1 = \begin{bmatrix}
	c_{1,1} & c_{1,2} & \dots & c_{1,K} \\
	c_{2,1} & c_{2,2} & \dots & c_{2,K} \\
	\vdots  & \vdots & \ddots \\
	c_{M-4,1} & c_{M-4,2} & \dots & c_{M-4,K} \\
\end{bmatrix} \quad  \quad  \\
C_2 = \begin{bmatrix}
	c_{1,K+1} & c_{1,K+2} & c_{1,K+3}  & c_{1,K+4} \\
	c_{2,K+1} & c_{2,K+2} & c_{2,K+3}  & c_{2,K+4} \\
	\vdots  & \vdots & \ddots \\
	c_{M-4,K+1} & c_{M-4,K+2} &   c_{M-4,K+3} & c_{M-4,K+4}\\
\end{bmatrix} \quad  \quad
\end{aligned}
$$

![HBG2_different_blocks.png](/figure/5GNR/pdsch/HBG2_different_blocks.png)

对于子矩阵 B，一共只有四种情况（而对于 A, C1 和 C2 各有 16 种情况，Base Graph 1 有 8 种， Base Graph 2 有 8 种 ) ， 虽然表面看，B 也有 16 种情况，但是，很多情况是得到相同的子矩阵 B.  对于这四种矩阵 B，分别记为 $$H_{BG1\_B1},H_{BG1\_B2},H_{BG2\_B1},H_{BG2\_B2}$$

1) 对于 Base Graph 1

如果 $$i_{LS} \in (0,1,2,3,4,5,7)$$，则   

$$
H_{BG1\_B1} = \begin{bmatrix}
	1 & 0 & -1 & -1 \\
	0 & 0 & 0 & -1 \\
	-1 & -1 & 0 & 0 \\
	1 & -1 & -1 & 0 
\end{bmatrix}
$$


如果 $$i_{LS}=(6)$$，则  

$$
H_{BG1\_B2} = \begin{bmatrix}
	0 & 0 & -1 & -1 \\
	105 & 0 & 0 & -1 \\
	-1 & -1 & 0 & 0 \\
	0 & -1 & -1 & 0 
\end{bmatrix}
$$

2) 对于 Base Graph 2 **

如果 $$i_{LS} \in (0,1,2,4,5,6)$$，    则  

$$
H_{BG2\_B1} = \begin{bmatrix}
	0 & 0 & -1 & -1 \\
	-1 & 0 & 0 & -1 \\
	1 & -1 & 0 & 0 \\
	0 & -1 & -1 & 0 
\end{bmatrix}
$$


  如果 $$i_{LS} \in (3,7)$$，则  

$$
H_{BG2\_B2} = \begin{bmatrix}
	1 & 0 & -1 & -1 \\
	-1 & 0 & 0 & -1 \\
	0 & -1 & 0 & 0 \\
	1 & -1 & -1 & 0 
\end{bmatrix}
$$

根据公式 (1) 和 (2)，我们可以得到如下的约束方程：

$$
A s^T + B P_b^T = 0^T   \\
C_1 S^T + C_2 P_b^T + P_c^T = 0
\tag{4}
$$

首先，根据公式 (4) 中的第一个矩阵方程，来计算 $$P_b$$，因为矩阵 B 有四种情况，我们也分四种情况来讨论。因为第一个矩阵方程中 $$B$$ 是一个 $$4\times4$$ 的矩阵（当然，这个矩阵中的元素又是 $$Z_c\times Z_c$$ 的矩阵），所以四种情况中的每种情况，都可以再列出四个方程来。

$$
H_{BG1\_B1}: \left\{\begin{array}{l}
	\sum_{j=1}^K a_{1,j} s_j + p_{b_1}^{(1)} + p_{b_2} = 0\\ 
	\sum_{j=1}^K a_{2,j} s_j + p_{b_1} + p_{b_2}+ p_{b_3} = 0 \\
	\sum_{j=1}^K a_{3,j} s_j + p_{b_3} + p_{b_4} = 0 \\
	\sum_{j=1}^K a_{4,j} s_j + p_{b_1}^{(1)} + p_{b_4} = 0
\end{array} \right.
\quad; \quad
H_{BG1\_B2}: \left\{\begin{array}{l}
	\sum_{j=1}^K a_{1,j} s_j + p_{b_1} + p_{b_2} = 0\\ 
	\sum_{j=1}^K a_{2,j} s_j + p_{b_1}^{(105\ mod\ Z_c)} + p_{b_2}+ p_{b_3} = 0 \\
	\sum_{j=1}^K a_{3,j} s_j + p_{b_3} + p_{b_4} = 0 \\
	\sum_{j=1}^K a_{4,j} s_j + p_{b_1} + p_{b_4} = 0 \\
\end{array} \right.
$$

$$
H_{BG1\_B1}: \left\{\begin{array}{l}
	\sum_{j=1}^K a_{1,j} s_j + p_{b_1} + p_{b_2} = 0\\ 
	\sum_{j=1}^K a_{2,j} s_j + p_{b_2}+ p_{b_3} = 0 \\
	\sum_{j=1}^K a_{3,j} s_j + p_{b_1}^{(1)} + p_{b_3} + p_{b_4} = 0 \\
	\sum_{j=1}^K a_{4,j} s_j + p_{b_1} + p_{b_4} = 0 \\
\end{array} \right.
\quad; \quad
H_{BG1\_B2}: \left\{\begin{array}{l}
	\sum_{j=1}^K a_{1,j} s_j + p_{b_1}^{(1)} + p_{b_2} = 0\\ 
	\sum_{j=1}^K a_{2,j} s_j + p_{b_2} + p_{b_3} = 0 \\
	\sum_{j=1}^K a_{3,j} s_j + p_{b_1} + p_{b_3} + p_{b_4} = 0 \\
	\sum_{j=1}^K a_{4,j} s_j + p_{b_1}^{(1)} + p_{b_4} = 0 \\
\end{array} \right.
$$

其中 $$p_{b_1}^{(1)}$$ 是一个行向量，上标的 (1) 表示对这个行向量循环右移，即 $$p_{b_1} = [\quad p_{b_{11}} \quad p_{b_{12}} \quad p_{b_{13}} \quad p_{b_{14}} \quad]$$, 则 $$p_{b_1}^{(1)} = [\quad p_{b_{14}} \quad p_{b_{11}} \quad p_{b_{12}} \quad p_{b_{13}} \quad]$$

同理 $$p_{b_1}^{(105\ mod\ Z_c)}$$ 也是循环右移，右移的位置数量是 $$105\ mod\ Z_c$$



我们可以比较轻松地解上面的方程，我们第一种情况为例子来说明解方程过程，另外三种情况将直接给出解。

即我们将解如下这个方程组：

$$
\begin{array}{l}
	\sum_{j=1}^K a_{1,j} s_j + p_{b_1}^{(1)} + p_{b_2} = 0\\ 
	\sum_{j=1}^K a_{2,j} s_j + p_{b_1} + p_{b_2}+ p_{b_3} = 0 \\
	\sum_{j=1}^K a_{3,j} s_j + p_{b_3} + p_{b_4} = 0 \\
	\sum_{j=1}^K a_{4,j} s_j + p_{b_1}^{(1)} + p_{b_4} = 0 \\
\end{array} \tag{5}
$$

将四个方程加在一起

$$
\sum_{j=1}^K a_{1,j} s_j + p_{b_1}^{(1)} + p_{b_2}  + 
\sum_{j=1}^K a_{2,j} s_j + p_{b_1} + p_{b_2}+ p_{b_3} + 
\sum_{j=1}^K a_{3,j} s_j + p_{b_3} + p_{b_4} +
\sum_{j=1}^K a_{4,j} s_j + p_{b_1}^{(1)} + p_{b_4} = 0 \tag{6}
$$

注意是异或加法，所以 $$p_{b_2} + p_{b_2} = 0$$,  因此，式(6) 可以化简为：

$$
\sum_{j=1}^K a_{1,j} s_j  +
\sum_{j=1}^K a_{2,j} s_j + p_{b_1} +
\sum_{j=1}^K a_{3,j} s_j +
\sum_{j=1}^K a_{4,j} s_j  = 0 \tag{7}
$$

所以：

$$
p_{b_1} = \sum_{j=1}^K a_{1,j} s_j  +  \sum_{j=1}^K a_{2,j} s_j +
\sum_{j=1}^K a_{3,j} s_j + \sum_{j=1}^K a_{4,j} s_j 
=\sum_{i=1}^4 \sum_{j=1}^K a_{i,j} s_j
\tag{8}
$$

再把 (8) 代入到 (5) 的第一个，第四个和第三个方程，可以得到:

$$
\begin{aligned}
p_{b_2} =  \sum_{j=1}^K a_{1,j} s_j + p_{b_1}^{(1)}  \\
p_{b_4} =  \sum_{j=1}^K a_{4,j} s_j + p_{b_1}^{(1)}  \\
p_{b_3} =  \sum_{j=1}^K a_{3,j} s_j + p_{b_4}  \\
\end{aligned}
$$

用同样的方法，可以把其他三种情况的方程都解出来，把四种情况的解都列在这里，为了书写方便，令：

$$
\lambda_i = \sum_{j=1}^K a_{i,j} s_j \quad \quad \quad i=1,2,3,4
$$

四种情况的解为：

$$
H_{BG1\_B1}: \left\{\begin{array}{l}
	p_{b_1} = \sum_{i=1}^4\lambda_i\\ 
	p_{b_2} = \lambda_1 + p_{b_1}^{(1)} \\ 
	p_{b_4} = \lambda_4 + p_{b_1}^{(1)} \\ 
	p_{b_3} = \lambda_3 + p_{b_4} 
\end{array}\right. 
\quad; \quad
H_{BG1\_B2}: \left\{\begin{array}{l}
	p_{b_1}^{(105\ mod\ z)} = \sum_{i=1}^4\lambda_i\\ 
	p_{b_2} = \lambda_1 + p_{b_1} \\ 
	p_{b_4} = \lambda_4 + p_{b_1} \\ 
	p_{b_3} = \lambda_3 + p_{b_4} 
\end{array}\right.
$$

$$
H_{BG1\_B3}: \left\{\begin{array}{l}
	p_{b_1}^{(1)} = \sum_{i=1}^4\lambda_i\\ 
	p_{b_2} = \lambda_1 + p_{b_1} \\ 
	p_{b_3} = \lambda_2 + p_{b_2} \\ 
	p_{b_4} = \lambda_4 + p_{b_1} 
\end{array}\right. 
\quad; \quad
H_{BG1\_B4}: \left\{\begin{array}{l}
	p_{b_1} = \sum_{i=1}^4\lambda_i\\ 
	p_{b_2} = \lambda_1 + p_{b_1}^{(1)} \\ 
	p_{b_3} = \lambda_2 + p_{b_2} \\ 
	p_{b_4} = \lambda_4 + p_{b_1} ^{(1)}
\end{array}\right.
$$

$$P_b$$ 已知之后，因为 $$S$$ 是已知的，所以，根据公式 (4) 的第二个方程，可以直接解出来 $$P_c$$:

$$
P_c^T = C_1 S^T + C_2 P_b^T \\
===>
P_{ci} = \sum_{j=1}^K c_{i,j} s_j + \sum_{j=1}^4 c_{i,(K+j)} p_{bj} \\
$$

然后，根据协议规定， $$S$$ 中的前 $$2 Z_c$$ 个数据扔掉不传，且 S 中原来 NULL 位置对应的 0 也不传，然后再把校验位拼接在后面，就是这一个 code block 后经过 LDPC 编码后有用的数据（要传输的数据都在这里面做 truncating 或者 repetition) .




[1] [5G NR QC-LDPC Encoding Algorithm - Lyons Zhang (dsprelated.com)] \\
\url{https://www.dsprelated.com/showarticle/1297.php}

## 计算过程的例子以及补充说明隐含的 shortening
### 以 Base Graph 1 为例子



**例子 1 不需要填充 NULL**

若 $$B=9104$$ ， 因为其大于等于 8448，所以需要分段。

首先确定段数：$$C =  \left \lceil 9104/(8448-24) \right \rceil = 2$$

则 $$B' = B + C * L = 9104 + 2 * 24 = 9152$$

然后 $$K' = B'/C = 9152/2 = 4576$$

再找最小的 $$Z$$ 使得， $$K_b * Z \ge K'$$ ，因为 Base Graph 1，所以， $$K_b = 22$$ ， 则可以找到 $$Z_c = 208$$， $$K_b * Z = 22 * 208 = 4576 = K'$$

而每一块的目标大小为 $$22Zc = 4576$$,  所以，这种情况下，不需要给每一块填 NULL, 这是因为 $$K'$$ 是分块后，每一块的原始数据（包括因为分块而在这个块上增加的 24 bits CRC 数据) 刚好是 $$22 Z_c$$ ， 即$$K-K' = 4576-4576=0$$


![分段示意图-01.png](/figure/5GNR/pdsch/分段示意图-01.png)



**例子 2 需要填充 NULL**

若 $$B=9064$$ ， 因为其大于等于 8448，所以需要分段。

首先确定段数：$$C = \left \lceil 9064/(8448-24) \right \rceil = 2$$

则 $$B' = B + C * L = 9064 + 2 * 24 = 9112$$

然后 $$K' = B'/C = 9112/2 = 4556$$

再找最小的 $$Z$$ 使得， $$K_b * Z \ge K'$$ ，因为 Base Graph 1，所以， $$K_b = 22$$ ， 则可以找到 $$Z_c = 208$$， $$K_b * Z = 22 * 208 = 4576 \ge K'=4556$$

而每一块的目标大小为 $$22Zc = 4576$$, 而现在给每一块的数据只有$$K'=4556$$,  这种情况下，**需要**给每一块填充 NULL,  则每一块需要填充 $$K-K' = 4576-4556=20$$ 个 NULL.

![分段示意图-02.png](/figure/5GNR/pdsch/分段示意图-02.png)


**例子 3 需要补充 0**

若 $$B=22000$$ ， 因为其大于等于 8448，所以需要分段。

首先确定段数：$$C =  \left \lceil 22000/(8448-24) \right \rceil = 3$$

则 $$B' = B + C * L = 22000 + 2 * 24 = 22072$$

然后 $$K' = B'/C = 22072/2 = 7357.3333333$$

再找最小的 $$Z$$ 使得， $$K_b * Z \ge K'$$ ，因为 Base Graph 1，所以， $$K_b = 22$$ ， 则可以找到 $$Z_c = 352$$， $$K_b * Z = 22 * 352 = 7744 \ge K'=7357.3333333$$

由于 $$K'$$ 不是整数，因此，需要在  $$B$$ 的长度基础上补充一些 0 ， 补充的数量为：因为 7537.3333333 是小数，即分到每个快的数据是小数，因此，需要向上取整到整数 7358，再减去每一块自己添加的 24 位 CRC 校验，一共 3 块，因此，补充 0 的数量为 (7358-24) * 3 - B =  22002 - 22000 = 2.



同样，每一块的目标大小为 $$22Zc = 7744$$, 而现在给每一块的数据经过取整后 只有 7538,  这种情况下，**需要**给每一块填充 NULL,  则每一块需要填充 $$K- 7538 = 7744-7358=386$$ 个 NULL.



### Base Graph 2 情况下的补充说明

在前面的文章有提到，当  $$B \le 640$$ 时，  $$K_b = 9， 8 ， 6$$ ， 用这个 $$K_b$$ 找到的 $$Z_c$$ 使得 $$K_b * Z \ge K'$$ 成立，我们知道 $$K'$$ 是每一块的有效数据长度，然后给每一块的实际数据长度又是 $$10 Z_c$$ ，所以，即使选择的 $$Z_c$$ 使得 $$K_b * Z = K'$$ ，而 $$K_b < 10$$，因此，无论如何，都是 $$10 Z_c > K_ b * Z_c$$ ，这就一定要填充 NULL，数量至少为 $$10Zc - K_b*Z_c$$，也就是说，**当数据较小时，就一定需要用到 shortenning**.

## Redundancy version

这篇文章来讲一下 5G Redundancy Version 的事情，由于控制信道不涉及重传的问题，所以，只是用于共享信道，共享信道用 LDPC 编码，所以，是在 LDPC 编码之后，来确定 Redundancy Version 的。

在前面的文章中我们已经讲了 LDPC 分段及其编码的过程，Redundancy Version 是针对每个分块而言的，也就是每个 LDPC 编码之后的数据。



Redundancy Version 是在 LDPC 编码后的数据中，从不同开始位置来选择发送的数据，第一次传的时候，是从 0 位置开始，当第一次失败了，基站知道是 NACK 之后，会从一个非零 的开始位置，来选择要发送的数据。

下面是一个示意图，摘自 [5G | ShareTechnote](\url{https://www.sharetechnote.com/html/5G/5G_HARQ.html})

![5G_HARQ_rv_01.png](/figure/5GNR/pdsch/5G_HARQ_rv_01.png)


这个整个圆环，就是 LDPC 编码之后的数据，在 Bit selection 之前的，即包含 NULL 数据的。这个在协议 38.212(h50 version) 5.4.2.1节的开始有说明，摘录如下：



5.4.2.1     Bit selection

The bit sequence after encoding      $$d_0,d_1,d_2,\dots,d_{N-1}$$    from Clause 5.3.2 is written into a **circular buffer** of length  $$N_{cb}$$ for the   $$r$$-th coded block, where $$N$$  is defined in Clause 5.3.2.



理论上，这个 NULL 数据其实可以不用包括在这个 循环 buffer 中（如果为了节省内存的话），但是，在后面的数据选择阶段，确定从什么位置开始拿的时候，会增加不同情况的判断，稍微增加了逻辑的复杂性。



38.212 中确定开始点的表格：

![redundancy_version_k0.png](/figure/5GNR/pdsch/redundancy_version_k0.png)


Notes:  For the   $$r$$-th code block, let $$N_{cb}=N$$  if   $$I_{LBRM}=0$$;





38.212 - 5.4.2.1 Bit Selection 过程：

![bit-selection.png](/figure/5GNR/pdsch/bit-selection.png)

可以看到，是在 circular buffer 中循环选择，其间如果碰到 NULL ，就跳过不取。


![reduancy_construct.png](/figure/5GNR/pdsch/reduancy_construct.png)

## NR 5G PDSCH TBS 的计算和在重传机制中的使用
TBS : Transport block size, 是用来告知一次数据传输中，有多少是用户的有效数据，即剔除掉增加的用于校验的冗余数据的部分。 5G NR 中，下行 TBS 是通过一个公式计算出来的，而不是在 DCI(Downlink Control Information) 中直接告知的。

这个小文章想讲一下在不同的 HARQ 传输（新数据， NACK 重传, DTX 重传）情况下，如何确定 TBS 的。

如果是新数据，即第一次传输的数据，则使用 38.214-5.1.3.2 节中的公式计算出来，公式如下：

$$
\begin{aligned}
N^{'}_\text{RE} = N^\text{RB}_\text{SC} \cdot N^\text{sh}_\text{symb} - N^{\text{PRB}}_{\text{DMRS}} - N^{\text{PRB}}_\text{oh}  \\ \\
N_\text{RE} = \text{min}(156,N^{'}_\text{RE}) \cdot n_\text{PRB} \\ \\
N_\text{info} = N_{\text{RE}} \cdot R \cdot Q_m \cdot v
\end{aligned}
$$

当 $$N_\text{info}  \le 3824$$

$$
N^{'}_\text{info} = \text{max} \left (24, 2^n \cdot \left \lfloor  \frac{N_\text{info}}{2^n} \right \rfloor \right ),\quad  其中 \ n= max(3, \left \lfloor \text{log}_2(N_\text{info})\right \rfloor)
$$

使用表 5.1.3.2-1来得到 TBS 的大小



当 $$N_\text{info}  > 3824$$

![TBS_calculation.png](/figure/5GNR/pdsch/TBS_calculation.png)

这里我们讨论上面这些公式的细节，我们只需要知道， TBS 是根据 PRB 数量，调制阶数，symbol 符号数量以及目标码率（当然还有层数，我们这里都假定是 1 层） 来确定的。



那么如果基站收到了一个 NACK，则基站需要重传，使用不同的 reduancy version(参考另外一篇文章) 来做 NACK 重传，因为 UE 需要把NACK 重传的数据与上一次收到的数据进行叠加，所以，基站侧是不能重新编码的，因此，意味着 NACK 重传的数据，其 TBS 是与第一次的是一样的，UE 收到的 NACK 重传数据，则不再重新计算 TBS. 然而，需要注意的是，NACK 重传时可以用不同数量的 PRB，不同的调制阶数，不同数量的 symbol 符号，甚至不同的层数。

![5G_HARQ_rv_01.png](/figure/5GNR/pdsch/5G_HARQ_rv_01.png)

在NACK 重传时，DCI 中给的调制阶数是用对应 MCS ( Modulation coding scheme)表格中 28 到 31 来表示的，例如表格

![MCS-table.png](/figure/5GNR/pdsch/MCS-table.png)

NACK 重传时用 28，29，30，或者 31，此时，这些值仅仅用于表示调制阶数，而不用来指示目标码率和频谱效率。这在 38.214-5.1.3.2 节中有描述，摘抄如下：

\begin{quote}
else if Table 5.1.3.1-2 is used and $$28 \le I_\text{MCS} \le 31$$

   -	the TBS is assumed to be as determined from the DCI transported in the latest PDCCH for the same transport block using  $$0 \le I_\text{MCS} \le 27$$.
\end{quote}