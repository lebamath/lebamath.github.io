---
layout: default
title: "A Brief Analysis of Bit-Flipping Decoding"
lang: en
back_url: /index.html?lang=en
---
## A Brief Analysis of Bit-Flipping Decoding
LDPC codes (low-density parity-check) are in fact a kind of linear block code. However, because of the low-density property of LDPC codes, decoding can be carried out with rather efficient algorithms. This short article does not attempt to discuss every aspect of LDPC codes; rather, on the assumption that the reader is already familiar with the basic concepts of linear block codes and with the parity-check matrix, it tries to give a description of one simple decoding algorithm for LDPC codes. In Gallager's doctoral dissertation [1], a hard-decision decoding method called Bit-Flipping is described very briefly. The English passage below is quoted from Gallager's doctoral dissertation [1]:

......, the decoder computes all the parity checks and then changes any digit that is contained in more than some fixed number of unsatisfied parity-check equations. Using these new values, the parity checks are recomputed, and the process is repeated until the parity checks are all satisfied.
		

An example is given below to explain this process concretely.



Suppose the parity-check matrix is:

$$
A = \begin{bmatrix}
	1& 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1\\
	1& 0 & 1 & 0 & 1 & 1 & 0 & 1 & 1 & 0\\
	0& 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1 & 1\\
	0& 1 & 0 & 1 & 1 & 1 & 0 & 1 & 0 & 1\\
	1& 1 & 0 & 1 & 0 & 0 & 1 & 1 & 1 & 0
\end{bmatrix}
$$

Represent the received codeword as a vector:

$$
r=\begin{bmatrix}
	r_1 & r_2 & r_3 & r_4 & r_5 & r_6 & r_7  & r_8 & r_9 & r_{10}
\end{bmatrix}
$$

Then, from the received codeword $$r$$, the syndrome can be computed (in medicine this word means a set of symptoms; here it may be understood as something used to "diagnose" whether the received codeword contains errors, and to diagnose which parity-check equations are not satisfied):

$$
s = r A^T
$$

The computed result $$s$$ is a vector containing 5 elements, denoted as:

$$
s = \begin{bmatrix}
	s_1& s_2 & s_3 & s_4 & s_5 
\end{bmatrix}
$$

For ease of understanding, we expand the above equation in matrix form into 5 parity-check equations:

$$
\begin{aligned}
	s_1 &= r_1 + r_2 + r_3 + r_6 + r_7 + r_{10} \\
	s_2 &= r_1 + r_3 + r_5 + r_6 + r_8 + r_{9} \\
	s_3 &= r_3 + r_4 + r_5 + r_7 + r_9 + r_{10} \\
	s_4 &= r_2 + r_4 + r_5 + r_6 + r_8 + r_{10} \\
	s_5 &= r_1 + r_2 + r_4 + r_7 + r_8 + r_{9} 
\end{aligned}
$$



Now let us take a concrete example: a codeword is transmitted, the received codeword contains one error, and the bit-flipping algorithm is used to perform the decoding.

The transmitted codeword is:

$$
c=\begin{bmatrix}
	0&  0&  0&  1&  0&  1&  0&  1&  0&1
\end{bmatrix}
$$

The received codeword contains an error:

$$
r=\begin{bmatrix}
	0&  0&  0&  1&  1&  1&  0&  1&  0&1
\end{bmatrix}
$$

It can be seen that $$r_5$$ is in error. Below, we try to decode it with the bit-flipping algorithm:

Step one, compute the syndrome:

$$
\begin{aligned}
	s_1 &= r_1 + r_2 + r_3 + r_6 + r_7 + r_{10} = 0+0+0+1+0+1=0 \nonumber\\
	s_2 &= r_1 + r_3 + r_5 + r_6 + r_8 + r_{9}  = 0+0+1+1+1+0=1\nonumber\\
	s_3 &= r_3 + r_4 + r_5 + r_7 + r_9 + r_{10} =0+1+1+0+0+1=1\nonumber\\
	s_4 &= r_2 + r_4 + r_5 + r_6 + r_8 + r_{10} =0+1+1+1+1+1=1\nonumber\\
	s_5 &= r_1 + r_2 + r_4 + r_7 + r_8 + r_{9} = 0+0+1+0+1+0=0\nonumber
\end{aligned}
$$


​			

​                 Note: the additions above are modulo-2 additions, $$1+1=0$$

Step two, check whether all elements of the syndrome are 0. If they are all 0, decoding ends, and the current $$r$$ is the correct codeword; if the syndrome is not 0, jump to step three.

Step three, look at which parity-check equations $$r_1$$ appears in, and among the parity-check equations in which $$r_1$$ appears, count how many are not satisfied, that is, how many $$s_i$$ are not equal to 0.

​      $$r_1$$ appears in $$s_1,s_2,s_5$$, among which $$s_2=1$$; therefore, among the parity-check equations in which $$r_1$$ appears, 1 equation is not satisfied;

​     $$r_2$$ appears in $$s_1,s_4,s_5$$, among which $$s_4=1$$; therefore, among the parity-check equations in which $$r_2$$ appears, 1 equation is not satisfied;

By analogy, the following table can be drawn up:




| $$r_i$$ | Which parity-check equations $$r_i$$ appears in | Unsatisfied parity-check equations | Number of unsatisfied parity-check equations |
|---|---|---|---|
| $$r_1$$ | $$s_1,s_2,s_5$$ | $$s_2$$ | 1 |
| $$r_2$$ | $$s_1,s_4,s_5$$ | $$s_4$$ | 1 |
| $$r_3$$ | $$s_1,s_2,s_3$$ | $$s_2,s_3$$ | 2 |
| $$r_4$$ | $$s_3,s_4,s_5$$ | $$s_3,s_4$$ | 2 |
| $$r_5$$ | $$s_2,s_3,s_4$$ | $$s_2,s_3,s_4$$ | 3 |
| $$r_6$$ | $$s_1,s_2,s_4$$ | $$s_2,s_4$$ | 2 |
| $$r_7$$ | $$s_1,s_3,s_5$$ | $$s_3$$ | 1 |
| $$r_8$$ | $$s_2,s_4,s_5$$ | $$s_2,s_4$$ | 2 |
| $$r_9$$ | $$s_2,s_3,s_5$$ | $$s_2,s_3$$ | 2 |
| $$r_{10}$$ | $$s_1,s_3,s_4$$ | $$s_3,s_4$$ | 2 |


Among the "Number of unsatisfied parity-check equations", choose the largest one; in the table above the largest is 3, which corresponds to the column of $$r_5$$. Therefore, flip $$r_5$$ from 1 to 0 (Flipping). After the received codeword has been flipped, it is denoted as the new $$r$$. Jump back to step one and continue.

At this point $$r$$ is:

$$
r=\begin{bmatrix}  0&  0&  0&  1&  0&  1&  0&  1&  0&1\end{bmatrix}
$$

Compute the syndrome $$s$$ again, and the result obtained is

$$
s=\begin{bmatrix}  s_1&  s_2&  s_3&  s_4&  s_5\end{bmatrix}=\begin{bmatrix}  0&  0&  0&  0&  0\end{bmatrix}
$$

Decoding ends.

The correct codeword is:

$$
r=\begin{bmatrix}  0&  0&  0&  1&  0&  1&  0&  1&  0&1\end{bmatrix}
$$

As can be seen, it is the same as the originally transmitted codeword:

$$
c=\begin{bmatrix}
	0&  0&  0&  1&  0&  1&  0&  1&  0&1
\end{bmatrix}
$$

Understood in plain terms, this iterative algorithm considers the bits in the received codeword: if, among the parity-check equations a bit participates in, more of them are unsatisfied, then it is more likely that this bit is in error, so this bit is flipped first as a trial.

[1]  R.G.Gallager, Low-Density Parity-Check Codes, *IRE Trans.Info.Theory*  IT-8:21-28. 1962.