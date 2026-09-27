---
layout: default
title: "FFT-QSPA: A Decoding Algorithm for Multi-ary LDPC Codes Based on the Fast Fourier Transform"
lang: en
back_url: /index.html?lang=en
---
## FFT-QSPA: A Decoding Algorithm for Multi-ary LDPC Codes Based on the Fast Fourier Transform
This article explains a decoding algorithm for multi-ary LDPC codes: the FFT-based decoding algorithm.

What is called multi-ary means that the data taking part in the operations are not bits, but have more possible values, for example the values 0, 1, 2, 3, or for example the values 01,2,3,4,5 and so on. The coefficients of the parity-check matrix can also take values other than just 0 and 1; for example the parity-check matrix below is a parity-check matrix based on modulo-4 arithmetic, that is, what many textbooks call the finite field $$\mathbb{F}_4$$. Of course this involves a lot of knowledge of advanced algebra, but here it is enough to understand everything as modulo-4 arithmetic: whether addition, subtraction or multiplication, the final result just needs to be taken modulo 4.

$$
H = \begin{bmatrix}
	1&  0&  1& 0 & 1 & 3\\
	2& 2 & 0 & 2 & 0 & 1\\
	0& 1 & 3 & 3 & 3 & 0
\end{bmatrix}
$$

So, there are three parity-check equations here; 6 numbers are received (denoted as $$r_1,r_2,r_3,r_4,r_5,r_6$$ ), the data after LDPC encoding are six in number, denoted as $$c_1,c_2,c_3,c_4,c_5,c_6$$, and the three parity-check equations can be written as:

$$
\begin{aligned}
	z_1: c_1 + c_3 + c_5 + 3c_6  =0  \\
	z_2: 2c_1 + 2c_2 + 2c_4 + c_6 = 0  \\
	z_3: c_2 + 3c_3 + 3c_4 + 3 c_5  = 0
\end{aligned}
$$

A special reminder: all the operations above are under the modulo-4 rule, and the results of the computations all need to be taken modulo 4.

Originally the optimal decoding method is to find the largest probability among $$p({c_1,c_2,c_3,c_4,c_5,c_6}\vert \{z_m=0\},r)$$, and take the $$c_1,c_2,c_3,c_4,c_5,c_6$$ corresponding to the largest probability as our decoding result. However, the amount of computation for this is large, and when the code length is rather long, the amount of computation becomes so large as to be infeasible. We settle for the next best thing and compute the probability of a single $$c_i$$ to make the decision:

$$
p(c_i|\{z_m=0\},r)
$$

Let us carry out some further derivation of the formula above:

$$
p(c_i|\{z_m=0\},r) = \frac{p(c_i,\{z_m=0\}|r)}{p(\{z_m=0\}|r)} =  \frac{p(\{z_m=0\}|c_i,r)p(c_i|r)}{p(\{z_m=0\}|r)} \tag{1}
$$

where $$p(c_i\vert r) = p(c_i\vert r_i)$$ (because in this condition there is no requirement of the constraint equations, therefore the other $$r_j, j\neq i$$ do not affect $$c_i$$.

In Formula (1), in order to simplify the computation, we assume that whether the individual parity-check equations hold is mutually independent; of course, in reality, because every transmitted datum $$c_i$$ takes part in several parity-check equations, the parity-check equations are mutually correlated, and the assumption here leads to an approximate equality:

$$
p(\{z_m=0\}|c_i,r) \approx p(\{z_1=0\}|c_i,r) p(z_2=0|c_i,r).... = \prod_m p(z_m=0|c_i,r)  \tag{2}
$$

Let us take an example: for instance, in the parity-check equations at the beginning, we consider decoding $$c_2$$

Then

$$
\begin{aligned}
	&p(c_2=3|\{z_1=0,z_2=0,z_3=0\},r_1,r_2,r_3,r_4,r_5,r_6) = \\
	&\frac{1}{p(\{z_1=0,z_2=0,z_3=0\}|r_1,r_2,r_3,r_4,r_5,r_6)} p(c_2=3|r_2) p(\{z_1=0,z_2=0,z_3=0\}|c_2=3,r_1,r_2,r_3,r_4,r_5,r_6) \\
	&\approx p(\{z_1=0,z_2=0,z_3=0\}|r_1,r_2,r_3,r_4,r_5,r_6) p(c_2=3|r_2) p(z1=0|r)p(z2=0|r)p(z3=0|r)
\end{aligned}
$$

Now, we have used the probability of whether the parity-check equations hold to express the probability of the transmitted data.

Next, carry out some derivation of the $$p(z_m=0\vert c_i,r)$$ in Formula (2):

$$
p(z_m=0|c_i,r) = \frac{p(z_m=0,c_i|r)}{p(c_i|r)} = \frac{p(z_m=0,c_i|r)}{p(c_i|r_i)} \tag{3}
$$

where $$p(c_i\vert r_i)$$ can be regarded as a constant; let us analyze the numerator part: because $$c_i$$ is given, then for $$z_m=0$$ to hold means that the other terms in the parity-check equation have to take all sorts of cases

$$
p(z_m=0,c_i|r)= \sum_{h_ic_i+\sum_{k\neq i}h_kc_k=0} p(\{c_j\}|r) \tag{4}
$$

where

$$
p(\{c_j\}|r) = \prod_{j\neq i} p(c_j|r_j)
$$

Then Formula (4) becomes:

$$
p(z_m=0,c_i|r)= \sum_{h_ic_i+\sum_{k\neq i}h_kc_k=0} \prod_{j\neq i} p(c_j|r_j) \tag{5}
$$

Let us take an example, for instance the parity-check equation $$z_2$$, taking $$c_2=3$$ as the example

$$
\begin{aligned}
	p(z_2=0,c_2=3|r) = p(2c_1 + 2c_2 + 2c_4 + c_6 = 0, c_2=3 | r) &= p(2c_1 + 6 + 2c_4 + c_6 = 0) \\
	&= p(2c_1 + 2 + 2c_4 + c_6 = 0|r)
\end{aligned}
$$

Then there are the following cases:

$$
\begin{aligned}
	c_1=0,c_4=0,c_6 = 2 \\
	c_1=0,c_4=1,c_6 = 0 \\
	c_1=0,c_4=2,c_6 = 2 \\
	c_1=0,c_4=3,c_6 = 0 \\
	...  \\
	c_1=3,c_4=0,c_6 = 0 \\
	c_1=3,c_4=1,c_6 = 2 \\
	c_1=3,c_4=2,c_6 = 0 \\
	c_1=3,c_4=3,c_6 = 2
\end{aligned}
$$

For any one of the cases above, for example

$$
p(c_1=3,c_4=1,c_6 = 2|r) = p(c_1=3|r_1)p(c_4=1|r_4)p(c_6 = 2|r_6)
$$

From the example above one can also see that computing Formula (4) is very complicated and the amount of computation is very large. Moreover, we also need to compute the cases in which $$c_2$$ equals the other three values (0,1,2); it can be seen how large the amount of computation of Formula (4) and Formula (5) is.

Here, we introduce a kind of Fourier transform under this modulo-4 operation. Here we do not give a rigorous proof; we only sort out this idea and process, and once it is understood what this is doing, one can later consult more specialized books and literature to see the rigorous proof.

Let us take an example of a binary code, that is, the values are only 0 and 1; there are two random variables, $$x_1, x_2$$; let:

$$
\begin{aligned}
	p(x_1=0) = p_1  \\
	p(x_2=0) = p_2
\end{aligned}
$$

If we want to compute:

$$
\sum_{x_1+x_2=0} p(x_1)p(x_2)  \quad ---Formula (6)
$$

and

$$
\sum_{x_1+x_2=1} p(x_1)p(x_2)  \quad ---Formula (7)
$$

then Formula (6) can be expanded as:

$$
p(x_1=0)p(x_2=0) + p(x_1=1)p(x_2=1) = p_1 p_2 + (1-p_1)(1-p_2) = 1 + 2 p_1 p_2 - p_1 - p_2  \tag{8}
$$

Formula (7) can be expanded as:

$$
p(x_1=0)p(x_2=1) + p(x_1=1)p(x_2=0) = p_1 (1-p_2) + (1-p_1) p_2 = p_1 + p_2 - 2 p_1 p_2 \tag{9}
$$

For this kind of binary case, we introduce a transform:

$$
H_1 = \begin{bmatrix}
	1& 1\\
	1& -1
\end{bmatrix}
$$

Because it is the binary case, this transform is a transform of the probabilities of the two possible values of a certain random variable; performing the transform on $$x_1=0, x_1=1$$:

$$
H_1 \begin{bmatrix}
	p_1\\
	1-p_1
\end{bmatrix} = \begin{bmatrix}
	1\\
	p_1-(1-p_1)
\end{bmatrix}= \begin{bmatrix}
	1\\
	2p_1-1
\end{bmatrix}
$$

Performing the transform on $$x_2=0,x_2=1$$

$$
H_1 \begin{bmatrix}
	p_2\\
	1-p_2
\end{bmatrix} = \begin{bmatrix}
	1\\
	p_2-(1-p_2)
\end{bmatrix}= \begin{bmatrix}
	1\\
	2p_2-1
\end{bmatrix}
$$

On the results after the two transforms above, that is, the two column vectors, perform an element-by-element multiplication:

$$
\begin{bmatrix}
	1\\
	2p_1-1
\end{bmatrix} \times_{element-wise}
\begin{bmatrix}
	1\\
	2p_2-1
\end{bmatrix}
=
\begin{bmatrix}
	1\\
	(2p_1-1)(2p_2-1)
\end{bmatrix}
=
\begin{bmatrix}
	1\\
	4p_1 p_2 - 2p_1 - 2 p_2 + 1
\end{bmatrix}
$$

On the result above, we introduce one more transform:

$$
H_1^{-1} = \frac{1}{2}\begin{bmatrix}
	1& 1\\
	1& -1
\end{bmatrix}
$$

Then:

$$
H_1^{-1}\begin{bmatrix}
	1\\
	4p_1 p_2 - 2p_1 - 2 p_2 + 1
\end{bmatrix}
=
\frac{1}{2}\begin{bmatrix}
	1& 1\\
	1& -1
\end{bmatrix}
\begin{bmatrix}
	1\\
	4p_1 p_2 - 2p_1 - 2 p_2 + 1
\end{bmatrix}
=
\begin{bmatrix}
	1+2p_1p_2-p_1-p_2\\
	p_1 + p_2 - 2 p_1 p_2
\end{bmatrix}
$$

It can be seen that the result above is exactly the result of Formulas (7)(8); in the vector of the result above, element 1 is the result of Formula (7), and element 2 is the result of Formula (8).

Here, we have obtained a new method:

First, on

$$
\begin{bmatrix}
	p(x_1=0)\\
	p(x_1=1)
\end{bmatrix}
$$

and

$$
\begin{bmatrix}
	p(x_2=0)\\
	p(x_2=1)
\end{bmatrix}
$$

perform the $$H_1$$ transform, obtaining one vector from each, then perform an element-by-element multiplication of the corresponding elements in the two vectors, obtaining one vector, and finally perform the $$H_1^{-1}$$ transform on this vector.



For Formula (5), we need to consider $$h_ic_i+\sum_{k\neq i}h_kc_k=0$$; we denote the result of $$h_j x_j$$ as $$c_j^p$$, where the superscript p means permutation; in fact it is just that, having taken a value $$c_j$$, by multiplying by $$h_j$$ one can compute a $$c_j^p$$, and then the subscript of the summation term in Formula (5) can be expressed as:

$$
c_i^p + \sum_{k \neq i} c_k^p= 0
$$

Then, Formula (5) can be written as:

$$
p(z_m=0,c_i^p|r) = \sum_{c_i^p+\sum_{k\neq i}c_k^p=0} \prod_{j\neq i} p(c_j^p|r_j)
$$

Let us ignore the superscript p for the time being; suppose Formula (5) is as:

$$
p(z_m=0,c_i|r) = \sum_{c_i+\sum_{k\neq i}c_k=0} \prod_{j\neq i} p(c_j|r_j) \tag{5.1}
$$

Then, we can introduce a transform-domain algorithm similar to the one for the binary case.



Let us look at Formula (5.1), in which

$$
p(z_m=0,c_i|r)
$$

has four cases (taking modulo 4 as the example):

$$
\begin{aligned}
	p(z_m=0,c_i=0|r) \\
	p(z_m=0,c_i=1|r) \\
	p(z_m=0,c_i=2|r) \\
	p(z_m=0,c_i=3|r)
\end{aligned}
$$

Suppose we look at the parity-check equation $$z_3$$, then $$c_2 + 3c_3 + 3c_4 + 3 c_5  = 0$$, so $$c_2$$ has four possible values, and according to Formula (5), the probabilities we need to compute are:

$$
\begin{aligned}
	p(z_3=0,c_2=0|r) \\
	p(z_3=0,c_2=1|r) \\
	p(z_3=0,c_2=2|r) \\
	p(z_3=0,c_2=3|r)
\end{aligned}
$$

They can be written as a vector:

$$
p_{z_3}=\begin{bmatrix}
	p(z_3=0,c_2=0|r) \\
	p(z_3=0,c_2=1|r) \\
	p(z_3=0,c_2=2|r) \\
	p(z_3=0,c_2=3|r)
\end{bmatrix} \tag{10}
$$

We have another three vectors:

$$
p_{c_3}=\begin{bmatrix}
	p(c_3=0|r) \\
	p(c_3=1|r) \\
	p(c_3=2|r) \\
	p(c_3=3|r)
\end{bmatrix}
$$

$$
p_{c_4}=\begin{bmatrix}
	p(c_4=0|r) \\
	p(c_4=1|r) \\
	p(c_4=2|r) \\
	p(c_4=3|r)
\end{bmatrix}
$$

$$
p_{c_5}=\begin{bmatrix}
	p(c_5=0|r) \\
	p(c_5=1|r) \\
	p(c_5=2|r) \\
	p(c_5=3|r)
\end{bmatrix}
$$

Perform the $$H_2$$ transform on each of the three vectors above; $$H_2$$ is computed from $$H_1$$:

$$
H_2 =
\begin{bmatrix}
	H_1 & H_1 \\
	H_1 & -H_1
\end{bmatrix}
$$

Then:

$$
P_{c_3}=H_2p_{c_3} =
\begin{bmatrix}
	1& 1  &  1&1\\
	1& -1 &  1&-1 \\
	1& 1  &  -1&-1\\
	1& -1 &  -1&1 \\
\end{bmatrix}
\begin{bmatrix}
	p(c_3=0|r) \\
	p(c_3=1|r) \\
	p(c_3=2|r) \\
	p(c_3=3|r)
\end{bmatrix}
=
\begin{bmatrix}
	P_{c_3}^{(1)}\\
	P_{c_3}^{(2)} \\
	P_{c_3}^{(3)} \\
	P_{c_3}^{(4)}
\end{bmatrix} \tag{11}
$$

Similarly:

$$
P_{c_4}=H_2p_{c_4} =
\begin{bmatrix}
	1& 1  &  1&1\\
	1& -1 &  1&-1 \\
	1& 1  &  -1&-1\\
	1& -1 &  -1&1 \\
\end{bmatrix}
\begin{bmatrix}
	p(c_4=0|r) \\
	p(c_4=1|r) \\
	p(c_4=2|r) \\
	p(c_4=3|r)
\end{bmatrix}
=
\begin{bmatrix}
	P_{c_4}^{(1)}\\
	P_{c_4}^{(2)} \\
	P_{c_4}^{(3)} \\
	P_{c_4}^{(4)}
\end{bmatrix} \tag{12}
$$

$$
P_{c_5}=H_2p_{c_5} =
\begin{bmatrix}
	1& 1  &  1&1\\
	1& -1 &  1&-1 \\
	1& 1  &  -1&-1\\
	1& -1 &  -1&1 \\
\end{bmatrix}
\begin{bmatrix}
	p(c_5=0|r) \\
	p(c_5=1|r) \\
	p(c_5=2|r) \\
	p(c_5=3|r)
\end{bmatrix}
=
\begin{bmatrix}
	P_{c_5}^{(1)}\\
	P_{c_5}^{(2)} \\
	P_{c_5}^{(3)} \\
	P_{c_5}^{(4)}
\end{bmatrix} \tag{13}
$$

Then, perform the corresponding element-by-element multiplication of the three vectors $$P_{c_3}, P_{c_4}, P_{c_5}$$

$$
P_{c_{345}} =P_{c_3} \times_{element-wise}\times_{element-wise}P_{c_4}\
\times_{element-wise} P_{c_5} \\
\begin{bmatrix}
	P_{c_3}^{(1)}P_{c_4}^{(1)}P_{c_5}^{(1)} \\
	P_{c_3}^{(2)}P_{c_4}^{(2)}P_{c_5}^{(2)} \\
	P_{c_3}^{(3)}P_{c_4}^{(3)}P_{c_5}^{(3)} \\
	P_{c_3}^{(4)}P_{c_4}^{(4)}P_{c_5}^{(4)}
\end{bmatrix}
$$

Then perform the $$H_2^{-1}$$ transform on the result of the expression above, where:

$$
H_2^{-1} =\frac{1}{4} H_2 = \frac{1}{4}\begin{bmatrix}
	1& 1  &  1&1\\
	1& -1 &  1&-1 \\
	1& 1  &  -1&-1\\
	1& -1 &  -1&1 \\
\end{bmatrix}
$$

Then:

$$
H_2^{-1} P_{c_{345}}
$$

is exactly the result of Formula (10), that is, the result of Formula (5), because this transform $$H_2$$ is a transform in the modulo-4 field, and the fast Fourier transform algorithm can be used to reduce the amount of computation.



Formulas (11)(12)(13) are all a kind of transform, from one domain to another domain; here it should be understood as the Fourier transform under modulo-4 arithmetic, and then the fast Fourier transform can be used; the algorithm of the fast Fourier transform is as follows:

For Formula (11)

$$
P_{c_3}=H_2p_{c_3}  = H_2 \begin{bmatrix}
	p(c_3=0|r) \\
	p(c_3=1|r) \\
	p(c_3=2|r) \\
	p(c_3=3|r)
\end{bmatrix} \\
=p_{c_3} \times_0 F \times_1 F
$$

If it is a $$2^s$$-ary LDPC code, then this transform is:

$$
FFT(u) = u \times_0 F \times_1 F \times ... \times_{s-1} F
$$

where

$$
F = \begin{bmatrix}
	1 & 1 \\
	1 & -1
\end{bmatrix}
$$

Then an s-dimensional vector $$[a_0,a_1,a_2,...,a_{s-1}]$$ , then

$$w = u \otimes_l F$$ is computed according to the following formulas:

$$
\begin{aligned}
	w(a_0,a_1,...,a_{l-1},0,a_{l+1},...,a_{s-1}) = u(a_0,a_1,...,a_{l-1},0,a_{l+1},...,a_{s-1}) + \\
	u(a_0,a_1,...,a_{l-1},1,a_{l+1},...,a_{s-1})
\end{aligned}
$$

$$
\begin{aligned}
	w(a_0,a_1,...,a_{l-1},1,a_{l+1},...,a_{s-1}) = u(a_0,a_1,...,a_{l-1},0,a_{l+1},...,a_{s-1}) - \\
	u(a_0,a_1,...,a_{l-1},1,a_{l+1},...,a_{s-1})
\end{aligned}
$$

Let us take an example to illustrate. Suppose what we want to analyze is an 8-ary LDPC code, then each datum has 3 bits; we denote one datum as $$c$$ , which is a datum composed of 3 bits, with a value range from 0 to 7.  Each of these eight possible values corresponds to a probability, and the 8 probabilities make up a vector:

$$
u =\begin{bmatrix}
	p(c=000) \\
	p(c=001) \\
	p(c=010) \\
	p(c=011) \\
	p(c=100) \\
	p(c=101) \\
	p(c=110) \\
	p(c=111)
\end{bmatrix}
$$

Then the result of $$u \times_0 F$$ is still an 8-dimensional vector, and its result is:

$$
u_1=\begin{bmatrix}
	p(c=000) + p(c=100) \\
	p(c=001) + p(c=101) \\
	p(c=010) + p(c=110) \\
	p(c=011) + p(c=111) \\
	p(c=000) - p(c=100) \\
	p(c=001) - p(c=101) \\
	p(c=010) - p(c=110) \\
	p(c=011) - p(c=111) \\
\end{bmatrix}
$$

Then $$u_1 \times_1 F$$

$$
u_2=\begin{bmatrix}
	u_1(000) + u_1(010) \\
	u_1(001) + u_1(011) \\
	u_1(000) - u_1(010) \\
	u_1(001) - u_1(011) \\
	u_1(100) + u_1(110) \\
	u_1(101) + u_1(111) \\
	u_1(100) - u_1(110) \\
	u_1(101) - u_1(111) \\
\end{bmatrix}
$$

Finally $$u_2 \times_2 F$$

$$
u_3=\begin{bmatrix}
	u(000) + u(001) \\
	u(000) - u(001) \\
	u(010) + (011) \\
	u(010) - u(011) \\
	u(100) + u(101) \\
	u(100) - u(101) \\
	u(110) + u(111) \\
	u(110) - u(111) \\
\end{bmatrix}
$$

Then $$u_3$$ is the final result after the transform.

### On the Introduction of the Permutation

The FFT fast transform introduced on the basis of Formula (6) and Formula (7) is in fact carried out on the $$x_1, x_2$$ in $$x_1 + x_2$$, so we assumed that Formula (5) is of the form of Formula (5.1); therefore, we need a permutation step to turn Formula (5) into the form of Formula (5.1), and then use the FFT fast transform.

In our example, suppose we consider $$c_3$$, then we denote $$h_3 c_3$$ as $$c_3^p$$, and then we need to use

$$
p_{c_3^p}=\begin{bmatrix}
	p(c_3^p=0|r) \\
	p(c_3^p=1|r) \\
	p(c_3^p=2|r) \\
	p(c_3^p=3|r)
\end{bmatrix}
$$

to multiply by $$H_2$$.



So, there exists a permutation in this; suppose $$h_3 = 2$$, then:

$$
\begin{aligned}
	c_3 = 0, ==> c_3^p=2\times 0 = 0  \\
	c_3 = 1, ==> c_3^p=2\times 1= 2  \\
	c_3 = 2, ==> c_3^p=2\times 2= 0  \\
	c_3 = 3, ==> c_3^p=2\times 3= 2
\end{aligned}
$$

So ( in the expressions the conditions of the conditional probabilities have all been omitted ):

$$
\begin{aligned}
	p(c_3^p=0) &= p(c_3 = 0) + p(c_3 = 2)  \\
	p(c_3^p=1) &= 0 \\
	p(c_3^p=2) &= p(c_3 = 1) + p(c_3 = 3) \\
	p(c_3^p=3) &= 0
\end{aligned}
$$

## An Example of the Permutation in the FFT-QSPA Algorithm for Multi-ary LDPC Codes


Suppose we have a parity-check equation:

$$
h_1 c_1 + h_2 c_2 + h_3c_3 = 0
$$

We let $$h_3 = 3$$, and then consider computing the probability for $$c_3$$; suppose $$h_1 = 3, h_2 = 2$$, then

$$
\begin{aligned}
	c_1 = 0 ===> c_1^p = 0 \\
	c_1 = 1 ===> c_1^p = 3  \\
	c_1 = 2 ===> c_1^p = 2 \\
	c_1 = 3 ===> c_1^p = 1
\end{aligned}
$$

and:

$$
\begin{aligned}
	c_2 = 0 ===> c_2^p = 0 \\
	c_2 = 1 ===> c_2^p = 2 \\
	c_2 = 2 ===> c_2^p = 3 \\
	c_2 = 3 ===> c_2^p = 1
\end{aligned}
$$

So, the probabilities we want to compute have the following four cases:

$$
\begin{aligned}
	\sum_{h_1c_1+h_2c_2=0} p(c_1)p(c_2)  = p_1^0(p_2^0+p_2^2) + p_1^2 ( p_2^1+p_2^3)  \\
	\sum_{h_1c_1+h_2c_2=1} p(c_1)p(c_2)  = p_1^1(p_2^1+p_2^3) + p_1^3 ( p_2^0+p_2^2)  \\
	\sum_{h_1c_1+h_2c_2=2} p(c_1)p(c_2)  = p_1^0(p_2^1+p_2^3) + p_1^2 ( p_2^0+p_2^2)  \\
	\sum_{h_1c_1+h_2c_2=3} p(c_1)p(c_2)  = p_1^1(p_2^0+p_2^2) + p_1^3 ( p_2^1+p_2^3)  \\
\end{aligned}
$$

So we can construct two vectors:

$$
\begin{bmatrix}
	p(c_1^p = 0) = p(c_1=0) = p_1^0 \\
	p(c_1^p = 1) = p(c_1=3) = p_1^3 \\
	p(c_1^p = 2) = p(c_1=2) = p_1^2 \\
	p(c_1^p = 3) = p(c_1=1) = p_1^1
\end{bmatrix}
$$

as well as:

$$
\begin{bmatrix}
	p(c_2^p=0) =p(c_2=0)+p(c_2=2) = p_2^0+p_2^2  \\
	p(c_2^p=1) =0  \\
	p(c_2^p=2) =p(c_2=1)+p(c_2=3) = p_2^1+p_2^3  \\
	0
\end{bmatrix}
$$

Then, after the two vectors are transformed into the transform domain:

$$
\begin{aligned}
	H_2
	\begin{bmatrix}
		p_1^0 \\
		p_1^3 \\
		p_1^2 \\
		p_1^1
	\end{bmatrix}
	=\begin{bmatrix}
		p_1^0+p_1^1+p_1^2+p_1^3 \\
		p_1^0-p_1^1+p_1^2-p_1^3 \\
		p_1^0-p_1^1-p_1^2+p_1^3 \\
		p_1^0+p_1^1-p_1^2-p_1^3
	\end{bmatrix}  \\
	\quad \\
	H_2
	\begin{bmatrix}
		p_2^0+p_2^2 \\
		0 \\
		p_2^1+p_2^3 \\
		0
	\end{bmatrix}
	=\begin{bmatrix}
		p_2^0+p_2^1+p_2^2+p_2^3 \\
		p_2^0+p_2^1+p_2^2+p_2^3 \\
		p_2^0-p_2^1+p_2^2-p_2^3 \\
		p_2^0-p_2^1+p_2^2-p_2^3
	\end{bmatrix}  \\
	\quad \\
\end{aligned}
$$

Then, performing an element-by-element multiplication on the two vectors above, we have:

$$
V = \begin{bmatrix}
	(p_1^0+p_1^1+p_1^2+p_1^3)(p_2^0+p_2^1+p_2^2+p_2^3) \\
	(p_1^0-p_1^1+p_1^2-p_1^3)(p_2^0+p_2^1+p_2^2+p_2^3) \\
	(p_1^0-p_1^1-p_1^2+p_1^3)(p_2^0-p_2^1+p_2^2-p_2^3) \\
	(p_1^0+p_1^1-p_1^2-p_1^3)(p_2^0-p_2^1+p_2^2-p_2^3)
\end{bmatrix}  \\
$$

Then:

$$
\begin{aligned}
	H_2^{-1}V = \frac{1}{4} H_2 V  = \\
	\begin{bmatrix}
		p_1^0(p_2^0+p_2^2) + p_1^2 ( p_2^1+p_2^3)  \\
		p_1^1(p_2^1+p_2^3) + p_1^3 ( p_2^0+p_2^2) \\
		p_1^0(p_2^1+p_2^3) + p_1^2 ( p_2^0+p_2^2) \\
		p_1^1(p_2^0+p_2^2) + p_1^3 ( p_2^1+p_2^3)
	\end{bmatrix}
\end{aligned}
$$

### Quaternary Codes
GF(4) is not the existence of a finite field, because it does not satisfy all the requirements of the definition of a finite field, because the element 2 has no reciprocal.

However, if we modify the elements a little and change GF(4) into $$GF(2^2)$$, a set of 4 elements can also become a finite field:

Addition:

| + | 00 | 01 | 10 | 11 |
|---|---|---|---|---|
| 00 | 00 | 01 | 10 | 11 |
| 01 | 01 | 00 | 11 | 10 |
| 10 | 10 | 11 | 00 | 01 |
| 11 | 11 | 10 | 01 | 00 |


Multiplication:

| x | 00 | 01 | 10 | 11 |
|---|---|---|---|---|
| 00 | 00 | 00 | 00 | 00 |
| 01 | 00 | 01 | 10 | 11 |
| 10 | 00 | 10 | 11 | 01 |
| 11 | 00 | 11 | 01 | 10 |



The difference between GF(4) and $$GF(2^2)$$ is that in $$GF(2^2)$$, when computing, every bit is a modulo-2 operation, that is, 'exclusive or'



What is used in communication coding is this form of finite field, $$GF(2^m)$$, because binary addition / exclusive or and the like are very suitable for communication hardware implementation.





Appendix:

$$GF(2^2)$$  is a finite field containing 4 elements, and its minimal polynomial is $$x^2 + x + 1$$. Here, I will use ab to denote ax + b (that is, $$10 = 1 \cdot x + 0$$); this is a standard representation when considering polynomials over the finite field GF(2), because it is consistent with the way we handle the bits in a byte.

As you can see, addition is done by bitwise exclusive or (xor): $$ab + cd := ab \oplus cd$$

For multiplication, we perform standard polynomial multiplication, but afterwards it must be reduced by the minimal polynomial. That is, we make use of the identity $$x^2 = x + 1$$. Therefore:

$$
ab \cdot cd = (a \cdot x + b)(c \cdot x + d) = (a \cdot c) x^2 + (a \cdot d + b \cdot c)x + (b \cdot d) = (a \cdot d + b \cdot c + a \cdot c)x + [b \cdot d + a \cdot c]
$$

As stated above, + is the exclusive-or operation (denoted as $$\oplus$$), and the hint given in the book is that the multiplication of the coefficients is equivalent to the AND operation (which I denote with \&):

$$
ab \cdot cd = (a \& d \oplus b \& c \oplus a \& c)x \oplus [b \& d \oplus a \& c] = [a \& d \oplus b \& c \oplus a \& c][b \& d \oplus a \& c]
$$