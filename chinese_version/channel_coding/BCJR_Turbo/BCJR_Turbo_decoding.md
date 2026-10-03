---
layout: default
title: " Turbo 译码浅析"
back_url: /index.html?lang=zh
---

## Turbo 译码浅析

录制的视频在[B站](https://www.bilibili.com/cheese/play/ss32595)

本文章和系列视频，将讲解 Turbo 码的基本入门。

需要的预备知识主要是知道卷积码的基本编码过程，知道卷积码的 BCJR 译码算法。

Turbo 码是由多个卷积码并联而成的， Turbo 名字的由来，是从译码的角度来看的。因此，严格来说，应该称为并联卷积码的 Turbo 译码算法更合适。
注：也有串联的卷积码构成 Turbo 码，也有由多个不同的卷积码并联/串联成的 Turbo 码，本文主要讲解由多个相同的卷积码并联构成的 Turbo 码。而且，为了叙述方便和公式的简洁，我们以下面的卷积码为例子进行讨论。


![turbo-encoder.png](/figure/卷积码编码和译码/BCJR-turbo/turbo-encoder.png) 

如果单独看每一个卷积码，我们可以用 BCJR 译码算法来译码，假如我们先用上面那一个卷积码，做 BCJR 译码，则根据之前的文章和视频讲解，我们知道对每个发送比特的后验概率，可以用如下公式计算：

$$
\begin{aligned}
	P(X_t=x|r) &= \sum_{(p,q)\in S_x}P(\psi_t=p,\psi_{t+1}=q|r) \\
	\\
	&\propto  \sum_{(p,q)\in S_x} \alpha_t(p) \gamma_t(p,q) \beta_{t+1}(q) 
\end{aligned}  \tag{1}
$$

其中：

$$
\begin{aligned}
	\gamma_t(p,q) 
	&= p(\psi_{t+1}=q, r_t |  \psi_t=p) \\  \\
	&= p( r_t | \psi_{t+1}=q,  \psi_t=p) p(\psi_{t+1}=q|\psi_t=p)  \\  \\
	&= p(r_t|a_t) p(x_t=x) 
\end{aligned}  \tag{2}
$$

以及：

$$
\begin{aligned}
	\alpha_{t+1}(q) &=\sum_p  \alpha_t(p) \lambda_t(p,q)   \\  \\
	\beta_t(p) &= \sum_q  \gamma_t(p,q)  \beta_{t+1}(q)
\end{aligned}  \tag{3}
$$

那么，我们可以很自然地想到，是否可以利用上面卷积码得到的后验概率信息，来增强对第二个卷积码做概率计算的可靠性？反过来，等第二个卷积码的后验概率计算出来后，是否又可以用来增强对第一个卷积码做概率计算的可靠性？如此往复迭代，就构成了类似涡轮增压的工作机制，因此，得名 Turbo 译码。

现在，我们来分析，从另外一个卷积码的译码结果中，拿什么样的概率信息给当前这个卷积码的译码使用。

最直观的，最容易理解的，就是把第一个卷积码的后验概率当成对发送比特的概率，代入到第二个卷积码中用到比特概率的地方。

如果令另外一个卷积码中公式 (2) 里的先验概率 $$p(x_t=x)$$ 等于上一个卷积码中的后验概率，即：

$$
p(x_t=x) = P(X_t=x|r)  \tag{4}
$$

我们来分析一下这样做会有什么问题。从上面的公式，我们在计算 $$\gamma$$ 这个概率时，用到了先验概率，我们把 $$\gamma$$  这个概率公式再展开分析一下，从公式 (2) 继续分析。因为我们用到的卷积码是系统码，即编码的 bit 会在编码后的码流中原封不动地保存，那么：

$$
\begin{aligned}
	\gamma_t(p,q) 
	&=  p(\psi_{t+1}=q, r_t |  \psi_t=p) \\  \\
	&= p( r_t | \psi_{t+1}=q,  \psi_t=p) p(\psi_{t+1}=q|\psi_t=p)  \\  \\
	&= p(r_t^{(0)},r_t^{(1)}|a_t^{(0)}, a_t^{(1)}) p(x_t=x) 
\end{aligned}  \tag{5}
$$

由于用的是系统码，所以 $$a_t^{(0)}$$ 对应的就是 $$x_t$$，所以，公式 (5) 可以写成：

$$
\begin{aligned}
	\gamma_t(p,q) 
	&= p(r_t^{(0)},r_t^{(1)}|x_t, a_t^{(1)}) p(x_t=x)   \\
	&= p(r_t^{(0)}|x_t)p(r_t^{(1)}| a_t^{(1)}) p(x_t=x)
\end{aligned}  \tag{6}
$$

把 (6)  代入 (1) 则：

$$
\begin{aligned}
	P(X_t=x|r) &\propto  \sum_{(p,q)\in S_x} \alpha_t(p) \gamma_t(p,q) \beta_{t+1}(q) \\
	& \propto \sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(0)}|x_t)p(r_t^{(1)}| a_t^{(1)}) p(x_t=x) \beta_{t+1}(q)\\
	& \propto p(r_t^{(0)}|x_t)p(x_t=x) \sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(1)}| a_t^{(1)}) \beta_{t+1}(q)
\end{aligned}  \tag{7}
$$

我们把公式 (7) 中最后三项，分别记为：

$$
\begin{aligned}
	P_{s,t}(x) &= p(r_t^{(0)}|x_t) \\ \\
	P_{p,t}(x)&=p(x_t=x) \\ \\
	P_{e,t}(x)&=\sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(1)}| a_t^{(1)}) \beta_{t+1}(q)
\end{aligned}
$$

如果把第一个卷积码计算出来的后验概率，代入到第二个卷积码的先验概率，则有：

$$
\begin{aligned}
	P(X_t=x|r) & \propto p(r_t^{(0)}|x_t)p(x_t=x) \sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(2)}| a_t^{(2)}) \beta_{t+1}(q)  \\
	& \propto  p(r_t^{(0)}|x_t)    p(r_t^{(0)}|x_t)p(x_t=x) \sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(1)}| a_t^{(1)}) \beta_{t+1}(q)   \sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(2)}| a_t^{(2)}) \beta_{t+1}(q)
\end{aligned}   \tag{8}
$$

可以看到，上面有两个 $$p(r_t^{(0)}\vert x_t)$$，而且代入后，还有 $$p(x_t=x)$$，这样会导致 “重复计算” 的感觉，参考书中好像是说不要这样，而是用公式 (7) 中 求和的部分，作为第一个卷积码因为编码而得到的概率信息，给第二个卷积码使用，即传递：

$$
p(x_t=x)=\sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(1)}| a_t^{(1)}) \beta_{t+1}(q)  \tag{9}
$$

所以， Turbo 译码的大致流程为：

1) 对第一个卷积码，计算 $$\alpha, \beta$$ 概率

2) 利用公式 (9)，计算  $$P_{e,t}(x)=\sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(1)}\vert a_t^{(1)}) \beta_{t+1}$$

3) 把 $$P_{e,t}(x)$$ 当成 $$p(x_t=x)$$，传递给第二个卷积码

4) 对第二个卷积码，计算 $$\alpha, \beta$$ 概率

5) 利用公式 (9) （稍微变化一点），计算  $$P_{e,t}(x)=\sum_{(p,q)\in S_x} \alpha_t(p) p(r_t^{(2)}\vert a_t^{(2)}) \beta_{t+1}$$

6) 把 $$P_{e,t}(x)$$ 当成 $$p(x_t=x)$$，传递回第一个卷积码，转到步骤 1 继续，直到结束.



![turbo-decoder.png](/figure/卷积码编码和译码/BCJR-turbo/turbo-decoder.png) 



详细的算法描述如下：

$$j\in \{1,2\}$$ 表示第几个卷积码

$$l$$  表示第几次迭代

$$P_{e,t}^{(l,j)}$$，表示第 $$l$$ 次迭代中，第 $$j$$ 个卷积码计算出来的要传递的外信息。

$$P^{(l,j)}$$  表示第 $$l$$ 次迭代中，第 $$j$$ 个卷积码用到的 先验概率

M  最大迭代次数



-----------------------------算法------begin

**初始化**： 令 $$P^{(0,1)}(x_t=x)=P^{(0)}(x_t=x)$$  (用初始先验概率输入给第一个卷积码，一般是等概率分布的)

**迭代**： 按照迭代次数循环 $$l=1,2,....M$$

1. 用 $$P^{(l-1,1)}(x_t=x)$$ 作为先验概率 $$P(x_t=x )$$ 输入给第一个卷积码，计算

\indent \indent 所有的 $$\alpha, \beta$$

\indent \indent 计算 $$P_{e,t}^{(l,1)}(x_t=x)$$

2. 令 $$P^{(l,2)}(x_t=x) = \prod [P_{e,t}^{(l,1)}(x_t=x)]$$

3. 用$$P^{(l,2)}(x_t=x)$$ 作为先验概率$$P(x_t=x )$$，计算

\indent \indent  所有的 $$\alpha, \beta$$

4. 如果不是最后一次迭代

\indent \indent 计算 $$P_{e,t}^{(l,2)}(x_t=x)$$

\indent \indent 令 $$P^{(l,1)}(x_t=x) = \prod^{-1} [P_{e,t}^{(l,2)}(x_t=x)]$$

5. 否则，如果是最后一次迭代

\indent \indent 用$$P^{(l,2)}(x_t=x)$$ 作为先验概率$$P(x_t=x )$$，计算 $$P(x_t=x\vert r)$$

\indent \indent 解交织 $$P(x_t=x\vert r) = \prod^{-1}[P(x_t=x\vert r)]$$



-----------------------------算法------end



我们以一个具体的例子来看译码的过程.



![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png) 

首先，做初始化，我们有的先验概率，是 0/1 比特的取值是等概率的，所以:

$$
P^{(0,1)}(x_t=0)=P^{(0)}(x_t=0) = \frac{1}{2}  \\
P^{(0,1)}(x_t=1)=P^{(0)}(x_t=1) = \frac{1}{2}
$$

对于时刻 0 到时刻 9，我们用公式 (6) 计算出来 $$\gamma$$ 概率：

$$
\begin{aligned}
	\gamma_0(p=0,q=0) &= p(r_t^{(0)}|x_0=0)p(r_t^{(1)}| a_0^{(1)}=-1) p(x_0=0)  \\
	\gamma_0(p=0,q=2) &= p(r_t^{(0)}|x_0=1)p(r_t^{(1)}| a_0^{(1)}=+1) p(x_0=1) \\
	...\\
	\gamma_0(p=3,q=1) &= p(r_t^{(0)}|x_0=0)p(r_t^{(1)}| a_0^{(1)}=-1) p(x_0=0) \\
	\gamma_0(p=3,q=3) &= p(r_t^{(0)}|x_0=1)p(r_t^{(1)}| a_0^{(1)}=+1) p(x_0=1) \\
\end{aligned}
$$

计算出所有的 $$\alpha$$

初始化 0 时刻的 $$\alpha$$

$$
\alpha_0(0) = 1  \\
\alpha_0(1) = 0 \\
\alpha_0(2) = 0 \\
\alpha_0(3) = 0
$$

1 时刻的 $$\alpha$$

$$
\alpha_1(0) =\sum_{p\in\{0,1\}}  \alpha_0(p) \lambda_0(p,0) =\alpha_0(0) \lambda_0(0,0)+\alpha_0(1) \lambda_0(1,0) \\
\alpha_1(1) =\sum_{p\in\{2,3\}}  \alpha_0(p) \lambda_0(p,1) =\alpha_0(2) \lambda_0(2,1)+\alpha_0(3) \lambda_0(3,1) \\
\alpha_1(2) =\sum_{p\in\{0,1\}}  \alpha_0(p) \lambda_0(p,2) =\alpha_0(0) \lambda_0(0,2)+\alpha_0(1) \lambda_0(1,2) \\
\alpha_1(3) =\sum_{p\in\{2,3\}}  \alpha_0(p) \lambda_0(p,3) =\alpha_0(2) \lambda_0(2,3)+\alpha_0(3) \lambda_0(3,3) \\
$$

依次类推，最后计算 9 时刻的 $$\alpha$$

$$
\alpha_9(0) =\sum_{p\in\{0,1\}}  \alpha_8(p) \lambda_8(p,0) =\alpha_8(0) \lambda_8(0,0)+\alpha_8(1) \lambda_8(1,0) \\
\alpha_9(1) =\sum_{p\in\{2,3\}}  \alpha_8(p) \lambda_8(p,1) =\alpha_8(2) \lambda_8(2,1)+\alpha_8(3) \lambda_8(3,1) \\
\alpha_9(2) =\sum_{p\in\{0,1\}}  \alpha_8(p) \lambda_8(p,2) =\alpha_8(0) \lambda_8(0,2)+\alpha_8(1) \lambda_8(1,2) \\
\alpha_9(3) =\sum_{p\in\{2,3\}}  \alpha_8(p) \lambda_8(p,3) =\alpha_8(2) \lambda_8(2,3)+\alpha_8(3) \lambda_8(3,3) \\
$$

再计算 $$\beta$$ 概率，计算时刻 10 到时刻 1 的：



初始化时刻 10 的 $$\beta$$ ：

$$
\beta_{10}(0) = 1  \\
\beta_{10}(1) = 0 \\
\beta_{10}(2) = 0 \\
\beta_{10}(3) = 0
$$

计算 9 时刻的 $$\beta$$

$$
\beta_9(0) = \sum_{q\in \{0,2\}}  \gamma_9(0,q)  \beta_{10}(q) = \gamma_9(0,0)  \beta_{10}(0) +  \gamma_9(0,2)  \beta_{10}(2) \\
\beta_9(1) = \sum_{q\in \{0,2\}}  \gamma_9(1,q)  \beta_{10}(q) = \gamma_9(1,0)  \beta_{10}(0) +  \gamma_9(1,2)  \beta_{10}(2) \\
\beta_9(2) = \sum_{q\in \{1,3\}}  \gamma_9(2,q)  \beta_{10}(q) = \gamma_9(2,1)  \beta_{10}(1) +  \gamma_9(2,3)  \beta_{10}(3) \\
\beta_9(3) = \sum_{q\in \{1,3\}}  \gamma_9(3,q)  \beta_{10}(q) = \gamma_9(3,1)  \beta_{10}(1) +  \gamma_9(3,3)  \beta_{10}(3) \\
$$

以此类推，计算 1 时刻的 $$\beta$$

$$
\beta_1(0) = \sum_{q\in \{0,2\}}  \gamma_1(0,q)  \beta_2(q) = \gamma_1(0,0)  \beta_2(0) +  \gamma_1(0,2)  \beta_2(2) \\
\beta_1(1) = \sum_{q\in \{0,2\}}  \gamma_1(1,q)  \beta_2(q) = \gamma_1(1,0)  \beta_2(0) +  \gamma_1(1,2)  \beta_2(2) \\
\beta_1(2) = \sum_{q\in \{1,3\}}  \gamma_1(2,q)  \beta_2(q) = \gamma_1(2,1)  \beta_2(1) +  \gamma_1(2,3)  \beta_2(3) \\
\beta_1(3) = \sum_{q\in \{1,3\}}  \gamma_1(3,q)  \beta_2(q) = \gamma_1(3,1)  \beta_2(1) +  \gamma_1(3,3)  \beta_2(3) \\
$$

至此，我们可以计算 extrinsic probability 外信息：

$$
\begin{aligned}
	P_{e,0}(0)&=\sum_{(p,q)\in S_0} \alpha_0(p) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(q) \\
	&= \alpha_0(0) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(0) + \alpha_0(1) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(2)  \\
	&\quad \quad +\alpha_0(2) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(1) + \alpha_0(3) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(3) \\  \\
	\\
	P_{e,0}(1)&=\sum_{(p,q)\in S_1} \alpha_0(p) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(q) \\
	&= \alpha_0(0) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(2) + \alpha_0(1) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(0)  \\
	&\quad \quad +\alpha_0(2) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(3) + \alpha_0(3) p(r_0^{(1)}| a_0^{(1)}) \beta_{1}(1) \\  \\
\end{aligned}
$$

我们把下一个卷积码要用的先验概率用前面的外信息赋值：

$$
P^{(0,2)}(x_t=0)=P_{e,0}(0) \\
P^{(0,2)}(x_t=1)=P_{e,0}(1)
$$

用同样的步骤计算 $$\gamma, \alpha, \beta$$， 然后计算出 $$P_{e,0}(x)$$ 外信息，把这个外信息作为下一轮的第一个卷积码的先验概率来使用，继续上面的过程。

 