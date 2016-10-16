---
title: ubuntu安装lampp
date: 2016-10-15 16:33:10
tags: ubuntu
categories: ubuntu
---

## 下载和安装

- [下载地址](https://www.apachefriends.org/zh_cn/index.html)

- 在终端中使用 root 权限，然后进入你刚刚下载的文件的那个目录

- 修改权限，将刚刚下载的文件变成可执行

```java
chmod 777 *.run
```

- 然后进行安装

```java
./ 你刚刚下载的文件名.run

//例如
./xampp-linux-x64-5.6.23-0-installer.run
```

- 文件安装后的默认保存路径是 /opt/lampp

## 配置文件

- Apache文档根目录：/opt/lampp/htdocs/

- Apache配置文件：/opt/lampp/etc/httpd.conf

- MySQL配置文件：/opt/lampp/etc/my.cnf

- PHP配置文件：/opt/lampp/etc/php.ini

- ProFTPD配置文件：/opt/lampp/etc/proftpd.conf

- PHPMyadmin配置文件：/opt/lampp/phpmyadmin/config.inc.php

<!-- more -->

## 常见命令

- 调出控制界面

```java
$ cd /opt/lampp/share/xampp-control-panel
$ sudo ./xampp-control-panel
```

- 启动/停止/重启Apache：

```java
启动：/opt/lampp/lampp start
停止：/opt/lampp/lampp stop
重启：/opt/lampp/lampp restart
```
- 安全设置：

```java
/opt/lampp/lampp  security
```

- 使用php版本/查看版本：

```java
使用php4：/opt/lampp/lampp php4
使用php5：/opt/lampp/lampp php5
查看php版本：/opt/lampp/lampp phpstatus
```
- 只启动和停止Apache：

```java
启动：/opt/lampp/lampp stopapache
停止：/opt/lampp/lampp startapache
```

- 只启动和停止MySQL：

```java
启动：/opt/lampp/lampp startmysql
停止：/opt/lampp/lampp stopmysql
```

- 只启动和停止ProFTPD服务器：

```java
启动：/opt/lampp/lampp startftp
停止：/opt/lampp/lampp stopftp
```

- 启动和停止Apache的SSL支持：

```java
启动：/opt/lampp/lampp startssl
停止：/opt/lampp/lampp stopssl
```

- 随系统自启动：

```java
ln –s /opt/lampp/lampp/etc/rc.d/rc3.d/S99lampp

ln –s /opt/lampp/lampp/etc/rc.d/rc4.d/S99lampp

ln –s /opt/lampp/lampp/etc/rc.d/rc5.d/S99lampp
```

- 取消自启动：

```java
ln –s /opt/lampp/lampp K01lampp
```
            
- 卸载XAMPP：

```java
rm –rf /opt/lampp
```

## XAMPP: Couldn't start MySQL!解决方案 (启动不了mysql服务）

```java
 sudo chmod 777 -R /opt/lampp/var
```
