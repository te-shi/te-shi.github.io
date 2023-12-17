---
layout: post
title: "SSH免密登陆服务器"
date: 2023-12-16
description: "Linux_ssh"
tag: Linux
---

每次连接都需要输入密码未免有些麻烦，一台机器想要免密访问其他机器，需要把自己的公钥内容发送到别的机器的authorized\_keys中去，并在本机config文件中配置私钥文件位置。具体操作分为如下三个步骤。

### **1. 生成新的密钥对**

使用命令`ssh-keygen`生成新的密钥对。**一路回车即可！**如下图所示。

![](/images/posts/sshpassword/ssh-keygen.png)


打开/home/\[Client 用户名]/.ssh/id\_rsa.pub然后复制文件内容。

### 2、在Server服务器中添加许可

使用命令行cd /home/\[Server用户名]/.ssh进入到.ssh文件夹下。(其中\[Server用户名]对应服务器的用户名)

使用touch authorized\_keys创建许可文件。

将客户机id\_rsa.pub中的内容全部复制到authorized\_keys文件中。

\


### 3、 配置客户机VSCode

在VSCode中，按Ctrl+Shift+P打开命令面板，输入并选择Remote-SSH: Open SSH Configuration File…，并编辑配置文件，如下所示：

Host \[自定义主机名称]

&#x20;HostName \[Server IP]

&#x20;User \[Server 用户名]

&#x20;PreferredAuthentications publickey

&#x20;IdentityFile "C:/Users/\[Client 用户名]/.ssh/id\_rsa"

