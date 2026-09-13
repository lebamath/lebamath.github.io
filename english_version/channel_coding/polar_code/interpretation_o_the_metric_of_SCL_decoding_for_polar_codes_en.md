---
layout: default
title: "Interpretation of the metric of SCL decoding for polar codes"
lang: en
back_url: /index.html?lang=en
---

# Interpretation of the metric of SCL decoding for polar codes
## Interpretation of the metric in SCL decoding

$$
PM_l^{[i]} \overset{\triangle}{=}  \sum_{j=1}^{i} ln(1+exp(-(1-2 \hat{u}_j[l]) L_N^{(j)}) \quad ----\quad (1)
$$

where $$L_N^{(j)}$$ is the log-likelihood ratio, denoted by $$LLR (W_N^j)$$, and defined as follows:

$$
L_N^{(j)} = ln \frac
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=0)}
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=1)}  = LLR (W_N^j)
$$

This is the log-likelihood ratio. Let us now remove the logarithm and introduce the likelihood ratio for the analysis; let:

$$
LR (W_N^j) =  \frac
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=0)}
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=1)}
$$

where
LLR : Log Likelihood Ratio
LR:  Likelihood Ratio

Then:

$$
LLR (W_N^j) = ln (LR (W_N^j))
$$

Then formula (1) can be written as:

$$
PM_l^{[i]} \overset{\triangle}{=}  \sum_{j=1}^{i} ln(1+LR (W_N^j)^{ (-(1-2 \hat{u}_j[l]))}) \quad ----\quad (2)
$$

Let us analyze the various cases:

$$
if LR (W_N^j) > 1, \text{then imply that } u_j = 0 
\begin{cases}
	\text{decision  }\hat u_j = 0,  -(1-2 \hat{u}_j)=-1,   LR (W_N^j)^{-1} <1 \\
	\text{decision  }\hat u_j = 1,  -(1-2 \hat{u}_j)=+1,   LR (W_N^j)^{+1} >1 \\
\end{cases}
$$

$$
if LR (W_N^j) < 1, \text{then imply that } u_j = 1 
\begin{cases}
	\text{decision  }\hat u_j = 0,  -(1-2 \hat{u}_j)=-1,   LR (W_N^j)^{-1} >1 \\
	\text{decision  }\hat u_j = 1,  -(1-2 \hat{u}_j)=+1,   LR (W_N^j)^{+1} <1 \\
\end{cases}
$$

If the hint (imply) given by the likelihood ratio data agrees with our decision, then the value added inside the ln in formula (2) is less than 1,
if the hint (imply) given by the likelihood ratio data is opposite to our decision, then the value added inside the ln in formula (2) is greater than 1

In this way, in the case where the decision agrees, the metric value increases by less, and in the case where the decision disagrees, the metric value increases by more; finally, we select the decoding path according to the criterion of the smallest metric value, which can then maximize the possibility that the decision agrees.

There is an approximation in the article: when the decision agrees, since what is added inside the ln is a number less than 1, that ln value is then close to 0, so in this case the metric value neither increases nor decreases;
whereas when the decision disagrees, since what is added inside the ln is a number greater than 1, the following approximation is used here:

$$
ln(1+LR (W_N^j)^{ (-(1-2 \hat{u}_j[l]))})  \approx |ln(LR (W_N^j))| = |LLR(W_N^j)| > 0
$$

The greater than 1 above is because, when the decision disagrees, LR > 1, and then LLR > 0.

If it is a frozen bit and the path taken is ``the decision is 1'', then the metric becomes  $$+\infty$$

![Approximate computation of the metric in SCL decoding](/figure/极化码/SCL译码的度量近似计算.png)

*Approximate computation of the metric in SCL decoding*

## Exact interpretation of the metric in SCL decoding

$$
\begin{aligned}
	\frac{P(u_1^{i-1}|y_1^N)}
	{P(u_1^{i}|y_1^N)}
	&= \frac{P(u_1^{i-1},y_1^N)}      {P(u_1^{i},y_1^N)}  \\ 
	\quad   \\
	&= \frac{P(u_1^{i-1},y_1^N|u_i=0)P(u_i=0)  + P(u_1^{i-1},y_1^N|u_i=1)P(u_i=1) }    
	{P(u_1^{i-1},y_1^N|u_i)P(u_i)}  \\
	\quad  \\
	&= \frac{P(u_1^{i-1},y_1^N|u_i=0)  + P(u_1^{i-1},y_1^N|u_i=1) }    
	{P(u_1^{i-1},y_1^N|u_i)}  \\
	\quad  \\
	&= 1 + [ \frac {P(u_1^{i-1},y_1^N|u_i=0)}  {P(u_1^{i-1},y_1^N|u_i=1)}]^{-(1-2u_i)}
\end{aligned}
$$

where it is assumed that $$u_i=0$$ and $$u_i=1$$ occur with equal probability.

$$
\begin{aligned}
	\frac{P(u_1|y_1^N)}      {P(u_1^{i}|y_1^N)}  &= 
	\frac{P(u_1|y_1^N)}      {P(u_1^{2}|y_1^N)}
	\frac{P(u_1^2|y_1^N)}      {P(u_1^{3}|y_1^N)}
	...
	\frac{P(u_1^{i-1}|y_1^N)}      {P(u_1^{i}|y_1^N)}
	&= \prod_{j=2}^{i}\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
\end{aligned}
$$

Then:

$$
\frac{1}      {P(u_1^{i}|y_1^N)} = \frac{1}{P(u_1|y_1^N)}   \prod_{j=2}^{i}\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$

Furthermore:

$$
\frac{1}{P(u_1|y_1^N)} = \frac{1}{P(u_1,y_1^N)/P(y_1^N)} = \frac{P(y_1^N)}{P(u_1,y_1^N)} = 1 + [ \frac{P(y_1^N|u_1=0)}{P(y_1^N|u_1=1)}] ^{-(1-2u_1)}
$$

Then:

$$
\frac{1}      {P(u_1^{i}|y_1^N)} =    \prod_{j=1}^{i}\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$

where $$U_1^0$$ amounts to no term at all.



After taking the logarithm:

$$
ln\frac{1}      {P(u_1^{i}|y_1^N)} =    \sum_{j=1}^{i}ln\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$