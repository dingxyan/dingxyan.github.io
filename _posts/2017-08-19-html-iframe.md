---
layout: post
title:  我所知道的iframe
categories: 前端知识积累
tags:  iframe html js
---

iframe在前端开发过程中应用广泛，其主要是将一个页面作为整体引用到一个页面中。一般有以下应用场景

### 1. 公司应用系统开发
一般会有一个左侧列表，作为一个框架，每一个目录引用独立的html页面，这样便于每一个页面独立开发。

### 2. 引入第三方
当页面需要从第三方引入模块的时候，往往会使用iframe，保证原始页面与第三方之间不冲突。

**接下来，我们聊一聊，iframe中，如何进行事件的绑定**
## iframe的事件绑定
### 1. 事件绑定
`$('iframe').load(function(){
	$('iframe').contents().click(function(){console.log("test1")});//会为所有iframe绑定事件
	//document.getElementsByTagName("iframe")[0].contentWindow.document.onclick=function(){console.log("test”)}//按照序号为每一个iframe绑定事件
	//document.getElementsByTagName("iframe")[0].contentWindow.onclick=function(){console.log("test”)}//同上
});
`
contentWindow用于获取iframe内容，原生写法，这个属性是只读的，iframe本身不能绑定事件，要获取其内容绑定事件，
$('iframe').find('body')//undefined
$('iframe').contents().find('body’)//ok
另外，需要等待iframe load成功之后再绑定事件，否则事件绑定失败
### 2. 事件委托
无法实现，因为事件冒泡到自己的document就会停止，不会继续向上，所以在外层无法检测内层的事件冒泡。
### 3. each
`$(document).ready(function () {
    $("iframe").each(function () {
        var iframe = $(this);
        iframe.on("load", function () { //保证完全加载完成
            console.log(iframe.contents())
            iframe.contents().click(function (event) {
                iframe.trigger("click");
            });
        });
        iframe.click(function () {
            console.log(3);
        });
    });
});
`
### 4. 伪类
`.g-content:after{
      content:"";
      position:absolute;
      z-index:1;
      width:100%;
      height:100%;
      left:0;
      top:0;
}
$("#j_content").on("click",function(){
    console.log("test")
})
`
给iframe的父容器添加伪类after，并给其绑定事件，缺点是，ifrmame内部的所有事件都会被遮挡，不会响应。

