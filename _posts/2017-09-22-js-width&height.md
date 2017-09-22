---
layout: post
title:   js 各种宽高
categories: js学习笔记
tags:   js width height
---
## 1. Element.clientWidth Element.clientHeight
用于获取元素宽高，例如 document.body.clientWidth，因此，如果页面没有内容，那么获取到的height为0；
下图展示了clientWidth及clientHeight所表示的元素的计算方法
<div  align="center">    
<img src="/images/Dimensions-client.png">
</div>
## 2. Element.scrollWidth
比clientWidth多滚动条的宽度
## 3. Element.scrollHeight
<div  align="center">    
	<img src="/images/scrollHeight.png">
</div>
## 4. Element.offsetWidth Element.offsetHeight
比clientWidth多border的宽度
<div  align="center">    
<img src="/images/Dimensions-offset.png">
</div>
## 5. window.screen.width
获取窗口分辨率宽高，window.screen.availWidth为可见区域的
> Note that not all of the width given by this property may be available to the window itself. When other widgets occupy space that cannot be used by the window object, there is a difference in window.screen.width and window.screen.availWidth. See also window.screen.height.

## 6. inner outer
window.innerWidth, window.innerHeight, 获取窗口可见区域宽高；
window.outerWidth, window.outerHeight, 获取窗口宽高，包括滚动条、边框、控制区域
## 7. jq 
$(window).width(), $(window).height() 获取窗口宽高；值同于window.innerWidth, window.innerHeight，不包含padding；
$(document).width(), $(document).height() 获取文档对象宽高，resize之后不变，当文档对象实际高度小于窗口高度的时候，会返回窗口高度
## 8. 获取元素位置信息
Element.getBoundingClientRect()
Element.getClientRects()

> 参考MDN开发者文档


