---
layout: post
title: "一台PC配置两个git"
date: 2024-01-16
description: "一台PC配置两个git"
tag: git
---

**现在云端仓库很多，有开源中国的 gitee.com 、微软的 github.com 、还有 gitlab.com 等等，当我们想同一台电脑链接多个云端仓库时，就需要设置不同的用户、邮箱生成不同的密钥进行链接**

### 一、生成公私钥对

**假设我有github.com 、gitlab.com 两个云端的账号，各自的注册邮箱和用户名不同。**

**建议：linux 或 mac 用户推荐在 `~/.ssh` 路径下运行命令行，Windows用户推荐在“ `C:\Users\用户名\.ssh` ”目录下运行命令行**

```c++
## 说明: 
## -t rsa 表示加密方式  
## -C 表示注册不同云端仓库时所用的邮箱 
## -f 表示自定义生成秘钥的名称
#--------------------------------------
# 生成 github.com 公私钥对
ssh-keygen -t rsa -C "te.shi@whu.edu.cn" -f github
# 生成 gitlab.com 公私钥对
ssh-keygen -t rsa -C "shite@xingshentech.com" -f gitlab
```

**生成的文件就在当前运行命令的文件夹内**

![](/images/posts/git2/1.png)

### **二、登录云端仓库配置公钥**

**这里拿github举例。**

**登录github后，点击右上角头像，选择Settings**

**弹出页面，选择SSH and GPG keys**

**输入标题，将生成的github的公钥(github.pub)文件内的内容拷贝至 Key，点击Add SSH key**

![](/images/posts/git2/2.png)

### 三、创建config文件

在刚刚生成公私钥对的文件夹内，创建config文件内容如下

![](/images/posts/git2/3.png)

配置解析

```yaml
# 这里是自定义的host简称，以后连接远程服务器就可以用命令ssh myhost
Host
# 主机名可用ip也可以是域名(如:github.com或者bitbucket.org)
HostName
# 服务器open-ssh端口（默认：22,默认时一般不写此行）
Port
# 配置登录时用什么权限认证
# 可设为(publickey, password publickey, keyboard-interactive 等)
PreferredAuthentications
# 证书文件路径（如~/.ssh/id_rsa_*)
IdentityFile
# 云端仓库登录用户名
User
```

### **四、远程测试链**

```yaml
# 测试链接是否成功, 这里的github、gitlab是config文件中设置的Host
ssh -T git@github
ssh -T git@gitlab
```

运行命令后如果出现“`Hi xxxx! You’ve successfully authenticated, but GitHub does not provide shell access.`”，其中“`xxxx`”为你配置的 User 名字，这时恭喜你，配置成功了~

### **五、下载github项目**

```yaml
# 单个云端仓库时, 运行 git clone 你的项目地址
git clone git@github.com: 我的用户名/Radish-Tools.git

# 多个云端仓库时, 需要修改你的项目地址的@对象
# 这里的github是config文件中的Host
git clone git@github: 我的用户名/Radish-Tools.git
# 这里的gitlab是config文件中的Host
git clone git@gitlab: 我的用户名/Radish-Tools.git
```



### **六、配置局部用户名与邮箱**

**取消全局用户名与邮箱设置**

```yaml
# 取消全局 用户名/邮箱 配置
git config --global --unset user.name
git config --global --unset user.email
```

**进入每个项目的文件夹，设置局部用户名与邮箱**

```yaml
# 单独为每个repo设置 用户名/邮箱
git config user.name "one_name" ; git config user.email "one@gmail.com" # 例如对github
git config user.name "two_name" ; git config user.email "two@gmail.com" # 例如对gitlab
```






