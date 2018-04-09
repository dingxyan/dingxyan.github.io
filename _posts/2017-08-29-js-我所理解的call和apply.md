---
layout: post
title:  我所理解的call和apply
categories: 前端知识积累
tags: js call
---

在js中很多时候遇到call以及apply方法，却一直都没有深入研究过，而且也一直都不知道它的使用场景以及如何自如的使用它，于是查阅资料，总结了一下如何理解它以及它的使用场景。

### call 与 apply
首先，这2个方法的效果是一致的，只是call接收的是一个或者一组参数，而apply接收的是数组，如下
```
function.call(thisArg, arg1, arg2, ...);
fun.apply(thisArg, [argsArray]);
```

因此，以下只讨论call方法。

call的作用是改变this指向，由于在js中，除了基础类型之外就都可以看作对象，因此其实不管call方法的用处有多少，总而言之，都可以归结为：

**以function.call(thisArg, arg1, arg2, ...)为例，执行function方法，参数为arg1, arg2, arg3...；在执行function方法的时候，方法中所使用的this，指向的是thisArg。**

### 举例说明

1. 例子1:
```
function fun1(a){
    console.log(++a);
}
function fun2(b){
    console.log(--b);
}
fun1.call(fun2,3);//4
```
理解：执行fun1，参数为3

2. 例子2:
```
function fun1(a){
    console.log(a+":"+this.name);
}
function fun2(){
    this.name = "bbb";
}
var test = new fun2("test");
fun1.call(test,"hello");//hello:bbb
```
理解：执行fun1，this指向fun2的实例化对象test, 参数为"hello"，输出hello:bbb
3. 例子3:
```
function Parent(name){
    this.name = name;
}
function Child(name){
    Parent.call(this, name);
    this.age = '20';
}
var fun = new Child('andy', 22);
```
理解：可以理解为在Child中执行Parent，this 指向当前执行上下文的this，name指Child接收的参数，所以这里可以理解为
```
function Child(name){
    this.name = name;
    this.age = '20';
}
```
因此，该方法可以用来实现js的继承，也可以理解为什么call不能继承原型链（因为call只是改变类this指向而已，对Parent.prototype.fun不会有丝毫影响）。

4. 例子4:
```
function greet() {
    var reply = [this.person, 'Is An Awesome', this.role].join(' ');
    console.log(reply);
}
var i = {
    person: 'Dxy', role: 'Javascript Developer'
};
greet.call(i);
```
理解：上述例子来源于MDN Web文档，第一次看的时候也没看懂，但是用前面说的理解方式，就不难理解了，执行greet方法，该方法不接收参数，而方法执行的时候，this指向i对象，而i.person及i.role很直观，所以，输出为   Dxy Is An Awesome Javascript Developer.

5. 例子5:
```
var animals = [
	{species: 'Lion', name: 'King'},
	{species: 'Whale', name: 'Fail'}
];
for (var i = 0; i < animals.length; i++){
	(function(i) {
		this.print = function(){console.log('#'+i+' '+this.species+': '+this.name);
	    }
		this.print();
	}).call(animals[i], i);
}
```
理解：同样来自于MDN Web文档的例子，只不过添加来循环以及立即执行方法，根据前面的理解方式这个很容易理解的吧，结果要你自己来分析哦。

思考：call和apply方法用于修改this指向，往往用于扩展一个方法的方法。

### 使用场景

1. 在回调函数中使用

在写组件的过程中，我们往往会写很多回调函数，方便用户在执行某一个任务之后，去执行个性化的任务；而在“个性化”任务中，往往需要调用组件的一些方法来实现，那么，这个时候，就需要将组件的实例化对象的操作句柄传给调用方，方便使用，

```
var Fun = function(opt){
	this.opt = opt || {};
	this.init();
}
Fun.prototype = {
	init: function(){
		var me = this;
		console.log(me.opt)
		me.opt.callback && me.opt.callback.call(me);//将操作句柄传给回调函数
	},
	fun1: function(){
		console.log("hello "+this.opt.name);
	}
}
var Show = new Fun({
	"name": "dxy",
	callback: function(){
	    this.fun1();//通过Fun类的操作句柄来调用Fun类的fun1方法
	}
});
Show.fun1();//通过Fun类的实例化对象来调用Fun的fun1方法
```

2. 继承

参看前面例子3

3. 其他
```
var a = [];
Object.prototype.toString.call(a);
```
### 补充

当function.call(thisArg, arg1, arg2, ...)的 thisArg 为null或者undefined的时候，在非严格模式下，指向的是全局对象，在严格模式下会报错。

### 参考连接
[MDN Web文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)


