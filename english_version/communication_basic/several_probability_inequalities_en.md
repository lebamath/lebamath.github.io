---
layout: default
title: "Several Probability Inequalities"
lang: en
back_url: /english_version.html
---
# Several Probability Inequalities
## The Markov and Chebyshev Bounds


This short article talks about several bounds in probability, that is, several inequalities. The purpose is to provide a foundation for some of the theory in communications that comes later; for example, the derivation of the polarization property in polar codes requires the Chernoff-Hoeffding inequality.



Let us first talk about the starting point of these bounds, namely the Markov Inequality.

X is a nonnegative random variable whose mean is $$\mu$$; then $$\forall a > 0$$:

$$
P(X\ge a) \le \frac{\mu}{a}
$$

Proof:

$$
\begin{aligned}
	\mu = E(X) = \int_0^{+\infty} xF_X(x)dx = \int_0^a xF_X(x)dx + \int_a^{+\infty} xF_X(x)dx \ge
	\int_a^{+\infty} xF_X(x)dx  \\
	\ge \int_a^{+\infty} aF_X(x)dx = a  \int_a^{+\infty} F_X(x)dx = a P(X\ge a)
\end{aligned}
$$

After a simple rearrangement we obtain formula (1).


If we also know the variance, then we can obtain a better estimate (the Chebyshev inequality):

$$
P(|X-\mu|\ge a) \le \frac{\sigma^2}{a^2}
$$

where $$\sigma^2$$ is the variance of the random variable X.

Proof:

Construct a new random variable

$$
Y = (X-\mu)^2
$$

Then, according to the definition of the variance,

$$
E(Y) = E[(X-\mu)^2] = \sigma^2
$$

Using the Markov inequality:

$$
P(Y\ge a^2) \le \frac{E(Y)}{a^2} = \frac{\sigma^2}{a^2}
$$

Then:

$$
P(|X-\mu|\ge a) = P((X-\mu)^2 \ge a^2) = P(Y\ge a^2) \le \frac{\sigma^2}{a^2}
$$

Note:

​      In formula (3), the condition $$X>0$$ is not required.
## Chernoff bounds, a first look: demonstrated with the Gaussian distribution
This article talks about Chernoff Bounds. This bound is actually a way of thinking rather than one fixed bound, so if you search on the internet, there will be all kinds of inequalities that are called Chernoff Bounds. In addition, as far as the name goes, there is also the name Chernoff-Hoeffding Bounds; Chernoff first expounded this idea, using it on coin tossing, and later Hoeffding generalized it to more general cases.



The line of thinking of this bound is as follows:

1). Convert the inequality into the form of an exponential of e, and introduce a free variable

2). Use the Markov inequality

3). Apply various other known conditions, for example independence, for example other purely mathematical inequalities

4). Regard the bound as a function of t, and solve for the extremum of this function with t as the variable (find the minimum, so as to make the upper bound as low as possible)

Let us take the normal distribution as an example to demonstrate the above line of thinking:

X is a random variable satisfying $$N(\mu,\sigma^2)$$; then:

The probability we want to look at is:

$$
P(X\ge a)
$$

1). We change the above probability into:

$$
P(X \ge a) = P(e^{tX} \ge e^{ta}) \quad \quad where \quad \forall t>0 \tag{1}
$$

2). Use the Markov inequality

$$
P(e^{tX} \ge e^{ta}) \le \frac{E(e^{tX})}{e^{ta}}  \tag{2}
$$

Now, let us compute the mathematical expectation in formula (2)

$$
\begin{aligned}
	E(e^{tX}) & = \int_{-\infty}^{+\infty} e^{tx} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu)^2}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu)^2 - 2\sigma^2 tx}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{x^2 - 2\mu x +\mu^2 - 2\sigma^2 tx}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{x^2 - 2(\mu +\sigma^2 t) x  + \mu^2}{2\sigma^2}} dx \\ \\
	&= \int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x - (\mu +\sigma^2 t))^2 - (\mu +\sigma^2 t)^2 + \mu^2}{2\sigma^2}} dx \\ \\
	&= e^{ \frac{ (\mu +\sigma^2 t)^2 - \mu^2} {2\sigma^2} }\int_{-\infty}^{+\infty} \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x - (\mu +\sigma^2 t))^2 }{2\sigma^2}} dx \\ \\
	& = e^{ \frac{ (\mu +\sigma^2 t)^2 - \mu^2} {2\sigma^2}} \\ \\
	& = e^{\frac{\sigma^2 t^2}{2} + \mu  t}
\end{aligned}  \tag{3}
$$

Substituting formula (3) into formula (2):

$$
P(e^{tX} \ge e^{ta}) \le \frac{e^\frac{\sigma^2 t^2}{2} + \mu  t}{e^{ta}} = e^{\frac{\sigma^2 t^2}{2} + \mu  t - ta}  \tag{4}
$$

Our goal is to make the right-hand side of formula (4) as small as possible, that is, to make the upper bound of this probability as small as possible.

We find the minimum of the exponent part on the right-hand side of formula (4):

$$
\frac{\partial(\frac{\sigma^2 t^2}{2} + \mu  t - ta) } { \partial t} = \sigma^2 t + \mu  - a = 0  \tag{5}
$$

So we obtain:

$$
t = \frac{a-\mu}{\sigma^2}   \tag{6}
$$

Substituting (6) into (4) gives:

$$
P(e^{tX} \ge e^{ta}) \le e^{-\frac{(a-\mu)^2}{2\sigma^2}}  \tag{7}
$$

That is:

$$
P(X\ge a) \le  e^{-\frac{(a-\mu)^2}{2\sigma^2}}  \tag{8}
$$

Let us substitute the standard normal distribution $$N(0,1)$$ and take a look

$$
P(X\ge a) \le  e^{-\frac{a^2}{2}}  \tag{9}
$$

Here a > 0, because formula (6) requires t > 0

## Chernoff bounds: applied to the Poisson process
This article talks about deriving the Chernoff-Hoeffding Bounds based on the Poisson process.

Define the random variable:

$$
X_i =
\begin{cases}
	1 & p_i \\
	0 & 1-p_i
\end{cases}
$$

The above random variables are required to be mutually independent.

Define the random variable:

$$
X = \sum_{i=1}^n X_i
$$

Then we want to know the following probability:

$$
P(X \ge m)
$$

where m is greater than the mean (the mathematical expectation) of X and less than or equal to n.

The derivation is laid out below; the specific details will be explained in the video.

$$
P(X \ge m ) = P(e^{tx} \ge e^{tm}) \le \frac{E(e^{tx})}{e^{tm}}  \tag{1}
$$

where: t > 0

$$
E(e^{tx}) = E(e^{t\sum_{i=1}^n X_i}) = E(\prod_{i=1}^n e^{tX_i}) = \prod_{i=1}^n E(e^{tX_i})  \tag{2}
$$

where:

$$
E(e^{tX_i}) = p_i e^{t\times 1} + (1-p_i) e^{t\times 0} = p_i e^t + 1-p_i \tag{3}
$$

Substituting formula (3) into formula (2):

$$
E(e^{tx}) = \prod_{i=1}^n(p_i e^t + 1-p_i )  \tag{4}
$$

Using the Arithmatic Mean/Geometric Mean Inequality (the arithmetic-geometric mean inequality):

$$
\prod_{i=1}^n(p_i e^t + 1-p_i )  \le \left (    \frac{\sum_{i=1}^n(p_i e^t + 1-p_i ) }{n}               \right )^n  = (pe^t+1-p)^n  \tag{5}
$$

where: $$p = \frac{\sum_i=1^n p_i}{n}$$

Substituting formula (5) into formula (4):

$$
E(e^{tx}) \le (pe^t+1-p)^n  \tag{6}
$$

Substituting formula (6) into formula (1):

$$
P(X \ge m ) \le \frac{(pe^t+1-p)^n}{e^{tm}} = e^{ln \left \{  \frac{(pe^t+1-p)^n}{e^{tm}}  \right \} }
= e^{ n ln (pe^t+1-p)-tm}  \tag{7}
$$

Find the minimum of the right-hand side of formula (7):

$$
\frac {\partial \left [ n ln (pe^t+1-p)-tm \right ]}   { \partial t} = \frac{n}{pe^t+1-p} pe^t - m = 0
$$

After the derivation we have:

$$
e^t = \frac{(1-p)m}{(n-m)p}   \tag{8}
$$

So:

$$
t = ln \frac{(1-p)m}{(n-m)p}  \tag{9}
$$

Substituting formulas (8) and (9) into (7):

$$
\begin{aligned}
	P(X \ge m ) & \le e^{ n ln \{\frac{(1-p)m}{(n-m) }+ 1 - p\}  - m ln\frac{(1-p)m}{(n-m)p} } \\  \\
	& = e^{ n ln \frac{(1-p)n}{(n-m) }  - m ln\frac{(1-p)m}{(n-m)p} } \\  \\
	&= e^{n\left \{    ln \{\frac{(1-p)}{(1-\frac{m}{n}) }\}  - \frac{m}{n} ln\frac{(1-p) \frac{m}{n} }{(1-\frac{m}{n})p}   \right \}}   \\  \\
	&= e^{n\left \{   (1-\frac{m}{n}) ln \frac{(1-p)}{(1-\frac{m}{n}) }  - \frac{m}{n} ln\frac{\frac{m}{n} }{ p}   \right \}} \\
	&= e^{-n\left \{   (1-\frac{m}{n}) ln \frac{(1-\frac{m}{n})}{(1-p) }  + \frac{m}{n} ln\frac{\frac{m}{n}}{p }   \right \}} \\ \\
	&= e^{-n D(\frac{m}{n}||p)}    
\end{aligned}  \tag{10}
$$

Writing formula (10) in a form that is not an exponential of e:

$$
\begin{aligned}
	P(X \ge m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{11}
$$

## Chernoff bounds: applied to the Poisson process - a different form


m is greater than E(X); then:

$$
\begin{aligned}
	P(X \ge m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{1}
$$

Using the derivation process from the previous article, if m is less than E(X), then:

$$
\begin{aligned}
	P(X \le m ) & \le \left \{  \left [\frac{(1-\frac{m}{n})}{(1-p) } \right  ]^{(1-\frac{m}{n})}
	\left [ \frac{\frac{m}{n}}{p } \right  ]^{\frac{m}{n}}
	\right \} ^{-n}
\end{aligned}  \tag{2}
$$

The two formulas look the same.

If m is replaced by the following expression:

$$
m = \mu + \lambda   \tag{3}
$$

then: m is greater than E(X)

$$
\frac{m}{n} = \frac{\mu+\lambda}{n} = \frac{u}{n}+\frac{\lambda}{n} = p + \varepsilon
$$

m is less than E(X)

$$
\frac{m}{n} = \frac{\mu-\lambda}{n} = \frac{u}{n}-\frac{\lambda}{n} = p - \varepsilon
$$

Then formulas (1) and (2) can be written as: m is greater than E(X)

$$
\begin{aligned}
	P(X \ge m )=P(\frac{X}{n} \ge p+\varepsilon ) & \le \left \{  \left [\frac{(1-p-\varepsilon)}{(1-p) } \right  ]^{(1-p-\varepsilon)}
	\left [ \frac{p+\varepsilon}{p } \right  ]^{p+\varepsilon}
	\right \} ^{-n}
\end{aligned}  \tag{4}
$$

and: m is less than E(X)

$$
\begin{aligned}
	P(X \le m )=P(\frac{X}{n} \le p-\varepsilon ) & \le \left \{  \left [\frac{(1-p+\varepsilon)}{(1-p) } \right  ]^{(1-p+\varepsilon)}
	\left [ \frac{p-\varepsilon}{p } \right  ]^{p-\varepsilon}
	\right \} ^{-n}
\end{aligned}  \tag{5}
$$

## Proof That a Minimum Exists

$$
e^{nln(pe^t+1-p) -tm}  \tag{1}
$$

To prove that formula (1) has a minimum: since the exponential of e is a monotonic function, it is only necessary to prove that the exponent part has a minimum. Take the derivative of the exponent part:

$$
\frac{\partial ({nln(pe^t+1-p) -tm}) }{\partial t} = n \frac{1}{pe^t+1-p} p e^t - m  \tag{2}
$$

When 

$$
e^t = \frac{(1-p)m}{(n-m)p}  \tag{3}
$$

formula (2) equals 0. There is only one extremum point.

Take the derivative of formula (2) again:

$$
\frac{\partial(n \frac{1}{pe^t+1-p} p e^t - m)}{\partial t} = \frac{npe^t(pe^t+1-p) - npe^t pe^t}{(pe^t+1-p)^2} = \frac{npe^t(1-p)}{(pe^t+1-p)^2} > 0  \tag{4}
$$

Therefore, formula (1) attains its minimum at the point (3).

Code: please download it from GitHub: \url{https://github.com/taichiorange/leba_math}

![05-有最小值的证明.png](/figure/通信基础/05-有最小值的证明.png)

## Chernoff bounds: another derivation result

This article derives one more, different, Chernoff bounds.

$$
P(X \ge (1+\delta)\mu) = P(e^{tX} \ge e^{t(1+\delta)\mu})  \le \frac{E(e^{tX})}{e^{t(1+\delta)\mu}}  \tag{1}
$$

where $$\delta > 0$$ and 

$$\mu = E(X)  = E(\sum_{i=1}^n X_i) = \sum_{i=1}^n E(X_i) = \sum_{i=1} p_i$$

Deriving further the mathematical expectation in formula (1):

$$
E(e^{tX})=E(e^{t\sum_{i=1}^n X_i}) = \prod_{i=1}^n E(e^{t X_i}) \tag{2}
$$

where

$$
E(e^{t X_i}) = p_i e^{t\times 1} + (1-p_i)e^{t\times 0} = p_i e^t + 1-p_i = 1 + p_i (e^t -1 )  \tag{3}
$$

Starting from this step, a scaling inequality different from that in the previous video/article is used; last time the algebraic-geometric mean inequality was used, this time we use:

$$
1 + x \le e^x, \quad \text{when}  \quad  x \ge 0
$$

Therefore, in formula (3):

$$
1 + p_i (e^t -1 ) \le e^{p_i (e^t -1 )}  \tag{4}
$$

Substituting formula (4) into formula (3):

$$
E(e^{t X_i}) \le e^{p_i (e^t -1 )} \tag{5}
$$

Substituting formula (5) into formula (2):

$$
E(e^{tX}) \le \prod_{i=1}^n e^{p_i (e^t -1 )} = e^{ (e^t -1 ) \sum_{i=1}^n p_i } = e^{\mu (e^t -1 )} \tag{6}
$$

Substituting formula (6) into formula (1):

$$
P(X \ge (1+\delta)\mu) \le \frac{e^{\mu (e^t -1 )}}{e^{t(1+\delta)\mu}} = e^{\mu [e^t -1 - t(1+\delta) ]} \tag{7}
$$

Find the minimum of formula (7):

$$
\frac{\partial{\mu [e^t -1 - t(1+\delta) ]}}{\partial t} = e^t - (1+\delta) = 0  \\ \\
e^t = 1 + \delta \\
t = ln(1+\delta)
\tag{8}
$$

Substituting formula (8) into formula (7):

$$
P(X \ge (1+\delta)\mu) \le e^{\mu[1+\delta - 1 - (1+\delta)(ln(1+\delta))]} = e^{\mu[\delta  - (1+\delta)(ln(1+\delta))]}  \\
= \left (\frac{e^\delta}{(1+\delta)^{(1+\delta)}} \right )^\mu   \tag{9}
$$

Starting from here, there are two further methods for scaling

Method 1:

If $$\delta > 2e-1$$

then $$1 + \delta > 2e$$  ,  substituting into formula (9)

$$
P(X \ge (1+\delta)\mu) < \left (\frac{e^\delta}{(2e)^{(1+\delta)}} \right )^\mu < \left (\frac{e^\delta}{(2e)^\delta} \right )^\mu  = e^{-\mu \delta}
$$

Method 2:

Because $$ln(1+\delta) \ge \frac{\delta}{1+\delta/2}$$

therefore, formula (9)

$$
P(X \ge (1+\delta)\mu) \le  e^{\mu[\delta  - (1+\delta)(\frac{\delta}{1+\delta/2})]} =  e^{-\mu \frac{\delta^2}{1+\delta}}
$$

For $$P(X \le (1-\delta)\mu)$$ it can be proved with the same method: replacing the earlier t with -t, the result is that the $$\delta$$ in formula (9) is replaced with $$-\delta$$, and we can obtain:

$$
P(X \le (1-\delta)\mu)  \le \left (\frac{e^{-\delta}}{(1-\delta)^{(1-\delta)}} \right )^\mu   \tag {10}
$$

Then use:

$$
\begin{aligned}
	ln(1-\delta) &= -\delta - \frac{\delta^2}{2} - \frac{\delta^3}{3}-\cdots  \\
	(1-\delta)ln(1-\delta) = &-\delta - \frac{\delta^2}{2} - \frac{\delta^3}{3}-\cdots  \\
	&\quad \quad + \delta^2 + \frac{\delta^3}{2} + \frac{\delta^4}{3}+\cdots \\
	& = -\delta + \frac{\delta^2}{2} +  \frac{\delta^3}{6} + \cdots \\
	& \ge -\delta + \frac{\delta^2}{2}
\end{aligned}
$$

Then:

$$
(1-\delta)^{(1-\delta)} \ge e^{-\delta + \frac{\delta^2}{2}}  \tag {11}
$$

Substituting (11) into (10):

$$
P(X \le (1-\delta)\mu)  \le  \left (\frac{e^{-\delta}}{e^{-\delta + \frac{\delta^2}{2}}  } \right )^\mu    = e^{-\frac{\delta^2}{2}\mu} \tag {12}
$$

It can be seen that in the end both can decay exponentially as $$\delta$$ increases.