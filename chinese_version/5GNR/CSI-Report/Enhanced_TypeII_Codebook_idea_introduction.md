---
layout: default
title: "Enhanced Type II Codebook 思想简介"
back_url: /index.html?lang=zh
---
## Enhanced Type II Codebook 思想简介


![Enhanced Type II Codebook-1.png](/figure/5GNR/CSI-report/Enhanced_TypeII_Codebook1.png) 


通过上面的公式，为每个 subband （其索引为 $$t$$ ，即第 t 个 subband )生成其预编码矩阵。

($$L$$ 是用的 beam 的数量，即用多少个 beam 来合成一个新的 beam

$$M_v$$ 表示有多少个非零的系数，或者说是回传回来的系数的个数，是 delay domain)

这个公式与 Type II Codebook 的最大不同，是多了一个求和符号，即第二个求和符号的部分：

$$
\LARGE \sum_{f=0}^{M_v-1} y_{t,l}^{(f)} p_{l,i,f}^{(2)} \varphi_{l,i,f}   \tag{1}
$$

以及

$$
\LARGE \sum_{f=0}^{M_v-1} y_{t,l}^{(f)} p_{l,i+L,f}^{(2)} \varphi_{l,i+L,f}  \tag{2}
$$

接下来我们主要来解释这个多出来的求和符号。

每个子带，我们都要回传系数（包括幅度和相位），这样的话，回传的数据量比较大，因为每个子带，每个 beam(用来合成的 beam)，每个 layer，每个极化，都要回传，这样下来，数据量很大，占用很多上行带宽。所以，需要进行数据压缩。

假定在某个极化、某个 layer 和某个用来组合的 beam 情况下，如果一共有 $$N_3$$ 个子带，这些子带上需要回传的系数，实际上反映了是在频域上不同子带的差异，根据我们已经讨论过的理论，应该是由于多径引起的在频域上的差异，多径在时域上，体现的就是若干个不同的路径，每个路径的延迟不同，有几个路径，我们就称之为几个 tap. 实际中，多径的个数不会很多，例如如果有 18 个子带，那么明显的多径可能就 3 个，4个等。多径时延与频域之间的关系，实际上是一组傅里叶变换对，因此，可以把频域的数据做个 iDFT 变换, 变换到时域，也称之为时延域 (delay domain)，那么由于路径个数不会很多，则在时延域的数据具有稀疏性，因此，我们就可以选择那些非零或者比较强的数据进行回传，那些是 0 或者很小数据的，就丢弃掉。

我们来看一下公式 (1)(2) ，其中 

$$
\LARGE  y_{t,l}^{(f)} = e^{ j \frac{2\pi t n_{3,l}^{(f)}}{N_3}}  \tag{3}
$$

其中, $$t$$ 表示的是第几个 subband，可以理解为傅里叶变换中的频率信息，即下式中的 $$k$$

$$
\LARGE X(k) = \sum_{n=0}^{N-1} x(n) e^{j\frac{2\pi k}{N}n}  \tag{4}
$$

式子 (1)(2) 其实就是 (4)， 

$$
\large p_{l,i+L,f}^{(2)} \varphi_{l,i+L,f}  --> x(n)
$$

$$
\large y_{t,l}^{(f)} = e^{ j \frac{2\pi t n_{3,l}^{(f)}}{N_3}} --> e^{j\frac{2\pi k}{N}n}
$$

公式 (1)(2) 就可以看成是对 delay domain 的信号，做 iDFT 傅里叶反变换.


一些具体参数的对应关系，可以参考下图来看：这个图中的 $$\tau_{n_f,l}^{(f)}$$ 就是协议中的那个 $$y_{t,l}^{(f)}$$
![Enhanced Type II Codebook-2](/figure/5GNR/CSI-report/Enhanced_TypeII_Codebook2.png) 

图片摘自文献 [1]


[1] Qin, Ziao and Haifan Yin. “A Review of Codebooks for CSI Feedback in 5G New Radio and Beyond.” *ArXiv* abs/2302.09222 (2023): n. pag.