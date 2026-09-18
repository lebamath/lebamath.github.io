---
layout: default
title: "A Brief Analysis of Soft-Decision Decoding Algorithms (Part Two): Reducing the Amount of Computation"
lang: en
back_url: /index.html?lang=en
---
# A Brief Analysis of Soft-Decision Decoding Algorithms (Part Two): Reducing the Amount of Computation
This article, together with the video recorded for it, is a further refinement of the article and video above.
In the article and video above, a preliminary derivation was made of the ideas and the mathematical formulas behind the LDPC soft-decision decoding algorithm, and the flow of the LDPC soft-decision decoding algorithm was roughly understood. However, there is still one detail in it that needs further optimization, and this optimization is mainly aimed at reducing the amount of computation.

This article belongs to this "sequel''; it is suggested that you read this only after finishing the previous article.

In the previous article, we estimated the probability that each parity-check equation holds from the probabilities of the individual bits, and obtained the following formula:

$$
r_{mn}(x)=p(z_m=0|c_n=x,r) = 
\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in N_{m,n}\}: x=\sum_l x_l\}}
\quad \prod_{l \in N_{m,n}}p(c_l=x_l|r)--------Formula (1)
$$

This formula looks really frightening!
First, the meaning of this formula is to compute the probability that the parity-check equation holds, that is, the probability that the parity-check equation $$z_m=0$$ holds, the given conditions being that the value of the n-th bit is known and the received data r, where r here is a vector.

Secondly, the right-hand side of this equation involves the probabilities that the bits contained in this parity-check equation take the value 0 or 1; by performing actions such as multiplying and summing these bit-related probabilities, the estimation of the probability that the parity-check equation holds is completed.

It is still better to use an example to explain it below.

Suppose the parity-check matrix used by the LDPC code is as follows; this article takes this parity-check matrix as its example throughout (this example is the same as the one in the previous articles).

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

The 5 parity-check equations are:

$$
\begin{aligned}
		z_1 &= c_1 + c_2 + c_3 + c_6 + c_7 + c_{10} \\
		z_2 &= c_1 + c_3 + c_5 + c_6 + c_8 + c_{9} \\
		z_3 &= c_3 + c_4 + c_5 + c_7 + c_9 + c_{10}\\
		z_4 &= c_2 + c_4 + c_5 + c_6 + c_8 + c_{10}\\
		z_5 &= c_1 + c_2 + c_4 + c_7 + c_8 + c_{9}
	\end{aligned}
$$

Suppose we are considering the probability that bit 2 takes the value 1; then $$c_n=x$$ in Formula (1) above is n=2, x=1, $$c_2=1$$. Further, suppose that what we are considering is the first parity-check equation; then $$z_m = z_1$$ in Formula (1), and $$N_{m,n}$$ is a set of subscripts, denoting the subscripts of the bits contained in the m-th parity-check equation, but not including the subscript n. In this concrete example, it is the subscripts of those bits in the m=1 parity-check equation other than the bit n=2, $$N_{m,n}=N_{1,2}=\{1,3,6,7,10\}$$.

Rewrite Formula (1)

$$
\begin{aligned}
	r_{12}(1)=p(z_1=0|c_2=1,r) = 
	\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in \{1,3,6,7,10\}\}: 1=\sum_l x_l\}}
	\quad \prod_{l \in \{1,3,6,7,10\}}p(c_l=x_l|r)--------Formula (1)
\end{aligned}
$$

The summation part is a summation performed once over all those bits that can make the m=1 parity-check equation hold; in total there are the following cases:

Because $$c_2=1$$, in order to make the m=1 parity-check equation hold, the other 5 bits need to have an odd number of 1s, that is, among the 5 bits $$c_1, c_3, c_6, c_7, c_{10}$$ there should be an odd number of 1s.

Then there are 5 cases containing one 1:

$$
\begin{aligned}
	\{c_1, c_3, c_6, c_7, c_{10}\} =  \\
	\{0,0,0,0,1\}  \\
	\{0,0,0,1,0\}  \\
	\{0,0,1,0,0\}  \\
	\{0,1,0,0,0\}  \\
	\{1,0,0,0,0\}
\end{aligned}
$$

There are 10 cases containing three 1s:

$$
\begin{aligned}
	\{c_1, c_3, c_6, c_7, c_{10}\} =  \\
	\{0,1,1,1,0\}  \\
	\{0,1,1,0,1\}  \\
	\{0,1,0,1,1\}  \\
	\{0,0,1,1,1\}  \\
	\{1,0,1,1,0\}  \\
	\{1,0,1,0,1\}  \\
	\{1,0,0,1,1\}  \\
	\{1,1,0,1,0\}  \\
	\{1,1,0,0,1\}  \\
	\{1,1,1,0,0\}
\end{aligned}
$$

There is only 1 case containing five 1s:

$$
\{c_1, c_3, c_6, c_7, c_{10}\} =  \{1,1,1,1,1\}
$$

16 cases in total.

For any one of these 16 cases, a continued product has to be performed as well. For example: $$\{c_1, c_3, c_6, c_7, c_{10}\} =  \{0,1,1,0,1\}$$, the continued product to be performed is:

$$
p(c_1=0|r)p(c_3=1|r)p(c_6=1|r)p(c_7=0|r)p(c_{10}=1|r)
$$

So, the total amount of computation is 16x4=64 multiplications (plus 15 additions that are almost negligible):

$$
\begin{aligned}
	r_{12}(1)&=p(z_1=0|c_n=1,r) = \\
	&p(c_1=0|r)p(c_3=0|r)p(c_6=0|r)p(c_7=0|r)p(c_{10}=1|r) + \\
	&p(c_1=0|r)p(c_3=0|r)p(c_6=0|r)p(c_7=1|r)p(c_{10}=0|r) +\\
	&\cdots \cdots \\
	&p(c_1=1|r)p(c_3=0|r)p(c_6=0|r)p(c_7=0|r)p(c_{10}=0|r)+ \\
	&p(c_1=0|r)p(c_3=1|r)p(c_6=1|r)p(c_7=1|r)p(c_{10}=0|r)+ \\
	&p(c_1=0|r)p(c_3=1|r)p(c_6=1|r)p(c_7=0|r)p(c_{10}=1|r)+\\
	&\cdots \cdots \\
	&p(c_1=1|r)p(c_3=1|r)p(c_6=1|r)p(c_7=0|r)p(c_{10}=0|r)+\\
	&p(c_1=1|r)p(c_3=1|r)p(c_6=1|r)p(c_7=1|r)p(c_{10}=1|r)
\end{aligned}
$$

In fact, from the computation above we can also see a lot of redundancy. For example, in the first continued product and the second continued product above, the product of the first three elements is the same, and can be computed only once. So how do we optimize away this redundancy? The great experts have derived a concise formula.

First introduce a temporary function:

$$
\xi (k)=\sum_{i=1}^k x_{N_{m,n}(i)}
$$

There are rather many subscripts in the summation formula, which makes it not easy to see clearly. The summation is a summation over x, only the subscripts differ; the subscript is: $$N_{m,n}(i)$$. Looking at the earlier example, this $$N_{m,n}=N_{1,2}=\{1,3,6,7,10\}$$

Then: $$N_{m,n}(1) = 1, N_{m,n}(2)=3,N_{m,n}(3)=6,N_{m,n}(4)=7,N_{m,n}(5)=10$$

So, for example when k = 3:

$$
\xi (3) = x_1+x_3+x_6
$$

Here, $$x_1$$ denotes the value of $$c_1$$.

We denote the number of elements in $$N_{m,n}$$ by L.

Then, a diagram as shown below can be formed:

![trellisL.png](/figure/LDPC译码浅析/trellisL.png) 


If $$\xi (L)=x$$ (where x is the value of $$c_n$$), then this case is taken into consideration; otherwise, this combination need not be considered, need not be expanded into a continued product and take part in the accumulation. Continuing the example above, because $$N_{m,n}=N_{1,2}={1,3,6,7,10}$$, L=5.

$$
\xi (L)=\xi (5)=x_1+x_3+x_6+x_7+x_{10}
$$

What is to be considered is $$c_2=x=1$$, so the combinations with $$\xi (5)=1$$ are the ones we need to consider; that is, all combinations satisfying $$x_1+x_3+x_6+x_7+x_{10}=1$$ are the ones we need to consider. This is the formula expression of the 16 cases that were listed ``painstakingly'' earlier.

Below we begin to derive the key recursive formula; using this recursive formula, the computation can be simplified and the redundancy in the computation process eliminated (here we first explain with the example above, and finally summarize it into a general formula)

First let $$\xi (0) = 0$$

Then $$\xi(1) = x_1$$, which depends on the value of $$x_1$$

Next $$\xi(2) = \xi(1) + x_3$$, which depends on the value of $$x_3$$

Next $$\xi(3) = \xi(2) + x_6$$, which depends on the value of $$x_6$$

Next $$\xi(4) = \xi(3) + x_7$$, which depends on the value of $$x_7$$

Finally $$\xi(5) = \xi(4) + x_{10}$$, which depends on the value of $$x_{10}$$.

![trellis5.png](/figure/LDPC译码浅析/trellis5.png) 

For convenience of writing, we introduce one more notation $$W_k(x)=p(\xi(k)=x)$$, denoting the probability that, at step k in the figure above, or in other words when the k-th number has been added, the result is x. In the example x =1, then

$$W_1(1)=p(\xi(1)=1)$$  denotes the probability that $$x_1=1$$

$$W_2(1)=p(\xi(2)=1)$$  denotes the probability that $$\xi(1)+x_3=x_1+x_3=1$$

$$W_3(1)=p(\xi(3)=1)$$  denotes the probability that $$\xi(2)+x_6=x_1+x_3+x_6=1$$

$$W_4(1)=p(\xi(4)=1)$$  denotes the probability that $$\xi(3)+x_7=x_1+x_3+x_6+x_7=1$$

$$W_5(1)=p(\xi(5)=1)$$  denotes the probability that $$\xi(4)+x_{10}=x_1+x_3+x_6+x_7+x_{10}=1$$

Because it starts from 0, $$W_0(0)=1, W_0(1)=0$$, that is, at node 0, the probability of being equal to 0 is 1, and the probability of being equal to 1 is 0.

Let us take a look:

There are two cases in which node 1 equals 1:

​			node 0 equals 0, and $$x_1=1$$

​			 node 0 equals 1, and $$x_1=0$$

Then

$$W_1(1)=p(\xi(1)=1) = W_0(0)p(x_1=1)+W_0(1)p(x_1=0)$$

Similarly:

$$W_1(0)=p(\xi(1)=0) = W_0(0)p(x_1=0)+W_0(1)p(x_1=1)$$



Then:



$$W_2(1)=p(\xi(2)=1) = W_1(0)p(x_3=1)+W_1(1)p(x_3=0)$$

$$W_2(0)=p(\xi(2)=0) = W_1(0)p(x_3=0)+W_1(1)p(x_3=1)$$

And so on, then:

$$W_5(1)=p(\xi(5)=1) = W_4(0)p(x_{10}=1)+W_4(1)p(x_{10}=0)$$

$$W_5(0)=p(\xi(5)=0) = W_4(0)p(x_{10}=0)+W_4(1)p(x_{10}=1)$$

Subtracting the two expressions above:

$$
W_5(0)-W_5(1)=[W_4(0)-W_4(1)][p(x_{10}=0)-p(x_{10}=1)]
$$

Similarly we obtain:

$$
\begin{aligned}
	W_4(0)-W_4(1)=[W_3(0)-W_3(1)][p(x_{7}=0)-p(x_{7}=1)]  \\
	W_3(0)-W_3(1)=[W_2(0)-W_2(1)][p(x_{6}=0)-p(x_{6}=1)]  \\
	W_2(0)-W_2(1)=[W_1(0)-W_1(1)][p(x_{3}=0)-p(x_{3}=1)] \\
	W_1(0)-W_1(1)=[W_0(0)-W_0(1)][p(x_{1}=0)-p(x_{1}=1)]
\end{aligned}
$$

where:

$$
W_0(0)-W_1(0) = 1
$$

Substituting the recursive formula back level by level, we have:

$$
\begin{aligned}
	W_5(0)-W_5(1)=
	[p(x_{10}=0)-p(x_{10}=1)]*[p(x_{7}=0)-p(x_{7}=1)]*[p(x_{6}=0)-p(x_{6}=1)]*\\
	[p(x_{3}=0)-p(x_{3}=1)]*[p(x_{1}=0)-p(x_{1}=1)]
\end{aligned}
$$

And $$W_5(0) = r_{12}(0), \quad W_5(1)=r_{12}(1)$$

$$
\begin{aligned}
	r_{12}(0) - r_{12}(1)  =
	[p(x_{10}=0)-p(x_{10}=1)]*[p(x_{7}=0)-p(x_{7}=1)]*[p(x_{6}=0)-p(x_{6}=1)]*\\
	[p(x_{3}=0)-p(x_{3}=1)]*[p(x_{1}=0)-p(x_{1}=1)] 
\end{aligned}
$$

We also have $$r_{12}(0) + r_{12}(1)=1$$



Then both $$r_{12}(0) , \quad r_{12}(1)$$  can be solved for.

For convenience of writing, we let:

$$
\begin{aligned}
	\delta r_{12} = r_{12}(0) - r_{12}(1)  =
	[p(x_{10}=0)-p(x_{10}=1)]*[p(x_{7}=0)-p(x_{7}=1)]*[p(x_{6}=0)-p(x_{6}=1)]*\\
	[p(x_{3}=0)-p(x_{3}=1)]*[p(x_{1}=0)-p(x_{1}=1)] 
\end{aligned}
$$

Then the solved results can be expressed as:

$$
\begin{aligned}
	r_{12}(0)=\frac{1+\delta r_{12}}{2}  \\
	r_{12}(1)=\frac{1-\delta r_{12}}{2}
\end{aligned}
$$

Let us look at the amount of computation: there are 4 multiplications, 6 additions/subtractions, and one division by 2 (which can be implemented by a right shift). Compared with the previous 64 multiplications, the amount of computation is much smaller.



Above, a derivation was first made with a concrete example. From the derivation and analysis of the concrete example, we can obtain a more general recursive formula.

Let:

$$
W_k(x) = p( \xi(k) = x)
$$

The recursive formula is:

$$
\begin{aligned}
	W_k(0) = W_{k-1}(0) p(x_{N_{m,n}(k)}=0) + W_{k-1}(1) p(x_{N_{m,n}(k)}=1)   \\
	W_k(1) = W_{k-1}(1) p(x_{N_{m,n}(k)}=0) + W_{k-1}(0) p(x_{N_{m,n}(k)}=1)
\end{aligned}
$$

Subtracting the two expressions, then:

$$
W_k(0) - W_k(1) = [W_{k-1}(0) - W_{k-1}(1)][p(x_{N_{m,n}(k)}=0) - p(x_{N_{m,n}(k)}=1)]
$$

Carrying this recursive formula all the way through, we have:

$$
W_k(0) - W_k(1) = \prod_{i=1}^{k}[p(x_{N_{m,n}(i)}=0) - p(x_{N_{m,n}(i)}=1)]
$$

Then, if L is the number of elements in $$N_{m,n}$$:

$$
W_L(0) - W_L(1) = \prod_{i=1}^{L}[p(x_{N_{m,n}(i)}=0) - p(x_{N_{m,n}(i)}=1)]-------Formula (2)
$$

All our derivations above actually implied one background condition: ``namely, under the condition that the data r has been received''. Adding this condition so that they become conditional probabilities, then:

$$
\begin{aligned}
	W_L(x)&= p( \xi(L) = x) \\
	W_L(x)&=p( \xi(L) = x|r) = p(z_m=0|c_n=x,r)=r_{mn}(x)
\end{aligned}
$$

In addition,

$$
p(x_{N_{m,n}(i)})  = p(c_{N_{m,n}(i)}=x_{N_{m,n}(i)}|r) = q_{m,N_{m,n}(i)}(x_{N_{m,n}(i)})
$$

Then the recursive Formula (2) is derived into:

$$
r_{mn}(0) - r_{mn}(1) = \prod_{i=1}^{L}[q_{m,N_{m,n}(i)}(0) - q_{m,N_{m,n}(i)}(1)]
$$

And because

$$
r_{mn}(0) + r_{mn}(1) = 1
$$

So, it is easy to solve for $$r_{mn}(0)$$ and $$r_{mn}(1)$$.

$$
\begin{aligned}
	r_{mn}(0) = (1-\prod_{i=1}^{L}[q_{m,N_{m,n}(i)}(0) - q_{m,N_{m,n}(i)}(1)] ) \space /2  \\
	r_{mn}(1) = (1+\prod_{i=1}^{L}[q_{m,N_{m,n}(i)}(0) - q_{m,N_{m,n}(i)}(1)] ) \space /2
\end{aligned}
$$

So, the amount of computation is roughly equal to L-1 multiplications.

In this way, we have obtained the simplified algorithm of Formula (1).

# Soft-Decision Decoding (Part Three) - Algorithm and Code

This short article writes out, in Octave/Matlab code, the principles and the algorithm explained in the previous two articles; I debugged it with matlab2020a. It has also been tested successfully on GNU Octave 7.2.0.



### Algorithm description:

---------------------------------------------------------------------------------------------------------------

**Input**: A, the a posteriori probabilities of the channel $$p_n(x) = P(c_n=x|r_n)$$, and the maximum number of iterations L

**Initialization**: for all (m,n) with A(m,n)=1, let $$q_{mn}(x) = p_n(x)$$ 

**Horizontal Step**: for each (m,n) with A(m,n)=1:

compute  $$\delta q_{ml} = q_{ml}(0)-q_{ml}(1)$$

compute

$$
\delta r_{mn} = \prod_{\{n'\in N_{m,n}\}}  \delta q_{mn'}
$$

compute $$r_{mn}(1) = (1-\delta r_{mn})/2$$  and  $$r_{mn}(0) = (1+\delta r_{mn})/2$$ 

**Vertical Step**: for each (m,n) with A(m,n)=1:

compute

$$
q_{mn}(0) = \alpha_{mn} p_n(0) \prod_{\{m'\in M_{n,m} \}}r_{m'n}(0)
$$

and

$$
q_{mn}(1) = \alpha_{mn} p_n(1) \prod_{\{m'\in M_{n,m} \}}r_{m'n}(1)
$$

where $$\alpha_{mn}$$  is the value that makes $$q_{mn}(0)+q_{mn}(1)=1$$ hold

**Compute the pseudo-posterior probabilities**:

​		

$$
q_n(0) = \alpha_n p_n(0) \prod_{\{m'\in M_n \}}r_{m'n}(0)
$$

and

$$
q_n(1) = \alpha_n p_n(1) \prod_{\{m'\in M_n \}}r_{m'n}(1)
$$

where $$\alpha_n$$  is the value that makes $$q_n(0)+q_n(1)=1$$ hold

**Make a tentative decision**: if $$q_n(1) > 0.5$$ then $$\hat c_n=1$$, otherwise $$\hat c_n=0$$

​		If $$A \mathbf {\hat c} = 0$$ is satisfied, then **stop**. Otherwise, if the number of iterations < L, loop back to **Horizontal Step**; otherwise, declare a decoding failure and **stop**.

---------------------------------------------------------------------------------------------------------------



The Octave/Matlab program below still uses the parity-check matrix from the previous articles:

$$
A = \begin{bmatrix}
	1& 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1\\
	1& 0 & 1 & 0 & 1 & 1 & 0 & 1 & 1 & 0\\
	0& 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1 & 1\\
	0& 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1\\
	1& 1 & 0 & 1 & 0 & 0 & 1 & 1 & 1 & 0
\end{bmatrix}
$$

The original transmitted message $$m=[1\quad 0\quad1\quad0\quad1]^T$$; after LDPC encoding (how the encoding is done is skipped here for now):

$$
c=[0 \quad 0\quad 0\quad1\quad0\quad1\quad0\quad1\quad0\quad1]^T
$$

After passing through a certain Gaussian channel, we can obtain the following a posteriori probabilities:

$$
P(\mathbf c=1|\mathbf r) = [0.22 \quad 0.16 \quad 0.19 \quad 0.48 \quad 0.55 \quad0.87 \quad0.18 \quad0.79 \quad0.25 \quad 0.76]^T
$$

If the decision is made using greater than 0.5 as 1, then the result obtained by hard decision is:

$$
\mathbf {\hat c} = [0 \quad 0\quad 0\quad \underline0\quad \underline 1\quad 1\quad 0\quad 1\quad 0\quad 1]
$$

The two underlined digits are wrong,

$$
A\mathbf{\hat c} = [0 \quad 1 \quad0\quad0\quad1]^T
$$

which means that two parity-check equations do not hold -- an error has occurred!



Below is the Octave/Matlab code:

Copyright notice: this code is taken from the code accompanying [1], with slight modifications.

Please download the code from github: \url{https://github.com/taichiorange/leba_math}

[1] Error Correction Coding--Mathematical Methods and Algorithms , Todd K. Moon, Wiley, 2005 