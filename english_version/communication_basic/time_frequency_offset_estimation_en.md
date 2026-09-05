---
layout: default
title: "Time and Frequency Offset Estimation"
lang: en
back_url: /english_version.html
---
# Time and Frequency Offset Estimation
## Frequency Offset Estimation

In this short article, we discuss how to estimate a frequency offset. The problem is as follows:



A sine wave of a certain frequency is transmitted through the channel. Due to various reasons (perhaps the local crystal oscillator is not accurate), a frequency offset is introduced. So how does the receiver compute this frequency offset?



A sine or cosine waveform is periodic. We transmit at one frequency, for example 100 Hz, so every $$1/100=0.01$$ second there is a period starting from phase 0, as shown by the green line in the figure below:

The first figure is for the cosine

![Frequency offset, cosine](/figure/通信基础/频谱估计_1.png)

*Frequency offset, cosine*


The second figure is for the sine

![Frequency offset, sine](/figure/通信基础//频谱估计_2.png)

*Frequency offset, sine*

If there is a frequency offset of 10 Hz, then the received signal is the one shown in red in the figure.



We analyze by means of the red line:

At second 0, the phase is $$2 \pi (f+\Delta f) * 0 = 0$$

At 0.01 second, the phase is $$2 \pi (f+\Delta f) * 0.01  = 2\pi f * 0.01 + 2 \pi \Delta f * 0.01$$. For this phase, since the preceding part consists of an integer number of periods, the phase offset we see is $$2 \pi \Delta f * 0.01$$

At 0.02 second, the phase is $$2 \pi (f+\Delta f) * 0.02  = 2\pi f * 0.02 + 2 \pi \Delta f * 0.02$$. For this phase, since the preceding part consists of an integer number of periods, the phase offset we see is $$2 \pi \Delta f * 0.02$$



If we obtain this phase offset $$\Delta \theta$$

then the frequency offset can be computed by the following formula:


$$
\Delta \theta = 2 \pi \Delta f * \Delta t
$$


so:


$$
\Delta f = \frac{\Delta \theta}{ 2 \pi  \Delta t}
$$


Because


$$
\Delta \theta \in [-\pi, \pi]
$$
the range of the frequency offset that can be estimated is:


$$
\Delta f \in [ -\frac{1}{2\Delta t}, \frac{1}{2\Delta t}]
$$


For example, if $$\Delta t = 0.01$$, then the range of the frequency offset that can be estimated is:


$$
\Delta f \in [ -\frac{1}{2*0.01}, \frac{1}{2*0.01}]
$$


that is:


$$
\Delta f \in [ -50, 50]
$$


For the Python code used to draw the figures, please download it from GitHub: \url{https://github.com/taichiorange/leba_math}


## Time Offset Estimation

This short article attempts to analyze how to perform time offset estimation. The so-called time offset here refers to alignment on integer periods, for example the following sine wave:

There are two waveforms, one at 100 Hz and one at 200 Hz, and they are divided according to 0.01 second. If there is no time offset at all, the situation should be as shown in the figure below.

![Frequency offset, sine](/figure/通信基础/时偏估计-1.png)

*Frequency offset, sine*


If, for the vertical lines above, the time interval between the vertical lines is still 0.01 second, but due to various reasons (an offset in the local clock, etc.) the positions of the vertical lines are not at integer multiples of 0.01 second, for example they are all shifted 0.002 second to the right, then the result shown in the figure below appears:

![Frequency offset, sine](/figure/通信基础/时偏估计-2.png)

*Frequency offset, sine*


If the offset is too large, it will cause various problems, so there must be a way to estimate this time offset.



Time offset estimation can be done by performing a sliding cross-correlation within a certain time window and finding the place where the correlation value is largest; this is one method. This article mainly explores estimating the time offset in OFDM by means of reference signals.

Let us look at the second figure above. In the case where they are not aligned, the phase offsets of two waveforms of different frequencies are different. Based on this feature, we can deduce the time offset.


$$
e^{j2\pi f_1 nT}   \quad \quad  n=0,1,2,\cdots
$$


And another one of a different frequency:


$$
e^{j2\pi f_2 nT}   \quad \quad  n=0,1,2,\cdots
$$


If there is a small time offset $$\Delta t$$:


$$
e^{j2\pi f_1 (nT+\Delta t)}   \quad \quad  n=0,1,2,\cdots
$$


And the other one of a different frequency:


$$
e^{j2\pi f_2 (nT+\Delta t)}   \quad \quad  n=0,1,2,\cdots
$$
Then the phase offset of these two frequencies at the same time instant is (f1 and f2 are in a multiple relationship, and T is the period
of the frequency represented by their greatest common divisor):


$$
2\pi (f2-f1) \Delta t
$$


This phase can be computed in the system; if it is $$\Delta \theta$$, then


$$
\Delta \theta = 2\pi (f2-f1) \Delta t
$$


Then the time offset can be computed:


$$
\Delta t = \frac{\Delta \theta}{ 2\pi (f2-f1) }
$$


What needs to be noted here is that, in this derivation process, it is necessary to ensure that f1 and f2 are in a multiple relationship, and that T is the period of the frequency represented by their greatest common divisor.

The code for drawing the figures: please download it from GitHub: \url{https://github.com/taichiorange/leba_math}
