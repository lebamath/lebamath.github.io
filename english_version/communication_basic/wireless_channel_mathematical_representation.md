---
layout: default
title: "A Brief Analysis of the Mathematical Representation of the Wireless Channel"
lang: en
back_url: /english_version.html
---

# A Brief Analysis of the Mathematical Representation of the Wireless Channel
## A Typical Multipath Propagation Scenario 
A typical multipath propagation scenario

![multi-path.png](/figure/通信基础/无线信道的数学表示/multi-path.png)

Let $$X_{LP}$$ denote the original transmitted signal (i.e., the one that has not been shifted up to a high frequency; LP: Low Pass), which is called the low-pass signal or the baseband signal, and let $$X_{BP}$$ denote the signal that has been shifted to the high frequency (BP: Band Pass), which is called the bandpass signal. Then the relationship between the two is:

$$
X_{BP} = Re\{ X_{LP}(t)e^{j2\pi f_0 t}\} \quad --------(1)
$$

where $$f_0$$ is the high frequency, for example 1800MHz, while $$X_{LP}$$ may be just $$\pm 100MHz$$, and is centered at zero frequency.


## General Expression for the Baseband of a Multipath Signal

1) Each path has a different delay in time, and the delay is different at different instants, i.e., it is time-varying; it is denoted by $$\tau'_n(t)$$, which represents the delay of the n-th path at instant t

2) At different times the number of paths is also different, i.e., it is time-varying; it is denoted by N(t)

3) The amplitude gain and the phase offset on each path are also different, and they are time-varying. The amplitude gain coefficient is denoted by $$c_n(t)$$, which represents the gain coefficient of the n-th path at instant t and is a positive real number; the phase offset is denoted by $$e^{j\phi_n(t)}$$, which represents the phase offset of the n-th path at instant t, where $$\phi_n(t)$$ is the offset phase

Therefore, according to formula (1), the expression of the multipath bandpass signal in this case is:

$$
Y_{BP}(t) = Re\{ \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (t-\tau'_n(t))} \}
$$

where $$X_{LP}$$ is the original baseband signal sent out by the base station.



Then the received equivalent baseband signal can be computed from the expression above.

$$
Y_{BP}(t) = Re\{ \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (-\tau'_n(t))}  e^{j2\pi f_0 t} \}
$$

Then $$Y_{LP}$$ is the part on the left-hand side of the equals sign with $$e^{j2\pi f_0 t}$$ removed, that is:

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)}  X_{LP}(t-\tau'_n(t))  e^{j2\pi f_0 (-\tau'_n(t))}
$$

After rearranging we obtain:

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j(\phi_n(t)-2\pi f_0 \tau'_n(t))}  X_{LP}(t-\tau'_n(t))
$$

Separating the angles in the exponent of e:

$$
Y_{LP}(t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)} e^{j2\pi f_0 (-\tau'_n(t)) }  X_{LP}(t-\tau'_n(t))
$$

According to the formula above, the time-varying impulse response of the multipath channel can be obtained:

$$
h(\tau',t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j(\phi_n(t)-2\pi f_0 \tau'_n(t))}  \delta(\tau'-\tau'_n(t))\quad ----(2)
$$

The version of the formula with the angles separated:

$$
h(\tau',t) = \sum_{n=1}^{N(t)} c_n(t)  e^{j\phi_n(t)} e^{-j2\pi f_0 \tau'_n(t)}  \delta(\tau'-\tau'_n(t))\quad ----(2.1)
$$

The meaning of the expression above is: at instant t (which can be regarded as a constant, being the instant to be analyzed; the variable is $$\tau'$$), the signal that arrives at the receiver is the ``pulse transmitted at instant $$t-\tau'$$'' that ``has gone through a delay as long as $$\tau'$$''. Note that for the same delay $$\tau'$$ there may be several different paths. Therefore, in the formula above there is a summation and a $$\delta$$ function.

(Remark: those paths that are inseparable, or in other words those whose delays are very close, are combined together to form one independent, identifiable path, and there may be several such paths: the spacing and separation between the paths is fairly obvious, and each path is in turn composed of a great many sub-paths that have the same delay but different angles and attenuations, with the same or nearly the same delay, as shown in the example figure below)



![multi-path2.png](/figure/通信基础/无线信道的数学表示/multi-path2.png)


In the figure above there are three paths with different delays in total, and each path is in turn composed of several different sub-paths. Among the various sub-paths under each path, the delays are the same (the attenuation coefficient and the phase, i.e., the complex attenuation coefficient, may be different) or the delays differ slightly.

## A Reasonable Simplification of the Multipath Channel Transfer Function

Since formula (2) is time-varying, it is very hard to analyze, so a reasonable simplification is needed.

Assume that within a very short observation window, i.e., within a very short time window $$T_0$$, we can assume that in formula (2) the number of propagation paths N(t), the gain coefficients $$c_n(t)$$ and the phases $$\phi_n(t)$$ are all time-invariant, and at the same time that the angle of arrival $$\alpha_n(t)$$ of the channel on each path and the moving speed $$v(t)$$ of the terminal are also time-invariant. Using the above assumptions, and letting $$t\in [t_0,t_0+T_0]$$, formula (2) can be simplified to:

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(t))}  \delta(\tau'-\tau'_n(t))\quad ----(3)
$$

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\phi_n}e^{-j2\pi f_0 \tau'_n(t)}  \delta(\tau'-\tau'_n(t))\quad ----(3)
$$

Here $$\tau'_n(t)$$ is still time-varying: even if the moving speed v of the terminal is time-invariant, the terminal moving at a constant speed will still cause the delays of the various paths to change. In the following we derive the delay, deriving it into a form that does not depend on the parameter time t. We take one particular path as the entry point of the analysis; without loss of generality, let $$t_0=0$$, so that $$t\in [0,T_0]$$. If at instant 0 the delay of the n-th path is $$\tau'_n(0)$$, then after the terminal has moved for a time t:

the distance moved is vt, and the distance moved in the direction of the electromagnetic wave is $$vt cos(\alpha_n)$$; dividing it further by the speed of light gives the time that the electromagnetic wave needs to propagate over this moved distance

$$
\frac{vt*cos(\alpha_n)}{c_0}
$$

where $$c_0$$ is the speed of light. Combining the above analysis we have:

$$
\tau'_n(t) = \tau'_n(0) - \frac{vt cos(\alpha_n)}{c_0}\quad------(4)
$$

(What is easy to confuse here is: the time t during which the phone moves and the delay time $$\tau'_n(t)$$ are different)

In the expression above, $$vt cos(\alpha_n)$$ is the component, along the direction of the electromagnetic wave, of the distance that the phone has moved, as shown in the figure below:


![doppler.png](/figure/通信基础/无线信道的数学表示/doppler.png)




The wavelength is obtained by multiplying the period $$\frac{1}{f_0}$$ of the electromagnetic wave by the speed of light $$c_0$$, so the distance divided by the wavelength is the corresponding number of periods:

$$
\frac{vt cos(\alpha_n)}{\frac{1}{f_0}c_0} = \frac{f_0 vt cos(\alpha_n)}{c_0}
$$

Dividing the above number of periods further by the corresponding time t gives the frequency (i.e., the frequency offset, the Doppler frequency shift); the t in the above formula can be cancelled out, so that it becomes:

$$
\frac{f_0 v cos(\alpha_n)}{c_0}
$$

It is denoted as the Doppler frequency shift $$f_n$$ of the n-th path. The part without the multiplication by the angle is called the maximum Doppler frequency shift $$f_{max}$$:

$$
f_{max} = \frac{f_0 v}{c_0}
$$

Then: $$f_n=f_{max} cos(\alpha_n)$$

$$
\tau'_n(t) = \tau'_n(0) - \frac{vt cos(\alpha_n)}{c_0} = \tau'_n(0) - t \frac{f_n}{f_0}
$$

Substituting this $$\tau'_n(t)$$ into formula (3), and considering that t is very small and that $$f_n$$ is generally much smaller than $$f_0$$, the following approximation can be made:

$$
\delta(\tau'-\tau'_n(t_0) + t \frac{f_n}{f_0} ) \approx \delta(\tau'-\tau'_n(t_0))
$$

Since the $$\tau'_n(t)$$ in the exponent part of formula (3) has to act together with the very large $$f_0$$, no approximation can be made there.



Then

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(0)+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(4)
$$

Separating out the part related to the Doppler frequency shift, the above expression can be written as:

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-2\pi f_0 \tau'_n(0))} e^{j2\pi f_nt}  \delta(\tau'-\tau'_n(0)) \quad -----(4.1)
$$

In which the second term in the exponent:

$$
2\pi f_0 \tau'_n(0) = 2\pi \frac{c_0}{\lambda_0} \tau'_n(0) = \frac{2\pi}{\lambda_0} c_0 \tau'_n(0) = K_0 D_n
$$

In the above expression, $$k_0=\frac{2\pi}{\lambda_0}$$ denotes the free-space wave number, which is related only to the radio frequency;

$$D_n = c_0 \tau'_n(0)$$ is the propagation distance

It can be seen that this $$k_0 D_n$$ is relatively large

The $$2\pi f_n t$$ in formula (4) is the phase offset caused by the Doppler frequency shift

$$c_n$$ and $$\phi_n$$ are the result of the interaction between the transmitted signal and the scatterers.

Letting $$\phi'_n = k_0 D_n$$, formula (4) can be written as

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\phi_n-\phi'_n+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(5)
$$

## The Multipath Channel Model: Introducing Random Variables

It can be assumed fairly reasonably that the phase difference $$\phi_n$$ caused by the scatterers and the phase difference $$\phi'_n$$ caused by the propagation distance are mutually independent and uniformly distributed over $$[0,2\pi]$$. Since the complex exponential is a periodic function, $$\theta_n = \phi_n-\phi'_n$$ can be regarded as uniformly distributed over $$[0,2\pi]$$; although $$\beta=\phi_n-\phi'_n$$ is a restricted triangle over $$[0,3\pi]$$, if we consider that $$\beta \quad mod \quad 2\pi$$ is uniformly distributed over $$[0,2\pi]$$, then $$\theta_n = \phi_n-\phi'_n$$ can be regarded as uniformly distributed over $$[0,2\pi]$$.

Then formula (5) can be simplified to

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j(\theta_n+2\pi f_nt)}  \delta(\tau'-\tau'_n(0)) \quad -----(5)
$$

that is:

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0)) \quad -----(6)
$$

## Frequency-Domain Analysis of the Multipath Channel Transfer Function

In formula (6), regarding t as a fixed parameter, perform the Fourier transform with respect to $$\tau'$$:

$$
H(f',t) = \sum_{n=1}^Nc_n e^{j(2\pi f_nt + \theta_n)} e^{-j2\pi\tau'_n(0)f'} \quad ----------(7)
$$

In the above expression $$f'$$ is the frequency variable, i.e., the variable in the spectrum. Both $$f_n$$ and $$\tau'_n(0)$$ are parameters.

It can be seen that the term $$e^{-j2\pi \tau'_n(0) f'}$$ is inside the summation, so the value of $$|H(f',t)|$$ is related to $$f'$$; therefore, different frequencies have different attenuation coefficients, and this is a frequency-selective channel.

If we want it to be a frequency-nonselective channel, also called a flat fading channel, then $$e^{-j2\pi \tau'_n(0) f'}$$ has to be unrelated to the summation, i.e., unrelated to n. If the symbol length $$T_{sym}$$ is much larger than the maximum delay, i.e., $$max|\tau'_n - \tau'_m|<< T_{sym}$$, then for the different paths n, $$\tau'_n(0)$$ all take the same value. Letting $$\tau'_0 = \tau'_n(0)$$, formula (7) can then be written as:

$$
H(f',t) = [\sum_{n=1}^Nc_n e^{j(2\pi f_nt + \theta_n)}] e^{-j2\pi\tau'_0 f'} \quad ----------(8)
$$

From formula (8) it can be seen that for different frequencies $$f'$$, the attenuation coefficient $$|H(f',t)|$$ is the same (because the modulus of $$e^{-j2\pi\tau'_0 f'}$$ equals one). Therefore, this kind of channel is called a frequency-nonselective channel, or a flat fading channel.

Then for such a flat fading channel, its expression in the time domain, again letting $$\tau'_0 = \tau'_n(0)$$, can be derived from formula (6):

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n)}e^{ j2\pi f_n t}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

The expression above can be understood as follows: at a certain instant t, among all the multiple paths, only the one whose delay $$\tau'$$ equals $$\tau'_0$$ has a value, and all the others are 0. So this impulse response of formula (9) is in fact only single-valued.



We analyze this single-valued case, which is in fact analyzing the path marked with a certain color in the figure below:


![multi-path2.png](/figure/通信基础/无线信道的数学表示/multi-path2.png)

For example, analyzing $$\tau_1$$, formula (9) becomes:

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau_1) \quad -----(9.1)
$$

Assume the transmitted signal is $$x(t)$$; then convolving it with formula 9.1 gives the received signal (note that the variable in formula 9.1 is $$\tau'$$):

$$
\begin{aligned}
	y(t) &= \int_{-\infty}^{+\infty} h(\tau',t)x(t-\tau') d\tau'  \\
	&=\int_{-\infty}^{+\infty} [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau_1)x(t-\tau') d\tau'  \\
	&=[\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] x(t-\tau_1)  \\
	&= c_1 e^{j\theta_1} e^{j2\pi f_1 t} x(t-\tau_1)+ \cdots +c_n e^{j\theta_n} e^{j2\pi f_n t} x(t-\tau_1)
\end{aligned}
$$

The last line of the expression above is in fact multiplying the original signal x(t) by a complex attenuation $$c_n e^{j\theta_n}$$ respectively, then multiplying it by a frequency offset term $$e^{j2\pi f_n t}$$, and then accumulating the results.

Its manifestation in the frequency domain is that the spectrum of the original signal x(t) is shifted in frequency by the multiple frequency offset terms $$e^{j2\pi f_n t}$$; of course, after the shift it also has to be multiplied by a complex attenuation $$c_n e^{j\theta_n}$$.



Seen in the frequency domain, the frequency is spread out. For example, if x(t) is a single-frequency sine wave, then this single-frequency sine wave is spread out into multiple frequencies, and the frequency points are the frequency points obtained by shifting the original frequency by $$f_n$$ respectively. This can be understood as an impulse response in the frequency domain: performing a convolution in the frequency domain corresponds to a direct multiplication in the time domain.

## Simulation Method for a Flat Fading Channel

From formula (9) it can be seen that the transmitted signal goes through a delay $$\tau'_0$$ and is then multiplied by an attenuation coefficient:

$$\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$, denoted by $$\mu(t)$$, i.e., $$\mu(t) =\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$



When N tends to infinity,



$$\mu(t) =\underset{n \to \infty}{\lim} \sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}$$ 



is a complex-valued Gaussian random process (if t is a certain fixed instant, it is then called a Gaussian random variable).



It has zero mean, and its variance is

$$
2\sigma^2 = Var\{\mu(t)\} =\underset{n \to \infty}{\lim} \sum_{n=1}^N E(c_n^2)
$$

???????????? In this computation of the variance, why can the exponent part be disregarded?

## A Simple Method

The sampling rate is fs = 10000 = 10k. If there are N time-domain signals, the total number of time-domain signals of one simulation run, and if we want to transmit 1 second of data, then the amount of data is 10k.



According to formula 9 (copied down here):

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j(\theta_n)}e^{ j2\pi f_n t}] \delta(\tau' - \tau'_0) \quad -----(9)
$$

For a single-path rayleight channel, that is, a frequency-nonselective fading channel, the angles in formula (9) are equiprobably distributed, and the incident angles of the multiple scattering paths are also equiprobably distributed; it can then be implemented with the following matlab code:

matlab code: please download it from GitHub: \url{https://github.com/taichiorange/leba_math}


The number of time-domain sampling points is 10000, which is within the very short period of time that we want to consider.

If the rayleigh channel has 10 taps ($$h(\tau')$$ has 10 taps), then $$h(\tau')$$ has to be convolved with x(t).



If in addition there is no Doppler frequency shift, then:

$$
h(\tau') = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_i)
$$

$$\tau'_i$$ is the delay of the i-th path, and this path may be composed of $$N_i$$ inseparable sub-paths.

When $$N_i$$ is large enough:

$$
\begin{aligned}
	h(\tau') &= [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_i)  \\
	&= [\sum_{n=1}^N c_n cos(\theta_n) + \sum_{n=1}^N c_n sin(\theta_n)] \delta(\tau' - \tau'_i)
\end{aligned}
$$

According to the central limit theorem:

$$\sum_{n=1}^N c_n cos(\theta_n)$$ and $$\sum_{n=1}^N c_n sin(\theta_n)$$ are both Gaussian distributed.



If there is a Doppler effect, then:

$$
\begin{aligned}
	h(\tau',t) &= [\sum_{n=1}^N c_n e^{j(\theta_n + 2\pi f_n t)}] \delta(\tau' - \tau'_0) \\
	&=[c_1 e^{j \theta_1} e^{j2\pi f_1 t}+....+c_N e^{j \theta_N} e^{j2\pi f_N t}] \delta(\tau' - \tau'_0)
\end{aligned}
$$

The following is a summary, discussing four cases



within a very small time range, for example within the time of a few OFDM symbols

## A. Frequency-Nonselective Fading

That is, there are no discrete independent paths

\subparagraph{A.1 If there is no Doppler frequency shift} 

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_0)
$$

It can be seen that the right-hand side of the above formula is unrelated to the time t, so:

$$
h(\tau') = [\sum_{n=1}^N c_n e^{j\theta_n }] \delta(\tau' - \tau'_0)
$$

This is flat fading, so there is only one delay

The delta function guarantees that $$\tau'$$ has a value only at the position $$\tau'_0$$.



\subparagraph{A.2 If there is a Doppler frequency shift} 

In the time domain the Doppler frequency shift is related to t, so t cannot be omitted:

$$
h(\tau',t) = [\sum_{n=1}^N c_n e^{j\theta_n}e^{j 2\pi f_n t}] \delta(\tau' - \tau'_0)
$$

## B. Frequency-Selective Fading

That is, there are multiple discrete paths.

The analysis has to start from formula (6):

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0)) \quad -----(6)
$$

** B.1  If there is no Doppler frequency shift, but there are multiple discrete independent paths**, each discrete independent path being composed of multiple indistinguishable paths:



It can be seen that the right-hand side of the above formula is unrelated to the time t, so:

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau'_n(0))
$$

Assume there are two discrete independent paths, with delays $$\tau_1, \tau_2$$ respectively

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau_1)
$$

$$
h(\tau') = \sum_{n=1}^{N} c_n  e^{j\theta_n} \delta(\tau'-\tau_2)
$$

** B.2  If there is a Doppler frequency shift, and there are multiple discrete independent paths**

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau'_n(0))
$$

Assume there are two discrete independent paths, with delays $$\tau_1, \tau_2$$ respectively

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau_1)
$$

one independent discrete path constituted by all of the multiple inseparable paths whose delay is $$\tau_1$$

$$
h(\tau',t) = \sum_{n=1}^{N} c_n  e^{j\theta_n}e^{j2\pi f_n t}  \delta(\tau'-\tau_2)
$$

This is flat fading, so there is only one delay, namely $$\tau'_0$$; this $$\tau'_0$$ is the $$\tau_1$$ in the figure

The delta function guarantees that $$\tau'$$ has a value only at the position $$\tau'_0$$.