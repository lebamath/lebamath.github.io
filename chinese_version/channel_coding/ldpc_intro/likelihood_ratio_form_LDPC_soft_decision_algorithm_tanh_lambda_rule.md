---
layout: default
title: " 软判决算法之似然比形式 (三) tanh-lambda 规则"
back_url: /index.html?lang=zh
---
## 软判决算法之似然比形式 (三) tanh-lambda 规则

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep1070109)

我们研究 二进制域下的 tanh rule, x 为二进制随机变量，$$\oplus$$ 代表二进制加法，满足：

$$
\begin{aligned}
0\oplus 0=0 \\
0\oplus 1=1 \\
1\oplus 0=1 \\
1\oplus 1=0 \\
\end{aligned}
$$

定义 $$\lambda$$ 函数为概率比的对数，即：

$$
\lambda(x) = log \frac{p(x=1)}{p(x=0)}
$$

其中 $$p(x=1)$$ 表示 x 取数值 1 的概率。

换一种写法：

$$
e^{\lambda(x)} = \frac{p(x=1)}{p(x=0)}
$$

我们引入两个相互独立的随机变量 $$x_1, x_2$$ ：

$$
\lambda(x_1 \oplus x_2) = log \frac { p(x_1 \oplus x_2=1) } { p(x_1 \oplus x_2=0 )  }  \quad ---- \quad  公式(1)
$$

其中分子, $$x_1 \oplus  x_2 =1$$ 有两种情况，$$x_1=1, x_2=0$$ 和 $$x_1 =0 , x_2 = 1$$，则：

$$
p(x_1 \oplus x_2=1)  = p(x_1=1, x_2=0)  + p( x_1 =0 , x_2 = 1 )
$$

因为我们已经假设两个随机变量相互独立，则：

$$
\begin{aligned}
p(x_1=1, x_2=0)  = p(x_1=1)  p(x_2=0)  \\
p(x_1=0, x_2=1)  = p(x_1=0)  p(x_2=1)  \\
\end{aligned}
$$

那么，最终：

$$
p(x_1 \oplus x_2=1)  =  p(x_1=1)  p(x_2=0) + p(x_1=0)  p(x_2=1)
$$

同理：

$$x_1 \oplus  x_2 =0$$ 有两种情况，$$x_1=0, x_2=0$$ 和 $$x_1 =1 , x_2 = 1$$，则：

$$
p(x_1 \oplus x_2=0)  = p(x_1=0, x_2=0)  + p( x_1 =1 , x_2 = 1 )
$$

因为我们已经假设两个随机变量相互独立，则：

$$
\begin{aligned}
p(x_1=0, x_2=0)  = p(x_1=0)  p(x_2=0)  \\
p(x_1=1, x_2=1)  = p(x_1=1)  p(x_2=1)  \\
\end{aligned}
$$

那么，最终：

$$
p(x_1 \oplus x_2=0)  =  p(x_1=0)  p(x_2=0) + p(x_1=1)  p(x_2=1)
$$

把上面的结果代入公式 (1) 可以得到：

$$
\lambda(x_1 \oplus x_2)  = log \frac
{   p(x_1=1)  p(x_2=0) + p(x_1=0)  p(x_2=1) }
{   p(x_1=0)  p(x_2=0) + p(x_1=1)  p(x_2=1)  }
$$

分子分母同时除以 $$p(x_1=0)  p(x_2=0)$$, 可得：

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
\quad ---- \quad  公式(2)
\end{aligned}
$$

现在，我们引入 tanh 函数， tanh 函数定义如下：

$$
tanh(x) = \frac { e^x - e^{-x}  } { e^x + e^{-x}  } = \frac { e^{2x} -1 }{ e^{2x} + 1}
$$

则

$$
tanh(\frac{x}{2}) = \frac { e^{x/2} - e^{-x/2}  } { e^{x/2} + e^{-x/2}  } = \frac { e^{x} -1 }{ e^{x} + 1}
\quad ---- \quad  公式(3)
$$

用上面的结论，把公式 (2)  进一步推导为：

$$
\lambda(x_1 \oplus x_2)  =   log \frac
{ 1 -  tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2}) }
{ 1 + tanh( \frac{\lambda(x_1}{2} )  tanh( \frac{\lambda(x_2)}{2}) }
\quad ---- \quad  公式(4)
$$

我们再从公式 (3) 出发，导出有 log 的表达式：

令

$$
t = \frac{ e^x - 1}{ e^x + 1}
$$

则

$$
x = log \frac{ 1 + t  } { 1 - t }
$$

把前两个推导结果，代入公式 (3) 得到：

$$
tanh ( \frac{1}{2}  log \frac{ 1 + t  } { 1 - t } ) = t
$$

稍微整理一下得到：

$$
log \frac{ 1 + t  } { 1 - t } = 2 tanh^{-1} (t)
$$

用上面这个结论，我们来推导公式 (4) ，把公式 (4) 中的

$$
tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2})
$$

看成  t ，则：

$$
\lambda(x_1 \oplus x_2) = -2 tanh^{-1} ( tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2})  )
\quad ---- \quad  公式(5)
$$

由于 tanh 函数是奇函数，所以：

$$
tanh( \frac{\lambda(x_1)}{2} )  tanh( \frac{\lambda(x_2)}{2}) = tanh( \textcolor{red}{-} \frac{\lambda(x_1)}{2} )  tanh( \textcolor{red}{-} \frac{\lambda(x_2)}{2})
$$

则公式 (5) 也可以写成：

$$
\lambda(x_1 \oplus x_2) = -2 tanh^{-1} ( tanh(  \textcolor{red}{-} \frac{\lambda(x_1)}{2} )  tanh(  \textcolor{red}{-}  \frac{\lambda(x_2)}{2})  )
\quad ---- \quad  公式(6)
$$

用数学归纳法，可以证明多个独立随机变量，以上关系也是成立的。我们证明一下三个独立随机变量的。
根据公式 (6)， 我们把  $$x_1\oplus x_2$$ 看成一个数，则：

$$
\lambda( (x_1 \oplus x_2)   \oplus x_3) = -2 tanh^{-1} ( tanh(  -\frac{\lambda(x_1 \oplus x_2)}{2} )  tanh(  -\frac{\lambda(x_3)}{2})  )
\quad ---- \quad  公式(7)
$$

再把公式 (6) 代入公式 (7)

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
tanh(  -\frac{\lambda(x_3)}{2})  )=  \\
-2 tanh^{-1} ( 
tanh(  -\frac{\lambda(x_1) } {2}  )   
tanh(  - \frac{\lambda(x_2)}{2}   )  
tanh(  -\frac{\lambda(x_3)}{2})  )  \\
\quad ---- \quad  公式(8)
\end{aligned}
$$

依次类推，可以得到：

$$
\begin{aligned}
\lambda(x_1 \oplus x_2 \oplus x_3 \oplus \cdots \oplus x_n) =  -2 tanh^{-1}( tanh(-\frac{\lambda(x_1)}{2}) tanh(-\frac{\lambda(x_2)}{2}) tanh(-\frac{\lambda(x_3)}{2}) \cdots tanh(-\frac{\lambda(x_n)}{2}) ) \\ 
= -2 tanh^{-1}(\prod_{i=1}^{n} tanh(-\frac{\lambda(x_i)}{2}))
\end{aligned}
$$