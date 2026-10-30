---
layout: default
title: " Frequency Domain"
lang: en
back_url: /index.html?lang=en
---
## Frequency Domain

5G NR, including the previous generation 4G LTE, are both built on the basis of OFDM (Orthogonal Frequency Division Multiplexing). We will not introduce OFDM in depth here, but we will introduce the idea of OFDM from a very intuitive angle.

The basic idea of OFDM is very simple, and it can be understood without needing knowledge of the discrete Fourier transform (DFT: Discrete Fourier Transform).  OFDM divides the frequency band into subcarriers of **equal bandwidth**.

For example, if the subcarrier width is 15kHz, then,

the frequency of the first subcarrier is 15kHz

the frequency of the second subcarrier is 30kHz = 15kHz * 2

the frequency of the third subcarrier is 45kHz = 15kHz * 3

the frequency of the fourth subcarrier is 60kHz = 15kHz * 4,

......

Here do not confuse the frequency value of a subcarrier with the width of a subcarrier: the frequency spacing between the individual subcarrier frequencies is 15kHz, whereas the frequency values of the individual subcarriers are different, and are all integer multiples of the frequency of the first subcarrier.


Now let us draw figures to understand this(in order to make the figures look concise, only the imaginary part of the complex exponential, that is, the sine part, has been drawn):

The waveform of the first subcarrier is shown in Figure 1; this is one complete period, the real part (cosine) is the solid line and the imaginary part (sine) is the dashed line.

The waveform of the second subcarrier is shown in Figure 2. This time span is the time of one period of the first subcarrier. Because the frequency of the second subcarrier is 2 times that of the first subcarrier, two complete periods can be seen in the figure.


The waveform of the third subcarrier is shown in Figure 3. This time span is the time of one period of the first subcarrier. Because the frequency of the third subcarrier is 3 times that of the first subcarrier, three complete periods can be seen in the figure.


The waveform of the fourth subcarrier is shown in Figure 4. This time span is the time of one period of the first subcarrier. Because the frequency of the fourth subcarrier is 4 times that of the first subcarrier, four complete periods can be seen in the figure.



![图1：First Subcarrier](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_k_1.png)

*图1：First Subcarrier*
![图2：the second subcarrier](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_k_2.png)

*图2：the second subcarrier*

![图3：the third subcarrier](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_k_3.png)

*图3：the third subcarrier*

![图4：the fourth subcarrier](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_k_4.png)

*图4：the fourth subcarrier*


The waveforms of these four subcarriers are sent out over the air at the same time, which is equivalent to these four waveforms being simply superposed together; the waveform after being superposed together is as shown in the figure 5.


![图5：Four subcarriers superposed together](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_4_SCs.png)

*图5：Four subcarriers superposed together*

We send waveforms out in order to carry useful data information, that is, data bits, so each subcarrier needs to be modulated. In the figures illustrated above, the initial phases of all the subcarriers are 0 and the maximum amplitudes are all 1, so in fact no information is carried. In wireless communications, different initial phases and/or amplitudes are used to represent the bit information carried, for example (if you want to understand modulation in depth, please refer to 16QAM modulation):

**Table 1: The amplitude/phase of each subcarrier and the corresponding bit information ($\Delta f = 15$ kHz)**

| $k$ | Frequency | Bits $b_0b_1b_2b_3$ | Amplitude $A_k$ | Phase $\varphi_k$ |
|:---:|:---------:|:-------------------:|:---------------:|:-----------------:|
| 1 | 15 kHz | 0011 | 1.3416 | $0.25\pi$ |
| 2 | 30 kHz | 1001 | 1.0000 | $0.6024\pi$ |
| 3 | 45 kHz | 1100 | 0.4472 | $-0.75\pi$ |
| 4 | 60 kHz | 0110 | 1.0000 | $-0.1024\pi$ |

Each subcarrier carries 4 bits of data, represented by different initial phases and amplitudes. Now let us draw once more the waveforms of the individual subcarriers that carry data (solid lines); the dashed lines in the figures are the original waveforms with 0 phase and amplitude 1 that carry no information.

The waveform of the first subcarrier is shown in Figure 6.

The waveform of the second subcarrier is shown in Figure 7.

The waveform of the third subcarrier is shown in Figure 8.

The waveform of the fourth subcarrier is shown in Figure 9.

![图6：First Subcarrier，initial phase and amplitude](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_modulation_k_1.png)

*图6：First Subcarrier，initial phase and amplitude*


![图7：Second Subcarrier，initial phase and amplitude](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_modulation_k_2.png)

*图7：Second Subcarrier，initial phase and amplitude*


![图8：Third Subcarrier，initial phase and amplitude](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_modulation_k_3.png)

*图8：Third Subcarrier，initial phase and amplitude*


![图9：Fourth Subcarrier，initial phase and amplitude](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_modulation_k_4.png)

*图9：Fourth Subcarrier，initial phase and amplitude*


The waveform after the four subcarriers carrying information have been superposed is shown in Figure 10.


![图10：Superposition of the four subcarriers carrying information](/figure/5G_NR_Access_Intro/grid/different_subcarrier_orth_modulation_sum.png)

*图10：Superposition of the four subcarriers carrying information*


For the example illustrated above, let us discuss it with simple mathematics; the amplitude and initial phase that each subcarrier has had added in order to carry bit information have also been taken into account. Suppose the subcarrier bandwidth is denoted by $$\Delta f$$, for example $$\Delta f = 15 \text{kHz}$$; the corresponding period is $$T = \frac{1}{\Delta f}$$.

For the first subcarrier, the information we want to send is $$\beta_1 e^{j\theta_1}$$, that is, the amplitude information is $$\beta_1$$ and the initial phase is $$\theta_1$$.

For the second subcarrier, the information we want to send is $$\beta_2 e^{j\theta_2}$$

For the third subcarrier, the information we want to send is $$\beta_3 e^{j\theta_3}$$

For the fourth subcarrier, the information we want to send is $$\beta_4 e^{j\theta_4}$$

Then, within one period, that is, the time parameter $$t$$  continuously runs over the time range $$[0,T)$$:

The waveform of the first subcarrier is then:  $$\beta_1 e^{j\theta_1} e^{j 2\pi\Delta f t}, t \in [0,T)$$, that is, $$\beta_1 e^{j \left (2\pi\Delta f t + \theta_1 \right ) }$$

The waveform of the second subcarrier is then:  $$\beta_2 e^{j\theta_2} e^{j 2\pi\cdot 2\Delta f t}, t \in [0,T)$$, that is, $$\beta_2 e^{j \left (2\pi\cdot 2\Delta f t + \theta_2 \right ) }$$

The waveform of the third subcarrier is then:  $$\beta_3 e^{j\theta_3} e^{j2\pi\cdot 3\Delta f t}, t \in [0,T)$$, that is, $$\beta_3 e^{j \left (2\pi\cdot3\Delta f t + \theta_3 \right ) }$$

The waveform of the fourth subcarrier is then:  $$\beta_4 e^{j\theta_4} e^{j 2\pi\cdot 4\Delta f t}, t \in [0,T)$$, that is, $$\beta_4 e^{j \left (2\pi\cdot 4\Delta f t + \theta_4 \right ) }$$

