
# 1\. FIFO介绍


1. ## 基本概念


FIFO（First In, First Out）是一种常用的数据结构，用于存储和处理数据。它的工作原理与排队的顺序类似，遵循"先进先出"的原则。即，第一个进入FIFO的数据会是第一个被取出的数据。在FPGA设计中，可以使用AM作为FIFO的存储单元，再通过控制逻辑来管理读写操作和指针的更新。


2. ## FIFO的作用


在FPGA中，主要有以下几个作用


1. **数据缓冲**：在处理数据流时，FIFO可用于平衡速度不一致的两个系统之间的数据流。例如，处理器和外设之间的数据传输。
2. **异步数据传输**：在不同的时钟域之间传递数据时，可以使用FIFO来暂存数据，以便顺利地进行传输。
3. **流水线处理**：在硬件设计中，FIFO常用于多阶段流水线设计中，作为各阶段之间的数据缓冲区。
4. ## 为什么要自己设计FIFO


我们知道，在进行FPGA开发中，FIFO的实现大多采用IP核的形式实现，这样可以大大减少开发周期，但是IP核的形式由于是一个黑盒，会导致项目的不可控及代码移植问题，同时自己设计FIFO有利于我们深入理解FPGA开发，同时对于IC设计等领域，FIFO的电路实现也是一项必备的技能。


2. # 其他必须的知识点


	1. ## 格雷码


在设计FIFO之前，需要用到格雷码（gray code）,因此需要对其进行了解。


我们知道，在计算机等架构中，数据是以二进制形式保存并参与计算的，例如10 dec \= 1010 bin，通常称之为自然二进制。Gray码是一种特殊的二进制编码方式，它与普通的二进制码不同，主要的特点是：相邻的两个数在二进制表示中只有一位发生变化。3位宽的gray码表示如下


 td {white\-space:nowrap;border:1px solid \#dee0e3;font\-size:10pt;font\-style:normal;font\-weight:normal;vertical\-align:middle;word\-break:normal;word\-wrap:normal;}


|  |  |  |
| --- | --- | --- |
| 十进制 | 自然二进制 | Gray码 |
| 0 | 000 | 000 |
| 1 | 001 | 001 |
| 2 | 010 | 011 |
| 3 | 011 | 010 |
| 4 | 100 | 110 |
| 5 | 101 | 111 |
| 6 | 110 | 101 |
| 7 | 111 | 100 |


可以发现Gray 码的关键特点是相邻的两个值在二进制表示中只有一位二进制位的不同。这意味着当从一个数字过渡到下一个数字时，只有一个位改变，从而避免了多个位同时变化时可能引入的误差。


在数字电路中，亚稳态问题一直是工程师所需要尽量减少的，特别在跨时钟域的问题上，而Gray码这种每次只变化一个位（对应在FPGA硬件上表现为只有一个寄存器进行反转），这样就能大大减小发生亚稳态的概率，从而保证电路的稳定性。


2. ## 双口RAM（Dual Port Memory Module）结构


在FPGA中实现FIFO，其基本存储结构是双口ram，以lattice双口RAM IP核为例，其结构如下图所示：


![](https://ex5xn5y3x9.feishu.cn/space/api/box/stream/download/asynccode/?code=MDIxNmI5NDcwYWQ4OTQwOGNhNmQyY2M1MWI1NmFjNjRfRng0YmY4N1RIV1IwYXRyeGt5a0lKczV2RU5ZWUptSURfVG9rZW46Ukgwc2I0aEgyb2VLeFh4clNDVWNyc3lNbmNlXzE3MzE1Nzc2ODE6MTczMTU4MTI4MV9WNA)


双口ram可以同时以不同的时钟速率对数据同时的分别进行写入和读出，其操作时序图如下图：


![](https://ex5xn5y3x9.feishu.cn/space/api/box/stream/download/asynccode/?code=MGJiNzk4YjM2MWMxNDZlZjFiMDc0MzZlOWNkN2FjYjhfd25GcDl3ekVvSXVRZEpPemxrU3c0SVNZS2tCRWlCc05fVG9rZW46QnFNTGI1bk14bzdxZGF4c0RxNGNXNzgzbkNkXzE3MzE1Nzc2ODE6MTczMTU4MTI4MV9WNA)


可以看到，以写为例，在写时钟的上升沿，写入地址和数据，并至少持续一个写入时钟周期，同时在该周期的时钟的下降沿，写使能信号wr\_en\_i拉高，并持续至第二个时钟的上升沿，这样一个数据就被存储到Addr\_0中了。读操作是在读时钟的上升沿给到读地址Addr\_0，同时在该读周期的时钟下降沿，读使能拉高，这样在下一个读时钟周期，地址Addr\_0中的数据被读出在rd\_data\_o数据线中。


 本博客参考[豆荚加速器](https://yirou.org)。转载请注明出处！
