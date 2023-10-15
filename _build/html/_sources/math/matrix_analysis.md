# 矩阵与张量分析

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