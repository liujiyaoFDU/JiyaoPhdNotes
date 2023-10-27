# 数学基础

## 概率-高斯分布1-极大似然估计

```{note}
高斯分布在统计机器学习中占据重要的地位。本节内容主要是利用极大似然估计计算高斯分布下的最优参数。
```

假设数据$X$中有$N$个样本，每个样本$x_i$为$p$维数据（含$p$个feature），所有的样本都独立同分布于高斯分布。

$$X = \left(x_1, x_2, \cdots, x_N\right)^T =  \begin{pmatrix} x_1^T\\ x_2^T\\ \vdots\\ x_N^T\\ \end{pmatrix}_{N \times P}$$

其中，$x_i \overset{iid} \sim  N(\mu ,  \Sigma), \theta=(\mu,\Sigma)$。

一维高斯分布：

$$suppose \ \ \ p{=}1, \theta{=}\left(\mu, \sigma ^2\right)   \ \ \ \ then \ \ \ \ \ P(x) = {1\over \sqrt{2\pi}\sigma}\exp(-{{(x-\mu)}^2\over2\sigma^2})$$

高维高斯分布：

$$ P(x) = {1\over(2\pi)^{p\over2}\lvert\Sigma\rvert^{1\over 2}}\exp(-{1\over2}(x-\mu)^T\Sigma^{-1}(x-\mu))$$

---
```{note}
极大似然估计
```

**MLE目标**：$\theta _{MLE} = arg \underset{\theta} \max P\left(X| \theta \right)$，即求最优的参数$\theta$使得对于这部分数据来说，采样出现的概率最大。对于数据集X，其对数似然

$$\begin{equation*}\begin{split} \log P(X|\theta) &= \log \displaystyle \prod _{i=1} ^{N}P(x_i|\theta)\\ &=\displaystyle \sum_{i=1}^{N}\log({1\over\sqrt{2\pi}\sigma}\exp(-{(x-\mu)^2\over{2\sigma^2}}))\\ &=\displaystyle \sum_{i=1}^N[\log{1\over\sqrt{2\pi}}+\log{1\over\sigma}-{(x-\mu)^2\over2\sigma^2}] \end{split}\end{equation*}$$

> 注意：先对$P(X|\theta)$取对数，使得后续求导计算更加方便。
因为$L(\theta)$与$\ln L(\theta)$在同一$\theta$处取到极值。

**首先求最优的参数$\mu$**，求导，找出极大值：

$$\begin{equation*} \begin{split} \mu_{MLE} &= arg \underset{\mu} \max{\log P(X|\theta)}\\ &=arg \underset{\mu} \max\displaystyle\sum_{i=1}^N{-{(x_i-\mu)^2\over2\sigma^2}}\\ &=arg \underset{\mu} \min\displaystyle\sum_{i=1}^N{(x_i-\mu)^2}\\ \end{split}\end{equation*}$$

$$\begin{equation*}\begin{split} {\partial\over\partial\mu}\displaystyle\sum_{i=1}^N(x_i-\mu)^2&=\displaystyle\sum_{i=1}^N2(x_i-\mu)(-1)=0\\ \displaystyle\sum_{i=1}^N(x_i-\mu)&=0\\ \displaystyle\sum_{i=1}^Nx_i-N\mu&=0\\ \mu_{MLE}&={1\over N}\displaystyle\sum_{i=1}^Nx_i \end{split}\end{equation*}$$

故，最优$\mu = {1\over N}\displaystyle\sum_{i=1}^Nx_i$，为样本均值。此结果为*无偏估计*（样本均值是否等于期望），因为$E[\mu_{MLE}]={1\over N}\displaystyle\sum_{i=1}^NE[x_i]={1\over N}\displaystyle\sum_{i=1}^N\mu=\mu$。

**接下来求$\sigma$的最优解**：

$$\begin{equation*} \begin{split} \sigma^2_{MLE} &= arg \underset{\sigma} \max\displaystyle\sum_{i=1}^N[\log{1\over\sigma}-{(x_i-\mu)^2\over2\sigma^2}]\\ &=arg \underset{\sigma} \max\displaystyle\sum_{i=1}^N[-\log{\sigma}-{1\over2\sigma^2}(x_i-\mu)^2] \end{split}\end{equation*}$$

$$\begin{equation*} \begin{split} {\partial\over\partial\sigma}\displaystyle\sum_{i=1}^N[-\log{\sigma}-{1\over2\sigma^2}(x_i-\mu)^2]&=\displaystyle\sum_{i=1}^N[-{1\over\sigma}-{1\over2}(x_i-\mu)^2(-2){1\over\sigma^3}]=0\\ \displaystyle\sum_{i=1}^N[-{1\over\sigma}+(x_i-\mu)^2\sigma^{-3}]&=0\\ \displaystyle\sum_{i=1}^N[-\sigma^2+(x_i-\mu)^2]&=0\\ -N\sigma^2+\displaystyle\sum_{i=1}^N(x_i-\mu)^2&=0\\ \sigma^2_{MLE}&={1\over N}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2 \end{split}\end{equation*}$$

此结果为*有偏估计*，因为$E[\sigma^2_{MLE}]= {N-1\over N}\sigma^2$，想要得到无偏估计结果需要对结果进行放缩：$\hat {\sigma^2} = {1\over N-1}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2$。这里我们给出有偏证明。

## 概率-高斯分布2-极大似然估计（无偏估计 VS 有偏估计）

> 本节内容主要是证明$\mu_{MLE}$和$\sigma^2_{MLE}$的无偏与有偏性，并计算无偏$\sigma^2_{MLE}$。

前一节得到了一维情况下的极大似然估计结果:

+ $\mu_{MLE}={1\over N}\displaystyle\sum_{i=1}^Nx_i$：无偏估计
+ $\sigma^2_{MLE} = {1\over N}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2$： 有偏估计

定义**若$E[\hat\mu] = \mu \ \ \ \ E[\hat\sigma] = \sigma$则无偏，若不相等，则有偏。** 已证$\mu_{MLE}$无偏。下面证明$\sigma^2_{MLE}$有偏：

$$\begin{equation*} \begin{split} E[\sigma^2_{MLE}] &=E[{1\over N}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2]\\ &=E[{1\over N}\displaystyle\sum_{i=1}^N(x_i^2-2x_i\mu_{MLE}+\mu_{MLE}^2)]\\ &=E[{1\over N}\displaystyle\sum_{i=1}^Nx_i^2-2\mu_{MLE}{1\over N}\displaystyle\sum_{i=1}^Nx_i+{1\over N}\displaystyle\sum_{i=1}^N\mu_{MLE}^2]\\ &=E[{1\over N}\displaystyle\sum_{i=1}^Nx_i^2-2\mu_{MLE}^2+\mu^2_{MLE}]\\ &=E[{1\over N}\displaystyle\sum_{i=1}^Nx_i^2-\mu^2_{MLE}]\\ &=E[{1\over N}\displaystyle\sum_{i=1}^N(x_i^2-\mu^2_{MLE})]\\ &={1\over N}\displaystyle\sum_{i=1}^N(E[x_i^2]-E[\mu^2_{MLE}])\\ &={1\over N}\displaystyle\sum_{i=1}^N(D[x_i]+{E[x_i]}^2-D[\mu_{MLE}]-{E[\mu_{MLE}]}^2)\\ &={1\over N}\displaystyle\sum_{i=1}^N(\sigma_{MLE}^2+\mu^2-D[{1\over N}\displaystyle\sum_{i=1}^Nx_i]-\mu^2)\\ &={1\over N}\displaystyle\sum_{i=1}^N(\sigma^2_{MLE}-{1\over N^2}N\sigma_{MLE}^2)\\ &={N-1 \over N}\sigma_{MLE}^2 \end{split}\end{equation*}$$

注意：有一步因为$D[x] = E[x^2]-{E[x]}^2$。

因此$\sigma^2_{MLE}$有偏，则$\sigma^2_{MLE} = {1\over N-1}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2$无偏。

## 概率-高斯分布3-从概率密度函数角度观察

```{note}
本节将从高维角度来看高斯分布
```
当$x$为$p$维随机变量，定义多维高斯分布pdf为

$$x\sim N(\mu,\Sigma) = {1\over(2\pi)^{p\over2}\lvert\Sigma\rvert^{1\over 2}}\exp(-{1\over2}\underbrace{(x-\mu)^T\Sigma^{-1}(x-\mu)}_{二次型})$$

其中，$x=\begin{pmatrix} x_1\\ x_2\\ \vdots\\ x_p\\ \end{pmatrix}$, $\mu=\begin{pmatrix} \mu_1\\ \mu_2\\ \vdots\\ \mu_p\\ \end{pmatrix}$，$\Sigma=\begin{pmatrix}  \sigma_{11}&\sigma_{12}&\cdots&\sigma_{1p}\\ \sigma_{21}&\sigma_{22}&\cdots&\sigma_{2p}\\  \vdots&\vdots&&\vdots\\  \sigma_{p1}&\sigma_{p2}&\cdots&\sigma_{pp}\\  \end{pmatrix}_{p\times p}$。

> 通常来说，$\Sigma$是半正定的(对称的)，本节内容假设是正定的（特征值$\lambda>0$）,便于叙述。首先从概率密度函数来看，$x$是自变量，因此$\exp$前面的内容为常数。
---
+ $(x-\mu)^T\Sigma^{-1}(x-\mu)$：马氏距离($x$和$\mu$之间，$\Sigma^{-1}$看做协方差矩阵的逆)

> 马氏距离：Mahalanobis Distance是一种用于测量多维空间中两个数据点之间的距离的方法，马氏距离考虑了各个特征之间的相关性，因此在具有多个特征的数据集中更为有效。注意，$\Sigma = I$时，马氏距离=欧氏距离。

下面我们分析一下这种马氏距离的实际意义：对$\Sigma = U\Lambda U^T$特征值分解（$U$为正交阵），$U=(u_1,u_2,\cdots,u_p)_{p\times p}$

则有：

$$\begin{equation*}\begin{split} \Sigma &= U\Lambda U^T\\ &=(u_1,u_2,\cdots,u_p)\begin{pmatrix}  \lambda_1&0&\cdots&0\\ 0&\lambda_2&\cdots&\vdots\\  \vdots&\vdots&\ddots&\vdots\\  0&0&\cdots&\lambda_p\\  \end{pmatrix} \begin{pmatrix} u_1^T\\ u_2^T\\ \vdots\\ u_p^T \end{pmatrix}\\ &=(u_1\lambda_1, u_2\lambda_2,\cdots,u_p\lambda_p)\begin{pmatrix} u_1^T\\ u_2^T\\ \vdots\\ u_p^T \end{pmatrix}\\ &=\displaystyle\sum_{i=1}^p{u_i\lambda_iu_i^T} \end{split}\end{equation*} $$

则：$\Sigma^{-1}=(U\Lambda U^T)^{-1}=(U^T)^{-1}\Lambda^{-1}U^{-1}=U\Lambda^{-1} U^T =\displaystyle\sum_{i=1}^p{u_i{1\over\lambda_i}u_i^T}$

将$U^T = U^{-1}$和上式$\Sigma^{-1}$带入，令$y_i=(x-\mu)^Tu_i$，则有

$$\begin{equation*}\begin{split} \Delta=&(x-\mu)^T\Sigma^{-1}(x-\mu)\\ =&(x-\mu)^T\displaystyle\sum_{i=1}^p({u_i{1\over\lambda_i}u_i^T})(x-\mu)\\ =&\displaystyle\sum_{i=1}^p({(x-\mu)^Tu_i{1\over\lambda_i}u_i^T(x-\mu)})\\ =&\displaystyle\sum_{i=1}^p({y_i{1\over\lambda_i}y_i^T})\\ =&\displaystyle\sum_{i=1}^p({y_i^2\over\lambda_i}) \end{split}\end{equation*}$$

上面的推导意味着马氏距离经过变换成为了$\displaystyle\sum_{i=1}^p({y_i^2\over\lambda_i})$，$y_i$是$x-\mu$在特征向量$u_i$上的投影长度。

令$p=2$,则上式便是令$\Delta取不同值时候的同心椭圆$

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/MDL7BE.png
---
height: 300px
name: blackhole
---
```

> 每当$\Delta$取不同值，椭圆就相当于对这一高度的等高线，也对应一个固定的概率值。若$\lambda_i=c$(常量)时，上图便是一个圆

## 概率-高斯分布4-局限性

继上节内容，将$\Delta$代入$p(x)$：$p(x) = {1\over(2\pi)^{p\over2}\lvert\Sigma\rvert^{1\over 2}}\exp(-{1\over2}\Delta)$。每当$p(x)$取一个概率值时，都对应一个固定的$\Delta$，即如下图

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/Zkyyj3.png
---
height: 200px
name: blackhole
---
```

> 每当$p(x)$取为某个概率值时，相当于水平切小山，投影到$x-y$面便是一个椭圆曲线，也是等高线。若$\Sigma$中对的对角元都相等，则称各向同性
---

高斯分布存在局限：有些数据无法用高斯分布表示，因此需要使用GMM等混合模型。


## 概率-高斯分布5-已知联合概率求边缘概率及条件概率

```{note}
本节目标：已知一个多维高斯分布的联合概率，求其边缘概率分布及条件概率分布

本节内容是推导最多最复杂的部分

```

已知：

$$x\sim N(\mu,\Sigma) = {1\over(2\pi)^{p\over2}\lvert\Sigma\rvert^{1\over 2}}\exp(-{1\over2}{(x-\mu)^T\Sigma^{-1}(x-\mu)}), x \in \mathbb R^p$$

其中，$x=\begin{pmatrix} x_1\\ x_2\\ \vdots\\ x_p\\ \end{pmatrix}$, $\mu=\begin{pmatrix} \mu_1\\ \mu_2\\ \vdots\\ \mu_p\\ \end{pmatrix}$，$\Sigma=\begin{pmatrix}  \sigma_{11}&\sigma_{12}&\cdots&\sigma_{1p}\\ \sigma_{21}&\sigma_{22}&\cdots&\sigma_{2p}\\  \vdots&\vdots&&\vdots\\  \sigma_{p1}&\sigma_{p2}&\cdots&\sigma_{pp}\\  \end{pmatrix}_{p\times p}$。

为了简化，我们假设已知两个高维高斯分布的联合分布（将$x$看做$x_a$和$x_b$的联合概率分布）：

$x=\begin{pmatrix} x_a\\ x_b\\ \end{pmatrix}, \mu=\begin{pmatrix} \mu_a\\ \mu_b\\ \end{pmatrix}, \Sigma=\begin{pmatrix}  \sigma_{aa}&\sigma_{ab}\\ \sigma_{ba}&\sigma_{bb}\\ \end{pmatrix}, (a+b=p)$

求：$p(x_a),p(x_b|x_a)$，由对称性可得$p(x_b),p(x_a|x_b)$

> 通用方法：配方法（RPML）。今天使用另一种方法，比配方法简便


```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/3zcMbU.png
---
height: 640px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/aCRHwY.png
---
height: 640px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/ndC19Q.png
---
height: 640px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/h5A1KN.png
---
height: 240px
name: blackhole
---

```


## 概率-高斯分布6-已知边缘和条件概率求联合概率分布

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/sC2hnu.png
---
height: 380px
name: blackhole
---

```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/IMqmt0.png
---
height: 580px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/c68tIQ.png
---
height: 600px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/m6VvVX.png
---
height: 600px
name: blackhole
---
```

## 概率-不等式1-杰森不等式（Jensen's Inequality）

```{note}
杰森不等式在机器学习的推导中经常被用到，其定义如下：假设$f(x)$为凸函数，则$E[f(x)]\geq f(E[x])$
```

证明如下：

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/r8Ajlm.png
---
height: 200px
name: blackhole
---
```
```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/2yqHuu.png
---
height: 400px
name: blackhole
---
```
---

实际上我们在机器学习中使用的更多的是**杰森不等式的变式**，如下推导

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/bu9HuF.png
---
height: 200px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/wvhDs6.png
---
height: 600px
name: blackhole
---
```

## 参考

[1] [机器学习-白板推导系列-数学基础(bilibili)](https://www.bilibili.com/video/av32905863/)

[2] [机器学习-白板推导系列(二)-数学基础笔记](https://zhuanlan.zhihu.com/p/290876484)