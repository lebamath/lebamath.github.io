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

## Spatial Smoothing MUSIC Algorithm 

In the **MUSIC (Multiple Signal Classification)** algorithm, **spatial smoothing** is commonly used to improve signal estimation accuracy and reduce the impact of noise.

Spatial smoothing is primarily employed to mitigate the issue of highly correlated signals (such as coherent signals). This is particularly important in **Direction of Arrival (DOA)** estimation for array antennas, where multiple signals may become coherent due to multipath propagation. This coherence leads to **rank deficiency** in the covariance matrix, preventing the MUSIC algorithm from correctly separating the signal and noise subspaces.

In the MUSIC algorithm, the covariance matrix of the received signal is calculated as follows (assuming the signal has a mean of zero):
$$
	\mathbf y(t)=\sum_{i=1}^M s_i(t) \mathbf a_i+ \mathbf n(t) 
\tag{1}
$$

The covariance matrix is:
$$
	\boldsymbol R_y=\boldsymbol A \boldsymbol R_s \boldsymbol A^H+\sigma^2 \boldsymbol I 
\tag{2}
$$




In the MUSIC algorithm [1][2], when deriving the formulas, we need to analyze the covariance matrix of the signal.
$$
    \textbf{R}_s = \textbf{E}[\mathbf{X} \mathbf{X}^H]
\tag{3}
$$

However, if the incident signals are fully correlated (i.e., they exhibit linear dependence, such as coherent signals in multipath propagation), the covariance matrix of these signals may become **singular (rank-deficient)**. As a result, the dimensionality of the signal subspace is reduced, causing traditional eigenvalue decomposition to fail in correctly separating the signal and noise subspaces.

Due to the correlation of the transmitted signals, the covariance matrix is not full rank, i.e.:
$$
    \text{rank}(\textbf{R}_s) < M
\tag{4}
$$

where $M$ is the actual number of beams.

To address this issue, **spatial smoothing** [3] is applied. This technique partitions the array antenna into multiple subarrays and computes the average of their covariance matrices. By doing so, it reduces the impact of signal correlation, allowing the MUSIC algorithm to remain effective.

### Spatial Smoothing Method

Assume we have a uniform linear array (ULA) with N elements, and there is coherence between the signals. We can perform Forward Spatial Smoothing (FSS) as follows:

**a) Divide into subarrays** Let the large array have N antennas, and we select L antennas as the size of the subarray. Multiple subarrays can then be constructed:

1st subarray: Elements [1, 2, 3, ..., L]
2nd subarray: Elements [2, 3, 4, ..., L+1]
...
(N−L+1)th subarray: Elements [N−L+1, ..., N]

**b) Compute the covariance matrix of each subarray** For the received signal of each subarray, calculate its covariance matrix:
$$
    \textbf{R}^{(i)}_y = \textbf{E}[\mathbf{Y}_i \mathbf{Y}^H_i]
\tag{5}
$$

**c) Calculate the average covariance matrix**

Compute the average of the covariance matrices for all subarrays:
$$
    \textbf{R}_{\text{avg}} = \frac{1}{N-L+1} 
        \sum_{i=1}^{N-L+1}\mathbf{R}^{(i)}_y
\tag{6}
$$

This reduces the coherence of the signals, making the resulting covariance matrix closer to full rank, thereby improving the performance of the MUSIC algorithm.

It is important to emphasize that the result of subarray smoothing is that the covariance matrix changes from [N, N] to [L, L]. The signal subspace dimension remains unchanged, but the noise subspace dimension is reduced. The impacts are:

- **Decreased resolution:** Since the covariance matrix becomes smaller, the available array aperture shrinks, and the angular resolution deteriorates.
- **Reduced detectable number of signals:** The original MUSIC algorithm can detect N−1 signals, but after spatial smoothing, only L−1 signal sources can be detected.

### Proof of Spatial Smoothing Method

Here, the received signal vector is represented as:
$$
    \textbf{Y} = 
    \begin{bmatrix}
    y_0 \\
    y_1 \\
    \vdots \\
    y_{(N-1)} \\
    \end{bmatrix}
\tag{7}
$$

The manifold vector is represented as:

$$
    \textbf{a}_m = 
    \begin{bmatrix}
    1 \\
    e^{j\theta_m} \\
    e^{j2\theta_m} \\
    \vdots \\
    e^{j(N-1)\theta_m} \\
    \end{bmatrix}
    =
    \begin{bmatrix}
    z^0_m \\
    z^1_m \\
    z^2_m \\
    \vdots \\
    z^{(N-1)}_m \\
    \end{bmatrix}
\tag{8}
$$

We use a window of length L to slice from Y from top to bottom, obtaining:
$$
    \textbf{Y}_0 = 
    \begin{bmatrix}
    y_0 \\
    y_1 \\
    \vdots \\
    y_{L-1} \\
    \end{bmatrix}
\tag{9}
$$

$$
    \textbf{Y}_1 = 
    \begin{bmatrix}
    y_1 \\
    y_2 \\
    \vdots \\
    y_L \\
    \end{bmatrix}
\tag{10}
$$

......

$$
    \textbf{Y}_{N-L+1} = 
    \begin{bmatrix}
    y_{N-L+1} \\
    y_{N-L+2} \\
    \vdots \\
    y_{N-1} \\
    \end{bmatrix}
\tag{11}
$$

For the B = N - L + 1 sub-vectors of the received signals mentioned above, compute their covariance matrix:

$$
    \textbf{R}^{(i)}_y =  \boldsymbol{A}^{(i)}  \boldsymbol{R}_s (\boldsymbol{A}^{(i)})^H + \sigma^2 \boldsymbol{I}
\tag{12}
$$

where:

$$
    \textbf{A}^{(i)} = 
    \begin{bmatrix}
        z^i_0  & z^i_1 & \cdots & z^i_{(M-1)}  \\
        z^{i+1}_0  & z^{i+1}_1 & \cdots & z^{i+1}_{(M-1)}  \\
        \vdots & \vdots & \ddots & \vdots \\
        z^{i+L-1}_0  & z^{i+L-1}_1 & \cdots & z^{i+L-1}_{(M-1)} 
    \end{bmatrix}
\tag{13}
$$

Decompose equation (13) into:

$$
    \begin{aligned}
    \textbf{A}^{(i)} &= 
    \begin{bmatrix}
        z^0_0  & z^0_1 & \cdots & z^0_{(M-1)}  \\
        z^1_0  & z^{1}_1 & \cdots & z^{1}_{(M-1)}  \\
        \vdots & \vdots & \ddots & \vdots \\
        z^{L-1}_0  & z^{L-1}_1 & \cdots & z^{L-1}_{(M-1)} 
    \end{bmatrix}
    \begin{bmatrix}
        z^i_0    & 0      & \cdots & 0  \\
        0      & z^i_1    & \cdots & 0  \\
        \vdots & \vdots & \ddots & \vdots \\
        0      & 0      & \cdots & z^i_{(M-1)} 
    \end{bmatrix}  \\
    &= \boldsymbol{A}^{(0)} 
        \begin{bmatrix}
        z^i_0    & 0      & \cdots & 0  \\
        0      & z^i_1    & \cdots & 0  \\
        \vdots & \vdots & \ddots & \vdots \\
        0      & 0      & \cdots & z^i_{(M-1)} 
    \end{bmatrix} \\
    &= \boldsymbol{A}^{(0)}  \boldsymbol{D}^{i}
    \end{aligned}
\tag{14}
$$

where：
$$
    \boldsymbol{D}^{i} =
    \begin{bmatrix}
        z^i_0    & 0      & \cdots & 0  \\
        0      & z^i_1    & \cdots & 0  \\
        \vdots & \vdots & \ddots & \vdots \\
        0      & 0      & \cdots & z^i_{(M-1)} 
    \end{bmatrix}
\tag{15}
$$

Substitute equation (14) into equation (12):

$$
    \textbf{R}^{(i)}_y =  \boldsymbol{A}^{(0)} \boldsymbol{D}^{i}   \boldsymbol{R}_s (\boldsymbol{D}^{i})^H (\boldsymbol{A}^{(0)})^H + \sigma^2 \boldsymbol{I}
\tag{16}
$$

Substitute equation (16) into equation (6):

$$
    \textbf{R}_{\text{avg}} = \frac{1}{B} 
    \left \{
    \boldsymbol{A}^{(0)} 
        \left [  
            \boldsymbol{R}_s +
             \boldsymbol{D}   \boldsymbol{R}_s (\boldsymbol{D})^H +
             \boldsymbol{D}^2   \boldsymbol{R}_s (\boldsymbol{D}^2)^H +
             \cdots
             \boldsymbol{D}^{N-L}   \boldsymbol{R}_s (\boldsymbol{D}^{N-L})^H
        \right ]
    (\boldsymbol{A}^{(0)})^H + \sigma^2 \boldsymbol{I}
    \right \}
\tag{17}
$$

We denote the middle part of the above equation as:

$$
    \tilde{\textbf{R}}_s = 
        \frac{1}{B}
        \left [  
            \boldsymbol{R}_s +
             \boldsymbol{D}   \boldsymbol{R}_s (\boldsymbol{D})^H +
             \boldsymbol{D}^2   \boldsymbol{R}_s (\boldsymbol{D}^2)^H +
             \cdots
             \boldsymbol{D}^{N-L}   \boldsymbol{R}_s (\boldsymbol{D}^{N-L})^H
        \right ]
\tag{18}
$$

Then, equation (17) can be written as:
$$
    \textbf{R}_{\text{avg}} =  
    \boldsymbol{A}^{(0)} \tilde{\textbf{R}}_s (\boldsymbol{A}^{(0)})^H + 
    \sigma^2 \boldsymbol{I}
\tag{19}
$$

Therefore, the problem now becomes proving:
$$
    \text{rank}(\tilde{\textbf{R}}_s) = M
\tag{20}
$$

That is, we need to prove that the covariance matrix of the newly constructed signal is full rank.

For convenience in writing, we will ignore the coefficient 1/B in equation (18), as it does not affect the rank analysis of the matrix.

We rewrite equation (18) as:
$$
    \tilde{\textbf{R}}_s = 
    \begin{bmatrix} \boldsymbol{I} & \boldsymbol{D} & \cdots & \boldsymbol{D}^{B-1}   \end{bmatrix}
    \begin{bmatrix}
    \boldsymbol{R}_s & 0 & \cdots & 0 \\
    0 & \boldsymbol{R}_s & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \boldsymbol{R}_s
    \end{bmatrix}
    \begin{bmatrix}
    I \\
    D^{H} \\
    \vdots \\
    (D^{B-1})^H
    \end{bmatrix}
\tag{21}
$$

We find a matrix of the same size such that the signal covariance matrix is decomposed as:
$$
    \boldsymbol{R}_s = C C^H
\tag{22}
$$

Then, the matrix in the middle of equation (21) can be decomposed as:
$$
    \begin{bmatrix}
    \boldsymbol{R}_s & 0 & \cdots & 0 \\
    0 & \boldsymbol{R}_s & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \boldsymbol{R}_s
    \end{bmatrix} =
    \begin{bmatrix}
    \boldsymbol{C} & 0 & \cdots & 0 \\
    0 & \boldsymbol{C} & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \boldsymbol{C}
    \end{bmatrix}
    \begin{bmatrix}
    \boldsymbol{C}^H & 0 & \cdots & 0 \\
    0 & \boldsymbol{C}^H & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \boldsymbol{C}^H
    \end{bmatrix}
\tag{23}
$$

let
$$
    \boldsymbol{G} = \begin{bmatrix} \boldsymbol{I} & \boldsymbol{D} & \cdots & \boldsymbol{D}^{B-1}   \end{bmatrix}
     \begin{bmatrix}
    \boldsymbol{C} & 0 & \cdots & 0 \\
    0 & \boldsymbol{C} & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    0 & 0 & \cdots & \boldsymbol{C}
    \end{bmatrix}
\tag{24}
$$

hen, equation (21) can be simplified as:：
$$
    \tilde{\textbf{R}}_s = \boldsymbol{G} \boldsymbol{G}^H
\tag{25}
$$

Next, we only need to prove that matrix $G$ is full rank.

Now, further derive equation (24):
$$
    \boldsymbol{G} = \begin{bmatrix}
    \boldsymbol{C} & \boldsymbol{DC} & \boldsymbol{D}^2\boldsymbol{C}
    & \cdots & \boldsymbol{D}^{B-1}\boldsymbol{C}
    \end{bmatrix}
\tag{26}
$$

Substitute equation (15) into (26), and also write out the coefficients of the $C$ matrix, then:
$$
    \boldsymbol{G} = 
    \begin{bmatrix}
    c_{00} & \cdots & c_{0,M-1} &  \cdots &  c_{0,0}z^{B-1}_0 & \cdots & c_{0,M-1}z^{B-1}_0\\
    \vdots & \ddots & \vdots & \ddots & \vdots & \ddots & \vdots\\
    c_{M-1,0} & \cdots & c_{M-1,M-1} &\cdots &  c_{M-1,0}z^{B-1}_{M-1} & \cdots & c_{M-1,M-1}z^{B-1}_{M-1}\\
    \end{bmatrix}
\tag{27}
$$

Rearranging the columns of equation (27) does not affect the rank of matrix $G$. Take every Mth column and group them together:
$$
    \boldsymbol{G} = 
    \begin{bmatrix}
    c_{00} & \cdots & c_{0,0}z^{B-1}_0 & \cdots & c_{0,M-1}  & \cdots & c_{0,,M-1}z^{B-1}_0\\
    \vdots & \ddots & \vdots & \ddots & \vdots &  \ddots & \vdots \\
    c_{M-1,0} & \cdots & c_{M-1,0}z^{B-1}_{M-1} & \cdots & c_{M-1,M-1} & \cdots & c_{M-,,M-1}z^{B-1}_{M-1}
    \end{bmatrix}
\tag{28}
$$

We define a row vector as:
$$
    \boldsymbol{b}_j = 
    \begin{bmatrix}
    1 & z_j & z^2_j & \cdots & z^{B-1}_j
    \end{bmatrix}
\tag{29}
$$

Then, equation (28) can be simplified as:
$$
    \boldsymbol{G} = 
    \begin{bmatrix}
    c_{00} \boldsymbol{b}_0 & c_{01} \boldsymbol{b}_0 & \cdots & c_{0,M-1} \boldsymbol{b}_0 \\
    \vdots & \vdots & \ddots & \vdots \\
    c_{M-1,0} \boldsymbol{b}_{M-1} & c_{M-1,1} \boldsymbol{b}_{M-1} & \cdots & c_{M-1,M-1} \boldsymbol{b}_{M-1} \\
    \end{bmatrix}
\tag{30}
$$

First, if these row vectors form a matrix, it is a Vandermonde matrix. Therefore, as long as the number of vectors $M$ is less than or equal to the dimension $B$ of the vector, these $M$ vectors are linearly independent, and the matrix they form is full rank, i.e., the rank is $M$.

Second, no row in matrix $C$ can be all zeros, because if it were, it would mean the energy of a transmitted signal is zero. Therefore, in matrix $G$, every row must have at least one non-zero row vector $b$. In the extreme case, if only the first subcolumn is left in matrix $G$, and the coefficients $c$ are all 1, then the matrix $G$ is full rank.
$$
    \boldsymbol{G} = 
    \begin{bmatrix}
    \boldsymbol{b}_0 & 0 & \cdots & 0 \\
    \vdots & \vdots & \ddots & \vdots \\
    \boldsymbol{b}_{M-1} & 0 & \cdots & 0 \\
    \end{bmatrix}
\tag{31}
$$

It is also full rank.

Thus, matrix $G$ is full rank, i.e., its rank is $M$.

### Intuitive Understanding from the Perspective of Column Vector Correlation

In equation (30), the rank of matrix $G$ might be misinterpreted as not being full rank because $C$ is not full rank (since we are considering signals with correlation). However, this is not the case. If we assume all transmitted signals are perfectly correlated, then the rank of $C$ is 1, and each column vector of $C$ is proportional to the others.

The first subcolumn of $G$ consists of the same column vector. The second subcolumn, however, is a linear combination of $D$ column vectors. Therefore, we cannot directly conclude that it is necessarily correlated with $C$. If the second subcolumn is $C$ multiplied by $D$, then $CD$ is also a linear combination of the column vectors of $C$, in which case $G$ would not be full rank.

Additionally, $DC$ can be seen as a linear combination of the row vectors of $C$. However, when combined with the first subcolumn of $C$, they are not aligned in rows but placed consecutively. This breaks the correlation between the vectors that are placed together. For example:
$$
    \boldsymbol{C} = 
    \begin{bmatrix}
        1 & 1  \\
        1 & 1
    \end{bmatrix}
\tag{32}
$$

$$
    \boldsymbol{D} = 
    \begin{bmatrix}
        e^{j\theta_1} & 0  \\
        0 & e^{j\theta_2}
    \end{bmatrix}
\tag{33}
$$


then：

$$
    \boldsymbol{G} = 
    \begin{bmatrix}
        1 & 1 & e^{j\theta_1} & e^{j\theta_1} \\
        1 & 1 & e^{j\theta_2} & e^{j\theta_2}
    \end{bmatrix}
\tag{34}
$$

As long as the two angles are not equal, the rank of matrix $G$ is 2.

If $DC$ in $G$ is replaced by $CD$, then:
$$
    \boldsymbol{G} = 
    \begin{bmatrix}
        1 & 1 & e^{j\theta_1} & e^{j\theta_2} \\
        1 & 1 & e^{j\theta_1} & e^{j\theta_2}
    \end{bmatrix}
\tag{35}
$$

Then, the rank of matrix $G$ becomes 1, and it is no longer full rank.

### An Example of Spatial Smoothing

Let N = 5, L = 3, and M = 2, i.e., there are 5 antennas, the subarray (or subspace) length is 3, and the true number of beams is 2. These two beams correspond to transmitted signals that are identical, i.e., completely correlated.

The manifold vectors corresponding to the two beams are:
$$
    \mathbf{a}_1 = 
    \begin{bmatrix}
        1 \\
        e^{j\theta_1} \\ 
        e^{j2\theta_1} \\ 
        e^{j3\theta_1} \\ 
        e^{j4\theta_1}
    \end{bmatrix} \quad \quad \quad \quad
    \mathbf{a}_1 = 
    \begin{bmatrix}
        1 \\
        e^{j\theta_2} \\ 
        e^{j2\theta_2} \\ 
        e^{j3\theta_2} \\ 
        e^{j4\theta_2}
    \end{bmatrix}
\tag{36}
$$

Since the signals are identical, we can assume the most extreme case where the two signals are exactly the same.
$$
    \mathbf{S} = 
    \begin{bmatrix}
        \sqrt{2}\\
        \sqrt{2}
    \end{bmatrix}
\tag{37}
$$

We can reasonably assume that the covariance matrix of the signals is:

$$
    \mathbf{R}_s = 
    \begin{bmatrix}
        2 & 2 \\
        2 & 2
    \end{bmatrix}
\tag{38}
$$


Then, the signal received by the first subarray is (for convenience, we temporarily ignore the noise and assume that the transmitted signals are all the square root of 2):

$$
\mathbf{Y}_0 = 
    \sqrt{2}
    \begin{bmatrix}
        1 \\
        e^{j\theta_1} \\ 
        e^{j2\theta_1} 
    \end{bmatrix}
    +
    \sqrt{2}
    \begin{bmatrix}
        1 \\
        e^{j\theta_2} \\ 
        e^{j2\theta_2} 
    \end{bmatrix}
\tag{39}
$$

The signal received by the second subarray is:

$$
    \mathbf{Y}_1 = 
    \sqrt{2}
    \begin{bmatrix}
        e^{j\theta_1} \\ 
        e^{j2\theta_1} \\ 
        e^{j3\theta_1} \\ 
    \end{bmatrix}
    +
    \begin{bmatrix}
        e^{j\theta_2} \\ 
        e^{j2\theta_2} \\ 
        e^{j3\theta_2} \\ 
    \end{bmatrix}
    =
    \sqrt{2}
    e^{j\theta_1}
    \begin{bmatrix}
        1 \\
        e^{j\theta_1} \\ 
        e^{j2\theta_1} 
    \end{bmatrix}
    +
    \sqrt{2}
    e^{j\theta_2}
    \begin{bmatrix}
        1 \\
        e^{j\theta_2} \\ 
        e^{j2\theta_2} 
    \end{bmatrix}
\tag{40}
$$

The signal received by the third subarray is:
$$
    \mathbf{Y}_2 = 
    \sqrt{2}
    \begin{bmatrix}
        e^{2j\theta_1} \\ 
        e^{j3\theta_1} \\ 
        e^{j4\theta_1} \\ 
    \end{bmatrix}
    +
    \sqrt{2}
    \begin{bmatrix}
        e^{2j\theta_2} \\ 
        e^{j3\theta_2} \\ 
        e^{j4\theta_2} \\ 
    \end{bmatrix}
    =
    \sqrt{2}
    e^{j2\theta_1}
    \begin{bmatrix}
        1 \\
        e^{j\theta_1} \\ 
        e^{j2\theta_1} 
    \end{bmatrix}
    +
    \sqrt{2}
    e^{j2\theta_2}
    \begin{bmatrix}
        1 \\
        e^{j\theta_2} \\ 
        e^{j2\theta_2} 
    \end{bmatrix}
\tag{41}
$$

then:

$$
\boldsymbol{A}^{(0)} = 
    \begin{bmatrix}
        1  & 1\\
        e^{j\theta_1} & e^{j\theta_2}\\ 
        e^{j2\theta_1} & e^{j2\theta_2} 
    \end{bmatrix}
\tag{42}
$$

and：
$$
    \boldsymbol{D}^0 = 
        \begin{bmatrix}
        1 & 0\\ 
        0 & 1
    \end{bmatrix}
    = \boldsymbol{I}
\tag{43}
$$
$$
    \boldsymbol{D}^1 = 
        \begin{bmatrix}
        e^{j\theta_1} & 0\\ 
        0 & e^{j\theta_2}
    \end{bmatrix}
\tag{44}
$$

$$
    \boldsymbol{D}^2 = 
        \begin{bmatrix}
        e^{j2\theta_1} & 0\\ 
        0 & e^{j2\theta_2}
    \end{bmatrix}
\tag{45}
$$

then
$$
\mathbf{Y}_0 = 
    \begin{bmatrix}
        1  & 1\\
        e^{j\theta_1} & e^{j\theta_2}\\ 
        e^{j2\theta_1} & e^{j2\theta_2} 
    \end{bmatrix} 
    \begin{bmatrix}
        1 & 0 \\
        0 & 1
    \end{bmatrix}
    \begin{bmatrix}
        \sqrt{2} \\
        \sqrt{2}
    \end{bmatrix}
    = A^{(0)} D^0 S = A^{(0)} I S
\tag{46}
$$
and：
$$
\mathbf{Y}_1 = 
    \begin{bmatrix}
        1  & 1\\
        e^{j\theta_1} & e^{j\theta_2}\\ 
        e^{j2\theta_1} & e^{j2\theta_2} 
    \end{bmatrix} 
    \begin{bmatrix}
        e^{j\theta_1} & 0\\ 
        0 & e^{j\theta_2}
    \end{bmatrix}
    \begin{bmatrix}
        \sqrt{2} \\
        \sqrt{2}
    \end{bmatrix}
    = A^{(0)} D^1 S
\tag{47}
$$
and
$$
    \mathbf{Y}_2 = 
    \begin{bmatrix}
        1  & 1\\
        e^{j\theta_1} & e^{j\theta_2}\\ 
        e^{j2\theta_1} & e^{j2\theta_2} 
    \end{bmatrix} 
    \begin{bmatrix}
        e^{j2\theta_1} & 0\\ 
        0 & e^{j2\theta_2}
    \end{bmatrix}
    \begin{bmatrix}
        \sqrt{2} \\
        \sqrt{2}
    \end{bmatrix}
    = A^{(0)} D^2 S
\tag{48}
$$

Therefore, it can be derived that:
$$
\begin{aligned}
    \boldsymbol{R}^{(0)}_y &= \boldsymbol{A}^{(0)} I S S^H I^H (\boldsymbol{A}^{(0)})^H
     =\boldsymbol{A}^{(0)} I \boldsymbol{R}_s I^H (\boldsymbol{A}^{(0)})^H  \\
    \boldsymbol{R}^{(1)}_y &= \boldsymbol{A}^{(0)} D S S^H D ^H (\boldsymbol{A}^{(0)})^H
     =\boldsymbol{A}^{(0)} D \boldsymbol{R}_s D^H (\boldsymbol{A}_{(0)})^H  \\
    \boldsymbol{R}^{(2)}_y &= \boldsymbol{A}^{(0)} D^2 S S^H (D^2)^H (\boldsymbol{A}^{(0)})^H
     =\boldsymbol{A}^{(0)} D^2 \boldsymbol{R}_s (D^2)^H  (\boldsymbol{A}^{(0)})^H
\end{aligned}
\tag{49}
$$

Taking the average of the above three subarray covariance matrices, we omit dividing by 3 for convenience of expression, as it does not affect the final proof.

$$
\begin{aligned}
    \boldsymbol{R}^{(0)}_y + \boldsymbol{R}^{(1)}_y + \boldsymbol{R}^{(2)}_y
    &= \boldsymbol{A}^{(0)} I \boldsymbol{R}_s I^H (\boldsymbol{A}^{(0)})^H +
      \boldsymbol{A}^{(0)} D^H \boldsymbol{R}_s D^H (\boldsymbol{A}_{(0)})^H +
      \boldsymbol{A}^{(0)} D^2 \boldsymbol{R}_s (D^2)^H  (\boldsymbol{A}^{(0)})^H  \\
    &= \boldsymbol{A}^{(0)} [\boldsymbol{R}_s +  D \boldsymbol{R}_s D^H + D^2 \boldsymbol{R}_s (D^2)^H] (\boldsymbol{A}^{(0)})^H
\end{aligned}
\tag{50}
$$

then：
$$
    \tilde{\boldsymbol{R}}_s = \boldsymbol{R}_s +  D \boldsymbol{R}_s D^H + D^2 \boldsymbol{R}_s (D^2)^H
\tag{51}
$$
We can decompose $R_s$ as:

$$
    \boldsymbol{R}_s = 
    \begin{bmatrix}
        2 & 2 \\
        2 & 2
    \end{bmatrix}
    =
    \begin{bmatrix}
        1 & 1 \\
        1 & 1
    \end{bmatrix}
    \begin{bmatrix}
        1 & 1 \\
        1 & 1
    \end{bmatrix}
    = \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
\tag{52}
$$

Then, equation (51) can be written as:
$$
\begin{aligned}
    \tilde{\boldsymbol{R}}_s &= \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
     + \boldsymbol{D} \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H \boldsymbol{D}^H
     + \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H  (\boldsymbol{D}^2)^H \\
     &= 
     \begin{bmatrix}
        \boldsymbol{I} & \boldsymbol{D} & \boldsymbol{D}^2
     \end{bmatrix}
    \begin{bmatrix}
         \boldsymbol{R}_s^{1/2} & 0 & 0\\
         0 & \boldsymbol{R}_s^{1/2} & 0 \\
         0 & 0 & \boldsymbol{R}_s^{1/2}
     \end{bmatrix}
    \begin{bmatrix}
         \boldsymbol{R}_s^{1/2} & 0 & 0\\
         0 & \boldsymbol{R}_s^{1/2} & 0 \\
         0 & 0 & \boldsymbol{R}_s^{1/2}
     \end{bmatrix}^H
     \begin{bmatrix}
        \boldsymbol{I}^H \\
        \boldsymbol{D}^H \\
        (\boldsymbol{D}^2)^H
     \end{bmatrix}
\end{aligned} 
\tag{53}
$$

Then, the matrix $G$ is:
$$
\begin{aligned}
    \boldsymbol{G} &= \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H
     + \boldsymbol{D} \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H \boldsymbol{D}^H
     + \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2} (\boldsymbol{R}_s^{1/2})^H  (\boldsymbol{D}^2)^H \\
     &= 
     \begin{bmatrix}
        \boldsymbol{I} & \boldsymbol{D} & \boldsymbol{D}^2
     \end{bmatrix}
    \begin{bmatrix}
         \boldsymbol{R}_s^{1/2} & 0 & 0\\
         0 & \boldsymbol{R}_s^{1/2} & 0 \\
         0 & 0 & \boldsymbol{R}_s^{1/2}
     \end{bmatrix} \\
     &=
     \begin{bmatrix}
        \boldsymbol{I}\boldsymbol{R}_s^{1/2} & \boldsymbol{D} \boldsymbol{R}_s^{1/2} & \boldsymbol{D}^2 \boldsymbol{R}_s^{1/2}
     \end{bmatrix}
\end{aligned} 
\tag{54}
$$

Expanding equation (54) gives:
$$
\begin{aligned}
\boldsymbol{G} & = 
    \begin{bmatrix}
         1 & 1 & e^{j\theta_1} & e^{j\theta_1} &  e^{j2\theta_1} & e^{j2\theta_1}\\\\
         1 & 1 & e^{j\theta_2} & e^{j\theta_2} & e^{j2\theta_2}  & e^{j2\theta_2}
    \end{bmatrix} \\
    &= 
    \begin{bmatrix}
         1  & e^{j\theta_1} &  e^{j2\theta_1} & 1 & e^{j\theta_1} & e^{j2\theta_1}\\
         1 & e^{j\theta_2} & e^{j2\theta_2}  & 1 & e^{j\theta_2}  & e^{j2\theta_2}
    \end{bmatrix} \\
    &=
    \begin{bmatrix}
         \boldsymbol{b}_0 & \boldsymbol{b}_0 \\
         \boldsymbol{b}_1 & \boldsymbol{b}_1\\
    \end{bmatrix}_{2\times 6} \\
\end{aligned} 
\tag{55}
$$

Based on the previous proof of the Vandermonde matrix, b0 and b1 are uncorrelated. Therefore, the rank of matrix G is 2, and it is full rank.

[1] R. Schmidt. “Multiple emitter location and signal parameter estimation”. In: Proceedings of RADC Spectrum Estimation Workshop. Saxpy Computer Corporation, USA, 1979, pp. 243–258.

[2] R. Schmidt. “Multiple emitter location and signal parameter estimation”. In: IEEE Trans-actions on Antennas and Propagation 34.3 (1986), pp. 276–280. doi: 10.1109/TAP.1986.1143830.

[3] Tie-Jun Shan, M. Wax, and T. Kailath. “On spatial smoothing for direction-of-arrival estima-tion of coherent signals”. In: IEEE Transactions on Acoustics, Speech, and Signal Processing 33.4 (1985), pp. 806–811. doi: 10.1109/TASSP.1985.1164649.

## Using SVD Decomposition to Find the Noise Subspace

In the MUSIC algorithm, both Singular Value Decomposition (SVD) and Eigenvalue Decomposition (EVD) can be used to obtain the noise subspace. This is because, for conjugate symmetric matrices, the results of SVD and EVD are identical. However, SVD has several advantages, particularly in terms of numerical stability in practical calculations.

SVD directly decomposes the original covariance matrix $R$, whereas EVD relies on computing eigenvalues and eigenvectors, which can be affected by numerical errors, especially when the signal-to-noise ratio (SNR) is low.

During the computation, SVD reduces the impact of small numerical errors by decomposing through orthogonal matrices, improving stability. It is particularly suitable for low-rank or degenerate matrices.

In certain cases (such as covariance matrices after spatial smoothing), $R$ might be a degenerate matrix (i.e., some eigenvalues are zero). SVD can still compute an effective noise subspace, while EVD might be affected by these degeneracies.
