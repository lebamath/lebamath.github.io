<script type="text/javascript" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML"> </script> <script async src="https://www.googletagmanager.com/gtag/js?id=G-V3E68S0VG4"></script> <script> window.dataLayer = window.dataLayer || []; function gtag(){dataLayer.push(arguments);} gtag('js', new Date()); gtag('config', 'G-V3E68S0VG4'); </script>

#  SINR/SNR of MIMO Equalizer

[Video: SINR of ZF Equalizer](https://www.youtube.com/watch?v=uFRUvERZHak&t=9s)

MIMO system model is：

$$
    Y = HX + N
$$

MIMO linear equalization is to find a matrix $$G$$，then use this matrix G to estimate the original signals $$X$$ as $$\hat X$$:

$$
    \hat X = GY
$$

This paper aims to analyze the signal-to-interference-plus-noise ratio (SINR) or signal-to-noise ratio (SNR) after equalization, focusing on two equalization algorithms: Zero-Forcing (ZF) and Minimum Mean Square Error (MMSE). Among them, the SINR under MMSE equalization is the most difficult to derive, and therefore the majority of the paper is devoted to its derivation.

## SNR for Zero Forcing Equalizer 

We know that the equalization matrix $$G$$ of the Zero-Forcing (ZF) algorithm  is：

$$
    G = (H^{\text H} H)^{-1} H^H
$$

Therefore, the estimated signal $$\hat X$$ after equalization is: 

$$
\begin{aligned}
    \hat X &= (H^{\text H} H)^{-1} H^H ( HX + N) \\
        &= (H^{\text H} H)^{-1} H^H  HX + (H^{\text H} H)^{-1} H^H N \\
        &= X +  (H^{\text H} H)^{-1} H^H N \\
        &= X + \hat N
\end{aligned}
$$

 where $$\hat N = (H^{\text H} H)^{-1} H^H N$$ is the noise vector after equalizing。

Since the Zero-Forcing algorithm eliminates inter-stream interference, the signal-to-interference-plus-noise ratio (SINR) is effectively equal to the signal-to-noise ratio (SNR).

then $$l\text{-th}$$  noise is：

$$
\begin{aligned}
    \text E\left [\hat N \hat N^{\text H}\right ]_{ll} &= \text E\left [(H^{\text H} H)^{-1} H^{\text H} N \left ((H^{\text H} H)^{-1} H^H N\right )^{\text H} \right ]_{ll}  \\
     &= \text E\left [(H^{\text H} H)^{-1} H^{\text H} N N^\text H H (H^{\text H}H)^{-1} \right ]_{ll} \\
     &= \left [(H^{\text H} H)^{-1} H^{\text H} E[N N^\text H] H (H^{\text H}H)^{-1} \right ]_{ll} \\
     &= \sigma^2 \left [(H^{\text H} H)^{-1} H^{\text H} H (H^{\text H}H)^{-1} \right ]_{ll} \\
     &= \sigma^2 \left [(H^{\text H} H)^{-1} \right ]_{ll}
\end{aligned}    
$$

then SNR is：

$$
    \text{SNR}_{\text{ZF}} = \frac{1}{\sigma^2 \left [(H^{\text H} H)^{-1} \right ]_{ll}}
$$

Next, we analyze this SNR. Since  $$H^{\text H} H$$ is a Hermitian matrix, its  eigenvalue decomposition is:

$$
H^{\text H} H = Q \Sigma Q^{\text H}
$$
where $$Q$$ is a unitary quadrature complex matrix，also called Unitary Matrix。

then the inverse matrix is：

$$
    (H^{\text H} H)^{-1} = Q \Sigma^{-1} Q^{\text H}
$$

Let $$q_l$$  be the $$l\text{-th} $$ column of $$Q^{\text H}$$ ，then the $$l\text{-th}$$ row of  $$Q$$ is： $$q_l^{\text H}$$。

then

$$
    \left [(H^{\text H} H)^{-1} \right ]_{ll} = q_l^{\text H} \Lambda^{-1} q_l = \sum_{i} |q_{li}|^2\lambda_i^{-1}
$$

where  $$\lambda_i$$ is the $$i\text{-th}$$ eigenvalue of $$ H^{\text H} H$$ .

so the SINR is：

$$
\text{SNR}_{\text{ZF}} = \frac{1}{\sigma^2  \sum_{i} |q_{li}|^2 \lambda_i^{-1}}
$$

If the matrix $$H^{\text{H}} H$$ has at least one very small eigenvalue, the SNR of each stream will degrade significantly. This is because even a single small eigenvalue can cause the term 

$$\sum_{i}|q_{li}|^2 \lambda_i^{-1}$$

to become large, thereby reducing the SNR across all signal streams.



## SINR of MMSE Equalizer

We know，MMSE Equalizer use the matrix G：

$$
G_{\text {MMSE}} = (H^\text H H + \sigma^2 I ) ^{-1} H^\text H
$$

then the estimated signal $$\hat X$$  is：

$$
\begin{aligned}
    \hat X &= G_{\text{MMSE}} Y = (H^\text H H + \sigma^2 I ) ^{-1} H^\text H (HX + N) \\
    &= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X + (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N 
\end{aligned}
\tag{1}
$$

Because the MMSE equalizer does not completely eliminate the interference between signals, the signal-to-interference ratio (SIR) under MMSE equalization includes both noise and interference, so the noise and interference need to be calculated separately.

### Noise Power 
First, calculate the covariance matrix of the noise vector:

$$
\begin{aligned}
R_N &=\text E\left [ \left ( (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N \right )
\left ( (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N \right )^{\text H} \right ] \\
&= \text E\left [  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H N 
 N^\text H H (H^\text H H + \sigma^2 I ) ^{-1} \right ] \\
 &= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H \text E\left [N 
 N^\text H\right ] H (H^\text H H + \sigma^2 I ) ^{-1}  \\
 &= \sigma^2  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H  H (H^\text H H + \sigma^2 I ) ^{-1}
\end{aligned}
\tag{2}
$$

Since $$H^\text{H} H$$ is a Hermitian matrix, its eigenvalue decomposition can be expressed as：

$$
    H^\text H H = Q^\text H \Lambda Q
$$

where $$Q$$ is a unitary (orthonormal) complex matrix，so $$Q^{-1}= Q^\text H$$.

Substituting into the noise covariance matrix (2) gives：

$$
    \begin{aligned}
        R_{\text N} &= \sigma^2 (Q^\text H \Lambda Q+ \sigma^2 I)^{-1} Q^\text H \Lambda Q (Q^\text H \Lambda Q+ \sigma^2 I)^{-1} \\
        &=  \sigma^2 \left ( Q^\text H (\Lambda+ \sigma^2 I) Q\right )^{-1}  Q^\text H \Lambda Q   \left ( Q^\text H (\Lambda+ \sigma^2 I) Q\right )^{-1}  \\
        &= \sigma^2  Q^\text H (\Lambda+ \sigma^2 I)^{-1} Q Q^\text H \Lambda Q  Q^\text H (\Lambda+ \sigma^2 I)^{-1} Q \\
        &= \sigma^2  Q^\text H (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} Q
    \end{aligned}
$$

Then, the noise power on the $$l\text{-th}$$ signal stream is the $$l\text{-th}$$ column and the $$l\text{-th}$$ row  element of the noise covariance matrix $$R_N$$.

let：

$$
Q = \begin{bmatrix} q_1 & q_2 & \cdots & q_l & \cdots & \end{bmatrix}
$$

wheter $$q_l$$  is the column vector of $$Q$$ .

Then, the noise power on the $$l$$-th signal stream is given by：

$$
P_{\text{noise}} =R_{\text N}(l,l) = \sigma^2 q_l^\text H  (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} q_l
\tag{3}
$$

### Signal Power 
In equation (1), the coefficient matrix of the signal $$X$$ is  $$(H^\text{H} H + \sigma^2 I)^{-1} H^\text{H} H$$. Substituting the eigenvalue decomposition yields：

$$
    \begin{aligned}
        (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H 
        &= (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}   Q^\text H \Lambda Q \\
        &= Q^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda Q
    \end{aligned}
$$

Then, the coefficient for the $$l$$-th signal stream (excluding interference from other streams) is the $$(l, l)$$-th element of the above coefficient matrix. Therefore, the signal coefficient is given by：

$$
    q_l^\text H (\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l
\tag{4}
$$

The power of the $$l$$-th signal stream is given by：

$$
    P_{\text{sig}} = q_l^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda q_l q_l^\text H \Lambda (\Lambda +   \sigma^2 I ) ^{-1} q_l
\tag{5}
$$

### Interference Power 
The interference power is not easy to derive directly. However, the total power of the signal plus interference is easier to compute. Therefore, we first derive the total power of signal plus interference, then subtract the signal power to obtain the interference power.

The $$l$$-th row of the coefficient matrix in equation (4) contains the coefficients of the desired signal $$X_l$$ as well as the interfering signals. We first derive the corresponding covariance matrix：

$$
    \begin{aligned}
        R_X &= \text E \left [ ((H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X)((H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X)^\text H   \right ]  \\
        &= \text E \left [ (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H X X^\text H  H^\text H H (H^\text H H + \sigma^2 I ) ^{-1}\right ]  \\
        &=  (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H \text E \left [ X X^\text H\right ]  H^\text H H (H^\text H H + \sigma^2 I )^{-1} \\
        &= (H^\text H H + \sigma^2 I ) ^{-1} H^\text H H H^\text H H (H^\text H H + \sigma^2 I )^{-1} 
    \end{aligned}
$$

Substituting the eigenvalue decomposition into the above expression gives:

$$
    \begin{aligned}
        R_X &= (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}Q^\text H \Lambda Q Q^\text H \Lambda Q  (Q^\text H \Lambda Q +   \sigma^2 I ) ^{-1}  \\
        &= Q^\text H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} Q
    \end{aligned}
$$

Therefore, the total power of the signal plus interference is the $$(l, l)$$-th element of the signal covariance matrix $$R_X$$.

$$
R_X(l,l) = q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} q_l
\tag{6}
$$

Then, subtracting equation (5) from equation (6) yields the interference power：

$$
    \begin{aligned}
        P_{\text{inter}} &= q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda^2 (\Lambda +   \sigma^2 I ) ^{-1} q_l - q_l^\text H (\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l q_l^\text H \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l  \\
        &= q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda (I - q_l q_l^\text H) \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l
    \end{aligned}
\tag{7}
$$

### Noise plus Interference Power 
By adding the noise power and the interference power together—that is, by adding equation (3) and equation (7)—we obtain the total power of noise plus interference.：

$$
    \begin{aligned}
        P_{\text{noise+inter}} &= P_{\text{noise}} + P_{\text{inter}} \\ 
        &=\sigma^2 q_l^\text H  (\Lambda+ \sigma^2 I)^{-1} \Lambda  (\Lambda+ \sigma^2 I)^{-1} q_l + q_l^H (\Lambda +   \sigma^2 I ) ^{-1} \Lambda (I - q_l q_l^\text H) \Lambda (\Lambda  +   \sigma^2 I ) ^{-1} q_l  \\
        &= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
        (\sigma^2 \Lambda + \Lambda^2 - \Lambda q_l q_l^\text H \Lambda)
        (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
        &= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
        (\sigma^2 \Lambda + \Lambda^2)(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
        q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
        &= q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}
        (\sigma^2 I + \Lambda)\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
        q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
        &=q_l^\text H\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l -
        q_l^\text H  (\Lambda+ \sigma^2 I)^{-1}   \Lambda q_l q_l^\text H \Lambda   (\Lambda  +   \sigma^2 I ) ^{-1} q_l \\
        &= c - c^* c
    \end{aligned}
$$

Here, $$c = q_l^\text{H} \Lambda (\Lambda + \sigma^2 I)^{-1} q_l$$ is a complex scalar, and $$c^*$$ denotes the complex conjugate of $$c$$.

### SINR 

The signal power in equation (5) can be written as：

$$
    P_{\text{sig}} = c^* c
$$

then MMSE SNIR is：

$$
    \begin{aligned}
    \gamma &= \frac{P_{\text{sig}}}{P_{\text{noise+inter}}} = \frac{c^* c}{c - c^* c} \\
    &= \frac{c^*}{1-c^*} \\
    &= \frac{1}{1 - c^*} - 1
    \end{aligned}
\tag{8}
$$

Next, we further derive $$c^*$$：

$$
    \begin{aligned}
        c^* &=  (q_l^\text H\Lambda(\Lambda  +   \sigma^2 I ) ^{-1} q_l)^* \\
        &= q_l^\text H(\Lambda  +   \sigma^2 I ) ^{-1} \Lambda q_l
    \end{aligned}
\tag{9}
$$

According to the matrix inverse expansion formula

$$
    \left( \mathbf{A} + \mathbf{U} \mathbf{C} \mathbf{V} \right)^{-1}
=
\mathbf{A}^{-1}
- \mathbf{A}^{-1} \mathbf{U}
\left( \mathbf{C}^{-1} + \mathbf{V} \mathbf{A}^{-1} \mathbf{U} \right)^{-1}
\mathbf{V} \mathbf{A}^{-1}
$$

In $$(\Lambda  +   \sigma^2 I ) ^{-1}$$ let $$A = \Lambda, U = \sigma^2 I, C = I,V=I$$，then：

$$
\begin{aligned}
    (\Lambda  +   \sigma^2 I ) ^{-1} &= \Lambda^{-1} - \Lambda^{-1}  \sigma^2 I ( I + I \Lambda^{-1} \sigma^2 I )^{-1} I \Lambda^{-1}  \\
    &= \Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1}
\end{aligned}
\tag{10}
$$

Substituting equation (10) into equation (9):

$$
    \begin{aligned}
        c^* &=  q_l^\text H \left (\Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1} \right ) \Lambda q_l \\
        &= q_l^\text H \left ( I - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \right ) q_l \\
        &=  1 - \sigma^2 q_l^\text H \Lambda^{-1}( I + \sigma^2 \Lambda^{-1})^{-1}  q_l  \\
        &= 1 - \sigma^2 q_l^\text H ( \Lambda + \sigma^2 I )^{-1}  q_l \\
        &= 1 - \sigma^2[Q^\text H ( \Lambda + \sigma^2 I )^{-1}  Q]_{ll}  \\
        &= 1 - \sigma^2[ (Q^\text H \Lambda Q + \sigma^2 Q^\text H I Q)^{-1}  ]_{ll} \\
        &= 1 - \sigma^2[ (Q^\text H \Lambda Q + \sigma^2 I )^{-1}  ]_{ll} \\
        &= 1 - \sigma^2[ (H^\text H H + \sigma^2 I )^{-1}  ]_{ll}
    \end{aligned}
\tag{11}
$$




Substituting equation (11) into equation (8) yields:



$$
    \begin{aligned}
        \gamma &= \frac{1}{1 - \left ( 1 - \sigma^2[ (H^\text H  H + \sigma^2 I )^{-1}  ]_{ll} \right )} - 1 \\[8pt]
        &=\frac{1}{\sigma^2[ (H^\text H  H + \sigma^2 I )^{-1}  ]_{ll}} - 1
    \end{aligned}
\tag{12}
$$

If the original signal power is 1, and the energy of the Gaussian white noise is $$\sigma^2$$, then the original SNR is given by：

$$
    \text{SNR} = 1/\sigma^2
$$

Then, the final signal-to-interference-plus-noise ratio (SINR) after MMSE equalization is:

$$
    \begin{aligned}
        \gamma 
        &=\frac{\text{SNR}}{[ (H^\text H  H + \frac{1}{\text{SNR}} I )^{-1}  ]_{ll}} - 1
    \end{aligned}
\tag{13}
$$


### An alternative expression SINR

$$
    \begin{aligned}
        \gamma = \frac{h_l^{\text H} R^{-1} h_l}{1-h_l^{\text H} R^{-1} h_l}
    \end{aligned}
\tag{14}
$$

where $$R=H H^{\text H} + \sigma^2 I$$.

Because 

$$
    h_l^{\text H} R^{-1} h_l = [H^{\text H} R^{-1} H]_{ll}
$$

Therefore, let's first derive the expression for $$H^{\text{H}} R^{-1} H$$.

First, perform the singular value decomposition (SVD) of $$H$$:

$$
    H = U \Lambda^{1/2} Q
$$

then：

$$
    \begin{aligned}
        H^{\text H} R^{-1} H &= Q^{\text H} \Lambda^{1/2} U^{\text H}\left (U \Lambda^{1/2} Q  (U \Lambda^{1/2} Q)^{\text H}  + \sigma^2 I \right )^{-1} U \Lambda^{1/2} Q \\
        &= Q^{\text H} \Lambda^{1/2} U^\text H\left (U \Lambda^{1/2} Q^{\text H}  Q \Lambda^{1/2} U^{\text H}  + \sigma^2 I \right )^{-1} U \Lambda^{1/2} Q \\
        &= Q^{\text H} \Lambda^{1/2} (  \Lambda + \sigma^2 I )^{-1} \Lambda^{1/2} Q
        \end{aligned}
$$

Similarly, by substituting equation (10) into the above expression, we have：

$$
    \begin{aligned}
        H^{\text H} R^{-1} H &= Q^{\text H} \Lambda^{1/2} (  \Lambda + \sigma^2 I )^{-1} \Lambda^{1/2} Q \\
        &= Q^{\text H} \Lambda^{1/2} \left (\Lambda^{-1} - \sigma^2 \Lambda^{-1}  ( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1} \right )  \Lambda^{1/2} Q \\
        &= Q^{\text H}  \left ( I - \sigma^2 \Lambda^{-1/2}( I + \sigma^2 \Lambda^{-1})^{-1} \Lambda^{-1/2} \right )  Q \\
        &= Q^{\text H}  \left ( I - \sigma^2 ( \Lambda + \sigma^2 I)^{-1} \right )  Q \\
        &= I -  \sigma^2 Q^{\text H} ( \Lambda + \sigma^2 I)^{-1} Q \\
        &= I -  \sigma^2 ( Q^{\text H} \Lambda Q  + \sigma^2 I)^{-1} \\
        &= I - \sigma^2 ( H^{\text H} H  + \sigma^2 I)^{-1}
        \end{aligned}
$$

So：

$$
    h_l^{\text H} R^{-1} h_l = [H^{\text H} R^{-1} H]_{ll} = 1 - \sigma^2 \left [  (  H^{\text H} H + \sigma^2 I)^{-1} \right ]_{ll}
$$

Substituting the above expression into (14) and performing some simplifications also leads to the same result as in equation (12), which is equation (13).

Therefore, the final signal-to-interference-plus-noise ratio (SINR) after MMSE equalization can be expressed in two equivalent forms, namely:

$$
        \gamma 
        =\frac{\text{SNR}}{[ (H^\text H  H + \frac{1}{\text{SNR}} I )^{-1}  ]_{ll}} - 1
$$

and

$$
\gamma = \frac{h_l^{\text H} R^{-1} h_l}{1-h_l^{\text H} R^{-1} h_l}
$$

## An alternative expression SINR-from physical perspective 

As mentioned earlier in the discussion of the MMSE equalizer algorithm

$$
    H^{\text H}\left ( H H^{\text H} + \sigma^2 I  \right )^{-1}  = \left ( H^{\text H} H + \sigma^2 I  \right )^{-1}H^{\text H}
$$

We use the equalizer matrix expression on the left to derive the SINR under MMSE equalization, this time from a physical interpretation perspective.

To simplify the derivation and notation, let $$ R =  H H^{\text H} + \sigma^2 I$$。

### signal power 

The signal after equalization is given by:

$$
    \hat X = H^{\text H} R^{-1} Y = H^{\text H} R^{-1}(HX+N)
     = H^{\text H} R^{-1} H X + H^{\text H} R^{-1} N
$$

Accordingly, the coefficient of $$x_l$$  in the equalized signal corresponding to stream $$l$$ is $$[H^{\text H} R^{-1} H]_{ll}$$， i.e., $$h^{\text H}_l R^{-1} h_l$$。Then, the power of the desired signal after equalization is:

$$
    P_{\text{sig}} = h^{\text H}_l R^{-1} h_l \left (h^{\text H}_l R^{-1} h_l \right )^{H} = h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
\tag{1}
$$

### The power of interference 

Similarly, we calculate the total power of the signal plus interference, and then subtract the power of the signal to obtain the interference power.
The total power of the signal plus interference corresponds to the elements on the diagonal of the following covariance matrix:

$$
\begin{aligned}
    &E\left [\left ( H^{\text H} R^{-1} H X \right )\left ( H^{\text H} R^{-1} H X \right )^{\text H} \right ] \\
    &= 
     E\left [ H^{\text H} R^{-1} H X X^H H^{\text H} R^{-1}   H \right ]  \\[8pt]
     & = H^{\text H} R^{-1} H E[X X^H] H^{\text H} R^{-1} H \\[8pt]
     & =  H^{\text H} R^{-1} H H^{\text H} R^{-1} H
\end{aligned}
$$

The element in the $$l$$-th row and $$l$$-th column is the energy of the signal plus interference:

$$
    P_{\text{sig+inter}} = h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l
$$

Then, the interference power is:

$$
\begin{aligned}
    P_{\text{inter}} &= P_{\text{sig+inter}} -  P_{\text{sig}} \\
    &= h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
    \end{aligned}
\tag{2}
$$

### noise power 

Noise power corresponds to the elements on the diagonal of the noise vector’s covariance matrix.
The covariance matrix of the noise vector is:

$$
\begin{aligned}
    &E\left [ H^{\text H} R^{-1} N \left ( H^{\text H} R^{-1} N \right )^{\text H}   \right ] \\
    &=E\left [ H^{\text H} R^{-1} N  N^{\text H} R^{-1} H \right ]   \\
    &= H^{\text H} R^{-1} E\left [N  N^{\text H} \right ] R^{-1} H  \\
    &= \sigma^2 H^{\text H} R^{-1} R^{-1} H
\end{aligned}
$$

Taking the element at the $$l$$-th row and $$l$$-th column of the above matrix gives the noise energy:

$$
    P_{\text{noise}} =  \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l
\tag{3}
$$

### The energy of interference plus noise 
Then, add the interference energy from formula (2) and the noise energy from formula (3) together:

$$
    \begin{aligned}
        P_{\text{inter+noise}} &= P_{\text{inter}} + P_{\text{noise}} \\[8pt]
        &= h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l  - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l + \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l  \\[8pt]
        & = h^{\text H}_l R^{-1} H H^{\text H} R^{-1} h_l +  \sigma^2 h^{\text H}_l R^{-1} R^{-1} h_l  - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
        &=h^{\text H}_l R^{-1} \left ( H H^{\text H} + \sigma^2 I \right ) R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
        &= h^{\text H}_l R^{-1} R R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l \\[8pt]
        &= h^{\text H}_l R^{-1} h_l - h^{\text H}_l R^{-1} h_l h^{\text H}_l R^{-1} h_l
    \end{aligned}
$$

let $$c = h^{\text H}_l R^{-1} h_l$$, Then the above expression becomes:

$$
    P_{\text{inter+noise}} = c - c^2
$$

### Signal-to-Interference-plus-Noise Ratio (SINR) 
The signal power in formula (1) is given by $$P_{\text{sig}} = c^2$$, Then, the signal-to-interference-plus-Noise ratio (SINR) is:

$$
    \begin{aligned}
    \text{SINR}_{\text{MMSE}} &= \frac{P_{\text{sig}}}{ P_{\text{inter+noise}}}  \\[8pt]
    &= \frac{c^2}{c - c^2}  \\[8pt]
    &= \frac{c}{1 - c} \\[8pt]
    &= \frac{h^{\text H}_l R^{-1} h_l}{1-h^{\text H}_l R^{-1} h_l}
    \end{aligned}
$$

Proof completed.
