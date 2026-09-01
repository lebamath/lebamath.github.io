---
layout: default
title: "Channel Capacity under Modulation"
lang: en
back_url: /english_version.html
---

## Channel Capacity under Modulation
In the channel capacity formula below, the premise is that no constraint is imposed on the input; when this capacity is achieved, the input follows a continuous Gaussian distribution:
$$
C = log(1+\frac{S}{N})  \tag{1}
$$
However, in practical systems, what we use is always a discrete input, i.e., discrete data after modulation. In that case, what is the upper limit of the channel capacity? Under different SNR conditions, what is the upper limit of the channel capacity?
Starting from the most fundamental formula of channel capacity, and assuming that the modulated data at the input is equiprobably distributed, the channel capacity is:
$$
I(X;Y)  \tag{2}
$$
Based on basic information theory, further deriving Eq. (2) gives:
$$
I(X;Y) = H(X) - H(X|Y)  \tag{3}
$$
where H(X) is easy to compute:
$$
H(X) = \sum_{i=1}^M p(x) log_2\frac{1}{p(x)} = \sum_{i=1}^M \frac{1}{M} log_2 M = log_2 M \tag{4}
$$
Therefore, the key is to compute the conditional entropy H(X|Y). Based on basic information theory:
$$
H(X|Y) = \int p(Y=y) H(X|Y=y) dy  \tag{5}
$$
The integral in Eq. (5) can be evaluated with the Monte Carlo method, so the key is to compute the probability H(X|Y=y).
$$
H(X|Y=y) = \sum_x p(X=x|Y=y) log \frac{1}{p(X=x|Y=y) } \tag{6}
$$
In Eq. (6):
$$
p(X=x|Y=y) = p(x|y) = \frac{p(y|x)p(x)}{p(y)}=p(y|x)\frac{p(x)}{p(y)} \tag{7}
$$
Since x is equiprobably distributed, the term $$\frac{p(x)}{p(y)}$$ in Eq. (7) is independent of the specific value of x, therefore
$$
p(x|y) \propto  p(y|x) \tag{8}
$$
So we can compute p(y|x) for every x, and after normalization we obtain the probability p(x|y).
![Channel capacity curves under modulation, PSK](/figure/information_theory/调制模式下信道容量曲线PSK.png)

*Channel capacity curves under modulation, PSK*

Code: on GitHub. \url{https://github.com/taichiorange/leba_math.git}
### Monte Carlo Algorithm
Applying the Monte Carlo method to Eq. (5), we can generalize the integral in Eq. (5), i.e., compute the expectation of a function f(y):
$$
\int p(y) f(y) dy  \tag{9}
$$
where $$p(y)$$ is the probability density of the random variable Y.
We need to generate enough data according to the probability density function $$p(y)$$; suppose there are N samples in total. From probability theory, we know that the larger $$p(y)$$ is, the more often the corresponding $$y$$ appears. Suppose:
$$
\begin{aligned}
	y_1 :\ & n_1 \\
	y_2 :\ & n_2 \\
	\cdots \\
	y_i :\ & n_i \\
	\cdots
\end{aligned}
$$
where $$n_i$$ denotes the number of occurrences, then:
$$
\frac{\sum_i n_i  f(y_i)}{N} = \sum_i \frac{n_i  f(y_i)}{N} = \sum_i \frac{n_i}{N} f(y_i) = \sum_i p(y_i) f(y_i)
\tag{10}
$$
The average of the samples, i.e., the sample mean, is used in place of the expectation. The result computed by Eq. (10) is used in place of Eq. (9).
More generally, for the Monte Carlo method, consider the following definite integral:
$$
\int_a^b f(x) dx   \tag{11}
$$
We convert Eq. (11) into an expectation:
$$
\int_a^b f(x) dx = \int_a^b \frac{1}{b-a}f(x)(b-a) =  \int_a^b \frac{1}{b-a} g(x)   \tag{12}
$$
where $$g(x) = (b-a)f(x)$$. Then Eq. (12) can be regarded as a random variable $$X$$ taking values in $$[a,b]$$ and following a uniform distribution, i.e., its probability density function is:
$$
p(x) = \frac{1}{b-a}
$$
Using the Monte Carlo method, generate enough uniformly distributed random data in $$[a,b]$$, substitute them into $$g(x)$$, and then take the average.
