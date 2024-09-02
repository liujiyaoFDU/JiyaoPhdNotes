#! https://zhuanlan.zhihu.com/p/685079125


# 【Talk】CVPRW 202206 | Towards robust and trustworthy AI for medical imaging - Ender Konukoglu

> Prof. Ender Konukoglu talk at 9th CVPR Medical Computer Vision Workshop
>
> Video: https://www.youtube.com/watch?v=-BsxP_zHxtA

## 1. Trustworthy AI总结

医学成像中的可信AI可以分为下面几种场景：

1. **鲁棒性：**可信AI应当是一个有适应能力的系统，能够保持输入特征的一致性，换句话说，也就是对输入或者分布的扰动和变化不敏感，仍能够正常工作，具有很强的泛化性；
2. **可解释性：**可信AI视图告诉用户或者与用户交互模型是如何预测的，例如可视化或者可解释的模型架构；
3. **自知之明：**模型应当知道自己的局限性，而不是做出奇怪的事情或者过度自信的错误决策；
4. **敏感性：**创建真正有用的模型。
5. 

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/Wb41rM.png)

## 2.如何构建robust AI？

下面是同一个模型用在美国和英国大量人口上实验的ROC曲线，可以看出他们有巨大的差别。

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/hVir3Y.png)

在一篇NC中也总结了不同的dataframe shift的成因：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/CClWH1.png)

我们在这里主要关注采集的shift（例如：采集设备、分辨率、对比度、模态、协议），理论上是可以被解决的。下面这个表总结了现在处理采集shift常用的方法分类：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/QuQEut.png)

对于医学图像而言，域泛化和TTA是比较有意义的，我可以通过一个已经训练的模型，而在测试时候解决泛化问题。Domain adaptation会提高泛化性，但是在我们的实验中还是存在很大的gap，实验结果如下：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/EWAhGg.png)



因此，我们更希望使用TTA，针对每个测试图像调整你的模型。下面这个工作（Test-time adaptable neural networks for robust medical image segmentation,2021,MeDIA）用到了一个比较简单但是有用的方法：增加一个Normalization Module $g(x;\phi^*)$，在测试阶段可以将图像标准化为训练数据的图像风格的强度（这里我觉得就是相当于在测试时训练一个图像风格迁移网络，把图像标准化到和训练数据同样的数据分布）：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/sTXHR7.png)

上面的这个思路来源于一个old trick：使用shape prior refine训练结果：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/izQI2E.png)

实验结果如下：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/BJmJYr.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240303200638161.png)

## 3. Can our models work at realistic operating points?

 为了避免高额的扫描动态造影剂的代价，下面的工作提出了一种方法，确定如果不需要DCE-MRI就能达到很好的诊断效果，那该病人就不用打造影剂。

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/ionl8a.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/b3wOfn.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/cqfkGV.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240303221555031.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240303221659214.png)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240303221737979.png)

*总结：这个工作还是很有意思的，如果能做到非常可信并且可解释性，在临床中很有价值。*