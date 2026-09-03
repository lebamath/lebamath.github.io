---
layout: default
title: "How to Evaluate Whether a Channel Coding/Decoding Algorithm Reaches the Shannon Limit via Simulation"
lang: en
back_url: /english_version.html
---
## How to Evaluate Whether a Channel Coding/Decoding Algorithm Reaches the Shannon Limit via Simulation

When designing channel coding algorithms, the goal is always to approach the Shannon channel capacity limit. So once we have designed a set of encoding and decoding algorithms and obtained a bit error rate (BER) curve through simulation, how do we use this BER curve to evaluate whether the Shannon channel capacity limit has been reached? This is what this short article aims to discuss.

We know that the unit of channel capacity C is bps/Hz, i.e., the maximum number of bits that can be transmitted per second per unit frequency. If the bandwidth is B, then the number of bits transmitted in one second is CB.

If we use a certain modulation scheme for data transmission, for example 1024QAM, then each symbol can carry 10 bits, which we denote as M, with the unit bit/symbol. If each period is used to transmit one symbol, then the number of bits transmitted in one second is MB.

Then the code rate is
$$
R = \frac{CB}{MB} = \frac{C}{M}
$$

The channel capacity C is achieved at a certain SNR. Then, at this SNR, for a channel code with code rate R, the bit error rate must be sufficiently low, for example as low as $$10^{-5}$$.

Let us take 1024QAM modulation as an example, with M = 10. Suppose that under 1024QAM modulation, the channel capacity curve is as shown in the figure below:

![Channel Capacity Curve](/figure/information_theory/信道容量曲线.png)

*Channel Capacity Curve*

At the position $$SNR = SNR_0$$, the channel capacity C = 3 bps/Hz.

Then the code rate is $$R = \frac{3}{10}$$. We can then draw a vertical line on the BER curve plot; as shown in the figure below, the BER curve obtained from simulating our encoding/decoding algorithm needs to lie to the right of this vertical line and close to it. The figure shows several different implementations of encoding and decoding, from which we can see how many dB of SNR each is away from the Shannon limit when reaching the target bit error rate.

![Distance Between Channel Coding and the Shannon Limit](/figure/information_theory/信道编码与香农限之间的距离.png)

*Distance Between Channel Coding and the Shannon Limit*