# 预编码

录制的视频在[B站](https://www.bilibili.com/cheese/play/ss411970303)

# MIMO 信道的理解-从特征波束的角度


设信道矩阵$$\mathbf H$$ 是 $$N_{\text t} \times N_{\text r}$$维的，则传输模型可以写为：

$$
\mathbf y = \mathbf H^{\text T} \mathbf x + \mathbf n
$$

为了方便描述，这里假设 $$N_{\text t} >= N_{\text r}$$，信道矩阵$$\mathbf H$$的秩是 $$L < N_{\text r}$$。

稍微需要说明一下的是这里的信道矩阵需要转置之后才用到传输模型中，这是为了考虑之后多用户 MIMO(MU-MIMO) 的场景，矩阵 $$\mathbf H$$ 的列，是与用户相对应的，假如某个用户是单天线的，那么这个用户的信道矩阵（向量）就是 $$\mathbf H$$  中的一列，这样表示的时候，向量就都是列向量，这与线性代数中的约定保持一致，即向量都缺省认为是列向量。$$\mathbf H$$ 矩阵就可以用一组列向量来表示：

$$
\mathbf H = \begin{bmatrix}
		\mathbf h_1 & \cdots & \mathbf h_{N_{\text r}}
	\end{bmatrix}
$$

对信道矩阵做 SVD 分解：

$$
\begin{aligned}
		\mathbf H^{\text T} &= \mathbf U \mathbf \Sigma \mathbf V^{\text H} = \begin{bmatrix}
			\mathbf u_1,\cdots,\mathbf u_{N_{\text r}}
		\end{bmatrix}
		\begin{bmatrix}
			\sigma_1 & \cdots& 0 & \cdots& 0\\
			\vdots & \ddots & \vdots & \ddots & \vdots\\
			0 & \cdots & \sigma_L & \cdots & 0 \\
			\vdots & \ddots & \vdots & \ddots & \vdots \\
			0 & \cdots & 0 & \cdots & 0 \\
		\end{bmatrix}
		\begin{bmatrix}
			\mathbf v_1^{\text H} \\
			\vdots \\
			\mathbf v_{N_\text t}^{\text H}
		\end{bmatrix}  \\
		&= \mathbf u_1 \sigma_1 \mathbf v_1^{\text H} + \cdots + \mathbf u_L \sigma_L \mathbf v_L^{\text H}
	\end{aligned}
$$

现在来分析一下其中任何一项 $$\mathbf u_i \sigma_i \mathbf v_i^{\text H}$$，可以看到这里的 $$\mathbf u$$  和 $$\mathbf v$$ 是成对出现的。而且所有的 $$\mathbf u$$ 都是正交的，所有的 $$\mathbf v$$ 都是正交的，所以，当信号 $$\mathbf x$$ 通过这个信道时，信道的作用过程就变成：

$$
\mathbf y =  \mathbf u_1 \sigma_1 \mathbf v_1^{\text H} \mathbf x + \cdots + \mathbf u_L \sigma_L \mathbf v_L^{\text H} \mathbf x + \mathbf n
$$

其中的任何一项 $$\mathbf u_i \sigma_i \mathbf v_i^{\text H} \mathbf x$$，首先信号 $$\mathbf x$$ 向向量 $$\mathbf v_i^{\text H}$$ 做了投影，即在这个向量$$\mathbf v_i^{\text H}$$对应的坐标轴上的投影（复数），这个投影被 $$\sigma_i$$ 缩放，然后作用在 $$\mathbf u_i$$ 这个向量上。

可以看到 $$\mathbf x$$ 投影到第$$i$$ 个向量 $$\mathbf v_i^{\text H}$$，只能走 $
\mathbf u_i$ 这个方向。

$$\{\mathbf v_i,i=1,\cdots,N_{\text t}\}$$ 是从发射端/发射天线看到的波束形状，也就是说，发送方应该把信道打到  $$\{\mathbf v_i,i=1,\cdots,L\}$$ 这些方向，才有可能被接收到，否则会完全接收不到。例如将发射信道打到 $$\mathbf v_{L+1}$$ 的方向， 无论接收方多么努力，都无法收到任何信号。

类似的，$$\{\mathbf u_i,i=1,\cdots,N_{\text r}\}$$ 是从接收端/接收天线看到的波束形状，也就是说，接收方应该尝试在  $$\{\mathbf u_i,i=1,\cdots,L\}$$ 这些方向来接收信号，也就是调整接收波束的方向指向这些方向，才有可能接收到发来的信号，否则会完全接收不到。例如将接收波束方向指向 $$\mathbf u_{L+1}$$ 的方向， 无论发送方多么努力，接收方都无法收到任何信号。再如，接收端将接收波束指向 $$\mathbf u_1$$，即用 $$\mathbf u_1^{\text H}$$ 作用到接收信号 $$\mathbf y$$ 上有：

$$
\begin{aligned}
		\mathbf u_1^{\text H}\mathbf y &=  \mathbf u_1^{\text H} \left (\mathbf u_1 \sigma_1 \mathbf v_1^{\text H} \mathbf x + \cdots + \mathbf u_L \sigma_L \mathbf v_L^{\text H} \mathbf x + \mathbf n \right )  \\
		&= \mathbf u_1^{\text H} \mathbf u_1 \sigma_1 \mathbf v_1^{\text H} \mathbf x + \cdots + \mathbf u_1^{\text H}\mathbf u_L \sigma_L \mathbf v_L^{\text H} \mathbf x + \mathbf u_1^{\text H} \mathbf n \\
		&= \sigma_1 \mathbf v_1^{\text H} \mathbf x + \mathbf u_1^{\text H} \mathbf n
	\end{aligned}
$$

这样就是 $$\mathbf u_1$$ 这个方向来的信号被接收到，当然，这个方向来的信号，也是需要发送方在这个发送波束方向 $$\mathbf v_1$$上发送才能被收到。

所以，将$$\mathbf V$$ 的各列称之为发射特征波束，$$\mathbf U$$ 的各列称之为接收特征波束，将一对特征波束 $$(\mathbf v_k, \mathbf u_k)$$ 称为第 $$k$$ 个特征波束对。这些定义，有助于后续分析信道的波束域表示。

![图1：非直射经(NLOS)，两条主径](/figure/mimo_precoder/linear/H_singular_vector_1.png)

*图1：非直射经(NLOS)，两条主径*

![图2：直射径 LOS](/figure/mimo_precoder/linear/H_singular_vector_2.png)

*图2：直射径 LOS*

图 1 演示了非直射情况的两条主路径，从左侧发射端看，对应的是波束 $$\mathbf v_1$$ 和 $$\mathbf v_2$$，然后分别在接收端被 $$\mathbf u_1$$ 和 $$\mathbf u_2$$的波束接收到。所以，发射端要以对齐$$\mathbf v_1$$ 和 $$\mathbf v_2$$这两个波束的方式来发射，而接收端如果想接收到信号，则需要以对齐$$\mathbf u_1$$ 和 $$\mathbf u_2$$这两个波束来接收。

图2这种  LOS 直射径，将看起来更清晰。当然图中画得比较近，要考虑发射端和接收端离得很远，所以，不同天线的入射和出射波是彼此近似平行的。



# 预编码及其均衡，以及信道估计

从前面对信道矩阵的分析，我们知道信道可以分解成多个特征波束对，每一个特征波束对都可以用来传递数据，这些特征波束对是可以分离的，即这些数据是可以在接收端被分别识别出来，独立出来，分开出来。

而特征波束对的个数就是矩阵的秩,这里记为 $$L$$，是有可能比接收和发送天线($$N_{\text t}, N_{\text r}$$)数都少，那么预编码就是要把 $$L$$ 这个数据如何打到 $$N_{\text t}$$ 根发射天线上，并有可能需要关注在接收端如何更能方便地提取数据。

在实际的通信系统中，一般接收方是不知道发送方具体用的是什么预编码矩阵，因此，需要一个方法来把预编码矩阵及其实际物理信道估计出来。下面我们以 $$L=2, N_{\text t}=4, N_{\text r}=3$$ 为例子进行讲解。

由于 $$L=2$$ ，所以，有两个特征波束对 $$(\mathbf v_1,\mathbf u_1)$$ 和  $$(\mathbf v_2,\mathbf u_2)$$,可以发送两个数据  $$s_1,s_2$$.

如图 3 所示，

![图3：预编码/物理信道/均衡/参考信号](/figure/mimo_precoder/linear/precoder_H_equalizer_referenceSignal.png)

*图3：预编码/物理信道/均衡/参考信号*

信道模型为：

$$
\mathbf y = \mathbf H^{\text T} \mathbf F \mathbf s + \mathbf n
$$

其中 $$\mathbf H$$ 是4行3列，即 $$4 \times 3$$ 维矩阵，$$\mathbf F$$ 是 $$4\times 2$$ 矩阵，均衡矩阵$$G$$ 是 $$2\times 3$$ 矩阵，线性均衡后的数据为：

$$
\hat{\mathbf s} = \mathbf G \mathbf y = \mathbf G \mathbf H^{\text T} \mathbf F \mathbf s + \mathbf G \mathbf n
$$

为了能根据一些算法，例如 MMSE 或者 Zero Forcing 算法，做线性均衡，得首先知道等效信道，即从 $$\mathbf s \to \mathbf y$$ 这个信道 $$\mathbf H^{\text T} \mathbf F$$，而接收端一般是不知道发射端用了什么预编码，所以，需要发射端在每个数据流上插入参考信号(例如 5G 上叫 DMRS)，当然，需要在不同时间或者不同载波上发送参考信号，数据安排在其它时间或者频率上。

每一层至少要有一个参考信号，所以，如图所示，最左边绿色的参考信号，就是能让接收端估计出等效信道 $$\mathbf H_{\text{eff}} = \mathbf H^{\text T} \mathbf F$$，是一个 $$3 \times 2$$ 的等效信道的矩阵。

那么最简单的均衡就是匹配滤波 $$\mathbf G = \mathbf H_{\text{eff}}^{\text H}$$，这个矩阵是 $$2 \times 3$$ 的矩阵。因此，可以从 3 个接收数据元素的向量 $$\mathbf y$$ 均衡出发射数据：$$\hat {\mathbf s} = \mathbf G \mathbf y$$，这是含有两个元素的列向量。

# 线性预编码之迫零预编码

本节中，信道传输模型采用的是 

$$
\mathbf y = \mathbf H \mathbf F \mathbf s + \mathbf n
$$


而不采用对 $$\mathbf H$$ 做了转置的信道传输模型 $$\mathbf y = \mathbf H^{\text T} \mathbf F \mathbf s + \mathbf n$$，这是为了方便在本节做公式推导。

这里信道矩阵 $$\mathbf H$$ 是 $$N_\text r \times N_\text t$$ 维度的，且假定 $$N_\text r <= N_\text t$$，并且是行满秩的，即 $$\operatorname{rank}(\mathbf H) = N_{\text r}$$. 

迫零预编码的目标是使得干扰完全消除，即：

$$
\mathbf H \mathbf F = \operatorname{diag}\{\sqrt{\mathbf P}\}, \quad
	\sqrt{\mathbf P} = \begin{bmatrix}
		\sqrt{p_1} & \cdots & \sqrt{p_{N_\text r}}
	\end{bmatrix}^{\text T}
$$

其中 $$p_i$$ 表示分配给第 $$i$$ 层的功率。如果将功率分配纳入到$$\mathbf F$$ 中，那么我们要找的就是 $$\mathbf H \mathbf F = \mathbf I_{N_{\text r}}$$.

可以看到 $$\mathbf F$$ 就是 $$\mathbf H$$ 的逆矩阵，但是，由于 $$\mathbf H$$ 不是方阵，因此，我们只能求 $$\mathbf H$$ 的广义逆矩阵，根据线性代数的定义，这个广义逆矩阵(记为 $$\mathbf H^{-}$$)满足：

$$
\mathbf H \mathbf H^{-} \mathbf H = \mathbf H
$$

如果矩阵是一个矮胖的矩阵，且行向量线性无关，则：

$$
\mathbf H \mathbf H^{-} = \mathbf I_{N_\text r}
$$

这样的广义逆矩阵可以写成两部分：

$$
\mathbf H^{-} = \mathbf{H}^{\dagger} + \mathbf{P}_{\perp} \mathbf{A}
\tag{1}
$$

其中 $$\mathbf{H}^{\dagger}$$ 是伪逆矩阵：$$\mathbf{H}^{\dagger} = \mathbf H^{\text H} ( \mathbf H \mathbf H^{\text H})^{-1}$$,因为 $$\mathbf H$$ 是行满秩，因此 $$\mathbf H\mathbf{H}^{\dagger} = \mathbf I_{N_{\text r}}$$；$$\mathbf{P}_{\perp}$$ 是向 $$\mathbf H$$的行向量构成的的零空间(Null Space)进行投影的矩阵： $$\mathbf{P}_{\perp} = \mathbf I -\mathbf{H}^{\dagger}\mathbf{H}$$, 这里的$$\mathbf{H}^{\dagger} \mathbf{H}$$ 其实是向 $$\mathbf H$$ 的行向量构成的子空间上投影。 $$\mathbf A$$ 是任何 $$N_{\text r} \times N_{\text t}$$


现在来证明一下公式(1) 这个广义逆矩阵，确实是一个逆矩阵：

$$
\begin{aligned}
		\mathbf H \mathbf H^{-} &= \mathbf H (\mathbf{H}^{\dagger} + \mathbf{P}_{\perp} \mathbf{A}) \\
		&= \mathbf H (\mathbf{H}^{\dagger} + \mathbf A -\mathbf{H}^{\dagger}\mathbf{H} \mathbf A)
		&= \mathbf H \mathbf{H}^{\dagger} + \mathbf H \mathbf A -\mathbf H \mathbf{H}^{\dagger}\mathbf{H} \mathbf A \\
		& =  \mathbf H \mathbf{H}^{\dagger} + \mathbf H \mathbf A - \mathbf I_{N_{\text r}} \mathbf{H} \mathbf A \\
		&= \mathbf H \mathbf{H}^{\dagger}  \\
		&= \mathbf I_{N_{\text r}}
	\end{aligned}
$$

文献 \cite{4599181} 进一步证明了，在总功率约束的条件下，伪逆是所有广义逆矩阵中最节省能量的。再具体的讨论，等做完基本线性预编码的基本介绍之后再展开。

因此，预编码矩阵 

$$
\mathbf F = \mathbf H^- = \mathbf{H}^{\dagger} + ( I_{N_\text t} -\mathbf{H}^{\dagger}\mathbf{H}) \mathbf A
\tag{2}
$$

则信道传输模型就变为：

$$
\begin{aligned}
		\mathbf y &= \mathbf H \mathbf F \mathbf s + n   \\
		&= \mathbf H \mathbf H^- \mathbf s + n \\
		&= \mathbf s + \mathbf n
	\end{aligned}
$$

可以看到，干扰被完全消除。

这里我们以一个具体例子来分析和讨论一下上面的过程，以一个 $$N_{\text r} = 3, N_{\text t} = 5$$ 即 5 发 3 收为例子，则 $$\mathbf H$$ 是 3 行 5 列。因为我们假定是 3 个行向量是线性无关的，且这三个行向量都各含有5 个元素，因此，这3个行向量都是 5 维空间中不相关的3个向量，这三个向量的线性组合，构成一个超平面，是 5 维空间中的一个子线性空间。

那么 $$\mathbf{P}_{\perp} = \mathbf I -\mathbf{H}^{\dagger}\mathbf{H}$$ 作用在 5 维空间中的任何一个向量 $$\vec{\mathbf a}$$ 上：

$$
\mathbf{P}_{\perp} \vec{\mathbf a} = \vec{\mathbf a} - \mathbf{H}^{\dagger}\mathbf{H} \vec{\mathbf a}
$$

其中 $$\mathbf{H}^{\dagger}\mathbf{H} \vec{\mathbf a}$$ 是投影到 3 维子空间中的一个向量，那么
$$\vec{\mathbf a} - \mathbf{H}^{\dagger}\mathbf{H} \vec{\mathbf a}$$ 就是与前面那个3维子空间正交的2维子空间中的向量，这些向量虽然都是在各自的2或者3维子空间中，但是，因为都是 5 维空间中的向量，因此，这些投影到子空间中的向量，也是各自都是用 5 个元素来表示的。

![图4：在三维空间中演示投影](/figure/mimo_precoder/linear/3D_vector_projection.png)

*图4：在三维空间中演示投影*

我们来看看这个预编码矩阵在具体做什么：

根据公式 (2), 其中  $$\mathbf A$$ 是 三个 5 维空间中的列向量组成，经过投影后 $$( I_{N_\text t} -\mathbf{H}^{\dagger}\mathbf{H}) \mathbf A$$ 还是 三个 5 维空间中的列向量，但是，这三个列向量位于 $$\mathbf H$$ 的 零空间中，把这三个向量记为 $$\mathbf h_1^{(\text{NULL})},\mathbf h_2^{(\text{NULL})},\mathbf h_3^{(\text{NULL})}$$.

另外，易于证明 $$\mathbf{H}^{\dagger}$$ 是在 $$\mathbf H$$ 的三个行向量所在的三维空间中的三个不相关的向量，也即代表 $$\mathbf H$$ 的三个行向量所在的三维空间。记为  $$\mathbf h_1^{\dagger},\mathbf h_2^{\dagger},\mathbf h_3^{\dagger}$$

将 $$\mathbf H$$ 表示为行向量的形式：

$$
\mathbf H = \begin{bmatrix}
		\bar{\mathbf h}_1 \\
		\bar{\mathbf h}_2 \\
		\bar{\mathbf h}_3 \\
	\end{bmatrix}
$$

则信道传输模型变成：

$$
\begin{aligned}
		\mathbf y &= \mathbf H \mathbf F \mathbf s + \mathbf n  \\
		&=  \mathbf H (  \mathbf{H}^{\dagger} + ( I_{N_\text t} -\mathbf{H}^{\dagger}\mathbf{H}) \mathbf A ) \mathbf s + \mathbf n \\
		&=\begin{bmatrix}
			\bar{\mathbf h}_1 \\
			\bar{\mathbf h}_2 \\
			\bar{\mathbf h}_3 \\
		\end{bmatrix}
		\left (
		\begin{bmatrix}
			\mathbf h_1^{\dagger} & \mathbf h_2^{\dagger} &\mathbf h_3^{\dagger}
		\end{bmatrix}
		+
		\begin{bmatrix}
			\mathbf h_1^{(\text{NULL})} & \mathbf h_2^{(\text{NULL})} & \mathbf h_3^{(\text{NULL})}
		\end{bmatrix}  \right )
		\begin{bmatrix}
			s_1 \\
			s_2 \\
			s_3 
		\end{bmatrix} + \mathbf n\\
		&= \begin{bmatrix}
			\bar{\mathbf h}_1 \\
			\bar{\mathbf h}_2 \\
			\bar{\mathbf h}_3 \\
		\end{bmatrix}
		\left (  
		s_1 \mathbf h_1^{\dagger} +s_2 \mathbf h_2^{\dagger} + s_3\mathbf h_3^{\dagger}
		+
		s_1 \mathbf h_1^{(\text{NULL})} +s_2 \mathbf h_2^{(\text{NULL})} +s_3 \mathbf h_3^{(\text{NULL}}
		\right ) + \mathbf n \\
		&=\begin{bmatrix}
			s_1 \\
			s_2 \\
			s_3
		\end{bmatrix}  + \mathbf n
	\end{aligned}
$$

## 信道矩阵的秩小于接收天线数是不能做zero forcing预编码

信道矩阵 $$\mathbf H$$ 是 $$N_{\text r} \times N_{\text t}$$ 维度的，其中 $$N_{\text r} \leq N_{\text t}$$，如果秩小于$$N_{\text r}$$，那么就不能做 zero forcing 预编码。

可以直观理解，如果所有接收天线都使用的话，发送的数据不可能直接在每个接收天线上直接分开。例如秩是 2， 接收天线是 3 根，那么，如果这3根天线都参与接收，那发送端不可能把这两个数据分配到三根天线上，除非其中一个接收天线不使用，这样的话相当于减少了 $$\mathbf H$$ 矩阵的一行，让秩等于接收天线数。

## 投影矩阵的分析
本文旨在分析为什么矩阵 $$\mathbf{P}_{\text{row}} = \mathbf{H}^\dagger \mathbf{H}$$ 是向矩阵 $$\mathbf{H}$$ 的行空间（Row Space）的正交投影。

我们假设 $$\mathbf{H}$$ 是一个 $$N_r \times N_t$$ 的矩阵，且 $$\mathbf{H}$$ 是行满秩的（即 $$\text{rank}(\mathbf{H}) = N_r$$，且 $$N_r \le N_t$$）。
根据定义，行满秩矩阵 $$\mathbf{H}$$ 的右伪逆（Pseudoinverse）为：

$$
\mathbf{H}^\dagger = \mathbf{H}^H (\mathbf{H} \mathbf{H}^H)^{-1}
$$

一个关键性质是，对于右伪逆，我们有 $$\mathbf{H} \mathbf{H}^\dagger = \mathbf{I}_{N_r}$$，其中 $$\mathbf{I}_{N_r}$$ 是 $$N_r \times N_r$$ 的单位矩阵。

### 方法一：验证正交投影矩阵的性质
一个矩阵 $$\mathbf{P}$$ 是正交投影矩阵，当且仅当它满足以下两个条件：

.**幂等性 (Idempotent):** $$\mathbf{P}^2 = \mathbf{P}$$

.**埃尔米特性 (Hermitian):** $$\mathbf{P}^H = \mathbf{P}$$

我们来验证 $$\mathbf{P}_{\text{row}} = \mathbf{H}^\dagger \mathbf{H}$$满足以上两个条件：

首先，验证满足幂等性：

$$
\begin{aligned}
		\mathbf{P}_{\text{row}}^2 &= (\mathbf{H}^\dagger \mathbf{H}) (\mathbf{H}^\dagger \mathbf{H}) \\
		&= \mathbf{H}^\dagger (\mathbf{H} \mathbf{H}^\dagger) \mathbf{H} \\
		&= \mathbf{H}^\dagger (\mathbf{I}_{N_r}) \mathbf{H} \quad (\text{因为 } \mathbf{H} \mathbf{H}^\dagger = \mathbf{I}_{N_r}) \\
		&= \mathbf{H}^\dagger \mathbf{H} \\
		&= \mathbf{P}_{\text{row}}
	\end{aligned}
$$

幂等性满足。这证明了 $$\mathbf{P}_{\text{row}}$$ 确实是一个投影矩阵。

其次，验证满足埃尔米特性：

$$
\begin{aligned}
		\mathbf{P}_{\text{row}}^H &= (\mathbf{H}^\dagger \mathbf{H})^H \\
		&= \mathbf{H}^H (\mathbf{H}^\dagger)^H \\
		&= \mathbf{H}^H \left( \mathbf{H}^H (\mathbf{H} \mathbf{H}^H)^{-1} \right)^H \\
		&= \mathbf{H}^H \left( ((\mathbf{H} \mathbf{H}^H)^{-1})^H (\mathbf{H}^H)^H \right) \\
		&= \mathbf{H}^H \left( ((\mathbf{H} \mathbf{H}^H)^H)^{-1} \mathbf{H} \right) \quad (\text{因为 } (\mathbf{A}^{-1})^H = (\mathbf{A}^H)^{-1} \text{ 且 } (\mathbf{A}^H)^H = \mathbf{A}) \\
		&= \mathbf{H}^H \left( ((\mathbf{H}^H)^H \mathbf{H}^H)^{-1} \mathbf{H} \right) \\
		&= \mathbf{H}^H \left( (\mathbf{H} \mathbf{H}^H)^{-1} \mathbf{H} \right) \\
		&= \left( \mathbf{H}^H (\mathbf{H} \mathbf{H}^H)^{-1} \right) \mathbf{H} \\
		&= \mathbf{H}^\dagger \mathbf{H} \\
		&= \mathbf{P}_{\text{row}}
	\end{aligned}
$$

埃尔米特性满足。

**结论：** $$\mathbf{P}_{\text{row}} = \mathbf{H}^\dagger \mathbf{H}$$ 是一个正交投影矩阵。

方阵:行空间与列空间相同的条件：

对于一个 $$n \times n$$ 的方阵，其行空间与列空间具有相同的维度（即矩阵的秩），但它们并不总是同一个子空间。两者是否为同一空间，取决于该方阵是否满足特定的对称性质。

对于一个复数方阵 $$\mathbf{H} \in \mathbb{C}^{n \times n}$$，其行空间与列空间相同的充分必要条件是该矩阵为**埃尔米特矩阵 (Hermitian Matrix)**。根据定义，$$\mathbf{H}$$ 的列空间为 $$\mathrm{Col}(\mathbf{H})$$，其行空间为 $$\mathrm{Col}(\mathbf{H}^H)$$。如果 $$\mathbf{H}$$ 是埃尔米特矩阵，则 $$\mathbf{H} = \mathbf{H}^H$$。因此，$$\mathrm{Col}(\mathbf{H}) = \mathrm{Col}(\mathbf{H}^H)$$，两个子空间必然相同。

对于一个实数方阵 $$\mathbf{A} \in \mathbb{R}^{n \times n}$$，其行空间与列空间相同的充分必要条件是该矩阵为**对称矩阵 (Symmetric Matrix)**。这是复数情况的一个特例。在实数域中，埃尔米特转置 ($$\mathbf{A}^H$$) 退化为普通转置 ($$\mathbf{A}^T$$)。因此，当 $$\mathbf{A} = \mathbf{A}^T$$ 时，$$\mathrm{Col}(\mathbf{A}) = \mathrm{Col}(\mathbf{A}^T)$$，即列空间与行空间相同。



### 方法二：分析投影的子空间
根据线性代数的四子空间基本定理，任意向量 $$\mathbf{x} \in \mathbb{C}^{N_t}$$ 都可以被唯一地分解为其在 $$\mathbf{H}$$ 的行空间（$$\text{Row}(\mathbf{H})$$）和 $$\mathbf{H}$$ 的零空间（$$\text{\text{null}}(\mathbf{H})$$）的分量之和：

$$
\mathbf{x} = \mathbf{x}_{\text{row}} + \mathbf{x}_{\text{null}}
$$

其中 $$\mathbf{x}_{\text{row}} \in \text{Row}(\mathbf{H})$$ 且 $$\mathbf{x}_{\text{null}} \in \text{Null}(\mathbf{H})$$。这两个子空间是正交补的。

一个向行空间投影的矩阵，其作用应该是保留 $$\mathbf{x}_{\text{row}}$$ 分量，并消除 $$\mathbf{x}_{\text{null}}$$ 分量。

**情况 A：向量在 $$\mathbf{H}$$ 的行空间中**
如果一个向量 $$\mathbf{x}_{\text{row}}$$ 位于 $$\mathbf{H}$$ 的行空间中，那么它一定可以被 $$\mathbf{H}$$ 的行共轭向量所线性表示。等价地，$$\mathbf{x}_{\text{row}}$$ 位于 $$\mathbf{H}^H$$ 的列空间中。因此，存在某个向量 $$\mathbf{a} \in \mathbb{C}^{N_r}$$ 使得：

$$
\mathbf{x}_{\text{row}} = \mathbf{H}^H \mathbf{a}
$$

这里取共轭，是为了保证在$$\mathbf H$$ 的行空间中的向量，与其零空间正交，任何零空间的向量$$\mathbf z$$ 满足 $$\mathbf H \mathbf z = \mathbf 0$$。 只有把行空间中的向量表达成共轭的形式才能保证正交性：

$$
\mathbf{x}_{\text{row}}^{\text H} \mathbf z 
	= (\mathbf{H}^H \mathbf{a})^{\text H} \mathbf z  
	= \mathbf{a}^{\text H} \mathbf{H}  \mathbf z  
	= \mathbf{a}^{\text H} \mathbf 0
	= \mathbf 0
$$

如果

$$
\mathbf{x}_{\text{row}} = \mathbf{H}^{\text T} \mathbf{a}
$$

那么：

$$
\mathbf{x}_{\text{row}}^{\text H} \mathbf z 
	= (\mathbf{H}^{\text T} \mathbf{a})^{\text H} \mathbf z  
	= \mathbf{a}^{\text H} \mathbf{H}^*  \mathbf z
$$

这样不能确保一定正交。



我们将 $$\mathbf{P}_{\text{row}}$$ 作用于 $$\mathbf{x}_{\text{row}}$$：

$$
\begin{aligned}
		\mathbf{P}_{\text{row}} \mathbf{x}_{\text{row}} &= (\mathbf{H}^\dagger \mathbf{H}) \mathbf{x}_{\text{row}} \\
		&= (\mathbf{H}^\dagger \mathbf{H}) (\mathbf{H}^H \mathbf{a}) \\
		&= \left( \mathbf{H}^H (\mathbf{H} \mathbf{H}^H)^{-1} \right) \mathbf{H} (\mathbf{H}^H \mathbf{a}) \\
		&= \mathbf{H}^H (\mathbf{H} \mathbf{H}^H)^{-1} (\mathbf{H} \mathbf{H}^H) \mathbf{a} \\
		&= \mathbf{H}^H \mathbf{I}_{N_r} \mathbf{a} \\
		&= \mathbf{H}^H \mathbf{a} \\
		&= \mathbf{x}_{\text{row}}
	\end{aligned}
$$

**结论 A：** 对于任何已经在 $$\mathbf{H}$$ 行空间中的向量，$$\mathbf{P}_{\text{row}}$$ 会使其保持不变。

**情况 B：向量在 $$\mathbf{H}$$ 的零空间中**
如果一个向量 $$\mathbf{x}_{\text{null}}$$ 位于 $$\mathbf{H}$$ 的零空间中，根据定义，我们有：

$$
\mathbf{H} \mathbf{x}_{\text{null}} = \mathbf{0}
$$

我们将 $$\mathbf{P}_{\text{row}}$$ 作用于 $$\mathbf{x}_{\text{null}}$$：

$$
\begin{aligned}
		\mathbf{P}_{\text{row}} \mathbf{x}_{\text{null}} &= (\mathbf{H}^\dagger \mathbf{H}) \mathbf{x}_{\text{null}} \\
		&= \mathbf{H}^\dagger (\mathbf{H} \mathbf{x}_{\text{null}}) \\
		&= \mathbf{H}^\dagger (\mathbf{0}) \\
		&= \mathbf{0}
	\end{aligned}
$$

**结论 B：** 对于任何在 $$\mathbf{H}$$ 零空间中的向量，$$\mathbf{P}_{\text{row}}$$ 会将其映射为零向量。

**最终总结**
综合情况 A 和 B，当 $$\mathbf{P}_{\text{row}} = \mathbf{H}^\dagger \mathbf{H}$$ 作用于任意向量 $$\mathbf{x} = \mathbf{x}_{\text{row}} + \mathbf{x}_{\text{null}}$$ 时：

$$
\mathbf{P}_{\text{row}} \mathbf{x} = \mathbf{P}_{\text{row}} (\mathbf{x}_{\text{row}} + \mathbf{x}_{\text{null}}) = \mathbf{P}_{\text{row}} \mathbf{x}_{\text{row}} + \mathbf{P}_{\text{row}} \mathbf{x}_{\text{null}} = \mathbf{x}_{\text{row}} + \mathbf{0} = \mathbf{x}_{\text{row}}
$$

此操作保留了 $$\mathbf{x}$$ 在 $$\mathbf{H}$$ 行空间中的分量，并消除了其在 $$\mathbf{H}$$ 零空间中的分量。

这严格证明了 $$\mathbf{P}_{\text{row}} = \mathbf{H}^\dagger \mathbf{H}$$ 是向 $$\mathbf{H}$$ 的行向量构成的子空间（行空间）上的正交投影矩阵。

## 幂等性和埃尔米特行与正交投影的等价性证明

### 幂等性和埃尔米特行==》正交投影

**定义：正交投影矩阵**

一个矩阵 $$\mathbf{P}$$ 是一个正交投影矩阵，如果它将任意向量 $$\mathbf{v}$$ 投影到其列空间 $$\mathrm{Col}(\mathbf{P})$$ 上，且其误差向量 $$\mathbf{e} = \mathbf{v} - \mathbf{P}\mathbf{v}$$ 与整个列空间 $$\mathrm{Col}(\mathbf{P})$$ 正交。


我们需要证明，同时满足这两个条件的矩阵 $$\mathbf{P}$$ 符合正交投影矩阵的定义。证明分为两个步骤。

**证明：**

a) 第一步：

利用幂等性，证明这是一个投影（可能是正交投影，可能是斜投影），投影要满足再次投影的特性。

对于任意向量 $$\mathbf{v} \in \mathbb{C}^n$$，我们都可以将其分解为：

$$
\mathbf{v} = \mathbf{I}\mathbf{v} = (\mathbf{P} + \mathbf{I} - \mathbf{P})\mathbf{v} = \mathbf{P}\mathbf{v} + (\mathbf{I} - \mathbf{P})\mathbf{v}
$$

我们定义：

\textbf{投影分量 $$\mathbf w$$}: $$\mathbf{w} = \mathbf{P}\mathbf{v}$$

\textbf{误差分量 $$\mathbf{e}$$}: $$\mathbf{e} = (\mathbf{I} - \mathbf{P})\mathbf{v}$$

如果对 “投影向量$$\mathbf w$$” 再投影，应该还是  “投影向量$$\mathbf w$$”: 

$$
\mathbf P \mathbf w = \mathbf P (\mathbf{P}\mathbf{v}) = \mathbf P^2 \mathbf v = \mathbf P \mathbf v = \mathbf w
$$

如果对 “误差分量$$\mathbf e$$” 再投影，应该 是 0 向量。

$$
\mathbf P \mathbf e = \mathbf P (\mathbf{I} - \mathbf{P})\mathbf{v} = (\mathbf P - \mathbf P^2) \mathbf{v} =  (\mathbf P - \mathbf P) \mathbf{v} = 0
\tag{3}
$$

因此，**幂等性** $$\mathbf{P}^2 = \mathbf{P}$$ 保证了 $$\mathbf{P}$$ 是一个投影矩阵，它将任意向量 $$\mathbf{v}$$ 唯一地分解为一个 $$\mathrm{Col}(\mathbf{P})$$ 中的分量和一个 $$\mathrm{Null}(\mathbf{P})$$ 中的分量。

b) 第二步:

证明正交性 (埃尔米特性的作用)

因为投影向量 $$\mathbf w = \mathbf P \mathbf v$$ 相当于在 $$\mathbf P$$ 的列空间中. 而上面已经证明的结论(公式 (3)) 只能说明 误差向量是 $$\mathbf P$$ 矩阵行向量空间的零空间。而投影分解出来的投影分量 $$\mathbf w$$ 在 $$\mathbf P$$ 的列空间中，因此，需要埃米尔特性来保证行空间和列空间是同一个空间，因为零空间与行空间是正交的，且行空间和列空间是同一个空间，因此，能保证是正交投影。

另外，我们也可以直接来证明，我们需要证明任何一个向量  $$\mathbf x$$ 在列向量空间中，都与误差向量正交，则可以证明出来误差向量与列空间是正交的，因此，是一个正交分解。

因为$$\mathbf x$$ 是在列向量空间中，因此可以表示为 $$\mathbf x = \mathbf P \mathbf a$$，我们来证明这个向量与误差向量正交：

$$
\begin{aligned}
		\mathbf x^{\text H} \mathbf e 
		&= (\mathbf P \mathbf a)^{\text H} ) \mathbf e = \mathbf a^{\text H} \mathbf P ^{\text H} \mathbf e \\
		&= \mathbf a^{\text H} \mathbf P \mathbf e  \quad \quad \quad (\mathbf P\text{的埃尔米特性})  \\
		&= \mathbf a^{\text H} \mathbf 0 \\
		&= 0
	\end{aligned}
$$

至此，我们已经证明：

a) $$\mathbf{P}^2 = \mathbf{P}$$ 保证了 $$\mathbf{P}$$ 是一个投影

b) $$\mathbf{P}^H = \mathbf{P}$$ 保证了这个投影是正交的。

### 证明：正交投影 \texorpdfstring{$$\implies$$}{} 幂等性与埃尔米特性

$$\mathbf{P}$$ 是一个正交投影矩阵，因此，$$\mathbf{P}$$ 将任意向量 $$\mathbf{v}$$ 投影到其列空间 $$S = \mathrm{Col}(\mathbf{P})$$ 上，
这个投影的结果是 $$\mathbf{w} = \mathbf{P}\mathbf{v}$$。
投影产生的误差向量 $$\mathbf{e} = \mathbf{v} - \mathbf{w} = (\mathbf{I} - \mathbf{P})\mathbf{v}$$ 必须与投影空间 $$S$$ 完全正交。

根据上述给定条件，我们来展开证明。

a) **首先** 证明幂等性 ($$\mathbf{P}^2 = \mathbf{P}$$)。

这个性质来自于“投影”这个操作的几何本质。

$$\mathbf{v}$$ 为空间中任意一个向量。
$$\mathbf{P}$$ 将 $$\mathbf{v}$$ 投影到子空间 $$S$$ 上，得到 $$\mathbf{w} = \mathbf{P}\mathbf{v}$$。
根据投影的定义，向量 $$\mathbf{w}$$ \textit{已经位于}目标子空间 $$S$$ 中。“投影”操作的含义是，如果一个向量（这里是 $$\mathbf{w}$$）已经位于目标子空间 $$S$$ 中，再次对其投影不会（也不应）产生任何改变。

因此，$$\mathbf{P}$$ 作用于 $$\mathbf{w}$$ 必须等于 $$\mathbf{w}$$ 本身：

$$
\mathbf{P}\mathbf{w} = \mathbf{w}
$$

我们将 $$\mathbf{w} = \mathbf{P}\mathbf{v}$$ 代入上式：

$$
\mathbf{P}(\mathbf{P}\mathbf{v}) = \mathbf{P}\mathbf{v}
$$

展开得到：

$$
\mathbf{P}^2 \mathbf{v} = \mathbf{P}\mathbf{v}
$$

因为这个等式对于\textit{任意}向量 $$\mathbf{v}$$ 都成立，所以矩阵本身必须相等：

$$
\mathbf{P}^2 = \mathbf{P}
$$

如果用误差向量的再次投影是0 ，也可以证明出来：

$$
0 = \mathbf p \mathbf e = \mathbf p (\mathbf I - \mathbf P) \mathbf v = ( \mathbf p -  \mathbf p^2) \mathbf v
$$

因为这个等式对于\textit{任意}向量 $$\mathbf{v}$$ 都成立，所以$$\mathbf p -  \mathbf p^2$$ 必须是0矩阵，得证。

幂等性得证。

b) 接下来证明埃尔米特性 ($$\mathbf{P}^H = \mathbf{P}$$)

这个性质来自于“正交”这个条件。


根据**正交**投影的定义，误差向量 $$\mathbf{e} = (\mathbf{I} - \mathbf{P})\mathbf{v}$$ 必须与投影空间 $$S = \mathrm{Col}(\mathbf{P})$$ 中的\textit{任意}向量都正交。
我们从 $$S = \mathrm{Col}(\mathbf{P})$$ 中任取一个向量 $$\mathbf{u}$$。根据列空间的定义，$$\mathbf{u}$$ 一定可以被写成 $$\mathbf{u} = \mathbf{P}\mathbf{a}$$ 的形式（对于某个向量 $$\mathbf{a}$$）。

正交性要求$$\mathbf{u}$$ 与 $$\mathbf{e}$$ 的内积必须为零：

$$
\mathbf{u}^H \mathbf{e} = 0
$$

我们将 $$\mathbf{u} = \mathbf{P}\mathbf{a}$$ 和 $$\mathbf{e} = (\mathbf{I} - \mathbf{P})\mathbf{v}$$ 代入：

$$
(\mathbf{P}\mathbf{a})^H ((\mathbf{I} - \mathbf{P})\mathbf{v}) = 0
$$

展开：

$$
\mathbf{a}^H \mathbf{P}^H (\mathbf{I} - \mathbf{P}) \mathbf{v} = 0
$$

这个等式必须对\textit{所有}可能的向量 $$\mathbf{a}$$ 和 $$\mathbf{v}$$ 都成立。

唯一能让这个等式（一个二次型）对所有 $$\mathbf{a}$$ 和 $$\mathbf{v}$$ 都为零的情况是，中间的矩阵必须是**零矩阵**：

$$
\mathbf{P}^H (\mathbf{I} - \mathbf{P}) = \mathbf{0}
$$

展开括号：

$$
\mathbf{P}^H \mathbf{I} - \mathbf{P}^H \mathbf{P} = \mathbf{0}
$$

即：

$$
\mathbf{P}^H = \mathbf{P}^H \mathbf{P}
$$

现在，我们对上式两边同时取埃尔米特共轭（即 $$(\cdot)^H$$）：

$$
(\mathbf{P}^H)^H = (\mathbf{P}^H \mathbf{P})^H
$$

整理后得到：

$$
\mathbf{P} = \mathbf{P}^H \mathbf{P}
$$

所以：

$$
\mathbf{P} = \mathbf{P}^H
$$

埃尔米特性得证。



## 迫零预编码算法

迫零算法也可以从让接收到的信号尽可能接近发射信号的角度来推导，即目标是最小化均方误差（MSE）：

$$
J(\mathbf F) = \mathbb{E}  || \mathbf H \mathbf F \mathbf x - \mathbf x ||^2
$$

找最优 $$\tilde{\mathbf F}$$：

$$
\tilde{\mathbf F} = \min_{\mathbf F}J(\mathbf F)
$$

由于发送信号 $$\mathbf x$$ 是随机变量，我们需要对误差求数学期望。假设信号是不相关的且归一化功率为 1，即 $$\mathbb{E}\{ \mathbf x \mathbf x^{\text H} \} = \mathbf I$$，则目标函数变为：

$$
\begin{aligned}
		J(\mathbf F) &= \mathbb{E} \left\{ \text{tr}\left[ (\mathbf H \mathbf F \mathbf x - \mathbf x)(\mathbf H \mathbf F \mathbf x - \mathbf x)^{\text H} \right] \right\}  \\
		&= \mathbb{E} \left\{ \text{tr}\left[ (\mathbf H \mathbf F - \mathbf I) \mathbf x \mathbf x^{\text H} (\mathbf H \mathbf F - \mathbf I)^{\text H} \right] \right\} \\
		&=  \text{tr}\left[ (\mathbf H \mathbf F - \mathbf I) \mathbb{E} \{ \mathbf x \mathbf x^{\text H} \} (\mathbf H \mathbf F - \mathbf I)^{\text H} \right] \\
		&= \text{tr}\left[ (\mathbf H \mathbf F - \mathbf I) (\mathbf H \mathbf F - \mathbf I)^{\text H} \right]  \\
		&=  ||\mathbf H \mathbf F - \mathbf I||_F^2 \\
		&=  \text{tr}\{(\mathbf H \mathbf F - \mathbf I)(\mathbf H \mathbf F - \mathbf I)^{\text H}\}
	\end{aligned}
$$

显然，要使上述均方误差最小（即为零），需满足条件 $$\mathbf H \mathbf F = \mathbf I$$。由于发射天线数通常大于用户数，该方程组是欠定的（Under-determined），可以找到满足条件的解，但是，不是唯一的。为了以最小的发射功率来实现迫零算法，我们要寻找该方程组的**最小范数解**，即采用摩尔-彭若斯右伪逆（Moore-Penrose Right Pseudo-inverse）：

$$
\mathbf F = \mathbf H^{\text H} (\mathbf H \mathbf H^{\text H})^{-1}
$$

**最小范数解的详细推导**

虽然满足迫零条件 $$\mathbf H \mathbf F = \mathbf I$$ 的解有无穷多个（因为 $$N_t > K$$，$$\mathbf H$$ 为行满秩的胖矩阵），但在实际系统中，我们希望在消除干扰的同时，尽可能降低发射总功率。

发射总功率与预编码矩阵 $$\mathbf F$$ 的 Frobenius 范数平方成正比。因此，该问题本质上是一个**带约束的优化问题**：

$$
\begin{aligned}
		& \min_{\mathbf F} \quad ||\mathbf F||_F^2 = \text{tr}(\mathbf F \mathbf F^{\text H}) \\
		& \text{s.t.} \quad \mathbf H \mathbf F - \mathbf I = \mathbf 0
	\end{aligned}
$$

**构造拉格朗日函数**
为了求解该问题，我们引入拉格朗日乘子矩阵 $$\mathbf \Lambda \in \mathbb{C}^{K \times K}$$。考虑到复数域的约束条件，拉格朗日函数 $$\mathcal{L}(\mathbf F, \mathbf \Lambda)$$ 构造如下：

$$
\mathcal{L}(\mathbf F, \mathbf \Lambda) = \underbrace{\text{tr}(\mathbf F \mathbf F^{\text H})}_{\text{目标函数}} - \underbrace{\text{tr}\left( \mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H} \right) - \text{tr}\left( \mathbf \Lambda^{\text H} (\mathbf H \mathbf F - \mathbf I) \right)}_{\text{约束项（确保结果为实数）}}
$$

**求解最优预编码矩阵**
我们对拉格朗日函数 $$\mathcal{L}$$ 关于 $$\mathbf F^*$$ 求偏导：

1. 对第一项 $$\text{tr}(\mathbf F \mathbf F^{\text H})$$ 求导，结果为 $$\mathbf F$$。

2. 对约束项展开：
$$ \text{tr}\left( \mathbf \Lambda (\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I) \right) = \text{tr}(\mathbf \Lambda \mathbf F^{\text H} \mathbf H^{\text H}) - \text{tr}(\mathbf \Lambda) 

$$
利用迹的循环性质 $$\text{tr}(\mathbf A \mathbf B \mathbf C) = \text{tr}(\mathbf B \mathbf C \mathbf A)$$，可变换为 $$\text{tr}(\mathbf H^{\text H} \mathbf \Lambda \mathbf F^{\text H})$$。对 $$\mathbf F^*$$ 求导结果为系数矩阵 $$\mathbf H^{\text H} \mathbf \Lambda$$。

3. 其余项不含 $$\mathbf F^{\text H}$$（即不含 $$\mathbf F^*$$），导数为 0。

综合以上步骤，得到梯度方程：
$$

\frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - \mathbf H^{\text H} \mathbf \Lambda = \mathbf 0

$$
由此得到最优解的结构形式：
$$

\mathbf F = \mathbf H^{\text H} \mathbf \Lambda

$$
最后，我们需要确定乘子 $$\mathbf \Lambda$$。将式 (\ref{eq:structure}) 代入原始约束条件 $$\mathbf H \mathbf F = \mathbf I$$：
$$

\mathbf H (\mathbf H^{\text H} \mathbf \Lambda) = \mathbf I

$$

$$
(\mathbf H \mathbf H^{\text H}) \mathbf \Lambda = \mathbf I
$$
由于 $$\mathbf H$$ 行满秩，$$(\mathbf H \mathbf H^{\text H})$$ 可逆，因此：
$$
\mathbf \Lambda = (\mathbf H \mathbf H^{\text H})^{-1}
$$
将 $$\mathbf \Lambda$$ 代回式 (\ref{eq:structure})，即得到最终的迫零预编码矩阵公式：
$$
\mathbf F = \mathbf H^{\text H} (\mathbf H \mathbf H^{\text H})^{-1}
$$
这正是著名的摩尔-彭若斯右伪逆（Moore-Penrose Right Pseudo-inverse）。 


### 深入讨论：为何约束项必须成对出现？

在构造拉格朗日函数时，为何不能简化约束项，仅使用 $$\text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H})$$ 单独一项？事实上，必须采用 $$\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$$ 这种成对形式。如果只写其中一项，会导致两个严重的数学问题：一是优化问题定义的非法性，二是复数求导时的梯度丢失。

下面分两点详细阐述。

**1. 定义的合法性：优化目标必须为实数**
这是最根本的原因。优化问题的本质是在可行域内寻找目标函数的最小值。这就要求目标函数的值域必须是可排序的（Ordered）。

1) 我们可以比较两个实数的大小（例如 $$3 < 5$$）。

2) **复数域是不可排序的**。我们无法定义复数 $$3+4i$$ 和 $$5+2i$$ 谁更“小”。


让我们观察只使用单项约束的后果：

1) 原始目标函数（发射功率）$$\text{tr}(\mathbf F \mathbf F^{\text H})$$ 本质上是能量，属于**实数**。

2) 单项约束 $$\text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H})$$ 通常是一个**复数**。


如果拉格朗日函数 $$\mathcal{L}$$ 仅包含一项约束：
$$
\mathcal{L} = \text{实数} - \text{复数} = \text{复数}
$$
这将导致“最小化 $$\mathcal{L}$$”这一数学表述失去意义，因为我们无法寻找一个复数函数的“极小值”。

**成对出现的数学意义：**
利用复数恒等式：任何复数 $$Z$$ 与其共轭 $$Z^*$$ 之和均为实数。
$$
Z + Z^* = 2\text{Re}\{Z\} \quad (\in \mathbb{R})
$$
因此，构造 $$\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$$ 的作用正是为了构造一个**实值**的约束项，从而使优化问题成立。

**2. 梯度的完备性：避免求导时的“消失”陷阱**
即使忽略实数定义的问题，强行进行数学推导，单项约束也会在复矩阵求导（Wirtinger Calculus）中带来巨大的风险。

Wirtinger 导数的核心在于：矩阵 $$\mathbf F$$ 和其共轭 $$\mathbf F^*$$ 被视为两个相互独立的变量。
设约束部分为 $$C(\mathbf F, \mathbf F^*)$$，我们分析两种单项情况：

\subparagraph{情况 A：仅使用包含 $$\mathbf F^*$$ 的项}
假设约束项为：
$$
C_1 = \text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H}) = \text{tr}(\mathbf \Lambda (\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I^{\text H}))
$$
该项显式包含 $$\mathbf F^{\text H}$$（即 $$\mathbf F^*$$）。对 $$\mathbf F^*$$ 求导可得 $$\mathbf H^{\text H} \mathbf \Lambda$$。
$$
\frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - \mathbf H^{\text H} \mathbf \Lambda = \mathbf 0
$$
\textit{评价：虽然定义不严谨（复数无法求极值），但歪打正着，导出的结构碰巧是正确的。}

\subparagraph{情况 B：仅使用包含 $$\mathbf F$$ 的项}
假设约束项为：
$$
C_2 = \text{tr}(\mathbf \Lambda^{\text H} (\mathbf H \mathbf F - \mathbf I))
$$
注意，该项\textbf{只包含 $$\mathbf F$$，不包含 $$\mathbf F^*$$}。根据 Wirtinger 导数规则，不含 $$\mathbf F^*$$ 的项对 $$\mathbf F^*$$ 的偏导数为 **0**。
此时求导结果为：
$$
\frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - 0 = \mathbf 0 \implies \mathbf F = \mathbf 0
$$
\textit{评价：彻底错误！推导出了全零矩阵，这显然不是迫零预编码的解。}

**总结**
采用 $$\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$$ 这种成对形式具有两重必要性：


1) **保实性（Realness）：** 确保整个拉格朗日函数是实数，赋予“求极小值”合法的数学意义。

2) **完备性（Completeness）：** 它同时包含了 $$\mathbf F$$ 和 $$\mathbf F^*$$ 的信息。无论对哪一个变量求导，约束项都不会凭空消失，保证了方程组的完整性。

### 直接无约束求导的局限性分析

如果不考虑发射功率最小化这一物理约束，而直接尝试对均方误差目标函数进行无约束求导，我们会遇到数学上的困境。以下是该路径的推导过程分析：

设定目标函数为：
$$
J(\mathbf F) = \text{tr}\{(\mathbf H \mathbf F - \mathbf I)(\mathbf H \mathbf F - \mathbf I)^{\text H}\}
$$
利用矩阵迹的线性性质及共轭转置规则展开上式：
$$
\begin{aligned}
		J(\mathbf F) &= \text{tr}\left( (\mathbf H \mathbf F - \mathbf I)(\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I) \right) \\
		&= \text{tr}(\mathbf H \mathbf F \mathbf F^{\text H} \mathbf H^{\text H}) - \text{tr}(\mathbf H \mathbf F) - \text{tr}(\mathbf F^{\text H} \mathbf H^{\text H}) + \text{tr}(\mathbf I)
	\end{aligned}
$$
为了寻找极值点，我们对共轭矩阵 $$\mathbf F^*$$ 求偏导并令其为零：
$$
\frac{\partial J}{\partial \mathbf F^*} = \mathbf H^{\text H} \mathbf H \mathbf F - \mathbf H^{\text H} = \mathbf 0
$$
整理上述方程，得到所谓的正规方程（Normal Equation）：
$$
\mathbf H^{\text H} \mathbf H \mathbf F = \mathbf H^{\text H}
$$
在试图从式 (\ref{eq:normal_eqn}) 解出 $$\mathbf F$$ 时，我们通常会尝试左乘 $$(\mathbf H^{\text H} \mathbf H)^{-1}$$。然而，在下行链路预编码场景中，发射天线数 $$N_t$$ 通常大于用户数 $$K$$。信道矩阵 $$\mathbf H$$ 的维度为 $$K \times N_t$$，这意味着乘积矩阵 $$\mathbf H^{\text H} \mathbf H$$ 是一个 $$N_t \times N_t$$ 的大方阵。由于 $$\mathbf H$$ 的秩受限于用户数 $$K$$（即 $$\text{rank}(\mathbf H) \le K$$），且 $$K < N_t$$，导致 $$\mathbf H^{\text H} \mathbf H$$ 必然是一个**亏秩矩阵（Rank Deficient）**。也就是说是奇异矩阵，其逆矩阵 $$(\mathbf H^{\text H} \mathbf H)^{-1}$$ 根本不存在。

因此，我们无法通过直接求逆的方法得到唯一的预编码矩阵 $$\mathbf F$$。这从数学上印证了该问题是一个欠定问题（Under-determined Problem），必须通过引入功率约束来寻找最小范数解。


### 数学预备：复数矩阵求导规则
为了寻找极值，我们需要相对复矩阵 $$\mathbf F$$ 求偏导。这里利用 **Wirtinger Calculus**（复微积分），我们将矩阵 $$\mathbf F$$ 和它的共轭 $$\mathbf F^*$$ 视为相互独立的变量。求极值时，只需令 $$\frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf 0$$。

在推导中，我们需要用到以下两条关键的矩阵迹（Trace）求导公式：

\begin{itemize}
	\item **规则 1（能量项求导）：** 
	
$$
\frac{\partial \text{tr}(\mathbf X \mathbf X^{\text H})}{\partial \mathbf X^*} = \mathbf X
$$
	\textit{解释：这类似于标量求导中 $$\frac{d}{dx^*} (x x^*) = x$$。}
	
	\item **规则 2（线性项求导）：**
	
$$
\frac{\partial \text{tr}(\mathbf A \mathbf X^{\text H})}{\partial \mathbf X^*} = \mathbf A
$$
	及
	
$$
\frac{\partial \text{tr}(\mathbf A \mathbf X)}{\partial \mathbf X^*} = \mathbf 0
$$
	\textit{解释：包含 $$\mathbf X^{\text H}$$ 的项可视作包含变量 $$\mathbf X^*$$，因此导数为系数矩阵 $$\mathbf A$$；而不包含 $$\mathbf X^{\text H}$$（仅包含 $$\mathbf X$$）的项对于 $$\mathbf X^*$$ 而言是常数，导数为 0。}
\end{itemize}


## Zero Forcing 迫零预编码的功率放大问题

Zero Forcing 迫零预编码的一个显著代价是功率消耗的增加（类似于接收端的噪声放大效应） 。信道矩阵 $$\mathbf{H}$$ 经过 SVD（奇异值分解）后，可以看成若干个发射和接收波束对构成的信道：
$$
\mathbf{H} = \mathbf{U} \mathbf{\Lambda} \mathbf{V}^H = \mathbf{u}_1 \lambda_1 \mathbf{v}_1^H + \dots + \mathbf{u}_{N_r} \lambda_{N_r} \mathbf{v}_{N_r}^H
$$
则迫零预编码可以分解为 ：
$$
\mathbf{H}^H(\mathbf{H}\mathbf{H}^H)^{-1} = \mathbf{V} \mathbf{\Lambda}^{-1} \mathbf{U}^H = \mathbf{v}_1 \lambda_1^{-1} \mathbf{u}_1^H + \dots + \mathbf{v}_{N_r} \lambda_{N_r}^{-1} \mathbf{u}_{N_r}^H
$$
发送的实际数据是 $$\mathbf{S}$$，含有 $$N$$ 个元素 。预编码后的情况为 ：
$$
\mathbf{H}^H(\mathbf{H}\mathbf{H}^H)^{-1} \mathbf{S} = \mathbf{v}_1 \lambda_1^{-1} \mathbf{u}_1^H \mathbf{S} + \dots + \mathbf{v}_{N_r} \lambda_{N_r}^{-1} \mathbf{u}_{N_r}^H \mathbf{S}
$$
其中，$$\mathbf{u}_i^H$$ 的平均功率也是 1 ：
$$
E\{\mathbf{u}_i^H \mathbf{S} (\mathbf{u}_i^H \mathbf{S})^H\} = \mathbf{u}_i^H E\{\mathbf{S}\mathbf{S}^H\} \mathbf{u}_i = \mathbf{u}_i^H \mathbf{u}_i = 1
$$
令 $$s_i' = \mathbf{u}_i^H \mathbf{S}$$，则公式 (0.2.3) 可以写成 ：
$$
\mathbf{H}^H(\mathbf{H}\mathbf{H}^H)^{-1} \mathbf{S} = \mathbf{v}_1 \lambda_1^{-1} s_1' + \dots + \mathbf{v}_{N_r} \lambda_{N_r}^{-1} s_{N_r}'
$$
从上式可见，从第 $$i$$ 个发射波束方向打出的信号 $$s_i$$，幅度增益是 $$\lambda_i^{-1}$$，对应的能量增益就是 $$\lambda_i^{-2}$$ 。如果信道矩阵 $$\mathbf{H}$$ 的奇异值分布不均匀，存在很小的值，则意味着需要把宝贵的能量更多地用于弱子信道，以对抗信道衰减 。为了强制消除干扰，ZF 必须在信道条件最差的方向上“用力过猛”，导致整体功率效率（Power Efficiency）下降。

### 总的功率增益

设 $$\mathbf{F} = \mathbf{H}^H(\mathbf{H}\mathbf{H}^H)^{-1}$$，则从天线上打出的总功率为 ：
$$
\begin{aligned}
		\text{Tr}(E\{\mathbf{FS}(\mathbf{FS})^H\}) &= \text{Tr}(\mathbf{FF}^H) \\
		&= \text{Tr}(\mathbf{V}_1 \mathbf{\Lambda}_1^{-2} \mathbf{V}_1^H) \\
		&= \text{Tr}(\mathbf{\Lambda}_1^{-2} \mathbf{V}_1^H \mathbf{V}_1) = \text{Tr}(\mathbf{\Lambda}_1^{-2}) \\
		&= \sum_{j=1}^{N_r} \frac{1}{\lambda_j^2}
	\end{aligned}
$$
### 在每个天线上打出的功率

第 $$i$$ 个天线上打出的功率为：
$$
(\mathbf{FF}^H)_{ii} = (\mathbf{V}_1 \mathbf{\Lambda}_1^{-2} \mathbf{V}_1^H)_{ii} = \sum_{j=1}^{N_t} \frac{|v_{ij}|^2}{\lambda_j^2}
$$
可以看到，每根天线打出的功率都是奇异值平方倒数的加权和。



## 通过全微分推导 Wirtinger 导数定义

在复分析和信号处理中，Wirtinger 微积分（Wirtinger calculus）允许我们将复变量 $$z$$ 及其共轭 $$z^*$$ 视为相互独立的变量。这一看似人为的定义，实际上自然地源于“全微分”的概念。

设 $$f(z)$$ 是一个复函数，其中 $$z = x + iy$$。我们可以将 $$f$$ 看作是两个实变量的函数 $$f(x, y)$$。$$f$$ 的全微分（Total Differential）定义为：
$$
df = \frac{\partial f}{\partial x} dx + \frac{\partial f}{\partial y} dy
\tag{5}
$$
我们希望将这个全微分表示为关于 $$dz$$ 和 $$dz^*$$ 的形式。复坐标与实坐标之间的关系如下：
$$
z = x + iy    \quad \quad \quad  z^* = x - iy
$$
反解这些关系式，可以用 $$z$$ 和 $$z^*$$ 来表示 $$x$$ 和 $$y$$：
$$
x = \frac{z + z^*}{2} \quad \quad \quad y = \frac{z - z^*}{2i}
$$
对上述表达式求微分，我们得到 $$dx$$ 和 $$dy$$：
$$
\begin{aligned}
		dx &= \frac{1}{2} (dz + dz^*) \\
		dy &= \frac{1}{2i} (dz - dz^*) = -\frac{i}{2} (dz - dz^*)
	\end{aligned}
\tag{4}
$$
将公式 (4)代回全微分方程 (5) 中：
$$
\begin{aligned}
		df &= \frac{\partial f}{\partial x} \left[ \frac{1}{2} (dz + dz^*) \right] + \frac{\partial f}{\partial y} \left[ -\frac{i}{2} (dz - dz^*) \right] \\
		&= \frac{1}{2} \left( \frac{\partial f}{\partial x} dz + \frac{\partial f}{\partial x} dz^* \right) - \frac{i}{2} \left( \frac{\partial f}{\partial y} dz - \frac{\partial f}{\partial y} dz^* \right)
	\end{aligned}
$$
现在，我们将关于 $$dz$$ 和 $$dz^*$$ 的项分别合并：
$$
df = \frac{1}{2} \left( \frac{\partial f}{\partial x} - i \frac{\partial f}{\partial y} \right) dz + \frac{1}{2} \left( \frac{\partial f}{\partial x} + i \frac{\partial f}{\partial y} \right) dz^*
\tag{6}
$$
根据定义，复域中的全微分形式应满足 $$df = \frac{\partial f}{\partial z} dz + \frac{\partial f}{\partial z^*} dz^*$$。通过对比(6)式中的系数，我们得到了标准的 Wirtinger 导数定义：
$$
\begin{aligned}
		\frac{\partial f}{\partial z}   &\triangleq \frac{1}{2} \left( \frac{\partial f}{\partial x} - i \frac{\partial f}{\partial y} \right) \\
		\frac{\partial f}{\partial z^*} &\triangleq \frac{1}{2} \left( \frac{\partial f}{\partial x} + i \frac{\partial f}{\partial y} \right)
	\end{aligned}
$$
### $$\frac{\partial}{\partial z^*} (z z^*) = z$$ 的严格证明

在标准的数学分析或复变函数中，我们通常用 $$z$$ 表示复变量。我们利用 Wirtinger 导数的定义来严格证明以下恒等式, 也就是相当于可以把 $$z$$  和 $$^*$$  看成相互独立的微分变量来对待 ：
$$
\frac{\partial}{\partial z^*} (z z^*) = z
$$
**证明：**

设复变量 $$z$$ 分解为实部 $$x$$ 和虚部 $$y$$：
$$
z = x + i y, \quad \text{其中 } x, y \in \mathbb{R}
$$
目标函数是 $$z$$ 的模平方：
$$
f(z) = z z^* = |z|^2 = x^2 + y^2
\tag{7}
$$
首先，我们计算 $$f$$ 对实变量 $$x$$ 和 $$y$$ 的偏导数：
$$
\begin{aligned}
		\frac{\partial f}{\partial x} &= \frac{\partial}{\partial x} (x^2 + y^2) = 2x \\
		\frac{\partial f}{\partial y} &= \frac{\partial}{\partial y} (x^2 + y^2) = 2y
	\end{aligned}
$$
接下来，应用关于共轭变量 $$z^*$$ 的 Wirtinger 导数定义：
$$
\frac{\partial f}{\partial z^*} = \frac{1}{2} \left( \frac{\partial f}{\partial x} + i \frac{\partial f}{\partial y} \right)
$$
将$$f$$的表达式 (7) 代入上式有：
$$
\begin{aligned}
		\frac{\partial}{\partial z^*} (z z^*) &= \frac{1}{2} (2x + i 2y) \\
		&= \frac{1}{2} \cdot 2 (x + i y) \\
		&= x + i y
	\end{aligned}
$$
由于 $$z = x + iy$$，证得：
$$
\frac{\partial}{\partial z^*} (z z^*) = z
$$
同理可证（利用 $$\frac{\partial}{\partial z} = \frac{1}{2}(\frac{\partial}{\partial x} - i\frac{\partial}{\partial y})$$）：
$$
\frac{\partial}{\partial z} (z z^*) = z^*
$$
因此，可以把 z 和 z 的共轭看成两个不同的变量来处理。
$$\square$$
### 标量对矩阵的微分

在矩阵求导（针对标量函数 $$f$$）中，我们寻找满足下面形式的矩阵 $$\mathbf A$$：
$$
df = \text{tr}(\mathbf A^{\text T} d\mathbf X)
$$
这里的 $$\mathbf A$$ 就是梯度（导数）。

### 全微分公式的完整形式

在 Wirtinger 微积分（复矩阵微积分）中，对于一个标量函数 $$f(\mathbf{X})$$，其全微分 $$df$$ 由两部分组成：一部分与 $$d\mathbf{X}$$ 相关，另一部分与 $$d\mathbf{X}^*$$ 相关。

完整的全微分公式如下：
$$
df = \underbrace{\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}} \right)^{\text{T}} d\mathbf{X} \right)}_{\text{关于 } \mathbf{X} \text{ 的变化项}} + \underbrace{\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}^*} \right)^{\text{T}} d\mathbf{X}^* \right)}_{\text{关于 } \mathbf{X}^* \text{ 的变化项}}
$$
其中：
\begin{itemize}
	\item 第一项描述了当 $$\mathbf{X}^*$$ 固定不变，仅改变 $$\mathbf{X}$$ 时 $$f$$ 的变化。
	\item 第二项描述了当 $$\mathbf{X}$$ 固定不变，仅改变 $$\mathbf{X}^*$$ 时 $$f$$ 的变化。
\end{itemize}

### 为什么我们只关注包含 $$d\mathbf{X}^*$$ 的那一项？

在推导关于共轭矩阵 $$\mathbf{X}^*$$ 的梯度 $$\frac{\partial f}{\partial \mathbf{X}^*}$$ 时，我们通常会直接忽略公式 (1) 中的第一项。这基于以下两个核心理由：

**1. 线性独立性 (Linear Independence)**
在 Wirtinger 微积分体系下，微分 $$d\mathbf{X}$$ 和 $$d\mathbf{X}^*$$ 被在数学上定义为**相互独立的基底**（类似于实数域中的 $$dx$$ 和 $$dy$$ 是独立的）。

当我们要求解关于 $$\mathbf{X}^*$$ 的偏导数时，我们的目标是寻找 $$d\mathbf{X}^*$$ 前面的线性系数矩阵。由于 $$d\mathbf{X}$$ 与 $$d\mathbf{X}^*$$ 独立，第一项（包含 $$d\mathbf{X}$$ 的项）不会对 $$d\mathbf{X}^*$$ 的系数产生任何影响。因此，我们可以只提取第二项进行系数匹配。

**2. 实值函数的共轭对称性 (Conjugate Symmetry)**
在无线通信和信号处理中，目标函数 $$f$$（如功率、信噪比、均方误差）通常是**实数值**（Real-valued），即 $$f \in \mathbb{R}$$。

对于实值函数，其关于 $$\mathbf{X}$$ 的导数和关于 $$\mathbf{X}^*$$ 的导数存在如下共轭关系：
$$
\frac{\partial f}{\partial \mathbf{X}} = \left( \frac{\partial f}{\partial \mathbf{X}^*} \right)^*
$$
这意味着两部分包含的信息本质上是相同的。只要计算出其中一项（通常计算 $$\frac{\partial f}{\partial \mathbf{X}^*}$$ 更为方便，因为它可以直接用于梯度下降更新），另一项也就自然确定了。

### 总结
在使用微分法求解矩阵梯度时，我们的标准操作流程是：

1) 对函数 $$f$$ 求微分 $$df$$。

2) 利用迹的性质（如 $$\text{tr}(\mathbf{AB}) = \text{tr}(\mathbf{BA})$$），将表达式整理为 $$\text{tr}(\mathbf{G}^{\text{T}} d\mathbf{X}^*)$$ 的形式。

3) 忽略所有包含 $$d\mathbf{X}$$ 的项。

4) 此时，$$\mathbf{G}$$ 即为所求的导数 $$\frac{\partial f}{\partial \mathbf{X}^*}$$。

### 例子

**例子1** 我们来求解 $$\text{tr}(X^H A B)$$ 对 $$X^*$$  的偏导数，我们想办法凑出来类似于这样的形式 $$\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}^*} \right)^{\text{T}} d\mathbf{X}^* \right)$$
$$
\begin{aligned}
		\text d({\text{tr}(X^H A B)}) &=  \text d({\text{tr}(X^H A B)^{\text T}})    \\
		&=  \text d(\text{tr}(B^{\text T} A^{\text T} X^*) \\
		&=> \text{tr}(B^{\text T} A^{\text T}  \text d X^*) \\
	\end{aligned}
$$
则：
$$
\frac{\partial (\text{tr}(X^H A B))}{\partial \mathbf{X}^*}  = (B^{\text T} A^{\text T})^{\text T} = A B
$$
也可以这样推导，更简洁，即把待微分以外的变量都弄到一起，看成一个整体，这样就很容易看出来结果：
$$
\begin{aligned}
		\text d({\text{tr}(X^H A B)}) &=  \text d(\text{tr}(( A B)X^H))    \\
		&= \text d(\text{tr}(( A B)X^H))^{\text T}) \\
		&= \text d(\text{tr}(X^* (AB)^{\text T} )) \\
		&= \text d(\text{tr}( (AB)^{\text T} ) X^* ) \\
		&=> \text{tr}( (AB)^{\text T}  \text d X^* )
	\end{aligned}
$$
则可以看出 $$AB$$ 就是想要的偏微分结果。


**例子2** 我们来求解 $$\text{tr}(A X X^H B)$$ 对 $$X^*$$  的偏导数，我们想办法凑出来类似于这样的形式 $$\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}^*} \right)^{\text{T}} d\mathbf{X}^* \right)$$
$$
\begin{aligned}
		\text d({\text{tr}(A X X^H B)}) &=    \text d({\text{tr}((A X X^H B)^{\text T})}) \\
		&=  \text d({\text{tr}(B^{\text T} X^* X^{\text T} A^{\text T})}) \\
		&=\text d(\text{tr}(X^{\text T}  A^{\text T}B^{\text T} X^*  )  \quad \quad \quad {\text{迹的循环不变性}}  \\
		&=> \text{tr}(X^{\text T}  A^{\text T}B^{\text T}  \text d X^* ) \\
	\end{aligned}
$$
则：
$$
\frac{\partial (\text{tr}(A X X^H B)}{\partial \mathbf{X}^*}  = (X^{\text T}  A^{\text T}B^{\text T})^{\text T} = B  A X
$$
$$\text{tr}(A X X^H B)$$ 对 $$X$$  的偏导数，我们想办法凑出来类似于这样的形式 $$\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}} \right)^{\text{T}} d\mathbf{X} \right)$$
$$
\begin{aligned}
		\text d({\text{tr}(A X X^H B)}) &=   \text d(\text{tr}(X^{\text H}  B A  X  )  \quad \quad \quad {\text{迹的循环不变性}}  \\
		&=> \text{tr}(X^{\text H}  B A  \text d X ) \\
	\end{aligned}
$$
则：
$$
\frac{\partial (\text{tr}(A X X^H B)}{\partial \mathbf{X}}  = (X^{\text H}  B A)^{\text T} = A^{\text T}  B^{\text T} X^*
$$
则全微分就可以表示为：
$$
\begin{aligned}
		\text d(A X X^H B) &=  
		\underbrace{\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}} \right)^{\text{T}} d\mathbf{X} \right)}_{\text{关于 } \mathbf{X} \text{ 的变化项}} + \underbrace{\text{tr}\left( \left( \frac{\partial f}{\partial \mathbf{X}^*} \right)^{\text{T}} d\mathbf{X}^* \right)}_{\text{关于 } \mathbf{X}^* \text{ 的变化项}}  \\
		\\
		&= \text{tr}(A^{\text T}  B^{\text T} X^* \text d X) +  \text{tr}( B  A X \text d X^*)
	\end{aligned}
$$
# 线性预编码: MMSE
## 含噪声且无功率约束下 MMSE 预编码的推导及其方程解的性质分析

考虑一个下行链路多用户 MIMO（MU-MIMO）系统，基站（BS）配置有 $$N_t$$ 根发射天线，同时服务 $$K$$ 个单天线用户。在此我们假设发射天线数大于被调度的用户总数（$$N_t > K$$）。

令 $$\mathbf{s} \in \mathbb{C}^{K \times 1}$$ 表示发射的数据符号向量，满足零均值及功率为1，即 $$\mathbb{E}[\mathbf{s}] = \mathbf{0}$$ 且 $$\mathbb{E}[\mathbf{s}\mathbf{s}^H] = \mathbf{I}_K$$。线性预编码矩阵定义为 $$\mathbf{W} \in \mathbb{C}^{N_t \times K}$$，从而得到基站实际发射的信号向量 $$\mathbf{x} = \mathbf{W}\mathbf{s} \in \mathbb{C}^{N_t \times 1}$$。用户终端接收到的联合信号向量 $$\mathbf{y} \in \mathbb{C}^{K \times 1}$$ 可以表示为：
$$
\mathbf{y} = \mathbf{H}\mathbf{x} + \mathbf{n} = \mathbf{H}\mathbf{W}\mathbf{s} + \mathbf{n}
$$
其中 $$\mathbf{H} \in \mathbb{C}^{K \times N_t}$$ 代表下行链路的信道矩阵。$$\mathbf{n} \in \mathbb{C}^{K \times 1}$$ 是用户端接收到的加性高斯白噪声向量，满足 $$\mathbb{E}[\mathbf{n}] = \mathbf{0}$$ 且 $$\mathbb{E}[\mathbf{n}\mathbf{n}^H] = \sigma^2 \mathbf{I}_K$$，其中 $$\sigma^2$$ 表示单天线接收噪声功率。我们假设数据符号 $$\mathbf{s}$$ 与噪声 $$\mathbf{n}$$ 彼此统计独立，即 $$\mathbb{E}[\mathbf{s}\mathbf{n}^H] = \mathbf{0}$$。

MMSE 预编码相比与 MMSE 均衡，从推导公式的角度来看，要复杂很多，我们需要分三种情况逐步推进地来推导最终的 MMSE 预编码公式。


我们先来考虑第一种情形，就是最直观的最小均方误差的定义，当然，假设接收端没有做任何的均衡或者增益控制。马优化目标就是最小化原始发送数据 $$\mathbf{s}$$ 与接收信号 $$\mathbf{y}$$ 之间的均方误差。定义误差向量为 $$\mathbf{e} = \mathbf{y} - \mathbf{s}$$，则其目标(代价)函数 $$J(\mathbf{W})$$ 构建如下：
$$
J(\mathbf{W}) = \mathbb{E}\left[ \|\mathbf{y} - \mathbf{s}\|^2 \right] = \text{Tr}\left( \mathbb{E}\left[ (\mathbf{y} - \mathbf{s})(\mathbf{y} - \mathbf{s})^H \right] \right)
$$
展开括号内部的矩阵期望项，利用符号与噪声的独立性，交叉项期望化为零，可得：
$$
\mathbb{E}\left[ (\mathbf{y} - \mathbf{s})(\mathbf{y} - \mathbf{s})^H \right] &= \mathbb{E}\left[ (\mathbf{H}\mathbf{W}\mathbf{s} + \mathbf{n} - \mathbf{s})(\mathbf{H}\mathbf{W}\mathbf{s} + \mathbf{n} - \mathbf{s})^H \right] \nonumber \\
	&= \mathbb{E}\left[ [(\mathbf{H}\mathbf{W} - \mathbf{I}_K)\mathbf{s} + \mathbf{n}] [(\mathbf{H}\mathbf{W} - \mathbf{I}_K)\mathbf{s} + \mathbf{n}]^H \right] \nonumber \\
	&= (\mathbf{H}\mathbf{W} - \mathbf{I}_K) \mathbb{E}[\mathbf{s}\mathbf{s}^H] (\mathbf{H}\mathbf{W} - \mathbf{I}_K)^H + \mathbb{E}[\mathbf{n}\mathbf{n}^H] \nonumber \\
	&= \mathbf{H}\mathbf{W}\mathbf{W}^H\mathbf{H}^H - \mathbf{H}\mathbf{W} - \mathbf{W}^H\mathbf{H}^H + \mathbf{I}_K + \sigma^2 \mathbf{I}_K
$$
则把代价函数对共轭预编码矩阵 $$\mathbf{W}^*$$ 求导。由于噪声协方差项 $$\sigma^2 \mathbf{I}_K$$ 与 $$\mathbf{W}^*$$ 无关，其导数为零。令整体梯度为零以寻求最优解：
$$
\frac{\partial J(\mathbf{W})}{\partial \mathbf{W}^*} = \mathbf{H}^H\mathbf{H}\mathbf{W} - \mathbf{H}^H = \mathbf{0}
$$
整理后得到：
$$
\mathbf{H}^H\mathbf{H}\mathbf{W} = \mathbf{H}^H
\tag{8}
$$
根据矩阵乘法的秩性质，可知 $$\text{Rank}(\mathbf{H}^H\mathbf{H}) = \text{Rank}(\mathbf{H}) \le \min(K, N_t) = K$$。由于 $$K < N_t$$，这意味着系数矩阵 $$\mathbf{H}^H\mathbf{H}$$ 存在严重的秩亏缺（Rank Deficient），在数学上严格不可逆，方程 (5) 依然缺乏唯一的确定解,而是由无数个解。

### 基于奇异值分解的代数解构

为了从结构上严谨地剖析这种矩阵不可逆的本质，我们对信道矩阵 $$\mathbf{H}$$ 进行奇异值分解（SVD）：
$$
\mathbf{H} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^H = \mathbf{U} \begin{bmatrix} \mathbf{\Sigma}_K & \mathbf{0}_{K \times (N_t-K)} \end{bmatrix} \begin{bmatrix} \mathbf{V}_K^H \\ \mathbf{V}_0^H \end{bmatrix}
$$
其中 $$\mathbf{U} \in \mathbb{C}^{K \times K}$$ 和 $$\mathbf{V} \in \mathbb{C}^{N_t \times N_t}$$ 均为酉矩阵，而 $$\mathbf{V}_0 \in \mathbb{C}^{N_t \times (N_t-K)}$$ 构成了信道矩阵的右零空间（Null Space），满足 $$\mathbf{H}\mathbf{V}_0 = \mathbf{0}$$。

将公式 (6) 代入系数矩阵 $$\mathbf{H}^H\mathbf{H}$$ 中，可以得到：
$$
\mathbf{H}^H\mathbf{H} = \mathbf{V} \mathbf{\Sigma}^H \mathbf{\Sigma} \mathbf{V}^H = \mathbf{V} \begin{bmatrix} \mathbf{\Sigma}_K^2 & \mathbf{0} \\ \mathbf{0} & \mathbf{0}_{(N_t-K) \times (N_t-K)} \end{bmatrix} \mathbf{V}^H
$$
由于对角线矩阵的后半段出现了 $$N_t - K$$ 个绝对的零，确证了该系统的奇异性。

假设已经存在一个能够使当前含噪系统均方误差最小化的有效预编码矩阵 $$\mathbf{W}_{\text{good}}$$。由于非平凡零空间的存在，我们可以在该预编码矩阵后任意叠加一个映射向零空间的任意系数矩阵 $$\Delta \in \mathbb{C}^{(N_t-K) \times K}$$，从而构建一个新的预编码矩阵 $$\mathbf{W}_{\text{new}} = \mathbf{W}_{\text{good}} + \mathbf{V}_0 \Delta$$将其带回方程 (8) 的左端进行检验：
$$
\mathbf{H}^H\mathbf{H}\mathbf{W}_{\text{new}} = \mathbf{H}^H\mathbf{H}\mathbf{W}_{\text{good}} + \mathbf{H}^H(\mathbf{H}\mathbf{V}_0)\Delta = \mathbf{H}^H\mathbf{H}\mathbf{W}_{\text{good}}
$$
由于矩阵乘积投影 $$\mathbf{H}\mathbf{V}_0$$ 的结果完全恒等于 $$\mathbf{0}$$，因此该方程组仍有无限个等效解。

### 物理直觉解释

从物理空间的现实视角来看，含噪且无功率约束的方程缺乏唯一解的物理根源可以归结为以下两点：
\begin{itemize}
	\item **发射自由度供过于求：** 边界条件 $$N_t > K$$ 意味着基站发射端可提供的空间控制源数量，远远超过了接收端用户所施加的物理约束。基站拥有富余的空间通道维度，这使得它可以通过无数种不同的天线相位和振幅形态的组合，来达到完全相同的多用户目标接收要求。
	\item **噪声无法惩罚零空间发射：** 在本模型中，虽然接收端存在功率为 $$\sigma^2$$ 的随机背景噪声，但数学优化目标是对预编码矩阵 $$\mathbf{W}$$ 进行调整。基站如果往信道的右零空间 $$\mathbf{V}_0$$ 方向发射任意强度的电磁波信号，其波束在穿过空间到达终端天线时，由于相位刚好相反，会发生物理上的彻底相消干涉。这意味着零空间方向的信号不会服务于用户，同时也完全不会转化为多用户串扰或额外的噪声放大项。
\end{itemize}


### 与 MMSE 均衡的对比分析

在之前讨论MMSE 均衡时，是直接对代价函数求导从而直接得到 MMSE 均衡公式的，解是唯一的。均衡的核心在于接收端（用户侧）尝试设计一个线性变换矩阵 $$\mathbf{G} \in \mathbb{C}^{K \times K}$$，对已经穿过信道并混入噪声的接收信号 $$\mathbf{y}$$ 进行后处理，以恢复出原始符号 $$\mathbf{s}$$，即估计值为 $$\hat{\mathbf{s}} = \mathbf{G}\mathbf{y}$$。

为了简化此处假设发射端未进行预编码处理（即发射信号 $$\mathbf{x} = \mathbf{s}$$，发射信号协方差矩阵满足 $$\mathbb{E}[\mathbf{s}\mathbf{s}^H] = \mathbf{I}_K$$）。则用户终端接收到的联合观测信号向量为：
$$
\mathbf{y} = \mathbf{H}\mathbf{s} + \mathbf{n}
$$
MMSE 均衡的优化目标是最小化估计误差 $$\mathbf{e} = \hat{\mathbf{s}} - \mathbf{s} = \mathbf{G}\mathbf{y} - \mathbf{s}$$ 的均方误差，其代价函数 $$J(\mathbf{G})$$ 定义如下：
$$
J(\mathbf{G}) = \mathbb{E}\left[ \|\mathbf{G}\mathbf{y} - \mathbf{s}\|^2 \right] = \text{Tr}\left( \mathbb{E}\left[ (\mathbf{G}\mathbf{y} - \mathbf{s})(\mathbf{G}\mathbf{y} - \mathbf{s})^H \right] \right)
$$
将公式 (9) 显式带入误差矩阵的内积项中，完全展开可得：
$$
(\mathbf{G}\mathbf{y} - \mathbf{s})(\mathbf{G}\mathbf{y} - \mathbf{s})^H &= \left[ (\mathbf{G}\mathbf{H} - \mathbf{I}_K)\mathbf{s} + \mathbf{G}\mathbf{n} \right] \left[ \mathbf{s}^H(\mathbf{G}\mathbf{H} - \mathbf{I}_K)^H + \mathbf{n}^H\mathbf{G}^H \right] \nonumber \\
	&= (\mathbf{G}\mathbf{H} - \mathbf{I}_K)\mathbf{s}\mathbf{s}^H(\mathbf{G}\mathbf{H} - \mathbf{I}_K)^H \nonumber \\
	&+ (\mathbf{G}\mathbf{H} - \mathbf{I}_K)\mathbf{s}\mathbf{n}^H\mathbf{G}^H + \mathbf{G}\mathbf{n}\mathbf{s}^H(\mathbf{G}\mathbf{H} - \mathbf{I}_K)^H \nonumber \\
	&+ \mathbf{G}\mathbf{n}\mathbf{n}^H\mathbf{G}^H
$$
利用数据符号 $$\mathbf{s}$$ 与接收噪声 $$\mathbf{n}$$ 统计独立的物理特性，可知交叉项的期望恒为零矩阵（即 $$\mathbb{E}[\mathbf{s}\mathbf{n}^H] = \mathbf{0}$$）。代入各自的自相关矩阵 $$\mathbb{E}[\mathbf{s}\mathbf{s}^H] = \mathbf{I}_K$$ 和 $$\mathbb{E}[\mathbf{n}\mathbf{n}^H] = \sigma^2 \mathbf{I}_K$$，对公式 (11) 整体求解统计期望，化简为：
$$
\mathbb{E}\left[ (\mathbf{G}\mathbf{y} - \mathbf{s})(\mathbf{G}\mathbf{y} - \mathbf{s})^H \right] = \mathbf{G}\mathbf{H}\mathbf{H}^H\mathbf{G}^H - \mathbf{G}\mathbf{H} - \mathbf{H}^H\mathbf{G}^H + \mathbf{I}_K + \sigma^2 \mathbf{G}\mathbf{G}^H
$$
将公式 (12) 带回迹函数中，利用维廷格（Wirtinger）微积分对共轭均衡矩阵 $$\mathbf{G}^*$$ 求导，并令梯度为零矩阵以寻求最优解：
$$
\frac{\partial J(\mathbf{G})}{\partial \mathbf{G}^*} = \mathbf{G}\mathbf{H}\mathbf{H}^H - \mathbf{H}^H + \sigma^2 \mathbf{G} = \mathbf{0}
$$
通过提取公因式并移项合并，解得包含噪声项的标准的 MMSE 线性均衡矩阵公式：
$$
\mathbf{G}_{\text{MMSE}} = \mathbf{H}^H(\mathbf{H}\mathbf{H}^H + \sigma^2 \mathbf{I}_K)^{-1}
$$
**预编码与均衡的代数及物理非对称性对比：**

通过对比预编码的方程 $$\mathbf{H}^H\mathbf{H}\mathbf{W} = \mathbf{H}^H$$ 与均衡解公式 ：
\begin{itemize}
	\item **正则化项的命运差异：** 在预编码推导中，噪声项是孤立加在方括号外的，在对发射变换矩阵 $$\mathbf{W}^*$$ 求导时，因该噪声项不包含待求变量而被算法直接抹消（导数为 $$\mathbf{0}$$），未能进入左侧的待逆系数矩阵。然而在均衡推导中，由于均衡器直接作用于含有噪声的观测信号，噪声与未知矩阵 $$\mathbf{G}^H$$ 紧密结合（表现为 $$\sigma^2 \mathbf{G}\mathbf{G}^H$$）。求导后，噪声功率 $$\sigma^2 \mathbf{I}_K$$ 成功保留并化身成了对角线上的加固补丁。
	\item **矩阵满秩与解的唯一性：** 预编码的待逆系数矩阵 $$\mathbf{H}^H\mathbf{H}$$ 维度是 $$N_t \times N_t$$，在空间自由度过剩（$$N_t > K$$）时由于信道右零空间的存在，必然发生严重的秩亏缺，导致其包含一个无限的等效解空间。而均衡的待逆矩阵 $$(\mathbf{H}\mathbf{H}^H + \sigma^2 \mathbf{I}_K)$$ 维度是 $$K \times K$$，只要接收端存在微小的随机背景噪声（$$\sigma^2 > 0$$），该矩阵便是严格正定且满秩的，从而从代数上确保了均衡矩阵 $$\mathbf{G}$$ 拥有全局唯一的确定性数学解。
	\item **物理制约真相：** 预编码属于“前处理”技术。在没有发射功率限制的假设下，基站可以无成本地向用户天线处由于相消干涉而完全听不见的零空间灌注任意大小的能量而不受惩罚。反之，均衡属于“后处理”技术，接收端面对的是“既成事实”的混噪信号。任何企图放大有用信号、消除信道畸变的均衡变换，都会不可避免地按同等比例放大背景噪声（即噪声增强效应）。为了防止噪声爆炸崩塌整个系统，数学优化被迫在“空间消除多用户干扰”与“抑制背景噪声放大”之间做出了唯一确切的平衡。
\end{itemize}

## MMSE Precoder 推导（含总功率约束）

现在我们把分析推进到第二种情况，考虑更多因素，现在我们把功率约束考虑进去，即要求基站的总发射功率满足：
$$
\text{tr}(\mathbf{W}\mathbf{W}^H) \leq P
$$
其中 $$P$$ 为基站的最大总发射功率。

则MMSE 预编码器的设计目标就变成 "考虑功率约束条件下的最小化所有用户的总均方误差（MSE）"，数学表示为：
$$
\begin{aligned}
		\min_{\mathbf{W}} \quad & \mathbb{E}\left[\|\mathbf{s} - \mathbf{H}\mathbf{W}\mathbf{s} - \mathbf{n}\|^2\right] \\
		\text{s.t.} \quad & \text{tr}(\mathbf{W}\mathbf{W}^H) \leq P
	\end{aligned}
$$
### 展开目标函数
将目标函数（MSE）进行展开：
$$
\text{MSE} &= \mathbb{E}\left[\|\mathbf{s} - \mathbf{H}\mathbf{W}\mathbf{s} - \mathbf{n}\|^2\right] \nonumber \\
	&= \text{tr}\left((\mathbf{I} - \mathbf{H}\mathbf{W})(\mathbf{I} - \mathbf{H}\mathbf{W})^H\right) + \sigma^2 K \nonumber \\
	&= \text{tr}\left(\mathbf{I} - \mathbf{H}\mathbf{W} - \mathbf{W}^H\mathbf{H}^H + \mathbf{H}\mathbf{W}\mathbf{W}^H\mathbf{H}^H\right) + \sigma^2 K
$$
### 拉格朗日乘子法
引入拉格朗日乘子 $$\lambda \geq 0$$，构造如下拉格朗日函数：
$$
\mathcal{L}(\mathbf{W}, \lambda) = \text{tr}\left(\mathbf{I} - \mathbf{H}\mathbf{W} - \mathbf{W}^H\mathbf{H}^H + \mathbf{H}\mathbf{W}\mathbf{W}^H\mathbf{H}^H\right) + \sigma^2 K + \lambda\left(\text{tr}(\mathbf{W}\mathbf{W}^H) - P\right)
$$
### 求导并令其为零
利用矩阵微分原理，对 $$\mathbf{W}^*$$ 求导并令其为零：
$$
\frac{\partial \mathcal{L}}{\partial \mathbf{W}^*} = -\mathbf{H}^H + \mathbf{H}^H\mathbf{H}\mathbf{W} + \lambda \mathbf{W} = 0
$$
整理上式可得：
$$
(\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I}_{N_t})\mathbf{W} = \mathbf{H}^H
$$
### 求解
从而解得预编码矩阵为：
$$
\mathbf{W} = (\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I}_{N_t})^{-1}\mathbf{H}^H
\tag{9}
$$
### 确定正则化参数 $$\lambda$$
拉格朗日乘子 $$\lambda$$ 由功率约束的边界条件确定：
$$
\text{tr}(\mathbf{W}\mathbf{W}^H) = P
$$
将 $$\mathbf{W}$$ 的表达式代入：
$$
\text{tr}\left[(\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I})^{-1}\mathbf{H}^H\mathbf{H}(\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I})^{-1}\right] = P
$$
对信道矩阵 $$\mathbf{H}$$ 进行奇异值分解（SVD）$$\mathbf{H} = \mathbf{U}\mathbf{\Sigma}\mathbf{V}^H$$，设 $$\sigma_i$$ 为其奇异值，上式可化简为(具体推导见稍后的补充推导）：
$$
\sum_{i=1}^{\min(K,N_t)} \frac{\sigma_i^2}{(\sigma_i^2 + \lambda)^2} = P
$$
此方程关于的左侧函数，是关于 $$\lambda$$ 的单调递减，在实际中可以通过二分法或牛顿法进行数值求解。


我们来讨论一下这个算法得到的结果：

1） 如果功率 P 比较小，那么则需要 $$\lambda$$ 比较大，那么根据预编码矩阵的公式(9)，则 $$(\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I}_{N_t})^{-1}$$ 更接近一个对角矩阵，那么预编码器就接近于 $$\mathbf H^{\text H}$$，即匹配滤波预编码器，我们可以这样想，因为发射功率小，则各层之间相互干扰比较小，就优先控制信号的接收能量最大。

2）如果功率 P 比较大，那么则需要 $$\lambda$$ 比较小，那么根据预编码矩阵的公式(9)，则 $$(\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I}_{N_t})^{-1}$$ 更接近$$\mathbf{H}^H\mathbf{H}$$，那么预编码器就接近于 $$(\mathbf{H}^H\mathbf{H})^{-1}\mathbf H^{\text H}$$，即迫零预编码器，我们可以这样想，因为发射功率大，则各层之间相互干扰比较大，就优先控制层间干扰。

但是，这个算法的问题是没有考虑接收端噪声的情况，假如接收端噪声很大，那么即使发射功率很强，我们也希望能用匹配滤波预编码器，让接收到的信号能量最大化。我们在下一个算法中来讨论如何来考虑接收端的噪声。


### 补充推导：功率约束的 SVD 化简

**目标**
证明以下等式成立：
$$
\text{tr}\left( \mathbf W \mathbf W^{\text H}\right ) = \sum_{i=1}^{\min(K,N_t)} \frac{\sigma_i^2}{(\sigma_i^2 + \lambda)^2}
$$
证明：

已知 $$\mathbf{W} = (\mathbf{H}^H\mathbf{H} + \lambda\mathbf{I})^{-1}\mathbf{H}^H$$，因为 $$\text{tr}(\mathbf{W}\mathbf{W}^{\text H}) = \text{tr}(\mathbf{W}^H\mathbf{W})$$， 所以，我们直接计算 $$\text{tr}(\mathbf{W}^H\mathbf{W})$$  ：
$$
\mathbf{W}^H\mathbf{W} &= \mathbf{H}(\mathbf{H}^H\mathbf{H} + \lambda\mathbf{I})^{-1}(\mathbf{H}^H\mathbf{H} + \lambda\mathbf{I})^{-1}\mathbf{H}^H \nonumber \\
	&= \mathbf{H}\left[(\mathbf{H}^H\mathbf{H} + \lambda\mathbf{I})^{-1}\right]^2\mathbf{H}^H
$$
代入 SVD 分解 $$\mathbf{H} = \mathbf{U}\mathbf{\Sigma}\mathbf{V}^H$$：
$$
\mathbf{W}^H\mathbf{W} &= \mathbf{U}\mathbf{\Sigma}\mathbf{V}^H \left[\mathbf{V}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-1}\mathbf{V}^H\right]^2 \mathbf{V}\mathbf{\Sigma}^H\mathbf{U}^H \nonumber \\
	&= \mathbf{U}\mathbf{\Sigma}\mathbf{V}^H \mathbf{V}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-2}\mathbf{V}^H \mathbf{V}\mathbf{\Sigma}^H\mathbf{U}^H
$$
利用 $$\mathbf{V}^H\mathbf{V} = \mathbf{I}$$ 消除中间项：
$$
\mathbf{W}^H\mathbf{W} = \mathbf{U}\mathbf{\Sigma}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-2}\mathbf{\Sigma}^H\mathbf{U}^H
$$
两边取迹并利用循环性质 $$\text{tr}(\mathbf{U}\mathbf{X}\mathbf{U}^H) = \text{tr}(\mathbf{X})$$：
$$
\text{tr}(\mathbf{W}^H\mathbf{W}) = \text{tr}\left(\mathbf{\Sigma}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-2}\mathbf{\Sigma}^H\right)
$$
注意此处 $$\mathbf{\Sigma} \in \mathbb{C}^{K \times N_t}$$ 且 $$\mathbf{\Sigma}^H\mathbf{\Sigma} = \mathbf{\Lambda}$$ 为 $$N_t \times N_t$$ 矩阵，而 $$\mathbf{\Sigma}(\cdot)\mathbf{\Sigma}^H$$ 的结果为 $$K \times K$$ 矩阵。
再次利用性质 $$\text{tr}(\mathbf{A}\mathbf{B}) = \text{tr}(\mathbf{B}\mathbf{A})$$ 调整顺序：
$$
\text{tr}(\mathbf{W}^H\mathbf{W}) = \text{tr}\left(\mathbf{\Sigma}^H\mathbf{\Sigma}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-2}\right) = \text{tr}\left(\mathbf{\Lambda}(\mathbf{\Lambda}+\lambda\mathbf{I})^{-2}\right)
$$
由于全是对角矩阵，直接展开即得：
$$
\text{tr}(\mathbf{W}^H\mathbf{W}) = \sum_{i=1}^{r} \frac{\sigma_i^2}{(\sigma_i^2+\lambda)^2}
$$
## 考虑接收噪声影响的 MMSE 公式

上述发射滤波器并不依赖于接收端噪声的特性。因此，只要可用发射功率足够大，即便噪声功率非常高， 也会接近于迫零预编码算法。但我们已经知道，在低信噪比（SNR）区域内（即噪声大），匹配滤波预编码器的性能要优于 迫零预编码算法，因为 匹配滤波预编码 所获的的接收信号功率比 迫零预编码的算法 更大。

因此，我们引入一个 $$\beta$$ 参数，表示在接收端，对接收信号的一个简单增益控制，这个增益用来控制接收到的有用信号的功率，以便后续的解码。需要注意的是，这个增益在放大信号的同时，也会放大噪声，所以，引入 $$\beta$$ 参数就把接收噪声也考虑了进来。

则我们的最优化就变成：
$$
\begin{aligned}
		\{\mathbf{W}, \beta\} = & \arg \min_{\{\mathbf{W}, \beta\}} \text{E}\left[ \|  \mathbf{S} - \beta^{-1} \mathbf{Y}\|_2^2 \right ]\\
		& \text{s.t.} \quad \text{E}\left[ \|\mathbf{W}\|_2^2 \right] = E_{tr}
	\end{aligned}
$$
构造拉格朗日函数：
$$
\begin{aligned}
		\mathcal{L}(\mathbf{W}, \beta, \lambda)  &= \text{E}\left[ \|  \mathbf{S} - \beta^{-1} \mathbf{Y}\|_2^2 \right ] + \lambda (\text{tr}(\mathbf W \mathbf W^H) - E_{\text{tr}}) \\
		&=  \text{tr}\left( \mathbf I - \beta^{-1}\mathbf H \mathbf W - \beta^{-1}\mathbf W^{\text H} \mathbf  H^{\text H} +  \beta^{-2}\mathbf H \mathbf W \mathbf W^{\text H} \mathbf  H^{\text H}  + \beta^{-2}\sigma^2 \mathbf I \right )   + \lambda (\text{tr}(\mathbf W \mathbf W^H) - E_{\text{tr}})
	\end{aligned}
$$
对 $$\mathbf W$$ 求偏导：
$$
\frac{\partial \mathcal L(\mathbf{W}, \beta, \lambda)}{\partial \mathbf{W}} 
	= - \beta^{-1} \mathbf{H}^{\mathrm{T}} + \beta^{-2} \mathbf{H}^{\mathrm{T}} \  \mathbf{H}^{*} \mathbf{W}^{*}  + \lambda \mathbf{W}^{*}    = \mathbf{0}
$$
整理后可得：
$$
\mathbf W = \beta ( \mathbf H^{\text H}  \mathbf H +\lambda \beta^2 \mathbf I)^{-1} \mathbf H^{\text H}
$$
若令：
$$
\tilde{\mathbf W}  = ( \mathbf H^{\text H}  \mathbf H +\lambda \beta^2 \mathbf I)^{-1} \mathbf H^{\text H}
$$
则：
$$
\mathbf W = \beta \tilde{\mathbf W}
$$
代价函数对 $$\beta$$ 求导:
$$
\begin{aligned}
		&\frac{\partial \mathcal L(\mathbf{W}, \beta, \lambda)}{\partial \beta} \\&= \text{tr}\left(-(\mathbf{H}\mathbf{W}\mathbf{W}^{\text{H}}\mathbf{H}^{\text{H}} + \sigma^2 \mathbf{I}) + \beta\text{Re}(\mathbf{H}\mathbf{W})\right)2\beta^{-3} \\
		&= 0
	\end{aligned}
$$
令 $$\xi = \lambda \beta^2$$

则:
$$
\tilde W(\xi) = ( H^H  H+\xi I)^{-1} H^H
$$

$$
W(\xi) = \beta  \tilde W(\xi)
$$
那么 :
$$
\text{tr}\left(- (\beta^2\mathbf{H} \tilde{\mathbf{W}}(\xi) \tilde{\mathbf{W}}^{\text{H}}(\xi)\mathbf{H}^{\text{H}} + \sigma^2 \mathbf{I}) + \beta^2\text{Re}(\mathbf{H}\tilde{\mathbf{W}})\right)2\beta^{-3} \\
	= 0
$$
因为 ( 证明过程见本节附录 A)
$$
\text{tr}\left (\text{Re}(\mathbf{H}\tilde{\mathbf{W}}(\xi)) \right ) = \text{tr}(\mathbf{H}\tilde{\mathbf{W}}(\xi))
\tag{10}
$$
所以
$$
\text{tr}\left(\beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} + \sigma^2 \mathbf{I} - \beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\right) = 0
$$
进一步推导可得(证明过程见本节附录 B)：
$$
\begin{aligned}
		&\text{tr}\left(\beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} + \sigma^2 \mathbf{I} - \beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\right)  \\ 
		&=\text{tr}(\sigma^2 \mathbf{I}) - \xi\beta^2 \text{tr}(\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi))
	\end{aligned}
\tag{11}
$$
所以，进一步化简：
$$
\begin{aligned}
		&\text{tr}(\sigma^2 \mathbf{I}) - \xi\beta^2 \text{tr}(\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi))  \\
		&= \text{tr}(\sigma^2 \mathbf{I}) - \xi E_{\text{tx}}  \\
		&= K\sigma^2 - \xi E_{\text{tx}} 
		= 0
	\end{aligned}
$$
所以：
$$
\xi  = \frac{K\sigma^2}{E_{\text{tx}}}
$$
则
$$
\tilde{\mathbf W} =  ( \mathbf H^{\text H}  \mathbf H + \frac{K\sigma^2}{E_{\text{tx}}} \mathbf I)^{-1} \mathbf H^{\text H}
$$
和
$$
\mathbf W = \beta \tilde{\mathbf W } =  \beta( \mathbf H^{\text H}  \mathbf H + \frac{K\sigma^2}{E_{\text{tx}}} \mathbf I)^{-1} \mathbf H^{\text H}
$$
把上式带入功率约束条件：
$$
\text{tr}(\mathbf W \mathbf W^H) = \beta^2   \text{tr}(\tilde{\mathbf W} \tilde{\mathbf W}^H)  =  \beta^2 ||\tilde{\mathbf W}||_2^2 =  E_{\text{tx}}
$$
 

从而解出 $$\beta$$ 的表达式：
$$
\beta = \sqrt{  \frac{E_{\text{tx}}}{||\tilde{\mathbf W}||_2^2} }
$$
最终
$$
\mathbf W = \sqrt{E_{\text{tx}}} 
	\frac{\tilde{\mathbf W }}{\sqrt{||\tilde{\mathbf W}||_2^2} }
$$
其中：
$$
\tilde{\mathbf W} =  ( \mathbf H^{\text H}  \mathbf H + \frac{K\sigma^2}{E_{\text{tx}}} \mathbf I)^{-1} \mathbf H^{\text H}
$$
### 附录 (A)：公式 (10) 的证明

因为求 trace 与取实部的操作可以交换，那么
$$
\text{tr}\left (\text{Re}(\mathbf{H}\tilde{\mathbf{W}}(\xi)) \right )  = \text{Re}\left (\text{tr}(\mathbf{H}\tilde{\mathbf{W}}(\xi)) \right )
$$
因此，我们只需要证明 $$\text{tr}(\mathbf{H}\tilde{\mathbf{W}}(\xi))$$  是实数而不是复数即可，即需要证明：
$$
\left  [ \text{tr}(\mathbf{H}\tilde{\mathbf{W}}(\xi))  \right ]^*  = \text{tr}(\mathbf{H}\tilde{\mathbf{W}}(\xi))
$$
**证明**

将 $$\tilde{W}(\xi) = (H^{H}H+\xi I)^{-1}H^{H}$$ 代入，我们有：
$$
\text{tr}(A) = \text{tr}\left( H(H^{H}H+\xi I)^{-1}H^{H} \right)
$$
现在，让我们利用迹的循环性质（$$\text{tr}(XYZ) = \text{tr}(ZXY)$$）：
$$
\text{tr}(A) = \text{tr}\left( H^H H (H^H H + \xi I)^{-1} \right)
$$
令： $$B = H^H H$$  和 $$C = (H^H H + \xi I)^{-1}$$

显然 $$B$$, $$C$$  和 $$BC$$, $$CB$$   都是是埃尔米特（Hermitian）矩阵，即其共轭转置等于其自身。

根据矩阵转置不改变迹：
$$
\begin{aligned}
		\left (\text{tr}(CB) \right )^* &= \text{tr}\left ((CB)^{\text T} \right )^* \\
		& =  \text{tr}\left ((CB)^{\text H} \right )  \\
		&= \text{tr}(CB)
	\end{aligned}
$$
也可以简单地想， 埃尔米特（Hermitian）矩阵的对角线元素一定是实数，所以 埃尔米特（Hermitian）矩阵的迹（对角线元素之和）也是实数。
$$
\begin{bmatrix}
		a_{11} & a_{12} \\
		a_{21} &  a_{22}
	\end{bmatrix}^{\text H} 
	=      
	\begin{bmatrix}
		a_{11}^* & a_{21}^* \\
		a_{12}^* &  a_{22}^*
	\end{bmatrix}
	=
	\begin{bmatrix}
		a_{11} & a_{12} \\
		a_{21} &  a_{22}
	\end{bmatrix}
$$
所以  $$a_{11}^* = a_{11} \ , a_{22}^* = a_{22}$$



### 附录 (B)：方程 (11) 的证明

首先，对于
$$
\text{tr}\left(\beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} + \sigma^2 \mathbf{I} - \beta^2 \mathbf{H}\tilde{\mathbf{W}}(\xi)\right) 
	\label{}
$$
利用迹算子的线性性质，我们可以将噪声协方差项 $$\text{tr}(\sigma^2 \mathbf{I})$$ 分离出来，并将剩余的 $$\beta^2$$ 项组合在一起：
$$
\text{tr}(\sigma^2 \mathbf{I}) + \beta^2 \text{tr}\left( \mathbf{H}\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{H}\tilde{\mathbf{W}}(\xi) \right)
\tag{13}
$$
现在，从两项中直接提取出公因式 $$\mathbf{H}\tilde{\mathbf{W}}(\xi)$$。这可以得出：
$$
\text{tr}\left( \mathbf{H}\tilde{\mathbf{W}}(\xi) \left[ \tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{I} \right] \right)
\tag{12}
$$
根据定义：
$$
\tilde{\mathbf{W}}(\xi) = (\mathbf{H}^\text{H}\mathbf{H} + \xi\mathbf{I})^{-1}\mathbf{H}^\text{H}
$$
则：
$$
\tilde{\mathbf{W}}^\text{H}(\xi) = \mathbf{H}(\mathbf{H}^\text{H}\mathbf{H} + \xi\mathbf{I})^{-1}
$$
利用矩阵恒等式 $$\mathbf{A}(\mathbf{BA}+\xi\mathbf{I})^{-1} = (\mathbf{AB}+\xi\mathbf{I})^{-1}\mathbf{A}$$（其中 $$\mathbf{A}=\mathbf{H}$$ 且 $$\mathbf{B}=\mathbf{H}^\text{H}$$）得到：
$$
\tilde{\mathbf{W}}^\text{H}(\xi)= (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1}\mathbf{H}
$$
为了简化括号中的项 $$\left[ \tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{I} \right]$$，让我们利用通分的形式来重写单位矩阵 $$\mathbf{I}$$：
$$
\mathbf{I} = (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1}(\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})
$$
将两者相减：
$$
\tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{I} &= (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1} \left( \mathbf{H}\mathbf{H}^\text{H} - (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I}) \right) \nonumber \\
	&= -\xi (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1}
$$
将上式代回  (12) 有：
$$
\text{tr}\left( \mathbf{H}\tilde{\mathbf{W}}(\xi) \left[ \tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{I} \right] \right) 
	= 
	-\xi \text{tr}\left( \mathbf{H}\tilde{\mathbf{W}}(\xi) (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1} \right)
$$
对迹内部做循环移动， 上式推导为：
$$
-\xi \text{tr}\left( (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1} \mathbf{H} \cdot \tilde{\mathbf{W}}(\xi) \right)
$$
最前面的部分恰好是 $$\tilde{\mathbf{W}}^\text{H}(\xi)$$ ：
$$
\tilde{\mathbf{W}}^\text{H}(\xi) = (\mathbf{H}\mathbf{H}^\text{H} + \xi\mathbf{I})^{-1} \mathbf{H}
$$
将其替换后得到：
$$
-\xi \text{tr}\left( \tilde{\mathbf{W}}^\text{H}(\xi) \tilde{\mathbf{W}}(\xi) \right)
$$
再次应用循环性质将两项交换位置，恰好得出：
$$
-\xi \text{tr}\left( \tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi) \right)
$$
即得到：
$$
\text{tr}\left( \mathbf{H}\tilde{\mathbf{W}}(\xi) \left[ \tilde{\mathbf{W}}^\text{H}(\xi)\mathbf{H}^\text{H} - \mathbf{I} \right] \right)
	=
	-\xi \text{tr}\left( \tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi) \right)
$$
将之代回到公式 (13):
$$
\text{tr}(\sigma^2 \mathbf{I}) - \xi\beta^2 \text{tr}(\tilde{\mathbf{W}}(\xi)\tilde{\mathbf{W}}^\text{H}(\xi))
$$
## 后面两种预编码的对比和思考

对于第二种情况,即不考虑接收端噪声的情况，预编码公式：
$$
\mathbf{W}_2 = (\mathbf{H}^H\mathbf{H} + \lambda \mathbf{I}_{N_t})^{-1}\mathbf{H}^H
$$
对比第三种情况，考虑接收端噪声的情况，预编码公式为：
$$
\mathbf W_3 =  \beta( \mathbf H^{\text H}  \mathbf H + \frac{K\sigma^2}{E_{\text{tx}}} \mathbf I)^{-1} \mathbf H^{\text H}
$$
其中 $$\lambda$$ 和 $$\beta$$  为：
$$
\sum_{i=1}^K \frac{\sigma_i^2}{(\sigma_i^2 + \lambda)^2} \le E_{\text{tx}}
$$

$$
\beta = \sqrt{  \frac{E_{\text{tx}}}{||\tilde{\mathbf W}||_2^2} }
$$
**从功率分配的角度来看**  第二种情况的总分配功率,  可能小于目标的总功率, 即当  $$\lambda = 0$$ 时，总功率为
$$
\sum_{i=1}^K \frac{1}{\sigma_i^2 }
$$

如果上面的求和还是小于总可用功率，也不能把预编码矩阵乘以一个系数来抬高总发射功率，因为会导致在接收端接收到的信号在幅度上与原始信号不匹配，进而恶化均方误差。

第三种情况，总是能保证发射功能等于总功率，因为第三种情况下是假定接收端可以做增益控制的，即把接收端的增益控制统一考虑进来了，所以，发射端可以把有用的总功率都用上\cite{1468466}。