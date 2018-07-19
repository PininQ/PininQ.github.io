---
layout: post
title: CentOS7中安装Git
categories: Git
description: CentOS7中安装Git
keywords: CentOS7, Git
---


**1、最新git源码下载地址：**

[https://github.com/git/git/releases](https://github.com/git/git/releases)

[https://www.kernel.org/pub/software/scm/git/](https://www.kernel.org/pub/software/scm/git/)

可以手动下载下来在上传到服务器上面

**2、移除旧版本git**

centos自带Git，7.x版本自带`git 1.8.3.1`（应该是，也可能不是）， 安装新版本之前需要使用`yum remove git`卸载（安装后卸载也可以）。

```vim
[root@Git ~]# git --version    ## 查看自带的版本
git version 1.8.3.1
[root@Git ~]# yum remove git   ## 移除原来的版本
```
**3、安装所需软件包**


```vim
[root@Git ~]# yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel 
[root@Git ~]# yum install gcc-c++ perl-ExtUtils-MakeMaker
```

下载&安装

```vim
[root@Git ~]# cd /usr/src
[root@Git ~]# wget https://www.kernel.org/pub/software/scm/git/git-2.9.5.tar.gz
```

**5、解压**

```vim
[root@Git ~]# tar xf git-2.9.5.tar.gz
```

**6、配置编译安装**

```vim
[root@Git ~]# cd git-2.9.5
[root@Git ~]# make configure
[root@Git ~]# ./configure --prefix=/usr/git ##配置目录
[root@Git ~]# make profix=/usr/git
[root@Git ~]# make install
```

`make configure`如果报错
```shell
GIT_VERSION = 2.9.5
     GEN configure
/bin/sh: autoconf: command not found
Make: *** [configure] Error 127
```
需要安装`libtool`：

```shell
yum install install autoconf automake libtool
```



**7、加入环境变量**

```vim
[root@Git ~]# echo "export PATH=$PATH:/usr/git/bin" >> /etc/profile
[root@Git ~]# source /etc/profile
```

**8、检查版本**

```vim
[root@qb git-2.9.5]# git --version 
git version 2.9.5
```












