## Deep Image Prior

![img](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/v2-4e0d514b7007ea450cbc538422c95e50_1440w.webp)

### 为什么需要prior进行图像修复？

A: 1.prior使得目标在正确的流型上进行优化，而不容易偏离到整个空间；2.使得优化目标不断接近并经过目标解。

![image-20240923212322489](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240923212322489.png)

使得优化目标不断接近并经过目标解。但是注意可能会出现过拟合，也就是走过了最优解。因此，在优化过程中需要early stopping。

![pD9zdC](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/pD9zdC.png)

### 为什么神经网络参数可以作为图像的参数化先验？/网络本身是否具有先验信息呢？

这篇文章最大的贡献在于其揭示了**网络在拟合的过程中先拟合简单的，再拟合复杂的**

结论是怎么得到的呢？作者做了一个实验

![img](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/v2-9358f9a6eeedade13faa8781d20f5201_1440w.webp)

好了，有了这个先验，就可以进行图像恢复了

即在网络学习了简单的自然信息后就停下来，让网络输出图像。由于网络此时只学习了简单的自然信息，输出的图像就是复原的图像了

### Method

固定随机编码z，将参数空间$\theta$作为优化目标，当收敛早停之后，$\theta*$作为求解到图像在参数空间的的最优解，通过参数空间到图像空间映射便可以得到求解的高质量图像$x$：$x=f_{\theta*}(z)$。

![img](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/v2-8b34f934cfa0508335849103377ba505_1440w.webp)

### 实验结果

![image-20240923214042620](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240923214042620.png)