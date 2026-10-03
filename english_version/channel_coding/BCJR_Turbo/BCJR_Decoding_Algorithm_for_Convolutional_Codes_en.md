---
layout: default
title: "The BCJR Decoding Algorithm for Convolutional Codes (Part One)"
lang: en
back_url: /index.html?lang=en
---
## The BCJR Decoding Algorithm for Convolutional Codes (Part One)
This article mainly discusses the BCJR decoding algorithm for convolutional codes. One needs to know the basic principles of convolutional codes and some knowledge of probability. We will derive the formulas of the decoding algorithm, and explain the BCJR decoding process with a concrete example.

We know that a convolutional code is a kind of state machine; the data in the registers of the convolutional code encoder is the state, and under the condition that the current state is known, the output of the current state is only related to the current input, and is unrelated to the past states and inputs -- this is the Markov property.

When the received data is:

$$
r=(r_0,r_1,\cdots, r_N)
$$

then, in order to decode the transmitted bit at instant t, we compute this a posteriori probability

$$
P(X_t = x|r)
$$

At instant t, the current state is

$$
\psi_t=p
$$

The next state is

$$
\psi_{t+1} = q
$$

We denote the set of state transitions when the input is 0 as

$$
S_0
$$

We denote the set of state transitions when the input is 1 as

$$
S_1
$$

Then:

$$
P(X_t=0|r) = \sum_{S_0}P(\psi_t,\psi_{t+1}|r)
$$

and

$$
P(X_t=1|r) = \sum_{S_1}P(\psi_t,\psi_{t+1}|r)
$$

So, the key point is to compute the following probability:

$$
P(\psi_t=p,\psi_{t+1}=q|r)
$$

Let us carry out a derivation:

$$
\begin{aligned}
	P(\psi_t=p,\psi_{t+1}=q|r) = \frac{p(\psi_t=p,\psi_{t+1}=q,r)}{p(r)}  \\
\end{aligned}
$$

Therefore, we need to compute this joint probability:

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r)
\end{aligned}
$$

We divide r into three parts: one part is the data received before instant t, one part is the data received at instant t, and one part is the data received after instant t:

$$
r = r_{<t}  \cup r_t \cup  r_{>t}
$$

Then:

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r)
	&= p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t, r_{>t})  \\
	\\
	&= p(r_{>t} | \psi_t=p,\psi_{t+1}=q,r_{<t}, r_t ) p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)    \quad \quad  \text{(conditional probability)}
	\\
	\\
	&= p(r_{>t} | \psi_{t+1}=q ) p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)    \quad \quad  \text{(Markov property)}
\end{aligned}   \tag{1}
$$

Let us further analyze the latter part of Formula (1)

$$
\begin{aligned}
	p(\psi_t=p,\psi_{t+1}=q,r_{<t}, r_t)  &= p(\psi_{t+1}=q, r_t |  \psi_t=p, r_{<t}) p( \psi_t=p , r_{<t})  \\
	\\
	&=p(\psi_{t+1}=q, r_t |  \psi_t=p) p( \psi_t=p , r_{<t})    \quad \quad  \text{(Markov property)}
\end{aligned}
\tag{2}
$$

Substituting (2) into (1) we have:

$$
p(\psi_t=p,\psi_{t+1}=q,r)  =p( \psi_t=p , r_{<t}) p(\psi_{t+1}=q, r_t |  \psi_t=p)  p(r_{>t} | \psi_{t+1}=q ) \tag{3}
$$

Let us look at the meaning of Formula (3):
We want to analyze the joint probability that the current state is p, the next state is q, and the received data is r; then this probability is obtained as the product of three parts:
(1) The probability that the data received before instant t is $$r_{<t}$$ and that the state at instant t, having been reached, is p:

$$
p( \psi_t=p , r_{<t})
$$

(2) The probability that, under the condition that the state at instant t is p, the next state reached is q and the data received is $$r_t$$

$$
p(\psi_{t+1}=q, r_t |  \psi_t=p)
$$

(3) The probability that, under the condition that the state at instant t+1 is q, the output after instant t is $$r_{>t}$$

$$
p(r_{>t} | \psi_{t+1}=q )
$$

Then:

$$
P(\psi_t=p,\psi_{t+1}=q|r)  = \frac{1}{p(r)}   p( \psi_t=p , r_{<t}) p(\psi_{t+1}=q, r_t |  \psi_t=p)  p(r_{>t} | \psi_{t+1}=q )
$$

Let us take an example to illustrate:

We use the convolutional code:

$$
G(x) = \frac{1}{1+x^2}
$$

Then the structure is as follows:


![convolutionary_code_encoder_1.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_code_encoder_1.png)


It can also be drawn as the figure below; the two are equivalent:


![convolutionary_code_encoder_2.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_code_encoder_2.png)


Then its state transition trellis diagram is:

![convolutionary_encoder_state_transition.png](/figure/卷积码编码和译码/BCJR-turbo/convolutionary_encoder_state_transition.png)


Suppose we want to encode 10 bits, starting from state 00 and returning to state 00 after the encoding is finished; then all the possible paths are:

![encoder_10bits_trellis.png](/figure/卷积码编码和译码/BCJR-turbo/encoder_10bits_trellis.png)


Suppose the bits we encode are  x=[1, 1, 0, 0, 1, 0,  1, 0, 1, 1]

Then the output bits are  V=[11  11  01  01  10  01  11   01  10  10]

The encoding path is shown in red in the figure above.



Suppose we want to compute the following probability

$$
P(X_6=1|r_0,r_1,\cdots,r_9)
$$

Note that each $$r_i$$ in the formula above is in fact two received data items, corresponding respectively to the data obtained after $$v_t^{(0)}, v_t^{(1)}$$ have been transmitted through the channel (a slight note here: we use BPSK, so 0--> -1,  1---->1 , and what is transmitted is -1 or +1).

When the input is the bit 1, the corresponding state transitions have the following several cases:

$$
\begin{aligned}
	\psi_6=0 \quad \quad  ---->  \psi_7=2 \\
	\psi_6=1 \quad \quad  ---->  \psi_7=0 \\
	\psi_6=2 \quad \quad  ---->  \psi_7=3 \\
	\psi_6=3 \quad \quad  ---->  \psi_7=1 \\
\end{aligned}
$$

So:

$$
\begin{aligned}
	P(X_6=1|r_0,r_1,\cdots,r_9) = & P(\psi_6=0,\psi_7=2|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=1,\psi_7=0|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9)+  \\
	& P(\psi_6=3,\psi_7=1|r_0,r_1,\cdots,r_9)
\end{aligned}
\tag{4}
$$

Then any one of the summations in Formula (4) can be expanded according to the example below; we take $$P(\psi_6=2,\psi_7=3\vert r_0,r_1,\cdots,r_9)$$ as the example:

$$
\begin{aligned}
	P(\psi_6=2,\psi_7=3|r_0,r_1,\cdots,r_9) &= p( \psi_6=2 , r_{<6}) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_{>6} | \psi_7=3 ) \\
	&=p( \psi_6=2 , r_0,r_1,\cdots,r_5) p(\psi_7=3, r_6 |  \psi_6=2)  p(r_7,\cdots,r_9 | \psi_7=3 )
\end{aligned}
$$