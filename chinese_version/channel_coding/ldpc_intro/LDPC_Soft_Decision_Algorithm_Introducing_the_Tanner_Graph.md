---
layout: default
title: "LDPC 软判决算法-- 引入Tanner图"
back_url: /index.html?lang=zh
---

## LDPC 软判决算法-- 引入Tanner图

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1070112)

经过前面多篇文章和视频的讲解，我们已经清楚地了解了 LDPC 译码的流程和背后的逻辑思想。现在，我们可以引入 Tanner 图，来为 LDPC 译码算法做一个图形化的抽象和理解。我觉得，在理解了译码算法的流程之后，再引入 Tanner 图，就比较容易理解 Tanner 图的作用，有一个更清晰的图形化的理解，方便我们记忆 LDPC 译码的流程。



我们知道，译码算法，不管是用 Log 形式的还是不用 Log 形式的，都有两个主要的步骤和思想：



1）校验方程成立与否的概率或者某种度量

2）比特取值的概率或者某种度量



而相互迭代这两个步骤，逐步逼近成功译码这个理想的目标。所以，我们可以把步骤 1) 的结果，称之为校验，则在 Tanner 图中引入校验节点；步骤 2）的结果，称之为数据或者比特，在 Tanner 图中引入变量节点。

在这两种节点之间，引入连线，规则是：某个比特参与某个校验方程，则在对应的比特节点和校验节点之间引入连线，表示一种相互关系：比特参与校验方程，校验方程含有比特。

这个一般称之为 Tanner 图，也称之为二分图，这个图本身，并不是算法的精确表达，而是对算法中各种角色之间关系的一种形象表达，至于这种表示的背后，还需要严格的数学推导作为支撑。即，角色之间的依赖关系是有什么样的数学公式来描述的。



本篇文章都是以这个校验矩阵为例子。

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


那么，用 Tanner 图来表示这些依赖关系，可以表示成下图：


![tanner_graph.png](/figure/LDPC译码浅析/tanner_graph.png) 


两种变量节点间的关系，可以用这个思想来描述：校验节点（校验方程）把自己的某种度量信息告知这个校验方程含有的比特对应的变量节点，同理，变量节点也把自己取值的概率信息的某种度量也发给包含这个比特的校验方程对应的校验节点。



我们之前推导的 LDPC 译码都是分两个主要步骤在迭代：用比特的概率信息估计校验方程的概率信息，然后用校验方程的信息更新比特的概率信息。这个过程，在 Tanner 图上来描述：变量节点的信息发送到与之相连的校验节点，校验节点据此计算出自己的信息，然后，校验节点把这个信息发送给与之相连的变量节点。

在 Tanner 图的表示中，则问题就变成：

1）节点之间发送的信息，是什么样的信息？

2）各个节点收到发来的信息后，如何计算自己的信息，或者说如何计算将要发出去的信息？



这两个问题的回答，都依赖于具体的算法，所以，不同的算法，上面两个问题的答案也不尽相同。我们之前讲的两种算法，就有两种不同的答案。



1. 节点之间发送的信息，是什么样的信息？



**对于不用  log likelihood ratio 形式的算法**：

校验节点 m 向变量节点 n 发送的信息：

$$
r_{mn}(x)=p(z_m=0|c_n=x,r) =
\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in N_{m,n}\}: x=\sum_l x_l\}}
\quad \prod_{l \in N_{m,n}}p(c_l=x_l|r)
$$

例如：

$$
\begin{aligned}
r_{25}(x)=p(z_2=0|c_5=x,r) =
\sum_{\{x_1+x_3+x_6+x_8+x_9=x\}}
\quad \prod_{l \in \{1,3,6,8,9 \}}p(c_l=x_l|r)  \\
\sum_{\{x_1+x_3+x_6+x_8+x_9=x\}}
\quad p(c_1=x_l|r)p(c_3=x_3|r)p(c_6=x_6|r)p(c_8=x_8|r)p(c_9=x_9|r)
\end{aligned}
$$

当然，这个公式的计算，是有一个简化的快速算法的，这里就不讲了，有兴趣的朋友可以看专栏往期内容。



变量节点 n 向 校验节点 m 发送的信息： $$r_{m,n}$$

$$
q_{mn}(x)=p(c_n=x|\{z_{m'}=0\},r) =\frac{1}{p(\{z_{m'}=0\}|r)}p(c_n=x|r_n)\prod_{m'} p(z_{m'}=0|c_n=x,r)
$$

例如:

$$
\begin{aligned}
q_{21}(x)&=p(c_1=x|\{z_{m'}=0,m'\in \{1,5\}\},r) \\
&=\frac{1}{p(\{z_{m'}=0,m'\in \{1,5\}\}|r)}p(c_1=x|r_n)\prod_{m'\in \{1,5\}\}} p(z_{m'}=0|c_1=x,r)   \\
&=\frac{1}{p(\{z_{m'}=0,m'\in \{1,5\}\}|r)}p(c_1=x|r_n)  p(z_1=0|c_1=x,r)  p(z_5=0|c_1=x,r)
\end{aligned}
$$

**对于 log likelihood ratio 形式的算法：**

校验节点 m 向变量节点 n 发送的信息：

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq n\})}{2}))
$$

例如：

$$
\begin{aligned}
\eta_{2,5}^{[l]} &=- 2 tanh^{-1}(\prod_{j \in \{1,3,6,8,9\} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq 5\})}{2}))  \\    \\
&=- 2 tanh^{-1}(  \\
&tanh(-\frac{\lambda^{[l]}(c_1|\{r_i,i \neq 5\})}{2}) \\
&tanh(-\frac{\lambda^{[l]}(c_3|\{r_i,i \neq 5\})}{2})\\
&tanh(-\frac{\lambda^{[l]}(c_6|\{r_i,i \neq 5\})}{2})\\
&tanh(-\frac{\lambda^{[l]}(c_8|\{r_i,i \neq 5\})}{2})\\
&tanh(-\frac{\lambda^{[l]}(c_9|\{r_i,i \neq 5\})}{2})\\
)
\end{aligned}
$$

变量节点 n 向 校验节点 m 发送的信息：

$$
\begin{aligned}
\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}   \\  \\
\lambda^{[l+1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l]}(c_j|r) -\eta_{m,j}^{[l]}
\end{aligned}
$$

例如：

​     变量节点 1 发给校验节点 2 的信息：

$$
\begin{aligned}
\lambda^{[l]}(c_1|r) = log\frac{p(c_1=1|r)}{p(c_1=0|r)} =\frac{2}{\sigma^2}r_n + \sum_{m \in \{1,2,5\}} \eta^{[l]}_{m,1}   \\  \\
\lambda^{[l+1]}(c_1|\{r_i,i \neq n\}) = \lambda^{[l]}(c_1|r) -\eta_{2,1}^{[l]}
\end{aligned}
$$

2. 对于第二个问题的回答，即各个节点收到发来的信息后，如何计算自己的信息，由于自己的消息，就是发给另外一种节点的信息，在回答第一个问题时，已经隐含了这个问题的答案。

变量节点收到校验节点的消息之后，**计算出变量节点的信息**，在非 log likelihood ratio算法中用的公式为：

$$
q_{mn}(x)=p(c_n=x|\{z_{m'}=0\},r) =\frac{1}{p(\{z_{m'}=0\}|r)}p(c_n=x|r_n)\prod_{m'} p(z_{m'}=0|c_n=x,r)
$$

所以，变量节点是对收到的来自校验节点的信息，用连乘的方式来计算

而在 log likelihood ratio 算法中，因为取了 log ，所以，变成了用连加来计算

$$
\begin{aligned}
\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}   \\  \\
\lambda^{[l+1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l]}(c_j|r) -\eta_{m,j}^{[l]}
\end{aligned}
$$

校验节点收到变量节点的消息之后，**计算出校验节点的信息**，在非 log likelihood ratio算法中用的公式为：

$$
r_{mn}(x)=p(z_m=0|c_n=x,r) =
\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in N_{m,n}\}: x=\sum_l x_l\}}
\quad \prod_{l \in N_{m,n}}p(c_l=x_l|r)
$$

则对收到的各种变量节点的信息的组合，先做连乘，然后再做连加。

而在 log likelihood ratio 算法中，用 tanh 函数来表示，更为复杂的一种计算公式。

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq n\})}{2}))
$$