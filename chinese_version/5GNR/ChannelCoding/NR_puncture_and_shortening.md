---
layout: default
title: "puncture 和 shortening"
back_url: /index.html?lang=zh
---
## puncture 和 shortening
### puncture 和 shortening 的基本原理
在 5G 通信的 Polar Coding 中，有两种方法（截短）来灵活实现码率的，一个是 Puncture，一个是 Shortening.

这两种方法都是把极化码编码后的 N 个数据做舍弃，得到 M ( M < N )个最终的数据，通过信道发送出去。这两种方法的最大区别在于：

舍弃掉的数据，对于接收方来讲，接收方是否知道是舍弃的什么数据：如果接收方知道舍弃的数据是已知的，这种方法称之为 Shortening，否则，称之为 Puncture.

下面这两个图摘自文献 [1]:


![极化码中级-001-puncture 和 shortening 的基本原理-puncture.png](/figure/极化码/puncture和shortening/极化码中级-001-puncture 和 shortening 的基本原理-puncture.png)

![极化码中级-001-puncture 和 shortening 的基本原理-shortening.png](/figure/极化码/puncture和shortening/极化码中级-001-puncture 和 shortening 的基本原理-shortening.png)


图 (a) 中 puncture 掉的两个比特数据，是不确定是什么具体值，因为这两个比特与发送的数据位有关。

图 (b) 中的 shortening 掉的两个比特数据，是确定的，其取值为 0，不管发送的数据比特是什么值。


\begin{quote}
	[1] V. Bioglio, F. Gabry and I. Land, "Low-Complexity Puncturing and Shortening of Polar Codes," 2017 IEEE Wireless Communications and Networking Conference Workshops (WCNCW), San Francisco, CA, USA, 2017, pp. 1-6, doi: 10.1109/WCNCW.2017.7919040.
\end{quote}

### puncture 和 shortening 的使用场景

​									   
![极化码中级-002-puncture 和 shortening 的使用场景-puncture.png](/figure/极化码/puncture和shortening/极化码中级-002-puncture 和 shortening 的使用场景-puncture.png)

![极化码中级-002-puncture 和 shortening 的使用场景-shortening.png](/figure/极化码/puncture和shortening/极化码中级-002-puncture 和 shortening 的使用场景-shortening.png)



Puncture 在高码率的时候性能不好，直观理解是当码率比较高时，被 puncture 掉的比特含有更多的数据比特的信息，而码率比较低时，冻结比特比较多，被打掉的 punture，含有的数据比特的信息比较少。



与之相反， Shortening 在低码率时性能不好，直观理解是，当码率比较低时，数据比特比较少，冻结比特比较多，而为了能做 shortening，则需要在高可靠的位置上放置冻结比特，从而让数据比特放到了不太可靠的位置上。 而数据比特又比较少，从而对性能影响比较大。



文献 [1]，对上述现象有做解释。



在 5G 通信协议中，如果码率 $$R \le \frac{7}{16}$$, 则使用 puncturing，如果码率 $$R > \frac{7}{16}$$， 则使用 shortening。



[1] V. Bioglio, C. Condo, and I. Land, “Design of Polar Codes in 5G New Radio,” CoRR, 2018.

