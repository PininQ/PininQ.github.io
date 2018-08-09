---
layout: page
title: About
description: 打码改变世界
keywords: Bin Qin, 覃斌
comments: true
menu: 关于
permalink: /about/
---

我是覃斌。

坚信熟能生巧，努力改变人生。

每一个不曾起舞的日子，都是对生命的辜负。

## 我的链接

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## 技能清单

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
