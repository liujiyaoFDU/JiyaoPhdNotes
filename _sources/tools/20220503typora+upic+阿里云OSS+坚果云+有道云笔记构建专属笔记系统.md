#! https://zhuanlan.zhihu.com/p/514021380
# typora+upic+阿里云OSS+坚果云+有道云笔记构建专属笔记系统

一直苦恼于怎么样才能更方便的用markdown记笔记，最近摸索了一套方案，不得不说确实很完美（shuang）🤩。功能如下：

+ 优雅的typora撰写markdown笔记；
+ upic图床+阿里云OSS实现在markdowm图片上传至云端不丢失；
+ 坚果云保证本地的markdown笔记同步到多个设备；
+ 有道云笔记可以将写好的md笔记上传，方便多设备查看，并且有文本内容搜索的功能

>  为什么要这么多同步呢？万一网易倒闭了or本地崩了，还有另一个笔记备份哈哈（生于忧患，死于安乐！！

好了，介绍完了为什么要这么做，接下来跟着我构建你的专属笔记系统把~

> 说明：本教程使用mac系统配置，window基本类似，可能仅有一些细节之处不同

## 1.下载typora

作为最好用的markdown编辑器，肯定要用他来写笔记啦！作为所写即所得的md编辑器，这颜值不用我吹了吧（如图

![image-20220513172108673](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513172108673.png)

今年开始，我最爱的typora也要收费了，作为白嫖党的我，怎么能就此收手呢？上有政策，下有对策：

> 破解版下载方法：微信搜索typora Mac 破解，就能找到很多破解的typora了，windows也有很多破解版本的。作为学生党，收费版伤不起啊😭

## 2.upic+阿里云OSS图床配置

typora里面插入的图片是保存在本地的，久而久之很容易出现丢失，因此，我们可以使用阿里云OSS作为图床，配置到upic图床上传工具，在嵌入typora，实现将图片粘贴进typora的同时就能将图片上传至云端图床。

**step1：下载upic**

upic是一款免费的图床客户端，下载链接：https://github.com/gee1k/uPic

**Step2：购买阿里云OSS**

出于图床的稳定性考虑，我们采用了更加稳定的阿里云，阿里云OSS一年每月40G只需要9元，很良心啊有木有！开通方法如下：

1. 进入[阿里云oss官网](https://www.aliyun.com/product/oss) → 折扣套餐 → 选择套餐购买即可

<img src="https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513173254095.png" alt="image-20220513173254095" style="zoom:50%;" />

![image-20220513173431013](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513173431013.png)

2. 点击[阿里云oss官网](https://www.aliyun.com/product/oss)的管理控制台 → 创建Bucket（一定要选择公共读，不然无法通过链接获取图片）

![image-20220513173608988](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513173608988.png)

![mnkAo8-zNaqXW](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/1460000040723593.png)

3. 鼠标放在头像上，点击accesskey管理 →创建accesskey → 记住对应的 keyid 以及 Secret

![截屏2022-05-13 17.39.38](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/%E6%88%AA%E5%B1%8F2022-05-13%2017.39.38.png)



4. 去uPic的偏好设置中，添加阿里云oss，及其他内容，点击验证，如果弹出消息上传成功即可：

![R9ByU3-0d73KT](https://segmentfault.com/img/remote/1460000040723598)

![image-20220513175005040](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513175005040.png)

到此，图床环境已经配置完毕。upic的使用方法就自行百度哈~

注意，在设置里更换完图床后一定记得将图床换到阿里云oss

![image-20220513174723823](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513174723823.png)

 ## 3. typora嵌入upic图床

为了更方便的上传图片，typora支持粘贴进编辑器即可上传，打开typora设置为如下选项：

![image-20220513175328966](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513175328966.png)

在typora中粘贴图片便可自动上传至阿里云oss，如下：

![截屏2022-05-13 17.54.05](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/%E6%88%AA%E5%B1%8F2022-05-13%2017.54.05.png)

## 4.坚果云配置（也可以不用这个，只是为了防止本地文件丢失）

很简单，只要将文件夹上传至坚果云即可。坚果云不像百度网盘是一个本地和云端完全分离的文件夹，坚果云实质上是将本地文件夹同步至云端，当本地文件夹加入新文件时，在云端也可实现同步。

Ps：坚果云最好用的地方在于zotero+坚果云+ipad pdf expert实现电脑平板同步看论文，再任意一个设备上批注的论文可以在另一个设备查看，最近也在使用，还是非常nice的，在之后我也会出一篇环境的配置教程。

## 5.上传md笔记至有道云笔记

我最心动的内容匹配检索功能，虽然说evernote等笔记软件也有，但是有道云还是颜值高一些

![image-20220513175809789](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/image-20220513175809789.png)

我自己的笔记内容如下：

![截屏2022-05-13 18.10.05](https://ossjiyaoliu.oss-cn-beijing.aliyuncs.com/uPic/%E6%88%AA%E5%B1%8F2022-05-13%2018.10.05.png)

至此，笔记系统也算构建完成了，最近爱上了记笔记。说实话，看的论文学的新东西很容易过完脑子就忘掉了，不要想着一次性记住（可能我的记性太差了，真的会刚看就忘掉）。好记性不如烂笔头，有一天忘了还能回来翻看，乱七八糟记了一大堆笔记，希望自己不仅仅只是把笔记记下来，更重要的是能够记在脑子里吧🧐