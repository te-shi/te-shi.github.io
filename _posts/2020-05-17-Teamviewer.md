---
layout: post
title: "Teamviewer被检测商用行为解决办法"
date: 2020-05-17 
description: "Teamviewer"
tag: 软件
---



### Teamviewer被检测商用行为解决办法

Teamviewer在当作个人用户使用时是完全免费的，但如果连接频次过高，时间长的话，会被认为是当作商业用途，出现下图的状况，必须要进行激活许可证才能正常使用，且是收费的。本文将介绍如何继续免费使用该软件。

![](/images/posts/tm/tm0.png)

**1 将Teamviewer完全卸载**

a. 首先要做的就是卸载原来安装的Teamviewer，找到Teamviewer，卸载。

![](/images/posts/tm/tm1.png)

b. 打开计算机>>>C>>>用户>>>Administrator>>>AppDate>>>Roaming，找到其中的Teamviewer选项，找不到的可以在文件夹内通过搜索Teamviewer，删除该选项。

Notice：AppDate文件夹是被隐藏的，需要显示隐藏的文件/文件夹

![](/images/posts/tm/tm2.png)

c. 按Win + R 键，调出运行窗口，输入regedit，打开电脑的注册表，然后一次找到下面的路径：HKEY_LOCAL_MACHINE\SOFEWARE\Wow6432Node\TeamViewer，也将其删除，如下图所示

![](/images/posts/tm/tm3.png)

**2 修改网卡MAC地址**

个人觉得，Teamviewer id应该是根据网卡的MAC地址进行散列得到的，所以想要改变Teamviewer的id，自然要改变MAC地址。

链接：https://pan.baidu.com/s/1PC8qpkK6rxuPOf5H-pipzQ 
提取码：xfb6 

下载该软件，修改网卡的MAC地址，软件界面如下所示。如果是win10系统，选择win8那项即可。然后选择对应的网卡，重置MAC地址就OK

![](/images/posts/tm/tm4.png)

**3 重新安装Teamviewer**

这时会发现teamviewer id变了，可以继续进行免费的个人使用了

![](/images/posts/tm/tm.png)



