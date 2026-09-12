---
layout: default
title: "极化码的 Belief Propagation 译码算法"
back_url: /index.html?lang=zh
---

# 极化码的 Belief Propagation 译码算法
录制的视频在 [B站](https://www.bilibili.com/cheese/play/ep1315781)

## 消息传递及其相关公式的推导

这篇文章介绍一下 Polar Code 的置信传播算法(Belief Propagation)。这篇文章需要一点  Factor Graph 或者 Forney-style factor graph (FFG)(也称为 Normal Factor Graph) 的背景知识。

N = 2  极化码示意图, 如图 1.

![图1：N = 2 极化码的示意图](/figure/极化码/bp/polar_code_core_N_is_2.png)

*图1：N = 2 极化码的示意图*

BP 算法, 是一种消息传递算法，需要迭代多次，在极化码的示意图中，由于是左右方向排布的，所以，消息传递的方向是从右向左和从左向右。 如图 2 所示。

![图2：极化码 BP 算法的消息传递](/figure/极化码/bp/polar_bp_2.png)

*图2：极化码 BP 算法的消息传递*


### 计算 LR(a)
LR(a) 这个概率，实际上是计算出 a 左方向的概率，因此，对应图 2 中的 $$a_l$$，为了书写方便，后面公式中都省略了下标 $$l$$.

为了计算 a 的概率，则传递的消息如图 3  所示。

![图3：极化码 BP 算法的消息传递, 计算 a](/figure/极化码/bp/polar_bp_3.png)

*图3：极化码 BP 算法的消息传递, 计算 a*

如果我们想知道 $$p(a=0)$$ 的概率，我们知道 $$a =  e \oplus c$$，则有两种情况满足 $$a = 0$$:

$$e = 0$$ 且 $$c =0$$  

$$e = 1$$ 且 $$c = 1$$

所以

$$
p(a=0) = p(e=0, c=0) + p(e=1,c=1)
\tag{1}
$$

在 graph 中，我们都会假定这些事件相互独立，虽然实际上因为有环的存在而不是相互独立的。这种假设就需要做多次迭代来接近真实值。

则继续推导公式 (1) 得到：

$$
p(a=0) = p(e=0) p(c=0) + p(e=1) p(c=1)
\tag{4}
$$

而 $$e = 0$$，当且仅当 $$b = 0$$  且 $$d = 0$$, 所以， 

$$
p(e=0) = p(b=0,d=0) = p(b=0)p(d=0).
\tag{2}
$$

同理 $$e = 1$$，当且仅当 $$b = 1$$  且 $$d = 1$$, 所以， 

$$
p(e=1) = p(b=1,d=1) = p(b=1)p(d=1).
\tag{3}
$$

把 (2) 和 (3) 代入 (4)

$$
p(a=0) = p(b=0)p(d=0) p(c=0) + p(b=1)p(d=1) p(c=1)
$$

用类似的推导过程，可以得到

$$
p(a=1) = p(b=1)p(d=1) p(c=0) + p(b=0)p(d=0) p(c=1)
$$

这里我们定义一个取值0/1的二值随机变量 X 的似然比为：

$$
LR(X) = \frac{p(X=1)}{p(X=0)}
$$

通过公式可以计算出似然比 LR(a) ( Likelyhood Ratio):

$$
\begin{aligned}
		LR(a) 
		&= \frac{p(a=1)}{p(a=0)} = \frac{p(b=1)p(d=1) p(c=0) + p(b=0)p(d=0) p(c=1)}{p(b=0)p(d=0) p(c=0) + p(b=1)p(d=1) p(c=1) }  \\ \\
		&=  \frac{\frac{p(b=1)}{p(b=0)} \frac{p(d=1)}{p(d=0)} + \frac{p(c=1)}{p(c=0)} }{1+ \frac{p(b=1)}{p(b=0)}  \frac{p(d=1)}{p(d=0)} \frac{p(c=1)}{p(c=0)} } \\ \\
		&= \frac{LR(b)LR(d) + LR(c)}{ 1+LR(b) LR(d) LR(c)}
	\end{aligned}
\tag{6}
$$

用公式 (2) 和 (3), 我们可以得到 LR(e) 的公式：

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(b=1)p(d=1)}{p(b=0)p(d=0)}  = LR(b)LR(d)
\tag{5}
$$

把公式 (5) 应用到公式 (6) 我们得到：

$$
LR(a) = \frac{LR(e)+LR(c)}{1+LR(c)LR(e)}
$$

### 计算 LR(b)

这里需要计算的是图 2 中的 $$b_l$$，为了书写方便，后面公式中都省略了下标 $$l$$，输入的消息中的下标 $$l,r$$  也都省略了。

为了计算 b 的概率，则传递的消息如图 4  所示。
![图4：极化码 BP 算法的消息传递，计算 b](/figure/极化码/bp/polar_bp_4.png)

*图4：极化码 BP 算法的消息传递，计算 b*

$$p(b=0)$$, $$b=0$$ 意味这需要 $$e = 0, d = 0$$, 则

$$
p(b=0) = p(e=0,d=0) = p(e=0) p(d=0)
\tag{7}
$$

同理，$$p(b=1)$$, $$b=1$$ 意味这需要 $$e = 1, d = 1$$, 则

$$
p(b=1) = p(e=1,d=1) = p(e=1) p(d=1)
\tag{8}
$$

结合公式 (7) 和 (8):

$$
LR(b) = \frac{p(b=1)}{p(b=0)} = \frac{p(e=1) p(d=1)}{p(e=0) p(d=0)} = LR(e)LR(d)
$$

从 b 的角度来看，此时 $$e = a \oplus c$$，则 $$e = 0$$  有两种情况：

$$a =0, c = 0$$

$$a = 1,c=1$$

则：

$$
p(e=0) = p(a=0,c=0) + p(a=1,c=1) = p(a=0)p(c=0) + p(a=1)p(c=1)
\tag{9}
$$

同理，$$e = 1$$  有两种情况：

$$a =0, c = 1$$

$$a = 1,c=0$$

则：

$$
p(e=1) = p(a=0,c=1) + p(a=1,c=0) = p(a=0)p(c=1) + p(a=1)p(c=0)
\tag{10}
$$

结合公式 (9) 和 (10) 可以推导出 LR(e):

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(a=0)p(c=1) + p(a=1)p(c=0)}{p(a=0)p(c=0) + p(a=1)p(c=1)}
$$

对上式，分子分母同时除以 $$p(a=0)p(c=0)$$，得到：

$$
LR(e) = \frac{\frac{p(c=1)}{p(c=0)} + \frac{p(a=1)}{p(a=0)}}{1+\frac{p(a=1)p(c=1)}{p(a=0)p(c=0)} } = \frac{LR(c)+LR(a)}{1+LR(a)LR(c)}
$$

接下来我们考虑 b, $$b =0$$ 需要满足 $$e = 0, d = 0$$, 则：

$$
p(b=0) = p(e=0,d=0) = p(e=0) p(d=0)
\tag{11}
$$

类似的, $$b =1$$ 需要满足 $$e = 1, d = 1$$, 则：

$$
p(b=1) = p(e=1,d=1) = p(e=1) p(d=1)
\tag{12}
$$

结合公式 (11) 和 (12) 有：

$$
LR(b) = \frac{p(b=1)}{p(b=0)} = \frac{p(e=1) p(d=1)}{p(e=0) p(d=0)} = LR(e)LR(d)
$$

### 计算 LR(c)

由于极化码的编码矩阵的逆矩阵就是其本身，或者从图上也可以看出， $$c = a+e$$ ，这与 $$a = c+e$$ 是类似的。
传递的消息如图 5  所示。
![图5：极化码 BP 算法的消息传递，计算 c](/figure/极化码/bp/polar_bp_5.png)

*图5：极化码 BP 算法的消息传递，计算 c*

因此，我们完全可以用推导 LR(a) 的步骤推导出 LR(c). 这里就不再详细推导了，把结果列出来：

$$
LR(c) = \frac{LR(a)+LR(e)}{1+LR(a)LR(e)}
$$

其中：

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(b=1)p(d=1)}{p(b=0)p(d=0)}  = LR(b)LR(d)
$$

### 计算 LR(d)
同理，可以按照计算 LR(b) 的方式，推导出 LR(d) 的计算公式.
传递的消息如图 6  所示。
![图6：极化码 BP 算法的消息传递，计算 d](/figure/极化码/bp/polar_bp_6.png)

*图6：极化码 BP 算法的消息传递，计算 d*

$$
LR(d) = \frac{p(d=1)}{p(d=0)} = \frac{p(e=1) p(b=1)}{p(e=0) p(b=0)} = LR(e)LR(b)
\tag{14}
$$

其中：

$$
LR(e) = \frac{LR(a)+LR(c)}{1+LR(a)LR(c)}
\tag{13}
$$

### 使用对数似然比 LLR 
对于以上各种情况下推导出来的公式，在工程中，一般使用对数似然比 Log Likelyhood Ratio ，所以，我们需要对以上几个推导的结果用 LLR 来进一步推导。

由于以上公式只有两种形式，所以，我们只需要对公式 (13) 和 (14) 进行进一步推导，然后就可以推广到其它公式上。

先推导一下公式(13).

LLR 的定义如下，其中 $$X$$ 是某个随机变量，取值 0 或者 1。

$$
LLR(X) = ln(LR(X))
\tag{15}
$$

所以

$$
LR(X) = e^{LLR(X)}
\tag{16}
$$

使用公式 (15) 和 (16) , 则公式(13) 的 LLR 形式为：

$$
\begin{aligned}
		LLR(e) &= ln \left (\frac{LR(a)+LR(c)}{1+LR(a)LR(c)} \right ) \\ \\
		&= ln \left (\frac{e^{LLR(a)}+e^{LLR(c)}}{1+e^{LLR(a)}e^{LLR(c)}} \right ) \\ \\
		&= ln \left (\frac{e^{LLR(a)}+e^{LLR(c)}}{1+e^{LLR(a)+LLR(c)}} \right )  
	\end{aligned}
\tag{18}
$$

这里为了方便书写，我们定义一个  box-plus, $$\boxplus$$ 运算 , 定义为：

$$
x \boxplus\ y = ln \left (  \frac{e^x + e^y}{ 1 + e^{x+y}}  \right )
\tag{17}
$$

使用公式 (17)，则公式 (18) 可以简写为:

$$
LLR(e) = LLR(a) \boxplus LLR(c)
$$

对于公式(13) ，则

$$
\text {LLR}(d) = \text {ln} (\text {LR}(e)\text {LR}(b)) = \text {ln} ( \text {LR}(e)) + \text {ln}(\text {LR}(b)) = \text {LLR}(e) + \text {LLR}(b)
$$

本主题后面的讨论，都是基于 LLR 形式的。


## N = 4 的 极化码 BP 译码过程描述

![图7：极化码 BP 算法的消息传递，N=4 的示例](/figure/极化码/bp/polar_bp_7.png)

*图7：极化码 BP 算法的消息传递，N=4 的示例*

现在，我们用一个 N = 4 的极化码来讨论具体的译码过程。如图7 所示。。

从图中我们可以看到，我们需要保存 $$1+log_2 N$$ 层的数据，在本例子中，需要保存 3 层。  图中括号里面 (2,3) 表示第二层的第三个数据位置。 我们用 $$L(2,3)$$ 表示在第 2 层第 3 个数据处，向左传递的消息。类似的，用 $$R(2,3)$$ 表示在第 2 层第 3 个数据处，向右传递的消息。编程时需要注意的是，例如第二层，我们可以把 标号放在靠右边，如图 8 所示。本文以标号在左边，即如图7 所示。

![图8：极化码 BP 算法的消息传递，N=4 的示例，标号都靠右](/figure/极化码/bp/polar_bp_8.png)

*图8：极化码 BP 算法的消息传递，N=4 的示例，标号都靠右*


第一层（即最左边一层）的，由于是原始的数据（有些是冻结比特 0），我们提供向右的初始概率信息。我们把其提供的概率信息初始化一下，如果是冻结比特，我们把其 LLR 初始化为无穷大，如果是数据比特，初始化为 0.  因为如果是冻结比特，则:

$$
\text {LLR}(f) = \text {ln} \left (   \frac{p(f=1)}{p(f=0)}  \right ) = \text {ln}(+\infty) = +\infty
$$

其中的无穷大，在编程可以用比较大的数来代替，例如 27  (注意，因为是 ln 之后的值，所以实际代表的值是 $$e^{27}$$).

数据比特，我们假定其取 1  和 0 的概率相等，因此，其 LLR 为 0.

所以：

$$
\text{L}(1,n) = \left\{\begin{matrix}
		27 &  \text{若是冻结比特}\\
		0 &    \text{若是数据比特}
	\end{matrix}\right.
$$

第三层（即做右边一层），由于是接收到的数据，根据信道和调制模式，可以计算出其对数似然比，本例子中，对$$\text{R}(3,1),\text{R}(3,2),\text{R}(3,3),\text{R}(3,4)$$ 进行初始化。

对其他所有的消息概率 R(x,x), L(x,x)， 都初始化为 0，表示没有任何信息可以给出对应比特的取值 0 或 1 的概率，因此假设其取值 0 或 1 的概率是相等的。


### 从右向左传递信息
从右向左，第三层信息不需要计算，已经根据信道和调制信息计算出来了。

计算第二层的概率：

$$
\begin{aligned}
		\text{L}(2,1) &= \text{L}(3,1) \boxplus [\text{R}(2,3) + \text{L}(3,2) ]  \\
		\text{L}(2,2) &= \text{L}(3,3) \boxplus [\text{R}(2,4) + \text{L}(3,4) ]  \\
		\text{L}(2,3) &= \text{L}(3,2) +  [\text{R}(2,1) \boxplus \text{L}(3,1)]  \\
		\text{L}(2,4) &= \text{L}(3,4) + [\text{R}(2,2) \boxplus \text{L}(3,3)] 
	\end{aligned}
$$

由于第一层向左的消息从来不用，可以不用计算.

### 从左向右传递信息
从左向右，第一层信息不需要计算，已经根据数据类型（数据比特和冻结比特）信息计算出来了。

计算第二层的概率：

$$
\begin{aligned}
		\text{R}(2,1) &= \text{R}(1,1) \boxplus [\text{R}(1,2) + \text{L}(2,2) ]  \\
		\text{R}(2,2) &= \text{R}(1,2)  + [\text{R}(1,1) \boxplus \text{L}(2,1)]   \\
		\text{R}(2,3) &= \text{R}(1,3)  \boxplus [\text{R}(1,4) + \text{L}(2,4) ] \\
		\text{R}(2,4) &= \text{R}(1,4) + [\text{R}(1,3) \boxplus \text{R}(2,3)] 
	\end{aligned}
$$

由于第三层向右的消息从来不用，可以不用计算.


## 同一个生成矩阵，不同的排列方式

前面的例子，是以一个排列方式为例子的。不同的排列方式，用 BP 译码，会得到不同的效果。

下面以不带置换的编码矩阵为例子，所谓的不带置换的，就是  $$F = \begin{pmatrix}   1&0 \\   1&1 \end{pmatrix}$$

使用 $$F$$ 矩阵，可以递推实现更多输入的极化码，对于码长为 $$N = 2^n$$ 的极化码，其生成矩阵 $$G_N$$ 为：

$$
G_N = F^{\otimes n}
$$

其中 $$F^{\otimes n}$$ 表示矩阵 $$F$$ 的 n 次 Kronecker 积。


为了后面画图的方便，我们把极化码的表示图做一点简化，从图 9  的左边的标准图，改画为右边的简化图.

![图9：N = 2 的简化画法](/figure/极化码/bp/polar_bp_N2_simplify.png)

*图9：N = 2 的简化画法*

### N=4 时的两种画法

N=4 时，不带置换的生成矩阵为 (19)：

$$
\begin{pmatrix}
		1 & 0 & 0 & 0\\
		1 & 1 & 0 & 0\\
		1 & 0 & 1 & 0\\
		1 & 1 & 1 & 1
	\end{pmatrix}
\tag{19}
$$

这里可以有多种画法，这些画法，如果是使用 SC（Successive Cancel） 译码算法，结果都是一样的，但是对于 BP 算法，则会有不同的结果（误码率不同）。

**第一种结构**，如图 10：注意看最后输出的数据，即最右边输出的。
![图10：N = 4 非置换的第一种结构的标准画法](/figure/极化码/bp/polar_code_core_N_is_4_standard_drawing_2_1.png)

*图10：N = 4 非置换的第一种结构的标准画法*

对应的简化算法如图 11

![图11：N = 4 非置换的第一种结构的简化画法](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_2_1.png)

*图11：N = 4 非置换的第一种结构的简化画法*

在用 BP 译码时，简化画法需要做一下反向，如图12
![图12：N = 4 非置换的第一种结构的简化画法：译码](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_2_1_reverse.png)

*图12：N = 4 非置换的第一种结构的简化画法：译码*

实际上图  10 和图 11 对应的极化码图都是图10。只是这种简化画法，让图形不对称了，那种标准画法，每个小的单元都是左右对称的。

**第二种结构**，如图 13：注意看最后输出的数据，即最右边输出的。
![图13：N = 4 非置换的第二种结构的标准画法](/figure/极化码/bp/polar_code_core_N_is_4_standard_drawing_1_2.png)

*图13：N = 4 非置换的第二种结构的标准画法*

对应的简化算法如图 14

![图14：N = 4 非置换的第二种结构的简化画法](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2.png)

*图14：N = 4 非置换的第二种结构的简化画法*

在用 BP 译码时，简化画法需要做一下反向，如图15
![图15：N = 4 非置换的第二种结构的简化画法：译码](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_reverse.png)

*图15：N = 4 非置换的第二种结构的简化画法：译码*

实际上图  13 和图 14 对应的极化码图都是图13。只是这种简化画法，让图形不对称了，那种标准画法，每个小的单元都是左右对称的。

**可以看到，两种结构，最右边的输出都是一样的。**


**编码和译码画在一起的简化画法**

上面第二种结构的简化画法，把编码和译码画在一起，如图 16 所示，从左到右是编码，从右到左是译码。

![图16：N = 4 非置换的第二种结构的简化画法：编译码](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_bidirection_together.png)

*图16：N = 4 非置换的第二种结构的简化画法：编译码*

这个正反向一起的，实际上可以看成图向左或者向右倾斜一点造成的，如图 17 上面的是编码的，下面的是译码的。

![图17：N = 4 非置换的第二种结构的变形至简化画法：编译码](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_tilt_to_get_encode_dec.png)

*图17：N = 4 非置换的第二种结构的变形至简化画法：编译码*


### N=8 时的多种结构

这里开始，就省略标准画法，只展示简化画法，而且因为我们讨论的是 BP 译码，所以，我们都画从译码角度看的简化画法。


**第一种结构**
如图 18,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图18：N = 8 的第一种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_124.png)

*图18：N = 8 的第一种结构*


**第二种结构**
如图 19,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图19：N = 8 的第二种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_142.png)

*图19：N = 8 的第二种结构*


**第三种结构**
如图 20,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图20：N = 8 的第三种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_214.png)

*图20：N = 8 的第三种结构*

**第四种结构**
如图 21,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图21：N = 8 的第四种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_241.png)

*图21：N = 8 的第四种结构*


**第五种结构**
如图 22,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图22：N = 8 的第五种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_412.png)

*图22：N = 8 的第五种结构*

**第六种结构**
如图 23,图中标注的绿色数字，是 $$u_x$$的简写，省略了 $$x$$.

![图23：N = 8 的第六种结构](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_421.png)

*图23：N = 8 的第六种结构*

**多种结构的性能差异**
以上以 N = 8 为例子，给出了 $$(\text{log}_2 N)! = (\text{log}_2 8)! = 3!=3*2*1=6$$ 种结构，基于每一种结构来做 BP 译码，得到的性能是不同的。 


### 其他画法
**显式画出变量节点和校验节点**

![图24：N = 8 显式画出变量节点和校验节点](/figure/极化码/bp/polar_code_core_N_is_8_variable_notes_check_notes.png)

*图24：N = 8 显式画出变量节点和校验节点*


图 24 摘自文献：
A. Elkelesh, M. Ebada, S. Cammerer and S. ten Brink, "Belief Propagation List Decoding of Polar Codes," in IEEE Communications Letters, vol. 22, no. 8, pp. 1536-1539, Aug. 2018, doi: 10.1109/LCOMM.2018.2850772. 


**正规图/Forney 因子图**


![图25：N = 8 正规图/Forney 因子图](/figure/极化码/bp/polar_code_core_N_is_8_Forney_Factor_Graph.png)

*图25：N = 8 正规图/Forney 因子图*


图 25 摘自书籍 
<5G 移动通信中的信道编码> 白宝明 孙韶辉 王加庆 著