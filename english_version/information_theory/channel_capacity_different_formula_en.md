---
layout: default
title: "Several Easily Confused Formulas for Channel Capacity"
lang: en
back_url: /english_version.html
---

# Several Easily Confused Formulas for Channel Capacity
This article attempts to explain the physical ideas behind channel capacity, or rather, to present my own rudimentary understanding of it.
First, let us put forward several channel capacity formulas that I have frequently encountered before but found confusing:
$$
C = \frac{1}{2} log(1+\frac{S}{N})   \tag{1}
$$
$$
C = W log(1+\frac{S}{N})  \tag{2}
$$
and
$$
C = log(1+\frac{S}{N})   \tag{3}
$$
Let us analyze them one by one. Formula (1) is the basis of the latter two, and its derivation is based on the following channel model:
$$
Y_i = X_i + Z_i   \tag{4}
$$
where $$X_i$$ is the input of the channel, the subscript i denoting the input at the i-th time instant, $$Y_i$$ is the output of the channel, and $$Z_i$$ is Gaussian white noise. All three quantities are real numbers, not complex numbers. Based on concepts and formulas from information theory such as mutual information and entropy, the channel capacity of the above channel can be found to be formula (1); for the detailed proof, see the derivation in Section 9.1 of reference [1].
The following is the idea that this article wants to emphasize: the above channel model (4) can be understood as a channel that transmits information by means of a cosine waveform within one period, that is, in reality, or on a physical line, information is conveyed through a cosine waveform. For example, if four cosine levels are used to convey information, then each level can carry two bits of information. We also know that the sine waveform at the same frequency as this cosine is orthogonal to the cosine waveform, so within the same time interval, we can also use the sine waveform to convey information (transmitted simultaneously with the cosine waveform of the same frequency). Because cosine and sine are orthogonal, the two will not interfere with each other.
Therefore, within 1 Hz, we can have two channels such as (4), so the maximum amount of data that can be transmitted in 1 Hz is twice that of (1), and thus we obtain formula (3). The unit of this capacity value in formula (3) is bits per second per Hz.
At this point, it becomes relatively easy to understand formula (2). Suppose our physical bandwidth (the frequency range actually occupied; note: positive frequencies, not negative frequencies) is W Hz, then the channel capacity within this bandwidth is formula (2), whose unit is bits per second.
This can be represented graphically as:
![A Brief Analysis of Channel Capacity](/figure/information theory/channel_capacity_tutorial.png)

*A Brief Analysis of Channel Capacity*
The above figure can be regarded as a 2-dimension channel. Reference [2] also mentions a 4-dimension channel; my personal understanding is that this requires implementing the structure shown in the figure above at another frequency point as well, with the two frequency points each being 2-dimension, together forming a 4-dimension channel.

[1] Elements of Information Theory - T M Cover- Second Edition

[2] P. E. McIllree, "Calculation of channel capacity for M-ary digital modulation signal sets," *Proceedings of IEEE Singapore International Conference on Networks/International Conference on Information Engineering '93*, Singapore, 1993, pp. 639-643 vol.2, doi: 10.1109/SICON.1993.515666.