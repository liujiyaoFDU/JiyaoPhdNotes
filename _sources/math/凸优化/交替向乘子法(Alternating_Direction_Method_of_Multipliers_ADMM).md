#! https://zhuanlan.zhihu.com/p/632433943
# 交替方向乘子法（Alternating Direction Method of Multipliers, ADMM）
[toc]

## 背景介绍

### 复杂约束优化问题

交替方向乘子法（ADMM）通常用于解决存在两个优化变量的**等式约束优化类问题**，其一般形式为：
$$
\begin{equation}
\begin{aligned}
\min\limits_{x,z} f(x)+g(z)\\
s.t. Ax+Bz=c
\end{aligned}
\tag{1}
\end{equation}
$$
其中，$x\in \mathbb{R}^n$为优化变量，等式约束中$A \in \mathbb{R} ^{p\times n}, B \in \mathbb{R} ^{p\times m}, c \in \mathbb{R} ^{p}$, 目标函数中 $f, g$ 都是凸函数。

> 说明：我们称$Ax+Bz=c$这种约束为复杂约束。其定义为，假设现在想要将一个复杂规划分解为简单规划（对用上面这个例子即可以对$x,z$分别求解），但由于复杂约束/变量的存在，导致这个规划无法分解。

### 拉格朗日乘子法

$$
\begin{equation}
\begin{aligned}
\max\limits_{u}\min\limits_{x,z} L_\rho(x,z,u) = f(x)+g(z)+u^T(Ax+Bz-c)
\end{aligned}
\tag{2}
\end{equation}
$$

### 增广拉格朗日函数

ADMM算法的核心是原始对偶算法的增广拉格朗日法（ALM）。拉格朗日函数是解决了多个约束条件下的优化问题，通过把复杂约束放到目标函数中去，这种方法可以求解一个有n个变量与k个约束条件的优化问题。原始对偶方法中的增广拉格朗日法（Augmented Lagrangian）是加了惩罚项的拉格朗日法，目的是加强目标函数的凸性，使得算法收敛的速度更快。
$$
\begin{equation}
\begin{aligned}
\max\limits_{u}\min\limits_{x,z} L_\rho(x,z,u) = f(x)+g(z)+u^T(Ax+Bz-c)+\frac\rho2 \Vert Ax+Bz-c \Vert_2^2
\end{aligned}
\tag{3}
\end{equation}
$$
增广拉格朗日函数就是在关于原问题的拉格朗日函数之后增加了一个和约束条件有关的惩罚项，惩罚项参数 $\rho > 0$.惩罚项参数影响迭代效率。


## ADMM定义

>  交替向乘子法（Alternating Direction Method of Multipliers, ADMM）是一种求解具有可分离的凸优化问题的重要方法，由于处理速度快，收敛性能好，ADMM算法在统计学习、机器学习等领域有着广泛应用。

对于上面的增广拉格朗日函数，我们采用每一步只更新一个变量而固定另外两个变量，如此交替重复更新，即：

![MGk2W3](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/MGk2W3.png)

不断重复以上三步直到收敛。

> - 使用和增广拉格朗日类似的对偶上升方法，固定其中两个变量，去更新第三个变量的值。
> - ADMM 算法提供了一个将多优化变量问题转化为单优化变量问题的转化方式（即，交替方向），并未涉及具体的下降方法，其中关于 x xx 和 z zz 的更新过程需要结合具体的下降类算法，如梯度下降算法等。
> - 

## 个人理解

ADMM方法就是通过 decomposition-coordination 的过程，通过连续协调规模小的局部的子问题的解来找到一个大规模的全局问题的解。



## 参考资料

1. [CSDN：ADMM算法学习](https://blog.csdn.net/weixin_44655342/article/details/121899501)
2. [知乎：优化算法-1|拉格朗日函数和对偶性](https://zhuanlan.zhihu.com/p/418115626)



