---
layout: default
title: "The BCJR Decoding Algorithm for Convolutional Codes (Part Two)"
lang: en
back_url: /index.html?lang=en
---
## The BCJR Decoding Algorithm for Convolutional Codes (Part Two) -- Computing $$\gamma$$ 
The previous article has already derived the following conditional probability of a state transition; this probability is the basis for further computing the a posteriori probability of the transmitted bit.

$$
P(\psi_t=p,\psi_{t+1}=q|r)  = \frac{1}{p(r)} \times    p( \psi_t=p , r_{r<t}) \times p(\psi_{t+1}=q, r_t |  \psi_t=p) \times  p(r_{r>t} | \psi_{t+1}=q )                 \tag{1}
$$

For convenience of expression later on, we denote the three parts on the right-hand side of the equals sign in (1) respectively as:

$$
\begin{aligned}
	\alpha_t(p) &=  p( \psi_t=p , r_{r<t})   \\
	\gamma_t(p,q) &=  p(\psi_{t+1}=q, r_t |  \psi_t=p) \\
	\beta_{t+1}(q) &= p(r_{r>t} | \psi_{t+1}=q )
\end{aligned}      \tag{2}
$$

Then Formula (1) can be written in the abbreviated form

$$
P(\psi_t=p,\psi_{t+1}=q|r) = \alpha_t(p) \gamma_t(p,q) \beta_{t+1}(q)
$$

![alpha_gamma_beta.png](/figure/卷积码编码和译码/BCJR-turbo/alpha_gamma_beta.png)


The second one in Formula (2) can in fact be computed; let us carry out a derivation:

$$
\begin{aligned}
	\gamma_t(p,q)
	&=  p(\psi_{t+1}=q, r_t |  \psi_t=p) \\
	&= p( r_t | \psi_{t+1}=q,  \psi_t=p) p(\psi_{t+1}=q|\psi_t=p)
\end{aligned}      \tag{3}
$$

where

$$
p(\psi_{t+1}=q|\psi_t=p) = p(X_t = x_t^{p,q})    \tag{4}
$$

The meaning of Formula (4) is the probability that, when the state at instant t is p, it transitions to state q at instant t+1; that is, at instant t, the input bit is the value that makes the state go from p to q. For example, at instant 6, the state is 1, then the probability of going to state 2 at instant 7 is the probability that the input bit at instant 6 is 0, because inputting 0 can make the state go from 1 to 2.  Generally an equiprobable assumption is made, so this is generally just 1/2.

Let us now look at the other part in Formula (3):

$$
p( r_t | \psi_{t+1}=q,  \psi_t=p) = p( r_t | X_t = x_t^{p,q} )  =p( r_t | X_t = v_t^{p,q} ) =p( r_t | a )  \tag{5}
$$

The meaning of the above is the probability that, under the condition of going from state p at instant t to state q at instant t+1, what is received is $$r_t$$. ”Going from state p at instant t to state q at instant t+1 “ corresponds to one input bit, which we denote as $$x_t^{p,q}$$; at this time, it corresponds to one output $$v_t^{p,q}$$, and after modulation the data obtained is a.  The data a is sent out through a Gaussian Gaussian white noise channel, and several data items are sent out through the channel one after another:

$$
r_t^{(i)} = a^{(i)} + n_t
$$

Each of them follows a Gaussian distribution with mean 0 and variance $$\sigma^2$$:

$$
p(r_t^{(i)}|a^{(i)} ) = \frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{ (r_t^{(i)}-a^{(i)})^2 }{2\sigma^2}}
$$

Several data items, that is, how many bits of output one bit of convolutional code output produces, which we denote as Q, then Formula (5) is:

$$
p( r_t | a ) =  \frac{1}{ (2\pi \sigma^2)^{Q/2}} e^{-\frac{ \sum_{i=1}^Q (r_t^{(i)}-a^{(i)})^2 }{2\sigma^2}}
$$

Let us take an example to illustrate, for instance t=6, that is, instant 6. The state goes from 1 to 2, then the probability we compute is:

$$
\begin{aligned}
	\gamma_6(1,2)
	&=  p(\psi_7=2, r_6 |  \psi_6=1) \\
	&= p( r_6 | \psi_7=2,  \psi_6=1) p(\psi_7=2|\psi_6=1)  \\
	&= p( r_6 | \psi_7=2,  \psi_6=1) p(X_6=0)  \\
	&= p( r_6 | \psi_7=2,  \psi_6=1) \times \frac{1}{2} \\
\end{aligned}      \tag{6}
$$

where:

$$
\begin{aligned}
	p( r_6 | \psi_7=2,  \psi_6=1)
	&= p(r_6^{(0)}, r_6^{(1)} | X_6 = 0) \\
	&= p(r_6^{(0)}, r_6^{(1)} | v_6^{(0)} = 0, v_6^{(0)} = 1)  \quad (convolutional encoding)\\
	&= p(r_6^{(0)}, r_6^{(1)} | a_6^{(0)} = -1, a_6^{(0)} = +1)  \quad (modulation)\\
	&= p(r_6^{(0)}| a_6^{(0)} = -1) \times p( r_6^{(1)} | a_6^{(0)} = +1)  \quad (mutually independent)\\
	\\
	&=  \frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{ (r_6^{(0)}-(-1))^2 }{2\sigma^2}}   \times
	\frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{ (r_6^{(1)}-(+1))^2 }{2\sigma^2}}   \\
	\\
	&= \frac{1}{2\pi \sigma^2}  e^{-\frac{ (r_6^{(0)}-(-1))^2 +(r_6^{(1)}-(+1))^2 }{2\sigma^2}}  \\
	\\
	& \propto  e^{-\frac{ (r_6^{(0)}+1)^2 +(r_6^{(1)}-1)^2 }{2\sigma^2}}
\end{aligned}      \tag{7}
$$

Substituting (7) back into (6) allows the computation to be done. In the course of the computation, we ignore both the 1/2 in Formula (6) and the $$\frac{1}{2\pi \sigma^2}$$ in Formula (7) and leave them out of the computation, because these are all unchanging during the computation, and in the end, when the probabilities are normalized, they can be eliminated.