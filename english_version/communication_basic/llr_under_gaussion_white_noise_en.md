---
layout: default
title: "Derivation of the Log Likelihood Ratio under the Gaussian White Noise Channel"
lang: en
back_url: /index.html?lang=en
---

# Derivation of the Log Likelihood Ratio under the Gaussian White Noise Channel



In communication systems, we often need to compute the Log Likelihood Ratio (LLR), especially when soft decoding is used in channel decoding. This short article derives how the LLR is computed from the received signal under different modulations; we mainly discuss three modulations: BPSK, QPSK, 8-PSK.

We talk about QPSK first, since it is fairly representative: simplified it becomes BPSK, and extended further it becomes 8-PSK.

A sequence made up of 0s and 1s becomes a complex number after QPSK encoding, with every two bits modulated into one complex number.
It is then transmitted through a Gaussian white noise channel. Here we regard the real part and the imaginary part as being transmitted separately and independently, each with independent Gaussian white noise interference added.

(Note 1: In reality the real part and the imaginary part are transmitted over the same channel, only that they are sent with mutually orthogonal high-frequency signals as carriers, so they can be regarded as being transmitted separately.
Note 2: We call the real part in-phase, using I as the subscript, and we call the imaginary part quadrature, denoted with Q.)


![channel.png](/figure/高斯白噪声软信息计算/channel.png)


In a communication system, what we care about is the probability, given that the signal y has been received, of deciding that the bit sent by the transmitter is 1 or 0, which can be expressed as $$p(b=0\vert y)$$ and $$p(b=1 \vert y)$$.

In many cases we care about the ratio of the two, because if $$p(b=0\vert y)$$ is greater than $$p(b=1\vert y)$$, then we have reason to decide that b=0; otherwise, we are more inclined to decide that b=1. Expressed as a ratio, this is:

$$
\begin{cases}
	\frac{p(b=0\vert y)  }{p(b=1\vert y)  } > 1&   b=0\\ \\
	\frac{p(b=0\vert y)  }{p(b=1\vert y)  } < 1& b=1
\end{cases}
$$

In order to simplify some of the computation further (this will be explained later), one generally also takes the logarithm, that is:

$$
log \frac{p(b=0\vert y)  }{p(b=1 \vert y)  }
$$

Then the decision criterion is:

$$
\begin{cases}
	log\frac{p(b=0\vert y)  }{p(b=1\vert y)  } > 0&   b=0\\ \\
	log\frac{p(b=0\vert y)  }{p(b=1\vert y)  } < 0& b=1
\end{cases}
$$

Next we carry out a derivation on the above ratio and derive the form of the likelihood ratio, because the form $$p(y\vert b)$$ is called the likelihood function, while $$p(b \vert y)$$ is generally called the posterior probability.

First let us do a derivation on $$p(b\vert y)$$:

$$
p(b\vert y) =\frac{p(b,y)}{p(y)} =\frac{p(y\vert b)p(b)}{p(y)}
$$

Then:

$$
log \frac{p(b=0 \vert y)  }{p(b=1\vert y)  } = log \frac {   \frac{p(y\vert b=0)p(b=0)}{p(y)}   }
{    \frac{p(y\vert b=1)p(b=1)}{p(y)}     }
= log \frac {   p(y\vert b=0)p(b=0) }
{    p(y\vert b=1)p(b=1)    }
= log \frac {   p(y\vert b=0) }
{    p(y\vert b=1)  }
$$

In the last step of the derivation above, we assume that the probabilities of the transmitted data being 0 or 1 are equal, both being 0.5. This assumption holds in most cases: at the data generation stage there is generally a step of pseudo-randomization, i.e., making the numbers of occurrences of 0 and of 1 equal.

The final result of the above formula is what we call the log likelihood ratio, Log Likelihood Ratio (LLR).

What we actually care about is the log ratio of the posterior probabilities, but under the premise of the assumption that the probabilities of 0 and of 1 are equal, it is in fact equivalent to the log likelihood ratio, so in what follows we directly derive the specific expression of the log likelihood ratio.

White noise with mean 0 and variance $$\sigma^2$$ follows the Gaussian distribution given by the following formula:

$$
p(n) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{n^2}{2\sigma^2}  }
$$

Then, if what is transmitted is x, the probability that what is received is y is:

$$
p(y\vert x) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y-x)^2}{2\sigma^2}  }
$$

## QPSK+BPSK
Under QPSK, two bits are modulated into one complex number. We denote these two bits as $$b_1 b_0$$, the modulated symbol as $$s_I + j s_Q$$, and the received complex signal as $$y_I + j y_Q$$.

The mapping relationship is shown in the figure below:

![QPSK.png](/figure/高斯白噪声软信息计算/QPSK.png)

$$
\begin{aligned}
b_1 b_0=00    --------> \quad   \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2}   \\
b_1 b_0=01    --------> \quad   -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2}   \\
b_1 b_0=11    --------> \quad   -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2}   \\
b_1 b_0=10    --------> \quad   \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2}
\end{aligned}
$$

Below we analyze how to compute the log likelihood ratio of $$b_0$$, that is:

$$
LLR(b_0) = log \frac {   p(y \vert b_0=0) }
{    p(y\vert b_0=1)  }
$$

So we need to find $$p(y\vert b_0=0)$$ and $$p(y\vert b_0=1)$$ separately.

We analyze one of them; the other is similar.
$$b_0 =0$$ has two cases, namely $$b_1 b_0 =00$$ and $$b_1 b_0 =10$$

Then:

$$
p(y\vert b_0=0) = p(y\vert b_1=0, b_0=0) *0.5 + p(y\vert b_1=1, b_0=0) *0.5
$$

where:

$$
\begin{aligned}
p(y\vert b_1=0, b_0=0) = p(y\vert s_I+js_Q = \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q\vert s_I+js_Q = \frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
=p(y_I\vert s_I=\frac{\sqrt 2}{2}) *p(y_Q\vert s_Q=\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

Similarly:

$$
\begin{aligned}
p(y\vert b_1=1, b_0=0) = p(y\vert s_I+js_Q = \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q\vert s_I+js_Q = \frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
=p(y_I\vert s_I=\frac{\sqrt 2}{2}) *p(y_Q\vert s_Q=-\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y\vert b_0=0) = 
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

$$b_0 =1$$ has two cases, namely $$b_1 b_0 =01$$ and $$b_1 b_0 =11$$

Then:

$$
p(y\vert b_0=0) = p(y\vert b_1=0, b_0=1) *0.5 + p(y\vert b_1=1, b_0=1) *0.5
$$

where:

$$
\begin{aligned}
p(y\vert b_1=0, b_0=1) = p(y\vert s_I+js_Q = -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q\vert s_I+js_Q = -\frac{\sqrt 2}{2} + j\frac{\sqrt 2}{2} ) \\
=p(y_I\vert s_I=-\frac{\sqrt 2}{2}) *p(y_Q\vert s_Q=\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y \vert b_1=1, b_0=1) = p(y\vert s_I+js_Q = -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
= p(y_I+j y_Q\vert s_I+js_Q = -\frac{\sqrt 2}{2} - j\frac{\sqrt 2}{2} ) \\
=p(y_I\vert s_I=-\frac{\sqrt 2}{2}) *p(y_Q\vert s_Q=-\frac{\sqrt 2}{2}) \\
=\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } *
\frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  }
\end{aligned}
$$

$$
\begin{aligned}
p(y\vert b_0=1) = 
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

Finally,

$$
log    \frac {   p(y\vert b_0=0) }  {    p(y\vert b_0=1)  } =  
log 
\frac{e^{  -\frac{(y_I-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }
{ e^{  -\frac{(y_I+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } } 
= 
log e^{ \frac{\sqrt 2 y_I} {\sigma^2} } =  \frac{\sqrt 2 y_I}{\sigma^2}
$$

Using the same method, we can obtain:

$$
log \frac {   p(y\vert b_1=0) }{ p(y\vert b_1=1)  } 
=  
log 
\frac
{e^{  -\frac{(y_Q-\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }
{ e^{  -\frac{(y_Q+\frac{\sqrt 2}{2})^2}{2\sigma^2}  } }  
= log e^{ \frac{\sqrt 2 y_Q}{\sigma^2} } 
=  \frac{\sqrt 2 y_Q}{\sigma^2}
$$

For QPSK, we finally get a fairly concise relationship, namely that the log likelihood ratios of the two bits correspond directly to the real part and the imaginary part of the received complex number respectively!

For BPSK, since there is only one bit, we denote it directly as b; now we quickly list the relevant derivation process:

$$
\begin{aligned}
LLR(b) = log \frac {   p(y\vert b=0) }  {    p(y\vert b=1)  }  \\
=log \frac
{ p(y_I + j y_Q \vert 1 + 0j)   }
{ p(y_I + j y_Q \vert -1 + 0j)   }   \\
=log \frac
{ p(y_I\vert 1) p( y_Q \vert 0) }
{ p(y_I\vert -1) p( y_Q \vert 0) }   \\
=log \frac
{  e^{  -\frac{(y_I-1)^2}{2\sigma^2} }             }
{  e^{  -\frac{(y_I+1)^2}{2\sigma^2} }             }  \\
=\frac{2 y_I}{\sigma^2}
\end{aligned}
$$

For 8-PSK, however, there is no such elegant result; since there is quite a lot of content, we leave the derivation to the next article.


## 8PSK

In communication systems, we often need to compute the LLR (Log Likelihood Ratio), especially when soft decoding is used in channel decoding. This short article derives how the LLR is computed from the received signal under different modulations; we mainly discuss three modulations: BPSK, QPSK, 8-PSK.

We already talked about QPSK first, since it is fairly representative: simplified it becomes BPSK, and extended further it becomes 8-PSK.

A sequence made up of 0s and 1s becomes a complex number after QPSK encoding, with every two bits modulated into one complex number.
It is then transmitted through a Gaussian white noise channel. Here we regard the real part and the imaginary part as being transmitted separately and independently, each with independent Gaussian white noise interference added. (Note 1: In reality the real part and the imaginary part are transmitted over the same channel, only that they are sent with mutually orthogonal high-frequency signals as carriers, so they can be regarded as being transmitted separately.
Note 2: We call the real part in-phase, using I as the subscript, and we call the imaginary part quadrature, denoted with Q.)

![channel.png](/figure/高斯白噪声软信息计算/channel.png)


In a communication system, what we care about is the probability, given that the signal y has been received, of deciding that the bit sent by the transmitter is 1 or 0, which can be expressed as $$p(b=0\vert y)$$ and $$p(b=1\vert y)$$.

In many cases we care about the ratio of the two, because if $$p(b=0\vert y)$$ is greater than $$p(b=1\vert y)$$, then we have reason to decide that b=0; otherwise, we are more inclined to decide that b=1. Expressed as a ratio, this is:

$$
\begin{cases}
	\frac{p(b=0\vert y)  }{p(b=1\vert y)  } > 1&   b=0\\ \\
	\frac{p(b=0\vert y)  }{p(b=1\vert y)  } < 1& b=1
\end{cases}
$$

In order to simplify some of the computation further (this will be explained later), one generally also takes the logarithm, that is:

$$
log \frac{p(b=0\vert y)  }{p(b=1\vert y)  }
$$

Then the decision criterion is:

$$
\begin{cases}
	log\frac{p(b=0\vert y)  }{p(b=1\vert y)  } > 0&   b=0\\ \\
	log\frac{p(b=0\vert y)  }{p(b=1\vert y)  } < 0& b=1
\end{cases}
$$

Next we carry out a derivation on the above ratio and derive the form of the likelihood ratio, because the form $$p(y\vert b)$$ is called the likelihood function, while $$p(b\vert y)$$ is generally called the posterior probability.

First let us do a derivation on $$p(b\vert y)$$:

$$
p(b\vert y) =\frac{p(b,y)}{p(y)} =\frac{p(y\vert b)p(b)}{p(y)}
$$

Then:

$$
log \frac{p(b=0\vert y)  }{p(b=1\vert y)  } = log \frac {   \frac{p(y\vert b=0)p(b=0)}{p(y)}   }
{    \frac{p(y\vert b=1)p(b=1)}{p(y)}     }
= log \frac {   p(y\vert b=0)p(b=0) }
{    p(y\vert b=1)p(b=1)    }
= log \frac {   p(y\vert b=0) }
{    p(y\vert b=1)  }
$$

In the last step of the derivation above, we assume that the probabilities of the transmitted data being 0 or 1 are equal, both being 0.5. This assumption holds in most cases: at the data generation stage there is generally a step of pseudo-randomization, i.e., making the numbers of occurrences of 0 and of 1 equal.

The final result of the above formula is what we call the log likelihood ratio, Log Likelihood Ratio (LLR).

What we actually care about is the log ratio of the posterior probabilities, but under the premise of the assumption that the probabilities of 0 and of 1 are equal, it is in fact equivalent to the log likelihood ratio, so in what follows we directly derive the specific expression of the log likelihood ratio.

White noise with mean 0 and variance $$\sigma^2$$ follows the Gaussian distribution given by the following formula:

$$
p(n) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{n^2}{2\sigma^2}  }
$$

Then, if what is transmitted is x, the probability that what is received is y is:

$$
p(y\vert x) = \frac{1}{\sqrt{2\pi} \sigma} e^{  -\frac{(y-x)^2}{2\sigma^2}  }
$$

Under 8-PSK, three bits are modulated into one complex number. We denote these three bits as $$b_2b_1 b_0$$, the modulated symbol as $$s_I + j s_Q$$, and the received complex signal as $$y_I + j y_Q$$.

The mapping relationship is shown in the figure below:

![8-psk.png](/figure/高斯白噪声软信息计算/8-psk.png)
Let:

$$
\begin{aligned}
c = cos(\frac{\pi}{8})  \\  \space \\
s = sin(\frac{\pi}{8})
\end{aligned}
$$

The mapping relationship is:

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

Under "8-PSK" we have to find separately:

$$
LLR(b_2), LLR(b_1),LLR(b_0),
$$

###  $$LLR(b_0)$$

Below we analyze how to compute the log likelihood ratio of $$b_0$$, that is:

$$
LLR(b_0) = log \frac {   p(y\vert b_0=0) }
{    p(y\vert b_0=1)  }
$$

So we need to find $$p(y\vert b_0=0)$$ and $$p(y\vert b_0=1)$$ separately.

We analyze one of them; the other is similar.
$$b_0 =0$$ has four cases:

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
p(y\vert b_0=0)= \\
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

$$b_0 =1$$ has four cases:

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
p(y\vert b_0=1)= \\
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

Then this ratio:

$$
\frac {   p(y\vert b_0=0) }
{    p(y\vert b_0=1)  }
$$

Neither the numerator nor the denominator allows any common factor to be extracted and cancelled out, and therefore it cannot be simplified into a simple expression.

###  $$LLR(b_1)$$

Below we analyze how to compute the log likelihood ratio of $$b_1$$, that is:

$$
LLR(b_1) = log \frac {   p(y\vert b_1=0) }
{    p(y\vert b_1=1)  }
$$

So we need to find $$p(y\vert b_1=0)$$ and $$p(y\vert b_1=1)$$ separately.

We analyze one of them; the other is similar.

$$b_1 =0$$ has four cases:

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
p(y\vert b_1=0)= \\
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

$$b_1 =1$$ has four cases:

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
p(y\vert b_1=1)= \\
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

Below we analyze how to compute the log likelihood ratio of $$b_2$$, that is:

$$
LLR(b_2) = log \frac {   p(y\vert b_2=0) }
{    p(y\vert b_2=1)  }
$$

So we need to find $$p(y\vert b_2=0)$$ and $$p(y\vert b_2=1)$$ separately.

We analyze one of them; the other is similar.

$$b_2 =0$$ has four cases:

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
p(y\vert b_2=0)= \\
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

$$b_2 =1$$ has four cases:

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
p(y\vert b_2=1)= \\
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

### An Approximation Theorem for the log of Exponentials

Theorem: $$ln(e^a+e^b)=max(a,b)+ln(1+e^{-\lvert a-b \rvert})$$

Because $$e^{-\lvert a-b\rvert} < 1$$

Therefore $$ln(1+e^{-\lvert a-b\rvert}) < ln2<1$$

Finally an approximate formula can be derived:

$$
ln(e^a+e^b)=max(a,b)+ln(1+e^{-\lvert a-b \rvert })\approx max(a,b)
$$

Generalizing further

$$
ln(e^a+e^b+e^c+e^d) \approx max(a,b,c,d)
$$