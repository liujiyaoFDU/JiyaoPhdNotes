#! https://zhuanlan.zhihu.com/p/681183050
# Remote-SSH XHR failed无法访问远程服务器

## 问题描述

当使用VScode Remote-SSH连接远程服务器时，出现错误：

```bash
无法与 "{serverxx}" 建立连接: XHR failed.
```

## 原因

该插件在连接远程服务器时，插件会在服务器端自动运行一个脚本。该脚本的主要内容是检查服务器的vscode-server的运行情况，若服务器端不能运行，则会自动下载。而当服务器网络不佳时，下载往往会超时，导致连接失败。

## 解决方案

手动在本地下载安装包，然后手动传到服务器端。步骤如下：

**1.获取最新的版本号（Commit ID）**：通过其他SSH等方式，进入远程服务器的Terminal，查看版本号，如

```bash
$ ls ~/.vscode-server/bin
2ccd690cbff1515156168451552234432401dc
```

在这里，我的commit id就是`2ccd690cbff1515156168451552234432401dc`。


**2.手动下载出问题的包（tarball）**，注意下面的COMMIT_ID要替换成自己在上一步得到的commit id。在浏览器输入网址：

```
https://update.code.visualstudio.com/commit:COMMIT_ID/server-linux-x64/stable
```

```
https://update.code.visualstudio.com/commit:2ccd690cbff1515156168451552234432401dc/server-linux-x64/stable
```
会自动打开一个下载链接下载对应的`vscode-server-linux-x64.tar.gz`。

**3.将vscode-server-linux-x64.tar.gz上传至服务器，解压**

使用scp命令上传至服务器。我的服务器是通过跳板机的，这里举一个例子，大家根据自己服务器的情况自行搜索命令。

```
# 服务器ssh进入vscode-server目录
$ cd /home/user/.vscode-server/bin/2ccd690cbff1515156168451552234432401dc/
# 清空原有文件，保持2ccd690cbff1515156168451552234432401dc文件夹下只有vscode-server-linux-x64.tar.gz解压出来的文件。
$ rm -rf *
# 在本地下载目录解压文件
$ tar -zxf vscode-server-linux-x64.tar.gz 
# 拷贝到服务器指定目录
$ cd vscode-server-linux-x64
$ scp -r -J serverjmp /* user@server575:/home/user/.vscode-server/bin/2ccd690cbff1515156168451552234432401dc/
```

完成上述步骤后,VS Code Server 就成功下载和安装了。重新使用VScode进行连接即可。

## 参考

1. https://zhuanlan.zhihu.com/p/671991410
2. https://blog.csdn.net/qq_45654306/article/details/132047411