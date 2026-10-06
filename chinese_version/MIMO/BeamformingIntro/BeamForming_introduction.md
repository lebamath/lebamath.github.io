# 线阵波束成形浅析
## 波束成形(Beamforming)的数学推导(一)---从发射端看


本篇小文尝试用简单的语言，把波束赋形的数学原理说一下，力求浅显易懂，同时给出一个简单的 python 代码来演示这些数学公式。

本篇文章参考了 [MIMO通信的角域表示 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/369340833)，在此特别感谢。本文是对这篇文章中一个很小的一个部分的重写，使用更加详细的语言来把我的理解说一下。

我们要探讨的是波束如何成形的，也就是 “指哪儿打哪儿”，能让无线电波按照指定的方向打过去。

天线阵列，我们讨论的是 ULA（Uniform Linear Array，均匀线性阵列），如下图所示：

![ULA（Uniform Linear Array，均匀线性阵列）](/figure/beamforming/ULA.png)

*ULA（Uniform Linear Array，均匀线性阵列）*


是水平均匀排列的一组天线，天线之间的间隔距离记为  d .

若不计信号的衰减，我们发射的数据是 s ( 一个复数 )，而且接收端与发射端距离足够远，发射端各个天线到接收端的单个天线，之间的连线，从发射端天线附近看起来就是平行的（实际上不是平行的，是接近平行，是为了计算的一种简化，如果不简化，计算就过于复杂）。

![bf transmitter](/figure/beamforming/bf_transmitter.png)

*bf transmitter*


假如天线阵列连线 与 接收端连线构成的夹角为

$$
\theta
$$

那么接收端接收的 来自不同发射天线的无线信号，就有不同的时间延迟，进而有不同的相位旋转.

我们以最边上的一个发射天线为参照，我们选择直线距离最短的那个天线做参考，则第一根发射天线的发射无线电波到达接收天线，则紧挨着的第二根发射天线的无线电波，要比第一根发射天线发射的无线电波，多走的距离为

$$
d \space   cos(\theta)
$$

则第 k 根天线发射的无线电波比第一根天线发射的无线电波，多走的距离为

$$
(k-1) d \space cos(\theta)
$$

则多走的时间为:  多走的距离除以光速 c

$$
\Delta t = \frac{(k-1) d \space cos(\theta)}  { c }
$$

我们讨论单频的电磁波，假设频率为 f，则由于多走的时间，导致的相位偏差为：

$$
2\pi f \Delta t = \frac{ 2 \pi f(k-1) d \space cos(\theta)}  { c } = \frac{ 2 \pi (k-1) d \space cos(\theta)} {\lambda}
$$

其中：

$$
\lambda=\frac{c}{f}
$$

为波长。

我们把

$$
\psi =\frac{ 2 \pi  d \space cos(\theta)} {\lambda}
$$

则 N 个天线，对应的相位偏差分别为：

$$
0,\space  \psi , \space  2\psi,\space  3\psi,\cdots,\space  (N-1)\psi
$$

我们是在频域分析的，所以，相当于发射的信号，虽然从每个发射天线出来的信号都是一样的，但是接收端接收到的信号，则分别被乘以：

$$
e^{j0},\space e^{j \psi},\space e^{j 2\psi},\space e^{j 3\psi},\cdots,\space e^{j (N-1)\psi}
$$

则接收到的信号为：

$$
\frac{1}{N} \sum_{k=0}^{N-1}s e^{jk\psi} = s \frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}
$$

注意：上式除以了 N，是能量归一化，不影响分析。

则接收到的信号，相对于发射的信号，其变化为：

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}
$$

若仅考虑能量增益，则：

$$
G(\psi) = \frac{1}{N}  |\sum_{k=0}^{N-1} e^{jk\psi}|
$$

经过一些推导（可参考后面的附录），上式整理为：

$$
G(\psi) = \begin{cases}
	\begin{vmatrix}
		\frac{sin(N\psi/2)}{Nsin(\psi/2)}  
	\end{vmatrix} 
	& \text{ if } \psi \neq 0 \\
	1  & \text{ if } \psi = 0
\end{cases}
$$

把

$$
\psi =\frac{ 2 \pi  d \space cos(\theta)} {\lambda}
$$

代入后，我们可以计算不同的角度对应不同的增益， 当 $$\psi$$ 为 0 时，取最大值，即

$$
\theta = \frac{\pi}{2}
$$

时取最大。

用 python 程序，在极坐标上画出来的增益曲线如下：

此程序中假设   $$\frac{d}{\lambda}=\frac{1}{2}$$ 

![bf.png](/figure/beamforming/bf.png)



16 个天线排一排

![bf_16.png](/figure/beamforming/bf_16.png)

可以看出来，天线多，则越集中。

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}




附录：

$$
\begin{aligned}
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}  
&=\frac{1-e^{jN\psi}}{1-e^{j\psi}} \\
&= \frac{e^{jN\psi/2}}{e^{j\psi/2}}  \frac{(e^{-jN\psi/2}-e^{jN\psi/2})}{(e^{-j\psi/2}-e^{j\psi/2})} \\
&=  \frac{e^{jN\psi/2}}{e^{j\psi/2}} \frac{sin(N\psi/2)}{sin(\psi/2)}
\end{aligned}
$$

若只考虑幅度，则上面推导中最后的两个分式中，第一个分式的模是 1，可以忽略。

$$
\begin{vmatrix}
	\frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}
\end{vmatrix} 
= 
\begin{vmatrix}
	\frac{sin(N\psi/2)}{Nsin(\psi/2)}  
\end{vmatrix}
$$

## 波束成形(Beamforming)的数学推导(二)---各天线发射不同的信号
在上一篇文章中，我们用浅显的语言，讲解了波束赋形的基本数学原理。在那篇文章中，我们是让所有的发射天线发送相同的信号，然后测量了在不同位置上接收天线接收到的信号的强度，结果是在垂直于发射天线阵列的方向上，信号强度最大。

那么，如何让指定方向上的接收天线能接收到最大强度的信号呢？一种很笨的方法，就是调整天线阵列的物理位置，使得垂直与发射天线阵列的方向，刚好指向接收者，虽然这个方法很笨，但是，可以方便我们理解后面的原理。实际上，从垂直与发射天线阵列的角度看，因为接收者在垂直方向，那么每个发射天线发射来的电磁波，都是以相同的相位抵达的，则刚好可以产生信号叠加的效果（没有信号相互抵消的），那么，在接收者不在垂直方向时，我们能否制造出来相同的效果：就是让每个发射天线发射来的电磁波，都是以相同相位的抵达接收天线？

如下图所示：

![16 个天线排一排](/figure/beamforming/bf_16.png)

*16 个天线排一排*

不在垂直方向上的接收者，接收不同发射天线来的电磁波，因为电磁波走的距离不同，从而造成了相位差异，那么，我们可以让天线上发出来的信号，提前把相位差异考虑进去，改变发射的信号，来抵消相位的差异。

我们用上一篇文章中的那个图来分析：

![bf_transmitter.png](/figure/beamforming/bf_transmitter.png)

以第一根天线为基准，第一根天线上发射的信号是 S ( 复数，含有幅度和相位），那么第二根发射天线发出的信号，抵达接收者后，与第一根天线发射的信号抵达接收者之间的相位差是  $$e^{j\psi}$$ ，所以，我们可以把第二根发射天线发出的信号，提前把相位抵消掉，在第一根天线的信号基础上，乘以 $$e^{-j\psi}$$ 后，再从第二根发射天线上发射出去，同理，对第三根发射天线，把原始信号 S 乘以$$e^{-j2\psi}$$ 后再从第三根发射天线上发射出去，以此类推，直到第 N 根发射天线，把原始信号 S 乘以$$e^{-j（N-1)\psi}$$ 后再从第N根发射天线上发射出去.

从以上的分析可以看出，再指定的那个方位上，接收天线应该能接收到最大强度的信号。下面，用数学公式来分析一下。

N 根发射天线上发出的信号为：

$$
S, \quad S*e^{-j\psi_0}, \quad S*e^{-j2\psi_0}, \cdots,\quad S*e^{-j(N-1)\psi_0}
$$

在上一篇文章中，我们已经得出 $$\psi_0 = \frac{2\pi d cos(\theta_0)}{\lambda}$$，若令 $$d = \lambda /2$$ , 则 $$\psi_0 =  \pi cos(\theta_0)$$, 其中 $$\theta_0$$ 是接收天线所在的方位角。

那么，在任意指定的方位角 $$\theta$$ 上，从 N 根发射天线接收到的信号，由于无线电波走的路径长度有差异，导致空中传输的信号，有不同的相位延迟，延迟的相位分别为：

$$
0,\quad \phi ,\quad 2\phi,\cdots, ,\quad (N-1)\phi
$$

其中 $$\psi =  \pi cos(\theta)$$，由于我们是在频域做的分析，所以，相当与每个发射天线上发射出来的信号，被分别乘以：

$$
1,\quad e^{j\psi},\quad e^{j2\psi},\cdots,,\quad e^{j(N-1)\psi}
$$

则接收天线从每个发射天线接收到的信号分别为：

$$
S, \quad S*e^{-j\psi_0}e^{j\psi}, \quad S*e^{-j2\psi_0}e^{j2\psi}, \cdots,\quad S*e^{-j(N-1)\psi_0}e^{j(N-1)\psi}
$$

整理一下：

$$
S, \quad S*e^{j(\psi-\psi_0)}, \quad S*e^{j2(\psi-\psi_0)}, \cdots,\quad S*e^{j(N-1)(\psi-\psi_0)}
$$

则接收天线最终收到的信号是对以上信号求和(额为除了个 N，只是为了能量归一化，不影响分析）：

$$
\frac{1}{N}\sum_{k=0}^{N-1}S e^{jk(\psi-\psi_0)} = S \frac{1}{N}\sum_{k=0}^{N-1} e^{jk(\psi-\psi_0)}
$$

对增益的部分做等比数列求和：

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk(\psi-\psi_0)} =
\begin{cases}
	|\frac{sin(N(\psi-\psi_0)/2)}{N sin((\psi-\psi_0)/2)}| & \text{ if } \psi-\psi_0 \neq 0  \\
	1 & \text{ if } \psi-\psi_0 = 0
\end{cases}
$$

可以看出，当 $$\psi = \psi_0$$ 时，信号的强度最大，也就是 $$\theta=\theta_0$$ 时信号强度最大，这样就实现了在接收天线方位角 $$\theta_0$$ 方向上，信号强度最大，实现了 “指哪儿打哪儿".

下面以 $$\theta_0=\frac{\pi}{6}$$ 为例，让 $$\theta \in [0,2\pi]$$，在极坐标上画出来信号强度的图：

![*信号强度](/figure/beamforming/bf_pointedt_to_somewhere.png)

**信号强度*

Python 代码如下：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}


## 波束成形(Beamforming)的数学推导(三)---从接收端看

在前两篇文章中，我们讨论了一组发射天线，如何做到波束成形的（让波束指哪儿打哪儿）。那么，我们很自然的容易问：是否能用一组接收天线，在指定方向上接收信号，在其他方向上抑制信号，或者说收不到其他方向上的信号？
我们用类似的模型，让一组接收天线排成一列，假如是 N 个天线，如下图所示：

假设在足够远处有一个发射天线，那么 N个 接收天线接收的信号，可以近似认为是平行到达各个接收天线的，与天线阵列的夹角都是相同的。如下图所示：

![接收](/figure/beamforming/receiver.png)

*接收*

那么以图中最下面天线收到的信号为基准，则其上紧挨着的天线，接收到的信号多走的距离就是:

$$
d \space cos(\theta)
$$

依此类推，第 k 个天线接收的信号，多走的距离就是：

$$
(k-1) d \space cos(\theta)
$$

距离除以光速 c，就是信号多走的时间（第k个天线比第一个天线多走的时间）：

$$
\Delta t_k = \frac{(k-1) d \space cos(\theta)}{c}
$$

假设发送的电磁波是一个单频信号，频率是 f，则多走的时间导致的相位偏差为：

$$
2\pi f \Delta t_k = \frac{ 2 \pi f(k-1) d \space cos(\theta)}{c}
$$

频率 f 对应的周期为 1/f，则一个周期对应的长度（波长），就是 c * 1/f，即：

$$
\lambda = \frac{c}{f}
$$

那么：

$$
2\pi f \Delta t_k = \frac{ 2 \pi f(k-1) d \space cos(\theta)}{c}= \frac{ 2 \pi (k-1) d \space cos(\theta)}{\lambda}
$$

把上面公式中与 k 无关的部分提取出来，单独给个记号：

$$
\psi = \frac{2 \pi d \space cos(\theta)}   {\lambda}
$$

那么这 N 个接收天线，以第一根天线为参照，其相位偏差分别为：

$$
0,\space \psi,\space 2\psi, \cdots, (N-1)\psi
$$

则这 N 根天线收到的信号，以第一根天线为参照，相当于分别被乘以一个复数：

$$
e^{j0\psi},\space e^{j1\psi}, \space e^{j2\psi},\cdots,e^{j(N-1)\psi}
$$

把 N 根天线收到的信号叠加起来:

$$
\frac{1}{N} \sum_{k=0}^{N-1} e^{jk\psi}
$$

做等比数列求和：

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk \psi} =
\begin{cases}
	|\frac{sin(N \psi /2)}{N sin(\psi/2)}| & \text{ if } \psi \neq 0  \\
	1 & \text{ if } \psi = 0
\end{cases}
$$

可以看出，当 $$\psi = 0$$ 时，信号的强度最大，也就是 $$\theta=\frac{\pi}{2}$$ 时信号强度最大，也就是说，垂直于天线阵列方向上发来的信号，会被接收阵列收到最大的信号。

图形如下：

![bf.png](/figure/beamforming/bf.png)

代码中假设 $$\frac{d}{\lambda} = \frac{1}{2}$$.

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：A\_beam\_shape\_wide\_narrow\_show.py


附录：

$$
\begin{aligned}
\frac{1}{N}\sum_{i=0}^{N-1} e^{jk\psi}  
&=\frac{1}{N}\frac{1-e^{jN\psi}}{1-e^{j\psi}} \\
&= \frac{1}{N}\frac{e^{jN\psi/2}}{e^{j\psi/2}}  \frac{(e^{-jN\psi/2}-e^{jN\psi/2})}{(e^{-j\psi/2}-e^{j\psi/2})} \\
&=  \frac{e^{jN\psi/2}}{e^{j\psi/2}} \frac{sin(N\psi/2)}{Nsin(\psi/2)}
	\end{aligned}
$$

若只考虑幅度，则上面推导中最后的两个分式中，第一个分式的模是 1，可以忽略。

$$
\begin{vmatrix}
	\frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}
\end{vmatrix}
=
\begin{vmatrix}
	\frac{sin(N\psi/2)}{Nsin(\psi/2)}  
\end{vmatrix}
$$

## 波束成形(Beamforming)的数学推导(四)---接收指定方向的信号

从上篇文章的分析中，我们知道 N 根接收天线，可以接收垂直与天线阵列方向上发射来的信号，抑制其他方向上的信号。但是，我们看到，这个是没有做到可以调整接收方向，即没有做到“指哪儿收哪儿”。

用如下图所示的天线阵列：


![receiver.png](/figure/beamforming/receiver.png)


这 N 根天线收到的信号，以第一根天线为参照，相当于分别被乘以一个复数：

$$
e^{j0\psi},\space e^{j1\psi}, \space e^{j2\psi},\cdots,e^{j(N-1)\psi}
$$

那么，我们可以把这个被乘上去的复数抵消掉，每个接收的信号，我们乘以上面复数的共轭，即：

$$
e^{-j0\psi},\space e^{-j1\psi}, \space e^{-j2\psi},\cdots,e^{-j(N-1)\psi}
$$

则接收到的信号应该是在 $$\theta$$ 方向上最大。下面我们可以推导一下。
假如乘上去的共轭复数，是以一个具体的角度  $$\theta_0$$ 推导出来的：

$$
\psi_0 = \frac{2 \pi d \space cos(\theta_0)}   {\lambda}
$$

则，假如在 $$\theta$$ 方向的发射信号进来，则接收的信号，乘以上面的共轭之后有：

$$
e^{j0(\psi-\psi_0)},\space e^{j1(\psi-\psi_0)},e^{j2(\psi-\psi_0)},\cdots,e^{j(N-1)(\psi-\psi_0)}
$$

则最终接收的信号为：

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk(\psi-\psi_0)} =  \frac{1}{N}\sum_{k=0}^{N-1} e^{jk(\psi-\psi_0)}
$$

按照等比数列求和有(可以参考后面附录）：

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk(\psi-\psi_0)} =
\begin{cases}
	|\frac{sin(N(\psi-\psi_0)/2)}{N sin((\psi-\psi_0)/2)}| & \text{ if } \psi-\psi_0 \neq 0  \\
	1 & \text{ if } \psi-\psi_0 = 0
\end{cases}
$$

下面以 $$\theta_0 = \pi/6$$  为例，让$$\theta \in [0,2\pi]$$ ，在极坐标上画出来信号强度的图：

![bf_pointedt_to_somewhere.png](/figure/beamforming/bf_pointedt_to_somewhere.png)

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：B\_different\_antenna\_with\_different\_signal.py

附录：

$$
\begin{aligned}
\frac{1}{N}\sum_{i=0}^{N-1} e^{jk\psi}  
&=\frac{1}{N}\frac{1-e^{jN\psi}}{1-e^{j\psi}} \\
&= \frac{1}{N}\frac{e^{jN\psi/2}}{e^{j\psi/2}}  \frac{(e^{-jN\psi/2}-e^{jN\psi/2})}{(e^{-j\psi/2}-e^{j\psi/2})} \\
&=  \frac{e^{jN\psi/2}}{e^{j\psi/2}} \frac{sin(N\psi/2)}{Nsin(\psi/2)}
\end{aligned}
$$

若只考虑幅度，则上面推导中最后的两个分式中，第一个分式的模是 1，可以忽略。

$$
\begin{vmatrix}
	\frac{1}{N}\sum_{k=0}^{N-1} e^{jk\psi}
\end{vmatrix}
=
\begin{vmatrix}
	\frac{sin(N\psi/2)}{Nsin(\psi/2)}  
\end{vmatrix}
$$

## 波束赋形(beamforming)的数学推导（五）- 天线间距半波长以及角域空间
### 天线间距半波长
这个小文章，我们仅仅从发射天线的角度来分析，通过前面四个小文章的分析，从接收天线的角度来分析，原理是一样的。

如果我们指定了一个方向，则在各个方向上的接收天线，能收到的能量满足：

$$
|G( \psi)| = 
\begin{cases}                 
	|\frac{sin(N\psi/2)}{Nsin(\psi/2)}|          & \text{ if }  \psi \neq 0    \\  
	1                                                              & \text{ if }  \psi = 0 
\end{cases}     \tag{1}
$$

其中：

$$
\psi = \frac{ 2\pi d }{ \lambda } cos(\theta)  -  
\frac{ 2\pi d }{ \lambda } cos(\theta_0)
$$

$$\theta_0$$ 表示我们想指向的方向， $$\theta$$ 是一个变的量，遍历整个 $$-\pi$$ 到 $$\pi$$ 的这样的一圈。
为了简单起见，我们不妨设 $$\theta_0 = \pi/2$$，则：

$$
\psi = \frac{ 2\pi d }{ \lambda} cos(\theta)  -  
\frac{ 2\pi d }{ \lambda } cos(\theta_0) =\frac{ 2\pi d}{ \lambda } cos(\theta) \tag{2}
$$

为了只有一个指定的波束方向(这里应该理解为数学上的极大值点，应该保证只有一个波束极大值点)，则 $$\psi/2$$ 应该介于 $$[-\pi/2,\pi/2]$$ 之间，即 $$\frac{ 2\pi d }{ \lambda  } cos(\theta)$$ 在$$[-\pi,\pi]$$ 之间，所以

$$
\frac{d}{\lambda } \le \frac{1}{2}
$$

我们来画图感受一下，如果 

$$
\frac{d}{\lambda} = 1> \frac{1}{2}
$$

那么

$$
\psi =\frac{ 2\pi d }{ \lambda  } cos(\theta) \in [-2\pi,2\pi]
$$

则，我们对公式 1 ，按照上式来画出幅度：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：c\_antenna\_distance\_half\_lambda\_wavelength.py

![幅度](/figure/beamforming/minus_2pi_2pi_psi.png)

*幅度*


在 $$[-\pi,\pi]$$ 之间只有一个最高点，在这之外，极大值点开始逐渐增大，即在另外一些方向上，也有较大的能量辐射到那个方向上。

所以，当 $$\frac{d}{\lambda}$$ 从 0.5 逐渐增大 1 的过程中，可以看到多出来的指向逐渐显现出来：

![d_vs_lambda_0.5.png](/figure/beamforming/d_vs_lambda_0.5.png)

![d_vs_lambda_0.6.png](/figure/beamforming/d_vs_lambda_0.6.png)

![d_vs_lambda_0.7.png](/figure/beamforming/d_vs_lambda_0.7.png)

![d_vs_lambda_0.8.png](/figure/beamforming/d_vs_lambda_0.8.png)

![Figure_0.9.png](/figure/beamforming/Figure_0.9.png)

![d_vs_lambda_1.0.png](/figure/beamforming/d_vs_lambda_1.0.png)

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：d\_antenna\_distance\_different\_lambda\_wavelength.py







### 角域空间

假设有 M 根发射天线，则每个发射角，我们都可以对应一个向量：

$$
[e^{j0\psi} \quad e^{j2\psi}  \quad \cdots \quad  e^{j(M-1)\psi}] \quad \quad ------ 公式(3)
$$

我们知道，上面这个是一个 M 维的向量，可以认为是 M 维空间上的一个向量，由于角度的选取有无穷多个，则可以产生无穷多个 向量，既然是 M 维空间，我们应该可以找到 M 个正交向量，构成一个基，其它所有向量都可以用这个基中的 M 个向量线性组合来生成。

那，问题是，我们能保证找到 M 个正交向量吗？当然，如果没有任何限制，那 M 维空间一定有 M 个正交向量构成一个基，但是，如果我们对正交向量加了约束条件，不能任意选择，那就未必能找到。

我们加的条件是，形如公式 (3) 的向量，$$\psi$$ 取不同的值，就得到不同的向量。在这样的条件下，两个向量正交，需要两个 $$\psi$$ 的取值，相差非 0 整数倍的 $$2\pi/M$$. 可以证明（见附录），这样的两个向量是正交的。

如果第一个向量，我们取

$$
\psi = 0
$$

第二个向量取

$$
\psi = \frac{2\pi}{M}
$$

依此类推，最后一个向量取

$$
\psi = \frac{2\pi}{M} * (M-1)
$$

所以，$$\psi$$ 取值范围要大于等于 $$2\pi$$，否则，就拿不到 M 个正交向量。

从公式 (2) 可以看到，$$\psi$$ 的范围宽度为

$$
\frac{ 2\pi d}{ \lambda }*2
$$

那么：

$$
\frac{ 2\pi d}{ \lambda }*2 \ge 2\pi
$$

则：

$$
\frac{d}{\lambda} \ge  \frac{1}{2}
$$

综合以上的推导，则：

$$
\frac{d}{\lambda} =  \frac{1}{2}
$$

而且 M 个正交向量分别为：

$$
\begin{aligned}
	[e^{j0*\frac{2\pi}{M}*0} \quad e^{j0*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j0*\frac{2\pi}{M}*(M-1)}]  \\ 
	[e^{j1*\frac{2\pi}{M}*0} \quad e^{j1*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j1*\frac{2\pi}{M}*(M-1)}]  \\
	[e^{j2*\frac{2\pi}{M}*0} \quad e^{j2*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j2*\frac{2\pi}{M}*(M-1)}]  \\
	\cdots  \\
	[e^{jk*\frac{2\pi}{M}*0} \quad e^{jk*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{jk*\frac{2\pi}{M}*(M-1)}]  \\
	\cdots  \\
	[e^{j(M-1)*\frac{2\pi}{M}*0} \quad e^{j(M-1)*\frac{2\pi}{M}*1}  \quad \cdots \quad  e^{j(M-1)*\frac{2\pi}{M}*(M-1)}]  \\
\end{aligned}
$$

这样得到了由 M 个正交向量组成的一个基。

如果 

$$
\frac{d}{\lambda} <  \frac{1}{2}
$$

那么就不能构成 M 维空间，就会变成 M 维空间的子空间（这里有点抽象，我也不知道该怎么说得能更清楚），从“指哪打哪” 的角度来理解，就不能做到很精确的 "指哪打哪".

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为： e\_antenna\_distance\_different\_lambda\_wavelength\_2.py



修改程序中变量  d vs lambda 的值，从 0.5 逐渐减小，看是什么效果：

![0.5](/figure/beamforming/kk_0.5.png)

*0.5*

![0.1](/figure/beamforming/kk_0.1.png)

*0.1*

![0.05](/figure/beamforming/kk_0.05.png)

*0.05*

![0.03](/figure/beamforming/kk_0.03.png)

*0.03*

![0.02](/figure/beamforming/kk_0.02.png)

*0.02*


![0.01](/figure/beamforming/kk_0.01.png)

*0.01*


![0.005](/figure/beamforming/kk_0.005.png)

*0.005*




### 附录：证明基正交

$$
\sum_{n=0}^{M-1}  e^{-j\psi n} e^{j(\psi+\frac{2\pi}{M}k) n} =\sum_{n=0}^{M-1} e^{j\frac{2\pi}{M}k n}
$$

可以认为是频率为 $$\frac{2\pi}{M}k$$，在一个周期内求和，结果就是 0.

## beam指向-用直观代码实现
这个小短文，把 beam 的指向用一个 python 小程序来演示一下。之前文章中的代码，是使用了等比数列求和的最终公式来实现的，比较简洁，也便于做理论分析。

$$
\frac{1}{N}\sum_{k=0}^{N-1} e^{jk \psi} =
\begin{cases}
	|\frac{sin(N \psi /2)}{N sin(\psi/2)}| & \text{ if } \psi \neq 0  \\
	1 & \text{ if } \psi = 0
\end{cases}
$$

只是直接看那个公式，如果不是很熟悉推导过程，直接看代码会有点不好理解，下面以竖向排列的一列天线作为例子。

极坐标系选择以向下的方向为 0 度。
如下图所示：

![beam指向-用直观代码实现-极坐标0度指向.png](/figure/beamforming/beam指向-用直观代码实现-极坐标0度指向.png)

以向下方向为 0 度，从天线指向 UE 的夹角，定义为 beamAngle

![beam指向-用直观代码实现-beam角度.png](/figure/beamforming/beam指向-用直观代码实现-beam角度.png)

则最下面那根天线发送信号 s，则倒数第二根发射的信号为：

$$
s e^{-j 2\pi \frac{d}{\lambda} cos(beamAngle)}
$$

如果把 

$$
2\pi \frac{d}{\lambda} cos(beamAngle) = \psi
$$

则从下往上，没根天线上发射的信号依次为：

$$
s  e^{-j0\psi}, s e^{-j1\psi}, s e^{-j2\psi}, s e^{-j3\psi}\cdots , s e^{-j(N-1)\psi}  \tag{1}
$$

现在依然以上面的坐标系为参考，假设接收端在角度为 $$\theta$$ 的位置，如下图所示：

![beam指向-用直观代码实现-接收方向.png](/figure/beamforming/beam指向-用直观代码实现-接收方向.png)

那么从下往上，各个天线上发射的信号，当到达接收端时不同天线间的相位延迟分别为：

$$
e^{j0\phi} , e^{j1\phi}, e^{j2\phi}, e^{j3\phi}\cdots , e^{j(N-1)\phi}  \tag{2}
$$

其中：

$$
\phi = 2 \pi \frac{d}{\lambda} cos(\theta)
$$

则最终接收端接收到的信号为：

$$
s \left (e^{j0(\phi-\psi)}+e^{j1(\phi-\psi)}+s  e^{j2(\phi-\psi)}+s  e^{j3(\phi-\psi)},\cdots,  e^{j(N-1)(\phi-\psi)} \right )
$$

如果把

$$
\left [e^{-j0\psi}, e^{-j1\psi}, e^{-j2\psi}, e^{-j3\psi}\cdots , e^{-j(N-1)\psi} \right ] = V_{\phi}
$$

且

$$
\left [ e^{j0\phi} , e^{j1\phi}, e^{j2\phi}, e^{j3\phi}\cdots , e^{j(N-1)\phi}  \right ] = V_{\phi}
$$

则信号的增益就是向量 $$V_{\psi}$$ 和 $$V_{\phi}$$ 的 点积（注意：不是复数内积，复数内积的定义是对第二个向量取共轭，这里对两个向量都不取共轭).


(似乎在 pdf 上无法显示一个 gif 动画, 需要看的朋友需要到网页上去看：\url{https://www.bilibili.com/read/cv27615862/}

latex 版本的，直接在这个目录下可以看：img//beamforming//beam指向-用直观代码实现-极坐标图-动画.gif）

分解的图：



![beam指向-用直观代码实现-极坐标图 0.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 0.png)


![beam指向-用直观代码实现-极坐标图 1.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 1.png)

![beam指向-用直观代码实现-极坐标图 2.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 2.png)

![beam指向-用直观代码实现-极坐标图 3.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 3.png)

![beam指向-用直观代码实现-极坐标图 4.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 4.png)

![beam指向-用直观代码实现-极坐标图 5.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 5.png)

![beam指向-用直观代码实现-极坐标图 6.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 6.png)

![beam指向-用直观代码实现-极坐标图 7.png](/figure/beamforming/beam指向-用直观代码实现-极坐标图 7.png)

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：f\_antenna\_distance\_using\_inner\_product.py

## 半波长大于0.5的效果分析
在前面的文章中，分析了天线间距是半波长的必要性，但是在实际系统中，有时候用例如 0.7波长作为间距，这个文章我们就来讨论一下天线间距大于半波长的情况。



下面这个小 python 程序，画出了两个天线发出的波形，这里只是画出了指向某一个方向的，实际上因为是全向天线，四周都有这个波形的：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：g\_antenna\_distance\_greater\_than\_0.5.py



![半波长大于0.5的效果分析_01.png](/figure/beamforming/半波长大于0.5的效果分析_01.png)

$$
2 \pi \frac{d cos \theta}{\lambda}
$$

这个实际上对应的就是在 d 这么长的距离内，其波形相位的变化，其中：

$$
\frac{d cos \theta}{\lambda}
$$

可以理解为是占一个周期的比例，一个周期按照 $$2 \pi$$  来考虑。



下面我们来看一下，当  UE 所在的方向是 45度，或者说我们来考查一下 45 度方向的波束情况，能量大小。

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

文件名为：h\_antenna\_distance\_greater\_than\_0.5\_45degree.py



![半波长大于0.5的效果分析_02_PI_over_4.png](/figure/beamforming/半波长大于0.5的效果分析_02_PI_over_4.png)

![半波长大于0.5的效果分析_03_PI_over_4_and_3_PI_over_4.png](/figure/beamforming/半波长大于0.5的效果分析_03_PI_over_4_and_3_PI_over_4.png)

当增加一个 $$pi$$ 的相位旋转，则在两个角度上都有波束：

## 波束的几种类型浅析
N 个天线（假定是线阵天线）构成的角域空间，可以用傅里叶变换中的各个向量，作为基中的坐标，用基中的这些向量，可以线性组合出所有的 beam。 但是，需要注意的是，在这个角域空间中，有多种不同类型的 beam:

(1) 基中的向量表示的 beam:
其向量的数学表示为：

$$
v_{l,m}= \begin{bmatrix}
	1\\
	e^{j\frac{2\pi k}{N}}\\
	\cdots  \\
	e^{j\frac{2\pi k}{N}(N-1)}\\
\end{bmatrix}
$$

其中 k 的取值为 0,1,2,...,N-1.

这类 beam，可以看成是单波束的，虽然有一些旁瓣。

（用附录一的代码画出来的）


![2023-11-28-波束的几种类型浅析--k是整数的情况.png](/figure/beamforming/2023-11-28-波束的几种类型浅析--k是整数的情况.png)

(2) 非基中的向量，但是是单波束的
其向量的数学表示为：

$$
v_{l,m}= \begin{bmatrix}
	1\\
	e^{j\frac{2\pi k}{N}}\\
	\cdots  \\
	e^{j\frac{2\pi k}{N}(N-1)}\\
\end{bmatrix}
$$

其中 k 的取值为：除了0,1,2,...,N-1之外的实数，当然因为是周期的，所以我们只考虑 k 大于等于 0 小于等于 N-1 的情况。

这类 beam，可以也看成是单波束的，虽然有一些旁瓣。

（附录二的代码画出来的）

![2023-11-28-波束的几种类型浅析--k是小数的情况.png](/figure/beamforming/2023-11-28-波束的几种类型浅析--k是小数的情况.png) 
(3) 除了以上两种情况
其向量的数学表示为：

$$
v_{l,m}= \begin{bmatrix}
	1\\
	e^{j\alpha_1}\\
	\cdots  \\
	e^{j\alpha_{N-1}}\\
\end{bmatrix}
$$

其中的 $$\alpha_1,\cdots \alpha_{N-1}$$ 可以是任何相位值。
这类波束就不是单波束的,可能有多个主瓣.

（附录三的代码画出来的）


![2023-11-28-波束的几种类型浅析--随机相位的情况.png](/figure/beamforming/2023-11-28-波束的几种类型浅析--随机相位的情况.png) 

在文献 [1] 中的第 6.1.1 节，把 beam 分成了两类，一类是 classical 的，一类的 generalized，前者是在 line-of-sight 或者 free-space 中做理论推导的，就是直接用各个电线发射的电子波，在指向的方向上平行射出直接到接收端，走的距离不同导致的相位偏差，然后在有些方向上加强，有些方向上衰减；但是，对于多径的情况，有较大的多径时延的时候，就用 generalized 的类型。在我这个文章的分类中，第一和第二类可以看成是文献 [1] 中说的 classical 的，第三类是文献 [1] 中说的 generalized 类别。



[1]《Advanced Antenna Systems for 5G Network Deployments: Bridging the Gap Between Theory and Practice》 by Asplund, H. and Astely, D. and von Butovitsch, P. and Chapman, T. and Frenne, M. and Ghasemzadeh, F. and Hagstrom, M. and Hogan, B. and Jongren, G. and Karlsson, J. and others
ISBN：9780128200469


附录一：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

i\_beam\_type\_a.py


附录二：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

i\_beam\_type\_b.py

附录三：

代码请到 github 下载：\url{https://github.com/taichiorange/leba_math}

i\_beam\_type\_c.py

## MIMO 波束增强的解释及其 EIRP

我们先考虑一根发射天线，在某处有一根接收天线，假设从这个发射天线过来到达这个接收天线的能量是 $$P_1$$。
如果把这根发射天线移动半个波长位置后发送相同功率的电磁波，那么，假如在接收天线接收到的能量是 $$P_2$$.
若两根同样的发射天线，相隔半个波长摆放，则同样位置的接收天线，收到的能量很大可能不是 $$P_1 + P_2$$，直观上似乎不好理解。

假如接收天线所处的位置，刚好是从两个电磁波来的电磁波是同相位的，那么若单发射天线发射，单天线在此位置接收的能量是 $$P$$ 的话，则同相位接收后的能量是 $$4P$$，而不是直观理解的 $$2P$$.

这里因为是波的能量，所以，要考虑两个波的叠加，是震动幅度的叠加（更严谨地讲，是场强的叠加），而不是能量的叠加，因此，因为震动幅度加倍，则能量变成4倍。

而如果是完全相互抵消的地方，相位完全相反，能量就是 0.

从能量守恒的角度看，相互抵消的地方，能量并不是平白无故消失了，而是通过波转移到了相互增强的地方。

因此，接收天线的功率，可以表示为（用dB表示）

$$
P_t - P_L + G_{\text{array}}
$$

其中 $$P_t$$ 是总发射功率，假设总功率受限且平均分配给各阵元，$$P_L$$ 是路径衰落或者损耗，而 $$G_{\text{array}}$$ 就是多天线增益，在两天线的情况下，是 $$10\operatorname{log}_{10}2 = 3\text{dB}$$。

而在完全抵消的地方，这个天线增益为 $$-\infty$$,即$$10\operatorname{log}_{10}0$$.

N 根天线的情况，总的原始能量是 $$NP$$，而阵列天线情况下收到的能量为$$(N\sqrt{P})^2 = N^2 P$$，所以增益为：

$$
G_{\text{array}} = \frac{N^2P}{NP} = N
$$

因此，用 dB 来表示就是：

$$
G_{\text{array}} = 10 \operatorname{log}_{10} N
$$

这里提一下 EIRP 这个术语：
EIRP: Effective/Equivalent Isotropic Radiated Power.

因为多天线 MIMO 系统，在某个点的接收能量，如何要换成一个单全向天线，需要多大的发射能量才能在这个点接收到，这个值是 $$P_t + G_{\text{array}}$$，单位都是 dB.