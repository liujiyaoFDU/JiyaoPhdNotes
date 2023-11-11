# Image misalignment problem

[toc]

> Jiyao Liu, 2023-11-01

## Burst image 

- **Towards Real-World Burst Image Super-Resolution: Benchmark and Method**. P Wei et.al. **ICCV**, **2023**.
    
    + 目标： investigate **non-trivial pixel-wise displacements** among Burst images under real-world image degradation.
    
    + contribution: a.针对uniform misalignment(整幅图像一致的偏移/抖动)，使用homography alignment from a structural geometry aspect首先对burst images进行对齐；b.The Federated Affinity Fusion (FAF) module is introduced to aggregate inter- and intra-frame information through affinity difference maps. (absorb  distinct information among frames)
    
      ![2sr18t](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/2sr18t.png)
    
    + > 注意：1.本文统计了burst images像素偏移在5px内；2. On BurstSR, the predicted SR images have to be warped by taking GT HRs as a reference before computing metrics.3. 对比了几种alignment的策略：
      >
      > ![o0jFHz](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/o0jFHz.png)
    
    + 可学习的点：1.FAF fusion：利用base image和others的相似度（higher similarity or affinity indicates more important pixels for fusion）矩阵及其difference maps（ alleviate the adverse effects derived from the misalignment due to large motions.）
    
      ![eqTIr0](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/eqTIr0.png)
    
      融合的结果等于相似的重要像素+模态特有的信息！
    
      ![g0i6GZ](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/g0i6GZ.png)
    
- **A Differentiable Two-stage Alignment Scheme for Burst Image Reconstruction with Large Shift.**. Shi Guo et.al. **CVPR**, **2022**
  
    + 目标：joint denoising and demosaicking (JDD) for burst images. 
    
    + contribution: coarse to fine alignment. a. aligned in the patch level (图像域邻域匹配(block matching)（降采样防止噪声干扰/大感受野）); b. implicit pixel-wise alignment via deformable conv in the full-resolution feature domain (特征alignment（局部感受野）
    
      ![XEDG8M](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/XEDG8M.png)
      
      ![BFseFN](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/BFseFN.png)

## Image translation

- **Breaking the Dilemma of Medical Image-to-image Translation.**. Lingke Kong et.al. **NeurIPS**, **2021**.
    + 目标：解决单模态配准误差
    
    + 缺陷：假设配准误差只来自仿射变换
    
      ![fJRfO9](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/fJRfO9.png)

## Other problems

- **Multi-Modal Tumor Segmentation With Deformable Aggregation and Uncertain Region Inpainting.**. Yue Zhang et.al. **IEEE Trans. Medical Imaging**, **2023**. 周少华组
    + 目标：多模态分割中：a.misalignment问题；b.边缘分割不确定性高
    + contribution: a. deformable feature fusion; b.uncertainty region inpainting module to refine uncertain pixels using neighboring discriminative features
    
    
    
    ![WAAxtt](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/WAAxtt.png)
    
    ![MtMxLX](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/MtMxLX.png)
    
    + 可学习的点：a. Uncertain Region Inpainting Module：边缘分割的不确定度大，因此利用不确定度大的周围的像素进行加权，减少分割的边缘不确定性。（但是本文的不确定度估计太过暴力，只是输出概率越接近0.5，代表uncertainty越高）
    + ![P5rfeR](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/P5rfeR.png)


- **Multimodal MRI Reconstruction Assisted With Spatial Alignment Network.**. Kai Xuan et.al. **IEEE Trans. Medical Imaging**, **2022**.， 沈定刚组
    + 目标：跨模态重建（source和target为单模态）中：misalignment问题；
    
    + contribution: a. joint train the spatial alignment network and reconstruction network; b. modality-synthesis-based registration loss.
    
    + 可学习的点：

      ![gCHj8d](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/gCHj8d.png)
    
    + ![O6DrUU](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/O6DrUU.png)
    
- **CIPPSRNet: A Camera Internal Parameters Perception Network Based Contrastive Learning for Thermal Image Super-Resolution.**. Kai Wang et.al. **CVPR Workshops**, **2022**.

    
    - 对比学习解决misalignment（旋转/平移misalignment）
    
    ![22lsSP](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/22lsSP.png)

