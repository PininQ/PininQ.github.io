---
layout: post
title: JavaScript 实现五角星评分功能
categories: JavaScript
description: JavaScript 实现五角星评分功能
keywords: JavaScript
---

很久没写博客了，今年元旦到现在的业余时间一直在学习英语，看英文原著，挺耗时间的，没时间写博客（借口）。

同事正在学习前端开发，写个 Demo 给他学习一下。嘻嘻

```html
<!DOCTYPE html>
<html>

<head>
	<meta charset="utf-8">
	<title>五角星评分案例</title>
	<link href="https://cdn.bootcss.com/font-awesome/4.7.0/css/font-awesome.css" rel="stylesheet">
	<style type="text/css">
		* {
			margin: 0;
			padding: 0;
		}

		i.fa {
			color: #e67e22;
			font-size: 40px;
		}
	</style>
</head>

<body>
	<div>
		<i class="fa fa-star-o" aria-hidden="true"></i>
		<i class="fa fa-star-o" aria-hidden="true"></i>
		<i class="fa fa-star-o" aria-hidden="true"></i>
		<i class="fa fa-star-o" aria-hidden="true"></i>
		<i class="fa fa-star-o" aria-hidden="true"></i>
	</div>

</body>
<script>
	var div = document.getElementsByTagName("div")[0];
	var stars = document.getElementsByTagName("i");
	var storage = void(0);  // 点击的时候记录当前点击的位置
	var sign = false; // 标记当前是否是半颗星，false 不是半星，true 为半星

	for (var i = 0; i < stars.length; i++) {
		stars[i].setAttribute("index", i);

		stars[i].onmousemove = function (e) {
			var currIndex = this.getAttribute("index");
			var currSign = starHalf(e.offsetX, this.offsetWidth)
			starLight(stars, currIndex, currSign)
		}

		stars[i].onmouseleave = function (e) {
			starLight(stars, storage, sign)
		}

		stars[i].onclick = function (e) {
			storage = this.getAttribute("index");
			sign = starHalf(e.offsetX, this.offsetWidth)
		}
	}

	/**
	 * 是否是半星
	 * @param {currWidth} 一个星星最左边到鼠标位置的长度
	 * @param {halfWidth} 一个星星一半的长度
	 * @return {boolean} true 是半星，false 不是半星
	 */
	function starHalf(currWidth, halfWidth) {
		return currWidth <= halfWidth / 2 ? true : false
	}

	/**
	 * 点亮星星
	 * @param {object} elem 星星数组元素
	 * @param {number} index 最多需要点亮多个星星
	 * @param {boolean} sign 是否是半星，true 是半星，false 不是半星
	 */
	function starLight(elem, index, sign) {
		for (var i = 0; i < elem.length; i++) {
			elem[i].className = i <= index ? "fa fa-star" : "fa fa-star-o";
		}
		if (sign) {
			elem[index].className = "fa fa-star-half-o";
		}
	}
</script>

</html>
```
