# 02 数学基础

## 概率-高斯分布1-极大似然估计

> 高斯分布在统计机器学习中占据重要的地位。本节内容主要是利用极大似然估计计算高斯分布下的最优参数。

假设数据$X$中有$N$个样本，每个样本$x_i$为$p$维数据（含$p$个feature），所有的样本都独立同分布于高斯分布。

$$X = \left(x_1, x_2, \cdots, x_N\right)^T =  \begin{pmatrix} x_1^T\\ x_2^T\\ \vdots\\ x_N^T\\ \end{pmatrix}_{N \times P}$$

其中，$x_i \overset{iid} \sim  N(\mu ,  \Sigma), \theta=(\mu,\Sigma)$。

一维高斯分布：

$$suppose \ \ \ p{=}1, \theta{=}\left(\mu, \sigma ^2\right)   \ \ \ \ then \ \ \ \ \ P(x) = {1\over \sqrt{2\pi}\sigma}\exp(-{{(x-\mu)}^2\over2\sigma^2})$$

高维高斯分布：

$$ P(x) = {1\over(2\pi)^{p\over2}\lvert\Sigma\rvert^{1\over 2}}\exp(-{1\over2}(x-\mu)^T\Sigma^{-1}(x-\mu))$$

---
> 极大似然估计

**MLE目标**：$\theta _{MLE} = arg \underset{\theta} \max P\left(X| \theta \right)$，即求最优的参数$\theta$使得对于这部分数据来说，采样出现的概率最大。对于数据集X，其对数似然

$$\begin{equation*}\begin{split} \log P(X|\theta) &= \log \displaystyle \prod _{i=1} ^{N}P(x_i|\theta)\\ &=\displaystyle \sum_{i=1}^{N}\log({1\over\sqrt{2\pi}\sigma}\exp(-{(x-\mu)^2\over{2\sigma^2}}))\\ &=\displaystyle \sum_{i=1}^N[\log{1\over\sqrt{2\pi}}+\log{1\over\sigma}-{(x-\mu)^2\over2\sigma^2}] \end{split}\end{equation*}$$

注意：先对$P(X|\theta)$取对数，使得后续求导计算更加方便。
因为$L(\theta)$与$\ln L(\theta)$在同一$\theta$处取到极值。

**首先求最优的参数$\mu$**，求导，找出极大值：

$$\begin{equation*} \begin{split} \mu_{MLE} &= arg \underset{\mu} \max{\log P(X|\theta)}\\ &=arg \underset{\mu} \max\displaystyle\sum_{i=1}^N{-{(x_i-\mu)^2\over2\sigma^2}}\\ &=arg \underset{\mu} \min\displaystyle\sum_{i=1}^N{(x_i-\mu)^2}\\ \end{split}\end{equation*}$$

$$\begin{equation*}\begin{split} {\partial\over\partial\mu}\displaystyle\sum_{i=1}^N(x_i-\mu)^2&=\displaystyle\sum_{i=1}^N2(x_i-\mu)(-1)=0\\ \displaystyle\sum_{i=1}^N(x_i-\mu)&=0\\ \displaystyle\sum_{i=1}^Nx_i-N\mu&=0\\ \mu_{MLE}&={1\over N}\displaystyle\sum_{i=1}^Nx_i \end{split}\end{equation*}$$

故，最优$\mu = {1\over N}\displaystyle\sum_{i=1}^Nx_i$，为样本均值。此结果为*无偏估计*，因为$E[\mu_{MLE}]={1\over N}\displaystyle\sum_{i=1}^NE[x_i]={1\over N}\displaystyle\sum_{i=1}^N\mu=\mu$。

**接下来求$\sigma$的最优解**：

$$\begin{equation*} \begin{split} \sigma^2_{MLE} &= arg \underset{\sigma} \max\displaystyle\sum_{i=1}^N[\log{1\over\sigma}-{(x_i-\mu)^2\over2\sigma^2}]\\ &=arg \underset{\sigma} \max\displaystyle\sum_{i=1}^N[-\log{\sigma}-{1\over2\sigma^2}(x_i-\mu)^2] \end{split}\end{equation*}$$

$$\begin{equation*} \begin{split} {\partial\over\partial\sigma}\displaystyle\sum_{i=1}^N[-\log{\sigma}-{1\over2\sigma^2}(x_i-\mu)^2]&=\displaystyle\sum_{i=1}^N[-{1\over\sigma}-{1\over2}(x_i-\mu)^2(-2){1\over\sigma^3}]=0\\ \displaystyle\sum_{i=1}^N[-{1\over\sigma}+(x_i-\mu)^2\sigma^{-3}]&=0\\ \displaystyle\sum_{i=1}^N[-\sigma^2+(x_i-\mu)^2]&=0\\ -N\sigma^2+\displaystyle\sum_{i=1}^N(x_i-\mu)^2&=0\\ \sigma^2_{MLE}&={1\over N}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2 \end{split}\end{equation*}$$

此结果为*有偏估计*，因为$E[\sigma^2_{MLE}]= {N-1\over N}\sigma^2$，想要得到无偏估计结果需要对结果进行放缩：$\hat {\sigma^2} = {1\over N-1}\displaystyle\sum_{i=1}^N(x_i-\mu_{MLE})^2$。这里我们给出有偏证明。

## 概率-高斯分布2-极大似然估计（无偏估计 VS 有偏估计）

> 本节内容主要是证明$\mu_{MLE}$和$\sigma^2_{MLE}$的无偏与有偏性，并计算无偏$\sigma^2_{MLE}$。

