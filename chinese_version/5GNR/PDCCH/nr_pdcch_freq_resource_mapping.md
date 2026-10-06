---
layout: default
title: "PDCCH 频域资源映射"
back_url: /index.html?lang=zh
---
## 频域资源映射

录制的视频在[B站](https://www.bilibili.com/cheese/play/ep574899)

### CCE 到 REG 的映射

协议 38.211的 g20 版本，第 7.3.2.2 节涉及的公式的说明，其中比较绕的公式如下：

$$
\large 
\begin{aligned}
f(x) = (rC + c + n_{shift}) mod (N^{CORESET}_{REG}/L) \\
x=cR + r\\
r=0,1,...,R-1 \\
c=0,1,...,C-1 \\
C = N^{CORESET}_{REG}/(LR) 
\end{aligned}  
\tag{1}
$$

公式 (1) 用于对于第 j 个 CCE，找出来构成这个 CCE 的 bundle:

$$
\{f(6j/L),f(6j/L+1),...,f(6j/L+L-1)\}  \tag{2}
$$

L 是 bundle 的大小， 则 $$N^{CORESET}_{REG}/L$$  就是含有多少个 bundle.

R 是  interleave size，可以看成是把所有的 bundle 分成了几段.

那么 C 就是跳跃多少个 bundle 去取一个 bundle。则 C 乘以 R 就是总的 bundle 数量。

(上面这些都是针对 interleave 的，如果不是 interleave，则 bundle size L = 6, f(x) = x，就是按照顺序取 6 个 REG 作为一个 CCE)。



举例：2个 symbols 的情况， N = 24, L=2，则 N/L=24/2=12个bundles. 

若 R = 3, 则 C = N/(LR) = 24/(2*3) = 4.

$$
\begin{aligned}
CCE(0) = \{f(6*0/2),f(6*0/2+1),f(6*0/2+2)\} =\{f(0),f(1),f(2)\} \\
CCE(1) = \{f(6*1/2),f(6*1/2+1),f(6*1/2+2)\} =\{f(3),f(4),f(5)\} \\
CCE(2) = \{f(6*2/2),f(6*2/2+1),f(6*2/2+2)\} =\{f(6),f(7),f(8)\} \\
CCE(3) = \{f(6*3/2),f(6*3/2+1),f(6*3/2+2)\} =\{f(9),f(10),f(11)\} 
\end{aligned} 
\tag{3}
$$

则：

$$
\begin{aligned}
0=cR+r=0*3+0, c=0,r=0,rC+c=0*4+0=0  \\
1=cR+r=0*3+1, c=0,r=1,rC+c=1*4+0=4  \\
2=cR+r=0*3+2, c=0,r=2,rC+c=2*4+0=8  \\
\\
3=cR+r=1*3+0, c=1,r=0,rC+c=0*4+1=1  \\
4=cR+r=1*3+1, c=1,r=1,rC+c=1*4+1=5  \\
5=cR+r=1*3+2, c=1,r=2,rC+c=2*4+1=9  \\
\\
6=cR+r=2*3+0, c=2,r=0,rC+c=0*4+2=2  \\
7=cR+r=2*3+1, c=2,r=1,rC+c=1*4+2=6  \\
8=cR+r=2*3+2, c=2,r=2,rC+c=2*4+2=10 \\
\\
9=cR+r=3*3+0, c=3,r=0,rC+c=0*4+3=3  \\
10=cR+r=3*3+1, c=3,r=1,rC+c=1*4+3=7  \\
11=cR+r=3*3+2, c=3,r=2,rC+c=2*4+3=11  \\
\end{aligned}
$$

!["CCE2REG的映射-1.png"](/figure/5GNR/PDCCH/CCE2REG的映射-1.png) 




若 R=6，则 C = N/(LR) = 24/(2*6)=2

$$
\begin{aligned}
0=cR+r=0*6+0, c=0,r=0,rC+c=0*2+0=0  \\
1=cR+r=0*6+1, c=0,r=1,rC+c=1*2+0=2  \\
2=cR+r=0*6+2, c=0,r=2,rC+c=2*2+0=4  \\
\\
3=cR+r=0*6+3, c=0,r=3,rC+c=3*2+0=6  \\
4=cR+r=0*6+4, c=0,r=4,rC+c=4*2+0=8  \\
5=cR+r=0*6+5, c=0,r=5,rC+c=5*2+0=10  \\
\\
6=cR+r=1*6+0, c=1,r=0,rC+c=0*2+1=1  \\
7=cR+r=1*6+1, c=1,r=1,rC+c=1*2+1=3  \\
8=cR+r=1*6+2, c=1,r=2,rC+c=2*2+1=5  \\
\\
9=cR+r=1*6+3, c=1,r=3,rC+c=3*2+1=7  \\
10=cR+r=1*6+4, c=1,r=4,rC+c=4*2+1=9  \\
11=cR+r=1*6+5, c=1,r=5,rC+c=5*2+1=11
\end{aligned}
$$

!["CCE2REG的映射-2.png"](/figure/5GNR/PDCCH/CCE2REG的映射-2.png) 




若 R=2, 则 C=N/(LR)=24/(2*2)=6

$$
\begin{aligned}
0=cR+r=0*2+0, c=0,r=0,rC+c=0*6+0=0  \\
1=cR+r=0*2+1, c=0,r=1,rC+c=1*6+0=6  \\
2=cR+r=1*2+0, c=1,r=0,rC+c=0*6+1=1  \\
\\
3=cR+r=1*2+1, c=1,r=1,rC+c=1*6+1=7  \\
4=cR+r=2*2+0, c=2,r=0,rC+c=0*6+2=2  \\
5=cR+r=2*2+1, c=2,r=1,rC+c=1*6+2=8  \\
\\
6=cR+r=3*2+0, c=3,r=0,rC+c=0*6+3=3  \\
7=cR+r=3*2+1, c=3,r=1,rC+c=1*6+3=9  \\
8=cR+r=4*2+0, c=4,r=0,rC+c=0*6+4=4  \\
\\
9=cR+r=4*2+1, c=4,r=1,rC+c=1*6+4=10  \\
10=cR+r=5*2+0, c=5,r=0,rC+c=0*6+5=5  \\
11=cR+r=5*2+1, c=5,r=1,rC+c=1*6+5=11  \\
\end{aligned}
$$

!["CCE2REG的映射-3.png"](/figure/5GNR/PDCCH/CCE2REG的映射-3.png) 



### CCE candidate 位置的确定

协议 38.213的 g20 版本，第 10.1 节涉及的公式，用来确定 在哪些位置盲搜 CCE，涉及的公式如下：

$$
L\cdot \left \{ \left (  Y_{p,{n^u_{s,f}}}   + 
\left \lfloor  
\frac{ m_{s,n_{CI}} \cdot N_{CCE,p}}
{L \cdot M_{s,max}^{(L)}}
\right \rfloor 
+ n_{CI}
\right )  mod \left \lfloor  N_{CCE,p}/L\right \rfloor 
\right \} +i   \tag{1}
$$

$$N_{CCE,p}$$ 是 总的 CCE 的个数，是在 p 这个 CORESET 里面的。

$$M_{s,max}^{(L)}$$  是总的可能出现发给自己的CCE的位置，即候选位置点的个数。

$$m_{s,n_{CI}}$$ 是第几个候选位置点，从 0 开始计算。

我们若假定括号里面的参数为 0， 则公式 (1) 就可以简化为：

$$
L\cdot  \left ( 
\left \lfloor  
\frac{ m_{s,n_{CI}} \cdot N_{CCE,p}}
{L \cdot M_{s,max}^{(L)}}
\right \rfloor 
\right ) 
+i   \tag{2}
$$

再进一步假设，公式 (2) 中的分式，是能够整除的，那么公式 (2) 就进一步简化为：

$$
\frac{ m_{s,n_{CI}} \cdot N_{CCE,p}  }
{ M_{s,max}^{(L)} }
+i   \tag{3}
$$

写成下面的形式就更容易理解：

$$
m_{s,n_{CI}} \cdot
\frac{   N_{CCE,p}  }
{ M_{s,max}^{(L)} }
+i   \tag{4}
$$

也就是把总数 N 划分成 M 份，每一份的开始位置就是 CCE 的候选点的位置。

那在把公式从 (4) 逐级加回到 公式 (1) 是为了：

a) 处理不能整除的情况

b) 增加一些偏移，应对干扰，也可以说是让 CCE 的候选点不是不同的 slot 下都固定在同一频率上的位置。



我们举个例子看看，假如 $$N_{CCE,p} = 48$$ , 为了分析简单，把 $$Y_{p,{n^u_{s,f}}} = 0, n_{CI}=0$$，则：

如果聚合等级 L = 1, 则 M = 6，候选点有：

$$
\begin{aligned}
1 \times \{ \frac{0\times 48 }{ 1 \times 6}\} = 0 \\
1 \times \{ \frac{1\times 48 }{ 1 \times 6}\} = 8 \\
1 \times \{ \frac{2\times 48 }{ 1 \times 6}\} = 16 \\
1 \times \{ \frac{3\times 48 }{ 1 \times 6}\} = 24 \\
1 \times \{ \frac{4\times 48 }{ 1 \times 6}\} = 32 \\
1 \times \{ \frac{5\times 48 }{ 1 \times 6}\} = 40 
\end{aligned}
$$

如果聚合等级 L = 2, 则 M = 6，候选点有：

$$
\begin{aligned}
2 \times \{ \frac{0\times 48 }{ 2 \times 6}\} = 0 \\
2 \times \{ \frac{1\times 48 }{ 2 \times 6}\} = 8 \\
2 \times \{ \frac{2\times 48 }{ 2 \times 6}\} = 16 \\
2 \times \{ \frac{3\times 48 }{ 2 \times 6}\} = 24 \\
2 \times \{ \frac{4\times 48 }{ 2 \times 6}\} = 32 \\
2 \times \{ \frac{5\times 48 }{ 2 \times 6}\} = 40 
\end{aligned}
$$

如果聚合等级 L = 4, 则 M = 2，候选点有：

$$
\begin{aligned}
4 \times \{ \frac{0\times 48 }{ 4 \times 2}\} = 0 \\
4 \times \{ \frac{1\times 48 }{ 4 \times 2}\} = 24 
\end{aligned}
$$

如果聚合等级 L = 8, 则 M = 2，候选点有：

$$
\begin{aligned}
8 \times \{ \frac{0\times 48 }{ 8 \times 2}\} = 0 \\
8 \times \{ \frac{1\times 48 }{ 8 \times 2}\} = 24
\end{aligned}
$$

!["CCE candidate 位置的确定-48CCEs.png"](/figure/5GNR/PDCCH/CCE_candidate_位置的确定-48CCEs.png) 


再举一个不能整除的例子，假如$$N_{CCE,p} = 18$$

如果聚合等级 L = 1, 则 M = 4，候选点有（还不是很确定 5G NR 中是否会有这种情况）：

$$
\begin{aligned}
1\cdot  \left ( \left \lfloor \frac{ 0 \times 18}  {1 \times 4}\right \rfloor \right ) =0 \\
1\cdot  \left ( \left \lfloor \frac{ 1 \times 18}  {1 \times 4}\right \rfloor \right ) =4 \\
1\cdot  \left ( \left \lfloor \frac{ 2 \times 18}  {1 \times 4}\right \rfloor \right ) =9 \\
1\cdot  \left ( \left \lfloor \frac{ 3 \times 18}  {1 \times 4}\right \rfloor \right ) =13 
\end{aligned}
$$

!["CCE candidate 位置的确定-18CCEs.png"](/figure/5GNR/PDCCH/CCE_candidate_位置的确定-18CCEs.png") 