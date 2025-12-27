# Zero-Forcing Precoding Algorithm 

The Zero-Forcing (ZF) algorithm can also be derived from the perspective of making the received signal as close as possible to the transmitted signal. That is, the goal is to minimize the Mean Square Error (MSE):
$$
    J(\mathbf F) = \mathbb{E}  || \mathbf H \mathbf F \mathbf x - \mathbf x ||^2  
$$
Find the optimal $\tilde{\mathbf F}$:
$$
     \tilde{\mathbf F} = \min_{\mathbf F}J(\mathbf F) 
$$

Since the transmitted signal $\mathbf x$ is a random variable, we need to calculate the mathematical expectation of the error. Assuming the signals are uncorrelated and have a normalized power of 1, i.e., $\mathbb{E}\{ \mathbf x \mathbf x^{\text H} \} = \mathbf I$, the objective function becomes:

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

Obviously, to minimize the above mean square error (i.e., to make it zero), the condition $\mathbf H \mathbf F = \mathbf I$ must be satisfied. Since the number of transmit antennas is usually larger than the number of users, this system of equations is under-determined. A solution satisfying the condition can be found, but it is not unique. To implement the Zero-Forcing algorithm with minimum transmit power, we seek the **minimum norm solution** of this system, i.e., employing the Moore-Penrose Right Pseudo-inverse:

$$
    \mathbf F = \mathbf H^{\text H} (\mathbf H \mathbf H^{\text H})^{-1}
$$

## Detailed Derivation of the Minimum Norm Solution

Although there are infinitely many solutions satisfying the zero-forcing condition $\mathbf H \mathbf F = \mathbf I$ (because $N_t > K$, and $\mathbf H$ is a row-full-rank "fat" matrix), in practical systems, we wish to minimize the total transmit power while eliminating interference.

The total transmit power is proportional to the square of the Frobenius norm of the precoding matrix $\mathbf F$. Therefore, this problem is essentially a **constrained optimization problem**:

$$
\begin{aligned}
    & \min_{\mathbf F} \quad ||\mathbf F||_F^2 = \text{tr}(\mathbf F \mathbf F^{\text H}) \\
    & \text{s.t.} \quad \mathbf H \mathbf F - \mathbf I = \mathbf 0
\end{aligned}
$$

## Constructing the Lagrangian Function
To solve this problem, we introduce the Lagrange multiplier matrix $\mathbf \Lambda \in \mathbb{C}^{K \times K}$. Considering the constraints in the complex domain, the Lagrangian function $\mathcal{L}(\mathbf F, \mathbf \Lambda)$ is constructed as follows:

$$
    \mathcal{L}(\mathbf F, \mathbf \Lambda) = \underbrace{\text{tr}(\mathbf F \mathbf F^{\text H})}_{\text{Objective Function}} - \underbrace{\text{tr}\left( \mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H} \right) - \text{tr}\left( \mathbf \Lambda^{\text H} (\mathbf H \mathbf F - \mathbf I) \right)}_{\text{Constraint Terms (Ensuring real result)}}
$$

## Solving for the Optimal Precoding Matrix
We take the partial derivative of the Lagrangian function $\mathcal{L}$ with respect to $\mathbf F^*$:

1. Differentiating the first term $\text{tr}(\mathbf F \mathbf F^{\text H})$ yields $\mathbf F$.

2. Expanding the constraint term:
   $$ \text{tr}\left( \mathbf \Lambda (\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I) \right) = \text{tr}(\mathbf \Lambda \mathbf F^{\text H} \mathbf H^{\text H}) - \text{tr}(\mathbf \Lambda) $$
   Using the cyclic property of the trace $\text{tr}(\mathbf A \mathbf B \mathbf C) = \text{tr}(\mathbf B \mathbf C \mathbf A)$, this transforms into $\text{tr}(\mathbf H^{\text H} \mathbf \Lambda \mathbf F^{\text H})$. The derivative with respect to $\mathbf F^*$ is the coefficient matrix $\mathbf H^{\text H} \mathbf \Lambda$.
   
3. The remaining terms do not contain $\mathbf F^{\text H}$ (i.e., do not contain $\mathbf F^*$), so their derivatives are 0.

Combining the above steps, we obtain the gradient equation:
$$
    \frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - \mathbf H^{\text H} \mathbf \Lambda = \mathbf 0
$$

From this, we get the structural form of the optimal solution:
$$
    \mathbf F = \mathbf H^{\text H} \mathbf \Lambda
\tag{1}
$$

Finally, we need to determine the multiplier $\mathbf \Lambda$. Substituting Eq. (\ref{eq:structure}) into the original constraint condition $\mathbf H \mathbf F = \mathbf I$:

$$
    \mathbf H (\mathbf H^{\text H} \mathbf \Lambda) = \mathbf I
$$
$$
    (\mathbf H \mathbf H^{\text H}) \mathbf \Lambda = \mathbf I
$$

Since $\mathbf H$ is row-full-rank, $(\mathbf H \mathbf H^{\text H})$ is invertible, therefore:
$$
    \mathbf \Lambda = (\mathbf H \mathbf H^{\text H})^{-1}
$$

Substituting $\mathbf \Lambda$ back into Eq. (1), we obtain the final formula for the Zero-Forcing precoding matrix:
$$
    \mathbf F = \mathbf H^{\text H} (\mathbf H \mathbf H^{\text H})^{-1}
$$

This is precisely the famous Moore-Penrose Right Pseudo-inverse. 


# In-depth Discussion: Why Must Constraint Terms Appear in Pairs? 

When constructing the Lagrangian function, why can't we simplify the constraint terms and use only a single term like $\text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H})$? In fact, the paired form $\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$ must be adopted. Writing only one of these terms leads to two serious mathematical problems: first, the illegitimacy of the optimization problem definition, and second, gradient loss during complex differentiation.

These are elaborated below in two points.

## 1. Legitimacy of Definition: The Optimization Objective Must Be Real
This is the most fundamental reason. The essence of an optimization problem is to find the minimum value of an objective function within a feasible region. This requires the range of the objective function to be Ordered.
    
1) We can compare the magnitude of two real numbers (e.g., $3 < 5$).

2) **The complex domain is not ordered.** We cannot define whether complex number $3+4i$ is "smaller" than $5+2i$.

Let us observe the consequences of using only a single constraint term:

1) The original objective function (transmit power) $\text{tr}(\mathbf F \mathbf F^{\text H})$ is essentially energy, which belongs to **Real Numbers**.

2) The single constraint term $\text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H})$ is usually a **Complex Number**.

If the Lagrangian function $\mathcal{L}$ contains only a single constraint term:
$$
    \mathcal{L} = \text{Real} - \text{Complex} = \text{Complex}
$$
This renders the mathematical statement "minimize $\mathcal{L}$" meaningless, as we cannot search for the "minimum value" of a complex function.

**Mathematical Significance of Paired Appearance:**
Using the complex identity: The sum of any complex number $Z$ and its conjugate $Z^*$ is a real number.
$$
    Z + Z^* = 2\text{Re}\{Z\} \quad (\in \mathbb{R})
$$
Therefore, the function of constructing $\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$ is precisely to create a **real-valued** constraint term, thereby making the optimization problem valid.

## 2. Completeness of Gradient: Avoiding the "Vanishing" Trap During Differentiation
Even if we ignore the issue of real number definition and force a mathematical derivation, single-term constraints pose huge risks in complex matrix differentiation (Wirtinger Calculus).

The core of Wirtinger derivatives is that the matrix $\mathbf F$ and its conjugate $\mathbf F^*$ are treated as two independent variables.
Let the constraint part be $C(\mathbf F, \mathbf F^*)$. We analyze two single-term cases:

### Case A: Using only the term containing $\mathbf F^*$
Assume the constraint term is:
$$
    C_1 = \text{tr}(\mathbf \Lambda (\mathbf H \mathbf F - \mathbf I)^{\text H}) = \text{tr}(\mathbf \Lambda (\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I^{\text H}))
$$
This term explicitly contains $\mathbf F^{\text H}$ (i.e., $\mathbf F^*$). Differentiating with respect to $\mathbf F^*$ yields $\mathbf H^{\text H} \mathbf \Lambda$.
$$
    \frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - \mathbf H^{\text H} \mathbf \Lambda = \mathbf 0
$$
\textit{Comment: Although the definition is not rigorous (complex numbers cannot be minimized), it accidentally works out, and the derived structure happens to be correct.}

### Case B: Using only the term containing $\mathbf F$
Assume the constraint term is:
$$
    C_2 = \text{tr}(\mathbf \Lambda^{\text H} (\mathbf H \mathbf F - \mathbf I))
$$
Note that this term **only contains $\mathbf F$ and does not contain $\mathbf F^*$**. According to Wirtinger derivative rules, the partial derivative of a term without $\mathbf F^*$ with respect to $\mathbf F^*$ is **0**.
The result of differentiation is:
$$
    \frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf F - 0 = \mathbf 0 \implies \mathbf F = \mathbf 0
$$
\textit{Comment: Completely wrong! It derives an all-zero matrix, which is obviously not the solution for Zero-Forcing precoding.}

## Summary
Adopting the paired form $\text{tr}(\mathbf \Lambda \mathbf G^{\text H}) + \text{tr}(\mathbf \Lambda^{\text H} \mathbf G)$ is necessary for two reasons:

1) **Realness:** Ensures that the entire Lagrangian function is a real number, giving legal mathematical meaning to "finding the minimum value."

2) **Completeness:** It contains information on both $\mathbf F$ and $\mathbf F^*$. Regardless of which variable is differentiated, the constraint term will not vanish into thin air, guaranteeing the integrity of the system of equations.

# Analysis of Limitations of Direct Unconstrained Derivation 

If we disregard the physical constraint of minimizing transmit power and attempt to directly differentiate the Mean Square Error objective function without constraints, we encounter a mathematical dilemma. The analysis of this derivation path is as follows:

Set the objective function as:
$$
    J(\mathbf F) = \text{tr}\{(\mathbf H \mathbf F - \mathbf I)(\mathbf H \mathbf F - \mathbf I)^{\text H}\}
$$

Expanding the above equation using the linear properties of the matrix trace and conjugate transpose rules:
$$
\begin{aligned}
    J(\mathbf F) &= \text{tr}\left( (\mathbf H \mathbf F - \mathbf I)(\mathbf F^{\text H} \mathbf H^{\text H} - \mathbf I) \right) \\
    &= \text{tr}(\mathbf H \mathbf F \mathbf F^{\text H} \mathbf H^{\text H}) - \text{tr}(\mathbf H \mathbf F) - \text{tr}(\mathbf F^{\text H} \mathbf H^{\text H}) + \text{tr}(\mathbf I)
\end{aligned}
$$

To find the extremum point, we take the partial derivative with respect to the conjugate matrix $\mathbf F^*$ and set it to zero:
$$
    \frac{\partial J}{\partial \mathbf F^*} = \mathbf H^{\text H} \mathbf H \mathbf F - \mathbf H^{\text H} = \mathbf 0
$$

Rearranging the above equation, we obtain the so-called Normal Equation:
$$
    \mathbf H^{\text H} \mathbf H \mathbf F = \mathbf H^{\text H}
\tag{2}
$$

When attempting to solve for $\mathbf F$ from Eq. (2), we would typically try to left-multiply by $(\mathbf H^{\text H} \mathbf H)^{-1}$. However, in the downlink precoding scenario, the number of transmit antennas $N_t$ is usually greater than the number of users $K$. The dimension of the channel matrix $\mathbf H$ is $K \times N_t$, which means the product matrix $\mathbf H^{\text H} \mathbf H$ is a large square matrix of size $N_t \times N_t$. Since the rank of $\mathbf H$ is limited by the number of users $K$ (i.e., $\text{rank}(\mathbf H) \le K$), and $K < N_t$, $\mathbf H^{\text H} \mathbf H$ is inevitably a **Rank Deficient** matrix. In other words, it is a singular matrix, and its inverse $(\mathbf H^{\text H} \mathbf H)^{-1}$ does not exist at all.

Therefore, we cannot obtain a unique precoding matrix $\mathbf F$ through direct inversion. This mathematically confirms that the problem is an Under-determined Problem, and a minimum norm solution must be found by introducing power constraints.


# Mathematical Preliminaries: Complex Matrix Differentiation Rules 
To find the extremum, we need to take the partial derivative with respect to the complex matrix $\mathbf F$. Here, utilizing **Wirtinger Calculus** (complex calculus), we treat the matrix $\mathbf F$ and its conjugate $\mathbf F^*$ as mutually independent variables. To find the extremum, we simply set $\frac{\partial \mathcal{L}}{\partial \mathbf F^*} = \mathbf 0$.

In the derivation, we need to use the following two key differentiation formulas for the matrix Trace:


 **Rule 1 (differentiation of energy term):** 
$$
        \frac{\partial \text{tr}(\mathbf X \mathbf X^{\text H})}{\partial \mathbf X^*} = \mathbf X
$$
Explanation: This is similar to scalar differentiation where $\frac{d}{dx^*} (x x^*) = x$.
    
**Rule 2 (differentiation of linear term):**
$$
        \frac{\partial \text{tr}(\mathbf A \mathbf X^{\text H})}{\partial \mathbf X^*} = \mathbf A
$$
and
$$
        \frac{\partial \text{tr}(\mathbf A \mathbf X)}{\partial \mathbf X^*} = \mathbf 0
$$
Explanation: Terms containing $\mathbf X^{\text H}$ can be viewed as containing the variable $\mathbf X^*$, so the derivative is the coefficient matrix $\mathbf A$; whereas terms not containing $\mathbf X^{\text H}$ (containing only $\mathbf X$) are constants with respect to $\mathbf X^*$, so the derivative is 0.
