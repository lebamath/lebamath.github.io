---
layout: default
title: " zadoff 码的超级快速的傅立叶变换"
back_url: /index.html?lang=zh
---

## zadoff 码的超级快速的傅立叶变换

录制的视频在 [B 站](https://www.bilibili.com/video/BV1EY4y1T7DY)



参考文献：[Efficient computation of DFT of Zadoff-Chu sequences](https://www.researchgate.net/publication/224408244\_Efficient\_computation\_of\_DFT\_of\_Zadoff-Chu\_sequences)

一般的 zadoff 码, 其数学表达式可以写成:

$$
x_u(m)=e^{-j\frac{\pi u m(m+1)}{L}},\qquad
m=0,1,\cdots,L-1
$$

**这个函数是以 $$L$$ 为周期的**

**证明：**

$$
\begin{aligned}
	x_u(m+L)
	&=
	e^{-j\frac{\pi u(m+L)(m+L+1)}{L}}\\
	&=
	e^{-j\frac{\pi u m(m+1)}{L}}
	\,e^{-j2\pi u\left(m+\frac{L+1}{2}\right)}.
\end{aligned}
$$

Zadoff 序列的码长一般取为质数。当 $$L>2$$ 时，$$L$$ 一定为奇数，因此 $$L+1$$ 一定为偶数，即

$$
\frac{L+1}{2}\in\mathbb{Z}.
$$

因此

$$
e^{-j2\pi u\left(m+\frac{L+1}{2}\right)}=1,
$$

从而得到

$$
x_u(m+L)=x_u(m).
$$

故 $$x_u(m)$$ 是一个以 $$L$$ 为周期的序列。

```
[language=Python]
	import matplotlib.pyplot as plt
	import numpy as np
	
	L = 7
	u = 3
	
	m = np.arange(7*L)
	
	x = np.exp(-1j * np.pi * m * (m+1) / L)
	
	fig,[ax_re,ax_im] = plt.subplots(2,1,figsize=(18,5))
	ax_re.plot(m, np.real(x),&amp;quot;.--&amp;quot;)
	ax_im.plot(m, np.imag(x),&amp;quot;.--&amp;quot;)
```

![zadoff.png](/figure/5GNR/RACH/zadoff.png)


对 ZC 序列做 DFT:

$$
Y[k] = \sum_{m=0}^{L-1} x_u(m) e^{-j 2\pi \frac{k}{L} m}
$$

把 $$x_u$$ 的表达式代入上式 

$$
\begin{aligned}
		Y[k] &= \sum_{m=0}^{L-1} e^{-j \frac{\pi u m (m+1)}{L}} e^{-j 2\pi \frac{k}{L} m} \\
		&= \sum_{m=0}^{L-1} e^{-j \frac{\pi u m (m+1) + 2\pi k m}{L}}
	\end{aligned}
\tag{1}
$$

找一个自然数 v，使得 uv mod L = 1，构造一个表达式 :

$$
e^{j \frac{\pi u v k (v k + 1)}{L}}
$$

把公式(1) 中提取出上式：

$$
Y[k] = e^{j \frac{\pi u v k (v k + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \frac{\pi u m (m+1) + 2\pi k m + \pi u v k (v k + 1)}{L}}
$$

因为 $$uv$$ 模 $$L$$ 余 $$1$$，所以，可以把 $$uv$$ 乘在分子的任何含有 $$2\pi$$ 的项上，容易证明，乘完之后不改变原等式。

$$
\begin{aligned}
		Y[k] &= e^{j \frac{\pi u v k (v k + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \frac{\pi u m (m+1) + 2\pi k m u v + \pi u v k (v k + 1)}{L}} \\
		&= e^{j \frac{\pi u v k (v k + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1) + 2k m v + v k (v k + 1)}{L}}
	\end{aligned}
$$

其中

$$
\begin{aligned}
		m(m+1) + 2kmv + vk(vk+1) &= m^2 + (2kv+1)m + vk(vk+1) 
		\\
		&= (m+vk)(m+vk+1)
	\end{aligned}
$$

所以

$$
Y[k] = e^{j \frac{\pi u v k (v k + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{(m+vk)(m+vk+1)}{L}}
$$

上式中的求和项，也是一个 zadoff 码，因为 zadoff 码是以 $$L$$ 为周期的周期函数，所以，第二个求和项可以等价替换：

$$
Y[k] = e^{j \frac{\pi u v k (v k + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1)}{L}}
$$

当 $$k=0$$ 时：

$$
Y[0] = \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1)}{L}}
$$

当 $$k=1$$ 时，

$$
Y[1] = e^{j \frac{\pi u v (v + 1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1)}{L}} = e^{j \frac{\pi u v (v + 1)}{L}} Y[0] = x_u^*(v) Y[0]
$$

其中 $$x_u^*(v)$$ 表示 $$x_u(v)$$ 的共轭。

可以看到，$$Y[k]$$ 可以用 $$Y[0]$$ 和某一个 $$x_u(m)$$ 的共轭相乘即可得到，这要比 DFT 的计算量要少很多，即使与 FFT 比较也计算量要少不少。

依次类推，

当 $$k=2$$ 时，

$$
Y[2] = e^{j \frac{\pi u 2v(2v+1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1)}{L}} = e^{j \frac{\pi u 2v(2v+1)}{L}} Y[0] = x_u^*((2v) \bmod L) Y[0]
$$

一般化：

$$
Y[k] = e^{j \frac{\pi u v k(v k+1)}{L}} \sum_{m=0}^{L-1} e^{-j \pi u \frac{m(m+1)}{L}} = e^{j \frac{\pi u v k(v k+1)}{L}} Y(0) = x_u^*((kv) \bmod L) Y[0]
$$