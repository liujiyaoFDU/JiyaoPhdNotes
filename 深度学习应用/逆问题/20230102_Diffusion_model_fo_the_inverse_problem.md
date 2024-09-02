#! https://zhuanlan.zhihu.com/p/675716426
# 【Method】inverse problem | 基于diffusion model的图像逆问题求解

## 参考资料

> CVPR 2023教程：[Denoising Diffusion Models: A Generative Learning Big Bang: part 3](https://www.youtube.com/watch?v=1d4r19GEVos)
>
> Slide：https://drive.google.com/file/d/18KfSKzS7dOaF15VJx8q_MXCG2yhmVd12/view

## 任务定义

假设我们目前有训练好的diffusion作为高质量图像的**先验**，我们的目标是通过**似然**得到其**后验**（重建后的图像）。使用同一个训练好的diffusion model可以去做不同的逆问题（例如下图的超分和补全），也就是**只需要不同的似然计算方法**就可以实现。

![Diffusion求解逆问题的示意图](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240102173141961.png)

![image-20240102173153750](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240102173153750.png)

## diffusion model求解逆问题的两种范式

1. 第一种是基于**替换**的方法，先让扩散模型生成完整结果，然后将已经观测到的部分替换掉模型输出的部分。
2. 第二种是基于**重构**的方法，让扩散模型输出结果，然后约束输出的结果与观测部分比较像，这点类似于**无需额外训练**近似无分类器引导的生成模型，过程如下图

![基于替换的方法和基于重构的方法](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240102173500080.png)

下面是两个例子：

1. 基于替换的方法：Song et al., "[Score-Based Generative Modeling through Stochastic Differential Equations](https://arxiv.org/abs/2011.13456)", ICLR 2021

![Song et al., "Score-Based Generative Modeling through Stochastic Differential Equations", ICLR 2021](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240102173733089.png)

即将每一个step重建后的结果和真实图像进行线性组合。

2. 基于重构的方法：Chung et al., "Diffusion Posterior Sampling for General Noisy Inverse Problems", ICLR 2023

![Chung et al., "Diffusion Posterior Sampling for General Noisy Inverse Problems", ICLR 2023](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/024-01-021.22.png)

![Chung et al., "Diffusion Posterior Sampling for General Noisy Inverse Problems", ICLR 2023](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240102174348222.png)



### 两种求解方式论文整理

**Reconstruction-based methods**

+ ScoreSDE: simple linear problems, e.g., inpainting, colorization; later extended to MRI and CT.
+ ILVR: more linear problems, e.g., super-resolution.
+ SNIPS: slow solution for noisy linear problems.
+ CCDF: better initializations.
+ DDRM: fast solution for all noisy linear problems, and JPEG.

Reference

+ Choi et al., “ILVR: Conditioning Method for Denoising Diffusion Probabilistic Models”, ICCV 2021
+ Kawar et al., ”SNIPS: Solving Noisy Inverse Problems Stochastically”, NeurIPS 2021
+ Chung et al., “Come-Closer-Diffuse-Faster: Accelerating Conditional Diffusion Models for Inverse Problems through Stochastic Contraction”, CVPR 2022
+ Song et al., "Solving Inverse Problems in Medical Imaging with Score-Based Generative Models", ICLR 2022
+ Kawar et al., "Denoising Diffusion Restoration Models", NeurIPS 2022

**Replacement-based methods**

+ Video Diffusion/Pyramid DDPM: used for super-resolution.
+ Pseudoinverse guidance: linear and some non-differentiable problems, e.g., JPEG
+ MCG: combines replacement & reconstruction for linear problems.

**Others**

+ CSGM: Posterior sampling with Langevin Dynamics based on the diffusion score model.
+ RED-Diff: A Regularizing-by-Denoising (RED), variational inference approach.
+ Posterior sampling: use RealNVP to approximate posterior samples from diffusion models.

Reference

+ Ho et al., "Video Diffusion Models", NeurIPS 2022
	Chung et al., "Improving Diffusion Models for Inverse Problems using Manifold Constraints", NeurIPS 2022
+ Ryu and Ye, "Pyramidal Denoising Diffusion Probabilistic Models", arXiv 2022
+ Chung et al., "Diffusion Posterior Sampling for General Noisy Inverse Problems", arXiv 2022
+ Song et al., "Pseudoinverse-Guided Diffusion Models for Inverse Problems", ICLR 2023
+ Jalal et al., "Robust Compressed Sensing MRI with Deep Generative Priors", NeurIPS 2021
+ Mardani et al., "A Variational Perspective on Solving Inverse Problems with Diffusion Models", arXiv 2023
+ Feng et al., "Score-Based Diffusion Models as Principled Priors for Inverse Imaging", arXiv 2023

