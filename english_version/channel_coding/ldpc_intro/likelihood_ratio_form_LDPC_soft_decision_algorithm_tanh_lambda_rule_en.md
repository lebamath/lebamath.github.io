---
layout: default
title: "The Likelihood-Ratio Form of the Soft-Decision Algorithm (Part Three): The tanh-lambda Rule"
lang: en
back_url: /index.html?lang=en
---

## The Likelihood-Ratio Form of the Soft-Decision Algorithm (Part Three): The tanh-lambda Rule
We study the tanh rule over the binary field, where x is a binary random variable and $$\oplus$$ denotes binary addition, satisfying:

$$
\begin{aligned}
	0\oplus 0=0 \\
	0\oplus 1=1 \\
	1\oplus 0=1 \\
	1\oplus 1=0 \\
\end{aligned}
$$

Define the $$\lambda$$ function as the logarithm of a probability ratio, that is:

$$
\lambda(x) = log \frac{p(x=1)}{p(x=0)}
$$

where $$p(x=1)$$ denotes the probability that x takes the value 1.

Written another way:

$$
e^{\lambda(x)} = \frac{p(x=1)}{p(x=0)}
$$

We introduce two mutually independent random variables $$x_1, x_2$$ :

$$
\lambda(x_1 \oplus x_2) = log \frac { p(x_1 \oplus x_2=1) } { p(x_1 \oplus x_2=0 )  }  \quad ---- \quad  Formula (1)
$$

In the numerator, $$x_1 \oplus  x_2 =1$$ has two cases, $$x_1=1, x_2=0$$ and $$x_1 =0 , x_2 = 1$$, so:

$$
p(x_1 \oplus x_2=1)  = p(x_1=1, x_2=0)  + p( x_1 =0 , x_2 = 1 )
$$

Because we have already assumed that the two random variables are mutually independent, then:

$$
\begin{aligned}
	p(x_1=1, x_2=0)  = p(x_1=1)  p(x_2=0)  \\
	p(x_1=0, x_2=1)  = p(x_1=0)  p(x_2=1)  \\
\end{aligned}
$$

Then, finally:

$$
p(x_1 \oplus x_2=1)  =  p(x_1=1)  p(x_2=0) + p(x_1=0)  p(x_2=1)
$$

Similarly:

$$x_1 \oplus  x_2 =0$$ has two cases, $$x_1=0, x_2=0$$ and $$x_1 =1 , x_2 = 1$$, so:

$$
p(x_1 \oplus x_2=0)  = p(x_1=0, x_2=0)  + p( x_1 =1 , x_2 = 1 )
$$

Because we have already assumed that the two random variables are mutually independent, then:

$$
\begin{aligned}
	p(x_1=0, x_2=0)  = p(x_1=0)  p(x_2=0)  \\
	p(x_1=1, x_2=1)  = p(x_1=1)  p(x_2=1)  \\
\end{aligned}
$$

Then, finally:

$$
p(x_1 \oplus x_2=0)  =  p(x_1=0)  p(x_2=0) + p(x_1=1)  p(x_2=1)
$$

Substituting the results above into Formula (1) we can obtain:

$$
\lambda(x_1 \oplus x_2)  = log \frac
{   p(x_1=1)  p(x_2=0) + p(x_1=0)  p(x_2=1) }
{   p(x_1=0)  p(x_2=0) + p(x_1=1)  p(x_2=1)  }
$$

Dividing both the numerator and the denominator by $$p(x_1=0)  p(x_2=0)$$, we get:

$$
\begin{aligned}
	\lambda(x_1 \oplus x_2)  = log \frac
	{   \frac{p(x_1=1)}{p(x_1=0)}  +  \frac{p(x_2=1)} {p(x_2=0)}  }
	{   1+ \frac{p(x_1=1)}{p(x_1=0)}   \frac{p(x_2=1)} {p(x_2=0)}  }
	=
	log \frac
	{ e^{\lambda(x_1)}  + e^{\lambda(x_2)}}
	{ 1 + e^{\lambda(x_1) + \lambda(x_2)}}  \\ \quad  \\
	= log \frac
	{  ( e^{\lambda(x_1)} + 1 )  (  e^{\lambda(x_2)} + 1 )   -   ( e^{\lambda(x_1)} - 1 ) (  e^{\lambda(x_2)} - 1 )  }
	{   ( e^{\lambda(x_1)} + 1 )  (  e^{\lambda(x_2)} + 1 )   +   ( e^{\lambda(x_1)} - 1 ) (  e^{\lambda(x_2)} - 1 )    }   \\
	=
	log \frac
	{  1   -  \frac{ ( e^{\lambda(x_1)} - 1 )}{ ( e^{\lambda(x_1)} + 1 )  }  \frac{ (  e^{\lambda(x_2)} - 1 ) } {(  e^{\lambda(x_2)} + 1 ) } }
	{  1  +  \frac{ ( e^{\lambda(x_1)} - 1 )}{ ( e^{\lambda(x_1)} + 1 )  }  \frac{ (  e^{\lambda(x_2)} - 1 ) } {(  e^{\lambda(x_2)} + 1 ) }   }
	\quad ---- \quad  Formula (2)
\end{aligned}
$$

Now, we introduce the tanh function; the tanh function is defined as follows:

$$
tanh(x) = \frac { e^x - e^{-x}  } { e^x + e^{-x}  } = \frac { e^{2x} -1 }{ e^{2x} + 1}
$$

Then

$$
tanh(\frac{x}{2}) = \frac { e^{x/2} - e^{-x/2}  } { e^{x/2} + e^{-x/2}  } = \frac { e^{x} -1 }{ e^{x} + 1}
\quad ---- \quad  Formula (3)
$$

Using the conclusion above, Formula (2) is further derived into:

$$
\lambda(x_1 \oplus x_2)  =   log \frac
{ 1 -  tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2}) }
{ 1 + tanh( \frac{\lambda(x_1}{2} )  tanh( \frac{\lambda(x_2)}{2}) }
\quad ---- \quad  Formula (4)
$$

Let us start once more from Formula (3) and derive an expression containing log:

Let

$$
t = \frac{ e^x - 1}{ e^x + 1}
$$

Then

$$
x = log \frac{ 1 + t  } { 1 - t }
$$

Substituting the previous two derived results into Formula (3) we obtain:

$$
tanh ( \frac{1}{2}  log \frac{ 1 + t  } { 1 - t } ) = t
$$

Rearranging it a little we obtain:

$$
log \frac{ 1 + t  } { 1 - t } = 2 tanh^{-1} (t)
$$

Using the conclusion above, let us derive Formula (4); regarding, in Formula (4), the quantity

$$
tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2})
$$

as t, then:

$$
\lambda(x_1 \oplus x_2) = -2 tanh^{-1} ( tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2})  )
\quad ---- \quad  Formula (5)
$$

Since the tanh function is an odd function, we have:

$$
tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2}) = tanh( \textcolor{red}{-} \frac{\lambda(x_1)}{2} )  tanh( \textcolor{red}{-} \frac{\lambda(x_2)}{2})
$$

Then Formula (5) can also be written as:

$$
\lambda(x_1 \oplus x_2) = -2 tanh^{-1} ( tanh(  \textcolor{red}{-} \frac{\lambda(x_1)}{2} )  tanh(  \textcolor{red}{-}  \frac{\lambda(x_2)}{2})  )
\quad ---- \quad  Formula (6)
$$

Using mathematical induction, it can be proved that the relation above also holds for several independent random variables. Let us prove the case of three independent random variables.
According to Formula (6), we regard $$x_1\oplus x_2$$ as a single number, then:

$$
\lambda( (x_1 \oplus x_2)   \oplus x_3) = -2 tanh^{-1} ( tanh(  -\frac{\lambda(x_1 \oplus x_2)}{2} )  tanh(  -\frac{\lambda(x_3)}{2})  )
\quad ---- \quad  Formula (7)
$$

Then substituting Formula (6) into Formula (7)

$$
\begin{aligned}
	\lambda( (x_1 \oplus x_2)   \oplus x_3) = -2 tanh^{-1} (
	tanh(  -\frac
	{   -2 tanh^{-1}  (
		tanh(  -\frac{\lambda(x_1) } {2}  )
		tanh(  - \frac{\lambda(x_2)}{2}   )
		)
	}
	{2}
	)
	tanh(  -\frac{\lambda(x_3)}{2})  ) = \\
	-2 tanh^{-1} (
	tanh(  -\frac{\lambda(x_1) } {2}  )
	tanh(  - \frac{\lambda(x_2)}{2}   )
	tanh(  -\frac{\lambda(x_3)}{2})  )  \\
	\quad ---- \quad  Formula (8)
\end{aligned}
$$

By analogy, we can obtain:

$$
\begin{aligned}
	\lambda(x_1 \oplus x_2 \oplus x_3 \oplus \cdots \oplus x_n) =  -2 tanh^{-1}( tanh(-\frac{\lambda(x_1)}{2}) tanh(-\frac{\lambda(x_2)}{2}) tanh(-\frac{\lambda(x_3)}{2}) \cdots tanh(-\frac{\lambda(x_n)}{2}) ) \\
	= -2 tanh^{-1}(\prod_{i=1}^{n} tanh(-\frac{\lambda(x_i)}{2}))
\end{aligned}
$$