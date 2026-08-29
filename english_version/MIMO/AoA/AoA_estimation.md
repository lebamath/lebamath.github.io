---
layout: default
title: "AoA: Angle of Arrival"
lang: en
back_url: /english_version.html
---
# AoA: Angle of Arrival

## Analyzing Phase Difference with the Traditional Method

For a MIMO antenna array, assuming it is a linear array, and assuming that the data to be received or transmitted by the antenna array is located far away from the antenna array, we can therefore assume that the RF waves enter each antenna of the antenna array in parallel, or are transmitted from each antenna of the antenna array to the receiver in parallel.

Assume that the angle between the antenna array (linear array) and the RF wave is $$\theta$$, and the antenna spacing is $$d$$. Then, as shown in Figure 1, the phase difference between antennas is

$$
\frac{2\pi}{\lambda} d \operatorname{cos}(\theta)
\tag{1}
$$

![图1：Projection onto the RF wave direction in terms of distance](/figure/MIMO/AoA/AoA_distance_projection.png)

*图1：Projection onto the RF wave direction in terms of distance*

where $$d \operatorname{cos}(\theta)$$ is the length of the projection of the antenna spacing onto the RF wave direction.

The above is a very intuitive diagram and explanation. This analysis is the perpendicular projection from the antenna spacing onto the RF wave direction, which is a projection of distance.

### Spatial Angular Velocity Projection

Let us look at Equation (1) from another perspective, rearranging it into the following form:

$$
\frac{2\pi}{\lambda} \operatorname{cos}(\theta)d
$$

Here, $$2\pi/\lambda$$ can be regarded as the spatial angular velocity, i.e., the rate at which the phase angle changes with distance.

After multiplying by $$\operatorname{cos}(\theta)$$, this can be regarded as the projection of the spatial angular velocity onto the direction of the linear array.

It should be particularly noted here that this is not a projection of distance onto the direction of the linear array, but rather of the spatial angular velocity. The spatial angular velocity is a vector, with a direction, so it can be projected onto the direction of the linear array. Along the direction of the linear array, the waveform can be viewed as having been stretched, with the wavelength becoming:
$$\lambda/\operatorname{cos}(\theta)$$.

Equation (1) can be written as:
$$
\frac{2\pi}{\lambda/\operatorname{cos}(\theta)} d
$$

A diagram of this projection is shown in Figure 2.

![图2：Projection of the spatial angular velocity onto the antenna array direction](/figure/MIMO/AoA/AoA_angle_space_velocity_projection.png)

*图2：Projection of the spatial angular velocity onto the antenna array direction*

In summary, the spatial angular velocity can be projected onto the direction of the linear array. Using this approach makes it easier to perform spatial projection analysis, without needing to draw the spatially parallel transmitted waves and then analyze their phase difference. Of course, in essence, parallel transmitted waves are still being used to analyze the phase difference.


## AoA Analysis under a Planar Array

When calculating the AoA angle in the case of a planar array, the azimuth angle and the zenith angle are generally used, as shown in Figure 3.

![图3：Spatial angular velocity projection / azimuth angle / zenith angle on a planar array](/figure/MIMO/AoA/AoA_3D_projection.png)

*图3：Spatial angular velocity projection / azimuth angle / zenith angle on a planar array*

**Zenith Angle:** For the zenith angle $$\theta$$, this is looking at the vertical direction of the planar array. Both of the methods above can be used to derive the corresponding formula, though it is easier to derive using the spatial angular velocity. The spatial angular velocity is: $$2\pi/\lambda$$.

By analyzing the received signal, the phase difference between two adjacent antennas can be obtained as $$\tilde{\theta}$$, so that:
$$
\tilde{\theta} = \frac{2\pi}{\lambda}  \operatorname{cos}(\theta) d_{\text v}
$$

The expression for $$\theta$$ can be derived as:
$$
\theta = \operatorname{cos}^{-1}\left (\frac{\tilde{\theta}}{2\pi} \cdot \frac{\lambda}{d_\text{v}} \right )
$$

where $$d_{\text v}$$ is the vertical antenna spacing.

**Azimuth Angle:** For the azimuth angle, once the zenith angle is known, and the phase difference $$\tilde{\phi}$$ between two antennas in the horizontal direction has been estimated from the received signal.

Based on the projection of the spatial angular frequency, one can project directly onto the horizontal antenna direction, which can be computed via two successive projections: first projecting onto the xy plane, then projecting onto the x axis within the xy plane.

Projection onto the xy plane:

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)
$$

Then projecting onto the x axis:

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)\operatorname{sin}(\phi)
$$

The equation is then:

$$
\frac{2\pi}{\lambda} \operatorname{sin}(\theta)\operatorname{sin}(\phi) d_{\text h} = \tilde{\phi}
$$

where $$d_{\text h}$$ is the horizontal antenna spacing.

Continuing the derivation, the expression for $$\phi$$ can be obtained as:

$$
\phi = \operatorname{sin}^{-1}\left ( \frac{\tilde{\phi}}{2\pi} \cdot \frac{\lambda}{d_{\text h} \operatorname{sin}(\theta)} \right )
\tag{2}
$$

**Without Using the Azimuth Angle:** If the second angle is instead taken as the angle $$\gamma$$ of the RF wave relative to the x axis, then

$$
\frac{2\pi}{\lambda} \operatorname{cos}(\gamma) d_{\text h} = \tilde{\phi}
$$

from which the expression for $$\gamma$$ can be solved as:

$$
\gamma = \operatorname{cos}^{-1}\left (\frac{\tilde{\phi}}{2\pi} \cdot \frac{\lambda}{d_\text{h}} \right )
\tag{3}
$$

Comparing Equations (2) and (3), since they solve for different angles, the denominators in the formulas contain $$d_{\text h} \operatorname{sin}(\theta)$$ and $$d_{\text h}$$, respectively.