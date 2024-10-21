# AI agent

##  第9講：以大型語言模型打造的AI Agent

> Slide: https://speech.ee.ntu.edu.tw/~hylee/genai/2024-spring-course-data/0412/0412_agent.pdf
>
> Video: https://www.youtube.com/watch?v=bJZTJ7MjYqg

目前多数人使用AI的方式让他做一件事，例如：翻译、产生一张图等等。但是对于人类来说，可以做需要多个步骤才能完成的有序的复杂任务。如果AI可以帮人完成这复杂的工作的话，就可以将其称为**AI agent**。

### AI agent介绍

利用LLM为接口，AI agent可以模拟人类去做多步骤的复杂任务，例如：做计划、调整计划、使用工具，下面是一些目前已有的AI agent工具：

+ AutoGPT: https://github.com/Significant-Gravitas/Auto-GPT
+ AgentGPT: https://agentgpt.reworkd.ai/
+ BabyAGI: https://github.com/yoheinakajima/babyagi 
+ Godmode: https://godmode.space/?ref=futuretools.io

下面是一些有趣的应用：[由 AI 村民組成的虛擬村莊](https://youtu.be/G44Lkj7XDsA?si=cMbKG3tqPbIgnnBq)、[會自己玩 Minecraft 的 AI](https://arxiv.org/abs/2305.16291)、由語言模型操控的機器人、用大型語言模型開自駕車等等

![image-20240921131624768](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240921131624768.png)

![image-20240921131641435](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240921131641435.png)

![image-20240921131654906](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240921131654906.png)

### AI agent的实现原理

AI agent要有一个终极目标，并且可能会有过去经验的记忆，然后从外界环境了解现在的状态，最后以此产生一个短期的目标的计划。Agent根據擁有的資訊來決定一個行動，也許是做某一種的操控或是產生一文字、語音之類的行為。
Agent在行動之後可能會改變環境狀態，這時候它得到的經驗就會更新，然後再調整它的計劃。

![8SEgKo](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/8SEgKo.png)

### 有记忆能力的GPT

可以使用使用**Retrieval-augmented Generation（RAG）**使得GPT有记忆能力，参考论文：https://github.com/cpacker/MemGPT

![

](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/tERDsG.png)

### AI agent如何将文本变成实际行动？

整個AI Agent中最困難的就是怎麼將文本指令變成實際行動，參考[RL-GPT](https://arxiv.org/abs/2402.19299)中將Agent分成兩個：

+ Slow Agent，產生人類可以理解的自然語言
+ Fast Agent，依Slow Agent指令想辦法執行特定行動，或者產生程式碼來執行特定行動

### AI agent综述

> https://arxiv.org/abs/2309.07864

![image-20240921132938641](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20240921132938641.png)