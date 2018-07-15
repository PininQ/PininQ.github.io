---
layout: post
title: Win10强制安装和强制卸载DataStage9.1方法
categories: DataStage
description: DataStage9.1的安装与卸载
keywords: DataStage
---

### 一、强制安装DataStage9.1

win10安装DataStage9.1时出现系统环境检测失败

![检查操作系统卸载环境](/images/posts/datastage/checkOS.png)

安装步骤：
 1. 找到自己ds安装目录下的setup.exe文件，复制该路径；
 2. 进入cmd；
 3. 粘贴setup.exe文件路径 + \setup.exe -force，例如：`G:\ETL\ds\ds\setup.exe -force`即可强制安装；
 4. 勾选忽略系统环境检查的选项，即可继续安装；
 5. 后续直接一路下一步，安装成功。

参考博客链接：[win10安装datastage出现系统检测需求失败解决方法](https://blog.csdn.net/weixin_41287692/article/details/79348536)

### 二、强制卸载DataStage9.1

win10卸载DataStage9.1时出现系统环境检测失败

![卸载中](/images/posts/datastage/uninstall.png)

卸载步骤：
 1. 找到自己ds安装目录下的setup.exe文件，复制该路径；
 2. 进入cmd；
 3. 粘贴uninstall.exe文件路径 + \setup.exe -force，例如：`E:\IBM\InformationServer\_uninstall\uninstall.exe -force`即可强制安装；
 4. 勾选忽略系统环境检查的选项，即可继续卸载；
 5. 后续直接一路下一步，卸载成功。


![卸载进度](/images/posts/datastage/uninstall-progress.png)

![卸载完成](/images/posts/datastage/uninstall-successfully.png)

**总结**：win10强制安装和强制卸载需要进入cmd，在`setup.exe`和`uninstall.exe`后面添加` -force`忽略**系统环境检测失败**而无法安装DataStage的问题。