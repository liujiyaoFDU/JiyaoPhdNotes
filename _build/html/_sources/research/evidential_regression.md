# Evidential Learning and Uncentainty

```{note}
In this chapter we present uncentainty estimation and a specific method called evidential learning to compute uncentainty.

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
## The classification of uncentainty

> Predictive Uncertainty Estimation via Prior Networks, 2018

<!-- ```{admonition} Abstract:
:class: important

Uncertainty可以通过**uncertainty in model parameters**，**irreducible data uncertainty**和**uncertainty due to distributional mismatch** between the test and training data distributions进行估计。本文提出了一个先验网络显示学习distributional uncertainty，通过参数化一个预测分布的先验分布实现。并且本文对uncentainty的分类比较清晰

``` -->
{cite:p}`malinin2018predictive` 将uncentainty分为三类。
+ model uncertainty / epistemic uncertainty（认知不确定性）：测量给定训练数据估计模型参数的不确定性，模型不确定性根据训练数据的增加而降低。
+ data uncertainty / aleatoric uncertainty（对抗不确定性）:  irreducible uncertainty which arises from the natural complexity of the data, such as class overlap, label noise, homoscedastic and heteroscedastic noise.
+ distributional uncertainty：mismatching between the training and test distributions


```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/2023-10-09%2023.00.53.png
---
height: 170px
name: blackhole
---
其中两种不确定性的定义及性质
```
## Evidential classification

### Categorical and multinomial Distribution

1. Categorical distribution

Categorical distribution是一个变量对应多种类别，每次只能归属为一类。使用向量对$x$进行编码。

Defination

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/8KR4lP.png
---
height: 400px
name: blackhole
---
Categorical distribution defination
```

2. Multinomial distribution

Multinomial 和 Categorical distribution的区别是前者是多个随机变量，也就是说在某一类中小球的数量。

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/aA0k1h.png
---
height: 400px
name: blackhole
---
```

### 背景知识：狄利克雷分布

1. Defination
```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/dlil.png
---
height: 400px
name: blackhole
---
狄利克雷分布定义
```

注意：根据定义，$k=3$时，$\theta$是在阴影三角形是，不能超出这个范围。$\alpha_k$为参数，必须满足$\alpha_k\geq 0$。

下图是狄利克雷参数的选择影响分布的结果：(红色表示分布更密集区域)
```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/UjwSlz.png
---
height: 400px
name: blackhole
---
狄利克雷参数的选择对分布的影响
```

下面这个例子说明了狄利克雷分布有什么作用：假设运动员有有三个指标，下面三幅图对应了三种运动的运动员通常的指标。对着$n$个运动员的三个指标分别进行打分，得到$n$个样本。我们假设每个运动员的指标$\theta = [\theta_1, ..., \theta_3]$是i.i.d的，且满足狄利克雷分布，那么便可以估计$\hat{\alpha}$，即估计得到样本的分布。


```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/Pn0Adj.png
---
height: 400px
name: blackhole
---
狄利克雷分布有什么作用
```

2. Conjugate Prior

*Dirichlet是multinomial的一个Conjugate Prior*：

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/GcGPOA.png
---
height: 400px
name: blackhole
---
```

```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/7IAQVs.png
---
height: 400px
name: blackhole
---
```

### $\Gamma$ Distribution

$\Gamma$分布：$X \sim \Gamma(\alpha, \gamma), \ \alpha,\lambda>0$, p.d.f 定义为：

$$f(x) = \frac{\lambda e^{-\lambda x}(\lambda x)^{\alpha-1}}{\Gamma(x)} = \frac{e^{-\lambda x}\lambda^\alpha x^{\alpha-1}}{\Gamma(x)}, x \geq0$$

其中，Gamma函数定义为

$$\Gamma(\alpha) = \int_{0}^{\infty}e^{-y}y^{\alpha-1}  \,dy $$

### inverse $\Gamma$ Distribution

逆Gamma分布是Gamma分布的变量的倒数分布

# Evidential Regression


```{figure} https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/N0QLOZ.png
---
height: 150px
name: blackhole
---
Depiction of a black hole as reconstructed from data recorded by the Event Horizon Telescope.
```