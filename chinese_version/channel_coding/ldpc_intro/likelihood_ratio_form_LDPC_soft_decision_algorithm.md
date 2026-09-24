---
layout: default
title: " LDPC 软判决算法之似然比形式"
back_url: /index.html?lang=zh
---
## LDPC 软判决算法之似然比形式 (一)

在前面的文章中，我们推导了 LDPC 软判决译码的迭代算法，这篇文章，我们用对数比的形式，再推导一下 LDPC 的迭代算法。

本文参考了文献[1] 的 15.5.6 章节。

为了判决各个发送比特，我们可以用后验概率比值取对数来评估，即：

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)}  \quad  ------ \quad  公式(1)
$$

如果这个比值大于 0 ， 则把 比特 n 判决为 1，否则判决为 0.
下面我们来推导上面公式的递推表达式。

我们先对公式(1)中的分子进行推导：

$$
\begin{aligned}
p(c_n=1|r) = p(c_n=1|r_n, \{r_i,i \neq n\}) \\
=\frac{  p(c_n = 1,  r_n |\{r_i,i \neq n\} ) }  { p(r_n | \{r_i,i \neq n\} ) }  \\
=\frac{  p( r_n | c_n = 1, \{r_i,i \neq n\} ) p(c_n=1|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) } \\
=\frac{  p( r_n | c_n = 1 ) p(c_n=1|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }
\end{aligned}
$$

其中用到了：

$$
p( r_n | c_n = 1, \{r_i,i \neq n\} ) =  p( r_n | c_n = 1 )
$$

同理，对公式(1)中的分母部分进行推导：

$$
\begin{aligned}
p(c_n=0|r) = p(c_n=0|r_n, \{r_i,i \neq n\}) \\
=\frac{  p(c_n = 0,  r_n |\{r_i,i \neq n\} ) }  { p(r_n | \{r_i,i \neq n\} ) }  \\
=\frac{  p( r_n | c_n = 0, \{r_i,i \neq n\} ) p(c_n=0|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }  \\
=\frac{  p( r_n | c_n = 0) p(c_n=0|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }  \\
\end{aligned}
$$

则公式 (1) 可以 推导为

$$
\begin{aligned}
\lambda(c_n|r) = log \frac
{ p( r_n | c_n = 1) p(c_n=1|\{r_i,i \neq n\} }
{ p( r_n | c_n = 0) p(c_n=0|\{r_i,i \neq n\} }  \\
=log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)}  +
log \frac{p(c_n=1|\{r_i,i \neq n\}}{p(c_n=0|\{r_i,i \neq n\} }
\end{aligned}
$$

其中

$$
log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)}
$$

这一部分是可以根据信道的特点（例如加性高斯白噪声信道）以及调制方式，可以容易计算出来。如果是 BPSK(1-->1, 0-->-1) 调制，经过加性高斯白噪声信道，则

$$
log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)} = log \frac{exp(-(r_n-1)^2/(2\sigma^2))}{exp(-(r_n+1)^2/(2\sigma^2))} = \frac{2}{\sigma^2}r_n
$$

而第二部分

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) }
$$

把校验方程的约束，考虑进来，对于 $$c_n=1$$ 的情况，$$c_n$$ 参与的那些校验方程要想成立，则每个校验方程中，除了 $$c_n$$ 这个比特外，其余的比特加起来应该等于 1；同理，对于 $$c_n=0$$ 的情况，$$c_n$$ 参与的那些校验方程要想成立，则每个校验方程中，除了 $$c_n$$ 这个比特外，其余的比特加起来应该等于 0. 为了行文的方便，我们引入一个记号：

$$
z_{m,n} = \sum_{i \in N_{m,n} } c_i
$$

那么 $$c_n=1$$，就可以表示为 $$c_n$$ 参与的那些校验方程，每个校验方程里面的其它比特之和为 1，即 $$\{z_{m,n}=1\}$$； 同理，$$c_n=0$$，就可以表示为 $$c_n$$ 参与的那些校验方程，每个校验方程里面的其它比特之和为 0，即 $$\{z_{m,n}=0\}$$，那么：

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = log \frac
{p(\{z_{m,n}=1\}|\{r_i,i \neq n\} )}
{p(\{z_{m,n}=0\}|\{r_i,i \neq n\} )}\quad ---- \quad 公式(2)
$$

这里需要做一个假设近似，即 $$c_n$$ 参与的校验方程，相互之间独立，即这些校验方程中，除了有共同的比特 $$c_n$$ 外，其它比特没有相同的。当然，这个假设在一般情况下都是不成立的，这里假定成立，或者近似成立（只有相同的比特数不是很多），在这个假设情况下，上面公式右边的分子和分母部分，又可以拆成多个概率的乘积：

$$
p(\{z_{m,n}=1\}|\{r_i,i \neq n\} ) = \prod_m p(z_{m,n}=1|\{r_i,i \neq n\})
$$

同理，

$$
p(\{z_{m,n}=0\}|\{r_i,i \neq n\} ) = \prod_m p(z_{m,n}=0|\{r_i,i \neq n\})
$$

则公式 (2) 变成：

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = log \prod_m \frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
= 
\sum_m log\frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
$$

根据公式 (1),

$$
log\frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
$$

可以记为 $$\lambda(z_{m,n}\vert \{r_i,i \neq n\})$$

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = \sum_m \lambda(z_{m,n}| \{r_i,i \neq n\})
$$

则把上面的推导的结果，代入公式 (1)，我们可以看到：

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \lambda(z_{m,n}| \{r_i,i \neq n\})\quad ----\quad 公式(3)
$$

这里面的思想，比特 $$c_n$$ 的对数比 $$\lambda(c_n)$$，可以用校验方程成立的某种比值（在 $$c_n=1$$ 和 $$c_n=0$$ 两个条件下，校验方程成立的概率的比值）来表示。即，校验方程成立的概率，可以用来估算比特取值的概率。我们举个例子来进一步理解上面的结果。

假设 LDPC 用的校验矩阵如下，本篇文章都是以这个校验矩阵为例子。

$$
A = \begin{bmatrix}
	1& 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1\\
	1& 0 & 1 & 0 & 1 & 1 & 0 & 1 & 1 & 0\\
	0& 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1 & 1\\
	0& 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1\\
	1& 1 & 0 & 1 & 0 & 0 & 1 & 1 & 1 & 0
\end{bmatrix}
$$

我们译码出来的码字表示成向量形式：

$$
c=\begin{bmatrix}
	c_1 & c_2 & c_3 & c_4 & c_5 & c_6 & c_7  & c_8 & c_9 & c_{10}
\end{bmatrix}
$$

这个译码出来的码字，不是指发送的正确的码字，是表示我们待译码出来的，例如，我们可能想评估  $$c_1=1$$ 的可能性（概率）。

我们把接收到的数据表示为一个向量：

$$
r=\begin{bmatrix}
	r_1 & r_2 & r_3 & r_4 & r_5 & r_6 & r_7  & r_8 & r_9 & r_{10}
\end{bmatrix}
$$

根据线性分组码相关的原理，我们知道有如下的校验方程：

$$
z = c A^T
$$

计算后的结果$$z$$，是一个含有5个元素的向量，记为：

$$
z = \begin{bmatrix}
	z_1& z_2 & z_3 & z_4 & z_5 
\end{bmatrix}
$$

为了易于理解，我们把上面这个矩阵形式的方程，展开成 5 个校验方程：

$$
\begin{aligned}
	z_1 &= c_1 + c_2 + c_3 + c_6 + c_7 + c_{10} \\
	z_2 &= c_1 + c_3 + c_5 + c_6 + c_8 + c_{9} \\
	z_3 &= c_3 + c_4 + c_5 + c_7 + c_9 + c_{10} \\
	z_4 &= c_2 + c_4 + c_5 + c_6 + c_8 + c_{10} \\
	z_5 &= c_1 + c_2 + c_4 + c_7 + c_8 + c_{9}  
\end{aligned}
$$



咱们考虑正在译码 $$c_2$$ 这个比特，我们需要计算如下这个概率比值的对数：

$$
\lambda(c_2|r) = log\frac{p(c_2=1|r)}{p(c_2=0|r)}= log\frac{p(c_2=1|r_2,r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}{p(c_2=0|r_2,r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
$$

根据公式 (3)：

$$
\lambda(c_2|r) = log\frac{p(c_2=1|r)}{p(c_2=0|r)}=\frac{2}{\sigma^2}r_2 +  \sum_m \lambda(z_{m,2}| \{r_i,i \neq 2\})
$$

$$c_2$$ 参与的校验方程有 $$z_1,z_4,z_5$$  这三个，所以

$$
\begin{aligned}
\sum_{m} \lambda(z_{m,2}| \{r_i,i \neq 2\}) = \sum_{m \in\{1,4,5\}} \lambda(z_{m,2}| \{r_i,i \neq 2\}) \\
= \lambda(z_{1,2}| \{r_i,i \neq 2\}) + \lambda(z_{4,2}| \{r_i,i \neq 2\}) + \lambda(z_{5,2}| \{r_i,i \neq 2\})
\end{aligned}
$$

因为第一个校验方程的比特有1,2,3,6,7,10，去掉比特 2 后还有 1,3,6,7,10，所以

$$
\begin{aligned}
\lambda(z_{1,2}| \{r_i,i \neq 2\}) = \lambda(c_1+c_3+c_6+c_7+c_{10}|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10}) \\
= log \frac
{p(c_1+c_3+c_6+c_7+c_{10}=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
{p(c_1+c_3+c_6+c_7+c_{10}=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
\end{aligned}
$$

同理：

$$
\begin{aligned}
\lambda(z_{4,2}| \{r_i,i \neq 2\}) = log \frac
{p(c_4+c_5+c_6+c_8+c_{10}=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
{p(c_4+c_5+c_6+c_8+c_{10}=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}   \\
\lambda(z_{5,2}| \{r_i,i \neq 2\}) = log \frac
{p(c_1+c_4+c_7+c_8+c_9=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
{p(c_1+c_4+c_7+c_8+c_9=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}   
\end{aligned}
$$

从例子中，也可以看出来有用校验方程成立的概率，来估算待译码比特的概率比值的对数，从而可以用来判决这个待译码的比特。

至此，我们第一阶段的推导已经结束，即用校验方程的某种概率来估计待译码比特的某种概率。

**第二阶段**

我们来看公式 (3) 中右边求和公式里面的表达式 

$$
\lambda(z_{m,n}| \{r_i,i \neq n\})
$$

根据 $$\lambda$$ 函数的定义，写成概率比值的对数形式：

$$
\lambda(z_{m,n}| \{r_i,i \neq n\}) = log \frac  {p(z_{m,n}=1| \{r_i,i \neq n\})}   {p(z_{m,n}=0| \{r_i,i \neq n\})}
$$

再把 $$z_{m,n}$$ 的定义代进去：

$$
\begin{aligned}
\lambda(z_{m,n}| \{r_i,i \neq n\}) = log \frac  {p(z_{m,n}=1| \{r_i,i \neq n\})}   {p(z_{m,n}=0| \{r_i,i \neq n\})} \\
=log \frac  {p((\sum_{i \in N_{m,n} } c_i)=1| \{r_i,i \neq n\})}   {p((\sum_{i \in N_{m,n} } c_i)=0| \{r_i,i \neq n\})}
\end{aligned}
$$

这是其具体含义。我们直接把  $$z_{m,n}$$ 的定义代入到 $$\lambda(z_{m,n} \vert \{r_i,i \neq n\})$$  有：

$$
\lambda(z_{m,n}| \{r_i,i \neq n\}) = \lambda((\sum_{j \in N_{m,n} } c_j)| \{r_i,i \neq n\})  \tag{4}
$$

根据 tanh rule 定理（这个在附录中推导)，这个公式是定理，其中用到的变量，与上面的推导无关，其中 $$x_i$$ 是 取值 0/1 的二值随机变量：

$$
\begin{aligned}
\lambda(x_1 \oplus x_2 \oplus x_3 \oplus \cdots \oplus x_n) &=  2 tanh^{-1}(
tanh(-\frac{\lambda(x_1)}{2})
tanh(-\frac{\lambda(x_2)}{2})
tanh(-\frac{\lambda(x_3)}{2})
\cdots
tanh(-\frac{\lambda(x_n)}{2})
)
\\ 
&=
-2 tanh^{-1}(\prod_i tanh(-\frac{\lambda(x_i)}{2}))
\end{aligned}
$$

把这个定理应用到公式 (4)

$$
\begin{aligned}
\lambda(z_{m,n}| \{r_i,i \neq n\}) &= \lambda((\sum_{j \in N_{m,n} } c_j)| \{r_i,i \neq n\}) \\
&= - 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq n\})}{2}))
\end{aligned}
$$

我们为了区分，引入一个新的符号，对上面公式右边，用一个新的符号来表示：

$$
\eta_{m,n}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq n\})}{2})) \quad -----\quad 公式(5)
$$

这个 $$\eta_{m,n}$$，可以理解为第 m 个校验方程成立的某种度量，是用 $$c_n=1$$ 和 $$c_n=0$$ 两种情况下的校验方程成立的概率做比较来实现的一种度量，总之，这是衡量校验方程成立的一个量。而这个量中，即公式(5) 中的 $$\lambda(c_j\vert \{r_i,i \neq n\})$$，又是 比特 j 的一种概率度量。在具体算法实现时，令其等于  $$\lambda(c_j\vert r_j)$$。

则公式 (3) 变成：

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta_{m,n}\quad ----\quad 公式(6)
$$

公式 (6) 和公式 (5) 一起构成了一种循环迭代。



------------------------------ 至此完成了一轮计算。我们继续举个例子来说明：

假如正在译码 $$c_2$$ 这个比特，即  n=2，$$c_2$$ 参与的校验方程有 $$z_1,z_4,z_5$$  这三个，我们考虑来计算第四个校验方程成立的度量，即 m=4，计算 $$\eta_{4,2}$$. 

第四个校验方程，参与的比特有2，4，5，6，8，10 这六个比特。则公式 (5) 就是：

$$
\eta_{4,2}=- 2 tanh^{-1}(\prod_{j \in \{4,5,6,8,10\} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq 2\})}{2}))
$$

为了计算出来上个公式，我们需要知道 :

$$
\begin{aligned}
\lambda(c_4|\{r_i,i \neq 2\})  \\
\lambda(c_5|\{r_i,i \neq 2\})  \\
\lambda(c_6|\{r_i,i \neq 2\})  \\
\lambda(c_8|\{r_i,i \neq 2\})  \\
\lambda(c_{10}|\{r_i,i \neq 2\})
\end{aligned}
$$

在第一轮时，直接用各自的自然比来计算：

$$
\begin{aligned}
\lambda(c_4|\{r_i,i \neq 2\})=\lambda(c_4|r_4) = \frac{2}{\sigma^2}r_4  \\
\lambda(c_5|\{r_i,i \neq 2\})=\lambda(c_5|r_5) = \frac{2}{\sigma^2}r_5   \\
\lambda(c_6|\{r_i,i \neq 2\})=\lambda(c_6|r_6) = \frac{2}{\sigma^2}r_6   \\
\lambda(c_8|\{r_i,i \neq 2\})=\lambda(c_8|r_8) = \frac{2}{\sigma^2}r_8   \\
\lambda(c_{10}|\{r_i,i \neq 2\})=\lambda(c_{10}|r_{10}) = \frac{2}{\sigma^2}r_{10}
\end{aligned}
$$

这样就可以计算出来 $$\eta_{4,2}$$:

$$
\begin{aligned}
\eta_{4,2} &=- 2 tanh^{-1}(\\
&tanh(-\frac{\lambda(c_4|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_5|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_6|\{r_i,i \neq 2\})}{2}) \\
&tanh(-\frac{\lambda(c_8|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_{10}|\{r_i,i \neq 2\})}{2}) )
\end{aligned}
$$

用类似的方法，可以把所有的 $$\eta_{m,n}$$ 都计算出来。

$$c_2$$ 参与的校验方程有 $$z_1,z_4,z_5$$  这三个，则：

$$
\lambda(c_2|r) = \frac{2}{\sigma^2}r_n +  \eta_{1,2}+\eta_{4,2}+\eta_{5,2}
$$

同理，可以计算出所有的 $$\lambda(c_i\vert r)$$.  做一次判决。

---------------------例子结束



这个时候，把  $$\lambda(c_j\vert r_j)$$ 都做一次判决，如果满足检验结果正确，则结束。否则，我们有理由用新估计出来的  $$\lambda(c_j\vert r_j)$$ 来进一步强化对校验方程成立的度量的准确性，则 $$\eta_{m,n}$$  这个需要被更新。

在这个更新中，因为上一轮的 $$\lambda(c_j\vert r_j)$$ 的计算中，是包含了第 m 个校验方程的度量的信息的，所以，为了更新第 m 个校验方程的信息，则需要把 上一轮的 $$\lambda(c_j\vert r_j)$$ 减掉 上一轮的 $$\eta_{m,n}$$，则这个更新公式为：

$$
\lambda^{[l-1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l-1]}(c_j|r) -\eta_{m,j}^{[l-1]}
$$

代入公式(5) 有：

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l-1]}(c_j|r) -\eta_{m,j}^{[l-1]}}{2})) ----公式(7)
$$

在公式（6）上增加轮次信息：

$$
\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}\quad ----\quad 公式(8)
$$

则公式 (7) (8) 构成了这个算法的迭代流程。



------------我们继续用例子来说明一下

接着前面的例子，我们现在已经有了新的  $$\lambda(c_i\vert r)$$，这是新的对比特取值的一种概率度量，现在用这个度量去更新对校验方程成立与否的度量。例如我们要更新这个：

$$
\eta_{4,2}=- 2 tanh^{-1}(\prod_{j \in \{4,5,6,8,10\} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq 2\})}{2}))
$$

需要注意的是，因为 $$\lambda(c_j\vert \{r_i,i \neq 2\})$$ 中都用了 第四个校验方程的信息，我们现在的目的又为了计算第四个校验方程的度量信息，因此避免这种自循环的问题，需要从中都拿掉 $$\eta_{4,*}$$ 的影响。

$$
\begin{aligned}
\lambda(c_4|r) = \frac{2}{\sigma^2}r_4 +  \eta_{3,4}+\eta_{4,4}+\eta_{5,4}  \\
\lambda(c_5|r) = \frac{2}{\sigma^2}r_5 +  \eta_{2,5}+\eta_{3,5}+\eta_{4,5}  \\
\lambda(c_6|r) = \frac{2}{\sigma^2}r_6 +  \eta_{1,6}+\eta_{2,6}+\eta_{4,6}  \\
\lambda(c_8|r) = \frac{2}{\sigma^2}r_8 +  \eta_{2,8}+\eta_{4,8}+\eta_{5,8}  \\
\lambda(c_{10}|r) = \frac{2}{\sigma^2}r_{10} +  \eta_{1,10}+\eta_{3,10}+\eta_{4,10}
\end{aligned}
$$

即在以上公式中依次分别拿掉：$$\eta_{4,4},\eta_{4,5},\eta_{4,6},\eta_{4,8},\eta_{4,10}$$， 然后，再送到  $$\eta_{4,2}$$  的计算公式中

$$
\begin{aligned}
\eta_{4,2}=- 2 tanh^{-1}( \\
tanh(-\frac{\lambda(c_4|r)-\eta_{4,4}^{last}}{2})* \\
tanh(-\frac{\lambda(c_5|r)-\eta_{4,5}^{last}}{2})* \\
tanh(-\frac{\lambda(c_6|r)-\eta_{4,6}^{last}}{2})* \\
tanh(-\frac{\lambda(c_8|r)-\eta_{4,8}^{last}}{2})* \\
tanh(-\frac{\lambda(c_{10}|r)-\eta_{4,10}^{last}}{2})
\\)
\end{aligned}
$$

------------例子结束





整体示意流程图如下：

![LLR-LDPC-soft.png](/figure/LDPC译码浅析/LLR-LDPC-soft.png) 

[1]  Error Correction Coding--Mathematical Methods and Algorithms , Todd K. Moon, Wiley, 2005 ，主要参考第 15.5 章节。

## 软判决算法之似然比形式 (二)--算法和代码

**输入**：校验矩阵 A，收到的数据向量 r，最大迭代次数 L，信道参数 $$L_c$$

**初始化**： 对所有 A(m,n) = 1 的 (m,n)，令 $$\eta^{[0]}_{m,n} = 0$$
令 $$\lambda^{[0]}_n = L_c r_n$$

迭代次数  $$l = 1$$

**校验节点**：对所有 A(m,n) = 1 的 (m,n)，计算：

$$
\eta^{[l]}_{m,n} = -2 tanh^{-1}
(  
\prod_{j \in N_{m,n}} 
tanh(  -\frac{ \lambda^{[l-1]}_j-\eta^{[l-1]}_{m,j}}
{2}  )
)
$$

**比特节点：**  n=1,2,...,N, 计算：

$$
\lambda^{[l]}_n = L_c r_n + \sum_{m \in M_n}  \eta^{[l]}_{m,n}
$$

做一次临时判决：如果 $$\lambda^{[l]}_n > 0$$,  则 $$\hat c_n = 1$$， 否则， $$\hat c_n = 0$$

如果 $$A \hat c = 0$$，则 **译码成功**，结束；如果迭代次数 $$l<L$$，则到 **校验节点** 继续下一轮，否则，就是**译码失败**，停止。

代码请到 [github 下载](https://github.com/taichiorange/leba_math)：[https://github.com/taichiorange/leba_math](https://github.com/taichiorange/leba_math)