---
layout: default
title: "The Likelihood-Ratio Form of the LDPC Soft-Decision Algorithm"
lang: en
back_url: /index.html?lang=en
---

## The Likelihood-Ratio Form of the LDPC Soft-Decision Algorithm (Part One)

In the previous articles, we derived the iterative algorithm for LDPC soft-decision decoding. In this article, we derive the LDPC iterative algorithm once more, in the log-ratio form.

This article refers to Section 15.5.6 of reference [1].

In order to decide each transmitted bit, we can evaluate it by taking the logarithm of the ratio of the a posteriori probabilities, that is:

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)}  \quad  ------ \quad  Formula (1)
$$

If this ratio is greater than 0, then bit n is decided as 1, otherwise it is decided as 0.
Below we derive the recursive expression of the formula above.

We first carry out the derivation of the numerator in Formula (1):

$$
\begin{aligned}
	p(c_n=1|r) = p(c_n=1|r_n, \{r_i,i \neq n\}) \\
	=\frac{  p(c_n = 1,  r_n |\{r_i,i \neq n\} ) }  { p(r_n | \{r_i,i \neq n\} ) }  \\
	=\frac{  p( r_n | c_n = 1, \{r_i,i \neq n\} ) p(c_n=1|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) } \\
	=\frac{  p( r_n | c_n = 1 ) p(c_n=1|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }
\end{aligned}
$$

where the following was used:

$$
p( r_n | c_n = 1, \{r_i,i \neq n\} ) =  p( r_n | c_n = 1 )
$$

Similarly, carry out the derivation of the denominator part in Formula (1):

$$
\begin{aligned}
	p(c_n=0|r) = p(c_n=0|r_n, \{r_i,i \neq n\}) \\
	=\frac{  p(c_n = 0,  r_n |\{r_i,i \neq n\} ) }  { p(r_n | \{r_i,i \neq n\} ) }  \\
	=\frac{  p( r_n | c_n = 0, \{r_i,i \neq n\} ) p(c_n=0|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }  \\
	=\frac{  p( r_n | c_n = 0) p(c_n=0|\{r_i,i \neq n\} }  { p(r_n | \{r_i,i \neq n\} ) }  \\
\end{aligned}
$$

Then Formula (1) can be derived into

$$
\begin{aligned}
	\lambda(c_n|r) = log \frac
	{ p( r_n | c_n = 1) p(c_n=1|\{r_i,i \neq n\} }
	{ p( r_n | c_n = 0) p(c_n=0|\{r_i,i \neq n\} }  \\
	=log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)}  +
	log \frac{p(c_n=1|\{r_i,i \neq n\}}{p(c_n=0|\{r_i,i \neq n\} }
\end{aligned}
$$

where

$$
log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)}
$$

this part can be easily computed according to the characteristics of the channel (for example an additive white Gaussian noise channel) and the modulation scheme. If it is BPSK (1-->1, 0-->-1) modulation, passing through an additive white Gaussian noise channel, then

$$
log \frac {p( r_n | c_n = 1) } { p( r_n | c_n = 0)} = log \frac{exp(-(r_n-1)^2/(2\sigma^2))}{exp(-(r_n+1)^2/(2\sigma^2))} = \frac{2}{\sigma^2}r_n
$$

And as for the second part

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) }
$$

taking the constraints of the parity-check equations into account: for the case $$c_n=1$$, if those parity-check equations in which $$c_n$$ participates are to hold, then in each parity-check equation the remaining bits, apart from the bit $$c_n$$, should add up to 1; similarly, for the case $$c_n=0$$, if those parity-check equations in which $$c_n$$ participates are to hold, then in each parity-check equation the remaining bits, apart from the bit $$c_n$$, should add up to 0. For convenience of exposition, we introduce a notation:

$$
z_{m,n} = \sum_{i \in N_{m,n} } c_i
$$

Then $$c_n=1$$ can be expressed as: for those parity-check equations in which $$c_n$$ participates, the sum of the other bits inside each parity-check equation is 1, that is $$\{z_{m,n}=1\}$$; similarly, $$c_n=0$$ can be expressed as: for those parity-check equations in which $$c_n$$ participates, the sum of the other bits inside each parity-check equation is 0, that is $$\{z_{m,n}=0\}$$. Then:

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = log \frac
{p(\{z_{m,n}=1\}|\{r_i,i \neq n\} )}
{p(\{z_{m,n}=0\}|\{r_i,i \neq n\} )}\quad ---- \quad Formula (2)
$$

Here an assumption/approximation has to be made, namely that the parity-check equations in which $$c_n$$ participates are mutually independent, that is, that among these parity-check equations, apart from having the common bit $$c_n$$, there are no other bits in common. Of course, this assumption does not hold in the general case; here it is assumed to hold, or to hold approximately (only when the number of identical bits is not very large). Under this assumption, the numerator and denominator parts on the right-hand side of the formula above can in turn be split into the product of several probabilities:

$$
p(\{z_{m,n}=1\}|\{r_i,i \neq n\} ) = \prod_m p(z_{m,n}=1|\{r_i,i \neq n\})
$$

Similarly,

$$
p(\{z_{m,n}=0\}|\{r_i,i \neq n\} ) = \prod_m p(z_{m,n}=0|\{r_i,i \neq n\})
$$

Then Formula (2) becomes:

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = log \prod_m \frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
=
\sum_m log\frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
$$

According to Formula (1),

$$
log\frac{p(z_{m,n}=1|\{r_i,i \neq n\})}{p(z_{m,n}=0|\{r_i,i \neq n\})}
$$

can be denoted as $$\lambda(z_{m,n} \vert \{r_i,i \neq n\})$$

$$
log \frac{p(c_n=1|\{r_i,i \neq n\})}{p(c_n=0|\{r_i,i \neq n\}) } = \sum_m \lambda(z_{m,n}| \{r_i,i \neq n\})
$$

Then, substituting the result derived above into Formula (1), we can see:

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \lambda(z_{m,n}| \{r_i,i \neq n\})\quad ----\quad Formula (3)
$$

The idea inside this is that the log-ratio $$\lambda(c_n)$$ of the bit $$c_n$$ can be expressed by a certain ratio of the parity-check equations holding (the ratio of the probabilities that the parity-check equations hold under the two conditions $$c_n=1$$ and $$c_n=0$$). That is, the probability that the parity-check equations hold can be used to estimate the probability of the value of a bit. Let us take an example to further understand the result above.

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


Let us consider that we are decoding the bit $$c_2$$; we need to compute the logarithm of the following probability ratio:

$$
\lambda(c_2|r) = log\frac{p(c_2=1|r)}{p(c_2=0|r)}= log\frac{p(c_2=1|r_2,r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}{p(c_2=0|r_2,r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
$$

According to Formula (3):

$$
\lambda(c_2|r) = log\frac{p(c_2=1|r)}{p(c_2=0|r)}=\frac{2}{\sigma^2}r_2 +  \sum_m \lambda(z_{m,2}| \{r_i,i \neq 2\})
$$

The parity-check equations in which $$c_2$$ participates are the three ones $$z_1,z_4,z_5$$, so

$$
\begin{aligned}
	\sum_{m} \lambda(z_{m,2}| \{r_i,i \neq 2\}) = \sum_{m \in\{1,4,5\}} \lambda(z_{m,2}| \{r_i,i \neq 2\}) \\
	= \lambda(z_{1,2}| \{r_i,i \neq 2\}) + \lambda(z_{4,2}| \{r_i,i \neq 2\}) + \lambda(z_{5,2}| \{r_i,i \neq 2\})
\end{aligned}
$$

Because the bits of the first parity-check equation are 1,2,3,6,7,10, and after removing bit 2 there remain 1,3,6,7,10, so

$$
\begin{aligned}
	\lambda(z_{1,2}| \{r_i,i \neq 2\}) = \lambda(c_1+c_3+c_6+c_7+c_{10}|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10}) \\
	= log \frac
	{p(c_1+c_3+c_6+c_7+c_{10}=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
	{p(c_1+c_3+c_6+c_7+c_{10}=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
\end{aligned}
$$

Similarly:

$$
\begin{aligned}
	\lambda(z_{4,2}| \{r_i,i \neq 2\}) = log \frac
	{p(c_4+c_5+c_6+c_8+c_{10}=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
	{p(c_4+c_5+c_6+c_8+c_{10}=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}   \\
	\lambda(z_{5,2}| \{r_i,i \neq 2\}) = log \frac
	{p(c_1+c_4+c_7+c_8+c_9=1|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
	{p(c_1+c_4+c_7+c_8+c_9=0|r_1,r_3,r_4,r_5,r_6,r_7,r_8,r_9,r_{10})}
\end{aligned}
$$

From the example, one can also see that there is using the probability that the parity-check equations hold to estimate the logarithm of the probability ratio of the bit to be decoded, which can then be used to decide this bit to be decoded.

At this point, our first-stage derivation is finished, that is, using a certain probability of the parity-check equations to estimate a certain probability of the bit to be decoded.

**Stage Two**

Let us look at the expression inside the summation on the right-hand side of Formula (3)

$$
\lambda(z_{m,n}| \{r_i,i \neq n\})
$$

According to the definition of the $$\lambda$$ function, write it in the form of the logarithm of a probability ratio:

$$
\lambda(z_{m,n}| \{r_i,i \neq n\}) = log \frac  {p(z_{m,n}=1| \{r_i,i \neq n\})}   {p(z_{m,n}=0| \{r_i,i \neq n\})}
$$

Then substitute the definition of $$z_{m,n}$$ into it:

$$
\begin{aligned}
	\lambda(z_{m,n}| \{r_i,i \neq n\}) = log \frac  {p(z_{m,n}=1| \{r_i,i \neq n\})}   {p(z_{m,n}=0| \{r_i,i \neq n\})} \\
	=log \frac  {p((\sum_{i \in N_{m,n} } c_i)=1| \{r_i,i \neq n\})}   {p((\sum_{i \in N_{m,n} } c_i)=0| \{r_i,i \neq n\})}
\end{aligned}
$$

This is its concrete meaning. Substituting the definition of $$z_{m,n}$$ directly into $$\lambda(z_{m,n}\vert  \{r_i,i \neq n\})$$ we have:

$$
\lambda(z_{m,n}| \{r_i,i \neq n\}) = \lambda((\sum_{j \in N_{m,n} } c_j)| \{r_i,i \neq n\})  \tag{4}
$$

According to the tanh rule theorem (which is derived in the appendix), this formula is a theorem, and the variables used in it have nothing to do with the derivation above, where $$x_i$$ are binary random variables taking the values 0/1:

$$
\begin{aligned}
	\lambda(x_1 \oplus x_2 \oplus x_3 \oplus \cdots \oplus x_n) &=  2 tanh^{-1}(
	tanh(-\frac{\lambda(x_1)}{2})
	tanh(-\frac{\lambda(x_2)}{2})
	tanh(-\frac{\lambda(x_3)}{2})
	\cdots
	tanh(-\frac{\lambda(x_n)}{2})
	)
	\\
	&=
	-2 tanh^{-1}(\prod_i tanh(-\frac{\lambda(x_i)}{2}))
\end{aligned}
$$

Applying this theorem to Formula (4)

$$
\begin{aligned}
	\lambda(z_{m,n}| \{r_i,i \neq n\}) &= \lambda((\sum_{j \in N_{m,n} } c_j)| \{r_i,i \neq n\}) \\
	&= - 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq n\})}{2}))
\end{aligned}
$$

In order to distinguish them, we introduce a new symbol, using a new symbol to denote the right-hand side of the formula above:

$$
\eta_{m,n}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq n\})}{2})) \quad -----\quad Formula (5)
$$

This $$\eta_{m,n}$$ can be understood as a certain measure of the m-th parity-check equation holding; it is a measure realized by comparing the probabilities that the parity-check equation holds under the two situations $$c_n=1$$ and $$c_n=0$$. In short, this is a quantity measuring whether the parity-check equation holds. And within this quantity, that is, the $$\lambda(c_j\vert \{r_i,i \neq n\})$$ in Formula (5), is in turn a certain probability measure of bit j. In the concrete implementation of the algorithm, it is set equal to $$\lambda(c_j\vert r_j)$$.

Then Formula (3) becomes:

$$
\lambda(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta_{m,n}\quad ----\quad Formula (6)
$$

Formula (6) and Formula (5) together constitute a kind of loop iteration.



------------------------------ At this point one round of computation is finished. Let us continue with an example for illustration:

Suppose the bit $$c_2$$ is being decoded, that is n=2. The parity-check equations in which $$c_2$$ participates are the three ones $$z_1,z_4,z_5$$, and we consider computing the measure of the fourth parity-check equation holding, that is m=4, computing $$\eta_{4,2}$$.

The fourth parity-check equation has the six bits 2, 4, 5, 6, 8, 10 participating in it. Then Formula (5) is:

$$
\eta_{4,2}=- 2 tanh^{-1}(\prod_{j \in \{4,5,6,8,10\} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq 2\})}{2}))
$$

In order to compute the formula above, we need to know :

$$
\begin{aligned}
	\lambda(c_4|\{r_i,i \neq 2\})  \\
	\lambda(c_5|\{r_i,i \neq 2\})  \\
	\lambda(c_6|\{r_i,i \neq 2\})  \\
	\lambda(c_8|\{r_i,i \neq 2\})  \\
	\lambda(c_{10}|\{r_i,i \neq 2\})
\end{aligned}
$$

In the first round, they are computed directly with their own natural ratios:

$$
\begin{aligned}
	\lambda(c_4|\{r_i,i \neq 2\})=\lambda(c_4|r_4) = \frac{2}{\sigma^2}r_4  \\
	\lambda(c_5|\{r_i,i \neq 2\})=\lambda(c_5|r_5) = \frac{2}{\sigma^2}r_5   \\
	\lambda(c_6|\{r_i,i \neq 2\})=\lambda(c_6|r_6) = \frac{2}{\sigma^2}r_6   \\
	\lambda(c_8|\{r_i,i \neq 2\})=\lambda(c_8|r_8) = \frac{2}{\sigma^2}r_8   \\
	\lambda(c_{10}|\{r_i,i \neq 2\})=\lambda(c_{10}|r_{10}) = \frac{2}{\sigma^2}r_{10}
\end{aligned}
$$

In this way $$\eta_{4,2}$$ can be computed:

$$
\begin{aligned}
	\eta_{4,2} &=- 2 tanh^{-1}(\\
	&tanh(-\frac{\lambda(c_4|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_5|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_6|\{r_i,i \neq 2\})}{2}) \\
	&tanh(-\frac{\lambda(c_8|\{r_i,i \neq 2\})}{2})tanh(-\frac{\lambda(c_{10}|\{r_i,i \neq 2\})}{2}) )
\end{aligned}
$$

By a similar method, all the $$\eta_{m,n}$$ can be computed.

The parity-check equations in which $$c_2$$ participates are the three ones $$z_1,z_4,z_5$$, then:

$$
\lambda(c_2|r) = \frac{2}{\sigma^2}r_n +  \eta_{1,2}+\eta_{4,2}+\eta_{5,2}
$$

Similarly, all the $$\lambda(c_i\vert r)$$ can be computed.  Make a decision once.

---------------------end of example



At this time, make a decision once on all the $$\lambda(c_j\vert r_j)$$; if the check result is found to be correct, then finish. Otherwise, we have reason to use the newly estimated $$\lambda(c_j\vert r_j)$$ to further strengthen the accuracy of the measure of the parity-check equations holding, so this $$\eta_{m,n}$$ needs to be updated.

In this update, because the computation of the previous round's $$\lambda(c_j\vert r_j)$$ contained the information of the measure of the m-th parity-check equation, therefore, in order to update the information of the m-th parity-check equation, the previous round's $$\eta_{m,n}$$ must be subtracted from the previous round's $$\lambda(c_j\vert r_j)$$, so this update formula is:

$$
\lambda^{[l-1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l-1]}(c_j|r) -\eta_{m,j}^{[l-1]}
$$

Substituting into Formula (5) we have:

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l-1]}(c_j|r) -\eta_{m,j}^{[l-1]}}{2})) ----Formula (7)
$$

Adding the round information to Formula (6):

$$
\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}\quad ----\quad Formula (8)
$$

Then Formulas (7) and (8) constitute the iteration flow of this algorithm.



------------Let us continue to illustrate with an example

Continuing the previous example, we now already have the new $$\lambda(c_i\vert r)$$, which is a new kind of probability measure of the values of the bits; now we use this measure to update the measure of whether the parity-check equations hold. For example, we want to update this one:

$$
\eta_{4,2}=- 2 tanh^{-1}(\prod_{j \in \{4,5,6,8,10\} } tanh(-\frac{\lambda(c_j|\{r_i,i \neq 2\})}{2}))
$$

It should be noted that, because the $$\lambda(c_j\vert \{r_i,i \neq 2\})$$ have all used the information of the fourth parity-check equation, and our present purpose is in turn to compute the measure information of the fourth parity-check equation, therefore avoiding this kind of self-loop problem, the influence of $$\eta_{4,*}$$ has to be removed from all of them.

$$
\begin{aligned}
	\lambda(c_4|r) = \frac{2}{\sigma^2}r_4 +  \eta_{3,4}+\eta_{4,4}+\eta_{5,4}  \\
	\lambda(c_5|r) = \frac{2}{\sigma^2}r_5 +  \eta_{2,5}+\eta_{3,5}+\eta_{4,5}  \\
	\lambda(c_6|r) = \frac{2}{\sigma^2}r_6 +  \eta_{1,6}+\eta_{2,6}+\eta_{4,6}  \\
	\lambda(c_8|r) = \frac{2}{\sigma^2}r_8 +  \eta_{2,8}+\eta_{4,8}+\eta_{5,8}  \\
	\lambda(c_{10}|r) = \frac{2}{\sigma^2}r_{10} +  \eta_{1,10}+\eta_{3,10}+\eta_{4,10}
\end{aligned}
$$

That is, in the formulas above remove, one by one respectively: $$\eta_{4,4},\eta_{4,5},\eta_{4,6},\eta_{4,8},\eta_{4,10}$$, and then feed them into the computation formula of $$\eta_{4,2}$$

$$
\begin{aligned}
	\eta_{4,2}=- 2 tanh^{-1}( \\
	tanh(-\frac{\lambda(c_4|r)-\eta_{4,4}^{last}}{2})* \\
	tanh(-\frac{\lambda(c_5|r)-\eta_{4,5}^{last}}{2})* \\
	tanh(-\frac{\lambda(c_6|r)-\eta_{4,6}^{last}}{2})* \\
	tanh(-\frac{\lambda(c_8|r)-\eta_{4,8}^{last}}{2})* \\
	tanh(-\frac{\lambda(c_{10}|r)-\eta_{4,10}^{last}}{2})
	\\)
\end{aligned}
$$

------------end of example





The overall schematic flow chart is as follows:

![LLR-LDPC-soft.png](/figure/LDPC译码浅析/LLR-LDPC-soft.png)

[1]  Error Correction Coding--Mathematical Methods and Algorithms , Todd K. Moon, Wiley, 2005 , mainly referring to Section 15.5.

## The Likelihood-Ratio Form of the Soft-Decision Algorithm (Part Two) -- Algorithm and Code

**Input**: the parity-check matrix A, the received data vector r, the maximum number of iterations L, the channel parameter $$L_c$$

**Initialization**: for all (m,n) with A(m,n) = 1, let $$\eta^{[0]}_{m,n} = 0$$
let $$\lambda^{[0]}_n = L_c r_n$$

number of iterations  $$l = 1$$

**Check nodes**: for all (m,n) with A(m,n) = 1, compute:

$$
\eta^{[l]}_{m,n} = -2 tanh^{-1}
(
\prod_{j \in N_{m,n}}
tanh(  -\frac{ \lambda^{[l-1]}_j-\eta^{[l-1]}_{m,j}}
{2}  )
)
$$

**Bit nodes:**  n=1,2,...,N, compute:

$$
\lambda^{[l]}_n = L_c r_n + \sum_{m \in M_n}  \eta^{[l]}_{m,n}
$$

Make a tentative decision: if $$\lambda^{[l]}_n > 0$$, then $$\hat c_n = 1$$, otherwise, $$\hat c_n = 0$$

If $$A \hat c = 0$$, then **decoding succeeds**, finish; if the number of iterations $$l<L$$, then go to **Check nodes** to continue with the next round; otherwise, it is a **decoding failure**, stop.

Please download the code from github: \url{https://github.com/taichiorange/leba_math}