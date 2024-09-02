# Parallel MRI reconstruction

[toc]

## 参考链接：

[1] [ISMRM-MR Academy: Reconstruction Methods for Undersampled Data](https://www.ismrm.org/online-education-program/phys-eng/quiz-physics-engineering-reconstruction-methods-for-undersampled-data//)

[2]

## 并行成像简介

### 为什么要欠采样？

+ Static imaging: Reduce scan time
+ Dynamic imaging: inherent
  + dynamic contrast motion
  + body motion

All such situations require assumptions/ constraints/ models. 需要引入额外信息假设，可以通过约束模型进行优化重建，也可以通过其他方法。下图为欠采样的例子（替换成更好地）：

![undersample](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/undersample.jpg)

### MRI信号基础模型

![image-20230705173912785](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230705173912785.png)

$f(r)$为连续的空间函数，即想要重建的目标图像。从数学的角度，MRI的采样都为欠采样。

![image-20230705174323305](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230705174323305.png)

### 空间灵敏度获取方法

1. 空间灵敏度图像估计：在重建阶段的第一步，通过对采集到的数据进行处理，估计每个接收线圈的空间灵敏度图像。这些灵敏度图像表示了各个线圈对不同位置上的信号的接收能力。常用的估计方法是根据接收线圈的位置和强度信息进行插值和加权处理。
2. 测量

### 基于k空间的并行成像

基于K空间的磁共振并行成像是利用**多通道的相控阵列线圈**，采集部分k空间数据结合**线圈的敏感度**信息，对未采集的K空间数据进行估计，从而组合成全采样的K空间数据。在K空间域的并行重建的典型方法有SMASH、AUTO-SMASH、VD-ATUO-SAMSH 以及目前普遍使用的**GRAPPA**。

## SENSE



## SMASH 

> (Simultaneous Acquisition of Spatial Harmonics)

> 算法简介
>
> 1. SMASH的多线圈采集和空间灵敏度获取和SENSE类似
> 2. 数据合并：在重建阶段的第二步，使用空间灵敏度图像对采集到的数据进行组合。每个线圈接收到的数据会被乘以相应位置上的空间灵敏度值，以减少相对强信号的重叠。
> 3. 图像重建：在重建阶段的最后一步，使用重建算法（如傅里叶变换）对合并后的数据进行处理，生成最终的图像。重建算法根据数据的频谱信息恢复图像的空间分布。由于采集到的数据是同时来自多个线圈，利用了**空间谐波**的信息，重建出的图像具有更高的空间分辨率。

SMASH的基本概念是线圈敏感度的线性组合可以直接生成确定的相位编码steps。敏感度值$C_k(x,y)$与适当的线性权重$n_k^{(m)}$相组合，以生成具有阶数$m$的正弦空间灵敏度变化的复合灵敏度分布$C_m^{comp}$:

![image-20230704010434891](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230704010434891.png)

![image-20230704013051414](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230704013051414.png)

上式中$\Delta k_y＝2\pi/FOV$。对于$N_c$个阵列线圈，并且索引$k$从$1$到$N_c$；$m$是整数，指定生成的空间谐波的阶数。

==上式中，只有线性权重$n_k^{m}$是未知的==。

## GRAPPA

> **ACS lines在GRAPPA中用于自动校准感受度分布，以便在加速数据采集时进行图像重建。**
>
> ACS（Autocalibrating Signal）lines是指用于自动校准的信号线。在MR图像重建中，GRAPPA是一种用于加速数据采集的技术，它利用附加的自校准线（ACS lines）来估计感受野内的敏感度分布。
>
> ACS lines通常位于图像采集区域之外的一些区域，例如在k空间中的角落或边缘。这些额外的自校准线用于估计每个线圈元素的空间敏感度分布，而无需依赖外部敏感度校准（如额外的校准扫描）。
>
> 通过在自校准线上进行采样，GRAPPA算法可以根据这些线上的信号和附近的加速线来推断出未采样线上的信号。使用这些推断的信号，可以进行更快速的图像重建，从而实现数据加速。
>
>  