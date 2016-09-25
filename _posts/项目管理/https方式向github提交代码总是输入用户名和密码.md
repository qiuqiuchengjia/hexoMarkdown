---
title: https方式向github提交代码总是输入用户名和密码
date: 2016-08-13 19:24:27
tags: 项目管理
categories: 项目管理
---

- 采用HTTPS方式提交代码到github和coding很方便，但是总是需要输入用户名和密码，以下这种方式可以解决

## 新建.git-credentials文件

在你的用户目录下新建一个文本文件, 名曰 __.git-credentials__

__用户目录:__

- windows: C:/Users/username

- mac os x: /Users/username

- linux:  /home/username

## 输入用户名和密码

```java
https://username:password@github.com
https://username:password@git.coding.net
```

> 如何用hexo的话可以不用输入用户名和密码，执行hexo语句会弹出对话框提示输入

## 修改git配置

- 执行命令

```java
git config --global credential.helper store
```

- 上述命令会在~/.gitconfig文件末尾添加如下配置:

```java
[credential]
     helper = store
```

<!-- more -->

## 参考资料

- [解决向github提交代码是老要输入用户名密码](http://www.jianshu.com/p/81ae6e77ff47)