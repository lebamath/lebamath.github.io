---
layout: default
title: "MIMO 非线性检测"
back_url: /index.html?lang=zh
---

# MIMO 非线性检测

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1062547)

## 基于马尔科夫随机场的置信传播算法
MIMO 检测(MIMO detection)，是根据接收天线接收到的数据，估计发射天线发送的数据。当然，这个问题的前提是信道系数已经知道。 我们假设信道是平坦衰落的，即发送的信号乘以一个复数增益，就是接收到的数据。

MIMO 检测有很多种方法，这个文档，我们想讲一下基于马尔科夫随机场(Markov Random Field， MRF) 的置信传播(Belief Propagration, BP)算法。

由于每个接收的天线都能接收到所有发射天线来的信号，因此，在接收到的每个信号，都可以给出每个发送符号的概率信息。因此，如果把每个发射信号都各看成一种需要估计的东西，那么这些估计之间是相互依赖的，这种依赖关系，是由接收天线得到的数据建立起来的。我们构造的马尔科夫随机场概率图模型中，是看不到接收到的信号的，是隐藏在后面的。例如，一个四根发射天线四根接收天线的系统如图一，对应的马尔科夫随机场如图二。


![图一 MIMO 系统示意图](/figure/MIMO检测/System.png)

*图一 MIMO 系统示意图*​																							


![图二  马尔科夫随机场概率图模型](/figure/MIMO检测/MRF.png)

*图二  马尔科夫随机场概率图模型*​	

### 非 LLR 形式的推导
我们来推导一下全概率公式，即全概率的后验概率

$$
p(x|y,H)
$$

其中 x 是发送数据的列向量， y 是接收数据的列向量， H 是 Nr x Nt 的信道系数矩阵。若 n 是高斯白噪声信号构成的列向量，则：

$$
y = Hx + n
$$

那么

$$
p(x|y,H) = \frac{p(x,y|H)}{p(y|H)} = \frac{1}{p(y|H)}p(x|H)p(y|x,H)  \quad ---公式(0)
$$

其中，因为 x 是与 H 无关的，且 x 各个元素的取值也是相互独立的，则：

$$
p(x|H) = p(x) = \prod_i p(x_i)
$$

现在来推导一下 $$p(y|x,H)$$ :

给定 x 和 H 后，y 是一个均值为 Hx（为什么？高手请指教一下），协方差为 $$\sigma^2 I_{N_r}$$ 的 复高斯随机向量。

$$
p(y|x,H) = \frac{1}{\sqrt{2 \pi} \sigma}  e^{-\frac{\left \|  y-Hx \right \| ^2}{ 2\sigma^2}}  \quad --- 公式(1)
$$

我们把 $$\left \|y-Hx\right \|^2$$ 展开:

$$
\begin{aligned}
	\left \|y-Hx\right \|^2 & = (y-Hx)^H(y-Hx) = (y^H - x^HH^H)(y-Hx)  \\
	&= y^H y - y^H Hx - x^H H^Hy + x^H H^H Hx   \\
	&= y^H y - (\textcolor{red}{ (x^H H^Hy)^H + x^H H^Hy }) +  x^H H^H Hx  \\
	&=y^H y - 2 \Re\{x^H H^Hy\} +  x^H H^H Hx  \quad --- 公式(2)
\end{aligned}
$$

其中 $$\Re$$ 表示取实部的意思。

将公式 (2) 代入 公式 (1):

$$
p(y|x,H) = \frac{1}{\sqrt{2 \pi} \sigma} 
e^{-\frac{y^H y}{2 \sigma^2}}
e^{\frac{\Re\{x^H H^Hy\} }{ \sigma^2}}
e^{-\frac{ x^H H^H Hx}{2 \sigma^2}}  \quad  --- 公式(3)
$$

因为接收信号 y 和噪声 都是已知的，因此，可以令：

$$
\beta = \frac{1}{\sqrt{2 \pi} \sigma} 
e^{-\frac{y^H y}{2 \sigma^2}}
$$

则可以把公式 (3) 简写为：

$$
p(y|x,H) = \beta   e^{\frac{\Re\{x^H H^Hy\} }{ \sigma^2}}
e^{-\frac{ x^H H^H Hx}{2 \sigma^2}}  \quad  --- 公式(4)
$$

根据矩阵乘法的一般推导公式（见附件），公式 (4) 可以展开为：

$$
p(y|x,H) = \beta'   (\prod_{i<j} e^{-x_i \Re(R_{ij})x_j})   ( \prod_i e^{x_i \Re(z_i)})  \quad --- 公式(5)
$$

其中

$$
R = \frac{1}{\sigma^2} H^H H  \\
z = \frac{1}{\sigma^2} H^H y
$$

另外需要注意，在矩阵展开时，又引入了一些常数，因此公式 (5) 中的常数与公式(4) 中的常数是不同的，因此，我加了一个撇号做标记，以示不同。

综合上面的推导，公式 (0) 可以写成：

$$
\begin{aligned}
	p(x|y,H) &=  \frac{1}{p(y|H)}p(x)  \beta'   (\prod_{i<j} e^{-x_i \Re(R_{ij})x_j})   ( \prod_i e^{x_i \Re(z_i)})  \\
	&=\beta''   (\prod_{i<j} e^{-x_i \Re(R_{ij})x_j})   ( \prod_i e^{x_i \Re(z_i)}) \prod_i p(x_i)  \quad ---公式(6)
\end{aligned}
$$

令：

$$
\begin{aligned}
	\psi_{i,j}(x_i,x_j)  &= e^{-x_i \Re(R_{ij}) x_j}  \\
	\phi_i(x_i) & = e^{x_i \Re(z_i) }p(x_i)
\end{aligned}
$$

根据马尔科夫随机场概率模型的相关定理（此处需要补充细节），从 i 到 j 的消息为：

$$
m^t_{i->j}(x_j) <---- \sum_{x_i} \phi_i(x_i) \psi_{i,j}(x_i,x_j)  \prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i)  \quad ---公式(7)
$$

公式(7) 中需要注意的是上标 t，这个表示迭代的轮次。第 t 轮的所有消息的计算，都是基于 t-1 轮的消息，要把所有消息都计算完毕之后， t-1 轮的消息才没有用了，t 轮的消息才变成 t+1 轮的输入。



则 最终关于变量 $$x_i$$ 的概率信息（置信度）正比于：

$$
b_i(x_i) \propto \phi_i(x_i) \prod_{k\in N(i) } m_{k->i}(x_i)                               \quad ---公式(8)
$$

通过比较  $$b_i(x_i=1)$$ 与 $$b_i(x_i=-1)$$ 的大小 ，可以对 $$x_i$$ 做判决。

### LLR 形式的推导

我看到的参考文献和书籍，基本上都是用上面这套公式的。在实践中，经常需要对数似然比的数据，给都下游环节做例如信道解码等工作。用对数似然比的公式，可以简化其中的一些步骤（例如归一化等），减少乘法的使用。

下面，我们从公式 (8) 出发，推导一个基于对数似然比的公式，这是很多教材和论文中没有的。

$$
\begin{aligned}
	LLR(b_i(x_i)) &= ln \frac{b_i(x_i=1)}{b_i(x_i=-1)} = ln \frac{ \phi_i(x_i=1) \prod_{k\in N(i) } m_{k->i}(x_i=1) }   { \phi_i(x_i=-1) \prod_{k\in N(i) } m_{k->i}(x_i=-1) }  \\
	&= ln \frac{ \phi_i(x_i=1)  }{ \phi_i(x_i=-1)}    + \sum_{k\in N(i)} ln \frac{m_{k->i}(x_i=1)}{m_{k->i}(x_i=-1)} \\
	&= LLR(\phi_i(x_i)) +  \sum_{k\in N(i)} LLR(m_{k->i}(x_i))                           \quad ---公式(9)
\end{aligned}
$$

我们继续来推导公式 (9) 中 $$LLR(m_{k->i}(x_i) ）$$ 这个似然比。注意下面的公式中把 k->i 换成了 i->j，没有实质影响，只是看的时候注意下标，表示的是从哪个节点到哪个节点的消息。

$$
\small
\begin{aligned}
	&LLR(m_{i->j}(x_j)) = ln\\
	&\frac 
	{    \phi_i(x_i=1) \psi_{i,j}(x_i=1,\textcolor{red}{x_j=1})  \prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i=1) 
		+  
		\phi_i(x_i=-1) \psi_{i,j}(x_i=-1,\textcolor{red}{x_j=1})  \prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i=-1)    
	}
	{    \phi_i(x_i=1) \psi_{i,j}(x_i=1,\textcolor{red}{x_j=-1})  \prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i=1) 
		+ 
		\phi_i(x_i=-1) \psi_{i,j}(x_i=-1,\textcolor{red}{x_j=-1})  \prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i=-1)    
	} 
\end{aligned}
\tag{10}
$$

上面公式 (10)，上下同时除以 $$\prod_{k\in N(i) \setminus j} m^{t-1}_{k->i}(x_i=-1)$$ 有：

$$
\begin{aligned}
	&LLR(m_{i->j}(x_j) ） =  ln \\
	&\frac 
	{    \phi_i(x_i=1) \psi_{i,j}(x_i=1,\textcolor{red}{x_j=1})  \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		\phi_i(x_i=-1) \psi_{i,j}(x_i=-1,\textcolor{red}{x_j=1})     
	}
	{    \phi_i(x_i=1) \psi_{i,j}(x_i=1,\textcolor{red}{x_j=-1})  \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		\phi_i(x_i=-1) \psi_{i,j}(x_i=-1,\textcolor{red}{x_j=-1})    
	}   \\
\end{aligned}
\tag{11}
$$

公式 (11) 中 ln 里面的分子和分母，同时除以 $$\phi_i(x_i=-1)$$

$$
\begin{aligned}
	LLR(m_{i->j}(x_j) ） =  	 
	ln\frac 
	{    \frac{\phi_i(x_i=1)}{ \phi_i(x_i=-1) } \psi_{i,j}(x_i=1,\textcolor{red}{x_j=1})  \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		\psi_{i,j}(x_i=-1,\textcolor{red}{x_j=1})     
	}
	{    \frac{\phi_i(x_i=1)}{ \phi_i(x_i=-1) } \psi_{i,j}(x_i=1,\textcolor{red}{x_j=-1})  \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		\psi_{i,j}(x_i=-1,\textcolor{red}{x_j=-1})    
	}   \\
\end{aligned}
\tag{12}
$$

根据 $$\phi_i(x_i)$$ 的定义，我们可以得到：

$$
\frac{\phi_i(x_i=1)}{ \phi_i(x_i=-1) }  = \frac{e^{(+1) \Re(z_i) }p(x_i=+1)}{e^{(-1) \Re(z_i) }p(x_i=-1)}
=e^{2 \Re(z_i)}     \frac{p(x_i=+1)}{p(x_i=-1)} =  e^{2 \Re(z_i)}   
\tag{13}
$$

公式 (13) 的推导中，假定了 $$p(x_i=+1) = p(x_i=-1) = 0.5$$

再把 $$\psi_{i,j}(x_i,x_j)$$ 的定义以及公式 (13) 代入公式(12) 有：

$$
\begin{aligned}
	LLR(m_{i->j}(x_j) ） &=  
	ln
	\frac 
	{    e^{2 \Re(z_i)} e^{-(+1) \Re(R_{ij}) (+1) }  \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		e^{-(-1) \Re(R_{ij}) (+1) }     
	}
	{    e^{2 \Re(z_i)} e^{-(+1) \Re(R_{ij}) (-1) }   \prod_{k\in N(i) \setminus j} \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} 
		\quad +  \quad
		e^{-(-1) \Re(R_{ij}) (-1) }  
	}   \\    \\
	&=
	ln
	\frac 
	{    e^{2 \Re(z_i)} e^{-(+1) \Re(R_{ij}) (+1) }   e^{\sum_{k\in N(i) \setminus j}  ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1) }} 
		\quad +  \quad
		e^{-(-1) \Re(R_{ij}) (+1) }     
	}
	{    e^{2 \Re(z_i)} e^{-(+1) \Re(R_{ij}) (-1) }   e^{\sum_{k\in N(i) \setminus j} ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} }
		\quad +  \quad
		e^{-(-1) \Re(R_{ij}) (-1) }  
	}   \\
	\\
	&=
	ln
	\frac 
	{    e^{2 \Re(z_i)- \Re(R_{ij}) + \sum_{k\in N(i) \setminus j}  ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1) }}
		\quad +  \quad
		e^{ \Re(R_{ij})  }     
	}
	{    e^{2 \Re(z_i)+\Re(R_{ij})+ \sum_{k\in N(i) \setminus j} ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} }
		\quad +  \quad
		e^{- \Re(R_{ij})  }  
	}   \\
	---公式(14)
\end{aligned}
$$

取公式(12) ln 里面分子部分的 $$2 \Re(z_i)-\Re(R_{ij})$$ 和 $$\Re(R_{ij})$$ 中最大的那个，记为 u  ( 意思是：max of numerator , 或者理解为 up);
取公式(12) ln 里面分母部分的 $$2 \Re(z_i)+\Re(R_{ij})$$ 和 $$-\Re(R_{ij})$$ 中最大的那个，记为  d（ 意思是 max of denominator， 或者理解为 down）

$$
\begin{aligned}
	&LLR(m_{i->j}(x_j) ） =\\
	&ln
	\frac 
	{    e^{2 \Re(z_i)- \Re(R_{ij}) -u+\sum_{k\in N(i) \setminus j}  ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1) }}
		+  
		e^{ \Re(R_{ij})   -u}     
	}
	{    e^{2 \Re(z_i)+ \Re(R_{ij}) - d+\sum_{k\in N(i) \setminus j} ln \frac{m^{t-1}_{k->i}(x_i=1) }{m^{t-1}_{k->i}(x_i=-1)} }
		+  
		e^{- \Re(R_{ij})  - d }  
	}  
	+
	ln 
	\frac{e^u}{e^d}  \\
	&= 
	ln
	\frac 
	{    e^{2 \Re(z_i) - \Re(R_{ij})  -u  + \sum_{k\in N(i) \setminus j}  LLR(m^{t-1}_{k->i}(x_i))} 
		+
		e^{ \Re(R_{ij})   -u}   
	}
	{    e^{2 \Re(z_i) + \Re(R_{ij})  - d   + \sum_{k\in N(i) \setminus j}LLR(m^{t-1}_{k->i}(x_i))}
		+  
		e^{- \Re(R_{ij})  - d }
	} + u-d
	\\
	&---公式(15)
\end{aligned}
$$

把公式 (15) 最终的消息更新机制的公式，列在下面：

$$
\begin{aligned}
	LLR^t(m_{i->j}(x_j) ） &=
	ln
	\frac 
	{    e^{2 \Re(z_i) -\Re(R_{ij})  -u  + \sum_{k\in N(i) \setminus j}  LLR(m^{t-1}_{k->i}(x_i))}
		+
		e^{ \Re(R_{ij})  -u}
	}
	{    e^{2 \Re(z_i) +1 \Re(R_{ij})  - d   + \sum_{k\in N(i) \setminus j}LLR(m^{t-1}_{k->i}(x_i))}
		+  
		e^{- \Re(R_{ij})  - d }
	}  
	+u-d
	\\
	---公式(15)
\end{aligned}
$$

这就是 LLR 形式的消息更新公式了。

### 引入函数节点





基于马尔科夫随机场的置信传播算法，我们也可以在马尔科夫随机场图模型的边上，引入一个函数节点（Function Nodes, FN），把原来的节点称为变量节点(Variable Nodes, VN), 如下图所示：

![MRF_with_FNs.png](/figure/MIMO检测/MRF_with_FNs.png)​	


按照如下图定义传递的消息：

![MRF_messaging_with_FNs.png](/figure/MIMO检测/MRF_messaging_with_FNs.png)​	

则变量节点发给函数节点的消息，可以理解为就是变量节点本身的置信度，因为是发给某条边的，因此，计算这个变量节点时，来自其目的地的边的消息，则不参与计算这个变量节点的置信度。

$$
\lambda_{i->k}(x_i) = \phi_i(x_i)\prod_{l\in N(i) \setminus k} \Lambda_{l->i}(x_i)
$$

其中 $$N(i)$$ 表示变量节点 $$i$$  的临边的集合. $$l\in N(i) \setminus k$$  表示去掉临边 $$k$$  上的函数节点 $$FN_k$$.

从函数节点到变量节点的消息：

$$
\Lambda_{k->j}(x_j) =\sum_{x_i}  \psi_{i,j}(x_i,x_j) \lambda_{i->k}(x_i), \quad i\in N(k)\setminus j
$$

稍微需要注意的是：因为每个函数节点只有两个相邻的变量节点，因此 $$i\in N(k)\setminus j$$ 中的 i 的取值就只有一种情况。



对于用 LLR 推导的公式，也可以定义两种传递的消息：

从变量节点 i 到函数节点 k 的消息：

$$
\lambda_{i->k}(x_i) = 2 \Re(z_i) + \sum_{l\in N(i) \setminus k}  \Lambda_{l->i}(x_i))
$$

从函数节点 k 到变量节点 j 的消息：

$$
\Lambda_{k->j}(x_j)=
ln
\frac 
{    e^{ - \Re(R_{ij})   + \lambda_{i->k}(x_i)} 
	+
	e^{ \Re(R_{ij})  }   
}
{    e^{ \Re(R_{ij})   + \lambda_{i->k}(x_i)}
	+  
	e^{- \Re(R_{ij})  }
} , \quad i\in N(k)\setminus j
$$

稍微需要注意的是：因为每个函数节点只有两个相邻的变量节点，因此 $$i\in N(k)\setminus j$$ 中的 i 的取值就只有一种情况。



代码一（非 LLR 形式）在 gitnub 中.


代码二 (LLR形式的)  在 gitnub 中.

### 附录一：矩阵公式的推导

在文中从公式(4) 到公式(5)，这里面有点跳跃，现在在这里详细证明一下。

$$
x^H H^H Hx
$$

首先，令 $$R = H^H H$$， 则我们需要分析的矩阵乘法是： $$x^H R x$$

我们先计算后两个相乘的部分：

$$
Rx = 
\begin{bmatrix}
	\sum_{j=1}^{Nt} R_{1j} x_j\\
	\sum_{j=1}^{Nt} R_{2j} x_j\\
	...\\
	\sum_{j=1}^{Nt} R_{N_tj} x_j
\end{bmatrix}
$$

则

$$
x^H Rx = x^H \begin{bmatrix}
	\sum_{j=1}^{N_t} R_{1j} x_j\\
	\sum_{j=1}^{N_t} R_{2j} x_j\\
	...\\
	\sum_{j=1}^{N_t} R_{N_tj} x_j
\end{bmatrix} = 
[x_1^*, x_2^*,...,x_{N_t}^*]
\begin{bmatrix}
	\sum_{j=1}^{N_t} R_{1j} x_j\\
	\sum_{j=1}^{N_t} R_{2j} x_j\\
	...\\
	\sum_{j=1}^{N_t} R_{N_tj} x_j
\end{bmatrix}
=
\sum_{i=1}^{N_t} x_i^* (\sum_{j=1}^{N_t} R_{ij}x_j) = \sum_{i=1,j=1}^{N_t,N_t} x_i^* R_{ij} x_j
$$

信道系数矩阵 H 的自相关矩阵 R 中，主对角线上的元素都是实数，没有虚部。

又由于我们假定是 BPSK，只有 +1 和 -1 两种取值，所以在上面的求和公式中，$$i=j$$ 的部分可以单独拿出来 $$( (-1)*(-1) =1, \quad 1*1 =1$$：

$$
\sum_{i=1,j=1}^{N_t,N_t} x_i^* R_{ij} x_j = \sum_{i=1}^{N_t} x_i^* R_{ii} x_i = \sum_{i=1}^{N_t} R_{ii}
$$

这是与 $$x_i$$ 无关的常量。



另外，当 $$i \neq j$$ 时，因为 R 矩阵有共轭转置就是其自身的特性，所以：

$$
\begin{aligned}
	\sum_{i=1,j=1}^{N_t,N_t} x_i^* R_{ij} x_j &= \sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i\neq j}^{N_t,N_t} x_i^* R_{ij} x_j  \\
	&= \sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i<j}^{N_t,N_t} (x_i^* R_{ij} x_j + x_j^* R_{ji} x_i) \\
	&= \sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i<j}^{N_t,N_t} (x_i^* R_{ij} x_j + (x_i^* R_{ji}^{\color{red}{*}} x_j)^*) \\
	&= \sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i<j}^{N_t,N_t} (x_i^* R_{ij} x_j + (x_i^* R_{ij} x_j)^*) \\
	&=\sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i<j}^{N_t,N_t} 2\Re(x_i^* R_{ij} x_j) \\
	&=\sum_{i=1}^{N_t} R_{ii} + \sum_{i=1,j=1, i<j}^{N_t,N_t} 2\Re(x_i R_{ij} x_j) \\
\end{aligned}
$$

###  附录二： LLR 形式下的 damping 公式



在非 LLR 形式下，消息 $$m_{ij}$$ 的 damping 非常直观：

$$
m^t_{ij} = \alpha m_{ij}^{old} + (1-\alpha) m_{ij}^{new}
$$

那对于 LLR 模式，因为 $$m_{ij}$$ 公式为：

$$
LLR(m_{i->j}(x_j))  =  ln \frac{m_{i->j}(x_j=1)}{m_{i->j}(x_j=-1)}
$$

则（做了一些简写，应该是很直观可以明白的）：

$$
LLRm_{ij} = ln \frac{m_{ij}(x_j=1)}{ 1- m_{ij}(x_j=1)}
$$

则：

$$
m_{ij}(x_j=1) = \frac{1}{e^{-LLRm_{ij}} + 1}  \quad ---公式(a)
$$

所以：

$$
\begin{aligned}
	LLRm^t_{ij} &= log\frac{\alpha m^{old} + (1-\alpha) m^{new}}
	{\alpha (1-m^{old}) + (1-\alpha) m^{new}}  \\ \quad \\
	&=  log(\alpha m^{old} + (1-\alpha) m^{new})  - log(\alpha (1-m^{old}) + (1-\alpha) m^{new})
\end{aligned}
$$

将公式(a)  代入上式后即可。


## 基于因子图的加权高斯近似算法
本文讲解基于因子图的置信传播算法，来做 MIMO detection, 即根据接收到的数据，假定信道系数矩阵已知的前提下，来估计发送的数据。这个文章需要的背景知识很少，只需要基本的概率知识以及高斯分布就可以了。

系统图如下：

![System.png](/figure/MIMO检测/System.png)​	


则：

$$
y = Hx + n
$$

信道系数矩阵 H 已知，且已经接收到了数据，那么如果我们要估算发送方的数据，当然最优的做法，是求解下面的概率：

$$
p(x|y,H)   \quad ---- 公式(1)
$$

其中 x 和 y 都是列向量，分别包含 $$N_t$$ 和 $$N_r$$ 个元素。 $$N_t$$ 和 $$N_r$$ 分别表示发送天线数和接收天线数.

在所有 x 的可能取值中，找上面公式 (1) 的概率的最大值。

但是，这种最大化后验概率的方法，计算量随着发送天线数的增加而急剧增大，因此，我们可以退而求其次，我们不要求全局最优，我们把$$N_t$$个发送数据分别处理，对于$$x_i$$，我们计算如下的概率：

$$
p^{k+} = p(x_k = +1 | y,H) \quad ---- 公式(2)
$$

如果大于 0.5，则认为 $$x_k = +1$$，否则，认为 $$x_k = -1$$.

我们把公式 (2) 用条件概率公式做一下推导，目的是推导出 用“收到 y”  概率 来表示这个 $$x_k$$ 的概率。

$$
\begin{aligned}
	p^{k+} &= p(x_k = +1 | y,H) =\frac{p(x_k=+1,y|H)}{p(y|H)} = \frac{p(y|x_k=+1,H) p(x_k=+1|H)}{p(y|H)}  \\
	&= \frac{1}{p(y|H)}   p(x_k=+1|H)   p(y|x_k=+1,H)
	\quad ---- 公式(3)
\end{aligned}
$$

其中，因为 $$x_k$$ 与信道 H 是相互独立的，因此 $$p(x_k=+1|H)  = p(x_k=+1)$$，可以认为是常数。
其中 $$p(y|H)$$ 用全概率公式展开为

$$
p(y|H) = p(y|x_k=+1,H)p(x_k=+1|H)  + p(y|x_k=-1,H)p(x_k=-1|H)
$$

因为 $$x_k$$ 与信道 H 是相互独立的，所以，上式继续推导为：

$$
\begin{aligned}
	p(y|H) &= p(y|x_k=+1,H)p(x_k=+1|H)  + p(y|x_k=-1,H)p(x_k=-1|H)   \\
	&= p(y|x_k=+1,H)p(x_k=+1)  + p(y|x_k=-1,H)p(x_k=-1)
\end{aligned}
$$

在假定  $$p(x_k=+1)  = p(x_k=-1) =0.5$$，即符号是等概率取值的，则公式 (3) 可以整理为：

$$
\begin{aligned}
	p^{k+} &= p(x_k=+1) \frac{p(y|x_k=+1,H)}{ p(y|x_k=+1,H)p(x_k=+1)  + p(y|x_k=-1,H)p(x_k=-1)}  \\
	&= \frac{p(y|x_k=+1,H)}{ p(y|x_k=+1,H)  + p(y|x_k=-1,H)}  
\end{aligned} \quad ---- 公式(4)
$$

至此，我们做一个不太准确的假设，即假设 $$y_1,y_2,....,y_{N_r}$$ 在 已知 H 和 $$x_k$$ 的条件下，相互独立。但是，在实际上，这里肯定不是相互独立的，因为每个接收天线都能接收到所有发射天线来的信号，那么这些接收到的数据肯定都包括相互重叠的信息，即来自同一个发射天线的信息。所以，下面的公式，只能是约等于：

$$
p(y|x_k=+1,H) \approx  \prod_{i=1}^{N_r}  p(y_i|x_k=+1,H)  \\
p(y|x_k=-1,H) \approx  \prod_{i=1}^{N_r}  p(y_i|x_k=-1,H)
$$

代入公式 (4) 有：

$$
p^{k+} = \frac{ \prod_{i=1}^{N_r}\frac{p(y_i|x_k=+1,H)}{p(y_i|x_k=-1,H)}  }   
{  \prod_{i=1}^{N_r}\frac{p(y_i|x_k=+1,H)}{p(y_i|x_k=-1,H)}                   +
	1}  \quad ----- 公式(5)
$$

令：

$$
\Lambda_i^k = log \frac{p(y_i|x_k=+1,H)}{p(y_i|x_k=-1,H)}   \quad ---- 公式 (6)
$$

那么：

$$
\prod_{i=1}^{N_r}\frac{p(y_i|x_k=+1,H)}{p(y_i|x_k=-1,H)} = exp({\sum_{i=1}^{N_r} \Lambda_i^k})
$$

代入公式 (5) 有：

$$
p^{k+} =\frac{exp({\sum_{i=1}^{N_r} \Lambda_i^k})}
{exp({\sum_{i=1}^{N_r} \Lambda_i^k}) + 1}  \quad ---- 公式 (7)
$$

至此，我们已经用 $$y_i$$ 的概率，表示出来了 $$x_k$$ 的概率，即用接收方的概率信息，来估计发送方发送的是什么数据的概率。

接下来，我们需要更新了的对发送方的估计，来进一步提高对 $$y_i$$ 的概率的估计，即提高 $$\Lambda_i^k$$ 的准确度。看公式 (6) 中的 $$y_i$$，我们把 $$y_i$$ 的公式写出来：

$$
y_i = \sum_{j=1}^{N_t} h_{ij}x_j + n_i = h_{ik}x_k +  \underbrace{\sum_{j=1,j\neq k}^{N_t} h_{ij}x_j}_{干扰} + n_i  \quad  ---- 公式(8)
$$

这里，我们把来自不是 $$x_i$$ 的发送信号，都视作干扰，这个干扰以及加性高斯白噪声项一起，构成了一个符合复高斯分布的随机变量 $$z_{ik}$$：

$$
y_i = \sum_{j=1}^{N_t} h_{ij}x_j + n_i = h_{ik}x_k +  \underbrace{\sum_{j=1,j\neq k}^{N_t} h_{ij}x_j + n_i }_{z_{ik}} \quad  ---- 公式(9)
$$

符合如下的复高斯分布：

$$
\begin{aligned}
	& CN(\mu_{z_{ik}}, \sigma^2_{z_{ik}})  \\ \quad\\
	\mu_{z_{ik}} &= \sum_{j=1,j \neq k}^{N_t}  h_{ij} E(x_j)   \\ \quad\\
	\sigma^2_{z_{ik}} &= \sum_{j=1,j \neq k}^{N_t}  |h_{ij}|^2 \text{Var}(x_j) + \sigma^2
\end{aligned}
$$

那么根据公式 (9) 和上面的假设，则  $$y_i$$ 是符合 $$CN(\mu_{z_{ik}}+h_{ik}x_i, \sigma^2_{z_{ik}})$$ 的复高斯分布。
那么：

$$
p(y_i|x_k=+1,H) = \frac{1}{\sqrt{\pi} \sigma_{ik}} exp( - \frac{|y_i -(\mu_{z_{ik}}+h_{ik}(+1))|^2 }{\sigma^2_{z_{ik}}} )
$$

类似的：

$$
p(y_i|x_k=-1,H) = \frac{1}{\sqrt{\pi} \sigma_{ik}} exp( - \frac{|y_i -(\mu_{z_{ik}}+h_{ik}(-1)|^2 }{\sigma^2_{z_{ik}}} )
$$

代入公式 (6) 有：

$$
\begin{aligned}
	\Lambda_i^k  &= \frac{|y_i -(\mu_{z_{ik}}+h_{ik}(-1)|^2}{\sigma^2_{z_{ik}}} -
	\frac{|y_i -(\mu_{z_{ik}}+h_{ik}(+1))|^2}{{\sigma^2_{z_{ik}}} }  \\ \quad  \\
	&= \frac{|y_i -\mu_{z_{ik}}+h_{ik}|^2 - |y_i -\mu_{z_{ik}}-h_{ik}|^2}    
	{\sigma^2_{z_{ik}}}    \\ \quad  \\
\end{aligned}   \quad ---- 公式 (10)
$$

其中：

$$
\begin{aligned}
	|y_i -\mu_{z_{ik}}+h_{ik}|^2 &= (y_i -\mu_{z_{ik}}+h_{ik}) (y_i -\mu_{z_{ik}}+h_{ik})^*  \\
	&= ((y_i -\mu_{z_{ik}})+h_{ik})((y_i -\mu_{z_{ik}})^*+h_{ik}^*)  \\
	&= |y_i -\mu_{z_{ik}}|^2 + |h_{ik}|^2 +  (y_i -\mu_{z_{ik}}) h_{ik}^* + (y_i -\mu_{z_{ik}})^* h_{ik}  \\
	&=|y_i -\mu_{z_{ik}}|^2 + |h_{ik}|^2 +  2 \Re ( (y_i -\mu_{z_{ik}}) h_{ik}^*)
\end{aligned}
$$

同理：

$$
|y_i -\mu_{z_{ik}}-h_{ik}|^2 = |y_i -\mu_{z_{ik}}|^2 + |h_{ik}|^2 -  2 \Re ( (y_i -\mu_{z_{ik}}) h_{ik}^*)
$$

代入公式 (10) 有：

$$
\Lambda_i^k = \frac{4}{  {\sigma^2_{z_{ik}}}  }    \Re ( (y_i -\mu_{z_{ik}}) h_{ik}^*)  \quad ---- 公式 (11)
$$

现在，我们来推导公式 (11) 中用到的两个参数 $$\mu_{z_{ik}}$$ 和  $$\sigma^2_{z_{ik}}$$ :

$$
\mu_{z_{ik}} = \sum_{j=1,j \neq k}^{N_t}  h_{ij} E(x_j)
$$

其中

$$
E(x_j)  = (x_j=+1) p(x_j=+1) + (x_j=-1) p(x_j=-1) = p^{j+} (-1) ( 1-p^{j+}) = 2 p^{j+} - 1
$$

则：

$$
\mu_{z_{ik}} = \sum_{j=1,j \neq k}^{N_t}  h_{ij} ( 2 p^{j+} - 1 )
$$

另外，方差的部分：

$$
\sigma^2_{z_{ik}} = \sum_{j=1,j \neq k}^{N_t}  |h_{ij}|^2 \text{Var}(x_j) + \sigma^2
$$

其中：

$$
\begin{aligned}
	\text{Var}(x_j)  &= E(x_j^2) - (E(x_j))^2 = ( 1*1*p^{j+} + (-1)(-1)(1-p^{j+})) - (2 p^{j+} - 1)^2  \\
	&= 4 p^{j+} ( 1- p^{j+})
\end{aligned}
$$

则：

$$
\sigma^2_{z_{ik}} = \sum_{j=1,j \neq k}^{N_t}  |h_{ij}|^2   4p^{j+}(1-p^{j+}) + \sigma^2
$$

最终，公式 (11) 变为：

$$
\Lambda_i^k = 
\frac{4}{  \sum_{j=1,j \neq k}^{N_t}  |h_{ij}|^2   4p^{j+}(1-p^{j+}) + \sigma^2  }   
\Re ( (y_i - (  \sum_{j=1,j \neq k}^{N_t}  h_{ij} ( 2 p^{j+} - 1 ) ) h_{ik}^*)  \quad ---- 公式 (12)
$$

至此，我们已经有一个迭代的过程了：
1）用 $$y_i$$ 的概率信息 $$\Lambda_i^k$$ 来估算每个发送方数据的概率信息 $$p^{j+}$$
2） 根据发送方概率信息 $$p^{j+}$$，可以计算出相关的均值和方差 $$\mu_{z_{ik}}$$ 和 $$\sigma^2_{z_{ik}}$$, 进而可以又来估计 $$y_i$$ 的概率。

因为我们这中间有一些假设导致的一种近似，所以，我们需要对上面两个步骤做多次迭代，才能收敛到一个稳定值。因为是迭代，所以，在后面的迭代过程中，公式(7) 中，计算左边的值时，需要把我们用来估计的 $$y_i$$ 对应的概率踢出去，下面的公式中 $$l$$ 表示要估计的 y 向量中元素的下标（而不是 i ）, 公式 (7) 变为：

$$
p^{k+}_l =\frac{exp({\sum_{i=1, i\neq l}^{N_r} \Lambda_i^k})}
{exp({\sum_{i=1,i\neq l}^{N_r} \Lambda_i^k}) + 1}  \quad ---- 公式 (13)
$$

则公式(12) 和公式 (13) 一起，构成这个算法的迭代过程。

至此，我们引入因子图来表示这种迭代关系以及迭代过程中传递的概率信息（称之为消息）。

![FG.png](/figure/MIMO检测/FG.png)​	

![FG_part.png](/figure/MIMO检测/FG_part.png)​	

### 算法和代码


MIMO检测：基于因子图高斯近似的置信传播算法 \\
初始化\\
1.  $$\Lambda_i^k=0, p_i^{k+}=0.5, s_{\Lambda^k}=0, \mu_{z_{ik}}=\sigma^2_{z_{ik}}=s_{\mu_{z_{i}}}=0, s_{\sigma^2_{z_i}}=0, \forall i=1,...,n_r, k=1,...,n_t$$\\
2. 从 t=1 到 num of iter\\
观察节点的 LLRs 计算\\
1）. 从 $$i=1 \quad to\quad n_r$$\\
\indent a. $$s_{u_{z_i}}=\sum_{j=1}^{n_t} h_{ij}(2p_i^{j+}-1)$$ \\
\indent b. $$s_{\sigma^2_{z_i}}=4\sum_{j=1}^{n_t} |h_{ij}|^2 p_i^{j+}(1-p_i^{j+})$$ \\
\indent c. 从 $$k=1 \quad to \quad n_t$$\\
\indent\indent c.1. $$u_{z_{ik}} = s_{u_{z_i}} - h_{ik}(2p_i^{k+}-1)$$\\
\indent\indent c.2. $$\sigma_{z_{ik}}^2 = s_{\sigma^2_{z_i}} - 4|h_{ik}|^2 p_i^{k+}(1-p_i^{k+}) + \sigma^2$$\\
\indent\indent c.3. $$\Lambda_i^k = \frac{4}{\sigma^2_{z_{ik}}} \Re(h^*_{ik}(r_i-\mu_{z_{ik}}))$$\\
\indent d. 终止循环 \\
2). 终止循环 \\
计算变量节点的概率  \\
3). 从 $$k=1 \quad to \quad n_t$$ \\
\indent a) $$s_{\Lambda^k} = \sum_{l=1}^{n_r}\Lambda_l^k$$ \\
\indent b) 从 $$i=1 \quad to \quad n_r$$ \\
\indent \indent b.1) $$p_i^{k+}=\frac{exp(s_{\Lambda^k}-\Lambda_i^k)}{1+exp(s_{\Lambda^k}-\Lambda_i^k)}$$ \\
\indent c) 终止循环 \\
4) 终止循环 \\
3. 终止循环, num\_iter 的循环