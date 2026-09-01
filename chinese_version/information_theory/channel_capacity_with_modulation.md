---
layout: default
title: "信道容量的几个易混淆的公式"
back_url: /chinese_version.html
---

## 调制模式下的信道容量

[录制的视频在：B 站](https://www.bilibili.com/video/BV1XT411r7WJ)

在如下这个信道容量公式中，其前提是没有对输入端做任何约束，能达到这个容量的时候，输入端是连续的高斯分布：
$$
C = log(1+\frac{S}{N})  \tag{1}
$$
但是，在实际系统中，我们用的都是离散的输入，即是经过调制之后的离散的数据，那么在这种情况下，其信道容量的上限是多少呢？不同的信噪比条件下，其信道容量的上限是多少呢？



我们从信道容量的最基本公式出发，我们假定输入端的调制后的数据是等概率分布的，则信道容量就是：
$$
I(X;Y)  \tag{2}
$$
根据基本的信息论知识，我们对公式 (2) 进一步推导有：
$$
I(X;Y) = H(X) - H(X|Y)  \tag{3}
$$
其中 H(X) 很容易计算：
$$
H(X) = \sum_{i=1}^M p(x) log_2\frac{1}{p(x)} = \sum_{i=1}^M \frac{1}{M} log_2 M = log_2 M \tag{4}
$$
所以，关键是计算 H(X|Y) 这个条件熵，根据信息论的基本知识有：
$$
H(X|Y) = \int p(Y=y) H(X|Y=y) dy  \tag{5}
$$
公式 (5) 可以用蒙特卡洛算法来计算这个积分，所以，关键是要计算出来 H(X|Y=y) 这个概率.
$$
H(X|Y=y) = \sum_x p(X=x|Y=y) log \frac{1}{p(X=x|Y=y) } \tag{6}
$$
公式 (6) 中：
$$
p(X=x|Y=y) = p(x|y) = \frac{p(y|x)p(x)}{p(y)}=p(y|x)\frac{p(x)}{p(y)} \tag{7}
$$
因为 x 是等概率分布的，所以，公式 (7) 中的 $$\frac{p(x)}{p(y)}$$ 与 x 的具体取值无关，因此
$$
p(x|y) \propto  p(y|x) \tag{8}
$$
所以，可以把 p(y|x) 对每个 x 计算出来，然后 归一化之后，就是  p(x|y) 的概率。


![调制模式下信道容量曲线PSK](/figure/information_theory/调制模式下信道容量曲线PSK.png)

*调制模式下信道容量曲线PSK*


代码：在 gitnub 中.  \url{https://github.com/taichiorange/leba_math.git}


### 蒙特卡罗算法算法
对公式 (5) 使用蒙特卡罗算法，我们可以把公式 (5) 中的积分更一般化，即对函数 f(y) 计算数学期望：
$$
\int p(y) f(y) dy  \tag{9}
$$

其中 $$p(y)$$ 是随机变量 Y 的概率密度。

我们要按照 $$p(y)$$ 这个概率密度函数，生成足够多的数据,假如总共有 N 个, 根据概率知识，我们知道，$$p(y)$$ 概率大的，对应的 $$y$$ 出现的就多，假如：
$$
\begin{aligned}
	y_1 :\ & n_1 \\
	y_2 :\ & n_2 \\
	\cdots \\
	y_i :\ & n_i \\
	\cdots
\end{aligned}
$$

其中 $$n_i$$ 表示出现的次数，那么：
$$
\frac{\sum_i n_i  f(y_i)}{N} = \sum_i \frac{n_i  f(y_i)}{N} = \sum_i \frac{n_i}{N} f(y_i) = \sum_i p(y_i) f(y_i)
\tag{10}
$$


用采样的平均，即样本均值，来代替数学期望。用公式 (10) 计算的结果，来代替公式 (9).


更一般的，对于蒙特卡罗算法，我们对下面的定积分：
$$
\int_a^b f(x) dx   \tag{11}
$$

我们将公式 (11) 转成一种数学期望：
$$
\int_a^b f(x) dx = \int_a^b \frac{1}{b-a}f(x)(b-a) =  \int_a^b \frac{1}{b-a} g(x)   \tag{12}
$$

其中 $$g(x) = (b-a)f(x)$$, 则公式 (12) 可以看成是随机变量 $$X$$，其取值范围在 $$[a,b]$$, 满足均匀分布，即其概率密度函数为：
$$
p(x) = \frac{1}{b-a}
$$

用蒙特卡罗算法，在$$[a,b]$$ 均匀随机生成足够多的数据，代入 $$g(x)$$，然后，取其平均值。
