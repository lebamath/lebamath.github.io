---
layout: default
title: "The Effect of Multipath Frequency Shift on the Received Signal"
lang: en
back_url: /index.html?lang=en
---
# The Effect of Multipath Frequency Shift on the Received Signal
When we study wireless communications, we often come across the need to study multipath effects. One of these situations is that the frequency has different offsets on different paths. In this situation, what kind of effect will a single-frequency signal transmitted by the base station have when it arrives at the receiver? This is the main content that this plain short article is going to show.

Here we will not discuss for now why there are different frequency offsets on different paths (this is related to the Doppler effect and to the angle of incidence of the radio wave).

Suppose the base station is going to transmit a single-frequency waveform to the receiver, and suppose the frequency is 100Hz. There are two different paths, where the frequency offset of one path is 5Hz and the frequency offset of the other path is 7Hz.

Then the transmitted signal can be expressed as:

$$
cos(2\pi*100*t)
$$

The signal received over the first path can be expressed as:

$$
cos(2\pi*(100-5)*t)
$$

The signal received over the second path can be expressed as:

$$
cos(2\pi*(100-7)*t)
$$

Then the signal received at the receiver can be expressed as:

$$
cos(2\pi*(100-5)*t) + cos(2\pi*(100-7)*t)
$$

The python code below plots the received signals of the two paths in the first figure and the second figure respectively

For the code please download it from github: \url{https://github.com/taichiorange/leba_math}

![waves_of_two_path.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/waves_of_two_path.png)

The upper waveform in the figure is the one offset by 5 Hz, and the second waveform is the one offset by 7Hz. At first glance they do not look any different, but in fact there is a tiny difference; if we plot them together, the difference can be clearly seen.

For the code please download it from github: \url{https://github.com/taichiorange/leba_math}
![two_waves_of_diff_freq_shift_in_one_image.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/two_waves_of_diff_freq_shift_in_one_image.png)
It can be clearly seen from the figure that as time goes on, the deviation between the two waveforms becomes larger and larger. If these two waveforms are superimposed together, what will the effect be? The figure below is the waveform after superposition:

For the code please download it from github: \url{https://github.com/taichiorange/leba_math}

![merge_of_two_waves_of_diff_freq_shift_in_one_image.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/merge_of_two_waves_of_diff_freq_shift_in_one_image.png)

It can be clearly seen that as time passes, the amplitude of the waveform first increases to 2 and then gradually decreases, and at around 0.25 second they have already cancelled each other out. This is the manifestation on the time axis of multipath having different frequency offsets.

Conclusion: in more professional terms, multipath frequency shift has selective fading in time, and the amplitude of the signal received at different instants (coming from the same frequency of the transmitter) is selective.

Let us extend the time in the last piece of code above to 1 second and see what the effect is:

For the code please download it from github: \url{https://github.com/taichiorange/leba_math}

![merge_of_two_waves_of_diff_freq_shift_in_one_image_duration_is_1s.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/merge_of_two_waves_of_diff_freq_shift_in_one_image_duration_is_1s.png)