# MT&CEST&NOE 磁化转移定量成像

```{note}
2023-10-18


```


## MT  磁化转移

### 基础

+ 自由水：CSF，水肿（炎症反应），细胞质中多数也是自由水，细胞外间隙也有自由水。
+ 结合水：它与机体内蛋白质、氨基酸、维生素等生物大分子结合,参与机体生命物质生化活动和生理活动。

+ 激发：低能级状态的氢质子跃迁到高能级；饱和：施加脉冲能量使得低能级和高能级达到平衡状态。

+ Two-pool system（双池系统）：小池子（自由水），大池子（结合水）。之后的内容基本都基于双池系统。

+ 1.5 交叉弛豫：两个氢质子（spin自旋），在距离非常近的情况下，这两个氢质子会相互作用，造成能量传递。（偶极耦合）。

+ 1.6 Nuclear Overhauser E：等同于交叉弛豫

+ 1.7 描述上面NOE的couple的过程：有两个常数自弛豫和交叉弛豫

  ![image-20231025120039520](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120039520.png)

+ 1.8 Chemical Exchange：大分子上有很多结合的氢质子，当有一个其他水分子和这个大分子有接触的时候，两个氢质子会发生化学交换（直接交换位置）。原因：水上的氢质子非常活泼。化学交换的衡量核心是交换速率，CEST只能对中速和慢速交换进行定量。

  ![image-20231025120113910](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120113910.png)

![image-20231025120127271](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120127271.png)

+ 1.9 描述化学交换的过程公式是在Block equation的基础上进行modify。

![image-20231025120140794](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120140794.png)

+ 1.10 饱和转移

  ![image-20231025120155121](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120155121.png)

### MT：磁化转移理论

+ semi-solid成分很难成像，一般是invisible的。因为T2时间非常短。例如软骨、细胞膜、髓鞘等

![image-20231025120212913](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120212913.png)

![image-20231025120225354](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120225354.png)

**由于大分子很难成像，因此，可以使用饱和激发/偏共振脉冲大分子，会使得自由水分子信号变化（发生交叉弛豫），通过检测水分子的信号变化，就能检测出大分子的信号变化。**

+ **这种序列如何设计：**使用较宽/无选择性的脉冲

![image-20231025120238701](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120238701.png)

+ **定量分析**：计算MTR

![image-20231025120253803](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120253803.png)

缺陷：没有考虑如何交换，是一种半定量技术

+ 最终的$MTR = (M_{sat}-M_0)/M_0$

![image-20231025120308271](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120308271.png)

可以看到白质信号更好，自由水信号变暗。为什么会这样：

![image-20231025120321034](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120321034.png)

+ 定量MT可以用2、3、4-pool model进行建模

## CEST

CEST是一种特殊的MT。CEST有两个池：free water和中等分子（代谢物、蛋白等可自由运动的分子，比大分子小的多比水分子大）。则两个池的信号如下面所示，中分子的信号相对于大分子更局限，如右图：

![image-20231025120334704](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120334704.png)

想要观察中分子可以先用选择性的频率对中等大小的分子（OH或者NH或者NH2）进行饱和，然后传递到水分子，就会发现化学传递。使得周围的水分子也饱和，从而造成水分子信号下降。

![image-20231025120346622](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120346622.png)

小池子是中分子，大池子是自由水。举个具体的例子：可以看做饱和脉冲抽到了A-pool中的水，就会是的B-pool中的水过来（这里的水相当于信号）。

![image-20231025120400536](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120400536.png)

![image-20231025120413703](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120413703.png)

**和MT相比CEST的优势：**有选择性，我们可以知道到底是哪一类的中等分子的贡献。

+ Z谱：每个点的ppm会得到一个对应的contrast

+ CEST可以观测到哪些呢？amide是最常用的

  ![image-20231025120429925](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20231025120429925.png)

**矫正：**静磁场不均匀性