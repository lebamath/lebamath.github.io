---
layout: default
title: "极化码SCL译码的度量解释"
back_url: /index.html?lang=zh
---
# 极化码SCL译码的度量解释

录制的视频在 [B站](https://www.bilibili.com/cheese/play/ep468349)

## SCL译码的度量解释

$$
PM_l^{[i]} \overset{\triangle}{=}  \sum_{j=1}^{i} ln(1+exp(-(1-2 \hat{u}_j[l]) L_N^{(j)}) \quad ----\quad (1)
$$

其中 $$L_N^{(j)}$$  是对数似然比，记为：$$LLR (W_N^j)$$, 定义如下：

$$
L_N^{(j)} = ln \frac
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=0)}
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=1)}  = LLR (W_N^j)
$$

这是对数似然比。我们再把对数去掉，引入似然比来分析，令：

$$
LR (W_N^j) =  \frac
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=0)}
{W_N^{(j)}(y_1^N,\hat u_1^{j-1}|\hat u_j=1)}
$$

其中
LLR : Log Likelihood Ratio
LR:  Likelihood Ratio

则：

$$
LLR (W_N^j) = ln (LR (W_N^j))
$$

则公式 (1) 可以写成：

$$
PM_l^{[i]} \overset{\triangle}{=}  \sum_{j=1}^{i} ln(1+LR (W_N^j)^{ (-(1-2 \hat{u}_j[l]))}) \quad ----\quad (2)
$$

我们来分析一下各种情况：

$$
if LR (W_N^j) > 1, \text{then imply that } u_j = 0 
\begin{cases}
\text{判决  }\hat u_j = 0,  -(1-2 \hat{u}_j)=-1,   LR (W_N^j)^{-1} <1 \\
\text{判决  }\hat u_j = 1,  -(1-2 \hat{u}_j)=+1,   LR (W_N^j)^{+1} >1 \\
\end{cases}
$$

$$
if LR (W_N^j) < 1, \text{then imply that } u_j = 1 
\begin{cases}
\text{判决  }\hat u_j = 0,  -(1-2 \hat{u}_j)=-1,   LR (W_N^j)^{-1} >1 \\
\text{判决  }\hat u_j = 1,  -(1-2 \hat{u}_j)=+1,   LR (W_N^j)^{+1} <1 \\
\end{cases}
$$

如果似然比的数据给的暗示(imply) 与我们判决一致，则在公式 (2) 中 ln 里面加的数值就小于1，
如果似然比的数据给的暗示(imply) 与我们判决相反，则在公式 (2) 中 ln 里面加的数值就大于1

这样，在判断一致的情况下，度量值是增加得更少，判断不一致得情况下，度量值增加得更多，最后我们按照度量值最小的准则来选择译码路径，则可以让判断一致的可能性最大。

文章中有个近似，当判断一致时，由于在 ln 中增加的是一个小于 1 的数，所以，那个 ln 值就接近与 0，所以，这种情况下，度量值就不增不减；
而当判断不一致时，由于在 ln 中增加的是一个大于 1 的数，这里用了下面这个近似：

$$
ln(1+LR (W_N^j)^{ (-(1-2 \hat{u}_j[l]))})  \approx |ln(LR (W_N^j))| = |LLR(W_N^j)| > 0
$$

上面的大于1，是因为判断不一致时，LR > 1, 则 LLR > 0.

如果是冻结比特，走的路径又是“判决是1”，则度量变为  $$+\infty$$

![SCL译码的度量近似计算](/figure/极化码/SCL译码的度量近似计算.png)

*SCL译码的度量近似计算*

## SCL译码的度量的精确解释

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

其中假定 $$u_i=0$$ 与 $$u_i=1$$ 是等概率出现的。

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

则：

$$
\frac{1}      {P(u_1^{i}|y_1^N)} = \frac{1}{P(u_1|y_1^N)}   \prod_{j=2}^{i}\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$

再者：

$$
\frac{1}{P(u_1|y_1^N)} = \frac{1}{P(u_1,y_1^N)/P(y_1^N)} = \frac{P(y_1^N)}{P(u_1,y_1^N)} = 1 + [ \frac{P(y_1^N|u_1=0)}{P(y_1^N|u_1=1)}] ^{-(1-2u_1)}
$$

则：

$$
\frac{1}      {P(u_1^{i}|y_1^N)} =    \prod_{j=1}^{i}\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$

其中$$U_1^0$$ 相当于没有任何项。



取对数后：

$$
ln\frac{1}      {P(u_1^{i}|y_1^N)} =    \sum_{j=1}^{i}ln\{ 1 + [ \frac{P(y_1^N,u_1^{j-1}|u_j=0)}{P(y_1^N,u_1^{j-1}|u_j=1)}] ^{-(1-2u_j)}\}
$$

## 代码讲解

![讲解代码的编码图](/figure/极化码/讲解代码的编码图.png)

*讲解代码的编码图*


![讲解代码的变量图](/figure/极化码/讲解代码的变量图.png)

*讲解代码的变量图*

![SCL代码讲解用](/figure/极化码/SCL代码讲解用.png)

*SCL代码讲解用*