#! https://zhuanlan.zhihu.com/p/677141632

[toc]


# 扩散模型 | 1.Score-based model精讲

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240110221752107.png)

本文主要讲解score-based model的开山之作，发表于2019年NIPS的**Generative Modeling by Estimating Gradients of the** **Data Distribution**，也称作*Noise Conditional Score Network (NCSN)*。这也是我非常喜欢的一个工作，从文章的撰写到算法的设计以及代码的实现，足以看出作者多年积累的深厚的数理和代码底蕴。

> Paper: https://arxiv.org/abs/1907.05600
>
> Code: https://github.com/ermongroup/ncsn

## 摘要

本文提出了一个全新的生成模型，不是直接学习数据的分布，而是学习分布的梯度。通过分布的score function便可以得到梯度，逐步逼近目标数据。假设通过score matching方法得到了score模型，便可以使用郎之万动力学迭代过程从一个分布走到目标分布。并且，因为当数据存在于低维流形上时，梯度可能不明确且难以估计，所以我们通过不同水平的高斯噪声扰动数据，实现更精确估计数据分布的梯度。

## score-based model概述

给定一个dataset $\{\mathbf{x}_1, \mathbf{x}_2, \cdots, \mathbf{x}_N\}$ 服从分布$p(x)$，我们的目标是训练一个生成器去近似分布$p(x)$。我们可以通过从该近似分布中采样，以获取新的数据。

我们首先定义一个概率分布。在likelihood based models中，我们定义生成的normalized probability **density function**为
$$
\begin{align} p_\theta(\mathbf{x}) = \frac{e^{-f_\theta(\mathbf{x})}}{Z_\theta}, \end{align}
$$
其中，$z_\theta > 0$是一个**normalizing constant**，保证了$\int p_\theta(\mathbf{x}) \textrm{d} \mathbf{x} = 1$ ，$f_\theta(\mathbf{x}) \in \mathbb{R}$是一个可学习的function（一般是一个神经网络或者解析表达式）。我们可以通过最大化likelihood来train $p_\theta(x)$ ：
$$
\begin{align} \max_\theta \sum_{i=1}^N \log p_\theta(\mathbf{x}i).  \end{align}
$$
*为了计算$p_\theta (x)$*，我们必须计算归一化常数$z_\theta$。根据定义$z_\theta=\int_{x}e^{-f_\theta(x)}dx$ ，然而计算这样一个积分常数并不容易。因此，为了使最大似然训练可行，基于似然的模型必须**限制其模型架构**（例如：a.flow-based model中要求$f_\theta$的雅可比矩阵可逆，具体来说可以参考[NICE模型](https://arxiv.org/abs/1410.8516)的网络设计，其采用了独特的**Coupling Layer**；b.自回归模型中的因果卷积）。这样做使得$z_\theta$近似$\int_{x}e^{-f_\theta(x)}dx$或者易于处理（例如，VAE 中的变分推断，或 MCMC 采样）这可能在计算上很昂贵）。

通过score function 替代 density function，我们可以避开难以处理的normalizing constants问题。即**score-based model不是直接学习概率分布，而是学习score**。

下面这幅图展示了score-based model最基本的想法：假设我们已经估计得到数据的score，也就是log概率密度的梯度，那么便可以通过类梯度下降（文中为**朗之万动力学**）的形式，逐渐从一个随机采样点移动到我们想要的分布。

![score-based model](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/87TjuJ.png)


## “score”的概念

接下来我们一步步介绍score-based model 的每一个组成。首先我们介绍一下score的概念。一般来说，生成式模型的目标是估计目标分布的概率$p_\theta(x)$，而s**core function定义为数据样本的log密度的梯度：**$\nabla_\mathbf{x} \log p(\mathbf{x})$ ，其中取对数log是为了把[0,1] 映射到无穷$(-\infty,0]$范围。下图表示了数据样本的概率分布（蓝色）和log密度的梯度（箭头）。

![“score”的概念](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/wSGalX.png)

针对score function设计的model称为score-based model $\mathbf{s}_\theta(\mathbf{x})$，**目标是希望学习到一个$\mathbf{s}_\theta(\mathbf{x}) \approx \nabla_\mathbf{x} \log p(\mathbf{x})$**。举个例子，我们可以容易地使用energy-based model 参数化一个score-based model：
$$
\begin{equation} \mathbf{s}\theta (\mathbf{x}) = \nabla_{\mathbf{x}} \log p_\theta (\mathbf{x} ) = -\nabla_{\mathbf{x}} f_\theta (\mathbf{x}) - \underbrace{\nabla_\mathbf{x} \log Z_\theta}_{const}{=0} = -\nabla\mathbf{x} f_\theta(\mathbf{x}). \end{equation}
$$
和likelihood方法相同，我们可以最小化**Fisher divergence**去训练模型：
$$
\begin{equation} \mathbb{E}{p(\mathbf{x})}[\| \nabla_\mathbf{x} \log p(\mathbf{x}) - \mathbf{s}_\theta(\mathbf{x}) \|2^2] \end{equation}
$$
*然而，直接计算Fisher divergence是不可行的，因为数据的score$\nabla_\mathbf{x} \log p(\mathbf{x})$*是未知的。因此，我们改变思路，用一种叫**score matching**的方法去求解。score match可以直接在数据集上估计，并可以使用SGD进行优化。实际上，**score-based model的唯一要求是输入和输出的维度应当相同**。

## 郎之万动力学（Langevin dynamics）

建立好score-based model $\mathbf{s}_\theta(\mathbf{x}) \approx \nabla\mathbf{x} \log p(\mathbf{x})$之后，相当于我们可以通过估计的score一步一步的逼近真实分布。我们可以使用Langevin dynamics迭代过程从一个任意分布走到目标分布。Langevin dynamics提供一种MCMC过程，仅使用$\nabla_\mathbf{x} \log p(\mathbf{x})$，去从$p(x)$分布中采样。具体来说，我们给定一个先验分布$\mathbf{x}_0 \sim \pi(\mathbf{x})$​，生成一系列链式结果，其迭代代公式如下：
$$
\mathbf{x}_{i+1} \gets \mathbf{x}i + \epsilon \nabla_\mathbf{x} \log p(\mathbf{x}) + \sqrt{2\epsilon}~ \mathbf{z}_i, \quad i=0,1,\cdots, K
$$
其中，$\mathbf{z}_i \sim \mathcal{N}(0, I)$，$\epsilon \to 0$，$K \to \infty$，最终$\mathbf{x}_K$收敛为$p(\mathbf{x})$的采样。当 $\epsilon$足够小、$K$足够大时，误差可以忽略不计。

当得到$\mathbf{s}_\theta(\mathbf{x}) \approx \nabla\mathbf{x} \log p(\mathbf{x})$之后，我们使用下面公式进行郎之万动力学采样：
$$
\mathbf{x}_{i+1} \gets \mathbf{x}i + \epsilon \nabla\mathbf{x} \log p(\mathbf{x}) + \sqrt{2\epsilon}~ \mathbf{z}_i, \quad i=0,1,\cdots, K
$$

![Langevin dynamics sampling](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/langevin.gif)

## Naive score-based generative modeling and its pitfalls

根据上述方法实现的生成过程依旧是有问题的：**在数据密度较低的位置，score的估计往往是不准确的。**由于随机采样点很大程度会落在概率密度较低的位置，因此推理阶段的早期，模型很容易根据错误的梯度而脱轨，不容易获得较好的结果。

> 为什么在数据密度较低的位置，score的估计往往是不准确的？
>
> ：模型的优化目标Fisher divergence可以写成如下形式：
>
> $$ \mathbb{E}_{p(\mathbf{x})}[\| \nabla_\mathbf{x} \log p(\mathbf{x}) - \mathbf{s}*\theta(\mathbf{x}) \|_2^2] = \int p(\mathbf{x}) \| \nabla_\mathbf{x} \log p(\mathbf{x}) - \mathbf{s}_\theta(\mathbf{x}) \|_2^2 \mathrm{d}\mathbf{x}. $$
>
> 由于真实数据的得分函数和基于得分的模型之间的差异被$p(x)$加权，所以在密度较低的区域中$p(x)$很小，这些差异很大程度上被忽略了。因此估计的score是不准确的。

![Estimated scores are only accurate in high density regions.](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/pitfalls.jpg)

如何实现准确估计score呢？为了更准确的估计score，我们对原始数据加噪声以此来扩大数据密度范围，让原本低密度的数据区域膨胀，这样能够比较准确估计score的区域就扩大很多了。如下图：

![perturb data points with noise and train score-based models on the noisy data points instead](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/single_noise.jpg)

然而，给原始分布加入噪声会导致原始分布发生改变。因此，所加的噪声需要去权衡。那么如何选择所加的噪声强度呢？我们现在所知道的理论是：

+ 对原始分布加较强的噪声，会使得更多的区域可以准确估计score，但是这样会严重损害原本的数据分布；
+ 对原始分布加较弱的噪声，会尽可能的避免损害原始数据的分布，但是这样会导致无法在大多数区域准确估计出score；

那么文中给出了一个巧妙的解决思路，即：**在推理的不同阶段加不同强度的噪声，推理的刚开始阶段噪声较大，推理的最终阶段噪声较小。**

Inference算法流程图如下所示：

![NCSN inference via annealed Langevin dynamics ](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240110204129768.png)

可以看到，Sampling的迭代过程有两个循环，外循环是$L$种不同的噪声和步长Level。在每个level中，步长$\alpha_i$和噪声$\sigma_i$级别是固定的。内循环实际上就是前面提到的郎之万迭代过程。如果我们只有一个噪声级别，也就是$L=1$，那么整个流程几乎是和DDPM一致的。

## Defination of Noise Conditional Score Networks (NCSN)

NCSN定义如下，

![7KbrxT](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/7KbrxT.png)

基于噪声的分数网络$\mathbf s_\theta(\mathbf x,\sigma)$可以估计任意噪声水平下的分数。噪声分数网络的设计如下：

![image-20240318144714833](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240318144714833.png)

## 如何训练NCSN？

本文使用了一种**denoise score matching**的方法估计score。对于加噪的图像$q_\sigma(\tilde{\mathbf{x}}\mid\mathbf{x})=\mathcal{N}(\tilde{\mathbf{x}}\mid\mathbf{x},\sigma^2I)$，其对数密度梯度为$\nabla_{\tilde{\mathbf{x}}}\operatorname{log}q_{\sigma}(\tilde{\mathbf{x}}\mid\mathbf{x})=-(\tilde{\mathbf{x}}-\mathbf{x})/\sigma^{2}$（注意是对$\tilde{\mathbf{x}}$求导），给定一个噪声水平$\sigma$，，denoiser score matching的目标函数为：
$$
\ell(\boldsymbol{\theta};\sigma)\triangleq\frac{1}{2}\mathbb{E}_{p_{\mathrm{data}}(\mathbf{x})}\mathbb{E}_{\tilde{\mathbf{x}}\sim\mathcal{N}(\mathbf{x},\sigma^2I)}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}},\sigma)+\frac{\tilde{\mathbf{x}}-\mathbf{x}}{\sigma^2}\right\|_2^2\right]
$$
那么，对于一系列噪声水平$\sigma \in \{\sigma_i\}_{i=1}^L$， 我们可以得到一个统一的目标函数：
$$
\mathcal{L}(\boldsymbol{\theta};\{|\sigma_i\}_{i=1}^L)\triangleq\frac{1}{L}\sum_{i=1}^{L}\lambda(\sigma_i)\ell(\boldsymbol{\theta};\sigma_i),
$$
其中，$\lambda(\sigma_i)>0$是和$\sigma_i$有关的系数，为了平衡损失，使得每个加权项数量级范围接近。关于$\lambda(\sigma_i)>0$的选择，文章中给定了一种方案，即让上述公式求和的每一项量级接近：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240110212822548.png)

其实，观察加噪的图像对数密度函数可知，我们想要估计的score，实际上和原图只差一个系数的关系，因此可以使用一个噪声估计网络来估计score。

本人自己也写了一段训练的流程：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240110221710475.png)

## NSCN如何推理？

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/swpg2s.png)

> **说明1（为什么大噪声水平的郎之万采样结果可以作为下一步较小噪声水平的分布的初始化？）**：As score estimation and Langevin dynamics perform better in **high-density regions**, samples from $q_{\sigma_1}(\mathbf x)$ will serve as good initial samples for Langevin dynamics of $q_{\sigma_2}(\mathbf x)$. Similarly, $q_{\sigma_2}(\mathbf x)$ provides good initial samples for *q**σ**i* (**x**), and finally we obtain samples of good quality from $q_{\sigma_L}(\mathbf x)$.

> **说明2（步长$\alpha_i$的选取）**：
>
> ![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/sJmWyA.png)

> **注意：**1. $\sigma$设置为等比数列比较好；2.参数更新时加上EMA更稳定；

## 补充1：关于文章2.1 **Score matching for score estimation**的推导

分数匹配[24]最初是为了学习基于未知数据分布的i.i.d.样本的非标准化统计模型而设计的，在位置数据分布的情况下近似对数密度梯度。与典型的分数匹配不同，我们选择不使用基于能量的模型的梯度作为分数网络，以避免高阶梯度带来的额外计算。对于目标函数$\frac12\mathbb{E}_{p_{\mathrm{data}}}[\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})-\nabla_{\mathbf{x}}\log p_{\mathrm{data}}(\mathbf{x})\|_{2}^{2}]$，标准的得分匹配等价于该目标函数的形式为：
$$
\mathbb{E}_{p_\text{data}(\mathbf{x})}\bigg[\operatorname{tr}(\nabla_{\mathbf{x}}\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}))+\frac{1}{2}\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right\|_{2}^{2}\bigg]
$$

> 上式的推导如下：
> $$
> \begin{aligned} &\mathbb{E}_{p_{\mathrm{data}}}[\|\nabla_{\mathbf{x}}\log p_{\mathrm{data}}(\mathbf{x})-\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\|_{2}^{2}] \\=&\frac{1}{2}\mathbb{E}_{p_\text{data}}[(\nabla_x \log p_\text{data}(x) - \nabla_x \log p_\theta(x))^2] \\ =& \frac{1}{2} \int p_\text{data}(x) (\nabla_x \log p_\text{data}(x) - \nabla_x \log p_\theta(x))^2 \text{d}x\\ =& \underbrace{\frac{1}{2} \int p_\text{data}(x) (\nabla_x \log p_\text{data}(x))^2 \text{d}x}_{\text{const}} + \frac{1}{2} \int p_\text{data}(x) (\nabla_x \log p_\theta(x))^2 \text{d} x \\ &- \int p_\text{data}(x) \nabla_x \log p_\theta(x) \nabla_x \log p_\text{data}(x)\text{d}x. \end{aligned}
> $$
> 第三项等于：
> $$
> \begin{aligned} &- \int p_\text{data}(x) \nabla_x \log p_\theta(x) \nabla_x \log p_\text{data}(x) \text{d}x\\ 
> =& - \int p_\text{data}(x) \nabla_x \log p_\theta(x) \frac{\nabla_x p_\text{data}(x)}{p_\text{data}(x)}\text{d} x\\ 
> =& - \int \nabla_x \log p_\theta(x) \nabla_x p_\text{data}(x)\text{d} x\\ 
> \underbrace{=}_{\int udv=uv-\int vdu}& - \underbrace{p_\text{data}(x) \nabla_x \log p_\theta(x)\bigg|_{-\infty}^{\infty}}_{when|x|\rightarrow\infty,p_{data}\rightarrow0} + \int p_\text{data}(x) \nabla_x^2 \log p_\theta(x) \text{d} x\\ {=}& \mathbb{E}_{p_\text{data}}[\nabla_x^2 \log p_\theta(x)], \end{aligned}
> $$
> 因此，目标函数变为：
> $$
> \mathbb{E}_{p_\text{data}}\bigg[\operatorname{tr}( \nabla_{\mathbf{x}}^2 \log p_\theta(\mathbf{x})) + \frac{1}{2} \| \nabla_\mathbf{x} \log p_\theta(\mathbf{x})\|_2^2 \bigg] + \text{const},
> $$
> 本推导参考了：https://zhuanlan.zhihu.com/p/485023846，具体推导细节可以查看原文章。

上面等价的目标函数中，$\nabla_{\mathbf{x}}^2s_\theta(\mathbf{x})$为$s$的Jacobian。

然而，在实际情况中，由于$\operatorname{tr}(\nabla_{\mathbf{x}}^2s_\theta(\mathbf{x}))$难以计算，因此score matching不能扩展到深度网络和高维数据。下面我们讨论两种常用的的大规模score matching方法：

1. **Denoising score matching**：也就是本文使用的方法，前文已经说明，不多赘述。

2. **Sliced score matching**：使用随机投影方法近似$\operatorname{tr}(\nabla_{\mathbf{x}}^2s_\theta(\mathbf{x}))$，目标为：
	$$
	\mathbb{E}_{p_{\mathbf{v}}}\mathbb{E}_{p_{\text{data}}}\left[\mathbf{v}^{\intercal}\nabla_{\mathbf{x}}\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\mathbf{v}+\frac{1}{2}\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right\|_{2}^{2}\right]
	$$

![Sliced score matching比Denoising score matching效果更好，但是时间消耗更大](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240110221433082.png)

## 补充2：Inpainting思路

作者还提出了一种Inpainting的思路，也就是对于训练好的score-based model，可以直接拿来做Inpainting任务，这也成为了现在diffusion model来解决inverse problem的主流范式，可见作者的科研前瞻性。Inpainting任务和原始退火朗之万采样方式的差异只是多了第九行，$m$代表mask，$y$代表被mask之后的图像。也就是我们强制已知的部分不变，让生成式模型直接去做这个Inpainting任务。

![Inpainting算法伪代码](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/BQcnMF.png)

## 总结

本文回答了为什么要在DDPM中加噪：本文的解答是为了更准确的估计score。

本文是DDPM的一种更加直观的解释。本文指出去噪的方式，可以达到生成的效果，也就是**估计噪声=估计score=估计目标分布的梯度**。通过这种梯度，逐步采样到目标分布。

本文提出的类似inpainting这种逆问题的求解，为之后的diffusion求解提供和很好的思路，也是现在大部分文章的主流。



## 参考

+ [blog | Generative Modeling by Estimating Gradients of the Data Distribution](https://yang-song.net/blog/2021/score/)
+ [Song Y, Ermon S. Generative modeling by estimating gradients of the data distribution[J]. Advances in neural information processing systems, 2019, 32.](https://arxiv.org/pdf/1907.05600.pdf)
+ [bilibili | 扩散模型 Diffusion Model 3-1 Score-based Model](https://www.bilibili.com/video/BV1VP411u71p?vd_source=225dba48b31d269151658db856705273)

