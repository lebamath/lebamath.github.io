---
layout: default
title: "Notes on Single-Sided and Double-Sided Noise"
lang: en
back_url: /english_version.html
---

# Notes on Single-Sided and Double-Sided Noise
When we run communication simulations, we basically always need to add white Gaussian noise into the system, and as far as the definition of noise power is concerned, we often hear of two kinds: double-sided white noise and single-sided white noise. So where do these two connect, and where do they differ?

We know that for a real signal its Fourier transform is double-sided, i.e. it exists at both positive and negative frequencies and is conjugate symmetric; for a complex signal the Fourier transform can be made single-sided, i.e. only positive frequencies are present. Of course, a complex signal may also have content at negative frequencies, but in that case the positive and negative frequencies are not guaranteed to be symmetric.

From the communication point of view, the signals we transmit over the air are all real signals, and the signals received by the antenna are real signals as well, so the corresponding power spectrum is conjugate symmetric between positive and negative frequencies. White noise, on the other hand, distributes its energy uniformly over all frequencies, so when computing the energy of white noise we have to count the noise within the bandwidth at the positive frequencies as well as the noise within the bandwidth at the negative frequencies. In this case, therefore, we define the noise power as
$$
\frac{N_0}{2}
$$
Suppose the bandwidth is B. Then there is a working band of bandwidth B at the positive frequencies and another working band of bandwidth B at the negative frequencies, so the total noise within these two bands is:
$$
\frac{N_0}{2}B + \frac{N_0}{2}B = \frac{N_0}{2} \times 2B = N_0 B \tag{1}
$$
Since the negative frequencies of a real signal are conjugate symmetric with the positive frequencies, they do not carry any additional information. So we convert the real signal into a complex signal, i.e. keep only the positive-frequency part of the real signal. Assuming that no signal energy is lost in this conversion, we must not let the noise energy increase or decrease either, otherwise the signal-to-noise ratio would be wrong. After the conversion to a complex signal (in other words, keeping only the positive-frequency part), the bandwidth is only B, and the noise power now has to be defined as $$N_0$$, so that the total noise is:
$$
N_0 B   \tag{2}
$$
Only in this way are the noise powers in equations (1) and (2) equal.
The illustrations are given below:
![Double-sided signal and noise](/figure/通信基础/双边带信号和噪声.png)

*Double-sided signal and noise*
Converted to single-sided:
![Single-sided signal and noise](/figure/通信基础/单边带信号和噪声.png)

*Single-sided signal and noise*
