---
layout: default
title: "多径频移对接收信号的影响"
back_url: /index.html?lang=zh
---

# 多径频移对接收信号的影响
录制的视频在[B站](https://www.bilibili.com/video/BV13Y4y1T7dB/)

   我们在学习无线通信的时候，经常会遇到要研究多径效应，其中一种情况就是不同路径上频率有不同的偏移，在这种情况下，基站发射的一个单一频率信号，到达接收端会有什么样的效果？这是这篇浅显的短文要展示的主要内容。

我们这里先不探讨为什么不同的路径上会有不同的频率偏移（这个与多普勒效应以及无线电波的入射角度有关）。

假设基站要发射一个单频的波形给接收端，假如频率是 100Hz. 有两个不同的路径，其中一个路径的频率偏移是 5Hz, 另外一个路径的频率偏移是 7Hz.

​    那么发射出来的信号，可以表示为：

$$
cos(2\pi*100*t)
$$

第一个路径接收到的信号，可以表示为：

$$
cos(2\pi*(100-5)*t)
$$

第二个路径接收到的信号，可以表示为：

$$
cos(2\pi*(100-7)*t)
$$

那么，接收端接收到的信号，可以表示为：

$$
cos(2\pi*(100-5)*t) + cos(2\pi*(100-7)*t)
$$

下面的 python 代码，把接收到的两个路径的信号分别画在第一个图和第二个图中

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

![waves_of_two_path.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/waves_of_two_path.png)

图中上边一个波形是偏移 5 Hz 的，第二个波形时偏移 7Hz 的，乍一看上去也没什么不同，实际上是有微小的差别的，我们把他们画在一起，就能明显看出来差别。

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

![two_waves_of_diff_freq_shift_in_one_image.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/two_waves_of_diff_freq_shift_in_one_image.png)

从图中可以明显看到，随着时间的延长，两个波形偏差越来越大，如果把这两个波形叠加再一起，会有什么效果呢？下图就是叠加在一起的波形：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

![merge_of_two_waves_of_diff_freq_shift_in_one_image.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/merge_of_two_waves_of_diff_freq_shift_in_one_image.png)

可以明显看出来，随着时间的推移，波形的幅度先是增强到 2 ，然后逐渐降低，大概在 0.25 秒左右，已经是相互抵消了。这就是多径有不同频率偏移，在时间轴上的表现。

结论：专业一点的说法，多径频移在时间上有选择性衰落，不同时刻接收的信号（来自发送者同一个频率的）幅度会有选择性。

我们把上面最后一个代码中的时间，拉长到 1 秒，看看什么效果：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

![merge_of_two_waves_of_diff_freq_shift_in_one_image_duration_is_1s.png](/figure/通信基础/不同路径有不同频移对接收信号的影响/merge_of_two_waves_of_diff_freq_shift_in_one_image_duration_is_1s.png)