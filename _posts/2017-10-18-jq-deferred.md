---
layout: post
title:  deferred
categories: 前端知识积累
tags: js JQuery
---

####   7. deferred
##### a. deferred对象是一种回调函数解决方案，延迟到未来某个点才执行；
##### b. 链式添加回调

```
$.ajax({
　　　　url: "test.html",
　　　　success: function(){
　　　　　　alert("哈哈，成功了！");
　　　　},
　　　　error:function(){
　　　　　　alert("出错啦！");
　　　　}
　　});
```
可以改写为：
```
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！");} )
　　.fail(function(){ alert("出错啦！"); } )
　　.done(function(){ alert("第二个回调函数！");} );
```
##### c. 对多个异步操作执行统一回调
$.when()的参数要求是deferred对象
```
$.when($.ajax("test1.html"), $.ajax("test2.html"))
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```
##### d. 指定同一个操作的多个回调函数

```
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！");} )
　　.fail(function(){ alert("出错啦！"); } )
　　.done(function(){ alert("第二个回调函数！");} );
```
##### e. 普通操作的回调函数

```
var dtd = $.Deferred(); // 新建一个deferred对象
　　var wait = function(dtd){
　　　　var tasks = function(){
　　　　　　alert("执行完毕！");
　　　　　　dtd.resolve(); // 改变deferred对象的执行状态
　　　　};
　　　　setTimeout(tasks,5000);
　　　　return dtd;
　　};
　　
$.when(wait(dtd))
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```
##### f. deferred.promise()
dtd是全局对象，可以随意改变其状态，为了防止状态被随意改变，使用deferred.promise()可以屏蔽与改变dtd对象的状体的方法。

```
var dtd = $.Deferred(); // 新建一个Deferred对象
var wait = function(dtd){
　　var tasks = function(){
　　　　alert("执行完毕！");
　　　　dtd.resolve(); // 改变Deferred对象的执行状态
　　};
　　setTimeout(tasks,5000);
　　return dtd.promise(); // 返回promise对象
};
var d = wait(dtd); // 新建一个d对象，改为对这个对象进行操作
$.when(d)
.done(function(){ alert("哈哈，成功了！"); })
.fail(function(){ alert("出错啦！"); });
d.resolve(); // 此时，这个语句是无效的
```
