---
title: hexo搭建个人博客
date: 2016-06-02 18:24:38
tags: [hexo,博客]
categories: 博客
photos:
- http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%875.png
- http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%877.png
- http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%873.png
---

- 首先我们应该学会看官方文档，文档是最好的资料 [传送门](https://hexo.io/zh-cn/docs/index.html)

- 首先我们需要安装Git [传送门](https://git-scm.com/)和 Node.js [传送门](https://nodejs.org/en/)

## Hexo安装

- 打开dos命令行，安装需要的组件

```cpp
npm install hexo-cli -g
npm install hexo --save
#如果命令无法运行，可以尝试更换taobao的npm源
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## Hexo初始化配置

- 安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件

```cpp
hexo init <folder>
cd <folder>
npm install
```

<!-- more -->

### 安装Hexo插件

- 如果想不出错，就将下面的插件都安装完

```cpp
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```

### 本地查看效果

- 执行下面语句，执行完即可登录localhost:4000查看效果

```java
hexo generate
hexo server
```


## 部署

- 安装 hexo-deployer-git
 
```cpp
 npm install hexo-deployer-git --save
```

### SSH方式

#### 配置SSH密钥

- 我们需要看看是否看看本机是否存在SSH keys,打开Git Bash,并运行:

```cpp
cd ~/. ssh 
```

#### 创建一对新的SSH密钥(keys)

```cpp
ssh-keygen -t rsa -C "your_email@example.com"
#这将按照你提供的邮箱地址，创建一对密钥
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): [Press enter]
```

#### 在GitHub账户中添加你的公钥

- 将公钥的内容复制到系统粘贴板(clipboard)中

#### 测试

```cpp
ssh -T git@github.com
```

- 然后会看到：

> Hi qiuchengjia! You've successfully authenticated, but GitHub does not provide shell access.

#### 设置用户信息

```cpp
git config --global user.name "cnfeat"//用户名
git config --global user.email  "cnfeat@gmail.com"//填写自己的邮箱
```


#### 修改配置文件

- 修改配置

```cpp
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

- 例如：

```java
deploy:
    type: git
    repo: 
         coding: git@git.coding.net:qiuchengjia/qiuchengjia.git,master
         github: git@github.com:qiuchengjia/qiuchengjia.github.io.git,master
    message: 
```

### HTTPS方式

- 修改配置文件

```cpp
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

- 例如：

```java
deploy:
    type: git
    repo: 
         github: https://github.com/qiuchengjia/qiuchengjia.github.io.git,master
         coding: https://git.coding.net/qiuchengjia/qiuchengjia.git,master
    message: 
```

- 这里会出现每次提交都要求输入用户名和密码，可以参考文章 [https方式向github提交代码总是输入用户名和密码](http://www.qiuchengjia.cn/2016/08/14/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/https%E6%96%B9%E5%BC%8F%E5%90%91github%E6%8F%90%E4%BA%A4%E4%BB%A3%E7%A0%81%E6%80%BB%E6%98%AF%E8%BE%93%E5%85%A5%E7%94%A8%E6%88%B7%E5%90%8D%E5%92%8C%E5%AF%86%E7%A0%81/) 进行配置

### 部署进 github 或 coding


```cpp
hexo g
hexo d
```

- 还有一点注意事项，如果要添加 README.md 文件的话，我们可以在本地
写好，然后放入public文件夹下，如图：

<center>![](http://7xstki.com1.z0.glb.clouddn.com/hexo%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA.png)</center>

- 此文只是安装hexo的主要流程，具体事项参考hexo官方文档 [传送门](https://hexo.io/zh-cn/docs/index.html)

- 我的hexo博客搭建在github [传送门](https://github.com/qiuchengjia/qiuchengjia.github.io)，coding上 [传送门](https://coding.net/u/qiuchengjia/p/qiuchengjia/git)，喜欢可以star一下

<center>![](http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%875.png)</center>

</br>
</center>![](http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%877.png)</center>

</br>

<center>![](http://7xstki.com1.z0.glb.clouddn.com/github%E5%9B%BE%E7%89%873.png)</center>


## 参考资料

- [小白独立搭建博客--Github Pages和Hexo简明教程](http://my.oschina.net/ryaneLee/blog/638440)