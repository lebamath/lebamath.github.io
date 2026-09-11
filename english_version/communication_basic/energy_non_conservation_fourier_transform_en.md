---
layout: default
title: "The Problem of Energy Non-Conservation in the Fourier Transform"
lang: en
back_url: /index.html?lang=en
---
# The Problem of Energy Non-Conservation in the Fourier Transform
In simulations of communication systems, we often need to add "Additive Gaussian White Noise" to the transmitted data according to a given signal-to-noise ratio (SNR: Signal Noise Ratio). However, the data before transmission is in many cases considered in terms of phases in the frequency domain; the given data to be transmitted has to be transformed to the time domain by the inverse Fourier transform, noise is added in the time domain, and the result is then sent to the receiver module for performance evaluation.

The problem is: whether in Matlab or in Python's numpy library, the fast Fourier transform and its inverse do not conserve energy before and after the transform, which creates a small difficulty for adding noise quantitatively. This article attempts to use simple QPSK as an example: the frequency domain is divided into 64 frequency bins, which are transformed to the time domain by ifft (inverse fast Fourier transform), and then the change in energy is analyzed quantitatively.

The frequency domain has 64 frequency bins. We send a signal on the second frequency bin, with amplitude 1 and phase $$\pi/4$$; this signal can be expressed as $$e^{j\pi/4}$$. Intuitively, the signal we send is:

$$
e^{j\frac{\pi}{4}}e^{j2\pi \frac{1}{64}n} = e^{j(2\pi\frac{1}{64}n+\frac{\pi}{4})}  \tag{1}
$$

This can be expanded as:

$$
e^{j(2\pi\frac{1}{64}n+\frac{\pi}{4})} = \text{cos}(2\pi\frac{1}{64}n+\frac{\pi}{4}) + j\text{sin}(2\pi\frac{1}{64}n+\frac{\pi}{4})
$$

It is easy to see that the maximum value of the real part is 1.  The power of the above signal is then the square of its modulus; it is easy to see that the squared modulus is 1, so its power is 1.

Let us look at the result after applying ifft. Among the 64 frequency bins, only the second frequency bin carries a signal, and the other frequency bins carry no signal, i.e., they are 0:

$$
0,e^{j\frac{\pi}{4}},0,0,\cdots,0
$$

64 in total.

The following python code is used to perform the ifft:

Please download the code from github: \url{https://github.com/taichiorange/leba_math}



The resulting plot is shown below:

![傅里叶变换能量不守恒问题-1.png](/figure/通信基础/傅里叶变换能量不守恒问题/傅里叶变换能量不守恒问题-1.png)

As can be seen, the maximum amplitude is not 1; the maximum value printed by the program is 0.015625 ! Why is that? Let us examine the ifft formula:

$$
x(n) = \frac{1}{N} \sum_{k=0}^{N-1} X(k) e^{j2\pi \frac{k}{N}n}
$$

Here, $$N$$ is the length of the inverse Fourier transform. In this example, N=64.

$$
x(n) = \frac{1}{64} \sum_{k=0}^{63} X(k) e^{j2\pi \frac{k}{64}n}
$$

In this example, since only $$X[1] = e^{j\frac{\pi}{4}}$$ is present and all the others are 0, the summation in the above formula degenerates to one in which only k=1 takes part in the computation:

$$
x(n) = \frac{1}{64}  X(1) e^{j2\pi \frac{1}{64}n}  \tag{2}
$$

Comparing Equation 2 with Equation 1, we can see that Equation 2 has an extra factor of $$\frac{1}{64}$$, and the figure also shows that the peak amplitude is $$\frac{1}{64} = 0.015625$$.

At this point in the discussion, the problem seems to be solved. However, looking more carefully, after the ifft the amplitude becomes 1/64 of what was originally expected; so is the energy the same before and after the transform? What we need is for it to stay the same.



Let us first look at it from the frequency domain. The total energy of the 64 frequency-domain signals is the sum of the energy on each frequency bin, i.e., $$\sum_{k=0}^{63} |X(k)|^2$$. This energy is over the time spanned by one fft, i.e., within 64 samples. Converting it into power, the power seen from the frequency domain is:

$$
\frac{\sum_{k=0}^{63} |X(k)|^2}{64}
$$

We analyze only one frequency bin, k=1. Seen from the frequency domain, its energy is 1; this is the energy of the frequency k=1 within the time spanned by one fft.

Now let us look at the ifft formula in matlab and in the python numpy library:

$$
x(n) = \frac{1}{64}  \sum_{k=0}^{63} X(k) e^{j2\pi \frac{1}{64}n}
=  \sum_{k=0}^{63} X(k) \frac{e^{j2\pi \frac{1}{64}n}}{64}
$$

Then, the waveform corresponding to k=1 is:

$$
\frac{e^{j2\pi \frac{1}{64}n}}{64}
$$

Then, let us compute its energy over 64 samples:

$$
\sum_{n=0}^{63} \left | \frac{e^{j2\pi \frac{1}{64}n}}{64} \right |^2 
= \sum_{n=0}^{63} \left | \frac{1}{64} \right |^2 = \frac{1}{64}
$$

As can be seen, the energy in the frequency domain is 1, while the energy in the time domain is $$\frac{1}{64}$$; the energies differ by a factor of 64!

Therefore, the ifft reduces the energy to 1/64 of its value before the transform. Similarly, it can be shown that the fft amplifies the energy to 64 times its value before the transform.

If we slightly modify the amplification or reduction factor in the formulas of the Fourier transform pair, we can guarantee that the energy stays the same before and after the transform, i.e., energy is conserved:

FFT transform:

$$
X(k) = \frac{1}{N} \sum_{n=0}^{N-1} x(n) e^{-j2\pi \frac{k}{N}n}   ====> 
X(k) = \frac{1}{\sqrt N} \sum_{n=0}^{N-1} x(n) e^{-j2\pi \frac{k}{N}n}
$$

IFFT transform

$$
x(n) = \frac{1}{N} \sum_{k=0}^{N-1} X(k) e^{j2\pi \frac{n}{N}k}   ====> 
x(n) = \frac{1}{\sqrt N} \sum_{k=0}^{N-1} X(k) e^{j2\pi \frac{n}{N}k}
$$

Using the modified formulas above guarantees that the energy stays the same before and after the transform.

Sometimes we care not only about the energy staying the same, but also about how large the energy of the signal is. For example, in order to add Gaussian white noise at a certain signal-to-noise ratio SNR, we need to know the power of the signal.

Note: the energy and power values discussed below are all based on the modified Fourier transform formulas, i.e., the ones that guarantee the energy stays the same before and after the transform.

Seen from the frequency domain, assume that the signal on one frequency bin has energy 1 (in general, e.g., for QPSK modulation, the energy is kept at 1). Since after the inverse Fourier transform it corresponds to the time of N samples, its power is: $$\frac{1}{N}$$.

Seen from the time domain, its corresponding waveform is:

$$
\frac{e^{-j2\pi \frac{k}{N}n}}{\sqrt N}
$$

Its energy is (note: to be consistent with the frequency domain, the summation must be taken over a time range of N samples):

$$
\sum_{n=0}^{N-1} \left |  \frac{e^{-j2\pi \frac{k}{N}n}}{\sqrt N}  \right |^2 = 1
$$

Its power is then  $$\frac{1}{N}$$.



So, if in the frequency domain only one frequency bin carries a signal, and in the frequency domain  $$|X(k)|^2 =1$$, i.e., it equals 1, then the signal power is  $$\frac{1}{N}$$, and the required noise power can be calculated from the SNR formula. If all N frequency bins carry signals, and in the frequency domain $$|X(k)|^2=1$$, i.e., it equals 1, then the power is 1.


To summarize: the core of this article is a reminder that, when using the FFT/IFFT functions in standard libraries, there is the problem that energy is not conserved before and after the transform.



## Further Reading:

Regard the Fourier transform and inverse transform as the coordinates of a vector in a linear space along the coordinate axes; the coordinate axes are the basis of the linear space. The requirements on these basis vectors are that, besides any two distinct basis vectors being orthogonal, each basis vector must also have length 1.

In the Fourier transform, $$\left \{\frac{e^{-j2\pi \frac{k}{N}n}}{\sqrt N},n=0,1,\cdots,N-1 \right \}$$ forms a valid basis vector, whereas $$\left \{ e^{-j2\pi \frac{k}{N}n}, n=0,1,\cdots,N-1 \right \}$$ is not a basis vector, because its length is $$\sqrt N$$.

![傅里叶变换能量不守恒问题-2.png](/figure/通信基础/傅里叶变换能量不守恒问题/傅里叶变换能量不守恒问题-2.png)
Let us look at each vector in the basis of the Fourier transform (listed in the correct way):

The first vector:

k=0

$$
\left [ 
\frac{e^{j2\pi \frac{0}{N}0}}{\sqrt N},
\frac{e^{j2\pi \frac{0}{N}1}}{\sqrt N},
\cdots,
\frac{e^{j2\pi \frac{0}{N}(N-1)}}{\sqrt N}
\right ]
$$

The second vector:

k=1

$$
\left [ 
\frac{e^{j2\pi \frac{1}{N}0}}{\sqrt N},
\frac{e^{j2\pi \frac{1}{N}1}}{\sqrt N},
\cdots,
\frac{e^{j2\pi \frac{1}{N}(N-1)}}{\sqrt N}
\right ]
$$

The third vector:

k=2

$$
\left [ 
\frac{e^{j2\pi \frac{2}{N}0}}{\sqrt N},
\frac{e^{j2\pi \frac{2}{N}1}}{\sqrt N},
\cdots,
\frac{e^{j2\pi \frac{2}{N}(N-1)}}{\sqrt N}
\right ]
$$

....



The (k+1)-th vector:

k=k

$$
\left [ 
\frac{e^{j2\pi \frac{k}{N}0}}{\sqrt N},
\frac{e^{j2\pi \frac{k}{N}1}}{\sqrt N},
\cdots,
\frac{e^{j2\pi \frac{k}{N}(N-1)}}{\sqrt N}
\right ]
$$

.....



The N-th vector:

k=N-1

$$
\left [ 
\frac{e^{j2\pi \frac{N-1}{N}0}}{\sqrt N},
\frac{e^{j2\pi \frac{N-1}{N}1}}{\sqrt N},
\cdots,
\frac{e^{j2\pi \frac{N-1}{N}(N-1)}}{\sqrt N}
\right ]
$$

Each basis vector has length 1:

$$
\left | \frac{e^{j2\pi \frac{k}{N}0}}{\sqrt N} \right |^2 + 
\left | \frac{e^{j2\pi \frac{k}{N}1}}{\sqrt N} \right |^2 +
\cdots
\left | \frac{e^{j2\pi \frac{k}{N}(N-1)}}{\sqrt N} \right |^2
=1
$$

If the Fourier transform formula in matlab is used, a vector is:

$$
\left [ 
e^{j2\pi \frac{N-1}{N}0},
e^{j2\pi \frac{N-1}{N}1},
\cdots,
e^{j2\pi \frac{N-1}{N}(N-1)}
\right ]
$$

Then its length is N:

$$
\left |  e^{j2\pi \frac{N-1}{N}0} \right |^2 +
\left |  e^{j2\pi \frac{N-1}{N}1} \right |^2 +
\cdots +
\left |  e^{j2\pi \frac{N-1}{N}(N-1)} \right |^2
=N
$$

This is therefore not a unit vector.