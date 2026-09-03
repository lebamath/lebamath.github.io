---
layout: default
title: "Several Easily Confused Formulas for Channel Capacity"
lang: en
back_url: /english_version.html
---


## BER Analysis over the AWGN Channel - BPSK
ref link: https://www.gaussianwaves.com/2012/07/intuitive-derivation-of-performance-of-an-optimum-bpsk-receiver-in-awgn-channel/

The channel is an AWGN channel with no fading, which can be expressed as:   r = x + w

where x is BPSK modulated, and w is additive white Gaussian noise, following a Gaussian distribution with mean 0 and variance $$\frac{N_0}{2}$$.

Since x is BPSK modulated, we assume its signal energy is Es (since it is BPSK, Eb = Es).

![BPSK Symbol Mapping Diagram](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-BPSK_ideal_constellation.png)

*BPSK Symbol Mapping Diagram*

When the transmitted bit is 0, then $$S_0 = -\sqrt{E_s}$$. Since w follows a Gaussian distribution with mean 0 and variance $$\frac{N_0}{2}$$, r follows a Gaussian distribution with mean $$-\sqrt{E_s}$$ and variance $$\frac{N_0}{2}$$.

When the transmitted bit is 1, then $$S_1 = +\sqrt{E_s}$$. Since w follows a Gaussian distribution with mean 0 and variance $$\frac{N_0}{2}$$, r follows a Gaussian distribution with mean $$+\sqrt{E_s}$$ and variance $$\frac{N_0}{2}$$.

Expressed as mathematical formulas:
$$
p(r|0_T) = \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(-\sqrt{E_s}))^2}{N_0}}   \tag{1A}
$$

$$
p(r|1_T) = \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(+\sqrt{E_s}))^2}{N_0}}   \tag{1B}
$$

Then, for making the optimal decision, as shown in the figure below:

![BPSK Optimal Decision](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-PDF_of_BPSK_symbols.png)

*BPSK Optimal Decision*

Then a bit error occurs in the following cases:

* 0 is sent, but it is decided as 1
* 1 is sent, but it is decided as 0

Expressed as a mathematical formula:
$$
P(error ) = P(\text{decided as 1, sent 0}) +  P(\text{decided as 0, sent 1})  \tag{2}
$$
that is:
$$
P(e) = P(1_D, 0_T) + P(0_D,1_T)  \tag{3}
$$
where D denotes Decided, i.e. what is decided; T denotes transmit, i.e. what is sent.

Using Bayes' theorem, formula (3) can be expressed as:
$$
P(e) = P(1_D| 0_T)P(0_T) + P(0_D|1_T)P(1_T)  \tag{4}
$$
From the illustration below, we can know the mathematical expressions of $$P(1_D| 0_T)$$ and $$P(0_D|1_T)$$, which actually represent the probability of the red region in the figure below.

![Region Where a Decision Error Occurs](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-Calculating-Error-Probability.png)

*Region Where a Decision Error Occurs*

Then:
$$
P(1_D| 0_T) = \int_0^{+\infty} p(r|0_T) dr   \tag{5A}
$$
and:
$$
P(1_D| 0_T) = \int_{+\infty}^0 p(r|1_T) dr   \tag{5B}
$$
Substituting formula (1A) into (5A) and formula (1B) into (5B):
$$
P(1_D| 0_T) = \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r-(-\sqrt{E_s}))^2}{N_0}} dr 
= \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr 
\tag{6A}
$$
and
$$
P(1_D| 0_T) = \int_{+\infty}^0 \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-(+\sqrt{E_s}))^2}{N_0}}  dr  
= \int_{+\infty}^0 \frac{1}{\sqrt{\pi N_0}} e^{-\frac{(r-\sqrt{E_s})^2}{N_0}}  dr
\tag{6B}
$$
Since these two distributions have symmetry, formula (4) can be derived as (substituting formulas 6A and 6B into formula (4), using the symmetry, and given that the transmitted symbols are equiprobably distributed):
$$
\begin{aligned}
	P(e) &= \frac{1}{2}P(1_D| 0_T) + \frac{1}{2}P(0_D|1_T)\\  \\
	&= P(1_D| 0_T)\\  \\
	&=  \int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr 
\end{aligned}
\tag{7}
$$
We continue analyzing the integral formula in formula (7) by performing a change of the integration variable, letting:
$$
t = \frac{r+\sqrt{E_s}}{\sqrt{N_0/2}}
$$
then the integration limits become:
$$
r=0, t =\frac{\sqrt{E_s}}{\sqrt{N_0/2}} = \sqrt{\frac{E_s}{N_0/2}}  \\
r =+\infty, t=+\infty,
$$
then the integral in formula (7) becomes:
$$
\int_0^{+\infty}  \frac{1}{\sqrt{\pi N_0}}  e^{-\frac{(r+\sqrt{E_s})^2}{N_0}} dr
=\int_{\sqrt{\frac{E_s}{N_0/2}} }^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{8}
$$
This is exactly the form of the Q function derived here: for a standard normal distribution with mean 0 and variance 1, computing the probability from x all the way to infinity. This is a function of x, i.e. Q(x), defined as follows:
$$
Q(x) =\int_x^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{9}
$$


Substituting formulas (8) and (9) step by step back into (7):
$$
P(e) = Q\left (\sqrt{\frac{E_s}{N_0/2}} \right )
$$


![Standard Normal Distribution Plot Qx](/figure/awgn/AWGN信道下的误码率分析-BPSK-SISO-标准正太分布图Qx.png)

*Standard Normal Distribution Plot Qx*



Python code that plots the figure above

Code on github:  \url{https://github.com/taichiorange/leba_math.git}


## BER Analysis under Rayleigh Fading - BPSK
This article analyzes the bit error rate under a Rayleigh fading channel, deriving the bit error rate for the Rayleigh fading case based on the bit error rate of the AWGN channel.
$$
y = hx + w  \tag{1}
$$
The transmitted symbol x is BPSK modulated, and h is a complex Gaussian random variable, where each dimension (real part and imaginary part) satisfies a Gaussian distribution with mean 0 and variance 1/2. Then the squared magnitude of h follows a Rayleigh distribution:
$$
p(z) = \frac{z}{\delta^2} e^{-\frac{z^2}{2\delta^2}} = 2z e^{-z^2}   \tag{2}
$$
If the signal energy is $$E_s$$, the signal-to-noise ratio without considering h is:
$$
SNR = \frac{Es}{N_0} = \mu  \tag{3}
$$
After considering h, the signal-to-noise ratio becomes
$$
\frac{|h|^2 E_s}{N_0} = a^2 \mu  \tag{4}
$$
where $$a = |h|$$. According to the bit error rate formula for BPSK modulation over an additive white Gaussian noise channel:
$$
BER = Q(\sqrt {SNR}) = Q(\sqrt{a^2 u}) \tag{5}
$$
Since h itself is also a random variable, we compute the average bit error rate under formula (1) according to the probability distribution of h:
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da \tag{6}
$$
Substituting (2) into (6):
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da = \int_0^{+\infty} Q(\sqrt{a^2 u}) 2a e^{-a^2} da \tag{7}
$$
Substituting the Q function into (7) as well:
$$
\int_0^{+\infty} 
\{ \int_{\sqrt{a^2u}}^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{t^2}{2}} dt   \} 
2a e^{-a^2} da \tag{8}
$$
Performing a change of the integration variable on the inner integral of formula (8), letting: 
$$
y = \frac{t}{\sqrt{a^2 \mu}}
$$
then formula (8) becomes (the second equal sign is an interchange of the order of integration):
$$
\begin{aligned}
	\int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} 2a e^{-a^2} da 
	&= \int_0^{+\infty} \int_1^{+\infty}  \frac{2a^2 \sqrt{\mu} }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + 2)}{2}}  dy  da
	\\  \\
	&= \int_1^{+\infty} \sqrt{\mu} \left \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + 2)}{2}} da \right \} dy 
	\\  \\
\end{aligned}
\tag{9}
$$
In the innermost integral of formula (9), let 
$$
sigma ^2 = \frac{1}{\mu y^2 + 2}   \tag{10}
$$
then formula (9) becomes:
$$
\int_1^{+\infty} \sqrt{\mu} \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
e^{-\frac{a^2 }{2 \sigma^2}} da \} dy  = \int_1^{+\infty} \sqrt{\mu} \sigma \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi} \sigma}    
e^{-\frac{a^2 }{2 \sigma^2}} da \} dy  \tag{11}
$$
The inner integral in formula (11) is exactly the variance of a Gaussian distribution with mean 0 and variance $$\sigma^2$$, so formula (10) is derived as:
$$
\int_1^{+\infty} \sqrt{\mu} \sigma \sigma^2 dy = \int_1^{+\infty} \sqrt{\mu} \sigma^3 dy  \tag{12}
$$
Substituting (10) into (12):
$$
\int_1^{+\infty} \sqrt{\mu} \frac{1}{(\mu y^2 + 2)^{\frac{3}{2}}} dy  \tag{13}
$$
Performing another change of variable on the integral in formula (13), letting:
$$
y = \sqrt{\frac{2}{\mu}} tan\theta   \tag{14}
$$
then when:
$$
\begin{aligned}
	&y=1,   &\theta = tan^{-1}\sqrt{\frac{\mu}{2}}    \\
	&y=+\infty, & \theta = \frac{\pi}{2} 
\end{aligned}
\tag{15}
$$


then formula (13) becomes:
$$
\int_1^{+\infty} \sqrt{\mu} (\mu y^2 + 2)^{-\frac{3}{2}} dy  
= \int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} }  \sqrt{\mu}(\mu(\sqrt{\frac{2}{\mu}} tan\theta)^2 + 2)^{-\frac{3}{2}} d(\sqrt{\frac{2}{\mu}} tan\theta)
\tag{16}
$$
where:
$$
(\mu(\sqrt{\frac{2}{\mu}} tan\theta)^2 + 2)^{-\frac{3}{2}} = (2tan^2\theta + 2)^{-\frac{3}{2}} = 2^{-\frac{3}{2}} {cos^{3}\theta}   \tag{17}
$$
and
$$
d(\sqrt{\frac{2}{\mu}} tan\theta) = \sqrt{\frac{2}{\mu}} \frac{1}{cos^2 \theta}  \tag {18}
$$
Substituting formulas (17)(18) into formula (16):
$$
\int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } \frac{1}{2}  cos\theta d \theta  \tag{19} 
=\frac{1}{2}  sin\theta |_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } = \frac{1}{2}   \left (1 - sin(tan^{-1}\sqrt{\frac{\mu}{2}})\right )
$$
where
$$
sin(x) = \sqrt{ \frac{tan^2 x}{1+tan^2 x}}
$$
then:
$$
\begin{aligned}
	sin(tan^{-1}\sqrt{\frac{\mu}{2}}) &= \sqrt{ \frac{tan^2 (tan^{-1}\sqrt{\frac{\mu}{2}})}{1+tan^2 (tan^{-1}\sqrt{\frac{\mu}{2}})}} \\  \\
	&=\sqrt{ \frac{ \sqrt{\frac{\mu}{2}}^2  }{1+\sqrt{\frac{\mu}{2}}^2} } \\ \\
	&= \sqrt{ \frac{\mu }{2+\mu} }
\end{aligned}
\tag {20}
$$
Substituting (20) into (19) gives:
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{2+\mu} } \right )   \tag{21}
$$
Note: In many textbooks and references, formula (21) is written as:
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{1+\mu} } \right )   \tag{21}
$$
This is because different assumptions are made about the statistical properties of the channel coefficient h, namely assuming mean 0 and variance 1, i.e. formula (2) becomes:
$$
p(z) = \frac{z}{\delta^2} e^{-\frac{z^2}{2\delta^2}} = z e^{-\frac{z^2}{2}}   \tag {22}
$$



It should also be noted that the curves obtained using formulas (2) and (22) will differ from each other, because the probability distribution of h has changed, and naturally the bit error rate is also different.



## BER Analysis under Rayleigh Fading - BPSK-SISO - Version without a Fixed Variance

This article analyzes the bit error rate under a Rayleigh fading channel, deriving the bit error rate for the Rayleigh fading case based on the bit error rate of the AWGN channel.
$$
y = hx + w  \tag{1}
$$
The transmitted symbol x is BPSK modulated, and h is a complex Gaussian random variable, where each dimension (real part and imaginary part) satisfies a Gaussian distribution with mean 0 and variance $$\sigma^2$$. Then the magnitude of h follows a Rayleigh distribution:
$$
p(z) = \frac{z}{\sigma^2} e^{-\frac{z^2}{2\sigma^2}}   \tag{2}
$$
If the signal energy is $$E_s$$, the signal-to-noise ratio without considering h is:
$$
SNR = \frac{Es}{N_0} = \mu  \tag{3}
$$
After considering h, the signal-to-noise ratio becomes
$$
\frac{|h|^2 E_s}{N_0} = a^2 \mu  \tag{4}
$$
where $$a = |h|$$. According to the bit error rate formula for BPSK modulation over an additive white Gaussian noise channel:
$$
BER = Q(\sqrt {SNR}) = Q(\sqrt{a^2 u}) \tag{5}
$$
Since h itself is also a random variable, we compute the average bit error rate under formula (1) according to the probability distribution of h:
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da \tag{6}
$$
Substituting (2) into (6):
$$
\int_0^{+\infty} Q(\sqrt{a^2 u}) p(a) da = \int_0^{+\infty} Q(\sqrt{a^2 u}) \frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} \tag{7}
$$
Substituting the Q function into (7) as well:
$$
\int_0^{+\infty} 
\{ \int_{\sqrt{a^2u}}^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{t^2}{2}} dt   \} 
\frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} da \tag{8}
$$
Performing a change of the integration variable on the inner integral of formula (8), letting: 
$$
y = \frac{t}{\sqrt{a^2 \mu}}
$$
then after the change of variable in the inner integral, the integration limits become:


$$
t =\sqrt{a^2u}, \quad y=1  \\
t = +\infty, \quad \quad y = +\infty
$$


then formula (8) becomes (the second equal sign is an interchange of the order of integration), and for convenience, let $$\gamma = \frac{1}{\sigma}$$ :
$$
\begin{aligned}
	&\int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} \frac{a}{\sigma^2} e^{-\frac{a^2}{2\sigma^2}} da  \\ \\
	&= \int_0^{+\infty} 
	\left \{ \int_1^{+\infty} \frac{1}{\sqrt{2\pi}} e^{-\frac{a^2 \mu y^2}{2}} d(y\sqrt{a^2 \mu} )  \right \} a\gamma^2 e^{-\frac{a^2 \gamma^2}{2}} da 
	\\ \\
	&= \int_0^{+\infty} \int_1^{+\infty}  \frac{ \gamma^2 a^2 \sqrt{\mu} }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + \gamma^2)}{2}}  dy  da
	\\  \\
	&= \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \left \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
	e^{-\frac{a^2 (\mu y^2 + \gamma^2)}{2}} da \right \} dy 
	\\  \\
\end{aligned}
\tag{9}
$$
In the innermost integral of formula (9), let 
$$
\hat \sigma ^2 = \frac{1}{\mu y^2 + \gamma^2}   \tag{10}
$$
then formula (9) becomes:
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi}}    
e^{-\frac{a^2 }{2 \hat \sigma^2}} da \} dy  = \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma \{ 2 \int_0^{+\infty}  \frac{a^2  }{\sqrt{2\pi} \hat \sigma}    
e^{-\frac{a^2 }{2 \hat \sigma^2}} da \} dy  \tag{11}
$$
The inner integral in formula (11) is exactly the variance of a Gaussian distribution with mean 0 and variance $$\hat \sigma^2$$, so formula (10) is derived as:
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma \hat \sigma^2 dy = \int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \hat \sigma^3 dy  \tag{12}
$$
Substituting (10) into (12):
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 \frac{1}{(\mu y^2 + \gamma^2)^{\frac{3}{2}}} dy  \tag{13}
$$
Performing another change of variable on the integral in formula (13), letting:
$$
y = \sqrt{\frac{\gamma^2}{\mu}} tan\theta   \tag{14}
$$
then when:
$$
\begin{aligned}
	&y=1,   &\theta = tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}    \\
	&y=+\infty, & \theta = \frac{\pi}{2} 
\end{aligned}
\tag{15}
$$


then formula (13) becomes:
$$
\int_1^{+\infty} \frac{1}{2}\sqrt{\mu}\gamma^2 (\mu y^2 + \gamma^2)^{-\frac{3}{2}} dy  
= \int_{ tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}}^{\frac{\pi}{2} }  \frac{1}{2}\sqrt{\mu}\gamma^2(\mu(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)^2 + \gamma^2)^{-\frac{3}{2}} d(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)
\tag{16}
$$
where:
$$
(\mu(\sqrt{\frac{\gamma^2}{\mu}} tan\theta)^2 + \gamma^2)^{-\frac{3}{2}} = (\gamma^2tan^2\theta + \gamma^2)^{-\frac{3}{2}} = ({\gamma^2})^{-\frac{3}{2}} {cos^{3}\theta}   \tag{17}
$$
and
$$
d(\sqrt{\frac{\gamma^2}{\mu}} tan\theta) = \sqrt{\frac{\gamma^2}{\mu}} \frac{1}{cos^2 \theta}  \tag {18}
$$
Substituting formulas (17)(18) into formula (16):
$$
\int_{ tan^{-1}\sqrt{\frac{\mu}{2}}}^{\frac{\pi}{2} } \frac{1}{2}\gamma^2 \frac{1}{\gamma^2}  cos\theta d \theta  \tag{19} 
=\frac{1}{2}  sin\theta |_{ tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}}^{\frac{\pi}{2} } = \frac{1}{2}   \left (1 - sin(tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})\right )
$$
where
$$
sin(x) = \sqrt{ \frac{tan^2 x}{1+tan^2 x}}
$$
then:
$$
\begin{aligned}
	sin(tan^{-1}\sqrt{\frac{\mu}{\gamma^2}}) &= \sqrt{ \frac{tan^2 (tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})}{1+tan^2 (tan^{-1}\sqrt{\frac{\mu}{\gamma^2}})}} \\  \\
	&=\sqrt{ \frac{ \sqrt{\frac{\mu}{\gamma^2}}^2  }{1+\sqrt{\frac{\mu}{\gamma^2}}^2} } \\ \\
	&= \sqrt{ \frac{\mu }{\gamma^2+\mu} }
\end{aligned}
\tag {20}
$$
Substituting (20) into (19) gives:
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\gamma^2+\mu} } \right )   \tag{21}
$$

## Comparison of BER between the Rayleigh Channel and the AWGN Channel

Bit error rate of the Rayleigh fading channel (BPSK modulation):


$$
BER_{Rayleigh} = \frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\frac{1}{\sigma^2}+\mu} } \right )   \tag{1}
$$
Bit error rate of the AWGN channel (BPSK modulation):
$$
BER_{AWGN} = Q(\mu) =\int_{\mu}^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt  \tag{2}
$$
where  $$\mu$$  is the signal-to-noise ratio without considering the attenuation coefficient:
$$
\mu = SNR = \frac{E_s}{N_0/2}  \tag{3}
$$



![Region Where a Decision Error Occurs](/figure/awgn/Rayleigh衰落信道与 AWGN 信道 BER 对比.png)

*Region Where a Decision Error Occurs*



a) For the Rayleigh fading channel, at the same signal-to-noise ratio, the larger the variance of the channel coefficient h, the lower the bit error rate: a larger variance of h can be understood as a greater amplification effect on the signal, and therefore is equivalent to increasing the signal-to-noise ratio.

b) Comparison between the AWGN channel and the Rayleigh fading channel:

b.1) When the variance of the Rayleigh fading channel is relatively small, i.e. the signal amplification factor is small or the signal is even attenuated, then AWGN outperforms the Rayleigh channel at both high and low signal-to-noise ratios.

b.2) When the variance of the Rayleigh fading channel is relatively large, i.e. the signal amplification factor is relatively large:

b.2.1) At low signal-to-noise ratio, i.e. when the noise energy is relatively large, since the signal amplification factor is large enough, the improvement brought by the increased signal-to-noise ratio exceeds the performance degradation caused by partial fading. So in this case, the bit error rate of the Rayleigh fading channel is lower than that of AWGN.

b.2.2) At high signal-to-noise ratio, i.e. when the noise power is relatively small, although the signal amplification factor is relatively large, there are always some cases where the signal is reduced. A reduced signal is a case of degraded signal-to-noise ratio, leading to an increased bit error rate. Since the noise is small, the performance of AWGN is very good (the energy difference between the two BPSK symbols is relatively large, so the probability of a bit error caused by noise interference is very low), whereas due to the randomness of the Rayleigh channel coefficient, there are always some coefficients that greatly reduce the signal energy, thereby degrading the signal-to-noise ratio and consequently increasing the bit error rate.




From the perspective of mathematical formulas, this is comparing the two bit error rates in formulas (1) and (2):
$$
\frac{1}{2} \left (   1 - \sqrt{ \frac{\mu }{\frac{1}{\sigma^2}+\mu} } \right ) 
\quad \quad\quad\quad  \quad\quad\quad\quad  \quad\quad\quad\quad
\int_{\mu}^{+\infty} \frac{1}{\sqrt{2\pi}}   e^{-\frac{t^2}{2}} dt
$$
Comparing the relative magnitude of the two, where there are two variables, namely $$\sigma^2$$ and $$\mu = SNR$$.

Code on github:  \url{https://github.com/taichiorange/leba_math.git}