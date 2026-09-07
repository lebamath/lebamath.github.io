---
layout: default
title: "高斯白噪声信道下对数自然比的推导"
back_url: /index.html?lang=zh
---
# 高斯白噪声信道下对数自然比的推导



在通信系统中，我们经常需要计算 Log Likelihood Ratio (LLR) 对数似然比，尤其是在信道译码采用软译码的时候。这个小文章，就来推导在不同调制下，如何根据接收到的信号，计算 LLR，我们主要讨论 三种调制：BPSK, QPSK, 8-PSK.

我们先讲 QPSK 的，这个比较有代表性，简化后就是 BPSK，进一步扩展就是 8-PSK.

一组 0 和 1 组成的序列，经过 QPSK 编码后变成一个复数，每两个比特调制成一个复数.
经过高斯白噪声信道传输，这里我们把实部和虚部看成是分别独立传输的，分别加上独立的高斯白噪声的干扰。

（注 1：实际上是实部和虚部在相同的信道上传输，只是由相互正交的高频信号做载波发送的，可以看成是分别传输的
注 2：实部我们称之为 in-phase，用 I 做下标，虚部我们称之为 quadrature，用 Q 做表示）。


![channel.png](/figure/高斯白噪声软信息计算/channel.png)


通信系统中，我们关心的是在收到信号 y 的情况下，判断发送方发送的比特是 1 或者 0 的概率，可以表示成  p(b=0|y)  和 p(b=1|y).

很多情况下，我们关心两者的比值，因为如果  p(b=0|y)  大于 p(b=1|y)，我们就有理由可以判断 b=0，否则，我们更倾向于判断 b=1.  用比值来表示就是：

$$
\begin{cases}
	\frac{p(b=0|y)  }{p(b=1|y)  } > 1&   b=0\\ \\
	\frac{p(b=0|y)  }{p(b=1|y)  } < 1& b=1
\end{cases}
$$

为了更简化一些运算（后面会解释），一般还要再取对数，即：

$$
log \frac{p(b=0|y)  }{p(b=1|y)  }
$$

那么判决准则为：

$$
\begin{cases}
	log\frac{p(b=0|y)  }{p(b=1|y)  } > 0&   b=0\\ \\
	log\frac{p(b=0|y)  }{p(b=1|y)  } < 0& b=1
\end{cases}
$$

接下啦我们把上面的比值，做一下推导，推导出似然比的形式，因为  p(y|b) 的形式，称之为似然函数，而 p(b|y) 一般称之为后验概率。

先对 p(b|y) 做一下推导：

$$
p(b|y) =\frac{p(b,y)}{p(y)} =\frac{p(y|b)p(b)}{p(y)}
$$

则：

$$
log \frac{p(b=0|y)  }{p(b=1|y)  } = log \frac {   \frac{p(y|b=0)p(b=0)}{p(y)}   }
{    \frac{p(y|b=1)p(b=1)}{p(y)}     }
= log \frac {   p(y|b=0)p(b=0) }
{    p(y|b=1)p(b=1)    }
= log \frac {   p(y|b=0) }
{    p(y|b=1)  }
$$

上式推导的最后一步，我们是假定发送数据是 0 还是 1 的概率是相等的，都是 0.5，这个假定大部分情况下都是成立的，在数据生成的阶段，一般都有一步做 伪随机化，即让 0 和 1 出现的数量是相等的。

则上面公式的最后结果，我们称之为 对数似然比 Log Likelihood Ratio (LLR)  。

我们实际上关心的是后验概率的对数比值，但是，在在 0 还是 1 的概率是相等的假设前提下，其实是与对数似然比等同的，所以，后面我们就直接推导对数似然比的具体表达式了。

均值为 0 方差为 $$\sigma^2$$ 的白噪声，符合以下公式的高斯分布：

$$
p(n) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{n^2}{2\sigma^2}  }
$$

则如果发送的是 x 收到的是 y 的概率就是：

$$
p(y|x) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y-x)^2}{2\sigma^2}  }
$$

## QPSK+BPSK
QSPK 下，两个比特调制为一个复数，我们把这两个比特记为  $$b_1 b_0$$，调制后的符号记为 $$s_I + j s_Q$$，接收到的复数信号为 $$y_I + j y_Q$$.

映射关系如下图所示：

![QPSK.png](/figure/高斯白噪声软信息计算/QPSK.png)

$$
\begin{aligned}
b_1 b_0=00    --------> \quad   \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2}   \\
b_1 b_0=01    --------> \quad   -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2}   \\
b_1 b_0=11    --------> \quad   -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2}   \\
b_1 b_0=10    --------> \quad   \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2}
\end{aligned}
$$

下面来分析，如何计算 $$b_0$$ 的对数似然比，即：

$$
LLR(b_0) = log \frac {   p(y|b_0=0) }
{    p(y|b_0=1)  }
$$

所以，需要分别求出来 $$p(y|b_0=0)$$  和 $$p(y|b_0=1)$$。

我们分析其中一个，另外一个是类似的。
$$b_0 =0$$ 有两种情况，即 $$b_1 b_0 =00$$ 和 $$b_1 b_0 =10$$

则：

$$
p(y|b_0=0) = p(y|b_1=0, b_0=0) *0.5 + p(y|b_1=1, b_0=0) *0.5
$$

其中：

$$
\begin{aligned}
p(y|b_1=0, b_0=0) = p(y|s_I+js_Q = \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q|s_I+js_Q = \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
=p(y_I|s_I=\frac{\sqrt 2}{2}) *p(y_Q|s_Q=\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

类似地：

$$
\begin{aligned}
p(y|b_1=1, b_0=0) = p(y|s_I+js_Q = \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q|s_I+js_Q = \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
=p(y_I|s_I=\frac{\sqrt 2}{2}) *p(y_Q|s_Q=-\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y|b_0=0) = 
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } * 0.5 + 
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } * 0.5 \\
=  e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } 
(
\frac{1}{2\pi \sigma^2} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }  +
\frac{1}{2\pi \sigma^2}  e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
) * 0.5
\end{aligned}
$$

$$b_0 =1$$ 有两种情况，即 $$b_1 b_0 =01$$ 和 $$b_1 b_0 =11$$

则：

$$
p(y|b_0=0) = p(y|b_1=0, b_0=1) *0.5 + p(y|b_1=1, b_1=1) *0.5
$$

其中：

$$
\begin{aligned}
p(y|b_1=0, b_0=1) = p(y|s_I+js_Q = -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q|s_I+js_Q = -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
=p(y_I|s_I=-\frac{\sqrt 2}{2}) *p(y_Q|s_Q=\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y|b_1=1, b_0=1) = p(y|s_I+js_Q = -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q|s_I+js_Q = -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
=p(y_I|s_I=-\frac{\sqrt 2}{2}) *p(y_Q|s_Q=-\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y|b_0=1) = 
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } * 0.5 + 
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } * 0.5 \\
=  e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } 
(
\frac{1}{2\pi \sigma^2} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }  +
\frac{1}{2\pi \sigma^2}  e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
) * 0.5
\end{aligned}
$$

最后，

$$
log    \frac {   p(y|b_0=0) }  {    p(y|b_0=1)  } =  
log 
\frac{e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }
{ e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } } 
= 
log e^{ \frac{\sqrt 2 y_I} {\sigma^2} } =  \frac{\sqrt 2 y_I}{\sigma^2}
$$

用同样的方法，可以得出：

$$
log \frac {   p(y|b_1=0) }{ p(y|b_1=1)  } 
=  
log 
\frac
{e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }
{ e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }  
= log e^{ \frac{\sqrt 2 y_Q}{\sigma^2} } 
=  \frac{\sqrt 2 y_Q}{\sigma^2}
$$

对于 QPSK , 我们最终得到一个比较简洁的关系，即两个比特的对数似然比分别直接对应接收到复数的实部和虚部！

对于 BPSK，因为只有一个 比特，我们直接记为 b，现在快速列出来相关的推导过程：

$$
\begin{aligned}
LLR(b) = log \frac {   p(y|b=0) }  {    p(y|b=1)  }  \\
=log \frac
{ p(y_I + j y_Q | 1 + 0j)   }
{ p(y_I + j y_Q | -1 + 0j)   }   \\
=log \frac
{ p(y_I|1) p( y_Q |0) }
{ p(y_I|-1) p( y_Q |0) }   \\
=log \frac
{  e^{  -\frac{(y_I-1)^2}{2\sigma^2} }             }
{  e^{  -\frac{(y_I+1)^2}{2\sigma^2} }             }  \\
=\frac{2 y_I}{\sigma^2}
\end{aligned}
$$

而对于 8-PSK，则没有这么优美的结果，由于内容较多，我们放到下一篇文章中去推导。


## 8PSK

在通信系统中，我们经常需要计算 LLR（Log Likelihood Ratio）对数似然比，尤其是在信道译码采用软译码的时候。这个小文章，就来推导一下在不同调制下，如何根据接收到的信号，计算 LLR，我们主要讨论 三种调制：BPSK, QPSK, 8-PSK.

我们先讲了 QPSK 的，这个比较有代表性，简化后就是 BPSK，进一步扩展就是 8-PSK.

一组 0 和 1 组成的序列，经过 QPSK 编码后变成一个复数，每两个比特调制成一个复数.
经过高斯白噪声信道传输，这里我们把实部和虚部看成是分别独立传输的，分别加上独立的高斯白噪声的干扰。（注 1：实际上是实部和虚部在相同的信道上传输，只是由相互正交的高频信号做载波发送的，可以看成是分别传输的
注 2：实部我们称之为 in-phase，用 I 做下标，虚部我们称之为 quadrature，用 Q 做表示）。

![channel.png](/figure/高斯白噪声软信息计算/channel.png)


通信系统中，我们关心的是在收到信号 y 的情况下，判断发送方发送的比特是 1 或者 0 的概率，可以表示成  p(b=0|y)  和 p(b=1|y).

很多情况下，我们关心两者的比值，因为如果  p(b=0|y)  大于 p(b=1|y)，我们就有理由可以判断 b=0，否则，我们更倾向于判断 b=1.  用比值来表示就是：

$$
\begin{cases}
	\frac{p(b=0|y)  }{p(b=1|y)  } > 1&   b=0\\ \\
	\frac{p(b=0|y)  }{p(b=1|y)  } < 1& b=1
\end{cases}
$$

为了更简化一些运算（后面会解释），一般还要再取对数，即：

$$
log \frac{p(b=0|y)  }{p(b=1|y)  }
$$

那么判决准则为：

$$
\begin{cases}
	log\frac{p(b=0|y)  }{p(b=1|y)  } > 0&   b=0\\ \\
	log\frac{p(b=0|y)  }{p(b=1|y)  } < 0& b=1
\end{cases}
$$

接下啦我们把上面的比值，做一下推导，推导出似然比的形式，因为  p(y|b) 的形式，称之为似然函数，而 p(b|y) 一般称之为后验概率。

先对 p(b|y) 做一下推导：

$$
p(b|y) =\frac{p(b,y)}{p(y)} =\frac{p(y|b)p(b)}{p(y)}
$$

则：

$$
log \frac{p(b=0|y)  }{p(b=1|y)  } = log \frac {   \frac{p(y|b=0)p(b=0)}{p(y)}   }
{    \frac{p(y|b=1)p(b=1)}{p(y)}     }
= log \frac {   p(y|b=0)p(b=0) }
{    p(y|b=1)p(b=1)    }
= log \frac {   p(y|b=0) }
{    p(y|b=1)  }
$$

上式推导的最后一步，我们是假定发送数据是 0 还是 1 的概率是相等的，都是 0.5，这个假定大部分情况下都是成立的，在数据生成的阶段，一般都有一步做 伪随机化，即让 0 和 1 出现的数量是相等的。

则上面公式的最后结果，我们称之为 对数似然比 Log Likelihood Ratio (LLR)  。

我们实际上关心的是后验概率的对数比值，但是，在在 0 还是 1 的概率是相等的假设前提下，其实是与对数似然比等同的，所以，后面我们就直接推导对数似然比的具体表达式了。

均值为 0 方差为 $$\sigma^2$$ 的白噪声，符合以下公式的高斯分布：

$$
p(n) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{n^2}{2\sigma^2}  }
$$

则如果发送的是 x 收到的是 y 的概率就是：

$$
p(y|x) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y-x)^2}{2\sigma^2}  }
$$

8-PSK 下，三个比特调制为一个复数，我们把这连个比特记为  $$b_2b_1 b_0$$，调制后的符号记为 $$s_I + j s_Q$$，接收到的复数信号为 $$y_I + j y_Q$$.

映射关系如下图所示：

![8-psk.png](/figure/高斯白噪声软信息计算/8-psk.png)
令：

$$
\begin{aligned}
c = cos(\frac{\pi}{8})  \\  \space \\
s = sin(\frac{\pi}{8})
\end{aligned}
$$

映射关系为：

$$
\begin{aligned}
b_2b_1 b_0 =000 ------>c+js  \\
b_2b_1 b_0 =001 ------>s+jc  \\
b_2b_1 b_0 =011 ------>-s+jc  \\
b_2b_1 b_0 =010 ------>-c+js  \\
b_2b_1 b_0 =110 ------>-c-js  \\
b_2b_1 b_0 =111 ------>-s-jc  \\
b_2b_1 b_0 =101 ------>s-jc  \\
b_2b_1 b_0 =100 ------>c-js
\end{aligned}
$$

"8-PSK" 下要分别求:

$$
LLR(b_2), LLR(b_1),LLR(b_0),
$$

###  $$LLR(b_0)$$

下面来分析，如何计算 $$b_0$$ 的对数似然比，即：

$$
LLR(b_0) = log \frac {   p(y|b_0=0) }
{    p(y|b_0=1)  }
$$

所以，需要分别求出来 $$p(y|b_0=0)$$  和 $$p(y|b_0=1)$$。

我们分析其中一个，另外一个是类似的。
$$b_0 =0$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =000 ------>c+js  \\
b_2b_1 b_0 =010 ------>-c+js  \\
b_2b_1 b_0 =110 ------>-c-js  \\
b_2b_1 b_0 =100 ------>c-js
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_0=0)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

$$b_0 =1$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =001 ------> s+jc  \\
b_2b_1 b_0 =011 ------> -s+jc  \\
b_2b_1 b_0 =111 ------> -s-jc  \\
b_2b_1 b_0 =101 ------> s-jc  \\
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_0=1)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

则这个比值：

$$
\frac {   p(y|b_0=0) }
{    p(y|b_0=1)  }
$$

分子和分母都无法提取出一些公因式可以约掉的了，因此，就不能化简成一个简单的表达形式了。

###  $$LLR(b_1)$$

下面来分析，如何计算 $$b_1$$ 的对数似然比，即：

$$
LLR(b_1) = log \frac {   p(y|b_1=0) }
{    p(y|b_1=1)  }
$$

所以，需要分别求出来 $$p(y|b_1=0)$$  和$$p(y|b_1=1)$$。

我们分析其中一个，另外一个是类似的。

$$b_1 =0$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =000 ------>c+js  \\
b_2b_1 b_0 =001 ------>s+jc  \\
b_2b_1 b_0 =101 ------>s-jc  \\
b_2b_1 b_0 =100 ------>c-js
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_1=0)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

$$b_1 =1$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =011 ------>-s+jc  \\
b_2b_1 b_0 =010 ------>-c+js  \\
b_2b_1 b_0 =110 ------>-c-js  \\
b_2b_1 b_0 =111 ------>-s-jc
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_1=1)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

###  $$LLR(b_2)$$

下面来分析，如何计算 $$b_2$$ 的对数似然比，即：

$$
LLR(b_2) = log \frac {   p(y|b_2=0) }
{    p(y|b_2=1)  }
$$

所以，需要分别求出来 $$p(y|b_2=0)$$  和 $$p(y|b_2=1)$$。

我们分析其中一个，另外一个是类似的。

$$b_2 =0$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =000 ------>c+js  \\
b_2b_1 b_0 =001 ------>s+jc  \\
b_2b_1 b_0 =011 ------>-s+jc  \\
b_2b_1 b_0 =010 ------>-c+js  \\
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_2=0)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-c)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q-s)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

$$b_2 =1$$ 有四种情况：

$$
\begin{aligned}
b_2b_1 b_0 =110 ------>-c-js  \\
b_2b_1 b_0 =111 ------>-s-jc  \\
b_2b_1 b_0 =101 ------>s-jc  \\
b_2b_1 b_0 =100 ------>c-js
\end{aligned}
$$

$$
\begin{aligned}
p(y/b_2=1)= \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I+s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}} *\frac{1}{4}
+  \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-s)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+c)^2}{2\sigma^2}} *\frac{1}{4}
+   \\
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_I-c)^2}{2\sigma^2}}  \cdot      
\frac{1}{\sigma \sqrt{2\pi}}   e^{-\frac{(Y_Q+s)^2}{2\sigma^2}}*\frac{1}{4}
\end{aligned}
$$

### log 指数的近似定理

定理：$$ln(e^a+e^b)=max(a,b)+ln(1+e^{-|a-b|})$$

因为 $$e^{-|a-b|} < 1$$

所以 $$ln(1+e^{-|a-b|}) < ln2<1$$

最后可以推导一个近似公式：

$$
ln(e^a+e^b)=max(a,b)+ln(1+e^{-|a-b|})\approx max(a,b)
$$

推而广之

$$
ln(e^a+e^b+e^c+e^d) \approx max(a,b,c,d)
$$