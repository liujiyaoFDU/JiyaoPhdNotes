#! https://zhuanlan.zhihu.com/p/644157062


# 【method】ADMM-CSNet |  一种图像压缩感知重建的深度学习方法（1）- 方法解析

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716005344967.png)

[toc]

> :dart: Paper: https://ieeexplore.ieee.org/document/8550778
>
> :rocket: matlab:  https://github.com/yangyan92/Deep-ADMM-Net
>
> :rocket:pytorch:  https://github1s.com/lixing0810/Pytorch_ADMM-CSNet/blob/HEAD/network/CSNet_Layers.py

## 摘要

本文将传统的基于模型的CS方法和数据驱动的深度学习方法相结合，地考虑了一个图像重建的广义CS模型，使用ADMM算法求解该模型，并进一步将ADMM算法扩展为unrolling深度网络，使得ADMM的所有参数通过端到端判别学习实现。

该方法在复数MR成像和实自然图像上实现了较好的重建精度。

## 广义压缩感知模型

本节展示了generalized CS model并提出了两种ADMM迭代求解方法。

### 1.Model

有**约束的标准CS模型**定义为：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716012134675.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/gUZCYH.png)

> + 稀疏系数$s$目标使用正交稀疏基$\Psi$重构原始信号$x$，即$x = \Psi s$；
> + $y$为$x$的观测，$\Phi$为随机欠采样矩阵；

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716013322342.png)

> $\Psi_l^+$是$\Psi^+的第$$l$行。

由于$x$任意分辨率，进一步将图像表示$\Psi^+ x$推广到使用多个固定局部滤波器的图像卷积，并将$l_1$正则推广到更通用的正则化$g(\cdot)$。这便可以放松对$\Psi$正交性和可逆性的要求，定义**广义CS模型**：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716014153912.png)

> $D_l$为第$l$个卷积运算的变换矩阵，$l$为滤波器的数量。

说明：特殊情况下，举个例子一个$D_l$可以是离散余弦变换，$g(\cdot)$可以为$l_q,q\in[0,1]$稀疏正则。<font color="blue">在本文中把他们设置为多个可学习的变换。</font><br />

### 2. ADMM Solver

下面根据引入辅助变量方式的不同，提出了两种ADMM求解器：

**a. Solver 1**：引入变换域独立辅助变量$z = {z_1,...,z_L}$，则公示(8)等价于

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716020203684.png)

其增广拉格朗日函数形式为

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716020528383.png)

![image-20230716020743876](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716020743876.png)

> $<\alpha_l,D_lx-z_l> = \alpha_l(D_lx-z_l)$；$\lambda,\rho$为人为设定的惩罚因子。

接着令$\beta_l = \frac{\alpha_l}{\rho_l}$，则ADMM可以用简化版缩放形式替代，通过交替优化下面的子问题求解：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716021308160.png)

> $\eta_l$为拉格朗日乘数的更新率
>
> 上述ADMM缩放形式推导：（具体推导参考自【1】[【凸优化笔记7】-交替方向乘子法（ADMM） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/106896627)）
>
> ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716023828761.png)

ADMM solver[11]可以通过下面的迭代式进行求解：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716024252257.png)

> $n$代表有多少次迭代，$H$为共轭转置，$S(\cdot)$表示具有参数$\lambda_l/\rho_l$的关于$g(\cdot)$的非线性近端映射（参考【2】[近端映射与软阈值](https://zhuanlan.zhihu.com/p/103161094)）算子，通常是软阈值或者硬阈值函数，分别对应$l_1$或者$l_0$稀疏正则化，具体可以看参考【2】。
>
> 上述求解式的推导如下：（参考【1】）
>
> ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716033152106.png)
>
> ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716033204222.png)

**b. Solver 2**：在图像域中引入辅助变量$z$，则公示(8)等价于：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716105449522.png)

增广拉格朗日函数为：![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716105838995.png)

令$\beta_l = \frac{\alpha_l}{\rho_l}$，则ADMM缩放形式为：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716110202457.png)

[15] 可以通过下面的迭代式进行求解：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716111413392.png)

一种求解[15]中第二项的另外方法是直接进行梯度下降，如[17]，其中$H(\cdot)$表示正则项$g(\cdot)$的导数。

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716111904101.png)

> 注意：1. $\mathcal H(\cdot)$可以是次梯度，是一个范围，例如一范数在0处的梯度是一个负一到1之间的一个随机数（即随机优化算法）；

### 3.重建的高效计算

略

## 用于CS成像的ADMM-CSNET

### 1.Basic-ADMM-CSNet

基于ADMM solver I （公式[12]），如图[1]：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716151627567.png)

### 2. Generic-ADMM-CSNet

对solver2使用神经网络进行拓展，提出Generic-ADMM-CSNet。

下图为提出的网络结构：

![图1 网络结构](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716004543073.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716111413392.png)

![图二 data flow：实心箭头表示正向过程中的数据流，虚线箭头表示反向传播中的反向流。](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716134219882.png)

图二 data flow：实心箭头表示正向过程中的数据流，虚线箭头表示反向传播中的反向流。

整个迭代流程为： (除了整个结构的迭代循环，z zz 的内部（红色框部分）又有多次的迭代循环)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/aa2fe04e7ca1483593041bf3ec7ea1a1-20230717203045010.png)

**a. Reconstruction Layer$X^{(n)}$：**并没有约束不同阶段的网络结构要相同；每个阶段的输出为

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716134931691.png)

在第一阶段，定义![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716134634660.png)

**b.Multiplier Update Layer$M^{(n)}$：**模块的输出为

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716151019956.png)

**c.Auxiliary Variable Update Block**

auxiliary variable update block to implement $\mathbf{Z}^{(n)}$ in eqn. [16]，参考公式[17]对$\mathbf{Z}^{(n)}$进行求解。

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716211634980.png)

每个auxiliary variable update block包括：两个卷积层$(\mathbf{C}_1^{(n,k)},\mathbf{C}_2^{(n,k)})$，一个非线性激活层$\mathbf{H}$，和一个addition layer$\mathbf A$。其中n表示第n和block，k表示迭代次数

+ 卷积层$(\mathbf{C}_1^{(n,k)},\mathbf{C}_2^{(n,k)})$，分别代表$D_l,D_l^T$。

+ 非线性激活层$\mathbf{H}^{(n,k)}$：我们不将其定义为公式8中的正则$g()$的导数，而是使用分段线性函数去学习一个广义的函数，如下，公式3举了例子:


> 注意：stepwise 非线性函数——分段线性（如MLP，或者类似于软thresholding 这样子的激活函数）
>
> 由于$g(\cdot)$为$l_p,p\in[0,1]$的正则项，$\mathcal H(\cdot)$是其梯度函数。分段表示的形式去设计可学习$\mathcal H(\cdot)$的可能是因为其导数是分段形式近似，类似soft thresholding。其中p指定了分段的段点，q是可学习的每个段点的值，可以通过网络训练。 

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716221831298.png)注意：当$c_1^{(n,k)}$为复数，我们需要对实部和虚部分开求解，并且实部和虚部共享分段线性函数，如下：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716222023143.png)

+ Addition Layer $\mathbf A^{(n,k)}$：简单的加权算子，定义初始化为$\mathbf A^{(n,0)}: z^{(n,0)} = x^{(n)}+\beta^{(n-1)}$。该模块的输出为：![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716223129902.png)

<font color="blue">因此，the output of the auxiliary variable update block in $n$-th stage is $z^{(n)} = z^{(n,N-t)}$ 。</font>

模型的可学习参数分析：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230716231645367.png)

### 3. Network Structure Analysis

两种ADMM-CSNet的主要不同之处在于reconstruction layer和network layers $\mathbf Z^{(n)}$的实现。

reconstruction layer：

+ Basic-ADMM-CSNet 使用CS MRI，如命题1、2讨论
+ Generic-ADMM-CSNet 使用CS观测矩阵进行快速矩阵求逆

network layers implementing $\mathbf Z^{(n)}$

+ Basic-ADMM-CSNet 使用公式12的$\mathbf Z^{(n)}$闭式解
+ Generic-ADMM-CSNet 使用unfolded梯度下降迭代$\mathbf Z^{(n)}$，这进一步引入了用于增强网络容量的可学习参数。实现非闭式解。<font color="blue">第6.1节所示，通用ADMMCSNet显著改善了CS-MRI的结果。</font>

## 网络训练

+ paired数据：全采样x和欠采样观测y

+ 损失函数：averaged normalized root mean square error (NRMSE) 

  ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230717002042044.png)

+ 可学习参数：

  ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230717002205548.png)

+ 初始化：by model-based initialization method or random initialization method
  + Model-Based Initialization：use a specific model to initialize 
  + random initialization such as Gaussian distribution initialization

## 参考资料

【1】[【凸优化笔记7】-交替方向乘子法（ADMM） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/106896627)

【2】*[【凸优化笔记4】-4.3 软阈值算子](https://zhuanlan.zhihu.com/p/103161094)*