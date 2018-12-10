---
layout: post
title: Anaconda 的下载和使用
categories: Anaconda
description: Anaconda 的下载和使用
keywords: Anaconda
---



### 1.Anaconda 各版本下载地址
压缩包下载网址
[https://repo.continuum.io/archive/.winzip/](https://repo.continuum.io/archive/.winzip/)

### 2.Anaconda 是什么？

Anaconda 是 Python 的一个发行版，如果把 Python 比作 Linux，那么 Anancoda 就是 CentOS 或者 Ubuntu。它解决了 Python 开发者的两大痛点。

- 第一：提供包管理，功能类似于 pip，Windows 平台安装第三方包经常失败的场景得以解决。
- 第二：提供虚拟环境管理，功能类似于 virtualenv，解决了多版本 Python 并存问题。

### 3. 多版本切换

```python
# 基于 python3.6 创建一个名为 test_py3 的环境
conda create --name test_py3 python=3.6

# 基于 python2.7 创建一个名为 test_py2 的环境
conda create --name test_py2 python=2.7

# 激活 test 环境
activate test_py2 # windows
source activate test_py2 # linux/mac

# 切换到 python3
activate test_py3

# 切换回 py2
deactivate py3 # 重新输入 spyder 等命令即可
source deactivate py3  # linux/mac
```

切换到某一 Python 环境后，`spyder` 和 `jupyter` 的安装，也可以再 Anaconda 管理界面直接 install


```python
# 安装 spyder
conda install -n py2 spyder

# 安装 jupyter
conda install -n py3 jupyter
```
一般要运行 Spyder 或者 jupyter，只需要在终端输入 `spyder` or `jupyter notebook` 即可运行。首先在终端输入：`source activate py3`，win 系统不需要 source，待前面出现 [py3] 之后，再输入 spyder/ jupyter notebook, 回车就行了

### 4. 包管理工具

conda 的包管理功能是对 pip 的一种补充，如果当前已经激活了某个 Python 环境，那么就可以在当前环境开始安装第三方包。
```python
# 安装 matplotlib
conda install matplotlib

# 查看已安装的包
conda list

# 包更新
conda update matplotlib

# 删除包
conda remove matplotlib
```


### 5. 提高下载速度
Anaconda 的镜像地址默认在国外，用 conda 安装包的时候会很慢，目前可用的国内镜像源地址是清华大学提供的。修改 ~/.condarc (Linux/Mac) 或 C:\Users \ 当前用户名. condarc (Windows) 配置


```shell
channels:
 - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
 - defaults
show_channel_urls: true
```
除此之外，你也可以把 pip 的镜像源地址也换成国内的，豆瓣源速度比较快。修改 ~/.pip/pip.conf (Linux/Mac) 或 C:\Users \ 当前用户名 \ pip\pip.ini (Windows) 配置：


```
[global]
trusted-host = pypi.douban.com
index-url = http://pypi.douban.com/simple
```
