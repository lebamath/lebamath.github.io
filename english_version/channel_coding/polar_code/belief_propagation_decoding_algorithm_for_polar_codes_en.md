---
layout: default
title: "Belief Propagation Decoding Algorithm for Polar Codes"
lang: en
back_url: /index.html?lang=en
---

# Belief Propagation Decoding Algorithm for Polar Codes

## Message passing and the derivation of the related formulas

This article introduces the belief propagation algorithm (Belief Propagation) for Polar Codes. This article requires a little background knowledge of Factor Graphs or Forney-style factor graphs (FFG) (also called Normal Factor Graphs).

Schematic diagram of the N = 2 polar code, as shown in Figure 1.

![图1：Schematic diagram of the N = 2 polar code](/figure/极化码/bp/polar_code_core_N_is_2.png)

*图1：Schematic diagram of the N = 2 polar code*

The BP algorithm is a message passing algorithm which requires multiple iterations. In the schematic diagram of the polar code, since it is laid out in the left-right direction, the directions of message passing are from right to left and from left to right. This is shown in Figure 2.

![图2：Message passing of the BP algorithm for polar codes](/figure/极化码/bp/polar_bp_2.png)

*图2：Message passing of the BP algorithm for polar codes*


### Computing LR(a)
The probability LR(a) is in fact the probability computed in the leftward direction of a, and therefore corresponds to $$a_l$$ in Figure 2; for convenience of writing, the subscript $$l$$ is omitted in all the formulas that follow.

In order to compute the probability of a, the messages that are passed are as shown in Figure 3.

![图3：Message passing of the BP algorithm for polar codes, computing a](/figure/极化码/bp/polar_bp_3.png)

*图3：Message passing of the BP algorithm for polar codes, computing a*

If we want to know the probability $$p(a=0)$$, we know that $$a =  e \oplus c$$, and then there are two cases that satisfy $$a = 0$$:

$$e = 0$$ and $$c =0$$  

$$e = 1$$ and $$c = 1$$

Therefore

$$
p(a=0) = p(e=0, c=0) + p(e=1,c=1)
\tag{1}
$$

In the graph, we always assume that these events are mutually independent, although in reality they are not mutually independent because of the existence of cycles. This assumption then requires performing multiple iterations in order to approach the true values.

Continuing the derivation of Equation (1) then gives:

$$
p(a=0) = p(e=0) p(c=0) + p(e=1) p(c=1)
\tag{4}
$$

And $$e = 0$$ if and only if $$b = 0$$ and $$d = 0$$, so, 

$$
p(e=0) = p(b=0,d=0) = p(b=0)p(d=0).
\tag{2}
$$

Similarly, $$e = 1$$ if and only if $$b = 1$$ and $$d = 1$$, so, 

$$
p(e=1) = p(b=1,d=1) = p(b=1)p(d=1).
\tag{3}
$$

Substituting (2) and (3) into (4)

$$
p(a=0) = p(b=0)p(d=0) p(c=0) + p(b=1)p(d=1) p(c=1)
$$

Using a similar derivation process, we can obtain

$$
p(a=1) = p(b=1)p(d=1) p(c=0) + p(b=0)p(d=0) p(c=1)
$$

Here we define the likelihood ratio of a binary random variable X taking the value 0/1 as:

$$
LR(X) = \frac{p(X=1)}{p(X=0)}
$$

From the formula, the likelihood ratio LR(a) ( Likelyhood Ratio) can be computed:

$$
\begin{aligned}
		LR(a) 
		&= \frac{p(a=1)}{p(a=0)} = \frac{p(b=1)p(d=1) p(c=0) + p(b=0)p(d=0) p(c=1)}{p(b=0)p(d=0) p(c=0) + p(b=1)p(d=1) p(c=1) }  \\ \\
		&=  \frac{\frac{p(b=1)}{p(b=0)} \frac{p(d=1)}{p(d=0)} + \frac{p(c=1)}{p(c=0)} }{1+ \frac{p(b=1)}{p(b=0)}  \frac{p(d=1)}{p(d=0)} \frac{p(c=1)}{p(c=0)} } \\ \\
		&= \frac{LR(b)LR(d) + LR(c)}{ 1+LR(b) LR(d) LR(c)}
	\end{aligned}
\tag{6}
$$

Using Equations (2) and (3), we can obtain the formula for LR(e):

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(b=1)p(d=1)}{p(b=0)p(d=0)}  = LR(b)LR(d)
\tag{5}
$$

Applying Equation (5) to Equation (6), we obtain:

$$
LR(a) = \frac{LR(e)+LR(c)}{1+LR(c)LR(e)}
$$

### Computing LR(b)

What needs to be computed here is $$b_l$$ in Figure 2; for convenience of writing, the subscript $$l$$ is omitted in all the formulas that follow, and the subscripts $$l,r$$ in the input messages are also all omitted.

In order to compute the probability of b, the messages that are passed are as shown in Figure 4.
![图4：Message passing of the BP algorithm for polar codes, computing b](/figure/极化码/bp/polar_bp_4.png)

*图4：Message passing of the BP algorithm for polar codes, computing b*

$$p(b=0)$$: $$b=0$$ means that it requires $$e = 0, d = 0$$, so

$$
p(b=0) = p(e=0,d=0) = p(e=0) p(d=0)
\tag{7}
$$

Similarly, $$p(b=1)$$: $$b=1$$ means that it requires $$e = 1, d = 1$$, so

$$
p(b=1) = p(e=1,d=1) = p(e=1) p(d=1)
\tag{8}
$$

Combining Equations (7) and (8):

$$
LR(b) = \frac{p(b=1)}{p(b=0)} = \frac{p(e=1) p(d=1)}{p(e=0) p(d=0)} = LR(e)LR(d)
$$

From the point of view of b, at this time $$e = a \oplus c$$, and then there are two cases for $$e = 0$$:

$$a =0, c = 0$$

$$a = 1,c=1$$

Then:

$$
p(e=0) = p(a=0,c=0) + p(a=1,c=1) = p(a=0)p(c=0) + p(a=1)p(c=1)
\tag{9}
$$

Similarly, there are two cases for $$e = 1$$:

$$a =0, c = 1$$

$$a = 1,c=0$$

Then:

$$
p(e=0) = p(a=0,c=1) + p(a=1,c=0) = p(a=0)p(c=1) + p(a=1)p(c=0)
\tag{10}
$$

Combining Equations (9) and (10), LR(e) can be derived:

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(a=0)p(c=1) + p(a=1)p(c=0)}{p(a=0)p(c=0) + p(a=1)p(c=1)}
$$

For the above expression, dividing both the numerator and the denominator by $$p(a=0)p(c=0)$$ gives:

$$
LR(e) = \frac{\frac{p(c=1)}{p(c=0)} + \frac{p(a=1)}{p(a=0)}}{1+\frac{p(a=1)p(c=1)}{p(a=0)p(c=0)} } = \frac{LR(c)+LR(a)}{1+LR(a)LR(c)}
$$

Next we consider b: $$b =0$$ requires $$e = 0, d = 0$$ to be satisfied, so:

$$
p(b=0) = p(e=0,d=0) = p(e=0) p(d=0)
\tag{11}
$$

Similarly, $$b =1$$ requires $$e = 1, d = 1$$ to be satisfied, so:

$$
p(b=1) = p(e=1,d=1) = p(e=1) p(d=1)
\tag{12}
$$

Combining Equations (11) and (12) gives:

$$
LR(b) = \frac{p(b=1)}{p(b=0)} = \frac{p(e=1) p(d=1)}{p(e=0) p(d=0)} = LR(e)LR(d)
$$

### Computing LR(c)

Since the inverse of the encoding matrix of the polar code is the matrix itself, or as can also be seen from the figure, $$c = a+e$$, which is similar to $$a = c+e$$.
The messages that are passed are as shown in Figure 5.
![图5：Message passing of the BP algorithm for polar codes, computing c](/figure/极化码/bp/polar_bp_5.png)

*图5：Message passing of the BP algorithm for polar codes, computing c*

Therefore, we can completely derive LR(c) using the steps used to derive LR(a). The detailed derivation is not repeated here; the result is listed as follows:

$$
LR(c) = \frac{LR(a)+LR(e)}{1+LR(a)LR(e)}
$$

where:

$$
LR(e) = \frac{p(e=1)}{p(e=0)} = \frac{p(b=1)p(d=1)}{p(b=0)p(d=0)}  = LR(b)LR(d)
$$

### Computing LR(d)
Similarly, the formula for computing LR(d) can be derived following the way LR(b) is computed.
The messages that are passed are as shown in Figure 6.
![图6：Message passing of the BP algorithm for polar codes, computing d](/figure/极化码/bp/polar_bp_6.png)

*图6：Message passing of the BP algorithm for polar codes, computing d*

$$
LR(d) = \frac{p(d=1)}{p(d=0)} = \frac{p(e=1) p(b=1)}{p(e=0) p(b=0)} = LR(e)LR(b)
\tag{14}
$$

where:

$$
LR(e) = \frac{LR(a)+LR(c)}{1+LR(a)LR(c)}
\tag{13}
$$

### Using the log-likelihood ratio LLR 
For the formulas derived above for the various cases, in engineering practice the log-likelihood ratio Log Likelyhood Ratio is generally used, so we need to carry the several derivation results above further using the LLR.

Since the above formulas have only two forms, we only need to further derive Equations (13) and (14), and then the results can be generalized to the other formulas.

Let us first derive Equation (13).

The definition of the LLR is as follows, where $$X$$ is some random variable taking the value 0 or 1.

$$
LLR(X) = ln(LR(X))
\tag{15}
$$

Therefore

$$
LR(X) = e^{LLR(X)}
\tag{16}
$$

Using Equations (15) and (16), the LLR form of Equation (13) is:

$$
\begin{aligned}
		LLR(e) &= ln \left (\frac{LR(a)+LR(c)}{1+LR(a)LR(c)} \right ) \\ \\
		&= ln \left (\frac{e^{LLR(a)}+e^{LLR(c)}}{1+e^{LLR(a)}e^{LLR(c)}} \right ) \\ \\
		&= ln \left (\frac{e^{LLR(a)}+e^{LLR(c)}}{1+e^{LLR(a)+LLR(c)}} \right )  
	\end{aligned}
\tag{18}
$$

Here, for convenience of writing, we define a box-plus, $$\boxplus$$ operation , defined as:

$$
x \boxplus\ y = ln \left (  \frac{e^x + e^y}{ 1 + e^{x+y}}  \right )
\tag{17}
$$

Using Equation (17), Equation (18) can be written more compactly as:

$$
LLR(e) = LLR(a) \boxplus LLR(c)
$$

For Equation (13), then

$$
\text {LLR}(d) = \text {ln} (\text {LR}(e)\text {LR}(b)) = \text {ln} ( \text {LR}(e)) + \text {ln}(\text {LR}(b)) = \text {LLR}(e) + \text {LLR}(b)
$$

The discussion in the remainder of this topic is all based on the LLR form.


## Description of the BP decoding process of the N = 4 polar code

![图7：Message passing of the BP algorithm for polar codes, example with N=4](/figure/极化码/bp/polar_bp_7.png)

*图7：Message passing of the BP algorithm for polar codes, example with N=4*

Now, we use an N = 4 polar code to discuss the specific decoding process. This is shown in Figure 7..

From the figure we can see that we need to store the data of $$1+log_2 N$$ layers; in this example, 3 layers need to be stored.  The (2,3) inside the parentheses in the figure denotes the third data position of the second layer.  We use $$L(2,3)$$ to denote the message passed to the left at the 3rd data position of layer 2. Similarly, we use $$R(2,3)$$ to denote the message passed to the right at the 3rd data position of layer 2. What needs to be noted when programming is that, for example for the second layer, we can place the indices toward the right, as shown in Figure 8. In this article the indices are on the left, i.e., as shown in Figure 7.

![图8：Message passing of the BP algorithm for polar codes, example with N=4, indices all toward the right](/figure/极化码/bp/polar_bp_8.png)

*图8：Message passing of the BP algorithm for polar codes, example with N=4, indices all toward the right*


For the first layer (i.e., the leftmost layer), since it is the original data (some of which are frozen bits 0), we provide the initial rightward probability information. We initialize the probability information it provides: if it is a frozen bit, we initialize its LLR to infinity; if it is a data bit, we initialize it to 0.  Because if it is a frozen bit, then:

$$
\text {LLR}(f) = \text {ln} \left (   \frac{p(f=1)}{p(f=0)}  \right ) = \text {ln}(+\infty) = +\infty
$$

The infinity therein can be replaced in programming by a relatively large number, for example 27  (note that, since it is a value after taking ln, the value it actually represents is $$e^{27}$$).

For a data bit, we assume that its probabilities of taking 1 and 0 are equal, and therefore its LLR is 0.

Therefore:

$$
\text{L}(1,n) = \left\{\begin{matrix}
		27 &  \text{if it is a frozen bit}\\
		0 &    \text{if it is a data bit}
	\end{matrix}\right.
$$

For the third layer (i.e., the rightmost layer), since it is the received data, its log-likelihood ratios can be computed according to the channel and the modulation scheme; in this example, $$\text{R}(3,1),\text{R}(3,2),\text{R}(3,3),\text{R}(3,4)$$ are initialized.

All the other message probabilities R(x,x), L(x,x) are initialized to 0, indicating that there is no information at all that can give the probability of the corresponding bit taking the value 0 or 1, and therefore it is assumed that its probabilities of taking the value 0 or 1 are equal.


### Passing information from right to left
Going from right to left, the information of the third layer does not need to be computed, as it has already been computed according to the channel and modulation information.

Compute the probabilities of the second layer:

$$
\begin{aligned}
		\text{L}(2,1) &= \text{L}(3,1) \boxplus [\text{R}(2,3) + \text{L}(3,2) ]  \\
		\text{L}(2,2) &= \text{L}(3,3) \boxplus [\text{R}(2,4) + \text{L}(3,4) ]  \\
		\text{L}(2,3) &= \text{L}(3,2) +  [\text{R}(2,1) \boxplus \text{L}(3,1)]  \\
		\text{L}(2,4) &= \text{L}(3,4) + [\text{R}(2,2) \boxplus \text{L}(3,3)] 
	\end{aligned}
$$

Since the leftward messages of the first layer are never used, they do not need to be computed.

### Passing information from left to right
Going from left to right, the information of the first layer does not need to be computed, as it has already been computed according to the data type (data bits and frozen bits) information.

Compute the probabilities of the second layer:

$$
\begin{aligned}
		\text{R}(2,1) &= \text{R}(1,1) \boxplus [\text{R}(1,2) + \text{L}(2,2) ]  \\
		\text{R}(2,2) &= \text{R}(1,2)  + [\text{R}(1,1) \boxplus \text{L}(2,1)]   \\
		\text{R}(2,3) &= \text{R}(1,3)  \boxplus [\text{R}(1,4) + \text{L}(2,4) ] \\
		\text{R}(2,4) &= \text{R}(1,4) + [\text{R}(1,3) \boxplus \text{R}(2,3)] 
	\end{aligned}
$$

Since the rightward messages of the third layer are never used, they do not need to be computed.


## The same generator matrix, different arrangements

The preceding example took one arrangement as an example. Different arrangements will give different results when decoded with BP.

Below we take the encoding matrix without permutation as an example; the so-called one without permutation is $$F = \begin{pmatrix}   1&0 \\   1&1 \end{pmatrix}$$

Using the $$F$$ matrix, polar codes with more inputs can be realized recursively; for a polar code with code length $$N = 2^n$$, its generator matrix $$G_N$$ is:

$$
G_N = F^{\otimes n}
$$

where $$F^{\otimes n}$$ denotes the n-th Kronecker power of the matrix $$F$$.


For convenience of drawing the figures later on, we simplify the representation diagram of the polar code a little, redrawing the standard diagram on the left of Figure 9 as the simplified diagram on the right.

![图9：Simplified drawing for N = 2](/figure/极化码/bp/polar_bp_N2_simplify.png)

*图9：Simplified drawing for N = 2*

### Two drawing methods for N=4

For N=4, the generator matrix without permutation is (19):

$$
\begin{pmatrix}
		1 & 0 & 0 & 0\\
		1 & 1 & 0 & 0\\
		1 & 0 & 1 & 0\\
		1 & 1 & 1 & 1
	\end{pmatrix}
\tag{19}
$$

There can be several drawing methods here. With these drawing methods, if the SC (Successive Cancel) decoding algorithm is used, the results are all the same; but for the BP algorithm, there will be different results (different bit error rates).

**The first structure**, as shown in Figure 10: pay attention to the final output data, i.e., the output on the far right.
![图10：Standard drawing of the first non-permuted structure for N = 4](/figure/极化码/bp/polar_code_core_N_is_4_standard_drawing_2_1.png)

*图10：Standard drawing of the first non-permuted structure for N = 4*

The corresponding simplified algorithm is shown in Figure 11

![图11：Simplified drawing of the first non-permuted structure for N = 4](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_2_1.png)

*图11：Simplified drawing of the first non-permuted structure for N = 4*

When decoding with BP, the simplified drawing needs to be reversed, as shown in Figure 12
![图12：Simplified drawing of the first non-permuted structure for N = 4: decoding](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_2_1_reverse.png)

*图12：Simplified drawing of the first non-permuted structure for N = 4: decoding*

In fact, the polar code diagrams corresponding to Figure  10 and Figure 11 are both Figure 10. It is just that this simplified drawing makes the figure asymmetric, whereas in that standard drawing every small unit is left-right symmetric.

**The second structure**, as shown in Figure 13: pay attention to the final output data, i.e., the output on the far right.
![图13：Standard drawing of the second non-permuted structure for N = 4](/figure/极化码/bp/polar_code_core_N_is_4_standard_drawing_1_2.png)

*图13：Standard drawing of the second non-permuted structure for N = 4*

The corresponding simplified algorithm is shown in Figure 14

![图14：Simplified drawing of the second non-permuted structure for N = 4](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2.png)

*图14：Simplified drawing of the second non-permuted structure for N = 4*

When decoding with BP, the simplified drawing needs to be reversed, as shown in Figure 15
![图15：Simplified drawing of the second non-permuted structure for N = 4: decoding](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_reverse.png)

*图15：Simplified drawing of the second non-permuted structure for N = 4: decoding*

In fact, the polar code diagrams corresponding to Figure  13 and Figure 14 are both Figure 13. It is just that this simplified drawing makes the figure asymmetric, whereas in that standard drawing every small unit is left-right symmetric.

**As can be seen, for both structures, the outputs on the far right are the same.**


**Simplified drawing with encoding and decoding drawn together**

The simplified drawing of the second structure above, with encoding and decoding drawn together, is shown in Figure 16; from left to right is encoding, and from right to left is decoding.

![图16：Simplified drawing of the second non-permuted structure for N = 4: encoding and decoding](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_bidirection_together.png)

*图16：Simplified drawing of the second non-permuted structure for N = 4: encoding and decoding*

This combination of the forward and reverse directions can in fact be regarded as being caused by tilting the figure a little to the left or to the right, as in Figure 17, where the upper one is for encoding and the lower one is for decoding.

![图17：Deformation of the second non-permuted structure for N = 4 into the simplified drawing: encoding and decoding](/figure/极化码/bp/polar_code_core_N_is_4_simple_drawing_1_2_tilt_to_get_encode_dec.png)

*图17：Deformation of the second non-permuted structure for N = 4 into the simplified drawing: encoding and decoding*


### Multiple structures for N=8

Starting from here, the standard drawing is omitted and only the simplified drawing is shown; moreover, since what we are discussing is BP decoding, we always draw the simplified drawing as seen from the decoding point of view.


**The first structure**
As shown in Figure 18, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图18：The first structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_124.png)

*图18：The first structure for N = 8*


**The second structure**
As shown in Figure 19, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图19：The second structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_142.png)

*图19：The second structure for N = 8*


**The third structure**
As shown in Figure 20, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图20：The third structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_214.png)

*图20：The third structure for N = 8*

**The fourth structure**
As shown in Figure 21, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图21：The fourth structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_241.png)

*图21：The fourth structure for N = 8*


**The fifth structure**
As shown in Figure 22, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图22：The fifth structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_412.png)

*图22：The fifth structure for N = 8*

**The sixth structure**
As shown in Figure 23, the green numbers marked in the figure are shorthand for $$u_x$$, with the $$x$$ omitted.

![图23：The sixth structure for N = 8](/figure/极化码/bp/polar_code_core_N_is_8_simple_drawing_reverse_421.png)

*图23：The sixth structure for N = 8*

**Performance differences among the various structures**
Taking N = 8 as an example above, $$(\text{log}_2 N)! = (\text{log}_2 8)! = 3!=3*2*1=6$$ structures were given; performing BP decoding based on each of these structures yields different performance. 


### Other drawing methods
**Explicitly drawing the variable nodes and the check nodes**

![图24：N = 8 with the variable nodes and the check nodes drawn explicitly](/figure/极化码/bp/polar_code_core_N_is_8_variable_notes_check_notes.png)

*图24：N = 8 with the variable nodes and the check nodes drawn explicitly*


Figure 24 is taken from the reference:
A. Elkelesh, M. Ebada, S. Cammerer and S. ten Brink, "Belief Propagation List Decoding of Polar Codes," in IEEE Communications Letters, vol. 22, no. 8, pp. 1536-1539, Aug. 2018, doi: 10.1109/LCOMM.2018.2850772. 


**Normal graph / Forney factor graph**


![图25：N = 8 normal graph / Forney factor graph](/figure/极化码/bp/polar_code_core_N_is_8_Forney_Factor_Graph.png)

*图25：N = 8 normal graph / Forney factor graph*


Figure 25 is taken from the book 
<Channel Coding in 5G Mobile Communications> by Bai Baoming, Sun Shaohui, Wang Jiaqing