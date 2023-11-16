# 线性动态系统-卡曼滤波（Kalman Filter）

> 2023.11.12

 **Link:** [https://zhuanlan.zhihu.com/p/387428987]

视频如下：

[机器学习-白板推导系列(十五)-线性动态系统-卡曼滤波（Kalman Filter）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1zW411U7fa?share\_source=copy\_web) 

## 一、背景介绍

我们在前面讲过，一般的概率图模型加上时间就是动态模型（Dynamic Model）

动态模型分为三类：

1. **HMM**：状态是离散的
2. **Linear Dynamic System(Kalman Filter)**：线性高斯模型（隐变量和观测变量都是连续的高斯）
3. **Particle Filter**：非线性高斯模型

对于图模型主要涉及到的是learning问题（估计系统的参数，如EM）和inference问题（如下图），kalman filter与上一章的HMM类似，如下图所示，其中$X$是观测变量，$Z$是隐变量。

inference问题种类如下：

+ decoding：已知观测序列，求最有可能的隐状态徐磊
+ evalution问题：观测变量出现的概率

<img src="https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/1ZF5VT.png" alt="1ZF5VT" style="zoom:70%;" />

这一章主要介绍线性动态系统中的卡曼滤波。

---

![bRn1jD](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/bRn1jD.png)

上图为其概率图模型，其中的传递关系与HMM类似，都是使用发射（$z\rightarrow x$）矩阵和转移（$z_{t-1}\rightarrow z_t$）矩阵表示的。

卡曼滤波是线性高斯模型，分为：

* **线性**:发射和转移都为线性
* **高斯**：

图中发射概率和传递概率的关系可描述为如下公式：

$z_t=A\cdot z_{t-1} + B + \epsilon$

$x_t = C\cdot z_t+D+\delta$

可以看出，其本质符合线性关系。 公式后面的 $\epsilon \sim N(0,Q)$与$\delta \sim N(0,R)$是一个服从高斯分布的随机变量，是用来描述噪声的，这一块便可以体现是**线性高斯模型**中的**高斯**。

实际上，发射概率和转移概率也符合高斯分布的：

$P(z_t|z_{t-1}) \sim N(A\cdot z_{t-1} + B, Q)$

$P(x_t|z_t) \sim N(C\cdot z_t+D, R)$

系统的初始状态与HMM中的$\pi$是类似的，在这里，它符合一个高斯分布：$z_1 \sim N(\mu_1, \Sigma_1)$

Kalman Filter的参数包含：$\theta = (A,B,C,D,Q,R,\mu_1,\Sigma_1)$。以上就是Kalman Filter的表示。

---

二、Filtering问题
-------------

我们先对kalman filter进行总结：

![NLkw3n](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/NLkw3n.png)

根据上一节内容，可以总结为如下关系：

$\begin{cases}P(z_t|z_{t-1})=N(A\cdot z_{t-1} + B, Q)\\P(x_t|z_t) = N(C\cdot z_t+D, R)\\P(z_1) = N(\mu_1, \Sigma_1)\end{cases}$

卡尔曼滤波的参数为：$\theta = (A,B,C,D,Q,R,\mu_1,\Sigma_1)$

若写为等式：

$\begin{cases}z_t=A\cdot z_{t-1} + B+\epsilon, & \epsilon \sim N(0,Q)\\x_t=C\cdot z_t +D + \delta, & \delta \sim N(0,R)\end{cases}$

需要解决的问题主要有两个：**Learning**和**Inference**

其中inference可以细分为很多小问题：

* decoding —> HMM（维特比）
* prob of evidence —> $P(O|\lambda)$（前向/后向）
* filtering：$P(z_t|x_1,x_2,\cdots,x_t)$
* smoothing：$P(z_t|x_1,x_2,\cdots,x_T)$
* prediction：
+ $P(z_{t}|x_1,x_2,\cdots,x_{t-1})$
+ $P(x_{t}|x_1,x_2,\cdots,x_{t-1})$

我们这一讲kalman filter最关心的就是filtering问题，也就是求这个边缘后验概率$P(z_t|x_1,x_2,\cdots,x_t)$

这里要借鉴HMM中前向算法的想法，给出一个递推关系。

$P(z_t|x_1,x_2,\cdots,x_t)={ P(x_1,x_2,\cdots,x_t, z_t) \over P(x_1,x_2,\cdots,x_t)}$

由于$x_i$是观测变量，是已知的，因此$P(x_1,x_2,\cdots,x_t)$可以被计算出来，那么:

$P(z_t|x_1,x_2,\cdots,x_t) \propto P(x_1,x_2,\cdots,x_t, z_t)$

其中：

$\begin{equation}\begin{split}P(x_1,x_2,\cdots,x_t, z_t) &= \underbrace{P(x_t|x_1,x_2,\cdots,x_{t-1}, z_t)}_{P(x_t|z_t)（观测独立假设）}\cdot P(x_1,x_2,\cdots,x_{t-1}, z_t) \\&=P(x_t|z_t)\cdot P(x_1,x_2,\cdots,x_{t-1}, z_t)\\&= P(x_t|z_t)\cdot P(z_t|x_1,x_2,\cdots,x_{t-1}) \cdot P(x_1,x_2,\cdots,x_{t-1})\end{split}\end{equation}$

与上述同理，$P(x_1,x_2,\cdots,x_{t-1})$是已知的，因此：

$P(x_1,x_2,\cdots,x_t, z_t) \propto P(x_t|z_t)\cdot P(z_t|x_1,x_2,\cdots,x_{t-1})$

其中 $P(z_t|x_1,x_2,\cdots,x_{t-1})$ 正是前面所说的**Prediction**问题，也就是说在求filtering问题前必须先解决预测问题。

$\begin{equation}\begin{split}P(z_t|x_1,x_2,\cdots,x_{t-1})&= \int_{z_{t-1}} P(z_t,z_{t-1}|x_1,x_2,\cdots,x_{t-1})dz_{t-1} \\&= \int_{z_{t-1}} \underbrace{ P(z_t|z_{t-1}, x_1,x_2,\cdots,x_{t-1})}_{P(z_t|z_{t-1})(HMM中的齐次马尔科夫假设)} \cdot P(z_{t-1}|x_1,x_2,\cdots,x_{t-1})  dz_{t-1}\\&= \int_{z_{t-1}}  P(z_t|z_{t-1})\cdot P(z_{t-1}|x_1,x_2,\cdots,x_{t-1})  dz_{t-1}\\\end{split}\end{equation}$ 

> 最后一步中的 $P(z_{t-1}|x_1,x_2,\cdots,x_{t-1})$ 正是 $t-1$ 时刻的filtering 

以上便求出了**filtering的递推式**

总结一下求解步骤：

* $t=1$：
+ 先根据已知信息求出$P(z_1|x_1)$，称为update
+ 然后使用$P(z_1|x_1)$求出$P(z_2|x_1)$，称为prediction

* $t=2$：
+ 求出$P(z_2|x_1,x_2)$，update
+ 求出$P(z_3|x_1,x_2)$，prediction

* $\cdots \cdots$
* $t=T$
+ 求出$P(z_T|x_1,\cdots,x_T)$，update
+ 求出$P(z_{T+1}|x_1,\cdots,x_T)$，prediction


> 以上是求解步骤，是一个online的过程

一个高斯分布的条件概率分布和边缘概率分布都是高斯分布，而且两个高斯分布的联合概率分布也是高斯分布，所以它的性质非常好，将已知信息全部代入进行求解，可以得到闭式解。



---

三、Filtering问题求解
---------------

上一节中所说一个高斯分布的条件概率分布和边缘概率分布都是高斯分布，而且两个高斯分布的联合概率分布也是高斯分布，因此filtering问题中的**update**和**prediction**的结论也是高斯分布。

以下，推导一下**update**和**prediction**结果的高斯分布表示。

首先，假设update中的结果$P(z_t|x_1,\cdots,x_t)$服从高斯分布$N(\mu_t,\Sigma_t)$；假设prediction中的结果$P(z_t|x_1,\cdots,x_{t-1})$服从高斯分布$N(\mu_t^*,\Sigma_t^*)$

并且，有如下已知条件：

$\begin{cases}P(z_t|z_{t-1})=N(A\cdot z_{t-1} + B, Q)\\P(x_t|z_t) = N(C\cdot z_t+D, R)\\P(z_1) = N(\mu_1, \Sigma_1)\end{cases}$

此外，给出高斯分布常用的计算公式：

$\begin{cases}P(x) = N(x|\mu,\Lambda^{-1})：\Lambda^{-1}为精度矩阵，为了方便计算\\P(y|x) = N(y|Ax+b,L^{-1})：y = Ax+b+\epsilon\\P(y) = N(y|A\mu + b, L^{-1}+A\Lambda^{-1} A^T)\\\end{cases}$

因此：

* **prediction问题：**

$P(z_t|x_1,\cdots,x_{t-1})=\int_{z_{t-1}} P(z_t|z_{t-1}) \cdot P(z_{t-1}|x_1,\cdots,x_{t-1}) dz_{t-1}$

实际上，这里的$P(z_t|z_{t-1})$是在已知$x_1,\cdots,x_{t-1}$的条件下，因此可以把$P(z_{t-1}|x_1,\cdots,x_{t-1})$看作$P(x)$，把$P(z_t|x_1,\cdots,x_{t-1})$看作$P(y)$，因此$P(z_t|z_{t-1})$为$P(y|x)$。

根据已知条件

$P(z_t|z_{t-1})=N(A\cdot z_{t-1} + B, Q)$

$P(z_{t-1}|x_1,\cdots,x_{t-1})=N(\mu_{t-1},\Sigma_{t-1})$

也就是说已知$P(x)、P(y|x)$，求$P(y)$，将已知条件套入到高斯分布的计算公式中：

则，$P(z_t|x_1,\cdots,x_{t-1})$服从$N(A\cdot \mu_{t-1}+B,Q+A\Sigma_{t-1}A^T)$

由于前面假设了$P(z_t|x_1,\cdots,x_{t-1})$服从高斯分布$N(\mu_t^*,\Sigma_t^*)$

因此：

$\begin{cases}\mu_t^*=A\cdot \mu_{t-1}+B\\\Sigma_t^*=Q+A\Sigma_{t-1}A^T\end{cases}$

* **update问题：**

$P(z_t|x_1,\cdots,x_t) \propto P(x_t|z_t)\cdot P(z_t|x_1,\cdots,x_{t-1})$

这里可以把$x_1,\cdots ,x_{t-1}$看作是给定的已知数据，因此将$P(z_t|x_1,\cdots,x_{t-1})$看作$P(x)$，将$P(x_t|z_t)$看作$P(y|x)$，将$P(z_t|x_1,\cdots,x_t)=P(z_t|x_t)$看作$P(x|y)$，也就是已知$P(x),P(y|x)$，求$P(x|y)$。

此外，根据已知条件：

$P(x_t|z_t)=N(C\cdot z_t +D,R)$

$P(z_t|x_1,\cdots,x_{t-1})=N(\mu_t^*,\Sigma_t^*)$

前面有假设$P(z_t|x_1,\cdots,x_t)$服从高斯分布$N(\mu_t,\Sigma_t)$

因此，同理将其套用到高斯分布的计算公式中，即可求出$\mu_t$与$\Sigma_t$。

