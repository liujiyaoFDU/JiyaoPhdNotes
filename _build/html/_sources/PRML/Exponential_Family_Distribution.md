# 指数族分布

![wuQ8zW](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/wuQ8zW.png)

```{note}
本节主要讲了指数族分布(Exponential Family Distribution)及其应用。

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

## 背景及应用

```{note} Defination
指数族分布(Exponential Family Distribution)是一个分布族，满足以下形式的分布都属于指数族分布：

$$P(x|\eta) = h(x)\exp(\eta^T\phi(x)-A(\eta))\\$$

其中，$\eta$为$P$维参数向量，$x\in \mathbb R^p$，$\phi(x)$为充分统计量，$A(\eta)$为log partition function。
```

> Log partition function（对数配分函数）可以理解为一个归一化常数，通常是一个积分，保证整个式子是一个概率密度函数。
>
> <img src="https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/Q9KNM6.png" style="zoom:67%;" />
>
> 

如下分布都属于指数族分布：

+ Gauss 分布
+ Bernoulli 分布 - 类别分布
+ 二项分布 - 多项式分布
+ 泊松分布
+ Beta 分布
+ Dirichlet 分布
+ Gamma 分布

指数族分布中有三个性质和三个应用，如下：

三个性质：
+ 充分统计量
+ 共轭
+ 最大熵（无信息先验）
三个应用：
+ 广义线性模型
+ 概率图模型
+ 变分推断

---
下面分别解释上述概念

**充分统计量**$\phi(x)$

统计量是指对样本的加工，即一个对于样本的函数，如样本均值、样本方差。充分统计量是指有这个统计量就足够了，此统计量可以包含样本的完整特征

> ![RCMH7n](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/RCMH7n.png)这一属性对在线学习（online learning）非常有用，只记录充分统计量，不用记录样本

**共轭**

假设有 $\underbrace{P(z|x)}_{后验}={\overbrace{P(x|z)}^{先验}\overbrace{P(z)}^{似然}\over \int _z P(x|z)P(z)dz}$ ，在求解时，分母积分过于复杂。若要求解$E_{P(z|x)}[f(z)]$，一般需要近似推断的方法，如变分和采样（MCMC）。共轭是另一种求后验的方法：对似然来说（*先验和具体的似然是相关的*），若能找到一个先验，与其共轭，那么后验与先验将有相同的分布形式。

> 例如：假设似然符合二项分布，令先验为Beta分布，那么后验也是Beta分布。但是先验和后验的参数不同，不过我们便只需要求解后验的参数即可

**最大熵（无信息先验）**

+ 最大熵原理：当给定一些已知限制条件的情况下，对于未知部分，我们假设它们等可能发生，但我们无法定量分析。我们期望选择一个概率分布，以便使得分布的信息熵最大，以便不对已知约束条件引入任何不必要的主观偏见，同时最大程度地保持不确定性。
+ 给定参数先验有如下几种方法
    - 共轭先验：计算方便 
    - 利用最大熵思想：从最大熵的角度给予先验的参数（无信息先验）
    - Jeffreys' prior：用于定义先验概率分布，以在没有先验信息的情况下估计参数。主要特点是：不依赖参数化、不具有主观信息、重参数化不变性

**广义线性模型**

指数族分布在此模型中占据重要地位。广义线性模型主要是为了解决回归和分类问题，在线性模型的基础上进行扩展，有如下：

1. 线性组合$w^Tx$
2. link function (激活函数的反函数)
3. 指数族分布：$y|x\sim$满足指数族分布 

> 例如：
> + 线性回归中我们用最小二乘，实际上和噪声满足高斯分布等价，则目标$y\sim \mathcal N(\mu,\Sigma)$
> + 分类任务中$y|x\sim Bernoulli$
> + 特殊模型：泊松回归 $y|x \sim Possion$

**概率图模型**

无向图：RBM（限制玻尔兹曼机），满足指数族模型。

**变分推断**：
指数族分布占据重要地位

若其中的分布满足指数族分布，则会使变分推断大大简化。

## 高斯分布的指数族形式

上一节给出了指数族分布的统一表达形式：



目标是将参数$\theta$转化成$\eta$的形式













## 总结

指数族分布是一类分布，包括高斯分布、伯努利分布等，可以统一写为如下形式：

$$P(x|\eta) = h(x)\exp(\eta^T\phi(x)-A(\eta))\\$$

第二节用高斯分布进行了验证

后续又找出了对数配分函数与充分统计量的关系

对经验分布利用最大熵原理导出的分布也是指数族分布



































