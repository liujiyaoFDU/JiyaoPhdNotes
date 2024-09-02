# VALSE 20200415 |  机器学习 vs 压缩感知：核磁共振成像与重建

```{note}
> 报告主页：http://valser.org/article-359-1.html
>
> [20200415 机器学习 vs 压缩感知：核磁共振成像与重建](https://www.bilibili.com/video/BV19Q4y1P7VS?vd_source=225dba48b31d269151658db856705273)
>
> PPT：[Shanshan Wang slides](http://valser.org/webinar/slide/slides/20200415/%E7%8E%8B%E7%8F%8A%E7%8F%8A-valse30min%E6%BC%94%E8%AE%B2%E7%A8%BF%E7%BB%88%E7%89%88.pdf) | [Bihan Wen slides](http://valser.org/webinar/slide/slides/20200415/Valse2020_WBH_TL-CSMRI.pdf)
>
> 谷歌学术：
>
> + [王珊珊 Shanshan Wang](https://scholar.google.com.hk/citations?hl=zh-CN&user=8pnz5L4AAAAJ&view_op=list_works&sortby=pubdate) | siat
> + [文碧汉](https://scholar.google.com.hk/citations?hl=zh-CN&user=ypkClpwAAAAJ) | ntu

2023/10/16

<script type="text/javascript">
function lastModified() {
    var modiDate = new Date(document.lastModified);
    var showAs = modiDate.getDate() + "-" + (modiDate.getMonth() + 1) + "-" + modiDate.getFullYear();
    return showAs
}
document.writeln("<div style='font-size: 14px;'>");
document.write("Last updated on " + lastModified());
document.writeln("</div>");
</script>
```

## Deep learning for MR imaging and analysis - Shanshan Wang

可以参考前面链接中的ppt，这里不重点讲，我们主要关注文碧汉老师的talk。

## Machine Learning for CS MRI: From Model-Based Methods to Deep Learning - Bihan Wen

### 重建问题简介

计算机视觉准确来说是图像理解，成像/重建的本质是感知，从低质量观测恢复高质量图像。

![计算机视觉和图像重建对比](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230706014353119.png)

理想情况下k-space是连续的，但是由于信息的采集都是的数字信号，因此采集到的都是离散化的数据，因此可以说采集到的k-空间的全采样图像都是真实情况下的欠采样。

但是我们要讨论的情况是在全采样情况下的欠采样，也就是对全采样的图像我们只采样部分区域，如下图：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013756984.png)

但是直接求解这个逆过程是一个ill-posed 问题。在十年前，压缩感知作为一个比较好的方法在重建领域大放异彩。

### 压缩感知介绍

经典压缩感知公式如下

+ **有约束优化问题**（Transform domain sparsity）：利用MR成像的关系式$F_ux=y$，我们设计一个有约束的优化问题。目标在固定的Transform（变换）下的最优的sparsity，找到使得sparsity最优的$x$。

  ![image-20230707013832092](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013832092.png)

+ **无约束优化问题**：sparsity作为正则项![image-20230707013842646](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013842646.png)

> **稀疏性**
>
> ①为什么sparsity比较重要？
>
> image model的作用如下，即提供判断是否为我们想要的图像的依据。![image-20230707013847785](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013847785.png)
>
> ②信号的稀疏性定义
>
> 定义：一个dense的信号在transform domain是稀疏的
>
> 举例：一个一维信号是稀疏的$\Leftrightarrow$大多数相关系数为0
>
> ![image-20230707013858597](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013858597.png)
>
> 特点：自然信号大都是满足sparsity属性的，例如自然图像的离散余弦变换![image-20230707013903212](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013903212.png)
>
> 而噪声的离散余弦变换仍为噪声：
>
> ![image-20230707013908933](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013908933.png)

磁共振重建领域的压缩感知方法发展如下：

![image-20230707013915500](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013915500.png)

### Model-based method: Transform learning for better sparsity

#### 基本概念

在压缩感知中，我们给定了一个变换$\Psi$（如DCT、小波等等），求得这个变换下最稀疏的$x$。如下式：

![image-20230707013920584](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013920584.png)

但是我们希望去学习这个变换，使得稀疏化更好，如下图：

![image-20230707013924578](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013924578.png)

#### 1.Transform learning Method 1: Sparsifying Transform Learning(STL)

![image-20230707013929165](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013929165.png)

> 符号解释：
>
> $\mathbf{W}$: 要学习的变换矩阵；$\mathbf{b}_i$: patch对应的sparse code
>
> $\mathbf{P}_i$: 图像中的第$i$个patch；
>
> $\frac \lambda 2 \Vert \mathbf W\Vert^2_F - \lambda log(det \mathbf W)$: 保证了$\mathbf W,\mathbf  b$不会变成一个trivial的解：为0，保证让$\frac \lambda 2 \Vert \mathbf W\Vert^2_F$其尽量趋近于1

> 该方法的优缺点：
>
> ① 有闭式解

#### 2.Unitary Transform Learning (UT)

![image-20230707014024926](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014024926.png)

算法的具体实现过程：![image-20230707013956805](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707013956805.png)

#### 3. Learning a UNIon of Transforms (UNITE)

![image-20230707014030802](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014030802.png)

图像比较复杂的情况下，一个transformer是不够的。

#### 4.Flipping and Rotation Invariant Sparsifying Transform (FRIST)

![image-20230707014035838](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014035838.png)

#### 5. Sparsifying TRansfOrm Learning and Low-Rankness (STROLLR) 

有的时候不光使用sparsity，也可以构建Low-rank，将二者结合在一起约束。

![image-20230707014040635](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014040635.png)

#### 总结

![image-20230707014044897](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014044897.png)

#### 讨论

![image-20230707014049220](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014049220.png)

> 注：不同的model在不同的MRI中有不同的效果，需要选择更适合的方法，达到最好的效果。![image-20230707014053294](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014053294.png)

### Deep Learning

#### 一些主流模型

model的发展，即model灵活性的上升，模型具有更好的适应性：![image-20230707014057478](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014057478.png)

下面我们主要关注两种方向：①unroll network（主流），将迭代过程变为多个network，如下图：

![image-20230707014101790](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014101790.png)

![image-20230707014105759](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014105759.png)

②另一种想法就是设计多层的transform：

![image-20230707014109415](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014109415.png)

一些结果：

![image-20230707014112740](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014112740.png)

![image-20230707014117510](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014117510.png)

此外，自监督的方式也有一定的优势：我们希望模型不只是拘泥于数据集，而要一定程度上focus在图像本身，根据图像本身的一些全局和局部信息进行重建。

#### 深度学习模型和传统模型的对比

![image-20230707014121664](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20230707014121664.png)