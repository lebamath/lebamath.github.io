---
layout: default
title: " Frequency Domain"
lang: en
back_url: /index.html?lang=en
---

### Frequency Domain

5G NR, including the previous generation 4G LTE, are both built on the basis of OFDM (Orthogonal Frequency Division Multiplexing). We will not introduce OFDM in depth here, but we will introduce the idea of OFDM from a very intuitive angle.

The basic idea of OFDM is very simple, and it can be understood without needing knowledge of the Discrete Fourier transform (DFT).  OFDM divides the frequency band into subcarriers of **equal bandwidth**.

For example, if the subcarrier width is 15kHz, then 

1st subcarrier frequency: 15kHz =15kHz * 1

2nd subcarrier frequency: 30kHz = 15kHz * 2

3rd subcarrier frequency: 45kHz = 15kHz * 3

4th subcarrier frequency: 60kHz = 15kHz * 4, 

......

the frequency spacing between the individual subcarrier frequencies is 15kHz.

As shown in [Figure 1](#fig-different-subcarrier), the first four waveforms are the subcarriers corresponding respectively; it can be seen that the frequency and period of each subcarrier are different, and the frequencies are all integer multiples of the subcarrier bandwidth.

It is easy to prove:

Different subcarriers (meaning subcarriers of the same width but at different positions) are orthogonal between the complex exponential waveforms; even if the initial phases and amplitudes of these waveforms are all different, they are still all orthogonal. The orthogonality here means orthogonality within the time interval $$[0,T)$$ ($$T=1/\Delta f$$, or an integer multiple of it).

**Figure 1: sub carriers**

<a id="fig-different-subcarrier"></a>

![sub carriers](/figure//5G_NR_Access_Intro/different_subcarrier_orth.png)


Superposing these subcarrier waveforms carrying phase and amplitude information together and sending them to the receiving side (the up-conversion and down-conversion actions of the high-frequency carrier are omitted here, that is, it is regarded as an equivalent baseband system), because of the orthogonality of the different subcarriers, the receiving side can separate out each subcarrier, and can thereby obtain the phase and amplitude information of each subcarrier.

Simply superposing together the first four waveforms shown in [Figure 1](#fig-different-subcarrier) forms the fifth waveform plot ($$\sum \text{Re}$$）, where only the superposition plot of the real part is displayed.

The last one in [Figure 1](#fig-different-subcarrier) is a spectrum plot, which displays the spectrum plots of the different subcarriers, used to intuitively show the orthogonality: that is, the highest amplitude point of the spectrum of a certain subcarrier corresponds exactly to the amplitude 0 points (null points) of the other subcarriers.

For the example illustrated above, let us discuss it with simple mathematics. Here we also take into account that each subcarrier has had an amplitude and an initial phase added in order to carry bit information. Suppose the subcarrier bandwidth is denoted by $$\Delta f $$, for example $$\Delta f = 15 \text{kHz}$$; the corresponding period is $$T = \frac{1}{\Delta f}$$.

For the first subcarrier, the information we want to send is $$\beta_1 e^{j\theta_1}$$, that is, the amplitude information is $$\beta_1$$ and the initial phase is $$\theta_1$$.

For the second subcarrier, the information we want to send is $$\beta_2 e^{j\theta_2}$$

For the third subcarrier, the information we want to send is $$\beta_3 e^{j\theta_3}$$

For the fourth subcarrier, the information we want to send is $$\beta_4 e^{j\theta_4}$$

Then, within one period, that is, the time parameter $$t$$ continuously runs over the time range $$[0,T)$$, where for intuitive understanding the time added by the cyclic prefix is temporarily ignored .

The waveform of the first subcarrier is then:  $$\beta_1 e^{j\theta_1} e^{j 2\pi\Delta f t}, t \in [0,T)$$, that is, $$\beta_1 e^{j \left (2\pi\Delta f t + \theta_1 \right ) }$$

The waveform of the second subcarrier is then:  $$\beta_2 e^{j\theta_2} e^{j 2\pi\cdot 2\Delta f t}, t \in [0,T)$$, that is, $$\beta_2 e^{j \left (2\pi\cdot 2\Delta f t + \theta_2 \right ) }$$

The waveform of the third subcarrier is then:  $$\beta_3 e^{j\theta_3} e^{j2\pi\cdot 3\Delta f t}, t \in [0,T)$$, that is, $$\beta_3 e^{j \left (2\pi\cdot3\Delta f t + \theta_3 \right ) }$$

The waveform of the fourth subcarrier is then:  $$\beta_4 e^{j\theta_4} e^{j 2\pi\cdot 4\Delta f t}, t \in [0,T)$$, that is, $$\beta_4 e^{j \left (2\pi\cdot 4\Delta f t + \theta_4 \right ) }$$

**Figure 2: sub carriers with modulation**

<a id="fig-different-subcarrier-with-modulation"></a>

![sub carriers with modulation](/figure//5G_NR_Access_Intro/different_subcarrier_orth_with_modulation.png)



As shown in [Figure 2](#fig-different-subcarrier-with-modulation), a different amplitude and initial phase has been given to each subcarrier; the amplitude and phase information is as shown in the constellation diagram at the lower left of the figure , where the four colored points correspond respectively to the amplitudes and phases of the four subcarriers above. The figure at the lower right is still the spectrum of the individual subcarriers, and it can be seen that orthogonality is still satisfied. The fifth waveform in the figure is the waveform after the four subcarriers with amplitude and initial phase have been simply superposed in time; the solid line is the real part and the dashed line is the imaginary part. This composite waveform is the waveform sent to the receiving side, that is to say, what the receiving side gets is the superposed waveform; to explain once more, because orthogonality is satisfied between the subcarriers, the receiving side can separate out the amplitude and phase information of each subcarrier.

The time-frequency grid is a two-dimensional grid; the vertical direction is the frequency domain,  and the boxes are divided according to the subcarrier width.
