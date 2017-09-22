---
layout: post
title: 手机键盘“开始”触发搜索
categories: h5随记
tags:  h5 js
---

html:
```
<form id="search" action="#">
	<input placeholder="" type="search" name="shops" id="shopName">
	<div class="search-btn"><span id="getShops" class="search-icon"></span></div>
</form>
```
js
```
$('#getShops').on('click', function(event){
    var name = $.trim($("#shopName").val());
    //code
})
```