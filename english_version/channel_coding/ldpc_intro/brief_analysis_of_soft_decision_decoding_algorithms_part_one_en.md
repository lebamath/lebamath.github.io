---
layout: default
title: "A Brief Analysis of Soft-Decision Decoding Algorithms (Part One)"
lang: en
back_url: /index.html?lang=en
---

## A Brief Analysis of Soft-Decision Decoding Algorithms (Part One)

The purpose of this article is to introduce the soft-decision decoding of LDPC codes in a simple and easy-to-understand way. When I first started learning LDPC codes, the greatest confusion I met was  "message passing'' in soft-decision decoding. Many books give the concrete description of the "message passing'' algorithm right at the very beginning, but why does the "message passing'' algorithm follow that particular procedure, and why does it work? Is there any principle (mathematical principle) behind it? Patient and detailed explanations are rare. Fortunately, I dug out a book [1] which gives a rather good introduction, and this article is written with the content of that book as its blueprint.

Provided that the reader is already familiar with the basic concepts of linear block codes and with the parity-check matrix, and has basic knowledge of probability, this article can be understood. It is suggested that, before reading this article, the reader read the one I wrote about the bit-flipping decoding algorithm, so as to get a feel for the underlying idea, which helps in understanding this article. Of course, this article can also be fully understood without reading the article on the bit-flipping decoding algorithm.



Suppose the parity-check matrix used by the LDPC code is as follows; this article takes this parity-check matrix as its example throughout.

$$
A = \begin{bmatrix}
	1& 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1\\
	1& 0 & 1 & 0 & 1 & 1 & 0 & 1 & 1 & 0\\
	0& 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1 & 1\\
	0& 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1\\
	1& 1 & 0 & 1 & 0 & 0 & 1 & 1 & 1 & 0
\end{bmatrix}
$$

The codeword we decode is written in vector form:

$$
c=\begin{bmatrix}
	c_1 & c_2 & c_3 & c_4 & c_5 & c_6 & c_7  & c_8 & c_9 & c_{10}
\end{bmatrix}
$$

This decoded codeword does not refer to the correct codeword that was transmitted; it denotes the codeword we are about to decode. For example, we may want to evaluate the likelihood (probability) that $$c_1=1$$.

We represent the received data as a vector:

$$
r=\begin{bmatrix}
	r_1 & r_2 & r_3 & r_4 & r_5 & r_6 & r_7  & r_8 & r_9 & r_{10}
\end{bmatrix}
$$

A special note here: the received data spoken of here are not the 0s or 1s that have already been hard-decided, but the decimal numbers that come through the channel. What the transmitter sends is 0 or 1, but because of the disturbance of channel noise, the received data can be anything: everything from negative infinity to positive infinity is possible, only that the further it deviates from the originally transmitted data, the smaller the possibility. If what was sent is 1, then the possibility of receiving 105.7 is far smaller than the possibility of receiving 1.1. For example, the data that might be received are as follows:

$$
\begin{aligned}
	r&=\begin{bmatrix}
		r_1 & r_2 & r_3 & r_4 & r_5 & r_6 & r_7  & r_8 & r_9 & r_{10}   
	\end{bmatrix}
	\\
	&=[-0.63\quad -0.81\quad -0.73\quad -0.04\quad 0.1\quad 0.95\quad -0.76\quad 0.66\quad -0.55\quad 0.58]
\end{aligned}
$$

According to the characteristics of the channel, for instance an additive white Gaussian noise channel, from the decimal numbers received above we can compute an a posteriori probability. For example, we can compute the probability that $$c_1=0$$ under the condition that $$r_1=-0.63$$ was received; written as a probability formula:

$$
p(c_1=1|r1=-0.63)
$$

We use $$p(c_1|r_1)$$ to denote this kind of a posteriori probability. 

If we directly use this a posteriori probability to decide whether $$c_1=0$$ or $$c_1=1$$, then we make no use of the fact that the individual bits inside the codeword are correlated, and thus gain no benefit from the coding.

So, what correlation properties are there? That is, what constraints are there?

According to the relevant principles of linear block codes, we know that the following parity-check equation holds:

$$
z = c A^T
$$

The computed result $$z$$ is a vector containing 5 elements, denoted as:

$$
z = \begin{bmatrix}
	z_1& z_2 & z_3 & z_4 & z_5 
\end{bmatrix}
$$

For ease of understanding, we expand the above equation in matrix form into 5 parity-check equations:


$$
\begin{aligned}
	z_1 &= c_1 + c_2 + c_3 + c_6 + c_7 + c_{10} \\
	z_2 &= c_1 + c_3 + c_5 + c_6 + c_8 + c_{9} \\
	z_3 &= c_3 + c_4 + c_5 + c_7 + c_9 + c_{10} \\
	z_4 &= c_2 + c_4 + c_5 + c_6 + c_8 + c_{10} \\
	z_5 &= c_1 + c_2 + c_4 + c_7 + c_8 + c_{9} 
\end{aligned}
$$





If the decoding is correct, then all the equations are equal to 0. If it is not the correct codeword, then at least one of the parity-check equations above is not equal to 0.

Since soft decision does not directly decide the codeword and then check whether the parity-check equations are satisfied. Using soft information, let us judge the probability that each parity-check equation holds, and then, having the probability that each equation holds, compute the probability that each bit $$c_i$$ in the codeword equals 0 or equals 1.



Let us derive this step by step.

We know that optimal decoding is to find the maximum of the following probability:

$$
p(\{c_i\}|\{z_m=0\},r)
$$

Explanation: $$\{z_m=0\}$$ means that all parity-check equations hold.

For example: under the condition that the parity-check equations are satisfied and the received data are $$r$$

$$
c = [0 \quad 0 \quad 0\quad 1\quad 0 \quad 1 \quad 0 \quad 1 \quad 0 \quad 1]
$$

then the probability can be computed

$$
p(c = [0 \quad 0 \quad 0\quad 1\quad 0 \quad 1 \quad 0 \quad 1 \quad 0 \quad 1] |\{z_1=0,z_2=0,z_3=0,z_4=0,z_5=0\},\{r_1,r_2,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10}\})
$$

Note: in the formula above, $$r$$ is a vector containing 10 elements, which are the received data.

Then, compute the probability of the expression above once for every $$c$$ that satisfies the parity-check equations, and among these probabilities choose the largest one as the decoding result.

Drawback: if the number of data bits in each block of the block code is k and the length after encoding is n (for example, in the above example k = 5, n = 10), then $$2^k$$ probabilities have to be computed. For block codes with lengths on the order of a few hundred, this amount of computation is enormously large and completely infeasible. Therefore, a fast compromise algorithm is needed.

The first compromise: let us start from the angle of optimality for a single bit, and solve for the following probability:

$$
p(c_i=x|\{z_m=0\},r)
$$

For binary transmitted data, the x in the formula above takes either 0 or 1.

Let us carry out some derivation of the formula above, deriving it into a form in which this probability is expressed by the probability that $$z_m=0$$, that is, using the probability that the parity-check equations hold to express the probability that this bit equals 0 or 1.

$$
\begin{aligned}
	p(c_i=x|\{z_m=0\},r) &=\frac{p(c_i=x,\{z_m=0\}|r)}{p(\{z_m=0\}|r)}\\
	&=\frac{p(\{z_m=0\}|c_i=x,r)p(c_i=x|r)}{p(\{z_m=0\}|r)}
\end{aligned}
$$

In the formula above, the denominator part is a term unrelated to $$c_i=x$$ and can be regarded as a constant.

For the two terms in the numerator, further simplifications are made (which are in fact a kind of approximation)

First:  let $$p(c_i=x|r) = p(c_i=x|r_i)$$, that is, assume that the other received data (apart from $$r_i$$) do not affect the decision that $$c_i=x$$. This is of course a simplification, because, considering the constraints of the parity-check equations, the values of the other data can give more information for the current judgment of $$c_i=x$$. This probability has nothing to do with the LDPC code any more; it is determined only by the characteristics of the channel.

Second: assume that the parity-check equations in which $$c_i$$ participates are, under the condition that $$c_i=x$$ and that r has been received, independent of each other, statistically independent. This holds only in the case where, among these parity-check equations, there is no other jointly participating bit apart from $$c_i$$. In the parity-check matrix of an actual LDPC code, this cannot be guaranteed. Therefore, this is also a simplification. Under this assumption, the term in the numerator can be expanded as:

$$
p(\{z_m=0\}|c_i=x,r) = \prod_m p(z_m=0|c_i=x,r)
$$

Then:

$$
\begin{aligned}
	p(c_i=x|\{z_m=0\},r) &=\frac{1}{p(\{z_m=0\}|r)}p(c_i=x|r_i)\prod_m p(z_m=0|c_i=x,r)-------\text{Formula (0)}
\end{aligned}
$$

At this point we have arrived at a fairly important step, or one may say we have obtained a fairly important idea:

Use the probability that the parity-check equations hold to express, or rather to compute, the probability that $$c_i=0$$. This is somewhat similar to the idea of the bit-flipping algorithm; again in the bit-flipping algorithm, the number of parity-check equations that do not hold determines the possibility that the bits participating in these parity-check equations are in error. The larger the number of erroneous parity-check equations a bit takes part in, the greater the possibility that this bit is in error. In the formula above, the shadow of this idea can also be seen. The greater the possibility that the parity-check equations hold, the greater the possibility that $$c_i=0$$ can be seen to be.

Let us take an example to understand the formula above. In the LDPC parity-check equations given earlier, let us consider that we are estimating the probability that $$c_2=0$$; the parity-check equations in which $$c_2$$ participates are the three ones $$z_1,z_4,z_5$$.

Then the estimate of the probability that $$c_2=0$$ can be expressed as:

$$
\begin{aligned}
	p(c_2=0|\{z_1=0,z_4=0,z_5=0\},r) &=\frac{p(c_2=0,\{z_1=0,z_4=0,z_5=0\}|r)}{p(\{z_1=0,z_4=0,z_5=0\}|r)}\\
	&=\frac{p(\{z_1=0,z_4=0,z_5=0\}|c_2=0,r)p(c_2=0|r)}{p(\{z_1=0,z_4=0,z_5=0\}|r)}
\end{aligned}
$$

And the first probability on the left of the numerator part in the expression above can, under the assumption that $$z_1,z_4,z_5$$ are mutually independent, be expanded into the product of three probabilities:

$$
p(\{z_1=0,z_4=0,z_5=0\}|c_2=0,r) = p(z_1=0|c_2=0,r)*p(z_4=0|c_2=0,r)*p(z_5=0|c_2=0,r)
$$

=================================

If everything so far is understood, we can start deriving further. Now let us look at how to judge the probability that a parity-check equation holds, that is

$$
p(z_m=0|c_i=x,r)
$$

How is this computed? Or rather, how is it estimated?

Let us interpret the formula above. This formula estimates the probability that $$z_m=0$$ holds, that is, the possibility that the m-th parity-check equation holds. We can quite naturally understand that this probability must be related to the bits contained in the parity-check equation $$z_m$$; and since the preceding formula is a conditional probability, one of whose conditions is $$c_i=x$$, which is fixed, in evaluating the probability above we only need to consider those bits contained in this parity-check equation other than $$c_i$$.

Below, we carry out some derivation of formulas so as to take the other participating bits into account:

First we use the total probability formula:

$$
\begin{aligned}
	p(z_m=0|c_i=x,r) = \sum_{x^{'}} p(z_m=0,\{c_{n^{'}}=x^{'}\}|c_n=x,r) \\
	= \sum_{x^{'}} p(z_m=0|c_n=x,\{c_{n^{'}}=x^{'}\},r)p(\{c_{n^{'}}=x^{'}\}|r) \\
	-------------  \text{Formula (1)}
\end{aligned}
$$

For the three probabilities on the right-hand side of the formula above, use the total probability formula to expand each of them. Now we expand the first one ( $$p(z_1=0|c_2=0,r)$$ ) as an example for illustration. In the parity-check equation $$z_1$$, besides $$c_2$$ which participates, the 5 bits $$c_1,c_3,c_6,c_7,c_{10}$$ also participate. Now enumerate all the value combinations of these 5 bits, each taking 0 or 1, giving $$2^5=32$$ cases:

$$
\begin{aligned}
	&p(z_1=0|c_2=0,r) =\\
	&p(z_1=0|c_2=0,\{c_1=0,c_3=0,c_6=0,c_7=0,c_{10}=0\},r)p(\{c_1=0,c_3=0,c_6=0,c_7=0,c_{10}=0\}|r) + \\
	&p(z_1=0|c_2=0,\{c_1=0,c_3=0,c_6=0,c_7=1,c_{10}=0\},r)p(\{c_1=0,c_3=0,c_6=0,c_7=1,c_{10}=0\}|r) + \\
	&p(z_1=0|c_2=0,\{c_1=0,c_3=0,c_6=0,c_7=1,c_{10}=1\},r)p(\{c_1=0,c_3=0,c_6=0,c_7=1,c_{10}=1\}|r) + \\
	&p(z_1=0|c_2=0,\{c_1=0,c_3=0,c_6=1,c_7=0,c_{10}=0\},r)p(\{c_1=0,c_3=0,c_6=1,c_7=0,c_{10}=0\}|r) + \\
	&\dots \dots   \\
	&p(z_1=0|c_2=0,\{c_1=1,c_3=1,c_6=1,c_7=1,c_{10}=1\},r)p(\{c_1=1,c_3=1,c_6=1,c_7=1,c_{10}=1\}|r)  
\end{aligned}
$$

From the example given above it is easy to see that, under the condition $$c_2=0$$, in order to require $$z_1=0$$, one can see that not all of the $$2^5=32$$ cases above hold; it holds only when there is an even number of 1s among the 5 bits $$c_1,c_3,c_6,c_7,c_{10}$$.

In addition, for $$p(z_m=0|c_n=x,\{c_{n^{'}}=x^{'}\},r)$$ in Formula (1), when all the $$c_i$$ are fixed, $$z_m=0$$ is already unrelated to r, therefore:

$$p(z_m=0|c_n=x,\{c_{n^{'}}=x^{'}\},r) = p(z_m=0|c_n=x,\{c_{n^{'}}=x^{'}\}$$



Therefore Formula (1) can be written as:

$$
p(z_m=0|c_i=x,r) = \sum_{x^{'}\text{ summing to x} } p(\{c_{n^{'}}=x^{'}\}|r)----------\text{Formula (2)}
$$

A supplementary explanation of the formula above: the $$n^{'}$$ in it ranges over the bits participating in the parity-check equation $$z_m$$, apart from $$c_i$$. For example, in the first parity-check equation $$z_1$$ of our example, the participating bits are $$c_1,c_2,c_3,c_6,c_7,c_{10}$$; if $$c_i$$ is $$c_2$$ and x=0, that is $$c_2=0$$, then the values taken by $$n^{'}$$ are $$\{1,3,6,7,10\}$$. Then, on the right-hand side of the formula above, what needs to be considered is $$\{ c_1=x_1^{'},c_3=x_3^{'},c_6=x_6^{'},c_7=x_7^{'},c_{10}=x_{10}^{'}\}$$, and then the values of these 5 bits must satisfy $$0=x_1^{'}+x_3^{'}+x_6^{'}+x_7^{'}+x_{10}^{'}$$. Then the formula above can be expanded as:

$$
\begin{aligned}
	p(z_1=0|c_2=0,r) =& p(c_1=0,c_3=0,c_6=0,c_7=0,c_{10}=0|r)+  \\
	& p(c_1=1,c_3=0,c_6=0,c_7=0,c_{10}=1|r)+  \\
	& p(c_1=1,c_3=0,c_6=0,c_7=1,c_{10}=0|r)+  \\
	& p(c_1=1,c_3=0,c_6=1,c_7=0,c_{10}=0|r)+  \\
	& p(c_1=1,c_3=1,c_6=0,c_7=0,c_{10}=1|r)+  \\
	& p(c_1=0,c_3=1,c_6=0,c_7=0,c_{10}=1|r)+  \\
	& p(c_1=0,c_3=1,c_6=0,c_7=1,c_{10}=0|r)+  \\
	& p(c_1=0,c_3=1,c_6=1,c_7=0,c_{10}=0|r)+  \\
	& p(c_1=0,c_3=0,c_6=1,c_7=0,c_{10}=1|r)+  \\
	& p(c_1=0,c_3=0,c_6=1,c_7=1,c_{10}=0|r)+  \\
	& p(c_1=0,c_3=0,c_6=0,c_7=1,c_{10}=1|r)+  \\
	& p(c_1=0,c_3=1,c_6=1,c_7=1,c_{10}=1|r)+  \\
	& p(c_1=1,c_3=0,c_6=1,c_7=1,c_{10}=1|r)+  \\
	& p(c_1=1,c_3=1,c_6=0,c_7=1,c_{10}=1|r)+  \\
	& p(c_1=1,c_3=1,c_6=1,c_7=0,c_{10}=1|r)+  \\
	& p(c_1=1,c_3=1,c_6=1,c_7=1,c_{10}=0|r)  \\
\end{aligned}
$$

For further simplification, one more assumption is made about Formula (2), namely that the $$\{c_{n^{'}}\}$$ are mutually independent; then Formula (2) can become:

$$
p(z_m=0|c_i=x,r) = \sum_{x^{'} summing to x} \prod_{\{n^{'}\}} p(c_{n^{'}}=x^{'}|r)----------Formula (3)
$$

For $$p(c_1=0,c_3=0,c_6=0,c_7=0,c_{10}=0|r)$$ in the example above, assuming $$c_1,c_3,c_7,c_{10}$$ are mutually independent, then:

$$
p(c_1=0,c_3=0,c_6=0,c_7=0,c_{10}=0|r) = p(c_1=0|r)p(c_3=0|r)p(c_6=0|r)p(c_7=0|r)p(c_{10}=0|r)
$$

At this point, the $$p(c_{n^{'}}=x^{'}|r)$$ in Formula (3) can be replaced by $$p(c_{n^{'}}=x^{'}|r_{n^{'}})$$ — obviously an approximation is used here as well — and then Formula (3) can be computed, and substituting it into Formula (0), $$p(c_i=x|\{z_m=0\},r)$$ can be computed.  At this moment a decision can be made on the bit $$c_i$$:

$$
If\quad p(c_i=0|\{z_m=0\},r) > 0.5, then\quad c_i=0,\quad otherwise\quad c_i=1
$$

Then, carrying out the above procedure once for all the $$c_i$$, $$c_1,\dots,c_{10}$$ are all decided. Because several assumptions and simplifications were used in the computation above, when the results decided this time are substituted into the parity-check equations, it may be that not all the parity-check equations hold. In that case, we must take some measures so as to make another attempt. If we simply run through the above procedure once more, we will still get the same result, so we naturally think: can we use some parameters estimated in the previous round to make this round's attempt? Using the results of the previous round, can some of the estimated probabilities be made more accurate?

Let us look at Formula (3). For the multiplication part $$p(c_{n^{'}}=x^{'}|r)$$ on the right-hand side of Formula (3), in the first round it was approximated by $$p(c_{n^{'}}=x^{'}|r_{n^{'}})$$; here the possibility that the parity-check equations in which $$c_{n^{'}}$$ participates hold was not taken into account. If this factor is taken into account, we can imagine that the accuracy of the estimate of $$c_{n^{'}}$$ should be improved. Therefore, we can use the following formula to further improve the accuracy of the estimate:

$$
p(c_{n^{'}}=x^{'}|r_{n^{'}}) \approx p(c_{n^{'}}=x^{'}|\{z_{m^{'}}=0\},r_{n^{'}})
$$

Because on the left-hand side of the equals sign in Formula (3) it is already assumed that $$z_m=0$$, the $$\{z_{m^{'}}=0\}$$ in the formula above must exclude the parity-check equation $$z_m=0$$. The right-hand side of the formula above can, using the derivation approach of Formula (0), in turn be converted into something computed from $$p(z_m=0|c_i=x,r)$$. In this way, using the probability $$p(z_m=0|c_i=x,r)$$ that the parity-check equations hold, computed in the first round, the accuracy of $$p(c_i=x|\{z_m=0\},r)$$ is improved recursively and iteratively.


![LDPC_soft_decoding.png](/figure/LDPC译码浅析/LDPC_soft_decoding.png) 

[1]  Error Correction Coding--Mathematical Methods and Algorithms , Todd K. Moon, Wiley, 2005 , mainly referring to Section 15.5.