---
layout: default
title: "Antenna Polarization"
lang: en
back_url: /index.html?lang=en
---

# Antenna Polarization


## Polarization

As stated above, the radiated electromagnetic wave is a transverse wave, meaning that the electric field and magnetic field vectors are orthogonal to the direction of propagation. Therefore, in three-dimensional space there are two local dimensions available for the oscillation of the field, that is to say, such a wave is considered to have two degrees of freedom. We define the polarization of the wave as the direction in which the electric field oscillates within these two dimensions, when observed along the direction of propagation. When discussing polarization in this book, we will use spherical coordinates rather than Cartesian coordinates, since the former are better suited to describing the field and the polarization of a wave transmitted through an antenna.

Mathematically, the electric field of a spherical wave propagating along the $$\hat{\boldsymbol{r}}$$ direction can be expressed as:

$$
\boldsymbol{E}_0 = E_{\theta} exp(j\phi_{\theta}) \hat{\boldsymbol{\theta}} +  E_{\varphi} exp(j\phi_{\varphi}) \hat{\boldsymbol{\varphi}} \tag{3.11}
$$

Here, if the transverse-wave electric field is decomposed along the fundamental unit vectors $$\hat{\boldsymbol{\theta}}$$ and $$\hat{\boldsymbol{\varphi}}$$, then $$E_{\theta}$$ and $$E_{\varphi}$$ are the amplitudes corresponding to these two basis vectors respectively, and $$\phi_{\theta}$$ and $$\phi_{\varphi}$$ are the corresponding phases. Note that these vectors can be expressed as coordinates in 3D space, i.e., as a $$3\times 1$$ vector (for how to convert between different coordinate systems, see Appendix 1.5).

The relationship between the phasors determines the polarization of the wave, i.e., the time-varying direction and the relative amplitude of the electric field vector. In this book, the polarization of a wave is observed along the direction of wave propagation. Some specific polarization types are as follows (see also Figure 3.5):


![图1：Figure 3.5 Observed along the direction of wave propagation, the trajectory of the electric field vector exhibits different polarization types: linear polarization (left), circular polarization (middle) and elliptical polarization (right).](/figure/通信基础/天线极化/fig_3.5.png)

*图1：Figure 3.5 Observed along the direction of wave propagation, the trajectory of the electric field vector exhibits different polarization types: linear polarization (left), circular polarization (middle) and elliptical polarization (right).* 


**Linear polarization**:  occurs when the electric field vector oscillates along a constant direction, i.e., when $$\phi_{\theta} = \phi_{\varphi}$$, or when either one of $$E_{\theta}$$ or $$E_{\varphi}$$ is zero. The amplitude relationship between $$E_{\theta}$$ and $$E_{\varphi}$$ determines the tilt angle of the electric field vector. In this book, vertical polarization (vertical polarization, VP) [Note 2] is defined as the polarization when the electric field oscillates along the $$\hat{\boldsymbol{\theta}}$$ axis, i.e., when $$E_{\varphi} = 0$$. Similarly, horizontal polarization (horizontal polarization, HP) is defined as the polarization when the electric field oscillates along the $$\hat{\boldsymbol{\varphi}}$$ axis ($$E_{\theta} = 0$$). Other linear polarizations include +45° polarization ($$E_{\theta}=E_{\varphi}$$) and -45° polarization ($$E_{\theta}=-E_{\varphi}$$).

Note 2: In the literature, horizontal and vertical polarization are sometimes defined relative to the horizon in a Cartesian coordinate system. However, this definition leads to problems when describing antenna characteristics, because the antenna directivity and the polarization become inseparable; see Equation (3.30).

**Circular polarization**:  occurs when the electric field vector rotates in the transverse plane with a constant amplitude, i.e., when $$E_{\theta}=E_{\varphi}$$ and $$\phi_{\theta} = \phi_{\varphi} \pm 90°$$. The direction of rotation can be clockwise or counterclockwise, leading respectively to right-hand circular polarization (right-hand circular polarization, RHCP) or left-hand circular polarization (lefthand circular polarization, LHCP).

Other relationships between the phasors lead to \textit{elliptical polarization}, i.e., both the amplitude and the rotation angle of the electric field vector change.

The polarization of a wave can generally be represented by a $$2\times 1$$ vector $$\hat{\boldsymbol{\psi}}$$ with complex-valued elements, which is used to re-express the electric field vector, i.e.:

$$
\begin{aligned}
	\boldsymbol{E}_0 &= E_{\theta} exp(j \phi_{\theta }) \hat{\boldsymbol{\theta}} +
	E_{\varphi} exp(j \phi_{\varphi }) \hat{\boldsymbol{\varphi}}  \\
	&= \sqrt{E_{\theta}^2+E_{\varphi}^2}
	\begin{bmatrix}
		\hat{\boldsymbol{\theta}} & \hat{\boldsymbol{\varphi}}  
	\end{bmatrix} \hat{\underline{\boldsymbol{\psi}}}
	\overset{\bigtriangleup}{=} \sqrt{E_{\theta}^2+E_{\varphi}^2} \hat{\boldsymbol{\psi}}
\end{aligned}   \tag{3.12}
$$

where,

$$
\hat{\boldsymbol{\psi}}   \overset{\bigtriangleup}{=} = 
\frac{1}{\sqrt{E_{\theta}^2+E_{\varphi}^2}} 
\begin{bmatrix}
	E_{\theta} exp(j \phi_{\theta })\\  
	E_{\varphi} exp(j \phi_{\varphi })
\end{bmatrix}
\tag{3.13}
$$

contains the coordinates of the polarization vector on the basis vectors $$\hat{\boldsymbol{\theta}}$$ and $$\hat{\boldsymbol{\varphi}}$$.
Here,
$$
\begin{bmatrix}
\hat{\boldsymbol{\theta}} & \hat{\boldsymbol{\varphi}}  
\end{bmatrix}
$$
is a $$3\times 2$$ real-valued matrix, which contains the basis vectors $$\hat{\boldsymbol{\theta}}$$ and $$\hat{\boldsymbol{\varphi}}$$ as its elements.

In general, this chapter adopts the notation $$\underline{x}$$ to denote the vector of $$x$$ in a different basis coordinate system. The basis coordinate system is given by the context, and is usually based on the quantities $$\hat{\boldsymbol{\theta}}$$ and $$\hat{\boldsymbol{\varphi}}$$ in the transmitter or receiver coordinate system. In what follows, the same vector expressed in these two different ways will be used interchangeably. The vector $$\underline{x}$$ corresponding to $$x$$ may be called its corresponding Jones vector [Note 3].

Note 3: The representation of the polarization states and the polarization scattering matrix $$\psi$$ introduced later in this chapter was introduced by R.C. Jones, and the related mathematical operations used to determine polarization are therefore called Jones calculus.

Using this notation, the following polarization vectors can be defined:

$$
\hat{\underline{\boldsymbol{\psi}}}_{VP} = \begin{bmatrix}
	1 \\  0
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{HP} = \begin{bmatrix}
	0 \\  1
\end{bmatrix} \tag{3.15}
$$

$$
\hat{\underline{\boldsymbol{\psi}}}_{LHCP} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  j
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{RHCP} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  -j
\end{bmatrix} \tag{3.16}
$$

$$
\hat{\underline{\boldsymbol{\psi}}}_{+45°} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  1
\end{bmatrix}, 
\hat{\underline{\boldsymbol{\psi}}}_{-45°} = \frac{1}{\sqrt{2}} \begin{bmatrix}
	1 \\  -1
\end{bmatrix} \tag{3.17}
$$

For any polarization $$\hat{\boldsymbol{\psi}}_1$$, there always exists an orthogonal polarization $$\hat{\boldsymbol{\psi}}_2$$ such that $$\hat{\boldsymbol{\psi}}_1 \cdot \hat{\boldsymbol{\psi}}_2 = \hat{\boldsymbol{\psi}}_1^* \hat{\boldsymbol{\psi}}_2^* = \hat{\underline{\boldsymbol{\psi}}}_1^* \hat{\underline{\boldsymbol{\psi}}}_2^* = 0$$  for example $$\hat{\boldsymbol{\psi}}_{VP}$$ is orthogonal to $$\hat{\boldsymbol{\psi}}_{HP}$$,  $$\hat{\boldsymbol{\psi}}_{+45°}$$ is orthogonal to $$\hat{\boldsymbol{\psi}}_{-45°}$$, $$\hat{\boldsymbol{\psi}}_{LHCP}$$ is orthogonal to $$\hat{\boldsymbol{\psi}}_{RHCP}$$. Any elliptical polarization has an orthogonal elliptical polarization, but with the opposite direction of rotation, and the relative tilt angle of these two elliptical polarizations is a right angle. Any polarization can be described as a linear combination of two orthogonal polarizations, for example, as a linear combination of vertical and horizontal polarizations, or as a linear combination of +45° and -45° polarizations.

$$
\hat{\underline{\boldsymbol{\psi}}} = a  \hat{\underline{\boldsymbol{\psi}}}_{VP} + 
b  \hat{\underline{\boldsymbol{\psi}}}_{HP}
= c  \hat{\underline{\boldsymbol{\psi}}}_{+45°} +
d  \hat{\underline{\boldsymbol{\psi}}}_{-45°}
\tag{3.18}
$$

As will be described in Section 6.3 (see Figure 6.21), the two polarization degrees of freedom of a wave can be used for communication purposes, in order to achieve more robust signal transmission, or even by transmitting different information on different polarizations.


## Derivation of Elliptical Polarization
The electric field can be expressed as

$$
a e^{j2\pi f_c+\theta} \hat{\boldsymbol{x}} + b e^{j2\pi f_c+\varphi} \hat{\boldsymbol{y}}
$$

Then the components corresponding to the two coordinate axes are, respectively:

$$
x = a \text{cos}(2\pi f_c t + \theta)  \\
y = b \text{cos}(2\pi f_c t + \varphi)
$$

Then:

$$
x = a \text{cos}(2\pi f_c t) \text{cos}(\theta) - a \text{sin}(2\pi f_c t) \text{sin}(\theta) \\
y = b \text{cos}(2\pi f_c t) \text{cos}(\varphi) - b \text{sin}(2\pi f_c t) \text{sin}(\varphi) \\
\tag{1}
$$

Let:

$$
a_1 = a \text{cos}(\theta),\quad  a_2 = - a\text{sin}(\theta) \\
b_1 = b \text{cos}(\varphi),\quad  b_2 = - b\text{sin}(\varphi) \\
$$

Then equation (1) can be written more compactly as:

$$
x = a_1 \text{cos}(2\pi f_c t) + a_2 \text{sin}(2\pi f_c t) \\
y = b_1 \text{cos}(2\pi f_c t) + b_2 \text{sin}(2\pi f_c t) 
\tag{2}
$$

Multiply both sides of the first equation in (2) by $$b_2$$, and the second equation by $$a_2$$:

$$
b_2 x = a_1 b_2 \text{cos}(2\pi f_c t) + a_2 b_2 \text{sin}(2\pi f_c t) \\
a_2 y = a_2 b_1 \text{cos}(2\pi f_c t) + a_2 b_2 \text{sin}(2\pi f_c t)
$$

Then we can solve for:

$$
\text{cos}(2\pi f_c t) = \frac
{b_2 x - a_2 y}
{a_1 b_2 - a_2 b_1}  \tag{3}
$$

In the same way, multiply both sides of the first equation in (2) by $$b_1$$, and the second equation by $$a_1$$:

$$
b_1 x = a_1 b_1 \text{cos}(2\pi f_c t) + a_2 b_1\text{sin}(2\pi f_c t) \\
a_1 y = a_1 b_1 \text{cos}(2\pi f_c t) + a_1 b_2 \text{sin}(2\pi f_c t)
$$

Then we can solve for:

$$
\text{sin}(2\pi f_c t) = \frac
{- b_1 x + a_1 y}
{a_1 b_2 - a_2 b_1}  \tag{4}
$$

Combining (3) and (4) gives:

$$
\left (\frac{b_2 x - a_2 y }{a_1 b_2 - a_2 b_1} \right )^2 +
\left (\frac{- b_1 x + a_1 y}{a_1 b_2 - a_2 b_1}\right )^2
=1  \tag{5}
$$

If we let:

$$
t = b_2 x - a_2 y \\
h = - b_1 x + a_1 y
$$

Then (5) becomes:

$$
t^2 + h^2 = (a_1 b_2 - a_2 b_1)^2  \tag{6}
$$

In the coordinate system with t, h as coordinates this is a circle; however, the transformation from x, y to t, h is not a rigid transformation, although it is a linear transformation:

$$
\begin{bmatrix}  t \\ h   \end{bmatrix} =
\begin{bmatrix}  
	b_2   & - a_2\\
	-b_1  & a_1 
\end{bmatrix}
\begin{bmatrix}  x \\ y   \end{bmatrix}
$$

Since the matrix 

$$
A=\begin{bmatrix}  
	b_2   & - a_2\\
	-b_1  & a_1 
\end{bmatrix} =
\begin{bmatrix}  
	- b\text{sin}(\varphi)   & a\text{sin}(\theta)\\
	-b \text{cos}(\varphi)  &  a \text{cos}(\theta) 
\end{bmatrix}
$$

is not an orthogonal matrix, this is therefore a transformation with different stretchings together with a rotation, and this matrix can be considered by means of SVD decomposition.

When  $$a=b \quad \text{and} \quad  \varphi = \theta \pm \frac{\pi}{2}$$  this matrix is an orthogonal matrix (not normalized), so it is a rigid transformation and does not change the shape.

If neither a nor b is zero, and $$\varphi \neq \theta$$, and moreover "$$a=b \quad \text{and} \quad  \varphi = \theta \pm \frac{\pi}{2}$$" is not satisfied, then the above matrix is not an orthogonal matrix:

$$
A A^T = \begin{bmatrix}  
	- b\text{sin}(\varphi)   & a\text{sin}(\theta)\\
	-b \text{cos}(\varphi)  &  a \text{cos}(\theta) 
\end{bmatrix}  \begin{bmatrix}  
	- b\text{sin}(\varphi)   & -b \text{cos}(\varphi)\\
	a\text{sin}(\theta)    &  a \text{cos}(\theta) 
\end{bmatrix}
=
\begin{bmatrix}
	b^2 \text{sin}^2(\varphi)+a^2 \text{sin}^2(\theta) & & &
	b^2 \text{sin}(\varphi) \text{cos}(\varphi) + a^2 \text{sin}(\theta)\text{cos}(\theta) \\
	b^2 \text{sin}(\varphi) \text{cos}(\varphi) +a^2 \text{sin}(\theta)\text{cos}(\theta) & & &
	b^2 \text{cos}^2(\varphi) + a^2 \text{cos}^2(\theta)
\end{bmatrix}
$$

If A is an orthogonal matrix, then the rightmost matrix in the above expression must satisfy:

$$
b^2 \text{sin}^2(\varphi)+a^2 \text{sin}^2(\theta) = b^2 \text{cos}^2(\varphi) + a^2 \text{cos}^2(\theta)  \\
b^2 \text{sin}(\varphi) \text{cos}(\varphi) + a^2 \text{sin}(\theta)\text{cos}(\theta) = 0
$$

Then:

$$
-\frac{b^2}{a^2} = \frac{\text{cos}^2(\theta) - \text{sin}^2(\theta)}{\text{cos}^2(\varphi) - \text{sin}^2(\varphi)}
=\frac{\text{cos}(2\theta)}{\text{cos}(2\varphi)}   \tag{7}
$$

as well as:

$$
-\frac{b^2}{a^2} = \frac{\text{sin}(\theta)\text{cos}(\theta)}{\text{sin}(\varphi)\text{cos}(\varphi)}
=\frac{\text{sin}(2\theta)}{\text{sin}(2\varphi)}  \tag{8}
$$

Then combining (7) and (8) gives:

$$
\frac{\text{cos}(2\theta)}{\text{cos}(2\varphi)} = \frac{\text{sin}(2\theta)}{\text{sin}(2\varphi)}
$$

Deriving further gives:

$$
\frac{\text{sin}(2\varphi)}{\text{cos}(2\varphi)} = \frac{\text{sin}(2\theta)}{\text{cos}(2\theta)}
$$

That is:

$$
tan(2\varphi) = tan(2\theta)
$$

Then:

$$
2\theta = 2\varphi \pm k\pi
$$

That is:

$$
\theta = \varphi \pm  \frac{\pi}{2}k, \quad k=0,1,2,\cdots   \tag{9}
$$

Substituting (9) into (8) gives:

$$
b = a
$$

which contradicts the preceding condition, and therefore it is not an orthogonal matrix.