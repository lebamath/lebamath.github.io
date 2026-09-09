---
layout: default
title: "The Effect of Multipath Delay"
lang: en
back_url: /index.html?lang=en
---
# The Effect of Multipath Delay
In wireless transmission, because of physical phenomena in the environment such as reflection and refraction, signals over multiple paths arrive at the receiver, and since the radio wave travels a different distance on each path, the delay with which each path arrives at the receiver is different.
Now let us demonstrate this with an example.

Suppose there are two frequencies transmitted over the air; one frequency is 20Hz. Run the following python code:

For the code please download it from [github](https://github.com/taichiorange/leba_math)

This will generate a waveform with amplitude 1:
![20Hz.png](/figure/通信基础/多径时延对不同频率的影响/20Hz.png)
The signal at this frequency is transmitted to the receiver over two paths, and the delays of the paths are 0.01 second and 0.015 second respectively. The signals received after the delays of the two paths are shown separately in the figure below:

Red: the originally transmitted signal

Green: the signal of the path with a delay of 0.01 second

Blue: the signal of the path with a delay of 0.015 second

Yellow: the result of superimposing, at the receiver, the two signals with different delays

The code is as follows:

For the code please download it from [github](https://github.com/taichiorange/leba_math)
![20hz_two_path.png](/figure/通信基础/多径时延对不同频率的影响/20hz_two_path.png)
It can be seen that after the two signals are superimposed, the amplitude is higher than that of the originally transmitted signal, i.e., it has been enhanced.

Next, let us look at the case of the 80Hz frequency; in this case the superimposed signal is weakened.

In the same way, the other frequency is 80 Hz. Run the following python code:

For the code please download it from [github](https://github.com/taichiorange/leba_math)

This will generate an 80Hz waveform with amplitude 1:
![80Hz.png](/figure/通信基础/多径时延对不同频率的影响/80Hz.png)
The signal at this frequency is transmitted to the receiver over two paths, and the delays of the paths are 0.01 second and 0.015 second respectively. The signals received after the delays of the two paths are shown separately in the figure below:

Red: the originally transmitted signal

Green: the signal of the path with a delay of 0.01 second

Blue: the signal of the path with a delay of 0.015 second

Yellow: the result of superimposing, at the receiver, the two signals with different delays

The code is as follows:

For the code please download it from [github](https://github.com/taichiorange/leba_math)
![80hz_two_path.png](/figure/通信基础/多径时延对不同频率的影响/80hz_two_path.png)
Red is the original signal. The blue and the green signals, the two with different delays, have a fairly large phase difference, so after superposition the signal is weakened; it can be seen that the superimposed yellow signal is much weaker than the red original signal.

Next, using the same two delayed paths, we sweep the frequency from 10 Hz to 1000Hz and look at how the maximum amplitude after superimposing the two delayed signals varies at different frequencies:

For the code please download it from [github](https://github.com/taichiorange/leba_math)
![10to1000.png](/figure/通信基础/多径时延对不同频率的影响/10to1000.png)
The amplitude of the original signal is 1 in every case. It can be seen that at 10Hz the received signal is stronger than the original signal, while at 100Hz the received signals have already cancelled each other out to 0.

