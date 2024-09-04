

# Krylov Subspace Regularization for Inverse Problems

## 一些参考资料

[知乎：Krylov算法入门](https://zhuanlan.zhihu.com/p/644117637)

[Youtube talk: Krylov Subspace Regularization for Inverse Problems ](https://www.youtube.com/watch?v=UF6NTQR4O9E)

## talk

![image-20240326163650436](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326163650436.png)

![image-20240326202741355](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326202741355.png)

![符号的定义](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326202845053.png)

注意：如果我们忽略noise，朴素的求解$\hat x = A^{-1}b = x + A^{-1}\eta$。这样会导致求解的x实际上是真实值+噪声（由于A严重欠定，因此噪声会被放大）。因此，朴素求解并不是一个好的办法。我们接下来会给出一些不错的求解办法：

![image-20240326204848622](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326204848622.png)

+ TSVD方法：对较小奇异值的$\frac{\mathbf u_i\mathbf b}{\sigma_i}\mathbf v_i$进行截断，只保留较大奇异值的求和，过滤掉噪声。但是，选择什么阈值进行truncate？

+ Tikhonov：有一个能量的正则化，约束求解的x能量在一定范围内，而不是放大太多。$\alpha$是一个可调参数。可以写成SVD的形式，乘以一个过滤因子。
+ General SVD Filter：将过滤因子写成通用的function。保留大的$\sigma_i$(奇异值)和奇异分量，剔除小的$\sigma_i$。

图中列了两种常用且有效的指导$\alpha$选择的方法

+ discrepancy principle (差异原则)：需要关于噪声水平的信息；

+ generalized cross validation：利用数据统计信息，不需要噪声水平信息

	![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326205430259.png)

---

当A比较大（100wx100w），很难求得奇异值分解（即使是A稀疏的情况下）。

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326210605637.png)

上图中的一些解释：

+ 截断迭代模拟截断SVD，如krylov subspace方法：共轭梯度法(CG)，...。这类方法需要early stopping，并且不要让残差减小到0。可以得到一个类似截断SVD方法的解。

+ $\mathcal R$的选择：Tikhonov，TV，稀疏等
+ $\alpha$的选择：（在Truncated iterations和variational problem方法中都存在）。如何选择$\alpha$？Krylov combined with SVD can help。
+ 很难通过一个flowchart来适应于所有问题。

---

==这里有点没懂==

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326210833978.png)

![image-20240326211054624](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326211054624.png)

---

下面是一个迭代求解的例子：

（已知噪声水平或者$\alpha$的初始化）

==图和分析==

**如果噪声和$\alpha$未知，如何做？Hybrid krylov solver**

基本的krylov solver：通常计算一组basis vector，接着将问题投用到最小二乘问题，然后再投影回来得到$x$的求解，接着不断迭代。

![基本的krylov solver](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326212253986.png)

Hybrid：在tiny Projected problem上加入正则化，那么这个tiny问题就可以使用SVD求解。

![image-20240326212703983](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326212703983.png)

![image-20240326212539585](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326212539585.png)

![image-20240326213337145](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326213337145.png)

下面是hybrid 方法的matlab求解的结果，可以看出，是很接近tikhonov的

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326213503111.png)

注意：在下面Hybrid方法中，对于tiny problem，先只迭代保真项3-4轮（保证$T_k$是学到信息后），然后再加入正则（在广义交叉验证中使用SVD来确定$\alpha$）。

![image-20240326213713621](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326213713621.png)

下图中的左图为$\alpha$的变化：

![image-20240326214051822](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326214051822.png)

> 上述做法不一定适合所有问题，但是使用于大多数问题

![image-20240326214229613](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326214229613.png)

----

 L1正则的求解：

![image-20240326214958772](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326214958772.png)

![image-20240326215030048](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326215030048.png)

---

这个方法motivated by：寻找SDE的低阶解

![image-20240326215430583](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326215430583.png)

---

+ vec为核范数

![image-20240326215658042](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326215658042.png)

![image-20240326215926418](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326215926418.png)

![image-20240326220311436](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326220311436.png)

![image-20240326220458074](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240326220458074.png)



## reference

https://www.youtube.com/watch?v=UF6NTQR4O9E