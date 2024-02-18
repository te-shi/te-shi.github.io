---
layout: post
title: "VScode ssh XHR failed无法访问远程服务器"
date: 2024-01-16
description: "vscode ssh failed"
tag: vscode
---

****

### 问题概述

当使用VScode连接远程服务器时，往往需要使用Remote-SSH这个插件。而该插件有一个小bug，当远程服务器网络不佳时容易出现。

在控制台会出现下述语句：

```bash
Resolver error: Error: XHR failed at y.onerror (vscode-file://vscode-app/d:/Microsoft%20VS%20Code/resources/app/out/vs/workbench/workbench.desktop.main.js:77:1261)
```



### **原因与解决方法**

**原因**

该插件在连接远程服务器时，插件会在服务器端自动运行一个脚本。该脚本的主要内容是检查服务器的vscode-server的运行情况，若服务器端不能运行，则会自动下载。而当服务器网络不佳时，下载往往会超时，导致连接失败。



**解决方法**

懂了报错的原因，那就可以从中找出解决方法。即**手动在本地下载安装包，然后手动传到服务器端**。这样是可以解决这个问题的，虽然不是很优雅。



**步骤**

1. 通过其他SSH等方式（如：Xshell），进入远程服务器的Terminal。

2. 在服务器端，进入下面的目录，获取commit id。

   过程入下图所示, 在这里，我的commit id就是2ccd690cbff1569e4a83d7c43d45101f817401dc。

![](/images/posts/vs_code_ssh/img1.png)

3. 手动下载出问题的包（tarball)，注意下面的COMMIT_ID要替换成自己在上一步得到的commit id。

- 稳定版下载链接：https://update.code.visualstudio.com/commit:COMMIT_ID/server-linux-x64/stable


- 内测版下载链接：https://update.code.visualstudio.com/commit:COMMIT_ID/server-linux-x64/insider

​	如我的稳定版链接就是：https://update.code.visualstudio.com/commit:2ccd690cbff1569e4a83d7c43d45	101f817401dc/server-linux-x64/stable

4. 下载后会得到一个vscode-server-linux-x64.tar.gz，将其通过sftp等方式传到服务器上。路径：~/.vscode-	server/bin/COMMIT_ID/vscode-server-linux-x64.tar.gz

   ![](/images/posts/vs_code_ssh/img2.png)

5. 在服务器端进入该路径，解压。

```bash
$ cd ~/.vscode-server/bin/$COMMIT_ID
$ tar -xvzf vscode-server-linux-x64.tar.gz --strip-components 1
```

6. 重新使用VScode进行连接。

