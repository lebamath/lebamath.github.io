<head>
  <script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>

  <!-- Google tag (gtag.js) -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-V3E68S0VG4"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
  
    gtag('config', 'G-V3E68S0VG4');
  </script>
</head>


[.返回主目录](/chinese_version.html) 


# 天线阵列的射频波入射角公式 AoA: Angle of Arrival

[录制的视频在 B 站](https://www.bilibili.com/cheese/play/ep2791553 "点击前往 B 站课程")


## 传统方法分析相位差

MIMO 天线阵列，假定是线阵，并且天线要接收或者发给的数据在离天线阵列很远的地方，因此，可以假设射频波是平行着进入到天线阵列的各个天线，或者是平行着从天线阵列的各个天线发射到接收方。

假设天线阵列（线阵）与射频波之间的夹角为 $$\theta$$, 天线间距是 $$d$$, 那么如图 1 所示，天线之间的相位差就是 
$$
\frac{2\pi}{\lambda} d \operatorname{cos}(\theta)
\tag{1}
$$

![图1：距离向射频波方向投影](/figure//mimo//AoA/AoA_distance_projection.png)

*图1：距离向射频波方向投影*

其中 $$d \operatorname{cos}(\theta)$$ 是天线间距到射频波方向上的投影长度。

上面是一种非常直观的示意图和解释。这个分析是从天线间距向射频波方向上的垂直投影，这个投影是对距离的投影。


### 空间角速度投影

我们把公式 (1) 从另外一个角度来看一下，调整一下顺序，写成如下形式：

$$
\frac{2\pi}{\lambda} \operatorname{cos}(\theta)d
$$

其中 $$2\pi/\lambda$$ 可以看成是空间角速度，即相位角随距离的变化速度。

再乘以 $$\operatorname{cos}(\theta)$$后，可以看成是空间角速度向线阵方向的投影。

这里要特别注意，不是距离在线阵方向的投影，而是空间角速度，空间角速度是一个矢量，带方向的，所以，可以向线阵方向投影。在线阵方向，可以看成波形被延展了，波长变为:
$$\lambda/\operatorname{cos}(\theta)$$.

公式 (1) 可以写成：
$$
\frac{2\pi}{\lambda/\operatorname{cos}(\theta)} d
$$

这个投影的示意图如图 2 所示。

![图2：空间角速度向天线阵列方向上投影](/figure//mimo//AoA//AoA_angle_space_velocity_projection.png)

*图2：空间角速度向天线阵列方向上投影*

总之，就是把空间角速度可以向线阵方向投影。用这种方法，比较好做空间投影分析，就不用画空间平行的发射波，然后分析其相位差，当然，本质上是用平行的发射波来分析相位差。


## 平面阵列下的到达角AoA分析

在平面阵列情况下计算 AoA 角度的时候，一般是使用天平角(方位角，Azimuth)和天顶角(Zenith)，如图 3 所示。

![图3：平面阵列上的空间角速度投影/方位角/天顶角](/figure//mimo//AoA//AoA_3D_projection.png)

*图3：平面阵列上的空间角速度投影/方位角/天顶角*

**天顶角：**对于天顶角 $$\theta$$，就是看平面阵列的垂直方向(vertical), 用上面两种方法都可以分析出对应的公式，用空间角速度更容易推导。空间角速度是:$$2\pi/\lambda$$。

经过对接收的信号进行分析，可以得到相邻两个天线之间的相位差是 $$\tilde{\theta}$$，则：
$$
\tilde{\theta} = \frac{2\pi}{\lambda}  \operatorname{cos}(\theta) d_{\text v}
$$

可以推导出$$\theta$$ 的表达式：
$$
\theta = \operatorname{cos}^{-1}\left (\frac{\tilde{\theta}}{2\pi} \cdot \frac{\lambda}{d_\text{v}} \right )
$$

其中 $$d_{\text v}$$ 是垂直方向天线间距。

**方位角：** 对于方位角，在知道天顶角后，并且已经通过接收到的信号估计出来了水平方向两个天线上的相位差 $$\tilde{\phi}$$。

根据空间角频率的投影，直接向天线水平方向投影，可以通过两步投影来计算：先投影到 xy  平面，再在 xy 平面上向 x 轴上投影。

投影到 xy 平面：

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)
$$

再向 x 轴投影：

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)\operatorname{sin}(\phi)
$$

则方程为：

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)\operatorname{sin}(\phi) d_{\text h} = \tilde{\phi}
$$

其中 $$d_{\text h}$$ 是水平方向天线间距。

进一步推导，可以得到 $$\phi$$ 的表达式：

$$
\phi = \operatorname{sin}^{-1}\left ( \frac{\tilde{\phi}}{2\pi} \cdot \frac{\lambda}{d_{\text h} \operatorname{sin}(\theta)} \right )
\tag{2}
$$

**不用方位角：** 如果第二个角用射频波相对于x 轴的夹角 $$\gamma$$，那么

$$
\frac{2\pi}{\lambda} \operatorname{cos}(\gamma) d_{\text h} = \tilde{\phi}
$$

则可以求解出 $$\gamma$$ 的表达式：

$$
\gamma = \operatorname{cos}^{-1}\left (\frac{\tilde{\phi}}{2\pi} \cdot \frac{\lambda}{d_\text{h}} \right )
\tag{3}
$$

对比公式 (2) 和 (3)，因为求的角度不同，公式中分母部分分别包含的是 $$d_{\text h} \operatorname{sin}(\theta)$$ 和 $$d_{\text h}$$。

