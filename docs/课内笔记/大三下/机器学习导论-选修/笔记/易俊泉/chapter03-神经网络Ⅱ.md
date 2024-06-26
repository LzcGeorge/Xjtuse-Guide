[TOC]

# 第三课——神经网络Ⅱ

机器学习与神经网络

![image-20220320162142265](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870932.png)

多层感知器（MLP，MultilayerPerceptron）是一种前馈[人工神经网络](https://baike.baidu.com/item/人工神经网络/382460)模型，其将输入的多个数据集映射到单一的输出的数据集上。

## 反向传播的一般情形

![image-20220320164555551](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870935.png)

![<imgsrc=""alt="image-20220320162636891"style="zoom:67%;"/>](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870938.png)

第𝑙层第𝑗个神经元和第𝑙−1层神经元之间关系

![image-20220320163831160](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870941.png)

k是上一层神经元的坐标

在第𝑙层第𝑗个神经元的误差如下：
$$
\delta_j^l\equiv \frac{\partial E }{\partial z_j^l}
$$

### 反向传播方程

四个反向传播方程如下：

![image-20220320165103232](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870945.png)

> 第一个反向传播方程是在计算输出层的误差
>
> 第二个反向传播方程是用L层的误差推导L-1层的误差，建立了两层之间的递推关系
>
> 第三个反向传播方程是损失函数关于任意一层偏差b的导数
>
> 第四个反向传播方程是损失函数关于任意一层权重W的导数
>
> 都是使用误差来表示![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870948.png)

### 反向传播算法

:one:**输入𝒙**：为输入层设置对应的激活值ℎ1

**:two:前向传播**：∀𝑙=2,…,𝐿，计算$z^l=(W^{l-1})^Th^{l-1}+b^{l-1}和h^l=\sigma(z^l)$

:three:输出层误差$\delta^L$和反向误差传播$\delta^l(l=L−1,…,2)$：(BP1)和(BP2)

:four:**输出**：误差函数的梯度由(BP3)和(BP4)给出

### 神经网络模型参数求解步骤

> 遍历所有数据算一次损失函数，然后计算梯度，更新梯度。每更新一次都需要将数据集中的所有样本遍历一遍，计算量大

$$
E=\frac{1}{2n}\sum_x||y^x-h^{x,L}||^2
$$



:one:输入训练样本$\{x_i,i=1,...,n\}$的集合

:two:对每个训练样本x：设置对应的激活值$h^{x,1}$

> :one:前向传播：∀𝑙=2,…,𝐿，计算$z^{x,l}=(W^{l-1})^Th^{x,l-1}+b^{x,l-1}和h^{x,l}=\sigma(z^{x,l)}$
>
> :two:输出层误差$\delta^{x,L}=(h^{x,L}-y^x)\odot\sigma'(z^{x,L})$
>
> :three:反向传播误差$\delta^{x,l}(l=L-1,...,2)=\sigma'(z^{x,l})\odot(W^l\delta^{x,l+1})$

:three:梯度下降:对每个$l=L-1,...,2$
$$
\ W^{l-1}\rightarrow W^{l-1}-\frac{\eta}{n}\sum_x h^{x,l-1}(\delta^{x,l})^T\\ b^{l-1}\rightarrow b^{l-1}-\frac{\eta}{n}\sum_x \delta^{x,l}
$$


:four:重复步骤2中的（1-3）和步骤3，直至收敛。

### mini-batch

> 将数据分为若干个批，按照批次来更新参数，这样一个批中的一组数据共同决定了本次梯度的方向，计算量也少了许多

:one:输入训练样本$\{x_i,i=1,...,n\}$的集合

:two:For i=1 :n/m

:three:对批量数据中的每个训练样本x：设置对应的激活值$h^{x,l}$

> :one:前向传播：∀𝑙=2,…,𝐿，计算$z^{x,l}=(W^{l-1})^Th^{x,l-1}+b^{x,l-1}和h^{x,l}=\sigma(z^{x,l)}$
>
> :two:输出层误差$\delta^{x,L}=(h^{x,L}-y^x)\odot\sigma'(z^{x,L})$
>
> :three:反向传播误差$\delta^{x,l}(l=L-1,...,2)=\sigma'(z^{x,l})\odot(W^l\delta^{x,l+1})$

:four:梯度下降:对每个$l=L-1,...,2$
$$
\begin{align}
\ W^{l-1}\rightarrow W^{l-1}-\frac{\eta}{m}\sum_x h^{x,l-1}(\delta^{x,l})^T\\ b^{l-1}\rightarrow b^{l-1}-\frac{\eta}{m}\sum_x \delta^{x,l}
\end{align}
$$


:five:End i(一个epoch，迭代期)

:six:进行多个epoch循环，直至收敛重复步骤2中的（1-3）和步骤3，直至收敛。

> 首先，如果训练集较小，直接使用batch梯度下降法，样本集较小就没必要使用minibatch梯度下降法，你可以快速处理整个训练集，所以使用batch梯度下降法也很好，这里的少是说小于2000个样本，这样比较适合使用batch梯度下降法。不然，样本数目较大的话，一般的mini-batch大小为64到512，考虑到电脑内存设置和使用的方式，如果minibatch大小是2的次方，代码会运行地快一些，64就是2的6次方，以此类推，128是2的7次方，256是2的8次方，512是2的9次方。所以我经常把mini-batch大小设成2的次方。在上一个视频里，我的mini-batch大小设为了1000，建议可以试一下1024，也就是2的10次方。也有mini-batch的大小为1024，不过比较少见，64到512的mini-batch比较常见。

### 方程证明

![image-20220322213324959](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870950.png)

#### BP1的证明

![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870953.png)

**粗略证明**

![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870956.png)

**对每个元素来看**

第𝑙层第𝑗个神经元和第𝑙−1层神经元之间的关系：

![image-20220322210735944](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870959.png)

中间变量$\delta^l_j\equiv\frac{\partial E}{\partial z^l_j}$,称为在第𝑙层第𝑗个神经元的误差。

输出层误差的方程,$\delta^L_j(BP1)$：

![image-20220322211058990](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870961.png)

<mark>输出层误差的向量表达形式,$\delta^L(BP1)$</mark>
$$
\delta^L=(h^L-y)\odot\sigma'(z^L)
$$

#### BP2的证明

![image-20220322212157473](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870953.png)

使用下一层的误差$\delta^{l+1}$表示当前层误差$𝛿^𝑙(BP2)$
$$
\delta^l=\sigma'(z^l)\odot(W^l\delta^{l+1})
$$
![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870966.png)



![image-20220322213016755](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870969.png)

#### BP3的证明

代价函数关于偏置的偏导$\frac{\partial E}{\partial b^{l-1}}(BP3)$：
$$
\frac{\partial E}{\partial b^{l-1}}(BP3)=\delta^l
$$
![image-20220322213630177](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870971.png)

#### BP4的证明

代价函数关于权重的偏导$\frac{\partial E}{\partial W_{jk}^{l-1}}(BP4)$：
$$
\frac{\partial E}{\partial W_{jk}^{l-1}}=h^{l-1}_k\delta^l_j\\
\frac{\partial E}{\partial W^{l-1}}=h^{l-1}(\delta^l)^T
$$
![image-20220322213925444](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870974.png)

(2022/3/10)

## 神经网络模型改进

### 改进损失函数：对数似然

以逻辑回归为例

|          | 二次代价                                                     | 交叉熵                                                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 损失函数 | ![image-20220322215447198](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870976.png) | ![image-20220322215502698](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870979.png) |
|          | ![image-20220322215518396](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870982.png) | ![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870982.png) |
|          | ![image-20220322215530747](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870987.png) | ![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870987.png) |
|          | ![image-20220322215545265](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870993.png) | ![image-20220322215557449](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870995.png) |
|          | ![image-20220322215606112](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695870998.png) | ![image-20220322215613491](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871001.png) |

#### 示例

**任务：让输入1转化为0**

:one:二次代价：初始权重和偏置设置为2.0，此时初始输出为0.98，学习率为0.15

![image-20220322220029739](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871005.png)

> 刚开始学习的速度比较缓慢，对于前150左右的学习次数，权重和偏置没有发生太大变化。随后当预测输出值远离1时，学习速度加快。
>
> :apple:注意到:apple:
> $\sigma'(z)=\sigma(z)(1-\sigma(z))$因此，当预测输出接近1的时候𝜎'(𝑧)很小，导致梯度很小，因而学习缓慢。当预测输出远离1时，学习速度加快。]
>
> ![](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871008.png)
>
> 

:two:交叉熵代价：初始权重和偏置设置为2.0，此时初始输出为0.98，学习率为0.005

![image-20220322220607288](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871011.png)

> 刚开始学习的速度相当快，与期待一样，当严重错误时能以最快速度学习；当预测输出接近正确输出时，学习速度变慢，也符合预期。
>
> 通过梯度公式可以看出，当预测输出与实际输出之间的差异越大时，梯度越大，因此学习速度也更快，相反当二者之间的差异较小时，学习速度变慢。因此交叉代价函数在分类问题应用更加广泛。

#### 反向传播方程

以包含两个隐藏层的三分问题为例，假设样本属于第二类i=2，使用softmax函数代替sigmoid函数

> 通常softmax会被用在网络的最后一层，用来进行最后的分类和归一化
>
> sigmoid通常用于二分类，softmax用于多分类

![image-20220322223709750](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871014.png)

![image-20220322222701534](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871017.png)

### 权重初始化

随机初始化：使用Numpy的 np.random.randn函数生成均值为0，标准差为1的高斯分布

对于第一个隐层的神经元

![image-20220322224027152](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871020.png)

若假设有一半的$h_k^1=1$，另一半为0，则$z_j^2$服从均值为0，标准差为$\sqrt{\frac{p}{2}+1}$的**高斯分布**

![image-20220322224228098](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871023.png)

$\sigma(z_j^2)$接近于1或0，梯度很小，导致学习速度很慢

:watermelon:**改进:watermelon: **：对于任意𝑙层，使用均值为0，标准差为$\frac{1}{\sqrt{n_{in}+1}}$的高斯分布随机分布初始化权重参数$W^{l-1},b^{l-1}$。此时中间变量$z_j^l$服从均值为0，标准差为1的高斯分布：

![image-20220322224550228](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871027.png)

大部分神经元的值$\sigma(z_j^l)$离0或1较远，从而学习速度快

### 减少过拟合：dropout

随机地删除网络中的一半的隐藏神经元，同时让输入层和输出层的神经元保持不变

![image-20220322224731645](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871031.png)

把输入x通过修改后的网络前向传播，然后把得到的损失结果通过修改的网络反向传播。在mini-batch 上执行完这个过程后，在<font color="red">**没有被删除**</font>的神经元上更新对应的参数（w，b）

**Dropout**

:one: 继续重复上述过程

:two: 恢复被删掉的神经元（此时被删除的神经元保持原样，而没有被删除的神经元已经有所更新）

:three: 从隐藏层神经元中随机选择一个一半大小的子集临时删除掉（备份被删除神经元的参数）。

:four: 对一小批训练样本，先前向传播然后反向传播损失并更新参数（w，b）（没有被删除的那一部分参数得到更新，删除的神经元参数保持被删除前的结果）。

### 缓解梯度消失：ReLU

#### 梯度消失问题

对于特定的分类问题，神经网络不是层数越多越好。

假设每层的神经元个数都为1，则

![image-20220322224949592](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871035.png)

若 |𝑾𝑙 |小于1，则当层数很多以后，容易导致梯度消失

#### 使用ReLU进行缓解

ReLU激活函数

![image-20220322225054711](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871038.png)

当𝑧是负数的时候，梯度为0，神经元停止学习（类似于 dropout作用，减少过拟合）；当𝑧大于0时，梯度为1，可 以缓解下溢问题

## 参考资料

[1]庞善民.西安交通大学机器学习导论2022春PPT

[2]周志华.机器学习.北京:清华大学出版社,2016

[3]MichaelA.Nielsen,“NeuralNetworksandDeepLearning”,DeterminationPress,2015

[4\][2-2理解mini-batch梯度下降法](https://www.cnblogs.com/xiaojianliu/articles/9649037.html)



