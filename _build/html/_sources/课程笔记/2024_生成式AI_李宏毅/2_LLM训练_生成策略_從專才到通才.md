# LLM训练

LLM的训练氛围三个步骤：

1.自我学习，积累实力  - Pre-training，

2.名师指点，发挥潜力  - instruction fine-tuning

3.参与实战，打磨技巧。注意：所有的阶段都是在学习文字接龙，只是训练资料不同。

##  第6講：大型語言模型修練史 — 第一階段: 自我學習，累積實力

> Slides: https://drive.google.com/file/d/1myvHjoeFOpIl1uGU9H1t4OpDErkhF0zO/view
>
> Video: https://www.youtube.com/watch?v=cCpErV7To2o

1. 大语言模型的本质是文字接龙，即：$下一个token = f(未完成的句子)$。

2. 训练大模型需要的文本量：模型会学习到**语言知识**（语法和语义正确）和**世界知识**（物理世界）。然而学习到世界知识很难，需要真正非常大量的资料去学习；而语言知识较为容易。如下图所示，当文本量为1B的时候，世界知识的学习是远远差于语言知识的。

	![image-20240911115705316](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911115705316.png)

3. 大语言模型如何学习：自监督训练。

> LLM的模型训练需要首先进行数据清洗，然后用这些数据进行自监督训练，如下图：
>
> ![image-20240911120106562](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911120106562.png)
>
> 

4. 模型训练多大才可以？下面是GPT系列的参数量。GPT3的报告中指出即使GPT3的参数量，其效果也是不够好的（few shot（incontext learning）的准确率只有60%不到）

	![MBr95V](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/MBr95V.png)

![image-20240911121059464](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911121059464.png)

5. **训练再大的模型也没用**：GPT3用起来还是比较难用，并且很受prompt的影响，而且还是会乱说话。Google训练了更大的PaLM，发现即使模型变大，乱说话的现象还是存在：

	![image-20240911121729018](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911121729018.png)

6. 为什么模型变大了，但是效果还不好？A：语言模型利用训练资料学了很多东西，却不知道使用的方法，因此，便需要第二阶段，名师指点。

## 第7講：大型語言模型修練史 — 第二階段: 名師指點，發揮潛力 (兼談對 ChatGPT 做逆向工程與 LLaMA 時代的開始)

> Slides: https://drive.google.com/file/d/1SOXBQhsC_L6aHXcLx2rltaDdcO6N2FmJ/view
>
> Video: https://www.youtube.com/watch?v=Q9cNkUPXUB8

### **Instruction Fine-tuning**（指令微调）

我们需要对自监督训练好的LLM进行，即用标注的问答文本进行有监督的学习。

> 为什么需要自监督预训练：即使只使用上百万Billion对话进行训练，模型一般来说就会比较笨。因此，预训练是LLM模型成功的关键因素。

![image-20240911132513307](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911132513307.png)

### Parameter-Efficient fine-tuning

为了防止模型调整的过程中过拟合，即参数和初始参数比较接近，使用PEFT方法进行微调。下图中展示了不同的added parameter模块（虚线所示）。https://adapterhub.ml/中有很多即插即用的adapter。

![image-20240911132951833](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911132951833.png)

**预训练-微调的优势**：模型有更好的举一反三的能力。例如：

![image-20240911150101324](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150101324.png)

模型在无预训练、中文、104种语言预训练的基础上进行中文/英文/中英文指令问答微调，结果发现英文微调后在中文的阅读问答理解上也有很高的性能提升。

![image-20240911150223296](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150223296.png)

### 打造finetuning的LLM的路线

#### 路线1：打造多个专才

![image-20240911150552029](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150552029.png)

![image-20240911150627481](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150627481.png)

#### 路线2：打造一个通才

![image-20240911150702330](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150702330.png)

![image-20240911150723307](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911150723307.png)

**Multi-task instruction fine-tuning**：即利用多种任务对Pre-trianed model进行fine-tuning，经典的论文例如：

1.**Google：FLAN**(https://arxiv.org/abs/2109.01652), **T0**(https://arxiv.org/abs/2110.08207)。下图分别为GPT-3，GPT-3（incontext learing）以及FLAN（instruction fine-tuning）在新的任务上的效果：

![image-20240911151837616](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911151837616.png)

实验结果表明随着模型变大和Fine-tuning的任务量的增加，模型的性能会越来越好 (https://arxiv.org/abs/2210.11416，https://research.google/blog/better-language-models-without-massive-compute/)：

![image-20240911152702228](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911152702228.png)

![image-20240911152903573](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911152903573.png)

**2. GPT系列：Instruct GPT**

>  论文：https://arxiv.org/abs/2203.02155

![image-20240911153836097](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911153836097.png)

+ SFT (instruction finetuning)效果优于GPT prompted (after incontext learning), 以及google的FLAN和T0.

+ **Instruct GPT使用得到fine-tuning资料来自于GPT-3使用者的真实问题**，并由人对这些真是问题进行回答。而FLAN的微调资料来自于“前提-假设”的问答模式收集到的一些模板判定对话。

	![image-20240911154237372](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911154237372.png)

### Instruction Fine-tuning的训练样本不必很多

Instruct GPT只用了11295例进行微调。LAMMA2中也有对微调的数据量说明的一个情况。

![image-20240911154458250](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911154458250.png)

### **ChatGPT逆向工程**

> 如果我们自己想做Instruction Fine-tuning，但是缺少高品质的fine-tuning资料，这时可以对chatgpt做**逆向工程**。（小团队好像都是这么做的）

![image-20240911154820598](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911154820598.png)

### LLaMA的横空出世！

直到meta开源LLaMA，才开始有开源的预训练模型可以使用：

+ LLaMA 1： https://arxiv.org/abs/2302.13971
+ LLaMA 2： https://arxiv.org/abs/2307.09288

现在人人都可以fine-tuning LLM （https://arxiv.org/abs/2303.18223）：

![image-20240911155553485](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911155553485.png)

![image-20240911155642854](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240911155642854.png)

## 第8講：大型語言模型修練史 — 第三階段: 參與實戰，打磨技巧 (Reinforcement Learning from Human Feedback, RLHF)

### RLHF

> 本质：根据人类反馈，微调模型。对人觉得比较好的答案，提高其出现的概率。

+ RLHF使得模型整体考虑答案的好坏（只问结果，不问过程），SFT主要考虑局部下一个生成词的正确与否（只问过程不问结果）。

> 一个有意思的例子：
>
> 在天龙八部珍珑棋局中，有一个人叫做苏星河，他摆了一个珍珑棋局来供大家破解。但是古往今来的各大高手都没有办法破解这个棋局。但是有一个少林寺的和尚叫做虚竹，他在棋盘上下了一个字，就把自己的所有棋子堵死了。其他人都非常疑惑，怎么可以这么下？但是令大家没想到的是，正是这个诡异的棋子 ，虚竹便破解了这个棋局，最终赢得了本局。因为这个珍珑棋局奇妙的地方就是需要在某个地方堵死自己的棋，才能在最终取得胜利。过去的人都在纠结在每一步都要下好，反而最终没法下好，而把某个地方堵死，最后便可海阔天空。

### 语言模型 v.s.下围棋 

![UWNcSX](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/UWNcSX.png)

+ AlphaGo的学习分为两阶段：1.跟着棋谱学习 == pretrain+finetuning；2.RL（如下图）

![image-20240914160355076](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914160355076.png)

+ AlphaGo和语言模型的RL的差别是，AlphaGo是不需要人类参与的，他的反馈来自于其他版本的AlphaGo

### 如何有效的利用人类的回馈？

> 因为人类的回馈是有限的，所以如何有效利用人类的回馈是很重要的。答案是：用人类的回馈训练一个回馈模型，如判断语言模型回答的好坏，用来模拟人类的喜好。

回馈模型有两种形式：多次回答打分以及一次回答打分

> ![image-20240914163127328](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914163127328.png)
>
> ![image-20240914163211148](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914163211148.png)

下图橙色代表了增加PPO（RLHF）的GPT的性能：

![image-20240914163323640](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914163323640.png)

### 过度和虚拟的reward model学习是有害的

![https://arxiv.org/abs/2009.01325](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914163541905.png)

下面是一些可能根据reward model学过头的现象：

![image-20240914163842540](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914163842540.png)

### 一些改进方法 (无需虚拟老师，但是有待考证)：DPO、KTO

![image-20240914164402744](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914164402744.png)

### RLAIF

> 核心：用语言模型来评价语言模型提供reward，下面是一些参考链接：

+ https://arxiv.org/abs/2212.08073 

+ https://arxiv.org/abs/2304.03277 

+ https://arxiv.org/abs/2309.00267 

+ https://arxiv.org/abs/2401.10020

Self-rewarded model： 可以用同一个语言模型来作为评价模型，因为语言模型是具有反省的能力的。

### RLHF存在的一些难题：Safety（是否安全）、Helpfulness（是否对人类有用）

+ LlaMA2中就提供了分别考虑两项更好的模型。

![image-20240914164346476](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914164346476.png)

+ 人類自己都無法正確判斷好壞的狀況？
 
## 总结

大模型训练：Foundation model + 对齐人类偏好

![image-20240914164801867](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240914164801867.png)
