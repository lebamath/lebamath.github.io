---
layout: default
title: "The Real Frequencies Corresponding to the Discrete Fourier Transform"
lang: en
back_url: /index.html?lang=en
---

# The Real Frequencies Corresponding to the Discrete Fourier Transform

When we do signal processing or digital communications, we often use the discrete Fourier transform, and in the discrete Fourier transform what we see for each discrete point is only the subscript (which point it is, i.e., the index). For example, when we perform a discrete Fourier transform on 256 sampling points $$x_i,i=0,1,2,...,255$$ in the time domain, we only know the subscripts 0,1,2..., and so on; after transforming to the frequency domain, we likewise get 256 pieces of frequency-domain information, and using capital letters to denote the frequency-domain information, we get $$X_k,k=0,1,2,...,255$$. But sometimes we care about which real frequency the $$X_k$$ in the frequency domain corresponds to, that is, at how many hertz it is. This short article uses plain and easy-to-understand words to explain this matter.

We know that in the continuous Fourier transform there is real frequency information. Below is the formula of the continuous Fourier transform:

$$
X(f) = \int_{-\infty }^{+\infty} x(t) e^{-j2\pi ft}dt
$$

It is easy to see that the $$f$$ in the above formula is the actual frequency. For example, if we care about the information at the frequency 100Hz (Hz: hertz), then we substitute $$f=100$$ into the above expression:

$$
X(100Hz) = \int_{-\infty }^{+\infty} x(t) e^{-j2\pi 100t}dt
$$

Without loss of generality, suppose we have $$N$$ discrete time-domain signals. We know that the frequency-domain information can be computed with the following discrete Fourier transform formula:

$$
X(k) = \sum_{n=0}^{N-1}x(n) e^{ -j2\pi \frac{k}{N}n} \quad ----------------formula(1)
$$

We can easily guess that the $$2\pi\frac{k}{N}$$ in the above formula implies the frequency information, so how can the frequency information be obtained?

Here, it is first necessary to know at what sampling rate the discrete data points we have were collected. For example, if 1000 points are collected per second, then the sampling rate is 1000. We use $$f_s$$ to denote the sampling rate, meaning that $$f_s$$ samples are collected in one second.

Then the $$n$$ in formula (1) can be converted into a specific time, namely $$\frac{n}{f_s}$$, whose unit of time is the second, and it can be understood as the $$t$$ in the continuous Fourier transform formula.

Then, in formula (1), since $$n$$ has been divided by $$f_s$$, in order to keep the identity we can multiply $$2\pi\frac{k}{N}$$ by an $$f_s$$, so that $$2\pi\frac{k}{N}$$ becomes $$2\pi\frac{k f_s}{N}$$. Combining with the discussion above, formula (1) can be rewritten in the following form:

$$
X(k) = \sum_{n=0}^{N-1}x(n) e^{ -j2\pi \frac{k f_s}{N} \frac{n}{f_s}} \quad ----------------formula(2)
$$

Examining formula (2) carefully, in the expression

$$\frac{n}{f_s}$$ is equivalent to the time $$t$$ in the continuous Fourier transform

$$\frac{k f_s}{N}$$ is equivalent to the frequency $$f$$ in the continuous Fourier transform

The frequency $$f$$ in the continuous Fourier transform is continuous, whereas the frequency in the discrete Fourier transform is discrete, and two adjacent frequency points are respectively: $$\frac{k f_s}{N}$$ and $$\frac{(k+1) f_s}{N}$$. Therefore, in the discrete Fourier transform the smallest frequency spacing is $$\frac{f_s}{N}$$.



Here is an example: suppose the sampling rate is 25600 hertz and we continuously sample 256 data points to do the discrete Fourier transform; then, after transforming to the frequency domain, the smallest frequency spacing we can see is $$\frac{25600}{256}$$, i.e., 100 hertz. If the signal before sampling has a frequency of 150 hertz, then we can see that this 150 hertz does not fall on an integer multiple of 100 hertz, and after the discrete Fourier transform we see that there is signal at the neighbouring integer multiples of 100Hz around 150 hertz; then our frequency analysis after this sampling is not very accurate. In order to improve the precision of the frequency analysis, we need to sample more data continuously in time. If 512 data points are sampled, then the frequency spacing becomes $$\frac{25600}{512}$$, i.e., 50 hertz, and the frequency precision is doubled.



Supplement:

Although the sampling frequency is $$f_s$$ and $$k=0,1,2,...,N-1$$, this does not mean that the frequency range is from $$0\frac{fs}{N},1\frac{fs}{N},...,(N-1)\frac{fs}{N}$$. According to the Nyquist sampling theorem, the maximum frequency we can acquire is $$\frac{fs}{2}$$. So the frequency range that can be expressed is in fact:

$$
-(\frac{N}{2}-1)\frac{fs}{N},...,-2\frac{fs}{N},-1\frac{fs}{N},0\frac{fs}{N},1\frac{fs}{N},2\frac{fs}{N},...,\frac{N}{2}\frac{fs}{N}
$$

In addition, we can think about it from the perspective of the orthogonal basis of a linear space. These Fourier transforms are in fact transforms in an N-dimensional space. Their orthogonal basis (leaving aside for the moment the question of unit vectors, i.e., not considering whether it is an orthonormal basis, i.e., whether the length of the basis vectors is 1 -- we leave this aside for now; interested friends can watch one of my videos, which talks about ``the problem of energy conservation before and after the Fourier transform'') is:

$$
\begin{aligned}
	e^{ -j2\pi 0\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	e^{ -j2\pi 1\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	e^{ -j2\pi 2\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	e^{ -j2\pi 3\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	...\\
	e^{ -j2\pi (\frac{N}{2})\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	e^{ -j2\pi (\frac{N}{2}+1)\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
	... \\
	e^{ -j2\pi (N-1)\frac{ f_s}{N} \frac{n}{f_s}}  \quad  n=0,1,2,...,N-1  \\
\end{aligned}
$$

where:

$$
\begin{aligned}
	e^{ -j2\pi (\frac{N}{2}+1)\frac{ f_s}{N} \frac{n}{f_s}} 
	&= e^{ -j2\pi (\frac{N}{2}+1)\frac{ f_s}{N} \frac{n}{f_s}}  e^{ j2\pi N \frac{ f_s}{N} \frac{n}{f_s}}  \\
	&=e^{ -j2\pi (\frac{N}{2}+1-N)\frac{ f_s}{N} \frac{n}{f_s}} \\
	& = e^{ -j2\pi (-(\frac{N}{2}-1))\frac{ f_s}{N} \frac{n}{f_s}}
\end{aligned}
$$

So the frequency $$(\frac{N}{2}+1)\frac{ f_s}{N}$$ corresponds to the frequency $$(-(\frac{N}{2}-1))\frac{ f_s}{N}$$. Let us draw a figure to get an intuitive feel for why this is so.


![离散傅里叶变换对应的真实频率.png](/figure/通信基础/离散傅里叶变换对应的真实频率.png)

For the code please download it from [github](https://github.com/taichiorange/leba_math):[https://github.com/taichiorange/leba_math](https://github.com/taichiorange/leba_math) 