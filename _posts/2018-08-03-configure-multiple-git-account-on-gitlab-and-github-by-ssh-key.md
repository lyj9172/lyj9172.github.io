---
layout: post
title:  "在windowns10上配置多套ssh密钥认证多个git用户（同一个gitlab多个用户和github用户）"
categories: gitlab
tags:  gitlab ssh
author: Evan
---
* content
{:toc}

## 用户信息

两个gitlab用户和一个github用户，同一个gitlab系统中的不同用户邮箱地址是不一样的

ID|平台|username |ssh密钥 passphrase| email
---|---|---|---|---
1|gitlab|user1 |password1|user1@163.com
2|gitlab|user2 |password2|user2@163.com
3|github|user3 |password3|user3@163.com

## 创建ssh密钥
使用下面命令创建gitlab平台用户user1的密钥
```
ssh-keygen -t rsa -C "gitlabuser1@163.com" -b 4096
```
详细过程
```
lyj91@DESKTOP-4SS3LVE MINGW64 ~
$ ssh-keygen -t rsa -C "gitlabuser1@163.com" -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/lyj91/.ssh/id_rsa): /c/Users/lyj91/.ssh/id_rsa_gitlabuser1
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/lyj91/.ssh/id_rsa_gitlabuser1.
Your public key has been saved in /c/Users/lyj91/.ssh/id_rsa_gitlabuser1.pub.
The key fingerprint is:
SHA256:25kdjm9vb9CiYVlPuscvZE4gFq1mfi0vFRyvORjCppQ lyj9172@163.com
The key's randomart image is:
+---[RSA 4096]----+
|           .     |
|          . . .  |
|         o o . o |
|        E X o + o|
|       .SB o.B O |
|        .o.*B.& o|
|        . =oo@ * |
|           .+ * +|
|           ..++=+|
+----[SHA256]-----+
```

使用上面同样的方法生成三套ssh密钥
ID|username |ssh私钥|ssh公钥
---|---|---|---|---
1|user1 |/c/Users/lyj91/.ssh/id_rsa_gitlabuser1|/c/Users/lyj91/.ssh/id_rsa_gitlabuser1.pub
2|user2 |/c/Users/lyj91/.ssh/id_rsa_gitlabuser2|/c/Users/lyj91/.ssh/id_rsa_gitlabuser2.pub
3|user3 |/c/Users/lyj91/.ssh/id_rsa_githubuser3|/c/Users/lyj91/.ssh/id_rsa_githubuser3.pub


## 将ssh公钥配置到gitlab和github对应用户下

使用下面命令复制公钥内容到复制面板
```
cat ~/.ssh/id_rsa_gitlabuser1.pub | clip
```

使用下面命令复制公钥内容到粘贴到gitlab或github的SSH Keys

[![360截图20180803020222056.jpg](https://i.loli.net/2018/08/03/5b6347d92bc89.jpg)](https://i.loli.net/2018/08/03/5b6347d92bc89.jpg)

## 配置config文件

在本地~/.ssh 目录下创建config文件，该文件用于配置私钥对应的服务器

```
Host 10.10.10.10  #ssh匹配项自定义名，一般配置gitlab的IP或者域名，如果是同一个gitlab有多个用户要配置时，可以在再加上用户名做标识
    HostName 10.10.10.10 #必须配置gitlab的IP或者域名
    IdentityFile ~/.ssh/id_rsa_gitlabuser1
    PreferredAuthentications publickey
    User user1

Host 10.10.10.10.user2 #避免与第一个冲突，配置配置gitlab的IP加.用户名
    HostName 10.10.10.10
    IdentityFile ~/.ssh/id_rsa_gitlabuser2
    PreferredAuthentications publickey
    User user2
	
Host github.com
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_githubuser3
    PreferredAuthentications publickey
    User user3
```

## 将gitlab或github的工程clone到本地

从平台获取项目的ssh链接如下

```
git@10.10.10.10.10:MMtools/perf_collectdata.git
```

[![360截图20180803021742034.jpg](https://i.loli.net/2018/08/03/5b634eab7adf5.jpg)](https://i.loli.net/2018/08/03/5b634eab7adf5.jpg)

@与:之间的IP或域名使用config文件中的Host参数值替换
比如使用user1来clone的项目，使用如下git命令

```
git clone git@10.10.10.10.10:MMtools/perf_collectdata.git
```

使用user2来clone的项目，使用如下git命令

```
git clone git@10.10.10.10.10.user2:MMtools/perf_collectdata.git
```

## 配置各仓库的**user.name/usere.mail**

gitlab和github在统计提交的时候，会判断邮箱是否跟github的登陆邮箱匹配，不匹配则不计算活跃度，即不生成小绿块。

需要配置正确的**user.name/usere.mail**

- 设置当前仓库的user.name/user.email

```
git config user.name user1
git config user.email user1@163.com
```

- 设置全局user.name/user.email

```
git config --global user.name user1
git config --global user.email user1@163.com
```

- 获取当前配置

```
git config user.name user1
git config user.email user1@163.com
```

现在可以做git push了
