# 矩阵与张量分析作业

## 特征值性质

```{admonition} Definition: *特征值性质*
:class: important

$A \in \mathbb C^{m\times n}$，$B\in \mathbb C^{m\times n}$矩阵相乘，$AB$和$BA$具有完全相同的非零特征值
```

```{admonition} 证明：

设$(AB)x_1 = \lambda_1 x_1$，即$\lambda_1$为$AB$的特征值，$x_1$为该特征值的特征向量，则有

$B(AB)x_1 = BA(Bx_1)$,  $B(AB)x_1 = B(\lambda_1x_1) = \lambda_1(Bx_1)$

则$BA(Bx_1) = \lambda_1(Bx_1)$，即：$\lambda_1$为$BA$的特征值，$Bx_1$为$BA$的特征向量。
```

## 对角元都是非负的对称对角占优矩阵是半正定矩阵

```{admonition} Definition:

一个对角元都是非负的对称对角占优矩阵是半正定矩阵

```

```{admonition} 证明：

设A是一个n阶对称对角占优矩阵,即对于所有的$i≠j$,有$a_{ii} ≥ a_{ij}$。要证明A是半正定矩阵,我们需要证明对任意非零向量$x$,$x^T Ax ≥ 0$成立。

利用对角占优矩阵的定义,我们有:

$x^T Ax = \sum_{i=1}^n \sum_{j=1}^n a_{ij}x_i x_j$

$= \sum_{i=1}^n a_{ii}x_i^2 + \sum_{i≠j} a_{ij}x_i x_j$

$≥ \sum_{i=1}^n a_{ii}x_i^2$  (因为$a_{ii} ≥ a_{ij}, i≠j)$

$≥ 0$ (因为$a_{ii} ≥ 0)$

因此,对任意非零向量x,$x^T Ax ≥ 0$都成立。

综上所述,对角占优矩阵A是半正定矩阵。

```