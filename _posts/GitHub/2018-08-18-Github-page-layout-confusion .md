---
layout: post
title: GitHub 网站排版错乱解决方式
categories: GitHub
description: GitHub 网站排版错乱解决方式
keywords: GitHub, 加速
---

之前偶然得知在 host 文件中添加 `192.30.253.112 github.com` 即可加速访问 GitHub，遂查看是否还有其他加速访问 GitHub 的方式

然后又在 `C:\Windows\System32\drivers\etc\host` 文件中添加
```
# GitHub speed up
192.30.253.112 github.com
151.101.228.133 assets-cdn.github.com
151.101.229.194 github.global.ssl.fastly.net
```

然而没过几天，使用谷歌 / 火狐浏览器打开 GitHub 均出现乱码、CSS、JS 加载不出来。使用其他电脑访问没有问题，怀疑是自己电脑问题，并且是 host 文件这里的问题。`assets-cdn.github.com` 和 `github.global.ssl.fastly.net` 这两个域名的 IP 经常更换。

（1）使用域名映射关系的工具，查看最新延迟最低的 IP，并在 host 文件中修改对应 IP。

```
# 查询域名映射关系的工具，找一个延迟低的修改一下
http://tool.chinaz.com/dns
http://ipaddress.com/
```

（1）ping 加速设置的对应域名，查看网络延迟，如：`ping -t assets-cdn.github.com`。

（2）清除浏览记录，浏览器本地缓存。

（3）修改 hosts 文件（只保留下面这行）：

```
192.30.253.112 github.com
```

（4）刷新 DNS 缓存：进入 CMD，执行 `ipconfig/flushdns`，如下：
```
C:\Users\BL>ipconfig/flushdns

Windows IP 配置

已成功刷新 DNS 解析缓存。
```

