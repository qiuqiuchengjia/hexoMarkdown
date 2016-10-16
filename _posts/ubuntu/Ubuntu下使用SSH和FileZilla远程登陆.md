---
title: Ubuntu下使用SSH和FileZilla远程登陆
date: 2016-10-15 17:12:03
tags: ubuntu
categories: ubuntu
---

## File Zilla下载安装

- File Zilla是一个开源的，跨平台的Linux FTP客户端。File Zilla有一个标签式的用户界面，允许用户查看正在传输的文件的所有细节。File Zilla是通过网络传输较大文件的完美方式，它允许恢复大于4GB的文件。它的拖放功能使其能够更轻松地通过FTP传输文件

<center>![](http://o99dg8ap9.bkt.clouddn.com/Ubuntu%E4%B8%8B%E4%BD%BF%E7%94%A8SSH%E5%92%8CFileZilla%E8%BF%9C%E7%A8%8B%E7%99%BB%E9%99%86.jpg)</center>

- [下载地址](https://filezilla-project.org/download.php?show_all=1)

## SSH概述

- SSH是指Secure Shell,是一种安全的传输协议，Ubuntu客户端可以通过SSH访问远程服务器 

- SSH分客户端openssh-client和openssh-server
如果你只是想登陆别的机器的SSH只需要安装openssh-client（ubuntu有默认安装，如果没有则sudoapt-get install openssh-client），如果要使本机开放SSH服务就需要安装openssh-server

## 安装SSH客户端

- Ubuntu缺省已经安装了ssh client

```java
sudo apt-get install ssh  或者 sudo apt-get installopenssh-client
 ssh-keygen 
```

- (按回车设置默认值)

- 按缺省生成id_rsa和id_rsa.pub文件，分别是私钥和公钥

- 说明：如果sudo apt-get insall ssh出错，无法安装可使用sudo apt-get install openssh-client进行安装

## SSH登陆远程服务器

- 假定服务器ip为192.168.1.1，ssh服务的端口号为22，服务器上有个用户为root

- 用ssh登录服务器的命令为：

```java
>ssh –p 22 root@192.168.1.1
>输入root用户的密码
```

## 安装SSH服务端

- Ubuntu缺省没有安装SSH Server，使用以下命令安装：

```java
sudo apt-get install openssh-server
```

- 然后确认sshserver是否启动了：（或用“netstat -tlp”命令）

```java
ps -e|grep ssh
```

- 如果只有ssh-agent那ssh-server还没有启动，需要/etc/init.d/ssh start，如果看到sshd那说明ssh-server已经启动了

- 如果没有则可以这样启动：

```java
sudo/etc/init.d/ssh start
```

- 事实上如果没什么特别需求，到这里 OpenSSH Server 就算安装好了。但是进一步设置一下，可以让 OpenSSH 登录时间更短，并且更加安全。这一切都是通过修改 openssh 的配置文件 sshd_config 实现的

<!-- more -->

## SSH配置

- ssh-server配置文件位于/etc/ssh/sshd_config，在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222。然后重启SSH服务：

```java
sudo /etc/init.d/sshresart 
```

- 通过修改配置文件/etc/ssh/sshd_config，可以改ssh登录端口和禁止root登录。改端口可以防止被端口扫描

```java
sudo cp/etc/ssh/sshd_config /etc/ssh/sshd_config.original  
sudochmod a-w /etc/ssh/sshd_config.original  
```

- 编辑配置文件：

```java
gedit /etc/ssh/sshd_config  
```

- 找到#Port 22，去掉注释，修改成一个五位的端口：
Port 22333
找到#PermitRootLogin yes，去掉注释，修改为：
PermitRootLogin no
配置完成后重起：


```java
sudo/etc/init.d/ssh restart  
```

## SSH服务命令

```java
停止服务：sudo /etc/init.d/ssh stop
启动服务：sudo /etc/init.d/ssh start
重启服务：sudo /etc/init.d/sshresart
断开连接：exit
登录：ssh root@192.168.0.100 
```

-   root为192.168.0.100机器上的用户，需要输入密码

## SSH登录命令

- 常用格式：ssh [-llogin_name] [-p port] [user@]hostname
更详细的可以用ssh -h查看

### 举例

- 不指定用户：

```java
ssh 192.168.0.1  
```

- 指定用户：

```java
ssh -l root 192.168.0.1  
ssh root@192.168.0.1  
```

- 如果修改过ssh登录端口的可以：

```java
ssh -p 22333 192.168.0.111  
ssh -l root -p 22333 216.230.230.105  
ssh -p 22333 root@216.230.230.105  
```

## 提高SSH登录速度

- 在远程登录的时候可能会发现，在输入完用户名后需要等很长一段时间才会提示输入密码。其实这是由于 sshd 需要反查客户端的 dns 信息导致的。可以通过禁用这个特性来大幅提高登录的速度。首先，打开 sshd_config 文件：

```java
sudo nano /etc/ssh/sshd_config  
```


- 找到 GSSAPI options 这一节，将下面两行注释掉：

```java
#GSSAPIAuthentication yes 
#GSSAPIDelegateCredentials no
```

- 然后重新启动 ssh 服务即可：

```java
sudo /etc/init.d/ssh restart  
```

- 再登录试试，应该非常快了吧

## 转载博客

- [Ubuntu环境下SSH的安装及使用](http://blog.csdn.net/netwalk/article/details/12952051)

