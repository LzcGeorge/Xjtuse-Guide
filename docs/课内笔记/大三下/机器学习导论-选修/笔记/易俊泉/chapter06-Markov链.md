[TOC]

# 第六课-Markov链

## 一、贝叶斯网

### 半朴素贝叶斯

#### 朴素贝叶斯

为了降低贝叶斯公式估计后验概率的困难，使用了属性条件独立性假设
$$
P(x_1,x_2,...,x_p|c_j)=\prod_{i=1}^pP(x_i|c_j)
$$
但是这一假设对于很多现实任务无法成立，例如时序数据：语音信号、基因芯片等

#### 半朴素贝叶斯

适当考虑一部分属性间的相互依赖信息，从而既**不需要进行完全联合概率计算，又不至于彻底忽略比较强的属性依赖关系**

### 贝叶斯网

借助**有向无环图**来刻画属性之间的依赖关系，并使用条件概率表来描述属性的联合分布

![image-20220328161316621](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871366.png)

一个贝叶斯网B由结构G和参数𝚯两部分构成

> 网络结构G是一个**有向无环图**，每个节点对应于一个属性；若两个属性有直接依赖关系，则由一条边连接（定性）
>
> 参数𝚯通过**条件概率表定量**描述依赖关系（定量）

根据概率的链式法则，属性的联合概率可以分解成
$$
P(X_1,X_2,...,X_n)=P(X_1)P(X_2|X_1)...P(X_{n-1}|X_{n-2,...X_1})P(X_n|X_{n-1},...,X_1)
$$
但是**贝叶斯网假设每个属性与它的非后裔属性独立**，于是在上例中
$$
P(x_1,x_2,x_3,x_4,x_5)=P(x_1)P(x_2)P(x_3|x_1)P(x_4|x_1,x_2)P(x_5|x_2)
$$
利用条件独立性，有效降低了参数的估计数目

#### 变量之间的典型依赖关系

在贝叶斯网中，三个变量之间的典型依赖关系如图所示：

![image-20220328162200266](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871368.png)

:one:**同父结构**中，给定𝒙𝟏的值，𝒙𝟑和𝒙𝟒条件独立；
$$
\begin{align}
&P(x_3,x_4,x_1)=P(x_1)P(x_3|x_1)P(x_4|x_3,x_1)=P(x_1)P(x_3|x_1)P(x_4|x_1)\\
&P(x_3,x_4|x_1)=P(x_3|x_1)P(x_4|x_1)
\end{align}
$$
:two:**顺序结构**中，给定**𝑥**,则**𝑦**和**𝑧**条件独立——重点

> y只受x影响，z不影响它

$$
P(y,x,z)=P(x,z)P(y|x,z)=P(x,z)P(y|x)=P(x)P(z|x)P(y|x)\\
P(y,z|x)=P(y|x)P(z|x)
$$

:three:v型结构中，给定𝑥4，𝑥1和𝑥2必然不条件独立
$$
P(x_4，x_1,x_2)=P(x_4|x_1,x_2)P(x_1)P(x_2)
$$

##### 独立与条件独立

若 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871369.png) ，则称事件 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871370.png) 相互独立

若 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871371.png) ，其中 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871372.png)，则称事件 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871370.png) 关于事件 ![[公式]](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871374.png) 条件独立

独立推不出条件独立，条件独立推不出独立

## 二、贝叶斯网到Markov模型

在贝叶斯网中，如果变量满足<mark>拓扑次序</mark>，而且任意相连的三个变量之间都是顺序结构，则就得到了一阶的Markov模型

> 对一个有向无环图G进行拓扑排序，是将G中所有顶点排成一个线性序列，使得图中任意一对顶点u和v,若边<u,v>∈E(G),则u在线性序列中出现在v之前。

![image-20220328162421826](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871375.png)

> 图中的x1出现在x2之前，x2出现在x3之前

一阶马尔可夫模型隐含的意思是“**给定当前的值，未来是独立于过去的**”，对应的联合分布可以写成
$$
\begin{align}
P(X_1,...,X_T)&=P(X_T|X_1,...,X_{T-1})\times P(X_1,...,X_{T-1})\\
&=P(X_T|X_{T-1})\times P(X_1,...,X_{T-1})\\
&=P(X_1)\prod_{t=2}^TP(X_t|X_{t-1})\\
\end{align}
$$

估计的参数量:$d+(T-1)d^2$

## 三、Markov链

### Markov模型

在一阶Markov模型中，假设转移概率$P(X_t|X_{t-1})$不依赖于时间，则称为**齐次马尔科夫链**HMC(HomogeneousMarkovChain)，即齐次性。

> 不依赖于时间指的是P(x2|x1)和P(x3|x2)...P(xt|xt-1)的转移概率相同，不依赖于下标

此外，主要关注**有限状态的齐次马尔可夫链**，即假设每个变量的取值（状态）是离散的，都有𝐾种可能的取值
$$
X_t\in \{1,2,...,K\}
$$
有限HMC由以下<mark>三个要素</mark>决定

> :one:状态空间𝑆
>
> :two:初始状态分布$P(X_1):\pi_i=P(X_1=i),i=1,...,K$
>
> :three:状态转移矩阵A

状态转移矩阵A：有限状态的HMC的状态转移概率$A_{ij}$定义如下
$$
A_{ij}=P(X_t=j|X_{t-1}=i)
$$
所有的条件转移概率构成了一个大小为𝐾×𝐾的转移矩阵$A=(A_{ij})_{K\times K}$。A的每一行的和都等于1，因此称A为**随机矩阵（stochasticmatrix）**

### 一步转移

$A_{ij}=P(X_t=j|X_{t-1}=i)$指定了从状态i到状态j的一步转移概率

给定𝑡时刻的状态分布向量$v_t=(P(X_t=1),...,P(X_t=K))$，则
$$
v_{t+1}(j)=P(X_{t+1}=j)=\sum_{i=1}^KP(X_t=i)P(X_{t+1}=j|X_t=i)=\sum_{i=1}^Kv_t(i)A_{ij}=v_t(A:,j)
$$

写成矩阵形式为：$v_{t+1}=v_tA$

### n步转移

$A_{ij}=P(X_t=j|X_{t-1}=i)$指定了从状态i到状态j的一步转移概率，那么一般的，对于n步转移矩阵A(n)，其被定义为

<img src="https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871388.png" alt="image-20220328164611807" style="zoom:67%;" />

由一步转移矩阵形式$v_{t+1}=v_tA$，递归得到$v_{t+n}=v_tA^n$

因此，得到n步转移矩阵$A(n)=A^n$

> 只要A是随机矩阵，那么$A^n$也是随机矩阵，每一行的值的和为1

### m+n步转移矩阵

一步转移矩阵：𝐀；𝑛步转移矩阵：$A(n)=A^n$

基于此，得到𝑚+𝑛步转移矩阵：A(𝑚+𝑛)=A(𝑚)A(𝑛)

从状态𝑖到状态𝑗的𝑚+𝑛步转移（条件）概率等于先从状态𝑖到状态𝑘的𝑚步转移概率，然后乘以从状态𝑘到状态𝑗的𝑛步转移概率，最后在关于𝑘求和：

<img src="https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871391.png" alt="image-20220328165033944" style="zoom:67%;" />

上述等式是Chapman-Kolmogorov（查普曼-科莫高洛夫,CK）方程在有限状态离散时间序列上的应用形式

### 转移图

马尔可夫链的演变可以按图（graph）结构，表示为转移图（transitiongraph），图中的每个节点表示状态可能的取值，每条边都被赋予一个转移概率。因此还可以使用转移图描述状态转移矩阵

![image-20220328165144063](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871377.png)

:question:思考：在贝叶斯网中假设无环，而这里却出现了环？

**<mark>这里的环图描述的是状态之间的转移，而贝叶斯网描述的是变量之间的转移</mark>**

### Markov链基本问题

给定HMC由以下三个要素决定

> :one:状态空间𝑆
>
> :two:初始状态分布$P(X_1):\pi_i=P(X_1=i),i=1,...,K$
>
> :three:状态转移矩阵A

其基本问题1是计算一个特定状态序列的发生概率：

![image-20220328165351014](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871378.png)

<mark>该基本问题表明Markov链是一个**生成模型**(generativemodel)，它描述了一个数据序列是如何被生成的。进一步说，它是一个具有**一阶记忆的随机过程**，因为转移概率仅仅依赖于前一个状态。</mark>

#### 举例：瓶子倒水

> 在一轮内完成：
> ①将甲瓶子的水倒出一半到乙瓶
> ②然后将乙瓶子的水倒出一半到甲瓶
>
> 假设甲瓶和乙瓶一开始分别有a升和b升水
>
> 若a=3/5，b=2/5，
> 则一轮、二轮和三轮后的水量分布分别是多少？
>
> ![image-20220328165844031](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871379.png)
>
> ![image-20220328165855544](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871381.png)

#### 举例：射击问题

**某射击选手，当他射中，则下次也中靶的概率为0.8；当他脱靶，则下次中靶的概率为0.5。若第一次没中，问他第4次射击射中的概率为多少？**

![image-20220430132744724](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871382.png)

**答**：

此问题可用马尔可夫链解决

:one: **定义状态空间**

设射中为状态1，未射中为状态2，状态向量$v=[v_1,v_2]$表示处于这两种状态的概率

:two: **初始状态分布**

初始状态为未射中，即初始状态为
$$
\pi=\begin{bmatrix}
0&1\\

\end{bmatrix}
$$
:three: **状态转移矩阵**A

由状态转移图可得到状态转移矩阵如下：
$$
A=\begin{bmatrix}
0.8&0.2\\
0.5&0.5

\end{bmatrix}
$$


:four: **求解问题**

第四次射击时的状态向量为
$$
v_3=\pi A^3=
\begin{bmatrix}
0.695& 0.305\\

\end{bmatrix}
$$
**第4次射击射中的概率为0.695**



### 稳定状态和平稳分布

平稳分布：对于一个Markov链，给定初始状态分布$v_1=\pi=(P(X_1=1),...,P(X_1=K))$，利用状态转移公式$v_{t+1}=v_tA$，经过一定次数的迭代之后，若能达到$\hat v=\hat{v}A$则称Markov链达到了平稳分布。

一旦进入平稳分布，在之后的任意时刻的概率分布 永远为$\hat v$，马尔可夫链处于稳定状态.稳定状态：$\hat v$经过A转移后仍然是$\hat v$

平稳分布的计算
$$
\hat v = \hat v A\Rightarrow A^Tp=p\qquad p=\hat v^T
$$

p是A^T^的特征为1的特征向量，为列向量，其中$\hat v$要做归一化操作

对于之前的倒水问题：

![image-20220513102301596](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871383.png)



### 典型应用：PageRank

Markov链被应用于如下问题中

> 1、句子补全：给定当前单词，可以预测最有可能出现的下一个单词
>
> 2、网页排序：在给定的N个网页中，都包含“机器学习”。可以根据网页之间的链接（转移概率）对这些网页进行排序

GooglePageRank算法：佩奇排名本质上是一种以网页之间的超链接个数和质量作为主要因素粗略地分析网页的重要性的算法。

对于某个互联网网页A来说，该网页PageRank的计算基于以下两个基本假设：

> 数量假设：在Web图模型中，**如果一个页面节点接收到的其他网页指向的入链数量越多，那么这个页面越重要。**
>
> 质量假设：指向页面A的入链质量不同，**质量高的页面会通过链接向其他页面传递更多的权重**。所以越是质量高的页面指向页面A，则页面A越重要。

PageRank基于**指向网页的链接数量以及输出网页的链接数量定义网页的重要性**。

若网页𝑗到网页𝑖有边，则令$L_{ij}=1$，否则$L_{ij}=0$。因此，𝑗的输出链接为
$$
c_j = \sum^N_{i=1}L_{ij}
$$
对于某个网页𝑖而言，指向网页𝑖的链接数量越多，网页𝑖的重要性越大。对于某个网页𝑗，其输出链接数量越多，对网页𝑖的重要性贡献越小。

网页𝑖的重要性$p_i$通过如下递归等式定义：
$$
p_i=(1-d)+d\sum_{j=1}^N(\frac{L_{ij}}{c_j})p_j
$$
其中， 0是一常数，  𝑑 的典型取值区间  [0.85 0.95]。 𝑑确保了每个网页的重要性分数至少为1-d，$1/c_j$表示一个网站的权重，如果网站j向外有7个连接，那么他对每一个网站的重要性为1/7。

将上述方程写成矩阵表达形式
$$
\pmb p=(1-d)\pmb e+d\cdot \pmb L\pmb D_c^{-1}\pmb p\\
其中，\pmb D_c^{-1}=\text{diag}(1/c_1,...,1/c_N),\quad\pmb e=(1,...,1)^T
$$
若假设N个网页的平均重要性为1，即$e^Tp=N$。则
$$
\pmb p=[(1-d)\pmb {ee}^T/N+d\cdot \pmb L\pmb D_c^{-1}]\pmb p=\pmb{Bp}\\
\pmb B是一个列随机矩阵，\pmb B=(1-d)\pmb {ee}^T/N+d\cdot \pmb L\pmb D_c^{-1}
$$
![image-20220513103902923](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871385.png)

其中$d\pmb L\pmb D_c^{-1}$的每列之和为d

#### 结论

这说明给定：网页之间的链接关系和一个初始$\pmb p_1$

可以通过$\pmb p_{k+1}=\pmb{Bp}_k,\pmb p_{k+1}=N\frac{\pmb p_{k+1}}{\pmb e^T \pmb p_{k+1}}$进行迭代

> N个网页的平均重要性为1，即$e^Tp=N$，第二个公式就是使其强制满足这个条件

平稳分布就是网页的重要性结果

![image-20220513112158640](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871386.png)

### 总结

:one: 马尔可夫链收敛到平稳分布的速度可能很慢。

:two: 马尔可夫链的性质是当前状态只与前一步状态相关，这在某些领域的应用中是一个缺点

> 如在自然语言处理中序列标注问题不仅和单个词相关，而且与观察序列的长度、单词的上下文等都相关。
>
> 一些其他基于马尔可夫链的模型对这些性质有进行改进，如最大熵隐马尔可夫模型（MEMM）可以整个观察序列。