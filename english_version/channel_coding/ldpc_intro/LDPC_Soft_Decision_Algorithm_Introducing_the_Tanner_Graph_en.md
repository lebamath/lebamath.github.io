---
layout: default
title: "LDPC Soft-Decision Algorithm -- Introducing the Tanner Graph"
lang: en
back_url: /index.html?lang=en
---
## LDPC Soft-Decision Algorithm -- Introducing the Tanner Graph
After the explanations in the several previous articles and videos, we have already clearly understood the flow of LDPC decoding and the logical thinking behind it. Now, we can introduce the Tanner graph, so as to make a graphical abstraction and understanding of the LDPC decoding algorithm. I think that introducing the Tanner graph only after the flow of the decoding algorithm has been understood makes it rather easy to understand the role of the Tanner graph, gives a clearer graphical understanding, and makes it convenient for us to memorize the flow of LDPC decoding.



We know that the decoding algorithm, whether in the Log form or not in the Log form, has two main steps and ideas:



1) The probability, or some kind of measure, of whether a parity-check equation holds

2) The probability, or some kind of measure, of the value of a bit



And by iterating these two steps against each other, we gradually approach the ideal goal of successful decoding. So, we can call the result of step 1) a check, and then introduce check nodes in the Tanner graph; the result of step 2) is called data or bits, and variable nodes are introduced in the Tanner graph.

Between these two kinds of nodes, we introduce connecting lines; the rule is: if a certain bit participates in a certain parity-check equation, then a connecting line is introduced between the corresponding bit node and check node, expressing a kind of mutual relation: the bit participates in the parity-check equation, and the parity-check equation contains the bit.

This is generally called a Tanner graph, and is also called a bipartite graph. This graph itself is not an exact expression of the algorithm, but a vivid expression of the relations among the various roles in the algorithm; as to what lies behind this representation, it still needs rigorous mathematical derivation as support. That is, what kind of mathematical formulas there are to describe the dependency relations among the roles.



This article takes this parity-check matrix as its example throughout.

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

Then, using a Tanner graph to express these dependency relations, it can be expressed as the figure below:


![tanner_graph.png](/figure/LDPC译码浅析/tanner_graph.png)


The relation between the two kinds of variable nodes can be described with this idea: a check node (a parity-check equation) tells some kind of measure information of its own to the variable nodes corresponding to the bits that this parity-check equation contains; similarly, a variable node also sends some kind of measure of the probability information of its own value to the check nodes corresponding to the parity-check equations that contain this bit.



The LDPC decoding we derived previously all iterates in two main steps: using the probability information of the bits to estimate the probability information of the parity-check equations, and then using the information of the parity-check equations to update the probability information of the bits. This process, described on the Tanner graph: the information of the variable nodes is sent to the check nodes connected to them, the check nodes accordingly compute their own information, and then the check nodes send this information to the variable nodes connected to them.

In the representation of the Tanner graph, the problem then becomes:

1) The information sent between the nodes -- what kind of information is it?

2) After each node receives the information sent to it, how does it compute its own information, or in other words, how does it compute the information it is about to send out?



The answers to these two questions both depend on the concrete algorithm, so, for different algorithms, the answers to the two questions above are also not quite the same. The two algorithms we talked about previously have two different answers.



1. The information sent between the nodes -- what kind of information is it?



**For the algorithm not in the log likelihood ratio form**:

The information that check node m sends to variable node n:

$$
r_{mn}(x)=p(z_m=0|c_n=x,r) =
\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in N_{m,n}\}: x=\sum_l x_l\}}
\quad \prod_{l \in N_{m,n}}p(c_l=x_l|r)
$$

For example:

$$
\begin{aligned}
	r_{25}(x)=p(z_2=0|c_5=x,r) =
	\sum_{\{x_1+x_3+x_6+x_8+x_9=x\}}
	\quad \prod_{l \in \{1,3,6,8,9 \}}p(c_l=x_l|r)  \\
	\sum_{\{x_1+x_3+x_6+x_8+x_9=x\}}
	\quad p(c_1=x_l|r)p(c_3=x_3|r)p(c_6=x_6|r)p(c_8=x_8|r)p(c_9=x_9|r)
\end{aligned}
$$

Of course, for the computation of this formula there is a simplified fast algorithm; it will not be discussed here, and interested friends can look at the earlier content of the column.



The information that variable node n sends to check node m: $$r_{m,n}$$

$$
q_{mn}(x)=p(c_n=x|\{z_{m'}=0\},r) =\frac{1}{p(\{z_{m'}=0\}|r)}p(c_n=x|r_n)\prod_{m'} p(z_{m'}=0|c_n=x,r)
$$

For example:

$$
\begin{aligned}
	q_{21}(x)&=p(c_1=x|\{z_{m'}=0,m'\in \{1,5\}\},r) \\
	&=\frac{1}{p(\{z_{m'}=0,m'\in \{1,5\}\}|r)}p(c_1=x|r_n)\prod_{m'\in \{1,5\}\}} p(z_{m'}=0|c_1=x,r)   \\
	&=\frac{1}{p(\{z_{m'}=0,m'\in \{1,5\}\}|r)}p(c_1=x|r_n)  p(z_1=0|c_1=x,r)  p(z_5=0|c_1=x,r)
\end{aligned}
$$

**For the algorithm in the log likelihood ratio form:**

The information that check node m sends to variable node n:

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq n\})}{2}))
$$

For example:

$$
\begin{aligned}
	\eta_{2,5}^{[l]} &=- 2 tanh^{-1}(\prod_{j \in \{1,3,6,8,9\} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq 5\})}{2}))  \\    \\
	&=- 2 tanh^{-1}(  \\
	&tanh(-\frac{\lambda^{[l]}(c_1|\{r_i,i \neq 5\})}{2}) \\
	&tanh(-\frac{\lambda^{[l]}(c_3|\{r_i,i \neq 5\})}{2})\\
	&tanh(-\frac{\lambda^{[l]}(c_6|\{r_i,i \neq 5\})}{2})\\
	&tanh(-\frac{\lambda^{[l]}(c_8|\{r_i,i \neq 5\})}{2})\\
	&tanh(-\frac{\lambda^{[l]}(c_9|\{r_i,i \neq 5\})}{2})\\
	)
\end{aligned}
$$

The information that variable node n sends to check node m:

$$
\begin{aligned}
	\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}   \\  \\
	\lambda^{[l+1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l]}(c_j|r) -\eta_{m,j}^{[l]}
\end{aligned}
$$

For example:

​     The information that variable node 1 sends to check node 2:

$$
\begin{aligned}
	\lambda^{[l]}(c_1|r) = log\frac{p(c_1=1|r)}{p(c_1=0|r)} =\frac{2}{\sigma^2}r_n + \sum_{m \in \{1,2,5\}} \eta^{[l]}_{m,1}   \\  \\
	\lambda^{[l+1]}(c_1|\{r_i,i \neq n\}) = \lambda^{[l]}(c_1|r) -\eta_{2,1}^{[l]}
\end{aligned}
$$

2. As for the answer to the second question, namely how each node computes its own information after receiving the information sent to it: since one's own message is exactly the information sent to the other kind of node, the answer to this question was already implied when the first question was answered.

After a variable node receives the message from a check node, the formula used to **compute the information of the variable node**, in the non log likelihood ratio algorithm, is:

$$
q_{mn}(x)=p(c_n=x|\{z_{m'}=0\},r) =\frac{1}{p(\{z_{m'}=0\}|r)}p(c_n=x|r_n)\prod_{m'} p(z_{m'}=0|c_n=x,r)
$$

So, the variable node computes, from the information received from the check nodes, by means of a continued product

Whereas in the log likelihood ratio algorithm, because the log has been taken, it becomes a computation by means of a continued sum

$$
\begin{aligned}
	\lambda^{[l]}(c_n|r) = log\frac{p(c_n=1|r)}{p(c_n=0|r)} =\frac{2}{\sigma^2}r_n + \sum_m \eta^{[l]}_{m,n}   \\  \\
	\lambda^{[l+1]}(c_j|\{r_i,i \neq n\}) = \lambda^{[l]}(c_j|r) -\eta_{m,j}^{[l]}
\end{aligned}
$$

After a check node receives the messages from the variable nodes, the formula used to **compute the information of the check node**, in the non log likelihood ratio algorithm, is:

$$
r_{mn}(x)=p(z_m=0|c_n=x,r) =
\sum_{\{ \{ x_{\grave{n}},\space \grave{n} \in N_{m,n}\}: x=\sum_l x_l\}}
\quad \prod_{l \in N_{m,n}}p(c_l=x_l|r)
$$

Then, on the combinations of the various pieces of information received from the variable nodes, a continued product is done first, and then a continued sum is done.

Whereas in the log likelihood ratio algorithm, it is expressed with the tanh function, a more complicated kind of computation formula.

$$
\eta_{m,n}^{[l]}=- 2 tanh^{-1}(\prod_{j \in N_{m,n} } tanh(-\frac{\lambda^{[l]}(c_j|\{r_i,i \neq n\})}{2}))
$$