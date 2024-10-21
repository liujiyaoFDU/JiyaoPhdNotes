# (入门) 提示工程与AI agent

> 本章节是课程的2-5讲，主要介绍了如何更好利用生成式AI的第一个角度，也就是”改变自己来强化模型“，并介绍了一些在不訓練模型的情況下強化語言模型的方法，包括：
> + 神奇咒语
> + 提供额外的信息
> + 拆解任务 
> + 使用工具
> + 語言模型彼此合作


## 提示工程 & AI 代理人

### 第2講：今日的生成式人工智慧厲害在哪裡？從「工具」變為「工具人」

如何更好利用生成式ai？有两种策略：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/7gvU1H.png)

首先将提示工程，这里是一本提示工程的手册[https://www.promptingguide.ai/zh](https://www.promptingguide.ai/zh)

### 第3/4/5講：訓練不了人工智慧嗎？你可以訓練你自己 [[slide1](https://speech.ee.ntu.edu.tw/~hylee/genai/2024-spring-course-data/0301/0301_prompt_part1.pdf)|[slide2](https://speech.ee.ntu.edu.tw/~hylee/genai/2024-spring-course-data/0308/0308_prompt_part2.pdf)|[slide3](https://drive.google.com/file/d/1dMxMAewRtcUM2xktVm77txSk1leepgD1/view)]

有哪些在不訓練模型的情況下強化語言模型的方法

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/yGNY6N.png)

#### 1.神奇咒语

一些论文发现“让模型一步步思考、解释一下为什么这样回答、情绪勒索”等等话语能够提高模型的性能。[202312,Principled Instructions Are All You Need for Questioning LLaMA-1/2, GPT-3.5/4](https://arxiv.org/pdf/2312.16171) 论文指出如下一些有趣的发现：

+ No need to be polite with LLM so there is no need to add phrases like “please”, “if you don’t mind”, “thank you”, “I would like to”, etc.,
+ Employ affirmative directives such as ‘do,’ while steering clear of negative language like ‘don’t’.
+ Add “I’m going to tip $xxx for a better solution!”
+ Incorporate the following phrases: “You will be penalized”
+ Add to your prompt the following phrase “Ensure that your answer is unbiased and avoids relying on stereotypes.”

论文[202206, Learning to Generate Prompts for Dialogue Generation through
Reinforcement Learning](https://arxiv.org/pdf/2206.03931) 使用强化学习来学习并给出更好的神奇咒语。

> 注意：不过对于最新的很强的模型，奇怪的咒语可能就没用了。可以让大语言模型自己给自己提供很不错的咒语。

#### 2.提供额外的信息

+ 讲清楚你的需求，模型会提供更符合要求的答案。
+ 提供大语言模型原本不清楚的信息，例如把新的论文给他。
+ 给模型提供一些反例，让他根据翻译（这种技术称为[In-context learning](https://arxiv.org/pdf/2005.14165)，注意这里的训练并没有真的对模型进行训练）。
> 但是模型有真的看懂范例吗？[202303Larger language models do incontext learning differently](https://arxiv.org/abs/2303.03846)这篇文章足够强的模型有能力看懂范例，而模型能力不足的情况下，就难以看懂范例。文章使用不同错误范例的比例进行研究，发现最新的模型对错误范例的增多是很敏感的。
![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/1Mp3Mx.png)

Gemini1.5的报告中指出，让机器翻译一个从没翻译过的语言，机器的能力很差（0-shot结果）；当给机器一些翻译书作为样例，机器的翻译准确率会有大幅提高
![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/WniCoH.png)

#### 3.拆解任务

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/oCcdWb.png)

+ 拆解任務（Recursive Reprompting and Revision）：叫模型思考 (Chain of Thought, CoT) 或解釋會有用。不过拆解任务这个步骤目前已部分整合到GPT3.5中。例如為GPT3.5解數學題都預設详细列式：
![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/kMb2T9.png)
+ [Constitutional AI- Harmlessness from AI Feedback](https://arxiv.org/pdf/2212.08073)： 模型檢查自己的答案。现在的大语言模型也集成了预纠错功能，如下图：

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/cqoQyP.png)

+ [Self-Consistency](https://arxiv.org/pdf/2203.11171)：多次生成，输出一致性/重复次数最高/不确定度最低的答案

我们可以将上述方法集成在一起，打一套组合拳：

![Qk7C84](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/Qk7C84.png)

#### 4.使用工具

+ 搜索引擎 - Retrieval Augmented Generation (RAG)

![tAJr9W](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/tAJr9W.png)

+ 代码 - [Program of Thought (PoT)](https://arxiv.org/pdf/2211.12588)

![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/lvLvdk.png)

+ 文字生圖 AI (DALL-E)
![](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/TSWymA.png)

+ 其他：
![z4BqN3](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/z4BqN3.png)

语言模型是如何使用工具的呢？其实使用工具都是在进行文字接龙：

![foZd4h](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/foZd4h.png)

+ 组合拳

![uIiSgF](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/uIiSgF.png)

#### 5.語言模型彼此合作

未來不需要打造全能的模型，語言模型可以專業分工，不同團隊可以專注於打造專業領域的語言模型，并通过一下几种方式增强模型的性能：

![9STp3B](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/9STp3B.png)

![9Q4RKr](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/9Q4RKr.png)

![0aYihx](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/0aYihx.png)

甚至可以对不同的Agent打分，以选择好用的模型：

![ByzBrd](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/ByzBrd.png)